# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Zuul路由网关
zuul 是netflix开源的一个API Gateway 服务器, 本质上是一个web servlet应用。
Zuul 在云平台上提供动态路由，监控，弹性，安全等边缘服务的框架。Zuul 相当于是设备和 Netflix 流应用的 Web 网站后端所有请求的前门。
zuul的例子可以参考 netflix 在github上的 simple webapp，可以按照netflix 在github wiki 上文档说明来进行使用。


### Zuul路由网关创建
1.pom配置
new project(选择
Cloud Discover——Eureka Discoverry 
Web web
Routing zuul

2.启动类注解
@EnableZuulProxy
@EnableEurekaClient

3.配置文件
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
server:
  port: 8769
spring:
  application:
    name: service-zuul
zuul:
  routes:
    api-a:
      path: /api-a/**
      serviceId: service-ribbon
    api-b:
      path: /api-b/**
      serviceId: service-feign
    api-c:
      path: /api-c/**
      url: forward:/api-d	#本地转发
    api-d:
      path: /**
      url: http://legacy.example.com
      
#zuul的负载均衡
ribbon:
  eureka:
    enable: false
service-ribbon:	#这边是ribbon要请求的微服务的serviceId
  ribbon：
    listOfServers: http://localhost:7900,http://localhost:7901

首先指定服务注册中心的地址为http://localhost:8761/eureka/，服务的端口为8769，服务名为service-zuul；以/api-a/ 开头的请求都转发给service-ribbon服务；以/api-b/开头的请求都转发给service-feign服务；

依次运行这五个工程;打开浏览器访问：http://localhost:8769/api-a/hi?name=forezp ;浏览器显示：

hi forezp,i am from port:8762

打开浏览器访问：http://localhost:8769/api-b/hi?name=forezp ;浏览器显示：

hi forezp,i am from port:8762

这说明zuul起到了路由的作用

4.服务过滤
@Component
public class MyFilter extends ZuulFilter 

5.负载均衡

6.zuul 本地转发
