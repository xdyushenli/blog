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

### 三列布局
三列布局主要指左右宽度固定, 中间自适应的三栏布局方案。这里假设左右宽度均为 100px。

```html
<div class='parent'>
  <div class='left'></div>
  <div class='right'></div>
  <div class='center'></div>
</div>
```

```css
/* 定位1 */
.parent {postion: relative};
.left {position: absolute; left: 0; width: 100px};
.right {position: absolute; right: 0; width: 100px};
.center {postion: absolute; left: 100px; right: 100px};

/* 定位2 */
.parent {postion: relative};
.left {position: absolute; left: 0; width: 100px};
.right {position: absolute; right: 0; top: 0; width: 100px};
.center {margin: 0 100px 0 100px};

/* 浮动 */
.parent {overflow: hidden;}
.left {float: left; width: 100px;}
.right: {float: right; width: 100px;}

/* 表格 */
.parent {dispaly: table; width: 100%;}
.left {display: table-cell; width: 100px;}
.center {display: table-cell;}
.right {display: table-cell; width: 100px;}

/* 弹性 */
.parent {display: flex;}
.left {width: 100px;}
.center {flex: 1;}
.right {width: 100px;}

/* 网格 */
.parent {
  display: grid; 
  width: 100%; 
  grid-template-rows: 100px; 
  grid-template-columns: 100px auto 100px;
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
* `inherit`: 规定从父元素继承 `position` 属性的值。
* `sticky`: 可以看出是 `position: relative` 和 `position: fixed` 的结合体。当元素在屏幕内, 表现为 `relative`, 就要滚出显示器屏幕的时候, 表现为 `fixed`。具体的可以看[这个](https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/)。

### 绝对定位与 float 的区别?
* 浮动的框可以向左或向右移动, 直到它的外边缘碰到包含框或另一个浮动框的边框为止。由于浮动框不在文档的普通流中, 所以文档的普通流中的块框表现得就像浮动框不存在一样。**使用 `float` 脱离文档流时, 父元素内的其他盒子会无视这个元素, 但父元素内的文本, 以及父元素内其他盒子内的文本依然会为这个元素让出位置, 环绕在周围。**
* 设置为绝对定位的元素框从文档流完全删除, 并相对于其包含块定位, 包含块可能是文档中的另一个元素或者是初始包含块。元素原先在正常文档流中所占的空间会关闭, 就好像该元素原来不存在一样。元素定位后生成一个块级框, 而不论原来它在正常流中生成何种类型的框。**对于使用 `position: absolute` 脱离文档流的元素, 其他盒子与其他盒子内的文本都会无视它。**

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

## 原型链以及实现原型继承的方式
关于原型链的描述, 只需要一张图就好。

![原型链](/images/interview-experence/02.jpg)

实现原型继承的方法有很多, 有组合继承、构造函数继承、原型式继承、寄生组合继承等。**最好的实现方式是寄生组合继承:**

```js
function Parent(name) {
    // ...
    this.name = name;
}
function Child(name, age) {
    Parent.call(this, name);
    // ...
    this.age = age;
}

Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child
```

## ES6 部分 
### var、let 和 const 的区别
这个是个很简单的问题, 只说一说容易忘记的部分吧:
`const` 在定义变量的时候必须赋值, 而 `let` 和 `var` 则不必。

### Promise
Promise 有以下几个特点, 我们一一来看看。

#### Promise 的立即执行性
Promise 对象表示未来某个将要发生的事件。但是在创建 Promise 对象时, 作为 Promise 参数传入的函数会立即执行。

```js
let p = new Promise((resolve, reject) => {
    console.log(1);
    resolve(2);
})

console.log(3);

p.then((val) => {
    console.log(val);
})

console.log(4);

// 打印结果: 1 3 4 2
```

#### Promise 的三种状态
Promise 其实本质是一个状态机, 有三种状态: `pending`、`resolved` 以及 `rejected`。
`pending` 状态能改变为 `resolved` 或 `rejected` 状态, 且一经改变后便不能再次改变。**这种状态的改变是不可逆的。**

#### 链式调用
Promise 支持链式调用, 用于链式调用的有三种方法:
* `then` 方法: Promise 实例具有 `then` 方法。它的作用是为 Promise `实例添加状态改变时的回调函数。then` 方法的第一个参数是 `resolved` 状态的回调函数, 第二个参数(可选)是 `rejected` 状态的回调函数。
* `catch` 方法: **该方法是 `then(null, rejection)` 或 `then(undefined, rejection)` 的别名,** 用于指定发生错误时的回调函数。
* `finally` 方法: 该方法用于指定不管 Promise 对象最后状态如何, 都会执行的操作。

以上三个方法最终的返回值都是 Promise 对象。
在链式调用的过程中, 传入作为参数的函数的返回值被用作创建 Promise 对象, 而这些函数的返回值可以是以下三种情况的一种:
* 同步值或 `undefined`: 这种情况下, `then` 方法将会返回一个状态为 `resolved` 的 Promise 对象, 且 Promise 对象的值即为返回值。
* 另一个 Promise 对象: 在这种情况下, `then` 方法将根据这个 Promise 对象的状态和值创建一个新的 Promise 对象返回。
* 使用 `throw` 抛出的异常: `then` 方法返回一个 `rejected` 状态的 Promise 对象, 值是异常。这种抛出的异常会被 `catch` 方法和 `then` 方法的第二个参数捕获到。

#### resolve() 和 reject()
```js
let p1 = new Promise(function (resolve, reject) {
    resolve(Promise.resolve('resolve'));
});

let p2 = new Promise(function (resolve, reject) {
    resolve(Promise.reject('reject'));
});

let p3 = new Promise(function (resolve, reject) {
    reject(Promise.resolve('resolve'));
});

p1.then(
    function fulfilled(value) {
        console.log('fulfilled: ' + value);
    },
    function rejected(err) {
        console.log('rejected: ' + err);
    }
);

p2.then(
    function fulfilled(value) {
        console.log('fulfilled: ' + value);
    },
    function rejected(err) {
        console.log('rejected: ' + err);
    }
);

p3.then(
    function fulfilled(value) {
        console.log('fulfilled: ' + value);
    },
    function rejected(err) {
        console.log('rejected: ' + err);
    }
);

// 控制台输出:
// p3 rejected: [object Promise]
// p1 fulfilled: resolve
// p2 rejected: reject
```

Promise 回调函数的第一个参数 `resolve` 会对传入的参数执行"拆箱"的操作。**当 `resolve` 的参数是一个 Promise 对象时, `resolve` 会"拆箱"获取这个 Promise 对象的状态和值, 但这个过程是异步的!**
但 Promise 回调函数中的第二个参数 `reject` 不具备"拆箱"的能力, **`reject` 的参数会直接传递给 then 方法的 `rejected` 回调, 无论其中的参数是不是 Promise 对象。**

#### 错误捕获
一般来说, 可以用 `catch` 方法和 `then` 方法的第二个参数来进行错误捕获, 并对错误进行处理。应当注意的是, **错误一经捕获并处理后, `then` 或 `catch` 返回的后续 Promise 对象将恢复正常, 为 `resolved` 状态, 并会被 Promise 执行成功的回调函数处理。**

### Generator
传统的编程语言, 对于异步编程有一种解决方案, 称为`协程(coroutine)`。意思是多个线程互相协作, 完成异步任务。
其运行流程大致如下:
> 第一步, 协程 A 开始执行。
>
> 第二步, 协程 A 执行到一半, 进入暂停, 执行权转移到协程 B。
>
> 第三步, (一段时间后)协程 B 交还执行权。
>
> 第四步, 协程 A 恢复执行。

`Generator` 函数是协程在 ES6 的实现, **最大的特点就是可以交出函数的执行权(即暂停执行)。**
剩下的注意事项有三点:
1. `Generator` 函数定义时要使用 `function*` 关键字来进行定义。
2. 在 `Generator` 函数内部, 可以使用 `yield` 关键字来交出当前代码的执行权, 阻塞剩余代码的运行。
3. 要想让剩余代码继续运行, 需要调用 `next` 方法。`next` 方法的作用是分阶段执行 `Generator` 函数。该方法返回一个包含 value 属性和 done 属性的对象, value 是 `yield` 关键字后面表达式的值, done 是一个布尔值, 表示 `Generator` 函数是否执行完毕。

### async\await
async 函数会将任何返回值都包装为 Promise 对象(**哪怕没有使用 return 关键字!**), 这使得 **async 函数支持链式调用。**
**await 关键字只能在 async 函数内部使用。**
await 关键字可以放在任何异步或同步代码之前。

#### await 关键字
当代码执行到 await 关键字时, 就会阻塞当前函数剩下代码的运行, 将运行权交给 await 关键字后面的代码。此时有两种情况:
1. 后面的代码返回的是 Promise 对象。
await 关键字会阻塞函数内剩余代码的执行, 直到 Promise 对象 fulfilled 为止, 并将 Promise 中传入 resolve 的值作为 await 表达式的结果返回。
如果 await 后面的 Promise 的状态是 rejected, 且没有 then 和 catch 来捕捉这一错误, 则这个错误会被抛出, 从而终止代码的运行。
如果 Promise 对象有 then 方法, 则会执行完 then 方法内的代码, 并将其 return 的值作为 await 表达式的结果返回。
2. 后面的代码返回的不是 Promise 对象。await 依然会阻塞函数内剩余代码的执行, 会优先执行 await 后面的代码, 并将后面代码的返回值作为 await 表达式的结果返回。

以上行为可以通过如下四段代码展现:
1. 第一段代码, await 后面跟着的是一个状态最终为 resolved 的 Promise 对象。
```js
async function asyncTask() {
    let result = await task1();
    console.log('await 表达式的结果是: ' + result);
    return result;
}

