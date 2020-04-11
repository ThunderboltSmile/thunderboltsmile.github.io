---
title: CSS3生成复杂图案
categories:
  - 前端开发
  - 读书笔记
tags: 
  - css
  - css3
---

在现代网页制作中，许多情况下需要制作复杂的图案效果，但背景图片的解决方案有着很大的局限性，因此使用css生成图案是一种对用户友好的方式。

涉及到的主要概念：background,background-image,background-size,background-position,linear-gradient。
最主要的介绍下linear-gradient.
<!-- more -->
#### linear-gradient
>MDN
The linear-gradient() CSS function creates a linear, progressive transition between two or more colors. Its result is an object of the gradient data type, which is a special kind of image.

Syntax:
```css
linear-gradient( 
  [ <angle> | to <side-or-corner> ,]? <color-stop> [, <color-stop>]+ )
  \---------------------------------/ \----------------------------/
    Definition of the gradient line        List of color stops  

where <side-or-corner> = [left | right] || [top | bottom]
  and <color-stop>     = <color> [ <percentage> | <length> ]?
```
解释：linear-gradient是css内置的函数，用于生成由颜色渐变而形成的图片，接收的参数有，渐变线的渐变方向（角度或者垂直方向），色标值和色标区域大小。

tip1:当两个色标区域处于同一位置时，也就是两个色标区域在本身定义的大小范围内相互重合，则渐变效果会消失，同时出现颜色的突然变化，我们可以利用这个特性来生成条纹等具有清晰颜色边界的图案。

tip2:如果某个色标区域值大小比之前声明的色标区域大小都要小，由于解释器会将颜色按照声明顺序排列，因此会以之前定义过的色标中位置最大的位置作为该色标的位置，我们可以利用这一点简化渐变的写法。

tip3:在background-image属性中，可使用一个或多个渐变来生成复杂图案。试想一下，当背景图片中有着各种角度渐变时，我们的操作空间可以有多大。

#### 网格
通过两个相互垂直的渐变图案进行组合，可以很容易的网格图形：
```css
background:white;
background-image:linear-gradient(90deg,rgba(200,0,0,0.5) 50%,transparent 50%),
                 linear-gradient(rgba(200,0,0,0.5) 50%,transparents 50%);
background-size:30px 30px
```
多个渐变之间以逗号分隔，同时，利用`background-size`将渐变区域总大小限制在30px*30px的区域内，此时，在外部容器中，背景将会重复直至铺满外部容器。

#### 波点
通过径向渐变，也就是`radial-gradient`可以生成波点图案

```css
background:#655;
background-image:radial-gradient(tan 30%,transparent 0),
                 radial-gradient(tan 30%,transparent 0);
background-size:30px 30px;
background-position:0 0, 15px 15px;
/*用过将两幅单调的波点图案进行交错，可以产生常见的波点图案*/
```
可以使用scss简化代码（虽然变多了，但其实抽象度更高

```scss
@mixin polka($size,$dot,$base,$accent){
    background:$base;
    background-image:
        radial-gradient($accent $dot,transparent 0),
        radial-gradient($accent $dot,transparent 0);
    background-size:$size $size;
    background-position:0 0, $size/2 $size/2;
}
/*想要创建波点时，可以很方便的调用*/
@include polka(30px,30%,#655,tan)
```

#### 棋盘
由于css无法创建出周围是空白的方块，我们转而使用另外一种方案，即两个三角形拼接成方块。再将两组方块相互交错，即可得到棋盘图案。




