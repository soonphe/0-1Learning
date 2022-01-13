# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## application.properties与Spring Environment详解

### springboot 获取enviroment.Properties的几种方式
springboot获取配置资源，主要分3种方式：@Value、 @ConfigurationProperties、Enviroment对象直接调用。
前2种底层实现原理，都是通过第三种方式实现。
@Value 是spring原生功能，通过PropertyPlaceholderHelper.replacePlaceholders()方法，支持EL表达式的替换。
@ConfigurationProperties则是springboot 通过自动配置实现，并且最后通过JavaBeanBinder 来实现松绑定
获取资源的方式
1. @Value标签获取
```
@Component
@Setter@Getter
public class SysValue {
  @Value("${sys.defaultPW}")
  private String defaultPW;
}
```
2. @ConfigurationProperties标签获取
```
@Component
@ConfigurationProperties(prefix = "sys")
@Setter@Getter
public class SysConfig {
  private String defaultPW;
}
```
3. 直接Enviroment对象获取
```
@Component
public class EnvironmentValue {
  @Autowired
  Environment environment;
  private String defaultPW;
  
  @PostConstruct//初始化调用
  public  void init(){
    defaultPW=environment.getProperty("sys.defaultPW");
  }

}
```

### PropertyResolver初始化与方法。
1. api解释：
Interface for resolving properties against any underlying source.
（解析properties针对于任何底层资源的接口）

2. 常用实现类
PropertySourcesPropertyResolver：配置源解析器。
Environment：environment对象也继承了解析器。

3. 常用方法。
java.lang.String getProperty(java.lang.String key)：根绝 Key获取值。
java.lang.String resolvePlaceholders(java.lang.String text)：替换$(....)占位符，并赋予值。（@Value 底层通过该方法实现）。

4. springboot中environment初始化过程初始化PropertySourcesPropertyResolver代码。
```
public abstract class AbstractEnvironment implements ConfigurableEnvironment {
//初始化environment抽象类是，会初始化PropertySourcesPropertyResolver，
//并将propertySources传入。
//获取逻辑猜想：propertySources是一个List<>。
//getProperty方法会遍历List根据key获取到value
//一旦获取到value则跳出循环，从而实现优先级问题。
private final ConfigurablePropertyResolver propertyResolver =
            new PropertySourcesPropertyResolver(this.propertySources);
}
```


### application.properties配置文件用法
在spring boot的项目当中，我们可以将配置信息以key-value，即键值对的形式写在application.properties文件里面，例如：
```
test.spring.configuration = testValue
```
其中test.spring.configuration就是key，testValue就是这个key的value。

不要小看写在这里的这个字符串。将信息写在properties文件里的意味着它已经和我们的应用程序隔离开了，是解耦合的，文件里面的内容是在程序启动之后动态读取的，这意味着我们可以通过更改配置文件而改变程序执行的逻辑，但是却并不需要改动任何逻辑代码。
为什么写在这些properties文件里的值可以被我们在代码中使用呢，原因是，spring boot在启动时已经帮我们把这些值都放在了Spring的Environment里面了。

SpringApplication从以下位置的application.properties文件加载属性，并将其添加到Spring的Environment中：
- 当前目录的/config子目录
- 当前目录
- classpath下的/config目录
- classpath路径根目录

### Spring的Environment概念
Environment即环境，也可以叫做上下文，相当于是是我们的应用程序运行时的一个背景信息。
在spring中，Environment这个概念是与spring的容器container集成在一起的一个抽象概念。
它主要为我们的应用程序环境的两个方面的支持：profiles and properties。
在spring库中，Environment也有其接口的代码定义，如下所示：
```
public interface Environment extends PropertyResolver {
    String[] getActiveProfiles();
 
    String[] getDefaultProfiles();
 
    boolean acceptsProfiles(String... var1);
}
```
properties我们在上文已经有所涉及了，我们知道它是与配置文件有关的东西，那么profiles是什么呢？

spring是这么定义profile这个概念的：profile的作用就是符合这个给定profile配置的bean的集合。
我们已经很熟悉spring中的bean了，而bean在定义好之后，是可以分配到一个指定的profile的。
注意，这与bean定义的方式没有关系，无论是采用xml的方式还是java注解的方式定义bean，我们都可以配置其属于哪一个profile。并且这一点是在运行时确立的。

