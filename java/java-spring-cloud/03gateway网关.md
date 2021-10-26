# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## gateWay网关
Cloud 全家桶中有个很重要的组件就是网关，在1.x版本中都是采用的Zuul网关；但在2.x版本中，zuul的升级一直跳票，SpringCloud最后自己研发了一个网关替代Zuul，那就是Spring Cloud Gateway

Gateway是在Spring 生态系统之上构建的API网关服务，基于Spring 5，SpringBoot 2和Project Reactor等技术。Gateway旨在提供一种简单而有效的方式来对API进行路由，以及提供一些强大的过滤器功能，例如：熔断、限流、重试等。

一句话：Spring Cloud Gateway使用的是Webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架。


### 功能：
反向代理
鉴权
流量控制
熔断
日志监控。。。

### SpringCloud Gateway 与 zuul 的区别
在SpringCloud Finchley 正式版之前，SpringCloud推荐的网关是Netflix提供的Zuul：
1、Zuul 1.x是一个基于阻塞 I/O 的API Gateway
2、Zuul 1.x基于servlet 2.5使用阻塞架构它不支持任何长连接（如websocket）Zuul的设计模式和Nginx较像，每次I/O 操作都是从工作线程中选择一个执行，请求线程被阻塞到工作线程完成，但是差别是Nginx用C++实现，Zuul用Java实现，而JVM本身会有一次加载较慢的情况，使得zuul的性能相对较差
3、Zuul 2.x理念更先进，向基于Netty非阻塞和支持长连接，但SpringCloud目前还没有整合。Zuul 2.x的性能较Zuul 1.x有较大提升。在性能方面，根据官方提供的基准测试，SpringCloud Gateway的RPS（每秒请求数）是Zuul的1.6倍
4、SpringCloud Gateway建立在Spring Framework5、Project Reactor和Spring Boot 2之上，使用非阻塞API
5、SpringCloud Gateway还支持WebSocket，并且与Spring紧密集成用于更好的开发体验

### 核心概念：
```
spring:
  application:
    name: gateway-service
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true                 # 启用自动根据服务ID生成路由
          lower-case-service-id: true   # 设置路由的路径为小写的服务ID
      routes:
        - id: order-service         #路由的ID，没有固定规则但要求唯一，简易配合服务名
          uri: lb://order-service   #匹配后提供服务的路由地址
          predicates:
            - Path=/api/order/**    #断言，路径相匹配的进行路由
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/user/**
```
- Route(路由)：路由是构建网关的基本模块，它由ID、目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

- Predicate(断言)：参考的是Java8的java.util.function.Predicate
开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数)，如果请求与断言相匹配则进行路由

- Filter(过滤)：指的是Spring框架中GatewayFilyter的实例，使用过滤器，可以在请求被路由前或者之后进行修改


### route中的uri配置类型
在gateway中配置uri配置有三种方式，包括
第一种：ws(websocket)方式: uri: ws://localhost:9000
第二种：http方式: uri: http://localhost:8130/
第三种：lb(注册中心中服务名字)方式: uri: lb://brilliance-consumer

### Gateway 常用的 Predicate
一般来说：path 只是predicate 中的一个，只要使predicates为true 就访问，false不访问。

其他配置
After、Before、Between和Cookie和Header和Host
```
- Path=/payment/**      #断言，路径相匹配
- After=2020-03-15      #在时间之后
- Cookie=token,zzyy     #携带cookie
- Header= X-Request-Id, \d+     #请求头要有X-Request-Id属性并且值为整数的正则表达式
- Host=**.atgui.com     #校验host
- Method=GET            #必须为GET方法
- Query=username, \d+   #必须要有参数为username并且值为整数
```

### Gateway的 Filter
SpringCloud Gateway内置了多种路由过滤器，他们都由GatewayFilter的工厂类来生成

SpringCloud Gateway的 Filter，生命周期有 pro 和 post，
种类有GatewayFilter 和 GlobalFilter
常用的GatewayFilter 有31种之多

自定义全局GlobalFilter

需要实现两个接口：GlobalFilter、Ordered
能做什么？

全局日志记录
统一网关鉴权

实现代码
```
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("***********come in MyLogGateWayFilter: "+new Date());
        String uname = exchange.getRequest().getQueryParams().getFirst("uname");//每次进来后判断带不带uname这个key
        if(uname == null){
            log.info("*********用户名为null ，非法用户，o(╥﹏╥)o");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);    //uname为null非法用户
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }
    @Override
    public int getOrder() {
        return 0;
    }
}
```









