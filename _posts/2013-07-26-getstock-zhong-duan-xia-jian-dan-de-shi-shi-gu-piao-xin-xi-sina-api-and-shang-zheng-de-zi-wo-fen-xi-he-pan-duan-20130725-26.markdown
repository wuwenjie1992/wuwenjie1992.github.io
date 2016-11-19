---
layout: post
title: "getStock-终端下简单的实时股票信息(Sina API) &amp; 上证的自我分析和判断20130725-26"
date: 2013-07-26 12:35
comments: true
categories: [shell,terminal,java,Stock,share,K线,技术分析]
---
##getStock
* 终端下简单的实时股票信息
* 以Java为工具实现，当然以[SHELL][1]、Python等更容易实现
*  使用[Sina API][2] ，数据的可靠性由Sina决定
*  例如 http://hq.sinajs.cn/list=sh000001
<!-- more -->
* github:[getStock](https://github.com/wuwenjie1992/getStock)
* 缺点
    * 太简单，但“[小即美][3]”，信息少，没功能
    * 没有GUI，没有k线，没有。。。
* 效果图
    ![getStock效果图](/images/getStock_sh600015.png)


-------------
##上证的自我分析和判断20130725-26
* 图一
    * 说明：黄色的[回归通道][4]是我基本上在4月末5月初时画的
    * 就技术分析上说，大盘还未作出很明显的大幅反弹，仍是比较疲弱
    * 黄色的回归通道，下行趋势并未改变
    * 其走势也和基本面上宏观经济相一致
        * 中国[7月汇丰制造业PMI][5]初值为47.7，不仅低于50荣枯值，近一年以来谷底
        * [经济增长“底线”7%言论][6]，也是印证了经济趋缓、动力不足的现实
    * ![sh000001_20130725](/images/sh000001_20130725.png)
* 图二 实验性的算出回归通道压力线方程
    * 以近期高点2444为第0天，如图第107天即为20130725
    * 可得两个点(0,2444);(107,2045) 2045即0725最高价
    * 易得y=-3.729x+2444
    * 使用
        * 若大盘最高价连续数日突破该压力线，短期呈上升趋势
        * 可短期跟进，但应随时准备退出
        * 若大盘最低价触及支撑线，可考虑缓慢加仓（不可持有估值过高的股票）
    * 结论
        * 大盘或仍在通道内徘徊，但1917附近应有较大支撑，可静观其变
    * ![sh000001_20130725_2](/images/sh000001_20130725_2.png)

-------------
## 一张原油指数的技术分析图片
* 等了近两个月终于看到它有所回落
* 因为110美元基本上已经触及历史高点
* 1309合约也可以用回归通道表示
* 预测
    * 原油已转变趋势，近月会跌破99.667 即黄金分割线23.6%
    * 1310、1311合约价格也印证了这样的说法
    * 38.2%-50.0% 96.3-93.693 有一段时间的支撑
    * 谨慎行事，90美元是较好的价位
* ![CLU3_1309_20130725](/images/CLU3_1309_20130725.png)



  [1]: www.wuhuixin.tk/blog/2013/05/05/Crude-oil-index-grep-android-floating-window-Service/ "shell 实现原油指数抓取"
  [2]: www.xuech.net/simple/?t46.html "Sina股票数据接口"
  [3]: http://zh.wikipedia.org/zh-cn/Unix%E5%93%B2%E5%AD%A6 "UNIX哲学 壹"
  [4]: http://ta.mql4.com/cn/linestudies/linear_regression_channel "线性回归通道"
  [5]: http://www.ftchinese.com/story/001051634?full=y "解读李克强的“微刺激”"
  [6]: http://cn.wsj.com/gb/20130726/mkt143611.asp?source=UpFeature "精明投资者看空中国"
  
