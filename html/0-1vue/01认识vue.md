# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 认识vue

### 环境
备注：学习之前最好具备HTML、CSS 和 JavaScript 的基础知识。

### VUE安装
vue的安装有两种方式

1. 直接用 script 引入
```
对于制作原型或学习，你可以这样使用最新版本：
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

对于生产环境，我们推荐链接到一个明确的版本号和构建文件，以避免新版本造成的不可预期的破坏：
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.12"></script>

```

2. 使用 NPM 安装
一句话解释NPM：一个包管理工具，类似yarn、maven都是项目包管理工具。(构建大型应用时推荐使用 NPM管理 VUE)
NPM 能很好地和诸如 webpack 或 Browserify 模块打包器配合使用。
```
# 最新稳定版
$ npm install vue
```

再补充一点node和npm的关系：
node.js是javascript的一种运行环境，是对Google V8引擎进行的封装，是一个服务器端的javascript的解释器。
nodejs中含有npm，比如说你安装好nodejs，你打开cmd输入npm -v会发现npm的版本号，说明npm已经安装好。

npm和yarn命令：
```
NPM	YARN	说明
npm init	yarn init	初始化某个项目
npm install/link	yarn install/link	默认的安装依赖操作
npm install taco —save	yarn add taco	安装某个依赖，并且默认保存到package.
npm uninstall taco —save	yarn remove taco	移除某个依赖项目
npm install taco —save-dev	yarn add taco —dev	安装某个开发时依赖项目
npm update taco —save	yarn upgrade taco	更新某个依赖项目
npm install taco --global	yarn global add taco	安装某个全局依赖项目
npm publish/login/logout	yarn publish/login/logout	发布/登录/登出，一系列NPM Registry操作
npm run/test	yarn run/test	运行某个命令
```

### VUE实例
创建一个 Vue 实例
每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的：
```
var vm = new Vue({
  // 选项
})
```

### 第一个Hello World
*别问怎么学，问就是Hello World*

1.创建一个html文件
你可以创建一个 HelloWord.html 文件，然后通过如下方式引入 Vue：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
<div id="app">
    {{ message }}
</div>
</body>

<script>
    var app = new Vue({
        el: '#app',
        data: {
            message: 'Hello World!'
        }
    })
</script>
</html>
```
你可以在浏览器新标签页中打开它，至此，我们已经成功创建了第一个 Vue 应用

声明式渲染：
现在数据和 DOM（文档对象模型，HTML的文档document页面是一切的基础，没有它dom就无从谈起）已经被建立了关联，所有东西都是响应式的。
当修改message的值，界面也会相应更新。


### 使用vue-cli
vue-cli：脚手架构建工具,通过一行命令快速创建一个vue应用
安装：
```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

创建一个项目：
```
vue create hello-world
# OR
vue ui
```

Vue CLI 有几个独立的部分——如果你看到了我们的源代码，你会发现这个仓库里同时管理了多个单独发布的包。

*CLI*
CLI (@vue/cli) 是一个全局安装的 npm 包，提供了终端里的 vue 命令。它可以通过 vue create 快速搭建一个新项目，或者直接通过 vue serve 构建新想法的原型。你也可以通过 vue ui 通过一套图形化界面管理你的所有项目。  
  [CLI vue create命令详情]: https://cli.vuejs.org/zh/guide/creating-a-project.html

*CLI 服务*
CLI 服务 (@vue/cli-service) 是一个开发环境依赖。它是一个 npm 包，局部安装在每个 @vue/cli 创建的项目中。
CLI 服务是构建于 webpack（构建工具） 和 webpack-dev-server 之上的。它包含了：
* 加载其它 CLI 插件的核心服务；
* 一个针对绝大部分应用优化过的内部的 webpack 配置；
* 项目内部的 vue-cli-service 命令，提供 serve、build 和 inspect 命令。  
  [CLI服务详情]: https://cli.vuejs.org/zh/guide/cli-service.html

