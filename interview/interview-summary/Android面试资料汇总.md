# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android面试资料汇总

### 要点
    前置条件：具备一定的java基础
    0.Framework
    1.Activity、Service、Fragment
    2.网络
    3.数据库
    4.UI、View与自定义View
    5.touch事件分发
    6.动画
    7.进程间通信与线程间通信
    8.Handler与内存泄漏
    9.Android常用技术、框架、工具
    10.APP性能优化

### Framework
简述一下什么是Android Framework
```
Framework是android 系统对 linux kernel，lib库等封装，提供WMS，AMS，bind机制，handler-message机制等方式，供app使用。
简单来说framework就是提供app生存的环境。
1）Activity在attch方法的时候，会创建一个phonewindow（window的子类）
2）onCreate中的setContentView方法，会创建DecorView
3）DecorView 的addview方法，会把layout中的布局加载进来。
```

Android应用程序的组成
```
Activites　　应用程序的表示层。
应用程序的每个界面都将是Activity类的扩展。Acitvities用视图(View)构成GUI来显示信息、响应用户操作。就桌面开发而言，一个活动(Activity)相当于一个窗体(Form)。
Services　　应用程序中的隐形工作者。
Service组件在后台运行，更新你的数据源和可见的Activities，触发通知(Notification)。在应用程序的Activities不激活或不可见时，用于执行依然需要继续的长期处理。
Content Providers　　可共享的数据存储。
Content Providers用于管理和共享应用程序数据库。是跨应用程序边界数据共享的优先方式。
Intents　　一个应用程序间(inter-application)的消息传递框架。
使用Intents你可以在系统范围内广播消息或者对一个目标Activity或Service发送消息，来表示你要执行一个动作。
Widgets　　可以添加到主屏幕界面(home screen)的可视应用程序组件。
作为Broadcase Receiver的特殊变种，widgets让你可以为用户创建可嵌入到主屏幕界面的动态的、交互的应用程序组件。
```

描述一下 android 的系统架构：
```
从小到上就是：
linux kernel,lib dalvik vm ,application framework, app
```

### Activity
Activity和Fragment生命周期有哪些？
```
Activity——onCreate->onStart->onResume->onPause->onStop->onDestroy

Fragment——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume->onPause->onStop->onDestroyView->onDestroy->onDetach
```

启动模式：
```
standard 标准模式(activity默认的): 每次调用startActivity, 都会把activity给创建.
singleTop 单一顶部模式: 每次调用startActivity, 需要判断当前的activity是否已经被创建过并且查看任务栈的顶部是否是当前的 activity, 如果是, 调用onNewIntent方法, 如果不是, 就创建一个新的activity实例.
应用场景: 非法程序员, 写的流氓程序, 一直在弹出某个页面.
singleTask 单一任务栈模式: 如果任务栈中已经存在当前activity, 再去调用startActivity, 会调用当前任务栈的onNewIntent方法. 同时 , 会把所有以上的activity都给清除出栈.
应用场景: 如果一个界面显示的资源非常大, 只需要初始化一次实例.
singleInstance 单一实例模式: activity会在一个新的任务栈中实例化, 并且其他的activity不会创建在新的任务栈中. 始终在整个系统中 会被初始化一次.
应用场景: 在整个系统中, 只需要初始化一次的页面.
```

如何退出Activity？如何安全退出已调用多个Activity的Application？
```
退出Activity直接finish即可。
抛异常强制退出,该方法通过抛异常，使程序Force Close。验证可以，但是，需要解决的问题是，如何使程序结束掉，而不弹出Force Close的窗口。
记录打开的Activity：每打开一个Activity，就记录下来。在需要退出时，关闭每一个Activity即可。
发送特定广播：在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。
递归退出：在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。
```

