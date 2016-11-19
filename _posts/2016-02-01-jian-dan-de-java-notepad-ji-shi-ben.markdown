---
layout: post
title: "简单的JAVA NOTEPAD 记事本"
date: 2016-01-01 20:52
comments: true
categories: [java]
---

##前言
* 人们一直会对JAVA有一种“丑陋”的错觉，特别是UI。
* 但JAVA其实是强大的、多功能的、适合网络的语言。
* 我们一简单的文本编辑器，管中窥豹，一探JAVA的UI设计。

<!-- more -->

## CODE
* 取名为tiff,它的样子
* ![java notepad](/images/JAVA_NOTEPAD.png)
* 

{% codeblock lang:java tiff.java %}

import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JMenuBar;
import javax.swing.JMenu;
import javax.swing.JMenuItem;
import javax.swing.JTextArea;
import javax.swing.JScrollPane;
import javax.swing.JCheckBox;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JFileChooser;
import javax.swing.JPopupMenu;
import javax.swing.UIManager;
import javax.swing.UnsupportedLookAndFeelException;

import javax.imageio.ImageIO;

import java.awt.event.WindowEvent;
import java.awt.event.WindowAdapter;
import java.awt.BorderLayout;
import java.awt.Dimension;
import java.awt.Toolkit;
import java.awt.Image;
import java.awt.Font;
import java.awt.datatransfer.Clipboard;
import java.awt.datatransfer.Transferable;
import java.awt.datatransfer.StringSelection;
import java.awt.datatransfer.DataFlavor;
import java.awt.datatransfer.UnsupportedFlavorException;

import java.io.File;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.io.BufferedWriter;

import java.net.URL;

public class tiff{

    JFrame f;	// 框架
	JPanel p;	//面板对象
	JMenuBar mb; // 菜单栏
	
	JMenu fileM,helpM,ViewM,CharM,FontM,SetM; // 菜单
	
	JMenuItem fileMI,aboutMI,SaveMI,ExitMI,TabMI; // 菜单项
	JMenuItem firstopenMI,uft8MI,gb1MI,bigMI,PCutMI,PCopyMI,PpasteMI;
	JMenuItem FontNMI,FontSMI,FontZMI,MasMI,LineWrapMI;
	
	JPopupMenu PM; // 弹出式菜单
	JTextArea ta; // 多行文本框
	String FontName_ta = "";	// 字体名称
	int FontStyle_ta = 0 ,FontSize_ta = 12; // 字体风格、字体大小
	
	JScrollPane sp;	//滚动条对象
	
	File fi; // file to load
	String FirstOpenwith = "GB2312"; // 首选文件打开编码
	int maxsize = 30; // 
	
	StringBuffer content = new StringBuffer();
	
