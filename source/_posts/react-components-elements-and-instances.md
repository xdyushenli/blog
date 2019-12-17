---
title: React Components, Elements, and Instances
date: 2019-12-17 19:20:45
tags: [ 翻译计划, react ]
---
在 React 初学者眼中, 常常会混淆组件（Components）、组件实例（Instances）以及元素（Elements）这三个概念。那么为什么需要三个不同的术语来指代展示在屏幕上的东西呢?

# 繁琐的实例管理
如果你刚刚接触到 React, 那么多半之前之和组件类（Components Class）以及实例（Instance）打过交道。举个例子, 你可以通过声明一个类来创建一个 `Button` 组件。当应用运行的时候, 针对 `Button` 类可能会存在多个实例, 每个实例都有自己的 `props` 和 `state`。这是传统的面向对象的 UI 编程的思路。那么为什么要引入 **元素（Element** 呢?
在传统的 UI 模型中, 需要你自己创建实例和销毁实例。如果一个 `Form` 组件想要渲染一个 `Button` 组件, 那么需要在 `Form` 组件中创建 `Button` 组件的实例。当有新信息输入时, 需要手动对 `Button` 组件进行更新。
```js
class Form extends TraditionalObjectOrientedView {
  render() {
    // 从目前显示的视图中读出信息
    const { isSubmitted, buttonText } = this.attrs;

    if (!isSubmitted && !this.button) {
      // 如果表单尚未提交, 则创建一个 Button 实例
      this.button = new Button({
        children: buttonText,
        color: 'blue'
      });
      this.el.appendChild(this.button.el);
    }

    if (this.button) {
      // Button 实例已显示在屏幕上, 更新其显示的文字
      this.button.attrs.children = buttonText;
      this.button.render();
    }

    if (isSubmitted && this.button) {
      // 表单已提交, 销毁 Button 实例
      this.el.removeChild(this.button.el);
      this.button.destroy();
    }

    if (isSubmitted && !this.message) {
      // 表单已提交, 显示提交成功的提示
      this.message = new Message({ text: 'Success!' });
      this.el.appendChild(this.message.el);
    }
  }
}
```
上面是一段伪代码。每个组件实例都必须保留对其 DOM 节点以及子组件实例的引用, 以便在合适的时候能够创建、更新和销毁它们。代码量会随着组件可能出现的状态的数量而呈几何式增长, 并且由于子组件的创建、更新和销毁都是在父组件中完成的, 这让解耦父子组件变得十分困难。
那么 React 有什么不同呢?

# 元素（Elements）
在 React 中, 元素（Elements）就是为了解决上述痛点而生的。**一个元素的本质是一个普通对象, 元素的作用是描述它所代表的DOM 节点或组件实例**。一般来说, 元素只包含三种信息。一是组件类型（比如 `Button`）, 二是组件本身的属性（比如 `color`）, 三是组件内部所包含的子组件。
一个元素并不是真正的实例。恰恰相反, 它只向 React 传达一种希望在屏幕上呈现什么的信息。在元素上, 你不能调用任何方法, 因为它只是一个具有两个字段的不可变描述对象：`type: (string | ReactClass)` 和 `props: Object`[1]()。todo

## DOM元素
当一个元素的 `type` 字段值是一个字符串时, 这个元素就代表了以该字符串为标签名的 DOM 节点, 而 `props` 字段相当于这个元素上的属性（Attributes）。那么 React 便会根据这些信息进行渲染。举个例子：
```js
{
  type: 'button',
  props: {
    className: 'button button-blue',
    children: {
      type: 'b',
      props: {
        children: 'OK!'
      }
    }
  }
}
```
上面这个对象是对如下 HTML 的描述：
```html
<button class='button button-blue'>
  <b>
    OK!
  </b>
</button>
```
注意这里元素之间是如何嵌套的。按照规范, 当我们想要创建一棵嵌套的元素树时, 会将一个或多个子元素添加到 `chileren` 属性中。
值得注意的是, **无论是子元素还是父元素, 都只是一种对实际渲染的描述而并非真正的实例**。当你创建它们时, 并不会引用任何真正显示在屏幕上的东西。你可以创建它们, 然后置之不理, 这并没有什么不妥。
React 元素十分易于遍历, 并且无需额外解析。和真正的 DOM 元素相比, 它们显得十分轻量, 毕竟说到底它们只是对象而已！

## 组件元素
元素的 `type` 值也可以是一个代表 React 组件的类或者函数。
```js
{
  type: Button,
  props: {
    color: 'blue',
    children: 'OK!'
  }
}
```
**和描述 DOM 节点的元素一样, 元素也可以用于描述组件。描述 DOM 节点的元素和描述组件的元素可以相互嵌套。**这就是 React 的核心思想。
这个特性让我们可以将一个具有特定 `color` 的 `Button` 组件定义为一个 `DangerButton` 组件, 而完全不用担心 `Button` 的本质是一个 `<button>`、`<div>` 还是任何其他的东西。
```js
const DangerButton = ({ children }) => ({
  type: Button,
  props: {
    color: 'red',
    children: children
  }
});
```
你可以在一棵元素树中混合使用 DOM 元素和组件元素。
```js
const DeleteAccount = () => ({
  type: 'div',
  props: {
    children: [{
      type: 'p',
      props: {
        children: 'Are you sure?'
      }
    }, {
      type: DangerButton,
      props: {
        children: 'Yep'
      }
    }, {
      type: Button,
      props: {
        color: 'blue',
        children: 'Cancel'
      }
   }]
});
```
如果你更喜欢 `jsx` 语法的话, 这么写也是等价的：
```html
const DeleteAccount = () => (
  <div>
    <p>Are you sure?</p>
    <DangerButton>Yep</DangerButton>
    <Button color='blue'>Cancel</Button>
  </div>
);
```
这种机制有助于组件之间彼此独立。通过简单的组合关系, 便能表示出 `is-a` 和 `has-a` 关系：
* `Button` 是具有特定属性的 `<button>` DOM 节点。
* `DangerButton` 是具有特定属性的 `Button`。
* `DeleteAccount` 是一个包含了`Button` 和 `DangerButton` 的 `<div>`。

## 使用组件来封装元素树

# 结语

# 注释
1. 

# 笔者说


# 原文链接
[React Components, Elements, and Instances](https://react.docschina.org/blog/2015/12/18/react-components-elements-and-instances.html)