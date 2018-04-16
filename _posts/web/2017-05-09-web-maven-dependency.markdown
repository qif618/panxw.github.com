---
layout: post
category: "web"
title:  "IDEA中个别依赖下载失败"
tags: [maven]
---



> 项目导入时总有那么几个jar下载失败，但同样的maven，在另外一台电脑使用IDEA是能正常下载的。  

使用原始setting.xml下载失败，尝试了使用阿里云镜像，还是不可以 

	<mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>       
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror> 



> 最后在mvnrepository.com上下载jar，然后使用命令行maven命令安装。。

	
	mvn install:install-file -Dfile=AA -DgroupId=BB -DartifactId=CC -Dversion=DD -Dpackaging=jar  

如要安装  

	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context-support</artifactId>
	    <version>4.3.13.RELEASE</version>
	</dependency>


AA为下载jar所在路径，如  

	E:\myWork\resources\mavenjar\respo\org\springframework\spring-context-support-4.3.13.RELEASE.jar
BB为  

	org.springframework  
CC为  

	spring-context-support
DD为  

	4.3.13.RELEASE