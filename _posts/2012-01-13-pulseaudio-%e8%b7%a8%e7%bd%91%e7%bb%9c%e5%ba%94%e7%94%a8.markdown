---
layout: post
title: "PulseAudio 跨网络应用"
author: "wuwenjie"
date: 2012-01-13 04:23
comments: true
categories: [PulseAudio]
---
<h1>屋里有一台计算机是专门用来做报告的，为它配备了一台投影仪。在作报告的时候，有时想播放一些音频文件，但是这台计算机没有扬声器。为了便于问题的描述，可以将这台计算机称为“<strong>哑巴机</strong>”。除了这台哑巴机之外，其他的计算机都内置了音效挺好的扬声器，相应的，我们管这些带扬声器的机器称为“<strong>有声机</strong>”。由于我们比较缺钱，所以不想为哑巴机再配备一个外部音箱，只想找个办法使得哑巴机可以通过有声机的扬声器播放音频。使用 PulseAudio 可以很简单的解决这个问题。</h1>
<div id="article_body">
<div id="article_content">
<h2>将有声机作为服务器</h2>
打开 /etc/pulse/default.pa 文件，增加以下内容：
<pre>load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1;192.168.0.0/24 auth-anonymous=1</pre>
<code>192.168.0.0</code> 这个 IP 起始地址可根据具体的网络环境进行修改。

然后重启 pulseaudio 服务：
<pre>$ pulseaudio --kill
$ pulseaudio --start</pre>
<h2>将哑巴机作为客户端</h2>
在哑巴机上，只需将有声机的 IP 地址设为服务器地址，然后开启音频程序（这个音频程序支持 PulseAudio）即可，例如：
<pre>$ export PULSE_SERVER=192.168.0.7
$ rhythmbox foo.mp3</pre>
如果想将某个有声机设为哑巴机的默认服务器，可以修改哑巴机上的 /etc/pulse/client.pa 文件，增加以下内容：
<pre>default-server = 192.168.0.7</pre>
然后执行以下命令刷新一下 PulseAudio 服务器地址：
<pre>$ pax11publish -e -r</pre>
这样每次在哑巴机上打开音频程序之前就不需要显式设定服务器地址变量 <code>PULSE_SERVER</code> 了。
<h2>让 X Window 网络透明也具备音频传送能力</h2>
在上文内容的基础上，我们可以在有声机上通过 X Window 的 X Serve 端访问哑巴机（其 IP 地址假设是 <code>192.168.0.22</code>）上的 X Client 端，命令如下：
<pre>$ xhost +
$ ssh -X mute@192.168.0.22
$ rhythmbox foo.mp3</pre>
这样，就可以让哑巴机上的 rhythmbox 利用本地机的扬声器放出音频。

</div>
转载时，希望不要链接文中图片，另外请保留本文原始出处：

<a href="http://garfileo.is-programmer.com/">http://garfileo.is-programmer.com</a>

</div>