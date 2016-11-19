---
layout: post
title: "白相Raspberrypi-4-装个眼睛哦"
date: 2016-07-31 16:38
comments: true
categories: [raspberrypi,树莓派,hardware,openCV,python]
---

###装个眼睛？
* 使用USB摄像头当作树莓派的眼睛。
* 利用OPENCV作为树莓派感知外部世界图像的处理工具。
* 本片文章目的是使得Pi能识别移动物体，具备入侵检测的功能。
* 参考文章 [1][1] [2][2] [3][3]

<!-- more -->

###准备
* 为Pi安装OPENGL兼容套件
* 
```
sudo apt-get install mesa-utils
sudo apt-get install libgl1-mesa-dri libgl1-mesa-swx11
```
* 安装python openCV 开发环境
* 
```
sudo apt-get install python-opencv libopencv-dev python-numpy python-dev
sudo apt-get install libjpeg8-dev libtiff4-dev libjasper-dev libpng12-dev
sudo apt-get install libgtk2.0-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libatlas-base-dev gfortran
```

* 推荐使用x11vnc
 * 使用x11vnc 作为VNC的服务端软件
 * *sudo apt-get install x11vnc*
 * 原因：具有支持openGL的功能
 * 不会出现 如 `window system doesn't support opengl` 的错误

* 测试
 * 测试opencv是否能够正常工作
 * python 代码如下

```
import cv2

print cv2.__version__
img = cv2.imread("a.jpg")
cv2.namedWindow("Image",cv2.CV_WINDOW_AUTOSIZE)
cv2.imshow("Image",img)
cv2.waitKey(0)
cv2.destoryAllWindows()
```
 * 如果没有正常现实图片a.jpg，说明开发环境仍有问题，请务必处理。
    
### 检测和跟踪运动对象
* 简单原理
 * 通过摄像头捕获的图像交由python的openCV库进行处理
 * 对每一张图像继续灰度化后，比较前后两张图像的不同
 * 不同的区域即认为是运动的对象。
* 效果
 * 
    <video id="video" controls="" preload="none" >
        <source id="mp4" src="/images/motion_detector.mp4" type="video/mp4">
    </video>

* python代码
 * 使用方法如下
```
python motion_detector.py -c conf.json
```
    
```
python motion_detector.py -c conf.json --video videos/example_01.mp4
```
 * 配置文件conf.json
 
    ```
    {
    "min-area":1500,
	"show-thresh":true,
	"show-frameDelta":true,
	"decay-time":1000,
	"delta-thresh":25
    }
    ```
 * 主要py代码如下

{% codeblock lang:python motion_detector.py %}
# -*- coding:utf-8 -*-

# import the necessary packages
import argparse
import datetime
#import imutils
import time
import cv2
import json

# construct the argument parser and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-v", "--video", help="path to the video file")
ap.add_argument("-c", "--conf" , required=True,help="path to the JSON config file")
args = vars(ap.parse_args())

conf = json.load(open(args["conf"]))

# 开启优化
if (not cv2.useOptimized()):
    cv2.setUseOptimized(True)


# if the video argument is None, then we are reading from webcam
if args.get("video", None) is None:
	camera = cv2.VideoCapture(0)
	time.sleep(3.5)

# otherwise, we are reading from a video file
else:
	camera = cv2.VideoCapture(args["video"])
	
# find opencv version
print cv2.__version__
(major_ver,minor_ver,subminor_ver,fin_ver) = (cv2.__version__).split('.')

if int(major_ver) < 3 :
	fps = camera.get(cv2.cv.CV_CAP_PROP_FPS)
else:
	fps = camera.get(cv2.CV_CAP_PROP_FPS)

print fps
print "Frames per second :{0}".format(fps)

# initialize the first frame in the video stream
firstFrame = None
avg = None
#avg1 = None
t = time.time()
decay = conf["decay-time"]

num_frames = 0

# loop over the frames of the video
while True:
	
	start = time.time()
	
	# grab the current frame
	(grabbed, frame) = camera.read()
	text = "safe"

	# if the frame could not be grabbed, reached the end of the video
	if not grabbed:
		break

	# resize the frame, convert it to grayscale, and blur it
	# frame = imutils.resize(frame, width=500)
	gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
	# gray = cv2.GaussianBlur(gray, (21, 21), 0)

	# if the first frame is None, initialize it
	if firstFrame is None:
		firstFrame = gray.copy().astype("float")
		#avg1 = firstFrame
		avg = firstFrame
		continue

	now = time.time()
	if (now -t >= decay):
		t = now
		# compute the absolute diff between current and first frame
		cv2.accumulateWeighted(avg,firstFrame,0.4)
		# firstFrame = (1-alpha)*firstFrame+alpha*avg
		# firstFrame = 0.6*firstFrame + 0.4*avg
	
	cv2.accumulateWeighted(gray,avg,0.05)
	# 更新一个动态平均数 avg = (1-alpha)*avg+alpha*gray
	# avg = 0.95 * avg + 0.05 * gary
	
	frameDelta = cv2.absdiff(gray,cv2.convertScaleAbs(avg))
	# 基于加权平均数的帧，当前帧减去加权平均值，得到帧变化量
	
	thresh = cv2.threshold(frameDelta, conf["delta-thresh"], 255, cv2.THRESH_BINARY)[1]
	# 阈值化 >25 255

	# dilate the thresholded image to fill in holes, then find contours
	# on thresholded image
	thresh = cv2.dilate(thresh, None, iterations=2)
	(cnts, _) = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
		cv2.CHAIN_APPROX_SIMPLE)

	# loop over the contours
	for c in cnts:
		# if the contour is too small, ignore it
		if cv2.contourArea(c) < conf["min-area"]:
			continue

		# compute the bounding box for the contour, draw it on the frame,
		# and update the text
		(x, y, w, h) = cv2.boundingRect(c)
		cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)
		text = "someone"

	num_frames += 1 
	fps = num_frames /(time.time() - start)
	num_frames = 0

	# draw the text and timestamp on the frame
	cv2.putText(frame, "Room Status: {}".format(text.encode('utf-8')), (10, 20),
		cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 255), 2)
	cv2.putText(frame, datetime.datetime.now().strftime("%A %d %B %Y %I:%M:%S%p" + " FPS:{}".format(str(fps))),
		(10, frame.shape[0] - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.35, (0, 0, 255), 1)

	# show the frame and record if the user presses a key
	
	cv2.imshow("安全监控画面", frame)
	
	if conf["show-thresh"]:
		cv2.imshow("阈值化画面-Thresh", thresh)

	if conf["show-frameDelta"]:
		cv2.imshow("畸变画面-Frame Delta", frameDelta)
	
	key = cv2.waitKey(1) & 0xFF

	# if the `q` key is pressed, break from the lop
	if key == ord("q"):
		break

# cleanup the camera and close any open windows
camera.release()
cv2.destroyAllWindows()

{% endcodeblock %}


[1]:http://python.jobbole.com/81593/
[2]:http://python.jobbole.com/81645/
[3]:http://www.pyimagesearch.com/2015/05/25/basic-motion-detection-and-tracking-with-python-and-opencv/



