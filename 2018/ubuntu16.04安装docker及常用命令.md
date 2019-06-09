---
title: " ubuntu16.04安装docker及常用命令\t\t"
tags:
  - docker
url: 1279.html
id: 1279
categories:
  - 后端
date: 2018-11-09 19:10:15
---

非阿里云直接装
-------

注意：建议试用加速器，否则安装很慢很慢

sudo apt-get update

添加使用 HTTPS 传输的软件包以及 CA 证书

sudo apt-get install apt-transport-https ca-certificates

添加GPG密钥

sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

添加软件源

echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" | sudo tee /etc/apt/sources.list.d/docker.list

添加成功后更新软件包缓存

sudo apt-get update

安装docker

sudo apt-get install docker-engine

启动docker

sudo systemctl enable docker
sudo systemctl start docker

阿里云安装及容器镜像服务
------------

阿里云有自己的容器镜像服务，[帮助文档](https://help.aliyun.com/product/60716.html) 直接用这个服务的安装介绍安装即可：[https://help.aliyun.com/document_detail/60742.html](https://help.aliyun.com/document_detail/60742.html) 注意先安装curl:sudo apt-get install curl然后才能进行教程中的curl指令 阿里云给的gpg证书可能不对，可以使用[官网](https://docs.docker.com/install/linux/docker-ce/ubuntu/#set-up-the-repository)给的：

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

容器镜像服务地址：https://cr.console.aliyun.com/cn-shanghai/repositories 登录方式：账号为阿里云账号，密码在第一次开启镜像服务时设置。注意当前是免费使用

docker login registry.cn-hangzhou.aliyuncs.com

测试
--

docker info

常用命令
----

直接输入docker可以看到所有可用指令。 中文版可以见：[http://www.runoob.com/docker/docker-command-manual.html](http://www.runoob.com/docker/docker-command-manual.html)

阿里云镜像加速
-------

直接下载镜像速度太慢，可以用阿里云的镜像加速： [https://help.aliyun.com/document_detail/60750.html](https://help.aliyun.com/document_detail/60750.html)