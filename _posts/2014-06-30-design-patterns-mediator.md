---
layout: post
title: "设计模式：中介者模式"
description: ""
category: Design Patterns
tags: [ Behavioral Patterns ]
---
{% include JB/setup %}

意图：用一个中介对象来封装一系列的对象交互。中介者使各个对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变他们之间的交互。

动机：将一个系统分割为许多对象通常可以增强可复用性，但是对象间相互连接的激增又会降低其可复用性。大量的互相连接使得对象似乎不太可能没有其他对象的支持下工作——系统表现为一个不可分割的整体。结果不得不定义很多子类以定制系统接口。可以将集体行为封装为一个单独的中介者，来避免这个问题。中介者负责控制和协调一组对象的交互中介者充当一个中介以使组中的对象不再显示引用。这些对象仅知道中介者，从而减少了相互连接的数目。
适用性：一组对象已经定义了良好但是复杂的方式进行通信；想定制一个分布在多个类中的行为，而又不想生成太多的子类；一个对象引用其他很多对象且直接与这些对象通信，导致难以复用该对象。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014062303.png" />

Mediator:中介者定义一个接口与其他Colleague对象进行通信

	class Mediator{
	    public:
	        virtual void notice(Colleague *colleague)=0; 
	};

Colleague：每一个同事类知道他的中介者对象

	class Colleague{
	    public:
	        virtual void send(){
	            _mediator->notice(this);
	        }
	        virtual std::string get(){
	            return "NULL";
	        }
	        Colleague(Mediator *mediator ):_mediator(mediator){
	        }
	    private:
	        Mediator *_mediator;
	};

ConcreteMediator：具体中介者通过协调各个同事对象实现协作行为；了解并维护它的各个同事;每一个同事类对象与其他同事通信时候，与它的中介者通信,同事向一个中介者对象发送和接受请求;
中介者在各同事间适当的转发请求以实现协作行为

	class ConcreteMediator:public Mediator{
	    public:
	        void notice(Colleague *colleague){
	            for(std::vector<Colleague *>::iterator it=_colleague.begin();it!=_colleague.end();it++){
	                if(*it!=colleague){
	                    std::cout<<colleague->get()<<" send msg to "<<(*it)->get()<<std::endl;
	                }
	            }
	        }
	        void regist(Colleague *colleague){
	            _colleague.push_back(colleague);
	        }
	    private:
	        std::vector<Colleague *> _colleague;
	};



ConcreteColleague 具体同事类

	class  ConcreteColleagueA:public Colleague{
	    public:
	        ConcreteColleagueA(Mediator *mediator,const char *name ):Colleague(mediator),_name(name){
	        }
	        std::string get(){
	            return _name;
	        }
	    private:
	        std::string _name;
	};


	class  ConcreteColleagueB:public Colleague{
	    public:
	        ConcreteColleagueB(Mediator *mediator,const char *name ):Colleague(mediator),_name(name){
	        }
	        std::string get(){
	            return _name;
	        }
	    private:
	        std::string _name;
			
	};

	
	ConcreteMediator *mediator=new ConcreteMediator();
	ConcreteColleagueA  *colleague_a=new ConcreteColleagueA(mediator,"Jim");
	ConcreteColleagueB  *colleague_b=new ConcreteColleagueB(mediator,"Tom");
	mediator->regist(colleague_a);	
	mediator->regist(colleague_b);
	colleague_a->send();
	colleague_b->send();
