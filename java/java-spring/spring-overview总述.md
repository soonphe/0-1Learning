# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## spring-overview总述

### Spring中bean生命周期
以注解类变成Spring Bean为例，Spring会扫描指定包下面的Java类，然后根据Java类构建beanDefinition对象，然后再根据beanDefinition来创建Spring的bean，特别要记住一点，Spring是根据beanDefinition来创建Spring bean的，关于beanDefinition下文会进行分析。

beanDefinition所包含的内容：
- constructorArgumentValues 
- beanClass：在对象实例化时，会根据beanClass来创建对象
- primary：是否是首要的
- lazyInit：在Spring中有一个@Lazy注解，用来标识其这个bean是否为懒加载的
- scope ：@scope注解来标识这个bean其作用域，是单例还是多例
- factoryMethodName
- initMethodName ：初始化方法
- destroyMethodName：销毁方法
- abstractFlag 
- dependsOn
- autowireMode：这个属性用于记录使用怎样的注入模型，注入模型常用的有根据名称和根据类型、不注入三种注入模型；
- autowireCandidate
- qualifiers 
- propertyValues

说明：
我们在beanDefinition指定其根据类型进行属性注入，那么在创建这个bean时，会将其beanClass中的所有属性都拿到，然后排除掉基本属性（如类型String、Double、Boolean、Object），然后对于剩下的进行属性注入，记住一点，在beanDefinition指定其根据类型进行属性注入，即使不在属性上面使用@Autowired注解也会对其进行属性注入。

在我们写注解类的时候为什么不使用@Autowired时，其属性就注入不进来呢？那是因为注解类在变成beanDefinition时，其注入类型是不注入，所以此时只有使用@Autowired注解进行标记的属性，才会完成依赖注入。

为什么不直接使用对象的class对象来创建bean，而要使用Spring中beanDefinition来完成bean的创建？

因为在class对象仅仅能描述一个对象的创建，它不足以用来描述一个Spring bean，而对于是否为懒加载、是否是首要的、初始化方法是哪个、销毁方法是哪个，这个Spring中特有的属性在class对象中并没有，所有Spring就定义了beanDefinition来完成bean的创建。


**java类 -> beanDefinition对象**

如果要说Bean的生命周期，那就不能不说Java类是在那一步变成beanDefinition的，我们先来看一下Spring中AbstractApplicationContext类中的refresh方法。
```
    @Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
			准备工作包括设置启动时间，是否激活标识位，
			// 初始化属性源(property source)配置
			prepareRefresh();
 
			// Tell the subclass to refresh the internal bean factory.
			//返回一个factory 为什么需要返回一个工厂
			//因为要对工厂进行初始化
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
 
			// Prepare the bean factory for use in this context.
			//准备工厂
			prepareBeanFactory(beanFactory);
 
			try {
				// Allows post-processing of the bean factory in context subclasses.
				//这个方法在当前版本的spring是没用任何代码的
				//可能spring期待在后面的版本中去扩展吧
				postProcessBeanFactory(beanFactory);
 
				// Invoke factory processors registered as beans in the context.
				//在spring的环境中去执行已经被注册的 factory processors
				//设置执行自定义的ProcessBeanFactory 和spring内部自己定义的
				invokeBeanFactoryPostProcessors(beanFactory);
 
				// Register bean processors that intercept bean creation.
				//注册beanPostProcessor
				registerBeanPostProcessors(beanFactory);
 
				// Initialize message source for this context.
				initMessageSource();
 
				// Initialize event multicaster for this context.
				//初始化应用事件广播器
				initApplicationEventMulticaster();
 
				// Initialize other special beans in specific context subclasses.
				onRefresh();
 
				// Check for listener beans and register them.
				registerListeners();
 
				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);
 
				// Last step: publish corresponding event.
				finishRefresh();
			}
 
			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}
 
				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();
 
				// Reset 'active' flag.
				cancelRefresh(ex);
 
				// Propagate exception to caller.
				throw ex;
			}
 
			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```

