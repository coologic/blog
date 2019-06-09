---
title: " Qt动态连接库/静态连接库创建与使用，QLibrary动态加载库\t\t"
tags:
  - dll
  - lib
  - qt
url: 680.html
id: 680
categories:
  - Qt
date: 2017-12-13 21:33:20
---

动态连接库创建与使用
----------

### 项目创建

![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fmfdzv7uqxj20gg06xglv.jpg) 注意选择成shared library ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fmfdzvoe7sj20dv02hgle.jpg) 此时新建的项目pro文件：

QT       -= gui
TARGET = library
TEMPLATE = lib
DEFINES += LIBRARY_LIBRARY
DEFINES += QT\_DEPRECATED\_WARNINGS
SOURCES += \
        library.cpp
HEADERS += \
        library.h
unix {
    target.path = /usr/lib
    INSTALLS += target
}

注意其Template为lib，且声明了一个LIBRARY_LIBRARY 对于global.h文件建议直接拷贝到library.h，这样发布的时候只需要给别人一个.h文件 library.h

#ifndef LIBRARY_H
#define LIBRARY_H
#include <QtCore/qglobal.h>
#if defined(LIBRARY_LIBRARY)
\#  define LIBRARYSHARED\_EXPORT Q\_DECL_EXPORT
#else
\#  define LIBRARYSHARED\_EXPORT Q\_DECL_IMPORT
#endif
class LIBRARYSHARED_EXPORT Library {
public:
    Library();
    int sum(int a, int b);
};
int LIBRARYSHARED_EXPORT sum(int a, int b);
#endif // LIBRARY_H

pro声明的宏在这里用上了，做了一个判断，如果有定义则LIBRARYSHARED\_EXPORT=Q\_DECL\_EXPORT，否则等于Q\_DECL\_IMPORT，也就是说在这个lib项目里是导出的意思，在其他项目因为给别人的只有.h文件并没有LIBRARY\_LIBRARY的定义，所以是导入。从而实现不做任何修改即可发布.h文件。 将global.h拷贝到library.h也是为了只提供一个文件，否则若忘记了提供global.h调用方会提示缺少文件。 library.cpp

Library::Library() {
}
int Library::sum(int a, int b) {
    return a+b;
}
int sum(int a, int b) {
    return a+b;
}

此时直接Ctrl+B构建即可liblibrary.a、library.dll、library.o三个文件(MinGW版，VS的会有lib文件)，提供给调用方*.h和*.dll文件即可(windows，linux共享库是*.so)

> 注意生成库也区分debug和release，debug的库内带有调试代码，一般debug的库文件名最后是d也就是

### 调用-使用.h文件

建立一个Qt Console Application项目，将library.dll和library.h文件拷贝到项目目录下(和新项目的main.cpp在一起即可) 默认pro文件：

QT -= gui
CONFIG += c++11 console
CONFIG -= app_bundle
DEFINES += QT\_DEPRECATED\_WARNINGS
SOURCES += main.cpp

在打开的pro项目右键，选择添加库(Add library），可以把dll文件包含到项目里，如果不包含此处选择外部库(External Library) 在pro文件最后添加

LIBS += library.dll

简单的写法是上面的样子，建议使用完整的写法：

LIBS += -LD:/my\_program\_design/dll\_test/test\_library\_by\_header/ -llibrary

+=前后允许有空格? -L和路径名不可有空格?? -l前面有空格后面不可有空格要紧跟文件名?? 文件名不需要后缀，系统会自动识别是dll还是lib

> 文件名是library，前面加了个-l变成了-llibrary，别忘了-l

main.cpp

#include <QCoreApplication>
#include <library.h>
#include <QDebug>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    qDebug()<<sum(1,2);//测试c函数
    Library t;
    qDebug()<<t.sum(1,2);//测试类函数
    return 0;
}

此时运行，可以生成成功，但是会报错，因为dll文件还需要拷贝到生成的exe文件目录，拷贝后再运行即可

静态库创建及使用
--------

### 创建

见动态库第二图，创建时不要选择shared，选择静态连接库Statically Linked Library。 创建项目以后没有什么特色，不会有global.h文件也不会有一个export、import的定义，因为静态库不需要导入导出，生成库提供给使用者，使用者在编译时会将代码需要的代码编译到自己的项目中，不需要附带dll/lib等文件。 先看pro文件：

QT       -= gui
TARGET = static_library
TEMPLATE = lib
CONFIG += staticlib
SOURCES += \
        static_library.cpp
HEADERS += \
        static_library.h
unix {
    target.path = /usr/lib
    INSTALLS += target
}

相比于动态链接库差异是增加了CONFIG += staticlib 删掉了DEFINES .h和.cpp文件和动态库一样，只不过没有了LIBRARYSHARED\_EXPORT 然后运行就会生成文件libstatic\_library.a和static_library.o文件(MinGW，VS是lib文件)

