---
title: " Qt状态栏(QStatusBar)使用\t\t"
tags:
  - qt
url: 896.html
id: 896
categories:
  - Qt
date: 2018-01-20 16:02:44
---

介绍
--

在QMainWindow最下方有状态栏QStatusBar，相关API：[帮助](http://doc.qt.io/qt-5/qstatusbar.html) Qt主要将状态栏的信息分为五大类：

1.  右下角的窗口尺寸调整符号，右下角的小黑三角。提供两个方法**isSizeGripEnabled**()、**setSizeGripEnabled**(_bool_)设置其是否显示。
2.  每个单元之间的小竖线，分割不同的控件，仔细看了看感觉也不是状态栏提供的分割控件更像是插入到其中的控件的边框线。。。这也算他一大类吧，隐藏方法：statusBar()->setStyleSheet("QStatusBar::item{border: 0px}");，将状态栏的所有item边框宽度设置为0
3.  永久信息显示，永久信息在状态栏最右侧。通过**addPermanentWidget**、**insertPermanentWidget**，这种信息会一直显示，一般是不改变的，比如版权、作者等，特殊功能的按钮等等，本质还是widget。Permanently means that the widget may not be obscured by temporary messages. It is is located at the far right of the status bar.
4.  非永久信息显示、一般信息显示，在最左侧，通过**addWidget**、**insertWidget**进行插入。同理说是信息实际是widget，可以是按钮等。
5.  临时信息，临时信息封装的很简单，不需要自己newwidget了，只需要直接传入信息内容和信息显示时间即可。**currentMessage**、**showMessage**、**clearMessage**，此类信息不需要自行创建widget，想要移除消息可以等**showMessage**的时间到了自动消失或者主动地使用clear。

使用
--

### 移除widget

其实上面已经整理了几乎所有的方法，剩余的方法比如**[removeWidget](http://doc.qt.io/qt-5/qstatusbar.html#removeWidget)**可以在插入了widget后再取出来，注意相关提示，remove本质是隐藏，并把widget的parent从状态栏转为null，若想要重新显示在状态栏需要重新add和show，注意还要show，因为被hide了。

> **Note:** This function does not delete the widget but _hides_ it. To add the widget again, you must call both the addWidget() and show() functions.

关于非永久信息的widget和永久信息PermanentWidget，实际上都是widget，可以做显示内容的修改，本质区别是，如果窗口小了，那PermanentWidget不会被showmessage的信息盖住

### 布局说明

PermanentWidget在窗口最右侧开始排列、普通Widget在最左侧开始排列，他们会限制窗口的最小宽度为两者widget所有item的最小宽度值，也就是说无论左右排列均会永久显示在窗口中，不会因为窗口缩小而被遮挡。 showMessage会在最左侧直接显示，等于覆盖了普通的Widget，若窗口已经达到最小，则无论showMessage内容有多长，只会显示左侧Widget可用的长度的内容，范例如下：   ![](https://github.com/TechieL/MyBlogPictureBackup/raw/master/%E5%9B%BE%E7%89%87/%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/Qt%E7%8A%B6%E6%80%81%E6%A0%8F(QStatusBar)%E4%BD%BF%E7%94%A8/gif.gif) Palmshop by TechieLiang为PermanentWidget，左侧绿色的为普通widege，乱大的字母为showmessage。范例代码如下：

//状态栏设置
//不显示其内控件的边框
statusBar()->setStyleSheet("QStatusBar::item{border: 0px}");
//永久部件
statusBar()->addPermanentWidget(new QLabel(kProgramName +
                                    " by " + kProgramDeveloperName));
//临时部件
statusbar\_state\_label_  = new QLabel(this);
statusBar()->addWidget(statusbar\_state\_label_);
statusBar()->showMessage("kajsdlfkjalksdjfklsdjafjadsjflkads");
SetState("数据库登陆失败", false);

void MainWindow::SetState(const QString &text, bool is_good) {
    QPalette pa;
    if(is_good)
        pa.setColor(QPalette::WindowText, Qt::green);
    else
        pa.setColor(QPalette::WindowText, Qt::red);
    statusbar\_state\_label_->setPalette(pa);
    statusbar\_state\_label_->setText(text);
}

### 获取状态栏指针

获取状态栏指针，可以通过ui->XXXX获取，也可以直接在mainwindow中使用statusBar()获取指针，因为状态栏只会有一个。

### showMessage

第二个参数是毫秒显示时间，可以通过设置以后，Qt会显示指定时间后自动删除当前消息内容。若不设置，将会一直显示知道clear，或者当下一次调用show。

> If _timeout_ is 0 (default), the _message_ remains displayed until the clearMessage() slot is called or until the showMessage() slot is called again to change the message.

注意，每次调用show，会清理以前的message，所以不要抱有永久显示某条消息的目的使用showmessage("XXXX",0);