# 0-1Learning

![alt ](../static/common/svg/luoxiaosheng.svg "公众号")
![alt ](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt ](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## sentinel流控防护
- github地址：https://github.com/alibaba/Sentinel
- 官网地址：https://sentinelguard.io/

随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

Sentinel 具有以下特征:

丰富的应用场景：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
完备的实时监控：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
广泛的开源生态：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Apache Dubbo、gRPC、Quarkus 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。同时 Sentinel 提供 Java/Go/C++ 等多语言的原生实现。
完善的 SPI 扩展机制：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

Sentinel 分为两个部分:

- 核心库（Java 客户端）不依赖任何框架/库，能够运行于所有 Java 运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
- 控制台（Dashboard）基于 Spring Boot 开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。


### 快速接入

#### STEP 1. 在应用中引入Sentinel Jar包
```
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-core</artifactId>
    <version>1.8.4</version>
</dependency>
```
spring-cloud应用引入：
```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
<!--sentinel持久化到nacos用到-->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```
### 下载控制界面jar：
https://github.com/alibaba/Sentinel/releases

执行运行命令： java -jar ./sentinel-dashboard-1.8.0.jar

访问：http://localhost:8080/# (登陆的账号密码都是sentinel)

一开始没有看到任何监控服务，但这并不能证明配置有问题，由于sentinel的内部机制就是懒加载，所以我们需要先访问微服务的任意可访问接口初始化加载一次即可。

### 配置yml
```
server:
  port: 8083

spring:
  application:
    name: cloud-sentinel-service
  cloud:
#    nacos:
#      discovery:
#        server-addr: localhost:8848 #Nacos服务注册中心地址
    sentinel:
      transport:
        dashboard: localhost:8080 #配置Sentinel dashboard地址
        port: 8719 #默认8719，如果被占用会从8719开始扫描+1直到找到未被占用的端口

# 图形化界面监控
management:
  endpoints:
    web:
      exposure:
        include: '*'

feign:
  sentinel:
    enabled: true # 激活Sentinel对Feign的支持
```
启动服务后查看8080dashboard还是没东西，因为dashboard是懒加载，需要有访问请求才会刷新

### 流控规则
https://sentinelguard.io/zh-cn/docs/basic-api-resource-rule.html

控制台——流控规则
- 资源名：唯一名称，默认请求路径
- 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default(不区分来源)
- 阈值类型（QPS和并发线程数）
  - QPS的单机阈值代表每秒最多允许请求多少次资源
  - 并发线程数的单机阈值代表代表可以同时运行的线程数量
- 是否集群
- 流控模式
  - 直接：代表超过阈值直接限流
  - 关联：代表关联的资源达到阈值时也进行限流。A、B接口关联，如果A接口被限流了，那么B接口也无法访问
  - 链路：代表当服务到达阈值时，所有使用该服务的服务也限流。A->B->C接口为链路调用关系时，当其中一个接口出现限流，那么整个链路上的接口都无法使用
- 流控效果
  - 快速失败：指直接进行限流
  - Warm Up：冷启动，某个接口平时访问量不大，突然某段时间访问量突增，针对某些抽奖类型的接口。开始时QPS的通过量为（请求数量/3），经设置预热时长才逐渐升至设定的QPS阈值。
  - 排队等待： 就是简单队列形式的等待，让限流的请求排队等待系统空闲时再通过，需要配置超时时间，过了超时时间再拒绝请求。

控制台——降级规则
- 熔断规则
  - 慢调用比例：最大RT（请求的最大调用时间），当一个请求响应时间超过该时间就被判断为慢调用，当慢调用的接口的比例超过比例阈值就进行熔断降级处理。在统计时长内请求资源的次数超过最小请求数，并且这些请求响应时间超过最大RT，总的慢调用比例超过比例阈值，就进行熔断降级处理
  - 异常比例： 在统计时长内当请求超过最小请求数，且异常比例超过比例阈值时进行熔断降级。
  - 异常数： 在统计时长内当请求超过最小请求数，且异常请求数超过异常数时进行熔断降级。

- 热点规则：针对于接口中某个参数请求次数过多，对其进行限流或者其他操作。热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的 Top K 数据，并对其访问进行限制
  - 参数索引：代表针对从第0位开始，依次向后递增代表第几个参数，在统计窗口时长内访问次数超过单机阈值，则会交由@SentinelResource注解blockHandler所指向的方法进行熔断处理。
    - @SentinelResource(value="热点key", blockHandler="自定义兜底规则方法")
  - 资源名：可以是限流的value值（即 testHotkey)也可以是限流接口的uri（即 /testHotkey）
  - 参数索引：0为最高，即p1,如果没有参数p1，访问频次多高也不会出问题
  - 单机阈值：单机限定次数
  - 统计窗口时长：多少窗口时长
  - 参数例外项：配置参数例外项的效果就是，正常p1的访问限制是多少秒多少次，这里可以设定指定参数值的限流阈值。
  - 备注：@SentinelResource只负责控制台配置的错误处理，即blockhandler只处理value绑定的问题，但如果程序自己抛异常，他是处理不了的。

