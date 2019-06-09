---
title: " QSet使用及Qt自定义类型使用QHash等算法\t\t"
tags:
  - qhash
  - qset
  - qt
url: 580.html
id: 580
categories:
  - Qt
date: 2017-12-07 15:11:33
---

介绍
--

Qt提供的一个单值的数学集合的快速查找容器，使用方式与QList相同，但其内元素不会有重复。详细说明见 [官方文档](http://doc.qt.io/qt-5/qset.html)

> 注意，此容器实现方式是基于哈希表，而不是红黑树，若使用自定义类必须提供对应的hash函数： [QSet](http://doc.qt.io/qt-5/qset.html)'s value data type must be an [assignable data type](http://doc.qt.io/qt-5/containers.html#assignable-data-type). You cannot, for example, store a [QWidget](http://doc.qt.io/qt-5/qwidget.html) as a value; instead, store a [QWidget](http://doc.qt.io/qt-5/qwidget.html) *. In addition, the type must provide `operator==()`, and there must also be a global [qHash](http://doc.qt.io/qt-5/qhash.html#qHash)() function that returns a hash value for an argument of the key's type. See the [QHash](http://doc.qt.io/qt-5/qhash.html#qhash) documentation for a list of types supported by [qHash](http://doc.qt.io/qt-5/qhash.html#qHash)().

简单范例
----

QSet <QString> m_set;
m_set.insert("1");
m_set.insert("3");
m_set.insert("2");//注意插入顺序
auto b\_set = m\_set.begin();
qDebug()<<m_set.size();
qDebug()<<*b_set++;
qDebug()<<*b_set++;
qDebug()<<*b_set++;//注意存储顺序
m_set.insert("1");//插入重复的
b\_set = m\_set.begin();
qDebug()<<m_set.size();
qDebug()<<*b_set++;
qDebug()<<*b_set++;
qDebug()<<*b_set++;

结果

3
"1"
"2"
"3"
3
"1"
"2"
"3"

自定义类型
-----

#include <QCoreApplication>
#include <QSet>
#include <QDebug>
class testCustomTypeByQSet {
public:
    testCustomTypeByQSet(int v):m_value(v){};
    int value() const{
        return m_value;
    }
    bool operator == (const testCustomTypeByQSet &t) const {
        return (m_value==t.value());
    }
private:
    int m_value;
};
uint qHash(const testCustomTypeByQSet &key, uint seed = 0) {
    return key.value();
}
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QSet<testCustomTypeByQSet> m_set;
    m_set.insert(testCustomTypeByQSet(1));
    m_set.insert(testCustomTypeByQSet(3));
    m_set.insert(testCustomTypeByQSet(2));
    m_set.insert(testCustomTypeByQSet(7));
    m_set.insert(testCustomTypeByQSet(-1));
    auto b\_set = m\_set.begin();
    qDebug()<<m_set.size();
    qDebug()<<(*b_set++).value();
    qDebug()<<(*b_set++).value();
    qDebug()<<(*b_set++).value();
    qDebug()<<(*b_set++).value();
    qDebug()<<(*b_set++).value();
    return 0;
}

结果

5
-1
1
2
3
7

qt自身的类已经实现了对应的qHash，存储在QHash类中，详见[官方文档](http://doc.qt.io/qt-5/qhash.html)：

uint qHash(const QXmlNodeModelIndex &index)
uint qHash(const QUrl &url, uint seed = 0)
uint qHash(const QDateTime &key, uint seed = 0)
uint qHash(const QDate &key, uint seed = 0)
uint qHash(const QTime &key, uint seed = 0)
uint qHash(const QPair<T1, T2> &key, uint seed = 0)
uint qHash(const std::pair<T1, T2> &key, uint seed = 0)
uint qHash(char key, uint seed = 0)
uint qHash(uchar key, uint seed = 0)
uint qHash(signed char key, uint seed = 0)
uint qHash(ushort key, uint seed = 0)
uint qHash(short key, uint seed = 0)
uint qHash(uint key, uint seed = 0)
uint qHash(int key, uint seed = 0)
uint qHash(ulong key, uint seed = 0)
uint qHash(long key, uint seed = 0)
uint qHash(quint64 key, uint seed = 0)
uint qHash(qint64 key, uint seed = 0)
uint qHash(float key, uint seed = 0)
uint qHash(double key, uint seed = 0)
uint qHash(const QChar key, uint seed = 0)
uint qHash(const QByteArray &key, uint seed = 0)
uint qHash(const QBitArray &key, uint seed = 0)
uint qHash(const QString &key, uint seed = 0)
uint qHash(const QStringRef &key, uint seed = 0)
uint qHash(QLatin1String key, uint seed = 0)
uint qHash(const T *key, uint seed = 0)
uint qHash(const QHash<Key, T> &key, uint seed = 0)
uint qHash(const QSet<T> &key, uint seed = 0)
uint qHash(const QVersionNumber &key, uint seed = 0)
uint qHash(const QSslCertificate &key, uint seed = 0)
uint qHash(QSslEllipticCurve curve, uint seed = 0)
uint qHash(const QSslError &key, uint seed = 0)
uint qHash(const QGeoCoordinate &coordinate, uint seed = 0)

同时也在对应类中做了“==”的重载操作符，比如[QString类](http://doc.qt.io/qt-5/qstring.html)。