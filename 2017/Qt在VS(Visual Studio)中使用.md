title: " Qt在VS(Visual Studio)中使用\t\t"
tags:
  - qt
  - vs
url: 490.html
id: 490
categories:
  - Qt
date: 2017-12-01 14:19:40
---
qt-vs-addin此工具由QT提供，实现QT在VS下编译运行。

安装
--

使用qt-vs-addin-1.2.5.exe安装即可，安装过程中无配置，直接下一步即可。 

此版本经测试支持vs2013及QT5.6。

配置
--

安装完成后打开VS，可以看到 ![](http://wx4.sinaimg.cn/mw690/a8dbb8d6ly1fm19h02zp8j20c801ndfr.jpg) 

若要在VS使用QT，打开项目的时候不应用文件下的项目打开，应该使用QT5下的打开项目，程序的运行调试和正常VS项目相同还是使用调试菜单下的功能。 

首次安装完成请先打开QT5菜单下的QT Options: ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm19h0i0zvj209106uq36.jpg) 首次安装完QT的版本是没有设置的，首先选择add，添加QT版本，路径一般是C:\\QT\\QTXXXX\\XXXX\\msvcXXXX，经过此配置后可以实现QT在VS下编译。 

下面需要设置系统环境变量： ![](http://wx3.sinaimg.cn/mw690/a8dbb8d6ly1fm19h0wcvwj208408kt9n.jpg) 在用户变量里增加Path 其值为：C:\\Qt\\Qt5.6.0\\5.6\\msvc2013\\bin;C:\\Qt\\Qt5.6.0\\Tools\\QtCreator\\bin; 

从而实现QT的可执行程序在任意路径均可正常运行，否则会提示缺少QT运行库

VS2015+版本配置
-----------

现在已经支持了qt-vs-addin，至于2017是否支持不清楚。可以使用另外的插件Qt Visual Studio Tools (2015) 

在VS中通过“工具 -> 扩展和更新”（“Tools->Extensions and Updates”），打开后搜索“Qt”，可以搜到Qt Visual Studio Tools (2015)插件 

安装以后可以在菜单栏看到“Qt VS Tools”与“qt-vs-addin”安装结果一样