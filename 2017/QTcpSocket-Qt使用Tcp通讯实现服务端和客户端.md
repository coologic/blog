---
title: " QTcpSocket-Qt使用Tcp通讯实现服务端和客户端\t\t"
tags:
  - client
  - qt
  - server
  - tcp
url: 530.html
id: 530
categories:
  - Qt
date: 2017-12-04 16:44:01
---

基本功能
----

详细说明请见[官方文档](http://doc.qt.io/qt-5/qabstractsocket.html) 范例代码见GitHub：**[QtOtherModuleExamples](https://github.com/TechieL/QtOtherModuleExamples)**

### pro文件配置

使用Qt网络功能需要在pro文件增加网络库

QT       += network

### QTcpServer服务端建立

QTcpServer server = new QTcpServer();
connect(server,
    &QTcpServer::newConnection,
    this,
    &MainWindow::server\_New\_Connect);//监听
if(!server->listen(QHostAddress::Any, 8000)) {
    qDebug()<<server->errorString();    //错误信息
}

创建server对象以后首先要监听客户端的连接，通过listen函数可以开启监听，需要指定监听的ip和端口号，ip可使用QHostAddress::Any QTcpServer当有新客户端连接时会发出QTcpServer::newConnection的信号，只需要关联到自定义的槽即可。

void MainWindow::server\_New\_Connect() {
    //获取客户端连接
    auto socket_temp = server->nextPendingConnection();//根据当前新连接创建一个QTepSocket
    m\_socket=socket\_temp;//记录此连接用于后续数据读写
    //连接QTcpSocket的信号槽，以读取新数据
    QObject::connect(socket\_temp, &QTcpSocket::readyRead, this, &MainWindow::socket\_Read_Data);
    //当断开连接时发出的信号
    QObject::connect(socket\_temp, &QTcpSocket::disconnected, this, &MainWindow::socket\_Disconnected);
}

上述为一个新连接到来的槽函数范例，利用nextPendingConnection获取到新连接的socket，存储此socket，并关联对应的信号 主要有两个：接收到新数据的信号以及连接断开的信号。 当连接断开可以通过响应信号槽机制实现断开后的操作。

### 客户端建立

客户端为主动连接方，不需要监听，直接建立QTcpSocket即可

m_socket = new QTcpSocket;
m_socket->connectToHost("127.0.0.1",80100,QTcpSocket::ReadWrite);  
connect(m_socket,SIGNAL(connected()),this,SLOT(connected()));

上述例子使用的是信号槽方式等待连接成功，也可以使用阻塞方式：waitForConnected，等到连接成功才会执行后续代码，不需要建立新的槽函数。 通过connectToHost连接指定ip和端口，同时将socket的连接成功的信号与对应槽连接，当连接成功可以将自定义的标记位置为true，可进行相应的收发。

void MainWindow::connected()  {  
    m\_is\_connected = true;
    connect(this->socket,SIGNAL(readyRead()),this,SLOT(readyread()));  //连接接收消息槽
    QObject::connect(socket\_temp,?&QTcpSocket::disconnected,?this,?&MainWindow::socket\_Disconnected);//断开连接
}

当连接成功建议将接收和断开连接的信号进行connect

### 消息收发

*   不阻塞收发：

无论客户端还是服务端只有在建立连接时有差异，后续的消息收发都相同。 首先通过QTcpSocket::close()可以主动断开连接，无论客户端服务端都可以执行主动断开 通过readyRead()信号可以在接到信息后进行信息操作，在槽中执行QTcpSocket::readAll()可以读取缓冲区所有数据 QTcpSocket::send()可发送信息，调用flush可立即发送缓冲区的数据，不需等待。

*   阻塞收发：

Qt同时提供了阻塞收发及连接、断开连接的函数： virtual bool waitForConnected(int msecs = 30000) virtual bool waitForDisconnected(int msecs = 30000) virtual bool waitForBytesWritten(int msecs = 30000) virtual bool waitForReadyRead(int msecs = 30000) 通过上述函数可以实现阻塞连接、断开连接、发送、接收数据内容

其他
--

### 实现单服务器多客户端通讯

网上大部分例子都是单服务器通讯，若不做修改连接多个客户端，会出现只有最后一个通讯有效的情况。 主要原因是在监听到新连接时的处理方式不当：

connect(server, &QTcpServer::newConnection, this, &MainWindow::server\_New\_Connect);//监听 
if(!server->listen(QHostAddress::Any, 8000)) { 
    qDebug()<<server->errorString(); //错误信息 
}

注意上述代码在服务端收到信连接时会固定的调用一个槽函数，而槽函数往往写成下述样式：

void MainWindow::server\_New\_Connect() {
    //获取客户端连接
    auto socket_temp = server->nextPendingConnection();//根据当前新连接创建一个QTepSocket
    m\_socket=socket\_temp;//记录此连接用于后续数据读写
    //连接QTcpSocket的信号槽，以读取新数据
    QObject::connect(socket\_temp, &QTcpSocket::readyRead, this, &MainWindow::socket\_Read_Data);
    //当断开连接时发出的信号
    QObject::connect(socket\_temp, &QTcpSocket::disconnected, this, &MainWindow::socket\_Disconnected);
}

m\_socket=socket\_temp;//记录此连接用于后续数据读写 这一行等于是每次有一个新的连接都替换了旧的连接记录，自然之友最后一个客户端连接有效，正确的可以建立list存储所有连接的socket，当收发数据时根据需要指定socket进行收发。这时将disconnected信号进行connect就具有了作用，当某个连接断开时应该从所有连接链表中删除此记录。

> 由nextPendingConnection创建的QTcpSocket，会有QTcpServer维护，当QTcpServer销毁是会自动销毁所有创建的socket，若想提前释放内容可以在disconnected信号发生时主动delete

### 关于QTcpServer

可能考虑到跨平台问题，Qt使用select实现io多路复用，连接数量限制是1024，若需要poll,epoll等可使用其他库，比如libevent

### 关于数据收发

可以通过setReadBufferSize设置接收缓冲区大小(Qt内部缓冲区大小) 当发送端发送的数据超过buffer大小时会触发readyread信号，需要注意对此情况的处理方法，可以考虑在消息头增加消息长度 未验证：Qt有自己内部的缓冲区，消息发送到系统缓冲区，Qt会读取出来，而调用的Qt函数readall等实际上读取的是Qt内部缓冲区而非系统缓冲区