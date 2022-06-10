# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 责任链模式

责任链模式（Chain of Responsibility Pattern）为请求创建了一个接收者对象的链。这种模式给予请求的类型，对请求的发送者和接收者进行解耦。这种类型的设计模式属于行为型模式。

在这种模式中，通常每个接收者都包含对另一个接收者的引用。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个接收者，依此类推。

### 使用场景
职责链上的处理者负责处理请求，客户只需要将请求发送到职责链上即可，无须关心请求的处理细节和请求的传递，所以职责链将请求的发送者和请求的处理者解耦了。
例如： 1、红楼梦中的"击鼓传花"。 2、JS 中的事件冒泡。 3、JAVA WEB 中 Apache Tomcat 对 Encoding 的处理，Struts2 的拦截器，jsp servlet 的 Filter。

### 优缺点
责任链模式的优缺点
优点：
```
降低了请求的发送者和接收者之间的耦合。
把多个条件判定分散到各个处理类中，使得代码更加清晰，责任更加明确。
```
缺点：
```
在找到正确的处理对象之前，所有的条件判定都要执行一遍，当责任链过长时，可能会引起性能的问题
可能导致某个请求不被处理。
责任链模式的适用场景
```

### 实现方式
```
步骤 1
创建抽象的记录器类。

AbstractLogger.java
public abstract class AbstractLogger {
   public static int INFO = 1;
   public static int DEBUG = 2;
   public static int ERROR = 3;
 
   protected int level;
 
   //责任链中的下一个元素
   protected AbstractLogger nextLogger;
 
   public void setNextLogger(AbstractLogger nextLogger){
      this.nextLogger = nextLogger;
   }
 
   public void logMessage(int level, String message){
      if(this.level <= level){
         write(message);
      }
      if(nextLogger !=null){
         nextLogger.logMessage(level, message);
      }
   }
 
   abstract protected void write(String message);
   
}
步骤 2
创建扩展了该记录器类的实体类。

ConsoleLogger.java
public class ConsoleLogger extends AbstractLogger {
 
   public ConsoleLogger(int level){
      this.level = level;
   }
 
   @Override
   protected void write(String message) {    
      System.out.println("Standard Console::Logger: " + message);
   }
}
ErrorLogger.java
public class ErrorLogger extends AbstractLogger {
 
   public ErrorLogger(int level){
      this.level = level;
   }
 
   @Override
   protected void write(String message) {    
      System.out.println("Error Console::Logger: " + message);
   }
}
FileLogger.java
public class FileLogger extends AbstractLogger {
 
   public FileLogger(int level){
      this.level = level;
   }
 
   @Override
   protected void write(String message) {    
      System.out.println("File::Logger: " + message);
   }
}
步骤 3
创建不同类型的记录器。赋予它们不同的错误级别，并在每个记录器中设置下一个记录器。每个记录器中的下一个记录器代表的是链的一部分。

ChainPatternDemo.java
public class ChainPatternDemo {
   
   private static AbstractLogger getChainOfLoggers(){
 
      AbstractLogger errorLogger = new ErrorLogger(AbstractLogger.ERROR);
      AbstractLogger fileLogger = new FileLogger(AbstractLogger.DEBUG);
      AbstractLogger consoleLogger = new ConsoleLogger(AbstractLogger.INFO);
 
      errorLogger.setNextLogger(fileLogger);
      fileLogger.setNextLogger(consoleLogger);
 
      return errorLogger;  
   }
 
   public static void main(String[] args) {
      AbstractLogger loggerChain = getChainOfLoggers();
 
      loggerChain.logMessage(AbstractLogger.INFO, "This is an information.");
 
      loggerChain.logMessage(AbstractLogger.DEBUG, 
         "This is a debug level information.");
 
      loggerChain.logMessage(AbstractLogger.ERROR, 
         "This is an error information.");
   }
}
步骤 4
执行程序，输出结果：

Standard Console::Logger: This is an information.
File::Logger: This is a debug level information.
Standard Console::Logger: This is a debug level information.
Error Console::Logger: This is an error information.
File::Logger: This is an error information.
Standard Console::Logger: This is an error information.
```

### 责任链实现：

虚拟类AbstractOrderFilter判断责任链Seletor处理（doFilter，handle）
DefaultFilterChain责任链判断实现

