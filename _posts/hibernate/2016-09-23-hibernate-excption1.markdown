---
layout: post
category: "hibernate"
title:  "hibernate方言异常"
tags: [Hibernate]
---
使用hibernate.cfg.xml配置时，启动Junit初始化表时，报错：  

    org.hibernate.HibernateException: The dialect was not set. Set the property  

这是由于没配置mapping引起的。