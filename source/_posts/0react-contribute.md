---
title: React贡献手册
date: 2019-12-13 16:00:37
tags: [ React ]
---
# 如何参与
## 开发流程
### 先决条件
* `Node` V8.0.0+ 以及 `yarn` v1.2.0+
* 安装`gcc`或任意您熟悉的编译器。对于OS X系统, `XCode`命令行工具会自动安装。在Ubuntu中, 需要运行`apt-get install build-essential`以进行安装。Windows环境中也需要进行额外操作。具体细节可参见[`node-gyp`安装指南](https://github.com/nodejs/node-gyp#installation)
* 熟悉`git`
### 具体开发流程
将仓库克隆到本地后, 运行`yarn`以获取其依赖项。之后可运行如下命令：
* `yarn lint` 检查代码样式。
* `yarn linc` 与 `yarn lint` 基本一致, 但是只检查被修改的文件, 因此速度更快。
* `yarn test` 运行完整测试套件。
* `yarn test --watch` 运行可交互测试。
* `yarn test <pattern>` 测试文件名和模式匹配的文件。
* `yarn test-prod` 在生产环境中运行测试。
* `yarn debug-test` 与 `yarn test` 基本一致, 但带有一个debugger, 打开[`chrome://inspect`](chrome://inspect), 并按下`Inspect`。
* `yarn flow` 使用Flow进行类型检查。
* `yarn build` 创建`build`文件夹, 并将所有文件打包进去。
* `yarn build react/index,react-dom/index --type=UMD` 创建只包含`React`和`React DOM`的UMD模块。

建议在更改后运行`yarn test`或其任何变体, 以确保修改的内容不会重新暴露已修改的bug。
首先运行`yarn build`。这将在`build`文件夹中生成预构建的包, 并在`build/packages`目录下准备npm软件包。
想观察修改结果的最简单的方法是运行`yarn build react/index,react-dom/index --type=UMD`后打开`fixtures/packaging/babel-standalone/dev.html`。该文件引用了来自`build`目录下的`react.development.js`, 修改的结果会反映在该js文件中。
若想在其他项目中观察修改React代码的结果, 则需要复制`build/dist/react.development.js`, `build/dist/react-dom.development.js`或任何已修改并构建好的文件, 用于替换其他项目中对应文件的稳定版本。若你的项目中通过npm来使用react, 则需要删除其他项目中的`node_modules/react`和`node_modules/react-dom`文件夹, 并使用yarn link命令将其指向本地的`build`文件夹。
```
cd ~/path_to_your_react_clone/build/node_modules/react
yarn link

cd ~/path_to_your_react_clone/build/node_modules/react-dom
yarn link

cd /path/to/your/project
yarn link react react-dom
```
执行完上述命令后, 每次在React文件夹中运行`yarn build`命令时, 更新的结果都会反映在其他项目的`node_modules`中。
在提交请求之前, 需要包含对任何新功能的单元测试。这样可以确保我们以后不会破坏您提交的代码。


## 如何提交pull request?
在提交`pull request`之前, 请确保已完成如下操作：
1. 克隆[仓库](https://github.com/facebook/react)的`master`分支到本地。
2. 在仓库根目录中运行`yarn`。
3. 若修正了一些bug或添加了代码, 则应该针对这些bug和代码添加测试。
4. 确保所有代码通过测试（在开发环境中可以使用`yarn test --watch TestName`进行测试）。
5. 执行`yarn test-prod`命令以进行生产环境的测试。
6. 若开发中需要`debugger`, 运行以下命令`yarn debug-test --watch TestName`, 打开[`chrome://inspect`](chrome://inspect), 并按下`Inspect`。
7. 使用[`prettier`](https://github.com/prettier/prettier)格式化代码（`yarn prettier`）。
8. 确保代码样式一致（`yarn lint`命令会检查所有文件, `yarn linc`仅仅检查修改过的文件）。
9. 使用[`Flow`](https://flowtype.org/)执行类型检查（`yarn flow`）。
10. 确保完成[贡献者许可协议（CLA）](https://code.facebook.com/cla)。


# 源码概述
## 外部依赖
React几乎没有外部依赖。通常`require()`都会引用React源码中的文件, 当然也有个别例外。
依赖[`fbjs`](https://github.com/facebook/fbjs)仓库是因为React需要和一些类似于[`Relay`](https://github.com/facebook/relay)的库共享一些小功能, 并且与他们保持同步。`fbjs`中没有公共 API, 他们仅仅用于 Facebook 的项目，比如 React。我们不依赖 Node 生态系统中同等功能的小模块。

## 顶层目录
克隆React仓库后, 可以看到一些顶层目录：
* `packages`包含了元数据（比如package.json）和React仓库中所有项目的源码（子目录src）。**如果需要修改源码, 那么每个包的src子目录是最需要花费精力的地方。**
* `fixtures`包含一些给贡献者准备的小型 React 测试项目。
* `build`是React的输出目录。源码仓库中并不包含这个目录, 但是它会在克隆React并第一次构建后出现。
文档位于[React仓库之外的一个独立仓库中](https://github.com/reactjs/reactjs.org/tree/master/src)。
其他顶层目录基本都是工具类的, 在贡献代码时基本不会涉及。

## 测试
我们没有设置单元测试的顶级目录, 而是将它们放置在了所需测试文件的相同目录下的`__test__`的目录之中。
## warning 和 invariant
React使用`warning`模块来显示警告信息。
```js
var warning = require('warning');

warning(
  2 + 2 === 4,
  'Math is not working today.'
);
```
**警告信息会在 warning 的条件为 false 时出现。**
warning 仅在开发环境中启用。在生产环境中, 它们会被完全剔除。如果需要在生产环境中禁止执行某些代码, 使用 `invariant` 模块代替（该句原文如下：If you need to forbid some code path from executing, use `invariant` module instead:）。
```js
var invariant = require('invariant');

invariant(
  2 + 2 === 4,
  'You shall not pass!'
);
```
**当 invariant 判别条件为 false 时, 会将 invariant 中的信息作为错误抛出。**
保持开发环境和生产环境的行为相似是十分重要的, 因此 invariant 在开发环境中也会抛出错误。

## 开发环境与生产环境
你可以在代码库中使用 `__DEV__` 这个伪全局变量, 用于管理开发环境中需要运行的代码块。
```js
if (__DEV__) {
    // 仅在开发环境下执行的代码
}
```

## Flow
我们最近将 [`Flow`](https://flow.org/) 引入源码用于类型检查。
我们接受添加 Flow 注释到现有代码。 Flow 注释看上去像这样：
```js
ReactRef.detachRefs = function(
  instance: ReactInstance,
  element: ReactElement | string | number | null | false,
): void {
  // ...
}
```
请尽可能在新代码中使用 Flow 注释。可以通过运行 `yarn flow`, 用 Flow 检查你的本地代码。

## 动态注入
React 在一些模块中使用了动态注入。虽然它总是显式的, 但仍会在一定程度上妨碍对代码的理解。它存在的主要原因是 React 原本只是以支持 DOM 为目标的, 但在 React Native 开始作为 React 的一个分支之后, 我们只好添加一些动态注入让 React Native 支持不同平台。
你可能见过一些模块, 像下面这样声明动态依赖：
```js
// 动态注入
var textComponentClass = null;

// 依赖动态注入的值
function createInstanceForText(text) {
  return new textComponentClass(text);
}

var ReactHostComponent = {
  createInstanceForText,

  // 提供动态注入的入口
  injection: {
    injectTextComponentClass: function(componentClass) {
      textComponentClass = componentClass;
    },
  },
};

module.exports = ReactHostComponent;
```
`injection` 字段并没有进行特殊处理。但当某个模块有这个字段存在时, 通常意味着该模块在运行时需要注入一些（很可能是与平台相关的）依赖。

## Multiple Packages
React 采用 [`monorepo`](https://xdyushenli.github.io/2019/12/14/advantages-of-monorepos/) 的管理方式。仓库中包含多个独立的包, 以便于更改时可以一起联调, 并且限制问题的影响范围。

## React Core
`React Core` 中包含所有的全局 React API, 比如：
* React.createElement()
* React.Component
* React.Children
React Core 只包含定义组件必要的 API。它不包含 diff 算法或者依赖其他平台特定的代码。它同时适用于 React DOM 和 React Native 组件。
React Core 的代码在源码的 `packages/react` 目录中。在 npm 上发布为 react 包。相应的独立浏览器构建版本称为 `react.js`, 它会导出一个名为 `React` 的全局对象。

## 渲染器（Renderers）
React 最初只是服务于 DOM, 但之后加入了对原生平台的 React Native 的支持。因此, 在 React 内部机制中引入了 `渲染器` 这一概念。
**渲染器用于管理一棵 React 树, 使其根据底层平台进行不同的调用。**
渲染器同样位于 `packages` 目录下：
* `React DOM Renderer` 将 React 组件渲染为 DOM。它实现了全局` ReactDOM API`, 在 npm 上作为 react-dom 包独立发布。也可作为单独浏览器版本进行使用, 称为 `react-dom.js`, 导出一个 `ReactDOM` 的全局对象。
* `React Native Renderer` 将 React 组件渲染为 Native 视图。此渲染器在 React Native 内部使用。
* `React Test Renderer` 将 React 组件渲染为 JSON 树, 用于 Jest 的 快照测试。在 npm 上作为 react-test-renderer 包发布。
* `React Art` 使用 React 绘制矢量图形。原本是一个独立的 Github 仓库, 目前加入了主源代码树中。

## 调度器（Reconcilers）
即使 React DOM 和 React Native 渲染器的区别很大, 但其中一些逻辑也是共享的。特别是 diff 算法需要尽可能相似, 这样可以让声明式渲染、state、生命周期方法等特性保持跨平台的一致性。
为了解决这一问题, 不同的渲染器之间彼此共享一些代码, 这部分代码称为 `Reconciler`。当处理类似于 `setState()` 这样的更新时, ``Reconciler`` 会调用树中组件上的 `render()`, `render()`根据平台不同调用不同的渲染器, 然后决定是否进行挂载、更新或是卸载操作。
目前 `Reconciles` 没有单独的包, 因此暂时没有公共 API。但它们仍是独立于 React DOM 和 React Native 等渲染器的, 不属于其中的一部分。

### Stack Reconsiler
`Stack Reconciler` 是 React 15 及更早的解决方案, 目前已停止使用。

### Fiber Reconciler
`Fiber Reconciler` 是一次新的尝试, 致力于解决 `Stack Reconciler` 中固有的问题, 同时解决一些历史遗留问题。`Fiber Reconciler` 从 React 16 开始变成了默认的 Reconciler。
它的主要目标是：
* 能够把可中断的任务切片处理。
* 能够调整优先级, 重置并复用任务。
* 能够支持在父元素和子元素之间交错处理, 以支持 React 中的布局。
* 能够在 `render()` 中返回多个元素。
* 更好地支持错误边界。
能够在[这里](https://github.com/acdlite/react-fiber-architecture)和[这里](https://medium.com/react-in-depth/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react-e1c04700ef6e), 深入了解 React Fiber 架构。虽然这已经在 React 16 中启用了, 但是 async 特性还没有默认开启。
源代码目录位于 `packages/react-reconciler` 目录下。

## 事件系统
React 实现一个合成事件, 这与渲染器无关, 换句话说就是和平台无关, 同时适用于 React DOM 和 React Native。源码在 `packages/react-events` 目录下。
这是一个[深入研究事件系统代码的视频（66min）](https://www.youtube.com/watch?v=dRo_egw7tBc)。

# 实现说明
这部分是一个对于如何实现 Stack Reconciler 的总结。
这部分比较晦涩难懂, 并且要求对 React 公共 API 以及核心代码、渲染器和 Reconcilers 是如何划分的有一定了解。如果对 React 代码库不是很熟悉, 请