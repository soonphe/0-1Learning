# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


### Java中的并发工具CountDownLatch、CyclicBarrier、Semapphore使用详解

### CountDownLatch(闭锁)

CountDownLatch是一个计数器闭锁。主要的功能就是：在完成一组线程中执行的操作之前，它允许一个或多个线程通过await()方法来阻塞处于一直等待状态，用给定的计数初始化 CountDownLatch ，调用countDown()方法计数减 1 ，当计数器减少到 0 时，再唤起这些线程继续执行。常用于监听某些初始化操作，等待初始化执行完毕后，通知主线程继续工作。

CountDownLatch 中主要用到的是 countDown()和await()这两个方法。
- await() 用于以执行完成任务的阻塞等待，使当前线程在计数为零之前一直阻塞。
- countDown() 递减计数，如果计数达到零，说明所有任务都执行完成。

它还提供了带参数的 await(long timeout,TimeUnit unit)方法，来指定其他线程等待的时长。如果超时还未完成，则直接跳出阻塞执行下面的流程。常用于接口调用超时的情况等 (用在下面场景就是：超时后，发现C 还未完成，那么A 和 B 扔下 C 就按开始去汇报任务了)

```
//WorkA、WorkB、WorkC实现Runnable即可：
    /**
     * 工人A
     */
    public static class WorkA implements Runnable{

        private CountDownLatch latch;

        public WorkA(CountDownLatch latch) {
            this.latch = latch;
        }

        @Override
        public void run() {

            try {
                System.out.println("工人A准备搬砖，时间:"+ LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
                TimeUnit.SECONDS.sleep(5);
                System.out.println("工人A任务完成，用时5s，时间:"+ LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                //执行 countDown() 进行-1 操作,说明任务已完成
                latch.countDown();
            }
        }
    }
    //WorkB、WorkC省略...

    public static void main(String[] args) {
        //创建一个无界线程池
        ExecutorService executorService = Executors.newCachedThreadPool();
        //创建一个 CountDownLatch 对象,指定计数器为 3
        CountDownLatch latch = new CountDownLatch(3);
        //创建3个任务(同一组线程中,需要使用同一个 CountDownLatch 对象)
        WorkA workA = new WorkA(latch);
        WorkB workB = new WorkB(latch);
        WorkC workC = new WorkC(latch);

        executorService.execute(workA);
        executorService.execute(workB);
        executorService.execute(workC);

        try {
            //执行 await() 阻塞
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("你个小样，干个活这么费劲，快走，一起汇报任务去，时间："+ LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));

 		System.out.println("##########开始执行主线程");
        System.out.println("1+1=" + (1+1));
        System.out.println("主线程执行完成");
        //关闭线程池任务
        executorService.shutdown();
    }
```

### CyclicBarrier(循环栅栏)

CyclicBarrier，也是 JUC 并发包下提供的一个并发工具类。Cyclic即循环的意思，Barrier即屏障的意思。综合起来，CyclicBarrier 即循环屏障 的意思。JDK 中对CyclicBarrier是这样描述的：

> A synchronization aid that allows a set of threads to all wait for each other to reach a common barrier point.
> CyclicBarriers are useful in programs involving a fixed sized party of threads that must occasionally wait for each other.
> The barrier is called cyclic because it can be re-used after the waiting threads are released.


翻译过来，如下：

- CyclicBarrier是一个同步辅助类，它允许一组数据线程相互等待，直到所有线程都到达一个公共的屏障点；
- 在程序中有固定的数量的线程，这些线程有时候必须等待彼此，这种情况下，使用CyclicBarrier很有帮助；
- 这个屏障之所以用循环修饰，是因为在所有的线程释放彼此之后，这个屏障是可以重新使用的

CountDownLatch和CyclicBarrier两个工具类，在功能看起来很相似，不易区分。但是它们二者之间还是存在着一些本质的区别的。CyclicBarrier可以协同多个线程，让多个线程在这个屏障前等待，直到所有线程都达到了这个屏障时，再一起继续执行后面的动作。下面我们就通过对比CountDownLatch类来了解CyclicBarrier的使用。（还是使用类似CountDownLatch的场景来介绍）

