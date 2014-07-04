---
layout: post
title: "设计模式：迭代器模式"
category: Design Patterns
tags: [ Behavioral Patterns ]
---
{% include JB/setup %}

意图：提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露该对象的内部表示。

动机：一个聚合对象例如列表(List)，应该可以提供一种方法，让别人来访问他的元素，而不需要暴露它的内部结构。将对聚合对象的访问和遍历分离出来放入一个迭代器，迭代器对象负责跟踪当前的元素 。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014063001.png" />






Aggregate：聚合定义创建相应迭代器对象的接口

	template <typename item>
	class Aggregate{
	    public:
	        virtual Iterator<item> * CreateIterator()=0;
	};
	
Iterator：定义访问和遍历元素的接口
	
	template <typename item>
	class Iterator{
	    public:
	        virtual void First()=0;
	        virtual void Next()=0;
	        virtual bool IsDone() const =0;
	        virtual item* CurrentItem() const =0;
	};

	
ConcreteIterator：实现具体迭代器接口；聚合遍历时跟踪当前位置。

	template <typename item>
	class ConcreteIterator:public Iterator<item>{
	    public:
	        ConcreteIterator(ConcreteAggregate<item> *concreteAggreate):_concreteAggreate(concreteAggreate),_current(0){
	        }
	        void First(){
	            _current=0;
	        }
	        void Next(){
	            ++_current;
	        }
	        bool IsDone() const{
	            if(_concreteAggreate->GetLength()<=_current) return false;
	            else return true;
	        }
	        item* CurrentItem() const{
	            return _concreteAggreate->GetElement(_current);
	        }
	    private:
	        ConcreteAggregate<item> * _concreteAggreate;
	        int _current;
	};



ConcreteIterator：该操作返回一个ConcreteIterator适当的实例。

	template <typename item>
	class ConcreteAggregate:public Aggregate<item>{
	    public:
	        ConcreteAggregate(int length):_length(length){
	            array=new item[_length]();
	            array[5]=5;
			}
	        Iterator<item> *CreateIterator() {
	            return new ConcreteIterator<item>(this);
	        }
	        int GetLength(){
	            return _length;
	        }
	        item *GetElement(int i){
	            return &array[i];
	        }
			
	    private:
	        int _length;
	        item *array;
	};


	Aggregate <int> * aggr =new ConcreteAggregate<int>(10);  
	Iterator<int> *it=aggr->CreateIterator();
	for(it->First();it->IsDone();it->Next()){
		std::cout<<*(it->CurrentItem())<<std::endl;
	}

