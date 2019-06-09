---
title: " ApiCloud学习笔记(2)-数据云API用户登录注册\t\t"
tags:
  - apicloud
url: 907.html
id: 907
categories:
  - 前端
date: 2018-02-06 20:18:02
---

用户登录
----

通过api调试页面可以获取用户登录的方法，也可在[云数据API](https://docs.apicloud.com/Cloud-API/data-cloud-api#3)中查到，使用jquery.ajax实现登陆： 页面html：

<section class="aui-content aui-margin-t-15">
        <ul class="aui-list aui-form-list">
            <li class="aui-list-item">
                <div class="aui-list-item-inner">
                    <div class="aui-list-item-label aui-border-r color-orange">
                        手机号 <small class="aui-margin-l-5 aui-text-warning">+86</small>
                    </div>
                    <div class="aui-list-item-input aui-padded-l-10">
                        <input type="number" pattern="\[0-9\]*" placeholder="输入手机号" id="mobile" >
                    </div>
                </div>
            </li>
            <li class="aui-list-item">
                <div class="aui-list-item-inner">
                    <div class="aui-list-item-input" style="width: auto;">
                        <input type="password" placeholder="输入密码" id="code">
                    </div>
                </div>
            </li>
        </ul>
    </section>
    <section class="aui-content-padded">
        <div class="aui-btn aui-btn-block aui-btn-info aui-btn-sm" tapmode onclick="fnSelectLogin();">登录</div>
    </section>

fnSelectLogin方法

<script type="text/javascript" src="../../script/jquery-3.3.1.js"></script>
<script type="text/javascript" src="../../script/SHA1.js"></script>
    function fnSelectLogin() {
      //取编辑框内容
      var code_edit = $api.byId('code');
      var mobile_edit = $api.byId('mobile');
      alert(mobile_edit.value);
      //取时间计算appkey
        var now = Date.now();
        var appKey = SHA1("A6062192783555" + "UZ" +
            "792719C9-1BDE-8474-D699-4362EC6E2546" +
            "UZ" + now) + "." + now;
            //调用jQuery.ajax
        $.ajax({
            "url": "https://d.apicloud.com/mcm/api/user/login",
            "cache": false,
            "headers": {
                "X-APICloud-AppId": "A6062192783555",
                "X-APICloud-AppKey": appKey
            },
            "data": {
                "username": mobile_edit.value,
                "password": code_edit.value
            },
            "type": "POST"
        }).done(function(data, status, header) {
            //success body
            var userid = data.userId;
            if (undefined == userid) {
                alert('登陆过程未知错误')
                return;
            }
            alert("userid=" + userid);
        }).fail(function(header, status, errorThrown) {
            //fail body
            alert("用户名或密码错误");
        });
    }

注意包含jquery.js和SHA1.js，sha1下载地址：[下载](https://github.com/apicloudcom/mcm-js-sdk/blob/master/SHA1.js)

用户注册
----

html：

     <section class="aui-content aui-margin-t-15">
        <ul class="aui-list aui-form-list">
            <li class="aui-list-item">
                <div class="aui-list-item-inner">
                    <div class="aui-list-item-label aui-border-r color-orange">
                        手机号 <small class="aui-margin-l-5 aui-text-warning">+86</small>
                    </div>
                    <div class="aui-list-item-input aui-padded-l-10">
                        <input type="number" pattern="\[0-9\]*" placeholder="请输入手机号注册" id="mobile" >
                    </div>
                </div>
            </li>
            <li class="aui-list-item">
                <div class="aui-list-item-inner">
                    <div class="aui-list-item-input" style="width: auto;">
                        <input type="password" placeholder="请输入密码" id="code">
                    </div>
                </div>
            </li>
            <li class="aui-list-item">
                <div class="aui-list-item-inner">
                    <div class="aui-list-item-input" style="width: auto;">
                        <input type="password" placeholder="请再次输入密码" id="code2">
                    </div>
                </div>
            </li>
        </ul>
    </section>
    <section class="aui-content-padded">
        <div class="aui-btn aui-btn-block aui-btn-info aui-btn-sm" tapmode onclick="fnSelectLogin();">注册</div>
    </section>

js

function fnSelectLogin() {
  //取编辑框内容
  var code_edit = $api.byId('code');
  var code2_edit = $api.byId('code2');
  if(code2\_edit.value != code\_edit.value) {
    alert("两次密码输入不一致");
    return;
  }
  var mobile_edit = $api.byId('mobile');
  //取时间计算appkey
    var now = Date.now();
    var appKey = SHA1("A6062192783555" + "UZ" +
        "792719C9-1BDE-8474-D699-4362EC6E2546" +
        "UZ" + now) + "." + now;
        //调用jQuery.ajax
    $.ajax({
        "url": "https://d.apicloud.com/mcm/api/user",
        "cache": false,
        "type": "POST",
        "headers": {
            "X-APICloud-AppId": "A6062192783555",
            "X-APICloud-AppKey": appKey
        },
        "data": {
            "username": mobile_edit.value,
            "password": code_edit.value
        }
    }).done(function(data, status, header) {
        //success body
        var userid = data.id;
        if (undefined == userid) {
          alert('注册失败，用户名已存在');
          return;
        }
        alert('注册成功  userid='+ userid);
        api.closeWin({
            name: api.winName
        });
        return;
    }).fail(function(header, status, errorThrown) {
        //fail body
        alert("用户名或密码错误");
    });
}

首先取输入框内容，注意输入框是number类型，如果输入了非数字内容提取到的mobile_edit.value只包括数字，会导致实际注册的用户名不一致，上述代码并未处理此问题。 然后判断两次密码输入的一次性，最后上传。 jq.ajax的done是表明收到了200状态码，但是并不一定收到200就是注册成功，应该判断data.id是否有内容，若没有表明注册失败，失败原因是用户名重名。