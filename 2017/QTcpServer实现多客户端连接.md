---
title: " QTcpServer实现多客户端连接\t\t"
tags:
  - qt
  - socket
  - tcp
url: 760.html
id: 760
categories:
  - Qt
date: 2017-12-21 21:35:50
---

介绍
--

QTcpServer使用请见：[**QTcpSocket-Qt使用Tcp通讯实现服务端和客户端**](http://techieliang.com/2017/12/530/) QTcpServer类默认提供的只有无参数的newConnection的信号，这样虽然知道有人连接了，并且可以通过nextPendingConnection获取连接的socket，但并不便于管理，尤其是在连接断开以后无法判断具体那个断开了，因为QTcpSocket只提供了无参的disconnected信号。。。 这样就算在newConnection是存储一个list或者map，也无法在disconnected是知道具体是那一项断开连接，给不同的QTcpSocket的信号指向不同的槽。 实际上socket有自己的句柄，并通过下述函数在初步连接时就赋予了对应的socketDescriptor

virtual void incomingConnection(qintptr socketDescriptor)

当有client连接时，首先是此方法被调用，可自行在此方法内建立QTcpSocket并将socketDescriptor值赋予socket，并在socket断开时告知此标识符

范例
--

![gif](https://github.com/TechieL/MyBlogPictureBackup/raw/master/%E5%9B%BE%E7%89%87/%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/QTcpServer%E5%AE%9E%E7%8E%B0%E5%A4%9A%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%BF%9E%E6%8E%A5/gif.gif) 源码请见GitHub：**[QtOtherModuleExamples](https://github.com/TechieL/QtOtherModuleExamples)** tcp_server.h

#ifndef TCP\_SERVER\_H
#define TCP\_SERVER\_H
#include <QTcpServer>
namespace tcp\_server\_private {
    class TcpServerPrivate;
}
class QTcpSocket;
/\*\*
 \* @brief Tcp多客户端服务器
 */
class TcpServer : public QTcpServer {
    Q_OBJECT
public:
    /\*\*
     \* @brief 构造函数
     \* @param parent 父QObject
     */
    explicit TcpServer(QObject *parent = Q_NULLPTR);
    /\*\*
     \* @brief 析构函数
     \*    非多线程模式行为：关闭所有连接后析构
     \*    多线程模式行为：关闭所有连接及线程池后析构
     */
    ~TcpServer();
signals:
    /\*\*
     \* @brief 客户端连入
     \* @param 连接句柄
     \* @param socket指针
     */
    void ClientConnected(qintptr, QTcpSocket*);//发送新用户连接信息
    /\*\*
     \* @brief socket已断开连接
     \*   若需要在socket后析构后进行操作的可连接此信号
     \* @param 连接句柄
     */
    void ClientDisconnected(qintptr);
    /\*\*
     \* @brief 主动断开连接信号
     \*   若服务端想要主动断开与客户端连接将会发出此信号
     \*   此信号发出这表明进行断开操作不表明断开成功，成功以SocketDisconnected信号为准
     \* @param 连接句柄
     */
    void InitiativeDisConnectClient(qintptr);
protected slots:
    /\*\*
     \* @brief 客户端已断开槽
     \*   此槽与客户端的已断开信号连接
     \* @param handle
     */
    void ClientDisconnectedSlot(qintptr handle);
protected:
    /\*\*
     \* @brief 重写-有连接到来
     \*    连接到来不一定连接，会根据maxPendingConnections决定是否连接
     \* @param handle 连接句柄
     */
    virtual void incomingConnection(qintptr handle);
private:
    tcp\_server\_private::TcpServerPrivate *private_;
};
#endif // TCP\_SERVER\_H

tcp_server.cpp

#include "tcp_server.h"
#include "tcp\_server\_private.h"
//构造函数
TcpServer::TcpServer(QObject *parent)
    : QTcpServer(parent),
      private_(new tcp\_server\_private::TcpServerPrivate) {
}
//析构函数
TcpServer::~TcpServer() {
    for(tcp\_server\_private::TcpSocket *client : private_->clients.values()) {
        client->disconnectFromHost();
        auto handle = client->socketDescriptor();
        client->deleteLater();
        //告知其他调用者 当前socket断开，避免有需要在socket后执行的方法
        emit ClientDisconnected(handle);
    }
    if(private_)
        delete private_;
    this->close();
}
//重写-有连接到来
void TcpServer::incomingConnection(qintptr handle) {
    //超出最大练级数量关闭连接
    if (private_->clients.size() > maxPendingConnections()) {
        QTcpSocket tcp;
        tcp.setSocketDescriptor(handle);
        tcp.disconnectFromHost();
        return;
    }
    auto client\_socket = new tcp\_server_private::TcpSocket(handle);
    Q\_ASSERT(client\_socket->socketDescriptor() == handle);
    //socket断开连接的信号与server槽连接
    connect(client_socket,
            &tcp\_server\_private::TcpSocket::ClientDisconnected,
            this,
            &TcpServer::ClientDisconnectedSlot);
    //主动断开连接的操作
    connect(this,
            &TcpServer::InitiativeDisConnectClient,
            client_socket,
            &tcp\_server\_private::TcpSocket::DisconnectSocket);
    //map记录
    private_->clients.insert(handle, client_socket);
    qDebug()<<handle<<"connected";
    emit ClientConnected(handle, client_socket);
}
//客户端已断开槽
void TcpServer::ClientDisconnectedSlot(qintptr handle) {
    //map中移除
    private_->clients.remove(handle);
    qDebug()<<handle<<"disconnected";
    //发出信号
    emit ClientDisconnected(handle);
}

private

#ifndef TCP\_SERVER\_PRIVATE_H
#define TCP\_SERVER\_PRIVATE_H
#include <QTcpSocket>
namespace tcp\_server\_private {
class TcpSocket : public QTcpSocket {
    Q_OBJECT
public:
    /\*\*
     \* @brief 构造函数
     \* @param socketDescriptor 连接句柄
     \* @param parent 父QObject
     */
    TcpSocket(qintptr handle, QObject *parent = 0);
signals:
    /\*
     \* 已断开连接信号
     */
    void ClientDisconnected(qintptr);
public slots:
    /\*\*
     \* @brief 断开连接
     \* @param handle 连接句柄
     */
    void DisconnectSocket(qintptr handle);
private:
    qintptr handle_;
};
/\*\*
 \* @brief Tcp多客户端服务器私有类
 */
class TcpServerPrivate {
public:
    QMap<int, TcpSocket *> clients; ///所有连接的map
};
}//tcp\_server\_private
#endif // TCP\_SERVER\_PRIVATE_H
//cpp
#include "tcp\_server\_private.h"
namespace tcp\_server\_private {
//构造函数
TcpSocket::TcpSocket(qintptr handle, QObject *parent)
    : QTcpSocket(parent), handle_(handle) {
    this->setSocketDescriptor(handle_);
    //断开连接消息
    connect(this,&TcpSocket::disconnected,
            \[&\](){
        this->deleteLater();
        emit this->ClientDisconnected(handle_);
    });
}
//主动断开连接槽
void TcpSocket::DisconnectSocket(qintptr handle) {
    if (handle == handle_)
        disconnectFromHost();
}
}

*   incomingConnection首先判断是否超出最大连接数量，超出就断开新链接，最大连接数量在maxPendingConnections方法获取，而当前已连接client在自定义server的TcpServerPrivate::clients这个QMap记录，此map记录了socketDescriptor和socket指针的映射关系
*   若未达最大连接数量，则连接创建新的tcpsocket，并将socketDescriptor赋值到socket对象，最后发出ClientConnected信号，此信号带有新链接的socket的指针，可以用于自定义收发信号槽。
*   socket对象的disconnected信号直接connect到了lambda表达式以发出新的信号，新信号ClientDisconnected，并带有socketDescriptor句柄标识符，从而避免了socket断开连接后不知道具体哪个断开
*   socket在创建时均吧ClientDisconnected信号与server的ClientDisconnectedSlot槽连接，当有连接断开时会动态维护map记录
*   server析构时以disconnectFromHost方法主动断开和所有客户端连接，若需要等客户端先断开可以用waitForDisconnected

> 上述范例将TcpSocket定义在tcp\_server\_private命名空间不对外可见，且TcpServer返回值也是QTcpSocket，若需要继承QTcpSocket做更多自定义需要将TcpSocket移出