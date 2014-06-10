---
layout: post
title: "设计模式：抽象工厂模式"
description: ""
category: Design Patterns
tags: [creational patterns]
---
{% include JB/setup %}

提供一个创建一系列相关或相互依赖的对象的接口，而无需指定它们具体的类。

一个系统要独立于它的产品的创建组合和表示。

一个系统要由多个产品系列来配置时。

当要强调由多个产品系列中的一个来配置时。

当提供一个产品类库，指想显示它们的接口而不是实现时。

<img  src="{{ site.url }}/assets/images/2014051901.png" />

在client 中仅使用AbstractProduct 和 AbstractFactory 类声明的接口。
易于交换产品序列，但不宜于产生新产品。

	class AbstractProductA{//抽象产品
	  public:
		virtual void draw()=0; 
	};
	class ProductA1:public AbstractProductA{//具体产品
	  public:
		void draw(){std::cout<<"This is ProductA1"<<std::endl;};
	};
	class ProductA2:public AbstractProductA{//具体产品
	  public:
		void draw(){std::cout<<"This is ProductA2"<<std::endl;};
	};
	
	
	class AbstractProductB{//抽象产品
	  public:
		virtual void write()=0;
	};
	class ProductB1:public AbstractProductB{//具体产品
	  public:
		void write(){std::cout<<"ProductB1 write"<<std::endl;};
	};
	class ProductB2:public AbstractProductB{//具体产品
	  public:
		void write(){std::cout<<"ProductB2 write"<<std::endl;};
	};

	
	class AbstractFactory{//抽象工厂
	  public:
		virtual  AbstractProductA *createProductA()=0;
		virtual  AbstractProductB *createProductB()=0;
	};
	class ConcreteFactory1:public AbstractFactory{//具体工厂
	  public:
		AbstractProductA *createProductA(){return new ProductA1();}
		AbstractProductB *createProductB(){return new ProductB1();}
	};
	class ConcreteFactory2:public AbstractFactory{//具体工厂
	  public:
		AbstractProductA *createProductA(){return new ProductA2();}
		AbstractProductB *createProductB(){return new ProductB2();}
	};

	
	class Client{
	  public:
		Client(AbstractFactory  *factory){
			pA=factory->createProductA();
			pB=factory->createProductB();
		};
		void show(){
			pA->draw();
			pB->write();
		};
	  private:
			AbstractProductA *pA;
			AbstractProductB *pB;
	}; 
	AbstractFactory *factory =new ConcreteFactory1(); 
    Client *client=new Client(factory);
    client->show();