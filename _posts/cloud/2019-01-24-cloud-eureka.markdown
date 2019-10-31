---
layout: post
category: "cloud"
title:  "eureka注册中心"
tags: [Cloud]
---


> 创建springboot项目，勾选Cloud Discovery下的Eureka Server，启动类加上注解 @EnableEurekaServer


	@EnableEurekaServer
	@SpringBootApplication
	public class EurekaServerApp {
		public static void main(String[] args) {
			SpringApplication.run(EurekaServerApp.class, args);
		}
	}


<!-- more -->

在地址里加上用户名密码，然后运行还是报错,需加配置类，新版security默认开启了csrf

	@EnableWebSecurity
	@Configuration
	public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
	
	    @Override
	    protected void configure(HttpSecurity http) throws Exception {
	   http.csrf().disable(); //关闭csrf
	    http.authorizeRequests().anyRequest().authenticated().and().httpBasic(); //开启认证
	    }
	
	}

> application.yml文件  

	spring:
	  application:
	    name: eureka-server
	  security:
		#basic：enabled参数在boot2.0之后无效
	    user:
	      name: admin
	      password: admin
	server:
	  port: 1111
	management:
	  endpoints:
	    web:
	      exposure:
	        include: "*"
	  endpoint:
	    health:
	      show-details: always
	eureka:
	  server:
	    enable-self-preservation: true    #开启服务器自我保护，客户端心跳检测15分钟内错误达到80%服务会保护
	    eviction-interval-timer-in-ms: 15000 #清理间隔（单位毫秒，默认是60*1000）将客户端剔除的服务在服务注册列表中剔除 
	  client: 
	    register-with-eureka: false  #false表示不作为一个客户端注册到注册中心
	    fetch-registry: false      #为true时，可以启动，但报异常：Cannot execute request on any known server
	    serviceUrl:
	       defaultZone: http://admin:admin@192.168.1.67:1111/eureka
	  instance:
	    hostname: 192.168.1.67
	    prefer-ip-address: true  #表示将自己的ip注册到Eureka Server
	    instance-id: ${spring.application.name}:${spring.cloud.client.ip-address}:${spring.application.instance_id:${server.port}}
	    lease-renewal-interval-in-seconds: 2    # 续约更新时间间隔（默认30秒）
	    lease-expiration-duration-in-seconds: 30 # 续约到期时间（默认90秒）



> 同一台服务器高可用部署  
		
	spring:
	  application:
	    name: eureka-server
	  security:
	    user:
	      name: admin
	      password: admin
	server:
	  port: 1111
	management:
	  endpoints:
	    web:
	      exposure:
	        include: "*"
	  endpoint:
	    health:
	      show-details: always
	eureka:
	  server:
	    enable-self-preservation: true    #开启服务器自我保护，客户端心跳检测15分钟内错误达到80%服务会保护
	    eviction-interval-timer-in-ms: 15000 #清理间隔（单位毫秒，默认是60*1000）将客户端剔除的服务在服务注册列表中剔除 
	  client: 
	    register-with-eureka: true  #true作为一个客户端注册到注册中心
	    fetch-registry: true      #为true时，如另外注册中心没启动，会报异常，但能起来：Cannot execute request on any known server
	    serviceUrl:
			#将服务中心注册到2222、3333端口到服务上，同理，另外两个注册中心也相互注册
	       defaultZone: http://admin:admin@192.168.1.67:2222/eureka,http://admin:admin@192.168.1.67:3333/eureka
	  instance:
	    hostname: 192.168.1.67
	    prefer-ip-address: true  #表示将自己的ip注册到Eureka Server
	    instance-id: ${spring.application.name}:${spring.cloud.client.ip-address}:${spring.application.instance_id:${server.port}}
	    lease-renewal-interval-in-seconds: 2    # 续约更新时间间隔（默认30秒）
	    lease-expiration-duration-in-seconds: 30 # 续约到期时间（默认90秒）



pom文件  

	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.qif</groupId>
	<artifactId>register-center</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>register-center</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
		<spring-cloud.version>Finchley.SR2</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
