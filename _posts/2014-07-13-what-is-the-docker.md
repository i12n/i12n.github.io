---
layout: post
title: "Docker 简单介绍"
description: ""
category: Docker
tags: [ Docker ]
---
{% include JB/setup %}

Docker是一个开源的平台，让开发者可以打包其应用以及依赖包到一个可移植的容器中，然后发布到任意Linux发行版，也可实现虚拟化。

<!-- more -->

1. Docker 平台

	PLATFORM= Docker Engine + Docker Hub +APIs + Ecosystem

	<img  src="{{ site.url }}/assets/images/docker/2014071301.gif" />

	Docker Engine是一个轻量级的运行时和打包工具，Docker Hub是一个共享应用程序的云服务。


2. Docker 与 LXC 关系

	LXC 是一个操作系统级的虚拟化技术，它允许在一个host上创建和运行多个隔离的Linux虚拟环境（virtual environment，VE）。
	这些隔离出的层级可被称为容器，既可以用来隔离具体的沙箱应用程序也可以用来生成一个新的host。
	Linux 内核使用cgroups实现了CPU 、内存、 网络等资源的隔离；使用namespace 完成了对进程、文件系统等应用执行环境的隔离。
	LXC将cgroups和namespace结合在一起，为应用提供一个隔离的环境。

	Docker 并不能实现容器，Docker的目的是对容器进行管理。
	起初，Docker是构建在LXC之上的轻量级VM解决方案，随着 Docker 的发展，它不再只局限于LXC。
	从 Docker 0.9版本之后，Docker 放弃了LXC 作为默认的执行环境，而是使用libcontainer 替代了LXC。

	<img  src="{{ site.url }}/assets/images/docker/2014071302.png" />

3. Docker 与 VM的区别

	Docker 和传统的VM相比,最大的区别是它不包含操作系统内核，直接在Host上运行应用程序，更加便捷和高效。
	在VM中的一个应用，不仅仅包含应用本身所需要的类库等内容，还要包含一个Guest OS。
	在Docker中的一个容器只包含应用和他的依赖项，在host操作系统上对进程进行隔离，在容器之间共享Host OS。

	<img  src="{{ site.url }}/assets/images/docker/2014071303.png" />

4. 资料
	
	[ what is docker and when to use it ](http://www.centurylinklabs.com/what-is-docker-and-when-to-use-it/)
	
	[ Docker vs LXC ](http://www.scriptrock.com/articles/docker-vs-lxc)
	
	[ Docker 中文社区 ](https://www.dockboard.org/)
	
	[ Docker 版本信息 ](http://blog.docker.com/category/docker-releases/)