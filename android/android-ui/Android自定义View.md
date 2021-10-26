# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Andorid自定义View

### 分类
自定义View的实现方式有以下几种

| 类型 |	定义 |
| ---- | ---- |
| 自定义组合控件	| 多个控件组合成为一个新的控件，方便多处复用|
| 继承系统View控件	| 继承自TextView等系统控件，在系统控件的基础功能上进行扩展|
| 继承View	| 不复用系统控件逻辑，继承View进行功能定义|
| 继承系统ViewGroup	|继承自LinearLayout等系统控件，在系统控件的基础功能上进行扩展 |
| 继承ViewViewGroup	|不复用系统控件逻辑，继承ViewGroup进行功能定义 |

1.2 View绘制流程
View的绘制基本由measure()、layout()、draw()这个三个函数完成
函数	作用	相关方法
measure()	测量View的宽高	measure(),setMeasuredDimension(),onMeasure()
layout()	计算当前View以及子View的位置	layout(),onLayout(),setFrame()
draw()	视图的绘制工作	draw(),onDraw()

1.5 自定义属性
Android系统的控件以android开头的都是系统自带的属性。为了方便配置自定义View的属性，我们也可以自定义属性值。
Android自定义属性可分为以下几步:

自定义一个View
编写values/attrs.xml，在其中编写styleable和item等标签元素
在布局文件中View使用自定义的属性（注意namespace）
在View的构造方法中通过TypedArray获取


监听点击回调事件：
1.定义接口和click等方法
2.自定义view在特定场景调用接口click方法
3.调用方实现接口