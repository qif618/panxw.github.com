---
layout: post
category: "database"
title:  "mysql定时备份与恢复"
tags: [Database]
---



> 备份脚本

	cd /myData/mysqlbackup
	vim backup.sh
	
	内容如下：
	--------------------------
	#!/bin/bash
	#要备份的数据库名称
	db_name='db1'
	#备份文件存放目录
	backup_dir='/myData/mysqlbackup/'
	current_time=$(date +'%Y-%m-%d_%H%M%S')
	filepath=$backup_dir$current_time'.sql.gz'
	#gzip 压缩，压缩前800k，压缩后4k
	echo '开始导出数据库...'
	#-F刷新日志，-R备份存储过程
	mysqldump --defaults-extra-file=/myData/mysqlbackup/login_info.cnf -F -R  $db_name | gzip > $filepath
	echo '导出成功，文件名为: '$filepath
	--------------------------
	vim login_info.cnf
	内容如下：
	--------------------------
	[mysqldump]
	#此处指定max_allowed_packet,需注释掉/etc/my.cnf中的max_allowed_packet
	max_allowed_packet=200M 
	host=127.0.0.1
	user=root
	password='qif'
	--------------------------

> 手动执行备份

	cd /myData/mysqlbackup
	chmod +x backup.sh
	sh ./backup.sh
	ll查看是否生成备份文件，如2018-04-17_092801.sql.gz

> 加入定时任务

	查看定时cron是否启动	
	ps -ef|grep cron
	若未启动，先启动
	systemctl start crond
	
	crontab -e
	--------------------------
	每天凌晨1点备份一次数据
	0 1 * * * /myData/mysqlbackup/backup.sh
	测试可设置每分钟备份一次数据
	*/1 * * * * /myData/mysqlbackup/backup.sh
	--------------------------


> 定时清理备份文件

	cd /myData/mysqlbackup
	vi delete_backup.sh
	内容如下：
	--------------------------
	#/bin/bash
	# 删除15天前的备份， -mtime +15 表示15*24+24小时以外......即15天以外......
	 find /myData/mysqlbackup/ -type f -mtime +15 | xargs rm -f
	---------------------------
	chmod +x ./delete_backup.sh
	crontab -e
	# 每天凌晨2点，自动清理N天前备份文件
	0 2 * * * /myData/mysqlbackup/delete_backup.sh 
	



> 恢复数据

先从最近一次备份文件恢复，恢复数据期间，禁止写入新数据，如有主从，停掉

	解压sql文件
	gunzip db1.sql.gz

	mysql -uroot -p #登录mysql
	create database if not exists db1 default character set utf8 collate utf8_general_ci;
	use db1;
	source /myData/db1.sql;
	show tables; #查看是否导入成功

接着从binlog文件恢复数据  

	#将binlog文件转为sql文件
	#参数--no-defaults防止提示不识别utf8编码
	#mysql-bin.000005为与最新备份文件相同时间的mysql-bin文件，因为每次备份时，-F参数会生成新的mysql-bin
	mysqlbinlog --no-defaults --start-position="2559" --stop-position="2700" mysql-bin.000005 > remained.sql
	或者使用时间
	mysqlbinlog --no-defaults --start-datetime="2018-09-23 00:00:00" --stop-datetime="2018-09-27 00:00:00" mysql-bin.000005 > remained.sql
	
	如remained.sql有drop、delete等误操作方法，先过滤掉。可根据pos或time过滤掉
	插入数据库
	source remained.sql；