# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


### 数据绑定错误
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


### sass和less区别
less客户端编译，需要编译时间
引入less：
<link rel="stylesheet/less" type="text/css" href="styles.less">
<script src="less.js" type="text/javascript"></script>
调用less样式表时，link标签的rel属性一定是“stylesheet/less”，其中“/less”不可缺少；
less.js脚本只能放在加载less样式的link后面，才能生效。

sass服务端处理
LESS和Sass中的变量的唯一区别就是，LESS使用@，而Sass使用$