- 系统规则：前面规则只针对于单一资源，系统规则针对的是整个应用入口
  - LOAD：当系统1分钟内的load（即系统的负载）超过阈值，且并发线程数超过系统容量时触发。 建议设置为CPU核心数 * 2.5 （仅对LInux/Unix-like机器生效） 。load，系统平均负载，linux中使用命令 uptime查看load指标。 系统容量（sentinel计算出来的） = maxQps * minRt 。maxQps: 秒级统计出来的最大QPS，minRt : 秒级统计出来的最小响应时间
  - RT:前面已经使用过，即是平均响应时间。当单台机器上所有入口流量的平均 RT 达到阈值即触发系保护，单位是毫秒。
  - 线程数：即是指线程的并发数，与前面类似。当并发线程数超过设定的阈值，就会发生服务的熔断降级
  - QPS：所有入口流量在1秒钟内的请求达到设置阈值都会触发。 QPS = 总请求数 / ( 进程总数 * 请求时间 )
  - CPU 使用率：1.5.0+ 版本，当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。

**@SentinelResource再深入**
目前限流方案存在的问题
1. 系统默认的，没有体现我们自己的业务要求。
2. 依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观。
3. 每个业务方法都要添加一个兜底方法，代码膨胀家具。
4. 全局统一的处理方法没有体现。

我们进行如下操作：
新增流控处理类blockhandler

在流控接口上进行配置，使其关联到自定义规则的相关方法。
@SentinelResource(value="热点key", blockHandlerClass= "全局流控处理.class", blockHandler="流控方法方法")

之前我们曾提到blockHandler只用于处理流控异常问题。对于程序本身的运行是异常是不做处理的。此处我们就需要调用另一种方法--fallback
@SentinelResource(value="热点key", fallbackClass="全局流控处理.class", fallback="异常处理方法", blockHandlerClass= "全局流控处理.class", blockHandler="流控方法方法")
当再次访问接口时，就会走 fallbackException 处理运行时异常的问题。

**sentinel规则持久化**
现在sentinel控制台存在这么一个问题，当重新登录控制台或者关闭服务，控制台的流控规则就消失了。

sentinel持久化有三种模式

- 原始模式：
  - API将规则推送至客户端并直接更新到内存中，扩展写数据源(writableDataSource)
  - 简单，无任何依赖；
  - 不保证一致性；规则保存在内存中，重启即消失。严重不建议用于生产环境
- Pull模式：
  - 扩展写数据源（WritableDataSource），客户端主动向某个规则管理中心定期轮询拉取规则，这个规则中心可以是RDBMS、文件等
  - 简单，无任何依赖；
  - 不保证一致性；实时性不保证，拉取过于频繁也可能会有性能问题
- Push模式：
  - 扩展读数据源（ReadableDataSource），规则中心统一推送，客户端通过注册监听器的方式时刻监听变化，比如使用Nagos、Zookeeper等配置中心。这种方式有更好的实时性和一致性保证。生产环境下一般采用push模式的数据源。
  - 规则持久化，一致性，快速
  - 引入第三方依赖
  
push模式：
我们可以将流控规则持久化进nacos进行保存。只要刷新微服务的某个rest地址，sentinel控制台的流控规则就能看到，只要nacos里面的配置不删除，针对改微服务的流控规则持续有效。
1. 依赖
```
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```
2. yml
```yml
spring: 
  profiles:
  active: dev
application:
  name: consumer
cloud: 
  sentinel: 
    transport: 
      dashboard: 127.0.0.1:8080 
      port: 8917 
    datasource: 
      ds1: 
        nacos: 
          server-addr: 192.168.50.37:8848
          dataId: consumer
          group: DEFAULT_GROUP 20
          data-type: json
          rule-type: flow
```

