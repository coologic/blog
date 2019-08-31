---
title: "吴恩达机器学习作业Python实现及学习笔记（1）"
tags:
  - Jenkins
categories:
  - CI
date: 2019-08-31 10:55:00
---

## 写在前面的话

上线前最后24小时，激动人心的时刻，突然Jenkins重启了。。。。。。

下面记录一下一波三折的过程

## 一波--Jenkins手动重启？No！

> Monitor一直显示着Jenkins所有Pipeline状态，保持常绿，心情舒畅，一键CD应该是没问题了，最后调调小Bug准备下班~~~~

”你们谁重启了Jenkins？“

”￥%©∆˙˙˙∫˚∆#￥%#￥&**！@#%“

”·∆˙©˚∆˙©˙˚∆ø˙∫¬˚˙©˚˙∆˚˙¥¡•ª¶£ª“

”等它重启完再提交吧“

…………

”Monitor怎么没了？“

…………

”我们的PipeLine怎么都没了？“

”快看看Jinkens Log！怎么Log都没了“

”这不是重启，谁给我们ReSet了？“

”是不是Ops操作错误？“

”先看看是不是坏了，先建立一个PipeLine能不能正常创建“

”OK建好了“

> 求救Dev Ops/Tech Leader

## 一折--容器内存不足挂了！Volume为什么没有？！

夏：“你看Kubernetes上显示，你们的Jenkins在45分钟前连续内存占用警报，然后容器的down了”

“你们的配置为什么也没了？看来是Volume没配置”

冰：“那为什么还有一个幸存的Pipeline”

……“那是我们自己建立一个实验的”

夏：”那现在容器自动重启了，没问题了，你们自己重新配置一下Pipeline就可以了“

………

XY：”我们负责装一下插件“

HH：”我们负责把Pipeline建好“

XY："插件装不上，connect error"

夏：“安装插件权限你们有”

> 经过多次尝试，终于装好了

夏：“可能是资源有限，下载总失败”

> 10分钟后，距离下班不到1小时

XY：“插件都没问题了”

HH：“Pipeline Ok”

夏：”Credentials配置好了“

ALL：”构建然后上线，尽快搞定“

#### Keynotes

* 容器配置一定要将服务的配置文件、数据文件都设置Volume
* Log文件也很重要，给自己留一条查看问题的路
* 容器配置好后，最好模拟一次down up，看看care的数据是否还在

## 二折--红着不能下班#￥%&那就加班吧

> 一波Build操作，从灰色全部变成了红色

"我们都配置好了，为什么还是无法运行？"

”让装的插件都装了“

”再试一次“

”还是不行“

HH：”是不是装了插件要重启？“

L：”如果提示了就要重启“

HH：”我怕重启了又Rollback了“

L：“放心，重启吧”   P.s.反正现在也是空的

> 重启后所有后端项目都OK了，前端还是一直红

??：“front-end一直报node sass”

``` 
Node Sass could not find a binding for your current environment: Linux 64-bit with Node.js 8.x

Found bindings for the following environments:
  - Linux 64-bit with Node.js 8.x

This usually happens because your environment has changed since running `npm install`.
Run `npm rebuild node-sass` to download the binding for your current environment.
```

HH："装个node.js插件，盖一下node版本试试"

> 多次安装尝试，终于成功装上插件了

HH："版本改了，还是不行，要不我们用npm rebuild node-sass写到build.sh？"

L："不让随便改配置了，先想想其他方法"

> 经过不断尝试，仍未找到方法，最后向夏求助

夏：”完全可以改build.sh呀，都挂了没法上线了，改了就行了，这个原因可能是部分Jinkens配置未加载，以前的配置文件在这次启动的容器中无法正常运行“

#### Keynotes

* 突发状况，一定要冷静处理
* 紧急时刻，多找老司机是很不错的途径
* 在有限的信息中，快速敏锐的发现/追踪到问题根源
* 不要盲目行动，最好现有几种方案，在时间/技术……多维度权衡后选择，懂得变通

## 三折--人生何处不精彩！staging模拟环境版本神奇Rollback

> 时间到了上线前最后2小时

”我们上线了第三次迭代的版本，为什么当前功能是第二次迭代的？“

”我又上传了一次，怎么成了第一次迭代的了？“

> 头戴光环的娟姐出现了

娟姐：”你们清除一下浏览器缓存，用隐身模式也行，是不是缓存的问题“

HY：”我这清空了浏览器缓存，看起来是第二次迭代的“

MM：”我这清空了缓存也是第一次迭代的“

> 每个人的电脑在隐身模式下看到的都不一样
>
> 一个人的电脑刷新前后看到的不一样

HH：”后端接口也有问题Postman访问不了“

夏：“给我你们没个迭代新增的后端接口”

夏：”我们这测试访问成功了第二次迭代的接口“

HH："我又测了一次不行"  ------P.s是不是这个时间段，有人又重新构建了一个版本的？无从考证了

> 冰姐闪亮登场

冰：“你们Jenkins初始化了？每次构建的Id从1开始了？会不会是和之前的id重复，导致编译后的包无法覆盖”

…………

L："把以前build后的删除行么"

夏：”这个有可能，我们试试能删除么“

> XF大佬最后终于出手了

XF：”你们知道Jenkins最后构建的image是怎么命名的么？“

”知道，应该包含了Build Number“

> XF打开了一个Pipeline，“Set Next Build Number"，设置了10000

XF：”再Bulid就行了，试试可以么，把其他的也改了，确认一下都没问题么“

#### Keynotes

* 描述问题要清晰，紧急时刻要保证交流效率
* 先确定流程的每一步，筛选出可能出问题的步骤，逐步验证每一步的正确性
* 对于工具的使用不能只浮于表面
* Jenkins构建的Image涉及到Build Number，不应让number重复
* 对环境的配置/部署尽量使用代码(写到对应的.sh里)，而不是直接进入容器操作，以便追踪/复现……



