---
title: " QMultiMap使用\t\t"
tags:
  - QMultiMap
  - qt
url: 539.html
id: 539
categories:
  - Qt
date: 2017-12-05 22:23:33
---

QMultiMap与QMap
--------------

操作可以说完全一样，只不过QMultiMap继承自QMap，并实现了一个key 对应多个value(通过插入多个相同key的值)。 由于一个key对应了多个值，所以QMultiMap取消了对"\[\]"的定义

> Unlike QMap, QMultiMap provides no operator\[\]. Use value() or replace() if you want to access the most recently inserted item with a certain key. If you want to retrieve all the values for a single key, you can use values(const Key &key), which returns a QList<T>: QList<int> values = map.values("plenty"); for (int i = 0; i < values.size(); ++i) cout << values.at(i) << endl;

同时value会返回最后一次插入的值，而values可以返回所有的值

使用范例
----

QMultiMap<QString,int> m_map;
//m_map\["a"\] = 10;//这几个会出错
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

测试结果

23
0
(23, 22)
"b"
"t1"
("t1", "t2")
(25, 22)
("t1", "t2", "t3")

上述范例可以对比本博客[QMap使用](http://techieliang.com/2017/12/537/)