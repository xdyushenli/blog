---
title: 常见面试题及答案汇总
date: 2019-08-03 09:36:50
tags: [ 面试 ]
---
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
* `可替换元素(replaced element)`就是浏览器根据元素的标签和属性, 来决定元素的具体显示内容, 这些元素的展示效果不受 CSS 的影响。
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

## flex
### 简介
`flex 布局`又叫`弹性布局`, 是 CSS3 中新增的一种布局方式。flex中的属性可以分为`容器属性`和`项目属性`。容器属性和项目属性各有6个, 共12个。

### 容器属性
六个容器属性如下: 
1. `flex-direction`: 决定主轴的方向(即项目排列的方向), 默认为 `row`(左端为起点的行排列)。
2. `flex-wrap`: 决定如果一条轴线排不下, 如何进行换行, 默认为 `nowrap`。
3. `flex-flow`: 是 `flex-direction` 和 `flex-wrap` 的简写形式。
4. `justify-content`: 定义了项目在主轴上的对齐方式, 默认为 `flex-start`(左对齐)。
5. `align-items`: 定义项目在竖轴上如何对齐, 默认为 `stretch`(若项目未设置高度, 将占满整个容器)。
6. `align-content`: 定义如果有多条主轴的话, 主轴之间应该如何对齐。

### 项目属性
六个项目属性如下: 
1. `order`: 定义项目的排列顺序, 数值越小, 排列越靠前, 默认为 `0`。
2. `flex-grow`: 定义项目的放大比例, 默认为 `0` (即如果存在剩余空间也不放大)。
3. `flex-shrink`: 定义项目的缩小比例, 默认为 `1` (即如果空间不足, 该项目将缩小)。
4. `flex-basis`: 定义在分配多余空间之前, 项目占据主轴空间的大小, 默认为 `auto`(即项目的本来大小)。
5. `align-self`: 允许单个项目有与其他项目不一样的对齐方式, 可覆盖 `align-items`值。
5. `flex`: 该属性是 `flex-grow`、`flex-shrink` 和 `flex-basis` 的简写, 默认为 `0 1 auto`。后两个属性可选。

## transition
`transition` 的主要用途是让一些交互效果(`transform`、`animation` 或 `hover` 等伪类)变得生动一些。
`transition` 的语法和示例如下。
```css
.example {
    transition: CSS属性 过渡时间 效果曲线(默认为 ease)  延迟时间(默认为 0);
    /* 在宽度从原始值到指定值时有一个过渡效果 */
    /* 运动曲线 `ease`, 在 `0.5` 秒内执行完毕, `0.2` 秒后执行过渡 */
    transition: width .5s ease .2s;
}
```

## animation
`animation` 通常是预设好的, 用于展示一些东西或增加交互效果。该属性常常和 `@keyframes` 结合使用。
`animation` 语法和示例如下:
```css
.example {
    animation: 动画名称 一个周期花费时间 运动曲线(默认 ease)  动画延迟(默认 0)  播放次数(默认 1)  是否反向播放动画(默认 normal)  是否暂停动画(默认 running);
    /* 2 秒后开始执行一次 wave 动画, 运动时间 2 秒, 运动曲线为 linear, 并且执行反向动画 */
    animation: wave 2s linear 2s infinite alternate;
}

/* 定义名为 wave 的动画 */
@keyframes wave {
    25% {
        height: 10px;
    }
    50% {
        height: 20px;
    }
    75% {
        height: 30px;
    }
    100% {
        height: 40px;
    }
}
```

## transform
`transform` 主要用于执行一些形状变换, 比如翻转、放大、缩小等。
`transform` 的语法如下:
```css
.example {
    /* 默认值, 不进行变换 */
    transform: none;
    /* 定义 2D 转换, 元素会进行移动, x 代表向右移动的偏移量, y代表向下移动的偏移量 */
    /* 如果是百分比的话, 则是根据当前元素宽高来计算的 */
    transform: translate(x, y);
    transform: translateX(y);
    transform: translateY(x);
    /* 定义 2D 转换, 元素会顺时针旋转给定的角度 */
    /* 注意: 需要在数字后面加上 deg、rad 或 turn 作为单位 */
    transform: rotate(degs);
    /* 定义 2D 转换, 元素的尺寸会增加或减小, 参数分别为在 x 轴和 y 轴上的变化程度 */
    transform: scale(x, y);
    transform: scaleX(x);
    transform: scaleY(y);
    /* 定义 2D 转换, 元素会围绕 x 轴和 y 轴翻转给定的角度 */
    transform: skew(x, y);
    transform: skewX(x);
    transform: skewY(y);
    /* 把所有 2D 转换方法组合在一起, 需要 6 个参数, 前四个参数描述线性变换的尺寸, 后两个参数描述如何应用这个变换 */
    transform: matrix(a, b, c, d, tx, ty);
    /* 定义 3D 转换, 表示初始视角在 z 轴上的坐标, 定义之后, 当元素在 z 轴上前后移动时, 会有透视效果的变化 */
    /* 注意: 这个距离是初始视角到 0 坐标的距离, 如果这个值是 0 或不合法的值, 则不会产生透视效果 */
    transform: perspective(z);
    /* 定义 3D 转换, 前两个参数照旧, 第三个参数表示当前视角离初始视角的距离, 正值表示沿 z 轴向后, 负值表示沿 z 轴向前 */
    /* 表现上, 第三个参数相当于对元素进行放大和缩小 */
    /* 注意: 第三个参数不能是百分数 */
    transform: translate3D(x, y, z);
    transform: translateZ(z);
    /* 定义 3D 转换, 定义元素绕 x 轴、y 轴、z 轴的旋转角度 */
    transform: rotate3D(x, y, z, degs);
    transform: rotateX(x);
    transform: rotateY(y);
    transform: rotateZ(z);
    /* 定义 3D 转换, 定义元素 3D 的缩放转换 */
    transform: scale3d(x, y, z);
    transform: scaleX(x);
    transform: scaleY(y);
    transform: scaleZ(z);
    /* 把所有 3D 转换方法组合在一起, 需要 16 个参数 */
    transform: matrix3D(a1, b1, c1, d1, a2, b2, c2, d2, a3, b3, c3, d3, a4, b4, c4, d4);
}
```
### 关于 z 轴以及 perspective(z) 的详解
下面是一个示例:
```css
.example {
    /* 初始视角在 50px 处 */
    transform: perspective(50px) translate3d(10px, 0px, 0px);
}
```
首先我们要明白, 所有的元素都和 z 轴原点在同一平面上。而我们的初始视角是由 `perspective(z)` 定义的。
当我们使用 `translate3D` 在 z 轴上移动视角时, 如果是正值, 看上去就像我们在靠近元素; 如果是负值, 看上去就像是在远离元素。

## box-shadow
`box-shadow` 的语法如下:
```css
.example {
    box-shadow: 阴影开始方向(默认是从里往外, 设置 inset 就是从外往里)  水平阴影的位置 垂直阴影的位置 模糊距离 阴影的大小 阴影的颜色;
}
```

`box-shadow` 是根据如下逻辑来进行渲染的:
* 给出两个、三个或四个数字值的情况:
    * 如果只给出两个值, 这两个值将被浏览器解释为 x 轴上的偏移量和 y 轴上的偏移量。正值阴影则位于元素右/下边, 负值阴影则位于元素左/上边。
    * 如果给出了第三个值, 这第三个值将被解释为模糊半径的大小  `blur-radius`。值越大, 模糊面积越大, 阴影就越大越淡。**不能为负值。**默认为 `0`。
    * 如果给出了第四个值, 这第四个值将被解释为扩展半径的大小  `spread-radius`。取正值时, 阴影扩大; 取负值时, 阴影收缩。默认为 `0`, 此时阴影与元素同样大。
* 可选, 阴影的开始方向。
* 可选, 颜色值。

以下这些写法都是合法的:
```css
.example {
    /* x偏移量 | y偏移量 | 阴影颜色 */
    box-shadow: 60px -16px teal;

    /* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影颜色 */
    box-shadow: 10px 5px 5px black;

    /* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 */
    box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);

    /* 插页(阴影向内) | x偏移量 | y偏移量 | 阴影颜色 */
    box-shadow: inset 5em 1em gold;
}
```

