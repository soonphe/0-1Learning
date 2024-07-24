# 0-1Learning

![alttext](../../static/common/svg/luoxiaosheng.svg "公众号")
![alttext](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alttext](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 认识Android

### 1.1 Android系统简介
#### Android系统架构
为了让你能够更好地理解Android系统是怎么工作的，我们先来看一下它的系统架构。
Android大致可以分为四层架构，五块区域。
1. Linux内核层Android系统是基于Linux2.6内核的，这一层为Android设备的各种硬件提供了底层的驱动，如显示驱动、音频驱动、照相机驱动、蓝牙驱动、Wi-Fi驱动、电源管理等。
2. 系统运行库层这一层通过一些C/C++库来为Android系统提供了主要的特性支持。如SQLite库提供了数据库的支持，OpenGL|ES库提供了3D绘图的支持，Webkit库提供了浏览器内核的支持等。同样在这一层还有Android运行时库，它主要提供了一些核心库，能够允许开发者使用Java语言来编写Android应用。另外Android运行时库中还包含了Dalvik虚拟机，它使得每一个Android应用都能运行在独立的进程当中，并且拥有一个自己的Dalvik虚拟机实例。相较于Java虚拟机，Dalvik是专门为移动设备定制的，它针对手机内存、CPU性能有限等情况做了优化处理。
3. 应用框架层这一层主要提供了构建应用程序时可能用到的各种API，Android自带的一些核心应用就是使用这些API完成的，开发者也可以通过使用这些API来构建自己的应用程序。
4. 应用层所有安装在手机上的应用程序都是属于这一层的，比如系统自带的联系人、短信等程序，或者是你从GooglePlay上下载的小游戏，当然还包括你自己开发的程序。

#### Android应用开发特色
1. 四大组件Android系统四大组件分别是活动（Activity）、服务（Service）、广播接收器（BroadcastReceiver）和内容提供器（ContentProvider）。
活动是所有Android应用程序的门面，凡是在应用中你看得到的东西，都是放在活动中的。
服务就比较低调了，你无法看到它，但它会一直在后台默默地运行，即使用户退出了应用，服务仍然是可以继续运行的。
广播接收器可以允许你的应用接收来自各处的广播消息，比如电话、短信等，当然你的应用同样也可以向外发出广播消息。
内容提供器则为应用程序之间共享数据提供了可能，比如你想要读取系统电话簿中的联系人，就需要通过内容提供器来实现。

2. 丰富的系统控件Android系统为开发者提供了丰富的系统控件，使得我们可以很轻松地编写出漂亮的界面。当然如果你品味比较高，不满足于系统自带的控件效果，也完全可以定制属于自己的控件。

3. SQLite数据库Android系统还自带了这种轻量级、运算速度极快的嵌入式关系型数据库。它不仅支持标准的SQL语法，还可以通过Android封装好的API进行操作，让存储和读取数据变得非常方便。

4. 地理位置定位移动设备和PC相比起来，地理位置定位功能应该可以算是很大的一个亮点。

5. 强大的多媒体Android系统还提供了丰富的多媒体服务，如音乐、视频、录音、拍照、闹铃等等

6. 传感器Android手机中都会内置多种传感器，如加速度传感器、方向传感器等


### 1.2 开发环境搭建
这里统一使用Android Studio作为IDE，置于怎么安装，按照官方文档或者网上的教程都可以实现安装

### 1.3 创建第一个Android项目
#### 创建HelloWorld程序
点击 Start a new Android Studio project就可以创建一个新项目

Application name：应用名称，这里填入HelloWorld就行
Company Domain：公司域名，这里填入example.com就行
Package Name：项目包名，Android系统就是通过项目包名来区分不同App，包名一定要有唯一性，这里默认即可

Target Android Devices：版本设置默认即可

Add an Activity to Mobile：创建活动Activity，活动就相当于页面，这里选择Empty Activity即可，
Activity Name 就填入 HelloWorldActivity，Layout Name 就填入 hello_world_layout

启动模拟器或者手机usb连接电脑，找到Android Studio顶部的工具栏图标，选择app旁边的三角形按钮启动

等待编译运行，模拟器或者手机出现app并显示HelloWorld就成功了

### 分析第一个Android程序
1. .gralde和.idea：这里是Android Studio自动生成的文件
2. app：项目的中的代码、资源几乎都是放在这个目录下，后面我们开发的大部分工作都是在这里完成
3. build：编译自动生成的文件
4. gradle：包含了gralde wrapper的配置文件，gradle wrapper的方式不需要提前将gradle下载好，gradle和maven使用类似
5. .gitignore：指定的目录或文件排除在版本控制之外
6. build.gradle：全局的gradle构建脚本
7. gradle.properties：全局的gredle配置文件，这里配置的属性会影响到项目中所有的gradle编译脚本
8. gradlew和gradlew.bat：命令行界面中执行gradle命令的，gradlew是linux或mac系统使用，gradlew.bat是windows使用
9. HelloWorld.iml：iml文件是所有IntelliJ IDEA项目都会自动生成的文件
10. local.properties：指定本机Android SDK路径，通常不用修改
11. settings.gradle：设置相关的gradle脚本
12. res 这个目录下的内容就有点多了，简单点说，就是你在项目中使用到的所有图片、布 局、字符串等资源都要存放在这个目录下，前面提到的 R.java中的内容也是根据这个目 录下的文件自动生成的。当然这个目录下还有很多的子目录，图片放在 drawable目录下， 布局放在 layout目录下，字符串放在 values目录下，所以你不用担心会把整个 res目录 弄得乱糟糟的。 
13. AndroidManifest.xml 这是你整个 Android项目的配置文件，你在程序中定义的所有四大组件都需要在这 个文件里注册。另外还可以在这个文件中给应用程序添加权限声明，也可以重新指定你 创建项目时指定的程序最低兼容版本和目标版本。由于这个文件以后会经常用到，我们 用到的时候再做详细说明。 
14. project.properties 这个文件非常地简单，就是通过一行代码指定了编译程序时所使用的 SDK版本。 我们的 HelloWorld项目使用的是 API 14，你也可以在这里改成其他版本试一试。 

这样整个项目的目录结构就都介绍完了，如果你还不能完全理解的话也很正常，毕竟里 面有太多的东西你都还没接触过。不用担心，这并不会影响到你后面的学习。
相反，等你学 完整本书后再回来看这个目录结构图时，你会觉得特别地清晰和简单。 接下来我们一起分析一下 HelloWorld 项目究竟是怎么运行起来的吧。首先打开 AndroidManifest.xml文件，从中可以找到如下代码： 
~~~~
<activity 
    android:name="com.test.helloworld.HelloWorldActivity" 
    android:label="@string/app_name" > 
    <intent-filter>
        <action android:name="android.intent.action.MAIN" /> 
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter> 
</activity>
~~~~

这段代码表示对 HelloWorldActivity这个活动进行注册，没有在 AndroidManifest.xml里 注册的活动是不能使用的。
其中 intent-filter里的两行代码非常重要，
```
<action android:name= "android.intent.action.MAIN"/>和<category android:name="android.intent.category.LAUNCHER"/> 
```
表示 HelloWorldActivity是这个项目的主活动，在手机上点击应用图标，首先启动的就是这 个活动。

#### 活动Activity
活动是 Android应用程序的门面，凡是在应用中你看得到的东西，都是放在活动中的。
因此 你所看到的界面，其实就是 HelloWorldActivity这个活动。
打开 HelloWorldActivity，代码如下所示：
~~~~
public class HelloWorldActivity extends Activity { 

    @Override 
    protected void onCreate(Bundle savedInstanceState) { 
        super.onCreate(savedInstanceState); 
        setContentView(R.layout.hello_world_layout);
    } 
    @Override 
    public boolean onCreateOptionsMenu(Menu menu) { 
        //Inflatethemenu;thisaddsitemstotheactionbarifitispresent. 
        getMenuInflater().inflate(R.menu.hello_world, menu); 
        return true; 
    } 
} 
~~~~
首先我们可以看到，HelloWorldActivity是继承自 Activity的。
Activity是 Android系统提 供的一个活动基类，我们项目中所有的活动都必须要继承它才能拥有活动的特性。
然后可以 看到 HelloWorldActivity中有两个方法，onCreateOptionsMenu()这个方法是用于创建菜单的， 我们可以先无视它，主要看下 onCreate()方法。
onCreate()方法是一个活动被创建时必定要执 行的方法，其中只有两行代码，并且没有Helloworld!的字样，其实，Helloworld藏在视图中。

#### 视图布局layout
其实Android程序的设计讲究逻辑和视图分离，因此是不推荐在活动中直接编写界面的
常规做法是：在布局文件中编写界面，然后在活动中引入进来。
你可以看到，在 onCreate()方法的第二行调用了 setContentView()方法，就是这个方法给当前的活动引入了一 个 hello_world_layout布局，那 Helloworld!一定就是在这里定义的了！
我们快打开这个文件 看一看。 布 局 文 件 都 是 定 义 在 res/layout 目 录 下 的 ， 当 你 展 开 layout 目 录 ， 你 会 看 到 hello_world_layout.xml这个文件。
打开之后代码如下所示： 
~~~~
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin" 
    android:paddingLeft="@dimen/activity_horizontal_margin" 
    android:paddingRight="@dimen/activity_horizontal_margin" 
    android:paddingTop="@dimen/activity_vertical_margin" 
    tools:context=".HelloWorldActivity" > 

    <TextView 
        android:layout_width="wrap_content" 
        android:layout_height="wrap_content" 
        android:text="@string/hello_world" /> 

</RelativeLayout>  
~~~~
现在还看不懂？没关系，后面我会对布局进行详细讲解的，你现在只需要看到上面代码 中有一个 TextView，这是 Android系统提供的一个控件，用于在布局中显示文字的。
然后你 终于在 TextView中看到了 helloworld的字样，哈哈终于找到了，原来就是通过 android:text= "@string/hello_world"这句代码定义的！
咦？感觉不对劲啊，好像图 1.15 中显示的是 Hello world!，这感叹号怎么没了，大小写也不太一样。 
其实你还是被欺骗了，真正的 Helloworld!字符串也不是在布局文件中定义的。

#### 资源values文件夹
Android不推荐在程序中对字符串进行硬编码，更好的做法一般是把字符串定义在res/values/strings.xml里，然后可以在布局文件或代码中引用。
那我们现在打开 strings.xml看 一下，里面的内容如下：
~~~~
<resources> 
    <string name="app_name">Hello World</string> 
    <string name="action_settings">Settings</string> 
    <string name="hello_world">Hello world!</string> 
</resources> 
~~~~

这下没有什么再能逃出你的法眼了，Helloworld!字符串就是定义在这个文件里的。
并且 字符串的定义都是使用键值对的形式，Helloworld!值对应了一个叫做 hello_world的键，因此 在hello_world_layout.xml布局文件中就是通过引用了hello_world这个键，才找到了相应的值。 
这个时候我无意中瞄到了这个文件中还有一个叫做 app_name的键。你猜对了，我们还 可以在这里通过修改 app_name对应的值，来改变此应用程序的名称。那到底是哪里引用了 app_name这个键呢？打开 AndroidManifest.xml文件自己找找去吧！


#### 详解项目中的资源——res目录
如果你展开 res目录看一下，其实里面的东西还是挺多的，很容易让人看得眼花缭乱，这里归纳一下
drawable开头的文件夹都是用来放图片的
values开头的文件夹都是用来放字符串的
layout文件夹是用来放布局文件的
menu文件夹是用来放菜单文件的
values文件夹下。
    ├── strings.xml -- 定义一些需要在开发中使用的字符串变量和数组，用来实现国际化
    ├── array.xml -- 定义存放一些数组的内容，使用方法同上。
    ├── dimens.xml -- 主要定义一些尺寸，使用方法同上
    └── bools.xml -- 定义一个布尔型的文件，使用方法同上。
    
说明：之所以有这么多 drawable开头的文件夹，其实主要是为了让程序能够兼容 更多的设备。
在制作程序的时候最好能够给同一张图片提供几个不同分辨率的副本，分别放 在这些文件夹下，然后当程序运行的时候会自动根据当前运行设备分辨率的高低选择加载哪 个文件夹下的图片。
当然这只是理想情况，更多的时候美工只会提供给我们一份图片，这时 你就把所有图片都放在 drawable-hdpi文件夹下就好了。 

知道了 res目录下每个文件夹的含义，我们再来看一下如何去使用这些资源吧。
比如刚 刚在 strings.xml中找到的 Hello world!字符串，我们有两种方式可以引用它： 
1. 在代码中通过 R.string.hello_world可以获得该字符串的引用； 
2. 在 XML中通过@string/hello_world可以获得该字符串的引用。 
基本的语法就是上面两种方式，其中 string部分是可以替换的，如果是引用的图片资源 就可以替换成 drawable，如果是引用的布局文件就可以替换成 layout，以此类推。这里就不 再给出具体的例子了，因为后面你会在项目中大量地使用到各种资源，到时候例子多得是呢。 

另外跟你小透漏一下，HelloWorld项目的图标就是在 AndroidManifest.xml 中通过 android:icon="@drawable/ic_launcher"来指定的，ic_launcher这张图片就在drawable文件夹下， 如果想要修改项目的图标应该知道怎么办了吧？


### 1.4 掌握日志工具

通过上一节的学习，你已经成功创建了你的第一个 Android 程序，并且对 Android 项目 的目录结构和运行流程都有了一定的了解。现在本应该是你继续前行的时候，不过我想在这 里给你穿插一点内容，讲解一下 Android 中日志工具的使用方法，这对你以后的 Android 开 发之旅会有极大的帮助。

既然 LogCat 已经添加完成，我们来学习一下如何使用 Android 的日志工具吧。Android 中的日志工具类是 Log（android.util.Log），这个类中提供了如下几个方法来供我们打印日志。

1. Log.v() 这个方法用于打印那些最为琐碎的，意义最小的日志信息。对应级别 verbose，是 Android 日志里面级别最低的一种。

2. Log.d() 这个方法用于打印一些调试信息，这些信息对你调试程序和分析问题应该是有帮助 的。对应级别 debug，比 verbose 高一级。

3. Log.i() 这个方法用于打印一些比较重要的数据，这些数据应该是你非常想看到的，可以帮 你分析用户行为的那种。对应级别 info，比 debug 高一级。

4. Log.w() 这个方法用于打印一些警告信息，提示程序在这个地方可能会有潜在的风险，最好 去修复一下这些出现警告的地方。对应级别 warn，比 info 高一级。

5. Log.e() 这个方法用于打印程序中的错误信息，比如程序进入到了 catch 语句当中。当有错 误信息打印出来的时候，一般都代表你的程序出现严重问题了，必须尽快修复。对应级 别 error，比 warn 高一级。 其实很简单，一共就五个方法，当然每个方法还会有不同的重载，但那对你来说肯定不 是什么难理解的地方了。我们现在就在 HelloWorld 项目中试一试日志工具好不好用吧。 打开 HelloWorldActivity，在 onCreate()方法中添加一行打印日志的语句，如下所示：

protected void onCreate(Bundle savedInstanceState) { super.onCreate(savedInstanceState); setContentView(R.layout.hello_world_layout); Log.d("HelloWorldActivity", "onCreate execute"); }

Log.d 方法中传入了两个参数，第一个参数是 tag，一般传入当前的类名就好，主要用于 对打印信息进行过滤。第二个参数是 msg，即想要打印的具体的内容。

其中你不仅可以看到打印日志的内容和 Tag 名，就连程序的包名、打印的时间以及应用 程序的进程号都可以看到。如果你的 LogCat 中并没有打印出任何信息，有可能是因为你当 前的设备失去焦点了。这时你只需要进入到 DDMS 视图，在 Devices 窗口中点击一下你当前 的设备，打印信息就会出来了。

另外不知道你有没有注意到，你的第一行代码已经在不知不觉中写出来了，我也总算是 交差了。

#### 为什么使用 Log 而不使用 System.out

我相信很多的 Java 新手都非常喜欢使用 System.out.println()方法来打印日志，不知道你 是不是也喜欢这么做。不过在真正的项目开发中，是极度不建议使用 System.out.println()方 法的！如果你在公司的项目中经常使用这个方法，就很有可能要挨骂了。

为什么 System.out.println()方法会这么遭大家唾弃呢？经过我仔细分析之后，发现这个 方法除了使用方便一点之外，其他就一无是处了。方便在哪儿呢？在 Eclipse 中你只需要输 入 syso，然后按下代码提示键，这个方法就会自动出来了，相信这也是很多 Java 新手对它 钟情的原因。那缺点又在哪儿了呢？这个就太多了，比如日志打印不可控制、打印时间无法 确定、不能添加过滤器、日志没有级别区分……

听我说了这些，你可能已经不太想用 System.out.println()方法了，那么 Log 就把上面所 说的缺点全部都做好了吗？虽然谈不上全部，但我觉得 Log 已经做得相当不错了。我现在就 来带你看看 Log 和 LogCat 配合的强大之处。






