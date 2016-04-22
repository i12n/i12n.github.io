---
layout: post
title: "nightmare 命令行提示"
description: "命令行提示输入相关信息"
category: [web]
tags: [nightmare]
---
{% include JB/setup %}

有些场景，nightmare 是不能自动完成的，这时需要人为去填一些信息，例如当用户登录时，常常需要填写图片验证码，而nightmare 是无法识别验证码的，这就需要人为去填写。

<!-- more -->

nightmare 任务脚本自动执行过程中，如果需要手工处理某些信息的时候，例如，验证码、密码等表单的填写，这时有两种方案。

第一种方案就是, 设置 wait 等待时间，只要等待的时间足够长，表单的填写就能够完成，例如下面的例子 -- 在登录 github 时，手动输入密码：

    var Nightmare = require('nightmare');

    var nightmare = new Nightmare({
            show: true,
            waitTime: 10000
        });

    nightmare
        .goto('https://github.com/session')
        .wait('#login_field')
        // 输入账号
        .type('#login_field', 'test@example.com')
        // 等待4s ，输入密码
        .wait(4000)
        .click('input[type=submit]')
        .wait('.flash-error')
        .evaluate(function() {
            // 返回登录错误信息
            return document.querySelector('.flash-error').textContent.trim();
        })
        .end();
        .then(function(text) {
            console.log(text);
        });

这种方法的缺点是，wait 等待的时间不容易确定，时间过短输入可能还未完成，时间过长等的时间又比较长；另外就是，在这段时间没有给出填写密码的任何提示，很可能忘记或不知道当前要输入密码。


第二种方案是使用命令提示的方式完成表单信息的输入。这种方法需要引入相关工具，这里使用的是 [prompt](https://www.npmjs.com/package/prompt)，prompt 是一个命令提示符工具，主要用于 node 项目。在 nightmare 中可使用 prompt，根据提示符输入一些信息。

    var Nightmare = require('nightmare');
    var prompt = require('prompt');

    var nightmare = new Nightmare({
            show: true,
            waitTime: 50000
        });

    nightmare
        .goto('https://github.com/session')
        .wait('#login_field')
        // 输入账号
        .type('#login_field', 'test@example.com')
        .then(function() {
            // 输入密码
            prompt.start();
            return new Promise(function(resolve, reject){
                prompt.get(['password'], function(err, values){
                    if(err){
                        return reject(err);
                    }
                    resolve(values['password']);
                })
            })
        })
        .then(function(password) {
            return nightmare
                .type('#password', password)
                .click('input[type=submit]')
                .wait('.flash-error')
                .evaluate(function() {
                    // 返回密码错误信息
                    return document.querySelector('.flash-error').textContent.trim();
                })
                .end()
        })
        .then(function(value){
            console.log(value);
        });


进一步了解更多，可参考[详细文档]('https://github.com/segmentio/nightmare/issues/543');

## 总结
主要介绍了如何从控制台以提示符的方式，将一些信息提供给 nightmare 处理。
