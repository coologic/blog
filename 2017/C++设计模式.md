---
title: " C++设计模式\t\t"
tags:
  - c++
  - 设计模式
url: 764.html
id: 764
categories:
  - C/C++
date: 2017-12-22 17:56:36
---

最近开始看设计模式之禅，之前看过一遍可惜没有学以致用，这次边看边整理

六大设计原则
------

### 单一职责原则(Single Responsibility Principle)

There should never be more than one reason for a class to change. 应该有且仅有一个原因引起类的变更 。

### 里氏替换原则(Liskov Substitution Principle, LSP)

第一种定义， 也是最正宗的定义： If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T,the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T.（如果对每一个类型为S的对象o1， 都有类型为T的对象o2， 使得以T定义的所有程序P在所有的对象o1都代换成o2时， 程序P的行为没有发生变化， 那么类型S是类型T的子类型。 ） 第二种定义： Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it.（所有引用基类的地方必须能透明地使用其子类的对象。 ）

### 依赖倒置原则(Dependence Inversion Principle, DIP)

High level modules should not depend upon low level modules.Both should depend upon abstractions.Abstractions should not depend upon details.Details should depend upon abstractions. 高层模块不应该依赖低层模块，两者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。 依赖关系有三种方式来传递： 构造函数传递依赖对象 、 Setter方法传递依赖对象 、 接口声明依赖对象。

### 接口隔离原则

实例接口（Object Interface）类的对象符合类的定义 类接口（Class Interface）类的定义符合接口定义 ，c++中是符合virtual关键词定义的虚函数，java中符合interface关键词定义的接口

*   Clients should not be forced to depend upon interfaces that they don't use.（客户端不应该依赖它不需要的接口。 ）
*   The dependency of one class to another one should depend on the smallest possible interface.（类间的依赖关系应该建立在最小的接口上。 ）

### 迪米特法则(Law of Demeter, LoD)

也称为最少知识原则（Least Knowledge Principle， LKP） , 一个对象应该对其他对象有最少的了解。

*   Only talk to your immediate friends（只与直接的朋友通信。 ）
*   朋友间也是有距离的， 尽量不要对外公布太多的public方法和非静态的public变量， 尽量内敛， 多使用private、 package-private、 protected等访问权限
*   是自己的就是自己的， 如果一个方法放在本类中， 既不增加类间关系， 也对本类不产生负面影响， 那就放置在本类中
*   谨慎使用Serializable

### 开闭原则

Software entities like classes,modules and functions should be open for extension but closed for modifications.（一个软件实体如类、 模块和函数应该对扩展开放， 对修改关闭。 ）

设计模式分类
------

共有 23 种设计模式可分为三大类

类型

简述

创建型模式（Creational Patterns）

