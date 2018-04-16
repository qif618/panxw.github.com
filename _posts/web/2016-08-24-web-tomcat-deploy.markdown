---
layout: post
category: "web"
title:  "tomcat部署web项目"
tags: [tomcat]
---



> 方法1  

将项目war包（可在eclipse中将项目export war包）直接拷贝到tomcat目录下的webapps文件下，运行tomcat，它会自行解压，通过项目名直接访问即可

<!-- more -->

> 方法二  

1、在Eclipse中的最底下的Servers视图，将所有部署的项目移除单击鼠标右键选择clean（为了第三步按钮不变灰）；在该服务器上继续单击鼠标右键选择“Open” 
 
2、在“Server Locations”处选择“Use Tomcat installation”，可以清楚的看到默认是“Use Workspace metadata”，即上边所说的目录。然后在Deploy的path处写上webapps  

3、修改完成之后，保存即可。这样以后的项目都会发布到tomcat下的Webapps下了，运行tomcat，通过项目名称即可访问



> 方法三	

 修改配置文件tomcat/conf/server.xml，在server.xml 中<Host></Host> 中增加   
	
	<Context path="/hello" docBase="D:/programFiles/eclipse/workspace/.metadata/.plugins/org.eclipse.wst.server.core/tmp1/webapps/SPMVC4.0" debug="0" reloadable="true"/>

其中，  
docBase：WEB-INF 的上一级目录
path：虚拟路径，
访问方式：根据web.xml和Context的配置，http://localhost:8080/hello  
注意：  
1，war包不放在webapps下，tomcat时不会自动解压的，所以需要自己解压好，把解压后的第一级目录配置到docBase下即可  

2，如果war是在webapps下，可以不修改server.xml 直接访问，但是如果仍想配置，或修改访问路径，比如域名直接访问，则可以这样配置，不需要解压war  

	<Context path="" docBase="/SPMVC4.0" debug="0" reloadable="true"/>
3、docBase指向的是项目war包解压后的路径，而非workspace下的项目地址



> 方法四  

在tomcat目录下的\conf\Catalina\localhost，随意新建一个XML，但是XML中的名称要跟其中的path一直。XML中的内容：(如新建hello.xml)  

	<Context path="/hello"  docBase="D:/SPMVC4.0"  reloadable="true"/>

启动tomcat，通过http://localhost:8080/hello/访问即可。  

**注**：方法四更灵活，只需在Catalina\localhost配置xml，不需要去修改server.Xml，也不需把war包放只webapps下





      