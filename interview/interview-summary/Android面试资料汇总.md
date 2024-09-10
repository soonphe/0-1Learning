# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android面试资料汇总

### 要点
    前置条件：具备一定的java基础
    0.Framework *
    1.Activity、Service、Fragment
    2.网络
    3.数据库
    4.UI与自定义View *
    5.React Native 混合开发（H5混合开发）
    6.JNI开发 *
    7.touch事件分发 
    8.动画
    9.进程间通信与线程间通信
    10.Handler与内存泄漏
    11.Android常用技术、框架、工具
    12.APP性能优化
    13.系统设计与场景

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

**Serializable和Parcelable**
Serializable (Java自带)和Parcelable (Android特有)都是用来序列化的。

Serializable和Parcelable二者差异：
在使用内存的时候，Parcelable比Serializable性能高，所以推荐使用Parcelable。
Serializable在序列化的时候会产生大量的临时变量，从而引起频繁的GC。
Parcelable不能使用在要将数据存储在磁盘上的情况，因为Parcelable不能很好的保证数据的持续性在外界有变化的情况下。尽管Serializable效率低点，但此时还是建议使用Serializable 。

**android 65k限制**
Android的65K限制‌指的是在Android应用程序开发中，单个DEX文件（Dalvik Executable）能够引用的方法总数上限为65536个。
这个限制包括应用程序本身的代码以及所有依赖库中的方法。当应用程序（包括其所有库依赖项）的方法总数超过这个限制时，会遇到构建错误，因为DEX文件不能包含超过65535个方法的引用。

为了解决这个问题，Android提供了MultiDex的支持，从Android 5.0（API 21）开始，Android支持在应用中使用多个DEX文件，这允许突破65K方法限制。
通过在build.gradle文件的defaultConfig部分启用MultiDex，并在应用的dependencies块中添加MultiDex支持库，可以解决这个问题

### Activity、Service、Fragment
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

讲一下android中进程的优先级？

```
前台进程
可见进程
服务进程
后台进程
空进程
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

Handler机制
```
1、handler封装消息的发送（主要包含消息发送给谁）
2、Looper——消息封装的载体。（1）内部包含一个MessageQueue，所有的Handler发送的消息都走向这个消息队列；（2）Looper.Looper方法，就是一个死循环，不断地从MessageQueue取消息，如果有消息就处理消息，没有消息就阻塞。
3、MessageQueue，一个消息队列，添加消息，处理消息
4、handler内部与Looper关联，handler->Looper->MessageQueue,handler发送消息就是向MessageQueue队列发送消息。

总结：handler负责发送消息，Looper负责接收handler发送的消息，并把消息回传给handler自己。
```
handler postdelay 是否会阻塞线程，对内存的影响
```
Handler.postDelayed不会造成线程阻塞。
Handler.postDelayed方法允许在指定的延迟时间后将一个Runnable对象添加到消息队列中，然后在主线程中执行该任务。这种技术的主要优势是可以在后台线程中执行耗时操作，而不会阻塞主线程，从而保持应用的响应性能。它常用于定时更新UI元素、执行定时任务、轮询服务器等场景。

Handler.postDelayed的工作原理是将消息插入到MessageQueue中，通过MessageQueue.enqueueMessage方法实现。
这个过程使用了同步锁来保证线程操作的安全，避免了线程阻塞。
虽然存在对线程阻塞的描述，但实际上，Handler对Message的处理是管道操作，MessageQueue对所有Message统一进行了管理。
在Looper.loop不停对MessageQueue遍历的时候，如果队列头部的Message没到执行时间，会计算剩余执行时间，然后调用nativePollOnce()阻塞线程，直到阻塞时间到或者下一次有Message进队。这种阻塞是为了等待消息的执行时间到达，而不是因为Handler.postDelayed本身造成的线程阻塞。

