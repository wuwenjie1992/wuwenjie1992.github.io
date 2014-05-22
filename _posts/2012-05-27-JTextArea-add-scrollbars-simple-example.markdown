---
layout: post
title: "JTextArea添加滚动条的简单实例"
author: "wuwenjie"
date: 2012-05-27 12:01
comments: true
categories: [java，JTextArea，JScrollpane]
---

```java
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class gd2{

public static void main(String[] args){

JFrame f=new JFrame();
JPanel p1=new JPanel();
JTextArea t1=new JTextArea(35,25);

p1.setLayout(new FlowLayout());

t1.setLineWrap(true);

p1.add(new JScrollPane(t1));
//将JTextArea放入JScrollPane中，这样就能利用滚动的效果
f.add(p1);
f.pack();
f.setVisible(true);

f.addWindowListener(new WindowAdapter(){ public void windowClosing(WindowEvent e){ System.exit(0);} });
}
}
```
