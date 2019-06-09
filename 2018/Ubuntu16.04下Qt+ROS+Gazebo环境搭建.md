---
title: " Ubuntu16.04下Qt+ROS+Gazebo环境搭建\t\t"
tags:
  - gazebo
  - ros
url: 1271.html
id: 1271
categories:
  - ROS
date: 2018-09-23 20:17:44
---

虚拟机系统
-----

使用VMware Player12。 创建虚拟机-稍后安装系统-下一步-Linux-Ubuntu-下一步 设置名字和路径-下一步-设置磁盘大小-将虚拟机拆分为多个文件-下一步 创建完成-编辑虚拟机设置-如果优盘安装新建一个USB注意版本2.0/3.0-如果ISO安装对CDROM设置好ISO文件路径 启动虚拟机，此时进入Ubuntu安装界面。选择中文-安装Ubuntu

> 不是用虚拟机直接安装，通过U盘引导，如果提示gfxboot.c32:not a COM32R image boot，此时直接按Tab，然后提示多个可选命令：live live-install check……输入live-install回车即可直接进入安装ubuntu界面，此时要求选择语言，后续一样

选择“为图形或无线硬件……”继续-默认选择“清除整个硬盘并安装Ubuntu”若需要可选择其它选项进行分区-地区-键盘布局-用户名密码-装完后重启

### 必要配置-U盘自动挂载

sudo apt-get install usbmount
sudo gedit /etc/usbmount/usbmount.conf

文件内容改为：

FILESYSTEMS=“ext2, ext3, vfat, ntfs” MOUNTOPTIONS=“iocharset=gb2312,sync,noexec,nodev,noatime”
FS_MOUNTOPATIONS=“-fstype=vfat,gid=floppy,dmask=0007,fmask=0117”
FS_MOUNTOPATIONS=“-fstype=ntfs,gid=floppy,dmask=0007,fmask=0117”

ROS Kinetic Kame安装
------------------

ubuntu16.04对应的版本使kinetic，[http://wiki.ros.org/kinetic](http://wiki.ros.org/kinetic) 添加代码列表

sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

设置公钥

sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116

更新软件包索引

sudo apt-get update

安装

sudo apt-get install ros-kinetic-desktop-full

初始化rosdep

sudo rosdep initros
dep update

安装rosinstall

sudo apt-get install python-rosinstall

环境配置

echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc

验证ros是否安装成功

roscore

Gazebo配置
--------

kinetic默认gazebo版本是7.0，当前201809最新是9.0，为保证稳定此处不更新。 默认的gazebo不带models： https://bitbucket.org/osrf/gazebo_models/ 下载这个压缩包。建议用下载工具下载 解压到，压缩包里面的所有子文件夹需要放到~/.gazebo/models下 使用下面的命令可以看到加载的相应的模型：

roslaunch gazebo\_ros empty\_world.launch
roslaunch gazebo\_ros willowgarage\_world.launch
roslaunch gazebo\_ros mud\_world.launch
roslaunch gazebo\_ros shapes\_world.launch
roslaunch gazebo\_ros rubble\_world.launch

Qt安装
----

### 安装

可以直接下载run文件安装，注意修改run权限，然后双击安装即可。注意run文件要和ubuntu32/64版本对应 为了快速测试直接安装默认版本5.5.1

sudo apt-get install build-essential
sudo apt-get install qt5-default qtcreator

这时候就有creator也有了后面配置cmake