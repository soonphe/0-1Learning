# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java常用注解

### 依赖注入方式
    依赖注入的方式：  
    1.constructor-arg：通过构造函数注入。   
    2.property：通过setxx方法注入。
---

### 常用注解说明:
如果组件采用xml的bean定义来配置，显然会增加配置文件的体积，查找以及维护起来也不太方便。
Spring2.5为我们引入了组件自动扫描机制，他可以在类路径底下寻找标注了@Component,@Service,@Controller,@Repository注解的类，并把这些类纳入进spring容器中管理

* @Configuration：用@Configuration注解该类，等价 与XML中配置beans；用@Bean标注方法等价于XML中配置bean。
* @Repository用于标注数据访问组件，即DAO组件
* @Service用于标注业务层组件，
* @Controller用于标注控制层组件（如struts中的action）
* @Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注
* @Autowired装配bean,即引入一个java bean（由于@Autowired 默认第一按照byType(类的类型),第二byName(l类名\类ID)来加载类，所以当存在类型相同,多个beanname时，想注入某个类，就必须指定根据什么beanName查找（使用@Qualifier注解指定），如果不用@Qualifier注解指定，则会以变量名为为beanName进行查找；）
* @Resource的作用相当于@Autowired，只不过@Autowired按byType自动注入，而@Resource默认按 byName自动注入罢了
* spring aop中：
    * @Around ：是在所拦截方法执行之前执行一段逻辑
    * @Before ：是在所拦截方法执行之后执行一段逻辑
    * @After  ：是可以同时在所拦截方法的前后执行一段逻辑。
    
* @ServletComponentScan: 
Servlet、Filter、Listener 可以直接通过 @WebServlet、@WebFilter、@WebListener 注解自动注册，这样通过注解servlet ，拦截器，监听器的功能而无需其他配置

* @MapperScan: 
spring-boot支持mybatis组件的一个注解，通过此注解指定mybatis接口类的路径，即可完成对mybatis接口的扫描。

* @ImportResource @Import @PropertySource 
资源导入注解，这三个注解都是用来导入自定义的一些配置文件。

- @Required：注解检查 但他只检查属性是否已经设置而不会测试属性是否非空
- @Primary：优先加载
- @Qualifier：注入bean时，以变量名为为beanName进行查找

#### @Autowired实现原理
简单来说：重点讲几个方面
- 反射：java的注解实现的核心技术是反射。
- @Autowired注解用途：应用于构造函数，@Autowired默认根据类型注入，通过类型装配，如果集合是一个的话，就是这个了，如果多个，根据name再过滤一次，如果没匹配就抛异常了，spring是多个bean可以是同一个类，但是名字不能相同。
- ioc部分内容（这部分太长省略了）
- jvm虚拟机栈和虚拟机使用到的本地方法栈

IOC重点：
- Spring对autowire注解的实现逻辑位于：后置处理器AutowiredAnnotationBeanPostProcessor，基本上所有spring的特性都是由相应的后置处理器实现的。
  - AutowiredAnnotationBeanPostProcessor中的方法：postProcessMergedBeanDefinition()方法会对标注了@Autowired进行预处理，然后调用postProcessProperties()进行注入，这里分两步，预处理和真正注入
- getbean流程：
  - 通过反射获取该类所有的字段，并遍历每一个字段，并通过方法findAutowiredAnnotation遍历每一个字段的所用注解，并如果用autowired修饰了，则返回auotowired相关属性，用@Autowired修饰的注解可能不止一个，因此都加在currElements这个容器里面，一起处理。有了目标类，与所有需要注入的元素集合之后，我们就可以实现autowired的依赖注入逻辑了
  - 它调用的方法是InjectionMetadata中定义的inject方法(逻辑就是遍历，然后调用inject方法)
  - inject也使用了反射技术并且依然是分成字段和方法去处理的。在代码里面也调用了makeAccessible这样的可以称之为暴力破解的方法，但是反射技术本就是为框架等用途设计的，这也无可厚非。
  - 获取注入点：populateBean方法以及后续后置处理器的执行。中间夹杂着三级缓存一起说


