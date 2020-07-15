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

**上面的都是阅读笔记，可以不用看！！**

# 从 ReactDOM.render 开始首次更新的流程
```js
// src/renderers/dom/ReactDOM.js
var ReactDOM = {
    // ...
    render: ReactMount.render,
}
```

接下来看看 ReactMount 是何方神圣。
ReactMount 下挂载有很多方法：
```js
// src/renderers/dom/client/ReactMount.js
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
// src/renderers/dom/client/ReactMount.js
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
        // 见 batchedMountComponentIntoNode 解析
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
```

```js
// src/renderers/shared/stack/reconciler/ReactDefaultBatchingStrategy.js
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
```

好了, 让我们来看看 `transaction` 是什么吧。

```js
var transaction = new ReactDefaultBatchingStrategyTransaction();

function ReactDefaultBatchingStrategyTransaction() {
    this.reinitializeTransaction();
}

// Transaction 的有关内容见 Transaction 解析
// 可以看到, ReactDefaultBatchingStrategyTransaction 是 Transaction 的子类
_assign(ReactDefaultBatchingStrategyTransaction.prototype, Transaction, {
    getTransactionWrappers: function () {
        return TRANSACTION_WRAPPERS;
    }
});
```

问题来了, `TRANSACTION_WRAPPERS` 是什么?

```js
// src/renderers/shared/stack/reconciler/ReactDefaultBatchingStrategy.js
var TRANSACTION_WRAPPERS = [FLUSH_BATCHED_UPDATES, RESET_BATCHED_UPDATES];

var FLUSH_BATCHED_UPDATES = {
    initialize: emptyFunction,
    close: ReactUpdates.flushBatchedUpdates.bind(ReactUpdates),
};

var RESET_BATCHED_UPDATES = {
    initialize: emptyFunction,
    close: function() {
        ReactDefaultBatchingStrategy.isBatchingUpdates = false;
    },
};
```

通过 Transaction 的定义代码可知, 前置函数都是 `emptyFunction`, 后置函数相当于:

```js
function () {
    ReactUpdates.flushBatchedUpdates.bind(ReactUpdates);
    ReactDefaultBatchingStrategy.isBatchingUpdates = false;
}
```

那么, 前面的 `ReactDefaultBatchingStrategy.batchedUpdates` 调用相当于执行了 `ReactUpdates.flushBatchedUpdates`, 那么 `ReactUpdates.flushBatchedUpdates` 又做了什么呢?

```js
var flushBatchedUpdates = function() {
    // ReactUpdatesFlushTransaction's wrappers will clear the dirtyComponents
    // array and perform any updates enqueued by mount-ready handlers (i.e.,
    // componentDidUpdate) but we need to check here too in order to catch
    // updates enqueued by setState callbacks and asap calls.
    // dirtyComponents 代表待更新的节点
    // todo asapEnqueued 代表调用 forceUpdate 产生的更新?
    while (dirtyComponents.length || asapEnqueued) {
        if (dirtyComponents.length) {
            var transaction = ReactUpdatesFlushTransaction.getPooled();
            transaction.perform(runBatchedUpdates, null, transaction);
            ReactUpdatesFlushTransaction.release(transaction);
        }

        if (asapEnqueued) {
            asapEnqueued = false;
            var queue = asapCallbackQueue;
            asapCallbackQueue = CallbackQueue.getPooled();
            queue.notifyAll();
            CallbackQueue.release(queue);
        }
    }
};
```

todo 其实从本节开始已经不怎么按照方法分 p 了...将就看吧

# Transaction 解析
`transaction` 中文翻译为 `事务`。
React 中的 `Transaction` 类用于给目标函数添加一系列的前置和后置函数, 对目标函数进行功能增强或者代码环境保护。

