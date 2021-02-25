---
title: background-position
date: 2021-02-01 10:10:01
tags:
---
百分比值的偏移指定图片的相对位置和容器的相对位置重合。值0%代表图片的左边界（或上边界）和容器的左边界（上边界）重合。值100%代表图片的右边界（或下边界）和容器的右边界（或下边界）重合。值50%则代表图片的中点和容器的中点重合。

当指定百分比值的时候，实际上执行了以下的计算公式（该公式可以用数学方式定义图片和容器相对位置重合）：

(container width - image width) * (position x%) = (x offset value)
(container height - image height) * (position y%) = (y offset value)

附赠一点 background repeat 的小知识
与通常的认知不同的是，background repeat 定义的并不是背景图展示完之后是否应该重复；而是背景图充满了元素之后，是否应该重复。
举个例子
```js
const style ={
    backgroundSize: '200%',
    backgroundRepeat: 'no-repeat',
    backgroundPosition: '-100%',
}
```
这种情况下，元素的背景图并不是右半张图，而是空白！