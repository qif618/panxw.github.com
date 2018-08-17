---
layout: post
category: "other"
title:  "redis安装使用"
tags: [Other]
---


下载redis  

```
http://download.redis.io/releases/
```

1.安装gcc :   

	yum install gcc-c++

2.下载命令：  

	wget http://download.redis.io/releases/redis-4.0.0.tar.gz

3.解压redis安装包   

	tar -zxvf redis-4.0.0.tar.gz  

<!-- more -->

4.进入redis目录，编译，安装  

  	cd  redis-4.0.0
	make
	make PREFIX=/usr/local/redis install

5、进入redis解压路径，拷贝redis.conf到安装目录：  

	cp redis.conf /usr/local/redis

6.将daemonize on修改为  daemonize yes，开启守护线程，让redis可以在后台运行

7、redis默认只有本机能访问，配置**公网访问**需在redis.conf中设置密码如：requirepass 123，并注释bind 127.0.0.1


8.后端启动redis：  

	./src/redis-server ./redis.conf 
	./src/redis-server ./myredis.conf(使用自己的配置) 

9、查看是否启动成功：  

	ps -ef	 | grep -i redis

10、连接redis客户端  

	src/redis-cli（无密码连接）
	src/redis-cli -a "密码" -p "端口号"

11、关闭redis进程：  

	src/redis-cli shutdown 
	src/redis-cli -a "密码" -p "端口号" shutdown

