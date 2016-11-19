---
layout: post
title: "[转]mysql cmd命令 MySQLCC++数据结构SQL"
author: "wuwenjie"
date: 2011-07-03 02:14
comments: true
categories: [转,mysql,cmd]
---
<a href="http://www.wuwenjie.tk/wp-content/uploads/2011/07/start_mysql.gif"><img class="size-full wp-image-72" title="start_mysql" src="http://www.wuwenjie.tk/wp-content/uploads/2011/07/start_mysql.gif" alt="start_mysql" width="498" height="234" /></a>

连接：mysql -h主机地址 -u用户名 －p用户密码 （注:u与root可以不用加空格，其它也一样）

断开：exit （回车）

创建授权：grant select on 数据库.* to 用户名@登录主机 identified by \"密码\"

修改密码：mysqladmin -u用户名 -p旧密码 password 新密码

删除授权: revoke select,insert,update,delete om *.* from test2@localhost;

显示数据库：show databases;

显示数据表：show tables;

显示表结构：describe 表名;

创建库：create database 库名;

删除库：drop database 库名;

使用库：use 库名;

创建表：create table 表名 (字段设定列表);

删除表：drop table 表名;

修改表：alter table t1 rename t2

查询表：select * from 表名;

清空表：delete from 表名;

备份表: mysqlbinmysqldump -h(ip) -uroot -p(password) databasename tablename &gt; tablename.sql

恢复表: mysqlbinmysql -h(ip) -uroot -p(password) databasename tablename &lt; tablename.sql（操作前先把原来表删除） 增加列：ALTER TABLE t2 ADD c INT UNSIGNED NOT NULL AUTO_INCREMENT,ADD INDEX (c); 修改列：ALTER TABLE t2 MODIFY a TINYINT NOT NULL, CHANGE b c CHAR(20); 删除列：ALTER TABLE t2 DROP COLUMN c; 备份数据库：mysql\bin\mysqldump -h(ip) -uroot -p(password) databasename &gt; database.sql

恢复数据库：mysql\bin\mysql -h(ip) -uroot -p(password) databasename &lt; database.sql 复制数据库：mysql\bin\mysqldump --all-databases &gt; all-databases.sql

修复数据库：mysqlcheck -A -o -uroot -p54safer

文本数据导入： load data local infile \"文件名\" into table 表名;

数据导入导出：mysql\bin\mysqlimport database tables.txt

执行sql文件：source 路径+文件（用“/”）2009-07-21

mysql cmd命令

MySQLCC++数据结构SQL

连接：mysql -h主机地址 -u用户名 －p用户密码 （注:u与root可以不用加空格，其它也一样）

断开：exit （回车）

创建授权：grant select on 数据库.* to 用户名@登录主机 identified by \"密码\"

修改密码：mysqladmin -u用户名 -p旧密码 password 新密码

删除授权: revoke select,insert,update,delete om *.* from test2@localhost;

显示数据库：show databases;

显示数据表：show tables;

显示表结构：describe 表名;

创建库：create database 库名;

删除库：drop database 库名;

使用库：use 库名;

创建表：create table 表名 (字段设定列表);

删除表：drop table 表名;

修改表：alter table t1 rename t2

查询表：select * from 表名;

清空表：delete from 表名;

备份表: mysqlbinmysqldump -h(ip) -uroot -p(password) databasename tablename &gt; tablename.sql

恢复表: mysqlbinmysql -h(ip) -uroot -p(password) databasename tablename &lt; tablename.sql（操作前先把原来表删除） 增加列：ALTER TABLE t2 ADD c INT UNSIGNED NOT NULL AUTO_INCREMENT,ADD INDEX (c); 修改列：ALTER TABLE t2 MODIFY a TINYINT NOT NULL, CHANGE b c CHAR(20); 删除列：ALTER TABLE t2 DROP COLUMN c; 备份数据库：mysql\bin\mysqldump -h(ip) -uroot -p(password) databasename &gt; database.sql

恢复数据库：mysql\bin\mysql -h(ip) -uroot -p(password) databasename &lt; database.sql 复制数据库：mysql\bin\mysqldump --all-databases &gt; all-databases.sql

修复数据库：mysqlcheck -A -o -uroot -p54safer

文本数据导入： load data local infile \"文件名\" into table 表名;

数据导入导出：mysql\bin\mysqlimport database tables.txt

执行sql文件：source 路径+文件（用“/”）
