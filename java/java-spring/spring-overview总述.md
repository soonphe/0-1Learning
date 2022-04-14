# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## spring-overview总述

### 如何在spring启动之后做一些操作
我们可能会有这样的需求：要在spring启动之后初始化一些bean，或者自动运行一些业务代码。

比方说，spring启动之后设置nacos端口并注册服务，定时任务的触发等。

#### 实现方式：implements CommandLineRunner {
包名：package org.springframework.boot;
执行时机：springboot 完全初始化完毕后
```
@Component
public class DataCleanElasticJobCommandRunner implements CommandLineRunner {

    @Override
    public void run(String... args) {
        log.info("启动任务");
    }
}
```

#### 实现方式：implements ApplicationRunner {
- 包名：package org.springframework.boot;
- 执行时机：springboot 完全初始化完毕后
- CommandLineRunner和ApplicationRunner区别：参数不同

- 可以添加 @Order 注解等注解：指定执行的顺序 (数字小的优先)
  - 先根据 @Order 注解的值，@Order 的 value 值越小，则优先，没有该注解则默认最后执行。
  - @Order 值相同时，ApplicationRunner 优先于 CommandLineRunner。

```
@Component
public class ApplicationRunAfter implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) {
        log.info("启动任务");
    }
}
```

#### 实现方式： implements ApplicationListener<ApplicationReadyEvent> {
- 包名：package org.springframework.context;
- 执行时机：在 IOC 容器的启动过程，当所有的 bean 都已经处理完成之后，spring 的 ioc 容器会有一个发布 ContextRefreshedEvent 事件的动作。

拓展：
- spring-context事件 
  - ContextRefreshedEvent：ApplicationContext 被初始化或刷新时，该事件被发布。这也可以在 ConfigurableApplicationContext 接口中使用 refresh() 方法来发生。此处的初始化是指：所有的 Bean 被成功装载，后处理 Bean 被检测并激活，所有 Singleton Bean 被预实例化，ApplicationContext 容器已就绪可用。
  - ContextStartedEvent：当使用 ConfigurableApplicationContext （ApplicationContext子接口）接口中的 start() 方法启动 ApplicationContext 时，该事件被发布。你可以调查你的数据库，或者你可以在接受到这个事件后重启任何停止的应用程序。
  - ContextStoppedEvent：当使用 ConfigurableApplicationContext 接口中的 stop() 停止 ApplicationContext 时，发布这个事件。你可以在接受到这个事件后做必要的清理的工作。
  - ContextClosedEvent：当使用 ConfigurableApplicationContext 接口中的 close() 方法关闭 ApplicationContext 时，该事件被发布。一个已关闭的上下文到达生命周期末端；它不能被刷新或重启。
  - RequestHandledEvent：这是一个 web-specific 事件，告诉所有 bean HTTP 请求已经被服务。只能应用于使用DispatcherServlet的Web应用。在使用Spring作为前端的MVC控制器时，当Spring处理用户请求结束后，系统会自动触发该事件。
- spring-boot事件
  - ApplicationContextInitializedEvent
  - ApplicationEnvironmentPreparedEvent
  - ApplicationFailedEvent
  - ApplicationPreparedEvent
  - ApplicationReadyEvent
  - ApplicationStartedEvent
  - ApplicationStartingEvent
  - EventPublishingRunListener
  - SpringApplicationEvent

- 优先级：
  - ContextRefreshedEvent > ApplicationRunner > ApplicationReadyEvent
  - 相同ApplicationEvent事件，注解方式 优先于 代码方式

代码方式实现：
```
@Component
public class NacosListener implements ApplicationListener<ApplicationReadyEvent> {
    @Autowired
    private NacosRegistration registration;

    @Autowired(required = false)
    private NacosAutoServiceRegistration nacosAutoServiceRegistration;

    @Override
    public void onApplicationEvent(ApplicationReadyEvent event) {
        System.out.println("加载onApplicationEvent");
    }
}
```
注解方式实现：
```
    @EventListener(ApplicationReadyEvent.class)
    public void onWebServerReady(ApplicationReadyEvent event) {
        System.out.println("加载onWebServerReady");
    }
```