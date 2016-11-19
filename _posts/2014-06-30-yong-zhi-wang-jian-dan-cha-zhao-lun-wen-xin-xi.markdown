---
layout: post
title: "用知网简单查找论文信息"
date: 2014-06-30 17:50
comments: true
categories: [java,论文]
---
<!-- more -->

# 功能
  * 通过用户输入的关键词查找相关论文信息
  * 利用[CNKI][1]知网的多个学位论文数据库，查找论文
  * 返回与关键词相似的论文信息包括：
      * 相似的论文题目
      * 作者信息
      * 论文性质
      * 论文来源
      * 论文发表时间
      * 论文下载次数
      * 论文相似句子

# 使用
* 编译
```bash
    javac GETcnki.java
```
* 执行
```bash
    java GETcnki 内幕交易 实证分析 "Rezaul Kabir" 阿姆斯特丹
```
* 效果
```text
正在通过CNKI查找内容相似的论文。。。
关键词：内幕交易 实证分析 Rezaul Kabir 阿姆斯特丹 

0:股票市场失灵与政府行为选择  宋玉臣 博士
吉林大学 2006-10-01 下载频次:1311
句子1：其次，实证分析方面，对宏观内幕交易和微观内幕交易分别进行了实证研究。
句子2：Rezaul Kabir和Theo Vermaelen（1996）对荷兰阿姆斯特丹股票市场进行的实证分析，通过检验年报公布前两个月的股票流动性来检验内幕交易对股票价格的影响，结论是认为该市场存在内幕交易并支持政府限制内幕交易；			   

1:我国股票市场内幕交易的实证研究  刘晓明 硕士
暨南大学 2008-05-01 下载频次:597
句子1：暨南大学硕士论文我国股票市场内幕交易的实证研究5内幕交易案例分析及原因分析按内幕交易的主体，可以将内幕人分为证券内幕信息的知情人员(第一内幕人)，从内幕信息知情人员获得内幕信息的第二内幕人和非法获取内幕信息的人。
句子2：李心丹(2007)通过建立内幕交易行为动机结构模型，实证分析了影响内幕交易行为发生的多种因素，指出高额的期望收益和跟风攀比心态的存在极大的强化了内幕交易主体从事内幕交易的倾向，而实施内幕交易引发的内疚感、导致社会声誉的受损、被查处的力度和被惩罚的力度，特别是查处力度与惩罚力度，在相当程度上弱化了内幕主体实施内幕交易的行为倾向。			   

2:股票市场内幕交易及量价波动的实证研究  陈婧 硕士
南京理工大学 2007-06-01 下载频次:535
句子1：Rezaul Kabir和Theovermaelen(1996)对荷兰阿姆斯特丹股票市场进行的实证分析，通过检验禁止内幕交易后股票流动性来检验其对股票市场的影响，发现限制内幕交易减少了股票的流动性(用交易量衡量流动性)，同时还发现市南京理工大学硕士学位论文股票市场内幕交易及量价波动的实证研究场对利好消息的反应速度减慢。
句子2：8.3进一步研究方向尽管本文对内幕交易进行了比较全面、细致的实证分析，但是有关内幕交易的研究仍然有待深入。			   

3:我国证券市场内幕交易对投资者利益影响的实证分析  张宇 硕士
西南大学 2011-04-05 下载频次:240
句子1：西南大学硕士学位论文第5章内幕交易实证分析第5章内幕交易实证分析上一章已经大致介绍了运用事件研究法的步骤，本章就以西南证券(印0369)为例，进行实验研究分析。
句子2：Theovermaelen(1996)对荷兰阿姆斯特丹股票市场进行了股票流动性与内幕交易的关系进行了实证分析，通过检验禁_l卜内幕交易后股票流动性来检验其对股票市场的影响发现，当内幕交易被限制了股票的流动性减少了，此时市场对利好消息的反应速度也减慢。			   
```