4. 登录nacos新增配置
```
[
  {
  "resource": "/testHotkey",
  "limitApp": "default",
  "grade": 1,
  "count": 1,
  "strategy": 0,
  "controlBehavior": 0,
  "clusterMode": false
  }
] 
```
- resource：资源名称；
- limitApp：来源应用；
- grade：阈值类型，0表示线程数，1表示QPS；
- count：单机阈值；
- strategy：流控模式，0表示直接，1表示关联，2表示链路；
- controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待； 
- clusterMode：是否集群。

5. 重启服务，测试接口


#### STEP 2. 定义资源
接下来，我们把需要控制流量的代码用 Sentinel API SphU.entry("HelloWorld") 和 entry.exit() 包围起来即可。在下面的例子中，我们将 System.out.println("hello world"); 这端代码作为资源，用 API 包围起来（埋点）。参考代码如下:
```
public static void main(String[] args) {
    initFlowRules();
    while (true) {
        Entry entry = null;
        try {
	    entry = SphU.entry("HelloWorld");
            /*您的业务逻辑 - 开始*/
            System.out.println("hello world");
            /*您的业务逻辑 - 结束*/
	} catch (BlockException e1) {
            /*流控逻辑处理 - 开始*/
	    System.out.println("block!");
            /*流控逻辑处理 - 结束*/
	} finally {
	   if (entry != null) {
	       entry.exit();
	   }
	}
    }
}
```

#### 注解方式定义资源
依赖（com.alibaba.cloud方式则不需要）
```
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-annotation-aspectj</artifactId>
    <version>x.y.z</version>
</dependency>
```

@SentinelResource 注解

注意：注解方式埋点不支持 private 方法。

@SentinelResource 用于定义资源，并提供可选的异常处理和 fallback 配置项。 @SentinelResource 注解包含以下属性：

- value：资源名称，必需项（不能为空）
- entryType：entry 类型，可选项（默认为 EntryType.OUT）
- blockHandler / blockHandlerClass: blockHandler 对应处理 BlockException 的函数名称，可选项。blockHandler 函数访问范围需要是 public，返回类型需要与原方法相匹配，参数类型需要和原方法相匹配并且最后加一个额外的参数，类型为 BlockException。blockHandler 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 blockHandlerClass 为对应的类的 Class 对象，注意对应的函数必需为 static 函数，否则无法解析。
- fallback / fallbackClass：fallback 函数名称，可选项，用于在抛出异常的时候提供 fallback 处理逻辑。fallback 函数可以针对所有类型的异常（除了 exceptionsToIgnore 里面排除掉的异常类型）进行处理。fallback 函数签名和位置要求：
  - 返回值类型必须与原函数返回值类型一致；
  - 方法参数列表需要和原函数一致，或者可以额外多一个 Throwable 类型的参数用于接收对应的异常。
- fallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 fallbackClass 为对应的类的 Class 对象，注意对应的函数必需为 static 函数，否则无法解析。
- defaultFallback（since 1.6.0）：默认的 fallback 函数名称，可选项，通常用于通用的 fallback 逻辑（即可以用于很多服务或方法）。默认 fallback 函数可以针对所有类型的异常（除了 exceptionsToIgnore 里面排除掉的异常类型）进行处理。若同时配置了 fallback 和 defaultFallback，则只有 fallback 会生效。defaultFallback 函数签名要求：
  - 返回值类型必须与原函数返回值类型一致；
  - 方法参数列表需要为空，或者可以额外多一个 Throwable 类型的参数用于接收对应的异常。
  - defaultFallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 fallbackClass 为对应的类的 Class 对象，注意对应的函数必需为 static 函数，否则无法解析。
- exceptionsToIgnore（since 1.6.0）：用于指定哪些异常被排除掉，不会计入异常统计中，也不会进入 fallback 逻辑中，而是会原样抛出。

1.8.0 版本开始，defaultFallback 支持在类级别进行配置。



