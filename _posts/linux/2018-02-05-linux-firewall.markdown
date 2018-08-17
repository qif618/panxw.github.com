---
layout: post
category: "linux"
title:  "centos防火墙"
tags: [Linux]
---



> centos6防火墙


进入防火墙配置命令  

	vim /etc/sysconfig/iptables

光标放到一行上，按yy,再按P，复制一行，开放端口

<!-- more -->

防火墙打开、关闭、重启、查看状态  

	service iptables start
	service iptables stop
	service iptables restart
	service iptables status



> centos7防火墙

进入防火墙开放端口配置文件  

	vim /etc/firewalld/zones/public.xml

防火墙打开、关闭、重启、查看状态 

	systemetc start firewalld
	systemetc restart firewalld
	systemetc stop firewalld
	systemetc status firewalld