1.FilterChain责任链接口
```
//OrderFilter作为业务处理
public interface OrderFilter<T extends OrderContext> {
  void doFilter(T var1, OrderFilterChain var2);
}

//OrderFilterChain责任链式处理
public interface OrderFilterChain<T extends OrderContext> {
  void handle(T var1);

  void fireNext(T var1);
}
```
2.实现责任链接口
```
public class DefaultFilterChain<T extends OrderContext> implements OrderFilterChain<T> {
  //下一个filterChain引用
  private OrderFilterChain<T> next;
  //filter业务处理
  private OrderFilter<T> filter;

  public DefaultFilterChain(OrderFilterChain chain, OrderFilter filter) {
    this.next = chain;
    this.filter = filter;
  }

  public void handle(T context) {
    this.filter.doFilter(context, this);
  }

  public void fireNext(T ctx) {
    OrderFilterChain nextChain = this.next;
    if (Objects.nonNull(nextChain)) {
      nextChain.handle(ctx);
    }

  }
}
```
3.责任链初始化
```
//第一种形式使用Pipline链式添加
public class FilterChainPipeline<T extends OrderFilter> {
  private DefaultFilterChain last;

  public FilterChainPipeline() {
  }

  public DefaultFilterChain getFilterChain() {
    return this.last;
  }

  public FilterChainPipeline addFilter(T filter) {
    DefaultFilterChain newChain = new DefaultFilterChain(this.last, filter);
    this.last = newChain;
    return this;
  }

  public FilterChainPipeline addFilter(String desc, T filter) {
    DefaultFilterChain newChain = new DefaultFilterChain(this.last, filter);
    this.last = newChain;
    return this;
  }
}
//添加引用pipline
FilterChainPipeline pipeline = new FilterChainPipeline();
pipeline.addFilter("创建订单附表", orderExtensionAuthFilter(syncService, meterRegistry))
	.addFilter...
return new IotCuoHePreAuthProvider(pipeline.getFilterChain(), iotCuoHeAuthReqConverter,
	producerServer, redisServer);

//第二种形式
//1.直接使用filterChain链式添加
FilterChain filterChain7=new DefaultFilterChain(null, orderExtensionAuthFilter);
FilterChain filterChain6=new DefaultFilterChain(filterChain7, orderExtensionAuthFilter);
return new IotCuoHePreAuthProvider(filterChain1, iotCuoHeAuthReqConverter, producerServer, redisServer);

```
4. 添加seletor选择执行责任链
```
//Filter选择器
public interface FilterSelector {
  //匹配Filter
  boolean matchFilter(String var1);

  List<String> getFilterNames();
}

//Filter选择器实现
public class LocalListBasedFilterSelector implements FilterSelector {
  private List<String> filterNames = Lists.newArrayList();

  public LocalListBasedFilterSelector() {
  }

  public boolean matchFilter(String classSimpleName) {
    return this.filterNames.stream().anyMatch((s) -> {
      return Objects.equals(s, classSimpleName);
    });
  }

  public List<String> getFilterNames() {
    return this.filterNames;
  }

  public void addFilter(String clsNames) {
    this.filterNames.add(clsNames);
  }
}

//实际业务使用
public class FeeItemDomainService {

  private final FilterChainPipeline orderFilterChainPipeline;

  public FeeItemDomainService(
      FilterChainPipeline orderFilterChainPipeline) {
    this.orderFilterChainPipeline = orderFilterChainPipeline;
  }

  /**
   * 构造context对象
   * 
   * @param request
   * @return
   */
  public FeeItemResult getFeeItemResult(OrderCreateRequest request){
    CalculateContext calculateContext = new CalculateContext(BizEnum.OVER_TIME_ORDER,feeFilter(),request);
    orderFilterChainPipeline.getFilterChain().handle(calculateContext);
    return calculateContext.getFeeItemResult();
  }

  /**
   * 构造filter选择器
   *
   * @param request
   * @return
   */
  private LocalListBasedFilterSelector feeFilter(){
    LocalListBasedFilterSelector selector = new LocalListBasedFilterSelector();
    selector.addFilter(OverTimeFeeItemCalculateFilter.class.getSimpleName());
    selector.addFilter(RequestSaveFilter.class.getSimpleName());
    return selector;
  }

}
```
5. context对象
```
//通用Context对象
public interface OrderContext {
  BizEnum getBizCode();

  FilterSelector getFilterSelector();

  List<String> getDynamicCloseFilters();
}

public abstract class AbstractOrderContext implements OrderContext {
  private final BizEnum bizEnum;
  private final FilterSelector selector;

  public AbstractOrderContext(BizEnum bizEnum, FilterSelector selector) {
    this.bizEnum = bizEnum;
    this.selector = selector;
  }

  public BizEnum getBizCode() {
    return this.bizEnum;
  }

  public FilterSelector getFilterSelector() {
    return this.selector;
  }
}
```

### Spring中的责任链
参考文档：https://blog.csdn.net/ljw761123096/article/details/79591133

#### DispatcherServlet
DispatcherServlet是Spring MVC的核心，处理请求的主要逻辑在方法doDispatch(request, response):
通过doDispatch(request, response)源码，我们可以清晰看到拦截器和controler具体实现原理和流程：
1）、获取HandlerExecutionChain，HandlerExecutionChain是对Controller和它的Interceptors的进行了包装。
2）、获取HandlerAdapter，即Handler对应的适配器。
3）、执行拦截器preHandle() 方法
4）、由HandlerAdapter的handle方法执行Controller的handleRequest,并且返回一个ModelAndView
5）、执行拦截器postHandle方法
6）、执行ModelAndView的render(mv, request, response)方法把合适的视图展现给用户;
7）、执行拦截器afterCompletion() 方法