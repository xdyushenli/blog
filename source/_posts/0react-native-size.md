---
title: react-native-size
date: 2020-07-22 16:37:40
tags:
---
# 简介
本文主要介绍在 React Native 中，如何适配不同设备。

# 为什么需要适配？
设备种类多，屏幕尺寸不确定，为了保证在多种设备上显示的统一性，所以需要进行单位转换及尺寸的适配。

# 单位有哪些？有什么区别？
RN 中的尺寸单位为 dp，而设计稿中的单位为 px。
todo px to dp 的公式是啥？
todo PixelRatio.getPixelSizeForLayoutSize() 以及 PixelRatio 是干啥的？
todo aspectRatio 是干啥的？

# 适配的场景有哪些？

# 分别的解决方案是？

# 参考资料
https://stackoverflow.com/questions/34493372/what-is-the-default-unit-of-style-in-react-native
https://github.com/haitaodesign/RN_STYLESHEET#%E5%9B%BE%E7%89%87%E9%80%82%E9%85%8D
https://bingoootang.github.io/blog/2018/08/31/react-native-adaptive/
https://zhuanlan.zhihu.com/p/55826586
https://dev.to/hrastnik/the-react-native-aspectratio-ge3

# 素材区
图片 RN 会自动进行适配(https://github.com/haitaodesign/RN_STYLESHEET#%E5%9B%BE%E7%89%87%E9%80%82%E9%85%8D)

系统型解决方案：https://github.com/vitalets/react-native-extended-stylesheet
支持高级 css 特性，如rem、百分比、媒体查询等。

适配解决方案
https://github.com/bingoootang/react-native-adaptive-stylesheet

RN好扯淡...为啥不跟小程序似的做个统一单位得了