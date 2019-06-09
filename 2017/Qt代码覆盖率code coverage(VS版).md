---
title: " Qt代码覆盖率code coverage(VS版)\t\t"
tags:
  - Code coverage
  - qt
  - 代码覆盖率
url: 493.html
id: 493
categories:
  - Qt
date: 2017-12-01 14:46:25
---

下述说明仅适用于VS编译，若Mingw可直接使用gcov。 QT代码覆盖率测试需要使用VS的开发平台，首先利用QT\_addin\_vs实现QT在VS下运行。然后使用VS下的[OpenCppCoverage工具](https://opencppcoverage.codeplex.com/)进行代码测试。由于OpenCppCoverage自身输出的报表不好看，所以使用[Jenkins](https://jenkins.io/)工具实现对报表的优化。 实现QT在VS下运行方法见此文：[Qt在VS(Visual Studio)中使用](http://techieliang.com/2017/12/490/)

OpenCppCoverage安装
-----------------

首先安装OpenCppCoverageSetup-x86-0.9.5.2.exe 默认下一步安装 然后安装OpenCppCoverage-0.9.1.1.vsix，这是一个VS的插件。 两者安装完成以后打开VS的工具菜单可以看到： ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm19gkceirj20bb022747.jpg)

代码覆盖率测试
-------

### ?利用VS插件实现代码覆盖率测试

在程序可运行的情况下，直接点击工具菜单下的RunOpenCppCoverage，会运行程序，然后会生成html文件，其内包含代码覆盖率报表。测试结果如下： ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm19gktq6mj20fe0bx0vk.jpg)

### 利用cmd命令实现代码覆盖率测试

由于命令代码过长，使用此方法建议用记事本重命名成.cmd文件写代码。 OpenCppCoverage --sources D:\\QT\\qt\_test\_vs --export\_type=binary -- D:\\QT\\qt\_test\_vs\\debug\\tst\_qt\_test\_vstest.exe OpenCppCoverage --sources D:\\QT\\qt\_test\_vs --export\_type=html -- D:\\QT\\qt\_test\_vs\\debug\\tst\_qt\_test\_vstest.exe OpenCppCoverage --sources D:\\QT\\qt\_test\_vs --export\_type=cobertura -- D:\\QT\\qt\_test\_vs\\debug\\tst\_qt\_test\_vstest.exe Pause 上述代码为测试代码，其中： --sources D:\\QT\\qt\_test\_vs指向项目所在路径，这个路径下应该包含.cpp .h文件等 --export_type=XXX 为报表显示类型，上述三行代码分别是二进制报表、html报表、cobertura的xml报表。若不写默认为html。 最后路径指向被测程序exe文件。 若为html类型报表结果如下： ![](http://wx3.sinaimg.cn/mw690/a8dbb8d6ly1fm19gl7kq8j20cg059glq.jpg) 主页面显示程序整体覆盖率 ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm19gllwi6j20fe03i0sv.jpg) 下一级显示每个文件覆盖率 ![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fm19gm2v90j20fe04074j.jpg) 最后点开每个文件显示每行代码是否覆盖，绿色为覆盖，红色为未覆盖。 ![](http://wx2.sinaimg.cn/mw690/a8dbb8d6ly1fm19gmgha8j20ac0cvwei.jpg)