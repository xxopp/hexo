---
title: Nodejs常用包管理操作以及修改缓存路径
tags:
  - npm
  - Node
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/adult-1807500_640.jpg
categories: 操作配置
abbrlink: 4288b4e5
date: 2023-02-28 15:01:00
---

主流包管理工具有npm、yarn、cnpm、pnpm，其中电脑端安装Node后会默认支持npm环境，控制台输入`npm -v`就能看到了

> 推荐使用nvm管理项目中Node版本切换的问题，详情可查看之前文章 [Node多版本管理工具NVM](https://laoyee.meijiecao.top/posts/3b769c6a.html)

项目安装的依赖库都会在`package.json`文件中列出，执行安装依赖命令后就会下载所对应版本的文件到`node_modules`目录

```js
{
	"lodash": "^4.17.4",
    "@vue/cli-plugin-eslint": "~5.0.0",
    "core-js": "3.8.3"
}
```

### package.json拓展

**符号`^`：锁定主版本，可更新次版本号、修正版本号和先行版本号**

例如`"vue-class-component": "^6.0.0"`，安装依赖时，可以安装符合`6.*.*` 的任意版本，只要主版本号是6即可。

**符号`~`：锁定主版本号和次版本号，可更新修正版本号和先行版本号**

例如`"vue": "~2.5.22"`，安装依赖时，可以安装符合`2.5.*`的任意版本。

**`空符号`：锁定所有版本号**

例如`"vue-router": "3.0.1"`，只能安装版本为`3.0.1`的依赖包。

**`符号\*`：定义某个版本号范围**

例如`vue-router": "3.0.*"`，可以安装`3.0`固定的任意版本，比如`3.0.1`、`3.0.2`。

### 国内镜像

npm、yarn、cnpm、pnpm默认镜像为https://registry.npmjs.org，国内访问慢经常连不上服务。因此列出几个国内知名的镜像站，都是与npm官方同步的

```js
// 阿里镜像/淘宝镜像
https://registry.npmmirror.com
// 华为镜像
https://mirrors.huaweicloud.com/repository/npm/
// 腾讯镜像
http://mirrors.cloud.tencent.com/npm/
```

注意的是淘宝镜像在2022年已经更换新域名，老域名虽然会跳转为了后期服务稳定，推荐使用上方新域名

查看镜像

```
npm config get registry
```

设置指定镜像

```js
npm config set registry https://registry.npmmirror.com
```

### 常用命令

```js
pnpm add Demo
pnpm add -g Demo
pnpm remove Demo //移除包
pnpm remove Demo --global //移除全局包

yarn global add Demo // yarn全局安装指定依赖
yarn global remove Demo // yarn删除全局指定依赖
yarn add Demo // yarn安装指定依赖
yarn remove Demo // yarn删除指定依赖
yarn config set registry https://registry.npmmirror.com // yarn设置镜像源
yarn config get registry // yarn查看镜像源

// 下面是传统npm
npm config set registry https://registry.npmmirror.com
npm config get registry
npm install -g @vue/cli

// node-sass设置
npm config set sass_binary_site https://registry.npmmirror.com/node-sass/
npm config set sass-binary-site https://registry.npmmirror.com/node-sass/
yarn config set sass_binary_site https://registry.npmmirror.com/node-sass/
npm i node-sass --sass_binary_site=https://registry.npmmirror.com/node-sass/
```

### 清理缓存

npm安装包的时候，有时候会遭遇报错，但是在此安装的时候，怎么都安装不上，此时需要清除缓存

```js
npm cache clean -f
// 或者
npm cache clean --force
```

### 推荐使用pnpm

pnpm是一个速度快、节省磁盘空间的软件包管理器，是同类工具速度的将近 2 倍，如果是新项目那么强烈建议使用pnpm

#### 安装

```js
npm install -g pnpm
```

#### 升级

```js
pnpm add -g pnpm
```

### 修改npm缓存位置

默认情况下，已作者本机为例，npm包缓存以及全局安装的包都在C盘路径中，分别是`C:\Users\DELL\AppData\Roaming\npm-cache`和`
C:\Users\DELL\AppData\Roaming\npm`长期以往便会将C盘塞满，因此可以塞到其他盘中

#### 常用命令

```js
// 查看npm安装目录
npm root -g
// 查看npm配置信息
npm config list
// 查看当前npm cache和prefix路径
npm config get cache
npm config get prefix
// 修改cache和prefix路径
npm config set cache  "D:\nodejs\node_cache"
npm config set prefix "D:\nodejs\node_global"
// 修改windows系统环境变量中增加node_global
path=D:\nodejs\node_global
```

选一个位置新建两个文件夹，分别起名为**node_cache** 和**node_global**

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228144717.png)

对于全局安装的依赖，如果不想因为更换目录而丢失，可以复制一份过去，这样就省去了重新安装的步骤

这里是将`C:\Users\DELL\AppData\Roaming\npm`下的文件复制到`D:\nodejs\node_global`下，node_cache缓存目录可无视

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230227174739.png)

修改路径

```javascript
npm config set cache  "D:\nodejs\node_cache"
npm config set prefix "D:\nodejs\node_global"
```

桌面找到`我的电脑`鼠标右键点击属性，点击`高级系统设置`，在系统属性窗口点击`环境变量`

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228145239.png)

系统变量新建NODE_PATH，值为刚刚设置的全局包的路径

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228145433.png)

用户变量设置path，双击path打开，添加全局包的路径

删除Roaming\npm字样的信息，作者这里的是C:\Users\DELL\AppData\Roaming\npm

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230228145604.png)

测试一下，大功告成！

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230227175543.png)

### Yarn修改缓存目录

注意：yarn的bin目录需要加到环境变量的path中，否则yarn全局安装的包在命令行中不能正常使用

默认目录如下，修改目录后需要更改

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230404134514.png)

查询yarn全局目录

```js
yarn global dir
```

查询yarn缓存目录

```js
yarn cache dir
```

改变yarn缓存位置

```js
yarn config set cache-folder "D:\nodejs\yarn_cache"
```

改变yarn全局依赖位置

```js
yarn config set global-folder "D:\nodejs\yarn_global"
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230404141129.png)

避免重复安装可以将原数据剪切过去，最后统一修改路径就行了

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230404135503.png)

修改yarn的bin目录，添加用户变量无效，这里放到了系统变量中

在我们使用全局安装包的时候，会在 “D:\nodejs\yarn_global”（上面的示例路径，要根据自己的实际情况配置） 下生成 node_modules/bin 目录

我们需要将 D:\nodejs\yarn_global\node_modules.bin 整个目录 添加到系统环境变量中去，否则通过yarn 添加的全局包 在cmd 中是找不到的。

```js
D:\nodejs\yarn_global\node_modules\.bin
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230407172727.png)

查看 yarn 全局bin位置

```js
yarn global bin
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230407173840.png)

默认的`yarn`命令`bin`，会存放到`npm`的全局安装的依赖包`node_global`里的`bin`，但是yarn添加的全局包还是会存放在自身设置的目录中，毕竟*Yarn* 还是*基于 npm* 的许多概念和流程来架构设计的

拓展文章：[yarn配置](https://juejin.cn/post/7125695269689098248)