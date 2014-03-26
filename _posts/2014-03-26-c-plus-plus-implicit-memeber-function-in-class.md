---
layout: post
title: "C++中类的隐式成员函数"
description: ""
category: CPP
tags: [construction ]
---
{% include JB/setup %}

C++ 为类提供了以下几种隐式成员函数：
1. 默认构造函数
2. 复制构造函数
3. 赋值操作符 (operator=)
4. 默认析构函数
5. 地址操作符
如果类中没有设计这些成员函数，则编译器将隐式自动生成这些成员函数。

#####1. 默认构造函数

C++在创建类的对象时都要调用构造函数，如果在类中没有提供任何构造函数，C++将隐式创建一个默认构造函数。
在类中一旦定义了构造函数，编译器将不再提供隐式生成的默认构造函数，如果还要使用默认构造函数的话，就要显式定义将默认构造函数。
	
	class Cat{
	  private:
	    char name[20];   
	  public:
	    void show_name(){std::cout<<name<<std::endl;}
	};
	 
在Cat类中没有定义构造函数，所以编译器将提够一个默认构造函数：

	Cat::Cat(){}  //implicit default constructor
	
使用默认构造函数可以这样定义对象：

	//invokes default constructor
	Cat blueCat;
	Cat redCat();
	
如果在类中显示定义一个构造函数：

	class Cat{
      private:
	    char name[20];   
      public:
	    //Cat::Cat(){};
	    Cat(const char *str){strcpy(name,str);}; //constructor Cat(const char *)
	    void show_name(){std::cout<<name<<std::endl;};
	};
	
	Cat blueCat("blueCat"); //invokes  Cat(const char *)
	blueCat.show_name();  //print  blueCat
	Cat redCat;       //error can not invokes default constructor
	
C++编译器提供的默认构造函数，在创建对象时不能完成成员变量的初始化，因此要根据需求显式定义构造函数。

#####2. 复制构造函数

复制构造函数用于一个对象初始化另一个对象时。如果没有定义复制构造函数，编译器提供默认的复制构造函数。
默认复制构造函数的功能是，逐个将非静态成员变量的值复制到新对象的成员变量，是一种浅复制。复制构造函数原型如下：
	
	Class_name(const Class_name &);
	
当创建一个新对象，并将新对象显式的初始化为现有对象，调用复制构造函数。无论构造函数还是复制构造函数只是在创建对象时调用。
Cat 类的默认复制构造函数为 

	Cat(const Cat &);
	
以下语句都将调用复制构造函数：

	Cat aCat(blueCat);    
	Cat myCat=blueCat;
	Cat theCat=Cat(blueCat);
	Cat *pCat=new Cat(blueCat);

当程序生成对象副本时，编译器都将使用复制构造函数，即 当函数按值传递对象或函数返回对象时，都将使用复制构造函数。复制构造函数原型中 
Class_name(const Class_name &); 之所以传递的是引用而不是对象，就是因为在传递对象的时候会调用复制构造函数，最终会造成无限循环调用。

当类中的包含了使用new初始化的指针成员变量时，应该定义一个复制构造函数，以复制指针指向的数据，而不是指针，这被称为深复制。
默认的复制构造函数是浅复制，浅复制只复制指针信息，而不复制指针引用的结构。重写 Cat类：
	
	class Cat{
	  private:
        char *name;   // pointer
	  public:
	    Cat(const char *str){name=new char[20]; strcpy(name,str);};
	    // Cat(const Cat &cat){name=new char[20];strcpy(name,cat.name); };
	    void show_name(){std::cout<<name<<std::endl;}
	    void set_name(const char *str){strcpy(name,str);}
	    ~Cat(){delete [] name;}; //deconstructor
	};
	
	Cat blueCat("blueCat");
    blueCat.show_name();	//print  blueCat
    Cat theCat=Cat(blueCat);	//copy constructor
    theCat.show_name();	// print blueCat
    theCat.set_name("theCat");
    blueCat.show_name(); 	// print theCat     #1
    theCat.show_name();	// print theCat     #2
	
