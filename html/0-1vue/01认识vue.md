# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 认识vue

### 环境
Vue.js 是什么
用官方的话解释说：Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。

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
一句话解释NPM：一个包管理工具，类似yarn、maven。(构建大型应用时推荐使用 NPM管理 VUE)
NPM 能很好地和诸如 webpack 或 Browserify 模块打包器配合使用。
```
# 最新稳定版
$ npm install vue
```

再补充一点node和npm的关系：
node.js是javascript的一种运行环境，是对Google V8引擎进行的封装。是一个服务器端的javascript的解释器。
nodejs中含有npm，比如说你安装好nodejs，你打开cmd输入npm -v会发现出啊线npm的版本号，说明npm已经安装好。


### 第一个Hello World
*别问怎么学，问就是Hello World*

，跟着例子学习一些基础用法。或者你也可以创建一个 .html 文件，然后通过如下方式引入 Vue：

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
当修改app.message的值，界面也会相应更新。

### 实例的生命周期
每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。

比如 created 钩子可以用来在一个实例被创建之后执行代码：
```
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

也有一些其它的钩子，在实例生命周期的不同阶段被调用，如init、created、 mounted、updated 和 destroyed。
生命周期钩子的 this 上下文指向调用它的 Vue 实例。