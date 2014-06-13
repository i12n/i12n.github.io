---
layout: post
title: "设计模式：组合模式"
description: ""
category: Design Patterns
tags: [ structural patterns ]
---
{% include JB/setup %}
意图：将对象组合成树形结构以表示“部分-整体”的层次结构。Composite使得用户对单个对象和组合对象具有一致性。

适用性：表示对象的部分-整体的层次结构，忽略单个对象与组合对象的不同。

<!-- more -->

<img  src="{{ site.url }}/assets/images/2014061201.png" />



Component: 为组合中的对象声明接口；在适当情况下，实现所有类共有接口的缺省行为；声明一个接口用于访问和管理Component的子组件。

	class Component{
	  public:
	    virtual void add(Component* component)=0;
	    virtual void remove(Component* component)=0;
	    virtual void operation()=0;
	    virtual void getChild()=0;
	};








Leaf: 在组合中定义元对象的行为。

	class Leaf:public Component{
	  public:
	    Leaf(const char *_name):name(_name){
	    }
	    void add(Component* _component){	
	    }
	    void remove(Component* _component){	
	    }
	    void operation(){
	        std::cout<<name<<std::endl;
	    }
	    void getChild(){	
		}
	  private:
	    std::string name;
	};




Composite：定义子部件的那些部件的行为；存储子部件；在Component接口中实现与子部件有关的操作。

	class Composite:public Component{
	  public:
	    Composite(const char *_name):name(_name){
	    }
	    void add(Component* _component){
	        component.push_back(_component);
	    }
	    void remove(Component* _component){
	        component.remove(_component);
	    }
	    void operation(){
	        std::cout<<name<<std::endl;
	    }
	    void getChild(){
	        for(std::list<Component *>::iterator it=component.begin();it!=component.end();it++){
	            (*it)->operation();
	            (*it)->getChild();
	        }
	    }
	  private:
	    std::list<Component *>  component;
	    std::string name;	
	};
	
	
	
	
	Component *root= new Composite("root");
	Component *c1= new Composite("c1");
	root->add(c1);
	Component *d1=new Composite("d1");
	Component *d2=new Composite("d2");
	c1->add(d1);
	c1->add(d2);
	Component *leaf1=new Leaf("leaf1");
	c1->add(leaf1);
	root->operation();
	root->getChild();