---
title: " docker配置Wordpress及MySQL\t\t"
tags:
  - mysql
  - wordpress
url: 1283.html
id: 1283
categories:
  - 后端
date: 2018-11-10 13:31:18
---

安装MySQL 
-------

docker pull mysql
docker run --name tlmysql -d -p 3306:3306 -v /ZZZZZ/mysql/data:/var/lib/mysql -e MYSQL\_ROOT\_PASSWORD="XXXXXXX" mysql

ZZZZ指路径，XXXXXX是mysql的root账号的初始密码设置，mysql新版本不允许root密码为空，所以一定要设置这个环境变量，否则无法run 其他参数：--name就是给容器取名字，-d后台运行，-v加上后面的目录表示把容器中的/var/lib/mysql目录和宿主机中的/ZZZZZ/mysql/data目录做映射，把数据库数据保存在本地。

安装PhpMyAdmin
------------

docker run --name tlphpmyadmin -d --link tlmysql:db -p 20201:80 phpmyadmin/phpmyadmin

link了刚建立的tlmysql容器，并且命名为别名db。在这个容器内通过tlmysql或者db均能访问到tlmysql容器的端口，如使用tlmysql:3306或者db:3306均可，注意不要使用Localhost这样访问的是当前容器（tlphpmyadmin）

安装WordPress
-----------

docker run --name tlwordpress -d -p 80:80 --link tlmysql:wpdb -v /XXXXX/wordpress:/var/www/html wordpress

link了刚建立的tlmysql容器，并且命名为别名wpdb 。在这个容器内通过tlmysql或者db均能访问到tlmysql容器的端口，如使用tlmysql:3306或者wpdb :3306均可，注意不要使用Localhost这样访问的是当前容器（tlwordpress），在初始化wp的过程需要设置数据库地址，此处地址就要写wpdb:3306即可，写localhost是无法连接的，当然如果是外部数据库直接写外部地址就行

### 数据备份还原

只需要备份/XXXXX/wordpress，/ZZZZZ/mysql/data文件目录即可。可保证数据不丢失 若只修改wp的wp-content目录，可以在-v后只映射wp-content目录-v /XXXXX/wordpress/wp-content:/var/www/html/wp-content。这样可以避免对wp的静态文件做备份。 还原只需要将run时的-v指向新的路径即可

其他注意
----

wp新版对mysql5.5+版本使用的默认是utf8mb4编码，而不是utf8/gbk等格式，迁移时注意编码避免乱码

使用Nginx反向代理
-----------

docker的wp默认是Apache，使用nginx进行反向代理，首先pull下来nginx的镜像，然后启动镜像，下面的代码是要先cd到自己建立好的目录下使用

docker run -p 80:80 --name tlnginx -v $PWD/nginx.conf:/etc/nginx/nginx.conf -v $PWD/conf.d:/etc/nginx/conf.d  -v $PWD/logs:/wwwlogs  -d --link wordpress:wp nginx

首先绑定了80端口，然后容器名字为tlnginx，把nginx.conf文件映射到了本地文件，同时把conf.d目录页映射了，最后将这个容器与wordpress的容器连接重命名为wp

> 注意如果这么设置，需要将wp的容器的-p 80:80删除，也就是wp容器不对外开放端口，nginx可通过link方式直接获取到wp的80端口，当然也可以选择映射到指定端口

然后nginx.conf文件默认不用修改就行，因为文件最后包含了conf.d目录下的所有*.conf文件，下面给出两个文件的内容。

