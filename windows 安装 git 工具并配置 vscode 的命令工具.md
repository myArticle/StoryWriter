---
title: windows 安装 git 工具并配置 vscode 的命令工具
tags: windows,git,vscode,shell,linux,命令行
grammar_cjkRuby: true
---


## 安装 git 工具

1. 下载 https://git-scm.com/
2. 安装，自定义目录安装，注意目录不要有中文字符， `D:\software\install`


## 安装 vscode

1. 下载 https://code.visualstudio.com/
2. 安装，自定义目录安装，`D:\software\install`
3. 打开 `vscode`，打开终端工具，选择默认终端 `select default shell`

![select default shell](./images/1563417326691.png)

4. 因为自定义安装 `git` 终端工具，所以默认 `C盘` 没有
5. 打开设置 `settings`，修改默认脚本终端安装地址 `D:\software\install\Git\git-bash.exe`

![修改默认脚本地址](./images/1563417613445.png)

6. 关闭 `vscode`，重新打开，查看是否配置成功