---
layout: post
category: "other"
title:  "npm安装卸、载依赖"
tags: [Other]
---


- 新建webpack.config.js文件  
- 配置js入口，输出文件以及输出目录  
- 在项目根目录下通过webpack命令进行打包  

<!-- more -->

安装css loader : 
 
	npm install css-loader style-loader --save-dev
安装插件对css单独打包   
 
	npm install extract-text-webpack-plugin@1.0.1 --save-dev

对CSS单独打包

        new ExtractTextPlugin("css/[name].css")  

引入webpack对通用模块单独打包  

	 new webpack.optimize.CommonsChunkPlugin({
	            name : 'common',
	            filename : 'js/base.js'
	 })  



安装插件处理html  

	npm install html-webpack-plugin@2.28.0 --save-dev

安装html loader  

	npm install html-loader --save-dev  

安装url-loader  

	先安装npm install file-loader@0.11.1 --save-dev
	后安装npm install url-loader@0.5.8 --save-dev  

> 如果安装失败，可能依赖冲突导致，可先执行卸载：npm uninstall file-loader --save-dev，然后
安装低版本0.11.1的file-loader  

***注意点：两个{}之间要有逗号***

安装webpack-dev-server  

	npm install webpack-dev-server@1.16.5 --save-dev

需要全局安装webpack-dev-server  

	npm install webpack-dev-server@1.16.5 -g



> PS:安装失败，可以尝试先卸载 






