---
title: " QT升级5.10后Android开发变动\t\t"
tags:
  - android
  - qt
url: 843.html
id: 843
categories:
  - Qt
date: 2017-12-30 22:52:16
---

今天卸载了5.9安装了5.10，安卓相关的jdk、ndk什么的都没删，全新装完以后，在Option-Device页面，自动识别到了以前配置的jdk、sdk、ndk，不需要重新配置。 然后编译以前的项目，不知道为什么下载了很多文件，会自动下载，耐心等待即可，大概第一个文件有六十多MB，下载速度60kb不到等了很久，然后又下载了一系列小文件，总时间不到半小时，第一次编译顺利通过，提示success。。。。就是找不到生成的apk在什么地方 回头仔细看Compile Output：

> Android package built successfully in 7.543 ms. -- File: D:/XXXXXXX/build-XXXXXX-Android\_for\_armeabi\_v7a\_GCC\_4\_9\_Qt\_5\_10\_0\_for\_Android_armv7-Release/android-build//build/outputs/apk/android-build-debug.apk

这个目录真的是越来越深了。。以前在build下就感觉相比于win平台文件多了一级，这回更好了，唯一好处是这个文件夹下面只有apk文件了