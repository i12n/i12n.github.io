---
layout: post
title: "DOM Error events"
description: ""
category: web
tags: [JavaScript]
---
{% include JB/setup %}

当 Javascript 发生运行时错误，则会触发 window.onerror() 事件；当资源加载失败时，也会触发 onerror() 事件。但是这两者存在一些区别，以下将进行介绍。

<!-- more -->

##1. window.onerror
window.onerror 可以捕捉到 Javascript 运行时出现的错误，也包括语法错误。window.onerror 回调函数的参数，如下所示：

- Error message，错误消息
- URL，发生错误的文件的 URL
- Line number，错误行号
- Column number, 错误列号
- Error Object (object)  


下面的代码中由于出现未定义变量 x ,因此会触发 Error 事件。

    <html>
      <head>
        <script>
          window.onerror = function(msg, url, line, col, obj) {
            var error = {
              msg: msg, url: url, line: line, col: col
            };
            console.log(error);
          }

          x; // 错误代码

        </script>
      </head>
    </html>


##2. element.onerror
当元素加载资源失败时（例如 img 元素加载图片失败），元素会触发 onerror 事件, 即 element.onerror。

    <html>
      <body>
        <img src="xxx.jpeg" onerror="console.log('Image load fail!')";>
      </body>
    </html>

这里要注意一点，如果在 JavaScript 代码中来捕获这个事件可能会有问题：

    <body>
      <img src="xxx.jpeg" id="test"/>
      <script>
        var element = document.getElementById('test');
        element.onerror = function() {
          console.log('Image load fail!');
        }
      </script>
    </body>

因为会存在这种现象，img 元素已经触发了 Error 事件，但是下面的JS代码还未被执行。如果把下面的JS代码放在 img 元素之前，也会有问题，即元素还没有加载到 DOM 。

那么能不能使用事件委托，用 window.onerror 来捕获 element.onerror 呢？运行下面的代码，当图片加载失败时，却不会触发 window.onerror事件。

    <head>
      <script>
        window.onerror = function() {
          console.log('Error!!');
        }
      </script>
    </head>
    <body>
      <img src="xxx.jpeg" id="test"/>
    </body>

这是因为，element.onerror 对应的事件类型为 Event 且其 bubbles 属性为 false。这就是说，元素的 error 事件不能被冒泡，而 window.onerror 是在冒泡阶段捕获事件的。 因此，window.onerror 是不能捕获到元素发生的 error 事件。

> All handlers work on bubbling stage excepts addEventListener with last argument true, which is the only way to catch the event on capturing stage.
> [Bubbling and capturing](http://javascript.info/tutorial/bubbling-and-capturing)

##3.Error 事件捕获
为了能够在对 Error 事件进行委托，可以用 addEventListener 来处理事件，并在捕获阶段调用事件处理函数。

    <head>
      <script>
        window.addEventListener('error', function(e){
          console.log(e);
        }, true);
      </script>
    </head>
    <body>
      <img src="xxx.jpeg" id="test"/>
    </body>

    /* 输出结果*/
    e: Event
      bubbles: false
      cancelBubble: false
      cancelable: false
      currentTarget: Window
      target: img#test
      type: "error"

这样就可以在通过 window 来捕获 Error 事件，并且进行统一的处理，并且不必担心之前 element.onerror 出现的问题。

除此之外，上面的代码也能捕获到由于 JavaScript 代码运行时错误而产生的 error 事件，与 window.onerror 有着相同的效果。

    <head>
      <script>
        window.addEventListener('error', function(e){
          console.log(e);
        }, true);

        x; // 错误代码
      </script>
    </head>

    /*输出结果*/
    e: ErrorEvent
      bubbles: false
      cancelBubble: false
      cancelable: true
      colno: 1
      currentTarget: Window
      defaultPrevented: false
      error: ReferenceError: x is not defined at example.html:8:1
      eventPhase: 2
      filename: "example.html"
      lineno: 8
      message: "Uncaught ReferenceError: x is not defined"
      target: Window
      type: "error"

很明显，与元素触发的 Event 事件类型不同，window 触发的是 ErrorEvent 事件。

##4.Error Event的用途

Error Event 可以捕获到代码运行时错误，也能捕获到资源加载失败，因此完全可以用来监控 JavaScript 代码的运行错误以及资源加载是否异常。可以参考 [前端代码异常监控](http://div.io/topic/743) 。

##5.相关

[Dom Events](http://www.smashingmagazine.com/2013/11/an-introduction-to-dom-events/#error)  
[How to Detect 404 Errors Using JavaScript](http://stackoverflow.com/questions/8504673/how-to-detect-on-page-404-errors-using-javascript)  
[How to catch JavaScript Errors with window.onerror](https://danlimerick.wordpress.com/2014/01/18/how-to-catch-javascript-errors-with-window-onerror-even-on-chrome-and-firefox/)  
[GlobalEventHandlers.onerror](https://developer.mozilla.org/en/docs/Web/API/GlobalEventHandlers/onerror)
