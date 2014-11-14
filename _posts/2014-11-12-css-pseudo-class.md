---
layout: post
title: "CSS 伪类"
description: ""
category: web 
tags: [ css ]
---
{% include JB/setup %}

伪类分为两种：
`UI 伪类` 在 HTML 元素处于某个状态时，为该元素应用 CSS 样式。
`结构化伪类` 存在某种结构关系时，为相应的元素应用 CSS 样式。
<!-- more -->
## UI 伪类
1. `链接伪类` 链接伪类有4个，需要按列出的顺序（**L**o**V**e **HA**te）指定它们，否则浏览器的不出预定的结果。其中 `:hover` 和 `:active` 除了用于链接，还能用于其他元素。
	- `:link`  链接未被访问过，与 `:visited` 互斥。
	- `:visited` 链接被鼠标点击过。
	- `:hover` 鼠标在链接（或元素）上悬停。
	- `:active` 链接正在被点击（或元素正在被激活），鼠标在元素上按下，但还没有释放。

2. `:focus` 伪类
	用法：*e*:focus 向拥有键盘输入焦点的元素添加样式。
	
	<iframe width="100%" height="100" src="http://jsfiddle.net/moonatcs/x0exrxhx/embedded/result,html,css"  frameborder="0">
	</iframe>
	
3. `:target` 伪类
	选取当前活动的目标元素。对于指向一个锚的 URI `http://example.com/document.html#section2`, id 属性值为 section2 的元素为目标元素。在下面的代码中，`id="answer"` 的 `<p>` 元素就是 `<a>`锚的目标元素。

		#answer{
		  display: none;
		}
		#answer:target{
		  display: block;
		}
		<p>1+1=? &nbsp;<a href="#answer">查看答案</a></p>
		<p id="answer">答案是： <span>2</span></p>
	`:target` 能用于 `display: none` 的元素(从上面的代码就可以看出)，可作为开/关控制元素的是否可见。维基百科中引证链接大量使用了 `:target` , 可以很方便的找到链接所对应的引证。使用 `:target` 可以实现 Tab 切换效果（控制 z-index ）。

	<iframe width="100%" height="200" src="http://jsfiddle.net/moonatcs/g3mgoz97/embedded/result,html,css" allowfullscreen="allowfullscreen" frameborder="0">
	</iframe>
	
	[CSS-TRICKS][1] 提到可以使用 `:target` 三种情况：  
	>1. When a "state" is needed  
	>2. When the jump-down behavior is acceptable  
	>3. When it's acceptable to affect the browser history  
	
	另外，关于跳转的问题在文中也有介绍。
	
	[MDN][2] 有两个例子：
	>1. The :target pseudo-class also works fine with undisplayed elements.
	>2. The :target pseudo-class is useful to switch on/off some invisible elements.
	
	如何在浏览器中新的页面框中打开 WEB 页面		
		
		<a href='#' target='_bank'>new</a>

4. `:enabled` 和 `:disabled` 伪类

		form input:enabled {
		  background:#eee;
		}
		form input:disabled {
		  background:#550;
		}
		
    	<form>
		  <label>允许输入</label><input type="text" >
		  <label>禁止输入</label><input type="text" disabled ="disabled">
		</form>
    
##结构化伪类

 `:first-child` 一组同胞元素中的第一个，也就是其父元素的第一个子元素  
 `:last-child` 一组同胞元素中的最后一个，也就是其父元素的最后一个子元素  
 `:nth-child(n)`一组元素中的第n个，也就是其父元素的第 n 个子元素
 
 使用 :nth-child(n) 可以设置表格交替效果：
 
<iframe width="100%" height="200" src="http://jsfiddle.net/moonatcs/wcetw91j/embedded/result,html,css" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

` e:nth-child(n) ` 与 ` e :nth-child(n) ` 的区别：
前者选择的是父元素的第 n 个子元素且这个子元素为 `e` ；后者选择的是 `e` 作为父元素选择它的第 n 个子元素。另外还要注意，第 n 个子元素 指的是从 1 开始计数且对所有子元素都要包括在内，而不是 第 n 个 为`e` 的子元素。 [MDN][3] 上的例子有助于理解这个问题。

通过伪类选择器可以为元素添加一些样式，虽然也可用 JavaScript 来实现其中的一些样式，但是使用伪类比较方便简单；另外，将伪类用于上下文选择符中，能够实现更强大的效果，例如，可以实现下拉菜单。


[1]: http://css-tricks.com/on-target/ "CSS-TRICKS"
[2]: https://developer.mozilla.org/en-US/docs/Web/CSS/:target?redirectlocale=en-US&redirectslug=CSS%2F%3Atarget "Mozilla Developer Network"
[3]: https://developer.mozilla.org/en-US/docs/Web/CSS/:nth-child " Mozilla Developer Network "