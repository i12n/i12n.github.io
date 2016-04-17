---
layout: post
title: "使用 nightmare 进行页面测试（一）"
description: "nightmare 测试介绍"
category: [web]
tags: ['nightmare']
---
{% include JB/setup %}
[nightmare](https://github.com/segmentio/nightmare) 是一个高层次浏览器自动化库，通过一些简单的方法就可以实现在网站上自动执行任务。nightmare 的底层使用 Electron 框架，Electron 相当于一个迷你的 Chromium 浏览器，可以将 web 页面渲染出来。 nightmare 的一系列 API 可以将对网页的交互操作保存为脚本，执行该脚本这一系列的操作将在 Electron 中自动完成。目前很多人用 nightmare 来测试 web 页面。下面将介绍如何使用 nightmare 进行测试。

<!-- more -->

## nightmare 使用方法

首先安装 Nightmare：

    $ npm install nightmare

如果在安装过程中下载 electron-v0.36.11-darwin-x64.zip 失败，可以使用下面的方法安装：

    $ ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/" npm install nightmare

electron-v0.36.11-darwin-x64.zip 的下载由 electron-download 来处理，默认下载地址是 https://github.com/atom/electron/releases/download/v0.36.11/electron-v0.36.11-darwin-x64.zip  ，目前速度特别慢。根据 [electron-download](https://www.npmjs.com/package/electron-download) 里面的文档介绍，我们可以更改下载镜像改为taobao的就可以了。

在 nightmare 实现自动执行任务过程时，要将任务以脚本的形式描述出来，nightmare 提供的方法和 API 比较简单易用。例如下面的脚本实现的是在 github 上搜索 nightmare。

    var Nightmare = require('nightmare');
    var nightmare = Nightmare({show: true, width: 1000});
    nightmare
        .goto('https://github.com/')
        .type('.header-search-input', 'nightmare\r\n')
        .wait('.codesearch-results')
        .wait(2000)
        .evaluate(function () {
            var ndName = document.getElementsByClassName('repo-list-name')[0];
            return ndName.getElementsByTagName('a')[0].href;
        })
        .then(function(result) {
            console.log(result);
        })
    nightmare.end();

执行这段代码，将打开一个浏览窗口并进入github，在搜索框中输入 `nightmare` 进行搜索，并将第一个搜索结果的链接打印。不难发现，这只是一个自动执行任务的脚步，并没有任何关于测试的内容，这是因为 nightmare 自身并没有测试的功能，需要测试框架的支持。

## 测试框架 mocha

[mocha](https://github.com/mochajs/mocha) 是一个功能非常丰富的 JavaScript 测试框架，可以运行在 Node.js 或 浏览器上，并且能够和很多断言库共同使用。

全局安装：

    $ npm install mocha -g

本地安装：

    $ npm install mocha

本地安装需要修改 package.json：

    "scripts": {
        "test": "node ./node_modules/mocha/bin/mocha"
    }

一个简单的 测试脚本：

    var assert = require('assert');
    describe('Array test', function() {
         describe('#indexOf()', function () {
              it('should return -1 when the value is not present', function () {
                   assert.equal(-1, [1,2,3].indexOf(5));
                   assert.equal(-1, [1,2,3].indexOf(0));
              });
         });
    });

上面的代码用来测试 Array 的 indexOf 方法。将这段代码保存在 ./test/ 路径，使用下面的命令就可以运行脚本：

    # 全局安装的 mocha
    $ mocha

    # 本地安装的mocha
    $ npm test

在上面的例子里面，使用了node 的原生断言模块 assert ，assert 相对比较底层。在单元测试时，一般会使用should.js、chai.js 等断言库，下面主要介绍一下 chai.js。

## 断言库 chai.js

[chai](https://github.com/chaijs/chai) 是一个 BDD/TDD 风格的断言库，可以很好的与mocha等测试框架结合使用。使用 chai 可以将上面的代码重写为：

    var chai = require('chai');
    chai.should();
    describe('Array test', function() {
         describe('#indexOf()', function () {
              it('should return -1 when the value is not present', function () {
                   [1,2,3].indexOf(5).should.equal(-1);
                   [1,2,3].indexOf(0).should.equal(-1);
              });
         });
    });

## web 页面测试

将 mocha、chai、nightmare 结合到一起就可以对 web 页面进行测试了。我们将对上面的例子 -- 在 github 中搜索 `nightmare` -- 设计一个测试用例，即搜索结果中的第一个链接应该为 `https://github.com/segmentio/nightmare` (nightmare 在 github 上的仓库地址)。

    var Nightmare = require('nightmare');
    var chai = require('chai');

    // 使 mocha 支持 generators
    require('mocha-generators').install()

    chai.should();

    describe('github test', function() {
        this.timeout(10000)

        // it 的 callback 为 generators
        it('should eable seach nightmare', function *() {
            var nightmare = Nightmare({show: true, width: 1000});
            var link = yield nightmare
                .goto('https://github.com/')
                .wait('.header-search-input')
                .type('.header-search-input', 'nightmare\r\n')
                .wait('.codesearch-results')
                .wait(1000)
                .evaluate(function () {
                    var ndName = document.getElementsByClassName('repo-list-name')[0];
                    return ndName.getElementsByTagName('a')[0].href;
                })
                .end();

            link.should.be.equal('https://github.com/segmentio/nightmare');
        });
    });

对上面的代码做一下说明：首先，要安装 [mocha-generators](https://github.com/vdemedes/mocha-generators) 可以使 mocha 支持 ES6 generators；在测试用例中 it 的 callback 函数为 generators 函数。

启动测试用例，可以下面效果：

![github-search-nightmare-test-opraters](/assets/images/web/nightmare/github-search-nightmare-test-opraters.gif)

测试结果如下：

![github-search-nightmare-test-result](/assets/images/web/nightmare/github-search-nightmare-test-result.png)


## 总结

本文主要简单介绍了如何使用 nightmare 、mocha 和 chai 进行web 页面测试。nightmare 给我们带来的好处就是，不需要以手动操作方式来完成页面的测试。nightmare 的 API 使用以及注意事项这里并为提及，将在后面再介绍。
