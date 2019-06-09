---
title: " Windows下Docker安装\t\t"
tags:
  - docker
url: 1007.html
id: 1007
categories:
  - 系统
date: 2018-02-26 14:03:58
---

介绍
--

最近装docker经历了一番波折。。。记载如下

问题1
---

现在docker下载安装后默认使用的是hyper-v。首先hyper-v和VT虚拟是冲突的。但是要开启hyper-v首先要开启vt。由于系统自带的hv启动以后vt默认关闭了，此时Vmware、安卓虚拟机等等均无法使用。当然为了用docker，我先放弃了那些。

问题2
---

最奇怪的就是启动了hyper-v在Windows功能里面完全都打上了对勾。包括管理工具及平台，也就说明hv启动成功了。但是本地虚拟服务就是没有开启，也就是hv管理工具打开以后，没有任何可选的可用的虚拟机，进而导致docker无法使用。最终也为解决。 怀疑：hv服务还使用了一些其他的win服务作为基础服务，但是被我关了。。没有找到具体用了哪些服务。

解决
--

既然无法用hv做虚拟，那就安装DockerToolbox，这个的下载主页面没找到，在文档里面提供了：[地址](https://docs.docker.com/toolbox/toolbox_install_windows/) 安装完成后会有是哪个图标一个VM的，一个docker quickstart terminal、一个kitematic。 docker quickstart terminal：通过命令方式操作docker kitematic：窗口化操作，可以直观的搜索安装docker hub的镜像 vm：虚拟机，初始装完后里面不带任何内容。 首先启动kitematic，软件会自动下载安装默认镜像，完成后打开vm会看到一个default虚拟系统正在运行。

总结
--

虽然我认为用hyper-v应该会更稳定，但是受限于hv好像可能被系统的“未知配置”影响，看来用toolbox可以保证安装过程更顺利。