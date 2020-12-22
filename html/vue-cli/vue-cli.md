# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 认识vue

### 环境
node.js环境
npm和yarn一样 项目包管理工具(国内也可使用cnpm npm的淘宝镜像)
vue-cli脚手架构建工具
webpack 构建工具

### 项目创建

3. 全局安装 vue-cli
一般是要 sudo 权限的
$ npm install --global vue-cli@2.9

4. 创建一个基于 mpvue-quickstart 模板的新项目
新手一路回车选择默认就可以了
$ vue init mpvue/mpvue-quickstart my-project


### 项目运行和部署
* 打包运行：
基于Vue-Cli,通过npm run build来进行打包的操作
* 如何部署：
基于Vue-Cli的一般是dist目录下有static目录和index.html文件，可以直接将这两个文件扔到服务端
但有时候，我们会直接将dist文件扔到服务端


### NPM和YARN对比
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


### 项目架构图
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
|-- dist                             //生产环境webpack编译打包输出目录，同样按照view、styles、js组织
|-- node_modules                     //所有使用的nodeJs模块
|-- config                           // 项目开发环境配置
|   |-- dev.env.js                   // 开发环境变量
|   |-- index.js                     // 项目一些配置变量
|   |-- prod.env.js                  // 生产环境变量
|   |-- test.env.js                  // 测试环境变量
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
|-- webpack.config.js: 开发环境webpack配置
|-- webpack.production.config.js: 生产环境webpack配置


### 依赖说明
~~~~
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
~~~~

### 基础规则
在*.vue文件，
template标签里写html代码，且template直接子级只能有一个标签。
style标签里写样式，
script里面写js代码

如果需要增加组件那就在components文件下定义xx.vue文件并编写代码即可，
如果需要配置路由就要进行在index.js进行路由“路径”配置，还需要点击跳转就要用到<router-link></router-link>标签了。
至于后面的过滤器啊，事件啊，钩子函数，ajax等等之类的和vue1.0差不多