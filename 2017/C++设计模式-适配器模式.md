---
title: " C++设计模式-适配器模式\t\t"
tags:
  - c++
  - 设计模式
url: 821.html
id: 821
categories:
  - C/C++
date: 2017-12-24 17:29:22
---

介绍
--

适配器模式就是把一个接口或类转换成其他的接口或类。

*   Target目标角色 该角色定义把其他类转换为何种接口，也就是我们的期望接口。
*   Adaptee源角色 它是已经存在的、 运行良好的类或对象。
*   Adapter适配器角色 适配器模式的核心角色，其他两个角色都是已经存在的角色，而适配器角色是需要新建立的，它的职责非常简单：把源角色转换为目标角色。

优点： 适配器模式可以让两个没有任何关系的类在一起运行；增加了类的透明性；提高了类的复用度；灵活性非常好 应用： 有动机修改一个已经投产中的接口时

> 适配器模式最好在详细设计阶段不要考虑它， 它不是为了解决还处在开发阶段的问题，而是解决正在服役的项目问题。 适配器模式是一个补偿模式， 或者说是一个“补救”模式，通常用来解决接口不相容的问题

范例
--

#ifndef TARGET_H
#define TARGET_H
class Target {
//目标角色有自己的方法
public:
    virtual void request() = 0;
};
#endif // TARGET_H
#ifndef ADAPTER_H
#define ADAPTER_H
#include "adaptee.h"
#include "target.h"
class Adapter : public Adaptee, public Target {
public:
    void request() override {
        doSomething();
    }
};
#endif // ADAPTER_H
#ifndef CONCRETETARGET_H
#define CONCRETETARGET_H
#include <QDebug>
#include "target.h"
class ConcreteTarget : public Target {
public:
    void request() override {
        qDebug()<<"if you need any help,pls call me!";
    }
};
#endif // CONCRETETARGET_H
#ifndef ADAPTEE_H
#define ADAPTEE_H
#include <QDebug>
class Adaptee {
//原有的业务逻辑
public:
    void doSomething(){
        qDebug()<<"I'm kind of busy,leave me alone,pls!";
    }
};
#endif // ADAPTEE_H
#include "adapter.h"
#include "concretetarget.h"
int main(int argc, char *argv\[\]) {
    Target *target = new ConcreteTarget();
    target->request();
    //现在增加了适配器角色后的业务逻辑
    Target *target2 = new Adapter();
    target2->request();
}

if you need any help,pls call me! I'm kind of busy,leave me alone,pls! 源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)