	public tiff(){
	
		f = new JFrame("tiff");
		
		try{
			// 使用相对路径，访问图片资源
			URL imURL = getClass().getResource("res/icon.gif");
			Image image = ImageIO.read(imURL);	// 	读取图形
			f.setIconImage(image);		//  设置图片
		}catch(IOException e){
			System.out.println("setIconImage error："+e.toString());
		}
		
		f.addWindowListener(new WindowAdapter(){
			public void windowClosing(WindowEvent e){
				System.out.println("windowClosing:" + e);
				System.exit(0);
			}
		});//注册窗口监听器
		
		MenuListener myListener=new MenuListener(); //创建监听对象

		p = new JPanel();
		f.setContentPane(p);	//设置容器
		p.setLayout(new BorderLayout()); //设置布局管理器

		p.setPreferredSize(new Dimension(300,150));	//设置面板区域大小
		//p.add(t,BorderLayout.NORTH);	//放置组件
		//p.add(sp,BorderLayout.CENTER);

		mb = new JMenuBar();
		f.setJMenuBar(mb);
		
		PM = new JPopupMenu(); // 弹出式菜单
		PCutMI  = new JMenuItem("Cut");  //创建弹出菜单项
		PCopyMI = new JMenuItem("Copy");
		PpasteMI = new JMenuItem("Paste");
		
		PM.add(PCutMI);PM.add(PCopyMI);PM.add(PpasteMI);
		
		PCutMI.addActionListener(myListener);
		PCutMI.setActionCommand("pop_cut");
		PCopyMI.addActionListener(myListener);
		PCopyMI.setActionCommand("pop_copy");
		PpasteMI.addActionListener(myListener);
		PpasteMI.setActionCommand("pop_paste");
		
		ta=new JTextArea(60,300);	//文本区域实例化
		ta.setSize(300, 600);
		ta.setFont(new Font(FontName_ta,FontStyle_ta,FontSize_ta));
		sp=new JScrollPane(ta);	//滚动条实例化
		p.add(sp);

		f.setBounds(250, 300, 600, 500);
		p.setVisible(true);
		f.setVisible(true);	//容器可视化

		fileM=new JMenu("File");	// 创建各个菜单
		mb.add(fileM);	//在menubar中增加fileM,将菜单加入菜单条
		fileM.setMnemonic('F');		// 定义访问键
		ViewM = new JMenu("View");
		mb.add(ViewM);
		ViewM.setMnemonic('V');
		SetM = new JMenu("Settings");
		mb.add(SetM);
		SetM.setMnemonic('S');
		helpM = new JMenu("Help");
		mb.add(helpM);
		helpM.setMnemonic('H');
		
		fileMI=new JMenuItem("Open");	// 创建个菜单项
		fileM.add(fileMI);
		fileMI.addActionListener(new ActionListener(){
			public void actionPerformed(ActionEvent e){
				
				// 文件选择器
				JFileChooser chooser = new JFileChooser();
				chooser.setDialogTitle("Choose File To Open");
				chooser.setApproveButtonText("Load");
				chooser.setApproveButtonToolTipText(
						"Load this file as " + FirstOpenwith);
				int ret = chooser.showOpenDialog(f); // 显示

				// 如果文件选择失败
				if (ret != JFileChooser.APPROVE_OPTION) {
					System.out.println("OPEN file not choosed");
					JOptionPane.showMessageDialog(f,
						"OPEN file no choosed","Warning",
                        JOptionPane.ERROR_MESSAGE);
					return;
				}

				fi = chooser.getSelectedFile(); // 选择的文件路径
				ta.setText(readfile(fi,maxsize,FirstOpenwith,f).toString());	
				// 读取文件内容,设置多行文本框内容
				
				content = new StringBuffer("");
			}
		});
		
		SaveMI = new JMenuItem("Save");	// 保存
		fileM.add(SaveMI);
		SaveMI.addActionListener(new ActionListener(){
			public void actionPerformed(ActionEvent e){
			
				// 文件选择器
				JFileChooser chooser = new JFileChooser();
				chooser.setDialogTitle("Choose File To Save");
				chooser.setApproveButtonText("Load");
				chooser.setApproveButtonToolTipText(
						"Save this file as " + FirstOpenwith);
				int ret = chooser.showSaveDialog(f); // 显示

				// 如果文件选择失败
				if (ret != JFileChooser.APPROVE_OPTION) {
					System.out.println("SVAE file not choosed");
					JOptionPane.showMessageDialog(f,
						"SAVE file no choosed","Warning",
                        JOptionPane.ERROR_MESSAGE);
					return;
				}

				fi = chooser.getSelectedFile(); // 选择的文件路径
				System.out.println("file to save: " + fi);
				
				savefile(fi,ta.getText(),FirstOpenwith,f);
			}
		});
		
		fileM.addSeparator(); // 添加一条横向分隔线
		
		ExitMI = new JMenuItem("Exit");	// 退出
		fileM.add(ExitMI);
		ExitMI.addActionListener(myListener);
		ExitMI.setActionCommand("Exit");
		
		CharM = new JMenu("Charset"); // 字体
		ViewM.add(CharM);
		
		firstopenMI = new JMenuItem("OPEN AS");
		uft8MI = new JMenuItem("REOPEN AS UFT-8");
		gb1MI  = new JMenuItem("REOPEN AS GB2312");
		bigMI  = new JMenuItem("REOPEN AS Big5");;
		CharM.add(firstopenMI);CharM.add(uft8MI);
		CharM.add(gb1MI);CharM.add(bigMI);
		
		firstopenMI.addActionListener(myListener);
		firstopenMI.setActionCommand("charset_firstopen");
		uft8MI.addActionListener(myListener);
		uft8MI.setActionCommand("charset_UTF-8");
		gb1MI.addActionListener(myListener);
		gb1MI.setActionCommand("charset_GB2312");
		bigMI.addActionListener(myListener);
		bigMI.setActionCommand("charset_Big5");
		
		// 换行
		LineWrapMI = new JMenuItem("LineWrap");
		ViewM.add(LineWrapMI);
		LineWrapMI.addActionListener(myListener);
		LineWrapMI.setActionCommand("linewarp");
		
		// TAB 大小
		TabMI = new JMenuItem("Tab");
		ViewM.add(TabMI);
		TabMI.addActionListener(myListener);
		TabMI.setActionCommand("tab");	
		
		// 字体菜单
		FontM = new JMenu("Font");
		ViewM.add(FontM);
		
		FontNMI = new JMenuItem("Name");
		FontSMI = new JMenuItem("Style");
		FontZMI = new JMenuItem("Size");;
		FontM.add(FontNMI);FontM.add(FontSMI);FontM.add(FontZMI);
		
		FontNMI.addActionListener(myListener);
		FontNMI.setActionCommand("Font_Name");
		FontSMI.addActionListener(myListener);
		FontSMI.setActionCommand("Font_Style");
		FontZMI.addActionListener(myListener);
		FontZMI.setActionCommand("Font_Size");
		
		MasMI = new JMenuItem("File MaxSize");
		SetM.add(MasMI);
		MasMI.addActionListener(myListener);
		MasMI.setActionCommand("Set_MaxSize");
		
		// 关于
		aboutMI = new JMenuItem("About");
		helpM.add(aboutMI);
		aboutMI.addActionListener(myListener);
		aboutMI.setActionCommand("about");
		
		//设置窗体Frame的Mouse监听对象
		ta.addMouseListener(new MouseAdapter(){
			public void mousePressed(MouseEvent e){ 
				if(e.isPopupTrigger()) //弹出菜单
				PM.show(e.getComponent(),e.getX(),e.getY());
			}
			public void mouseReleased(MouseEvent e){
				mousePressed(e);
			}
		});
	}
	
