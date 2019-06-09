---
title: " C++设计模式-策略模式\t\t"
tags:
  - c++
  - 设计模式
url: 819.html
id: 819
categories:
  - C/C++
date: 2017-12-24 17:08:55
---

介绍
--

*   Context封装角色 也叫做上下文角色， 起承上启下封装作用， 屏蔽高层模块对策略、 算法的直接访问，封装可能存在的变化。
*   Strategy抽象策略角色 策略、 算法家族的抽象， 通常为接口， 定义每个策略或算法必须具有的方法和属性。
*   ConcreteStrategy具体策略角色 实现抽象策略中的操作， 该类含有具体的算法。

优点： 算法可以自由切换 、 避免使用多重条件判断 、 扩展性良好 缺点： 每一个策略都是一个类，复用的可能性很小，类数量增多； 所有的策略类都需要对外暴露 应用： 多个类只有在算法或行为上稍有不同的场景； 算法需要自由切换的场景；需要屏蔽算法规则的场景

> 一个策略家族的具体策略数量超过4个，则需要考虑使用混合模式， 解决策略类膨胀和对外暴露的问题

范例
--

#ifndef STRATEGY_H
#define STRATEGY_H
class Strategy {
//策略模式的运算法则
public:
    virtual ~Strategy() { }
    virtual void doSomething() = 0;
};
#endif // STRATEGY_H
#ifndef CONCRETESTRATEGY_H
#define CONCRETESTRATEGY_H
#include <QDebug>
#include "strategy.h"
class ConcreteStrategy1 : public Strategy {
public:
    void doSomething() override {
        qDebug()<<"ConcreteStrategy1 doSomething";
    }
};
class ConcreteStrategy2 : public Strategy {
public:
    void doSomething() override {
        qDebug()<<"ConcreteStrategy2 doSomething";
    }
};
#endif // CONCRETESTRATEGY_H
#ifndef CONTEXT_H
#define CONTEXT_H
#include "strategy.h"
class Context {
public:
    //构造函数设置具体策略
    Context(Strategy *strategy) {
        strategy_ = strategy;
    }
    //封装后的策略方法
    void doAnythinig() {
        strategy_->doSomething();
    }
private:
    Strategy *strategy_ = nullptr;
};
#endif // CONTEXT_H
#include "concretestrategy.h"
#include "context.h"
int main(int argc, char *argv\[\]) {
    Strategy *strategy = new ConcreteStrategy1();
    //声明上下文对象
    Context context(strategy);
    //执行封装后的方法
    context.doAnythinig();
    delete strategy;
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)