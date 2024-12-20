# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## html-overview

### Ajax：
Ajax：“Asynchronous JavaScript and XML”，翻译过来就是异步JavaScript和XML。
要创建Ajax，主角是XMLHttpRequest（下简称XHR）对象。
```
第一步：创建XHR对象
var xhr = new XMLHttpRequest();

第二步：向服务器发送请求
方法：open(method,url,async) 和 send(string)
open()方法传入三参数
• method：请求的类型（GET/POST）
• url：文件在服务器上的位置
• async：布尔值，true表示异步，false表示同步（可选，默认为true）
1 xhr.open("GET","demo.asp?t=" + Math.random(),true);
2 xhr.send();

第三步：服务器响应
XMLHttpRequest对象的responseText和responseXML属性分别获得字符串形式的响应数据和XML形式的响应数据
还有三个关于响应状态的属性也经常用到：
• readyState：存有XMLHttpRequest的状态。XHR对象会经历5种不同的状态
○ 0：请求未初始化（new完后）；
○ 1：服务器连接已建立（对象已创建并初始化，尚未调用send方法）；
○ 2：请求已接收；
○ 3：请求处理中；
○ 4：请求已完成，响应就绪；
• status：（HTTP状态码很多，请自行了解，举例常见的）
○ 200：请求成功
○ 404：未找到页面
• onreadystatechange：存储函数（或函数名），每当readyState属性改变时，就会调用该函数。
1 xhr.onreadystatechange = function () {
2     if (xhr.readyState == 4 && xhr.status == 200) {
3     console.log(xhr.responseText);
4 };
```

### js定时器、延时器
在JavaScript中，可以使用setTimeout和setInterval来设置定时器。

setTimeout用于设置一个单次定时器，当定时器到期后，会执行一次指定的函数。
```
// 设置一个1000毫秒后执行的定时器
setTimeout(function() {
    console.log('Hello, World!');
}, 1000);
```

setInterval用于设置一个重复定时器，每隔一定时间间隔就执行一次指定的函数。
```
// 设置一个每2000毫秒执行一次的定时器
setInterval(function() {
    console.log('Hello, World!');
}, 2000);
```
要取消定时器，可以使用clearTimeout或clearInterval，传入相应的定时器标识。
```
// 设置一个定时器，然后取消它
var timerId = setTimeout(function() {
    console.log('This will not be logged.');
}, 1000);
 
clearTimeout(timerId);
 
// 设置一个重复定时器，然后在第二次执行之前取消它
var intervalId = setInterval(function() {
    console.log('Hello, World!');
    clearInterval(intervalId);
}, 2000);
```

### 模板字符串
JavaScript 模板字符串（template string）是一种字符串字面量，使用反引号（`）来标识。它可以包含变量，并且可以内嵌表达式。模板字符串可以包含换行符，而无需使用+运算符连接多行字符串。

模板字符串中可以嵌入变量或表达式，其写法是在${}内部写入JavaScript表达式。
```
//解法1：
let name = 'John';
let age = 25;
let greeting = `Hello, my name is ${name} and I am ${age} years old.`;
console.log(greeting);

//解法2：
let x = 10;
let y = 20;
let sum = `${x} + ${y} = ${x + y}`;
console.log(sum);

