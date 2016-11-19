---
layout: post
title: "[改]Java Native Interface 播放MP3 "
date: 2015-02-28 14:32
comments: true
categories: [java,JNI,c,GStreamer]
---
#前言与背景
* JAVA对多媒体的处理能力较弱，但JNI给了我们一扇逃生门（"escape hatch"）
* [JNI][1]提供给Java调用C语言、C++或被C、C++调用的功能
* 使得JAVA可以使用丰富的C、C++库，而且本地方法使程序运行更快

#如何播放MP3呢？
* 利用JNI调用著名的[GStreamer][2]流式多媒体框架进行播放。
* 它采用插件（plugin）和管道（pipeline）的体系结构，可以像搭积木一样简单地创建多媒体应用。 
* 并且GStreamer提供众多插件，易于扩展，以后可以考虑用Java播放视频。
<!-- more -->
#在debain系下安装GStreamer
* sudo apt-get install libgstreamer0.10-0 libgstreamer0.10-dev libgstreamer0.10-0-dbg
* sudo apt-get install gstreamer0.10-plugins-ugly
    *  使用mad解码器插件安装该包

#CODE

###1.先编写好JAVA代码，Jaudio.java

```java
// javac Jaudio.java
// javah -jni Jaudio
// java -Djava.library.path=. Jaudio

public class Jaudio{
    
	// JNI 调用的方法
	public native void playMP3(String url);
	
	static{
		System.loadLibrary("Jaudio"); //加载库
	}
	
	public void play(String url){
		
		playMP3(url);
		
	}
	
	// java方法可供JNI调用
	public static long nanoTime(){
		
		return System.nanoTime();
		
	}
	
	public static void main(String[] args){
		
		System.out.println("Wellcom to Jaudio! v0.0.1 20150211");
		
		//System.out.println(nanoTime());
		
		Jaudio audio = new Jaudio();
		audio.play(args[0]);
		
	}
	
}
```
###2.javac Jaudio.java编译
###3.利用 javah -jni Jaudio 生成Jaudio.h头文件
###4.使用gcc编译Jaudio.c
 * 编译命令，注：/usr/lib/jvm/java-1.7.0-openjdk-i386/include 为 jni.h路径
```text
gcc -fPIC -shared -static Jaudio.c -o libJaudio.so \
-I /usr/lib/jvm/java-1.7.0-openjdk-i386/include \
$(pkg-config --cflags --libs gstreamer-0.10) 
```
 * Jaudio.c