其实这篇[文章](https://segmentfault.com/a/1190000021303172)讲的已经很明白了, 有啥不懂的可以直接看这个。

可以将一个更新任务视为一个 Transaction, 为了实现 componentWillUpdate 以及 componentDidUpdate, 通过 Transaction 指定的方法为更新任务添加前置或后置函数。 

```js
// src/renderers/shared/utils/Transaction.js
/*
 * Copyright 2013-present, Facebook, Inc.
 * All rights reserved.
 *
 * This source code is licensed under the BSD-style license found in the
 * LICENSE file in the root directory of this source tree. An additional grant
 * of patent rights can be found in the PATENTS file in the same directory.
 *
 * @providesModule Transaction
 * @flow
 */

'use strict';

var invariant = require('invariant');

var OBSERVED_ERROR = {};

/**
 * `Transaction` creates a black box that is able to wrap any method such that
 * certain invariants are maintained before and after the method is invoked
 * (Even if an exception is thrown while invoking the wrapped method). Whoever
 * instantiates a transaction can provide enforcers of the invariants at
 * creation time. The `Transaction` class itself will supply one additional
 * automatic invariant for you - the invariant that any transaction instance
 * should not be run while it is already being run. You would typically create a
 * single instance of a `Transaction` for reuse multiple times, that potentially
 * is used to wrap several different methods. Wrappers are extremely simple -
 * they only require implementing two methods.
 *
 * <pre>
 *                       wrappers (injected at creation time)
 *                                      +        +
 *                                      |        |
 *                    +-----------------|--------|--------------+
 *                    |                 v        |              |
 *                    |      +---------------+   |              |
 *                    |   +--|    wrapper1   |---|----+         |
 *                    |   |  +---------------+   v    |         |
 *                    |   |          +-------------+  |         |
 *                    |   |     +----|   wrapper2  |--------+   |
 *                    |   |     |    +-------------+  |     |   |
 *                    |   |     |                     |     |   |
 *                    |   v     v                     v     v   | wrapper
 *                    | +---+ +---+   +---------+   +---+ +---+ | invariants
 * perform(anyMethod) | |   | |   |   |         |   |   | |   | | maintained
 * +----------------->|-|---|-|---|-->|anyMethod|---|---|-|---|-|-------->
 *                    | |   | |   |   |         |   |   | |   | |
 *                    | |   | |   |   |         |   |   | |   | |
 *                    | |   | |   |   |         |   |   | |   | |
 *                    | +---+ +---+   +---------+   +---+ +---+ |
 *                    |  initialize                    close    |
 *                    +-----------------------------------------+
 * </pre>
 *
 * Use cases:
 * - Preserving the input selection ranges before/after reconciliation.
 *   Restoring selection even in the event of an unexpected error.
 * - Deactivating events while rearranging the DOM, preventing blurs/focuses,
 *   while guaranteeing that afterwards, the event system is reactivated.
 * - Flushing a queue of collected DOM mutations to the main UI thread after a
 *   reconciliation takes place in a worker thread.
 * - Invoking any collected `componentDidUpdate` callbacks after rendering new
 *   content.
 * - (Future use case): Wrapping particular flushes of the `ReactWorker` queue
 *   to preserve the `scrollTop` (an automatic scroll aware DOM).
 * - (Future use case): Layout calculations before and after DOM updates.
 *
 * Transactional plugin API:
 * - A module that has an `initialize` method that returns any precomputation.
 * - and a `close` method that accepts the precomputation. `close` is invoked
 *   when the wrapped process is completed, or has failed.
 *
 * @param {Array<TransactionalWrapper>} transactionWrapper Wrapper modules
 * that implement `initialize` and `close`.
 * @return {Transaction} Single transaction for reuse in thread.
 *
 * @class Transaction
 */
var TransactionImpl = {
    /**
     * Sets up this instance so that it is prepared for collecting metrics. Does
     * so such that this setup method may be used on an instance that is already
     * initialized, in a way that does not consume additional memory upon reuse.
     * That can be useful if you decide to make your subclass of this mixin a
     * "PooledClass".
     */
    // 初始化或重新初始化
    reinitializeTransaction: function (): void {
        // getTransactionWrappers 是通过外部注入的一个函数, 通过这个函数可以自定义返回的 Transaction Wrapper
        // Transaction Wrapper 应该是一个数组, 里面存储的是当前事务的前置函数和后置函数
        this.transactionWrappers = this.getTransactionWrappers();

        // todo wrapper 函数的初始数据?
        if (this.wrapperInitData) {
            this.wrapperInitData.length = 0;
        } else {
            this.wrapperInitData = [];
        }
        // react 不允许调用正在进行的 Transaction
        // 当事务的前置函数或后置函数在执行时, 这个标志位应该变为 true?
        this._isInTransaction = false;
    },

    _isInTransaction: false,

    /**
     * @abstract
     * @return {Array<TransactionWrapper>} Array of transaction wrappers.
     */
    // 给事务添加 wrappers 的方法, 通过外部指定, 返回值是一个数组, 数组中的每个元素都为对象, 每个对象都可选地包含(前置和后置函数可以为空) initialize 和 close 方法
    getTransactionWrappers: null,

    isInTransaction: function (): boolean {
        return !!this._isInTransaction;
    },

    /**
     * Executes the function within a safety window. Use this for the top level
     * methods that result in large amounts of computation/mutations that would
     * need to be safety checked. The optional arguments helps prevent the need
     * to bind in many cases.
     *
     * @param {function} method Member of scope to call.
     * @param {Object} scope Scope to invoke from.
     * @param {Object?=} a Argument to pass to the method.
     * @param {Object?=} b Argument to pass to the method.
     * @param {Object?=} c Argument to pass to the method.
     * @param {Object?=} d Argument to pass to the method.
     * @param {Object?=} e Argument to pass to the method.
     * @param {Object?=} f Argument to pass to the method.
     *
     * @return {*} Return value from `method`.
     */
    // 这里就是我们在初次渲染时执行的方法了
    // 用于对目标函数完成包裹动作, 并执行相应的前置或后置函数
    // 可以看到 perform 接受三种参数: 第一个参数为目标函数, 第二个参数为目标函数调用的作用域, 第三个及之后的参数为需要向目标函数中传递的参数
    perform: function <
        A, B, C, D, E, F, G,
        T: (a: A, b: B, c: C, d: D, e: E, f: F) => G // eslint-disable-line space-before-function-paren
    > (
        method: T, scope: any,
        a: A, b: B, c: C, d: D, e: E, f: F,
    ): G {
        var errorThrown;
        var ret;
        try {
            this._isInTransaction = true;
            // Catching errors makes debugging more difficult, so we start with
            // errorThrown set to true before setting it to false after calling
            // close -- if it's still set to true in the finally block, it means
            // one of these calls threw.
            errorThrown = true;
            // 执行所有前置函数
            this.initializeAll(0);
            ret = method.call(scope, a, b, c, d, e, f);
            errorThrown = false;
        } finally {
            // closeAll 用于执行所有后置函数
            try {
                if (errorThrown) {
                    // If `method` throws, prefer to show that stack trace over any thrown
                    // by invoking `closeAll`.
                    try {
                        this.closeAll(0);
                    } catch (err) {
                    }
                } else {
                    // Since `method` didn't throw, we don't want to silence the exception
                    // here.
                    this.closeAll(0);
                }
            } finally {
                this._isInTransaction = false;
            }
        }
        return ret;
    },

    initializeAll: function(startIndex: number): void {
        // Transaction Wrapper 数组
        var transactionWrappers = this.transactionWrappers;
        // todo 没看懂哎
        for (var i = startIndex; i < transactionWrappers.length; i++) {
            var wrapper = transactionWrappers[i];
            try {
                // Catching errors makes debugging more difficult, so we start with the
                // OBSERVED_ERROR state before overwriting it with the real return value
                // of initialize -- if it's still set to OBSERVED_ERROR in the finally
                // block, it means wrapper.initialize threw.
                // OBSERVED_ERROR 是空白对象
                this.wrapperInitData[i] = OBSERVED_ERROR;
                this.wrapperInitData[i] = wrapper.initialize ?
                    wrapper.initialize.call(this) :
                    null;
            } finally {
                if (this.wrapperInitData[i] === OBSERVED_ERROR) {
                    // The initializer for wrapper i threw an error; initialize the
                    // remaining wrappers but silence any exceptions from them to ensure
                    // that the first error is the one to bubble up.
                    try {
                        this.initializeAll(i + 1);
                    } catch (err) {
                    }
                }
            }
        }
    },

    /**
     * Invokes each of `this.transactionWrappers.close[i]` functions, passing into
     * them the respective return values of `this.transactionWrappers.init[i]`
     * (`close`rs that correspond to initializers that failed will not be
     * invoked).
     */
    closeAll: function(startIndex: number): void {
        invariant(
            this.isInTransaction(),
            'Transaction.closeAll(): Cannot close transaction when none are open.'
        );
        var transactionWrappers = this.transactionWrappers;
        for (var i = startIndex; i < transactionWrappers.length; i++) {
            var wrapper = transactionWrappers[i];
            var initData = this.wrapperInitData[i];
            var errorThrown;
            try {
                // Catching errors makes debugging more difficult, so we start with
                // errorThrown set to true before setting it to false after calling
                // close -- if it's still set to true in the finally block, it means
                // wrapper.close threw.
                errorThrown = true;
                if (initData !== OBSERVED_ERROR && wrapper.close) {
                    wrapper.close.call(this, initData);
                }
                errorThrown = false;
            } finally {
                if (errorThrown) {
                    // The closer for wrapper i threw an error; close the remaining
                    // wrappers but silence any exceptions from them to ensure that the
                    // first error is the one to bubble up.
                    try {
                        this.closeAll(i + 1);
                    } catch (e) {
                    }
                }
            }
        }
        this.wrapperInitData.length = 0;
    },
};

export type Transaction = typeof TransactionImpl;

module.exports = TransactionImpl;
```

# batchedMountComponentIntoNode 解析
```js
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