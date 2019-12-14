---
layout: post
category: "cloud"
title:  "docker构建镜像"
tags: [Cloud]
---

> 容器化运行条件  
 
- 基础环境：JDK

- 应用：Spring Boot 应用 jar 包

- 外部配置：Spring Boot 配置参数

<!-- more -->

> 方式一：Dockerfile


新建springboot项目并打包
	
	mvn clean package

项目根目录下创建Dockerfile

	FROM openjdk:8-jre-alpine
	ENV TZ="Asia/Shanghai" HOME="/root" JVM_PARAMS=" " SPRING_PARAMS=" "
	WORKDIR ${HOME}
	ADD target/*.jar ${HOME}/myServer.jar
	EXPOSE 8080
	CMD java $JVM_PARAMS -Djava.security.egd=file:/dev/./urandom -jar ${HOME}/myServer.jar $SPRING_PARAMS
	
构建镜像(镜像名：myserver:last)  

	docker build -t myserver:last .

带jvm boot参数启动（非必须）

	docker run -d --name <容器名> -p 8080:8080 -e JVM_PARAMS="-Xmx1024M -Xms1024M" -e SPRING_PARAMS="--spring.profiles.active=prod" <镜像名>



> 方式二：

https://hub.docker.com/上注册账号，springboot中pom.xml文件添加插件  

	<plugin>
	    <groupId>com.google.cloud.tools</groupId>
	    <artifactId>jib-maven-plugin</artifactId>
	    <version>1.7.0</version>
	    <configuration>
	        <from>
	            <image>openjdk:alpine</image>
	        </from>
	        <to>
	            <image>docker.io/qif/qifJib</image>
	            <tags>
	                <tag>v1</tag>
	            </tags>
	            <auth>
	                <username>qif</username>
	                <password>qif</password>
	            </auth>
	        </to>
	    </configuration>
	    <executions>
	        <execution>
	            <phase>package</phase>
	            <goals>
	                <goal>build</goal>
	            </goals>
	        </execution>
	    </executions>
	</plugin>

构建镜像并推到docker hub
	
	mvn compile jib:build 

启动  

	docker run -d --name mydockerjib -p 8080:8080 docker.io/qif/qifJib:v1