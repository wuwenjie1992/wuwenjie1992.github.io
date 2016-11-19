---
layout: post
title: "64位Ubuntu下WTK无法启动"
author: "wuwenjie"
date: 2012-02-15 14:06
comments: true
categories: [ubuntu,java,WTK,jdk]
---
在64位系统下启动J2ME的模拟器时会报错：

java.lang.UnsatisfiedLinkError:

/home/xxx/WTK2.5.2/bin/sublime.so:

/home/xxx/WTK2.5.2/bin/sublime.so:

wrong ELF class: ELFCLASS32

(Possible cause: architecture word width mismatch)

<a title="点击下载参考图片" href="http://www.21stp.com/cviudvinf.html" target="_blank">点击下载参考图片</a>

&nbsp;

提示架构的问题，解决方案是安装一个32位的jre或jdk，

如：jdk-7u3-linux-i586    可安装在，此目录一般为 /usr/lib/jvm/java....../bin/

sudo vi /bin/emulator

将里javapathtowtk修改为新安装的32位jre或jdk的bin目录下，

记得后面的斜线

&nbsp;

&nbsp;

&nbsp;

---------

http://hi.baidu.com/erdosjiang/

blog/item/4a8ee30907719ac363d986eb.html
