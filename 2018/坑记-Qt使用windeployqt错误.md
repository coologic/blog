---
title: " 坑记-Qt使用windeployqt错误\t\t"
tags:
  - qt
  - windeployqt
url: 882.html
id: 882
categories:
  - Qt
date: 2018-01-16 22:10:25
---

介绍
--

Qt在非静态编译时，发布需要提供较多的附带库文件及一些翻译文件，可以使用windeployqt XXX.exe方式快速生成，其中windeployqt在D:\\Qt\\Qt5.10.0\\5.10.0\\mingw53_32\\bin\\windeployqt，具体根据自身版本信息更改路径即可。可以建立cmd文件，实现快速打包。

D:\\Qt\\Qt5.10.0\\5.10.0\\mingw53_32\\bin\\windeployqt test.exe
pause

提示path不存在
---------

请注意，如果使用上面的代码，建立cmd 文件以后，双击打开即可，不要使用管理员身份打开。若使用管理员身份打开，则默认的cmd起始路径不再是cmd文件的路径，而是“C:\\WINDOWS\\system32>”此时在system32中肯定找不到test.exe，若想要以管理员你打开需要将“test.exe”改为绝对路径。

提示Unable to find the platform plugin
------------------------------------

优先查看系统环境变量，是否有冲突的地方，比如Anaconda其中就包含了PyQt，windeployqt会先根据path中由Anaconda创建的路径去寻找，找到具有qt-bin的库后就会使用，此时可能找到的是PyQt路径，将会出现错误。 解决方案：先将path中的路径改一下比如前面加个“1”，然后运行windeployqt，再改回原始路径。

> 对于Anaconda冲突只需要修改形如“C:\\ProgramData\\Anaconda3\\Library\\bin”路径即可，一定要先全部确定后再运行windeployqt（没记错确定要点3次）

问题发现过程： Unable to find the platform plugin这个错误的提示是由windeployqt打包release文件时提示的，具体内容，并无更多的详细提示，后尝试打包debug文件，得到了形如XX/anaconda/qt/bin/XXX.dll无法找到的错误提示，明白了是anaconda建立的path导致的错误，修改后无误。

MinGW下找不到g++.exe
----------------

在环境变量path中添加形如“D:\\Qt\\Qt5.10.0\\Tools\\mingw530_32\\bin”的路径即可，还是先全部确定以后再运行windeployqt。（没记错确定要点3次）