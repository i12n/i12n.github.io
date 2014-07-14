---
layout: post
title: "在 Ubuntu 12.04 LTS 上安装Docker"
description: ""
category: Docker
tags: [ Docker ]
---
{% include JB/setup %}

Docker 支持 Ubuntu 64 操作系统，可以在 Ubuntu 12.04(LTS)(64-bit) 上安装Docker。
由于LXC的存在的一个bug，Linux 3.8以上版本内核对Docker的支持会更好。
Docker Engine 包含一个两个部分，服务进程可以个客户进程。
前者是个守护进程，用来管理所有容器；后者用于用户和服务进程交互。


<!-- more -->

1. 对内核升级

		# install the backported kernel
		$ sudo apt-get update
		$ sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raring
		# reboot
		$ sudo reboot

2. 添加Docker repository key

		$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9

3. 添加 Docker repository 到 apt sources list, 安装 lxc-docker 

		$ sudo sh -c "echo deb https://get.docker.io/ubuntu docker main\
		> /etc/apt/sources.list.d/docker.list"
		$ sudo apt-get update
		$ sudo apt-get install lxc-docker


4. 在 host 上安装并运行一个 image
    
	可以把容器看作是一个在盒子里面的进程，盒子里面装着进程所需要的一切，例如文件系统、系统类库以及shell等。
	默认状况下它们都处于非运行状态，Docker 通过运行容器里面的一个进程来启动容器。
	`docker run imagename command` 用于在容器中执行一个应用，image是 要运行的容器的源，command是要该image去执行的命令。
	`docker run` 先查看 Docker host 上有没有这个 image ，如果没有，则会从 Docker Hub 下载到 host 。
	
		#download and run ubuntu image
		$ sudo docker run -i -t ubuntu /bin/bash
	
	以上命令先安装一个容器，生成一个ubuntu环境，然后启动它的bash