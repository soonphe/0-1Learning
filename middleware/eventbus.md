# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## eventbus

### springboot使用eventbus：
事件驱动：
```
Google-Eventbus
        // EventBus对象创建
        EventBus eventBus = new EventBus("test");
        // 注册监听者（监听者@Subscribe 订阅时间）
        eventBus.register(new OrderEventListener());
        // 发布消息
        eventBus.post(new OrderMessage());
// 异步事件消息处理
EventBusbus=newAsyncEventBus(threadPoolExecutor);
```

1.依赖
```
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>28.1-jre</version>
</dependency>
```
2.EventBusConfig
```
/**
 * 事件监听配置
 * 
 * @author soonphe
 * @since 1.0
 */
@Configuration
public class EventBusConfig {

  /**
   * eventbus注册异步监听
   * @param eventListener
   * @return
   */
  @Bean
  public EventBus eventBus(AsyncEventListener eventListener) {
    Builder builder = new Builder().namingPattern("event-bus-threads");
    //参数：corePoolSize，maximumPoolSize，keepAliveTime，TimeUnit，BlockingQueue等待对了
    ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10, 10, 60L,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(100), builder.build());
    //同步
    //EventBus eventBus = new EventBus();
    //异步
    EventBus bus = new AsyncEventBus(threadPoolExecutor);
    bus.register(eventListener);
    return bus;
  }
}
```
3.事件监听类
```
@Slf4j
@Component
@AllArgsConstructor
public class AsyncEventListener {

/**
   * 监听操作日志时间
   * @param event
   */
  @Subscribe
  public void saveOperationRecordEvent(SaveOverTimeOperationRecordEvent event) {
    OverTimeOperationRecordDto operationRecordDto = OverTimeOperationRecordMapper.INSTANCE.eventToDto(event);
    try {
      Long record = operationService.createOverTimeOperationRecord(operationRecordDto);
      log.info("---event  新增操作记录成功! 记录id为{}", record);
    } catch (Exception e) {
      log.error("---event  新增操作记录表出现异常,异常信息为", e);
    }
  }
}
```
4.事件bean
```
@Getter
@Builder
public class SaveOverTimeOperationRecordEvent extends OverTimeOperationRecord {

  private Long operator;
  private String operatorAccount;
  private Integer operatorType;
  private Long operationTableId;
  private Integer operatorChannel;

}
```
5.注入eventbus，post测试发送事件
```
  @Autowired
  private EventBus eventBus;

  eventBus.post(overTimeFeeModelDeleteEvent);
```
备注：还可以定义一层handle处理eventbus的注册和注销操作
```java
@Component
@Slf4j
public class EventHandler {

    @Autowired
    private EventBus eventBus;

    @Autowired
    private EventListener eventListener;

    @PostConstruct
    public void init() {
        eventBus.register(eventListener);
    }

    @PreDestroy
    public void destroy() {
        eventBus.unregister(eventListener);
    }

    public void eventPost(){
        eventBus.post("test.md");
        log.info("post event");
    }
}
```
