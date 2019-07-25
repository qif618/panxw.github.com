---
layout: post
category: "database"
title:  "mysql主从复制"
tags: [Database]
---


> 介绍  
 
mysql服务器， 一般有两个线程来负责复制和被复制。
1. 作为主服务器Master,会把自己的每一次改动都记录到 二进制日志 Binarylog 中。 从服务器会负责来读取这个log， 然后在自己那里再执行一遍。  
2. 作为从服务器Slave， 会用master上的账号登陆到 master上， 读取master的Binarylog,  写入到自己的中继日志 Relaylog， 然后自己的sql线程会负责读取这个中继日志，并执行一遍。 

<!-- more -->
> 环境准备  
	
	主：192.168.1.110  从：192.168.1.111 

> 主机配置  
 
主机授权给从机  


	GRANT REPLICATION SLAVE ON *.* to 'qif'@'192.168.1.111 ' identified by 'qif';
	FLUSH PRIVILEGES; 

修改/etc/my.cnf  

	[mysqld]
	log-bin=/mysqldata/binlog
	server-id=1 //主从环境下的唯一标志符,不重复即可
	binlog-do-db = test //要同步的数据库名，当从机连上主机后，只有这里配置的数据库才会被同步，其他的不会被同步。  

重启mysql，并获取从机同步开始的位置  


	systemctl restart mysqld
	show master status; //表示当前的文件偏移量，如Position: 100。从服务器从哪读取主服务器的数据  


> 从机配置  

修改/etc/my.cnf  

	[mysqld]
	server-id=2 //从机只需配置server-id即可

执行命令  
	
	change master to master_host='192.168.1.110',master_port=3306,master_user='qif',master_password='qif',master_log_file='binlog.000001',master_log_pos=100;

启动并查看slave  

	start slave;
	show slave status\G;//Slave_IO_Running与Slave_SQL_Running都为YES表示配置成功

