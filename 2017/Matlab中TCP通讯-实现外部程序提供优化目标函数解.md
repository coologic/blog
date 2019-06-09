---
title: " Matlab中TCP通讯-实现外部程序提供优化目标函数解\t\t"
tags:
  - matlab
  - tcp
url: 551.html
id: 551
categories:
  - 其他
date: 2017-12-06 11:33:18
---

介绍
--

TCP如此常用的通讯功能，matlab自然也是支持的。而在用途方面也有很多，比如matlab优化问题中目标函数可以是其他程序的运算结果，此时需要动态的每次优化调用其他程序，这时可以使用tcp实现两程序之间的数据交互，由matlab的优化工具箱提供每次优化的设计变量值，有某程序提供基于设计变量得出的运算结果值作为目标函数的解。

TCP使用方法
-------

Matlab 对TCP的封装很好，只有在建立和关闭tcp需要专用的函数，其他操作均与文件操作完全一致。 下面先给出一个用于优化的范例： 主脚本：

clc;clear;
%开启tcp
global mtcpip   %用全局变量方便优化函数中使用tcp
global circle_number
circle_number=0;
mtcpip=tcpip('127.0.0.1', 8000);
fopen(mtcpip);
fwrite(mtcpip,\['Matlab Connect in',char(13), char(10)\]) 
%优化算法--
current_value = \[123123  123123 123123 123321 123123  123123 13 123123 123123\];
lb=\[\];
ub=\[\];
new\_error = fmincon(@matlab\_function,current_value,\[\],\[\],\[\],\[\],lb,ub);%优化
% 优化技术-关闭tcp
fwrite(mtcpip,\['Matlab Disconnect',char(13), char(10)\]) 
fclose(mtcpip);
delete(mtcpip);

优化函数：

function \[ f \] = matlab_function( x )
global mtcpip
global circle_number
circle\_number=circle\_number+1;
x_size = size(x);
send_text=\['matlabvalue--'\];
for i=1:1:x_size(2)
    if i==x_size(2)
        send\_text=\[send\_text,num2str(x(i),'%.4f'),char(13), char(10)\];
    else
        send\_text=\[send\_text,num2str(x(i),'%.4f'),';'\];
    end
end
fwrite(mtcpip,send_text);
while 1
    A = fread(mtcpip, 20);
    text = char(A);
    text_size = size(text);
    if text_size(1)<2
        continue;
    end
    if  text(1) == 'm' && text(2) == 'r'
        text=text';
        text = strrep(text,'@','');//符号替换
        text = strrep(text,'mr','');
        text = strrep(text,'#','');//删除末尾多余的占位符，我用#作为占位符了
        f = str2double(text);
        \['circle one begin',num2str(circle_number,'%.4f')\]
        break;
    end
end
end

此范例的优化函数中利用一个while 1实现了阻塞，会不断的循环等待其他程序算出结果后成功接收到结果才完成单步优化计算。我设定了结果的固定表示格式 “mrXXXXXX###########@”消息总长度为20，末尾为@作为结束符，mr作为开头，XXXX为消息实际内容，#为占位符，以此方式保证每次发送消息均为20长度，同时可以通过数据头的mr确定收到的内容是不是传给matlab的结果文件（若多个tcp通讯需要做此判断防止发送错误，导致后续结果计算错误），只有当结果均计算正确，才目标函数返回值赋值为接收到的结果，并break进入下一次优化迭代

### 创建tcp

tcpip()函数创建一个tcp对象，可以在matlab中用help tcpip查到帮助，上述范例为开启客户端。对于目标主机ip可以用'localhost'代替127 注意此函数第三项参数用来配置为服务端还是客户端，若不写默认为客户端 注意开启完成后的返回值一定要保存，后续开启、收发和关闭均需要使用，此处因为需要跨多个m文件使用此tcp所以设置为了全局变量

### 开启tcp

fopen即可，参数传入tcp对象

### 关闭tcp

fclose? 就可以关闭一个已经开启的tcp了，参数传入tcp对象 建议用delete释放tcp对象的内容

### 收发

启动tcp以后，matlab支持直接用文件读写的方式进行tcp消息收发，直接使用fwrite、fread即可，第一个参数要传入开启tcp对象

其他
--

### matlab发送回车，换行符的方法

matlab不支持在字符串中用\\n实现换行，但是可以直接发送对应ASCII码，可以通过char(13), char(10)实现换行的发送，注意这两个对应的是\\r\\n，windows中是用\\r\\n实现换行，若linux直接发送\\n的10即可。

### matlab字符串连接

很简单\[send_text,num2str(x(i),'%.4f'),char(13), char(10)\]，直接用行向量数组即可，本身matlab就是用char数组表示字符串的

### 接收数据为列向量，转行向量方法

直接text=text';即可，和矩阵操作一样 别用string()去强制字符串，matlab中本身就是用char数组表示字符串，没有单独的字符串概念，转换完也没区别

### 字符串处理-替换

text = strrep(text,'@','');实现了吧text中的所有@删除，上述范例是因为发送来的数据是固定的格式，对格式做预处理 由于matlab接收tcp消息是按照固定长度，所以发送端若发送的内容不够长，可以在后面加占位符，matlab接收到以后删除占位符即可

### 接收长度限制

A = fread(mtcpip, 20); 此函数指定了接收的长度，若消息发送的长度不够，会一直阻塞，直到超时以后才会接收已有的为满足长度要求的数据，为了保证立即接收，建议发送数据末尾用占位符，接收到以后再替换掉。