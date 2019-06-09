---
title: " django笔记(4)连接MySQL数据库\t\t"
tags:
  - django
url: 1146.html
id: 1146
categories:
  - python
  - 后端
date: 2018-04-18 18:03:33
---

介绍
--

django的模型层使用ORM隐藏了数据库的SQL操作，但对于与数据库的连接还需要单独配置。 ORM：Object Relational Mapping(关系对象映射)

连接Mysql
-------

### 安装客户端

pip install mysqlclient 默认是没有mysql客户端的，默认django使用的sqlite，所以需要安装一下。

### 修改settings.py配置文件

配置文件在 project\_file\\project\_name\\settings.py。项目目录下项目名称的文件夹下面

\# Database
\# https://docs.djangoproject.com/en/2.0/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

默认引擎是sqlite3，数据库名等均设置好了。改为下面的

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django',
        'USER':'django',
        'PASSWORD':'django',
        'HOST':'localhost',
        'PORT':'3306',
    }
}

host是主机地址，本机直接写localhost即可，name是数据库名不是用户名，user才是用户名。

### 更新数据库

python manage.py makemigrations
python manage.py migrate

makemigrations是分析models.py文件，然后根据文件创建migrations目录下的文件，也就是应用最新的数据表配置。此时还没有将配置同步到数据库。使用migrate是将migrations目录下的配置文件同步到数据库，也就是在数据库中修改字段。 后面也可以再接上一个app名字，只更新指定app的数据库

多数据库设置
------

官方2.0版本文档：[地址](https://docs.djangoproject.com/en/2.0/topics/db/multi-db/)，下面中文内容为1.x版本，转自 [Django 多数据库联用](https://code.ziqiangxuetang.com/django/django-multi-database.html) 本文讲述在一个 django project 中使用多个数据库的方法, 多个数据库的联用 以及多数据库时数据导入导出的方法。代码文件下载：[project_name.zip](https://code.ziqiangxuetang.com/media/uploads/files/project_name_20170501173221_818.zip "project_name.zip")（2017年05月01日更新）

### 每个app都可以单独设置一个数据库

settings.py中有数据库的相关设置，有一个默认的数据库 default,我们可以再加一些其它的，比如：

\# Database
\# https://docs.djangoproject.com/en/1.8/ref/settings/#databases
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    },
    'db1': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'dbname1',
        'USER': 'your\_db\_user_name',
        'PASSWORD': 'yourpassword',
        "HOST": "localhost",
    },
    'db2': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'dbname2',
        'USER': 'your\_db\_user_name',
        'PASSWORD': 'yourpassword',
        "HOST": "localhost",
    },
}
 
\# use multi-database in django
\# add by WeizhongTu
DATABASE\_ROUTERS = \['project\_name.database_router.DatabaseAppsRouter'\]
DATABASE\_APPS\_MAPPING = {
    # example:
    #'app\_name':'database\_name',
    'app1': 'db1',
    'app2': 'db2',
}

在project\_name文件夹中存放 database\_router.py 文件，内容如下：

\# -*- coding: utf-8 -*-
from django.conf import settings
 
DATABASE\_MAPPING = settings.DATABASE\_APPS_MAPPING
 
 
class DatabaseAppsRouter(object):
    """
    A router to control all database operations on models for different
    databases.
 
    In case an app is not set in settings.DATABASE\_APPS\_MAPPING, the router
    will fallback to the \`default\` database.
 
    Settings example:
 
    DATABASE\_APPS\_MAPPING = {'app1': 'db1', 'app2': 'db2'}
    """
 
    def db\_for\_read(self, model, **hints):
        """"Point all read operations to the specific database."""
        if model.\_meta.app\_label in DATABASE_MAPPING:
            return DATABASE\_MAPPING\[model.\_meta.app_label\]
        return None
 
    def db\_for\_write(self, model, **hints):
        """Point all write operations to the specific database."""
        if model.\_meta.app\_label in DATABASE_MAPPING:
            return DATABASE\_MAPPING\[model.\_meta.app_label\]
        return None
 
    def allow_relation(self, obj1, obj2, **hints):
        """Allow any relation between apps that use the same database."""
        db\_obj1 = DATABASE\_MAPPING.get(obj1.\_meta.app\_label)
        db\_obj2 = DATABASE\_MAPPING.get(obj2.\_meta.app\_label)
        if db\_obj1 and db\_obj2:
            if db\_obj1 == db\_obj2:
                return True
            else:
                return False
        return None
 
    # for Django 1.4 - Django 1.6
    def allow_syncdb(self, db, model):
        """Make sure that apps only appear in the related database."""
 
        if db in DATABASE_MAPPING.values():
            return DATABASE\_MAPPING.get(model.\_meta.app_label) == db
        elif model.\_meta.app\_label in DATABASE_MAPPING:
            return False
        return None
 
    # Django 1.7 - Django 1.11
    def allow\_migrate(self, db, app\_label, model_name=None, **hints):
        print db, app\_label, model\_name, hints
        if db in DATABASE_MAPPING.values():
            return DATABASE\_MAPPING.get(app\_label) == db
        elif app\_label in DATABASE\_MAPPING:
            return False
        return None

这样就实现了指定的 app 使用指定的数据库了,当然你也可以多个sqlite3一起使用，相当于可以给每个app都可以单独设置一个数据库！如果不设置或者没有设置的app就会自动使用默认的数据库。

### 使用指定的数据库来执行操作

在查询的语句后面用 using(dbname) 来指定要操作的数据库即可

\# 查询
YourModel.objects.using('db1').all() 
或者 YourModel.objects.using('db2').all()
 
\# 保存 或 删除
user\_obj.save(using='new\_users')
user\_obj.delete(using='legacy\_users')

### 多个数据库联用时数据导入导出

使用的时候和一个数据库的区别是: **如果不是defalut(默认数据库）要在命令后边加 --database=数据库对应的settings.py中的名称**? 如： --database=db1 ?或 --database=db2 **数据库同步（创建表）**

\# Django 1.6及以下版本
python manage.py syncdb #同步默认的数据库，和原来的没有区别
 
\# 同步数据库 db1 (注意：不是数据库名是db1,是settings.py中的那个db1，不过你可以使这两个名称相同，容易使用)
python manage.py syncdb --database=db1
 
\# Django 1.7 及以上版本
python manage.py migrate --database=db1

**数据导出**

python manage.py dumpdata app1 --database=db1 > app1_fixture.json
python manage.py dumpdata app2 --database=db2 > app2_fixture.json
python manage.py dumpdata auth > auth_fixture.json

**数据库****导入**

python manage.py loaddata app1_fixture.json --database=db1
python manage.py loaddata app2_fixture.json --database=db2