// resolve 的 Promise 对象
function task1() {
    return new Promise((resolve, reject) => {
        console.log('task 1');
        resolve(1);
    })
}

asyncTask()
.then((val) => {
    console.log('最终的返回值是: ' + val)
})
// 最终输出结果如下:
// task 1
// await 表达式的结果是: 1
// 最终的返回值是: 1
```

2. 第二段代码, await 后面是一个最终状态为 rejected 的 Promise 对象。
```js
async function asyncTask() {
    let result = await task2();
    console.log('await 表达式的结果是: ' + result);
    return result;
}

// rejected 的 Promise 对象
function task2() {
    return new Promise((resolve, reject) => {
        console.log('task 2');
        reject(2);
    })
}

asyncTask()
.then((val) => {
    console.log('最终的返回值是: ' + val)
})
// 最终输出结果如下:
// task 2
// Uncaught (in promise) 2
```
最终会抛出错误。

3. 第三段代码, await后面跟着的是一个调用了 then 方法的 Promise 对象。
```js
async function asyncTask() {
    let result = await task3();
    console.log('await 表达式的结果是: ' + result);
    return result;
}

// 调用了 then 方法的 Promise 对象
function task3() {
    return new Promise((resolve, reject) => {
        console.log('task 3');
        // 注意这里是 reject, 调用的是 then 方法的第二个参数
        reject(3);
    }).then((val) => {
        console.log('then 方法的第一个参数');
        return 4
    }, (val) => {
        console.log('then 方法的第二个参数');
        return 5
    })
}

asyncTask()
.then((val) => {
    console.log('最终的返回值是: ' + val)
})
// 最终输出结果如下:
// task 3
// then 方法的第二个参数
// await 表达式的结果是: 5
// 最终的返回值是: 5
```
无论是 then、catch 还是 finally 方法, 最终的结果都是一样的, 有异曲同工之妙, 自己理解就好。

4. 第四段代码, await 后面跟着的是一个同步任务。
```js
async function asyncTask() {
    let result = await task4();
    console.log('await 表达式的结果是: ' + result);
    return result;
}

// 同步任务
function task4() {
    console.log('同步任务');
    return 6;
}

asyncTask()
.then((val) => {
    console.log('最终的返回值是: ' + val)
})
// 最终输出结果如下:
// 同步任务
// await 表达式的结果是: 6
// 最终的返回值是: 6
```

#### async 函数与同步任务
async 函数本身的行为很好理解, 但是当在 async 函数之前执行一些同步代码时, 就不那么好理解了。
让我们把第一段代码稍加改动一下, 来预测下输出:
```js
async function asyncTask() {
    let result = await task1();
    console.log('await 表达式的结果是: ' + result);
    return result;
}

// resolve 的 Promise 对象
function task1() {
    return new Promise((resolve, reject) => {
        console.log('task 1');
        resolve(1);
    })
}

console.log('before async 函数');

asyncTask()
.then((val) => {
    console.log('最终的返回值是: ' + val)
})

console.log('after async 函数');
```
你觉得上面的代码会输出什么呢? 答案是:

> before async 函数
> task 1
> after async 函数
> await 表达式的结果是: 1
> 最终的返回值是: 1

是不是有点迷惑? 为什么 async 函数执行到 task1 函数的时候, 明明返回了 Promise 对象, 但是却没有继续向下执行 async 函数剩下的代码呢?
首先我们来看一段相似的代码:

```js
console.log(1);

(new Promise((resolve, reject) => {
    console.log(2);
    resolve(4)
})).then((val) => {
    console.log(val)
})

console.log(3);

