# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## 后端面试资料汇总

### 要点
    0.java基础
    1.Spring
    2.springboot
    3.springcloud
    4.设计模式
    5.数据结构
    6.多线程
    7.锁与同步
    8.依赖注入与控制翻转
    9.AOP与cglib
    10.JVM
    11.计算机网络
    12.分布式
    13.数据库
    14.索引
    15.消息队列MQ
    16.缓存redis
    17.搜索引擎ES
    
### java基础
Java中try catch finally的执行顺序
```
先执行try中代码发生异常执行catch中代码，最后一定会执行finally中代码
```

switch是否能作用在byte上，是否能作用在long上，是否能作用在String上？
```
switch支持以下类型：
基本数据类型：int，byte，short，char
基本数据类型封装类：Integer，Byte，Short，Character
枚举类型：Enum（JDK 5+开始支持）
字符串类型：String（JDK 7+ 开始支持）

switch支持使用byte类型，不支持long类型，String支持在java1.7引入
```

静态内部类、内部类、匿名内部类，为什么内部类会持有外部类的引用？持有的引用是this？还是其它？
```
静态内部类：使用static修饰的内部类
匿名内部类：使用new生成的内部类
因为内部类的产生依赖于外部类，持有的引用是类名.this。
```

### Spring
Spring类的加载机制

    java中的类加载器负载加载来自文件系统、网络或者其他来源的类文件。
    jvm的类加载器默认使用的是双亲委派模式。
    三种默认的类加载器Bootstrap ClassLoader、Extension ClassLoader和System ClassLoader（Application ClassLoader）
    每一个中类加载器都确定了从哪一些位置加载文件

Spring IOC加载全过程：

    Application加载xml
    AbstractApplicationContext的refresh函数载入Bean定义过程
    AbstractApplicationContext子类的refreshBeanFactory()方法
    AbstractRefreshableApplicationContext子类的loadBeanDefinitions方法
    AbstractBeanDefinitionReader读取Bean定义资源
    资源加载器获取要读入的资源
    XmlBeanDefinitionReader加载Bean定义资源
    DocumentLoader将Bean定义资源转换为Document对象
    XmlBeanDefinitionReader解析载入的Bean定义资源文件
    DefaultBeanDefinitionDocumentReader对Bean定义的Document对象解析
    BeanDefinitionParserDelegate解析Bean定义资源文件中的元素
    BeanDefinitionParserDelegate解析元素
    解析元素的子元素
    。。。

Spring的@Transactional如何实现的：

    实现@Transactional原理是基于spring aop，aop又是动态代理模式的实现。

Spring的事务传播级别

    事务传播行为(为了解决业务层方法之间互相调用的事务问题)：当事务方法被另一个事务方法调用时,必须指定事务应该如何传播
    支持当前事务的情况:
    TransactionDefinition.PROPAGATION_REQUIRED: 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
    TransactionDefinition.PROPAGATION_SUPPORTS: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
    TransactionDefinition.PROPAGATION_MANDATORY: 如果当前存在事务，则加入该事务;如果当前没有事务，则抛出异常。(mandatory:强制性)
    不支持当前事务的情况:
    TransactionDefinition.PROPAGATION_REQUIRES_NEW: 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
    TransactionDefinition.PROPAGATION_NOT_SUPPORTED: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
    TransactionDefinition.PROPAGATION_NEVER: 以非事务方式运行，如果当前存在事务，则抛出异常。
    其他情况:
    TransactionDefinition.PROPAGATION_NESTED: 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

### Springboot
Spring和Springboot区别

    1.Spring Boot提供极其快速和简化的操作,让 Spring 开发者快速上手。
    2.Spring Boot提供了 Spring 运行的默认配置。
    3.Spring Boot为通用 Spring项目提供了很多非功能性特性,例如:嵌入式 Serve、Security、统计、健康检查、外部配置等等。
    4.嵌入Tomcat, Jetty Undertow 而且不需要部署他们。
    5.提供的“starters” poms来简化Maven配置

Springboot 怎么实现自动装载组件的
    
    通过Springboot的@EnableAutoConfiguration注解上@Import引入的AutoConfigurationImportSelector.class类来实现的

### springcloud
常用的组件：Eureka，ribbon，Feign，Hystrix

Eureka 和 Nacos对比？怎么做选择？Eureka中高可用是怎么做的？进行的调优有哪些？原理是什么？

    都是服务注册发现中心，但是Nacos还可以用作配置中心，目前来看，建议使用Nacos，因为Eureka已经不在开源，而且性能上和高可用上没有Nacos方便。

