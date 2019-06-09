---
title: " Qt多线程-QThread\t\t"
tags:
  - qt
  - qthread
  - 多线程
url: 592.html
id: 592
categories:
  - Qt
date: 2017-12-09 22:38:52
---

介绍
--

QThread是Qt提供的线程类，每一个QThread均可管理一个线程。 其具有两种使用方式：1、继承为QThread的子类；2、继承为QObject的子类，并使用QObject::moveToThread将此对象移到线程中运行 QThread提供了如下基本函数： 线程启动：start()运行一次 线程终止：terminate 终止线程，强制终止线程但会依据操作系统的调度策略，可能不是立即终止，最好用wait等待 quit退出线程，也可以调用exit，效果相同，会正常终止线程。 线程状态查询：isRunning是否正在运行,isFinished是否运行完成 线程状态信号：started线程启动时发出，finished线程结束时发出 其他：wait阻塞方式等待线程结束，调用此函数会将调用指令所在函数阻塞

> 建议对finished信号建立对应槽，实现线程结束后操作，而不是使用wait等待

更多详细说明见[官方文档](http://doc.qt.io/qt-5/qthread.html)

### 线程优先级

start函数有一个参数是线程优先级，此处使用的默认参数，若未设置也可以调用setPriority函数设置优先级，优先级分为以下几类：

Constant

Value

Description

`QThread::IdlePriority`

`0`

scheduled only when no other threads are running.

`QThread::LowestPriority`

`1`

scheduled less often than LowPriority.

`QThread::LowPriority`

`2`

scheduled less often than NormalPriority.

`QThread::NormalPriority`

`3`

the default priority of the operating system.

`QThread::HighPriority`

`4`

scheduled more often than NormalPriority.

`QThread::HighestPriority`

`5`

scheduled more often than HighPriority.

`QThread::TimeCriticalPriority`

`6`

scheduled as often as possible.

`QThread::InheritPriority`

`7`

use the same priority as the creating thread. This is the default.

### 线程休眠

sleep秒休眠、msleep毫秒休眠、usleep微秒休眠

基本使用
----

### 建立QThread子类法

//mythread.h
#pragma once
#include <QThread>
#include <QDebug>
class MyThread : public QThread {
    Q_OBJECT
protected:
    void run() {
        while(1) {
            num++;
            qDebug()<<num<<"thread start:"<<QThread::currentThreadId();
            msleep(500);
            qDebug()<<num<<"thread end:"<<QThread::currentThreadId();
        }
    }
private:
    int num = 0;
};
//main.cpp
#include <QCoreApplication>
#include <QThread>
#include <QDebug>
#include "mythread.h"
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    qDebug()<<"Main:"<<QThread::currentThreadId();
    MyThread m;
    m.start();
    QThread::sleep(5);
    m.terminate();
    m.wait();
    return 0;
}

上述代码测试了线程启动、强制停止以及currentthreadid获取当前线程id。 还可以使用currentthread获取当前线程指针函数 上述使用terminate强制关闭线程，此行为不建议使用，安全结束方式请见[http://techieliang.com/2017/12/599/](http://techieliang.com/2017/12/599/)

### moveToThread方法

#pragma once
#include <QThread>
#include <QDebug>
class MyThread : public QObject {
    Q_OBJECT
public slots://注意要用槽函数
    void start() {
            qDebug()<<"thread end:"<<QThread::currentThreadId();
    }
};
#include <QCoreApplication>
#include <QThread>
#include <QDebug>
#include "mythread.h"
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    qDebug()<<"Main:"<<QThread::currentThreadId();
    QThread thread;
    MyThread m;
    m.moveToThread(&thread);
    QObject::connect(&thread,SIGNAL(started()),&m,SLOT(start()));
    thread.start();
    return 0;
}

线程同步
----

### QMutex互斥量

