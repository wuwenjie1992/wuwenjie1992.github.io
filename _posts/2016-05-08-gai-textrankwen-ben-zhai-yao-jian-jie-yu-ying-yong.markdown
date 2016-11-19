---
layout: post
title: "改-TextRank文本摘要简介与应用"
date: 2016-03-31 19:13
comments: true
categories: [Data mining,Machine Learning,NLP,java]
---

##文本摘要简介
* [Automatic Summarization][1] 主要有[两种方法][2]。
 * Extraction : 抽取式，提取文档中已存在的关键词、句子形成摘要。
 * Abstraction: 生成式，建立抽象的语意表示，使用自然语言生成技术，形成摘要。

##TextRank的文本摘要
* TextRank的方法属于[graph-based][3] Extraction。
* 对文本中的句子重要性排序后得到摘要。
* 权值为句子间的相似度，计算两个句子的内容覆盖率。
* 与[TextRank关键字提取][4]的不同：考虑了句子间的权值。

<!-- more -->

##实现参考
* 同样参考hankcs的实现，[github][5]
* 改用smartcn进行分词
* 部分结果
```text
sentence_count:134
document_length:1404
sentence_length_avg:10
size:6
topSentence:沪XX路XX弄一居民养猫百余只熏煞一楼人来源
topSentence:该户人家豢养了至少100多只猫
topSentence:养猫扰民确实不该
topSentence:每只笼子都有猫
topSentence:记者从社区民警、居委会干部处证实了该说法
topSentence:有居民甚至曾目击该户主在夜间捕捉小猫后

沪XX路XX弄一居民养猫百余只熏煞一楼人来源。养猫扰民确实不该。有居民甚至曾目击该户主在夜间捕捉小猫后。

```

## 实现代码

{% codeblock lang:java TextRankSentence.java %}

import org.apache.lucene.analysis.TokenStream;
import org.apache.lucene.analysis.cn.smart.SmartChineseAnalyzer;
import org.apache.lucene.analysis.tokenattributes.CharTermAttribute;
import org.apache.lucene.analysis.util.CharArraySet;
import org.apache.lucene.util.Version;

import java.util.*;

/*
 * javac -cp '.:lucene-core-4.10.1.jar:lucene-analyzers-common-4.10.1.jar:lucene-analyzers-smartcn-4.10.1.jar' TextRankSentence.java
 * 
 * java -cp '.:lucene-core-4.10.1.jar:lucene-analyzers-common-4.10.1.jar:lucene-analyzers-smartcn-4.10.1.jar' TextRankSentence `cat a.txt` 50
 * 
 * jar cvfm TextRankSentence.jar MANIFEST.MF *.class
 * 
 * java -jar TextRankSentence.jar
 * 
 */

/**
 * TextRank 自动摘要
 *
 * @author hankcs
 * @modify wuwenjie
 */
public class TextRankSentence{
    
    /**
     * 阻尼系数（ＤａｍｐｉｎｇＦａｃtｏｒ），一般取值为0.85
     */
    final static double d = 0.85;
    /**
     * 最大迭代次数
     */
    final static int max_iter = 200;
    final static double min_diff = 0.001;
    /**
     * 文档句子的个数
     */
    int D;
    /**
     * 拆分为[句子[单词]]形式的文档
     */
    List<List<String>> docs;
    /**
     * 排序后的最终结果 score <-> index
     */
    TreeMap<Double, Integer> top;

    /**
     * 句子和其他句子的相关程度
     */
    double[][] weight;
    /**
     * 该句子和其他句子相关程度之和
     */
    double[] weight_sum;
    /**
     * 迭代之后收敛的权重
     */
    double[] vertex;

    /**
     * BM25相似度
     */
    BM25 bm25;

    public TextRankSentence(List<List<String>> docs){
        this.docs = docs;
        bm25 = new BM25(docs);
        D = docs.size();
        weight = new double[D][D];
        weight_sum = new double[D];
        vertex = new double[D];
        top = new TreeMap<Double, Integer>(Collections.reverseOrder());
        solve();
    }

    private void solve(){
        int cnt = 0;
        for (List<String> sentence : docs) {
            double[] scores = bm25.simAll(sentence);
			//System.out.println(Arrays.toString(scores));
            weight[cnt] = scores;
            weight_sum[cnt] = sum(scores) - scores[cnt];
            // 减掉自己，自己跟自己肯定最相似
            vertex[cnt] = 1.0;
            ++cnt;
        }
        
        for (int _ = 0; _ < max_iter; ++_){
            double[] m = new double[D];
            double max_diff = 0;
            for (int i = 0; i < D; ++i){
                m[i] = 1 - d;
                for (int j = 0; j < D; ++j){
                    if (j == i || weight_sum[j] == 0) continue;
                    m[i] += (d * weight[j][i] / weight_sum[j] * vertex[j]);
                }
                double diff = Math.abs(m[i] - vertex[i]);
                if (diff > max_diff){
                    max_diff = diff;
                }
            }
            vertex = m;
            if (max_diff <= min_diff) break;
        }
        // 排序
        for (int i = 0; i < D; ++i){
            top.put(vertex[i], i);
        }
    }

