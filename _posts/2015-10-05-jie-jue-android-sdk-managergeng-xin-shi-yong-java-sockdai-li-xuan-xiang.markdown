---
layout: post
title: "解决android sdk manager更新--使用JAVA SOCK代理选项"
date: 2015-09-30 15:43
comments: true
categories: [java,android,shell]
---
###前言
* 我国一直努力地去做互联网的强国。[1][1]
* android sdk manager 无法正常更新，国情如此。
* android开发者急需新版的SDK，以跟上时代。

###普通方法
* 普遍的方法是改SDK manager的http proxy。
* 之前用过几个地址，效果不佳，大概是失效了。

###我的解决方法

* 以linux版为例
* 启动sdk manager 其实是执行**SDK目录下/tools/android**。
* 它其实是一个可执行的shell脚本。
<!-- more -->
* 最为重要的是脚本的最后一段，添加[java socket代理选项][2]即可。
* 启动执行之后即可进行使用了。
* 注：你必需有科学上网的工具，并能正确链接。

###shell修改如下(android 文件)

```bash
exec "$java_cmd" \
    -Xmx256M $os_opts $java_debug \
    -Dcom.android.sdkmanager.toolsdir="$progdir" \
    -DsocksProxyHost="127.0.0.1" \
    -DsocksProxyPort="7070" \
    -classpath "$jarpath:$swtpath/swt.jar" \
    com.android.sdkmanager.Main "$@"
```

* socksProxyHost socket代理服务器地址。
* socksProxyPort socket代理端口。

[1]:http://news.xinhuanet.com/politics/2014-02/27/c_119538788.htm
[2]:http://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html