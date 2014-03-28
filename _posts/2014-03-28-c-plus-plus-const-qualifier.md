---
layout: post
title: "C++中的const限定符"
description: ""
category: CPP
tags: [const ]
---
{% include JB/setup %}


使用const声明符号常量，要在声明的同时进行初始化。常量被初始化后，其值就固定了，编译器不允许再修改它。
如果常量在声明时没有初始化，它的值是不确定的，而在此之后又不能修改常量，所以常量将无法正常使用。

const可以取代#define 的一部分功能（内联函数也能取代#define的一些功能），const比#define优越的地方在于：
const能指定类型；const可以使用作用域规则；const可以用于复杂的类型。


const在函数中的应用
1. const 限定参数，禁止在函数中修改参数。
2. const 限定返回值，主要用于引用。
3. const 限定类成员函数，禁止成员函数改变类中的数据成员。

定义学生类：

    class student{
	  private:
		std::string name; //姓名
		int age;        //年龄
	  public:
		student(){//默认构造函数
		  name="null";
		  age=-1;
		}
		const std::string &get_name() const{//获取姓名
		  return name;
		}
		const int get_age() const{//获取年龄
		  return age;      
		}
		void set_name(const std::string _name){//设置姓名
		  name=_name;
		}
		void set_age(const int _age){//设置年龄
		  age=_age;
		}
         //友元函数 输出打印学生姓名和年龄
		friend  std::ostream &operator<<(std::ostream &os,const student &stu);
    };

    std::ostream &operator<<(std::ostream &os,const student &stu){
	   std::cout<<"name: "<<stu.name<<" "<<"age: "<<stu.age;
	   return os;
    }
	
    int main(){
	
	    student jim,tom;
	    jim.set_name("Jim Green");
	    jim.set_age(12);
	    tom.set_name("Tom Hanks");
	    tom.set_age(12);
        std::cout<<jim<<"\n"<<tom<<std::endl;

        const std::string &jim_name=jim.get_name();
        std::string jim_name1=jim.get_name();
        int jim_age=jim.get_age();
	    std::cout<<jim_name<<std::endl;

	    const student jim_cst=jim;
	    //jim_cst.set_name("jim_cst");
	    jim_cst.get_age();
	    return 0;
    }
	

	
1.成员函数 `void set_name(const std::string _name)` 中参数 _name被const限制，表明_name 在函数中不能被修改，任何改变_name值的行为都被禁止。
例如 在函数中t添加代码：

		_name="A String";

将报错。

2.`const std::string &get_name() const` 中的第一个const限定返回值是不能被修改的，所以返回值也只能赋值给const限定的常量，这样才能保证不被修改。代码中

	const std::string &jim_name=jim.get_name();
	
其中jim_name需要用const限定，否则会报错。这是因为，返回值为const引用，如果返回值传递给引用，则能够修改返回的对象。也就是说修改了 jim_name的值，jim中的name值会被改变。而const引用返回值是禁止这种修改的。

	std::string jim_name1=jim.get_name();
	int jim_age=jim.get_age();
	
这里只是调用了拷贝构造函数，即使修改了jim_name1也不会影响到jim中的name。

3.`const std::string &get_name() const` 中的第二个const限制get_name()不能修改类的数据成员，无论是在函数的内部还是，还是通过返回引用在外部的外部，都禁止修改。
也就是对于这个函数来说，所有的数据成员变量看作常量，不能被修改。如果把这个函数定义为：
 	
	std::string &get_name() const{
       return name;
    }
	
这将导致错误，因为通过传递引用的方式可以对name进行修改，而后面的const是禁止修改。所以编译器将不会通过这种定义的。

4.const对类的成员函数进行约束，禁止了成员函数对成员变量的修改，但是类的const对象可以调用这些函数。
	
	const student jim_cst=jim;
	//jim_cst.set_name("jim_cst");
	jim_cst.get_age();
	
5.应尽可能使用const。可以保护数据不被修改。而在引用类型的函数参数中使用const 能够打破对参数的左值的要求。


其他：

1. 去除const 限制使用强制类型转换 `const_cast< type >(expression)`

		void set_default(student  &stu){
		    stu.set_name("null");
		    stu.set_age(-1);
		}
		set_default(const_cast<student &>(jim_cst));

	jim_cst中的内容将被改变。

2. 使用mutable 修饰的类成员变量，可以被const限定的成员函数修改。例如
	
		mutable string name;
	
	如果不使用mutable 也可以通过对 this 进行类型转换，去除常量性，从而进行修改。例如：
	为student类加入成员函数void set_default() const：
	
		void student::set_default() const{
		    student * const fake_this=const_cast<student *const>(this);
		    fake_this->name="null";
		    fake_this->age=-1;
		}

	因此可以推测const 对类成员函数的限制，实质是在这个函数作用域内对this指针所指向的对象（也就是对象自身）进行const限制（pointer -to- const）。
	而在代码中的fake_this 则是个pointer-to-non-const。另外 this应该也被限制（可能是常量），不能指向其他的地址。



