---
layout: post
title: "mencoder转码"
author: "wuwenjie"
date: 2012-06-10 09:05
comments: true
categories: [shell，mplayer，mencoder]
---
mencoder [若干选项] 文件 [文件|URL|-] [-o 文件 | file://文件 | smb://[用户名:密码@]主机/文件路径]
mencoder [若干选项] 文件1 [该文件专用的选项] [文件2] [该文件专用的选项]

&nbsp;

[caption id="attachment_487" align="alignleft" width="205" caption="mplayer"]<a href="http://www.mplayerhq.hu/design7/news.html"><img class="size-full wp-image-487" title="mplayer" src="http://www.wuwenjie.tk/wp-content/uploads/2012/06/left.jpg" alt="mplayer" width="205" height="449" /></a>[/caption]

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

------------

实例 1：f4v---&gt;mp4

mencoder a.f4v -o a1.mp4 -oac mp3lame -lameopts cbr:br=128 -ovc x264 -x264encopts bitrate=440 -vf scale=480:320

&nbsp;

-------------

解释:

a.fv4是指输入的视频，要转换的视频。
-o a1.avi 中的“-o”是指你要输出视频，输出视频名为a1.avi
-oac 设置音频编码器。
mp3lame 设置音频编码器为mp3lame，就是mp3
-lameopts 设置mp3lamer的相关参数
cbr:br=128 设置音频的码率为128。96、112、128、192、256、320Kbps
-ovc 设置视频编码器。
x264 设置视频编码器为x264。

-x264encopts 设置x264的参数。
bitrate=440 设置x264的视频的码率为440。
-vf scale=480:320  设置视频的宽为480，高为320.( -vf scale=448:-3) -3是让mencoder来设置最佳宽度。

&nbsp;

&nbsp;

----------

实例 2： flv---&gt;mp3

mencoder b.flv -o b.mp3 -ovc frameno -oac mp3lame -lameopts cbr:br=320 -of rawaudio -ss 1:30 -endpos 2:45

-------------

解释:

b.flv 输入源文件
-o b.mp3 输出的目标文件名称
-ovc frameno 不处理视频编码
-oac mp3lame 输出的音频编码格式为mp3
-lameopts cbr:br=320 音频附件选项，cbr(常量比特率)编码格式，音频码流为320bps
-of rawaudio 输出文件为原始音频流
-ss 1:30 音频截取的起始时间(表示从b.flv的第1分30秒开始截取)
-endpos 2:45 预截取音频的长度(表示预截取的音频长度是2分45秒，可计算出其结束时间4:15)(-endpos 10mb 编码数据量为10M)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

--------
注：

1）
-of &lt;格式&gt;（用于 BETA 测试！）
编码为指定的容器格式（默认值：AVI）
-of lavf
使用 libavformat 流合并器编码

EX：mencoder a.f4v -o a1.mp4 -oac mp3lame -lameopts cbr:br=128 -ovc x264 -x264encopts bitrate=440 -vf scale=480:320 -of lavf

2）安装<em> sudo apt</em>-get <em>install</em> mencoder

----

参考：

1）man mplayer        man mencoder

2)http://baike.baidu.com/view/3353694.htm

3)http://www.mplayerhq.hu/DOCS/HTML/zh_CN/mencoder.html

4)http://www.mplayerhq.hu/DOCS/HTML/zh_CN/index.html

5)http://hi.baidu.com/hailoong/blog/item/545cfa17baefe4084b90a77c.html

&nbsp;

&nbsp;

&nbsp;

&nbsp;
