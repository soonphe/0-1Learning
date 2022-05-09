# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Spring Cloud Feign服务
目前，在Spring cloud 中服务之间通过restful方式调用有两种方式
- restTemplate+Ribbon
- feign

二者区别： 
1. 启动类使用的注解不同，Ribbon用的是@RibbonClient，Feign用的是@EnableFeignClients。
2. 服务的指定位置不同，Ribbon是在@RibbonClient注解上声明，Feign则是在定义抽象方法的接口中使用@FeignClient声明。
3. 调用方式不同，Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐。Feign则是在Ribbon的基础上进行了一次改进，采用接口的方式，将需要调用的其他服务的方法定义成抽象方法即可，不需要自己构建http请求。不过要注意的是抽象方法的注解、方法签名要和提供服务的方法完全一致。

- Feign是一个声明式的REST客户端，它的目的就是让REST调用更加简单。通过提供HTTP请求模板，让Ribbon请求的书写更加简单和便捷。
- 另外，在Feign中整合了Ribbon，从而不需要显式的声明Ribbon的jar包。
- Feign还自带断路器


### 依赖
如果我们的依赖中有spring-cloud-dependencies：
```
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
直接添加以下依赖即可
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

或者有
```
    <dependencyManagement>
      <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>${spring-cloud-alibaba.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencyManagement>
```
那添加以下依赖也可以
```
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
```


### 配置文件
如果不配置，就会使用默认的配置
```yml
# 默认为所有的feign client做配置
feign:
  client:
    config:
      default:
        connectTimeout: 5000                        # 连接超时时间  默认的连接时间为10s，读取时间为60s，也可以通过重写FeignClient的config配置类文件的Options方法进行配置
        readTimeout: 5000                           # 读超时时间设置
  okhttp:
    enabled: true   # 默认使用httpClient，也可以使用OkHttp client 作为客户端
#  hystrix:
#    enabled: true  # 全局开启feign的fallback功能
  httpclient:
    max-connections: 300            # 连接池中最大连接数，默认为200，按需配置。
    max-connections-per-route: 60   # 每个route最大并发数，默认50，按需配置。
  compression:
    request:
      enabled: true     # 请求压缩，默认关闭，开启时，默认请求内容大于2M时进行压缩，与feign.compression.request.min-request-size配合使用。

hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 60000  #默认hystrix处理超时时长
```

其他配置说明：
```yml

feign: 
  client: 
    config: 
      feignName: 
        connectTimeout: 5000   # 连接超时时间  默认的连接时间为10s，读取时间为60s，也可以通过重写FeignClient的config配置类文件的Options方法进行配置
        readTimeout: 5000      # 读取超时时间
        loggerLevel: full      # 日志等级
        retryer: com.example.SimpleRetryer # 重试
        requestInterceptors[0]: com.example.FooRequestInterceptor  # 拦截器
        requestInterceptors[1]: com.example.BarRequestInterceptor
        encoder: com.example.SimpleEncoder     # 编码器
        decoder: com.example.SimpleDecoder     # 解码器
        contract: com.example.SimpleContract   # 契约
```

#### 日志配置
有时候我们遇到 Bug，比如接口调用失败、参数没收到等问题，或者想看看调用性能，就需要配置 Feign 的日志了，以此让 Feign 把请求信息输出来。

首先定义一个配置类，代码如下所示。
```java
@Configuration
public class FeignConfiguration {
    /**
     * 日志级别
     *
     * @return
     */
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```
通过源码可以看到日志等级有 4 种，分别是：
- NONE：不输出日志。
- BASIC：只输出请求方法的 URL 和响应的状态码以及接口执行的时间。
- HEADERS：将 BASIC 信息和请求头信息输出。
- FULL：输出完整的请求信息。

配置类建好后，我们需要在 Feign Client 中的 @FeignClient 注解中指定使用的配置类，代码如下所示。
```java
@FeignClient(value = "eureka-client-user-service", configuration = FeignConfiguration. class)
public interface UserRemoteClient {
  // ...
}
```
在配置文件中执行 Client 的日志级别才能正常输出日志，格式是“logging.level.client 类地址=级别”。
```
logging.level.net.biancheng.feign_demo.remote.UserRemoteClient=DEBUG
```

#### 超时时间配置
通过 Options 可以配置连接超时时间和读取超时时间（代码如下所示），Options 的第一个参数是连接超时时间（ms），默认值是 10×1000；第二个是取超时时间（ms），默认值是 60×1000。

```java
@Configuration
public class FeignConfiguration {
    @Bean
    public Request.Options options() {
        return new Request.Options(5000, 10000);
    }
}
```

#### 客户端组件配置
Feign 中默认使用 JDK 原生的 URLConnection 发送 HTTP 请求，我们可以集成别的组件来替换掉 URLConnection，比如 Apache HttpClient，OkHttp。

配置 OkHttp 只需要加入 OkHttp 的依赖，代码如下所示。（最新版本已经自带okhttp依赖，直接配置启用就可以了）
```
<dependency>
  <groupId>io.github.openfeign</groupId>
  <artifactId>feign-okhttp</artifactId>
</dependency>
```
然后修改配置，将 Feign 的 HttpClient 禁用，启用 OkHttp，配置如下：
```
#feign 使用 okhttp
feign.httpclient.enabled=false
feign.okhttp.enabled=true
```

源码中默认使用httpclient，就是通过@ConditionalOnProperty注解配置的：
参见`FeignAutoConfiguration`类：
```
@ConditionalOnProperty(value = "feign.httpclient.enabled", matchIfMissing = true)
	protected static class HttpClientFeignConfiguration {

...

@ConditionalOnProperty("feign.okhttp.enabled")
	protected static class OkHttpFeignConfiguration {
```

#### Feign重试机制配置
feign本身也有重试机制，底层通过Retryer类实现逻辑：
```java
public interface Retryer extends Cloneable {

  /**
   * if retry is permitted, return (possibly after sleeping). Otherwise propagate the exception.
   */
  void continueOrPropagate(RetryableException e);

  Retryer clone();

  class Default implements Retryer {

    private final int maxAttempts;
    private final long period;
    private final long maxPeriod;
    int attempt;
    long sleptForMillis;

    public Default() {
      this(100, SECONDS.toMillis(1), 5);
    }

    public Default(long period, long maxPeriod, int maxAttempts) {
      this.period = period;
      this.maxPeriod = maxPeriod;
      this.maxAttempts = maxAttempts;
      this.attempt = 1;
    }

    // visible for testing;
    protected long currentTimeMillis() {
      return System.currentTimeMillis();
    }

    public void continueOrPropagate(RetryableException e) {
      if (attempt++ >= maxAttempts) {
        throw e;
      }

      long interval;
      if (e.retryAfter() != null) {
        interval = e.retryAfter().getTime() - currentTimeMillis();
        if (interval > maxPeriod) {
          interval = maxPeriod;
        }
        if (interval < 0) {
          return;
        }
      } else {
        interval = nextMaxInterval();
      }
      try {
        Thread.sleep(interval);
      } catch (InterruptedException ignored) {
        Thread.currentThread().interrupt();
        throw e;
      }
      sleptForMillis += interval;
    }

    /**
     * Calculates the time interval to a retry attempt. <br>
     * The interval increases exponentially with each attempt, at a rate of nextInterval *= 1.5
     * (where 1.5 is the backoff factor), to the maximum interval.
     *
     * @return time in nanoseconds from now until the next attempt.
     */
    long nextMaxInterval() {
      long interval = (long) (period * Math.pow(1.5, attempt - 1));
      return interval > maxPeriod ? maxPeriod : interval;
    }

    @Override
    public Retryer clone() {
      return new Default(period, maxPeriod, maxAttempts);
    }
  }
}
```
默认是重试5次，每次重试间隔100ms，最大重试间隔不超过1s
可以通过在FeignClient的config配置类中重写Retryer方法，修改Feign的重试方法和间隔时间
period重试间隔时间ms，maxPeriod最大重试间隔时间ms，maxAttempts最大重试次数（包含第一次请求，即重试次数=maxAttempts-1）

```
	@Bean
    public Retryer feignRetryer() {
        return new Retryer.Default(period, maxPeriod, maxAttempts);
    }
```
feign的重试机制相对来说比较鸡肋，使用Feign 的时候一般会关闭该功能。Ribbon的重试机制默认配置为0，也就是默认是去除重试机制的。


---

### 启用Feign服务流程

#### 定义feign API，服务提供方去实现，消费方使用接口调用
feign API，使用注解@FeignClient 定义feign客户端 ;

```
@FeignClient(name = "authsrv", contextId = "platform", path = "permission/platform")
public interface IPlatformService {

	@GetMapping("findById/{id}")
  	String findById(@PathVariable(name = "id") Long id);

    @RequestMapping(value = "/echo", method = RequestMethod.GET)
    String echo(@RequestParam("parameter") String parameter);
}
```
参数：
- String value() default "";  //微服务ID，即应用名，指定Feign Client的名称，如果项目使用了 Ribbon，name属性会作为微服务的名称，用于服务发现
- String name() default "";   //value的别名指定Feign Client的serviceId，如果项目使用了 Ribbon，将使用serviceId用于服务发现,但上面可以看到serviceId做服务发现已经被废弃，所以也不推荐使用该配置
- String contextId() default "";  //bean名称，如果存在，这将用作 bean 名称而不是名称，但不会用作服务 ID。
- String serviceId() default "";  //用serviceId做服务发现已经被废弃，所以不推荐使用该配置
- String qualifier() default "";  //为Feign Client 新增注解@Qualifier
- String url() default "";  //请求地址的绝对URL，或者解析的主机名
- boolean decode404() default false;  //调用该feign client发生了常见的404错误时，是否调用decoder进行解码异常信息返回,否则抛出FeignException
- Class<?>[] configuration() default {};  //Feign Client设置默认配置类
- Class<?> fallback() default void.class;  //定义容错的处理类，当调用远程接口失败或超时时，会调用对应接口的容错逻辑,fallback 指定的类必须实现@FeignClient标记的接口。实现的方法即对应接口的容错处理逻辑
- Class<?> fallbackFactory() default void.class;  //工厂类，用于生成fallback 类示例，通过这个属性我们可以实现每个接口通用的容错逻辑，减少重复的代码
- String path() default "";  //定义当前FeignClient的所有方法映射加统一前缀
- boolean primary() default true;  //是否将此Feign代理标记为一个Primary Bean，默认为ture




#### 服务提供方

**服务提供方Application**

使用注解`@EnableDiscoveryClient`启用服务发现；
使用注解`@EnableFeignClients(basePackages = {"com.soonphe.timber"})`启用feign客户端；`basePackages`为要扫描的包路径
```
@EnableDiscoveryClient
@SpringBootApplication
@EnableFeignClients
public class TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class, args);
    }
}
```

**@EnableFeignClients 注解参数：**
- String[] value() default {};  //等价于basePackages属性，更简洁的方式
- String[] basePackages() default {};  //指定多个包名进行扫描
- Class<?>[] basePackageClasses() default {};  //指定多个类或接口的class,扫描时会在这些指定的类和接口所属的包进行扫描
- Class<?>[] defaultConfiguration() default {};  //为所有的Feign Client设置默认配置类
- Class<?>[] clients() default {};  //指定用@FeignClient注释的类列表。如果该项配置不为空，则不会进行类路径扫描


**服务提供方Controller**
```
@RestController
@RequestMapping(value = "permission/platform")
public class PlatformController {


  @GetMapping(value = "findById/{id}")
  public String findById(@PathVariable Long id) {
    return "1";
  }
  
  @GetMapping(value = "echo")
  public String findById(@PathVariable String parameter) {
    return parameter;
  }
}
```

**如果使用ribbon+Eureka，这里需要自定义微服务前缀，方法签名**
```
//  private static final String REST_URL_PREFIX = "http://MICROSERVICECLOUD-DEPT";
//
//	@Autowired
//	private RestTemplate restTemplate;
//
//	@RequestMapping(value = "/consumer/dept/add")
//	public boolean add(Dept dept){
//		return restTemplate.postForObject(REST_URL_PREFIX + "/dept/add", dept, Boolean.class);
//	}
```

#### 服务使用方
**Application**
使用注解`@EnableDiscoveryClient`启用服务发现；
使用注解`@EnableFeignClients(basePackages = {"com.soonphe.timber"})`启用feign客户端；`basePackages`为要扫描的包路径

使用注解@Autowired使用上面所定义feign的客户端 ；
```
@Autowired   
TestService testService;
 
public void run()
{
    // 这里的使用本地Java API的方式调用远程的Restful接口
    String dto = testService.echo("Hello,你好!");
    log.info("echo : {}", dto);
}
```

### Feign调用过程分析

FeignClientsRegistrar在这里做了两件事情：
1. 将EnableFeignClients注解对应的配置属性注入；
2. 将FeignClient注解对应的属性注入。

生成FeignClient对应的bean，注入到Spring 的IOC容器。

在registerFeignClient方法中构造了一个BeanDefinitionBuilder对象，BeanDefinitionBuilder的主要作用就是构建一个AbstractBeanDefinition，AbstractBeanDefinition类最终被构建成一个BeanDefinitionHolder 然后注册到Spring中。

beanDefinition类为FeignClientFactoryBean，故在Spring获取类的时候实际返回的是FeignClientFactoryBean类。

FeignClientFactoryBean作为一个实现了FactoryBean的工厂类，那么每次在Spring Context 创建实体类的时候会调用它的getObject()方法。


---
### Feign断路器
断路器配置文件
```
spring:
  application:
    name: feign-consumer
 
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:1111/eureka/
      
#开启feign的断路器功能
feign:
  hystrix:
    enabled: true
ribbon:	#Feign的ribbon超时配置
  ConnectionTimeout: 500 #连接超时
  ReadTimeout: 3000 #读取超时
  
```

api层配置，fallback选项指定服务降级实现类工厂
```
@FeignClient(value = "YunCai-SearchServer", fallback=ServiceHystrix.class)
public interface SchedualServiceHi {
 
	ResponseMap sayHiFromClientOne(@RequestParam(value = "start") Integer start);
}
```
ServiceHystrix.class，可以implements FallbackFactory<FeignConsumerService> 来返回统一处理结果
```
@Component
public class ServiceHystrix implements SchedualServiceHi {
 
	@Override
	public ResponseMap sayHiFromClientOne(Integer start, Integer rows, Integer tenantId, Integer status, String key) {
		SearchResult result = new SearchResult(0, new ArrayList<>(Arrays.asList(1L,2L,3L)));
		return new ResponseMap(false, "111", 506, result);
	}
}
```










