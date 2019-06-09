---
title: " C++设计模式-代理模式\t\t"
tags:
  - c++
  - 设计模式
url: 802.html
id: 802
categories:
  - C/C++
date: 2017-12-23 15:36:51
---

介绍
--

通过代理模式可以在原有业务逻辑外增加一定的约束，比如排序、范围限制等等，无论具体主体还是代理主体都实现抽象主题

*   Subject抽象主题角色 抽象主题类可以是抽象类也可以是接口， 是一个最普通的业务类型定义， 无特殊要求。
*   RealSubject具体主题角色 也叫做被委托角色、 被代理角色。业务逻辑的具体执行者。
*   Proxy代理主题角色 也叫做委托类、 代理类。 它负责对真实角色的应用， 把所有抽象主题类定义的方法限制委托给真实主题角色实现， 并且在真实主题角色处理完毕前后做预处理和善后处理工作。

优点： 职责清晰；高扩展性；智能化 缺点：引入了另一个抽象层；影响速度

范例
--

#ifndef PROXY_H
#define PROXY_H
#include <QDebug>
#include "subject.h"
#include "realsubject.h"
class Proxy : public Subject {
public:
    //支持自定义具体场景
    Proxy(Subject *subject = nullptr){
        if(subject != nullptr)
            subject_ = subject;
        else
            subject_ = new RealSubject;
    }
    //在原有逻辑外代理对num做了过滤，筛掉了<=100的值
    void request(int num) {
        if(num > 100)
            subject_->request(num);
        else
            qDebug()<<"not good num";
    }
private:
    Subject *subject_ = nullptr;
};
#endif // PROXY_H
#ifndef REALSUBJECT_H
#define REALSUBJECT_H
#include "subject.h"
#include <QDebug>
class RealSubject : public Subject {
public:
    void request(int num) override {qDebug()<<"hi";}
};
#endif // REALSUBJECT_H
#ifndef SUBJECT_H
#define SUBJECT_H
class Subject {
public:
    virtual ~Subject() { }
    virtual void request(int num) = 0;
};
#endif // SUBJECT_H
#include "realsubject.h"
#include "proxy.h"
int main(int argc, char *argv\[\]) {
    Proxy p;
    p.request(10);
    p.request(110);
}

推广
--

### 普通代理

普通代理， 它的要求就是客户端只能访问代理角色， 而不能访问真实角色.

### 强制代理

一般的思维都是通过代理找到真实的角色， 但是强制代理却是要“强制”， 你必须通过真实角色查找到代理角色， 否则不能访问。

### 动态代理

实现阶段不用关心代理谁，而在运行阶段指定代理哪一个对象。AOP(Aspect Oriented Programming)面向横切面编程。核心就是动态代理。 AOP编程没有使用什么新的技术， 但是它对我们的设计、 编码有非常大的影响， 对于日志、 事务、 权限等都可以在系统设计阶段不用考虑， 而在设计后通过AOP的方式切过去。 源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)