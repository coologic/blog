---
title: " C++设计模式-装饰模式\t\t"
tags:
  - c++
  - 设计模式
url: 815.html
id: 815
categories:
  - C/C++
date: 2017-12-24 16:27:21
---

介绍
--

*   Component抽象构件 Component是一个接口或者是抽象类， 就是定义我们最核心的对象， 也就是最原始的对象， 如上面的成绩单。
*   ConcreteComponent 具体构件 ConcreteComponent是最核心、 最原始、 最基本的接口或抽象类的实现， 你要装饰的就是它。
*   Decorator装饰角色 在它的属性里必然有一个private变量指向Component抽象构件。若只有一个装饰类，则可以没有抽象装饰角色，直接实现具体的装饰角色即可。
*   ConcreteDecorator具体装饰角色 要把最核心的、 最原始的、 最基本的东西装饰成其他东西。

原始方法和装饰方法的执行顺序在具体的装饰类是固定的， 可以通过方法重载实现多种执行顺序。 优点： 装饰类和被装饰类可以独立发展， 而不会相互耦合； 装饰模式是继承关系的一个替代方案； 装饰模式可以动态地扩展一个实现类的功能 缺点： 多层的装饰是比较复杂的， 尽量减少装饰类的数量， 以便降低系统的复杂度 应用：

*   需要扩展一个类的功能， 或给一个类增加附加功能。
*   需要动态地给一个对象增加功能， 这些功能可以再动态地撤销。
*   需要为一批的兄弟类进行改装或加装功能， 当然是首选装饰模式。

范例
--

抽象构件

#ifndef COMPONENT_H
#define COMPONENT_H
class Component {
//抽象的构件
public:
    virtual void operate() = 0;
};
#endif // COMPONENT_H

具体构件

#ifndef CONCRETECOMPONENT_H
#define CONCRETECOMPONENT_H
#include "component.h"
#include <QDebug>
class ConcreteComponent : public Component {
public:
    void operate() override {
        qDebug()<<"ConcreteComponent do Something";
    }
};
#endif // CONCRETECOMPONENT_H

抽象装饰角色

#ifndef DECORATOR_H
#define DECORATOR_H
#include "component.h"
class Decorator : public Component {
//通过构造函数传递被修饰者
public:
    Decorator(Component *component){
        component_ = component;
    }
    //委托给被修饰者执行
    void operate() override {
        component_->operate();
    }
private:
    Component *component_ = nullptr;
};
#endif // DECORATOR_H

具体装饰角色

#ifndef CONCRETEDECORATOR_H
#define CONCRETEDECORATOR_H
#include <QDebug>
#include "decorator.h"
class ConcreteDecorator1 : public Decorator {
public:
    ConcreteDecorator1(Component *component)
        : Decorator(component) {
    }
    //重写父类的Operation方法
    void operate() override {
        method1();
        Decorator::operate();
    }
private:
    //定义自己的修饰方法
    void method1(){
        qDebug()<<"method1 of Decorator1";
    }
};
class ConcreteDecorator2 : public Decorator {
public:
    ConcreteDecorator2(Component *component)
        : Decorator(component) {

    }
    //重写父类的Operation方法
    void operate() override {
        method1();
        Decorator::operate();
    }
private:
    //定义自己的修饰方法
    void method1(){
        qDebug()<<"method1 of Decorator2";
    }
};
#endif // CONCRETEDECORATOR_H

main

#include "concretecomponent.h"
#include "concretedecorator.h"
int main(int argc, char *argv\[\]) {
    Component *component = new ConcreteComponent();
    //第一次修饰
    component = new ConcreteDecorator1(component);
    //第二次修饰
    component = new ConcreteDecorator2(component);
    //修饰后运行
    component->operate();
}

例子又忘了delete。。。每个例子到最后main都忘了delete 结果

method1 of Decorator2
method1 of Decorator1
ConcreteComponent do Something

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)