nginx.conf:

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log\_format  main  '$remote\_addr - $remote\_user \[$time\_local\] "$request" '
                      '$status $body\_bytes\_sent "$http_referer" '
                      '"$http\_user\_agent" "$http\_x\_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;
    
    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;
    
    #gzip  on;
    
    include /etc/nginx/conf.d/*.conf;
}

conf.d/wp.conf
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

注意server\_name是访问地址，也可以用自己的其他地址比如blog.techieliang.com，location后面的/是表明将www.techieliang.com/地址代理到下面的地址，当然你也可以增加一层路径比如“location /wp {}”这样只有输入www.techieliang.com/wp才能跳转 下面的proxy\_pass http://wp:80;应该是指向一个具体路径，此处使用的wp，因为两个容器关联了，所以可以通过wp解析到容器具体ip，wp容器可以直接不对外映射端口 后面运行就行，如果docker ps -a无法看到正在运行nginx，请用docker logs tlnginx查看日志

低内存空间开启swap
-----------

内存过小docker可能会出现exited(137)错误，随机关闭某个容器，我这mysql被关了好几次。这实际上是os关的，并非docker。可以建立swap交换空间。

### 查看当前已有swap大小

free -m
total used free shared buff/cache available
Mem: 992 436 75 34 480 364
Swap: 0 0 0

可以看到 Swap 只有0，下面我们来扩大到2G，为什么2G，因为我这个物理内存是1G，一般大小建议如下：

物理内存

建议的交换空间大小

如果开启休眠功能建议的交换空间大小

? 2GB

2倍

3倍

\> 2GB – 8GB

相等

2倍

\> 8GB – 64GB

>4GB

1.5倍

\> 64GB

>4GB

不推荐休眠

### 创建一个 Swap 文件

首先cd到一个想要创建文件的地方，要注意这个目录所在硬盘分区要有大于所要创建大小的空间

mkdir /swap
cd /swap
sudo dd if=/dev/zero of=swapfile bs=1024 count=2000000

然后会提示创建成功，创建的大小、用时、拷贝速度 将普通文件转换成 Swap 文件

sudo mkswap -f swapfile

给出如下提示：

Setting up swapspace version 1, size = 1999996 KiB
no label, UUID=XXXXXXXXXXX

### 激活 Swap 文件

sudo swapon swapfile

再次查看 free -m 的结果

### 卸载Swap文件及自动挂载

如果需要卸载这个 swap 文件，可以进入建立的 swap 文件目录。执行下列命令。

sudo swapoff swapfile

如果需要一直保持这个 swap开机自动挂载，可以把它写入 /etc/fstab 文件。

/XXXX/swapfile /XXXX swap defaults 0 0

### 补充dd指令含义

dd：用指定大小的块拷贝一个文件，并在拷贝的同时进行指定的转换

注意：指定数字的地方若以下列字符结尾，则乘以相应的数字：b=512；c=1；k=1024；w=2

1\. if=文件名：输入文件名，缺省为标准输入。即指定源文件。< if=input file >
2\. of=文件名：输出文件名，缺省为标准输出。即指定目的文件。< of=output file >
3\. ibs=bytes：一次读入bytes个字节，即指定一个块大小为bytes个字节。
  obs=bytes：一次输出bytes个字节，即指定一个块大小为bytes个字节。
  bs=bytes：同时设置读入/输出的块大小为bytes个字节。
4\. cbs=bytes：一次转换bytes个字节，即指定转换缓冲区大小。
5\. skip=blocks：从输入文件开头跳过blocks个块后再开始复制。
6\. seek=blocks：从输出文件开头跳过blocks个块后再开始复制。
注意：通常只用当输出文件是磁盘或磁带时才有效，即备份到磁盘或磁带时才有效。
7\. count=blocks：仅拷贝blocks个块，块大小等于ibs指定的字节数。
8\. conv=conversion：用指定的参数转换文件。
  ascii：转换ebcdic为ascii
  ebcdic：转换ascii为ebcdic
  ibm：转换ascii为alternate ebcdic
  block：把每一行转换为长度为cbs，不足部分用空格填充
  unblock：使每一行的长度都为cbs，不足部分用空格填充
  lcase：把大写字符转换为小写字符
  ucase：把小写字符转换为大写字符
  swab：交换输入的每对字节
  noerror：出错时不停止
  notrunc：不截短输出文件
  sync：将每个输入块填充到ibs个字节，不足部分用空（NUL）字符补齐。

上述建立过程就是bs=1024 count=2000000，以1024位块大小，建立2000000个块，总共2048000000字节，所以显示的不是2GB大小，因为不是2097152字节。