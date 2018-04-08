---
layout: post
category: "web"
title:  "JSON测试"
description: "定义一个json，在页面中引入json，调用"
tags: [Web]
---

WEB-INF文件夹下新建json文件夹，新建一个test.json文件  

	testJson={
		"testA":{
			"a1" : "20", 
			"a2" : "30", 
		},
		"testB":{
			"b1" : "20", 
			"b2" : "30", 
		},
	} 

<!-- more -->
在JSP中通过引入json  

	<script type="text/javascript" src="/WEB-INF/json/testJson.json"></script>

给JSP中id为test的DIV赋值  

	$("#test").html(testJson[a1].a1);

