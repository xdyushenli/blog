---
title: React Components, Elements, and Instances
date: 2019-12-17 19:20:45
tags: [ 翻译计划, react ]
---
在 React 初学者眼中, 常常会混淆**组件（Components）**、**组件实例（Instances）**以及**元素（Elements）**这三个概念。那么为什么需要三个不同的术语来指代展示在屏幕上的东西呢?

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
注意这里元素之间是如何嵌套的。按照规范, 当我们想要创建一棵嵌套的元素树（Element Tree）时, 会将一个或多个子元素添加到 `chileren` 属性中。
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

# 使用组件来封装一小段元素树
当 React 检查到具有 `type` 值为函数或类的元素时, 它会查询该组件, 并给出相应的 `props`。
比如, 当看到如下元素时：
```js
{
  type: Button,
  props: {
    color: 'blue',
    children: 'OK!'
  }
}
```
React 会询问 `Button` 应该如何渲染, `Button` 会返回如下元素：
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
React 会重复这个过程, 直到所有的元素都变为以 `type` 值为 DOM 标签的元素为止。
还记得上面 `Form` 的例子吗? 有了 React 之后, 它可以重写为如下代码[1]()todo：
```js
const Form = ({ isSubmitted, buttonText }) => {
  if (isSubmitted) {
    // 表单已提交, 返回提交成功的信息
    return {
      type: Message,
      props: {
        text: 'Success!'
      }
    };
  }

  // 表单还未提交, 返回 Button 组件
  return {
    type: Button,
    props: {
      children: buttonText,
      color: 'blue'
    }
  };
};
```
是不是简洁多了? 对于一个 React 组件而言, `props` 就是输入, 而输出是一段元素树。
**作为输出的元素树既可以包括描述 DOM 节点的元素, 也可以包括描述组件的元素。这种特性让你可以把 UI 划分为若干独立的部分, 而不用关心其内部的 DOM 结构。**
React 承担了创建、更新以及销毁实例的工作。我们用组件返回的元素树来描述实例应该是怎么样的, 而 React 代替我们承担实例的管理工作。

# 类组件（class component）和函数组件（function component）
在上面的代码中, `Form`、`Message` 以及 `Button` 都是 React 组件。它们既可以使用和上面的代码中一样的函数方式进行定义, 也可以作为 `React.Component` 的子类进行定义。以下三种声明方式是等价的：
```js
// 1) 函数声明式定义
const Button = ({ children, color }) => ({
  type: 'button',
  props: {
    className: 'button button-' + color,
    children: {
      type: 'b',
      props: {
        children: children
      }
    }
  }
});

// 2) 使用 React.createClass() 工厂函数
const Button = React.createClass({
  render() {
    const { children, color } = this.props;
    return {
      type: 'button',
      props: {
        className: 'button button-' + color,
        children: {
          type: 'b',
          props: {
            children: children
          }
        }
      }
    };
  }
});

// 3) 使用 ES6 语法, 作为 React.Component 的子类定义
class Button extends React.Component {
  render() {
    const { children, color } = this.props;
    return {
      type: 'button',
      props: {
        className: 'button button-' + color,
        children: {
          type: 'b',
          props: {
            children: children
          }
        }
      }
    };
  }
}
```
当一个组件使用定义为类时, 与函数组件相比, 它的功能更加强大。它可以储存一些局部状态, 并且在实例创建或销毁对应的 DOM 节点时执行一些自定义逻辑。
函数组件功能较少, 但是更加简洁。函数组件可以看做只有 `render()` 方法的类组件。除非你需要一些只能在类组件中才能使用的特性, 否则我们推荐你使用函数组件[2]()todo。
**不管是函数组件还是类组件, 对于 React 来说, 它们本质上都是组件。组件将 props 作为输入, 将对应元素作为输出。**

