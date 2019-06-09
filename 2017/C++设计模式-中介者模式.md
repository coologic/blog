---
title: " C++设计模式-中介者模式\t\t"
tags:
  - c++
  - 设计模式
url: 806.html
id: 806
categories:
  - C/C++
date: 2017-12-23 19:40:14
---

介绍
--

*   Mediator 抽象中介者角色 抽象中介者角色定义统一的接口， 用于各同事角色之间的通信。
*   Concrete Mediator 具体中介者角色 具体中介者角色通过协调各同事角色实现协作行为， 因此它必须依赖于各个同事角色。
*   Colleague 同事角色 每一个同事角色都知道中介者角色， 而且与其他的同事角色通信的时候， 一定要通过中介者角色协作。

每个同事类的行为分为两种：

*   自发行为（Self-Method）：同事本身的行为， 比如改变对象本身的状态， 处理自己的行为等，与其他的同事类或中介者没有任何的依赖
*   依赖方法（DepMethod）：必须依赖中介者才能完成的行为

优点： 一对多的依赖变成了一对一，降低耦合 缺点： 同事类越多， 中介者的逻辑就越复杂 应用：机场调度中心、MVC框架、媒体网关、中介服务

范例
--

抽象中介

#ifndef MEDIATOR_H
#define MEDIATOR_H
#include "colleague.h"
class Mediator {
public:
    virtual void setC1(Colleague *c1) = 0;
    virtual void setC2(Colleague *c2) = 0;
    virtual void doSomething1() = 0;
    virtual void doSomething2() = 0;
};
#endif // MEDIATOR_H

具体中介

#ifndef CONCRETEMEDIATOR_H
#define CONCRETEMEDIATOR_H
#include <QDebug>
#include "mediator.h"
class ConcreteMediator : public Mediator {
public:
    void setC1(Colleague *c1) override {
        c1_ = c1;
    }
    void setC2(Colleague *c2) override {
        c2_ = c2;
    }
    void doSomething1() override {
        qDebug()<<"mediator get c1 sended message";
        c2_->selfMethod();
    }
    void doSomething2() override {
        qDebug()<<"mediator get c2 sended message";
        c1_->selfMethod();
    }
private:
    Colleague *c1_;
    Colleague *c2_;
};
#endif // CONCRETEMEDIATOR_H

抽象同事

#ifndef COLLEAGUE_H
#define COLLEAGUE_H
class Colleague {
public:
    virtual void depMethod() = 0;
    virtual void selfMethod() = 0;
};
#endif // COLLEAGUE_H

具体同事-两个

#ifndef CONCRETECOLLEAGUE_H
#define CONCRETECOLLEAGUE_H
#include <QDebug>
#include "mediator.h"
#include "colleague.h"
class ConcreteColleague1 : public Colleague {
public:
    //通过构造函数传递中介者
    ConcreteColleague1(Mediator *mediator) {
        mediator_ = mediator;
    }
    //自有方法 self-method
    void selfMethod() override {
        qDebug()<<"c1 get mediator message";
    }
    //依赖方法 dep-method
    void depMethod() override {
        qDebug()<<"c1 send message";
        mediator_->doSomething1();
    }
private:
    Mediator *mediator_;
};
class ConcreteColleague2 : public Colleague {
//通过构造函数传递中介者
public:
    ConcreteColleague2(Mediator *mediator) {
        mediator_ = mediator;
    }
    void selfMethod() override {
        qDebug()<<"c2 get mediator message";
    }
    void depMethod() override {
        qDebug()<<"c2 send message";
        mediator_->doSomething2();
    }
private:
    Mediator *mediator_;
};
#endif // CONCRETECOLLEAGUE_H

使用

#include "concretecolleague.h"
#include "concretemediator.h"
int main(int argc, char *argv\[\]) {
    //建立中介，完成联系
    Mediator *m = new ConcreteMediator;
    Colleague *c1 = new ConcreteColleague1(m);
    Colleague *c2 = new ConcreteColleague2(m);
    m->setC1(c1);
    m->setC2(c2);
    c1->depMethod();
    c2->depMethod();
}

结果

c1 send message
mediator get c1 sended message
c2 get mediator message
c2 send message
mediator get c2 sended message
c1 get mediator message

同事类要使用构造函数注入中介者， 而中介者使用getter/setter方式注入同事类， 因为同事类必须有中介者， 而中介者却可以只有部分同事类 源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)