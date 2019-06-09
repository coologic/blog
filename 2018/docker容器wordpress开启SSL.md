---
title: " docker容器wordpress开启SSL\t\t"
tags:
  - nginx
  - ssl
  - wordpress
url: 1309.html
id: 1309
categories:
  - 后端
date: 2018-11-26 11:04:17
---

证书申请
----

可以用阿里云、百度云等证书管理，这里使用永久免费证书Let's Encrypt，注意是3个月期限。。。后续要更新 阿里云有Symantec 免费版，一年期限。

### Let's Encrypt申请方法

在ubuntu16.04版本下（没有其他修改）可以直接git下载。当然如果有修改请确保有python2.7+和git。 python -V查python版本，git --version查git版本。 git clone https://github.com/letsencrypt/letsencrypt? 下载，注意这之前请先cd到自己想要的目录下,git会下载到指令当前目录 然后cd letsencrypt 然后确保nginx等已关闭，不要bind 80等端口，否则下面一步无法进行： 创建证书：./letsencrypt-auto certonly --standalone --email techieliang@qq.com -d techieliang.com -d www.techieliang.com

> 如果报错：OSError: Command /opt/eff.org/certbot/venv/bin/python2.7 - setuptools pkg_re……………… 卸载virtualenv： pip uninstall virtualenv 再安装virtualenv ： pip install virtualenv==XX.X.X?? 这里写个高版本就行 18.1.0可以 15.1.0也可以

注意上面的代码需要修改自己的邮箱地址（会发邮件让确认的），-d是网址，现在新版支持通配符，可以*.techieliang.com，具体自行百度 此时到/etc/letsencrypt/live/hostXXXX.XXX（网址路径）下可以看到证书文件 cert.pem - Apache服务器端证书 chain.pem - Apache根证书和中继证书 fullchain.pem - Nginx所需要ssl_certificate文件 privkey.pem - 安全证书KEY文件

> 90天期限，最后7天才可以续期。续期方法： ./letsencrypt-auto certonly --renew-by-default --email techieliang@qq.com -d techieliang.com-d www.techieliang.com 建议使用linux的 crontab做自动续期，避免遗忘

### 阿里云中申请Symantec 免费版

https://yundunnext.console.aliyun.com/然后购买证书，选择symantec，此时不显示免费行，选择下面的增强型，然后再选择免费型就行了。 两个小时即可完成申请，不用等几天。刷新控制台页面可以绑定到云产品也可以直接下载，下载后是个压缩包解压即可。

开启ssl
-----

理论上ssl只需要在最外层的服务上开启即可，如nginx/apache，如果是nginx跳转到apache只需要在nginx开启。 本博客使用wp的docker容器所以wp是由apache运行的，但是还用上了nginx反向代理，nginx的http配置文件如下：

server {
    listen       80;
    server_name   www.techieliang.com;
    location / {
        #反向代理路径
        proxy_pass http://wp:80;
        #反向代理的超时时间
        proxy\_connect\_timeout 10;
    } 
}

具体可见上一篇文章：[docker配置WordPress及MySQL](https://www.techieliang.com/2018/11/1283/) 再建立另一个.conf文件提供对443端口监控：

server {
    listen 443 ssl;
    #证书(公钥.发送到客户端的)
    ssl_certificate /etc/nginx/conf.d/wpssl.crt;
    #私钥,
    ssl\_certificate\_key /etc/nginx/conf.d/wpssl.key;
    ssl\_session\_timeout 10m;
    ssl_protocols SSLv2 SSLv3 TLSv1;
    ssl_ciphers "CHACHA20:GCM:HIGH:!DH:!RC4:!aNULL:!eNULL:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS"; 
    #下面是绑定域名
    server_name www.techieliang.com;
    location / {
        #反向代理路径
        proxy_pass http://wp:80;
        #反向代理的超时时间
        proxy\_connect\_timeout 10;
    } 
}

> 此处监听443端口，不需要写ssl on;高版本的nginx已经不用写了 反向代理还是用http和80端口就行 路径地址注意将秘钥文件放到已经和nginx容器映射的地址中，然后地址写容器的地址不要写本机地址 不需要配置跳转，上面两个conf文件都保留运行即可

用http协议打开wp后台，然后插件安装**Really Simple SSL**，然后开启，此时再用https登录后台，选择启用ssl功能即可，不需要其他配置

> 如果开启really simple ssl仍然没有绿锁，请在wp的根目录文件：wp-config.php文件最后增加： define('FORCE\_SSL\_LOGIN', true); define('FORCE\_SSL\_ADMIN', true);

使用插件不需要进行其他配置，包括301跳转此插件也会完成，但是如果用nginx反向代理到Apache，最好在nginx设置跳转，否则百度的https认证无法通过(虽然输入http最后会变成https并有绿锁，但就是通不过认证)，如果要在nginx进行跳转，请将对80端口监听的conf文件修改成，并重启nginx：

server {
    listen       80;
    server_name   www.techieliang.com;
    return 301 https://$server\_name$request\_uri;
}