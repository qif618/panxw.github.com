---
layout: post
category: "linux"
title:  "docker运行项目"
tags: [Docker]
---



> 安装Docker及配置镜像


安装docker  

	yum -y install docker
	docker --version
	systemctl start docker

登录阿里云获取镜像加速

	sudo mkdir -p /etc/docker
	sudo tee /etc/docker/daemon.json <<-'EOF'
	{
	  "registry-mirrors": ["https://qifjlwgz.mirror.aliyuncs.com"]
	}
	EOF
	sudo systemctl daemon-reload
	sudo systemctl restart docker


<!-- more -->



> 拉取tomcat  

	拉取tomcat:docker pull tomcat:8.5.40
	查看拉取的镜像：docker images
	启动tomcat容器：docker run --name tomcat8 -d -p 8080:8080 tomcat:8.5.40
	启动日志：docker logs containerID
	查看启动容器：docker ps
	查看停止容器列表：docker ps -a
	进入容器：docker exec -it containerID /bin/bash
	退出容器：exit
	关闭容器：docker stop tomcat8
	删除容器：docker rm tomcat8
	开启对应端口，重启防火墙后需重启docker，否则启动容器失败

> 启动项目

	docker cp test.war tomcat8:/usr/local/tomcat/webapps
	docker restart tomcat8
	访问项目：ip:8080/test
	


> 构建自己的镜像

	vim Dockerfile
	内容：
		from tomcat:8.5.40
		
		MAINTAINER qif qif@foxmail.com
		
		COPY qif.war /usr/local/tomcat/webapps
	构建：docker build -t qifProject:latest .
	运行：docker run --name qif -d -p 8080:8080 qifProject