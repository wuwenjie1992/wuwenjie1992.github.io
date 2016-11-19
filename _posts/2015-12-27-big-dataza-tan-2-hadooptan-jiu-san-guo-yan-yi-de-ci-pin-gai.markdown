---
layout: post
title: "BIG_DATA杂谈(2)-hadoop探究《三国演义》的词频-改"
date: 2015-11-27 20:36
comments: true
categories: [Data mining,dig data,hadoop,java,三国]
---
## 与传统文化的碰撞
* 虽然这个时代日新月异，但古老的故事依然让人们留恋。
* 此次用hadoop从另一个方面，探究一下脍炙人口的《三国演义》。

## 原理
* 利用lucene的[smartcn][1]进行分词，结合[hadoop的编写wordcount][2]，进行词频统计。

## 难点
* 编译与运行包含第三方jar包的hadoop程序。

<!-- more -->

## 准备工作
* 启动hdfs与yarn
{% codeblock lang:bash %}
cd ~/hello/bigdata/hadoop-2.7.1 #hadoop所在文件夹
sbin/start-dfs.sh
sbin/start-yarn.sh
jps
{% endcodeblock %}

* hadoop执行准备工作
{% codeblock lang:bash %}
export JAVA_HOME="/usr/lib/jvm/java-7-openjdk-i386"
export PATH=${JAVA_HOME}/bin:${PATH}
export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
{% endcodeblock %}


* 编写WordCountOrder 进行词频统计
 * cd ~/hello/bigdata/hadoop_ex/wordcount
{% codeblock lang:java WordCountOrder.java %}

import java.io.IOException;
import java.util.Random;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;

import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.io.WritableComparable;

import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.SequenceFileInputFormat;
import org.apache.hadoop.mapreduce.lib.map.InverseMapper;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.SequenceFileOutputFormat;

import org.apache.hadoop.util.GenericOptionsParser;

import java.util.Iterator;

import org.apache.lucene.analysis.TokenStream;
import org.apache.lucene.analysis.cn.smart.SmartChineseAnalyzer;
import org.apache.lucene.analysis.tokenattributes.CharTermAttribute;
import org.apache.lucene.analysis.util.CharArraySet;
import org.apache.lucene.util.Version;

public class WordCountOrder {
    
	public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
		
		private final static IntWritable one = new IntWritable(1);
		private Text word = new Text();
		
		public void map(Object key, Text value, Context context)
				throws IOException, InterruptedException {
			
			/*
			String[] self_stop_words = { "的", "在","了", "呢", "是"};
			CharArraySet cas = new CharArraySet(0, true);
			for(int i = 0; i < self_stop_words.length; i++) {
				  cas.add(self_stop_words[i]);
			}
			// 加入系统默认停用词
			Iterator<Object> itor = SmartChineseAnalyzer.getDefaultStopSet().iterator();
			while (itor.hasNext()) {
				cas.add(itor.next());
			}
			*/
			
			// 中英文混合分词器
			SmartChineseAnalyzer sca = new SmartChineseAnalyzer();
			//SmartChineseAnalyzer sca = new SmartChineseAnalyzer(cas);
			
            TokenStream ts = sca.tokenStream("field", value.toString());
            CharTermAttribute ch = ts.addAttribute(CharTermAttribute.class);

            ts.reset();
            while (ts.incrementToken()) {
                word.set(ch.toString());
				context.write(word, one);
            }
            ts.end();
            ts.close();
			
		}
	}
	
	public static class IntSumReducer extends
			Reducer<Text, IntWritable, Text, IntWritable> {
		private IntWritable result = new IntWritable();
		public void reduce(Text key, Iterable<IntWritable> values,
				Context context) throws IOException, InterruptedException {
			int sum = 0;
			for (IntWritable val : values) {
				sum += val.get();
			}
			result.set(sum);
			context.write(key, result);
		}
	}
	
	private static class IntWritableDecreasingComparator extends IntWritable.Comparator {
	      public int compare(WritableComparable a, WritableComparable b) {
	        return -super.compare(a, b);
	      }
	      
	      public int compare(byte[] b1, int s1, int l1, byte[] b2, int s2, int l2) {
	          return -super.compare(b1, s1, l1, b2, s2, l2);
	      }
	  }
	  
	  
	public static void main(String[] args) throws Exception {
		
		Configuration conf = new Configuration();
		String[] otherArgs = new GenericOptionsParser(conf, args)
				.getRemainingArgs();
		if (otherArgs.length != 2) {
			System.err.println("Usage: wordcount <in> <out>");
			System.exit(2);
		}
		Path tempDir = new Path("wordcount-temp-" + Integer.toString(
			new Random().nextInt(Integer.MAX_VALUE))); //定义一个临时目录
		
		Job job = Job.getInstance(conf, "word count");
		job.setJarByClass(WordCountOrder.class);
		try{
			job.setMapperClass(TokenizerMapper.class);
			job.setCombinerClass(IntSumReducer.class);
			job.setReducerClass(IntSumReducer.class);
			
			job.setOutputKeyClass(Text.class);
			job.setOutputValueClass(IntWritable.class);
			
			FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
			FileOutputFormat.setOutputPath(job, tempDir);
			//先将词频统计任务的输出结果写到临时目录中, 下一个排序任务以临时目录为输入目录。
			
			job.setOutputFormatClass(SequenceFileOutputFormat.class);
			
			if(job.waitForCompletion(true)){ //当word count结束
				
				Job sortJob = Job.getInstance(conf, "sort");
				sortJob.setJarByClass(WordCountOrder.class);
				
				FileInputFormat.addInputPath(sortJob, tempDir);
				sortJob.setInputFormatClass(SequenceFileInputFormat.class);
				
	            sortJob.setMapperClass(InverseMapper.class);
	            //InverseMapper作用是实现map()之后的数据对的key和value交换
	            sortJob.setNumReduceTasks(1);
	            // Reducer 的个数限定为1, 最终输出的结果文件就是一个
	            FileOutputFormat.setOutputPath(sortJob, new Path(otherArgs[1]));
	            
	        	sortJob.setOutputKeyClass(IntWritable.class);
				sortJob.setOutputValueClass(Text.class);
				/* Hadoop 默认对 IntWritable 按升序排序，而我们需要的是按降序排列。
				 * 因此实现了 IntWritableDecreasingComparator 类,　
				 * 并指定使用这个自定义的 Comparator 类对输出结果中的 key (词频)进行排序*/
	            sortJob.setSortComparatorClass(IntWritableDecreasingComparator.class);
	 
				System.exit(sortJob.waitForCompletion(true) ? 0 : 1);
			}
		}finally{
			FileSystem.get(conf).deleteOnExit(tempDir);
		}
	}
}
{% endcodeblock %}