同时，当有多个profile的时候，我们可以指定哪一个Profile是生效的，如果不指定的话spring则会根据默认的profile去运行。若使用java配置的话，我们可以使用注解@Profile来指定某个bean属于哪一个profile，只有当这个指定的profile被激活处于active状态时，这个bean才会被创建。

properties文件顾名思义就是拿来存放属性的。它能帮我们管理各种信息，如：应用程序中的一些属性变量、JVM系统属性、系统的环境变量、servlet上下文参数、等等。properties配置文件在几乎所有的应用程序中都有着重要的应用。

Environment接口代码当中，我们看但这个接口继承了另一个叫PropertyResolver的接口，这个接口是与property相关的，而Environment当中除了定义了profile的相关方法之后并没有其他和profile相关的类或接口的抽象。

这一点其实不难理解，Environment和property是最基本的概念与抽象，而profile是指的生效的properties，因此它只是提供了方法的抽象，从涉及角度出发我们就能准确的理解这两个接口的定义。PropertyResolver接口的定义如下:
```
public interface PropertyResolver {
    boolean containsProperty(String var1);
 
    @Nullable
    String getProperty(String var1);
 
    String getProperty(String var1, String var2);
 
    @Nullable
    <T> T getProperty(String var1, Class<T> var2);
 
    <T> T getProperty(String var1, Class<T> var2, T var3);
 
    String getRequiredProperty(String var1) throws IllegalStateException;
 
    <T> T getRequiredProperty(String var1, Class<T> var2) throws IllegalStateException;
 
    String resolvePlaceholders(String var1);
 
    String resolveRequiredPlaceholders(String var1) throws IllegalArgumentException;
}
```

接口里面定义的大部分方法都是获取property的，这很好理解，同时还有两个方法与PlaceHolder占位符有关，我们可以联想到这与spring中property的用法必然有关系，这一点稍后再谈。

### Profile-specific Properties – 特定profile的属性
上文我们已经介绍了profile和property这两个概念，以及更大的Environment概念，现在我们来看一下前两者结合的一个重要用法。

我们在上文提到properties文件时，都是默认其为application.properties文件，事实上除了application.properties文件外，还可以使用以下命名约定定义特定的属性：
```
application-profile.properties
```
spring的配置环境有一组默认配置文件，如果未在启动时指定active的配置文件，则使用默认配置文件。换句话说，如果没有显式激活某一个配置文件，那么应用程序就将加载application-default.properties中的属性

如何激活某一个profile
上面的内容讲到了我们可以定义多个profile，并根据实际需求进行配置和切换。那么spring是如何确定到底哪个profile是active的呢。spring主要是通过下面两个属性来实现判断逻辑的

- spring.profiles.active
- spring.profiles.default
  我们可以通过设置spring.profiles.active来告诉spring哪个profile应该被激活。若不设置，则spring就查找spring.profiles.default的值激活默认的profile，如果两者都没设置，则spring只创建没有依赖于profile的bean。值得注意的是，spring boot的应用当中，spring为我们提前已经设置了默认的profile。

告诉spring激活哪个profile的方式也是很多种的，我们可以在代码里面指定如下：
```
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.refresh();
```
注意这里的方法定义是叫setActiveProfiles，这个方法值得注意的点是它是个复数，这意味着我们可以指定多个生效的profile，事实也确实如此，如下的方式也是支持的：
```
ctx.getEnvironment().setActiveProfiles("developement", "test");
```
也可以用项目的启动参数指定生效的profile，如下
```
-Dspring.profile.active="dev,test"
```
> 简单说明一下jvm参数
> java -D 我们经常见到。
> - 格式： java -D<name>=<value>
> - 作用： Set a system property value. If value is a string that contains spaces, you must enclose the string in double quotes.
> - 获取设置的值： System.getProperty(“name”)

在springboot的应用当中，哪个具体的配置文件会被加载，需要在application.properties文件中通过spring.profiles.active属性来设置，例如：
```
spring.profiles.active=dev
```
就会加载application-dev.properties配置文件内容，若要指定多个profile，则以逗号分隔即可。


