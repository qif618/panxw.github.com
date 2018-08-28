---
layout: post
category: "spring"
title:  "Autowired和Resource区别"
tags: [Spring]
---
## Autowired和Resource区别 ##




- @Autowired默认按类型装配（这个注解是属业spring的），默认情况下必须要求依赖对象必须存在，如果要允许null值，可以设置它的required属性为false  

<!-- more -->

- @Resource是JAVA1.6支持的注解，当知道name,则只会按name装配。如：
@Resource(name="aa")。  
当没有aa这个实例时，不会装配别的实例  



- 如果不指定，根据注解的字段名装配，如果没有找到，按类型装配 。  
如：  
@Resource
UserService userService//这种情况。当没有userService实例时。会自动查找UserService的其他实例



> PS：@Resource不知道name时，跟@Autowired没什么差别。