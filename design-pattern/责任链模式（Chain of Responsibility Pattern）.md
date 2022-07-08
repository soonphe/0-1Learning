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
步骤如下：
1. 分别定义不同的业务放在Filter中实现
2. 定义pipline去编排Filter该如何执行
3. 定义上下文对象context用于保存入参、过程值、出参
4. 
虚拟类AbstractOrderFilter判断责任链Seletor处理（doFilter，handle）
DefaultFilterChain责任链判断实现

#### 1. FilterChain责任链接口（用于处理所有的业务）
```
public interface IFilter<T extends IFilterContext> {

  /**
   * Filter 唯一方法,执行过滤器任务
   * @param context Filter上下文对象
   */
  FilterHandleResultEnum doFilter(T context);
}
```

#### 2. FilterChainNode链式接口实现（用于控制所有的业务执行）

```
public interface IFilterChainNode<T extends IFilterContext> {

  /**
   * 业务逻辑处理
   * @param context Filter上下文对象
   */
  void handle(T context);

  /**
   * 执行下一个Filter
   * @param context Filter上下文对象
   */
  void handleNext(T context);

}
```

链式接口默认实现方案：Filter放入节点中，每个节点都有指向下一个节点FilterChainNode
- 这里的handler方法判断了selector，用于选择是否执行当期那Filter
- 同时判断了Filter的返回值，用于选择是否继续链式执行或者跳出当前执行链。（如果不需要返回值也可以去掉）
```
/**
 * Filter Chain 链式接口默认实现
 *
 * @author soonphe
 * @since 1.0
 */
public class DefaultFilterChainNode<T extends IFilterContext> implements IFilterChainNode<T>{

  /**
   * chain名称
   */
  String chainName = "";
  /**
   * Filter Chain 下一个节点
   */
  private IFilterChainNode<T> next;
  /**
   * Filter Chain 当前接口节点Filter对象
   */
  private IFilter<T> filter;

  public DefaultFilterChainNode(IFilterChainNode chain, IFilter filter){
    this.next = chain;
    this.filter = filter;
  }

  public DefaultFilterChainNode(String chainName, IFilterChainNode chain, IFilter filter){
    this.chainName = chainName;
    this.next = chain;
    this.filter = filter;
  }

  public void setChainName(String name){
    this.chainName = name;
  }

  public String getChainName(){
    return this.chainName;
  }

  @Override
  public void handle(T context) {
    FilterHandleResultEnum resultEnum = FILTER_HANDLE_RESULT_FAIL;
    if(context.getFilterSelector().matchFilter(this.filter.getClass().getSimpleName())) {
      resultEnum = filter.doFilter(context);
    }else{
      resultEnum = FILTER_HANDLE_RESULT_NOT_MATCH;
      context.getNotMatchSuccessFilters().add(this.filter.getClass().getSimpleName());
    }
    if (FILTER_HANDLE_RESULT_SUCCESS.getCode().equals(resultEnum.getCode())){
      context.getExecutedSuccessFilters().add(this.filter.getClass().getSimpleName());
    }
    if (FILTER_HANDLE_RESULT_FAIL.getCode().equals(resultEnum.getCode())){
      context.getExecutedFailFilters().add(this.filter.getClass().getSimpleName());
    }
    if(!FILTER_HANDLE_RESULT_BREAK_CHAIN.getCode().equals(resultEnum.getCode())){
      this.handleNext(context);
    }
  }

  @Override
  public void handleNext(T ctx) {
    IFilterChainNode nextChain = this.next;
    if(Objects.nonNull(nextChain)){
      nextChain.handle(ctx);
    }
  }
}
```
#### 3. pipline（用于编排Filter业务）
```java
public class FilterChainPipeline<T extends IFilter> {

  public DefaultFilterChainNode getFilterChain() {
    return last;
  }

  private DefaultFilterChainNode last;

  /**
   * Filter Chain 添加Filter
   * @param filter filter对象
   * @return
   */
  public FilterChainPipeline addFilter(T filter) {
    DefaultFilterChainNode newChain = new DefaultFilterChainNode(last, filter);
    last = newChain;
    return this;
  }

  /**
   * Filter Chain 添加Filter
   * @param desc filter描述
   * @param filter filter对象
   * @return
   */
  public FilterChainPipeline addFilter(String desc, T filter) {
    DefaultFilterChainNode newChain = new DefaultFilterChainNode(desc, last, filter);
    last = newChain;
    return this;
  }

}
```

