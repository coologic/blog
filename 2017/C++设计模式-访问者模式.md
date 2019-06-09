---
title: " C++设计模式-访问者模式\t\t"
tags:
  - c++
  - 设计模式
url: 838.html
id: 838
categories:
  - C/C++
date: 2017-12-27 22:01:02
---

介绍
--

*   Visitor——抽象访问者 抽象类或者接口， 声明访问者可以访问哪些元素， 具体到程序中就是visit方法的参数定义哪些对象是可以被访问的。
*   ConcreteVisitor——具体访问者 它影响访问者访问到一个类后该怎么干， 要做什么事情。
*   Element——抽象元素 接口或者抽象类， 声明接受哪一类访问者访问， 程序上是通过accept方法中的参数来定义的。
*   ConcreteElement——具体元素 实现accept方法， 通常是visitor.visit(this)， 基本上都形成了一种模式了。
*   ObjectStruture——结构对象 元素产生者， 一般容纳在多个不同类、 不同接口的容器， 如List、 Set、 Map等， 在项目中， 一般很少抽象出这个角色

优点：符合单一职责原则；优秀的扩展性；灵活性非常高 缺点：具体元素对访问者公布细节；具体元素变更比较困难； 违背了依赖倒置转原则 应用：

*   一个对象结构包含很多类对象， 它们有不同的接口， 而你想对这些对象实施一些依赖于其具体类的操作，也就说是用迭代器模式已经不能胜任的情景
*   需要对一个对象结构中的对象进行很多不同并且不相关的操作， 而你想避免让这些操作“污染”这些对象的类

范例
--

#ifndef VISITOR_H
#define VISITOR_H
#include "ivisitor.h"
class Visitor : public IVisitor {
public:
    //访问el1元素
    void visit1(Element *el1) override {
        el1->doSomething();
    }
    //访问el2元素
    void visit2(Element *el2) override {
        el2->doSomething();
    }
};
#endif // VISITOR_H
#ifndef OBJECTSTRUTURE_H
#define OBJECTSTRUTURE_H
#include "concreteelement.h"
class ObjectStruture {
//对象生成器， 这里通过一个工厂方法模式模拟
public:
    static Element *createElement() {
        if(true)//判断条件
            return new ConcreteElement1();
        return new ConcreteElement2();
    }
};
#endif // OBJECTSTRUTURE_H
#ifndef IVISITOR_H
#define IVISITOR_H
class Element;
class IVisitor {
//可以访问哪些对象
public:
    virtual void visit1(Element *el1) = 0;
    virtual void visit2(Element *el2) = 0;
};
#endif // IVISITOR_H
#ifndef ELEMENT_H
#define ELEMENT_H
class IVisitor;
class Element {
public:
    virtual ~Element() { }
    virtual void doSomething() = 0;
//允许谁来访问
    virtual void accept(IVisitor *visitor) = 0;
};
#endif // ELEMENT_H
#ifndef CONCRETEELEMENT_H
#define CONCRETEELEMENT_H
#include "element.h"
#include "ivisitor.h"
class ConcreteElement1 : public Element {
//完善业务逻辑
public:
    void doSomething() override { }
    //允许那个访问者访问
    void accept(IVisitor *visitor) override {
        visitor->visit1(this);
    }
};
class ConcreteElement2 : public Element {
//完善业务逻辑
public:
    void doSomething() override { }
    //允许那个访问者访问
    void accept(IVisitor *visitor) override {
        visitor->visit2(this);
    }
};
#endif // CONCRETEELEMENT_H
#include "element.h"
#include "objectstruture.h"
#include "visitor.h"
int main(int argc, char *argv\[\]) {
    for(int i=0;i<10;i++){
    //获得元素对象
        Element *el = ObjectStruture::createElement();
    //接受访问者访问
        el->accept(new Visitor());
        delete el;
    }
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)