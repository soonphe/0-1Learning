# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## 广播接收器broadcast receiver

## 前言
    什么是广播：来自村口的广播了一句下雨了，回家收衣服。身在地里干活的你，收到消息，立刻回家，收好了衣服，关好了窗户。

    广播接收器用于响应来自其他应用程序或者系统的广播消息。比如开机广播、短信广播、下载广播，不断能监听到这些信息，还能响应响应的信息。

### 广播机制简介
标准广播（Normal broadcasts）是一种完全异步执行的广播，在广播发出之后，所有的
广播接收器几乎都会在同一时刻接收到这条广播消息，因此它们之间没有任何先后顺序可言。这种广播的效率会比较高，但同时也意味着它是无法被截断的。

有序广播（Ordered broadcasts）则是一种同步执行的广播，在广播发出之后，同一时刻
只会有一个广播接收器能够收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递


### 广播接收器
Broadcast Receiver
android 广播分为两个角色：广播发送者、广播接收者
android 广播：
1），用于不同组件间的通信（含：应用内/不同应用之间）
2），用于多线程通信
3），与android系统的通信


### 自定义广播接收者
继承BroadcastReceive 基类，重写抽象方法onReceive()方法
1.广播接收器收到相应广播后，会自动调用onReceive(） 方法
2.一般情况下，onReceive方法会会涉及与其他组件之间的交互，如 发送Notiotification，启动server等
3.默认情况下，广播接收器运行在UI线程，因此，onReceive方法不能执行耗时操作，否则将导致ANR
4.最后要记得，动态注册的广播接收器一定都要取消注册才行，这里我们是在onDestroy()方法中通过调用unregisterReceiver()方法来实现的


### 广播接收器注册
注册的方式有两种：静态注册、动态注册

#### 静态注册
注册方式：在AndroidManifest.xml 里通过<receive 标签声明
属性说明
```
<receiver
	android:enable="true"/"false"
	//此broadcastReceiver 是否接受其他应用发出的广播
	//默认值时由receiver 中d有无inter-filter决定，如果有，默认true，否则默认false
	android:exported="true"/"false"
	android:icon="drawable resource"
	android:label="string resource"
	//继承BroadcastReceiver子类的类名
    android:name=".mBroadcastReceiver"
//具有相应权限的广播发送者发送的广播才能被此BroadcastReceiver所接收；
    android:permission="string"
//BroadcastReceiver运行所处的进程
//默认为app的进程，可以指定独立的进程
//注：Android四大基本组件都可以通过此属性指定自己的独立进程
    android:process="string" >

//用于指定此广播接收器将接收的广播类型
//本示例中给出的是用于接收网络状态改变时发出的广播
 <intent-filter>
	<action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
 </intent-filter>
 </receiver>

注册示例：
<receiver 
    //此广播接收者类是mBroadcastReceiver
    android:name=".mBroadcastReceiver" >
    //用于接收网络状态改变时发出的广播
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```
当此APP首次启动时，系统会自动实例化mBroadcastReceiver类，并注册到系统中。

#### 动态注册

注册方式：在代码中调用Context.registerReceiver() 方法
```
具体代码如下：
	// 1. 实例化BroadcastReceiver子类 &  IntentFilter
     mBroadcastReceiver mBroadcastReceiver = new mBroadcastReceiver();
     IntentFilter intentFilter = new IntentFilter();

    // 2. 设置接收广播的类型
    intentFilter.addAction(android.net.conn.CONNECTIVITY_CHANGE);

    // 3. 动态注册：调用Context的registerReceiver（）方法
     registerReceiver(mBroadcastReceiver, intentFilter);

```

//动态注册广播后，需要在相应位置记得销毁广播
unregisterReceiver(mBroadcastReceiver);

### 动态广播应该何时注册和注销
动态广播最好在onResume中注册， onPause注销
原因：
1，对于动态广播，有注册必然得有注销，否则会导致内存泄漏
2，onPause在App死亡前一定会被执行，从而保证app死亡前一定会被注销，从而防止内存泄漏

### 两种注册方式的区别

广播的发送 = 广播发送者 将此广播的意图（intent）通过 sendBroasdcast() 方法发送出去
广播的类型：普通广播 系统广播 有序广播 粘性广播 App 应用内广播

特别注意：
对于不同注册方式的广播接收器回调OnReceive（Context context，Intent intent）中的context返回值是不一样的：

对于静态注册（全局+应用内广播），回调onReceive(context,intent)中的context返回值是：ReceiverRestrictedContext；

对于全局广播的动态注册，回调onReceive(context, intent)中的context返回值是：Activity Context；

对于应用内广播的动态注册（LocalBroadcastManager方式），回调onReceive(context,intent)中的context返回值是：Application Context。

对于应用内广播的动态注册（非LocalBroadcastManager方式），回调onReceive(context,intent)中的context返回值是：Activity Context；


### 发送广播
```
Intent intent = new Intent("com.example.broadcasttest.MY_BROADCAST");
sendBroadcast(intent);  //发送标准广播,这个是context中的方法

sendOrderedBroadcast(intent, null); // 发送有序广播
```

### 使用本地广播——LocalBroadcastManager
前面我们发送和接收的广播全部都是属于系统全局广播，即发出的广播可以被其他任何
的任何应用程序接收到，并且我们也可以接收来自于其他任何应用程序的广播。这样就很容
易会引起安全性的问题，比如说我们发送的一些携带关键性数据的广播有可能被其他的应用
程序截获，或者其他的程序不停地向我们的广播接收器里发送各种垃圾广播。
为了能够简单地解决广播的安全性问题，Android 引入了一套本地广播机制，使用这个
机制发出的广播只能够在应用程序的内部进行传递，并且广播接收器也只能接收来自本应用
程序发出的广播，这样所有的安全性问题就都不存在了。

```
localBroadcastManager = LocalBroadcastManager.getInstance(this);// 获取实例
Button button = (Button) findViewById(R.id.button);
button.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent("com.example.broadcasttest.LOCAL_BROADCAST");
        localBroadcastManager.sendBroadcast(intent); // 发送本地广播
    }
});
intentFilter = new IntentFilter();
intentFilter.addAction("com.example.broadcasttest.LOCAL_BROADCAST");
localReceiver = new LocalReceiver();
localBroadcastManager.registerReceiver(localReceiver, intentFilter);    // 注册本地广播监听器


class LocalReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "received local broadcast",
        Toast.LENGTH_SHORT).show();
    }
}
```