Handler导致内存泄漏一般发生在发送延迟消息的时候，当Activity关闭之后，延迟消息还没发出，那么主线程中的MessageQueue就会持有这个消息的引用，而这个消息是持有Handler的引用，而handler作为匿名内部类持有了Activity的引用，所以就有了以下的一条引用链。
主线程 —> threadlocal —> Looper —> MessageQueue —> Message —> Handler —> Activity
其根本原因是因为这条引用链的头头，也就是主线程，是不会被回收的，所以导致Activity无法被回收，出现内存泄漏，其中Handler只能算是导火索。
而我们平时用到的子线程通过Handler更新UI，其原因是因为运行中的子线程不会被回收，而子线程持有了Actiivty的引用（不然也无法调用Activity的Handler），所以就导致内存泄漏了，但是这个情况的主要原因还是在于子线程本身。
```

*httpResponseCode
```
200 - 请求成功
301 - 资源（网页等）被永久转移到其它URL
404 - 请求的资源（网页等）不存在
500 - 内部服务器错误
```

gson原理
```
gson解析流程：
（1）Gson对象创建使用的是门面模式。
（2）而排除器使用的是代理模式  com.google.gson.internal.Excluder
（3）字段命名策略使用的是策略模式  com.google.gson.FieldNamingStrategy#translateName
（4）实例构造器  com.google.gson.internal.ConstructorConstructor  根据不同的数据类型返回一个构造器。
（5）TypeAdapter的工厂列表
List<TypeAdapterFactory> factories = new ArrayList<TypeAdapterFactory>();  决定json的解析顺序
jsonstr — > 排除器---->自定义TypeAdapter—>gson自带的TypeAdapter----> 反射ReflectiveTypeAdapterFactory解析。
（6）GsonBuilder的创建  使用的是建造者模式  最终通过create方法创建Gson对象出来
（7）toJson()
（8）JsonWriter 写操作类
（9）fromJson
（10）JsonReader  com.google.gson.stream.JsonReader#doPeek  将json从栈中取出进行词法分析
（11）getAdapter()
（12）调用TypeAdapter的read方法

2.5JSON反射机制
com.google.gson.internal.bind.ReflectiveTypeAdapterFactory#create
其他的解析器都没办法解析的时候，会使用这种反射的解析器来解析。

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

### UI与自定义View
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

RemoteViews
```
远程View，它表示的是一个View结构，它可以在其他进程中显示，为了跨进程更新它的界面，RemoteViews提供了一组基础的操作来实现这个效果。
```

WebView和JS
```

```


### React Native 混合开发（H5混合开发）
框架：RN, flutter, uniapp

React Native开发流程：
1.创建React Native项目：
2.添加原生模块：
3.编写React Native组件：
4.调用原生API或者组件：
注意：具体的原生模块和视图名称需要根据实际情况进行替换。在实际开发中，还需要处理平台特定代码，比如Java（Android）和Objective-C/Swift（iOS）的适配等。

### JNI开发
在Android中使用JNI进行开发通常涉及以下步骤：
1. 编写Java原生接口(JNI)函数。
2. 编译C/C++源代码，生成共享库(.so文件)。
3. 在Android应用中加载并使用这些共享库。

```
步骤1：编写Java原生接口函数
在Java类中添加本地方法声明，并使用native关键字。
public class MyNativeClass {
    static {
        System.loadLibrary("mynative");
    }
 
    public native static int getNumber();
}

步骤2：使用javah生成JNI头文件
在命令行中使用javah工具生成本地方法的C/C++头文件。
javah -jni com.example.MyNativeClass
这将生成一个com_example_MyNativeClass.h文件。

步骤3：实现JNI函数
编写C/C++源文件，实现在com_example_MyNativeClass.h中声明的函数。
#include <jni.h>
#include "com_example_MyNativeClass.h"
 
JNIEXPORT jint JNICALL Java_com_example_MyNativeClass_getNumber(JNIEnv *env, jclass clazz) {
    return 42;
}

步骤4：编译C/C++代码
使用NDK编译器将C/C++源代码编译成共享库。
cd /path/to/your/jni/source
ndk-build

步骤5：加载共享库
在Android应用的AndroidManifest.xml中添加所需的共享库。
<application ...>
    ...
    <uses-library android:name="mynative" />
</application>
确保共享库.so文件位于libs/目录下的对应ABI子目录中，或者在应用的build.gradle文件中指定路径
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
热修复的原理：
```
1：JavaSisst
2：AspectJ
3：Xposef
```

插件化，动态加载原理：
```
Android插件化的核心原理是基于Java的类加载机制

Android插件化的核心原理是基于Java的类加载机制。在Java中，ClassLoader负责加载类。每个ClassLoader都有一个父ClassLoader，当我们尝试加载一个类时，ClassLoader会首先尝试让它的父ClassLoader加载这个类，这就是所谓的双亲委托机制。这种机制可以确保同一个类只会被加载一次。

