---
title: " Qt Creator代码风格-使用Beautifier插件利用Astyle格式化\t\t"
tags:
  - astyle
  - qt
  - 代码风格
url: 873.html
id: 873
categories:
  - Qt
date: 2018-01-16 15:28:36
---

介绍
--

Qt Creator自身可以通过Ctrl+A全选Ctrl+i自动格式化，此处的格式化应该只限于缩进格式化，但不会对大括号位置、小括号前后空格、运算符前后空格等进行格式化操作，要实现类似于VS的全面的代码风格格式化，需要利用插件。

启动Beautifier插件
--------------

官方介绍：[http://doc.qt.io/qtcreator/creator-beautifier.html](http://doc.qt.io/qtcreator/creator-beautifier.html) 首先需要启动Beautifier插件，帮助-关于插件-C++-Beautifier勾选此项即可。然后重启Creator。 ![](https://github.com/TechieL/MyBlogPictureBackup/raw/master/%E5%9B%BE%E7%89%87/%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/Qt%20Creator%E4%BB%A3%E7%A0%81%E9%A3%8E%E6%A0%BC-%E4%BD%BF%E7%94%A8Beautifier%E6%8F%92%E4%BB%B6%E5%88%A9%E7%94%A8Astyle%E6%A0%BC%E5%BC%8F%E5%8C%96/1.png)

配置Astyle
--------

启动插件后在工具-选项中会具有Beautifier的配置项，启动此功能，并选择为Artisitic Style，Qt还支持另外两个ClangFomat和Uncrustify。 ![](https://github.com/TechieL/MyBlogPictureBackup/raw/master/%E5%9B%BE%E7%89%87/%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/Qt%20Creator%E4%BB%A3%E7%A0%81%E9%A3%8E%E6%A0%BC-%E4%BD%BF%E7%94%A8Beautifier%E6%8F%92%E4%BB%B6%E5%88%A9%E7%94%A8Astyle%E6%A0%BC%E5%BC%8F%E5%8C%96/2.png) 然后再Artisitic的配置页面设置Artisitic的exe文件路径，此时需要下载Artisiti：[http://astyle.sourceforge.net/](http://astyle.sourceforge.net/) 到上述页面，选择download，然后download latest version即可下载win版本，无论编译器是vs还是mingw均可用，如果是linux下使用qt，需要单独打开下方的文件夹下载最新版的linux文件自行编译。

### 自定义编码风格

astyle提供了一系列现成的风格：

> [style=allman](http://astyle.sourceforge.net/astyle.html#_style=allman) [style=java](http://astyle.sourceforge.net/astyle.html#_style=java) [style=kr](http://astyle.sourceforge.net/astyle.html#_style=kr) [style=stroustrup](http://astyle.sourceforge.net/astyle.html#_style=stroustrup) [style=whitesmith](http://astyle.sourceforge.net/astyle.html#_style=whitesmith) [style=vtk](http://astyle.sourceforge.net/astyle.html#_style=vtk) [style=ratliff](http://astyle.sourceforge.net/astyle.html#_style=ratliff) [style=gnu](http://astyle.sourceforge.net/astyle.html#_style=gnu) [style=linux](http://astyle.sourceforge.net/astyle.html#_style=linux) [style=horstmann](http://astyle.sourceforge.net/astyle.html#_style=horstmann) [style=1tbs](http://astyle.sourceforge.net/astyle.html#_style=1tbs) [style=google](http://astyle.sourceforge.net/astyle.html#_style=google) [style=mozilla](http://astyle.sourceforge.net/astyle.html#_style=mozilla) [style=pico](http://astyle.sourceforge.net/astyle.html#_style=pico) [style=lisp](http://astyle.sourceforge.net/astyle.html#_style=lisp)

除此以外还可以进行自定义，需要在Artisitic style的use custom style中通过Add添加 ![](https://github.com/TechieL/MyBlogPictureBackup/raw/master/%E5%9B%BE%E7%89%87/%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/Qt%20Creator%E4%BB%A3%E7%A0%81%E9%A3%8E%E6%A0%BC-%E4%BD%BF%E7%94%A8Beautifier%E6%8F%92%E4%BB%B6%E5%88%A9%E7%94%A8Astyle%E6%A0%BC%E5%BC%8F%E5%8C%96/4.png) 我使用的是google风格同时增加了 -p运算符两侧增加空格 参数。

### 使用

主动使用：打开某个文档，然后通过工具-Beautifier-Artisitic style-Fomat即可对当前文档格式化 自动使用：任意文档在修改保存时均会自动格式化