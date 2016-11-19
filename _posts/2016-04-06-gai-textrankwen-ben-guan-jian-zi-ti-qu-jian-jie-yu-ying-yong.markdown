---
layout: post
title: "改-TextRank文本关键字提取简介与应用"
date: 2016-02-06 21:03
comments: true
categories: [Data mining,Machine Learning,NLP,java]
---
##TextRank与PageRank
* TextRank脱胎于PageRank，受其启发应用于文本处理。[->论文][1]
* TextRank在PageRank的基础上，引入了边的权值概念，代表两个句子的相似度。
* PageRank 公式
 * ![pagerank](/images/pagerank.png)
* TextRank 公式
 *  ![textrank](/images/textrank.png)
* [公式解释][2]
 * 模型描述了一个有向有权图 G =(V, E), 由点集合V和边集合E组成
 * 图中任两点 Vi , Vj 之间边的权重为 wji 
 * 对于一个给定的点Vi, In(Vi)为指向该点的点集合, Out(Vi)为点Vi指向的点集合
 * d为阻尼系数,代表从图中某一特定点指向其他任意点的概率,一般取值为0.85

<!-- more -->

##关键字提取
* 单词构成的边的权重变为0（没有相似性），[权值约掉][3]，算法退化为PageRank
* 大致步骤：
 * 分词。
 * 按预定窗口大小，依次划分单词。
 * 计算公式，迭代、直至收敛。
 * 对结果倒序排序，得到最重要关键词。
 
##测试结果
* 某抱怨养猫新闻
``` text
猫 - 10.739771
记者 - 6.869424
居民 - 5.822851
养 - 5.185602
该 - 4.929795
家 - 3.1442623
楼 - 2.9776037
中 - 2.8466675
咪 - 2.7177916
小区 - 2.664779
```

##java实现参考
* 参考hankcs的实现，[github][4]
* 改用smartcn进行分词
* 算法关键：getRank
* 实例
{% codeblock lang:java TextRankKeyword.java %}

import java.util.*;

import org.apache.lucene.analysis.TokenStream;
import org.apache.lucene.analysis.cn.smart.SmartChineseAnalyzer;
import org.apache.lucene.analysis.tokenattributes.CharTermAttribute;
import org.apache.lucene.analysis.util.CharArraySet;
import org.apache.lucene.util.Version;


/*
 * javac -cp '.:lucene-core-4.10.1.jar:lucene-analyzers-common-4.10.1.jar:lucene-analyzers-smartcn-4.10.1.jar' TextRankKeyword.java
 * 
 * java -cp '.:lucene-core-4.10.1.jar:lucene-analyzers-common-4.10.1.jar:lucene-analyzers-smartcn-4.10.1.jar' TextRankKeyword `cat a.txt` 50|grep -E '.{2,} - '
 * 
 */ 


/**
 * 基于TextRank算法的关键字提取，适用于单文档
 * @author hankcs
 * @modify wuwenjie
 */
public class TextRankKeyword {
    /**
     * 提取多少个关键字
     */
    int nKeyword = 10;
    /**
     * 阻尼系数（ＤａｍｐｉｎｇＦａｃｔｏｒ），一般取值为0.85
     */
    final static float d = 0.85f;
    /**
     * 最大迭代次数
     */
    final static int max_iter = 200;
    final static float min_diff = 0.001f;

    /**
     * 提取关键词
     * @param document 文档内容
     * @param size 希望提取几个关键词
     * @return 一个列表
     */
    public static List<String> getKeywordList(String document, int size)
    throws Exception{
        TextRankKeyword textRankKeyword = new TextRankKeyword();
        textRankKeyword.nKeyword = size;

        return textRankKeyword.getKeyword(document);
    }

    /**
     * 提取关键词
     * @param content
     * @return
     */
    public List<String> getKeyword(String content) throws Exception{
    	
        Set<Map.Entry<String, Float>> entrySet = getTermAndRank(content, nKeyword).entrySet();
        List<String> result = new ArrayList<String>(entrySet.size());
        for (Map.Entry<String, Float> entry : entrySet)
        {
            result.add(entry.getKey());
        }
        return result;
    }

    /**
     * 返回全部分词结果和对应的rank
     * @param content
     * @return
     */
    public Map<String,Float> getTermAndRank(String content) throws Exception{
        assert content != null;
        
        CharArraySet cas = new CharArraySet(0, true);
        
        // 自定义停用词
        String[] self_stop_words = { "的", "在","了", "呢", "，", "：", ",","是","一","我","会","这","着","也","为","里","个","要","来","与","但","只","对","就","那些","这些","她们","我们","他们","但是","或者","一个","其他","自己","人","和","上","不","有","他"};
        for (int i = 0; i < self_stop_words.length; i++) {
			cas.add(self_stop_words[i]);
		}
        
        // 加入系统默认停用词
		Iterator<Object> itor = SmartChineseAnalyzer.getDefaultStopSet().iterator();
		while (itor.hasNext()) {
			cas.add(itor.next());
		}
        
        
        // 中英文混合分词器(其他几个分词器对中文的分析都不行)
        SmartChineseAnalyzer sca = new SmartChineseAnalyzer(cas);
        
        
        List<String> wordList = new LinkedList<String>();

		TokenStream ts = sca.tokenStream("field", content);
		CharTermAttribute ch = ts.addAttribute(CharTermAttribute.class);

		ts.reset();
		while (ts.incrementToken()) {
			wordList.add(ch.toString());
			//System.out.print(ch.toString()+"\\");
			
		}
		ts.end();
		ts.close();
        
        return getRank(wordList);
    }