```c
#include <stdio.h>
#include <jni.h>

#include <gst/gst.h>
#include "Jaudio.h"

// gcc -fPIC -shared -static Jaudio.c -o libJaudio.so \
-I /usr/lib/jvm/java-1.7.0-openjdk-i386/include \
$(pkg-config --cflags --libs gstreamer-0.10) 

/*
 * symbol lookup error: libJaudio.so: undefined symbol: gst_init
 * gcc参数顺序相关的问题 http://m.oschina.net/blog/97611
 * 对于C/C++编译而言，读取编译选项是按照从左到右的顺序执行的 。当编译器遇到源文件时，
 * 开始对源文件中用到的函数进行解析，找到相对应函数实现（Definition of Function）。
 * 过程是按照先遇到不能解析的函数（unresolved function），
 * 然后在源文件选项后面的一些选项中寻找可能的函数体的信息，是这样的一个顺序进行的。
 * 包含函数体或者函数定义信息的编译选项出现在源文件之前，当编译器遇到不能解析的函数时，
 * 在源文件之后的选项中寻找相关的信息，也就是无法找到相关的函数定义。
 */

// java -Djava.library.path=. Jaudio

//定义消息处理函数
static gboolean bus_call(GstBus *bus,GstMessage *msg,gpointer data){
    GMainLoop *loop = (GMainLoop *) data;
    //这个是主循环的指针，在接受EOS消息时退出循环
    gchar *debug;
	GError *error;
    
    switch (GST_MESSAGE_TYPE(msg)){
        case GST_MESSAGE_EOS:
        
            g_print("播放结束\n");
            g_main_loop_quit(loop);
            //退出循环
            break;
        case GST_MESSAGE_ERROR:
			
			gst_message_parse_error(msg,&error,&debug);
			g_free(debug);
			g_printerr("错误:%s\n",error->message);
			g_error_free(error);
			g_main_loop_quit(loop);
			break;
        default:
             break;
    }
    return TRUE;
}

JNIEXPORT void JNICALL Java_Jaudio_playMP3(JNIEnv *env, jobject this, 
		jstring jstr){
	
	/*
	 * 所有的 JNI 调用都使用了 JNIEnv * 类型的指针，
	 * 习惯上在 CPP 文件中将这个变量定义为 evn，它是任意一个本地方法的第一个参数。
	 * env 指针指向一个函数指针表，在 VC 中可以直接用"->"操作符访问其中的函数。
	 * object指向在此Java中实例化的对象 LocalFunction 的句柄，相当于this指针。
	 */
	const char* str;
    str = (*env)->GetStringUTFChars(env,jstr, JNI_FALSE);
    // 从 jstr 字符串取得指向字符串 UTF 编码的指针
	printf("播放地址：%s\n",str); 
	
	jclass cls = (*env)->FindClass(env, "Jaudio");
	if(cls != 0){
		//printf("FindClass:%p\n",cls);
		jmethodID mid = 
			(*env)->GetStaticMethodID(env, cls, "nanoTime", "()J");
		
		if(mid != 0){
			
			//printf("GetMethodID:%p\n",mid);
			jlong jl = (*env)->CallStaticLongMethod(env,cls,mid);
			long long l = jl;
			printf("Java nanoTime：%lld\n",l);//long jlong signed 64bits
			
		}else{
			printf("GetMethodID err\n");
		}
	}else{
		printf("FindClass err\n");
	}
    
    GMainLoop *loop;
    GstElement *pipeline,*source,*decoder,*sink;//定义组件
    GstBus *bus;
    
    const gchar *nano_str;
    guint major, minor, micro, nano;
    
    gst_init(NULL,NULL);	//初始化
    loop = g_main_loop_new(NULL,FALSE);
    //创建主循环，在执行 g_main_loop_run后正式开始循环
    
    gst_version (&major, &minor, &micro, &nano);
    if (nano == 1) nano_str = "(CVS)";
    else if (nano == 2) nano_str = "(Prerelease)";
    else nano_str = "";
    printf("Power By\n\tJava Native Interface & GStreamer%d.%d.%d %s\n",
			major, minor, micro, nano_str);
    printf("\tJaudio.c v0.0.1 20150211\n");
    
    //创建管道和组件
    //创建用来容纳元件的新管道，管道是一个特殊的组件，可以更好的让数据流动
    pipeline = gst_pipeline_new("audio-player");
    
    //生成用于读取硬盘数据的元件
    source = gst_element_factory_make("filesrc","file-source");
    
    //创建解码器元件
    decoder = gst_element_factory_make("mad","mad-decoder");
    
    //创建音频回放元件
    sink = gst_element_factory_make("autoaudiosink","audio-output");

    /*if(!pipeline||!source||!decoder||!sink){
        g_printerr("One element could not be created.Exiting.\n");
        return -1;
    }*/
    if(!pipeline){
		g_printerr("GST管道不能创建!\n");
		exit(-1);
	}
    if(!source){
		g_printerr("GST播放文件不能创建!\n");
		exit(-1);
	}
	if(!decoder){
		g_printerr("GST解码元件不能创建\n");
		exit(-1);
	}
    if(!decoder){
		g_printerr("GST音频输出不能创建\n");
		exit(-1);
	}
    
    //设置 source的location 参数 文件地址.
    g_object_set(G_OBJECT(source),"location",str,NULL);
    
    //把组件添加到管道中
    gst_bin_add_many(GST_BIN(pipeline),source,decoder,sink,NULL);
    
    //依次连接组件
	gst_element_link_many(source,decoder,sink,NULL);
    
    //得到 管道的消息总线
    bus = gst_pipeline_get_bus(GST_PIPELINE(pipeline));
    
    //添加消息监视器
    gst_bus_add_watch(bus,bus_call,loop);
    gst_object_unref(bus);
   
	//开始播放
    gst_element_set_state(pipeline,GST_STATE_PLAYING);
    g_print("开始播放 -> %s\n",str);
    
    //开始循环
    g_main_loop_run(loop);
    
    //停止管道处理流程
    gst_element_set_state(pipeline,GST_STATE_NULL);
    //释放占用的资源
    gst_object_unref(GST_OBJECT(pipeline));
    
    g_print("拜拜，退出\n");
    //return 0;
	  
	(*env)->ReleaseStringUTFChars(env,jstr, (const char *)str ); 
	// 通知虚拟机本地代码不再需要通过 str 访问 Java 字符串。
	
}
```
###运行Jaudio
* 如果共享库libJaudio.so与Jaudio.class在同一目录下，可省略“-Djava.library.path=”选项
```text
java -Djava.library.path=.  Jaudio "a.mp3"
```

##结束语
* JNI是强大的给Java带来了活力与生命力。
* Android上很多app也使用类JNI技术，被称为[NDK][3],用于业务加密、高效处理等方面。
* Gstreamer 也提供多个平台的[SDK][5]，最感兴趣的是android平台的，可以试一试。
* [gstreamer-java][6]项目是一个成熟的利用JNI的Gst项目，可以使用、参考。


[1]:http://zh.wikipedia.org/wiki/Java%E6%9C%AC%E5%9C%B0%E6%8E%A5%E5%8F%A3
[2]:http://zh.wikipedia.org/wiki/GStreamer
[3]:https://developer.android.com/tools/sdk/ndk/index.html
[4]:http://docs.gstreamer.com/display/GstSDK/Installing+for+Android+development
[5]:http://docs.gstreamer.com/display/GstSDK/Android+tutorials
[6]:https://code.google.com/p/gstreamer-java/