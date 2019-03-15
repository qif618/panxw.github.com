---
layout: post
category: "cloud"
title:  "zuul网关"
tags: [Cloud]
---

> 功能  
 
- 权限验证：提供统一服务出口,对每一个请求进行权限验证，不需要在每个微服务中重复校验；
- 数据监控：对请求做日志记录；
- 动态路由：默认和Ribbon结合实现了负载均衡，动态的将请求转发到不同的集群上；
- 负载分配：为每一种负载类型分配对应容量，超出则弃用；





> 引入依赖  


	<dependency>
	  <groupId>org.springframework.cloud</groupId>
	  <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
	</dependency>
	<dependency>
	  <groupId>org.springframework.cloud</groupId>
	  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
	</dependency>  



> 启动类上加注解@EnableZuulProxy  

	@SpringBootApplication
	@EnableZuulProxy
	@EnableEurekaClient
	@EnableDiscoveryClient
	public class ZuulApplication {
	
	    public static void main(String[] args) {
	        SpringApplication.run( ZuulApplication .class, args );
	    }
	}



> yml中加入配置  

	server:
	  port: 10086
	spring:
	  application:
	    name: api-gateway
	eureka:
	  client:
	    service-url:
	      defaultZone: http://localhost:8761/eureka/  
	management:
		endpoints:
		web:
		  exposure:
		    include: '*' # *号代表启用所有的监控端点
		endpoint:
		health:
		  show-details: always  # 开启健康检查，访问http://localhost:10086/actuator/health查看效果
	zuul:
	    ignoredServices: "*" 
	    routes:
	        restaurantapi: # 忽略所有默认的path，只路由到手动配置的路径
	            path: /myUser/**
	            serviceId: user-service
	            stripPrefix: true



> 测试  
 
访问http://127.0.0.1:10086/myUser/users/login,请求会被转发到user-service服务的/users/login  
zuul整合了hystrix，关掉user服务，再次访问查看效果  



