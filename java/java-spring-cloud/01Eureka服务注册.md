# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Eureka服务注册

### Eureka服务注册中心创建

1.建立Eureka服务项目
new project(选择 Cloud Discover——Eureka Server)

2.依赖关系（会自动导入）
```
    <dependencies>
        <!--eureka server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>

    <!-- 可以定义parent统一管理依赖版本 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

3.在启动类 注解@EnableEurekaServe

4.配置文件（.yml）
```
spring:
  application:
    name: eurka-server
server:
  port: 8761
eureka:
  server:
    # 中小规模下，自我保护模式坑比好处多，所以关闭它
    enableSelfPreservation = false
    # 心跳阈值计算周期，如果开启自我保护模式，可以改一下这个配置
#    renewalThresholdUpdateIntervalMs=120000
    # 主动失效检测间隔,配置成5秒
    evictionIntervalTimerInMs = 5000
    # 禁用readOnlyCacheMap
    useReadOnlyResponseCache = false
    # 如果开起Cache可以尝试修改这个值，更新输出缓存的间隔时间
#    response-cache-update-interval-ms=3000
  instance:
    hostname: localhost
    //如果需要展示真实IP
    prefer-ip-address: true	
    # 设置心跳间隔
    lease-renewal-interval-in-seconds: 5
    # 没有心跳的淘汰时间，10秒
    leaseExpirationDurationInSeconds = 10
#    instance-id: ${spring.cloud.client.ip_address}:${server.port}	//这里设置ipAddress找不到
    instance-id: 192.168.1.6:${server.port}	//这里写死成功
    # 元数据——可以在/eureka/apps/microservice-provider-user/中查看
#    metadata-map:
#      zone: ABC
  client:
    # 注册自身到eureka
    registerWithEureka: false
    # 表示是否从eureka server中获取注册信息，单一结点不需要，集群需要设置为true，默认为true
    fetchRegistry: false
    serviceUrl:
      # 这里如果配置集群则需要指向其他注册中心
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      # defaultZone: http://peer2:8001/eureka/,http://peer3:8002/eureka/    //# 例：peer1分别指向peer2和peer3
```

5.访问Eureka界面
eureka server 是有界面的，启动工程,打开浏览器访问： 
http://localhost:8761 ,界面如下：

### Eureka服务发现
1.建立Eureka服务提供者
new project(选择 Cloud Discover——Eureka Discoverry + Web web)

2.依赖关系
```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

3.启动类注解：@EnableDiscoveryClient 表明自己是一个eurekaclient.
```
@SpringBootApplication
@EnableDiscoveryClient
@RestController
public class ServiceHiApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServiceHiApplication.class, args);
    }

    @Value("${server.port}")
    String port;
    @RequestMapping("/hi")
    public String home(@RequestParam String name) {
        return "hi "+name+",i am from port:" +port;
    }

}
```

4.配置文件（.yml）
```
server:
  port: 8762
spring:
  application:
    name: service-hi	//指明spring.application.name,这个很重要，这在以后的服务与服务之间相互调用一般都是根据这个name 
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

5.启动工程，访问Eureka界面(例：http://localhost:8761)查看服务运行状态

主要分为一下几类：
    
    1.System Status：当前Eureka系统的状态信息
        Environment：test为单机运行
        Data center：数据中心
        Current time：系统时间
        Uptime：启动时间
        lease expiration enabled：是否启动租约到期
    2.DS Replicas：副本（单机版默认为localhost）
    3.Instances currently registered with Eureka：所有注册到Eureka上的服务列表
        Application：服务名
        AMIs：
        Availability Zones：可用区
        Status：状态（UP为可用，DOWN为不可用）这里会列举所有服务名和端口
    4.General Info：一般信息
        total-avail-memory：总可用内存
        environment：环境
        num-of-cpus：cpu核数
        current-memory-usage：已用内存
        server-uptime：服务运行时间
        registered-replicas：当前注册的eureka service节点
        unavailable-replicas：当前不可用节点
        available-replicas		
    5.Instance Info：当前实例的信息（ipAddr和status）