---
title: " C++设计模式-单例模式\t\t"
tags:
  - c++
  - 设计模式
url: 772.html
id: 772
categories:
  - C/C++
date: 2017-12-22 20:11:32
---

介绍
--

几个重点：

*   构造函数为private
*   提供一个获取单例对象指针的函数
*   一个静态指针成员存储单例对象

注意：

*   获取单例对象也可以获取对象引用，但要注意拷贝构造函数和赋值运算符
*   如果有多线程访问单例，需要注意线程同步

范例
--

源码GitHub：**[CppDesignPattern](https://github.com/TechieL/CppDesignPattern)**

### 单线程

#ifndef SIGLETON_H
#define SIGLETON_H
/\*\*
 \* @brief 非线程安全单例，无多线程时使用
 */
class Singleton {
public:
    /\*\*
     \* @brief 单例模式，获取实例化对象
     \* @param 无
     \* @return 单例对象
     */
    static Singleton *GetInstance();
    /\*\*
     \* @brief 单例模式，主动销毁实例化对象
     \* @param 无
     \* @return 无
     */
    static void DestoryInstance();
private:
    /\*\*
     \* @brief 构造函数
     */
    Singleton();
    /\*\*
     \* @brief 单例模式在程序结束时自动删除单例的方法
     */
    class SingletonDel {
    public:
        ~SingletonDel() {
            if (m_instance != nullptr) {
                delete m_instance;
                m_instance = nullptr;
            }
        }
    };
    static SingletonDel m\_singleton\_del;///程序结束时销毁
    static Singleton *m_instance;       //单例对象指针
};
#endif // SIGLETON_H
//cpp
#include "sigleton.h"
Singleton* Singleton::m_instance = nullptr;
Singleton *Singleton::GetInstance() {
    if (m_instance == nullptr) {
        m_instance = new Singleton();
    }
    return m_instance;
}

void Singleton::DestoryInstance() {
    if (m_instance != nullptr) {
        delete m_instance;
        m_instance = nullptr;
    }
}

Singleton::Singleton() {
}

### 多线程

#ifndef THREAD\_SAFE\_SINGLETON_H
#define THREAD\_SAFE\_SINGLETON_H
/\*\*
 \* @brief 线程安全单例，多线程时使用
 */
class ThreadSafeSingleton {
public:
    /\*\*
     \* @brief 单例模式，获取实例化对象
     \* @param 无
     \* @return 单例对象
     */
    static ThreadSafeSingleton* GetInstance();
    /\*\*
     \* @brief 单例模式，主动销毁实例化对象
     \* @param 无
     \* @return 无
     */
    static void DestoryInstance();
private:
    /\*\*
     \* @brief 构造函数
     */
    ThreadSafeSingleton();
    /\*\*
     \* @brief 单例模式在程序结束时自动删除单例的方法
     */
    class SingletonDel {
    public:
        ~SingletonDel() {
            if (m_instance != nullptr) {
                delete m_instance;
                m_instance = nullptr;
            }
        }
    };
    static ThreadSafeSingleton m\_singleton\_del;///程序结束时销毁
    static ThreadSafeSingleton *m_instance;       //单例对象指针
};
#endif // THREAD\_SAFE\_SINGLETON_H

#include "thread\_safe\_singleton.h"
#include <QMutex>
#include <QMutexLocker>
namespace thread\_safe\_singleton_private {
    static QMutex mutex;
}
ThreadSafeSingleton* ThreadSafeSingleton::m_instance = nullptr;
ThreadSafeSingleton *ThreadSafeSingleton::GetInstance() {
    if (m_instance == nullptr) {
        QMutexLocker lock(&thread\_safe\_singleton_private::mutex);
        if (m_instance == nullptr) {
            m_instance = new ThreadSafeSingleton();
        }
    }
    return m_instance;
}

void ThreadSafeSingleton::DestoryInstance() {
    if (m_instance != nullptr) {
        delete m_instance;
        m_instance = nullptr;
    }
}

ThreadSafeSingleton::ThreadSafeSingleton() {
}

> 上面的范例提供了主动销毁单例的方法同时提供了自动销毁的方法。 若主动销毁需注意其他存储了单例指针的对象

C++11单例模式实现
-----------

C++11保证静态成员构造的时候线程安全，所以可以避免在构造时通过线程锁的方式实现线程安全

#ifndef CPP11\_SIGLETON\_H
#define CPP11\_SIGLETON\_H
#include <QDebug>
//C++11的单例实现
//c++11自动处理静态成员初始化竞争，是线程安全的
template <typename T>
class Cpp11Sigleton {
public:
    static T *GetInstance() {
        static T instance;
        return &instance;
    }
};
class TestCpp11Sigleton {
    friend class Cpp11Sigleton<TestCpp11Sigleton>;
private:
    TestCpp11Sigleton(){qDebug()<<"created"<<this;}
};
#endif // CPP11\_SIGLETON\_H

通过一个通用的单例模板类，并在需要单例类中将构造函数私有化，设置与模板类的friend关系即可实现多线程单例创建的安全。 注意返回的仍然是函数指针，若返回引用请注意拷贝和复制方法的处理

*   Class(const Class&); 拷贝
*   Class&operator=(const Class&); 复制

测试：

#include <cpp11_sigleton.h>
#include <thread>
int main(int argc, char *argv\[\]) {
    std::thread t1(Cpp11Sigleton<TestCpp11Sigleton>::GetInstance);
    t1.detach();
    std::thread t2(Cpp11Sigleton<TestCpp11Sigleton>::GetInstance);
    t2.detach();
    std::thread t3(Cpp11Sigleton<TestCpp11Sigleton>::GetInstance);
    t3.detach();
    std::thread t4(Cpp11Sigleton<TestCpp11Sigleton>::GetInstance);
    t4.detach();
    Cpp11Sigleton<TestCpp11Sigleton>::GetInstance();
    Cpp11Sigleton<TestCpp11Sigleton>::GetInstance();
    Cpp11Sigleton<TestCpp11Sigleton>::GetInstance();
    Cpp11Sigleton<TestCpp11Sigleton>::GetInstance();
}

只会输出一次构造函数的qDebug()<<"created"<<this;

C++11单例模式实现构造时参数传入
------------------

11支持可变模板参数，所以可以用一个模板实现不同类型参数的构造，GetInstance方法改一下：

template<typename... Args>
static T* GetInstance(Args&&... args) {
    static T instance(std::forward<Args>(args)...);
    return &m_pInstance;
}

相关链接：[C++设计模式](http://techieliang.com/2017/12/764/)