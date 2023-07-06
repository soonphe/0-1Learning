# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## node & npm & yarn
- npm: Nodejs下的包管理器。
- node：基于 Chrome V8 引擎的 JavaScript 运行时;

### node和npm的关系：
* Node环境：
  - node官网下载：http://nodejs.cn/download/
  - node.js是javascript的一种运行环境，是对Google V8引擎进行的封装，是一个服务器端的javascript的解释器。
  - nodejs中含有npm，比如说你安装好nodejs，你打开cmd输入npm -v会发现npm的版本号，说明npm已经安装好。

* npm(node package manager)包结构管理工具：
  - 官网：https://www.npmjs.com/
  - 组成：网站，注册表（registry），命令行工具 (CLI)

- webpack: 它主要的用途是通过CommonJS的语法把所有浏览器端需要发布的静态资源做相应的准备，比如资源的合并和打包。


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

### node常用命令
node -v               #查看node版本
npm install -g n      #n模块是专门用来管理nodejs的版本，安装n模块，无权限添加sudo
n stable // 把当前系统的 Node 更新成最新的 “稳定版本”
n lts // 长期支持版
n latest // 最新版
n 10.14.2 // 指定安装版本
sudo n 17.4.0 切换指定版本
n 命令行 查看/切换 你需要的node版本 上下切换 回车选择 q退出


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

### 其他常见问题

#### node-sass安装失败：
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/


