---
title: 无限下拉滚组件的实现方式
date: 2020-06-16 13:49:03
tags: [ react ]
---
# intersection-observer
简单来说，intersection observer是一种新技术, 用于监控目标元素与指定区域的重合程度, 在达到指定程度时执行回调函数。
具体参见 [Intersection Observer API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)。

# 弹性布局与响应式布局的区别？
弹性布局是响应式布局的一种。
弹性布局强调等比例缩放。
响应式布局强调不同屏幕有不同显示，比如媒体查询。

# em、rem 是什么？
rem 是相对于根节点的 font-size 大小来设置的。当没有设置根元素 font-size 或使用 rem 设置根元素 font-size 时，rem 是相对于初始 font-size 大小————也就是 16 px ————来计算的。
em 是相对于自身的 font-size 大小来设置的。

# rem 如何实现 vw？
html { font-size: width / 100; }

# 如何使用 rem 完美实现设计图？
说白了就是使用 vw 和 vh。
屏幕比例不可控，可以让设计多出点图。

# 为什么说 em 是为 font-size 而生的？
todo

# 参考资料
* [Intersection Observer API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)
* [Rem布局的原理解析](https://yanhaijing.com/css/2017/09/29/principle-of-rem-layout/)

# 有待阅读