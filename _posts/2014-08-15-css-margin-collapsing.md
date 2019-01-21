---
layout: post
title: "CSS 外边距合并"
description: ""
category: web
tags: [css]
---
{% include JB/setup %}

CSS 外边框合并（margin collapsing）是指普通文档流中块框的垂直方向的外边距（margin-top，margin-bottom）在一些境况下会
合并成一个外边距，且为所有参与合并的外边距最大的值。

<!-- more -->

注意以下几点：
	1.垂直方向的外边距（ top 和 bottom ）才能发生合并，水平方向（ left 和 right ）的不会发生。
	2.普通文档流中的块框才会发生合并，而行内框 浮动或绝对定位元素的外边距不会发生。 

**发生外边距合并的几种情况：**

1. 相邻的兄弟元素外边距合并（Adjacent siblings）
	
	> The margins of adjacent siblings are collapsed 
	> (except when the later sibling needs to be cleared past floats).  
	> [*Margin Collapsing*](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing)*-MDN*
	
	![]({{ site.url }}/assets/images/web/css/2014081501.png)
	
	[简单示例](//jsfiddle.net/p70tyedx/)

2. 父元素和首/尾子元素合并（Parent and first/last child）
	
	> If there is no border, padding, inline content, 
	> or clearance to separate the margin-top of a block with the margin-top of its first child block, 
	> or no border, padding, inline content, height, min-height, 
	> or max-height to separate the margin-bottom of a block with the margin-bottom of its last child,
	> then those margins collapse. 
	> The collapsed margin ends up outside the parent.    
	> [*Margin Collapsing*](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing)*-MDN*
	
	![]({{ site.url }}/assets/images/web/css/2014081502.png)
	
3. 空块框合并（Empty blocks）
	
	> If there is no border, padding, inline content, height, 
	> or min-height to separate a block's margin-top from its margin-bottom, 
	> then its top and bottom margins collapse.  
	> [*Margin Collapsing*](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing)*-MDN*
	
	![]({{ site.url }}/assets/images/web/css/2014081503.png)
	
4. 以上三种情况组合时，可能会发生多次合并
	
	![]({{ site.url }}/assets/images/web/css/2014081504.png)
	
	[简单示例](//jsfiddle.net/Lg1kz592/)
	