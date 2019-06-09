---
title: " Ghost版Windows10桌面IE图标删除\t\t"
tags:
  - win10
url: 1003.html
id: 1003
categories:
  - 系统
date: 2018-02-21 13:47:12
---

介绍
--

ghost版本win系统都在桌面带了个无法删除的ie10，无论windows10还是8等系统均无法通过常规方式、软件删除。

删除方法
----

Win+R? regedit打开注册表 KEY\_LOCAL\_MACHINE-SOFTWARE-Microsoft-Windows-CurrentVersion-Explorer-Desktop-NameSpace 首先想办法找Internet Explorer删除即可，一般是{B416D21B-XXXXX} 如果找不到Internet Explorer 那就找到Windows Media 删除即可，一般都是{B416D21B-XXXXX}开头