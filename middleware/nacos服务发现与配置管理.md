# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## nacos服务发现与配置管理
官网地址：https://nacos.io/zh-cn/index.html
github地址：https://github.com/alibaba/nacos

Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

Nacos 的关键特性包括:

    服务发现和服务健康监测
    动态配置服务
    动态 DNS 服务
    服务及其元数据管理

Nacos、Eureka、zookeeper对比：

    Nacos（可选择CP或AP）、Eureka（AP）、zookeeper（ CP）

### 安装启动
1.预备环境准备
Nacos 依赖 Java 环境来运行。如果您是从代码开始构建并运行Nacos，还需要为此配置 Maven环境，请确保是在以下版本环境中安装使用:

1. 64 bit OS，支持 Linux/Unix/Mac/Windows，推荐选用 Linux/Unix/Mac。
2. 64 bit JDK 1.8+；下载 & 配置。
3. Maven 3.2.x+；下载 & 配置。

2.下载源码或者安装包
你可以通过源码和发行包两种方式来获取 Nacos。

下载编译后压缩包方式
您可以从 最新稳定版本 下载 nacos-server-$version.zip 包。
```
  unzip nacos-server-$version.zip 或者 tar -xvf nacos-server-$version.tar.gz
  cd nacos/bin
```
  
3.启动服务器
Linux/Unix/Mac
启动命令(standalone代表着单机模式运行，非集群模式):
```
sh startup.sh -m standalone
```

Windows
启动命令(standalone代表着单机模式运行，非集群模式):
```
startup.cmd -m standalone
```

启动后可以在网页端查看：*http://localhost:8848/nacos/#/login*

4.服务注册&发现和配置管理
```
服务注册
curl -X POST 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=nacos.naming.serviceName&ip=20.18.7.10&port=8080'

服务发现
curl -X GET 'http://127.0.0.1:8848/nacos/v1/ns/instance/list?serviceName=nacos.naming.serviceName'

发布配置
curl -X POST "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test&content=HelloWorld"

获取配置
curl -X GET "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test"
```

5.关闭服务器
```
Linux/Unix/Mac
sh shutdown.sh

Windows
shutdown.cmd
```

### Nacos Spring Boot 快速开始

#### 启动配置管理
依赖
```
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-config-spring-boot-starter</artifactId>
    <version>${latest.version}</version>
</dependency>
```

application.propertie配置 Nacos server 的地址
```
nacos.config.server-addr=127.0.0.1:8848
```

使用 @NacosPropertySource 加载 dataId 为 example 的配置源，并开启自动更新：
```
@SpringBootApplication
@NacosPropertySource(dataId = "example", autoRefreshed = true)
public class NacosConfigApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosConfigApplication.class, args);
    }
}
```

通过 Nacos 的 @NacosValue 注解设置属性值。
```
@Controller
@RequestMapping("config")
public class ConfigController {

    @NacosValue(value = "${useLocalCache:false}", autoRefreshed = true)
    private boolean useLocalCache;

    @RequestMapping(value = "/get", method = GET)
    @ResponseBody
    public boolean get() {
        return useLocalCache;
    }
}
```

#### 启动服务发现
依赖
```
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-discovery-spring-boot-starter</artifactId>
    <version>${latest.version}</version>
</dependency>
```

application.propertie配置 Nacos server 的地址和应用名
```
nacos.config.server-addr=127.0.0.1:8848
```

使用 @NacosInjected 注入 Nacos 的 NamingService 实例：
```
@Controller
@RequestMapping("discovery")
public class DiscoveryController {

    @NacosInjected
    private NamingService namingService;

    @RequestMapping(value = "/get", method = GET)
    @ResponseBody
    public List<Instance> get(@RequestParam String serviceName) throws NacosException {
        return namingService.getAllInstances(serviceName);
    }
}

@SpringBootApplication
public class NacosDiscoveryApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosDiscoveryApplication.class, args);
    }
}

```



### Nacos Spring Cloud 快速开始

#### 启动配置管理 
依赖
```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>${latest.version}</version>
</dependency>
```

