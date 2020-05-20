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
### 选择器的种类

![选择器](/images/interview-experence/01.jpg)

详情见[CSS 选择器参考手册](https://www.w3school.com.cn/cssref/css_selectors.asp)。

### 选择器的权重
设置样式的方式有很多种, 不同方式的权重不同。**当给一个 DOM 节点设置同一个样式时, 权重高者生效。**

以下为使用不同方式定义样式时的权重:
* `!important`: 权重值为无限大。
* 行内样式: 权重值为 `1000`。
* `id` 选择器: 权重值为 `100`。
* 类、伪类、属性选择器: 权重值为 `10`。
* 元素、伪元素选择器: 权重值为 `1`。
* 继承样式、通配符: 权重值为 `0`。

CSS 样式的呈现遵循以下规则:
1. 权重大的 CSS 生效。
2. 如果权重一致, 则后面定义的样式覆盖前面定义样式。**注意: 这里是定义顺序, 而不是类名顺序。**
3. 如果某个节点上, 有多个选择器为其定义了样式, 且样式之间相互冲突, 则单个权重更高的选择器定义的样式会生效。**也就说对于不同等级的选择器, 即使低等级的选择器再多, 其权重仍小于一个高等级选择器定义的 CSS。**

对于第二、三条规则, 是不是有点不好理解? 那我们就来看两段代码:

```html
<style type='text/css'>
    .red {
        color: red;
    }

    .blue {
        color: blue;
    }
</style>

<p class='red blue'>text</p>
<p class='blue red'>text</p>
```

问题来了, 两个 `<p>` 标签中的字体颜色是什么呢? 最终显示都是蓝色的。这是因为 `.blue` 后于 `.red` 定义, 因此无论类名是先 `.red` 还是先 `.blue`, 最终显示的结果都是蓝色的。

接下来我们看一段关于规则三的代码:

```html
<style type='text/css'>
    #root {
        width: 100px;
        height: 100px;
        background: red;
    }

    .root1 .root2 .root3 .root4 .root5 .root6 .root7 .root8 .root9 .root10 .root11 {
        background: black;
    }
</style>

<div class="root1">
    <div class="root2">
        <div class="root3">
            <!-- ... -->
            <!-- 中间省略了 root4 - root10 -->
            <div id="root" class="root11"></div>
        </div>
    </div>
</div>
```

按照权重计算规则, 类选择定义样式的权重应该为 `120`, 大于 `id` 选择器的权重 `100`。但由于规则三的存在, 最终显示的背景色为红色。

## 伪类与伪元素的区别
先来看看 `w3c` 对于二者的定义:
* CSS 伪类用于向某些选择器添加特殊的效果。
* CSS 伪元素用于将特殊的效果添加到某些选择器。

直接说结论:
伪类的操作对象是文档树中已有的元素, 而伪元素则创建了一个文档外的元素。因此, **伪类与伪元素的区别在于: 有没有创建一个文档树之外的元素。**
需要注意的是, 伪元素的重点在于一个**伪**字, 虽然它们可以被浏览器渲染引擎识别并正确渲染, 然而**伪元素本身并不是 DOM 元素,** 所以无法被 js 直接操作————**因此任何基于 js 直接选取 DOM 元素的 CSS 更改方法对伪元素都不起作用。**

![伪类](/images/interview-experence/32.png)

![伪元素](/images/interview-experence/33.png)

### 参考资料
* [总结伪类与伪元素](http://www.alloyteam.com/2016/05/summary-of-pseudo-classes-and-pseudo-elements/)

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
`display` 属性有两个作用:
1. 定义元素的外部显示类型(块级元素或行内元素), 规定元素在文档流中的行为。
2. 控制其子元素的布局(`flex` 或 `grid`)。

也就是说, `display` 的值可以分为两部分。这也体现在 `display` 属性设置时的语法上。

```css
/* <display-outside> values 外部显示类型 */
display: block;
display: inline;
display: run-in;

/* <display-inside> values 内部显示类型 */
display: flow;
display: flow-root;
display: table;
display: flex;
display: grid;
display: ruby;

/* <display-outside> plus <display-inside> values */
display: block flow;
display: inline table;
display: flex run-in;

/* 还有一些值, 这里就不做阐释了 */
```

下面来具体说一下各个值的含义。首先是外部显示类型:
* `block`: 当前元素在文档流中表现为块级元素。 
* `inline`: 当前元素在文档流中表现为行内元素。
* `run-in`: 

之后是内部显示类型:
* `flow`: 
* `flow-root`: 
* `table`: 
* `flex`: 
* `grid`: 
* `ruby`: 

除此之外, 还有几个属性值得额外说一下:
* `none`:
* `inline-block`:
* `list-item`: 
todo

### 参考资料
* [MDN: display](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)
* [小 tip: 使用 CSS(Unicode 字符)让 inline 水平元素换行](https://www.zhangxinxu.com/wordpress/2012/03/tip-css-multiline-display/)

## box-sizing
简单来说, 该属性可以改变元素宽高的计算方式。
这个属性有以下几个值:
* `content-box`: 默认值。当设置为这个值时, 元素宽高只包含内容宽高, 不包含 `padding` 和 `border` 值。`标准盒模型(standard box model)`。
* `border-box`: 当设置为这个值时, `border` 和 `padding` 值也会算入元素宽高之内。`替代(IE)盒模型(alternate box model)`。

## 盒模型
由于其中涉及到了关于 `display` 以及 `box-sizing` 的知识, 所以放在这里进行叙述。
盒模型一般有两种, 分`标准盒模型(standard box model)`和``替代(IE)盒模型(alternate box model)`。这些都在 `box-sizing` 里介绍过了, 就不说了。这里要关注的, 是一些更深入的知识。

### 内部盒子和外部盒子
在 `display` 中, 我们了解到, `display` 的值可以分为外部显示类型和内部显示类型两类。
要理解这一点, 我们可以把一个元素想象成两个嵌套的盒子。其中, 外部盒子对应 `display` 中的外部显示类型, 控制元素在文档流中的行为; 内部盒子对应 `display` 中的内部显示类型, 控制盒子内部子元素是如何布局的。

### 块级盒子和内联盒子
在 CSS 中, 我们会广泛应用两种盒子: `块级盒子(block box)`和`内联/行内盒子(inline box)`。两种盒子在文档流中的显示行为有所不同。
一个被定义为块级的盒子会表现出如下行为:
* 盒子会横向扩展, 并占据父元素在该方向上的所有可用空间。绝大多数情况下, 意味着盒子会和父容器一样宽。
* 每个盒子都会换行。
* `width` 和 `height` 属性可以发挥作用。
* `padding`、`border` 和 `margin` 会将其他元素从当前盒子周围推开。

除非特殊指定, `<h1>`、`<p>`、`<div>` 等默认情况下都是块级盒子。

一个被定义为行内的盒子会表现出如下行为:
* 盒子不会换行。
* `width` 和 `height` 属性不起作用。
* `padding`、`border` 和 `margin` 会被应用在盒子上, 但不会把周围同样定义为 `inline` 的盒子推开。

应当注意的是, **除可替换元素外, 对于行内盒子来说, 尽管内容周围存在内边距与边框, 但其占用空间(每一行文字的高度)则由 `line-height` 属性决定。**你可以将 `line-height` 视为行内元素的 `height` 属性。
常见的行内元素有: `<a>`、`<span>`、`<em>`、`<strong>` 等。

不管是块级盒子还是行内盒子, 都属于外部显示类型的范畴, 可以通过设置 `dispaly` 来进行改变。

最后附赠一点关于 `<em>` 与 `<strong>` 的小知识:
`<em>` 在表现上是斜体, `<strong>` 在表现上是粗体。二者都表示强调, 都可以通过嵌套。嵌套的层级越深, 则其包含的内容被认定为越需要着重阅读。
那么问题来了, 既然 `<em>` 与 `<i>`、`<strong>` 与 `<b>` 在视觉上都是一样的, 那么二者有什么区别呢?
**在默认情况下, 它们的视觉效果是一样的, 但语义是不同的。**`<em>` 和 `<strong>` 表示其内容的着重强调, 而 `<i>` 和 `<b>` 表示从正常散文中区分出的文本。

### 替换元素与非替换元素
在 CSS 中, `可替换元素(replaced element)`的展现效果不是由 CSS 来控制的。这些元素是一种外部对象, 它们外观的渲染, 是独立于 CSS 的。
简单来说, 它们的内容不受当前文档的样式的影响。**CSS 可以影响可替换元素的位置, 但不会影响到可替换元素自身的内容。**
常见的替换元素有: `<iframe>`、`<video>`、`<img>`。
`非替换元素(non-replaced element)`指展示效果由 CSS 控制的元素。大多数元素都属于非替换元素。

### 参考资料
* [MDN: CSS基础框盒模型介绍](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)
* [MDN: 盒模型](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/The_box_model)
* [MDN: 可替换元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Replaced_element)

## margin
这是个大家都十分熟悉的属性, 但你真的了解这个属性吗?
废话不多说, 直接罗列知识点。
`margin` 指定的是外边距, 指定的外边距允许为负数。这里需要提一下, **`padding` 属性不能设置负数。**
另外, **`margin` 的 `top` 和 `bottom` 属性对非替换内联元素无效,** 例如 `<span>` 和 `<code>`。
以上这些都不是这次要讨论的重点, 这里之所以把这个属性单列出来, 是因为其有一个非常特别的行为, 那就是`外边距折叠(margin collapsing)`。

### 外边距折叠
首先简单介绍一下`外边距折叠(margin collapsing)`的概念。简单地说, 外边距折叠指的是, 当两个外边距相遇时, 它们将形成一个外边距。合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者。
一般有如下三种场景。

#### 相邻元素之间的外边距合并
当一个元素与另一个元素相邻, 且两个元素都在相邻的边上定义了外边距时, 就会发生外边距合并。
以垂直相邻为例。当一个元素出现在另一个元素上面时, 上面元素的下外边距会和下面元素的上外边距发生合并。

![相邻元素之间的外边距合并](/images/interview-experence/34.gif)

#### 子元素与父元素之间的外边距合并
当一个元素包含在另一个元素中, 且两个元素之间没有边框、内边距将两个元素隔开的情况下, 子元素的外边距会和父元素的外边距发生合并。注意, **这有可能导致子元素的外边距溢散到父元素之外!**

![子元素与父元素之间的外边距合并](/images/interview-experence/35.gif)

#### 元素自身的外边距合并
最后一个是比较诡异的一点, 即元素自身的外边距也可以相互合并。
如果一个元素有外边距, 但没有内容, 也没用边框和内边距的情况下, 元素的上外边距和下外边距、左外边距和右外边距便会接触在一起, 就会发生合并。

![元素自身的外边距合并](/images/interview-experence/36.gif)

如果这个外边距遇到另一个元素的外边距，它还会发生合并。

![发生多次外边距合并](/images/interview-experence/37.gif)

### 为什么需要外边距合并?
虽然看起来十分迷惑, 但这种外边距合并是必不可少的。以由几个段落组成的典型文本页面为例。第一个段落上面的空间等于段落的上外边距。如果没有外边距合并，后续所有段落之间的外边距都将是相邻上外边距和下外边距的和。这意味着段落之间的空间是页面顶部的两倍。如果发生外边距合并，段落之间的上外边距和下外边距就合并在一起，这样各处的距离就一致了。

![需要外边距合并的理由](/images/interview-experence/38.gif)

#### 值得注意的地方
上面叙述的都是比较简单的场景。下面说几个比较值得注意的地方。

* **外边距折叠可以发生好几次。**也就是说, 上面的情况可以组合在一起, 产生更复杂的场景。有可能会发生三四个外边距折叠成一个的场景。
* 如果参与折叠的边距中有负值, 折叠后的外边距的值为最大的正边距与最小的负边距(即绝对值最大的负边距)的和。
* 如果参与折叠的边距都是负值, 折叠后的外边距的值为最小的负边距的值。这一规则适用于相邻元素和嵌套元素。
* 如果没有给块级元素设置宽度, 一般来说, 块级元素会和父元素等宽。此时给元素设置负的 `margin-left` 和 `margin-right`, 会导致元素向左右两个方向拉伸, 最终宽度为: `父元素宽度 + | margin-left | + | margin-right |`。

最后说一下, **只有普通文档流中块级元素的外边距才会发生外边距合并。行内框、浮动框或绝对定位之间的外边距不会合并。**

这里只做了简单地知识点总结。之后有时间会写一篇博客好好讨论一下这个问题。

### 参考资料
* [MDN: margin](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin)
* [MDN: 外边距折叠](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
* [w3school: 外边距折叠](https://www.w3school.com.cn/css/css_margin_collapsing.asp)
* [The Definitive Guide to Using Negative Margins](https://www.smashingmagazine.com/2009/07/the-definitive-guide-to-using-negative-margins/)
* [小 Tip: margin 负值详解](https://www.jianshu.com/p/6e4f5de683ae)

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

## 层叠上下文
todo

## 重绘与回流
todo
https://juejin.im/post/5a9923e9518825558251c96a
https://www.zhangxinxu.com/wordpress/2010/01/%E5%9B%9E%E6%B5%81%E4%B8%8E%E9%87%8D%E7%BB%98%EF%BC%9Acss%E6%80%A7%E8%83%BD%E8%AE%A9javascript%E5%8F%98%E6%85%A2%EF%BC%9F/

# JavaScript
## 闭包
闭包是函数和声明该函数的词法环境的组合。在 js 中, 函数运行在它们被定义的作用域, 而不是它们被执行的作用域。
通俗点的说法, 定义一个返回值是函数 B 的函数 A , 函数 B 在执行时可以访问函数 A 内部的变量及参数, 而其他函数无法访问 A 内部的变量及参数。函数 B 就被称为`闭包`。
闭包封住了变量作用域, 有效防止了全局污染, 但同时也存在内存泄漏的风险。
当然, 闭包也不是全无用处。比如以下场景: 假设我们现在需要写一个函数, 每次调用这个函数, 返回值都比上次调用时增加 1。这个时候就要用到闭包了。

```js
const times = (()=>{
  var times = 0;
  return () => times++;
})();

times(); // 1
times(); // 2
times(); // 3
times(); // 4
```

## 原型链以及实现原型继承的方式
### 原型链
关于原型链的描述, 只需要一张图就好。

![原型链01](/images/interview-experence/02.jpg)

如果更复杂一点的话, 可以看看这张图。

![原型图02](/images/interview-experence/23.jpg)

另一点值得注意的是, **箭头函数没有 `prototype` 属性, 但是有 `__proto__` 属性。**

### 实现原型继承的方式
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

### Class
在 ES6 中, 推出了 `class`。**JavaScript 中并没有真正的类, 一直以来都是通过构造函数 + 原型的方式来模拟类的行为的。**`class` 也不例外, 它只是语法糖, 本质还是函数。

类的数据类型就是函数, 类本身就指向构造函数。
使用的时候, 也是直接对类使用new命令, 跟构造函数的用法完全一致。
构造函数的prototype属性, 在 ES6 的“类”上面继续存在。事实上, 类的所有方法都定义在类的prototype属性上面。
prototype对象的constructor属性, 直接指向“类”的本身, 这与 ES5 的行为是一致的。
类的内部所有定义的方法, 都是不可枚举的(non-enumerable)。通过Objet.keys是拿不到的。
constructor方法是类的默认方法, 通过new命令生成对象实例时, 自动调用该方法。一个类必须有constructor方法, 如果没有显式定义, 一个空的constructor方法会被默认添加。
类必须使用new调用, 否则会报错。这是它跟普通构造函数的一个主要区别, 后者不用new也可以执行。

Class 可以通过extends关键字实现继承, 这比 ES5 的通过修改原型链实现继承, 要清晰和方便很多。
子类必须在constructor方法中调用super方法, 否则新建实例时会报错。这是因为子类自己的this对象, 必须先通过父类的构造函数完成塑造, 得到与父类同样的实例属性和方法, 然后再对其进行加工, 加上子类自己的实例属性和方法。如果不调用super方法, 子类就得不到this对象。
ES5 的继承, 实质是先创造子类的实例对象this, 然后再将父类的方法添加到this上面(Parent.apply(this))。ES6 的继承机制完全不同, 实质是先将父类实例对象的属性和方法, 加到this上面(所以必须先调用super方法), 然后再用子类的构造函数修改this。
如果子类没有定义constructor方法, 这个方法会被默认添加, 代码如下。也就是说, 不管有没有显式定义, 任何一个子类都有constructor方法。

class ColorPoint extends Point {
}

// 等同于
class ColorPoint extends Point {
  constructor(...args) {
    super(...args);
  }
}

另一个需要注意的地方是, 在子类的构造函数中, 只有调用super之后, 才可以使用this关键字, 否则会报错。这是因为子类实例的构建, 基于父类实例, 只有super方法才能调用父类实例。
todo

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
* `Set` 中存储的是不重复的值, 可以是简单值, 也可以是引用类型的值。
* `WeakSet` 只能用于存储引用类型的值, 存储简单值时会报错。**值得注意的是, `WeakSet` 对于对象的引用都是弱引用, 如果没有其他的对 `WeakSet` 中对象的引用, 那么这些对象会被当成垃圾回收掉。**这也意味着 `WeakSet` 中没有存储当前对象的列表。正因为这样, **`WeakSet` 是不可枚举的。**不支持 `forEach`、`for-of`、`keys`、`values` 方法, 没有 `size` 属性。

```js
let set = new Set();
let obj = {name: 'cc'};
set.add(obj);
obj = null;
[...set][0]; // {name: 'cc'} 转数组后依然可以访问到

let weakSet = new WeakSet();
let obj = {};
weakSet.add(obj);
obj = null;  // 会移除引用
weakSet.has(obj); // false
```

### Map 和 WeakMap 的区别
* `Map` 是一种增强 key/value 集合, 其 key 和 value 都是任意的。常用于创建哈希表。**当使用 get 访问其中某个元素时, 不会进行任何类型转换**
* `WeakMap` 只接收非 `null` 的对象作为 key, 否则会报错, value 可以是任意的。**值得注意的是, `WeakMap` 对于作为其 key 的对象的引用也是弱引用。因此, `WeakMap` 也是不可枚举的。**

```js
// 一般对象
const obj = Object.create(null);
obj[1] = 'cc';
obj['1']; // cc

// map
const map = new Map();
map.set(1, 'cc');
map.has('1');  // false   1 和 '1'不会被转换
```

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
<ul id='js-list'></ul>
```

需要向其中添加 3 个 `li` 元素, 如何添加?
下面是一份比较完善的代码:

```js
(() => {
    var ndContainer = document.getElementById('js-list');
    if (!ndContainer) {
        return;
    }

    // 方法1
    for (var i = 0; i < 3; i++) {
        var ndItem = document.createElement('li');
        ndItem.innerText = i + 1;
        ndContainer.appendChild(ndItem);
    }

    // 方法2
    var html = [];
    for (var i = 0; i < 3; i++) {
        html.push('<li>' + (i + 1) + '</li>');
    }
    container.innerHTML = html.join('');
})();
```

上面这段代码其实很简单, 但需要注意的地方有两点:
1. 我们使用了 IIFE 封锁了作用域, 避免产生全局变量, 也避免命名冲突的风险。
2. 对取不到对应节点的情形, 进行了额外的处理, 增加了代码的容错性。

那么问题来了, 如果要插入 30000 个节点, 那应该怎么做呢?
这就涉及到一个优化问题。数据量变大之后, 该如何操作 DOM 呢?
关于这个问题, 可以从两方面去解决:
* 懒加载。这里的懒加载是广义上的。由于节点非常多, 很多节点其实用户并不会看到, 并没有加载的必要。因此我们可以将节点分批加载, 既保证了首屏渲染速度, 又保证了性能。这一点可以利用 `requestAnimationFrame()` 来解决。
* 减少 DOM 操作的次数。这一点可以通过创建 `DocumentFragment` 来解决。

```js
(() => {
    const ndContainer = document.getElementById('js-list');
    if (!ndContainer) {
        return;
    }

    const total = 30000;
    const batchSize = 4; // 每批插入的节点次数, 越大越卡
    const batchCount = total / batchSize; // 需要批量处理多少次
    let batchDone = 0;  // 已经完成的批处理个数

    function appendItems() {
        // 创建 DocumentFragment 对象
        const fragment = document.createDocumentFragment();
        // 向 DocumentFragment 中添加内容
        for (let i = 0; i < batchSize; i++) {
            const ndItem = document.createElement('li');
            ndItem.innerText = (batchDone * batchSize) + i + 1;
            fragment.appendChild(ndItem);
        }

        // 每次批处理只修改 1 次 DOM
        ndContainer.appendChild(fragment);

        batchDone += 1;
        doBatchAppend();
    }

    function doBatchAppend() {
        if (batchDone < batchCount) {
            window.requestAnimationFrame(appendItems);
        }
    }

    // 开始执行
    doBatchAppend();
})();
```

更多关于 `requestAnimationFrame()` 和 `DocumentFragment` 的信息, 参见:
* [MDN: window.requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)
* [Using requestAnimationFrame](https://css-tricks.com/using-requestanimationframe/)
* [MDN: DocumentFragment](https://developer.mozilla.org/zh-CN/docs/Web/API/DocumentFragment)

### 如何绑定事件? 有什么区别?
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

之后是 `useCapture` 参数, 它是一个布尔值, 表示是否要使用事件捕获来触发事件。默认为 `false`。

和 `.on` 事件绑定语法一样, 当绑定的函数是普通函数时, 事件处理函数在执行时 this 指向触发事件的元素; 当绑定的函数是箭头函数时, 事件处理函数在执行时 this 指向 window(非严格模式)或 undefined(严格模式)。
因此, **使用箭头函数时, 原函数中可用的变量和常量在事件处理器中同样可用。**
另外, 还有一点非常重要, **`addEventLinstener()` 对任何 DOM 节点都是有效的, 而不仅仅只对 HTML 元素有效。**

### 事件流模型? 事件代理?
#### 事件流
当一个事件被触发时, 分为三个阶段:
1. 事件捕获阶段: 事件从根节点出发, 不断向子节点传递, 直到目标节点。沿途触发相同类型的事件。
2. 事件到达注册的目标元素上, 执行对应事件处理函数。
3. 事件冒泡阶段: 事件从目标元素出发, 一直冒泡到根节点, 沿途触发相同类型的事件。在此过程中, 可以在每个节点捕捉到相关事件。可以通过 `e.stopPropagation` 方法终止冒泡。

**事件捕获和事件冒泡只能有一个阶段触发事件。**这个通常是通过 `target.addEventLinstener` 的第三个参数来指定的。如果是 `true`, 即为在事件捕获阶段触发事件; 如果是 `false`, 即为在事件冒泡阶段触发事件, 此为默认值。

#### 事件代理
事件代理又名事件委托。
当节点很多时, 为了避免一一绑定事件带来的麻烦, 我们可以使用事件代理。
事件代理是利用事件冒泡的特性, 将子节点的事件绑定在父节点上, 然后再回调函数里针对事件对象的类型以及触发事件的 DOM 节点进行区分, 从而执行不同的操作。

### 如何阻止事件冒泡和浏览器默认行为?
#### 阻止事件冒泡?
w3c 的方法是 `e.stopPropagation()`, IE 则是使用 `e.cancelBubble = true`。

#### 阻止浏览器默认行为?
* w3c 的方法是 `e.preventDefault()`, IE 则是使用 `e.returnValue = false`。
* 事件处理函数返回 `false`。

### 获取页面元素位置与宽高?
`element.clientWidth` 和 `element.clientHeight` 可以分别用于获取元素宽高。获取到的宽高为 `content + padding + border`。

要获取位置, 可以使用 `element.getBoundingClientRect()` 方法。**要注意的是, 该方法返回的元素相对于视口的位置, 而不是元素在整个文档流中的位置。**返回值是一个对象, 包含 x、y、width、height、top、left、right、bottom。

## requestAnimationFrame
todo

## v8 的垃圾回收机制
todo
v8是什么?
JavaScript 是一门具有自动垃圾回收机制的编程语言, 主要有以下两种方式来回收内存。

### 引用计数
`引用计数(reference counting)`的含义是跟踪记录每个值被引用的次数。
当声明了一个变量并将一个引用类型值赋给该变量时, 则这个值的引用次数就是 1。如果同一个值又被赋给另一个变量, 则该值的引用次数加 1。相反, 如果包含对这个值引用的变量又取得了另外一个值, 则这个值的引用次数减 1。
当这个值的引用次数变成 0 时, 则说明没有办法再访问这个值了, 因而就可以将其占用的内存空间回收回来。这样, 当垃圾收集器下次再运行时, 它就会释放那些引用次数为零的值所占用的内存。
**这种方法的局限性在于, 无法清除循环引用的实例。**

### 标记-清除
这个算法把"对象是否不再需要"简化定义为"对象是否可以获得"。
这个算法假定设置一个叫做 root 的根对象(在 Javascript 里, root 即为全局对象)。垃圾回收器将定期从 root 开始, 找所有从 root 开始引用的对象, 然后找这些对象引用的对象....从 root 开始, 垃圾回收器将找到所有可以获得的对象, 之后回收所有不能获得的对象的内存。
**从 2012 年起, 所有现代浏览器都使用了标记-清除垃圾回收算法, 这也是 JavaScript 采用的垃圾回收机制。**

## v8 中一段代码的执行过程
在执行一段代码时, JS 引擎会首先创建一个执行栈
然后JS引擎会创建一个全局执行上下文, 并push到执行栈中, 这个过程JS引擎会为这段代码中所有变量分配内存并赋一个初始值（undefined）, 在创建完成后, JS引擎会进入执行阶段, 这个过程JS引擎会逐行的执行代码, 即为之前分配好内存的变量逐个赋值(真实值)。
如果这段代码中存在function的声明和调用, 那么JS引擎会创建一个函数执行上下文, 并push到执行栈中, 其创建和执行过程跟全局执行上下文一样。但有特殊情况, 即当函数中存在对其它函数的调用时, JS引擎会在父函数执行的过程中, 将子函数的全局执行上下文push到执行栈, 这也是为什么子函数能够访问到父函数内所声明的变量。
还有一种特殊情况是, 在子函数执行的过程中, 父函数已经return了, 这种情况下, JS引擎会将父函数的上下文从执行栈中移除, 与此同时, JS引擎会为还在执行的子函数上下文创建一个闭包, 这个闭包里保存了父函数内声明的变量及其赋值, 子函数仍然能够在其上下文中访问并使用这边变量/常量。当子函数执行完毕, JS引擎才会将子函数的上下文及闭包一并从执行栈中移除。
最后, JS引擎是单线程的, 那么它是如何处理高并发的呢？即当代码中存在异步调用时JS是如何执行的。比如setTimeout或fetch请求都是non-blocking的, 当异步调用代码触发时, JS引擎会将需要异步执行的代码移出调用栈, 直到等待到返回结果, JS引擎会立即将与之对应的回调函数push进任务队列中等待被调用, 当调用(执行)栈中已经没有需要被执行的代码时, JS引擎会立刻将任务队列中的回调函数逐个push进调用栈并执行。这个过程我们也称之为事件循环。
附言：需要更深入的了解JS引擎, 必须理解几个概念, 执行上下文, 闭包, 作用域, 作用域链, 以及事件循环。建议去网上多看看相关文章, 这里推荐一篇非常精彩的博客, 对于JS引擎的执行做了图形化的说明, 更加便于理解。
todo
https://tylermcginnis.com/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/?spm=ata.13261165.0.0.2d8e16798YR8lw

## 跨域问题
### 为什么会有跨域问题?
这就要说到浏览器`同源策略`了。
出于对保护敏感信息的考虑, 避免网站通过 `Cookie` 等手段获取到别的网站保存的敏感用户信息, 浏览器设置了同源策略, 具体就是:
JavaScript 在发送请求时, URL 的域名必须和当前页面完全一致。
完全一致是指域名要相同(如 www.baidu.com 和 baidu.com 是不同的), 协议要相同(http 和 https 不同), 端口要相同。

### 跨域发送 Ajax 请求的四种方法
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

##### CORS的两种请求
* 简单请求(同时满足以下三个条件):
 1. 请求方法是 `HEAD`、`GET`、`POST` 的三者之一。
 2. HTTP头信息不超出以下几种字段: `Accept`、`Accept-Language`、`Content-Language`、`Content-Type`、`DPR`、`DownLink`、`Save—Data`、`Width`、`Viewport-Width`。
 3. `Content-type` 的值为下列三者之一: `application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`。

* 非简单请求: 不是简单请求的就是非简单请求。

##### 简单请求
简单来说, 简单请求就是简单请求方法和简单HTTP头信息的结合。
在 JavaScript 向外域发送一个请求后, 浏览器收到响应后会先检查响应的 `Access-Control-Allow-Origin` 是否包含本域。若包含则请求成功, 若不包含则浏览器会报错, 且 JavaScript 收不到任何响应。
**简单请求其实就是表单请求。**表单请求一直都可以发起跨域请求。
**综上所述, 简单跨域请求能否成功的关键在于让对方的服务器返回一个正确的 `Access-Control-Allow-Origin`。**

##### 非简单请求
对于非简单请求来说, 浏览器会先发送`预检请求(preflight)`, 询问服务器当前网页能不能发送 CORS 请求。预检请求的请求方法是 `OPTIONS`。
在预检请求中, 头部必须包含三个字段:
* `origin`: 发送请求的域名。
* `Access-Control-Request-Method`: 实际请求使用的请求方法。
* `Access-Control-Request-Headers`: 实际请求将携带的、不属于简单请求范畴的首部字段。

根据以上字段, 服务器会判断是否接受实际请求。

在预检请求的响应中, 头部信息有四个需要注意的字段:
* `Access-Control-Allow-Origin`: 服务器允许访问该资源的外域 URI。如果是 `*`, 则表明允许来自所有域的请求。
* ` Access-Control-Allow-Methods`: 服务器允许客户端使用的请求方法。
* `Access-Control-Allow-Headers`: 服务器允许请求头中携带的字段, 一般与 `Access-Control-Request-Headers` 相对应。
* `Access-Control-Max-Age`: 这个字段的值一般为一个数字, 单位是秒。表示该响应的有效时间。在有效时间内, 浏览器无须为统一请求再次发起预检请求。应当注意的是, **浏览器自身也维护了一个默认的预检请求最大有效时间, 如果该首部字段的值超过了浏览器的最大有效时间, 则该字段不会生效。**

如果服务器同意请求, 则服务器会返回一个包含了 `Access-Control-Allow-Origin` 等字段的响应, 浏览器收到响应以后检查对应字段, 确认允许跨源请求, 就可以做出回应。
如果服务器否定请求, 则服务器会返回一个不包含任何 CORS 相关字段的响应, 或者明确表示请求不符合条件。之后浏览器会抛出一个错误, 可被 `XMLHttpRequest` 对象的 `onerror` 回调函数捕获。
一旦服务器通过了预检请求, 以后每次浏览器发送 CORS 请求, 就都跟简单请求一样, 会有一个 `Origin` 头信息字段。服务器的回应, 也都会有一个 `Access-Control-Allow-Origin` 头信息字段。
另外还有一点值得注意, **在最初的规范中, CORS 规定预检请求是不能重定向的,** 如果一个预检请求发生了重定向, 浏览器将抛出一个错误。**不过在后续的修订中废弃了这一要求。**

##### 附带身份验证信息的请求
附带身份验证信息的请求也属于简单请求或非简单请求的一种, 但会有些额外的处理, 因此需要另外说明一下。
一般而言, 对于跨域 `XMLHttpRequest` 或 `Fetch` 请求, 浏览器不会发送身份凭证信息。如果要发送凭证信息, 需要设置 `XMLHttpRequest` 的 `withCredentials` 标志位, 将其设置为 `true`。之后, 便可以发送带有身份验证信息的请求了。
但是, 如果服务器端的响应中未携带 `Access-Control-Allow-Credentials: true`, 浏览器将不会把响应内容返回给请求的发送者。
对于附带身份凭证的请求, 服务器不得设置  `Access-Control-Allow-Origin` 的值为 `*`, 否则请求会失败。

#### http 的 proxy
todo
#### document.domain
当二级域名相同时, 例如a.test.html和b.test.html, 只需要给两个页面都设置document.domain = 'test.html', 就可以实现跨域。
todo

#### postMessage
如a.html页面通过iframe嵌入了b.html页面, 其中一个可以通过postMessage方法发送信息, 另一页面通过监听message事件判断来源并接受消息。
todo

### 规避 Cookie 同源策略的方法
Cookie 是服务器写入浏览器的一小段信息。由于同源策略的存在, 只有同源的页面才能共享 Cookie。
那么, 如何才能规避这种影响, 让不同源的页面也访问到 Cookie 呢?

#### document.domain
一般来说, `document.domain` 指向的都是原始域名。但是这个属性是可写的, 因此可以通过更改该属性, 实现不同页面之间共享 Cookie。
应当注意的是, 如果成功设置此属性, 则原始端口的端口部分也将设置为 `null`。

举个例子。假设我们有一个 
todo

**这种方法只适用于 Cookie 和 iframe 窗口, LocalStorage 和 IndexDB 无法通过这种方法, 规避同源政策, 而要使用下文介绍的PostMessage API。**
另外, document.domain 并不是可以任意赋值的。

#### 设置 Cookie 的 path 或 domain
https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie

### 规避 iframe 同源策略的方法

https://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html

#### 参考链接
* [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
* [浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
* [CORS 通信](http://javascript.ruanyifeng.com/bom/cors.html)
* [document.domain 解决跨域问题, 详细讲解](https://www.sojson.com/blog/179.html)
* [Document.cookie](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie)
* [MDN: HTTP 访问控制(CORS)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

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

### 关于 instanceof 的扩展知识
这里关于 `instanceof` 还要多说两句。

#### instanceof 的迷惑行为大赏
首先我们来看一些关于 `instanceof` 的迷惑行为:

```js
console.log(Object instanceof Object); //true 
console.log(Function instanceof Function); //true 
console.log(Number instanceof Number); //false 
console.log(String instanceof String); //false 
 
console.log(Function instanceof Object); //true 
 
console.log(Foo instanceof Function); //true 
console.log(Foo instanceof Foo); //false
```

要理解上面的行为, 我们首先要看看在 JavaScript 中, `instanceof` 的判断逻辑是怎样的。

```js
//L 表示左表达式, R 表示右表达式
function instance_of(L, R) {
    var O = R.prototype; // 取 R 的显示原型
    L = L.__proto__; // 取 L 的隐式原型
    while (true) { 
        if (L === null) 
            return false; 
        if (O === L) // 这里重点: 当 O 严格等于 L 时, 返回 true 
            return true; 
        L = L.__proto__; 
    } 
}
```

配合这张图看, 事情就变得清晰起来了。

![原型链](/images/interview-experence/23.jpg)

总结一下, 有以下三点:
1. 所有对象和函数 `instance Object` 都返回 `true`。
2. 所有函数 `instanceof Function` 都返回 `true`。
3. 除 `Object` 和 `Function` 之外的构造函数 `instanceof` 自身都返回 `false`。**因为构造函数的原型链上只有`Function.prototype` 和 `Object.prototype` 而没有它们自身的 `prototype`, 这一点很不容易理解!**

#### instanceof 能否判断简单值?
接下来是 `instanceof` 是否能判断简单值的问题, 答案是**能**。原理也很简单, 那就是使用 `[Symbol.hasInstance]`, 这个属性常用于判断某对象是否为某构造函数的实例。因此你可以用它自定义 `instanceof` 操作符在某个类上的行为, **包括 JavaScript 的内置类, 如 Number、String 等**。

```js
class Number {
    static [Symbol.hasInstance](instance) {
        if (typeof instance === 'number') {
            return true;
        }

        // instanceof 操作符原有的判断逻辑
        let left = instance.__proto__; // 取等式左边对象的隐式原型
        let right = this.prototype; // 取等式右边对象的显示原型
        while (true) {
            if (left === null)
                return false;
            if (right === left)// 这里重点: 当右边的对象严格等于左边的对象时, 返回 true 
                return true;
            left = left.__proto__;
        }
    }
}

console.log(111 instanceof Number); // true
console.log(new Number(123) instanceof Number); // true
```

上面的代码相当于重写了内置 `Number` 函数, 虽然可以让我们通过 `instanceof` 判断简单值, 但也有副作用, 那就是**使用 `class` 声明的 `Number` 函数不能在没有 `new` 的时候调用了, 否则会报错。**

### 参考资料
* [Symbol.hasInstance](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol/hasInstance)
* [JavaScript instanceof 运算符深入剖析](https://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/index.html)

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

## getter 与 setter
首先说明参数。
* `get` 方法没有参数。当访问该属性时, 该方法会被执行。方法执行时虽然没有参数传入, 但是会传入 this 对象。
* `set` 方法将接受唯一参数, 即该属性新的参数值。当属性值修改时, 触发执行该方法。

其次说明用途, `get` 与 `set` 常常是在 `Object.setProperty()` 和 `Object.setProperties()` 中使用的。二者结合可以定义一个伪属性, 这个伪属性的用途一般是对真实属性的数据进行加工。也可用来为真实属性添加额外操作。
最重要的是, **这两个属性要设就设一对,** 不要只设一个, 不然会出现意想不到的行为。
至于循环引用的问题, 只要 get 方法返回 `this[属性名]` 就不会出现这个问题了。

## for...in 和 for...of
for..in 遍历的是对象的键名, 对于数组来说, 就是数组的下标。
for..of 遍历的是数组的值, **不能直接用于遍历对象。**
**两者都不仅会遍历对象本身的属性, 还会沿着对象的原型链向上, 并遍历沿途所有的对象, 直到 `Object.prototype` 为止。**

## JavaScript 中的数字
### 从 0.1 + 0.2 != 0.3 说起
todo

## `CommonJS` 与 ES6 中模块引入的区别?
`CommonJS` 是一种模块规范, 最初被应用于 Nodejs, 成为 Node.js 的模块规范。运行在浏览器端的 JavaScript 由于也缺少类似的规范, 在 ES6 出来之前, 前端也实现了一套相同的模块规范(例如: AMD), 用来对前端模块进行管理。
自 ES6 起, 引入了一套新的 `ES6 Module` 规范, 在语言标准的层面上实现了模块功能, 而且实现得相当简单, 有望成为浏览器和服务器通用的模块解决方案。**但目前浏览器对 `ES6 Module` 兼容还不太好**, 我们平时在 Webpack 中使用的 export 和 import, 会经过 Babel 转换为 `CommonJS` 规范。

在语法上, 二者的差别如下:
* `CommonJS` 模块输入和输出的关键字是 `require()` 和 `module.exports`/`exports`。
`require()` 接收一个模块的文件路径作为参数, 返回这个模块。
`module.exports` 和 `exports` 指向的是同一个对象, 使用 `require()` 读取后返回的也是这个对象。我们可以把要导出的变量作为属性挂载在这个对象上。
**最终导出时, 导出的是 `module.exports` 指向的对象。**也就是说, 我们可以改变 `exports` 指向的对象, 这对最终导出的结果没有影响, 但会破坏 `module.exports` 和 `exports` 指向的一致性, 因此是不推荐的。
下面是具体的例子:
```js
// 例程一
// ModuleA.js
module.exports.a = 1;
exports.b = 2;
// test.js
let A = require('./ModuleA.js');
// {a: 1, b: 2}
console.log(A);

// 例程二
// ModuleA.js
module.exports = { a: 1 };
exports = { b: 2 };

// test.js
let A = require('./ModuleA.js');
// {a: 1}
console.log(A);
```

* ES6 中, 模块输入和输出使用的关键字分别是 `import ... from ...` 和 `export`/`export default`。
**注意: 一个文件只能有一个 `export defalut`, 但 `export` 可以存在多个, 且可以和 `export default` 一起使用。**
至于具体怎么使用, 还是看例子吧:

```js
// module.js
const a = 1;
const b = 2;
const c = 3;
export function fn() {
    // ...
}
export {
    a,
    b,
}
export default c;
// 注意到, export 和 export default 后面既可以跟对象, 也可以跟变量的声明

// test.js
// 可以通过 as 关键字来对导出模块中的变量进行重命名
import * as Module from './module.js';
// {a: 1, b: 2, fn: function, default: 3}
console.log(Module);

import { a, b, c } from './module.js';
// 1 2 3
console.log(a, b, c)

import x from './module.js';
// 3
console.log(x);

import {a as x, default as y} from './module.js';
// 1 3
console.log(x, y);
```

梳理完了语法上的差别, 该看看在使用上的差别了:
1. `CommonJS` 模块输出的是一个值的浅拷贝, `ES6 Module` 输出的是值的引用。之所以输出拷贝, 主要是为了防止某个模块在其他文件中引用并修改后, 后续别的文件引用该模块时出现值被篡改的问题。
2. `CommonJS` 模块是运行时加载, `ES6 Module` 是编译时输出接口。
3. `CommonJs` 是动态语法可以写在判断里, `ES6 Module` 静态语法只能写在顶层。
4. `CommonJs` 的 this 是当前模块, `ES6 Module` 的 this 是 `undefined`。
5. `CommonJS` 加载的是整个模块, 将所有的变量全部加载进来; `ES6 Module` 可以单独加载其中的某个变量。

## 常用数组遍历方法
map、reduce、forEach、
map 是常用的数组遍历方法之一。
```js
var a = [];

a[5] = undefined;
a[10] = undefined;

// fn 只会执行两次, 分别是 5、10, 其他时候不会执行
// 内部原理是使用 Object.prototype.hasOwnProperty()
// a.hasOwnProperty(0) === false
// a.hasOwnProperty(5) === true
a.map(fn);

// map: 生成一个新数组, 遍历原数组, 
// 将每个元素拿出来做一些变换然后放入到新的数组中
let newArr = [1, 2, 3].map(item => item * 2);
console.log(`New array is ${newArr}`);

// filter: 数组过滤, 根据返回的boolean
// 决定是否添加到数组中
let newArr2 = [1, 2, 4, 6].filter(item => item !== 6);
console.log(`New array2 is ${newArr2}`);

// reduce: 结果汇总为单个返回值
// acc: 累计值; current: 当前item
let arr = [1, 2, 3];
const sum = arr.reduce((acc, current) => acc + current);
const sum2 = arr.reduce((acc, current) => acc + current, 100);
console.log(sum); // 6
console.log(sum2); // 106
```
todo

## 常见浏览器安全问题及防范措施
### XSS
`XSS` 全称是 `Cross Site Scripting, 跨站脚本`, 为了与 `CSS` 区分, 所以叫它 `XSS`。
`XSS` 的最终目的是在用户浏览器中执行恶意 JavaScript 代码, 以此来获取用户的 Cookie 等敏感信息, 或实现监听用户行为等目的。

#### XSS 的攻击方式
为了实现这一目的, 主要有以下几种方法:
1. 向服务器提交包含恶意代码的文件, 使服务器返回给客户端的文件包含恶意代码, 然后在客户端中执行, 以此来实现攻击的效果。
常见的场景就是在评论区中提交评论, 如果前后端转义不完善的话, 包含恶意代码的评论就会储存在数据库中, 并返回给每一个请求网页的用户。
2. 作为中间人, 在数据传输过程劫持到网络数据包, 然后修改里面的文件, 使其包含恶意代码。
这种劫持方式包括 `WIFI路由器劫持`或`本地恶意软件`等。

#### 防范 XSS 攻击的措施
XSS 的防范措施也很简单:
1. **永远不要相信任何用户的输入!**无论是前端还是后端, 都要对用户的输入进行转码和过滤。主要过滤的字符是: `<`、`>`、`'`、`"`、`/`、`\`。
2. `CSP(浏览器中的内容安全策略)`, 它的核心思想就是服务器决定浏览器加载哪些资源, 具体来说可以完成以下功能:
* 限制其他域下的资源加载。
* 禁止向其它域提交数据。
* 提供上报机制, 能帮助我们及时发现 XSS 攻击。

3. 许多 XSS 脚本都是用来窃取 Cookie 的。因此在设置 Cookie 时, 将其设置为 `HttpOnly`, 这样 JavaScript 就无法读取 Cookie 的值了, 可以有效防范 XSS 攻击。

### CSRF
`CSRF(Cross-site request forgery)`, 即跨站请求伪造, 指的是黑客诱导用户点击链接, 打开黑客的网站, 然后黑客利用用户目前的登录状态(通常是 Cookie)发起跨站请求。

#### CSRF 的攻击方式
CSRF 攻击一般有三种方式:
* 自动发送 GET 请求。
* 自动发送 POST 请求。
* 诱导点击发送 GET 请求。

#### 防范 CSRF 攻击的措施
防范 CSRF 攻击的措施主要有以下几种:
1. 检测来源站点。方法是在服务器端检查请求头中 `Origin` 和 `Referer` 字段, 其中 `Origin` 只包含域名信息, `Referer` 则包含了具体的 URL 路径。
当然, 这两者都是可以伪造的, 因此这种方法可靠性较差。
2. 避免保存登录态的 `Cookie` 长时间存储在客户端中。
这点说起来很容易, 做起来却很困难。因为无法有效判断多长的时间最合适, 因此这种方法也不太可靠。
3. 利用 Cookie 的 `SameSite` 属性, 防范 CSRF 攻击。
在 Cookie 当中有一个关键的字段, 可以对请求中 Cookie 的携带作一些限制, 这个字段就是 `SameSite`。
`SameSite` 可以设置为三个值: `Strict`、`Lax` 和 `None`。
* 在 `Strict` 模式下, 浏览器完全禁止第三方请求携带 Cookie。比如请求 `a.com` 网站只能在 `a.com` 域名当中请求才能携带 Cookie, 在其他网站请求都不能。
* 在 `Lax` 模式, 就宽松一点了, 但是只能在 GET 方法提交表单况或者 `<a>` 标签发送 GET 请求的情况下可以携带 Cookie, 其他情况均不能。
* 在 `None` 模式下, 也就是默认模式, 请求会自动携带上 Cookie。

4. 关键请求使用验证码或者 `token` 机制。
在一些十分关键的操作, 比如交易付款环节的请求中, 加入验证码或 `token` 机制, 可以防止 CSRF 攻击。
验证码很好理解, 那么 `token` 机制的原理是什么呢?
具体来说就是服务器返回客户端页面的时候, 随机生成一个字符串, 植入返回的页面中。
之后, 客户端请求的时候带上这个字符串, 然后由服务器验证是否合法, 如果不合法则拒绝响应。这个字符串就是 `token`。一般来说, 第三方站点是无法拿到 `token` 的, 因此请求就会被服务器拒绝。

## 前端如何处理大批量数据?
### 减少 DOM 操作
DOM 操作过多确实会非常影响性能。如果实在要求很多的 DOM 操作, 可以试试使用 `DocumentFragment` 对象, 将要操作的结果保存下来, 最后进行一次规模较大的替换。而不是每次都直接在 DOM 上插入, 这样会多次触发回流, 影响性能。

### 懒加载
这里的懒加载是广义上的。
由于节点非常多, 很多节点其实用户并不会看到, 并没有加载的必要。因此我们可以将节点分批加载, 既保证了首屏渲染速度, 又保证了性能。这一点可以利用 `requestAnimationFrame()` 来解决。

### Web Worker
如果上面两个手段都试过了, 还是不能满足要求, 就来试试我们的大杀器—— `Web Worker` 吧。
todo
#### 什么是 
https://juejin.im/post/5cb03fbee51d456e853f810b

### 后端
如果上面的方案都解决不了, 那我们就丢给后端同学吧。嘻嘻嘻。

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
// 最可靠
function unique(arr) {
    return [...new Set(arr)];
}

// 有缺陷, 不会去重 NaN
function unique(arr) {
    return arr.filter((val, index, arr) => {
        return arr.indexOf(val) === index;
    });
}

// 改进版: 使用 includes 判断前面的元素中是否有当前元素, 可以对 NaN 进行去重
function unique(arr) {
    return arr.filter((val, index, arr) => {
        return !arr.slice(0, index).includes(val);
    });
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
    let previous = +new Date();

    return function () {
        let now = +new Date();
        let context = this;
        let args = Array.from(arguments);

        if (now - previous > wait) {
            previous = +new Date();
            func.apply(context, args);
        }
    }
}

// 使用定时器
function throttle(func, wait) {
    let timeout = null;

    return function() {
        let context = this;
        let args = Array.from(arguments);

        if (!timeout) {
            timeout = setTimeout(function() {
                func.apply(context, args);
                timeout = null;
            }, delay);
        }
    }
}

function throttle(func, delay) {
    let prev = +new Date();
    let timeout = null;
    
    return function(...args) {
        let now = + new Date();
        let context = this;
        
        if(now - prev > delay) {
            prev = + new Date();
            func.apply(context, args)
        } else {
            clearTimeout(timeout);
            timeout = setTimeout(() => {
                prev = +new Date();
                func.apply(context, args);
            }, delay - (now - prev));
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

## js深入系列
todo
https://github.com/mqyqingfeng/Blog

## JavaScript 如何实现按首字母对数组排序?
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare
https://segmentfault.com/q/1010000002546028
todo

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
这部分应该是 `reconciler` 和 `render` 的内容, 在这里不做赘述。

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

## 如何实现一个 Virtual DOM?
### Virtual DOM 的更新思路
首先我们要理解 Virtual DOM 的更新思路。主要分三步:
1. 用 JavaScript 对象结构表示 DOM 树的结构; 然后用这个树构建一个真正的 DOM 树, 插到文档当中。
2. 当状态变更的时候, 重新构造一棵新的对象树。然后用新的树和旧的树进行比较, 记录两棵树差异。
3. 把2所记录的差异应用到步骤1所构建的真正的 DOM 树上, 视图就更新了。

### todo

### 参考资料
* [深度剖析: 如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)

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
https://medium.com/react-in-depth/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-67f1014d0eb7

## React diff 算法
React 把 diff 算法的时间复杂度从 O(n^3) 降到了 O(n)。

### 传统 diff 的复杂度 O(n^3) 是怎么来的?
传统 diff 算法会将新树中的每个节点与旧树中的所有节点一一进行比较, 这就有 n^2 的复杂度了。
之后还需要编辑树, 编辑的树可能发生在任何节点, 需要对树进行再一次遍历操作, 因此复杂度为 n。加起来就是 n^3。

### React 的 diff 策略
* 把树形结构按层级分解, 同级元素只与同级元素进行比较, 而不是与所有元素进行比较。
* 给列表结构添加 `key` 属性, 标记不同的 DOM 节点, 以便在数据变化时复用。
* React 只会匹配相同名称的组件。
* 合并操作。调用 `setState` 方法, 并不会立即执行更新, 而是会将当前组件标记为 `dirty`。在一个事件循环结束后, React 会检查所有标记为 `dirty` 的组件, 并在下一个事件循环中对其进行更新。
* 开发人员可以使用 `shouldComponentUpdate` 来避免不必要的 diff, 提高性能。

## React 的事件系统
React 自己实现了一套事件机制, 其将所有绑定在虚拟 DOM 上的事件映射到真正的 DOM 事件, 并将所有的事件都代理到 `document` 上, 自己模拟了事件冒泡和捕获的过程, 并且进行统一的事件分发。

React 自己构造了合成事件对象 `SyntheticEvent`, 这是一个跨浏览器原生事件包装器。 它具有与浏览器原生事件相同的接口, 包括 `stopPropagation()` 和 `preventDefault()` 等等, 在所有浏览器中他们工作方式都相同。这抹平了各个浏览器的事件兼容性问题。

到这里, 我们已经对 React 的事件系统有了大致了解。接下来的内容, 要回答下面五个问题:
1. 为什么要手动绑定 `this`?
2. React 事件和原生事件有什么区别?
3. React 事件和原生事件的执行顺序? 是否可以混用?
4. React 事件系统是如何实现跨浏览器兼容的?
5. 什么是合成事件?

为了回答上面的问题, 我们首先要看看, 一个事件是如何在 React 中注册、存储和触发的。

### React 是如何注册、存储和触发事件的?
首先我们来看事件是如何在 React 注册的。
todo

### 参考资料
* [\[React 深入\]React 事件机制](https://segmentfault.com/a/1190000018391074)

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

## 调用 setState 之后发生了什么?
在代码中调用 setState 函数之后, React 会将传入的参数对象与组件当前的状态合并, 然后触发所谓的`调和过程(Reconciliation)`。
所谓调和过程, 其实就是 diff + 计算最小操作。
首先 React 通过 diff 算法找出需要更新的节点。之后, 再计算出更新所需的最小操作。这些过程都是在虚拟 DOM 中完成的。
在计算完最小操作后, React 便会开始进行更新, 渲染页面。

## 在 React 中, Element、Component 与 Instance 有什么区别?
**元素(element)**是一个用于描述你想在屏幕上显示什么 DOM 节点或组件的普通对象。元素可以在 props 中包含其他元素。创建一个元素的代价十分低廉, 而一旦一个元素被创建, 这个对象便是不可变的。
**组件(component)**可以通过多种方式进行定义。它可以定义为一种具有 `render()` 方法的类, 也可以简单地定义为一个函数。无论哪一种定义方式, 都会把 props 作为输入, 返回一个元素树作为输出。
如果一个组件接受到了一些 props 作为输入, 那是由于某个特定的父组件返回了带有其 `type` 和这些 props 的元素树。这就是为何人们总说在 React 中数据的流向是单向的：从父组件到子组件。
**实例(instance)**是你在编写类组件时使用 `this` 来指代的对象。它对于[存储局部状态以及相应生命周期事件](https://react.docschina.org/docs/component-api.html)十分有用。
函数组件并没有实例。类组件虽然具有实例, 但并不需要你自己进行管理, React 会负责所有的实例管理工作。

以上摘自[React Components, Elements, and Instances](https://react.docschina.org/blog/2015/12/18/react-components-elements-and-instances.html), 我对本文也做了翻译, 可以看[这里](https://xdyushenli.github.io/2019/12/17/react-components-elements-and-instances/#more)。

## React 组件通信
### 父组件向子组件通信
### 子组件向父组件通信
### 跨级组件通信
### 没有嵌套关系组件之间的通信
todo

## React Hooks
### Hooks 解决了什么问题?
todo

## 容器组件和展示组件的区别
`展示组件(presentational component)`是指专门用于展示数据的组件, 内部不含任何业务逻辑。
`容器组件(container component)`是指用于控制展示组件行为, 内部含有业务逻辑的组件。

## React Router 实现原理
todo

## Redux
这里来说一下 Redux 的缺点吧。
* 一个组件所需的数据必须由父组件传进去, 而不能像 Flux 一样直接从 store 中获取。
* 当一个组件相关的数据更新时, 即使父组件不需要用到这个组件, 父组件还是会重新渲染。弥补措施就是使用 `shouldComponentUpdate` 进行判断。
todo

## React 服务端渲染
todo

# Vue 
## 组件通信
vuex 和 redux 都是将数据存储在内部状态中的, 页面刷新后就消失了。
组件通信还可以利用本地化存储技术, 比如 localstorage 等, 实现跨组件通信。
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
2. 实现了多路复用。一个 tcp 连接可并发多个 HTTP 请求。
可一个 tcp 连接可承载任意多个双向数据流, 每个请求对应一个数据流, 数据拆分为帧进行发送, 每个帧头部都有流标识 id, 这些帧可以乱序发送, 再根据流标识 id 进行重组。为避免关键请求被阻塞, 可为数据流设置优先级, 优先级高的数据流优先处理。这一改进解决了线头阻塞问题。
3. 头部压缩。为了减少请求头的大小, 客户端和服务端分别维护相同的静态字典(常用头部名称及值)和动态字典(可动态添加内容)。通过传输序号和字典查询再加上合适的压缩算法可大大减少头部大小。
4. 服务器推送。服务器可主动向客户端推动内容。

## http 与 https 的区别
主要有下面四点:
1. https 协议需要到 CA 申请证书, 一般免费证书较少, 因而需要一定费用。
2. http 是超文本传输协议, 信息是明文传输; https 则是具有安全性的 ssl 加密传输协议。
3. http 和 https 使用的是完全不同的连接方式, 用的端口也不一样, 前者是 80, 后者是 443。
4. http 的连接很简单, 是无状态的; https 协议是由 http + ssl 构建的可进行加密传输、身份认证的网络协议, 比 http 协议安全。
5. http 页面响应速度比 https 快。主要是因为 http 使用 TCP 三次握手建立连接, 客户端和服务器需要交换 3 个包; 而 https 除了 TCP 的 3 个包, 还要加上 ssl 握手需要的 9 个包, 所以一共是 12 个包。

### https 的工作流程

![https](/images/interview-experence/31.png)


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

## 常用 HTTP 请求方法及区别
| 方法 | 描述 |
| -- | -- |
| GET | 请求指定的页面信息, 并返回实体主体 |
| HEAD | 类似于 GET 请求, 只不过返回的响应中没有具体的内容, 用于获取报头 |
| POST | 向指定资源提交数据进行处理请求(例如提交表单或者上传文件)。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和 / 或已有资源的修改 |
| PUT | 从客户端向服务器传送的数据取代指定的文档的内容 |
| DELETE | 请求服务器删除指定的页面 |
| OPTIONS | 用于获取服务器所支持的通信选项, 比如方法、允许请求的域名等 |
| TRACE | 回显服务器收到的请求, 主要用于测试或诊断 |
| CONNECT | 把服务器作为跳板, 让服务器代为访问其他网页 |
| PATCH | 对 PUT 方法的补充, 用来对已有资源进行局部更新 |

### GET 与 POST 的区别
关于这个问题, 答案分两部分。一部分来自 w3c, 属于标准答案:
* GET 在浏览器回退时是无害的, 而 POST 会再次提交请求。
* GET 产生的 URL 地址可以被 Bookmark, 而POST不可以。
* GET 请求会被浏览器主动 cache, 而 POST 不会, 除非手动设置。
* GET 请求只能进行 URL 编码, 而 POST 支持多种编码方式。
* GET 请求参数会被完整保留在浏览器历史记录里, 而 POST 中的参数不会被保留。
* GET 请求在 URL 中传送的参数是有长度限制的(一般浏览器设置 URL 最大长度为 2K), 而 POST 没有。
* 对参数的数据类型, GET 只接受 ASCII 字符, 而 POST 没有限制。
* GET 比 POST 更不安全, 因为参数直接暴露在 URL 上, 所以不能用来传递敏感信息。
* GET 参数通过 URL 传递, POST 放在请求体中。

另一部分, 就涉及到 TCP 的知识了。
GET 与 POST 的另一点不同, 在于其发送的 TCP 包的数量不同。**GET 方法发送一个 TCP 包, POST 方法发送两个 TCP 包。**
具体来说就是, 对于 GET 方式的请求, 浏览器会把 http header 和 data 一并发送出去, 服务器响应 `200 ok`(返回数据)。
而对于 POST, 浏览器先发送 header, 服务器响应 `100 continue`, 浏览器再发送 data, 服务器响应 `200 ok`(返回数据)。
应该注意的是, **并不是所有浏览器都会在POST中发送两次包, Firefox就只发送一次。**

### POST、PUT、PATCH 的区别
首先我们要了解一个概念, `幂等(Idempotency)`。
在 HTTP 中经常能见到这个概念。我们常说的某个请求方法是幂等的, 就是说, 使用该方法传输同样的数据, 无论传输多少次, 结果都是相同的(这里的结果不仅仅包括返回的结果, 也包括其对服务器产生的影响)。典型的幂等方法有: PUT 和 GET。

相对应, 非幂等就是说一个请求执行若干次, 其结果是不同的。这类请求方法的典型是: POST、PATCH 和 DELETE。
以 POST 为例, 

之所以要划分幂等和非幂等的概念, 主要是因为**幂等请求在请求失败后可以放心得重新请求, 而非幂等请求则不能。**如提交一个支付(非幂等)时网络超时了, 客户端不会允许再次提交相同的请求, 如果超时发生在服务端返回客户端的过程中则会造成两次支付。

另外一个概念是`安全(safe)`。如果说一个 HTTP 方法是安全的, 是指这是个不会修改服务器的数据的方法。也就是说, 这是一个对服务器只读操作的方法。安全的方法只有 GET、HEAD 与 OPTIONS, 其他请求方法都是非安全的。

之后便要来阐释下具体的区别了。
首先是 POST。**在语义上, POST 表示新建资源, 是非幂等的。**多次执行, 将会导致多条相同的数据被创建。

之后是 PUT。**在语义上, PUT 表示更新资源(其实叫替换或创建更准确), 是幂等的。**
客户端对一个 URI 发送一个资源, 如果服务器之前没有资源 , 那么服务器就应该将客户端提交的放在这个 URI 上; 如果服务器在这个 URI 下如果已经又了一个资源, 那么此刻服务器应该将其替换成客户端提交的资源。由此保证了PUT的幂等性。
总结一下: PUT 方法, 其行为与其字面意义一致, 就是将客户端资源放在请求的 URI 位置。至于服务器到底是创建还是更新, 由服务器自己决定, 客户端也可以根据服务器返回的状态码来区别。
注意: **通过上面可以知道, 如果用 PUT 来更新资源, 需要客户端提交资源全部信息; 如果只有部分信息, 不应该使用 PUT(因为服务器使用客户端提交的对象整体替换服务器的资源)。**

最后是 PATCH。**在语义上, 其与 PUT 类似, 均表示更新资源。但 PATCH 是局部更新, 且是非幂等的。**
那么问题来了, 为什么 PUT 和 PATCH 在语义上都表示更新资源, 但 PATCH 不是幂等的呢?
这里我们可以看看在 [RFC5789](https://tools.ietf.org/html/rfc5789) 中对于 PUT 和 PATCH 的描述:
> PUT 和 PATCH 请求的区别体现在服务器处理封闭实体以修改 Request URI 指向的资源的方式。
> 在一个 PUT 请求中, 封闭实体被认为是存储在源服务器上的资源的修改版本, 并且客户端正在请求替换存储的版本。
> 而对于 PATCH 请求, 封闭实体中包含了一组描述当前保留在源服务器上的资源应该如何被修改来产生一个新版本的指令。PATCH 方法影响由 Request URI 标志的资源, 而且它也可能对其他资源有副作用; 也就是, 通过使用 PATCH, 新资源可能被创造, 或者现有资源被修改。

**也就是说, PUT 传输的是更新后的资源, 服务器收到后只需要进行创建或替换就好, 因此是幂等的; 而 PATCH 传输的是一系列用于更新的指令, 这些指令在执行的过程中可能会产生副作用, 所以 PATCH 是非幂等的。**

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

## Cookie: token 与 session 的爱恨情仇
todo

## 内网穿透
https://zhuanlan.zhihu.com/p/30351943
### 什么是内网穿透?

### 为什么我们需要内网穿透?
todo

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

## VPN 相关
todo
VPN 对于用户的安全还是有一定作用的, 不过要小心 VPN 提供商对用户信息的窃取。
VPN属于网络安全设备，是为了防止第三方非法入侵的设备，起到保护内部网络不受入侵的作用。 它是目前最常用的信息安全隧道技术，是利用加密技术在公网上封装出一个数据通讯隧道，利用虚拟专用网建立一条加密的可靠的数据连接信道，让信息传递更加安全。
网络隧道技术?

一 VPN概述
　　
　　 为了使得远程的企业员工可以与总部实时的交换数据信息。企业得向ISP租用网络提供服务。但公用网容易遭受各种安全攻击（比如拒绝服务攻击来堵塞正常的网络服务，或窃取重要的企业内部信息）
　　
　　 VPN这个概念的引进就是用来解决这个问题。它是利用公用网络来连接到企业私有网络。但在VPN中，用安全机制来保障机密型，真实可靠行，完整性严格的访问控制。这样就建立了一个逻辑上虚拟的私有网络。虚拟局域网提供了一个经济有效的手段来解决通过公用网络安全的交换私有信息。
　　
　　 二 VPN特性
　　
　　 一般VPN所具备的优点有以下几点：
　　
　　 a) 最小成本：无须购买网络设备和专用线路覆盖所有远程用户
　　
　　 b) 责任共享：通过购买公用网的资源，部分维护责任迁移至provider（更专业，有经验，是操作，维护成本降低）。
　　
　　 c) 安全性：
　　
　　 d) 保障Qos
　　
　　 e) 可靠性：如果一个VPN节点坏了，可以一个替换VPN建立起来绕过他，这种恢复工作是得VPN操作可以尽可能的延续
　　
　　 f) 可扩展性：可以通过从公用网申请更多得资源达到非常容易的扩展VPN,或者协商重构VPN
　　
　　 其中安全性是vpn最重要的一个特性,也是各类vpn产品所必须具备和支持的要素.
　　
　　 三 VPN的安全技术
　　
　　 目前VPN主要采用四项技术来保证安全，这四项技术分别是隧道技术（Tunneling）、加解密技术（Encryption & Decryption）、密钥管理技术（Key Management）、使用者与设备身份认证技术（Authentication）。
　　
　　 加解密技术是数据通信中一项较成熟的技术，VPN可直接利用现有技术。
　　
　　 密钥管理技术的主要任务是如何在公用数据网上安全地传递密钥而不被窃取。现行密钥管理技术又分为SKIP与ISAKMP/OAKLEY两种。SKIP主要是利用Diffie-Hellman的演算法则，在网络上传输密钥；在ISAKMP中，双方都有两把密钥，分别用于公用、私用。
　　
　　 身份认证技术最常用的是使用者名称与密码或卡片式认证等方式。
　　
　　 隧道指的是利用一种网络协议来传输另一种网络协议，它主要利用网络隧道协议来实现这种功能。网络隧道技术涉及了三种网络协议，即网络隧道协议、隧道协议下面的承载协议和隧道协议所承载的被承载协议。网络隧道技术是个关键技术,这项vpn的基本技术也是本文要详细和阐述的.
　　
　　 四 网络隧道协议
　　
　　 网络隧道是指在公用网建立一条数据通道（隧道），让数据包通过这条隧道传输。现有两种类型的网络隧道协议，一种是二层隧道协议，用于传输二层网络协议，它主要应用于构建远程访问虚拟专网（AccessVPN）；另一种是三层隧道协议，用于传输三层网络协议，它主要应用于构建企业内部虚拟专网（IntranetVPN）和扩展的企业内部虚拟专网（Extranet VPN）。
　　
　　 二层隧道协议
　　
　　 第二层隧道协议是先把各种网络协议封装到PPP中，再把整个数据包装入隧道协议中。这种双层封装方法形成的数据包靠第二层协议进行传输。第二层隧道协议主要有以下三种：第一种是由微软、Ascend、3COM 等公司支持的 PPTP（Point to Point Tunneling Protocol，点对点隧道协议），在WindowsNT4.0以上版本中即有支持。
　　
　　 第二种是Cisco、北方电信等公司支持的L2F（Layer2Forwarding，二层转发协议），在 Cisco 路由器中有支持。
　　
　　 第三种由 IETF 起草，微软 Ascend 、Cisco、 3COM 等公司参与的 L2TP（Layer 2TunnelingProtocol，二层隧道协议）结合了上述两个协议的优点，L2TP协议是目前IETF的标准，由IETF融合PPTP与L2F而形成。这里就主要介绍一下 L2TP 网络协议。
　　
　　 其中，LAC 表示 L2TP 访问集中器（L2TPAccessConcentrator），是附属在交换网络上的具有 PPP 端系统和 L2TP 协议处理能力的设备，LAC 一般就是一个网络接入服务器 NAS（Network Access Server）它用于为用户通过 PSTN/ISDN 提供网络接入服务；LNS 表示 L2TP 网络服务器（L2TP Network Server），是 PPP 端系统上用于处理 L2TP 协议服务器端部分的软件。
　　
　　 在一个LNS和LAC对之间存在着两种类型的连接，一种是隧道（tunnel）连接，它定义了一个LNS和LAC对；另一种是会话（session）连接，它复用在隧道连接之上，用于表示承载在隧道连接中的每个 PPP 会话过程。
　　
　　 L2TP连接的维护以及PPP数据的传送都是通过L2TP消息的交换来完成的，这些消息再通过 UDP的1701端口承载于TCP/IP之上。L2TP消息可以分为两种类型，一种是控制消息，另一种是数据消息。控制消息用于隧道连接和会话连接的建立与维护。数据消息用于承载用户的 PPP 会话数据包。 L2TP 连接的维护以及 PPP 数据的传送都是通过 L2TP 消息的交换来完成的，这些消息再通过UDP的1701端口承载于 TCP/IP 之上。
　　
　　 控制消息中的参数用AVP值对（AttributeValuePair）来表示，使得协议具有很好的扩展性；在控制消息的传输过程中还应用了消息丢失重传和定时检测通道连通性等机制来保证了 L2TP 层传输的可靠性。数据消息用于承载用户的 PPP 会话数据包。L2TP 数据消息的传输不采用重传机制，所以它无法保证传输的可靠性，但这一点可以通过上层协议如TCP等得到保证；数据消息的传输可以根据应用的需要灵活地采用流控或不流控机制，甚至可以在传输过程中动态地使用消息序列号从而动态地激活消息顺序检测和流量控制功能；在采用流量控制的过程中，对于失序消息的处理采用了缓存重排序的方法来提高数据传输的有效性。
　　
　　 L2TP 还具有适用于VPN 服务的以下几个特性：
　　
　　 · 灵活的身份验证机制以及高度的安全性
　　
　　 L2TP 可以选择多种身份验证机制（CHAP、PAP等），继承了PPP的所有安全特性，L2TP 还可以对隧道端点进行验证，这使得通过L2TP所传输的数据更加难以被攻击。而且根据特定的网络安全要求还可以方便地在L2TP之上采用隧道加密、端对端数据加密或应用层数据加密等方案来提高数据的安全性。
　　
　　 · 内部地址分配支持
　　
　　 LNS可以放置于企业网的防火墙之后，它可以对于远端用户的地址进行动态的分配和管理，可以支持DHCP和私有地址应用（RFC1918）等方案。远端用户所分配的地址不是Internet地址而是企业内部的私有地址，这样方便了地址的管理并可以增加安全性。
　　
　　 · 网络计费的灵活性
　　
　　 可以在LAC和LNS两处同时计费，即ISP处（用于产生帐单）及企业处（用于付费及审记）。L2TP能够提供数据传输的出入包数，字节数及连接的起始、结束时间等计费数据，可以根据这些数据方便地进行网络计费。
　　
　　 · 可靠性
　　
　　 L2TP 协议可以支持备份 LNS，当一个主 LNS 不可达之后，LAC（接入服务器）可以重新与备份 LNS 建立连接，这样增加了 VPN 服务的可靠性和容错性。
　　
　　 · 统一的网络管理
　　
　　 L2TP协议将很快地成为标准的RFC协议，有关L2TP的标准MIB也将很快地得到制定，这样可以统一地采用 SNMP 网络管理方案进行方便的网络维护与管理。
　　
　　 三层隧道协议
　　
　　 第三层隧道协议是把各种网络协议直接装入隧道协议中，形成的数据包依靠第三层协议进行传输。三层隧道协议并非是一种很新的技术，早已出现的 RFC 1701 Generic Routing Encapsulation（GRE）协议就是个三层隧道协议。新出来的 IETF 的 IP 层加密标准协议 IPSec 协议也是个三层隧道协议。
　　
　　 IPSec（IPSecurity）是由一组RFC文档组成，定义了一个系统来提供安全协议选择、安全算法，确定服务所使用密钥等服务，从而在IP层提供安全保障。它不是一个单独的协议，它给出了应用于IP层上网络数据安全的一整套体系结构，它包括网络安全协议 Authentication Header（AH）协议和 Encapsulating Security Payload（ESP）协议、密钥管理协议Internet Key Exchange （IKE）协议和用于网络验证及加密的一些算法等。下面就IPSec的认证与加密机制和协议分别做一些详细说明。
　　
　　 1． IPSec认证包头（AH）：
　　
　　 它是一个用于提供IP数据报完整性和认证的机制。其完整性是保证数据报不被无意的或恶意的方式改变，而认证则验证数据的来源（识别主机、用户、网络等）。AH本身其实并不支持任何形式的加密，它不能保证通过Internet发送的数据的可信程度。AH只是在加密的出口、进口或使用受到当地政府限制的情况下可以提高全球Intenret的安全性。当全部功能实现后，它将通过认证IP包并且减少基于IP欺骗的攻击机率来提供更好的安全服务。AH使用的包头放在标准的IPv4和IPv6包头和下一个高层协议帧（如TCP、UDP、ICMP等）之间。
　　
　　 AH协议通过在整个IP数据报中实施一个消息文摘计算来提供完整性和认证服务。一个消息文摘就是一个特定的单向数据函数，它能够创建数据报的唯一的数字指纹。消息文摘算法的输出结果放到 AH包头的认证数据（Authentication_Data）区。消息文摘5算法（MD5）是一个单向数学函数。当应用到分组数据中时，它将整个数据分割成若干个128比特的信息分组。每个128比特为一组的信息是大分组数据的压缩或摘要的表示。当以这种方式使用时，MD5只提供数字的完整性服务。一个消息文摘在被发送之前和数据被接收到以后都可以根据一组数据计算出来。如果两次计算出来的文摘值是一样的，那么分组数据在传输过程中就没有被改变。这样就防止了无意或恶意的窜改。在使用HMAC－MD5认证过的数据交换中，发送者使用以前交换过的密钥来首次计算数据报的64比特分组的MD5文摘。从一系列的16比特中计算出来的文摘值被累加成一个值，然后放到AH包头的认证数据区，随后数据报被发送给接收者。接收者也必须知道密钥值，以便计算出正确的消息文摘并且将其与接收到的认证

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
**红黑树是一种自平衡的二叉查找树。**除了符合 BST 的基本特性外, 还具有如下性质:
1. 节点只能是红色和黑色二者之一。
2. 根节点必须是黑色。
3. 红黑树的叶子节点为 `null` 节点。所有叶子节点都是黑色的。
4. 每个红色节点的两个子节点都是黑色。即从根节点到任意叶子节点的路径上, 不能有两个连续的红色节点。但可以有连续的黑色节点。
5. 从任意一节点到其每个叶子的所有路径, 都包含相同数目的黑色节点。

这些性质, 保证了红黑树的自平衡性。

当有节点插入或删除时, 常常会破坏红黑树的性质。这个时候, 就需要执行一些操作来维持红黑树的性质。这种操作有两种:
* **变色**。将红色节点改为黑色, 或将黑色节点改为红色。
* **旋转**。旋转分为**左旋转**和**右旋转**。描述起来可能不太方便, 直接上图。

![左旋转](/images/interview-experence/28.jpg)


![右旋转](/images/interview-experence/29.jpg)

总体来说, 恢复红黑树的性质需要少量的颜色变更(实际是非常快速的)和不超过三次树旋转(对于插入操作是两次)。
对于插入操作, 有五种情况。
对于删除操作, 有六种情况。
这里就不展开了, 具体参见[维基百科: 红黑树](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)

### 参考资料
* [什么是红黑树?](https://zhuanlan.zhihu.com/p/31805309)
* [维基百科: 红黑树](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)

## AVL 树
为了避免二叉搜索树的最差情况, 还可以利用平衡的二叉搜索树, 即 `AVL 树`。使用 AVL 树, 我们可以用 O(logn) 的时间插入元素, 同时用 O(1) 的时间得到所有节点的中位数。
下面我们便来详细介绍一下 AVL 树。

### AVL 树的简介
AVL树是最早被发明的自平衡二叉查找树。
在AVL树中, 任一节点对应的两棵子树的最大高度差为 `1`, 因此它也被称为`高度平衡树`。
查找、插入和删除在平均和最坏情况下的时间复杂度都是 O(logn)。
增加和删除元素的操作则可能需要借由一次或多次树旋转, 以实现树的重新平衡。
在 AVL 树中, 有一个概念也十分重要, 那就算节点的`平衡因子`。节点的平衡因子是它的左子树的高度减去它的右子树的高度(有时相反)。**平衡因子为 `1`、`0` 或 `-1` 的节点被认为是平衡的。平衡因子为 `-2` 或 `2` 的节点被认为是不平衡的, 并需要重新平衡这个树。**具体的操作就是旋转。平衡因子可以直接存储在每个节点中。

### AVL 树的操作
#### 插入
假设平衡因子是左子树的高度减去右子树的高度所得到的值, 又假设由于在二叉排序树上插入节点而失去平衡的最小子树根节点的指针为 `a`(即 `a` 是离插入点最近, 且平衡因子绝对值超过 `1` 的祖先节点), 则失去平衡后的情况可归纳为下列四种:
1. **单向右旋平衡处理LL**: 由于在 `a` 的左子树根节点的左子树上插入节点,  `a` 的平衡因子由 1 增至 2, 致使以 `a` 为根的子树失去平衡, 则需进行一次右旋转操作。
2. **单向左旋平衡处理RR**: 由于在 `a` 的右子树根节点的右子树上插入节点,  `a` 的平衡因子由 -1 变为 -2, 致使以 `a` 为根的子树失去平衡, 则需进行一次左旋转操作。
3. **双向旋转(先左后右)平衡处理LR**: 由于在 `a` 的左子树根节点的右子树上插入节点,  `a` 的平衡因子由 1 增至 2, 致使以 `a` 为根的子树失去平衡, 则需进行两次旋转(先左旋后右旋)操作。
4. **双向旋转(先右后左)平衡处理RL**: 由于在 `a` 的右子树根节点的左子树上插入节点,  `a` 的平衡因子由 -1 变为 -2, 致使以 `a` 为根的子树失去平衡, 则需进行两次旋转(先右旋后左旋)操作。

更直观一点, 可以用下图来说明。

![旋转](/images/interview-experence/30.png)

#### 删除
从AVL树中删除, 可以通过把要删除的节点向下旋转成一个叶子节点, 接着直接移除这个叶子节点来完成。因为在旋转成叶子节点期间最多有log n个节点被旋转, 而每次AVL旋转耗费固定的时间, 所以删除处理在整体上耗费O(log n) 时间。

#### 搜索
可以像普通二叉查找树一样的进行, 所以耗费O(log n)时间, 因为AVL树总是保持平衡的。不需要特殊的准备, 树的结构不会由于查找而改变。(这是与伸展树搜索相对立的, 它会因为搜索而变更树结构。)

### 参考资料
* [维基百科: AVL 树](https://zh.wikipedia.org/wiki/AVL%E6%A0%91)

## 最小编辑距离
最近在编写 Virtual DOM 的实现, 刚好遇到了如何比较列表元素的问题。
将元素列表抽象成以 key 排列的列表, 那么求旧树到新树的最小操作问题, 就变成了如何求两个字符串间的最小`编辑距离(edit distance)`的问题。因此做了一些研究。
最小编辑距离问题和寻径问题一样, 都可以用动态规划来解决。

莱文斯坦距离, 又称Levenshtein距离, 是编辑距离的一种。指两个字串之间, 由一个转成另一个所需的最少编辑操作次数。

允许的编辑操作包括：

将一个字符替换成另一个字符
插入一个字符
删除一个字符
俄罗斯科学家弗拉基米尔·莱文斯坦在1965年提出这个概念。
todo

### 简介
给定两个字符串 a 和 b, a 与 b 之间的编辑距离是指将 a 转化为 b 的操作数。其中只允许以下三种操作:
1. 插入一个字符。
2. 删除一个字符。
3. 替换一个字符。

求 a 与 b 之间的最小编辑距离。

### 思路描述
在这里, 我不仅会描述最终的模式是怎么样的, 更会描述最终的模式是怎么来的。
所谓动态规划, 就是将大问题分解为小问题, 最后通过合并小问题的答案来解决大问题的过程。
https://www.bilibili.com/video/av51808367?from=search&seid=10728745868910882791
https://www.dreamxu.com/books/dsa/dp/edit-distance.html

# 浏览器
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
2. `Etag`、`If-None-Match`: 这两个值为服务器生成的每个资源的唯一标识符, 只要资源有变化就改变这个值, 如果资源没有改变就保持原来的值。判断流程与上一组类似, 只不过是通过 `Etag` 判断资源是否被修改。不同的是, 当服务器返回 304 时, 响应头中还会包含 `Etag`, 哪怕资源没有变化。
注意: `Last-Modified` 与 `Etag` 共存的情况下, `Etag` 的优先级高于 `Last-Modified`。

这里有一个小问题。既然已经有了 `Last-Modified`, 那为什么还要 `Etag` 呢?
主要有两点原因:
1. 如果在服务器上, 一个资源被修改了, 但其实际内容根本没发生改变, 则会因为 `Last-Modified` 时间匹配不上而返回了整个实体给客户端(即使客户端缓存里有个一模一样的资源)。这个时候就需要 `Etag` 字段了。客户端会保留该 `ETag` 字段, 并在下一次请求时将其一并带过去给服务器。服务器只需要比较客户端传来的 `ETag` 跟自己服务器上该资源的 `ETag` 是否一致, 就能很好地判断资源相对客户端而言是否被修改过了。
2. `Last-Modified` 的精度是秒。如果我们在一秒内多次修改了文件, 同时返回给客户端的是第一次修改后的文件, 则仅凭 `Last-Modified` 是无法判断出客户端的资源是不是最新的。这个时候, 也需要 `Etag` 辅助我们的判断。

### 缓存位置
那么问题来了, 不管是强缓存还是协商缓存, 是存储在哪里的呢? 下面我们便来回答这个问题。
浏览器中的缓存位置有四种, 按照优先级从高到低排列分别是:
* Service Worker Cache
* Memory Cache
* Disk Cache
* Push Cache

在浏览器发送请求之前, 会依次检查上面四个缓存, 尝试找到符合要求的资源。如果没有找到, 才会向服务器发送请求。
下面我们一个个来看一下。

#### Service Worker Cache
`Service Worker` 借鉴了 `Web Worker` 的思路, 即让 JS 运行在主线程之外, 由于它脱离了浏览器的窗体, 因此无法直接访问DOM。虽然如此, 但它仍然能帮助我们完成很多有用的功能, 比如离线缓存、消息推送和网络代理等功能。其中的离线缓存就是 `Service Worker Cache`。
具体的内容可以参见[Service Worker](#Service-Worker)。

#### Memory Cache & Disk Cache
`Memory Cache` 指内存缓存。从效率上讲它是最快的, 但是从存活时间来讲又是最短的。当渲染进程结束后, 内存缓存也就不存在了。
`Disk Cache` 就是存储在磁盘中的缓存。从存取效率上讲是比内存缓存慢的, 但是优势在于存储容量和存储时长。
那么问题来了, 浏览器如何决定将资源放进内存还是硬盘呢？主要策略如下：
* 比较大的 JS、CSS 文件会直接被丢进磁盘, 反之丢进内存。
* 当内存占用率比较高的时候, 文件优先进入磁盘。

#### Push Cache
提到这个, 就不得不提到 `Server Push`。简单来说, `Server Push` 能让 `HTTP/2` 服务器在客户端请求资源之前将资源发送到符合 `HTTP/2` 的客户端。而发送到客户端的资源, 会被放入 `Push Cache` 即推送缓存当中。
由于 `Push Cache` 是 `HTTP/2` 中的内容, 所以现在应用的并不广泛, 各个浏览器之间的支持也有一定差异。但随着 `HTTP/2` 的推广, 它的应用会越来越广泛。

## 从输入 url 到显示页面发生了什么?
### 网络篇
首先我们来看看, 在请求的发送和接收方面, 浏览器做了什么。

#### 请求发送
1. 构建请求: 浏览器首先会构造一个请求。
2. 查找强缓存: 先检查请求是否命中强缓存。如果命中, 则直接返回; 否则进入下一步。
3. DNS 解析: 通过域名解析服务器的 IP 地址。浏览器在解析某个域名后, 会将其存入 DNS 缓存中。在下次解析相同域名时, 直接返回, 不需要与 DNS 服务器发生交互。
4. 建立 TCP 连接: 通过三次握手, 建立 TCP 连接。值得注意的是, **Chrome 在同一个域名下最多能同时保持 6 个 TCP 连接, 用于并行发送请求。**
5. 发送 HTTP 请求: HTTP 请求一般有三部分, `请求行`、`请求头`和`请求体`。
首先是`请求行`。`请求行`由三部分组成, `请求方法`、`请求 URI` 和 `HTTP 版本协议`。
```js
// 请求方法是GET, 路径为根路径, HTTP协议版本为1.1
GET / HTTP/1.1
```
 之后是`请求头`, 包含一些说明性信息。
 最后是`请求体`, **在 http 1.0 中只有在 POST 方法下存在请求体, 在 http1.1 后, 所有方法请求方法都能用于请求体。**常见的场景是表单提交。

#### 网络响应
HTTP 请求到达服务器, 服务器进行对应的处理。最后把数据传给浏览器, 也就是返回网络响应。
网络响应具有三个部分: `响应行`、`响应头`和`响应体`。
首先是`响应行`。`响应行`也由三部分组成, `HTTP 协议版本`、`状态码` 和 `状态描述`。

```js
HTTP/1.1 200 OK
```

之后是`响应头`, 包含了服务器及其返回数据的一些信息, 服务器生成数据的时间、返回的数据类型以及对即将写入的 Cookie 信息等。
最后是`响应体`, 包含了应该服务器端返回的数据。

那么问题来了, 响应完成之后, TCP 连接就断开了吗?
**答案是不一定**。需要对 `Connection` 字段进行判断。
如果请求头或响应头中包含 `Connection: keep-alive`, 就表明建立了长连接。**在 HTTP1.1 中, `keep-alive` 是 `Connection` 字段的默认值。也就是说, HTTP1.1 是默认建立长连接的。**
只有当请求头或响应头中包含 `Connection: close` 时, 才会关闭该连接。**在 HTTP1.0 中, `close` 是 `Connection` 字段的默认值。也就是说, HTTP1.0 是默认在请求后关闭连接的。**

### 解析篇
到这里, 我们已经通过网络拿到了目标文件。如果响应头中 `Content-Type` 的值是 `text/html`, 那么接下来就是浏览器的解析工作了。
简单来说, 浏览器的解析可以分为三个步骤:
1. 构建 DOM 树
2. 计算样式, 生成 `CSS 规则树(CSS rule tree)`
3. 生成`布局树(layout tree)`

#### 构建 DOM 树
首先要构建的是 DOM 树。**需要说明的是, 为了你能更好的理解这段内容, 强烈建议你阅读 [HTML 规范](https://html.spec.whatwg.org/multipage/parsing.html), 哪怕只阅读几个关键的部分也行。**以下是我觉得比较重要的几个部分:
* [Overview of the parsing model](https://html.spec.whatwg.org/multipage/parsing.html#overview-of-the-parsing-model)
* [Tokenization](https://html.spec.whatwg.org/multipage/parsing.html#tokenization)
* [Tree construction](https://html.spec.whatwg.org/multipage/parsing.html#tree-construction)

由于浏览器无法直接理解 HTML 字符串, 因此将这一系列的字节流转换为一种有意义并且方便操作的数据结构, 这种数据结构就是 DOM 树。**DOM 树本质上是一个以 `document` 为根节点的多叉树。**
构建 DOM 树的整个过程可以用下图来表示。

![构建 DOM 树](/images/interview-experence/24.svg)

HTML 构建 DOM 树时使用的解析算法分为两步:
1. `标记化(tokenizer)`, 对应`词法分析`的过程。
2. `建树(tree construction)`, 对应`语法分析`的过程。

值得注意的是, **上述两个过程在执行的时候并不是顺序执行, 而是不断交替执行的!**

##### 标记化
这个算法输入为 HTML 文本, 输出为 HTML 标记, 包括: `DOCTYPE`, `start tag`, `end tag`, `comment`, `character`, `end-of-file` 五种。因此称之为`标记生成器`。
**算法的原理是有限状态机。**在当前状态下, 接收一个或多个字符, 就会更新到下一个状态。整个状态机有 80 种状态, 每种状态的行为不尽相同。哪怕是同一个状态, 具体行为也会受[插入模式(insertion mode)](https://html.spec.whatwg.org/multipage/parsing.html#insertion-mode)和[开放元素栈(stack of open elements)](https://html.spec.whatwg.org/multipage/parsing.html#stack-of-open-elements)的影响而略有不同。这里就不展开讨论了。
**当一个标记被输出后, 它必须立即被建树算法所处理。**
在建树阶段, 还可以通过添加额外的字符进入流中, 来影响标记化阶段。

##### 建树
之前提到过, DOM 树是一个以 `document` 为根节点的多叉树。因此`解析器(parser)`首先会创建一个 `document` 对象。`标记生成器`会把每个标记的信息发送给`建树器`。`建树器`接收到相应的标记时, 会创建对应的 DOM 对象。创建这个 DOM 对象后会做两件事情:
1. 将创建好的 DOM 对象添加到 DOM 树中。
2. 将对应标记压入开放元素栈(与闭合标签对应)中。

在这个过程中, 当遇到某些会改变 DOM 结构的操作时, `建树器`会重新调用`标记生成器`, 来处理新出现的标签。引用一下原文吧:

>The tree construction stage is reentrant, meaning that while the tree construction stage is handling one token, the tokenizer might be resumed, causing further tokens to be emitted and processed before the first token's processing is complete.

#### 计算样式
仅仅有 DOM 树是不够的, 我们还需要计算加载在 DOM 节点上的样式。这个过程分为三步:
1. 汇总所有样式的来源, 格式化样式表。
2. 将样式表中的属性值转化浏览器能够理解的值。
3. 计算每个节点的具体样式。

##### 格式化样式表
一般来说, 样式的来源有三种: `<link> 标签`、`<style> 标签` 以及 内嵌样式。
在计算样式之前, 浏览器会收集所有样式, 将其转化为一个结构化的对象, 即 `styleSheets`。这个对象可以在控制台通过 `document.styleSheets` 来查看。

##### 转化标准值
在书写 CSS 时, 有些属性值是不被渲染引擎理解的, 因此需要将其转化为标准值。如: `em` -> `px`、`red` -> `#ff0000`、`bold` -> `700` 等。

##### 计算具体样式
一个节点的具体样式主要由两个规则决定: **继承**和**层叠**。
继承很好理解。每个子节点都会默认继承父节点的样式。如果父节点中没有对应样式, 则采用`浏览器默认样式(useragent style)`。
层叠是指, 计算哪些样式应该生效, 哪些会被覆盖, 样式与样式之间的组合会如何影响最终的结果。这里不过多介绍。
计算完的样式, 可以通过 `window.getComputedStyle()` 进行查看。此方法需要一个元素作为参数, 返回加在这个元素上的所有样式。

#### 生成布局树
到这里, 我们已经拥有了一棵完整的 DOM 树, 也有了每个节点的样式。接下来要做的, 就是计算元素所在的位置, 也就是要生成一棵`布局树(layout tree)`。
布局树生成的大致工作如下:
1. 遍历生成的 DOM 树节点, 并把他们添加到布局树中。
2. 计算布局树节点的坐标位置。

值得注意的是, **这棵布局树只包含可见元素, `<head> 标签`和设置了 `display: none` 的元素, 将不会被放入其中。**
有人说首先会生成`渲染树(Render Tree)`, 其实这还是 16 年之前的事情, 现在 Chrome 团队已经做了大量的重构, 已经没有这一过程了。而布局树的信息已经非常完善, 完全拥有渲染树的功能。

### 渲染篇
现在我们已经具备了渲染前的一切条件, 可以往页面上绘制元素了。
整个渲染过程分为以下一个步骤:
1. 根据布局树, 生成`绘制记录(paint records)`
2. 创建`图层树(layer tree)`
3. 对图层进行`栅格化(raster)`, 并传入 GPU 中进行渲染
4. 合成帧, 显示内容

#### 生成绘制记录
即使知道了不同元素的位置及样式信息, 我们还需要知道不同元素的绘制先后顺序才能正确绘制出整个页面。
浏览器主线程会遍历布局树以创建绘制记录。
**绘制记录可以看做是记录各元素绘制先后顺序的笔记。**

#### 创建图层树
首先我们要明白什么是`图层(layer)`以及图层的作用。
浏览器会遍历布局树, 将符合一定要求的节点提升为一个单独的图层, 称为`合成层(Compositing Layers)`, 有别于普通文档流内的**普通图层**。在渲染时, 各个图层之间都是相对独立的。在最终渲染时, 再通过`合成器线程(compositor thread)`将不同图层组合在一起, 形成我们看到的页面。
首先, 普通文档流内可以理解为一个**复合图层**(这里称为**默认复合层**, 里面不管添加多少元素, 其实都是在同一个复合图层中)。
**需要注意的是, 脱离文档流并不代表会为该元素创建新的图层。**以 `absolute` 布局(`fixed` 也一样)为例, 虽然可以脱离普通文档流, 但它仍然属于默认复合层。
其次, 某些特殊的渲染层会被认为是`合成层(Compositing Layers)`, 合成层拥有单独的图层。其他不是合成层的图层, 则和其第一个拥有单独图层的父层共用同一个图层。
那么使用合成层有哪些优点呢? 主要有下面三条:
1. 合成层的在绘制时, 会交由 GPU 合成, 比 CPU 处理要快。
2. 当需要重绘时, 只需要重绘当前层本身, 不会影响到其他图层。
3. 对于 `transform` 和 `opacity` 效果, 不会触发重绘。

在这里我们只介绍了最简单的图层的概念和优点, 如果想深入了解的话, 可以看看这两篇文章:
* [浏览器渲染流程&Composite(渲染层合并)简单总结](https://segmentfault.com/a/1190000014520786)
* [无线性能优化: Composite](https://fed.taobao.org/blog/2016/04/26/performance-composite/)

#### 栅格化
有的时候, 页面很大, 而我们的视口很小。这个时候, 盲目的去渲染整个页面显然是得不偿失的, 因为有很多地方根本就不会显示在页面上!
为了解决这个问题, 我们可以将页面根据图层划分为小块, 并优先渲染靠近视口的部分, 以获得更好的首屏性能。这个过程称为`栅格化(raster)`。
在栅格化之后, 会将小块传入 GPU 中进行渲染。整个栅格化的过程涉及到浏览器的两个线程, 一个是`合成器线程(compositor thread)`, 一个是`栅格化线程(raster thread)`。
合成器线程会优先选定视口附近的图块, 将其交给栅格化进程进行栅格化。栅格化进程会将图块栅格化后的结果传入 GPU, 并存放在 GPU 的显存中。

另外值得一提的是, 由于图块数据要进入 GPU 内存, 而浏览器内存上传 GPU 内存的速度比较慢, 即使是绘制一部分图块, 也可能会耗费大量时间。
针对这个问题, Chrome 采用了一个策略: 在首次合成图块时只采用一个低分辨率的图像, 这样首屏展示的时候只是展示出低分辨率的图像。之后继续进行合成操作, 当正常的图块内容绘制完毕后, 会将当前低分辨率的图块内容替换。这也是 Chrome 底层优化首屏加载速度的一个手段。

#### 合成帧
在栅格化结束后, `合成器线程` 会生成一个绘制页面的指令, 即`Draw Quads`, 指定需要将哪部分页面绘制出来, 并将这个命令发送给`浏览器进程(browser thread)`。
浏览器进程根据这个指令, 把页面内容绘制到内存, 也就是生成了页面, 然后把这部分内存发送给显卡。为什么发给显卡呢? 这里就有必要聊一聊显示器显示图像的原理了。

一般来说, 显卡有两个缓冲区, 分别称为`前缓冲区`和`后缓冲区`。在我们更新页面时, 每次更新的图像都来自显卡的前缓冲区。显卡接收到浏览器进程传来的页面后, 会合成相应的图像, 并将图像保存到后缓冲区, 然后由系统自动将前缓冲区和后缓冲区对换位置, 如此循环, 更新图像。
看到这里你也就是明白, 当某个动画大量占用内存的时候, 浏览器生成图像的时候会变慢, 图像传送给显卡就会不及时, 而显示器还是以 60 fps 的频率刷新, 因此会出现卡顿, 也就是明显的掉帧现象。

### 总结
到这里, 整个过程就全部结束了。用三张图来总结一下吧:

![选择器](/images/interview-experence/25.jpg)

![选择器](/images/interview-experence/26.jpg)

![选择器](/images/interview-experence/27.jpg)


## 浏览器本地存储的方式及优劣
浏览器本地缓存主要分为 `Cookie`、`WebStorage` 和 `IndexedDB`, 其中 `WebStorage` 又分为 `localStorage` 和 `sessionStorage`。

### Cookie
`Cookie` 最早被设计出来的目的, 是用于帮助 HTTP 进行状态管理的。
`Cookie` 的本质上就是浏览器里面存储的一个很小的文本文件, 内部以键值对的方式来存储。浏览器向某个域名发送请求时, 会自动带上当前域名下的 `Cookie`。服务器拿到 `Cookie` 之后, 便可以进行一系列状态解析工作。
但 `Cookie` 也有缺陷:
1. `Cookie` 的容量很小, 单个域名的存储上限仅有 4KB。
2. 在发送请求时, 浏览器会把当前域名下所有的 `Cookie` 都发送出去, 不管这个请求需不需要。当挂载在某个域名下的 `Cookie` 很多时, 会造成性能上的浪费。
3. `Cookie` 易被篡改。没有设置 `HttpOnly` 的 `Cookie` 可以被 JavaScript 访问。且在请求传输的过程中, 容易非法用户被截获并篡改。

### localStorage
`localStorage` 有一点和 `Cookie` 一样, 那就是都是按照域名存储的, 只要进入对应域名, 就能访问到该域名下的 `localStorage`。除此之外, `localStorage` 还有以下特点:
1. 容量大。单个域名存储的上限达到了 5M。
2. 只存在于客户端, 不参与与服务器的通信。
3. 操作方便。`localStorage` 暴露在全局下, 通过其下面挂载的 `setItem` 和 `getItem` 等方法, 可以很方便的进行操作。
4. 与 `Cookie` 不同, **`localStorage` 没有过期时间。**除非手动清除缓存, 否则会一直保留。
5. **`localStorage` 中存储的值都是字符串。**如果要存储对象的话, 需要调用 `JSON.stringify` 方法将对象转化为字符串后, 再进行存储。

### sessionStorage
`sessionStorage` 与 `localStorage` 大致相同。二者都具有以下特点:
1. 容量上限均为 5M。
2. 只存在于客户端, 不与服务器发生交互。
3. `sessionStorage` 暴露在全局之下, 操作方法与 `localStorage` 一致。

二者的不同点在于: **`sessionStorage` 只是会话级别的存储, 关闭页面之后, `sessionStorage` 会自动清除。**

### IndexedDB
`IndexedDB` 是运行在浏览器中的非关系型数据库。
由于其本质上是数据库, 因此不存在存储上限。
关于 `IndexedDB`, 有如下特点需要注意:
1. `IndexedDB` 遵循同源策略。
2. `IndexedDB` 的 API 基本上都是异步的。
3. 在 `IndexedDB` 内部, 使用键值对存储数据。

`IndexedDB` 可以让开发者不考虑网络可用性, 创建具有丰富查询能力的可离线 Web 应用程序, 对于存储大量数据的应用程序和不需要持续网络连接的应用程序很有用。
更多信息请参考[MDN: IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB)。

## 浏览器的显示优化
todo

## IndexedDB
todo

## Web Worker
todo

## Service Worker
Service workers 本质上充当Web应用程序与浏览器之间的代理服务器, 也可以在网络可用时作为浏览器和网络间的代理。它们旨在(除其他之外)使得能够创建有效的离线体验, 拦截网络请求并基于网络是否可用以及更新的资源是否驻留在服务器上来采取适当的动作。他们还允许访问推送通知和后台同步API。
简单来说, ServiceWorker 允许应用完全控制浏览器的网络行为, 修改网络请求。你可以完全控制应用在特定情形(最常见的情形是网络不可用)下的表现。可以通过ServiceWorker创建离线应用。
Service worker运行在worker上下文, 因此它不能访问DOM。相对于驱动应用的主JavaScript线程, 它运行在其他线程中, 所以不会造成阻塞。它设计为完全异步, 同步API(如XHR和localStorage)不能在service worker中使用。
出于安全考量, Service workers只能由HTTPS承载, 毕竟修改网络请求的能力暴露给中间人攻击会非常危险。在Firefox浏览器的用户隐私模式, Service Worker不可用。
参考链接
https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API
https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API/Using_Service_Workers
todo

## 参考资料
* [(1.6w字)浏览器灵魂之问, 请问你能接得住几个?](https://juejin.im/post/5df5bcea6fb9a016091def69#heading-8)
* [HTTP/2 push is tougher than I thought](https://jakearchibald.com/2017/h2-push-tougher-than-i-thought/)
* [HTML 规范](https://html.spec.whatwg.org/multipage/parsing.html)
* [从 Chrome 源码看浏览器如何 layout 布局](https://www.rrfed.com/2017/02/26/chrome-layout/)
* [史上最全! 图解浏览器的工作原理](https://www.infoq.cn/article/cs9-wzqlnr5h05hhdo1b)
* [浏览器渲染流程&Composite(渲染层合并)简单总结](https://segmentfault.com/a/1190000014520786)
* [无线性能优化: Composite](https://fed.taobao.org/blog/2016/04/26/performance-composite/)
* [浏览器渲染过程与性能优化](https://juejin.im/post/59d489156fb9a00a571d6509#heading-3)
* [MDN: IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB)

# git
## git merge 与 git rebase 的区别
## git pull 和 git fetch 的区别
todo

# Node

# 其他
## Linux 操作系统相关
todo 软连接

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

# typescript
读文档

# React Native

# Flutter

# 小程序相关
todo
权限管理
小程序发布
小程序包压缩策略

# 移动端
适配相关

AOE 网
java基本语法
在JavaScript中, 所有延迟加载的方式中,只有IE浏览器支持的是__________方式。
图的遍历方法