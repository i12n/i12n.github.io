---
layout: post
title: "C++中的虚基类"
description: ""
category: CPP 
tags: []
---
{% include JB/setup %}

当类的继承中出现菱形继承关系时，会在最底层派生类中出现同一个基类的两个副本，
例如AB类对象中会保存两份Base对象。可以用虚基类的方式解决这个问题。所谓虚基类是指Base这个类，
SubA和SubB两个子类可以以虚继承的方式来继承。而AB类以正常方式继承SubA和SubB类。

虚基类改变了基类被初始化的方式。在非虚基类继承时，
子类只能在其构造函数初始化列表调用即时基类构造函数来完成即时基类的初始化(如果没有指定即时基类的构造函数，则调用默认构造函数完成初始化工作，即时基类是指最直接的基类)。
也就是说，AB只能调用SubA和SubB的构造函数，而不能越过中间类(SubA和SubB)调用Base的构造函数。
在这种情况下，只能通过传值给SubA和SubB的构造函数，进而由SubA和SubB来初始化Base。
在虚基类继承时改变了这个规则。
当基类是虚拟时，禁止将信息通过中间类自动传递给基类，而是允许初始化列表可以直接调用虚基类的构造函数(如果没有指定虚基类的构造函数，则调用默认构造函数完成初始化工作)。
也就是说，AB类初始化列表可以直接调用Base的构造函数，而AB类通过SubA和SubB传递的关于构造Base的信息将不起作用。

![虚基类类图]({{ site.url }}/assets/images/2014041702.png)
![虚基类内存布局]({{ site.url }}/assets/images/2014041701.png)

虚基类的内存布局，会有一些特殊的地方。
首先为了支持虚基类，派生类的对象要额外存储一个指针，这个指针指向虚基类对象的地址或偏移地址。
 

	class Base{
	  public:
	    Base(int i=0):i(i){};
	    int getBase(){return i;};
	  private:
	    int i; 
	};
	class SubA:virtual public Base{
	  public:
	    SubA(int i,int a):a(a),Base(i){ };
	    void showSubA(){
	        std::cout<<"Base:"<<getBase()<<" SubA:"<<a<<std::endl;
	    }
	  private:
	    int a;
	};
	class SubB:virtual public Base{
	  public:
	    SubB(int i,int b):b(b),Base(i){ };
	    void showSubB(){
	        std::cout<<"Base:"<<getBase()<<" SubB:"<<b<<std::endl;
	    };
	  private:
	    int b;
	};
	class AB: public SubA, public SubB{
	  public:
	    //use default Base constructor
	    AB(int a,int i1,int b,int i2,int ab):SubA(a,i1),SubB(b,i2),ab(ab){}; //#1
	    AB(int a,int b,int i):Base(i),SubA(a,a),SubB(b,b){};            //#2
	    void showAB(){
	        std::cout<<"showAB"<<std::endl;SubA::showSubA();SubB::showSubB();
	    };
	  private:
	    int ab;
	};
Code:

     AB ab(1,2,3,4,5);     //#1
     ab.showAB();
	 
Result:

	Base:0 SubA:2
	Base:0 SubA:4

Code:

	AB ab(1,2,3);     //#2
	ab.showAB();
	
Result:

	Base:3 SubA:1
	Base:3 SubA:2

虚继承要在继承时加关键字virtual 如代码所示:
          
	class SubA:virtual public Base{………}

如果将不使用虚基类，则上面的代码会在#2处报编译错:
	
	type `class Base' is not a direct base of `AB'

也就是说禁止AB的初始化列表中跨过中间类而用Base的构造函数。
