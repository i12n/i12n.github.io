---
layout: post
title: "JavaScript 操作符相关"
description: ""
category: "web"
tags: [JavaScript]
---
{% include JB/setup %}

操作符的相关总结。

<!-- more -->

<h2 id="value-convert">
类型值转换
</h2>

在操作符进行操作时，很多时候会将操作数的值转换为所需要的类型值。

1. 转换为 Bool 型值

undefined null 0 NaN '' -> false

2. 转换为 Number 型值

undefined -> NaN
null '' -> 0
包含非数字字符的字符串 ->NaN
全是数字字符的字符串 -> 对应的数字
对象 先 valueOf() , 如果转换后是 NaN ，那么再 toString()

<h2 id="bool-operators">
布尔操作符：或和与
</h2>

|| 和 && 两个操作符返回的不一定是 true 和 false，其返回值的规则如下：

    var a = b || c;  // a = b ? b : c
    var a = b && c;  // a = !b ? b : c

<h2 id="addtive-operators">
加减法操作符
</h2>

加法和减法的一个主要区别是，加法支持字符串的加操作。因此当有一个操作数是字符串时，加法将另外一个操作数转换为字符串处理，最终是字符串加操作；而减法则是将这个字符串转换为数值，最终是数值减操作

<h2 id="relational-operators">
关系操作符
</h2>
关系操作符也能比较字符串的大小，但是与加法操作符不同之处在于，当有一个操作数是字符串是字符时，将这个字符串转换为数值，最终进行数值比较。

<h2 id="equality-operators">
相等操作符
</h2>

undefined == null 为 true；
