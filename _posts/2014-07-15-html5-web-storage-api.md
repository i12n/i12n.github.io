---
layout: post
title: "html5 Web Storage API"
description: ""
category: web 
tags: [ html5 ]
---
{% include JB/setup %}

在Web Storage API 出现之前，远程web服务器需要把客户端和服务器之间交互使用的所有相关数据都要进行存储。
随着Web Storage API 的出现，开发者可以将重复访问的数据直接储存在客户端的浏览器中，
还可以在关闭浏览器很久之后再次打开时恢复数据，以减小流量。

<!-- more -->

Cookie是一个在服务器和客户端之间来回传送文本值的内置机制，
服务器可以根据cookie中的数据在不同的web页面之间追踪用户信息。
Cookie可以将将本地个性化数据存储在应用程序当中，一边后续网页加载时使用。
Cookie存在的问题：cookie大小受限，大约4KB左右；
cookie要在服务器和浏览器之间传送，所以cookie数据在网络上可见而且会消耗网络带宽。

#####Web Storage API#####

1. 数据存储对象
	Web Storage 可以将数据存储在JavaScript对象中(sessionStorage或localStorage)，存储的数据不会在网络上传输，
	可以保存几MB的大数据。  
	sessionStorage：数据会保存到存储它的窗口或标签关闭的时候，数据只在构建他们的窗口或者标签页内可见。  
	sessionStore对象作用域范围为同源网页，页面重新加载时候数据不丢失，但是只限于同一个窗口或者标签页。
	正是由于数据只在构建他们的窗口或者标签页内可见这种作用域机制，有效的解决数据泄漏问题。    
	localStorage：数据的生命周期比窗口或浏览器的生命时间长，数据可被同源的每个窗口或者标签使用。  
	localStorage可以在浏览器关闭一段时间，数据不会丢失，而且不同窗口或着标签页之间共享数据。  
2. Web Storage API接口

		interface Storage {
		  readonly attribute unsigned long length;
		  DOMString? key(unsigned long index);
		  getter DOMString getItem(DOMString key);
		  setter creator void setItem(DOMString key, DOMString value);
		  deleter void removeItem(DOMString key);
		  void clear();
		};
		
	设置数据：
	
		window.sessionStorage.setItem(key,value); //key是键,value是值
		window.localStorage.setItem(key,value);
		window.sessionStorage.myKey=myValue;
		
	获取数据：
	
		var value=window.sessionStorage.getItem(key);
		var value=window.localStorage.getItem(key);
		var myValue=window.sessionStorage.myKey;
 
	Web Storage API 事件接口：
	
		interface StorageEvent : Event {
		  readonly attribute DOMString key;
		  readonly attribute DOMString? oldValue;
		  readonly attribute DOMString? newValue;
		  readonly attribute DOMString url;
		  readonly attribute Storage? storageArea;
		};
		window.addEventListener("storage",displayStorageEvent,true);
		
	在多窗口或标签页中，localStorage被操作之后触发storage事件。

	
#####简单示例#####

循环生成五个随机数存储到seesionStorage中，然后把最大的数字发送到"ws://echo.websocket.org/"，
然后收到回传的信息也是该数字，把这个数字再存储到localStorage中。

	<html>
	<head>
	<script type="text/javascript">
	function init(){
	    if(window.WebSocket&&window.sessionStorage&&window.localStorage){
	        var id=0;
	        uri="ws://echo.websocket.org/";
	        //无论向websocket发送数据，websocket都会将原数据回传。
	        websocket=new WebSocket(uri);
	        showLocalData();//显示localStorage中的数据
	        showSesstionData();//显示sessionStorage中的数据
	        setInterval(
	            function(){
	                setSessionStorage(id);//写入sessionStorage
	                id=(id+1)%5;
	                if(id==0){
	                    sendMessage(websocket);//向websocket服务端发送五个数中最大的数
	                    showSesstionData();//显示当前sessionStorage中的五个数字
	                }
	            },10000);
	        websocket.onopen=function(e)
	        {
	            showMessage("connect websocket success!");
	        };
	        websocket.onclose=function(){
	            showMessage("disconnect websocket success!");
	        };
	        websocket.onmessage=function(e){
	            showMessage("receive data from websocket:"+e.data);
	            //向localStorage写入websocket服务端回传的数字
	            setLocalStorage(e.data);
	            showLocalData();
	        };
	        //清除localStorage中的数据
	        document.getElementById("clear").onclick=function(){
	            window.localStorage.clear();
	            showLocalData();	
	        };
	        //修改localStorage中的键为0所对应的数据
	        document.getElementById("update").onclick=function(){
	            window.localStorage.setItem(0,-2);
	            showLocalData();	
	        };
	    }
	};
	//作为storage事件的回调函数，显示localStorage中数据
	function displayStorageEvent(e){
	    showLocalData();
	};
	window.addEventListener("storage",displayStorageEvent,true);

	//向websocket发送最大数
	function sendMessage(websocket){
	    var r=0;
	    for(var i=0;i<5;i++){
	        var data=parseInt(window.sessionStorage.getItem(i));
	        r=data>r?data:r;	
	    }
	    websocket.send(r);
	}
	//生成一个随机数字存入sessionStorage
	function setSessionStorage(id){
	    var r=parseInt(Math.random()*100);
	    window.sessionStorage.setItem(id,r);
	}
	//数字存入localStorage
	function setLocalStorage(value){
	    id=window.localStorage.length;
	    window.localStorage.setItem(id,value);
	}
	//显示websocekt相关信息
	function showMessage(msg){
	    var showMsg=document.getElementById("showMsg");
	    showMsg.innerHTML=msg;
	}
	//显示sessionStorage中数据
	function showSesstionData(){
	    var dataText=document.getElementById("showSesstionData");
	    var data="now data in sessionStorage:";
	    for(var i=0;i<5;i++)
	        data=data+window.sessionStorage.getItem(i)+" ";
	    dataText.innerHTML=data;
	}
	//显示localStorage中数据
	function showLocalData(){
	    var dataText=document.getElementById("showLocalData");
	    var data="now data in localStorage:";
	    length=window.localStorage.length;
	    for(var i=0;i<length;i++)data=data+window.localStorage.getItem(i)+" ";
	    dataText.innerHTML=data;
	}
	window.onload=init;

	</script>
	</head>
	<body>
	<div id="showMsg"></div>
	<div id="showSesstionData"></div>
	<div id="showLocalData"></div>
	<input type="button" id="clear" value="clear"/>
	<input type="button" id="update" value="update"/>
	</body>
	</html>

要将网页挂在服务器上，否则对storage事件有影响。当打开两个标签页时候，其中任何一个标签页在localStorage中添加修改或着删除新键值的时候，都会调用storage事件。 
运行结果：chrome浏览器
 

![]({{ site.url }}/assets/images/web/html5/2014071504.png)



localStorage：

![]({{ site.url }}/assets/images/web/html5/2014071505.png)

sessionStorage：
 ![]({{ site.url }}/assets/images/web/html5/2014071506.png)
#####参考资料#####

https://developer.mozilla.org/en-US/docs/DOM/Storage  
http://www.w3.org/TR/webstorage/  
http://www.html5rocks.com/en/features/storage  
http://www.cnblogs.com/amtf/archive/2012/02/19/2355693.html  


