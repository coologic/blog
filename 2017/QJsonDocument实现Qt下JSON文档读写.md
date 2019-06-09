---
title: " QJsonDocument实现Qt下JSON文档读写\t\t"
tags:
  - json
  - qt
url: 718.html
id: 718
categories:
  - Qt
date: 2017-12-17 20:43:24
---

介绍
--

Qt提供了一系列类以供进行Json 文档的读写，分别为： [QJsonDocument](http://doc.qt.io/qt-5/qjsondocument.html)Json文档、[QJsonArray](http://doc.qt.io/qt-5/qjsonarray.html)数组、[QJsonObject](http://doc.qt.io/qt-5/qjsonobject.html)对象、[QJsonValue](http://doc.qt.io/qt-5/qjsonvalue.html)值、[QJsonParseError](http://doc.qt.io/qt-5/qjsonparseerror.html)错误。

### 错误分类

Constant

Value

Description

`QJsonParseError::NoError`

`0`

No error occurred

`QJsonParseError::UnterminatedObject`

`1`

An object is not correctly terminated with a closing curly bracket

`QJsonParseError::MissingNameSeparator`

`2`

A comma separating different items is missing

`QJsonParseError::UnterminatedArray`

`3`

The array is not correctly terminated with a closing square bracket

`QJsonParseError::MissingValueSeparator`

`4`

A colon separating keys from values inside objects is missing

`QJsonParseError::IllegalValue`

`5`

The value is illegal

`QJsonParseError::TerminationByNumber`

`6`

The input stream ended while parsing a number

`QJsonParseError::IllegalNumber`

`7`

The number is not well formed

`QJsonParseError::IllegalEscapeSequence`

`8`

An illegal escape sequence occurred in the input

`QJsonParseError::IllegalUTF8String`

`9`

An illegal UTF8 sequence occurred in the input

`QJsonParseError::UnterminatedString`

`10`

A string wasn't terminated with a quote

`QJsonParseError::MissingObject`

`11`

An object was expected but couldn't be found

`QJsonParseError::DeepNesting`

`12`

The JSON document is too deeply nested for the parser to parse it

`QJsonParseError::DocumentTooLarge`

`13`

The JSON document is too large for the parser to parse it

`QJsonParseError::GarbageAtEnd`

`14`

The parsed document contains additional garbage characters at the end

### Json内容读写

QJsonDocument::toJson可以生成json文档，具有可选参数，可以生成紧凑结构和缩进结构：

Constant

Value

Description

`QJsonDocument::Indented`

`0`

Defines human readable output as follows:

{
    "Array": \[
        true,
        999,
        "string"
    \],
    "Key": "Value",
    "null": null
}

`QJsonDocument::Compact`

`1`

Defines a compact output as follows:

{"Array":\[true,999,"string"\],"Key":"Value","null":null}

除此以外还可以用**[toBinaryData](http://doc.qt.io/qt-5/qjsondocument.html#toBinaryData)**、**[toVariant](http://doc.qt.io/qt-5/qjsondocument.html#toVariant)**用于结果输出 QJsonDocument除了使用构造函数创建以外，还支持静态函数创建，主要用于读取已有文件的内容：

QJsonDocument fromBinaryData(const QByteArray &data, DataValidation validation = Validate)
QJsonDocument fromJson(const QByteArray &json, QJsonParseError *error = Q_NULLPTR)
QJsonDocument fromRawData(const char *data, int size, DataValidation validation = Validate)
QJsonDocument fromVariant(const QVariant &variant)

> QJsonDocument并不会直接操作文件，需要自行利用QFile进行readAll或者Write

fromRawData/fromBinaryData并不会返回QJsonParseError错误而是直接返回DataValidation枚举类型，表明读取的数据是否有效

Constant

Value

Description

`QJsonDocument::Validate`

`0`

Validate the data before using it. This is the default.

`QJsonDocument::BypassValidation`

`1`

Bypasses data validation. Only use if you received the data from a trusted place and know it's valid, as using of invalid data can crash the application.

### 数据类型

QJsonValue用于存储所有值，可以用type判断其类型，含以下类型

Constant

Value

Description

`QJsonValue::Null`

`0x0`

A Null value

`QJsonValue::Bool`

`0x1`

A boolean value. Use [toBool](http://doc.qt.io/qt-5/qjsonvalue.html#toBool)() to convert to a bool.

`QJsonValue::Double`

`0x2`

A double. Use [toDouble](http://doc.qt.io/qt-5/qjsonvalue.html#toDouble)() to convert to a double.

`QJsonValue::String`

`0x3`

A string. Use [toString](http://doc.qt.io/qt-5/qjsonvalue.html#toString)() to convert to a [QString](http://doc.qt.io/qt-5/qstring.html).

`QJsonValue::Array`

`0x4`

An array. Use [toArray](http://doc.qt.io/qt-5/qjsonvalue.html#toArray-1)() to convert to a [QJsonArray](http://doc.qt.io/qt-5/qjsonarray.html).

`QJsonValue::Object`

`0x5`

An object. Use [toObject](http://doc.qt.io/qt-5/qjsonvalue.html#toObject-1)() to convert to a [QJsonObject](http://doc.qt.io/qt-5/qjsonobject.html).

`QJsonValue::Undefined`

`0x80`

The value is undefined. This is usually returned as an error condition, when trying to read an out of bounds value in an array or a non existent key in an object.

也可以通过isXXXX用于判断，并通过toXXXX转换为对应类型

读写操作
----

源码请见GitHub：**[QtCoreExamples](https://github.com/TechieL/QtCoreExamples)**

### json范例

{
  "Array": \[
      true,
      999,
      "string"
  \],
  "Key": "Value",
  "null": null
}

创建
--

#include <QCoreApplication>
#include <QJsonDocument>//json文档
#include <QJsonArray>//json数组
#include <QJsonObject>//json对象
#include <QJsonValue>//json值
#include <QJsonParseError>//错误处理
#include <QDebug>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QJsonDocument json;
    QJsonArray array;
    array.append(true);
    array.append(999);
    array.append("string");
    QJsonObject object;
    object.insert("Array",array);
    object.insert("Key","Value");
    //null用空的QJsonValue即可
    object.insert("null",QJsonValue());
    //最外层是大括号所以是object
    json.setObject(object);
    qDebug()<<json.toJson(QJsonDocument::Compact);
    return 0;
}

此时使用QJsonDocument::Compact方式写出，其结果为： "{\\"Array\\":\[true,999,\\"string\\"\],\\"Key\\":\\"Value\\",\\"null\\":null}"

> QDebug会将\\n直接输出成\\n而不会换行

解析
--

#include <QCoreApplication>
#include <QJsonDocument>//json文档
#include <QJsonArray>//json数组
#include <QJsonObject>//json对象
#include <QJsonValue>//json值
#include <QJsonParseError>//错误处理
#include <QDebug>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QJsonDocument json;
    QJsonArray array;
    array.append(true);
    array.append(999);
    array.append("string");
    QJsonObject object;
    object.insert("Array",array);
    object.insert("Key","Value");
    //null用空的QJsonValue即可
    object.insert("null",QJsonValue());
    //最外层是大括号所以是object
    json.setObject(object);
    auto json_text = json.toJson(QJsonDocument::Compact);
    auto json_binary = json.toBinaryData();
    QJsonDocument read1 = QJsonDocument::
            fromJson(json_text);
    QJsonDocument read2 = QJsonDocument::
            fromBinaryData(json_binary);
    //验证两个是一样的
    if(QString(read1.toBinaryData()) ==
            QString(read2.toBinaryData()))
        qDebug()<<"same";
    //用于判断是否为空，对QJsonXXX对象均支持
    if(!read1.isEmpty())
        qDebug()<<"empty";
    //下面开始解析代码
    if(read1.isObject() ) {
        auto read_object = read1.object();
        if(!read_object.isEmpty()) {
            qDebug()<<read_object.value("Key").toString();
            qDebug()<<read_object.value("null").isNull();
            QJsonValue value = read_object.value("Array");
            qDebug()<<value.type()<<value;
            if(value.isArray()) {
                auto read_array = value.toArray();
                for(auto one\_of\_array : read_array)
                    qDebug()<<one\_of\_array;
                //此处建议判断好具体类型，因为array里面也可能有object
            }
        }
    }
    return 0;
}

结果

same
empty
"Value"
true
4 QJsonValue(array, QJsonArray(\[true,999,"string"\]))
QJsonValue(bool, true)
QJsonValue(double, 999)
QJsonValue(string, "string")

根据正常的结构进行判断即可，对于array需要进行遍历，支持C++的for(:)方式遍历

> fromJson、fromBinaryData、fromRawData、fromVariant这几个静态函数都不会直接返回成功与否，而是在参数中实现解析结果判断，正式使用时务必进行判断，避免后续代码均出错

其他
--

1.  对于每一步建议明确判断QJsonValut的type如果type错误，会输出为""，比如int类型用toString不会自动转换，而是直接写出""
2.  相比于Qt使用core模块的xml读写，json操作很简单，不需要逐行的读取操作使用readNext，获取内容的顺序与文本顺序可以不一致，xml使用请见文章：[QXmlStreamReader/QXmlStreamWriter实现Qt下xml文件读写](http://techieliang.com/2017/12/714/)
3.  上面介绍的例子最外层为object，也支持最外层为array，使用setArray即可，最外层只能为一种，不能不断的add
4.  QJsonDocument主要负责的是数据文本格式或者说是表现方式的转换，其余类负责内容
5.  QJsonArray可以用size获取数组的大小，后续操作感觉类似于QList<QVariant> ，也具有迭代器
6.  [QJsonParseError](http://doc.qt.io/qt-5/qjsonparseerror.html)使用方式自行查看，主要是在解析时出现，但均不会作为返回值直接返回，在QJsonDocument::fromJson处使用，其余几个静态fromXXX函数直接返回枚举类型，此类操作均不可保证绝对正确，故应当做判断避免后续连锁错误。