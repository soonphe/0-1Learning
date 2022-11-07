# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

## 后端面试资料汇总

## 目录
- [java基础](#项目介绍)
- [Spring和SpringBoot和SpringCloud](#Spring和SpringBoot和SpringCloud)
- [JVM](#JVM)
- [设计模式](#设计模式)
- [数据结构](#数据结构)
- [网络结构](#网络结构)
- [数据库](#数据库)
- [高并发与多线程](#高并发与多线程)
- [分布式与高可用](#分布式与高可用)
- [消息系统](#消息系统)
- [缓存Redis](#缓存Redis)
- [ElasticSearch搜索引擎](#ElasticSearch搜索引擎)
- [系统设计和场景](#系统设计和场景)
- [算法](#算法)
    
## java基础
抽象类和接口、普通类的区别
```
抽象类体现继承关系，一个类只能单继承；接口体现实现关系，一个类可以多实现

抽象类中可以定义非抽象方法，供子类直接使用；接口的方法都是抽象，接口中的成员都有固定修饰符

抽象类中的成员变量可以实现多个权限 public private protected final等，接口中只能用 public static final修饰。

抽象类中的抽象方法的修饰符只能为public abstract或者protected abstract，默认为public abstract；

抽象类不能用来创建对象；抽象类可以包含属性、方法、构造方法，但是构造方法不能用于实例化，主要用途是被子类调用。

如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。

接口默认方法（default method）：JDK 1.8允许给接口添加非抽象的方法实现，但必须使用default关键字修饰；定义了default的方法可以不被实现子类所实现，但只能被实现子类的对象调用；如果子类实现了多个接口，并且这些接口包含一样的默认方法，则子类必须重写默认方法；

接口静态方法（static method）：JDK 1.8中允许使用static关键字修饰一个方法，并提供实现，称为接口静态方法。接口静态方法只能通过接口调用（接口名.静态方法名）。
```

Java中try catch finally的执行顺序
```
先执行try中代码发生异常执行catch中代码，最后一定会执行finally中代码，不管有没有出现异常，finally块中代码都会执行

当try和catch中有return时，finally会执行吗？
仍然会，finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，不管finally中的代码怎么样，返回的值都不会改变，任然是之前保存的值），所以函数返回值是在finally执行前确定的

finally如果包含return会怎样？
最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。
```

for和for i和foreach
```
for…in 语句用于对数组或者对象的属性进行循环操作。
语法：for (变量 in 对象){ 在此执行代码}
在这个for …in… 最大问题是不能赋值

for循环是对数组的元素进行循环，而不能引用于非数组对象。
语法：for(int 变量初始值;条件;递增或递减){ 在此执行代码}
在这个for 这个是可以赋值的

foreach会生成lamada的内部的静态方法，然后再根据这个静态方法生成一个类似闭包的函数，然后再调用。
foreach支持遍历删除，但是没有fori的随机访问了。
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

String类为什么是final的。

反射中，Class.forName和classloader的区别

### java new String的时候会创建几个对象
```
String a = "a";
```
创建了1个对象：常量池1个
ldc 出现了1次

```
String a = new String("a");
```
创建了2个对象：常量池1个，堆1个
ldc 出现了1次

```
String a = "a"; String b = "b"; String c = "a" + "b";
```
创建了3个对象：常量池3个
字面量 "a" + "b" 在编译前就已经识别为 ”ab“ 了， 被放到常量池中。所以只有一个对象 ：ab
ldc 出现3次

字符串拼接操作的总结
- 常量 与 常量 的拼接结果在 常量池，原理是 编译期 优化；
- 常量池 中不会存在相同内容的常量；
- 只要其中一个是变量，结果在堆中。 如： String s2 = s1+"DEF" ;
- 变量拼接的原理 是StringBuilder 。
- 如果拼接的结果是调用 intern() 方法，则主动将常量池中 还没有的字符串 对象放入池中，并返回地址。

```
String a = "a"; String b = "b"; String c = a + b;
```
严格来说：创建了4个对象（还创建了StringBuilder对象）：常量池2个，堆2个
StringBuilder.toString 方法的实现是 new String();

```
String str = new String(“a”) + new String(“b”) 
```
创建了6个String对象：常量池2个，堆4个

ldc 出现了2次，表示常量池的 a，b

堆里有 StringBuilder，a，b，ab（StringBuilder.toString()生成，不会在常量池创建）


## Spring和SpringBoot和SpringCloud
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
    TransactionDefinition.PROPAGATION_REQUIRED: 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
    TransactionDefinition.PROPAGATION_SUPPORTS: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
    TransactionDefinition.PROPAGATION_MANDATORY: 如果当前存在事务，则加入该事务;如果当前没有事务，则抛出异常。(mandatory:强制性)
    不支持当前事务的情况:
    TransactionDefinition.PROPAGATION_REQUIRES_NEW: 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
    TransactionDefinition.PROPAGATION_NOT_SUPPORTED: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
    TransactionDefinition.PROPAGATION_NEVER: 以非事务方式运行，如果当前存在事务，则抛出异常。
    其他情况:
    TransactionDefinition.PROPAGATION_NESTED: 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

**AOP（动、静、cglib）**
JDK动态代理只能对实现了接口的类生成代理，而不能针对类；
CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法（继承）实现增强，但是因为采用的是继承，所以该类或方法最好不要声明成final，对于final类或方法，是无法继承的；

JDK动态代理：利用拦截器(拦截器必须实现InvocationHanlder)加上反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
CGLiB动态代理：利用ASM开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

**Cglib实现原理**

CGLib不能对声明为final的方法进行代理，因为CGLib原理是动态生成被代理类的子类。
到jdk8的时候，jdk代理效率高于CGLIB代理，总之，每一次jdk版本升级，jdk代理效率都得到提升，而CGLIB代理消息确有点跟不上步伐。

Spring在选择用JDK还是CGLiB的依据：
当Bean实现接口时，Spring会用JDK的动态代理。
当Bean没有实现接口时，Spring使用CGlib是实现。
如果Bean实现了接口，强制使用CGlib时，（添加CGLIB库，在spring配置中加入<aop:aspectj-autoproxy proxy-target-class="true"/>）。

**把当前用户对象作为成员变量放在Controller里行不行？**
```
如果回答“行”，那就可以pass了，后面所有问题都不用问了；

如果回答“不行”，那么就问为什么；正确答案其实是默认不行，因为Controller默认单例，成员变量在多线程下会有并发安全问题，但可以使用@Scope("prototype")注解成多例对象。

对线程安全一知半解的多半会死在这里，对Spring的scope不了解的也会死在这里。
```

**SpringMVC 默认创建 Bean 是单例的，在高并发情况下是如何保证性能的？**
```
spring中的bean默认是单例的，通常对单例进行多线程访问时，为了线程安全而采用同步机制，以时间换空间的方式，而Spring中是利用ThreadLocal来以空间换取时间，为每一个线程提供变量副本，来保证变量副本对于某一线程都是线程安全的。

用ThreadLocal是为了保证线程安全，实际上ThreadLoacal的key就是当前线程的Thread实例。

虽然spring对象是单例的，但类里面方法对每个线程来说都是独立运行的，不存在多线程问题，只有成员变量有多线程问题，所以方法里面如果有用到成员变量就要考虑用安全的数据结构。

单例模式是spring推荐的配置，单利模式因为大大节省了实例的创建和销毁，它在高并发下能极大的节省资源，提高服务抗压能力。
```

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

spring为什么使用三级缓存？（解决循环依赖）

    1.不支持循环依赖情况下，只有一级缓存生效，二三级缓存用不到
    2.二三级缓存就是为了解决循环依赖，且之所以是二三级缓存而不是二级缓存，主要是可以解决循环依赖对象需要提前被aop代理，以及如果没有循环依赖，早期的bean也不会真正暴露，不用提前执行代理过程，也不用重复执行代理过程。


Spring和Springboot区别

    1.Spring Boot提供极其快速和简化的操作,让 Spring 开发者快速上手。
    2.Spring Boot提供了 Spring 运行的默认配置。
    3.Spring Boot为通用 Spring项目提供了很多非功能性特性,例如:嵌入式 Serve、Security、统计、健康检查、外部配置等等。
    4.嵌入Tomcat, Jetty Undertow 而且不需要部署他们。
    5.提供的“starters” poms来简化Maven配置

Springboot 怎么实现自动装载组件的

    通过Springboot的@EnableAutoConfiguration注解上@Import引入的AutoConfigurationImportSelector.class类来实现的

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

zookeeper是什么一致性，怎么实现的？
```
(2) 一致性分类
一致性是指从统外部读取系统内部的数据时，在一定约束条件下相同，即数据变动在系统内部各节点应该是同步的。根据一致性的强弱程度不同，可以将一致性级别分为如下几种：

① 强一致性（strong consistency）。任何时刻，任何用户都能读取到最近一次成功更新的数据。

② 单调一致性（monotonic consistency）。任何时刻，任何用户一旦读到某个数据在某次更新后的值，那么就不会再读到比这个值更旧的值。也就是说，可获取的数据顺序必是单调递增的。

③ 会话一致性（session consistency）。任何用户在某次会话中，一旦读到某个数据在某次更新后的值，那么在本次会话中就不会再读到比这个值更旧的值。会话一致性是在单调一致性的基础上进一步放松约束，只保证单个用户单个会话内的单调性，在不同用户或同一用户不同会话间则没有保障。

④ 最终一致性（eventual consistency）。用户只能读到某次更新后的值，但系统保证数据将最终达到完全一致的状态，只是所需时间不能保障。

⑤ 弱一致性（weak consistency）。用户无法在确定时间内读到最新更新的值。

ZooKeeper是一种强一致性的服务（通过sync操作），也有人认为是单调一致性（更新时的大多说概念），还有人为是最终一致性（顺序一致性），反正各有各的道理这里就不在争辩了。然后它在分区容错性和可用性上做了一定折中，这和CAP理论是吻合的。
```

Eureka和zookeeper有哪些不同
    
    ZooKeeper保证的是CP,Eureka保证的是AP
    ZooKeeper在选举期间注册服务瘫痪,虽然服务最终会恢复,但是选举期间不可用的
    Eureka各个节点是平等关系,只要有一台Eureka就可以保证服务可用,而查询到的数据并不是最新的

Dubbo隐式传参：
```
dubbo通过客户端向服务器端传递参数，传递参数时path,group,version,dubbo,token,timeout即可key有特殊处理，不能使用这几个特殊key
服务提供方使用：RpcContext.getContext().getAttachments();获取参数，
服务调用方使用：RpcContext.getContext().setAttachment(); 传递参数。
```

## JVM

JVM结构
    * 程序计数器：当前线程所执行的字节码的行号指示器，用于记录正在执行的虚拟机字节指令地址，线程私有。
    * Java虚拟栈：存放基本数据类型、对象的引用、方法出口等，线程私有。
    * Native方法栈：和虚拟栈相似，只不过它服务于Native方法，线程私有。
    * Java堆：java内存最大的一块，所有对象实例、数组都存放在java堆，GC回收的地方，线程共享。
    * 方法区：存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码数据等。（即永久带），回收目标主要是常量池的回收和类型的卸载，各线程共享

**触发GC的场景**

**垃圾回收过程**
JDK1.8之前，JVM中堆空间可以分为新生代、老年代和永久代（1.8后永久代改为元空间，元空间不再使用堆而是使用本地内存）。默认的，新生代 ( Young ) 与老年代 ( Old ) 的比例的值为 1:2 ，可以通过参数 –XX:NewRatio 配置。 
而新生代又分为一个Eden space 和两个survivor space（S0和S1）， 默认的，Eden : from : to = 8 : 1 : 1 ( 可以通过参数 –XX:SurvivorRatio 来设定)

1. 首先，任何新对象都分配到 eden 空间。两个幸存者空间开始时都是空的。
2. 当 eden 空间填满时，将触发一个Minor GC(年轻代的垃圾回收)。
3. 引用的对象将移动到第一个幸存者空间。清除 eden 空间时，将删除未引用的对象。
4. 在下一个Minor GC中，eden空间也会发生同样的事情。 未引用的对象将被删除， 引用的对象将移动到survivor空间。
但是，在这种情况下，它们被移动到第二个 survivor空间（S1）。
此外，来自第一个survivor空间（S0）上最后一个次要 GC的对象的年龄递增并移动到S1。一旦所有幸存的物体都被移动到S1，S0和eden 都会被清除。
5. 在下一个Minor GC 中，将重复相同的过程。然而，这一次survivor 空间切换了。 引用的对象将移动到 S0。幸存的对象已过期(未引用)。eden和S1被清除。
6. 重复前面的过程，在Minor GC 之后，当老化对象达到某个年龄阈值 时，它们将从新生代提升到老年代中。
7. 随着Minor GC的不断发生，对象将继续被提升到老年代空间。
8. 因此，这几乎涵盖了新生代的整个过程。最终，将在老年代上进行Full GC， 以清理和压缩该空间。

**GC算法，年轻代和老年代的GC算法区别**

**jvm垃圾收集器**
* Serial收集器： 单线程的收集器，收集垃圾时，必须stop the world，使用复制算法。
* ParNew收集器： Serial收集器的多线程版本，也需要stop the world，复制算法。
* Parallel Scavenge收集器： 新生代收集器，复制算法的收集器，并发的多线程收集器，目标是达到一个可控的吞吐量。如果虚拟机总共运行100分钟，其中垃圾花掉1分钟，吞吐量就是99%。
* Serial Old收集器： 是Serial收集器的老年代版本，单线程收集器，使用标记整理算法。
* Parallel Old收集器： 是Parallel Scavenge收集器的老年代版本，使用多线程，标记-整理算法。
* CMS(Concurrent Mark Sweep) 收集器： 是一种以获得最短回收停顿时间为目标的收集器，标记清除算法，运作过程：初始标记，并发标记，重新标记，并发清除，收集结束会产生大量空间碎片。
* G1收集器： 标记整理算法实现，运作流程主要包括以下：初始标记，并发标记，最终标记，筛选标记。不会产生空间碎片，可以精确地控制停顿。

**默认的垃圾收集器**
jdk1.7 默认垃圾收集器Parallel Scavenge(新生代)+Parallel Old(老年代)
jdk1.8 默认垃圾收集器Parallel Scavenge(新生代)+Parallel Old(老年代)
jdk1.9 默认垃圾收集器G1

## 设计模式
常见的设计模式及适用场景：单例，工厂，观察者，建造者，代理，装饰器，责任链...

**面向对象编程中，都有哪些设计原则**
```
开闭原则 ：对扩展开放，对修改关闭。就是如果要修改原有的功能或者是扩展功能，尽量去扩展原有的代码，而不是修改原来已有的代码。
里氏替换原则（Liskov Substitution Principle） ：任何子类对象都应该可以替换其派生的超类对象 。即，子类可以扩展父类的功能，但不要修改父类原有的功能。 也就是说，当一个子类继承父类后，尽量不要去重写它原有的方法。
依赖转置（依赖倒置）原则 ：要面向接口编程，不要面向实现编程。两个模块交互时，都访问各自接口，而不是具体的实现类。
单一职责原则 ：一个对象要专注于一种事情，不要让它担任太多责任。
接口隔离原则 ：一个接口尽量只包含用户关心的内容。就是一个接口不要太庞大。
迪米特法则 ：如果两个软件实体之间不是特别必要，尽量不要让他们直接通信。而是找个第三方进行转发，比如使用MQ（消息队列）。
合成复用原则 ：如果在“组合/聚合”和“继承”之间做抉择时，优先选择“组合/聚合”。
```

观察者模型：略

责任链模型：略

装饰器模型：略

建造者的好处：

    1. 相同的方法，不同的执行顺序，产生不同的事件结果时；
    2. 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时；
    3. 产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式非常合适；

## 数据结构
**常用的集合**
List,Set,hashmap，hashtable，ConcurrentHashMap

**栈和队列的理解，如何用队列实现栈**

**Set 怎么实现**
拉链法，拉链法实现个 map

Set是如何实现元素不重复：
```
Set是一个接口，最常用的实现类就是HashSet，今天我们就拿HashSet为例。
将key-value对放入HashMap中时，首先根据key的hashCode()返回值决定该Entry的存储位置，
如果两个key的hash值相同，那么它们的存储位置相同。
如果这个两个key的equals比较返回true。那么新添加的Entry的value会覆盖原来的Entry的value，key不会覆盖。
且HashSet中add()中 map.put(e, PRESENT)==null 为false，HashSet添加元素失败。因此,如果向HashSet中添加一个已经存在的元素，新添加的集合元素不会覆盖原来已有的集合元素。
```

hashmap常见问题：

    * 1.7和1.8区别：1.8加入红黑树用尾插法（避免头插法环化，出现死循环），扩容后数据存储位置的计算方式也不一样
    * hashmap中默认的数组大小为16
    * 扩容机制：元素个数超过数组大小*loadFactor时，loadFactor的默认值为0.75
    * 第一次扩容：16\*0.75=12，就把数组的大小扩展为2\*16=32，即扩大一倍
    
    * jdk1.8 HashMap的底层实现：Entry数组 + 链表 + 红黑树。
    * 在HashMap类中有一个Entry的内部类（包含了key-value作为实例变量）。
    * put存储键值对时，先计算key的hashcode，定位到合适的数组索引，然后在该索引上的单向链表进行遍历，用equals函数比较key是否存在。如果存在，则覆盖原来的value值；如果不存在，则将新的Entry对象插入到链表头部
    * 当链表长度大于某个值（可能是8）时，链表会变为红黑树，链表的查询效率为O(n)，红黑树的查询效率为O(lgn)，

hashmap如何解决hash冲突：
```
hashmap的结构：
HashMap底层是一个数组，数组中的每个元素是链表。
HashMap中的节点是Map.Entry类型的，而不是简单的value，hashmap底层为数组结构，即一个Node<K,V>[] table数组（在jdk6中是Entry<K,V>数组），Node是Map.Entry的实现类。

hashmap使用“拉链法/链表法”解决hash冲突：
put元素进去的时候，计算key的hash值来获取bucketIndex，Entry 对象放入 table 数组的 bucketIndex 索引处。
如果 bucketIndex 索引处已经有了一个 Entry 对象，就会插入到链表的头部。新添加的 Entry 对象指向原有的 Entry 对象（产生一个 Entry 链）。

查询时：
HashMap里面没有出现hash冲突时，没有形成单链表时，hashmap查找元素很快,get()方法能够直接定位到元素。
key值不同的两个或多个Map.Entry<K,V>可能会插在同一个桶下面，但是当查找到某个特定的hash值的时候，下面挂了很多个<K,V>映射，怎么确定哪个是我要找的那个<K,V>呢？
出现单链表后，单个bucket 里存储的不是一个 Entry，而是一个 Entry 链，系统只能必须按顺序遍历每个 Entry，直到找到想搜索的 Entry 为止。如果恰好要搜索的 Entry 位于该 Entry 链的最末端（该 Entry 是最早放入该 bucket 中），那系统必须循环到最后才能找到该元素。
这就是HashMap底层结构的一个亮点，在它的Entry中不仅仅只是插入value的，他是插入整个Entry 的，里面包含key和value的，所以能识别同一个hash值下的不同Map.Entry,
```

为什么Hashmap底层使用数组：
```
1）数组效率高
在HashMap中，定位桶的位置是利用元素的key的哈希值对数组长度取模得到。此时，我们已得到桶的位置。显然数组的查找效率比LinkedList快。
2）可自定义扩容机制
采用基本数组结构，扩容机制可以自己定义，HashMap中数组扩容刚好是2的次幂，在做取模运算的效率高。
```

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

ConcurrentHashMap的实现原理，为什么是线程安全的
    
    jdk1.8ConcurrentHashMap是数组+链表，或者数组+红黑树结构,
    并发控制使用Synchronized关键字和CAS（比较并替换Compare-and-swap）操作保证节点插入扩容的线程安全，在添加元素时候，采用synchronized来保证线程安全，然后计算size的时候采用CAS操作进行计算
    ConcurrentHashMap的扩容机制（最少是16，构造过渡节点，扩容2倍的数组，死循环转移节点，给不同的线程设置不同的下表索引进行扩容操作，synchronized同步锁锁住操作的节点）
    在JDK1.8中，ConcurrentHashMap摒弃了1.7分段锁，使用了乐观锁的实现方式。

**map 加上 lru 怎么加**
参见：[LRU缓存-最近最少使用策略](../../algorithm/case/LRU缓存-最近最少使用策略.md)

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

## 网络结构

### tcp和udp
参见：[TCP与UDP](../../computer-network/TCP与UDP.md)

**网络中哪些协议**

**tcp三次握手，四次挥手，为什么四次挥手要多一次**

**为什么不使用udp，udp不是更快吗**

**tcp如何保证可靠性的**

**tcp发送的报文一定是按顺序的吗，如果乱序了怎么办**
考虑这么一种场景，应该说互联网上经常发生，不可避免，比如说我发送一个很大的文件，也或者说下载，几m大，也不是说很大，tcp报文是有上限的，你应该理解，6万多的字节放不下这个文件，需要分成好几个包去发送，因为两地之间路由器非常多，中间有某些数据在进行路由时晚到了，这是非常容易发生的情况，那你认为tcp是如何保证数据的顺序的正确性

**https三次握手，服务器端第三次握手会持续到什么时候**

### http和https
**http 500状态码**

**HTTPS的加密方式**
```
https = 非对称加密➕数字摘要➕数字签名➕对称加密
```

**CA证书是什么，上面有啥内容**
```
ca证书是为了防止中间人攻击由可信第三方使用私钥对服务器公钥数字签名颁发
用户接收到服务端传来的ca证书后，会根据本地已经存在的ca机构公钥解密得到服务器公钥，还会检查证书所颁发的域名与当前域名是否一致，是否在证书有效期以内
```

**http和https的区别**
```

```

**DDOS攻击是怎么样的**
```
ddos分布式拒绝服务攻击，利用分布式的大量主机同时请求服务试图耗尽服务器资源导致服务无法为正常用户提供。分为直接型ddos和反射型ddos
```

**抓取HTTP报文，怎么抓取HTTPS明文**

**socket 结构**
两对，发送方IP和端口接收方IP和端口，socket 四元组而已

### 如何理解rpc框架，或者说rpc框架应该包括哪些功能

**自定义的rpc是基于什么协议传输数据的**
自定义RPC可以基于TCP协议也可以基于HTTP协议

**为什么不使用http**
HTTP发明的初衷是为了传送超文本的资源，协议设计的比较复杂，参数传递的方式效率也不高。开源的RPC框架针对远程调用协议上的效率会比HTTP快很多。

HTTP需要事先通知，修改Nginx/HAProxy配置。RPC能做到自动通知，不影响上游。

HTTP大部分是通过Json来实现的，字节大小和序列化耗时都比Thrift要更消耗性能。RPC，可以基于Thrift实现高效的二进制传输。

**为什么不使用udp，udp不是更快吗**



## 数据库
### 数据库隔离级别
隔离级别：读未提交，读已提交，可重复读，串行化四个！默认是可重复读
Oracle默认的隔离级别：提交读
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

### SQL里的like走不走索引？为什么不能从左侧匹配？
部分走，`xx%` 右侧匹配可以走，

### 如果非要like的效果，但SQL的执行效率就是提不上去了，怎么办？
这个问题其实是“没有银弹的”，也即没有标准答案，要视具体场景、具体技术架构、具体客户而定，水货遇到这种问题一般是直接懵逼，但凡能说出2-3个值得尝试的方案的就至少不是水货了。

### SQL：行转列（基础）

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

## 高并发与多线程

### 并发和并行的区别
- 并发：当有多个线程在操作时,如果系统只有一个CPU,则它根本不可能真正同时进行一个以上的线程，它只能把CPU运行时间划分成若干个时间段,再将时间段分配给各个线程执行，在一个时间段的线程代码运行时，其它线程处于挂起状。这种方式我们称之为并发(Concurrent)。
- 并行：当系统有一个以上CPU时,则线程的操作有可能非并发。当一个CPU执行一个线程时，另一个CPU可以执行另一个线程，两个线程互不抢占CPU资源，可以同时进行，这种方式我们称之为并行(Parallel)。

1. 并发和并行是即相似又有区别的两个概念，并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔内发生。
2. 在多道程序环境下，并发性是指在一段时间内宏观上有多个程序在同时运行，但在单处理机系统中，每一时刻却仅能有一道程序执行，故微观上这些程序只能是分时地交替执行。
3. 若在计算机系统中有多个处理机，则这些可以并发执行的程序便可被分配到多个处理机上，实现并行执行，即利用每个处理机来处理一个可并发执行的程序，这样，多个程序便可以同时执行。

问题发散：
- 进程有了多线程，能实现并行吗？   可以，但需要多核CPU支持

### 产生死锁的条件
1、互斥条件：一个资源每次只能被一个进程使用；
2、请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放；
3、不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺；
4、循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系；


### 线程池核心参数

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


Lock锁实现逻辑：
```
在java.util.concurrent.locks包中有很多Lock的实现类，常用的有ReentrantLock、ReadWriteLock（实现类ReentrantReadWriteLock），
其实现都依赖java.util.concurrent.AbstractQueuedSynchronizer类，实现思路都大同小异

ReentrantLock把所有Lock接口的操作都委派到一个Sync类上，该类继承了AbstractQueuedSynchronizer：
Sync又有两个子类：公平锁和非公平锁，默认情况下为非公平锁。

加锁实现：AbstractQueuedSynchronizer会把所有的请求线程构成一个CLH队列，当一个线程执行完毕（lock.unlock()）时会激活自己的后继节点，但正在执行的线程并不在队列中，而那些等待执行的线程全部处于阻塞状态，经过调查线程的显式阻塞是通过调用LockSupport.park()完成，而LockSupport.park()则调用sun.misc.Unsafe.park()本地方法，再进一步，HotSpot在Linux中中通过调用pthread_mutex_lock函数把线程交给系统内核进行阻塞。

AbstractQueuedSynchronizer通过构造一个基于阻塞的CLH队列容纳所有的阻塞线程，而对该队列的操作均通过Lock-Free（CAS）操作，但对已经获得锁的线程而言，ReentrantLock实现了偏向锁的功能。

synchronized的底层也是一个基于CAS操作的等待队列，但JVM实现的更精细，把等待队列分为ContentionList和EntryList，目的是为了降低线程的出列速度；当然也实现了偏向锁，从数据结构来说二者设计没有本质区别。但synchronized还实现了自旋锁，并针对不同的系统和硬件体系进行了优化，而Lock则完全依靠系统阻塞挂起等待线程。

当然Lock比synchronized更适合在应用层扩展，可以继承AbstractQueuedSynchronizer定义各种实现，比如实现读写锁（ReadWriteLock），公平或不公平锁；同时，Lock对应的Condition也比wait/notify要方便的多、灵活的多。 
```

LOCK和synchronized、volatile区别

    1. lock是一个接口，而synchronized是java的关键字，synchronized是内置的语言实现；
    2. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而lock在发生异常时，如果没有主动通过unlock（）去释放锁，则很可能造成死锁现象，因此使用lock（）时需要在finally块中释放锁；
    3. lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不响应中断
    4. 通过lock可以知道有没有成功获取锁，而synchronized却无法办到
    5. lock可以提高多个线程进行读操作的效率
    * 在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而竞争资源非常激烈是（既有大量线程同时竞争），此时lock的性能要远远优于synchronized。所以说，在具体使用时适当情况选择。
    volatile是一种稍弱的同步机制，在访问volatile变量时不会执行加锁操作，也就不会执行线程阻塞，因此volatilei变量是一种比synchronized关键字更轻量级的同步机制

ReentrantLock：

    * 直接使用lock接口的话，我们需要实现很多方法，不太方便，ReentrantLock是唯一实现了Lock接口的类，并且ReentrantLock提供了更多的方法，ReentrantLock，意思是“可重入锁”。
    * ReentrantReadWriteLock实现了ReadWriteLock接口.ReadWriteLock也是一个接口，在它里面只定义了两个方法：一个用来获取读锁，一个用来获取写锁。也就是说将文件的读写操作分开，分成2个锁来分配给线程，从而使得多个线程可以同时进行读操作。
    * ReentrantReadWriteLock里面提供了很多丰富的方法，不过最主要的两个方法：readlock()和writelock用来获取读锁和写锁

ThreadLocal：
```
每个thread中都存在一个map, map的类型是ThreadLocal.ThreadLocalMap. Map中的key为一个threadlocal实例
使用场景：Threadlocal用来设置线程私有变量，本身不储存值 主要提供自身引用 和 操作ThreadLocalMap 属性，保证线程的独立性，解决多线程使用共享对象的问题 空间换时间

ThreadLocalHashmap与hashmap区别：
和普通Hashmap类似存储在一个数组内，但与hashmap使用的拉链法解决散列冲突的是 ThreadLocalMap使用开放地址法
//拉链法原理
把具有相同散列地址的关键字(同义词)值放在同一个单链表中，称为同义词链表。
有m个散列地址就有m个链表，同时用指针数组A[0,1,2…m-1]存放各个链表的头指针，凡是散列地址为i的记录都以结点方式插入到以A[i]为指针的单链表中。A中各分量的初值为空指针。
//开放地址法缺点 ： 空间利用率低 开发地址发会在散列冲突时寻找下一个可存入的槽点 为了避免冲突 负载因子会设置的相对较小
ThreadLocalMap的散列值均匀 且经常增删 纯数组较方便 节点数量少时也能节省掉指针域带来的空间开销
数组 初始容量16，负载因子2/3
node节点 的key封装了WeakReference 用于回收

ThreadLocalMap的键为什么使用弱引用：
JVM利用设置ThreadLocalMap的Key为弱引用，来避免内存泄露。
JVM利用调用remove、get、set方法的时候，回收弱引用。
当ThreadLocal存储很多Key为null的Entry的时候，而不再去调用remove、get、set方法，那么将导致内存泄漏。
当使用static ThreadLocal的时候，延长ThreadLocal的生命周期，那也可能导致内存泄漏。因为，static变量在类未加载的时候，它就已经加载，当线程结束的时候，static变量不一定会回收。那么，比起普通成员变量使用的时候才加载，static的生命周期加长将更容易导致内存泄漏危机。

每个thread中都存在一个map, map的类型是ThreadLocal.ThreadLocalMap. Map中的key为一个threadlocal实例. 这个Map的确使用了弱引用,不过弱引用只是针对key. 每个key都弱引用指向threadlocal. 当把threadlocal实例置为null以后,没有任何强引用指向threadlocal实例,所以threadlocal将会被gc回收. 但是,我们的value却不能回收,因为存在一条从current thread连接过来的强引用. 只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收.
所以得出一个结论就是只要这个线程对象被gc回收，就不会出现内存泄露，但在threadLocal设为null和线程结束这段时间不会被回收的，就发生了我们认为的内存泄露。其实这是一个对概念理解的不一致，也没什么好争论的。最要命的是线程对象不被回收的情况，这就发生了真正意义上的内存泄露。比如使用线程池的时候，线程结束是不会销毁的，会再次使用的。就可能出现内存泄露
```

ThreadLocal、InheritableThreadLocals和TransmittableThreadLocal区别

    ThreadLocal：父线程的本地变量是无法传递给子线程的
    InheritableThreadLocals：
        支持父线程的本地变量传递给子线程（线程池中可能失效），因为父线程的TLMap是通过init一个Thread的时候进行赋值给子线程的，而线程池在执行异步任务时可能不再需要创建新的线程了，因此也就不会再传递父线程的TLMap给子线程了
        线程不安全（任意子线程针对本地变量的修改都会影响到主线程的本地变量（本质上是同一个对象））
    TransmittableThreadLocal：支持父线程的本地变量传递给子线程，解决线程池异步值传递问题（使用replay进行设置父线程里的本地变量给当前子线程，任务执行完毕，会调用restore恢复该子线程原生的本地变量）

如果线程数是10000，然后响应时间是100ms，那么QPS怎么计算
```
简单粗暴的计算的话，如果是并发10000的情况下，rt是100ms, 那么qps 就是10万。
深究一下，这100ms都是什么操作？io密集型还是cpu计算密集型？是否可以异步？cpu核心数？线程池大小？网络带宽 内存，都有影响
```

1万并发量和1万qps有什区别
```
举个例子，10000个请求并发抵达，服务端有能力同时处理，那就是10000并发（正在处理）。如果每个请求它都能之花0.5秒处理完，那它1秒内一共处理了20000个请求，叫20000 rps（对应的qps也是类似概念，但是不是request，是query）
- qps：queries per second 并发量：qps * 平均响应时间，并发量是压力，qps是能力
```

## 分布式与高可用

### 分布式协议有哪些

### 分布式id
```
分布式id都有哪些实现方式
比较常见的有三种方式：
- uuid（缺点，会重复，且无法保证多台）
- n 台mysql服务器自增id
- snowflakeid算法

uuid：
用来生成一串无序唯一的32位长度数据。
涉及到以太网卡地址、纳秒级时间、芯片ID等。
底层是一组32位的十六进制数字，理论上总数为1632 = 2128 ≈ 3.4×10123。
就是说每纳秒都能产生上百万个UUID，能用100亿年。
uuid理论上不会重复，random既然是随机当然可能会重复。

优点:
- 本地生成，没有网络消耗，生成简单，没有高可用风险。

缺点:
- 不易于存储：UUID太长，16字节128位，通常以36长度的字符串表示，很多场景不适用。
- 信息不安全：基于MAC地址生成UUID的算法可能会造成MAC地址泄露，这个漏洞曾被用于寻找梅丽莎病毒的制作者位置。
- 无序查询效率低：由于生成的UUID是无序不可读的字符串，所以其查询效率低。
- UUID不适合用来做数据库的唯一ID，如果用UUID做主键，无序的不递增，大家都知道，主键是有 索引的，然后mysql的索引是通过b+树来实现的，每一次新的UUID数据的插入，为了查询的优 化，都会对索引底层的b+树进行修改，因为UUID数据是无序的，所以每一次UUID数据的插入都会对主键的b+树进行很大的修改，严重影响性能。

数据库自增ID：
优点：
- 非常简单，利用现有数据库系统的功能实现，成本小，有DBA专业维护。
- ID号单调自增，可以实现一些对ID有特殊要求的业务。

缺点:
- 强依赖DB，当DB异常时整个系统不可用，属于致命问题。配置主从复制可以尽可能的增加可用性，但是数据一致性在特殊情况下难以保证。主从切换时的不一致可能会导致重复发号。
- ID发号性能瓶颈限制在单台MySQL的读写性能。

snowflakeid算法：
最高位是符号位，始终为0，二进制中最高位为1的都是负数，但是我们生成的id一般都使用整数，所以这个最高位固定是0
41位的时间序列，精确到毫秒级，41位的长度可以使用69年。时间位还有一个很重要的作用是可以根据时间进行排序。
10位的机器标识，10位的长度最多支持部署1024个节点, 一般是5位IDC+5位machine编号，唯一确定一台机器
12位的计数序列号，序列号即一系列的自增id（多线程建议使用atomic），可以支持同一节点同一毫秒生成多个ID序号，若同一毫秒把序列号用完了，则“等待至下一毫秒”, 12位的计数序列号支持每个节点每毫秒产生4096个ID序号。

生成的ID特点:
1、twitter的SnowFlake生成ID能够按照时间有序生成
2、SnowFlake算法生成id的结果是一个64bit大小的整数
3、分布式系统内不会产生重复id（用有datacenterId和workerId来做区分）

雪花算法的优点：
1. 系统环境ID不重复：能满足高并发分布式系统环境ID不重复，比如大家熟知的分布式场景下的数据库表的ID生成。
2. 生成效率极高：在高并发，以及分布式环境下，除了生成不重复 id，每秒可生成百万个不重复 id，生成效率极高。
3. 保证基本有序递增：基于时间戳，可以保证基本有序递增，很多业务场景都有这个需求。
4. 不依赖第三方库：不依赖第三方的库，或者中间件，算法简单，在内存中进行。

缺点： 依赖服务器时间，服务器时钟回拨时可能会生成重复 id。
```

### 分布式锁
基于redis中setNx：拿setnx(如果key存在返回0)争抢锁，抢到后加一个expire过期时间防止忘记释放

### 分布式事务
1. 2PC（ Two-Phase Commit)两阶段提交协议——强一致模型
   - 是将整个事务流程分为两个阶段，准备阶段（Preparephase）、提交阶段（commit phase），2是指两个阶段，P是指准备阶段，C是指提交阶段。
   - 准备阶段（Prepare phase）：事务管理器给每个参与者发送Prepare消息，每个数据库参与者在本地执行事务，并写本地的Undo/Redo日志，此时事务没有提交。 （Undo日志是记录修改前的数据，用于数据库回滚，Redo日志是记录修改后的数据，用于提交事务后写入数 据文件）
   - 提交阶段（commit phase）：如果事务管理器收到了参与者的执行失败或者超时消息时，直接给每个参与者发送回滚(Rollback)消息；否则，发送提交(Commit)消息；参与者根据事务管理器的指令执行提交或者回滚操作，并释放事务处理过程中使用的锁资源。注意:必须在最后阶段释放锁资源。
   - 优缺点：
     - 优点 原理简单，实现方便
     - 缺点：
       - 同步阻塞：二阶段提交协议存在最明显也是最大的一个问题就是同步阻塞，在二阶段提交的执行过程中，所有参与该事务操作的逻辑都处于阻塞状态，也就是说，各个参与者在等待其他参与者响应的过程中，无法进行其他操作。这种同步阻塞极大的限制了分布式系统的性能。
       - 单点问题：协调者在整个二阶段提交过程中很重要，如果协调者在提交阶段出现问题，那么整个流程将无法运转，更重要的是：其他参与者将会处于一直锁定事务资源的状态中，而无法继续完成事务操作。
       - 数据不一致：假设当协调者向所有的参与者发送 commit 请求之后，发生了局部网络异常或者是协调者在尚未发送完所有commit 请求之前自身发生了崩溃，导致最终只有部分参与者收到了 commit 请求。这将导致严重的数据不一致问题。
       - 过于保守：如果在二阶段提交的提交询问阶段中，参与者出现故障而导致协调者始终无法获取到所有参与者的响应信息的话，这时协调者只能依靠其自身的超时机制来判断是否需要中断事务，显然，这种策略过于保守。换句话说，二阶段提交协议没有设计较为完善的容错机制，任意一个节点失败都会导致整个事务的失败。
2. 使用消息队列来避免分布式事务——最终一致性

**分布式理论：一致性协议 3PC**

3PC，全称 “three phase commit”，是 2PC 的改进版，将 2PC 的 “提交事务请求” 过程一分为二，共形成了由CanCommit、PreCommit和doCommit三个阶段组成的事务处理协议。

**2PC对比3PC**
1. 首先对于协调者和参与者都设置了超时机制（在2PC中，只有协调者拥有超时机制，即如果在一定时间内没有收到参与者的消息则默认失败）,主要是避免了参与者在长时间无法与协调者节点通讯（协调者挂掉了）的情况下，无法释放资源的问题，因为参与者自身拥有超时机制会在超时后，自动进行本地commit从而进行释放资源。而这种机制也侧面降低了整个事务的阻塞时间和范围。

2. 通过CanCommit、PreCommit、DoCommit三个阶段的设计，相较于2PC而言，多设置了一个缓冲阶段保证了在最后提交阶段之前各参与节点的状态是一致的 。

3. PreCommit是一个缓冲，保证了在最后提交阶段之前各参与节点的状态是一致的。

问题：3PC协议并没有完全解决数据不一致问题。

### 海量分布式事务问题
场景举例：每秒超过100W以上的并发分布式事务写入操作如何解决？

一般而言，高并发的分布式事务处理，用消息队列做写入缓冲，用二端提交和串联的transaction做事务处理，在出现commit异常的情况下，采用事务补偿，也就是说，先假设事务成功的概率是99%以上，然后检查最终一致性，最后针对出错的不到1%做事务补偿。

但这种也有问题： 消息队列最多承受20W/s的并发，而且消息队列会有很长的延时。

消息队列是可以集群的，最终的瓶颈只是在数据库上面，所以要做多master应该就可以解决了。

如果单库多master还无法解决的话，那就要进行数据库分割。分库分表，多维度分表等。

如果分割了还无法解决的话，那就要采用内存数据库，然后在持久化到磁盘。

补充：其实这类问题基本上没有正确的方案，每一个平台根据业务性质都会不同，唯一能够提供的就是一个大体的思虑，其他的根据自己的业务性质自行提炼和优化。

## 消息系统
Rabbitmq、rocketMq、kafka区别

RocketMQ消息事务：
RocketMQ第一阶段发送Prepared消息时，会拿到消息的地址，第二阶段执行本地事物，第三阶段通过第一阶段拿到的地址去访问消息，并修改消息的状态。

更多问题，参见：[kafka](../../middleware/kafka.md)

## Redis缓存
Redis为什么快：

    1. 纯内存操作
    2. io多路复用
    3. 单线程避免加锁

Redis数据结构有哪几种，为啥不支持int或number
```
String，Hash，List，Set（String无序集合），zset（有序集合，实现方式是跳表+字典）

redis 是使用 C 语言开发，但 C 中并没有 String 类型，只能使用指针或字符数组的形式表示一个字符串，所以 redis 设计了一种简单动态字符串(SDS[Simple Dynamic String])作为底层实现。

在 Redis 中，String 是可以修改的，称为动态字符串(Simple Dynamic String简称SDS)，说是字符串但它的内部结构更像是一个ArrayList，内部维护着一个字节数组，并且在其内部预分配了一定的空间，以减少内存的频繁分配。Redis的内存分配机制是这样：

当字符串的长度小于 1MB时，每次扩容都是加倍现有的空间。
如果字符串长度超过 1MB时，每次扩容时只会扩展 1MB 的空间。

这样既保证了内存空间够用，还不至于造成内存的浪费，字符串最大长度为512MB.。

分析一下字符串的数据结构
struct SDS {
  T capacity;       //数组容量
  T len;            //实际长度
  byte flages;  //标志位,低三位表示类型
  byte[] content;   //数组内容
}

capacity和len两个属性都是泛型，为什么不直接用int类型？因为Redis内部有很多优化方案，为更合理的使用内存，不同长度的字符串采用不同的数据类型表示（int、embstr、raw），且在创建字符串的时候len会和capacity一样大，不产生冗余的空间，所以String值可以是字符串、数字（整数、浮点数) 或者 二进制。

Redis会根据当前值的类型和长度决定使用哪种内部编码来实现。字符串类型的内部编码有3种：

int：8个字节的长整型。
embstr：小于等于39个字节的字符串。
raw：大于39个字节的字符串。
```

Redis持久化：

    * 持久化原理：使用fork子线程，存入buffer，通过策略刷入硬盘
    * 持久化策略：RDB(默认),AOF

redis的全量同步 和 增量同步、异步同步过程？
```
全量复制
master 执行 bgsave ，在本地生成一份 rdb 快照文件。
master node 将 rdb 快照文件发送给 slave node，如果 rdb 复制时间超过 60秒(repl-timeout)，那么 slave node 就会认为复制失败，可以适当调大这个参数(对于千兆网卡的机器，一般每秒传输 100MB，6G 文件，很可能超过 60s)
master node 在生成 rdb 时，会将所有新的写命令缓存在内存中，在 slave node 保存了 rdb 之后，再将新的写命令复制给 slave node。
如果在复制期间，内存缓冲区持续消耗超过 64MB，或者一次性超过 256MB，那么停止复制，复制失败。
client-output-buffer-limitslave256MB64MB60
slave node 接收到 rdb 之后，清空自己的旧数据，然后重新加载 rdb 到自己的内存中，同时基于旧的数据版本对外提供服务。
如果 slave node 开启了 AOF，那么会立即执行 BGREWRITEAOF，重写 AOF。

增量复制
如果全量复制过程中，master-slave 网络连接断掉，那么 slave 重新连接 master 时，会触发增量复制。
master 直接从自己的 backlog 中获取部分丢失的数据，发送给 slave node，默认 backlog 就是 1MB。
master 就是根据 slave 发送的 psync 中的 offset 来从 backlog 中获取数据的。
heartbeat
主从节点互相都会发送 heartbeat 信息。
master 默认每隔 10秒 发送一次 heartbeat，slave node 每隔 1秒 发送一个 heartbeat。

异步复制
master 每次接收到写命令之后，先在内部写入数据，然后异步发送给 slave node。
```

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

Q：redis-zset和java-treeMap区别
```
跳表和红黑树，查询和构建的，读、写、内存、实现成本上略有差异

zset 底层的存储结构有两种：ziplist 和 skiplist。其中，skiplist 就是“跳跃表”结构（简称跳表）。

关于一个 zset 何时使用 ziplist，何时使用 skiplist，有如下的判断条件：zset当数据量大于128个或者字节数大于64转为跳表，否则为压缩列表

zset zrange用法：与单值查找不同，范围查找不能直接按照 score 从高到低排序后，通过比较缩小范围，最终定位到一个节点。我们需要的是类似 sql 中 limit 0,100 这类的效果。
```

## ElasticSearch搜索引擎
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

## 系统设计和场景题

### 秒杀系统
限流，锁ip，锁用户

限流算法（漏桶、令牌桶、滑动窗口）

### 设计rpc
如SpringCloud、Dubbo、ZRPC等

关键指标：
- 初始化时，服务端向注册中心注册
- 客户端从注册中心获取服务端的列表，注册需要的服务
- 当服务端有变化时，注册中心通知客户端
- 客户端通过动态代理的方式反射调用服务端
- 实现了像调用本地服务一样调用远程服务

框架注意点：
- 注册中心：注册中心负责服务的注册和发现，相当于目录服务。服务端启动的时候将服务名称及其对应的地址（IP和端口）注册到注册中心，消费端根据服务名称找到对应的服务地址。有了服务地址，就可以通过网络请求到服务端了。选型Zookeeper or Redis。
- 网络传输：网络通信的话，肯定是采用号称性能之王的Netty
- 序列化：涉及网络通信的话，肯定就涉及到序列化（如dubbo的hession，thrift的thrift，gRPC的protobuf）
- 动态代理：使用动态代理可以屏蔽远程方法调用的细节，比如网络传输
- 负载均衡：随机、轮询、Hash、加权轮询


### 设计服务治理/服务发现系统
那微服务又有哪些问题需要治理？
1. 服务注册与发现。单体服务拆分为微服务后，如果微服务之间存在调用依赖，就需要得到目标服务的服务地址，也就是微服务治理的”服务发现“。要完成服务发现，就需要将服务信息存储到某个载体，载体本身即是微服务治理的”服务注册中心“，而存储到载体的动作即是”服务注册“。
2. 可观测性。微服务由于较单体应用有了更多的部署载体，需要对众多服务间的调用关系、状态有清晰的掌控。可观测性就包括了调用拓扑关系、监控（Metrics）、日志（Logging）、调用追踪（Trace）等。
3. 流量管理。由于微服务本身存在不同版本，在版本更迭过程中，需要对微服务间调用进行控制，以完成微服务版本更迭的平滑。这一过程中需要根据流量的特征（访问参数等）、百分比向不同版本服务分发，这也孵化出灰度发布、蓝绿发布、A/B测试等服务治理的细分主题。
4. 安全。不同微服务承载自身独有的业务职责，对于业务敏感的微服务，需要对其他服务的访问进行认证与鉴权，也就是安全问题。
5. 控制。对服务治理能力充分建设后，就需要有足够的控制能力，能实时进行服务治理策略向微服务分发。


### 短链接生成/分享链接
```
原则就是尽可能短，定长，全局唯一，重定向和一对多。
- 可以先计算url空间大小据此估计设计短链接为多长，使用数字大小写字母来组成链接，一般对于一个app来说，链接长度在5-6位即可，对于整个网络空间7位也足够了。
- 全局唯一其实就是分布式id生成问题，即如何生成尽可能短，按照时间粗略有序且全局唯一的id，再根据id生成链接。
- 重定向一般使用302或307重定向，不使用301主要是因为，301浏览器会对原始url缓存，302和307不会缓存，缓存之后用户访问短链接浏览器直接跳转，服务无法再去收集用户的访问信息。
- 一对多指的是在不同时间生成的同一个视频url短链接应该不一样。

补充一下，一般肯定还会追问分布式id生成怎么搞，参见本项目中其他介绍。
```

### 排行榜
如热搜排行榜，文件下载排行榜等

热搜排行榜实现要点: 性能/简单/时效 .
redis性能高是公认的, redis的数据结构zset有个score,可以实现排序,redis可以给key设定时效
```
    /**
     * 排行榜添加或更新数据score
     * 排行版每天凌晨刷新一遍
     * @param key
     * @param member
     */
    public static void leaderboardAdd(String key, String member){
        // 添加或更新数据
        getRedisTemplate().opsForZSet().incrementScore(key, member, 1);
        // 设置有效期
        LocalDateTime localDateTime = LocalDateTime.of(LocalDate.now(), LocalTime.of(23, 59, 59));
        LocalDateTime now = LocalDateTime.now();
        // 时间差
        Duration between = Duration.between(now, localDateTime);
        getRedisTemplate().expire(key,between);
    }

    /**
     * 通过索引区间返回有序集合成指定区间内的成员 分数从高到低
     *
     * @param key   键
     * @param size  获取个数
     * @return 成员集合
     */
    public static List<LeaderboardDto> leaderboardGet(String key, int size) {
        List<LeaderboardDto> leaderboardDtos = null;
        try {
            Set<ZSetOperations.TypedTuple<String>> typedTuples = getRedisTemplate().opsForZSet().reverseRangeWithScores(key, 0, -1);
            if(CollectionUtils.isNotEmpty(typedTuples)){
                leaderboardDtos = typedTuples.stream().limit(size).map(stringTypedTuple -> {
                    return LeaderboardDto.builder()
                            .value(stringTypedTuple.getValue())
                            .score(stringTypedTuple.getScore())
                            .build();
                }).collect(Collectors.toList());
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return leaderboardDtos;
    }
```

历史搜索的实现也差不多,可以用redis的数据结构list(列表)实现,每次在头部插入数据,然后控制列表的长度就行
```
    /**
     * 添加记录
     *
     * @param key   键
     * @param value 值
     * @return boolean
     */
    public static void lpush(String key, String value) {
        try {
            ListOperations<String, String> redisTemplate = getRedisTemplate();
            redisTemplate.leftPush(key, value);
            // 截取列表长度
            redisTemplate.trim(key,0,max_size-1);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取列表数据
     *
     * @param key   键
     * @return 元素列表
     */
    public static List<String> lrange(String key) {
        try {
            ListOperations<String, String> redisTemplate = getRedisTemplate();
            return redisTemplate.range(key, 0, -1);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
```

文件下载排行榜：也可以使用LRU算法实现，该方案就是在内存中维护一个双向链表。将页面准备展示的排行榜的最小名次作为链表的固定长度，将前台每次下载的文件名称都插入到链表的头部，如果插入的内容超过链表的长度，则把链表的尾节点删除，用以保证链表的长度不变，这样就自然的形成了一个排行榜，最新的下载永远在前边。

### 微信抢红包
类似秒杀活动

秒杀一般流程：
- 锁库存
- 插入“秒杀”记录
- 更新库存

微信红包业务相比普通商品“秒杀”活动，具有海量并发、高安全级别要求的特点。
- 事务级操作量极大。上文介绍微信红包业务特点时提到，普遍情况下同时会有数以万计的微信群在发红包。这个业务特点映射到微信红包系统设计上，就是有数以万计的“并发请求抢锁”同时在进行。这使得 DB 的压力比普通单个商品“库存”被锁要大很多倍。
- 事务性要求严格。微信红包系统本质上是一个资金交易系统，相比普通商品“秒杀”系统有更高的事务级别要求。


**方案一：使用内存操作替代实时的 DB 事务操作**
将“实时扣库存”的行为上移到内存 Cache 中操作，内存 Cache 操作成功直接给 Server 返回成功，然后异步落 DB 持久化。

这个方案的优点是用内存操作替代磁盘操作，提高了并发性能。

但是缺点也很明显，在内存操作成功但 DB 持久化失败，或者内存 Cache 故障的情况下，DB 持久化会丢数据，不适合微信红包这种资金交易系统。

**方案二：悲观锁**
悲观锁的实现方式: SQL + FOR UPDATE。
这样在并发期间一旦有一个事务持有了数据库记录的锁，其他的线程将不能再对数据进行更新了，这就是悲观锁的实现方式。
```
1    <!--悲观锁-->
2    <select id="getRedPacketForUpdate" parameterType="int" resultType="com.demo.entity.RedPacket">
3        select id, user_id as userId, amount, send_date as sendDate, total, unit_amount as unitAmount,
4        stock, version, note
5        from t_red_packet
6        where
7        id = #{id} for update
8    </select>
```
select查询是不加锁的，select…for update是会加锁的，而且是悲观锁，但是在不同查询条件时候加的锁的类型（行锁，表锁）是不同的。
```
select * from t_user where id = 1 for update；
```
在where 后面查询条件是主键索引，唯一索引时候是行锁
查询条件是普通字段时候加的是表锁

问题：
对于悲观锁来说，当一条线程抢占了资源后，其他的线程将得不到资源，那么这个时候， CPU 就会将这些得不到资源的线程挂起，挂起的线程也会消耗CPU 的资源，尤其是在高井发的请求中。

一旦线程提交了事务，那么锁就会被释放，这个时候被挂起的线程就会开始竞争资源，那么竞争到的线程就会被CPU 恢复到运行状态，继续运行。

于是频繁挂起，等待持有锁线程释放资源，一旦释放资源后，就开始抢夺，恢复线程，周而复始直至所有红包资源抢完。试想在高并发的过程中，使用悲观锁就会造成大量的线程被挂起和恢复，这将十分消耗资源，这就是为什么使用悲观锁性能不佳的原因。有些时候，我们也会把悲观锁称为独占锁，毕竟只有一个线程可以独占这个资源，或者称为阻塞锁，因为它会造成其他线程的阻塞。无论如何它都会造成并发能力的下降，从而导致CPU频繁切换线程上下文，造成性能低下。为了克服这个问题，提高并发的能力，避免大量线程因为阻塞导致CPU进行大量的上下文切换，程序设计大师们提出了乐观锁机制，乐观锁已经在企业中被大量应用了。

**方案三：乐观锁**
商品“秒杀”系统中，乐观锁的具体应用方法，是在 DB 的“库存”记录中维护一个版本号。在更新“库存”的操作进行前，先去 DB 获取当前版本号。在更新库存的事务提交时，检查该版本号是否已被其他事务修改。如果版本没被修改，则提交事务，且版本号加 1；如果版本号已经被其他事务修改，则回滚事务，并给上层报错。
```
 1    <!--乐观锁-->
 2    <update id="decreaseRedPacketByVersion">
 3        update t_red_packet
 4        set
 5          stock = stock - 1,
 6          version = version + 1
 7        where
 8          id = #{id}
 9        and version = #{version}
10    </update>
```
存在下面三个问题：
1. 如果拆红包采用乐观锁，那么在并发抢到相同版本号的拆红包请求中，只有一个能拆红包成功，其他的请求将事务回滚并返回失败，给用户报错，用户体验完全不可接受。
2. 如果采用乐观锁，将会导致第一时间同时拆红包的用户有一部分直接返回失败，反而那些“手慢”的用户，有可能因为并发减小后拆红包成功，这会带来用户体验上的负面影响。
3. 如果采用乐观锁的方式，会带来大数量的无效更新请求、事务回滚，给 DB 造成不必要的额外压力。

因此，在高并发的情景下，由于版本不一致的问题，存在大量红包争抢失败的问题。为了提高抢红包的成功率，我们加入重入机制。

按时间戳重入(比如100ms时间内)
```
1        // 记录开始的时间
2        long start = System.currentTimeMillis();        // 无限循环，当抢包时间超过100ms或者成功时退出
3        while(true) {            // 循环当前时间
4            long end = System.currentTimeMillis();            // 如果抢红包的时间已经超过了100ms,就直接返回失败
5            if(end - start > 100) {                return FAILED;
6            }
7            ....
8
9        }
```

按次数重入(比如3次机会之内)
```
 1        // 允许用户重试抢三次红包
 2        for(int i = 0; i < 3; i++) {            
 3            RedPacket redPacket = redPacketDao.getRedPacket(redPacketId);     // 获取红包信息, 注意version信息            
 4            if(redPacket.getStock() > 0) {                   // 如果当前的红包大于0 
 5                int update = redPacketDao.decreaseRedPacketByVersion(redPacketId, redPacket.getVersion());    // 再次传入线程保存的version旧值给SQL判断，是否有其他线程修改过数据                
 6                if(update == 0) {                     // 如果没有数据更新，说明已经有其他线程修改过数据，则继续抢红包
                        continue;
 7                }
 8            ....
 9            }
10            ...
11        }
```
使用乐观锁有助于提高并发性能，但是由于版本号冲突，乐观锁导致多次请求服务失败的概率大大提高，而我们通过重入（按时间戳或者按次数限定）来提高成功的概率，这样对于乐观锁而言实现的方式就相对复杂了，其性能也会随着版本号冲突的概率提升而提升，并不稳定。使用乐观锁的弊端在于， 导致大量的SQL被执行，对于数据库的性能要求较高，容易引起数据库性能的瓶颈，而且对于开发还要考虑重入机制，从而导致开发难度加大。

**方案四：微信红包系统的高并发解决方案**
1. 系统垂直 SET 化，分而治之
微信红包用户发一个红包时，微信红包系统生成一个 ID 作为这个红包的唯一标识。接下来这个红包的所有发红包、抢红包、拆红包、查询红包详情等操作，都根据这个 ID 关联。

红包系统根据这个红包 ID，按一定的规则（如按 ID 尾号取模等），垂直上下切分。切分后，一个垂直链条上的逻辑 Server 服务器、DB 统称为一个 SET。

各个 SET 之间相互独立，互相解耦。并且同一个红包 ID 的所有请求，包括发红包、抢红包、拆红包、查详情详情等，垂直 stick 到同一个 SET 内处理，高度内聚。通过这样的方式，系统将所有红包请求这个巨大的洪流分散为多股小流，互不影响，分而治之。

这个方案解决了同时存在海量事务级操作的问题，将海量化为小量。

2. 逻辑 Server 层将请求排队，解决 DB 并发问题
红包系统是资金交易系统，DB 操作的事务性无法避免，所以会存在“并发抢锁”问题。但是如果到达 DB 的事务操作（也即拆红包行为）不是并发的，而是串行的，就不会存在“并发抢锁”的问题了。

按这个思路，为了使拆红包的事务操作串行地进入 DB，只需要将请求在 Server 层以 FIFO（先进先出）的方式排队，就可以达到这个效果。从而问题就集中到 Server 的 FIFO 队列设计上。

微信红包系统设计了分布式的、轻巧的、灵活的 FIFO 队列方案。其具体实现如下：

**首先，将同一个红包 ID 的所有请求 stick 到同一台 Server。**

上面 SET 化方案已经介绍，同个红包 ID 的所有请求，按红包 ID stick 到同个 SET 中。不过在同个 SET 中，会存在多台 Server 服务器同时连接同一台 DB（基于容灾、性能考虑，需要多台 Server 互备、均衡压力）。

为了使同一个红包 ID 的所有请求，stick 到同一台 Server 服务器上，在 SET 化的设计之外，微信红包系统添加了一层基于红包 ID hash 值的分流。

**其次，设计单机请求排队方案。**

将 stick 到同一台 Server 上的所有请求在被接收进程接收后，按红包 ID 进行排队。然后串行地进入 worker 进程（执行业务逻辑）进行处理，从而达到排队的效果。

**最后，增加 memcached 控制并发。**

为了防止 Server 中的请求队列过载导致队列被降级，从而所有请求拥进 DB，系统增加了与 Server 服务器同机部署的 memcached，用于控制拆同一个红包的请求并发数。

具体来说，利用 memcached 的 CAS 原子累增操作，控制同时进入 DB 执行拆红包事务的请求数，超过预先设定数值则直接拒绝服务。用于 DB 负载升高时的降级体验。

通过以上三个措施，系统有效地控制了 DB 的“并发抢锁”情况。

3. 双维度库表设计，保障系统性能稳定

红包系统的分库表规则，初期是根据红包 ID 的 hash 值分为多库多表。随着红包数据量逐渐增大，单表数据量也逐渐增加。而 DB 的性能与单表数据量有一定相关性。当单表数据量达到一定程度时，DB 性能会有大幅度下降，影响系统性能稳定性。采用冷热分离，将历史冷数据与当前热数据分开存储，可以解决这个问题。

处理微信红包数据的冷热分离时，系统在以红包 ID 维度分库表的基础上，增加了以循环天分表的维度，形成了双维度分库表的特色。

具体来说，就是分库表规则像 db_xx.t_y_dd 设计，其中，xx/y 是红包 ID 的 hash 值后三位，dd 的取值范围在 01~31，代表一个月天数最多 31 天。

通过这种双维度分库表方式，解决了 DB 单表数据量膨胀导致性能下降的问题，保障了系统性能的稳定性。同时，在热冷分离的问题上，又使得数据搬迁变得简单而优雅。

综上所述，微信红包系统在解决高并发问题上的设计，主要采用了 SET 化分治、请求排队、双维度分库表等方案，使得单组 DB 的并发性能提升了 8 倍左右，取得了很好的效果。


### 设计点赞功能
功能要求：
- 用户应该可以对上的内容进行评价 喜欢 like 或者 不喜欢 dislike
- 用户应该可以取消之前的操作 取消喜欢，取消不喜欢。
- 我们的服务应能够记录内容的统计信息，例如喜欢/不喜欢的数据。

非功能需求：
- 该系统应高度可靠，上传的统计信息确保真实可靠。
- 该系统应具有很高的可用性。一致性可能会受到影响（为了可用性）；如果用户没有马上看到其评价，也是可以接受的。
- 挑战：最受欢迎的热门内容如何处理性能瓶颈， 恶意的重复操作。

**容量估算和约束**
假设我们有13亿总用户，其中8亿是日常活动用户（DAU）。

如果用户平均每天评价15个内容，则每秒的总数将为： 800M * 15/86400秒 => 138K URL/秒

考虑到观看和评价20：1的比率，每秒的观看内容将为： 20 * 138K URL/秒 => 2760K URL/秒

存储空间估算：不考虑内容大小，只考虑点赞需求。我们需要50个字节来存储带有每条推文的元数据（例如ID，时间戳，用户ID，喜欢的总数，不喜欢的总数等。假设每天每个活动用户用户有10个内容上传。上传的内容所需的总存储空间为：

800M DAU * 10 个内容 * 50 Byte => 400 GB /天

带宽估算值： 对于评价(写入)请求，由于我们预计每秒138K 个评价，因此我们服务的总传入数据将约相当于 7 MB /秒。

138K URL/秒 * 50 字节 = 7 MB /s

对于观看点赞数的读取请求，由于我们预计每秒进行约 2760K 访问, 因此我们服务的总传出数据将约相当于 14 MB /秒。

2760K URL/秒 * 50 字节 = 14 MB /s

**挑战问题：**

Q：如果某个资源变得受欢迎，该怎么办？拥有该资源的服务器上可能有很多查询；这可能会造成性能瓶颈。这也将影响我们服务的整体性能。
A：重新分区/重新分配数据，要么使用一致的哈希来平衡服务器之间的负载。

Q：恶意的重复操作
A：设置风控规则，对用户封禁、限流

**数据的冷热分离**

Hot（热数据）
- 被频繁查询或更新 （假设为 一个月内：喜欢和不喜欢的执行总次数为10 万以上）
- 对访问的响应时间要求很高，通常在10毫秒以内

Cold（冷数据）
- 不允许更新，偶尔被查询
- 对访问的响应时间要求不高，通常在1~10秒内都可以接受


### 微博feed流或微信朋友圈
Feed流实现的三种方式：

- 拉模式：发布者的内容仅仅写入自己的发件箱里，当用户访问自己的收件箱时，首先遍历这个用户的关系列表，然后再从关系列表的发件箱里读取数据，最后按照规则进行过滤和排序，这种方式的优点是写数据快，但读数据就要慢很多，适合于大V模式；
- 推模式：发布者的内容不仅写入自己的发件箱里，通过批处理任务，还需要写到对应关系人的收件箱里，当用户访问自己的收件箱时，直接从自己的收件箱里读取数据，最后按照规则进行过滤和排序，这种方式的优点是写数据很慢，但读数据就要快很多，适合于关系社交；
- 推拉结合模式：大V与普通用户分开维护。

> Feed流的两个核心：即存储系统、推送系统。

存储系统目的有两个，一个是数据不丢失，一个是数据可以永久保存。早期微的系统使用Redis内存数据库来提高产品的响应速度，但由于成本增长非常快，因此总要有一些其他的方案来进行替代。Feed流系统虽然数据量级很大、对数据的可靠性要求很高，但它有一个很显著的特点，就是“关系极其简单”，这对于我们的技术选型提供了实现的思路。针对这种情况，我们可以设计如下的三种解决方案：

- Mysql分库分表方案，非常稳定可靠，如果数据量级不大，采用这种方案能够有效的降低开发时间及运维时间，由于发展历史很长，技术也很成熟；
- NoSQL方案，典型的代表是HBase，key-value型的数据库天然的适合二元关系场景，能够存储海量的数据，由于发展历史也很长了，因此可靠性是有保障的，但HBase的问题在于需要一定的运维成本；
- 商业方案，TableStore(表格存储)是阿里云2013年推出的一款分布式数据库，存储账号关系是一个比较好的选择。

推送系统目的是解决消息的时效性。Feed流系统有一个很显著的特点，即读写是非常不平衡的，绝大多数情况下，用户都在“读”，而内容只有少量的人来“写”。因此，推送系统会面临如下几个方面的压力：

- TPS/QPS是千万级的；
- 读写操作对于延迟非常敏感；
- 消息严格意义上是不能丢失的；
- 对于主键自增有很强烈的诉求。

因此，推送系统最好是一个性能不错，又能主键自增的系统，而内存数据库Redis就能比较好的满足这些要求。但Redis由于属于内存数据库，成本非常好，而且Redis单机的特性，会带来不小的运维复杂性。对于工程师而言，思考的就是如何尽可能的少向Redis中写数据，比如只计算ID的数据，然后去存储系统获取Feed的信息。


### 楼中楼，嵌套评论怎么实现(如贴吧、微博等)
**表设计：**
用户表(略)
评论表：
```
id	int	自增，主键
uid	int	用户 id
replyid	int	该评论回复的评论 id，没有则为 0
pid	int	该评论所在的父级 id，没有则为 0
aid	varchar(100)	文章的标识(文章的 id/或唯一标识)
content	varchar(300)	评论内容
createtime	int	评论时间的时间戳
```
楼中楼评论条数统计信息
楼中楼评论如有需要显示被评论人名称，可随replyid一起加上对应user相关信息。
楼中楼评论删除

评论展示说明：
- 根据 aid 就能拿到当前文章所有的评论
- 根据pid=0 拿到直接对文章进行的评论，pid 为其他数据的，必然是属于某个评论之下，应当作为楼中楼展示。
- 获取楼中楼评论：根据pid=x 获取楼中楼评论，注意分页

发表评论说明：
- 直接对文章发表评论，pid 与 replyid 为空；
- 对一级评论进行回复，pid 与 replyid 均为一级评论的 id;
- 对楼中楼进行回复，pid 为一级评论的 id，replyid 为你回复的评论的 id

**拓展：帖子id、评论id怎么保证唯一**
自增主键

### 活跃用户数的统计
难点：
- BitMap如何进行相关操作
- 用户id非自增该如何生成（理解成怎么获取的把，扯了会话管理那一套）
   --- 为什么需要拦截器去拦截每次请求

**redis 位图相关命令**
位图（bitmap），就是用位(bit)来表示存放的某种状态，如开关，有无。在redis中，字符串是以二进制的形式存储的，因此位图在redis中并不是一种数据类型，而是一种字符串的表现形式。位图中每个元素在内存中占用1位，所以可以节省存储空间。

1. SETBIT key offset value：对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
   - 位的设置或清除取决于 value 参数，可以是 0 也可以是 1 。
   - 当 key 不存在时，自动生成一个新的字符串值。
   - 字符串会进行伸展以确保它可以将 value 保存在指定的偏移量上。当字符串值进行伸展时，空白位置以 0 填充。
   - offset 参数必须大于或等于 0 ，小于 2^32 (bit 映射被限制在 512 MB 之内)。

2. BITOP operation destkey key [key …]：对一个或多个保存二进制位的字符串 key 进行位元操作，并将结果保存到 destkey 上。
   - operation 可以是 AND 、 OR 、 NOT 、 XOR 这四种操作中的任意一种：
   - BITOP AND destkey key [key ...] ，对一个或多个 key 求逻辑并，并将结果保存到 destkey 。 
   - BITOP OR destkey key [key ...] ，对一个或多个 key 求逻辑或，并将结果保存到 destkey 。 
   - BITOP XOR destkey key [key ...] ，对一个或多个 key 求逻辑异或，并将结果保存到 destkey 。 
   - BITOP NOT destkey key ，对给定 key 求逻辑非，并将结果保存到 destkey 。

3. BITCOUNT key [start] [end]：计算给定字符串中，被设置为 1 的比特位的数量。
   - 一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。
   - start 和 end 参数的设置和 GETRANGE key start end 命令类似，都可以使用负数值： 比如 -1 表示最后一个字节， -2 表示倒数第二个字节，以此类推。
   - 不存在的 key 被当成是空字符串来处理，因此对一个不存在的 key 进行 BITCOUNT 操作，结果为 0 。

为了统计每日登录的用户数，建立了一个bitmap, 每一位标识一个用户ID。
示例：
```
1000 1101 0010 0101
最左边1代表user_id=15，最右边1代表user_id=0 
```
当某个用户登录了网站或执行了某个操作，就在bitmap中把标识此用户的位置为1，未登录默认为0，

然后进行and操作，即可统计活跃用户数。

redis key可以设计成：时间周；如mon，tue，wed，thu，fri，sat，sun

offset 用user_id。如：100、101、102、103 代表用户user_id

用户户登录数据如下：
```
周一：{100,101,102,103}
周二：{101,102,103}
周三：{101,102}
周四：{100,101,102}
周五：{100,101,102,103}
周六：{101,102,103}
周日：{100,101,102}
一周内连续登录的用户有2个:{101,102}
```

统计一周内连续登录用户操作：
```
赋值：
SETBIT mon 100 1
SETBIT mon 101 1
SETBIT mon 102 1
SETBIT mon 103 1

SETBIT tue 101 1
...

统计求和：
BITOP AND result mon tue wed thu fri sat sun

计算总数：
BITCOUNT result
```
使用 bitmap 实现用户上线次数统计： 可以使用 SETBIT key offset value 和 BITCOUNT key [start] [end] 来实现。

比如说，每当用户在某一天上线的时候，我们就使用 SETBIT key offset value ，以用户名作为 key ，将那天所代表的网站的上线日作为 offset 参数，并将这个 offset 上的value设置为 1 。

举个例子，如果今天是网站上线的第 100 天，而用户 peter 在今天阅览过网站，那么执行命令 SETBIT peter 100 1 ；如果明天 peter 也继续阅览网站，那么执行命令 SETBIT peter 101 1 ，以此类推。

当要计算 peter 总共以来的上线次数时，就使用 BITCOUNT key [start] [end] 命令：执行 BITCOUNT peter ，得出的结果就是 peter 上线的总天数。


### 设计订单系统
实现目标：日万级~日千万级的订单量如何设计、重构

一般来说，订单系统架构存在着这样几个问题：下单服务处理接单慢；数据库压力大；数据异构延迟高、缓存数据质量差等：
- 关键逻辑不要使用读写分离的查询方式，避免从库同步延迟造成订单查询异常，比如创建订单之后要创建支付单。但是，在反查订单的时候由于主从延迟，没查询到订单信息，这就可能会造成创建支付单失败。
- 关键逻辑也不要使用缓存来进行订单的查询，这样做，同时是为了避免因为缓存延迟造成订单反查的失败。
- 订单补偿不要粗暴地使用消息队列的方式，避免中间件引发的订单丢失。比如在进行订单状态的修改时，如果处理失败，就将这个订单信息插入到消息队列中，重新消费，以此完成订单的补偿，这种方式在发送消息和接收消息时有可能存在丢消息的情况。
- 接收消息处理失败时一定要让消息重试，避免丢失，尤其注意 return、continue 等关键字。比如，一次消费多条订单记录，一条条地进行处理，如果修改订单成功，就继续处理下一条；如果修改失败，可能会因为 retrun 或 cotinue 关键字将其余的消息都丢失掉了。
- 不能过重依赖于数据库，而且这个数据库还是订单库，持续读写请求会给数据库造成很大的压力，比如，修改订单状态时就需要反查数据库，并进行订单状态的更新，这些操作在高并发写请求下，会造成数据库资源的抢占，从而影响系统的稳定性。
- 为了避免数据不一致，请求访问主要集中在主库，这样主库压力就会很大，因此，就需要实现分库分表的部署架构，下单服务为此也必须改造支持分库分表的架构设计，但由于热点数据的存在，可能导致数据库出现数据倾斜问题，引发提早的数据库扩容。
- 由于下单服务耦合业务过重，使得即使是多集群的部署架构，也很难实现快速的处理响应，更何况不同业务的订单处理流程还不一样，使得系统维护性也会越来愈差。比如，创建订单时由于业务不同，数码、3C、图书等订单包含的信息是不一样的，这就需要特殊处理，这种特殊处理逻辑与创建订单耦合在一起，系统自然就会变得越来越慢。
- 由于数据库存储量的增大，还会导致数据异构性能（数据的结构不同）的直线下降，以及缓存存储容量的不断扩大，这都会极大的影响查询的性能，而且，还可能出现业务间的相互影响等问题。

一般设计思路： 
**下单服务进行了服务拆分**
使用单独的接单服务处理接单，使用订单引擎和订单管道处理订单业务逻辑，
- 接单服务：统一入口，参数处理，服务限流，进行不同业务请求筛选分离（当用户在结算页点击提交订单之后，接单服务会在同一个事务里，将订单插入接单库，将首任务插入任务库，再交由订单引擎进行任务调度）
  - 什么是任务？任务就是执行订单操作的步骤，比如写订单缓存、减库存、发送订单通知等，以及前面提到不同的特殊业务流程，这些都是一个个的任务。我们通过将整个订单处理流程分解成一个一个的任务，逐个单独处理，来应对日千万级的订单处理压力。
- 订单引擎服务：进行任务创建、任务调度，并导向订单处理管道。（可以引入任务状态机，对任务调度进行重试、监控、报警等。）
  - 任务的创建方式：任务库由订单引擎驱动执行，任务通过订单引擎的服务编排能力生成任务队列，首任务执行成功之后，会插入第二个任务，或者，会同时插入第二个和第三个任务。如果插入任务失败，订单引擎会重新执行当前任务，执行成功之后，继续执行插入操作，这里就需要每个任务的业务处理都需要保证幂等性。
  - 任务的调度方式：任务使用多线程的异步方式进行调度，并根据配置选择是串行执行还是并发执行。如果任务执行失败，订单引擎是如何重新执行失败任务的呢？这就是通过任务状态机来实现的，任务状态机就如同一个系统的守护线程，任务状态机通过识别任务的状态，来判断每个任务是执行完成，还是执行失败的，并根据状态来进行任务调度，并且，会多次执行失败的任务，重试调度的频次也会逐渐递减，当超过一定的重试次数之后，就会告警通知人工干预。
- 订单管道服务：分离不同业务线进行不同业务处理，每条业务线由多个业务处理模块组成，业务模块可以在不同业务线之间实现复用。
  - 订单引擎调度订单管道，订单管道去调度远程真实的服务来执行的，其原因在于任务引擎本身就是多线程的设计架构，对线程占用就比较高，而远程调度会注册很多的服务，服务调度也会启动多线程去执行，如果共同部署在同一系统，就会出现线程数过多，造成 CPU 飙升的情况
  - 订单管道请求服务之后，将处理结果返回给任务引擎

场景总结：
用户在结算页点击结算，结算页调用后台的接单服务；

接单服务接收到下单请求之后，会负责接单，并将订单插入到接单库，同时在一个事务里将首任务插入任务库， 并通知调度起订单引擎开始执行任务。

订单引擎，根据任务编号依次进行任务调度，更新任务状态，并由任务状态机进行任务校验补偿处理。 订单引擎通过调度订单管道，实现真实服务的远程调度，订单管道请求服务之后，将处理结果返回给任务引擎。最后，订单中心会在接单服务创建订单时，异步写一份订单缓存在订单中心，然后再通过数据异构的方式，再次写一份数据在订单缓存中。

### 设计定时任务系统
设计要点：
- 单机还是集群
- 多实例任务类型：是同一时间只能在一个应用实例上执行的定时任务；还是可以多个实例同时执行，多实例任务重复执行，产生的脏数据如何处理（可以采用任务处理版本号类似乐观锁等）
- 锁争抢机制：例如，通过 for update语句实现数据库行级锁。 也可以通过Redis提供的分布式锁，不过如果本身应用系统比较简单，还需要搭建Redis集群，解决方案较重。
- 外部信号调度规则：通过一个统一的任务调度框架，发送任务处理信号，可以通过F5等负载均衡路由到某个应用实例上。 但是，无法达到所有实例，都运行某个任务的功能。
- 定时任务时间间隔：如果时间过长会导致任务执行无法即时生效，时间过段可能占用过多应用资源。
- 待执行数据优先级：如果待执行任务的数据分为不同类别，可能需要均衡FIFO或者RAMDOM等策略，防止执行任务不均匀。例如一直在执行C类数据，而D类数据一直堆积。
- 定时任务单条效率：如果单条任务执行时间过程，也会一直抢占线程，导致无法释放资源处理其它数据。
- 定时任务动态配置/可弹性伸缩
- 定时任务执行的状态监控、告警，以及后补偿机制


**操作系统实现定时任务**
1. 维护一个带过期时间的任务链表
```
简单且行之有效的方式。在一个全局链表中维护一个定时任务链。每次 tick 中断来临，遍历该链表找到 expire 到期的任务。
如果将任务以 expire 排序，每次只用找到链头的元素即可，时间复杂度为 O(1)。
这种方式对于早期的 Linux 系统来说没有问题，随着现在的系统复杂度渐渐变化，它无法支撑如今的网络流量暴增时代的需求。
```
2. 时间轮（Timing-Wheel）算法
```
时间轮很容易理解，圆环有 n 个 bucket，每一个 bucket 表示一秒，当前 bucket 表示当前这一秒到来之后要触发的事件。
每个 bucket 会对应着一个链表，链表中存储的就是当前时刻到来要处理的事件。
那这里有个问题来了，如果有个定时任务需要在 16 小时后执行，换算成秒就是 57600s，难道我们的时间轮也要这么多个 bucket 吗。几万个对内存也是一种损耗。
为了减少 bucket 的数量，时间轮算法提供了一个扩展算法，即 Hierarchy 时间轮。

Hierarchy 很好理解，层级制度。既然一个时间轮可能会导致 bucket 过多，那么为什么不能多弄几个轮子来代替时分秒呢？
基于时、分、秒各自实现一个 wheel，每个 wheel 维护一个自己的 cursor，在 Hour 数组中，每个 bucket 代表一个小时。
Minute 数组中每一个 bucket 代表 1 分钟，Second 数组中每个 bucket 代表 1 秒。
采用分层时间轮，我们只需要 24+60+60=144 个 bucket 就可以表示所有的时间。
完全模拟到时钟的用法，Second wheel 每转完 60 个 bucket ，要联动 Minute wheel 转动一格，同理 Minite wheel 转动 60 个 bucket 也要联动 Hours wheel 转动一格。
```
3. 维护一个基于小根堆算法的定时任务
```
小根堆的性质是满足除了根节点以外的每个节点都不小于其父节点的堆。基于这种性质从根节点开始遍历每个节点能保证获取到一个最小优先级的队列。

那么应用到定时器中，每次只用获取当前最小堆的 root 节点看是否到期即可。最小堆的插入时间复杂度为 O(lgn)，获取头结点时间复杂度为 O(1)。
```

### 设计调用链监控系统
调⽤链基本元素：基本所有的调用系统都是这些元素，可能名称叫法不同。
1. 事件： 请求处理过程当中的具体动作（吃饭是一个事件，夹筷子是吃饭的子事件，张嘴是吃饭的子事件。嘴动是吃饭的子事件。都是嵌套关系。）
2. 节点：请求所经过的系统节点，即事件的空间属性。
3. 时间：事件的开始和结束时间
4. 关系：事件与上⼀个事件关系。 （0.1和0.1.1，0.1.2是父子关系 ，0.2和0.2.3，0.2.1，0.2.2是是父子关系。）

跟写作文一样，时间，地点，人物，事件。（事件是一个串一个，还要保证他们之间的关系不是错综复杂的，还要并发的情况这些调用事件不是错乱的才算完成，理论很简单，但是真正要搞明白，在生产环境使用还要解决很多很多问题。）
1. 什么时间？
2. 在什么节点上？
3. 发⽣了什么事情？
4. 这个事情由谁触发？

**事件捕捉方式**：（其实就是输出信息）
1. 硬编码埋点捕捉
2. AOP埋点捕捉
3. 公开组件埋点捕捉
4. 字节码插桩捕捉

**事件串联**：（事件串联的⽬的）
1. 所有事件都关联到同⼀个调⽤
2. 各个事件之间是有层级关系的

为了到达这两个⽬的地，⼏乎所有的调⽤链系统都会有以下两个属性：
- trackID:在整个系统中唯⼀，该值相同的事件表示同⼀次调⽤。
- eventID（spanID）:在⼀次调⽤中唯⼀、并展出事件的层级关系

串联要解决的问题：
1. 怎么⽣成TrackID和eventID
2. 怎么传递参数
3. 怎么并发情况下不允响传递的结果

串联的过程：
1. 由跟踪的起点⽣成⼀个TrackId，⼀直传递⾄所有节点，并保存在事件属性值当中。
2. 由跟踪的起点⽣成初始EventId（SpanID），每捕捉⼀个事件ID加1，每传递⼀次，层级加1。
3. trackId与eventId 的传递及多线程问题：解决办法是每个跟踪请求创建⼀个互相独⽴的会话，EventId的⾃增都基于该会话实现。通常会话对象的存储基于ThreadLocal实现。

**事件的开始与结束**

⼀个事件是⼀个时间段内系统执⾏的若⼲动作，所以对于事件捕捉必须包含开启监听和结束监听两个动作？如果⼀个事件在⼀个⽅法内完成的，这个问题是⽐较好解决的，我们只要在⽅法的开始创建⼀个Event对象，在⽅法结束时调⽤该对像的close ⽅法即可。

public void addUser(){ // 方法的开始处，开启一个监听 Event event=new Event(); //业务代码执行 ..... ..... // 方法的结束处，关闭一个监听 event.close(); }

但如果⼀个事件的开始和结束触发分布在多个对象或⽅法当中，情况就会变得异常复杂。⽐如⼀个JDBC执⾏事件，应该是在构建 Statement 时开始，在Statement 关闭时结束。怎样把这两个触发动作对应到同⼀个事件当中去呢（即传递Event对象）？在这⾥的解决办法是对返回结果进⾏动态代理，把Event放置到代理对象的属性当中，以达到付递的⽬标。当这个⽅法只是适应JDBC这⼀个场景，其它场景需要重新设计Event 传递路径，⽬前还没有通⽤的解决办法。



### 设计监控告警系统
监控告警系统是一个软件系统，给用户提供监控、告警、通知的功能。例如普罗米修斯

监控：监控系统采集并存储监控对象的一个或者多个指标。这里提到了几个名词，稍加解释：
- 监控系统。对下采集一个或者多个监控对象的指标数据并存储，对上暴露接口供上层做应用图形化展示、告警评估、报表；
- 监控对象。在互联网和软件行业，可能是服务器、虚拟机等基础设施，也可能是apiserver、消息队列、数据库等软件；
- 监控指标。监控对象的某一特征，例如服务器的CPU利用率、apiserver的RPS等。一般会周期性的采集，采集方式包括但不限于：Agent主动推送到Server、Server从Agent拉取、Agent发布Server订阅等方式，其值跟时间相关，类似下面的数据：

告警：告警系统根据设定的规则，周期性评估所有规则是否满足条件，并输出评估结果。这里解释一下几个概念：
- 告警规则。告警规则是一个或者多个监控指标运算表达式。例如：以一分钟为评估周期，内存使用率峰值大于60%；
- 评估。对所有的告警规则进行计算；
- 评估结果。评估的结果有三种情况：
  - 满足。表达式成立。例如：内存使用率峰值>60%；
  - 不满足。表达式不成立。例如：内存使用率峰值<=60%;
  - 数据不足。采集的数据无法支撑表达式的计算。例如最近一分钟内没有采集到内存使用率的数据

通知：大多数监控告警系统，会把告警评估后的动作并入告警的范畴，动作可以是执行某个操作，但更多的情况是通知某对象，由某对象来执行具体的操作。通知模块负责将告警评估的结果发布出去，涉及到几个关键部分：
- 发布方式。以何种方式发布？例如：电视墙、大屏、短信、企业微信、邮件、电话、报警铃声等等
- 发布范围。发布的范围？例如：手机或者邮件的收件人列表

### 设计日志采集系统

### 各种海量数据处理

#### mysql海量数据扩容、分库分表
思路：停机扩容(不推荐），不停机扩容(在线双写)

#### MQ海量消息堆积
海量数据的top K问题。海量数据存储于多个文件，任何一条数据都可能存在于任何一个文件当中，现需要筛选出现的次数最多的k条数据。

一般思路：

1）、 依次遍历这些文件，通过hash映射，将每个文件的每条数据映射到新构造的多个小文件中（设生成了n个小文件）；
2）、依次统计每个小文件中出现次数最多的k条数据，构成hash表，hash表中每个键值对的形式为 dataItem: count；
3）、利用堆排序，依次遍历这些hash表，在n∗k条数据中，找出count值最大的k个；

#### 海量日志分析，提取出某日访问某网站次数最多的那个IP
思路：分而治之/hash映射 + hash统计 + 堆/快速/归并排序，就是先映射，后统计，最后排序。

#### 多个海量文件之间对比查重：A和B两个大文件，每个文件都存储着海量数据，要求给出A，B中重复的数据 
一般思路：

1. 遍历A中的所有数据，通过hash映射将他们分布存储在n个小文件中，记为{a1,a2,…,an}；
2. 遍历B中的所有数据，通过hash映射将他们分布存储在n个小文件中，记为{b1,b2,…,bn}；
3. 根据hash函数性质可知，A和B中的相同数据一定被映射到序号相同的小文件，所以我们依次比较{ai,bi}即可；
4. 如果问题更进一步，要求返回重复次数最多的k条数据，则可以将对比小文件找到的数据存入hash表，键为数据，值为该数据出现的次数。再用大小为k的堆，排序找出即可。


#### 如何在1亿个数中找出最大的100个数
a. 如果这1亿个书里面有很多重复的数，先通过Hash法，把这1亿个数字去重复，这样如果重复率很高的话，会减少很大的内存用量，从而缩小运算空间。
b. 将1亿个数据分成100份，每份100万个数据，
c. 统计每份文件中数据出现的kv值，找到每份数据中最大的100个。
d. 堆/快速排序归并获取最大的100个数



#### 1兆能存下100万个值在1千万～9千万的数吗
算下简单存储：10000000~90000000，最近的 2^27=134217728，假如用Int 4字节32位存储，结果是100w*4=400w字节，400w/1024/1024=3.8147兆

很明显存不下。

那有没有别的方式能够存储呢，可以参考es倒排索引，倒排链：保存了每个term对应的docId的列表，采用skipList的结构保存，用于快速跳跃。

压缩算法bitmap可以存下1兆内，比如0～8区间的数，1个字节8位就足够了，100w/1024/1024=0.9537兆

bitmap是使用bit位来存储数据的一种结构,当数据有明确的上下界时,我们可以转换到bitmap去存储,比如0～8区间的数,如果使用int来存,则需要耗费4字节32位大小,如果使用位来存,只需要花费1个字节大小,相差32倍,在大数据量的情况下,比较节约空间,而且索引效率高
bitmap的缺点也很明显，首先，当数据比较稀疏时，bitmap显然比较浪费空间，如果要存储整个int32的数据，则需要512MB的空间大小，其次，无法对重复数据进行排序和查找。

为了解决bitmap在稀疏数据集下浪费空间的问题，出现了几种改进算法：
- RLE bitmap：
  run-length encoding（RLE）编码，其大致思想是对于bitmap中重复的值（很多个0或者很多个1），采用值加上重复出现的次数表示，从而起到压缩的目的。这种算法包括Oracle’s BBC、WAH、EWAH、 Concise等。我们从WAH开始，介绍一下压缩思路的改进
  WAH（Word Aligned Hybrid）是在Fastbit项目中提出的，这个算法的核心在于两点：Word Aligned和Hybrid，其中，Word Aligned利用现代CPU特性：操作Word比Byte效率高，所以首先会以Word为单元对bitmap数组进行分割，而Hybrid思想是对一个Word的数据格式进行分类处理。
- Roaring_bitmap
  当插入或者查询时，必须decompress之后才能check
  某些情况下，业务需要对两个bimap求位运算，这时不能跳过大部分全0或者全1的区间。
  而Roaring算法提供了另外的思路来处理压缩的问题，其思路为：

  对于32位的整数，每2^16个数分成一组，称为一个容器
  一个容器如果包含的数小于4096个，认为其比较稀疏，用有序数组容器(ArrayContainer)进行保存。
  对于元素个数大于4096的容器，认为是一个密集数据块，使用2^16bit的bitmap进行保存。
  每个 Roaring 容器都会维护一个计数器用以存储该容器的基数，从而能够通过计数器求和快速获得一个Roaring Bitmap的基数大小。容器计数器的存在，还能够快速确定一个整型所属的容器，以及支持 bitmap
  某个位置值的高效查询。



## 算法

### 二叉树的最大直径

### 递归和非递归前序遍历二叉树

### 用1小时的绳子烧出来1小时15分钟

### 统计小岛个数 DFS

### 能看见楼的数量，高的楼会挡住矮的
比如[5,3,8,3,2,5]就是[3,3,5,4,4,4].8可以看到5,3,8,3,5, 2被挡住了







