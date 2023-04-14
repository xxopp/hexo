---
title: git使用知识整理笔记
tags:
  - 小程序
  - uniapp
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/prairie-7362991_640.jpg
categories: 操作配置
abbrlink: f9e9e6ba
date: 2023-02-27 09:17:53
---

先安装git工具

官网： [https://git-scm.com/download](https://git-scm.com/download)

为了标识身份，建议先完成 Git 全局设置

```js
git config --global user.name "you name" 
git config --global user.email "you email"
```

在生成新的 SSH 密钥前，请先确认是否需要使用本地已生成的SSH密钥，SSH 密钥对一般存放在本地用户的根目录下。

```js
cat ~/.ssh/id_rsa.pub
```

如果先前没生成则需要手动生成密钥

```js
ssh-keygen -t rsa -C "you email"
```

命令执行后使劲回车，在指定的保存路径下会生成2个名为id_rsa和id_rsa.pub的文件：

测试 SSH 是否连接成功

```js
# 这里以 Github 服务为例
ssh -T git@github.com
```

### git命令使用

克隆仓库

```js
git clone https://codeup.aliyun.com/6122872703f23adfbed1158b/mini/yeyutool.git
cd yeyutool
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

已有文件夹或仓库

```js
cd existing_folder
git init
git remote add origin https://codeup.aliyun.com/6122872703f23adfbed1158b/mini/yeyutool.git
git add .
git commit
git push -u origin master
```

切换到 master 分支

```js
git checkout master
```

查看本地仓库对应的远程仓库地址

```js
git remote -v
```

将本地分支和远程某分支建立对应（如远程的 master 分支）

```js
git branch -u origin/master
```

和远程分支同步

```js
git pull
```

创建分支

```js
git checkout -b <branch>
```

查看分支

```js
git branch
```

切换一个本地已经存在的分支

```js
git checkout <branch>
```

查看当前代码库的文件修改状态列表

了解发生了什么以及文件变更的状态很重要。

```js
git status
```

将本地提交推送至远端

```js
git push
```

合并分支

```js
// 假设有主分支master和需要汇入合并的分支dev

// 切换到master分支
git checkout master
// 更新master代码到最新
git pull

// 把dev（你要合并到master的基分支）分支的代码合并到master上
git merge dev

// 查看状态
git status
```

版本回退

```js
// 回退上个版本：
git reset --hard HEAD^
// 回退上上个版本：
git reset --hard HEAD^^

// reset
// 使用reset命令，Git会把要回退版本之后提交的修改都删除掉。要从第四次修改回退到第一次修改，
// 那么会删除第二、三、四次的修改。【注：这里并不是真正的物理删除】
// 使用git reflog show命令,可以查看完整的提交历史
// 查看分支提交历史，确认需要回退的版本
git log
// 进行版本回退
git reset --hard commit_id
// 推送至远程分支
git push

// revert
// 回退并保留当前修改
git revert -n commit_id
git commit -m "恢复上一次修改"
```