如何理解Activity，View，Window三者之间的关系？
```
如何理解Activity，View，Window三者之间的关系？
这个问题真的很不好回答。所以这里先来个算是比较恰当的比喻来形容下它们的关系吧。Activity像一个工匠（控制单元），Window像窗户（承载模型），View像窗花（显示视图）LayoutInflater像剪刀，Xml配置像窗花图纸。
1：Activity构造的时候会初始化一个Window，准确的说是PhoneWindow。
2：这个PhoneWindow有一个“ViewRoot”，这个“ViewRoot”是一个View或者说ViewGroup，是最初始的根视图。
3：“ViewRoot”通过addView方法来一个个的添加View。比如TextView，Button等
4：这些View的事件监听，是由WindowManagerService来接受消息，并且回调Activity函数。比如onClickListener，onKeyDown等。
```

service生命周期，可以执行耗时操作吗？
```
他有两种启动方式：
startService：onCreate()- >onStartCommand()->startService()->onDestroy()
BindService：onCreate()->onBind()->onUnbind()->onDestroy()
```

### 网络
*AsyncTask原理
```
AsyncTask
InternalHandler
构造函数中默认绑定 MainLooper ，其处理消息在主线程

SerialExecutor sDefaultExecutor 串行执行任务的线程池

Executor THREAD_POOL_EXECUTOR 并行执行任务的线程池

AsyncTask 初始化时，初始化 FutureTask 对象，为一个 Runnable，其保持了 WorkerRunnable 对象的引用，WorkerRunnable 中持有任务参数。

excute 方法中，调用了 ExcuteOnExecutor 方法， 将 SerialExecutor 作为默认线程池处理 FutureTask 任务

ExcuteOnExecutor 会先判断当前异步任务的状态，如果在运行或结束则抛出异常，无异常则修改 AsyncTask 的状态并执行 onPreExcute 方法，接着调用 SerialExecutor 的 execute 方法

SerialExecutor 的 execute 方法将任务添加到其内部的队列 mTasks 中，按顺序依次执行

SerialExecutor 中执行任务时，调用 THREAD_POOL_EXECUTOR 处理任务，执行完毕后再从 mTasks 中取下一个任务，从而实现串行

执行任务时在子线程中，调用被执行的 FutureTask 的 run 方法，其中调用其保持的 WorkerRunnable 对象的 call 方法

call 方法中，会调用 doInBackground 方法从而该方法中的代码执行在子线程，调用 publishProgress 方法通过 InternalHandler 消息机制向主线程发送进度

call 方法中 doInBackground 执行结束后，调用 postResult 方法,通过消息机制，调用 AsyncTask 的 finish 方法

finish 方法中判断如果任务取消则调用 onCancelled 如果未取消则调用 onPostExecute ，最后更新任务的状态
```

*httpResponseCode
```
200 - 请求成功
301 - 资源（网页等）被永久转移到其它URL
404 - 请求的资源（网页等）不存在
500 - 内部服务器错误
```

### 数据库
数据存储，数据持久化的方式有哪些
```
SharedPreference
数据库SQLite
文件
ContentProvider内容提供者，如联系人
网络
```

文件和数据库哪个效率高
```
数据量大时使用数据库效率高
数据量小时使用文件效率高
```

### UI、View与自定义View
Android中常用的五种布局
```
FrameLayout（框架布局）
LinearLayout （线性布局）
AbsoluteLayout（绝对布局）
RelativeLayout（相对布局）
TableLayout（表格布局）
```

自定义View相关方法：
```
View的绘制基本由measure()、layout()、draw()这个三个函数完成

函数	作用	相关方法
measure()	测量View的宽高	measure(),setMeasuredDimension(),onMeasure()
layout()	计算当前View以及子View的位置	layout(),onLayout(),setFrame()
draw()	视图的绘制工作	draw(),onDraw()
```

SurfaceView和View的区别是什么？
```
SurfaceView中采用了双缓存技术，在单独的线程中更新界面
View在UI线程中更新界面
```

如何设计一个图片加载框架
```
异步加载：线程池
切换线程：Handler，没有争议吧
缓存：LruCache、DiskLruCache
防止OOM：软引用、LruCache、图片压缩、Bitmap像素存储位置
内存泄露：注意ImageView的正确引用，生命周期管理
列表滑动加载的问题：加载错乱、队满任务过多问题
当然，还有一些不是必要的需求，例如加载动画等。
```

