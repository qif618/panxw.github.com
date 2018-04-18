---
layout: post
category: "other"
title:  "IDEA热部署"
tags: [IDEA]
---



> 使用spring-boot-devtools实现热部署  




引入依赖

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>  

<!-- more -->

按CTRL+ALT+S打开settings，搜索compiler，选中compiler，勾选**build project automatically**  

按CTRL + SHIFT + ALT + /,选择Registry,勾上 **Compiler autoMake allow when app running**,然后close



> 可在启动springboot的main方法中加入代码禁用热部署  

	System.setProperty("spring.devtools.restart.enabled","false");
	SpringApplication.run(myApplication.class, args);



> 也可在application.yml中加入配置是否启用热部署  
	
	spring:
	  devtools:
	    restart:
	      enabled: false
	     