在Android插件化中，我们可以创建一个新的ClassLoader来加载插件中的类。这个ClassLoader的父ClassLoader是宿主应用的ClassLoader，因此，插件中的类可以访问宿主应用中的类，但是宿主应用中的类不能访问插件中的类。

1.2 资源加载
在Android中，资源文件是通过Resources对象来加载的。每个APK都有一个独立的Resources对象，用于加载它自己的资源文件。
在Android插件化中，我们可以创建一个新的Resources对象来加载插件中的资源文件。这个Resources对象的AssetManager是通过反射创建的，可以加载任意路径的APK文件。

1.3 四大组件支持
在Android插件化中，四大组件（Activity，Service，BroadcastReceiver，ContentProvider）的支持是一个重要的问题。因为在Android系统中，这些组件都需要在AndroidManifest.xml中进行声明，而插件APK的AndroidManifest.xml是不会被解析的，所以我们需要采取一些特殊的手段来支持这些组件。

Activity：Activity是最常用的组件，也是最需要支持的组件。在插件化中，我们可以通过Proxy Activity代理或者Hook方式来支持Activity。
Service：Service的支持也是通过代理或者Hook方式来实现的。我们可以在宿主中预先声明一些代理Service，然后在运行时将这些代理Service替换为插件中的Service。
BroadcastReceiver：BroadcastReceiver的支持相对比较简单，我们可以在运行时动态注册BroadcastReceiver，或者通过Hook方式来支持静态注册的BroadcastReceiver。
ContentProvider：ContentProvider的支持是最复杂的，因为ContentProvider在应用启动时就会被创建，而且每个ContentProvider都需要一个唯一的authority。我们可以通过Hook方式来支持ContentProvider，或者使用一些特殊的技巧，例如使用同一个authority来支持多个ContentProvider。

1.4 两种支持Activity的方式
所有的插件框架在解决的问题都不是如何动态加载类，而是动态加载的Activity没有在AndroidManifest中注册，该如何能正常运行。如果Android系统没有AndroidManifest的限制，那么所有插件框架都没有存在的必要了。因为Java语言本身就支持动态更新实现的能力。
Proxy Activity代理：这种方式是通过在宿主应用中预先声明一些代理Activity，然后在运行时将这些代理Activity替换为插件中的Activity。这种方式的优点是实现简单，但是有一些限制，例如无法支持插件中的Activity在AndroidManifest.xml中声明的一些属性。
Hook方式：这种方式是通过Hook AMS（Activity Manager Service），替换AMS中的一些方法，从而绕过AMS的检查。这种方式的优点是可以完全支持插件中的Activity，但是实现复杂，需要对Android系统有深入的了解。
```

断点续传实现原理：
```
断点续传是指在网络不稳定或者传输中断的情况下，能够从上次中断的地方继续传输文件的技术。在Android中实现断点续传通常涉及到以下几个步骤：
1.使用HttpURLConnection或OkHttp等网络库进行文件下载或上传。
2.记录已经传输的文件大小。
3.设置HTTP请求头Range以指定从哪个位置开始传输。
4.使用Thread或ThreadPool进行多线程下载提高效率。
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
常见的性能优化工具：profiler，MAT

android Debug和Release状态的不同
```
Android的Debug和Release状态在编译配置、调试信息、代码优化、资源处理、签名配置等方面存在显著差异。‌
‌编译配置‌：Debug模式通常具有较低的优化级别，侧重于缩短编译时间和提高调试效率，而Release模式则启用更高级别的编译优化，包括代码内联、循环展开、死代码移除等，以提高应用性能和减少最终包的大小。此外，Debug模式编译的应用包含丰富的调试信息，如变量名和方法调用栈，而Release模式则剥离这些信息以减少应用体积和提高安全性。
‌调试信息‌：Debug模式包含调试信息，便于开发者进行调试，而Release模式则不保存调试信息，以减小体积和提高运行速度。
‌代码优化‌：Release模式经常使用代码混淆工具如ProGuard或R8进行代码混淆和资源优化，以防止反编译和保护代码不被轻易理解，而Debug构建不会执行这些步骤，因为这些优化可能会干扰调试。
‌资源处理‌：Release模式对资源文件进行更加严格的压缩和优化，以减少应用的体积，而Debug模式则不进行这些优化。
‌签名配置‌：在Debug模式下，Android Studio会自动使用一个默认的debug.keystore对应用进行签名，而在Release模式下，需要使用开发者自己的密钥库和密钥对应用进行签名。
```

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