### touch事件分发
Android事件传递及处理机制
```
dispatchTouchEvent()事件分发
onInterceptTouchEvent()事件拦截
onTouchEvent()

首先由父控件接收到事件进行分发，调用拦截，返回true进行消费调用onTouchEvent，返回false则将事件传递给子控件，若子控件为ViewGroup则继续传递，最终为View时，事件无法向下传递调用onTouchEvent返回true消费，返回false，回传事件，父控件的onTouchEvent返回true消费，返回false事件丢失
无子类的View没有onInterceptTouchEvent()事件拦截方法
```

onInterceptTouchEvent()和onTouchEvent()的区别？
```
onInterceptTouchEvent()用于拦截触摸事件
onTouchEvent()用于处理触摸事件
```


### 动画
Android中有几种动画
```
Android3.0之前有2种，3.0后有3种。
FrameAnimation（逐帧动画）：将多张图片组合起来进行播放，类似于早期电影的工作原理，很多App的loading是采用这种方式。
TweenAnimation（补间动画）：是对某个View进行一系列的动画的操作，包括淡入淡出（Alpha），缩放（Scale），平移（Translate），旋转（Rotate）四种模式。
PropertyAnimation（属性动画）：属性动画不再仅仅是一种视觉效果了，而是一种不断地对值进行操作的机制，并将值赋到指定对象的指定属性上，可以是任意对象的任意属性。

```

### 进程间通信与线程间通信
Android中跨进程通讯有几种方式
```
1：访问其他应用程序的Activity
如调用系统通话应用
IntentcallIntent=newIntent(Intent.ACTION_CALL,Uri.parse("tel:12345678");
startActivity(callIntent);

2：Content Provider
如访问系统相册

3：广播（Broadcast）
如显示系统时间
```

Broadcast、Content Provider 和 AIDL的区别和联系
```
这3种都可以实现跨进程的通信，那么从效率，适用范围，安全性等方面来比较的话他们3者之间有什么区别？
Broadcast：用于发送和接收广播！实现信息的发送和接收！
AIDL：用于不同程序间服务的相互调用！实现了一个程序为另一个程序服务的功能！
Content Provider:用于将程序的数据库人为地暴露出来！实现一个程序可以对另个程序的数据库进行相对用的操作！
```

Android 线程间通信有哪几种方式
```
1）共享变量（内存）
2）管道
3）handle机制
runOnUiThread(Runnable)
view.post(Runnable)
```

子线程中更新UI的方法
```
第一种：Handler+Message
第二种：view.post
new Handler(context.getMainLooper()).post(
  new Runnable(){
    public void run(){
    //更新UI
    }
  });
第三种：runinUiThread
((Activity)context)runOnUiThread(
  new Runnable(){
    public void run(){
      //更新UI
    }
  }
  );
AsyncTask，EventBus，广播。。。
```
Devik 进程，linux 进程，线程的区别
```
Dalvik进程：每一个android app都会独立占用一个dvm虚拟机，运行在linux系统中。
所以dalvik进程和linux进程是可以理解为一个概念。
```

### Handler与内存泄漏
什么情况下会存在内存泄漏：
```
注册没取消造成内存泄露，如：广播
静态变量持有Activity的引用
单例模式持有Activity的引用
查询数据库后没有关闭游标cursor
构造Adapter时，没有使用 convertView 重用
Bitmap对象不在使用时调用recycle()释放内存
对象被生命周期长的对象引用，如activity被静态集合引用导致activity不能释放
使用Handler造成的内存泄露
```

如何解决内存泄漏
```
静态内部类+弱引用WeakReference
在Activity onStop或者onDestroy的时候，取消掉该Handler对象的Message和Runnable。
通过查看Handler的API，它有几个方法：removeCallbacks(Runnable r)和removeMessages(int what)等。
```

