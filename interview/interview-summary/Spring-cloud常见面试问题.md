# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Spring-cloud常见面试问题

### SpringCloud和SpringCloudAlibaba的区别
SpringCloudAlibaba实际上对我们的SpringCloud2.x和1.x实现拓展组件功能.
1.nacos 分布式配置中心+分布式注册中心=Eureka+config
2.目的是为了推广阿里的产品，如果使用了SpringCloudAlibaba,最好使用alibaba整个体系产品

||SpringCloud|	SpringCloudAlibaba|
|---|---|---|
|注册中心|	Eureka|	nacos|
|消息中间件|	无(第三方替代方案：rabbitmq)|	RecketMQ|
|分布式事务解决方案|	无(第三方替代方案：2pc)|	Seata|
|分布式调度服务|	无(第三方替代方案：xxl-job)|	Alibaba Cloud SchedulerX|
|短信平台|	无|	Alibaba Cloud  SMS|
|分布式配置中心|	SpringCloudConfig|	nacos|
|熔断降级|	Hystrix|	Sentinel|

### Spring Boot有哪些优点？
一句话概括：spring boot来简化spring应用开发，约定大于配置，去繁从简，just run就能创建一个独立的，产品级别的应用
答：
-快速创建独立运行的spring项目与主流框架集成
-使用嵌入式的tomcat容器，应用无需打包成war包
-创建独立的spring引用程序 main方法运行
-自动配置spring添加对应功能starters自动依赖与版本控制，简化maven配置
-大量的自动配置，简化开发，也可修改默认值
-准生产环境的运行应用监控
-与云计算的天然集成

### springboot自动配置的原理
在spring程序main方法中 注解@SpringBootApplication或者@EnableAutoConfiguration

在启动时扫描项目所依赖的每个starter中寻找包含spring.factories文件的JAR
根据spring.factories配置加载AutoConfigure类
根据 @Conditional注解的条件，进行自动配置并将Bean注入Spring Context
如何重新加载Spring Boot上的更改，而无需重新启动服务器？
DevTools模块完全满足开发人员的需求。该模块将在生产环境中被禁用。它还提供H2数据库控制台以更好地测试应用程序。

添加【修改代码】自动重启功能
添加开发者工具集=====spring-boot-devtools
~~~~
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
~~~~

### Spring Boot中的监视器是什么？
Spring boot actuator是spring启动框架中的重要功能之一。Spring boot监视器可帮助您访问生产环境中正在运行的应用程序的当前状态。
有几个指标必须在生产环境中进行检查和监控。即使一些外部应用程序可能正在使用这些服务来向相关人员触发警报消息。监视器模块公开了一组可直接作为HTTP URL访问的REST端点来检查状态。

### 什么是YAML？
YAML是一种人类可读的数据序列化语言。它通常用于配置文件。
与属性文件相比，如果我们想要在配置文件中添加复杂的属性，YAML文件就更加结构化，而且更少混淆。可以看出YAML具有分层配置数据。

### springboot常用的starter有哪些
spring-boot-starter-web 嵌入tomcat和web开发需要servlet与jsp支持
spring-boot-starter-data-jpa 数据库支持
spring-boot-starter-data-redis redis数据库支持
spring-boot-starter-data-solr solr支持
mybatis-spring-boot-starter 第三方的mybatis集成starter

### springcloud如何实现服务的注册和发现
服务在发布时 指定对应的服务名（服务名包括了IP地址和端口） 将服务注册到注册中心（eureka或者zookeeper）
这一过程是springcloud自动实现 只需要在main方法添加@EnableDisscoveryClient 同一个服务修改端口就可以启动多个实例

调用方法：传递服务名称通过注册中心获取所有的可用实例 通过负载均衡策略调用（ribbon和feign）对应的服务

### 除了eureka和zookeeper注册中心还知道其他服务注册中心么？服务注册是如何实现的？
1.ZooKeeper保证的是CP,Eureka保证的是AP
ZooKeeper在选举期间注册服务瘫痪,虽然服务最终会恢复,但是选举期间不可用的
Eureka各个节点是平等关系,只要有一台Eureka就可以保证服务可用,而查询到的数据并不是最新的

自我保护机制会导致
Eureka不再从注册列表移除因长时间没收到心跳而应该过期的服务
Eureka仍然能够接受新服务的注册和查询请求,但是不会被同步到其他节点(高可用)
当网络稳定时,当前实例新的注册信息会被同步到其他节点中(最终一致性)
Eureka可以很好的应对因网络故障导致部分节点失去联系的情况,而不会像ZooKeeper一样使得整个注册系统瘫痪

