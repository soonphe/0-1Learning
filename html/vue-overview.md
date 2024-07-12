# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## html-overview

### node和npm的关系：
* Node环境：
    - node官网下载：http://nodejs.cn/download/
    - node.js是javascript的一种运行环境，是对Google V8引擎进行的封装，是一个服务器端的javascript的解释器。
    - nodejs中含有npm，比如说你安装好nodejs，你打开cmd输入npm -v会发现npm的版本号，说明npm已经安装好。

* npm(node package manager)包结构管理工具：
    - 官网：https://www.npmjs.com/
    - 组成：网站，注册表（registry），命令行工具 (CLI)

- webpack: 它主要的用途是通过CommonJS的语法把所有浏览器端需要发布的静态资源做相应的准备，比如资源的合并和打包。

### 工具介绍
* nvm nodejs管理工具
  - nvm全名node.js version management，顾名思义是一个nodejs的版本管理工具。 通过它可以安装和切换不同版本的nodejs
  - github地址： https://github.com/coreybutler/nvm-windows/releases
  - brew安装：brew install nvm
  - 配置文件：echo "source $(brew --prefix nvm)/nvm.sh" >> .bash_profile
  - 常用命令：显示nvm版本： nvm -v
  ```
  # 显示可以安装的所有nodejs版本
  nvm list available
  # 安装指定版本的nodejs
  nvm install <version>
  # 显示已安装版本列表
  nvm list
  # 使用指定版本node
  nvm use [version]
  # 卸载指定版本node
  nvm uninstall <version>
  ```

* node.js js运行环境
  - 官方解释：Node.js is an open-source, cross-platform JavaScript runtime environment. 翻译过来：Node.js是一个开源、跨平台的JavaScript运行时环境。
  - 官网地址：https://nodejs.org/en/

* Vite 前端构建工具
  - Vite是尤雨溪团队开发的，官方称是下一代新型前端构建工具，能够显著提升前端开发体验。 上面称是下一代，当前一代当然是我们熟悉的webpack
  - Vite 官网：https://cn.vitejs.dev/
  ```
  Vite 优势
  开发环境中，无需打包操作，可快速的冷启动。
  轻量快速的热重载（HMR）。
  真正的按需编译，不再等待整个应用编译完成。
  我们到官网 https://cn.vitejs.dev/guide/ 根据官网一步步往下走即可
  Vite 需要 Node.js 版本 18+ 或 20+。然而，有些模板需要依赖更高的 Node 版本才能正常运行，当你的包管理器发出警告时，请注意升级你的 Node 版本
  ```
  - 创建vite项目
  ```
  $ npm create vite@latest
  例如，要构建一个 Vite + Vue 项目，运行:
  # npm 7+, 需要额外加 --:
  npm create vite@latest my-vue-app -- --template vue
  ```
  - vite项目结构
  ```
  
  ```

* NRM镜像管理工具
  - nrm 全称是：（npm registry manager） 是npm的镜像管理工具 有时候国外的资源太慢，使用它就可以快速地在npm镜像源间快速切换
  - 安装命令：sudo npm install -g nrm
  ```
  # 查看镜像列表
  nrm ls
  # 查看当前使用的镜像
  nrm current 
  # 添加镜像
  nrm add <名称> <远程地址或私服地址>
  # 删除镜像
  nrm del <名称>
  # 切换镜像
  nrm use <名称> 
  # 测试镜像网络传输速度
  nrm test <名称>
  # 查看nrm版本号
  nrm <-version | -V> 
  # 查看nrm相关信息
  nrm <-help | -h>
  # 打开镜像主页
  nrm home <名称> [browser]
  # 上传npm包或命令程序
  nrm publish [<tarball>|<folder>]
  ```


### npm和yarn区别：
Yarn是由Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具 ，正如官方文档中写的，Yarn 是为了弥补 npm 的一些缺陷而出现的

Yarn的优点：速度快，安装版本统一，更简洁的输出，多注册来源处理，更好的语义化

使用区别：
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

pnpm:集成npm和yarn优点
```
$ pnpm install element-plus
```

