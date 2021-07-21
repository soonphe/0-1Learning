# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## 定时任务

### Spring定时任务
1.开始定时任务能力
只需要在配置类中添加一个@EnableScheduling注解即可开启SpringTask的定时任务能力。
~~~~
@Configuration
@EnableScheduling
public class SpringTaskConfig {
}
~~~~

2.定义定时任务类
~~~~
@Component
public class ScheduledTask {

	//定时任务方法
	@Scheduled(fixedDelay = 5000)        //fixedDelay = 5000表示当前方法执行完毕5000ms后，Spring scheduling会再次调用该方法
	public void getTask1() {
	    System.out.println("当前时间：" + new Date());
	  }
}
~~~~
注解说明：
* @Scheduled(fixedRate=2000)：上一次开始执行时间点后2秒再次执行；
* @Scheduled(fixedDelay=2000)：上一次执行完毕时间点后2秒再次执行；
* @Scheduled(initialDelay=1000, fixedDelay=2000)：第一次延迟1秒执行，然后在上一次执行完毕时间点后2秒再次执行；
* @Scheduled(cron="* * * * * ?")：按cron规则执行
* cron表达式：Seconds Minutes Hours DayofMonth Month DayofWeek [Year]
* 例：
     * 每10分钟扫描一次
    @Scheduled(cron = "0 0/10 * ? * ?")
(cron = "0 0 0 * * ?") 每天凌晨执行一次

---

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

### Executor多线程与定时任务
从JDK1.5开始，Java API提供了Executor框架让你可以创建不同的线程池。

#### Executors类说明：
JDK1.5中提供Executors工厂类来产生连接池，ExecutorService继承了Executor接口，该工厂类中包含如下的几个静态工程方法来创建连接池：

1. public static ExecutorService newFixedThreadPool(int nThreads)：创建一个可重用的、具有固定线程数的线程池。
2. public static ExecutorService newSingleThreadExecutor()：创建一个只有单线程的线程池，它相当于newFixedThreadPool方法是传入的参数为1
3. public static ExecutorService newCachedThreadPool()：创建一个具有缓存功能的线程池，系统根据需要创建线程，这些线程将会被缓存在线程池中。
4. public static ScheduledExecutorService newSingleThreadScheduledExecutor： 创建只有一条线程的线程池，他可以在指定延迟后执行线程任务
5. public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)：创建具有指定线程数的线程池，它可以在指定延迟后执行线程任务，corePoolSize指池中所保存的线程数，即使线程是空闲的也被保存在线程池内。

说明：4和5加了Scheduled，也就是具备定时任务的能力。

#### Executors定时方法说明：
Executors中的方法
* schedule(Callable<V> callable, long delay, TimeUnit unit)
         创建并执行在给定延迟后启用的 ScheduledFuture。
* schedule(Runnable command, long delay, TimeUnit unit)
         创建并执行在给定延迟后启用的一次性操作。
* scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnitunit)
         创建并执行一个在给定初始延迟后首次启用的定期操作，后续操作具有给定的周期；也就是将在 initialDelay 后开始执行，然后在initialDelay+period 后执行，接着在 initialDelay + 2 * period 后执行，依此类推。
* scheduleWithFixedDelay(Runnable command, long initialDelay, long delay,TimeUnit unit)
         创建并执行一个在给定初始延迟后首次启用的定期操作，随后，在每一次执行终止和下一次执行开始之间都存在给定的延迟。

### 线程池调用示例：  
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

#### 配置多线程原理
~~~~
ScheduledTaskRegistrar中，判断taskScheduler为空，那么就给定时任务做了一个单线程的线程池

if (this.taskScheduler == null) {
	this.localExecutor = Executors.newSingleThreadScheduledExecutor();
	this.taskScheduler = new ConcurrentTaskScheduler(this.localExecutor);
}

ScheduledTaskRegistrar此类中提供设置taskScheduler方法：

public void setScheduler(Object scheduler) {
	Assert.notNull(scheduler, "Scheduler object must not be null");
	if (scheduler instanceof TaskScheduler) {
		this.taskScheduler = (TaskScheduler) scheduler;
	}
	else if (scheduler instanceof ScheduledExecutorService) {
		this.taskScheduler = new ConcurrentTaskScheduler(((ScheduledExecutorService) scheduler));
	}
	else {
		throw new IllegalArgumentException("Unsupported scheduler type: " + scheduler.getClass());
	}
}
~~~~
所以，只需要显式的设置一个ScheduledExecutorService就可以达到并发的效果了


