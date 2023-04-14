---
title: TinyMCE富文本组件使用指北
tags:
  - 富文本
  - Vue
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/kid-5718703_640.jpg
categories: Web前端
abbrlink: 9c5c0b
date: 2023-03-09 11:22:00
---

富文本为前端填不完的坑之一，虽说市面上有很多开源的产品可供选择，但是对于一些产品需求而言还是需要一些拓展性会比较好

选择TinyMCE无疑就是看重历史悠久且稳定，网上资源教程丰富，就连WordPress之前的经典编辑器就是用的TinyMCE，纵观国内外各种富文本还是选择了它

可惜就是官方文档未汉化，不过好在国内还是有不少开发者热衷于它，于是就有了本土汉化的文档，结合官方英文文档食用效果更佳

 [TinyMCE中文手册](http://tinymce.ax-z.cn/)    [汉化包zh_CN.js](http://tinymce.ax-z.cn/static/tiny/langs/zh_CN.js)

### 安装依赖

演示项目是基于Vue2的脚手架，因此我们需要安装`@tinymce/tinymce-vue`来使用

新版本的tinymce-vue不再支持Vue2，如需使用请按下方安装

```js
// vue2.x安装 tinymce-vue 3或以下的相关版本
npm install --save "@tinymce/tinymce-vue@^3"

// vue3.x安装tinymce-vue 4的相关版本
npm install --save "@tinymce/tinymce-vue@^4"
```

演示项目依赖版本如下：

```js
"tinymce": "5.4.1"
"@tinymce/tinymce-vue": "2.1.0"
```

### 配置运行

文件内引入依赖、相关样式、tinymce插件等

```js
import tinymce from 'tinymce/tinymce'
import Editor from '@tinymce/tinymce-vue'
import 'tinymce/icons/default'
import 'tinymce/themes/silver/theme'
import 'tinymce/plugins/image'
import 'tinymce/plugins/link'
import 'tinymce/plugins/media'
import 'tinymce/plugins/table'
import 'tinymce/plugins/lists'
import 'tinymce/plugins/wordcount'
import 'tinymce/plugins/colorpicker'
import 'tinymce/plugins/fullscreen'
```

页面元素以及初始化配置

主题文件以及汉化包文件直接放至`\public\tinymce`文件夹内

需要将 node_modules下的`tinymce/skins`文件夹复制到项目的`public/tinymce`文件夹下，保持对应的目录结构

```js
<div class="tinymce-editor">
    <editor id="tinymce" v-model="myValue" :init="init" :disabled="disabled"> </editor>
</div>

mounted() {
   tinymce.init({});
},

  data() {
    return {
      disabled: false,
      myValue: 'TinyMCE：功能强大、所见即所得的富文本编辑器',
      reloading: false,
      //初始化配置
      init: {
        selector: '#tinymce', // tinymce的id
        language_url: '/tinymce/langs/zh_CN.js',
        language: 'zh_CN',
        skin_url: '/tinymce/skins/ui/oxide',
        content_css: `/tinymce/skins/content/default/content.css`,
        height: 850,
        plugins: 'lists image media link table wordcount fullscreen',
        toolbar: 'undo redo | formatselect | bold italic | alignleft aligncenter alignright alignjustify | bullist numlist outdent indent | lists link unlink image media table | removeformat | fullscreen',
        branding: false, // 去水印
        menubar: false, // 隐藏最上方menu
        elementpath: false, // 禁用编辑器底部的状态栏
        statusbar: false, // 隐藏编辑器底部的状态栏
        paste_data_images: true, // 允许粘贴图像
        toolbar_mode: false, // 工具栏抽屉模式：toolbar_drawer5.2.0已废除用toolbar_mode替代
        media_live_embeds: true, // 媒体实时嵌入, 启用此选项后，用户将在可编辑区域中看到嵌入视频内容的实时预览，而不是占位符图像
        block_formats: 'Paragraph=p;Header 1=h1;Header 2=h2;Preformatted=pre', // 区块列表
        formats: {
          pre: { block: 'pre', classes: 'center' }
        },
        content_style: '.center{text-align:center;font-size:12px;}',
        convert_urls: false // 默认情况下，URL会自动转换（混乱），因为内置的浏览器逻辑就是这样工作的,富文本编辑器在服务器图片上传是相对路径
        images_upload_handler: () => {}, // 图片上传
        video_template_callback: () => {},
        file_picker_callback: () => {}
      }
    }
  },
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230308163221.png)

TinyMCE内置的插件有很多，例如加一个查看html代码的功能，则可以引入

```js
import 'tinymce/plugins/code'
```

然后在初始化配置项`plugins`添加code

如果要在toolbar显示则在相应位置添加code

### 音视频支持

默认TinyMCE无上传图片的选项，需要使用`images_upload_handler`方法实现

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230308153757.png)

```js
images_upload_handler: (blobInfo, success) => {
    let formData = new FormData()
    formData.append('file', blobInfo.blob(), blobInfo.filename())
    uploadOSS(formData).then((res) => {
        if (res.code === 200) {
            success(res.data)
        }
    })
}
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20230308153711.png)

