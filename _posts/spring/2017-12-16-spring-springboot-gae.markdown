---
layout: post
category: "spring"
title:  "springBoot部署GAE"
tags: [springBoot]
---

前提  

- 电脑安装了GCloud SDK  
- 有[https://console.cloud.google.com](https://console.cloud.google.com "GCP")账号  
- 在https://console.cloud.google.com创建项目ID

在[https://start.spring.io/](https://start.spring.io/ "https://start.spring.io/")新建spring boot项目appenginTest  

- Java Version = 8  
-  Packaging = war  
- dependencies = Web  
<!-- more -->

将生产的zip项目解压，并在启动spring boot的类中加入方法  



	@GetMapping("/test")
        public String hello() {
                return "Hello Spring Boot!";
        }
	}


> 本地local运行  

cd进appenginTest项目，并执行  

	mvn spring-boot:run

通过localhost:8080/test则能访问显示：Hello Spring Boot!  



> 让项目兼容App Engine  

因为在GAE使用Jetty，故将依赖修改如下   
	
	<!-- 移除 spring-boot-starter-tomcat dependency 
	  <dependency>
	       <groupId>org.springframework.boot</groupId>
	       <artifactId>spring-boot-starter-tomcat</artifactId>
	       <scope>provided</scope>
	  </dependency>
	  -->
	   <!-- 添加 -->
	   <dependency>
	       <groupId>javax.servlet</groupId>
	       <artifactId>javax.servlet-api</artifactId>
	       <version>3.1.0</version>
	       <scope>provided</scope>
	   </dependency>
	   
添加Maven App Engine插件  

	<plugin>
        <groupId>com.google.cloud.tools</groupId>
        <artifactId>appengine-maven-plugin</artifactId>
        <version>1.3.1</version>
     </plugin>

创建WEB-INF，并在它下面加入appengine-web.xml  

	mkdir -p src/main/webapp/WEB-INF/
	touch src/main/webapp/WEB-INF/appengine-web.xml 

appengine-web.xml内容  

	<appengine-web-app xmlns="http://appengine.google.com/ns/1.0">
	  <version>1</version>
	  <threadsafe>true</threadsafe>
	  <runtime>java8</runtime>
	</appengine-web-app>  

执行命令，即可访问localhost:8080/test  

	mvn appengine:run

> 将项目部署到GCloud  

将项目添加压缩包，通过GCP上的google cloud shell窗口上传项目。  
解压项目，cd进入项目,执行项目绑定ID  

	$ gcloud config set project myID
	//执行成功显示Updated property [core/project].
	$ gcloud app create  

部署项目至GAE  

	$ mvn appengine:deploy

通过myID.appspot.com/test即可访问显示Hello Spring Boot!  

[参考文档](https://codelabs.developers.google.com/codelabs/cloud-app-engine-springboot/index.html?index=..%2F..%2Findex#0)