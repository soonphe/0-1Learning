
Android事件总线：
Event和Otto
针对事件提供统一订阅，发布以达到组件间通信的解决方案
EventBus > Otto > BroadcastReceiver(当然BroadcastReceiver作为系统内置组件，有一些前两者没有的功能).
EventBus最简洁，Otto最符合Guava EventBus的设计思路， BroadcastReceiver最难使用
 
概念：EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程间的通信。比如请求网络，等网络返回时通过Handler或Broadcast通知UI，两个Fragment之间需要通过Listener通信，这些需求都可以通过EventBus实现。
EventBus比较适合仅仅当做组件间的通讯工具使用，主要用来传递消息。
使用EventBus可以避免搞出一大推的interface
 
添加依赖：
compile 'org.greenrobot:eventbus:3.0.0'
 
注册
在onStart()方法中注册
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
 
订阅者
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
 
EventBus包含4个ThreadMode
ThreadMode.POSTING
ThreadMode.MAIN
ThreadMode.BACKGROUND
ThreadMode.ASYNC
 
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
 
发布者
 
不管发布者是在主线程还是子线程，发布的消息在所有定义好的实体类型订阅者中都可以接收到消息。也就是，可以实现一个订阅者订阅多个事件，和一个事件对应多个订阅者。
EventBus.getDefault().post(new MessageEvent("你好!"));
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
