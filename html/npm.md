# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## npm
npm: Nodejs下的包管理器。


### 常用命令
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


### npm修改仓库地址
npm仓库默认地址：/usr/local/lib/node_modules/npm/node_modules

npm config list：npm所有配置信息
npm config ls -l：npm所有配置信息
npm config get registry：查看镜像源

npm切换淘宝源：npm config set registry http://registry.npm.taobao.org
npm切换华为云：npm config set registry https://mirrors.huaweicloud.com/repository/npm/
npm恢复官方源：npm set registry https://registry.npmjs.org/


### node和npm的关系：
* Node环境：
    - node官网下载：http://nodejs.cn/download/
    - 查看node版本：node –v
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


### 使用package.json安装模块
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




