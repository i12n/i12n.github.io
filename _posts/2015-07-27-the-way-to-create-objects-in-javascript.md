---
layout: post
title: "JavaScript 创建对象的方法"
description: ''
category: 'web'
tags: ['JavaScript', 'Object']
---
{% include JB/setup %}

JavaScript 是一种面向对象的编程语言(OOP)，但是与C++等面向对象的编程语言相比，JavaScript具有很多独特之处。在JavaScript中，创建对象有两种方式：对象字面量方式创建对象和使用构造函数创建对象。

<!-- more -->

<h2 id="using-object-initilizers">
对象字面量方式
</h2>

对象字面量方式(object literal notation)，也被称为对象初始化(object initalizers)。在JavaScript中，对象是属性的集合，每个属性都由name和value组成。对象字面量方式创建对象时，就是直接指定对象属性的name和value。
    
    var fruit = {
        name: 'apple',
        price: '3.14',
        showPrice: function () {
            console.log(this.price);
        }
    };

    fruit.showPrice(); // 3.14

<h2 id="using-constructor">
构造函数创建对象
</h2>

与C++中创建对象的方式比较类似，也要使用new关键字。当然，如果创建的不是JavaScript原生对象类型，就要自定义构造函数了。
    
    var fruit = new Object;
    fruit.name = 'apple';
    fruit.price = '3.14';
    fruit.showPrice = function () {
        console.log(this.price);
    }

    fruit.showPrice(); //3.14

<h2 id="difference-between-2-ways">
两种创建对象方式的区别
</h2>

- 通常，使用字面量方式创建对象，会使用更少的代码量.

        var o = {}; // same as new Object()


- 如果，对象的属性中有比较多的方法，同时又要大量创建对象，使用字面量方式创建对象会造成一定程度上的内存浪费。而基于原型的构造函数可以解决内存的浪费。

<h2 id="setting-object-properties">
对象的属性设置
</h2>

访问和设置对象的属性可以使用` . ` 操作符。除此之外，还可以使用 `[]` 访问和设置对象的属性，特别是属性名称里包含空格等字符以及动态设置属性时。

    var fruit = {};
    var name = 'weight';
    var value = '100g';

    fruit.color = 'red';
    fruit['color is red'] = true;
    fruit[name] = value;

    console.log(fruit.color); // red
    console.log(fruit['color is red']); //true
    console.log(fruit.weight); // 100g

<h2 id="create-object-referance">
参考资料
</h2>

[What is the difference between `new Object()` and object literal notation?](http://stackoverflow.com/questions/4597926/what-is-the-difference-between-new-object-and-object-literal-notation) -- Stack Overflow   
[Working with objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)  -- Mozilla Developer Network  
