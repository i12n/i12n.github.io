---
layout: post
title: "html5 window.postMessage"
description: ""
category: web 
tags: [ html5 ]
---
{% include JB/setup %}


  出于安全考虑，运行在同一浏览器中的框架、标签页、窗口间的通信都受到了严格的限制。
通常，只有在协议(规则)、端口、主机完全相同的时候，也就是同源(origin)文档才可以通信，
不同页面之间的脚本才能够相互访问。这个安全策略虽然防止恶意网站与其他内容交互，
但是也让制作有多个数据来源的聚合应用(mashup)变得很困难。

<!-- more -->

  为了满足这一需求，浏览器厂商和标准制定机构一直同意引入一种新功能：
跨文档消息通信(Cross Document Messaging)。
跨文档消息通信可以确保iframe、标签页、窗口间安全的进行跨源通信。
window.postMessage 使跨源通信成为一种安全的方法。
window.postMessage 通过安全可靠的方式实现了对这个限制的突破，很方便的实现了跨文档消息通信。
跨文档消息通信是构建实时(real-time)跨源(cross-origin)通信的两个重要模块之一(另一个是XMLHttpRequest Level 2)。

源(origin)的概念：由规则(scheme)、主机(host)、端口构成(port)组成。
由于规则不同(如https与http)，所以https://www.123.com与http://www.123.com的源是不同的。
而源不考虑路径，http://www.123.com/default.html与http://www.123.com/index.html 是同源的。
当然window.postMessage不仅仅可以在跨源(cross-origin)通信中使用，在同源通信中也可以使用。


> window.postMessage is a method for safely enabling cross-origin communication. 
> Normally, scripts on different pages are only allowed to access each other 
> if and only if the pages which executed them are at locations 
> with the same protocol (usually bothhttp), port number (80 being the default for http), 
> and host (modulo document.domain being set by both pages to the same value).
> window.postMessage provides a controlled mechanism to circumvent this restriction 
> in a way which is secure when properly used. 
> [*Window.postMessage*](https://developer.mozilla.org/en-US/docs/DOM/window.postMessage)*-MDN*


#####使用方法#####

1. 支持检测：版本比较旧的浏览器可能不支持window.postMessage，
	IE8以下的版本不支持。

		typeof(otherWindow .postMessage===undefined)
2. 发送消息：

		otherWindow .postMessage(message ,targetOrigin );
	调用某个otherWindow 对象的postMessage()时，
	目标域名和该otherWindow 的域名一致，那么document的message事件则会被触发。
	也就是说otherWindow 是要接收消息的目标，是otherWindow 自己给自己发消息，
	只不过这个语句是在该otherWindow 的外部调用。

	otherWindow :目标对象的window。  
	message:发送的消息。  
	targetOrigin:目标对象的源。


	> Note: Prior to Gecko 6.0 (Firefox 6.0 / Thunderbird 6.0 / SeaMonkey 2.3), 
	> the message parameter must be a string. 
	> Starting in Gecko 6.0 (Firefox 6.0 / Thunderbird 6.0 / SeaMonkey 2.3),
	> the message parameter is serialized using the structured clone algorithm. 
	> This means you can pass a broad variety of data objects safely to the destination window without having to serialize them yourself.  
	> [*Window.postMessage*](https://developer.mozilla.org/en-US/docs/DOM/window.postMessage)*-MDN*



3. 接收消息：用otherWindow 中的事件监听来监听消息。为该消息事件创建的Event对象包含以下属性：

	data:传递过来的消息，也就是postMessage函数中的第一个参数message。  
	origin:发送该消息的源，otherWindow .postMessage(message ,targetOrigin );所在window的源  
	source:发送该消息的窗口对象  

#####例子#####

定义两个不同的源分别用来发送消息和接收消息，部署在本地同一个服务器IIS中实现。
将发布的绑定地址为127.0.0.1，但是要把端口设置不一样。

1. http://post.com:8080 用来发送消息，发送的是一个时间秒数，每秒发送一次。
代码如下:

		<html>
		<head>
		<h2>PostMessage</h2>
		<p>http://post.com:8080</p>
		</head>
		<body>
		<input id="button" type="button" value="button" />
		<br />
		<script type="text/javascript">
		window.onload = function () {
			if (typeof window.postMessage === undefined) {
				alert("unavailibale");
			} else {
				var button = document.getElementById("button");
				setInterval(
				function () {
					var date = new Date();
					var s = date.getSeconds();
					button.value = s;
					window.top.postMessage(s, "http://receive.com:8081");
				}, 1000);
			}
		}
		</script>
		</body>
		</html>
	将以上代码发布到服务器，绑定ip为127.0.0.1 端口为8080  
2. http://receive.com:8081/用来接收传来的消息，其中要在iframe中包含http://post.com:8080，代码如下:

		<html>
		<head>
		<h2>ReceiveMessage</h2>
		</head>
		<body>
		    <p>http://receive.com:8081</p>
		    <iframe id="iframe" src="http://post.com:8080/"></iframe>
		    <br />
		    <input id="message" type="text" />
		<script type="text/javascript">
		    if (typeof window.postMessage === undefined) {
			    alert("unavailibale");
		    }
		    else{
		        function receiveMsg(e) {
		    	    if (e.origin == "http://post.com:8080") {
		    	        var massage = document.getElementById("message");
		    	        massage.value = e.data;
		            };
		        };
		        window.addEventListener("message",receiveMsg,true);
		    }
		</script>
		</body>
		</html>
		
	将以上代码发布到服务器，绑定ip为127.0.0.1 端口为8081
	
3. 最后，在C:\Windows\System32\drivers\etc找到hosts进行修改，加入如下两句  
	127.0.0.1 receive.com  
	127.0.0.1 post.com
	
4. 在浏览器中进入http://receive.com:8081/ 运行如下图所示。
	
	![]({{ site.url }}/assets/images/web/html5/2014071503.png)