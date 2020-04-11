---
title: svg-line&polyline&polygan
date: 2017-07-021
categories:
  - 前端开发
tags:
  - svg
---
#### line
用于创建直线

```
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <line x1="0" y1="0" x2="200" y2="200"
  style="stroke:rgb(255,0,0);stroke-width:2"/>
</svg>
```
属性：
* x1,y1 属性定义线条的开始坐标
* x2,y2 属性定义线条的结束坐标
<!-- more -->
#### polyline
用于创建折线
```
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <polyline points="20,20 40,25 60,40 80,120 120,140 200,180"
  style="fill:none;stroke:black;stroke-width:3" />
</svg>
```
属性：
points:定义一系列的坐标，以空格间隔，描述折线经过的点

#### polygan
用于定义多边形，polygan可以理解为定义了一条封闭的折线，即首尾相连的折线，所以在使用时，points属性的点一定要按照折线生成路径顺序来声明。
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:nonzero;" />
</svg>
```