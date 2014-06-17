---
layout: post
title: "设计模式：代理模式"
description: ""
category: Design Patterns
tags: [ structural patterns ]
---
{% include JB/setup %}
意图：为其他对象提供一种代理以控制这个对象的访问。

动机：控制一个对象的访问，是因为只在需要的时候才创建这个对象；代理模式就是使用其他的对象替代要访问的对象，并且在需要的时候负责实例化它。

适用性：远程代理，虚代理，保护代理，智能指针。
<!-- more -->

<img  src="{{ site.url }}/assets/images/2014061601.png" />

Subject：定义RealSubject和Proxy的公用接口任何使用RealSubject的地方都可以使用Proxy

	class Subject{
	  public:
	    virtual void  Request()=0;
	};

RealSubject：定义Proxy所代表的实体

	class RealSubject:public Subject{
	  public:
	    void Request(){
	        std::cout<<"request"<<std::endl;
	    }
	};

Proxy：保存一个引用使得代理可以访问一个实体，若RealSubject和Subject的接口相同，Proxy会引用Subject；提供一个与Subject的接口相同的接口，这样代理就可以代替实体；控制对实体的存取，并可能负责创建和删除它

	class Proxy:public Subject{
	  public:
		Proxy():realSubject(NULL){
		}
		void Request(){
	        if(realSubject==NULL){
	            realSubject=new RealSubject();
	        }
	        realSubject->Request();
	    }
	  private:
	    RealSubject *realSubject;
	};


	Proxy *proxy= new Proxy();
	proxy->Request();


