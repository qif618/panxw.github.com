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


> 初始化从机库数据

主机mysqldump出需要同步的sql文件，压缩并拷至从机

	mysqldump -u root -p --master-data=2 --skip-add-locks --skip-lock-tables --single-transaction -F  db1 > /myData/db1.sql

	此语句导出的sql文件不包含创建数据库语句
	1、--master-data=1会在mysqldump生成的sql文件里面加上CHANGE MASTES TO(master-data=2会注释掉)，默认开启锁表
	2、不锁表--skip-add-locks --skip-lock-tables --single-transaction
	3、--flush logs命令的作用就是关闭当前使用的binary log，然后打开一个新的binary log文件，文件的序号加1
	4、--hex-blob 即以16进制导出blob字段数据。 同版本下不加此参数一般不会有什么问题，但在不同MySQL版本间最好加此参数
	5、要想导出大表的话，应结合使用--quick 选项
	6、每次执行mysqldump都刷新binlog，生成一个新的：-F
	压缩并拷至从机
	gzip db1.sql
	scp /myData/db1.sql.gz qif@192.168.1.111:/myData/ #拷贝sql文件至从机服务器的myData文件夹下


从机导入db1.sql  

	解压sql文件
	gunzip db1.sql.gz

	mysql -uroot -p #登录mysql
	create database if not exists db1 default character set utf8 collate utf8_general_ci;
	use db1;
	source /myData/db1.sql;
	show tables; #查看是否导入成功
	

> 主机配置  
 
主机授权给从机  


	GRANT REPLICATION SLAVE ON *.* to 'qif'@'192.168.1.111 ' identified by 'qif';
	FLUSH PRIVILEGES; 

修改/etc/my.cnf  

	[mysqld]
	log-bin=/mysqldata/binlog 
	server-id=1 //主从环境下的唯一标志符,不重复即可

	#read-only = 1 设置从机为只读
	#binlog-do-db=xxxx   二进制日志记录的数据库,只有这里配置的数据库才会被同步
    #binlog-ignore-db=xxxx 二进制日志中忽略数据库
    以上两个任意指定其中一行参数就行，如果需要忽略多个库，则添加多行


重启mysql，如之前已设置好，不必重启

	systemctl restart mysqld


> 从机配置  

修改/etc/my.cnf  

	[mysqld]
	server-id=2 //从机配置server-id，需跟主机不一致
	replicate-do-db = db1 #指定同步的数据库,其他主机的库不被同步
	log-slave-updates = 1 #默认为0，即同步的sql语句不写入bin-log中
	relay-log = /myData/myqllog/mysql-relay-bin #指定relay-log位置

	#replicate-ignore-db 设定需要忽略的复制数据库
	#replicate-do-table  设定需要复制的表
	#replicate-ignore-table 设定需要忽略的复制表
	#replicate-wild-do-table 同replication-do-table功能一样，但是可以通配符
	#replicate-wild-ignore-table 同replication-ignore-table功能一样，但是可以加通配符

重启mysql  

	systemctl restart mysqld
	
从库设置主库信息  

	先获取同步开始文件（MASTER_LOG_FILE）、位置（MASTER_LOG_POS）
	head /myData/db1.sql -n80 | grep "MASTER_LOG_POS"
	
	设置连接主库信息 
	CHANGE MASTER TO MASTER_HOST='192.168.1.110',MASTER_PORT=3306,MASTER_USER='qif',MASTER_PASSWORD='qif', MASTER_LOG_FILE='mysql-bin.000014',MASTER_LOG_POS=469767035;

启动并查看slave  

	start slave;
	show slave status\G;     //Slave_IO_Running与Slave_SQL_Running都为YES表示配置成功



> 校验主从结果

主机中的db1插入一条数据，从机中查看是否存在