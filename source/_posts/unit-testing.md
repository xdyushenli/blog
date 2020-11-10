
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

todo jest 引以为傲的 parallel testing 是什么？

todo mocha、jasmine、jest 如何测试异步代码？

todo
对于前端而言，视图显示的正确与否是测试需要解决的一大问题。那么如何才能测试视图展示的是否正确呢？

todo 如何使用 jest 测试 hooks 和生命周期函数？为什么需要编写测试？
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

todo jest 引以为傲的 parallel testing 是什么？

todo mocha、jasmine、jest 如何测试异步代码？

todo
对于前端而言，视图显示的正确与否是测试需要解决的一大问题。那么如何才能测试视图展示的是否正确呢？

todo 如何使用 jest 测试 hooks 和生命周期函数？