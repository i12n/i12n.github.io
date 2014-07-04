---
layout: post
title: "CM Labs Vortex 6.0 ICD 接口的使用"
description: ""
category: VR
tags: [ Vortex , VegaPrime ]
---
{% include JB/setup %}
ICD(Interface Control Documents) 提供了Vortex与其他系统交互的接口，ICD 主要由 producers 和 consumers 构成。
producers 将仿真中parts 的位姿等更新信息提供给consumers；consumers接收producers 发送过来的数据信息并做出响应。

<!-- more -->
<img  src="{{ site.url }}/assets/images/vr/2014070101.png" />
VxMappingPartIDMap：VxMappingPartIDMap记录producers关注的parts；map() 为part分配一个id与其对应；mMapping记录id 与part的对应关系

VxProducerPart：registerConsumer()用于注册 consumer；postUpdateImpl()把VxMappingPartIDMap中 parts的更新通知给consumer

VxConsumerPartPrinter:对事件做出响应，每当producers的postUpdateImpl() 发生时，consumer的partUpdated() 被调用，将parts的位姿等信息打印输出。


	Vx::VxUniverse *universe=vpVxUniverse::find("myVxUniverse")->getUniverse();
	Vx::ICD::VxMappingPartIDMap *partmap= new Vx::ICD::VxMappingPartIDMap();
	Vx::VxPart *box=Vx::Find::part("box");
	//为 part 生成唯一id，记录 part 与 id 的一一对应关系
	unsigned int id= partmap ->map(box);
	//初始化 producer
	Vx::ICD::VxProducerPart producer = new Vx::ICD::VxProducerPart(universe, partmap);
	//初始化 consumer
	Vx::ICD::VxConsumerPartPrinter* consumer = new Vx::ICD::VxConsumerPartPrinter();
	//注册consumer
	producer->registerConsumer(consumer);

	//发送更新
	producer->postUpdateImpl();
