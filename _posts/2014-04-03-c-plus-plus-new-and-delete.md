---
layout: post
title: "C++中的 new 和 delete 操作符"
description: ""
category: CPP
tags: [ operators]
---
{% include JB/setup %}

##### new operator 与 operator new 区别

`new operator` 就是用于创建堆内存对象 (heap-based object) 的 new，它首先分配堆内存空间，然后调用构造函数初始化内存空间。它是程序内建的，不能被改变。
因为无法直接调用对象初始化所必需的构造函数，所以建一个 heap-based object 需要使用 `new operator` 。

`operator new` 则是用于分配原始的未设初值的堆内存空间，与 c 语言中的 malloc 类似。也就是new operator 在分配堆内存空间时，要调用operator new来实现。
 `operator new` 是可以被重载的。函数原型为：
 
	void *operator new(size_t size) throw(std::bad_alloc)

`operator new` 有多种重载版本，例如placement new，但每一个版本的第一个参数都是 size ，表示申请分配内存的大小。例如

	char *p= (char *)operator new(1); //使用 operator new 申请一个字节空间
	p[0]='8';
	std::cout<<p[0]<<std::endl; //print 8

禁止一个类产生 heap-based object 方法就是将 `operator new` 私有化，这样就禁止了 `new operator` 使用 `operator new` ，使 `new operator` 分配堆内存空间失败。例如：
	
	class Object{
      private:
	        static void *operator new(size_t size);
	};
	Object *object=new Object();//编译错误 `static void* Object::operator new(size_t)' is private
	
也就是说在 `new operator` 中会调用 `operator new` 来分配内存空间。

#####placement new 特定位置上的 new

`placement new` 用于在已经分配的内存空间中构造对象。它通过调用一个构造函数来初始化一个对象，然后将这个对象放在一块已经分配好的原始内存上(这块内存也可是栈内存)。
在STL中它的用途之一就是负责在 vector 的未使用的空间上创建对象。使用 `placement new` 需要使用头文件 `#include<new>`。

	void *operator new(std::size_t size,void *pMemory) throw();

pMemory 用于指向内存空间

例如：

	#include<new>
	char buff[100];
	double *pd;
	pd=new(buff) double[3];  //类似于 在new double[3]; 基础上加了个参数 buff
	pd[0]=1;
	std::cout<<pd[0]<<std::endl;


#####释放内存delete相关

与`new operator` 、`operator new` 和 `placement new` 对应的是 `delete operator`、`operator delete` 和 `placement delete`。

`delete operator`  释放由`new operator` 申请的内存，分为两步操作：首先调用对象的析构函数，然调用 `operator delete`释放内存。

`operator delete` 处理原始的为被设初值的内存，例如，释放operator new申请的内存并且这个内存未调用任何构造函数。原型为：

	void operator delete(void *memoryToBeDeallocated); //memoryToBeDeallocated 为要释放的内存地址
	
例如：

	void *buff=operator new(50*sizeof(char));//分配内存，没有调用任何ctors
	.....
	operator delete(buff);// 释放内存，未调用任何dtors

	placement delete 可以调用对象析构函数，而不释放内存空间，释放动作通过原始的内存地址实现。
	