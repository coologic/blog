---
title: " Qt图像显示及裁剪\t\t"
tags:
  - qt
  - 图像
url: 867.html
id: 867
categories:
  - Qt
date: 2018-01-03 21:10:12
---

介绍
--

最近做了一个长图分割的小工具([GitHub](https://github.com/TechieL/Tools/tree/master/LongPictureSegmentation))，记录一下用到的两个类[QImage](http://doc.qt.io/qt-5/qimage.html)、[QPixmap](http://doc.qt.io/qt-5/qpixmap.html)还有控件QLabel以及ScrollArea。

> Qt provides four classes for handling image data: [QImage](http://doc.qt.io/qt-5/qimage.html), [QPixmap](http://doc.qt.io/qt-5/qpixmap.html), [QBitmap](http://doc.qt.io/qt-5/qbitmap.html) and [QPicture](http://doc.qt.io/qt-5/qpicture.html). [QImage](http://doc.qt.io/qt-5/qimage.html) is designed and optimized for I/O, and for direct pixel access and manipulation, while [QPixmap](http://doc.qt.io/qt-5/qpixmap.html) is designed and optimized for showing images on screen. [QBitmap](http://doc.qt.io/qt-5/qbitmap.html) is only a convenience class that inherits [QPixmap](http://doc.qt.io/qt-5/qpixmap.html), ensuring a depth of 1. Finally, the [QPicture](http://doc.qt.io/qt-5/qpicture.html) class is a paint device that records and replays [QPainter](http://doc.qt.io/qt-5/qpainter.html) commands. 大概是说image可以进行io交互，其提供了直接通过文件路径进行读写图片的方法，也可以对图像做修改，像素级的。pixmap用于在屏幕显示。bitmap是pixmap子类，显示单色深度图。picture是个类似于画板的东西，可以记录和重放painter的操作，painter就是个画笔了。

Qt图像显示
------

这里只说基本的显示，通过QLabel可以显示图片，其提供了setPixmap方法可。 首先用QFileDialog::getOpenFileName获取图像文件路径，传递给QImage，通过load方法读入图像。 然后QLabel::setPixmap(QPixmap::fromImage(image))实现吧image转换成pixmap并在label显示。 此时有个问题：label可能显示不全图片，那就需要控件ScrollArea来协作。 直接在designer中拖入ScrollArea控件，然后将label拖到ScrollArea中，并且给ScrollArea增加任意一种layout即可，此时显示图像如果大于了label原始尺寸则会出现滚动条可以通过拖拽滚动条实现整个图片的显示。

> layout有默认的边框尺寸，可以修改layoutleftmargin、layouttopmargin、layoutrightmargin、layoutbottommargin等改为0，这样label和ScrollArea的尺寸就完全相同了

其他
--

### 用户点击图片位置确定

void PictureLabel::mousePressEvent(QMouseEvent *ev) {
    if(ev->button() == Qt::RightButton) {
        emit SegmentationPostion(ev->pos());
    }
    QLabel::mousePressEvent(ev);
}

重写label的mousePressEvent方法，判断按键类型即可，上面判断的是右键则发出当前鼠标位置的信号

### 图片分割

在QImage中利用copy方法可以直接分割图片，方法需要传入左上角坐标和长宽，传入后返回原图像指定长方形区域的图片，不会修改原图。