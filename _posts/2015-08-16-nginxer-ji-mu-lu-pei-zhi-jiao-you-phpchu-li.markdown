---
layout: post
title: "nginx二级目录配置--交由php处理"
date: 2015-07-31 22:36
comments: true
categories: [nginx,php]
---

###nginx？
 * 10年前，一款由俄罗斯程序员开发轻量级的代理服务器出现。
 * 如今，[nginx][1]由于性能出色，应用广泛，受到了越来越多的关注。

### nginx二级目录配置
 * 场景
  * 无域名
  * 访问/,显示blog主页
  * 访问/bbs（二级目录），显示php写的论坛主页
 
 * ngnix配置文件及详解

<!-- more -->

```text
server {

	# 监听80端口
	listen 80;
	server_name localhost;

	# pass the PHP scripts to FastCGI server listening on the php-fpm socket
	# 将PHP脚本交由监听php-fpm套接字的FastCGI服务器处理

	# 将请求地址 是以/bbs开始的，其中带有php字样的用以下方式处理
	location ~* /bbs/.*\.php(.*)$ {
		include /etc/nginx/fastcgi_params;

		# 设置根目录地址，实际地址
		# 该目录下要有名为bbs的目录，将论坛系统放在目录下
		root /var/www;

		#交由php-fpm处理
		fastcgi_pass   unix:/var/run/php5-fpm.sock;

		fastcgi_index  index.php;

		#cgi程序的参数传递 真正执行php文件的地址
		fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
	}

	# /bbs 访问设置
	location /bbs {
		alias   /var/www/bbs;
		index   index.php index.html;
	}

	# 主页 反向代理设置
	location / {
		proxy_set_header   X-Real-IP $remote_addr;
		proxy_set_header   Host      $http_host;
		#反向代理了本地的ghost blog
		proxy_pass         http://localhost:2368;
	}

}


```

###相关参考
* [Basic nginx config (self-hosted with custom domain)][2]
* [Nginx开发从入门到精通][3]


[1]:https://zh.wikipedia.org/zh-cn/Nginx
[2]:http://support.ghost.org/basic-nginx-config/#configure-nginx
[3]:http://tengine.taobao.org/book/