---
layout: post
category: "other"
title:  "fastjson使用"
tags: [Other]
---


> json字符串  

	{
		"code": 200,
		"msg": "成功",
		"data": {
			"recordList": [
				{
					"name": "lili",
					"age": 18
				},
				{
					"name": "quincy",
					"age": 20
				}
			]
		}
	}

<!-- more -->

> 将json字符串转化JSON对象  

	JSONObject json = JSONObject.parseObject(jsonStr);



> 获取JSON对象中的list数组  

	JSONObject data = (JSONObject) json.get("data");
	JSONArray list = data.getJSONArray("recordList");



> 新建JSON对象,赋值，将JSON对象转化为json字符串  

	JSONObject result = new JSONObject();
	result.put("total", list.size());
	result.put("rows", list);
	String jsonStr = result.toJSONString();

将json字符串转化为javabean  

	TestPerson person = JSON.parseObject(jsonstr, TestPerson.class);

	personListStr = "[{"name":"aa","age":"22"},{"name":"2","bb":"30"}]";
	List<TestPerson> personList = JSONObject.parseArray(personListStr, TestPerson.class);