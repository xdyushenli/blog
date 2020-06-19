---
title: The Journey of React 15 Source Code
date: 2020-06-16 13:50:22
tags: [ react ]
---
We can write better code by reading good code.

# todo
There are more before...

# Children
onlyChildren

# ReactComponent.js
```js
ReactComponent.prototype.setState = function (partialState, callback) {
    this.updater.enqueueSetState(this, partialState);

    if (callback) {
        this.updater.enqueueCallback(this, callback, 'setState');
    }
};

ReactComponent.prototype.forceUpdate = function (callback) {
    this.updater.enqueueForceUpdate(this);

    if (callback) {
        this.updater.enqueueCallback(this, callback, 'forceUpdate');
    }
};
```
What can we learn about from code above?
Are there two kinds of queue in React? One is a state queue, the other is a callback queue.

# ReactPureComponent.js
## What ReactPureComponent Do?
> `React.PureComponent` is similar to `React.Component`. The difference between them is that `React.Component` doesn't implement `shouldComponentUpdate()`, but `React.PureComponent` implements it with a shallow prop and state comparison.

## Dive into ReactPureComponent
```js
function ComponentDummy() {}
ComponentDummy.prototype = ReactComponent.prototype;
ReactPureComponent.prototype = new ComponentDummy();
ReactPureComponent.prototype.constructor = ReactPureComponent;
// Avoid an extra prototype jump for these methods.
Object.assign(ReactPureComponent.prototype, ReactComponent.prototype);
ReactPureComponent.prototype.isPureReactComponent = true;
```
todo: this paragraphs definitely has some grammatical errors...
These codes could be a little diffcult to understand. This snippet has two effects:
* Make `ReactPureComponent` and `ReactComponent` has inheritance.
* Make `ReactPureComponent` instance inherits `React.prototype`'s properties.

The final point direction will be like this:

todo: Pretend to have a image here...

> If that does't make sense, take a look at the following snippet and try to understand what's going on here:
```js
function ReactComponent() {
  console.log('I am a ReactComponent!');
}

ReactComponent.prototype.ReactComponentMethod = function() {
  console.log('This is a method on the ReactComponent.prototype');
};

function ReactPureComponent() {
  console.log('I am a ReactPureComponent!');
}

function ComponentDummy() {}

ComponentDummy.prototype = ReactComponent.prototype;
ReactPureComponent.prototype = new ComponentDummy();
ReactPureComponent.prototype.constructor = ReactPureComponent;

ComponentDummy.prototype.constructor(); // I am a ReactComponent!
ReactComponent.prototype.constructor(); // I am a ReactComponent!
ReactPureComponent.prototype.constructor(); // I am a ReactPureComponent!

const newPureComp = new ReactPureComponent(); // I am a ReactPureComponent!
newPureComp.ReactComponentMethod(); // This is a method on the ReactComponent.prototype
```

## Let's Dive a little Deeper
Two Further Questions:
todo: WHY WHY WHY!!!
* Why not just uses these codes? Why `ComponentDummy`?
```js
ReactPureComponent.__proto__ = ReactComponent.prototype;
```

* Why we need to assign `ReactComponent.prototype` to `ReactPureComponent.prototype`?
```js
// Avoid an extra prototype jump for these methods.
Object.assign(ReactPureComponent.prototype, ReactComponent.prototype);
```

These two questions are actually one same questions, and the better description is: Why take a long way to make `ReactPureComponent.prototype.__proto__` points at `ReactComponent.prototype`? We will copy `ReactComponent.prototype` to `ReactPureComponent.prototype` eventually anyway.

todo: edit this paragraphs.
I don't know why they did this, but I **DO KNOW** what these code does't do.**It would't rewrites `ReactPureComponent.prototype.constructor` property.**

