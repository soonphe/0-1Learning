# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## html总结

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
this.$router.push({
    // 由于动态路由也是传递params的，所以在 this.$router.push() 方法中 path不能和params一起使用，否则params将无效。需要用name来指定页面
    // path: ({path: '/advert/add', params: {typeList: this.typeList}}) 错误
    // 通过路由名称跳转，携带参数（已成功）
    // name: 'advertAdd', params: {typeList: this.typeList}
});

接收：
          this.listQuery.clueId = this.$route.query.clueId;
          this.listQuery.cpid = this.$route.query.cpid;
          
页面间传值：
    // path: '/clue/detail', query: { clue: row }	//query方式页面刷新不丢失
    path: '/clue/detail', query: { clueId: row.id, cpid: row.cpid } //但无法支持对象不丢失
页面跳转携带参数还可以使用vuex


### public/index.html 初始页
插值：
因为 index 文件被用作模板，所以你可以使用 lodash template 语法插入内容：
<%= VALUE %> 用来做不转义插值；
<%- VALUE %> 用来做 HTML 转义插值；
<% expression %> 用来描述 JavaScript 流程控制。
<link rel="icon" href="<%= BASE_URL %>favicon.ico">	//引用客户端环境变量


### 指令
v-bind——缩写：:，动态地绑定一个或多个特性，或一个组件 prop 到表达式。
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
常用指令：v-text:v-html,v-show,v-if,v-else,v-for,v-slot,v-pre,v-cloak,v-once

### 特殊特性：
key：虚拟 DOM 算法
有相同父元素的子元素必须有独特的 key。重复的 key 会造成渲染错误。
最常见的用例是结合 v-for：
<ul>
  <li v-for="item in items" :key="item.id">...</li>
</ul>

### ref：给元素或子组件注册引用信息
引用信息将会注册在父组件的 $refs 对象上。
如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；
<!-- `vm.$refs.p` will be the DOM node -->
<p ref="p">hello</p>
如果用在子组件上，引用就指向组件实例：
<!-- `vm.$refs.child` will be the child component instance -->
<child-component ref="child"></child-component>

### is：用于动态组件且基于 DOM 内模板的限制来工作。
<!-- 当 `currentView` 改变时，组件也跟着改变 -->
<component v-bind:is="currentView"></component>

### 内置组件：
component：渲染一个“元组件”为动态组件
<!-- 动态组件由 vm 实例的属性值 `componentId` 控制 -->
<component :is="componentId"></component>

### transition：元素作为单个元素/组件的过渡效果
Props：name (CSS 过渡类名,会自动拓展),mode离开/进入的过渡时间序列，例 "out-in" 和 "in-out"
事件：before-enter，before-leave，before-appear，enter，leave，appear。。
<!-- 简单元素 -->
<transition>
  <div v-if="ok">toggled content</div>
</transition>
<!-- 动态组件 -->
<transition name="fade" mode="out-in" appear>
  <component :is="view"></component>
</transition>
  <transition @after-enter="transitionComplete"> //事件钩子

### transition-group：元素作为多个元素/组件的过渡效果
Props：tag - string，默认为 span，哪个属性应该被渲染
move-class - 覆盖移动过渡期间应用的 CSS 类。
除了 mode，其他特性和 <transition> 相同。
事件：事件和 <transition> 相同。

### keep-alive：主要用于保留组件状态或避免重新渲染。

### slot：插槽

### vue对路径@不识别问题：
webstorm配置webpack引用路径
node_modules\@vue\cli-service\webpack.config.js


### 组建注册与引用：components
Vue.component('命名'，{template,data等，注：这里自动使用new Vue(),所有省略了new}
)

在 Vue 里，一个组件本质上是一个拥有预定义选项的一个 Vue 实例。在 Vue 中注册组件很简单：
// 定义名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是个待办项</li>'
})

### 生命周期钩子
craeted（创建），mounted（挂载），updated（更新），destoryed（销毁）
例：
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
不要在选项属性或回调上使用箭头函数，
比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。
因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，
经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。


### VueX
两种方式可以操作存取：

存对象：
import { mapActions } from 'vuex'
  methods: {
    ...mapActions(['saveAdvert', 'saveAdvertType', 'clearAdvert']),
最后调用this.saveAdvertType(this.list)

取对象：
import { mapState } from 'vuex'	//这里的mapState 只用于获取值
  computed: {
    ...mapState({
      form: state => state.advert.advert
      // typeList: state => state.Advert.advertType
    })
  },

推荐使用方式：
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



### import Layout from '@/layout'
这里是“@”相当于“../” 

### 获取环境信息：
process.env.VUE_APP_API_HOST,

### registry源
npm下载源切换
//npm修改为淘宝源
npm config set registry https://registry.npm.taobao.org
// 验证是否成功
npm config get registry

### data () {  与  data：  的区别：
data：如果多次引用统一组件，data中的值只有一份，一次改变就都改变，
data（）使用返回值方法，每次调用都是新的返回值

### 锚点
@change="goAnchor"

跳跃：
goAnchor(){
this.$el.querySelector('#table'+this.tabPosition).scrollIntoView();
},


### filters（变换状态等）
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


### formatter（变换状态等）
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


if-else判断显示：
<span v-if="scope.row.state === 0">未审核</span>
<span v-else-if="scope.row.state === 1">审核未通过</span>

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

