---
layout: post
category: "linux"
title:  "tar命令解压JDK1.8"
tags: [Linux]
---

查看是否有安装mysql  

	 rpm -qa|grep jdk

如有使用yum命令卸载  

 	yum -y remove jdk版本

<!-- more -->

进入/usr/java下载JDK

	wget http://download.oracle.com/otn-pub/java/jdk/8u181-b13/96a7b8442fe848ef90c96a2fad6ed6d1/jdk-8u181-linux-x64.tar.gz

解压

	tar -zvxf jdk-8u181-linux-x64.tar.gz




配置环境变量，在/etc/profile下添加  

	export JAVA_HOME=/usr/java/jdk1.8.0_181
	export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	export PATH=$PATH:$JAVA_HOME/bin

使配置生效  

	source /etc/profile