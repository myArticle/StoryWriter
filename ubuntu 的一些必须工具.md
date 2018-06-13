---
title: ubuntu 的一些必须工具
tags: 上网,markdown,微信开发者工具
grammar_cjkRuby: true
date: 2018-6-13
---


---
# 科学上网，安装 shadowsocks

**前提：有运行 shadowsocks 服务的服务器**

**注意，设置完浏览器代理后，浏览器才可以科学上网**


---
## 安装 pip

``` dos?linenums
sudo apt-get update
sudo apt-get install python-pip
sudo apt-get install python-setuptools m2crypto
```


---
## 安装 shadowsocks

``` dos?linenums
pip install shadowsocks
```

或 ubuntu16.04 可以这样安装（apt 是 apt-get 的一项改进）

``` dos?linenums
sudo apt-get install shadowsocks
```

安装成功后会有 sslocal 命令


---
## 安装 vim

``` dos?linenums
sudo apt install vim
sudo apt install vim-gtk3
sudo apt install vim-tiny
sudo apt install neovim
sudo apt install vim-athena
sudo apt install vim-gtk
sudo apt install vim-nox
```


---
## 安装 google chrome 浏览器

	``` dos?linenums
	sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/


	wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -

	sudo apt-get update

	sudo apt-get install google-chrome-stable

	google-chrome
	
	```


---
## 配置 shadowsocks.json 文件

可以在 /home/jialei/ 下新建文件 shadowsocks.json

``` json
{
    "server": "[运行 shadowsocks 服务的服务器外网 IP]",
    "server_port": [运行 shadowsocks 服务的服务器端口],
    "local_port": [本地端口],
    "password": "[服务器 shadowsocks 服务的密码]",
    "timeout": 600,
    "method": "aes-256-cfb"
}
```


---
## 启动 shadowsocks

``` dos?linenums
sslocal -c /home/jialei/shadowsocks.json
```


---
## 配置浏览器

- 给浏览器安装代理插件 SwitchyOmega ，下载下面的这两个文件，分别拖到火狐和谷歌的浏览器扩展安装。

	- proxy_switchyomega-baidubaidu2.5.11-an.fx.xpi 火狐插件

	- SwitchyOmega_Chromium.crx 谷歌插件


---
## 新建情景模式 —— shadowsocks

- 代理协议： SOCKS5

- 代理服务器： 127.0.0.1 （就是本机）

- 代理端口： 1080


---
## 情景模式 shadowsocks

- 配置情景模式 —— auto switch

- 切换规则 => 情景模式 —— shadowsocks

- 切换规则 => 默认情景模式 => 情景模式 —— 直接连接

- 规则列表设置 => 规则列表网址 ——https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt


---
## 情景模式 auto switch

- 注意，始终使用 auto switch 。如果去访问其他网站（如 twitter.com ），结果无法访问，可以选择 添加条件 添加一条匹配该网站的规则，就可以正常访问了。


---
## 开机自启 shadowsocks 服务

- 安装 supervisor 来管理 sslocal 启动

	``` dos?linenums
	sudo apt-get install supervisor
	```

- 安装成功后，在 /etc/supervisor/conf.d/ 下新建 shadowsocks.conf 文件

	``` dos?linenums
	sudo vim /etc/supervisor/conf.d/shadowsocks.conf
	```

- 在 shadowsocks.conf 文件中写入下面内容，其中， command=[启动 shadowsocks 服务的命令]（即 sslocal -c /home/jialei/shadowsocks.json）

	``` json
	[program:shadowsocks]
	command=sslocal -c /home/jialei/shadowsocks.json
	autostart=true
	autorestart=true
	user=root
	log_stderr=true
	logfile=/var/log/shadowsocks.log
	```

- sslocal 和 ssserver 命令在 /usr/bin/ 下，复制一份到 /bin/ 目录下

	``` dos?linenums
	sudo cp /usr/bin/sslocal /bin
	```

- 现在可以关闭之前运行的 sslocal -c /home/jialei/shadowsocks.json 命令的终端

- 通过 supervisor 去运行 shadowsocks 服务

	``` dos?linenums
	sudo service supervisor restart
	```

- 通过 supervisor 去停止 shadowsocks 服务

	``` dos?linenums
	sudo service supervisor stop
	```

