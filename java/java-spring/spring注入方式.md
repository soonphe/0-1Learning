# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Spring bean声明，属性，注入方式

### 如何声明一个bean 
- 声明方式有很多，最简单的就是添加以下注解去实现
  - @Bean
  - @Component
  - @Service
  - @Controller
  - ...

- 如何自定义bean的命名
  - 默认情况下bean的名称和方法名称相同，你也可以使用name属性来指定
  例：@Bean(name = "xxx")

### spring中重载bean
添加配置即可
```
spring:
  main:
    allow-bean-definition-overriding: true
```

### @Bean(initMethod = "init")的作用
init-method="init"  destroy-method="close" 作用：

init-method="init"是指bean被初始化时执行的方法，当bean实例化后,执行init-method用于初始化数据库连接池。

destroy-method="close" 是指bean被销毁时执行的方法   Spring容器关闭时调用该方法即调用close()将连接关闭。

### @Component默认是单例还是多例？
@Component注解默认实例化的对象是单例，如果想声明成多例对象可以使用@Scope("prototype")
@Repository默认单例
@Service默认单例（注解由@Component组成）
@Controller默认单例（注解就是由@Component组成） 

### SpringMVC 默认创建 Bean 是单例的，在高并发情况下是如何保证性能的？
spring中的bean默认是单例的，通常对单例进行多线程访问时，为了线程安全而采用同步机制，以时间换空间的方式，而Spring中是利用ThreadLocal来以空间换取时间，为每一个线程提供变量副本，来保证变量副本对于某一线程都是线程安全的。

用ThreadLocal是为了保证线程安全，实际上ThreadLoacal的key就是当前线程的Thread实例。

虽然spring对象是单例的，但类里面方法对每个线程来说都是独立运行的，不存在多线程问题，只有成员变量有多线程问题，所以方法里面如果有用到成员变量就要考虑用安全的数据结构。

单例模式是spring推荐的配置，单利模式因为大大节省了实例的创建和销毁，它在高并发下能极大的节省资源，提高服务抗压能力。


---
### 常见的spring注入方法

#### 使用最广泛的 @Autowired和@Resource
```
@Service
public class BaseInfoCompanyFareServiceImpl implements BaseInfoCompanyFareService {

    @Autowired
    private BaseInfoCompanyFareDao baseInfoCompanyFareDao;
```
说明：
@Autowired按byType自动注入，而@Resource默认按 byName自动注入罢了
由于@Autowired 默认第一按照byType(类的类型),第二byName(l类名\类ID)来加载类，所以当存在类型相同,多个beanname时，想注入某个类，就必须指定根据什么beanName查找（使用@Qualifier注解指定），如果不用@Qualifier注解指定，则会以变量名为为beanName进行查找；
备注：@Autowired实现方式是通过 *反射* 来设置属性值

#### 构造器注入
```
@Service
public class BaseInfoCompanyPayServiceImpl implements BaseInfoCompanyPayService {

    private final BaseInfoCompanyPayDao baseInfoCompanyPayDao;

    public BaseInfoCompanyPayServiceImpl(BaseInfoCompanyPayDao baseInfoCompanyPayDao) {
        this.baseInfoCompanyPayDao = baseInfoCompanyPayDao;
    }
}
```
通过有参的构造函数注入
备注：构造注入是一种高内聚的体现，特别是针对有些属性需要在对象在创建时候赋值，且后续不允许修改（不提供setter方法）。

#### 属性注入
```
@Service
public class BaseInfoCompanyPayServiceImpl implements BaseInfoCompanyPayService {

    private final BaseInfoCompanyPayDao baseInfoCompanyPayDao;

    public BaseInfoCompanyPayServiceImpl( ) {
    }
    
    private void setBaseInfoCompanyPayDao(BaseInfoCompanyPayDao baseInfoCompanyPayDao){
    	this.baseInfoCompanyPayDao = baseInfoCompanyPayDao;
    }
}
```
通过无参构造函数+setter方法注入
备注：SpringContext利用无参的构造函数创建一个对象，然后利用setter方法赋值。所以如果无参构造函数不存在，Spring上下文创建对象的时候便会报错。

#### lombok提供的@RequiredArgsConstructor方式
```
@Service
@RequiredArgsConstructor
public class BaseInfoCompanyServiceImpl implements BaseInfoCompanyService {

    final BaseInfoCompanyDao baseInfoCompanyDao;
```
说明：在注入时生成具有所需参数的构造函数（原理还是利用的构造注入）。必需参数是最终字段和具有约束的字段，例如final和@NonNull注解
在我们写controller或者Service层的时候，需要注入很多的mapper接口或者另外的service接口，这时候就会写很多的@AutoWired注解，@RequiredArgsConstructor避免代码看起来很乱

