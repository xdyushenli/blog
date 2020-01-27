---
title: 常见面试题及答案汇总
date: 2019-08-03 09:36:50
tags: [ 面试 ]
---
目标腾讯！冲鸭！

# CSS
## CSS 布局
### 水平居中
* 对于水平居中, 首先应当考虑的就是 `text-align: center`。但是这个只对行内内容有效, 所以要想使用 `text-align: center`, 就必须把子元素设置为 `display: inline` 或 `display: inline-block`。
* 其次要考虑能不能用 `margin: 0 auto`。使用这个样式的前提是子元素必须为块状元素, 宽度不能为 `auto`, 且子元素宽度必须必父元素小。
* 再次要考虑使用 `flex` 布局。水平居中布局的代码为:
```css
.parent {
    display: flex;
    justify-content: center;
}
```
* 最后再考虑使用绝对定位。
原理是: 父元素相对定位, 子元素绝对定位, 其中 `top`、`left`、`right` 和 `bottom` 都是相对于父元素尺寸的, 而 `margin` 和 `transform` 都是相对于自身尺寸的。
**应当注意的是, 设置 `margin-left` 值时不能使用百分比值, 因此需要提前知道元素的宽度。**
```css
.parent {
    position: relative;
}

.son {
    width: 100px;
    position: absolute;
    left: 50%;
    transform: translateX(-50%); /* 相当于: margin-left: -50px; */

    /* 或者使用下面这种方式: */
    left: 0;
    right: 0;
    margin: 0 auto;
    /*原理: 当 left 和 right 为 0 时, margin-left 和 margin-right 会无限延伸占满父元素空间并且平分。*/
}
```

### 垂直居中
* 对于垂直居中, 首先应该想到的就是 `line-height`, 但是这个只适用于当前元素的子元素均是行内元素或文本时适用。**应当注意的是, 当 `line-height` 的值是百分比时, 其计算是根据字体大小, 而不是元素宽高来计算的!**
* 其次, 如果是图片的话, 要考虑能不能使用 `vertical-align: middle`。
原理比较复杂, 首先我们要明白, `vertical-align` 设置的是行内元素相对于父元素 `line-height` 的对其方式。**`<img />标签`也属于行内元素的一种**, 要想让其垂直居中且适用 `vertivertical-align: middle`, 需要把父元素的 `line-height` 设置为父元素高度, **且必须在父元素上加上 `font-size: 0` 才能起作用。**代码如下:
```css
.parent {
    height: 100px;
    line-height: 100px;
    font-size: 0;
}

.son {
    vertical-align: middle;
}
```
* 再次便是考虑能不能用 `flex` 布局解决。
```css
.parent {
    display: flex;
    align-items: center;
}
```
* 最后再考虑使用绝对定位。原理和适用绝对定位水平居中一样。
```css
.parent {
    position: relative;
}

.son {
    height: 100px;
    position: absolute;
    top: 50%;
    transform: translateY(-50%); /* 相当于: margin-top: -50px; */

    /* 或者使用下面这种方式: */
    top: 0;
    bottom: 0;
    margin: auto 0;
    /*原理: 当 top 和 bottom 为 0 时, margin-top 和 margin-bottom 会无限延伸占满父元素空间并且平分。*/
}
```

#### 可替换元素 (replaced element) 与非替换元素 (none-replaceable element)
* `可替换元素(replaced element)`就是浏览器根据元素的标签和属性，来决定元素的具体显示内容, 这些元素的展示效果不受 CSS 的影响。
典型的可替换元素有: `<img>`、`<video>`、`<iframe>` 以及 `<embed>`。
* `非替换元素(none-replaceable element)`就是那些内容直接呈现给客户端的标签。
HTML 中绝大多数标签都是非替换元素。

### 水平垂直居中
* 首先考虑使用绝对定位。
```css
.parent {
    position: relative;
}

.son {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);

    /* 或者使用下面这种方法 */
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
}
```
* 其次考虑使用 `flex` 布局。
```css
.parent: {
    display: flex;
    justify-content: center;
    align-items: center;
}
```
* 如果是行内元素或行内块状元素, 可以考虑将 `text-align`、`line-height` 以及 `vertical-align` 结合起来使用。
```css
.parent {
    height: 150px;
    line-height: 150px; /* 行高与元素高度相等 */
    text-align: center; /* 水平居中 */
    font-size: 0; /* 消除幽灵空白节点的 bug */
}

.son {
    /* 如果是块级元素需改为行内或行内块级才生效 */
    /* display: inline-block; */
    vertical-align: middle; /* 垂直居中 */
}
```
* 如果需要相对于视窗居中, 可以使用 `vh` 和 `vw` 来实现。
```css
.son {
    margin: 50vh auto 0;
    transform: translateY(-50%);
}
```

