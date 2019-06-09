---
title: " C++设计模式-观察者模式\t\t"
tags:
  - c++
  - 设计模式
url: 829.html
id: 829
categories:
  - C/C++
date: 2017-12-27 20:22:08
---

介绍
--

*   Subject被观察者 定义被观察者必须实现的职责，它必须能够动态地增加、取消观察者。它一般是抽象类或者是实现类，仅仅完成作为被观察者必须实现的职责：管理观察者并通知观察者。
*   Observer观察者 观察者接收到消息后，即进行update（更新方法）操作，对接收到的信息进行处理。
*   ConcreteSubject具体的被观察者 定义被观察者自己的业务逻辑，同时定义对哪些事件进行通知。
*   ConcreteObserver具体的观察者 每个观察在接收到消息后的处理反应是不同，各个观察者有自己的处理逻辑。

优点：观察者和被观察者之间是抽象耦合；建立一套触发机制 应用：关联行为场景。需要注意的是，关联行为是可拆分的，而不是组合关系；事件多级触发场景；跨系统的消息交换场景，如消息队列的处理机制 注意：广播连，消息最多转发一次（传递两次）。

> 和责任链模式的最大区别就是观察者广播链在传播的过程中消息是随时更改的， 它是由相邻的两个节点协商的消息结构； 而责任链模式在消息传递过程中基本上保持消息不可变， 如果要改变， 也只是在原有的消息上进行修正。

范例
--

#ifndef SUBJECT_H
#define SUBJECT_H
#include "observer.h"
#include <QList>
class Subject {
public:
    //增加一个观察者
    void addObserver(Observer *o) {
        obsVector.append(o);
    }
    //删除一个观察者
    void delObserver(Observer *o) {
        obsVector.removeOne(o);
    }
    //通知所有观察者
    void notifyObservers() {
        for(Observer *o : obsVector)
            o->update();
    }
private:
    //定义一个观察者数组
    QList<Observer *> obsVector;
};
#endif // SUBJECT_H
#ifndef OBSERVER_H
#define OBSERVER_H
class Observer {
//更新方法
public:
    virtual ~Observer() { }
    virtual void update() = 0;
};
#endif // OBSERVER_H
#ifndef CONCRETESUBJECT_H
#define CONCRETESUBJECT_H
#include "subject.h"
class ConcreteSubject : public Subject {
//具体的业务
public:
    void doSomething() {
        notifyObservers();
    }
};
#endif // CONCRETESUBJECT_H
#ifndef CONCRETEOBSERVER_H
#define CONCRETEOBSERVER_H
#include <QDebug>
#include "observer.h"
class ConcreteObserver : public Observer {
//实现更新方法
public:
    void update() override {
        qDebug()<<"get message";
    }
};
#endif // CONCRETEOBSERVER_H
#include "concreteobserver.h"
#include "concretesubject.h"
int main(int argc, char *argv\[\]) {
    ConcreteSubject *subject = new ConcreteSubject();
    //定义一个观察者
    Observer *obs= new ConcreteObserver();
    //观察者观察被观察者
    subject->addObserver(obs);
    //观察者开始活动了
    subject->doSomething();
    delete subject;
    delete obs;
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)