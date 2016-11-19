---
layout: post
title: "改进的超买超卖指标w-power"
date: 2014-08-31 20:35
comments: true
categories: [Stock,share,技术分析,指标]
---
##前言
* 在金融交易活动中，投机者多数时候以获取短期[超额收益][1]为目的
* 交易中，对时机的准确把握是及其关键的，决定了投资中短期的盈利
* 许多工具有助于时机的把握，比如[技术分析][2]中的[K线][2]、图形线形、道氏理论、波浪理论等等
* 而技术指标也多如牛毛，比如MA,MACD,KDJ,RSI,BIAS,BOLL,PSY,BBI等等
<!--more-->
##w-power的诞生
* 命名
 * w意为wuwenjie
 * power意为有力量的指标，因为指标将成交量糅合进价格指标
* 设计初衷及灵感
 * 在没有完善的市场认识和强大的基本面分析的支撑下，个人投资者是及其盲目的
 * 很有可能成为一个[噪音交易者][4]，对市场的运行从而产生[空中阁楼][5]的幻觉
 * 而股市名言：“股市中什么都可以骗人，唯有量是真实的”，是指标设计的灵感
 * 大多数的指标只考量价格，而鲜有将量价同时考虑，初衷是为了增强指标的可信度。
 
##w-power的公式

```text
VO:=MA(VOL,N);
MAGIC_VOL:=MA(VO/LLV(VO,M),M);
C_BBI:=(MA(CLOSE,3)+MA(CLOSE,6)+MA(CLOSE,12))/3;
H_BBI:=(MA(HIGH,3)+MA(HIGH,6)+MA(HIGH,12))/3;
L_BBI:=(MA(LOW,3)+MA(LOW,6)+MA(LOW,12))/3;

MAGIC_CP_BBI:(C_BBI-LLV(L_BBI,M))/(HHV(H_BBI,M)-LLV(L_BBI,M));

POWER:MAGIC_CP_BBI*MAGIC_VOL;
POWER_BBI:=(MA(POWER,3)+MA(POWER,6)+MA(POWER,12)+MA(POWER,24))/4;

POWER_BBI_AD:POWER_BBI+1.809*STD(POWER_BBI,M);
POWER_BBI_DE:POWER_BBI-2.191*STD(POWER_BBI,M);

DRAWTEXT(CROSS(POWER,POWER_BBI_AD),POWER,'B'),COLORGREEN,LINETHICK3;
DRAWTEXT(CROSS(POWER_BBI_AD,POWER),POWER,'S'),COLORRED,LINETHICK3;

HI:FINDHIGH(POWER,0,R,1);
LO:FINDLOW(POWER,0,R,1);
```
* 参数 N 15 M 3 R 30

* 解释

```text
VO赋值:成交量(手)的N日简单移动平均
MAGIC_VOL赋值:VO/M日内VO的最低值的M日简单移动平均
C_BBI赋值:(收盘价的3日简单移动平均+收盘价的6日简单移动平均+收盘价的12日简单移动平均)/3
H_BBI赋值:(最高价的3日简单移动平均+最高价的6日简单移动平均+最高价的12日简单移动平均)/3
L_BBI赋值:(最低价的3日简单移动平均+最低价的6日简单移动平均+最低价的12日简单移动平均)/3
输出MAGIC_CP_BBI:(C_BBI-M日内L_BBI的最低值)/(M日内H_BBI的最高值-M日内L_BBI的最低值)
输出POWER:MAGIC_CP_BBI*MAGIC_VOL
POWER_BBI赋值:(POWER的3日简单移动平均+POWER的6日简单移动平均+POWER的12日简单移动平均+POWER的24日简单移动平均)/4
输出POWER_BBI_AD:POWER_BBI+1.809*POWER_BBI的M日估算标准差
输出POWER_BBI_DE:POWER_BBI-2.191*POWER_BBI的M日估算标准差
当满足条件POWER上穿POWER_BBI_AD时,在POWER位置书写文字,画绿色,线宽为3
当满足条件POWER_BBI_AD上穿POWER时,在POWER位置书写文字,画红色,线宽为3
输出HI:POWER在0日前的R天内第1个最高价
输出LO:POWER在0日前的R天内第1个最低价
```

* 用法

```text
1.与KDJ指标类似，指标太高，可能价格被高估；指标太低可能被低估
2.BS点只是作为参考，近几个周期如果频繁出现BS点，可能说明价格正在进行一定区间的震荡整理，行情将会在之后出现
3.建议使用的方法，对指标图形作趋势线，分析较为准确。
3.2 以射线连接多个周期的波峰，作为压力线，指标运行到压力线，可能承压下行。支撑线亦如此。
4.最高线HI，与最低线LO两者之间的关系，也可以作为判断价格走势的标准
4.2 HI不变时，LO下降，价格快速下降，寻找短期底部
4.3 LO不变时，Hi上升，价格快速上升，冲击短期高点
4.4 依照以上规律，可以找出很多相似的规律
```

##w-power的效果
* ![silver-w-powre1](/images/silver20140805.png)
* ![silver-w-power2](/images/silver20140817.png)
* ![silver-w-power3](/images/silver20140830.png)

[1]:http://zh.wikipedia.org/wiki/%E8%A9%B9%E6%A3%AE%E9%98%BF%E5%B0%94%E6%B3%95
[2]:http://zh.wikipedia.org/wiki/%E6%8A%80%E6%9C%AF%E5%88%86%E6%9E%90
[3]:http://wuwenjie.tk/blog/categories/kxian/
[4]:http://wiki.mbalib.com/wiki/%E5%99%AA%E5%A3%B0%E4%BA%A4%E6%98%93%E8%80%85
[5]:http://baike.baidu.com/view/56921.htm