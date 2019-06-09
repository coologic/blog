---
title: " Qt下Doxygen使用\t\t"
tags:
  - doxygen
  - qt
url: 525.html
id: 525
categories:
  - Qt
date: 2017-12-03 17:03:55
---

Doxygen文档功能开启
-------------

默认此功能是开启的，若未开启可手动开启 菜单栏-工具-选项-文本编辑器-Completion ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm3on177f7j20tr0gy75j.jpg) 此处可以配置是否开启doxygen代码块功能、自动添加brief描述及是否添加星号

使用方法
----

直接在函数、类、结构体、枚举、文件头等部位输入"/**"然后按回车，会自动补充后续内容 ![](http://wx3.sinaimg.cn/mw690/a8dbb8d6ly1fm3on2attcj20ej05v74e.jpg) 如果不开启添加brief，则@brief行不会显示，如果不开启添加星号，那只有收尾/**及*/处有星号

自定义
---

在doxygen代码块输入“@”符号，可以添加新的描述类型，Qt也会提供代码补全功能提示可以选内容： ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm3on36e8ej208g0dkaaa.jpg)