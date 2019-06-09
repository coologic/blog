---
title: " python中datetime时区问题\t\t"
tags:
  - python
  - 时区
url: 1059.html
id: 1059
categories:
  - python
date: 2018-04-02 13:58:53
---

介绍
--

使用阿里云函数计算功能，基于python3构建函数，在本机通过datetime.now获取当前时间与数据库存储时间对比，获取时间差，可正常运行。 上传到阿里云后运行错误，后检测发现阿里云服务器默认时间为utc时间。解决方法如下。

解决方案
----

### 第三方模块

pytz可以很方便的修改时区，但是需要再引入一个模块，所以没用这个。

import pytz
import datetime
tz = pytz.timezone('Asia/Shanghai')
datetime.datetime.now(tz)#获得此时区的当期那时间
#可以通过pytz.timezone('cn')获取中国的所有可用的时区
#\['Asia/Shanghai', 'Asia/Harbin', 'Asia/Chongqing', 'Asia/Urumqi', 'Asia/Kashgar'\]

### 直接修改时区

下面转子，[缪雪峰博客](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431937554888869fb52b812243dda6103214cd61d0c2000)

\# 拿到UTC时间，并强制设置时区为UTC+0:00:
>>\> utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)
>>\> print(utc_dt)
2015-05-18 09:05:12.377316+00:00
\# astimezone()将转换时区为北京时间:
>>\> bj\_dt = utc\_dt.astimezone(timezone(timedelta(hours=8)))
>>\> print(bj_dt)
2015-05-18 17:05:12.377316+08:00
\# astimezone()将转换时区为东京时间:
>>\> tokyo\_dt = utc\_dt.astimezone(timezone(timedelta(hours=9)))
>>\> print(tokyo_dt)
2015-05-18 18:05:12.377316+09:00
\# astimezone()将bj_dt转换时区为东京时间:
>>\> tokyo\_dt2 = bj\_dt.astimezone(timezone(timedelta(hours=9)))
>>\> print(tokyo_dt2)
2015-05-18 18:05:12.377316+09:00

此方法直接获取很容易，包括使用pytz，但是这样获取到的dt类型都是带时区类型，此时直接和sql获取到的时间比较会出现“带时区与不带时区类型不可转换”的错误

### 直接加减

utc_dt = datetime.utcnow()
bj\_dt = utc\_dt+timedelta(hours=8)
delta\_dt = bj\_dt - sql_dt

此方案要求sql的计时时区要固定