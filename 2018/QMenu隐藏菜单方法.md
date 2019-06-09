---
title: " QMenu隐藏菜单方法\t\t"
tags:
  - qt
url: 900.html
id: 900
categories:
  - Qt
date: 2018-01-22 22:04:42
---

介绍
--

QMenu是Qt提供的菜单控件，菜单可用于窗口菜单栏也可用于右键菜单，相关帮助：[QMenu](http://doc.qt.io/qt-5/qmenu.html) 菜单的使用时通过菜单+action动作组合的方式实现功能的，QMenu继承自QWidget，用于其父类的hide/setVisible/setHide等方法，但是均无法隐藏菜单。

隐藏方法
----

查看相关api可以发现上述说到的方法都是继承自widget的，当然理论上来说应该是可以通过上述方法隐藏一个widget，毕竟是继承的呀。 后来仔细看QMenu的接口，找到了一个比较另类的接口：**[menuAction](http://doc.qt.io/qt-5/qmenu.html#menuAction)**()，难道他的意思是menu也实际上是个Aciton？，获取以后调用Aciton的setVisible，成功隐藏了menu，具体调用： QMenu::menuAction()->setVisible(false);