---
title: React 15 中的栈调度器
date: 2019-12-15 16:27:23
tags: [ 翻译计划, react ]
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
function isClass(type) {
  // React.Component 的子类具有此标志
  return (
    Boolean(type.prototype) &&
    Boolean(type.prototype.isReactComponent)
  );
}

// 此函数接收一个React元素 (例如<App />) , 返回表示需要挂载的 DOM 树或 Native 节点树。
function mount(element) {
  var type = element.type;
  var props = element.props;

  // 根据元素的 type 判断应该如何获取渲染后的元素：要么直接调用 type 对应的函数并把 props 传进去; 要么创建组件实例, 并调用 render()
  var renderedElement;
  if (isClass(type)) {
    // 类组件
    var publicInstance = new type(props);
    // 设置 props
    publicInstance.props = props;
    // 调用生命周期函数
    if (publicInstance.componentWillMount) {
      publicInstance.componentWillMount();
    }
    // 通过调用 render() 获取渲染的元素
    renderedElement = publicInstance.render();
  } else {
    // 函数组件
    renderedElement = type(props);
  }

  // 此过程是递归的，因为组件可能返回具有其他组件类型的元素
  return mount(renderedElement);

  // Note: 这段代码并不完整且会无限递归！
  // 这段代码只处理像 <App /> 和 <Button />一样的组件元素, 并不会处理像 <div /> 和 <p /> 这样的元素
}

var rootEl = document.getElementById('root');
var node = mount(<App />);
rootEl.appendChild(node);
```

> 这段伪代码只是为了说明思路, 它真正的实现并不相似。由于没有设置停止递归的条件, 这段代码还会造成堆栈溢出。

让我们回顾一下上面实例中的一些关键思想：
* React 元素是表示元素类型 (例如 App) 以及 props 的普通对象。
* 用户定义的组件 (例如 App) 可以是类或函数, 但它们都被渲染为元素。
* `挂载`是一个递归过程, 它通过给定的顶级 React 元素 (例如 <App />) 创建 DOM 或 Native 节点树。

# 挂载宿主元素 (host elements) 
宿主元素就是由不同平台自定义的元素, 比如 DOM 中的 <div />。对于宿主元素来说, 用户不需要编写其定义。
除了用户定义的复合组件之外, React 元素还可能代表平台相关的组件。例如, Button 的 render() 或许会返回一个 <div />。
如果元素的 type 属性是一个字符串, 那么 React 会将其作为与宿主元素进行处理。
```js
console.log(<div />);
// { type: 'div', props: {} }
```
当 reconciler 处理到宿主元素时, 它会使特定渲染器负责进行挂载。例如 React DOM 将创建一个 DOM 节点。
如果宿主元素包含子元素, 那么 reconciler 将会使用和上面一样的算法递归地进行挂载。子元素可以包含宿主元素 (比如 <div><hr /></div>) 或复合组件 (如 <div><Button /></div>) , 也可以同时包含两者。
子组件产生的 DOM 节点将被添加到父 DOM 节点中, 然后将递归地组装完整的 DOM 结构。

> reconciler 与 DOM 平台并不绑定。挂载的结果取决于使用的渲染器。

我们将上面的代码扩展一下, 以便其支持宿主元素：
```js
function isClass(type) {
  // React.Component 的子类拥有此标志
  return (
    Boolean(type.prototype) &&
    Boolean(type.prototype.isReactComponent)
  );
}

// 该函数只处理组件, 不处理宿主元素
// 例如<App /> 或 <Button />, 但不处理 <div />
function mountComposite(element) {
  var type = element.type;
  var props = element.props;

  var renderedElement;
  if (isClass(type)) {
    // 类组件
    var publicInstance = new type(props);
    // 设置 props
    publicInstance.props = props;
    // 调用生命周期函数
    if (publicInstance.componentWillMount) {
      publicInstance.componentWillMount();
    }
    renderedElement = publicInstance.render();
  } else if (typeof type === 'function') {
    // 函数组件
    renderedElement = type(props);
  }

  // 当元素是宿主元素 (如 <div />) 时, 终止递归
  return mount(renderedElement);
}

