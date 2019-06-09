---
title: " QMetaEnum利用Qt元数据实现枚举(enum)类型值及字符串转换\t\t"
tags:
  - enum
  - qt
  - 枚举
  - 字符串
url: 622.html
id: 622
categories:
  - Qt
date: 2017-12-11 14:31:10
---

介绍
--

QMetaEnum类属于Qt core模块，提供了一系列针对枚举类型的操作函数，当然不能操作任意枚举类型，若想进行自定义枚举的操作，首先需要对枚举做处理，此时需要QObject类的帮助，使用此类提供的Q_ENUM模板。 详细信息请见官方文档：[QObject](http://doc.qt.io/qt-5/qobject.html)、[QMetaEnum](http://doc.qt.io/qt-5/qmetaenum.html)

自定义枚举类型
-------

#include <QObject>
class TestClass : public QObject {
    Q_OBJECT   //必须有
public:
    enum TestEnum {
        one = 0,
        two,
        three
    };
    Q_ENUM(TestEnum)
};

枚举类型的声明与c++标准相同，只不过需要将枚举放置在一个继承自QObject的子类中，同时要使用Q\_OBJECT模板，在枚举声明后面添加Q\_ENUM(enum_name)即可。 Qt自身的枚举类型都不需要此操作，可以直接使用。

> *   必须有Q_OBJECT? 不能只继承自QObject
> *   Q\_ENUM和Q\_OBJECT都不要加分号，强迫症会出错
> *   很遗憾不能将枚举类型直接放置在全局域

疑惑：这个类必须在单独的文件？直接放到main.cpp中一直报错，具体原因没有详细研究

QMetaEnum使用
-----------

Qt自身的枚举都可以直接使用。

#include <QCoreApplication>
#include <QDebug>
#include <QMetaEnum>
#include <QObject>
#include "testclass.h"
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QMetaEnum tenum = QMetaEnum::fromType<TestClass::TestEnum>();
    qDebug() << tenum.isValid();//判断是否有效
    qDebug() << tenum.name();//枚举名字
    qDebug() << tenum.scope();//范围
    //获取枚举数量，根据序号获取字符串
    for(int i = 0; i < tenum.keyCount(); i++)
            qDebug() << tenum.key(i);
    //根据字符串获取值
    qDebug() << tenum.keyToValue("two");
    //根据值获取字符串
    qDebug() << tenum.valueToKey(2);
    //根据序号获取值
    for(int i = 0; i < tenum.keyCount(); i++)
        qDebug() << tenum.value(i);

    return 0;
}

testclass.h就是上面的TestClass 类文件 QMetaEnum不光实现了枚举值和字符串的映射关系，额应该是key和value的映射关系，key 就是数字12345……，value就是枚举定义里面的字符串。同时还提供了枚举名称、枚举类型所属类、枚举项数量的函数，使用很方便。