内存泄露问题：
```
静态类持有Activity引用会导致内存泄露
```


### 系统设计与场景
**自己设计一个图片加载框架**
```
异步加载：线程池
切换线程：Handler，没有争议吧
缓存：LruCache、DiskLruCache
防止OOM：软引用、LruCache、图片压缩、Bitmap像素存储位置
内存泄露：注意ImageView的正确引用，生命周期管理
列表滑动加载的问题：加载错乱、队满任务过多问题
当然，还有一些不是必要的需求，例如加载动画等。
```

---
### 补充：
**Fragment的生命周期和activity如何的一个关系**

**Fragment生命周期**

注意和Activity的相比的区别,按照执行顺序
* onAttach(),onDetach()
* onCreateView(),onDestroyView()

**为什么在Service中创建子线程而不是Activity中**
这是因为Activity很难对Thread进行控制，当Activity被销毁之后，就没有任何其它的办法可以再重新获取到之前创建的子线程的实例。而且在一个Activity中创建的子线程，另一个Activity无法对其进行操作。但是Service就不同了，所有的Activity都可以与Service进行关联，然后可以很方便地操作其中的方法，即使Activity被销毁了，之后只要重新与Service建立关联，就又能够获取到原有的Service中Binder的实例。因此，使用Service来处理后台任务，Activity就可以放心地finish，完全不需要担心无法对后台任务进行控制的情况。

**Intent的使用方法，可以传递哪些数据类型。**
通过查询Intent/Bundle的API文档，我们可以获知，Intent/Bundle支持传递基本类型的数据和基本类型的数组数据，以及String/CharSequence类型的数据和String/CharSequence类型的数组数据。而对于其它类型的数据貌似无能为力，其实不然，我们可以在Intent/Bundle的API中看到Intent/Bundle还可以传递Parcelable（包裹化，邮包）和Serializable（序列化）类型的数据，以及它们的数组/列表数据。
所以要让非基本类型和非String/CharSequence类型的数据通过Intent/Bundle来进行传输，我们就需要在数据类型中实现Parcelable接口或是Serializable接口。

**Service的两种启动方法，有什么区别**
1.在Context中通过``public boolean bindService(Intent service,ServiceConnection conn,int flags)`` 方法来进行Service与Context的关联并启动，并且Service的生命周期依附于Context(**不求同时同分同秒生！但求同时同分同秒屎！！**)。
2.通过`` public ComponentName startService(Intent service)``方法去启动一个Service，此时Service的生命周期与启动它的Context无关。
3.要注意的是，whatever，**都需要在xml里注册你的Service**，就像这样:
```
<service
        android:name=".packnameName.youServiceName"
        android:enabled="true" />
```

**广播(Broadcast Receiver)的两种动态注册和静态注册有什么区别。**
  静态注册：在AndroidManifest.xml文件中进行注册，当App退出后，Receiver仍然可以接收到广播并且进行相应的处理
  动态注册：在代码中动态注册，当App退出后，也就没办法再接受广播了

**目前能否保证service不被杀死**

**Service设置成START_STICKY**

* kill 后会被重启（等待5秒左右），重传Intent，保持与重启前一样

**提升service优先级**
* 在AndroidManifest.xml文件中对于intent-filter可以通过``android:priority = "1000"``这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，**同时适用于广播**。
* 【结论】目前看来，priority这个属性貌似只适用于broadcast，对于Service来说可能无效

**提升service进程优先级**
* Android中的进程是托管的，当系统进程空间紧张的时候，会依照优先级自动进行进程的回收
* 当service运行在低内存的环境时，将会kill掉一些存在的进程。因此进程的优先级将会很重要，可以在startForeground()使用startForeground()将service放到前台状态。这样在低内存时被kill的几率会低一些。
* 【结论】如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart()

**onDestroy方法里重启service**
* service +broadcast  方式，就是当service走onDestory()的时候，发送一个自定义的广播，当收到广播的时候，重新启动service
* 也可以直接在onDestroy()里startService
* 【结论】当使用类似口口管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证

