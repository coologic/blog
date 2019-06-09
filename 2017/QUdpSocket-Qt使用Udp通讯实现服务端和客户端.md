---
title: " QUdpSocket-Qt使用Udp通讯实现服务端和客户端\t\t"
tags:
  - qt
  - udp
url: 532.html
id: 532
categories:
  - Qt
date: 2017-12-04 17:12:10
---

QNetworkDatagram
----------------

qt网络报文，可用其建立一个通讯内容包括目标ip、端口号、数据内容。同时接收到的信息也未此类型，可以访问接收数据的长度、发送者的ip及端口等信息 详情请见[帮助](http://doc.qt.io/qt-5/qnetworkdatagram.html)

QUdpSocket
----------

有Qt提供的udp通讯的类，详细介绍请见[官方文档](http://doc.qt.io/qt-5/qudpsocket.html) 范例代码见GitHub：**[QtOtherModuleExamples](https://github.com/TechieL/QtOtherModuleExamples)**

客户端
---

注意pro文件要包含QT += network

#include <QHostAddress>
#include <QUdpSocket>
QUdpSocket *m_socket=new QUdpSocket;
m_socket.writeDatagram(msg, QHostAddress::localhost, 8000);

qudpsocket对于发送数据报文提供了三个重载函数：

qint64 	writeDatagram(const char *data, qint64 size, const QHostAddress &address, quint16 port)
qint64 	writeDatagram(const QNetworkDatagram &datagram)
qint64 	writeDatagram(const QByteArray &datagram, const QHostAddress &host, quint16 port)

上面的范例使用的是第三种类型，也可以自行建立QNetworkDatagram对象，修改好内容后直接调用writeDatagram

服务端
---

服务端与客户端类似，需要先绑定端口“bind”，此函数在[QAbstractSocket](http://doc.qt.io/qt-5/qabstractsocket.html)类中提供

bool 	bind(const QHostAddress &address, quint16 port = 0, BindMode mode = DefaultForPlatform)
bool 	bind(quint16 port = 0, BindMode mode = DefaultForPlatform)

具体使用方式：

m_socket = new QUdpSocket;
m_socket->bind(QHostAddress::localhost, 8000);
connect(m_socket, SIGNAL(readyRead()), this, SLOT(receive()));

绑定端口以后，需要将此socket的readyread信号与自定义槽函数连接，当服务端收到消息时会触发此信号。

消息收发
----

由于udp与tcp不同，不需要三次握手建立连接，所以并不会在连接之后记录当前socket。 发送消息在客户端中已经提供示例 接收消息需要使用Qudpsocket提供以下函数：

qint64 pendingDatagramSize() const
qint64 readDatagram(char \*data, qint64 maxSize, QHostAddress \*address = Q\_NULLPTR, quint16 *port = Q\_NULLPTR)
QNetworkDatagram receiveDatagram(qint64 maxSize = -1)

当readyRead信号触发时，可直接通过receiveDatagram函数获取消息报文对象，其内存储消息的发送ip、端口、消息内容等所有信息。 也可以通过pendingDatagramSize判断消息长度，然后通过readDatagram获取消息内容。 在readyRead槽函数中可以先调用QUdpSocket::hasPendingDatagrams判断当前是否有需要读取的消息，若有消息再进行pendingDatagramSize获取需要读取的大小