2.ZooKeeper有Leader和Follower角色,Eureka各个节点平等
3.ZooKeeper采用过半数存活原则,Eureka采用自我保护机制解决分区问题
4.Eureka本质上是一个工程,而ZooKeeper只是一个进程

### ribbon和feign区别
Ribbon添加maven依赖 spring-starter-ribbon 使用@RibbonClient(value=“服务名称”) 使用RestTemplate调用远程服务对应的方法
feign添加maven依赖 spring-starter-feign 服务提供方提供对外接口 调用方使用 在接口上使用@FeignClient(“指定服务名”)

Ribbon和Feign的区别：

    Ribbon和Feign都是用于调用其他服务的，不过方式不同。

    1.启动类使用的注解不同，Ribbon用的是@RibbonClient，Feign用的是@EnableFeignClients。
    2.服务的指定位置不同，Ribbon是在@RibbonClient注解上声明，Feign则是在定义抽象方法的接口中使用@FeignClient声明。
    3.调用方式不同，
Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐。
Feign则是在Ribbon的基础上进行了一次改进，采用接口的方式，将需要调用的其他服务的方法定义成抽象方法即可，
不需要自己构建http请求。不过要注意的是抽象方法的注解、方法签名要和提供服务的方法完全一致。


### 什么是服务熔断?什么是服务降级？二者有什么区别
当下游服务因访问压力过大而响应变慢或失败，上游服务为了保护系统整体的可用性，可以暂时切断对下游服务的调用。这种牺牲局部，保全整体的措施就叫做熔断

服务降级则相对于整体应用完全可用状态而言的，因为部分不可用从保全大部分服务可用的这种服务状态成为服务降级

熔断在consumer，降级在provider。
熔断降级基本是一体的。熔断了就是不再调用有问题下游，这个一般是根据触发条件自动执行的。降级就是用其他方式代替这个下游，熔断后可以直接采用降级方案代替。

### springcloud断路器 Hystrix的作用和状态
当一个服务调用另一个服务由于网络原因或者自身原因出现问题时 调用者就会等待被调用者的响应 当更多的服务请求到这些资源时
导致更多的请求等待 这样就会发生连锁效应（雪崩效应） 断路器就是解决这一问题

断路器有完全打开状态
一定时间内 达到一定的次数无法调用 并且多次检测没有恢复的迹象 断路器完全打开，那么下次请求就不会请求到该服务
半开
短时间内 有恢复迹象 断路器会将部分请求发给该服务 当能正常调用时 断路器关闭
关闭
当服务一直处于正常状态 能正常调用 断路器关闭
Hystrix 实现原理
在执行的时候 Hystrix 会解析 Command 的隔离规则来创建 RxJava Scheduler 并在其上调度执行，若是线程池模式则 Scheduler 底层的线程池为配置的线程池，若是信号量模式则简单包装成当前线程执行的 Scheduler
Hystrix 的 Command 强依赖于隔离规则配置的原因是隔离规则会直接影响 Command 的执行。

重试：重试次数、重试间隔、重试条件、回调Listener

### 还知道其他断路器组件么?Sentinel 与 Hystrix 的对比
在这里插入图片描述
Sentinel 是阿里中间件团队研发的面向分布式服务架构的轻量级高可用流量控制组件，最近正式开源。Sentinel 主要以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度来帮助用户保护服务的稳定性。

### 资源模型和执行模型上的对比
Hystrix 的资源模型设计上采用了命令模式，
Sentinel 的资源定义与规则配置的耦合度更低：用户先通过 Sentinel API 给对应的业务逻辑定义资源（埋点），然后可以在需要的时候配置规则。
埋点方式有两种：
try-catch 方式（通过 SphU.entry(…)），用户在 catch 块中执行异常处理 / fallback
if-else 方式（通过 SphO.entry(…)），当返回 false 时执行异常处理 / fallback


### Java微服务的框架有 dubbo（只能用来做微服务），spring cloud（提供了服务的发现，断路器等）

传统开发模式和微服务的区别
优点：
①开发简单，集中式管理
②基本不会重复开发
③功能都在本地，没有分布式的管理和调用消耗

缺点：
1、效率低：开发都在同一个项目改代码，相互等待，冲突不断
2、维护难：代码功功能耦合在一起，新人不知道何从下手
3、不灵活：构建时间长，任何小修改都要重构整个项目，耗时
4、稳定性差：一个微小的问题，都可能导致整个应用挂掉
5、扩展性不够：无法满足高并发下的业务需求

常见的系统架构遵循的三个标准和业务驱动力：
1、提高敏捷性：及时响应业务需求，促进企业发展
2、提升用户体验：提升用户体验，减少用户流失
3、降低成本：降低增加产品、客户或业务方案的成本

基于微服务架构的设计：
目的：有效的拆分应用，实现敏捷开发和部署

