---
layout: post
title: "hello_luence 本地文本查找引擎的一个实例 lucene+nodejs"
date: 2013-09-06 21:11
comments: true
categories: [lucene,java,nodejs,shell]
---

##hello_luence 本地文本查找引擎的一个实例 lucene+nodejs

###开始
* 为什么会有它
    * 我有的时候会想查一些书、文章当中的字，看看这个字到底是怎么用的。
    * 在网上找到一些html版的书的网站，当用wget抓下来之后，花了很长时间，内容很多，文件很多。
    * 当然在linux下，有很好的文本查找、处理的工具，比如使用以下命令。
        * `find ./* | xargs grep OpenJDK`
        * 作用：在当前目录下，找出其中含有`OpenJDK`的文本。
        * 解释：`find ./*` 列出当前目录下所有文件。
            * `|`管道，将前面的`标准输出`导入到后面的`标准输入`。
            * [`xargs`][1] ,将`标准输入`分块给后面的命令，将参数列表转换成小块分段传递给其他命令。
            * `grep OpenJDK` ,[grep][2]匹配正则表达式的文本进行搜索，输出匹配或不匹配的行或文本。
    * 虽然方法简单但每次每个历遍，东西太多时间太长。
    * 有个[lucene项目][3],基于Java的全文索引工具包,虽不是搜索引擎，但开源搜索[Solr][4]、[Nutch][5]均基于它。
	<!-- more -->
### 尝试使用lucene
   * 暑假之前借了本 [Lucene IN ACTION][6]中文版,其实书已经很老了，但作为参考资料还是有作用的。
   * 在下载`Apache Lucene 4.3`（4.4已经available），`docs/core/index.html`是Lucene的核心API文档
   * 用 Eclipse 就可以导入`core/lucene-core-4.3.0.jar`和`analysis/common/lucene-analyzers-common-4.3.0.jar`等jar包
    * Lucene 自带的Demo 在 
        * docs/demo/src-html/org/apache/lucene/demo/IndexFiles.html
        * docs/demo/src-html/org/apache/lucene/demo/SearchFiles.html 是很好的入门资料

### 学习Lucene的参考
   * [倒排索引][7]
   * `Lucene API`
   * [实战 Lucene，第 1 部分: 初识 Lucene][8]
   * [Lucene：基于Java的全文检索引擎简介][9]
   * [使用 Apache Lucene 搜索文本][10]
   * [深入 Lucene 索引机制][11]
   * [Lucene中文分析器的中文分词准确性和性能比较][12]
   * [Lucene Payload 的研究与应用][13]
   * [漫话中文自动分词和语义识别（上）：中文分词算法][14]
   * 等等

###使用lucene成果
 * 建立索引，[多线程][15]
    * 硬件信息
    * CUP:  800MHz * 2  AMD Athlon(tm) II X2 240 Processor , `cat /proc/cpuinfo`
    * MEM: 1801072 kB , `cat /proc/meminfo`
    * SDA: Hitachi HDT721032SLA380 320 GB , `sudo smartctl -a /dev/sda`
    * 系统和软件
    * Linux 3.2.0-52-generic #78-Ubuntu SMP i686 athlon i386 GNU/Linux , `uname -a`
    * java version "1.7.0_25" , `java -version`
        * [OpenJDK][16] Runtime Environment (IcedTea 2.3.10) (7u25-2.3.10-1ubuntu0.12.04.2) 
        * OpenJDK Client VM (build 23.7-b01, mixed mode, sharing)

    * 执行的命令 `time java -Xms512m -Xmx1024m -jar lucene.jar -AI /media/linux_lenovo/file /media/linux_lenovo/L_index`
* 对象 10000个最终目标文件，平均30KB
* 输出 
     * real	1m28.123s
     * user 1m27.045s
     * sys	0m3.392s

* 执行搜索
    * `time java -Xms512m -Xmx1024m -jar lucene.jar -SQPS /media/linux_lenovo/L_index 张飞* contents "";`
    * 输出
        * real	0m0.521s
        * user	0m0.604s
        * sys	0m0.076s


###更多
![hello_lucene](/images/hello_lucene.png)






[1]: http://zh.wikipedia.org/wiki/Xargs
[2]: http://zh.wikipedia.org/wiki/Grep
[3]: http://lucene.apache.org/
[4]: http://lucene.apache.org/solr/
[5]: http://nutch.apache.org/
[6]: http://www.manning.com/hatcher2/
[7]: http://zh.wikipedia.org/zh-cn/%E5%80%92%E6%8E%92%E7%B4%A2%E5%BC%95
[8]: http://www.ibm.com/developerworks/cn/java/j-lo-lucene1/
[9]: http://www.chedong.com/tech/lucene.html
[10]: http://www.ibm.com/developerworks/cn/opensource/os-apache-lucenesearch/
[11]: http://www.ibm.com/developerworks/cn/java/wa-lucene/
[12]: http://approximation.iteye.com/blog/345885
[13]: http://www.ibm.com/developerworks/cn/opensource/os-cn-lucene-pl/
[14]: http://www.matrix67.com/blog/archives/4212
[15]: http://zh.wikipedia.org/wiki/%E5%A4%9A%E7%BA%BF%E7%A8%8B
[16]: http://openjdk.java.net/
