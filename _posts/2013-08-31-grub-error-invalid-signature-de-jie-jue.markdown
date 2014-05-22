---
layout: post
title: "GRUB error : invalid signature 的解决"
date: 2013-08-31 21:28
comments: true
categories: [grub,error,df,fdisk,blkid]
---
##GRUB error : invalid signature 的解决
之前，有一次更新了内核还是其他什么东西，  
想进入win7，玩玩[Total War: Shogun 2](http://zh.wikipedia.org/wiki/%E5%85%A8%E8%BB%8D%E7%A0%B4%E6%95%B5%EF%BC%9A%E5%B9%95%E5%BA%9C%E5%B0%87%E8%BB%8D2)  
但是在选取win7选项之后，给我返回了
###error: invalid signature
<!-- more -->
在进入[Xubuntu](http://xubuntu.org/)后，我就开始找解决方案  

发现只要修改一下/boot/grub/grub.cfg就可以了
```sh
sudo gedit /boot/grub/grub.cfg
```
将原来的“Windows 7...”替换为以下就完成重要一步了
```sh
menuentry "Windows 7 (loader) (on /dev/sda1)" --class windows --class os {
	insmod part_msdos
	insmod ntfs
	set root='(hd0,msdos1)'
	search --no-floppy --fs-uuid --set=root AAAAAAAAAA
	chainloader +1
}
```
###NOTICE
其中 AAAAAAAA 为Filesystem UUID，使用以下命令替换对应的UUID
```sh
blkid|grep sda1
```
blkid命令是‘定位/打印块设备属性’的作用
###最后
```sh
sudo update-grub
```
打印出
```
Generating grub.cfg ...
Found background image: /home/grub.png
Found linux image: /boot/vmlinuz-3.2.0-52-generic
Found initrd image: /boot/initrd.img-3.2.0-52-generic
Found memtest86+ image: /boot/memtest86+.bin
Found Windows 7 (loader) on /dev/sda1
done
```
就完成了，可以去玩Shogun2了

##MORE
 * [SOLVED Grub 2 gives Error: Invalid Signature](https://bbs.archlinux.org/viewtopic.php?id=153988)
 * [Ubuntu和WIN7双系统解决方案](http://blog.missyi.com/page_468.html)