// 该函数只处理宿主元素, 不处理组件
// 例如 <div /> , 但不处理 <App /> 或 <Button />
function mountHost(element) {
  var type = element.type;
  var props = element.props;
  var children = props.children || [];
  if (!Array.isArray(children)) {
    children = [children];
  }
  children = children.filter(Boolean);

  // 下面这段代码不应该放在 reconciler 中
  // 不同渲染器初始化节点的方式也不同
  var node = document.createElement(type);
  Object.keys(props).forEach(propName => {
    if (propName !== 'children') {
      node.setAttribute(propName, props[propName]);
    }
  });

  // 挂载子元素
  children.forEach(childElement => {
    // 子元素既可以是宿主元素 (如 <div />) , 也可以是组件 (如：<Button />) 
    // 我们将递归地挂载它们
    var childNode = mount(childElement);

    // 这段代码也是由渲染器指定的, 不同渲染器有不同的添加子元素的方法
    node.appendChild(childNode);
  });

  // 返回 DOM 节点作为挂载的结果
  // 递归过程在此处终止
  return node;
}

function mount(element) {
  var type = element.type;
  if (typeof type === 'function') {
    // 用户自定义的组件
    return mountComposite(element);
  } else if (typeof type === 'string') {
    // 平台相关的组件 (宿主元素) 
    return mountHost(element);
  }
}

var rootEl = document.getElementById('root');
var node = mount(<App />);
rootEl.appendChild(node);
```
现在这段代码可以正常工作了, 但是离实现真正的 reconciler 依然很遥远。缺失的主要部分在于如何支持更新。

# 内部实例简介
React 的一个特性便是你可以重新渲染任何东西, 并且它不会重新创建 DOM 节点或重新设置 state。
```js
ReactDOM.render(<App />, rootEl);
// 应当复用已存在的 DOM 节点
ReactDOM.render(<App />, rootEl);
```
但是, 我们之前的实现只知道能初次挂载节点树。它无法执行更新, 由于它没有保存必要的信息, 比如所有的 `publicInstance`, 或者哪个 DOM 节点对应哪个组件。
stack reconciler 通过将 `mount()` 函数作为一个方法放入某个类中来解决这个问题。这种方法有缺陷, 目前正在进行重写。不过这就是目前的工作方式。
不同于分离的 `mountHost` 和 `mountComposite` 函数, 我们将创建两个类：`DOMComponent` 以及 `CompositeComponent`。
两个类都有接收元素作为参数的构造函数, 以及一个返回已挂载的节点的 `mount()`方法。我们将把上面代码中的 `mount()` 替换为一个用于实例化某个的工厂函数。
```js
function instantiateComponent(element) {
  var type = element.type;
  if (typeof type === 'function') {
    // 用户自定义组件
    return new CompositeComponent(element);
  } else if (typeof type === 'string') {
    // 平台相关的组件
    return new DOMComponent(element);
  }  
}
```
首先来实现 `CompositeComponent`：
```js
class CompositeComponent {
  constructor(element) {
    this.currentElement = element;
    this.renderedComponent = null;
    this.publicInstance = null;
  }

  getPublicInstance() {
    // 对于复合组件, 使用此方法可以获取组件实例
    return this.publicInstance;
  }

