---
title: " C++设计模式-建造者模式\t\t"
tags:
  - c++
  - 设计模式
url: 794.html
id: 794
categories:
  - C/C++
date: 2017-12-23 11:50:56
---

介绍
--

建造者模式中， 有如下4个角色：

*   Product产品类 通常是实现了模板方法模式， 也就是有模板方法和基本方法， 这个参考第10章的模板方法模式。 例子中的BenzModel和BMWModel就属于产品类。
*   Builder抽象建造者 规范产品的组建， 一般是由子类实现。 例子中的CarBuilder就属于抽象建造者。
*   ConcreteBuilder具体建造者 实现抽象类定义的所有方法， 并且返回一个组建好的对象。 例子中的BenzBuilder和BMWBuilder就属于具体建造者。
*   Director导演类 负责安排已有模块的顺序， 然后告诉Builder开始建造。

优点：封装性；建造者独立，容易扩展；便于控制细节风险 使用：

*   相同的方法，不同的执行顺序具有不同结果；
*   多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同；
*   产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能；
*   在对象创建过程中会使用到系统中的一些其他对象，这些对象在产品对象的创建过程中不易得到时

建造者模式关注的是零件类型和装配工艺（顺序），这是它与工厂方法模式最大不同的地方，虽然同为创建类模式，但是注重点不同。建造者模式最主要的功能是基本方法的 调用顺序安排，也就是这些基本方法已经实现了，通俗地说就是零件的装配，顺序不同产生的对象也不同；而工厂方法则重点是创建，创建零件是它的主要职责，组装顺序则不是它关心的。

范例
--

#ifndef PRODUCT_H
#define PRODUCT_H
#include <QDebug>
class Product {
public:
    void setA(int a) {a_ = a;}
    void setB(int b) {b_ = b;}
    void Show() {qDebug()<<a_<<b_;}
private:
    int a_, b_;
};
#endif // PRODUCT_H
#ifndef BUILDER_H
#define BUILDER_H
#include "product.h"
class Builder {
public:
    virtual void setA() = 0;
    virtual void setB() = 0;
    virtual Product getProduct() = 0;
};
#endif // BUILDER_H
#ifndef CONCRETEPRODUCT_H
#define CONCRETEPRODUCT_H
#include "builder.h"
class ConcreteBuilder : public Builder {
public:
    virtual void setA() override {product.setA(1);}
    virtual void setB() override {product.setB(2);}
    Product getProduct() {
        return product;
    }
private:
    Product product;
};
#endif // CONCRETEPRODUCT_H
#ifndef DIRECTOR_H
#define DIRECTOR_H
#include "concrete_builder.h".h"
class Director {
public:
    Product getAProduct(Builder *builder){
        builder->setA();
        builder->setB();
        return builder->getProduct();
    }
};
#endif // DIRECTOR_H
#include "director.h"
int main(int argc, char *argv\[\]) {
    Director d;
    Product p =d.getAProduct(new ConcreteBuilder());
    p.Show();
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)