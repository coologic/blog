---
title: " QLayout窗口布局\t\t"
tags:
  - layout
  - qt
url: 690.html
id: 690
categories:
  - Qt
date: 2017-12-14 11:56:11
---

介绍
--

QLayout

Header:

#include <QLayout>

qmake:

QT += widgets

Inherits:

[QObject](http://doc.qt.io/qt-5/qobject.html) and [QLayoutItem](http://doc.qt.io/qt-5/qlayoutitem.html)

Inherited By:

[QBoxLayout](http://doc.qt.io/qt-5/qboxlayout.html), [QFormLayout](http://doc.qt.io/qt-5/qformlayout.html), [QGridLayout](http://doc.qt.io/qt-5/qgridlayout.html), and [QStackedLayout](http://doc.qt.io/qt-5/qstackedlayout.html)

涉及到的控件主要有：[QSplitter](http://doc.qt.io/qt-5/qsplitter.html)窗口分割器、[QSpacerItem](http://doc.qt.io/qt-5/qspaceritem.html)[间距控制](http://doc.qt.io/qt-5/qspaceritem.html)(类似于弹簧效果)、[QHBoxLayout](http://doc.qt.io/qt-5/qhboxlayout.html)(1行n列)和[QVBoxLayout](http://doc.qt.io/qt-5/qvboxlayout.html)(n行1列)行列布局、[QFormLayout](http://doc.qt.io/qt-5/qformlayout.html)表单布局(n行2列)、[QGridLayout](http://doc.qt.io/qt-5/qgridlayout.html)栅格布局(n行n列) **[addWidget](http://doc.qt.io/qt-5/qlayout.html#addWidget)**(QWidget *_w_)、**[removeWidget](http://doc.qt.io/qt-5/qlayout.html#removeWidget)**(QWidget *_widget_)? QWidget操作 setSpacing(int spacing) setHorizontalSpacing(int spacing) setVerticalSpacing(int spacing)设置间距

### QSpacerItem

在使用Designer时，就是Spacers里面的行列spacer，弹簧样式的图标，此控件添加以后不会在界面显示，主要是占位使用。任何layout默认是先符合控件的sizePolicy的要求下进行控件大小、间距调整。 但如果想要实现类似于程序标题栏的效果，左侧图标、程序名，右侧最大化、最小化关闭按钮，中间就需要一个占位的空白控件，这时候需要使用QSpacerItem。 在Designer时，直接拖拽到需要占位的地方（注意，两个空间之间或者布局之间均可，但其所在空间必须是QLayout而不是QWidget） 代码使用：使用**[addSpacerItem](http://doc.qt.io/qt-5/qboxlayout.html#addSpacerItem)**(QSpacerItem *_spacerItem_)、[insertSpacerItem](qboxlayout.html#insertSpacerItem)(int index, QSpacerItem *spacerItem)、**[removeItem](http://doc.qt.io/qt-5/qlayout.html#removeItem)**(QLayoutItem *_item_)

> **[addSpacing](http://doc.qt.io/qt-5/qboxlayout.html#addSpacing)**(int _size_)这类方法是设置间距而不是插入spaceritem spacerItem父类是QLayoutItem，直接removeQLayoutItem 即可删除，同理可以使用**[removeItem](http://doc.qt.io/qt-5/qlayout.html#removeItem)**(QLayoutItem *_item_)、

### QHBoxLayout、QVBoxLayout

其父类为[QBoxLayout](qboxlayout.html)，可以配合QSpacerItem使用

### QFormLayout

n行两列表单，提供了一套**[insertRow](http://doc.qt.io/qt-5/qformlayout.html#insertRow)**、**[removeRow](http://doc.qt.io/qt-5/qformlayout.html#removeRow)**、**[addRow](http://doc.qt.io/qt-5/qformlayout.html#addRow-4)**的方法，此类默认第一列为QLabel，支持第一列只提供字符串而不提供QLabel对象

#### 表单换行策略

setRowWrapPolicy(RowWrapPolicy policy)

Constant

Value

Description

`QFormLayout::DontWrapRows`

`0`

一直在一行Fields are always laid out next to their label. This is the default policy for all styles except Qt Extended styles.

`QFormLayout::WrapLongRows`

`1`

自适应，如果空间不够则两行Labels are given enough horizontal space to fit the widest label, and the rest of the space is given to the fields. If the minimum size of a field pair is wider than the available space, the field is wrapped to the next line. This is the default policy for Qt Extended styles.

`QFormLayout::WrapAllRows`

`2`

一直两行Fields are always laid out below their label.

#### setWidget(int row, ItemRole role, QWidget *widget)

不使用addrow一类的整行添加，也可以逐个添加，使用此函数需要设置ItemRole

Constant

Value

Description

`QFormLayout::LabelRole`

`0`

标签列A label widget.

`QFormLayout::FieldRole`

`1`

输入框列A field widget.

`QFormLayout::SpanningRole`

`2`

单控件占用整行A widget that spans label and field columns.

### QGridLayout

适用于复杂布局

void addLayout(QLayout *layout, int row, int column, Qt::Alignment alignment = Qt::Alignment())
void addLayout(QLayout *layout, int row, int column, int rowSpan, int columnSpan, Qt::Alignment alignment = Qt::Alignment())
void addWidget(QWidget *widget, int row, int column, Qt::Alignment alignment = Qt::Alignment())
void addWidget(QWidget *widget, int fromRow, int fromColumn, int rowSpan, int columnSpan, Qt::Alignment alignment = Qt::Alignment())

注意row,column为起点位置，rowSpan，columnSpan表示占用的行数，类似于excel中的合并单元格效果

void setColumnStretch(int column, int stretch)
void setRowStretch(int row, int stretch)
void setHorizontalSpacing(int spacing)
void setVerticalSpacing(int spacing)

设置伸缩空间和间距

### QStackedLayout

堆布局，这个布局实现widget分页显示，Qt提供了他的一个控件[QStackedWidget](http://doc.qt.io/qt-5/qstackedwidget.html)

### QSplitter

实现窗口分割效果，且可以动态调整比例 **[setOpaqueResize](http://doc.qt.io/qt-5/qsplitter.html#opaqueResize-prop)**(bool _opaque_ = true) 在调整比例时是否动态更新 **[setChildrenCollapsible](http://doc.qt.io/qt-5/qsplitter.html#childrenCollapsible-prop)**(_bool_) 是否允许子窗口为0尺寸 **[addWidget](http://doc.qt.io/qt-5/qsplitter.html#addWidget)**(QWidget *_widget_)、**[insertWidget](http://doc.qt.io/qt-5/qsplitter.html#insertWidget)**(int _index_, QWidget *_widget_) 添加窗口 注意是窗口分割 不是布局分割，所以不能支持布局的添加