  mount() {
    var element = this.currentElement;
    var type = element.type;
    var props = element.props;

    var publicInstance;
    var renderedElement;
    if (isClass(type)) {
      // 类组件
      publicInstance = new type(props);
      // 设置 props
      publicInstance.props = props;
      // 调用生命周期函数
      if (publicInstance.componentWillMount) {
        publicInstance.componentWillMount();
      }
      // 返回元素树
      renderedElement = publicInstance.render();
    } else if (typeof type === 'function') {
      // 函数组件
      publicInstance = null;
      // 返回元素树
      renderedElement = type(props);
    }

    // 保存当前组件对应的实例
    this.publicInstance = publicInstance;

    // 根据元素树实例化子元素
    // 对于 <div /> 或 <p /> 这样的元素来说, 将返回一个 DOMComponent 实例
    // 对于 <App /> 或 <Button /> 这样的元素来说, 将返回一个 CompositeComponent 实例：
    var renderedComponent = instantiateComponent(renderedElement);
    this.renderedComponent = renderedComponent;

    // 对组件的输出进行挂载
    return renderedComponent.mount();
  }
}
```
值得注意的是, `CompositeComponent` 的实例并不是用户自定义的组件的实例。`CompositeComponent` 属于 reconciler 的实现细节, 并不会向用户公开。用户定义的类是从 `element.type` 中读出的, `CompositeComponent` 会为其创建一个实例, 并放入 `publicInstance` 属性中保存。
为了避免混淆, 我们把 `CompositeComponent` 和 `DOMComponent` 称为 `内部实例`。由于它们的存在, 使得我们可以将一些需要长期保存的数据与它们相关联, 只有渲染器和 reconciler 知晓内部实例的存在。
相反, 我们把用户自定义类的实例称为`公共实例`。公共实例指代的就是自定义类中的 `render()` 以及其他方法中的 `this`。
我们将 `mountHost` 函数进行重构, 得到一个有 `mount()` 方法的 `DOMComponent`类：
```js
class DOMComponent {
  constructor(element) {
    this.currentElement = element;
    this.renderedChildren = [];
    this.node = null;
  }

  getPublicInstance() {
    // 返回 DOM 节点
    return this.node;
  }

  mount() {
    var element = this.currentElement;
    var type = element.type;
    var props = element.props;
    var children = props.children || [];
    if (!Array.isArray(children)) {
      children = [children];
    }

    // 创建并保存节点
    var node = document.createElement(type);
    this.node = node;

    // 设置属性
    Object.keys(props).forEach(propName => {
      if (propName !== 'children') {
        node.setAttribute(propName, props[propName]);
      }
    });
    // 创建并保存所有子代
    // 每个子代既可能是一个 DOMComponent, 也可能是一个 CompositeComponent
    // 取决于 element.type 是一个字符串还是函数
    var renderedChildren = children.map(instantiateComponent);
    this.renderedChildren = renderedChildren;

    // 收集它们在挂载时返回的 DOM 节点
    var childNodes = renderedChildren.map(child => child.mount());
    // 将子 DOM 节点添加到 DOM 结构中
    childNodes.forEach(childNode => node.appendChild(childNode));

    // 返回 DOM 节点作为挂载的最终结果
    return node;
  }
}
```
`mountHost()` 重构前后最大的区别便是我们现在将 `this.node` 和 `this.renderedChildren` 与内部 DOMComponent 实例联系在一起。这些属性将在之后用于进行非损毁性更新。
以上代码的最终结果, 每个内部实例, 无论是复合组件还是宿主元素, 都保存有其子内部实例。如果一个函数组件 <App> 渲染后的结果是一个 <Button> 类组件, <Button> 类组件的渲染结果是一个 <div>, 那么它们的内部实例树将是这个样子：
```js
[object CompositeComponent] {
  currentElement: <App />,
  publicInstance: null,
  renderedComponent: [object CompositeComponent] {
    currentElement: <Button />,
    publicInstance: [object Button],
    renderedComponent: [object DOMComponent] {
      currentElement: <div />,
      node: [object HTMLDivElement],
      renderedChildren: []
    }
  }
}
```
在 DOM 中你将只能看到 <div>。但是内部实例树中同时包括了组件元素和宿主元素。
对于复合组件的内部实例来说, 需要保存：
* 当前元素
* 如果当前元素是类组件, 则需要保存公共实例
* 下个内部实例, 可以是 DOMComponent, 也可以是 CompositeComponent。

对于宿主元素的内部实例来说, 需要保存：
* 当前元素
* 当前元素对应的DOM节点
* 所有子元素的内部实例。每个内部实例可以是 DOMComponent, 也可以是 CompositeComponent。

如果你还在纠结于内部实例树的结构在复杂应用中到底是怎么样的, [React DevTools](https://github.com/facebook/react-devtools) 能为你提供一个近似的示例。它使用灰色标记出宿主元素实例, 用紫色标记出复合组件实例。

![](/images/react-stack-reconcilers/01.png))

最后, 我们将引入一个能够将一个完整的树添加到某个容器节点中的函数, 就像 `ReactDOM.render()` 所做的那样。同样, 该函数也返回一个公共实例。
```js
function mountTree(element, containerNode) {
  // 创建顶级内部实例
  var rootComponent = instantiateComponent(element);

  // 将顶级内部实例挂载到容器中
  var node = rootComponent.mount();
  containerNode.appendChild(node);

  // 返回顶级内部实例提供的公共实例
  var publicInstance = rootComponent.getPublicInstance();
  return publicInstance;
}