# 自上而下的调度（Top-Down Reconciliation）
当你运行如下代码时：
```js
ReactDOM.render({
  type: Form,
  props: {
    isSubmitted: false,
    buttonText: 'OK!'
  }
}, document.getElementById('root'));
```
React 会询问 `Form` 组件在给定 props 的条件下应该返回什么元素树。之后 React 会通过递进的方式, 逐步完善自己对于整个元素树的了解：
```js
// React: You told me this...
{
  type: Form,
  props: {
    isSubmitted: false,
    buttonText: 'OK!'
  }
}

// React: ...And Form told me this...
{
  type: Button,
  props: {
    children: 'OK!',
    color: 'blue'
  }
}

// React: ...and Button told me this! I guess I'm done.
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
这个过程 React 称之为 [`调度 / 协调（reconciliation）`](https://react.docschina.org/docs/reconciliation.html)。每次当你使用 `ReactDOM.render()` 或 `setState()` 时这个过程就会发生。在调度的最后阶段, React 会获悉整个 DOM 结构, 然后使用渲染器（renderer） ————比如`react-dom` 或 `react-native` ————最小化地更新 DOM 节点。
这个过程的递进式特点也是 React 易于优化的原因。如果元素树中的某个部分变得过于庞大, 以至于 React 不能十分高效地进行遍历, 那么你可以告诉 React [如果这部分相关的 props 并没有变化的话, 就跳过这一部分转而比较其他部分](https://react.docschina.org/docs/optimizing-performance.html)。如果这部分 props 是不可变对象的话, 则可以很快判断它有没有发生变化, 所以 React 和不可变性可以很好地结合在一起, 以最小的代价提供最大程度的优化（关于这部分内容可以参见：[Immutable 详解及 React中实践](https://zhuanlan.zhihu.com/p/20295971?columnSlug=purerender)）。
也许你已经注意到了, 整篇文章花费大量篇幅对元素和组件进行讨论, 但是对于实例却着墨甚少。事实上, 与其他面向对象的 UI 框架相比, 实例在 React 中的地位并不高。
只有当组件是类组件时才具有实例, 并且你永远不需要自己手动创建实例：React 对代替你完成这个工作。虽然[父组件实例能够通过一些机制获取到子组件的实例](https://react.docschina.org/docs/refs-and-the-dom.html), 但是这种方法往往只用于完成特殊功能（比如聚焦特定区域）, 通常情况下应该避免使用。
React 负责为每个类组件创建实例, 因此你可以使用方法和局部状态, 以面向对象的方式编写组件。但是除此之外, 实例在 React 的编程模型中并不是很重要, 且实例的管理都是由 React 自己完成的。

# 结语
**元素（element）**是一个用于描述你想在屏幕上显示什么 DOM 节点或组件的普通对象。元素可以在 props 中包含其他元素。创建一个元素的代价十分低廉, 而一旦一个元素被创建, 这个对象便是不可变的。
**组件（component）**可以通过多种方式进行定义。它可以定义为一种具有 `render()` 方法的类, 也可以简单地定义为一个函数。无论哪一种定义方式, 都会把 props 作为输入, 返回一个元素树作为输出。
如果一个组件接受到了一些 props 作为输入, 那是由于某个特定的父组件返回了带有其 `type` 和这些 props 的元素树。这就是为何人们总说在 React 中数据的流向是单向的：从父组件到子组件。
**实例（instance）**是你在编写类组件时使用 `this` 来指代的对象。它对于[存储局部状态以及相应生命周期事件](https://react.docschina.org/docs/component-api.html)十分有用。
函数组件并没有实例。类组件虽然具有实例, 但并不需要你自己进行管理, React 会负责所有的实例管理工作。
最后, 如果你需要创建元素, 请使用[React.createElement()](https://react.docschina.org/docs/react-api.html)、[JSX](https://react.docschina.org/docs/jsx-in-depth.html)和[元素工厂函数](https://react.docschina.org/docs/react-api.html)。不要在代码中把元素直接写作一般对象, 只需要知道元素的本质是对象即可。

# 进阶阅读
* [React Elements 的简介](https://react.docschina.org/blog/2014/10/14/introducing-react-elements.html)
* [React Elements 的优化](https://react.docschina.org/blog/2015/02/24/streamlining-react-elements.html)
* [React Virtual DOM 术语表](https://react.docschina.org/docs/glossary.html)

# 注释
1. 出于[一些安全上的考虑](https://github.com/facebook/react/pull/4832), 所有的 React 元素都应该添加一个额外的字段：`$$typeof: Symbol.for('react.element')`。在上文的代码中, 这个属性被省略了。整篇博客中都使用了省略该属性的对象, 以便于你建立起 React 元素的概念, 明白 React 中元素的行为方式。但是这些代码并不能真正运行, 除非你在对象中加上 `$$typeof` 属性或者使用 `React.createElement()` 或 JSX 来创建元素。
2. 本文写于 2015 年, 当时 Hooks 特性还并未实现。

# 笔者说
看到这里你也应该明白了, 上文中大书特书的元素（Elements）就是 React 中的**虚拟 DOM（Virtual DOM）**, 而所谓的元素树（Element Tree）对应的就是就是虚拟 DOM 的树形结构。由于元素本身是对象, 相对于 DOM 节点来说十分轻量, 比较起来也很方便, 因此 React 的更新策略便是优先比较虚拟 DOM, 只有虚拟 DOM 发生了变化, 才会更新 DOM 节点。
如何在 React 中使用**不可变对象（immutable object）**来提高性能呢? 可以在 shouleUpdate 中比较 props 和 state, 如果没有发生变化, 就不更新, 可以提升性能。但这要求组件必须是函数式的组件（注意不是函数组件, 函数式组件要求对于相同的输入总会有相同的输出）。

# 原文链接
[React Components, Elements, and Instances](https://react.docschina.org/blog/2015/12/18/react-components-elements-and-instances.html)