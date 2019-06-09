---
title: " QList和QVector使用\t\t"
tags:
  - qlist
  - qt
  - qvector
url: 563.html
id: 563
categories:
  - Qt
date: 2017-12-06 14:13:53
---

介绍
--

QVector

> The QVector class is a template class that provides a dynamic array. QVector<T> is one of Qt's generic container classes. It stores its items in adjacent memory locations and provides fast index-based access. QList<T>, QLinkedList<T>, QVector<T>, and QVarLengthArray<T> provide similar APIs and functionality. They are often interchangeable, but there are performance consequences. Here is an overview of use cases: QVector should be your default first choice. QVector<T> will usually give better performance than QList<T>, because QVector<T> always stores its items sequentially in memory, where QList<T> will allocate its items on the heap unless sizeof(T) <= sizeof(void*) and T has been declared to be either a Q\_MOVABLE\_TYPE or a Q\_PRIMITIVE\_TYPE using Q\_DECLARE\_TYPEINFO. See the Pros and Cons of Using QList for an explanation. However, QList is used throughout the Qt APIs for passing parameters and for returning values. Use QList to interface with those APIs. If you need a real linked list, which guarantees constant time insertions mid-list and uses iterators to items rather than indexes, use QLinkedList. Note: QVector and QVarLengthArray both guarantee C-compatible array layout. QList does not. This might be important if your application must interface with a C API. Note: Iterators into a QLinkedList and references into heap-allocating QLists remain valid as long as the referenced items remain in the container. This is not true for iterators and references into a QVector and non-heap-allocating QLists. Here's an example of a QVector that stores integers and a QVector that stores QString values:

更多请见[QVector](http://doc.qt.io/qt-5/qvector.html) QList：

> The QList class is a template class that provides lists. QList<T> is one of Qt's generic container classes. It stores items in a list that provides fast index-based access and index-based insertions and removals. QList<T>, QLinkedList<T>, and QVector<T> provide similar APIs and functionality. They are often interchangeable, but there are performance consequences. Here is an overview of use cases: QVector should be your default first choice. QVector<T> will usually give better performance than QList<T>, because QVector<T> always stores its items sequentially in memory, where QList<T> will allocate its items on the heap unless sizeof(T) <= sizeof(void*) and T has been declared to be either a Q\_MOVABLE\_TYPE or a Q\_PRIMITIVE\_TYPE using Q\_DECLARE\_TYPEINFO. See the Pros and Cons of Using QList for an explanation. However, QList is used throughout the Qt APIs for passing parameters and for returning values. Use QList to interface with those APIs. If you need a real linked list, which guarantees constant time insertions mid-list and uses iterators to items rather than indexes, use QLinkedList. Note: QVector and QVarLengthArray both guarantee C-compatible array layout. QList does not. This might be important if your application must interface with a C API. Note: Iterators into a QLinkedList and references into heap-allocating QLists remain valid as long as the referenced items remain in the container. This is not true for iterators and references into a QVector and non-heap-allocating QLists.

更多请见[QList](http://doc.qt.io/qt-5/qlist.html) QList<T>, QLinkedList<T>, and QVector<T>等使用操作近似，下述范例仅提供QList的

QList使用
-------

### 简单范例

#include <QList>
#include <QDebug>
QList<QString> m_list;
m_list.append("a");//尾插入
m_list.append("b");
m_list.append("c");
qDebug()<<m_list.at(0)<<
          m_list\[1\]<<
          m\_list\[m\_list.size()-1\];
qDebug()<<m\_list.first()<<m\_list.last();//取首尾值
m_list.prepend("t");//头插入
qDebug()<<m_list.at(0);
qDebug()<<m_list.takeAt(2);//取出值(会删除原值)
qDebug()<<m_list.at(2);

运行结果

"a" "b" "c"
"a" "c"
"t"
"b"
"c"

### 其他函数

> insert在指定位置插入 swap交换两个位置的值 contains是否包含判断 count查找某个值的个数 indexOf找某个值对应的位置

### 迭代器风格

支持Java和STL风格，详情请见[Qt容器介绍](http://techieliang.com/2017/12/542/)