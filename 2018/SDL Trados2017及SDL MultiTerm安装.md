---
title: " SDL Trados2017及SDL MultiTerm安装\t\t"
tags:
  - trados
url: 976.html
id: 976
categories:
  - 翻译
date: 2018-02-12 20:42:16
---

SDL Trados
----------

2017版本百度搜索可以很容易搜到安装包，大概300多兆，版权原因不再提供，最后记着先打开软件，然后再把那个文件覆盖即可。 trados只是翻译软件，同时可以创建、编辑和使用记忆库。但对于术语库仅可使用，术语库的编辑等需要另外的软件。

MultiTerm
---------

### 说明及安装

此软件是sdl提供的数据库创建、管理软件，可以置顶术语的存储格式并添加每一项术语。 软件的注册会自动调用trados的注册，若trados已注册则相同或低版本的mt可以自动注册成功。

### multierm2014使用java7.71

java控制面板中将安全策略的“使用浏览器中……”勾选，并把下面的条拉到中。重启mt即可

### multiterm 2014使用高版本java8不显示术语内容

由于java高版本改变了安全策略，以往的中等安全已经取消，最低为高级。高安全默认认定file:\\\目录为不安全 建议删除java装低版本，当然装2017最省事。否则可尝试同样在java控制面板，安全-编辑站点列表 增加下面两项： file:/ file:///C:/Program Files (x86)/Common Files/SDL/MultiTerm11/Editor/script/connector.js 重启mt，我试了没成功。