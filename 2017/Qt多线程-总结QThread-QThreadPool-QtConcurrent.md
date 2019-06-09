---
title: " Qt多线程-总结QThread-QThreadPool-QtConcurrent\t\t"
tags:
  - qt
  - QtConcurrent
  - qthread
  - QThreadPool
  - 多线程
url: 616.html
id: 616
categories:
  - Qt
date: 2017-12-10 22:29:50
---

总结
--

QThread：Qt提供的最基础的线程类，一个对象管理一个线程，自己维护线程启动停止，创建销毁，当然也能基于此类自己建立一个线程池 QThreadPool：Qt提供的基于QThread实现的线程池，只需要提供给线程池“任务”即可，每一个“任务”需要继承QRunnable，pool还贴心的帮忙在运行完成后释放内存。只不过runnable不支持信号槽，可以做多重继承QObject即可。 QtConcurrent：并行计算的高级API，用起来很方便，完全不需要想线程的问题，全都是静态函数，可以运行自定义函数也提供了对容器的操作函数。 相关博客： [Qt多线程-QThread](http://techieliang.com/2017/12/592/ "Qt多线程-QThread-Techie亮博客") [QThread安全的结束线程](http://techieliang.com/2017/12/599/ "QThread安全的结束线程-Techie亮博客") [Qt多线程-QThreadPool线程池与QRunnable](http://techieliang.com/2017/12/605/ "Qt多线程-QThreadPool线程池与QRunnable-Techie亮博客") [Qt多线程-QtConcurrent并行运算高级API](http://techieliang.com/2017/12/608/ "Qt多线程-QtConcurrent并行运算高级API-Techie亮博客")

详细对比
----

### Qt事件处理

只有QThread支持。但是其他两个可以用QApplication::postEvent发出事件

### Qt信号槽

QThread完全支持，QThreadPool的QRunnable可以通过多重继承支持 Concurrent提供的map/filter函数可以利用QFutureWatcher，使用此方式可用信号控制线程，但仍然无发通过信号槽对线程的数据做修改 run就完全与信号槽无缘了，毕竟调用的只是一个函数

### 线程优先级

这个优先级设置以后不一定有效，要看系统 QThread完全支持，可以用**[setPriority](http://doc.qt.io/qt-5/qthread.html#setPriority)**函数

### 其他

Concurrent所有函数都支持QFuture，同时Concurrent支持指定QThreadPool

使用
--

*   只进行一次运行，或者调用不频繁不需要长时间开着线程，也不需要数据交互，直接Concurrent省事，毕竟只需要一行，也不需要定义什么类。但如果不希望包含QT += Concurrent，那就用线程池吧。
*   单次不频繁，需要数据交互，三个都能用，对于Concurrent虽然没有信号槽但是可以自定义函数参数，注意线程安全即可。
*   频繁调用，一定不要重复的创建销毁线程，可以用线程池
*   长时间在幕后运行，一般这样的线程总要有数据交互的，建议直接QThread，主要是QThread还支持事件处理，能做的事情会很多。