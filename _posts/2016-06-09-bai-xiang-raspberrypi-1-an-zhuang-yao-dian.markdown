---
layout: post
title: "白相Raspberrypi-1-安装要点"
date: 2016-04-30 18:49
comments: true
categories: [raspberrypi,树莓派,hardware]
---

###前言
* 随着云、物联网、编程教育等事物的兴起，树莓派（raspberrypi）作为人们实现想法、控制硬件的玩具越来越火。
* 由于无聊，也想试着玩玩树莓派，就买了raspi 2b、传感器、面包版、杜邦线等。

<!-- more -->

###硬件准备
* 树莓派一个
* 电源一个 microusb
* SD卡一个
* 网线一根
* 读卡器一个（备用）

###硬件准备注意
* 树莓派不是支持所有SD卡的，->[参考列表][1]
* 我购买的是SanDisk Ultra microSDHC UHS-1 16G，如下图：
* ![SanDisk](/images/SanDiskmicroSDHC.png)

###系统安装
* 推荐raspbian，基于debain的树莓派发行版
* 下载，https://www.raspberrypi.org/downloads/raspbian/
* 安装教程 [->linux][2]
 * 下载raspbian镜像，并解压
 * PC插上SD卡，可以进行分区
 * 使用dd命令写入SD卡，dcfldd可视化的dd

```text
dcfldd bs=4m if=2016-02-26-raspbian-jessie.img of=/dev/sdf
```

###硬件安装
* 树莓派上插好SD卡
* 插上网线，连接到路由器
* 插上电源，通电
* 树莓派启动，act绿灯闪烁，pwr红灯点亮

###连接与控制
* 树莓派IP可在路由器内查到
* ssh命令，默认用户pi，密码raspberrypi

```text
ssh pi@192.168.1.106
```

* 使用vnc可视化连接
* pi上：sudo apt-get install tightvncserver
* pc上：sudo apt-get install xtightvncviewer
* 参考->[教程][3]

###OK
* 树莓派之旅正是开始！
* ![raspi](/images/raspi-1.png)

[1]:http://elinux.org/RPi_SD_cards
[2]:https://www.raspberrypi.org/documentation/installation/installing-images/linux.md
[3]:https://www.raspberrypi.com.tw/586/setting-up-vnc/