# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

## Android架构(Android Framework)
Android架构主要分为分为四部分，从下往上以此为
- LINUX KERNEL(内核层)
- LIBRARIES(共享库，以及android runtime运行时库)
- APPLICATION FRAMEWORK(应用框架层)
- APPLICATION(应用程序)

### Linux Kernel
Android 的核心系统服务依赖于 Linux 内核，如安全性，内存管理，进程管理， 网络协议栈和驱动模型。 Linux 内核也同时作为硬件和软件栈之间的抽象层。


### Libraries
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

### APPLICATION
主要为系统中的应用，如桌面，闹铃，设置，日历，电话，短信等系统应用。

### APPLICATION FRAMEWORK
Android的应用程序框架为应用程序层的开发者提供APIs，它实际上是一个应用程序的框架。由于上层的应用程序是以JAVA构建的，因此本层次提供了以下服务：

- 丰富而又可扩展的视图(Views)，可以用来构建应用程序， 它包括列表(lists)，网格(grids)，文本框(text boxes)，按钮(buttons)， 甚至可嵌入的web浏览器；
- 内容提供器(Content Providers)使得应用程序可以访问另一个应用程序的数据(如联系人数据库)，或者共享它们自己的数据；
- 资源管理器(Resource Manager)提供非代码资源的访问，如本地字符串，图形，和布局文件( layout files )；
- 通知管理器 (Notification Manager) 使得应用程序可以在状态栏中显示自定义的提示信息；
- 活动管理器( Activity Manager) 用来管理应用程序生命周期并提供常用的导航回退功能。

#### Framework简介
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


