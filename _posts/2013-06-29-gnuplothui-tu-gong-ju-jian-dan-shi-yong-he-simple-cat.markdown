---
layout: post
title: "gnuplot绘图工具简单使用和simple cat"
date: 2013-06-29 20:10
comments: true
categories: [shell,gnuplot,sudo apt-get install,cut,awk,c,fread]
author: wuwenjie
---

# gnuplot绘图工具简单使用
##安装 
* sudo apt-get install gnuplot

##使用
* $ gnuplot
* 使用实例:[gnuplot 让您的数据可视化](http://www.ibm.com/developerworks/cn/linux/l-gnuplot/)
<!-- more -->
##我的实例
* 画[600600青岛啤酒](http://quote.eastmoney.com/sh600600.html)走势图和5日[简单移动平均线](http://zh.wikipedia.org/zh/%E7%A7%BB%E5%8B%95%E5%B9%B3%E5%9D%87)
    * 600600青岛啤酒的[日线资料(19930827-20130628)](https://mega.co.nz/#!F4AFjaDA!S0qSqRGszmWqQdA6fQUBsl-C0bJhJtXAvvnOwEjWGAo)从[钱龙](http://www.qianlong.com.cn/)下载
    * 用[Gnumeric](https://projects.gnome.org/gnumeric/)或[Kingsoft Spreadsheet](http://community.wps.cn/download/)打开并另存为[CSV格式](http://zh.wikipedia.org/wiki/CSV)
    * 型如
            * 时间,涨跌,幅度%,开盘,最高,最低,收盘,成交量,成交额
            * 1993/08/27,+0.00,+0.00,15,15.3,13.45,14,168226,24084
            * 1993/08/30,-0.74,-5.29,13.7,13.7,13.17,13.26,32937,4442
            * 1993/08/31,-0.36,-2.71,13.18,13.37,12.8,12.9,31062,4051
        删除第一行，600600.csv
    * 使用cut截取 时间 
        * ```shell
            cut -d , -f 1,7 600600.csv >600600.txt
        ```
        * 型如
                * 1993/08/27,14
                * 1993/08/30,13.26
                * 1993/08/31,12.9
                * 1993/09/01,12.92
    * 使用SHELL生成移动平均线数据文本600600ma5.txt
```bash
            #!/bin/sh
            #MA 5
            #
            I=1;
            FileTime="";
            FileNum=0;
            OUT=0;
            NA=0;
            NB=0;
            NC=0;
            ND=0; #前四天数值
            NE=0;

            while read list
            do
                FileNum=`echo $list |cut -d , -f 2 `;   #截取
                FileTime="`echo $list |cut -d , -f 1 `";

	            if [  $(($I%5)) -eq 1 ] # =
		            then 
		    	    NA=$FileNum;
	            elif [  $(($I%5)) -eq 2 ]
			        then 
			        NB=$FileNum;
	            elif [  $(($I%5)) -eq 3 ]
			        then 
		    	    NC=$FileNum;
	            elif [  $(($I%5)) -eq 4 ]
			        then 
			        ND=$FileNum;
	            elif [  $(($I%5)) -eq 0 ]
			        then 
			        NE=$FileNum;
	            fi
            
        	    if [ $I -ge 5 ]
		            then 	
		    	    OUT=`awk ' BEGIN { print ('$NA'+'$NB'+'$NC'+'$ND'+'$NE')/5 }'`;
		    	    echo "$FileTime,$OUT" >> 600600ma5.txt
	            fi
                
	            I=$(($I+1));
	        done <600600.txt
```
        
        * 型如(600600ma5.txt)
                * 1993/09/02,13.22
                * 1993/09/03,13.036
                * 1993/09/06,13
                * 1993/09/07,12.94
                * 1993/09/08,12.806
    * 编写600600.gunplot脚本画出图像

```bash
                set datafile separator "," #设置数据文件分割符为,
                set xdata time          #设置x数据为时间类型
                set timefmt "%Y/%m/%d"  #设置时间格式 

                set grid    #设置网格

                set title '600600 青岛啤酒' #设置标题
                set xlabel '时间'       #设置X轴名称
                set ylabel '收盘价'     #设置y轴名称

                plot "./600600.txt" using 1:2 \
                title '600600' \
                with lines #points
                #使用600600.txt的1、2列画线，名称为600600，
                
                #set terminal png
                #set output "output2.png"

                replot "./600600ma5.txt" using 1:2 \
                title 'MA5'    \
                with lines#points
                #添加一条使用600600ma5.txt,即移动平均线数据，的线，名称为MA5
                
                #pause 10	# help pause 保持窗口
```


* 使用gnuplot批处理模式运行600600.gunplot
                gnuplot> load "./600600.gunplot"
    * 效果
        * ![600600&MA5_withlinespoints](/images/600600&MA5_withlinespoints.png)
        * ![600600&MA5_withlines](/images/600600&MA5_withlines.png)
        * ![600600scale](/images/600600scale.png)
        * ![600600&MA5_withlinespoints0629](/images/600600&MA5_withlinespoints0629.png)

----

----
# simple Cat
```c
#include <stdio.h>
#include <sys/stat.h>
#include <malloc.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    FILE *fp;
	int i, j, k = 1;
	char a[] = "";


	for (i = 0; i < argc && argv[i + 1] != NULL; i++)
	{
		printf("\nargv[%d] is: %s\n", i + 1, argv[i + 1]);

		fp = fopen(argv[i + 1], "r");

		k = getFSSystemCall(argv[i + 1]);
		printf("FileSize:%d\n", k);

		// 请求动态内存
		char *a_p = a;
		a_p = (char *)malloc(sizeof(char) * k);
		if (a_p == NULL)
		{
			puts("malloc null");
			return 1;
		}


		if (fp == NULL)
		{
			printf("\e[1;31mNO SUCH FILE\e[0m\n\n");
			printInfo();
			return 1;
		}
        //size_t fread ( void   *buffer,  size_t size,  size_t count,  FILE *stream) ;
    	//返回值：实际读取的元素个数.如果返回值与count不相同,则可能文件结尾或发生错误.
		for (j = 0; fread(a_p + j, sizeof(char), 1, fp) == 1; j++)
		{
			fseek(fp, j + 1, 0);
			printf("%c", *(a_p + j));
		}

		fclose(fp);
		// 释放
		free(a_p);
	}
	return 0;
}

int getFileSize(FILE * fps)
{
	fseek(fps, 0L, SEEK_END);
	int size = ftell(fps);
	// <stdio.h> long ftell( FILE *s )
	// 返回s当前的文件位置
	// 错误返回-1
	return size;
}

// include <sys/stat.h> 
int getFSSystemCall(char *strFileName)
{
	struct stat temp;
	stat(strFileName, &temp);
	return temp.st_size;
}

printInfo()
{
	printf("\e[1;33m1cat:simple cat Ver:0.0.3 20130528\e[0m\n");
	printf("\e[1;33mAuthor:wuwenjie.tk\e[0m\n");
}
```
