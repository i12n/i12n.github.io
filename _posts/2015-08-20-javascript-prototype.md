---
layout: post
title: "JavaScript prototype"
description: "JavaScript 原型"
category: web 
tags: [JavaScript]
---
{% include JB/setup %}

JavaScript 在实现继承等面向对象的特征时，离不开原型的支持。使用原型可以实现对象共享和继承属性和方法。

<!-- more -->

<h2 id="object-prototype">
对象与原型
</h2>

每个对象内部都包含一个指针[[Prototype]]，这个指针指向原型对象。虽然没有标准方式访问到 [[Prototype]] ，Chrome、Firefox 等浏览器为对象增加了 \_\_proto\_\_ 属性，而 \_\_proto\_\_ 属性则指向原型对象。
    
    // Constructor
    function Fruit (name, price) {
      this.name = name;
      this.price = price;
    }
    
    var apple = new Fruit('apple', 3.14); // 对象实例 apple
    console.log(apple.__proto__);

    // result
    Fruit {
        constructor: Fruit(name, price)
        __proto__: Object
    }

默认情况下原型对象会自动获得一个 constructor 属性，这个属性指向构造函数。

    console.log(apple.__proto__.constructor === Fruit); // true

<h2 id="constructor-prototype">
构造函数与原型
</h2>

尽管通过对象的 \_\_proto\_\_ 可以访问原型对象，但毕竟不是标准的方法，不是每一种浏览器都支持。访问原型对象的标准方式是通过构造函数来访问。
每一个函数都有一个 prototype 属性，这个属性也指向原型对象。

    console.log(apple.__proto__ === Fruit.prototype); // true

这说明同一个构造函数生成的实例所指向的原型对象是同一个。

    var banana = new Fruit('banana', 0.68);
    console.log(apple.__proto__ === banana.__proto__); // true

实现对象之间属性和方法共享，也正是由于这一点。

<h2 id="prototype-chain">
原型链
</h2>

由于原型对象也是对象，所以它也有 \_\_proto\_\_ 属性，
对象的原型属性构成了原型链，原型链的尽头为 null 。

    console.log(apple.__proto__.__proto__);
    
    // result
    Object {
        constructor: Object()
        toString: function toString()
        get __proto__: get __proto__()
        set __proto__: set __proto__()
    }

    console.log(apple.__proto__.__proto__.__proto__); // null

当对象访问某个属性时，如果在对象实例内部找不到该属性，就会在原型链上查找。

    apple.toString(); // "[object Object]"


<h2 id="use-prototype">
原型应用
</h2>

在构造函数定义的属性和方法，会在不同的实例对象中重复创建，而对于实现同一个功能的方法来说，重复定义意味着资源的浪费。

    function Fruit (name, price) {
        this.name = name;
        this.price = price;
        this.sayName = function(){
            console.log(this.name);
        }
    }

    var apple = new Fruit('apple', 3.14);
    var banana = new Fruit('banana', 0.68);

    console.log(banana.sayName == apple.sayName); // false


同一个构造函数定义的各个实例对象指向同一个原型对象，可以在原型对象上定义属性和方法以实现实例之间的共享。

    Fruit.prototype.sayName = function() {
      console.log( this.name );
    }

    var apple = new Fruit('apple', 3.14);
    var banana = new Fruit('banana', 0.618);
    
    console.log(apple.sayName === banana.sayName); // true

对象能访问原型对象上的属性，是因为当对象读取某个属性时，首先从实例本身进行搜索，如果在实例中查找到指定名字的属性，则将属性的值返回；如果没有找到则对原型的对象进行搜索；也就是说会在整个原型链上查找属性，直到找到了该属性或者到了原型对象的尽头null，才停止查找。原型链的这个特点可以实现属性的集成，将父类的实例对象作为子类的原型对象，子类对象就可以通过原型链查找到父类的方法。

    // Fruit constructor
    function Fruit (name, price) {
      this.name = name;
      this.price = price;
    }

    Fruit.prototype.sayName = function() {
      console.log( this.name );
    }

    // Pear constructor
    function Pear(color) {
      this.color = color;
    };

    Pear.prototype = new Fruit('pear','1.44');

    // Pear object
    var pear = new Pear('green');
    pear.sayName(); // pear


<h2 id="reference">
参考资料
</h2>

[A Plain English Guide to JavaScript Prototypes](http://sporto.github.io/blog/2013/02/22/a-plain-english-guide-to-javascript-prototypes/)
