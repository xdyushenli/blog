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

# 特性
## 语法
主要有以下几种：

- `Optional Chaining（?.）`
- `Nullish Coalescing（??）`
- `Private Fields`：在类语法中，使用 `#` 前缀来定义私有方法和属性。
```js
class ClassWithPrivateMethod {
    #privateMethod() {
        return 'hello world'
    }
}
```

- `Destructuring assignment（解构取值）`
- `Spread Operator（...）`
- `Arrow Functions`

除了定义私有方法和属性外，其他的都是比较普及的语法了。

## 语言
### Decorator
修饰器（Decorator）是一个函数，用来修改类的行为。简而言之，就是在类被实例化时修改其方法或属性。

```js
// Decorator 改变整个类
function testable(target) {
    target.isTestable = true;
}

@testable
class MyTestableClass {}

console.log(MyTestableClass.isTestable) // true

// 或
function testable(isTestable) {
    return function(target) {
        target.isTestable = isTestable;
    }
}

@testable(true)
class MyTestableClass {}

MyTestableClass.isTestable // true

@testable(false)
class MyClass {}

MyClass.isTestable // false
```

```js
// Decorator 改变类的方法
class Person {
    @readonly
    name() { return `${this.first} ${this.last}` }
}

function readonly(target, name, descriptor){
    // 改变方法的描述对象
    descriptor.writable = false;
    return descriptor;
}
```

### Dynamic Import
动态引入。
```js
import('/modules/my-module.js')
.then((module) => {
    // Do something with the module.
});

let module = await import('/modules/my-module.js');
```

### Proxy
通俗的来说，Proxy 会在对象之前架起一道代理，所有对于对象属性和方法的访问都会先执行代理中的方法。

```js
let hero = {
    name: "赵云",
    age: 25
}

let handler = {
    get: (hero, name, ) => {
        const heroName =`英雄名是${hero.name}`;
        return heroName;
    },
    set:(hero,name,value)=>{
        console.log(`${hero.name} change to ${value}`);
        hero[name] = value;
        return true;
    }
}

let heroProxy = new Proxy(hero, handler);

console.log(heroProxy.name);
// --> 英雄名是赵云
heroProxy.name = '黄忠';
// --> 赵云 change to 黄忠
console.log(heroProxy.name);
// --> 英雄名是黄忠
```


### async/await
老朋友了，不做介绍。

### Promise
不会吧不会吧，不会还有人没用过 Promise 吧。

### Promise.allSettled()
> The Promise.allSettled() method returns a promise that resolves after all of the given promises have either fulfilled or rejected, with an array of objects that each describes the outcome of each promise.
—— MDN: Promise.allSettled()

## 浏览器 API
### Custom Elements（自定义元素）
就支持性来说，还没有达到能在生产环境应用的标准。——2021.03

> Firefox、Chrome 和 Opera 默认就支持。Safari 目前只支持 Autonomous Custom Elements（自主自定义标签），而 Edge 也正在积极实现中。

通过这个 API，开发者可以将 HTML 页面的功能封装为一个自定义元素，以便于复用。**自定义元素是 Web Component 的重要组成部分。**

使用时，通过 `CustomElementRegistry.define()` 来注册自定义元素。在自定义元素的构造方法中可以进行一系列 DOM 操作。同时自定义元素还提供了一些生命周期函数，如`connectedCallback（自定义元素被插入 DOM 时调用）`、`disconnectedCallback（自定义元素从 DOM 中删除时调用）`等，便于进行额外操作。

总体上给人的感觉类似于 React 组件，只不过是浏览器原生支持的。

### Shadow DOM
支持性较佳，在主流浏览器以及新 Edge 中均支持，旧 Edge 不支持。

**Shadow DOM 也属于 Web Component 的组成部分**。通过 Shadow DOM 可以封装一系列 DOM 节点，同时将这些隐藏的、独立的 DOM 节点附加到某个元素上。 

> From what I've read and the way I use it, is ShadowDom has to do with encapsulation like if you put a \<style> tag inside the ShadowDom the css will only be applied to the ShadowDom and document fragments has to do with browser reflow.
———— ShadowDOM vs Document Fragments — How do they differ?

### Web Speech
支持性尚不佳。——2021.03

用于处理语音数据的 API，分为两部分：
- `SpeechSynthesis`：语音合成，提供了文本到语音（TTS）的能力。
- `SpeechRecognition`：语音识别，提供了从音频输入设备中获取语音数据的能力。

### Web Audio
支持性尚不佳。——2021.03

Web Audio API 是一组用于在浏览器中操作音频数据的 API，通过这些 API，开发者可以做到选择音频源、为音频增加混响等特效、音频可视化等功能。

一个使用 Web Audio API 的基本流程是：
1. 创建一个 Web Audio 上下文。
2. 在 Web Audio 上下文中创建源，如 `<audio>` 等。
3. 创造效果节点，如混响、压缩、平移等，用于为音频数据添加特效。
4. 选择输出目的地，如你的系统扬声器。
5. 将源连接到输出目的地，输出效果节点处理后的音频数据。

