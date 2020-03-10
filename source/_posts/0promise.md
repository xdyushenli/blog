---
title: 如何自己实现一个 Promise?
date: 2020-02-25 14:12:27
tags: [ js ]
---
# 简介
Promise 在 js 中是十分重要的存在, 但是之前并没有好好想过这个东西到底是什么实现的。最近, 我花了一天的时间查阅资料及编写代码, 终于解决了这个问题。
**注意, 本文并不会对 Promise 及其相关 API 的使用方法进行说明。**如果对 Promise 的使用有疑问, 请移步阮大的[ECMAScript 6 入门教程](https://es6.ruanyifeng.com/)。
另外, **强烈建议在开始编写自己的 Promise 前, 先阅读 [Promise 实现规范](https://promisesaplus.com)。**这篇文章详细说明了 Promise 在不同情况下应当具有的表现, 能帮我们尽快理清思路。

# 从一个测试用例开始
首先来看一个测试用例:
```js
// test1
console.log(1);
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(4);
    }, 1000)
})

console.log(2);

p.then((val) => {
    console.log(val);
    return val + 1;
}).then((val) => {
    console.log(val);
    return 5;
})

console.log(3);
```
了解 Promise 的人都知道, 上面这段代码最终的输出结果, 应该是按顺序的 `1 2 3 4 5`。**如果你猜错了, 那么强烈建议你在此止步, 去阅读下阮大写的关于 Promise 的文章或 Promise 的 MDN 文档后再来阅读本文。**
上面的代码主要用到了 Promise 的链式调用以及异步任务的特性。
链式调用很好解决, 只要让 `then` 方法返回一个 Promise 对象就好。
比较困难的地方在于异步任务, 那么如何才能保证 `then` 方法传入的函数, 只有在 Promise 状态为 `resolved` 或 `rejected` 后才被调用呢? 
解决办法就是: **在 Promise 上添加两个队列, 分别用于存放 `then` 方法的第一个参数和第二个参数。**当 Promise 状态为 `pending` 时, 将 `then` 方法传入的函数放入队列中。当 Promise 的状态变化后, 便按照 Promise 的最终状态, 依次调用存放在队列中的方法。

```js
// todo 代码版本太高了
function Promise(fn) {
    if (typeof fn !== 'function') {
        throw new TypeError(`Promise resolver ${fn} is not a function`)
    }

    this.status = 'pending';
    this.value = null;

    this._onResolvedFns = [];
    this._onRejectedFns = [];
    this._promiseList = [];

    function _clearFnQueue(queue, promiseList) {
        let executor = null;
        let currentPromise = null;

        while (queue.length > 0) {
            executor = queue.shift();
            currentPromise = promiseList.shift();

            // executor 为函数
            if (typeof executor === 'function') {
                let value = null;
                try {
                    value = executor();
                } catch (err) {
                    currentPromise.status = 'rejected';
                    currentPromise.value = err;
                    // 如果执行出错, 影响的应该是后面 then 的执行, 而不是数组中其他 fn 的执行
                    // 肯定是得广度优先执行, 队列中的函数应提前绑定好 this 以及 value 值
                    // 执行错误的时候, 绑定的应该是下级中的 onRejectedFns
                    while (currentPromise._promiseList.length > 0) {
                        let nextPromise = currentPromise._promiseList.shift();
                        let nextRejectedFns = currentPromise._onRejectedFns;

                        // 将下一层 then 调用所需的 Promise 放入 promiseList 中
                        promiseList.push(nextPromise);

                        while (nextRejectedFns.length > 0) {
                            let fn = nextRejectedFns.shift();
                            queue.push(fn.bind(currentPromise, currentPromise.value));
                        }
                    }
                }

                currentPromise.status = 'resolved';
                currentPromise.value = value;

                // 肯定是得广度优先执行, 队列中的函数应提前绑定好 this 以及 value 值
                while (currentPromise._promiseList.length > 0) {
                    let nextPromise = currentPromise._promiseList.shift();
                    let nextResolvedFns = currentPromise._onResolvedFns;

                    // 将下一层 then 调用所需的 Promise 放入 promiseList 中
                    promiseList.push(nextPromise);

                    while (nextResolvedFns.length > 0) {
                        let fn = nextResolvedFns.shift();
                        queue.push(fn.bind(currentPromise, currentPromise.value));
                    }
                }
            } else {
                continue;
            }
        }
    }

    function resolve(value) {
        this.status = 'resolved';
        this.value = value;
        let context = window || undefined;

        // 将 onResolvedFns 队列中的函数绑定上 this 对象以及传入的 value 值
        for (let i = 0; i < this._onResolvedFns.length; i++) {
            let fn = this._onResolvedFns[i];
            this._onResolvedFns[i] = fn.bind(context, value);
        }

        _clearFnQueue.call(this, this._onResolvedFns, this._promiseList);
    }

    function reject(err) {
        this.status = 'rejected';
        this.value = err;
        let context = window || undefined;

        // 将 onRejectedFns 队列中的函数绑定上 this 对象以及传入的 value 值
        for (let i = 0; i < this._onRejectedFns.length; i++) {
            let fn = this._onRejectedFns[i];
            this._onRejectedFns[i] = fn.bind(context, err);
        }

        _clearFnQueue.call(this, this._onRejectedFns, this._promiseList);
    }

    try {
        fn(resolve.bind(this), reject.bind(this));
    } catch (err) {
        throw (err);
    }
}

// 返回新对象, 新对象状态根据之前 Promise 执行的结果来进行判断
Promise.prototype.then = function (onFulfilled, onRejected) {
    if (onFulfilled === undefined) {
        onFulfilled = val => val;
    }
    if (onRejected === undefined) {
        onRejected = err => err;
    }

    // 用于保存要执行的函数
    let executor = null;

    if (this.status === 'pending') {
        this._onResolvedFns.push(onFulfilled);
        this._onRejectedFns.push(onRejected);
        // 返回新的 Promise 对象, 挂载在 _promiseList 上
        let nextPromise = new Promise(() => { })
        this._promiseList.push(nextPromise);
        return nextPromise;
    } else if (this.status === 'resolved') {
        // 如果前面的 Promise 状态为 resolved, 且 onFulfilled 不为函数, 则应该返回和前面 Promise 值一样的新 Promise
        if (typeof onFulfilled !== 'function') {
            return Promise.resolve(this.value);
        }

        executor = onFulfilled;
    } else if (this.status === 'rejected') {
        // 如果前面的 Promise 状态为 rejected, 且 onRejected 不为函数, 则应该返回和前面 Promise 错误原因一样的新 Promise
        if (typeof onRejected !== 'function') {
            return Promise.reject(this.value);
        }

        executor = onRejected;
    }

    try {
        let result = executor(this.value);
        return Promise.resolve(result);
    } catch (err) {
        return Promise.reject(err);
    }
}
```
现在, 上面的代码便能正常运行了。
# 多个 then 方法的调用
上面我们的代码中, 当 Promise 状态为 `pending` 时, `then` 方法返回的并不是一个新的 Promise 对象, 而是原本的 Promise。当 Promise 状态改变后, 需要清空 `then` 方法挂载的函数队列。在这个过程中, 我们修改了原本的 Promise 的最终值。也就是如果我们运行了上述测试用例后, `p` 的最终值会是 `5`, 而不是它最初创建时指定的 `1`。因此需要对代码做出修改, **让每个 `then` 方法都应该返回新的 Promise 对象, 而不是对旧的 Promise 对象进行修改。**

todo
以下内容为总结, 如果要写在文章里需要另行扩展。
归根结底就一句话, onResolvedFns/onRejectedFns 中存放的函数, 都是同级调用, 假如当前 Promise 是 p, 那么 p 的onResolvedFns/onRejectedFns 中存放的函数, 都是由 p.then 传入的参数。p.promiseList 中存放的, 是按照 p.then 调用顺序存放的、p.then 返回的 Promise。
如果涉及到更深层次的 then 调用, 首先要在 p.promiseList 中找到对应的 Promise。由更深层次 then 调用传入的函数, 就存放在这些 Promise 的 onResolvedFns/onRejectedFns 中。
在执行的时候, 也要小心, 要广度优先执行, 先执行完所有同级 then 调用, 再执行更深层次的 then 调用。
下面是对这个过程的详细阐释:
假设最初的 Promise 为 p, p 中的 PromiseList 保存的是 p.then 返回的 Promise。当 p 的状态变为 resolve 或 reject 时, 会按照 PromiseList 中的顺序, 执行对应的 onResolved/onRejected 代码。
如果当前 PromiseList 中的元素, 假设会 a, a.PromiseList 不为空, 则将 a.PromiseList 中的元素拿出来, 放在 p.Promise 的末尾, 同时在将 a.onResolvedFns/a.onRejectedFns 绑定 this 和 val 之后, 放入 p.onResolvedFns/p.onRejectedFns 中。

# 参考资料
* [Promise/A+](https://promisesaplus.com)
* [ECMAScript 6 入门教程](https://es6.ruanyifeng.com/)
* [MDN Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)