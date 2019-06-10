---
title: "吴恩达机器学习作业Python实现及学习笔记（1）"
tags:
  - 机器学习
  - 笔记
categories:
  - 人工智能
date: 2019-06-10 11:43:01
---

教学视频地址https://www.coursera.org/learn/machine-learning/

黄海广博士笔记：https://github.com/fengdu78/Coursera-ML-AndrewNg-Notes

作业原始文件、本人的Python版本作业答案：https://github.com/TechieLiang/AndrewNgMeachineLearningNotes

博客原始内容：https://github.com/TechieLiang/blog/tree/master/2019/

此系列记录本人学习吴恩达老师的机器学习系列视频的学习过程以及基于Python的作业实现，第一部分先记录Python环境搭建及使用

# 环境准备

IDE可以用[PyCharm](https://www.jetbrains.com/pycharm/)或者[Jupyter Notebook](https://jupyter.org/)，这需要配置下载一些工具包（Package）,用于在程序中import来引用一些成熟的工具，如numpy等

工具包方面可以用[Anaconda](https://www.anaconda.com/)，直接包含了多种常用科学计算包，当然这个开源项目也包含了Jupyter Notebook。

Ubuntu安装方法及根目录配置可参考本人[Github](https://github.com/TechieLiang/blog/blob/master/2017/TensorFlow%E5%AE%89%E8%A3%85%E5%8F%8Ajupyter%20notebook%E9%85%8D%E7%BD%AE.md)或者[Techie亮博客](https://www.techieliang.com/2017/11/68/)；Windows下Anaconda安装直接下载exe即可，其他配置与ubuntu一致

## 启动环境

### Jupyter

Jupyter Notebook我就简称nb呀，我也不知道业内一般怎么叫，看他文件后缀名是ipynb

直接打开对应程序会弹出cmd/终端窗口，启动后台服务器后然后开启浏览器访问对应的web服务（nb是基于web的，可以分段撰写代码，看到每一段的结果，有助于逐步查看结果，相对于pycharm再结果展示上更直观不需要学习调试方法，有一种Matlab/Octave的感觉）

> 建议根据前面提到的博客内容配置一下根目录地址，nb自带文件管理功能在web上，这样配置以后启动自动就是指定的根目录，便于管理每个.ipynb文件

启动以后默认是文件管理主页面，然后直接在网页右上角New-Python 3即可建立一个py文件，应该说是ipynb文件，这样就进入了代码专业的网页，左上角file可以中可以选择download as以指定格式下载下来对应的程序（当然也可以自己在根目录下找到文件操作。。。）

后续指令操作可以在每一个cell中撰写多个代码段，并分别执行每个代码段，cell可以写任何代码包括class，def的function等，如果有输出内容执行cell后会在其下直接显示输出结果。

File Edit View等是菜单栏，下方有常用功能按钮，最重要的是功能按钮最右侧有个小键盘，记录了所有快捷键，建议熟记常用的比如执行当前cell、执行所有cell、复制当前cell等等。

我最重要的两个快捷键：Esc按下后进入命令模式可以进行各种快捷键操作（查看所有快捷键的时候，显示command mode的快捷键只有在此模式下才可用），选中一个cell后按Enter进入当前cell的编辑（没有写command mode的快捷键此时可以使用）。

### Pycharm

这个IDE是真正的编程IDE了，对于一个程序首先要建立一个项目，这个相比于nb要复杂一些，在启动窗口中选择Create new project，此时设置项目路径（nb的类似于根目录），然后ide会自动建立一个虚拟环境，也就是目录下面的venv文件夹，后面下载的package会保存到这里以保证多个项目间的package互不影响有助于项目发布足够轻量，当然这些现在没什么用

右键project管理的项目名，默认的是untitled，new-python file就建立了一个文件，可以开始写程序。

```
import numpy as np
np.ones(2,2)
```

这里的运行方式是在菜单栏的Run-Run，会发现numpy下面有红色波浪线，鼠标移动过去可以看到错误信息（No module named numpy...）。这是因为没有找到这个package，需要安装pip install/pip3 install（这里涉及到pip，如果有版本问题需要多百度）

依赖包安装/配置方法：

* 安装方法1：窗口最下面有几个窗口，分别有Python Console、Terminal，前者是直接执行python指令，后者是终端（win下就当cmd），选择终端会显示：

```
Microsoft Windows [版本 10.0.17763.503]
(c) 2018 Microsoft Corporation。保留所有权利。

(venv) C:\XXXXXX\XXXXXX\XXXx\untitled>
```

可以注意到路径前面有个（venv），这就是为什么就算装了anaconda没有找到numpy的原因，在这个虚拟环境里面是完全空的，所以没有这个库，直接输入pip3 install numpy可以自己安装上

* 安装方法2：通过pycharm的工具Ctrl+Alt+S，Projece:xxxx-Projet Interpreter，绿色加号搜索numpy然后install package

* 其他解决方案：直接使用anaconda环境，创建项目时需要设置项目路径，此时下方有Project Interpreter: New....可展开项目，展开后选择Existing Interpreter，将路径选择到/anaconda3/python.exe。已经建立好的项目在Ctrl+Alt+S，Projece:xxxx-Projet Interpreter，选择Project Interpreter后面的齿轮-add增加conda的路径

* > win下conda路径一般是c:\programdata\anaconda3\python.exe

# 常用包使用

NumPy中文文档：[NumPy文档](https://www.numpy.org.cn/)





