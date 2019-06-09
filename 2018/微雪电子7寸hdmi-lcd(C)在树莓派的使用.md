---
title: " 微雪电子7寸hdmi-lcd(C)在树莓派的使用\t\t"
tags:
  - 树莓派
url: 1182.html
id: 1182
categories:
  - 嵌入式
date: 2018-05-03 09:51:08
---

用于树莓派
-----

1.  两根线都插到树莓派上
2.  打开lcd背部的backlight开关
3.  config.txt文件中写入：

max\_usb\_current=1
hdmi_group=2
hdmi_mode=87
hdmi_cvt 1024 600 60 6 0 0 0
hdmi_drive=1

标准hdmi接口使用(win10等)
------------------

1.  打开lcd背部的backlight开关
2.  把两根线接到笔记本或其他设备上（hdmi线用于显示，usb线用于触摸）

> usb线除了触摸作用还有供电，所以无法单独舍弃触摸功能。说明书说插上以后会导致只能使用此屏幕作为触摸屏，实际在win10测试可以同时使用鼠标