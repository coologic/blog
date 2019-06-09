title: " Qt自定义标题栏\t\t"
tags:
  - qt
  - qtoolbar
  - 标题栏
url: 326.html
id: 326
categories:
  - Qt
date: 2017-11-25 17:27:53
---
![图片](http://wx4.sinaimg.cn/mw690/a8dbb8d6ly1flugooja77j20gv09ht8n.jpg)

标题栏的最大化、最小化、关闭按钮图标
------------------

此类按钮建议使用QToolButton实现，图标可以自定义也可以用Qt自带的基础图标
```
QToolButton *toolButton_mini;//最小化
QToolButton *toolButton_max;//最大化
QToolButton *toolButton_close;//关闭
toolButton_mini->setIcon(style()->standardPixmap(QStyle::SP_TitleBarMinButton));
toolButton_max->setIcon(style()->standardPixmap(QStyle::SP_TitleBarMaxButton));
toolButton_close->setIcon(style()->standardPixmap(QStyle::SP_TitleBarCloseButton));
```
将上述控件的点击事件与相应函数connect即可实现对应功能，注意最大化分为“最大化”和“还原”两个状态，且Qt提供了两个函数，不能直接connect。对应函数名分别为：
```
showMinimized()
showNormal()//还原
showMaximized()//最大化
close()
```
QToolBar基本使用
------------

直接new一个控件，然后再窗口类中使用：

`addToolBar(this);`

实现此控件的添加 通过`QToolBar *a;a->asetMovable(false);`实现禁止移动，同时取消工具栏左侧的移动标示按钮

QToolBar控件局右显示
--------------

一般关闭等按钮在右侧，QToolBar默认在左侧，可以在中间添加一个QWidget实现占位，从而保证按钮局右
```
QWidget *toolBar_seat = new QWidget;
toolBar_seat->setSizePolicy(QSizePolicy::Expanding, QSizePolicy::Expanding);//长宽自动扩展
addWidget(toolBar_seat);
```
双击标题栏最大化
--------

直接重写QObject的鼠标双击事件
```
protected:
void TitleBar::mouseDoubleClickEvent(QMouseEvent *event) {
    if(Qt::LeftButton == event->button())
        MaximizeButtonClicked();//此处调用最大化/还原按钮点击槽
    event->ignore();
}
```
窗口拖拽
----

使用自定义标题栏以后，窗口将失去拖拽标题栏移动的功能，通过此步骤可以重现，同样重写对应鼠标事件
```
virtual void mousePressEvent(QMouseEvent *event);
virtual void mouseReleaseEvent(QMouseEvent *event);
virtual void mouseMoveEvent(QMouseEvent *event);
```
主要原理是在按下时记录按下状态及按下时的窗口坐标，抬起时取消状态，在鼠标移动时判断状态并根据当前坐标差进行移动。
```
event->globalPos()//获取系统下全局坐标
widget=window();//获取主窗口指针
qwidget->move()//移动窗口
```
基于上述描述，可构建**QtCustomTitleBar**类,具体源码请见GitHub：**[QtWidgetsExamples](https://github.com/TechieLiang/QtWidgetsExamples)**