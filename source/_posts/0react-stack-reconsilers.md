---
title: 0react-stack-reconsilers
date: 2019-12-15 16:27:23
tags:
---
# 实现说明
这部分是一个对于如何实现 Stack Reconciler 的总结。
这部分比较晦涩难懂, 并且要求对 React 公共 API 以及核心代码、渲染器和 Reconcilers 是如何划分的有一定了解。如果对 React 源码不是很熟悉, 请先阅读源码概述。
这部分同样要求你明白在 React 中组件、实例和元素的区别。
Stack Reconciler 在 React 15 以及更早的版本中使用。源码位置位于`src/rederers/shared/stack/reconciler`。

## 从零开始构建 React
Paul O'Shannessy 关于[如何从零开始构建 React](https://www.youtube.com/watch?v=_MAD4Oly9yg) 的演讲大大启发了该文章。
本文以及他的演讲都是对源码的简化概述, 因此要想更好地了解 React 其中的原委, 最好对二者都进行一定了解。

## 概述
reconciler 本身并没有公共 API。像 React DOM 和 React Native 这样的渲染器使用 reconciler 高效更新使用者编写的 React 组件, 进而更新用户页面。

## 递归挂载
假设我们需要首次将一个组件挂载：
```js
ReactDOM.render(<App />, rootEl);
```
React DOM 会把 `<App />` 传递给 reconsiler。记住 `<App />` 是一个 React 元素, 描述的是应该去渲染什么。你可以认为它本质上是一个普通的对象。
```js
console.log(<App />);
// { type: App, props: {} }
```
reconciler 会对 `App` 进行检测, 以确定它是一个函数还是一个类。
如果 `App` 是一个函数, 那么 reconciler 会调用 `App(props)` 来获取需要呈现的元素。
如果 `App` 是一个组件, 那么 reconciler 会以 `new App(props)` 的形式来创建一个 `App` 实例, 并调用 `componentWillMount()` 这一个生命周期方法, 然后再调用 `render()` 方法以获取需要呈现的元素。
不管通过哪种方式, reconciler 都会了解到 `App` 应该最终呈现出的元素。
整个过程是递归的。`App` 可以渲染为 `<Greeting />`, `Greeting` 可以渲染为一个 `<Button />`, 以此类推。reconciler 在呈现某个组件时会递归地“深入挖掘”用户自定义的组件, 直到整个组件被渲染出来为止。
你可以通过如下伪代码来了解这一进程：
```js

```
