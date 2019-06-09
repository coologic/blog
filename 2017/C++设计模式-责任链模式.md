---
title: " C++设计模式-责任链模式\t\t"
tags:
  - c++
  - 设计模式
url: 811.html
id: 811
categories:
  - C/C++
date: 2017-12-23 22:49:32
---

介绍
--

*   抽象处理者 Handler 定义一个请求的处理方法handleMessage，唯一对外开放的方法；定义一个链的编排方法setNext，设置下一个处理者；定义了具体的请求者必须实现的两个方法：定义自己能够处理的级别getHandlerLevel和具体的处理任务echo
*   具体处理者 ConcreteHandler 实现抽象方法
*   请求发出者Client 发出请求

优点： 请求和处理分开 缺点：

*   性能问题，每个请求都是从链头遍历到链尾，特别是在链比较长的时候。
*   调试不很方便，采用了类似递归的方式，调试的时候逻辑可能比较复杂

> 避免出现超长链的情况， 一般的做法是在Handler中设置一个最大节点数量， 在setNext方法中判断是否已经是超过其阈值， 超过则不允许该链建立， 避免无意识地破坏系统性能

范例
--

抽象处理者

#ifndef HANDLER_H
#define HANDLER_H
#include "request.h"
#include "response.h"
class Handler {
private:
    Handler *nextHandler;
//每个处理者都必须对请求做出处理
public:
     virtual Response handleMessage(Request *request) final{
        Response response;
        //判断是否是自己的处理级别
        if(getHandlerLevel() == request->getRequestLevel()){
            response = echo(request); 
        } else { //不属于自己的处理级别
        }
        //判断是否有下一个处理者
        //注意此处如果已经符合了级别并处理完成了消息还会继续向下传
        //若只需要当前级别消息被一次处理只需要将下属判断你放到上面的else内
        if(nextHandler != nullptr){
            response = nextHandler->handleMessage(request);
        } else {//没有其他的处理者
        }
        return response;
    }
     //设置下一个处理者是谁
    void setNext(Handler *handler) {
        nextHandler = handler;
    }
protected:
    //每个处理者都有一个处理级别
    virtual Level getHandlerLevel() = 0;
    //每个处理者都必须实现处理任务
    virtual Response echo(Request *request) = 0;
};
#endif // HANDLER_H

处理者

#ifndef CONCRETEHANDLER_H
#define CONCRETEHANDLER_H
#include "handler.h"
#include "level.h"
#include "response.h"
class ConcreteHandler1 : public Handler {
protected:
    Response echo(Request *request) override {
        qDebug()<<"Handler1 echo request";
        //处理请求并返回结果
        return Response();
    }
    //设置自己的处理级别
    Level getHandlerLevel() override {
        return Level1;
    }
};
//还有第二第三个省略了
#endif // CONCRETEHANDLER_H

处理请求的级别

#ifndef LEVEL_H
#define LEVEL_H
enum Level {
    Level1 = 0,
    Level2,
};
#endif // LEVEL_H

抽象处理请求和具体处理请求

#ifndef REQUEST_H
#define REQUEST_H
#include "level.h"
class Request {
public:
    virtual Level getRequestLevel() = 0;
};
class Request1 : public Request{
public:
    Level getRequestLevel() override {
        return Level1;
    }
};
class Request2 : public Request{
public:
    Level getRequestLevel() override {
        return Level2;
    }
};
#endif // REQUEST_H

处理结果

#ifndef RESPONSE_H
#define RESPONSE_H
#include "QDebug"
class Response {
public:
    void text() {
        qDebug()<<"get text";
    }
};
#endif // RESPONSE_H

main

#include "concretehandler.h"
#include "request.h"
#include "response.h"
int main(int argc, char *argv\[\]) {
    //声明所有的处理节点
    Handler *handler1 = new ConcreteHandler1();
    Handler *handler2 = new ConcreteHandler2();
    Handler *handler3 = new ConcreteHandler3();
    //设置链中的阶段顺序1-->2-->3
    handler1->setNext(handler2);
    handler2->setNext(handler3);
    //提交请求， 返回结果
    Response response = handler1->handleMessage(new Request1());
    response = handler1->handleMessage(new Request2());
}

结果

Handler1 echo request
Handler3 echo request
Handler2 echo request

> 上述例子一共发了两次请求有三个响应是因为在抽象处理者中，无论是否消息已处理均向下传播保证消息遍历所有处理者，可以在处理成功后直接结束避免一个请求被多个不同处理者处理

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)