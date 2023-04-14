---
title: 解决Git拉取代码报错Host key verification failed.
tags:
  - Git
  - 开发环境
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2023/australia-1973524_640.jpg
categories: 操作配置
abbrlink: 84d65c6e
date: 2023-04-14 16:49:00
---

 Github日常就放点Hexo的数据，最近pull代码发现直接报错无法正常使用，重新配置了SSH keys也无济于事，一番折腾发现不是Git和Github的问题

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230414164541.png)

报错代码如下

```js
$ git pull
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:uNiVe8BXVUpKZztksCsDujc0u9IDTMczQhcCvj3tD2s.
Please contact your system administrator.
Add correct host key in /c/Users/DELL/.ssh/known_hosts to get rid of this message.
Offending RSA key in /c/Users/DELL/.ssh/known_hosts:1
RSA host key for github.com has changed and you have requested strict checking.
Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

```

网上找了一番，发现这个报错主要是因为远程主机的ssh公钥发生了变化，两边不一致导致的

删除本地对应ip的在known_hosts相关信息，具体路径如下

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230414164313.png)

删除后重新pull会提示`Are you sure you want to continue connecting (yes/no)?`输入yes即可

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230414164840.png)

到此问题得以解决！从网上找到一些才解惑，不然不明不白下次别人遇到都不好解释了

拉取代码都是通过SSH连接GitHub仓库，不然也就不会配置SSH keys了，ssh会把你每个你访问过计算机的公钥(public key)都记录在~/.ssh/known_hosts。当下次访问相同计算机时，SSH会核对公钥。如果公钥不同，SSH连接时会发出警告，避免你受到DNS Hijack之类的攻击