如何避免OOM
```
当程序需要申请一段“大”内存，但是虚拟机没有办法及时的给到，即使做了GC操作以后，这就会抛出 OutOfMemoryException 也就是OOM

减少内存对象的占用
1.ArrayMap/SparseArray代替hashmap（在数据量较少的时候，HashMap的Entry Array比ArrayMap占用更多的内存，HashMap则是使用Serializable进行序列化。在Android中Parcelable比Serializable性能要高）
**2.**避免在android里面使用Enum（会牺牲执行的速度和并大幅增加文件体积）
**3.**减少bitmap的内存占用
inSampleSize：缩放比例，在把图片载入内存之前，我们需要先计算出一个合适的缩放比例，避免不必要的大图载入。
decode format：解码格式，选择ARGB_8888/RBG_565/ARGB_4444/ALPHA_8，存在很大差异。
**4.**减少资源图片的大小，过大的图片可以考虑分段加载

内存对象的重复利用
1.listview/gridview/recycleview contentview的复用
2.inBitmap 属性对于内存对象的复用ARGB_8888/RBG_565/ARGB_4444/ALPHA_8
这个方法在某些条件下非常有用，比如要加载上千张图片的时候。
**3.**避免在ondraw方法里面 new对象
**4.**StringBuilder 代替+
```

### Android常用技术、框架、工具
JNI开发流程
```

```

热修复的原理：
```
1：JavaSisst
2：AspectJ
3：Xposef
```

插件化，动态加载原理：
```

```

断点续传实现原理：
```

```

所使用的开源框架的实现原理，源码：
```
RxJava
Volley
```

Android 屏幕适配
```
屏幕适配的方式：xxxdpi， wrap_content,match_parent. 获取屏幕大小，做处理。
dp来适配屏幕，sp来确定字体大小
drawable-xxdpi, values-1280*1920等 这些就是资源的适配。
wrap_content,match_parent, 这些是view的自适应
weight，这是权重的适配。
```

开发中都使用过哪些框架、平台
```
**1.**EventBus 事件分发机制，由handler实现，线程间通信
**2.**xUtils->DbUtils,ViewUtils,HttpUtils,BitmapUtils
**3.**百度地图
**4.**volley
**5.**fastjson
**6.**picciso
**7.**友盟
**8.**zxing
**9.**Gson
```



### APP性能优化
如何对 Android 应用进行性能分析
```
android 性能主要之响应速度 和UI刷新速度。

首先从函数的耗时来说，有一个工具TraceView 这是androidsdk自带的工作，用于测量函数耗时的。
UI布局的分析，可以有2块，一块就是Hierarchy Viewer 可以看到View的布局层次，以及每个View刷新加载的时间。
这样可以很快定位到那块layout & View 耗时最长。
还有就是通过自定义View来减少view的层次。
```

布局优化
```
避免OverDraw过渡绘制
优化布局层级
避免嵌套过多无用布局
当我们在画布局的时候，如果能实现相同的功能，优先考虑相对布局，然后在考虑别的布局，不要用绝对布局。
使用<include />标签把复杂的界面需要抽取出来
使用<merge />标签，因为它在优化UI结构时起到很重要的作用。目的是通过删减多余或者额外的层级，从而优化整个Android Layout的结构。核心功能就是减少冗余的层次从而达到优化UI的目的！
ViewStub 是一个隐藏的，不占用内存空间的视图对象，它可以在运行时延迟加载布局资源文件。
```
Android布局优化之Merge Include ViewStub使用与源码分析
Android抽象布局——include、merge 、ViewStub
Android LayoutInflater原理分析，带你一步步深入了解View(一)

代码优化
```
使用AndroidLint分析结果进行相应优化
不使用枚举及IOC框架，反射性能低
常量加static
静态方法
减少不必要的对象、成员变量
尽量使用线程池
适当使用软引用和弱引用
尽量使用静态内部类，避免潜在的内存泄露
图片缓存，采用内存缓存LRUCache和硬盘缓存DiskLRUCache
Bitmap优化，采用适当分辨率大小并及时回收
```
网络请求优化
```
连接复用 :节省连接建立时间，如开启 keep-alive。
对于 Android 来说默认情况下 HttpURLConnection 和 HttpClient 都开启了 keep-alive。只是 2.2 之前 HttpURLConnection 存在影响连接池的 Bug，具体可见：Android HttpURLConnection 及 HttpClient 选择
请求合并:即将多个请求合并为一个进行请求，比较常见的就是网页中的 CSS Image Sprites。如果某个页面内请求过多，也可以考虑做一定的请求合并。
    减少请求数据的大小：对于post请求，body可以做gzip压缩的，header也可以作数据压缩(不过只支持http 2.0)。
    返回的数据的body也可以作gzip压缩，body数据体积可以缩小到原来的30%左右。(也可以考虑压缩返回的json数据的key数据的体积，尤其是针对返回数据格式变化不大的情况，支付宝聊天返回的数据用到了)
根据用户的当前的网络质量来判断下载什么质量的图片(电商用的比较多)。
```

