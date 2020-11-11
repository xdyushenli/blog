---
title: 如何用 CSS 画一个旋转的球体？
date: 2020-09-28 10:00:49
tags: [ css ]
---
transform-style: preserve-3d;

```css
@keyframes rotate {
    0% {
        transform: rotateZ(30deg) rotateX(20deg) rotateY(0deg);
    }

    50% {
        transform: rotateZ(30deg) rotateX(20deg) rotateY(180deg);
    }

    100% {
        transform: rotate(30deg) rotateX(20deg) rotateY(360deg);
    }
}

.global {
    position: relative;
    width: 200px;
    height: 200px;
    transform-style: preserve-3d;
    animation: rotate 2s linear infinite;
}

.circle {
    position: absolute;
    width: 100%;
    height: 100%;
    margin: auto;
    border-radius: 50%;
    border: 1px double lightblue;
}

.two {
    transform: rotateY(90deg);
}

.three {
    transform: rotateY(45deg);
}

.four {
    transform: rotateY(135deg);
}

.five {
    transform: rotateX(90deg);
}

.six {
    width: 173.205px;
    height: 173.205px;
    margin: 13.3975px;
    transform: rotateX(90deg) translateZ(50px);
}

.seven {
    width: 173.205px;
    height: 173.205px;
    margin: 13.3975px;
    transform: rotateX(90deg) translateZ(-50px);
}
```

```html
<div class="global">
    <div class="circle one"></div>
    <div class="circle two"></div>
    <div class="circle three"></div>
    <div class="circle four"></div>
    <div class="circle five"></div>
    <div class="circle six"></div>
    <div class="circle seven"></div>
</div>
```

一个不动的、具有一些径向渐变的圆 + 圆上一些移动的标记点
就能让人眼以为是眼前这个球体在运动

# 参考资料
- http://timokorinth.de/css-3d-sphere/
- https://stackoverflow.com/questions/45238194/how-can-i-create-pure-css-3-dimensional-spheres