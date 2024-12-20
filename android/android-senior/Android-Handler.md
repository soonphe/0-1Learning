# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-Handler
Android的线程间通信就靠Handler、Looper、Message、MessageQueue

android handle解析：android主线程不能访问网络，子线程不能更新UI，当子线程操作涉及UI更新时，Handle就出现了
Handler运行在主线程中(UI线程中)，  
它与子线程可以通过Message对象来传递数据， 这个时候，Handler就承担着接受子线程传过来的(子线程用sendMessage()方法传递)Message对象，(里面包含数据)，把这些消息放入主线程队列中，配合主线程进行更新UI

### Looper
在Looper中，维持一个`Thread`对象以及`MessageQueue`，通过Looper的构造函数我们可以知道:
```
    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);//传入的参数代表这个Queue是否能够被退出
        mThread = Thread.currentThread();
    }
```
`Looper`在构造函数里干了两件事情：
1. 将线程对象指向了创建`Looper`的线程
2. 创建了一个新的`MessageQueue`

分析完构造函数之后，接下来我们主要分析两个方法:
1. `looper.loop()`
2. `looper.prepare()`

### looper.loop()（在当前线程启动一个Message loop机制，此段代码将直接分析出Looper、Handler、Message、MessageQueue的关系）
```
 public static void loop() {
        final Looper me = myLooper();//获得当前线程绑定的Looper
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue;//获得与Looper绑定的MessageQueue

        // Make sure the identity of this thread is that of the local process,
        // and keep track of what that identity token actually is.
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();
        
        //进入死循环，不断地去取对象，分发对象到Handler中消费
        for (;;) {
            Message msg = queue.next(); // 不断的取下一个Message对象，在这里可能会造成堵塞。
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }
            
            //在这里，开始分发Message了
            //至于这个target是神马？什么时候被赋值的？ 
            //我们一会分析Handler的时候就会讲到
            msg.target.dispatchMessage(msg);

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            // Make sure that during the course of dispatching the
            // identity of the thread wasn't corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }
            
            //当分发完Message之后，当然要标记将该Message标记为 *正在使用* 啦
            msg.recycleUnchecked();
        }
    }
```
*分析了上面的源代码，我们可以意识到，最重要的方法是：*
1. `queue.next()`
2. `msg.target.dispatchMessage(msg)`
3. `msg.recycleUnchecked()`

其实Looper中最重要的部分都是由`Message`、`MessageQueue`组成的有木有！这段最重要的代码中涉及到了四个对象,他们与彼此的关系如下：
1. `MessageQueue`：装食物的容器
2. `Message`：被装的食物
3. `Handler`（msg.target实际上就是`Handler`）：食物的消费者
4. `Looper`：负责分发食物的人


### looper.prepare()（在当前线程关联一个Looper对象）
```
 private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        //在当前线程绑定一个Looper
        sThreadLocal.set(new Looper(quitAllowed));
    }
```
以上代码只做了两件事情：
1. 判断当前线程有木有`Looper`，如果有则抛出异常（在这里我们就可以知道，Android规定一个线程只能够拥有一个与自己关联的`Looper`）。
2. 如果没有的话，那么就设置一个新的`Looper`到当前线程。

--------------
### Handler
由于我们使用Handler的通常性的第一步是:
```
 Handler handler = new Handler(){
        //你们有没有很好奇这个方法是在哪里被回调的？
        //我也是！所以接下来会分析到哟！
        @Override
        public void handleMessage(Message msg) {
            //Handler your Message
        }
    };
```
所以我们先来分析`Handler`的构造方法
```
//空参数的构造方法与之对应，这里只给出主要的代码，具体大家可以到源码中查看
public Handler(Callback callback, boolean async) {
        //打印内存泄露提醒log
        ....
        
        //获取与创建Handler线程绑定的Looper
        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread that has not called Looper.prepare()");
        }
        //获取与Looper绑定的MessageQueue
        //因为一个Looper就只有一个MessageQueue，也就是与当前线程绑定的MessageQueue
        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
        
    }
```
*带上问题：*
1. `Looper.loop()`死循环中的`msg.target`是什么时候被赋值的？
2. `handler.handleMessage(msg)`在什么时候被回调的？

### A1：`Looper.loop()`死循环中的`msg.target`是什么时候被赋值的？
要分析这个问题，很自然的我们想到从发送消息开始，无论是`handler.sendMessage(msg)`还是`handler.sendEmptyMessage(what)`，我们最终都可以追溯到以下方法
```
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        //引用Handler中的MessageQueue
        //这个MessageQueue就是创建Looper时被创建的MessageQueue
        MessageQueue queue = mQueue;
        
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        //将新来的Message加入到MessageQueue中
        return enqueueMessage(queue, msg, uptimeMillis);
    }
```

我们接下来分析`enqueueMessage(queue, msg, uptimeMillis)`:
```
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        //显而易见，大写加粗的赋值啊！
        **msg.target = this;**
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }
```


### A2：`handler.handleMessage(msg)`在什么时候被回调的？
通过以上的分析，我们很明确的知道`Message`中的`target`是在什么时候被赋值的了，我们先来分析在`Looper.loop()`中出现过的过的`dispatchMessage(msg)`方法

```
public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            //看到这个大写加粗的方法调用没！
            **handleMessage(msg);**
        }
    }
```

加上以上分析，我们将之前分析结果串起来，就可以知道了某些东西：
`Looper.loop()`不断地获取`MessageQueue`中的`Message`，然后调用与`Message`绑定的`Handler`对象的`dispatchMessage`方法，最后，我们看到了`handleMessage`就在`dispatchMessage`方法里被调用的。

------------------
通过以上的分析，我们可以很清晰的知道Handler、Looper、Message、MessageQueue这四者的关系以及如何合作的了。

### 总结：
当我们调用`handler.sendMessage(msg)`方法发送一个`Message`时，实际上这个`Message`是发送到**与当前线程绑定**的一个`MessageQueue`中，然后**与当前线程绑定**的`Looper`将会不断的从`MessageQueue`中取出新的`Message`，调用`msg.target.dispathMessage(msg)`方法将消息分发到与`Message`绑定的`handler.handleMessage()`方法中。

一个`Thread`对应多个`Handler`
一个`Thread`对应一个`Looper`和`MessageQueue`，`Handler`与`Thread`共享`Looper`和`MessageQueue`。
`Message`只是消息的载体，将会被发送到**与线程绑定的唯一的**`MessageQueue`中，并且被**与线程绑定的唯一的**`Looper`分发，被与其自身绑定的`Handler`消费。




