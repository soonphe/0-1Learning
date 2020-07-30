# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## 多线程Timer和ScheduledThreadPool


### Timer和ScheduledThreadPool比较说明：
    1. Timer在执行定时任务时只会创建一个线程，所以如果存在多个任务，且任务时间过长，超过了两个任务的间隔时间，则会发生一些缺陷
    2. ScheduledThreadPool内部是个线程池，所以可以支持多个任务并发执行
    3. 如果TimerTask抛出RuntimeException，Timer会停止所有任务的运行
    4. ScheduledThreadPool周期执行任务时，某个任务抛出异常后，下个周期再执行的时候，有异常的任务就会被排除在计划表外
    
#### Timer（Timer只能调用TimerTask来实现）
示例：
~~~~
声明两个Task：
注：TimerTask implements Runnable 
final TimerTask task1 = new TimerTask()  
        {  
  
            @Override  
            public void run()  
            {  
                throw new RuntimeException();  
            }  
        };  
  
        final TimerTask task2 = new TimerTask()  
        {  
  
            @Override  
            public void run()  
            {  
                System.out.println("task2 invoked!");  
            }  
        };  

注：Timer只能调用TimerTask来实现
        Timer timer = new Timer();  
        timer.schedule(task1, 100);  
        timer.scheduleAtFixedRate(task2, new Date(), 1000);  
~~~~


---
### Executors类

#### 创建ExecutorService
ExecutorService继承了Executor接口，JDK1.5中提供Executors工厂类来产生连接池，该工厂类中包含如下的几个静态工程方法来创建连接池：
1、public static ExecutorService newFixedThreadPool(int nThreads)：创建一个可重用的、具有固定线程数的线程池。
2、public static ExecutorService newSingleThreadExecutor()：创建一个只有单线程的线程池，它相当于newFixedThreadPool方法是传入的参数为1
3、public static ExecutorService newCachedThreadPool()：创建一个具有缓存功能的线程池，系统根据需要创建线程，这些线程将会被缓存在线程池中。
4、public static ScheduledExecutorService newSingleThreadScheduledExecutor：创建只有一条线程的线程池，他可以在指定延迟后执行线程任务
5、public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)：创建具有指定线程数的线程池，它可以再指定延迟后执行线程任务，corePoolSize指池中所保存的线程数，即使线程是空闲的也被保存在线程池内。

### 参数说明
Executors中的方法
schedule(Callable<V> callable, long delay, TimeUnit unit)
         创建并执行在给定延迟后启用的 ScheduledFuture。
schedule(Runnable command, long delay, TimeUnit unit)
         创建并执行在给定延迟后启用的一次性操作。
scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnitunit)
         创建并执行一个在给定初始延迟后首次启用的定期操作，后续操作具有给定的周期；也就是将在 initialDelay 后开始执行，然后在initialDelay+period 后执行，接着在 initialDelay + 2 * period 后执行，依此类推。
scheduleWithFixedDelay(Runnable command, long initialDelay, long delay,TimeUnit unit)
         创建并执行一个在给定初始延迟后首次启用的定期操作，随后，在每一次执行终止和下一次执行开始之间都存在给定的延迟。



线程池调用示例：  
~~~~
ScheduledExecutorService既能调用TimerTask，也可以调用runable来实现


//创建一个具有延时功能的线程池
ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);
//执行一次任务
pool.schedule(task1, 100, TimeUnit.MILLISECONDS);
//周期化执行任务
参数说明  //执行线程  //初始化延时 //两次开始执行最小间隔时间	//计时单位
pool.scheduleAtFixedRate(task2, 0 , 1000, TimeUnit.MILLISECONDS);

~~~~