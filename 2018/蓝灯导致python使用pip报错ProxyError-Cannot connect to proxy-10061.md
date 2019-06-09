---
title: " 蓝灯导致python使用pip报错ProxyError-Cannot connect to proxy-10061\t\t"
tags:
  - lantern
  - pip
  - python
url: 1155.html
id: 1155
categories:
  - python
  - 系统
date: 2018-04-21 10:00:13
---

介绍
--

最初装好了python需要的所有环境，近日需要增加一些package发现pip出错，就算pip install pip --upgrade也出错。

> Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by 'ProxyError('Cannot connect to proxy.', NewConnectionError('<pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x000000000617CE80>: Failed to establish a new connection: \[WinError 10061\] 由于目标计算机积极拒绝，无法连接。',))': /simple/pip/

首先各种百度没找到，没找到能解决的办法，提示内容是proxy错误，但是ie的proxy、win10下的代理设置、Firefox的代理都设置没问题。

解决
--

后来想到了用过“蓝灯”，但是当前并没有开启蓝灯，应该没事，本着试一试的方法打开蓝灯重新关闭，问题解决。。。 所以在win10下关闭蓝灯还是右键先选择“断开”，再退出，避免其对系统做的设置为取消就被关闭。 至于在win10下蓝灯如何设置的系统代理？猜测： 可能是在注册表：HKEY\_CURRENT\_USERS\\Software\\Microsoft\\Windows\\CurrentVersion\\Internet Settings\\Connections 应该可以通过：cmd下netsh winsock reset直接恢复，没有测试过

> 蓝灯在win下：C:\\Users\\{user_name}\\AppData\\Roaming\\byteexec目录下有相关的代理设置文件 若删除蓝灯无法上网，直接把这个目录删了重启就行