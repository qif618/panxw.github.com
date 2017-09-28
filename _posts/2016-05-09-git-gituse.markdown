---
layout: post
category: "git"
title:  "git安装使用"
tags: [Git]
---
1、git[安装](https://git-for-windows.github.io/)  

2、设置用户名以及邮箱  

    $ git config --global user.name "用户名" 
    $ git config --global user.email "邮箱"
<!-- more -->
3、配置SSH，连接远程仓库  

    $ ssh-keygen -t rsa -C"邮箱" //无脑回车

4、在github上用户名下打开settings,通过左侧的SSH AND GPG KEYS中添加SSH KEY（点击new key,出现key栏位）。打开C:\Users\你的计算机用户名）内 .ssh 文件夹，找到id_rsa.pub，拷贝全部内容至github中的KEY栏位。

![](../assets/test_result_cloudflare.png)

5、测试是否连接成功  

    输入$ ssh -T git@github.com测试是否连接成功。  
    
![](/assets/test_result_cloudflare.png)

6、从github上clone项目到本地仓库。  

    先cd进存放项目的文件夹。然后克隆：
    $ git clone https://github.com/用户名/用户名.github.io.git 

7、从github上更新文件到本地（项目已被克隆下来）。
	
    1、cd到项目文件夹路径
    2、$  git pull

8、提交本地文件到远程仓库

    1、cd到项目文件夹路径
    2、$ git add .
    3、$ git commit -m "修改的注释"
    4、$ git push origin master   











