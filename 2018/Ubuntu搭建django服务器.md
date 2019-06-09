---
title: " Ubuntu搭建django服务器\t\t"
tags:
  - django
url: 1228.html
id: 1228
categories:
  - 后端
date: 2018-07-10 22:04:53
---

介绍
--

使用Ubuntu版本的阿里云ECS搭建django服务器

连接
--

我这用的putty连的

更新源
---

注意不用改源地址，默认就是用阿里的源 `sudo apt-get update`

安装ftp服务器
--------

`安装：apt-get install vsftpd` 查看安装情况：`vsftpd -version` 新建ftp服务器的目录：`mkdir /home/ftp` 新建FTP用户，ftpname为你为该ftp创建的用户名：`sudo useradd -d /home/ftp -s /bin/bash ftpname` 给用户设置密码：`passwd ftpname`

### 编辑配置文件

vim /etc/vsftpd.conf 修改如下内容

anonymous_enable=NO
local_enable=YES
write_enable =YES
local_root=/home/ftp

然后输入:光标到最后进入命令模式，输入w，然后:q保存并退出。直接输入:x可以保存退出 配置完成以后可以连接测试。 此时允许切换到上一级目录，可以通过在conf文件中修改chroot\_local\_user=YES实现进制切换到根目录，这个在默认配置文件中有，默认处于被注释状态。

### 设置目录权限

再新建一级目录`mkdir /home/ftp/write` sudo chmod -R 777 /home/test/write实现可写入 此时ftp目录只可查看，write目录可写

### 启动服务

`service vsftpd start`

### 阿里云专用网络补充

对于阿里云专用网络需要进行下面的配置，否则提示“服务器发回了不可路由的地址。使用服务器地址代替”以后不会获取到目录： 对于vsftpd.conf中增加如下内容：

listen=YES # 监听默认21端口
write_enable=YES # 可写权限
pasv_enable=YES# 启用pasv模式
pasv\_min\_port=20000 # 设置pasv模式中的可用端口范围（开始）
pasv\_max\_port=20500 # 设置pasv模式中的可用端口范围（结束）
pasv_address=XXX.XXX.XX.XXX # 设置pasv模式中的外网IP
seccomp_sandbox=NO # 关闭 seccomp 功能

然后在安全组的配置规则中增加： 21/21， 20000/20500 这两段端口的访问授权（授权对象0.0.0.0/0）

django配置
--------

### 项目修改

首先修改settings文件

1.  ALLOWED_HOSTS = \['服务器ip'\] 可以是ip，也可以是域名，也可以是通配域名`'.example.com'`，当DEBUG=True，且默认的hosts为空是，会自动填充为`['localhost'，'127.0.0.1'，'[:: 1]']`，若在服务器测试需要主动指定
2.  DEBUG=False
3.  整理静态文件，在debug=true是django会自动寻找静态文件，但关闭调试后不再寻找。
4.  注意python3.5和3.6版本中json区别，3.5版本无法自动解析二进制，需要用json.loads(text.decode('utf-8'))

### pip更新

pip更新以后会出现cannot import name main错误 通过vim /usr/bin/pip打开文件编辑

#!/usr/bin/env python
\# -*- coding: utf-8 -*-
import re
import sys
from pip.\_internal import main as \_main
if \_\_name\_\_ == '\_\_main\_\_':
    sys.argv\[0\] = re.sub(r'(-script\\.pyw?|\\.exe)?$', '', sys.argv\[0\])
    sys.exit(_main())

注意python脚本是空格，不是制表符 然后esc， :，x，回车 保存 注意 ubuntu默认带2.7，3.5。装了3.6以后，需要指定pip到python3.6版本 `alias "pip3.6"="python3.6 -m pip $1"` 这样以后输入pip3.6 可以在python3.6下安装package，否则默认的pip3安装到python3.5下，不通用

### 安装python3.6

sudo apt-get install software-properties-common
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
apt-get install libmysql-dev（为了装python-mysqlclient）
apt-get install libmysqlclient-dev（为了装python-mysqlclient）
sudo apt-get install python3.6-dev(如果是3.5就直接写python3-dev即可，2.7只需要写python-dev，不安装这个可能后面的python-mysqlclient安装失败)

输入python3.6测试安装结果，Ctrl+z退出

#### 安装uwsgi

pip3 install uwsgi

注意此处应该用运行django的python版本的pip指令，比如pip3.6对应python3.6,且用3.6运行django，那么需要用Pip3.6安装，否则若django/uwsgi不在一个版本下，将无法运行成功（uwsgi可以运行起来，但log记录的是无法找到python或者其他错误）

apt-get install uwsgi-plugin-python3

测试： 新建一个文件test.py

def application(env, start_response):
    start_response('200 OK', \[('Content-Type','text/html')\])
    return "Hello World"

然后cd到文件目录，uwsgi --http :20010--wsgi-file test.py 注意：端口号必须是阿里云安全组规则允许的端口，刚才ftp开了一些端口，所以直接用了其中一个 浏览器访问对应ip:端口可以看到Hello World

#### 安装nginx

sudo apt-get install nginx

启动service nginx start
关闭service nginx stop
重启service nginx restart

测试：启动以后，浏览器输入地址，可以看到Welcome to nginx!

### 安装django依赖库

