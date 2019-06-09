---
title: " Windows下基于http的git服务器搭建-gitstack\t\t"
url: 514.html
id: 514
categories:
  - 其他
date: 2017-12-02 11:39:15
tags:
---

下载安装
----

[官网](https://gitstack.com/)下载即可 安装流程也很简洁方便。[安装步骤](https://gitstack.com/getting-started/) 安装完成后可通过管理地址：http://localhost/gitstack登陆后台进行操作 [基本操作说明](https://gitstack.com/web-repository-browsing/) 客户端可直接使用Tortoise_Git进行操作。_

注意
--

### 关于Python冲突问题

GitStack使用python(自带，不需要单独安装)搭建的http服务，python是2.7版本，安装完成后会新建系统环境变量： PYTHONHOME：C:\\GitStack\\python PYTHONPATH：C:\\GitStack\\python\\lib 注意这个环境变量直接置顶了python根目录和路径，不是单纯的在path变量里添加了路径，这样可以保证有其他版本的python的环境下gitstack也能稳定运行找到自带的python路径。 但是这两个环境变量是通用的，对于任意版本python均有效，所以无论装其他任何版本的python，包括anaconda在启动的时候均会访问此路径下的文件，若版本不同会报错。 解决方案：当前未找并存gitstack及其他版本python方案，暂时解决方法是，删除这两个变量可以正常使用其他版本python，添加以后需要重新启动gitstack服务才能正常使用gitstack，未找到并存方法。

### gitstack密码重置

密码遗忘以后可以重置密码 cd c:\\GitStack\\app ? （gitstack目录因安装而异） python manage.py shell 其中 manage.py

from django.contrib.auth.models import User
u = User.objects.get(username__exact='admin')
u.set_password('password')
u.save()
quit()

_gitstack与wamp冲突_
-----------------

打开任务管理-服务 找到服务“GitStack”、“wampmysqld”、“wampapache”,根据需要进行开关即可。

> wamp的对应服务名前面均带了wamp而不是原始名称