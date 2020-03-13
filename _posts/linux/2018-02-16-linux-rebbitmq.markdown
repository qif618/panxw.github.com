---
layout: post
category: "linux"
title:  "centos7下安装rabbitMQ"
tags: [Linux]
---



> yum命令安装ocat

	yum -y install socat



> 下载安装erlang

	wget http://www.rabbitmq.com/releases/erlang/erlang-19.0.4-1.el7.centos.x86_64.rpm
	rpm -ivh erlang-19.0.4-1.el7.centos.x86_64.rpm





- 执行erl命令么,出现erlang相关信息则表示安装成功




> 下载安装rebbitMQ

	wget  http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.10/rabbitmq-server-3.6.10-1.el7.noarch.rpm
	rpm -ivh rabbitmq-server-3.6.10-1.el7.noarch.rpm


- PS:重新安装，先/var/lib/rabbitmq目录和/etc/rabbitmq目录



- 防火墙需开放5672（AMQP）  15672（WEB）端口

> 相关命令 


	systemctl start rabbitmq-server
	rabbitmqctl status //查看启动状态
	rabbitmqctl stop  //关闭服务
	rabbitmq-server -detached           //进入mq目录，启动rabbitmq，-detached代表后台守护进程方式启动
	rabbitmqctllist_users //查看角色



> 启动web访问rabbitmq插件

	rabbitmq-plugins enable rabbitmq_management



> 默认guest用户不能通过web端访问，需新建用户  

	rabbitmqctl add_user admin 123 //admin为用户名  123为密码
	rabbitmqctl set_user_tags admin administrator  //给用户赋角色



> 修改rabbitmq配置

	cp /usr/share/doc/rabbitmq-server-3.6.10/rabbitmq.config.example /etc/rabbtimq/rabbitmq.config


> 安装消息延迟插件

	wget https://dl.bintray.com/rabbitmq/community-plugins/3.6.x/rabbitmq_delayed_message_exchange/rabbitmq_delayed_message_exchange-20171215-3.6.x.zip

	unzip rabbitmq_delayed_message_exchange-20171215-3.6.x.zip -d /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.10/plugins

	rabbitmq-plugins enable rabbitmq_delayed_message_exchange