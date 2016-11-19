---
layout: post
title: "三月&&AssaultCube-跨平台的FPS游戏-Linux平台上的类CS游戏"
author: "wuwenjie"
date: 2013-03-23 15:10
comments: true
categories: []
---
三月，开学几周了。

新学期还是一样，

但皑皑的樱花，

还是依旧灿烂。
<!-- more -->
命运中总有失去。

No matter how good things are lost one day.

And then have forgotten the deep memory of the day.

-------------------

天高淡微雲

月蟾星宿隕

破曉聞靈駒

騏驥兮驎驎

錦衾若冰綃

身骸草覆絞

凡塵水鏡花

天涯潮星寡

<a href="http://www.facebook.com/photo.php?fbid=4218206386156&amp;set=oa.413474595379710&amp;type=1&amp;relevant_count=1&amp;ref=nf"><img class="size-full wp-image-623" alt="破曉的片刻" src="http://www.wuwenjie.tk/wp-content/uploads/2013/03/47166_4218206386156_829464416_n.jpg" width="762" height="505" /></a>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

============================================================

&nbsp;


<h1><strong>A. </strong>AssaultCube</h1>
--跨平台的FPS游戏-Linux平台上的类CS游戏，

寒假无聊时候玩过，不错但是还有缺点。

<img alt="wiki上的AssaultCube截图" src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/82/AssaultCube_screenshot.png/800px-AssaultCube_screenshot.png" width="800" height="473" />

&nbsp;

<b>AssaultCube</b>(<a title="wiki" href="https://zh.wikipedia.org/wiki/AssaultCube" target="_blank">wiki</a>)

是一款开源的“第一人称射击游戏 ”（First Person Shooter）

官网：<a title="assault.cubers" href="http://assault.cubers.net/" target="_blank">http://assault.cubers.net/</a>

下载：<a title="download" href="http://assault.cubers.net/download.html" target="_blank">http://assault.cubers.net/download.html</a>

&nbsp;

<strong>B. 注意【官网的提示】</strong>

<strong>1)</strong>

<strong></strong>For installation guidance, read the getting started guide in the AssaultCube documentation.
Linux users should take special note that they will need these additional libraries installed:
<ul>
	<li>SDL</li>
	<li>SDL_image</li>
	<li>zlib</li>
	<li>libogg</li>
	<li>libvorbis</li>
	<li>OpenAL Soft</li>
</ul>
For RedHat based systems, type this at the command line:
sudo yum install SDL SDL_image zlib libogg libvorbis openal-soft
For Debian based systems, type this at the command line:
sudo apt-get install libsdl1.2debian-all libsdl-image1.2 zlib1g libogg0 libvorbis0a libopenal1

<strong> 2)----以上主要意思是linux也许会缺少几个库。</strong>

需要安装一下：

RedHat类：sudo yum install SDL SDL_image zlib libogg libvorbis openal-soft

Debian类：sudo apt-get install libsdl1.2debian-all libsdl-image1.2 zlib1g libogg0 libvorbis0a libopenal1

<strong>3)我的操作：</strong>

1. 我不想装这么多东西，搜一下SDL

<a title="SDL" href="https://zh.wikipedia.org/wiki/SDL" target="_blank">SDL</a>(时常被比较为‘跨平台的<a title="DirectX" href="https://zh.wikipedia.org/wiki/DirectX">DirectX</a>’)

<strong><em>apt-cache search SDL |grep lib</em></strong>

#apt-cache search &lt;pattern&gt;

#搜索满足 &lt;pattern&gt; 的包裹和描述.

2.结果太多，挑个

<em><strong>sudo apt-get install libsdl-image1.2</strong></em>

#结果太多，我只装了这个包

3.不行的话就全装

<em><strong>sudo apt-get install libsdl1.2debian-all libsdl-image1.2 zlib1g libogg0 libvorbis0a libopenal1</strong></em>

&nbsp;

<strong>C. 下载，解压，运行</strong>

linux版链接到了<a title="v1.1.0.4" href="http://sourceforge.net/projects/actiongame/files/AssaultCube%20Version%201.1.0.4/AssaultCube_v1.1.0.4.tar.bz2/download" target="_blank">sourseforge下载</a>

解压，运行 assaultcube.sh 就行了

截图：

<a href="http://www.wuwenjie.tk/wp-content/uploads/2013/03/屏幕截图-2013年03月23日-21时13分13秒.png"><img class="size-full wp-image-621" alt="AssaultCube远行示意图" src="http://www.wuwenjie.tk/wp-content/uploads/2013/03/屏幕截图-2013年03月23日-21时13分13秒.png" width="717" height="323" /></a>

<a href="http://www.wuwenjie.tk/wp-content/uploads/2013/03/20130212_06.46.26_ac_depot_BTDM.jpg"><img class="size-full wp-image-622" alt="AssaultCube游戏画面" src="http://www.wuwenjie.tk/wp-content/uploads/2013/03/20130212_06.46.26_ac_depot_BTDM.jpg" width="700" height="525" /></a>

<a href="http://yfdisk.com/file/common/4441d847/" target="_blank">更多图片 </a>

&nbsp;

<strong>D. MORE</strong>

https://zh.wikipedia.org/wiki/AssaultCube

http://assault.cubers.net/

https://zh.wikipedia.org/wiki/SDL

http://www.gamedev.net/topic/313047-apt-get-install-sdl-libs-on-linux/

http://page2.yunfile.com/file/common/4441d847/

http://down.tech.sina.com.cn/content/42971.html