function multiply(a, b) {
  return `${a} * ${b} = ${a * b}`;
}
console.log(multiply(5, 6));
```

### 解构赋值
在 ES6 中，允许按照一定的模式，从数组和对象中提取值，对变量进行赋值，这种行为被称为解构（Destructuring）。
解构赋值的规则是，只要被解构的值（等号右边的值）不为对象或者数组（如字符串、数值、布尔值），就先将其转为对象。但 undefined 和 null 除外，因为它俩无法转为对象，所以进行解构赋值会报错。

只要某种数据结构具有 Iterator 接口（可遍历结构），都可以采用数组形式的解构赋值。JavaScript 中原生具备 Iterator 接口的数据结构如下：
- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

基础解构示例：
```
// 在 ES5 我们只能直接指定值
var a = 1 var b = 2 var c = 3
// 在 ES6 允许这样为变量赋值
let [a, b, c] = [1, 2, 3]
let [foo, [[bar], baz]] = [1, [[2], 3]]
let [ , , third] = ["foo", "bar", "baz"]
let [x, , y] = [1, 2, 3]
let [head, ...tail] = [1, 2, 3, 4]
let [x, y, ...z] = ['a']    // x="a" y=undefined
```
如果解构不成功，变量的值就等于 undefined
```
let [foo] = []  // foo=undefined
let [bar, foo] = [1]    //foo=undefined
```
当等号左边的模式，只匹配一部分等号右边的数组，依然可以解构成功，这种情况属于不完全解构。
```
let [x, y] = [1, 2, 3]; //x=1 y=2
let [a, [b], d] = [1, [2, 3], 4]; //a= 1 b=2 d=4
```


### Icon
1. img一个页面的请求资源中图片 img 占了大部分，所以为了优化有了image sprite 就是所谓的雪碧图，
   就是将多个图片合成一个图片，然后利用 css 的 background-position 定位显示不同的 icon 图标，但这个也有一个很大的痛点，维护困难
2. font 库，常见的如 Font Awesome ，使用起来也非常的方便，定制性也非常的不友善，图标库一共有675个图标
3. iconfont：几百个公司的开源图标库，还有各式各样的小图标，还支持自定义创建图标库

**iconfont 三种使用姿势**
```
1.unicode
第一步：引入自定义字体 `font-face 第二步：定义使用iconfont的样式 第三步：挑选相应图标并获取字体编码，应用于页面

2.font-class <i class="iconfont icon-xxx"></i>

3.symbol：svg-icon 使用形式慢慢成为主流和推荐
第一步 引入  ./iconfont.js  
引入  ./iconfont.js
第二步：加入通用css代码（引入一次就行）
<style type="text/css">
    .icon {
       width: 1em; height: 1em;
       vertical-align: -0.15em;
       fill: currentColor;
       overflow: hidden;
    }
</style>
第三步：挑选相应图标并获取类名，应用于页面：
<svg class="icon" aria-hidden="true">
<use xlink:href="#icon-xxx"></use>
</svg>

使用svg-sprite：引入 svg-sprite-loader
svgo：清除svg中多余的东西
```

### Content-Type几种值的区别及用法
- application/json
  - 告诉服务器请求的主题内容是json格式的字符串，服务器端会对json字符串进行解析  好处： 前端不需要关心数据结构的复杂度，只要是标准的json格式就能提交成功。

- application/x-www-form-urlencoded
  - 数据被编码为名称/值对。这是标准的编码格式，是post的默认格式，所有浏览器都支持。
  - 在请求发送过程中会对数据进行序列化处理，以键值对形式？key1=value1&key2=value2的方式发送到服务器
  - 使用js中URLencode转码方法。包括将name、value中的空格替换为加号；将非ascii字符做百分号编码；将input的name、value用‘=’连接，不同的input之间用‘&’连接。

- multipart/form-data
  - 需要在表单中进行文件上传时，就需要使用该格式。常见的媒体格式是上传文件之时使用的
  - 对于一段utf8编码的字节，用application/x-www-form-urlencoded传输其中的ascii字符没有问题，但对于非ascii字符传输效率就很低了（汉字‘丁’从三字节变成了九字节），因此在传很长的字节（如文件）时应用multipart/form-data格式。smtp等协议也使用或借鉴了此格式。
  - multipart/form-data将表单中的每个input转为了一个由boundary分割的小格式，没有转码，直接将utf8字节拼接到请求体中，在本地有多少字节实际就发送多少字节，极大提高了效率，适合传输长字节。

- text/plain
  - 数据以纯文本形式(text/json/xml/html)进行编码，其中不含任何控件或格式字符。

### 前端调试神器：vConsole和eruda

### TypeScript 是 JavaScript 的关系
TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript

### 组件介绍
- element-ui
  "element-ui": "2.7.0",

- Normalize.css：供了跨浏览器的高度一致性
  "normalize.css": "7.0.0",
  import 'normalize.css/normalize.css'

- font-awesome：矢量图标
  "font-awesome": "4.7.0",
  import 'font-awesome/css/font-awesome.min.css' // font-awesome

- nprogress：进度条
  "nprogress": "0.2.0",
  import 'nprogress/nprogress.css';
  使用：
