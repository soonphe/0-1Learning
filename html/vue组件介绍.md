# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## vue相关组件介绍

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














