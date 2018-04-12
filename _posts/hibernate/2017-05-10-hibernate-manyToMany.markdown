---
layout: post
category: "hibernate"
title:  "hibernate多对多"
tags: [Hibernate]
---

学生类  


	@Entity(name="t_student")
	public class Student {
		@Id
		@GeneratedValue
		private int id;
		
		private String name;
		private int age;
		
		@Transient//表示属性不会被ORM映射到数据库中
		private double salary;
		
		@ManyToMany(cascade=CascadeType.ALL)
		@JoinTable(
			  name="t_stu_teacher",
			  joinColumns={@JoinColumn(name="sid")},
			  inverseJoinColumns={@JoinColumn(name="tid")}
		)
		private Set<Teacher> teacher;
	}  

老师类  

	@Entity
	public class Teacher {
		@Id
		@GeneratedValue
		private int id;
		
		private String name;
		
		@ManyToMany(mappedBy="teacher")
		private Set<Student> stu;
	}