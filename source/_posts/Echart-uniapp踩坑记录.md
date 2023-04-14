---
title: Echart+uniapp踩坑记录
tags:
  - uniapp
  - 小程序
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/110331751.jpg
categories: Web前端
abbrlink: 6a9f4f5b
date: 2022-05-24 13:25:57
---
H5端流行的echart报表因为涉及大量dom操作，无法跨端使用,wx-chart在跨端和更新方面都不足,插件市场提供了比wx-chart更好的、全端可用的[uChart](https://ext.dcloud.net.cn/plugin?id=271)。但受限于小程序架构逻辑层和视图层分离，导致的通信折损，图表的动画性能不佳。并且uchart只实现了echart的常用功能，还有一些功能没有实现。

## **新的解决方案**

从uni-app 2.5.5+起，新提供了[renderjs](https://uniapp.dcloud.io/frame?id=renderjs)技术。它是wxs的升级版，一种可以运行在视图层的js。

通过renderjs编写的代码，直接运行在视图层（也就是webview中），可以完整的运行echart等库，并且没有了逻辑层和视图层频繁通行的折损，让动画不再卡顿。

renderjs支持app-vue和h5，不支持其他平台。如果你只考虑这2个平台，可以直接使用本示例，使用完整版的echart。如果还要兼容多端小程序，建议仍然使用uchart。

renderjs，不止能运行echart，其他如F2、threejs等web库都可以运行。

### 注意事项：

echart最新版本对移动端触摸交互不太理想，这里推荐使用3.6.2旧版本使用

[https://www.jsdelivr.com/package/npm/echarts](https://www.jsdelivr.com/package/npm/echarts)

```jsx
<template>
	<view class="content">
		<view @click="echarts.onClick" :prop="option" :change:prop="echarts.updateEcharts" id="echarts" class="echarts"></view>
		<button @click="changeOption">更新数据</button>
		<button @click="onIndex(1)">tooltip1</button>
		<button @click="onIndex(2)">tooltip2</button>
		<button @click="onIndex(3)">tooltip3</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        myChart: null,
        chartsData: chartsJson,
				option: {
            tooltip: {
              show: true,
              trigger: 'axis'
            },
            xAxis: {
                type: 'category',
                boundaryGap: false,
                data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
            },
            yAxis: {
                type: 'value'
            },
            series: [
                {
                    name: '高程',
                    data: [820, 932, 901, 934, 1290, 1330, 1320],
                    type: 'line',
                    smooth: true,
                    symbolSize: 0,
                    areaStyle: {
                      normal: {
                        color: '#5EC9F7'
                      }
                    },
                    lineStyle: {
                      normal: {
                        color: '#0BA1E3',
                        opacity: 0.2
                      }
                    },
                    markLine: {
                      symbol: "none",
                      silent: false,
                      lineStyle: {
                        normal: {
                          type: 'solid',
                          color: '#666',
                          opacity: 0.4
                        }
                      },
                      label: {
                        show: true,
                        position: 'end',
                        formatter: '标记线'
                      },
                      data: [
                        {
                          label: {
                            normal: {
                              formatter: '标记线2'
                            }
                          },
                          xAxis: 30
                        },
                        {
                          label: {
                            normal: {
                              formatter: '标记线6'
                            }
                          },
                          xAxis: 66
                        }
                      ]
                    }
                }
            ]
				}
			}
		},
		methods: {
			changeOption() {
				const data = this.option.series[0].data
				// 随机更新示例数据
				data.forEach((item, index) => {
					data.splice(index, 1, Math.random() * 40)
				})
			},
      onIndex(index) {
        this.myChart.dispatchAction({
          type: 'showTip',
          seriesIndex:0,  // 显示第几个series
          dataIndex: index // 显示第几个数据
        })
      },
			onViewClick(options) {
				console.log(options)
			}
		}
	}
</script>

<script module="echarts" lang="renderjs">
	let myChart
	export default {
		mounted() {
			if (typeof window.echarts === 'function') {
				this.initEcharts()
			} else {
				// 动态引入较大类库避免影响页面展示
				const script = document.createElement('script')
				// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
				script.src = 'static/echarts3.6.2.js'
				script.onload = this.initEcharts.bind(this)
				document.head.appendChild(script)
			}
		},
		methods: {
			initEcharts() {
				this.myChart = echarts.init(document.getElementById('echarts'))
				// 观测更新的数据在 view 层可以直接访问到
				this.myChart.setOption(this.option)
        this.myChart.on('click', function(params) {
          console.log(params,'-----params-----')
        })
      },
			updateEcharts(newValue, oldValue, ownerInstance, instance) {
				// 监听 service 层数据变更
				this.myChart.setOption(newValue)
			},
			onClick(event, ownerInstance) {
				// 调用 service 层的方法
				ownerInstance.callMethod('onViewClick', {
					test: 'test'
				})
			},
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.echarts {
		margin-top: 100px;
		width: 100%;
		height: 300px;
	}
</style>
```

echart有个坑，就是折线图点击不会触发组件自身的点击事件，这里可以换一种实现方法，就是通过tooltip配置项formatter触发

通过缓存实现数据传递

```jsx
formatter: function(params) {
    sessionStorage.setItem('xindex', params[0].dataIndex)
    return `${params[0].data.name ? params[0].data.name + '<br />' : ''}` + '距离: ' + params[0].data.distance + 'km<br />' + '高程: ' + params[0].data.value + 'm'
}
```

dispatchAction显示指定位置的tooltip框

```jsx
this.myChart.dispatchAction({
    type: 'showTip',
    seriesIndex: 0,  // 显示第几个series
    dataIndex: index // 显示第几个数据，index变量控制
})
```

实现简单的图表页面缩放效果

```jsx
this.option.dataZoom[0].start = 20
this.option.dataZoom[0].end = 50
let timer = setTimeout(() => {
    this.option.dataZoom[0].start = 0
    this.option.dataZoom[0].end = 100
    clearTimeout(timer)
},800)
```