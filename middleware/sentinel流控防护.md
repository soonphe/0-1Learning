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
```


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