### 两列布局
#### 一列定宽, 另一列自适应
#### 一列不定宽, 另一列自适应

### 三列布局

### 多列布局

### 全屏布局

## BFC
### 什么是 BFC?
BFC 的全程是`块状格式化上下文(Block Formatting Context)`, **是一个独立的渲染区域, 让处于 BFC 内部的元素与外部的元素相互隔离, 使内外元素的定位不会相互影响。**
### BFC 的创建方法
BFC 的创建方法有很多, 如下:
* 根元素 (`<html>`)
* 浮动元素(元素的 `float` 不是 `none`)
* 绝对定位元素(元素的 `position` 为 `absolute` 或 `fixed`)
* 行内块元素(元素的 `display` 为 `inline-block`)
* `overflow` 值不为 `visible` 的块元素
* 弹性盒元素(`display` 为 `flex` 或 `inline-flex` 元素的直接子元素)

### BFC 内部规则
* 属于同一个 BFC 区域的两个相邻盒子垂直排列。
* 属于同一个 BFC 区域的两个相邻盒子的 `margin` 会发生重叠。
* 两个相邻不同的 BFC 区域的 `margin` 会发生重叠。
* BFC 中子元素不会超出 BFC 区域。
* BFC 区域不会与浮动元素重叠。
* 计算 BFC 区域宽高时, 浮动元素也参与计算。
* 文字层不会被浮动层覆盖, 而是环绕于周围。

### BFC 的用途
BFC 的用途如下:
* 阻止 margin 重叠。两个处于不同 BFC 区域的元素 margin 不重叠。
* 清除浮动。**清浮动的意义在于防止父元素高度坍塌**, 而BFC 区域可包含浮动元素, 将浮动元素的父元素设置为 BFC 区域, 可清除内部浮动。
* 阻止元素被浮动元素覆盖。

## flex 布局
### 简介
`flex 布局`又叫`弹性布局`, 是 CSS3 中新增的一种布局方式。flex中的属性可以分为`容器属性`和`项目属性`。容器属性和项目属性各有6个, 共12个。

### 容器属性
六个容器属性如下：
1. `flex-direction`：决定主轴的方向(即项目排列的方向), 默认为 `row`(左端为起点的行排列)。
2. `flex-wrap`：决定如果一条轴线排不下, 如何进行换行, 默认为 `nowrap`。
3. `flex-flow`：是 `flex-direction` 和 `flex-wrap` 的简写形式。
4. `justify-content`：定义了项目在主轴上的对齐方式, 默认为 `flex-start`(左对齐)。
5. `align-items`：定义项目在竖轴上如何对齐, 默认为 `stretch`(若项目未设置高度, 将占满整个容器)。
6. `align-content`：定义如果有多条主轴的话, 主轴之间应该如何对齐。

### 项目属性
六个项目属性如下：
1. `order`：定义项目的排列顺序, 数值越小, 排列越靠前, 默认为 `0`。
2. `flex-grow`：定义项目的放大比例, 默认为 `0` (即如果存在剩余空间也不放大)。
3. `flex-shrink`：定义项目的缩小比例, 默认为 `1` (即如果空间不足, 该项目将缩小)。
4. `flex-basis`：定义在分配多余空间之前, 项目占据主轴空间的大小, 默认为 `auto`(即项目的本来大小)。
5. `align-self`：允许单个项目有与其他项目不一样的对齐方式, 可覆盖 `align-items`值。
5. `flex`：该属性是 `flex-grow`、`flex-shrink` 和 `flex-basis` 的简写, 默认为 `0 1 auto`。后两个属性可选。

## CSS3 特性总结


# JavaScript
## 闭包
## 原型链
## Promise
## async\await
## this
## apply\call\bind
## JS 的执行机制
## DOM
## 跨域问题
## 正则

# React

# Vue

# 计算机网络

# 算法

# Node