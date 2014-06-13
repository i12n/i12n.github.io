---
layout: post
title: "设计模式：适配器模式"
description: ""
category: Design Patterns
tags: [structural patterns]
---
{% include JB/setup %}
意图：将一个类的接口转换为客户希望的另一个接口，使得原本接口不兼容的那些类能够在一起工作。

适用性：使用一个已经存在的类，但是它的接口不符合需求；复用一个类，但是该类需要与其他不相关的类或不可预见的类协同工作。

适配器分为两类：类适配器和对象适配器。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014061001.png" />

类适配器用一个具体的Adapter类对Adaptee和Target进行匹配；Adapter类可以重新定义Adaptee类的部分行为（因为继承关系，可以对虚函数重写）；
当要匹配一类以及它的所有子类时，类Adapter将不能胜任工作。

<img  src="{{ site.url }}/assets/images/2014061002.png" />

对象适配器允许一个Adaper与一个或多个Adpatee（包括Adaptee的子类）同时工作；重定义Adapter的行为比较困难（需要生成Adaptee的子类，
并且Adapter要引用这个子类）。

Target 定义Client使用的特定领域的接口

	class Target{
	  public:
	    virtual void Request()=0;
	    virtual ~Target(){}
	};

Adaptee 定义以及存在的接口，且接口需要适配

	class Adaptee{
	  public:
	    void SpecificRequest(){
	        std::cout<<"this is Adaptee's SpecificRequest"<<std::endl;
	    }
	};

Adapter对Adapee的接口与Target接口进行适配

	//类适配器
	class Adapter:public Target,private Adaptee{// 私有继承Adaptee
	  public:
	    void Request(){
	        this->SpecificRequest();
	    }
	};

	//对象适配器
	class Adapter:public Target{
	  public:
	    void Request(){
	        Adaptee adaptee;
	        adaptee.SpecificRequest();
	    }
	};

Client与符合Target接口的对象协同

	class Client{
	  public:
	    void show(Target *target){
	        target->Request();			
	    }
	};
	
	
	Client client;
	client.show(new Adapter());	
