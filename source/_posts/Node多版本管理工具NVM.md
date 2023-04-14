---
title: Node多版本管理工具NVM
tags:
  - Node
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/girl-6738285_640.jpg
categories: 操作配置
abbrlink: 3b769c6a
date: 2022-09-05 09:32:21
---

 接触不同的项目，往往最头疼的就是各种Node版本的切换了，卸载安装再卸载反反复复。NVM的出现就大大的解决此类问题，同时可以安装多个Node版本，一行命令就能随意切换

### 下载安装

Windows直接点击下面链接现在exe文件，安装前最好选择自定义安装目录，后续需要配置文件用

https://github.com/coreybutler/nvm-windows/releases

MacOS 则是使用指令进行安装，打开「终端机」或自行安装的「iTerm」等工具，输入以下指令即可开始安装

```js
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

安装完毕后，终端输入PowerShell或者CMD输入`nvm -v`能查到版本号就代表安装成功

### 使用

```js
nvm install <version> ## 安装指定版本，可模糊安装，如：安装v4.4.0，既可nvm install v4.4.0，又可nvm install 4.4
nvm uninstall <version> ## 删除已安装的指定版本，语法与install类似
nvm use <version> ## 切换使用指定的版本node
nvm ls ## 列出所有安装的版本
nvm ls-remote ## 列出所以远程服务器的版本（官方node version list）
nvm current ## 显示当前的版本
nvm alias <name> <version> ## 给不同的版本号添加别名
nvm unalias <name> ## 删除已定义的别名
nvm reinstall-packages <version> ## 在当前版本node环境下，重新全局安装指定版本号的npm包
```

### 踩坑记录

#### 切换时乱码

PowerShell或者CMD以管理员权限打开即可

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20220905100348.png)

#### 安装Node指定版本报错

一般是连不上服务造成的，国内在正常不过了

找到NVM的安装目录，找到`settings.txt`打开，复制下方两行代码粘贴保存

```js
node_mirror:npmmirror.com/mirrors/node/
npm_mirror:npmmirror.com/mirrors/npm/
```

设置成淘宝镜像后就完美解决了

淘宝镜像最近换了域名，之前的npm.taobao.org最好是替换为npmmirror.com