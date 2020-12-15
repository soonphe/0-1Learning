# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Eureka服务注册

1.建立Eureka服务项目
new project(选择 Cloud Discover——Eureka Server)

2.依赖关系（会自动导入）
    <dependencies>
        <!--eureka server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
        </dependency>

        <!-- spring boot test-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>	//统一管理依赖版本
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Dalston.RC1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

3.在启动类 注解@EnableEurekaServe

4.配置文件（.yml）
```
spring:
  application:
    name: eurka-server
server:
  port: 8761
eureka:
  instance:
    hostname: localhost
    # 设置心跳间隔
#    lease-renewal-interval-in-seconds: 5
    # 元数据——可以在/eureka/apps/microservice-provider-user/中查看
#    metadata-map:
#      zone: ABC
    //如果需要展示真实IP
    prefer-ip-address: true	
    instance-id: ${spring.cloud.client.ip_address}:${server.port}	//这里设置ipAddress找不到
    instance-id: 192.168.1.6:${server.port}	//这里写死成功
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


——————————————————————————————————————————————
1.建立Eureka服务提供者
new project(选择 Cloud Discover——Eureka Discoverry + Web web)

2.启动类注解：@EnableEurekaClient 表明自己是一个eurekaclient.
```
@SpringBootApplication
@EnableEurekaClient
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
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
server:
  port: 8762
spring:
  application:
    name: service-hi	//指明spring.application.name,这个很重要，这在以后的服务与服务之间相互调用一般都是根据这个name 



### 为什么zookeeper使用奇数个节点
避免脑裂时产生竞争