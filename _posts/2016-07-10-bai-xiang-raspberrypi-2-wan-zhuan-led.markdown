---
layout: post
title: "白相Raspberrypi-2-玩转LED"
date: 2016-05-31 19:52
comments: true
categories: [raspberrypi,树莓派,hardware,GPIO,python]
---
###为何要玩LED
 * led是体验树莓派控制硬件魔力的最简单方法。
 * 通过[GPIO][1](General purpose input/output)来实现与外部硬件的交互。

<!-- more -->

###准备工作
 * 树莓派 * 1
 * 草帽型led灯 * 1
 * 杜邦线 * 2
 * 可附带工具
  * T字板：T型GPIO扩展板
  * 面包板：面包电路板

###安装LED
 * 如下图
 * ![gpio_led](/images/gpio_led.png)
 * GPIO口
 * ![GPIO](/images/GPIO.jpg)

###使用python控制GPIO
 * 使用著名的[RPi.GPIO][2]包
 * exp1
{% codeblock lang:python led.py %}
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)
GPIO.setup(18,GPIO.OUT)

while True:
    GPIO.output(18,GPIO.HIGH)
    time.sleep(0.1)
    GPIO.output(18,GPIO.LOW)
    time.sleep(0.1)
GPIO.cleanup()
{% endcodeblock %}
 
 * 效果1
 * <video id="video" controls="" preload="none" >
      <source id="mp4" src="/images/led1.mp4" type="video/mp4">
      <p>Your user agent does not support the HTML5 Video element.</p>
    </video>

 *exp2
{% codeblock lang:python led2.py %}
#-*- coding:utf-8 -*-
import RPi.GPIO as GPIO

GPIO.setmode(GPIO.BCM)
GPIO.setup(18,GPIO.OUT)

p = GPIO.PWM(18,1)
# channel frequency(Hz 每秒几次)
p.start(90)
# dc duty cycle 占空比 0-100 在一个周期内高电平的比

input('Enter to stop')
p.stop()
GPIO.cleanup()

{% endcodeblock %}

 * 效果2
 * <video id="video" controls="" preload="none" >
      <source id="mp4" src="/images/led2.mp4" type="video/mp4">
      <p>Your user agent does not support the HTML5 Video element.</p>
    </video>

###玩的开心啊

[1]:https://zh.wikipedia.org/zh/GPIO
[2]:https://sourceforge.net/projects/raspberry-gpio-python/