	// 读取文件内容
	public StringBuffer readfile(
				File fi,int maxsize,String charset,JFrame f){
	
		StringBuffer content = new StringBuffer();
		System.gc();
		
		if (fi.isFile() && fi.canRead()) {
			System.out.println("file path:" + fi);
			
			try{
				FileInputStream fis=new FileInputStream(fi); 
				//构造文件字节输入流
				
				Long size = fi.length() >> 10 ;
				System.out.println(
					"file size:" +size +"K MaxSize:" + maxsize);
				
				if(size >> 10 <= maxsize ){
				
					InputStreamReader isr = 
								new InputStreamReader(fis,charset);
					BufferedReader br = new BufferedReader(isr);
					
					String str;
					while((str=br.readLine())!=null)
						content.append(str+"\n");			
					
					br.close(); // 关闭流，释放资源
					isr.close();fis.close();
					
					f.setTitle("tiff-" + fi);
				
				}else{
					System.out.println("large file !");
					JOptionPane.showMessageDialog(f,
						"large file may cause error!\n" + 
						"Please change Java Heap Memory!\n" +
						"then use Seetings -> File MaxSize",
						"large file",JOptionPane.ERROR_MESSAGE);
				}
			
			}catch(Exception ex){
				System.out.println("read file error!"+ex.toString());
				JOptionPane.showMessageDialog(f,"Could not read file: ",
				"Error reading file",JOptionPane.ERROR_MESSAGE);
			}
		} else {
			System.out.println("open file error!");
			JOptionPane.showMessageDialog(f,"Could not open file: " + fi,
				"Error opening file",JOptionPane.ERROR_MESSAGE);
		}
		return content;
	}
	
	// 保存文件
	public void savefile(File fi,String save,
								String charset,JFrame f){
		
		if (!fi.exists()) {
			System.out.println("file save path:" + fi);
			
			try{
				//构造文件字节输出流
				FileOutputStream fos=new FileOutputStream(fi);
				
				OutputStreamWriter osw = 
								new OutputStreamWriter(fos,charset);
				BufferedWriter bw = new BufferedWriter(osw);
				
				bw.write(save,0,save.length());
				bw.flush();
				
				fos.close();
				osw.close();
				bw.close();
				
				f.setTitle("tiff-" + fi);
			
			}catch(Exception ex){
				System.out.println("save file error !"+ex.toString());
				JOptionPane.showMessageDialog(f,"Could not save file: ",
				"Error saveing file",JOptionPane.ERROR_MESSAGE);
			}
		} else {
			System.out.println("save file exists !");
			JOptionPane.showMessageDialog(f,"save file exists: " + fi ,
				"Saveing file",JOptionPane.WARNING_MESSAGE);
		}
	
	}
	