**监听系统广播判断Service状态**
* 通过系统的一些广播，比如：手机重启、界面唤醒、应用状态改变等等监听并捕获到，然后判断我们的Service是否还存活，别忘记加权限
* 【结论】这也能算是一种措施，不过感觉监听多了会导致Service很混乱，带来诸多不便

**在JNI层,用C代码fork一个进程出来**

* 这样产生的进程,会被系统认为是两个不同的进程.但是Android5.0之后可能不行

**root之后放到system/app变成系统级应用**

**大招: 放一个像素在前台(手机QQ)**

**动画有哪两类，各有什么特点？三种动画的区别**

* tween 补间动画。通过指定View的初末状态和变化时间、方式，对View的内容完成一系列的图形变换来实现动画效果。
  Alpha
  Scale
  Translate
  Rotate。

* frame 帧动画
  AnimationDrawable 控制
  animation-list xml布局

* PropertyAnimation 属性动画

**Android的数据存储形式。**
```
* SQLite：SQLite是一个轻量级的数据库，支持基本的SQL语法，是常被采用的一种数据存储方式。Android为此数据库提供了一个名为SQLiteDatabase的类，封装了一些操作数据库的api
* SharedPreference： 除SQLite数据库外，另一种常用的数据存储方式，其本质就是一个xml文件，常用于存储较简单的参数设置。
* File： 即常说的文件（I/O）存储方法，常用语存储大数量的数据，但是缺点是更新数据将是一件困难的事情。
* ContentProvider: Android系统中能实现所有应用程序共享的一种数据存储方式，由于数据通常在各应用间的是互相私密的，所以此存储方式较少使用，但是其又是必不可少的一种存储方式。例如音频，视频，图片和通讯录，一般都可以采用此种方式进行存储。每个Content Provider都会对外提供一个公共的URI（包装成Uri对象），如果应用程序有数据需要共享时，就需要使用Content Provider为这些数据定义一个URI，然后其他的应用程序就通过Content Provider传入这个URI来对数据进行操作。
```

**如何判断应用被强杀**
在Application中定义一个static常量，赋值为－1，在欢迎界面改为0，如果被强杀，application重新初始化，在父类Activity判断该常量的值。

**应用被强杀如何解决**
如果在每一个Activity的onCreate里判断是否被强杀，冗余了，封装到Activity的父类中，如果被强杀，跳转回主界面，如果没有被强杀，执行Activity的初始化操作，给主界面传递intent参数，主界面会调用onNewIntent方法，在onNewIntent跳转到欢迎页面，重新来一遍流程。

**Json有什么优劣势。**

**怎样退出终止App**

