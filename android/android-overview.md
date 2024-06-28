# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

## android-overview

### Gradle
Gradle wrapper：用来管理版本与相关控制
其中gradle-wrapper.jar ，是使用gradle-wrapper的一个辅助库
gradle-wrapper.properties：是gradle-wrapper的配置文件，里面配置了gradle的版本
gradle配置必须与Gradle plugin版本匹配，否则编译就会失败

**Gradle plugin：位于项目的根目录下的build.gradle中配置的插件**
> gradle配置必须与Gradle wrapper版本不匹配，编译就会失败。

gradlew：W意思是wrapper，它是一个用bash命令包装过的gradle编译启动脚本，里面会进行环境变量检测和设置。最终进行编译的还是gradle

首次导入项目的时候AS默认是使用 gradle-wrapper-properties 中默认的设置，它会从网上下载所需要的对应版本的 gradle
解决方法一(断网操作)：AS设置,快捷键 ctrl+alt+s,在搜索框输入gradle，选择gradle本地编译
解决方法二：删除gradle-wrapper-properties 中的最后一行网址（distributionUrl=""）

gradlehome：C:\Users\anna\.gradle\wrapper\dists
可以设置gradlehome的环境变量，变量值为：gradlehome路径 + gradle-2.14.1-all\8bnwg5hd3w55iofp58khbp6yv（项目对应的gradle版本）
设置环境变量可以解决导入不了项目的问题,但是每次导入都得重新设置一遍


### Andorid 分辨率

dpi范围	密度
0dpi ~ 120dpi	ldpi
120dpi ~ 160dpi	mdpi	1
160dpi ~ 240dpi	hdpi	1.5
240dpi ~ 320dpi	xhdpi	2
320dpi ~ 480dpi	xxhdpi	3
480dpi ~ 640dpi	xxxhdpi	4


ico尺寸
密度	建议尺寸
mipmap-mdpi	48 * 48
mipmap-hdpi	72 * 72
mipmap-xhdpi	96 * 96
mipmap-xxhdpi	144 * 144
mipmap-xxxhdpi	192 * 192


不同分辨率图片放大缩小规则：
图片所有文件dpi x 图片尺寸 = 手机dpi
图片所有文件dpi越小，图片尺寸越大
图片所有文件dpi越大，图片尺寸越小
放大倍数未dpi之间的倍数


占用内存的算法是 (图片所属资源密度 / 手机DPI * 原始图片宽度) * (图片所属资源密度 / 手机DPI * 原始图片高度)


adb查看手机分辨率：
adb shell dumpsys window displays
结果：init=1080x1920 420dpi cur=1080x1920 app=1080x1920 rng=1080x1017-1920x1857


### Android图片中的三级缓存
为什么要使用三级缓存
* 如今的 Android App 经常会需要网络交互，通过网络获取图片是再正常不过的事了
* 假如每次启动的时候都从网络拉取图片的话，势必会消耗很多流量。在当前的状况下，对于非wifi用户来说，流量还是很贵的，一个很耗流量的应用，其用户数量级肯定要受到影响
* 特别是，当我们想要重复浏览一些图片时，如果每一次浏览都需要通过网络获取，流量的浪费可想而知
* 所以提出三级缓存策略，通过网络、本地、内存三级缓存图片，来减少不必要的网络交互，避免浪费流量

什么是三级缓存
* 网络加载，不优先加载，速度慢，浪费流量
* 本地缓存，次优先加载，速度快
* 内存缓存，优先加载，速度最快

三级缓存原理
* 之后运行 App 时，优先访问内存中的图片缓存，若内存中没有，则加载本地SD卡中的图片
* 总之，只在初次访问新内容时，才通过网络获取图片资源

### Android自定义Dialog
调用方式一：获取dialog 的Window，然后调用setContentView方法
1.编写自定义Dialog View
2.程序中调用
~~~~
String info = cityData.getPointerList().get(position).toString();  
AlertDialog alertDialog = new AlertDialog.Builder(CityActivity.this).create();  
alertDialog.show();  

Window window = alertDialog.getWindow();  
window.setContentView(R.layout.dialog_main_info);  

TextView tv_title = (TextView) window.findViewById(R.id.tv_dialog_title);  
tv_title.setText(详细信息);  
TextView tv_message = (TextView) window.findViewById(R.id.tv_dialog_message);  
tv_message.setText(info);

~~~~

调用方式二：使用AlertDialog.Builder中的setView方法
~~~~
AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
View view = View.inflate(getActivity(), R.layout.custom_dialog, null);
builder.setView(view);
builder.setCancelable(true);