zookeeper如何保证数据一致性

    消息广播阶段
    Leader 节点接受事务提交，并且将新的 Proposal 请求广播给 Follower 节点，收集各个节点的反馈，决定是否进行 Commit，在这个过程中，也会使用上一课时提到的 Quorum 选举机制。
    崩溃恢复阶段
    如果在同步过程中出现 Leader 节点宕机，会进入崩溃恢复阶段，重新进行 Leader 选举，崩溃恢复阶段还包含数据同步操作，同步集群中最新的数据，保持集群的数据一致性。
    整个 ZooKeeper 集群的一致性保证就是在上面两个状态之前切换，当 Leader 服务正常时，就是正常的消息广播模式；当 Leader 不可用时，则进入崩溃恢复模式，崩溃恢复阶段会进行数据同步，完成以后，重新进入消息广播阶段。
    
    副本控制协议：
    WARO(Write All Read one)机制：当Client请求向某副本写数据时(更新数据)，只有当所有的副本都更新成功之后，这次写操作才算成功，否则视为失败。
    Quorum机制：假设有N个副本，更新操作wi 在W个副本中更新成功之后，才认为此次更新操作wi 成功。称成功提交的更新操作对应的数据为：“成功提交的数据”。对于读操作而言，至少需要读R个副本才能读到此次更新的数据。
    其中，W+R>N ，即W和R有重叠。一般，W+R=N+1

为什么zookeeper使用奇数个节点
    
    避免脑裂时产生竞争

zookeeper选举算法：

    从3.4.0版本开始，ZooKeeper废弃了0、1、2这三种Leader选举算法，只保留了TCP版本的FastLeaderElection选举算法。
    1. 自增选举轮次
    2. 初始化选票
    3. 发送初始化选票
    4. 接收外部投票
    5. 判断选举轮次
    6. 选票PK
    7. 变更投票
    8. 选票归档
    9. 统计投票
    10. 更新服务器状态
    以上10个步骤就是FastLeaderElection的核心，其中步骤4-9会经过几轮循环，直到有Leader选举产生。

Eureka和zookeeper有哪些不同
    
    ZooKeeper保证的是CP,Eureka保证的是AP
    ZooKeeper在选举期间注册服务瘫痪,虽然服务最终会恢复,但是选举期间不可用的
    Eureka各个节点是平等关系,只要有一台Eureka就可以保证服务可用,而查询到的数据并不是最新的

### 设计模式
常见的设计模式及适用场景：单例，工厂，观察者，建造者，代理。。。

观察者模型：略（挺简单的）

建造者的好处：

    1. 相同的方法，不同的执行顺序，产生不同的事件结果时；
    2. 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时；
    3. 产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式非常合适；

### 数据结构
常用的集合（List,Set,hashmap，hashtable，ConcurrentHashMap）

hashmap常见问题：

    * 1.7和1.8区别：1.8加入红黑树用尾插法（避免头插法环化，出现死循环），扩容后数据存储位置的计算方式也不一样
    * hashmap中默认的数组大小为16
    * 扩容机制：元素个数超过数组大小*loadFactor时，loadFactor的默认值为0.75
    * 第一次扩容：16\*0.75=12，就把数组的大小扩展为2\*16=32，即扩大一倍
    
    * jdk1.8 HashMap的底层实现：Entry数组 + 链表 + 红黑树。
    * 在HashMap类中有一个Entry的内部类（包含了key-value作为实例变量）。
    * put存储键值对时，先计算key的hashcode，定位到合适的数组索引，然后在该索引上的单向链表进行遍历，用equals函数比较key是否存在。如果存在，则覆盖原来的value值；如果不存在，则将新的Entry对象插入到链表头部
    * 当链表长度大于某个值（可能是8）时，链表会变为红黑树，链表的查询效率为O(n)，红黑树的查询效率为O(lgn)，

为什么现在没有人用hashtable:
    
    建议少用hashtable,在单线程中,无需做线程控制,运行效率更高;
    在多线程中,synchronized会造成线程饥饿,死锁,可以用concurrentHashMap替代