// 最终输出结果:
// 1 2 3 4
```
输出结果是不是很相似呢? 现在就到了揭示秘密的时刻啦。
为了解释 async 函数的奇怪行为, 我们需要看一段话:

> 对于 async 函数而言:
> 在 await 之前的代码都是同步执行的, **可以理解为 await 之前的代码属于 `new Promise()` 时传入的代码。**
> **而 await 之后的所有代码都是在 `Promise.then()` 中的回调。**

再结合宏任务、微任务以及浏览器事件循环的原理, 我们再来看看上面的代码:
首先, 浏览器会读取整个文件, 这属于同步任务, 是最高优先级的, 因此会先执行输出 `before async 函数`。
之后开始执行 `asyncTask` 函数。进入该函数后, 开始执行 `task1` 函数。由于其返回的是 Promise 对象, 且在新建 Promise 时, 传入 Promise 的代码是立即执行的, 因此会输出 `task 1`。
之后, 浏览器会把 `asyncTask` 剩余的函数作为微任务, 放入微任务队列, **但并不会立即执行。**同样的, 浏览器会把 then 方法中传入的代码放入微任务队列, **且也不会立即执行。**
在此之后, 浏览器便执行到了最后一行, 此时会输出 `after async 函数`。
所有宏任务执行完毕, 开始清空微任务队列。由于先放入队列的是 `asyncTask` 函数的剩余代码, 因此会先输出 `await 表达式的结果是: 1`, 最后再输出 `最终的返回值是: 1`。到这里, 整段代码也就执行完毕了。

### Set 和 WeakSet 的区别
### Map 和 WeakMap 的区别
### Class
### ES Module
### Proxy
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
todo

## 跨域问题
### 为什么会有跨域问题?
这就要说到浏览器`同源策略`了。
处于对保护敏感信息的考虑, 避免���前网站通过 `Cookie` 等手段获取到别的网站保存的敏感用户信息, 浏览器设置了同源策略, 具体就是:
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
 * `.`: `.` 即 `[^\n\r\u2028\u2029]`。通配符, 表示几乎任意字符。换行符、回车符、行分隔符和段分隔符除外。记忆方式: 想想省略号`...`中的每个点, 都可以理解成占位符, 表示任何类似的东西。
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
* 回溯法的基本思想是: 从问题的某一状态出发, 探索从此状态能够到达的所有状态。当一条路径走到尽头时, 回退一步或若干步, 从另一种可能的状态出发, 继续搜索, 直到所有���径都试探过为止。
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
* `null` 和 `undefined` 没有以上两个方法。
* `toString`: 值类型时返回自身的字符串形式; 当是引用类型时, 如果是数组, 则会先将其转化为一维数组, 在根据如下规则进行转换: `null` 和 `undefined` 转为空字符串 `''`, 对象转为 `[object Object]`, 函数原样返回字符串形式。
* `valueOf`: 无论是值类型还是引用类型, 大部分情况下都是原样返回。当是 `Date` 类型时, 返回时间戳。
* **在进行字符串操作的时候, `toString` 会优先于 `valueOf`; 在进行数值运算时, `valueOf` 会优先于 `toString`。**
* 当执行 `toString` 的变量是一个数字类型时, 支持传参, 表示需要转为多少进制的字符串。

## typeof 和 instanceof
这两个方法常用于判断变量的类型, 只不过有如下区别:
* `typeof` 对于基础类型除了 `null` 以外(显示 `object`)都可以显示正确的类型, 对于数组和对象都会显示 `object`, 对于函数会显示 `function。`
* `instanceof` 主要是用来判断引用类型, 它的原理是根据原型链来查找。

除了这两种方法外, 还可以使用 `Object.prototype.toString.call(variable)` 来更加准确的判断某个元素的类型。

## 在 Javascript 中什么是伪数组? 如何将伪数组转化为标准数组?
伪数组是指类数组对象, 最常见的就是字符串。
伪数组是指无法直接调用数组方法或期望 `length` 属性有什么特殊的行为, 但仍可以对真正数组遍历方法来遍历它们。
典型的是函数的 `arguments` 参数, 还有像调用 `getElementsByTagName`, `document.childNodes` 之类的, 它们都返回的`NodeList` 对象都属于伪数组。
可以使用 `Array.prototype.slice.call(fakeArray)` 将数组转化为真正的 `Array` 对象。

## 如何判断一个对象是否可为 iterable 对象, 即可迭代对象?
很简单, 只要检测对象的 `Symbol.iterator` 是否为一个函数就可以了。
```js
function isIterable(obj) {
    return typeof obj['Symbol.iterator'] === 'function';
}
```

## 字符串 slice、substring、substr 的区别
* `slice` 接收两个参数, 第一个参数为起始下标, 第二个为末尾的下标, 前闭后开。**下标为负数时, 表示从末尾往前数。**
* `substring` 接收两个参数, 较小的参数为起始下标, 较大的参数为末尾下标, 前闭后开。**下标为负数时, 表示 0。**
* `substr` 接收两个参数, 第一个参数为起始下标, 第二个参数为需要截取的字符串个数。**第二个参数不能为负数。第一个参数为负数时, 表示从末尾往前数。**

## == 和 ===
两者的区别如下:
* `===` 会判断两边变量的类型是否完全相等。
* `==` 存在变量类型转换的问题, **并不推荐使用。**只用一种情况会被使用, `variable == null` 是 `variable === undefined || variable === null` 的简写, 其余情况一律使用 `===`。

**值得注意的是, `===` 并不完全可靠。**例如:

```js
0 === -0; // true
NaN === NaN; // false
```

判断两个变量是否完全相等, 可以使用 `Object.is` 方法, 这是 ES6 新增的 API。

```js
Object.is(0, -0); // false
Object.is(NaN, NaN); // true
```

另外, **除了 `undefined`、`null`、`false`、`NaN`、`''`、`0`、`-0` 以外的值在类型转换中会被转换为 `true`,** 应当额外注意。

## 常见浏览器安全问题及防范措施
todo

## 常用工具函数的实现
### bind、call、apply
核心思路是, **当某个函数是某个对象的属性时, 调用该函数, 该函数内部的 this 便会指向那个对象。**为了实现这一目标, 要分四步实现:
1. 调用 call、apply 或 bind 的对象不是函数, 此时应该抛出错误。
2. 调用时没有传入第一个参数, 此时应该自动把其执行域绑定到 window。
3. 把函数放入需要绑定的上下文对象中, 执行完毕后从上下文对象中删除该函数, 然后返回函数的执行结果。
4. 如果是 bind , 要特别小心, 因为其返回的函数可以作为构造函数来调用, 因此**要利用 instance 判断返回的函数是否被作为构造函数。**

注意, **Symbol 的使用是必须的,** 这是为了避免当传入的第一个参数中有重名的属性 (fn) 时会对其进行覆盖。
具体代码如下:
```js
// apply / call
Function.prototype.apply2 = function (context, args = []) {
    if(!(this instanceof Function)) {
        throw new TypeError('Error')
    }

    context = context || window;

    let fn = Symbol('fn')

    context[fn] = this;
    
    let result = context[fn](...args);
    delete context[fn];
    return result;
}

Function.prototype.call2 = function (context, ...args) {
    if (!(this instanceof Function)) {
        throw new TypeError('Error')
    }

    context = context || window;

    let fn = Symbol('fn')

    context[fn] = this;
    
    let result = context[fn](...args);
    delete context[fn];
    return result;
}

// bind
Function.prototype.bind2 = function (context, ...args) {
    if (!(this instanceof Function)) {
        throw new TypeError('Error')
    }

    context = context || window;

    // this 指代调用 bind2 的函数
    const that = this;

    return function F() {
        if(this instanceof F) {
            return new F();
        } else {
            return that.apply(context, args.concat(arguments));
        }
    }
}
```

### 深复制
属性值分三种情况考虑: 简单值的话就直接赋值, 对象的话就递归调用, 数组的话则判断每个元素的类型, 然后进行复制。
```js
function deepCopy(obj) {
    let keys = Object.keys(obj);
    let copy = {};

    for(let key of keys) {
        let currentVal = obj[key];

        if(Array.isArray(currentVal)) {
            copy[key] = copyArr(currentVal);
        } else if(typeof currentVal == 'object') {
            copy[key] = deepCopy(currentVal);
        } else {
            copy[key] = currentVal;
        }
    }

    return copy;
}

function copyArr(arr) {
    let copy = [];

    for(let item of arr) {
        if(Array.isArray(item)) {
            copy.push(copyArr(item))
        } else if(typeof item == 'object') {
            copy.push(deepCopy(item));
        } else {
            copy.push(item);
        }
    }

    return copy;
}
```

### 双向绑定
有两种实现方法, 一种是使用 ES5 的 Object.defineProperty 进行拦截; 另一种是使用 ES6 的 Proxy 进行拦截。
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <script>
        // ES5: 利用 Object.defeinProperty 进行拦截
        const obj = {};

        function changeInput(e) {
            obj.value = e.target.value;
        }

        Object.defineProperty(obj, 'value', {
            get: () => {
                return e.target.value;
            },
            set: (newValue) => {
                document.getElementById('text').innerHTML = newValue;
            }
        })

        // ES6: 使用 Proxy 进行拦截
        // 从功能上来说, obj 完全可以去掉。之所以保留是因为 obj 代表了数据模型(Model)。
        const obj = {};

        function changeInput(e) {
            proxy.value = e.target.value;
        }

        const proxy = new Proxy(obj, {
            get: (target, key, receiver) => {
                return target[key];
            },
            set: (target, key, value, receiver) => {
                if(key === 'value') {
                    document.getElementById('text').innerHTML = value;
                    obj[key] = value;
                }
            },
        })
    </script>
    <input id='input' type="text" onkeyup="changeInput(event)">
    <p id='text'></p>
</body>
</html>
```

