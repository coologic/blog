---
title: " ApiCloud学习笔记(1)\t\t"
tags:
  - apicloud
url: 904.html
id: 904
categories:
  - 前端
date: 2018-02-06 11:43:46
---

项目建立
----

登陆账户-管理台即可建立，其中index.html为入口文件，在config.xml中可以更改入口文件名

新建页面框架
------

在studio中要右键项目名才能出现此提示，新建后默认在html目录根目录，如果要移动文件位置注意修改文件内的所有路径，路径默认是依照根目录进行的,css文件路径是../css，js文件路径是../script

html模板
------

<!DOCTYPE html>
  <html>
  <head>
      <meta charset="utf-8">
      <meta name="viewport" content="maximum-scale=1.0,minimum-scale=1.0,user-scalable=0,width=device-width,initial-scale=1.0"/>
      <title>title</title>
      <link rel="stylesheet" type="text/css" href="../css/api.css"/>
      <style>
          body{

          }
      </style>
  </head>
  <body>

  </body>
  <script type="text/javascript" src="../script/api.js"></script>
  <script type="text/javascript">
      apiready = function(){

      };
  </script>
  </html>

head里面添加需要引用的css文件使用<link rel="stylesheet" type="text/css" href="../css/api.css"/>，同时可以在head-style中写当前文件自定义的css body写页面代码，其后引用js文件，并在<script type="text/javascript">? </script>之间写当前文件自定义js脚本 如果使用jq需要自行[下载js文件](http://jquery.com/download/)，放置到script目录，并在需要使用html中添加<script type="text/javascript" src="../../script/jquery-3.3.1.js"></script>，注意相对路径的更改。

html“构造函数”-apiready事件
---------------------

从C++开始用apicloud，感觉这就是个构造函数，每个html文件都要具有apiready = function(){ };，在窗口被创建时会自动调用此函数，当然apiready也是一个事件，只不过写的方式比较另类，其余的事件使用方法见下

事件收发机制
------

### 创建事件和系统事件

类似于信号槽机制？首先看一下apicloud自带的事件列表：[https://docs.apicloud.com/Client-API/api](https://docs.apicloud.com/Client-API/api)，进入后选择Event标签即可。 当然还提供了自定义事件的方法在method标签下的消息事件，具有如下方法： [addEventListener](https://docs.apicloud.com/Client-API/api#2 "添加事件监听")监听事件 [removeEventListener](https://docs.apicloud.com/Client-API/api#38 "移除事件监听")取消监听 [sendEvent](https://docs.apicloud.com/Client-API/api#72 "发送事件")发出一个事件（创建事件） [accessNative](https://docs.apicloud.com/Client-API/api#88 "SuperWebView中js向原生发送消息")这个不太懂也没试 [notification](https://docs.apicloud.com/Client-API/api#65 "弹出通知提示")向用户发出震动、声音提示、灯光闪烁、状态栏消息等通知，以及闹钟功能 [cancelNotification](https://docs.apicloud.com/Client-API/api#69 "取消本应用弹出到状态栏的某个或所有通知")取消通知 创建事件的方法：

api.sendEvent({
    name: 'myEvent',
    extra: {
        key1: 'value1',
        key2: 'value2'
    }
});

extra为关键字，不可以更改，里面的为附带参数可以为多个。name为事件名称，要注意事件名称全局有效，当发出事件后所有监听者都会接收到，而不是类似于Qt信号槽创建了信号不connect不会与任何槽有关联。当然同一个事件名称带不同的参数应该也是可行的。

### 监听事件

无论自定义事件还是系统事件都用一致的监听方法。apiready事件除外。

api.addEventListener({
    name: 'online'
}, function(ret, err) {
    alert('已连接网络');
});

回调函数参数中ret是个json，其内value包含了所有参数，可通过ret.value.key1获取到上面的'value1'值

### 基于事件监听实现底部菜单切换

底部菜单切换后需要头部和中间内容同时切换，看了一些例子都是在实现底部菜单切换处负责了头部和中间内容切换，比如在index.html的body中写了底部，在js中处理点击事件，点击某个页面后将对应的头部和内容show，可以new新的或者设置hidden为false其余为true，同时更改body中不同按钮的class是否具有aui-active。 当然使用事件监听可以只将头部作为显示，也就是说只需要在index中建立一个framesgroup存储头部即可，同时发出事件等待不同的头部自行处理自己的页面显示。

//选择一个页面
    function fnSelectPage(index_id) {
        //转换显示的选中状态
        //获取底部可选项dom
        var eFootLis = $api.domAll('#footer .aui-bar-tab-item');
        for (var i = 0, len = eFootLis.length; i < len; i++) {
            $api.removeCls(eFootLis\[i\], 'aui-active');
        }
        $api.addCls(eFootLis\[index_id\], 'aui-active');
        //显示指定页面
        api.setFrameGroupIndex({
            name: 'group',
            index: index_id
        });
        api.sendEvent({
            name: 'PageChanged',
            extra: {
                page\_index: index\_id,
            }
        });
    }

AUI前端框架
-------

第一次使用，还不太理解，相关的帮助文档：[http://www.auicss.com/](http://www.auicss.com/)，通过css框架实现了常用界面ui的实现，包括布局、按钮、标签、列表等