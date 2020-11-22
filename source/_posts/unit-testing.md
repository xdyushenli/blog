
---
title: unit-testing
date: 2020-11-02 09:59:34
tags:
---
基本思路：
为什要编写测试？
完整测试框架的构成要件
几种常见测试框架的对比
jest：
    基本测试
    测试异步代码
    测试生命周期函数
    内核：测试视图->测试vm
tip：如何测试视图
    snapshot？

为什么需要编写测试？
1. 保证我们的代码按照预想的那样运行。
2. 保证我们的代码在做了修改之后，仍然按照预想的那样运行。

比较出名的 js 测试框架：jasmine、mocha、chai

什么是 TDD（Test Driven Development）？
核心理念：在编写代码之前先编写测试。
三步走：
1. Write a test, watch it fail.
2. Write just enough code to pass the test.
3. Improve the code without changing its behavior.
Make it work, Make it right, Make it fase.

BDD：我目前采用的开发方式
Behavior Driven Development
测试作为产品的第一个使用者，保证产品的质量。————和目前的开发模式很像，测试作为开发的最后一个把关环节。

测试工具
todo 能不能做更细粒度的拆分？
todo 覆盖率工具 伊斯坦布尔 js
1. 测试环境：Mocha、Jasmine、Jest、Karma
2. 测试架构：Mocha、Jasmine、Jest、Cucumber
3. 断言：Chai、Jasmine、Jest、Unexpected
4. 测试结果的展示：Mocha、Jasmine、Jest、Karma

Karma 是什么？
基于 Node 的测试执行过程管理工具。
该工具可用于测试所有主流 Web 浏览器，也可以集成到 CI（Continuous integration）工具，还可以和其他代码编辑器一起使用。
Karma 会监控配置文件中所指定的每一个文件，每当文件发生改变，它都会向测试服务器发送信号，来通知所有的浏览器再次运行测试代码。此时，浏览器会重新加载源文件，并执行测试代码。其结果会传递回服务器，并以某种形式显示给开发者。

mocha、jasmine、jest 的区别？
* jasmine：需要在全局引入。不依赖 DOM。
* mocha：比较灵活。全局引入省去了你在文件中引入的麻烦，但是如果需要额外的插件的话，还是需要在对应的文件中引入这个插件才能起作用。
不包含断言库！这意味着 mocha 不包含 equal、expect 等语句。
不过幸运的是，mocha 支持很多种断言库。
* jest：
facebook 自己的测试框架，也是其推荐的与 react 一起使用的测试框架。
snapshot 能够检测 react 视图。当然，只要找到了正确的 plugin，jest 也能测试其他框架的视图。
API 比较丰富，无需引入其他的库。

其他值得一起的测试库：
* Puppeteer：
chrome 团队开发。Node 库、允许操控无头浏览器。
真正的交互式测试框架！支持页面结构测试、爬虫测试、屏幕快照测试等。几乎任何的 UI 测试都可以进行，并且还能测试键盘输入。

如何挑选测试框架？
- 需要很多的 API 支持以及较好的拓展性：mocha
- 需要轻量级测试：Ava、Tape
- 中大型项目、不想做很多配置工作：jest

todo 如何测试 ui 组件
这句话点出了测试必备的条件：稳定的测试环境、测试运行工具（测试服务器？）、测试工具、CI 持续集成（Continuous Integration）
> Sure, test-driven development (TDD) is weird at first, but a predictable environment, multiple test runners, test tooling baked into frameworks, and continuous integration support, make life easy.

> Here’s what we want to be able to do:
- Test React user events
- Test the response to those events
- Make sure the right things render at the right time
- Run tests in many browsers
- Re-run tests on file changes
- Work with continuous integration systems like Travis
jest 与 jasmine 的关系
>  Jest is built on Jasmine and uses the same syntax.

假的，jest 现在有 watch mode 了
> jest didn’t have a watch mode to automatically test new changes.
todo 真的吗？
> Jest doesn’t support running React tests in multiple browsers.（这里的 multiple browsers 不仅仅指多个浏览器，还指多个版本的浏览器）
https://www.toptal.com/react/how-react-components-make-ui-testing-easy

todo 如何让测试运行在多个浏览器里？并且还能自动运行？

todo 什么是 ci 持续集成？
https://www.redhat.com/zh/topics/devops/what-is-ci-cd#:~:text=CI%20%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90%EF%BC%88Continuous%20Integration,%E6%97%B6%EF%BC%8C%E8%80%8C%E4%B8%94%E9%9C%80%E8%A6%81%E6%89%8B%E5%8A%A8%E5%AE%8C%E6%88%90%E3%80%82
https://www.ruanyifeng.com/blog/2015/09/continuous-integration.html

测试的种类：
unit tests, acceptance tests, integration tests, end to end tests, component tests, and service tests.

Visual Testing——工具：react storybook

> Code that is perfect for unit-testing is code that has no I/O or UI dependencies
> We understood that we should keep our modules fairly independent of one another, but if there is a dependency, we should either mock the dependency and still use unit tests, or do integration tests.
> And we understood that the units we test should be isomorphic code, so that it can be tested using NodeJS. And we understood how to build isomorphic code — no I/O, using require as the way to import modules, and using Webpack to bundle all modules for browser consumption.
https://medium.com/@giltayar/testing-your-frontend-code-part-ii-unit-testing-1d05f8d50859

可以让一些单元测试聚焦于测试逻辑->数据的变化 vm 层

层级划分（从上到下）：e2e->交互测试->单元测试
> This is one reason why there should not be many E2E tests — if we tested correctly in the unit and integration tests, we have probably checked everything. The E2E test should just check that the glue binding all units works.
> E2E tests run in an environment that is as similar to production as possible.
e2e测试并不需要人工，但是需要尽可能真实的模拟用户环境和用户交互。
单元测试可以在node环境中运行，但是e2e测试必须在浏览器中运行（前提是你的代码是一个web app）。
本文中作者用express+mocha搭建了一个简易的文件服务器，使用其他一些工具模拟用户交互，再去监听视图的变化。（很奇怪，没有用snapshot，而是用了findelement这种方式，找到某个元素再手动确认其视觉表现）。
https://medium.com/@giltayar/testing-your-frontend-code-part-iii-e2e-testing-e9261b56475

todo 做一个 sdk，检测 parseData 之类的函数

todo react storybook 如何进行 visual testing？

todo jest 引以为傲的 parallel testing 是什么？

todo jest 如何测试异步代码？

todo snapshot

todo 如何测试视图？

todo 生命周期测试视图 -> 检测数据

todo 如何测试事件处理函数

todo 对于前端而言，视图显示的正确与否是测试需要解决的一大问题。那么如何才能测试视图展示的是否正确呢？

todo 如何使用 jest 测试 hooks 和生命周期函数？

todo
1. Jasmine, Karma, Mocha, expect.js, Jest 的历史
参考：airbnb的方案？
2. 要解决的实际问题：render次数  生命周期测试

# 为什么要进行单元测试？
# jest
# 简单测试
# state
# 异步代码
# 生命周期函数 & hooks
# render 次数
# snapshot