### Promise
```js
function Promise(fn) {
    if (typeof fn !== 'function') {
        throw new TypeError(`Promise resolver ${fn} is not a function`)
    }

    this.status = 'pending';
    this.value = null;

    this._onResolvedFns = [];
    this._onRejectedFns = [];
    this._promiseList = [];

    function _clearFnQueue(fnQueue, promiseList) {
        let executor = null;
        let currentPromise = null;

        while (fnQueue.length > 0) {
            executor = fnQueue.shift();
            currentPromise = promiseList.shift();

            // executor 为函数
            if (typeof executor === 'function') {
                let value = null;
                try {
                    value = executor();
                } catch (err) {
                    currentPromise.status = 'rejected';
                    currentPromise.value = err;
                    // 如果执行出错, 影响的应该是后面 then 的执行, 而不是数组中其他 fn 的执行
                    // 肯定是得广度优先执行, 队列中的函数应提前绑定好 this 以及 value 值
                    // 执行错误的时候, 绑定的应该是下级中的 onRejectedFns
                    while (currentPromise._promiseList.length > 0) {
                        let nextPromise = currentPromise._promiseList.shift();
                        let nextRejectedFns = currentPromise._onRejectedFns;

                        // 将下一层 then 调用所需的 Promise 放入 promiseList 中
                        promiseList.push(nextPromise);

                        while (nextRejectedFns.length > 0) {
                            let fn = nextRejectedFns.shift();
                            fnQueue.push(fn.bind(currentPromise, currentPromise.value));
                        }
                    }
                }

                currentPromise.status = 'resolved';
                currentPromise.value = value;

                // 肯定是得广度优先执行, 队列中的函数应提前绑定好 this 以及 value 值
                while (currentPromise._promiseList.length > 0) {
                    let nextPromise = currentPromise._promiseList.shift();
                    let nextResolvedFns = currentPromise._onResolvedFns;

                    // 将下一层 then 调用所需的 Promise 放入 promiseList 中
                    promiseList.push(nextPromise);

                    while (nextResolvedFns.length > 0) {
                        let fn = nextResolvedFns.shift();
                        fnQueue.push(fn.bind(currentPromise, currentPromise.value));
                    }
                }
            } else {
                continue;
            }
        }
    }

    function resolve(value) {
        this.status = 'resolved';
        this.value = value;
        let context = window || undefined;

        // 将 onResolvedFns 队列中的函数绑定上 this 对象以及传入的 value 值
        for (let i = 0; i < this._onResolvedFns.length; i++) {
            let fn = this._onResolvedFns[i];
            this._onResolvedFns[i] = fn.bind(context, value);
        }

        _clearFnQueue.call(this, this._onResolvedFns, this._promiseList);
    }

    function reject(err) {
        this.status = 'rejected';
        this.value = err;
        let context = window || undefined;

        // 将 onRejectedFns 队列中的函数绑定上 this 对象以及传入的 value 值
        for (let i = 0; i < this._onRejectedFns.length; i++) {
            let fn = this._onRejectedFns[i];
            this._onRejectedFns[i] = fn.bind(context, err);
        }

        _clearFnQueue.call(this, this._onRejectedFns, this._promiseList);
    }

    try {
        fn(resolve.bind(this), reject.bind(this));
    } catch (err) {
        throw (err);
    }
}

// 返回新对象, 新对象状态根据之前 Promise 执行的结果来进行判断
Promise.prototype.then = function (onFulfilled, onRejected) {
    if (onFulfilled === undefined) {
        onFulfilled = val => val;
    }
    if (onRejected === undefined) {
        onRejected = err => err;
    }

    // 用于保存要执行的函数
    let executor = null;

    if (this.status === 'pending') {
        this._onResolvedFns.push(onFulfilled);
        this._onRejectedFns.push(onRejected);
        // 返回新的 Promise 对象, 挂载在 _promiseList 上
        let nextPromise = new Promise(() => { })
        this._promiseList.push(nextPromise);
        return nextPromise;
    } else if (this.status === 'resolved') {
        // 如果前面的 Promise 状态为 resolved, 且 onFulfilled 不为函数, 则应该返回和前面 Promise 值一样的新 Promise
        if (typeof onFulfilled !== 'function') {
            return Promise.resolve(this.value);
        }

        executor = onFulfilled;
    } else if (this.status === 'rejected') {
        // 如果前面的 Promise 状态为 rejected, 且 onRejected 不为函数, 则应该返回和前面 Promise 错误原因一样的新 Promise
        if (typeof onRejected !== 'function') {
            return Promise.reject(this.value);
        }

        executor = onRejected;
    }

    try {
        let result = executor(this.value);
        return Promise.resolve(result);
    } catch (err) {
        return Promise.reject(err);
    }
}

Promise.all = function (arr) {
    if (typeof arr[Symbol.iterator] !== 'function') {
        throw new TypeError(`${arr} is not iterable (cannot read property Symbol(Symbol.iterator))`)
    }

    let p = new Promise(() => { });
    // 剩余任务的数量
    let count = 0;
    let onResolved = function (val) {
        if (p.status === 'pending') {
            count--;
        }
        if (count === 0) {
            p.status = 'resolved';
            p.value = arr;
        }
    }
    let onRejected = function (val) {
        if (p.status !== 'rejected') {
            p.status = 'rejected';
            p.value = val;
        }
    }

    // 数组中元素是 Promise 对象时, 才添加 then 方法
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] instanceof Promise) {
            count++;
            arr[i].then(onResolved, onRejected);
        }
    }

    if (count === 0) {
        return Promise.resolve(arr);
    } else {
        return p;
    }
}

Promise.race = function (arr) {
    if (typeof arr[Symbol.iterator] !== 'function') {
        throw new TypeError(`${arr} is not iterable (cannot read property Symbol(Symbol.iterator))`)
    }

    let p = new Promise(() => { });
    let onResolved = function (val) {
        if (p.status === 'pending') {
            p.status = 'resolved';
            p.value = val;
        }
    }
    let onRejected = function (val) {
        if (p.status === 'pending') {
            p.status = 'rejected';
            p.value = val;
        }
    }

    // 数组中元素是 Promise 对象时, 才添加 then 方法
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] instanceof Promise) {
            arr[i].then(onResolved, onRejected);
        }
    }

    return p;
}
```

### 使用 async/await 封装 API
```js
// 这里封装的是 fetch
async function request(url, head) {
    try {
        // 等待 fetch 被 resolve() 后才继续执行
        let res = await fetch(url, head);
        return res;
    } catch(err) {
        throw err;
        return null;
    }
}
```

### 柯里化
todo

### 数组扁平化
```js
function flatten(arr) {
    // 注意这里的解构符号!
    return [].concat(...arr.map(val => {
        return Array.isArray(val) ? flatten(val) : val;
    }))
}

function flatten(arr) {
  return arr.reduce((pre, cur) => {
    return pre.concat(Array.isArray(cur) ? flatten(cur) : cur);
  }, [])
}
```

### 数组去重
```js
function unique(arr) {
    return [...new Set(arr)];
}

function unique(arr) {
    return arr.filter((val, index, arr) => {
        return arr.indexOf(val) === index;
    })
}
```

### 判断浏览器类型
判断浏览器类型, 主要是根据 `navigator.userAgent` 来获取浏览器信息, 之后根据里面的关键字来确定。
具体实现如下(支持 Chrome、Firefox、Opera、IE、Edge 以及 Safari):

```js
todo
```

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
todo

## WebGL
### 什么是 WebGL?
`WebGL` 的全称是 `Web Graphics Library`, 是一种 JavaScript API, 它让我们可以直接在浏览器中实现交互式 3D 图像。
`WebGL` 作为 `<canvas>` 的特定上下文运行。在支持HTML `<canvas>` 标签的浏览器中, 不需要安装任何插件, 便可以使用基于 `OpenGL ES 2.0`(`OpenGL` 是最常用的跨平台图形库) 的 API 在 `canvas` 中进行 3D 渲染, 借助系统显卡来在浏览器里更流畅地展示 3D 场景和模型, 且有很好的跨平台特性。
`WebGL` 程序由 JavaScript 的控制代码, 和在计算机的`图形处理单元(GPU, Graphics Processing Unit)`中执行的`特效代码(shader code, 渲染代码)`组成。

### WebGL 的渲染流程
WebGL 比一般的 web 技术要稍微复杂一点, 因为其涉及到和 GPU 的协同工作。也正是因为如此, 大量的渲染计算会通过 GPU 来完成, 从而显著提高渲染速度。
当我们需要渲染某个场景的时候, 会先使用 JavaScript 生成一些用于指定如何创建图形、图形的外观以及图形的位置的信息。然后再将这些信息发送到 GPU, 通过`渲染管线(rendering pipeline)`进行处理,最后返回场景视图。

