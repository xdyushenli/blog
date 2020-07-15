---
title: 初探函数式编程
date: 2020-06-12 09:42:15
tags: [ javascript, ramda, 函数式编程 ]
---
# 前言
这是一篇介绍纯函数的博客。

# 什么是函数式编程？
JavaScript 不是一门函数式编程的语言，但是能够很好的支持函数式编程范式。因为函数在 JavaScript 中是作为`一等公民（First-class citizen）`的。

# 什么是“第一公民”？
所谓的**“将函数作为第一公民”**是指函数能够做到以下几点：
* 被赋值给变量。
* 能够作为函数的参数。
* 能够作为函数的返回值。

在 JavaScript 中，函数本质上不过是对象而已，所以能够很轻易地满足上述条件。

# 函数式编程的中心思想？
函数式编程有两个中心思想，一个是**拆分 + 组合**，另一个是**数据不可变**。

## 拆分 + 组合
想象一下你有一个十分复杂的积木军舰，但你不想要军舰，你想要一辆积木坦克。但军舰并不会凭空变成坦克，不过还好你手里的是积木，于是你把军舰拆了，用军舰的零件组装了一辆坦克，目标圆满完成！

上面的事例传递出了一个思想：通过拆分和组合，即使是相同的东西，我们也能实现不同的复杂功能。
Linux 有一个很著名的设计理念：**让一个程序专注做一件事。**实现复杂功能的函数就像是一个复杂的积木玩具，专注于完成一个小功能的函数就像是一个小积木块。通过尽可能地拆分复杂功能的函数，我们可以得到许多只实现一个功能的简单函数。通过简单函数的排列组合，我们可以做到像积木一样，实现不同的复杂功能。

## 数据不可变性（Immutability）
这里的数据不可变性并不是说我们在程序运行时不能修改数据，而是说不在原数据上直接进行修改。

也就是说，我们每次修改之后的数据都应该指向内存中一块新的区域，而不是和修改前的数据指向同一区域。

# 所以这一切和 React 有什么关系呢？
别急，下面我们会来解释**为什么推荐在 React 中使用函数式编程**以及**为什么数据不可变对 React 很重要。**

在 React 中，当上层组件的数据变化并重新渲染时，即使在下层组件的数据没有变化的情况下，也会导致下层组件跟着刷新。为了限制每次数据变化带来的更新范围，React 推出了 `shouldComponentUpdate` 和 `getDrivedStateFromProps`。在这两个生命周期中，用户可以通过比较 `state` 和 `props` 来确认是否更新某个组件。

现在让我们暂停并思考一下，上述提议看起来很美好，但有一个致命的缺陷，那就是**我们使用的是 JavaScript！**在 JavaScript 中，传递复杂对象的方式是引用传递，也就是说我们不能通过比较 `state` 更改前后是否相等来判断 `state` 是否发生了变化，因为它们本质上都指向同一个对象！

```js
const prevState = {
    name: 'Bob',
}

function changeState(state) {
    state.name = 'Bill';
    return state;
}

const nextState = changeState(prevState);

// true
prevState === nextState;
```

这时候，数据不可变便可以发挥它的作用了！每次变化的数据指向的都是新的内存，因此 React 可以轻易地识别数据有没有发生变化，从而避免不必要的更新。

但是，要想在 JavaScript 中实现数据不可变，需要进行一些额外的操作：每次数据将要发生变化时，都先将原来的数据复制一份到新对象，然后在新对象上进行修改。

```js
const prevState = {
    name: 'Bob',
}

const nextState = Object.assign({}, prevState);

nextState.name = 'Bill';

// false
prevState === nextState;
```

在 React 中使用这种编程范式时，需要根据数据量来具体判断。
当数据量较小时，可以采用直接深复制的方法实现数据不可变。当数据量比较大时，可以使用 `ImmutableJS` 等框架来实现这一范式。

# 函数式编程 vs 命令式编程
todo
函数式编程着眼的**函数**，而不是**过程**。它强调如何通过函数的组合变换来解决问题，而不是通过写什么语句去解决问题。

推崇函数式编程，核心理念是数据不变性以及函数无副作用。
推崇通过构建函数序列来完成工作。每个函数对数据进行变换，并将结果传递给下一个函数。这种范式被称为`pipeline`。


可以通过提供的高阶函数 API 来对函数进行复用。

函数式编程中，函数就是一切。函数可以是操作本身，也可以是被操作的数据。

# 函数式编程的重要概念
## 纯函数
`纯函数（pure function）`指的是没有**“副作用”**的函数。
其中，会带来副作用的操作包括但不限于：创建日期对象、请求接口等。

todo 引用透明的英文
纯函数的特征是：输入同样的参数，总会得到相同的输出。这被称为`引用透明性（）`。