var rootEl = document.getElementById('root');
mountTree(<App />, rootEl);
```

# 卸载
现在我们有了保存有其子级和 DOM 节点的内部实例, 我们可以开始实现卸载卸载功能了。对于复合组件, 卸载对调用一个生命周期方法并向下递归：
```js
class CompositeComponent {

  // ...

  unmount() {
    // 调用生命周期方法
    var publicInstance = this.publicInstance;
    if (publicInstance) {
      if (publicInstance.componentWillUnmount) {
        publicInstance.componentWillUnmount();
      }
    }

    // 卸载单个渲染后的组件
    var renderedComponent = this.renderedComponent;
    renderedComponent.unmount();
  }
}
```
对于 DOMComponent, 卸载会通知每个子元素执行卸载功能：
```js
class DOMComponent {

  // ...

  unmount() {
    // 卸载所有子元素
    var renderedChildren = this.renderedChildren;
    renderedChildren.forEach(child => child.unmount());
  }
}
```
在实践中, 卸载 DOMComponent 时还会移除一些事件监听器, 并清空一些缓存。不过我们这里先跳过这些细节。
现在我们可以添加一个新的名为 `unmountTree(containerNode)` 顶级函数, 这个函数与 `ReactDOM.unmountComponentAtNode()` 类似。
```js
function unmountTree(containerNode) {
  // 从 DOM 节点读取内部实例
  // 目前该功能还不能实现, 我们还需要更改 mountTree() 来在 DOM 节点上保存内部实例
  var node = containerNode.firstChild;
  var rootComponent = node._internalInstance;

  // 卸载树并清空容器
  rootComponent.unmount();
  containerNode.innerHTML = '';
}
```
为了实现上述功能, 我们需要对 `mountTree()` 进行修改, 将 `_internalInstance` 属性添加到根 DOM 节点。为了能够多次调用 `mountTree()`, 我们还需要让其能够销毁已存在的树。
```js
function mountTree(element, containerNode) {
  // 销毁所有已存在的树
  if (containerNode.firstChild) {
    unmountTree(containerNode);
  }

  // 创建顶级内部实例
  var rootComponent = instantiateComponent(element);

  // 将顶级内部实例添加到容器中
  var node = rootComponent.mount();
  containerNode.appendChild(node);

  // 保存对内部实例的引用
  node._internalInstance = rootComponent;

  // 返回内部实例提供的公共实例
  var publicInstance = rootComponent.getPublicInstance();
  return publicInstance;
}
```
现在, 运行 `unmountTree()` 或重复运行 `mountTree()`, 将会删除旧树并调用组件上的 `componentWillUnmount()` 生命周期方法。

# 更新
在先前的部分中, 我们实现了卸载功能。但是每次 props 变化都卸载和挂载整个树会让 React 的效率十分低下。reconciler 的目的是尽可能复用已有的实例, 以保存 DOM 节点和状态。
```js
var rootEl = document.getElementById('root');

