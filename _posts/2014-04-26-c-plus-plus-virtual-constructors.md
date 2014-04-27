---
layout: post
title: "C++中的虚拟构造函数"
description: ""
category: cpp
tags: [ construction ]
---
{% include JB/setup %}

构造函数虚化(virtual constructors)是指某种函数根据输入产生不同类型的对象，在设计模式的几个创建型模式中，都涉及了构造函数的虚化。
简单工厂设计模式(又叫做静态工厂方法，不属于23种GOF设计模式)，便是以构造函数虚化进行的。

以简单工厂设计模式为例，水果商店能够提供水果，顾客通过水果商店购买水果。FruitShop Fruit Apple Banana 构成简单工厂模式。
其中FruitShop 对应工厂，Fruit对应抽象产品，Apple和 Banana 对应具体产品。 FruitShop 的 getFruit 方法产生不同类型的水果对象，按需提供不同的对象，实现了构造函数的虚拟化。

![  ]({{ site.url }}/assets/images/2014042601.png)

	class Fruit{//抽象水果类，对应产品 
	    public:     
	        virtual void showName()=0;
	        virtual ~Fruit(){};    
	};
	class Apple:public Fruit{//苹果类，对应具体产品 
	    public:
		    Apple():name("Apple"){};
		    void showName(){std::cout<<"Fruit::"<<name<<std::endl;};
	    private:
		    std::string name;
	};
	class Banana:public Fruit{//香蕉类，对应具体产品 
	    public:
		    Banana():name("Banana"){};
		    void showName(){std::cout<<"Fruit::"<<name<<std::endl;};
	    private:
			std::string name;
	};
	class FruitShop{//水果商店类，对应工厂 
	    public:
	        static Fruit *getFruit(int fruitType){//获取具体水果 
	        switch(fruitType){
	            case 1: return new Apple(); 
		        default: return new Banana();
	        }
	    }
	};

	class Client{//顾客类 
	    public:
	        void buyFruit(int fruitType){
	        basket.push_back(FruitShop::getFruit(fruitType));
	    }
	    void hasFuit(){
	        for(std::vector< Fruit * >::iterator it=basket.begin();it<basket.end();++it){
	            (*it)->showName();
	        }
	    }
	    private:
	        std::vector< Fruit * >basket;
	};

	Client client;
	client.buyFruit(1);// Apple
	client.buyFruit(1);// Apple
	client.buyFruit(0);// Banana
	client.hasFuit();

	
对复制构造函数虚化(virtual copy constructors)，会返回一个指向自身新副本的指针。在Book类中，clone 方法返回的是自身的一个新副本的指针，而且对象是通过复制构造函数来生成的。
在设计模式中的原型模式使用了复制构造函数虚化方法。

	class Book{
	    public:
	        Book(std::string _bookname,std::string _author):bookname(_bookname),author(_author){}
		    void showBook(){
	            std::cout<<"bookname  "<<bookname<<std::endl;
	            std::cout<<"bookauthor  "<<author<<std::endl;
	        };
	        Book *clone(){
	            return new Book(*this);
	        }
	    private:
	        std::string bookname;
	        std::string author;           
	};

	Book book("One Hundred Years of Solitude","Gabriel García Márquez");
	Book *bookcopy=book.clone();
	book.showBook();
	bookcopy->showBook();