```
public class CyclicBarrierDemo {

    /**
     * 线程
     */
    static class Worker implements Runnable{

        private CyclicBarrier barrier;
        //名称
        private String name;

        public Worker(CyclicBarrier barrier,String name) {
            this.barrier = barrier;
            this.name = name;
        }

        @Override
        public void run() {
            String[] arr = {"搬砖","吃饭","扫地"};
            try {
                for (int i = 0; i < 3; i++) {
                    int seconds = (int) (Math.random() * 10 + 1);//产生1到10之间的随机整数
                    TimeUnit.SECONDS.sleep(seconds);
                    System.out.println(name+"，"+arr[i]+"完成，时间："+LocalTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss")));
                    // barrier的await方法，在所有参与者都已经在此 barrier 上调用 await 方法之前，将一直等待。
                    barrier.await();
                    System.out.println(name+"，"+arr[i]+"结束等待，时间："+ LocalTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss")));
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        // 创建一个无界线程池
        ExecutorService exec = Executors.newCachedThreadPool();
        //创建一个 CyclicBarrier 对象,指定线程数为 3
        CyclicBarrier barrier = new CyclicBarrier(3,()-> System.out.println("任务都执行都完成了"));

        exec.execute(new Worker(barrier,"A"));
        exec.execute(new Worker(barrier,"B"));
        exec.execute(new Worker(barrier,"C"));


        System.out.println("##########开始执行主线程");
        System.out.println("1+1=" + (1+1));
        System.out.println("主线程执行完成");

        exec.shutdown();//关闭线程池(仅用作程序中断使用)
    }
}
```
执行结果：

从运行结果看，由于是同一个 CyclicBarrier 对象，C 搬砖先运行到了屏障，await() 等待；B 搬砖完成接着运行到了屏障 await() 等待；A 搬砖最后运行到了 await() 屏障，所有的线程都运行到了这个屏障，三个线程"同时"运行后面的代码。我们可以看到，await() 之后三个线程结束等待的时间一模一样，都是14:53:48。然后循环继续使用该屏障(可循环使用)
```
##########开始执行主线程
1+1=2
主线程执行完成
A，搬砖完成，时间：14:53:40
C，搬砖完成，时间：14:53:42
B，搬砖完成，时间：14:53:48
任务都执行都完成了
B，搬砖结束等待，时间：14:53:48
A，搬砖结束等待，时间：14:53:48
C，搬砖结束等待，时间：14:53:48
C，吃饭完成，时间：14:53:49
A，吃饭完成，时间：14:53:52
B，吃饭完成，时间：14:53:55
任务都执行都完成了
B，吃饭结束等待，时间：14:53:55
C，吃饭结束等待，时间：14:53:55
A，吃饭结束等待，时间：14:53:55
A，扫地完成，时间：14:53:57
B，扫地完成，时间：14:53:59
C，扫地完成，时间：14:54:03
任务都执行都完成了
C，扫地结束等待，时间：14:54:03
A，扫地结束等待，时间：14:54:03
B，扫地结束等待，时间：14:54:03
```

### CountDownLatch 和 CyclicBarrier 区别
CountDownLatch和CyclicBarrier两个工具类，在功能看起来很相似，不易区分。它们两个都是用于多个线程之间的协调工作。但是它们还是存在着一些差别的：

- 是否阻塞main主线程：CountDownLatch 是在多线程执行完成之后，才会执行main主线程，有先后顺序；CyclicBarrier 则没有先后顺序，在多线程任务被阻塞时，并不会影响 main 主线程的运行。
- 是否能够循环使用：CountDownLatch 不能循环使用，当计数器减为 0 就代表线程执行完成，并不能被重置；CyclicBarrier提供了reset()方法，支持循环使用。例如，若计算发生错误，可以重置计数器，并让线程重新执行一次。
- 计数方式：CountDownLatch 使用.await()方法只进行阻塞，使用 .countDown()方式递减，每次递减1。递减源码如下：sync.releaseShared(1);；CyclicBarrier 使用的是 .await() 方式递减，每次递减1。递减源码如下：int index = --count; 【注意：很多文章说是 CyclicBarrier 是使用的 累加方式，在JDK8是错误的，之前版本是累加？？我没研究】

注意：因为使用 CyclicBarrier 的线程都会阻塞在 await() 方法上，所以在线程池中使用 CyclicBarrier 时要特别小心，如果线程池的线程过少，那么就很容易发生死锁了。

### Semaphore(信号量)
Semaphore又称"信号量"，也是一个非常有用的工具类，它相当于是一个并发控制器，用来控制同时访问某个特定资源的操作数量，或者同时执行某个指定操作的数量。Semaphore 内部维护了一组虚拟的许可，许可的数量可以通过构造函数的参数指定。访问特定资源前，必须使用acquire()方法获得许可，如果许可数量为0，该线程则一直阻塞，直到有可用许可。访问资源后，使用release()方法释放许可。

Semaphore(int permits,boolean fair)提供了2个参数。permits 代表资源池的长度；fair 代表 公平许可 或 非公平许可。类似公平锁/非公平锁的概念。Semaphore中主要用到的是acquire() 和release()两个方法，分别用来获取信号量 和 释放信号量。

Semaphore 例子
一个固定长度的资源池，当池为空时，请求资源会失败。使用 Semaphore可以实现当池为空时，请求会阻塞，非空时解除阻塞。也可以使用Semaphore将任何一种容器变成有界阻塞容器。
```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class SemaphoreDemo {
    public static void main(String[] args) {
        // 创建一个无界线程池
        ExecutorService exec = Executors.newCachedThreadPool();
        // 配置只能5个线程同时访问
        final Semaphore semaphore = new Semaphore(5);
        // 模拟10个客户端访问
        for (int i = 0; i < 10; i++) {
            int num = i;
            Runnable task = (() ->{
                try {
                    // 获取许可
                    semaphore.acquire();
                    System.out.println("获得许可: " + num);
                    //休眠随机秒(表示正在执行操作)
                    TimeUnit.SECONDS.sleep((int)(Math.random()*10+1));
                    // 访问完后，释放许可
                    semaphore.release();
                    // availablePermits()指还剩多少个许可
                    System.out.println("----------当前还有多少个许可:" + semaphore.availablePermits());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            exec.execute(task);
        }
        // 退出线程池
        exec.shutdown();
    }
}
```
执行结果：

初始化时，设置许可为 5 个线程可以同时访问。所以1、6、5、2、0获得许可。本例通过随机休眠来模拟执行操作，我们发现休眠完成后线程释放许可后，其他线程才能够获得许可，开始执行它的相关操作。等到所有线程任务执行完成。信号量便会再次回复到初始化时的长度，迎接新任务线程的到来。
```
获得许可: 1
获得许可: 6
获得许可: 5
获得许可: 2
获得许可: 0
----------当前还有多少个许可:1
获得许可: 4
----------当前还有多少个许可:1
获得许可: 3
----------当前还有多少个许可:1
获得许可: 7
----------当前还有多少个许可:1
获得许可: 8
----------当前还有多少个许可:1
获得许可: 9
----------当前还有多少个许可:1
----------当前还有多少个许可:2
----------当前还有多少个许可:3
----------当前还有多少个许可:4
----------当前还有多少个许可:5
```

### Exchanger(交换器)
Exchanger又称"交换器"，是 JDK 5 时随着 JUC 而引入的一个同步器。从字面上来看，这个类的主要作用就是来交换数据。注意只能在两个线程之间进行数据交换。线程会阻塞在 Exchanger 的 exchange() 方法上，直到另外一个线程也到了同一个 Exchanger 的 exchange() 方法时，二者进行数据交换，然后两个线程继续执行自身相关的代码。Exchanger可以看成是一个双向栅栏。

Exchanger例子
两个线程到达栅栏后，交换线程，继续执行。
```
public class ExchangerDemo {

    /**
     * 定义内部线程类 ExchangerThread
     */
    static class ExchangeThread implements Runnable{
        //Exchanger对象
        private Exchanger<String> exchanger;
        //数据
        private String data;
        //传入同一个 Exchanger
        public ExchangeThread(Exchanger<String> exchanger, String data) {
            this.exchanger = exchanger;
            this.data = data;
        }

        @Override
        public void run() {
            try {
                System.out.println(Thread.currentThread().getName() + "线程，数据为:"+data +"，当前时间："+ LocalTime.now());
                String exchange = exchanger.exchange(data);
                System.out.println(Thread.currentThread().getName() + "线程，交换数据后，数据为："+exchange +",当前时间："+ LocalTime.now());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {

        ExecutorService executor = Executors.newCachedThreadPool();
        Exchanger<String> exchanger = new Exchanger<>();

        //线程1
        ExchangeThread thread01 = new ExchangeThread(exchanger,"001");
        //线程2
        ExchangeThread thread02 = new ExchangeThread(exchanger,"002");
        //执行线程1
        executor.execute(thread01);
        //休眠5s
        TimeUnit.SECONDS.sleep(5);
        //执行线程2
        executor.execute(thread02);
        //关闭线程池
        executor.shutdown();

    }
}

```

执行结果：

通过执行结果，我们可以看到两个线程已经交换了数据。代码中我们可以看到两个线程之间间隔5s，通过时间对比发现：thread-1 会等待 thread-2 的到来，5s 后当 thread-2 来了之后，两个线程便开始交换数据，然后再各自执行各自的线程。
```
pool-1-thread-1线程，数据为：001，当前时间：14:23:25
pool-1-thread-2线程，数据为：002，当前时间：14:23:30
pool-1-thread-2线程，交换数据后，数据为：001，当前时间：14:23:30
pool-1-thread-1线程，交换数据后，数据为：002，当前时间：14:23:30
```

