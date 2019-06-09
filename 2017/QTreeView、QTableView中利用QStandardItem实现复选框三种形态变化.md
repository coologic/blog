---
title: " QTreeView/QTableView中利用QStandardItem实现复选框三种形态变化\t\t"
tags:
  - QStandardItem
  - qt
  - QTableView
  - QTreeview
url: 729.html
id: 729
categories:
  - Qt
date: 2017-12-18 18:18:11
---

介绍
--

![](http://wx4.sinaimg.cn/mw690/a8dbb8d6ly1fmn30kdwmug20ay09740p.gif) 复选框有三种形态：全选对勾、全不选空白、半选黑点 在item中通过：setCheckable(true);可开启复选框功能，但默认只支持全选对勾、全不选空白，而且自身的状态变动不会使父/子节点相应，比如子节点全部选中时父节点不会自动勾选 下面你提供一个完整的UsingCheckboxItem类，此类继承自QStandardItem，实现了复选框三种状态的使用。类内容很纯粹并未增加其他设置，仅为复选框实现。

> 注意，此方式让一个item调用了其父节点及子节点的data和setdata两个方法，若不符合设计要求，可仿照此方式在model中重现

下面直接上源码：

源码
--

GitHub**：[QtWidgetsExamples](https://github.com/TechieL/QtWidgetsExamples)**

### using\_checkbox\_item.h

/\*\*
 \* @file using\_checkbox\_item.h
 \* @brief 本文件包含支持复选框item类声明。
 \* @version 1.0.0.0
 \* @date 2017.12.18
 \* @author Techie亮
 */
#ifndef \_H\_USINGCHECKBOXITEM_
#define \_H\_USINGCHECKBOXITEM_
#include <QStandardItem>
#include <QString>
/\*\*
 \* @brief 支持复选框item类
 \*   支持复选框三态转变\-全选对勾、全不选空白、半选黑点
 \*   子类会自动通知父子节点item，若不符合设计需要可仿照此方式在model中的setDate重现
 */
class UsingCheckboxItem : public QStandardItem {
public:
    /\*\*
     \* @brief 构造函数
     \* @param item显示内容
     */
    explicit UsingCheckboxItem(const QString &text);
    /\*\*
     \* @brief setData重写
     \* @param value data值
     \* @param role data类型
     */
    virtual void setData(const QVariant &value, int role = Qt::UserRole + 1);
};
#endif // \_H\_USINGCHECKBOXITEM_

### using\_checkbox\_item.cpp

#include "using\_checkbox\_item.h"
//构造函数
UsingCheckboxItem::UsingCheckboxItem(const QString &text)
    : QStandardItem(text) {
    setCheckable(true);
}
//setData重写
void UsingCheckboxItem::setData(const QVariant &value, int role) {
    if(role == Qt::CheckStateRole) {//针对复选框变动做操作
        Qt::CheckState check_state = (Qt::CheckState)value.toInt();
        QString mtext=text();
        switch (check_state) {
        case Qt::Unchecked: {//取消
            for(int i = 0, num = rowCount(); i < num; i++) {
                child(i)->setData(Qt::Unchecked, Qt::CheckStateRole);
            }
            //修改内容-必须先修改自己再通知父节点
            QStandardItem::setData(value,role);
            //通知父节点，我取消了选择，直接告诉父节点半选即可
            if(parent())parent()->setData(Qt::PartiallyChecked, role);
        }
            return;//此事件已完成直接return
        case Qt::PartiallyChecked: {//半选
            Qt::CheckState current_state = checkState();//当前状态
            int checked_num = 0;//被选择的数量
            int unchecked_num = 0;//未选择的数量
            bool is_partially = false;
            Qt::CheckState child_state;
            int m_rowCount = rowCount();
            //遍历所有子节点
            for(int i = 0; i < m_rowCount; i++) {
                child_state = child(i)->checkState();
                //子节点半选，则直接半选
                switch (child_state) {
                case Qt::PartiallyChecked:is_partially = true;break;
                case Qt::Unchecked:unchecked_num++;break;
                case Qt::Checked:checked_num++;break;
                default:checked_num++;break;
                }
            }
            //根据子节点状态确定当前节点应该设置的状态
            Qt::CheckState now_state;
            if(is_partially)
                now_state = Qt::PartiallyChecked;
            else if(checked\_num == m\_rowCount)
                now_state = Qt::Checked;
            else if(unchecked\_num == m\_rowCount)
                now_state = Qt::Unchecked;
            else
                now_state = Qt::PartiallyChecked;
            //修改状态并通知父节点
            if(current\_state != now\_state) {
                //修改内容-必须先修改自己再通知父节点
                QStandardItem::setData(now_state,role);
                //通知父节点，我的状态更改,也就是父节点进入半选
                if(parent())parent()->setData(Qt::PartiallyChecked, role);
            }
        }
            return;//此事件已完成直接return
        case Qt::Checked: {//全选
            for(int i = 0, num = rowCount(); i < num; i++) {
                child(i)->setData(Qt::Checked, Qt::CheckStateRole);
            }
            //修改内容-必须先修改自己再通知父节点
            QStandardItem::setData(value,role);
            //通知父节点，我被选了,也就是父节点进入半选
            if(parent()) {
                parent()->setData(Qt::PartiallyChecked, role);
            }
        }
            return;//此事件已完成直接return
        default://如果出现此情况就是错了，可以加错误处理
            break;
        }
    }
    QStandardItem::setData(value,role);
}

### 测试函数

#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <qstandarditemmodel.h>
#include "using\_checkbox\_item.h"
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow) {
    ui->setupUi(this);
    QStandardItemModel *m = new QStandardItemModel(this);
    ui->treeView->setModel(m);
    auto i1 = new UsingCheckboxItem("1");
    auto i2 = new UsingCheckboxItem("2");
    m->appendRow(i1);
    m->appendRow(i2);
    auto i11 = new UsingCheckboxItem("1-1");
    auto i12 = new UsingCheckboxItem("1-2");
    i1->appendRow(i11);
    i1->appendRow(i12);
    auto i111 = new UsingCheckboxItem("1-1-1");
    auto i112 = new UsingCheckboxItem("1-1-2");
    i11->appendRow(i111);
    i11->appendRow(i112);
    auto i121 = new UsingCheckboxItem("1-2-1");
    auto i122 = new UsingCheckboxItem("1-2-2");
    i12->appendRow(i121);
    i12->appendRow(i122);

    auto i21 = new UsingCheckboxItem("2-1");
    auto i22 = new UsingCheckboxItem("2-2");
    i2->appendRow(i21);
    i2->appendRow(i22);
    auto i211 = new UsingCheckboxItem("2-1-1");
    auto i212 = new UsingCheckboxItem("2-1-2");
    i21->appendRow(i211);
    i21->appendRow(i212);
    auto i221 = new UsingCheckboxItem("2-2-1");
    auto i222 = new UsingCheckboxItem("2-2-2");
    i22->appendRow(i221);
    i22->appendRow(i222);
}

说明
--

*   重写了setData方法，但仅对CheckStateRole类型data做了操作，其余类型通过最后的QStandardItem::setData(value,role)直接使用默认方式
*   setData中每个case后均直接return，因为会根据value和父子类情况对实际要使用value做修改，最终赋值给QStandardItem::setData不一定是参数value，所以若不返回，必然会调用最后一样导致出错
*   系统默认只有两个状态切换选中和补选中，所以可以借用这个特性，当一个节点状态修改时都通知其父类为PartiallyChecked部分选中状态，由父节点自行判断子节点情况并设置自身状态
*   注意一定要先修改自身状态以后在通知父节点，否则在父节点函数运行过程中自身仍为未修改状态，会导致判断错误
*   在PartiallyChecked的case中判断了一下新旧状态是否改变，若改变会向上一父节点继续传递消息，不改变则立刻停止减少运算量
*   若子节点存在PartiallyChecked状态的，则当前节点一定为PartiallyChecked
*   注意最顶级item是没有parent的所以想父节点传递消息前一定要判断parent是否为nullptr
*   选择一个节点那么此节点一定会在全选-不选两个状态切换，而部分选择仅存在于此节点的子节点发生变动，所以全选-不选两个case直接对所有子节点赋值