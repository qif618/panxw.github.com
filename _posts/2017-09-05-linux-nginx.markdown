---
layout: post
category: "linux"
title:  "linux下安装nginx"
tags: [linux]
---

批量安装nginx依赖  

```
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```

下载nginx:http://nginx.org/download/    

```
wget url
```

解压  

```
tar -zxvf nginx文件名
```
<!-- more -->

进入解压的nginx目录下执行 

```
sudo ./configure
sudo make
sudo make install
```

查看nginx安装目录  

```
whereis nginx
```

进入按照目录  

```
cd /usr/local/nginx/sbin
```

启动nginx  

```
./nginx
```

查看nginx进程  

```
ps aux | grep nginx
```

使用ip即可访问。nginx是80端口。如果不行，关闭虚拟机防火墙  

```
service iptables stop
```
 
在nginx.conf文件中添加  
 
```
include vhost/*.conf;
```

 在nginx安装目录下的conf文件夹下面新建vhost文件夹。而不是解压文件夹下。  
 
```
mkdir vhost
```
  
 vhost中创建文件ghotmall.conf,添加如下内容  

	server {
	    default_type 'text/html';
	    charset utf-8;
	    listen 80;
	    autoindex on;
	    server_name www.ghotmall.com ghotmall.com;
	    access_log /usr/local/nginx/logs/access.log combined;
	    index index.html index.htm index.jsp index.php;
	    #error_page 404 /404.html;
	    if ( $query_string ~* ".*[\;'\<\>].*" ){
	        return 404;
	    }
	
	    location / {
	        proxy_pass http://127.0.0.1:8080;
	        add_header Access-Control-Allow-Origin *;
	    }
	}



继续添加image.ghotmall.conf文件，添加如下内容  

	server {
	    listen 80;
	    autoindex off;
	    server_name image.ghotmall.com;
	    access_log /usr/local/nginx/logs/access.log combined;
	    index index.html index.htm index.jsp index.php;
	    #error_page 404 /404.html;
	    if ( $query_string ~* ".*[\;'\<\>].*" ){
	        return 404;
	    }
	    location ~ /(ghotmall_fe|ghotmall_admin_fe)/dist/view/* {
	        deny all;
	    }
	    location / {
	        root /ftpfile/;
	        add_header Access-Control-Allow-Origin *;
	    }
	}


重启nginx 

```
./nginx -s reload
```
> 访问时要在本机hosts配置ghomall.com映射。非在虚拟机中配置

