---
title: " QSettings配置读写-win注册表操作-ini文件读写\t\t"
url: 674.html
id: 674
categories:
  - Qt
date: 2017-12-12 22:01:38
tags:
---

介绍
--

官方帮助文档：[QSettings](http://doc.qt.io/qt-5/qsettings.html) 一套完整的配置文件读写机制，多平台支持，支持ini文件读写、win下注册表读写等操作。同时支持当前用户配置及当前系统配置两个作用范围。

创建配置文件
------

配置文件涉及到作用域（scope）、文件名(filename)、组织名(organization)、程序名(application)、配置格式(format)等，下面是可用的构造函数：

QSettings(const QString &organization, const QString &application = QString(), QObject *parent = Q_NULLPTR) //1
QSettings(Scope scope, const QString &organization, const QString &application = QString(), QObject *parent = Q_NULLPTR) //2
QSettings(Format format, Scope scope, const QString &organization, const QString &application = QString(), QObject *parent = Q_NULLPTR) //3
QSettings(const QString &fileName, Format format, QObject *parent = Q_NULLPTR) //3
QSettings(QObject *parent = Q_NULLPTR)

构造方式1在win下自动在注册表读写，2也一样 3如果设置为ini类型会在"C:/Users/XXXXX/AppData/Roaming/"用户范围建立ini文件，"C:/ProgramData/”系统范围 4可以指定文件名和类型

### 配置格式

Constant

Value

Description

QSettings::NativeFormat

0

Store the settings using the most appropriate storage format for the platform. On Windows, this means the system registry; on macOS and iOS, this means the CFPreferences API; on Unix, this means textual configuration files in INI format.

QSettings::Registry32Format

2

Windows only: Explicitly access the 32-bit system registry from a 64-bit application running on 64-bit Windows. On 32-bit Windows or from a 32-bit application on 64-bit Windows, this works the same as specifying NativeFormat. This enum value was added in Qt 5.7.

QSettings::Registry64Format

3

Windows only: Explicitly access the 64-bit system registry from a 32-bit application running on 64-bit Windows. On 32-bit Windows or from a 64-bit application on 64-bit Windows, this works the same as specifying NativeFormat. This enum value was added in Qt 5.7.

QSettings::IniFormat

1

Store the settings in INI files.

QSettings::InvalidFormat

16

Special value returned by registerFormat().

### 作用域

QSettings::UserScope

0

Store settings in a location specific to the current user (e.g., in the user's home directory).

QSettings::SystemScope

1

Store settings in a global location, so that all users on the same machine access the same set of settings.

> 如果设置作用域为用户，则先检查用户，如果没有再检查系统范围。如果设置为系统则不会检查用户。

### 关于组织、程序名

程序具有全局唯一的组织及程序名，可以直接使用。如果需要单独建立则不需要

QSettings settings("Moose Soft", "Facturo-Pro");//自定义
//配置全局名称并使用
QCoreApplication::setOrganizationName("Moose Soft");
QCoreApplication::setApplicationName("Facturo-Pro");
QSettings settings;//此时可以无参数构造

配置文件读写
------

读写配置：setValue、value 为了数据的分类明确还提供了配置分组功能，需要使用**[beginGroup](http://doc.qt.io/qt-5/qsettings.html#beginGroup)**、**[endGroup](http://doc.qt.io/qt-5/qsettings.html#endGroup) 注意begin开始后面代码表示在组内操作，若想访问组外内容必须先end**

> 读写时可以不用group操作，通过以下方式也可表示组的概念：

settings.setValue("mainwindow/size", win->size());
settings.setValue("mainwindow/fullScreen", win->isFullScreen());
settings.setValue("outputpanel/visible", panel->isVisible());
//等效于：
settings.beginGroup("mainwindow");
settings.setValue("size", win->size());
settings.setValue("fullScreen", win->isFullScreen());
settings.endGroup();
settings.beginGroup("outputpanel");
settings.setValue("visible", panel->isVisible());
settings.endGroup();

同时支持数组**[beginReadArray](http://doc.qt.io/qt-5/qsettings.html#beginReadArray)、[beginWriteArray](http://doc.qt.io/qt-5/qsettings.html#beginWriteArray)、[endArray](http://doc.qt.io/qt-5/qsettings.html#endArray) 注意begin开始后面代码表示在组内操作，若想访问组外内容必须先end** 除此以外还有**[remove](http://doc.qt.io/qt-5/qsettings.html#remove)**删除配置内容，注意此删除是完全删除此配置项，不是把当前配置内容制空 在读数据之前可以使用**[contains](http://doc.qt.io/qt-5/qsettings.html#contains)**判断是否有当前key的配置项 还有具有范围伤害的两个方法：**[clear](http://doc.qt.io/qt-5/qsettings.html#clear)**删除所有配置项（注册表需要符合组合和程序名，ini就是清空）、**[allKeys](http://doc.qt.io/qt-5/qsettings.html#allKeys)**读取当前group及下属所有配置项key的名称

范例
--

上面的可能看不懂，下面遍历一遍win下的配置格式和作用域

### win下SystemScope、IniFormat

#include <QCoreApplication>
#include <QDebug>
#include <QSettings>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc,argv);
    QSettings config(QSettings::IniFormat, QSettings::SystemScope,"TechieLiang", "testQSettings");
    qDebug()<< config.fileName();
    //写入配置文件
    config.beginGroup("config");
    config.setValue("user_name", "test");
    config.setValue("key", 123);
    config.endGroup();
    config.beginGroup("config");
    qDebug()<<config.value("user_name").toString()
           <<config.value("key").toInt();
    config.beginGroup("config");
    return 0;
}
//"C:/ProgramData/TechieLiang/testQSettings.ini"
//"test" 123

### win下UserScope、IniFormat

#include <QCoreApplication>
#include <QDebug>
#include <QSettings>
int main(int argc, char *argv\[\]) {
    QCoreApplication a(argc,argv);
    QSettings config(QSettings::IniFormat, QSettings::UserScope,"TechieLiang", "testQSettings");
    qDebug()<< config.fileName();
    return 0;
}
//"C:/Users/XXXX/AppData/Roaming/TechieLiang/testQSettings.ini"
//XXXX用户名

### win下不设置IniFormat、UserScope

QSettings config(QSettings::UserScope,"TechieLiang", "testQSettings");
qDebug()<< config.fileName();

//"\\\HKEY\_CURRENT\_USER\\\Software\\\TechieLiang\\\testQSettings"

### win下不设置IniFormat、SystemScope

QSettings config(QSettings::SystemScope,"TechieLiang", "testQSettings");
//"\\\HKEY\_LOCAL\_MACHINE\\\Software\\\TechieLiang\\\testQSettings"

### win下InvalidFormat、SystemScope

QSettings config(QSettings::InvalidFormat,QSettings::SystemScope,"TechieLiang", "testQSettings");
//"C:/ProgramData/TechieLiang/testQSettings.ini"

### win下InvalidFormat、UserScope

QSettings config(QSettings::InvalidFormat,QSettings::UserScope,"TechieLiang", "testQSettings");
//"C:/Users/XXXX/AppData/Roaming/TechieLiang/testQSettings.ini"
//XXXX用户名

AllKeys
-------

QSettings settings;
settings.setValue("fridge/color", QColor(Qt::white));
settings.setValue("fridge/size", QSize(32, 96));
settings.setValue("sofa", true);
settings.setValue("tv", false);
QStringList keys = settings.allKeys();
// keys: \["fridge/color", "fridge/size", "sofa", "tv"\]
settings.beginGroup("fridge");
keys = settings.allKeys();
// keys: \["color", "size"\]

高级
--

### 自定义读写配置方法

**[registerFormat](http://doc.qt.io/qt-5/qsettings.html#registerFormat)**(const QString &_extension_, ReadFunc _readFunc_, WriteFunc _writeFunc_, Qt::CaseSensitivity _caseSensitivity_ = Qt::CaseSensitive) 此方法可以注册自定义格式

bool readXmlFile(QIODevice &device, QSettings::SettingsMap &map);
bool writeXmlFile(QIODevice &device, const QSettings::SettingsMap &map);

int main(int argc, char *argv\[\])
{
    const QSettings::Format XmlFormat =
            QSettings::registerFormat("xml", readXmlFile, writeXmlFile);

    QSettings settings(XmlFormat, QSettings::UserScope, "MySoft",
                       "Star Runner");

    ...
}

### Win特例

windows下可能一个key同时具有value和子项目，此时值需要通过Default或“.”来访问

> On Windows, it is possible for a key to have both a value and subkeys. Its default value is accessed by using "Default" or "." in place of a subkey:

settings.setValue("HKEY\_CURRENT\_USER\\\MySoft\\\Star Runner\\\Galaxy", "Milkyway");
settings.setValue("HKEY\_CURRENT\_USER\\\MySoft\\\Star Runner\\\Galaxy\\\Sun", "OurStar");
settings.value("HKEY\_CURRENT\_USER\\\MySoft\\\Star Runner\\\Galaxy\\\Default"); // returns "Milkyway"

### setPath函数-不同模式、范围的默认路径

如果开始设置的范围、配置格式、文件路径不对，也可以通过此函数修改，主要是按默认路径

### void QSettings::setPath(Format _format_, Scope _scope_, const QString &_path_)

对应的默认路径如下：

Platform

Format

Scope

Path

Windows

[IniFormat](http://doc.qt.io/qt-5/qsettings.html#Format-enum)

[UserScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`FOLDERID_RoamingAppData`

[SystemScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`FOLDERID_ProgramData`

Unix

[NativeFormat](http://doc.qt.io/qt-5/qsettings.html#Format-enum), [IniFormat](http://doc.qt.io/qt-5/qsettings.html#Format-enum)

[UserScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`$HOME/.config`

[SystemScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`/etc/xdg`

Qt for Embedded Linux

[NativeFormat](http://doc.qt.io/qt-5/qsettings.html#Format-enum), [IniFormat](http://doc.qt.io/qt-5/qsettings.html#Format-enum)

[UserScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`$HOME/Settings`

[SystemScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`/etc/xdg`

macOS and iOS

[IniFormat](http://doc.qt.io/qt-5/qsettings.html#Format-enum)

[UserScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`$HOME/.config`

[SystemScope](http://doc.qt.io/qt-5/qsettings.html#Scope-enum)

`/etc/xdg`