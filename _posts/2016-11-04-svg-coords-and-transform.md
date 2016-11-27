---
layout: post
title: "SVG 坐标系统与坐标转换"
description: ""
category: svg
tags: [svg]
---
{% include JB/setup %}

## 世界、视窗与视野

SVG 的世界是指用来定义内容的区域（画布），这个区域是无限大的；而视窗是浏览器用于渲染 SVG 世界的一个矩形窗口，通过这个窗口用户可以看到 SVG 世界中的内容；而视野是指选取的 SVG 世界中的一个有限区域。举个例子如下图所示，SVG 的世界可以看做是一张无限大的世界地图，里面包含了山川、河流等地理信息(类似于 SVG 世界中的各种线条、形状、颜色等内容)；iPad的屏幕相当于一个视窗，它能够把地图的内容某绘制出来；当我们想看到地图的某个区域时，可以通过滑动等操作来控制，这相当于定义了一个视野。
 
![Example iPad](/assets/images/svg/iPad.png)

视窗的大小是由 `svg` 标签的 width 和 height 属性来定义，浏览器会根据这两个属性来提供相应的区域。如果 width 和 height 不写单位则默认单位为 px。

    <svg width="100" height="100"></svg>
     
视野是由`svg` 标签的 viewBox 来定义。viewBox 属性值由 "x y width height" 组成，表示视野的范围是 SVG 世界的(x, y) 和 (x + width, x + height) 所围成的矩形区域。 为了便于理解可以把 (x, y)、((x + width)/2, (y + height)/2) 和 (x + width, y + width) 分别称为 (xMin, yMin)、(xMid, yMid) 和 (xMax, yMax)。

    <svg width="100" height="100" viewBox="0 0 200 400"></svg>
     
