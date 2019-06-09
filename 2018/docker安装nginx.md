---
title: " docker安装nginx\t\t"
tags:
  - docker
  - nginx
url: 1009.html
id: 1009
categories:
  - 其他
date: 2018-02-26 15:34:24
---

介绍
--

_Nginx_ (engine x) 是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP服务器。

安装
--

在docker hub中可以直接照到官方的镜像，[介绍文档](https://hub.docker.com/r/library/nginx/) pull可以拉取：docker pull nginx

### 简易的启动 docker run --name some-nginx -p 81:80 -v /some/content:/usr/share/nginx/html:ro -d nginx 81为本机端口，80为nginx端口号，content为本机html存放目录

### 配置文件目录挂载

docker run --name my-custom-nginx-container -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx 具体配置文件撰写方式见[官方指南](https://nginx.org/en/docs/beginners_guide.html)

### 所有目录挂载

首先创建目录，分别为www文件夹、logs日志、conf配置文件夹

mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf