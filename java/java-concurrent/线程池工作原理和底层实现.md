# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java并发基础知识

### 重点
* Executor框架和多线程基础

### Thread与Runable如何实现多线程

* Java 5以前实现多线程有两种实现方法：
    * 继承Thread类
    * 实现Runnable接口
* 两种方式都要通过重写run()方法来定义线程的行为，
* 推荐使用后者，因为Java中的继承是单继承，一个类有一个父类，如果继承了Thread类就无法再继承其他类了，显然使用Runnable接口更为灵活。
* 实现Runnable接口相比继承Thread类有如下优势：
    1. 可以避免由于Java的单继承特性而带来的局限
    2. 增强程序的健壮性，代码能够被多个程序共享，代码与数据是独立的
    3. 适合多个相同程序代码的线程区处理同一资源的情况

首先通过Thread类实现
```
class MyThread extends Thread{  
    private int ticket = 5;  
    public void run(){  
        for (int i=0;i<10;i++)  
        {  
            if(ticket > 0){  
                System.out.println("ticket = " + ticket--);  
            }  
        }  
    }  
}  
  
public class ThreadDemo{  
    public static void main(String[] args){  
        new MyThread().start();  
        new MyThread().start();  
        new MyThread().start();  
    }  
} 
```

运行结果：

```
ticket = 5
ticket = 4
ticket = 5
ticket = 5
ticket = 4
ticket = 3
ticket = 2
ticket = 1
ticket = 4
ticket = 3
ticket = 3
ticket = 2
ticket = 1
ticket = 2
ticket = 1
```

每个线程单独卖了5张票，即独立的完成了买票的任务，但实际应用中，比如火车站售票，需要多个线程去共同完成任务，在本例中，即多个线程共同买5张票。

通过实现Runnable借口实现的多线程程序

```
class MyThread implements Runnable{  
    private int ticket = 5;  
    public void run(){  
        for (int i=0;i<10;i++)  
        {  
            if(ticket > 0){  
                System.out.println("ticket = " + ticket--);  
            }  
        }  
    }  
}  
  
public class RunnableDemo{  
    public static void main(String[] args){  
        MyThread my = new MyThread();  
        new Thread(my).start();  
        new Thread(my).start();  
        new Thread(my).start();  
    }  
} 
```

运行结果

```
ticket = 5
ticket = 2
ticket = 1
ticket = 3
ticket = 4
```

* 在第二种方法(Runnable)中，ticket输出的顺序并不是54321，这是因为线程执行的时机难以预测。ticket并不是原子操作。
* 在第一种方法中，我们new了3个Thread对象，即三个线程分别执行三个对象中的代码，因此便是三个线程去独立地完成卖票的任务；
* 而在第二种方法中，我们同样也new了3个Thread对象，但只有一个Runnable对象，3个Thread对象共享这个Runnable对象中的代码，因此，便会出现3个线程共同完成卖票任务的结果。如果我们new出3个Runnable对象，作为参数分别传入3个Thread对象中，那么3个线程便会独立执行各自Runnable对象中的代码，即3个线程各自卖5张票。
* 在第二种方法中，由于3个Thread对象共同执行一个Runnable对象中的代码，因此可能会造成线程的不安全，比如可能ticket会输出-1（如果我们System.out....语句前加上线程休眠操作，该情况将很有可能出现），这种情况的出现是由于，一个线程在判断ticket为1>0后，还没有来得及减1，另一个线程已经将ticket减1，变为了0，那么接下来之前的线程再将ticket减1，便得到了-1。这就需要加入同步操作（即互斥锁），确保同一时刻只有一个线程在执行每次for循环中的操作。而在第一种方法中，并不需要加入同步操作，因为每个线程执行自己Thread对象中的代码，不存在多个线程共同执行同一个方法的情况。


补充：Java 5以后创建线程还有第三种方式：实现Callable接口，该接口中的call方法可以在线程执行结束时产生一个返回值，代码如下所示：
```
class MyTask implements Callable<Integer> {  
    private int upperBounds;  
      
    public MyTask(int upperBounds) {  
        this.upperBounds = upperBounds;  
    }  
      
    @Override  
    public Integer call() throws Exception {  
        int sum = 0;   
        for(int i = 1; i <= upperBounds; i++) {  
            sum += i;  
        }  
        return sum;  
    }  
      
}  
  
public class Test {  
  
    public static void main(String[] args) throws Exception {  
        List<Future<Integer>> list = new ArrayList<>();  
        ExecutorService service = Executors.newFixedThreadPool(10);  
        for(int i = 0; i < 10; i++) {  
            list.add(service.submit(new MyTask((int) (Math.random() * 100))));  
        }  
          
        int sum = 0;  
        for(Future<Integer> future : list) {  
            while(!future.isDone()) ;  
            sum += future.get();  
        }  
          
        System.out.println(sum);  
    }  
}  
```
---

### 线程

#### 线程阻塞

线程可以阻塞于四种状态：

1. 当线程执行Thread.sleep()时，它一直阻塞到指定的毫秒时间之后，或者阻塞被另一个线程打断
2. 当线程碰到一条wait()语句时，它会一直阻塞到接到通知(notify())、被中断或经过了指定毫秒 时间为止(若指定了超时值的话)
3. 线程阻塞与不同的I/O的方式有多种。常见的一种方式是InputStream的read()方法，该方法一直阻塞到从流中读取一个字节的数据为止，它可以无限阻塞，因此不能指定超时时间
4. 线程也可以阻塞等待获取某个对象锁的排它性访问权限(即等待获得synchronized语句必须的锁时阻塞)

并非所有的阻塞状态都是可中断的，以上阻塞状态的前两种可以被中断，后两种不会对中断做出反应。

#### 守护线程
Java中有两类线程：
    * User Thread(用户线程)
    * Daemon Thread(守护线程)
用户线程即运行在前台的线程，而守护线程是运行在后台的线程。 守护线程作用是为其他前台线程的运行提供便利服务，而且仅在普通、非守护线程仍然运行时才需要，比如垃圾回收线程就是一个守护线程。当VM检测仅剩一个守护线程，而用户线程都已经退出运行时，VM就会退出，因为如果没有了守护者，也就没有继续运行程序的必要了。如果有非守护线程仍然活着，VM就不会退出。
守护线程并非只有虚拟机内部提供，用户在编写程序时也可以自己设置守护线程。用户可以用Thread的setDaemon(true)方法设置当前线程为守护线程。
虽然守护线程可能非常有用，但必须小心确保其它所有非守护线程消亡时，不会由于它的终止而产生任何危害。因为你不可能知道在所有的用户线程退出运行前，守护线程是否已经完成了预期的服务任务。一旦所有的用户线程退出了，虚拟机也就退出运行了。因此，不要再守护线程中执行业务逻辑操作(比如对数据的读写等)。
还有几点：
1. setDaemon(true)必须在调用线程的start()方法之前设置，否则会跑出IllegalThreadStateException异常。
2. 在守护线程中产生的新线程也是守护线程
3. 不要认为所有的应用都可以分配给守护线程来进行服务，比如读写操作或者计算逻辑。

#### 线程间通信，使用wait/notify/notifyAll
* Object是所有类的超类，它有5个方法组成了等待/通知机制的核心：
    * notify（）
    * notifyAll（）
    * wait（）
    * wait（long）
    * wait（long，int）
* 在Java中，所有的类都从Object继承而来，因此，所有的类都拥有这些共有方法可供使用。而且，由于他们都被声明为final，因此在子类中不能覆写任何一个方法。

* 在Java中，可以通过配合调用Object对象的wait（）方法和notify（）方法或notifyAll（）方法来实现线程间的通信。在线程中调用wait（）方法，将阻塞等待其他线程的通知（其他线程调用notify（）方法或notifyAll（）方法），在线程中调用notify（）方法或notifyAll（）方法，将通知其他线程从wait（）方法处返回。


