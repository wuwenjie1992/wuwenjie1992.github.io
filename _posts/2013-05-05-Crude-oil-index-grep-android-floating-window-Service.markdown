---
layout: post
title: "原油指数抓取+android浮动窗口Service"
author: "wuwenjie"
date: 2013-05-05 10:02
comments: true
categories: [android, awk, Finance, grep, shell]
---
<h1>1.原油指数抓取</h1>
1.0 背景

ICBC工商银行推出的<a title="账户原油" href="http://www.icbc.com.cn/ICBC/%E9%87%91%E8%9E%8D%E5%B8%82%E5%9C%BA%E4%B8%93%E5%8C%BA/%E4%BA%A7%E5%93%81%E6%9C%8D%E5%8A%A1/%E6%8A%95%E8%B5%84%E4%BA%A4%E6%98%93%E7%B1%BB%E4%BA%A7%E5%93%81/%E8%B4%A6%E6%88%B7%E5%8E%9F%E6%B2%B9/default.htm" target="_blank">账户原油</a>，
<!-- more -->
以人民币交易是按照

<span id="MyFreeTemplateUserControl"><a title="NYMEX" href="http://zh.wikipedia.org/wiki/%E7%B4%90%E7%B4%84%E5%95%86%E5%93%81%E6%9C%9F%E8%B2%A8%E4%BA%A4%E6%98%93%E6%89%80" target="_blank">纽约商业交易所</a>（NYMEX）</span>

<span id="MyFreeTemplateUserControl"><a title="西得克萨斯中间基原油" href="http://zh.wikipedia.org/wiki/%E8%A5%BF%E5%BE%97%E5%85%8B%E8%90%A8%E6%96%AF%E4%B8%AD%E9%97%B4%E5%9F%BA%E5%8E%9F%E6%B2%B9" target="_blank">西德克萨斯轻质低硫原油</a>期货合约（WTI）价格报价，</span>

<span id="MyFreeTemplateUserControl">以及账户国际原油参考洲际交易所（ICE）</span>

<span id="MyFreeTemplateUserControl"><a title="搜索/北海布伦特原油" href="http://zh.wikipedia.org/wiki/Special:%E6%90%9C%E7%B4%A2/%E5%8C%97%E6%B5%B7%E5%B8%83%E4%BC%A6%E7%89%B9%E5%8E%9F%E6%B2%B9" target="_blank">布伦特原油</a>期货合约（Brent）价格报价</span>

<span id="MyFreeTemplateUserControl">和综合考虑人民币与美元汇率报价的。</span>

1.1 SHELL实现

&nbsp;

```bash
#!/bin/sh
#
# 抓取 wap.eastmoney.com 原油指数

#最新价
latestprice="";
#涨跌值
Pricevalue="";
#涨跌幅
risingAndfalling="";
#今开盘
openingQuotation="";
#最高价
ceilingprice="";
#最低价
bottomprice="";
#昨收盘
closingQuotation="";
#交易时间
exchangehour="";
# 分时图URL
timeLine="";
# K线图
KChart="";

#获得html
wget -O conc.html -q --user-agent="Mobile Safari/534.30" --no-cookies \
--referer="http://wap.eastmoney.com/Futures.aspx" \
http://wap.eastmoney.com/FuturesInfo.aspx?c=CONC

# 最新价：.{7}(.{4,5})
latestprice="`grep -o '最新价：.\{7\}\(.\{4,5\}\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"
#gsub(r,s,t) 在字符串t中，用s替换和正则r匹配所有字符串 返回替换的个数。如果没有给出t，缺省为$0

# 涨跌值：.{45}(.{3,5}[0-9])
Pricevalue="`grep -o '涨跌值：.\{45\}\(.\{3,5\}[0-9]\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 涨跌幅：.{45}(.{4,5}%)
risingAndfalling="`grep -o '涨跌幅：.\{45\}\(.\{4,5\}%\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 今开盘：.{7}(.{3,5}[0-9])
openingQuotation="`grep -o '今开盘：.\{7\}\(.\{3,5\}[0-9]\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 最高价：.{7}(.{4,5}[0-9])
ceilingprice="`grep -o '最高价：.\{7\}\(.\{4,5\}[0-9]\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 最低价：.{7}(.{4,5}[0-9])
bottomprice="`grep -o '最低价：.\{7\}\(.\{4,5\}[0-9]\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 昨收盘：.{7}(.{4,5}[0-9])
closingQuotation="`grep -o '昨收盘：.\{7\}\(.\{4,5\}[0-9]\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 交易时间：.{7}(.{19})
exchangehour="`grep -o '交易时间：.\{7\}\(.\{19\}\)' conc.html | awk 'gsub(/&lt;[^&gt;]*&gt;/,"")'`"

