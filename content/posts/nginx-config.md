---
title: Nginx配置详解
date: 2019-08-27 21:27:28
cover: 
categories: Nginx
tags:
- Nginx
---

> Nginx配置详解

<!--more-->

```
#运行用户
#user  nobody;

#启动进程，通常设置成和cpu的数量相等或者2倍于cpu的个数(具体结合cpu和内存)。默认为1
worker_processes  1;

#全局的错误日志和日志级别[ debug | info | notice | warn | error | crit ]
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid进程文件
#pid        logs/nginx.pid;

#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的值ulimit -n）
#与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
#默认不需设置
#worker_rlimit_nofile 65535;

#工作模式以及连接数上限
events {
	#epoll是多路复用IO(I/O Multiplexing)中的一种方式，
	#仅用于linux2.6以上内核，可以大大的提高nginx的性能
	#参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ];
	#在FreeBSD上面，就用kqueue模型
	#window下没有epoll
	#use epoll;
	
	#单个后台worker process进程的最大并发连接数。默认为1024
	#单个进程最大连接数（最大连接数=连接数*进程数）
    worker_connections  1024;
	
	#并发总数是worker_processes和worker_connections的乘积
	#即 max_clients=worker_processes * worker_connections
	#在设置了反向代理的情况下，max_clients=worker_processes * worker_connections / 4
	#worker_connections 值的设置跟物理内存大小有关
    #因为并发受IO约束，max_clients的值须小于系统可以打开的最大文件数
	#而系统可以打开的最大文件数和内存大小成正比，一般1GB内存的机器上可以打开的文件数大约是10万左右
    #我们来看看360M内存的VPS可以打开的文件句柄数是多少：
    #$ cat /proc/sys/fs/file-max
    #输出 34336
    #32000 < 34336，即并发连接总数小于系统可以打开的文件句柄总数，这样就在操作系统可以承受的范围之内
    #所以，worker_connections 的值需根据 worker_processes 进程数目和系统可以打开的最大文件总数进行适当地进行设置
    #使得并发总数小于操作系统可以打开的最大文件数目
    #其实质也就是根据主机的物理CPU和内存进行配置
    #当然，理论上的并发总数可能会和实际有所偏差，因为主机还有其他的工作进程需要消耗系统资源。
    #ulimit -SHn 65535
}

#设定http服务器
http {
	#设定mime类型,类型由mime.type文件定义。文件扩展名与文件类型映射表
    include       mime.types;
	
	##默认文件类型
    default_type  application/octet-stream;
	
	#默认编码
	#charset utf-8; 
	
	#服务器名字的hash表大小
	#server_names_hash_bucket_size 128; 
	
	#上传文件大小限制
	#client_header_buffer_size 32k; 
	
	#设定请求缓
	#large_client_header_buffers 4 64k; 
	
	#设定请求缓
	#client_max_body_size 8m; 
	
	#开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，
	#对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，
	#以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。默认开启状态
	sendfile on; 
	
	#开启目录列表访问，合适下载服务器，默认关闭。
	#autoindex on; 
	
	#防止网络阻塞
	#tcp_nopush on; 
	
	#防止网络阻塞
	#tcp_nodelay on; 
	
	#长连接超时时间，单位是秒
	#keepalive_timeout 120; 
	keepalive_timeout  65;
	
	#FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
	#fastcgi_connect_timeout 300;
	#fastcgi_send_timeout 300;
	#fastcgi_read_timeout 300;
	#fastcgi_buffer_size 64k;
	#fastcgi_buffers 4 64k;
	#fastcgi_busy_buffers_size 128k;
	#fastcgi_temp_file_write_size 128k;

	#gzip模块设置
	#开启gzip压缩输出
	#gzip on; 
	#gzip_min_length 1k; #最小压缩文件大小
	#gzip_buffers 4 16k; #压缩缓冲区
	#gzip_http_version 1.0; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
	#gzip_comp_level 2; #压缩等级
	#gzip_types text/plain application/x-javascript text/css application/xml;
	#压缩类型，默认就已经包含text/html，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
	#gzip_vary on;
	
	#开启限制IP连接数的时候需要使用
	#limit_zone crawler $binary_remote_addr 10m; 

	upstream www.xttblog.com {
		#upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
		#server 192.168.80.121:80 weight=3;
		
		#每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
		ip_hash;
		
		server 10.10.2.71:8080;
		#server 192.168.80.123:80 weight=3;
	}
	
	#设定日志格式
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

	#access日志文件的路径,采用上面定义的main 格式记录
    #access_log  logs/access.log  main;   

	#虚拟主机的配置
    server {
		#侦听80端口
        listen       80;
		
		#定义使用 localhost，可以自定义域名访问
		#域名可以有多个，用空格隔开
        server_name  localhost;		

		#编码
        #charset koi8-r;

        #access_log  logs/host.access.log  main;

		#默认请求
        location / {
			proxy_pass http://www.xttblog.com;
			proxy_redirect off;
			proxy_set_header X-Real-IP $remote_addr;
			
			#后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			
			#以下是一些反向代理的配置，可选。
			proxy_set_header Host $host;
		
			#定义服务器的默认网站根目录位置
            root D:/apache-tomcat-6.0.41/webapps/test;
			
			#定义首页索引文件的名称。定义多个用空格分隔
            index index.jsp;
        }
		
		#定义404错误提示页面
        #error_page  404              /404.html;
		
		#定义50x错误提示页面
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
			#定义服务器的默认网站错误页面位置
            root   html;
        }

		#静态文件，nginx自己处理。正则表达式
        #location ~ ^/(images|javascript|js|css|flash|media|static|htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)/ {
            #过期30天，静态文件不怎么更新，过期可以设大一点，
            #如果频繁更新，则可以设置得小一点。
            #expires 30d;
        #}
		
		#禁止访问 .htxxx 文件
		#location ~ /\.ht {
        #    deny  all;
        #}
		
		#对 "/" 启用反向代理
		#location / {
			#proxy_pass http://127.0.0.1:88;
			#proxy_redirect off;
			#proxy_set_header X-Real-IP $remote_addr;
			
			#后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
			#proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			
			#以下是一些反向代理的配置，可选。
			#proxy_set_header Host $host;
			
			#允许客户端请求的最大单文件字节数
			#client_max_body_size 10m; 
			
			#缓冲区代理缓冲用户端请求的最大字节数
			#client_body_buffer_size 128k; 
			
			#nginx跟后端服务器连接超时时间(代理连接超时)
			#proxy_connect_timeout 90; 
			
			#后端服务器数据回传时间(代理发送超时)
			#proxy_send_timeout 90; 
			
			#连接成功后，后端服务器响应时间(代理接收超时)
			#proxy_read_timeout 90; 
			
			#设置代理服务器（nginx）保存用户头信息的缓冲区大小
			#proxy_buffer_size 4k; 
			
			#proxy_buffers缓冲区，网页平均在32k以下的设置
			#proxy_buffers 4 32k; 
			
			#高负荷下缓冲大小（proxy_buffers*2）
			#proxy_busy_buffers_size 64k; 
			
			#设定缓存文件夹大小，大于这个值，将从upstream服务器传
			#proxy_temp_file_write_size 64k;
		#}
		
		#设定查看Nginx状态的地址
		#location /NginxStatus {
			#stub_status on;
			#access_log on;
			#auth_basic "NginxStatus";
			#auth_basic_user_file conf/htpasswd;
			#htpasswd文件的内容可以用apache提供的htpasswd工具来产生。
		#}
		
		#本地动静分离反向代理配置
		#所有jsp的页面均交由tomcat或resin处理
		#location ~ .(jsp|jspx|do)?$ {
			#proxy_set_header Host $host;
			#proxy_set_header X-Real-IP $remote_addr;
			#proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			#proxy_pass http://127.0.0.1:8080;
		#}
		
    }


    #另一个虚拟主机使用混合的IP的配置
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    #HTTPS server的配置，默认不开启
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```