处理对象的创建机制，试图在一个适合的方式创建对象，隐藏了创造过程的逻辑不需要使用new。[wiki](https://en.wikipedia.org/wiki/Creational_pattern)

结构型模式（Structural Patterns）

通过描述实体间关系、类和对象的组合来简化设计。[wiki](https://en.wikipedia.org/wiki/Structural_pattern)

行为型模式（Behavioral Patterns）

确定对象之间的公共通信模式并实现这些模式的设计模式。通过这样做，这些模式增加了执行此通信的灵活性。[wiki](https://en.wikipedia.org/wiki/Behavioral_pattern)

### 创建型模式

*   [单例模式](http://techieliang.com/2017/12/772/)（Singleton Pattern） Ensure a class has only one instance, and provide a global point of access to it.（确保某一个类只有一个实例， 而且自行实例化并向整个系统提供这个实例。)
*   [工厂方法模式](http://techieliang.com/2017/12/775/) （Factory Method Pattern） Define an interface for creating an object,but let subclasses decide which class to instantiate.Factory Method lets a class defer instantiation to subclasses.（定义一个用于创建对象的接口， 让子类决定实例化哪一个类。 工厂方法使一个类的实例化延迟到其子类。 ）
*   [抽象工厂模式](http://techieliang.com/2017/12/775/) （Abstract Factory Pattern） Provide an interface for creating families of related or dependent objects without specifying their concrete classes.（为创建一组相关或相互依赖的对象提供一个接口， 而且无须指定它们的具体类。 ）
*   [建造者模式/ 生成器模式](http://techieliang.com/2017/12/794/)（Builder Pattern） Separate the construction of a complex object from its representation so that the same construction process can create different representations.（将一个复杂对象的构建与它的表示分离， 使得同样的构建过程可以创建不同的表示。 ）
*   [原型模式](http://techieliang.com/2017/12/799/)（Prototype Pattern） Specify the kinds of objects to create using a prototypical instance,and create new objects by copying this prototype.（用原型实例指定创建对象的种类， 并且通过拷贝这些原型创建新的对象。 ）

### 机构型模式

*   [代理模式](http://techieliang.com/2017/12/802/)（Proxy Pattern） Provide a surrogate or placeholder for another object to control access to it.（为其他对象提供一种代理以控制对这个对象的访问。 ）
*   [装饰模式](http://techieliang.com/2017/12/815/)（Decorator Pattern） Attach additional responsibilities to an object dynamically keeping the same interface.Decorators provide a flexible alternative to subclassing for extending functionality.（动态地给一个对象添加一些额外的职责。就增加功能来说， 装饰模式相比生成子类更为灵活。 ）
*   [适配器模式/变压器模式](http://techieliang.com/2017/12/821/)（Adapter Pattern）/ 包装模式（Wrapper） 包装模式也包括装饰模式 Convert the interface of a class into another interface clients expect.Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.（将一个类的接口变换成客户端所期待的另一种接口， 从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。 ）
*   [组合模式/合成模式](http://techieliang.com/2017/12/826/)（Composite Pattern）/ 部分-整体模式（Part-Whole） Compose objects into tree structures to represent part-whole hierarchies.Composite lets clients treat individual objects and compositions of objects uniformly.（将对象组合成树形结构以表示“部分-整体”的层次结构， 使得用户对单个对象和组合对象的使用具有一致性。 ）
*   [外观模式/ 门面模式](http://techieliang.com/2017/12/831/)（Facade Pattern） Provide a unified interface to a set of interfaces in a subsystem.Facade defines a higher-level interface that makes the subsystem easier to use.（要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。 门面模式提供一个高层次的接口， 使得子系统更易于使用。 ）
*   享元模式（Flyweight Pattern） 池技术的重要实现方式。 Use sharing to support large numbers of fine-grained objects efficiently.（使用共享对象可有效地支持大量的细粒度的对象。 ）
*   桥梁模式/桥接模式（Bridge Pattern） Decouple an abstraction from its implementation so that the two can vary independently.（将抽象和实现解耦， 使得两者可以独立地变化。 ）

### 行为型模式

*   [模版方法模式](http://techieliang.com/2017/12/790/)（Template Method Pattern） Define the skeleton of an algorithm in an operation,deferring some steps to subclasses.Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.（定义一个操作中的算法的框架， 而将一些步骤延迟到子类中。 使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。 ）
*   [命令模式](http://techieliang.com/2017/12/808/)（Command Pattern） Encapsulate a request as an object,thereby letting you parameterize clients with different requests,queue or log requests,and support undoable operations.（将一个请求封装成一个对象， 从而让你使用不同的请求把客户端参数化， 对请求排队或者记录请求日志， 可以提供命令的撤销和恢复功能。 ）
*   [中介者模式](http://techieliang.com/2017/12/806/)（Mediator Pattern） Define an object that encapsulates how a set of objects interact.Mediator promotes loose coupling by keeping objects from referring to each other explicitly,and it lets you vary their interaction independently.（用一个中介对象封装一系列的对象交互， 中介者使各对象不需要显示地相互作用， 从而使其耦合松散， 而且可以独立地改变它们之间的交互。 ）
*   [职责链模式 / 责任链模式](http://techieliang.com/2017/12/811/)（Chain of Responsibility Pattern） Avoid coupling the sender of a request to its receiver by giving more than one object a chance to handle the request.Chain the receiving objects and pass the request along the chain until an object handles it.（使多个对象都有机会处理请求， 从而避免了请求的发送者和接受者之间的耦合关系。 将这些对象连成一条链， 并沿着这条链传递该请求， 直到有对象处理它为止。 ）
*   [策略模式/政策模式](http://techieliang.com/2017/12/819/)（Strategy Pattern） Define a family of algorithms,encapsulate each one,and make them interchangeable.（定义一组算法， 将每个算法都封装起来， 并且使它们之间可以互换。 ）
*   迭代器模式（Iterator Pattern） Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation.（它提供一种方法访问一个容器对象中各个元素， 而又不需暴露该对象的内部细节。 ）
*   [观察者模式](http://techieliang.com/2017/12/829/)（Observer Pattern）/发布订阅模式（Publish/subscribe） Define a one-to-many dependency between objects so that when one object changes state,all its dependents are notified and updated automatically.（定义对象间一种一对多的依赖关系， 使得每当一个对象改变状态， 则所有依赖于它的对象都会得到通知并被自动更新。 ）
*   [备忘录模式](http://techieliang.com/2017/12/835/)（Memento Pattern） Without violating encapsulation,capture and externalize an object's internal state so that the object can be restored to this state later.（在不破坏封装性的前提下， 捕获一个对象的内部状态， 并在该对象之外保存这个状态。 这样以后就可将该对象恢复到原先保存的状态。 ）
*   [访问者模式](http://techieliang.com/2017/12/838/)（Visitor Pattern） Represent an operation to be performed on the elements of an object structure. Visitor lets you define a new operation without changing the classes of the elements on which it operates. （封装一些作用于某种数据结构中的各元素的操作， 它可以在不改变数据结构的前提下定义作用于这些元素的新的 操作。 ）
*   [状态模式](http://techieliang.com/2017/12/840/)（State Pattern） Allow an object to alter its behavior when its internal state changes.The object will appear to change its class.（当一个对象内在状态改变时允许其改变行为， 这个对象看起来像改变了其类。 ）
*   解释器模式（Interpreter Pattern） Given a language, define a representation for its grammar along with an interpreter that uses the representation to interpret sentences in the language.（给定一门语言， 定义它的文法的一种表示， 并定义一个解释器， 该解释器使用该表示来解释语言中的句子。 ）

范例源码
----

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)**