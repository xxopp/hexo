---
title: vue实现iframe通信
tags:
  - Vue
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/0805623231.jpg
categories: Web前端
abbrlink: ac993ba7
date: 2022-07-21 16:06:47
---

之前没有在 Vue 中使用过 Iframe，网上的相关内容也比较少，这次的主要难点有以下两个：

1. 如何优雅地嵌入 Iframe
2. 如何优雅地实现父子页面间的通信

第一点倒是不难，嵌入 Iframe 时使用 ref 属性，可以在父组件中优雅地获取到 Iframe 对象及其内部的所有对象。相比之下，比较棘手的是第二点。

父子页面通过 EventListener 监听信号，然后以postMessage 方式进行发信。但是要注意 EventListener 的销毁和发信的时机（慎用 Iframe 的 onload 事件）。

### 文档

https://www.runoob.com/tags/tag-iframe.html

https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe

### 示例代码

#### 父页面

```js
<template>
  <div class="act-form">
    <iframe :src="src" ref="iframe"></iframe>
    <div @click="sendMessage">向 iframe 发送信息</div>
  </div>
</template>

<script>

export default {
  data () {
    return {
      src: '你的src',
      iframeWin: {}
    }
  },
  mounted () {
    this.iframeWin = this.$refs.iframe.contentWindow
    this.$nextTick(() => {
      // 在外部 Vue 的 window 上添加 postMessage 的监听，并且绑定处理函数 handleMessage
      window.addEventListener('message', this.handleMessage)
    })
  },
  destroyed () {
      // 注意移除监听！注意移除监听！注意移除监听！
      window.removeEventListener('message', this.handleMessage)
  }    
  methods: {
    sendMessage () {
      // 外部vue向iframe内部传数据
      this.iframeWin.postMessage({
        cmd: 'doSomething',
        params: {}
      }, '*')
    },
    handleMessage (event) {
      // 根据上面制定的结构来解析 iframe 内部发回来的数据
      const data = event.data
      switch (data.cmd) {
        case 'ready-for-receiving':
          // 业务逻辑
          break
      }
    }
  }
}
</script>
```

#### 子页面

```js
<template>
  <div>
    我是子组件
  </div>
</template>

<script>

export default {
  data () {
    return {}
  },
  mounted () {
    this.$nextTick(() => {
      window.addEventListener('message', this.handleMessage)

      // 告诉父组件准备好接收消息了
      window.parent.postMessage({
        cmd: 'ready-for-receiving'
      }, '*')
    })
  },
  destroyed () {
      // 注意移除监听！注意移除监听！注意移除监听！
      window.removeEventListener('message', this.handleMessage)
  }
  methods: {
    handleMessage (event) {
      // 根据上面制定的结构来解析 iframe 内部发回来的数据
      const data = event.data
      switch (data.cmd) {
        case 'doSomething':
          // 业务逻辑
          break
      }
    }
  }
}
</script>
```

### 补充

多个iframe时可以通过一个唯一的key值作为向父页面传值，父组件接收辨别条件，防止几个iframe一起接收

注意父页面监听事件及时销毁，至于销毁iframe示例可以直接将src指向空白页面释放内存

```js
// 把iframe指向空白页面，释放内存
this.iframeDom.src = 'about:blank';
```

