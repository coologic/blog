---
title: " 树莓派无显示器、无网线，优盘(U盘)启动，远程桌面\t\t"
tags:
  - 树莓派
url: 698.html
id: 698
categories:
  - 嵌入式
date: 2017-12-14 22:21:38
---

系统下载写入sd卡/u盘
------------

https://www.raspberrypi.org/ 官网下载镜像解压。 再下载个win32diskimager，安装 插入sd卡或优盘，打开win32diskimager找到镜像文件（记着解压4GB多）然后写入到设备 有显示器的此时不用看后面的了? 直接插到树莓派通电即可 登陆账号pi密码raspberry

无显示器远程访问树莓派-SSH
---------------

### 开启SSH

默认开机不启动，需要在root盘符，就是windows下能直接打开的fat32文件系统的盘符，在里面新建一个"ssh"的文件即可，不要后缀，新建个记事本吧.txt删了，如果系统默认隐藏缀名。。。那先让他显示出来

### 无显示器配置网络-有网线

找到etc目录下的dhcpcd.conf文件，在最后增加

\# 接口 eth0
interface eth0
\# 静态IP，后面是掩码不是端口
static ip_address=192.168.1.2/24
\# 网关IP地址
static routers=192.168.1.1
\# 义DNS服务器
static domain\_name\_servers=114.114.114.114

然后保存放到pi里面通电，利用putty连接ip即可，putty可以下中文版 比如putty-x64-0.70cn 上面修改文件这个需要有linux系统，如果没有在win下需要装软件，使系统能够读取ext文件系统，方法见后

### 网线也没有

那就wifi吧。。。这需要提前配置wifi 修改/etc/wpa\_supplicant/wpa\_supplicant.conf文件，文件最后增加

network={
    ssid="xxxx"
    psk="xxxxxxx"
}

网上有说修改`/boot/wpa_supplicant.conf`文件的，也是可以的，这个我没试验，介绍见：[无屏幕和键盘配置树莓派WiFi和SSH](http://shumeipai.nxez.com/2017/09/13/raspberry-pi-network-configuration-before-boot.html) 然后电脑开启wifi热点，或者路由器。查看到路由器地址或者共享软件记录的地址就是树莓派的地址了。大概等了半分钟多才连进来，还是一分钟？反正挺久。然后putty登陆即可。

远程桌面连树莓派
--------

首先先实现ssh访问，因为需要安装一个xrdp软件

### 安装xrdp

ssh连入输入

sudo apt-get install tightvncserver
sudo apt-get install xrdp

树莓派默认是官方的源，如果自动下载成功了，那就OK了，否则需要先换源，然后再下载。比如提示了404 Not Found 一类的，此时换个源即可，具体见最后其他介绍。 win下运行输入mstsc，打开远程桌面连接(也可以在附件中找到)，输入树莓派ip即可连接，

U盘启动
----

还需要做几个操作： root盘符下的cmdline.txt文件 里面的PARTUUID=970b8044-02 改成/dev/sda2 系统盘符下的/etc/fstab改成：

proc            /proc           proc    defaults          0       0
/dev/sda1  /boot           vfat    defaults          0       2
/dev/sda2  /               ext4    defaults,noatime  0       1

直接可以启动，如果还是没反应只能先借助一下sd卡 sd卡启动系统，ssh连接以后分别输入：

echo program\_usb\_boot_mode=1 | sudo tee -a /boot/config.txt
sudo reboot
#此时会中断连接树莓派重启，等重启完了重现连接，注意观察路由器登陆ip或者共享软件
vcgencmd otp_dump | grep 17:
#看结果是 17:3020000a 就对了，继续下面的删除添加的那一行
sudo nano /boot/config.txt
#一直按下找到最后一行 退格键删除
Ctrl+o
回车一次确认覆盖文件
Ctrl+X退出
sudo shutdown -h now
#关机

不闪了就是关机了，然后拔下sd卡用U盘就行了，优盘开机比较慢可能是先找sd卡没有再找优盘

其他
--

### windows下访问ext文件系统

ExtFS，软件需要注册，否则免费十天，但是注册是免费的。其他软件也有就不推荐了

### 换源方法

linux不太熟，直接上教程：下面来源于[http://www.shumeipaiba.com/wanpai/jiaocheng/16.html](http://www.shumeipaiba.com/wanpai/jiaocheng/16.html) 操作流程如下：ssh登陆以后

sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo nano /etc/apt/sources.list
#此时进入nano编辑界面了，下面用的阿里的镜像
deb http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib
Ctrl+O
回车
Ctrl+X
sudo apt-get update
sudo apt-get upgrade

### xrdp连接提示5910 error connecting错误

ssh下输入 先装tightvncserver? 再装xrdp

sudo apt-get remove xrdp tightvncserver
sudo apt-get install tightvncserver
sudo apt-get install xrdp 
#然后重启xrdp 
sudo /etc/init.d/xrdp restart

保证按顺序安装应该在远程的时候可以成功并看到xrdp的输入界面 如果再有问题，比如输入pi的账号密码登陆以后只有灰点或者纯灰色显示 直接重新写系统即可

### xrdp连接成功，登陆成功，屏幕灰色或灰点

此时再remove两个事没用的，remove以后再install也不会改变灰点显示 直接重新写系统 安装顺序保证先tightvncserver后xrdp即可