### PropertySource介绍
上文中我们提到了property，并且知道spring boot当中我们可以在application.properties文件里以及同一目录下的其他文件配置property，这样我们就能在系统当中使用它们。
事实上这是因为spring boot帮我们做了一些事情，application.properties文件里的内容才能被检测到。
由于是学习知识，我们考虑没有spring boot的项目，一个普通的spring项目是如何告诉spring哪些property是应该加载的呢？答案是通过一个叫PropertySource的东西。

PropertySource是一个可以存放任何一个键值对(key value)抽象概念，这和我们上面提到的属性值就联系起来了。Spring的中的标准的环境(standardProperty)里面环境变量是通过两个PropertySource对象来确定的：

- 一个是表示JVM系统属性的（通过System.GetProperties（）方法获取）
- 另一个则是表示系统环境变量的（通过System.GetEnv（）方法获取）。

对于spring用户来说必须关注的一点是，PropertySource中的内容是可配置的，任何时候，我们想传入一个换进参数，只需将它以一个键值对的形式告诉spring就可以了。
PropertySource的路径也是可以自己指定的，这意味着，我们可以指定某一个路径下的某一个文件，作为PropertySource的环境变量输入文件。
使用java注解的代码示例如下：
```
@Configuration
@PropertySource("classpath:/com/myco/app.properties")
public class AppConfig {
 
    @Autowired
    Environment env;
 
    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
```
我们假设这个app.properties的文件已被创建，且里面有一个键值对属性：
```
testbean.name=myTestBean
```
那么上述代码的TestBean对象就能够拿到这个myTestBean的String字符串。
不难想到，spring boot的加载功能，也必然是像这样实现的。

### @Value注解的使用
上文我们提到了我们可以在properties文件里定义我们需要的属性，也可以通过spring的Environment 提供的getProperty方法获取对应的值，除此之外，spring还提供了非常方便的注解@Value供我们使用。

我们可以使用@value注释将properties中的值直接注入到spring的任意一个bean中。
下面是一个使用@Value注解的代码示例
```
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;
 
@Component
public class MyBean {
 
    @Value("${name}")
    private String name;
 
    // ...
 
}
```
@Value后面可以跟一个参数，也就是上面代码中的name。Spring将根据这个name作为key值去我们对应的properties文件当中找它对应的value值并赋给这个String类型的变量name，

### spring cloud bootstrap.properties介绍
所谓bootstrap，就是指的引导程序的意思，它比我们上面提到的配置文件优先级更加高，或者说更先被加载。它的作用是负责加载外部资源的配置属性，并对加密属性进行解密。bootstrap properties指的就是外部资源，工具的一些属性。

这里需要注意的是外部这个词语，这里的外部是相对于应用程序而言的，如应用程序所部署的平台，对应用程序而言就是一个外部的环境，一个第三方的管理权限的工具，也是一个外部的环境。但是数据库的信息，我们看作是应用程序的一部分。

spring怎么处理这些外部平台的bootstrap properties呢，是通过另外为它创建一套抽象概念吗，这显然不合理，外部的环境依然属于环境这个大概念的一部分。bootstrap properties和application应用程序是共享的一个环境。并且默认情况下，这些bootstrap properties具有更高的加载优先级，并且它们是不能被覆盖的。这意外着如果我们在bootstrap.properties和application.properties中定义两个key一样的属性，那么bootstrap.properties中的属性优先级更高，且application.properties中的定义不会覆盖。并且，从这两种文件的职责划分来看，它们不应该出现定义相同key属性的情况。


### Spring环境Environment：System.getProperty 和 System.getenv 区别
标准环境默认会将如下信息放入到Environment.MultablePropertySource
- JVM变量–System.getProperties()
- 系统变量–System.getEnv()

java命令的jVM系统参数
标准环境默认会将如下信息放入到Environment.MultablePropertySource
- JVM变量–System.getProperties() 设置方式 java -jar -D name=value
- 系统变量–System.getEnv() 设置方式 比如vi etc/profiles
- 命令行参数 – 设置方式 java -jar xx.jar --spring.profiles.active=dev

