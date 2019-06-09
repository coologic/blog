---
title: " 阿里云API网关与函数计算交互\t\t"
tags:
  - python
  - 阿里云
url: 1066.html
id: 1066
categories:
  - 后端
date: 2018-04-12 22:07:25
---

介绍
--

阿里云提供两种套件：api网关、函数计算，综合使用可实现Severless，其相关的帮助文档如下：[API网关](https://help.aliyun.com/document_detail/29464.html)、[函数计算](https://help.aliyun.com/document_detail/52895.html)。 官方文档很全面，认真研读可以完全理解并正确使用，下面记录我的理解。对于函数计算我用的是python语言，可以支持多种语言开发。 函数计算：实现后端业务逻辑，包括对请求的验证/处理、数据库的增删改查、第三方API的调用等等，以函数的形式表示，每个函数对应一个业务或者说一个API。函数指对外的一个函数，并不是所有业务都要写在一个函数中，支持文件夹、压缩包上传，只要指定函数入口即可。 API网关：url到函数计算中的某个函数的映射 对于函数计算是可以单独使用的，若嵌套api网关，则函数计算的输入输出就需要符合api网关的规则。输入自然是无法改变的，网关自动传入，输出必须符合要求，否则网关无法识别则会自动返回错误。 这个文章写得很乱，也不会有图片，想要图文并茂的可以看：[地址](https://help.aliyun.com/document_detail/68135.html)

连接方式
----

这个在文章：[API网关触发函数计算](https://help.aliyun.com/document_detail/66672.html)中写得很清楚

网关到函数计算的入参及返回格式
---------------

### 入参

网关的调用都是http协议，涉及到header、body等等，最终会构成一个json字符串以python的event参数传入，传入格式：

{
    "path":"api request path",
    "httpMethod":"request method name",
    "headers":{all headers,including system headers},
    "queryParameters":{query parameters},
    "pathParameters":{path parameters},
    "body":"string of request payload",
    "isBase64Encoded":"true|false, indicate if the body is Base64-encode"
}

在网关中将body类型传入到函数计算的query(后端参数类型)，此时query会以json格式显示。 下面一个例子，输入a,b两个数字（字符串形式显示），返回加法结果

import json
import zaliapi.ApiResponse as zapi
def handler(event, context):
    event_json = json.loads(event)
    headers\_json = event\_json\['headers'\]
    queryParameters\_json = event\_json\['queryParameters'\]
    a\_num = queryParameters\_json\['a'\]
    b\_num = queryParameters\_json\['b'\]
    return json.dumps({'isBase64Encoded': False,
         'statusCode': 200,
         'headers': {},
         'body': {'msg': str(int(a\_num)+int(b\_num))}})

如果直接return event，则可以看到event的内容。注意body里面放event

import json
import zaliapi.ApiResponse as zapi
def handler(event, context):
    event_json = json.loads(event)
    headers\_json = event\_json\['headers'\]
    queryParameters\_json = event\_json\['queryParameters'\]
    a\_num = queryParameters\_json\['a'\]
    b\_num = queryParameters\_json\['b'\]
    return2 = json.dumps({'isBase64Encoded': False,
         'statusCode': 200,
         'headers': {},
         'body': event_json})
    return return2

> {"headers":{"X-Ca-Api-Gateway":"BE0C2165-16EC-4C91-9C4E-40B05A75EA08","X-Forwarded-For":"106.11.228.XXX","Content-Type":"application/x-www-form-urlencoded; charset=UTF-8"},"body":"","pathParameters":{},"httpMethod":"POST","path":"/testadd","isBase64Encoded":false,"queryParameters":{"b":"142","a":"12323"}}

### 返回格式

{
    "isBase64Encoded":true|false,
    "statusCode":httpStatusCode,
    "headers":{response headers},
    "body":"..."
}

注意如果用python，body处也可以直接用python类型，不用像上面的例子一样去转换为json字符串

其他
--

### sdk下载

需要下载sdk：aliyunsdkdybaseapi模块，如果使用阿里云的短信服务还需要下载aliyunsdkcore、aliyunsdkdysmsapi模块，当然可以直接在下面地址下载完整的包：[地址](https://developer.aliyun.com/sdk)

### 网关调用

在网关管理平台可以直接测试api，测试的时候只需要输入参数即可，但在其他平台调用，需要对请求头部进行整理，其中的规则请见文档：[地址](https://help.aliyun.com/document_detail/29490.html)。 在测试的时候这些头部会自动生成，可以见测试右侧的请求内容，而自行调用时需要自己填充。 除此以外想要调用还需要：

1.  api网关建立一个应用app
2.  将api授权给app
3.  将api发布
4.  调用对应的发布状态，同时填充X-Ca-Stage: RELEASE头部