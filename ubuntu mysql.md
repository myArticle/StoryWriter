---
title: ubuntu 18.04 安装 mysql 服务
tags: mysql,ubuntu
grammar_cjkRuby: true
date: 2018-7-23
---

## 安装

- 安装

	``` dos?linenums
	sudo apt-get install mysql-server mysql-common mysql-client
	```
	
-  `mysqladmin -u root password your-new-password` 期望为 root 用户设置密码成功。

	设置密码失败。
	错误原因  `mysqladmin: connect to server at 'localhost' failed
error: 'Access denied for user 'root'@'localhost''` 。

- 重启

``` dos?linenums
sudo /etc/init.d/mysql restart
```


##  登录 mysql 服务问题

- `mysql -u root -p` 期望正确输入密码连接服务。

	输入密码是否正确，都会连接失败。
	错误原因 `ERROR 1698 (28000): Access denied for user 'root'@'localhost'` 。

	- 解决办法

		>删除 root 用户，重新创建用户。

		- 登录

			``` dos
			sudo mysql
			```

		- 查看当前用户用户

			``` dos?linenums
			select user, host from mysql.user;
			
			+------------------+-----------+
			| user             | host      |
			+------------------+-----------+
			| debian-sys-maint | localhost |
			| mysql.session    | localhost |
			| mysql.sys        | localhost |
			| root             | localhost |
			+------------------+-----------+
			```

		- 删除 'root'@'localhost' 该条记录

			``` dos?linenums
			drop user 'root'@'localhost';
			```

		- 重新创建 root 用户

			``` dos?linenums
			create user 'root'@'%' identified by '666666';
			```

		- 查看当前用户用户

			``` dos?linenums
			select user, host from mysql.user;
			
			+------------------+-----------+
			| user             | host      |
			+------------------+-----------+
			| root             | %         |
			| debian-sys-maint | localhost |
			| mysql.session    | localhost |
			| mysql.sys        | localhost |
			+------------------+-----------+
			```
			
		- 给新建的用户 root 所有权限（重要）

			``` dos?linenums
			grant all privileges on *.* to 'root'@'%' with grant option;
			```

			``` dos?linenums
			flush privileges;
			```
			
		- `sudo mysql` 失效，需要通过 `mysql -u root -p` 正确输入密码后成功连接。

## 修改字符集

- 查看字符集

	``` dos
	sudo mysql
	```
	
	``` sql?linenums
	show variables like 'char%';
	```
	
	``` sql?linenums
	show variables like 'collation%';
	```
	
- 修改字符集

	- 打开文件 `/etc/mysql/my.cnf`
	
	``` dos
	sudo vim /etc/mysql/my.cnf
	```
	
	- 添加以下内容
	
	``` dsconfig?linenums
	[mysqld]
	collation-server = utf8_unicode_ci
	init-connect='SET NAMES utf8'
	character-set-server = utf8
	```
	
	- 重启服务

	``` dos
	sudo service mysql restart
	```


## 卸载

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
dpkg -l | grep ^rc | awk '{print$2}' | sudo xargs dpkg -P
```

- 再次查看依赖项
``` dos
dpkg --list|grep mysql
```

- 卸载（如果还有依赖项）

``` dos
sudo apt-get autoremove --purge mysql-apt-config
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
	
	
## node 连接 mysql 服务

``` javascript?linenums
var mysql = require('mysql');
var connection = mysql.createConnection({
    host: 'localhost',		// 本机
    // host: '192.168.4.43',		// 内网 ip
    // host: 'jia-lei.vicp.io',		//  外网 ip ，这里用到了花生壳的内网穿透
    // port: '52751',		//  本机 和 内网 的端口为默认 3306
    user: 'root',
    password: '666666',
    database: 'mysql'
})

connection.connect(function (err) {
    if (err) {
        console.error('error connecting: ' + err.stack);
        return;
    }
    console.log('connected as id ' + connection.threadId);
});

connection.query('SELECT 1 + 1 AS solution', function (error, results, fields) {
    if (error) throw error;
    console.log('The solution is: ', results[0].solution);
});

connection.end();
```