hashmap死锁会产生死锁吗，有则如何解决:

    会，
    jdk1.7多线程环境下，如果两个线程都发现HashMap需要重新调整大小，那么它们会同时试着去调整大小。
    在调整大小时，HashMap会将Entry对象不断的插入链表的头部。插入头部也主要是为了防止尾部遍历，否则这对key的HashCode相同的Entry每次添加还要定位到尾节点。
    如果条件竞争发送了，可能会出现环形链表，之后当我们get(key)操作时，就有可能发生死循环。
    
    JDK1.8是打乱顺序排一遍(JDK1.8之中，引入了高低位链表（双端链表）,先根据长度进行高低位分段，然后用尾插法)，
    但是如果恰好打乱后的数据某一段顺序和之前一样，还是会出现死锁问题！

ConcurrentHashMap为什么是线程安全的
    
    jdk1.8ConcurrentHashMap是数组+链表，或者数组+红黑树结构,
    并发控制使用Synchronized关键字和CAS（比较并替换Compare-and-swap）操作保证节点插入扩容的线程安全，在添加元素时候，采用synchronized来保证线程安全，然后计算size的时候采用CAS操作进行计算
    ConcurrentHashMap的扩容机制（最少是16，构造过渡节点，扩容2倍的数组，死循环转移节点，给不同的线程设置不同的下表索引进行扩容操作，synchronized同步锁锁住操作的节点）
    在JDK1.8中，ConcurrentHashMap摒弃了1.7分段锁，使用了乐观锁的实现方式。

ArrayList和Vector的主要区别是什么？

```
ArrayList在Java1.2引入，用于替换Vector

Vector:
线程同步
当Vector中的元素超过它的初始大小时，Vector会将它的容量翻倍

ArrayList:
线程不同步，但性能很好
当ArrayList中的元素超过它的初始大小时，ArrayList只增加50%的大小
```

### 多线程
线程池核心参数

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

线程池工作原理

    1. 通过execute方法提交任务时，当线程池中的线程数小于corePoolSize时，新提交的任务将通过创建一个新线程来执行，即使此时线程池中存在空闲线程。
    2. 通过execute方法提交任务时，当线程池中线程数量达到corePoolSize时，新提交的任务将被放入workQueue中，等待线程池中线程调度执行。
    3. 通过execute方法提交任务时，当workQueue已存满，且maxmumPoolSize大于corePoolSize时，新提交的任务将通过创建新线程执行。
    4. 当线程池中的线程执行完任务空闲时，会尝试从workQueue中取头结点任务执行。
    5. 通过execute方法提交任务，当线程池中线程数达到maxmumPoolSize，并且workQueue也存满时，新提交的任务由RejectedExecutionHandler执行拒绝操作。
    6. 当线程池中线程数超过corePoolSize，并且未配置allowCoreThreadTimeOut=true，空闲时间超过keepAliveTime的线程会被销毁，保持线程池中线程数为corePoolSize。（注：销毁空闲线程，保持线程数为corePoolSize，不是销毁corePoolSize中的线程。）
    7. 当设置allowCoreThreadTimeOut=true时，任何空闲时间超过keepAliveTime的线程都会被销毁。


### 锁与同步

LOCK和synchronized、volatile区别

    1. lock是一个接口，而synchronized是java的关键字，synchronized是内置的语言实现；
    2. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而lock在发生异常时，如果没有主动通过unlock（）去释放锁，则很可能造成死锁现象，因此使用lock（）时需要在finally块中释放锁；
    3. lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不讷讷狗狗响应中断
    4. 通过lock可以知道有没有成功获取锁，而synchronized却无法办到
    5. lock可以提高多个线程进行读操作的效率
    * 在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而竞争资源非常激烈是（既有大量线程同时竞争），此时lock的性能要远远优于synchronized。所以说，在具体使用时适当情况选择。
    volatile是一种稍弱的同步机制，在访问volatile变量时不会执行加锁操作，也就不会执行线程阻塞，因此volatilei变量是一种比synchronized关键字更轻量级的同步机制

ReentrantLock：

    * 直接使用lock接口的话，我们需要实现很多方法，不太方便，ReentrantLock是唯一实现了Lock接口的类，并且ReentrantLock提供了更多的方法，ReentrantLock，意思是“可重入锁”。
    * ReentrantReadWriteLock实现了ReadWriteLock接口.ReadWriteLock也是一个接口，在它里面只定义了两个方法：一个用来获取读锁，一个用来获取写锁。也就是说将文件的读写操作分开，分成2个锁来分配给线程，从而使得多个线程可以同时进行读操作。
    * ReentrantReadWriteLock里面提供了很多丰富的方法，不过最主要的两个方法：readlock()和writelock用来获取读锁和写锁

