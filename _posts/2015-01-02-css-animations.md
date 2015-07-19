---
layout: post
title: "CSS Animations"
description: ""
category: web
tags: [ css ]
---
{% include JB/setup %}

Transitions 实现的动画效果，是把指定的 CSS 属性从初始值平滑过渡到终点值，相当于在动画效果中只包括了两个关键帧。因此，Transitions 是不能实现由多个关键帧组成的动画效果，可以使用 Animations 来实现这种相对复杂的动画效果。Animations 可以定义多个关键帧，而且在每个关键帧中可以定义元素的属性值。
<!-- more -->

<iframe width="100%" height="200" src="http://jsfiddle.net/moonatcs/f4x8y09b/embedded/result,css,html" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

##Animations 属性

|                              |                                       |
|------------------------------|---------------------------------------|
|   animation-name             |  指定 @keyframes                      |
|   animation-duration         |  动画一个周期持续的时间               |
|   animation-iteration-count  |  动画循环次数，无限循环infinite       |
|   animation-delay            |  延迟一定时间后播放动画，负数表示快进 |
|   animation-timing-function  |  关键帧之间渐变过程                   |
|   animation-fill-mode        |  动画开始之前与结束之后设定元素属性值 |
|   animation-play-state       |  指定动画暂停或者播放                 |
|   animation-direction        |  设置动画播放方向，正向或者反向       |   


animation-delay 主要控制开始播放动画的时刻，默认值为 0 。
当它的值为正数 n 时表示延迟一定时间后播放动画， 
也就是动画在开始播放之前要等待的时间为 n；
当它的值为负数 -n 时，则动画会立即从动画中的第 n 时刻开始播放。
animation-delay只在动画开始播放之前起作用，一旦动画开始播放不会在播放过程中起作用。

<iframe width="100%" height="200" src="http://jsfiddle.net/moonatcs/jpnrz1jk/embedded/result,css,html" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

animation-timing-function 是设置关键帧与关键帧之间的渐变过程而不是整个动画循环的渐变过程，
可以为关键帧单独设置 animation-timing-function 值, 默认值为 ease。
	
<iframe width="100%" height="200" src="http://jsfiddle.net/moonatcs/oyv7zsg9/embedded/result,css,html" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>
	
animation-fill-mode 动画结束之后设定元素样式。

<iframe width="100%" height="250" src="http://jsfiddle.net/moonatcs/h8kLf4z6/embedded/result,css,html" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>


##相关资料
MDN - [Using CSS animations](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Using_CSS_animations)

