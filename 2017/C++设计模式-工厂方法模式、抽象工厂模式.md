---
title: " C++设计模式-工厂方法模式/抽象工厂模式\t\t"
tags:
  - 设计模式
url: 775.html
id: 775
categories:
  - C/C++
date: 2017-12-22 21:27:45
---

介绍
--

抽象工厂模式主要有四个关键元素：

*   抽象产品（Product）：负责定义产品的共性，实现对事物最抽象的定义。
*   具体产品（ConcreteProduct）：具体产品可以由多个。
*   抽象工厂（Factory）：工厂类必须实现这个接口，负责定义产品对象的产生。
*   具体工厂（ConcreteFactory）：具体如何产生一个产品的对象，一个具体工厂与一个具体产品一一对应。

优点

*   良好的封装性， 代码结构清晰
*   工厂方法模式的扩展性非常优秀 。 在增加产品类的情况下， 只要适当地修改具体的工厂类或扩展一个工厂类， 就可以完成“拥抱变化”。
*   屏蔽产品类。 这一特点非常重要， 产品类的实现如何变化， 调用者都不需要关心， 它只需要关心产品的接口， 只要接口保持不变， 系统中的上层模块就不要发生变化。
*   工厂方法模式是典型的解耦框架。 高层模块值需要知道产品的抽象类， 其他的实现类都不用关心， 符合迪米特法则、依赖倒置原则、里氏替换原则

缺点：产品与工厂对应，所以增加一个产品必须对应的增加一个工厂。 工厂模式说明见最后扩展

范例
--

抽象产品

#ifndef PRODUCT_H
#define PRODUCT_H
class Product {
public:
    virtual ~Product() { };
    virtual void Init() = 0;
    virtual int Number() = 0;
};
#endif // PRODUCT_H

具体产品

#ifndef CONCRETE\_PRODUCT\_H
#define CONCRETE\_PRODUCT\_H
#include "product.h"
//下面把多个产品写到一起了，应该分开
class ConcreteProduct1 : public Product {
public:
    virtual void Init() override { }
    virtual int Number() override {
        return 1;
    }
};
class ConcreteProduct2 : public Product {
public:
    virtual void Init() override { }
    virtual int Number() override {
        return 2;
    }
};
class ConcreteProduct3 : public Product {
public:
    virtual void Init() override { }
    virtual int Number() override {
        return 3;
    }
};
#endif // CONCRETE\_PRODUCT\_H

抽象工厂

#ifndef CREATOR_H
#define CREATOR_H
#include "product.h"
class Creator {
public:
    virtual ~Creator() { };
    virtual Product *Create() = 0;
};
#endif // CREATOR_H

具体工厂

#ifndef CONCRETE\_CREATOR\_H
#define CONCRETE\_CREATOR\_H
#include "creator.h"
#include "concrete_product.h"
//下面把多个工厂写到一起了，应该分开
class ConcreteCreator1 : public Creator {
public:
    virtual Product *Create() override {
        auto product = new ConcreteProduct1();
        product->Init();//工厂负责完成初始化过程
        //返回一个可直接使用的产品
        return product;
    }
};
class ConcreteCreator2 : public Creator {
public:
    virtual Product *Create() override {
        auto product = new ConcreteProduct2();
        product->Init();
        return product;
    }
};
class ConcreteCreator3 : public Creator {
public:
    virtual Product *Create() override {
        auto product = new ConcreteProduct3();
        product->Init();
        return product;
    }
};
#endif // CONCRETE\_CREATOR\_H

使用

#include <QDebug>
//包含某一个具体工厂头文件即可
#include "concrete_creator.h"
int main(int argc, char *argv\[\]) {
    Creator *c1 = new ConcreteCreator1();
    Product *p1 = c1->Create();
    Creator *c2 = new ConcreteCreator2();
    Product *p2 = c2->Create();
    Creator *c3 = new ConcreteCreator3();
    Product *p3 = c3->Create();
    qDebug()<<p1->Number();
    qDebug()<<p2->Number();
    qDebug()<<p3->Number();
    delete c1;
    delete c2;
    delete c3;
    delete p1;
    delete p2;
    delete p3;
    return 0;
}

扩展
--

### 工厂方法模式

工厂方法模式没有抽象工厂和具体工厂，只有一个工厂，其create通过不同的标识符传入返回对应产品，一般通过一系列case

### 简单工厂模式

静态方法生成产品，从而避免了工厂类的new。 可以用模板的方式实现一个静态方法创建不同产品，在Qt里面有一些静态工厂方法是传入字符串，根据字符串内容决定返回何种产品，比如[QStyleFactory](http://doc.qt.io/qt-5/qstylefactory.html)

### 延迟初始化

延迟初始化（Lazy initialization）一个对象被消费完毕后， 并不立刻释放， 工厂类保持其初始状态， 等待再次被使用。 实现方法：工厂类定义一个Map容器， 容纳所有产生的对象， 如果在Map容器中已经有的对象， 则直接取出返回； 如果没有， 则根据需要的类型产生一个对象并放入到Map容器中， 以方便下次调用。同时还可以通过Map限制某种产品的最大实例化数量。

### 利用C++11实现自动注册的工厂模式

参考qicosmos博客：[C++11实现一个自动注册的工厂](http://www.cnblogs.com/qicosmos/p/5090159.html) 源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)