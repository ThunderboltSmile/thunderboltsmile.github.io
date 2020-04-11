---
title: svg简介
date: 2017-07-023
categories:
  - 前端开发
tags:
  - svg
---

>svg：Scalable Vector Graphics (SVG) is an XML -based markup language for describing two-dimensional vector graphics.

现代网页中许多能够形象的表达功能的图标都是用svg技术实现的，这种技术实现的图案可以在任意比率下放大而不出现失真，因为是矢量图案，并且占用的资源也比传统图片少很多。
<!-- more -->
#### svg在html中的嵌入形式
    
1.`<embed src="circle1.svg" type="image/svg+xml" />`
  所有浏览器都支持，允许使用脚本，不能用于html4
2.`<object data="circle1.svg" type="image/svg+xml"></object>`
  所有浏览器都支持，但不支持脚本
3.`<iframe src="circle1.svg"></iframe>`
  所有浏览器都支持，支持脚本
4.直接嵌入
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
	<circle cx="100" cy="50" r="40" stroke="black" stroke-width="2" fill="red" />
</svg>
```

#### svg支持的图形

*   矩形   `<rect>`
*	圆形   `<circle>`
*	椭圆   `<ellipse>`
*	线     `<line>`
*	折线   `<polyline>`
*	多边形 `<polygon>`
*	路径   `<path>`