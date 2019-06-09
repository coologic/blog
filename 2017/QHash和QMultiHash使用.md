---
title: " QHash和QMultiHash使用\t\t"
tags:
  - qhash
  - QMultiHash
  - qt
url: 557.html
id: 557
categories:
  - Qt
date: 2017-12-06 13:23:13
---

介绍
--

QHash<Key, T>是一个在哈希表中存储键值对的数据结构。它的接口几乎与QMap<Key, T>相同，但是与QMap<Key, T>相比，它对ey的模板类型有不同的要求，而且它提供了比QMap<Key, T>更快的查找功能。

> The key type of a QMap must provide operator<(). The key type of a QHash must provide operator==() and a global hash function called qHash() (see qHash).

QMap需要提供operator<()。QHash<K, T>中K的值类型还需要提供一个operator==()，并需要一个能够为键返回哈希值的全局qHash()函数的支持。Qt已经为qHash()函数提供了对整型、指针型、QChar、QString以及QByteArray。 QMultiHash类似于QMultiMap相对于QMap的，实现了但key对应多值。 相关帮助文档：[QHash](http://doc.qt.io/qt-5/qhash.html)、[QMultiHash](http://doc.qt.io/qt-5/qmultihash.html)

简单范例
----

### QHash

#include <QHash>
#include <QDebug>
QHash<QString,int> m_map;
m_map\["a"\] = 10;
m_map\["a"\] = 11;
m_map\["as"\] = 13;
m_map.insert("b",22);//同key不同value
m_map.insert("b",23);
m_map.insert("ba",55);
m_map.insert("ba",56);
m_map.insert("t1",77);//同value不同key
m_map.insert("t2",77);
auto find\_index = m\_map.find("as");
if(find\_index!=m\_map.end()) {
    qDebug()<<find\_index.key()<<find\_index.value();
}
qDebug()<<m_map.value("a");
qDebug()<<m_map.value("b");
qDebug()<<m_map.value("aa");
qDebug()<<m_map.values("b");//测试同key不同value
qDebug()<<m_map.key(22);
qDebug()<<m_map.key(77);
qDebug()<<m_map.keys(77);//测试同value不同key

结果

"as" 13
11
23
0
(23)
""
"t2"
("t2", "t1")

### QMultiHash

QMultiHash<QString,int> m_map;
//m_map\["a"\] = 10;
//m_map\["a"\] = 11;
//m_map\["as"\] = 13;
m_map.insert("b",22);//同key不同value
m_map.insert("b",23);
m_map.insert("ba",55);
m_map.insert("ba",56);
m_map.insert("t1",77);//同value不同key
m_map.insert("t2",77);
auto find\_index = m\_map.find("as");
if(find\_index!=m\_map.end()) {
    qDebug()<<find\_index.key()<<find\_index.value();
}
qDebug()<<m_map.value("a");
qDebug()<<m_map.value("b");
qDebug()<<m_map.value("aa");
qDebug()<<m_map.values("b");//测试同key不同value
qDebug()<<m_map.key(22);
qDebug()<<m_map.key(77);
qDebug()<<m_map.keys(77);//测试同value不同key
//修改必须用replace
m_map.replace("b",25);//讲第一个key=b的修改为了25
m_map.replace("t3",77);//由于没有t3=77所以新增加了一个
qDebug()<<m_map.values("b");
qDebug()<<m_map.keys(77);

结果

0
23
0
(23, 22)
"b"
"t1"
("t1", "t2")
(25, 22)
("t1", "t2", "t3")

自定义类型实现hash
-----------

见[QSet使用-自定义类型](http://techieliang.com/2017/12/580/)，QSet也是利用哈希表实现，原理相同。