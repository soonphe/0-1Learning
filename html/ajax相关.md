# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## ajax相关

### Ajax：
Ajax：“Asynchronous JavaScript and XML”，翻译过来就是异步JavaScript和XML。
要创建Ajax，主角是XMLHttpRequest（下简称XHR）对象。
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