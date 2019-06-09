---
title: " 坑记-float、memcmp\t\t"
tags:
  - float
  - memcmp
  - memset
url: 696.html
id: 696
categories:
  - C/C++
date: 2017-12-14 16:05:55
---

float
-----

float source = 100.0f;
float result = source * 0.02f;
qDebug()<<(int)(source * 0.02f)<<source * 0.02f;
qDebug()<<(int)result<<result;

结果 1 2 2 2 很神奇的结果，source * 0.02f默认结果不是float类型还是其他什么地方有问题？还是默默用double的好 运行平台：Qt5.9.2-MinGW

### memcmp

struct tempStruct {
    int n;
    bool f;
    double d;
};
int main(int argc, char *argv\[\]) {
    tempStruct a1{10,true,10.0};
    tempStruct a2 = a1;
    tempStruct a3;
    memset(&a3,0,sizeof(a1));
    a3.n=10;a3.f=true;a3.d=10.0;
    qDebug()<<memcmp(&a1,&a2,sizeof(a1));
    qDebug()<<memcmp(&a1,&a3,sizeof(a1));
    return 0;
}

结果

0
1
16 4 1 8

编译器自动补位对齐，所以memset以后是把a3内存所有值都改为0，然后将对应类型所在内存改为对应值，补位的区域还是0，而a1的补位区域是随机值。 若不能保证整个程序所有与此类型相关的操作都是赋值或memset，那就不要用memcmp判断。