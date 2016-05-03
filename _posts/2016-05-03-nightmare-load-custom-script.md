---
layout: post
title: "nightmare 注入自定义脚本"
description: "nightmare 注入JavaScript 或 css"
category: web
tags: [nightmare]
---
{% include JB/setup %}

nightmare 在执行自动化任务时，有时需要注入一些额外的JavaScript 或 css 代码，
例如，可能需要将 jQuery 等工具加载到浏览器窗口里面；也可能需要在加载web页面之前要做一些额外的处理。nightmare 提供了两种加载外部脚本的方式，一种是使用 preload 进行配置，另外一种是使用 .inject() 方法。

<!-- more -->

##自定义 preload 脚本##

在 window 第一次加载的时候，可以通过配置 preload 脚本来做一些额外的事情。如果不配置 preload 脚本， nightmare 在默认情况下会加载自带的 [preload 脚本](https://github.com/segmentio/nightmare/blob/master/lib/preload.js)，这个脚本的作用就是阻止了alert 的弹窗以及console输出。我们也可以加载一个自定义 preload 脚本，方法如下：

    var nightmare = Nightmare({
        webPreferences: {
            preload: custom-script.js
        }
    })
 
其中 custom-script.js 的开头要包含下面两行代码：

    window.__nightmare = {};
    __nightmare.ipc = require('ipc');
     
通过 preload 配置的脚本会在页面加载之前执行，因此 preload 可以做一些初始化的操。

##inject JavaScript 或 CSS##

使用 inject(type, file) 方法也可以注入脚本，可以是 js 或 css 文件：
     
    nightmare
        .goto('example.url')
        .inject('js', 'script.js'))
     
其中注入的 script.js 会在页面加载完成之后执行。另外，nightmare 的版本小于 2.3.0 会有一个[bug](https://github.com/segmentio/nightmare/issues/523) , js 脚本会执行两次。另外值得注意的是，inject 脚本的执行的作用域为函数作用域，声明的变量并不是全局变量，可[参考文档](https://github.com/rosshinkley/nightmare-examples/blob/master/docs/known-issues/globally-defined-variables.md)。

##总结##
两种注入脚本的方法加载的脚本的时机不同，preload 是在页面加载之前引入脚本，inject 则是在页面加载完成之后引入脚本。
下面的例子更能体现各自的特点：


    var Nightmare = require('nightmare');
    var path = require('path');
    var nightmare = new Nightmare({
            show: true,
            webPreferences: {
                preload: path.join(__dirname, 'custom-script.js')
            }
        });

    nightmare
        .goto('https://github.com')
        .inject('css', path.join(__dirname, 'custom-style.css'))
        .wait(2000)
        .then(function(){
        });

    nightmare.end();

custom-script.js 如下：

    window.__nightmare = {};
    __nightmare.ipc = require('ipc');

    (function() {
        window.onload = function () {
            // 增加 header 的 className
            var header = document.getElementsByTagName('header')[0];
            header.className += ' custom-site-header';
            alert(header.className);
        }
    })();

custom-style.css 如下：

    .custom-site-header {
        background-color: #21A675 !important;
    }

以上的代码实现的功能就是当 github 页面加载完成，更换了 header 部分的背景颜色。
