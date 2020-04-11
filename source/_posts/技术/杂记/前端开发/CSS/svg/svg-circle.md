---
title: svg-circle&&ellipse
date: 2017-07-020
categories:
  - 前端开发
tags:
  - svg
---

#### circle
用于定义圆形
属性：cx,cy,r
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <circle cx="100" cy="50" r="40" stroke="black"
  stroke-width="2" fill="red"/>
</svg>
```
解释：cx,cy定义了圆心的位置（相对于左上角）
      r定义了圆的半径
<!-- more -->
#### ellipse
用于定义椭圆
属性：cx, cy, rx, ry
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <ellipse cx="300" cy="80" rx="100" ry="50"
  style="fill:yellow;stroke:purple;stroke-width:2"/>
</svg>
```
解释：rx, ry 分别为椭圆两个轴的半轴长

