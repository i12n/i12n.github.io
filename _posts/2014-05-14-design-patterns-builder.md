---
layout: post
title: "设计模式：建造者模式"
description: ""
category: Design Patterns
tags: [creational patterns]
---
{% include JB/setup %}

建造者模式将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

建造者模式适用于当创建复杂对象的算法应该独立于该对象的组成部分以及他们的装配方式时，当构造过程必须允许被构造的对象有不同的表示时。

<!-- more -->


 <img  src="{{ site.url }}/assets/images/2014051401.png" />
 
Builder:为创建一个Product对象的各个部件指定抽象接口；

ConcreteBuilder：实现Builder的接口以构造和装配该产品的各个部件；定义并明确它所创建的表示；提供一个检索产品的接口

Director：构造一个使用Builder接口的对象

Product：复杂对象，产品没有抽象类，通常情况下由不同的ConcreteBuilder生成的产品，它们的表示会相差很大，导致没有共同的接口



	class ProductA{
	  public:
	    ProductA(char name):_name(name){}
	    int setPartOne(int one){
	        _partOne=one;
	        return _partOne;
	    }
	    int setPartTwo(int two){
	        _partTwo=two;
	        return _partTwo;
	        }
	    void show(){
	        std::cout<<"Name:"<<_name<<std::endl;
	        std::cout<<"PartOne:"<<_partOne<<std::endl;
	        std::cout<<"PartTwo:"<<_partTwo<<std::endl;
	    }
	  private:
	    int _partOne;
	    int _partTwo;
	    char _name;	  
	} ;
	
	class ProductB{
	  public:
	    ProductB(char name):_name(name){}
	    int setPartOne(int one){
	        _partOne=one;
	        return _partOne;
	    }
	    int setPartTwo(int two){
	        _partTwo=two;
	        return _partTwo;
	    }
	    void show(){
	        std::cout<<"Name:"<<_name<<std::endl;
	        std::cout<<"PartOne:"<<_partOne<<std::endl;
	        std::cout<<"PartTwo:"<<_partTwo<<std::endl;
	    }
	  private:
	    int _partOne;
	    int _partTwo;
	    char _name;
	} ;
	
	class Builder {
	  public:
	    virtual int buidPartOne(int one){};
	    virtual int buidPartTwo(int two){}; 
	};
	
	class ConcreteBuilderA:public Builder{
	  public:
	    ConcreteBuilderA(){
	        _result=new ProductA('A');
	    }
	    int  buidPartOne(int one){return _result->setPartOne(one);};
	    int  buidPartTwo(int two){return _result->setPartTwo(two);};
	    ProductA *getResult(){return _result;}
	  private:
	    ProductA *_result;
	};
	
	class ConcreteBuilderB:public Builder{
	  public:
	    ConcreteBuilderB(){
	        _result=new ProductB('B');
	    }
	    int  buidPartOne(int one){return _result->setPartOne(one);};
	    int  buidPartTwo(int two){return _result->setPartTwo(two);};
	    ProductB *getResult(){return _result;}
	  private:
	    ProductB *_result;

	};


	class Director{
	  public:
	    void setBuilder(Builder *builder){
	        _builder=builder;
	    }
	    void construct(int one,int two){
		    _builder->buidPartOne(one);
		    _builder->buidPartTwo(two);
		}
	  private:
	    Builder *_builder;
	};



	ConcreteBuilderA *builderA=new ConcreteBuilderA();
	ConcreteBuilderB *builderB=new ConcreteBuilderB();
	Director *director=new Director();
	director->setBuilder(builderA);
	director->construct(10,30);
	director->setBuilder(builderB);
	director->construct(210,310);
	builderA->getResult()->show();
	builderB->getResult()->show();