* 编译java代码并打包
{% codeblock lang:bash %} 
hadoop="../../hadoop-2.7.1/bin/hadoop"
javac -cp `$hadoop classpath`':lucene-core-4.10.1.jar:lucene-analyzers-common-4.10.1.jar:lucene-analyzers-smartcn-4.10.1.jar' WordCountOrder.java
jar cf wcr.jar WordCountOrder*.class
{% endcodeblock %} 

* ![hadoop-001](/images/hadoop-001.png)

* 察看hdfs内容
* ../../hadoop-2.7.1/bin/hdfs dfs -ls -R /
* ![hadoop-002](/images/hadoop-002.png)

* 运行hadoop程序
{% codeblock lang:bash %} 
export LIBJARS=lucene-core-4.10.1.jar,lucene-analyzers-common-4.10.1.jar,lucene-analyzers-smartcn-4.10.1.jar
../../hadoop-2.7.1/bin/hadoop jar wcr.jar WordCountOrder -libjars ${LIBJARS} /hadoop/test3 /hadoop/out
{% endcodeblock %} 

* 运行察看界面
* ![hadoop-003](/images/hadoop-003.png)

* 察看词频统计结果
* ../../hadoop-2.7.1/bin/hdfs dfs -cat /hadoop/out/part-r-00000 > out.txt
 * cat out.txt | grep '[[:digit:]]\{1,\}[[:blank:]].\{1\}$'|head
 *
``` text
8503    曰
7077	之
3880	不
3730	兵
3495	人
3087	一
2590	有
2516	军
2389	大
2343	于
```

 * cat out.txt | grep '[[:digit:]]{1,}[[:blank:]].{2}$'|head
 * 
``` text
880 曹操
837	将军
541	司马
512	丞相
494	关公
420	不可
406	荆州
372	夏侯
367	如此
321	主公
```

 * cat out.txt | grep '[[:digit:]]{1,}[[:blank:]].{3}$'|head
 * 
``` text
146   诸葛亮
48	大将军
34	刀斧手
30	中郎将
29	阳平关
28	不得已
24	大丈夫
23	不可不
18	弓弩手
15	东南风
```

 * cat out.txt | grep '[[:digit:]]\{1,\}[[:blank:]].\{4,\}$'|head
 * 
``` text
24    决一死战
22	措手不及
18	不计其数
16	深沟高垒
15	按兵不动
14	所到之处
13	勃然大怒
13	出其不意
12	人困马乏
11	将计就计
```

## 结束语
* 从短短几字就不难看出，《三国演义》的主要人物、地点、策略。


[1]:http://lucene.apache.org/core/5_4_0/analyzers-smartcn/index.html
[2]:https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop2/
