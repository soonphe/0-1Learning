# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## android-overview

### Android架构
Android架构主要分为分为四部分，从下往上以此为
- LINUX KERNEL(内核层)
- LIBRARIES(共享库，以及android runtime运行时库)
- APPLICATION FRAMEWORK(应用框架层)
- APPLICATION(应用程序)

#### Linux Kernel
Android 的核心系统服务依赖于 Linux 内核，如安全性，内存管理，进程管理， 网络协议栈和驱动模型。 Linux 内核也同时作为硬件和软件栈之间的抽象层。

#### Libraries
Android 包含一些C/C++库，这些库能被Android系统中不同的组件使用。它们通过 Android 应用程序框架为开发者提供服务。以下是一些核心库：

- 系统 C 库：一个从 BSD 继承来的标准 C 系统函数库( libc )， 它是专门为基于 embedded linux 的设备定制的。
- 媒体库：基于 PacketVideo OpenCORE。该库支持多种常用的音频、视频格式回放和录制，同时支持静态图像文件。编码格式包括MPEG4，H.264，MP3，AAC，AMR，JPG，PNG。
- Surface Manager：对显示子系统的管理，并且为多个应用程序提供了2D和3D图层的无缝融合。
- LibWebCore：一个最新的web浏览器引擎用，支持Android浏览器和一个可嵌入的web视图。
- SGL：底层的2D图形引擎
- 3D libraries：基于OpenGL ES 1.0 APIs实现，该库可以使用硬件3D加速(如果可用)或者使用高度优化的3D软加速。
- FreeType：位图(bitmap)和矢量(vector)字体显示。

SQLite：一个对于所有应用程序可用，功能强劲的轻型关系型数据库引擎。

#### Android Runtime
Android 包括了一个核心库，该核心库提供了JAVA编程语言核心库的大多数功能。每一个Android应用程序都在它自己的进程中运行，都拥有一个独立的Dalvik虚拟机实例。Dalvik被设计成一个设备可以同时高效地运行多个虚拟系统。 Dalvik虚拟机执行(.dex)的Dalvik可执行文件，该格式文件针对小内存使用做了优化。同时虚拟机是基于寄存器的，所有的类都经由JAVA编译器编译，然后通过SDK中 的 “dx” 工具转化成.dex格式由虚拟机执行。Dalvik虚拟机依赖于linux内核的一些功能，比如线程机制和底层内存管理机制。

#### APPLICATION FRAMEWORK
Android的应用程序框架为应用程序层的开发者提供APIs，它实际上是一个应用程序的框架。由于上层的应用程序是以JAVA构建的，因此本层次提供了以下服务：

- 丰富而又可扩展的视图(Views)，可以用来构建应用程序， 它包括列表(lists)，网格(grids)，文本框(text boxes)，按钮(buttons)， 甚至可嵌入的web浏览器；
- 内容提供器(Content Providers)使得应用程序可以访问另一个应用程序的数据(如联系人数据库)，或者共享它们自己的数据；
- 资源管理器(Resource Manager)提供非代码资源的访问，如本地字符串，图形，和布局文件( layout files )；
- 通知管理器 (Notification Manager) 使得应用程序可以在状态栏中显示自定义的提示信息；
- 活动管理器( Activity Manager) 用来管理应用程序生命周期并提供常用的导航回退功能。

#### APPLICATION
主要为系统中的应用，如桌面，闹铃，设置，日历，电话，短信等系统应用。

### ART和Dalvik区别
- Dalvik：Dalvik是Google公司自己设计用于Android平台的Java虚拟机。Dalvik虚拟机是Google等厂商合作开发的Android移动设备平台的核心组成部分之一，它可以支持已转换为.dex(即Dalvik Executable)格式的Java应用程序的运行，.dex格式是专为Dalvik应用设计的一种压缩格式，适合内存和处理器速度有限的系统。Dalvik经过优化，允许在有限的内存中同时运行多个虚拟机的实例，并且每一个Dalvik应用作为独立的Linux进程执行。独立的进程可以防止在虚拟机崩溃的时候所有程序都被关闭。
- ART:Android操作系统已经成熟，Google的Android团队开始将注意力转向一些底层组件，其中之一是负责应用程序运行的Dalvik运行时。Google开发者已经花了两年时间开发更快执行效率更高更省电的替代ART运行时。ART代表Android Runtime,其处理应用程序执行的方式完全不同于Dalvik，Dalvik是依靠一个Just-In-Time(JIT)编译器去解释字节码。开发者编译后的应用代码需要通过一个解释器在用户的设备上运行，这一机制并不高效，但让应用能更容易在不同硬件和架构上运行。ART则完全改变了这套做法，在应用安装的时候就预编译字节码到机器语言，这一机制叫Ahead-Of-Time(AOT)编译。在移除解释代码这一过程后，应用程序执行将更有效率，启动更快。

ART: Ahead of Time Dalvik: Just in Time ，Art上应用启动快，运行快，但是耗费更多存储空间，安装时间长，总的来说ART的功效就是"空间换时间"。

ART优点：
1. 系统性能的显著提升
2. 应用启动更快、运行更快、体验更流畅、触感反馈更及时
3. 更长的电池续航能力
4. 支持更低的硬件

ART缺点：
1. 更大的存储空间占用，可能会增加10%-20%
2. 更长的应用安装时间

### Android Framework
- Framework的范围 ：Framework负责APPLICATION FRAMEWORK、ANDROID RUNTIME和LIBRARIES三部分。
    - 系统Manager和Service相关内容
    - 系统接口和jni相关内容
    - 系统功能相关内容（watchdog、vold、binder等）
    - 虚拟机dalvik、art
    - 系统so库相关内容
    - CTS、GTS等预分析
    - Monkey预分析
    - 系统稳定性问题（系统ANR、冻屏、重启、蓝屏等）
    - 系统性能问题

- 系统服务 ：SystemServer 是framework中非常重要的一个进程，它是在虚拟机启动后运行的第一个java进程，SystemServer启动其他系统服务，这些系统服务都是以一个线程的方式存在于SystemServer进程中。
    - EntropyService 提供伪随机数
    - PowerManagerService 电源管理服务
    - ActivityManagerService 最核心的服务之一，管理Activity
    - TelephonyRegistry 通过该服务注册电话模块的事件响应，比如重启、关闭、启动等
    - PackageManagerService 程序包管理服务
    - AccountManagerService 账户管理服务，是指联系人账户，而不是Linux系统的账户
    - ContentService ContentProvider服务，提供跨进程数据交换
    - BatteryService 电池管理服务
    - LightsService 自然光强度感应传感器服务
    - VibratorService 震动器服务
    - AlarmManagerService 定时器管理服务，提供定时提醒服务
    - WindowManagerService Framework最核心的服务之一，负责窗口管理
    - BluetoothService 蓝牙服务
    - DevicePolicyManagerService 提供一些系统级别的设置及属性
    - StatusBarManagerService 状态栏管理服务
    - ClipboardService 系统剪切板服务
    - InputMethodManagerService 输入法管理服务
    - NetStatService 网络状态服务
    - NetworkManagementService 网络管理服务
    - ConnectivityService 网络连接管理服务
    - AccessibilityManagerService 辅助管理程序截获所有的用户输入，并根据这些输入给用户一些额外的反馈，起到辅助的效果
    - MountService 挂载服务，可通过该服务调用Linux层面的mount程序
    - NotificationManagerService 通知栏管理服务，Android中的通知栏和状态栏在一起，只是界面上前者在左边，后者在右边
    - DeviceStorageMonitorService 磁盘空间状态检测服务
    - LocationManagerService 地理位置服务
    - SearchManagerService 搜索管理服务
    - DropBoxManagerService 通过该服务访问Linux层面的Dropbox程序
    - WallpaperManagerService 墙纸管理服务，墙纸不等同于桌面背景，在View系统内部，墙纸可以作为任何窗口的背景
    - AudioService 音频管理服务
    - BackupManagerService 系统备份服务
    - AppWidgetService         Widget服务
    - RecognitionManagerService 身份识别服务
    - DiskStatsService 磁盘统计服务

