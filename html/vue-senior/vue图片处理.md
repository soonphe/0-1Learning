

### Vue - 引入本地图片的两种方式
第一种，只引入单个图片，这种引入方法在异步中引入则会报错。 比如需要遍历出很多图片展示时
<image :src = require('图片的路径') />

第二种，可引入多个图片，也可引入单个图片。 vuelic3版本没有static文件夹。可将静态图片存放到public目录下，直接引入即可
<image src="/public/image/logo.png"/>

第三种：使用相对路径形式@/assets/icon/group_4.png
```
<!--    <img class="card-panel-icon" src="@/assets/icon/group_4.png" alt="404" style="width: 48px">-->

    <img class="card-panel-icon" :src=imgSrc(item.icon) alt="404" style="width: 48px">

    imgSrc: function(index) {
      return require('@/assets/icon/' + (index) + '.png')
    },