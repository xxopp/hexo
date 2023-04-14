---
title: Powershell中禁止执行脚本解决办法
tags:
  - Windows
  - 开发环境
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/0305178398.jpg'
categories: 操作配置
abbrlink: 1f6b68f8
date: 2021-08-30 10:16:12
---

公司电脑通过Powershell执行脚本的时候遇到如下报错：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20210707143115.png)

### 解决办法

首次在计算机上启动 Windows PowerShell 时，现用执行策略很可能是 Restricted（默认设置）,Restricted 策略不允许任何脚本运行。

 若要了解计算机上的现用执行策略，输入： 

```
get-executionpolicy 
```

一共有 6 种策略，分别是：Undefined, Bypass, Unrestricted, Remotesigned, Allsigned, Restricted。默认情况下是 Restricted 策略

若要在本地计算机上运行您编写的未签名脚本和来自其他用户的签名脚本，请使用以下命令将计算机上的执行策略更改为RemoteSigned： 命令提示符下执行： 

```
set-ExecutionPolicy RemoteSigned
```

这里需要注意的一点是，需要以管理员权限打开Windows PowerShell 再执行上述代码才能生效

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20210707144007.png)

### 拓展补充

六种策略的区别：

- Restricted.
  不读取任何配置文件、不运行任何脚本，这个是默认策略。
-  AllSigned.
  所有的脚本和配置文件必须有受信任的的发布者的签名，就算是自己写的脚本也同样如此，否则无法执行。
- RemoteSigned.
  和上面的类似，但是针对的是从网上下载下来的脚本，这些脚本同样也需要可信的签名。
- Unrestricted.
  可以运行脚本或者读取配置文件，如果执行的是从网上下载的脚本，那么会有一个申请权限的提示。
-  Bypass.
  不阻止任何脚本或配置文件，也不会显示警告或者提示。
-  Undefined.
  把当前 scope 的所有策略全部都删除，但是不会删除 Group Policy scope 中的策略。如果你想删除某个设置好的策略，用这个就行了。
