---
layout: post
title: "Generators 总结"
description: ""
category: [web]
tags: [ES6, Generators, co]
---
{% include JB/setup %}

## 什么是 Generators
Generators 是一种能够暂停并且能够重新进入的函数。Generators 函数被调用时，并不会立即执行它的函数体，而是返回一个Iterator对象。每次调用Iterator对象的next方法，就可以使函数执行下去，直到遇见return语句或者 yield 语句。在 Generators 函数中 return 和 yield 都能返回数据；两者的区别在于 return 退出了函数，而 yield 只是暂停执行并且使用next 可继续执行函数。

    function *sayHello() {
        console.log('start say hello ...');
        var count = 0;
        while (name = yield count++) {
            console.log('hello ' + name);
        }
        console.log('end say hello ...');
        return count;
    }

    var it = sayHello('Jim');

    var count = it.next();       // start say hello ...
    console.log(count);          // { value: 0, done: false }

    var count = it.next('Tom');  // hello Tom
    console.log(count);          // { value: 1, done: false }

    var count = it.next('Kate'); // hello Kate
    console.log(count);          // { value: 2, done: false }

    var count = it.next();       // end say hello ...
    console.log(count);          // { value: 3, done: true }

执行 next 方法会返回一个对象，该对象有两个属性 value 和 done，value 表示返回值，done  表示遍历的状态，如果 done 为 true 则表示函数执行结束。而 yield 可以接收 next 方法携带的参数，并将这些数据提供给 Generators 函数使用。因此，Generators 函数既可以生产数据，又可以消费数据。它的两个重要应用是：1. 实现迭代器，2.函数异步调用。

