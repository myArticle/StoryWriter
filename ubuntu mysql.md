---
title: ubuntu mysql
tags: mysql,ubuntu
grammar_cjkRuby: true
date: 2018-7-23
---

## 安装

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
	
- 卸载

	- 查看依赖项

	``` dos
	dpkg --list|grep mysql
	```
	
	- 卸载
	
	``` dos
	sudo apt-get remove mysql-common
	```
	
	- 卸载

	``` dos
	sudo apt-get autoremove --purge mysql-server-5.7
	```

	- 卸载

	``` dos
	dpkg -l|grep ^rc|awk '{print$2}'|sudo xargs dpkg -P
	```

	- 再次查看依赖项
	``` dos
	dpkg --list|grep mysql
	```
	
	- 卸载（如果还有依赖项）

	``` dos
	sudo apt-get autoremove --purge mysql-apt-config
	```

- 进入 mysql 服务

	``` dos?linenums
	sudo mysql -u root -p <password>
	```
	
	- 删除匿名用户

		- 选择数据库 mysql

		``` dos
		use mysql;
		```
		- 删除匿名用户

		``` dos
		delete from user where user='';
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
				
			>如果需要指定访问主机，可以把 `%` 替换为主机的IP或者主机名。另外，这种方法会在数据库 mysql 的数据表 user 中，增加一条记录。如果不想增加记录，只是想把某个已存在的用户（例如 root ）修改成允许远程主机访问，则可以使用如下 SQL 来完成：

			``` dos
			update user set host='%' where user='root' and host='localhost';
			```
			
		- 使操作生效

			``` dos?linenums
			flush privileges;
			```
			
		- 重启 mysql 服务

			``` dos?linenums
			sudo service mysql restart
			```
			
## 操作

- 查看当前数据库

	``` dos
	show databases;
	```
	
- 创建数据库

	``` dos
	create database <database_name>;
	```
	
- 删除数据库

	``` dos
	drop database <database_name>;
	```
	
- 选择数据库

	``` dos
	use <database_name>;
	```
	
- 查看当前数据库的数据表

	``` dos
	show tables;
	```
	
- 创建数据表

	``` dos
	create table <table_name> (<column_name> <column_type>) engine=innodb default charset=utf8;
	```

- 删除数据表

	``` dos
	drop table <table_name>;
	```
	
- 查看数据表的表结构

	``` dos
	desc <table_name>;
	```
	
- 数据表插入数据

	``` dos
	insert into <table_name> (field1, field2, field3, field4) values (value1, value2, value3, value4), (value1, value2, value3, value4), (value1, value2, value3, value4);
	```
	
- 读取数据表所有数据

	 ``` dos
	 select * from <table_name>;
	 ```
	 
- 按条件查询（分页）数据表数据

	``` dos
	select (field1, field2) from <table_name> [where <query_terms>] [limit <page-size>] [offset <page-number>];
	```
	
- 修改数据表数据

	``` dos
	update <table_name> set field1 = value1, field = value2 [where <query_terms>];
	```
	
- 删除数据表数据

	``` dos
	delete from <table_name> [where <query_terms>];
	```
	
- 清空数据表数据

	``` dos
	delete from <table_name>;
	```