示例：
```java
public class TestService {

    // 原函数
    @SentinelResource(value = "hello", blockHandler = "exceptionHandler", fallback = "helloFallback")
    public String hello(long s) {
        return String.format("Hello at %d", s);
    }
    
    // Fallback 函数，函数签名与原函数一致或加一个 Throwable 类型的参数.
    public String helloFallback(long s) {
        return String.format("Halooooo %d", s);
    }

    // Block 异常处理函数，参数最后多一个 BlockException，其余与原函数一致.
    public String exceptionHandler(long s, BlockException ex) {
        // Do some log here.
        ex.printStackTrace();
        return "Oops, error occurred at " + s;
    }

    // 这里单独演示 blockHandlerClass 的配置.
    // 对应的 `handleException` 函数需要位于 `ExceptionUtil` 类中，并且必须为 public static 函数.
    @SentinelResource(value = "test.md", blockHandler = "handleException", blockHandlerClass = {ExceptionUtil.class})
    public void test() {
        System.out.println("Test");
    }
}
```

配置
Spring Cloud Alibaba
若您是通过 Spring Cloud Alibaba 接入的 Sentinel，则无需额外进行配置即可使用 @SentinelResource 注解。

Spring AOP
若您的应用使用了 Spring AOP（无论是 Spring Boot 还是传统 Spring 应用），您需要通过配置的方式将 SentinelResourceAspect 注册为一个 Spring Bean：
```
@Configuration
public class SentinelAspectConfiguration {

    @Bean
    public SentinelResourceAspect sentinelResourceAspect() {
        return new SentinelResourceAspect();
    }
}
```
我们提供了 Spring AOP 的示例，可以参见 sentinel-demo-annotation-spring-aop。

AspectJ
若您的应用直接使用了 AspectJ，那么您需要在 aop.xml 文件中引入对应的 Aspect：

<aspects>
    <aspect name="com.alibaba.csp.sentinel.annotation.aspectj.SentinelResourceAspect"/>
</aspects>

#### STEP 3. 定义规则
接下来，通过规则来指定允许该资源通过的请求次数，例如下面的代码定义了资源 HelloWorld 每秒最多只能通过 20 个请求。
```
private static void initFlowRules(){
    List<FlowRule> rules = new ArrayList<>();
    FlowRule rule = new FlowRule();
    rule.setResource("HelloWorld");
    rule.setGrade(RuleConstant.FLOW_GRADE_QPS);
    // Set limit QPS to 20.
    rule.setCount(20);
    rules.add(rule);
    FlowRuleManager.loadRules(rules);
}
```
完成上面 3 步，Sentinel 就能够正常工作了。更多的信息可以参考 使用文档。

#### STEP 4. 检查效果
Demo 运行之后，我们可以在日志 ~/logs/csp/${appName}-metrics.log.xxx 里看到下面的输出:

```
|--timestamp-|------date time----|-resource-|p |block|s |e|rt
1529998904000|2018-06-26 15:41:44|HelloWorld|20|0    |20|0|0
1529998905000|2018-06-26 15:41:45|HelloWorld|20|5579 |20|0|728
1529998906000|2018-06-26 15:41:46|HelloWorld|20|15698|20|0|0
1529998907000|2018-06-26 15:41:47|HelloWorld|20|19262|20|0|0
1529998908000|2018-06-26 15:41:48|HelloWorld|20|19502|20|0|0
1529998909000|2018-06-26 15:41:49|HelloWorld|20|18386|20|0|0
```


其中 p 代表通过的请求, block 代表被阻止的请求, s 代表成功执行完成的请求个数, e 代表用户自定义的异常, rt 代表平均响应时长。

可以看到，这个程序每秒稳定输出 "hello world" 20 次，和规则中预先设定的阈值是一样的。

更详细的说明可以参考: 如何使用

更多的例子可以参考: Sentinel Examples

#### STEP 5. 启动 Sentinel 控制台
您可以参考 Sentinel 控制台文档 启动控制台，可以实时监控各个资源的运行情况，并且可以实时地修改限流规则。

---

### 客户端
Sentinel 提供一个轻量级的开源控制台，它提供机器发现以及健康情况管理、监控（单机和集群），规则管理和推送的功能。这里，我们将会详细讲述如何通过简单的步骤就可以使用这些功能。

Sentinel 控制台提供一个轻量级的控制台，它提供机器发现、单机资源实时监控、集群资源汇总，以及规则管理的功能。您只需要对应用进行简单的配置，就可以使用这些功能。

注意: 集群资源汇总仅支持 500 台以下的应用集群，有大概 1 - 2 秒的延时。

Sentinel 控制台包含如下功能:

