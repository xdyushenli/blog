---
title: React.Children.map源码解析
date: 2019-10-11 18:21:10
tags: [ React ]
---
# 简介
`React.Children`提供了用于处理`this.props.children`不透明数据结构的实用方法。而`React.Children.map`是其中的一种。
```js
React.Children.map(children, function[(thisArg)])
```
在`children`里的每个直接子节点上调用一个函数，并将`this`设置为`thisArg`。
如果`children`是一个数组，它将被遍历并为数组中的每个子节点调用该函数。
如果子节点为`null`或是`undefined`，则此方法将返回`null`或是`undefined`，而不会返回数组。
值得注意的是，若`function`的返回值是一个多维数组，那么`React.Children.map`的返回值是一个将多维数组展开形成的一维数组。
# 流程图
![](/images/react-children-map/01.jpg)
# 主要函数解析
主要的函数有五个，按照执行的先后顺序分别是`mapIntoWithKeyPrefixInternal`，`getPooledTraverseContext`，`traverseAllChildrenImpl`、`mapSingleChildIntoContext`和`releaseTraverseContext`。

## mapIntoWithKeyPrefixInternal
### 源代码
```js
function mapIntoWithKeyPrefixInternal(children, array, prefix, func, context) {
    let escapedPrefix = '';
    if (prefix != null) {
        escapedPrefix = escapeUserProvidedKey(prefix) + '/';
    }
    const traverseContext = getPooledTraverseContext(
        array,
        escapedPrefix,
        func,
        context,
    );
    traverseAllChildren(children, mapSingleChildIntoContext, traverseContext);
    releaseTraverseContext(traverseContext);
}
```
### 源码解析
整个`mapIntoWithKeyPrefixInternal`实际上可以分为三步。
1. 调用`getPooledTraverseContext`，创建上下文对象。
2. 调用`traverseAllChildren`，执行遍历操作。
3. 调用`releaseTraverseContext`，将本次遍历用到的上下文对象保存入上下文池中。

## getPooledTraverseContext
### 源代码
```js
// 上下文池的最大长度
const POOL_SIZE = 10;
// 上下文池
const traverseContextPool = [];
function getPooledTraverseContext(
  mapResult,
  keyPrefix,
  mapFunction,
  mapContext,
) {
  if (traverseContextPool.length) {
    const traverseContext = traverseContextPool.pop();
    traverseContext.result = mapResult;
    traverseContext.keyPrefix = keyPrefix;
    traverseContext.func = mapFunction;
    traverseContext.context = mapContext;
    traverseContext.count = 0;
    return traverseContext;
  } else {
    return {
      result: mapResult,
      keyPrefix: keyPrefix,
      func: mapFunction,
      context: mapContext,
      count: 0,
    };
  }
}
```
### 源码解析
#### 函数功能
从这段代码中可以看出，`getPooledTraverseContext`所做的就是从`traverseContextPool`取出位于数组尾部的最后一个对象，并将对应参数赋值给对象中的对应属性。若上下文池中没有需要的对象，则创建一个新对象并返回。
该对象承载了许多内容，可以说是名副其实的上下文对象。在之后的各个函数中，基本都会把该对象作为参数传入其中。
#### 上下文对象解析
* mapResult：保存在`mapChildren`中创建的`result`对象的内存地址。
* keyPrefix：`key`属性前缀。
* mapFunction：`React.Children.map`传入的回调函数。
* mapContext：`React.Children.map`传入的上下文。
* count：执行回调函数后，`result`数组中元素的个数。

#### 上下文池的作用
上下文池用于保存之前用过的上下文对象。之所以要采用这种保存对象的设计，是因为js引擎对于创建和销毁对象的开销都比较大，保存之前用过的对象可以在需要遍历的数组维度和长度都比较大时提升一定性能。

