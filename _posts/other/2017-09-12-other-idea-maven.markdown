---
layout: post
category: "other"
title:  "IDEA新建聚合工程"
tags: [IDEA]
---


> 创建工程，删掉src  

File==>new==>project==>maven==>跳过模板，创建工程  
![](https://i.imgur.com/RjdqujM.png)  


<!-- more -->

==>next  
![](https://i.imgur.com/P1R3HVY.png)  
==>finish
test作为聚合工程文件夹，删掉src  
![](https://i.imgur.com/GGgD8jj.png)  



> 新建模块操作  

test文件夹上右键==>new==>module==>选择maven==>跳过模板next
![](https://i.imgur.com/I0kWvUe.png)  
弹窗窗口中输入test1==>next  
![](https://i.imgur.com/8uqmRTM.png)  
如上图输入内容==>finish  
test1中src\main下的java文件夹上右键==>mark as directory==>sources root  
test1中src\main下的resources右键==>mark as directory==>resources root  
test1中src\test\java文件夹上右键==>mark as directory==>test sources root  
效果如下图  
![](https://i.imgur.com/E2xLLpA.png)  
在src/mian/java下新建com.qif包，新建类Provider  

	public class Provider {
	    public String provide(String str){
	        return "只要998 " + str;
	    }
	}



> 重复新建模块操作，新建模块test2跟test3,结果如下图  

![](https://i.imgur.com/vZaoeat.png)  

> 给模块test2添加web.xml跟artifacts  

file==>project structure,弹出的窗口中选中module==>test2==>绿色加号==>添加web  
![](https://i.imgur.com/UEQzDx7.png)  
修改web resource directory path  
![](https://i.imgur.com/XpZHNO1.png)  
修改deployment  
![](https://i.imgur.com/nsv2CsR.png)  
修改complier output  
![](https://i.imgur.com/lt4wfi2.png)  
添加模块test1的依赖  
![](https://i.imgur.com/VC0zCug.png)  
添加artifacts  
![](https://i.imgur.com/KX9QaGq.png)  
弹出的select modules窗口中选test2，选完后如下，最后点击OK    
![](https://i.imgur.com/yQ8pXck.png)  
在test2下面的webpp文件夹下新建index.jsp  

	<%@ page import="com.qif.Provider" %>
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>test2</title>
	</head>
	<body>
	<%Provider provide = new Provider();%>
	<%=provide.provide("冰箱")%>
	</body>
	</html>
idea右上角配置tomcat  
![](https://i.imgur.com/YXVW8nl.png)  
![](https://i.imgur.com/DPqXZkQ.png)  
运行tomcat效果如下  
![](https://i.imgur.com/IkO3Me5.png)

重复给模块test2添加web.xml跟artifacts的步骤，对test3操作。注意修改tomcat跟jmx端口.
可以同时运行test3模块。  