I think the answer may in the [test file](https://github.com/facebook/react/blob/v15.4.1/src/isomorphic/modern/class/__tests__/ReactPureComponent-test.js).
```js
it('extends React.Component', () => {
    var renders = 0;
    class Component extends React.PureComponent {
      render() {
        expect(this instanceof React.Component).toBe(true);
        expect(this instanceof React.PureComponent).toBe(true);
        renders++;
        return <div />;
      }
    }

    ReactDOM.render(<Component />, document.createElement('div'));
    expect(renders).toBe(1);
});
```
As we can see, the `ReactPureComponent` should be both an instance of `ReactPureComponent` and `ReactComponent`. It implies that `ReactComponent` is extended by `ReactPureComponent`.

# ReactElement.js
todo
* article link: https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030
* unpkg link: https://unpkg.com/browse/react@15.4.1/lib/ReactElement.js
* react github link: https://github.com/facebook/react/blob/v15.4.1/src/isomorphic/modern/class/__tests__/ReactPureComponent-test.js

# References
* [The React Source Code: a Beginner’s Walkthrough I](https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030)
* [React Source Code V15.4.1](https://github.com/facebook/react/tree/v15.4.1)
* [React Documentation](https://reactjs.org/docs/react-api.html)

# More References (not read yet)
* [Using a function in setState instead of an object](https://medium.com/@wisecobbler/using-a-function-in-setstate-instead-of-an-object-1f5cfd6e55d1)
* [JavaScript Pseudoclassical Subclasses](https://yctercero.github.io/javascript-subclasses/)

# 从 ReactDOM.render 开始首次更新的流程
```js
// src/renderers/dom/ReactMount.js
var ReactDOM = {
    // ...
    render: ReactMount.render,
}
```

接下来看看 ReactMount 是何方神圣。
ReactMount 下挂载有很多方法：
```js
var ReactMount = {
    TopLevelWrapper,
    _instancesByReactRootID,
    scrollMonitor(),
    _updateRootComponent(),
    _renderNewRootComponent(),
    renderSubtreeIntoContainer(),
    _renderSubtreeIntoContainer(),
    render(),
    unmountComponentAtNode(),
    _mountImageIntoNode()
}
```
其中，`render` 方法长这样：
```js
/**
* Renders a React component into the DOM in the supplied `container`.
* See https://facebook.github.io/react/docs/top-level-api.html#reactdom.render
*
* If the React component was previously rendered into `container`, this will
* perform an update on it and only mutate the DOM as necessary to reflect the
* latest React component.
*
* @param {ReactElement} nextElement Component element to render.
* @param {DOMElement} container DOM element to render into.
* @param {?function} callback function triggered on completion
* @return {ReactComponent} Component instance rendered in `container`.
*/
render: function(nextElement, container, callback) {
    return ReactMount._renderSubtreeIntoContainer(null, nextElement, container, callback);
}
```

`ReactMount._renderSubtreeIntoContainer` 中与初次渲染有关的代码：
```js
// 首次渲染没有 parentComponent, 所以传入的是 null
_renderSubtreeIntoContainer: function(parentComponent, nextElement, container, callback) {
    // 检测传入的 callback 是否为函数
    ReactUpdateQueue.validateCallback(callback, 'ReactDOM.render');

    // 创建一个类型为 TopLevelWrapper 的 ReactElement，并将需要渲染的元素作为这个 ReactElement 的 child
    // todo createElement
    var nextWrappedElement = React.createElement(
        // TopLevelWrapper 是挂载在 ReactMount 上的
        TopLevelWrapper,
        { child: nextElement }
    );

    var nextContext;
    // 第一次渲染 parentComponent 为 null, 所以 nextContext 为空对象
    if (parentComponent) {
        var parentInst = ReactInstanceMap.get(parentComponent);
        nextContext = parentInst._processChildContext(parentInst._context);
    } else {
        nextContext = emptyObject;
    }

    // 见 getTopLevelWrapperInContainer 解析
    // 第一次渲染, prevComponent 为 null
    var prevComponent = getTopLevelWrapperInContainer(container);

    // getReactRootElementInContainer 返回 Document 的根节点或 container 下的第一个子元素
    // 初次渲染, reactRootElement 为 null
    var reactRootElement = getReactRootElementInContainer(container);
    // null
    var containerHasReactMarkup =
        reactRootElement && !!internalGetID(reactRootElement);
    
    // undefined
    var containerHasNonRootReactChild = hasNonRootReactChild(container);

    // 初次渲染，无可复用的标记，因此为 false
    var shouldReuseMarkup =
        containerHasReactMarkup &&
        !prevComponent &&
        !containerHasNonRootReactChild;
    
    var component = ReactMount._renderNewRootComponent(
        // ReactElement 对象
        nextWrappedElement,
        // html 元素对象
        container,
        // false
        shouldReuseMarkup,
        // {}
        nextContext
    )._renderedComponent.getPublicInstance();
    
    if (callback) {
        callback.call(component);
    }
    return component;
},
```

可以看到，最终调用了 `ReactMount._renderNewRootComponent()._renderedComponent.getPublicInstance()`, 并返回了调用结果。

`_renderNewRootComponent` 如下：
```js
/**
* Render a new component into the DOM. Hooked by hooks!
*
* @param {ReactElement} nextElement element to render
* @param {DOMElement} container container to render into
* @param {boolean} shouldReuseMarkup if we should skip the markup insertion
* @return {ReactComponent} nextComponent
*/
_renderNewRootComponent: function(
    nextElement,
    container,
    shouldReuseMarkup,
    context
) {
    // todo 应该是用于监控滚动事件?
    ReactBrowserEventEmitter.ensureScrollValueMonitoring();
    // 见 instantiateReactComponent 解析
    var componentInstance = instantiateReactComponent(nextElement, false);

    // 见 batchedUpdates 解析
    ReactUpdates.batchedUpdates(
        // 见下
        batchedMountComponentIntoNode,
        // 复合组件 wrapper 实例
        componentInstance,
        // html 元素
        container,
        // false
        shouldReuseMarkup,
        // {}
        context
    );

    var wrapperID = componentInstance._instance.rootID;
    instancesByReactRootID[wrapperID] = componentInstance;

    return componentInstance;
}

// todo
/**
 * Batched mount.
 *
 * @param {ReactComponent} componentInstance The instance to mount.
 * @param {DOMElement} container DOM element to mount into.
 * @param {boolean} shouldReuseMarkup If true, do not insert markup
 */
function batchedMountComponentIntoNode(componentInstance, container, shouldReuseMarkup, context) {
    var transaction = ReactUpdates.ReactReconcileTransaction.getPooled(!shouldReuseMarkup && ReactDOMFeatureFlags.useCreateElement);

    transaction.perform(mountComponentIntoNode, null, componentInstance, container, transaction, shouldReuseMarkup, context);
    
    ReactUpdates.ReactReconcileTransaction.release(transaction);
}
```

# createElement 解析
todo

# getTopLevelWrapperInContainer 解析
`getTopLevelWrapperInContainer` 源码如下：
```js
function getTopLevelWrapperInContainer(container) {
    var root = getHostRootInstanceInContainer(container);
    return root ? root._hostContainerInfo._topLevelWrapper : null;
}


function getHostRootInstanceInContainer(container) {
    // 获取 container 的第一个子节点或 Document 的根节点
    var rootEl = getReactRootElementInContainer(container);
    // 初次渲染，一般 container 内并没有任何节点，因此 rootEl 为 null，所以 prevHostInstance 为 null
    var prevHostInstance =
        rootEl && ReactDOMComponentTree.getInstanceFromNode(rootEl);
    return (
        prevHostInstance && !prevHostInstance._hostParent ?
        prevHostInstance : null
    );
}

function getReactRootElementInContainer(container) {
    if (!container) {
        return null;
    }

    // DOC_NODE_TYPE = 9
    // nodeType === 9 表示该节点为一个 Document 对象
    if (container.nodeType === DOC_NODE_TYPE) {
        // 返回 Document 对象的根元素
        return container.documentElement;
    } else {
        // 返回 container 的第一个子节点
        return container.firstChild;
    }
}
```

# instantiateReactComponent 解析
```js
/**
 * Given a ReactNode, create an instance that will actually be mounted.
 *
 * @param {ReactNode} node
 * @param {boolean} shouldHaveDebugID
 * @return {object} A new instance of the element's constructor.
 * @protected
 */
function instantiateReactComponent(node, shouldHaveDebugID) {
    var instance;

    if (node === null || node === false) {
        instance = ReactEmptyComponent.create(instantiateReactComponent);
    } else if (typeof node === 'object') {
        var element = node;

        // Special case string values
        if (typeof element.type === 'string') {
            instance = ReactHostComponent.createInternalComponent(element);
        } else if (isInternalComponentType(element.type)) {
            instance = new element.type(element);

            // We renamed this. Allow the old name for compat. :(
            if (!instance.getHostNode) {
                instance.getHostNode = instance.getNativeNode;
            }
        } else {
        // 初次渲染时这个断言为 true
        // 创建复合组件 Wrapper 实例，实例的 _currentElement 属性指向 element
            instance = new ReactCompositeComponentWrapper(element);
        }
    } else if (typeof node === 'string' || typeof node === 'number') {
        instance = ReactHostComponent.createInstanceForText(node);
    } else {
        invariant(
            false,
            'Encountered invalid React node of type %s',
            typeof node
        );
    }

    instance._mountIndex = 0;
    instance._mountImage = null;

    return instance;
}
```

# batchedUpdates 解析
```js
// ReactUpdates.js
function batchedUpdates(callback, a, b, c, d, e) {
    // 打印警告，无特殊作用
    ensureInjected();
    return batchingStrategy.batchedUpdates(callback, a, b, c, d, e);
}

// 别问我为啥 batchingStrategy === ReactDefaultBatchingStrategy, 问就是 magic
// 应该也是 assign 了一下吧
batchingStrategy = ReactDefaultBatchingStrategy

// ReactDefaultBatchingStrategy.js
var ReactDefaultBatchingStrategy = {
    isBatchingUpdates: false,

    batchedUpdates: function(callback, a, b, c, d, e) {
        var alreadyBatchingUpdates = ReactDefaultBatchingStrategy.isBatchingUpdates;

        ReactDefaultBatchingStrategy.isBatchingUpdates = true;

        if (alreadyBatchingUpdates) {
            return callback(a, b, c, d, e);
        } else {
        // 初次渲染此断言为真
            return transaction.perform(callback, null, a, b, c, d, e);
        }
    },
};

var transaction = new ReactDefaultBatchingStrategyTransaction();

function ReactDefaultBatchingStrategyTransaction() {
    this.reinitializeTransaction();
}

// Transaction 的有关内容见 Transaction 解析
_assign(ReactDefaultBatchingStrategyTransaction.prototype, Transaction, {
    getTransactionWrappers: function () {
        return TRANSACTION_WRAPPERS;
    }
});
```

# Transaction 解析
`transaction` 中文翻译为 `事务`。
React 中的 `Transaction` 类用于给目标函数添加一系列的前置和后置函数，对目标函数进行功能增强或者代码环境保护。

https://segmentfault.com/a/1190000021303172#item-2
```js
// Transaction.js
todo
```