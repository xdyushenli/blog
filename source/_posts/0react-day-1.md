---
title: react源码阅读日记（一）
date: 2019-09-16 17:05:30
tags: [ react ]
---
# 要着重关注的目录
* events: react的事件绑定系统
* react: react核心代码，定义节点及表现行为，不负责渲染或更新
* react-dom: react-dom核心代码，负责渲染和更新
* react-reconciler: react-dom重度依赖该模块(协调器)
* scheduler: 实现react异步渲染(调度表)
* shared: 公用代码

# jsx到js
jsx代码经过babel转义后，实际上调用的是`React.createElement`函数。
## `React.createElement`参数列表
* 第一个参数：标签名字符串 \ 组件变量名
* 第二个参数：该标签或组件上所有属性和属性值的对象
* 第三个参数及之后的参数：子元素列表

## `React.createElement`返回值
* ReactElement类型的对象

# ReactElement
React上挂载的关于ReactElement的API有四个，分别是
* createElement
* createFactory
* cloneElement
* isValidElement

## createElement内部流程
1. 将除内建设置（ref、key、__self、__source）外的设置放入props对象。
2. 将所有子元素放入props对象的children属性中。
3. 若有默认属性值，将默认属性值填充到props对象中。
4. 将type、key、ref、props等传入ReactElement函数中，并返回其执行结果。

## ReactElement内部流程
1. 接收参数，并将参数放入一个对象中。
2. 返回该对象。

# Component & PureComponent
Component和PureComponent都是挂载在React对象上的基类，常以此为基类构建新的类组件。

## Component和PureComponent的区别
Component类对应类组件。PureComponent对应纯函数组件。
二者的唯一区别在于，PureComponent实现了更为简便的shouldComponentUpdate，避免了在props对象不变的情况下进行不必要的更新。

## Component
### Component函数
#### Component函数参数列表
* props：元素的属性对象
* context：上下文
* updater：用于DOM更新的函数，由ReactDOM传入

#### Component函数函数流程
该函数是一个构造函数，所做的所有工作就是将参数挂载在this对象的同名属性上。
### Component.prototype
* isReactComponent：是一个空对象，在ReactDOM中判断更新类型。
* setState：调用updater的enqueueSetState函数，用于更新状态。
* forceUpdate：调用updater的enqueueForceUpdate函数，用于强制更新状态。

## PureComponent
### PureComponent函数
结构及作用和Component函数差不多。
### PureComponent.prototype
结构上与Componen.prototype类似。代码上，实现了PureComponent.prototype对Component.prototyp的继承e，同时通过将原型上`isPureReactComponent`属性标识为`true`的方法标记这种类型。

# createRef & ref
## ref简介
ref的主要作用是用于获取DOM节点或组件实例以进行进一步操作。主要有三种使用方式：
* ref = String
* ref = Function
* ref = React.createRef()

## createRef
调用React.createRef会创建一个对象，该对象的current属性指向DOM节点实例。

# forwardRef
ref获取的是DOM节点或组件实例，但是当组件是纯函数组件时，是没有实例的。此时使用ref会报错。
使用React.forwardRef包装纯函数组件后，允许函数组件接受第二个参数作为ref，并将其传递下去。
## forwardRef代码解析
forwardRef接受一个函数式组件作为参数，返回一个格式如下的对象：
```js
{
    $$typeof: REACT_FORWARD_REF_TYPE,
    render, // 函数式组件的定义
}
```

# Context
## Context简介
Context常在需要将某个属性从高层级组件传递到较深层级的组件中时使用。在上层组件中创建Context后，在其所有后代组件中都能访问到该Context，并实时更新，以此来实现跨层级通信。
## Context的使用方式
调用React.createContext函数会返回一个对象，其中有两个名为Provider和Consumer的内置组件。
其中使用Provider组件将需要向下传递信息的组件包裹起来。当需要提取信息时，使用Consumer组件即可。
```js
const { Provider, Consumer } = React.createContext();

// 父组件
<Provider value={this.state.value}>{this.props.children}</Provider>

// 子组件
<Consumer>{value => <p>{value}</p>}</Consumer>
```
## createContext源码解析
### createContext的参数
createContext接受两个参数，第一个参数是Context的默认值，第二个参数用于计算state变化的大小。
### createContext的返回值
调用该函数会返回一个context对象，对象上挂载有_currentValue、Provider、Consumer等属性。具体如下：
* $$typeof: REACT_CONTEXT_TYPE
* _currentValue：用于保存当前Context的值
* Provider：本质是一个对象，具有$$typeof属性和_context属性。其中$$typeof属性值为REACT_PROVIDER_TYPE，_context属性指向context对象。
* Consumer：指向context对象本身。

