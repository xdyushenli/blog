---
title: PixiJS 不完全指南
date: 2021-03-26 14:29L31
tags: [js, pixijs]
---
# 前言
最近动画的需求越来越多，因此调研了下 PixiJS 这一动画引擎，简单整理了下调研的结果。

# PixiJS 是什么？
PixiJS 最初是一个 2D 的动画引擎，目前稳定且使用人数较多的版本为 4.x 和 5.x，通过插件的形式已支持骨骼动画和3D 动画。

# PixiJS 常用 api
## PIXI.Application
要使用 PixiJS，这是第一个需要使用的 api。它会自动生成一个 `<canvas>` 元素，然后在该元素内显示图像。

```js
// 创建 PIXI 应用程序, 生成 canvas 元素
let app = new PIXI.Application({width: 256, height: 256});

// 将生成的 canvas 元素添加到 DOM 中
document.body.appendChild(app.view);
```

PIXI.Application 会自动根据浏览器的支持程度来选择使用 webGL 渲染还是使用 Canvas 渲染。

PIXI.Application 下还有一个 stage 属性，所有生成的 PIXI 图像都必须添加到 stage 中后才能显示在屏幕上。

## PIXI.Sprite
`PIXI.Sprite` 绝对是 PixiJS 中最常用的 api。
所有的图像、视频、纹理等资源都要先通过 Sprite 加载后才能展示在屏幕上。对于图像等资源显示上的操作也是通过 Sprite 来完成的。

### 资源加载
```js
// 加载图片资源
const bunny = PIXI.Sprite.from('examples/assets/bunny.png');

// 加载纹理资源
const texture = PIXI.Texture.from('examples/assets/flowerTop.png');
const dude = new PIXI.Sprite(texture);

// 加载视频资源
const texture = PIXI.Texture.from('examples/assets/video.mp4');
const videoSprite = new PIXI.Sprite(texture);

// 加载一组图片, 加载后会按加载顺序依次播放这些图片
const explosionTextures = [];
for (let i = 0; i < 26; i++) {
    const texture = PIXI.Texture.from(`Explosion_Sequence_A ${i + 1}.png`);
    explosionTextures.push(texture);
}
const explosion = new PIXI.AnimatedSprite(explosionTextures);

// 加载分片图
const texture = PIXI.Texture.from('examples/assets/p2.jpeg');
const tilingSprite = new PIXI.TilingSprite(
    texture,
    app.screen.width,
    app.screen.height,
);
```

### 操作
在加载资源后，还可以通过 Sprite 对资源进行缩放、旋转、添加滤镜等一系列操作。

### 基础 demo
```js
// 创建应用
const app = new PIXI.Application({ backgroundColor: 0x1099bb });
document.body.appendChild(app.view);

// 加载图片资源
const bunny = PIXI.Sprite.from('examples/assets/bunny.png');

// 将 Sprite 移动到屏幕中心
bunny.x = app.screen.width / 2;
bunny.y = app.screen.height / 2;

// 将 Sprite 展示在屏幕上
app.stage.addChild(bunny);
```

