---
layout: post
title: "FLASH MP3播放器--DewPlayer"
author: "wuwenjie"
date: 2011-06-10 16:01
comments: true
categories: []
---
转载于情留蚊子博客http://blog.sina.com.cn/wenzi
 
以前介绍过一款FLASH MP3单播放器，这次要介绍另一款播放器--DewPlayer，也是一种FLASH MP3播放器，样式简洁小巧，在一些论坛比较流行。它的官网提供了三种样式：
 
Version normale(标准型) [200x20] : FLASH <wbr>MP3播放器--DewPlayer[图例]
 
<embed src=http://haobeizhan.go3.icpcn.com/flashplayer/dewplayer.swf width=200 height=20 type=application/x-shockwave-flash quality="high" wmode="transparent" flashvars="son=MP3歌曲地址&autoplay=0&autoreplay=1&showtime=1&volume=100"></embed>
Version mini(迷你型) [150x20] ：FLASH <wbr>MP3播放器--DewPlayer[图例]
 
<embed src=http://haobeizhan.go3.icpcn.com/flashplayer/dewplayer-mini.swf width=150 height=20 type=application/x-shockwave-flash quality="high" wmode="transparent" flashvars="son=MP3歌曲地址&autoplay=0&autoreplay=1&showtime=1&volume=100"></embed> Version multi(扩展型) [240x20] : FLASH <wbr>MP3播放器--DewPlayer[图例]
 
<embed src=http://haobeizhan.go3.icpcn.com/flashplayer/dewplayer-multi.swf width=240 height=20 type=application/x-shockwave-flash quality="high" wmode="transparent" flashvars="son=MP3歌曲地址&autoplay=0&autoreplay=1&showtime=1&volume=100"></embed> 参数说明：
 
1、autoplay=0 表示不自动播放，如果把 0 改为 1 则表示自动播放。
2、autoreplay=1 表示循环播放，如果把 1 改为 0 则表示不循环播放。
3、showtime=1 表示时间显示为分:秒格式，如果去掉 &showtime=1 则时间显示为 秒格式。
4、volume=100 表示播放器音量值，其设定值为 0～100，播放器中无音量按钮。
5、wmode="transparent" 表示播放器背景颜色为透明(建议为设置为透明)，如果把它去掉而加上 bgcolor="颜色代码" 则可以设置播放器的背景颜色，颜色代码如：白色：#ffffff ，黑色：#000000 ，红色：#ff0000 等十六进制颜色代码。
 
注意事项：
 
1、歌曲只能是MP3格式的，不支持其他任何格式。
2、歌曲地址后面各参数间由& 号隔开，注意此符号。
3、autoplay 和 autoreplay 参数如若去掉，其默认值为 0 。
4、showtime 参数如若去掉，其默认值为秒格式。
5、volume 参数如若去掉，其默认值为 100 。
与之前所介绍的单播放器不同的是这些播放器都可以实现多歌曲连放功能，方法很简单，只要在各歌曲地址之间加个“ | ”符号即可，不过歌曲好像只能按所添加的地址顺序播放，官网上定制播放器代码的时候有提供 random 随机播放参数，不过经过我试验这个参数没用，歌曲不能随机播放，但是能循环播放。以扩展型为例，如果要实现多歌曲连放功能建议最好使用扩张型播放器，因为它有上下曲按钮，而其他播放器没有，代码如下：
 
<EMBED src=http://haobeizhan.go3.icpcn.com/flashplayer/dewplayer-multi.swf width=240 height=20 type=application/x-shockwave-flash quality="high" wmode="transparent" flashvars="son=MP3歌曲地址1|MP3歌曲地址2|MP3歌曲地址3&autoplay=0&autoreplay=1&showtime=1&volume=100"></EMBED>
 
标准型，迷你型和下面要介绍的标准型优化版也是相同的道理。 另外再给大家提供两种官网上没有的仿DewPlayer播放器：
 
1、标准型优化版 [240x20] ：[实例]
 
<EMBED src=http://haobeizhan.go3.icpcn.com/flashplayer/wenzi_player_2.swf width=300 height=20 type=application/x-shockwave-flash flashvars="son=MP3歌曲地址&autoplay=0&autoreplay=1&showtime=1&volume=100" wmode="transparent" quality="high"></EMBED>
 
各参数同上所述。 2、标准型改进版 [300x25] ：[实例]
 
<EMBED src=http://haobeizhan.go3.icpcn.com/flashplayer/wenzi_player_1.swf width=300 height=25 type=application/x-shockwave-flash quality="high" wmode="transparent" flashvars="sname=歌曲信息&song=MP3歌曲地址&autoplay=0&time=1&vol=1"></EMBED>
 
autoplay,time,vol分别代表显示自动播放、播放时间、声音控制，默认为 0 不显示；不支持多曲连放功能；无循环播放功能；点击歌曲信息即可下载所播歌曲；歌曲信息可以填：歌手--歌曲名字 等信息，可根据自己需要