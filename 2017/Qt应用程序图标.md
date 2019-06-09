title: " Qt应用程序图标\t\t"
tags:
  - icon
  - qt
  - 图标
url: 401.html
id: 401
categories:
  - Qt
date: 2017-11-26 21:26:35
---
### 程序、任务栏等图标

在对应窗口页面通过以下代码可增加图标：
```
setWindowIcon(QIcon("file_name.XXX"));
```
直接提供图片文件路径即可，图片支持所有常用格式，具体说明请见QIcon相关帮助文档。 

QIcon帮助文档：[http://doc.qt.io/qt-5/qicon.html](http://doc.qt.io/qt-5/qicon.html) 

QImageReader帮助文档：[http://doc.qt.io/qt-5/qimagereader.html](http://doc.qt.io/qt-5/qimagereader.html#supportedImageFormats)

### 程序文件图标-windows

程序文件图标格式只支持*.ico文件，尺寸为512\*512 

在\*.pro文件增加? RC\_FILE=icon.rc 

建立icon.rc文件，其内写 ?IDI\_ICON1 ICON "file\_name.ico" 

同时保证file\_name.ico及icon.rc与*.pro文件在相同目录即可 

详情请见帮助文档：[Setting the Application Icon](http://doc.qt.io/qt-5/appicon.html) 

对于Mac、Linux等系统，上述文档也包含相应配置方式