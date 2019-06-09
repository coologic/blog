title: " Qt-窗口Close()后自动释放空间\t\t"
tags:
  - qt
url: 94.html
id: 94
categories:
  - Qt
date: 2017-11-10 19:33:55
---
在进行一个四窗口项目，当第四个窗口显示结果后，若重新开始则close()结果页面后new第一个窗口 

发现不断的重新开始会导致内存占用越来越多 

Qt窗口在Close()指令后调用CloseEven()，最后判断是否关闭 

若关闭，则Hide()窗口，并不是真正的释放内存。若不关闭则不作任何操作 

此时给窗口增加如下设置：
```
setAttribute(Qt::WA_DeleteOnClose);
```
可实现窗口在Close()后自动释放内存