title: " Qt-QTreeview/QTableView排序问题\t\t"
tags:
  - qt
  - QTableView
  - QTreeview
url: 66.html
id: 66
categories:
  - Qt
date: 2017-11-09 18:15:50
---
QTreeview/QTableView自带排序功能 

Qt可通过sortByColumn()实现对QTreeview/QTableView某列的排序 

也可通过setSortingEnabled()实现允许用户点击表头进行排序 

排序默认是对item的内容进行排序 若使用
```
auto mitem = new QStandardItem("XXXX");
```
或者
```
auto mitem = new QStandardItem();
mitem .setText("XXXX");
```
由于其函数参数特性，会导致qt默认认为传入的值为QString类型，就算"XXXX"写的是数字也是字符串。 但可通过
```
mitem.setData(12313.223,Qt::EditRole);
```
实现对数字的传入，此函数默认参数类型为QVariant，故传入后view可通过QVariant识别出内容为数字，后进行排序可实现数值内容排序