### 渲染管线(rendering pipeline)做了什么?
首先我们要明白一点, **当我们的 GPU 绘制图像的时候, 其最小图形单元(注意是图形单元!)是三角形。**
GPU 绘制图像的基本流程是: 主机程序向 OpenGL 传递一组或多组`顶点数组(vertex array)`, 这些顶点数组被放在`顶点缓冲区(vertex buffer)`。之后, 这些顶点被投影到屏幕空间中, 按照顺序连接顶点, 拼装为三角形。再之后, 对三角形所覆盖的区域, 再次进行划分, 每个区域的大小等于像素大小, 这个过程被称为`栅格化(rasterization)`。最后, 再为每个像素点分配颜色值, 并最终绘制到`帧缓冲区(frame buffer)`。
下面这张图更加详细的展示了这一过程:

![渲染管线的工作流程](/images/interview-experence/09.jpg)

让我们更详细地看一下每个阶段:
1. 整个过程从创建**顶点数组**开始。这些数组中, 每个对象对应一个顶点, 对象中包含了顶点的位置、颜色、`纹理(texture)`等相关信息。这些数组及其包含的信息可以通过以下几种方式在 JavaScript 中创建:
 * 读取描述 3D 模型的文件(例如 `.obj` 文件)。
 * 从头开始, 手动创建数据。
 * 使用框架提供的内置图形。

2. 在创建完顶点数组后, 需要将其发送到 GPU 进行处理, 这一点可以通过将顶点数组送入**顶点缓冲区**来完成。值得注意的是, 在向 GPU 发送顶点数组的时候, 还必须提供一个额外的**索引数组(index array)**, 索引数组中的元素指向顶点数组中的元素。索引数组用于控制之后如何划分三角形。

3. 在此之后, GPU 会依次从顶点缓冲区中读取顶点, 并将顶点信息送入**顶点着色器(vertex shader)**中。顶点着色器是一个用于计算顶点详细属性的程序, 它将一组顶点属性作为输入, 并输出一组新属性。顶点着色器至少要计算顶点在空间中的投影位置; 也可以为每个顶点生成其他属性。顶点着色器并不一定需要自己编写, 你也可以使用 WebGL 内置的着色器。

4. 在计算完顶点的空间投影位置等信息后, GPU 会连接顶点的投影, 以形成空间三角形。这一过程会按索引数组指定的顺序获取顶点, 并将它们划分为三个一组, 来实现这一目的。这个过程被称为**三角形的组装(triangle assembly)**。
在这一过程中, 可以按以下三种不同的方式对顶点进行分组:
 * `独立三角形`: 将每三个元素分为一组, 作为三角形的三个顶点。
 * `条带状三角形`: 重用每个三角形的最后两个顶点, 作为下一个三角形的前两个顶点。 
 * `风扇状三角形`: 将第一个顶点连接到随后的每个顶点对。

![三角形的组装](/images/interview-experence/10.png)

5. 接下来, 便要开始**栅格化**了。首先要做的, 是对上一步形成的三角形进行裁剪, 丢弃不可见的部分。之后, 将剩余部分划分为像素大小的小块。同时, 如果顶点分配有颜色值, **栅格化器(rasterizer)**按照不同顶点的不同颜色, 对整个区域施加颜色渐变。

![栅格化](/images/interview-experence/11.png)

6. 在栅格化完成后, 会将每个像素点送入**片段着色器(fragment shader)**。片段着色器也是一个程序, 为每个像素填充颜色和空间深度值, 之后将其绘制到**帧缓冲区**中。与顶点着色器一样, 片段着色器既可以自己编写, 也可以使用 WebGL 库提供的 API。

7. **帧缓冲区**便是我们渲染输出的最终目标。需要注意的是, 我们所绘制的图形是处在 3D 空间中的, 因此帧缓冲区除了具有一个或多个**颜色缓冲区(colour buffer)**外, 还可以具有**深度缓冲区(depth buffer)**和**模板缓冲区(stencil buffer)**。
深度缓冲区的作用就是区分颜色所在的空间深度, 防止把被遮挡住的颜色显示出来。
模板缓冲区则可以指定一个形状, 让位于形状内部的元素显示, 外部的元素不显示, 类似于遮罩的效果。

注意, **上文提到的着色器, 都是使用 `OpenGL ES Shading Language(GLSL)` 编写的程序, 并不能通过 JavaScript 来编写!**

### WebGL 库做了什么?
由于 WebGL 的使用比一般 web 技术更加复杂, 因此相关的库有很多(比如 `three.js` 等)。
这些库共同的作用, 便是在 WebGL 基础上创建了可直接用于 3D 渲染的元素, 比如场景、观察角度、光源、环境光、纹理、漂浮例子特效等。这些元素的概念在所有库中都基本相同, 不同的是它们在不同库中的使用方法。
由于 WebGL 是交互式的, 所以大多数库也提供了简单的事件处理方法。
最后, 大多数库还提供了一些顶点着色器和片段着色器, 使用者可以根据自己的需求进行选用。