ThreadLocalT、InheritableThreadLocals和TransmittableThreadLocal区别

    ThreadLocal：父线程的本地变量是无法传递给子线程的
    InheritableThreadLocals：
        支持父线程的本地变量传递给子线程（线程池中可能失效），因为父线程的TLMap是通过init一个Thread的时候进行赋值给子线程的，而线程池在执行异步任务时可能不再需要创建新的线程了，因此也就不会再传递父线程的TLMap给子线程了
        线程不安全（任意子线程针对本地变量的修改都会影响到主线程的本地变量（本质上是同一个对象））
    TransmittableThreadLocal：支持父线程的本地变量传递给子线程，解决线程池异步值传递问题（使用replay进行设置父线程里的本地变量给当前子线程，任务执行完毕，会调用restore恢复该子线程原生的本地变量）


### spring依赖注入和循环依赖

spring依赖注入原理：

    1.构造器注入
    2.接口注入
    3.set方法参数注入

spring循环依赖是如何解决的：

    Spring三级缓存，也就是三个Map集合类：
    singletonObjects：第一级缓存，里面放置的是实例化好的单例对象；
    earlySingletonObjects：第二级缓存，里面存放的是提前曝光的单例对象；
    singletonFactories：第三级缓存，里面存放的是要被实例化的对象的对象工厂。
    循环依赖问题的解决也是基于Java的引用传递，当我们获取到对象的引用时，对象的field或者属性是可以延后设置的。即Spring是先将Bean对象实例化之后再设置对象属性的
    
    举个例子：
    ```
    假设对象A中有属性是对象B，对象B中也有属性是对象A，即A和B循环依赖。
    创建对象A，调用A的构造，并把A保存下来。
    然后准备注入对象A中的依赖，发现对象A依赖对象B，那么开始创建对象B。
    调用B的构造，并把B保存下来。
    然后准备注入B的构造，发现B依赖对象A，对象A之前已经创建了，直接获取A并把A注入B（注意此时的对象A还没有完全注入成功，对象A中的对象B还没有注入），于是B创建成功。
    把创建成功的B注入A，于是A也创建成功了。
    于是循环依赖就被解决了。
    ```

    spring中的循环依赖会有3种情况
    1.构造器循环依赖（不能解决）
        构造器的循环依赖是不可以解决的，spring容器将每一个正在创建的bean标识符放在一个当前创建bean池中，在创建的过程一直在里面，如果在创建的过程中发现已经存在这个池里面了，这时就会抛出异常表示循环依赖了。导致问题：当存在循环依赖时，Spring将无法决定先创建哪个bean，这种情况下，Spring将产生异常BeanCurrentlyInCreationException。
    2.setter和@Autowire循环依赖
        通过spring容器提前暴露刚完成构造器注入，但并未完成其他步骤（如setter注入）的bean来完成的，而且只能决定单例作用域的bean循环依赖
    3.prototype范围的依赖
        对于“prototype”作用域bean，Spring容器无法完成依赖注入，因为“prototype”作用域的bean，Spring容器不进行缓存，因此无法提前暴露一个创建中的Bean。
        说明：singleton是默认作用域，在使用singleton时，Spring容器只存在一个可共享的Bean实例。


### AOP（动、静、cglib）
JDK动态代理只能对实现了接口的类生成代理，而不能针对类；
CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法（继承）实现增强，但是因为采用的是继承，所以该类或方法最好不要声明成final，对于final类或方法，是无法继承的；

JDK动态代理：利用拦截器(拦截器必须实现InvocationHanlder)加上反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
CGLiB动态代理：利用ASM开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。
### Cglib实现原理

CGLib不能对声明为final的方法进行代理，因为CGLib原理是动态生成被代理类的子类。
到jdk8的时候，jdk代理效率高于CGLIB代理，总之，每一次jdk版本升级，jdk代理效率都得到提升，而CGLIB代理消息确有点跟不上步伐。

Spring在选择用JDK还是CGLiB的依据：
当Bean实现接口时，Spring会用JDK的动态代理。
当Bean没有实现接口时，Spring使用CGlib是实现。
如果Bean实现了接口，强制使用CGlib时，（添加CGLIB库，在spring配置中加入<aop:aspectj-autoproxy proxy-target-class="true"/>）。

