---
title: " QMap使用\t\t"
tags:
  - map
  - qmap
  - qt
url: 537.html
id: 537
categories:
  - Qt
date: 2017-12-05 21:50:07
---

简单范例
----

QMap与std::map相同，会自动根据key(第一项)进行升序排列

QMap<QString,int> m_map;
m_map\["a"\] = 10;//插入方式1
m_map\["as"\] = 13;
m_map.insert("b",22);//插入方式2
m_map.insert("ba",23);
auto find\_index = m\_map.find("as");//搜索
if(find\_index!=m\_map.end()) {//返回为end表明未搜索到
    qDebug()<<find\_index.key()<<find\_index.value();
}
qDebug()<<m_map.value("a");//直接搜索，根据值key找值
qDebug()<<m_map.value("aa");//没这项
qDebug()<<m_map.key(13);//根据值找key
qDebug()<<m_map.key(14);//没这项

返回结果：

"as" 13
10
0
"as"
""

相关帮助文档请见[官网](http://doc.qt.io/qt-5/qmap.html) erase删除某项，由于map的key具有唯一性，可以通过m_map\[XX\]=YY;修改已有项的值，若此项并不存在则会创建新的。

其他
--

### value/key方法返回值

value若查找不到目标会返回默认值，对于默认值得解释：

> Returns a list containing all the values in the map, in ascending order of their keys. If a key is associated with multiple values, all of its values will be in the list, and not just the most recently inserted one.

如果没有主动设置默认值，返回Qt默认值，此值在[文档的容器介绍有说明](http://doc.qt.io/qt-5/containers.html)

> The documentation of certain container class functions refer to _default-constructed values_; for example, [QVector](http://doc.qt.io/qt-5/qvector.html) automatically initializes its items with default-constructed values, and [QMap::value](http://doc.qt.io/qt-5/qmap.html#value)() returns a default-constructed value if the specified key isn't in the map. For most value types, this simply means that a value is created using the default constructor (e.g. an empty string for [QString](http://doc.qt.io/qt-5/qstring.html)). But for primitive types like `int` and `double`, as well as for pointer types, the C++ language doesn't specify any initialization; in those cases, Qt's containers automatically initialize the value to 0.

key方法若找不到相应key，同样返回默认值。

### QMap与std::map

QMap使用Iterator.key()，和Iterator.value()方法获取第一个或第二个元素的值。 而std::map使用Iterator->first()， Iterator->second()来获取第一个或第二个元素的值

### std::map<Key, T> QMap::toStdMap() const

QMap实现了与std::map相互转换，toStdMap转到std，使用QMap的构造函数从std::map转到QMap

QMap(std::initializer_list<std::pair<Key, T> > list)
QMap(const QMap<Key, T> &other)
QMap(QMap<Key, T> &&other)
QMap(const std::map<Key, T> &other)//可以导入std::map