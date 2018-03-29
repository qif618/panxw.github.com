---
layout: post
category: "git"
title:  "git命令"
tags: [Git]
---



查看本地分支:  

```
git branch
```

查看远程分支：  

```
git branch -r
```

初始化本地git:  

```
git init
```

查看状态：  

```
git status
```

添加到本地：  

```
git add .
```

提交：   

```
git commit -am 'first commit'
```
  
连接到远程仓库：  

```
git remote add origin https://github.com/yourgithub/test.git
```

推送到远程：  

```
git push -u origin master
```

强制推送：  

```
git push -u -f origin master
```

从远程拉取更新：  

```
git pull
```

查看当前remote url  

```
git remote -v
```

修改remote url为ssh  

```
git remote set-url origin git@github.com:yourgithub/test.git
```

切换分支,切换到v1.0  

```
git checkout v1.0
```

本机同步远程成功，拉取文件  

    git remote add origin git@github.com:yourgithub/test.git
    
    git pull origin master
