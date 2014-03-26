---
layout: post
title: "C++中的函数返回值"
description: ""
category: CPP
tags: [ const, reference ]
---
{% include JB/setup %}


#####1. 函数返回reference-to-const引用

使用reference-to-const 返回值是为了提高效率并且保证安全不被切割，但是不能把函数内部的局部变量作为返回值，因为局部变量在函数执行结束后会被释放，导致返回引用指向的对象将不会存在。
const作为限定符防止通过引用对其指向的对象进行修改。

	class vector{
	  private:
	    loat x;
	    float y;
	  public:
	    vector(float x,float y):x(x),y(y){};//构造函数
	    friend const vector &max_mod(const vector &,const vector &);//取模的最大
	};
	
	const vector &max_mod(const vector &v1,const vector &v2){
	    float d1=(v1.x)*(v1.x)+(v1.y)*(v1.y);
	    float d2=(v2.x)*(v2.x)+(v2.y)*(v2.y);
	    if (d1>d2) return v1;
	    return v2;
	}
	 
使用对象接收函数返回的引用，将调用对象的operator= (赋值操作符)将引用所指向的对象赋值给外面的对象。例如max_mod函数将返回的值赋值给v3。

	vector v3
	v3=max_mod(v2,v1);
	
使用 reference-to-non-const 引用来接收 reference-to-const 返回值是被禁止的，编译器会报错。下面的代码是错误的，const限制符不允许修改指向的对象，而v4 是 reference-to-non-const 能够改变所指向的对象。
	
	vector &v4=max_mod(v2,v1);
	
使用reference-to-const 接收函数的reference-to-non-const 是可以的。v4与max_mod函数返回对象(v1或v2)的地址是一致的。
	
	const vector &v4=max_mod(v2,v1);

#####2. 函数返回reference-to-non-const引用
 
返回指向非const对象的引用主要有两个目的，第一是为了提高效率，non-const允许通过引用的方式对指向的对象进行修改。第二是只能返回引用，不能返回对象，因为返回对象需要调用复制构造函数，当复制构造函数禁止被调用时，就不能以对象的方式返回，只能返回引用。 

	class vector{
	  ..............................
	  public:
	    vector(float x,float y):x(x),y(y){};//构造函数
	    vector &operator=(const vector &);//引用作为参数提高效率
	    friend std::ostream & operator<<(std::ostream &os,const vector & v );//ostream 没有公有的复制构造函数
	};

#####3. 返回对象

如果返回的是函数中的局部变量，则应该以对象的方式返回。在函数返回对象时，返回语句会隐式调用复制构造函数来创建一个调用程序能够访问的对象(临时对象)。这也是为什么返回对象效率低的原因，而现代编译器已经对返回值进行优化(RVO)，在一定程度上提高了效率。
例如 对vector类的operator+进行重载:

	class vector{
	  ...............................
	  public:
	    vector operator+(const vector & v);
	};
	
	vector vector::operator+(const vector &v){
	    return vector(this->x+v.x,this->y+v.y);
	}
	
#####4. 返回const对象

const限制对象的返回，会防止某些错误行为发生。在上面的例子中对operator+的重载会允许下面的代码发生：
	
	if(v1+v2=v3){}
	
而我们想要的代码可能是：

	if(v1+v2==v3){}

为了禁止这种行为，可以返回const对象，对operator+重载如下：

	class vector{
	  ...............................
	  public:
	    const vector operator+(const vector & v);
	};
	
	const vector vector::operator+(const vector &v){
	    return vector(this->x+v.x,this->y+v.y);
	}

#####5. 函数返回值优化(RVO)

函数以by-value方式返回对象时，会产生临时对象。临时对象的产生与销毁都需要代价。
这个临时对象是不可见的，不会在源代码中出现，它的作用是接收函数的返回值(只要产生了一个non-heap object而没有为它命名，于是便诞生了一个临时对象)。   
例如，对于上面重载的operator+，在没有返回值优化的情况下，编译器可以将其重写为：
	
	void vector_add(const vector & __result,const vector &v1,vector &v2){
	    struct  vector __tempObject;//匿名对象
	    __tempObject.vector::vector(this->x+v.x,this->y+v.y);//构造匿名变量
	    __result.vector::vector(__tempObject);//调用复制构造函数
	    __tempObject.vector::~vector();//销毁匿名变量
	    return;
	}

对于语句：v3=v1+v2;
解释为以下伪代码：
	
	struct vector __tempResult; //暂存，此处没有构造函数，临时对象
	vector_add(__tempResult,v1,v2);//所有参数按引用传递
	v3=__tempResult;//将结果反馈到左边


临时对象 "__tempResult" 是为了接收函数的返回值。

当编译器提供返回值优化(ROV)时，伪代码如下：
	
	void vector_add(const vector & __result,const vector &v1,vector &v2){
	    __result.vector::vector(this->x+v.x,this->y+v.y);//创建__result
	    return;
	}
	
在RVO优化中，直接把要返回的对象构造在接收返回值的临时对象内，从而省去了函数内的局部变量的构造等操作。

返回值优化是编译器进行的优化。

#####6. 返回类型协变(covariance of return type)

在子类中重新定义父类的方法，应该确保与父类中的函数原型完全相同，如果返回类型为父类的指针或引用，则可以修改为指向子类的指针或引用。
	
	class base{
	  public:
	    virtual base &test(){
		    //code
		}
	};
	
	class driver:public base{
	  public:
	    virtual driver &test(){
		    //code
		}
	};

#####7. 返回对象与返回引用的使用原则
	
能返回引用的时候尽量返回引用，因为引用的效率高，而且安全。例如在智能指针的解引时对operator * 的重载返回的就是引用，如果返回对象会有切割的问题存在。

当函数要返回的是函数生成的局部变量而且这个变量是对象，这时就不要试图返回引用。

#####8. pass-by-value与pass-by-reference传参方式

pass-by-value与pass-by-reference-to-const两种传参方式的共同点是，都不会对外面的实参更改。

pass-by-reference就像返回引用时的众多优点，pass-by-reference传参方式也具备这些优点：效率高，避免切割，在对象没有拷贝构造函数会拷贝构造函数有限制的时候只能用pass-by-reference传参方式(例如 传递 auto_ptr,只能用传递引用)。

但是pass-by-reference并不适用于传递内置对象，或者是STL迭代器和容器，这是要用pass-by-value方式。