```
import NProgress from 'nprogress' // Progress 进度条
import 'nprogress/nprogress.css'// Progress 进度条样式
router.beforeEach((to, from, next) => {
  NProgress.start()
  。。。
  next()
})
router.afterEach(() => {
  NProgress.done() // 结束Progress
})
```

- babel-polyfill：ES6转码ES5
  "babel-polyfill": "^6.26.0",
  import "babel-polyfill";


### 引入方式

- 完整引入
```
/*Element：完整引入*/
  import ElementUI from 'element-ui';
  import 'element-ui/lib/theme-chalk/index.css';
```
- 按需引入
```
/*icon字体路径变量*/
$--font-path: "~element-ui/lib/theme-chalk/fonts";
/*按需引入用到的组件的scss文件和基础scss文件*/
@import "~element-ui/packages/theme-chalk/src/base.scss";
/*按需引入组件*/
import {Rate,Row,Button} from 'element-ui'
```


### 路由Router跳转携带参数
```
this.$router.push({
    // 由于动态路由也是传递params的，所以在 this.$router.push() 方法中 path不能和params一起使用，否则params将无效。需要用name来指定页面
    // path: ({path: '/advert/add', params: {typeList: this.typeList}}) 错误
    // 通过路由名称跳转，携带参数（已成功）
    // name: 'advertAdd', params: {typeList: this.typeList}
});
```
接收：
```
          this.listQuery.clueId = this.$route.query.clueId;
          this.listQuery.cpid = this.$route.query.cpid;
```       
页面间传值：
```
    // path: '/clue/detail', query: { clue: row }	//query方式页面刷新不丢失
    path: '/clue/detail', query: { clueId: row.id, cpid: row.cpid } //但无法支持对象不丢失
```
页面跳转携带参数还可以使用vuex


### public/index.html 初始页
因为 index 文件被用作模板，所以你可以使用 lodash template 语法插入内容：
```
<%= VALUE %> 用来做不转义插值；
<%- VALUE %> 用来做 HTML 转义插值；
<% expression %> 用来描述 JavaScript 流程控制。
<link rel="icon" href="<%= BASE_URL %>favicon.ico">	//引用客户端环境变量
```

### 指令
v-bind——缩写：:，动态地绑定一个或多个特性，或一个组件 prop 到表达式。
```
<a v-bind:href="url" v-bind:class="klass">click me</a>
<a :class="{active:isActive}">click me</a> //动态判断是否加载class
<!-- prop 绑定。“prop”必须在 my-component 中声明。-->
<my-component :prop="someThing"></my-component>
v-on:——缩写：@，綁定事件
<!-- 阻止默认行为 -->
<button @click.prevent="doThis"></button>
@click.native.prevent="onSubmit"  //监听根组件，阻止默认行为
v-model：随表单控件类型不同而不同。
限制：
<input>
<select>
<textarea>
components
修饰符：
.lazy - 取代 input 监听 change 事件
.number - 输入字符串转为有效的数字
.trim - 输入首尾空格过滤
```
常用指令：v-text:v-html,v-show,v-if,v-else,v-for,v-slot,v-pre,v-cloak,v-once

### 特殊特性：
key：虚拟 DOM 算法
有相同父元素的子元素必须有独特的 key。重复的 key 会造成渲染错误。
最常见的用例是结合 v-for：
```
<ul>
  <li v-for="item in items" :key="item.id">...</li>
</ul>
```

### ref：给元素或子组件注册引用信息
引用信息将会注册在父组件的 $refs 对象上。
如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；
```
<!-- `vm.$refs.p` will be the DOM node -->
<p ref="p">hello</p>
```
如果用在子组件上，引用就指向组件实例：
```
<!-- `vm.$refs.child` will be the child component instance -->
<child-component ref="child"></child-component>
```

### is：用于动态组件且基于 DOM 内模板的限制来工作。
```
<!-- 当 `currentView` 改变时，组件也跟着改变 -->
<component v-bind:is="currentView"></component>
```

### 内置组件：
```
component：渲染一个“元组件”为动态组件
<!-- 动态组件由 vm 实例的属性值 `componentId` 控制 -->
<component :is="componentId"></component>
```

