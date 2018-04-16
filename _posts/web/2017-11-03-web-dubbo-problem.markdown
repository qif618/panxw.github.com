---
layout: post
category: "web"
title:  "dubbo注册zookeeper问题"
tags: [dubbo]
---

provider配置文件   

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
	
		<!-- 为当前服务提供者取个名字，并且提供给注册中心 -->  
		<dubbo:application name="imooc-dubbo-item-service"/>  
		<!-- 向zookeeper注册-->  
		<dubbo:registry protocol="zookeeper" address="192.168.1.52:2181"/>  
		<!-- 定义暴露服务的端口号 -->  
		<dubbo:protocol name="dubbo" host="192.168.1.16"  port="20992"/>  
		<!-- 暴露具体的服务接口，retries表示重连次数,ref表示实现类bean-->  
		<dubbo:service retries="3" interface="com.qif.service.orderService"
			ref="orderService"/>  
	</beans>  



> 提供方部署tomcat向zookeeper注册时，dubbo IP乱入  
  
解决办法，在配置中host指定ip    

	<dubbo:protocol name="dubbo" host="192.168.1.16"  port="20992"/>  



> 报连接zookeeper超时，可能导致的原因有：  

1. zookeeper服务端未启动  
1. 防火墙未开放client端口  
