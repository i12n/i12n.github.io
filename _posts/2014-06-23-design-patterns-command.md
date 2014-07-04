---
layout: post
title: "设计模式：命令模式"
category: Design Patterns
tags: [ Behavioral Patterns ]
---
{% include JB/setup %}
意图：将一个请求封装为一个对象，从而可以使用请求对客户进行参数化；可对请求排队或记录请求日志，以及支持可撤销操作。

动机：有时必须向某个对象提交请求，但不知道关于被请求的操作或者请求的接受者的任何信息。命令模式通过将请求本身变成一个对象，可向未指定的应用对象提出请求。

适用性：要抽象出待执行的动作以参数化某对象，可以使用回调函数表达这种参数化机制，即先注册后调用；在不同的时刻指定排列和执行请求；支持撤销和修改日志。
命令模式比较适合请求-响应这一功能，命令的接受者和执行者之间的耦合度比较低。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014062301.png" />

Command:声明执行操作的接口

	class Command{
	    public:
	        virtual void Execute()=0;
	};




Receiver：知道如何实施与执行一个请求的相关操作。

	class Receiver{
	    public:
	        void Action(){
	            std::cout<<"Action"<<std::endl;
	        }
	};

ConcreteCommand：将接收者对象绑定一个动作；调用接受者的相关操作。
	
	class ConcreteCommand:public Command{
	    public:
	        ConcreteCommand(Receiver *_receiver):receiver(_receiver){
	        }
	        void Execute(){
	        receiver->Action();
	    }
	    private:
	        Receiver *receiver;
    };

Invoker：要求该命令执行这个请求

	class Invoker{
	    public:
	    void SetCommand(Command *_cmd){
	        this->cmd=_cmd;
	    }
	    void RunCommand(){
	        this->cmd->Execute();
	    }
	    private:
	        Command *cmd;
};



	Receiver *receiver=new Receiver();
	Command *command=new ConcreteCommand(receiver);
	Invoker *invoker=new Invoker();
	
	invoker->SetCommand(command);
	invoker->RunCommand();

