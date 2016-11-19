---
layout: post
title: "用Python分析白银T+D价格走势"
date: 2016-08-19 21:23
comments: true
categories: [python,Data mining,技术分析,投资,白银,指标]
---
###工具与数据
* numpy scipy matplotlib
* 新浪历史数据

###效果
* ![agtd](/images/plotagtd.png)

<!-- more -->

###代码

{% codeblock lang:python plotAGTD.py %}

# -*- coding:utf-8 -*-

'''
sudo apt-get install python-dev
sudo apt-get install python-scipy
sudo apt-get install python-numpy
sudo apt-get install python-matplotlib
 
sudo easy_install -U distribute
sudo pip install matplotlib

'''

import matplotlib as mpl
mpl.use('Agg')
import matplotlib.pyplot as plt
from matplotlib.dates import DateFormatter
from matplotlib.ticker import FuncFormatter
import datetime
import numpy as np
import time

import httplib
import StringIO

import subprocess

now_t = time.time()
conn = httplib.HTTPConnection('vip.stock.finance.sina.com.cn',80,timeout=20)

now = time.strftime("%Y-%m-%d", time.localtime())
# print now
conn.request("GET", 
    "/q/view/download_gold_history.php?breed=AGTD&start=1992-06-05&end="+now)
r1 = conn.getresponse()
# print r1.status, r1.reason
raw_data = r1.read()
conn.close()

print "HTTP time is "+ str(time.time() - now_t)[0:5]

raw_data = StringIO.StringIO(raw_data)


#file = open('AGTD.xls')
#firstline = unicode(file.readline(),'gb2312').encode('utf-8')
firstline = unicode(raw_data.readline(),'gb2312').encode('utf-8')

data = [firstline]

for each_line in raw_data:
	data.append(each_line.split('\t'))

close=[]
date =[]

i= 0
for da in data:
	if i != 0:
		close.append(da[5])
		date.append(da[0])
	i += 1

close.reverse()
date.reverse()

datelist= [datetime.date(int(ys), int(ms), int(ds)) for ys, ms, ds 
in [ da.split('-') for da in date ]]

close = close[2000:]
datelist = datelist[2000:]



def ma(values, window):
	values = np.array(values,dtype=float)
	weights = np.repeat(1.0, window)/window
	ma = np.convolve(values, weights, 'valid')
	return ma


zhfont = mpl.font_manager.FontProperties(
	fname='/usr/share/fonts/truetype/droid/DroidSansFallback.ttf')
mpl.rcParams['axes.unicode_minus'] = False


left, width = 0.1, 0.8
rect1 = [left, 0.45, width, 0.45]
rect2 = [left, 0.10, width, 0.34]

fig = plt.figure()


axescolor = '#f6f6f6'  # the axes background color

ax = fig.add_axes(rect1, facecolor=axescolor)  # left, bottom, width, height
ax2 = fig.add_axes(rect2,facecolor=axescolor, sharex=ax)


ax.set_title(u'【白银递延AGTD 日线】 更新日期：%s' % 
	str((time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()))),
	fontsize = 14, fontweight='bold',fontproperties=zhfont)
# ax.set_title(u'AGTD 日线 ',fontproperties=zhfont)

for label in ax.xaxis.get_ticklabels():
	label.set_visible(False)

for label in ax2.xaxis.get_ticklabels():
	label.set_rotation(25)


ax.plot(datelist,close,'.-')

BBI = (ma(close,3)[48-3:]+ma(close,6)[48-6:]
	+ma(close,12)[48-12:]+ma(close,24)[48-24:]+ma(close,48))/5

BBIST = (ma(close,3)[6-3:]+ma(close,6))/2
BBIMT = ma(BBI,40)
BBILT = ma(BBI,200)


# ax.plot(datelist[48-1:],BBI)
ax.plot(datelist[6-1:],BBIST)
ax.plot(datelist[48+40-1-1:],BBIMT)
ax.plot(datelist[48+200-1-1:],BBILT)

'''
ax.plot(datelist[5-1:],ma(close,5))
ax.plot(datelist[30-1:],ma(close,30))
ax.plot(datelist[90-1:],ma(close,90))
ax.plot(datelist[180-1:],ma(close,180))
'''


N1 = 5
N2 = 30
N3 = 100
dis = ma(close,N1)[N2-N1:] - ma(close,N2)
disma = ma(dis,N1)
middle_disma = ma(dis,N3)

ax2.plot(datelist[N2-1:],dis)
ax2.plot(datelist[N2+N1-1-1:],disma)
ax2.plot(datelist[N3+N2-1-1:],middle_disma)
ax2.text(0.025, 0.95, u'强弱W_MA (%d, %d, %d)' % (N1, N2, N3), va='top',
         transform=ax2.transAxes, fontsize=10,fontproperties=zhfont)

ax.grid(True)
ax2.grid(True)

ax.autoscale_view() # 自动调整坐标轴范围
ax2.autoscale_view()

# position bottom right
fig.text(0.67, 0.001, 'from wuwenjie',
         fontsize=60, color='gray',
         ha='right', va='bottom', alpha=0.6)

plt.show()

fig.set_size_inches(9.25, 5.25)
plt.savefig('agtd.png', dpi=75)

# sudo apt-get install pngquant
p=subprocess.Popen("pngquant -force 64 agtd.png",
	shell=True,stderr=subprocess.PIPE)  
if p.wait() != 0:
	print u"压缩错误"+p.stderr.read()




{% endcodeblock %}

