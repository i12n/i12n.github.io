---
layout: post
title: "设计模式：桥接模式"
description: ""
category: Design Patterns
tags: [ structural patterns ]
---
{% include JB/setup %}

意图：将抽象部分与实现部分相分离，使他们都可以独立的变化。

动机：当一个抽象类可以有多个实现时，通常由继承来协调他们。
但是，这种方法缺少灵活性，继承机制将抽象部分和实现部分固定在了一起，使得对抽象部分和实现部分的修改、扩充和重用难以进行。

适用性：不希望抽象和实现部分有固定的捆绑关系；抽象和实现部分可以通过生成子类的方法进行扩充；C++中对用户隐藏抽象的实现部分。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014061101.png" />

Abstraction：定义抽象类接口；维护一个指向Implementor对象的指针

	class Abstraction{
	  public:
	    virtual void operation()=0; 
	    Abstraction(Implementor *_imp):imp(_imp){}
	    virtual ~Abstraction(){}
	  protected:	
	    Implementor *imp;
	};

RefinedAbstraction：扩充Abstraction定义的接口

	class RedefineAbstraction:public Abstraction{
	  public:
	    void operation(){
	        imp->operationImp();
	    }
	    RedefineAbstraction(Implementor *_imp):Abstraction(_imp){
	    }	
	};


Implementor：定义实现类的接口

	class Implementor{
	  public:
		virtual void operationImp()=0;
		~Implementor(){}
	  protected:
		Implementor(){}
	};

ConcreteImplementor：实现Implementor接口并定义它的具体实现



	class ConcreteImplementor:public Implementor{
	  public:
		void operationImp(){
			std::cout<<"operator implementor"<<std::endl;
		}
	};


	Implementor *imp= new ConcreteImplementor();
	Abstraction *abstraction=new RedefineAbstraction(imp);
	abstraction->operation();