简述静默安装的原理，如何在无需root权限的情况下实现静默安装？
```
伪装成系统应用，这就要给app打上系统应用的签名，但是这些签名在小米等手机上是没用的。
通过把应用放在system/app的目录下也可以实现。
```

ANR 是什么？怎样避免和解决 ANR（重要）
```
ANR->Application Not Responding

也就是在规定的时间内，没有响应。

三种类型：

1）. KeyDispatchTimeout(5 seconds) —主要类型按键或触摸事件在特定时间内无响应
2）. BroadcastTimeout(10 seconds) —BroadcastReceiver在特定时间内无法处理完成
3）. ServiceTimeout(20 seconds) —小概率类型 Service在特定的时间内无法处理完成
为什么会超时：事件没有机会处理 & 事件处理超时
```
怎么避免ANR
```
ANR的关键是处理超时，所以应该避免在UI线程，BroadcastReceiver 还有service主线程中，处理复杂的逻辑和计算交给work thread操作。
1）避免在activity里面做耗时操作，oncreate & onresume
2）避免在onReceiver里面做过多操作
3）避免在Intent Receiver里启动一个Activity，因为它会创建一个新的画面，并从当前用户正在运行的程序上抢夺焦点。
4）尽量使用handler来处理UI thread & workthread的交互。
```
如何解决ANR
```
首先定位ANR发生的log：
从log可以看出，cpu在做大量的io操作。
所以可以查看io操作的地方。
当然，也有可能cpu占用不高，那就是 主线程被block住了。
```


---


## Android面试经历

### 案例一
一面
* 介绍一下你的项目
* 网络框架的搭建
* 图片加载框架的实现
* 写个图片浏览器，说出你的思路
* 上网站写代码，如下：
  有一个容器类 ArrayList，保存整数类型的元素，现在要求编写一个帮助类，类内提供一个帮助函数，帮助函数的功能是删除 容器中<10的元素。


二面
* Activity的启动模式
* 事件分发机制
* 写代码，LeetCode上股票利益最大化问题
* 写代码，剑指offer上第一次只出现一次的字符

三面

* 聊项目，聊大学做过的事
* 写代码，反转字符串
* 写代码，字符串中出现最多的字符。

---
### 案例二
一面

* 说一下你怎么学习安卓的？
* 项目中遇到哪些问题，如何解决的？
* Android事件分发机制？
* 三级缓存底层实现？
* HashMap底层实现，hashCode如何对应bucket?
* Java的垃圾回收机制，引用计数法两个对象互相引用如何解决？
* 用过的开源框架的源码分析
* Acticity的生命周期，Activity异常退出该如何处理？
* tcp和udp的区别，tcp如何保证可靠的，丢包如何处理？

二面：

* 标号1-n的n个人首尾相接，1到3报数，报到3的退出，求最后一个人的标号
* 给定一个字符串，求第一个不重复的字符 abbcad -> c

---

### 案例三
一面：

* 自我介绍
- Toolbar的使用
- 如何判断本地缓存的时候数据需要从网络端获取
- 跨进程间通信
- Handler消息机制
- SharedPreference实现
- 快速排序
- 项目难点
* Android中ClassLoader和java中有什么关系和区别？
* 熟不熟jvm，说一下Jvm的自动内存管理？
* 语言基础，String类可以被继承吗？为什么？
* Final能修饰什么？（当时我说class、field、method，他说还有吗？然后又叫我不要在意，后来回想起，应该是问到我在参数里面要不要用final，接下来是因为匿名内部类）
* Java中有内存泄露吗？（先说本质，再结合handler+匿名内部类）当时如何分析的？
* 描述下Aidl？觉得aidl有什么缺陷（这里在这个问题上回答有欠缺）
* **评价一下我，如果顺利进网易，需要往技术栈加什么点尽快投入业务？**



