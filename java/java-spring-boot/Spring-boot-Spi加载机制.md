# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Spring Boot SPI加载机制

### 1.SPI思想 

1.SPI机制

SPI的全名为Service Provider Interface.这个是针对厂商或者插件的。

SPI的思想：系统里抽象的各个模块，往往有很多不同的实现方案，比如日志模块的方案，xml解析模块、jdbc模块的方案等。

面向的对象的设计里，我们一般推荐模块之间基于接口编程，模块之间不对实现类进行硬编码。一旦代码里涉及具体的实现类，就违反了可拔插的原则，如果需要替换一种实现，就需要修改代码。为了实现在模块装配的时候能不在程序里动态指明，这就需要一种服务发现机制。

java spi就是提供这样的一个机制：为某个接口寻找服务实现的机制


### 2.SPI约定
当服务的提供者，提供了服务接口的一种实现之后，在jar包的META-INF/services/目录里同时创建一个以服务接口命名的文件。该文件里就是实现该服务接口的具体实现类全限定类名，多个实现类用换行符分隔。

而当外部程序装配这个模块的时候，就能通过该jar包META-INF/services/里的配置文件找到具体的实现类名，并装载实例化，完成模块的注入。通过这个约定，就不需要把服务放在代码中了，通过模块被装配的时候就可以发现服务类了。

- SPI使用案例
common-logging apache最早提供的日志的门面接口。只有接口，没有实现。
具体方案由各提供商实现， 发现日志提供商是通过扫描 META-INF/services/org.apache.commons.logging.LogFactory配置文件，通过读取该文件的内容找到日志提工商实现类。
只要我们的日志实现里包含了这个文件，并在文件里制定 LogFactory工厂接口的实现类即可。

总结：SPI的好处是避免写死，调用者可以根据自己的需求调用不同的实现类 。


### 3.SPI实现
```
定义一个接口，SPIService
package com.viewscenes.netsupervisor.spi;
public interface SPIService {
    void execute();
}

定义两个实现类：
package com.viewscenes.netsupervisor.spi;
public class SpiImpl1 implements SPIService{
    public void execute() {
        System.out.println("SpiImpl1.execute()");
    }
}

package com.viewscenes.netsupervisor.spi;
public class SpiImpl2 implements SPIService{
    public void execute() {
        System.out.println("SpiImpl2.execute()");
    }
}

```
在 src/main/resources/META-INF/services/下添加文件`com.viewscenes.netsupervisor.spi.SPIService`
文件名字是接口的全限定类名，内容是实现类的全限定类名，多个实现类用换行符分隔。
```
com.viewscenes.netsupervisor.spi.SpiImpl1
com.viewscenes.netsupervisor.spi.SpiImpl2
```

测试调用
通过ServiceLoader.load或者Service.providers方法拿到实现类的实例。
其中，Service.providers包位于sun.misc.Service，而ServiceLoader.load包位于java.util.ServiceLoader。两种方式的输出结果是一致的：
```
public class Test {
    public static void main(String[] args) {    
        Iterator<SPIService> providers = Service.providers(SPIService.class);
        ServiceLoader<SPIService> load = ServiceLoader.load(SPIService.class);

        while(providers.hasNext()) {
            SPIService ser = providers.next();
            ser.execute();
        }
        System.out.println("--------------------------------");
        Iterator<SPIService> iterator = load.iterator();
        while(iterator.hasNext()) {
            SPIService ser = iterator.next();
            ser.execute();
        }
    }
}

```

### Spring-boot自动装配原理
如果我们想自定义一个starter，应该如何操作？

前置步骤：新增一个项目，配置相关属性和依赖，配置文件写好参数。

配置读取指定参数
```
@ConfigurationProperties(prefix = "microservice.redis")
public class RedissonProperties {
    private String host="localhost";
    private int port = 379;
    private String password;
    private int timeout;
    private boolean ssl;
    //setter getter方法省略
}
```

