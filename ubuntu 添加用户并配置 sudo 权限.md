---
title: ubuntu 添加用户并配置 sudo 权限
tags: ubuntu,用户,sudo
grammar_cjkRuby: true
date: 2018-6-20
---


## 添加用户

- useradd -r -m -s /bin/bash jialei

- passwd jialei  （设置新创建的用户的密码两次）


## 给新用户添加 `root` 权限

- chmod +w /etc/sudoers

- vim /etc/sudoers

	``` dos?linenums
	# User privilege specification
	root    ALL=(ALL:ALL) ALL
	jialei  ALL=(ALL:ALL) ALL
	```