	// 普通菜单项监听类，实现ActionListener接口,与界面解耦
	public class MenuListener implements ActionListener{
	
		public void actionPerformed(ActionEvent e){
		
			// 获得系统剪贴板
			Clipboard sysClip = 
				Toolkit.getDefaultToolkit().getSystemClipboard();
			Transferable cutText;	// 获取剪切文字
		
			switch(e.getActionCommand()){
			
				case "Exit": //如果选Exit菜单项，结束
					System.out.println("MenuLis:Exit!");
					System.exit(0);
				break;
				case "charset_firstopen":
					FirstOpenwith = JOptionPane.showInputDialog(f,
						"First Open Charset Name Like UTF-8",
						"Change Charset Name",
						JOptionPane.INFORMATION_MESSAGE);
					System.out.println("FirstOpenwith:" + FirstOpenwith);
				break;
				case "charset_UTF-8": // 以UTF-8编码打开
					System.out.println("MenuLis:charset_UTF-8");
					if(fi!=null){
						// 读取文件函数,设置文字
						ta.setText(readfile(fi,maxsize,"UTF-8",f).toString());
						FirstOpenwith = "UTF-8";
					}
				break;
				case "charset_GB2312":
					System.out.println("MenuLis:charset_GB2312");
					if(fi!=null){
						ta.setText(readfile(fi,maxsize,"GB2312",f).toString());
						FirstOpenwith = "GB2312";
					}
				break;				
				case "charset_Big5":
					System.out.println("MenuLis:charset_Big5");
					if(fi!=null){
						ta.setText(readfile(fi,maxsize,"Big5",f).toString());
						FirstOpenwith = "Big5";
					}
				break;
				case "pop_cut": // 弹出式菜单 剪切
					System.out.print("MenuLis:pop_cut");
					
					System.out.println(" " + ta.getSelectionStart()
								+ ":" + ta.getSelectionEnd());
					
					// 获取剪切文字
					cutText = new StringSelection(ta.getSelectedText());
					// 剪切文字赋到系统剪贴板
					sysClip.setContents(cutText,null);
					// 替换
					ta.replaceRange("",ta.getSelectionStart(),ta.getSelectionEnd());
					
				break;				
				case "pop_copy": // 弹出式菜单 复制
					System.out.print("MenuLis:pop_copy");
					System.out.println(" " + ta.getSelectionStart()
								+ ":" + ta.getSelectionEnd());
					// 获取剪切文字
					cutText = new StringSelection(ta.getSelectedText());
					// 剪切文字赋到系统剪贴板
					sysClip.setContents(cutText,null);
					
				break;					
				case "pop_paste": // 弹出式菜单 黏贴
					System.out.print("MenuLis:pop_paste");
					System.out.println(" " + ta.getSelectionStart()
								+ ":" + ta.getSelectionEnd());
					
					// 替换已选取的文本为空
					ta.replaceRange("",ta.getSelectionStart(),ta.getSelectionEnd());
					
					Transferable clip = sysClip.getContents(null); // 获取系统剪贴板
					if(clip != null 	// 是否为空 是否支持字符型
						& clip.isDataFlavorSupported(DataFlavor.stringFlavor)){
						
						try{
							ta.insert((String)clip.getTransferData(DataFlavor.stringFlavor),
									ta.getSelectionStart());// 插入剪贴板字符文字
						}catch(UnsupportedFlavorException ue){
							System.out.println("Unsupport Flavor error:" + ue.toString());
						}catch(IOException ie){
							System.out.println("IO error:" + ie.toString());
						}
					}
				break;
				case "tab": // 设置tab大小
					System.out.println("MenuLis:tab");
					ta.setTabSize(2);
				break;
				
				case "linewarp": // 换行
					System.out.println("MenuLis:linewarp");
					if(!ta.getLineWrap())
						ta.setLineWrap(true);
					else
						ta.setLineWrap(false);
				break;
				case "Font_Name": // 字体名称
					System.out.print("MenuLis:Font_Name");
					
					FontName_ta = JOptionPane.showInputDialog(f,
						"Font Name","Change Font Name",
						JOptionPane.INFORMATION_MESSAGE);
					System.out.println(":" + FontName_ta);
					
					ta.setFont(new Font(FontName_ta,FontStyle_ta,FontSize_ta));
				break;
				case "Font_Style": // 字体风格
					System.out.print("MenuLis:Font_Style");
				
					String FontStyle = JOptionPane.showInputDialog(f,
						"Font Style 0-3","Change Font Style",
						JOptionPane.INFORMATION_MESSAGE);
					System.out.println(":" + FontStyle_ta);
					
					try{
						FontStyle_ta = Integer.parseInt(FontStyle);
					}catch(NumberFormatException nfe){
					
						System.out.println("Number Formaterror:" + nfe.toString());
						JOptionPane.showMessageDialog(f,"Please input Integer!",
								"Error Change Font Style",JOptionPane.ERROR_MESSAGE);
					}
					ta.setFont(new Font(FontName_ta,FontStyle_ta,FontSize_ta));
				
				break;
				case "Font_Size": // 字体大小
					System.out.print("MenuLis:Font_Size");
				
					String FontSize = JOptionPane.showInputDialog(f,
						"Font Size","Change Font Size",
						JOptionPane.INFORMATION_MESSAGE);
					System.out.println(":" + FontSize);
					
					try{
						FontSize_ta = Integer.parseInt(FontSize);
					}catch(NumberFormatException nfe){
					
						System.out.println("Number Formaterror:" + nfe.toString());
						JOptionPane.showMessageDialog(f,"Please input Integer!",
								"Error Change Font Size",JOptionPane.ERROR_MESSAGE);
					}
					ta.setFont(new Font(FontName_ta,FontStyle_ta,FontSize_ta));
				
				break;
				case "Set_MaxSize": // 设置文件打开大小
					System.out.print("MenuLis:Set_MaxSize");
				
					String maxSize = "";
					maxSize = JOptionPane.showInputDialog(f,
						"Open File MAX Size IN (MB)","Change File MAX Size",
						JOptionPane.INFORMATION_MESSAGE);
					System.out.println(": " + maxSize + " MB.");
				
					if(!(maxSize == "" | maxSize == null)){
						try{
							maxsize = Integer.parseInt(maxSize);
						}catch(NumberFormatException nfe){
						
							System.out.println("Number Formaterror:" + nfe.toString());
							JOptionPane.showMessageDialog(f,"Please input Integer!",
									"Error Change Font Size",JOptionPane.ERROR_MESSAGE);
						}
					}
				break;
				case "about": // 关于
					System.out.println("MenuLis:about");
					JOptionPane.showMessageDialog(f,
						"tiff is a free software like a simple notepad.\n" +
						"Use JAVA Swing.\n" +
						"AUTHOR:WWJ\nVERSION:0.0.1 20150518",
						"FREE SOFTWARE [tiff]",
						JOptionPane.INFORMATION_MESSAGE);
				break;
			} // switch
			
		}
	
	} // MenuListener
	
	public static void main (String[]args){
	
		// 设置UI界面风格
		try{
			if(Math.random() > 0.4)
				UIManager.setLookAndFeel(
					// "com.sun.java.swing.plaf.nimbus.NimbusLookAndFeel");
					// "com.sun.java.swing.plaf.windows.WindowsLookAndFeel");
					"javax.swing.plaf.nimbus.NimbusLookAndFeel");
					// "javax.swing.plaf.synth.SynthLookAndFeel");
			else
				UIManager.setLookAndFeel(
					"com.sun.java.swing.plaf.windows.WindowsLookAndFeel");
		}catch(ClassNotFoundException e){
			System.out.println("setLookFeel no found error:" + e.toString());
		}catch(InstantiationException e){
			System.out.println("setLookFeel instant error:"+e.toString());
		}catch(IllegalAccessException e){
			System.out.println("setLookFeel Access error:"+e.toString());
		}catch(UnsupportedLookAndFeelException e){
			System.out.println("setLookFeel support error:"+e.toString());
		}
		
		new tiff();
	}
	
}

{% endcodeblock %}


