---
title: 给Hexo装一个后台吧！hexo-admin使用指南
tags:
  - Hexo
  - Hexo插件
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/dwarf-rabbit-4833046_640.jpg'
categories: Hexo
abbrlink: b2893acd
date: 2023-03-07 14:00:00
---
目前hexo已经发表了几十篇文章，平时记录一些笔记还是非常好使的，感谢[hexo-theme-fluid](https://github.com/fluid-dev/hexo-theme-fluid)提供了这么精美实用的hexo主题

但是在使用过程中还是发现hexo这种静态博客的弊端，就是无后台管理，每次用Typora写完Markdown内容后再执行命令部署还是比较繁琐，如果有一套懒人方案就好了

### 初识hexo-admin

hexo-admin地址：[https://github.com/jaredly/hexo-admin](https://github.com/jaredly/hexo-admin)

#### 安装插件
```js
yarn add hexo-admin
npm install --save hexo-admin

```
Hexo安装教程就此省略，hexo-admin只能在Hexo网站中使用

#### 打开后台

没错！安装完插件后运行`hexo s`，然后访问`http://localhost:4000/admin/`就能直接看到后台地址了
默认打开是不需要设置帐号密码等配置的，为了安全性我们需要在*settings*中配置
![](https://pic.imgdb.cn/item/6406d823f144a01007e42c12.png)

### 配置_config.yml文件
在本地Hexo的根目录找到_config.yml文件

上一步填写帐号密码后，下方会自动生成一些内功供复制

然后粘贴进_config.yml文件保存即可
![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230307142545.png)

图中的deployCommand配置向下面会单独解释

### 自定义帖子元数据

编辑文章时发现原主题顶部的一些配置项未展示，通过后台生成的内容肯定跟主题有所不兼容，正好hexo-admin支持用户自定义配置元数据，将相关的配置项复制过来即可

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230307143034.png)

根据使用的主题要求填写入_config.yml文件

```js
metadata:
  index_img: ''
  abbrlink: ''
```

### 部署

hexo-admin内置`deploy`功能，如果出现`Error: Config value "admin.deployCommand" not found`则需要配置deployCommand

```js
deployCommand: 'hexo-pubish.bat'
```

然后在根目录新建一个bat脚本文件。填入以下内容

```js
hexo g -d
```
再次到`deploy`点击部署就可以正常部署了！