    /**
     * 返回分数最高的前size个分词结果和对应的rank
     * @param content
     * @param size
     * @return
     */
    public Map<String,Float> getTermAndRank(String content, Integer size)throws Exception{
		
        Map<String, Float> map = getTermAndRank(content);
        Map<String, Float> result = new LinkedHashMap<String, Float>();
        for (Map.Entry<String, Float> entry : new MaxHeap<Map.Entry<String, Float>>(size, new Comparator<Map.Entry<String, Float>>()
        {
            @Override
            public int compare(Map.Entry<String, Float> o1, Map.Entry<String, Float> o2)
            {
                return o1.getValue().compareTo(o2.getValue());
            }
        }).addAll(map.entrySet()).toList())
        {
            result.put(entry.getKey(), entry.getValue());
        }

        return result;
    }

    /**
     * 使用已经分好的词来计算rank
     * @param termList
     * @return
     */
    public Map<String,Float> getRank(List<String> wordList)
    {
        
//        System.out.println(wordList);
        Map<String, Set<String>> words = new TreeMap<String, Set<String>>();
        Queue<String> que = new LinkedList<String>();
        for (String w : wordList)
        {
            if (!words.containsKey(w))
            {
                words.put(w, new TreeSet<String>());
            }
            que.offer(w);
            if (que.size() > 5)
            {
                que.poll();
            }

            for (String w1 : que)
            {
                for (String w2 : que)
                {
                    if (w1.equals(w2))
                    {
                        continue;
                    }

                    words.get(w1).add(w2);
                    words.get(w2).add(w1);
                }
            }
        }
//        System.out.println(words);
        Map<String, Float> score = new HashMap<String, Float>();
        for (int i = 0; i < max_iter; ++i)
        {
            Map<String, Float> m = new HashMap<String, Float>();
            float max_diff = 0;
            for (Map.Entry<String, Set<String>> entry : words.entrySet())
            {
                String key = entry.getKey();
                Set<String> value = entry.getValue();
                m.put(key, 1 - d);
                for (String element : value)
                {
                    int size = words.get(element).size();
                    if (key.equals(element) || size == 0) continue;
                    m.put(key, m.get(key) + d / size * (score.get(element) == null ? 0 : score.get(element)));
                }
                max_diff = Math.max(max_diff, Math.abs(m.get(key) - (score.get(key) == null ? 0 : score.get(key))));
            }
            score = m;
            if (max_diff <= min_diff) break;
        }

        return score;
    }



	public static void main(String[] args) throws Exception{
        
        TextRankKeyword myout = new TextRankKeyword();
        
        
        Map<String,Float> myRank = myout.getTermAndRank(args[0], Integer.parseInt(args[1]));
        
        
        for(String key: myRank.keySet())
			System.out.println(key + " - " + myRank.get(key));
        
        
    }


}

{% endcodeblock %}

{% codeblock lang:java MaxHeap.java %}

import java.util.*;

/**
 * 用固定容量的优先队列模拟的最大堆，用于解决求topN大的问题
 *
 * @author hankcs
 */
public class MaxHeap<E>
{
    /**
     * 优先队列
     */
    private PriorityQueue<E> queue;
    /**
     * 堆的最大容量
     */
    private int maxSize;

    /**
     * 构造最大堆
     * @param maxSize 保留多少个元素
     * @param comparator 比较器，生成最大堆使用o1-o2，生成最小堆使用o2-o1，并修改 e.compareTo(peek) 比较规则
     */
    public MaxHeap(int maxSize, Comparator<E> comparator)
    {
        if (maxSize <= 0)
            throw new IllegalArgumentException();
        this.maxSize = maxSize;
        this.queue = new PriorityQueue<E>(maxSize, comparator);
    }

    /**
     * 添加一个元素
     * @param e 元素
     * @return 是否添加成功
     */
    public boolean add(E e)
    {
        if (queue.size() < maxSize)
        { // 未达到最大容量，直接添加
            queue.add(e);
            return true;
        }
        else
        { // 队列已满
            E peek = queue.peek();
            if (queue.comparator().compare(e, peek) > 0)
            { // 将新元素与当前堆顶元素比较，保留较小的元素
                queue.poll();
                queue.add(e);
                return true;
            }
        }
        return false;
    }

    /**
     * 添加许多元素
     * @param collection
     */
    public MaxHeap<E> addAll(Collection<E> collection)
    {
        for (E e : collection)
        {
            add(e);
        }

        return this;
    }

    /**
     * 转为有序列表，自毁性操作
     * @return
     */
    public List<E> toList()
    {
        ArrayList<E> list = new ArrayList<E>(queue.size());
        while (!queue.isEmpty())
        {
            list.add(0, queue.poll());
        }

        return list;
    }
}

{% endcodeblock %}

[1]:https://web.eecs.umich.edu/~mihalcea/papers/mihalcea.emnlp04.pdf
[2]:http://www.cnblogs.com/chenbjin/p/4600538.html
[3]:http://www.hankcs.com/nlp/textrank-algorithm-to-extract-the-keywords-java-implementation.html
[4]:https://github.com/hankcs/TextRank/blob/master/src/main/java/com/hankcs/textrank/TextRankKeyword.java