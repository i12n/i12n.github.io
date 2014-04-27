---
layout: post
title: "C++中数据成员的布局"
description: ""
category: cpp
tags: []
---
{% include JB/setup %}

在类中，成员函数会被编译器转换为外部函数，因此，对象中不存储成员函数，
类对象中存储的是数据成员变量以及编译器加入的数据信息等。
一个类对象所占空间的大小除了与数据成员有关系之外还与这些相关：

语言的额外负担：在C++中为了支持虚函数等，编译器会在类对象加入额外的信息。

编译器优化处理：对一些特殊情况，编译器会进行优化处理；而不同的厂商的编译器处理机制也存在着差别。

聚合结构边界调整：为了更有效率的存取数据，会在对象中填充额外的字节。

因此对于不同类型的类中，类对象的组织方式是不同的，
主要分为以下几种情况：无继承也无虚函数的类、有继承关系且无虚函数的类、有继承关系且有虚函数的类以及虚基类中。

#####1.无继承关系且无虚函数的类

在没有继承关系也无虚函数的类中，情况相当比较简单，类对象所占用的存储空间只与类的数据成员有关系。
	
	class B{    //sizeof(B)==4
	  int b;
	};
	class C{    //sizeof(C)==1
	  char c;
	};
	
但是对于一个没有任何数据成员的类，它的大小并不是0，编译器要做一些处理，加入一个隐藏的char 使对象有一个独一无二的地址。

	class A{    //sizeof(A)==1
	};
如果类成员在存在不同类型数据成员时，可能会发生边界调整，默认调成到
4的倍数。
	class D{    //sizof(D)==8
	  int d1;
	  char d2;
	};
	
![D object layout]({{ site.url }}/assets/images/2014042101.png)
 

#####2.有继承关系且无虚函数的类
当基类和派生类中都不存在虚函数时，派生类中既要存储基类的数据成员又要存储自己的数据成员，所占用的存储空间由这两部分决定。

	class B1:public B{    //sizeof(B1)==8
	  int b1;
	};
	class C1:public C{    //sizeof(C1)==2
	  char c;
	};
	
	但是当基类无数据成员时，编译器会做出优化处理，在派生类中去掉基类中的那个char。
	
	class A1:public A{    //sizeof(A1)==1
	};
	class A2:public A{    //sizeof(A2)==1
	  char a2;
	};
	
在继承过程中也会发生边界调整。

	class B2:public B{    //sizeof(B2)==8
		A a;
	};

#####3.有继承关系且有虚函数的类

虚函数的出现，在类对象中要存放一个vptr指针指向vtbl，不同编译器把vptr存放的位置不同，有的存在对象的开始位置，有的存在结束位置。

	class VA{   //sizeof(VA)==4
	  virtual ~VA(){}
	};

VA虽然是无成员数据的类，因为有了vptr指针，就不再需要额外的一个char。
	
	class VA1:public VA{    // sizeof(VA1)==8
		char c1;
	};//在VA1中需要进行边界调整。
	class VB{
	  virtual ~VB(){}
	};
	class VAB:public VA,public VB{    // sizeof(VA1)==8
	};

![VAB object layout]({{ site.url }}/assets/images/2014042102.png)
 
#####4.虚基类

虚继承中，在派生类中还需要一个指针，指向虚基类的对象。

	class M{    // sizeof(M)==1
      char m;
	};
	class X:virtual public M{    // sizeof(X)==8
	};
	class Y:virtual public M{    // sizeof(Y)==8
	};
	class Z:public X,public Y{    // sizeof(Z)==12
	};
	
![Z object layout]({{ site.url }}/assets/images/2014042103.png)