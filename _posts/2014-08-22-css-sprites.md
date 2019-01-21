---
layout: post
title: "CSS Sprites"
description: ""
category: web 
tags: [ css ]
---
{% include JB/setup %}

CSS Sprites 是将一些图片集合成一个大图，通过 CSS 中的 background position 来控制图片的显示部分。
这样做的好处是，减少了 HTTP requests 数量，使网站的加载速度更快。这是因为每一个 /<img/> 标签 以及 
background-image 属性都对应一个  HTTP requests ，将多个图片集成一个大图，最终只需要一次  HTTP requests 。

<!-- more -->

> Back in the day, everybody and their brothers were "slicing" images to make pages load faster. 
> All that technique did was fool the eye to make it look like 
> the page was loading faster by loading bits and pieces all over at once. 
> Each one of those images is a separate HTTP-Request, 
> and the more of those, the less efficient your page is.    
> [*CSS Sprites*](http://css-tricks.com/css-sprites/)  *- CSS Tricks*
<style>
	#close {
	    width: 30px;
	    height: 30px;
	    background-image: url('/assets/images/web/css/2014082201.png');
	    display: inline-block;
		vertical-align: middle
	}
	#close:hover {
	    background-position: -44px 0px;
	}
</style>
在下面的例子中，将两个关闭图标合并到一个图片中 ![](/assets/images/web/css/2014082201.png) ,
然后通过 CSS 来控制显示具体的图标 
<a id="close" href="#" >
</a>
主要代码：

	a {
	    background-image: url('/assets/images/web/css/2014082201.png');
	    display: inline-block;
	}
	a:hover {
	    background-position: -44px 0px;
	}

通过设置 background-position 属性来显示图片的不同部分。
[详细代码示例](//jsfiddle.net/e7duy47q/)

CSS Sprites 的缺点在于，当对图片某个位置改变时，需要对相应的 CSS 样式进行修改。