### transition：元素作为单个元素/组件的过渡效果
Props：name (CSS 过渡类名,会自动拓展),mode离开/进入的过渡时间序列，例 "out-in" 和 "in-out"
事件：before-enter，before-leave，before-appear，enter，leave，appear。。
```
<!-- 简单元素 -->
<transition>
  <div v-if="ok">toggled content</div>
</transition>
<!-- 动态组件 -->
<transition name="fade" mode="out-in" appear>
  <component :is="view"></component>
</transition>
  <transition @after-enter="transitionComplete"> //事件钩子
```

### transition-group：元素作为多个元素/组件的过渡效果
Props：tag - string，默认为 span，哪个属性应该被渲染
move-class - 覆盖移动过渡期间应用的 CSS 类。
除了 mode，其他特性和 `<transition>` 相同。
事件：事件和 `<transition>` 相同。

### keep-alive：主要用于保留组件状态或避免重新渲染。

### slot：插槽

### vue对路径@不识别问题：
webstorm配置webpack引用路径
```
node_modules\@vue\cli-service\webpack.config.js
```

### 组建注册与引用：components
```
Vue.component('命名'，{template,data等，注：这里自动使用new Vue(),所有省略了new}
)

在 Vue 里，一个组件本质上是一个拥有预定义选项的一个 Vue 实例。在 Vue 中注册组件很简单：
// 定义名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是个待办项</li>'
})
```

### 生命周期钩子

created（创建），mounted（挂载），updated（更新），destoryed（销毁）
例：
```
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
```
不要在选项属性或回调上使用箭头函数，

比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。
因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，
经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。


### VueX
两种方式可以操作存取：

- 存对象：
```
import { mapActions } from 'vuex'
  methods: {
    ...mapActions(['saveAdvert', 'saveAdvertType', 'clearAdvert']),

最后调用
this.saveAdvertType(this.list)

取对象：
import { mapState } from 'vuex'	//这里的mapState 只用于获取值
  computed: {
    ...mapState({
      form: state => state.advert.advert
      // typeList: state => state.Advert.advertType
    })
  },
```
推荐使用方式：
```
存对象：
this.$store.dispatch('advert/saveAdvert', this.list)
this.$store.dispatch('app/toggleSideBar')	//无参
this.$store.dispatch('advert/saveAdvertType',this.typeList)	//有参


取对象：mapGetters//这里可以理解为store的计算属性，访问：store.getters.getItems
  computed: {
    ...mapGetters([
      'permission_routes',
      'sidebar'
    ]),
```


### import Layout from '@/layout'
这里是“@”相当于“../” 

### 获取环境信息：
```
process.env.VUE_APP_API_HOST,
```

### registry源
```
npm下载源切换
//npm修改为淘宝源
npm config set registry https://registry.npm.taobao.org
// 验证是否成功
npm config get registry
```

### data () {  与  data：  的区别：
```
data：如果多次引用统一组件，data中的值只有一份，一次改变就都改变，
data（）使用返回值方法，每次调用都是新的返回值
```

### 锚点
```
@change="goAnchor"

跳跃：
goAnchor(){
this.$el.querySelector('#table'+this.tabPosition).scrollIntoView();
},
```

### filters（变换状态等）
```
组件内声明过滤器：
    filters: {
        statusFilter(status) {
            const statusMap = {
                published: 'success',
                draft: 'gray',
                deleted: 'danger'
            };
            return statusMap[status];
        }
    },
引用：
<el-table-column prop="state" label="状态" align="center" >
                <template slot-scope="scope">
                    <span>{{scope.row.state | statusFilter}}</span>
                </template>
</el-table-column>
注：
{{ msg | filter('arg1','arg2') }}
// msg对应函数中的第一个参数data，arg1为第二个参数，类推
```

### formatter（变换状态等）
```
method中定义：
        typeFormat(row, column) {
            if (row.state === 0) {
                return '未知';
            } else if (row.state === 1) {
                return '男';
            } else {
                return '女';
            }
        },
引用：
<el-table-column prop="state" label="状态" align="center" :formatter="typeFormat">
</el-table-column>
注：el-table-column中不用写template
```

if-else判断显示：
```
<span v-if="scope.row.state === 0">未审核</span>
<span v-else-if="scope.row.state === 1">审核未通过</span>
```

也可以使用foreach循环匹配
```
      this.typeList.forEach((item, index) => {
        console.log(row.delFlag + '___' + item.id)
        if (row.delFlag === item.id) {
          console.log('equals___' + item.name)
          return item.name
        }
      })
```