## border
`border` 是由 `border-width`、`border-style` 和 `border-color` 组成的简写属性。
**值得注意的是, 虽然 `border-width`、`border-style` 和 `border-color` 属性最多可以接受 4 个参数来为不同的边设置宽度、风格和颜色, 但 `boder` 属性只接受 3 个参数, 分别是宽度、风格和颜色, 所以这样会使得四条边的边框相同。**
下面逐条来介绍 `border` 相关的属性:
* `border-width`: 最多接受 4 个参数, 分别对应上、右、下、左四个边框的宽度。
* `border-style`: 最多接受 4 个参数, 分别对应上、右、下、左四个边框的样式。可能的值为 `none`、`hidden`、`dotted`、`dashed`、`solid`、`double`、`groove`、`ridge`、`inset` 以及 `outset`。
* `border-color`: 最多接受 4 个参数, 分别对应上、右、下、左四个边框的颜色。
* `border-image`: 使用外部图像来绘制边框。
    **使用 border-image 时, 其将会替换掉 `border-style` 属性所设置的边框样式。但是若 `border-image-source` 值为 `none` 或者图片不能显示, 则将应用 `border-style`。**
    `border-image` 是如下这些属性的简写属性:
    * `border-image-source`: 用于声明元素的边框图片资源。默认为 `none`。
    * `border-image-slice`: 指定 1 - 4 个值, 对应 4 条分割线, 将边框图片切成九宫格, 九宫格每一部分分别对应边框的一部分。默认为 `100%`。**注意: 当值为像素值时, 不要带 `px` 单位。**
    ```css
        .example {
            /* 以百分比值划分图片 */
            border-image-slice: 30% 30% 30% 30%;
            /* 以像素值划分图片 */
            border-image-slice: 27 27 27 27;
            /* 以像素值划分图片, 同时使用 fill 关键字, 将九宫格中间的部分作为 DOM 节点的背景 */
            border-image-slice: 27 27 27 27 fill;
        }
    ```

    * `border-image-width`: 定义图像边框宽度。默认为 `1`。**如果 `border-image-width` 大于已指定的 `border-width`, 那么它将向内部( padding/content )扩展。**
    ```css
        .example {
            /* 按 DOM 元素百分比值表示边框宽度 */
            border-image-width: 30%;
            /* 根据元素 border-width 属性, 用倍数表示边框宽度 */
            border-image-width: 1;
            /* 用像素值表示边框宽度 */
            border-image-width: 27px;
        }
    ```

    * `border-image-outset`: 定义边框图像可超出边框盒子的大小。默认为 `0`。
    * `border-image-repeat`: 定义图片如何填充边框。
    ```css
        .example {
            /* 拉伸图片以填充边框 */
            border-image-repeat: stretch;
            /* 平铺图片以填充边框 */
            border-image-repeat: repeat;
            /* 平铺图像, 当不能整数次平铺时, 根据情况放大或缩小图像 */
            border-image-repeat: round;
            /* 平铺图像, 当不能整数次平铺时, 会用空白间隙填充在图像周围(不会放大或缩小图像) */
            border-image-repeat: space;
        }
    ```

## background
`background` 是一个简写属性, 可以在一次声明中定义如下一个或多个属性:
* `background-clip`: 设置背景的延伸范围。
```css
.example {
    /* 背景延伸至边框外沿(但是在边框下层) */
    background-clip: border-box;
    /* 背景延伸至内边距(padding)外沿, 不会绘制到边框处 */
    background-clip: padding-box;
    /* 背景被裁剪至内容区(content box)外沿 */
    background-clip: content-box;
}
```
* `background-color`: 设置背景色。
* `background-image`: 为元素设置一个或多个背景图片。指定多个图片时, 中间用逗号隔开。
    **在绘制时, 图像以 z 方向堆叠的方式进行, 先指定的图像会在之后指定的图像上面绘制。**
* `background-origin`: 指定 `background-position` 属性应该是相对哪个位置。
    **注意如果背景图像 `background-attachment` 是 `fixed`, 这个属性没有任何效果。**
    ```css
    .example {
        /* 背景图片的摆放以 border 区域为参考 */
        background-origin: border-box;
        /* 背景图片的摆放以 padding 区域为参考 */
        background-origin: padding-box;
        /* 背景图片的摆放以 content 区域为参考 */
        background-origin: content-box;
    }
    ```
* `background-position`: 为每一个背景图片设置初始位置。这个位置是相对于由 `background-origin` 定义的位置图层的。
    **当有指定了多个背景图片时, 可以设定多个初始位置, 中间用逗号隔开即可。**
* `background-repeat`: 定义背景图像的重复方式。
```css
.example {
    /* 图像会重复来覆盖整个区域, 如果图像不合适的话, 会对最后一个图像进行裁剪 */
    background-repeat: repeat;
    /* 图像会尽可能重复来覆盖整个区域, 但不会裁剪, 而是会留白 */
    background-repeat: space;
    /* 图像会尽可能重复来覆盖整个区域, 如果图像不合适的话, 会对图像进行压缩和扩张 */
    background-repeat: round;
    /* 图像不会重复 */
    background-repeat: no-repeat;
}
```

* `background-size`: 设置背景图片的大小。
值可以是 `contain`、`cover` 以及宽度和高度值。当通过宽度和高度值来设定尺寸时, 如果仅有一个数值被给定, 这个数值将作为宽度值大小, 高度值将被设定为 `auto`。如果有两个数值被给定, 第一个将作为宽度值大小, 第二个作为高度值大小。
```css
.example {
    /* 宽高比不变的缩放整张图片来覆盖整个元素, 如果元素大小和图片大小不同, 则会裁剪图片 */
    background-size: cover;
    /* 宽高比不变的缩放整张图片以便能在元素中放下整个图片 */
    background-size: contain;
    /* 宽高值, 如果是百分比值, 则根据 background-origin 设置的背景区的宽高设置 */
    background-size: 20px;    
    background-size: 50%;
    background-size: 20px 100%;
}
```

* `background-attachment`: 设置背景图像的位置是在视口内固定, 还是随着包含它的元素一起滚动。
```css
.example {
    /* 背景相对于元素本身固定, 而不是随着它的内容滚动(对元素边框是有效的) */
    background-attachment: scroll;
    /* 背景相对于视口固定。即使一个元素拥有滚动机制, 背景也不会随着元素的内容滚动 */
    background-attachment: fixed;
    /* 背景相对于元素的内容固定。如果一个元素拥有滚动机制, 背景将会随着元素的内容滚动, 并且背景的绘制区域和定位区域是相对于可滚动的区域而不是包含他们的边框 */
    background-attachment: local;
}
```

## filter
`filter` 能够将模糊或颜色偏移等效果应用于元素, 通常用于调整图像、背景或边框的渲染。
```css
.example {
    /* url 函数接受一个 XML 文件 */
    /* 该文件设置了一个 SVG 滤镜, 且可以包含一个锚点来指定一个具体的滤镜元素 */
    filter: url('./svg#filter1');
    /* 给图片设置高斯模糊, 值越大越模糊, 默认为 0 */
    /* 这个参数可设置为 CSS 长度值, 但不接受百分比值 */
    filter: blur(5px);
    /* 调整图片亮度 */
    /* 值为 0% 图像会全黑, 值为 100% 图像无变化, 超过 100% 图像会更亮, 默认为 1 */
    filter: brightness(0.5);
    /* 调整对比度, 默认为 1 */
    filter: contrast(0.5);
    /* 为图像设置阴影效果, 类似于 box-shadow */
    filter: drop-shadow(16px 16px 10px black);
    /* 将图像转换为灰度图像 */
    /* 值为 100% 则完全转为灰度图像, 值为 0% 图像不变化, 默认为 0 */
    filter: grayscale(100%);
    /* 给图像应用色相旋转, 默认为 0 deg, 360 deg 图像无变化 */
    filter: hue-rotate(90deg);
    /* 反转输入图像的颜色 */
    /* 值为 100% 则为完全反转, 值为 0% 则无变化, 默认为 0 */
    filter: invert(100%);
    /* 改变图像透明程度, 默认为 1 */
    filter: opacity(50%);
    /* 改变图像饱和度, 默认为 1 */
    filter: saturate(200%);
    /* 将图像转变为深褐色, 默认为 0 */
    filter: sepia(100%);
    /* 可同时使用多个函数来增加多个滤镜, 中间用空格隔开 */
    filter: contrast(175%) brightness(3%);
}
```

