---
layout: post
title: "设计模式：工厂方法"
description: ""
category: Design Patterns
tags: [ creational patterns ]
---
{% include JB/setup %}

工厂方法设计模式是一种创建型模式，定义一个创建对象的接口，让子类决定实例化哪一个类。工厂方法不再将与特定应用有关的类绑定到代码中，代码仅处理Product接口；工厂方法要为特定的ConcreteProduct创建Creator的子类。

1.Product 定义工厂方法所创建对象的接口。

	class Fruit{//抽象水果类，对应Product 
	public:     
		virtual void showName()=0;
		virtual ~Fruit(){};    
	};

2.ConcreteProduct 实现Product接口

	class Apple:public Fruit{//苹果类，对应ConcreteProduct 
	public:
		Apple():name("Apple"){};
		void showName(){std::cout<<"Fruit::"<<name<<std::endl;};
	private:
		std::string name;
	};
	class Banana:public Fruit{//香蕉类，对应ConcreteProduct 
	public:
		   Banana():name("Banana"){};
		   void showName(){std::cout<<"Fruit::"<<name<<std::endl;};
	private:
			std::string name;
	};

3.Creator 声明工厂方法，调用工厂方法创建Product对象。

	class FruitShop{//水果商店类，对应Creator
	public:
		 virtual Fruit *getFruit()=0;
		 virtual ~FruitShop(){};
	};

4.ConcreteCreator 重定义工厂方法返回 ConcreteProduct 实例

	class AppleShop:public FruitShop{//苹果商店，对应ConcreteProduct
	public:
			Fruit *getFruit(){
				  return new Apple(); 
			}
	};

	class BananaShop:public FruitShop{//香蕉商店，对应ConcreteProduct
	public:
			Fruit *getFruit(){
				  return new Banana(); 
			}
	};

5.以上相关类的类图	
![  ]({{ site.url }}/assets/images/2014042602.png)

6.通过 FruitShop 获取水果，而无需知道具体的水果类。

	FruitShop *shop=new AppleShop();
	std::vector< Fruit * >basket;
	basket.push_back(shop->getFruit());//获取苹果
	delete shop;
	shop=new BananaShop();
	basket.push_back(shop->getFruit());//获取香蕉
	for(std::vector< Fruit * >::iterator it=basket.begin();it<basket.end();++it)
	(*it)->showName();

7.不难发现， ConcreteProduct 的产生需要与之对应的ConcreteCreator ，也就是说当出现了新的水果时，就需要建立相对应的商店。为了避免产生大量的ConcreteCreator在C++中可以使用模板来生成 ConcreteProduct 对象。

	template<typename T>
	class StandardFruitShop:public FruitShop{
	public:
		   virtual Fruit *getFruit();

	};
	template<typename T>
	Fruit * StandardFruitShop<T>::getFruit(){
		   return new T;
	}

	StandardFruitShop<Apple> *appleShop=new StandardFruitShop<Apple>();//苹果商店
	Fruit *apple=appleShop->getFruit();//获取苹果
	apple->showName();



