

HTML标签头说明：
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
width=device-width：网页宽度等于设备屏幕宽度
 initial-scale=1：页面初始缩放比例为1
user-scalable=no：禁止用户进行缩放
maximum-scale=1，minimum-scale=1：最大和最小的页面缩放比例

实现手机端自适应，就是可以让页面的元素字体、间距、宽高等属性的属性值可以随着手机屏幕尺寸的变化而变化

em：em的特点 : ① em的值并不是固定的; ② em始终会继承父级元素的字体大小。
body{
    font-size: 20px;
}
.one{
    font-size: 1.5em;——30px
}
.two{
    font-size: 0.5em;——15px
}
相对长度单位rem 
rem是CSS3新增的一个相对单位（root em，根em）。注：相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。
html{
    font-size: 20px;
}
.one{
    font-size: 1.5rem;——30px
}
.two{
    font-size: 0.5rem;——10px
}


<!-- rem布局，根据字体 -->
  <script info="text/javascript">
    /* 因为我们后面用的是rem布局，所以这里做下处理，根据不用设备分辨率更改跟字体大小。 
rem相关布局[请参考](http://www.jianshu.com/p/65f80d4b44bb)*/

    (function(win,doc){
      change();
      function change(){
        doc.documentElement.style.fontSize = doc.documentElement.clientWidth *20/320+'px';
      }
      win.addEventListener('resize',change,false);
      win.addEventListener('orientationchange',change,false);  /* 这个是移动端设备横屏、竖屏转换时触发的事件处理函数 */
    })(window,document);
  </script>