视频一起其他文件需要使用`file_picker_callback`方法

```js
file_picker_callback: (callback, value, meta) => {
    let filetype = '.pdf, .txt, .zip, .rar, .7z, .doc, .docx, .xls, .xlsx, .ppt, .pptx, .mp3, .mp4'
    switch (meta.filetype) {
        case 'image':
            filetype = '.jpg, .jpeg, .png, .gif'
            break
        case 'media':
            filetype = '.mp3, .mp4'
            break
        case 'file':
        default:
    }
    const input = document.createElement('input')
    input.setAttribute('type', 'file')
    input.setAttribute('accept', filetype)
    const that = this // 为 Vue 构造函数中的 this，指向 Vue 实例对象
    input.onchange = async function () {
        const file = this.files[0]
        let formData = new FormData()
        formData.append('file', file, file.name)
        uploadOSS(formData).then((res) => {
            if (res.code === 200) {
                callback(res.data)
            } else {
                callback()
            }
        })
    }
    input.click()
}
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/image-20230308153956078.5hwoaq2huog0.webp)

图片很好搞定，接下来就是视频了，网上找了一堆方法多办治标不治本，无非就是这两种

media_url_resolver函数调用：

```js
		media_url_resolver: function (data, resolve) {
          try {
            let videoUri = encodeURI(data.url)
            let embedHtml = `<p>
                 <span
                data-mce-selected="1"
                data-mce-object="video"
                data-mce-p-width="745"
                data-mce-p-height="auto"
                data-mce-p-controls="controls"
                data-mce-p-controlslist="nodownload"
                data-mce-p-allowfullscreen="true"
                data-mce-p-src=${videoUri} >
                <video src=${videoUri} width="745" height="auto" controls="controls" controlslist="nodownload">
                </video>
              </span>
                </p>
                <p style="text-align: left;"></p>`
            resolve({ html: embedHtml })
          } catch (e) {
            resolve({ html: '' })
          }
        },
```

video_template_callback函数调用：

```js
		video_template_callback: (data) => {
          return `<p>
                  <span
                    class="mce-object mce-object-video"
                    data-mce-selected="1"
                    data-mce-object="video"
                    data-mce-p-width="745"
                    data-mce-p-height="auto"
                    data-mce-p-controls="controls"
                    data-mce-p-controlslist="nodownload"
                    data-mce-p-allowfullscreen="true"
                    data-mce-p-src=${data.source} >
                    <video src=${data.source} width="745" height="auto" controls="controls" controlslist="nodownload">
                    </video>
                  </span>
                </p>`
        }