- 核心服务介绍
    - ActivityManagerService ：ActivityManagerService（以下简称：AMS）是android系统的一个系统服务，是应用进程的管理服务端，直接的控制了应用程序的各个行为，保证了系统中不同的应用程序之间能够和谐的合理的进行调度运行。
        - AMS是android上层系统最核心的模块之一，其主要的工作是对所有的应用进程及其进程中的四大组件进行管理。(当然这里面也涉及了一些window、电源、权限等内容)
        - 对进程的管理包括：进程的创建与销毁、进程的优先级调整。
        - 对组件的管理包括：Activity的调度管理、Service的管理、Broadcast的分发、以及ContentProvider管理。
    - WindowManagerService ：对系统中所有窗口进行管理；动画处理 ；Input分发、处理；Display管理（多屏显示） 。
    - Android Graphics系统
        - Surfaceflinger ：负责Layer合成（composer）；创建surface；管理surface。
        - PackageManagerService ：负责Package的管理，应用程序的安装 、卸载、信息查询等。
    - Input系统
        - android的输入系统主要完成键盘、触屏、鼠标等输入设备的事件输入及向焦点窗口和焦点视图的事件派发，插入，过滤，拦截等功能。
        - android支持的输入设备主要有：键盘、鼠标、触摸屏、轨迹球、游戏摇杆/手柄、绘图板。


### Gradle

#### Gradle wrapper
Gradle wrapper：用来管理版本与相关控制
- gradle-wrapper.jar ，是使用gradle-wrapper的一个辅助库
- gradle-wrapper.properties：是gradle-wrapper的配置文件，里面配置了gradle的版本，首次导入项目的时候AS默认是使用 gradle-wrapper.properties 中默认的设置，它会从网上下载所需要的对应版本的 gradle-wrapper.jar

**注意：Gradle wrapper配置必须与项目中`build.gradle`引入的Gradle plugin版本匹配，否则编译就会失败**
配置示例：
```
- gradle-5.4.1-all.zip 对应的 com.android.tools.build:gradle:3.5.3
- gradle-6.7.1-all.zip 对应的 com.android.tools.build:gradle:4.2.2

不同的gradle插件支持的compileSdkVersion版本不同，相关的插件如：kotlin-gradle-plugin也不相同
最新gradle插件：com.android.tools.build:gradle:8.1.0
```

#### Gradle plugin 
Gradle plugin：位于项目的根目录下的build.gradle中配置的插件
> gradle配置必须与Gradle wrapper版本不匹配，编译就会失败。

#### gradlew 
gradlew：W意思是wrapper，它是一个用bash命令包装过的gradle编译启动脚本，里面会进行环境变量检测和设置。最终进行编译的还是gradle

./gradlew命令是Gradle Wrapper的命令行工具，‌它允许开发者在没有任何预先安装的Gradle版本的情况下运行构建。‌Gradle Wrapper确保了构建的一致性，‌因为它使用固定的Gradle版本，‌避免了不同机器上可能存在的版本冲突问题。‌使用Gradle Wrapper可以简化构建过程，‌因为它会自动下载并使用正确的Gradle版本进行构建。‌

Gradle Wrapper命令的基本用法包括：‌
- ./gradlew -v：‌显示Gradle Wrapper的版本信息。‌
- ./gradlew build：‌构建整个项目并生成可执行文件。‌
- ./gradlew clean：‌清除构建目录下的build文件夹。‌
- ./gradlew test：‌运行项目的所有单元测试。‌
- ./gradlew assemble：‌生成项目的输出文件，‌如jar或war文件。‌
此外，‌Gradle Wrapper还支持其他命令选项，‌如--stop、‌--version、‌--info、‌--debug、‌--offline和--no-daemon等，‌这些选项提供了更灵活的控制构建过程的方式。‌例如，‌./gradlew --offline允许在没有网络连接的情况下继续构建项目，‌而./gradlew --no-daemon则指示Gradle在守护进程模式下运行。‌

在使用Gradle Wrapper时，‌开发者还可以指定具体的任务或子项目进行构建。‌例如，‌要为所有子项目运行测试任务，‌可以使用命令./gradlew test。‌如果需要为特定的子项目运行构建任务，‌可以使用:subproject:taskName的格式，‌如./gradlew :submodule-name:build。‌这种灵活性使得Gradle Wrapper成为管理和构建复杂项目时的强大工具12。‌


### Android 依赖

#### jar与arr
Jar包里面只有代码，aar里面不光有代码还包括代码还包括资源文件，比如 drawable 文件，xml 资源文件。对于一些不常变动的 Android Library，我们可以直接引用 aar，加快编译速度

#### multidex
默认情况下，Android 应用程序支持 SingleDex，这会限制您的应用程序只有 65536 个方法（引用）。所以 multidexEnabled = true 只是意味着现在您可以在应用程序中编写超过 65536 个方法（引用）

project根目录下配置：
```
android {  
  defaultConfig {  
  // Enabling multidex support.  
    multiDexEnabled true  
  }  
}
```

app下配置：
dependencies {  compile 'com.google.android:multidex:0.1'}


如果你的工程中已经含有Application类,那么让它继承android.support.multidex.MultiDexApplication类,
如果你的Application已经继承了其他类并且不想做改动，那么还有另外一种使用方式,覆写attachBaseContext()方法:

public class MyApplication extends FooApplication {  
@Override  
protected void attachBaseContext(Context base) {  
super.attachBaseContext(base);  
MultiDex.install(this);  
}  
}


### 编译、签名与打包

#### android签名keystore和jks区别
keystore 是Eclipse 打包生成的签名。
jks是Android studio 生成的签名。


#### aar文件
AAR是Google为Android Studio专门推出的一种库文件格式
- *.jar：只包含了class文件与清单文件，不包含资源文件，如图片、布局等所有res中的文件。
- *.aar：包含所有资源，class以及res资源等全部文件。

**引入aar文件**
1. 将要集成的AAR文件拷贝到工程的libs目录下；
2. 在项目工程的build.gradle配置文件中做以下配置：
```
repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
 implementation(name: 'IMeasureSDK', ext: 'aar')
}
```

**android 生成一个aar文件：**
1. 创建一个 Android Library 项目，在 Android Studio 中，点击 “File” -> “New” -> “New Project”，然后选择 “Android Library” 作为项目类型。输入项目的名称和路径，并选择适当的设置。
2. 配置 Library 项目
  - 添加需要的第三方库的依赖项。
  - 设置合适的 minSdkVersion 和 targetSdkVersion。
  - 在 Library 项目的 build.gradle 文件中，确保 apply plugin: 'com.android.library' 已被添加。
3. 然后要引入你的项目中，在setting.gradle 中加上 include ':你的库名' 和 app build.gradle 下引入 implementation project(path: ':你的库名') ,不同gradle 版本可能写法不一样
4. 生成 AAR 包，打开你android studio 的 命令输入界面，项目根目录，输入以下命令：
```
./gradlew assemble
```
这将生成一个AAR文件，并将其放置在.gradle_bulid/<your_module_name>/outputs/aar/目录下。
这里同时有Release包 和Debug包，二者还是有差别的，你直接在Android Studio 里安装app 就会在 build/outputs/aar/ 目录下生成 Debug包的，当然，正式环境最好还是用 Release 包。

