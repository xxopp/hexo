---
title: 安装Hexo-abbrlink生成唯一永久文章链接
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119dd.jpg
categories: Hexo
abbrlink: f92fa3a3
date: 2020-11-20 17:24:58
tags: [Hexo, Hexo插件]
---

Hexo已经使用有几天了给我的感觉就是非常轻巧快速，用来做个人博客算是最佳选择了，托管在各大Git平台基本上也不需要自己买服务器了，如果有幸看到这篇文章并且想写日志写笔记的话，我还是非常建议自己搭建一个Hexo博客的，毕竟不需要成本还能学到一些东西。

Hexo的文章链接默认都是时间加标题拼成的，复制文章链接出来给给人看时就显得又臭又长非常不美观，作为强迫症的我还是决定要彻底解决这个缺陷。于是乎网上搜了一圈一款`Hexo-abbrlink`的插件还是非常不错的，生成的文章url链接能永久生效，即使日后换电脑博客搬家都不会有变动。

## 安装

```bash
npm install hexo-abbrlink --save
```

## 配置

安装成功后就需要找到Hexo的配置文件了，注意是根目录的`_config.yml`文件而不是主题里面的配置文件

找到下图中标示的范围

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201120173353.png)

修改的相关代码如下

```bash
permalink: posts/:abbrlink.html  # 此处可以自己设置，也可以直接使用 :/abbrlink
abbrlink:
    alg: crc32   #算法： crc16(default) and crc32
    rep: hex     #进制： dec(default) and hex
```

个人更倾向于改成`:abbrlink.html`，因为玩过WordPress的都只要配置伪静态就是将url后面接上`html`对搜索引擎收录有些许好处，当然了配置成什么样完全靠个人喜好

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201120173657.png)

大功告成！