### Jvm结构
    * 程序计数器：当前线程所执行的字节码的行号指示器，用于记录正在执行的虚拟机字节指令地址，线程私有。
    * Java虚拟栈：存放基本数据类型、对象的引用、方法出口等，线程私有。
    * Native方法栈：和虚拟栈相似，只不过它服务于Native方法，线程私有。
    * Java堆：java内存最大的一块，所有对象实例、数组都存放在java堆，GC回收的地方，线程共享。
    * 方法区：存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码数据等。（即永久带），回收目标主要是常量池的回收和类型的卸载，各线程共享

### jvm有哪些垃圾收集器
* Serial收集器： 单线程的收集器，收集垃圾时，必须stop the world，使用复制算法。
* ParNew收集器： Serial收集器的多线程版本，也需要stop the world，复制算法。
* Parallel Scavenge收集器： 新生代收集器，复制算法的收集器，并发的多线程收集器，目标是达到一个可控的吞吐量。如果虚拟机总共运行100分钟，其中垃圾花掉1分钟，吞吐量就是99%。
* Serial Old收集器： 是Serial收集器的老年代版本，单线程收集器，使用标记整理算法。
* Parallel Old收集器： 是Parallel Scavenge收集器的老年代版本，使用多线程，标记-整理算法。
* CMS(Concurrent Mark Sweep) 收集器： 是一种以获得最短回收停顿时间为目标的收集器，标记清除算法，运作过程：初始标记，并发标记，重新标记，并发清除，收集结束会产生大量空间碎片。
* G1收集器： 标记整理算法实现，运作流程主要包括以下：初始标记，并发标记，最终标记，筛选标记。不会产生空间碎片，可以精确地控制停顿。

### 默认的垃圾收集器是哪个
jdk1.7 默认垃圾收集器Parallel Scavenge(新生代)+Parallel Old(老年代)
jdk1.8 默认垃圾收集器Parallel Scavenge(新生代)+Parallel Old(老年代)
jdk1.9 默认垃圾收集器G1

### 网络,http和https的区别

### 为什么三次握手，四次挥手，为什么要多一次

### https三次握手

### 如何实现分布式锁
基于redis中setNx：拿setnx(如果key存在返回0)争抢锁，抢到后加一个expire过期时间防止忘记释放

### 分布式事务
1.分布式事务—————— 两阶段提交协议
2.使用消息队列来避免分布式事务

### 数据库
隔离级别：读未提交，读已提交，可重复读，串行化四个！默认是可重复读
mysql默认隔离级别：repeatable，（mysql ---repeatable,oracle,sql server ---read commited）
mysql binlog的格式三种：statement,row,mixed
为什么mysql用的是repeatable而不是read committed:在 5.0之前只有statement一种格式，而主从复制存在了大量的不一致，故选用repeatable
为什么默认的隔离级别都会选用read commited 原因有二：repeatable存在间隙锁会使死锁的概率增大，在RR隔离级别下，条件列未命中索引会锁表！而在RC隔离级别下，只锁行

### 数据库索引
MySQL 索引类型有：唯一索引，主键（聚集）索引，非聚集索引，全文索引。
聚合索引:数据行的物理顺序与列值（一般是主键的那一列）的逻辑顺序相同，一个表中只能拥有一个聚集索引。

### 索引回表
直接在索引中不能拿到数据，索引中存储的为主键，必须根据主键值再查一遍

### 索引覆盖
直接在索引中能拿到数据就是索引覆盖

### 联合索引为什么是最左侧匹配（B+树左侧匹配）
索引会根据 age, height, weight从左到右排序，个索引是一个非聚簇索引，查询时需要回表

### mybatis三级缓存
一级缓存：
    默认开启
    一级缓存是在SqlSession 层面进行缓存的，即同一个SqlSession ，多次调用同一个Mapper和同一个方法的同一个参数，只会进行一次数据库查询，然后把数据缓存到缓冲中，以后直接先从缓存中取出数据，不会直接去查数据库
二级缓存：
    默认不开启，需要手动进行配置
    SqlSessionFactory层面给各个SqlSession 对象共享。
    在mybatis-config.xml文件中添加<setting name="cacheEnabled" value="true"/>
    在xxxMapper.xml文件中添加<cache eviction="FIFO" flushInterval="60000" readOnly="false" size="1024" ></cache>
    各个属性意义如下：
        ​eviction：缓存的回收策略。
        flushInterval：缓存刷新间隔。
        readOnly：是否只读。
        size：缓存存放多少个元素。
        ​type：指定自定义缓存的全类名(实现Cache接口即可)。ps：要使用二级缓存，对应的POJO必须实现序列化接口 。
        ​useCache="true"是否使用一级缓存，默认false。sqlSession.clearCache();只是清除当前session中的一级缓存。
        ​useCache配置：如果一条句每次都需要最新的数据，就意味着每次都需要从数据库中查询数据，可以把这个属性设置为false
    二级缓存默认会在insert、update、delete操作后刷新缓存，可以手动配置不更新缓存
