---
layout: post
title: "html5 WebSockets API"
description: ""
category: web
tags: [html5]
---
{% include JB/setup %}

HTML5 WebSockets 是HTML5中最强大的通信功能，它定义了一个全双工的通信信道，仅通过一个Socket就可以进行通信。
与传统的Comet和Ajax  poling(轮询)、(长轮询)long poling以及流(streaming)解决方案相比，
减少了不必要的网络流量并降低了网络延迟，更好的支持了实时数据的通信。
这是因为，传统的解决方案实现实时数据的交互，都是在HTTP技术基础上模拟浏览器双向通信，
然而HTTP技术并不是为了实现实时全双工通信设计。

<!-- more -->

#####WebSockets API#####

WebSocket在通信中使用是另一种协议------WebSocket协议。
WebSocket协议包含两部分,握手(handshake )和数据传输(data transfer)。
在客户端和服务器之间建立WebSocket通信连接时首先进行握手，在这个过程中将HTTP协议升级到WebSocket协议。
握手成功则通信连接建立成功，客户端和服务器就可以在全双工模式下进行数据传输了。
数据传输是以 WebSocket 消息(message)传递的方式进行。

WebSockets API定义了一组API来实现网页通过WebSocket协议进行全双工通信。  
WebSockets API事件，建立连接时触发open，收到消息时触发message，关闭连接时触发close。  
WebSockets API定义了readyState用来描述连接的状态：  
　　CONNECTING (numeric value 0)：The connection has not yet been established.  
　　OPEN (numeric value 1)：The WebSocket connection is established and communication is possible.  
　　CLOSING (numeric value 2)：The connection is going through the closing handshake, or the close() method has been invoked.  
　　CLOSED (numeric value 3)：The connection has been closed or could not be opened.  
WebSockets API定义了send()方法用来发送消息，close()方法用来关闭连接。

#####例子##### 
HTML5 WebSockets要求浏览器和服务器都支持才能实现。

	<html>
	<head>
	<script type="text/javascript">
	function init(){
		var submit=document.getElementById("submit");
		var connect=document.getElementById("connect");
		submit.setAttribute("disabled","true");
		submit.addEventListener("click",subClick,false);
		connect.addEventListener("click",conClick,false);
	}
	window.addEventListener("load",init,false);
	function subClick(){
		var text=document.getElementById("sendText").value;
		if(text){
			websocket.send(text);
		}
	}
	function conClick(){
		var submit=document.getElementById("submit");
		if(this.value=="connect"){
			this.value="disconnect";
			if("WebSocket" in window){//浏览器支持检测
				var url = "ws://echo.websocket.org/";
				websocket=new WebSocket(url);
				submit.removeAttribute("disabled");
				websocket.addEventListener("open",wsOpen,false);
				websocket.addEventListener("message",wsMessage,false);
				websocket.addEventListener("close",wsClose,false);
			}else{
				showMessage("the brower can't support WebSocket!");
				this.value="disable";
				connect.setAttribute("disabled","true");
				submit.setAttribute("disabled","true");
			}
			
			
		}else{
			this.value="connect";
			websocket.close();
			alert(websocket.readyState);//2  closing handshake, or the close() method has been invoked
			submit.setAttribute("disabled","true");
		}
	}
	function wsOpen(){
		alert(websocket.readyState);//1 connection is established 
		showMessage("connection is established");
	}
	function wsMessage(e){
		showMessage("receive msg:"+e.data);
	}
	function wsClose(e){
		alert(websocket.readyState);//3  closed or could not be opened
		showMessage("connect closed or could not be opened");
	}
	function showMessage(msg){
		var p = document.createElement("p"); 
		var messages=document.getElementById("messages");
		p.innerHTML = msg;
		messages.appendChild(p); 
	}
	</script>
	</head>
	<body>
	<input id="connect" style="width:100px" type="button" value="connect"/>
	<input id="sendText"/>
	<input id="submit" type="button" value="submit"/>
	<div id="messages">
	</div>
	</body>
	</html>
	
#####参考资料#####
http://www.websocket.org/echo.html 
https://developer.mozilla.org/en-US/docs/tag/WebSockets  
http://www.ibm.com/developerworks/cn/web/1112_huangxa_websocket/  
http://dev.w3.org/html5/websockets/  
http://datatracker.ietf.org/doc/rfc6455/?include_text=1  
 
