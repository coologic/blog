---
title: " Qt使用QNetworkAccessManager实现Ftp操作\t\t"
tags:
  - ftp
  - QNetworkAccessManager
  - qt
url: 653.html
id: 653
categories:
  - Qt
date: 2017-12-11 21:04:05
---

介绍
--

QtNetwork是Qt网络操作模块，提供了基于TCP/IP的各种API，除了之前介绍过的最基础的TCP及UDP通讯：[QTcpSocket-Qt使用Tcp通讯实现服务端和客户端](http://techieliang.com/2017/12/530/)、[QUdpSocket-Qt使用Udp通讯实现服务端和客户端](http://techieliang.com/2017/12/532/)，还提供了HTTP、HTTPS、FTP等高级API，并统一使用QNetworkAccessManager进行操作。HTTP操作请看：[Qt使用QNetworkAccessManager实现Http操作](http://techieliang.com/2017/12/649/)

> qt4x分别使用QFtp和QHttp，5以后统一用QNetworkAccessManager

使用说明
----

首先请看：[Qt使用QNetworkAccessManager实现Http操作](http://techieliang.com/2017/12/649/) ftp与http操作完全一样，只不过需要设置一下用户名、密码、地址、端口、文件路径。这些操作只需要对QUrl做配置，其余不需要改变 分别调用QUrl的

void setUserName(const QString &userName, ParsingMode mode = DecodedMode)
void setPassword(const QString &password, ParsingMode mode = DecodedMode)
void setHost(const QString &host, ParsingMode mode = DecodedMode)
void setPort(int port)
void?setPath(const QString &path, ParsingMode mode = DecodedMode)

上述对于还需要配置一下：setScheme("ftp")

void setScheme(const QString &scheme)

这个指的是 ftp:// 和http://由于并没有通过setUrl设置url，需要主动的指定scheme 上面分别指定了ftp以及ip，port及path，这四项可以直接setUrl但是建议分别调用组合

上传与下载
-----

下载就是get,除了QUrl配置不一样其他与http完全相同，最后把get得到的所有数据保存到文件即可 上传那就是put，先从文件读取出所有数据，然后put即可，注意读取完存为`QByteArray`类型