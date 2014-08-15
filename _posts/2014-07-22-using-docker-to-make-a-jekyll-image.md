---
layout: post
title: "使用 docker 创建一个 jekyll image"
description: ""
category: Docker
tags: [ Docker , jekyll ]
---
{% include JB/setup %}

为 jekyll 制作一个简单的 image 。

<!-- more -->

1. 创建 Dockerfile

		FROM ubuntu:12.04
		MAINTAINER Ge <gewenmao@gmail.com>

		RUN apt-get update

		#install ruby, jekyll, rake, nodejs, git and get jekyll-bootstrap 
		RUN apt-get install -y ruby1.9.1  ruby1.9.1-dev make
		RUN gem install jekyll
		RUN gem install rake
		RUN apt-get install -y  nodejs
		RUN apt-get install -y git
		RUN git clone https://github.com/plusjade/jekyll-bootstrap.git /sites

		#Default port
		EXPOSE 4000

2. 生成 image

		$ sudo docker build -t ubuntu-jekyll .
		
3. 运行容器

	用容器中的 jekyll 编译生成主机上的站点

		#将host上位于 /home/yourusername/blog中的静态网页，启动
		#浏览器中 127.0.0.0:4000 可以查看生成结果
		$ sudo docker run -v /home/yourusername/blog:/sites --name BLOG_DATA busybox true 
		$ sudo docker run --volumes-from BLOG_DATA -d -p 4000:4000 --name jekyll --workdir /sites  ubuntu-jekyll jekyll server  

	用容器中的 jekyll 编译生成容器中的站点
	
		#编译启动 git 复制到本地的 jekyll-bootstrap 
		sudo docker run -d -p 4000:4000 --workdir /sites --name jekyll ubuntu-jekyll jekyll server 


	