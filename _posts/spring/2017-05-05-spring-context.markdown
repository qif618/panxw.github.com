---
layout: post
category: "spring"
title:  "annotation-config和component-scan"
tags: [spring]
---

合并注解配置   

    <context:component-scan base-package="com.**.controller" annotation-config="true"/>
    
的作用等价于  这两句

    <context:component-scan base-package="com.**.controller"/>
    <context:annotation-config/>


说明：http://www.cnblogs.com/lcngu/p/5080702.html