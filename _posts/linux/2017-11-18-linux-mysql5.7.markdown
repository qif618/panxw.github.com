---
layout: post
category: "database"
title:  "centos7下RPM安装mysql5.7"
tags: [Database]
---

查看是否有安装mysql  

	 rpm -qa|grep -i mysql

如有使用yum命令卸载  

 	yum -y remove mysql版本


<!-- more -->

开始安装mysql5.7  

	cd /usr/local/src/
	wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm 
	rpm -ivh mysql57-community-release-el7-8.noarch.rpm 
	yum -y install mysql-server 

启动mysql  

	systemctl restart mysqld

查看随即生成的密码  

	 grep "password" /var/log/mysqld.log    
	 结果：[Note] A temporary password is generated for root@localhost: #xVt4w;ISGt0

登陆mysql

	mysql -u root -p 
    接着输入随机产生的密码#xVt4w;ISGt0

进入mysql，必须先修改密码（5.7对密码的要求比较严，必须包含数字字母符号）  

	可先执行set global validate_password_policy=0;这个参数表示密码可以简单点，只要密码长度至少8位即可
	alter user 'root'@'localhost' identified by 'test2017!!';

可以添加host为%的用户，让所有IP访问  

	CREATE USER 'root'@'%' IDENTIFIED BY 'test2017!!';

给用户授权  

	grant all on *.* to 'root'@'%' identified by 'test2017!!' with grant option;



安装完成，默认配置  

	配置文件：/etc/my.cnf 
	日志文件：/var/log/var/log/mysqld.log 
	服务启动脚本：/usr/lib/systemd/system/mysqld.service 
	socket文件：/var/run/mysqld/mysqld.pid

在myData文件夹下面新建mysqllog文件夹，用于存放log日志  

	cd /myData
	mkdir mysqllog
	chown mysql:mysql mysqllog

修改mysql数据存到数据盘/myData,先停mysql,在/etc/my.cnf下修改  

	log_error = /myData/mysqllog/mysql-error.log
	datadir=/myData/mysql
	socket=/myData/mysql/mysql.sock

进入/myData下，拷贝mysql文件夹，最后启动mysql  

	cd /myData
	cp -a /var/lib/mysql ./

后续用户信息  

	mysql -u root -p 
	use mysql;
	修改密码：update user set authentication_string=password("新密码") where user='qif';
	修改host:update user set host = '%' where user = 'qif';
	flush privileges;

/etc/my.cnf配置  

	[client]
	port = 3306
	socket = /myData/mysql/mysql.sock
	default-character-set = utf8
	
	[mysqld]
	port = 3306
	socket = /myData/mysql/mysql.sock
	
	basedir = /usr/lib64/mysql
	datadir = /myData/mysql
	user = mysql
	bind-address = 0.0.0.0
	server-id = 1
	
	init-connect = 'SET NAMES utf8'
	character-set-server = utf8
	
	skip-name-resolve
	#skip-networking
	back_log = 300
	
	sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
	
	max_connections = 1000
	max_connect_errors = 6000
	open_files_limit = 65535
	table_open_cache = 256
	max_allowed_packet = 4M
	binlog_cache_size = 1M
	max_heap_table_size = 8M
	tmp_table_size = 32M
	
	read_buffer_size = 2M
	read_rnd_buffer_size = 8M
	sort_buffer_size = 8M
	join_buffer_size = 8M
	key_buffer_size = 16M
	
	thread_cache_size = 16
	
	query_cache_type = 1
	query_cache_size = 16M
	query_cache_limit = 2M
	
	ft_min_word_len = 4
	
	log_bin = /myData/mysqllog/mysql-bin
	binlog_format = mixed
	expire_logs_days = 30
	
	log_error = /myData/mysqllog/mysql-error.log
	slow_query_log = 1
	long_query_time = 1
	slow_query_log_file = /myData/mysqllog/mysql-slow.log
	
	performance_schema = 0
	explicit_defaults_for_timestamp
	
	#lower_case_table_names = 1
	
	skip-external-locking
	
	default_storage_engine = InnoDB
	
	innodb_file_per_table = 1
	innodb_open_files = 500
	innodb_buffer_pool_size = 4096M
	innodb_write_io_threads = 4
	innodb_read_io_threads = 4
	innodb_thread_concurrency = 0
	innodb_purge_threads = 1
	innodb_flush_log_at_trx_commit = 2
	innodb_log_buffer_size = 2M
	innodb_log_file_size = 32M
	innodb_log_files_in_group = 3
	innodb_max_dirty_pages_pct = 90
	innodb_lock_wait_timeout = 120
	
	event_scheduler=ON 
	
	bulk_insert_buffer_size = 8M
	myisam_sort_buffer_size = 16M
	myisam_max_sort_file_size = 10G
	myisam_repair_threads = 1
	
	interactive_timeout = 28800
	wait_timeout = 28800
	
	[mysqldump]
	quick
	max_allowed_packet = 16M
	
	[myisamchk]
	key_buffer_size = 16M
	sort_buffer_size = 8M
	read_buffer = 4M
	write_buffer = 4M


