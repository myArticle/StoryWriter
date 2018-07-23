---
title: ubuntu mysql
tags: mysql,ubuntu
grammar_cjkRuby: true
date: 2018-7-23
---


- 安装

	``` dos?linenums
	sudo apt install mysql-server
	sudo apt install mysql-client
	sudo apt install libmysqlclient-dev
	```

- 查看安装是否成功

	``` dos?linenums
	sudo netstat -tap | grep mysql
	```
	
- 进入 mysql 服务

	``` dos?linenums
	sudo mysql -u root -p <password>
	```
	
	- 设置 mysql 允许远程访问

		- 注释掉 `bind-address = 127.0.0.1`

			``` dos?linenums
			sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
			```
			
		- 进入 mysql 服务，执行授权命令，为用户赋予操作数据库的所有权限

			``` dos?linenums
			grant all on *.* to root@'%' identified by '你的密码' with grant option;
			```
			
		- 使操作生效

			``` dos?linenums
			flush privileges;
			```
			
		- 重启 mysql 服务

			``` dos?linenums
			sudo service mysql restart
			```