### 使用

和动态库一样新建个项目，把libstatic\_library.a或者lib文件以及static\_library.h考到项目 在pro文件增加：

LIBS += -LD:/my\_program\_design/dll\_test/test\_static\_library/ -llibstatic\_library

main.cpp文件：

#include <QCoreApplication>
#include <static_library.h>
#include <QDebug>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    qDebug()<<sum(1,2);
    Static_library l;
    qDebug()<<l.sum(1,2);
    return 0;
}

运行即可，这里就不需要吧.a文件复制到程序目录了，因为静态库编译的时候已经把需要的内容编译到exe程序了。

QLibrary动态加载动态库
---------------

### 介绍

在动态库使用那里，直接编译运行会无法打开，把dll文件拷贝到运行目录才能打开程序，否则会提示缺少文件，然后就没有然后了。。。 不知道是否注意到有些程序是主动提示缺少文件错误的？会在程序运行到需要库文件时提示缺少此库，并且可以自定义提示内容，而不是用系统默认的错误提示，若想实现此功能需要动态加载。当然在库供应方只给了dll文件没给.h的时候也能使用，换句话说动态加载方式在编写项目时不需要.h文件，也不需要在pro文件增加“libs+=” 先看帮助文档：[http://doc.qt.io/qt-5/qlibrary.html](http://doc.qt.io/qt-5/qlibrary.html) 接口很简单：

QLibrary(QObject *parent = Q_NULLPTR)
QLibrary(const QString &fileName, QObject *parent = Q_NULLPTR)
QLibrary(const QString &fileName, int verNum, QObject *parent = Q_NULLPTR)
QLibrary(const QString &fileName, const QString &version, QObject *parent = Q_NULLPTR)
~QLibrary()
QString errorString() const
QString fileName() const
bool isLoaded() const
bool load()
LoadHints loadHints() const
QFunctionPointer resolve(const char *symbol)
void setFileName(const QString &fileName)
void setFileNameAndVersion(const QString &fileName, int versionNumber)
void setFileNameAndVersion(const QString &fileName, const QString &version)
void setLoadHints(LoadHints hints)
bool unload()

注意load用完了记着unload，还可以做version版本判断，其他的不说了，直接看简单范例。

### 范例

不需要吧dll文件放到工程目录就行，因为编译的时候不会访问它，只需要放到运行目录即可

#include <QCoreApplication>
#include <QString>
#include <QDebug>
#include <QLibrary>
typedef int (*myfun)(int, int);//定义函数格式
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QLibrary test_dll("library.dll");//加载dll
    if(test_dll.load()) {//判断是否加载成功
        myfun fun1 = (myfun)test_dll.resolve("sum");//获取dll的函数
        if (fun1) {//判断是否获取到此函数
            double result;
            result=fun1(1,2);//和正常调用函数一样了
            qDebug()<<QString(QStringLiteral("load ok, result:"))+
                      QString::number(result);
        }
        else {
            //函数解析失败
            qDebug()<<QStringLiteral("dll function load error");
        }
    }
    else {
        qDebug()<<QStringLiteral("dll load error");//dll文件加载失败
    }
    return 0;
}

范例很简单，然后运行就会发现给出的结果是：dll function load error 此时不要盲目的去找检查typedef 的错误，其实这个程序没错，错的是之前的dll项目。 因为c++为了适应函数重载，对于函数名称在编译过程中会做一定的修改，所以此时找sum肯定找不到，应该修改动态库的项目文件，把头文件改成：

#ifndef LIBRARY_H
#define LIBRARY_H
#include <QtCore/qglobal.h>
#if defined(LIBRARY_LIBRARY)
\#  define LIBRARYSHARED\_EXPORT Q\_DECL_EXPORT
#else
\#  define LIBRARYSHARED\_EXPORT Q\_DECL_IMPORT
#endif
class LIBRARYSHARED_EXPORT Library {
public:
    Library();
    int sum(int a, int b);
};
extern "C" int LIBRARYSHARED_EXPORT sum(int a, int b);
#endif // LIBRARY_H

> extern "C" 包含双重含义，从字面上即可得到：首先，被它修饰的目标是“extern”的；其次，被它修饰的目标是“C”的。让我们来详细解读这两重含义。 被extern "C"限定的函数或变量是extern类型的：extern是C/C++语言中表明函数和全局变量作用范围（可见性）的关键字，该关键字告诉编译器，其声明的函数和变量可以在本模块或其它模块中使用。同时"C"又告诉编译器以C语言方式编译和连接。

把新生成的文件拷贝到运行目录即可得到"load ok, result:3"

### 比较extern"C"的dll与原始dll的差别

用depends工具查看dll文件，我把新的命名为library.dll旧的命名为library2.dll看图对比即可：   ![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fmfgryx6z6j20l90azq5u.jpg) ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fmfgrzxn1ej20nj0b8mzr.jpg)