> Generators可以作为三种角色： 当Generators函数作为生产者生产数据时，实际上就是一个迭代器；当Generators函数作为消费者消费数据时，可以将其视为Observers；当Generators函数既是生产者又是消费者时，是一个coroutines。 ---[2ality](http://www.2ality.com/2015/03/es6-generators.html)

##Generators 与 Iterators

In computer programming, an iterator is an object that enables a programmer to traverse a container.
-- Wikipedia
ES6 提供了 Iterator 接口为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署Iterator接口，就可以使用for of完成遍历操作，Iterator 接口的定义如下：

    interface IteratorResult {
        done: boolean;
        value: any;
    }

    interface Iterator {
        next(): IteratorResult;
    }

    interface Iterable {
        [Symbol.iterator](): Iterator
    }

根据上面的接口可以实现一个简单的 Iterator：

    var fruits = {
        [Symbol.iterator]() {
            var fruits_data = ['apple', 'banana', 'pear'];
            var index = 0;
            return {
                next() {
                    var fruit;
                    if (index < fruits_data.length) {
                        fruit = fruits_data[index ++];
                        return {value: fruit, done: false}
                    } else {
                        return {value: fruit, done: true};
                    }
                }
            }
        }
    }

    for (var fruit of fruits) {
        console.log(fruit);
    }

如果使用 Generator 函数实现一个Iterator则比较简单：

    function *fruits() {
        yield 'apple';
        yield 'banana';
        yield 'pear';
    }

    for (var fruit of fruits()) {
        console.log(fruit);
    }

##Generators 与 Asynchronous

JavaScript在大部分情况下是单线程运行的，为了避免一些比较耗时的操作（例如：HTTP请求、数据库读取等）将线程阻塞，通常是以异步的方式处理这些操作。例如实现这样的一个功能：首先打开 a.txt 文件，如果失败则打开 b.txt 文件，代码如下：

    var fs = require('fs');

    function openFile() {
        fs.open('a.txt', 'r+', function(err, fd) {
            if(err) {
                fs.open('b.txt', 'r+', function(err, fd) {
                    if (err) {
                        console.log('failed');
                    }
                });
            }
       });
    }

    openFile();

如果进一步打开 c.txt、d.txt 等更多的文件，上面代码中的回调函数会嵌套得越来越深（Callback Hell），这样会使代码的维护变得困难。Generators 的出现为我们解决异步问题提供了另外一种思路：以同步的方式写异步代码。其主要原理就是，Generators 的暂停机制可以阻塞异步调用，并且在未来恰当的时机可将其唤醒完成剩余的工作。把上面的代码改写为下面这个样子，能够实现同样的功能：

    var fs = require('fs');

    function *openFile() {
        var err = yield fs.open('a.txt', 'r+', function(err, fd) {
            it.next(err);  // A
        });
        if (err) {
            err = yield fs.open('b.txt', 'r+', function(err, fd) {
                it.next(err); // B
            });
        }
        if (err) {
            console.log ('failed');
        }
    }

    var it = openFile();
    it.next();  // C

    // a.txt 和 b.txt 都不存在，输出结果如下：
    open a.txt failed
    open b.txt failed
    failed

在上面代码中，A、B、C 三处调用的是同一个 it 变量，在整个函数运行过程中， it 调用next是确保整个流程一步步走下去的关键，但是在 openFile 函数中这么调用外部变量 it 是不推荐的，因此需要换一种方式来进行流程控制。TJ 是这样描述 Generators 与回调函数之间的关系：

> First off generators are complementary to callbacks, some form of callback is required to “feed” the generators. These “futures”, “thunks”, or “promises” — whatever you prefer to call them allow deferred execution of some logic, this is what you yield a value and allow the generator to handle the rest.
> Once one of these values is yielded to the caller, the caller waits around for the callback and then resumes the generator. Depending on how you look at this, generators used in this way are effectively the same mechanism as a callback, however with some added benefits that we’ll look at soon.   — TJ [callbacks vs coroutines](https://medium.com/@tjholowaychuk/callbacks-vs-coroutines-174f1fe66127#.f7wnfhbjc)

仅仅依靠 Generators 是无法解决异步问题的，正如TJ所描述的那样，Generators  需要与  “thunks” 或 “promises” 配合，由 “thunks” 或 “promises” 控制 Generators 的暂停或执行，便可以实现回调函数调用的效果。将他们结合到一起，并且能够使 Generators 自动执行下去，这就是 co 所要解决的问题。

## Generators 与  co

co 是 TJ 写的一个用于流程控制的工具，使用 co 可以实现以同步的方式去写异步代码，并且能够很好的进行错误处理。

> Generator based control flow goodness for nodejs and the browser, using promises, letting you write non-blocking code in a nice-ish way.

将上面的代码用 co 改写如下：

    var fs = require('fs');
    var co = require('co');

    function openFile(filename) {
        return function (fn) {  // A
            fs.open(filename, 'r+', function(err, fd) {
                if (err) {
                    console.log('open ' + filename + ' failed');
                }
                return fn(null, err); // B
            });
        }
    }

    co(function *() {
        var err = yield openFile('a.txt');
        if (err) {
            err = yield openFile('b.txt');
        }
        if (err) {
            console.log('failed');
        }
    }).then(function() {});

    // a.txt 和 b.txt 都不存在，输出结果如下：
    open a.txt failed
    open b.txt failed
    failed

openFile 是一个 trunk 函数，执行 openFile 会得到一个函数，而这个函数是的参数是一个回调函数，也就是说 A 处的 fn 是一个回调函数。co 接收的是一个 Generators 函数，它会为 yield 之后的 openFile 提供一个 回调函数，就是 A 处的 fn。fn 的作用是在异步操作完成之后，调用对应 generator的next函数并且提供返回值，这样就可以使整个流程不断的执行下去 。目前co支持 trunk 和 Promise ，但是未来 co 可能不再支持 trunk 函数，将其上面的代码重写为 Promise 形式，如下：

    var fs = require('fs');
    var co = require('co');

    function openFile(filename) {
        return new Promise(function (resolve, reject) {
            fs.open(filename, 'r+', function(err, fd){
                if (err) {
                    console.log('open ' + filename + ' failed');
                    resolve(err);
                }
            });
        });
    }

    co(function *() {
        var err = yield openFile('a.txt');
        if (err) {
            err = yield openFile('b.txt');
        }
        if (err) {
            console.log('failed');
        }
    }).then(function() {});

## Generators 与 async
async 函数是 Generators 函数的语法糖，使异步操作更加简单，与  Generators 相比它的好处在于以下几点：

- 内置执行器，就是说调用 async 函数时，函数会自动执行，不再需要 co 模块
- 使用关键字 async 和 await，async 表示函数里面有异步操作，await 表示要等待表达式得到最后的结果
- 返回值为 Promise ，而不是 Iterator  


用 async 重写上面的代码：

    var fs = require('fs');
    var co = require('co');

    function openFile(filename) {
        return new Promise(function (resolve, reject) {
            fs.open(filename, 'r+', function(err, fd){
                if (err) {
                    console.log('open ' + filename + ' failed');
                    resolve(err);
                }
            });
        });
    }

    (async () => {
        var err = await openFile('a.txt');
        if (err) {
            err = await openFile('b.txt');
        }
        if (err) {
            console.log('failed');
        }
    })();

由于 async 是 ES7 的提案，目前 node 还不能完全支持，因此需要使用 babel  ：

    # 安装babel
    $ npm install --global babel

    #运行支持ES6的REPL环境
    $ babel-node

    #运行ES6脚本
    $ babel-node yourfilename.js


## 相关资料
- [es6 generators](http://www.2ality.com/2015/03/es6-generators.html)  
- [callbacks vs coroutines](https://medium.com/@tjholowaychuk/callbacks-vs-coroutines-174f1fe66127#.f7wnfhbjc)  