# 分时图.*(ht.*)K
timeLine="`grep -o '分时图.*\(ht.*\)K' conc.html|\
grep -o '\(http.*0\)'|awk 'gsub(/amp;/,"")'`"

#获得分时图
wget -O timeline.png -q --user-agent="Mobile Safari/534.30" --no-cookies \
--referer="http://wap.eastmoney.com/Futures.aspx" $timeLine

#获得k线图 K线图.*(ht.*C0)
KChart="`grep -o 'K线图.*\(ht.*C0\)' conc.html|\
grep -o '\(http.*0\)'|awk 'gsub(/amp;/,"")'`";

wget -O KChart.png -q --user-agent="Mobile Safari/534.30" --no-cookies \
--referer="http://wap.eastmoney.com/Futures.aspx" $KChart

echo $latestprice
echo $Pricevalue
echo $risingAndfalling
echo $openingQuotation
echo $ceilingprice
echo $bottomprice
echo $closingQuotation
echo $exchangehour
#echo $timeLine
#echo $KChart

tPicPath="`pwd`""/timeline.png";
KPicPath="`pwd`""/KChart.png";

notify-send -t 20000 -i $tPicPath  "$latestprice $Pricevalue" " $risingAndfalling $openingQuotation \
$ceilingpric $bottomprice $closingQuotation $exchangehour"
```
注：grep 的正则表达式也许不准(在网页的改变下）

1.2 效果

<img alt="效果" src="http://i.troll.ws/c914df5d.png" width="499" height="103" />

more:
<a href="http://www.icbc.com.cn/ICBC/%E9%87%91%E8%9E%8D%E5%B8%82%E5%9C%BA%E4%B8%93%E5%8C%BA/%E4%BA%A7%E5%93%81%E6%9C%8D%E5%8A%A1/%E6%8A%95%E8%B5%84%E4%BA%A4%E6%98%93%E7%B1%BB%E4%BA%A7%E5%93%81/%E8%B4%A6%E6%88%B7%E5%8E%9F%E6%B2%B9/default.htm" target="_blank">账户原油</a>
<a href="http://www.icbc.com.cn/icbc/%E9%87%91%E8%9E%8D%E5%B8%82%E5%9C%BA%E4%B8%93%E5%8C%BA/%E9%87%91%E8%9E%8D%E5%B8%82%E5%9C%BA%E5%AD%A6%E8%8B%91/%E7%83%AD%E7%82%B9%E9%97%AE%E7%AD%94/%E4%B8%AD%E5%9B%BD%E5%B7%A5%E5%95%86%E9%93%B6%E8%A1%8C%E8%B4%A6%E6%88%B7%E5%8E%9F%E6%B2%B9%E4%B8%9A%E5%8A%A1%E7%9F%A5%E8%AF%86%E9%97%AE%E7%AD%941.htm" target="_blank">中国工商银行账户原油业务知识问答</a>
<strong>2.android浮动窗口Service</strong>

<a title="FloatView.java" href="https://github.com/wuwenjie1992/Xhullo/blob/master/src/wo/wocom/xwell/view/FloatView.java" target="_blank">FloatView.java</a>

<a title="SV_FloateViewService" href="https://github.com/wuwenjie1992/Xhullo/blob/master/src/wo/wocom/xwell/service/SV_FloateViewService.java" target="_blank">SV_FloateViewService.java</a>

```java
ComponentName component = new ComponentName
(this,wo.wocom.xwell.service.SV_FloateViewService.class);</code>

// 组件名称，intent会根据component

// name启动一个组件（activity,service,contentProvider）

Intent mIntent01 = new Intent();

mIntent01.setComponent(component);

startService(mIntent01);
```

效果：

<img alt="浮动窗口" src="http://i.troll.ws/ab5bef34.png" width="320" height="480" />

注：该版除屏幕外其他按键不能工作，

原因LayoutParams相关参数的设置（可能导致）

LayoutParams.FLAG_WATCH_OUTSIDE_TOUCH

&nbsp;
-----

更新：

添加WindowManager.LayoutParams.flags的设置LayoutParams.FLAG_NOT_FOCUSABLE

//FLAG_NOT_FOCUSABLE Constant Value: 8 (0x00000008)不聚焦 可使实体按键反应//Window flag: this window won't ever get key input focus,
//so the user can not send key or other button events to it.
