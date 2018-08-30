---
layout: post
category: "other"
title:  "bat脚本设置mysql环境变量"
tags: [Mysql]
---



## 步骤  

先创建变量，如果存在先删除再创建，然后获取PATH值，判断是否已存在变量，存在则不更新，否则更新  

	

> 新建setPath.bat脚本,必须以管理员的方式运行，否则拒绝  
	
	@ECHO OFF表示关闭命令打印

> 获取环境变量Path,并赋值到变量pt中，如果用%Path%会带上用户变量而且会直接显示变量值，而不是变量名  

	FOR /f "skip=1 tokens=* delims=" %%i in ('wmic ENVIRONMENT WHERE "Name='Path'and UserName='<System>'" GET VariableValue') do (
		SET pt=%%i
		GOTO pt
	)
	:pt

<!-- more -->

> 脚本内容如下,其中%~d0表示bat所在的盘  



	@ECHO OFF
	
	set MYSQL_HOME_5_6=%~d0/mysql/mysql-5.6.41-winx64
	wmic ENVIRONMENT where name="MYSQL_HOME_5_6" delete
	wmic ENVIRONMENT create name="MYSQL_HOME_5_6", username="<system>",VariableValue="%MYSQL_HOME_5_6%"
	
	wmic ENVIRONMENT where "name='Path' and username='<system>'" get VariableValue | findstr /ic:"%%MYSQL_HOME_5_6%%\bin">nul
	
	if "%errorlevel%"=="0" echo 数据库环境变量已存在&pause&goto :eof
	
	
	FOR /f "skip=1 tokens=* delims=" %%i in ('wmic ENVIRONMENT WHERE "Name='Path'and UserName='<System>'" GET VariableValue') do (
		SET pt=%%i
		GOTO pt
	)
	:pt
	
	wmic ENVIRONMENT where "name='Path' and username='<system>'" set VariableValue="%%MYSQL_HOME_5_6%%\bin;%pt%"

	echo 设置数据库环境变量成功
	
	PAUSE


