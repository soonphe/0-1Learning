# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Micrometer监控器指标

### Micrometer
官网文档：https://micrometer.io/docs
Micrometer为最流行的监控系统提供了一个简单的仪表客户端外观，允许仪表化JVM应用，而无需关心是哪个供应商提供的指标。它的作用和SLF4J类似，只不过它关注的不是Logging（日志），而是application metrics（应用指标）。简而言之，它就是应用监控界的SLF4J。

1.依赖
Micrometer记录的应用程序指标用于观察、告警和对环境当前/最近的操作状态做出反应。

为了使用Micrometer，首先要添加你所选择的监视系统的依赖。以Prometheus为例：
```
<dependency>
 	<groupId>io.micrometer</groupId>
   	<artifactId>micrometer-registry-prometheus</artifactId>
   	<version>${micrometer.version}</version>
</dependency>
```
2.1.  Registry

Meter是收集关于你的应用的一系列指标的接口。Meter是由MeterRegistry创建的。每个支持的监控系统都必须实现MeterRegistry。

Micrometer中包含一个SimpleMeterRegistry，它在内存中维护每个meter的最新值，并且不将数据导出到任何地方。如果你还没有一个首选的监测系统，你可以先用SimpleMeterRegistry：
```
MeterRegistry registry = new SimpleMeterRegistry();
注意：如果你用Spring的话，SimpleMeterRegistry是自动注入的
@Autowired
private MeterRegistry meterRegistry;
```

2.2.  Meters

Micrometer提供一系列原生的Meter，包括Timer , Counter , Gauge , DistributionSummary , LongTaskTimer , FunctionCounter , FunctionTimer , TimeGauge。不同的meter类型导致有不同的时间序列指标值。例如，单个指标值用Gauge表示，计时事件的次数和总时间用Timer表示。

每一项指标都有一个唯一标识的名字和维度。“维度”和“标签”是一个意思，Micrometer中有一个Tag接口，仅仅因为它更简短。一般来说，应该尽可能地使用名称作为轴心。

（PS：指标的名字很好理解，维度怎么理解呢？如果把name想象成横坐标的话，那么dimension就是纵坐标。Tag是一个key/value对，代表指标的一个维度值）

2.3.  Naming meters（指标命名）

Micrometer使用了一种命名约定，用.分隔小写单词字符。不同的监控系统有不同的命名约定。每个Micrometer的实现都要负责将Micrometer这种以.分隔的小写字符命名转换成对应监控系统推荐的命名。你可以提供一个自己的NamingConvention来覆盖默认的命名转换：
```
registry.config().namingConvention(myCustomNamingConvention);
```
有了命名约定以后，下面这个timer在不同的监控系统中看起来就是这样的：
```
registry.timer("http.server.requests"); 
//在Prometheus中，它是http_server_requests_duration_seconds
```
2.3.1.  Tag naming

假设，我们想要统计HTTP请求数和数据库调用次数，那么可以这样写：
```
registry.counter("database.calls", "db", "users");       // 数据库调用次数
registry.counter("http.requests", "uri", "/api/users");  // HTTP请求数

//第一个参数是name，后面的是tags
public Timer timer(String name, String... tags) {
public Counter counter(String name, String... tags) {
```


2.3.2.  Common tags

Common tags可以被定义在registry级别，并且会被添加到每个监控系统的报告中

预定义的Tags有host , instance , region , stack等
```
registry.config().commonTags("stack", "prod", "region", "us-east-1");
registry.config().commonTags(Arrays.asList(Tag.of("stack", "prod"), Tag.of("region", "us-east-1"))); // 二者等价
```
2.3.4.  Tag values

Tag values must be non-null
标签值必须非空

2.5.  Counters（计数器）

Counter接口允许以固定的数值递增，该数值必须为正数。
```
MeterRegistry registry = new SimpleMeterRegistry();

 //  写法一
 Counter counter = registry.counter("counter");

 //  写法二
 Counter counter = Counter
     .builder("counter")
     .baseUnit("beans") // optional
     .description("a description of what this counter does") // optional
     .tags("region", "test") // optional
     .register(registry);
```

2.5.1.  Function-tracking counters

跟踪单调递增函数的计数器
```
1 Cache cache = ...; // suppose we have a Guava cache with stats recording on
2 registry.more().counter("evictions", tags, cache, c -> c.stats().evictionCount()); // evictionCount()是一个单调递增函数，用于记录缓存被剔除的次数
```

2.6.  Gauges

gauge是获取当前值的句柄。典型的例子是，获取集合、map、或运行中的线程数等。

MeterRegistry接口包含了用于构建gauges的方法，用于观察数字值、函数、集合和map。
```
1 List<String> list = registry.gauge("listGauge", Collections.emptyList(), new ArrayList<>(), List::size); //监视非数值对象
2 List<String> list2 = registry.gaugeCollectionSize("listSize2", Tags.empty(), new ArrayList<>()); //监视集合大小
3 Map<String, Integer> map = registry.gaugeMapSize("mapGauge", Tags.empty(), new HashMap<>());
```
还可以手动加减Gauge
```
1 AtomicInteger n = registry.gauge("numberGauge", new AtomicInteger(0));
2 n.set(1);
3 n.set(2);
```

2.7.  Timers（计时器）

Timer用于测量短时间延迟和此类事件的频率。所有Timer实现至少将总时间和事件次数报告为单独的时间序列。

例如，可以考虑用一个图表来显示一个典型的web服务器的请求延迟情况。服务器可以快速响应许多请求，因此定时器每秒将更新很多次。
```
1 // 方式一
2 public interface Timer extends Meter {
3     ...
4     void record(long amount, TimeUnit unit);
5     void record(Duration duration);
6     double totalTime(TimeUnit unit);
7 }
8
9 // 方式二
10 Timer timer = Timer
11     .builder("my.timer")
12     .description("a description of what this timer does") // optional
13     .tags("region", "test") // optional
14     .register(registry);
```

实际使用
```
//监控调用第三方接口时间的开始
Timer.Sample sample = Timer.start(meterRegistry);
//监控调用第三方接口时间的结束
sample.stop(meterRegistry.timer("online_green_certificate_service_timer", "green_certificate_service_success", "online_green_certificate_service"));
//计数
meterRegistry
	.counter("online_show_userinfo_counter", "show_userinfo_fail", "online_show_userinfo")
	.increment();
```