## traverseAllChildrenImpl
### 源代码
```js
/**
 * @param {?*} children Children tree container.
 * @param {!string} nameSoFar Name of the key path so far.
 * @param {!function} callback Callback to invoke with each child found.
 * @param {?*} traverseContext Used to pass information throughout the traversal
 * process.
 * @return {!number} The number of children in this subtree.
 */
function traverseAllChildrenImpl(
    children,
    nameSoFar,
    callback,
    traverseContext,
) {
    const type = typeof children;

    if (type === 'undefined' || type === 'boolean') {
        // All of the above are perceived as null.
        children = null;
    }

    let invokeCallback = false;

    if (children === null) {
        invokeCallback = true;
    } else {
        switch (type) {
            case 'string':
            case 'number':
                invokeCallback = true;
                break;
            case 'object':
                switch (children.$$typeof) {
                    case REACT_ELEMENT_TYPE:
                    case REACT_PORTAL_TYPE:
                        invokeCallback = true;
                }
        }
    }

    if (invokeCallback) {
        callback(
            traverseContext,
            children,
            // If it's the only child, treat the name as if it was wrapped in an array
            // so that it's consistent if the number of children grows.
            nameSoFar === '' ? SEPARATOR + getComponentKey(children, 0) : nameSoFar,
        );
        return 1;
    }

    let child;
    let nextName;
    let subtreeCount = 0; // Count of children found in the current subtree.
    const nextNamePrefix =
        nameSoFar === '' ? SEPARATOR : nameSoFar + SUBSEPARATOR;

    if (Array.isArray(children)) {
        for (let i = 0; i < children.length; i++) {
            child = children[i];
            nextName = nextNamePrefix + getComponentKey(child, i);
            subtreeCount += traverseAllChildrenImpl(
                child,
                nextName,
                callback,
                traverseContext,
            );
        }
    } else {
        const iteratorFn = getIteratorFn(children);
        if (typeof iteratorFn === 'function') {
            // 具有遍历器接口的对象
            // ...
        } else if (type === 'object') {
            // 错误提示
            // ...
        }
    }

    return subtreeCount;
}
```
### 源码解析
在该函数中，根据`children`的类型和`React.Children.map`传入的回调函数的执行结果进行了路径划分。
若`children`是数组，则会遍历该数组，递归调用`traverseAllChildrenImpl`函数，并将每个数组中的每个元素作为`children`参数传入其中。
若`children`是字符串、数字或组件，则会调用`mapSingleChildIntoContext`函数，并将`children`传入其中。

## mapSingleChildIntoContext
### 源代码
```js
function mapSingleChildIntoContext(bookKeeping, child, childKey) {
    const { result, keyPrefix, func, context } = bookKeeping;

    // 在child上调用React.Children.map传入的回调函数
    let mappedChild = func.call(context, child, bookKeeping.count++);
    if (Array.isArray(mappedChild)) {
        mapIntoWithKeyPrefixInternal(mappedChild, result, childKey, c => c);
    } else if (mappedChild != null) {
        if (isValidElement(mappedChild)) {
            // 克隆节点，并赋值为mappedChild
            // ...
        }
        // 将执行结果存入result数组
        result.push(mappedChild);
    }
}
```
### 源码解析
在`mapSingleChildIntoContext`中，终于执行了在一开始传入的回调函数。
若回调函数的执行结果是一个数组，则会递归调用`mapIntoWithKeyPrefixInternal`。值得注意的是，此时传入`mapIntoWithKeyPrefixInternal`的回调函数是`c => c`，即直接返回当前数组元素。
若回调函数执行结果不是数组，则将其放入`result`中保存。

## releaseTraverseContext
### 源代码
```js
function releaseTraverseContext(traverseContext) {
    traverseContext.result = null;
    traverseContext.keyPrefix = null;
    traverseContext.func = null;
    traverseContext.context = null;
    traverseContext.count = 0;
    if (traverseContextPool.length < POOL_SIZE) {
        traverseContextPool.push(traverseContext);
    }
}
```
### 源码解析
在`releaseTraverseContext`中，重置了之前遍历用到的上下文对象，并将其保存到上下文池中。

# 总结
在阅读`React.Children.map`源码的过程中，比较值得注意的是上下文池的设计。  
由于在处理`children`数组时错综复杂的函数调用关系，因此需要有一个统一的对象用于保存执行的结果。从这个角度上来说，将其翻译为上下文还是挺合适的。而上下文池的设计，则是为了尽可能的复用之前遍历时已经产生的对象。上下文池中的每个对象，都是空白的上下文。js引擎创建对象和销毁对象的开销都很大，在数组长度和维数比较大时，这种做法能带来性能上的提升。
