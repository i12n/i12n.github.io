---
layout: post
title: "html5地理位置API"
description: ""
category: web
tags: [ html5 ]
---
{% include JB/setup %}

Geolocation API 允许用户在web应用中共享他们的位置信息。
位置信息是通过支持html5地理定位功能的底层设备提供给浏览器的。
Geolocation API 不指定设备用哪种底层技术来定位，也不保证设备返回的实际位置是精确的。
设备可以使用ip地址，GPS，从RFID、WIFI、和蓝牙到WIFI的MAC地址，GSM/CDMA手机的ID作为位置信息来源。
应用程序不能直接访问设备，它只能请求浏览器来代表它访问设备。
Html5 Geolocation 规范了一套保护用户隐私的机制，除非用户明确许可，否则不可以获取位置信息。

<!-- more -->

> The Geolocation API defines a high-level interface to location information associated only with the device 
> hosting the implementation, such as latitude and longitude. 
> The API itself is agnostic of the underlying location information sources. 
> Common sources of location information include Global Positioning System (GPS) and 
> location inferred from network signals such as IP address, RFID, WiFi and Bluetooth MAC addresses, 
> and GSM/CDMA cell IDs, as well as user input. 
> No guarantee is given that the API returns the device's actual location.   
> [ *Geolocation API Specification.* ](http://dev.w3.org/geo/api/spec-source.html) *W3C*
 
#####浏览器支持#####

> Firefox 3.5 and later support the Geolocation API; 
> this allows the user to provide their location to web applications if they so desire. 
> For privacy reasons, the user is asked to confirm permission to report location information.  
> [ *Using Geolocation.* ](https://developer.mozilla.org/en-US/docs/Using_geolocation ) *MDN*


#####使用方法#####

1.浏览器支持检测

	if(navigator.geolocation){
	    //支持，做一些事情
	}else{
	    //不支持
	}
	
或者

	if("geolocation" in navigator) {
	  /* geolocation is available */
	}else{
	  alert("I'm sorry, but geolocation services are not supported by your browser.");
	}

2.位置请求

* 单次位置请求

navigator.geolocation.getCurrentPosition(geo_success,geo_error,options)
geo_success:函数，接收一个位置对象，用来处理位置信息
geo_error：函数，接收错误对象，当出错的时候进行处理
Options：可选的地理定位情怯特性


* 重复位置请求

navigator.geolocation.watchPosition(geo_success,geo_error, options);
参数与getCurrentPosition()相同

3.位置对象

Coordinates为位置对象，属性如下：latitude，longitude，accuracy等

4.错误对象

PositionError为错误对象。

#####例子#####

用Google地图标记我所在的位置。

	<!DOCTYPE html>
	<html>
	  <head>
	    <meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
	    <style type="text/css">
	      html { height: 100% }
	      body { height: 100%; margin: 0; padding: 0 }
	      #map_canvas { height:500px;width:500px }
	    </style>
	    <script type="text/javascript"
	      src="http://maps.googleapis.com/maps/api/js?sensor=true">
	    </script>
	    <script type="text/javascript">
	        function initialize(){
	            //调用google地图
	            var mapOptions = {
	                center: new google.maps.LatLng( 39.92,116.42),
	                zoom:3,
	                mapTypeId: google.maps.MapTypeId.ROADMAP
	            };
	            var map = new google.maps.Map(document.getElementById("map_canvas"),
	                mapOptions);
	            //调用Geolocation
	            if(navigator.geolocation){//检测浏览器是否支持Geolocation
	               var f=getLocation(map);
	               //每60s重新获取地理位置信息
	               navigator.geolocation.watchPosition(f,showError,{maximumAge:60000});
	            }
	            else{
	                alert("The browser can't support html5 geolocation");
	        };
	      };
	      //获取位置对象，闭包
	      function getLocation(map){
	        return function (map){
	            return function(position){
	                //position为实际的位置对象，由watchPosition传递过来的
	                if(position.coords.accuracy>=500) alert("The position isn't accurate");
	                else showPosition(position,map);
	            }
	        }(map);
	      };
	      //显示错误消息，出错处理
	      function showError(error){
	        switch(error.code){
	            case 0:
	                alert("There was a error while retrieving your location:"+error.message);
	                break;
	            case 1:
	                alert("The user prevented retrieving your location.");
	                break;
	            case 2:
	                alert("The browser was unable to determine your location:"+error.message);
	                break;
	            case 3:
	                alert("The browser timed out before retrieving the location.");
	                break;
	        }
	      };
	      //在地图中标注当前位置
	      function showPosition(position,map) {
	        var myLatlng=new google.maps.LatLng(position.coords.latitude, position.coords.longitude);
	        var marker = new google.maps.Marker({
	            position: myLatlng,
	            map: map,
	            title:"I am here!"
	        });
	        map.setCenter(myLatlng);
	    };
		 
	    </script>
	  </head>
	  <body onload="initialize()">
		<div id="map_canvas"></div>
	  </body>
	</html>

#####结果演示#####

* 浏览器的用户隐私机制
 
![]({{ site.url }}/assets/images/web/html5/2014071501.png)

* 运行结果：
 
![]({{ site.url }}/assets/images/web/html5/2014071502.png)

该例子不能在台式机上运行。由于近期 Google Map API 不能加载的原因，导致不能正常运行。
