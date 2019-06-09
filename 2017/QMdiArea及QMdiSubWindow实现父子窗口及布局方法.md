---
title: " QMdiArea及QMdiSubWindow实现父子窗口及布局方法\t\t"
tags:
  - QMdiArea
  - qt
  - 子窗口
url: 756.html
id: 756
categories:
  - Qt
date: 2017-12-20 22:48:39
---

介绍
--

![](https://github.com/TechieL/MyBlogPictureBackup/raw/master/%E5%9B%BE%E7%89%87/%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/QMdiArea%E5%8F%8AQMdiSubWindow%E5%AE%9E%E7%8E%B0%E7%88%B6%E5%AD%90%E7%AA%97%E5%8F%A3%E5%8F%8A%E5%B8%83%E5%B1%80%E6%96%B9%E6%B3%95/gif.gif) QMdiArea类提供了一个子窗口区域，而QMdiSubWindow则是子窗口类，均继承自QWidget。 相关帮助文档：[QMdiArea](http://doc.qt.io/qt-5/qmdiarea.html)、[QMdiSubWindow](http://doc.qt.io/qt-5/qmdisubwindow.html) QMdiArea可在Designer中直接拖拽控件，其内可以添加QMdiSubWindow也可以添加其他QWidget及其子类，也支持布局功能

### QMdiArea接口

QMdiSubWindow \*addSubWindow(QWidget \*widget, Qt::WindowFlags windowFlags = Qt::WindowFlags())
QMdiSubWindow *activeSubWindow() const
void closeActiveSubWindow()
void closeAllSubWindows()

添加窗口，当前活动窗口，关闭当前活动窗口，关闭所有窗口 还有以下枚举类型： QMdiArea::ViewMode显示模式`：SubWindowView，TabbedView` QMdiArea::AreaOption默认不全屏设置，如果不设置此项，在`TabbedView`时会将当前选中窗口最大化，且无边框 QMdiArea::WindowOrder排列顺序，`CreationOrder`、`StackingOrder`、`ActivationHistoryOrder`

### QMdiSubWindow接口

使用方面和QWidget无太大差异，若有对此类特殊的使用要求可看帮助文档。

范例
--

源码请见GitHub：**[QtWidgetsExamples](https://github.com/TechieL/QtWidgetsExamples)**

### ?