---
layout: post
category: "springBoot"
title:  "定义自己的starter"
tags: [SpringBoot]
---



> 原理  

- springboot启动默认只会扫描本项目下的带@Configuration注解的类，如果自定义starter，不在本工程中，是无法加载的，故要配置META-INF/spring.factories配置文件

- springboot启动时会去依赖的starter包中寻找 resources/META-INF/spring.factories，自动配置bean  

- @Configuration与@Bean->基于java代码的bean配置

- @Conditional->设置自动配置条件依赖

- @EnableConfigurationProperties与@ConfigurationProperties->读取配置文件转换为bean

- @EnableAutoConfiguration->实现bean发现与加载。


<!-- more -->

> 添加依赖


	<groupId>com.qif</groupId>
	#命名规范 name-spring-boot-starter
	<artifactId>qif-spring-boot-starter</artifactId>
	<version>1.0.0-SNAPSHOT</version>
	<packaging>jar</packaging>

	<dependencies>
	    <dependency>
	        <groupId>org.springframework.boot</groupId>
	        <artifactId>spring-boot-configuration-processor</artifactId>
	        <optional>true</optional>
	    </dependency>
	    <dependency>
	        <groupId>org.springframework.boot</groupId>
	        <artifactId>spring-boot-autoconfigure</artifactId>
	    </dependency>
	</dependencies>



> 配置文件读取类  

	@ConfigurationProperties(prefix = "com.qif")
	@Data
	public class QifProperties {
	    private boolean enabled;
	    private String name;
	}



> 编写功能类  

	public class QifService {
	    private String name;
	
	    public QifService(String name) {
	        this.name = name;
	    }
	    
	    public void sayHello() {
	       System.out.println("hello:"+name)
	    }
	    
	}



> 编写AutoConfigure类  

	@Configuration
	#当classpath下发现该类的情况下进行自动配置
	@ConditionalOnClass(QifService.class)
	@EnableConfigurationProperties(QifProperties.class)
	@ConditionalOnProperty(prefix = "com.qif", value = "enabled", havingValue = "true")
	public class StarterAutoConfigure {
	
	    @Autowired
	    private QifProperties properties;
	
	    @Bean
		#当Spring Context中不存在该Bean时
	    @ConditionalOnMissingBean
	    QifService qifService(){
	        return new QifService(properties.getName());
	    }
	
	}

> 在resources/META-INF/下创建spring.factories文件并编写内容

	org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
	  com.qif.StarterAutoConfigure



> 测试
	
	#将自定义starter依赖添加到项目中
	<dependency>
	    <groupId>com.qif</groupId>
	    <artifactId>qif-spring-boot-starter</artifactId>
	    <version>1.0-SNAPSHOT</version>
	 </dependency>

	#application.yml 配置文件中添加配置信息
	com
	  qif
	    enabled: true
	    name： quincy

	#JUNIT测试
	@Autowired
	private QifService qifService;
	
	@Test
	public void starterTest() {
	    System.out.println(qifService.sayHello());
	}