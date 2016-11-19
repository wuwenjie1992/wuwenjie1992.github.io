---
layout: post
title: "Hello_luence 本地文本查找引擎的一个实例 Lucene+nodejs 2"
date: 2013-10-06 22:09
comments: true
categories: [lucene,java,nodejs,shell]
---

##hello_luence 本地文本查找引擎的一个实例 lucene+nodejs 2

###接着[之前的文章](http://wuwenjie.tk/blog/2013/09/06/hello-luence-ben-di-wen-ben-cha-zhao-yin-qing-de-ge-shi-li-lucene-plus-nodejs/)
 * 为了更好地使用luence搜索，我使用了nodejs 简单的建构了一个http服务端
 * 我主要参考了以下两篇文章，来写出一个nodejs的服务端
<!-- more -->
  * [Node入门](http://www.nodebeginner.org/index-zh-cn.html)
  * [用NodeJS打造你的静态文件服务器](http://cnodejs.org/topic/4f16442ccae1f4aa27001071)
 * nodejs 事件驱动的编程方式，与传统的编程思维，线性方式不一样，是值得注意的
 * nodejs 有很好的[API](http://www.nodejs.org/api/)，适合于开发web程序，方便、小巧、轻快
 * 两个比较好的http服务器例子
  * [smallweb](https://github.com/leapon/smallweb) : A light-weight web server
  * [ping](https://github.com/JacksonTian/ping) : 基于Node的Web开发框架

###Show me the code
 * [`hello_luence`](https://github.com/wuwenjie1992/hello_luence)
 * 现在的样子
  * ![hello_lucene_2](/images/hello_lucene_2.png)
