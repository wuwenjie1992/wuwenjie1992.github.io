---
layout: post
title: "[改]Gstreamer 播放MP4 并 添加文字水印 "
date: 2015-03-31 17:58
comments: true
categories: [c,GStreamer]
---
##提要
* 之前已经写过[JNI播放mp3][1]的文章，使用[Gstreamer][2]流式多媒体框架。
* 这次直接使用C播放MP4文件，文件对应解码器使用[ffmpeg -i 视频文件][3]命令察看。

##要点
* 使用动态方法链接音频、视频的接收垫片。
* 使用gst_element_seek函数循环播放文件，本用来跳转播放。
* 使用textoverlay组件为视频添加文字水印。

<!-- more -->

##概览
```text
                                    test-pipline
----------------------------------------------------------------------------------------
                          [sink_pad] >
                            > videoqueue -> textoverlay -> video-decoder -> video-output
file-source -> demuxer ->
                          [sink_pad] >
                            > audioqueue -> audio-decoder -> audio-output
                          
----------------------------------------------------------------------------------------

```

##code
```c
#include <gst/gst.h>
#include <glib.h>

/* 
 * 编译：
 * gcc gst_mp4.c -o gst_mp4 $(pkg-config --cflags --libs gstreamer-0.10) 
 * 
 * 运行：
 * ./gst_mp4 test.mp4
 * 
 * 测试：
 * gst-launch-0.10 filesrc location="/test.mp4" ! qtdemux name=qtdemuxer \
 * qtdemuxer. ! queue ! ffdec_h264! \
 * textoverlay deltax= 20 deltay= 20 font-desc="Sans Bold 8" text="wuwnjie.tk" \
 * halign = "left" shaded-background = "TRUE" valign="top" wrap-mode="char" \
 * xpad = "20" ypad = "20" ! autovideosink \
 * qtdemuxer. ! queue ! ffdec_aac ! autoaudiosink
 * 
 * 参考：
 * blog.csdn.net/mengyy_linux/article/details/13261935
 * 
 * stackoverflow.com/questions/16006142/
 * playing-avi-file-with-gstreamer-sdk-in-windows
 * 
 * gstreamer-devel.966125.n4.nabble.com/
 * GStreamer-C-Code-to-play-video-td3715064.html
 * 
 * stackoverflow.com/questions/12902574/
 * using-textoverlay-and-timeoverlay-together
 * 
 * gstreamer.freedesktop.org/data/doc/gstreamer/0.10.1/
 * gst-plugins-base-plugins/html/gst-plugins-base-plugins-textoverlay.html
 * */

GstElement *pipeline,*source,*demuxer,
    	*queue,*textoverlay,*decoder,*sink,
		*Audioqueue,*Audiodecoder,*Audiosink; 

static gboolean bus_call(GstBus *bus,GstMessage *msg,gpointer data){
	GMainLoop *loop = (GMainLoop *)data;
    
	switch(GST_MESSAGE_TYPE(msg)){
    
	case GST_MESSAGE_EOS:
	
	g_print("播放结束，尝试重播,Ctrl+c 结束!\n");
	/*在播放结束后，尝试重播*/
	if (!gst_element_seek(pipeline,
			1.0,GST_FORMAT_TIME,GST_SEEK_FLAG_FLUSH,
			GST_SEEK_TYPE_SET, 0,//2 seconds (in nanoseconds)
			GST_SEEK_TYPE_NONE,GST_CLOCK_TIME_NONE)) {
				g_print("重播失败，准备退出!\n");
				g_main_loop_quit(loop);
		}
	gst_element_set_state (pipeline,GST_STATE_PLAYING);
		break;
	case GST_MESSAGE_ERROR: {
		gchar	*debug;
		GError	*error;
		gst_message_parse_error(msg,&error,&debug);
		g_free(debug);
		g_printerr("错误: %s\n",error->message);
		g_error_free(error);
		g_main_loop_quit(loop);
		break;
	}
	default:
		break;
	}
	return(TRUE);
}

static void on_pad_added(GstElement *element,GstPad *pad,gpointer data){

	char video[9]="video_00"; //char audio[9]="audio_00";
	
	gchar *new_pad_name = GST_PAD_NAME(pad); 
	
	g_print("收到来自'%s'的新垫片'%s'\n",GST_ELEMENT_NAME (element),
			new_pad_name);
	
	GstPad *audio_sink_pad ,*video_sink_pad;
	video_sink_pad = gst_element_get_static_pad(queue,"sink"); 
	audio_sink_pad = gst_element_get_static_pad(Audioqueue,"sink");  
	
	if(!strncmp(new_pad_name,video)){
		g_print("链接%s垫片\n",new_pad_name);  
		gst_pad_link(pad,video_sink_pad);
	}else{
		g_print("链接%s垫片\n",new_pad_name);  
		gst_pad_link(pad,audio_sink_pad); 
	}
	
	gst_object_unref(video_sink_pad);
	gst_object_unref(audio_sink_pad);
}

int main(int argc,char *argv[]){
	
	GMainLoop *loop;
	GstBus *bus;
	gst_init(&argc,&argv);
	loop = g_main_loop_new(NULL,FALSE);
	
	if (argc != 2)
	{
		g_printerr("用法: %s <mp4 filename>\n",argv[0]);
		return(-1);
	}
	
	//创建用来容纳元件的新管道
	pipeline = gst_pipeline_new("test-pipeline");
	
	//生成用于读取硬盘数据的元件
	source   = gst_element_factory_make("filesrc","file-source");
	
	//流分离器
	demuxer  = gst_element_factory_make("qtdemux","demuxer");
	
	//队列元件 同步输出设备，播放视频、音频流，双线程可以独立运行并达到更好的同步效果。
	queue = gst_element_factory_make("queue","videoqueue");
	
	//向视频添加文字
	textoverlay = gst_element_factory_make("textoverlay","textoverlay");
	
	//视频解码器 GStreamer encountered a general stream error 
	//GStreamer 遇到了常规流错误 ffdec_h264 ffdec_mpeg4
	decoder  = gst_element_factory_make("ffdec_h264","video-decoder");
	
	//视频回放元件
	sink = gst_element_factory_make("autovideosink","video-output");
	
	//队列元件
	Audioqueue = gst_element_factory_make("queue","audioqueue");
    
    //音频解码器
	Audiodecoder = gst_element_factory_make("ffdec_aac","audio-decoder");
	
	//音频回放元件
	Audiosink = gst_element_factory_make("autoaudiosink","audio-output");
	
	if (!pipeline || !source || !demuxer || !queue || !decoder 
		|| !textoverlay || !sink || !Audioqueue || !Audiodecoder 
		|| !Audiosink){
			g_printerr("有某一元件无法创建，退出！\n");
			return(-1);
	}
	
	//设置 source的location 参数 文件地址
	g_object_set(G_OBJECT(source),"location",argv[1],NULL);
	
	//得到 管道的消息总线
	bus = gst_pipeline_get_bus(GST_PIPELINE(pipeline));
	gst_bus_add_watch(bus,bus_call,loop); //添加消息监视器
	gst_object_unref(bus);
	
	//添加元件
	gst_bin_add_many(GST_BIN(pipeline),
			  source,demuxer,queue,decoder,textoverlay,sink,
			  Audioqueue,Audiodecoder,Audiosink,NULL);
			  
	//分别链接元件
	gst_element_link(source,demuxer);
	gst_element_link_many(queue,decoder,textoverlay,sink,NULL);
	gst_element_link_many(Audioqueue,Audiodecoder,Audiosink,NULL);
	
	//按照不同的流，动态链接不同的接收衬垫 sink pad
	g_signal_connect(demuxer,"pad-added",G_CALLBACK(on_pad_added),NULL);
	
	//设置视频文字
	g_object_set(G_OBJECT(textoverlay),"text","wuwenjie.tk",NULL); //文字
	g_object_set(G_OBJECT(textoverlay),"deltax",20,NULL);	//x轴方向
	g_object_set(G_OBJECT(textoverlay),"deltay",20,NULL);	//y轴方向
	g_object_set(G_OBJECT(textoverlay),"font-desc","Sans Bold 8",NULL);//字体
	g_object_set(G_OBJECT(textoverlay),"halign","left",NULL);	//水平对齐方向
	g_object_set(G_OBJECT(textoverlay),"shaded-background",TRUE,NULL);	//背景
	g_object_set(G_OBJECT(textoverlay),"valign","top",NULL);	//竖直方向
	g_object_set(G_OBJECT(textoverlay),"wrap-mode","word",NULL);//绘制文本
	g_object_set(G_OBJECT(textoverlay),"xpad",20,NULL); //水平
	g_object_set(G_OBJECT(textoverlay),"ypad",20,NULL); //竖直
	
	//开始播放
	g_print("开始播放 %s\n",argv[1]);
	gst_element_set_state(pipeline,GST_STATE_PLAYING);
	g_main_loop_run(loop);
	
	//停止管道处理流程
	gst_element_set_state(pipeline,GST_STATE_NULL);
	//释放占用的资源
	gst_object_unref(GST_OBJECT(pipeline));
	return(0);
}
```



[1]:/blog/2015/02/28/gai-java-native-interface-bo-fang-mp3/
[2]:http://zh.wikipedia.org/wiki/GStreamer
[3]:http://linux-wiki.cn/wiki/zh-hans/视频/照片的编码和拍照信息