### timestamp转date
```
    filters: {
        formatDate: function (value) {
            if (value === 0) {
                return '';
            }
            let date = new Date(value);
            let y = date.getFullYear();
            let MM = date.getMonth() + 1;
            MM = MM < 10 ? ('0' + MM) : MM;
            let d = date.getDate();
            d = d < 10 ? ('0' + d) : d;
            let h = date.getHours();
            h = h < 10 ? ('0' + h) : h;
            let m = date.getMinutes();
            m = m < 10 ? ('0' + m) : m;
            let s = date.getSeconds();
            s = s < 10 ? ('0' + s) : s;
            return y + '-' + MM + '-' + d + ' ' + h + ':' + m + ':' + s;
        }
    },

调用：
<templateslot-scope="scope">
    {{scope.row.createdAt|formatDate}}
</template>
```

组件形式发布：
```
过滤器组件定义：
export const formatDateFilter = value => {
  if (value === 0) {
    return '';
  }
  const date = new Date(value);
  const y = date.getFullYear();
  let MM = date.getMonth() + 1;
  MM = MM < 10 ? ('0' + MM) : MM;
  let d = date.getDate();
  d = d < 10 ? ('0' + d) : d;
  let h = date.getHours();
  h = h < 10 ? ('0' + h) : h;
  let m = date.getMinutes();
  m = m < 10 ? ('0' + m) : m;
  let s = date.getSeconds();
  s = s < 10 ? ('0' + s) : s;
  return y + '-' + MM + '-' + d + ' ' + h + ':' + m + ':' + s;
};

引用：
import { formatDateFilter } from '@/filter/index';

export default {
  components: {
    followRecordAdd
  },
  filters: {
    formatDate: formatDateFilter
  },
```


### 封装axios直接请求
```
          axios({
            url: this.uploadAction + 'sysRole/add',
            method: 'POST',
            data: formData,
            params: parm
          }).then((result) => {
            this.loading = false
            this.$message.success('添加成功')
            this.$router.push({
              path: '/sysRole/index'
            })
          }).catch((err) => {
            this.loading = false
            this.$message({
              message: '添加失败!',
              info: 'warning'
            })
          })
```

### vue axios get请求传参：
示例：
- 带花括号`{token}`
  实际参数 `token: admin-token`
- 不带花括号`token`
  实际参数 `0:token`

### axios异步获取stream
```
axios({
  url: 'https://api',
  data: {
    prompt: 'a beautiful cat',
    action: 'generate'
  },
  headers: {
    'accept': 'application/x-ndjson',
    'content-type': 'application/json'
  },
  responseType: 'stream',
  method: 'POST',
  onDownloadProgress: progressEvent => {
     const response = progressEvent.target.response;
     const lines = response.split('\r\n').filter(line => !!line)
     const lastLine = lines[lines.length - 1]
     console.log(lastLine)
  }
}).then(({ data }) => Promise.resolve(data));
```


### 跨域获取cookie

domain表示的是cookie所在的域，默认为请求的地址，
如网址为www.test.net/test/test.aspx，那么domain默认为www.test.net。

而跨域访问，如域A为t1.test.com，域B为t2.test.com，
一般情况下，域A和域B对应的cookie是不能互相访问的
那么在域A生产一个令域A和域B都能访问的cookie就要将该cookie的domain设置为.test.com；

例：
线上地址：https://jzedu-recruit-betaa.djtest.cn/#/clue/list  //携带有cookie验证，domain为.djtest.cn
修改本机hosts：127.0.0.1  jzedu-recruit-stable.djtest.cn——将线上cookie验证指向本机后台服务
本机后台服务器启动
nginx配置80端口转发：本地真实服务地址：proxy_pass http://localhost:8080/;
最终访问线上地址：http://jzedu-recruit-stable.djtest.cn/#/clue/list
就能通过本机后台cookie验证

### Flex布局
Flet布局 语法（Flex 指定容器 —父元素）
注意：设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

```
<template>
  <div class="box">
    <div class="item"></div>
  </div>
</template>
<style scoped>
  .box{
    // 将box指定为Flex容器
    display: flex;
    // 行内元素当然也可以使用flex布局
    display: inline-flex;
    // Webkit 内核的浏览器，必须加上-webkit前缀。
    display: -webkit-flex;
  }
</style>
```

