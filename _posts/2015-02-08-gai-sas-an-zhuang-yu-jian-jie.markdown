---
layout: post
title: "[改]SAS-安装与简介"
date: 2015-01-31 15:43
comments: true
categories: [Data mining,SAS]
---

## 前言
* SAS 是[数据挖掘][1]界的巨无霸,几十年的发展业务遍布全球各行各业。
* 其在官网上介绍的[客户案例][2],有招行（包括Credit Card）、工商、交通、广发等等。
* 它是“邪恶的”商业软件，那我为什么还要用它呢？
<!-- more -->
    * 使用简单，只要data步、proc步、options（选项）等即可，不需自己实现算法。
    * 方法全，基本上囊括了大部分计量统计、数据挖掘方面的方法。
* 问题：当你挖掘得深了以后，发现SAS会是你成长上的[桎梏][3]（zhì ɡù）。
    * 因为算法的实现都被包裹起来了，你只能做参数的改动，无法改进算法。
    * SAS的简单语法也给做复杂项目，带来了很多麻烦。
* 解决方案：以SAS作为入门级的工具 -> 进阶后可使用[R][4] -> 高级后使用编程语言。

## 下载
* google搜索 ： SAS.9.2多国语言版.iso site:pan.baidu.com
* 约3GB，3,188,455,424字节。
* 用不了google搜索？ 可以使用[gso][5],它是基于nodejs的开源google搜索代理服务。
* 可用列表 -> [点这里][6]

## 安装
* 下载后，解压SAS.9.2多国语言版.iso (md5sum:41b1b1098837d9a0cafb73fc8781055e)
* 搜索：SAS9.2多国语言版安装破解图解，[破解教程][7]。
* 重要步骤：
    * 将系统时间调整到2009年1月1日，适合于SID文件的时间，否则会被识别为过期。
    * 替换sashost.dll文件，在你的安装路径\SASFoundation\9.2下。
    * 破解完成后即可改回时间。
* 破解文件：SID文件、sashost.dll (md5sum:a82df493b9faeb7bd261728345e413d1)
* 下载 -> [点这里][8]

##简介

<iframe src ="/images/SAS教程.pdf" width="800" height="1000">
<p>你的浏览器不支持iframes！</p>
</iframe>

[1]:http://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98
[2]:http://www.sas.com/zh_cn/customers.html#--
[3]:http://zh.wikipedia.org/zh/%E6%A1%8E%E6%A2%8F
[4]:http://www.r-project.org/
[5]:https://github.com/lenbo-ma/gso
[6]:https://github.com/lenbo-ma/gso/wiki/%E5%8F%AF%E7%94%A8%E6%9C%8D%E5%8A%A1%E5%88%97%E8%A1%A8
[7]:http://wenku.baidu.com/view/f358faedba0d4a7302763ad4.html
[8]:/images/SAS9.2.zip


