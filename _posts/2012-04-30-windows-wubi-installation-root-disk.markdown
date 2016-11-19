---
layout: post
title: "windowns wubi安装中的root.disk文件"
author: "wuwenjie"
date: 2012-04-30 18:07
comments: true
categories: []
---
﻿如果出现了故障

可以用linux启动盘启动

把root.disk所在分区挂载

然后这样：

代码:
sudo mount -o loop [root.disk路径]  [需要挂载到的路径]

EX：sudo mount -o loop /media/软件/root.disk /mnt


root.disk文件其实就是ubuntu的文件系统

需要的文件没有丢失