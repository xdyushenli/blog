---
title: 初探函数式编程
date: 2020-06-12 09:42:15
tags: [ javascript, ramda, 函数式编程 ]
---
# 前言
这是一篇介绍函数式编程 & `ramda` 的博客。

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

## 还有什么值得一提的？
函数式编程着眼的**函数**，而不是**过程**。它强调如何通过函数的组合变换来解决问题，而不是通过写什么语句去解决问题。

推崇函数式编程，核心理念是数据不变性以及函数无副作用。
推崇通过构建函数序列来完成工作。每个函数对数据进行变换，并将结果传递给下一个函数。这种范式被称为`pipeline`。

可以通过提供的高阶函数 API 来对函数进行复用。

函数式编程中，函数就是一切。函数可以是操作本身，也可以是被操作的数据。

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

# 函数式编程的重要概念
## 纯函数
`纯函数（pure function）`指的是没有**“副作用”**的函数。
其中，会带来副作用的操作包括但不限于：创建日期对象、请求接口等。

纯函数的特征是：输入同样的参数，总会得到相同的输出。这被称为`引用透明性（referential transparency）`。

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

todo 书中的函子

# Ramda 中值得注意的 API
## lens
`lens` 的意思是`透镜`。
在 Ramda 中，`lens` 方法提供了一种数据的处理模式。这么说可能比较抽象，还是结合实例来看下吧。

先来介绍一下 `lens` 方法。
`lens` 方法：需要传入 `getter` 与 `setter`，返回封装了 `getter` 和 `setter` 方法的 `lens`。
使用方法如下：
```js
const xLens = R.lens(R.prop('x'), R.assoc('x'));

R.view(xLens, {x: 1, y: 2});            //=> 1
R.set(xLens, 4, {x: 1, y: 2});          //=> {x: 4, y: 2}
R.over(xLens, R.negate, {x: 1, y: 2});  //=> {x: -1, y: 2}
```

**可以看到, 通过 lens 我们可以将数据的处理方法抽象出来进行复用, 但 lens 方法本身并不会修改数据**, 真正查看/修改数据的是 `view`、`set` 和 `over` 方法。

这三个方法的作用分别是：
* `over`：将变换函数应用于透镜。
* `set`：修改透镜访问的值。
* `view`：访问。

其中，`over` 和 `set` 的相同点是它们都会返回新的对象，不同点是对于修改值的方式不同。

从上面的例子可以得到两个结论：
* 调用 `view` 方法，会调用作为参数传入的 `lens` 的 `getter`，并将后面的参数传入 `getter`。
* 调用 `set` & `over` 方法，会调用传入的 `lens` 的 `setter`，并将后面的参数传入 `setter`。

除了 `lens` 之外，还有三个方法可以用于聚焦某个值，分别是 `lensIndex`、`lensProp` 和 `lensPath`。
**这三种方法本质上都是 lens 的简化版本，可以看做传入特定 getter 与 setter 的 lens！**

`lensIndex` 相当于
```js
R.lensIndex(index)
// ==>
R.lens(R.nth(index), R.update(index))
```

`lensProp` 相当于
```js
R.lensProp('x')
// ==>
R.lens(R.prop('x'), R.assoc('x'));
```

`lensPath` 相当于
```js
const path = ['a', 'b'];
// {a: {b: 1}}
R.lensPath(path)
// ==>
R.lens(R.path(path), R.assocPath(path))
```

### lens 的源码解析
在知晓了 `lens` 的定义，以及与其搭配的几个方法后，我们就可以来看看 `lens` 的源码了。

