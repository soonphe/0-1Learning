# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## rem自适应布局

### 自定义布局实现
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

### em 
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

### rem
相对长度单位rem 
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

