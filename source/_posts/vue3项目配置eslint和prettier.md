---
title: Vscode配置vue3项目eslint和prettier
tags:
  - Vscode
  - Vue
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/1401822459.jpg
categories: Web前端
abbrlink: 83aab208
date: 2022-08-05 10:27:50
---

### 准备工作

**Vscode安装eslint、prettier插件**

推荐安装Vue VSCode Snippets、Volar、EditorConfig

Vetur是vue2推荐的插件，不适用于vue3

vue3推荐使用vue官方推荐的**Volar**

**全局安装**

```js
npm i eslint prettier -g
npm i eslint-plugin-vue vue-eslint-parser -g
```

进入Vscode设置搜索 eslint 把Vetur的验证模板，**取消勾选**Validate vue-html in using eslint-plugin-vue

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20220805103623.png)

### eslint配置

项目根目录新建.eslintrc.js文件

```js
module.exports = {
  'root': true, // 是否是根目录
  'env': {
    'commonjs': true,
    'es6': true,
    'node': true,
    'browser': true
  },
  'extends': [
    'eslint:recommended',
    // 'plugin:vue/recommended', // 用于 Vue 2.x
    // 'plugin:vue/vue3-recommended', // 用于 Vue 3.x
    // 'plugin:vue/essential', // 预防错误的（用于 Vue 2.x）
    'plugin:vue/vue3-essential', // 预防错误的（用于 Vue 3.x）
    // 'prettier' // 配置prettier则开启
  ],
  // rules 里面的内容根据团队风格统一配置
  'rules': {
    'vue/no-multiple-template-root': 'off', // vue3不校验template根组件
     'generator-star-spacing': 'off', // 禁止空格报错检查
    'space-before-function-paren': 0, // 函数参数前的空格
    'prettier/prettier': 'warn', // prettier
    '@typescript-eslint/no-explicit-any': 'off', // 禁用typescript-eslint规则
    '@typescript-eslint/no-unused-vars': 'off', // 禁用eslint变量规则
    'vue/multi-word-component-names': 'off' // 用于检测当前的组件名称是否使用驼峰或多单词命名
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off'
  },
  'parserOptions': {
    'parser': 'babel-eslint'
  }
}
```

根目录新建.eslintignore文件，按照自身要求灵活配置

```js
node_modules
dist
src/assets/js
src/views
package-lock.json
.DS_Store
vue.config.js
public/
src/main.js
```

### prettier配置

项目根目录新建.prettierrc.js文件

```js
module.exports = {
  printWidth: 80,                    //（默认值）单行代码超出 80 个字符自动换行
  tabWidth: 2,                       //（默认值）一个 tab 键缩进相当于 2 个空格
  useTabs: true,                     // 行缩进使用 tab 键代替空格
  semi: false,                       //// 句尾不使用分号
  singleQuote: true,                 // 使用单引号
  quoteProps: 'as-needed',           //（默认值）仅仅当必须的时候才会加上双引号
  jsxSingleQuote: true,              // 在 JSX 中使用单引号
  trailingComma: 'all',              // 不用在多行的逗号分隔的句法结构的最后一行的末尾加上逗号
  bracketSpacing: true,              //（默认值）在括号和对象的文字之间加上一个空格
  jsxBracketSameLine: true,          // 把 > 符号放在多行的 JSX 元素的最后一行
  arrowParens: 'avoid',              // 当箭头函数中只有一个参数的时候可以忽略括弧
  htmlWhitespaceSensitivity: 'ignore', // vue template 中的结束标签结尾尖括号掉到了下一行
  vueIndentScriptAndStyle: false,    //（默认值）对于 .vue 文件，不缩进 <script> 和 <style> 里的内容
  embeddedLanguageFormatting: 'auto', //（默认值）允许自动格式化内嵌的代码块
}
```

根目录新建.prettierignore文件，按照自身要求灵活配置

```js
build/
package.json
public/
test/*.*
```

### 拓展配置

VSCode中setting.json的两种配置位置，一种是用户全局配置，一种是工作区配置，而工作区配置的权限大于用户全局配置，当然仅在配置了setting.json中生效

项目根目录设置setting.json为了方便团队代码质量与格式规范

```js
{
    "eslint.alwaysShowStatus": true, // 总是在 VSCode 显示 ESLint 的状态
    "eslint.quiet": true, // 忽略 warning 的错误
    "editor.codeActionsOnSave": { // 保存时使用 ESLint 修复可修复错误
        "source.fixAll": true,
        "source.fixAll.eslint": true
    },
    "vetur.validation.template": false // 使用ESLint插件的提示而不是vetur自带的
}
```

### eslint和prettier区别

Prettier 是一款代码格式化工具，用于检测代码中的格式问题，比如单行代码长度、tab长度、空格、逗号表达式等。 在功能职责上，ESlint 偏向于把控项目的代码质量，而Prettier 更偏向于统一项目的编码风格

ESlint 的主要功能包含代码格式的校验，代码质量的校验。 prettier 只是代码格式的校验（并格式化代码），不会对代码质量进行校验。 代码格式问题通常指的是：单行代码长度、tab长度、空格、逗号表达式等问题。而代码质量问题指的是：未使用变量、三等号、全局变量声明等问题

ESlint一般为必须项，而Prettier看个人以及团队是否有这需求

### 补充

可以通过配置.eslintrc.js，新增prettier插件以及规则，就能将不符合prettier格式的问题通过eslint触发校验提醒

```js
'extends': [
   'plugin:prettier/recommended',
   'prettier', // eslint-config-prettier 可简写成 prettier
 ],
'rules': {
   "prettier/prettier": "error",
},
```

如果项目是通过vue-cli创建的，可以在package.json配置文件新增一条命令，避免手动输入麻烦

```js
{
    "scripts": {
        "lint": "vue-cli-service lint"
    }
}
```

执行这个命令之后，它会去检查和修复部分可以修复的问题
