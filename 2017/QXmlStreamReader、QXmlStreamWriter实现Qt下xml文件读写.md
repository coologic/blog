---
title: " QXmlStreamReader/QXmlStreamWriter实现Qt下xml文件读写\t\t"
tags:
  - qt
  - xml
url: 714.html
id: 714
categories:
  - Qt
date: 2017-12-17 14:47:06
---

介绍
--

帮助文档：[QXmlStreamReader](http://doc.qt.io/qt-5/qxmlstreamreader.html)、[QXmlStreamWriter](http://doc.qt.io/qt-5/qxmlstreamwriter.html) 除此以外读取时还需要使用[QXmlStreamAttributes](http://doc.qt.io/qt-5/qxmlstreamattributes.html)

### QXml-Token标记类型

Constant

Value

Description

`QXmlStreamReader::NoToken`

`0`

The reader has not yet read anything.

`QXmlStreamReader::Invalid`

`1`

An error has occurred, reported in [error](http://doc.qt.io/qt-5/qxmlstreamreader.html#error)() and [errorString](http://doc.qt.io/qt-5/qxmlstreamreader.html#errorString)().

`QXmlStreamReader::StartDocument`

`2`

The reader reports the XML version number in [documentVersion](http://doc.qt.io/qt-5/qxmlstreamreader.html#documentVersion)(), and the encoding as specified in the XML document in [documentEncoding](http://doc.qt.io/qt-5/qxmlstreamreader.html#documentEncoding)(). If the document is declared standalone, [isStandaloneDocument](http://doc.qt.io/qt-5/qxmlstreamreader.html#isStandaloneDocument)() returns `true`; otherwise it returns `false`.

`QXmlStreamReader::EndDocument`

`3`

The reader reports the end of the document.

`QXmlStreamReader::StartElement`

`4`

The reader reports the start of an element with [namespaceUri](http://doc.qt.io/qt-5/qxmlstreamreader.html#namespaceUri)() and [name](http://doc.qt.io/qt-5/qxmlstreamreader.html#name)(). Empty elements are also reported as StartElement, followed directly by EndElement. The convenience function [readElementText](http://doc.qt.io/qt-5/qxmlstreamreader.html#readElementText)() can be called to concatenate all content until the corresponding EndElement. Attributes are reported in [attributes](http://doc.qt.io/qt-5/qxmlstreamreader.html#attributes)(), namespace declarations in [namespaceDeclarations](http://doc.qt.io/qt-5/qxmlstreamreader.html#namespaceDeclarations)().

`QXmlStreamReader::EndElement`

`5`

The reader reports the end of an element with [namespaceUri](http://doc.qt.io/qt-5/qxmlstreamreader.html#namespaceUri)() and [name](http://doc.qt.io/qt-5/qxmlstreamreader.html#name)().

`QXmlStreamReader::Characters`

`6`

The reader reports characters in [text](http://doc.qt.io/qt-5/qxmlstreamreader.html#text)(). If the characters are all white-space, [isWhitespace](http://doc.qt.io/qt-5/qxmlstreamreader.html#isWhitespace)() returns `true`. If the characters stem from a CDATA section, [isCDATA](http://doc.qt.io/qt-5/qxmlstreamreader.html#isCDATA)() returns `true`.

`QXmlStreamReader::Comment`

`7`

The reader reports a comment in [text](http://doc.qt.io/qt-5/qxmlstreamreader.html#text)().

`QXmlStreamReader::DTD`

`8`

The reader reports a DTD in [text](http://doc.qt.io/qt-5/qxmlstreamreader.html#text)(), notation declarations in [notationDeclarations](http://doc.qt.io/qt-5/qxmlstreamreader.html#notationDeclarations)(), and entity declarations in [entityDeclarations](http://doc.qt.io/qt-5/qxmlstreamreader.html#entityDeclarations)(). Details of the DTD declaration are reported in in [dtdName](http://doc.qt.io/qt-5/qxmlstreamreader.html#dtdName)(), [dtdPublicId](http://doc.qt.io/qt-5/qxmlstreamreader.html#dtdPublicId)(), and [dtdSystemId](http://doc.qt.io/qt-5/qxmlstreamreader.html#dtdSystemId)().

`QXmlStreamReader::EntityReference`

`9`

The reader reports an entity reference that could not be resolved. The name of the reference is reported in [name](http://doc.qt.io/qt-5/qxmlstreamreader.html#name)(), the replacement text in [text](http://doc.qt.io/qt-5/qxmlstreamreader.html#text)().

`QXmlStreamReader::ProcessingInstruction`

`10`

The reader reports a processing instruction in [processingInstructionTarget](http://doc.qt.io/qt-5/qxmlstreamreader.html#processingInstructionTarget)() and [processingInstructionData](http://doc.qt.io/qt-5/qxmlstreamreader.html#processingInstructionData)().

主要是用`StartDocument`、`EndDocument`文档开始结束，`StartElement`、`EndElement`元素开始结束、`Characters`特征

### 范例xml文件

源码请见GitHub：**[QtCoreExamples](https://github.com/TechieL/QtCoreExamples)**

<?xml version="1.0" encoding="UTF-8"?>
<bookmark href="http://qt-project.org/">
    <title>Qt Project</title>
</bookmark>

第一行为`StartDocument` bookmark、title为`StartElement`，/bookmark、/title为`EndElement` href为attributes的一项，可以有多项，通过[QXmlStreamAttributes::](http://doc.qt.io/qt-5/qxmlstreamattributes.html)value获取后面的地址内容 Qt Project是title这个element的charcters

写xml
----

#include <QCoreApplication>
#include <QFile>
#include <QXmlStreamWriter>
int main2(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QFile file("test.xml");
    if(file.open(QIODevice::WriteOnly | QIODevice::Text)) {
        QXmlStreamWriter stream(&file);
        stream.setAutoFormatting(true);
        stream.writeStartDocument();
        stream.writeStartElement("bookmark");
        stream.writeAttribute("href", "http://qt-project.org/");
        stream.writeTextElement("title", "Qt Project");
        stream.writeEndElement();
        stream.writeEndDocument();
        file.close();
    }
    return 0;
}

写并不复杂，按顺序写即可，注意区分Attribute和Element，如果是一个小节用StartElement，如果是单行的类似于<title>Qt Project</title>可以直接用writeTextElement。

> QXmlStreamWriter只是格式操作，并不提供文件操作，需要利用QFile建立文件并传递指针，也可以提供QString的指针，这样最终的xml信息会赋值到QString中。

读xml
----

#include <QCoreApplication>
#include <QFile>
#include <QXmlStreamReader>
#include <QDebug>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc, argv);
    QFile file("test.xml");
    if(file.open(QIODevice::ReadOnly | QIODevice::Text)) {
        QXmlStreamReader xml(&file);
        while (!xml.atEnd() && !xml.hasError()) {//循环逐行读取
            QXmlStreamReader::TokenType token = xml.readNext();
            if(token == QXmlStreamReader::StartDocument)//文件开始跳过
                continue;
            if(token == QXmlStreamReader::StartElement) {//StartElement类型，主要针对bookmark和title
                if(xml.name() == "bookmark") {//bookmark读取，其下attributes有但只有一个
                    qDebug()<<"StartElement-"<<xml.name();
                    QXmlStreamAttributes attributes = xml.attributes();
                    if(attributes.hasAttribute("href")) {//针对href的attribute做判断
                        qDebug()<<"Attributes-"<<attributes.value("href");//返回值是QStringRef
                    }
                    //多个attributes在这里增加更多的if
                    continue;
                }
                if(xml.name() == "title") {//title
                    xml.readNext();//没有attributes，理应直接进入while，此处偷懒了
                    if(xml.isCharacters()) //可以做Characters判断也可以直接用text
                        qDebug()<<"title-Characters-"<<xml.text();
                }
            }
        }
        if (xml.hasError()) {
            //范例，判断枚举类型，写出错误字符串
            if(xml.error() == QXmlStreamReader::CustomError) {
                //可以直接写出错误字符串
                qDebug()<<"error:" <<xml.errorString();
            }
        }
    }
    return 0;
}

*   上述代码都没判断EndElement，不建议这样
*   建议进入每一个StartElement都直接开启一个while循环，直到循环到EndElement，从何保证对一个开头到结尾的完整操作，而不是像上述代码吧title放在和bookmark同级的循环内，并没有标明title包含于bookmark的关系，这样容易导致错误。
*   注意xml中每一个<>括住的项都作为一个标记，都占用一次readNext，其开头均为name，其后可能有attribute及对应value。
*   每两个<>XXX<>之间的XXX均作为一个Characters标记，也占用一次readNext，也就是上文中仍有未打印出的Character("\\n??? "换行符和四个空格、纯换行符)：因为QXml以"\\r"作为换行识别那么在前后两行的<><>之间还会余留一个\\n；在title前的缩进也会当做一项Character。因此建议保证完整的包含关系并做好isXXX或者token枚举类型的判断以免被干扰。

其他
--

除上述xml操作以外，Qt还提供了Qt XML模块，用于更高级的xml操作，提供了高速及使用便利的操作函数(不可兼得呀) 高速的SAX方法读取，[QXmlSimpleReader及相关类](http://doc.qt.io/qt-5/qxmlsimplereader.html) 使用便捷的DOM方式：[QDomDocument及相关类](http://doc.qt.io/qt-5/qdomdocument.html) 使用这两个方法，由于是用的非core模块，需要在pro中添加qt += xml