---
layout: post
title: "CM Labs Vortex 6.0 VHL 接口的使用"
description: ""
category: VR
tags: [ Vortex , VegaPrime ]
---
{% include JB/setup %}

Vortex High Level (VHL) 简化了对mechanism 的参数进行输入输出的编码过程。在Vortex Editor 中设置好VHL接口，在程序中可以使用这些接口。

<!-- more -->

1. 在Vortex Editor中设置VHL 接口

	添加两个接口Interface.Inputs.setLinearVelocity 和 Interface.Inputs.Awake ，前者用于设置线速度，后者用于唤醒。
 
	<img  src="{{ site.url }}/assets/images/vr/2014061901.png" />


2. 在LnyX Prime中导入由Vortex Editor生成的acf文件

	导入成功之后，在Interface 中，可以看到两个VHL 接口，并且可以设置初始值等操作。
	
	<img  src="{{ site.url }}/assets/images/vr/2014061902.png" />

3. 在程序中，可以通过VHL接口对目标进行操作

		#include <vpVortex/vpVxPropertyVector3.h>
		#include <vpVortex/vpVxPropertyBoolean.h>
		//获取VHL接口
		vpVxPropertyVector3 *pvelocity=vpVxPropertyVector3::find("Interface.Inputs.setLinearVelocity");
		if( pvelocity ) {
		    //通过接口设置值
		    pvelocity->setValue(1,0,0);
		}
		
		//获取VHL接口
		vpVxPropertyBoolean *pwake=vpVxPropertyBoolean::find("Interface.Inputs.Awake");
		if(  pwake) {
		    //通过接口获取值
			bool f=pwake->getValue();
		    //通过接口设置值
			pwake->setValue(!f);
		}

