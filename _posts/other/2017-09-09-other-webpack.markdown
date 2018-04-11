---
layout: post
category: "other"
title:  "webpack安装"
tags: [webpack]
---  


> webpack依赖nodejs，故先安装nodejs  

[下载](https://nodejs.org/download/release/v4.4.7/)  
安装：一路next  

## 安装webpack   ##

> 在项目中初始化npm,生成packjson文件  

    npm init

全局卸载,防止之前有安装错误  

    npm uninstall webpack -g   

<!-- more -->

> 如果不安装淘宝npm镜像，可以直接使用nmp install -g webpack@1.15.0直接安装webpack
安装了淘宝npm镜像增加下载速度  

    npm install -g cnpm --registry=https://registry.npm.taobao.org



> 全局安装webpack  

    cnpm install -g webpack@1.15.0

cd到你的项目里面,### 局部安装 ###,防止部署的环境的版本不一致  

    cnpm install --save-dev webpack@1.15.0

> 安装成功则多一个node_moduls文件夹  
> webpack -v 查看显示版本  


