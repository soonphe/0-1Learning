# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Icon
1. img一个页面的请求资源中图片 img 占了大部分，所以为了优化有了image sprite 就是所谓的雪碧图，
就是将多个图片合成一个图片，然后利用 css 的 background-position 定位显示不同的 icon 图标，但这个也有一个很大的痛点，维护困难

2. font 库，常见的如 Font Awesome ，使用起来也非常的方便，定制性也非常的不友善，图标库一共有675个图标

3. iconfont：几百个公司的开源图标库，还有各式各样的小图标，还支持自定义创建图标库

### iconfont 三种使用姿势
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


