title: " Qt-Qt creator项目构建配置，实现不同构建具有不同的宏定义\t\t"
tags:
  - pro文件
  - qt
  - qt creator
url: 235.html
id: 235
categories:
  - Qt
date: 2017-11-18 19:57:06
---
pro配置
-----

首先需要了解pro配置的方法，pro文件涉及到的关键词如下： 

CONFIG、DEFINES、DEPENDPATH、DESTDIR、FORMS、HEADERS、INCLUDEPATH、LIBS、MOC\_DIR、OBJECTS\_DIR、QT、RCC\_DIR、RESOURCES、RC\_FILE、RC\_ICONS、SOURCES、TARGET、TEMPLATE、TRANSLATIONS、UI\_DIR…… 

具体配置说明请见本博客其他文章

Qt Creator项目构建方式配置
------------------

打开项目，在QT Creator左侧操作栏选择“项目”，进入项目构建设置页面 

在构建步骤下有qmake及Make配置 

在qmake的额外参数输入： `"DEFINES+=XXX"` 

注意必须增加双引号，否则无效，可以在当前构建方式下进行宏定义的添加 

此处内容的格式与pro配置的格式完全相同