### node常用命令
node -v               #查看node版本
npm install -g n      #n模块是专门用来管理nodejs的版本，安装n模块，无权限添加sudo
n stable // 把当前系统的 Node 更新成最新的 “稳定版本”
n lts // 长期支持版
n latest // 最新版
n 10.14.2 // 指定安装版本
sudo n 17.4.0 切换指定版本
n 命令行 查看/切换 你需要的node版本 上下切换 回车选择 q退出

当前长期稳定版：Node.js v21.7.3 npm 10.5.0

### npm常用命令
npm -v  #查看版本
npm install express     # 本地安装
npm install express -g  # 全局安装
npm ls                  #查看包安装信息
npm ls -g               #查看全局包安装信息
npm list -g             #查看全局包安装信息（ls=list）
npm list grunt          #查看某个模块的版本号
npm uninstall express   #卸载模块
npm update express      #更新模块
npm search express      #搜索模块
npm init                #生成package.json
npm adduser             #注册用户
npm publish             #创建模块
npm install -g npm   #更新最新版本，无权限添加sudo
npm install -g <package_name>@latest  #更新全局安装的npm包：
npm -g install npm@6.8.0  #更新到指定版本，运行指令，无权限添加sudo

npm cache clean --force   # 清除缓存
npm audit                 #允许开发人员分析复杂的代码，并查明特定的漏洞和缺陷
npm audit fix             #检测项目依赖中的漏洞并自动安装需要更新的有漏洞的依赖，而不必再自己进行跟踪和修复
npm audit fix --force     #强制更新
npm audit fix --only=prod #只更新dependencies中安装的包，跳过devDependencies中的包

### npm修改仓库地址
npm仓库默认地址：/usr/local/lib/node_modules/npm/node_modules

npm config list：npm所有配置信息
npm config ls -l：npm所有配置信息
npm config get registry：查看镜像源

npm切换淘宝源：npm config set registry http://registry.npm.taobao.org
npm切换华为云：npm config set registry https://mirrors.huaweicloud.com/repository/npm/
npm恢复官方源：npm set registry https://registry.npmjs.org/

npm临时切换下载源：npm install node-sass --registry=http://registry.npm.taobao.org

淘宝镜像原地址2024年1月22日已过期
切换新源：npm config set registry https://registry.npmmirror.com
如果执行完后依旧是报同样的报错，请依次执行以下两行命令
npm cache clean --force
npm config set strict-ssl false

其他镜像源
npm 官方原始镜像网址是：https://registry.npmjs.org/
淘宝最新 NPM 镜像：https://registry.npmmirror.com
阿里云 NPM 镜像：https://npm.aliyun.com
腾讯云 NPM 镜像：https://mirrors.cloud.tencent.com/npm/
华为云 NPM 镜像：https://mirrors.huaweicloud.com/repository/npm/
网易 NPM 镜像：https://mirrors.163.com/npm/
中科院大学开源镜像站：http://mirrors.ustc.edu.cn/
清华大学开源镜像站：https://mirrors.tuna.tsinghua.edu.cn/
腾讯，华为，阿里的镜像站基本上比较全

### npm install 安装报错解决思路：
1、删除  package-lock.json文件
2、npm cache clean --force
3、npm config rm proxy    npm config rm https-proxy
最后试试更换源：
npm set registry https://registry.npmjs.org/

其他修复办法：
1. npm audit fix
   npm audit fix --force
   npm audit
2. 删除已经安装的：node_modules 和 package-lock.json（可行）
   修改 package.json 格式如下
   npm audit fix --force
   npm install

### node多环境管理
node有问题的，我的建议是装个nvs https://github.com/jasongin/nvs

node版本管理 n和nvm说明：一个是组件，一个是外置

nvm安装：brew install nvm
```
创建nvm文件夹
mkdir ~/.nvm

添加环境变量
export NVM_DIR="$HOME/.nvm"
  [ -s "/usr/local/opt/nvm/nvm.sh" ] && . "/usr/local/opt/nvm/nvm.sh"  # This loads nvm
  [ -s "/usr/local/opt/nvm/etc/bash_completion.d/nvm" ] && . "/usr/local/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion
```
nvm命令
- nvm root：查看nvm的安装根路径在那个文件夹
- 修改镜像源：/usr/local/Cellar/nvm/0.38.0找到setting.txt
    - node_mirror: https://npm.taobao.org/mirrors/node/
    - npm_mirror: https://npm.taobao.org/mirrors/npm/
