---
title: git 命令
tags: git,版本控制,工具
grammar_cjkRuby: true
time: 2018-5-22
author: jialei
---


---
# 首次上传现有文件

- 进入要上传的文件夹
	
``` dos?linenums
cd <my-dirname>
```
	
- 初始化，新建一个 git 仓库

``` dos?linenums
git init
```
	
- 将文件从工作区添加到暂存区

``` dos?linenums
git add .
```
	
- 提交，将文件从暂存区提交到本地仓库

``` dos?linenums
git commit -m "first commit"
```

- 增加一个新的远程仓库 origin

``` dos?linenums
git remote add origin <url>
```
	
- 将本地仓库推送到远程仓库 origin 的 master 分支上

``` dos?linenums
git push -u origin master
```
	
	
---
# 文件存放区

## 术语

en | zh
-- | -
workspace | 工作区
index / stage | 暂存区
repository | 本地仓库
remote | 远程仓库


## 简单示意图

![img](./images/1527048211775.jpg)


---
# 分支

- 查看所有本地分支

``` dos?linenums
git branch
```

- 新建一个分支

``` dos?linenums
git branch [branch-name]
```

- 切换分支，工作区也会更新

``` dos?linenums
git checkout [branch-name]
```

- 分支内操作

``` dos?linenums
git status
git add .
git status
git commit -m "edit this branch files"
git pull origin master
git push --set-upstream origin [current-branch-name]
```

- 切到主分支，合并分支

``` dos?linenums
git checkout master
git merge [branch-name]
git pull
git push
```


---
# 标签

- 查看所有 tag

``` dos?linenums
git tag
```

- 将文件从工作区添加到暂存区

``` dos?linenums
git add .
```

- 在 commit 前（后）打 tag ， 本次 tag 不包含（包含）本次 commit 的文件

``` dos?linenums
git tag [tag-name]
```

- 更新工作区代码

``` dos?linenums
git pull
```

- 提交本次 tag

``` dos?linenums
git push origin [tag-name]
```

- 将本次修改文件从本地仓库提交到远程仓库

``` dos?linenums
git push
```