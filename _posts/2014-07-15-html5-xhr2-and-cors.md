---
layout: post
title: "html5  XMLHttpRequest Level2 and CORS"
description: ""
category: web
tags: [ html5 ]
---
{% include JB/setup %}

由于同源限制(same-origin policy)，以前XMLHttpRequest(XHR) 只限于同源通讯，
不能进行跨源资源共享(Cross-Origin Resource Sharing,CORS) 。
由于HTML 5的概念形成，在原有XHR的基础上提出了XMLHttpRequest Level2(XHR2 )，在XHR2中对CORS有了很好的支持。
当前浏览器对XHR2支持情况,[点击查看](http://caniuse.com/xhr2)
在IE中，引入了XDomainRequest(XDR)类型对CORS进行支持，由此实现跨域通讯。
在使用XHR2之前要进行检测，检查浏览器是否支持XHR2。

<!-- more -->

#####Cross-Origin Resource Sharing#####

> The basic idea behind CORS is to use custom HTTP headers to allow both the browser and 
> the server to know enough about each other to determine 
> if the request or response should succeed or fail.
> For a simple request,
> one that uses either GET or POST with no custom headers and whose body is text/plain, 
> the request is sent with an extra header called Origin. The Origin header contains the origin (protocol, domain name, and port) of the requesting page so that the server can easily determine whether or not it should serve a response.

> The Cross-Origin Resource Sharing standard works by adding new HTTP headers that
>  allow servers to describe the set of origins that are permitted to read that 
>  information using a web browser.
>  Additionally, for HTTP request methods that can cause side-effects on user data 
>  (in particular, for HTTP methods other than GET, or for POST usage with certain MIME types),
>  the specification mandates that browsers "preflight" the request, 
>  soliciting supported methods from the server with an HTTPOPTIONS request header, 
>  and then, upon "approval" from the server, sending the actual request with the actual HTTP request method.  Servers can also notify clients whether "credentials" (including Cookies and HTTP Authentication data) should be sent with requests. 


#####简单实现#####

服务器需要设置，具体设置方法[点击查看](http://enable-cors.org/server.html )

在IIS 7.0服务器实现，需要在Web.config 中加入如下代码：

	<?xml version="1.0" encoding="utf-8"?>
	<configuration>
	 <system.webServer>
	   <httpProtocol>
		 <customHeaders>
		   <add name="Access-Control-Allow-Origin" value="*" />
		 </customHeaders>
	   </httpProtocol>
	 </system.webServer>
	</configuration>

在http://post.com:8080中加入一个source.html文件，其中代码只有一个字符串。
在http://receive.com:8081中加入get.html,代码如下：

	<html>
	<head>
	<script type="text/javascript">
	function getString(){
		   var request=createCORSRequest("GET","http://post.com:8080/source.html");
		if(request){
			request.onload=function(){
				alert(request.responseText);
			}
			request.onerror=function(e){
				alert("error");
			}
			request.send();
		}
	}
	function createCORSRequest(method,url){
		var xhr=new XMLHttpRequest();
		if("withCredentials" in xhr){
			xhr.open(method,url,true);
		}else if(typeof XDomainRequest!="undefined"){
			xhr=new XDomainRequest();	
			xhr.open(method,url);
		}else{
			xhr=null;
		}
		return xhr;
	}
	window.onload=getString();
	//window.addEventListener("load",getString,true);
	</script>
	</head>
	<body>
	</body>
	</html>
	
如果服务器不支持XHR2将进入onerror执行。
这时候xmlhttprequest 的status则很可能为 0.关于HTTP头部响应,服务器不支持：
 
![]({{ site.url }}/assets/images/web/html5/2014071507.png)

服务器支持：
 
 ![]({{ site.url }}/assets/images/web/html5/2014071508.png)
 
对比发现差别在响应头信息中的Access-Control-Allow-Origin的有无，
Access-Control-Allow-Origin是在服务器中设置允许访问获得信息的源，
如果任意源都可以获取则Access-Control-Allow-Origin:\*。
