---
title: docker 的使用
tags: docker,部署,开发环境
grammar_cjkRuby: true
date: 2018-6-25
---


## 安装 docker

- 更新包管理器

	``` dos?linenums
	sudo apt update
	```
	
- 安装 docker

	```dos?linenums
	sudo apt install docker.io
	```
	
- 查看安装是否成功

	``` dos?linenums
	docker
	```
	
	
## 使用 docker

- 开启 docker 服务

	``` dos?linenums
	sudo systemctl start docker

	or

	sudo service docker start
	```
	
- 查看本地所有镜像

	``` dos?linenums
	sudo docker images
	```
	
- 下载远程镜像

	``` dos?linenums
	sudo docker image pull [image-name]
	```
	
- 运行本地镜像

	``` dos?linenums
	sudo docker container run [image-name]
	```
	
- 查看所有容器（所有运行过的镜像）

	``` dos?linenums
	sudo docker container ls --all
	```
	
- 停止运行镜像

	``` dos?linenums
	sudo docker container kill [container-id]
	```
	
- 删除容器

	``` dos?linenums
	sudo docker container rm [container-id]
	```
	
- 删除镜像

	``` dos?linenums
	sudo docker image rm [image-name]
	```


## 创建自己的 docker 镜像

- 新建文件 app.js

	``` javascript
	console.log("This is a docker image demo.")
	```

- 新建文件 Dockerfile

	``` tex?linenums
	FROM node:9.0
	COPY app.js /usr/src/app-demo/
	WORKDIR /usr/src/app-demo/
	CMD node app.js
	```
	
- build 生成 image 镜像

	``` dos?linenums
	sudo docker image build -t app-demo .
	```
	
- 查看本地镜像

	``` dos?linenums
	sudo docker images
	```
	
- 运行镜像

	``` dos?linenums
	sudo docker run app-demo
	```
	