# ConcurrentMode
## ConcurrentMode简介
ConcurrentMode的作用是区分组件更新的优先级，允许中断更新和按照优先级不同更新组件。
关于其用法直接上代码。
```js
// 所有被ConcurrentMode组件包括的组件，其内部更新都是低优先级的
import { ConcurrentMode } from 'react';

<ConcurrentMode>{this.props.children}</ConcurrentMode>
```
## ConcurrentMode源码解析
令人惊奇的是，ConcurrentMode本身只是一个Symbol。其特殊更新机制的实现依赖于ReactDOM。

# Suspense & Lazy
## Suspense简介
Suspense是React的一种内置组件，常用于异步加载。
使用时，使用该组件包裹需要异步加载的组件。在异步组件内部需要throw一个Promise对象。
Suspense组件接受一个名为fallback的参数，作为异步加载未完成时的显示值。只有Suspense组件包裹的组件抛出的Promise全都resolve之后，才会显示组件，否则会一直显示fallback指定的值。
## Suspense源码解析
Suspense本身也只是一个Symbol。
## Lazy简介
Lazy也是React用于异步加载的一种手段，但相对来说比较繁琐，需要结合WebPack异步加载模块进行使用。用代码表示比较好一点。
```js
import { lazy } from 'react';

// 经webpack打包后，lazy.js会在另一个网络请求中加载
const LazyComp = lazy(() => import('./lazy.js'));
```
## Lazy源码解析
从上面的代码中我们可以得知，lazy函数接受一个函数作为参数，但这个函数的返回值必须是thenable对象（即类Promise对象）。
lazy函数的返回值是一个对象，包含有下列属性：
* $$typeof：REACT_LAZY_TYPE
* _ctor：调用时传入的函数
* _status：传入的函数的执行状态（resolve、pending、reject)
* _result：传入的函数的执行结果

# Hooks
## Hooks简介
Hooks为函数式组件提供了类似于Class组件的能力，比如state、生命周期函数等。
## Hooks源码解析
Hooks中所有use开头的API，返回的都是挂载在dispatcher对象上的同名函数的执行结果，而dispatcher对象是根据平台变化而变化的，因此不再赘述。

# Children
## Children简介
一般来说，在组件内部可以直接通过this.props.children来获取子组件并进行一系列操作。但由于子组件的内部数据结构是由React制定的，因此直接操作可能出现意料之外的问题。因此React提供了一系列用于操作children的API。列表如下：
* React.Children.map：接受两个参数，第一个参数为children数组，第二个参数为回调函数。该方法会在children数组的每个元素上调用回调函数，并返回所有调用结果组成的数组。
* React.Children.forEach：参数同map函数。该方法会在children数组的每个元素上调用回调函数，没有返回值。
* React.Children.count：接受children数组作为唯一参数。返回children中组件的总数量（注意：是组件的数量，不是真实DOM元素的数量）。
* React.Children.only：接受children数组作为唯一参数。验证children数组是否只有一个子节点，如果有则返回，否则抛出错误。
* React.Children.toArray：接受children数组作为唯一参数。按文档的说法，该方法会将children展开并返回，但我明没有看出有什么实际的作用。

## React.Children.map源码解析
在上述一系列方法中，最复杂的就map和forEach了，但二者除了返回值以外并没有什么不同。因此挑选map方法的源码进行解析。需要注意的是，传入map方法的回调函数，其返回值若为多维数组，则React会将其展开为一维数组后返回。
由于篇幅比较长，所以另写了一篇博文。详情见[React.Children.map源码解析](https://xdyushenli.github.io/2019/10/11/react-children-map/)

# Others
## memo
功能上与PureComponent类似，实现了在props不变的情况下不重新渲染，提高性能。
只适用于函数式组件，不适用与Class组件。
## Fragment
本质上是一个Symbol，常见的形态是`<></>`。
## StrictMode
本质上是一个Symbol。在该模式下，使用快要过期的API时会提示。
## cloneElement
用于克隆安全ReactElement。
## createFactory
可创建一个可用于快速创建节点的工厂函数。

# 注意
* React源码中使用了flow对变量类型进行限制，因此会有一些特殊语法
