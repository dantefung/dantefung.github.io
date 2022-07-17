---
title: Docker安装Nginx
date: 2022-07-17 18:21:01
tags:
 - 运维
categries:
 - 运维
---

# Docker安装Nginx

## 数据卷规划

{% asset_img 2022-07-17-18-30-55.png %}

## 安装命令

```
docker run -d -p 80:80 --name fhltestnginx -v ~/Documents/software/docker_volume_mapping/nginx/www:/opt/www -v ~/Documents/software/docker_volume_mapping/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/Documents/software/docker_volume_mapping/nginx/conf/conf.d:/etc/nginx/conf.d -v ~/Documents/software/docker_volume_mapping/nginx/logs:/var/log/nginx  nginx:latest
```
## Nginx.conf

```
user  root;
worker_processes  1;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    keepalive_timeout  120;
    client_max_body_size 20m;
    #gzip  on;


    server {
        listen       80;
        server_name  shop.foch.tk;
        #root /usr/share/nginx/html;
	#root /opt/www/redis_geektime/html;
        index index.php index.html index.htm;

        location / {
            root /opt/www/;
        }

#        if (!-e $request_filename) {
#            root /opt/www/SpringSeries;
#        }


        location /redis {
            alias /opt/www/redis_geektime/html;
            #指定主页
            index index.html;
            #自动跳转
            autoindex on;
        }

        location /interview {
            alias /opt/www/interview/;
             #指定主页
            index index.html;
            #自动跳转
            autoindex on;
        }

        location /Spring  {
            alias /opt/www/Spring;
            #指定主页
            index index.html;
            #自动跳转
            autoindex on;
        }

        location /Netty  {
              alias /opt/www/Netty;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /StudyGuide  {
              alias /opt/www/StudyGuide;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /Dubbo  {
              alias /opt/www/Dubbo;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /SpringMVC  {
              alias /opt/www/SpringMVC;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }


        location /SpringBoot  {
              alias /opt/www/SpringBoot;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /mybatis  {
              alias /opt/www/mybatis;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /JDK  {
              alias /opt/www/JDK;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /RedisSource  {
              alias /opt/www/RedisSource;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /DatabaseDesign  {
              alias /opt/www/DatabaseDesign;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        location /SpringSeries{
              alias /opt/www/SpringSeries;
              #指定主页
              index index.html;
              #自动跳转
              autoindex on;
        }

        # 该配置能成功访问到静态pdf资源
        location /pdfjs {
             alias /opt/www/static/pdfjs;
        }


       # location /pdfjs {
       #      alias /opt/www/SpringSeries;
       # }


    	location ~* \.(gif|jpg|jpeg|bmp|png|ico|txt|js|css)$ {
    	    root /opt/www/static;
    	    expires  30d;
    	}

    }
}

```