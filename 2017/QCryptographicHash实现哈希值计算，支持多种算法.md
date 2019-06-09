---
title: " QCryptographicHash实现哈希值计算，支持多种算法\t\t"
url: 668.html
id: 668
categories:
  - Qt
date: 2017-12-12 14:20:36
tags:
---

介绍
--

多看看Qt core模块会发现很多惊喜呀，里面包含的类很多涉及到很多方面的功能实现 先附上所有core类：[Qt Core](http://doc.qt.io/qt-5/qtcore-module.html)，再直接给出QCryptographicHash的帮助：[QCryptographicHash](http://doc.qt.io/qt-5/qcryptographichash.html) 此类用于提供密码散列，哈希值。可以生成二进制或文本形式的hash值，并支持多种算法，算法可以由QCryptographicHash::Algorithm选择

### 支持的算法

Constant

Value

Description

`QCryptographicHash::Md4`

`0`

Generate an MD4 hash sum

`QCryptographicHash::Md5`

`1`

Generate an MD5 hash sum

`QCryptographicHash::Sha1`

`2`

Generate an SHA-1 hash sum

`QCryptographicHash::Sha224`

`3`

Generate an SHA-224 hash sum (SHA-2). Introduced in Qt 5.0

`QCryptographicHash::Sha256`

`4`

Generate an SHA-256 hash sum (SHA-2). Introduced in Qt 5.0

`QCryptographicHash::Sha384`

`5`

Generate an SHA-384 hash sum (SHA-2). Introduced in Qt 5.0

`QCryptographicHash::Sha512`

`6`

Generate an SHA-512 hash sum (SHA-2). Introduced in Qt 5.0

`QCryptographicHash::Sha3_224`

`RealSha3_224`

Generate an SHA3-224 hash sum. Introduced in Qt 5.1

`QCryptographicHash::Sha3_256`

`RealSha3_256`

Generate an SHA3-256 hash sum. Introduced in Qt 5.1

`QCryptographicHash::Sha3_384`

`RealSha3_384`

Generate an SHA3-384 hash sum. Introduced in Qt 5.1

`QCryptographicHash::Sha3_512`

`RealSha3_512`

Generate an SHA3-512 hash sum. Introduced in Qt 5.1

`QCryptographicHash::Keccak_224`

`7`

Generate a Keccak-224 hash sum. Introduced in Qt 5.9.2

`QCryptographicHash::Keccak_256`

`8`

Generate a Keccak-256 hash sum. Introduced in Qt 5.9.2

`QCryptographicHash::Keccak_384`

`9`

Generate a Keccak-384 hash sum. Introduced in Qt 5.9.2

`QCryptographicHash::Keccak_512`

`10`

Generate a Keccak-512 hash sum. Introduced in Qt 5.9.2

### 提供的接口

QCryptographicHash(Algorithm method)
~QCryptographicHash()
void addData(const char *data, int length)
void addData(const QByteArray &data)
bool addData(QIODevice *device)
void reset()
QByteArray result() const

static QByteArray hash(const QByteArray &data, Algorithm method)

可以实例化此类，构造时需要提供算法类型，然后通过addData需要计算hash的数据，最后通过result获取结果，可以利用reset清空数据但不能修改算法。 还给了一个方便易用的静态方法，直接提供算法类型及数据内容即可。

范例
--

#include <QCoreApplication>
#include <QDebug>
#include <QCryptographicHash>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc,argv);
    QString text("test");
    qDebug()<<QCryptographicHash::hash(text.toLatin1(), QCryptographicHash::Md5); //16进制结果
    qDebug()<<QCryptographicHash::hash(text.toLatin1(), QCryptographicHash::Md5).toHex(); //转换为字符串
    qDebug()<<QCryptographicHash::hash(text.toLatin1(), QCryptographicHash::Keccak_512); //16进制结果
    qDebug()<<QCryptographicHash::hash(text.toLatin1(), QCryptographicHash::Keccak_512).toHex(); //转换为字符串
    return 0;
}

结果

"\\t\\x8Fk\\xCD""F!\\xD3s\\xCA\\xDEN\\x83&'\\xB4\\xF6"
"098f6bcd4621d373cade4e832627b4f6"
"\\x1E.\\x9F\\xC2\\x00+\\x00-u\\x19\\x8Bu\\x03!\\f\\x05\\xA1\\xBA\\xAC""E`\\x91j<m\\x93\\xBC\\xCE:P\\xD7\\xF0\\x0F\\xD3\\x95\\xBF\\x16G\\xB9\\xAB\\xB8\\xD1\\xAF\\xCC\\x9Cv\\xC2\\x89\\xB0\\xC9""8;\\xA3\\x86\\xA9V\\xDAK8\\x93""D\\x17x\\x9E"
"1e2e9fc2002b002d75198b7503210c05a1baac4560916a3c6d93bcce3a50d7f00fd395bf1647b9abb8d1afcc9c76c289b0c9383ba386a956da4b38934417789e"

其中test计算md5的结果是098f6bcd4621d373cade4e832627b4f6 可以在相关网站反查结果：[http://www.cmd5.com/](http://www.cmd5.com/)，可以证明计算正确。