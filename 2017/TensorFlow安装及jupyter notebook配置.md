title: " TensorFlow安装及jupyter notebook配置\t\t"
tags:
  - python
  - tensorflow
  - ubuntu
url: 68.html
id: 68
categories:
  - 其他
date: 2017-11-09 18:16:57
---
Ubuntu下安装Anaconda
-----------------
```
bash ~/file\_path/file\_name.sh
```
出现许可后可按Ctrl+C跳过，yes同意。 

安装完成后询问是否加入path路径，亦可自行修改文件内容 

关闭命令台重开
```
python -V
```
可查看是否安装成功 修改anaconda的python版本，以符合tf要求
```
conda install python=3.5
```
Anaconda安装TensorFlow
--------------------

获取Anaconda的TF源
```
anaconda search -t conda tensorflow
```
等待后会显示列表，我选择的_conda-forge/tensorflow源进行安装_
```
anaconda show conda-forge/tensorflow
```
会显示当前安装信息，并提示想要安装需要输入的指令：_conda install --channel?_ XXXX网址 

输入后提示需要安装的包信息，输入Y同意并安装。 

部分包下载速度很慢，可以先开lantern

jupyter notebook使用及配置
---------------------

Ubuntu下输入jupyter notebook打开 

`jupyter notebook --generate-config` 可生成默认配置文件`jupyter\_notebook\_config.py`

### 默认地址配置

修改配置文件  `c.NotebookApp.notebook_dir = 'XXXX'`

### 远程访问

默认不可远程访问 首先生成登陆密码：打开ipython输入
```
from notebook.auth import passwd
passwd()
```
根据提示输入欲设置的密码，复制生成的sha1密码 需要完整复制，例如：
```
sha1:ce23d945972f:34769685a7ccd3d08c84a18c63968a41f1140274
```
修改配置文件
```
c.NotebookApp.ip='*'
c.NotebookApp.password = u'sha:xxxxxxxxxxxxxxxxxxxxxxxxxx'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888
```
注意 ip内为*，密码前有u 

配置完成后可远程访问，配置完成后启动notebook时不会自动弹出网页，需要手动打开，打开后先输入密码才可访问。 

注1：ubuntu可用ifconfig获取本机ip地址 

注2：虚拟机可使用NAT模式或桥接模式使宿主可访问notebook