### Phaser
Phaser是JDK 7新增的一个同步辅助类，它同样在 JUC 包下，在功能上跟 CyclicBarrier和CountDownLatch差不多，但支持更丰富的用法，在使用上更为灵活。

以下内容，来源于 Phaser 类源码注释：

- Registration（注册）：与其它 barriers 不同的是，在 Phaser 上注册的 parties 数量可能会随着时间的变化而变化。任务可以随时注册（使用register()、bulkRegister() 方法，或者通过构造器提供默认 parties 参数的方式来注册）。并且可以选择在任何一个在到达点时随意撤销该注册（使用arriveAndDeregiste()方法）。就像大多数基本的同步结构一样，注册和撤销只会影响内部 counts；它并不会创建更深的内部记录，所以任务不能查询他们是否已经注册（或许，你可以通过继承来实现类似的记录）

- Synchronization（同步）：和 CyclicBarrier 类似，Phaser 同样也支持重复 awaited。Phaser 类中的 arriveAndAwaitAdvance() 方法的效果类似于 CyclicBarrier 类中的 await() 方法。phaser 的每一代都有一个相关的 phase number，phase number 的初始值为 0，当所有注册的任务都到达 phaser 时 ，phaser 向前推进一步，即：phase+1，当到达最大值(Integer.MAX_VALUE)之后清零。使用 phase numbers 可以独立控制到达phaser 和 已经到达并且在等待其他线程的动作，通过如下两种类型方法可以被任何注册方进行调用：

    - Arrival（到达机制）：
  
      arrive() 和 arriveAndDeregister() 方法记录了到达状态。这些方法并不会阻塞，但是会返回一个相关联的 arrival phase number；也就是说，phaser 的 phase number 就是用来确定哪个已经处于到达状态。当所有的任务都已经到达指定的 phase 时，此时可以执行一个可选的函数。这个函数通过重写 onAdvance(int, int)方法实现，通常可以用来控制终止状态。重写这个方法类似于为 CyclicBarrier 提供一个 barrier action，但比它更灵活。
    
    - Waiting（等待机制）：
  
      awaitAdvance()方法需要一个到达的 phase number 参数，并且会在 phaser 推进到与给定 phase 不同的 phase 时返回。和使用 CyclicBarrier 不同的是，即使等待线程已经被中断，awaitAdvance() 方法也会一直等待。中断和超时 versions 同样时可用的，但是当任务等待中断或者超时后并没有改变 phaser 的状态时，会遇到异常。如果有必要的话，你可以在执行 forceTermination() 方法之后，执行这些异常的相关的 handlers 来执行恢复相关操作。Phasers 同样也可以被在 ForkJoinPool 中执行的任务使用，这样在其他任务阻塞等待一个 phase 时可以保证足够的并行度来执行任务。

- Termination（终止）：我们可以通过isTerminated()方法来检查 phaser 是否进入了终止状态。在终止时，所有的同步方法会立即返回，比如：返回一个负值。在终止时，如果去尝试注册也是无效的。当调用onAdvance() 方法，并返回true时，Termination 终止将会被触发。当取消注册操作导致已注册的 parties 变成了 0 时，onAdvance（）的默认实现也会返回true。也可以重写onAdvance() 方法来定义终止动作。forceTermination()方法也可以释放等待线程并且允许它们终止。

- Tiering（分层）：Phaser支持分层结构(树状构造)来减少竞争，注册了大量 parties 的 Phaser 可能会因为同步竞争消耗很高的成本， 因此可以设置一些子Phaser来共享一个通用的parent。这样的话即使每个操作消耗了更多的开销，但是会提高整体吞吐量。

       在一个分层结构的 phaser 里，子节点 phaser 的注册和取消注册都通过父节点进行管理。子节点phaser通过构造器或 register()、bulkRegister() 方法进行首次注册时，在其父节点上注册。子节点 phaser 通过调用 arriveAndDeregister() 方法进行最后一次取消注册时，也在其父节点上取消注册。

- Monitoring（监控）：当同步方法或许只会被已注册的 parties 调用时，phaser 的当前状态可能会被任何调用者监控。在任何时候，我们可以通过getRegisteredParties() 方法获取全部的 parties 总数，其中 getArrivedParties()方法可以返回已经到达当前 phase 的 parties 数。当剩余的 parties（通过getUnarrivedParties() 方法获取）到达时，phase进入到下一代(即：下一个phase阶段)。这些方法返回的值可能只表示短暂的状态，所以一般来说在同步结构里并没有多大用处。

Phaser 或许可以用来替代 CountDownLatch 来控制可变参数任务量的多线程任务。Phaser 非常适用于在多线程环境下同步协调分阶段计算任务（Fork/Join框架中的子任务之间需同步时，优先使用Phaser）。







