---
title: " C++设计模式-组合模式\t\t"
tags:
  - c++
  - 设计模式
url: 826.html
id: 826
categories:
  - C/C++
date: 2017-12-27 18:13:07
---

介绍
--

*   Component抽象构件角色 定义参加组合对象的共有方法和属性， 可以定义一些默认的行为或属性。
*   Leaf叶子构件 叶子对象， 其下再也没有其他的分支， 也就是遍历的最小单位。
*   Composite树枝构件 树枝对象， 它的作用是组合树枝节点和叶子节点形成一个树形结构

透明组合模式中，抽象构件Component中声明了所有用于管理成员对象的方法，包括add()、remove()以及getChild()等方法，这样做的好处是确保所有的构件类都有相同的接口。此模式中Component、Leaf、Composite均需要实现所有方法，对于leaf实际上add等方法是无意义的，但对于client来说不需要区分Leaf和Composite。 安全组合模式中，在抽象构件Component中没有声明任何用于管理成员对象的方法，而是在Composite类中声明并实现这些方法。 优点： 高层模块调用简单；节点自由增加 缺点： 树叶和树枝使用实现类，不符合依赖倒置 应用：维护和展示部分-整体关系的场景，如树形菜单、文件和文件夹管理；从一个整体中能够独立出部分模块或功能的场景。

范例
--

#ifndef COMPONENT_H
#define COMPONENT_H
class Component {
//个体和整体都具有的共享
public:
    virtual ~Component() { }
    virtual void doSomething() = 0;
};
#endif // COMPONENT_H
#ifndef COMPOSITE_H
#define COMPOSITE_H
#include <QList>
#include "component.h"
class Composite : public Component {
public:
    ~Composite() {
        for(auto component : componentArrayList) {
            delete component;
        }
    }
    void doSomething() override { }
    //增加一个叶子构件或树枝构件
    void add(Component *component){
        componentArrayList.append(component);
    }
    //删除一个叶子构件或树枝构件
    void remove(Component *component){
        delete component;
        componentArrayList.removeOne(component);
    }
    //获得分支下的所有叶子构件和树枝构件
    QList<Component *> getChildren(){
        return componentArrayList;
    }
//构件容器
private:
    QList<Component *> componentArrayList;
};
#endif // COMPOSITE_H
#ifndef LEAF_H
#define LEAF_H
#include "component.h"
class Leaf : public Component {
public:
    void doSomething() override { }
};
#endif // LEAF_H
#include "composite.h"
#include "leaf.h"
int main(int argc, char *argv\[\]) {
    Composite *root = new Composite();
    root->doSomething();
    //创建一个树枝构件
    Composite *branch = new Composite();
    //创建一个叶子节点
    Leaf *leaf = new Leaf();
    //建立整体
    root->add(branch);
    branch->add(leaf);
    //父节点析构会删除子节点
    delete root;
}

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)** 相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)