### 微服务的具体特征
官方的定义：
1、一些列的独立的服务共同组成系统
2、单独部署，跑在自己的进程中
3、每个服务为独立的业务开发
4、分布式管理
5、非常强调隔离性

大概的标准：
1、分布式服务组成的系统
2、按照业务，而不是技术来划分组织
3、做有生命的产品而不是项目
4、强服务个体和弱通信（ Smart endpoints and dumb pipes ）
5、自动化运维（ DevOps ）
6、高度容错性
7、快速演化和迭代

### SOA和微服务的区别
1、SOA喜欢重用，微服务喜欢重写
SOA的主要目的是为了企业各个系统更加容易地融合在一起。 说到SOA不得不说ESB(EnterpriseService Bus)。 ESB是什么? 各服务间可能有
复杂的依赖关系
微服务通常由重写一个模块开始。要把整个巨石型的应用重写是有很大的风险的，也不一定必要。我们向微服务迁移的时候通常从耦合度最低的模块或对扩展性要求最高的模块开始，
把它们一个一个剥离出来用敏捷地重写，可以尝试最新的技术和语言和框架，然 后单独布署。

2、SOA喜欢水平服务，微服务喜欢垂直服务
3、SOA喜欢自上而下，微服务喜欢自下而上

###  怎么具体实践微服务
要实际的应用微服务，需要解决一下四点问题：
1、客户端如何访问这些服务
2、每个服务之间如何通信
3、如此多的服务，如何实现？

4、服务挂了，如何解决？（备份方案，应急处理机制）
①重试机制
②限流：限流就是限制系统的输入和输出流量已达到保护系统的目的。一般来说系统的吞吐量是可以被测算的，为了保证系统的稳定运行，一旦达到的需要限制的阈值，就需要限制流量并采取一些措施以完成限制流量的目的。比如：延迟处理，拒绝处理，或者部分拒绝处理等等。
③熔断机制
④负载均衡
⑤降级（本地缓存）：当服务器压力剧增的情况下，根据当前业务情况及流量对一些服务和页面有策略的降级，以此释放服务器资源以保证核心任务的正常运行

常见的限流算法有：计数器、漏桶和令牌桶算法。参考：https://www.cnblogs.com/haoxinyue/p/6792309.html

### 六种常见的微服务架构设计模式：
1、聚合器微服务设计模式
2、代理微服务设计模式
3、链式微服务设计模式
4、分支微服务设计模式
5、数据共享微服务设计模式
6、异步消息传递微服务设计模式

### RPC是什么
例：rpc远程过程调用
常见的语境是利用HTTP协议作为调用控制协议,XML 和 JSON 作为对象序列化之后的格式。
通讯协议有哪些：http，dubbo，thrift协议
数据格式：json，xml
所以搭配起来就是：http+json、tcp  +.probuff等

### 如何实现解耦合
普通java类解耦合：A类不应该由B类创建，例如spring的依赖注入(IOC)
具体解耦合操作：1.接口代替继承，2.set方法，3.构造器注入，4.java注解
服务解耦合：异步且基于消息传递：JMS，AMQP
拓展：
服务同步：http，thrift（如果A依赖B提供的API，如果B提供的服务不可用将直接影响到A不可用）
同步模式：客户端请求需要服务端即时响应，甚至可能由于等待而阻塞。
异步模式：客户端请求不会阻塞进程，服务端的响应可以是非即时的。
线程间通信用queue，比如BlockingQueue，进程间通信用mq

### 如何最大限度的降低线上故障的影响
项目有没有发布，发布了，先回滚！没发布，有没有降级（服务），先降级！没有降级，重启！然后拉日志慢慢定位分析问题。
服务降级手段（dubbo/Hystrix）：1.拒绝服务（拒绝低优先级或随机拒绝）  2.关闭服务（关闭冷门或边缘服务）

### 高并发项目如何保护系统
高并发系统时有三把利器用来保护系统：缓存、降级和限流（漏桶，令牌桶）

### 优先级别p1,p2,p3,p4分别与以上等级对应。 
P1:对产品影响非常大，找出产品无法移交 
P2:对产品影响比较大，如果发布给用户将会产生麻烦 
P3:对产品影响一般，如果bug被解决，产品会更好 
P4:对产品影响较小，其他bug解决后，在解决该类bug

### 响应式编程和异步回调
响应式编程（RxJava）比传统的异步回调（代码混乱，api存在并行和先后，回调难以控制）
响应式编程一般采用观察者模式，如RxJava，mvvm，及时响应数据或视图的变化
异步回调：正如线程中的,IO和NIO区别一样。简单点就是A持有B类的对象，A类完成后调用B类方法


