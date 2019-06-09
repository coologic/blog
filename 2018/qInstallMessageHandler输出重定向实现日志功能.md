---
title: " qInstallMessageHandler输出重定向实现日志功能\t\t"
tags:
  - qt
url: 880.html
id: 880
categories:
  - Qt
date: 2018-01-16 19:54:01
---

介绍
--

Qt提供了一系列消息模板：qInfo、qDebug、qWarning、qCritical、qFatal，[官方文档](http://doc.qt.io/qt-5/qtglobal.html) qDebug可能比较常用，他们分别表示消息、调试信息、一般警告、严重错误、致命错误，默认情况会输出至调试窗口。而在release模式下可以将其输出重定向至文件，从而实现日志功能。

qInstallMessageHandler
----------------------

使用此方法可以将上述五种消息重定向至指定函数，范例如下：

/\*\*
 \* @brief CustomOutputMessage
 \* @param type 消息类型
 \* @param context 消息信息
 \* @param msg 消息内容
 */
void CustomOutputMessage(QtMsgType type,
                         const QMessageLogContext &context,
                         const QString &msg) {
    QString message_type;
    switch(type) {
    case QtInfoMsg:
        message_type = QString("Info:");
        break;
    case QtDebugMsg:
        message_type = QString("Debug:");
        break;
    case QtWarningMsg:
        message_type = QString("Warning:");
        break;
    case QtCriticalMsg:
        message_type = QString("Critical:");
        break;
    case QtFatalMsg:
        message_type = QString("Fatal:");
    }
    QString current_date = QDateTime::currentDateTime().
                           toString("yyyy-MM-dd hh:mm:ss");
    QFile file("log.txt");
    file.open(QIODevice::WriteOnly | QIODevice::Append);
    QString message = QString("%1 %2 (%3)\\r\\n").arg(message_type).
                      arg(msg).arg(current_date);
    file.write(message.toLatin1());
    file.close();
    if(type == QtFatalMsg) {
        QMessageBox::critical(nullptr, "致命错误",
                              QString("%2\\r\\n%1\\r\\n请解决问题后重新启动程序").
                              arg(msg).arg(current_date));
    }
}

int main(int argc, char *argv\[\]) {
    QApplication a(argc, argv);
    //日志功能
#ifndef QT_DEBUG
    qInstallMessageHandler(CustomOutputMessage);
#endif
    qDebug("123");
//    qFatal("123");
    MainWindow w;
    w.show();
    return a.exec();
}

其中使用qInstallMessageHandler方法前判断了QT_DEBUG宏，即在调试模式直接输出，在release模式输出至文件 函数模板：

typedef void (*QtMessageHandler)(QtMsgType, const QMessageLogContext &, const QString &);

方法的第一个参数是枚举类型，除上述类型外还有一个QtSystemMsg=QtCriticalMsg

其他
--

### 取消重定向

通过qInstallMessageHandler(nullptr);即可取消重定向。此处不建议写0，毕竟此处应当传递的是一个指针。

### qFatal

Qt比较人性化，当发生qFatal时(致命错误)，会在完成qInstallMessageHandler的操作后就直接调用std::abort让程序被系统kill，并由系统弹出错误框。系统弹出提示框的情况在用户使用时并不美观。 分析原因： qFatal宏会调用如下方法：

void QMessageLogger::fatal(const char *msg, ...) const Q\_DECL\_NOTHROW
{
    QString message;
    va_list ap;
    va_start(ap, msg); // use variable arg list
    QT\_TERMINATE\_ON\_EXCEPTION(message = qt\_message(QtFatalMsg, context, msg, ap));//1
    va_end(ap);
    qt\_message\_fatal(QtFatalMsg, context, message);//2
}

1处会调用qInstallMessageHandler指向的函数句柄 2处代码如下：

static void qt\_message\_fatal(QtMsgType, const QMessageLogContext &context, const QString &message)
{
#if defined(Q\_CC\_MSVC) && defined(QT\_DEBUG) && defined(\_DEBUG) && defined(\_CRT\_ERROR)
    wchar_t contextFileL\[256\];
    convert\_to\_wchar\_t\_elided(contextFileL, sizeof contextFileL / sizeof *contextFileL,
                              context.file);
    int reportMode = \_CrtSetReportMode(\_CRT\_ERROR, \_CRTDBG\_MODE\_WNDW);
    \_CrtSetReportMode(\_CRT_ERROR, reportMode);
    int ret = \_CrtDbgReportW(\_CRT\_ERROR, contextFileL, context.line, \_CRT\_WIDE(QT\_VERSION_STR),
                             reinterpret\_cast<const wchar\_t *>(message.utf16()));
    if ((ret == 0) && (reportMode & \_CRTDBG\_MODE_WNDW))
        return; // ignore
    else if (ret == 1)
        _CrtDbgBreak();
#else
    Q_UNUSED(context);
    Q_UNUSED(message);
#endif
    std::abort();
}

最后调用了abort。。。。真的是致命错误，所以直接通过abort令系统杀死当前程序，这算他杀性致命？ 对比qDebug：

void QMessageLogger::debug(const char *msg, ...) const
{
    va_list ap;
    va_start(ap, msg); // use variable arg list
    const QString message = qt_message(QtDebugMsg, context, msg, ap);
    va_end(ap);
    if (isFatal(QtDebugMsg))//5
        qt\_message\_fatal(QtDebugMsg, context, message);
}

会在5行提前判断一下是不是fatal，此处当然不是，所以不会调用后面的qt\_message\_fatal。 为什么明知不是还判断？isFatal里面对非Fatal做了一些操作，具体的没有深入阅读 解决方案： 上述代码修改：

if(type == QtFatalMsg) {
    QMessageBox::critical(nullptr, "致命错误",
             QString("%2\\r\\n%1\\r\\n请解决问题后重新启动程序").
             arg(msg).arg(current_date));
    exit(0);
}

此方法等于主动退出，算是自杀了。但是无法很好地处理所有的线程、内存等各种问题。如果没有exit，紧接着即将调用的abort也无法优雅的解决这些问题，线程问题、数据存储等等该如何处理就要看系统了。 当然，既然已经发生了致命错误，并且成功在log日志文件中记录了此错误，那就让他崩溃吧。由此也说明程序中除非无法挽回的问题用qFatal，其他的最好最高只用到qCritical.