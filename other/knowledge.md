# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## knowledge(知识点整理)

### Mapstruct：
```
实体属性相同、名称不同转换：
@Mappings({ @Mapping(source="grade", target="level") })
属性、常量转换：
@Mapping(target="ordType",constant="3")
```
基本属性与枚举互相转换：
```
自定义CustomMapper转换类，并在mapper接口中引用
@Mapper(uses={DateMapper.class,CustomMapper.class})
public interface OverTimeOrderMapper {
    ...
}

//自定义mapper转换类
public class CustomMapper {
    // 枚举转基础字段
    public Integer asOrderStateInteger(OverTimeOrderState state){
        return state.getCode();
    }
    // 基础字段转枚举
    public OverTimeOrderState asEnumState(Integer state){
        for(OverTimeOrderStateoverTimeOrderState:OverTimeOrderState.values()){
            if(overTimeOrderState.getCode().equals(state)){
                return overTimeOrderState;
            }
        }
    }
}
```



### P6Spy集成：
文档相关：https://p6spy.readthedocs.io/en/latest/install.html
1.maven依赖：
```
<dependency>
<groupId>p6spy</groupId>
<artifactId>p6spy</artifactId>
<version>3.9.1</version>
</dependency>
```
2.新增配置spy.prroperties
配置文件添加JDBC驱动
```
driverlist=com.mysql.jdbc.Driver
。。。
```
3.修改properties中数据源配置：
```
driver-class-name:com.p6spy.engine.spy.P6SpyDriver
```

### QueryDSL集成：
Querydsl 是一个框架，它可以为多个后端（包括 JPA、MongoDB 和 Java 中的 SQL）构建类型安全的 SQL 类查询。
文档：https://hub.fastgit.org/querydsl/querydsl
http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html#jpa_integration
查询类型：
QCustomer customer = QCustomer.customer;
QCustomer customer = new QCustomer("myCustomer");
查询：
QCustomer customer = QCustomer.customer;
Customer bob = queryFactory.selectFrom(customer)
  .where(customer.firstName.eq("Bob"))
  .fetchOne();