- 在 /etc/ 下新建文件 rc.local 文件

	``` dos?linenums
	sudo vim /etc/rc.local
	```

- 在 /etc/rc.local 文件中写入下面内容

	``` dos?linenums
	service supervisor start
	```

- 关机重启，直接打开浏览器查看是否成功。


---
## 安装 node

- 安装 nvm

	``` dos?linenums
	curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.4/install.sh | bash
	```

- 安装 node

	``` dos?linenums
	nvm install v9.0.0
	```

- 其他 nvm 命令
	- 列出远程服务器上 node 的所有版本

		``` dos?linenums
		nvm ls-remote
		```

	- 安装某一版本的 node

		``` dos?linenums
		nvm install v10.0.0
		```

	- 列出本地安装的所有 node 版本

		``` dos?linenums
		nvm list
		```

	- 本地 node 版本的切换

		``` dos?linenums
		nvm use v9.0.0
		```


---
## 安装微信开发者工具（cytle/wechat_web_devtools）

- 安装 Wine（Ubuntu - WineHQ Wiki）

	- 如果你的系统是 64 位，则启用 32 位

		``` dos?linenums
		sudo dpkg --add-architecture i386
		```

	- 添加存储库

		``` dos?linenums
		wget -nc https://dl.winehq.org/wine-builds/Release.key
		sudo apt-key add Release.key
		sudo apt-add-repository'deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main'
		```

	- 更新软件包

		``` dos?linenums
		sudo apt-get update
		```

	- 安装稳定的版本

		``` dos?linenums
		sudo apt-get install --install-recommends winehq-stable
		```

- 下载并安装微信开发者工具

	- 下载项目

		``` dos?linenums
		git clone https://github.com/cytle/wechat_web_devtools.git
		```

	- 进入项目

		``` dos?linenums
		cd wechat_web_devtools
		```

	- 自动下载最新 nw.js ，同时部署目录 ~/.config/微信web开发者工具/

		``` dos?linenums
		./bin/wxdt install
		```

	- 运行，在项目根目录下运行

		``` dos?linenums
		./bin/wxdt
		```


---
## 生成桌面快捷方式

- 在 /usr/share/applications/ 新建文件 WeChat-developer-tools.desktop

- 在 WeChat-developer-tools.desktop 文件内写入以下内容

	``` dos?linenums
	[Desktop Entry]
	Encoding=UTF-8
	Name=WeChat developer tools
	Comment=微信开发者工具
	Exec=/home/jialei/wechat_web_devtools/bin/wxdt
	Icon=/home/jialei/wechat_web_devtools/WeChat-developer-tools.png
	Terminal=false
	StartupNotify=true
	Type=Application
	Categories=Application;Development;
	```

- 对该文件进行赋权

	``` dos?linenums
	sudo chmod u+x /usr/share/applications/WeChat-developer-tools.desktop
	```

- 在文件系统 /usr/share/applications/ 下找到图标 WeChat-developer-tools ，将它移动到桌面


---
## 安装小书匠 markdown 笔记（小书匠编辑器）

- 下载 Story-writer-linux64.zip 安装包

- 解压下载下来的 zip 文件到 /opt/Story-Writer/

	``` dos?linenums
	sudo unzip Story-writer-linux64.zip -d /opt/Story-Writer/
	```

- 启动小书匠

	``` dos?linenums
	cd /opt/Story-Writer/
	./Story-Writer
	```

- 创建小书匠桌面快捷方式

	- 在 /usr/share/applications/ 目录下新建文件 Story-Writer.desktop

		``` dos?linenums
		sudo vim /usr/share/applications/Story-Writer.desktop
		```

	- 在 Story-Writer.desktop 文件内输入以下内容

		``` dos?linenums
		[Desktop Entry]
		Encoding=UTF-8
		Name=Story-Writer
		Comment=小书匠 markdown笔记
		Exec=/opt/Story-Writer/Story-writer
		Icon=/opt/Story-Writer/Story-writer.png
		Terminal=false
		StartupNotify=true
		Type=Application
		Categories=Application;Development;
		```

	- 对该文件进行赋权

		``` dos?linenums
		sudo chmod u+x /usr/share/applications/Story-Writer.desktop
		```

	- 在文件系统 /usr/share/applications/ 下找到图标 Story-Writer ，将它移动到桌面