- nvm install <version>：安装指定版本的 Node.js。
- nvm use <version>：切换到指定版本的 Node.js。
- nvm ls：列出已安装的所有 Node.js 版本。
- nvm alias <name> <version>：给指定版本创建别名。
- nvm run <version> <script>：在指定版本下运行脚本。
- nvm current：显示当前正在使用的 Node.js 版本。
- nvm uninstall <version>：卸载指定版本的 Node.js。


###  package.json

#### 使用package.json安装模块
每个项目的根目录下面，一般都有一个package.json文件，定义了这个项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）。
npm install命令根据这个配置文件，自动下载所需的模块，也就是配置项目所需的运行和开发环境。

Package.json 属性说明
```
name - 包名。
version - 包的版本号。
description - 包的描述。
homepage - 包的官网 url 。
author - 包的作者姓名。
contributors - 包的其他贡献者姓名。
dependencies - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下。
repository - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上。
main - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。
keywords - 关键字
```

#### package.json中 ^ 和 ~ 的区别
指定版本号
(1)普通版本号: 表示安装此版本,比如"classnames": "2.2.5"，表示安装2.2.5的版本
(2)表示安装大版本的最小最新子版本: ~版本,比如 "babel-plugin-import": "~1.1.0",表示安装1.1.x的最新版本（不低于1.1.0），但是不安装1.2.x，也就是说安装时不改变大版本号和次要版本号
(3)表示安装大版本的最高中版本: ^版本,比如 "antd": "^3.1.4",，表示安装3.1.4及以上的版本，但是不安装4.0.0，也就是说安装时不改变大版本号。

#### package.json中 devDependencies和dependencies区别
- devDependencies用于本地环境开发时候。
  devDependencies用于本地环境开发时候，所以，所有的不会在发布时候打包进线上代码的npm包都放在这里，命令是：npm i -D ***。
  比如像这些包：babel-core、babel-eslint、等babel系列，autoprefixer、webpack、webpack-dev-server、koa、*-loaderloader系列等等
- dependencies用户发布环境
  用户发布环境，所以，不会包含本地开发任何的包,比如：react、react-redux、react-router-dom等

#### node-sass安装失败：
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/

#### 浏览器兼容：
package.json —— browserslist ：指定目标浏览器的范围
Polyfill ：旧浏览器兼容新功能	babel：ES6》ES5