**注意：**
如果报错：no such file or directory: ./gradlew ，解决：那就是缺少文件，引入gradlew和gradlew.bat文件即可

#### so文件
Android 系统本质是一个经过改造的 Linux 系统，so库是Linux系统上使用的共享库（类似windows上的dll）。
最早，Android 系统只支持 ARMv5 的 CPU 构架，随着 Android 系统的发展，又加入了 ARMv7 (2010), x86 (2011), MIPS (2012), ARMv8, MIPS64 和 x86_64 (2014)。
每一种 CPU 构架，都定义了一种 ABI（Application Binary Interface），ABI 决定了二进制文件如何与系统进行交互

| CPU架构       | 描述                                        |
|-------------|-------------------------------------------|
| armeabi     | 第5代ARMv5TE，使用软件浮点运算，兼容所有ARM设备，通用性强，速度慢    |
| armeabi-v7a | 第7代ARMv7，使用硬件浮点运算，具有高级扩展功能                |
| arm64-v8a   | 第8代，64位，包含AArch32、AArch64两个执行状态对应32、64bit |
| x86         | intel 32位，一般用于平板                          |
| x86_64      | intel 64位，一般用于平板                          |
| mips        | 少接触                                       |
| mips64      | 少接触                                       |

**使用so文件**
1. 在build文件中配置
```
defaultConfig {
        ndk {
            abiFilters "armeabi", "armeabi-v7a","arm64-v8a", "x86", "x86_64", "mips", "mips64"
        }
    }
 
 
 
//    读取libs中的so文件
    sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
```
为什么要设置ndk的abiFilters
其实这个可以不设置，这样编译时，就会将项目里所有依赖资源包里的so库都打到最终的apk里。
但是有些平台，我们是不需要支持的，如果不删除的话，apk就臃肿了。如果那些so库是我们自己编译出来的，那可以直接在工程中删除对应so文件，但是如果是第三方提供的，就不好删除了，所以就需要使用abiFilters来过滤了。
如果需要针对不同的平台出不同的包，可以在productFlavors里进行设置

armeabi、armeabi-v7a、arm64-v8a的兼容性问题
看上面的描述，以为新增一个so库文件可以随便根据需要适配的目录放，就错了。如果你有库文件在armeabi里有，但是armeabi-v7a目录下没有，那么运行在V7a的架构时，就会出现找不到so库文件的情况。具体描述参照：Android 关于arm64-v8a、armeabi-v7a、armeabi、x86下的so文件兼容问题。

正确的做法
当前市面绝大多数是arm的CPU，而且都是V7架构的了，所以可以保留armeabi或者armeabi-v7a即可。
如果仅保留armeabi-v7a，而有些第三方包未提供v7a的包，则可以将对应armeabi包拷贝到armeabi-v7a。
如果同时保留armeabi和armeabi-v7a，则需要保证两个目录下的so库文件数相同。

#### Android更新低版本
Android不能更新低版本
如果一个新版本的应用的versionCode值不大于之前旧版本的versionCode值，‌系统会提示无法安装，‌因为这违反了Android的系统规则，‌即低版本的APK不能直接覆盖安装高版本的APK。
即使versionName相同，只要versionCode不同（且新APK的versionCode更大），那么就可以进行覆盖安装。但如果versionCode也相同，那么系统就会认为这两个APK是同一个版本，从而拒绝安装或更新。

#### APK打包
Build——Generate Signed APK——选择keystore——输入keyPasswrod等——选择debug/release

#### 获取当前SDK/系统版本号
系统版本号：android.os.Build.DISPLAY
String androidVersion = android.os.Build.VERSION.RELEASE;//android版本
String model = android.os.Build.MODEL;//型号
String brand = android.os.Build.BRAND;//品牌
String dis = android.os.Build.DISPLAY;//版本号

获取系统应用版本号：如1.0.0
PackageInfo packageInfo = context.getPackageManager().getPackageInfo(context.getPackageName(), 0);
versionCode = packageInfo.versionCode;

#### 如何把自己写的库发布到网上的仓库 Bintray/JCenter/JitPack
Bintray/JCenter/JitPack发布及配置流程
JitPack虽然是最简单的，但是他是基于把整个项目作为依赖的，Bintray/JCenter则可以上传单个模块作为依赖
推荐使用Bintray
发布流程：https://blog.csdn.net/u014780554/article/details/79628041?utm_source=blogxgwz1

### Android开机过程
* BootLoder引导,然后加载Linux内核.
* 0号进程init启动.加载init.rc配置文件,配置文件有个命令启动了zygote进程
* zygote开始fork出SystemServer进程
* SystemServer加载各种JNI库,然后init1,init2方法,init2方法中开启了新线程ServerThread.
* 在SystemServer中会创建一个socket客户端，后续AMS（ActivityManagerService）会通过此客户端和zygote通信
* ServerThread的run方法中开启了AMS,还孵化新进程ServiceManager,加载注册了一溜的服务,最后一句话进入loop 死循环
* run方法的SystemReady调用resumeTopActivityLocked打开锁屏界面

### Activity

#### Activity生命周期
* 启动Activity:
  onCreate()—>onStart()—>onResume()，Activity进入运行状态。
* Activity退居后台:
  当前Activity转到新的Activity界面或按Home键回到主屏：
  onPause()—>onStop()，进入停滞状态。
* Activity返回前台:
  onRestart()—>**onStart()**—>onResume()，再次回到运行状态。
* Activity退居后台，且系统内存不足，
  系统会杀死这个后台状态的Activity（此时这个Activity引用仍然处在任务栈中，只是这个时候引用指向的对象已经为null），若再次回到这个Activity,则会走onCreate()–>onStart()—>onResume()(将重新走一次Activity的初始化生命周期)
* 锁屏：`onPause()->onStop()`
* 解锁：`onStart()->onResume()`

#### Activity启动方式有四种
Activity栈：先进后出规则
* standard：默认模式，默认创建一个新的实例（在这种模式下，可以有多个相同的实例，也允许多个相同Activity叠加）
* singleTop：可以有多个实例，但是不允许多个相同Activity叠加（启动相同的Activity，不会创建新的实例，而会调用其onNewIntent方法）
* singleTask：只有一个实例。在同一个应用程序中启动他的时候，若Activity不存在，则会在当前task创建一个新的实例，若存在，则会把task中在其之上的其它Activity destory掉并调用它的onNewIntent方法（如果是在别的应用程序中启动它，则会新建一个task，并在该task中启动这个Activity）
* singleInstance：singleInstance只有一个实例，并且这个实例独立运行在一个task中，这个task只有这个实例，不允许有别的Activity存在。
  可以根据实际的需求为Activity设置对应的启动模式，从而可以避免创建大量重复的Activity等问题。

* 使用``android:launchMode="standard|singleInstance|singleTask|singleTop"``来控制Acivity任务栈。
  - standard : 标准模式,每次启动Activity都会创建一个新的Activity实例,并且将其压入任务栈栈顶,而不管这个Activity是否已经存在。Activity的启动三回调(*onCreate()->onStart()->onResume()*)都会执行。
  - singleTop : 栈顶复用模式.这种模式下,如果新Activity已经位于任务栈的栈顶,那么此Activity不会被重新创建,所以它的启动三回调就不会执行,同时Activity的``onNewIntent()``方法会被回调.如果Activity已经存在但是不在栈顶,那么作用与*standard模式*一样.
  - singleTask: 栈内复用模式.创建这样的Activity的时候,系统会先确认它所需任务栈已经创建,否则先创建任务栈.然后放入Activity,如果栈中已经有一个Activity实例,那么这个Activity就会被调到栈顶,``onNewIntent()``,并且singleTask会清理在当前Activity上面的所有Activity.(clear top)
  - singleInstance : 加强版的singleTask模式,这种模式的Activity只能单独位于一个任务栈内,由于栈内复用的特性,后续请求均不会创建新的Activity,除非这个独特的任务栈被系统销毁了
