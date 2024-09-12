# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android事件总线

### Android常见的事件总线
针对事件提供统一订阅，发布以达到组件间通信的解决方案

EventBus > Otto > BroadcastReceiver(当然BroadcastReceiver作为系统内置组件，有一些前两者没有的功能).

EventBus最简洁，Otto最符合Guava EventBus的设计思路， BroadcastReceiver最难使用

###  EventBus
EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程间的通信。比如请求网络，等网络返回时通过Handler或Broadcast通知UI，两个Fragment之间需要通过Listener通信，这些需求都可以通过EventBus实现。
EventBus比较适合仅仅当做组件间的通讯工具使用，主要用来传递消息。
使用EventBus可以避免搞出一大推的interface

EventBus作为一个消息总线，有三个主要的元素：
* Event：事件。可以是任意类型的对象
* Subscriber：事件订阅者，接收特定的事件。在EventBus中，使用约定来指定事件订阅者以简化使用。即所有事件订阅都都是以onEvent开头的函数，具体来说，函数的名字是onEvent,onEventMainThread，onEventBackgroundThread，onEventAsync这四个，这个和ThreadMode有关。
* Publisher：事件发布者，用于通知 Subscriber 有事件发生。可以在任意线程任意位置发送事件，直接调用eventBus.post(Object) 方法，可以自己实例化 EventBus 对象，但一般使用默认的单例就好了：EventBus.getDefault()， 根据post函数参数的类型，会自动调用订阅相应类型事件的函数。
 
### 集成示例
添加依赖：
```
compile 'org.greenrobot:eventbus:3.0.0'
```

在onStart()方法中注册
```
  //注册eventBus
    @Override
    protected void onStart() {
        super.onStart();
        EventBus.getDefault().register(this);
    }
 
    //取消注册
    @Override
    protected void onStop() {
        super.onStop();
        EventBus.getDefault().unregister(this);
    }
```
订阅者
```
@Subscribe注解来描述一个public无返回值的非静态方法，注解后面可以跟threadMode，来给定订阅者处理事件所在的线程。
@Subscribe(threadMode = ThreadMode.MAIN)
public void onEventMainThread(IdEvent event) {

    if (event != null) {
        System.out.println("onEventMainThread:"+event.getId()+" "+Thread.currentThread().getName());
        textView.setText(""+event.getId());
    }else {
        System.out.println("event:"+event);
    }
}
```

发送消息：
```
EventBus.getDefault().post(new MessageEvent("你好!"));
```
 
### EventBus包含4个ThreadMode
Subscriber的函数只能是那4个，因为每个事件订阅函数都是和一个ThreadMode相关联的，ThreadMode指定了会调用的函数。
根据事件订阅都函数名称的不同，会使用不同的ThreadMode，如果在后台线程加载了数据想在UI线程显示，订阅者只需把函数命名onEventMainThread。

有以下四个ThreadMode：
- ThreadMode.POSTING
- ThreadMode.MAIN
- ThreadMode.BACKGROUND
- ThreadMode.ASYNC

* PostThread：事件的处理在和事件的发送在相同的进程，所以事件处理时间不应太长，不然影响事件的发送线程，而这个线程可能是UI线程。对应的函数名是onEvent。
* MainThread: 事件的处理会在UI线程中执行。事件处理时间不能太长，这个不用说的，长了会ANR的，对应的函数名是onEventMainThread。
* BackgroundThread：事件的处理会在一个后台线程中执行，对应的函数名是onEventBackgroundThread，虽然名字是BackgroundThread，事件处理是在后台线程，但事件处理时间还是不应该太长，因为如果发送事件的线程是后台线程，会直接执行事件，如果当前线程是UI线程，事件会被加到一个队列中，由一个线程依次处理这些事件，如果某个事件处理时间太长，会阻塞后面的事件的派发或处理。
* Async：事件处理会在单独的线程中执行，主要用于在后台线程中执行耗时操作，每个事件会开启一个线程（有线程池），但最好限制线程的数目。

- **onEvent**:如果使用onEvent作为订阅函数，那么该事件在哪个线程发布出来的，onEvent就会在这个线程中运行，也就是说发布事件和接收事件线程在同一个线程。使用这个方法时，在onEvent方法中不能执行耗时操作，如果执行耗时操作容易导致事件分发延迟。
- **onEventMainThread**:如果使用onEventMainThread作为订阅函数，那么不论事件是在哪个线程中发布出来的，onEventMainThread都会在UI线程中执行，接收事件就会在UI线程中运行，这个在Android中是非常有用的，因为在Android中只能在UI线程中跟新UI，所以在onEvnetMainThread方法中是不能执行耗时操作的。
- **onEventBackground**:如果使用onEventBackgrond作为订阅函数，那么如果事件是在UI线程中发布出来的，那么onEventBackground就会在子线程中运行，如果事件本来就是子线程中发布出来的，那么onEventBackground函数直接在该子线程中执行。
- **onEventAsync**：使用这个函数作为订阅函数，那么无论事件在哪个线程发布，都会创建新的子线程在执行onEventAsync。

示例：
```
/**
 * 在后台线程中执行，如果当前线程是子线程，则会在当前线程执行，如果当前线程是主线程，则会创建一个新的子线程来执行
 * @param event
 */
@Subscribe(threadMode = ThreadMode.BACKGROUND)
public void  onEventBackgroundThread(MessageEvent event){
    System.out.println("onEventBackgroundThread::"+" "+Thread.currentThread().getName());
}

/**
 * 创建一个异步线程来执行
 * @param event
 */
@Subscribe(threadMode = ThreadMode.ASYNC)
public void onEventAsync(MessageEvent event){
    System.out.println("onEventAsync::"+" "+Thread.currentThread().getName());
}

/**
 * 在主线程中运行
 * @param event
 */
@Subscribe(threadMode = ThreadMode.MAIN)
public void onEventMain(MessageEvent event){
    System.out.println("onEventMain::"+" "+Thread.currentThread().getName());
}

/**
 *默认的线程模式，在当前线程下运行。如果当前线程是子线程则在子线程中，当前线程是主线程，则在主线程中执行。
 * @param event
 */
@Subscribe(threadMode = ThreadMode.POSTING)
public void onEventPosting(MessageEvent event){
    System.out.println("onEventPosting::"+" "+Thread.currentThread().getName());
}
```
不管发布者是在主线程还是子线程，发布的消息在所有定义好的实体类型订阅者中都可以接收到消息。
也就是，可以实现一个订阅者订阅多个事件，和一个事件对应多个订阅者。


### EventBus索引加速
3.0 后引入了索引加速(默认不开启)的功能，即通过 apt 编译插件的方式，在代码编译的时候对注解进行索引，避免了以往通过反射造成的性能损耗。
如何使用可以参考[官方文档](http://greenrobot.org/eventbus/documentation/subscriber-index/)

最后，proguard 需要做一些额外处理:
```
#EventBus
 -keepclassmembers class ** {
    public void onEvent*(**);
    void onEvent*(**);
 }
```
