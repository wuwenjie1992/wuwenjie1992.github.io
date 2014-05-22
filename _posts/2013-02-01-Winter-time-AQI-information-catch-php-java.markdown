---
layout: post
title: "冬天的这段时间+AQI信息的抓取（php+java)"
author: "wuwenjie"
date: 2013-02-01 11:46
comments: true
categories: []
---
2012年末和13年年初，

这段日子不好。

发生人生大变故。
<!-- more -->
&nbsp;

第一，大爹grandfa的过世，

亲人病故，让我看淡、看清人生，

也看轻了第二件事，

我的方向找到了方向，

而我失去了方向。

&nbsp;

无所谓，成长=磨练+打击；

人生起起落落，

我已经看到了，

自己的低谷底部。

充满希望还能前进。

------------------------------------------

<strong>AQI信息的抓取（php+java)</strong>

<strong>一、什么是AQI</strong>

<a title="空气污染指数" href="https://zh.wikipedia.org/wiki/%E7%A9%BA%E6%B0%A3%E6%B1%A1%E6%9F%93%E6%8C%87%E6%95%B8" target="_blank">AQI</a>,<a title="空气质量指数：baidu" href="http://baike.baidu.com/view/3251379.htm" target="_blank">空气质量指数</a>,

反映空气质量的指标。

（更多信息，点击链接）

其中，最为重要的是PM2.5，

之前，冬天前就有，

<a title="美国驻上海总领事馆空气质量监测站 " href="http://shanghai-ch.usembassy-china.org.cn/airmonitor.html" target="_blank">米国的PM2.5</a>的预报，

也不当一回事，但冬天到了，

雾霾情况就愈发严重，

我才刚意识到空气污染的现状。

在股市之中，

也开始炒起了“<a href="http://finance.ifeng.com/stock/roll/20130129/7617419.shtml" target="_blank">环保概念股</a>”。

相关链接<a href="http://finance.eastmoney.com/news/1353,20130201271557065.html" target="_blank"> 1</a> <a href="http://stock.eastmoney.com/news/1406,20130131271410936.html" target="_blank">2</a> <a href="http://stock.eastmoney.com/news/1406,20130130271079896.html" target="_blank">3</a> <a href="http://finance.eastmoney.com/news/1350,20130130271044553.html" target="_blank">4</a> <a href="http://www.semc.gov.cn/home/index.aspx" target="_blank">5</a>

&nbsp;

<strong>二、如何得到AQI</strong>

上海：<a href="http://www.semc.gov.cn/home/index.aspx" target="_blank">上海市环境监测中心</a>

<a href="https://twitter.com/CGShanghaiAir" target="_blank">CGShanghaiAir</a>

北京：<a href="https://twitter.com/BeijingAir" target="_blank">BeijingAir</a>

&nbsp;

<strong>三、代码</strong>

以上海环境监测中心的网页上的数据为原始数据资料，

利用php抓取有用信息，因为原始网页包含太多无用信息，

android客户端，在网络不通畅情况下，速度太慢，

获取信息及处理所需时间太长，导致体验下降。

&nbsp;

相关代码：<a title="github" href="https://github.com/wuwenjie1992/Xhullo/blob/master/src/wo/wocom/xwell/XA_AirQuality.java" target="_blank">XA_AirQuality.java</a>

相关php代码于最后的注释内

（format 过，排版有问题）

&nbsp;

<strong>四、效果</strong>

简单的代码，简单的效果。。。

<a href="http://www.wuwenjie.tk/2013/02/01/%e5%86%ac%e5%a4%a9%e7%9a%84%e8%bf%99%e6%ae%b5%e4%ba%8baoi_php_java/device-2013-02-01-193536/" rel="attachment wp-att-608"><img class="size-full wp-image-608" title="AQI效果1" src="http://www.wuwenjie.tk/wp-content/uploads/2013/02/device-2013-02-01-193536.png" alt="AQI效果1" width="320" height="480" /></a> AQI效果1

&nbsp;

<a href="http://www.wuwenjie.tk/2013/02/01/%e5%86%ac%e5%a4%a9%e7%9a%84%e8%bf%99%e6%ae%b5%e4%ba%8baoi_php_java/device-2013-02-01-193554/" rel="attachment wp-att-609"><img class="size-full wp-image-609" title="AQI效果2" src="http://www.wuwenjie.tk/wp-content/uploads/2013/02/device-2013-02-01-193554.png" alt="AQI效果2" width="320" height="480" /></a> AQI效果2

&nbsp;

&nbsp;
