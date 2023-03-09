---
title: canvas 实现彩虹画笔绘画板
date: 2021-06-15 21:37:32
tags: javascript
swiper: true
swiperDesc: 'canvas 的初步学习——用 canvas 实现彩虹画笔绘画板
'
categories: 原生js项目
---

## 项目效果

[效果预览](https://v833.github.io/javscript-projects/01-Rainbow-brush-painting-board/)

## 涉及特性

Canvas：

- 模板骨架
- 基本属性
  - `getContext()`
  - `strokeStyle`
  - `fillStyle`
  - `fillRect`
  - `lineCap`
  - `lineJoin`
- 路径绘制
  - `beginPath()`
  - `lineTo()`
  - `moveTo()`

鼠标事件处理：

- `mousemove`
- `mousedown`
- `mouseup`
- `mouseout`

## 过程指南

1. 获取 HTML 中的 `<canvas>` 元素，并设定宽度和高度
2. `.getContext('2d')` 获取上下文，下面以 ctx 表示
3. 设定 ctx 基本属性
   - 描边和线条颜色
   - 线条宽度
   - 线条末端形状
4. 绘画效果
   1. 设定一个用于标记绘画状态的变量
   2. 鼠标事件监听，不同类型的事件将标记变量设为不同值
   3. 编写发生绘制时触发的函数，设定绘制路径起点、终点
5. 线条彩虹渐变效果（运用 hsl 的 `h` 值的变化，累加）
6. 线条粗细渐变效果（设定一个范围，当超出这个范围时，线条粗细进行逆向改变



## Canvas相关知识

[Canvas_API](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API)

### 彩虹画笔绘画板简单介绍

#### 一、 模板骨架

```js
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>HTML5 Canvas 实现彩虹画笔绘画板</title>
  <style type="text/css">
    canvas {
      border: 1px solid black;
    }
  </style>
</head>

<body>
  <canvas id="canvas" width="150" height="150"></canvas>
  < script></ script>
</body>

</html>
```

- < canvas> 元素

```js
<canvas id="canvas" width="150" height="150"></canvas>
```

`canvas` 看起来和 `img` 元素很相像，唯一的不同就是它并没有 `src` 和`alt` 属性。实际上，`canvas` 标签只有两个属性——`width`和`height`。这些都是可选的，并且同样利用 `DOM properties` 来设置。当没有设置宽度和高度的时候，`canvas`会初始化宽度为`300`像素和高度为`150`像素。该元素可以使用CSS来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果CSS的尺寸与初始画布的比例不一致，它会出现扭曲。


- 渲染上下文（The rendering context）

```js
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
```

`canvas`元素创造了一个固定大小的画布，它公开了一个或多个渲染上下文，其可以用来绘制和处理要展示的内容。

`canvas`起初是空白的。为了展示，首先脚本需要找到渲染上下文，然后在它的上面绘制。`canvas`元素有一个叫做 `getContext()` 的方法，这个方法是用来获得渲染上下文和它的绘画功能。`getContext()`只有一个参数，上下文的格式。对于2D图像而言，基本教程，你可以使用[CanvasRenderingContext2D](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D)




## 项目源码分析

### 源码

```js
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>实现彩虹画笔绘画板😊</title>
  <style>
    * {
      margin: 0;
      padding: 0;
    }

    canvas {
      border: 1px solid black;
    }
  </style>
</head>

<body>
  <canvas id="canvas" width="1200" height="800"></canvas>

  < script type="text/javascript">
    // 获取canvas节点
    let canvas = document.querySelector('#canvas');
    let ctx = canvas.getContext("2d");

    let colorDeg = 0;
    let lineWidth = 50;
    let direction = true;
    // hsl 色相(0 - 360) 饱和度 明度
    ctx.strokeStyle = `hsl(${colorDeg}, 90%, 50%)`;
    ctx.lineWidth = `${lineWidth}`;
    ctx.lineCap = 'round';
    ctx.lineJoin = 'round';

    let drawing = false;
    let [x, y] = [0, 0]
    function draw(e) {
      if (!drawing) return;
      ctx.beginPath();

      // 控制笔触大小
      colorDeg = colorDeg < 360 ? colorDeg + 1 : 0;
      if (lineWidth > 50 || lineWidth < 10) {
        direction = !direction
      }
      direction ? lineWidth++ : lineWidth--;
      ctx.lineWidth = `${lineWidth}`;

      // 彩虹效
      ctx.strokeStyle = `hsl(${colorDeg}, 90%, 50%)`;

      // 控制绘制路径
      ctx.moveTo(x, y);
      console.log(x, y);
      ctx.lineTo(e.offsetX, e.offsetY);
      [x, y] = [e.offsetX, e.offsetY];
      ctx.stroke();

    }
    canvas.addEventListener("mousedown", (e) => {
      drawing = true;
      // 括号，方括号，正则开头的斜杠，加号，减号，反引号 作为行首的时候必须要加分号
      [x, y] = [e.offsetX, e.offsetY]
    });
    canvas.addEventListener('mousemove', draw)
    canvas.addEventListener('mouseup', () => {
      drawing = false
    });
    canvas.addEventListener('mouseleave', () => {
      drawing = false
    })
  </ script>
</body>

</html>
```

### 源码分析

[Canvas](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API)

#### 属性

- `lineCap`：笔触的形状，有 round | butt | square 圆、平、方三种。
- `lineJoin`：线条相交的样式，有 round | bevel | miter 圆交、斜交、斜接三种。
- `lineWidth`：线条的宽度
- `strokeStyle`：线条描边的颜色
- `fillStyle`：填充的颜色

#### 方法

- `beginPath()`：新建一条路径
- `stroke()`：绘制轮廓
- `moveTo()`：（此次）绘制操作的起点
- `lineTo()`：路径的终点

### 彩虹渐变颜色——HSL  

在这个挑战中，涉及到改变线条的颜色，如何实现彩虹的渐变效果？我们需要利用 HSL 色彩模式，首先可以去这个网站 [http://mothereffinghsl.com](http://mothereffinghsl.com/) 感受一下 HSL 不同色彩值对应的效果。

- H(hue) 代表色调，取值为 0~360，专业术语叫色相
- S 是饱和度，可以理解为掺杂进去的灰度值，取值为 0~1
- L 则是亮度，取值也是 0~1，或者百分比。

这之中 H 值从 0 到 360 的变化代表了色相的角度的值域变化，利用这一点就可以实现绘制时线条颜色的渐变了，只需要在它的值超过 360 时恢复到 0 重新累加即可。

```js
let colorDeg = 0;

ctx.strokeStyle = `hsl(${ colorDeg }, 90%, 50%)`;	
colorDeg = colorDeg < 360 ? colorDeg + 1 : 0;
```

除此之外，如果想实现黑白水墨的颜色，可以将颜色设置为黑色，通过透明度的改变来实现深浅不一的颜色。

### 控制笔触大小

```js
 //		控制笔触大小
      if (lineWidth > 50 || lineWidth < 10) {
        direction = !direction
      }
      direction ? lineWidth++ : lineWidth--;
      ctx.lineWidth = `${lineWidth}`;
```

上面的代码中，根据线条的宽度的变化来控制`direction`的值，根据`direction`的值来控制线宽是增加还是减少。


### 控制线条路径

```js
      // 控制绘制路径
	  ctx.beginPath();
      ctx.moveTo(x, y);
      console.log(x, y);
      ctx.lineTo(e.offsetX, e.offsetY);
      [x, y] = [e.offsetX, e.offsetY];
      ctx.stroke();
```

### 事件监听代码逻辑分析

```js
canvas.addEventListener('mousedown', (e) => {
// 开始绘图
 drawing = true;
// 绘图起始坐标初始化
 [x, y] = [e.offsetX, e.offsetY]
});

// 鼠标移动时，调用draw方法
canvas.addEventListener('mousemove', draw);
// 鼠标抬起时，将drawing置为false
canvas.addEventListener('mouseup', () => drawing = false);
// 当鼠标不在可绘图区域范围内时，将drawing置为false
canvas.addEventListener('mouseout', () => drawing = false);
```

