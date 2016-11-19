---
layout: post
title: "白相Raspberrypi-3-装个耳朵哦"
date: 2016-06-14 20:31
comments: true
categories: [raspberrypi,树莓派,hardware,GPIO,python]
---
###BEGIN
* [前一篇][1]已经讲到如何玩LED了
* 这次说给树莓派装个耳朵

<!-- more -->

###耳朵？
* 用声音传感器作为它的耳朵
* 声音传感器的样子
* ![sound_moudle](/images/sound_moudle.png)

###材料与安装
* 准备若干杜邦线、LED、声音传感器等
* LED正极接GPIO18口
* 声音传感器接GPIO4
* 其他接地GND

###让耳朵听到声音1
* 这种方法比较简单，便于理解，但不推荐日常使用
* 
{% codeblock lang:python sound.py %}
#-*- coding:utf-8 -*-

import RPi.GPIO as GPIO
import time

# LED正极连接的GPIO口 GPIO18
LED = 18

# 声音感应器OUT口连接的GPIO口 GPIO4
SENSOR = 4

# 当前LED灯的开关状态
flg = False

GPIO.setmode(GPIO.BCM)

# 指定GPIO4（声音感应器的OUT口连接的GPIO口）的模式为输入模式
# 默认拉高到高电平，低电平表示OUT口有输出
GPIO.setup(SENSOR, GPIO.IN, pull_up_down=GPIO.PUD_UP)

# 指定GPIO18（LED长针连接的GPIO针脚）的模式为输出模式
GPIO.setup(LED, GPIO.OUT)

try:
        while True:
                # 检测声音感应器是否输出低电平，若是低电平，表示声音被检测到，点亮或关闭LED灯
                if (GPIO.input(SENSOR) == 0):
                        flg = not flg
    		GPIO.output(LED, flg)
			# 稍微延时一会，避免刚点亮就熄灭，或者刚熄灭就点亮。
			time.sleep(1.5)
except KeyboardInterrupt:
	pass

GPIO.cleanup()

{% endcodeblock %}


###让耳朵听到声音2
* 这种方法用到[GPIO的中断处理1][2] [2][3]
* 执行：echo 4 > /sys/class/gpio/export
* 声音感应器OUT口连接的GPIO口 GPIO4
* 
{% codeblock lang:python sound2.py %}

#-*- coding:utf-8 -*-

import RPi.GPIO as GPIO
import time

print GPIO.VERSION

# LED正极连接的GPIO口 GPIO18
LED = 18

# 声音感应器OUT口连接的GPIO口 GPIO4
# echo 4 > /sys/class/gpio/export
SENSOR = 4

# 当前LED灯的开关状态
flg = False

GPIO.setmode(GPIO.BCM)

# 指定GPIO4（声音感应器的OUT口连接的GPIO口）的模式为输入模式
# 默认拉高到高电平，低电平表示OUT口有输出
GPIO.setup(SENSOR, GPIO.IN, pull_up_down=GPIO.PUD_UP)

# 指定GPIO18（LED长针连接的GPIO针脚）的模式为输出模式
GPIO.setup(LED, GPIO.OUT)

#GPIO.add_event_detect(SENSOR,GPIO.RISING,callback=my_callback)
while 1:
        try:
                #print "\nWaiting for falling edge on port "+str(SENSOR)
                GPIO.wait_for_edge(SENSOR,GPIO.FALLING)
                #print "\nFalling edge detected."

                # 检测声音感应器是否输出低电平，若是低电平，表示声音被检测到，点亮或关闭LED灯
                if (GPIO.input(SENSOR) == 0):
                        flg = not flg
                        GPIO.output(LED, flg)
                        # 稍微延时一会，避免刚点亮就熄灭，或者刚熄灭就点亮。
                        #time.sleep(1.5)
                        
        except KeyboardInterrupt:
                GPIO.cleanup()

GPIO.cleanup()


{% endcodeblock %}


###效果
* <video id="video" controls="" preload="none" >
    <source id="mp4" src="/images/PIsound.mp4" type="video/mp4">
    <p>Your user agent does not support the HTML5 Video element.</p>
</video>

###玩的开心啊

[1]:http://wuwenjie.tk/blog/2016/05/31/bai-xiang-raspberrypi-2-wan-zhuan-led/
[2]:http://hugozhu.myalert.info/2013/04/08/27-interrupts-with-gpio-pins.html?utm_source=tuicool&utm_medium=referral
[3]:http://dreamcolor.net/archives/rpi-gpio-module-inputs.html