* Activity的堆栈管理以ActivityRecord为单位,所有的ActivityRecord都放在一个List里面.可以认为一个ActivityRecord就是一个Activity栈
* 任务栈是一种后进先出的结构。位于栈顶的Activity处于焦点状态,当按下back按钮的时候,栈内的Activity会一个一个的出栈,并且调用其`onDestory()`方法。如果栈内没有Activity,那么系统就会回收这个栈,每个APP默认只有一个栈,以APP的包名来命名.

#### Activity整个生命周期的4种状态、7个重要方法和3个嵌套循环

>四种状态

    活动（Active/Running）状态
当Activity运行在屏幕前台(处于当前任务活动栈的最上面),此时它获取了焦点能响应用户的操作,属于运行状态，同一个时刻只会有一个Activity 处于活动(Active)或运行(Running)状态

    暂停(Paused)状态
当Activity失去焦点但仍对用户可见(如在它之上有另一个透明的Activity或Toast、AlertDialog等弹出窗口时)它处于暂停状态。暂停的Activity仍然是存活状态(它保留着所有的状态和成员信息并保持和窗口管理器的连接),但是当系统内存极小时可以被系统杀掉

    停止(Stopped)状态
完全被另一个Activity遮挡时处于停止状态,它仍然保留着所有的状态和成员信息。只是对用户不可见,当其他地方需要内存时它往往被系统杀掉

    非活动（Dead）状态
Activity 尚未被启动、已经被手动终止，或已经被系统回收时处于非活动的状态，要手动终止Activity，可以在程序中调用"finish"方法。
如果是（按根据内存不足时的回收规则）被系统回收，可能是因为内存不足了，内存不足时，Dalvak 虚拟机会根据其内存回收规则来回收内存：

>7个重要方法

当Activity第一次被实例化的时候系统会调用,整个生命周期只调用1次这个方法
onCreate(Bundle savedInstanceState);

当Activity可见未获得用户焦点不能交互时系统会调用
onStart();

当Activity已经停止然后重新被启动时系统会调用
onRestart();

当Activity可见且获得用户焦点能交互时系统会调用
onResume();

当系统启动另外一个新的Activity时,在新Activity启动之前被系统调用保存现有的Activity中的持久数据、停止动画等
onPause();

当Activity被新的Activity完全覆盖不可见时被系统调用
onStop();

当Activity(用户调用finish()或系统由于内存不足)被系统销毁杀掉时系统调用,（整个生命周期只调用1次）用来释放onCreate ()方法中创建的资源,如结束线程等
onDestroy();

调用只有一个Activity的实例存在，调用的是Activity的onNewIntent(Intent intent)方法了
onNewIntent();


>3个嵌套循环
1. Activity完整的生命周期:从第一次调用onCreate()开始直到调用onDestroy()结束

2. Activity的可视生命周期:从调用onStart()到相应的调用onStop()
   在这两个方法之间,可以保持显示Activity所需要的资源。如在onStart()中注册一个广播接收者监听影响你的UI的改变,在onStop() 中注销。

3. Activity的前台生命周期:从调用onResume()到相应的调用onPause()。

#### Activity缓存方法 
有a、b两个Activity，当从a进入b之后一段时间，可能系统会把a回收，这时候按back，执行的不是a的onRestart而是onCreate方法，a被重新创建一次，这是a中的临时数据和状态可能就丢失了。
可以用Activity中的onSaveInstanceState()回调方法保存临时数据和状态，这个方法一定会在活动被回收之前调用。
方法中有一个Bundle参数，putString()、putInt()等方法需要传入两个参数，一个键一个值。数据保存之后会在onCreate中恢复，onCreate也有一个Bundle类型的参数。

示例代码：
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //这里，当Acivity第一次被创建的时候为空，所以我们需要判断一下
        //
        if( savedInstanceState != null ){
            savedInstanceState.getString("anAnt");
        }
    }

    //当某个activity变得“容易”被系统销毁时，该activity的onSaveInstanceState就会被执行，除非该activity是被用户主动销毁的，例如当用户按BACK键的时候。
    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putString("anAnt","Android");
        savedInstanceState.putBoolean("MyBoolean", true);
        savedInstanceState.putDouble("myDouble", 1.9);
        savedInstanceState.putInt("MyInt", 1);
        savedInstanceState.putString("MyString", "Welcome back to Android");
    }
    
    @Override
    public void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
    
        boolean myBoolean = savedInstanceState.getBoolean("MyBoolean");
        double myDouble = savedInstanceState.getDouble("myDouble");
        int myInt = savedInstanceState.getInt("MyInt");
        String myString = savedInstanceState.getString("MyString");
    }
