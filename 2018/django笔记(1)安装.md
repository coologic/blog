---
title: " django笔记(1)安装\t\t"
tags:
  - django
url: 1019.html
id: 1019
categories:
  - 后端
date: 2018-03-01 22:34:05
---

介绍
--

主要是安装python+django+ide，python我直接用的Anaconda3，然后输入命令行输入pip install Django即可完成安装。ide用的pycharm 还可以装一些其他的插件： pip install bootstrap-admin安装基于bootstrap后台管理页面，注意INSTALLED_APPS=('django.contrib.admin'……)前增加改为：

INSTALLED_APPS = (
  'bootstrap_admin',
  'django.contrib.admin',
  ……
)