    /**
     * 获取前几个关键句子
     *
     * @param size 要几个
     * @return 关键句子的下标
     */
    public int[] getTopSentence(int size){
		
        Collection<Integer> values = top.values();
        size = Math.min(size, values.size());
        int[] indexArray = new int[size];
        Iterator<Integer> it = values.iterator();
        
        for (int i = 0; i < size; ++i){
            indexArray[i] = it.next();
        }
        return indexArray;
    }

    /**
     * 简单的求和
     *
     * @param array
     * @return
     */
    private static double sum(double[] array){
        double total = 0;
        for (double v : array){
            total += v;
        }
        return total;
    }

    public static void main(String[] args) throws Exception{
        System.out.println("\n\n\n"+TextRankSentence.getTopSentenceList(args[0], Integer.parseInt(args[1])));
        System.out.println("\n\n\n"+TextRankSentence.getSummary(args[0], Integer.parseInt(args[1])));
    }

    /**
     * 将文章分割为句子
     *
     * @param document
     * @return
     */
    static List<String> spiltSentence(String document){
        List<String> sentences = new ArrayList<String>();
        for (String line : document.split("[\r\n]")){
            line = line.trim();
            if (line.length() == 0) continue;
            for (String sent : line.split("[，,。:：“”？?！!；;]")){
                sent = sent.trim();
                if (sent.length() == 0) continue;
                sentences.add(sent);
            }
        }

        return sentences;
    }

    /**
     * 将句子列表转化为文档
     *
     * @param sentenceList
     * @return
     */
    private static List<List<String>> convertSentenceListToDocument(List<String> sentenceList)
     throws Exception{
        
        CharArraySet cas = new CharArraySet(0, true);
        
        // 自定义停用词
        String[] self_stop_words = { "的", "在","了", "呢", "，", "：", ",","是","一"};
        
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
        
        List<List<String>> docs = new ArrayList<List<String>>(sentenceList.size());
        
        for (String sentence : sentenceList){
			
            List<String> wordList = new LinkedList<String>();

            TokenStream ts = sca.tokenStream("field", sentence);
            CharTermAttribute ch = ts.addAttribute(CharTermAttribute.class);

            ts.reset();
            while (ts.incrementToken()) {
                wordList.add(ch.toString());
                //System.out.print(ch.toString()+"\\");
                
            }
            ts.end();
            ts.close();
            
            docs.add(wordList);
        }
        return docs;
        
    }

    /**
     * 一句话调用接口
     *
     * @param document 目标文档
     * @param size     需要的关键句的个数
     * @return 关键句列表
     */
    public static List<String> getTopSentenceList(String document, int size)throws Exception{
		
        List<String> sentenceList = spiltSentence(document);
        List<List<String>> docs = convertSentenceListToDocument(sentenceList);
        TextRankSentence textRank = new TextRankSentence(docs);
        int[] topSentence = textRank.getTopSentence(size);
        List<String> resultList = new LinkedList<String>();
        for (int i : topSentence)
        {
            resultList.add(sentenceList.get(i));
        }
        return resultList;
    }

    /**
     * 一句话调用接口
     *
     * @param document   目标文档
     * @param max_length 需要摘要的长度
     * @return 摘要文本
     */
    public static String getSummary(String document, int max_length){
		
        List<String> sentenceList = spiltSentence(document);

        int sentence_count = sentenceList.size();
        int document_length = document.length();
        int sentence_length_avg = document_length / sentence_count;
        int size = max_length / sentence_length_avg + 1;
        
        System.out.println("sentence_count:"+sentence_count);
        System.out.println("document_length:"+document_length);
        System.out.println("sentence_length_avg:"+sentence_length_avg);
        System.out.println("size:"+size);
        
        List<String> resultList = new LinkedList<String>();
        try{
        List<List<String>> docs = convertSentenceListToDocument(sentenceList);
        
        TextRankSentence textRank = new TextRankSentence(docs);
        int[] topSentence = textRank.getTopSentence(size);
        
        for (int i : topSentence){
            resultList.add(sentenceList.get(i));
            
            System.out.println("topSentence:"+sentenceList.get(i));
        }

        resultList = permutation(resultList, sentenceList);
        resultList = pick_sentences(resultList, max_length);
        
        }
		catch (Exception ex) {
            ex.printStackTrace();
        }
        
        return join("。", resultList);
    }

