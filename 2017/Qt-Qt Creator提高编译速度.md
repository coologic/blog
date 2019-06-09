title: " Qt-Qt Creator提高编译速度\t\t"
tags:
  - qt
  - qt creator
  - 编译
  - 多线程
  - 预编译
url: 237.html
id: 237
categories:
  - Qt
date: 2017-11-18 20:09:12
---
提高Qt Creator的编译速度，有如下几个途径

多线程编译
-----

选择左侧“项目”/“Projects”进入项目构建页面，在构建步骤下的Make 

修改Make参数 增加`-j x`

注意j与x之间有空格，x为线程数，建议x不要大于cpu物理核心数量

预编译头文件
------

使用VS的都应该熟悉stdafx.h文件，此文件为vs默认增加的预编译头文件 

qt可以在pro文件中增加

`PRECOMPILED_HEADER = xxxx.h`

通过此命令，指定xxxx.h文件为预编译文件。 

在此头文件中可以包含不常变动的头文件、标准库以提高编译速度 

同时可在此文件定义类似于#pragma execution\_character\_set("UTF-8")等指令，保证指令在所有文件开始编译前起效