mountTree(<App />, rootEl);
// 应当复用已存在的 DOM
mountTree(<App />, rootEl);
```
我们将为 `DOMComponent` 和 `CompositeComponent` 添加一个名为 `receive(nextElement)` 的新方法。
```js
class CompositeComponent {
  // ...

  receive(nextElement) {
    // ...
  }
}

class DOMComponent {
  // ...

  receive(nextElement) {
    // ...
  }
}
```
这个方法的工作是无论任何时候都尽量保持组件 (以及其所有子代) 都尽可能与 `nextElement` 提供的描述保持一致。
这部分又被称为 `virtual DOM diffing`, 尽管我们真正做的是递归地遍历内部实例树, 并让每个内部实例接收更新。

# 更新复合组件
当一个复合组件受到一个新元素时, 我们会调用 `componentWillUpdate()` 生命周期方法。
之后我们用新的 props 重新渲染组件, 并获取新的渲染后的元素：
```js
class CompositeComponent {

  // ...

  receive(nextElement) {
    var prevProps = this.currentElement.props;
    var publicInstance = this.publicInstance;
    var prevRenderedComponent = this.renderedComponent;
    var prevRenderedElement = prevRenderedComponent.currentElement;

    // 更新自身对应的元素
    this.currentElement = nextElement;
    var type = nextElement.type;
    var nextProps = nextElement.props;

    // 找出 nextElement render() 的输出是什么, 并更新 publicInstance
    var nextRenderedElement;
    if (isClass(type)) {
      // 类组件
      // 调用生命周期方法
      if (publicInstance.componentWillUpdate) {
        publicInstance.componentWillUpdate(nextProps);
      }
      // 更新 props
      publicInstance.props = nextProps;
      // 重渲染
      nextRenderedElement = publicInstance.render();
    } else if (typeof type === 'function') {
      // 函数组件
      nextRenderedElement = type(nextProps);
    }

    // ...
```
接下来我们会查看渲染后元素的 `type`。如果 `type` 与上次渲染结果相比未发生变化, 则内部实例可以直接更新。
例如, 如果第一次返回的结果是 `<Button color="red" />`, 而第二次返回的结果是 `<Button color="blue" />`, 那么我们可以通过把第二次返回的结果传入 `receive()` 来更新内部实例。
```js
    // ...

    // 如果渲染后的元素 type 并未改变,
    // 复用已存在的组件实例并退出当前函数
    if (prevRenderedElement.type === nextRenderedElement.type) {
      prevRenderedComponent.receive(nextRenderedElement);
      return;
    }

    // ...
```
然而, 如果下次渲染后的元素与之前的元素有不同的 type, 那么我们就不能只更新内部实例了。一个 `<button>` 是不可能变成一个 `<input>` 的。
我们需要卸载当前已有的内部实例, 然后挂载一个与新的渲染后的元素类型对应的内部实例。
```js
    // ...

    // 如果能执行到这里, 我们需要卸载之前挂载的组件, 挂载一个新的, 并交换二者的节点

    // 找到旧节点以便替换
    var prevNode = prevRenderedComponent.getHostNode();

    // 卸载旧的子代, 挂载一个新的
    prevRenderedComponent.unmount();
    var nextRenderedComponent = instantiateComponent(nextRenderedElement);
    var nextNode = nextRenderedComponent.mount();

    // 替换对子项的引用
    this.renderedComponent = nextRenderedComponent;

    // 用新节点替换旧节点
    // Note: 这是渲染器特定的代码，
    // 理想情况下应位于CompositeComponent之外：
    prevNode.parentNode.replaceChild(nextNode, prevNode);
  }
}
```
综上所述, 当一个复合组件受到一个新元素时, 要么它将更新委托给其渲染后的内部实例, 要么将其卸载并在原位置挂载新的实例.
还有一种情况, 组件将会重新挂载而不是接收元素, 那就是元素的 `key` 的更改。由于太过复杂, 在这里我们将不讨论这种情况。
请注意, 我们需要向内部实例添加一个新的方法 `getHostNode()`, 以便在更新期间能够找到特定于平台的节点并替换该节点。对于两个类, 其实现都很简单:
```js
class CompositeComponent {
  // ...