```

一、onSaveInstanceState (Bundle outState)
其onSaveInstanceState方法会在什么时候被执行，有这么几种情况：
1、当用户按下HOME键时。 这是显而易见的，系统不知道你按下HOME后要运行多少其他的程序，自然也不知道activity A是否会被销毁，故系统会调用onSaveInstanceState，让用户有机会保存某些非永久性的数据。以下几种情况的分析都遵循该原则
2、长按HOME键，选择运行其他的程序时。
3、按下电源按键（关闭屏幕显示）时。
4、从activity A中启动一个新的activity时。
5、屏幕方向切换时，例如从竖屏切换到横屏时。（如果不指定configchange属性）

在屏幕切换之前，系统会销毁activity A，在屏幕切换之后系统又会自动地创建activity A，所以onSaveInstanceState一定会被执行,总而言之，onSaveInstanceState的调用遵循一个重要原则，即当系统“未经你许可”时销毁了你的activity，则onSaveInstanceState会被系统调用，这是系统的责任，因为它必须要提供一个机会让你保存你的数据（当然你不保存那就随便你了）。

另外，需要注意的几点：
1.布局中的每一个View默认实现了onSaveInstanceState()方法，这样的话，这个UI的任何改变都会自动地存储和在activity重新创建的时候自动地恢复。但是这种情况只有在你为这个UI提供了唯一的ID之后才起作用，如果没有提供ID，app将不会存储它的状态。
2.由于默认的onSaveInstanceState()方法的实现帮助UI存储它的状态，所以如果你需要覆盖这个方法去存储额外的状态信息，你应该在执行任何代码之前都调用父类的onSaveInstanceState()方法（super.onSaveInstanceState()）。 既然有现成的可用，那么我们到底还要不要自己实现onSaveInstanceState()?这得看情况了，如果你自己的派生类中有变量影响到UI，或你程序的行为，当然就要把这个变量也保存了，那么就需要自己实现，否则就不需要。
3.由于onSaveInstanceState()方法调用的不确定性，你应该只使用这个方法去记录activity的瞬间状态（UI的状态）。不应该用这个方法去存储持久化数据。当用户离开这个activity的时候应该在onPause()方法中存储持久化数据（例如应该被存储到数据库中的数据）。
4.onSaveInstanceState()如果被调用，这个方法会在onStop()前被触发，但系统并不保证是否在onPause()之前或者之后触发。

注意：
- onSaveInstanceState方法和onRestoreInstanceState方法“不一定”是成对的被调用的
- onRestoreInstanceState被调用的前提是，activity A“确实”被系统销毁了，而如果仅仅是停留在有这种可能性的情况下，则该方法不会被调用，例如，当正在显示activity A的时候，用户按下HOME键回到主界面，然后用户紧接着又返回到activity A，这种情况下activity A一般不会因为内存的原因被系统销毁，故activity A的onRestoreInstanceState方法不会被执行
- onRestoreInstanceState的bundle参数也会传递到onCreate方法中，你也可以选择在onCreate方法中做数据还原。
- onRestoreInstanceState在onstart之后执行。


#### Acitivity跳转 Finish返回跳转携带数据
//请求，并携带回调
~~~~
Intent intent=new Intent();
intent.setClass(getContext(), AddressCheckActivity.class);
startActivityForResult(intent,0x002);
~~~~

//请求，添加传参，finish当前activity
~~~~
Intent intent = new Intent();
intent.putExtra("receiveAddress", ((UmsAddress) adapter.getItem(position)).getTrxAddress());
setResult(0x001, intent);
finish();
~~~~


### Fragment
Fragment为何产生 :同时适配手机和平板、UI和逻辑的共享。

* Fragment也会被加入回退栈中。
* Fragment拥有自己的生命周期和接受、处理用户的事件
* 可以动态的添加、替换和移除某个Fragment

生命周期,必须依存于Activity，Fragment依附于Activity的生命状态

#### Fragment生命周期方法含义：
* `public void onAttach(Context context)`：onAttach方法会在Fragment于窗口关联后立刻调用。从该方法开始，就可以通过Fragment.getActivity方法获取与Fragment关联的窗口对象，但因为Fragment的控件未初始化，所以不能够操作控件。
* `public void onCreate(Bundle savedInstanceState)`：在调用完onAttach执行完之后立刻调用onCreate方法，可以在Bundle对象中获取一些在Activity中传过来的数据。通常会在该方法中读取保存的状态，获取或初始化一些数据。在该方法中不要进行耗时操作，不然窗口不会显示。
* `public View onCreateView(LayoutInflater inflater, ViewGroup container,Bundle savedInstanceState)`：该方法是Fragment很重要的一个生命周期方法，因为会在该方法中创建在Fragment显示的View，其中inflater是用来装载布局文件的，container是`<fragment>`标签的父标签对应对象，savedInstanceState参数可以获取Fragment保存的状态，如果未保存那么就为null。
* `public void onViewCreated(View view,Bundle savedInstanceState)`：Android在创建完Fragment中的View对象之后，会立刻回调该方法。其种view参数就是onCreateView中返回的view，而bundle对象用于一般用途。
* `public void onActivityCreated(Bundle savedInstanceState)`：在Activity的onCreate方法执行完之后，Android系统会立刻调用该方法，表示窗口已经初始化完成，从这一个时候开始，就可以在Fragment中使用getActivity().findViewById(Id);来操控Activity中的view了。
* `public void onStart()`：这个没啥可讲的，但有一个细节需要知道，当系统调用该方法的时候，fragment已经显示在ui上，但还不能进行互动，因为onResume方法还没执行完。
* `public void onResume()`：该方法为fragment从创建到显示Android系统调用的最后一个生命周期方法，调用完该方法时候，fragment就可以与用户互动了。
* `public void onPause()`：fragment由活跃状态变成非活跃状态执行的第一个回调方法，通常可以在这个方法中保存一些需要临时暂停的工作。如保存音乐播放进度，然后在onResume中恢复音乐播放进度。
* `public void onStop()`：当onStop返回的时候，fragment将从屏幕上消失。
* `public void onDestoryView()`：该方法的调用意味着在 `onCreateView` 中创建的视图都将被移除。
* `public void onDestroy()`：Android在Fragment不再使用时会调用该方法，要注意的是~这时Fragment还和Activity藕断丝连！并且可以获得Fragment对象，但无法对获得的Fragment进行任何操作（呵~呵呵~我已经不听你的了）。
* `public void onDetach()`：为Fragment生命周期中的最后一个方法，当该方法执行完后，Fragment与Activity不再有关联(分手！我们分手！！(╯‵□′)╯︵┻━┻)。

#### Fragment比Activity多了几个额外的生命周期回调方法：
* onAttach(Activity):当Fragment和Activity发生关联时使用
* onCreateView(LayoutInflater, ViewGroup, Bundle):创建该Fragment的视图
* onActivityCreate(Bundle):当Activity的onCreate方法返回时调用
* onDestoryView():与onCreateView相对应，当该Fragment的视图被移除时调用
* onDetach():与onAttach相对应，当Fragment与Activity关联被取消时调用

注意：除了onCreateView，其他的所有方法如果你重写了，必须调用父类对于该方法的实现

#### Fragment与Activity之间的交互
* Fragment与Activity之间的交互可以通过`Fragment.setArguments(Bundle args)`以及`Fragment.getArguments()`来实现。

#### Fragment状态的持久化
由于Activity会经常性的发生配置变化，所以依附它的Fragment就有需要将其状态保存起来问题。下面有两个常用的方法去将Fragment的状态持久化。

* 方法一：
  * 可以通过`protected void onSaveInstanceState(Bundle outState)`,`protected void onRestoreInstanceState(Bundle savedInstanceState)` 状态保存和恢复的方法将状态持久化。
* 方法二(更方便,让Android自动帮我们保存Fragment状态)：
  * 我们只需要将Fragment在Activity中作为一个变量整个保存，只要保存了Fragment，那么Fragment的状态就得到保存了，所以呢.....
    * `FragmentManager.putFragment(Bundle bundle, String key, Fragment fragment)` 是在Activity中保存Fragment的方法。
    * `FragmentManager.getFragment(Bundle bundle, String key)` 是在Activity中获取所保存的Frament的方法。
  * 很显然，key就传入Fragment的id，fragment就是你要保存状态的fragment，但，我们注意到上面的两个方法，第一个参数都是Bundle，这就意味着*FragmentManager*是通过Bundle去保存Fragment的。但是，这个方法仅仅能够保存Fragment中的控件状态，比如说EditText中用户已经输入的文字（*注意！在这里，控件需要设置一个id，否则Android将不会为我们保存控件的状态*），而Fragment中需要持久化的变量依然会丢失，但依然有解决办法，就是利用方法一！
  * 下面给出状态持久化的事例代码：
    ```		
        @Override
        protected void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.fragment_activity);
            if( savedInstanceState != null ){
                fragmentB = (FragmentB) getSupportFragmentManager().getFragment(savedInstanceState,"fragmentB");
            }
            init();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            if( fragmentB != null ){
                getSupportFragmentManager().putFragment(outState,"fragmentB",fragmentB);
            }
            super.onSaveInstanceState(outState);
        }

        /** Fragment中保存变量的代码 **/
        @Nullable
        @Override
        public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            AppLog.e("onCreateView");
            if ( null != savedInstanceState ){
                String savedString = savedInstanceState.getString("string");
                //得到保存下来的string
            }
            View root = inflater.inflate(R.layout.fragment_a,null);
            return root;
        }

        @Override
        public void onSaveInstanceState(Bundle outState) {
            outState.putString("string","anAngryAnt");
            super.onSaveInstanceState(outState);
        }
    ```

#### 静态的使用Fragment
1. 继承Fragment，重写onCreateView决定Fragment的布局
2. 在Activity中声明此Fragment,就和普通的View一样

#### Fragment常用的API
* android.support.v4.app.Fragment 主要用于定义Fragment
* android.support.v4.app.FragmentManager 主要用于在Activity中操作Fragment，可以使用FragmentManager.findFragmenById，FragmentManager.findFragmentByTag等方法去找到一个Fragment
* android.support.v4.app.FragmentTransaction 保证一些列Fragment操作的原子性，熟悉事务这个词

主要的操作都是FragmentTransaction的方法(一般我们为了向下兼容，都使用support.v4包里面的Fragment),getFragmentManager() // Fragment若使用的是support.v4包中的，那就使用getSupportFragmentManager代替
```
	FragmentTransaction transaction = fm.benginTransatcion();//开启一个事务
	transaction.add() 
	//往Activity中添加一个Fragment

	transaction.remove() 
	//从Activity中移除一个Fragment，如果被移除的Fragment没有添加到回退栈（回退栈后面会详细说），这个Fragment实例将会被销毁。

	transaction.replace()
	//使用另一个Fragment替换当前的，实际上就是remove()然后add()的合体~

	transaction.hide()
	//隐藏当前的Fragment，仅仅是设为不可见，并不会销毁

	transaction.show()
	//显示之前隐藏的Fragment

	detach()
	//当fragment被加入到回退栈的时候，该方法与*remove()*的作用是相同的，
	//反之，该方法只是将fragment从视图中移除，
	//之后仍然可以通过*attach()*方法重新使用fragment，
	//而调用了*remove()*方法之后，
	//不仅将Fragment从视图中移除，fragment还将不再可用。

	attach()
	//重建view视图，附加到UI上并显示。

	transatcion.commit()
	//提交一个事务
