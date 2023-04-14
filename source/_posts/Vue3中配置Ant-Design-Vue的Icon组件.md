---
title: Vue3中配置Ant Design Vue的Icon组件
tags:
  - Ant Design
  - Vue3
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/kid-6565461_640.jpg
categories: Web前端
abbrlink: 57596eb
date: 2022-08-24 10:24:13
---

Ant Design Vue新版本取消了标签引入icon的方式，改用了组件引入注册，一个icon对应一个组件，个人感觉更加麻烦了，而 Vue3页面组件引入注册与Vue2有修改，Ant Design Vue复制过来的代码并不能正常引用

#### 准备工作

先安装Ant Design Vue

```js
npm i --save ant-design-vue
```

全局完整注册

```js
import { createApp } from 'vue';
import Antd from 'ant-design-vue';
import App from './App';
import 'ant-design-vue/dist/antd.css';

const app = createApp(App);

app.use(Antd).mount('#app');
```

使用图标组件，你需要安装 `@ant-design/icons-vue` 图标组件包：

```js
npm install --save @ant-design/icons-vue
```

#### main.ts文件配置

```js
import * as Icons from "@ant-design/icons-vue";
const icons: any = Icons;


// 使用全部图标
for (const i in iconList) {
    // 全局注册一下组件
    app.component(i, icons[i]);
}


// 使用部分图标(根据实际业务需要的icon组件扩充)
const iconList: any = [
    "UserOutlined",
    "VideoCameraOutlined",
    "UploadOutlined",
    "MenuUnfoldOutlined",
    "MenuFoldOutlined",
    "PlayCircleOutlined",
];
for (const i of iconList) {
    // 全局注册一下组件
    app.component(i, icons[i]);
}
```

完成后看效果

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20220824104013.png)

#### 注意事项

全局注册时`app.component(i, icons[i]);`应在`app.mount("#app");`之前