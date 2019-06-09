title: " Qt语言家(Qt Linguist)更新翻译报错-Qt5.9-MinGW\t\t"
tags:
  - qt
  - qt linguist
  - qt语言家
url: 300.html
id: 300
categories:
  - Qt
date: 2017-11-22 17:00:49
---
使用Qt语言家 

工具-外部-Qt语言家-更新翻译(lupdate)选择后无效，并在控制台-概要信息处提示：

WARNING: Project ERROR: Cannot run compiler 'g++'. Maybe you forgot to setup the environment? 

解决方法：添加g++路径到环境变量 

两种路径注意区分： 

C:\\Qt\\Qt5.9.2\\5.9.2\\mingw53\_32\\bin\ qt运行库路径 

C:\\Qt\\Qt5.9.2\\Tools\\mingw530\_32\\bin g++路径

> 注意更新环境变量后需要重启QT