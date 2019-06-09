---
title: " QThread安全的结束线程\t\t"
tags:
  - qt
  - qthread
  - 多线程
url: 599.html
id: 599
categories:
  - Qt
date: 2017-12-10 12:26:04
---

QThread使用
---------

基本使用请见：[http://techieliang.com/2017/12/592/](http://techieliang.com/2017/12/592/) 在上文中提到了一个简单范例：

#include <QCoreApplication>
#include <QThread>
#include <QDebug>
class MyThread : public QThread {
protected:
    void run() {
        while(1) {
            qDebug()<<"thread start:"<<QThread::currentThreadId();
            msleep(500);
        }
    }
};
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

此范例使用terminate强制关闭线程，此行为是很危险的，下面提供一种安全的关闭方式

#include <QCoreApplication>
#include <QThread>
#include <QDebug>
class MyThread : public QThread {
public:
    void stop() { //用于结束线程
        is_runnable =false;
    }
protected:
    void run() {
        while(is_runnable) {
            qDebug()<<"thread start:"<<QThread::currentThreadId();
            msleep(500);
        }
        is_runnable = true;
    }
private:
    bool is_runnable = true; //是否可以运行
};
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    qDebug()<<"Main:"<<QThread::currentThreadId();
    MyThread m;
    m.start();
    QThread::sleep(5);
    m.stop();
    m.wait();
    return 0;
}

通过对while循环增加bool类型作为判断实现安全的结束线程，当is_runnable=false时，程序会完成此次循环后结束，所以要wait等待，不可直接关闭程序。