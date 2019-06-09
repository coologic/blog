title: " Qt Creator子目录项目-类似VS解决方案\t\t"
tags:
  - qt
  - qt creator
  - 子目录
url: 479.html
id: 479
categories:
  - Qt
date: 2017-11-30 21:59:43
---
通过Qt Creator-新建项目-其他项目-子目录项目，可以建立一个类似于VS解决方案的工程，其内可以建立多个项目，并且可以配置多个项目之间的构建顺序。

简述
--

新建完成后，会发现项目很简单，只有一个pro文件，文件里也只有一行

`TEMPLATE = subdirs`

在项目名称可以右键，新建子项目，此后的子项目操作与正常qt工程完全一致 

建立两个子项目以后，pro文件如下：
```
TEMPLATE = subdirs

SUBDIRS += \
    untitled \
    untitled1
```
SUBDIRS关键字表明的是子项目名称

子项目构建顺序
-------

pro增加? CONFIG+=ordered?? 可限制构建顺序为 SUBDIRS? 的顺序，此时只需要调整SUBDIRS 中各项顺序即可 

还可写

`untitled.depends = untitled1`

表明untitle依赖于untitled1，会优先构建untitled1