---
title: uniapp接口请求uni.request封装使用
tags:
  - 小程序
  - uniapp
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/7342177_640.jpg
categories: Web前端
abbrlink: d304ee7d
date: 2022-08-15 09:44:28
---

uniapp开发下程序肯定涉及到很多接口请求，无论是原生小程序还是uniapp均提供了`request()`方便直接使用，但是项目做到最后发现直接通过uni.request的写法太过于冗余且不利于后期管理，万一后期接口主域名换了还得一个个批量修改，得不偿失

https://uniapp.dcloud.net.cn/api/request/request.html

#### 未封装前的uni.request

```js
uni.request({
    url: 'https://www.example.com/request', //仅为示例，并非真实接口地址。
    data: {
        time: '2022.08.15',
        num: 6
    },
    header: {
        'custom-header': 'hello' //自定义请求头信息
    },
    success: res => {
        console.log(res.data);
        this.text = 'request success';
    },
    fail: e => {
        console.log(e);
    }
});

```

#### 开始封装

在config文件夹新建request.js文件，具体路径以及文件名可自定义发挥

```js
// 引入全局的url配置变量
import baseUrl from '@/config/baseUrl'

const request = param => {
	// 请求参数
	let method = param.method
	let header = {}
	let data = param.data || {}
	let requestUrl = baseUrl.jdUrl + param.url
	method = method.toUpperCase() //小写改为大写
    // header请求头信息默认，如果需要动态设置可通过param.header自定义
	if (method === "POST") {
		header = {
			'content-type': "application/x-www-form-urlencoded"
		}
	} else {
		header = {
			'content-type': "application/json"
		}
	}
	// 返回 promise
	return new Promise((resolve, reject) => {
		// 请求
		uni.request({
			url: requestUrl,
			data: data,
			method: method,
			header: header,
			success: res => {
				// 判断 请求api 格式是否正确
                // 此处的判断业务请根据接口返回的实际数据来自定义，此处代码仅供参考修改，可能并不适用于你的项目
				if (res.data.code && res.data.code !== '10000') {
					reject(res)
				} else {
					// 将结果抛出
					resolve(res.data)
				}
			},
			//请求失败
			fail: e => {
				reject(e)
			}
		})
	})
}
export default request
```

项目上的接口一般都会把指定功能所涉及到的接口集中放置到一个js文件中管理

新建api文件夹，在文件夹内新建api.js文件

```js
import request from '@/config/request' // 引入封装好的request

// 获取新闻列表
export function getNewList (data) {
	return request({
		method: "GET", // 请求方式
		url: 'jisuapi/get', // 请求url
		data: data // 参数
	})
}

// 段子
export function getHaHa (data) {
	return request({
		method: "GET", // 请求方式
		url: 'showapi/tpxh', // 请求url
		data: data // 参数
	})
}
```

如何在页面中引用呢

```js
import { getNewList } from '@/api/api'


getNewList(data).then(res => {
    if (res.code === '10000') {
        // 此处写业务代码
    }
}).catch(e => {
    console.log('~~~请求错误~~~')
})
```

