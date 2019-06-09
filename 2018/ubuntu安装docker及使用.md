---
title: " ubuntu安装docker及使用\t\t"
tags:
  - docker
url: 1195.html
id: 1195
categories:
  - 后端
date: 2018-05-18 19:30:59
---

介绍
--

阿里云-ubuntu16服务器安装docker方法及使用

连接服务器
-----

首先可以基于b-s的管理控制台直接远程连接服务器，但是有指令行数的显示限制。可以用putty基于用户名密码登录：[方法](https://help.aliyun.com/document_detail/25434.html?spm=a2c4g.11186623.2.5.LbV4l9#windows)。除此以外，阿里云建议使用ssh密钥对的方式登录，使用此方式会自动禁用用户名密码登录方法：[方法](https://help.aliyun.com/document_detail/51798.html?spm=a2c4g.11186623.6.617.M8E6OB)

docker安装
--------

CentOS的安装请见[官方最佳实践文档](https://help.aliyun.com/document_detail/51853.html?spm=a2c4g.11186623.6.763.9EscFr)。上面的文档有docker基本使用介绍，对于ubuntu只是安装和启动过程有所区别，安装方法：

#!/bin/bash
\# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
\# step 2: 安装GPG证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
\# Step 3: 写入软件源信息
sudo add-apt-repository "deb \[arch=amd64\] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
\# Step 4: 更新并安装Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce
\# 安装指定版本的Docker-CE:
\# Step 1: 查找Docker-CE的版本:
\# apt-cache madison docker-ce
\#   docker-ce | 17.03.1~ce-0~ubuntu-xenial | http://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
\#   docker-ce | 17.03.0~ce-0~ubuntu-xenial | http://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
\# Step 2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.1~ce-0~ubuntu-xenial)
\# sudo apt-get -y install docker-ce=\[VERSION\]

启动docker

\# 启动
service docker start
\# 停止
service docker stop
\# 重启
service docker restart

查看安装情况和帮助

\# 查看信息
docker info
\# 查看帮助
docker -h

docker开机自动启动
------------

docker run --restart=always XXXXX
docker update --restart=always xxx

第一行为开机自启动。第二行为若没有增加restart字段可以通过update增加。 --restart有如下选项： no -? 容器退出时，不重启容器； on-failure - 只有在非0状态退出时才从新启动容器，数字值为尝试重启次数； always - 无论退出状态是如何，都重启容器，若启动失败一直尝试重启；

修改docker源
---------

国内直接下载docker镜像速度很慢，可以修改源，最简单的事每次下载都增加--registry-mirror修饰。 可用源如下：

Docker 官方中国区
https://registry.docker-cn.com
网易
http://hub-mirror.c.163.com
ustc
https://docker.mirrors.ustc.edu.cn

单次有效方法：

docker run hello-world --registry-mirror=https://docker.mirrors.ustc.edu.cn

永久有效修改：

修改 /etc/default/docker，加入 DOCKER_OPTS=”镜像地址”，可以有多个：
DOCKER_OPTS="--registry-mirror=https://docker.mirrors.ustc.edu.cn"

支持 systemctl 的系统，通过 sudo systemctl edit docker.service，会生成 etc/systemd/system/docker.service.d/override.conf 覆盖默认的参数，在该文件中加入如下内容：
\[Service\]
ExecStart=
ExecStart=/usr/bin/docker -d -H fd:// --registry-mirror=https://docker.mirrors.ustc.edu.cn

新版的 Docker 推荐使用 json 配置文件的方式，默认为 /etc/docker/daemon.json，非默认路径需要修改 dockerd 的 –config-file，在该文件中加入如下内容：
{
"registry-mirrors": \["https://docker.mirrors.ustc.edu.cn"\]
}