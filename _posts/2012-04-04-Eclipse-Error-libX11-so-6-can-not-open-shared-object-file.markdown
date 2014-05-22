---
layout: post
title: "Eclipse 错误 libX11.so.6: 无法打开共享对象文件"
author: "wuwenjie"
date: 2012-04-04 07:54
comments: true
categories: [libX11.so.6, sudo apt-get -f install, 无法打开共享对象文件]
---
java.lang.UnsatisfiedLinkError:
/home/*******/libmawt.so        :
libX11.so.6: 无法打开共享对象文件: 没有那个文件或目录

使用LDD命令：查看包依赖
ldd /home/*******/libmawt.so

libX11.so.6     =&gt; not found
libXrender.so.1 =&gt; not found
libXtst.so.6    =&gt; not found
libXi.so.6      =&gt; not found
libjvm.so       =&gt; not found

<em><strong>其实只要用apt-get install</strong></em>

<em><strong>sudo apt-get -f install #-f = --fix-missing</strong></em>

<em><strong>以下一大堆废话</strong></em>

&nbsp;

&nbsp;

&nbsp;

http://packages.ubuntu.com 查找遗失的包

Search the contents of packages

packages that contain files whose names contain the keyword

----------------------------
libX11.so.6     =&gt; not found
----------------------------

1）Search   libx11

/usr/lib/x86_64-linux-gnu/libX11.so.6

其他与 libx11-6 有关的软件包

multiarch-support
Transitional package to ensure multiarch compatibility

libc6 (&gt;= 2.4)
Embedded GNU C Library: Shared libraries
同时作为一个虚包由这些包填实: libc6-udeb

libx11-data
X11 client-side library

libxcb1 (&gt;= 1.2)
X C Binding

libx11-6_1.4.4-2ubuntu1_i386.deb

下载好用 sudo dpkg -i ***.deb   命令

libx11-6:i386 依赖于 libxcb1 (&gt;= 1.2)；然而：
未安装软件包 libxcb1:i386。

2)Search libxcb1

http://packages.ubuntu.com/oneiric/libxcb1

libxcb1:i386 依赖于 libxdmcp6；然而：
未安装软件包 libxdmcp6:i386。

3)libxdmcp6

http://packages.ubuntu.com/oneiric/libxdmcp6

sudo dpkg -i libxdmcp6_1.1.0-3_i386.deb

4)安装libxcb1

sudo dpkg -i libxcb1_1.7-3_i386.deb

libxcb1:i386 依赖于 libxau6；然而：
未安装软件包 libxau6:i386。

5）安装libxau6

http://packages.ubuntu.com/oneiric/libxau6

sudo dpkg -i libxau6_1.0.6-3_i386.deb

6)安装libxcb1

sudo dpkg -i libxcb1_1.7-3_i386.deb

7)安装libX11.so
sudo dpkg -i libxau6_1.0.6-3_i386.deb

-------------------------------
libXrender.so.1 =&gt; not found
-------------------------------

1)search

/usr/lib/x86_64-linux-gnu/libXrender.so.1

http://packages.ubuntu.com/oneiric/libxrender1

2)安装libxrender1     libxrender1_0.9.6-2_i386.deb

sudo dpkg -i libxrender1_0.9.6-2_i386.deb

-----------------------------
libXtst.so.6    =&gt; not found
-----------------------------
1)search

/usr/lib/libXtst.so.6.1.0

http://packages.ubuntu.com/oneiric/libxtst6

sudo apt-get install libxext6

ia32-libs : 依赖: lib32z1 但是它将不会被安装
依赖: lib32stdc++6 但是它将不会被安装
依赖: lib32asound2 但是它将不会被安装
依赖: lib32ncurses5 但是它将不会被安装
依赖: lib32ffi6 但是它将不会被安装
依赖: lib32ncursesw5 但是它将不会被安装
推荐: ia32-libs-multiarch

sudo apt-get -f install
