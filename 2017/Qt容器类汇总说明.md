---
title: " Qt容器类汇总说明\t\t"
tags:
  - qt
  - 容器
url: 542.html
id: 542
categories:
  - Qt
date: 2017-12-05 22:43:56
---

下述说明来源于[官方文档](http://doc.qt.io/qt-5/containers.html)

介绍
--

Qt库提供了一组通用的基于模板的容器类。这些类可用于存储指定类型的项。例如，如果你需要一个可调整大小的数组qstrings，使用QVector <QString>。 这些容器类的设计要比STL容器更轻、更安全、更容易使用。如果您不熟悉STL，或者更喜欢做“qt方式”，您可以使用这些类而不是STL类。 Qt还提供了一个foreach关键字，让它来遍历所有的物品存放在一个容器很容易。

> qt提供的foreach在c++标准中并没有，在c++11中提供了类似于for(auto t:container)方式遍历容器，此方式qt也支持，个人感觉使用for更好

……更多介绍看官网

本博客的Qt容器使用说明
------------

[QMap](http://techieliang.com/2017/12/537/)、[QMultiMap](http://techieliang.com/2017/12/539/)、[QHash](http://techieliang.com/2017/12/557/)、[QMultiHash](http://techieliang.com/2017/12/557/)、[QQueue](http://techieliang.com/2017/12/576/)、[QStack](http://techieliang.com/2017/12/576/)、

容器类
---

Qt提供了下列顺序容器：QList，QLinkedList，QVector，QStack，和QQueue。**对于大多数应用程序，QList是使用最好的类型**。虽然它底层用数组链表实现，但提供了非常快速的前后追加函数。如果你真的需要一个链表，用QLinkedList；如果你想让你的项目占用连续的内存位置，使用QVector。QStack 和QQueue提供了先进先出(LIFO )，先结后出（FIFO ）。 Qt也提供了关联式容器：QMap,QMultiMap,QHash,QMultiHash,和QSet。Multi容器支持多个value关联到单一的key。Hash容器提供了通过hash函数快速查找取代二分查找。 特殊情况下，QCache和QContiguousCache提供了在有限的cache中高效的查找对象。

Class

Summary

[QList](http://doc.qt.io/qt-5/qlist.html)<T>

This is by far the most commonly used container class. It stores a list of values of a given type (T) that can be accessed by index. Internally, the [QList](http://doc.qt.io/qt-5/qlist.html) is implemented using an array, ensuring that index-based access is very fast.Items can be added at either end of the list using [QList::append](http://doc.qt.io/qt-5/qlist.html#append)() and [QList::prepend](http://doc.qt.io/qt-5/qlist.html#prepend)(), or they can be inserted in the middle using [QList::insert](http://doc.qt.io/qt-5/qlist.html#insert)(). More than any other container class, [QList](http://doc.qt.io/qt-5/qlist.html) is highly optimized to expand to as little code as possible in the executable. [QStringList](http://doc.qt.io/qt-5/qstringlist.html) inherits from [QList](http://doc.qt.io/qt-5/qlist.html)<[QString](http://doc.qt.io/qt-5/qstring.html)>.

[QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html)<T>

This is similar to [QList](http://doc.qt.io/qt-5/qlist.html), except that it uses iterators rather than integer indexes to access items. It also provides better performance than [QList](http://doc.qt.io/qt-5/qlist.html) when inserting in the middle of a huge list, and it has nicer iterator semantics. (Iterators pointing to an item in a [QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html) remain valid as long as the item exists, whereas iterators to a [QList](http://doc.qt.io/qt-5/qlist.html) can become invalid after any insertion or removal.)

[QVector](http://doc.qt.io/qt-5/qvector.html)<T>

This stores an array of values of a given type at adjacent positions in memory. Inserting at the front or in the middle of a vector can be quite slow, because it can lead to large numbers of items having to be moved by one position in memory.

[QStack](http://doc.qt.io/qt-5/qstack.html)<T>

This is a convenience subclass of [QVector](http://doc.qt.io/qt-5/qvector.html) that provides "last in, first out" (LIFO) semantics. It adds the following functions to those already present in [QVector](http://doc.qt.io/qt-5/qvector.html): [push()](http://doc.qt.io/qt-5/qstack.html#push), [pop()](http://doc.qt.io/qt-5/qstack.html#pop), and [top()](http://doc.qt.io/qt-5/qstack.html#top).

[QQueue](http://doc.qt.io/qt-5/qqueue.html)<T>

This is a convenience subclass of [QList](http://doc.qt.io/qt-5/qlist.html) that provides "first in, first out" (FIFO) semantics. It adds the following functions to those already present in [QList](http://doc.qt.io/qt-5/qlist.html): [enqueue()](http://doc.qt.io/qt-5/qqueue.html#enqueue), [dequeue()](http://doc.qt.io/qt-5/qqueue.html#dequeue), and [head()](http://doc.qt.io/qt-5/qqueue.html#head).

[QSet](http://doc.qt.io/qt-5/qset.html)<T>

This provides a single-valued mathematical set with fast lookups.

[QMap](http://doc.qt.io/qt-5/qmap.html)<Key, T>

This provides a dictionary (associative array) that maps keys of type Key to values of type T. Normally each key is associated with a single value. [QMap](http://doc.qt.io/qt-5/qmap.html) stores its data in Key order; if order doesn't matter [QHash](http://doc.qt.io/qt-5/qhash.html#qhash) is a faster alternative.

[QMultiMap](http://doc.qt.io/qt-5/qmultimap.html)<Key, T>

This is a convenience subclass of [QMap](http://doc.qt.io/qt-5/qmap.html) that provides a nice interface for multi-valued maps, i.e. maps where one key can be associated with multiple values.

[QHash](http://doc.qt.io/qt-5/qhash.html#qhash)<Key, T>

This has almost the same API as [QMap](http://doc.qt.io/qt-5/qmap.html), but provides significantly faster lookups. [QHash](http://doc.qt.io/qt-5/qhash.html#qhash) stores its data in an arbitrary order.

[QMultiHash](http://doc.qt.io/qt-5/qmultihash.html)<Key, T>

This is a convenience subclass of [QHash](http://doc.qt.io/qt-5/qhash.html#qhash) that provides a nice interface for multi-valued hashes.

迭代器类
----

Qt提供了多种风格的迭代器，Java风格和stl风格

### Java风格迭代器

Containers

Read-only iterator

Read-write iterator

[QList](http://doc.qt.io/qt-5/qlist.html)<T>, [QQueue](http://doc.qt.io/qt-5/qqueue.html)<T>

[QListIterator](http://doc.qt.io/qt-5/qlistiterator.html)<T>

[QMutableListIterator](http://doc.qt.io/qt-5/qmutablelistiterator.html)<T>

[QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html)<T>

[QLinkedListIterator](http://doc.qt.io/qt-5/qlinkedlistiterator.html)<T>

[QMutableLinkedListIterator](http://doc.qt.io/qt-5/qmutablelinkedlistiterator.html)<T>

[QVector](http://doc.qt.io/qt-5/qvector.html)<T>, [QStack](http://doc.qt.io/qt-5/qstack.html)<T>

[QVectorIterator](http://doc.qt.io/qt-5/qvectoriterator.html)<T>

[QMutableVectorIterator](http://doc.qt.io/qt-5/qmutablevectoriterator.html)<T>

[QSet](http://doc.qt.io/qt-5/qset.html)<T>

[QSetIterator](http://doc.qt.io/qt-5/qsetiterator.html)<T>

[QMutableSetIterator](http://doc.qt.io/qt-5/qmutablesetiterator.html)<T>

[QMap](http://doc.qt.io/qt-5/qmap.html)<Key, T>, [QMultiMap](http://doc.qt.io/qt-5/qmultimap.html)<Key, T>

[QMapIterator](http://doc.qt.io/qt-5/qmapiterator.html)<Key, T>

[QMutableMapIterator](http://doc.qt.io/qt-5/qmutablemapiterator.html)<Key, T>

[QHash](http://doc.qt.io/qt-5/qhash.html#qhash)<Key, T>, [QMultiHash](http://doc.qt.io/qt-5/qmultihash.html)<Key, T>

[QHashIterator](http://doc.qt.io/qt-5/qhashiterator.html)<Key, T>

[QMutableHashIterator](http://doc.qt.io/qt-5/qmutablehashiterator.html)<Key, T>

### STL风格迭代器

Containers

Read-only iterator

Read-write iterator

[QList](http://doc.qt.io/qt-5/qlist.html)<T>, [QQueue](http://doc.qt.io/qt-5/qqueue.html)<T>

[QList](http://doc.qt.io/qt-5/qlist.html)<T>::const_iterator

[QList](http://doc.qt.io/qt-5/qlist.html)<T>::iterator

[QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html)<T>

[QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html)<T>::const_iterator

[QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html)<T>::iterator

[QVector](http://doc.qt.io/qt-5/qvector.html)<T>, [QStack](http://doc.qt.io/qt-5/qstack.html)<T>

[QVector](http://doc.qt.io/qt-5/qvector.html)<T>::const_iterator

[QVector](http://doc.qt.io/qt-5/qvector.html)<T>::iterator

[QSet](http://doc.qt.io/qt-5/qset.html)<T>

[QSet](http://doc.qt.io/qt-5/qset.html)<T>::const_iterator

[QSet](http://doc.qt.io/qt-5/qset.html)<T>::iterator

[QMap](http://doc.qt.io/qt-5/qmap.html)<Key, T>, [QMultiMap](http://doc.qt.io/qt-5/qmultimap.html)<Key, T>

[QMap](http://doc.qt.io/qt-5/qmap.html)<Key, T>::const_iterator

[QMap](http://doc.qt.io/qt-5/qmap.html)<Key, T>::iterator

[QHash](http://doc.qt.io/qt-5/qhash.html#qhash)<Key, T>, [QMultiHash](http://doc.qt.io/qt-5/qmultihash.html)<Key, T>

[QHash](http://doc.qt.io/qt-5/qhash.html#qhash)<Key, T>::const_iterator

[QHash](http://doc.qt.io/qt-5/qhash.html#qhash)<Key, T>::iterator

Qt提供的其他容器
---------

Qt includes three template classes that resemble containers in some respects. These classes don't provide iterators and cannot be used with the `foreach` keyword.

*   [QVarLengthArray](http://doc.qt.io/qt-5/qvarlengtharray.html)<T, Prealloc> provides a low-level variable-length array. It can be used instead of [QVector](http://doc.qt.io/qt-5/qvector.html) in places where speed is particularly important.
*   [QCache](http://doc.qt.io/qt-5/qcache.html)<Key, T> provides a cache to store objects of a certain type T associated with keys of type Key.
*   [QContiguousCache](http://doc.qt.io/qt-5/qcontiguouscache.html)<T> provides an efficient way of caching data that is typically accessed in a contiguous way.
*   [QPair](http://doc.qt.io/qt-5/qpair.html)<T1, T2> stores a pair of elements.

Additional non-template types that compete with Qt's template containers are [QBitArray](http://doc.qt.io/qt-5/qbitarray.html), [QByteArray](http://doc.qt.io/qt-5/qbytearray.html), [QString](http://doc.qt.io/qt-5/qstring.html), and [QStringList](http://doc.qt.io/qt-5/qstringlist.html).

Qt容器算法复杂性
---------

Algorithmic complexity is concerned about how fast (or slow) each function is as the number of items in the container grow. For example, inserting an item in the middle of a [QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html) is an extremely fast operation, irrespective of the number of items stored in the [QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html). On the other hand, inserting an item in the middle of a [QVector](http://doc.qt.io/qt-5/qvector.html) is potentially very expensive if the [QVector](http://doc.qt.io/qt-5/qvector.html) contains many items, since half of the items must be moved one position in memory. To describe algorithmic complexity, we use the following terminology, based on the "big Oh" notation:

*   **Constant time:** O(1). A function is said to run in constant time if it requires the same amount of time no matter how many items are present in the container. One example is [QLinkedList::insert](http://doc.qt.io/qt-5/qlinkedlist.html#insert)().
*   **Logarithmic time:** O(log _n_). A function that runs in logarithmic time is a function whose running time is proportional to the logarithm of the number of items in the container. One example is qBinaryFind().
*   **Linear time:** O(_n_). A function that runs in linear time will execute in a time directly proportional to the number of items stored in the container. One example is [QVector::insert](http://doc.qt.io/qt-5/qvector.html#insert)().
*   **Linear-logarithmic time:** O(_n_ log _n_). A function that runs in linear-logarithmic time is asymptotically slower than a linear-time function, but faster than a quadratic-time function.
*   **Quadratic time:** O(_n_?). A quadratic-time function executes in a time that is proportional to the square of the number of items stored in the container.

The following table summarizes the algorithmic complexity of Qt's sequential container classes:

Index lookup

Insertion

Prepending

Appending

[QLinkedList](http://doc.qt.io/qt-5/qlinkedlist.html)<T>

O(_n_)

O(1)

O(1)

O(1)

[QList](http://doc.qt.io/qt-5/qlist.html)<T>

O(1)

O(n)

Amort. O(1)

Amort. O(1)

[QVector](http://doc.qt.io/qt-5/qvector.html)<T>

O(1)

O(n)

O(n)

Amort. O(1)

In the table, "Amort." stands for "amortized behavior". For example, "Amort. O(1)" means that if you call the function only once, you might get O(_n_) behavior, but if you call it multiple times (e.g., _n_ times), the average behavior will be O(1). The following table summarizes the algorithmic complexity of Qt's associative containers and sets:

Key lookup

Insertion

Average

Worst case

Average

Worst case

[QMap](http://doc.qt.io/qt-5/qmap.html)<Key, T>

O(log _n_)

O(log _n_)

O(log _n_)

O(log _n_)

[QMultiMap](http://doc.qt.io/qt-5/qmultimap.html)<Key, T>

O(log _n_)

O(log _n_)

O(log _n_)

O(log _n_)

[QHash](http://doc.qt.io/qt-5/qhash.html#qhash)<Key, T>

Amort. O(1)

O(_n_)

Amort. O(1)

O(_n_)

[QSet](http://doc.qt.io/qt-5/qset.html)<Key>

Amort. O(1)

O(_n_)

Amort. O(1)

O(_n_)