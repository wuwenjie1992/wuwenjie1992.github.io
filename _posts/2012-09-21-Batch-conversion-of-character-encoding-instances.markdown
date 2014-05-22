---
layout: post
title: "批量转换字符编码的实例（iconv）"
author: "wuwenjie"
date: 2012-09-21 10:47
comments: true
categories: [shell，bash，iconv，awk,read]
---
<em><strong>前言</strong></em>

借的书里光盘有C代码，

可也抄来看看，

但中文说明乱码。

行如：// 68ÎªÎÕÊÖÏûÏ¢µÄ¹Ì¶š³€¶È

而且文件较多，

以 .c|.h结尾，

手动改的话就太笨了，
<!-- more -->
发挥linux shell的优势的时候来了。

&nbsp;

代码
```bash
#!/bin/sh
#iconv2
#批量转换字符编码

 ls -al | awk '/.c|h/{print $9 >"l.txt"}' 
   #将LS出来的结果，管道到，用AWK找到第九列中为.c或.h的文件，输出到L.txt

  while read  list #读取（read）l.txt，将read的结果命名为 list

    do
      echo $list;   #显示list的neir
      iconv -f  gb18030  -t  utf-8  $list  > $list.new;
                 #将list的编码从gb18030改为utf8，输入list.new中
     rm $list;   #删除list文件（未转码的）
     mv $list.new $list;   #将list.new文件重命名为list

    done < l.txt #从l.txt输入


rm l.txt;rm iconv2.sh;  #删除l.txt与iconv2.sh
```



&nbsp;

&nbsp;
注意：chmod u+x iconv2.sh 执行：./iconv2.sh
