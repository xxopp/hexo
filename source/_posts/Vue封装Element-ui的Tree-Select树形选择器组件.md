---
title: Vue封装Element ui的Tree Select树形选择器组件
tags:
  - Vue
  - Element
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119f.jpg'
categories: Web前端
abbrlink: c89a5d43
date: 2020-11-19 17:33:44
---

Element ui中的Select选择器功能太单调远远无法满足各种花式的产品需求，起初使用一些各路大牛写好的插件直接NPM安装就完事，久而久之就有点依赖各种第三方了，于是参考网上封装的教程修修改改终于可以放在项目上用了

之前用的是[vue-treeselect](https://vue-treeselect.js.org/)，直接NPM下载安装到项目上就能用了

```bash
npm install --save @riophae/vue-treeselect
```

## treeSelect封装代码

创建一个vue文件将以下代码复制进去

```bash
<template>
  <el-select ref="select" :value="valueTitle" :clearable="clearable" :placeholder="placeholder" @clear="clearHandle">
    <el-input
      v-model="filterText"
      class="selectInput"
      placeholder="检索关键字"
    />

    <el-option :value="valueTitle" :label="valueTitle" class="options">
      <el-tree
        id="tree-option"
        ref="selectTree"
        :accordion="accordion"
        :data="options"
        :props="props"
        :node-key="props.value"
        :default-expanded-keys="defaultExpandedKey"
        :filter-node-method="filterNode"
        @node-click="handleNodeClick"
      />
    </el-option>
  </el-select>
</template>

<script>
export default {
  name: 'ElTreeSelect',
  props: {
    /* 配置项 */
    props: {
      type: Object,
      default: () => {
        return {
          value: 'id', // ID字段名
          label: 'title', // 显示名称
          children: 'children' // 子级字段名
        }
      }
    },
    /* 选项列表数据(树形结构的对象数组) */
    options: {
      type: Array,
      default: () => { return [] }
    },
    /* 初始值 */
    value: {
      default: () => { return null }
    },
    /* 可清空选项 */
    clearable: {
      type: Boolean,
      default: () => { return true }
    },
    /* 自动收起 */
    accordion: {
      type: Boolean,
      default: () => { return false }
    },
    placeholder: {
      type: String,
      default: () => { return '请选择' }
    }
  },
  data() {
    return {
      filterText: '',
      valueId: this.value, // 初始值
      valueTitle: '',
      defaultExpandedKey: []
    }
  },
  watch: {
    value() {
      this.valueId = this.value
      this.initHandle()
    },
    filterText(val) {
      this.$refs.selectTree.filter(val)
    }
  },
  mounted() {
    this.initHandle()
  },
  methods: {
    // 初始化值
    initHandle() {
      if (this.valueId) {
        this.valueTitle = this.$refs.selectTree.getNode(this.valueId).data[this.props.label] // 初始化显示
        this.$refs.selectTree.setCurrentKey(this.valueId) // 设置默认选中
        this.defaultExpandedKey = [this.valueId] // 设置默认展开
      }
      this.initScroll()
    },
    // 初始化滚动条
    initScroll() {
      this.$nextTick(() => {
        const scrollWrap = document.querySelectorAll('.el-scrollbar .el-select-dropdown__wrap')[0]
        const scrollBar = document.querySelectorAll('.el-scrollbar .el-scrollbar__bar')
        scrollWrap.style.cssText = 'margin: 0px; max-height: none; overflow: hidden;'
        scrollBar.forEach(ele => ele.style.width = 0)
      })
    },
    // 切换选项
    handleNodeClick(node) {
      this.valueTitle = node[this.props.label]
      this.valueId = node[this.props.value]
      const data = {
        id: this.valueId,
        val: this.valueTitle,
        father: this.$refs.selectTree.getNode(node.id).parent.data.label // 获取父节点
      }
      this.$emit('getValue', data)
      this.defaultExpandedKey = []
      this.$refs.select.blur()
    },
    // 清除选中
    clearHandle() {
      this.valueTitle = ''
      this.valueId = null
      this.defaultExpandedKey = []
      this.clearSelected()
      this.$emit('getValue', null)
    },
    /* 清空选中样式 */
    clearSelected() {
      const allNode = document.querySelectorAll('#tree-option .el-tree-node')
      allNode.forEach((element) => element.classList.remove('is-current'))
    },
    filterNode(value, data) {
      if (!value) return true
      return data.name.indexOf(value) !== -1
    }
  }
}
</script>

<!-- 添加scoped属性，是为了让css只在该组件生效，避免样式污染 -->
<style scoped>
  .el-scrollbar .el-scrollbar__view .el-select-dropdown__item{
    height: auto;
    max-height: 274px;
    padding: 0;
    overflow: hidden;
    overflow-y: auto;
  }
  .el-select-dropdown__item.selected{
    font-weight: normal;
  }
  ul li >>>.el-tree .el-tree-node__content{
    height:auto;
    padding: 0 20px;
  }
  .el-tree-node__label{
    font-weight: normal;
  }
  .el-tree >>>.is-current .el-tree-node__label{
    color: #409EFF;
    font-weight: 700;
  }
  .el-tree >>>.is-current .el-tree-node__children .el-tree-node__label{
    color:#606266;
    font-weight: normal;
  }
  .selectInput{
    padding: 0 5px;
    box-sizing: border-box;
  }
</style>
```

handleNodeClick()为异步父节点点击事件，通过传入的参数node我们可以得到

```bash
const data = {
    id: this.valueId, // id
    val: this.valueTitle, // lable
    father: this.$refs.selectTree.getNode(node.id).parent.data.label // 获取父节点
}
```

`this.$refs.selectTree.getNode(node.id).parent.data`为获取父节点方法

## 项目引用

封装完毕直接import引入

```bash
import treeSelect from '@/components/treeSelect'
components: {
    treeSelect
  },
```

template中这样填写

```bash
            <el-col :span="12">
              <el-form-item label="预案类型" prop="type">
                <treeSelect
                  :props="defaultProps"
                  :options="treeData"
                  style="width:100%;"
                  :value="addForm.basicInfo.type"
                  :accordion="true"
                  placeholder="请选择预案类型"
                  @getValue="getValue($event)"
                />
              </el-form-item>
            </el-col>
```

methods定义方法

```bash
// 树节点
    getValue(value) {
    	console.log(value) // 这里会打印一个包含id，lable以及父节点的对象方便处理数据
    }
```

