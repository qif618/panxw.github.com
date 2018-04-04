---
layout: post
category: "hibernate"
title:  "hibernate缓存"
tags: [Hibernate]
---



> 一级缓存  

- session级别，默认开启，不能关闭。  
- 当程序执行时，先放我一级缓存，如果没有才访问数据库。


##  二级缓存 ##
> 二级缓存说明  

<!-- more -->

- sessionFactory级别，默认不开启  
- 先访问一级缓存，没有访问二级，没有则访问数据库  
- 二级缓存的策略一般有:read-only、nonstrict-read-write、read-write、transactional
- 通常二级缓存都配置成read-only



> Hibernater二级缓存插件  



- EhCache：可作为进程范围的缓存，存放数据的物理介质可以是内存或硬盘，对Hibernate的查询缓存提供了支持。


- OSCache：可作为进程范围的缓存，存放数据的物理介质可以是内存或硬盘，提供了丰富的缓存数据过期策略，对Hibernate的查询

#### 二级缓存配置 ####



> 需引入相关包  

	<!-- 启动二级缓存 -->  
    <property name="hibernate.cache.use_second_level_cache">true</property>  
	<!--hibernate4指定二级缓存的提供类-->  
	<property name="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</property>  
    <!--指定二级缓存配置文件的位置-->  
    <property name="hibernate.cache.provider_configuration_file_resource_path">ehcache.xml</property>  



> ehcache.xml的配置  

	<ehcache updateCheck="false">  
		<!-- Sets the path to the directory where cache .data files are created.  
		 If the path is a Java System Property it is replaced by its value in the running VM.  
		 The following properties are translated:  
		 user.home - User's home directory  
		 user.dir - User's current working directory  
		 java.io.tmpdir - Default temp file path  
		-->  
		
		<!--指定二级缓存存放在磁盘上的位置-->  
		<diskStore path="java.io.tmpdir" />  
		<!--<diskStore path="user.dir" />-->  
		
		<!--可以每个实体类指定一个对应的缓存，如果没有匹配到该类，则使用这个默认的缓存配置-->  
		<!--  
		maxElementsInMemory: 在内存中存放的最大对象数;  
		eternal: 是否永久保存缓存,设置成false;  
		timeToLiveSeconds: 以创建时间为基准开始计算的超时时长;  
		timeToIdleSeconds: 在创建时间和最近访问时间中取出离现在最近的时间作为基准计算的超时时长;  
		overflowToDisk: 如果对象数量超过内存中最大的数,是否将其保存到磁盘中,设置成true;  
		如果仅设置timeToLiveSeconds,则该对象的超时时间=创建时间+timeToLiveSeconds,假设为A;  
		如果没设置timeToLiveSeconds,则该对象的超时时间=max(创建时间,最近访问时间)+timeToIdleSeconds,假设为B;  
		如果两者都设置,则取出A、B中的较小值,即min(A,B),表示只要有一个超时成立即算超时;  
		　	-->  
		<defaultCache   
		maxElementsInMemory="10000"   
		eternal="false"   
		overflowToDisk="true"   
		timeToIdleSeconds="120"   
		timeToLiveSeconds="120"   
		diskPersistent="false"   
		diskExpiryThreadIntervalSeconds="120" />  
		
		<!--可以给每个实体类指定一个配置文件，通过name属性指定，要使用类的全名-->  
		<!--<cache name="com.lyf.entity.Student" maxElementsInMemory="10000" eternal="false" timeToIdleSeconds="300" timeToLiveSeconds="600" overflowToDisk="true" />-->  
	</ehcache>   