# java 代码
* 说明：主要是要保存第一次的[GET请求][2]的[COOKIES][3]，用于设置于第二个GET请求
```java
import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.UnsupportedEncodingException;

import java.lang.Integer;

import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLEncoder;

import java.util.ArrayList;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class GETcnki {
    public static void readContentFromGet(String GET_URL) {
        String lines;
        URL cnkiURL = null;

        try {
            cnkiURL = new URL(GET_URL);
        } catch (java.net.MalformedURLException e) {
            e.printStackTrace();
            readContentFromGet(GET_URL);
        }

        List<String> cookies = null;
        StringBuffer response = new StringBuffer();

        // 打开连接，URL.openConnection函数会根据URL的类型，
        // 返回不同的URLConnection子类的对象，URL是http,返回HttpURLConnection
        try {
            HttpURLConnection connection = (HttpURLConnection) cnkiURL.openConnection();

            connection.setRequestProperty("User-Agent",
                "Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:29.0) Gecko/20100101 Firefox/29.0");
            connection.connect();
            /*
            String headerName = null;
            for (int i = 1; (headerName = connection.getHeaderFieldKey(i)) != null;
                    i++) {
                System.out.println(headerName + ":" + connection.getHeaderField(i));
            }
            */
            cookies = connection.getHeaderFields().get("Set-Cookie");

            BufferedReader reader = new BufferedReader(new InputStreamReader(
                        connection.getInputStream(), "utf-8"));

            while ((lines = reader.readLine()) != null) {
                response.append(lines);
            }

            reader.close();
            connection.disconnect();

            //System.out.println("----------\n"+response);
        } catch (java.io.IOException e) {
            e.printStackTrace();
            readContentFromGet(GET_URL);
        }

        //---------------------------
        URL cnkiURL2 = null;

        try {
            cnkiURL2 = new URL(
                    "http://epub.cnki.net/KNS/brief/brief.aspx?pagename=" +
                    response.toString());
        } catch (java.net.MalformedURLException e) {
            e.printStackTrace();
            readContentFromGet(GET_URL);
        }

        StringBuffer response2 = new StringBuffer();

        try {
            HttpURLConnection con = (HttpURLConnection) cnkiURL2.openConnection();

            con.setRequestProperty("User-Agent",
                "Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:29.0) Gecko/20100101 Firefox/29.0");
            con.setRequestProperty("Referer",
                "http://epub.cnki.net/KNS/brief/result.aspx?dbprefix=CDMD");

            String newcookies = "";

            for (String cookie : cookies) {
                newcookies += (cookie.substring(0, cookie.indexOf(";")) + ";");
            }

            //System.out.println("newcookies:" + newcookies);
            //ASP.NET_SessionId=oaxxoh3bazgbhcuei5aharzk; LID=; SID_kns=120113
            con.addRequestProperty("Cookie", newcookies);
            /*
            for (String header : con.getRequestProperties().keySet()) {
            if (header != null) {
            for (String value : con.getRequestProperties().get(header)) {
                System.out.println(header + ":" + value);
            }
            }
            }
            */
            con.connect();

            BufferedReader reader2 = new BufferedReader(new InputStreamReader(
                        con.getInputStream(), "utf-8"));

            while ((lines = reader2.readLine()) != null) {
                response2.append(lines);
            }

            reader2.close();
            con.disconnect();
            
        } catch (java.io.IOException e) {
            e.printStackTrace();
            readContentFromGet(GET_URL);
        }
        
        String info = response2.toString();

        //句子&nbsp;1([\s\S]+?)句子来
        List<String> sentences = GETcnki.StrMatch(info,
                "句子&nbsp;1([\\s\\S]+?)句子来");

        for(int i =0;i<sentences.size();i++){
			//去除标签
			sentences.set(i,sentences.get(i).replaceAll("<[^>]*>",""));
			
			//分段
			Matcher mat = 
				Pattern.compile("句子&nbsp;").matcher(sentences.get(i));
			while (mat.find())
				sentences.set(i,
					sentences.get(i).replaceAll("句子&nbsp;","\n句子"));
					
			sentences.set(i,"句子1"+sentences.get(i));
			
        }

		int i = 0;
        //har\('(.+?)'
        List<String> titles = GETcnki.StrMatch(info, "har\\('(.+?)'");
        //作者.+?">(.+?)<
        List<String> auhtors = GETcnki.StrMatch(info, "作者.+?\">(.+?)<");
        //文献来源.+?">(.+?)<
        List<String> literatures = GETcnki.StrMatch(info, "文献来源.+?\">(.+?)<");
        //en.{2}发表时间.+?>(.+?\b)<
        List<String> dates = GETcnki.StrMatch(info, "en.{2}发表时间.+?>(.+?\\b)<");
        //下载频次.+?>([\d]*)
        List<String> downloadfrequencys =
                        GETcnki.StrMatch(info,"下载频次.+?>([\\d]*)");
        //来源库.+?>(.+?)[\s]+?<
        List<String> databases =
                        GETcnki.StrMatch(info,"来源库.+?>(.+?)[\\s]+?<");
         
         for(i =0;i<sentences.size();i++){
			 System.out.println(i + ":" + titles.get(i)
				+"  "+auhtors.get(i)+" "+databases.get(i)
				+"\n"+literatures.get(i)+" "+dates.get(i)
				+" 下载频次:"+downloadfrequencys.get(i)+"\n"+sentences.get(i)+"\n");
		 }
    }

    public static List<String> StrMatch(String raw, String regex) {
        List<String> result = new ArrayList<String>();

        Pattern pat = Pattern.compile(regex);
        Matcher mat = pat.matcher(raw);

        if (mat.find()) {
            //System.out.println("---\n"+mat.group(1)+"---\n");
            result.add(mat.group(1));

            while (mat.find())
                //System.out.println("---\n"+mat.group(1)+"---\n");
                //捕获组是从 1 开始从左到右的索引
                result.add(mat.group(1));
        } else {
            return result;
        }

        return result;
    }

    public static String encodeURIComponent(String component) {
        String result = null;

        try {
            result = URLEncoder.encode(component, "UTF-8")
                               .replaceAll("\\%28", "(").replaceAll("\\%29", ")")
                               .replaceAll("\\+", "%20").replaceAll("\\%27", "'")
                               .replaceAll("\\%21", "!").replaceAll("\\%7E", "~");
        } catch (UnsupportedEncodingException e) {
            result = component;
        }

        return result;
    }

    public static void main(String[] args) {
		
		String[] s={"","","",""};
		System.out.print("正在通过CNKI查找内容相似的论文。。。\n关键词：");
		
		if(args.length != 4){
			System.err.println("请输入四个字符串!");
			return ;
		}
		
		for(int i =0 ;i<args.length;i++){
			if(args[i].length() == 0 ){
				System.err.println("\n请不要输入空字符串!");
				return ;
			}
			s[i] = encodeURIComponent(args[i]);
			System.out.print(args[i]+" ");
		}
		System.out.println("\n");

        String GET_URL = "http://epub.cnki.net/KNS/request/SearchHandler.ashx?" +
            "action=&NaviCode=*&ua=1.21&PageName=ASP.brief_result_aspx&DbPrefix=CDMD" +
            "&DbCatalog=中国优秀博硕士学位论文全文数据库" + "&ConfigFile=CDMD.xml" +
            "&db_opt=中国优秀博硕士学位论文全文数据库" +
            "&db_value=中国博士学位论文全文数据库,中国优秀硕士学位论文全文数据库" +
            "&sen_1_sel=%2FNEAR%2020&sen_1_value1=" + s[0] + "&sen_1_value2=" +
            s[1] + "&sen_2_sel=%2FSEN%2020&sen_2_value1=" + s[2] +
            "&sen_2_value2=" + s[3] + "&sen_2_logical=and" +
            "&his=0&issen=1&__=Mon%20Jun%2009%202014%2022%3A15%3A59%20GMT%2B0800%20(CST)";

        /*ConfigFile        CDMD.xml
        DbCatalog        中国优秀博硕士学位论文全文数据库
        DbPrefix        CDMD
        NaviCode        *
        PageName        ASP.brief_result_aspx
        __        Tue Jun 10 2014 12:08:21 GMT+0800 (CST)
        action
        db_opt        中国优秀博硕士学位论文全文数据库
        db_value        中国博士学位论文全文数据库,中国优秀硕士学位论文全文数据库
        his        0
        issen        1
        sen_1_sel        /NEAR 20
        sen_1_value1        信息
        sen_1_value2        某种程度
        sen_2_logical        and
        sen_2_sel        /SEN 20
        sen_2_value1        证券市场
        sen_2_value2        最具敏感性
        ua        1.21
        */
        GETcnki gs = new GETcnki();

        gs.readContentFromGet(GET_URL);
    }
}

```


[1]: http://www.cnki.com.cn/
[2]: https://zh.wikipedia.org/wiki/超文本传输协议
[3]: http://zh.wikipedia.org/wiki/Cookie