```

#### 管理Fragment回退栈
```
* 跟踪回退栈状态
  * 我们通过实现*``OnBackStackChangedListener``*接口来实现回退栈状态跟踪，具体如下
  public class XXX implements FragmentManager.OnBackStackChangedListener 

  /** 实现接口所要实现的方法 **/
 @Override
 public void onBackStackChanged() {
     //do whatevery you want
 }
 /** 设置回退栈监听接口 **／
 getSupportFragmentManager().addOnBackStackChangedListener(this);
```
* 管理回退栈
  * ``FragmentTransaction.addToBackStack(String)`` *--将一个刚刚添加的Fragment加入到回退栈中*
  * ``getSupportFragmentManager().getBackStackEntryCount()`` *－获取回退栈中实体数量*
  * ``getSupportFragmentManager().popBackStack(String name, int flags)`` *－根据name立刻弹出栈顶的fragment*
  * ``getSupportFragmentManager().popBackStack(int id, int flags)`` *－根据id立刻弹出栈顶的fragment*

### UI界面

#### Andorid 分辨率
dpi范围	密度
0dpi ~ 120dpi	ldpi
120dpi ~ 160dpi	mdpi	1
160dpi ~ 240dpi	hdpi	1.5
240dpi ~ 320dpi	xhdpi	2
320dpi ~ 480dpi	xxhdpi	3
480dpi ~ 640dpi	xxxhdpi	4

**ico尺寸**
密度	建议尺寸
mipmap-mdpi	48 * 48
mipmap-hdpi	72 * 72
mipmap-xhdpi	96 * 96
mipmap-xxhdpi	144 * 144
mipmap-xxxhdpi	192 * 192

**不同分辨率图片放大缩小规则：**
- 图片所有文件dpi x 图片尺寸 = 手机dpi
- 图片所有文件dpi越小，图片尺寸越大
- 图片所有文件dpi越大，图片尺寸越小
- 放大倍数未dpi之间的倍数

> 占用内存的算法是 (图片所属资源密度 / 手机DPI * 原始图片宽度) * (图片所属资源密度 / 手机DPI * 原始图片高度)

adb查看手机分辨率：
```
adb shell dumpsys window displays

结果：init=1080x1920 420dpi cur=1080x1920 app=1080x1920 rng=1080x1017-1920x1857
```

#### Android五种基础布局 
全都继承自ViewGroup
- FrameLayout ：(框架布局)此布局是五种布局中最简单的布局，Android中并没有对child view的摆布进行控制，这个布局中所有的控件都会默认出现在视图的左上角，我们可以使用``android:layout_margin``，``android:layout_gravity``等属性去控制子控件相对布局的位置。
- LinearLayout：(线性布局)一行（或一列）只控制一个控件的线性布局，所以当有很多控件需要在一个界面中列出时，可以用LinearLayout布局。 此布局有一个需要格外注意的属性:``android:orientation=“horizontal|vertical``。
  * 当`android:orientation="horizontal`时，*说明你希望将水平方向的布局交给**LinearLayout** *，其子元素的`android:layout_gravity="right|left"` 等控制水平方向的gravity值都是被忽略的，*此时**LinearLayout**中的子元素都是默认的按照水平从左向右来排*，我们可以用`android:layout_gravity="top|bottom"`等gravity值来控制垂直展示。
  * 反之，可以知道 当`android:orientation="vertical`时，**LinearLayout**对其子元素展示上的的处理方式。
- AbsoluteLayout ：(绝对布局)可以放置多个控件，并且可以自己定义控件的x,y位置
- RelativeLayout ：(相对布局)这个布局也是相对自由的布局，Android 对该布局的child view的 水平layout& 垂直layout做了解析，由此我们可以FrameLayout的基础上使用标签或者Java代码对垂直方向 以及 水平方向 布局中的views进行任意的控制.
  - 相关属性：`android:layout_centerInParent="true|false"`，`android:layout_centerHorizontal="true|false"`，`android:layout_alignParentRight="true|false"`
- TableLayout ：(表格布局)将子元素的位置分配到行或列中，一个TableLayout由许多的TableRow组成

#### Android图片中的三级缓存
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

#### android Bitmap绘制图片
```
绘制照片：
文件解码为位图：
Bitmap src = BitmapFactory.decodeFile(filePath);
创建可变位图对象:
Bitmap result = Bitmap.createBitmap(src.getWidth(), src.getHeight(), src.getConfig());
绘制位图：
Canvas canvas = new Canvas(result);
canvas.drawBitmap(src, 0, 0, null);
创建缩放位图对象：
result = Bitmap.createScaledBitmap(result, 960, 540, true);
String watermarkFilePath = filePath.replace(".jpg", "_small.jpg");
文件压缩：
FileOutputStream out = new FileOutputStream(watermarkFilePath)
result.compress(Bitmap.CompressFormat.JPEG, 80, out);
```

#### Android自定义Dialog
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

#### Andorid自定义View
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

#### PopupWindow和AlertDialog区别
PopupWindow和AlertDialog区别
本质区别为：AlertDialog是非阻塞式对话框：AlertDialog弹出时，后台还可以做事情；
而PopupWindow是阻塞式对话框：PopupWindow弹出时，程序会等待，在PopupWindow退出前，程序一直等待，只有当我们调用了dismiss方法的后，PopupWindow退出，程序才会向下执行。
这两种区别的表现是：AlertDialog弹出时，背景是黑色的，但是当我们点击背景，AlertDialog会消失，证明程序不仅响应AlertDialog的操作，还响应其他操作，其他程序没有被阻塞，这说明了AlertDialog是非阻塞式对话框；
PopupWindow弹出时，背景没有什么变化，但是当我们点击背景的时候，程序没有响应，只允许我们操作PopupWindow，其他操作被阻塞。

PopupWindow pw = new PopupWindow(view,width,height);
pw.setContentView(popupconten);//重新设置PopupWindow的内容
pw.setFocusable(true);//默认是false，为false时，PopupWindow没有获得焦点能力，如果这是PopupWindow的内容中有EidtText，需要输入，这是是无法输入的；只有为true的时候，PopupWindow才具有获得焦点能力，EditText才是真正的EditText。

pw.setAsDropDown(View view);//设置PopupWindow弹出的位置。


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

### RecycleBin机制

RecycleBin机制是ListView能够实现成百上千条数据都不会OOM最重要的一个原因。RecycleBin是AbsListView的一个内部类。

* RecycleBin当中使用mActiveViews这个数组来存储View，调用这个方法后就会根据传入的参数来将ListView中的指定元素存储到mActiveViews中。
* mActiveViews当中所存储的View，一旦被获取了之后就会从mActiveViews当中移除，下次获取同样位置的时候将会返回null，所以mActiveViews不能被重复利用。
* addScrapView()用于将一个废弃的View进行缓存，该方法接收一个View参数，当有某个View确定要废弃掉的时候（比如滚动出了屏幕）就应该调用这个方法来对View进行缓存，RecycleBin当中使用mScrapV
* iews和mCurrentScrap这两个List来存储废弃View。
* getScrapView 用于从废弃缓存中取出一个View，这些废弃缓存中的View是没有顺序可言的，因此getScrapView()方法中的算法也非常简单，就是直接从mCurrentScrap当中获取尾部的一个scrap view进行返回。
* 我们都知道Adapter当中可以重写一个getViewTypeCount()来表示ListView中有几种类型的数据项，而setViewTypeCount()方法的作用就是为每种类型的数据项都单独启用一个RecycleBin缓存机制。

View的流程分三步，onMeasure()用于测量View的大小，onLayout()用于确定View的布局，onDraw()用于将View绘制到界面上。

### mkkv
tencent.mmkv：键值对存储系统
使用方式：
```
MMKV kv=MMKVService.getKV();
kv.putString(MMKVService.DEVICE_CFG,ServiceLocator.getGson().toJson(deviceCfgInformation));
```
* 依赖：implementation 'com.tencent:mmkv:1.3.1'
- 初始化：String rootDir = MMKV.initialize(this);
- 获取全局实例：        MMKV mmkv = MMKV.defaultMMKV();
- 存储数据：mmkv.encode("Id",value);
- 读取数据：        int idValue =  mmkv.decodeInt("Id");
* 注意事项：MMKV可以存储各种类型的数据，包括String、Int、Float、Double、 ByteArray等。您只需要根据需要使用相应的encode和decode方法


### android进程与线程

#### android进程
1. 前台进程：即与用户正在交互的Activity或者Activity用到的Service等，如果系统内存不足时前台进程是最后被杀死的
2. 可见进程：可以是处于暂停状态(onPause)的Activity或者绑定在其上的Service，即被用户可见，但由于失去了焦点而不能与用户交互
3. 服务进程：其中运行着使用startService方法启动的Service，虽然不被用户可见，但是却是用户关心的，例如用户正在非音乐界面听的音乐或者正在非下载页面自己下载的文件等；当系统要空间运行前两者进程时才会被终止
4. 后台进程：其中运行着执行onStop方法而停止的程序，但是却不是用户当前关心的，例如后台挂着的QQ，这样的进程系统一旦没了有内存就首先被杀死
5. 空进程：不包含任何应用程序的程序组件的进程，这样的进程系统是一般不会让他存在的

如何避免后台进程被杀死：

1. 调用startForegound，让你的Service所在的线程成为前台进程
2. Service的onStartCommond返回START_STICKY或START_REDELIVER_INTENT
3. Service的onDestroy里面重新启动自己

#### android 主线程与工作线程
activity中更新UI.runOnUiThread
```
            MainActivity.this.runOnUiThread(() -> {
                layout_container.setBackgroundResource(R.color.bg);
            });
```

rxjava中io线程与主线程：
```
            Observable.interval(1, TimeUnit.SECONDS)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                    .subscribe(aLong -> layout_container.setBackgroundResource(R.color.white));
```
- Schedulers.io(): 适合执行 I/O 密集型的操作，如数据库操作、文件读写、网络请求等。默认会使用 RxJava 提供的 CachedThreadScheduler，这是一个线程缓存调度器，会重用之前创建过的线程，减少了线程创建和销毁的开销。
- AndroidSchedulers.mainThread(): 用于指定操作将在 Android 的主线程执行，适合执行 UI 更新操作。

当我们启动一个App的时候，Android系统会启动一个Linux Process，该Process包含一个Thread，称为UI Thread或Main Thread。

* @MainThread，应用程序启动时运行的第一个线程，
* @UiThread，从 MainThread 运行用于 UI 工作，
* @WorkerThread，在程序员定义线程时运行
- @BinderThread，用于 ContentProvider 中的 query()/insert()/update()/delete() 方法。

#### android和iOS维护后台进程 
默认情况下，手机放置一段时间后，是会熄屏，然后停止cpu的。执行后台任务时，需要唤醒cpu。唤醒cpu可以使用闹钟（alarm），本文不做具体介绍，本文考虑的是接收到广播之后的处理。
Receiver是worker thread的执行是并行的，Receiver执行完毕之后，可能被回收。这样就不能保证工作线程正常执行。
正确的方向是Receiver启动一个后台的Service（一般是IntentService），来执行后台任务。

#### 线程休眠 Thread.sleep和SystemClock.sleep()
SystemClock.sleep()和Thread.sleep() 是 Java 中用于线程休眠的方法，它们都可以暂停当前线程一段时间以实现一些特定的功能。它们的主要区别如下：
SystemClock.sleep()方法是 Android 中提供的 API，它是一个简单的休眠方法，不会抛出 InterruptedException 异常，并且可以保证在任何情况下都能正常工作，无需担心线程安全问题。
而Thread.sleep()方法是 Java 中的标准 API，当线程处于 sleep 状态时，如果有其他线程中断了该线程，则会抛出 InterruptedException 异常，因此需要进行异常处理，否则可能会导致程序崩溃。


### Android闹钟服务AlarmManager 
1.Timer类与AlarmManager类区别：
如果你学过J2SE的话，那么你对Timer肯定不会陌生，定时器嘛，一般写定时任务的时候 肯定离不开他，
但是在Android里，他却有个短板，不太适合那些需要长时间在后台运行的 定时任务，因为Android设备有自己的休眠策略，当长时间的无操作，设备会自动让CPU进入 休眠状态，这样就可能导致Timer中的定时任务无法正常运行！
而AlarmManager则不存在 这种情况，因为他具有唤醒CPU的功能，可以保证每次需要执行特定任务时CPU都能正常工作， 或者说当CPU处于休眠时注册的闹钟会被保留(可以唤醒CPU)，但如果设备被关闭，或者重新 启动的话，闹钟将被清除！(Android手机关机闹钟不响...)

2.获得AlarmManager实例对象：
```
AlarmManager alarmManager = (AlarmManager) getSystemService(ALARM_SERVICE);
int anHour = 2 * 60 * 1000; // 这是一小时的毫秒数
long triggerAtTime = SystemClock.elapsedRealtime() + anHour;
Intent i = new Intent(this, AlarmReceiver.class);
PendingIntent pi = PendingIntent.getBroadcast(this, 0, i, 0);
manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, triggerAtTime, pi);
```

### Android 服务
```
//跳转服务
Intent intent = new Intent(this, UploadService.class);
context.startService(intent);

//编写服务
public class UploadService extends Service {

//AndroidManifes.xml注册服务
<service android:name=".service.UploadService" />
```
Service中的onStartCommand方法，bindService方法（都会启动服务，且服务始终为单例）

Android之service之onStartCommand是否再服务启动的时候就执行一次
是的，onStartCommand(Intent intent, int flags, int startId)方法在服务启动时被调用，并且每次通过startService(Intent)方法启动服务时都会被调用。这使得onStartCommand成为初始化数据和执行服务启动逻辑的理想位置。

### Android 广播接收器
```
Intent tmpIntent = new Intent("com.android.zqc.recv");
sendBroadcast(tmpIntent);
context..sendBroadcast(intent);
//注册广播
intentFilter = new IntentFilter();
intentFilter.addAction("com.example.broadcasttest.LOCAL_BROADCAST");
localReceiver = new LocalReceiver();
localBroadcastManager.registerReceiver(localReceiver, intentFilter);    // 注册本地广播监听器（本地广播只接收本应用发出的广播）
//广播接收器
class LocalReceiver extends BroadcastReceiver {
@Override
public void onReceive(Context context, Intent intent) {
```

### Android路由框架ARouter 
1.添加依赖
```
android {     defaultConfig {         ...         javaCompileOptions {             annotationProcessorOptions {                 arguments = [AROUTER_MODULE_NAME: project.getName()]             }         }     } }
dependencies {     // Replace with the latest version     compile 'com.alibaba:arouter-api:?'     annotationProcessor 'com.alibaba:arouter-compiler:?'     ... }
```
2.Activity添加注解，至少为两级结构
```
@Route(path = "/test/activity") public class YourActivity extend Activity {     ... }
```
3.初始化SDK
```
//此配置必须在init之前
if (isDebug()) {                ARouter.openLog();          ARouter.openDebug();  
} ARouter.init(mApplication);
```
4.初始化路由
```
// 1. 简单跳转
ARouter.getInstance().build("/test/activity").navigation();
// 2. 携带参数跳转 ARouter.getInstance().build("/test/1")             .withLong("key1", 666L)             .withString("key3", "888")             .withObject("key4", new Test("Jack", "Rose"))             .navigation();
```
6.使用 Gradle 插件实现路由表的自动加载 (可选)
```
apply plugin: 'com.alibaba.arouter'
buildscript {    repositories {        jcenter()    }
dependencies {        classpath "com.alibaba:arouter-register:?"    }}
```
7.参数解析
```
@Route(path = "/test/activity")public class Test1Activity extends Activity {    @Autowired    public String name;    @Autowired    int age;
```
8.声明拦截器(拦截跳转过程，面向切面编程)
```
// 比较经典的应用就是在跳转过程中处理登陆事件，这样就不需要在目标页重复做登陆检查// 拦截器会在跳转之间执行，多个拦截器会按优先级顺序依次执行@Interceptor(priority = 8, name = "测试用拦截器")public class TestInterceptor implements IInterceptor {
@Override    public void process(Postcard postcard, InterceptorCallback callback) {    ...    callback.onContinue(postcard);  // 处理完成，交还控制权    // callback.onInterrupt(new RuntimeException("我觉得有点异常"));      // 觉得有问题，中断路由流程
// 以上两种至少需要调用其中一种，否则不会继续路由    }
@Override    public void init(Context context) {    // 拦截器的初始化，会在sdk初始化的时候调用该方法，仅会调用一次    }
```
9.处理跳转结果
```
// 使用两个参数的navigation方法，可以获取单次跳转的结果ARouter.getInstance().build("/test/1").navigation(this, new NavigationCallback() {    @Override    public void onFound(Postcard postcard) {    ...    }
@Override    public void onLost(Postcard postcard) {    ...    }});
```
10.自定义全局降级策略
```
// 实现DegradeService接口，并加上一个Path内容任意的注解即可@Route(path = "/xxx/xxx")public class DegradeServiceImpl implements DegradeService {
```

### kotlin跨平台
kotlin-multiplatform：android studio安装`Kotlin Multiplatform plugin`、`Kotlin plugin`插件

Mac安装环境检测：brew install kdoctor
```
[~]$ kdoctor      
Environment diagnose (to see all details, use -v option):
[✓] Operation System
[✓] Java
[!] Android Studio
! Android Studio (AI-233.14808.21.2331.11709847)
Location: /Applications/Android Studio.app
Bundled Java: openjdk 17.0.10 2024-01-16
Kotlin Plugin: 233.14808.21.2331.11709847-AS
Kotlin Multiplatform Mobile Plugin: not installed
Install Kotlin Multiplatform Mobile plugin - https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile
[✓] Xcode
[✓] CocoaPods

Conclusion:
✓ Your operation system is ready for Kotlin Multiplatform Mobile Development!
```

**Kotlin Multiplatform和Compose Multiplatform**
* Kotlin Multiplatform：提供了底层逻辑的跨平台，为 Compose Multiplatform 提供了基础支撑
* Compose Multiplatform：Compose UI 的跨平台框架，提供 UI 跨平台能力

sqldelight 数据库
```
  plugins {
+  id("app.cash.sqldelight") version "2.0.2"
   }
   dependencies {
+  implementation("app.cash.sqldelight:sqlite-driver:2.0.2")
   }
```

### Android常见问题处理

> Java.lang.NoClassDefFoundError: Failed resolution of: Lorg/apache/http/conn/scheme/SchemeRegistry

这个在9.0以上运行闪退会报上面这个错误，解决办法：
在AndroidManifest.xml文件的application标签里面加入如下：
```
<application...>
//加入如下
<uses-library android:name="org.apache.http.legacy" android:required="false" />

 </application>
```

**权限问题**
> telephonyManager.getSimSerialNumber();  //报错 The user 10154 does not meet the requirements to access device identifiers.

通过代码获取硬件标识符。自 Android 10（API 级别 29）起，您的应用必须是设备或个人资料所有者应用，具有特殊运营商许可，或具有 READ_PRIVILEGED_PHONE_STATE 特权，才能访问不可重置的设备标识符 这个是运行在Android10及以上会出现的闪退问题，解决办法：
1. 第一种，降低当前的targetSdkVersion，使其低于29（这显然和我写这篇文章的初心不符）
2. 第二种，就是加一个判断啦，如下：
```
private String deviceCode;
if (Build.VERSION.SDK_INT>28){
   deviceCode = Settings.System.getString(
   this.getContentResolver(), Settings.Secure.ANDROID_ID);
}else {
   deviceCode = DeviceUtils.getDeviceId(getContext());
}
```
这里是通过Android的自带ID取代这个硬件标识。。。
添加权限：
```
<uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
```

**权限问题**
> startForeground requires android.permission.FOREGROUND_SERVICE

在AndroidManifest.xml添加以下权限代码：
```
<uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
```

**Android10+存储域变化**
部分手机（oppo的android11某机型）因为定制化的问题，用户无法查看data/下的文件内容，导致我们经常会使用的拍照存图片啊之类的存储会崩溃，而某些过低的版本的手机（5.0）在新的存储方法中又会崩溃，所以也需要适配：

**Android9.0以后，谷歌建议使用AndroidX作为编译库,Bug：AndroidX与原有库冲突**
解决
~~~~
# 选择AndroidX作为编译库
android.useAndroidX=true
# Automatically convert third-party libraries to use AndroidX
android.enableJetifier=true
~~~~

**Android避免内存溢出（Out of Memory）方法总结**
当出现错误：Out of Memory，表示当前设备的缓存不足以运行该程序造成内存溢出

解决方法一： 牺牲其他程序进程来获取更大的内存空间
使用步骤： 只要在AndroidManifest.xml文件中的<application>节点属性中加上”android:largeHeap="true"

原理： 可以在程序中使用ActivityManager.getMemoryClass()方法来获取App内存正常使用情况下的大小，通过ActivityManager.getLargeMemoryClass()可获得开启largeHeap时最大的内存大小