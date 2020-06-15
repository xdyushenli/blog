---
title: React贡献手册
date: 2019-12-13 16:00:37
tags: [ 翻译计划, react ]
---
本文为对 React 贡献手册的提炼, 便于快速回顾。
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
4. 确保所有代码通过测试 (在开发环境中可以使用`yarn test --watch TestName`进行测试) 。
5. 执行`yarn test-prod`命令以进行生产环境的测试。
6. 若开发中需要`debugger`, 运行以下命令`yarn debug-test --watch TestName`, 打开[`chrome://inspect`](chrome://inspect), 并按下`Inspect`。
7. 使用[`prettier`](https://github.com/prettier/prettier)格式化代码 (`yarn prettier`) 。
8. 确保代码样式一致 (`yarn lint`命令会检查所有文件, `yarn linc`仅仅检查修改过的文件) 。
9. 使用[`Flow`](https://flowtype.org/)执行类型检查 (`yarn flow`) 。
10. 确保完成[贡献者许可协议 (CLA) ](https://code.facebook.com/cla)。


# 源码概述
## 外部依赖
React几乎没有外部依赖。通常`require()`都会引用React源码中的文件, 当然也有个别例外。
依赖[`fbjs`](https://github.com/facebook/fbjs)仓库是因为React需要和一些类似于[`Relay`](https://github.com/facebook/relay)的库共享一些小功能, 并且与他们保持同步。`fbjs`中没有公共 API, 他们仅仅用于 Facebook 的项目, 比如 React。我们不依赖 Node 生态系统中同等功能的小模块。

## 顶层目录
克隆React仓库后, 可以看到一些顶层目录：
* `packages`包含了元数据 (比如package.json) 和React仓库中所有项目的源码 (子目录src) 。**如果需要修改源码, 那么每个包的src子目录是最需要花费精力的地方。**
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
warning 仅在开发环境中启用。在生产环境中, 它们会被完全剔除。如果需要在生产环境中禁止执行某些代码, 使用 `invariant` 模块代替 (该句原文如下：If you need to forbid some code path from executing, use `invariant` module instead:) 。
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
`injection` 字段并没有进行特殊处理。但当某个模块有这个字段存在时, 通常意味着该模块在运行时需要注入一些 (很可能是与平台相关的) 依赖。

## Multiple Packages
React 采用 [`monorepo`](https://xdyushenli.github.io/2019/12/14/advantages-of-monorepos/) 的管理方式。仓库中包含多个独立的包, 以便于更改时可以一起联调, 并且限制问题的影响范围。

## React Core
`React Core` 中包含所有的全局 React API, 比如：
* React.createElement()
* React.Component
* React.Children
React Core 只包含定义组件必要的 API。它不包含 diff 算法或者依赖其他平台特定的代码。它同时适用于 React DOM 和 React Native 组件。
React Core 的代码在源码的 `packages/react` 目录中。在 npm 上发布为 react 包。相应的独立浏览器构建版本称为 `react.js`, 它会导出一个名为 `React` 的全局对象。

## 渲染器 (Renderers) 
React 最初只是服务于 DOM, 但之后加入了对原生平台的 React Native 的支持。因此, 在 React 内部机制中引入了 `渲染器` 这一概念。
**渲染器用于管理一棵 React 树, 使其根据底层平台进行不同的调用。**
渲染器同样位于 `packages` 目录下：
* `React DOM Renderer` 将 React 组件渲染为 DOM。它实现了全局` ReactDOM API`, 在 npm 上作为 react-dom 包独立发布。也可作为单独浏览器版本进行使用, 称为 `react-dom.js`, 导出一个 `ReactDOM` 的全局对象。
* `React Native Renderer` 将 React 组件渲染为 Native 视图。此渲染器在 React Native 内部使用。
* `React Test Renderer` 将 React 组件渲染为 JSON 树, 用于 Jest 的 快照测试。在 npm 上作为 react-test-renderer 包发布。
* `React Art` 使用 React 绘制矢量图形。原本是一个独立的 Github 仓库, 目前加入了主源代码树中。

## 调度器 (Reconcilers) 
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
这是一个[深入研究事件系统代码的视频 (66min) ](https://www.youtube.com/watch?v=dRo_egw7tBc)。

# 实现说明
这部分是一个对于如何实现 Stack Reconciler 的总结。由于其已被 Fiber Reconciler 代替, 因此不做赘述。

# 设计理念
这部分主要讲述的是 React 的设计理念, 而非 React 组件的设计理念。

## 组合
组件之间的组合是 React 的重要特征。不同开发者写的组件应该可以一起正常执行。给一个组件添加功能, 而不会对整个代码库造成涟漪似的变化, 这对我们很重要。

## 公用抽象
一般而言我们拒绝添加一些开发者可以实现的特性。我们不想因为无用的库代码使得大家的应用变的累赘, 然而也有特例。如果我们发现很多组件以不兼容或者不高效的方式实现了某些特性, 我们会倾向在 React 中实现它。我们轻易不这样做, 只有我们非常确定提高抽象层级有助于整个生态系统时我们才会这样做。State、生命周期函数、跨浏览器事件的正规化都是很好的范例。

## 应急方案
如果要废弃某个我们不喜欢的模式, 我们有责任在废弃之前, 考虑所有已知的用例并且教育社区这些模式的替代做法。
如果某些有助于开发应用的模式很难以声明式的方式表达, 我们会提供一个命令式的 API。
如果我们发现很多应用中必要的模式我们找不到一个完美的API, 我们会提供一个临时欠佳的 API, 只要以后可以移除它并且方便后续的优化。

## 稳定性
当我们废弃一个模式时, 我们会研究它在 Facebook 内部的使用情况并添加废弃警告。这样我们可以评估变更的影响范围。
如果我们自信地认为这次改动破坏性不是非常大, 而且迁移策略对所有用户场景都可行, 我们会在开源社区发布废弃警告。
没有充分的理由我们不会废弃任何特性。我们意识到有时废弃警告会让开发者沮丧, 但我们添加废弃警告是因为它们为社区中的很多人认为有价值的优化和新特性扫除了障碍。

## 协调性
我们很看重 React 与已有系统的协调性和渐进式地使用。任何产品团队能够开始在小功能上使用React而不是重写他们的代码, 这对我们很重要。
这就是 React 提供应急方案的原因：让不同的模型协同工作, 让不同的 UI 库协同工作。你可以把一个已有的命令式 UI 封装成声明式的组件, 反之亦然。这对渐进式地应用 React 而言至关重要。

## 调度
即便你的组件以 function 的方式声明, 在 React 中你也并不会直接调用它们。每个组件返回一个该渲染什么的描述, 该描述会包含开发者写的组件如 <LikeButton> 和 平台特定的组件 如 <div>。由 React 决定在未来的某个时间点展开 <LikeButton>, 并根据组件的渲染结果递归地把这些变更实际应用到 UI 树上。
虽然只是微小的区别, 但这样做意义重大。因为你不需要调用组件方法而是让 React 调用它, 这意味着如果必要 React 可以延迟调用。在 React 当前的实现中, React 在单个 tick 周期中递归地走完这棵树, 然后调用整个更新后树的渲染方法。但是以后 React 可能会延迟一些更新操作来防止掉帧。
React 不是一个常规的数据处理库, 它是开发用户界面的库。我们认为 React 在一个应用中的位置很独特, 它知道当前哪些计算当前是相关的, 哪些不是。
如果不在当前屏幕, 我们可以延迟执行相关逻辑。如果数据数据到达的速度快过帧速, 我们可以合并、批量更新。我们优先执行用户交互 (例如按钮点击形成的动画) 的工作, 延后执行相对不那么重要的后台工作 (例如渲染刚从网络上下载的新内容) , 从而避免掉帧。
如果我们允许用户使用在一些函数反应式编程范式中常见的 "push" 模式直接拼接视图, 我们将会很难获得调度的控制权。
React 的一个关键目标是在把控制权转交给 React 之前执行的用户代码量最少。这确保 React 保持调度的能力, 并根据它所知道的 UI 的情况把工作切分成小块处理。

## 开发者体验
提供好的开发者体验对我们很重要。
例如, 我们维护了 React DevTools, 它让大家在 Chrome 和 Firefox 浏览器中可以检查 React 组件树。我们听说它大幅提高了 Facebook 的工程师和社区的生产效率。
我们还提供了对开发有所帮助的开发者警告。比如你以浏览器不理解的方式嵌套标签或者你在编写 API 时常见的错别字, React 会警告你。开发者警告和相关的检查导致了 React 的开发版本比生产版本速度慢。
Facebook 内部的使用模式帮助我们了解常见的错误有哪些, 以及如何提前预防。当我们添加新特性时, 我们尝试去预测可能会发生的错误并发出警告。
我们一直在寻找提高开发者体验的方法。我们很乐意听取大家的建议, 接受大家的贡献, 把开发者体验做的更好。

## 调试
当发生错误时, 你有一些线索可以追溯到代码库中的具体代码, 这很重要。在 React 中, props 和 state 就是线索。
当你看到屏幕上出现错误时, 你可以打开 React 开发者工具, 找到负责渲染的组件, 检查它的 props 和 state 是否正确。如果正确, 你就知道为题要么在于组件的 render() 方法, 要么在于某个被 render() 调用的方法。可见问题被独立出来了。
如果 state 发生错误, 你就知道问题在于这个文件中的某个 setState() 调用, 定位和修复也相对简单, 因为在单个文件中 setState() 调用次数很少。
如果 props 出现错误, 你可以在检查器中沿着树向上排查, 查找到第一个因为向下传递了错误的 props 而“污染了这口井”的组件。

## 配置
我们发现全局的运行时配置会造成很多问题。
比如我们时常收到这样的请求, 实现类似于 `React.configure(options)` 或者 `React.register(component)` 的方法。然而这样做会出现很多问题, 而我们对此并没有很好的解决方案。
如果有人在第三方组件库中调用了这样的方法怎么办？如果一个 React 应用内嵌了另一个 React 应用, 而且他们需要的配置不兼容怎么办？一个第三方组件怎么申明它需要某个特定配置呢？我们认为全局的配置与组合搭配效果不好。既然组合是 React 的核心, 那么我们不在代码里提供全局的配置。
然而我们在构建层面提供了一些全局的配置。比如我们提供了独立的开发和生产环境的构建。我们后面也许会增加一个用于分析的构建方案。我们对考虑使用其他构建参数保持开放态度。

## DOM 之外的平台
React 的一个重要设计约束是要渲染引擎无关。这在内部呈现增加了一些开销, 另一方面, 对内核的任何优化将对所有平台都有益。
单一的编程模型使我们能够围绕产品形成工程团队, 而不是围绕平台。目前来看这样的取舍对我们来说很值得。

## 内部实现
相比优雅的实现, 我们更想提供尽可能优雅的 API。现实是不完美的, 如果能让用户无需写这些代码, 在合适的范围内, 我们偏向把丑陋的代码写在库里。我们评估新代码时, 我们看中的是正确地实现、高性能并且能够带来良好的开发体验, 优雅是第二位的。
我们宁可写无聊的代码也不写耍聪明的代码。代码是一次性的而且经常变更, 所以**除非极度必要, 不引入新的内部抽象**很重要。很方便移动、改动和删除的啰嗦的代码比过早抽象且难以变更的优雅的代码更好。

## 针对工具的优化
React 一些常用的 API 名字很冗长。这是有意为之的, 目的是使得其他框架和 React 的交互点具有高可见性。在像 Facebook 这样庞大的代码库中, 能够搜索某些特定 API 的使用很重要。我们重视清晰冗长的名字, 特别是在一些需要保守使用的特性上。我们希望非常容易、安全地在代码库中应用大量自动化变更, 独特冗长的名字帮助了我们。类似地, 独特的命名使得编写自定义 React 用法的提示规则变得很容易, 无需担心潜在的错误匹配。

## 内部测试
我们全力解决社区提出的问, 但我们可能会优先处理在 Facebook 内部遇到的同样的问题。