## 柯里化
`柯里化（curry）`也是函数式编程的一个重要概念。

### 什么是柯里化？
我们常说的将函数柯里化就是将一个多参函数，转换为一个依次调用的单参函数。

```js
f(a,b,c) → f(a)(b)(c)
```

### 为什么我们需要柯里化？
`curry` 的最大作用是**允许函数被部分地使用**，而不是单纯的将一个多参函数转化为单参函数。

我们在使用某个柯里化的函数时，可以先传入一些参数，然后该函数会返回一个接受剩余参数、同时内置之前传入的参数的函数，方便下次使用。这样就可以做到部分地应用函数。
部分地使用函数可以让我们很方便地从原有函数中生成新的函数，并对已有函数进行组合，从而提高函数的复用程度。

**柯里化 ≠ 部分函数应用！！**柯里化强调的是生成单元函数，`部分函数应用（partially applied function）`的强调的固定任意元参数。

### 有没有形象点的比喻？
当然有！
todo 积木工厂？可以不写！

### curry 函数的实现
下面是一个 curry 函数的简单实现：
```js
function curry(func, ...args) {
    if(typeof func !== 'function') {
        throw new Error('curry: arg is not a function!');
    }

    const argsLength = func.length;
    const internalArgs = [...args];
    const funcWrapper = function (...args) {
        internalArgs.push(...args);

        // 之所以是 >=, 是因为使用 ... 定义的参数不会使函数的 length 属性加一
        if(internalArgs.length >= argsLength) {
            // 清空内置参数数组, 便于再次调用 curry 后的函数
            internalArgs.length = 0;
            return func.apply(this, internalArgs);
        } else {
            return funcWrapper;
        }
    }

    return funcWrapper;
}
```

现在让我们来验证一下：

```js
function add(a, b, c) {
    return a + b + c;
}

function sum(...nums) {
    return nums.reduce((prev, total) => prev + total, 0)
}

const curryAdd = curry(add);
const currySum = curry(sum);

// function (...args) {...}
curryAdd(1, 2);
// 6
curryAdd(3, 4);

// 1
currySum(1);
// 6
currySum(1, 2, 3);
```

## 部分函数应用
`部分函数应用（partially applied function）`与柯里化十分相似，不同的是：严格的柯里化要求每次只传入一个参数，而部分函数应用强调一次可以固定任意个参数。

## 函子
`函子（functor）`也是函数式编程中的一个重要概念。
简单来说，函子就是具有某个值、且只能通过 `map` 方法来操作该值的对象。
其中，`map` 函数会对当前函子的值做一定的处理，返回一个新的函子。

在函数式编程的流水线类比中，`functor` 可以理解为流水线上的某个加工车间。

> Tip: 在 Ramda 的源码中，将函子上的 `map` 函数称为 `fantasy map`。

todo《入门》中提到的几个 functor

纯函数、curry、partial、functor、transducers、

# Ramda 中的 lens
`lens` 的意思是`透镜`。
在 Ramda 中，`lens` 提供了一个对象的引用。

over：原地修改
set： 修改
view：访问
over、set 函数的作用？
* [函数式引用 lenses](https://kms.netease.com/article/4748)

todo
lens源码实现

# transducer
参数列表
1. 每个数组元素上要执行的操作
2. reduce 方法接受的函数，有两个参数，分别为累加值和当前值（第一个参数调用的结果）
3. 初值
4. 目标数组

* [transduce，高级抽象foldable](https://kms.netease.com/article/4303)
[a good introductory article](http://simplectic.com/blog/2015/ramda-transducers-logs/

# What else?
到这里就结束啦。
不过这里有一份阅读 Think in Ramda 专栏的[摘要](todo)，介绍了一些函数式编程的基本概念以及 Ramda API，回顾时可以看一下。

# 参考资料
* [Why Ramda?](https://fr.umio.us/why-ramda/)
* [为什么不可变性对 React 很重要？](https://python.freelycode.com/contribution/detail/179)
* [Thinking in Ramda](https://randycoulman.com/blog/categories/thinking-in-ramda/)
* [简明 JavaScript 函数式编程——入门篇](https://juejin.im/post/5d70e25de51d453c11684cc4)
* [函数式编程进阶：杰克船长的黑珍珠号](https://juejin.im/post/5e09554bf265da33b5074d7f)
* [第一类对象](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%B8%80%E9%A1%9E%E7%89%A9%E4%BB%B6)

未阅读
* [函数式引用 lenses](https://kms.netease.com/article/4748)
* [transduce，高级抽象foldable](https://kms.netease.com/article/4303)
* [Transducers: Supercharge your functional JavaScript](https://www.jeremydaly.com/transducers-supercharge-functional-javascript/)