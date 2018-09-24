# [ubuntu install nvm](https://github.com/creationix/nvm)

>curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.4/install.sh | bash

## 注意

安装失败，参考[nvm Installation](https://github.com/creationix/nvm)
>export NVM_DIR="${XDG_CONFIG_HOME/:-$HOME/.}nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

>nvm install v9.0.0

## 其他 nvm 命令

### 列出远程服务器上 node 的所有版本

>nvm ls-remote

### 安装某一版本的 node

>nvm install v10.0.0

### 列出本地安装的所有 node 版本

>nvm list

### 本地 node 版本的切换

nvm use v9.0.0

