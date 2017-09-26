---
layout: post
category: "python"
title:  "python初认识"
tags: [Python]
---
### python安装   ###

 
- www.pathon.org下载pythoy  
    
- 安装pythoy,安装时后续add path  
    Eclipse中安装python环境：点击help,点击install software,add 名称pyDev,[链接](http://www.pydev.org/update_sites/4.5.5),python4.5.5是JDK7支持的最高版本，
    pydev安装跟jdk版本、eclipse版本要一致，否则安装完成后，在preference中不能找到pydev.  
 
-    PS:安装完成pydev后，需在preference中的pyDEV中配置本地安装的python.  
   
-  PS:python3.5不再支持mysqldb，所以需安装pymysql，命令：执行pip3 install pymysql即可。
<!-- more -->   
### 设置python默认编码 ###
    import sys  
    reload(sys)  
    sys.setdefaultencoding('utf8')

### python初使用 ###
    CMD,输入python命令，启动python运行环境.输入1+1,回车得结果2  
    
    - print("hello"),输出结果hello,print("hi","lili"),输出结果hi lili,因为print方法碰到逗号就打印空格  
    - 输入name=input()，则输入任何东西按回车后，内容都被保存在名字为name的变量中，通过print(name)可查看。  
    - 输出i'm时可用转义字符\，例如print('\"ddd\"')  
    name=100  
    if name>30:  
       print(name)  
    else:  
       print(-name)  
    - 整除求余print(10//3)为3，print(10/3)为3.0
    - print('name,%.2f' %0.235423)结果为0.24
    for循坏：  
    names=['dddd','eeee','rrewwr']  
    for name in names:  
    print(name)  
    - 取LIST前三个数L[0:3]
 
#### _private_或者__private表示私有方法，是不应该让外部使用的（python没那么严格的约束方法不让外部使用，所以是不应该） 
    def _private_1(name):  
    	return "hello,%s" % name  
    def _private_2(name):  
    	return "Hi,%s" % name  
    
    def method3(name):  
    	if len(name) == 3:  
    		_private_1(name)  
    	else:  
    		_private_2(name)  

#### 安装第三方模块：  
    通过命令行下执行pip install pillow命令即可安装pillow  (前提是先安装了pip,安装python时勾选pip即可)  
    定义一个类class Student(object):  
    构造方法：	def __init__(self,name,age): 
    		self.age = age  
    		self.name = name  
#### 对象与数据 ####
    判断数据类型：type(11) type('ddd')   
    判断对象：isinstance(dog,animal)  
    获取一个对象的所有属性和方法：dir('ABC')


#### 判断对象是否存在某个属性：hasattr(obj,'x')   ####
    获取对象属性：getattr(obj,'x')  
    设置对象属性：setattr(obj,'y',20)  
#### 定义类属性：  
	class Student(object):  
		name="AAAA"  
	print(Student.name)结果为AAA  

#### __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称 ####