```

以上任意一个函数在配置后都能生效，编辑时新增一个视频时并且真的能在富文本框内播放了，此时的你欣喜若狂，如果富文本框会缓存数据，会发现改了个寂寞

编辑时预览是没问题了，但是再次打开渲染原数据又是无法预览，此时要是再添加个新视频至富文本，就会有一个能预览一个不能

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230308170825.3ua5qwmdtlo0.webp)

控制台一看发现将视频标签解析成`<img>`了

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20230308171456.vevismrgpgg.webp)

上述方法如果只能编辑一次且不能再次编辑的情况下倒是完美解决了，但还是不满足项目的需求，于是继续寻找解决方案

寻寻觅觅，终于找到一篇博文[[tinymce富文本编辑器，视频上传后无法预览](https://www.xqtt29.com/articles/2020/04/23/1587627704218.html)](https://www.xqtt29.com/articles/2020/04/23/1587627704218.html)

解决该问题，我们就需要修改Media插件的源代码了。我们修改他的渲染逻辑。基本思路就是当他渲染video节点时。我们把他的img标签强行换成video标签。src和controls属性什么的都填充好即可。

具体实施步骤如下,可以无视tinymce版本。原理都一样,不管哪个版本照改不误，无非就是修改的代码所在行数不一样

首先在函数外，推荐到顶部`var global`下声明一个变量`var videoSource = ''`

然后找到`if (node.name === 'iframe' && hasLiveEmbeds(editor) && global$8.ceFalse)`这一行，如下面注释所示：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/image.3p6bs912nrk0.webp)

将注释的地方添加以下代码：

```js
if (node.name === 'video' && hasLiveEmbeds(editor) && global$8.ceFalse) {
    videoSource = ''
    if (node.attributes['map'] && node.attributes['map'].src) {
        videoSource = node.attributes['map'].src
    } else {
        for (var ii = 0; ii < node.attributes.length; ii++) {
            if (node.attributes[ii].name == 'src') {
                videoSource = node.map.node.attributes[ii].value
            }
        }
    }
    if (node.firstChild && node.firstChild.value) {
        var elel = node.firstChild && node.firstChild.value
        var objE = document.createElement('div')
        objE.innerHTML = elel
        var dom = objE.getElementsByTagName('source')[0]
        videoSource = dom.getAttribute('src')
    }
    node.replace(createPreviewIframeNode(editor, node))
}
```

接着我们找到`var createPreviewIframeNode = function (editor, node)`并注释，如下图

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/image.6af9dsumsoo0.webp)

注释内容修改为：

```js
var createPreviewIframeNode = function (editor, node) {
    var previewWrapper
    var previewNode
    var shimNode
    var name = node.name
    previewWrapper = new global$7('span', 1)
    previewWrapper.attr({
        contentEditable: 'false',
        style: node.attr('style'),
        'data-mce-object': name,
        class: 'mce-preview-object mce-object-' + name
    })
    retainAttributesAndInnerHtml(editor, node, previewWrapper)
    previewNode = new global$7(name, 1)
    previewNode.attr({
        // src: node.attr('src'),
        src: videoSource || node.attr('src'), // 修改
        controls: 'controls', // 新增
        allowfullscreen: node.attr('allowfullscreen'),
        style: node.attr('style'),
        class: node.attr('class'),
        width: node.attr('width'),
        height: node.attr('height'),
        frameborder: '0'
    })
    shimNode = new global$7('span', 1)
    shimNode.attr('class', 'mce-shim')
    previewWrapper.append(previewNode)
    previewWrapper.append(shimNode)
    return previewWrapper
}
```

到此无论是编辑还是回显，富文本内的视频都能直接点击播放了

### 添加按钮

以下是给toolbar(工具栏)添加一个简单的按钮

```js
toolbar: 'myCustomToolbarButton',
setup: function (editor) {
 editor.ui.registry.addButton('myCustomToolbarButton', {
     text: 'My Custom Button',
     onAction: function () {
       	alert('Button clicked!')
     }
  })
}
```

然后toolbar就会出现一个`myCustomToolbarButton`文字，点击就会触发alert网页弹窗

当然了可能太简陋，我们需要进阶以下加个按钮图标或者其他的操作

点击按钮后，通过url打开浮动窗口

```js
setup: function (editor) {
    editor.ui.registry.addButton('myCustomToolbarButton', {
        icon: 'accessibility-check',
        onAction: function () {
            editor.windowManager.openUrl({
                title: '选择会议模板',
                url: 'https://www.ak47s.cn/',
                width: 840,
                height: 300
            })
        }
    })
}
```

### 更换icon图标

TinyMCE内置的图片可以在node_modules下的`tinymce/icons`文件夹找到，正常情况下我们可以直接找到相关的icon名使用

但是有时候产品觉得图标丑需要替换那也很简单，将icons文件夹复制到指定文件夹单独编辑后引入就可以了

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/image.3zxl3tllemm0.webp)

直接去阿里矢量库找相关的svg素材或者直接找UI要就行了，在文件内添加即可生效！

```js
// 5.0.6新增通过一个url来指定图标js文件的所在位置,但设置该参数以后，还需要再用icons一次，这和引入外部语言包的用法一致。
icons_url: '/icons/custom/icons.js'
icons: 'custom'
```

更多功能还在探索中！

### 参考

[https://www.cnblogs.com/qingfengyuan/p/14239754.html](https://www.cnblogs.com/qingfengyuan/p/14239754.html)