三级缓存：
    自定义缓存
    自定义缓存对象，该对象必须实现 org.apache.ibatis.cache.Cache 接口。

### 消息队列：
Rabbitmq、rocketMq、kafka区别

RocketMQ消息事务：
RocketMQ第一阶段发送Prepared消息时，会拿到消息的地址，第二阶段执行本地事物，第三阶段通过第一阶段拿到的地址去访问消息，并修改消息的状态。

### redis：
Redis为什么快：

    1. 纯内存操作
    2. io多路复用
    3. 单线程避免加锁

Redis持久化：

    * 持久化原理：使用fork子线程，存入buffer，通过策略刷入硬盘
    * 持久化策略：RDB(默认),AOF

架构模式：

    * 单机：单节点
    * 主从：默认配置下，master节点可以进行读和写，slave节点只能进行读操作，写操作被禁止，
        slave节点挂了不影响其他slave节点的读和master节点的读和写，重新启动后会将数据从master节点同步过来
        master节点挂了以后，redis就不能对外提供写服务了，因为剩下的slave不能成为master
    * 哨兵sentinel：监控检查主和从服务器是否正常，故障提醒，自动故障迁移
        缺点：主从切换需要时间，会丢失数据；还是没有解决主节点写的压力；
    * 集群proxy型：
        缺点：增加了新的 proxy，需要维护其高可用。
    * 集群直连型：Redis-Cluster采用无中心结构，每个节点保存数据和整个集群状态,每个节点都和其他所有节点连接。
        缺点：1、资源隔离性较差，容易出现相互影响的情况。   2、数据通过异步复制,不保证数据的强一致性

redis读写缓慢，如何问题定位与分析
    
    1.使用复杂度高的命令
    2.存储大key
    3.集中过期
    4.内存达到上限
    5.fork耗时严重
    6.绑定CPU
    7.使用Swap
    8.网卡负载过高

### ES

```
Q ) 简单介绍下ES？
A：ES是一种存储和管理基于文档和半结构化数据的数据库（搜索引擎）。它提供实时搜索（ES最近几个版本才提供实时搜索，以前都是准实时）和分析结构化、半结构化文档、数据和地理空间信息数据。

Q ) 请介绍启动ES服务的步骤？
A：启动步骤如下
Windows下进入ES文件夹的bin目录下，点击ElasticSearch.bat开始运行
打开本地9200端口http://localhost:9200, 就可以使用ES了

Q ) 索引是什么?
A: ES集群包含多个索引，每个索引包含一种表，表包含多个文档，并且每个文档包含不同的属性。

Q ) 请解释什么是分片(SHARDs)?
A: 随着索引文件的增加，磁盘容量、处理能力都会变得不够，在这种情况下，将索引数据切分成小段，这就叫分片(SHARDS)。它的出现大大改进了数据查询的效率。

Q ) 什么是副本(REPLICA), 他的作用是什么？
A: 副本是分片的完整拷贝，副本的作用是增加了查询的吞吐率和在极端负载情况下获得高可用的能力。副本有效的帮助处理用户请求。

Q ) 在ES集群中增加和创建索引的步骤是什么？
A: 可以在Kibana中配置新的索引，进行Fields Mapping，设置索引别名。也可以通过HTTP请求来创建索引。

Q ) ES支持哪些类型的查询？
A: 主要分为匹配（文本）查询和基于Term的查询。
文本查询包括基本匹配，match phrase, multi-match, match phrase prefix, common terms, query-string, simple query string.
Term查询，比如term exists, type, term set, range, prefix, ids, wildcard, regexp, and fuzzy.

Q ) 倒排索引
A: 在搜索引擎中，每个文档都有一个对应的文档 ID，文档内容被表示为一系列关键词的集合。例如，文档 1 经过分词，提取了 20 个关键词，每个关键词都会记录它在文档中出现的次数和出现位置。
那么，倒排索引就是关键词到文档 ID 的映射，每个关键词都对应着一系列的文件，这些文件中都出现了关键词。




```

