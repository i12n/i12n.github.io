---
layout: post
title: "CM Labs Vortex Editor 与 LynX Prime 结合"
description: ""
category: VR
tags: [ Vortex , VegaPrime ]
---
{% include JB/setup %}


Vortex 作为一款物理引擎，能够在虚拟现实仿真中提供动力学特性，在Vortex 6.0版本以后，可以使用可视化建模工具Vortex Editor 建立物理模型。
Vega Prime 在做视景仿真时，可以使用LynX Prime 工具来配置场景。Vortex Editor 与 LynX Prime 两者有效的结合，会为开发工作很便利。

<!-- more -->

1. 建立Mechanism ，并导出acf文件

	<img  src="{{ site.url }}/assets/images/vr/2014061701.png" />
 
	file  ->  Export  -> 保存类型选择acf  -> 保存为 theMechanism.acf

	最终会生成三个文件：

	<img  src="{{ site.url }}/assets/images/vr/2014061702.png" />

2. 建立acf，并导入VxMechanism 对象
 
	<img  src="{{ site.url }}/assets/images/vr/2014061703.png" />
 
	File  ->  import ACF  ->  打开theMechanism.acf文件

	在导入的VxMechanism 对象中，既包含了 几何模型，也包含了 物理模型

	<img  src="{{ site.url }}/assets/images/vr/2014061704.png" />

3. 在acf中添加VxUniverse ，并且为VxUniverse 设置Scene ，将VxMechanism加到VxUniverse中。

	<img  src="{{ site.url }}/assets/images/vr/2014061705.png" />
  
4. 在VC++ 工程中调用acf文件

	首先，要配置所需的附加包含目和附加库目录，可以根据Vortex和VegePrime 的自带例子，进行配置。
	然后，编写代码，调用acf 启动仿真。这个过程与VegaPrime 调用acf 文件的代码基本一致。

		#include <vpApp.h>
		#include <Vx/VxPart.h>
		#include <Vx/Find.h>

		class myApp : public vpApp {
		public:
			myApp() {
			}
			void onKeyInput( vrWindow::Key key, int mod ){
				switch ( key ){
					case vrWindow::KEY_w:{
						//获取一个 part
						Vx::VxPart *pipe=Vx::Find::part("pipe");
						//唤醒 part
						pipe->wakeDynamics();
						//设置线速度
						pipe->setLinearVelocity(1,0,0);
						break;
					}
				default: vpApp::onKeyInput(key,mod);
				}
			}

			~myApp()  { }
		};
	
	在主函数与 Vega Prime 的使用基本一致。
	
		int main(int argc, char *argv[])
		{
			// initialize vega prime
			vp::initialize(argc, argv);

			// create my app instance
			myApp *app = new myApp;

			// load acf file
			if (argc <= 1)
				app->define("test.acf");
			else
				app->define(argv[1]);

			// configure my app
			app->configure();

			// runtime loop
			app->run();

			// unref my app instance
			app->unref();

			// shutdown vega prime
			vp::shutdown();

			return 0;
		}
