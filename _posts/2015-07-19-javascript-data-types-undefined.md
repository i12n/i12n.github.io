---
layout: post
title: "JavaScript 数据类型： Undefined"
description: ""
category: web
tags: ['JavaScript', 'undefined']
---
{% include JB/setup %}

Undefined 是 JavaScript 的基本数据类型之一，下面进行简单的介绍。

<!-- more -->
<h2 id="datatype-undefined">
	Undefined 数据类型
</h2>

Undefined 数据类型只有一个值 `undefined`，当一个变量被声明但未被初始化时，这个变量就是 Undefined 类型，并且其默认值为 `undefined`。

*声明与定义的区别，变量的声明是让编译器知道变量的存在；变量的定义是为变量分配内存空间。*

<h2 id="undefined-in-scope">
	window.undefined
</h2>

`undefined` 值只存在于规范或文档中，在代码中看到的 undefined 通常是全局对象属性 window.undefined，该属性的初始值就是 `undefined`。
通常情况下 *console.log(a === undefined);* 中的 undefined 就是 window.undefined。

	console.log(typeof undefined === 'undefined'); //true

	var a;
	console.log(a === undefined); //true

*在 ES3 中，window.undefined 的值是可以更改的，因此在某些情况下要确保使用的是[真正的 `undefined` 值](#really-undefined)；在ES5中，window.undefined 是不可更改的(non-configurable, non-writable)。*

另外，undefined 并不是保留字，可以在全局作用域链之外的作用域链中作为变量名使用。

	(function() {
		var undefined = true;

		console.log(typeof undefined === 'undefined'); //false
		console.log(window.undefined === undefined); //false
		console.log(undefined === true); //true
	})();

<h2 id="check-undefined">
	变量初始化判断
</h2>

判断一个变量是否被初始化，有两种方式：第一种判断变量的值是否为 `undefined` ，第二种使用 typeof 判断变量的类型是否是 Undefined 数据类型。

	var a;
	console.log(a === undefined); // true
	console.log(typeof a === "undefined") // true

两者的区别在于在变量未声明时，前者会抛出异常，而后者却不会。

	console.log(a === undefined); // ReferenceError
	console.log(typeof a === 'undefined'); //true

但是前者的优点在于，js压缩之后占用的字符个数更少。

	console.log(a === undefined);
	// 可以压缩为
	console.log(a === u);

*由于 undefined 派生自 null， 因此 console.log(null == undefinded) 的值为 true 。在判断是否为 `undefinded` 值时，通常要用 === 操作符。*

判断变量是否声明，可以通过阅读代码人为判断；用 in 操作符判断全局作用域链和其他对象的属性。

	// 判断 window.undefined 是否存在
	console.log(undefined in window); //true
<h2 id="really-undefined">
undefined 形参
</h2>

由于 [undefined 在作用域链中可以更改](#undefined-in-scope)，为了避免 undefined 的值被更改而得不到 `undefined` 值，在某些库里常常以下面的方式来保证使用的是真正的 undefined 值。

	(function (window, document, undefined) {
		//
	})(window, document);

函数定义中使用 undefined 作为形参，而又不为它传递实参，那么它的值就是 `undefined` 值。这样做的目的是确保使用 a === undefined 这样的语句来判断变量初始化是有效的。

*void 操作符也可以返回 `undefined` 值，在有些函数库中则是使用的是 void 0 来判断变量是否初始化，例如 a === void 0。使用 void 0 好处是字节数比较少。无论是使用 void 0，还是使用这种 undefined 形参，在保证真正的 undefined 值的同时，还避免了在作用域链上查找 window.undefined ,因此效率更高。*

<h2 id="undefined-conversions">
undefined 转型
</h2>

`undefined` 值转换为 Number 为 NaN；转换为 Boolean 为 false；转换为 String 为 'undefined'。

	console.log(+undefined); // NaN
	console.log(!undefined); // true
	console.log('hello '+undefined); // hello undefined

<h2 id="undefined-references">
参考资料
</h2>

[Global Objects: undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined) -- Mozilla Developer Network  
[Understanding JavaScript’s 'undefined'](https://javascriptweblog.wordpress.com/2010/08/16/understanding-undefined-and-preventing-referenceerrors/) -- Angus Croll  
[What (function (window, document, undefined) {})(window, document); really means](http://toddmotto.com/what-function-window-document-undefined-iife-really-means/) -- Todd Motto  
[How Does This JavaScript/Jquery Syntax Work: (function( window, undefined ) { })(window)?](http://stackoverflow.com/questions/2716069/how-does-this-javascript-jquery-syntax-work-function-window-undefined) -- Stack Overflow   
[How to Check for Undefined in Javascript](http://stackoverflow.com/questions/3390396/how-to-check-for-undefined-in-javascript) -- Stack Overflow 
[Operators: void](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void) -- Mozilla Developer Network