定义自动装配的配置类（这里以redission组件为例）
```
@Configuration
@ConditionalOnClass(Redisson.class)
@EnableConfigurationProperties(RedissonProperties.class)
public class TimberConfiguration {
    @Bean
    public RedissonClient redisonClient(RedissonProperties redissonProperties) {
        Config config = new Config();
        String prefix = "redis://";
        if (redissonProperties.isSsl()) {
            prefix = "rediss://";
        }
        config.useSingleServer()
                .setAddress(prefix + redissonProperties.getHost() + ":" + redissonProperties.getPort())
                .setConnectTimeout(redissonProperties.getTimeout())
                .setPassword(redissonProperties.getPassword());
        return Redisson.create(config);
    }
}
```
告诉Spring去装配这个自动配置，在META-INF下创建spring.factories文件，在文件中声明要自动装配的配置类
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=cn.org.micorservice.redisson.TimberConfiguration
```
然后在使用的时候我们只需要引入这个依赖即可。

然后在application.properties配置RedissonProperties 中的配置项，示例如下：
```
microservice.redis.host = 127.0.0.1
microservice.redis.port = 6379
```

### springboot中的类SPI扩展机制
在springboot的自动装配过程中，最终会加载META-INF/spring.factories文件，而加载的过程是由SpringFactoriesLoader加载的。

从CLASSPATH下的每个Jar包中搜寻所有META-INF/spring.factories配置文件，然后将解析properties文件，找到指定名称的配置后返回。

需要注意的是，其实这里不仅仅是会去ClassPath路径下查找，会扫描所有路径下的Jar包，只不过这个文件只会在Classpath下的jar包中。

```
public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
// spring.factories文件的格式为：key=value1,value2,value3
// 从所有的jar包中找到META-INF/spring.factories文件
// 然后从文件中解析出key=factoryClass类名称的所有value值
public static List<String> loadFactoryNames(Class<?> factoryClass, ClassLoader classLoader) {
    String factoryClassName = factoryClass.getName();
    // 取得资源文件的URL
    Enumeration<URL> urls = (classLoader != null ? classLoader.getResources(FACTORIES_RESOURCE_LOCATION) : ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
   List<String> result = new ArrayList<String>();
    // 遍历所有的URL
    while (urls.hasMoreElements()) {
        URL url = urls.nextElement();
        // 根据资源文件URL解析properties文件，得到对应的一组@Configuration类
         Properties properties = PropertiesLoaderUtils.loadProperties(new UrlResource(url));
        String factoryClassNames = properties.getProperty(factoryClassName);
        // 组装数据，并返回
        result.addAll(Arrays.asList(StringUtils.commaDelimitedListToStringArray(factoryClassNames)));
    }
    return result;
}
```

### 源码解析
从代码的层次深入解读Springboot的SPI机制。

首先，是一个很重要的注解@EnableAutoConfiguration，它的源码如下：
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(EnableAutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
 
	Class<?>[] exclude() default {};
 
	String[] excludeName() default {};
 
}
```

> @EnableAutoConfiguration注解就是SpringBoot实现自动配置的关键了。因为注解在底层会被翻译为接口，继承注解本质上等同于继承接口，所以@SpringBootApplication注解继承了@EnableAutoConfiguration注解后，就有了@EnableAutoConfiguration注解的能力。

注意EnableAutoConfiguration是用@Import(EnableAutoConfigurationImportSelector.class)注解的，所以我们先来说说@Import注解，我们来看看EnableAutoConfigurationImportSelector类的部分源码：
```
public class EnableAutoConfigurationImportSelector implements DeferredImportSelector,
		BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware {
 
	private ConfigurableListableBeanFactory beanFactory;
 
	private Environment environment;
 
	private ClassLoader beanClassLoader;
 
	private ResourceLoader resourceLoader;
 
	@Override
	public String[] selectImports(AnnotationMetadata metadata) {
		try {
			AnnotationAttributes attributes = getAttributes(metadata);
            //从spring.factories文件中找出所有配置好的factory的名称
			List<String> configurations = getCandidateConfigurations(metadata,
					attributes);
            //去重
			configurations = removeDuplicates(configurations);
			//分析所有需要排除掉的factory类（EnableAutoConfiguration注解中配置的）
			Set<String> exclusions = getExclusions(metadata, attributes);
            //移除所有需要过滤放入factory类
			configurations.removeAll(exclusions);
            //排序
			configurations = sort(configurations);
            //记录
			recordWithConditionEvaluationReport(configurations, exclusions);
            //数组形式返回
			return configurations.toArray(new String[configurations.size()]);
		}
		catch (IOException ex) {
			throw new IllegalStateException(ex);
		}
	}
 
}
```

在Spring容器启动过程中，会通过invokeBeanFactoryPostProcessors()方法执行很多BeanFactoryPostProcessors()方法来完成BeanFactory的初始化。在这个过程中，EnableAutoConfigurationImportSelector类的selectImports()方法也被调用了。

接下来，我们来看看selectImports()方法都做了些什么？EnableAutoConfigurationImportSelector类导入了一个这样的包：

> import org.springframework.core.io.support.SpringFactoriesLoader;

我们看看它的源码（精简）：
```
public abstract class SpringFactoriesLoader {
 
	private static final Log logger = LogFactory.getLog(SpringFactoriesLoader.class);
 
	public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
 
 
	public static <T> List<T> loadFactories(Class<T> factoryClass, ClassLoader classLoader) {
	
	}
 
	public static List<String> loadFactoryNames(Class<?> factoryClass, ClassLoader classLoader) {
		
	}
 
}
```

这个类我们有利于我们继续往下找线索的代码是这一行：
```
public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
```

有了这一行代码，我们大致就可以猜出来这个SpringFactoriesLoader类大概是干嘛的了吧？它的两个核心方法一个是用来寻找spring.factories文件中的Factory名称的，一个是用来寻找类的。我们再来看看EnableAutoConfigurationImportSelector类中是怎样使用SpringFactoriesLoader类的，以一段代码如下：
```
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata,
			AnnotationAttributes attributes) {
   return SpringFactoriesLoader.loadFactoryNames(
		getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
}
```

其中SpringBoot starter组件也是SPI实现的一种，原理其实是类加载相关的知识点。其中SpringBoot组件中的SPI主要是配置项这一块，具体可以看下AutoConfigurationImportSelector这个实现类下面的源码，源码如下所示：SpringBoot的加载主要使用的是SpringFactoriesLoader这个加载器。在SpringBoot中，AutoConfigurationImportSelector这个类很重要
```
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
        String factoryTypeName = factoryType.getName();
　　　　 // 返回的是一个一个的配置文件，such as (org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration)
        return loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
    }

    private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
　　　　　// 先从缓存里面取数据
        MultiValueMap<String, String> result = cache.get(classLoader);
        if (result != null) {
            return result;
        }

        try {
            Enumeration<URL> urls = (classLoader != null ?
                    classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
                    ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
            result = new LinkedMultiValueMap<>();
            while (urls.hasMoreElements()) {
                URL url = urls.nextElement();
                UrlResource resource = new UrlResource(url);
　　　　　　　　　　// 根据资源路径获取properties配置文件
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                for (Map.Entry<?, ?> entry : properties.entrySet()) {
                    String factoryTypeName = ((String) entry.getKey()).trim();
                    for (String factoryImplementationName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
                        result.add(factoryTypeName, factoryImplementationName.trim());
                    }
                }
            }
　　　　　　　// 放到缓存里面，防止重复加载
            cache.put(classLoader, result);
            return result;
        }
        catch (IOException ex) {
            throw new IllegalArgumentException("Unable to load factories from location [" +
                    FACTORIES_RESOURCE_LOCATION + "]", ex);
        }
    }
```

下面的代码就是把一个一个的配置类进行实例化成对象，其中也是使用了反射的原理。代码如下所示：
```
     @SuppressWarnings("unchecked")
    private static <T> T instantiateFactory(String factoryImplementationName, Class<T> factoryType, ClassLoader classLoader) {
        try {
            Class<?> factoryImplementationClass = ClassUtils.forName(factoryImplementationName, classLoader);
            if (!factoryType.isAssignableFrom(factoryImplementationClass)) {
                throw new IllegalArgumentException(
                        "Class [" + factoryImplementationName + "] is not assignable to factory type [" + factoryType.getName() + "]");
            }
            return (T) ReflectionUtils.accessibleConstructor(factoryImplementationClass).newInstance();
        }
        catch (Throwable ex) {
            throw new IllegalArgumentException(
                "Unable to instantiate factory class [" + factoryImplementationName + "] for factory type [" + factoryType.getName() + "]",
                ex);
        }
    }
```


