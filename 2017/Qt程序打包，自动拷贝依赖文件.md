---
title: " Qt程序打包，自动拷贝依赖文件\t\t"
url: 624.html
id: 624
categories:
  - Qt
date: 2017-12-11 15:31:48
tags:
---

Qt提供了windeployqt可快速拷贝依赖文件 cmd中输入下面指令即可，pro.exe为自己开发的文件 C:\\Qt\\Qt5.9.2\\5.9.2\\mingw53_32\\bin\\windeployqt.exe pro.exe 根据实际qt版本修改路径，会自动识别debug/release模式的文件拷贝相应库