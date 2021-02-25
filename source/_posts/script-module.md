---
title: script-module
date: 2020-12-21 10:10:01
tags:
---
问题：是不是所有支持 script module 的浏览器都能很好的支持 es6？

为什么要用这个？？
type=module 的代码内部会支持 ES6 模块。
检测浏览器是否支持 type=module，不支持的话打上 polyfill，支持的话就不转化了。 -> 可以打上 nomodule 属性，支持 script module 的浏览器会忽略这种 script 标签，而不支持 script module 的浏览器会加载这种 script 标签。

It has therefore made sense in recent years to start thinking about providing mechanisms for splitting JavaScript programs up into separate modules that can be imported when needed.

推荐使用 .mjs 后缀来标记模块代码，.js 来标记一般代码。

To get modules to work correctly in a browser, you need to make sure that your server is serving them with a Content-Type header that contains a JavaScript MIME type such as text/javascript.

Note: In some module systems, you can omit the file extension and the leading /, ./, or ../ (e.g. 'modules/square'). This doesn't work in native JavaScript modules.
不能忽略文件后缀名

module script 与 regular script 的区别
- You can only use import and export statements inside modules, not regular scripts.
- You need to pay attention to local testing — if you try to load the HTML file locally (i.e. with a file:// URL), you'll run into CORS errors due to JavaScript module security requirements. You need to do your testing through a server.
- Also, note that you might get different behavior from sections of script defined inside modules as opposed to in standard scripts. This is because modules use strict mode automatically.
- There is no need to use the defer attribute (see <script> attributes) when loading a module script; modules are deferred automatically.
- Modules are only executed once, even if they have been referenced in multiple <script> tags.
- Last but not least, let's make this clear — module features are imported into the scope of a single script — they aren't available in the global scope. Therefore, you will only be able to access imported features in the script they are imported into, and you won't be able to access them from the JavaScript console, for example. You'll still get syntax errors shown in the DevTools, but you'll not be able to use some of the debugging techniques you might have expected to use.

Old browsers do not understand type="module". Scripts of an unknown type are just ignored. For them, it’s possible to provide a fallback using the nomodule attribute
这点特性很重要，可以据此降级，使用 type=module 加载 es6 代码，使用 nomodule 标签打包不支持 es6 的代码

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#A_background_on_modules
- https://javascript.info/modules-intro#in-a-module-this-is-undefined
- https://philipwalton.com/articles/deploying-es2015-code-in-production-today/