容器属性
- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content
```
<style scoped>
	// row（默认值）：主轴为水平方向，起点在左端   下图第三幅
	// row-reverse：主轴为水平方向，起点在右端    下图第四幅
	// column：主轴为垂直方向，起点在上沿         下图第二幅 
	// column-reverse：主轴为垂直方向，起点在下沿 下图第一幅
  .box {
    flex-direction: row | row-reverse | column | column-reverse;
  }
  	// 默认情况下，项目都排在一条线（又称"轴线"）上。flex-wrap属性定义，如果一条轴线排不下，如何换行
	// nowrap（默认）：不换行           下图第一幅
	// wrap：换行，第一行在上方         下图第二幅 
	// wrap-reverse：换行，第一行在下方 下图第三幅
  .box {
    flex-wrap: nowrap | wrap | wrap-reverse;
  }
   // flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap
    flex-flow: <flex-direction> || <flex-wrap>;
        // flex-start（默认值）：左对齐
    // flex-end：右对齐
    // center： 居中
    // space-between：两端对齐，项目之间的间隔都相等
    // space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍
    justify-content: flex-start | flex-end | center | space-between | space-around;
    
    // flex-start：交叉轴的起点对齐
    // flex-end：交叉轴的终点对齐
    // center：交叉轴的中点对齐
    // baseline: 项目的第一行文字的基线对齐
    // stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度
    align-items: flex-start | flex-end | center | baseline | stretch;
</style>
```
还有：语法（Flex 指定项目 —子元素）


### rem自适应布局
先来看一段代码：
```
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="renderer" content="webkit">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= webpackConfig.name %></title>
  </head>
```
meta为网页的元属性：
1. name项：常用的选项有Keywords(关键字) ，description(网站内容描述)，author(作者)，robots(机器人向导)等。
2. http-equiv项：可用于代替name项，常用的选项有Expires(期限)，Pragma(cache模式)，Refresh(刷新)，Set-Cookie(cookie设定)，Window-target(显示窗口的设定)，content-Type(显示字符集的设定)等。
3. content项：根据name项或http-equiv项的定义来决定此项填写什么样的字符串。

- width=device-width：网页宽度等于设备屏幕宽度
- initial-scale=1：页面初始缩放比例为1
- user-scalable=no：禁止用户进行缩放
- maximum-scale=1，minimum-scale=1：最大和最小的页面缩放比例

实现手机端自适应，就是可以让页面的元素字体、间距、宽高等属性的属性值可以随着手机屏幕尺寸的变化而变化

**em**
em：em的特点 : ① em的值并不是固定的; ② em始终会继承父级元素的字体大小。
```
body{
    font-size: 20px;
}
.one{
    font-size: 1.5em;——30px
}
.two{
    font-size: 0.5em;——15px
}
```

**rem：相对长度单位rem**
rem是CSS3新增的一个相对单位（root em，根em）。注：相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。
```
html{
    font-size: 20px;
}
.one{
    font-size: 1.5rem;——30px
}
.two{
    font-size: 0.5rem;——10px
}
```
<!-- rem布局，根据字体 -->
```
  <script info="text/javascript">
    (function(win,doc){
      change();
      function change(){
        doc.documentElement.style.fontSize = doc.documentElement.clientWidth *20/320+'px';
      }
      win.addEventListener('resize',change,false);
      win.addEventListener('orientationchange',change,false);  /* 这个是移动端设备横屏、竖屏转换时触发的事件处理函数 */
    })(window,document);
  </script>
```


### gulp和webpack
**webpack：一个模块打包工具（更适合单页面spa模块开发）**
Webpack更侧重于模块打包，把开发中的所有资源（图片、js文件、css文件等）看成模块。Webpack是通过loader（加载器）和plugins（插件）对资源进行处理的

**gulp：基于流格式的打包构建工具（更适合多页面模块mpa开发）**
gulp：前端自动化打包构建工具，基于流格式的打包构建工具
打包:把文件压缩,整合,移动,混淆

gulp的常见api：gulp.src()、gulp.dest()、gulp.task()、gulp.watch()、gulp.series()、gulp.parallel()

