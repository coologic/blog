---
title: " C++设计模式-原型模式\t\t"
tags:
  - c++
  - 设计模式
url: 799.html
id: 799
categories:
  - C/C++
date: 2017-12-23 15:32:17
---

介绍
--

原型模式的核心是一个clone方法，通过该方法进行对象的拷贝 。 优点： 性能优良 ； 逃避构造函数的约束 缺点：每个类都要重写有clone方法，对于以后的类需要全面的考虑所有成员的拷贝 应用： 资源优化场景 、 性能和安全要求的场景 、 一个对象多个修改者的场景 需要有抽象原型和具体原型，抽象原型只需要有虚析构函数和纯虚方法clone即可，有具体原型实现clone方法

> 注意clone内的操作要深拷贝，对于指针等成员变量不能只copy指针

范例
--

#ifndef PROTOTYPE_H
#define PROTOTYPE_H
class Prototype {
public:
    virtual ~Prototype() { }
    virtual Prototype* Clone() = 0;
};
#endif // PROTOTYPE_H
#ifndef CONCRETEPROTOTYPE_H
#define CONCRETEPROTOTYPE_H
#include "prototype.h"
class ConcretePrototype : public Prototype {
public:
    ConcretePrototype() {b = new int;}
    virtual ~ConcretePrototype() {delete b;}
    virtual Prototype* Clone() override  {
        auto r = new ConcretePrototype;
        r->a = this->a;
        //b在构造函数会new，不需要拷贝
        return r;
    }
private:
    int a;
    int *b;
};
#endif // CONCRETEPROTOTYPE_H
#include "concreteprototype.h"
int main(int argc, char *argv\[\]) {
    Prototype *p = new ConcretePrototype();
    Prototype *p2 = p->Clone();
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)