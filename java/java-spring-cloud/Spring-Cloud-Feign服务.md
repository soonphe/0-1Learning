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

Feign是一个声明式的REST客户端，它的目的就是让REST调用更加简单。通过提供HTTP请求模板，让Ribbon请求的书写更加简单和便捷。
另外，在Feign中整合了Ribbon，从而不需要显式的声明Ribbon的jar包。
Feign还自带断路器


### 依赖
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
另外，我们需要添加spring-cloud-dependencies：
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


### 配置文件
如果不配置，就会使用默认的配置
```
feign:
  client:
    config:                                         
    # 默认为所有的feign client做配置(注意和上例github-client是同级的)
      default:                                      
        connectTimeout: 5000                        # 连接超时时间
        readTimeout: 5000                           # 读超时时间设置  
```


### 启用Feign服务流程
1. 服务提供方Application，使用注解@EnableFeignClients启用feign客户端；
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

2. 服务提供方api，使用注解@FeignClient 定义feign客户端 ;
这里的name为value的别名，即该接口对应于应用id为authsrv的微服务
```
@FeignClient(name = "authsrv", contextId = "platform", path = "permission/platform")
public interface IPlatformService {

	@GetMapping("findById/{id}")
  	OpenAPIResponse<PlatformResponse> findById(@PathVariable(name = "id") Long id);

    @RequestMapping(value = "/echo", method = RequestMethod.GET)
    TestModel echo(@RequestParam("parameter") String parameter);
}
```

3. 服务提供方Controller
```
@RestController
@RequestMapping(value = "permission/platform")
public class PlatformController {

  private final IPlatformApplicationService platformApplicationService;

  public PlatformController(IPlatformApplicationService platformApplicationService) {
    this.platformApplicationService = platformApplicationService;
  }

  @GetMapping(value = "findById/{id}")
  public OpenAPIResponse<PlatformVo> findById(@PathVariable Long id) {
    return OpenAPIResponse.success(platformApplicationService.findById(id));
  }
  
  //使用ribbon+Eureka，这里需要自定义微服务前缀，方法签名
//  private static final String REST_URL_PREFIX = "http://MICROSERVICECLOUD-DEPT";
// 
//	@Autowired
//	private RestTemplate restTemplate;
// 
//	@RequestMapping(value = "/consumer/dept/add")
//	public boolean add(Dept dept){
//		return restTemplate.postForObject(REST_URL_PREFIX + "/dept/add", dept, Boolean.class);
//	}

}
```

4. 服务使用方，使用注解@Autowired使用上面所定义feign的客户端 ；
```
@Autowired   
TestService testService;
 
public void run()
{
    // 这里的使用本地Java API的方式调用远程的Restful接口
    TestModel dto = testService.echo("Hello,你好!");
    log.info("echo : {}", dto);
}
```

### Feign断路器
断路器配置文件
```
server:
  port: 3001
 
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


### Feign调用过程分析
@EnableFeignClients 注解：
```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Import(FeignClientsRegistrar.class)
public @interface EnableFeignClients {

    //等价于basePackages属性，更简洁的方式
	String[] value() default {};
    //指定多个包名进行扫描
	String[] basePackages() default {};

    //指定多个类或接口的class,扫描时会在这些指定的类和接口所属的包进行扫描
	Class<?>[] basePackageClasses() default {};

	 //为所有的Feign Client设置默认配置类
	Class<?>[] defaultConfiguration() default {};

	 //指定用@FeignClient注释的类列表。如果该项配置不为空，则不会进行类路径扫描
	Class<?>[] clients() default {};
}
```
FeignClient 注解：
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface FeignClient {

    //指定Feign Client的名称，如果项目使用了 Ribbon，name属性会作为微服务的名称，用于服务发现
	@AliasFor("name")
	String value() default "";
	//用serviceId做服务发现已经被废弃，所以不推荐使用该配置
	@Deprecated
	String serviceId() default "";
	//指定Feign Client的serviceId，如果项目使用了 Ribbon，将使用serviceId用于服务发现,但上面可以看到serviceId做服务发现已经被废弃，所以也不推荐使用该配置
	@AliasFor("value")
	String name() default "";
	//为Feign Client 新增注解@Qualifier
	String qualifier() default "";
    //请求地址的绝对URL，或者解析的主机名
	String url() default "";
    //调用该feign client发生了常见的404错误时，是否调用decoder进行解码异常信息返回,否则抛出FeignException
	boolean decode404() default false;
     //Feign Client设置默认配置类
	Class<?>[] configuration() default {};
    //定义容错的处理类，当调用远程接口失败或超时时，会调用对应接口的容错逻辑,fallback 指定的类必须实现@FeignClient标记的接口。实现的法方法即对应接口的容错处理逻辑
	Class<?> fallback() default void.class;
    //工厂类，用于生成fallback 类示例，通过这个属性我们可以实现每个接口通用的容错逻辑，减少重复的代码
	Class<?> fallbackFactory() default void.class;
    //定义当前FeignClient的所有方法映射加统一前缀
	String path() default "";
    //是否将此Feign代理标记为一个Primary Bean，默认为ture
	boolean primary() default true;
}
```

FeignClientsRegistrar在这里做了两件事情：
1. 将EnableFeignClients注解对应的配置属性注入；
2. 将FeignClient注解对应的属性注入。

生成FeignClient对应的bean，注入到Spring 的IOC容器。

在registerFeignClient方法中构造了一个BeanDefinitionBuilder对象，BeanDefinitionBuilder的主要作用就是构建一个AbstractBeanDefinition，AbstractBeanDefinition类最终被构建成一个BeanDefinitionHolder 然后注册到Spring中。

beanDefinition类为FeignClientFactoryBean，故在Spring获取类的时候实际返回的是FeignClientFactoryBean类。

FeignClientFactoryBean作为一个实现了FactoryBean的工厂类，那么每次在Spring Context 创建实体类的时候会调用它的getObject()方法。







