# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-UI

### Shape
（1）带圆角，白色背景，彩色边框的矩形
首先来定义一个带圆角,白色背景,绿色边框的矩形，在selector中设置它为单击时的背景
```
<?xml version="1.0" encoding="utf-8"?>  
    <!--定义一个带圆角,白色背景,绿色边框的矩形-->  
    <shape xmlns:android="http://schemas.android.com/apk/res/android"  
    android:shape="rectangle">  
    <!--圆角-->  
    <corners android:radius="5dp" />  
    <!--填充颜色-->  
    <solid android:color="@color/white" />  
    <!--描边-->  
    <stroke  
    android:width="1dp"  
    android:color="@color/green" />  
</shape>
```

### selector背景选择器：
```
<?xml version="1.0" encoding="utf-8"?>  
<selector xmlns:android="http://schemas.android.com/apk/res/android">  
    <item android:drawable="@drawable/shape_border_press" android:state_pressed="true" />          //组件被单击时的背景图片
    <item android:drawable="@drawable/shape_border_nor" android:state_window_focused="false" />  //默认时情况下的背景图片

    android:state_focused="true"表示在获得焦点时的背景图片
    android:state_selected="true"表示被选中时的背景图片
    android:state_enabled="true"表示响应时的背景图片
</selector>
```
组合使用：
```
<?xml version="1.0" encoding="utf-8"?>  
<selector xmlns:android="http://schemas.android.com/apk/res/android">  
    <!--第一种方法-->  
    <!--<item android:drawable="@drawable/shape_border_press" android:state_pressed="true" />-->  
    <!--<item android:drawable="@drawable/shape_border_nor" android:state_window_focused="false"/>-->  

    <!--第二种方法-->  
    <!--默认情况下是一个带圆角,白色背景,蓝色边框的矩形-->  
    <item android:state_window_focused="false">  
        <shape android:shape="rectangle">  
            <!-- 圆角 -->  
            <corners android:radius="5dp" />  
            <!--填充颜色为白色-->  
            <solid android:color="@color/white" />  
            <!-- 描边 -->  
            <stroke android:width="1dp" android:color="@color/blue" />  
        </shape>  
    </item>  
    <!--单击时是一个带圆角,白色背景,绿色边框的矩形-->  
    <item android:state_pressed="true">  
        <shape android:shape="rectangle">  
            <!--圆角-->  
            <corners android:radius="5dp" />  
            <!--填充颜色为白色-->  
            <solid android:color="@color/white" />  
            <!--描边-->  
            <stroke android:width="1dp" android:color="@color/green" />  
        </shape>  
    </item>  
</selector>  
```

### 渐变色属性
```
<!--渐变色-->
<gradient
    android:angle="270"
    android:centerColor="#0000FF"
    android:endColor="#0000FF"
    android:startColor="#0000FF" />
```