bootstrap.propertie配置 Nacos server 的地址和应用名
```
spring.cloud.nacos.config.server-addr=127.0.0.1:8848

spring.application.name=example
```

通过 Spring Cloud 原生注解 @RefreshScope 实现配置自动更新：
```
@RestController
@RequestMapping("/config")
@RefreshScope
public class ConfigController {

    @Value("${useLocalCache:false}")
    private boolean useLocalCache;

    @RequestMapping("/get")
    public boolean get() {
        return useLocalCache;
    }
}
```

#### 启动服务发现
依赖
```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>${latest.version}</version>
</dependency>
```

bootstrap.propertie配置 Nacos server 的地址和应用名
```
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848

server.port=8070
spring.application.name=example
```

配置服务提供者
通过 Spring Cloud 原生注解 @EnableDiscoveryClient 开启服务注册发现功能：
```
@SpringBootApplication
@EnableDiscoveryClient
public class NacosProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(NacosProviderApplication.class, args);
	}

	@RestController
	class EchoController {
		@RequestMapping(value = "/echo/{string}", method = RequestMethod.GET)
		public String echo(@PathVariable String string) {
			return "Hello Nacos Discovery " + string;
		}
	}
}
```
启动 ProviderApplication 和 ConsumerApplication ，调用 http://localhost:8080/echo/2018，返回内容为 Hello Nacos Discovery 2018。



### nacos服务添加示例
依赖
```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>${latest.version}</version>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>${latest.version}</version>
</dependency>
```

在 Nacos Spring Cloud 中，dataId 的完整格式如下：
${prefix}-${spring.profiles.active}.${file-extension}
prefix 默认为 spring.application.name 的值，也可以通过配置项 spring.cloud.nacos.config.prefix来配置。


配置：
*在 bootstrap.properties / bootstrap.yml 中配置 Nacos server 的地址和应用名*
```
spring:
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
        # public环境
        namespace:
      config:
        server-addr: 127.0.0.1:8848
        namespace:
```
代码：
```//配置管理
@RestController
@RequestMapping("/config")
@RefreshScope
public class ConfigController {

    @Value("${useLocalCache:false}")
    private boolean useLocalCache;

    @RequestMapping("/get")
    public boolean get() {
        return useLocalCache;
    }
}

//服务发现（服务消费者和提供者都要配置）
@SpringBootApplication
@EnableDiscoveryClient

//服务消费
给 RestTemplate 实例添加 @LoadBalanced 注解，开启 @LoadBalanced 与 Ribbon 的集成：
@LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
//调用服务
    restTemplate.getForObject("http://service-provider/echo/" + str, String.class);
```


### 集群模式部署
集群架构图：
```
          DNS（nacos.com）
                |
          SLB(intranet)
         /      |      \
Nacos(ip1)  Nacos(ip2)  Nacos(ip3)
```

环境准备：
> 64 bit OS Linux/Unix/Mac，推荐使用Linux系统。
> 
> 64 bit JDK 1.8+；下载.配置。
> 
> Maven 3.2.x+；下载.配置。
> 
> 3个或3个以上Nacos节点才能构成集群。

配置集群配置文件
在nacos的解压目录nacos/的conf目录下，有配置文件cluster.conf，请每行配置成ip:port。（请配置3个或3个以上节点）
```
# ip:port
200.8.9.16:8848
200.8.9.17:8848
200.8.9.18:8848
```

确定数据源
```
使用内置数据源
无需进行任何配置

使用外置数据源
生产使用建议至少主备模式，或者采用高可用数据库。
```
初始化 MySQL 数据库
![sql语句源文件](https://github.com/alibaba/nacos/blob/master/distribution/conf/nacos-mysql.sql)
application.properties 配置
![application.properties配置文件](https://github.com/alibaba/nacos/blob/master/distribution/conf/nacos-mysql.sql)


启动服务器
```

Linux/Unix/Mac
Stand-alone mode
sh startup.sh -m standalone

集群模式
使用内置数据源
sh startup.sh -p embedded

使用外置数据源
sh startup.sh
```