**Asset目录与res目录的区别。**
res 目录下面有很多文件，例如 drawable,mipmap,raw 等。res 下面除了 raw 文件不会被压缩外，其余文件都会被压缩。同时 res目录下的文件可以通过R 文件访问。Asset 也是用来存储资源，但是 asset 文件内容只能通过路径或者 AssetManager 读取。 [官方文档](https://developer.android.com/studio/projects/index.html)

**Android怎么加速启动Activity。**
分两种情况，启动应用 和 普通Activity
启动应用 ：Application 的构造方法，onCreate 方法中不要进行耗时操作，数据预读取(例如 init 数据) 放在异步中操作
启动普通的Activity：A 启动B 时不要在 A 的 onPause 中执行耗时操作。因为 B 的 onResume 方法必须等待 A 的 onPause 执行完成后才能运行

**Android内存优化方法：ListView优化，及时关闭资源，图片缓存等等。**

**Android中弱引用与软引用的应用场景。**

**Bitmap的四种属性，与每种属性队形的大小。**

**View与View Group分类。自定义View过程：onMeasure()、onLayout()、onDraw()。**

**如何自定义控件：**
```
1. 自定义属性的声明和获取
    * 分析需要的自定义属性
    * 在res/values/attrs.xml定义声明
    * 在layout文件中进行使用
    * 在View的构造方法中进行获取
2. 测量onMeasure
3. 布局onLayout(ViewGroup)
4. 绘制onDraw
5. onTouchEvent
6. onInterceptTouchEvent(ViewGroup)
7. 状态的恢复与保存
```

**Android长连接，怎么处理心跳机制。**

**View树绘制流程**


**下拉刷新实现原理**

**Context区别**
```
* Activity和Service以及Application的Context是不一样的,Activity继承自ContextThemeWraper.其他的继承自ContextWrapper
* 每一个Activity和Service以及Application的Context都是一个新的ContextImpl对象
* getApplication()用来获取Application实例的，但是这个方法只有在Activity和Service中才能调用的到。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法，getApplicationContext()比getApplication()方法的作用域会更广一些，任何一个Context的实例，只要调用getApplicationContext()方法都可以拿到我们的Application对象。
* Activity在创建的时候会new一个ContextImpl对象并在attach方法中关联它，Application和Service也差不多。ContextWrapper的方法内部都是转调ContextImpl的方法
* 创建对话框传入Application的Context是不可以的
* 尽管Application、Activity、Service都有自己的ContextImpl，并且每个ContextImpl都有自己的mResources成员，但是由于它们的mResources成员都来自于唯一的ResourcesManager实例，所以它们看似不同的mResources其实都指向的是同一块内存
* Context的数量等于Activity的个数 + Service的个数 + 1，这个1为Application
```

**IntentService的使用场景与特点。**
```
IntentService是Service的子类，是一个异步的，会自动停止的服务，很好解决了传统的Service中处理完耗时操作忘记停止并销毁Service的问题
优点：
* 一方面不需要自己去new Thread
* 另一方面不需要考虑在什么时候关闭该Service

onStartCommand中回调了onStart，onStart中通过mServiceHandler发送消息到该handler的handleMessage中去。最后handleMessage中回调onHandleIntent(intent)。
```

**图片缓存**

---

### 面试案例
一面
* 自我介绍
* 介绍一下你的项目，项目中遇到哪些问题，如何解决的？
* Acticity的生命周期，Activity异常退出该如何处理？Fragment生命周期，嵌套
* Activity的启动模式
* 四大组件
* Android事件分发机制？
* 网络框架的搭建
* 如何判断本地缓存的时候数据需要从网络端获取
* tcp和udp的区别，tcp如何保证可靠的，丢包如何处理？
* AsyncTask机制
* Handler机制，Handler消息机制 ,postDelayed会造成线程阻塞吗？(不会)对内存有什么影响？
- Toolbar的使用
- SharedPreference原理
- 事件分发机制
- Android进程间通信，Binder机制
- 描述下Aidl？觉得aidl有什么缺陷（一般能答到这里就很不错了）
* 图片加载框架的实现
* 图片缓存，三级缓存底层实现？
* HashMap底层实现，hashCode如何对应bucket?
* 语言基础，String类可以被继承吗？为什么？
* Final能修饰什么？（当时我说class、field、method，他说还有吗？然后又叫我不要在意，后来回想起，应该是问到我在参数里面要不要用final，接下来是因为匿名内部类）
* Java中有内存泄露吗？（先说本质，再结合handler+匿名内部类）当时如何分析的？
* 熟不熟jvm，说一下Jvm的自动内存管理？
* Java的垃圾回收机制，引用计数法两个对象互相引用如何解决？
* 用过的开源框架的源码分析，GSON原理，RxJava+Retrofit原理
* Android中ClassLoader和java中有什么关系和区别？
* 写个图片浏览器，说出你的思路

二面
* Activity生命周期？情景：现在在一张act1点了新的act2，周期如何？
* Act的launchMode，有没有结合项目用过（自己的程序锁和微信的PC端登陆对比，不过我现在又发现，应该大约估计可能是动态加载的一个缺陷，如果有找到相关信息，请务必跟我说。具体问题就是，当在PC端登录时，Android终端的微信会跳出，即使wechat的task不是在fore，当按下确认，返回的是wechat，而不是自己先前的app）
* View的绘制原理，有没有用canvas自己画过ui？
- 面试官写程序让判断GC引用计数法循环引用会发生什么情况
- Debug和Release状态的不同
* 写代码，LeetCode上股票利益最大化问题
* 写代码，剑指offer上第一次只出现一次的字符
* 写代码，反转字符串
* 写代码，字符串中出现最多的字符。
* 标号1-n的n个人首尾相接，1到3报数，报到3的退出，求最后一个人的标号
* 给定一个字符串，求第一个不重复的字符 abbcad -> c
* 实现stack 的pop和push接口 要求： 1.用基本的数组实现 2.考虑范型 3.考虑下同步问题 4.考虑扩容问题