更详细的 demo 可以参见[这里](https://pixijs.io/examples/#/sprite/basic.js)。

## PIXI.Text
`PIXI.Text` 用于展示艺术字效果。由于其底层是 WebGL 或 canvas，因此其提供了比 css 更丰富的文字样式定义属性，如：
- fill: 字体填充颜色
- stroke: 字体周边颜色
- dropShadow: 字体投影
- ...

不仅如此，PIXI.Text 还支持加载第三方的字库。

更详细的 demo 可以参见[这里](https://pixijs.io/examples/#/text/text.js)。

## PIXI.Graphics
`PIXI.Graphics` 是绘图用的 api，用于绘制自定义图形，因此其 api 与 canvas 绘图的 api 极其相似，如果对于 canvas 比较熟悉的话，基本可以无缝衔接地使用。

```js
const app = new PIXI.Application({ antialias: true });
document.body.appendChild(app.view);

const graphics = new PIXI.Graphics();

// 红色正方形
graphics.beginFill(0xDE3249);
graphics.drawRect(50, 50, 100, 100);
graphics.endFill();

app.stage.addChild(graphics);
```

更详细的 demo 可以参见[这里](https://pixijs.io/examples/#/graphics/simple.js)。

## 交互
对于动画来说，可交互性也是十分重要的一部分。PixiJS 中的对象是否可交互（响应事件）是通过设置对象上的 `interactive` 属性控制的。
常用的交互事件有：
- pointerdown：同 mousedown。
- pointerup：同 mouseup。
- pointerupoutside：同 mouseupoutside。
- pointerover：同 mouseover。
- pointerout：同 mouseout。
- pointermove：同 mousemove。

```js
const app = new PIXI.Application({ backgroundColor: 0x1099bb });
document.body.appendChild(app.view);

PIXI.settings.SCALE_MODE = PIXI.SCALE_MODES.NEAREST;

const sprite = PIXI.Sprite.from('examples/assets/bunny.png');

// Sprite 可响应事件
sprite.interactive = true;

// Sprite 对 click 事件进行响应
sprite.on('pointerdown', onClick);
app.stage.addChild(sprite);

function onClick() {
    console.log('click bunny!');
}
```

更详细的 demo 可以参见[这里](https://pixijs.io/examples/#/interaction/click.js)。


## 蒙版
蒙版并不是一个方法，而是在 PIXI 中很多对象（如 Sprite、Graphics 等）上挂载的一个属性。**与直觉不同的是，PixiJS 中的蒙版定义的并不是需要遮盖哪些部分，而是定义需要显示哪些部分**。也就是说，当你在对某个图像应用蒙版时，只有该图像与蒙版重叠的部分才会显示，未重叠的部分会隐藏。

```js
const app = new PIXI.Application({
  width: 1000,
  height: 1000
});
document.body.appendChild(app.view);

// 背景图
const bg = PIXI.Sprite.from('examples/assets/bg_scene_rotate.jpg');
bg.anchor.set(0.5);
bg.x = app.screen.width / 2;
bg.y = app.screen.height / 2;

// 蒙版
const mask = new PIXI.Graphics();
mask.x = app.screen.width / 2;
mask.y = app.screen.height / 2;
mask.beginFill(0x8bc5ff, 0.4);
mask.moveTo(-120, -120);
mask.lineTo(120, -120);
mask.lineTo(120, 120);
mask.lineTo(-120, 120);

// 在背景图上应用蒙版
bg.mask = mask;

app.stage.addChild(bg, mask);
```

更详细的 demo 可以参见[这里](https://pixijs.io/examples/#/masks/graphics.js)。

## PIXI.filters
`PIXI.filters` 用于创建滤镜，应用于图形上可以得到不同的视觉效果。
常用的滤镜有：

```js
const app = new PIXI.Application();
document.body.appendChild(app.view);

// 机器人
const littleRobot = PIXI.Sprite.from('examples/assets/pixi-filters/depth_blur_moby.jpg');
littleRobot.x = (app.screen.width / 2) - 200;
littleRobot.y = 100;
app.stage.addChild(littleRobot);

// 创建模糊滤镜
const blurFilter = new PIXI.filters.BlurFilter();
// 在机器人上应用模糊滤镜
littleRobot.filters = [blurFilter];

let count = 0;
app.ticker.add(() => {
    count += 0.005;
    blurFilter.blur = 20 * Math.cos(count);
});

```
更详细的 demo 可以参见[这里](https://pixijs.io/examples/#/filters-basic/blur.js)。

## todo texture mesh、shader

# 插件
插件部分为 PixiJS 提供了额外的能力，但由于插件大多并非 PixiJS 的官方实现，因此只做简单介绍。

# 参考资料
- [Pixijs examples](https://pixijs.io/examples/#/demos-basic/container.js)
- [pixi.js api](https://aitrade.ga/pixi.js-cn/index.html)