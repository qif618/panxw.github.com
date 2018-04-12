---
layout: post
category: "hibernate"
title:  "hibernate注解"
tags: [Hibernate]
---
### 一对一   ###

学生类：  

    @Id
    @GeneratedValue
    private int id;
    private String name;
    private int age;
    @Transient//表示属性不会被ORM映射到数据库中
    private double salary;
    @OneToOne(cascade=CascadeType.ALL)//级联操作，包含所有
    @JoinColumn(name="pid")
    private IdCard card;

<!-- more -->

工号类：  

    @Id
    @GeneratedValue(generator="pid")
    @GenericGenerator(name="pid",strategy="assigned")
    @Column(length=18)
    private String id;
    private String sname;
    @OneToOne(mappedBy="card")
    private Student stu;