- gulp.src()方法： 创建一个流，用于从文件系统读取 Vinyl 对象。
- 语法：gulp.src(globs, [options])
  - globs参数是文件匹配模式(类似正则表达式)，用来匹配文件路径(包括文件名)，当然这里也可以直接指定某个具体的文件路径。当有多个匹配模式时，该参数可以为一个数组。
  - options为一个可选的参数对象，通常我们不需要用到
  注：BOMs(字节顺序标记)在 UTF-8 中没有任何作用，除非使用 removeBOM 选项禁用，否则 src() 将从读取的 UTF-8 文件中删除BOMs。

- gulp.dest()方法：创建一个用于将 Vinyl 对象写入到文件系统的流。
- 语法：gulp.dest(path[,options])
  - path为写入文件的路径
  - options为一个可选的参数对象，通常我们不需要用到

- gulp.task方法：用来定义任务
- 语法：gulp.task(name[, deps], fn)
  - name为任务名
  - deps 是当前定义的任务需要依赖的其他任务，为一个数组。当前定义的任务会在所有依赖的任务执行完毕后才开始执行。如果没有依赖，则可省略这个参数
  - fn 为任务函数，我们把任务要执行的代码都写在里面。该参数也是可选的。

- gulp.watch方法：用来监视文件的变化，当文件发生变化后，我们可以利用它来执行相应的任务，例如文件压缩等。
- 语法1：gulp.watch(glob[, opts], tasks)
  - glob 为要监视的文件匹配模式，规则和用法与gulp.src()方法中的glob相同
  - opts 为一个可选的配置对象，通常不需要用到
  - tasks 为文件变化后要执行的任务，为一个数组

- gulp.series方法：将任务函数和/或组合操作组合成更大的操作，这些操作将按顺序依次执行。
对于使用 series() 和 parallel() 组合操作的嵌套深度没有强制限制
语法1：gulp.series(…tasks)

- gulp.parallel方法：将任务功能和/或组合操作组合成同时执行的较大操作。
对于使用 series() 和 parallel() 进行嵌套组合的深度没有强制限制。
语法1：gulp.parallel(…tasks)


### js原型链
原型链由三个部分组成：构造函数、实例、原型对象
构造函数：由大写字母开头的函数。在创建构造过程中，js会自动为它添加prototype属性，值为拥有constructor属性原型对象，constructor属性值为该构造函数。只有函数才有prototye属性。
实例：由构造函数new(实例化）得到。
原型对象：构造函数会在创建的过程中自动创建。
```
function Person(){} // 构造函数
var person = new Person() // 实例对象
Person.prototype
```

### 浏览器指纹：
网站通过采集设备上多样的硬件和软件设置来构建数字指纹，如操作系统、用户代理、CPU 核心数量、语言、时区、cookies 启用情况、屏幕分辨率、插件使用、广告拦截器、字体等。这些数据形成独特数字指纹，浏览器能借此于数百万用户中精准识别，准确性达 90 到 99%。


### CF（cloudFlare反爬虫，403和429）
- 403：重定向错误，服务器拒绝请求
  * 原因分析：没有访问权限，这类原因可能由目标网站的防护措施改变导致，
  * 解决方法：建议升级爬虫策略，或者更换91HTTP优质的代理ip，运营商授权自建机房，低延迟高可用率。
- 429：429 Too Many Requests
  * 原因分析：1.请求过快，需要降低请求速率 2.目标平台有反采集机制，限制了爬虫的请求。
  * 解决方法：降低请求的速度，多ip和设备尝试
* 407：错误代码：407 Proxy Authentication Required
  * 原因分析：代理认证信息错误，该代理需要用户认证，
  * 解决方法：带上正确的用户认证头部
* 504：错误代码：504 Proxy Gateway TimeoutLink
  * 原因分析：1.代理ip线路问题，或正在切换中IP中，稍后再试即可；2.目标网站服务器问题，原本不可访问导致。
* 解决方法：先尝试多个ip或设备访问目标网站，如果其他ip线路正常访问，则通过更换其他ip即可；如果更换大量ip还是出现504，建议在不使用代理的情况下先检查目标网站是否可以访问。 若可以访问，则有可能是目标网站的防护措施所导致的，这时便需要升级爬虫策略了；如不能访问，则联系网站管理员处理。
  