---
layout: post
title: "设计模式：观察者模式"
description: ""
category: Design Patterns
tags: [ Behavioral Patterns ]
---
{% include JB/setup %}

意图：定义对象间一种一对多的一依赖关系，当一个对象的状态发生改变时，所有依赖它的对象都得到通知并被自动更新。

动机：为了维护对象之间的一致性，一个目标有任意数量的依赖他的观察者，一旦目标状态发生改变，所有观察者都能得到通知。作为这个通知的相应，每个观察者都将查询目标以使其状态与目标状态同步。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014063002.png" />

Observer：为那些在目标发生改变时，需要得到通知的对象定义一个更新接口。

	class Observer{
	    public:
	        virtual void update(Subject *subject)=0;
	};
	
Subject：目标知道观察者，提供注册和删除观察者对象接口

	class Subject{
	    public:
	        void attach(Observer *o){
	            observers.push_back(o);
	        }
	        void detach(Observer *o){
	            for(std::vector< Observer *>::iterator it=observers.begin();it!=observers.end();it++){
	                if(*it==o){
	                    observers.erase(it);std::cout<<"erase"<<std::endl;
	                    break;
	                }
	            }
	        }
	        void notify(){
	            for(std::vector< Observer *>::iterator it=observers.begin();it!=observers.end();it++){
	                (*it)->update(this);
	            }
	        }
	    private:
	        std::vector< Observer *> observers;
	};

ConcreteSubject：将有关状态存入各个ConcreteObserver对象；当它的状态发生变化，向它的观察者发送通知。

	class ConcreteSubject:public Subject{
	    public:
	        void setState(int st){
	            state=st;
	            std::cout<<"ConcreteSubject state: "<<state<<std::endl;
	            notify();
	        }
	        int getState(){
	            return state;
	        }
	    private:
	        int state;
	};

ConcreteObserver：维护一个指向ConcreteSubject对象引用，存储有关状态，这些状态与目标状态保持一致性；实现Observer的更新接口以使自身状态与目标状态保持一致。

	class ConcreteObserver:public Observer{
	    public:
	        void update(Subject *sub){
	            if(sub==subject){
	                state=subject->getState()%2;
	                std::cout<<"ConcreteObserver state: "<<state<<std::endl;
	            }	
	        }
	        ConcreteObserver(ConcreteSubject *sub):subject(sub){
	            subject->attach(this);
	        }
	        ~ConcreteObserver(){
	            subject->detach(this);
	        }
	    private:
	        int state;
	        ConcreteSubject *subject;
	};

 
	ConcreteSubject *subject=new ConcreteSubject();
	subject->setState(0);
	ConcreteObserver *observer=new ConcreteObserver(subject);
	subject->setState(1);
	subject->setState(2);
	delete observer;
	subject->setState(3);
	subject->setState(4);
 
