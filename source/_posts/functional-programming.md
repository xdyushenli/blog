---
title: 初探函数式编程
date: 2020-06-12 09:42:15
tags: [ javascript, ramda, 函数式编程 ]
---
# 什么是函数式编程？

# 函数式编程的基本概念

# 为什么推荐在 React 中使用函数式编程？

# 推崇的编程风格
推崇函数式编程，核心理念是数据不变性以及函数无副作用。
推崇通过构建函数序列来完成工作。每个函数对数据进行变换，并将结果传递给下一个函数。这种范式被称为`pipeline`。
> The new function takes the same number of arguments as the first function it is given. It then “pipes” those arguments through each function in the list. It applies the first function to the arguments, passes its result to the second function and so on. The result of the last function is the result of the pipe call.

> Note that all of the functions after the first must only take a single argument.

> By combining several functions in specific ways, we can start to write more powerful functions.

可以通过提供的高阶函数 API 来对函数进行复用。

函数式编程中，函数就是一切。函数可以是操作本身，也可以是被操作的数据。

# 函数式编程
函数式编程着眼的**函数**，而不是**过程**。它强调如何通过函数的组合变换来解决问题，而不是我通过写什么语句去解决问题。

有众多单一目的的小程序，一个小程序只实现一个功能，多个程序组合完成复杂任务。

# Ramda API 的特点
所有对数据的操作——包括加减、修改数组元素等——都需要通过内部的 API 来操作。

在数据不可变的思想有点类似于 ImmutableJS，都需要通过框架提供的 API 来对数据进行操作。

# 为什么数据的 Immutability 对 React 很重要？
在 React 中，当上层组件的数据变化并重新渲染时，即使在下层组件的数据没有变化的情况下，也会导致下层组件跟着刷新。为了限制每次数据变化带来的更新范围，React 推出了 shouldComponentUpdate 和 getDrivedStateFromProps。在这两个生命周期中，用户可以通过比较 state 和 props 来确认是否更新某个组件。

让我们暂停并思考一下，上述提议看起来很美好，但有一个致命的缺陷，那就是我们使用的是 JavaScript。在 JavaScript 中，传递复杂对象的方式是引用传递，也就是说我们不能通过比较 state 更改前后是否相等来判断 state 是否发生了变化，因为它们本质上都指向同一个对象！
```js
const prevState = {
    name: 'Bob',
}

const nextState = prevState;

nextState.name = 'Bill';

// true
prevState === nextState;
```

到这里让我们在回过头来看看`Immutability`。在 JavaScript 中，数据 Immutability 的具体表现在于：每次数据将要发生变化时，都先将原来的数据复制一份到新对象，然后在新对象上进行修改。todo
> When I’m working in an immutable fashion, once I initialize a value or an object I never change it again. That means no changing elements of an array or properties of an object.

If I need to change something in an array or object, I instead return a new copy of it with the changed value. Later posts will talk about this in great detail.

```js
const prevState = {
    name: 'Bob',
}

const nextState = Object.assign({}, prevState);

nextState.name = 'Bill';

// false
prevState === nextState;
```

在 React 中使用这种编程范式时，需要根据数据量来具体划分。
当数据量较小时，可以采用直接深复制的方法实现数据不可变。当数据量比较大时，可以使用 ImmutableJS 等框架来实现这一范式。

# 为什么我们需要 curry？
curry 的意思是将一个多参函数，转换为一个依次调用的单参函数。
```js
f(a,b,c) → f(a)(b)(c)
```
curry 的本质是**允许函数被部分地使用**，而不是单纯的将一个多参函数转化为单参函数。

**柯里化 ≠ 部分函数应用！！**柯里化强调的是生成单元函数，部分函数应用的强调的固定任意元参数。

我们在使用某个 curry 的函数时，可以先传入一些参数，然后该函数会返回一个接受剩余参数、同时内置之前传入的参数的函数，方便下次使用。这个过程就是 curry。

curry 后的函数有一个比较通俗的名字：高阶函数（High-order Function）。类似于高阶组件。

那么 curry 带来了什么好处呢？
通过 curry 可以做到部分地使用函数，方便对函数进行组合，从而大大提高函数的复用程度。

简单来说，curry 就是支持下面这么调用的函数：
todo
```js
// 实现如下函数
// 3
add(1, 2);
// 3
add(1)(2);
// 6
add(1)(2)(3);

// 我怎么知道后续有没有调用了呢？？
function add(...nums) {
    let sum = nums.reduce((curr, sum) => sum += curr, 0);

    return (...nums) => {
        
    }
}
```

# JavaScript 中的一等公民：函数
By "first-class", I mean that functions can be used in the same way as other kinds of values. You can:

* refer to them from constants and variables
* pass them as parameters to other functions
* return them as results from other functions

# Part 1
纯函数
Immutability

# Part 2
> The new function takes the same number of arguments as the first function it is given. It then “pipes” those arguments through each function in the list. It applies the first function to the arguments, passes its result to the second function and so on. The result of the last function is the result of the pipe call.

> Note that all of the functions after the first must only take a single argument.

