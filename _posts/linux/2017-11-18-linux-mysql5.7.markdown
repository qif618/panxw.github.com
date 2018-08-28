---
layout: post
category: "linux"
title:  "centos7下RPM安装mysql5.7"
tags: [Linux]
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

安装完成，默认配置  

	配置文件：/etc/my.cnf 
	日志文件：/var/log/var/log/mysqld.log 
	服务启动脚本：/usr/lib/systemd/system/mysqld.service 
	socket文件：/var/run/mysqld/mysqld.pid

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

修改mysql数据存到数据盘/myData,先停mysql,在/etc/my.cnf下修改

	datadir=/myData/mysql
	socket=/myData/mysql/mysql.sock

进入/myData下，移动mysql文件夹，最后启动mysql  

	mv -a /var/lib/mysql ./