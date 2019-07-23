---
title: nginx
tags: nginx,ip 映射,域名
grammar_cjkRuby: true
---


## nginx ip 映射

### 目的

>本地浏览器通过 `http://localhost/` 访问 `nginx` 配置静态页，现在要将 `localhost` 替换为任意域名访问，即 `http://www.test.com/` 访问。

### 步骤

#### windows

1. 确保正确安装 `nginx`，并且 `http://localhost/` 可以正常访问
2. 打开文件夹 `C:\Windows\System32\drivers\etc`，找到 `hosts` 文件
3. 将 `hosts` 文件复制到某一文件夹下
4. 打开复制的 `hosts` 文件，在文件末尾追加 `127.0.0.1	www.test.com`
5. 保存修改，将修改后的文件替换文件夹 `C:\Windows\System32\drivers\etc` 下的 `hosts` 文件
6. 访问 `http://www.test.com/`，查看

## nginx 配置

### 目的

>方便管理 nginx。

### 步骤

#### windows

1. 在 `nginx` 安装目录下找到 `conf/` 目录，并在 `conf/` 目录下新建目录 `my-conf` ，用来管理自己的 `nginx` 配置文件
2. 在 `conf/my-conf/` 目录下新建配置文件 `test.conf` ，写入

	``` nginxconf
	server {
			listen       8001;
			server_name  localhost;
		 location / {
				root   html;
				index  index.html index.htm;
			}
	}
	```
	
3. 保存 `test.conf` 文件后，打开 `conf/nginx.conf` 文件，添加引入 `my-conf/` 目录下的所有 `.conf` 文件

	```nginxconf
	...
	http {
		...
		include	my-conf/*.conf;
		...
	}
	...
	```
	
4. 保存修改后，重启 `nginx` 服务，查看 `http://localhost:8001/` 是否正常