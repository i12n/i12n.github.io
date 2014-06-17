---
layout: post
title: "设计模式：装饰模式"
description: ""
category: Design Patterns
tags: [ structural patterns ]
---
{% include JB/setup %}


意图：动态的给一个对象添加职责。

动机：希望为某个对象动态的添加一些特性，而不是为整个类添加功能。通过继承机制添加的功能会被多个子类对象所使用，而且不能控制使用该功能的时机，因此这种方法不够灵活。
适用性：在不影响其他对象的前提下，以动态透明的方式为单个对象添加职责；
处理那些可以撤销的职责；当不能以生成子类的方法进行扩充时。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014061701.png" />


Component:定义一个接口，可以给这些对象动态的添加职责

	class Component{
	  public:
	    virtual void Operation()=0;
	};

ConcreteComponent：定义一个对象，可以给这些对象添加职责。

	class ConcreteComponent:public Component{
	  public:
	    void Operation(){
	        std::cout<<"ConcreteComponent"<<std::endl;
	    }
	};

Decorator：维持一个指向Component的指针，定义一个与Component 一致的接口。

	class Decorator:public Component{
	  public:
	    Decorator(Component *_component):component(_component){
	    }
	    void Operation(){
	        component->Operation();
	    }
	private:
	    Component *component;
	};

Concrete Decorator：向组件添加职责

	class ConcreteDecorator:public Decorator{
	  public:
	    ConcreteDecorator(Component *_component):Decorator(_component){
	    }
	    void Operation(){
	        Decorator::Operation();
	        AddedBehavior();
	    }
	    void AddedBehavior(){
	        std::cout<<"AddedBehavior"<<std::endl;
	    }
	};

	Component *component=new 	ConcreteComponent();
	Decorator *decorator=new ConcreteDecorator(component);
	decorator->Operation();