### Web Animations
支持性尚不佳，但提供了 [ployfill](https://github.com/web-animations/web-animations-js)。 ——2021.03

简单来说，Web Animation 是 CSS Animation 和 CSS Translation 的 JS 版本。除此之外，还提供了一些用于控制动画播放的 API，如 `play()`、`pause()`、`reverse()` 等。

### Fetch
老朋友了，不做赘述。

### WebSocket
用于创建 WebSocket 连接，通过该连接可以发送和接收数据，允许服务器推送数据到客户端。

### Service Worker
**Service workers 本质上充当 Web 应用程序、浏览器与网络（可用时）之间的代理服务器**。这个 API 旨在创造更好的离线体验，它会拦截网络请求并根据网络是否可用采取来适当的动作、更新来自服务器的的资源。它还提供入口以推送通知和访问后台同步 API。

### Local Storage
老朋友了，不做赘述。

### WebVR
通过 WebVR，开发者可以将位置和动作信息转换成 3D 场景中的运动。

就目前而言，无论是 VR 还是 WebVR API 都尚不成熟，预计还要很久才能投入生产环境中。

### WebRTC
`WebRTC（Web Real-time Communication）`是一项实时通讯技术，它允许站点在不借助中间媒介的情况下，建立浏览器之间的`点对点（peer to peer）`连接，实现视频流和音频流或其他任意数据的传输。这使得实时网络通讯和点对点分享称为可能。

WebRTC 不仅仅只是一组 API，还包括一系列网络协议和连接标准。其使用的协议包括：[ICE](https://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment)、[STUN](https://en.wikipedia.org/wiki/STUN)、[NAT](https://en.wikipedia.org/wiki/Network_address_translation)、[TURN](https://en.wikipedia.org/wiki/TURN) 以及 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 等。

### Intl
`Intl` 全称是 `Internationalization（国际化支持）`，代表了一组方法，提供了一系列字符串、数字、日期、列表等的格式化方法。具体包括：

- [Intl.Conllator()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Collator/Collator)：`Collators（校对员） `的构造函数，用于创建对语言敏感的字符串比较对象。
- [Intl.DateTimeFormat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat)：`DateTimeFormat` 的构造函数，用于创建对语言敏感的 Date 对象格式化函数。
- [Intl.DisplayNames()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DisplayNames/DisplayNames)：用于创建可以自由切换语言的、用于显示国家名称的对象。
- [Intl.ListFormat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/ListFormat/ListFormat)：`ListFormat` 的构造函数，用于创建对语言敏感的列表格式化函数。
- [Intl.Locale()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Locale/Locale)：`locale` 意为 `区域设置`，也称为 `本地化策略集`、`本地环境`，是表达程序用户地区方面的软件设定。该方法用于构造代表指定地域的 Unicode 编码的设置。
- [Intl.NumberFormat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat/NumberFormat)：`NumberFormat` 的构造函数，用于创建对语言敏感的数字格式化函数。
- [Intl.PluralRules()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/PluralRules/PluralRules)：`Plural（复数）Rules` 的构造函数，用于创建对语言敏感的复数格式化函数。
- [Intl.RelativeTimeFormat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat/RelativeTimeFormat)：`RelativeTimeFormat` 的构造函数，用于创建语言敏感的相对时间格式化函数。

可以看到，Intl 的大多数方法都是对语言敏感的，因此常用于国际化网站的开发和处理。

### WebGL
`WebGL (Web Graphics Library) `是一组用于渲染交互式 2D 和 3D 图形的 API，可以在 \<canvas> 中使用。

工作中之后用到的应该会比较多，值得注意。

# 技术
这里会列举一些排名比较靠前的技术，日后可以多多了解一下。

- 开发语言
  - TypeScript
- 前端框架
  - Svelte
  - React
  - Vue.js
- 数据层
  - GraphQL
  - Apollo Client
  - Vuex
- 后端框架
  - Next.js
  - Express
- 测试
  - Testing Library
  - Jest
- 构建工具
  - esbuild
  - Snowpack
  - Typescript
  - webpack
- 移动端和客户端
  - Electron

# 其他工具
常用工具库。

- Axios
- Lodash：增加多种数据类型，补充了一些对于数组、对象等的处理方法。
- Moment
- date-fns
- Material UI
- D3：绘图库。

# 发展方向
目前大家认为 JavaScript 最缺少了的东西的 `Static Typing（静态类型）`。

简单来说，静态类型就是像 C 语言一样，在变量使用之前需要先定义其类型。

# 总结
每年都看一看吧，把握业界最新的状态。

# 参考链接
- [State of js 2020](https://2020.stateofjs.com/zh-Hans/)
- [MDN: import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
- [MDN: Promise.allSettled()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled)
- [MDN: Custom Elements](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components/Using_custom_elements)
- [知乎: 深入实践 ES6 Proxy & Reflect](https://zhuanlan.zhihu.com/p/60126477)
- [MDN: Shadow DOM](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components/Using_shadow_DOM)
- [Stack Overflow: ShadowDOM vs Document Fragments — How do they differ? [closed]](https://stackoverflow.com/questions/27512512/shadowdom-vs-document-fragments-how-do-they-differ)
- [MDN: Web Speech API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Speech_API)
- [MDN: Web Audio API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API)
- [MDN: Web Animations API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Animations_API)
- [MDN: Use Web Animations API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Animations_API/Using_the_Web_Animations_API)
- [MDN: WebScoket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
- [MDN: Service Worker API](https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API)
- [MDN: Web VR API](https://developer.mozilla.org/zh-CN/docs/Web/API/WebVR_API)
- [MDN: WebRTC API](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API)
- [MDN: Introduction to WebRTC protocols](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols)
- [MDN: Intl](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl)
- [MDN: Intl(en)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl)
- [MDN: WebGL API](https://developer.mozilla.org/zh-CN/docs/Web/API/WebGL_API)
- [MDN: WebGL API(en)](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)