---
layout: post
title: "近况+JNI Android NDK初识"
author: "wuwenjie"
date: 2012-09-01 19:11
comments: true
categories: [android,java,JNI,c]
---
最近（7月末-9月初），

边学习，证券方面的知识

（为了考一个证书，出来混饭吃（还是要干一行爱一行）），

边搞SHELL和android application。

也无聊看看多啦A梦，

中间也出去玩过，
<!-- more -->
宝山和大宁国际（大悦城，南京路）。

外公开刀也去了医院2。

&nbsp;

暑假过的一般，对了，

明天有人就要去学校了，

不知道还玩煮线吗，

还是要搞搞正业，

（没关系，嫁个好人家，万事不愁），

今天梦到去家，不在，人寐，恐，出。

&nbsp;

下半年的打算，考C，6；读证券，去考出来。

那人我stay here，见都难，其他都别说了，

快了，人妻的头衔在等着你（4-5年）

&nbsp;

还有构思new诗，素材还在收集，

还没抒发的适合情境。

&nbsp;

&nbsp;

--------    --------    --------    --------    --------    --------

----------------------- CUT HERE ------------------------

--------    --------    --------    --------    --------    --------

Java Native Interface
<a title="ndk" href="http://developer.android.com/tools/sdk/ndk/index.html" target="_blank">Android NDK</a>

我的理解：

为了使applicatin更为流畅，

效率更高，借助C/C++，

很多应用都有用，

未来的一大发展方向吧，

虚拟机存在，

使用java

对我这样的初学者友善，

也可以向更底层发展。

&nbsp;

-------------------------

A。下载NDK包，解压

<a href="http://dl.google.com/android/ndk/android-ndk-r8b-linux-x86.tar.bz2">android-ndk-r8b-linux-x86.tar.bz2</a>

---------------------------

B。PATH设置：

1)在/home/用户名 下修改.bashrc

<tt>#~/.bashrc</tt>文件决定了交互shell的行为

2)末尾添加

PATH=$PATH:/home/用户名/android-ndk-r8b
export PATH
#当然你要解压到 /home/用户名 下

3)到其他目录 测试

ndk-build 命令

出错：成功

Android NDK: Could not find application project directory !
Android NDK: Please define the NDK_PROJECT_PATH variable to point to it.
/home/用户名/android-ndk-r8b/build/core/build-local.mk:130: *** Android NDK: Aborting    .  Stop.
------------------

C.Eclipse的设置

project--&gt;properties--&gt;Builders---&gt;new---&gt;program
---&gt;location (*/android-ndk-r8b/ndk-build)
---&gt;working directory(${workspace_loc:/yorproject/jni})
----&gt;refresh  --specify resources

参考：<a title="设置" href="http://www.360doc.com/content/11/0223/17/2734308_95473676.shtml" target="_blank">http://www.360doc.com/content/11/0223/17/2734308_95473676.shtml</a>

<a title="os-androidndk/" href="http://www.ibm.com/developerworks/cn/opensource/tutorials/os-androidndk/section6.html" target="_blank">http://www.ibm.com/developerworks/cn/opensource/tutorials/os-androidndk/section6.html</a>

&nbsp;

D。实践 New Project 主要（Android.mk&amp;&amp;ok.c）

1）准备

--------MainActivity.java--------------
```java
package tk.wuwenjie.jnidemo;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;
import android.app.Activity;

public class MainActivity extends Activity {
static String TAG="MainActivity";

public void onCreate(Bundle savedInstanceState) {
Log.i(TAG, "MA_oncreate!!");
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
TextView textView = (TextView)findViewById(R.id.MA_tv);

String myString =stringFromSo();//stringFromNDKJNI

textView.setText(myString);

printLOGI();
}

static{
try{System.loadLibrary("ok"); }
catch(UnsatisfiedLinkError ule){
Log.i(TAG,"WARNING: Could not load lib!"+ule.toString());
}
}

public native String stringFromSo();

public native void printLOGI();

}
```

-----------------------activity_main.xml----------------
```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true"
        android:text="@string/hello_world"
        android:id="@+id/MA_tv" />

</RelativeLayout>
```

&nbsp;

&nbsp;

&nbsp;

2）在src的同级目录，创建目录（mkdir jni）

进入 jni 创建(touch)

Android.mk

ok.c

-----Android.mk-------
```bash
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := ok     #编译的目标对象
LOCAL_LDLIBS    :=-L$(SYSROOT)/system/lib -llog        #编译指定额外库，"-lxxx"格式
LOCAL_SRC_FILES := ok.c    #编译的源文件

include $(BUILD_SHARED_LIBRARY)
```

--------ok.c---------------------
```c
#include <stdio.h>
#include <string.h>
#include <jni.h>

#define LOG_TAG "TAG" //自定义的变量，相当于logcat函数中的tag
#undef LOG
#include <android/log.h>
#define LOGI(...) ((void)__android_log_print(ANDROID_LOG_INFO,LOG_TAG,__VA_ARGS__))

jstring Java_tk_wuwenjie_jnidemo_MainActivity_stringFromSo( JNIEnv* env,jobject thiz ){

   return (*env)->NewStringUTF(env, "well,i from jni!!!OKOKOKOKOKOKOKO");

}//tk.wuwenjie.jnidemo包名  MainActivity类名 stringFromSo函数名

void  Java_tk_wuwenjie_jnidemo_MainActivity_printLOGI(JNIEnv * env, jobject jobj){

 LOGI("LOGI\n");
}
```

-------ndk-build 编译-----------

:~/workspace/Sayok/jni$ ndk-build
Compile thumb  : ok &lt;= ok.c
SharedLibrary  : libok.so
Install        : libok.so =&gt; libs/armeabi/libok.so

出现了 libs/armeabi/libok.so

<img class="alignnone" src="http://pic.qndown.com/img/common/8538924c/" alt="" width="142" height="137" />

&nbsp;

3）run 就可以了

<img title="logcat" src="http://pic.qndown.com/img/common/217be66a/" alt="" width="491" height="47" />

&nbsp;

<img title="效果" src="http://pic.qndown.com/img/common/116cfe3f/" alt="" width="244" height="419" /> 效果

&nbsp;

-----------------------------------------

MORE:

1)project file<a title="Sayok" href=" http://filemarkets.com/file/common/81252e98/" target="_blank">【Sayok20120902.tar.gz】</a>
http://filemarkets.com/file/common/81252e98/

MD5：efa15676d843b4d21edac12b5c035c02

2)

/home/用户名/android-ndk-r8b/docs

/home/用户名/android-ndk-r8b/samples

http://www.ibm.com/developerworks/cn/opensource/tutorials/os-androidndk/index.html

http://blog.devdiv.com/android-jni%E5%85%A5%E9%97%A8%E7%B3%BB%E5%88%97%E6%95%99%E7%A8%8B.html

http://www.cnblogs.com/shadox/archive/2011/12/02/2272564.html

&nbsp;

&nbsp;