#### 各个方法在使用中需要注意的几点：
1. wait（）
```
public final void wait()  throws InterruptedException,IllegalMonitorStateException
```
该方法用来将当前线程置入休眠状态，直到接到通知或被中断为止。在调用wait（）之前，线程必须要获得该对象的对象级别锁，即只能在同步方法或同步块中调用wait（）方法。进入wait（）方法后，当前线程释放锁。在从wait（）返回前，线程与其他线程竞争重新获得锁。如果调用wait（）时，没有持有适当的锁，则抛出IllegalMonitorStateException，它是RuntimeException的一个子类，因此，不需要try-catch结构。
2. notify（）
```
public final native void notify() throws IllegalMonitorStateException
```
该方法也要在同步方法或同步块中调用，即在调用前，线程也必须要获得该对象的对象级别锁，的如果调用notify（）时没有持有适当的锁，也会抛出IllegalMonitorStateException。
该方法用来通知那些可能等待该对象的对象锁的其他线程。如果有多个线程等待，则线程规划器任意挑选出其中一个wait（）状态的线程来发出通知，并使它等待获取该对象的对象锁（notify后，当前线程不会马上释放该对象锁，wait所在的线程并不能马上获取该对象锁，要等到程序退出synchronized代码块后，当前线程才会释放锁，wait所在的线程也才可以获取该对象锁），但不惊动其他同样在等待被该对象notify的线程们。当第一个获得了该对象锁的wait线程运行完毕以后，它会释放掉该对象锁，此时如果该对象没有再次使用notify语句，则即便该对象已经空闲，其他wait状态等待的线程由于没有得到该对象的通知，会继续阻塞在wait状态，直到这个对象发出一个notify或notifyAll。这里需要注意：它们等待的是被notify或notifyAll，而不是锁。这与下面的notifyAll（）方法执行后的情况不同。 

3、notifyAll（）
```
public final native void notifyAll() throws IllegalMonitorStateException
```
该方法与notify（）方法的工作方式相同，重要的一点差异是：

notifyAll使所有原来在该对象上wait的线程统统退出wait的状态（即全部被唤醒，不再等待notify或notifyAll，但由于此时还没有获取到该对象锁，因此还不能继续往下执行），变成等待获取该对象上的锁，一旦该对象锁被释放（notifyAll线程退出调用了notifyAll的synchronized代码块的时候），他们就会去竞争。如果其中一个线程获得了该对象锁，它就会继续往下执行，在它退出synchronized代码块，释放锁后，其他的已经被唤醒的线程将会继续竞争获取该锁，一直进行下去，直到所有被唤醒的线程都执行完毕。

4、wait（long）和wait（long,int）
显然，这两个方法是设置等待超时时间的，后者在超值时间上加上ns，精度也难以达到，因此，该方法很少使用。对于前者，如果在等待线程接到通知或被中断之前，已经超过了指定的毫秒数，则它通过竞争重新获得锁，并从wait（long）返回。另外，需要知道，如果设置了超时时间，当wait（）返回时，我们不能确定它是因为接到了通知还是因为超时而返回的，因为wait（）方法不会返回任何相关的信息。但一般可以通过设置标志位来判断，在notify之前改变标志位的值，在wait（）方法后读取该标志位的值来判断，当然为了保证notify不被遗漏，我们还需要另外一个标志位来循环判断是否调用wait（）方法。


深入理解：
* 如果线程调用了对象的wait（）方法，那么线程便会处于该对象的等待池中，等待池中的线程不会去竞争该对象的锁。
* 当有线程调用了对象的notifyAll（）方法（唤醒所有wait线程）或notify（）方法（只随机唤醒一个wait线程），被唤醒的的线程便会进入该对象的锁池中，锁池中的线程会去竞争该对象锁。
* 优先级高的线程竞争到对象锁的概率大，假若某线程没有竞争到该对象锁，它还会留在锁池中，唯有线程再次调用wait（）方法，它才会重新回到等待池中。而竞争到对象锁的线程则继续往下执行，直到执行完了synchronized代码块，它会释放掉该对象锁，这时锁池中的线程会继续竞争该对象锁。


#### 线程同步
* 同步方法
    * 锁
    * synchronized块
    * 信号量等


---
### ThreadPool（线程池）

#### 为什么要使用线程池
* 当需要多线程并发执行任务时，只能不断的通过new Thread创建线程，每创建一个线程都需要在堆上分配内存空间，同时需要分配虚拟机栈、本地方法栈、程序计数器等线程私有的内存空间，当这个线程对象被可达性分析算法标记为不可用时被GC回收，这样频繁的创建和回收需要大量的额外开销。
* 再者说，JVM的内存资源是有限的，如果系统中大量的创建线程对象，JVM很可能直接抛出OutOfMemoryError异常，还有大量的线程去竞争CPU会产生其他的性能开销，更多的线程反而会降低性能，所以必须要限制线程数。

* 使用线程池好处：
    * 降低资源消耗。使用线程池可以复用池中的线程，不需要每次都创建新线程，减少创建和销毁线程的开销；
    * 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
    * 提高线程的可管理性。线程是稀缺资源，如果无限的创建，不仅会消耗资源，还会较低系统的稳定性，使用线程池可以进行统一分配，调优和监控。
    * 线程池具有队列缓冲策略、拒绝机制和动态管理线程个数，特定的线程池还具有定时执行、周期执行功能，比较重要的一点是线程池可实现线程环境的隔离，例如分别定义支付功能相关线程池和优惠券功能相关线程池，当其中一个运行有问题时不会影响另一个。 

#### 架构实现
  Java 中的线程池是通过 Executor 框架实现的，该框架中用到了Executor，Executors，ExecutorService，ThreadPoolExecutor 这几个类。



#### 如何构造一个线程池对象
* 本文内容我们只聊线程池ThreadPoolExecutor，查看它的源码会发现它继承了AbstractExecutorService抽象类，
而AbstractExecutorService实现了ExecutorService接口，
ExecutorService继承了Executor接口，
所以ThreadPoolExecutor间接实现了ExecutorService接口和Executor接口

* 一般我们使用的execute方法是在Executor接口中定义的，而submit方法是在ExecutorService接口中定义的，所以当我们创建一个Executor类型变量引用ThreadPoolExecutor对象实例时可以使用execute方法提交任务，当我们创建一个ExecutorService类型变量时可以使用submit方法，当然我们可以直接创建ThreadPoolExecutor类型变量使用execute方法或submit方法。

* ThreadPoolExecutor定义了七大核心属性：
    * corePoolSize(int)：核心线程数量。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到任务队列当中。线程池将长期保证这些线程处于存活状态，即使线程已经处于闲置状态。除非配置了allowCoreThreadTimeOut=true，核心线程数的线程也将不再保证长期存活于线程池内，在空闲时间超过keepAliveTime后被销毁。
    * workQueue：阻塞队列，存放等待执行的任务，线程从workQueue中取任务，若无任务将阻塞等待。当线程池中线程数量达到corePoolSize后，就会把新任务放到该队列当中。JDK提供了四个可直接使用的队列实现，
        * 分别是：基于数组的有界队列ArrayBlockingQueue、
        * 基于链表的无界队列LinkedBlockingQueue、
        * 只有一个元素的同步队列SynchronousQueue、
        * 优先级队列PriorityBlockingQueue。在实际使用时一定要设置队列长度。
    * maximumPoolSize(int)：线程池内的最大线程数量，线程池内维护的线程不得超过该数量，大于核心线程数量小于最大线程数量的线程将在空闲时间超过keepAliveTime后被销毁。当阻塞队列存满后，将会创建新线程执行任务，线程的数量不会大于maximumPoolSize。
    * keepAliveTime(long)：线程存活时间，若线程数超过了corePoolSize，线程闲置时间超过了存活时间，该线程将被销毁。除非配置了allowCoreThreadTimeOut=true，核心线程数的线程也将不再保证长期存活于线程池内，在空闲时间超过keepAliveTime后被销毁。
    * TimeUnit unit：线程存活时间的单位，例如TimeUnit.SECONDS表示秒。
    * RejectedExecutionHandler：拒绝策略，当任务队列存满并且线程池个数达到maximunPoolSize后采取的策略。ThreadPoolExecutor中提供了四种拒绝策略，分别是：抛RejectedExecutionException异常的AbortPolicy(如果不指定的默认策略)、使用调用者所在线程来运行任务CallerRunsPolicy、丢弃一个等待执行的任务，然后尝试执行当前任务DiscardOldestPolicy、不动声色的丢弃并且不抛异常DiscardPolicy。项目中如果为了更多的用户体验，可以自定义拒绝策略。
    * threadFactory：创建线程的工厂，虽说JDK提供了线程工厂的默认实现DefaultThreadFactory，但还是建议自定义实现最好，这样可以自定义线程创建的过程，例如线程分组、自定义线程名称等。

* 构造方法
* Executors提供的构造方法：
    Executors.newSingleThreadExecutor：创建一个单线程化的线程池
    Executors.newScheduledThreadPool：创建一个定长线程池，支持定时及周期性任务执行。
    Executors.newFixedThreadPool：创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待
    Executors.newCachedThreadPool：创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
* ThreadPoolExecutor提供的构造方法：(建议使用)
    ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime, TimeUnit unit,BlockingQueue<Runnable> workQueue)
    ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime, TimeUnit unit,BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler)
    ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime, TimeUnit unit,BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory)
    ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime, TimeUnit unit,BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler)
* 可以看到前三个方法最终都调用了最后一个、参数列表最长的那个方法，在这个方法中给七个属性赋值。

### 任务队列workQueue
workQueue 指被提交但未执行的任务队列，它是一个BlockingQueue接口的对象，仅用于存放Runnable对象。 
根据队列功能分类，在ThreadPoolExecutor的构造函数中可使用以下几种BlockingQueue。

* 直接提交的队列  
该功能由 SynchronousQueue对象提供。SynchronousQueue 是一个特殊的BlocingQueue。 它没有容量，每一个插入操作都要等待一个相应的删除操作，反之，每一个删除操作都要等待对应的插入操作。如皋市使用SynchronousQueue，提交的任务不会被真实的保存，而总是将新任务提交给线程执行，如果没有空闲的进程，则尝试创建新的进程，如果进程的数量已达到最大值，则执行拒绝策略。

* 有界的任务队列  
有界的任务队列可以使用ArrayBlockingQueue实现。当使用有界队列时，若有新的任务需要执行，如果线程池的实际线程数小于corePoolSize，则会优先创建新的线程，若大于corePoolSize，则会将新任务假如等待队列。若等待队列已满，无法加入，在总线程数，不大于maximumPoolSize的前提下，创建新的进程执行任务。若大于maximumPoolSize，则执行拒绝策略。

* 无界的任务队列  
无界的任务队列可以通过LinkedBlockingQueue类实现。与有界队列相反，除非系统资源耗尽，否则无界的任务队列不存在任务入队失败的情况。当有新的任务到来，系统的线程数小于corePoolSize时，线程池会产生新的线程执行任务，但当系统的线程数达到corePoolSize后，就会继续增加。若后续仍有新的任务假如，而又没有空闲的线程资源，则任务直接进入对列等待。若任务创建和处理的速度差异很大，无界队列会保持快速增长，知道耗尽系统内存。

* 任务优先队列  
优先任务队列是带有执行优先级的队列，它通过PriorityBlockingQueue实现，可以控制任务的只想你个先后顺序。它是一个特殊的无界队列。

#### 创建线程池对象，强烈建议通过使用ThreadPoolExecutor的构造方法创建，不要使用Executors
* 阿里《Java开发手册》中的一段描述。
    * 【强制】线程池不允许使用Executors创建，建议通过ThreadPoolExecutor的方式创建，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
    * 说明：Executors返回的线程池对象的弊端如下：
    * 1.FixedThreadPool和SingleThreadPool:    允许的请求队列长度为Integet.MAX_VALUE,可能会堆积大量的请求从而导致OOM;
    * 2.CachedThreadPool:    允许创建线程数量为Integet.MAX_VALUE,可能会创建大量的线程，从而导致OOM.
* 使用自定义线程工厂：
    当项目规模逐渐扩展，各系统中线程池也不断增多，当发生线程执行问题时，通过自定义线程工厂创建的线程设置有意义的线程名称可快速追踪异常原因，高效、快速的定位问题。
* 使用自定义拒绝策略：虽然，JDK给我们提供了一些默认的拒绝策略，但我们可以根据项目需求的需要，或者是用户体验的需要，定制拒绝策略，完成特殊需求。
* 线程池划分隔离：不同业务、执行效率不同的分不同线程池，避免因某些异常导致整个线程池利用率下降或直接不可用，进而影响整个系统或其它系统的正常运行。
    

#### 线程池工作原理
1. 通过execute方法提交任务时，当线程池中的线程数小于corePoolSize时，新提交的任务将通过创建一个新线程来执行，即使此时线程池中存在空闲线程。
2. 通过execute方法提交任务时，当线程池中线程数量达到corePoolSize时，新提交的任务将被放入workQueue中，等待线程池中线程调度执行。
3. 通过execute方法提交任务时，当workQueue已存满，且maxmumPoolSize大于corePoolSize时，新提交的任务将通过创建新线程执行。
4. 当线程池中的线程执行完任务空闲时，会尝试从workQueue中取头结点任务执行。
5. 通过execute方法提交任务，当线程池中线程数达到maxmumPoolSize，并且workQueue也存满时，新提交的任务由RejectedExecutionHandler执行拒绝操作。
6. 当线程池中线程数超过corePoolSize，并且未配置allowCoreThreadTimeOut=true，空闲时间超过keepAliveTime的线程会被销毁，保持线程池中线程数为corePoolSize。（注：销毁空闲线程，保持线程数为corePoolSize，不是销毁corePoolSize中的线程。）
7. 当设置allowCoreThreadTimeOut=true时，任何空闲时间超过keepAliveTime的线程都会被销毁。


#### 合理配置线程池
分两种情况：
* CPU密集
    * CPU密集的意思是该任务需要大量的运算，而没有阻塞，CPU一直全速运行。
    * CPU密集任务只有在真正的多核CPU上才可能得到加速(通过多线程)，而在单核CPU上，无论你开几个模拟的多线程，该任务都不可能得到加速，因为CPU总的运算能力就那些。

* IO密集
    * IO密集型，即该任务需要大量的IO，即大量的阻塞。在单线程上运行IO密集型的任务会导致浪费大量的CPU运算能力浪费在等待。所以在IO密集型任务中使用多线程可以大大的加速程序运行，即时在单核CPU上，这种加速主要就是利用了被浪费掉的阻塞时间。

要想合理的配置线程池的大小，首先得分析任务的特性，可以从以下几个角度分析：
1.  任务的性质：CPU密集型任务、IO密集型任务、混合型任务。
2.  任务的优先级：高、中、低。
3.  任务的执行时间：长、中、短。
4.  任务的依赖性：是否依赖其他系统资源，如数据库连接等。

性质不同的任务可以交给不同规模的线程池执行：
* CPU密集型任务应配置尽可能小的线程，如配置CPU个数+1的线程数，
* IO密集型任务应配置尽可能多的线程，因为IO操作不占用CPU，不要让CPU闲下来，应加大线程数量，如配置两倍CPU个数+1，
* 对于混合型的任务，如果可以拆分，拆分成IO密集型和CPU密集型分别处理，前提是两者运行的时间是差不多的，如果处理时间相差很大，则没必要拆分了。
* 若任务对其他系统资源有依赖，如某个任务依赖数据库的连接返回的结果，这时候等待的时间越长，则CPU空闲的时间越长，那么线程数量应设置得越大，才能更好的利用CPU。
* 当然具体合理线程池值大小，需要结合系统实际情况，在大量的尝试下比较才能得出，以上只是前人总结的规律。

~~~~
最佳线程数目 = （（线程等待时间+线程CPU时间）/线程CPU时间 ）* CPU数目
比如平均每个线程CPU运行时间为0.5s，而线程等待时间（非CPU运行时间，比如IO）为1.5s，CPU核心数为8，那么根据上面这个公式估算得到：((0.5+1.5)/0.5)*8=32。

这个公式进一步转化为：
最佳线程数目 = （线程等待时间与线程CPU时间之比 + 1）* CPU数目

可以得出一个结论： 
线程等待时间所占比例越高，需要越多线程。线程CPU时间所占比例越高，需要越少线程。 
以上公式与之前的CPU和IO密集型任务设置线程数基本吻合。
~~~~

### 总结
实际工作中，我们经常使用线程池，对这块的要求不仅是常规的如何使用，原理我们也要清楚是怎么回事。同时，线程池工作原理和底层实现原理也是面试必问的考题，所以，这块是一定要掌握的。

### 相关问题
~~~~
问：wait()和sleep()的区别
答:
sleep() 方法是线程类（Thread）的静态方法，让调用线程进入睡眠状态，让出执行机会给其他线程，等到休眠时间结束后，线程进入就绪状态和其他线程一起竞争cpu的执行时间。
wait()是Object类的方法，当一个线程执行到wait方法时，它就进入到一个和该对象相关的等待池，同时释放对象的机锁，使得其他线程能够访问，可以通过notify，notifyAll方法来唤醒等待的线程





