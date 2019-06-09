---
title: " QSerialPort-Qt串口通讯\t\t"
tags:
  - qt
  - serial
  - 串口
url: 534.html
id: 534
categories:
  - Qt
date: 2017-12-04 18:42:16
---

介绍
--

Qt对串口通讯提供了专用类[QSerialPort](http://doc.qt.io/qt-5/qserialport.html)，需要在pro文件增加：QT += serialport，其继承自[QIODevice](http://doc.qt.io/qt-5/qiodevice.html) 相关类还有QSerialPortInfo 提供当前设备串口信息

QSerialPortInfo
---------------

QSerialPortInfo::availablePorts(); 可以获取当前设备的所有串口信息，提供了以下操作函数，可获得对应的信息类型。

QString description() const
bool hasProductIdentifier() const
bool hasVendorIdentifier() const
bool isBusy() const
bool isNull() const
QString manufacturer() const
QString portName() const
quint16 productIdentifier() const
QString serialNumber() const
void swap(QSerialPortInfo &other)
QString systemLocation() const
quint16 vendorIdentifier() const

portName一般为“COMX”；Description为描述信息；serialNumber为编号，此号一般不相同可用于串口设备识别。

QSerialPort
-----------

参考类[帮助文档](http://doc.qt.io/qt-5/qserialport.html) 相关串口配置函数：

bool sendBreak(int duration = 0)
bool setBaudRate(qint32 baudRate, Directions directions = AllDirections)
bool setBreakEnabled(bool set = true)
bool setDataBits(DataBits dataBits)
bool setDataTerminalReady(bool set)
bool setFlowControl(FlowControl flowControl)
bool setParity(Parity parity)
void setPort(const QSerialPortInfo &serialPortInfo)
void setPortName(const QString &name)
void setReadBufferSize(qint64 size)
bool setRequestToSend(bool set)
bool setStopBits(StopBits stopBits)

先配置完成后，调用open可开启串口 阻塞数据读写：

virtual bool open(OpenMode mode)
virtual bool waitForBytesWritten(int msecs = 30000)
virtual bool waitForReadyRead(int msecs = 30000)

同时可以利用QIODevice类的readyRead信号，connect以后可在收到信息后在槽中响应，利用

qint64 read(char *data, qint64 maxSize)
QByteArray read(qint64 maxSize)
QByteArray readAll()

读取内容。 串口可能在发送一串字符时每一个字符收到均有一次readyRead响应，此时需要自行判断终止符。