- 查看机器列表以及健康情况：收集 Sentinel 客户端发送的心跳包，用于判断机器是否在线。
- 监控 (单机和集群聚合)：通过 Sentinel 客户端暴露的监控 API，定期拉取并且聚合应用监控信息，最终可以实现秒级的实时监控。
- 规则管理和推送：统一管理推送规则。
- 鉴权：生产环境中鉴权非常重要。这里每个开发者需要根据自己的实际情况进行定制。

注意：Sentinel 控制台目前仅支持单机部署。Sentinel 控制台项目提供 Sentinel 功能全集示例，不作为开箱即用的生产环境控制台，不提供安全可靠保障。若希望在生产环境使用请根据文档自行进行定制和改造。

#### 启动控制台

2.1 获取 Sentinel 控制台
- 您可以从 release 页面 下载最新版本的控制台 jar 包。
- 您也可以从最新版本的源码自行构建 Sentinel 控制台：

下载 控制台 工程
使用以下命令将代码打包成一个 fat jar: mvn clean package
2.2 启动
注意：启动 Sentinel 控制台需要 JDK 版本为 1.8 及以上版本。

使用如下命令启动控制台：

java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
其中 -Dserver.port=8080 用于指定 Sentinel 控制台端口为 8080。

从 Sentinel 1.6.0 起，Sentinel 控制台引入基本的登录功能，默认用户名和密码都是 sentinel。可以参考 鉴权模块文档 配置用户名和密码。

注：若您的应用为 Spring Boot 或 Spring Cloud 应用，您可以通过 Spring 配置文件来指定配置，详情请参考 Spring Cloud Alibaba Sentinel 文档。

配置控制台信息
application.yml
```
spring:
  cloud:
    sentinel:
      transport:
        port: 8719
        dashboard: localhost:8080
```

#### 客户端接入控制台
依赖（com.alibaba.cloud方式则不需要）
```
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-simple-http</artifactId>
    <version>x.y.z</version>
</dependency>
```

3.2 配置启动参数
启动时加入 JVM 参数 -Dcsp.sentinel.dashboard.server=consoleIp:port 指定控制台地址和端口。若启动多个应用，则需要通过 -Dcsp.sentinel.api.port=xxxx 指定客户端监控 API 的端口（默认是 8719）。

从 1.6.3 版本开始，控制台支持网关流控规则管理。您需要在接入端添加 -Dcsp.sentinel.app.type=1 启动参数以将您的服务标记为 API Gateway，在接入控制台时您的服务会自动注册为网关类型，然后您即可在控制台配置网关规则和 API 分组。

除了修改 JVM 参数，也可以通过配置文件取得同样的效果。更详细的信息可以参考 启动配置项。

3.3 触发客户端初始化
确保客户端有访问量，Sentinel 会在客户端首次调用的时候进行初始化，开始向控制台发送心跳包。

注意：您还需要根据您的应用类型和接入方式引入对应的 适配依赖，否则即使有访问量也不能被 Sentinel 统计。

#### 查看机器列表以及健康情况
   当您在机器列表中看到您的机器，就代表着您已经成功接入控制台；如果没有看到您的机器，请检查配置，并通过 ${user.home}/logs/csp/sentinel-record.log.xxx 日志来排查原因，详细的部分请参考 日志文档。


### Feign 支持
Sentinel 适配了 Feign 组件。如果想使用，除了引入 spring-cloud-starter-alibaba-sentinel 的依赖外还需要 2 个步骤：

- 配置文件打开 Sentinel 对 Feign 的支持：feign.sentinel.enabled=true

- 加入 spring-cloud-starter-openfeign 依赖使 Sentinel starter 中的自动化配置类生效：
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

这是一个 FeignClient 的简单使用示例：
```
@FeignClient(name = "service-provider", fallback = EchoServiceFallback.class, configuration = FeignConfiguration.class)
public interface EchoService {
    @RequestMapping(value = "/echo/{str}", method = RequestMethod.GET)
    String echo(@PathVariable("str") String str);
}

class FeignConfiguration {
    @Bean
    public EchoServiceFallback echoServiceFallback() {
        return new EchoServiceFallback();
    }
}

class EchoServiceFallback implements EchoService {
    @Override
    public String echo(@PathVariable("str") String str) {
        return "echo fallback";
    }
}
```
Feign 对应的接口中的资源名策略定义：httpmethod:protocol://requesturl。@FeignClient 注解中的所有属性，Sentinel 都做了兼容。

