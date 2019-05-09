---
title: nginx 配置 http 跳转 https 
tags: nginx,http,https
grammar_cjkRuby: true
date: 2019-4-26
---


```nginx?linenums
server{
	listen       80;
        server_name  jialei.chengxuyuan.xn--6qq986b3xl;
	rewrite ^(.*) https://jialei.chengxuyuan.xn--6qq986b3xl permanent;
}
 server {
 	   listen 443;

 	   server_name jialei.chengxuyuan.xn--6qq986b3xl;

 		ssl on;

 	   root /home/jialei/jialei.chengxuyuan.我爱你;
 	   index index.html;

 		ssl_certificate  /etc/nginx/ssl/jialei.chengxuyuan.xn--6qq986b3xl/1508360_jialei.chengxuyuan.xn--6qq986b3xl.pem;
 		ssl_certificate_key  /etc/nginx/ssl/jialei.chengxuyuan.xn--6qq986b3xl/1508360_jialei.chengxuyuan.xn--6qq986b3xl.key;
 		ssl_session_timeout 5m;
 		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
 		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
 		ssl_prefer_server_ciphers on;

 	   location / {
 			   try_files $uri $uri/ =404;
 	   }
 }
```