#### @Autowired 与@Resource的区别：
    1、 @Autowired与@Resource都可以用来装配bean.都可以写在字段上,或写在setter方法上。
    2、 @Autowired默认按类型装配
    3、 @Resource（这个注解属于J2EE的），默认安装名称进行装配

#### @Controller和@RestController的区别
~~~~
官方文档：
@RestController is a stereotype annotation that combines @ResponseBody and @Controller.
意思是：
@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。

如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
例如：本来应该到success.jsp页面的，则其显示success.
~~~~

#### @service，@component，@repository区别
@component是通用性的注解，@service 和@repository则是在@component的基础上添加了特定的功能。

所以@component可以替换为@service和@repository，但是为了规范，服务层bean用@service，dao层用@repository。就好比代码规范，变量、方法命名一样。还有一点，正如文档描述那样：

@Repository的工作是捕获特定于平台的异常，并将它们作为Spring统一未检查异常的一部分重新抛出。为此，提供了PersistenceExceptionTranslationPostProcessor。

如果在dao层使用@service，就不能达到这样的目的。



### springboot注解
@SpringBootApplication复合注解
```
1. @SpringBootConfiguration Extends Configuration：标注当前类为配置类
2. @EnableAutoConfiguration ：自动配置注解，Spring会根据你添加的jar包来配置项目的默认设置
3. @ComponentScan：扫描当前包及其子包下被@Component，@Controller，@Service，@Repository
注：@Controller @Service @Repository是@Component的子注解
自动装配原理：将META-INF/spring.factories里面配置的所有EnableAutoConfiguration的值加入溶氧仪
```

### 属性注解
* @DateTimeFormat(pattern="HH:mm:ss"):是将String转换成Date，一般前台给后台传值时用，Spring自带的处理框架
* @JsonFormat(pattern="yyyy-MM-dd", timezone = "GMT+8")  将Date转换成String  一般后台传值给前台时
* @JSONField来源于fastjson，是阿里巴巴的开源框架，主要进行JSON解析和序列化。@JSONField(format=”yyyy-MM-dd”)


### 方法注解
@PostConstruct：@PostConstruct该注解被用来修饰一个非静态的void（）方法。被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器执行一次。PostConstruct在构造函数之后执行，init（）方法之前执行。
@PreDestroy：被@PreDestroy修饰的方法会在服务器卸载Servlet的时候运行，并且只会被服务器调用一次，类似于Servlet的destroy()方法。被@PreDestroy修饰的方法会在destroy()方法之后运行，在Servlet被彻底卸载之前。
```
    @PostConstruct
    public void init() {
        eventBus.register(eventListener);
    }

    @PreDestroy
    public void destroy() {
        eventBus.unregister(eventListener);
    }
```

* 全局捕获异常
```
@ControllerAdvice
public class GlobalDefaultExceptionHandler{

    //异常处理类型
	@ExceptionHandler(Exception.class）
	@ResponseBody
	public String defaultExceptionHandler(HttpServletRequest req,Exception e){
		return ""; //返回的字符串

		ModelAndView mv=new ModelAndView();
		mv.setViewName(viewName);
	}
	
}
```

### lombok注解
@Data 注解在类上；生成无参构造方法，属性的set/get方法，还提供了equals、canEqual、hashCode、toString 方法
@Setter ：注解在属性上；为属性提供 setting 方法
@Getter ：注解在属性上；为属性提供 getting 方法
@Log4j ：注解在类上；为类提供一个 属性名为log 的 log4j 日志对象
@NoArgsConstructor ：注解在类上；为类提供一个无参的构造方法
@AllArgsConstructor ：注解在类上；为类提供一个全参的构造方法
@RequiredArgsConstructor：注解在类上；生成具有所需参数的构造函数。必需参数是最终字段和具有约束的字段，例如final和@NonNull注解。
@Cleanup : 可以关闭流
@Builder ： 被注解的类加个构造者模式
@Synchronized ： 加个同步锁
@SneakyThrows : 等同于try/catch 捕获异常
@NonNull : 如果给参数加个这个注解 参数为null会抛出空指针异常
@Value : 和@Data类似，区别在于生成有参构造方法，所有成员变量默认定义为private final修饰，只有get方法，没有set方法。

