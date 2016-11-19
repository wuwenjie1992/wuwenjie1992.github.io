---
layout: post
title: "BIG_DATA杂谈(1)-简单的hadoop伪分布式配置与测试"
date: 2015-10-31 20:23
comments: true
categories: [Data mining,dig data,hadoop,java,三国]
---
##前言
* [Big Data][1] and [AI][2]'s age has coming!
* 面对海量的数据，传统方式已无法满足用户高效地使用和处理数据了。
* [hadoop][3]的意义非同一般，它赋予了人们面对bigdata的信心和能力，同时也开创了一个时代，
* 它是一个可靠的、可扩展的、分布式的计算框架,给予人们PB级的计算处理能力。
* 由[Doug Cutting][4]根据谷歌公司发表的MapReduce和GFS的论文自行实现而成。
* 诞生历史：Lucene (1999) -> Nutch (2003) -> hadoop (2011)

<!-- more -->

##配置
* 对hadoop的配置资料有很多，不再赘述。可参考[它][5]
* 主要关注的是配置中存在的问题
    * 解决 错误: 找不到或无法加载主类 org.apache.hadoop.hdfs.server.namenode.NameNode
     * export HADOOP_HOME=/home/wuwenjie/hello/bigdata/hadoop-2.7.1
     * export PATH="${HADOOP_HOME}:$PATH"
    * etc/hadoop/hadoop-env.sh 中配置JDK
     * export JAVA_HOME="/usr/lib/jvm/java-7-openjdk-i386"
    * etc/hadoop 下的一些配置：
{% codeblock lang:xml etc/hadoop/core-site.xml %}
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost:9000</value>
	</property>
	<property>  
		<name>hadoop.tmp.dir</name>
		<value>/home/wuwenjie/hello/bigdata/hadoopTmpDir</value>  
	</property>  
</configuration>
{% endcodeblock %}
        
{% codeblock lang:xml etc/hadoop/hdfs-site.xml %}
<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
	<property>
		<name>dfs.name.dir</name>  
		<value>file:///home/wuwenjie/hello/bigdata/hdfs/name</value>
	</property>
	<property>
		<name>dfs.data.dir</name>  
		<value>file:///home/wuwenjie/hello/bigdata/hdfs/data</value>
	</property>
</configuration>
{% endcodeblock %}
     
{% codeblock lang:xml etc/hadoop/mapred-site.xml%}
<configuration>
	<property>
		<name>mapred.job.tracker</name>
		<value>localhost:9001</value>
	</property>

	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>
{% endcodeblock %}
     
{% codeblock lang:xml etc/hadoop/yarn-site.xml %}
<configuration>
	<!-- Site specific YARN configuration properties -->
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>
{% endcodeblock %}

##初始化及启动
* 启动hdsf：sbin/start-dfs.sh
* 格式化文件系统（只需一次）：bin/hdfs namenode -format
* 启动yarn：sbin/start-yarn.sh
* 察看java进程：jps
 * jps命令结果 
```text
5600 ResourceManager
5987 Jps
5024 DataNode
5399 SecondaryNameNode
5890 NodeManager
4738 NameNode
```

 * 解释
    * ResourceManager：处理客户端请求启动/监控、ApplicationMaster监控、NodeManager资源分配与调度。
    * DataNode：HDFS工作节点，根据namenode的调度存储和检索数据，定期发送存储的block列表。
    * SecondaryNameNode：帮助NameNode管理Metadata数据，减小在NameNode上的压力。
    * NodeManager：单个节点上的资源管理、处理来自ResourceManager、ApplicationMaster的命令。
    * NameNode：管理HDFS的Namespace，维护所有文件和文件夹的元数据(metadata）。

##测试
* 在hdfs中创建文件夹和导入数据
```sh
bin/hdfs dfs -mkdir /hadoop
bin/hdfs dfs -mkdir /hadoop/test
bin/hdfs dfs -mkdir /hadoop/out
bin/hdfs dfs -put "/media/三国志.txt" /hadoop/test
```
* 运行内置示例：grep
```sh
bin/hadoop jar \
      share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.1.jar \
      grep /hadoop/test /hadoop/out '漢.'
```
* 察看结果
```sh
bin/hdfs dfs -cat /hadoop/out/part-r-00000
```
 * 部分结果
```sh
41    漢書
38	漢中
36	漢晉
34	漢，
30	漢之
29	漢室
23	漢紀
23	漢氏
16	漢末
16	漢高
14	漢朝
13	漢祖
12	漢文
12	漢祚
9	漢帝
8	漢家
7	漢水
```


[1]:https://zh.wikipedia.org/wiki/%E5%A4%A7%E6%95%B8%E6%93%9A
[2]:https://zh.wikipedia.org/wiki/%E4%BA%BA%E5%B7%A5%E6%99%BA%E8%83%BD
[3]:http://hadoop.apache.org
[4]:https://en.wikipedia.org/wiki/Doug_Cutting
[5]:https://hadoop.apache.org/docs/r1.0.4/cn/quickstart.html#PseudoDistributed