---
title: svg-rect
date: 2017-07-022
categories:
  - 前端开发
tags:
  - svg
---

#### `<rect>` 创建矩形：
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <rect width="300" height="100"
  style="fill:rgb(0,0,255);stroke-width:1;stroke:rgb(0,0,0)"/>
</svg>
```
属性解释
* rect 元素的 width 和 height 属性可定义矩形的高度和宽度
* style 属性用来定义 CSS 属性
* CSS 的 fill 属性定义矩形的填充颜色（rgb 值、颜色名或者十六进制值）
* CSS 的 stroke-width 属性定义矩形边框的宽度
* CSS 的 stroke 属性定义矩形边框的颜色
<!-- more -->
#### x,y,fill-opacity,stroke-opacity
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <rect x="50" y="20" width="150" height="150"
  style="fill:blue;stroke:pink;stroke-width:5;fill-opacity:0.1;
  stroke-opacity:0.9;opacity:0.5"/>
</svg>
```
属性解释
* x 属性定义矩形的左侧位置（例如，x="0" 定义矩形到浏览器窗口左侧的距离是 0px）
* y 属性定义矩形的顶端位置（例如，y="0" 定义矩形到浏览器窗口顶端的距离是 0px）
* CSS 的 fill-opacity 属性定义填充颜色透明度（合法的范围是：0 - 1）
* CSS 的 stroke-opacity 属性定义笔触颜色的透明度（合法的范围是：0 - 1）
* CSS opacity 属性用于定义了元素的透明值 (范围: 0 到 1)。

#### rx,ry
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <rect x="50" y="20" rx="20" ry="20" width="150" height="150"
  style="fill:red;stroke:black;stroke-width:5;opacity:0.5"/>
</svg>
```
属性解释
* rx 和 ry 属性可使矩形产生圆角，但是这个圆角的定义和border-radius不同，rx，ry定义的是在单一方向的弯曲程度。