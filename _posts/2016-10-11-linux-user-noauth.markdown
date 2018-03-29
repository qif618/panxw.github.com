---
layout: post
category: "linux"
title:  "解决用户没权限问题"
tags: [linux]
---

切换到root  

```
su root
```
 
查看sudoers权限  

```
ll /etc/sudoers
```
<!-- more -->

修改权限  

```
chmod 777 /etc/sudoers
```

vim /etc/sudoers,找到 root    ALL=(ALL)    ALL的字段,在下一行追加:


```
username    ALL=(ALL)     ALL
```


保存退出，然后修改权限回440  

```
chmod 440 /etc/sudoers
```


