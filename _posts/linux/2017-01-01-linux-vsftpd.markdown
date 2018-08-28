b---
layout: post
category: "linux"
title:  "linux下安装vsftpd"
tags: [Linux]
---
安装vsftpd  

```
yum -y install vsftpd  
```

在根目录创建ftpfile  

```
mkdir ftpfile
```

在ftpfile目录下创建ftp用户  

```
useradd ftpuser -d /ftpfile/ -s /sbin/nologin
```
<!-- more -->

赋予权限  

```
chown -R ftpuser.ftpuser /ftpfile/
```

查看权限  

```
ll | grep ftp
```

重置ftpuser密码  

```
passwd ftpuser
```

> 进入/etc/vsftpd文件夹下新建chroot_list文件,里面添加用户名  

执行命令，讲selinux改成disabled  

```
vim /etc/selinux/config
```
> 编辑/etc/vsftpd下的vsftpd.conf文件

启动vsftpd服务器  
```
service vsftpd start
```
查看状态  

```
service vsftpd status
```

> 在浏览器中输入ftp://192.168.1.26访问

> 要在防火墙中开放端口