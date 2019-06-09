---
title: " C++设计模式-命令模式\t\t"
tags:
  - c++
  - 设计模式
url: 808.html
id: 808
categories:
  - C/C++
date: 2017-12-23 22:07:17
---

介绍
--

*   Receive接收者角色 该角色就是干活的角色， 命令传递到这里是应该被执行的。作为抽象类，定义一个可接受消息的抽象类，从而保证多个不同的具体角色均可接受命令
*   Command命令角色 需要执行的所有命令都在这里声明。定义抽象类一个和一系列具体命令类，每个类对应一个命令。
*   Invoker调用者角色 接收到命令， 并执行命令。

优点：类间解耦、可扩展性 、命令模式结合其他模式会更优秀 缺点： 如果有N个命令， Command的子类就是N个

范例
--

抽象接收者

#ifndef RECEIVER_H
#define RECEIVER_H
class Receiver {
    //抽象接收者， 定义每个接收者都必须完成的业务
public:
    virtual ~Receiver() { }
    virtual void doSomething() = 0;
};
#endif // RECEIVER_H

具体接收者

#ifndef CONCRETERECIVER_H
#define CONCRETERECIVER_H
#include <QDebug>
#include "receiver.h"
class ConcreteReciver1 : public Receiver{
//每个接收者都必须处理一定的业务逻辑
public:
    void doSomething(){
        qDebug()<<"Reciver1 doing";
    }
};
class ConcreteReciver2 : public Receiver{
//每个接收者都必须处理一定的业务逻辑
public:
    void doSomething(){
        qDebug()<<"Reciver2 doing";
    }
};
#endif // CONCRETERECIVER_H

抽象命令

#ifndef COMMAND_H
#define COMMAND_H
class Command {
//每个命令类都必须有一个执行命令的方法
public:
    virtual void execute() = 0;
};
#endif // COMMAND_H

具体命令

#ifndef CONCRETECOMMAND_H
#define CONCRETECOMMAND_H
#include <QDebug>
#include "command.h"
#include "receiver.h"
class ConcreteCommand1 : public Command {
private:
    Receiver *receiver_;//哪个Receiver类进行命令处理
public:
    //构造函数传递接收者
    ConcreteCommand1(Receiver *receiver) {
        receiver_ = receiver;
    }
    //必须实现一个命令
    void execute() {
        //业务处理
        qDebug()<<"command1 run";
        receiver_->doSomething();
    }
};
class ConcreteCommand2 : public Command {
private:
    Receiver *receiver_;
public:
    ConcreteCommand2(Receiver *receiver) {
        receiver_ = receiver;
    }
    void execute() {
        qDebug()<<"command2 run";
        receiver_->doSomething();
    }
};
#endif // CONCRETECOMMAND_H

调用者

#ifndef INVOKER_H
#define INVOKER_H
#include <QDebug>
#include "command.h"
class Invoker {
private:
    Command *command_;
public:
    //接受命令
    void setCommand(Command *command) {
        command_ = command;
        qDebug()<<"invoker add command";
    }
    //执行命令
    void action(){
        command_->execute();
        qDebug()<<"invoker action command";
    }
};
#endif // INVOKER_H

main

#include "concretecommand.h"
#include "concretereciver.h"
#include "invoker.h"
int main(int argc, char *argv\[\]) {
    //首先声明调用者Invoker
    Invoker *invoker = new Invoker();
    //定义接收者
    Receiver *receiver = new ConcreteReciver1();
    Receiver *receiver2 = new ConcreteReciver2();
    //定义一个发送给接收者的命令
    Command *command = new ConcreteCommand1(receiver);
    Command *command2 = new ConcreteCommand2(receiver);
    Command *command3 = new ConcreteCommand1(receiver2);
    Command *command4 = new ConcreteCommand2(receiver2);
    //把命令交给调用者去执行
    invoker->setCommand(command);
    invoker->action();
    invoker->setCommand(command2);
    invoker->action();
    invoker->setCommand(command3);
    invoker->action();
    invoker->setCommand(command4);
    invoker->action();
}

结果

invoker add command
command1 run
Reciver1 doing
invoker action command
invoker add command
command2 run
Reciver1 doing
invoker action command
invoker add command
command1 run
Reciver2 doing
invoker action command
invoker add command
command2 run
Reciver2 doing
invoker action command

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)