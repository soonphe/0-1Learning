# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Andorid进阶知识

### Activity启动方式有四种
Activity栈：先进后出规则

* standard：默认模式，默认创建一个新的实例（在这种模式下，可以有多个相同的实例，也允许多个相同Activity叠加）
* singleTop：可以有多个实例，但是不允许多个相同Activity叠加（启动相同的Activity，不会创建新的实例，而会调用其onNewIntent方法）
* singleTask：只有一个实例。在同一个应用程序中启动他的时候，若Activity不存在，则会在当前task创建一个新的实例，若存在，则会把task中在其之上的其它Activity destory掉并调用它的onNewIntent方法（如果是在别的应用程序中启动它，则会新建一个task，并在该task中启动这个Activity）
* singleInstance：singleInstance只有一个实例，并且这个实例独立运行在一个task中，这个task只有这个实例，不允许有别的Activity存在。
可以根据实际的需求为Activity设置对应的启动模式，从而可以避免创建大量重复的Activity等问题。

### Activity整个生命周期的4种状态、7个重要方法和3个嵌套循环

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


#### Android9.0以后，谷歌建议使用AndroidX作为编译库,Bug：AndroidX与原有库冲突
解决
~~~~
# 选择AndroidX作为编译库
android.useAndroidX=true
# Automatically convert third-party libraries to use AndroidX
android.enableJetifier=true
~~~~

#### android签名keystore和jks区别
keystore 是Eclipse 打包生成的签名。
jks是Android studio 生成的签名。


#### Acitivity Finish返回跳转携带数据
//请求
~~~~
Intent intent=new Intent();
intent.setClass(getContext(), AddressCheckActivity.class);
startActivityForResult(intent,0x002);
~~~~

//目标
//activity finish传值
~~~~
Intent intent = new Intent();
intent.putExtra("receiveAddress", ((UmsAddress) adapter.getItem(position)).getTrxAddress());
setResult(0x001, intent);
finish();
~~~~

#### 如何把自己写的库发布到网上的仓库 Bintray/JCenter/JitPack
Bintray/JCenter/JitPack发布及配置流程
JitPack虽然是最简单的，但是他是基于把整个项目作为依赖的，Bintray/JCenter则可以上传单个模块作为依赖
推荐使用Bintray
发布流程：https://blog.csdn.net/u014780554/article/details/79628041?utm_source=blogxgwz1