---
layout: post
title: "设计模式：职责链模式"
description: ""
category: Design Patterns
tags: [ Behavioral Patterns ]
---
{% include JB/setup %}
意图：使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连接成一条链，并沿着这条链传递该请求，直到一个对象处理它为止。

适用性：有多个对象可以处理一个请求，哪个对象处理该请求要在运行时刻自动确定；在不明确指定接收者的情况下，向多个对象中的一个提交一个请求；可处理一个请求的对象集合应被动态指定。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014061801.png" />

Handler: 定义出来请求接口；实现后继链接。

	class Handler{
		public:
			Handler(Handler * handler):_successor(handler){
			}
			virtual void HandleRequest(int param){
				if(_successor)_successor->HandleRequest(param);
				else std::cout<<"can't find "<<param<<std::endl;
			};
		private:
			Handler *_successor;	
	};

ComcreteHandler: 处理它所负责的请求；可访问它的后继者；如果可处理该请求，就处理它，否则转发请求给后继者。

	class ConcreteHandler:public Handler{
		public:
			ConcreteHandler(Handler *handler, int param):Handler(handler), _param(param){
			}
			void HandleRequest(int param){
				if(param*2==_param){
					std::cout<<"this is "<<_param<<std::endl;
				}
				else{
					Handler::HandleRequest(param);
				}
			}
			private:
				int _param;
	};

	Handler *hander1=new ConcreteHandler(0,2);
	Handler *hander2=new ConcreteHandler(hander1,6);
	hander2->HandleRequest(3);