	// 排列
    public static List<String> permutation(List<String> resultList, List<String> sentenceList){
        int index_buffer_x;
        int index_buffer_y;
        String sen_x;
        String sen_y;
        int length = resultList.size();
        // bubble sort derivative
        for (int i = 0; i < length; i++)
            for (int offset = 0; offset < length - i; offset++){
                sen_x = resultList.get(i);
                sen_y = resultList.get(i + offset);
                index_buffer_x = sentenceList.indexOf(sen_x);
                index_buffer_y = sentenceList.indexOf(sen_y);
                // if the sentence order in sentenceList does not conform that is in resultList, reverse it
                if (index_buffer_x > index_buffer_y){
                    resultList.set(i, sen_y);
                    resultList.set(i + offset, sen_x);
                }
            }

        return resultList;
    }

	// 选句
    public static List<String> pick_sentences(List<String> resultList, int max_length){
		
        int length_counter = 0;
        int length_buffer;
        int length_jump;
        List<String> resultBuffer = new LinkedList<String>();
        for (int i = 0; i < resultList.size(); i++){
            length_buffer = length_counter + resultList.get(i).length();
            if (length_buffer <= max_length){
                resultBuffer.add(resultList.get(i));
                length_counter += resultList.get(i).length();
            }
            else if (i < (resultList.size() - 1)){
                length_jump = length_counter + resultList.get(i + 1).length();
                if (length_jump <= max_length){
                    resultBuffer.add(resultList.get(i + 1));
                    length_counter += resultList.get(i + 1).length();
                    i++;
                }
            }
        }
        return resultBuffer;
    }
    
    // 连接 
    public static String join(String delimiter, Collection<String> stringCollection){
		
        StringBuilder sb = new StringBuilder(stringCollection.size() * (16 + delimiter.length()));
        for (String str : stringCollection){
            sb.append(str).append(delimiter);
        }
        return sb.toString();
    }

}

{% endcodeblock %}



{% codeblock lang:java BM25.java %}

import java.util.List;
import java.util.Map;
import java.util.TreeMap;

/**
 * 搜索相关性评分算法
 * @author hankcs
 */
public class BM25{
    /**
     * 文档句子的个数
     */
    int D;

    /**
     * 文档句子的平均长度
     */
    double avgdl;

    /**
     * 拆分为[句子[单词]]形式的文档
     */
    List<List<String>> docs;

    /**
     * 文档中每个句子中的每个词与词频
     */
    Map<String, Integer>[] f;

    /**
     * 文档中全部词语与出现在几个句子中
     */
    Map<String, Integer> df;

    /**
     * IDF
     */
    Map<String, Double> idf;

    /**
     * 调节因子
     */
    final static float k1 = 1.5f;

    /**
     * 调节因子
     */
    final static float b = 0.75f;

    public BM25(List<List<String>> docs){
        this.docs = docs;
        D = docs.size();
        for (List<String> sentence : docs){
            avgdl += sentence.size();
        }
        avgdl /= D;
        f = new Map[D];
        df = new TreeMap<String, Integer>();
        idf = new TreeMap<String, Double>();
        init();
    }

    /**
     * 在构造时初始化自己的所有参数
     */
    private void init(){
        int index = 0;
        for (List<String> sentence : docs){
            Map<String, Integer> tf = new TreeMap<String, Integer>();
            for (String word : sentence){
                Integer freq = tf.get(word);
                freq = (freq == null ? 0 : freq) + 1;
                tf.put(word, freq);
            }
            f[index] = tf;
            for (Map.Entry<String, Integer> entry : tf.entrySet()){
                String word = entry.getKey();
                Integer freq = df.get(word);
                freq = (freq == null ? 0 : freq) + 1;
                df.put(word, freq);
            }
            ++index;
        }
        for (Map.Entry<String, Integer> entry : df.entrySet()){
            String word = entry.getKey();
            Integer freq = entry.getValue();
            idf.put(word, Math.log(D - freq + 0.5) - Math.log(freq + 0.5));
        }
    }

    public double sim(List<String> sentence, int index){
        double score = 0;
        for (String word : sentence){
            if (!f[index].containsKey(word)) continue;
            int d = docs.get(index).size();
            Integer wf = f[index].get(word);
            score += (idf.get(word) * wf * (k1 + 1)/ (wf + k1 * (1 - b + b * d/ avgdl)));
        }

        return score;
    }

    public double[] simAll(List<String> sentence){
        double[] scores = new double[D];
        for (int i = 0; i < D; ++i){
            scores[i] = sim(sentence, i);
        }
        return scores;
    }
}

{% endcodeblock %}


[1]:https://en.wikipedia.org/wiki/Automatic_summarization
[2]:http://www.cnblogs.com/chenbjin/p/4600538.html
[3]:https://en.wikipedia.org/wiki/Automatic_summarization#Unsupervised_approach:_TextRank
[4]:blog/2016/02/06/gai-textrankwen-ben-guan-jian-zi-ti-qu-jian-jie-yu-ying-yong/
[5]:https://github.com/hankcs/TextRank/blob/master/src/main/java/com/hankcs/textrank/TextRankSummary.java
