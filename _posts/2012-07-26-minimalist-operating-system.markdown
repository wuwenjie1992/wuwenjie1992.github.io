---
layout: post
title: "【改】十分钟的极简操作系统"
author: "wuwenjie"
date: 2012-07-26 12:05
comments: true
categories: [nasm,virtualbox,转，改，apt-get]
---
1)准备 nasm ,VirtualBox

sudo apt-get install nasm

sudo apt-get install virtualbox

====================================

2)编译boot0.0.1.asm

-------------------------------------

%ifdef  _BOOT_DEBUG_
org  0100h        ; 调试状态, 做成 .COM 文件, 可调试
%else
org  07c00h        ; BIOS 将把 Boot Sector 加载到 0:7C00 处
%endif

mov    ax, cs
mov    ds, ax
mov    es, ax
call    DispStr        ; 调用显示字符串例程
jmp    $        ; 无限循环
DispStr:
mov    ax, BootMessage
mov    bp, ax        ; ES:BP = 串地址
mov    cx, 17        ; CX = 串长度
mov    ax, 01301h    ; AH = 13,  AL = 01h
mov    bx, 000ch    ; 页号为0(BH = 0) 黑底红字(BL = 0Ch,高亮)
mov    dl, 0
int    10h        ; int 10h
ret
BootMessage:        db    "Ohhhh,wuwenjie!!!"
times     510-($-$$)    db    0 ; 填充剩下的空间，使生成的二进制代码恰好为512字节
dw     0xaa55              ; 结束标志

-------------------------------------------------

2.2）利用nasn 编译

nasm boot0.0.1.asm -o boot0.0.1.bin
2.3）dd if=boot0.0.1.bin of=myos0.0.1.img bs=512 count=1

生成img文件

&nbsp;

==================================================

3）VirtualBox检验极简OS

步骤：

&nbsp;

新建--&gt;下一步--&gt;名称（myos0.0.1)other other/unknown

&nbsp;

--&gt;下一步--&gt;下一步--&gt;下一步--&gt;下一步--&gt;下一步--&gt;下一步

&nbsp;

--&gt;创建--&gt;创建--&gt;设置--&gt;存储---&gt;

&nbsp;

添加软盘控制器（启动顺序默认软驱开始）--&gt;

<a href="http://www.wuwenjie.tk/wp-content/uploads/2012/07/屏幕截图-2012年07月26日-14时55分47秒.png"><img class="alignleft  wp-image-556" title="添加软盘控制器" src="http://www.wuwenjie.tk/wp-content/uploads/2012/07/屏幕截图-2012年07月26日-14时55分47秒.png" alt="" width="408" height="352" /></a>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

添加虚拟软驱---&gt;选择磁盘--&gt;选择刚刚生成的myos0.0.1.img--&gt;

<a href="http://www.wuwenjie.tk/wp-content/uploads/2012/07/屏幕截图-2012年07月26日-14时57分43秒1.png"><img class="alignleft size-full wp-image-558" title="添加虚拟软驱" src="http://www.wuwenjie.tk/wp-content/uploads/2012/07/屏幕截图-2012年07月26日-14时57分43秒1.png" alt="" width="204" height="22" /></a>

&nbsp;

打开--&gt;确定---&gt;启动

<a href="http://www.wuwenjie.tk/wp-content/uploads/2012/07/屏幕截图-2012年07月26日-15时02分33秒.png"><img title="成功画面" src="http://www.wuwenjie.tk/wp-content/uploads/2012/07/屏幕截图-2012年07月26日-15时02分33秒.png" alt="" width="511" height="194" /></a>

&nbsp;

&nbsp;

====================================================

4）参考

1.[于渊]一个操作系统的实现

2.<a title="boot.asm" href="https://github.com/yyu/osfs01/blob/master/boot.asm" target="_blank">https://github.com/yyu/osfs01/blob/master/boot.asm</a>

3.<a title="还在建设中（2012.7.26）" href="http://osfromscratch.org" target="_blank">http://osfromscratch.org</a>

&nbsp;

&nbsp;
