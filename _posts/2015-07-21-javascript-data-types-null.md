---
layout: post
title: "JavaScript 数据类型: Null"
description: ""
category: "web"
tags: ["JavaScript", "null"]
---
{% include JB/setup %}

Null 是JavaScript 基本数据类型之一。

<!-- more -->

<h2 id="null-type">
数据类型Null
</h2>

Null 类型只有一个值 null，表示一个空对象指针。如果一个变量将来要保存对象，可以将该变量初始化化为 null ，以方便检查它是否保存了一个对象的引用。

    var o = null;
    console.log(o === null); // true

    o={};
    console.log(o === null); // false

由于 null 表示空对象指针，所以对 null 使用 typeof 操作符返回的是 'object'，因此typof 是检测不出一个变量的类型是否为 Null 类型。

    console.log(typeof null);   // 'object'

<h2 id="null-equal">
null 与相等操作符
</h2>

在相等和不等操作符中( `==` 和 `!=`)，null 和 undefined 都不会发生类型转换。

    console.log(null == false); // false
    console.log(null == true);  // false
    console.log(null == 0);     // false
    console.log(null == NaN);   // false
    console.log(null == '');   // false

但是 null 和 undefined 是相等的。
    
    console.log(null == undefined); // true
    console.log(null === undefined); // false

在判断对象是否为空时，使用`==`和 `===`存在一些区别的。当变量未初始化(undefined)或者为空指针(null)，在进行`== null` 判断时都返回 true 。因此与下面代码等价：

    value === null || value === undefined; // 与 value == null;等价

>More of that, except being more compact, *\'value == null\'* also will be protected from *\'undefined\'* replacements, and the browser also won\'t perform a variable name lookup, because null is a keyword and there is nothing besides it. Which will make the things faster.   
>  -- [Nikolay V. Nemshilov](http://st-on-it.blogspot.hk/2011/03/why-null-not-null.html)