#### Configuration配置方式
@Configuration
//所有的定时任务都放在一个线程池中，定时任务启动时使用不同的线程。
public class ScheduleConfig implements SchedulingConfigurer {
    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        //设定一个长度10的定时任务线程池
        taskRegistrar.setScheduler(Executors.newScheduledThreadPool(10));
    }
}

#### 手动配置
1. 手动配置ScheduledExecutorService ，手动执行任务
~~~~
Executors.newSingleThreadExecutor();    // 创建单线程线程池
Executors.newCachedThreadPool();    // 创建具有缓存功能的线程池
Executors.newFixedThreadPool(5);    // 创建一个可重用的、具有固定线程数的线程池
        
Executors.newSingleThreadScheduledExecutor  // 创建单线程定时任务线程
Executors.newScheduledThreadPool(3);    // 创建指定线程数定时任务线程池
~~~~

2. 执行多线程定时任务
~~~~
/**
* 参数说明
* Callable/runable：the function/task to execute
* task(TimerTask)：要执行的任务
* delay(Long)：多久后去执行
* period(Long)：每隔多久执行一次
* unit：计时单位
*/
//执行单次定时任务
executor.schedule(callable, delay, TimeUnit.SECONDS);
//以固定周期频率执行任务
executor.scheduleAtFixedRate(runnable, initialdelay,period ,TimeUnit.SECONDS);
//执行固定延迟时间定时任务
service.scheduleWithFixedDelay(runnable, 1,1, TimeUnit.SECONDS);
~~~~

#### 推荐使用ThreadPoolExecutor创建线程池
ExecutorService extends Executor
ThreadPoolExecutor继承自ExecutorService
~~~~
/**
 * 推荐ThreadPoolExecutor创建线程池，可以自定义传入我们设置的线程池的参数，更加灵活
 * 上面的所有创建线程的方法最终都是创建ThreadPoolExecutor实现
 * 实现定时任务都是创建ScheduledThreadPoolExecutor，传入了一个DelayedWorkQueue队列而已
 *
 * corePoolSize：在线程池中保持的线程的数量
 * maximumPoolSize：最大线程数量，当workQueue队列已满，放不下新的任务，再通过execute添加新的任务则线程池会再创建新的线程，线程数量大于corePoolSize但不会超过maximumPoolSize
 * keepAliveTime：线程的空闲时间大于keepAliveTime，则该线程会被销毁
 * unit：计时单位
 * workQueue：阻塞队列，当线程池正在运行的线程数量已经达到corePoolSize，那么再通过execute添加新的任务则会被加到workQueue队列中，在队列中排队等待执行，而不会立即执行，还有其他队列：ArrayBlockingQueue(20)
 * threadFactory：线程工厂类
 */
Executor myThread=new ThreadPoolExecutor(100,100,0L, TimeUnit.SECONDS,new PriorityBlockingQueue(),Executors.defaultThreadFactory());

// 基于ThreadPoolExecutor的定时任务线程
ScheduledThreadPoolExecutor executor  = new ScheduledThreadPoolExecutor(1);
~~~~


---
### 其他定时任务

#### quartz
* 调度器：Scheduler
* 任务：JobDetail
* 触发器：Trigger，包括SimpleTrigger和CronTrigger

依赖：
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.2</version>
</dependency>

~~~~
定义执行任务job：
public class PrintWordsJob implements Job{

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        String printTime = new SimpleDateFormat("yy-MM-dd HH-mm-ss").format(new Date());
        System.out.println("PrintWordsJob start at:" + printTime + ", prints: Hello Job-" + new Random().nextInt(100));

    }
}

创建Schedule，执行任务：
public class MyScheduler {

    public static void main(String[] args) throws SchedulerException, InterruptedException {
        // 1、创建调度器Scheduler
        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        Scheduler scheduler = schedulerFactory.getScheduler();
        // 2、创建JobDetail实例，并与PrintWordsJob类绑定(Job执行内容)
        JobDetail jobDetail = JobBuilder.newJob(PrintWordsJob.class)
                                        .withIdentity("job1", "group1").build();
        // 3、构建Trigger实例,每隔1s执行一次
        Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "triggerGroup1")
                .startNow()//立即生效
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                .withIntervalInSeconds(1)//每隔1s执行一次
                .repeatForever()).build();//一直执行

        //4、执行
        scheduler.scheduleJob(jobDetail, trigger);
        System.out.println("--------scheduler start ! ------------");
        scheduler.start();

        //睡眠
        TimeUnit.MINUTES.sleep(1);
        scheduler.shutdown();
        System.out.println("--------scheduler shutdown ! ------------");


    }
}
~~~~


