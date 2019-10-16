---
title: react源码阅读日记（二）
date: 2019-10-11 19:11:43
tags: [ react ]
---
# 本章简介
本章主要介绍React创建更新的过程，但不涉及执行更新。
在React中，有三种创建更新的方式：
1. ReactDOM.render || hydrate
2. setState
3. forceUpdate
咱们一个一个来看。

# ReactDOM.render
## 创建更新的步骤
1. 创建ReactRoot
2. 创建FiberRoot和RootFiber
3. 执行更新，进入更新调度阶段