  getHostNode() {
    // 要求 renderedComponent 提供它, 最终会找到 DOMComponent 的节点
    return this.renderedComponent.getHostNode();
  }
}

class DOMComponent {
  // ...

  getHostNode() {
    return this.node;
  }  
}
```

# 更新宿主元素
当 DOMComponent 收到一个元素时, 它们需要更新平台特定的视图。对 React DOM 而言, 这意味需要更新 DOM 属性:
```js
class DOMComponent {
  // ...

  receive(nextElement) {
    var node = this.node;
    var prevElement = this.currentElement;
    var prevProps = prevElement.props;
    var nextProps = nextElement.props;    
    this.currentElement = nextElement;

    // 移除旧属性
    Object.keys(prevProps).forEach(propName => {
      if (propName !== 'children' && !nextProps.hasOwnProperty(propName)) {
        node.removeAttribute(propName);
      }
    });
    // 设置新属性
    Object.keys(nextProps).forEach(propName => {
      if (propName !== 'children') {
        node.setAttribute(propName, nextProps[propName]);
      }
    });

    // ...
```
接下来将更新其子代。与复合组件不同, DOM 节点可能包含多个子代。
在这个简化的示例中, 我们使用内部实例的数组保存其子代, 并对其进行遍历。根据收到的示例的 type 是否匹配先前实例的 type 来更新或替换内部实例。实际上, reconciler 还考虑了元素上 key 属性的存在, 并对插入和删除操作进行跟踪, 但我们将省略此逻辑。
我们在列表中收集子项上的 DOM 操作, 以便可以批量执行它们:
```js
    // ...

    // 由 React 元素组成的数组
    var prevChildren = prevProps.children || [];
    if (!Array.isArray(prevChildren)) {
      prevChildren = [prevChildren];
    }
    var nextChildren = nextProps.children || [];
    if (!Array.isArray(nextChildren)) {
      nextChildren = [nextChildren];
    }

    // 这里是由内部实例组成的数组:
    var prevRenderedChildren = this.renderedChildren;
    var nextRenderedChildren = [];
    // 在遍历子级时, 我们将需要在子级上进行的操作放入数组中
    var operationQueue = [];

    // 注意: 以下内容为真实情况的简化版本! 仅用于说明思路!
    // 它没有处理重排序, 以及具有 key 的子级
    for (var i = 0; i < nextChildren.length; i++) {
      // 获取该子级现有的内部实例
      var prevChild = prevRenderedChildren[i];

      // 如果当前子级没有内部实例, 则将新的子级添加到尾部, 并创建一个新的内部实例并挂载
      if (!prevChild) {
        var nextChild = instantiateComponent(nextChildren[i]);
        var node = nextChild.mount();

        // 记录下需要执行的操作: 添加一个节点
        operationQueue.push({type: 'ADD', node});
        nextRenderedChildren.push(nextChild);
        continue;
      }

      // 仅当实例的 type 属性相同时才能更新实例
      var canUpdate = prevChildren[i].type === nextChildren[i].type;

      // 如果我们无法更新现有实例, 那么我们需要把已有的实例卸载, 然后创建一个新的实例来代替它
      if (!canUpdate) {
        var prevNode = prevChild.getHostNode();
        prevChild.unmount();

        var nextChild = instantiateComponent(nextChildren[i]);
        var nextNode = nextChild.mount();

        // 记录下我们需要执行的操作: 交换节点
        operationQueue.push({type: 'REPLACE', prevNode, nextNode});
        nextRenderedChildren.push(nextChild);
        continue;
      }

      // 如果我们可以更新当前内部实例, 那么我们只需让其接收下一个元素, 该内部实例会自己进行更新
      prevChild.receive(nextChildren[i]);
      nextRenderedChildren.push(prevChild);
    }

    // 卸载所有不应该存在的子代
    for (var j = nextChildren.length; j < prevChildren.length; j++) {
      var prevChild = prevRenderedChildren[j];
      var node = prevChild.getHostNode();
      prevChild.unmount();

      // 记录下我们需要执行的操作: 移除节点
      operationQueue.push({type: 'REMOVE', node});
    }

    // 将子代列表指向更新后的版本
    this.renderedChildren = nextRenderedChildren;

    // ...
```
最后, 我们将执行 DOM 操作。真正的 reconciler 代码会更复杂, 因为其还会处理节点移动:
```js
    // ...

    // 处理操作队列
    while (operationQueue.length > 0) {
      var operation = operationQueue.shift();
      switch (operation.type) {
      case 'ADD':
        this.node.appendChild(operation.node);
        break;
      case 'REPLACE':
        this.node.replaceChild(operation.nextNode, operation.prevNode);
        break;
      case 'REMOVE':
        this.node.removeChild(operation.node);
        break;
      }
    }
  }
}
```
以上便是宿主组件更新的全部过程。

# 顶级更新
到这里, 我们已经分别在 `CompositeComponent` 和 `DOMComponent` 上实现了 `receive(nextElement)` 方法, 我们可以更改顶级更新方法 `mountTree()` 函数, 让其在元素 `type` 一致时可以使用。

```js
function mountTree(element, containerNode) {
  // 检查是否已经有存在的树
  if (containerNode.firstChild) {
    var prevNode = containerNode.firstChild;
    var prevRootComponent = prevNode._internalInstance;
    var prevElement = prevRootComponent.currentElement;

    // 尽可能复用已存在的 root 组件
    if (prevElement.type === element.type) {
      prevRootComponent.receive(element);
      return;
    }

    // 否则的话, 卸载已存在的树
    unmountTree(containerNode);
  }

  // ...

}
```
现在调用 `mountTree()` 更新相同类型时不会造成不必要的破坏。
```js
var rootEl = document.getElementById('root');

