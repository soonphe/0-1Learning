# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Java并发

### 重点
* Executor框架和多线程基础

### Thread与Runable如何实现多线程**

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

### 锁的等级：
* 方法锁
* 对象锁
* 类锁


### ThreadPool（线程池）的底层实现和工作原理

#### 为什么要使用线程池
* 当需要多线程并发执行任务时，只能不断的通过new Thread创建线程，每创建一个线程都需要在堆上分配内存空间，同时需要分配虚拟机栈、本地方法栈、程序计数器等线程私有的内存空间，当这个线程对象被可达性分析算法标记为不可用时被GC回收，这样频繁的创建和回收需要大量的额外开销。
* 再者说，JVM的内存资源是有限的，如果系统中大量的创建线程对象，JVM很可能直接抛出OutOfMemoryError异常，还有大量的线程去竞争CPU会产生其他的性能开销，更多的线程反而会降低性能，所以必须要限制线程数。

* 使用线程池好处：
    * 使用线程池可以复用池中的线程，不需要每次都创建新线程，减少创建和销毁线程的开销；
    * 同时，线程池具有队列缓冲策略、拒绝机制和动态管理线程个数，特定的线程池还具有定时执行、周期执行功能，比较重要的一点是线程池可实现线程环境的隔离，例如分别定义支付功能相关线程池和优惠券功能相关线程池，当其中一个运行有问题时不会影响另一个。 



### Java相关问题
~~~~
问：wait()和sleep()的区别
答:
sleep() 方法是线程类（Thread）的静态方法，让调用线程进入睡眠状态，让出执行机会给其他线程，等到休眠时间结束后，线程进入就绪状态和其他线程一起竞争cpu的执行时间。
wait()是Object类的方法，当一个线程执行到wait方法时，它就进入到一个和该对象相关的等待池，同时释放对象的机锁，使得其他线程能够访问，可以通过notify，notifyAll方法来唤醒等待的线程