[帮助文档](http://doc.qt.io/qt-5/qmutex.html) 通过lock,unlock实现加锁、解锁 使用tryLock尝试加锁，会返回加锁成功与否，同时可设置超时时间。

> 注意在lock以后，任意return前必须进行unlock，否则会造成死锁

### QMutexLocker

建立一个QMutex，通过QMutexLocker locker(&mutex);可以实现对mutex的自动处理，后续不需要自行进行lock和unlock，避免多个return情况下出现遗忘。 [帮助文档](http://doc.qt.io/qt-5/qmutexlocker.html)范例： 只是用QMutex的代码：

int complexFunction(int flag) {
    mutex.lock();

    int retVal = 0;

    switch (flag) {
    case 0:
    case 1:
        retVal = moreComplexFunction(flag);
        break;
    case 2:  {
            int status = anotherFunction();
            if (status < 0) {
                mutex.unlock();
                return -2;
            }
            retVal = status + flag;
        }
        break;
    default:
        if (flag > 10) {
            mutex.unlock();
            return -1;
        }
        break;
    }

    mutex.unlock();
    return retVal;
}

使用QMutexLocker 代码

int complexFunction(int flag) {
    QMutexLocker locker(&mutex);
    int retVal = 0;
    switch (flag) {
    case 0:
    case 1:
        return moreComplexFunction(flag);
    case 2: {
            int status = anotherFunction();
            if (status < 0)
                return -2;
            retVal = status + flag;
        }
        break;
    default:
        if (flag > 10)
            return -1;
        break;
    }
    return retVal;
}

### QReadWriteLock

使用QMutex无论对变量进行何种操作（读写）均会锁定变量，而实际上对于读取变量值并不需要等待，可以多个线程同时读取，此时使用**QReadWriteLock**可以实现多线程读，单线程写的操作。[帮助文档](http://doc.qt.io/qt-5/qreadwritelock.html) 范例：

QReadWriteLock lock;
void ReaderThread::run() {
    ...
    lock.lockForRead();
    read_file();
    lock.unlock();
    ...
}

void WriterThread::run() {
    ...
    lock.lockForWrite();
    write_file();
    lock.unlock();
    ...
}

### QReadLocker和QWriteLocker

对于QReadWriteLock，qt也提供了类似于QMutexLocker的类，提供了对QReadWriteLock对象的托管，具体请见官方文档： [QReadLocker](http://doc.qt.io/qt-5/qreadlocker.html)???[QWriteLocker](http://doc.qt.io/qt-5/qwritelocker.html)

### QSemaphore

帮助文档：[http://doc.qt.io/qt-5/qsemaphore.html](http://doc.qt.io/qt-5/qsemaphore.html) Qt提供的信号量，相比于互斥量只能锁定一次，信号量可以获取多次，它可以用来保护一定数量的同种资源，可用于对缓冲区的管理。

QSemaphore sem(5);      // sem.available() == 5
sem.acquire(3);         // sem.available() == 2
sem.acquire(2);         // sem.available() == 0
sem.release(5);         // sem.available() == 5
sem.release(5);         // sem.available() == 10
sem.tryAcquire(1);      // sem.available() == 9, returns true
sem.tryAcquire(250);    // sem.available() == 9, returns false

### QWaitCondition

帮助文档：[http://doc.qt.io/qt-5/qwaitcondition.html](http://doc.qt.io/qt-5/qwaitcondition.html) 提供了wait和wake方法，不光操作信号量还会主动唤醒其他线程，而且同一个变量可以控制多个线程，通过wakeone系统会随机唤醒一个等待的线程，通过wakeall会唤醒所有。此类不可单独使用需要配合QReadWriteLock或者QMutex，需要作为参数传递给QWaitCondition。

其他
--

### 线程结束后自动销毁的方法

connect(&thread, SIGNAL(finished()), &thread, SLOT(deleteLater())); 直接将线程结束的信号与deleteLater槽连接即可，deleteLater是QObject的槽函数