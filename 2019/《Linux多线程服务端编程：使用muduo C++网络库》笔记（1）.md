---
title: " 《Linux多线程服务端编程：使用muduo C++网络库》笔记（1）\t\t"
tags:
  - 笔记
categories:
  - C/C++
  - 后端
date: 2019-02-23 23:04:32
---
以往完全没学过服务端、os等相关知识，甚至于C++语法还有好多没涉及到过，所以在阅读本书过程中遇到了好多全新的知识，一遍阅读理解有限，先记录一下。 相关知识了解过少，下面分类可能不对，主要是记录新遇到的知识点、学过但遗忘的知识点。

C++/C++11
---------

### sizeof

首先这不是函数，也不是return一类的操作符关键字，是一个特殊的宏，会在编译期求解

> 注意是编译期求解所以内部表达式编译后会成为最后值，运行时不会执行内部代码 

`int a=0; sizeof(a=1); cout<<a;//此处是0`

两种用法，`sizeof(typename/object) sizeof object` 即类型名必须用括号，对象名可不用括号

### std::function/std::bind

std::function仿函数，将函数指针封装成了模板类。 

std::function<int(int ,int)> （）外为返回值，内为多个参数类型 

bind可实现对函数的绑定，支持不同参数类型：[C++11 中function和bind以及lambda 表达式的用法](https://www.cnblogs.com/leijiangtao/p/4200969.html)

```
#include <iostream>
using namespace std;
class A {
public:
    void fun_3(int k,int m)     {
        cout<<k<<" "<<m<<endl;
    }
};
 
void fun(int x,int y,int z) {
    cout<<x<<"  "<<y<<"  "<<z<<endl;
}
 
void fun_2(int &a,int &b) {
    a++;
    b++;
    cout<<a<<"  "<<b<<endl;
}
 
int main(int argc, const char * argv\[\]) {
    auto f1 = std::bind(fun,1,2,3); //表示绑定函数 fun 的第一，二，三个参数值为： 1 2 3
    f1(); //print:1  2  3
 
    auto f2 = std::bind(fun, placeholders::\_1,placeholders::\_2,3);
    //表示绑定函数 fun 的第三个参数为 3，而fun 的第一，二个参数分别有调用 f2 的第一，二个参数指定
    f2(1,2);//print:1  2  3
 
    auto f3 = std::bind(fun,placeholders::\_2,placeholders::\_1,3);
    //表示绑定函数 fun 的第三个参数为 3，而fun 的第一，二个参数分别有调用 f3 的第二，一个参数指定
    //注意： f2  和  f3 的区别。
    f3(1,2);//print:2  1  3
 
    int n = 2;
    int m = 3;
    auto f4 = std::bind(fun\_2, n,placeholders::\_1);
    f4(m); //print:3  4
    cout<<m<<endl;//print:4  说明：bind对于不事先绑定的参数，通过std::placeholders传递的参数是通过引用传递的
    cout<<n<<endl;//print:2  说明：bind对于预先绑定的函数参数是通过值传递的

    A a;
    auto f5 = std::bind(&A::fun\_3, a,placeholders::\_1,placeholders::_2);
    f5(10,20);//print:10 20
    std::function<void(int,int)> fc = std::bind(&A::fun\_3, a,std::placeholders::\_1,std::placeholders::_2);    在实际使用中都用 <strong>auto<> 关键字来代替std::function… 这一长串了。
    fc(10,20);//print:10 20
    return 0;
}
```
### emplace\_back/emplace\_front/emplace

emplace\_back能就地通过参数构造对象，不需要拷贝或者移动内存，相比push\_back能更好地避免内存的拷贝与移动，使容器插入元素的性能得到进一步提升。在大多数情况下应该优先使用emplace\_back来代替push\_back。

### assert/assert_static

三者差异：运行时、静态 

assert_static在编译期间进行判定 

所有断言仅用作判断，不要在内部添加可改变全局/局部状态的操作或函数 

release编译不会屏蔽assert，除非主动声明NDEBUG宏

### std::move/std::forward

下面内容摘自：[（原创）C++11改进我们的程序之move和完美转发](https://www.cnblogs.com/qicosmos/p/3376241.html)

IO
--

### 阻塞/非阻塞/同步/异步

同步和异步是针对应用程序和内核的交互而言的，对同一个io的多个操作是否有序的，有序同步否则异步

*   同步指的是用户进程触发IO操作并等待或者轮询的去查看IO操作是否就绪
*   异步是指用户进程触发IO操作以后便开始做自己的事情，而当IO操作已经完成的时候会得到IO完成的通知

阻塞和非阻塞从进程/线程角度出发，访问数据时，数据是否就绪与进程/线程是否等待的关系

*   阻塞方式下读取或者写入函数将一直等待，
*   非阻塞方式下，读取或者写入函数会立即返回一个状态值

一般来说I/O模型可以分为：同步阻塞，同步非阻塞，IO多路复用，信号驱动，异步IO（**只有同步才有阻塞、非阻塞之分**） 

IO多路复用、信号驱动也是同步的，在IO操作上还是有序的，但表现出一定异步的性质。

同步阻塞，同步非阻塞，IO多路复用，信号驱动，实质都有一定阻塞：内核拷贝数据到进程数据空间。 

而异步都是非阻塞的（AIO、IOCP），只有用户线程在操作IO的时候根本不去考虑IO的执行全部都交给CPU去完成，而自己只等待一个完成信号的时候，才是真正的异步IO

### select/poll/epoll

https://www.cnblogs.com/zhaodahai/p/6831456.html 

select本质上是通过设置或者检查存放fd标志位的数据结构来进行下一步处理。

1.  单个进程可监视的fd数量被限制，即能监听端口的大小有限。
2.  对socket进行扫描时是线性扫描，即采用轮询的方法，效率较低
3.  需要维护一个用来存放大量fd的数据结构，这样会使得用户空间和内核空间在传递该结构时复制开销大

poll本质上和select没有区别，它将用户传入的数组拷贝到内核空间，然后查询每个fd对应的设备状态，如果设备就绪则在设备等待队列中加入一项并继续遍历，如果遍历完所有fd后没有发现就绪设备，则挂起当前进程，直到设备就绪或者主动超时，被唤醒后它又要再次遍历fd。这个过程经历了多次无谓的遍历。

1.  没有最大连接数的限制，原因是它是基于链表来存储的
2.  大量的fd的数组被整体复制于用户态和内核地址空间之间，而不管这样的复制是不是有意义。
3.  poll还有一个特点是“水平触发”，如果报告了fd后，没有被处理，那么下次poll时会再次报告该fd。

epoll有EPOLLLT和EPOLLET两种触发模式，LT是默认的模式，条件触发，ET是“高速”模式，边缘触发。

1.  在ET模式下，read一个fd的时候一定要把它的buffer读光，也就是说一直读到read的返回值小于请求值，或者 遇到EAGAIN错误。
2.  epoll使用“事件”的就绪通知方式，通过epoll_ctl注册fd，一旦该fd就绪，内核就会采用类似callback的回调机制来激活该fd
3.  系统中一旦有大量你不需要读写的就绪文件描述符，ET模式比条件触发效率高，系统不会充斥大量你不关心的就绪文件描述符
4.  没有最大并发连接的限制，能打开的FD的上限远大于1024（1G的内存上能监听约10万个端口）
5.  效率提升，不是轮询的方式，不会随着FD数目的增加效率下降。只有活跃可用的FD才会调用callback函数
6.  内存拷贝，利用mmap()文件映射内存加速与内核空间的消息传递，epoll使用mmap减少复制开销
7.  在连接数少并且连接都十分活跃的情况下，select和poll的性能可能比epoll好

### 边缘触发(Edge Trigger)/条件触发(Level Trigger)

条件触发(Level Trigger)下，只要这个fd还有数据可读，每次 epoll_wait都会返回它的事件，提醒用户程序去操作 

在边缘触发模式中，它只会提示一次，直到下次再有数据流入之前都不会再提示了，无 论fd中是否还有数据可读。

### Reactor模式/Proactor模式

事件处理模型，[IO设计模式：Reactor和Proactor对比](https://www.cnblogs.com/me115/p/4452801.html) 

Reactor（反应器）模式用于同步I/O，而Proactor运用于异步I/O操作

分布式系统
-----

*   分布式系统险恶之处：时间与事件顺序违反直觉（狭义相对论效应，每个主机均有自己的时钟）
*   分布式系统软件开发不必纠结于想尽一切办法防止崩溃，应把能随时重启程序作为设计目标
*   心跳协议确定发送周期、检查周期、timeout。一般前两者一致均为Tc，timeout=2Tc。Tc根据故障延迟敏感性取1s或10s
*   UTC时间闰秒会在12.31或6.30日插入，需要考虑影响
*   心跳要在工作线程发出，不要另起心跳线程，与业务用同一个连接，不要单独的心跳连接。为了防止伪心跳
*   以ip:port:start_time:pid作为分布式系统一个进程每次启动/重启的唯一标识符gpid，time为64位整数UTC时间。还可以再加上程序名称和版本号
*   分布式系统中每一个长期运行的会与其他机器打交道的进程都应该提供一个管理接口，对外提供一个维修探查同道（tcp server），最合适的是使用HTTP提供接口
*   可扩展的消息格式：不要加协议版本号，应使用扩展字段，确保每个字段终身不变。要用中间语言描述消息格式，JSON、XML、Google Protocol Buffers
*   分布式系统中单元测试局限性：

1.  妨碍大型重构，要同时重写源码和测试代码，切换语言测试代码作废
2.  为方便测试实施依赖注入，破坏代码整体性：可能会为了可测试进行解耦
3.  稍微复杂一点的测试要用mock,C++中会增加为了测试用的接口及mock类
4.  某些failure场景难以测试
5.  对多线程程序无能为力

*   通过tcp做进程间通讯可通过test harness（mock）所有与被测程序交互的程序来进行测试，此时不会改变被测程序代码，不影响重构，也可进行压力测试，但局限性

1.  有连接到复杂的程序，如数据库，需要内嵌数据库或建立测试数据库
2.  如果有其他io，如硬盘操作等，可通过tmpfs测试但与实际有差异

*   DNS用于静态、缓变的解析，用name service替代DNS，解决服务器变动情况，一般以5台服务器提供服务，可以用Paxos算法，有开源实现（ZooKeeper/KeySpace/Doozer）