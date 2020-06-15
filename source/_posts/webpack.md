---
title: Webpack 入门指南
date: 2020-06-15 11:59:21
tags: [ webpack ]
---
# Webpack 是什么？
`Webpack` 是一种打包工具，常常与 `Babel` 搭配使用。
其中，`Babel` 负责将我们的项目 `js` 代码转化为所有浏览器都能够识别的代码；`Webpack` 则负责将所有的代码打包成一个文件，同时支持本地开发热更新、编译 `Less/Sass` 等功能。

# 如何从零开始构建 React 项目？
通常情况下，我们可以使用 `create-react-app` 来为我们完成这些工作，但是了解一下总是好的。具体过程参见[这里](https://www.valentinog.com/blog/babel/)。

这篇文章中只介绍了如何在项目中使用 `React`、`Webpack` 以及 `Babel`，并没有提及如何在项目中使用 `Less/Sass`，因此这里再附加一点内容，算是额外赠品。

从上面的文章中我们了解到，所谓的 `loader` 其实就是集成在 `Webpack` 中的文件处理方法。那么要将 `Less` 转化并打包，需要三个 `loader`:
* `less-loader`：将 `Less` 代码编译为 `CSS`。
* `css-loader`：将 `CSS` 装载到 `JS` 代码中。
* `style-loader`：让 `JS` 代码能够识别 `CSS`。

最终的配置看起来应该是这个样子的：
```js
module.exports = {
    module: {
        rules: [
            // ...
            {
                test: /\.less$/,
                exclude: /node_modules/,
                use: [
                    {
                        loader: "style-loader" // creates style nodes from JS strings
                    }, {
                        loader: "css-loader" // translates CSS into CommonJS
                    }, {
                        loader: "less-loader" // compiles Less to CSS
                    }
                ]
            },
        ]
    },
}
```

多个 `loader` 的加载顺序是从**右往左，从下到上**。前一个 `loader` 的输出作为下一个 `loader` 的输入，因此它们之间的顺序并不能发生替换。

使用 `Sass` 和使用 `Less` 的设置差不多，只要将 `less-loader` 替换为 `sass-loader` 即可。

# 常见问题
* 引入 `.less` 文件不生效，显示 `module not found`。
这种情况一般是文件路径不明确导致的。假设 `js` 文件与 `less` 文件放在同一个目录下：
```js
// Module not found!
import 'example.less';

// Correct!
import './example.less';
```

# 参考文献
* [Tutorial: How to set up React, webpack, and Babel from scratch (2020)](https://www.valentinog.com/blog/babel/)