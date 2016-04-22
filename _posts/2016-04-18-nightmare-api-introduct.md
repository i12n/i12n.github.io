---
layout: post
title: "nightmare API 简单介绍"
description: "nightmare API 介绍"
category: [ web ]
tags: [ nightmare ]
---
{% include JB/setup %}

nightmare 的 API 不是特别的多，平常使用比较多的主要是配置 nightmare 以及与页面交互的相关API，这里是官方给出的 [nightmare 文档](https://www.npmjs.com/package/nightmare)可以参考学习。

<!-- more -->

## 相关配置 API

API                             |   描述                                                |
--------------------------------|------------------------------------------------------
Nightmare(options)              |   初始化实例，options 为设置 Electron 浏览窗口的配置，[参考文档](https://github.com/electron/electron/blob/master/docs/api/browser-window.md#new-browserwindowoptions)
waitTimeout (default: 30s)      |   wait 等待最长时间，超过这个时间还未返回，则抛出异常
paths                           |   为 Electron 设置数据路径，[参考文档](https://github.com/atom/electron/blob/master/docs/api/app.md#appgetpathname)
switches                        |   chrome 的一些配置，[参考文档](https://github.com/atom/electron/blob/master/docs/api/chrome-command-line-switches.md)

以上的 API 均为 nightmare 初始化实例对象的配置，例如我们要配置浏览窗口的大小，如下所示：
    
    var nightmare = new Nightmare({
        width: 1000,
        height: 800
        show: true
    });

## 与页面交互API


API                             |   描述
------------------------------- | -------------------------------------------------------
.goto(url)                      |   加载 url 页面  
.back()                         |   后退到前一页   
.forward()                      |   前进到下一页  
.refresh()                      |   刷新当前页  
.click(selector)                |   点击selector元素  
.mousedown(selector)            |   鼠标按下selector元素
.type(selector [, text])        |   如果有text参数，则向 selector 输入 text，否则清空 selector , 并触发键盘事件
.insert(selector [, text])      |   与type功能一致，但是不触发键盘事件，输入速度快于.type
.check(selector)                |   选中 selector checkbox
.uncheck(selector)              |   取消选中 selector checkbox
.select(selector,option)        |   选中 dropdown 的option选项
.scrollTo(top, left)            |   屏幕滚动
.viewport(width, height)        |   调整视窗大小
.inject(type, file)             |   注入本地的 js、css 文件
.evaluate(fn[, arg1, arg2])     |   在页面中执行fn
.wait(ms \| selector \| fn)     |   等待ms秒, 等待 selector, 等待 fn
.exists(selector)               |   selector 是否存在
.visible(selector)              |   selector 是否是可见的
.on(event, callback)            |   捕获页面上的事件
.title()                        |   返回当前页面的title   
.url()                          |   返回当前页面的url
.screenshot()                   |   截图


上面的 API 都是与页面之间进行交互，其中有几个方法比较特别，将进一步对其介绍。

- **nightmare 的 .evaluate() 方法**

evaluate(fn[, arg1, arg2 ...]) 方法是在浏览器端执行 fn 函数，因此对 dom 节点的一些读写操作要使用  evaluate() 方法才行。

    var Nightmare = require('nightmare');
    var nightmare = new Nightmare({ show: true });
    var content;
    nightmare
         .goto('https://example.com')
         .evaluate(function(selector){
              return document.querySelector(selector).textContent; // 返回dom节点信息
         }, '.head')
         .then(function(res) {
              console.log(res); // 打印返回的节点信息
         });

要注意以下几点：
首先，evaluate() 方法是在浏览器端执行 fn 函数，也就是说 fn 函数的执行环境是浏览器环境，而不是 node 环境，因此一些浏览器不支持的 ES6 特性，在 fn 函数中不要使用
其次，浏览器中的dom信息只能通过fn的返回值来获得，而提供给fn使用的数据只能通过参数传递，也就是说不要使用全局变量的方式进行数据传递

例如：

    var Nightmare = require('nightmare'),
        nightmare = new Nightmare({ show: true }),
        content,                // A
        selector = '.head';     // B

    nightmare
         .goto('https://example.com')
         .evaluate(function(selector) {
              let msg = 'test'; // 浏览器不支持的特性, 错误
              content = document.querySelector(selector).textContent; // 使用 A, B 处定义的变量，错误
         })
         .then();

[关于evaluate() 的坑](https://github.com/rosshinkley/nightmare-examples/issues/14)

- **nightmare 的 .wait() 方法**

wait() 方法的作用是等待某个事件发生，这些事件包括：一段时间结束、等待某个dom元素出现、等待某个函数执行完成。wait(fn) 方法与 evaluate(fn) 方法类似，fn 也是要在浏览器上执行，但是最后要返回 true 才能结束等待，最长的等待时间为 waitTimeout 设置的时长。

    var Nightmare = require('nightmare');
    var nightmare = Nightmare({ show: true });

    nightmare
        .goto('https://baidu.com')
        .wait(function() {
            // 在 baidu 的页面中绑定一个 dom 节点
            var ndTest = document.createElement('p');
            ndTest.innerHTML = 'This is test for nightmare\'s wait';
            ndTest.className = 'test';
            document.body.appendChild(ndTest);
            return true;
        })
        .wait('.test')
        .evaluate(function() {
            // 返回节点文本信息
            return document.querySelector('.test').textContent;
        })
        .then(function(res) {
            console.log(res);
        });

- **nightmare 的 .insert() 与 .type() 方法**

两者都能向 web 页面的输入框输入信息，但是存在着一些差别。type() 再输入过程中触发键盘事件，而 insert() 不触发键盘事件，但是insert() 输入的速度要快一些。因此如果输入框要响应键盘输入的事件，这时就要 type()了。


## 总结

主要介绍 nightmare 的 API，nightmare 的配置参数主要分为三个方面：Electron 配置参数（nightmare 的底层是 Electron 框架）、Chrome 配置参数（Electron 使用了 Chrome 浏览器）以及自身的配置。nightmare 与外面的交互 API 可以分为两类：其一，操作类 API 如  wait、click、evaluate  等；其二，输出结果 API 如 title、exists 等。使用时最值得注意的就是 evaluate()，很容易出错。 