> By combining several functions in specific ways, we can start to write more powerful functions.

compose & pipe

# Part 3
> Functions that take or return other functions are known as “higher-order functions”.

partial & partialRight

curry

> In Ramda, a curried function can be called with only a subset of its arguments, and it will return a new function that accepts the remaining arguments. If you call a curried function with all of its arguments, it will call just call the function.

> Notice that to make curry work for us, we had to reverse the argument order. This is extremely common with functional programming, so almost every Ramda function is written so that the data to be operated on comes last.
You can think of the earlier parameters as configuration for the operation. 

# Part 4
impreative programming VS Declarative programming
> imperative programming is a style of programming where the programmers tell the computer what to do by telling it how to do it.
Declarative programming is a style of programming where the programmers tell the computer what to do by telling it what they want. The computer then has to figure out how to produce the result.

> Functional programming is considered a subset of declarative programming. In a functional program.

Ramda 提供了一系列 API 来实现计算、比较及逻辑控制。

计算和比较还可以理解，为什么要提供用于 if-else 的 API 呢？
考虑以下场景。使用 const 定义一个变量，这要求我们立即对其进行赋值。姑且称这个常量为 a 吧。a需要通过一系列复杂的计算才能得到，所以不能使用三元表达式。计算条件是：符合某些条件的话，经过复杂计算得到一个值；不符合该条件就经过另一些复杂计算得到另一个值。这时你会怎么做？
常见的方法是使用 if-else，但这是个常量啊，无法进行后续更改。这个时候就可以用 Ramda 提供的 if-else API 了。通过这些 API，我们可以形成一个表达式，可以在定义的时候赋值给常量。

# Part 5
pointfree style

# Part 6
Topic: How to manipulate object in a immutable way by using Ramda?

To read properties, Ramda provides a lot helps, such as: 
prop, pick, has, hasIn, path, propOr, pathOr, etc.

Ramda won't change object directly. Instead, it provides some useful APIs to help us change object in a immutable way. If we change object by using these APIs, we can make sure that we always get a new object.

add & update property: assoc & assocPath

delete property: dissoc & dissocPath & omit

transform property: evolve

merge object: merge

# Part 7
Topic: How to manipulate array in a immutable way by using Ramda?

reading array elements: nth, slice, contains, head, tail, last, init, take, takeLast.

add elements: insert, append, prepend.

update elements: update, adjust

merge array: concat

remove elements: remove, without, drop, dropLast

# Part 8
lens is short for lenses.

> Lenses concludes by introducing the concept of a lens, a construct that allows us to focus on a small part of a larger data structure. Using the `view`, `set`, and `over` functions, we can read, update, and transform the focused value in the context of its larger data structure.

# Part 9
> If you’re interested in more advanced functional topics, here are some places you can go:

* Transducers: There’s [a good introductory article](http://simplectic.com/blog/2015/ramda-transducers-logs/) on parsing logs with transducers.

* Algebraic Data Types: If you’ve read much about functional programming, you’ll have heard about algebraic types and terms such as “Functor”, “Applicative”, and “Monad.” If you’re interested in exploring these ideas in the context of Ramda, check out the [ramda-fantasy](https://github.com/ramda/ramda-fantasy) project, which implements several data types that comply with the [Fantasy Land Specification](https://github.com/fantasyland/fantasy-land) (a.k.a. the Algebraic JavaScript Specification).

todo
有一个小问题：使用 Ramda 每次更改对象返回的都是新对象。如果我们在一个事件处理函数中修改了 state 两次，假设原来的值为 1，我们先改为 2，再改为 1。此时这个对象应该是新对象，那岂不是还会触发更新？React 的 batchUpdate 能识别出来吗？

# What is a functor?
将一种数据结构处理为另一种数据结构的函数成为 functor（也可以转化为同种数据结构）。

在函数式编程的流水线类比中，functor 可以理解为流水线上的某个加工车间。

定义了 map 函数，且只能通过 map 函数来操作数据的对象称为 functor.

todo
引申：是否可以认为函数式编程中的函数都属于 functor 呢？

# lenses 详解
todo
lens 提供了一个对象的引用。
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

# 参考资料
* [Why Ramda?](https://fr.umio.us/why-ramda/)
* [为什么不可变性对 React 很重要？](https://python.freelycode.com/contribution/detail/179)
* [Thinking in Ramda](https://randycoulman.com/blog/categories/thinking-in-ramda/)
* [简明 JavaScript 函数式编程——入门篇](https://juejin.im/post/5d70e25de51d453c11684cc4)
* [函数式编程进阶：杰克船长的黑珍珠号](https://juejin.im/post/5e09554bf265da33b5074d7f)
* [函数式引用 lenses](https://kms.netease.com/article/4748)
* [transduce，高级抽象foldable](https://kms.netease.com/article/4303)

# 任务
todo
发表 KMS
学习标准 HTTP、CSS、JavaScript、Promise
学习标准发布流程，以及流程背后的技术原理
webpack
Linux的开发习惯
React React 15里的DIFF算法，16里的Fiber算法
regularjs