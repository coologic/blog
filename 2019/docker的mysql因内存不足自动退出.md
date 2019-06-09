---
title: " docker的mysql因内存不足自动退出\t\t"
categories:
  - 后端
date: 2019-01-31 01:27:38
tags:
---
博客竟然掉线了10天。。。最近比较忙一直没注意，初步怀疑阿里云自动重启了，然后不知道swap没有启动，再次记录一下关于低内存服务器自启需要的内容：

swap内存
------

参考：[docker配置WordPress及MySQL](https://www.techieliang.com/2018/11/1283/) 

free -m 查阅是否有 

建立swapfile
```
mkdir /swap
cd /swap
sudo dd if=/dev/zero of=swapfile bs=1024 count=2000000
```
激活
```
sudo mkswap -f swapfile sudo swapon swapfile
```
很重要，自动启动：/etc/fstab 文件增加下面内容，xxxx是/swap这个路径
```
/XXXX/swapfile /XXXX swap defaults 0 0
```
docker容器自启动
-----------

### 建立容器时设置自启动

`docker run XXXXXXXXXXXXX --restart=always --restart`

具体参数值详细信息：

*   no - 容器退出时，不重启容器；
*   on-failure - 只有在非0状态退出时才从新启动容器；
*   always - 无论退出状态是如何，都重启容器；

### 对已经建立好的容器设置自启动

`docker update --restart=always name`

name是容器名