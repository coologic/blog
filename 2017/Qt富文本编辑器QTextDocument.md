---
title: " Qt富文本编辑器QTextDocument\t\t"
tags:
  - qt
  - QTextDocument
url: 726.html
id: 726
categories:
  - Qt
date: 2017-12-18 16:49:07
---

介绍
--

![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fmn5s67tcgj20b609fq2u.jpg) 对于文本编辑，qt提供了很多控件

*   [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html)：单行文本输入，比如用户名密码等简单的较短的或者具有单一特征的字符串内容输入。使用text、settext读写
*   [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html)：富文本编辑器，支持html显示，可以用sethtml/tohtml进行html文本操作或使用，也可利用setPlainText、toPlainText进行纯文本操作
*   [QPlainTextEdit](http://doc.qt.io/qt-5/qplaintextedit.html)：纯文本编辑器，使用了近似于textedit的技术并做了纯文本编辑的优化，并具有文章段落的概念也提供了撤销等功能，但不支持html显示。
*   [QTextBrowser](http://doc.qt.io/qt-5/qtextbrowser.html)：继承于QTextEdit，仅提供显示功能，并提供了超文本导航功能，如果不需要超文本连接只需要使用QTextEdit并设置[QTextEdit::setReadOnly](http://doc.qt.io/qt-5/qtextedit.html#readOnly-prop)

上述都是显示控件，可以确定的是富文本编辑器要用QTextEdit或者QPlainTextEdit，但是肯定不能主动撰写html代码或者逐个处理显示格式实现富文本，实际上Qt提供了相关类：QTextDocument富文本文档、QTextBlock文本快、QTextFrame框架、QTextTable表格、QTextList列表、QTextCursor指针位置、QTextXXXXFormat各种数据类型样式。对于富文本的所有帮助请见官方文档：[Rich Text Processing](http://doc.qt.io/qt-5/richtext.html)

> QTextEdit和QPlainTextEdit选择：差异是QTextEdit提供了tohtml，如果想在处理完文档，直接根据文档生成html作为博客等内容，可以使用此类，没有需要后者即可

**注意关系：QTextDocument>QTextFrame>QTextBlock/QTextTable/QTextList前包含后** 查看两个类的api，均提供了document方法，可以返回QTextDocument指针，用于通过QTextDocument的方式操作文档内容格式，官方范例：

> [Application Example](http://doc.qt.io/qt-5/qtwidgets-mainwindows-application-example.html)这个比较简单 [Syntax Highlighter Example](http://doc.qt.io/qt-5/qtwidgets-richtext-syntaxhighlighter-example.html)语法高亮的例子 [Text Edit Example](http://doc.qt.io/qt-5/qtwidgets-richtext-textedit-example.html)类似于word编辑器的例子 [Calendar Example](http://doc.qt.io/qt-5/qtwidgets-richtext-calendar-example.html)利用富文本编辑器的方式实现日历(不建议学这个毕竟已经有现成的日历控件，而且文档中往往也不会插入日历) [Order Form Example](http://doc.qt.io/qt-5/qtwidgets-richtext-orderform-example.html)根据一些的参数设置生成报表，其实和上面的原理一样

基本使用
----

首先当具有一个edit时，不需要自行创建document，可以直接用document方法可以获取当前edit的document QTextDocument只是一个文档，其内还有根节点，需要使用QTextDocument::rootFlrame获取，设置根节点的边框粗细、颜色就类似于设置word文档边框底纹

### 简单范例

测试源码GitHub**：[QtWidgetsExamples](https://github.com/TechieL/QtWidgetsExamples)**

#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QTextDocument>
#include <QTextFrame>
#include <QTextBlock>
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow) {
    ui->setupUi(this);
    QTextDocument* doc = ui->textEdit->document();
    QTextFrame *root_frame = doc->rootFrame();
    QTextFrameFormat root\_frame\_format = root_frame->frameFormat();//创建框架格式
    root\_frame\_format.setBorderBrush(Qt::darkBlue);//设置边界颜色
    root\_frame\_format.setBorder(5);//设置边界宽度
    root\_frame->setFrameFormat(root\_frame_format); //给框架使用格式
    QTextFrameFormat frame_format;
    frame_format.setBackground(Qt::darkRed);//设置背景色
    frame_format.setMargin(10);//设置边距
    frame_format.setPadding(5);//设置填充
    frame_format.setBorder(2);//设置边界宽度
    frame_format.setBorderStyle(
                QTextFrameFormat::BorderStyle_Solid);//设置边框样式
    frame_format.setPosition(QTextFrameFormat::FloatRight);//右侧
    frame_format.setWidth(QTextLength(
                QTextLength::PercentageLength, 40));//宽度设置
    QTextCursor cursor = ui->textEdit->textCursor();
    cursor.insertText("A company");
    cursor.insertBlock();
    cursor.insertText("321 City Street");
    cursor.insertBlock();
    cursor.insertFrame(frame_format);
    cursor.insertText("Industry Park");
    cursor.insertBlock();
    cursor.insertText("Another country");
}

上述代码仅显示了四行文字，前两行在root跟框架显示，后两行在一个新建的frame中显示，并将frame置于右侧限定了宽度，更多的布局方法请参考：[Order Form Example](http://doc.qt.io/qt-5/qtwidgets-richtext-orderform-example.html) 上述并未对文本格式做设置，可以在insertText的第二个参数直接赋予一个文本格式QTextCharFormat

### QTextCursor光标操作/遍历嵌套Frame/遍历所有Block

首先他有各种instert函数可以插入上面提到的各种文档中的数据类型。有时并不能一次准确的建立好整个文档，需要在中间插入，这样就需要setPosition命令，而positon的具体值可以通过上述各种数据类型的类获取到每个块或者框架或者其他类型开头的positon，也可以通过length获取到当前块的长度用于定位末尾位置。下面提供一个简单范例

#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QTextDocument>
#include <QTextFrame>
#include <QTextBlock>
#include <QDebug>
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow) {
    ui->setupUi(this);
    QTextDocument* doc = ui->textEdit->document();
    QTextFrame *root_frame = doc->rootFrame();
    QTextFrameFormat root\_frame\_format = root_frame->frameFormat();//创建框架格式
    root\_frame\_format.setBorderBrush(Qt::darkBlue);//设置边界颜色
    root\_frame\_format.setBorder(5);//设置边界宽度
    root\_frame->setFrameFormat(root\_frame_format); //给框架使用格式
    QTextFrameFormat frame_format;
    frame_format.setBackground(Qt::darkRed);//设置背景色
    frame_format.setMargin(10);//设置边距
    frame_format.setPadding(5);//设置填充
    frame_format.setBorder(2);//设置边界宽度
    frame_format.setBorderStyle(
                QTextFrameFormat::BorderStyle_Solid);//设置边框样式
    frame_format.setPosition(QTextFrameFormat::FloatRight);//右侧
    frame_format.setWidth(QTextLength(
                QTextLength::PercentageLength, 40));//宽度设置
    QTextCursor cursor = ui->textEdit->textCursor();
    cursor.insertText("A company");
    cursor.insertBlock();
    cursor.insertText("321 City Street");
    cursor.insertFrame(frame_format);
    cursor.insertText("Industry Park");
    cursor.insertBlock();
    cursor.insertText("Another country");
    //遍历frame
    for(auto block = root_frame->begin();!block.atEnd();++block) {
        if(block.currentBlock().isValid()) {
            qDebug()<<block.currentBlock().text();
        }
        else if(block.currentFrame()) {//frame嵌套，范例只有两层所以不递归了
            auto child_frame = block.currentFrame();
            for(auto block2 = child_frame->begin();!block2.atEnd();++block2) {
                if(block.currentBlock().isValid()) {
                    qDebug()<<block2.currentBlock().text();
                }
            }
        }
    }
    //还可以通过root_frame->childFrames()直接获取所字frame
    //遍历文本块
    QTextBlock block = doc->firstBlock();
    for(int i = 0; i < doc->blockCount();i++) {
        qDebug() << QString("block num:%1\\tblock first line number:%2\\tblock length:%3\\ttext:")
                    .arg(i).arg(block.firstLineNumber()).arg(block.length())
                 << block.text();
        block = block.next();
    }
    QTextBlock insert_block = doc->firstBlock().next();
    //在第二行末尾添加
    cursor.setPosition(insert\_block.position()+insert\_block.length()-1);
    cursor.insertText("change cursor postion and insert");
    //在第三行开头添加-也就是新frame里面最开始添加
    //方法一，第二行末尾+1就是第三行开头
    cursor.setPosition(insert\_block.position()+insert\_block.length());
    //方法二，position默认返回的就是一个块开头
    cursor.setPosition(insert_block.next().position());
    //方法三,利用frame，frame是在一个锚点定位，开头在第二行末尾所以必须加一
    cursor.setPosition(frame_format.position()+1);
    cursor.insertText("change cursor postion and insert");
}

前面的内容没有变化，后面先展示了如何遍历所有frame以及嵌套的frame。如果是全文检索或者整体修改也可以直接遍历全文所有block