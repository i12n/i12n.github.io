---
layout: post
title: "nightmare 的 screenshot 方法在测试中的应用"
description: "介绍nightmare 的 screenshot 以及在测试中使用该方法"
category: [web]
tags: [nightmare]
---
{% include JB/setup %}

nightmare 的 screenshot 方法可以对窗口进行截图，通过将截图进行对比可测试页面的 UI 变化。

<!-- more -->

## .screenshot 方法

.screenshot(image, option) 方法有两个参数，第一个参数 image 是截图保存的文件名称，第二个参数 option 是截图的配置信息，包括起始位置和长宽。

    var nightmare = new Nightmare({
            show: true
        });
    // 截图的配置信息
    var option = {
        x: 0, 
        y: 90,
        width: 250,
        height: 30
    }

    nightmare
        .goto('https://github.com/segmentio/nightmare')
        .wait('.entry-title')
        // 截图保存为 ./joxeankoret.png
        .screenshot('./joxeankoret.png', option)
        .end()
        .then(function() {
        });

## 测试中的应用
首先介绍一个图片处理工具 [gm](https://www.npmjs.com/package/gm)，它可以用来对图片进行处理，如压缩、对比等。在使用前要保证 GraphicsMagick 或 ImageMagick 中的一个已经安装，因为 gm 的是在它俩的基础上实现的。  
先实现一个图片对比功能：

    var gm = require('gm');
    function compare(image1, image2) {
        return new Promise(function(resolve, reject) {
            var options = {
                file: './diff.png', // 对比结果生成一张图片
                //highlightColor: 'red',
                //highlightStyle: 'XOR',
                tolerance: 0.02 // 差异程度阈值，小于这个阈值将返回true，否则返回false
            };
            // 回调函数的参数 isEqual 表示对比结果是否一致，equality 为差异程度 
            gm.compare(image1, image2, options, function(err, isEqual, equality) {
                if(err) {
                    reject(err)
                }
                resolve([isEqual, equality]);
            });

        });
    }

再次基础上，与 nightmare 结合，对比截图的差异，将上面的截图代码进行改造：

    nightmare
        // 进入第一个url 截图
        .goto('https://github.com/segmentio/nightmare')
        .wait('.entry-title')
        .screenshot('./segmentio.png', option) // option 的定义在上面的第一段代码中
        // 进入第二个url 截图
        .goto('https://github.com/joxeankoret/nightmare')
        .wait('.entry-title')
        .screenshot('./joxeankoret.png', option)
        .end()
        .then(function() {
            // 调用上面定义的 compare 函数
            return compare('./segmentio.png', './joxeankoret.png');
        })
        .then(function(res) {
            // 输入对比结果
            console.log(res);
        })
        .catch(function(err) {
            console.log(err);
        });

输出结果：
[ false, 0.0240098279 ]

第一张截图：
![](/assets/images/web/nightmare/segmentio.png)

第二张截图：
![](/assets/images/web/nightmare/joxeankoret.png)

两者对比图：
![](/assets/images/web/nightmare/diff.png)

有时，github 加载的时候有一个请求会被拒绝，等待时间比较长，打开vpn就好了。

测试脚本还需要 mocha 支持，不在这里介绍，可参考之前写的相关文档。

## 总结
使用 nightmare 的 screenshot 方法可以用来截图，并使用 gm 进行对比检测 ui 变化。但是这种方式，还是比较粗糙，毕竟 nightmare 一开始设计的初衷不是用来测试的。
