---
title: " C++设计模式-备忘录模式\t\t"
tags:
  - c++
  - 设计模式
url: 835.html
id: 835
categories:
  - C/C++
date: 2017-12-27 21:07:37
---

介绍
--

*   Originator发起人角色 记录当前时刻的内部状态， 负责定义哪些属于备份范围的状态， 负责创建和恢复备忘录数据。
*   Memento备忘录角色 负责存储Originator发起人对象的内部状态， 在需要的时候提供发起人需要的内部状态。
*   Caretaker备忘录管理员角色 对备忘录进行管理、 保存和提供备忘录。

应用：

*   需要保存和恢复数据的相关状态场景
*   提供一个可回滚（rollback） 的操作； 比如Word中的CTRL+Z组合键， IE浏览器中的后退按钮， 文件管理器上的backspace键等
*   需要监控的副本场景中。 例如要监控一个对象的属性， 但是监控又不应该作为系统的主业务来调用， 它只是边缘应用， 即使出现监控不准、 错误报警也影响不大， 因此一般的做法是备份一个主线程中的对象， 然后由分析程序来分析
*   数据库连接的事务管理就是用的备忘录模式

范例
--

#ifndef CARETAKER_H
#define CARETAKER_H
#include "memento.h"
class Caretaker {
public:
    Memento *getMemento() {
        return memento_;
    }
    void setMemento(Memento *memento) {
        memento_ = memento;
    }
//备忘录对象
private:
    Memento *memento_;
};
#endif // CARETAKER_H
#ifndef MEMENTO_H
#define MEMENTO_H
#include <qstring.h>
class Memento {
//发起人的内部状态
private:
    QString state_ = "";
//构造函数传递参数
public:
    Memento(QString state){
        state_ = state;
    }
    QString getState() {
        return state_;
    }
    void setState(QString state) {
        state_ = state;
    }
};
#endif // MEMENTO_H
#ifndef ORIGINATOR_H
#define ORIGINATOR_H
#include "memento.h"
class Originator {
public:
    QString getState() {
        return state_;
    }
    void setState(QString state) {
        state_ = state;
    }
    //创建一个备忘录
    Memento* createMemento(){
        return new Memento(state_);
    }
    //恢复一个备忘录
    void restoreMemento(Memento *_memento){
        setState(_memento->getState());
    }
//内部状态
private:
    QString state_ = "";
};
#endif // ORIGINATOR_H
#include "caretaker.h"
#include "originator.h"
int main(int argc, char *argv\[\]) {
    //定义出发起人
    Originator *originator = new Originator();
    //定义出备忘录管理员
    Caretaker *caretaker = new Caretaker();
    //创建一个备忘录
    caretaker->setMemento(originator->createMemento());
    //恢复一个备忘录
    originator->restoreMemento(caretaker->getMemento());
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)