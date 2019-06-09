title: " MySQL编码utf8与utf8转换\t\t"
tags:
  - mysql
  - utf8
  - 编码转换
  - 导入
url: 226.html
id: 226
categories:
  - 数据库
date: 2017-11-18 19:24:25
---
MySQL在5.5之后增加了这个utf8的编码，mb4：most bytes 4，专门用来兼容四字节的unicode。

utf8转utf8
---------

由于utf8是utf8的超集，只需直接转换即可。 

从低版本mysql可轻松升级到高版本。

utf8转utf8
---------

此部分用于从高版本数据库导入到低版本 

若不做改变可能出现类似于下述提示： 

"ERROR 1273 (HY000) at line 93: Unknown collation: 'utf8\_general\_ci'" 

Unknown character set: 'utf8'

### 方案一：

升级数据库版本

### 方案二：

使用phpmyadmin等，导出过程选择兼容旧版本MYSQL40，此方案有可能无效

### 方案三：

记事本直接打开*.sql文件，进行查找替换 

utf8\_general\_ci 替换成 utf8\_general\_ci 

utf8\_general\_ci 替换成 utf8\_general\_ci 

utf8 替换成 utf8 

保证替换的顺序