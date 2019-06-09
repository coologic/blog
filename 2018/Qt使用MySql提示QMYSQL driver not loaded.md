---
title: " Qt使用MySql提示QMYSQL driver not loaded\t\t"
tags:
  - mysql
  - qt
url: 891.html
id: 891
categories:
  - Qt
date: 2018-01-18 22:12:05
---

介绍
--

Qt5.10使用Mysql提示如下错误：

> QSqlDatabase: QMYSQL driver not loaded QSqlDatabase: available drivers: QSQLITE QMYSQL QMYSQL3 QODBC QODBC3 QPSQL QPSQL7 QSqlDatabasePrivate::addDatabase: duplicate connection name 'PalmshopSqlConnection', old connection removed.

调用方式如下：

QSqlDatabase::addDatabase("QMYSQL");

解决方案
----

首先Qt后续版本已经打包了支持的数据库的驱动，即完全可以直接通过QSqlDatabase::addDatabase("QMYSQL")使用对应的数据库，支持的数据库列表如下： QPSQL、QMYSQL、QOCI、QODBC、QDB2、QTDS、QSQLITE、QIBASE 每个数据库对应的类： QPSQLDriver、QMYSQLDriver、QOCIDriver、QODBCDriver、QDB2、QTDSDriver、QSQLiteDriver、QIBaseDriver 驱动文件在：D:\\Qt\\Qt5.10.0\\5.10.0\\mingw53\_32\\plugins\\sqldrivers，地址请对应修改 但是这些驱动只是让QSqlDatabase能够使用QMYSQLDriver类，但这个类还是会调用对应数据库的操作库，所以必须安装MySQL，准确来说是必须有MySQL的库“libmysql.dll”和“libmysqld.dll”，这两个文件，文件一般在“\\mysql5.7.14\\lib\\”路径，只需要将这两个文件复制到“D:\\Qt\\Qt5.10.0\\5.10.0\\mingw53\_32\\bin”下即可。 重新编译运行即可正常使用。

> 发布release版本时附带相关文件，不要忘了带上“libmysql.dll”。windeployqt自动打包不会打包此文件，需要自行拷贝