---
layout: post
title: "设计模式：单例模式"
description: ""
category: Design Patterns
tags: [ creational patterns ]
---
{% include JB/setup %}

单例模式保证一个类只有一个实例，并且提供了它的全局访问点。


<img  src="{{ site.url }}/assets/images/2014042901.png" />


	class Singleton{
	  public:
	    static  Singleton *Instance(){
	        if(instance==NULL){
	            instance=new Singleton();
			}
	        return instance;
	    };
	    std::string getName(){return name;}
	  protected:
	    Singleton():name("Singleton"){}; 
	  private:
	    std::string name;
	    static  Singleton *instance;
	};
	Singleton *Singleton::instance=NULL;

Instance()声明为static允许客户访问Singleton的唯一实例；为了保证唯一的实例将构造函数Singleton()进行保护。

	Singleton *instance=Singleton::Instance();
	std::cout<<instance->getName()<<std::endl;

1.单例模式对实例的初始化是缓式(lazy)的，只有在第一次访问 Instance() 才初始化创建Singleton 对象。

2.针对线程安全问题，可以将缓式(lazy)改为在声明时初始化：

	Singleton *Singleton::instance= new Singleton();

也可以加入互斥锁进行控制。

