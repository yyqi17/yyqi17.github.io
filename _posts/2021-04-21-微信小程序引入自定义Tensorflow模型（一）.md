---
layout: article
title: 微信小程序引入自定义Tensorflow模型（一）
tags: 微信小程序 Tensorflow
mode: immersive
key: post20210421
lightbox: true
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image: 
    gradient: 'linear-gradient(135deg, rgba(40, 129, 161 , .4), rgba(80, 171, 204, .4))'
    src: https://s2.ax1x.com/2019/10/10/uThyxe.png
---

<!--more-->

<br/>

### 一、基础环境

1. 微信开发者工具（稳定版）

2. 含Tensorflow运行环境的Python编译器

<br/>

### 二、导入步骤

#### Step 1. 创建项目+引入插件

<br/>
项目需要使用正式可发布的AppID，测试号的小程序目前（2021.4）无法使用插件。

Tensorflow.js插件可以从[TensorFlow.js微信官方文档](https://mp.weixin.qq.com/wxopen/plugindevdoc?appid=wx6afed118d9e81df9&token=692983652&lang=zh_CN)点击引入，引入前需要先使用正式的AppID登陆。引入后应该可以从开发者工具“详情-基本信息-插件信息”看到新添加的插件。

另一项需要对项目进行的配置是基础库的选择，在“详情-本地设置”中，将基础库设置为新版。

<br/>
#### Step 2. 安装Node.js

<br/>
[官网](nodejs.org)下载，选择LTS版本。

命令行中输入 `node --version` 和 `npm --version` 可以得到版本号，即证明安装成功。

<br/>
#### Step 3. 引入tfjs包+fetch-wechat包

<br/>
命令行切换到小程序项目路径*（项目名不能含中文）*。

1） 输入 `npm init` 初始化npm环境。需要填的信息一路回车默认即可。

<br/>
2） 安装三个必要的tfjs包与fetch-wechat包：

  `npm install @tensorflow/tfjs-core`
  `npm install @tensorflow/tfjs-converter`
  `npm install @tensorflow/tfjs-backend-webgl`
  `npm install @tensorflow/fetch-wechat`
  
   这里如果按照NEXT学院视频没有安装第三个tfjs包的话，保存运行后会提示“No backend found in registry”问题。
  
<br/>
3） 项目内构建npm

先从“详情-本地设置”中打开npm模块，再点击“工具-构建npm”。*每次添加新的npm模块都需要重新构建*。

<br/>
4） 修改项目代码

app.json <mark>这里version一定不要直接复制官方文档的旧版0.0.6</mark>，否则会报错Plugin"wx.../0 is not defined.
```json
{
  ...
  "plugins": {
    "tfjsPlugin": {
      "version": "0.1.0", 
      "provider": "wx6afed118d9e81df9"
    }
  }
  ...
}
```

<br/>
app.js
```javascript
var fetchWechat = require('fetch-wechat');
var tf = require('@tensorflow/tfjs-core');
var webgl = require('@tensorflow/tfjs-backend-webgl');
var plugin = requirePlugin('tfjsPlugin');
//app.js
App({
  onLaunch: function () {
    plugin.configPlugin({
      fetchFunc: fetchWechat.fetchFunc(),
      tf, webgl, canvas: wx.createOffscreenCanvas()
    });
  }
});
```

<br/>
5） 验证

此时在.js文件中输入 `tf.tensor([1, 2, 3, 4]).print()` 可以输出Tensor [1, 2, 3, 4]，证明Tensorflow.js的引入成功。


<br/>


参考资料：

[TensorFlow.js微信官方文档](https://mp.weixin.qq.com/wxopen/plugindevdoc?appid=wx6afed118d9e81df9&token=692983652&lang=zh_CN)

[【NEXT学院】TensorFlow.js遇到小程序](https://ke.qq.com/course/428263)

<br/>

