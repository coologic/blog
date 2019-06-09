title: " QWidget一生，从创建到销毁事件流\t\t"
tags:
  - qevent
  - qt
  - qwidget
  - 事件
url: 319.html
id: 319
categories:
  - Qt
date: 2017-11-23 16:41:52
---
最近做UI，有多个窗口嵌套，且所有窗口均用了Layout布局，当运行程序时，主窗口布局有效，而嵌套的窗口布局未生效。

构造函数Resize()
------------

首先我想到的是，我设置了Layout，那么他会自动调整大小，是不是在创建子窗口的时候并没有获取到此窗口在父类布局中占用的位置？那么我在构造的时候分别设置每个子窗口即可。 

child_widget->Resize(this->XXXX->size());//XXXX是子窗口一个widget的区域，或者qstackedwidget等某个页面的大小 

使用上述类似的指令去重新设置，发现没有任何效果。而根本原因是构造函数中获得的Size()并不对，不是主窗口的真实尺寸。

笨方法-QTimer()
------------

由于时间关系，使用了笨方法。无论如何最后构造完成以后肯定窗口显示了。。。。 

那么建立一个定时器，构造的时候启动定时器，连接到槽，然后timeout()以后执行Resize指令，并且stop Timer。。。 

此方法确实解决了布局适应的问题，但是建立一个timer，方法并不友好

QWidget构造到销毁事件流分析
-----------------

此处为了方便，不对QWidget做范例分析，直接用QMainWindow做分析，QMainWindow是QWidget的子类。 

下面会详细说明分析方法，若需要对QWidget或者其他控件做分析，可以仿照进行。

### 实验项目配置

直接新建一个Qt Widgets项目，为了测试方便，我把默认的菜单栏、工具栏、状态栏都取消了，只添加了一个QPushButton按钮控件。 

程序代码如下： 

*.pro
```
QT       += core gui
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
TARGET = untitled
TEMPLATE = app
DEFINES += QT_DEPRECATED_WARNINGS
SOURCES += \
        main.cpp \
        mainwindow.cpp
HEADERS += \
        mainwindow.h
FORMS += \
        mainwindow.ui
```
mainwindow.ui
```
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>MainWindow</class>
 <widget class="QMainWindow" name="MainWindow">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>400</width>
    <height>300</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>MainWindow</string>
  </property>
  <widget class="QWidget" name="centralWidget">
   <widget class="QPushButton" name="pushButton">
    <property name="geometry">
     <rect>
      <x>190</x>
      <y>150</y>
      <width>89</width>
      <height>24</height>
     </rect>
    </property>
    <property name="text">
     <string>PushButton</string>
    </property>
   </widget>
  </widget>
 </widget>
 <layoutdefault spacing="6" margin="11"/>
 <resources/>
 <connections/>
</ui>
```
main.cpp
```
#include "mainwindow.h"
#include <QApplication>
int main(int argc, char *argv[]) {
    QApplication a(argc, argv);
    MainWindow w;
    w.show();
    return a.exec();
}
```
mainwindow.h
```
#pragma once
#include <QMainWindow>
namespace Ui {
class MainWindow;
}
class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    explicit MainWindow(QWidget *parent = 0);
    ~MainWindow();
protected:
    bool event(QEvent *event) Q_DECL_OVERRIDE;
private:
    Ui::MainWindow *ui;
};
```
mainwindow.cpp
```
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QEvent>
#include <QDebug>
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow) {
    qDebug()<<"Befor ui->setupUi(this)";
    ui->setupUi(this);
    qDebug()<<"After ui->setupUi(this)";

}
MainWindow::~MainWindow() {
    delete ui;
}
bool MainWindow::event(QEvent *event) {
    qDebug()<<event->type();
    QMainWindow::event(event);
}
```
其实项目很简单，就是直接重写了event(QEvent *event)函数，利用qDebug()输出事件类型。 

事件类型通过type调用，返回TYPE枚举类型，此类型在“qcoreevent.h”文件夹存储
```
enum Type {
        None = 0,                               // invalid event
        Timer = 1,                              // timer event
        MouseButtonPress = 2,                   // mouse button pressed
        MouseButtonRelease = 3,                 // mouse button released
        MouseButtonDblClick = 4,                // mouse button double click
        MouseMove = 5,                          // mouse move
        KeyPress = 6,                           // key pressed
        KeyRelease = 7,                         // key released
        FocusIn = 8,                            // keyboard focus received
        FocusOut = 9,                           // keyboard focus lost
        FocusAboutToChange = 23,                // keyboard focus is about to be lost
        Enter = 10,                             // mouse enters widget
        Leave = 11,                             // mouse leaves widget
        Paint = 12,                             // paint widget
        Move = 13,                              // move widget
        Resize = 14,                            // resize widget
        Create = 15,                            // after widget creation
 ……………………………………………………………………//太多了，具体自行查看
};
```
### 结果

直接运行程序就出现了结果，从运行到窗口创建完毕： 同时在main()函数中每个位置增加断点可以看到不同指令的详细执行效果。
```
Befor ui->setupUi(this)//构造函数开始----MainWindow?w;
QEvent::Type(ChildAdded)
QEvent::Type(WindowTitleChange)
After ui->setupUi(this)//构造函数结束---MainWindow?w结束;
QEvent::Type(PlatformSurface)//---w.show()开始;
QEvent::Type(WinIdChange)
QEvent::Type(WindowIconChange)//icon
QEvent::Type(Polish)//style polish
QEvent::Type(ChildPolished)
QEvent::Type(Move)//调整窗口位置
QEvent::Type(Resize)//调整窗口尺寸
QEvent::Type(Show)//显示
QEvent::Type(CursorChange)
QEvent::Type(ShowToParent)
QEvent::Type(PolishRequest)
QEvent::Type(LayoutRequest)
QEvent::Type(UpdateLater)
QEvent::Type(UpdateRequest)//---w.show()结束
QEvent::Type(WindowActivate)//---a.exec()
QEvent::Type(ActivationChange)
QEvent::Type(Paint)//---a.exec()结束
```
关闭窗口：
```
QEvent::Type(NonClientAreaMouseMove)//鼠标移动到关闭
QEvent::Type(NonClientAreaMouseButtonPress)//点击右上角关闭
QEvent::Type(Close)
QEvent::Type(WindowDeactivate)
QEvent::Type(ActivationChange)
QEvent::Type(Hide)
QEvent::Type(HideToParent)
QEvent::Type(UpdateRequest)
```
### 结果分析

通过构造的事件流可以看到，Qt的设置各个控件的尺寸的操作在构造函数之后，所以在构造函数用Size获取到的尺寸是错误的。 

Qt的Event类型有很多，发型不同类型的事件会调用对应的函数，可以看QWidget的API，上述发生事件部分对照关系已经在注释中写出，下面总结一下主要的事件流，出现的先后顺序就是事件发生顺序： 

MainWindow w;

*   调用构造函数

w.show();

*   QStyle::polish()
*   QWidget::moveEvent()
*   QWidget::resizeEvent()
*   QWidget::showEvent()-----在这里刷新所有嵌入的子页面的size即可实现自适应，注意最好直接用repaint彻底重回，不要用resize这个还需要自己获取尺寸，也不要用update这个是优化后的方法会选择出不变的区域然后进行局部重绘。

a.exec();

*   QWidget::ensurePolished()
*   QWidgetBackingStore::sync()
*   QWidget::paintEvent()

退出：

*   QWidget::closerEvent()

测试源码GitHub**：[QtWidgetsExamples](https://github.com/TechieLiang/QtWidgetsExamples)**