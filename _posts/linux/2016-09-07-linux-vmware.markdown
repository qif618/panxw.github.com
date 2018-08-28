---
layout: post
category: "linux"
title:  "安装虚拟机"
tags: [Linux]
---

下载VMWARE12进行安装。  

  
  [视频教程](https://www.imooc.com/learn/175)

备份源   
`mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup` 

输入ifconfig后没有eth0信息的，找不到IP的，可以试试输入 ifup eth0 (就是打开网卡)，然后再输入ifconfig  

    vim /etc/sysconfig/network-scripts/ifcfg-eth0