实际使用：
@Generated("com.querydsl.codegen.EntitySerializer")
Public class QOverTimeFeeModel extends EntityPathBase<OverTimeFeeModel>{
	Public static final QOverTimeFeeModel overTimeFeeModel = new QOverTimeFeeModel("overTimeFeeModel");

QOverTimeFeeModel qOverTimeFeeModel=QOverTimeFeeModel.overTimeFeeModel;

Return jpaQueryFactory
.select(Projections.fields(OverTimeFeeModelStationVo.class,
	qOverTimeFeeModel.id.as("id"),qOverTimeStation.stationNo,qOverTimeStation.stationName,
	qOverTimeStation.operateState,qOverTimeStation.address,qOverTimeFeeModel.validStatus,
	qOverTimeFeeModel.feeRuleList))
.from(qOverTimeFeeModel)
.leftJoin(qStationFeeModel).on(qOverTimeFeeModel.id.eq(qStationFeeModel.feeModelId))
.leftJoin(qOverTimeStation)
.on(qOverTimeStation.stationNo.eq(qStationFeeModel.stationNo))
.where(predicate)
.offset((long)(query.getPage()-1)*query.getPageSize())
.limit(query.getPageSize())
.fetchResults();








### 解决“Jenkins 主机密钥验证失败”
1. ssh-keygen命令生成公钥私钥
2.去--> cat /var/lib/jenkins/.ssh/id_rsa.pub 从 id_rsa.pub 复制密钥（也可以使用scp命令）
3.文本复制
ssh登录目标服务器：ssh -l root 192.168.161.168
touch /root/.ssh/authorized_keys 
vi .ssh/authorized_keys  粘贴复制的密钥

4.scp复制
scp复制公钥：scp /root/.ssh/id_rsa.pub root@192.168.1.181:/root/.ssh/authorized_keys
由于还没有免密码登录的，所以要输入一次B机的root密码。

5.修改权限
需要特别注意的是：B主机的.ssh文件的所有者要是root，如果不是要改：
chown -R root:root .ssh
同时，B主机的authorized_keys文件，要是600权限的，如果不是，也要改：
chmod 600 authorized_keys
如果执行权限不够报错：-bash: ./startup.sh: 权限不够
使用：chmod u+x *

6.手动登录mercurial服务器
注意：请手动登录，否则 jenkins 会再次报错“主机验证失败”
ssh -l root 192.168.1.181


### mac安装Mysql启动报错（系统为BigSur版本）：
mysql启动：
sudo /usr/local/mysql/support-files/mysql.server start
报错可能是没有权限
sudo chmod -R a+rwx /usr/local/mysql/data/



### 在线接口数据示例：
参考：http://omdbapi.com/
搜索列表：http://omdbapi.com/?apikey=682d8365&s=Longest
详情：http://omdbapi.com/?apikey=682d8365&i=tt2726560




### spring StateMachine状态机：
1 引入依赖
```
<dependency>
<groupId>org.springframework.statemachine</groupId>
<artifactId>spring-statemachine-core</artifactId>
<version>2.0.1.RELEASE</version>
</dependency>
```
2 创建订单状态枚举类和状态转换枚举类
```
/**
* 订单状态
*/
public enum OrderStatus {
// 待支付，待发货，待收货，订单结束
WAIT_PAYMENT, WAIT_DELIVER, WAIT_RECEIVE, FINISH;
}
```
3 添加配置

4 添加订单状态监听器/拦截器


### 阿里云ONS接入：
参考文档：https://help.aliyun.com/document_detail/44711.html?spm=a2c4g.11186623.6.599.372a35e8LQkvOK
1.引入依赖
```
<dependency>
<groupId>com.aliyun.openservices</groupId>
<artifactId>ons-client</artifactId>
<version>1.8.4.Final</version>
</dependency>
```
2.MQ配置
```
mq:
  rocketmq:
	v2-access-key:62f49160c57b4f158a8ab3ebd2ff66cc
	v2-secret-key:J8H3v+Go55DIimy6gbKu3Bbnc0U=
	ons-addr:http://172.169.101.121:8080/rocketmq/nsaddr4broker-internal
	v2enabled:true
	productId:PID_order_dev
```
配置文件：
```
public static final String TOPIC = "您刚创建的Topic";
public static final String GROUP_ID = "您刚创建的Group ID";
public static final String ORDER_TOPIC = "您刚创建的用于收发顺序消息的Topic";
public static final String ORDER_GROUP_ID = "您刚创建的用于收发顺序消息的Group ID";
public static final String ACCESS_KEY = "您的阿里云账号的AccessKey ID";
public static final String SECRET_KEY = "您的阿里云账号的AccessKey Secret";
public static final String TAG = "您自定义的消息Tag属性";
public static final String NAMESRV_ADDR = "您刚创建的消息队列RocketMQ版实例的TCP接入点，可在消息队列RocketMQ版控制台的实例详情页面获取TCP协议客户端接入点";     
```
3.发送消息
说明：
1.发送/接收 普通消息
2.发送/接收 事务消息
3.发送/接收 顺序消息
```
@Autowired
privateProducerBeanproducerBean;

MessagesendMessage=newMessage();
OverTimeFeeModelCreateMsgoverTimeOrderBody=getOverTimeFeeModelCreateMsgBody(event.getStationFeeModel());
sendMessage.setBody(JSON.toJSONBytes(overTimeOrderBody));
sendMessage.setTopic(overTimeFeeModelCreatedProperties.getTopic());
sendMessage.setTag(overTimeFeeModelCreatedProperties.getExpressionCreate());
producerBean.send(sendMessage);
```
4.消费消息
```
@Component
@Slf4j
@AllArgsConstructor
Public class ReceiptPayOverTimeOrderListener implements MessageListener{
  
  //实现consume方法
  @Override
  public Action consume(Message message, ConsumeContext consumeContext) {
    log.info("===监听支付成功mq消息  订单状态修改成功!");
    return Action.CommitMessage;
  }
}


@Configuration
@RequiredArgsConstructor
@Profile(value = {"prod"})
@EnableConfigurationProperties(value = {ReceiptPayOrderProperties.class, DrawTheGunConsumerProperties.class})
public class OverTimeConsumerV3 {
  //自动注入的mq配置
  private final MqProperties mqProperties;
  //mq监听器
  private final ReceiptPayOverTimeOrderListener receiptPayOverTimeOrderListener;

  //绑定订阅和监听
  @Bean(initMethod = "start", destroyMethod = "shutdown")
  public ConsumerBean buildOrderSyncConsumer() {
    ConsumerBean consumerBean = new ConsumerBean();
    Properties properties = getMqProperties();
    properties.setProperty(PropertyKeyConst.GROUP_ID, mqProperties.getGroupId());
    properties.setProperty(PropertyKeyConst.ConsumerId, mqProperties.getGroupId());
    consumerBean.setProperties(properties);
    Map<Subscription, MessageListener> subscriptionTable = new HashMap<>(16);
    Subscription subscription = new Subscription();
    subscription.setTopic(receiptPayOrderProperties.getTopic());
    subscription.setExpression(receiptPayOrderProperties.getExpression());
    subscriptionTable.put(subscription, receiptPayOverTimeOrderListener);
    consumerBean.setSubscriptionTable(subscriptionTable);
    return consumerBean;
  }
```

### openresty搭建高性能web应用、网关：
官网地址：http://openresty.org/cn/


### soul网关（shenyu）：
dromara官网地址：https://dromara.org/
soul官网地址：https://github.com/apache/incubator-shenyu
官网地址：https://shenyu.apache.org/

   





### 责任链实现：

虚拟类AbstractOrderFilter判断责任链Seletor处理（doFilter，handle）
DefaultFilterChain责任链判断实现

1.FilterChain责任链接口
```
//OrderFilter作为业务处理
public interface OrderFilter<T extends OrderContext> {
  void doFilter(T var1, OrderFilterChain var2);
}

//OrderFilterChain责任链式处理
public interface OrderFilterChain<T extends OrderContext> {
  void handle(T var1);

  void fireNext(T var1);
}
```
2.实现责任链接口
```
public class DefaultFilterChain<T extends OrderContext> implements OrderFilterChain<T> {
  //下一个filterChain引用
  private OrderFilterChain<T> next;
  //filter业务处理
  private OrderFilter<T> filter;

  public DefaultFilterChain(OrderFilterChain chain, OrderFilter filter) {
    this.next = chain;
    this.filter = filter;
  }

  public void handle(T context) {
    this.filter.doFilter(context, this);
  }

  public void fireNext(T ctx) {
    OrderFilterChain nextChain = this.next;
    if (Objects.nonNull(nextChain)) {
      nextChain.handle(ctx);
    }

  }
}
```
3.责任链初始化
```
//第一种形式使用Pipline链式添加
public class FilterChainPipeline<T extends OrderFilter> {
  private DefaultFilterChain last;

  public FilterChainPipeline() {
  }

  public DefaultFilterChain getFilterChain() {
    return this.last;
  }

  public FilterChainPipeline addFilter(T filter) {
    DefaultFilterChain newChain = new DefaultFilterChain(this.last, filter);
    this.last = newChain;
    return this;
  }

  public FilterChainPipeline addFilter(String desc, T filter) {
    DefaultFilterChain newChain = new DefaultFilterChain(this.last, filter);
    this.last = newChain;
    return this;
  }
}
//添加引用pipline
FilterChainPipeline pipeline = new FilterChainPipeline();
pipeline.addFilter("创建订单附表", orderExtensionAuthFilter(syncService, meterRegistry))
	.addFilter...
return new IotCuoHePreAuthProvider(pipeline.getFilterChain(), iotCuoHeAuthReqConverter,
	producerServer, redisServer);

//第二种形式
//1.直接使用filterChain链式添加
FilterChain filterChain7=new DefaultFilterChain(null, orderExtensionAuthFilter);
FilterChain filterChain6=new DefaultFilterChain(filterChain7, orderExtensionAuthFilter);
return new IotCuoHePreAuthProvider(filterChain1, iotCuoHeAuthReqConverter, producerServer, redisServer);

```
4. 添加seletor选择执行责任链
```
//Filter选择器
public interface FilterSelector {
  //匹配Filter
  boolean matchFilter(String var1);

  List<String> getFilterNames();
}

//Filter选择器实现
public class LocalListBasedFilterSelector implements FilterSelector {
  private List<String> filterNames = Lists.newArrayList();

  public LocalListBasedFilterSelector() {
  }

  public boolean matchFilter(String classSimpleName) {
    return this.filterNames.stream().anyMatch((s) -> {
      return Objects.equals(s, classSimpleName);
    });
  }

  public List<String> getFilterNames() {
    return this.filterNames;
  }

  public void addFilter(String clsNames) {
    this.filterNames.add(clsNames);
  }
}

//实际业务使用
public class FeeItemDomainService {

  private final FilterChainPipeline orderFilterChainPipeline;

  public FeeItemDomainService(
      FilterChainPipeline orderFilterChainPipeline) {
    this.orderFilterChainPipeline = orderFilterChainPipeline;
  }

  /**
   * 构造context对象
   * 
   * @param request
   * @return
   */
  public FeeItemResult getFeeItemResult(OrderCreateRequest request){
    CalculateContext calculateContext = new CalculateContext(BizEnum.OVER_TIME_ORDER,feeFilter(),request);
    orderFilterChainPipeline.getFilterChain().handle(calculateContext);
    return calculateContext.getFeeItemResult();
  }

  /**
   * 构造filter选择器
   *
   * @param request
   * @return
   */
  private LocalListBasedFilterSelector feeFilter(){
    LocalListBasedFilterSelector selector = new LocalListBasedFilterSelector();
    selector.addFilter(OverTimeFeeItemCalculateFilter.class.getSimpleName());
    selector.addFilter(RequestSaveFilter.class.getSimpleName());
    return selector;
  }

}
```
5. context对象
```
//通用Context对象
public interface OrderContext {
  BizEnum getBizCode();

  FilterSelector getFilterSelector();

  List<String> getDynamicCloseFilters();
}

public abstract class AbstractOrderContext implements OrderContext {
  private final BizEnum bizEnum;
  private final FilterSelector selector;

  public AbstractOrderContext(BizEnum bizEnum, FilterSelector selector) {
    this.bizEnum = bizEnum;
    this.selector = selector;
  }

  public BizEnum getBizCode() {
    return this.bizEnum;
  }

  public FilterSelector getFilterSelector() {
    return this.selector;
  }
}

```


### underscore和jquery区别
underscore和jquery（jquey主要用于页面交互，封装了很多操作dom的方法，以及ajax，
underscore则可以理解为一个js的函数库，其中主要封装了一些常用的js方法）

### OkHttp基础请求方式：
```
public static final MediaType JSON = MediaType.get("application/json; charset=utf-8");
OkHttpClient client = new OkHttpClient();
String post(String url, String json) throws IOException {  
    RequestBody body = RequestBody.create(JSON, json);  
    Request request = new Request.Builder()      
                        .url(url)      
                        .post(body)      
                        .build();  
    try (Response response = client.newCall(request).execute()) {    
        return response.body().string();  
    }
}
```







### IDEA、WebStorm项目无法被识别为Git项目
VCS - Enable Version Control Intergration






### Vue - 引入本地图片的两种方式
第一种，只引入单个图片，这种引入方法在异步中引入则会报错。 比如需要遍历出很多图片展示时
<image :src = require('图片的路径') />

第二种，可引入多个图片，也可引入单个图片。 vuelic3版本没有static文件夹。可将静态图片存放到public目录下，直接引入即可
<image src="/public/image/logo.png"/>

第三种：使用相对路径形式@/assets/icon/group_4.png
```
<!--    <img class="card-panel-icon" src="@/assets/icon/group_4.png" alt="404" style="width: 48px">-->

    <img class="card-panel-icon" :src=imgSrc(item.icon) alt="404" style="width: 48px">

    imgSrc: function(index) {
      return require('@/assets/icon/' + (index) + '.png')
    },
```


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

### vue-element-admin项目
官网文档：https://panjiachen.github.io/vue-element-admin-site/zh/

### git提交PR
1. fork别人仓库
2. clone自己的仓库到本地，并与两个远程仓库分别建立链接;
3. 将修改提交到自己的远程仓库;
4. 别的仓库中点击Pull Requests，再点击New pull requests按钮;
5. Comparing changes界面，Compare需要注意。
6. 填写相关信息，在点击Create pull request按钮即可。

### timestamp和datetime区别
1. 取值范围 
   - timestamp存储需要四个字节，它的取值范围为“1970-01-01 00:00:01” UTC ~ “2038-01-19 03:14:07” （和时区有关） 
   - datetime取值范围为“1000-01-01 00:00:00” ~ “9999-12-31 23:59:59”（和时区无关，怎么存入怎么返回，对程序员友好）
2. timestamp类型还有一个很大的特殊点，就是时间是根据时区来显示的。
例如，在东八区插入的timestamp类型为2009-09-30 14:21:25，在东七区显示时，时间部门就变成了13:21:25，在东九区显示时，时间部门就变成了15:21:25。
3. 需要显示日期与时间，timestamp类型需要根据不同地区的时区来转换时间，但是，timestamp类型的范围太小，其最大时间为2038-01-19 11:14:07。
如果插入时间的比这个大，将会数据库插入0000-00-00 00:00:00。所以需要的时间范围比较大，还是选择dateTime类型比较安全。


### tinyint和bit
TINYINT是8位整数值，BIT字段可以存储1位BIT（1）和64位BIT（64）之间。对于布尔值，BIT（1）很常见。
说明：
如果确定只有2个状态，可以用bit，否则就用tinyint，以防今后就多个状态的可能。
bit 不能建索引，TINYINT可以建索引。



### logback动态配置日志路径
定义变量
- 在 logback.xml 中定义
```
  <property name="LOGPATH" value="/home/admin/logs" />
```
- 在VM变量中定义
```
  -DLOG_PATH="/Users/luoxiaosheng/logs"
```
- 引入properties/yml文件
```
  <property resource="bootstrap.yml" />
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
  
#配置文件中
LOG_PATH: /Users/luoxiaosheng/logs/
```
- 引入的spring配置变量springProperty️（对比上一种不用引入配置文件，直接引入单个属性就行）
```
  <springProperty scope="context" name="LOG_PATH" source="log.path"></springProperty>
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
  
#配置文件application-*.yml中：
log:
  level: info
  path: /Users/luoxiaosheng/logs/
```

使用变量：${var}
```
<file>/${home}/myApp.log</file>
```
变量的默认值：
在引用一个变量时，如果该变量未定义，需要为其指定默认值，写法是：*${变量名:-默认值}*
```
  <springProperty scop="context" name="springAppName" source="spring.application.name"
    defaultValue=""/>
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
```


### jenkins打包vue 全局工具中找不到node版本
解决办法
去官网找个文件 （文末带百度云共享文件 2018年12月22日10:20:07）

hudson.plugins.nodejs.tools.NodeJSInstaller

这个文件就是nodejs文件版本信息文件

放在jenkins 的目录下 /var/jenkins_home/updates

docker cp ./hudson.plugins.nodejs.tools.NodeJSInstaller jenkins:/var/jenkins_home/updates




### 域名和IP和端口
域名是只能80端口吗
“准确的说,域名什么端口也绑定不了,只能绑定ip地址。你说的80端口是web端口,浏览器访问的默认端口。所以,你用浏览器,浏览器当然只会访问80端口了。
这个不是因为域名解析的问题。web服务器设置的问题。如果80端口已被占用，那就没有办法了。
除非你使用隐藏的域名转发，但是实际还要加 :端口

一台服务器可以被2个域名访问，但一个域名不能同时访问2台服务器。

### java使用okhttp
引入依赖：
```
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>okhttp</artifactId>
  <version>4.9.1</version>
</dependency>
```

工具类：
```
/**
 * okHttp工具类
 *
 * @author soonphe
 * @since 1.0
 */
public class HttpUtil {

    private static final Logger logger = LoggerFactory.getLogger(HttpUtil.class);

    private static OkHttpClient client;
    /**
     * 默认请求json格式
     */
    private static final String DEFAULT_MEDIA_TYPE = "application/json; charset=utf-8";
    /**
     * 连接超时
     */
    private static final int CONNECT_TIMEOUT = 60;
    /**
     * 读取超时
     */
    private static final int READ_TIMEOUT = 100;
    /**
     * 写入超时
     */
    private static final int WRITE_TIMEOUT = 60;

    private static final String GET = "GET";
    private static final String POST = "POST";

    /**
     * 单例模式  获取类实例
     *
     * @return client
     */
    private static OkHttpClient getInstance() {
        if (client == null) {
            synchronized (OkHttpClient.class) {
                if (client == null) {
                    client = new OkHttpClient.Builder()
                            .connectTimeout(CONNECT_TIMEOUT, TimeUnit.SECONDS)
                            .readTimeout(READ_TIMEOUT, TimeUnit.SECONDS)
                            .writeTimeout(WRITE_TIMEOUT, TimeUnit.SECONDS)
                            .connectionPool(new ConnectionPool(32,5,TimeUnit.MINUTES))
                            .build();
                }
            }
        }
        return client;
    }

    /**
     * Get请求
     *
     * @param url
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static String doGet(String url, String callMethod) throws HttpStatusException {

        try {
            long startTime = System.currentTimeMillis();
            addRequestLog(GET, callMethod, url, null, null);

            Request request = new Request.Builder().url(url).build();
            // 创建一个请求
            Response response = getInstance().newCall(request).execute();
            int httpCode = response.code();
            String result;
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new RuntimeException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }

    /**
     * post 请求
     *
     * @param url 请求路径
     * @param postBody body对象
     * @param mediaType 请求类型，默认为application/json
     * @param callMethod 回调方法
     * @return
     * @throws HttpStatusException
     */
    public static String doPost(String url, String postBody, String mediaType, String callMethod) throws HttpStatusException {
        try {
            long startTime = System.currentTimeMillis();
            addRequestLog(POST, callMethod, url, postBody, null);

            MediaType createMediaType = MediaType.parse(mediaType == null ? DEFAULT_MEDIA_TYPE : mediaType);
            Request request = new Request.Builder()
                    .url(url)
                    .post(RequestBody.create(createMediaType, postBody))
                    .build();

            Response response = getInstance().newCall(request).execute();
            int httpCode = response.code();
            String result;
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new IOException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }

    /**
     * Okhttp异步请求
     *
     * @param url
     * @param postBody
     * @param mediaType
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static void doPostAsyc(String url, String postBody, String mediaType, Callback callMethod) throws HttpStatusException {
        try {
            addRequestLog(POST, callMethod.toString(), url, postBody, null);

            MediaType createMediaType = MediaType.parse(mediaType == null ? DEFAULT_MEDIA_TYPE : mediaType);
            Request request = new Request.Builder()
                .url(url)
                .post(RequestBody.create(createMediaType, postBody))
                .build();

            Call call=getInstance().newCall(request);
            call.enqueue(callMethod);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
        }
    }


    /**
     * post请求，form/data数据
     *
     * @param url
     * @param parameterMap
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static String doPost(String url, Map<String, String> parameterMap, String callMethod) throws HttpStatusException {
        try {
            long startTime = System.currentTimeMillis();
            List<String> parameterList = new ArrayList<>();
            FormBody.Builder builder = new FormBody.Builder();
            if (parameterMap.size() > 0) {
                for (Map.Entry<String, String> entry : parameterMap.entrySet()) {
                    String parameterName = entry.getKey();
                    String value = entry.getValue();
                    builder.add(parameterName, value);
                    parameterList.add(parameterName + ":" + value);
                }
            }

            addRequestLog(POST, callMethod, url, null, Arrays.toString(parameterList.toArray()));

            FormBody formBody = builder.build();
            Request request = new Request.Builder()
                    .url(url)
                    .post(formBody)
                    .build();

            Response response = getInstance().newCall(request).execute();
            String result;
            int httpCode = response.code();
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new IOException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);

        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }


    private static void addRequestLog(String method, String callMethod, String url, String body, String formParam) {
        logger.info("===========================request begin================================================");
        logger.info("URI          : {}", url);
        logger.info("Method       : {}", method);
        if (StringUtils.isNotBlank(body)) {
            logger.info("Request body : {}", body);
        }
        if (StringUtils.isNotBlank(formParam)) {
            logger.info("Request param: {}", formParam);
        }
        logger.info("===========================request end================================================");
    }

    private static void addResponseLog(int httpCode, String result, long startTime) {
        logger.info("===========================response begin================================================");
        long endTime = System.currentTimeMillis();
        logger.info("Status       : {}", httpCode);
        logger.info("Response     : {}", result);
        logger.info("Time Cost         : {} ms", endTime - startTime);
        logger.info("===========================response end================================================");
    }

    private static String handleHttpResponse(int httpCode, String result) throws HttpStatusException {
        if (httpCode == HttpStatus.OK.value()) {
            return result;
        }
        HttpStatus httpStatus = HttpStatus.valueOf(httpCode);
        throw new HttpStatusException(httpStatus);
    }

    private static void handleHttpThrowable(Exception ex, String url) throws HttpStatusException {
        if (ex instanceof HttpStatusException) {
            throw (HttpStatusException) ex;
        }
        logger.error("OkHttp error url: " + url, ex);
        if (ex instanceof SocketTimeoutException) {
            throw new RuntimeException("request time out of OkHttp when do url:" + url);
        }
        throw new RuntimeException(ex);
    }

}


/**
 * json解析工具类
 *
 * @author soonphe
 * @since 1.0
 */
public class JsonUtil {

    private JsonUtil() {
    }

    /**
     * 避免指令重排序导致拿到半初始化的对象，保证线程可见性
     */
    private static volatile Gson gson;

    private static Gson getGson() {
        if (gson == null) {
            synchronized (JsonUtil.class) {
                if (gson == null) {
                    gson = new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create();
                }
            }
        }
        return gson;
    }

    public static <T> String toJson(T object) {
        return getGson().toJson(object);
    }


    public static <T> T fromJson(String json, Type type) {
        return getGson().fromJson(json, type);
    }

    public static <T> T fromJson(String json, Class<T> clazz) {
        return getGson().fromJson(json, clazz);
    }
}

/**
 * HttpStatusException
 *
 * @author soonphe
 * @since 1.0
 */
public class HttpStatusException extends Exception {

    private HttpStatus httpStatus;


    private String result;

    public HttpStatusException(HttpStatus httpStatus) {
        this.httpStatus = httpStatus;
    }

    public HttpStatusException(HttpStatus httpStatus, String result) {
        this.httpStatus = httpStatus;

    }

    public HttpStatus getHttpStatus() {
        return httpStatus;
    }

    public String getResult() {
        return result;
    }
}
```
请求示例
```
//同步请求
HttpUtil.doPost(url,JsonUtil.toJson(orderQueryRequest), null, null);

//异步请求
HttpUtil.doPostAsyc(url,
	JsonUtil.toJson(orderQueryRequest), null, new Callback() {
		@Override
		public void onFailure(Call call, IOException e) {
			log.error("fail", e.toString());
		}

		@Override
		public void onResponse(Call call, Response response) throws IOException {
			log.info("success", response.body());
			String result;
			ResponseBody body = response.body();
			if (body != null) {
				result = body.string();
				log.info("result:" + result);
			}
		}
	});
```


### SpringBoot中CommandLineRunner的作用
> 平常开发中有可能需要实现在项目启动后执行的功能，SpringBoot提供的一种简单的实现方案就是添加一个model并实现CommandLineRunner接口，实现功能的代码放在实现的run方法中

代码实现
``` java
package org.springboot.sample.runner;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class MyStartupRunner implements CommandLineRunner {

	@Override
	public void run(String... args) throws Exception {
		System.out.println(">>>>>>>>>>>>>>>服务启动执行，执行加载数据等操作<<<<<<<<<<<<<");
	}

}
```

### Spring JdbcTemplate 方法详解
JdbcTemplate主要提供以下五类方法：
- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

JdbcTemplate类支持的回调类：
预编译语句及存储过程创建回调：用于根据JdbcTemplate提供的连接创建相应的语句；
PreparedStatementCreator：通过回调获取JdbcTemplate提供的Connection，由用户使用该Conncetion创建相关的PreparedStatement；
CallableStatementCreator：通过回调获取JdbcTemplate提供的Connection，由用户使用该Conncetion创建相关的CallableStatement；
预编译语句设值回调：用于给预编译语句相应参数设值；
PreparedStatementSetter：通过回调获取JdbcTemplate提供的PreparedStatement，由用户来对相应的预编译语句相应参数设值；
BatchPreparedStatementSetter：；类似于PreparedStatementSetter，但用于批处理，需要指定批处理大小；
自定义功能回调：提供给用户一个扩展点，用户可以在指定类型的扩展点执行任何数量需要的操作；
ConnectionCallback：通过回调获取JdbcTemplate提供的Connection，用户可在该Connection执行任何数量的操作；
StatementCallback：通过回调获取JdbcTemplate提供的Statement，用户可以在该Statement执行任何数量的操作；
PreparedStatementCallback：通过回调获取JdbcTemplate提供的PreparedStatement，用户可以在该PreparedStatement执行任何数量的操作；
CallableStatementCallback：通过回调获取JdbcTemplate提供的CallableStatement，用户可以在该CallableStatement执行任何数量的操作；
结果集处理回调：通过回调处理ResultSet或将ResultSet转换为需要的形式；
RowMapper：用于将结果集每行数据转换为需要的类型，用户需实现方法mapRow(ResultSet rs, int rowNum)来完成将每行数据转换为相应的类型。
RowCallbackHandler：用于处理ResultSet的每一行结果，用户需实现方法processRow(ResultSet rs)来完成处理，在该回调方法中无需执行rs.next()，该操作由JdbcTemplate来执行，用户只需按行获取数据然后处理即可。
ResultSetExtractor：用于结果集数据提取，用户需实现方法extractData(ResultSet rs)来处理结果集，用户必须处理整个结果集；


### Spring FactoryBean和BeanFactory 区别
1. BeanFactory 是ioc容器的底层实现接口，是顶层容器（根容器），不能被实例化，不允许我们直接操作 BeanFactory bean工厂，它定义了所有 IoC 容器 必须遵从 的⼀套原则，具体的容器实现可以增加额外的功能。

  	BeanFactory 接口又衍生出以下接口
    - ApplicationContext，ApplicationContext包含BeanFactory的所有功能,同时还进行更多的扩展， 
      - ClassPathXmlApplicationContext 包含了解析 xml 等⼀系列的内容
      - AnnotationConfigApplicationContext 则是包含了注解解析等⼀系列的内容。Spring IoC 容器继承体系

2. FactoryBean 是spirng提供的工厂bean的一个接口
FactoryBean 接口提供三个方法，用来创建对象。 
FactoryBean 具体返回的对象是由getObject 方法决定的。 
```
public interface FactoryBean<T> {

	//创建的具体bean对象的类型
	@Nullable
	T getObject() throws Exception;

	//工厂bean 具体创建具体对象是由此getObject()方法来返回的
	@Nullable
	Class<?> getObjectType();
	
 	//是否单例
	default boolean isSingleton() {
		return true;
	}

}
```

### Java8之Consumer、Supplier、Predicate和Function

这几个接口都在 java.util.function 包下的，分别是
- Consumer<T>（消费型）

  Consumer<T>接口就是一个消费型的接口，实现 accept 的方法，T：入参类型；没有出参。
  ```
  Consumer<String> consumer = new Consumer<String>() {
	
                @Override
                public void accept(String s) {
                    System.out.println(s);
                }
            };
    //lambda表达式
	Consumer<String> consumer1 = (s) -> System.out.println(s);//lambda表达式返回的就是一个Consumer接口
  ```
  
- Supplier<T>（供给型）

  Supplier<T> 接口是一个供给型的接口，实现 get 方法。T：出参类型；没有入参。可以用来存储数据，然后可以供其他方法使用的这么一个接口
  ```
  Supplier<Integer> supplier = () -> new Random().nextInt();
  ```
  
- Predicate<T>（谓词型）

  Predicate<T> 接口是一个谓词型接口，实现一个 test 方法。T：入参类型；出参类型是Boolean。作用：类似于 bool 类型的判断的接口。
  ```
  Predicate<Integer> predicate = (t) -> t > 5;
  ```
  
- Function<T, R> （功能性）

  Function<T, R>  接口是一个功能型接口，实现 apply 方法。T：入参类型，R：出参类型。作用：将输入数据转换成另一种形式的输出数据。
  ```
  Function<String, Integer> function = new Function<String, Integer>() {
            @Override
            public Integer apply(String s) {
                return s.length();//获取每个字符串的长度，并且返回
            }
        };
  
  ```

### 反射操作如何得到对象、方法、参数
- 反射获取对象
```
//注意，要传完整类名，如：com.soonphe.timber.hello.Helloworld
Class cls = Class.forName("完整类名");
Object yourObj = cls.newInstance();
```
- 反射获取方法
```
Method methlist[] = cls.getDeclaredMethods();
for (int i = 0; i < methlist.length; i++) {
	Method m = methlist[i];
}
```
- 反射获取方法参数
```
//获取到方法对象,假设方法的参数是一个int,method名为setAge
Method sAge = cls.getMethod("setAge", new Class[] {int.class});
//构造参数Object
Object[] arguments = new Object[] { new Integer(37)};
//执行方法
sAge.invoke(yourObj , arguments);
```


### 反射时getConstructor()与getDeclaredConstructor()方法的区别及setAccessible()方法的作用
```
	@Test
	void testName() throws Exception {
		HelloWorld world=null;
		String className="hello.HelloWorld";
		Constructor con=Class.forName(className).getConstructor();
		//设置不检查是否可以访问
		con.setAccessible(true);
		Object obj=con.newInstance();
		world=(HelloWorld) obj;
		world.sayHello();
	}
```
**getConstructor()** 和 **getDeclaredConstructor()** 的作用：获取类的构造器	

不同点：
- Class类的getConstructor()方法,无论是否设置setAccessible(),都不可获取到类的私有构造器.
- Class类的getDeclaredConstructor()方法,可获取到类的私有构造器(包括带有其他修饰符的构造器），但在使用private的构造器时，必须设置setAccessible()为true,才可以获取并操作该Constructor对象。


### <? extends U> 和 <? super U>的区别：
- <? super U> ：规定元素的最小粒度下限，不影响往里存，但往外取只能放在Object对象里，如Number super Integer。
```
Box<? super RedApple> box3 = new Box<Fruit>();
```

- <? extends U>：规定元素的最大粒度上限，不影响取，存只能存指定类型的变量，如：Integer extends Number。
```
Box<? extends Food> box1 = new Box<Fruit>();
Box<? extends Food> box2 = new Box<Meat>();
```

### spring-boot-maven-plugin打两个包jar/exec.jar
参考文档：https://blog.csdn.net/guduyishuai/article/details/60968728

一、spring-boot-maven-plugin打包出来的jar是不可依赖的

         比如我有一个root工程，type为pom，下面两个spring-boot工程作为它的module，分别为moduleA和moduleB。假如moduleA依赖于moduleB。如果你在moduleB中使用了spring-boot-maven-plugin的默认配置build，或者在root中使用spring-boot-maven-plugin的默认配置build。很遗憾，你在clean package的时候会发现moduleA找不到moduleB中的类。原因就是默认打包出来的jar是不可依赖的。

         解决方案：

        1、调整你的代码，把spring-boot的东西从moduleB中移走。官方文档是这样说的，但是大部分人不会

              这么干吧。

        2、官方告诉我们，你如果不想移代码，好吧，我这样来给你解决，给你打两个jar包，一个用来直接执

              行，一个用来依赖。于是，你需要指定一个属性classifier，这个属性为可执行jar包的名字后缀。比

              如我设置<classifier>exec</classifier>，原项目名为Vehicle-business。

              那么我会得到两个jar：Vehicle-business.jar和Vehicle-bussiness-exec.jar

         官方文档位置：84.5 Use a Spring Boot application as a dependency

		总结：回到聚合maven上，如果你在root工程中使用了spring-boot-maven-plugin作为builder，那么你的依赖module一定要用解决方案二来设置。否则你不在root工程中用spring-boot-maven-plugin作为builder，而在需要打包的module上使用。

### Okhttp连接超时
错误日志
```
2021-09-23 16:18:13.863||[HSFBizProcessor-DEFAULT-8-thread-19]||| OkHttp error url: http://xxx.gateway.com:9966/api/query/es/getOrderListByQuery
java.net.SocketTimeoutException: timeout
```
- 解决方案：
修改最大连接数：
```
newOkHttpClient.Builder()
.connectTimeout(CONNECT_TIMEOUT,TimeUnit.SECONDS)
.readTimeout(READ_TIMEOUT,TimeUnit.SECONDS)
.connectionPool(newConnectionPool(32,5,TimeUnit.MINUTES))
.build();
```
- 参考文档：
https://blog.csdn.net/Vincent2014Linux/article/details/98881462

  

### CompletableFuture
使用Future获得异步执行结果时，要么调用阻塞方法get()，要么轮询看isDone()是否为true，这两种方法都不是很好，因为主线程也会被迫等待。

从Java 8开始引入了CompletableFuture，它针对Future做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。

### litepal操作
取：
LitePal.findAll(TGame.class);
LitePal.order("id desc").limit(1).findFirst(TDataVersion.class);
LitePal.where("phone = ?", phone + "").order("id desc").findFirst(TStats.class);
LitePal.findFirst(TAdvert.class) != null) {
LitePal.deleteAll(TAdvert.class);

存：object.save





