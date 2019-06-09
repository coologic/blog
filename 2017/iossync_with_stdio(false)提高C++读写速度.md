title: " ios::sync_with_stdio(false)提高C++读写速度\t\t"
tags:
  - cin
  - cout
  - iostream
  - stdio
  - sync_with_stdio
url: 275.html
id: 275
categories:
  - C/C++
date: 2017-11-19 16:31:53
---
C++为了兼容C，默认使iostream与stdio关联，使cin与scanf、cout和printf保持同步，保证混用过程中文件指针不混乱。 

此方式会造成性能损失，导致使用cin/cout效率远远低于使用scanf/printf。若保证程序只是用一套指令，可取消同步：
```
std::ios::sync_with_stdio(false);
```
取消后使用cin/cout速度有明显提升，但注意不要混用iostream与stdio
```
std::cin.tie(0);
```
> C++98：In terms of _static initialization order_, cin is guaranteed to be properly constructed and initialized no later than the first time an object of type [ios_base::Init](http://www.cplusplus.com/ios_base::Init) is constructed. 
> 
> C++11：In terms of _static initialization order_, cin is guaranteed to be properly constructed and initialized no later than the first time an object of type [ios_base::Init](http://www.cplusplus.com/ios_base::Init) is constructed, with the inclusion of [<iostream>](http://www.cplusplus.com/<iostream>) counting as at least one initialization of such objects with _static duration_. 
>
> cin is _[tied](http://www.cplusplus.com/ios::tie)_ to the standard output stream [cout](http://www.cplusplus.com/cout) (see [ios::tie](http://www.cplusplus.com/ios::tie)), which indicates that [cout](http://www.cplusplus.com/cout)'s buffer is _flushed_ (see [ostream::flush](http://www.cplusplus.com/ostream::flush)) before each i/o operation performed on cin. 
>
> (来源:http://www.cplusplus.com/reference/iostream/cin/ [网址](http://网址))

当传入参数为0时，实现cin与cout独立运行，不会等待，提高cin速度 
  
全局域的通过静态常量初始化一个lambda的方式实现对此代码的优先调用
```
static const int _ = []() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    return 0;
}();
```