TextView title= (TextView) view.findViewById(R.id.title);//设置标题
EditText input_edt= (EditText) view.findViewById(R.id.dialog_edit);//输入内容
Button btn_cancel=(Button)view.findViewById(R.id.btn_cancel);//取消按钮
Button btn_comfirm=(Button)view.findViewById(R.id.btn_comfirm);//确定按钮

//取消或确定按钮监听事件处理
AlertDialog dialog = builder.create();
dialog.show();  
~~~~

### Andorid自定义View
自定义View的实现方式有以下几种

| 类型               | 	定义                                  |
|------------------|--------------------------------------|
| 自定义组合控件	         | 多个控件组合成为一个新的控件，方便多处复用                |
| 继承系统View控件	      | 继承自TextView等系统控件，在系统控件的基础功能上进行扩展     |
| 继承View	          | 不复用系统控件逻辑，继承View进行功能定义               |
| 继承系统ViewGroup	   | 继承自LinearLayout等系统控件，在系统控件的基础功能上进行扩展 |
| 继承ViewViewGroup	 | 不复用系统控件逻辑，继承ViewGroup进行功能定义          |

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

### 事件分发机制

* 对于一个根ViewGroup来说,发生点击事件首先调用dispatchTouchEvent
* 如果这个ViewGroup的onIterceptTouchEvent返回true就表示它要拦截当前事件,接着这个ViewGroup的onTouchEvent就会被调用.如果onIterceptTouchEvent返回false,那么就会继续向下调用子View的dispatchTouchEvent方法
* 当一个View需要处理事件的时候,如果它没有设置onTouchListener,那么直接调用onTouchEvent.如果设置了Listenter 那么就要看Listener的onTouch方法返回值.为true就不调,为false就调onTouchEvent
* View的默认实现会在onTouchEvent里面把touch事件解析成Click之类的事件
* 点击事件传递顺序 Activity -> Window -> View
* 一旦一个元素拦截了某事件,那么一个事件序列里面后续的Move,Down事件都会交给它处理.并且它的onInterceptTouchEvent不会再调用
* View的onTouchEvent默认都会消耗事件,除非它的clickable和longClickable都是false(不可点击),但是enable属性不会影响

### java和kotlin混用
Android Studio添加Kotlin插件

项目根目录-引入依赖插件
```
buildscript {
    ... 
    dependencies {
        //gradle插件
        classpath 'com.android.tools.build:gradle:4.2.2'
        //kotlin插件
        //classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:2.0.0'
        classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.0'
    }
}
```

app目录-应用插件：kotlin-android
```
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
apply plugin: 'kotlin-kapt'
apply plugin: 'kotlin-parcelize'
```

app引入-kotlin支持：
```
    //    implementation 'androidx.core:core-ktx:1.10.1'    //需要compilersdk到33
    //    implementation "org.jetbrains.kotlin:kotlin-stdlib:1.8.0" //支持到kotlin1.8
implementation 'androidx.core:core-ktx:1.3.2'
implementation "org.jetbrains.kotlin:kotlin-stdlib:1.8.0"

app中指定jdk版本
    kotlinOptions {
        jvmTarget = '1.8'
    }
```
在 kotlin 1.8.0 之前，kotlin 的标准库 kotlin-stdlib 的 jvmTarget 是 Java 1.6，但是如果程序的 jvmTarget 是 1.7 或 1.8，则可以手动添加 kotlin-stdlib-jdk7 或 kotlin-stdlib-jdk8 来使用 kotin 对相关 Java 版本提供的 API
在 kotlin 1.8.0 中 kotlin 标准库的 jvmTarget 修改为了 1.8（kotlinc 的 jvmTarget 默认为 1.8，支持到 18），且将 kotlin-stdlib-jdk7 和 kotlin-stdlib-jdk8 中的代码也打包到了 kotlin-stdlib 中，同时将 kotlin-stdlib-jdk7:1.8.0 和 kotlin-stdlib-jdk8:1.8.0 及之后的版本中的 sourceSets 置为了空，而是仅仅将 kotlin-stdlib 作为其依赖进行传递，以保证兼容。
因此在 Kotlin1.8+ 中只需添加 kotlin-stdlib 的依赖即可，不再需要手动添加 kotlin-stdlib-jdk7 或 kotlin-stdlib-jdk8 的依赖。


添加kotlin代码
```
// Kotlin 文件中的代码
fun helloWorld() {
    println("Hello, World!")
}
```

添加java支持(貌似没啥用)
```
android {
    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
    }
}
```
java调用kotlin代码
```
// Java 文件中的代码
public class Main {
    public static void main(String[] args) {
        MyKotlinFileKt.helloWorld();
    }
}
```
