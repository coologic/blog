title: " Boost安装\t\t"
tags:
  - boost
  - c/c++
url: 98.html
id: 98
categories:
  - C/C++
date: 2017-11-10 19:35:21
---
windows下boost安装过程 

boost版本1.65.0 

win版本10 

vs版本vs2013+vs2017 

其他环境：windows kits 

安装boost解压到D盘 

直接运行bootstrap.bat可直接生成bjam文件

> 若无法生成查看log，如果内容是缺少.h文件可用cmd输入，此过程仅适用于系统具有多个vs版本的环境 
> 
> XXX/bootstrap.bat vsXX 版本为vs版本号，若为其他版本可以输入-vs00此时会失败，但是log文件会显示所有支持的指定版本，然后重新输入可生成bjam.exe

运行bjam.exe完成编译

> 若系统具有多个vs版本需要指定版本，否则默认最高版本 
> 
> cmd中输入bjam.exe --toolset=msvc-12.0 
> 
> 12为vs2013版本，注意双-和空格