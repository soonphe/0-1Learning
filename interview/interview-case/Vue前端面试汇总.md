# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## interview

1.vue生命周期及其特点 ?  
vue实例从被创建到销毁的一系列过程就叫vue生命周期. 也就是从开始创建、初始化数据、编译模版、挂载DOM→渲染、更新、渲染、卸载等一系列过程。
2.Vue的双向绑定原理是什么?
发布者-订阅者模式（backbone.js）
脏值检查（angular.js） 
数据劫持（vue.js）

3.父子组件传递数据的方法
父组件的数据要通过prop传到子组件

4.子组件之间如何共享数据
Vuex
5..axios的特点有哪些
1.	axios是一个基于promise的HTTP库,支持promise的所有API
2.	它可以拦截请求和响应
3.	它可以转换请求数据和响应数据,并对响应回来的内容自动转换为json类型的数据
4.	它安全性更高,客户端支持防御XSRF

5.axiosd有哪些常用方法
一、axios.get(url[, config])   //get请求用于列表和信息查询
二、axios.delete(url[, config])  //删除
三、axios.post(url[, data[, config]])  //post请求用于信息的添加
四、axios.put(url[, data[, config]])  //更新操作

6.谈谈javascript数组排序方法sort()的使用,重点介绍参数使用及内部机制?
语法：arrayObject.sort(sortby)
参数sortby可选，规定排序顺序，必须是函数
注：如果调用该方法是没有使用参数，将按字符编码的顺序进行排序，要实现这一点，首先应把数组的元素都转换成字符串，以便进行比较。
如果想按照其他的标准进行排序，就需要两个比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对排序的数字。比较函数应该具有两个参数a和b，其返回值如下：
若a<b，则返回一个小于0的值
若a=b，则返回一个0
若a>b，则返回一个大于0的值

7.简述DIV元素和span元素的区别
div是一个块级元素，span是内嵌元素。块元素相当于内嵌元素在前后各加了一个换行。其实，块元素和行内元素也不是一成不变的，只要给块元素定义display：inline，块元素就变成了内嵌元素，同样的，给内嵌元素定义了display：block就变成了块元素了。

8.说几条XHTML规范的内容(至少3条)
1.	所有的标记都必须有一个相应的结束标记
2.	所有标签的元素和属性的名字都必须使用小写
3.	所有的xml标记都必须合理嵌套
4.	所有的属性值都必须用引号“”括起来
5.	所有的<和&特殊符号用编码表示
6.	给所有属性赋一个值

9.对web标准化(或网站重构)知道哪些相关的知识,简述几条你知道的Web标准?
网页主要有三部分组成：结构（Structrue）、表现（presentation）和行为（Behavior）。对应的网站标准也分为三方面：
1.	结构化标准语言，主要包括XHTML和XML；
2.	表现标准主要包括css
3.	行为标准主要包括对象模型（如W3C  DOM）、ECMAScript等

10.localstorage和sessionstorage是什么?区别是什么?
localstorage和sessionstorage一样都是用来存储客户端临时信息的对象，他们均只能存储字符串类型对象
localstorage生命周期是永久的，这意味着除非用户在浏览器提供的UI上清除localstorage信息，否则这些信息将永远存在。
sessionstorage生命周期为当前窗口或标签，一旦窗口或标签被永久关闭了，那么所有通过sessionstorage存储的数据也将被清空。
不同浏览器无法共享localstorage或sessionstorage中的信息。相同浏览器的不同页面可以共享相同的localstorage（页面属于相同的域名和端口），但是不同页面或标签间无法共享sessionstorage。这里需要注意的是，页面及标签仅指顶级窗口，如果一个标签页包含多个iframe标签他们属于同源页面，那么他们之间是可以共享sessionstorage的。

11.如何获取一个元素的属性值
element.getAttribute('属性名称')

12.举例说明一下什么是事件委托?
事件委托就是利用冒泡的原理，把事件加到父元素或祖先元素上，触发执行效果

13.json和jsonp的区别?
json返回的是一串json格式数据；而jsonp返回的是脚本代码（包含一个函数调用）
jsonp的全名叫做json with padding，就是把json对象用符合js语法的形式包裹起来以使其他的网站可以请求到，也就是将json封装成js文件传过去。

14.本地存储和cookie(存储在用户本地终端上的数据)之间的区别是什么?
cookie数据始终在同源http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递；cookie数据还有路径的概念，可以限制cookie只属于某个路径下。存储大小限制不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如回话标识。数据有效期不同，cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。

15.行内元素有哪些?块级元素有哪些? 空(void)元素有哪些?
行内元素：a b span img select strong    块级元素：div ul ol li dl dt h1 p 空元素： <br> <hr> <img> <link> <meta>

16.css选择符有哪些？哪些属性可以继承？优先级算法如何计算？css3有哪些新属性？
css选择符：
.	id选择器（#myid）
.	类选择器（.myclassname）
.	标签选择器（div，h1, p）
.	相邻选择器（h1 + p）
.	子选择器（ul > li）
.	后代选择器（li a）
.	通配符选择器（*）
.	属性选择器(a[rel = 'external'])
.	伪类选择器（a:hover，li：nth-child）

可继承属性：
1.	font-size
2.	font-family
3.	color
4.	text-indent
优先级算法：
1.	优先级就近原则，同权重情况下样式定义最近者为准；
2.	载入样式以最后载入的定位为准
3.	！import > id > class > tag
4.	important 比内联优先级搞，但内联比id要高


17.简述一下 src 和 href 的区别？
scr用于替换当前元素，href用于在当前文档和引用资源之间确立关系。
src是source的缩写，指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在的位置；在请求src资源时会将其指向的资源下载并应用到文档内，列如js脚本，img图片，和iframe等元素。当浏览器解析到该元素时，会暂停其他资源的加载，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似将所指向资源嵌入当前标签内。
href是Hypetext Reference的缩写，指向网络资源所在的位置，建立和当前元素（锚点）或当前文档（链接）之间链接，
如果我们在文档中添加
<link href="common.css" rel="stylesheet"/>
那么浏览器会识别该文档为css文件，就会并行下载资源并且不会停止对当前文档的处理。这也是为什么建议使用link方式来加载css，而不是使用@import方式。

18.浏览器的内核分别是什么？
1.Trident内核：代表作品是IE
2.Gecko内核：代表作品Firefox
3.Webkit内核：代表作品是Safari
4.prosto内核：Opera
5.Blink内核：chrome



