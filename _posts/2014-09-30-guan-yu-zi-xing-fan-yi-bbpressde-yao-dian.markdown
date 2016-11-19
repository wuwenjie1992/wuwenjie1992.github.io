---
layout: post
title: "关于自行翻译bbpress的要点"
date: 2014-09-30 18:59
comments: true
categories: [bbpress,shell]
---

##what is bbpress
* bbpress 是一款由[WordPress](https://cn.wordpress.org/)(zh_CN)的创作者编写的论坛软件
<!--more-->
* 它是作为一个WordPress的插件，先安装wp，然后你可以在[此下载](https://bbpress.org/download/)它
* 它的理念
 * 开源，直到永远！
 * 更少的代码
 * 代码是诗
 * 简约是一种功能
 * 造就优秀用户体验，最重要的是速度和安全性

##bbpress localization
* bbpress的默认语言是英语
* 本地化可以使用wp-content/plugins/bbpress/languages/bbpress.pot的文件进行翻译
* .pot文件是[xgettext](https://zh.wikipedia.org/wiki/Gettext#.E5.BC.80.E5.8F.91)程序从源代码生成的
* .po文件作为源代码中需翻译内容的模板

##how to translate
* 将.pot文件另存为.po文件
* 重命名，bbpress-zh_CN.po文件
* 在msgstr中填写翻译后的文字
* 型如
```text
    #. Author of the plugin/theme
    msgid "The bbPress Community"
    msgstr "bbPress的社区"
```
* 在翻译完毕之后，使用msgfmt命令
* msgfmt bbpress-zh_CN.po -o bbpress-zh_CN.mo
* 如果没有错误后，即可生成.mo文件
* 最后将bbpress-zh_CN.po bbpress-zh_CN.mo [上传](http://codex.bbpress.org/bbpress-in-your-language/)到wp-content/languages/plugins目录下

##The effect
* 你可以访问这个仅经过翻译的原生bbpress站点，[论坛](http://www.wuhuixin.tk/forums/)
* 此站点并未完全翻译完成，可能会中英同时出现