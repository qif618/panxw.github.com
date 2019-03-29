---
layout: post
category: "cloud"
title:  "微服务链路追踪zipkin"
tags: [Cloud]
---


#启动zipkin服务 

> linux上下载zipkin服务  

	wget -O zipkin.jar 'https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec'  


> 后台运行  

	nohup java -jar zipkin.jar &



> 通过ip:9411访问zipkin UI界面  


![](https://i.imgur.com/x5ZpOWm.png)  

<!-- more -->

#  微服务整合zipkin



> 引入依赖  

	<dependency>
	  	<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-zipkin</artifactId>
	</dependency>

	或
	<dependency>
	    <groupId>org.springframework.cloud</groupId>
		    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
		</dependency>
		<dependency>
	   		<groupId>org.springframework.cloud</groupId>
	    <artifactId>spring-cloud-starter-sleuth</artifactId>
	</dependency>




> 加入配置  
	
	spring:
	  zipkin:
	    base-url: http://localhost:9411 #指定 Zipkin Server 地址
	  sleuth:
	    sampler:
	      # 采样率，默认0.1，也就是10%，开发环境1.0.即100%
	      probability: 1.0