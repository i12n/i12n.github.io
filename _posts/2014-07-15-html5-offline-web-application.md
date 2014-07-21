---
layout: post
title: "html5离线web应用"
description: ""
category: web
tags: [ html5 ]
---
{% include JB/setup %}

HTML5 缓存控制机制综合了web应用和桌面应用两者的优势，
使基于web构建的web应用程序可在浏览器中运行并同步更新。
而HTML5的离线应用缓存使得在无网络连接的状态下运行应用程序成为可能。
开发者通过为浏览器指定缓存应用资源，来支持应用在离线状态下可用。

<!-- more -->

#####配置.manifest文件#####

.manifest 文件用来配置离线应用缓存的资源清单。

* IIS7.5 配置MIME类型  
  .manifest 的MIME类型是text/cache-manifest。
  在IIS 7.5中manifest 文件的MIME类型默认是application/x-ms-manifest。
  application/x-ms-manifest 在chrome中能够支持，但是firefox不支持，所以还是要改成text/cache-manifest。
  可以在IIS的功能视图的MIME类型中进行查看和配置。

* manifest编写方法  
关于manifest 文件编写方法和使用方法可以参考：  
 [*Using the Application Cache*](https://developer.mozilla.org/en-US/docs/HTML/Using_the_application_cache) *- MDN*  
 [*A Beginner's Guide to Using the Application Cache*](http://www.html5rocks.com/en/tutorials/appcache/beginner/) *-html5rocks*  
 检测.manfest是否存在错误：[点击进入](http://manifest-validator.com/)  

 
* 注意事项  
1.对离线加载的资源更新，就要对manifest 文件进行更改，一般都是加一个版本号的方法控制。  
2.NETWORK 和FALLBACK之后要有冒号。  
3.在IIS7.5中发现一个config文件：
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		    <system.webServer>
		        <directoryBrowse enabled="false" />
		        <staticContent>
		            <remove fileExtension=".manifest" />
		            <mimeMap fileExtension=".manifest" mimeType="text/cache-manifest" />
		        </staticContent>
		    </system.webServer>
		</configuration>  
4.在html中配置.manifest文件
	
		<!DOCTYPE html>
		<html lang="en" manifest="offline.manifest">




#####applicationCache API#####

applicationCache API：操作应用缓存的接口。
只有对资源进行存储，才能在离线之后依然能够进行操作应用。

#####其他#####

* 浏览器进行离线下载事件过程：  
1.如果浏览器访问<\html\>元素带有manifest属性，首先触发window.applicationCache对象中的checking事件。  
2.如果浏览器从来没有访问过该缓存清单，则触发downloading事件然后下载缓存清单中的资源。在下载资源过程中阶段性触发process事件，通过该事件可以获取下载资源的过程。所以资源下载完毕后触发cached事件，表示资源下载完毕。  
3.如果之前访问过该缓存清单，该缓存清单在上次访问之后未发生变化，则触发noupdate事件；如果该缓存清单发生变动则触发downloading事件，并将资源重新下载，在下载资源过程中阶段性触发process事件，重新下载完毕后下载updateready。  
4.如果发生错误则触发error事件。

* 关于下载资源的触发：  
1.浏览器通过普通的HTTP语义来检测缓存清单文件是否过期。Web服务器会在响应头中包含面熟文件过期时间的信息，这样浏览器就可以通过头信息(Expires和Cache-Control)得知该文件可以被缓存多久，什么时候过期，这样就不必询问服务器。这种方式的缓存并不是专门为离线Web应用设计，所有的HTML页面，图片，样式文件等其他web资源都会用这种方式。  
2.如何缓存文件过期，那么浏览器询问服务器是否有新版本。浏览器向服务器发送一个HTTP请求，该请求头包含缓存清单最后一次修改的时间。服务器获得这个修改时间之后和最新的缓存文件的修改时间进行对比，计算出缓存清单文件是否修改。如果未发生变动，返回304。这个过程并不是专门为离线Web应用设计。  
3.如果发生变动，返回200，同时将新文件内容以及新的Cache-Control和最后修改时间的HTTP头响应信息返回给浏览器。缓存清单下载完毕之后，浏览器要读取其中的内容和原版本进行对比，如果没有变化就不会下载缓存清单中的内容。  

 触发更新离线资源的重要因素：缓存清单缓存时间过期，缓存清单文件修改

#####资料#####

[*Offline Web applications*](http://www.w3.org/TR/2011/WD-html5-20110525/offline.html)*-W3C*  
[*使用 HTML5 开发离线应用*](http://www.ibm.com/developerworks/cn/web/1011_guozb_html5off/)  
