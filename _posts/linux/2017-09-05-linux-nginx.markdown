---
layout: post
category: "linux"
title:  "linux下安装nginx"
tags: [Linux]
---
* 目录
{:toc}



> 要安装nginx需先安装依赖  

批量安装nginx依赖  

	yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel



> 下载安装nginx  

	下载nginx:http://nginx.org/download/    
	wget url


解压  

	tar -zxvf nginx文件名

<!-- more -->

进入解压的nginx目录下执行 


	sudo ./configure --prefix=/usr/local/nginx
	sudo make
	sudo make install


查看nginx安装目录  

	whereis nginx

进入按照目录  


	cd /usr/local/nginx/sbin


启动nginx  


	./nginx


查看nginx进程  

	ps aux | grep nginx

使用ip即可访问。nginx是80端口。如果不行，关闭虚拟机防火墙  

	service iptables stop




> 配置nginx

在nginx.conf文件中添加    
 

	include vhost/*.conf;


 在nginx安装目录下的conf文件夹下面新建vhost文件夹。而不是解压文件夹下。  

	mkdir vhost
  
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

	./nginx -s reload


> 配置SSL、TCP模块


进入nginx解压目录，执行

	1、./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-stream --with-stream_ssl_module
	2、make
	3、make install
	4、nginx安装目录下，执行killall nginx && sbin/nginx
	5、通过/usr/local/nginx/sbin/nginx -V查看已安装的模块  



- PS:执行make install会覆盖nginx。所以执行configure的时候需带上之前安装的别模块  





server中配置SSL




    listen		443 ssl;
    listen		80;
    server_name www.mysite.com;
    ssl_certificate   cert/1528320307000.pem;
    ssl_certificate_key  cert/1528320307000.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;



- 因为vhost下的conf会被引入到nginx.conf下，所以证书放在nginx.conf通目录下的cert文件夹即可 

> 配置开机启动  

	init.d下新增nginx文件
	vim /etc/init.d/nginx
	授权
	chmod a+x /etc/init.d/nginx
	测试开启关闭nginx
	/etc/init.d/nginx start
	/etc/init.d/nginx stop
	将nginx服务加入chkconfig管理列表：
	chkconfig --add /etc/init.d/nginx
	设置开机启动：
	chkconfig nginx on

开机启动文件内容如下：

	#!/bin/sh
	#
	# nginx - this script starts and stops the nginx daemon

	. /etc/rc.d/init.d/functions
	 
	# Source networking configuration.
	. /etc/sysconfig/network
	 
	# Check that networking is up.
	[ "$NETWORKING" = "no" ] && exit 0
	 
	nginx="/myData/nginx/sbin/nginx"
	prog=$(basename $nginx)
	 
	NGINX_CONF_FILE="/myData/nginx/conf/nginx.conf"
	 
	[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
	 
	lockfile=/var/lock/subsys/nginx
	 
	make_dirs() {
	   # make required directories
	   user=`$nginx -V 2>&1 | grep "configure arguments:" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
	   if [ -z "`grep $user /etc/passwd`" ]; then
	       useradd -M -s /bin/nologin $user
	   fi
	   options=`$nginx -V 2>&1 | grep 'configure arguments:'`
	   for opt in $options; do
	       if [ `echo $opt | grep '.*-temp-path'` ]; then
	           value=`echo $opt | cut -d "=" -f 2`
	           if [ ! -d "$value" ]; then
	               # echo "creating" $value
	               mkdir -p $value && chown -R $user $value
	           fi
	       fi
	   done
	}
	 
	start() {
	    [ -x $nginx ] || exit 5
	    [ -f $NGINX_CONF_FILE ] || exit 6
	    make_dirs
	    echo -n $"Starting $prog: "
	    daemon $nginx -c $NGINX_CONF_FILE
	    retval=$?
	    echo
	    [ $retval -eq 0 ] && touch $lockfile
	    return $retval
	}
	 
	stop() {
	    echo -n $"Stopping $prog: "
	    killproc $prog -QUIT
	    retval=$?
	    echo
	    [ $retval -eq 0 ] && rm -f $lockfile
	    return $retval
	}
	 
	restart() {
	    configtest || return $?
	    stop
	    sleep 3 
	    start
	}
	 
	reload() {
	    configtest || return $?
	    echo -n $"Reloading $prog: "
	    killproc $nginx -HUP
	    RETVAL=$?
	    echo
	}
	 
	force_reload() {
	    restart
	}
	 
	configtest() {
	  $nginx -t -c $NGINX_CONF_FILE
	}
	 
	rh_status() {
	    status $prog
	}
	 
	rh_status_q() {
	    rh_status >/dev/null 2>&1
	}
	 
	case "$1" in
	    start)
	        rh_status_q && exit 0
	        $1
	        ;;
	    stop)
	        rh_status_q || exit 0
	        $1
	        ;;
	    restart|configtest)
	        $1
	        ;;
	    reload)
	        rh_status_q || exit 7
	        $1
	        ;;
	    force-reload)
	        force_reload
	        ;;
	    status)
	        rh_status
	        ;;
	    condrestart|try-restart)
	        rh_status_q || exit 0
	            ;;
	    *)
	        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
	        exit 2
	esac