todo
```js
const curry = (fn) => {
    return (...args) => {
        if (fn.length <= args.length) {
            return fn(...args);
        } else {
            return curry(fn.bind(undefined, ...args));
        }
    }
};

const dispatchable = (methodNames, transducerCreator, fn) => {
    // 缩略版
    return function () {
        const obj = arguments[arguments.length - 1];
        
        if (!Array.isArray(obj)) {
            let idx = 0;

            while (idx < methodNames.length) {
                if (typeof obj[methodNames[idx]] === 'function') {
                    return obj[methodNames[idx]].apply(obj, Array.prototype.slice.call(arguments, 0, -1));
                }

                idx += 1;
            }
        }
    };
}

// xmap
const map = curry(dispatchable(['fantasy-land/map', 'map'], {}, function map(fn, functor) {
    // 缩略版
    return fn.call(this, functor.apply(this, arguments));
}));

const lens = curry(function lens(getter, setter) {
    return function (toFunctorFn) {
        return function (target) {
            return map(
                function (focus) {
                    return setter(focus, target);
                },
                toFunctorFn(getter(target))
            );
        };
    };
});

const Const = function(x) {
    return {value: x, 'fantasy-land/map': function() { return this; }};
};

const Identity = function(x) {
    return {value: x, map: function(f) { return Identity(f(x)); }};
};

const always = curry(function always(val) {
    return function() {
        return val;
    };
});

const view = curry(function view(lens, x) {
    // Using `Const` effectively ignores the setter function of the `lens`,
    // leaving the value returned by the getter function unmodified.
    return lens(Const)(x).value;
});

const over = curry(function over(lens, f, x) {
    // The value returned by the getter function is first transformed with `f`,
    // then set as the value of an `Identity`. This is then mapped over with the
    // setter function of the lens.
    return lens(function(y) { return Identity(f(y)); })(x).value;
});

const set = curry(function set(lens, v, x) {
    return over(lens, always(v), x);
});

// =========================================
// 测试代码
const data = { x: 1, y: 2 };
const xLens = lens(R.prop('x'), R.assoc('x'));

const viewValue = view(xLens, data);
const setValue = set(xLens, 2, data);
const overValue = over(xLens, R.negate, data);

// 1
console.log(viewValue);
// { x: 2, y: 2 }
console.log(setValue);
// { x: -1, y: 2}
console.log(overValue);

// false
console.log('setValue', setValue === data);
// false
console.log('overValue', overValue === data);
```

## transduce
### 什么是 transducer？transducer 的作用是什么？
`transducer` 接受一个对象或数组，并在每个属性或元素上执行一定的操作，并返回由操作结果组成的对象。
感觉上有点像 `map` 与 `reduce` 的结合，并且这个 `map` 允许跳过元素。

在 `ramda` 中提供了 `transduce` API，这个 API 接受四个参数：
1. `transformer`，即要在每个元素或数组上执行的操作。
2. 函数，类似于 `javascript` 中 `reduce` 方法接受的函数，有两个参数：第一个参数为累加值，第二个参数为当前值。
累加值的初值是第三个参数，当前值是通过在当前元素上调用第一个参数得到的。
3. 初值。
4. 源数组或对象。

### transducer 的应用场景？
想象这样一个场景，你有一个由人名组成的数组，就像这样：

```js
const names = ['tom', 'joe', 'eric', 'lily', 'ted'];
```

你需要从中获取所有名字中有 `e` 这个字母的人名，并将它们的字母大写，并且出于某种特殊原因，你需要将这些人名反过来。
在 `ramda` 中，我们可以这么写：

```js
const transformer = R.compose(
    R.filter(x => /e/i.test(x)),
    R.map(R.toUpper),
    R.map(R.reverse),
)

// ["EOJ", "CIRE", "DET"]
transformer(names);
```

在这里，数组被遍历了三次才得到最终结果，并且当我们还需要进行其他操作时，还需要继续遍历！

通过 `R.transduce` 我们可以将上面的代码转化为如下形式：

```js
const transformer = R.compose(
    R.filter(x => /e/i.test(x)),
    R.map(R.toUpper),
    R.map(R.reverse),
)

R.transduce(transformer, R.flip(R.append), [], names);
```

**与传统的 map 方式相比，transduce 只会遍历一遍数组就能得到最终结果！**这也是为什么我们需要用 `transduce` 来进行复杂操作的原因。

# What else?
到这里就结束啦。
不过这里有一份阅读 Think in Ramda 专栏的[摘要](https://github.com/xdyushenli/blog/blob/master/source/files/functional-programming/Thinking-in-Ramda.md)，介绍了一些函数式编程的基本概念以及 Ramda API，回顾时可以看一下。

# 参考资料
* [Why Ramda?](https://fr.umio.us/why-ramda/)
* [为什么不可变性对 React 很重要？](https://python.freelycode.com/contribution/detail/179)
* [Thinking in Ramda](https://randycoulman.com/blog/categories/thinking-in-ramda/)
* [简明 JavaScript 函数式编程——入门篇](https://juejin.im/post/5d70e25de51d453c11684cc4)
* [函数式编程进阶：杰克船长的黑珍珠号](https://juejin.im/post/5e09554bf265da33b5074d7f)
* [第一类对象](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%B8%80%E9%A1%9E%E7%89%A9%E4%BB%B6)
* [函数式引用 lenses](https://kms.netease.com/article/4748)
* [transduce，高级抽象foldable](https://kms.netease.com/article/4303)
* [Transducers: Supercharge your functional JavaScript](https://www.jeremydaly.com/transducers-supercharge-functional-javascript/)
* [a good introductory article](http://simplectic.com/blog/2015/ramda-transducers-logs/)
* [todo](https://juejin.im/post/6844903762641829901)