有关Spring Bean生命周期最主要的方法有三个invokeBeanFactoryPostProcessors、registerBeanPostProcessors和finishBeanFactoryInitialization。

- invokeBeanFactoryPostProcessors方法会执行BeanFactoryPostProcessors后置处理器及其子接口BeanDefinitionRegistryPostProcessor，执行顺序先是执行BeanDefinitionRegistryPostProcessor接口的postProcessBeanDefinitionRegistry方法，然后执行BeanFactoryPostProcessor接口的postProcessBeanFactory方法。 对于BeanDefinitionRegistryPostProcessor接口的postProcessBeanDefinitionRegistry方法，该步骤会扫描到指定包下面的标有注解的类，然后将其变成BeanDefinition对象，然后放到一个Spring中的Map中，用于下面创建Spring bean的时候使用这个BeanDefinition

- registerBeanPostProcessors方法根据实现了PriorityOrdered、Ordered接口，排序后注册所有的BeanPostProcessor后置处理器，主要用于Spring Bean创建时，执行这些后置处理器的方法，这也是Spring中提供的扩展点，让我们能够插手Spring bean创建过程。

**beanDefinition对象 -> Spring中的bean**

- finishBeanFactoryInitialization是完成非懒加载的Spring bean的创建的工作，在该步骤中会有8个后置处理的方法4个后置处理器的类贯穿在对象的实例化、属性赋值、初始化、和销毁的过程中

关于每个后置处理的作用如下：

先看一下继承关系，InstantiationAwareBeanPostProcessor、SmartInstantiationAwareBeanPostProcessor、MergedBeanDefinitionPostProcessor直接或间接的继承BeanPostProcessor，而SmartInitializingSingleton则是单独的一个接口。

**一、InstantiationAwareBeanPostProcessor**
InstantiationAwareBeanPostProcessor接口继承BeanPostProcessor接口，它内部提供了3个方法，再加上BeanPostProcessor接口内部的2个方法，所以实现这个接口需要实现5个方法。InstantiationAwareBeanPostProcessor接口的主要作用在于目标对象的实例化过程中需要处理的事情，包括实例化对象的前后过程以及实例的属性设置

在org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#createBean()方法的Object bean = resolveBeforeInstantiation(beanName, mbdToUse);方法里面执行了这个后置处理器

**二、SmartInstantiationAwareBeanPostProcessor**
智能实例化Bean后置处理器（继承InstantiationAwareBeanPostProcessor）

**三、MergedBeanDefinitionPostProcessor**

**四、SmartInitializingSingleton**
智能初始化Singleton，该接口只有一个方法，是在所有的非懒加载单实例bean都成功创建并且放到Spring IOC容器之后进行执行的。

下面按照图中的顺序进行说明每个后置处理的作用：

1、InstantiationAwareBeanPostProcessor# postProcessBeforeInstantiation

在目标对象实例化之前调用，方法的返回值类型是Object，我们可以返回任何类型的值。由于这个时候目标对象还未实例化，所以这个返回值可以用来代替原本该生成的目标对象的实例(一般都是代理对象)。如果该方法的返回值代替原本该生成的目标对象，后续只有postProcessAfterInitialization方法会调用，其它方法不再调用；否则按照正常的流程走

2、SmartInstantiationAwareBeanPostProcessor# determineCandidateConstructors

检测Bean的构造器，可以检测出多个候选构造器

3、MergedBeanDefinitionPostProcessor# postProcessMergedBeanDefinition

缓存bean的注入信息的后置处理器，仅仅是缓存或者干脆叫做查找更加合适，没有完成注入，注入是另外一个后置处理器的作用

4、SmartInstantiationAwareBeanPostProcessor# getEarlyBeanReference

循环引用的后置处理器，这个东西比较复杂， 获得提前暴露的bean引用。主要用于解决循环引用的问题，只有单例对象才会调用此方法，以后如果有时间，我会写一篇关于Spring是怎么处理循环依赖的，会将这个，现在你就只需要知道他能提前暴露bean就行了。

