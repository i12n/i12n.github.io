---
layout: post
title: "html5 Worker API"
description: ""
category: web 
tags: [ html5 ]
---
{% include JB/setup %}

Web Worker API 为 Web 应用程序的创作人员提供了一种方法，用于生成与主页并行运行的后台脚本。
JavaScript 属于单线程环境，也就是说无法同时运行多个脚本，
Web Worker可以一次生成多个线程以用于长时间运行的任务。
新的 worker 对象需要一个 .js 文件，该文件通过一个发给服务器的异步请求包含在内。
Web Worker API可以让Web应用程序具备后台处理能力。

<!-- more -->

#####Web Worker 的使用方法#####

1. 浏览器检测

		if (typeof (Worker)!="undefined"){
		    //支持
		}else{
		    //不支持
		}
		
2. 加载JS
	
	Web Worker初始化时要接受一个JavaScript文件的URI地址，其中包含了供worker执行的代码，
	但是要求JavaScript一定是同源才行，不能跨域加载JS。

		var   worker= new Worker("task.js");
	 
	由于worker内代码不能访问DOM，所以如果一个应用程序由多个JavaScript文件构成，
	则只能使用

		imortScripts("other1.js","other2.js");	
	这种方式加载和执行。
3. Web Worker中数据通信
	在Web Worker中要使用postMessage API进行数据通信,用message事件进行监听接收的信息，包括数据。
	在html页面中：
	
		var   worker= new Worker("task.js");
		worker.postMessage("helloworld!");//html页面向worker发送消息
		worker.onmessage=function(e){//e.data}//html页面监听worker发来的消息
		
	在task.js中：
		postMessage("helloworld!");//向页面发送消息
		onmessage=function(e){}//监听页面发送的信息

#####示例#####

echo.js

	onmessage=function(e){
		postMessage("echo:"+e.data);
	}
	
Html 代码

	<!DOCTYPE html>
	<html>
	<head>
	<script type="text/javascript">
	function init(){
	    if (typeof (Worker)!="undefined"){
		    var echo=new Worker("echo.js");
		    var btn=document.getElementById("button");
		    btn.onclick=function(){
			    var text=document.getElementById("text");
			    if(text.value) echo.postMessage(text.value);
		    }
		    echo.onmessage=function(e){showMessage(e.data);}
	    }else{
	        showMessage("browser doesn't support Worker");
	    }
	}
	function showMessage(msg){
	    var p=document.createElement("p");
	    var show=document.getElementById("show");
	    p.innerHTML=msg;
	    show.appendChild(p);
	}
	window.onload=init;
	</script>
	</head>
	<body>
	    <input type="text" id="text"/>
	    <input type="button" id="button" value="submit"/>
	    <div id="show"></div>
	</body>
	</html>

#####参考资料#####  
http://msdn.microsoft.com/zh-cn/library/ie/hh673568%28v=vs.85%29.aspx  
http://www.html5rocks.com/zh/tutorials/workers/basics/   
https://developer.mozilla.org/en-US/docs/DOM/Using_web_workers   
http://www.html5china.com/HTML5features/worker/20110926_2011.html  