不难发现视窗和视野两者的 width 和 height 可以不一致，那么视野所定义的区域如何在视窗中绘制出来呢？具体的展示规则还需要依赖 preserveAspectRatio 属性。preserveAspectRatio 的属性值由两个部分组成：align 和 meetOrSlice，align 控制对齐视窗的方式，meetOrSlice 控制 viewBox 覆盖视窗的方式。具体见下图：

 ![Example PreserveAspectRatio](https://www.w3.org/TR/SVG/images/coords/PreserveAspectRatio.png)

在视窗的 width 和 height 确定前提下，更改 viewBox 的 width 和 height 会出现缩放的效果。如果 preserveAspectRatio: none;
则 viewBox 并不按等比例缩放，而是填充满整个视窗；preserveAspectRatio 为其他值的时候都是按等比例缩放，如果 meetOrSlice 为 meet 则要把整个 viewBox 都显示在视窗中，width、 height 比例最大的一边充满视窗， 如果 meetOrSlice 为 slice 则会把比例 width、 height 最小的一边充满视窗，超出部分不会显示。



## SVG 坐标系统
SVG 的坐标系统包括视窗坐标系(viewport coordinate system) 和 用户坐标系(user coordinate system)，两者的区别在于作用空间不一样，视窗坐标系是作用于视窗，而用户坐标系是作用于 SVG 世界。由于坐标变换会生成新的的视窗坐标系和用户坐标系，为了便于区分，我们把最原始的坐标系称为初始坐标系。
原始视窗坐标系和原始用户坐标系的原点都是位于左上角，x正方向为水平向右，y正方向为竖直向下，并且这个两个坐标系是重合的，默认单位都是1px。

- 用户坐标系变换

前面提到，viewBox 属性会使SVG的内容发生缩放，其实质是在用户空间自动做了一次坐标变换，生成了一个新的用户坐标系。在新的坐标系里单位发生了变化，不再是默认的1px，这个坐标系会作用于所有的后代元素以及 viewBox 所在元素上的属性(width、height、x、y 等属性例外)。

除此之外 transform 属性也会进行坐标变换，它可以定义平移、旋转和缩放等变换操作。而且同一个元素的可以组合使用变换操作，每一次变换都是基于上一次变换生成的坐标系。

[代码示例](https://jsfiddle.net/moonatcs/9kwe7tbw/)

<script async src="//jsfiddle.net/moonatcs/9kwe7tbw/embed/html,result/dark/"></script>

- 视窗坐标系转换

在 svg 元素中可以嵌套一个 svg 子元素，这个 svg 子元素通过 x、y 属性来确定位置。svg 子元素中的新视窗坐标系和新用户坐标系都是以x 和 y 属性变换之后的值为原点，单位大小与 svg 子元素所在用户坐标系的单位大小一致。

[代码示例](https://jsfiddle.net/moonatcs/eye9xj7e/)

<script async src="//jsfiddle.net/moonatcs/eye9xj7e/embed/html,result/dark/"></script>

## transform 属性

transform 属性主要用于坐标变换，包括以下几种坐标变换

- 矩阵变换 （Matrix）
    
        maxtrix(a b c d e f)
        
        等价矩阵
        T = [a c e, b d f, 0 0 1] 

- 平移变换（Translation）

        translate(x [y]) // 如果不设置 y，则 y 为 0
        
        // 等价矩阵
        matrix(1 0 0 1 x y)
        T = [1 0 x, 0 1 y, 0 0 1]

- 缩放变换（Scale）

        scale(sx sy)

        // 等价矩阵
        maxtrix(sx 0 0 sy 0 0)
        T = [sx 0 0, 0 sy 0, 0 0 1]


- 旋转变换（Rotation）
        
        rotate(a [x y]) // 如果设置 x 和y 则等价于 translate(x, y) rotate(a) translate(-x, -y)
        
        // 等价矩阵
        matirx(cos(a) sin(a) -sin(a) cos(a) 0 0)
        T = [cos(a) -sin(a) 0, sin(a) cos(a) 0, 0 0 1]

- 斜切变换 (Skew)
    
    x 轴

        skewX(a)
    
        // 等价矩阵
        matrix(1 0 tan(a) 1 0 0)
        T = [1 tan(a) 0, 0 1 0, 0 0 1]
    
    y轴     

        skewY(a)
        
        // 等价矩阵
        matrix(1 tan(a) 0 1 0 0)
        T = [1 0 0, tan(a) 1 0, 0 0 1]

SVG transform 属性和 HTML transform 属性的区别在于前者的坐标变换是以坐标系原点为参考进行变换，而后者是以元素自身的原点为参考进行变换。HTML transform 默认原点为 (50%, 50%) 可以用 transform-origin 设置原点。

## 坐标映射

当前用户坐标系和前驱用户坐标系之间的坐标映射关系：

![MatrixNested](https://www.w3.org/TR/SVG/images/coords/MatrixNested.png)

    [xpre, ypre, 1] = T1·T2· ... ·Tn·[xcur, ycur, 1]

例如对于下面的 SVG:
[代码示例](https://jsfiddle.net/moonatcs/woqmzce9/)

    <svg veiwBox="0 0 200 200">
      <g transform="translate(100 20) scale(2) rotate(45)">
        <path d="M0 0h40 v40 l0 0z"></path>
      </g>
    </svg>

计算坐标

    | x |    | 1 0 100 |   | 2 0 0 |   | cos(45) -sin(45) 0       |   | x0 |
    | y | =  | 0 1 20  | · | 0 2 0 | · | 0        sin(45) cos(45) | · | y0 |
    | 1 |    | 0 0 1   |   | 0 0 1 |   | 0        0       1       |   | 1  |

转换结果如下

    <svg veiwBox="0 0 200 200">
      <g>
        <path d="M100 20l56.568542494923804 56.5685424949238-56.5685424949238 56.568542494923804 0 0z"></path>
      </g>
    </svg>


[svgpath](https://www.npmjs.com/package/svgpath) 是一个对 path 进行转换的工具，可以将当前坐标系转换到前驱坐标系。实现上面的转换：

    var svgpath = require('svgpath');
    svgpath('M0 0h40 v40 l0 0z').rotate(45).scale(2).translate(100,20).toString();


## 相关资料：
 - [http://www.w3cplus.com/html5/svg-coordinate-systems.html](http://www.w3cplus.com/html5/svg-coordinate-systems.html)  
 - [http://www.w3cplus.com/html5/svg-transformations.html](http://www.w3cplus.com/html5/svg-transformations.html)  
 - [http://www.w3cplus.com/html5/nesting-svgs.html](http://www.w3cplus.com/html5/nesting-svgs.html)  
 - [https://www.w3.org/TR/SVG/coords.html](https://www.w3.org/TR/SVG/coords.html)  
 - [https://css-tricks.com/transforms-on-svg-elements/](https://css-tricks.com/transforms-on-svg-elements/)