5、InstantiationAwareBeanPostProcessor# postProcessAfterInstantiation

方法在目标对象实例化之后调用，这个时候对象已经被实例化，但是该实例的属性还未被设置，都是null。如果该方法返回false，会忽略属性值的设置；如果返回true，会按照正常流程设置属性值。方法不管postProcessBeforeInstantiation方法的返回值是什么都会执行

6、InstantiationAwareBeanPostProcessor# postProcessPropertie

方法对属性值进行修改(这个时候属性值还未被设置，但是我们可以修改原本该设置进去的属性值)。如果postProcessAfterInstantiation方法返回false，该方法不会被调用。可以在该方法内完成对属性的自动注入（注意：在Spring5.1之前，完成属性自动注入的方法是postProcessPropertyValues方法，在5.1就开始使用postProcessPropertie方法了，如果发现不同也不必惊慌，就是改了一下方法名，内部逻辑一样的）（在这一步，会完成标注了@Autowired、@Resource注解的自动装配）

7.BeanPostProcessor# postProcessBeforeInitialization

该方法会在初始化之前进行执行，其中有一个实现类比较重要ApplicationContextAwareProcessor，该后置处理的一个作用就是当应用程序定义的Bean实现ApplicationContextAware接口时注入ApplicationContext对象

看到这里你可能恍然大悟，原来调用BeanFactoryAware和ApplicationContextAware时，相应的处理是不同的，所以，你如果把这个不同点说出来，那么面试官就会认为你是看过源码的人。ApplicationContextAware是在一个BeanPostProcessor后置处理器的postProcessBeforeInitialization方法中进行执行的。

8.BeanPostProcessor# postProcessAfterInitialization

该后置处理器的执行是在调用init方法后面进行执行，主要是判断该bean是否需要被AOP代理增强，如果需要的话，则会在该步骤返回一个代理对象。

9.SmartInitializingSingleton# afterSingletonsInstantiated

该方法会在所有的非懒加载单实例bean都成功创建并且放到Spring IOC容器之后，依次遍历所有的bean，如果当前这个bean是SmartInitializingSingleton的子类，那么就强转成SmartInitializingSingleton类，然后调用SmartInitializingSingleton的afterSingletonsInstantiated方法。

对于SmartInitializingSingleton# afterSingletonsInstantiated方法，是在所有的bean完成之后进行调用的，这个扩展点可以让我们自己很轻松的自定义注解，完成我们想要实现的逻辑，比如使用@EventListener注解实现事件监听，其底层就是通过 SmartInitializingSingleton# afterSingletonsInstantiated来实现的，所以对于这个重点说一下。

**总结**
1. 普通Java类是在哪一步变成beanDefinition的：AbstractApplicationContext类中的refresh中invokeBeanFactoryPostProcessors方法，执行BeanDefinitionRegistryPostProcessor接口中的postProcessBeanDefinitionRegistry方法，该方法会扫描到指定包下面的标有注解的类，然后将其变成BeanDefinition对象，然后放到一个Spring中的Map中，用于下面创建Spring bean的时候使用这个BeanDefinition
2. 有8个后置处理器方法4个后置处理器，贯穿了对象的创建->属性赋值->初始化->销毁
3. 处理器方法执行的时机和作用
4. invokeInitMethods执行的Aware和BeanPostProcessor# postProcessBeforeInitialization方法执行的aware

Spring Bean的生命周期分为四个阶段和多个扩展点。扩展点又可以分为影响多个Bean和影响单个Bean。整理如下：

四个阶段：
- 实例化 Instantiation
- 属性赋值 Populate
- 初始化 Initialization
- 销毁 Destruction

多个扩展点：
- 影响多个Bean
  - BeanPostProcessor
    - postProcessBeforeInitialization
    - postProcessAfterInitialization
  - InstantiationAwareBeanPostProcessor
    - postProcessBeforeInstantiation
    - postProcessAfterInstantiation
    - postProcessPropertyValues
  - MergedBeanDefinitionPostProcessor
    - postProcessMergedBeanDefinition
  - SmartInstantiationAwareBeanPostProcessor
    - determineCandidateConstructors
    - getEarlyBeanReference