### 参考资料
* (An Introduction to WebGL — Part 1)[https://dev.opera.com/articles/introduction-to-webgl-part-1/]
* (An intro to modern OpenGL. Chapter 1: The Graphics Pipeline)[http://duriansoftware.com/joe/An-intro-to-modern-OpenGL.-Chapter-1:-The-Graphics-Pipeline.html]
* (初识 WebGL)[https://developer.mozilla.org/zh-CN/docs/Web/API/WebGL_API/Tutorial/Getting_started_with_WebGL]

## 前端路由
todo
https://juejin.im/post/5d2d19ccf265da1b7f29b05f#heading-7

## 移动端点透问题的成因及解决方法
todo

## MVC、MVP、MVVM 框架有什么区别?
### MVC
`MVC` 的意味着软件可以分为三个部分:
* 视图(View): 用户界面
* 控制器(Vontroler): 业务逻辑
* 模型(Model): 数据保存

各部分之间的通信方式如下, **所有通信都是单向的。**

![MVC 框架通信方式](/images/interview-experence/12.png)

接收用户指令时, `MVC` 框架有两种模式, 一种是直接通过 `View` 接收指令, 传递给 `Controler`; 另一种是直接通过 `Controler` 接收指令。

### MVP
`MVP` 模式就是将 `Controler` 改名为 `Presenter`, 同时改变了通信方向。

![MVP 框架通信方式](/images/interview-experence/13.png)

1. 各部分之间的通信都是双向的。
2. `View` 和 `Model` 不发生联系, 都通过 `Presenter` 传递。
3. `View` 不部署任务业务逻辑, 称为`被动视图(Passive View)`, 即没有任何主动性; 所有的业务逻辑都部署在 `Presenter` 中。

### MVVM
`MVVM` 模式将 `Presenter` 改名为 `ViewModel`, 基本上与 `MVP` 模式完全一致。

![MVVM 框架通信方式](/images/interview-experence/14.png)

唯一的区别是, 它采用`双向绑定(data-binding)`: `View` 的变动, 自动反映在 `ViewModel`, 反之亦然。

### 参考资料
* [MVC, MVP 和 MVVM 的图示](https://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

# React
## React 中的虚拟 DOM
### 虚拟 DOM 是什么?
虚拟 DOM 说白了就是 JS 对象。
![虚拟 DOM](/images/interview-experence/07.png)
当我们需要创建或更新元素时, React 首先会让这个 `VitrualDom` 对象进行创建和更改, 然后再将 `VitrualDom` 对象渲染成真实 DOM。
当我们需要对 DOM 进行事件监听时, 首先对 `VitrualDom` 进行事件监听, `VitrualDom` 会代理原生的 DOM 事件从而做出响应。

### 为什么需要虚拟 DOM?
1. React 掩盖了底层的 DOM 操作, 让我们得得以用更加声明式的方式来描述我们的目的, 让代码更容易维护。
2. 使用虚拟 DOM 为我们带来了不错的性能优化。因为框架的 DOM 操作层需要应对上层 API 产生的所有可能的操作, 因此它的实现必须是普适的。**React 从来没有说过 "React 比原生操作 DOM 快"。**React 给我们的保证是, 在不需要手动优化的情况下, 它依然可以给我们提供过得去的性能。而这之所以能有很好的性能, 很大程度上归功于虚拟 DOM 的 `batching` 和 `diff`。`batching` 把所有 DOM 操作收集起来, 一次性提交给真实 DOM; `diff` 算法的时间复杂度也从标准 `diff` 算法的 `O(n^3)` 降到了 `O(n)`。
3. React 基于虚拟 DOM 实现了自己的事件系统, 抹平了不同浏览器之间的差异。
4. 虚拟 DOM 为 React 带来了跨平台渲染的能力。转换平台时, 只需要转换 `render(渲染器)` 即可。

### React 组件的渲染流程
大致上的渲染流程如下:

![虚拟 DOM 渲染流程图](/images/interview-experence/08.png)

在 React 内部, 其具体渲染流程如下:
* 使用 `React.createElement` 或 `jsx` 编写 React 组件, 实际上所有的 `jsx` 代码最后都会转换成 `React.createElement(...)`, Babel帮助我们完成了这个转换的过程。
* `createElement` 函数对 `key` 和 `ref` 等特殊的 `props` 进行处理, 并获取 `defaultProps` 对默认 `props` 进行赋值, 并且对传入的孩子节点进行处理, 最终构造成一个 `ReactElement` 对象(所谓的虚拟  DOM)。
* `ReactDOM.render` 将生成好的虚拟 DOM 渲染到指定容器上, 其中采用了批处理、事务等机制并且对特定浏览器进行了性能优化, 最终转换为真实 DOM。

### 虚拟 DOM 的组成
虚拟 DOM 即 `ReactElement` 对象, 我们的组件最终会被渲染为下面的结构:
* `$$typeof`: 它是一个 `Symbol` 类型的变量 `Symbol.for('react.element')`, 当环境不支持 `Symbol` 时, `$$typeof` 被赋值为 `0xeac7`。**至于为什么是 `0xeac7`, 官方的解释是 `0xeac7` 看起来有点像 `React`。**
* `type`: 元素的类型, 可以是原生 html 类型(字符串), 或者自定义组件(函数或 class)。
* `key`: 组件的唯一标识, 用于 Diff 算法。
* `ref`: 用于访问原生 DOM 节点。
* `props`: 传入组件的 props, `chidren` 是 props 中的一个属性, 它存储了当前组件的孩子节点, 可以是数组(多个孩子节点)或对象(只有一个孩子节点)。
* `owner`: 当前正在构建的 Component 所属的 Component。
* `self`: **(仅用于非生产环境)**指定当前位于哪个组件实例。
* `_source`: **(仅用于非生产环境)**指定调试代码来自的文件(`fileName`)和代码行数(`lineNumber`)。

### 防止 XSS 注入
`ReactElement` 对象的 `$$typeof` 属性可以防止 XSS 注入。
如果你的服务器有一个漏洞, 允许用户存储任意 JSON 对象, 而客户端代码需要一个字符串, 这可能为你的应用程序带来风险。**然而 JSON 中不能存储 `Symbol` 类型的变量, 而 React 渲染时会把没有 `$$typeof` 标识的组件过滤掉。**

### 批处理和事务
React 在渲染虚拟 DOM 时应用了批处理以及事务机制, 以提高渲染性能。
这一部分参见[\[React 深入\] setState 的执行机制](https://segmentfault.com/a/1190000018260218)

### 针对性能的优化
**在 `IE(8-11)` 和 `Edge` 浏览器中, 一个一个插入无子孙的节点, 效率要远高于插入一整个序列化完整的节点树。**

React 通过 `lazyTree` 变量, 在 `IE(8-11)` 和 `Edge` 中进行单个节点依次渲染节点; 而在其他浏览器中则首先递归地将整个大的 DOM 结构构建好, 然后再整体插入容器。

并且, 在单独渲染节点时, React 还考虑了 `fragment` 等特殊节点, 这些节点则不会递归插入, 而是会先将此节点的孩子节点渲染出来, 再将渲染完的节点插入 html。

### 虚拟 DOM 事件机制
React 自己实现了一套事件机制, 其将所有绑定在虚拟 DOM 上的事件映射到真正的 DOM 事件, 并将所有的事件都代理到 `document` 上, 自己模拟了事件冒泡和捕获的过程, 并且进行统一的事件分发。

React 自己构造了合成事件对象 `SyntheticEvent`, 这是一个跨浏览器原生事件包装器。 它具有与浏览器原生事件相同的接口, 包括 `stopPropagation()` 和 `preventDefault()` 等等, 在所有浏览器中他们工作方式都相同。这抹平了各个浏览器的事件兼容性问题。

### 结语
上面只分析虚拟 DOM 首次渲染的原理和过程, 当然这并不包括虚拟 DOM 进行 Diff 的过程。
以上的结论, 都来自对 React 15 版本的阅读, 16 版本可能会有一些出入, 但总体上是正确的。

### 参考资料
* [\[React 深入\]深入分析虚拟 DOM 的渲染原理和特性](https://segmentfault.com/a/1190000018891454#item-6-11)
* [\[React 深入\] setState 的执行机制](https://segmentfault.com/a/1190000018260218)

## React 中的批处理和事务
todo

## diff算法
### diff 策略
React 总的 diff 策略如下:
1. 对于 DOM 的跨层级移动操作特别少, 可以忽略不计。
2. 拥有相同类名的两个组件会形成相似的树形结构; 拥有不同类名的两个组件会生成不同的树形结构。
3. 对于同一层级的一组子节点, 通过唯一 id 进行区分。

下面我们来看看以上策略在 tree diff、component diff 以及 element diff 上的应用。
todo

## React 组件的生命周期有哪些?
### 挂载
当组件实例被创建并插入到 DOM 中时, 其生命周期调用顺序如下:
* `constructor()`
* `static getDerivedStateFromProps()`: 该方法会在调用 render 方法之前调用, 并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state, 如果返回 null 则不更新任何内容。**`derived` 的意思是`派生的`。**此方法适用于罕见的用例, 即 state 的值在任何时候都取决于 props。
* `render()`
* `componentDidMount()`

> 注意: 原有的 `UNSAFE_componentWillMount()` 方法即将过期, 应避免使用。

### 更新
当组件的 props 或 state 发生改变时会触发更新。组件更新的生命周期调用顺序如下:
* `static getDerivedStateFromProps()`
* `shouldComponentUpdate()`
* `render()`
* `getSnapshotBeforeUpdate()`: 在最近一次渲染输出(提交到 DOM 节点)之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息(例如滚动位置)。此生命周期的任何返回值将作为参数传递给 `componentDidUpdate()`。
* `componentDidUpdate()`

> 注意: 原有的 `UNSAFE_componentWillUpdate()` 和 `UNSAFE_componentWillReceiveProps()` 方法即将过期, 应避免使用。

### 卸载
当组件从 DOM 中移除时会调用如下方法:
* `componentWillUnmount()`

### 错误处理
当渲染过程、生命周期或子组件构造函数抛出错误时, 会调用如下方法:
* `static getDerivedStateFromError()`: 此生命周期会在后代组件抛出错误后被调用。它将抛出的错误作为参数, 并返回一个值以更新 state。
* `componentDidCatch()`

## React Fiber
首先我们要了解到, 要插入一万个 dom 节点, 有两种方式, 一种是插入一万次一个节点, 一种是插入一百次一百个节点。第二种方法更好, 因为这样浏览器便有时间进行代码优化和合并执行。
`jsx`是嵌套的天然结构, 在 react 15 中被翻译为递归执行的代码, 因此称 react 15 的调度器为栈调度器。栈调度器代码量少, 浅显易懂, 但缺点是不能随意 break、continue。
react 16 的调度器将 `jsx` 翻译为链表结构, 将组件的递归更新改成链表的依次执行。如果页面有有多个虚拟 dom 树, 则将它们的根保存在一个数组中。
react 从上到下大致有三层, 一层为虚拟 dom 层, 只负责描述结构与逻辑, 第二次为内部组件层, 负责更改 state, 执行生命周期函数, 第三层为底层渲染层, 针对不同环境有不同的渲染器。
react 16 将内部组件层改为 fiber 这种数据结构。每个 fiber 有三个属性, return、child 和 sibling。return 指向父节点, child 指向第一个子节点, sibling 指向它右边的兄弟节点。
### 更新过程
在 react 15 中, 每次更新时, 都是从根组件或者 setState 后的组件开始, 更新整个子树。我们唯一能做的, 便是在某个节点中使用 shouldComponentUpdate 断开某一部分的更新, 或是优化 shouldComponentUpdate 的比较效率。
react 16 则是将虚拟 dom 转化为 fiber 节点, 再将 fiber 节点转化为组件实例或真实 dom。它会规定一个时间段, 在这个时间段内, 能转化多少个 fiber 节点, 就更新多少个 fiber 节点。
todo

## React 组件通信
### 父组件向子组件通信
### 子组件向父组件通信
### 跨级组件通信
### 没有嵌套关系组件之间的通信
todo
  
## 容器组件和展示组件的区别
`展示组件`是指专门用于展示数据的组件, 内部不含任何业务逻辑。
`容器组件`是指用于控制展示组件行为, 内部含有业务逻辑的组件。

## React Router 实现原理
todo

## Redux 实现原理6

todo

# Vue 
todo

# 计算机网络
## http1.0、http1.1、http2.0 之间的区别
### http1.0
1. http1.0 中请求完成后即断开 tcp 连接, 发向同一服务器的不同请求会重复建立 tcp 连接, 服务器也不会保存之前请求的状态。
2. `线头阻塞(Head Of Line Block)`问题, 即若引服务器正忙等原因导致第一个请求未处理, 那么后续的请求会被阻塞。

### http1.1
1. 为了解决以上问题实现了长连接, 即加入了 `Connection` 字段, 其设置 `keep-alive` 后哪怕请求结束后, tcp 连接也不会断开。若要断开连接, 需客户端在请求中将该字段设为 `false` 来告知服务器关闭连接。
2. 在长连接基础上, http1.1还引入了`管线化(pipelining)`, 即客户端可并行发送多个请求, 服务端会按顺序返回请求结果, 以保证客户端能分辨出每次请求的结果。这样做的意义是将先入先出的队列由客户端(请求队列)转移到了服务器端(响应队列)。**但即便如此, 还是没有两个请求是并行的, 所以线头阻塞问题还是没有解决, 且管线化还有很多其他的问题, 因此一般浏览器都是默认关闭管线化的。**为了实现真正的并行请求和响应, 现阶段各大浏览器厂商的解决方法一般为开启多个 tcp 连接, 在不同连接上进行http 请求和响应。
3. 加入了`缓存处理(cache-control)`相关的字段。
4. 支持断点传输, 通过 `Range` 字段指定文件传输的范围实现。
5. 增加了 `HOST` 字段, 使同一服务器能够建立多个 Web 站点。

### http2.0
1. 进行了二进制分帧, 在应用层和传输层中间加入了二进制分帧层, 提高了传输效率。
2. 实现了多路复用。一个 tcp 连接可承载任意多个双向数据流, 每个请求对应一个数据流, 数据拆分为帧进行发送, 每个帧头部都有流标识 id, 这些帧可以乱序发送, 再根据流标识 id 进行重组。为避免关键请求被阻塞, 可为数据流设置优先级, 优先级高的数据流优先处理。这一改进解决了线头阻塞问题。
3. 头部压缩。为了减少请求头的大小, 客户端和服务端分别维护相同的静态字典(常用头部名称及值)和动态字典(可动态添加内容)。通过传输序号和字典查询再加上合适的压缩算法可大大减少头部大小。
4. 服务器推送。服务器可主动向客户端推动内容。

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
## cookie、sessionStorage、localStorage 以及 webStorage 有什么区别?
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

## Fetch
Fetch API 提供了一个获取资源的接口(包括跨域请求), 用于访问和操作 HTTP 请求。
它提供了一个全局的 `fetch()` 方法, 这个方法是挂载在 window 上的, 该方法提供了一种简单、合理的方式来异步获取资源。

### fetch()
`fetch()` 方法的语法如下:
```js
fetch(input[, init]);
```
参数列表如下:
* `input`: 定义要获取的资源。可以是一个 URL, 也可以是一个 `Request` 对象。
* `init`: 一个配置项对象。可选的参数如下:
 * `method`: 请求使用的方法。
 * `headers`: 请求的头信息。
 * `body`: 请求的 body 信息。注意 GET 和 HEAD 方法的请求不能包含 body 信息。
 * `mode`: 请求的模式。如 `cors`、`no-cors` 或 `same-origin`。
 * `credentials`: 请求的证书。为了在当前域名内自动发送 cookie, 必须提供这个选项。`fetch` 默认是不会发送 cookie 的。
 * ...

这个方法的返回值是一个 Promise 对象, 返回的 Promise 中包含一个 `Response` 对象。
值得注意的是, 当接收到一个代表错误的 HTTP 状态码时, 从 `fetch` 返回的 Promise 并不会被标记为 reject, 即使状态码是 404 或 500。**仅当由于网络故障或其他原因请求被阻止时, 才会标记为 reject。**
下面来看一段示例代码:
```js
let myHeaders = new Headers();

let myInit = { 
    method: 'GET',
    headers: myHeaders,
    mode: 'cors',
    cache: 'default', 
};

let myRequest = new Request('flowers.jpg', myInit);

fetch(myRequest).then(function(response) {
    return response.blob();
}).then(function(myBlob) {
    let objectURL = URL.createObjectURL(myBlob);
    myImage.src = objectURL;
});
```
在上述代码中, 使用了 `Request()` 构造函数创建了一个 `Request` 对象, 作为参数传给了 `fetch()`。同时还使用 `Headers()` 构造函数创建了 `Headers` 对象, 作为请求的 `headers` 来使用。
接下来我们便来看看 `Headers` 对象、`Request` 对象和 `Response` 对象。

### Headers
使用 Headers() 构造方法可以创建一个新的 Headers 对象。

append() 在该接口的所有方法中, 标题名称由不区分大小写的字节序列匹配。
get()
init 参数, 可选, 可以是一个对象或已存在的 Headers 对象。
set()
has()
delete()

值得注意的是,在header已存在或者有多个值的状态下Headers.set() 和 Headers.append()的使用有如下区别, Headers.set() 将会用新的值覆盖已存在的值, 但是Headers.append()会将新的值添加到已存在的值的队列末尾. 

如果您尝试传入名称不是有效的HTTP头名称的引用, 则所有Headers方法都将引发 TypeError 。 如果头部有一个不变的Guard, 则变异操作将会抛出一个 TypeError 。 在其他任何失败的情况下, 他们默默地失败。

[有效的http头部字段](https://fetch.spec.whatwg.org/#concept-header-name)

#### Grard
由于 Headers 可以在 request 请求中被发送或者在 response 请求中被接收, 并且规定了哪些参数是可写的, Headers 对象有一个特殊的 guard 属性。这个属性没有暴露给 Web, 但是它影响到哪些内容可以在 Headers 对象中被操作。

你不可以添加或者修改一个 guard 属性是 request 的 Request Header 的 Content-Length 属性。同样地, 插入 Set-Cookie 属性到一个 response header 是不允许的, 因此, Service Worker 中, 不能给合成的 Response 的 header 添加一些 cookie。
todo
### Re

### 

### 参考链接
* [Fetch 的使用方法](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)
* [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch)
* [Fetch API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)

## WebWorker
todo

## ServiceWorker
Service workers 本质上充当Web应用程序与浏览器之间的代理服务器, 也可以在网络可用时作为浏览器和网络间的代理。它们旨在(除其他之外)使得能够创建有效的离线体验, 拦截网络请求并基于网络是否可用以及更新的资源是否驻留在服务器上来采取适当的动作。他们还允许访问推送通知和后台同步API。
简单来说, ServiceWorker 允许应用完全控制浏览器的网络行为, 修改网络请求。你可以完全控制应用在特定情形(最常见的情形是网络不可用)下的表现。可以通过ServiceWorker创建离线应用。
Service worker运行在worker上下文, 因此它不能访问DOM。相对于驱动应用的主JavaScript线程, 它运行在其他线程中, 所以不会造成阻塞。它设计为完全异步, 同步API(如XHR和localStorage)不能在service worker中使用。
出于安全考量, Service workers只能由HTTPS承载, 毕竟修改网络请求的能力暴露给中间人攻击会非常危险。在Firefox浏览器的用户隐私模式, Service Worker不可用。
参考链接
https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API
https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers
todo

# 数据结构与算法
## 堆
### 预备知识
* **堆是一棵完全二叉树。**完全二叉树指: 每层结点都完全填满, 在最后一层上如果不是满的, 则只缺少右边的若干结点。
* 最大堆: 根结点为最大值, 每个结点的值大于或等于其后代结点的值。
* 最小堆: 根结点为最小值, 每个结点的值小于或等于其后代结点的值。
* 堆的存储: 出于方便插入和删除操作的考虑, **堆由数组来实现, 存储的顺序相当于对二叉树做广度优先遍历。**

![堆01](/images/interview-experence/15.png)

### 堆的操作
#### 构造函数
堆的构造函数接受一个数组作为内置元素的值。构造出的堆结构还具有 `size` 属性。

```js
// 这里以最大堆为例
function MaxHeap(nums) {
    this.data = nums.sort((a, b) => a - b);

    Object.defineProperty(this, 'size', {
        get: function() {
            return this.data.length;
        }
    })
}
```

#### 删除
**每次执行删除操作时, 都会删除堆的`堆顶元素`, 之后移动剩余节点, 以维持堆的性质。**
下面我们来看看如何实现:
假设我们有如下的最大堆:

![堆02](/images/interview-experence/16.jpg)

当我们执行删除操作时, 我们删除的是堆顶元素, 也就是最大的元素。但是删除之后, 由于这是一棵完全二叉树, 所以不能贸然移动节点。所以我们**先将最后一个元素补在之前删除的位置, 以保证完全二叉树的性质。**

![堆03](/images/interview-experence/17.jpg)

这时候, 我们需要想办法恢复堆的排序性。于是我们需要**比较根节点与其子节点的大小, 如果子节点比根节点大, 则交换位置。这个过程直到所有子节点为空或所有子节点都比它小为止。**这个过程反映在数组上, 就是先找到根节点对应的子节点, 之后进行比较并决定要不要交换二者在数组中的位置。
对于节点 i, 其子节点为 2i + 1 和 2i + 2。

![堆04](/images/interview-experence/18.jpg)

![堆05](/images/interview-experence/19.jpg)


具体实现如下:
```js
function deleteHeapTop() {
    let data = this.data;
    let heapTop = data.shift();

    // 如果删除后, 堆中剩余元素的个数小于 1, 则不需要进行额外操作
    if (this.size > 1) {
        // 提取树中最下面一层、最右侧的节点, 即数组中的最后一个元素
        let leaf = data.pop();
        // 将最后一个节点放在堆顶, 即数组的第一个元素
        data.unshift(leaf);

        let index = 0;
        let leftIndex = 2 * index + 1;
        let rightIndex = 2 * index + 2;

        while (data[leftIndex] || data[rightIndex]) {
            if (data[leftIndex] && data[index] < data[leftIndex]) {
                // 当前节点比左节点小, 交换二者的位置
                [data[index], data[leftIndex]] = [data[leftIndex], data[index]];
                
                // 重新计算左右子节点在数组中的位置
                index = leftIndex;
                leftIndex = 2 * index + 1;
                rightIndex = 2 * index + 2;
            } else if (data[rightIndex] && data[index] < data[rightIndex]) {
                // 当前节点比右节点小, 交换二者位置
                [data[index], data[rightIndex]] = [data[rightIndex], data[index]];

                // 重新计算左右子节点在数组中的位置
                index = rightIndex;
                leftIndex = 2 * index + 1;
                rightIndex = 2 * index + 2;
            } else {
                break;
            }
        }
    }

    return heapTop;
}
```

#### 插入
仍以最大堆为例。当执行插入操作时, 我们**先将节点插入到树的最后一个位置, 然后比较其与父节点的大小。如果插入的元素比父节点大, 则交换二者位置。直到插入节点小于父节点为止。**
前面我们已经知道了如何通过父节点下标得出子节点下标, 现在我们需要通过子节点下标得出父节点下标。
方法也很简单, 设子节点下标为 i, 首先判断子节点是左节点还是右节点。如果 i 为奇数, 则为左节点; 如果 i 为偶数, 则为右节点。
接下来推导父节点下标。如果是左节点, 则父节点下标为 (i - 1) / 2; 如果是右节点, 则父节点下标为 (i - 2) / 2。
下面来看一个例子:
假设我们有如下最大堆:

![堆06](/images/interview-experence/20.jpg)

我们需要插入的值为 58, 由于其比父节点大, 则有

![堆07](/images/interview-experence/21.jpg)

然后继续我们的比较, 直到最终结果。

![堆08](/images/interview-experence/22.jpg)

具体实现如下:

```js
function insert(val) {
    let data = this.data;
    // 将新元素添加到数组末尾
    data.push(val);

    let index = data.length - 1;
    let parentIndex = index % 2 === 1 ? (index - 1) / 2 : (index - 2) / 2;

    while (parentIndex >= 0) {
        if (data[index] > data[parentIndex]) {
            // 如果新节点的值比父节点大, 则交换二者位置
            [data[index], data[parentIndex]] = [data[parentIndex], data[index]];

            // 重新计算父节点位置
            index = parentIndex;
            parentIndex = index % 2 === 1 ? (index - 1) / 2 : (index - 2) / 2;
        } else {
            break;
        }
    }
}
```

### 参考资料
* [JS 实现堆排序](https://segmentfault.com/a/1190000015487916)
* [最大/最小堆的排序](https://zhuanlan.zhihu.com/p/40782547)

## 红黑树


# js深入系列
todo
https://github.com/mqyqingfeng/Blog

# Node

# 其他
## Linux 操作系统相关
todo 软连接

## 前端性能优化
todo
参考资料 https://www.cnblogs.com/xianyulaodi/p/5755079.html

## 前后端渲染的优缺点
首先要明确三个概念, 那就是`后端渲染`, `前端渲染`和`同构渲染`。
「后端渲染」指传统的 ASP、Java 或 PHP 的渲染机制；「前端渲染」指使用 JS 来渲染页面大部分内容, 代表是现在流行的 SPA 单页面应用；「同构渲染」指前后端共用 JS, 首次渲染时使用 Node.js 来直出 HTML。一般来说同构渲染是介于前后端中的共有部分。
前端渲染的优势:
局部刷新。无需每次都进行完整页面请求
懒加载。如在页面初始时只加载可视区域内的数据, 滚动后rp加载其它数据, 可以通过 react-lazyload 实现
富交互。使用 JS 实现各种酷炫效果
节约服务器成本。省电省钱, JS 支持 CDN 部署, 且部署极其简单, 只需要服务器支持静态文件即可
天生的关注分离设计。服务器来访问数据库提供接口, JS 只关注数据获取和展现
JS 一次学习, 到处使用。可以用来开发 Web、Serve、Mobile、Desktop 类型的应用
后端渲染的优势:
服务端渲染不需要先下载一堆 js 和 css 后才能看到页面(首屏性能)
SEO
服务端渲染不用关心浏览器兼容性问题(随着浏览器发展, 这个优点逐渐消失)
对于电量不给力的手机或平板, 减少在客户端的电量消耗很重要

以上服务端优势其实只有首屏性能和 SEO 两点比较突出。但现在这两点也慢慢变得微不足道了。
**前端渲染是未来趋势, 但前端渲染遇到了首屏性能和SEO的问题。**
todo

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
5. 抽象类和接口的区别?
6. 抽象、继承、多态?

ios oc的事件、消息机制?
ios 耗电优化?

# typescript
读文档

# React Native

# Flutter

# 小程序相关
todo 这些东西在航旅纵横二面前看完
权限管理
小程序发布
小程序包压缩策略

# 移动端
适配相关

AOE 网
java基本语法
在JavaScript中, 所有延迟加载的方式中,只有IE浏览器支持的是__________方式。
图的遍历方法