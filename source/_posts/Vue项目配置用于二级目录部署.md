---
title: Vue项目配置用于二级目录部署
tags:
  - Vue
  - 开发环境
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/baby-623417_640.jpg'
categories: Web前端
abbrlink: 79ac99c1
date: 2023-02-28 15:30:00
---
 ### 前言

公司项目在进入预生产阶段时，需要部署到一个二级目录中去，nginx那边运维已经配置好了，现在压力给到了前端这边

翻看了网上一些教程发现还是挺简单的

### 教程

#### 修改配置文件

打开`vue.config.js`找到`publicPath`和 `outputDir` 都改为 '/子目录名/'

如果开发环境不想开启，可以参考下方注释的一行

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228152818.png)

#### 配置入口文件

指定位置添加`<meta base="/backend/">`

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228153016.png)

#### 配置路由

vue-router 路由文件的配置，填写二级目录

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228153143.png)

### 运行项目

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228153234.png)