- 影响单个Bean
  - Aware
    - Aware Group1（调用invokeInitMethods方法）
      - BeanNameAware
      - BeanClassLoaderAware
      - BeanFactoryAware
    - Aware Group2（调用Aware和BeanPostProcessor# postProcessBeforeInitialization方法）
      - EnvironmentAware
      - EmbeddedValueResolverAware
      - ApplicationContextAware(ResourceLoaderAware\ApplicationEventPublisherAware\MessageSourceAware)
  - 生命周期
    - InitializingBean
    - DisposableBean


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



### Spring JdbcTemplate 方法详解
JdbcTemplate主要提供以下五类方法：
- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

JdbcTemplate类支持的回调类：
预编译语句及存储过程创建回调：用于根据JdbcTemplate提供的连接创建相应的语句；
PreparedStatementCreator：通过回调获取JdbcTemplate提供的Connection，由用户使用该Conncetion创建相关的PreparedStatement；
CallableStatementCreator：通过回调获取JdbcTemplate提供的Connection，由用户使用该Conncetion创建相关的CallableStatement；
预编译语句设值回调：用于给预编译语句相应参数设值；
PreparedStatementSetter：通过回调获取JdbcTemplate提供的PreparedStatement，由用户来对相应的预编译语句相应参数设值；
BatchPreparedStatementSetter：；类似于PreparedStatementSetter，但用于批处理，需要指定批处理大小；
自定义功能回调：提供给用户一个扩展点，用户可以在指定类型的扩展点执行任何数量需要的操作；
ConnectionCallback：通过回调获取JdbcTemplate提供的Connection，用户可在该Connection执行任何数量的操作；
StatementCallback：通过回调获取JdbcTemplate提供的Statement，用户可以在该Statement执行任何数量的操作；
PreparedStatementCallback：通过回调获取JdbcTemplate提供的PreparedStatement，用户可以在该PreparedStatement执行任何数量的操作；
CallableStatementCallback：通过回调获取JdbcTemplate提供的CallableStatement，用户可以在该CallableStatement执行任何数量的操作；
结果集处理回调：通过回调处理ResultSet或将ResultSet转换为需要的形式；
RowMapper：用于将结果集每行数据转换为需要的类型，用户需实现方法mapRow(ResultSet rs, int rowNum)来完成将每行数据转换为相应的类型。
RowCallbackHandler：用于处理ResultSet的每一行结果，用户需实现方法processRow(ResultSet rs)来完成处理，在该回调方法中无需执行rs.next()，该操作由JdbcTemplate来执行，用户只需按行获取数据然后处理即可。
ResultSetExtractor：用于结果集数据提取，用户需实现方法extractData(ResultSet rs)来处理结果集，用户必须处理整个结果集；



### Spring FactoryBean和BeanFactory 区别
1. BeanFactory 是ioc容器的底层实现接口，是顶层容器（根容器），不能被实例化，不允许我们直接操作 BeanFactory bean工厂，它定义了所有 IoC 容器 必须遵从 的⼀套原则，具体的容器实现可以增加额外的功能。

   BeanFactory 接口又衍生出以下接口
  - ApplicationContext，ApplicationContext包含BeanFactory的所有功能,同时还进行更多的扩展，
    - ClassPathXmlApplicationContext 包含了解析 xml 等⼀系列的内容
    - AnnotationConfigApplicationContext 则是包含了注解解析等⼀系列的内容。Spring IoC 容器继承体系

2. FactoryBean 是spirng提供的工厂bean的一个接口
   FactoryBean 接口提供三个方法，用来创建对象。
   FactoryBean 具体返回的对象是由getObject 方法决定的。
```
public interface FactoryBean<T> {

	//创建的具体bean对象的类型
	@Nullable
	T getObject() throws Exception;

	//工厂bean 具体创建具体对象是由此getObject()方法来返回的
	@Nullable
	Class<?> getObjectType();
	
 	//是否单例
	default boolean isSingleton() {
		return true;
	}

}
```