测试用到了mysql，需要安装mysqlclient（注意是python的package）

> 如果无法安装提示Failed building wheel for mysqlclient，可以直接下载whl后安装。https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient pip3 install XXX.whl可直接安装本地的文件，确定上面的安装python3.6小节提到的都装了

apt-get install libmysqlclient-dev python3-dev（不进行此步会出现mysqlclient mysql_config not found错误，注意python3.X-dev，如果是3.5只需要写python3-dev，2.7只需要写python-dev，3.6必须写python3.6-dev） 然后pip3 install mysqlclient

### 测试项目-测试环境

python3 manage.py runserver 0.0.0.0:20200

建议先进入python中，分别import每个需要用到的包，确保都存在 注意要用python3, 否则python默认是2.X版本，也可以自行更改默认版本。0.0.0.0表示任意地址可访问，指定端口还是要用阿里云允许的。

### uwsgi配置

建立uwsgi.ini，我上传到了/home/ftp/write目录下

\# uwsgi.ini 文件说明 
\[uwsgi\] 
socket = 127.0.0.1:8000 
chdir=/home/ftp/write/testserver # 工程的绝对路径 
module=mysite.wsgi # wsgi.py在自己工程中的相对路径，”.”指代一层目录 
master = true 
workers=2 
vacuum=true 
thunder-lock=true 
enable-threads=true 
harakiri=30 
post-buffering=4096 
daemonize =/home/ftp/write/uwsgi.log # uWSGI日志的存储路径
stats=/home/hg/write/uwsgi.status  #记录状态
pidfile=/home/hg/write/uwsgi.pid #记录主进程id用于关闭

我的django项目在/home/ftp/write/testserver，也就是manage.py在/home/ftp/write/testserver/manage.py，wsgi.py在/home/ftp/write/testserver/testproject/wsgi.py。所以module路径写testproject.wsgi

ps aux | grep uwsgi 查看所有uwsgi进程
uwsgi --ini uwsgi.ini 启动
uwsgi --reload uwsgi.pid 重新启动 注意用的pid文件
uwsgi --connect-and-read uwsgi.status 查看状态
uwsgi --stop uwsgi.pid 停止

注意在启动以后，查看一下uwsgi.log文件，看是否有错误，如果在root运行会有warning:you ar running uWSGI as root!!!警告，这个不影响运行。主要确定是否有python version: X.X.X，这个对应自己的django版本，同时查看。 如果有“no python application found, check your startup logs for errors”，“uwsgi no internal routing support, rebuild with pcre support”错误，考虑pip安装uwsgi时使用的不是django运行python的版本，即运行uwsgi的python中不包含django及项目相关的package。

### nginx配置

`sudo nginx -t` 获取默认配置文件的路径：/etc/nginx/nginx.conf，根据返回内容确定 vim /etc/nginx/nginx.conf 里面写了默认路径include /etc/nginx/sites-enabled/* 编辑上面的路径下的文件编辑配置文件default（/etc/nginx/sites-enabled/default） 这是修改默认的文件，建议根据项目建立新的文件名进行server配置，注意不要修改/etc/nginx/nginx.conf文件，这个文件里已经包含了整个sites-enabled目录了

\# server 字段说明 
server { 
  listen 80; # 代表服务器开放80端口 
  server_name XXXXXXXXX; # 服务器地址 
  charset utf-8; 
  access\_log /home/ftp/write/nginx\_access.log; 
  error\_log /home/ftp/write/nginx\_error.log; 
  client\_max\_body_size 75M; 
  location /static { 
    alias /home/ftp/write/testserver/static; # 自己定义的项目引用静态文件 
  } 
  location / { 
    uwsgi_pass 127.0.0.1:8000; # uWSGI绑定的监听地址，这里使用了8000端口 
  } 
}

### 运行启动

sudo uwsgi --ini /home/ftp/write/uwsgi.ini，启动uwsgi nginx -c /etc/nginx/nginx.conf，启动nginx

> 启动uwsgi提示Can't find section "uwsgi" in INI configuration file。考虑一下情况： 1、win下建立的这个文件，看文件是不是utf8格式 2、utf8格式，如果win建立，最好开始有空行，比如空行写##，考虑到utf8+bom的因素，在linux下读取到的第一个符号可能乱码，如果开头是\[uwsgi\]的\[可能乱码，导致无法识别

出现问题时查错方法
---------

1.  查看python python3 python3.6等等一系列指令可用性，确定自己要用哪个版本
2.  查看pipXX -V的方式看装各种包的pip是不是上面确定的版本号
3.  pythonXX进入python，分别用import引用需要的所有的库，包括django/mysqlclient等，如果mysqlclient无法包含，查看上面提到的安装pythonX.X-dev
4.  cd到项目路径，通过pythonXX manage.py runserver 0.0.0.0:XXXX运行，注意端口号要是阿里云安全允许的端口，通过外部测试django是否可以正常运行
5.  阿里云中开放uwsgi使用的端口，比如uwsgi使用的是8000，则先打开8000端口的，通过外部直接访问uwsgi，看是否可以获取响应，若不可以，确定上述123点没有错误的情况下，查看uwsgi.log文件，找出问题解决，直到可以直接访问，此时关闭8000端口。
6.  最后nginx一般不会错，配置不需要过多的路径和依赖，只需要指定uwsgi的端口号即可