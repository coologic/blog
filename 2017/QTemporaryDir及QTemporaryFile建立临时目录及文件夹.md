---
title: " QTemporaryDir及QTemporaryFile建立临时目录及文件夹\t\t"
tags:
  - qt
  - QTemporaryDir
  - QTemporaryFile
  - 临时
url: 672.html
id: 672
categories:
  - Qt
date: 2017-12-12 16:30:29
---

介绍
--

还是老套路，上官方文档地址：[QTemporaryDir](http://doc.qt.io/qt-5/qtemporarydir.html)、[QTemporaryFile](http://doc.qt.io/qt-5/qtemporaryfile.html) 两者都是在构造时创建一个随机名称的目录或文件，并在其销毁时自动删除对应的目录和文件，同时两者均能保证不会覆盖已有文件。 实例化时若不传递参数则随机确定名称，若传入名称会优先尝试指定名称若此名称已存在文件则会随机创建。 file的父类是QFile，可以进行QFile的所有操作。 但要注意Dir的父类只是QObject并不是QDir，也很正常，毕竟只是个临时文件夹不需要其他操作。

QTemporaryDir
-------------

### 接口说明

QTemporaryDir()
QTemporaryDir(const QString &templatePath)
~QTemporaryDir()
bool autoRemove() const
QString errorString() const
QString filePath(const QString &fileName) const
bool isValid() const
QString path() const
bool remove()
void setAutoRemove(bool b)

注意构造函数说明，支持相对路径：

> If templatePath is a relative path, the path will be relative to the current working directory.

同时注意对于路径末尾字符的说明，文件夹路径会直接在指定路径之后添加随机字符，如果指定路径最后不是/则为指定名称+XXX构成新路径名，如果最后是/则在前面的路径后新建一个随机目录，**注意此时必须保证前面的文件夹都存在否则出错，见后面的范例**：

> If the templatePath ends with XXXXXX it will be used as the dynamic portion of the directory name, otherwise it will be appended. Unlike QTemporaryFile, XXXXXX in the middle of the template string is not supported.

remove可以主动提前删除目录，注意会删除目录下所有文件，毕竟是临时目录不要存有用的东西

> Removes the temporary directory, including all its contents.

autoremove默认是true 最后，[filePath](qtemporarydir.html#filePath)可以获取文件的路径名，这个比较特殊，需要传入一个文件名，此函数返回一个完整的路径名。这样可以用于后续的QTemporaryFile。

### 范例

#include <QCoreApplication>
#include <QDebug>
#include <QTemporaryFile>
#include <QTemporaryDir>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc,argv);
    QTemporaryDir testdir1;
    qDebug()<<testdir1.autoRemove();
    qDebug()<<testdir1.filePath("123.txt");
    QTemporaryDir testdir2("testdir2");
    qDebug()<<testdir2.autoRemove();
    qDebug()<<testdir2.filePath("123.txt");
    QTemporaryDir testdir3("testdir3/");
    //注意这样等于是指定在当前运行目录下的testdir3目录下建立一个随机名称的目录
    //如果testdir3文件夹不存在将会出错
    qDebug()<<testdir3.autoRemove();
    qDebug()<<testdir3.filePath("123.txt");
    return 0;
}

结果

true
"C:/Users/XXXXXX/AppData/Local/Temp/untitled-GN2aKw/123.txt"
true
"testdir2bE7tFd/123.txt"
true
"testdir3/zxPK5t/123.txt"

XXXXXX是当前系统登录的用户名，也就是默认目录自动指向了系统默认的临时文件地址。

> 最后的testdir3目录下建立临时目录，最后删除的只是临时目录及其下所有文件，并不会删除testdir3文件夹

QTemporaryFile
--------------

### 接口说明

QTemporaryFile()
QTemporaryFile(const QString &templateName)
QTemporaryFile(QObject *parent)
QTemporaryFile(const QString &templateName, QObject *parent)
~QTemporaryFile()
bool autoRemove() const
QString fileTemplate() const
bool open()
void setAutoRemove(bool b)
void setFileTemplate(const QString &name)

构造函数可以传入一个临时文件名，这个名称就可以用QTemporaryDir::filePath实现在临时目录建立一个指定文件名的临时文件。（如果这个文件已经存在那么还是会建立一个随机名称的） fileTemplate是文件名，临时文件的实现原理是文件名后面加上一个".XXX"随机名称，所以前面的文件名可以随机指定

### 范例

#include <QCoreApplication>
#include <QDebug>
#include <QTemporaryFile>
#include <QTemporaryDir>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc,argv);
    QTemporaryFile testfile1;//建立第一个文件
    qDebug()<<"testfile1"<<testfile1.fileName()
           <<testfile1.fileTemplate();
    //第一次open之前文件是没有建立的，所以没名字
    testfile1.open();
    testfile1.close();
    qDebug()<<"testfile1"<<testfile1.fileName()
           <<testfile1.fileTemplate();
    //只要对象不被销毁，可以重复open，不会变文件
    testfile1.open();
    qDebug()<<"testfile1"<<testfile1.fileName()
           <<testfile1.fileTemplate();
    //指定名字
    QTemporaryFile testfile2("testfile2");
    testfile2.open();
    qDebug()<<"testfile2"<<testfile2.fileName()
           <<testfile2.fileTemplate();
    //建立一个重名的
    QTemporaryFile testfile3("testfile2");
    testfile3.open();
    qDebug()<<"testfile3"<<testfile3.fileName()
           <<testfile3.fileTemplate();
    //在QTemporaryDir临时目录下建立一个
    QTemporaryDir testdir1;
    qDebug()<<"testdir1"<<testdir1.filePath("testfile4.txt");
    QTemporaryFile testfile4(testdir1.filePath("testfile4.txt"));
    testfile4.open();
    qDebug()<<"testfile4"<<testfile4.fileName()
           <<testfile4.fileTemplate();
    //注意最后一个就算文件名定义了txt后缀，夜壶自动在后面加.xxxxxx
    //QTemporaryFile继承了QFile，在open以后可以直接进行QFile的所有操作
    return 0;
}

结果

testfile1 "" "C:/Users/XXXXX/AppData/Local/Temp/untitled.XXXXXX"
testfile1 "C:/Users/XXXXX/AppData/Local/Temp/untitled.Hp9316" "C:/Users/zhouliang/AppData/Local/Temp/untitled.XXXXXX"
testfile1 "C:/Users/XXXXX/AppData/Local/Temp/untitled.Hp9316" "C:/Users/zhouliang/AppData/Local/Temp/untitled.XXXXXX"
testfile2 "D:/my\_program\_design/untitled/build-untitled-Desktop\_Qt\_5\_9\_2\_MinGW\_32bit-Debug/testfile2.gq9316" "testfile2"
testfile3 "D:/my\_program\_design/untitled/build-untitled-Desktop\_Qt\_5\_9\_2\_MinGW\_32bit-Debug/testfile2.Uh9316" "testfile2"
testdir1 "C:/Users/XXXXX/AppData/Local/Temp/untitled-i9GN2a/testfile4.txt"
testfile4 "C:/Users/XXXXX/AppData/Local/Temp/untitled-i9GN2a/testfile4.txt.lY9316" "C:/Users/zhouliang/AppData/Local/Temp/untitled-i9GN2a/testfile4.txt"

file的父类是QFile，可以进行QFile的所有操作。上述范例没有演示读写操作。