#### 4. 构造seletor选择执行责任链（可选）
```
public interface IFilterSelector {

  /**
   * filter 匹配
   * @param currentFilterName
   * @return
   */
  boolean matchFilter(String currentFilterName);

  /**
   * 获取所有的filterNames
   * @return
   */
  List<String> getFilterNames();

}

public class DefaultFilterSelector implements IFilterSelector{

  /**
   * Filter名称List
   */
  private List<String> filterNames = new ArrayList<>();

  @Override
  public boolean matchFilter(String classSimpleName) {
    if (Objects.nonNull(filterNames)||filterNames.size()>0){
      return filterNames.stream().anyMatch(s -> Objects.equals(s,classSimpleName));
    }
    return true;
  }

  @Override
  public List<String> getFilterNames() {
    return filterNames;
  }

  public void addFilter(String clsNames){
    filterNames.add(clsNames);
  }

  public void addFilters(List<String> filterNames){
    filterNames.addAll(filterNames);
  }

  public DefaultFilterSelector(){}

  /**
   * 需要实例化，传入list以实例化Selector选择器
   * @param filterNames
   */
  public DefaultFilterSelector(List<String> filterNames){
    this.filterNames = filterNames;
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

#### 5. context对象
```
public interface IFilterContext {

  /**
   * 获取过滤器选择器
   * @return
   */
  IFilterSelector getFilterSelector();

  /**
   * 获取chain执行成功的Filter Name list
   * @return
   */
  List<String> getExecutedSuccessFilters();

  /**
   * 获取chain执行失败的Filter Name list
   * @return
   */
  List<String> getExecutedFailFilters();

  /**
   * 获取chain执行不满足条件的Filter Name list
   * @return
   */
  List<String> getNotMatchSuccessFilters();



}

public abstract class AbstractFilterContext implements IFilterContext {

  /**
   * Selector选择执行Filter对象
   */
  private IFilterSelector selector;
  /**
   * 执行成功Filter对象
   */
  private List<String> successFilters;
  /**
   * 不满足执行条件Filter对象
   */
  private List<String> notMatchFilters;
  /**
   * 执行失败Filter对象
   */
  private List<String> failFilters;

  public AbstractFilterContext(IFilterSelector selector) {
    this.selector = selector;
    this.successFilters = new ArrayList<>();
    this.failFilters = new ArrayList<>();
    this.notMatchFilters = new ArrayList<>();
  }

  @Override
  public IFilterSelector getFilterSelector() {
    return selector;
  }

  public void setFilterSelector(IFilterSelector filterSelector) {
    this.selector = filterSelector;
  }

  @Override
  public List<String> getExecutedSuccessFilters(){
    return successFilters;
  }

  @Override
  public List<String> getExecutedFailFilters(){
    return failFilters;
  }

  @Override
  public List<String> getNotMatchSuccessFilters(){
    return notMatchFilters;
  }
}

```

#### 6. provider对象
```
public interface IProvider {

  /**
   * 执行filter
   * @param context 上下文对象
   */
  AbstractFilterContext handleFilter(AbstractFilterContext context);

  /**
   * 执行filter
   * @param context 上下文对象
   * @param filterChainPipeline pipline
   * @return
   */
  AbstractFilterContext handleFilter(AbstractFilterContext context, FilterChainPipeline filterChainPipeline);

  /**
   * 执行filter
   * @param context 上下文对象
   * @param filterChainPipeline pipline
   * @param selector selector
   * @return
   */
  AbstractFilterContext handleFilter(AbstractFilterContext context, FilterChainPipeline filterChainPipeline, DefaultFilterSelector selector);


}