#### HTML文件和静态资源：
public/index.html 文件是一个会被 html-webpack-plugin 处理的模板
<%= VALUE %> 用来做不转义插值；
<%- VALUE %> 用来做 HTML 转义插值；
<% expression %> 用来描述 JavaScript 流程控制。
不生成index.html： vue.config.js》chainWebpack: config => {
构建多页应用： vue.config.js》pages

#### CSS相关：
预处理器： (Sass/Less/Stylus简洁-css)
Less基于javascript，是在客户端处理（也可以服务端开发环节使用，生成CSS部署）变量使用@
Sass是基于Ruby的，然后是在服务器端处理的，变量使用$

#### mixin：混入	//var mixin = {...
混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。
***一个混入对象可以包含任意组件选项。***
当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。
当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”
***全局混入***：Vue.mixin({。。。

#### webpack 相关
简单配置：vue.config.js 》 configureWebpack
链式操作：vue.config.js 》 chainWebpack

#### 环境变量和模式
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

### prop和model的区别
prop(表单域model字段，即传入form组件的model中的字段)和model(表单数据对象)
```
<el-form ref="form" :model="form" label-width="80px">	//:model引用上层传入的form对象，同时真正被提交的form表单
//$refs 是所有注册过的ref的一个集合，
//ref="form"用来给元素或子组件引用注册信息，引用信息将会注册在父组件的$refs对象上，
//this.$refs.form指的就是这个form表单
  <el-form-item prop="name" label="活动名称">	//prop：在使用 validate、resetFields 方法的情况下，该属性是必填的
    <el-input v-model="form.name"></el-input>	//引用form对象的name字段
  </el-form-item>
```

value和 :value
:model和v-model的区别： :model是v-bind:model的缩写,
<child :model="msg"></child>这种只是将父组件的数据传递到了子组件，并没有实现子组件和父组件数据的双向绑定


### vue定时任务
js中定时器有两种，一个是循环执行setInterval，另一个是定时执行setTimeout

一、 循环执行（setInterval）
顾名思义，循环执行就是设置一个时间间隔，每过一段时间都会执行一次这个方法,直到这个定时器被销毁掉

用法是setInterval（“方法名或方法”，“延时”）， 第一个参数为方法名或者方法，注意为方法名的时候不要加括号,第二个参数为时间间隔

```
<template>
  <section>
    <h1>hello world~</h1>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        timer: '',
        value: 0
      };
    },
    methods: {
      get() {
        this.value ++;
        console.log(this.value);
      }
    },
    mounted() {
      this.timer = setInterval(this.get, 1000);
    },
    beforeDestroy() {
      clearInterval(this.timer);
    }
  };
</script>
```

二、 定时执行 （setTimeout）

定时执行setTimeout是设置一个时间，等待时间到达的时候只执行一次，但是执行完以后定时器还在，只是没有运行

用法是setTimeout(“方法名或方法”, “延时”); 第一个参数为方法名或者方法，注意为方法名的时候不要加括号,第二个参数为时间间隔

```
<template>
  <section>
    <h1>hello world~</h1>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        timer: '',
        value: 0
      };
    },
    methods: {
      get() {
        this.value ++;
        console.log(this.value);
      }
    },
    mounted() {
      this.timer = setTimeout(this.get, 1000);
    },
    beforeDestroy() {
      clearTimeout(this.timer);
    }
  };
</script>
```

### promise 和 async/await 区别
promise的用法
Promise,简单来说就是一个容器，里面保存着某个未来才会结束的时间(通常是一个异步操作的结果)
```
 let p = new Promise((resolve,reject) => {
        //...
        resolve('success')
    });
    
    p.then(result => {
        console.log(result);//success
    });
```
promise共有三个状态
pending（执行中）、success（成功）、rejected（失败）

链式调用
错误捕获
Promise.prototype.catch用于指定Promise状态变为rejected时的回调函数，可以认为是.then的简写形势，返回值跟.then一样
```
let p = new Promise((resolve,reject) => {
  reject('error');
});

p.catch(result => {
  console.log(result);
})
```

async、await
简洁：异步编程的最高境界就是不关心它是否是异步。async、await很好的解决了这一点，将异步强行转换为同步处理。
async/await与promise不存在谁代替谁的说法，因为async/await是寄生于Promise，Generater的语法糖。

用法
async用于申明一个function是异步的，而await可以认为是async wait的简写，等待一个异步方法执行完成。
规则：
1 async和await是配对使用的，await存在于async的内部。否则会报错
2 await表示在这里等待一个promise返回，再接下来执行
3 await后面跟着的应该是一个promise对象，（也可以不是，如果不是接下来也没什么意义了…）

写法：
```
async function demo() {undefined
  let result01 = await sleep(100);
  //上一个await执行之后才会执行下一句
  let result02 = await sleep(result01 + 100);
  let result03 = await sleep(result02 + 100);
  // console.log(result03);
  return result03;
}

demo().then(result => {
  console.log(result);
});
```

错误捕获
如果是reject状态，可以用try-catch捕捉
```
let p = new Promise((resolve,reject) => {
  setTimeout(() => {
    reject('error');
  },1000);
});

async function demo(params) {
  try {
    let result = await p;
  }catch(e) {
    console.log(e);
  }
}

demo();
```
区别：
1. promise是ES6，async/await是ES7
2. async/await相对于promise来讲，写法更加优雅
3. reject状态：
   1）promise错误可以通过catch来捕捉，建议尾部捕获错误，
   2）async/await既可以用.then又可以用try-catch捕捉

### 数据绑定错误(应使用v-bind，或缩写:src等)
在vue2.0中，src实现数据绑定稍不留神就不成功。假定value.src是绑定的数据。
常见错误写法1：
<img src="value.src">
错误之处在于：
1.属性值数据绑定应该用v-bind，应该写成v-bind:src=""
2.直接用引号"value.src"会报错，取不到值。

常见错误写法2:
<img src="{{value.src}}">
常见错误写法3：
<img src="{value.src}">

以上均会报错。个人亲测的2种正确写法：
<img :src=" 'files/'+value.src ">
<img :src="value.src">

img属于HTML标签，image属于服务器控件，大部分时候两者没有多大的区别，可以交替使用，但是我们一般使用image
Image控件能解决一个棘手的问题。因为Img标签不能识别～这个符号，通常这个符号都是代表网站根目录，img标签只能使用相对路径引用图片

第一种，只引入单个图片，这种引入方法在异步中引入则会报错。 比如需要遍历出很多图片展示时
<image :src = require('图片的路径') />

第二种，可引入多个图片，也可引入单个图片。 vuelic3版本没有static文件夹。可将静态图片存放到public目录下，直接引入即可
<image src="/public/image/logo.png"/>

第三种：使用相对路径形式@/assets/icon/group_4.png
```
<!--    <img class="card-panel-icon" src="@/assets/icon/group_4.png" alt="404" style="width: 48px">-->

    <img class="card-panel-icon" :src=imgSrc(item.icon) alt="404" style="width: 48px">

    imgSrc: function(index) {
      return require('@/assets/icon/' + (index) + '.png')
    },
使用相对路径形式@/assets/icon/group_4.png
```

### sass和less区别
less客户端编译，需要编译时间
引入less：
<link rel="stylesheet/less" type="text/css" href="styles.less">
<script src="less.js" type="text/javascript"></script>
调用less样式表时，link标签的rel属性一定是“stylesheet/less”，其中“/less”不可缺少；
less.js脚本只能放在加载less样式的link后面，才能生效。

sass服务端处理
LESS和Sass中的变量的唯一区别就是，LESS使用@，而Sass使用$

### Vue页面间传值和组件通信 

#### 1.vuex传值——index界面使用mapActions存，add界面使用mapState取值
    methods: {
      ...mapActions([ 'saveAdvert', 'clearAdvert']),
某方法中引用this.saveAdvert(row)

    computed: {	//计算属性基于数据之上的数据（有缓存），不同于watch在数据变化时做一些事情
      ...mapState({
        form: state => state.Advert.advert,
      })
    },


#### 2.路由传值——query/params（弊端：不是路由跳转的界面无法使用）
注：实用params去传值的时候，在页面刷新时，参数会消失，用query则不会有这个问题。
```
this.$router.push({
/**
* 页面间传值 ①使用路由带参数 ②使用vuex
*/
path: '/advertSponsor/add'
// 由于动态路由也是传递params的，所以在 this.$router.push() 方法中 path不能和params一起使用，否则params将无效。需要用name来指定页面
// path: ({path: '/advert/add', params: {typeList: this.typeList}}) 错误
// 通过路由名称跳转，携带参数（已成功）
// name: 'advertAdd', params: {typeList: this.typeList}
})
```

#### 3.通过$parent,$chlidren等方法调取用层级关系的组件内的数据和方法（弊端：耦合性太高）
this.$parent.$data.id  //获取父元素data中的id
this.$children.$data.id  //获取父元素data中的id

#### 4.使用eventbus
全局定义：window.eventBus = new Vue();
发送数据：eventBus.$emit('eventBusName', id);
用on接受该值（或对象）：eventBus.$on('eventBusName', function(val) { 　　console.log(val)})
关闭eventbus：eventBus.$off('eventBusName');

#### 父组件向子组件传递数据
在vue中，父组件往子组件传递参数都是通过属性props的形式来传递的
```
父组件：
<parent>
<child :child-com="content"></child>
</parent>

子组件：
第一种方法
props: ['childCom']
第二种方法
props: {
childCom: String //这里指定了字符串类型，如果类型不一致会警告的哦
}
第三种方法
props: {
childCom: {
type: String,
default: 'sichaoyun'
}
}
```

示例：
```

父组件：<child :inputName="name">
子组件
  props: {
    inputName: { //注意这里用驼峰写法哦
      type: String,
      default: 'chart'
    }
  }
```

#### 子组件向父组件传递数据
子组件向父组件传值$emit
```
子组件
<template>
<div @click="open"></div>
</template>
methods: {
open() {
this.$emit('showbox','the msg'); //触发showbox方法，'the msg'为向父组件传递的数据
}
}
父组件——这里用v-on:showbox / @showbox都行
<child @showbox="toshow" :msg="msg"></child> //监听子组件触发的showbox事件,然后调用toshow方法
methods: {
toshow(msg) {
this.msg = msg;
}
}
```

#### 兄弟组件通信：
我们可以实例化一个vue实例，相当于一个第三方
```
**main.js**
let vm = new Vue(); //创建一个新实例/也可以称之为事件中心
组件他哥
<div @click="ge"></div>
methods: {
    ge() {
        vm.$emit('blur','sichaoyun'); //触发事件
    }
}
组件小弟接受大哥命令
<div></div>
created() {
  vm.$on('blur', (arg) => { 
        this.test= arg; // 接收
    });
}
```

### vswagger接口自动化

vswagger是一个基于 swagger 快速生成 API 调用文件的命令行工具, 主要功能将接口同步到本地文件
• vswagger是一个基于 swagger 快速生成 API 调用文件的命令行工具
• 支持多分支、多模块接口管理
• 支持冗余接口清理
• 支持接口丢失提示
npm install -g vswagger-cli


添加根目录配置文件 .vswagger.js
/**
* .vswagger 配置文件
  */
  module.exports = {
  template: 'souche-finance#v2', // 可为空使用默认接口生成模板
  safe: false, // 是否生成保护数据(需要后台支持)
  output: "src/api", // 输出到api目录
  projectDir: "src", // 代码存放目录(可不配置默认为src路径)
  suffix: [".js",".vue"], // 指定过滤的文件(可不配置，默认.js,.vue文件)
  projects: [{
  domain: '',  // 环境变量
  token: '值', // swagger令牌
  modelName: "demo1", // 模块化名称
  docUrl: ['api-docs', 'api-docs', 'api-docs', 'api-docs']  // swagger base-url
  }, {
  domain: '',  // 环境变量
  token: '值', // swagger令牌
  modelName: 'demo2',
  docUrl: ['api-docs'] // 多个
  }] // 项目配置
  };
  // api-docs 是指swagger接口文档最下面的BASE URL链接
  // token 是指在需要登录后才能访问的swager文档字符串(_security_token)

可直接运行案例附上:
/**
* .vswagger.js 配置文件
  */
  module.exports = {
  template: 'souche-finance#v2', // 可为空使用默认接口生成模板
  safe: false, // 是否生成保护数据(需要后台支持)
  output: "src/api", // 输出到api目录
  projectDir: "src", // 代码存放目录(可不配置默认为src路径)
  projects: [{
  domain: 'LEASECONSUMER',  // 环境变量
  token: '', // swagger令牌
  modelName: "leaseconsumer", // 模块化名称
  docUrl: ['http://leaseconsumer-a.sqaproxy.dasouche.net/api-docs']  // swagger base-url
  }, {
  domain: 'FMC',  // 环境变量
  token: '', // swagger令牌
  modelName: 'fmc',
  docUrl: ['http://fmc.sqaproxy.dasouche-inc.net/api-docs'] // 多个
  }] // 项目配置
  };
  vswagger init 项目目录(.vswagger.js目录) 模块名称(a模块,b模块,c模块)
  例: vswagger init ./ demo1,demo2


直接上图看效果
index.js 文件是接口存放文件 instance.js 文件是配置 开发/预发/线上 接口访问的域名 util.js 文件是工具方法

### 关于vue中的ref
ref 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 $refs 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例：
组件元素只能通过 ref 获取的标签方法、变量、....  ；
普通的元素标签可以使用 ref或者id或者... 获取标签 ;
使用方法：this.$refs.ref名称
```
<template>  
<Student ref="studentEl"></Student>
</template>
<script>
    const studentByRef = this.$refs.studentEl;
    studentByRef.showStudentName()
</script>
```

### vue element菜单无限嵌套
使用siderbar-item属性引入 :item="child"
也可以自定义子菜单组件
```
<sidebar-item
  v-for="child in item.subs"
  :key="child.id"
  :is-nest="true"
  :item="child"
  :base-path="resolvePath(child.url.substring(6) + '/index')"
  :name="child.name"
  class="nest-menu"
/>
```

### vue左侧菜单滑动不流畅问题
overflow-y: auto;
max-height: 100%;

