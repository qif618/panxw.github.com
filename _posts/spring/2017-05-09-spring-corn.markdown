---
layout: post
category: "spring"
title:  "spring定时器"
tags: [Spring]
---


> 后台代码  

	@Component
	public class DemoTask {

	   @Scheduled(cron="0 */1 * * * ?")
	    public void demo1(){
	        logger.info("定时任务demo1开始......");
	    }
	}


<!-- more -->


> application.xml配置  

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:task="http://www.springframework.org/schema/task"
	       xsi:schemaLocation="http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
	     http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	     http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
	     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd">
	
	    <context:component-scan base-package="com.qif" annotation-config="true">
	        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
	    </context:component-scan>
	
		<!--定时器-->
	    <task:annotation-driven/>
	</beans>






> 定时器在线转换

	http://cron.qqe2.com/