public class DefaultProvider implements IProvider{

  private final FilterChainPipeline pipeline;

  public DefaultProvider() {
    this.pipeline = null;
  }

  public DefaultProvider(FilterChainPipeline pipeline) {
    this.pipeline = pipeline;
  }

  @Override
  public AbstractFilterContext handleFilter(AbstractFilterContext context){
    if (Objects.nonNull(pipeline)){
      pipeline.getFilterChain().handle(context);
    }
    return context;
  }

  @Override
  public AbstractFilterContext handleFilter(AbstractFilterContext context, FilterChainPipeline filterChainPipeline){
    filterChainPipeline.getFilterChain().handle(context);
    return context;
  }

  @Override
  public AbstractFilterContext handleFilter(AbstractFilterContext context, FilterChainPipeline filterChainPipeline, DefaultFilterSelector selector){
    context.setFilterSelector(selector);
    filterChainPipeline.getFilterChain().handle(context);
    return context;
  }

}
```

#### 7. 责任链初始化
```
  public FilterChainPipeline getFilterChainPipeline() {
    FilterChainPipeline filterChainPipeline = new FilterChainPipeline();
    // TODO: 添加filter
    filterChainPipeline.addFilter("ONE", new OneFilter());
    filterChainPipeline.addFilter("TWO", new TwoFilter());
    filterChainPipeline.addFilter("THREE",new ThreeFilter());
    return filterChainPipeline;
  }
```
补充：也可以使用new方法添加，添加对应的ChainNode放入pipline中

#### 8. 执行责任链
```
  public void testProvider(){
    List<String> filterNames = new ArrayList<>();
    filterNames.add("OneFilter");
//    filterNames.add("TwoFilter");
    filterNames.add("ThreeFilter");
    // 1.构造selector
    DefaultFilterSelector defaultFilterSelector = new DefaultFilterSelector(filterNames);
    // 2.构造context
    TestFilterContext testFilterContext = new TestFilterContext(BizEnum.BIZ_ENUM_ONE, defaultFilterSelector);
    // 3.构造pipline
    FilterChainPipeline filterChainPipeline = new TestPipeline().getFilterChainPipeline();
    // 4.构造provider（无参/有参构造）
    DefaultProvider defaultProvider = new DefaultProvider();
    // 5.执行pipline
    AbstractFilterContext abstractFilterContext = defaultProvider.handleFilter(testFilterContext, filterChainPipeline);

    System.out.println("All:" + abstractFilterContext.getFilterSelector().getFilterNames().size());
    System.out.println("Success:" + abstractFilterContext.getExecutedSuccessFilters().size());
    System.out.println("Fail:" + abstractFilterContext.getExecutedFailFilters().size());

  }
```

### 简单的链式编程
这个例子也是一种责任链思路
```
public interface Flow<I, O> {

    O execute(I input);

}

public interface Stage<I,O,C> {

    void process(Pipeline<I, O, C> pipeline);

}

@Order(1)
@Component
public class AStage implements Stage<Request, Response, Context> {

    @Override
    public void process(Pipeline<Request, Response, Context> pipeline) {
        System.out.println("A stage execute...");
    }
}

@Order(2)
@Component
public class BStage implements Stage<Request, Response, Context> {

    @Override
    public void process(Pipeline<Request, Response, Context> pipeline) {
        System.out.println("B stage execute...");
    }
}

@Component
public class OrderService implements Flow<Request, Response> {

    @Resource
    private List<Stage<Request, Response, Context>> stageList;

    @Override
    public Response execute(Request input) {
        Pipeline<Request, Response, Context> pipeline = new Pipeline<>();
        pipeline.setParam(input);
        pipeline.setContext(new Context());
        pipeline.setResult(new Response());

        for (Stage<Request, Response, Context> stage : stageList) {
            stage.process(pipeline);
        }

        return pipeline.getResult();
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