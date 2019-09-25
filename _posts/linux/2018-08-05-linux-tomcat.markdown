---
layout: post
category: "linux"
title:  "生产环境tomcat占用CPU 100%"
tags: [Tomcat]
---



> 生产环境CPU逐渐飙升到100%，导致项目卡顿，访问失败  

通过top命令查看进程下各个线程的CPU使用情况

	top -H -p PID

	PID   USE   PR   NI  VIRT  RES   SHR S %CPU   %MEM    TIME+  COMMAND    
	23658 qifly  20   0  1842m  136m  13m R  100.2 0.9    1:35.45 java

通过JDK的jstack命令查看线程的堆栈信息，找到出问题的代码  

	jstack PID|grep -A 20 TID 
	说明：PID为JAVA进程ID  TID为16进制的线程ID -A 20为打印20行
	经排查为特殊情况导致while死循环导致CPU飙升100%

或者通过jstack将堆栈信息保存至文件中  

	jstack PID > info.log