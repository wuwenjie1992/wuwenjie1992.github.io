---
layout: post
title: "Xfce 外框没了--解决"
author: "wuwenjie"
date: 2012-07-13 15:04
comments: true
categories: [xfce，error]
---
不知道为何
登录了Xubuntu
后
发现为社么程序的外框都没有的

用得十分的不习惯

找谷神 一问

有了以下解决方法：

1）Alt + F2

2)输入  rm -rf ~/.cache/sessions

删除 缓存

3）运行

4）reboot 重启

5) 完成

---------
MORE：
<h1 id="Quick_Fix">Quick Fix</h1>
This requires you to log out and log in again.

&nbsp;
<pre>    Alt+F2                           (run window)
    rm -rf ~/.cache/sessions         (delete the Xfce4 saved sessions files for your user)
    Click on "Run"                   (or hit the enter key)</pre>
Now you will log out and log in again. Make sure "Save session for future logins" is not checked.

If the <strong>Quick Fix</strong> fails, please try the next method.

&nbsp;

<a title="XubuntuPanels" href="https://help.ubuntu.com/community/XubuntuPanels" target="_blank">XubuntuPanels</a>
<a title="xfce" href="https://threelegcat.wordpress.com/2012/05/28/%E8%A7%A3%E6%B1%BA-xubuntuxfceubuntu-studio-12-04-%E8%A6%96%E7%AA%97%E9%82%8A%E6%A1%86%E5%95%8F%E9%A1%8C/" target="_blank">解決 Xubuntu/Xfce/Ubuntu Studio 12.04 視窗邊框消失問題</a>