二面：

* 用过什么开源，举一个例子？（volley）
* Activity生命周期？情景：现在在一张act1点了新的act2，周期如何？
* Act的launchMode，有没有结合项目用过（自己的程序锁和微信的PC端登陆对比，不过我现在又发现，应该大约估计可能是动态加载的一个缺陷，如果有找到相关信息，请务必跟我说。具体问题就是，当在PC端登录时，Android终端的微信会跳出，即使wechat的task不是在fore，当按下确认，返回的是wechat，而不是自己先前的app）
* View的绘制原理，有没有用canvas自己画过ui？
* 以后想做Android什么方向？（中间件+SDK）
* 怎么看待前端和后端？
* 如果学前端会如何学？
* 优缺点？兴趣？
* 想不想来杭州？
* **评价一下我？往技术栈加什么？**

---
### 案例四

一面

- 自我介绍
- 面向对象三大特性
- Java虚拟机，垃圾回收
- GSON
- RxJava+Retrofit
- 图片缓存，三级缓存
- Android启动模式
- 四大组件
- Fragment生命周期，嵌套
- AsyncTask机制
- Handler机制

二面

- 面试官写程序，看错误。
- 面试官写程序让判断GC引用计数法循环引用会发生什么情况
- Android进程间通信，Binder机制
- Handler消息机制，postDelayed会造成线程阻塞吗？对内存有什么影响？
- Debug和Release状态的不同
- 实现stack 的pop和push接口 要求：
    - 1.用基本的数组实现
    - 2.考虑范型
    - 3.考虑下同步问题
    - 4.考虑扩容问题


---
### 案例五
一面

静态内部类、内部类、匿名内部类，为什么内部类会持有外部类的引用？持有的引用是this？还是其它？

```
静态内部类：使用static修饰的内部类
匿名内部类：使用new生成的内部类
因为内部类的产生依赖于外部类，持有的引用是类名.this。
```

ArrayList和Vector的主要区别是什么？

```
ArrayList在Java1.2引入，用于替换Vector

Vector:

线程同步
当Vector中的元素超过它的初始大小时，Vector会将它的容量翻倍

ArrayList:

线程不同步，但性能很好
当ArrayList中的元素超过它的初始大小时，ArrayList只增加50%的大小
```

[Java集合类框架](http://yuweiguocn.github.io/2016/01/06/java-collection/)


Java中try catch finally的执行顺序

```
先执行try中代码发生异常执行catch中代码，最后一定会执行finally中代码
```

switch是否能作用在byte上，是否能作用在long上，是否能作用在String上？

```
switch支持使用byte类型，不支持long类型，String支持在java1.7引入
```

Activity和Fragment生命周期有哪些？

```
Activity——onCreate->onStart->onResume->onPause->onStop->onDestroy

Fragment——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume->onPause->onStop->onDestroyView->onDestroy->onDetach
```


onInterceptTouchEvent()和onTouchEvent()的区别？

```
onInterceptTouchEvent()用于拦截触摸事件
onTouchEvent()用于处理触摸事件
```

RemoteView在哪些功能中使用

```
APPwidget和Notification中
```

SurfaceView和View的区别是什么？

```
SurfaceView中采用了双缓存技术，在单独的线程中更新界面
View在UI线程中更新界面
```

讲一下android中进程的优先级？

```
前台进程
可见进程
服务进程
后台进程
空进程
```

tips：静态类持有Activity引用会导致内存泄露


二面

* service生命周期，可以执行耗时操作吗？
* JNI开发流程
* Java线程池，线程同步
* 自己设计一个图片加载框架
* 自定义View相关方法
* http ResponseCode
* 插件化，动态加载
* 性能优化，MAT
* AsyncTask原理
* 65k限制
* Serializable和Parcelable
* 文件和数据库哪个效率高
* 断点续传
* WebView和JS
* 所使用的开源框架的实现原理，源码








