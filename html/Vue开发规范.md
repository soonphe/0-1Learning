# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## vue开发规范

### 一、页面文件目录规范
views 文件夹是用来存放pages组件的目录，其结构格式如下：
views
• bar （bar页面模块）
• index.vue （入口文件，强制约定命名成index）
• Module1.vue （bar 页面的子模块1）
• Module2.vue （bar 页面的子模块2）
• foo （foo页面模块）
• index.vue
• Module1.vue 
• Module2.vue 
注：命名规范如有争议，后面可以再讨论


### 二、单文件组件的命名、代码格式与引入方式
1、命名的方式
根据Vue 官方推荐的规范来，以大写驼峰的方式命
反：mycomponent.vue 正：MyComponent.vue

2、单文件组件标签的顺序
约定格式：
<template>
</template>
<script>
export default {
}
</script>
<style>
</style>
3、组件的引入
建议统一使用大写驼峰的形式，好处就是形式统一，方便代码快速定位。
反例：
<template>
    <title-bar></title-bar> 
</template>
<script>
import titleBar from 'components/TitleBar';
export default {
    components: {
        titleBar
    }
}
</script>
好例子：
<template>
    <TitleBar></TitleBar>   
</template>
<script>
import TitleBar from 'components/TitleBar';
export default {
    components: {
        TitleBar
    }
}
</script>
顺便可以将 TitleBar 组件的name属性也命名为"TitleBar" 方便全局代码查找。
再啰嗦一句，som-ui 给的实例代码基本都是以横杆作为组件的命名，可以做个转换。
<!--原来-->
<som-switch title="默认 false"></som-switch>
<!--转换后-->
<SomSwitch title="默认 false"></SomSwitch>


### 三、样式相关规范
1、设定全局公共的样式与默认的样式很重要，建议引入 'normalize.css' 磨平基础样式的在不同浏览器上的差异。
2、根据业务与设计规范，设置全局的样式，如下图所示
如：*{...}，a{...},li{...},input{...},.border-bottom{...}

3、使用mixin 提高样式的复用性
定义mixin.less

在业务组件中使用
@import '~style/mixin';
figcaption {
  .Height(48px);
  font-size: 14px;
  .centerBlock();
  .mLineEs(3);
}


### 四、静态资源的引入
1、路径书写规范
规定统一通过别名方式的路径引入，不推荐使用相对路径，如果是同一目录下可以接受。
反例：
<img src="../../assets/img/brand.png" />
.background-container {
    background-image: url(../../assets/img/brand.png);  
}
好例子（定义路径别名）：
<img src="~assets/img/brand.png" />
.background-container {
    background-image: url(~assets/img/brand.png);   
}
2、文件的格式
色值单一的图片文件，建议使用svg的格式。如果色彩比较丰富，就使用jpg格式，大小是视觉稿上显示的两倍。
改下vue.config 的配置 让它可以支持将svg格式的文件转为base64。
    chainWebpack: config => {
        const svgRule = config.module.rule('svg');
        svgRule.uses.clear();
        svgRule
            .use('url-loader')
            .loader('url-loader')
            .options({
                limit: 4096,
                fallback: {
                    loader: 'file-loader',
                    options: {
                        name: 'img/[name].[hash:8].[ext]'
                    }
                }
            });
    }


### 五、公共方法的使用
1、高频方法
推荐直接绑定在vue实例的原型上，方便全局直接调用，最常见的就是http的请求方法。
反例：
// app.js
import API from 'utils/api';
...
API(params).then(()=>{
    ....
})
好例子：
// main.js
Vue.prototype.$api = function(){
    ....
}
// app.js
this.$api(params).then(()=>{
    ....
})
2、非高频方法
建议使用模块的方式引入
// tools.js
export function throttle(){
    ....
}
export function timeFormat(){
    ....
}
//app.js
import {throttle, timeFormat} from 'utils/tools';
...
throttle();
timeFormat();


### 六、业务实践中的要点
1、API 请求代码自动生成
在项目中建议使用 vswagger 自动生成 api 请求代码，便于后期的更新与维护，详情请看使用手册。
2、图片懒加载
再图片数量多的情况下，建议使用引入 ossImage 组件来代替 传统的 img 标签，实现图片的懒加载
3、在不同环境中获取用户的token
考虑到大部分项目可能是依赖APP 环境去获取用户token，但是本地dev模式下是在浏览器环境下进行的，考虑到兼容两者之间的差异，我们的做法是如下：
• 在APP环境中，拿到用户的token，作为一个公共参数在所有的请求中带上这个值。
• 在浏览器环境中 通过 cookies 的方式携带用户信息，需要做两个准备
• 1、在本地浏览器完成用户登录
• 2、设置 ajax请求的配置项  withCredentials: true