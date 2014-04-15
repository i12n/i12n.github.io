---
layout: post
title: "C++中的操作符重载"
description: ""
category: CPP
tags: [ operators ]
---
{% include JB/setup %}

#####算术运算符的重载

算术运算符的重载要注意返回值的类型，`+ - * /`计算结果要以 pass-value 方式传递一个变量做返回值，这样可以对多个变量的连续运算进行支持。

对于数乘形式的运算 `A*k` 可以作为类的成员函数进行重载，而 `k*A` 无法作为类的成员函数，可以以友元函数的形式进行重载。以满足对 `A*k` 和 `k*A` 的支持。

对于前置 `++(--)` 也要以 pass-value 方式返回一个 const value，这样禁止 i++++ 通过编译。
对于后置 `++(--)` 要返回一个 reference-to-non-const ，对 ++++i 提供了支持。
后置 `++(--)` 的效率要比前置 `++(--)` 效率高。后置 `++(--)` 需要用一个 int 做参数来区别前置 `++(--)`。


	class vector{
      private:
        float x;
        float y;
      public:
        vector(float x,float y):x(x),y(y){};
	    vector operator+(const vector & v) const{//+
	        return vector(this->x+v.x,this->y+v.y);
	    }
	    vector operator*(float k) const{ //数乘，V*k
			return vector(this->x*k,this->y*k);
	    }
	    vector &operator++(){//前置++ 
	        ++x;
	        ++y;
	        return *this;
		}
	    const vector operator++(int){//后置++ 
	        vector tmp=(*this);
	        ++*this;
	        return tmp; 
		}
	    friend vector operator *(float k,const vector &v);//数乘，k*V
	}
	
	vector operator*(float k,const vector &v){
	    return v*k;
	}
	


</br>	
#####输入输出操作符 `operator>>` 和 `operator<<`

为了支持输入输出运算符，也需要这两个操作符进行重载。

	class vector{
      private:
        float x;
        float y;
      public:
        vector(float x,float y):x(x),y(y){};
	    std::ostream & operator<<(std::ostream &os){
	        os<<"("<<v.x<<","<<v.y<<")";
		}
	}
    
	vector v(1,2);
    v<<(v<<cout); //相当于 cout 在 operator<< 中做参数 
	
为了符合 `cout<<v` 的形式，可以以友元函数的方式对 `operator<< (operator>>)` 重载。
	
	class vector{
      private:
        float x;
        float y;
      public:
        vector(float x,float y):x(x),y(y){};
	    friend std::ostream & operator <<(std::ostream & os,const vector &v)
	}
	
	std::ostream & operator <<(std::ostream & os,const vector &v){
        os<<v.get_x()<<","<<v.get_y()<<endl;
        return os;
	}
	vector v(1,2);
    cout<<v; //vector 相当于 operator<< 的参数
	
reference-to-const做参数对常量支持的同时，也支持了非左值的类型 `cout<<v+v`。
为了支持 `cout<<v<<v` ，需要返回 ostream 的引用，除了效率的原因，还因为 ostream 不提供复制构造函数。
	
</br>
#####数组索引操作符 `operator[]` 重载
 
`operator[]` 提供以下两种重载形式：

	reference operator[] (size_type n);
	const_reference operator[] (size_type n) const;

第一种形式可以通过索引对数组元素进行修改，第二种是通过返回的常量引用可以获取数组的内部元素。

	template <typename T,int row,int col>
	class Array2d {
	  class Array1d{ //nested class
	    public:
	        const T &operator[](int i) const{	//overload operator[]
	            return    array[i];   
	        }
	        T &operator[](int i){	//overload operator[]
	            return    array[i];   
	        }
	    private:
	        T array[col];             
	  };
      public:
	    const Array1d &operator[](int i) const{	//overload operator[]
	        return    array[i];   
	    }
	    Array1d &operator[](int i){	//overload operator[]
	        return    array[i];   
	    }
	  private:
	    Array1d    array[row];     
	};
	
	Array2d <int , 3, 7> array;
	array[2][2]=2;
	std::cout<<array[2][2]<<std::endl; //print 2

在上面的代码中，以 nested class 的方法，并且对 `operator[]` 进行重载，实现对二维数组中的元素可以用`array[][]` 方式进行访问。
	
</br>
#####智能指针中Dereferencing Operators

在智能指针的设计中需要对其内部的 raw pointer 进行解引，智能指针的使用更像指针，主要涉及到 `operator*` 和 `operator->` 两个操作符。
这两个操作符重载的形式如下:

	T& operator*() const;
	T* operator->() const;
	
在第一个重载中，返回 reference 的主要原因是为了保证多态性。，以 pass-value 返回可能会引起切割。
而且这两个重载在类内被 const 限制，主要是防止 raw pointer 被修改。

	template<typename T>
	class PTR{
	  public:
	    PTR(T *p):ptr(p){}
	    T &operator*() const{
	        return *ptr;
	    }
        T *operator->()const{
	        return ptr;
	    }
	  private:
	    T *ptr;      //raw pointer
	};

	PTR<std::string> str(new string("i'm here"));
	std::cout<<"length "<<(*str).size()<<std::endl;	//print length 8
	std::cout<<"length "<<str->size()<<std::endl;	//print length 8

