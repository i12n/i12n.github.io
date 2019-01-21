---
layout: post
title: "CSS Transitions"
description: ""
category: web
tags: [ css ]
---
{% include JB/setup %}

##CSS Transitions 简介
CSS 提供了两种动画效果，分别为 **transitions**  和 **animations** 。当 CSS 属性发生变化时，使用 CSS transitions 指定 CSS 属性，使这些属性在限定时间内 **平滑过渡** 到最终呈现效果，而不是立即呈现最终效果。简单来说就是，CSS transitions  能够实现效果的平滑过渡，它可作用于所有的元素包括 :before 和 :after 伪元素。

<!-- more -->

<iframe width="100%" height="200" src="//jsfiddle.net/moonatcs/Lra1saLr/embedded/result,css,html/" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

##CSS Transitions 四个属性值

- `transition-property` - 指定应用 transition 的 CSS 属性名称

	`transition-property: none | <single-transition-property> [ ‘,’ <single-transition-property> ]*`
	`<single-transition-property> = all | <IDENT>`
	
	可以指定一个或多个 CSS 属性，也可以指定所有属性。

        /* 指定一个 CSS 属性 */
        transition-property: background-color;

        /* 指定多个 CSS 属性 */
        transition-property: background-color, color;

        /* 指定所有 CSS 属性 */
        transition-property: all;

- `transition-duration` - 限定 transition 时间

	`transition-duration:	<time> [, <time>]*`
	
	可以设置一个或多个时间值，与 transition-property 中的属性名称有对应关系。

		div {
			transition-property: background-color, color, width, height;

			/* 设置一个时间值 */
			transition-duration: 1s;
		}
	等价于为每一个 transition-property 中的属性都设置 1s 的变化时间，即：

		div {
			transition-property: background-color, color, width, height;

			/* 设置多个时间值 */
			transition-duration: 1s, 1s, 1s, 1s;
		}
	而下面的情况：

		div {
			transition-property: background-color, color, width, height;
			transition-duration: 1s, 2s;
		}
	等价为：

		div {
			transition-property: background-color, color, width, height;
			transition-duration: 1s, 2s, 1s, 2s;
		}

- `transition-timing-function` - 设定 transition  的变化速率的函数

	`transition-timing-function: <single-transition-timing-function> [ ‘,’ <single-transition-timing-function> ]*`

	`<single-transition-timing-function> = ease | linear | ease-in | ease-out | ease-in-out | step-start | step-end |`
	`steps(<integer>[, [ start | end ] ]?) | cubic-bezier(<number>, <number>, <number>, <number>)`
	
	其中包含有 ease、linear、ease-in、ease-out、ease-in-out 等函数。
	
	<iframe width="100%" height="330" src="//jsfiddle.net/moonatcs/wbzrmmkz/embedded/result,css,html" allowfullscreen="allowfullscreen" frameborder="0">
	</iframe>
	
	在 [CSS-TRICKS][1] 提供关于 transition-timing-function 更详细的介绍。


- `transition-delay` - 设定延迟触发 transition 的时间

	`transition-delay: <time> [, <time>]*`

##CSS Transitions 简写形式
	
`transition` - CSS transitions 的简写形式
		
`transition: <single-transition> [ ‘,’ <single-transition> ]*`

`<single-transition> = [ none | <single-transition-property> ] || <time> || <single-transition-timing-function> || <time>`

## Transition Events

当 transition 完成之后触发事件 transitionend（ webkitTransitionEnd MSTransitionEnd oTransitionEnd ），不同的浏览器定义的事件名称不同。点击 [MDN][2] 可以阅读关于 transitionend 更多介绍。

<iframe width="100%" height="200" src="//jsfiddle.net/moonatcs/7L5bf5h2/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

##相关资料

MDN - [Using CSS transitions](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Using_CSS_transitions?redirectlocale=en-US&redirectslug=CSS%2FTutorials%2FUsing_CSS_transitions)

W3C - [CSS Transitions](http://www.w3.org/TR/css3-transitions/)

[1]: http://css-tricks.com/almanac/properties/t/transition-timing-function/	"CSS-TRICKS"
[2]: https://developer.mozilla.org/en-US/docs/Web/Events/transitionend "MDN"