---
layout: post
category: "hibernate"
title:  "hibernate-spring链接Oracle"
tags: [Hibernate]
---

> 使用hibernate以及spring链接Oracle数据库的Application.xml的配置：  

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    		<property name="user" value="youruser"></property>
    		<property name="password" value="yourpassword"></property>
    		<property name="driverClass" value="oracle.jdbc.driver.OracleDriver"></property>
    		<property name="jdbcUrl" value="jdbc:oracle:thin:@192.168.0.1:1521:instance"></property>
    		<!-- <property name="hibernate.dialect" value="org.hibernate.dialect.OracleDialect"/> -->
    		<property name="initialPoolSize" value="5"></property>
    		<property name="maxPoolSize" value="10"></property>
    	</bean>
    	
    	<!-- 配置sessionFactory -->
    	<bean id="sessionFactory" class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
    		<!-- <property name="configLocation" value="classpath:hibernate.cfg.xml"></property> -->	
    			<!-- 查找哪个包中的实体类 -->
    		<property name="packagesToScan" >
    			<value>com.lyf.entity</value>
    		</property>
    		<!-- 注入数据源 -->
    		<property name="dataSource" ref="dataSource" />
    		<property name="hibernateProperties">
    			<!-- <value> hibernate.dialect=org.hibernate.dialect.HSQLDialect </value> -->
    			<props> 
                    <!--数据库方言-->
    				<prop key="hibernate.dialect">org.hibernate.dialect.Oracle10gDialect</prop>
    				<prop key="hibernate.show_sql">false</prop>
    				<prop key="hibernate.hbm2ddl.auto">update</prop>
    				<prop key="hibernate.format_sql">false</prop>
    			</props>
    		</property>
    	</bean>  


> 加入的驱动包：ojdbc6.jar.