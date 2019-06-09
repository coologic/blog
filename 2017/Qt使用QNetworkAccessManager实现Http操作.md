---
title: " Qt使用QNetworkAccessManager实现Http操作\t\t"
tags:
  - http
  - QNetworkAccessManager
  - qt
url: 649.html
id: 649
categories:
  - Qt
date: 2017-12-11 21:02:35
---

介绍
--

QtNetwork是Qt网络操作模块，提供了基于TCP/IP的各种API，除了之前介绍过的最基础的TCP及UDP通讯：[QTcpSocket-Qt使用Tcp通讯实现服务端和客户端](http://techieliang.com/2017/12/530/)、[QUdpSocket-Qt使用Udp通讯实现服务端和客户端](http://techieliang.com/2017/12/532/)，还提供了HTTP、HTTPS、FTP等高级API，并统一使用QNetworkAccessManager进行操作。Ftp使用请见：[Qt使用QNetworkAccessManager实现Ftp操作](http://techieliang.com/2017/12/653/)

> qt4x分别使用QFtp和QHttp，5以后统一用QNetworkAccessManager

![](http://wx4.sinaimg.cn/mw690/a8dbb8d6ly1fmn86ign49j20mw0ekmy1.jpg) 范例代码见GitHub：**[QtOtherModuleExamples](https://github.com/TechieL/QtOtherModuleExamples)**

HTTP请求方法
========

此节内容来源：[HTTP请求方法](http://www.runoob.com/http/http-methods.html) 根据HTTP标准，HTTP请求可以使用多种请求方法。 HTTP1.0定义了三种请求方法： GET, POST 和 HEAD方法。 HTTP1.1新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。

序号

方法

描述

1

GET

请求指定的页面信息，并返回实体主体。

2

HEAD

类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头

3

POST

向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。

4

PUT

从客户端向服务器传送的数据取代指定的文档的内容。

5

DELETE

请求服务器删除指定的页面。

6

CONNECT

HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。

7

OPTIONS

允许客户端查看服务器的性能。

8

TRACE

回显服务器收到的请求，主要用于测试或诊断。

QNetworkAccessManager接口介绍
-------------------------

官方文档：[QNetworkAccessManager](http://doc.qt.io/qt-5/qnetworkaccessmanager.html) 接口很多，就不全部复制过来了，如果机器装着qt5，可以直接在助手看。 可以一目了然的看到几个熟悉词汇的api：post、get、put、head，当然还有几个cookie相关的方法。

QNetworkReply *get(const QNetworkRequest &request)
QNetworkReply *head(const QNetworkRequest &request)
bool isStrictTransportSecurityEnabled() const
bool isStrictTransportSecurityStoreEnabled() const
NetworkAccessibility networkAccessible() const
QNetworkReply \*post(const QNetworkRequest &request, QIODevice \*data)
QNetworkReply *post(const QNetworkRequest &request, const QByteArray &data)
QNetworkReply \*post(const QNetworkRequest &request, QHttpMultiPart \*multiPart)
QNetworkProxy proxy() const
QNetworkProxyFactory *proxyFactory() const
QNetworkReply \*put(const QNetworkRequest &request, QIODevice \*data)
QNetworkReply *put(const QNetworkRequest &request, const QByteArray &data)
QNetworkReply \*put(const QNetworkRequest &request, QHttpMultiPart \*multiPart)

可以发现使用manager还需要几个类：QNetworkRequest 专门用于请求的，QNetworkReply 接收请求的响应

### QNetworkRequest

同样看帮助文档：[http://doc.qt.io/qt-5/qnetworkrequest.html](http://doc.qt.io/qt-5/qnetworkrequest.html)

void setAttribute(Attribute code, const QVariant &value)
void setHeader(KnownHeaders header, const QVariant &value)
void setMaximumRedirectsAllowed(int maxRedirectsAllowed)
void setOriginatingObject(QObject *object)
void setPriority(Priority priority)
void setRawHeader(const QByteArray &headerName, const QByteArray &headerValue)
void setSslConfiguration(const QSslConfiguration &config)
void setUrl(const QUrl &url)

主要就是这几个写方法，分别对一个请求的不同类进行配置。

> 客户端发送一个HTTP请求到服务器的请求消息包括以下格式：请求行（request line）、请求头部（header）、空行和请求数据四个部分组成，下图给出了请求报文的一般格式。请求行组成：请求方法+空格+url+空格+协议版本+回车符+换行符。详情见[HTTP 消息结构](http://www.runoob.com/http/http-messages.html)

对于header，qt提供了一个枚举类型KnownHeaders分别表示不同项：

Constant

Value

Description

`QNetworkRequest::ContentDispositionHeader`

`6`

Corresponds to the HTTP Content-Disposition header and contains a string containing the disposition type (for instance, attachment) and a parameter (for instance, filename).

`QNetworkRequest::ContentTypeHeader`

`0`

Corresponds to the HTTP Content-Type header and contains a string containing the media (MIME) type and any auxiliary data (for instance, charset).

`QNetworkRequest::ContentLengthHeader`

`1`

Corresponds to the HTTP Content-Length header and contains the length in bytes of the data transmitted.

`QNetworkRequest::LocationHeader`

`2`

Corresponds to the HTTP Location header and contains a URL representing the actual location of the data, including the destination URL in case of redirections.

`QNetworkRequest::LastModifiedHeader`

`3`

Corresponds to the HTTP Last-Modified header and contains a [QDateTime](http://doc.qt.io/qt-5/qdatetime.html) representing the last modification date of the contents.

`QNetworkRequest::CookieHeader`

`4`

Corresponds to the HTTP Cookie header and contains a [QList](http://doc.qt.io/qt-5/qlist.html)<[QNetworkCookie](http://doc.qt.io/qt-5/qnetworkcookie.html)\> representing the cookies to be sent back to the server.

`QNetworkRequest::SetCookieHeader`

`5`

Corresponds to the HTTP Set-Cookie header and contains a [QList](http://doc.qt.io/qt-5/qlist.html)<[QNetworkCookie](http://doc.qt.io/qt-5/qnetworkcookie.html)\> representing the cookies sent by the server to be stored locally.

`QNetworkRequest::UserAgentHeader`

`7`

The User-Agent header sent by HTTP clients.

`QNetworkRequest::ServerHeader`

`8`

The Server header received by HTTP clients.

请求类主要是进行对于地址，还给出了QUrl 类，详情见后。

### QNetworkReply

帮助文档：[http://doc.qt.io/qt-5/qnetworkreply.html](http://doc.qt.io/qt-5/qnetworkreply.html) 此类继承自QIODevice，可使用QIODevice的所有接口，包括readall读取接收的所有信息。 同时此类提供了finished信号，在响应完斥候发出此信号，可关联自定义槽函数函数，做响应处理。 提供了attribute属性函数，可以判断响应的类型，比如RedirectionTargetAttribute是目标url告知进行重定向

> QNetworkReply不会自动释放空间，一定要主动处理内存释放，可以调用QObject::deleteLater令其自动释放空间

范例
--

.h文件

#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QtNetwork>
#include <QFile>
namespace Ui {
class MainWindow;
}

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = 0);
    ~MainWindow();
    void Get(QUrl u);
private slots:
    void on\_pushButton\_clicked();
    void finished();
private:
    Ui::MainWindow *ui;
    QNetworkAccessManager manager;
    QUrl url;
    QNetworkReply *reply;
    QString html_text;
};

#endif // MAINWINDOW_H

.cpp文件

#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow) {
    ui->setupUi(this);
    reply = Q_NULLPTR;
}

MainWindow::~MainWindow() {
    delete ui;
}

void MainWindow::Get(QUrl u) {
    QNetworkRequest request;
    url=u;
    request.setUrl(url);
    if(reply != Q_NULLPTR) {//更改reply指向位置钱一定要保证之前的定义了自动delete
        reply->deleteLater();
    }
    reply = manager.get(request);
    qDebug() << "start get";
    connect(reply, &QNetworkReply::finished, this, &MainWindow::finished);
}

void MainWindow::on\_pushButton\_clicked() {
    html_text = "";
    Get(QUrl("http://www.baidu.com/"));

}

void MainWindow::finished() {
    QByteArray bytes = reply->readAll();
    const QVariant redirectionTarget = reply->attribute(QNetworkRequest::RedirectionTargetAttribute);
    reply->deleteLater();
    reply = Q_NULLPTR;
    if (!redirectionTarget.isNull()) {//如果网址跳转重新请求
        const QUrl redirectedUrl = url.resolved(redirectionTarget.toUrl());
        qDebug()<<"redirectedUrl:"<<redirectedUrl.url();
        Get(redirectedUrl);
        return;
    }
    qDebug()<<"finished";
    html_text = bytes;
    qDebug()<<"get ready,read size:"<<html_text.size();
//    QFile f("result.html");//写出文件
//    f.open(QFile::WriteOnly);
//    f.write(bytes);
//    f.close();
}

程序很简单 on\_pushButton\_clicked 作为范例的入口，当点击按钮时开始访问，会传递百度的网址的到Get函数。 get函数中进行get操作，并把返回值的reply connect到finished槽。 finished中首先判断响应头是否有重定向要求，如果有重定向则销毁当前reply并利用指定的新地址重新调用get，可以试验“www.sina.com”会指向"www.sina.com.cn" 最后通过readll读取所有数据并保存到文件，双击打开文件可以看到效果。当然不会包含图片信息

其他
--

### post使用

post其实和get类似，只不过同时还传递了串数据 post(request, data)即可，其他操作完全一样

### QUrlQuery

http://techieliang.com/wp-admin/post.php?post=000&action=edit&name=techieliang 对于上述指令直接使用QUrl赋值也是可以的，但是如果后续参数一直在变动，需要自己封装一个字符串拼接的过程。简单的办法是使用QUrlQuery

QUrl url("http://techieliang.com/wp-admin/post.php");
QUrlQuery tt;
tt.addQueryItem("post","000");
tt.addQueryItem("action","edit");
tt.addQueryItem("name","techieliang");
url.setQuery(tt);
qDebug()<<url.url();
tt.clear();
tt.addQueryItem("post","000");
url.setQuery(tt);
qDebug()<<url.url();
url.setUrl("http://techieliang.com/wp-admin/post.php?");
qDebug()<<url.url();
url.setQuery(tt);
qDebug()<<url.url();

结果

"http://techieliang.com/wp-admin/post.php?post=000&action=edit&name=techieliang"
"http://techieliang.com/wp-admin/post.php?post=000"
"http://techieliang.com/wp-admin/post.php?"
"http://techieliang.com/wp-admin/post.php?post=000"

*   setUrl会将Url改为新值，并清空Query，直接更改url后需要重新setQuery
*   setQuery不会改变Url值，可以不断的setQuery去构造不同的参数
*   QUrl会自动处理url后的?若setUrl的值末尾没有?会自动在url和query之间增加，若已经包含则不会重复