mountTree(<App />, rootEl);
// 复用已存在的 DOM 节点
mountTree(<App />, rootEl);
```
以上就是 React 内部是如何工作的。

# 未实现的功能
该文档是源码的简化版本, 还有几个很重要的方面是我们没有兼顾到的:

* 组件应当能够渲染 `null`, 而 `reconciler` 应当可以处理 " empty slots" 的情况。
* reconciler 会从元素中读取 key 属性, 并据此把内部实例和元素对应起来。一系列 React 特性的实现都与此有关。
* 除了复合组件和宿主类组件外, 还应该有两个类: `text` 类以及 `empyt` 类。它们分别对应文字节点以及渲染 `null` 带来的空组件。
* 渲染器通过外部注入的方法告知 reconciler 当前的宿主环境。
* 子列表的更新逻辑被提取到一个称为 `ReactMultiChild` 的类中, 供 React DOM 和 React Native 使用。
* reconciler 还实现了对 `setState()` 的支持, 在事件处理器内部, 多个更新被合并为一个更新。
* reconciler 还负责将 ref 附加到对应的复合组件以及宿主节点上。
* 在 DOM 准备就绪后调用的生命周期方法(比如 componentDidMount 以及 componentDidUpdate) 会被收集到 `callback queues` 中, 并在单个 batch 中执行。
* React 会将当前更新信息放入一个称为 `transaction` 的对象中。

# 进入源码中
略

# 未来可能实现的特性
Stack reconciler 有局限性, 比如不能中断任务执行, 不能为任务分派优先级等。新的 reconciler 称为 Fiber reconciler, 正在开发中。