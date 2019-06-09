---
title: " 树莓派/linux挂载优盘\t\t"
tags:
  - linux
  - 树莓派
url: 709.html
id: 709
categories:
  - 嵌入式
  - 系统
date: 2017-12-15 21:35:00
---

之前写的为了方便把树莓派的U盘分成了三个分区 一个用来存储文件的fat32分区，保证win和linux均可使用 树莓派不会自动加载优盘多余的分区，需要手动加载 首先在mnt文件夹下建立一个文件夹

sudo mkdir /mnt/upan

获取存储设备分区信息

sudo fdisk -l

在这里最下方可以找到自己想要挂载的分区 如果按照我之前的分区而且没有加载其他设备，应该是/dev/sda3 然后挂载，要获取读取权限需要挂到pi用户下，如果改了用户那就用自己的用户名

sudo mount -o uid=pi,gid=pi /dev/sdax /mnt/upan

> sdax的x换成自己的号

卸载：

sudo umount /mnt/upan

> 我用虚拟机装的ubuntu，之前一直没事还是自动装在优盘，今天突然不管用了，而且fdisk也查不到，具体问题还没找到