## grid
通篇主要参考的是阮大的博客, 如果有不明白的地方可以看[原文](https://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)。
`grid` 布局的属性分为两类, 一类定义在容器上面, 称为`容器属性`; 另一类定义在项目上面, 称为`项目属性`。
### grid 容器属性
#### display
指定一个容器采用网格布局。
```css
.example {
    display: grid;
    display: inline-grid;
}
```
**注意, 设为网格布局以后, 容器子元素(项目)的 `float`、`display: inline-block`、`display: table-cell`、`vertical-align` 和 `column-*` 等设置都将失效。**

#### grid-template-columns 和 grid-template-rows
容器指定了网格布局后, 就要划分行和列。
```css
.example {
    display: grid;
    /* 可使用绝对单位, 也可以使用百分比 */
    /* 划分为三行, 每行高度为 100px */
    grid-template-rows: 100px 100px 100px;
    /* 划分为三列, 第一列宽度为 30%, 第二列宽度为 40%, 第三列为 30% */
    grid-template-columns: 30% 40% 30%;
}
```
除了绝对单位和百分比, 也可以使用函数和关键字来定义行和列。
* `repeat()`: 有时候重复写十分麻烦, 尤其网格很多时, 此时可以使用 `repeate()` 函数。
该函数接受两个参数, 第一个参数是重复的次数, 第二个参数是要重复的值。
```css
.example {
    display: grid;
    grid-template-columns: repeat(3, 33.33%);
    grid-template-rows: repeat(3, 33.33%);
    /* 也可以重复某种模式 */
    grid-template-columns: repeat(2, 100px 20px 80px);
    grid-template-rows: repeat(2, 200px 100px);
}
```

* `auto-fill`: 如果单元格的大小固定, 但容器大小不固定, 且希望每一行/列容纳尽可能多的单元格, 则可以使用 `auto-fill` 来进行自动填充。
```css
.example {
    display: grid;
    grid-template-columns: repeat(auto-fill, 100px);
}
```

* `fr`: 可以使用 `fr` 关键字(`fraction` 的缩写, 意为 "片段")来表示比例关系。
```css
.example {
    display: grid;
    /* 表示第二列的宽度是第一列的两倍 */
    grid-template-columns: 1fr 2fr;
}
```

* `minmax()`: 该函数产生一个长度范围, 表示长度就在这个范围内。
该函数接受两个参数, 分别表示最大值和最小值。
```css
.example {
    display: grid;
    /* 第三列宽度不小于 100px, 不大于 1fr */
    grid-template-columns: 1fr 1fr minmax(100px, 1fr);
}
```

* `auto`: 让浏览器自己决定长度。
```css
.example {
    display: grid;
    /* 第二列的宽度, 等于容器总宽度减去 200px */
    grid-template-columns: 100px auto 100px;
}
```

* 网格线的名称: 可以使用方括号, 指定每一根网格线的名字, 方便以后引用。
```css
.example {
    display: grid;
    grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
    /* 允许同一根线有多个名字 */
    grid-template-columns: [c1 c4] 100px [c2 c5] 100px [c3 c6];
}
```

#### row-gap、column-gap 和 gap
这三个属性用于设置单元格之间的间距。
这三个属性的详解:
* `row-gap`: 设置行与行的间距。
* `column-gap`: 设置列与列的间距。
* `gap`: 该属性是 `row-gap` 和 `column-gap` 的合并简写形式。

#### grid-template-areas
该属性用于划分区域, 每个区域在布局上是相连的, 一个区域由单个或多个单元格组成。
网格区块(`grid areas`)和网格项(`grid item`)没有关联, 但是它们可以和一些网格定位属性(`grid-placement properties`)关联起来, 比如 `grid-row-start`、`grid-row-end`、`grid-column-start` 和 `grid-column-end`; 也可以和一些速记(`shorthands`)属性关联起来, 比如 `grid-row`, `grid-column` 和 `grid-area`。
详情见 [`grid-template-areas` 的 MDN 页面](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-template-areas)
```css
.example {
    /* 这么写代码只是为了美观和易读, 其实只用加空格就好了 */
    /* 将九宫格划分为 3 个区域, 分别命名为 a - c */
    grid-template-areas: 'a a c'
                         'b b c'
                         'c c c';
    /* 如果某些区域不需要利用, 则使用"点"(.)表示 */
    grid-template-areas: 'a . c'
                         'd . f'
                         'g . i';
}
```

#### grid-auto-flow
该属性用于设置容器子元素向每个网格中的放置顺序。
```css
.example {
    /* 默认值。先行后列 */
    grid-auto-flow: row;
    /* 先列后行 */
    grid-auto-flow: column;
    /* 先行后列, 同时尽可能填满空白空间 */
    grid-auto-flow: row dense;
    /* 先列后行, 同时尽可能填满空白空间 */
    grid-auto-flow: column dense;
}
```

#### align-items、justify-items 和 place-items
* `align-items`: 设置容器子元素在单元格中的垂直位置。
* `justify-items`: 设置容器子元素在单元格中的水平位置。
* `place-items`: 为前两个属性的合并简写形式。

```css
.example {
    align-items: start | end | center | stretch;
    justify-items: start | end | center | stretch;
    place-items: <align-items> <justify-items>;
}
```

#### align-content、justify-content 和 place-content
* `align-content`: 设置整个内容区域在容器里面的垂直位置。
* `justify-content`: 设置整个内容区域在容器里面的水平位置。
* `place-content`: 为前两个属性的合并简写形式。

```css
.example {
    justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
    align-content: start | end | center | stretch | space-around | space-between | space-evenly;
    place-content: <align-content> <justify-content>;
}
```

#### grid-auto-columns 和 grid-auto-rows
这两个项目用于设置浏览器自动创建的多余网格的列宽和行高。

#### grid-template 和 grid
 * `grid-template`: 这个属性是 `grid-template-columns`、`grid-template-rows` 和 `grid-template-areas` 这三个属性的合并简写形式。
 * `grid`: 这个属性是 `grid-template-rows`、`grid-template-columns`、`grid-template-areas`、`grid-auto-rows`、`grid-auto-columns`、`grid-auto-flow` 这六个属性的合并简写形式。

### 项目属性
#### grid-column-start、grid-column-end、grid-row-start 和 grid-row-end
项目的位置是可以指定的, 具体方法就是指定项目的四个边框, 分别定位在哪根网格线。
* `grid-column-start`: 左边框所在的垂直网格线。
* `grid-column-end`: 右边框所在的垂直网格线。
* `grid-row-start`: 上边框所在的水平网格线。
* `grid-row-end`: 下边框所在的水平网格线。

```css
.example {
    grid-column-start: 2;
    grid-column-end: 4;
    /* 除了指定为第几个网格线外, 还可以指定为网格线的名字 */
    grid-column-start: header-start;
    grid-column-end: header-end;
    /* 还可以使用 span 关键字, 表示跨越多少个网格 */
    /* 下面的样式表示左边框距离右边框跨越 2 个网格 */
    grid-column-start: span 2;
}
```

使用这四个属性, 如果产生了项目的重叠, 则使用 `z-index` 属性指定项目的重叠顺序。

#### grid-column 和 grid-row
`grid-column` 属性是 `grid-column-start` 和 `grid-column-end` 的合并简写形式。
`grid-row` 属性是 `grid-row-start` 属性和 `grid-row-end` 的合并简写形式。
属性值之间要用"左斜杠" (`/`) 分开。

#### grid-area
该属性用于指定项目放在哪一个区域。

#### justify-self、align-self 和 place-self
* `justify-self`: 设置当前元素在单元格内的水平位置, 跟 `justify-items` 属性的用法完全一致, 但只作用于单个项目。
* `align-self`: 设置当前元素在单元格内的垂直位置, 跟 `align-items` 属性的用法完全一致, 也是只作用于单个项目。
* `place-self`: 该属性是 `align-self` 属性和 `justify-self` 属性的合并简写形式。

## box-sizing
简单来说, 该属性可以改变元素宽高的计算方式。
这个属性有以下几个值:
* `content-box`: 默认值。当设置为这个值时, 元素宽高只包含内容宽高, 不包含 `padding` 和 `border` 值。
* `border-box`: 当设置为这个值时, `border` 和 `padding` 值也会算入元素宽高之内。

## 媒体查询
用的不多, 就不讲了。详见[媒体查询 MDN 页面](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)。

## 文字
### word-wrap
`word-wrap` 用于说明当一个不能被分开的字符串太长而不能填充其包裹盒时, 为防止其溢出, 浏览器是否允许这样的单词中断换行。
**其实该属性更贴切的名字是 `overflow-wrap`, 目前已作为正式名称使用, `word-wrap` 作为该属性的别名。**
```css
.example {
    /* 默认值, 表示在正常的单词结束处换行 */
    word-wrap: normal;
    /* 表示如果行内没有多余的地方容纳该单词到结尾, 则那些正常的不能被分割的单词会被强制分割换行 */
    word-wrap: break-word;
}
```

### word-break
`word-break` 指定了如何在单词内断行。
```css
.example {
    /* 默认值, 使用默认的断行规则 */
    word-break: normal;
    /* 可在任意字符间断行 */
    word-break: break-all;
    /* 中日韩语言不断行, 其他语言表现和 normal 一样 */
    word-break: keep-all;
}
```

### text-overflow
确定当文本溢出容器时如何表现。
```css
.example {
    /* 默认值, 表现上类似于 overflow: hidden; */
    text-overflow: clip;
    /* 用省略号表示被截断的文本 */
    text-overflow: ellipsis;
    /* 用自定义字符串表示被截断的文本 */
    text-overflow: <string>;
}
```

### text-shadow
用于给文字加上阴影。
```css
.example {
    text-shadow: 水平阴影  垂直阴影  模糊距离  阴影的颜色;
}
```

## 颜色
提供了两种新的颜色表示方法。
* `rgba()`: 前三个参数为颜色值, 最后一个参数为透明度。
* `hsla()`: 第一个参数为色相, 第二个参数为饱和度, 第三个参数为亮度, 第四个参数为透明度。

## 选择器
![选择器](/images/interview-experence/01.jpg)

详情见[CSS 选择器参考手册](https://www.w3school.com.cn/cssref/css_selectors.asp)。

## 清浮动的几种方法
1. 为父元素设置固定高度(不推荐)。
2. 增加尾元素清除浮动。
![清浮动](/images/interview-experence/03.jpg)
`clear` 属性指定一个元素是否可以在它之前的浮动元素旁边, 或者必须向下移动(清除浮动)到这些浮动元素的下面。这就是清浮动的原理。
3. 为浮动元素创建父级 BFC。如为父元素添加 `overflow: hidden`。

## position
`position` 的常用值如下:
* `static`: 默认值。没有定位, 元素出现在正常的流中**(忽略 `top`, `bottom`, `left`, `right` 和 `z-index` 声明)**。
* `absolute`: 生成绝对定位的元素, 相对于 `static` 定位以外的第一个父元素进行定位。
* `fixed`: 生成绝对定位的元素, 相对于浏览器窗口进行定位。   
* `relative`: 生成相对定位的元素, 相对于其正常位置进行定位。
* `inherit` 规定从父元素继承 `position` 属性的值。

## display
`display` 的常用值如下:
* `none`: 缺省值。
* `block`: 默认值, 表示像块类型元素一样显示。
* `inline-block`: 像行内元素一样显示, 但其内容像块状元素一样显示。
* `list-item`: 块类型元素一样显示, 并添加样式列表标记。  

## px、em 与 rem
`px` 是最常见的单位, 1 px = 1 像素点。
`em` 与 `rem` 都是与 `font-size` 相关的长度单位。`em` 根据父元素的 `font-size` 进行换算。`rem` 根据根元素的 `font-size` 进行换算。
浏览器默认 `font-size` 为 `16px`。

## 常用布局
1. 水平居中: 子元素为行内元素 `inline` + 父元素设置 `text-align: center`。定宽的话就用 `margin: auto`, 不定宽的话就设置 `display: inline` 变成行内元素然后在处理。
2. 垂直居中: 子元素为行内元素, 父元素设置 `line-height` 与高度一致。子元素为块状元素, 子元素设置为绝对定位, 设置 `top` 和 `bottom` 均为 `0`, 父元素设置相对定位。
3. 单列布局: 略
4. 双列布局: 利用 `margin` 在左边或者右边留出一定空白, 然后让另一元素通过 `absolute` 或 `float` 浮动到另一侧, 注意要保证 `margin` 和另一元素宽度一样, 不然会有留白。
5. 自适应两列布局: 父元素固定宽度, 一个元素设置与父元素等宽, 另一元素设置浮动或定位, 宽度不定。这样看上去就是自适应的宽度布局。
5. 圣杯布局: 三列布局, 中间宽度自适应, 两边定宽, 增加父元素宽度, 中间元素宽度变化, 两侧元素不变。父元素定义左右 `padding` 值为左右两列元素的宽度。三元素都`float` 到同一方向, 并加上相对定位。之后定义左侧元素 `margin-left: -100%`, 定位 `left` 为该元素宽度的负值。中间元素定义宽度 `100%`, 右侧元素定义 `margin-right` 和定位 `right` 均为该元素宽度的负值。**注意给父元素清浮动, 且三元素的顺序为 `main` 在最前, 然后是 `left`, 然后是 `right`。**
6. 双飞翼布局: 双飞翼布局与三栏布局大部分一样, 三栏全部 `float` 浮动, 但左右两栏加上负 `margin` 让其跟中间栏 `div` 并排, 先中间后左右, 以形成三栏布局。但不同的是, 其在 `main` 里加了一个子 `div` 用于显示内容, 并通过设置该子 `div` 的 `margin` 值为两测两栏留出空白。父元素无需添加 `padding` 属性, 左右 `div` 也不需要加上相对定位。
7. `flex` 布局: 块状元素使用 `flex`, 行内元素使用 `inline-flex`。`flex` 盒子中, `float`、`clear`、`vertical-align` 失效。

# JavaScript
## 闭包
闭包是函数和声明该函数的词法环境的组合。在 js 中, 函数运行在它们被定义的作用域, 而不是它们被执行的作用域。
通俗点的说法, 定义一个返回值是函数 B 的函数 A , 函数 B 在执行时可以访问函数 A 内部的变量及参数, 而其他函数无法访问 A 内部的变量及参数。函数 B 就被称为`闭包`。
闭包封住了变量作用域, 有效防止了全局污染, 但同时也存在内存泄漏的风险。

## 原型链
就一张图就好。
![原型链](/images/interview-experence/02.jpg)

## Promise
todo
## async\await
todo
## this
一般有以下几种情况: 
* 如果是一般函数, 则其 this 指向其执行时所在的作用域。
* 如果是作为构造函数调用, 其 this 指向新创建的对象。
* 如果是作为对象的属性被调用时, 其 this 指向对象本身。
* 如果是箭头函数, 则其 this 指向定义时所在的、非箭头函数内的作用域。
* 如果在窗口直接调用, 非严格模式下, 其 this 指向 `window`; 严格模式下, 其 this 指向 `undefined`。
* 如果是在事件对象中, 则其 this 指向触发事件的 DOM 对象。

**对于箭头函数来说, bind、call、apply 不能改变其 this 指向。**
**多次调用 bind 函数, 其 this 会绑定到第一次调用 bind 时绑定的上下文。**

## apply\call\bind
关于 bind、call 和 apply:
* 三者的相同点是都会改变 this 指向, 且第一个参数都为新的 this 对象。
* 不同点是 bind 和 call 从第二个参数开始, 接收参数列表作为传入调用函数的参数, 而 apply 第二个参数为一个包含参数的数组。
* 还有一点不同是 bind 返回一个绑定 this 后的函数, 而 call 和 apply 都是直接返回函数在指定 this 对象下执行的结果。

## JS 的执行机制
简单来说, js 内的任务有两种, 一种是`同步任务`, 一种是`异步任务`。
![异步任务和同步任务](/images/interview-experence/04.jpg)
上面的图翻译一下, 就是:
* 同步任务和异步任务进入不同的执行场所, 同步任务进入 js 主线程, 异步任务进入`事件表(Event Table)`, 并注册回调函数。
* 当异步任务在事件表中执行完毕后, 便会将回调函数添加到`事件队列(Event Queue)`尾部。
* 当主线程把所有任务都执行完毕之后, 主线程便会去读取事件队列, 并从头部取出要执行的下一个任务, 并开始执行。
* 上述过程不断循环, 便是 js 的`事件循环(Event Loop)`机制

### setTimeout
`setTimeout` 这个函数, 是经过指定时间后, 把要执行的任务加入到事件队列中。然而因为是单线程任务要一个一个执行, 如果前面的任务需要的时间太久, 那么只能等着, 导致真正的延迟时间远远大于设定时间。
另外, **`setTimeout` 函数的最低延迟时间为 4 ms,** 设置 0 ms 是不会起作用的!

### setInterval
`setInterval` 和 `setTimeout` 差不多, 其本质是每次过 x 毫秒, 就会把一个任务放入事件队列中去。

### Promise 和 process.nextTick(cb)
`Promise` 不做赘述。
`process.nextTick(cb)` 类似于 nodejs 版的 setTimeout, 即在事件循环的下一次循环中调用 cb 函数。

前面说了当有新的异步任务时, 会进入事件队列。事实上, 异步任务是可以做进一步细分的, 有以下两种
* `宏任务(Macro Task)`: 包括整体代码 script、setTimeout、setInterval、setImmediate、I\O 操作以及页面渲染等。
* `微任务(Micro Task)`: 包括 Promise、process.nextTick 等。

其中, 不同的任务会进入不同的事件队列。宏任务会进入宏任务队列, 微任务会进入微任务队列。
应当注意的是, **`new Promise()` 内部的代码相当于同步任务, 会立即执行, 而其后面跟的 `then` 或 `catch` 内的代码会进入微任务队列中。**
由于存在两个任务队列, 因此上文我们对事件循环的描述便不那么准确了。真正的情况是这样的:
1. 当事件循环开始时(一般为整体代码开始执行), 主线程会首先执行宏任务队列中的第一个宏任务(即整体代码), 并将执行过程中所有的异步任务分为宏任务和微任务两类, 添加到对应的事件队列中去。
2. 当第一个宏任务执行完毕后, 主线程会读取微任务队列, 并执行其中所有的微任务。
3. 在所有微任务执行完毕后, 主线程会读取下一个宏任务, 并重复上述过程。

整个过程可以用这样图来解释:
![宏任务与微任务](/images/interview-experence/05.jpg)

## DOM
### DOM 中 Node 与 Element 的区别
简单来说, Node 是一个基类, DOM 中的 Text、Element 和 Comment 都继承于它。**这也意味着所有 Node 的方法和属性都能在 Element 上调用。**Node 代表一个比较大的范围, HTML 中的文本、元素、甚至注释都属于 Node(节点)。
Element 代表元素, 它只代表 HTML 标签。

### DOM 中 NodeList 与 HTMLCollection 的区别
`NodeList` 是节点集合(包括文本、图片等)。由 `Node.childNodes` 和 `document.querySelectorAll` 返回。其中 `Node.childNodes` 返回的是实时的, 另一个返回的是静态的。该集合同样可通过方括号语法访问。
`HTMLCollection` 是元素集合, 可通过方括号语法选中元素, 方括号中数字代表序号, 字符串代表 `id`。`document.forms`、`document.querySelectorAll` 等都返回 `HTMLCollection`。该集合是实时的。
应当注意的是, **`NodeList` 以及 `HTMLCollection` 都不是标准的数组, 而是伪数组。**

### 常用 DOM API
#### Node
```js
// 属性
// 节点信息
Node.nodeName // 返回节点名称, 只读
Node.nodeType // 返回节点类型的常数值, 只读
Node.nodeValue // 返回 Text 或 Comment 节点的文本值, 只读
Node.textContent // 返回当前节点和它的所有后代节点的文本内容, 可读写
Node.baseURI // 返回当前页面的绝对路径
// 节点列表
Node.ownerDocument // 返回当前节点所在的 document 对象
Node.nextSibling // 返回节点的下一个兄弟节点
Node.previousSibling // 返回节点的上一个兄弟节点
Node.parentNode // 返回节点的父节点
Node.childNodes // 返回节点的子节点列表
Node.firstChild // 返回节点的第一个子节点
Node.lastChild // 返回节点的最后一个子节点
// 元素节点列表
Node.children // 返回节点的元素节点列表
Node.firstElementChild // 返回节点的第一个元素子节点
Node.lastElementChild // 返回节点的最后一个元素子节点
Node.childElementCount // 返回节点的元素子节点个数

// 操作
Node.appendChild(node) // 向当前节点在子节点尾部添加节点
Node.cloneNode(true) // 克隆节点, 默认为 false, 只克隆节点, true的话会克隆节点及其属性和后代
Node.insertBefore(newNode, oldNode) // 在指定子节点之前插入新节点, 只能在父节点上操作
Node.removeChild(node) // 删除子节点, 只能在父节点上操作
Node.remove() // 删除当前节点
Node.before(...node) // 在当前节点之前插入一个或多个节点
Node.after(...node) // 在当前节点之后插入一个或多个节点
Node.replaceWith(node) // 使用指定节点替换当前节点
```
#### document
```js
// 属性
// 文档元素信息
document.defaultView // 返回当前文档所在的 window 对象
document.documentElement // 返回文档的根节点
document.body // 返回文档的 body 元素
document.head // 返回文档的 head 元素
document.activeElement // 返回文档当前被聚焦的元素
document.links // 返回文档中所有 a 元素
document.forms // 返回文档中所有 form 元素
document.images // 返回文档中所有 img 元素
document.scripts // 返回文档中所有 script 元素
document.styleSheets // 返回文档中所有样式表
// 文档信息
document.cookie // 返回当前文档的 cookie
document.title // 返回文档的 title
document.documentURI // 返回当前文档的网址
document.URL // 返回当前文档的网址
document.domain // 返回当前文档的域名
document.location // 返回 location 对象, 提供当前文档的 URL 信息

// 方法
// 读写方法
document.open() // 新建并打开一个文档对象
document.close() // 关闭 document.open 打开的文档对象
document.write() // 向 document.open 打开的文档对象中写入内容
// 查找节点
document.querySelector(selectors) // 返回第一个匹配的元素节点
document.querySelectorAll(selectors) // 返回所有匹配的元素节点
document.getElementsByTagName(tagName) // 返回所有指定 HTML 标签名的元素节点
document.getElementsByClassName(className) // 返回所有指定类名的元素节点
document.getElementsByName(name) // 返回所有指定 name 属性值的元素节点
document.getElementById(id) // 返回拥有指定 id 的元素节点
document.elementFromPoint(x, y) // 返回位于页面指定位置最上层的元素的子节点
// 生成节点
document.createElement(tagName) // 创建有指定标签名的元素节点
document.createTextNode(text) // 创建文本节点
document.createAttribute(name) // 创建 attribute 对象
document.createDocumentFragment() // 创建 DocumentFragment 对象
// 事件方法
document.createEvent(type) // 创建指定类型的 event 对象
document.addEventLinstener(type, linstener, capture) // 在指定元素上注册事件
document.removeEventLinstener(type, linstener, capture) // 移除指定元素上的事件监听器
document.dispatchEvent(event) // 触发事件
```
#### Element
```js
// 属性
// attribute 相关属性
Element.attributes // 获取包含当前元素所有属性的列表
Element.id // 返回当前元素的 id
Element.tagName // 返回当前元素标签名
Element.innerHTML // 返回当前元素包含的 HTML 代码
Element.outerHTML // 返回当前元素自身及其子代的 HTML 代码
Element.className // 返回元素 class 属性的属性值
Element.classList // 返回包含当前元素所有 class 属性值的列表
Element.dataset // 返回当前元素中所有 data-* 属性
Element.style // 返回当前元素的行内样式
// 尺寸属性
// offset
// 返回元素包含边框和滚动条在内的宽高(width/height + padding + border + 滚动条)
Element.offsetHeight
Element.offsetWidth
// 如果是一般元素, 返回元素相对于文档的坐标, 如果是定位元素, 返回相对于祖先元素的坐标
Element.offsetLeft
Element.offsetTop
// client
// 与 offset 类似, 返回元素宽高, 但不包括边框和滚动条(width/height + padding)
Element.clientHeight
Element.clientWidth
// 返回元素的内边距的外边缘和他的边框的外边缘的水平距离和垂直距离, 通常这些值就等于左边和上边的边框宽度
Element.clientLeft
Element.clientTop
// scroll
// 这两个属性是元素的内容区域加上内边距, 再加上任何溢出内容的尺寸, 如果没有溢出时, 这些属性与 clientWidth 和 clientHeight 是相等的。
Element.scrollHeight
Element.scrollWidth
// 这两个属性可写, 用于指定滚动条的位置
Element.scrollLeft 
Element.scrollTop

// 节点相关属性
Element.children // 返回当前元素所有子元素
Element.childElementCount // 返回当前元素子元素节点的个数
Element.firstElementChild // 返回当前元素第一个子元素节点
Element.lastElementChild // 返回当前元素最后一个子元素节点
Element.nextElementsSibling // 返回当前元素下一个兄弟元素节点
Element.previousElementSibling // 返回当前元素上一个兄弟节点元素
Element.offsetParent // 返回当前元素节点的最靠近的、并且 position 属性不等于 static 的父元素

// 方法
// 位置方法
Element.getBoundingClientRect() // 返回元素大小及其相对于视口的位置
// 属性方法
Element.getAttribute(name) // 获取指定属性值
Element.setAttribute(name, value) // 设置属性值
Element.removeAttribute(name) // 移除属性值
Element.hasAttribute(name) // 返回布尔值, 判断元素是否有某个属性
// 查找方法
Element.querySelector(selector) // 返回第一个匹配的子元素节点
Element.querySelectorAll(selector) // 返回所有匹配的子元素节点
Element.getElementsByTagName(name) // 返回所有拥有指定标签名的子元素节点
Element.getElementsByClassName(name) // 返回所有拥有指定类名的子元素节点
// 事件方法
Element.addEventLinstener(type, linstener, capture) // 为当前元素注册事件
Element.removeEventListener(type, linstener, capture) // 移除当前元素上的事件监听器
Element.dispatchEvent(event) // 触发事件
// 其他
Element.scrollIntoView() // 将窗口滚动至当前元素可见的区域
Element.insertAdjacentHTML(where, HTMLString) // 在指定位置添加 HTML 节点
Element.insertAdjacentHTML('beforeBegin', HTMLString) // 在该元素前插入  
Element.insertAdjacentHTML('afterBegin', HTMLString) // 在该元素第一个子元素前插入 
Element.insertAdjacentHTML('beforeEnd', HTMLString) // 在该元素最后一个子元素后面插入 
Element.insertAdjacentHTML('afterEnd', HTMLString) // 在该元素后插入
Element.remove() // 将当前元素从文档中移除
Element.focus() // 聚焦当前元素
```
关于 `Element.getBoundingClientRect()`, 该方法返回一个包含 `left`、top、right、bottom、width 和 height 的对象, 单位为 `px`。计算方法如下:
![Element.getBoundingClientRect()方法的返回值](/images/interview-experence/06.png)

### 如何修改页面内容?
假设目前 HTML 如下:
```html
<ul id='container'></ul>
```
需要向其中添加 3 个 `li` 元素, 如何添加?
todo
### 如何绑定事件?
绑定事件有三种方法:
1. 使用内联
2. 使用 `.on` 的方式进行绑定
3. 使用事件监听 `addEventLinstener` 进行绑定

#### 内联
语法如下:
```html
<p onclick='func()'><p>
```
不管是普通函数还是箭头函数, 事件处理函数执行时, this 都指向 window(非严格模式)或 undefined(严格模式)。也就是说, **原函数中可用的变量和常量在事件处理器中同样可用。**
这种语法附加的监听器可以被使用 `.on` 语法附加的监听器覆盖。

#### .on 语法
语法如下:
```js
document.getElementById('btn').onclick = function () {
    // ...
}

// 或者
// 注意函数名后面不要加括号
document.getElementById('btn').onclick = func;
```
当绑定的函数是普通函数时, 事件处理函数在执行时 this 指向触发事件的元素; 当绑定的函数是箭头函数时, 事件处理函数在执行时 this 指向 window(非严格模式)或 undefined(严格模式)。
因此, **使用箭头函数时, 原函数中可用的变量和常量在事件处理器中同样可用。**

#### addEventLinstener()
以上两种方法都有一个很严重的弊端, 那就是**同一个事件只能绑定一个事件处理函数, 如果绑定了多次, 则后绑定的事件处理函数会覆盖之前绑定的事件处理函数。**
而 `addEventLinstener()` 则不然, MDN 对它的工作原理描述如下:
> `addEventListener()` 的工作原理是将实现 `EventListener` 的函数或对象添加到调用它的 `EventTarget` 上的指定事件类型的事件侦听器列表中。

从上面的描述可以看出, `addEventLinstener()` 不会覆盖任何绑定的事件, 哪怕是通过内联语法或 `.on` 绑定的事件。
`addEventLinstener()` 的语法如下:
```js
target.addEventListener(type, listener[, options]);
target.addEventListener(type, listener[, useCapture]);

target.addEventListener('click', function () {
    // ...
});
target.addEventListener('click', func);
```
着重需要描述的是 `options` 对象, 它是一个有关 `linstener` 的可选参数对象, 可用的选项如下:
* `capture`: Boolean, 表示, 表示 `listener` 会在该类型的事件捕获阶段传播到该 EventTarget 时触发。
* `once`: Boolean, 表示 `listener` 在添加之后最多只调用一次。如果是 true,  `listener` 会在其被调用之后自动移除。
* `passive`: Boolean, 设置为 true 时, 表示 `listener` 永远不会调用 `preventDefault()`。如果 `listener` 仍然调用了这个函数, 客户端将会忽略它并抛出一个控制台警告。

和 `.on` 事件绑定语法一样, 当绑定的函数是普通函数时, 事件处理函数在执行时 this 指向触发事件的元素; 当绑定的函数是箭头函数时, 事件处理函数在执行时 this 指向 window(非严格模式)或 undefined(严格模式)。
因此, **使用箭头函数时, 原函数中可用的变量和常量在事件处理器中同样可用。**
另外, 还有一点非常重要, **`addEventLinstener()` 对任何 DOM 元素都是有效的, 而不仅仅只对 HTML 元素有效。**

### 当数据量变大之后, 如何进行优化?
### DOM 树的遍历


## 跨域问题
### 为什么会有跨域问题?
这就要说到浏览器`同源策略`了。
处于对保护敏感信息的考虑, 避免当前网站通过 `Cookie` 等手段获取到别的网站保存的敏感用户信息, 浏览器设置了同源策略, 具体就是:
JavaScript 在发送请求时, URL 的域名必须和当前页面完全一致。
完全一致是指域名要相同(如 www.baidu.com 和 baidu.com 是不同的), 协议要相同(http 和 https 不同), 端口要相同。

### 跨域请求的四种方法
#### Flash (废弃)

#### 在同源域名下架设一个代理服务器转发(需额外开发)
JavaScript 负责将请求发送到代理服务器, 之后代理服务器发送请求后再把结果返回。

#### JSONP
本质上这种跨域请求是利用了浏览器允许跨域引用 JavaScript 资源。
```html
<head>
    <script src='http://file'>
        // 该文件内部包含一函数调用
        // jsonp(trueData)();
    </script>
    <script>
        // 在该处进行声明上面的函数, 并在函数内部调用进行数据处理
        function jsonp(data) {
            // ...
        }
    </script>
</head>
```
`JSONP` 方法的缺点是限制只能用 `GET` 请求, 并要求返回 JavaScript。这种方法要求编写页面的人对后端的数据返回有很全面的了解才能对返回的数据有正确的处理, 这实际上是很不必要的, 也违背了前后端分离的原则。

#### CORS
全称是 `Cross-Origin Resource Sharing`, 是最常用的跨域策略, 只要浏览器支持 HTML5 就能用。
前端部分和普通 Ajax 请求无任何区别, 浏览器会自动进行信息补全。
后端部分是实现 CORS 的重点, 只有服务器实现了 CORS 接口, 才能跨源通信。

##### CORS的两种请求
* 简单请求(同时满足以下两条件):
 1. 请求方法是 `HEAD`、`GET`、`POST` 的三者之一。
 2. HTTP头信息不超出以下几种字段: `Accept`、`Accept-Language`、`Content-Language`、`Last-Event-ID`、`Content-Type`、`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`。

* 非简单请求: 不是简单请求的就是非简单请求。

##### 简单请求
简单来说, 简单请求就是简单请求方法和简单HTTP头信息的结合。
在 JavaScript 向外域发送一个请求后, 浏览器收到响应后会先检查响应的 `Access-Control-Allow-Origin` 是否包含本域。若包含则请求成功, 若不包含则浏览器会报错, 且 JavaScript 收不到任何响应。
**简单请求其实就是表单请求。**表单请求一直都可以发起跨域请求。
**综上所述, 简单跨域请求能否成功的关键在于让对方的服务器返回一个正确的 `Access-Control-Allow-Origin`。**
##### 非简单请求
基本流程简述
先发送`预检请求`, 询问服务器当前网页能不能发送 CORS 请求。预检请求的请求方法是 `OPTIONS`。
如果服务器同意请求, 则服务器会返回一个包含了 `Access-Control-Allow-Origin` 等字段的响应, 浏览器收到响应以后检查对应字段, 确认允许跨源请求, 就可以做出回应。
如果服务器否定请求, 则服务器会返回一个不包含任何 CORS 相关字段的响应, 或者明确表示请求不符合条件。之后浏览器会抛出一个错误, 可被 `XMLHttpRequest` 对象的 `onerror` 回调函数捕获。
一旦服务器通过了预检请求, 以后每次浏览器发送 CORS 请求, 就都跟简单请求一样, 会有一个 `Origin` 头信息字段。服务器的回应, 也都会有一个 `Access-Control-Allow-Origin` 头信息字段。

#### 参考链接
* [http://www.ruanyifeng.com/blog/2016/04/cors.html](http://www.ruanyifeng.com/blog/2016/04/cors.html)
* [http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
* [http://javascript.ruanyifeng.com/bom/cors.html](http://javascript.ruanyifeng.com/bom/cors.html)

## 正则
### 正则表达式字符匹配攻略
* 正则表达式就是匹配模式, 要么匹配字符, 要么匹配位置。
* 以 `/\d{2, 5}/g` 为例, 贪婪匹配指其会尽可能多的匹配数字, 比如有 6 个数字, 那我就要 5 个, 有 3 个就要 3 个, 反正越多越好, 追求范围的上限。
* 以 `/\d{2, 5}?/g` 为例, 惰性匹配是指尽可能少的匹配, 只要你有 2 个数字, 匹配就结束了, 一个都不多要, 追求范围的下限。
* 分支结构是惰性的, 如果前面的模式匹配上了, 就不会再尝试后面的模式。
* 常用字符组:
 * `\d`: 即 `[0-9]`。表示是一位数字。记忆方式: 其英文是 `digit`(数字)。
 * `\D`: 即 `[^0-9]`。表示除数字外的任意字符。
 * `\w`: 即 `[0-9a-zA-Z_]`。表示数字、大小写字母和下划线。记忆方式: `w` 是 `word` 的简写, 也称单词字符。
 * `\W`: 是`[^0-9a-zA-Z_]`。非单词字符。
 * `\s`: 是 `[ \t\v\n\r\f]`。表示空白符, 包括空格、水平制表符、垂直制表符、换行符、回车符、换页符。记忆方式: `s` 是 `space character` 的首字母。
 * `\S`: 是 `[^ \t\v\n\r\f]`, 非空白符。
 * `.`: . 即 `[^\n\r\u2028\u2029]`。通配符, 表示几乎任意字符。换行符、回车符、行分隔符和段分隔符除外。记忆方式: 想想省略号`...`中的每个点, 都可以理解成占位符, 表示任何类似的东西。
 * 匹配任意字符: 可以使用 `[\d\D]`、`[\w\W]`、`[\s\S]` 和 `[^]` 中任何的一个。

* 常用量词:
 * `{m, }`: 表示至少出现 m 次。
 * `{m}`: 等价于 `{m, m}`, 表示出现 m 次。
 * `?`: 等价于 `{0, 1}`, 表示出现或者不出现。记忆方式: 问号的意思表示, 有吗?
 * `+`: 等价于 `{1,}`, 表示出现至少一次。记忆方式: 加号是追加的意思, 得先有一个, 然后才考虑追加。
 * `*`: 等价于` {0,}`, 表示出现任意次, 有可能不出现。记忆方式: 看看天上的星星, 可能一颗没有, 可能零散有几颗, 可能数也数不过来。

* 惰性匹配量词: `{m, n}?`、`{m, }?` 、`??`、`+?` 和 `*?`。

### 正则表达式位置匹配攻略
* 正则表达式的位置匹配, 匹配的是字符之间的空间。
* 可以把位置理解为空字符, 字符串开头、结尾以及字符与字符之间都可以有不止一个空字符。
* 问题: 为什么?
```js
// ',1,2,3,4,5,678'
var result = "12345678".replace(/(?=(\d{3})+)/g, ',');
// '12,345,678'
var result = "12345678".replace(/(?=(\d{3})+$)/g, ',');
```
* 常用位置匹配字符:
 * `^`: 
 * `$`: 
 * `\b`: 
 * `\B`: 
 * `(?=p)`: 
 * `(?!p)`: 

### 正则表达式括号的作用
* 括号提供了分组, 以便于我们之后引用它们。
* 通过分组, 我们可以使用 `String.prototype.match()` 以及 `RegExp.prototype.exec()` 来提取数据。
* `String.prototype.match()` 和 `RegExp.prototype.exec()` 的返回值是一个数组, 第一个元素是整体匹配结果, 之后的元素是各个分组(正则表达式中括号内)匹配的内容, 然后是匹配下标, 最后是输入的文本。
```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
console.log( string.match(regex) ); 
// => ["2017-06-12", "2017", "06", "12", index: 0, input: "2017-06-12"]
```
* 还有一种引用分组的方式, 是通过 `RegExp` 对象的全局属性 `$1` 到 `$9` 来引用。
```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";

regex.test(string); // 正则操作即可, 例如
//regex.exec(string);
//string.match(regex);

console.log(RegExp.$1); // "2017"
console.log(RegExp.$2); // "06"
console.log(RegExp.$3); // "12"
```
* 替换字符可以这么写:
```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, "$2/$3/$1");
console.log(result); 
// => "06/12/2017"
```
其中, `$1`、`$2` 和 `$3` 就分别代表了第一、二、三个分组。

* 反向引用是指在正则表达式中引用之前出现过的分组。形式为 `\x`, 其中 `x` 为需要引用的分组的标号, 需要引用第一个分组就是 `\1`, 需要引用第二个分组就是 `\2`, 需要引用第十个分组就是 `\10`,以此类推。
* 如果有括号嵌套的情况, 则根据开括号出现的顺序来界定分组的顺序。
```js
var regex = /^((\d)(\d(\d)))\1\2\3\4$/;
var string = "1231231233";
console.log( regex.test(string) ); // true
console.log( RegExp.$1 ); // 123
console.log( RegExp.$2 ); // 1
console.log( RegExp.$3 ); // 23
console.log( RegExp.$4 ); // 3
```
* 反向引用引用的是前面的分组, 如果该分组不存在, 那么正则表达式匹配字符本身。例如 `\3`, 就匹配 `\3` 转义后对应的字符。

### 正则表达式回溯法原理
* 回溯法的基本思想是: 从问题的某一状态出发, 探索从此状态能够到达的所有状态。当一条路径走到尽头时, 回退一步或若干步, 从另一种可能的状态出发, 继续搜索, 直到所有路径都试探过为止。
* **回溯法的本质就是深度优先搜索算法。**
* 在正则表达式匹配的过程中, 如果尝试匹配失败, 那么下一步通常就是回溯。
* 在 js 中, 能够触发回溯的一般有三种: `贪婪量词`, `惰性量词` 以及 `分支结构`。

### 正则表达式的读取
* 拆分时, `|` 管道符的优先级最低, 最后拆分。
* 所有元字符都尽量转义。

### 正则表达式的构建
* 首先, 要认识到正则的有限性, 并不是所有问题都能用正则来解决。其次, 要认识到很多问题并不需要正则也能解决, 不要把问题复杂化。
* 当一个正则表达式过于复杂时, 可以尝试按分组将其拆分成若干小的正则表达式。
* 在构建正则表达式之前, 分别写出各种情况的正则, 然后用分支将它们合并在一起, 再提取分支公共部分, 就能得到准确的正则。
* 至于优化, 一般不怎么能用得到, 除非太过反人类。

### 正则表达式编程
* 操作正则表达式的方法有六种: `String.prototype.search()`、`String.prototype.split()`、`String.prototype.match()`、`String.prototype.replace()`、`RegExp.prototype.test()` 以及 `RegExp.prototype.exec()`。
* `String.prototype.search()` 和 `String.prototype.match()` 会把传入的字符串转换为正则。
* `String.prototype.match()` 返回结果的格式, 与正则对象是否有修饰符 `g` 有关。没有 `g`, 返回的是标准匹配格式。有 `g`, 返回的是所有匹配的内容。当没有匹配时, 不管有无 `g`, 都返回 `null`。
```js
var string = "2017.06.27";
var regex1 = /\b(\d+)\b/;
var regex2 = /\b(\d+)\b/g;
console.log( string.match(regex1) );
console.log( string.match(regex2) );
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => ["2017", "06", "27"]
```

* `RegExp.prototype.exec()` 会接着上一次匹配后继续匹配。正则的实例属性 `lastIndex` 表示下一次匹配开始的位置。
```js
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => 4
// => ["06", "06", index: 5, input: "2017.06.27"]
// => 7
// => ["27", "27", index: 8, input: "2017.06.27"]
// => 10
// => null
// => 0
```

* 对于 `RegExp.prototype.exec()` 方法和 `RegExp.prototype.test()` 方法而言, 当正则是全局匹配时, 每完成一次匹配, 都会修改 `lastIndex` 属性。

* 对于 `String.prototype.replace()` 来说, 第二个参数既可以是字符串, 也可以是函数。
当第二个参数是字符串时, 一些字符有特殊意义:
 * `$1`, `$2`, ... , `$99` 匹配第 1 ~ 99个 分组里捕获的文本。
 * `$&` 匹配到的子串文本。
 * `$\`` 匹配到的子串的左边文本。
 * `$'` 匹配到的子串的右边文本。
 * `$$` 美元符号。

### 参考资料
* [JS 正则表达式完整攻略](https://juejin.im/post/5965943ff265da6c30653879)

## toString() 和 valueOf()
当一个**对象**需要进行数学运算时, 一般会发生如下类型转换:
1. 如果是原始值, 则直接返回原始值。
2. 调用 `valueOf` 方法, 如果返回的是基础类型, 则返回, 否则继续。
3. 调用 `toString` 方法, 如果返回的是基础类型, 则返回, 否则继续。
4. 如果两者都没返回基础类型, 则报错。

## 在 Javascript 中什么是伪数组? 如何将伪数组转化为标准数组?
伪数组是指类数组对象, 最常见的就是字符串。
伪数组是指无法直接调用数组方法或期望 `length` 属性有什么特殊的行为, 但仍可以对真正数组遍历方法来遍历它们。
典型的是函数的 `arguments` 参数, 还有像调用 `getElementsByTagName`, `document.childNodes` 之类的, 它们都返回的`NodeList` 对象都属于伪数组。
可以使用 `Array.prototype.slice.call(fakeArray)` 将数组转化为真正的 `Array` 对象。

## 常用工具函数的实现
### bind、call、apply
todo
### 深复制
todo
### 双向绑定
todo
## Promise
todo
## 防抖与节流
### 防抖
防抖的原理就是: 当一个事件被触发时, 在 n 秒后执行事件处理函数。如果这段等待的时间内, 事件又被触发了, 则以新的事件时间为准。总之就是, 等触发完事件 n 秒内不再触发事件, 才执行一次事件处理函数。
```js
function debounce(func, time) {
    let timeout;

    return function () {
        let context = this;
        let result;
        let args = Array.from(arguments)

        clearTimeout(timeout);
        timeout = setTimeout(function() {
            result = func.apply(context, args);
        }, time)

        return result;
    }
}
```

### 节流
节流的原理就是: 如果你持续触发事件, 每隔一段时间, 只执行一次事件。
```js
// 使用时间戳
function throttle(func, wait) {
    let previous = 0;

    return function() {
        let now = +new Date();
        let context = this;
        let args = Array.from(arguments);

        if(now - previous > wait) {
            func.apply(context, args);
            previous = now;
        }
    }
}

// 使用定时器
function throttle(func, wait, ...args) {
    let timeout;
    let previous = 0;

    return function() {
        context = this;

        if(!timeout) {
            timeout = setTimeout(function() {
                timeout = null;
                func.apply(context, args)
            }, wait)
        }
    }
}
```

## js 中的堆内存与栈内存
首先我们要明白, js 中的变量可以分为两大类: 一种是`简单值`, 又称为`基本数据类型`, **储存时储存的是值,** 有如下 6 种:
1. Sting
2. Number
3. Boolean
4. null
5. undefined
6. Symbol

一种是`复杂值`, 又称为`引用数据类型`, 对象、函数、数组都属于这种类型, **储存时储存的是地址的引用。**
https://juejin.im/post/5d116a9df265da1bb47d717b

## 前端路由
todo
https://juejin.im/post/5d2d19ccf265da1b7f29b05f#heading-7

# React
## diff算法
### diff 策略
React 总的 diff 策略如下:
1. 对于 DOM 的跨层级移动操作特别少, 可以忽略不计。
2. 拥有相同类名的两个组件会形成相似的树形结构; 拥有不同类名的两个组件会生成不同的树形结构。
3. 对于同一层级的一组子节点, 通过唯一 id 进行区分。

下面我们来看看以上策略在 tree diff、component diff 以及 element diff 上的应用。
todo
### 
## React Fiber
首先我们要了解到, 要插入一万个 dom 节点, 有两种方式, 一种是插入一万次一个节点, 一种是插入一百次一百个节点。第二种方法更好, 因为这样浏览器便有时间进行代码优化和合并执行。
jsx是嵌套的天然结构, 在 react 15 中被翻译为递归执行的代码, 因此称 react 15 的调度器为栈调度器。栈调度器代码量少, 浅显易懂, 但缺点是不能随意 break、continue。
react 16 的调度器将 jsx 翻译为链表结构, 将组件的递归更新改成链表的依次执行。如果页面有有多个虚拟 dom 树, 则将它们的根保存在一个数组中。
react 从上到下大致有三层, 一层为虚拟 dom 层, 只负责描述结构与逻辑, 第二次为内部组件层, 负责更改 state, 执行生命周期函数, 第三层为底层渲染层, 针对不同环境有不同的渲染器。
react 16 将内部组件层改为 fiber 这种数据结构。每个 fiber 有三个属性, return、child 和 sibling。return 指向父节点, child 指向第一个子节点, sibling 指向它右边的兄弟节点。
### 更新过程
在 react 15 中, 每次更新时, 都是从根组件或者 setState 后的组件开始, 更新整个子树。我们唯一能做的, 便是在某个节点中使用 shouldComponentUpdate 断开某一部分的更新, 或是优化 shouldComponentUpdate 的比较效率。
react 16 则是将虚拟 dom 转化为 fiber 节点, 再将 fiber 节点转化为组件实例或真实 dom。它会规定一个时间段, 在这个时间段内, 能转化多少个 fiber 节点, 就更新多少个 fiber 节点。
todo

## React 组件通信
todo
  
## 容器组件和展示组件的区别
`展示组件`是指专门用于展示数据的组件, 内部不含任何业务逻辑。
`容器组件`是指用于控制展示组件行为, 内部含有业务逻辑的组件。

# Vue

# 计算机网络
## 浏览器缓存
缓存分为两种, 一种为`强缓存`, 一种为`协商缓存`。
浏览器第一次请求发生后再次请求时, 会先获取该资源缓存的 header 信息, 根据缓存的 header 信息判断是否命中强缓存, 若命中则直接返回缓存文件, 本次请求不会与服务器发送通信, 但状态码为200。
若没有命中强缓存, 浏览器会发送请求到服务器, 请求会携带资源缓存的 header 中的一些信息, 再由服务器自行判断相关信息是否命中协商缓存, 若命中则返回 304, 返回新的 header 信息以更新缓存中的 header 信息, 并告知浏览器可直接从缓存中获取该资源。否则返回最新的资源内容。

### 强缓存
与强缓存相关的字段有两个。
1. `expires`: 是 http1.0 的标准, 其值为一 GMT 格式的时间字符串, 若发送请求的时间在 expires 之前, 那么本地缓存有效, 否则就会发送请求到服务器来获取资源。
2. `cache-control`: 是 http1.1 的标准。主要是利用该字段的 max-age 值来判断, 该值加上第一次资源请求的时间计算出一个资源有效时间。若发送时间在该资源有效时间之内, 则本地缓存有效。否则发送请求。`cache-control` 还有几个常用的值。
 1. `max-age`: 是一个数字, 用于配合资源请求时间计算资源过期时间。
 2. `no-cache`: 不使用本地缓存, 需使用协商缓存。先与服务器通信确认资源是否被修改, 若之前的响应中存在 ETag(服务器生成的资源的唯一标识, 资源更改, ETag 也更改), 则请求时会与服务器验证资源是否被修改, 若资源未更改, 则可避免重新下载。
 3. `no-store`: 禁用浏览器缓存数据, 每次请求该资源都需要重新发送请求。
 4. `public`: 该资源可被所有用户缓存, 包括CDN中间代理服务器和终端用户。
 5. `private`: 只允许终端用户缓存, 不允许CDN等中级缓存器对其进行缓存。
注意: 若两字段同时存在, `cache-control` 的优先级高于 `expires`。

### 协商缓存
协商缓存涉及到的字段主要有两组(四个), 这两组都是成对出现的, 即第一次请求头带上某个字段, 后续的请求会带上相对应的请求字段。
1. `Last-Modified`、`If-Modified-Since`: 两者均为GMT格式的时间字符串。第一次请求返回的响应头中含有 `Last-Modified` 字段, 表示该资源在服务器上最后的修改时间。之后的请求同一资源时, 头部中会含有 `If-Modified-Since` 字段, 其值与 `Last-Modified` 一致。服务器收到请求后, 将资源的最后修改时间与 `If-Modified-Since` 做比较, 若修改过, 则返回最新的资源, 并在响应头中更新 `Last-Modified` 的时间。否则即为协商缓存, 返回 304, 浏览器从缓存中加载资源。
2. `Etag`、`If-None-Match`: 这两个值为服务器生成的每个资源的唯一标识符, 只要资源有变化就改变这个值。判断流程与上一组类似, 只不过是通过 `Etag` 判断资源是否被修改。不同的是, 当服务器返回 304 时, 响应头中还会包含 `Etag`, 哪怕资源没有变化。
注意: `Last-Modified` 与 `Etag` 共存的情况下, `Etag` 的优先级高于 `Last-Modified`。

## 三次握手四次挥手
### 三次握手
三次握手是建立 TCP 连接的过程, 具体如下:
1. 客户端向服务器发送一 `SYN 报文段(SYN segment)`, **该报文段不包含数据。**客户端会把随机选择的`初始序号(client_isn)`放在报文段的序号字段中。
2. **在收到 SYN 报文段后, 服务器会为该连接分配缓存及变量。**之后服务器会返回 `SYNACK 报文段(SYNACK segment)`。该报文段 SYN 位被置为1, 确认号为 `client_isn + 1`, 同时服务器会随机选择`初始序号(server_isn)`, 放入报文段的序号字段中。**此报文段仍然不包含数据。**
3. **在收到 SYNACK 报文段后, 客户端会为该连接分配缓存及变量。**客户端会发送最后一个报文段来告知服务器连接已建立。在该报文段中, 首部的确认字段为 `server_isn + 1`。**在该报文段中可以携带数据。**

### 四次挥手
四次挥手是拆除 TCP 连接的过程, 具体如下:
1. 客户端发送`终止报文段(FIN segment)`, 该报文段的 FIN 位被置为 1。
2. 服务器向客户端发送一个该报文段的确认报文段。
3. 服务器向客户端发送`终止报文段(FIN segment)`。
4. **客户端向服务器发送一个该报文段的确认报文段, 同时释放资源。服务器在收到确认后, 也释放资源。**

## 从输入 url 到显示页面发生了什么?
todo
## cookie、sessionStorage 和 localStorage 有什么区别?
cookie 是后端通过 `Set-Cookie` 字段设置的, 

## 常用 HTTP 请求方法
| 方法 | 描述 |
| -- | -- |
| GET | 请求指定的页面信息, 并返回实体主体 |
| HEAD | 类似于 GET 请求, 只不过返回的响应中没有具体的内容, 用于获取报头 |
| POST | 向指定资源提交数据进行处理请求(例如提交表单或者上传文件)。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和 / 或已有资源的修改 |
| PUT | 从客户端向服务器传送的数据取代指定的文档的内容 |
| DELETE | 请求服务器删除指定的页面 |
| OPTIONS | 用于获取服务器所支持的通信选项, 比如方法、允许请求的域名等 |
| TRACE | 回显服务器收到的请求, 主要用于测试或诊断 |

## 常用状态码
状态码有五个主要的分类:

| 分类 | 分类描述 |
| - | ---- |
| 1** | 信息, 服务器收到请求, 但需要请求者继续操作|
| 2** | 成功, 操作被成功接收并处理|
| 3** | 重定向, 需要进一步执行操作已完成请求|
| 4** | 客户端错误, 请求语法错误或无法完成请求|
| 5** | 服务器错误, 服务器在处理请求的过程中出现了错误|

状态码具体列表:

|状态码|描述|
| - | ---- |
|100| 继续。客户端应继续其请求 |
|101| 应当切换协议 |
|200| 请求成功。一般用于 GET 和 POST 请求 |
|201| 已创建。成功请求并创建了新的资源|
|202| 已接受。已经接受请求, 但未处理完成|
|203| 非授权信息。请求成功, 但返回的 meta 信息不在原始的服务器, 而是一个副本 |
|204| 无内容。服务器成功处理, 但未返回内容, 在未更新网页的情况下, 可确保浏览器继续显示当前文档 |
|205| 重置内容。服务器处理成功, 用户终端应重置文档视图, 可通过此返回码清除浏览器的表单域 |
|206| 部分内容.服务器成功处理了部分 GET 请求 |
|300| 多种选择。请求的资源可包括多个位置, 相应可返回一个资源特征与地址的列表用于用户终端选择 |
|301| 永久移动。请求的资源已被永久的移动到新 URI, 返回信息会包括新的 URI, 浏览器会自动定向到新 URI。今后任何新的请求都应使用新的 URI 代替 |
|302| 临时移动。与 301 类似, 但资源只是临时被移动, 客户端应继续使用原有 URI |
|303| 查看其它地址。与 301 类似。使用GET和POST请求查看 |
|304| 未修改。所请求的资源未修改, 服务器返回此状态码时, 不会返回任何资源。客户端通常会缓存访问过的资源, 通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源 |
|305| 使用代理。所请求的资源必须通过代理访问 |
|306| 已经被废弃的HTTP状态码 |
|307| 临时重定向。与302类似。使用GET请求重定向 |
|401| 客户端请求的语法错误, 服务器无法理解|
|403| 请求要求用户的身份认证 |
|404| 服务器理解请求客户端的请求, 但是拒绝执行此请求 |
|405| 服务器无法根据客户端的请求找到资源 |
|500| 服务器内部错误, 无法完成请求 |
|501| 服务器不支持请求的功能, 无法完成请求 |
|502| 作为网关或者代理工作的服务器尝试执行请求时, 从远程服务器接收到了一个无效的响应 |
|503| 由于超载或系统维护, 服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的 Retry-After 头信息中 |
|504| 充当网关或代理的服务器, 未及时从远端服务器获取请求 |
|505| 服务器不支持请求的 HTTP 协议的版本, 无法完成处理 |

## 内网穿透
todo 如何进行前后端联调?
# 算法

# Node

# 其他
## Linux 操作系统相关
todo 软连接

## 前端性能优化
todo
参考资料 https://www.cnblogs.com/xianyulaodi/p/5755079.html

## 思考题
1. 有 64 匹赛马, 8 条赛道, 每匹赛马速度恒定, 每次比赛只知道名次, 不知道具体时间。最少比赛多少次才能找出前三名?
答: 至少需要 11 次。首先先分为 8 组, 比赛 8 次。之后把每组的第一名放在一起比一次, 找出所有赛马里的第一名。之后将第一名所在的分组里的第二名和其他组的第一名放在一起比一次, 找出第二名。之后再用相同的方法找出第三名。总共比赛了 8 + 3 = 11 次。
2. 实现函数, 输入给定的 k 和 n, 给出 x ^ k = n 中的 x 值( k, n 均大于 0)。
todo

# Java
1. 使用静态属性必须以类名做前缀?
2. 线性表中利用( )存储方式最省时间? 
3. 在有n个结点的二叉链表中,值为非空的链域的个数为__________。
4. Java语言中,不考虑反射机制,一个子类显式调用父类的构造器必须用__________关键字。