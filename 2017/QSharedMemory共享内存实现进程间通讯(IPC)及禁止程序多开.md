---
title: " QSharedMemory共享内存实现进程间通讯(IPC)及禁止程序多开\t\t"
tags:
  - ipc
  - qt
  - 共享内存
url: 685.html
id: 685
categories:
  - Qt
date: 2017-12-13 22:34:42
---

介绍
--

很简单的库，直接看帮助文档：[http://doc.qt.io/qt-5/qsharedmemory.html](http://doc.qt.io/qt-5/qsharedmemory.html) 主要函数：设置key，create向系统申请建立一个内存空间、attach当前进程与内存绑定、detach解除绑定，lock/unlock同步锁，data/constdata获取内存指针 创建者流程：setkey,create,attach,lock,data,操作data,unlock,不用的时候detach 访问者：setkey,attach,lock,data,操作data,unlock,不用的时候detach?? 不需要create了

*   作为创建者应该确定别人也不用了再解绑
*   QSharedMemory析构是也会自动detach
*   一个内存空间如果0个attach时会被销毁，数据就没了
*   读写操作记着lock，注意不要忘了unlock
*   没有create的key，在调用attach时会返回false，注意这句在禁止程序多开有用

size获取共享内存大小，error/errorString是错误信息，isAttached判断当前进程是否已经绑定到内存。

范例
--

源码请见GitHub：**[QtCoreExamples](https://github.com/TechieL/QtCoreExamples)** 偷个懒，写到一起了：

#include <QCoreApplication>
#include <QSharedMemory>
#include <QDebug>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    //创建的同时设置key,也可以setKey
    QSharedMemory sm("test_shared");
    //如果是第一个使用必须先创建
    //其余进程不需要创建直接attach
    if(!sm.create(1024))
        qDebug()<<"create error";
    sm.attach();//绑定内存
    //attach会返回bool，可以不用下面方式判断
    if(!sm.isAttached())
        qDebug()<<"attach error";
    sm.lock();
    int \*memdata = static_cast<int\*>( sm.data() );
    *memdata = 1024;
    sm.unlock();
    //如此偷懒！想要测试多进程把下面拷走建项目
    QSharedMemory testsm("test_shared");
    testsm.attach();
    int \*testdata = static_cast<int\*>( testsm.data() );
    qDebug()<<*testdata;
    //使当前进程与内存分离，析构的时候也会自动调用
    sm.detach();
    return a.exec();
}

testsm析构时会调用detach，可以吧sm.detach();放在QSharedMemory testsm("test_shared");? 就会看到出错了

其他IPC方法
-------

D-Bus模块：Qt提供的Unix使用的模块，用这个模块信号槽都可以用，快换系统呀，我这win没法测试 TCP/IP：万能的，看我的博客：[QTcpSocket-Qt使用Tcp通讯实现服务端和客户端](http://techieliang.com/2017/12/530/)。 QProcess：打开程序的时候传个参数，前提是要通讯的那个程序有当前程序主动打开，然后让它作为子进程就可以随便摆布了 除了直接用这个还有Qt Network模块还提供了 [QLocalServer](http://doc.qt.io/qt-5/qlocalserver.html)和[QLocalSocket](http://doc.qt.io/qt-5/qlocalsocket.html)实现本地通讯，在win下使用的是有名管道

> On Windows this is a named pipe and on Unix this is a local domain socket.

提到了有名管道，就说一下通讯方式：管道，信号量，消息队列，共享内存，信号，套接字。 介绍跳转到别人的博客：[https://www.cnblogs.com/tureno/articles/6918902.html](https://www.cnblogs.com/tureno/articles/6918902.html)

禁止程序多开
------

#include <QCoreApplication>
#include <QSharedMemory>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    //创建的同时设置key,也可以setKey
    QSharedMemory sm("test_shared");
    if(sm.attach())
        return 0;
    sm.create(1);
    MainWindow w;
    w.show();
    return a.exec();
}

很简单的原理，只要有一个开启成功那就会create一个1大小的空间，后续再开程序就能attach了然后就return了……