### Java8之Consumer、Supplier、Predicate和Function

这几个接口都在 java.util.function 包下的，分别是
- Consumer<T>（消费型）

  Consumer<T>接口就是一个消费型的接口，实现 accept 的方法，T：入参类型；没有出参。
  ```
  Consumer<String> consumer = new Consumer<String>() {
	
                @Override
                public void accept(String s) {
                    System.out.println(s);
                }
            };
    //lambda表达式
	Consumer<String> consumer1 = (s) -> System.out.println(s);//lambda表达式返回的就是一个Consumer接口
  ```

- Supplier<T>（供给型）

  Supplier<T> 接口是一个供给型的接口，实现 get 方法。T：出参类型；没有入参。可以用来存储数据，然后可以供其他方法使用的这么一个接口
  ```
  Supplier<Integer> supplier = () -> new Random().nextInt();
  ```

- Predicate<T>（谓词型）

  Predicate<T> 接口是一个谓词型接口，实现一个 test 方法。T：入参类型；出参类型是Boolean。作用：类似于 bool 类型的判断的接口。
  ```
  Predicate<Integer> predicate = (t) -> t > 5;
  ```

- Function<T, R> （功能性）

  Function<T, R>  接口是一个功能型接口，实现 apply 方法。T：入参类型，R：出参类型。作用：将输入数据转换成另一种形式的输出数据。
  ```
  Function<String, Integer> function = new Function<String, Integer>() {
            @Override
            public Integer apply(String s) {
                return s.length();//获取每个字符串的长度，并且返回
            }
        };
  
  ```

### <? extends U> 和 <? super U>的区别：
- <? super U> ：规定元素的最小粒度下限，不影响往里存，但往外取只能放在Object对象里，如Number super Integer。
```
Box<? super RedApple> box3 = new Box<Fruit>();
```

- <? extends U>：规定元素的最大粒度上限，不影响取，存只能存指定类型的变量，如：Integer extends Number。
```
Box<? extends Food> box1 = new Box<Fruit>();
Box<? extends Food> box2 = new Box<Meat>();
```

### CompletableFuture
使用Future获得异步执行结果时，要么调用阻塞方法get()，要么轮询看isDone()是否为true，这两种方法都不是很好，因为主线程也会被迫等待。

从Java 8开始引入了CompletableFuture，它针对Future做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。


### 反射操作如何得到对象、方法、参数
- 反射获取对象
```
//注意，要传完整类名，如：com.soonphe.timber.hello.Helloworld
Class cls = Class.forName("完整类名");
Object yourObj = cls.newInstance();
```
- 反射获取方法
```
Method methlist[] = cls.getDeclaredMethods();
for (int i = 0; i < methlist.length; i++) {
	Method m = methlist[i];
}
```
- 反射调用方法并参数
```
//获取到方法对象,假设方法的参数是一个int,method名为setAge
Method sAge = cls.getMethod("setAge", new Class[] {int.class});
//构造参数Object
Object[] arguments = new Object[] { new Integer(37)};
//执行方法
sAge.invoke(yourObj , arguments);
```


### 反射时getConstructor()与getDeclaredConstructor()方法的区别及setAccessible()方法的作用
```
	@Test
	void testName() throws Exception {
		HelloWorld world=null;
		String className="hello.HelloWorld";
		Constructor con=Class.forName(className).getConstructor();
		//设置不检查是否可以访问
		con.setAccessible(true);
		Object obj=con.newInstance();
		world=(HelloWorld) obj;
		world.sayHello();
	}
```
**getConstructor()** 和 **getDeclaredConstructor()** 的作用：获取类的构造器

不同点：
- Class类的getConstructor()方法,无论是否设置setAccessible(),都不可获取到类的私有构造器.
- Class类的getDeclaredConstructor()方法,可获取到类的私有构造器(包括带有其他修饰符的构造器），但在使用private的构造器时，必须设置setAccessible()为true,才可以获取并操作该Constructor对象。
