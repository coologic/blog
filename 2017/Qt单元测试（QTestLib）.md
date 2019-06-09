title: " Qt单元测试（QTestLib）\t\t"
tags:
  - qt
  - QTestLib
  - 单元测试
url: 483.html
id: 483
categories:
  - Qt
date: 2017-12-01 13:53:29
---
创建
--

QTestLib框架提供了一个简单易用的单元测试框架，需要在工程文件中添加Qt+=testlib,或在新建项目是选择“其他项目-qt单元测试”，详细帮助请看[qt4.8官方文档](http://doc.qt.io/qt-4.8/qtestlib-manual.html)，[Qt5官方文档](http://doc.qt.io/qt-5/qttest-index.html)

基本操作
----
```
class Untitled2Test : public QObject {
    Q_OBJECT
public:
    Untitled2Test();
private Q_SLOTS:
    void initTestCase();
    void cleanupTestCase();
    void testCase1();
};
Untitled2Test::Untitled2Test() {
}
void Untitled2Test::initTestCase() {
}
void Untitled2Test::cleanupTestCase() {
}
void Untitled2Test::testCase1() {
    QVERIFY2(true, "Failure");
}
QTEST_APPLESS_MAIN(Untitled2Test)
#include "tst_untitled2test.moc"
```
注意： 

1. 每一个测试类（Case）中的每一项测试条目必须在Qt槽中，否则无法调用“private Q_SLOTS:”“private slots:”均可 
2. 最后两行必须有，单元测试不具有Main函数，使用此函数实现自动添加并调用所有槽

> `1、initTestCase()` will be called before the first testfunction is executed. initTestCase()会在第一个测试函数执行前调用。 
> 
> `2、cleanupTestCase()` will be called after the last testfunction was executed. cleanupTestCase()会在最后一个测试函数执行后调用。 
> 
> `3、init()` will be called before each testfunction is executed. init()会在每一个测试函数执行前调用。 
> 
> `4、cleanup()` will be called after every testfunction. cleanup()会在每一个测试函数执行后调用。

高级操作
----

### 命令行操作-可以输出调试结果到文本

打开cmd，通过cd命令将目录指向到项目的debug目录下的debug，在此文件内应该可以直接看到测试项目的可执行exe文件，此时出入tst\_qttesttest -xml -vs -lightxml -o testres.txt指令即可导出测试结果保存到texstres.txt文件中。 

其中tst\_qttesttest为测试项目对应的可执行文件名。 

还可以使用以下命令行参数：

*   *   -help 输出可能的命令行参数，并提供一些有益的帮助。
    *   的功能 输出在测试中所有测试功能。
    *   -O?_文件名_ ??写输出到指定的文件，而不是标准输出
    *   无声的 沉默输出，只显示警告，失败和最小的状态消息
    *   -V1 详细输出，输出的进入和退出测试功能的信息。
    *   -V2 扩展的详细输出;还输出每个[QCOMPARE](http://doc.qt.nokia.com/4.6/qtest.html#QCOMPARE)（）和[QVERIFY](http://doc.qt.nokia.com/4.6/qtest.html#QVERIFY)（）
    *   -VS 输出每个被发出的信号
    *   -XML 输出XML格式的结果，而不是纯文本
    *   lightxml 输出结果作为XML标签流
    *   -eventdelay?_毫秒_ 延迟，如果没有模拟键盘或鼠标（指定[QTest :: keyClick](http://doc.qt.nokia.com/4.6/qtest.html#keyClick)（），[QTest ::mouseClick](http://doc.qt.nokia.com/4.6/qtest.html#mouseClick)（）等），从这个参数的值（以毫秒为单位）取代。
    *   keydelay?_MS_ 类似eventdelay，但只影响模拟键盘而不是鼠标模拟。
    *   mousedelay?_MS_ 类似eventdelay，但只影响鼠标模拟，而不是键盘模拟。
    *   -KeyEvent的详细 输出更详细的输出模拟键盘
    *   \- maxwarnings _numberBR_设置输出最大数量的警告。无限，默认为0到2000。

### GUI测试

Qtestlib单元测试提供GUI操作函数，可对控件发送消息后检测执行结果，比如QTest::keyClick()，QTest::mouseClick()等等，详情请见[帮助文档](http://doc.qt.io/qt-5/qtest.html) 

下面提供鼠标操作相关的枚举类型：

Constant|Value|Description
---|---|---
`QTest::MousePress`|`0`|A mouse button is pressed.
`QTest::MouseRelease`|`1`|A mouse button is released.
`QTest::MouseClick`|`2`|A mouse button is clicked (pressed and released).
`QTest::MouseDClick`|`3`|A mouse button is double clicked (pressed and released twice).
`QTest::MouseMove`|`4`|The mouse pointer has moved.

下面为鼠标相关的操作函数：
```
void mouseClick(QWidget *widget, Qt::MouseButton button, Qt::KeyboardModifiers modifier = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mouseClick(QWindow *window, Qt::MouseButton button, Qt::KeyboardModifiers stateKey = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mouseDClick(QWidget *widget, Qt::MouseButton button, Qt::KeyboardModifiers modifier = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mouseDClick(QWindow *window, Qt::MouseButton button, Qt::KeyboardModifiers stateKey = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mouseMove(QWidget *widget, QPoint pos = QPoint(), int delay = -1)
void mouseMove(QWindow *window, QPoint pos = QPoint(), int delay = -1)
void mousePress(QWidget *widget, Qt::MouseButton button, Qt::KeyboardModifiers modifier = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mousePress(QWindow *window, Qt::MouseButton button, Qt::KeyboardModifiers stateKey = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mouseRelease(QWidget *widget, Qt::MouseButton button, Qt::KeyboardModifiers modifier = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
void mouseRelease(QWindow *window, Qt::MouseButton button, Qt::KeyboardModifiers stateKey = Qt::KeyboardModifiers(), QPoint pos = QPoint(), int delay = -1)
```
### 结果可视化-AutoTest插件

![](http://wx3.sinaimg.cn/mw690/a8dbb8d6ly1fm183rt13mj20r106paaa.jpg) 

默认测试结果显示在控制台（应用程序输出标签）以纯文本形式显示，不够直观，可使用AutoTest插件实现可视化效果 

通过帮助-关于插件-AutoTest开启即可，启动此插件需要重启Qt Creator，然后再下方会多出TestResults的标签，可直接在此标签点击上方的运行按钮运行所有测试，同时在“工具-Test”也可运行所有测试 

此插件可以在运行单元测试后以红、绿色表示明确标记处运行结果，并且以Case为单位显示，可以展开看到具体每一个测试用例的结果。

### 可以用到的测试宏命令
```
QBENCHMARK
QBENCHMARK_ONCE
QCOMPARE(actual, expected)
QEXPECT_FAIL(dataIndex, comment, mode)
QFAIL(message)
QFETCH(type, name)
QFINDTESTDATA(filename)
QSKIP(description)
QTEST(actual, testElement)
QTEST_APPLESS_MAIN(TestClass)
QTEST_GUILESS_MAIN(TestClass)
QTEST_MAIN(TestClass)
QTRY_COMPARE(actual, expected)
QTRY_COMPARE_WITH_TIMEOUT(actual, expected, timeout)
QTRY_VERIFY2(condition, message)
QTRY_VERIFY(condition)
QTRY_VERIFY2_WITH_TIMEOUT(condition, message, timeout)
QTRY_VERIFY_WITH_TIMEOUT(condition, timeout)
QVERIFY2(condition, message)
QVERIFY(condition)
QVERIFY_EXCEPTION_THROWN(expression, exceptiontype)
QWARN(message)
```
### 单元测试注意事项

1.  单元测试类中建议不要出现私有成员，尤其是指针，同时不建议在测试函数中建立被测类的指针，而是直接建立被测类的对象，在测试结束后容易遗忘指针。若需要指针`initTestCase`中new，在`cleanupTestCase`中delete。
2.  若某个测试函数中出现了new，一定记着delete，且务必让delete在第一个断言前出现，因为断言失败函数就回立刻结束，并把当前函数标记为测试失败。若delete在第一个断言之后，而第一个断言失败则不会执行之后的delete。
3.  若测试类必须有私有成员，必须注意一个测试类中的所有函数公用私有成员，不会在每个测试之前刷新状态。

### 被测类为单例时

若被测类为单例，欲对其内所有函数做单元测试，会出现测试第一个函数可以保证测试环境为初始状态，后续测试回因为单例的原因，导致测试时建立在之前操作后的环境下。欲解决此问题，需要删除单例。 

单例类如下：
```
class SingleModel {
public:
    static inline SingleModel* get_instance() {
        if (m_instance == nullptr) {
               m_instance = new SingleModel();
        }
        return m_instance;
    };
private:
    SingleModel();
    class SingletonDel {
    public:
        ~SingletonDel() {
            if (m_instance != nullptr) {
                delete m_instance;
                m_instance = NULL;
            }
        }
    };
    static SingleModel* m_instance;
    static SingletonDel m_singleton_del;
};
```
  此时使用get\_instance可以获得单例的指针，使用delete可以删除单例的空间，但是由于单例实例的指针是静态私有成员的，所以应该将其设置为NULL，否则下次调用get\_instance判断非NULL会直接返回之前的已经被删除的空间指针而不进行new操作，导致后续操作错误。 
  
  由于m_instance是私有成员，在单元测试时无法进行修改，故做以下设置： 
  
  .h文件不变：
```
#include <QtTest>
class ProjectModelTest: public QObject {
    Q_OBJECT
private Q_SLOTS:
        void TestXXX();
};
```
.cpp文件修改：
```
#define private public
#include "project_model.h"
void ProjectModelTest::TestXXX() {}
```
注意一定要在include前define，通过将private定义为public，从而实现将源程序的私有成员编程public。同时此处的define在cpp里面，只对此cpp有效，不会影响其余的单元测试。