---
layout: post
title: "设计模式：原型模式"
description: ""
category: 
tags: [  creational patterns  ]
---
{% include JB/setup %}

用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
原型模式避免了与产品层次平行的工厂类层次，即不需要建立额外的工厂类来提供对象。原型模式减少了子类的构造，因为原型模式是克隆出一个对象。原型模式在运行时刻指定要实例化的类，如果当前不能确定运行期间生成的类则原型模式会提供便利。原型模式可以改变值和结构来指定新对象。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;
![ prototype ]({{ site.url }}/assets/images/2014042801.png)
	
实现原型要考虑的问题：

1.使用原型管理器 当系统中原型数目不固定时，需要一个注册表(关联存储器)来注册原型与解除注册。

2.实现克隆 需要注意深拷贝与浅拷贝的问题。

3.初始化克隆对象 在Clone 操作中传递参数会破坏Clone接口的统一性。

	class Fruit{//抽象水果类，对应Prototype  
	  public:     
	    virtual void showName()=0;
	    virtual Fruit *clone()=0;
	    virtual ~Fruit(){};    
	};
	class Apple:public Fruit{//苹果类，对应ConcretePrototype  
	  public:
		Apple():name("Apple"){};
		Fruit *clone(){return new Apple(*this);}
	    void showName(){std::cout<<"Fruit::"<<name<<std::endl;};
	private:
		std::string name;
	};
	class Banana:public Fruit{//香蕉类，对应ConcretePrototype  
	  public:
	    Banana():name("Banana"){};
	    Fruit *clone(){return new Banana(*this);}
	    void showName(){std::cout<<"Fruit::"<<name<<std::endl;};
		   
	  private:
	    std::string name;
	};

	Fruit *fruit1=new Banana();
	Fruit *fruit2=fruit1->clone();;
	fruit1->showName();
	fruit2->showName();

这篇写得文章不错 [点击查看](http://www.cnblogs.com/hegezhou_hot/archive/2010/12/04/1896471.html)