---
title: " Qt多线程-QtConcurrent并行运算高级API\t\t"
tags:
  - qt
  - QtConcurrent
  - 并行
  - 多线程
url: 608.html
id: 608
categories:
  - Qt
date: 2017-12-10 21:58:36
---

介绍
--

Qt除了提供基本的QThread实现多线程，并提供QThreadPool实现线程池以外，还提供了QtConcurrent模块用于并行计算。

> 使用此类需要在pro文件增加QT += concurrent

[QtConcurrent命名空间帮助文档](http://doc.qt.io/qt-5/qtconcurrent.html)，[API介绍帮助](http://doc.qt.io/qt-5/qtconcurrent-index.html)

### API

void blockingFilter(Sequence &sequence, FilterFunction filterFunction)
Sequence blockingFiltered(const Sequence &sequence, FilterFunction filterFunction)
Sequence blockingFiltered(ConstIterator begin, ConstIterator end, FilterFunction filterFunction)
T blockingFilteredReduced(const Sequence &sequence, FilterFunction filterFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
T blockingFilteredReduced(ConstIterator begin, ConstIterator end, FilterFunction filterFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
void blockingMap(Sequence &sequence, MapFunction function)
void blockingMap(Iterator begin, Iterator end, MapFunction function)
T blockingMapped(const Sequence &sequence, MapFunction function)
T blockingMapped(ConstIterator begin, ConstIterator end, MapFunction function)
T blockingMappedReduced(const Sequence &sequence, MapFunction mapFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
T blockingMappedReduced(ConstIterator begin, ConstIterator end, MapFunction mapFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
QFuture<void> filter(Sequence &sequence, FilterFunction filterFunction)
QFuture<T> filtered(const Sequence &sequence, FilterFunction filterFunction)
QFuture<T> filtered(ConstIterator begin, ConstIterator end, FilterFunction filterFunction)
QFuture<T> filteredReduced(const Sequence &sequence, FilterFunction filterFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
QFuture<T> filteredReduced(ConstIterator begin, ConstIterator end, FilterFunction filterFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
QFuture<void> map(Sequence &sequence, MapFunction function)
QFuture<void> map(Iterator begin, Iterator end, MapFunction function)
QFuture<T> mapped(const Sequence &sequence, MapFunction function)
QFuture<T> mapped(ConstIterator begin, ConstIterator end, MapFunction function)
QFuture<T> mappedReduced(const Sequence &sequence, MapFunction mapFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
QFuture<T> mappedReduced(ConstIterator begin, ConstIterator end, MapFunction mapFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
QFuture<T> run(Function function, ...)
QFuture<T> run(QThreadPool *pool, Function function, ...)

很多都是重载的，主要函数如下：

*   [Concurrent Map and Map-Reduce](http://doc.qt.io/qt-5/qtconcurrentmap.html)
    *   [QtConcurrent::map()](http://doc.qt.io/qt-5/qtconcurrent.html#map) applies a function to every item in a container, modifying the items in-place.
    *   --这个函数会将作为参数传入的函数应用到容器中的每一项，对这些项进行直接修改，会修改传入变量内容。
    *   [QtConcurrent::mapped()](http://doc.qt.io/qt-5/qtconcurrent.html#mapped) is like map(), except that it returns a new container with the modifications.
    *   --功能类似于map()，只不过它不是直接修改原始容器，而是将修改后的元素放到一个新的容器中并作为返回值返回。
    *   [QtConcurrent::mappedReduced()](http://doc.qt.io/qt-5/qtconcurrent.html#mappedReduced) is like mapped(), except that the modified results are reduced or folded into a single result.
    *   --功能类似于mapped()，首先执行mapped的操作，然后传入一个Reduce函数进行简化，最终返回唯一一个元素，此操作不会修改原始容器。

*   [Concurrent Filter and Filter-Reduce](http://doc.qt.io/qt-5/qtconcurrentfilter.html)
    *   [QtConcurrent::filter()](http://doc.qt.io/qt-5/qtconcurrent.html#filter) removes all items from a container based on the result of a filter function.
    *   --从容器中删除那些满足某个过来条件的项。
    *   [QtConcurrent::filtered()](http://doc.qt.io/qt-5/qtconcurrent.html#filtered) is like filter(), except that it returns a new container with the filtered results.
    *   --功能类似于filter()，只不过它会返回一个包含剩余元素的容器。
    *   [QtConcurrent::filteredReduced()](http://doc.qt.io/qt-5/qtconcurrent.html#filteredReduced) is like filtered(), except that the filtered results are reduced or folded into a single result.
    *   --功能类似于filtered()，后续进行reduce操作。
*   [Concurrent Run](http://doc.qt.io/qt-5/qtconcurrentrun.html)
    *   [QtConcurrent::run()](http://doc.qt.io/qt-5/qtconcurrent.html#run) runs a function in another thread.
    *   --用另一个进程运行一个函数
*   [QFuture](http://doc.qt.io/qt-5/qfuture.html) represents the result of an asynchronous computation.
*   \-\- 获取一步计算结果
*   [QFutureIterator](http://doc.qt.io/qt-5/qfutureiterator.html) allows iterating through results available via [QFuture](http://doc.qt.io/qt-5/qfuture.html).
*   --通过使用QFuture允许遍历结果
*   [QFutureWatcher](http://doc.qt.io/qt-5/qfuturewatcher.html) allows monitoring a [QFuture](http://doc.qt.io/qt-5/qfuture.html) using signals-and-slots.
*   \-\- 利用信号槽监视QFuture
*   [QFutureSynchronizer](http://doc.qt.io/qt-5/qfuturesynchronizer.html) is a convenience class that automatically synchronizes several QFutures.
*   --自动同步多个futures

QtConcurrent::map
-----------------

map的范例：[http://doc.qt.io/qt-5/qtconcurrent-map-example.html](http://doc.qt.io/qt-5/qtconcurrent-map-example.html) map详细介绍：[http://doc.qt.io/qt-5/qtconcurrentmap.html](http://doc.qt.io/qt-5/qtconcurrentmap.html)

#include <QCoreApplication>
#include <QtConcurrent>
#include <QVector>
#include <QDebug>
#include <QFuture>
void MapFunction(int& num) {
    num += 1;
}
int mappedReducedFunction(const int& num) {
    return num + 1;
}
void ReduceFunction(int& result, const int& item) {
    int t_r = result;
    result = item > result ? item : result;
    qDebug()<<t_r<<result<<item;
}
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QVector<int> testVactor;
    for(int i = 1; i <= 3; i++) {
        testVactor.push_back(i);
    }
    for(int i = 1; i <= 3; i++) {
        testVactor.push_back(10-i);
    }
    qDebug() << "start m:" << testVactor;
    QFuture<void> f = QtConcurrent::map(testVactor, MapFunction);
    f.waitForFinished();
    qDebug() << "map result:" << testVactor;//map直接修改源数据
    QFuture<int> r = QtConcurrent::mappedReduced(testVactor, mappedReducedFunction, ReduceFunction);
    qDebug() << "mappedReduced result:" << r.result();
    return 0;
}

注意几个函数的声明形式，不可有差距。结果

start m: QVector(1, 2, 3, 9, 8, 7)
map result: QVector(2, 3, 4, 10, 9, 8)
0 3 3
3 4 4
4 5 5
5 11 11
11 11 10
11 11 9
mappedReduced result: 11

结果示意很明显，reduced最终表留的是等于函数result参数值的项

QtConcurrent::filter
--------------------

filter详细介绍：[http://doc.qt.io/qt-5/qtconcurrentfilter.html](http://doc.qt.io/qt-5/qtconcurrentfilter.html)

#include <QCoreApplication>
#include <QtConcurrent>
#include <QList>
#include <QDebug>
#include <QFuture>
bool filterFunction(const int& num) {
    return (num > 5);
}
void ReduceFunction(int& result, const int& item) {
    int t_r = result;
    result = item > result ? item : result;
    qDebug()<<t_r<<result<<item;
}
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QList<int> testVactor;
    for(int i = 1; i <= 3; i++) {
        testVactor.push_back(i);
    }
    for(int i = 1; i <= 3; i++) {
        testVactor.push_back(10-i);
    }
    qDebug() << "start m:" << testVactor;
    QFuture<void> f = QtConcurrent::filter(testVactor, filterFunction);
    f.waitForFinished();
    qDebug() << "map result:" << testVactor;//map直接修改源数据
    QFuture<int> r = QtConcurrent::filteredReduced(testVactor, filterFunction, ReduceFunction);
    qDebug() << "mappedReduced result:" << r.result();
    return 0;
}

注意几个函数的声明形式，不可有差距。filter函数要返回bool类型，用于判断是否过滤此元素 结果：

start m: (1, 2, 3, 9, 8, 7)
map result: (9, 8, 7)
0 9 9
9 9 8
9 9 7
mappedReduced result: 9

QtConcurrent::run
-----------------

感觉run用起来很舒服，因为他没有对运行函数头做限制，可以是任意数量的任意类型参数。 run的详细帮助：[http://doc.qt.io/qt-5/qtconcurrentrun.html](http://doc.qt.io/qt-5/qtconcurrentrun.html)，也可以看看本机的qtconcurrentrun.h文件，可以看到里面有很多的run的重载函数 下面给出最基本的使用

#include <QCoreApplication>
#include <QtConcurrent>
#include <QList>
#include <QDebug>
#include <QThread>
void function(const QList<int>& param1, const int& param2, Qt::HANDLE main_id) {
    qDebug()<<"function param:"<<param1<<param2<<main_id;
    qDebug()<<"function thread id:" <<QThread::currentThreadId();
}
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QList<int> testVactor;
    for(int i = 1; i <= 3; i++) {
        testVactor.push_back(i);
    }
    qDebug() << "main thread id:" << QThread::currentThreadId();
    QFuture<void> f = QtConcurrent::run(function,testVactor,666,QThread::currentThreadId());
    f.waitForFinished();//要等待，否则线程没运行完程序结束会出错
    return 0;
}

结果

main thread id: 0x2a10
function param: (1, 2, 3) 666 0x2a10
function thread id: 0x2344

### 其他使用方式-指定线程池

有时候希望运行的函数在全局线程池或者局部线程池运行，而不是有qt托管处理，可以进行如下方式调用：

extern void aFunction();
QThreadPool pool;
QFuture<void> future = QtConcurrent::run(&pool, aFunction);

阻塞QtConcurrent
--------------

上述所有函数都是非阻塞的，所以在return 0前都有waitForFinished，qt同样提供了阻塞函数 见最开始API帮助介绍连接，可以看到相关接口

void blockingFilter(Sequence &sequence, FilterFunction filterFunction)
Sequence blockingFiltered(const Sequence &sequence, FilterFunction filterFunction)
Sequence blockingFiltered(ConstIterator begin, ConstIterator end, FilterFunction filterFunction)
T blockingFilteredReduced(const Sequence &sequence, FilterFunction filterFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
T blockingFilteredReduced(ConstIterator begin, ConstIterator end, FilterFunction filterFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
void blockingMap(Sequence &sequence, MapFunction function)
void blockingMap(Iterator begin, Iterator end, MapFunction function)
T blockingMapped(const Sequence &sequence, MapFunction function)
T blockingMapped(ConstIterator begin, ConstIterator end, MapFunction function)
T blockingMappedReduced(const Sequence &sequence, MapFunction mapFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)
T blockingMappedReduced(ConstIterator begin, ConstIterator end, MapFunction mapFunction, ReduceFunction reduceFunction, QtConcurrent::ReduceOptions reduceOptions = UnorderedReduce | SequentialReduce)

可以看到对应函数的介绍都有：

> Note: This function will block until all items in the sequence have been processed.

使用方式近似，不提供示例了。