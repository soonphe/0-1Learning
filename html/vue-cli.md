# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## vue-cli

### 浏览器兼容：package.json —— browserslist ：指定目标浏览器的范围
Polyfill ：旧浏览器兼容新功能	babel：ES6》ES5

### HTML文件和静态资源：
public/index.html 文件是一个会被 html-webpack-plugin 处理的模板
<%= VALUE %> 用来做不转义插值；
<%- VALUE %> 用来做 HTML 转义插值；
<% expression %> 用来描述 JavaScript 流程控制。
不生成index.html： vue.config.js》chainWebpack: config => {
构建多页应用： vue.config.js》pages

### CSS相关：
预处理器： (Sass/Less/Stylus简洁-css)
Less基于javascript，是在客户端处理（也可以服务端开发环节使用，生成CSS部署）变量使用@
Sass是基于Ruby的，然后是在服务器端处理的，变量使用$

### mixin：混入	//var mixin = {...
混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。
***一个混入对象可以包含任意组件选项。***
当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。
当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”
***全局混入***：Vue.mixin({。。。

### webpack 相关
简单配置：vue.config.js 》 configureWebpack
链式操作：vue.config.js 》 chainWebpack

### 环境变量和模式
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略

development 模式用于 vue-cli-service serve
production 模式用于 vue-cli-service build 和 vue-cli-service test:e2e
test 模式用于 vue-cli-service test:unit

构建环境命令使用开发环境变量：
"dev-build": "vue-cli-service build --mode development",

只有以 VUE_APP_ 开头的变量会被 webpack.DefinePlugin 静态嵌入到客户端侧的包中
除了 VUE_APP_* 变量之外，在你的应用代码中始终可用的还有两个特殊的变量：
NODE_ENV - 会是 "development"、"production" 或 "test" 中的一个。具体的值取决于应用运行的模式。
BASE_URL - 会和 vue.config.js 中的 publicPath 选项相符，即你的应用会部署到的基础路径。


### 构建目标：应用（默认）/库

部署：
和后端一起部署：确保 Vue CLI 生成的构建文件在正确的位置，并遵循后端框架的发布方式
单独部署：将dist部署到文件服务器（确保正确的 publicPath）

本地预览：
dist 目录需要启动一个 HTTP 服务器来访问 
(除非你已经将 publicPath 配置为了一个相对的值)，
所以以 file:// 协议直接打开 dist/index.html 是不会工作的
本地预览：使用一个 Node.js 静态文件服务器，例如 serve：
npm install -g serve
\# -s 参数的意思是将其架设在 Single-Page Application 模式下
\# 这个模式会处理即将提到的路由问题
serve -s dist

