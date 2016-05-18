---
layout: post
title: "nightmare 操作队列和异步"
description: "nightmare 异步相关简单介绍"
category: [web]
tags: ['nightmare']
---
{% include JB/setup %}

在使用 nightmare 的时候，如果在忘记了在代码的最后调用 `then` 方法，会发现 nightmare 不会执行任何操作。这是因为每一个 nightmare 实例都有一个操作队列，而这个操作队列保存着 nightmare 的一系列操作。而 nightmare 的每一个链式调用只是将操作保存到队列里面，并没有立刻执行操作。

<!-- more -->

例如下面这段代码：

    nightmare
        .goto('https://github.com')
        .wait(1000)
        .evaluate(function(){})
        .end()
        

如果仅仅执行这段代码，在打开浏览窗口之后不会有任何操作结果。这是因为，这段代码是将两个操作 `goto` 和 `wait` 放到了操作队列中，但是并没有执行这两个操作。而 `end` 的作用是在执行完队列中所有的操作之后，关闭 electron 进程即浏览窗口会被关闭(end 也是一个操作)。由于没有队列里面的操作没有执行，所以浏览窗口也会一直打开。

如果在上面的代码中的最后调用 `run`，则会执行队列中的所有操作：

    nightmare.run(function(e) {
            console.log('ok!!!');
        })

所有的队列里面的操作都是同步执行的，在操作执行完毕之后会调用回调函数。为了更好的支持异步，我们很少使用`run` 而是使用 `then`。可以参考文档[nightmare run](https://github.com/rosshinkley/nightmare-examples/blob/master/docs/beginner/callbacks.md)、[nightmare async operations loops](https://github.com/rosshinkley/nightmare-examples/blob/master/docs/common-pitfalls/async-operations-loops.md)。

then 方法返回的是一个 Promise：
   
    var Nightmare = require('nightmare');
    var nightmare = Nightmare({ show: true });
    nightmare
        .goto('https://github.com')
        .then(function(e) {
            console.log('ok!!!');
        })
        .then(function(e) {
            return nightmare
                    .goto('https://google.com')
                    .end()
        })
        .then(function(e) {
            console.log('ok!!!');
        })

nightmare 可以很好地与 co 等一起使用。例如：

    var co = require('co');
    var Nightmare = require('nightmare');
    var nightmare = Nightmare({ show: true });

    co(function *() {
        var result;
        result = yield nightmare
            .goto('https://github.com')
            .evaluate(function(){
                return 'a';
             });

         console.log(result); // a

         result = yield nightmare
            .goto('https://github.com')
            .evaluate(function() {
                return 'b';
             });

         console.log(result); // b
    });
    
    console.log('c');
    
    // 输出结果: c b a


