---
title: " 机智云Gokit3.0开发板宠物屋案例实现\t\t"
tags:
  - 机智云
url: 1011.html
id: 1011
categories:
  - 嵌入式
date: 2018-02-28 16:55:28
---

介绍
--

[机智云](http://www.gizwits.com/)提供物联网的智能硬件自主开发及云服务平台。其提供了Gokit系列开发板可用于快速搭建物联网实验产品。提供了相应的文档介绍，但不够连续，经过了半天的研究，整理如下。

准备工作
----

首先，机智云提供两种开发模式，MCU及SoC模式，mcu就是使用stm32/arduino作为控制芯片使用wifi/gprs等作为联网芯片。soc模式是充分利用esp8266这个wifi，直接用此模块实现控制和联网，节省了stm32等成本。soc模式使用方法请见：[GoKit3(S) 二次开发--开发环境搭建](http://docs.gizwits.com/zh-cn/deviceDev/WiFiSOC/GoKit3S%E4%BA%8C%E6%AC%A1%E5%BC%80%E5%8F%91.html)。里面讲解的绝大部分都是soc模式使用方法，包括开发环境搭建都是为了对esp8266进行烧录。下面主要记录一下我用mcu模式的使用过程：

*   Gokit3.0硬件一套，购买网址：[淘宝](https://item.taobao.com/item.htm?_u=svfnck382cf&id=542479181481)。199一套。 包括了stm32底板（也有arduino底板，我只会stm32）、功能板（就是一系列的传感器及联网模块的接口）、esp8266模块（wifi联网模块，可以再买G510的gprs联网模块）
*   Keil，因为用的stm，需要装上。如果是5版本，需要下载对应芯片的包，stm32f103c8
*   Stm32CubeMx，一个很实用的stm32工具，[下载](http://www.st.com/content/st_com/zh/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-configurators-and-code-generators/stm32cubemx.html)，下载的是软件，最后在选择具体芯片以后还会下载对应芯片的包。
*   CP2102驱动，gokit有usb转串口模块2102，需要装好驱动，就能够识别，注意win10请安装新版本，给一个测试可用的地址，[下载](http://www.121down.com/soft/softview-83274.html)
*   flash\_loader\_demonstrator。官方提供的stm32烧录软件。
*   “微信宠物屋 for GoKit 2/3 STM32 V03010101”官方提供的案例，里面包含了对gokit不同传感器/模块的控制驱动，还有一个教程文档。[下载](https://download.gizwits.com/zh-cn/p/92/93)
*   自动生成的MCU代码，见后面说明

代码准备
----

详见“微信宠物屋 for GoKit 2/3 STM32 V03010101”中《DIY微信宠物屋_STM32CubeMX版.pdf》文档的“ 2 定义"微信宠物屋"产品” 文档里说明了 “自动生成的MCU代码” 如何获取下载。

> 注意用STM32CubeMX版文档

对于*.ioc文件配置，首先从“微信宠物屋 for GoKit 2/3 STM32 V03010101”复制并覆盖自动生成的代码的同名文件，然后打开Stm32CubeMx，用此软件打开这个文件并点击重新配置按钮，此软件会自动根据ico文件内容配置微信宠物屋项目的代码。

烧录
--

文档里面没有烧录的说明了。首先要确保已经安装了CP2102驱动。

1.  项目默认配置只生成*.axf文件，需要修改配置。在keil中点击Option of target图标(编译按钮右侧，剪刀+魔术棒图案，快捷键Alt+F7)，output页面下选中 Create HEX file
2.  选择编译按钮，会在“MCU\_STM32F103C8x\_source\\MDK-ARM\\STM32F103C8x”目录下生成“STM32F103C8x.hex”文件。
3.  把开发板底板的一个开关从Flash改为System，然后点击Reset按钮。
4.  打开flash\_loader\_demonstrator，如果装了驱动会识别到这个串口的，如果多个串口，那就自己去设备管理器找具体哪个。下一步再下一步。
5.  Target目标改成128k，下一步
6.  Downloa from file 选择上面的“STM32F103C8x.hex”文件，下一步完成。
7.  设备拨回Flash，Reset。

> 注意，如果忘了点击Reset按钮，flash\_loader\_demonstrator回识别不到，点一下就行。还不行重开flash\_loader\_demonstrator。 如果flash\_loader\_demonstrator在第一个界面点了下一步，再点上一步后再点下一步，此时可能会卡死，估计是因为第一次下一步占用了这个串口，返回以后再下一步有一次连这个串口导致的错误，还是关闭以后，Reset设备，然后再打开即可。

使用设备
----

### 更改设备wifi信息接入机智云

设备接电以后，按一下key2进入等待airlink连接模式。（注意不会亮绿灯了，因为按照上面的配置把短按key2的代码改了，没有亮绿灯的代码了，但是还会进入airlink模式） 此时手机启动 **IoE Demo App** ，右上角，添加设备，输入当前使用的wifi账号密码。然后会自动“告知”设备连入此wifi，进而实现设备联网。注意这个wifi应该是能够连到外网的。 设备成功联网后，手机可以在任何途径控制设备。

设备基于Wifi联网接入原理
--------------

转子：[网址](http://club.gizwits.com/thread-3221-1-1.html) GoKit提供三种配置方式：AirLink 、WebConfig、 SoftAP。下面我们分别学习三种不同的配置方式

### **AirLink配置入网**

AirLink配置就是说明书上介绍的方法，实现过程就是：通过按键触发开启设备“AirLink”模式，开启后设备会不断接收特定编码的WiFi广播包，手机连接可用的WiFi网络后，通过指定的App（如IoE Demo）发送编码后的WiFi网络的SSID和密码广播，设备接收到之后自动尝试连接此WiFi网络，连接成功即配置完成。下面一步步完成GoKit通过AirLink接入路由器连接网络吧。（注意：AirLink配置不支持5G的WiFi网络，请使用传统2.4G WiFi信号）

1.智能手机进入“系统设置”连接您附件可用的WiFi网络。

2.打开下载好的“IoE Demo” App，点击主屏幕右上角“菜单栏”中的“添加新设备”。如下图 3.使用USB线为GoKit供电，开机后长按［KEY2］直到［RGB LED］亮绿灯（Arduino版本GoKit短按［KEY2］），表示设备AirLink模式已经开启。如下图

4.IoE Demo APP上输入已连接WiFi的密码，点击配置按钮，等待30秒到一分钟，APP提示配置成功。在此期间，您可以看到GoKit的绿灯熄灭，WiFi模组两个指示灯瞬间熄灭，直到指示灯开始交叉闪烁，这表示GoKit已经连上路由器，配置完成。

### **SoftAP配置入网**

由于**AirLink配置方式有一定的技术限制，GoKit支持另一种配置方式——SoftAP，**实现过程就是将GoKit上的WiFi模组切换到AP模式，手机直接与GoKit连接，并将可用的WiFi网络SSID和密码发送给GoKit，GoKit接收到配置信息后自动尝试连接路由器。具体步骤如下：

1.GoKit正常供电情况下，长按［KEY1］直到［RGB LED］亮红灯（Arduino底板［RGB LED］蓝色闪一下），表示GoKit已经初始化。而GoKit在初始状态下将自动进入“SoftAP”模式。

2.手机进入“系统设置”中的“WiFi设置”，找到“XPG-[GAgent](http://docs.gizwits.com/zh-cn/deviceDev/gagent_info.html)-XXXX”（XXXX是你的GoKit MAC地址后4位）并连接此WiFi网络，如需密码请输入：123456789 。(问了客服wifi名也可能是123456789，密码也是123456789)

3.打开“IoE Demo” App，此时App会自动进入SoftAP配置模式，选择或手动输入你附近的可用WiFi网络SSIS及密码，点击“确定”。

4.等待30秒到一分钟，当GoKit上WiFi模组的指示灯交叉闪烁时，表示配置完成。

### **Web Config配置模式**

Web Config是SoftAP配置模式的一种升级，解决了智能硬件配置上网对独立专用App的依赖问题。实现原理与SoftAP类似，但直接使用手机自带的浏览器即可配置。具体步骤如下：

1.GoKit正常供电情况下，长按［KEY1］直到［RGB LED］亮红灯（Arduino底板［RGB LED］蓝色闪一下），表示GoKit已经初始化，而GoKit在初始状态下将自动进入“SoftAP”模式。

2.手机进入“系统设置”中的“WiFi设置”，找到“XPG-GAgent-XXXX”（XXXX是你的GoKit MAC地址后4位）并连接此WiFi网络，如需密码请输入：123456789 。

3.打开手机浏览器，并在地址栏输入“10.10.100.254”即可访问GoKit配置页面，根据提示输入可用WiFi网络SSID及密码，点击配置。 4.等待30秒到一分钟，当GoKit上WiFi模组的指示灯交叉闪烁时，表示配置完成。