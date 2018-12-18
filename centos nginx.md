---
title: centos nginx
tags: nginx,liunx,web
grammar_cjkRuby: true
date: 2018-12-18
---

## 安装

```cmd?linenums
sudo yum install nginx
```

## 卸载

```cmd?linenums
sudo yum remove nginx
```

## 操作

- 查看进程号

	```cmd?linenums
	ps -ef | grep nginx
	```
	
- 停止 nginx 服务（首次停止需要通过杀死进程的方式）

	```cmd?linenums
	sudo kill [pid]
	```

- 启动

	```cmd?linenums
	sudo nginx 
	```
	
- 重启

	```cmd?linenums
	sudo nginx -s reload
	```
	
- 停止

	```cmd?linenums
	sudo nginx -s stop
	```
	
## 实例

1.用 `chengxuyuan.我爱你` 解析了两个子域名， `www.chengxuyuan.我爱你` 和 `jialei.chengxuyuan.我爱你` 。

2.在目录 `/etc/nginx/conf.d` 下建立了两个配置文件 `www.chengxuyuan.我爱你.conf` 和 `jialei.chengxuyuan.我爱你.conf` 。

3.在 `/home/jialei/` 下建立了两个项目文件夹 `www.chengxuyuan.我爱你` 和 `jialei.chengxuyuan.我爱你` ，分别建立文件 `index.html` ，分别写入 `www` ， `jialei` 。

- www.chengxuyuan.我爱你.conf

	```nginxconf
	server{
		listen       80;
		server_name  www.chengxuyuan.我爱你;
		root         /home/jialei/www.chengxuyuan.我爱你;
		index		index.html;
	}
	```

- jialei.chengxuyuan.我爱你.conf

	```nginxconf
	server{
		listen       80;
		server_name  jialei.chengxuyuan.我爱你;
		root         /home/jialei/jialei.chengxuyuan.我爱你;
		index		index.html;
	}
	```

4.打开浏览器，分别输入 `http://www.chengxuyuan.我爱你/` 和 `http://jialei.chengxuyuan.我爱你/` ，结果访问得到的都是 `nginx` 默认欢迎页，说明配置不成功。

5.回到服务器查看配置， `curl http://localhost -H "Host: www.chengxuyuan.我爱你"
` 和 `curl http://localhost -H "Host: jialei.chengxuyuan.我爱你"
` 得到的都是 `403 Forbidden` 。一般是 `nginx` 没有权限访问 `root` 目录。

6.在目录 `/etc/nginx/` 下，修改 `nginx` 默认文件（即 `nginx.conf`） 的 `user` 的值，改为服务器当前的用户名（我当前的用户名为 `jialei`）或 `root` 。 `curl http://localhost -H "Host: www.chengxuyuan.我爱你"` 得到 `www` ， `curl http://localhost -H "Host: jialei.chengxuyuan.我爱你"` 得到 `jialei` 。

7.打开浏览器，分别输入 `http://www.chengxuyuan.我爱你/` 和 `http://jialei.chengxuyuan.我爱你/` ，结果访问得到的还是是 `nginx` 默认欢迎页，说明配置还是不成功。

8.打开浏览器的控制台，切换到 `NetWork`下，刷新页面，查看所有的请求中有一项为 `jialei.chengxuyuan.xn--6qq986b3xl` ，查看该项，发现其 `host` 为 `jialei.chengxuyuan.xn--6qq986b3xl` 。

9.尝试将配置文件 `www.chengxuyuan.我爱你.conf` 和 `jialei.chengxuyuan.我爱你.conf` 中的 `server_name` 分别替换为 `www.chengxuyuan.xn--6qq986b3xl` ， `jialei.chengxuyuan.xn--6qq986b3xl` 。重启 `nginx` 服务，打开浏览器再次查看，得到了期望的页面内容 `www` 和 `jialei` 。

>总结
>在配置 `nginx` 过程中遇到了两个问题：
>其一：`403 Forbidden` 问题，一般是 `nginx` 没有足够的操作权限，修改默认配置中的 `user`项即可。
>其二：中文域名绑定不成功问题，应该说是 `nginx` 配置项 `server_name` 与浏览器的请求头中的 `host` 不一致问题，导致绑定域名不成功，该 `server` 配置无效，如果有 `server` 的 `listen  80 default_server` 设置了默认服务，访问该无效的 `server` 就会得到默认 `server` 的访问页；如果没有 `server` 设置了 `default_server` ，默认取目录 `/etc/nginx/conf.d/` 下 `ls` 操作下的第一个文件为无效 `server` 访问页。