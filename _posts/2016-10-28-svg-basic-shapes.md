---
layout: post
title: "SVG 基本形状"
description: ""
category: svg
tags: [svg]
---
{% include JB/setup %}
SVG 提供了一些基本的形状用于图形绘制，这些形状可以直接用来使用和操作.

<!-- more -->

1. `<rect>` 矩形, 拥有如下属性

        x       矩形左上角x位置, 默认值为 0 
        y       矩形左上角y位置, 默认值为 0
        width   矩形的宽度, 不能为负值否则报错, 0 值不绘制
        height  矩形的高度,  不能为负值否则报错, 0 值不绘制
        rx      圆角x方向半径, 不能为负值否则报错
        ry      圆角y方向半径, 不能为负值否则报错

    关于 rx 和 ry 的还有如下规则:
    - 如果 rx 和 ry 都没有设置, 则 rx = 0 ry = 0
    - 如果 rx 和 ry 有一个被设置, 则全部取这个被设置的值
    - rx 的最大值为 width 的一半, ry 的最大值为 height 的一半
    
        rx = rx || ry || 0;
        ry = ry || rx || 0;

        rx = rx > width / 2 ? width / 2 : rx;
        rx = ry > width / 2 ? width / 2 : ry;

    使用示例如下
    
    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/e1we5aL0/1/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

2. `<circle>` 园, 拥有如下属性

        r       半径
        cx      圆心x位置, 默认为 0
        cy      圆心y位置, 默认为 0

    使用示例如下
        
    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/mhxt6g38/1/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

3. `<ellipse>`椭圆, 拥有如下属性

        rx      椭圆x半径
        ry      椭圆y半径
        cx      圆心x位置, 默认为 0
        cy      圆心y位置, 默认为 0

    使用示例如下

    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/df9njock/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

4. `<line>` 线段, 拥有如下属性

        x1      起点x, 默认为 0
        x2      终点x, 默认为 0
        y1      起点y, 默认为 0
        y2      终点y, 默认为 0
   
    使用示例如下

    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/z0Lzozb8/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

5. `<polyline>` 折线, 只有一个属性
        
        points  折线上的点集，每个点都是一个坐标

    使用示例如下

    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/jnq0e978/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

6. `<ploygon>` 多边形, 只有一个属性

        points  折线上的点集，每个点都是一个坐标

    使用示例如下

    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/mr0txav1/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>


7. `<path>` 路径, 功能非常强大，上面介绍的六个基本图形均可由路径实现，路径是由一些命令来控制的，每一个命令对应一个字母，并且区分大小写，大写主要表示绝对定位，小写表示相对定位。`<path>` 通过属性 *d* 来定义路径， *d* 是一系列命令的集合，主要有以下几个命令：

    - M = moveto 移动到某一点，只移动画笔位置而不绘制
        
            M x y 或 m dx dy

    一个路径必须要以 moveto 命令开始，后续的 moveto 命令则表示一个子路径的开始。另外，moveto 命令后面可以跟多个坐标，从第二个坐标开始相当于 lineto 命令。如果一个路径以 `m` 则第一个坐标是绝对坐标，其余坐标是相对坐标。例如：
            
            // 以下两个等价
            d='M 10 10 20 20'     // (10, 10) (20 20) 都是绝对坐标
            d='M 10 10 L 20 20'

            // 以下两个等价
            d='m 10 10 20 20'     // (10, 10) 绝对坐标, (20 20) 相对坐标
            d='M 10 10 l 20 20'


    - L = lineto 当前位置和新位置之间绘制一条直线

            L x y 或 l dx dy
            
    - H = horizontal lineto   水平直线

            H x 或 h dx

    - V = vertical lineto     竖直直线

            V y 或 v dy

    - C = curveto                             三次贝塞尔曲线

            C x1 y1 x2 y2 x y 或 c dx1 dy1 dx2 dy2 dx dy

        (x1, y1) (x2, y2) 是两个控制点，(x, y) 为曲线终点

    - S = smooth curveto                      三次光滑贝塞尔曲线
        
            S x2 y2 x y  或 s dx2 dy2 dx dy

        S 与 C 的不同之处是少了第一个控制点，那是因为 S 命令是将多个贝塞尔曲线光滑的连接到一起，所用它的第一个控制点是通过上一个贝塞尔曲线的第二个控制点推断出来的; 如果它前面没有C 或 S 命令，则第一个控制点也取值为 (x2, y2)，也就是说两个控制点都取值 (x2, y2)

    - Q = quadratic Belzier curve             二次贝塞尔曲线

            Q x1 y1 x y 或 q dx1 dy1 dx dy

        (x1, y1) 为控制点，(x, y) 为曲线终点

    - T = smooth quadratic Belzier curveto    二次光滑贝塞尔曲线
    
            T x y 或 t dx dy

        作用与 S 类似

    - A = elliptical Arc      弧线

            A rx ry x-axis-rotation large-arc-flag sweep-flag x y
            a rx ry x-axis-rotation large-arc-flag sweep-flag dx dy

        rx 和 ry 表示 x 轴半径和 y 轴半径   
        x-axis-rotation 表示弧形旋转角度    
        large-arc-flag 取值为 1 表示大角弧，取值为 0 表示小角弧     
        sweep-flag 取值为 1 表示顺时针绘制弧，取值为 0 表示逆时针绘制弧     
        x 和 y 有表示弧的终点位置坐标       

    - Z = closepath           闭合路径

        结束当前子路径，在当前子路径上绘制一条从终点到起点的直线，也就是说 closepath 命令之后，画笔的位置处于当前路径的起点位置，因此如果下一个子路径的开始没有 moveto 命令，则当前子路径的起点也是下一个路径的起点。

            // 以下两者等价
            d='M20 40 l 40 60 100 80z l 100 10 80 80z' // l 100 10 的起点为绝对坐标(20, 40)
            d='M20 40 l 40 60 100 80z M 20 40 l 100 10 80 80z'
    
    
    直线:

    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/byx9v2qz/embedded/html,result/dark" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

    贝塞尔曲线:

    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/wmozwd8e/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

    弧线：
    
    <iframe width="100%" height="300" src="//jsfiddle.net/moonatcs/pvnwqg5t/embedded/html,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>



参考资料

[Basic shapes](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Basic_Shapes) -- MDN