*CLI 插件*
CLI 插件是向你的 Vue 项目提供可选功能的 npm 包，例如 Babel/TypeScript 转译、ESLint 集成、单元测试和 end-to-end 测试等。Vue CLI 插件的名字以 @vue/cli-plugin- (内建插件) 或 vue-cli-plugin- (社区插件) 开头，非常容易使用。

当你在项目内部运行 vue-cli-service 命令时，它会自动解析并加载 package.json 中列出的所有 CLI 插件。

插件可以作为项目创建过程的一部分，或在后期加入到项目中。它们也可以被归成一组可复用的 preset。  
  [CLI插件详情]: https://cli.vuejs.org/zh/guide/plugins-and-presets.html

### Vue项目结构
使用vue-cli创建的项目结构会比较复杂
.
|-- build                            // 项目构建(webpack)相关代码
|   |-- build.js                     // 生产环境构建代码
|   |-- check-version.js             // 检查node、npm等版本
|   |-- dev-client.js                // 热重载相关
|   |-- dev-server.js                // 构建本地服务器
|   |-- utils.js                     // 构建工具相关
|   |-- webpack.base.conf.js         // webpack基础配置
|   |-- webpack.dev.conf.js          // webpack开发环境配置
|   |-- webpack.prod.conf.js         // webpack生产环境配置
|-- config                           // 项目开发环境配置
|   |-- dev.env.js                   // 开发环境变量
|   |-- index.js                     // 项目一些配置变量
|   |-- prod.env.js                  // 生产环境变量
|   |-- test.env.js                  // 测试环境变量
|-- dist：                           // 生产环境webpack编译打包输出目录，同样按照view、styles、js组织
|-- node_modules：                   // 所以使用的nodeJs模块
|-- src                              // 源码目录
|   |-- components                     // vue公共组件
|   |-- store                          // vuex的状态管理
|   |-- App.vue                        // 页面入口文件
|   |-- main.js                        // 程序入口文件，加载各种公共组件
|-- static                           // 静态文件，比如一些图片，json数据等
|   |-- data                           // 群聊分析得到的数据用于数据可视化
|-- .babelrc                         // ES6语法编译配置
|-- .editorconfig                    // 定义代码格式
|-- .gitignore                       // git上传需要忽略的文件格式
|-- README.md                        // 项目说明
|-- favicon.ico 
|-- index.html                       // 入口页面
|-- package.json                     // 项目基本信息


### Vue项目依赖
```
    "axios": "^0.17.1", //给予promise的HTTP请求客户端
//    "mock":"^0.1.1",  //生成随机数据，拦截 Ajax 请求
    "babel-polyfill": "^6.26.0",  //用于实现实现浏览器不支持原生功能的代码
    "element-ui": "^2.0.9", //前端组件库
    "js-md5": "^0.7.3", //MD5加密
    "jsencrypt": "^2.3.1",  // 加密工具
    "normalize.css": "^7.0.0",  //相对于CSS reset更加平和
    "vue": "^2.5.2",
    "vue-core-image-upload": "^2.3.10", //图片上传
    "vue-quill-editor": "^2.3.3", //富文本编辑器
    "vue-router": "^3.0.1", //vue路由
    "vuex": "^3.0.1"  //应用程序状态管理
```

### vue文件结构

说明：在任一个*.vue文件
```
<template>
    template标签里写html代码，且template直接子级只能有一个标签。
</template>
<script>
    script里面写js代码
</script>
<style scoped>
    style标签里写样式
</style>
```

### 运行和打包
vue打包
vue-cli-service build 会在 dist/ 目录产生一个可用于生产环境的包，带有 JS/CSS/HTML 的压缩，和为更好的缓存而做的自动的 vendor chunk splitting。
也可以通过npm run build来进行打包的操作

如何部署
将打包出来的资源，基于Vue-Cli的一般是dist目录下有static目录和index.html文件，可以直接将这两个文件扔到服务端，但有时候，我们会直接将dist文件扔到服务端






