---
title: " C++设计模式-模板方法模式\t\t"
tags:
  - c++
  - 设计模式
url: 790.html
id: 790
categories:
  - C/C++
date: 2017-12-23 10:53:15
---

介绍
--

由抽象模板和具体模板构成，抽象模板分为两类

*   基本方法：基本操作，有子类实现的方法，在模板方法中被调用
*   模板方法：可以由一个或多个，是一个具体的方法，也就是一个框架，实现对基本方法的掉队，完成固定的逻辑。为了防止恶意的操作一般模板方法都加上final关键字不允许被覆写

具体模板实现抽象模板，但只覆写基本方法 优点：封装不变部分，扩展可变部分；提取公共部分代码；便于维护、行为由父类控制，子类实现 缺点：抽象类纯虚函数；子类执行的结果影响了父类的结果 应用：多个子类有公有的方法，并且逻辑基本相同时；重要、复杂的算法，可以把核心算法设计为模板方法；重构时把相同的代码抽取到父类中。

范例
--

#ifndef ABSTRACT\_CLASS\_H
#define ABSTRACT\_CLASS\_H
class AbstractClass {
//模板方法
public:
    virtual void templateMethod() final{
        doAnything();
        doSomething();
    }
//基本方法
protected:
    virtual void doSomething() { };
    virtual void doAnything() { };
};
#endif // ABSTRACT\_CLASS\_H
#ifndef CONCRETE\_CLASS\_H
#define CONCRETE\_CLASS\_H
#include "abstract_class.h"
#include <QDebug>
class ConcreteClass1 : public AbstractClass{
//实现基本方法
protected:
    virtual void doAnything() override {qDebug()<<"ConcreteClass1";}
    virtual void doSomething() override { }
};
class ConcreteClass2 : public AbstractClass{
//实现基本方法
protected:
    virtual void doAnything() override {qDebug()<<"ConcreteClass2";}
    virtual void doSomething() override { }
};
#endif // CONCRETE\_CLASS\_H
#include "concrete_class.h"
int main(int argc, char *argv\[\]) {
    AbstractClass *t1 = new ConcreteClass1;
    AbstractClass *t2 = new ConcreteClass2;
    t1->templateMethod();
    t2->templateMethod();
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)