默认复制构造函数使 blueCat和theCat 的name 指向相同的地址，导致修改其中一个的name 指向的内容，另一个也会修改。对一个对象的操作会对另一个对象产生影响。
定义复制构造函数：

	Cat(const Cat &cat){
	    name=new char[20];
	    strcpy(name,cat.name);
	};
	
最后两句代码#1  #2输出结果将不一样：
	
	blueCat.show_name(); 	// print blueCat    #1
    theCat.show_name();	// print theCat		#2

定义复制构造函数的原因在于，一些类成员是使用new初始化的、指向数据的指针，而不是数据本身。
默认的拷贝构造函数是浅复制，只是将各个成员变量的值进行简单的赋值；当成员变量是指针时，只是复制了其地址而没有开辟新的存储空间，这时就需要自定义拷贝构造函数，进行深复制。

#####3. 赋值操作符

将已有的对象赋给另一个对象时自动为类重载赋值操作符。赋值操作符的隐式实现也是对成员的逐个复制,当类中的包含了使用new初始化的指针成员变量时，也要对其显示定义。
赋值操作符的原型为：
	
	Class_name &Class_name::operator=(const Class_name &);
	
注意事项：

分配数据之前要对以前分配的数据释放，因为有些对象在赋值之前可能已经分配了数据，这就要在分配数据之前将其释放。
避免将对象赋给自身，因为赋值之前的释放内存会破坏数据。
函数返回指向自身的引用，可以用于连续赋值。

对Cat类定义赋值操作符：

	Cat &Cat::operator=(const Cat &cat){
	    f(this==&cat)return *this;
        delete [] name;
        name=new char[20];
        strcpy(name,cat.name);
        return *this;
	}
	
    Cat blueCat("blueCat");
    Cat aCat(blueCat);
    aCat.show_name(); // print blueCat
    aCat=greenCat;
    aCat.show_name(); //print greenCat
    blueCat.show_name();//print blueCat


#####4. 析构函数
定义析构函数，主要是因为在对象销毁时要完成一些操作。例如 Cat 类的对象销毁时，要对name 释放空间。

	~Cat(){delete [] name;}; //deconstructor
	
在函数多态继承时，要虚拟析构函数。如果不虚拟析构函数，则只调用对应于指针类型的析构函数。(多态机制)

	class Cat{
      private:
        char *name;   
      public:
        Cat(const char *str){name=new char[20]; strcpy(name,str);};
	    Cat(const Cat &cat){name=new char[20];strcpy(name,cat.name); };
	    void show_name(){std::cout<<name<<std::endl;}
	    void set_name(const char *str){strcpy(name,str);}
	    Cat &operator=(const Cat &);
	    virtual ~Cat(){delete [] name;std::cout<<"Cat Deconstructor \n";};
	};
	
	Cat &Cat::operator=(const Cat &cat){
        if(this==&cat)return *this;
	    delete [] name;
	    name=new char[20];
	    strcpy(name,cat.name);
	    return *this;
	}
	
	class BigCat:public Cat{
	  public:
	    BigCat(const char * str):Cat(str){};
	    ~BigCat(){std::cout<<"BigCat Deconstructor \n";};
	};
	
	Cat *bigCat=new BigCat("bigCat");
	bigCat->show_name();
	delete bigCat;

输出：

	BigCat Deconstructor
	Cat Deconstructor
	
如果将去除 Cat 中的析构函数虚拟化，则只输出：

	Cat Deconstructor

#####5. 关于复制构造函数和赋值操作符原型

对于复制构造的原型Class_name(const Class_name &);传递的是reference-to-const参数，也就是说不对要复制源进行修改。
但是也有例外情况如智能指针auto_ptr的复制构造函数传递的就是reference-to-non-const，这是为了能够修改复制源进行所有权转移。同理赋值操作符原型。

#####6. 关于禁止使用这些成员函数

禁止使用这些成员函数可以用private或protected进行限定。

将所有构造函数包括复制构造函数都加入private限定，会导致该类不能被继承。

将析构函数加上private限定也会导致该类不能被继承。
