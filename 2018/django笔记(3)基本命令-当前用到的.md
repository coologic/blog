---
title: " django笔记(3)基本命令-当前用到的\t\t"
tags:
  - django
url: 1027.html
id: 1027
categories:
  - 后端
date: 2018-03-02 22:01:37
---

介绍
--

主要有两个文件一个是django自带文件django-admin.py，另一个是项目根目录下的manage.py。我最开始的理解是manage.py实际上就是调用的django-admin.py，只不过django-admin.py进行的是全盘操作，需要指定位置。而manage.py自动定义了路径就是当前项目，看过[官方文档](https://docs.djangoproject.com/en/2.0/ref/django-admin/)以后看来理解的并没有太大出入：

> `manage.py` does the same thing as `django-admin` but takes care of a few things for you:
> 
> *   It puts your project’s package on `sys.path`.
> *   It sets the `DJANGO_SETTINGS_MODULE` environment variable so that it points to your project’s `settings.py` file.

常用指令
----

下面的django-admin.py和manage.py使用无效的时候可以去掉.py用manage和django-admin

### 创建项目

django-admin.py startproject project_name

此时还没有项目，所以需要用django-admin.py，在当前shell发起路径创建项目，需要先用cd命令指向目标目录

### 创建APP

python manage.py startapp app_name

如果用pycharm，此时可以直接在terminal窗口输入指令即可直接调用项目目录下的manage.py

### 数据表操作

python manage.py makemigrations
python manage.py migrate

makemigrations是分析models.py文件，然后根据文件创建migrations目录下的文件，也就是应用最新的数据表配置。此时还没有将配置同步到数据库。使用migrate是将migrations目录下的配置文件同步到数据库，也就是在数据库中修改字段。

> 这两个命令都还带有参数，具体可参考官方文档。若不输入参数则默认更新所有app，也可以通过参数指定具体app

### 开发服务器

python manage.py runserver \[addrport\]

django可以直接启动开发服务器，名字也就说明了只建议开发时使用，不要用在生产环境。可以不指定端口号默认是本地可以通过8000端口访问。也可以指定端口号实现本地指定端口号访问。 \[addrport\]可以输入0.0.0.0:n实现外网通过n端口访问此开发服务器。

### 超级管理员

python manage.py createsuperuser
python manage.py changepassword username

django自带账户管理系统，而超级管理员可以通过后台系统登录后管理everything。第一行代码创建账户，第二行在忘记密码时可以更改密码。

### 获取django版本号

django-admin version

### 获取命令列表

python manage.py

不带任何参数，django会返回所有的可用命令列表

注意
--

运行以后如果无法访问，请按照如下顺序检查：

1.  开启admin，访问admin，如果可以访问，则自建的url有误
2.  检查进程是否正在运行django服务
3.  使用traceroute（linux）、tracert（win）查看路由，是否正确的到达了本机的地址，若错误考虑vpn等未关闭情况