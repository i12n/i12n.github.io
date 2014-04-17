---
layout: post
title: "C++中的隐式类型转换"
description: ""
category: CPP
tags: [ construction, operators ]
---
{% include JB/setup %}

C++ 编译器在处理不同数据类型之间会执行类型转换，其中包括以下四种情况：
	赋值时进行的转换 将一种数据类型赋值给另一种数据类型时发生类型转换
	表达式中的转换 统一表达式包含两种不同的算术类型时，但数据类型相同时会出现自动提升变换(例如将 bool、char、unsigned char、signed char、short 转换为int 等)；数据类型不同时会出现其他一些变换(如 标准转换和用户自定义的转换)
	传递参数时的转换 在函数传递参数时，会发生类型转换
	强制类型转换 用户进行的显示类型转换
除了强制类型转换之外，其余都是C++编译器进行的隐式类型转换。C++ 隐式类型转换可以促进计算机的计算效率(如提升转换中的 int 提升转换，把数据类型转换为计算机的最自然的类型int ，会方便计算机处理)；C++ 隐式类型转换也可以为函数的调用提供便利。但是C++ 的转型在某些情况下，也会造成数据信息丢失(例如将int 转换为char)，对于C++ 内置数据类型之间的转型，用户是无法控制的，但是用户可以控制自定义数据类型的隐式类型转换。C++ 为自定义数据类型提供了两种方法来允许编译器执行隐式类型转换：单自变量constructions 和隐式类型转换操作符。
#####1.单自变量 constructions 隐式转换 

单自变量 constructions 隐式转换是指能够以单一自变量成功调用的constructions。
在constructions声明时可能有多个参数，但是除了第一个参数之外都有默认值。

	template <typename T>
	class SmartPointer{
	  public:
	    SmartPointer(T *pointer=0):pointer(pointer){};
	    T &operator*() const{return *pointer;};
	    T *operator->() const{return pointer;};
	  private:
	    T *pointer;      
	};

	int i=2014;
	int *p=&i;
	SmartPointer<int> data=p;    // 类型转换 将int * 转换为 SmartPointer<int>
	std::cout<<*data<<std::endl;  // print 2014

#####2.隐式类型转换操作符 

隐式类型转换操作符是在关键词operator之后加上一个类型名，返回值类型就是这个类型名，形如

	operator return_type()  
	
在上例中 SmartPointer 加入 成员函数：
	
	operator T() const {return *pointer;} //隐式类型转换操作符

调用以下代码：	
	
	int i=2014;
	int *p=&i;
	SmartPointer<int> data=p;
	std::cout<<data<<std::endl; //print 2014  //调用 operator T() const  进行类型转换 

#####3.要避免提供隐式类型转换

用explicit 声明construction 禁止隐式类型转换，而在C++提供的智能指针std::auto_ptr 的construction是被explicit声明的。 
还要避免声明隐式类型转换操作符而改用函数，在std::string 中 从string Object 到C-style char* 的转换是用成员函数c_str 而不是隐式类型转换函数。

