---
title: " QQueue与QStack使用\t\t"
tags:
  - QQueue
  - QStack
  - qt
url: 576.html
id: 576
categories:
  - Qt
date: 2017-12-07 14:52:05
---

介绍
--

QQueue是Qt的队列实现，符合先进先出FIFO，继承自QList，可以使用QList所有方法，但不建议使用，属于QQueue的方法有

T dequeue()
void enqueue(const T &t)
T &head()
const T &head() const
void swap(QQueue<T> &other)

QStack是Qt的栈实现，符合后进先出LIFO，继承自QVector，可以使用QVector所有方法，但不建议食用，属于QStack的方法有

T pop()
void push(const T &t)
void swap(QStack<T> &other)
T &top()
const T &top() const

简单范例
----

所有父类方法均可使用，可参见[QList使用](http://techieliang.com/2017/12/563/)下面例子仅对比分析

QQueue<int> m_queue;
m_queue.enqueue(1);
m_queue.enqueue(2);
m_queue.enqueue(3);
qDebug()<<m_queue.size();
qDebug()<<m_queue.dequeue();
qDebug()<<m_queue.dequeue();
qDebug()<<m_queue.dequeue();
qDebug()<<m_queue.size();
QStack<int> m_stack;
m_stack.push(1);
m_stack.push(2);
m_stack.push(3);
qDebug()<<m_stack.size();
qDebug()<<m_stack.pop();
qDebug()<<m_stack.pop();
qDebug()<<m_stack.pop();
qDebug()<<m_stack.size();

结果

3
1
2
3
0
3
3
2
1
0