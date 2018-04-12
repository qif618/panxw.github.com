---
layout: post
category: "hibernate"
title:  "hibernate一对多"
tags: [Hibernate]
---
一的一方：学生类  

	@Id
	@GeneratedValue
	private int id;
	
	private String name;
	private int age;
	
	@Transient//表示属性不会被ORM映射到数据库中
	private double salary;
	
	@ManyToOne(cascade=CascadeType.ALL)//用1的一方维护关联关系
	@JoinColumn(name="cid")
	private ClassRoom classRoom;  

多的一方：教室  

	@Id
	private String id;
	
	private String cname;