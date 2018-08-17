---
layout: post
category: "spring"
title:  "maven项目整合redis"
tags: [Spring]
---



> 引入依赖  

    <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>2.6.0</version>
    </dependency>

    <dependency>
      <groupId>org.springframework.session</groupId>
      <artifactId>spring-session-data-redis</artifactId>
      <version>1.2.0.RELEASE</version>
    </dependency>

<!-- more -->


> 基于XML配置redis  
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns="http://www.springframework.org/schema/beans" xmlns:p="http://www.springframework.org/schema/p"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	                        http://www.springframework.org/schema/beans/spring-beans.xsd">
		<!-- Jedis配置 -->
		<bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
			<property name="maxIdle" value="350" />
			<property name="minIdle" value="250" />
			<property name="maxTotal" value="450" />
			<property name="testOnBorrow" value="true" />
		</bean>
	
	   <!-- 配置jedis连接工厂 -->
		<bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
			<constructor-arg index="0" ref="jedisPoolConfig" />
			<property name="hostName" value="121.22.62.34" />
			<property name="port" value="6379" />
			<property name="timeout" value="30000" />
			<property name="password" value="123456"/>
			
		</bean>
	
		 <!-- 配置redisTemplate -->
		<bean id="redisTemplate" class="org.springframework.data.redis.core.StringRedisTemplate" p:connection-factory-ref="jedisConnectionFactory">
			<property name="keySerializer">   
	            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />   
	        </property>      
	        <property name="valueSerializer">   
	            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />   
	        </property>   
	        <property name="hashKeySerializer">     
	           <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />     
	        </property>   
	        <property name="hashValueSerializer">   
	           <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />     
	        </property>
		</bean>
	</beans>



> 在类中注入redisTemplate即可使用