---
title: 盘点一些公共的前端CDN静态资源库
tags:
  - CDN
  - 静态资源
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/7333557_640.jpg
categories: Web前端
abbrlink: 9a073722
date: 2022-08-17 13:24:30
---

##### CDNJS：

国内测试节点扫了一下全绿，速度还行，由cloudflare提供服务，非常稳定的一个静态资源加速服务

https://cdnjs.com/

##### jsDelivr：

国内节点被ban，无代理情况下访问速度不佳，不过支持Github个人仓库加速，因此不少人拿来当图床

cdn子域名部分地区被墙，目前jsdelivr可用子域名有：fastly*，gcore*

https://gcore.jsdelivr.net/gh/yeyulingfeng01/yuedu.github.io@latest/hexo/20201119bb.jpg

https://cdn.jsdelivr.net/gh/yeyulingfeng01/yuedu.github.io@latest/hexo/20201119bb.jpg

官网：

https://www.jsdelivr.com/

##### loli：

国内个人搭建的服务，目前口碑不错，稳定多年了，博主部分站点就在使用

https://u.sb/css-cdn/

##### UNPKG：

资源绝对丰富，同步npmjs.com。但是速度不行，可惜。同时不支持检索，当然检索可以用https://www.npmjs.com/ 搜索替代。

https://unpkg.com/

##### BootCDN：

不稳定不建议经常换域名以及改动，**非常不推荐**

https://www.bootcdn.cn/

##### 七牛云存储开放静态文件CDN：

https://www.staticfile.org/

##### 360前端资源库：

https://cdn.baomitu.com/

##### 字节资源库：

版本更新过慢，其他还好

https://cdn.bytedance.com/

##### 补充：

个人练手项目无所谓，如果是企业项目尽量避免使用这些公共服务，目前尚未发现一款国内访问速度理想且稳定的，丢到OSS效果也是一样