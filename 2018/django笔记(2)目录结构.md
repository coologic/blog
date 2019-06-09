---
title: " django笔记(2)目录结构\t\t"
tags:
  - django
url: 1023.html
id: 1023
categories:
  - 后端
date: 2018-03-02 21:19:32
---

建立一个first项目，其内包含blog应用，创建指令如下：

django-admin.py startproject first
cd first
python manage.py startapp blog

会具有如下目录结构：

manage.py-根目录文件，可以针对此项目运行django命令

first文件夹-项目目录

| \-\-\- settings.py-项目的默认设置，包括数据库信息、app信息、文件位置等默认配置 | --- urls.py-负责把URL模式映射到应用程序 | ---wsgi.py-用于项目部署

blog文件夹-应用路径

| \-\-\- migrations文件夹-内为自动生成文件，在使用python manage.py makemigrations;python manage.py migrate更新数据库代码后生成 | --- static文件夹-自己建立用于存放静态文件 | --- templates文件夹-自己建立用户存放模板 | --- admin.py-自带后面管理系统，将models.py设置的表以指定规律进行注册后可在后台进行CURD | --- apps.py-blog应用的相关配置 | ---models.py-django的ORM，在此进行数据表设置 | ---tests.py-单元测试 | ---veiws.py-主要处理逻辑，urls.py一般映射到此处进行相应的操作，可以响应post、get等http操作