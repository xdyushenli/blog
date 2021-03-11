---
title: State of JS 2020
date: 2021-03-11 08:18:09
tags: [ js ]
---
# 前言
针对 State of js 2020 报告的个人版总结。

# 脚手架
框架方面，出现了 `Svelte`
构建工具方面，出现了 `snowpack` 以及撰写该文稿时出现的 `vite`。

后续会体验下这三个工具，看看具体能带来多大的提升。
todo 博客

# 特性
## 浏览器 API
### 自定义元素（Custom Elements）
就支持性来说，还没有达到能在生产环境应用的标准。

> Firefox、Chrome 和 Opera 默认就支持。Safari 目前只支持 Autonomous Custom Elements（自主自定义标签），而 Edge 也正在积极实现中。

通过这个 API，开发者可以将 HTML 页面的功能封装为一个自定义元素，以便于复用。**自定义元素是 web component 的重要组成部分。**

使用时，通过 `CustomElementRegistry.define()` 来注册自定义元素。在自定义元素的构造方法中可以进行一系列 DOM 操作。同时自定义元素还提供了一些生命周期函数，如`connectedCallback（自定义元素被插入 DOM 时调用）`、`disconnectedCallback（自定义元素从 DOM 中删除时调用）`等，便于进行额外操作。

总体上给人的感觉类似于 React 组件，只不过是浏览器原生支持的。

### Web Speech API
支持性尚不佳。

用于处理语音数据的 API，分为两部分：
- `SpeechSynthesis`：语音合成，提供了文本到语音（TTS）的能力。
- `SpeechRecognition`：语音识别，提供了从音频输入设备中获取语音数据的能力。

### 

# 总结

# 参考链接
- [State of js 2020](https://2020.stateofjs.com/zh-Hans/)
- [MDN: Custom Elements](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components/Using_custom_elements)
- [MDN: Web Speech API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Speech_API)