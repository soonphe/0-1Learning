# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

## 后端面试资料汇总

## 目录
- [java基础](#项目介绍)
- [Spring和SpringBoot和SpringCloud](#Spring和SpringBoot和SpringCloud)
- [JVM](#JVM)
- [设计模式](#设计模式)
- [数据结构](#数据结构)
- [网络结构](#网络结构)
- [数据库](#数据库)
- [高并发与多线程](#高并发与多线程)
- [分布式与高可用](#分布式与高可用)
- [消息系统](#消息系统)
- [缓存Redis](#缓存Redis)
- [ElasticSearch搜索引擎](#ElasticSearch搜索引擎)
- [系统设计和场景](#系统设计和场景)
- [算法](#算法)
    
## java基础
- java解决问题的关键：时间换空间，空间换时间，能解决99%的问题，懂这两点，不能说是高手，但也可以说基础不错
- 面对对象几大特性：封装、继承、多态、抽象，封装是指隐藏对象的属性和实现细节，仅对外提供公共访问方式；继承是指子类继承父类的属性和方法；多态是指同一个方法调用，不同的执行效果；抽象是指抽取对象的共同特征，形成类的过程。
- 提升代码的鲁棒性(稳定性)：空指针异常、数组越界异常、类型转换异常、迭代器异常、并发修改异常、文件未找到异常、SQL异常、网络异常、栈溢出异常、内存溢出异常
- 异常类的层次结构：Throwable、Error、Exception、RuntimeException、IOException、SQLException
- 存储金额的方案：BigDecimal、Long、Double，BigDecimal是最好的方案，Long是次之，Double是最差的方案,因为Double在计算时会有精度问题(0.1+0.2结果会出现多个0),Long在计算时会有溢出问题,而BigDecimal是完全精确的，float和double一样存在精度损失，比较的时候，可能得到意想不到的结果。
- Io流的结构：字节流、字符流、节点流、处理流，字节流和字符流的区别在于字节流是以字节为单位进行读写的，字符流是以字符为单位进行读写的，节点流是直接与数据源相连的流，处理流是对已存在的流进行包装，通过对已存在的流进行包装，可以提供更为强大的功能(基础是：输入、输出流，字节流字符流，难点才是io流和nio流、io多路复用、信号驱动、异步io)
- 说说对范型的理解：泛型一般用在集合类、参数化，一些框架或者依赖库。泛型提供了类型安全的机制，使得我们在编译时就能发现潜在的错误，比如错误的类型转换等。代码复用：提供效率同时使代码更加易于维护。
- 如何设计一个上传接口（图片或者文件），应该注意什么，上传之后如何让前端访问到 并展示出来
- http状态码：200成功，301重定向,401禁止访问，404找不到,500服务器内部错误，502网关错误
- head Content-Type请求头类型：application/json，application/x-www-form-urlencoded，multipart/form-data，blob，text/html，xml，byte，event stream
- cookie、token、session三者的区别：
    * cookie：存储在客户端，大小有限制，不安全，可以被篡改，可以设置过期时间，可以设置域名，可以设置路径
    * session：存储在服务端，大小无限制，安全，不可篡改，可以设置过期时间，可以设置域名，可以设置路径
    * token：存储在客户端，大小无限制，安全，不可篡改，可以设置过期时间，可以设置域名，可以设置路径
    * 为什么不用cookie和session，适配多客户端介入，cookie和token存储在客户端，Session 在服务端存，token 在接口 head验证，放篡改，分布式调用
    * session分布式方案：session共享，session复制，session代理，session数据库，session缓存
- oauth2的流程：授权码模式，密码模式，简化模式，客户端模式
    - 授权码模式：用户访问客户端，客户端将用户导向认证服务器，用户同意授权，认证服务器将用户导向客户端，客户端通过授权码向认证服务器申请令牌
    - 密码模式：用户向客户端提供用户名和密码，客户端向认证服务器申请令牌
    - 简化模式：适用于web应用，客户端将用户导向认证服务器，用户同意授权，认证服务器将用户导向客户端，客户端通过令牌向认证服务器申请令牌
    - 客户端模式：适用于没有用户交互的客户端，客户端向认证服务器申请令牌

抽象类和接口、普通类的区别
```
抽象类体现继承关系，一个类只能单继承；接口体现实现关系，一个类可以多实现

抽象类中可以定义非抽象方法，供子类直接使用；接口的方法都是抽象，接口中的成员都有固定修饰符

抽象类中的成员变量可以实现多个权限 public private protected final等，接口中只能用 public static final修饰。

抽象类中的抽象方法的修饰符只能为public abstract或者protected abstract，默认为public abstract；

抽象类不能用来创建对象；抽象类可以包含属性、方法、构造方法，但是构造方法不能用于实例化，主要用途是被子类调用。

如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。

接口默认方法（default method）：JDK 1.8允许给接口添加非抽象的方法实现，但必须使用default关键字修饰；定义了default的方法可以不被实现子类所实现，但只能被实现子类的对象调用；如果子类实现了多个接口，并且这些接口包含一样的默认方法，则子类必须重写默认方法；

接口静态方法（static method）：JDK 1.8中允许使用static关键字修饰一个方法，并提供实现，称为接口静态方法。接口静态方法只能通过接口调用（接口名.静态方法名）。
```

Java中try catch finally的执行顺序
```
先执行try中代码发生异常执行catch中代码，最后一定会执行finally中代码，不管有没有出现异常，finally块中代码都会执行

当try和catch中有return时，finally会执行吗？
仍然会，finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，不管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的

finally如果包含return会怎样？
最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。
```

for和for i和foreach
```
for…in 语句用于对数组或者对象的属性进行循环操作。
语法：for (变量 in 对象){ 在此执行代码}
在这个for …in… 最大问题是不能赋值

for循环是对数组的元素进行循环，而不能引用于非数组对象。
语法：for(int 变量初始值;条件;递增或递减){ 在此执行代码}
在这个for 这个是可以赋值的

foreach会生成lamada的内部的静态方法，然后再根据这个静态方法生成一个类似闭包的函数，然后再调用。
foreach支持遍历删除，但是没有fori的随机访问了。
```

switch是否能作用在byte上，是否能作用在long上，是否能作用在String上？
```
switch支持以下类型：
基本数据类型：int，byte，short，char
基本数据类型封装类：Integer，Byte，Short，Character
枚举类型：Enum（JDK 5+开始支持）
字符串类型：String（JDK 7+ 开始支持）

switch支持使用byte类型，不支持long类型，String支持在java1.7引入
```

静态内部类、内部类、匿名内部类，为什么内部类会持有外部类的引用？持有的引用是this？还是其它？
```
静态内部类：使用static修饰的内部类
匿名内部类：使用new生成的内部类
因为内部类的产生依赖于外部类，持有的引用是类名.this。
```

**String类为什么是final的**
主要目的就是保证String是不可变（immutable）。不可变就是第二次给一个String 变量赋值的时候，不是在原内存地址上修改数据，而是重新指向一个新对象，新地址。

- 从内存角度来看：字符串常量池的要求：创建字符串时，如果该字符串已经存在于池中，则将返回现有字符串的引用，而不是创建新对象。多个String变量引用指向同一个内存地址。如果字符串是可变的，用一个引用更改字符串将导致其他引用的值错误。这是很危险的。
- 缓存Hashcode ：字符串的Hashcode在java中经常配合基于散列的集合一起正常运行，这样的散列集合包括HashSet、HashMap以及HashTable。不可变的特性保证了hashcode永远是相同的。不用每次使用hashcode就需要计算hashcode。这样更有效率。因为当向集合中插入对象时，是通过hashcode判别在集合中是否已经存在该对象了（不是通过equals方法逐个比较，效率低）。
- 方便其它类使用 ：其他类的设计基于string不可变，如set存储string，改变该string后set包含了重复值。
- 安全性 ：String被广泛用作许多java类的参数，例如网络连接、打开文件等。如果对string的某一处改变一不小心就影响了该变量所有引用的表现，则连接或文件将被更改，这可能导致严重的安全威胁。 不可变对象不能被写，所以不可变对象自然是线程安全的，因为不可变对象不能更改，它们可以在多个线程之间自由共享。

总结 ：由于效率和安全性的原因，字符串被设计为不可变。

### java new String的时候会创建几个对象
```
String a = "a";
```
创建了1个对象：常量池1个
ldc 出现了1次

```
String a = new String("a");
```
创建了2个对象：常量池1个，堆1个
ldc 出现了1次

```
String a = "a"; String b = "b"; String c = "a" + "b";
```
创建了3个对象：常量池3个
字面量 "a" + "b" 在编译前就已经识别为 ”ab“ 了， 被放到常量池中。所以只有一个对象 ：ab
ldc 出现3次

字符串拼接操作的总结
- 常量 与 常量 的拼接结果在 常量池，原理是 编译期 优化；
- 常量池 中不会存在相同内容的常量；
- 只要其中一个是变量，结果在堆中。 如： String s2 = s1+"DEF" ;
- 变量拼接的原理 是StringBuilder 。
- 如果拼接的结果是调用 intern() 方法，则主动将常量池中 还没有的字符串 对象放入池中，并返回地址。

```
String a = "a"; String b = "b"; String c = a + b;
```
严格来说：创建了4个对象（还创建了StringBuilder对象）：常量池2个，堆2个
StringBuilder.toString 方法的实现是 new String();

```
String str = new String(“a”) + new String(“b”) 
```
创建了6个String对象：常量池2个，堆4个

ldc 出现了2次，表示常量池的 a，b

堆里有 StringBuilder，a，b，ab（StringBuilder.toString()生成，不会在常量池创建）

### 深拷贝和浅拷贝
深拷贝和浅拷贝都是指的对象的拷贝
- 浅拷贝：只会拷贝基本数据类型的值和实际的引用地址,实际指向还是同一个对象,对基本类型数据修改,原对象也会紧接着修改
- 深拷贝：基本数据类型和所指向的对象都会进行复制,内部实际指向的不是一个对象,所以在做修改时两者不会同时改变

### i++和++i哪个线程不安全
i++和++i都是i=i+1的意思，但是过程有些许区别：
- i++：先赋值再⾃加。（例如：i=1；a=1+i++；结果为a=1+1=2，语句执⾏完后i再进⾏⾃加为2）
- ++i：先⾃加再赋值。（例如：i=1；a=1+++i；结果为a=1+（1+1）=3，i先⾃加为2再进⾏运算）
- 但是在单独使⽤时没有区别：如for(int i=0;i<10;i++）{}和for(int i=0;i<10;++i) { }没有区别。

i++和++i的线程安全分为两种情况：
1. 如果i是局部变量（在⽅法⾥定义的），那么是线程安全的。因为局部变量是线程私有的，别的线程访问不到，其实也可以说没有线程安不安全之说，因为别的线程对他造不成影响。
2. 如果i是全局变量（类的成员变量），那么是线程不安全的。因为如果是全局变量的话，同⼀进程中的不同线程都有可能访问到。如果有⼤量线程同时执⾏i++操作，i变量的副本拷贝到每个线程的线程栈，当同时有两个线程栈以上的线程读取线程变量，假如此时是1的话，那么同时执⾏i++操作，再写⼊到全局变量，最后两个线程执⾏完，i会等于3⽽不会是2，所以，出现不安全性


## Spring和SpringBoot和SpringCloud

### Spring类的加载机制

    java中的类加载器负载加载来自文件系统、网络或者其他来源的类文件。
    jvm的类加载器默认使用的是双亲委派模式。
    三种默认的类加载器Bootstrap ClassLoader、Extension ClassLoader和System ClassLoader（Application ClassLoader）
    每一个中类加载器都确定了从哪一些位置加载文件

### Java类加载过程，双亲委派机制，如何打破双亲委派

* 如果一个类加载器接收到了类加载的请求，它首先把这个请求委托给他的父类加载器去完成，每个层次的类加载器都是如此，因此所有的加载请求都应该传送到顶层的启动类加载器中，只有当父加载器反馈自己无法完成这个加载请求（它在搜索范围中没有找到所需的类）时，子加载器才会尝试自己去加载。
* 好处：java类随着它的类加载器一起具备了一种带有优先级的层次关系。例如类java.lang.Object，它存放在rt.jar中，无论哪个类加载器要加载这个类，最终都会委派给启动类加载器进行加载，因此Object类在程序的各种类加载器环境中都是同一个类。相反，如果用户自己写了一个名为java.lang.Object的类，并放在程序的Classpath中，那系统中将会出现多个不同的Object类，java类型体系中最基础的行为也无法保证，应用程序也会变得一片混乱。
* 实现：在java.lang.ClassLoader的loadClass()方法中，先检查是否已经被加载过，若没有加载则调用父类加载器的loadClass()方法，若父加载器为空则默认使用启动类加载器作为父加载器。如果父加载失败，则抛出ClassNotFoundException异常后，再调用自己的findClass()方法进行加载。
* 打破双亲委派机制则不仅要继承ClassLoader类，还要重写loadClass和findClass方法。

### 反射中，Class.forName和classloader的区别
class.forname和classloader的调用关系
```
Class.forName(String name)->
Class.forName(className, true, ClassLoader.getClassLoader(caller))->
Class.forName0(String name, boolean initialize, ClassLoader loader)->
ClassLoader.loadClass()
```
- class.forName()前者除了将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块。而classLoader只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容,只有在newInstance才会去执行static块。
- Class.forName得到的class是已经初始化完成的，Classloder.loaderClass得到的class是还没有链接的。

### Spring IOC加载全过程：

    Application加载xml
    AbstractApplicationContext的refresh函数载入Bean定义过程
    AbstractApplicationContext子类的refreshBeanFactory()方法
    AbstractRefreshableApplicationContext子类的loadBeanDefinitions方法
    AbstractBeanDefinitionReader读取Bean定义资源
    资源加载器获取要读入的资源
    XmlBeanDefinitionReader加载Bean定义资源
    DocumentLoader将Bean定义资源转换为Document对象
    XmlBeanDefinitionReader解析载入的Bean定义资源文件
    DefaultBeanDefinitionDocumentReader对Bean定义的Document对象解析
    BeanDefinitionParserDelegate解析Bean定义资源文件中的元素
    BeanDefinitionParserDelegate解析元素
    解析元素的子元素
    。。。

### Spring的@Transactional如何实现的：

    实现@Transactional原理是基于spring aop，aop又是动态代理模式的实现。

### Spring的事务传播级别

    事务传播行为(为了解决业务层方法之间互相调用的事务问题)：当事务方法被另一个事务方法调用时,必须指定事务应该如何传播
    支持当前事务的情况:
    TransactionDefinition.PROPAGATION_REQUIRED: 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
    TransactionDefinition.PROPAGATION_SUPPORTS: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
    TransactionDefinition.PROPAGATION_MANDATORY: 如果当前存在事务，则加入该事务;如果当前没有事务，则抛出异常。(mandatory:强制性)
    不支持当前事务的情况:
    TransactionDefinition.PROPAGATION_REQUIRES_NEW: 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
    TransactionDefinition.PROPAGATION_NOT_SUPPORTED: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
    TransactionDefinition.PROPAGATION_NEVER: 以非事务方式运行，如果当前存在事务，则抛出异常。
    其他情况:
    TransactionDefinition.PROPAGATION_NESTED: 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

### AOP（动、静、cglib）
JDK动态代理只能对实现了接口的类生成代理，而不能针对类；
CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法（继承）实现增强，但是因为采用的是继承，所以该类或方法最好不要声明成final，对于final类或方法，是无法继承的；

JDK动态代理：利用拦截器(拦截器必须实现InvocationHanlder)加上反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
CGLiB动态代理：利用ASM开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

**Cglib实现原理**

CGLib不能对声明为final的方法进行代理，因为CGLib原理是动态生成被代理类的子类。
到jdk8的时候，jdk代理效率高于CGLIB代理，总之，每一次jdk版本升级，jdk代理效率都得到提升，而CGLIB代理消息确有点跟不上步伐。

Spring在选择用JDK还是CGLiB的依据：
当Bean实现接口时，Spring会用JDK的动态代理。
当Bean没有实现接口时，Spring使用CGlib是实现。
如果Bean实现了接口，强制使用CGlib时，（添加CGLIB库，在spring配置中加入<aop:aspectj-autoproxy proxy-target-class="true"/>）。

### SpringMVC 默认创建 Bean 是单例的，在高并发情况下是如何保证性能的
```
spring中的bean默认是单例的，通常对单例进行多线程访问时，为了线程安全而采用同步机制，以时间换空间的方式，而Spring中是利用ThreadLocal来以空间换取时间，为每一个线程提供变量副本，来保证变量副本对于某一线程都是线程安全的。

用ThreadLocal是为了保证线程安全，实际上ThreadLoacal的key就是当前线程的Thread实例。

虽然spring对象是单例的，但类里面方法对每个线程来说都是独立运行的，不存在多线程问题，只有成员变量有多线程问题，所以方法里面如果有用到成员变量就要考虑用安全的数据结构。

单例模式是spring推荐的配置，单利模式因为大大节省了实例的创建和销毁，它在高并发下能极大的节省资源，提高服务抗压能力。
```

**问题拓展：把当前用户对象作为成员变量放在Controller里行不行？**
```
如果回答“行”，那就可以pass了，后面所有问题都不用问了；

如果回答“不行”，那么就问为什么；正确答案其实是默认不行，因为Controller默认单例，成员变量在多线程下会有并发安全问题，但可以使用@Scope("prototype")注解成多例对象。

对线程安全一知半解的多半会死在这里，对Spring的scope不了解的也会死在这里。
```

### spring依赖注入原理：

    1.构造器注入
    2.接口注入
    3.set方法参数注入

### spring循环依赖是如何解决的：

    Spring三级缓存，也就是三个Map集合类：
    singletonObjects：第一级缓存，里面放置的是实例化好的单例对象；
    earlySingletonObjects：第二级缓存，里面存放的是提前曝光的单例对象；
    singletonFactories：第三级缓存，里面存放的是要被实例化的对象的对象工厂。
    循环依赖问题的解决也是基于Java的引用传递，当我们获取到对象的引用时，对象的field或者属性是可以延后设置的。即Spring是先将Bean对象实例化之后再设置对象属性的
    
    举个例子：
    ```
    假设对象A中有属性是对象B，对象B中也有属性是对象A，即A和B循环依赖。
    创建对象A，调用A的构造，并把A保存下来。
    然后准备注入对象A中的依赖，发现对象A依赖对象B，那么开始创建对象B。
    调用B的构造，并把B保存下来。
    然后准备注入B的构造，发现B依赖对象A，对象A之前已经创建了，直接获取A并把A注入B（注意此时的对象A还没有完全注入成功，对象A中的对象B还没有注入），于是B创建成功。
    把创建成功的B注入A，于是A也创建成功了。
    于是循环依赖就被解决了。
    ```

    spring中的循环依赖会有3种情况
    1.构造器循环依赖（不能解决）
        构造器的循环依赖是不可以解决的，spring容器将每一个正在创建的bean标识符放在一个当前创建bean池中，在创建的过程一直在里面，如果在创建的过程中发现已经存在这个池里面了，这时就会抛出异常表示循环依赖了。导致问题：当存在循环依赖时，Spring将无法决定先创建哪个bean，这种情况下，Spring将产生异常BeanCurrentlyInCreationException。
    2.setter和@Autowire循环依赖
        通过spring容器提前暴露刚完成构造器注入，但并未完成其他步骤（如setter注入）的bean来完成的，而且只能决定单例作用域的bean循环依赖
    3.prototype范围的依赖
        对于“prototype”作用域bean，Spring容器无法完成依赖注入，因为“prototype”作用域的bean，Spring容器不进行缓存，因此无法提前暴露一个创建中的Bean。
        说明：singleton是默认作用域，在使用singleton时，Spring容器只存在一个可共享的Bean实例。

### spring为什么使用三级缓存？（解决循环依赖）

    1.不支持循环依赖情况下，只有一级缓存生效，二三级缓存用不到
    2.二三级缓存就是为了解决循环依赖，且之所以是二三级缓存而不是二级缓存，主要是可以解决循环依赖对象需要提前被aop代理，以及如果没有循环依赖，早期的bean也不会真正暴露，不用提前执行代理过程，也不用重复执行代理过程。

Spring 为何需要三级缓存解决循环依赖，而不是二级缓存

    如果没有AOP的话确实可以两级缓存就可以解决循环依赖的问题，如果加上AOP，两级缓存是无法解决的，不可能每次执行singleFactory.getObject()方法都给我产生一个新的代理对象，所以还要借助另外一个缓存来保存产生的代理对象。
    singleFactory.getObject()返回的是代理对象，那么注入的也应该是代理对象，我们可以看到注入的确实是经过CGLIB代理的AService对象。

### Spring和Springboot区别

    1.Spring Boot提供极其快速和简化的操作,让 Spring 开发者快速上手。
    2.Spring Boot提供了 Spring 运行的默认配置。
    3.Spring Boot为通用 Spring项目提供了很多非功能性特性,例如:嵌入式 Serve、Security、统计、健康检查、外部配置等等。
    4.嵌入Tomcat, Jetty Undertow 而且不需要部署他们。
    5.提供的“starters” poms来简化Maven配置

### SpringBoot启动过程全流程
Springboot 启动过程，其核心就是IOC容器初始化过程，所以可以把上诉过程简化为

第一步，创建IOC容器；

IOC容器是由类AnnotationConfigServletWebServerApplicationContext初始化的一个实例，其中包含了bean工厂、类加载器、bean定义信息和环境变量等主要信息。

通过AnnotationConfigServletWebServerApplicationContext的初始化，IOC容器初始化了一些属性，设置了一些特定的bean定义信息。其主要的作用，还是为后续的IOC容器创建Bean做准备。

第二步，准备IOC容器相关信息

在项目启动后，进行一系列的初始化。包括SpringApplication实例准备，listener监听器准备，environment环境准备。

第三步，刷新IOC容器，也就是我们通常说的bean的初始化过程

加载当前主启动类下的所有类文件到IOC容器：经过ConfigurationClassParser类中的parse方法调用AnnotatedBeanDefinition类型的parse方法，我们完成了对当前项目工程下所有类，注册到IOC容器中的操作。

加入Spring自带的框架类到IOC容器（自动装配）：在ConfigurationClassParser类中的执行方法为：this.deferredImportSelectorHandler.process(); 这里用到Spring Factories 机制，见Springboot自动配置

关于bean信息注册的方法，基本都在ConfigurationClassParser这个类中，processConfigBeanDefinitions实现bean信息注册的入口方法在ConfigurationClassParser的parse方法中：

第四步，完成刷新IOC容器后处理

第五步，结束Springboot启动流程

### Springboot 怎么实现自动装载组件的

    通过Springboot的@EnableAutoConfiguration注解上@Import引入的AutoConfigurationImportSelector.class类来实现的

### Eureka 和 Nacos对比？怎么做选择？Eureka中高可用是怎么做的？进行的调优有哪些？原理是什么？

    都是服务注册发现中心，但是Nacos还可以用作配置中心，目前来看，建议使用Nacos，因为Eureka已经不在开源，而且性能上和高可用上没有Nacos方便。

### zookeeper

如何保证数据一致性

    消息广播阶段
    Leader 节点接受事务提交，并且将新的 Proposal 请求广播给 Follower 节点，收集各个节点的反馈，决定是否进行 Commit，在这个过程中，也会使用上一课时提到的 Quorum 选举机制。
    崩溃恢复阶段
    如果在同步过程中出现 Leader 节点宕机，会进入崩溃恢复阶段，重新进行 Leader 选举，崩溃恢复阶段还包含数据同步操作，同步集群中最新的数据，保持集群的数据一致性。
    整个 ZooKeeper 集群的一致性保证就是在上面两个状态之前切换，当 Leader 服务正常时，就是正常的消息广播模式；当 Leader 不可用时，则进入崩溃恢复模式，崩溃恢复阶段会进行数据同步，完成以后，重新进入消息广播阶段。
    
    副本控制协议：
    WARO(Write All Read one)机制：当Client请求向某副本写数据时(更新数据)，只有当所有的副本都更新成功之后，这次写操作才算成功，否则视为失败。
    Quorum机制：假设有N个副本，更新操作wi 在W个副本中更新成功之后，才认为此次更新操作wi 成功。称成功提交的更新操作对应的数据为：“成功提交的数据”。对于读操作而言，至少需要读R个副本才能读到此次更新的数据。
    其中，W+R>N ，即W和R有重叠。一般，W+R=N+1

为什么zookeeper使用奇数个节点
    
    避免脑裂时产生竞争

zookeeper选举算法：

    从3.4.0版本开始，ZooKeeper废弃了0、1、2这三种Leader选举算法，只保留了TCP版本的FastLeaderElection选举算法。
    1. 自增选举轮次
    2. 初始化选票
    3. 发送初始化选票
    4. 接收外部投票
    5. 判断选举轮次
    6. 选票PK
    7. 变更投票
    8. 选票归档
    9. 统计投票
    10. 更新服务器状态
    以上10个步骤就是FastLeaderElection的核心，其中步骤4-9会经过几轮循环，直到有Leader选举产生。

zookeeper是什么一致性，怎么实现的？
```
(2) 一致性分类
一致性是指从统外部读取系统内部的数据时，在一定约束条件下相同，即数据变动在系统内部各节点应该是同步的。根据一致性的强弱程度不同，可以将一致性级别分为如下几种：

① 强一致性（strong consistency）。任何时刻，任何用户都能读取到最近一次成功更新的数据。

② 单调一致性（monotonic consistency）。任何时刻，任何用户一旦读到某个数据在某次更新后的值，那么就不会再读到比这个值更旧的值。也就是说，可获取的数据顺序必是单调递增的。

③ 会话一致性（session consistency）。任何用户在某次会话中，一旦读到某个数据在某次更新后的值，那么在本次会话中就不会再读到比这个值更旧的值。会话一致性是在单调一致性的基础上进一步放松约束，只保证单个用户单个会话内的单调性，在不同用户或同一用户不同会话间则没有保障。

④ 最终一致性（eventual consistency）。用户只能读到某次更新后的值，但系统保证数据将最终达到完全一致的状态，只是所需时间不能保障。

⑤ 弱一致性（weak consistency）。用户无法在确定时间内读到最新更新的值。

ZooKeeper是一种强一致性的服务（通过sync操作），也有人认为是单调一致性（更新时的大多说概念），还有人为是最终一致性（顺序一致性），反正各有各的道理这里就不在争辩了。然后它在分区容错性和可用性上做了一定折中，这和CAP理论是吻合的。
```

Eureka和zookeeper有哪些不同
    
    ZooKeeper保证的是CP,Eureka保证的是AP
    ZooKeeper在选举期间注册服务瘫痪,虽然服务最终会恢复,但是选举期间不可用的
    Eureka各个节点是平等关系,只要有一台Eureka就可以保证服务可用,而查询到的数据并不是最新的

Dubbo隐式传参：
```
dubbo通过客户端向服务器端传递参数，传递参数时path,group,version,dubbo,token,timeout即可key有特殊处理，不能使用这几个特殊key
服务提供方使用：RpcContext.getContext().getAttachments();获取参数，
服务调用方使用：RpcContext.getContext().setAttachment(); 传递参数。
```

### Seata
**Seata底层实现、AT模式与TCC模式分别原理是什么，他们区别，以及各自优缺点。**
我们可以将Seata分布式事务的参与角色分为三个：TC（事务协调者，即Seata-Server），TM（事务管理器），RM（资源管理器）。了解完参与角色之后，我们还需要先了解下每个角色在分布式事务中负责的内容
- TC：事务协调者，负责事务的协调，根据TM最后的指令决定提交/回滚全局事务
- TM：事务管理器，负责开启全局事务，调用其他的RM资源以及通知TC是要提交/回滚全局事务
- RM：资源管理器，负责管理分支事务设计到的资源（即不属于TC本地事务中，但属于全局事务中的那部分资源）

在Seata分布式事务中，两阶段可以分为以下的两阶段
一阶段（prepare）：资源准备阶段
二阶段（commit/rollback）：根据全局事务执行的结果，将一阶段冻结的资源提交或回滚
Seata两阶段执行流程：
```
1. TM向TC请求开启全局事务，TC生成一个XID代表此次全局事务并返回给TM
2. XID会在微服务的调用链传递
3. RM将本地事务作为一个分支，通过XID注册到全局事务中，有TC来负责协调该分支
4. TM通过XID来告诉TC去提交/回滚该XID绑定的全局事务
5. TC通过XID找到对应的每一个分支事务，去协调RM去提交/回滚每个分支事务（此处的分支事务不是刚刚一阶段的本地事务，而是TM角度上的一个全局事务try+commit/cancel）
```

AT 模式基于 **支持本地 ACID 事务 的 关系型数据库：**
SEATA AT 模式需要 UNDO_LOG 表
一阶段 prepare 行为：在本地事务中，一并提交业务数据更新和相应回滚日志记录。
二阶段 commit 行为：马上成功结束，自动 异步批量清理回滚日志。
二阶段 rollback 行为：通过回滚日志，自动 生成补偿操作，完成数据回滚。

相应的，TCC 模式，不依赖于底层数据资源的事务支持：
一阶段 prepare 行为：调用 自定义 的 prepare 逻辑。
二阶段 commit 行为：调用 自定义 的 commit 逻辑。
二阶段 rollback 行为：调用 自定义 的 rollback 逻辑。

|	|AT	|TCC|
|---|---|---|
|全局锁	|需要	|不需要|
|回滚日志	|需要	|不需要|
|commit/cancel阶段代码实现	|不需要	|需要|
|是否需要开发者解决悬挂和空回滚问题	|不需要	|需要|
|性能	|低（需要全局锁导致）	|高（无锁）|

Saga 模式
Saga模式是SEATA提供的长事务解决方案，在Saga模式中，业务流程中每个参与者都提交本地事务，当出现某一个参与者失败则补偿前面已经成功的参与者，一阶段正向服务和二阶段补偿服务都由业务开发实现。
适用场景：
业务流程长、业务流程多
参与者包含其它公司或遗留系统服务，无法提供 TCC 模式要求的三个接口


### Sentinel
**Hystrx怎么实现与Sentinel怎么实现，他们底层 实现区别。**
Hystrix 通过线程池的方式，来对依赖(在我们的概念中对应资源)进行了隔离。这样做的好处是资源和资源之间做到了最彻底的隔离。缺点是除了增加了线程切换的成本，还需要预先给各个资源做线程池大小的分配。
Sentinel 对这个问题采取了两种手段:
通过并发线程数进行限制
和资源池隔离的方法不同，Sentinel 通过限制资源并发线程的数量，来减少不稳定资源对其它资源的影响。这样不但没有线程切换的损耗，也不需要您预先分配线程池的大小。当某个资源出现不稳定的情况下，例如响应时间变长，对资源的直接影响就是会造成线程数的逐步堆积。当线程数在特定资源上堆积到一定的数量之后，对该资源的新请求就会被拒绝。堆积的线程完成任务后才开始继续接收请求。

通过响应时间对资源进行降级
除了对并发线程数进行控制以外，Sentinel 还可以通过响应时间来快速降级不稳定的资源。当依赖的资源出现响应时间过长后，所有对该资源的访问都会被直接拒绝，直到过了指定的时间窗口之后才重新恢复。

Hystrix整个工作流如下：
```
构造一个 HystrixCommand或HystrixObservableCommand对象，用于封装请求，并在构造方法配置请求被执行需要的参数；
执行命令，Hystrix提供了4种执行命令的方法，后面详述；
判断是否使用缓存响应请求，若启用了缓存，且缓存可用，直接使用缓存响应请求。Hystrix支持请求缓存，但需要用户自定义启动；
判断熔断器是否打开，如果打开，跳到第8步；
判断线程池/队列/信号量是否已满，已满则跳到第8步；
执行HystrixObservableCommand.construct()或HystrixCommand.run()，如果执行失败或者超时，跳到第8步；否则，跳到第9步；
统计熔断器监控指标；
走Fallback备用逻辑
返回请求响应
```
Hystrix断路器使用时最常用的三个重要指标参数

在微服务中使用Hystrix 作为断路器时，通常涉及到以下三个重要的指标参数（这里是写在@HystrixProperties注解中，当然实际项目中可以全局配置在yml或properties中）

1、circuitBreaker.sleepWindowInMilliseconds
断路器的快照时间窗，也叫做窗口期。可以理解为一个触发断路器的周期时间值，默认为10秒（10000）。
2、circuitBreaker.requestVolumeThreshold
断路器的窗口期内触发断路的请求阈值，默认为20。换句话说，假如某个窗口期内的请求总数都不到该配置值，那么断路器连发生的资格都没有。断路器在该窗口期内将不会被打开。
3、circuitBreaker.errorThresholdPercentage
断路器的窗口期内能够容忍的错误百分比阈值，默认为50（也就是说默认容忍50%的错误率）。打个比方，假如一个窗口期内，发生了100次服务请求，其中50次出现了错误。在这样的情况下，断路器将会被打开。在该窗口期结束之前，即使第51次请求没有发生异常，也将被执行fallback逻辑。

综上所述，在以上三个参数缺省的情况下，Hystrix断路器触发的默认策略为：
在10秒内，发生20次以上的请求时，假如错误率达到50%以上，则断路器将被打开。（当一个窗口期过去的时候，断路器将变成半开（HALF-OPEN）状态，如果这时候发生的请求正常，则关闭，否则又打开）

其他：
设置全局的fallback：如果不设置@HystrixCommand(fallbackMethod = "testService_fallback",中的fallbackMethod，走全局的DefaultProperties

在触发熔断之后，会停止调用当前服务，当熔断到一定的时长会进入半熔断状态，部分请求根据规则调用当前服务，如果符合规则则认为服务恢复正常，关闭熔断。

**Sentinel**
在 Sentinel 里面，所有的资源都对应一个资源名称以及一个 Entry。Entry 可以通过对主流框架的适配自动创建，也可以通过注解的方式或调用 API 显式创建；每一个 Entry 创建的时候，同时也会创建一系列功能插槽（slot chain）。这些插槽有不同的职责，例如:

NodeSelectorSlot 负责收集资源的路径，并将这些资源的调用路径，以树状结构存储起来，用于根据调用路径来限流降级；
ClusterBuilderSlot 则用于存储资源的统计信息以及调用者信息，例如该资源的 RT, QPS, thread count 等等，这些信息将用作为多维度限流，降级的依据；
StatisticSlot 则用于记录、统计不同纬度的 runtime 指标监控信息；
FlowSlot 则用于根据预设的限流规则以及前面 slot 统计的状态，来进行流量控制；
AuthoritySlot 则根据配置的黑白名单和调用来源信息，来做黑白名单控制；
DegradeSlot 则通过统计信息以及预设的规则，来做熔断降级；
SystemSlot 则通过系统的状态，例如 load1 等，来控制总的入口流量；

**Sentinel如何做熔断限流、QPS设置的值是如何定的，有没有经过生产的压测。**
例，3台机器，8C 16G一台，4C 16G两台，8C的能承受3000qps,4C的能承受1500qps,那么理想状态总共可承载6000qps。
但是却只能设置1500qps，超过1500可能会导致两台4C的挂掉。

基于这种问题，我们需要一个集群限流模式，通过一个server来专门统计调用量和分配令牌，进而产生了集群模式。和单机的区别在于单机是每个实例中进行统计，集群是有一专门token server实例进行统计。其他client会向token server去请求token。当达到集群的总阀值，当前实例被block。限流基于上述问题，我们就可以设置总qps为6000。

sentinel的集群限流有两种身份：
- token client:流控客户端，用于向token server请求token,集群会返回给客户端结果，决定是否限流。 
- token server:集群流控服务端，处理来自client的请求，根据配置的集群规则判断是否应发token。

单机流控只有一种身份，每个sentinel都是一个token server。
集群限流的token server是单点，挂掉，会退化成单机本地限流模式。在ClusterFlowConfig的fallbackToLocalWhenFail参数配置。

阀值：两种模式：单机均摊和总体阀值

部署方式：
- 独立部署：单独启动一个token server来处理token client的请求。
- 嵌入式部署：在多个 sentinel-core 中选择一个实例设置为 token server，随着应用一起启动，其他的 sentinel-core 都是集群中 token client。

Sentinel 支持系统自适应限流，Hystrix 所不支持的。当系统负载较高的时候，如果仍持续让请求进入，可能会导致系统崩溃，无法响应。在集群环境下，负载均衡把本应这台机器承载的流量转发到其它的机器上去，如果这个时候其它的机器也处在一个边缘状态的时候，这个增加的流量就会导致这台机器崩溃，最后导致整个集群不可用。针对这个情况，Sentinel 提供了对应的保护机制，让系统的入口流量和系统的负载达到一个平衡，保证系统在能力范围之内处理最多的请求。

除了轮询负载均衡算法外，其它的算法比如hash都会导致流量到集群的每个节点都不一样，有的多有的少。集群流控虽然可以精确地控制整个集群的调用总量，结合单机限流兜底，可以更好地发挥流量控制的效果。

问题思考：可能出现令牌申请成功了，若不同节点硬件配置不一致呢？单机均摊可能会超出其最大承受限额。

此时想到的方案：监控所有节点的qps,avgRt，负载等。可按照最优负载进行自定义路由转发。

限流是对资源的一种保护，使其不会瘫痪。不可避免会出现block。

**Hystrix 和 Sentinel 对比**
共同特性：

隔离机制：二者都提供了隔离机制，Hystrix通过线程池活信号量来实现隔离机制，针对某个依赖服务的请求，全部会在一个线程池内部管理，信号量更轻量级一些；Sentinel的隔离机制更轻量级，支持通过不同的运行指标进行限流，例如通过控制QPS，系统负载，调用关系。
熔断降级：Sentinel与Hystrix都支持基于失败比率（异常比率）的熔断降级，在调用达到一定量级并且失败比率达到设定的阈值时自动进行熔断，此时所有对该资源的调用都会被阻塞，知道过了指定时间窗口后才启发性的恢复。Sentinel还支持基于平均响应时间的熔断降级，可以在服务响应时间持续标高的时候自动熔断，拒绝更多的请求，知道一段时间后才恢复。Hystrix除了会统计请求的失败比例，还会统计请求的超时次数，从这个角度看，Hystrix也提供基于响应时间的熔断机制。
实时指标：Hystrix的实时指标计算使用RxJava的事件响应机制来实现，Sentinel的实时指标计算是通过LeapArray来实现的，不过，二者使用的算法都是滑动窗口算法。
Hystrix的特性： 请求聚合和缓存：Hystrix提供了请求聚合和请求缓存功能，从一定程度上实现了整形功能。

Sentinel的特性：
流量控制：Sentinel可以针对不同的调用关系，以不同的运行指标（QPS，并发调用数，系统负载等）为基准，对资源调用进行流量控制，将随机的请求调整成合适的形状。从对流量控制的角度说，Sentinel是优于Hystrix的。
系统负载保护：Sentinel还提供了集群维度的系统负载保护功能，在系统的入口处对流量进行管理，如果系统的服务能力降低，这相应的限制能够提供的服务，从而保护系统不被流量“打挂”。


## JVM

JVM结构
    * 程序计数器：当前线程所执行的字节码的行号指示器，用于记录正在执行的虚拟机字节指令地址，线程私有。
    * Java虚拟栈：存放基本数据类型、对象的引用、方法出口等，线程私有。
    * Native方法栈：和虚拟栈相似，只不过它服务于Native方法，线程私有。
    * Java堆：java内存最大的一块，所有对象实例、数组都存放在java堆，GC回收的地方，线程共享。
    * 方法区：存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码数据等。（即永久带），回收目标主要是常量池的回收和类型的卸载，各线程共享

**触发GC的场景**

**垃圾回收过程**
JDK1.8之前，JVM中堆空间可以分为新生代、老年代和永久代（1.8后永久代改为元空间，元空间不再使用堆而是使用本地内存）。默认的，新生代 ( Young ) 与老年代 ( Old ) 的比例的值为 1:2 ，可以通过参数 –XX:NewRatio 配置。 
而新生代又分为一个Eden space 和两个survivor space（S0和S1）， 默认的，Eden : from : to = 8 : 1 : 1 ( 可以通过参数 –XX:SurvivorRatio 来设定)

1. 首先，任何新对象都分配到 eden 空间。两个幸存者空间开始时都是空的。
2. 当 eden 空间填满时，将触发一个Minor GC(年轻代的垃圾回收)。
3. 引用的对象将移动到第一个幸存者空间。清除 eden 空间时，将删除未引用的对象。
4. 在下一个Minor GC中，eden空间也会发生同样的事情。 未引用的对象将被删除， 引用的对象将移动到survivor空间。
但是，在这种情况下，它们被移动到第二个 survivor空间（S1）。
此外，来自第一个survivor空间（S0）上最后一个次要 GC的对象的年龄递增并移动到S1。一旦所有幸存的物体都被移动到S1，S0和eden 都会被清除。
5. 在下一个Minor GC 中，将重复相同的过程。然而，这一次survivor 空间切换了。 引用的对象将移动到 S0。幸存的对象已过期(未引用)。eden和S1被清除。
6. 重复前面的过程，在Minor GC 之后，当老化对象达到某个年龄阈值 时，它们将从新生代提升到老年代中。
7. 随着Minor GC的不断发生，对象将继续被提升到老年代空间。
8. 因此，这几乎涵盖了新生代的整个过程。最终，将在老年代上进行Full GC， 以清理和压缩该空间。

**GC算法，年轻代和老年代的GC算法区别**

**jvm垃圾收集器**
* Serial收集器： 单线程的收集器，收集垃圾时，必须stop the world，使用复制算法。
* ParNew收集器： Serial收集器的多线程版本，也需要stop the world，复制算法。
* Parallel Scavenge收集器： 新生代收集器，复制算法的收集器，并发的多线程收集器，目标是达到一个可控的吞吐量。如果虚拟机总共运行100分钟，其中垃圾花掉1分钟，吞吐量就是99%。
* Serial Old收集器： 是Serial收集器的老年代版本，单线程收集器，使用标记整理算法。
* Parallel Old收集器： 是Parallel Scavenge收集器的老年代版本，使用多线程，标记-整理算法。
* CMS(Concurrent Mark Sweep) 收集器： 是一种以获得最短回收停顿时间为目标的收集器，标记清除算法，运作过程：初始标记，并发标记，重新标记，并发清除，收集结束会产生大量空间碎片。
* G1收集器： 标记整理算法实现，运作流程主要包括以下：初始标记，并发标记，最终标记，筛选标记。不会产生空间碎片，可以精确地控制停顿。

**默认的垃圾收集器**
jdk1.7 默认垃圾收集器Parallel Scavenge(新生代)复制算法+Parallel Old(老年代)标记整理
jdk1.8 默认垃圾收集器Parallel Scavenge(新生代)复制算法+Parallel Old(老年代)标记整理
jdk1.9 默认垃圾收集器G1
jdk1.8可以使用CMS，1.9则提示过时

**何时进行JVM调优**
Heap内存（老年代）持续上涨达到设置的最大内存值；
Full GC 次数频繁；
GC 停顿时间过长（超过1秒）；
应用出现OutOfMemory等内存异常；
应用中有使用本地缓存且占用大量内存空间；
系统吞吐量与响应性能不高或不降。

优化原则：
大多数的Java应用不需要进行JVM优化；
大多数导致GC问题的原因是代码层面的问题导致的（代码层面）；
上线之前，应先考虑将机器的JVM参数设置到最优；
减少创建对象的数量（代码层面）；
减少使用全局变量和大对象（代码层面）；
优先架构调优和代码调优，JVM优化是不得已的手段（代码、架构层面）；
分析GC情况优化代码比优化JVM参数更好（代码层面）。

**生产常用的JVM调优参数**
-Xms4g：初始化堆内存大小为4GB。初始化堆内存大小，默认为物理内存的1/64(小于1GB)。
-Xmx4g：堆内存最大值为4GB。
-Xmn1200m：**设置年轻代大小为1200MB。**增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，Sun官方推荐配置为整个堆的3/8。
-Xss512k：**设置每个线程的堆栈大小。**JDK5.0以后每个线程堆栈大小为1MB，以前每个线程堆栈大小为256K。应根据应用线程所需内存大小进行调整。
-XX:NewRatio=4：**设置年轻代（包括Eden和两个Survivor区）与年老代的比值（除去持久代）。**设置为4，则年轻代与年老代所占比值为1：4，年轻代占整个堆栈的1/5
-XX:SurvivorRatio=8：**设置年轻代中Eden区与Survivor区的大小比值。
-XX:PermSize=100m：初始化永久代大小为100MB。
-XX:MaxPermSize=256m：设置持久代大小为256MB。
-XX:MaxTenuringThreshold=15：**设置垃圾最大年龄。**如果设置为0的话，则年轻代对象不经过Survivor区，直接进入年老代。
-XX:+DisableExplicitGC：禁止运行期显式地调用System.gc()来触发fulll GC。
注意: Java RMI的定时GC触发机制可通过配置-Dsun.rmi.dgc.server.gcInterval=86400来控制触发的时间。
-XX:CMSInitiatingOccupancyFraction=60：老年代内存回收阈值，默认值为68。
-XX:ConcGCThreads=4：CMS垃圾回收器并行线程线，推荐值为CPU核心数。
-XX:ParallelGCThreads=8：新生代并行收集器的线程数。
-XX:CMSFullGCsBeforeCompaction=4：指定进行多少次fullGC之后，进行tenured区 内存空间压缩。
-XX:CMSMaxAbortablePrecleanTime=500：当abortable-preclean预清理阶段执行达到这个时间时就会结束。
注意：在设置的时候，如果关注性能开销的话，应尽量把永久代的初始值与最大值设置为同一值，因为永久代的大小调整需要进行FullGC才能实现。

**G1与CMS的优缺点**
G1优点：
（1）可指定最大停顿时间
该值要合理的进行设置，如果设置的太小，会使得每次筛选出的回收集只占堆内存的很小一部分，收集速度跟不上分配速度，导致垃圾堆积，最终产生Full GC，通常该为100~300ms较为合理。
（2）按受益动态确定回收集
（3）低内存碎片

G1缺点：
（1）内存占用高
由于堆内存被划分为许多个小的Region分区数量，面对跨Region对象引用问题，每个Region分区都需要独立维护一份记忆集，使得用于维持G1正常运行的额外内存空间占到了总堆内存空间的10%~20%。
（2）执行负载高

CMS采用增量更新算法实现，而G1通过原始快照（SATB）算法实现。

为什么不使用G1，有没有真实对比使用过几种收集器，有没有在生产中真实调优，有什么工具处理的，
在小内存的机器上CMS大概率表现得比G1更好，而大内存则G1的优势更加明显。大小内存的平衡点在6-8G之间。

**有什么使用全链路测试（Jmeter只是其中一种），知不知道生产多久触发一次fullGC**
Java虚拟机的垃圾回收主要分为两种：Young Generation（YGC）和Full Generation（FGC）。
在JVM中，对于新创建的对象，会优先分配到年轻代（Young Generation）的内存区域。年轻代内存区域又被分为三个部分：一个Eden区和两个Survivor区。当Eden区内存满时，会触发一次YGC。年轻代垃圾回收的目的是尽可能快地释放那些生命周期短的对象，减小堆内存的使用量。
而当老年代（Tenured Generation）内存区域溢出时，会触发一次FGC。FGC会扫描整个堆内存，释放那些无法被引用的对象，并且对于剩下的对象进行整理，将它们紧密地排列在一起，释放出大块的空闲内存。FGC会使应用暂停运行，因此应该尽量将FGC的触发率降到最低。

正常的频率
YGC：10秒1次。
FGC：1天1次以内。

正常的GC时间
YGC：100ms以内
FGC：1秒以内

## 设计原则
**面向对象编程中，都有哪些设计原则**
SOLID 原则其实是用来指导软件设计的，它一共分为五条设计原则，分别是：
* 单一职责原则（SRP）（Single Responsibility Principle）
* 开闭原则（OCP）（Open Closed Principle）
* 里氏替换原则（LSP）
* 接口隔离原则（ISP）（Interface Segregation Principle）
* 依赖倒置原则（DIP）（Dependence Inversion Principle）
```
SOLID 原则：
    开闭原则 ：对扩展开放，对修改关闭。就是如果要修改原有的功能或者是扩展功能，尽量去扩展原有的代码，而不是修改原来已有的代码。
    里氏替换原则（Liskov Substitution Principle） ：任何子类对象都应该可以替换其派生的超类对象 。即，子类可以扩展父类的功能，但不要修改父类原有的功能。 也就是说，当一个子类继承父类后，尽量不要去重写它原有的方法。
    依赖转置（依赖倒置）原则 ：要面向接口编程，不要面向实现编程。两个模块交互时，都访问各自接口，而不是具体的实现类。
    单一职责原则 ：一个对象要专注于一种事情，不要让它担任太多责任。
    接口隔离原则 ：一个接口尽量只包含用户关心的内容。就是一个接口不要太庞大。
其他原则：
    迪米特法则 ：如果两个软件实体之间不是特别必要，尽量不要让他们直接通信。而是找个第三方进行转发，比如使用MQ（消息队列）。
    合成复用原则 ：如果在“组合/聚合”和“继承”之间做抉择时，优先选择“组合/聚合”。
```

### 开闭原则（Open Closed Principle）
一个软件实体，如类、模块和函数应该对扩展开放，对修改关闭。简单地说：就是当别人要修改软件功能的时候，使得他不能修改我们原有代码，只能新增代码实现软件功能修改的目的。

### 里氏替换原则（LSP）
所有引用基类的地方必须能透明地使用其子类的对象。简单地说：所有父类能出现的地方，子类就可以出现，并且替换了也不会出现任何错误。 例如下面 Parent 类出现的地方，可以替换成 Son 类，其中 Son 是 Parent 的子类。
```
Parent obj = new Son();
等价于
Son son  = new Son();
这样的例子在 Java 语言中是非常常见的，但其核心要点是：替换了也不会出现任何的错误。这就要求子类的所有相同方法，都必须遵循父类的约定，否则当父类替换为子类时就会出错。 这样说可能还是有点抽象，我举个例子。
public class Parent{
// 定义只能扔出空指针异常
public void hello throw NullPointerException(){
}
}
public class Son extends Parent{
public void hello throw NullPointerException(){
// 子类实现时却扔出所有异常
throw Exception;
}
}
```
上面的代码中，父类对于 hello 方法的定义是只能扔出空指针异常，但子类覆盖父类的方法时，却扔出了其他异常，违背了父类的约定。那么当父类出现的地方，换成了子类，那么必然会出错。
其实这个例子举得不是很好，因为这个在编译层面可能就有错误。但表达的意思应该是到位了。
而这里的父类的约定，不仅仅指的是语法层面上的约定，还包括实现上的约定。有时候父类会在类注释、方法注释里做了相关约定的说明，当你要覆写父类的方法时，需要弄懂这些约定，否则可能会出现问题。例如子类违背父类声明要实现的功能。比如父类某个排序方法是从小到大来排序，你子类的方法竟然写成了从大到小来排序。
里氏替换原则 LSP 重点强调：对使用者来说，能够使用父类的地方，一定可以使用其子类，并且预期结果是一致的。

### 接口隔离原则（Interface Segregation Principle）
的定义是：类间的依赖关系应该建立在最小的接口上。简单地说：接口的内容一定要尽可能地小，能有多小就多小。
举个例子来说，我们经常会给别人提供服务，而服务调用方可能有很多个。很多时候我们会提供一个统一的接口给不同的调用方，但有些时候调用方 A 只使用 1、2、3 这三个方法，其他方法根本不用。调用方 B 只使用 4、5 两个方法，其他都不用。接口隔离原则的意思是，你应该把 1、2、3 抽离出来作为一个接口，4、5 抽离出来作为一个接口，这样接口之间就隔离开来了。
那么为什么要这么做呢？我想这是为了隔离变化吧！ 想想看，如果我们把 1、2、3、4、5 放在一起，那么当我们修改了 A 调用方才用到 的 1 方法，此时虽然 B 调用方根本没用到 1 方法，但是调用方 B 也会有发生问题的风险。而如果我们把 1、2、3 和 4、5 隔离成两个接口了，我修改 1 方法，绝对不会影响到 4、5 方法。
除了改动导致的变化风险之外，其实还会有其他问题，例如：调用方 A 抱怨，为什么我只用 1、2、3 方法，你还要写上 4、5 方法，增加我的理解成本。调用方 B 同样会有这样的困惑。
在软件设计中，ISP 提倡不要将一个大而全的接口扔给使用者，而是将每个使用者关注的接口进行隔离。

### 依赖倒置原则（Dependence Inversion Principle）
高层模块不应该依赖底层模块，两者都应该依赖其抽象。抽象不应该依赖细节，即接口或抽象类不依赖于实现类。细节应该依赖抽象，即实现类不应该依赖于接口或抽象类。简单地说，就是说我们应该面向接口编程。通过抽象成接口，使各个类的实现彼此独立，实现类之间的松耦合。
如果我们每个人都能通过接口编程，那么我们只需要约定好接口定义，我们就可以很好地合作了。软件设计的 DIP 提倡使用者依赖一个抽象的服务接口，而不是去依赖一个具体的服务执行者，从依赖具体实现转向到依赖抽象接口，倒置过来。

### SOLID 原则的本质
我们总算把 SOLID 原则中的五个原则说完了。但说了这么一通，好像是懂了，但是好像什么都没记住。 那么我们就来盘一盘他们之间的关系。ThoughtWorks 上有一篇文章说得挺不错，它说：
* 单一职责是所有设计原则的基础，开闭原则是设计的终极目标。
* 里氏替换原则强调的是子类替换父类后程序运行时的正确性，它用来帮助实现开闭原则。
* 而接口隔离原则用来帮助实现里氏替换原则，同时它也体现了单一职责。
* 依赖倒置原则是过程式编程与面向对象编程的分水岭，同时它也被用来指导接口隔离原则。

简单地说：依赖倒置原则告诉我们要面向接口编程。当我们面向接口编程之后，接口隔离原则和单一职责原则又告诉我们要注意职责的划分，不要什么东西都塞在一起。当我们职责捋得差不多的时候，里氏替换原则告诉我们在使用继承的时候，要注意遵守父类的约定。而上面说的这四个原则，它们的最终目标都是为了实现开闭原则。

## 设计模式
常见的设计模式及适用场景：单例，工厂，观察者，建造者，代理，装饰器，责任链...

观察者模型：略

责任链模型：略

装饰器模型：略

建造者的好处：

    1. 相同的方法，不同的执行顺序，产生不同的事件结果时；
    2. 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时；
    3. 产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式非常合适；

## 数据结构
**常用的集合**
List,Set,hashmap，hashtable，ConcurrentHashMap

**HashMap、HashTable和concurrentHashMap的区别、底层实现原理**

**栈和队列的理解，如何用队列实现栈**

**Set 怎么实现**
拉链法，拉链法实现个 map

Set是如何实现元素不重复：
```
Set是一个接口，最常用的实现类就是HashSet，今天我们就拿HashSet为例。
将key-value对放入HashMap中时，首先根据key的hashCode()返回值决定该Entry的存储位置，
如果两个key的hash值相同，那么它们的存储位置相同。
如果这个两个key的equals比较返回true。那么新添加的Entry的value会覆盖原来的Entry的value，key不会覆盖。
且HashSet中add()中 map.put(e, PRESENT)==null 为false，HashSet添加元素失败。因此,如果向HashSet中添加一个已经存在的元素，新添加的集合元素不会覆盖原来已有的集合元素。
```

hashmap常见问题：

    * 1.7和1.8区别：1.8加入红黑树用尾插法（避免头插法环化，出现死循环），扩容后数据存储位置的计算方式也不一样
    * hashmap中默认的数组大小为16
    * 扩容机制：元素个数超过数组大小*loadFactor时，loadFactor的默认值为0.75
    * 第一次扩容：16\*0.75=12，就把数组的大小扩展为2\*16=32，即扩大一倍
    
    * jdk1.8 HashMap的底层实现：Entry数组 + 链表 + 红黑树。
    * 在HashMap类中有一个Entry的内部类（包含了key-value作为实例变量）。
    * put存储键值对时，先计算key的hashcode，定位到合适的数组索引，然后在该索引上的单向链表进行遍历，用equals函数比较key是否存在。如果存在，则覆盖原来的value值；如果不存在，则将新的Entry对象插入到链表头部
    * 当链表长度大于某个值（可能是8）时，链表会变为红黑树，链表的查询效率为O(n)，红黑树的查询效率为O(lgn)，

hashmap如何解决hash冲突：
```
hashmap的结构：
HashMap底层是一个数组，数组中的每个元素是链表。
HashMap中的节点是Map.Entry类型的，而不是简单的value，hashmap底层为数组结构，即一个Node<K,V>[] table数组（在jdk6中是Entry<K,V>数组），Node是Map.Entry的实现类。

hashmap使用“拉链法/链表法”解决hash冲突：
put元素进去的时候，计算key的hash值来获取bucketIndex，Entry 对象放入 table 数组的 bucketIndex 索引处。
如果 bucketIndex 索引处已经有了一个 Entry 对象，就会插入到链表的头部。新添加的 Entry 对象指向原有的 Entry 对象（产生一个 Entry 链）。

查询时：
HashMap里面没有出现hash冲突时，没有形成单链表时，hashmap查找元素很快,get()方法能够直接定位到元素。
key值不同的两个或多个Map.Entry<K,V>可能会插在同一个桶下面，但是当查找到某个特定的hash值的时候，下面挂了很多个<K,V>映射，怎么确定哪个是我要找的那个<K,V>呢？
出现单链表后，单个bucket 里存储的不是一个 Entry，而是一个 Entry 链，系统只能必须按顺序遍历每个 Entry，直到找到想搜索的 Entry 为止。如果恰好要搜索的 Entry 位于该 Entry 链的最末端（该 Entry 是最早放入该 bucket 中），那系统必须循环到最后才能找到该元素。
这就是HashMap底层结构的一个亮点，在它的Entry中不仅仅只是插入value的，他是插入整个Entry 的，里面包含key和value的，所以能识别同一个hash值下的不同Map.Entry,
```

为什么Hashmap底层使用数组：
```
1）数组效率高
在HashMap中，定位桶的位置是利用元素的key的哈希值对数组长度取模得到。此时，我们已得到桶的位置。显然数组的查找效率比LinkedList快。
2）可自定义扩容机制
采用基本数组结构，扩容机制可以自己定义，HashMap中数组扩容刚好是2的次幂，在做取模运算的效率高。
```

为什么现在没有人用hashtable:
    
    建议少用hashtable,在单线程中,无需做线程控制,运行效率更高;
    在多线程中,synchronized会造成线程饥饿,死锁,可以用concurrentHashMap替代

hashmap死锁会产生死锁吗，有则如何解决:

    会，
    jdk1.7多线程环境下，如果两个线程都发现HashMap需要重新调整大小，那么它们会同时试着去调整大小。
    在调整大小时，HashMap会将Entry对象不断的插入链表的头部。插入头部也主要是为了防止尾部遍历，否则这对key的HashCode相同的Entry每次添加还要定位到尾节点。
    如果条件竞争发送了，可能会出现环形链表，之后当我们get(key)操作时，就有可能发生死循环。
    
    JDK1.8是打乱顺序排一遍(JDK1.8之中，引入了高低位链表（双端链表）,先根据长度进行高低位分段，然后用尾插法)，
    但是如果恰好打乱后的数据某一段顺序和之前一样，还是会出现死锁问题！

ConcurrentHashMap的实现原理，为什么是线程安全的
    
    jdk1.8ConcurrentHashMap是数组+链表，或者数组+红黑树结构,
    并发控制使用Synchronized关键字和CAS（比较并替换Compare-and-swap）操作保证节点插入扩容的线程安全，在添加元素时候，采用synchronized来保证线程安全，然后计算size的时候采用CAS操作进行计算
    ConcurrentHashMap的扩容机制（最少是16，构造过渡节点，扩容2倍的数组，死循环转移节点，给不同的线程设置不同的下表索引进行扩容操作，synchronized同步锁锁住操作的节点）
    在JDK1.8中，ConcurrentHashMap摒弃了1.7分段锁，使用了乐观锁的实现方式。

**map 加上 lru 怎么加**
参见：[LRU缓存-最近最少使用策略](../../algorithm/case/LRU缓存-最近最少使用策略.md)

ArrayList和Vector的主要区别是什么？

```
ArrayList在Java1.2引入，用于替换Vector

Vector:
线程同步
当Vector中的元素超过它的初始大小时，Vector会将它的容量翻倍

ArrayList:
线程不同步，但性能很好
当ArrayList中的元素超过它的初始大小时，ArrayList只增加50%的大小
```

一个数组有大量重复元素，设计一个结构可以压缩空间
```
在多维数组中可能会存在大量空间存储无效数值的现象，为了节省空间，我们可以通过记录 行 ，列，值的方式，来压缩数组空间，这样形成的新数组我们称之为稀疏数组

算法步骤：
1.双层循环遍历，获取有效数据个数；
2.创建行数为count+1，列数为3的二维数组；
3.在第一行中第一列和第二列分别赋值原数组行数、原数组列数；
4.行数初始化为1，双层循环遍历，记录有效数据所在的行、列、值，赋值到新数组当前行，行数+1
5.还原数组：按层遍历新数组，根据稀疏数组的每行存储的原数组的行数和列数、值进行赋值

例：15×15的数组，[1][2] -----> 1, [2][3]----> 2 ,其余位置都为0,用代码来压缩数组成稀疏数组

package com.demo.array;

public class Demo5 {
    public static void main(String[] args) {

        int[][] arrays = new int[15][15];

        arrays[1][2] = 1;
        arrays[2][3] = 2;

        printArrays(arrays);

        int[][] sparseArrays = createSparseArrays(arrays);
        System.out.println("================================");
        System.out.println("压缩后的稀疏数组：");
        printArrays(sparseArrays);

        int[][] newArrays = getInitialArrays(sparseArrays);
        System.out.println("================================");
        System.out.println("还原后的数组：");
        printArrays(newArrays);
    }

    //    还原数组
    public static int[][] getInitialArrays(int[][] sparseArrays) {
        int[][] newArrays = new int[sparseArrays[0][0]][sparseArrays[0][1]]; 
        // 根据稀疏数组的第一行存储的原数组的行数和列数
        for (int row = 1; row < sparseArrays.length; row++) {
    newArrays[sparseArrays[row][0]][sparseArrays[row][1]] = sparseArrays[row][2];
        }
        return newArrays;
    }

    //    创建稀疏数组
    public static int[][] createSparseArrays(int[][] arrays) {
        int count = 0;
        for (int[] array : arrays
        ) {
            for (int value : array
            ) {
                if (value != 0) count++; // 获取有效数据个数
            }

        }
        int[][] sparseArrays = new int[count + 1][3];  // 创建稀疏数组
        count = 1;
        sparseArrays[0][0] = arrays.length; // 记录原数组行数
        sparseArrays[0][1] = arrays[0].length; // 记录原数组列数
        for (int row = 0; row < arrays.length; row++) {
            for (int cls = 0; cls < arrays[row].length; cls++) {
                if (arrays[row][cls] != 0) {
                    sparseArrays[count][0] = row;  // 记录有效数据的行
                    sparseArrays[count][1] = cls; // 记录有效数据的列
                    sparseArrays[count][2] = arrays[row][cls];
                    count++;
                }
            }
        }
        return sparseArrays;
    }

    //    打印数组
    public static void printArrays(int[][] arrays) {
        for (int row = 0; row < arrays.length; row++) {
            for (int cls = 0; cls < arrays[row].length; cls++) {
                System.out.print(arrays[row][cls] + "\t");
            }
            System.out.println();  // 换行
        }
    }
}
```

## 网络结构

**HTTP 重定向**
在 HTTP 中，服务器可以通过返回一个重定向响应来进行重定向。这个重定向响应有一个以 3 开头的状态码 ，并且有一个 Location 头字段 表示要重定向到的位置。 
浏览器接收到这个重定向之后，会立即加载 Location 中指定的 URL。通常这一过程耗时极端，用户基本注意不到这个过程。

重定向状态码及含义：重定向相关的状态码都是以 3 开头的，主要有以下 9 种状态码：
300 Multiple Choices 当请求的 URL 对应有多个资源时（如同一个 HTML 的不同语言的版本），返回这个代码时，可以返回一个可选列表，这样用户可以自行选择。通过 Location 头字段可以自定首选内容。
301 Moved Permanetly 当前请求的资源已被移除时使用，响应的 Location 头字段会提供资源现在的 URL。直接使用 GET 方法发起新情求。
302 Found 与 301 类似，但客户端只应该将 Location 返回的 URL 当做临时资源来使用，将来请求时，还是用老的 URL。直接使用 GET 方法发起新情求。
303 See Other 用于在 PUT 或者 POST 请求之后进行重定向，这样在结果页就不会再次触发重定向了。
304 Not Modified 资源未修改，表示本地缓存仍然可用。
305 Use Proxy 用来表示必须通过一个代理来访问资源，代理的位置有 Location 头字段给出
306 Switch Proxy 在最新版的规范中，306 状态码已经不再被使用。最初是指“后续请求应使用指定的代理”。
307 Temporary Redirect 与 302 类似，但是使用原请求方法发起新情求。
308 Permanent Redirect 与 301 类似，但是使用原请求方法发起新情求。

永久重定向类
301 和 308 都属于永久重定向。永久重定向意味着原始 URL 不再可用，替换成了一个新的内容。所以搜索引擎、聚合内容阅读器以及其他爬虫识别这两个状态码时，会更新旧 URL 的资源。
划重点：这个就是永久重定向和临时重定向的区别。
规范中，301 本来不允许改变请求方法，但是已有的浏览器厂商都使用了 GET 方法进行新的请求。所以创建了 308 用来处理需要使用非 GET 进行重定向的场景。

临时重定向类
302/303/307 都属于临时重定向。有时，当原有资源因为一些不可预测的原因而临时无法访问时，可以通过临时重定向的方式将请求转移到另一个地方。搜索引擎和爬虫不应该记住这个临时的连接。
此外，临时重定向还可以用来在创建、修改和删除时展示临时的进度页，这里通常使用 303。
302 和 307 的关系类似于 301 和 308，参见上文。

其他类型的重定向方式
HTTP 是最简易使用的重定向方式，但是有些时候我们并不能够操作服务端。好在，除了 HTTP 重定向外，还有两种方式：
通过<meta>进行 HTML 重定向和通过 DOM 的 JS 重定向。

HTML 重定向
如下代码所示，我们可以通过在<meta>元素上设置http-equiv="Refresh可以实现页面的重定向
```
<head>
  <meta http-equiv="Refresh" content="0; URL=https://example.com/">
</head>
```
content属性需要以数字开头，表示多少秒之后重定向到指定页面。通常我们设置为 0。注意，这一方式只适用于 HTML

JavaScript 重定向
这个大家都用过，使用window.location可以重定向页面。

使用 Nginx 配置 301 永久重定向
```
server {
 listen 80;
 server_name example.com;
 return 301 $scheme://www.example.com$request_uri;
}

也可以通过rewrite指令指定目录和页面进行重定向：
rewrite ^/images/(.*)$ https://images.example.com/$1 redirect;
rewrite ^/images/(.*)$ https://images.example.com/$1 permanent;
```

浏览器会缓存“301”永久重定向
这所以会这样，这是因为浏览器会缓存“301”永久重定向。
可以看出除了 Safari 重启/修改时间之后，能够使用新的重定向，Chrome/Firefox 都会无限期的缓存 301 重定向。

如果我们没有提供明确的缓存头，浏览器就会默认永久缓存 301 响应，因为 301 是永久重定向的意思。

优雅地使用 301
前面解释浏览器为什么会缓存 301 重定向时，已经隐晦地提到了这一方法。
既然浏览器认为这是一个可以缓存的资源，并且我们可以通过缓存头来控制。那么在使用 301 时，我们将其设置为不缓存就可以了。比如设置 Cache-Control: no-store 或 Cache-Control: no-cache。
```
location /original-page {
+ add_header Cache-Control no-store;
  # 永久重定向
  rewrite ^/original-page http://redirect.example.com/301 permanent;
  # 临时重定向
  # rewrite ^/original-page http://redirect.example.com/302 redirect;
}
```


### tcp和udp
参见：[TCP与UDP](../../computer-network/TCP与UDP.md)

**网络中哪些协议**

**tcp三次握手，四次挥手，为什么四次挥手要多一次**

**为什么不使用udp，udp不是更快吗**

**tcp如何保证可靠性的**

**tcp发送的报文一定是按顺序的吗，如果乱序了怎么办**
考虑这么一种场景，应该说互联网上经常发生，不可避免，比如说我发送一个很大的文件，也或者说下载，几m大，也不是说很大，tcp报文是有上限的，你应该理解，6万多的字节放不下这个文件，需要分成好几个包去发送，因为两地之间路由器非常多，中间有某些数据在进行路由时晚到了，这是非常容易发生的情况，那你认为tcp是如何保证数据的顺序的正确性

**https三次握手，服务器端第三次握手会持续到什么时候**

### http和https
**http 500状态码**

**HTTPS的加密方式**
```
https = 非对称加密➕数字摘要➕数字签名➕对称加密
```

**CA证书是什么，上面有啥内容**
```
ca证书是为了防止中间人攻击由可信第三方使用私钥对服务器公钥数字签名颁发
用户接收到服务端传来的ca证书后，会根据本地已经存在的ca机构公钥解密得到服务器公钥，还会检查证书所颁发的域名与当前域名是否一致，是否在证书有效期以内
```

**http和https的区别**
```

```

**DDOS攻击是怎么样的**
```
ddos分布式拒绝服务攻击，利用分布式的大量主机同时请求服务试图耗尽服务器资源导致服务无法为正常用户提供。分为直接型ddos和反射型ddos
```

**抓取HTTP报文，怎么抓取HTTPS明文**

**socket 结构**
两对，发送方IP和端口接收方IP和端口，socket 四元组而已

### 如何理解rpc框架，或者说rpc框架应该包括哪些功能

**自定义的rpc是基于什么协议传输数据的**
自定义RPC可以基于TCP协议也可以基于HTTP协议

**为什么不使用http**
HTTP发明的初衷是为了传送超文本的资源，协议设计的比较复杂，参数传递的方式效率也不高。开源的RPC框架针对远程调用协议上的效率会比HTTP快很多。

HTTP需要事先通知，修改Nginx/HAProxy配置。RPC能做到自动通知，不影响上游。

HTTP大部分是通过Json来实现的，字节大小和序列化耗时都比Thrift要更消耗性能。RPC，可以基于Thrift实现高效的二进制传输。


## 数据库
### 数据库隔离级别
隔离级别：读未提交，读已提交，可重复读，串行化四个！默认是可重复读
Oracle默认的隔离级别：提交读
mysql默认隔离级别：repeatable可重复读，（mysql ---repeatable,oracle,sql server ---read commited）
mysql binlog的格式三种：statement,row,mixed
为什么mysql用的是repeatable而不是read committed:在 5.0之前只有statement一种格式，而主从复制存在了大量的不一致，故选用repeatable
为什么默认的隔离级别都会选用read commited 原因有二：repeatable存在间隙锁会使死锁的概率增大，在RR隔离级别下，条件列未命中索引会锁表！而在RC隔离级别下，只锁行

### 数据库索引
MySQL 索引类型有：唯一索引，主键（聚集）索引，非聚集索引，全文索引。
聚合索引:数据行的物理顺序与列值（一般是主键的那一列）的逻辑顺序相同，一个表中只能拥有一个聚集索引。

**MySQL建表时，不指定主键，能否建表成功？**
可以，innoDB会自动帮你创建一个不可见的、长度为6字节的row_id，而且这个row_id是由InnoDB维护全局的dictsys.row_id，每次插入一条数据时都会让全局row_id加一（未定义主键的表会使用全局row_id作为主键id）。
但是如果全局row_id一直涨，直到涨到2的48次方-1时，这时候再加一就会让低48位的row_id都为0，此时如果再插入一条数据，它拿到的row_id就是0，这样的话就有可能存在主键冲突的。
所以为了避免这种隐患，每个表都需要一个主键。

### 索引回表
直接在索引中不能拿到数据，索引中存储的为主键，必须根据主键值再查一遍

### 索引覆盖
直接在索引中能拿到数据就是索引覆盖

### 索引失效
1、违反最左前缀法则（如果索引有多列，要遵守最左前缀法则 ，即查询从索引的最左前列开始并且不跳过索引中的列）
2、在索引列上做任何操作 （如计算、函数、(自动or手动)类型转换等操作，会导致索引失效从而全表扫描）
3、索引范围条件右边的列（索引范围条件右边的索引列会失效 ）
4、尽量使用覆盖索引（只访问索引查询(索引列和查询列一致)，减少select*）
5、使用不等于(!=、<>)（mysql在使用不等于(!=、<>)的时候无法使用索引会导致全表扫描(除覆盖索引外)）
6、like以通配符开头('%abc')
7、字符串不加单引号索引失效
8、or连接
9、索引字段上使用is null， is not null，可能导致索引失效。

### 联合索引为什么是最左侧匹配（B+树左侧匹配）
索引会根据 age, height, weight从左到右排序，个索引是一个非聚簇索引，查询时需要回表

### SQL里的like走不走索引？为什么不能从左侧匹配？
部分走，`xx%` 右侧匹配可以走，
MySQL在查询时会根据索引进行优化，但是如果使用左模糊匹配，MySQL就无法使用索引，只能采用全表扫描的方式进行查询

### 如果非要like的效果，但SQL的执行效率就是提不上去了，怎么办？
这个问题其实是“没有银弹的”，也即没有标准答案，要视具体场景、具体技术架构、具体客户而定，水货遇到这种问题一般是直接懵逼，但凡能说出2-3个值得尝试的方案的就至少不是水货了。

### 数据库隔离级别算法mvcc
mvcc，也就是多版本并发控制，是为了在读取数据时不加锁来提高读取效率和并发性的一种手段。

数据库并发有以下几种场景：
- 读-读：不存在任何问题。
- 读-写：有线程安全问题，可能出现脏读、幻读、不可重复读。
- 写-写：有线程安全问题，可能存在更新丢失等。

mvcc实现原理：基于undolog、版本链、readview。

在mysql存储的数据中，除了我们显式定义的字段，mysql会隐含的帮我们定义几个字段。
- trx_id:事务id，每进行一次事务操作，就会自增1。
- roll_pointer:回滚指针，用于找到上一个版本的数据，结合undolog进行回滚。

readview中的参数：
- m_ids：活跃的事务就是指还没有commit的事务。
- max_trx_id：例如m_ids中的事务id为（1，2，3），那么下一个应该分配的事务id就是4，max_trx_id就是4。
- creator_trx_id：执行select读这个操作的事务的id。

trx_id表示要读取的事务id
- 如果要读取的事务id等于进行读操作的事务id，说明是我读取我自己创建的记录，当然是可以的
- 如果要读取的事务id小于最小的活跃事务id，说明要读取的事务已经提交，那么可以读取。
- max_trx_id表示生成readview时，分配给下一个事务的id，如果要读取的事务id大于max_trx_id，说明该id已经不在该readview版本链中了，故无法访问。
- m_ids中存储的是活跃事务的id，如果要读取的事务id不在活跃列表，那么就可以读取，反之不行。

mvcc如何实现RC和RR的隔离级别
- RC的隔离级别下，每个快照读都会生成并获取最新的readview。
- RR的隔离级别下，只有在同一个事务的第一个快照读才会创建readview，之后的每次快照读都使用的同一个readview，所以每次的查询结果都是一样的。

### SQL：行转列
1. case when方式
```
示例：
insert into student_score values(null,'张三','数学',90);
insert into student_score values(null,'张三','语文',85);
insert into student_score values(null,'张三','英语',92);
insert into student_score values(null,'李四','数学',88);
insert into student_score values(null,'李四','语文',91);
insert into student_score values(null,'李四','英语',99);
insert into student_score values(null,'王五','数学',100);
insert into student_score values(null,'王五','语文',82);
insert into student_score values(null,'王五','英语',88);

实现sql：
select s_name,
sum(case s_sub when '数学' then s_score else 0 end) 数学,
sum(case s_sub when '语文' then s_score else 0 end) 语文,
max(case s_sub when '英语' then s_score else 0 end) 英语
from student_score group by s_name
```

### SQL：列转行
```
SELECT
	s_name,
	'数学' AS s_sub,
	c_math AS s_score
  FROM s_score
UNION
SELECT
	s_name,
	'语文' AS s_sub,
	c_language AS s_score
  FROM s_score
UNION
SELECT
	s_name,
	'英语' AS s_sub,
	c_english AS s_score
  FROM s_score
```

### SQL：多行转一行
```
select s_name,group_concat(s_sub,':',s_score separator '@') all_score 
from  student_score group by s_name
```
原理：group_concat参数可为多个列，separator为列之间的分隔符，默认为','
说明：最后一定要用group by 维度聚合,不写group by 会拼接所有的行

### group by多分组取最大最小值
第一种方式正确写法——使用limit
````
SELECT
    * 
FROM
    ( SELECT * FROM student order by age desc,c_class asc limit 99999999) AS b
GROUP BY
    c_class;
````
分析：
limit 99999999是必须要加的，如果不加的话，数据不会先进行排序，通过 explain 查看执行计划，可以看到没有 limit 的时候，少了一个 DERIVED(得到) 操作。

第二种方式正确写法——使用having
````
SELECT
    * 
FROM
    ( SELECT * FROM student having 1 order by age desc,c_class asc) AS b
GROUP BY
    c_class;
````
分析：
通过 explain 查看执行计划，可以看到 使用having 1，也会使用 DERIVED(得到) 操作。

### mybatis三级缓存
一级缓存：
默认开启
一级缓存是在SqlSession 层面进行缓存的，即同一个SqlSession ，多次调用同一个Mapper和同一个方法的同一个参数，只会进行一次数据库查询，然后把数据缓存到缓冲中，以后直接先从缓存中取出数据，不会直接去查数据库
二级缓存：
默认不开启，需要手动进行配置
SqlSessionFactory层面给各个SqlSession 对象共享。
在mybatis-config.xml文件中添加<setting name="cacheEnabled" value="true"/>
在xxxMapper.xml文件中添加<cache eviction="FIFO" flushInterval="60000" readOnly="false" size="1024" ></cache>
各个属性意义如下：
eviction：缓存的回收策略。
flushInterval：缓存刷新间隔。
readOnly：是否只读。
size：缓存存放多少个元素。
type：指定自定义缓存的全类名(实现Cache接口即可)。ps：要使用二级缓存，对应的POJO必须实现序列化接口 。
useCache="true"是否使用一级缓存，默认false。sqlSession.clearCache();只是清除当前session中的一级缓存。
useCache配置：如果一条句每次都需要最新的数据，就意味着每次都需要从数据库中查询数据，可以把这个属性设置为false
二级缓存默认会在insert、update、delete操作后刷新缓存，可以手动配置不更新缓存
三级缓存：
自定义缓存
自定义缓存对象，该对象必须实现 org.apache.ibatis.cache.Cache 接口。

### ShardingJdbc底层有哪些分表的策略。多少数据才需要进行分表。
分片策略：
1）分片维度：数据源分片策略、表分片策略，即分库跟分表；
2）分片键：用于分片的数据库字段，是将数据库(表)水平拆分的关键字段。SQL中如果无分片字段，将执行全路由(全库、全表逐一执行)，性能较差。 除了对单分片字段的支持，ShardingSphere也支持根据多个字段进行分片；
3）分片算法：PreciseShardingAlgorithm、RangeShardingAlgorithm、HintShardingAlgorithm、

一、行表达式分片策略（InlineShardingStrategy）：
（1）使用场景：只支持单分片健；支持对 SQL语句中的 = 、IN 等的分片操作，不支持between and。
（2）使用方法：适用于做简单的分片算法，在配置中使用 Groovy 表达式，无需自定义分片算法，省去了繁琐的代码开发，是几种分片策略中最为简单的。利用inline.algorithm-expression书写表达式，如ds-$->{order_id % 2} 表示对 order_id 做取模计算，$ 是个通配符用来承接取模结果，最终计算出分库ds-0 ··· ds-n

二、标准分片策略（StandardShardingStrategy）：
（1）使用场景：SQL 语句中有>，>=, <=，<，=，IN和BETWEEN AND操作符，都可以应用此分片策略。它只支持对单个分片健（字段）为依据的分库分表，并提供了两种分片算法 PreciseShardingAlgorithm（精准分片）和 RangeShardingAlgorithm（范围分片）。
（2）使用方法：使用标准分片策略时，精准分片算法是必须实现的算法，用于 SQL 含有 = 和 IN等 的分片处理；范围分片算法是非必选的，用于处理含有 BETWEEN AND 的分片处理。如果没配置范围分片算法，而 SQL 中又用到 BETWEEN AND 或者 like等，那么 SQL 将按全库、表路由的方式逐一执行，查询性能会很差需要特别注意。

三、复合分片策略：
1、使用场景：SQL 语句中有>，>=, <=，<，=，IN 和 BETWEEN AND 等操作符，且复合分片策略支持对多个分片健操作。
2、实现方法：自定义复合分片策略要实现 ComplexKeysShardingAlgorithm 接口，重新 doSharding()方法。

四、Hint分片策略：
（1）使用场景：这种分片策略无需配置分片健，分片健值也不再从 SQL中解析，而是由外部指定分片信息，让 SQL在指定的分库、分表中执行。ShardingSphere 通过 Hint API实现指定操作，实际上就是把分片规则tablerule 、databaserule由集中配置变成了个性化配置。
（2）使用方法：实现 HintShardingAlgorithm 接口并重写 doSharding()方法。

根据阿里开发手册， 单表行数超过500W或者单表数据容量超过2G，需要考虑水平分表；根据mysql官网，一个表中列数超过1017列，开发者记住1000列就好，需要考虑垂直分表。即行数和数据容量不够，水平分表，列数不够，垂直分表。

水平分表涉及的问题有三个：

第一个问题是确定分片策略：水平分表存在一个无法避免的问题是，需要决定根据哪个字段将一个表拆分为多个表，这就是分片字段，决定使用哪个分片字段、决定如何对目标表分片的就是分片策略，常见的分片策略方案包括：哈希取模分片算法、一致性哈希分片算法(哈希环偏斜)、范围分片算法。
第二个问题是确定分布式ID：需要额外自定义一个字段来存储全局唯一的主键，即分布式ID，因为mysql的物理主键只能保证在一个表内唯一，水平分表将一个表变为多个表，而这个数据库的水平分表对前端来说是透明的，前端需要做 select * from tablename where 主键列= ‘xxx’这种类型操作，不能再用id列，常见的方式是水平分表的每个表增加一个额外的列，用uuid或者雪花算法保证唯一。都能保证唯一性，但是用雪花算法比uuid要好，因为uuid没有任何意义，雪花算法可以存储时间戳。
第三个问题是引入依赖：需要让后端Java代码中像操作一个表一样，去操作水平分表之后的多个表，要么在服务端代码中引入Sharding-JDBC依赖，要么是在linux上独立安装ShardingSphere这个中间件，两者的原理是一样的，只是一个在服务端代码层面实现，一个在数据库层面实现。

### mysql窗口函数
mysql5.7是没有这样的开窗函数的，8.0之后的版本已经内置了开窗函数。

**什么是窗口函数？**
窗口函数可以进行排序，生成序列号等一般的聚合函数无法实现的高级操作。窗口函数也称为OLAP函数，意思是对数据库数据进行实时分析处理。
作用类似于在查询中对数据进行分组，不同的是，group分组操作会把分组的结果聚合成一条记录，而窗口函数是结果分组然后分别处理。
窗口函数是对 where 或者 group by 子句处理后的结果进行操作，所以窗口函数原则上只能写在 select 子句中

语法
select 窗口函数 over (partition by 用于分组的列名， order by 用于排序的列名）

常见窗口函数
名称 描述
CUME_DIST()     累积分配值
DENSE_RANK()    当前行在其分区中的排名，稠密排序
FIRST_VALUE()   指定区间范围内的第一行的值
LAG()           取排在当前行之前的值
LAST_VALUE()    指定区间范围内的最后一行的值
LEAD()          取排在当前行之后的值
NTH_VALUE()     指定区间范围内第N行的值
NTILE()         将数据分到N个桶，当前行所在的桶号
PERCENT_RANK()  排名值的百分比
RANK()          当前行在其分区中的排名，稀疏排序
ROW_NUMBER()    分区内当前行的行号

### 数据库常见的设计和优化方案
**分库分表**

**慢sql优化**

**数据库灾备**

## 高并发与多线程

### 线程安全的三大特性。这三个特性分别可以通过什么来保证
1. 原子性:原子性（atomic）:一组操作，不会被分割 或者 即使被分割之后，保证不会受到其他线程的干扰。
2. 可见性:
   -. 1）JMM（Java Memory Model——Java内存模型 ）:工作内存-load和save操作-主内存（要求:每个线程只能操作工作内存，不能直接操作主内存。）
   -. 2）可见性(visible)：某一个线程已经在工作内存中对变量做修改了，但其他线程没有感受到就产生了可见性问题。
3. 有序性:代码重排序？你书写的代码顺序并不一定是最终执行代码的顺序。代码重排序的基本要求： 单线程情况，不能因为重排序，导致结果都不一样了。
   - 编译器（javac） 就会进行重排序——进行一部分的重排序。
   - JVM（JIT Just In Time即时编译器）——运行期间进行重排序.
   - CPU内部对真正要执行的指令，也会进行重排序。

### AQS，底层实现，公平非公平是如何实现的
AQS 的全称为（AbstractQueuedSynchronizer），这个类在 java.util.concurrent.locks 包下面。

AQS 是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出应用广泛的大量的同步器， 比如我们提到的 ReentrantLock，Semaphore，其他的诸如 ReentrantReadWriteLock，SynchronousQueue，FutureTask(jdk1.7) 等等皆是基于 AQS 的。当然，我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。

AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒 时锁分配的机制，这个机制 AQS 是用 CLH 队列锁实现的，即将暂时获取不到锁的线程加入到队列中。

AQS 定义两种资源共享方式
1) Exclusive（独占） ：只有一个线程能执行，如 ReentrantLock。又可分为公平锁和非公平锁,ReentrantLock 同时支持两种锁
2) Share（共享）
多个线程可同时执行，如 Semaphore／CountDownLatch。 CyclicBarrier、ReadWriteLock

ReentrantReadWriteLock 可以看成是组合式，因为 ReentrantReadWriteLock 也就是读写锁允许多个 线程同时对某一资源进行读。

不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源 state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队／唤醒出队等）AQS已经在上层已经帮我们实现好了。

**公平和非公平实现**
1．非公平锁在调用 lock后，首先就会调用CAS 进行一次抢锁，如果这个时候恰巧锁没有被占用，那么直接就获取到锁返回了。
2．非公平锁在CAS失败后，和公平锁一样都会进入到tryAcquire方法，在tryAcquire方法中，如果发现锁这个时候被释放了（state＝＝0），非公平锁会直接CAS抢锁，但是公平锁会判断等待队列是否有线程处于等待状态，如果有则不去抢锁，乖乖排到后面。

ReentrantLock 默认采用非公平锁，因为考虑获得更好的性能，通过 boolean 来决定是否用公平锁（传入 true 用公平锁）。

### CAS，底层原理，CAS可以保证三大特性中哪些
cas是检查并替换

CAS是基于乐观锁的思想。 synchronized是基于悲观锁的思想

cas保证原子性，但是不能解决ABA的问题

Synchronized保证三大特性（原子性，可见性，有序性）

### Semaphore信号量与CountDownLatch的使用场景 
**Semaphore(信号量)**-允许多个线程同时访问

synchronized 和 ReentrantLock 都是一次只允许一个线程访问某个资源，Semaphore(信号量)可以指定多个线程同时访问某个资源。

执行 acquire 方法阻塞，直到有一个许可证可以获得然后拿走一个许可证；每个 rerelease方法增加一个许可证，这可能会释放一个阻塞的 acquire 方法。然而，其实并没有实际的许可证这个对象，Semaphore 只是维持了一个可获得许可证的数量。 Semaphore 经常用于限制获取某种资源的线程数量。
Semaphore 有两种模式，公平模式和非公平模式。
公平模式： 调用 acquire 的顺序就是获取许可证的顺序，遵循 FIFO；
非公平模式： 抢占式的。

**CountDownLatch （倒计时器）**
CountDownLatch允许 count 个线程阻塞在一个地方，直至所有线程的任务都执行完毕。在Java 并发中，countdownlatch 的概念是一个常见的面试题，所以一定要确保你很好的理解了它。

CountDownLatch是共享锁的一种实现，它默认构造AQS的state值为count。当线程使用countDown 方法时，其实使用了tryReleaseShared 方法以CAS的操作来减少state，直至state为0就代表所有的线程都调用了countDown方法。当调用await方法的时候，如果state不为0，就代表仍然有线程没有调用

countDown方法，那么就把已经调用过countDown的线程都放入阻塞队列Park，并自旋CAS判断state ＝0，直至最后一个线程调用了countDown，使得state＝＝0，于是阻塞的线程便判定成功，全部往下执行。

countDown两种典型用法
1，某一线程在开始运行前等待n个线程执行完毕。将CountDownLatch的计数器初始化为n： newCountDownLatch（n） ，每当一个任务线程执行完毕，就将计数器减1
countdownlatch.countDown（） ，当计数器的值变为 0时，在CountDownLatch上 await（） 的线程就会被唤醒。一个典型应用场景就是启动一个服务时，主线程需要等待多个组件加载完毕，之后再继续执行。

2，实现多个线程开始执行任务的最大并行性。注意是并行性，不是并发，强调的是多个线程在某一时刻同时开始执行。类似于赛跑，将多个线程放到起点，等待发令枪响，然后同时开跑。做法是初始化一个共享的CountDownLatch对象，将其计数器初始化为1 ： new CountDownLatch（1） ，多个线程在开始执行任务前首先coundownlatch.await() ,当主线程调用coundown()计数器变为0，多个线程同时被唤醒。

**JUC包是什么**
JUC是java.util.concurrent包的简称，在Java5.0添加，目的就是为了更好的支持高并发任务。让开发者进行多线程编程时减少竞争条件和死锁的问题！

**JUC工具包实际工作中还用了哪些**
CountDownLatch、CyclicBarrier、Semapphore、Exchanger、Phaser

### 并发和并行的区别
- 并发：当有多个线程在操作时,如果系统只有一个CPU,则它根本不可能真正同时进行一个以上的线程，它只能把CPU运行时间划分成若干个时间段,再将时间段分配给各个线程执行，在一个时间段的线程代码运行时，其它线程处于挂起状。这种方式我们称之为并发(Concurrent)。
- 并行：当系统有一个以上CPU时,则线程的操作有可能非并发。当一个CPU执行一个线程时，另一个CPU可以执行另一个线程，两个线程互不抢占CPU资源，可以同时进行，这种方式我们称之为并行(Parallel)。

1. 并发和并行是即相似又有区别的两个概念，并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔内发生。
2. 在多道程序环境下，并发性是指在一段时间内宏观上有多个程序在同时运行，但在单处理机系统中，每一时刻却仅能有一道程序执行，故微观上这些程序只能是分时地交替执行。
3. 若在计算机系统中有多个处理机，则这些可以并发执行的程序便可被分配到多个处理机上，实现并行执行，即利用每个处理机来处理一个可并发执行的程序，这样，多个程序便可以同时执行。

问题发散：
- 进程有了多线程，能实现并行吗？   可以，但需要多核CPU支持

### 产生死锁的条件
1、互斥条件：一个资源每次只能被一个进程使用；
2、请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放；
3、不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺；
4、循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系；

### 线程有哪些状态，各自的转换过程
线程的五大状态：创建状态、就绪状态、运行状态、阻塞状态、死亡状态

1. 创建状态**（新生状态）：new 线程对象Thread t = new Thread();线程对象一旦创建，就进入了新生状态。
2. 就绪状态：new出来以后，当调用start()方法，线程就会立即进入就绪状态，但不意味着立即调度执行。
3. 运行状态：处于就绪状态的线程，经过CPU调度就会进入运行状态，线程才会真正执行线程体的代码块。
4. 阻塞状态**：当调用sleep、wait或同步锁定时，线程进入阻塞状态，就是代码块不往下执行，阻塞事件解除后，重新进入就绪状态，等待CPU调度执行。
5. 死亡状态：线程中断或结束，一旦进入死亡状态，就不能再次启动。

补充：
1. 线程停止 ：不推荐使用JDK提供的stop()、destroy()方法（已废弃），推荐线程自己停下来：建议使用一个标志位进行终止变量，当flag=false时，则终止线程运行。
2. 线程休眠sleep()方法
sleep(时间)指定当前线程阻塞的毫秒数
sleep存在异常InterruptedException，需要抛异常
sleep时间达到后线程进入就绪状态
sleep可以模拟网络延时、倒计时等功能
3. 线程礼让yield()方法 ：线程礼让：让当前正在执行的线程停止，但并不是阻塞，将线程状态从运行状态转为就绪状态，让CPU重新调度，线程重新竞争；礼让不一定成功，看CPU心情
4. 线程强制执行join()：可以想象成插队，待此线程执行完毕后，再执行其他线程，其他线程阻塞

### 线程池
**为什么需要线程池**
我们有两种常见的创建线程的方法，一种是继承Thread类，一种是实现Runnable的接口，Thread类其实也是实现了Runnable接口。但是我们创建这两种线程在运行结束后都会被虚拟机销毁，如果线程数量多的话，频繁的创建和销毁线程会大大浪费时间和效率，更重要的是浪费内存。那么有没有一种方法能让线程运行完后不立即销毁，而是让线程重复使用，继续执行其他的任务哪？ 这就是线程池的由来，很好的解决线程的重复利用，避免重复开销。

线程池的优点：
1. 线程是稀缺资源，使用线程池可以减少创建和销毁线程的次数，每个工作线程都可以重复使用。
2. 可以根据系统的承受能力，调整线程池中工作线程的数量，防止因为消耗过多内存导致服务器崩溃。

线程池的风险：
1. 死锁 :任何多线程应用程序都有死锁风险。当一组进程或线程中的每一个都在等待一个只有该组中另一个进程才能引起的事件时，我们就说这组进程或线程 死锁了。死锁的最简单情形是：线程 A 持有对象 X 的独占锁，并且在等待对象 Y 的锁，而线程 B 持有对象 Y 的独占锁，却在等待对象 X 的锁。除非有某种方法来打破对锁的等待（Java 锁定不支持这种方法），否则死锁的线程将永远等下去。
2. 资源不足 :线程池的一个优点在于：相对于其它替代调度机制（有些我们已经讨论过）而言，它们通常执行得很好。但只有恰当地调整了线程池大小时才是这样的。
3. 并发错误 ：线程池和其它排队机制依靠使用 wait() 和 notify()方法，这两个方法都难于使用。如果编码不正确，那么可能丢失通知，导致线程保持空闲状态，尽管队列中有工作要处理。使用这些方法时，必须格外小心；即便是专家也可能在它们上面出错。而最好使用现有的、已经知道能工作的实现，例如在 util.concurrent 包。
4. 线程泄漏 ：各种类型的线程池中一个严重的风险是线程泄漏，当从池中除去一个线程以执行一项任务，而在任务完成后该线程却没有返回池时，会发生这种情况。发生线程泄漏的一种情形出现在任务抛出一个 RuntimeException 或一个 Error 时。
5. 请求过载 ：仅仅是请求就压垮了服务器，这种情况是可能的。在这种情形下，我们可能不想将每个到来的请求都排队到我们的工作队列，因为排在队列中等待执行的任务可能会消耗太多的系统资源并引起资源缺乏。在这种情形下决定如何做取决于您自己；在某些情况下，您可以简单地抛弃请求，依靠更高级别的协议稍后重试请求，您也可以用一个指出服务器暂时很忙的响应来拒绝请求。

线程池原理：预先启动一些线程，线程无限循环从任务队列中获取一个任务进行执行，直到线程池被关闭。如果某个线程因为执行某个任务发生异常而终止，那么重新创建一个新的线程而已，如此反复。

### 线程池核心参数

    * corePoolSize(int)：核心线程数量。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到任务队列当中。线程池将长期保证这些线程处于存活状态，即使线程已经处于闲置状态。除非配置了allowCoreThreadTimeOut=true，核心线程数的线程也将不再保证长期存活于线程池内，在空闲时间超过keepAliveTime后被销毁。
    * workQueue：阻塞队列，存放等待执行的任务，线程从workQueue中取任务，若无任务将阻塞等待。当线程池中线程数量达到corePoolSize后，就会把新任务放到该队列当中。JDK提供了四个可直接使用的队列实现，
        * 分别是：基于数组的有界队列ArrayBlockingQueue、
        * 基于链表的无界队列LinkedBlockingQueue、
        * 只有一个元素的同步队列SynchronousQueue、
        * 优先级队列PriorityBlockingQueue。在实际使用时一定要设置队列长度。
    * maximumPoolSize(int)：线程池内的最大线程数量，线程池内维护的线程不得超过该数量，大于核心线程数量小于最大线程数量的线程将在空闲时间超过keepAliveTime后被销毁。当阻塞队列存满后，将会创建新线程执行任务，线程的数量不会大于maximumPoolSize。
    * keepAliveTime(long)：线程存活时间，若线程数超过了corePoolSize，线程闲置时间超过了存活时间，该线程将被销毁。除非配置了allowCoreThreadTimeOut=true，核心线程数的线程也将不再保证长期存活于线程池内，在空闲时间超过keepAliveTime后被销毁。
    * TimeUnit unit：线程存活时间的单位，例如TimeUnit.SECONDS表示秒。
    * RejectedExecutionHandler：拒绝策略，当任务队列存满并且线程池个数达到maximunPoolSize后采取的策略。ThreadPoolExecutor中提供了四种拒绝策略，分别是：抛RejectedExecutionException异常的AbortPolicy(如果不指定的默认策略)、使用调用者所在线程来运行任务CallerRunsPolicy、丢弃一个等待执行的任务，然后尝试执行当前任务DiscardOldestPolicy、不动声色的丢弃并且不抛异常DiscardPolicy。项目中如果为了更多的用户体验，可以自定义拒绝策略。
    * threadFactory：创建线程的工厂，虽说JDK提供了线程工厂的默认实现DefaultThreadFactory，但还是建议自定义实现最好，这样可以自定义线程创建的过程，例如线程分组、自定义线程名称等。

**线程池参数能够运行时动态调整吗**可以
线程池的监控分为2种类型，一种是在执行任务前后全量统计任务排队时间和执行时间，另外一种是通过定时任务，定时获取活跃线程数，队列中的任务数，核心线程数，最大线程数等数据。

以下参数支持动态修改
corePoolSize	核心线程数
maximumPoolSize	最大线程数
queueCapacity	等待队列大小
timeout	任务超时时间告警阈值
execTimeout	任务执行超时时间告警阈值
queuedTaskWarningSize	等待队列排队数量告警阈值
checkInterval	线程池定时监控时间间隔
autoExtend	是否自动扩容

其中的corePoolSize、maximumPoolSize都可以使用ThreadPoolExecutor提供的api实现： public void setCorePoolSize(int corePoolSize) public void setMaximumPoolSize(int maximumPoolSize)

设置新的核心线程数时， 如果设置的新值小于当前值，多余的现有线程将在下一次空闲时终止，如果新设置的corePoolSize值更大，将在需要时启动新线程来执行任何排队的任务；

设置新的最大线程数时，如果新值小于当前值，多余的现有线程将在下一次空闲时终止。

**什么时候核心线程会启动，什么时候会进行最大线程数的添加**
核心线程池中的线程默认情况下是当有任务提交的时候才开始创建，而且就算空闲的线程足以处理新任务，它仍然会创建新的线程去处理，直到核心线程数达到最大值

第一种情况，在任务数不超过核心线程数的情况下，不管是否有空闲的线程，都会去创建新的线程去执行任务。
第二种情况，任务数超过了核心线程数，但没有超过核心线程数+任务队列容量的时候，会将处理不过来的任务放在任务队列中，然后复用核心线程去处理任务，不会去创建临时工线程。
第三种情况，当核心线程都被占用并且队列已满的情况下，会创建临时工线程去处理任务，当临时工线程超过指定时间没有处理任务时就会被销毁。
第四种情况，当线程池线程都在处理任务，并且队伍队列也满了的时候，新任务就会交给饱和策略去处理，默认的是AbortPolicy，也就是直接抛异常。


线程池工作原理

    1. 通过execute方法提交任务时，当线程池中的线程数小于corePoolSize时，新提交的任务将通过创建一个新线程来执行，即使此时线程池中存在空闲线程。
    2. 通过execute方法提交任务时，当线程池中线程数量达到corePoolSize时，新提交的任务将被放入workQueue中，等待线程池中线程调度执行。
    3. 通过execute方法提交任务时，当workQueue已存满，且maxmumPoolSize大于corePoolSize时，新提交的任务将通过创建新线程执行。
    4. 当线程池中的线程执行完任务空闲时，会尝试从workQueue中取头结点任务执行。
    5. 通过execute方法提交任务，当线程池中线程数达到maxmumPoolSize，并且workQueue也存满时，新提交的任务由RejectedExecutionHandler执行拒绝操作。
    6. 当线程池中线程数超过corePoolSize，并且未配置allowCoreThreadTimeOut=true，空闲时间超过keepAliveTime的线程会被销毁，保持线程池中线程数为corePoolSize。（注：销毁空闲线程，保持线程数为corePoolSize，不是销毁corePoolSize中的线程。）
    7. 当设置allowCoreThreadTimeOut=true时，任何空闲时间超过keepAliveTime的线程都会被销毁。


Lock锁实现逻辑：
```
在java.util.concurrent.locks包中有很多Lock的实现类，常用的有ReentrantLock、ReadWriteLock（实现类ReentrantReadWriteLock），其实现都依赖java.util.concurrent.AbstractQueuedSynchronizer类（AQS），实现思路都大同小异

ReentrantLock把所有Lock接口的操作都委派到一个Sync类上，该类继承了AbstractQueuedSynchronizer（AQS），ReentrantLock又有两个子类：公平锁和非公平锁，默认情况下为非公平锁。

（AQS）加锁实现：AbstractQueuedSynchronizer会把所有的请求线程构成一个CLH队列，当一个线程执行完毕（lock.unlock()）时会激活自己的后继节点，但正在执行的线程并不在队列中，而那些等待执行的线程全部处于阻塞状态，经过调查线程的显式阻塞是通过调用LockSupport.park()完成，而LockSupport.park()则调用sun.misc.Unsafe.park()本地方法，再进一步，HotSpot在Linux中中通过调用pthread_mutex_lock函数把线程交给系统内核进行阻塞。

AbstractQueuedSynchronizer通过构造一个基于阻塞的CLH队列容纳所有的阻塞线程，而对该队列的操作均通过Lock-Free（CAS）操作，但对已经获得锁的线程而言，ReentrantLock实现了偏向锁的功能。

synchronized的底层也是一个基于CAS操作的等待队列，但JVM实现的更精细，把等待队列分为ContentionList和EntryList，目的是为了降低线程的出列速度；当然也实现了偏向锁，从数据结构来说二者设计没有本质区别。但synchronized还实现了自旋锁，并针对不同的系统和硬件体系进行了优化，而Lock则完全依靠系统阻塞挂起等待线程。

当然Lock比synchronized更适合在应用层扩展，可以继承AbstractQueuedSynchronizer定义各种实现，比如实现读写锁（ReadWriteLock），公平或不公平锁；同时，Lock对应的Condition也比wait/notify要方便的多、灵活的多。 
```
ReentrantReadWriteLock详解：
```
JDK1.5之后，提供了读写锁ReentrantReadWriteLock，读写锁维护了一对锁，一个读锁，一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大提升。ReentrantLock中所有操作都是具有互斥性的，而现实中有一种场景就是读得特别频繁，但是写得特别少，在这样的场景下，如果可以做到读与读之间不互斥的话，那么就可以大大提高并发效率，所以对应的出现了ReentrantReadWriteLock。

读写锁中同样依赖队列同步器Sync(AQS)实现同步功能，而读写状态就是其同步器的同步状态。ReentrantReadWriteLock可以做到：读读共享，读写互斥，写写互斥。

其实现方式就是：在内部封装了两把锁，一把独占锁，一把共享锁，获取独占锁的线程之间要进行同步；获取独占锁与获取共享锁之间的线程也要进行同步；获取共享锁的线程之间不需要进行同步。
```

LOCK和synchronized、volatile区别

    1. lock是一个接口，而synchronized是java的关键字，synchronized是内置的语言实现；
    2. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而lock在发生异常时，如果没有主动通过unlock（）去释放锁，则很可能造成死锁现象，因此使用lock（）时需要在finally块中释放锁；
    3. lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不响应中断
    4. 通过lock可以知道有没有成功获取锁，而synchronized却无法办到
    5. lock可以提高多个线程进行读操作的效率
    * 在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而竞争资源非常激烈是（既有大量线程同时竞争），此时lock的性能要远远优于synchronized。所以说，在具体使用时适当情况选择。
    volatile是一种稍弱的同步机制，在访问volatile变量时不会执行加锁操作，也就不会执行线程阻塞，因此volatilei变量是一种比synchronized关键字更轻量级的同步机制

ReentrantLock：

    * 直接使用lock接口的话，我们需要实现很多方法，不太方便，ReentrantLock是唯一实现了Lock接口的类，并且ReentrantLock提供了更多的方法，ReentrantLock，意思是“可重入锁”。
    * ReentrantReadWriteLock实现了ReadWriteLock接口.ReadWriteLock也是一个接口，在它里面只定义了两个方法：一个用来获取读锁，一个用来获取写锁。也就是说将文件的读写操作分开，分成2个锁来分配给线程，从而使得多个线程可以同时进行读操作。
    * ReentrantReadWriteLock里面提供了很多丰富的方法，不过最主要的两个方法：readlock()和writelock用来获取读锁和写锁

ThreadLocal：
```
每个thread中都存在一个map, map的类型是ThreadLocal.ThreadLocalMap. Map中的key为一个threadlocal实例
使用场景：Threadlocal用来设置线程私有变量，本身不储存值 主要提供自身引用 和 操作ThreadLocalMap 属性，保证线程的独立性，解决多线程使用共享对象的问题 空间换时间

ThreadLocalHashmap与hashmap区别：
和普通Hashmap类似存储在一个数组内，但hashmap使用的拉链法解决散列冲突, ThreadLocalMap使用开放地址法解决散列冲突
//拉链法原理
把具有相同散列地址的关键字(同义词)值放在同一个单链表中，称为同义词链表。
有m个散列地址就有m个链表，同时用指针数组A[0,1,2…m-1]存放各个链表的头指针，凡是散列地址为i的记录都以结点方式插入到以A[i]为指针的单链表中。A中各分量的初值为空指针。
//开放地址法缺点 ： 空间利用率低 开发地址发会在散列冲突时寻找下一个可存入的槽点 为了避免冲突 负载因子会设置的相对较小
ThreadLocalMap的散列值均匀 且经常增删 纯数组较方便 节点数量少时也能节省掉指针域带来的空间开销
数组 初始容量16，负载因子2/3
node节点 的key封装了WeakReference 用于回收

ThreadLocalMap的键为什么使用弱引用：
JVM利用设置ThreadLocalMap的Key为弱引用，来避免内存泄露。
JVM利用调用remove、get、set方法的时候，回收弱引用。
当ThreadLocal存储很多Key为null的Entry的时候，而不再去调用remove、get、set方法，那么将导致内存泄漏。
当使用static ThreadLocal的时候，延长ThreadLocal的生命周期，那也可能导致内存泄漏。因为，static变量在类未加载的时候，它就已经加载，当线程结束的时候，static变量不一定会回收。那么，比起普通成员变量使用的时候才加载，static的生命周期加长将更容易导致内存泄漏危机。

每个thread中都存在一个map, map的类型是ThreadLocal.ThreadLocalMap. Map中的key为一个threadlocal实例. 这个Map的确使用了弱引用,不过弱引用只是针对key. 每个key都弱引用指向threadlocal. 当把threadlocal实例置为null以后,没有任何强引用指向threadlocal实例,所以threadlocal将会被gc回收. 但是,我们的value却不能回收,因为存在一条从current thread连接过来的强引用. 只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收.
所以得出一个结论就是只要这个线程对象被gc回收，就不会出现内存泄露，但在threadLocal设为null和线程结束这段时间不会被回收的，就发生了我们认为的内存泄露。其实这是一个对概念理解的不一致，也没什么好争论的。最要命的是线程对象不被回收的情况，这就发生了真正意义上的内存泄露。比如使用线程池的时候，线程结束是不会销毁的，会再次使用的。就可能出现内存泄露
```

ThreadLocal、InheritableThreadLocals和TransmittableThreadLocal区别

    ThreadLocal：父线程的本地变量是无法传递给子线程的
    InheritableThreadLocals：
        支持父线程的本地变量传递给子线程（线程池中可能失效），因为父线程的TLMap是通过init一个Thread的时候进行赋值给子线程的，而线程池在执行异步任务时可能不再需要创建新的线程了，因此也就不会再传递父线程的TLMap给子线程了
        线程不安全（任意子线程针对本地变量的修改都会影响到主线程的本地变量（本质上是同一个对象））
    TransmittableThreadLocal：支持父线程的本地变量传递给子线程，解决线程池异步值传递问题（使用replay进行设置父线程里的本地变量给当前子线程，任务执行完毕，会调用restore恢复该子线程原生的本地变量）

如果线程数是10000，然后响应时间是100ms，那么QPS怎么计算
```
简单粗暴的计算的话，如果是并发10000的情况下，rt是100ms, 那么qps 就是10万。
深究一下，这100ms都是什么操作？io密集型还是cpu计算密集型？是否可以异步？cpu核心数？线程池大小？网络带宽 内存，都有影响
```

1万并发量和1万qps有什区别
```
举个例子，10000个请求并发抵达，服务端有能力同时处理，那就是10000并发（正在处理）。如果每个请求它都能之花0.5秒处理完，那它1秒内一共处理了20000个请求，叫20000 rps（对应的qps也是类似概念，但是不是request，是query）
- qps：queries per second 并发量：qps * 平均响应时间，并发量是压力，qps是能力
```

## 分布式与高可用

### 分布式协议有哪些

### 分布式id
```
分布式id都有哪些实现方式
比较常见的有三种方式：
- uuid（缺点，会重复，且无法保证多台）
- n 台mysql服务器自增id
- snowflakeid算法

uuid：
用来生成一串无序唯一的32位长度数据。
涉及到以太网卡地址、纳秒级时间、芯片ID等。
底层是一组32位的十六进制数字，理论上总数为1632 = 2128 ≈ 3.4×10123。
就是说每纳秒都能产生上百万个UUID，能用100亿年。
uuid理论上不会重复，random既然是随机当然可能会重复。

优点:
- 本地生成，没有网络消耗，生成简单，没有高可用风险。

缺点:
- 不易于存储：UUID太长，16字节128位，通常以36长度的字符串表示，很多场景不适用。
- 信息不安全：基于MAC地址生成UUID的算法可能会造成MAC地址泄露，这个漏洞曾被用于寻找梅丽莎病毒的制作者位置。
- 无序查询效率低：由于生成的UUID是无序不可读的字符串，所以其查询效率低。
- UUID不适合用来做数据库的唯一ID，如果用UUID做主键，无序的不递增，大家都知道，主键是有 索引的，然后mysql的索引是通过b+树来实现的，每一次新的UUID数据的插入，为了查询的优 化，都会对索引底层的b+树进行修改，因为UUID数据是无序的，所以每一次UUID数据的插入都会对主键的b+树进行很大的修改，严重影响性能。

数据库自增ID：
优点：
- 非常简单，利用现有数据库系统的功能实现，成本小，有DBA专业维护。
- ID号单调自增，可以实现一些对ID有特殊要求的业务。

缺点:
- 强依赖DB，当DB异常时整个系统不可用，属于致命问题。配置主从复制可以尽可能的增加可用性，但是数据一致性在特殊情况下难以保证。主从切换时的不一致可能会导致重复发号。
- ID发号性能瓶颈限制在单台MySQL的读写性能。

snowflakeid算法：
最高位是符号位，始终为0，二进制中最高位为1的都是负数，但是我们生成的id一般都使用整数，所以这个最高位固定是0
41位的时间序列，精确到毫秒级，41位的长度可以使用69年。时间位还有一个很重要的作用是可以根据时间进行排序。
10位的机器标识，10位的长度最多支持部署1024个节点, 一般是5位IDC+5位machine编号，唯一确定一台机器
12位的计数序列号，序列号即一系列的自增id（多线程建议使用atomic），可以支持同一节点同一毫秒生成多个ID序号，若同一毫秒把序列号用完了，则“等待至下一毫秒”, 12位的计数序列号支持每个节点每毫秒产生4096个ID序号。

生成的ID特点:
1、twitter的SnowFlake生成ID能够按照时间有序生成
2、SnowFlake算法生成id的结果是一个64bit大小的整数
3、分布式系统内不会产生重复id（用有datacenterId和workerId来做区分）

雪花算法的优点：
1. 系统环境ID不重复：能满足高并发分布式系统环境ID不重复，比如大家熟知的分布式场景下的数据库表的ID生成。
2. 生成效率极高：在高并发，以及分布式环境下，除了生成不重复 id，每秒可生成百万个不重复 id，生成效率极高。
3. 保证基本有序递增：基于时间戳，可以保证基本有序递增，很多业务场景都有这个需求。
4. 不依赖第三方库：不依赖第三方的库，或者中间件，算法简单，在内存中进行。

缺点： 依赖服务器时间，服务器时钟回拨时可能会生成重复 id。
```

### 分布式锁
基于redis中setNx：拿setnx(如果key存在返回0)争抢锁，抢到后加一个expire过期时间防止忘记释放

#### 原生Redis分布式锁有什么问题，怎么解决
参考：[redis分布式锁](../../middleware/redis分布式锁.md)


### 分布式事务
1. 2PC（ Two-Phase Commit)两阶段提交协议——强一致模型
   - 是将整个事务流程分为两个阶段，准备阶段（Preparephase）、提交阶段（commit phase），2是指两个阶段，P是指准备阶段，C是指提交阶段。
   - 准备阶段（Prepare phase）：事务管理器给每个参与者发送Prepare消息，每个数据库参与者在本地执行事务，并写本地的Undo/Redo日志，此时事务没有提交。 （Undo日志是记录修改前的数据，用于数据库回滚，Redo日志是记录修改后的数据，用于提交事务后写入数 据文件）
   - 提交阶段（commit phase）：如果事务管理器收到了参与者的执行失败或者超时消息时，直接给每个参与者发送回滚(Rollback)消息；否则，发送提交(Commit)消息；参与者根据事务管理器的指令执行提交或者回滚操作，并释放事务处理过程中使用的锁资源。注意:必须在最后阶段释放锁资源。
   - 优缺点：
     - 优点 原理简单，实现方便
     - 缺点：
       - 同步阻塞：二阶段提交协议存在最明显也是最大的一个问题就是同步阻塞，在二阶段提交的执行过程中，所有参与该事务操作的逻辑都处于阻塞状态，也就是说，各个参与者在等待其他参与者响应的过程中，无法进行其他操作。这种同步阻塞极大的限制了分布式系统的性能。
       - 单点问题：协调者在整个二阶段提交过程中很重要，如果协调者在提交阶段出现问题，那么整个流程将无法运转，更重要的是：其他参与者将会处于一直锁定事务资源的状态中，而无法继续完成事务操作。
       - 数据不一致：假设当协调者向所有的参与者发送 commit 请求之后，发生了局部网络异常或者是协调者在尚未发送完所有commit 请求之前自身发生了崩溃，导致最终只有部分参与者收到了 commit 请求。这将导致严重的数据不一致问题。
       - 过于保守：如果在二阶段提交的提交询问阶段中，参与者出现故障而导致协调者始终无法获取到所有参与者的响应信息的话，这时协调者只能依靠其自身的超时机制来判断是否需要中断事务，显然，这种策略过于保守。换句话说，二阶段提交协议没有设计较为完善的容错机制，任意一个节点失败都会导致整个事务的失败。
2. 使用消息队列来避免分布式事务——最终一致性

**分布式理论：一致性协议 3PC**

3PC，全称 “three phase commit”，是 2PC 的改进版，将 2PC 的 “提交事务请求” 过程一分为二，共形成了由CanCommit、PreCommit和doCommit三个阶段组成的事务处理协议。

**2PC对比3PC**
1. 首先对于协调者和参与者都设置了超时机制（在2PC中，只有协调者拥有超时机制，即如果在一定时间内没有收到参与者的消息则默认失败）,主要是避免了参与者在长时间无法与协调者节点通讯（协调者挂掉了）的情况下，无法释放资源的问题，因为参与者自身拥有超时机制会在超时后，自动进行本地commit从而进行释放资源。而这种机制也侧面降低了整个事务的阻塞时间和范围。

2. 通过CanCommit、PreCommit、DoCommit三个阶段的设计，相较于2PC而言，多设置了一个缓冲阶段保证了在最后提交阶段之前各参与节点的状态是一致的 。

3. PreCommit是一个缓冲，保证了在最后提交阶段之前各参与节点的状态是一致的。

问题：3PC协议并没有完全解决数据不一致问题。

### 海量分布式事务问题
场景举例：每秒超过100W以上的并发分布式事务写入操作如何解决？

一般而言，高并发的分布式事务处理，用消息队列做写入缓冲，用二端提交和串联的transaction做事务处理，在出现commit异常的情况下，采用事务补偿，也就是说，先假设事务成功的概率是99%以上，然后检查最终一致性，最后针对出错的不到1%做事务补偿。

但这种也有问题： 消息队列最多承受20W/s的并发，而且消息队列会有很长的延时。

消息队列是可以集群的，最终的瓶颈只是在数据库上面，所以要做多master应该就可以解决了。

如果单库多master还无法解决的话，那就要进行数据库分割。分库分表，多维度分表等。

如果分割了还无法解决的话，那就要采用内存数据库，然后在持久化到磁盘。

补充：其实这类问题基本上没有正确的方案，每一个平台根据业务性质都会不同，唯一能够提供的就是一个大体的思虑，其他的根据自己的业务性质自行提炼和优化。

## 消息系统
**Rabbitmq、rocketMq、kafka区别**

**Kafka的使用场景，为什么使用Kafka，内部原理**

RocketMQ消息事务：
RocketMQ第一阶段发送Prepared消息时，会拿到消息的地址，第二阶段执行本地事物，第三阶段通过第一阶段拿到的地址去访问消息，并修改消息的状态。

**kafka转存到mysql  吞吐量很差 一秒几十条  如何优化**
首先要看慢的原因，然后再谈优化。具体有哪些慢的原因不重要，主要是看你分析的过程。例如可以分解为三个问题，消息消费慢，消息处理慢，和写数据库慢。
1.消费慢可能原因有，生产者本身生产消息就很慢，消费者和Kafka之间网络有延迟，消费者所在节点有问题，例如消费者进程full Gc
2，消息处理慢，比如你的消息是压缩的，解压慢，比如业务逻辑中有没有多次copy消息，有没有锁竞争等待
3，最后是写数据库慢，看看数据库的监控，比如iops 连接池，索引等

还可以再发散一些，比如从日志和可监控手段讲一下，代码中有没有关键监控指标日志，有没有打印写数据库的监控日志 和tps，有没有打印一个消息面在业务测的平均处理时间。
再比如，吞吐低是不是突然发生的，是不是最近变更引起的。

更多问题，参见：[kafka](../../middleware/kafka.md)

## Redis缓存
Redis为什么快：
1. 纯内存操作
2. io多路复用
3. 单线程避免加锁

Redis分布式锁实现：setnx+expire，uuid+lua加过期时间，redlock算法

Redis数据结构有哪几种，为啥不支持int或number
```
String，Hash，List，Set（String无序集合），zset（有序集合，实现方式是跳表+字典）

redis 是使用 C 语言开发，但 C 中并没有 String 类型，只能使用指针或字符数组的形式表示一个字符串，所以 redis 设计了一种简单动态字符串(SDS[Simple Dynamic String])作为底层实现。

在 Redis 中，String 是可以修改的，称为动态字符串(Simple Dynamic String简称SDS)，说是字符串但它的内部结构更像是一个ArrayList，内部维护着一个字节数组，并且在其内部预分配了一定的空间，以减少内存的频繁分配。Redis的内存分配机制是这样：

当字符串的长度小于 1MB时，每次扩容都是加倍现有的空间。
如果字符串长度超过 1MB时，每次扩容时只会扩展 1MB 的空间。

这样既保证了内存空间够用，还不至于造成内存的浪费，字符串最大长度为512MB.。

分析一下字符串的数据结构
struct SDS {
  T capacity;       //数组容量
  T len;            //实际长度
  byte flages;  //标志位,低三位表示类型
  byte[] content;   //数组内容
}

capacity和len两个属性都是泛型，为什么不直接用int类型？因为Redis内部有很多优化方案，为更合理的使用内存，不同长度的字符串采用不同的数据类型表示（int、embstr、raw），且在创建字符串的时候len会和capacity一样大，不产生冗余的空间，所以String值可以是字符串、数字（整数、浮点数) 或者 二进制。

Redis会根据当前值的类型和长度决定使用哪种内部编码来实现。字符串类型的内部编码有3种：

int：8个字节的长整型。
embstr：小于等于39个字节的字符串。
raw：大于39个字节的字符串。
```

Redis持久化：

    * 持久化原理：使用fork子线程，存入buffer，通过策略刷入硬盘
    * 持久化策略：RDB(默认),AOF

redis的全量同步 和 增量同步、异步同步过程？
```
全量复制
master 执行 bgsave ，在本地生成一份 rdb 快照文件。
master node 将 rdb 快照文件发送给 slave node，如果 rdb 复制时间超过 60秒(repl-timeout)，那么 slave node 就会认为复制失败，可以适当调大这个参数(对于千兆网卡的机器，一般每秒传输 100MB，6G 文件，很可能超过 60s)
master node 在生成 rdb 时，会将所有新的写命令缓存在内存中，在 slave node 保存了 rdb 之后，再将新的写命令复制给 slave node。
如果在复制期间，内存缓冲区持续消耗超过 64MB，或者一次性超过 256MB，那么停止复制，复制失败。
client-output-buffer-limitslave256MB64MB60
slave node 接收到 rdb 之后，清空自己的旧数据，然后重新加载 rdb 到自己的内存中，同时基于旧的数据版本对外提供服务。
如果 slave node 开启了 AOF，那么会立即执行 BGREWRITEAOF，重写 AOF。

增量复制
如果全量复制过程中，master-slave 网络连接断掉，那么 slave 重新连接 master 时，会触发增量复制。
master 直接从自己的 backlog 中获取部分丢失的数据，发送给 slave node，默认 backlog 就是 1MB。
master 就是根据 slave 发送的 psync 中的 offset 来从 backlog 中获取数据的。
heartbeat
主从节点互相都会发送 heartbeat 信息。
master 默认每隔 10秒 发送一次 heartbeat，slave node 每隔 1秒 发送一个 heartbeat。

异步复制
master 每次接收到写命令之后，先在内部写入数据，然后异步发送给 slave node。
```

架构模式：

    * 单机：单节点
    * 主从：默认配置下，master节点可以进行读和写，slave节点只能进行读操作，写操作被禁止，
        slave节点挂了不影响其他slave节点的读和master节点的读和写，重新启动后会将数据从master节点同步过来
        master节点挂了以后，redis就不能对外提供写服务了，因为剩下的slave不能成为master
    * 哨兵sentinel：监控检查主和从服务器是否正常，故障提醒，自动故障迁移
        缺点：主从切换需要时间，会丢失数据；还是没有解决主节点写的压力；
    * 集群proxy型：
        缺点：增加了新的 proxy，需要维护其高可用。
    * 集群直连型：Redis-Cluster采用无中心结构，每个节点保存数据和整个集群状态,每个节点都和其他所有节点连接。
        缺点：1、资源隔离性较差，容易出现相互影响的情况。   2、数据通过异步复制,不保证数据的强一致性

redis读写缓慢，如何问题定位与分析
    
    1.使用复杂度高的命令
    2.存储大key
    3.集中过期
    4.内存达到上限
    5.fork耗时严重
    6.绑定CPU
    7.使用Swap
    8.网卡负载过高

Q：redis-zset和java-treeMap区别
```
跳表和红黑树，查询和构建的，读、写、内存、实现成本上略有差异

zset 底层的存储结构有两种：ziplist 和 skiplist。其中，skiplist 就是“跳跃表”结构（简称跳表）。

关于一个 zset 何时使用 ziplist，何时使用 skiplist，有如下的判断条件：zset当数据量大于128个或者字节数大于64转为跳表，否则为压缩列表

zset zrange用法：与单值查找不同，范围查找不能直接按照 score 从高到低排序后，通过比较缩小范围，最终定位到一个节点。我们需要的是类似 sql 中 limit 0,100 这类的效果。
```

**redis删除机制：**
redis是采用定期删除+惰性删除策略，redis数据库键的过期时间都保存在过期字典中，根据系统时间和存活时间判断是否过期。

1. 定时删除：实现方式，创建定时器(对内存友好，但是对cpu很不友好(因为单线程))
   - 定时删除，用一个定时器来负责监视key，当这个key过期就自动删除，虽然内存及时释放，但是十分消耗CPU资源，在大并发请求下CPU要尽可能的把时间都用在处理请求，而不是删除key，因此没有采用这一策略
2. 定期删除：每隔一段时间，对数据库进行一次检查，删除过期键，由算法决定删除多少过期键和检查多少数据库（如果删除太频繁，将退化为定时删除，如果删除次数太少，将退化为惰性删除。）
   - redis默认每100ms检查是否有过期的key，有过期的key则删除。需要说明的是redis不是每个100ms将所有的key检查一次，而是随机抽取进行检查。因此，如果只采用定期策略，会导致很多key到时间没有删除,也就是使用定时删除会导致删除不完全，于是引入了惰性删除
3. 惰性删除：每次获取键时，检查是否过期(如果删除太频繁，将退化为定时删除，如果删除次数太少，将退化为惰性删除。)
   - 某个键值过期后，此键值不会马上被删除，而是等到下次被使用的时候，才会被检查到过期，此时才能得到删除。所以惰性删除的缺点很明显:浪费内存

**redis的内存淘汰机制**
是不是采用定期删除+惰性删除就没问题了呢？
不是的如果定期删除没有删除key，然后你也没有及时去请求这个key，也就是说惰性删除也没有生效。这样redis的内存会越来越高，那么就应该采用内存淘汰机制。
内存淘汰机制就能保证在redis内存占用过高的时候，去进行内存淘汰，也就是删除一部分key，保证redis的内存占用率不会过高

## ElasticSearch搜索引擎
```
Q ) 简单介绍下ES？
A：ES是一种存储和管理基于文档和半结构化数据的数据库（搜索引擎）。它提供实时搜索（ES最近几个版本才提供实时搜索，以前都是准实时）和分析结构化、半结构化文档、数据和地理空间信息数据。

Q ) 请介绍启动ES服务的步骤？
A：启动步骤如下
Windows下进入ES文件夹的bin目录下，点击ElasticSearch.bat开始运行
打开本地9200端口http://localhost:9200, 就可以使用ES了

Q ) 索引是什么?
A: ES集群包含多个索引，每个索引包含一种表，表包含多个文档，并且每个文档包含不同的属性。

Q ) 请解释什么是分片(SHARDs)?
A: 随着索引文件的增加，磁盘容量、处理能力都会变得不够，在这种情况下，将索引数据切分成小段，这就叫分片(SHARDS)。它的出现大大改进了数据查询的效率。

Q ) 什么是副本(REPLICA), 他的作用是什么？
A: 副本是分片的完整拷贝，副本的作用是增加了查询的吞吐率和在极端负载情况下获得高可用的能力。副本有效的帮助处理用户请求。

Q ) 在ES集群中增加和创建索引的步骤是什么？
A: 可以在Kibana中配置新的索引，进行Fields Mapping，设置索引别名。也可以通过HTTP请求来创建索引。

Q ) ES支持哪些类型的查询？
A: 主要分为匹配（文本）查询和基于Term的查询。
文本查询包括基本匹配，match phrase, multi-match, match phrase prefix, common terms, query-string, simple query string.
Term查询，比如term exists, type, term set, range, prefix, ids, wildcard, regexp, and fuzzy.

Q ) 倒排索引
A: 在搜索引擎中，每个文档都有一个对应的文档 ID，文档内容被表示为一系列关键词的集合。例如，文档 1 经过分词，提取了 20 个关键词，每个关键词都会记录它在文档中出现的次数和出现位置。
那么，倒排索引就是关键词到文档 ID 的映射，每个关键词都对应着一系列的文件，这些文件中都出现了关键词。

```

## 系统设计和场景题

### 分布式session
redis集群存储用户信息，WebConfig implements WebMvcConfigurer，重写addInterceptors方法，添加拦截器，拦截器中通过redis获取用户信息，存入ThreadLocal中，然后在controller中获取用户信息

### 秒杀系统
限流，锁ip，锁用户

限流算法（漏桶、令牌桶、滑动窗口）

### 设计rpc
如SpringCloud、Dubbo、ZRPC等

关键指标：
- 初始化时，服务端向注册中心注册
- 客户端从注册中心获取服务端的列表，注册需要的服务
- 当服务端有变化时，注册中心通知客户端
- 客户端通过动态代理的方式反射调用服务端
- 实现了像调用本地服务一样调用远程服务

框架注意点：
- 注册中心：注册中心负责服务的注册和发现，相当于目录服务。服务端启动的时候将服务名称及其对应的地址（IP和端口）注册到注册中心，消费端根据服务名称找到对应的服务地址。有了服务地址，就可以通过网络请求到服务端了。选型Zookeeper or Redis。
- 网络传输：网络通信的话，肯定是采用号称性能之王的Netty
- 序列化：涉及网络通信的话，肯定就涉及到序列化（如dubbo的hession，thrift的thrift，gRPC的protobuf）
- 动态代理：使用动态代理可以屏蔽远程方法调用的细节，比如网络传输
- 负载均衡：随机、轮询、Hash、加权轮询


### 设计服务治理/服务发现系统
那微服务又有哪些问题需要治理？
1. 服务注册与发现。单体服务拆分为微服务后，如果微服务之间存在调用依赖，就需要得到目标服务的服务地址，也就是微服务治理的”服务发现“。要完成服务发现，就需要将服务信息存储到某个载体，载体本身即是微服务治理的”服务注册中心“，而存储到载体的动作即是”服务注册“。
2. 可观测性。微服务由于较单体应用有了更多的部署载体，需要对众多服务间的调用关系、状态有清晰的掌控。可观测性就包括了调用拓扑关系、监控（Metrics）、日志（Logging）、调用追踪（Trace）等。
3. 流量管理。由于微服务本身存在不同版本，在版本更迭过程中，需要对微服务间调用进行控制，以完成微服务版本更迭的平滑。这一过程中需要根据流量的特征（访问参数等）、百分比向不同版本服务分发，这也孵化出灰度发布、蓝绿发布、A/B测试等服务治理的细分主题。
4. 安全。不同微服务承载自身独有的业务职责，对于业务敏感的微服务，需要对其他服务的访问进行认证与鉴权，也就是安全问题。
5. 控制。对服务治理能力充分建设后，就需要有足够的控制能力，能实时进行服务治理策略向微服务分发。


### 短链接生成/分享链接
```
原则就是尽可能短，定长，全局唯一，重定向和一对多。
- 可以先计算url空间大小据此估计设计短链接为多长，使用数字大小写字母来组成链接，一般对于一个app来说，链接长度在5-6位即可，对于整个网络空间7位也足够了。
- 全局唯一其实就是分布式id生成问题，即如何生成尽可能短，按照时间粗略有序且全局唯一的id，再根据id生成链接。
- 重定向一般使用302或307重定向，不使用301主要是因为，301浏览器会对原始url缓存，302和307不会缓存，缓存之后用户访问短链接浏览器直接跳转，服务无法再去收集用户的访问信息。
- 一对多指的是在不同时间生成的同一个视频url短链接应该不一样。

补充一下，一般肯定还会追问分布式id生成怎么搞，参见本项目中其他介绍。
```

### 排行榜
如热搜排行榜，文件下载排行榜等

热搜排行榜实现要点: 性能/简单/时效 .
redis性能高是公认的, redis的数据结构zset有个score,可以实现排序,redis可以给key设定时效
```
    /**
     * 排行榜添加或更新数据score
     * 排行版每天凌晨刷新一遍
     * @param key
     * @param member
     */
    public static void leaderboardAdd(String key, String member){
        // 添加或更新数据
        getRedisTemplate().opsForZSet().incrementScore(key, member, 1);
        // 设置有效期
        LocalDateTime localDateTime = LocalDateTime.of(LocalDate.now(), LocalTime.of(23, 59, 59));
        LocalDateTime now = LocalDateTime.now();
        // 时间差
        Duration between = Duration.between(now, localDateTime);
        getRedisTemplate().expire(key,between);
    }

    /**
     * 通过索引区间返回有序集合成指定区间内的成员 分数从高到低
     *
     * @param key   键
     * @param size  获取个数
     * @return 成员集合
     */
    public static List<LeaderboardDto> leaderboardGet(String key, int size) {
        List<LeaderboardDto> leaderboardDtos = null;
        try {
            Set<ZSetOperations.TypedTuple<String>> typedTuples = getRedisTemplate().opsForZSet().reverseRangeWithScores(key, 0, -1);
            if(CollectionUtils.isNotEmpty(typedTuples)){
                leaderboardDtos = typedTuples.stream().limit(size).map(stringTypedTuple -> {
                    return LeaderboardDto.builder()
                            .value(stringTypedTuple.getValue())
                            .score(stringTypedTuple.getScore())
                            .build();
                }).collect(Collectors.toList());
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return leaderboardDtos;
    }
```

历史搜索的实现也差不多,可以用redis的数据结构list(列表)实现,每次在头部插入数据,然后控制列表的长度就行
```
    /**
     * 添加记录
     *
     * @param key   键
     * @param value 值
     * @return boolean
     */
    public static void lpush(String key, String value) {
        try {
            ListOperations<String, String> redisTemplate = getRedisTemplate();
            redisTemplate.leftPush(key, value);
            // 截取列表长度
            redisTemplate.trim(key,0,max_size-1);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取列表数据
     *
     * @param key   键
     * @return 元素列表
     */
    public static List<String> lrange(String key) {
        try {
            ListOperations<String, String> redisTemplate = getRedisTemplate();
            return redisTemplate.range(key, 0, -1);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
```

文件下载排行榜：也可以使用LRU算法实现，该方案就是在内存中维护一个双向链表。将页面准备展示的排行榜的最小名次作为链表的固定长度，将前台每次下载的文件名称都插入到链表的头部，如果插入的内容超过链表的长度，则把链表的尾节点删除，用以保证链表的长度不变，这样就自然的形成了一个排行榜，最新的下载永远在前边。

### 微信抢红包
类似秒杀活动

秒杀一般流程：
- 锁库存
- 插入“秒杀”记录
- 更新库存

微信红包业务相比普通商品“秒杀”活动，具有海量并发、高安全级别要求的特点。
- 事务级操作量极大。上文介绍微信红包业务特点时提到，普遍情况下同时会有数以万计的微信群在发红包。这个业务特点映射到微信红包系统设计上，就是有数以万计的“并发请求抢锁”同时在进行。这使得 DB 的压力比普通单个商品“库存”被锁要大很多倍。
- 事务性要求严格。微信红包系统本质上是一个资金交易系统，相比普通商品“秒杀”系统有更高的事务级别要求。


**方案一：使用内存操作替代实时的 DB 事务操作**
将“实时扣库存”的行为上移到内存 Cache 中操作，内存 Cache 操作成功直接给 Server 返回成功，然后异步落 DB 持久化。

这个方案的优点是用内存操作替代磁盘操作，提高了并发性能。

但是缺点也很明显，在内存操作成功但 DB 持久化失败，或者内存 Cache 故障的情况下，DB 持久化会丢数据，不适合微信红包这种资金交易系统。

**方案二：悲观锁**
悲观锁的实现方式: SQL + FOR UPDATE。
这样在并发期间一旦有一个事务持有了数据库记录的锁，其他的线程将不能再对数据进行更新了，这就是悲观锁的实现方式。
```
1    <!--悲观锁-->
2    <select id="getRedPacketForUpdate" parameterType="int" resultType="com.demo.entity.RedPacket">
3        select id, user_id as userId, amount, send_date as sendDate, total, unit_amount as unitAmount,
4        stock, version, note
5        from t_red_packet
6        where
7        id = #{id} for update
8    </select>
```
select查询是不加锁的，select…for update是会加锁的，而且是悲观锁，但是在不同查询条件时候加的锁的类型（行锁，表锁）是不同的。
```
select * from t_user where id = 1 for update；
```
在where 后面查询条件是主键索引，唯一索引时候是行锁
查询条件是普通字段时候加的是表锁

问题：
对于悲观锁来说，当一条线程抢占了资源后，其他的线程将得不到资源，那么这个时候， CPU 就会将这些得不到资源的线程挂起，挂起的线程也会消耗CPU 的资源，尤其是在高井发的请求中。

一旦线程提交了事务，那么锁就会被释放，这个时候被挂起的线程就会开始竞争资源，那么竞争到的线程就会被CPU 恢复到运行状态，继续运行。

于是频繁挂起，等待持有锁线程释放资源，一旦释放资源后，就开始抢夺，恢复线程，周而复始直至所有红包资源抢完。试想在高并发的过程中，使用悲观锁就会造成大量的线程被挂起和恢复，这将十分消耗资源，这就是为什么使用悲观锁性能不佳的原因。有些时候，我们也会把悲观锁称为独占锁，毕竟只有一个线程可以独占这个资源，或者称为阻塞锁，因为它会造成其他线程的阻塞。无论如何它都会造成并发能力的下降，从而导致CPU频繁切换线程上下文，造成性能低下。为了克服这个问题，提高并发的能力，避免大量线程因为阻塞导致CPU进行大量的上下文切换，程序设计大师们提出了乐观锁机制，乐观锁已经在企业中被大量应用了。

**方案三：乐观锁**
商品“秒杀”系统中，乐观锁的具体应用方法，是在 DB 的“库存”记录中维护一个版本号。在更新“库存”的操作进行前，先去 DB 获取当前版本号。在更新库存的事务提交时，检查该版本号是否已被其他事务修改。如果版本没被修改，则提交事务，且版本号加 1；如果版本号已经被其他事务修改，则回滚事务，并给上层报错。
```
 1    <!--乐观锁-->
 2    <update id="decreaseRedPacketByVersion">
 3        update t_red_packet
 4        set
 5          stock = stock - 1,
 6          version = version + 1
 7        where
 8          id = #{id}
 9        and version = #{version}
10    </update>
```
存在下面三个问题：
1. 如果拆红包采用乐观锁，那么在并发抢到相同版本号的拆红包请求中，只有一个能拆红包成功，其他的请求将事务回滚并返回失败，给用户报错，用户体验完全不可接受。
2. 如果采用乐观锁，将会导致第一时间同时拆红包的用户有一部分直接返回失败，反而那些“手慢”的用户，有可能因为并发减小后拆红包成功，这会带来用户体验上的负面影响。
3. 如果采用乐观锁的方式，会带来大数量的无效更新请求、事务回滚，给 DB 造成不必要的额外压力。

因此，在高并发的情景下，由于版本不一致的问题，存在大量红包争抢失败的问题。为了提高抢红包的成功率，我们加入重入机制。

按时间戳重入(比如100ms时间内)
```
1        // 记录开始的时间
2        long start = System.currentTimeMillis();        // 无限循环，当抢包时间超过100ms或者成功时退出
3        while(true) {            // 循环当前时间
4            long end = System.currentTimeMillis();            // 如果抢红包的时间已经超过了100ms,就直接返回失败
5            if(end - start > 100) {                return FAILED;
6            }
7            ....
8
9        }
```

按次数重入(比如3次机会之内)
```
 1        // 允许用户重试抢三次红包
 2        for(int i = 0; i < 3; i++) {            
 3            RedPacket redPacket = redPacketDao.getRedPacket(redPacketId);     // 获取红包信息, 注意version信息            
 4            if(redPacket.getStock() > 0) {                   // 如果当前的红包大于0 
 5                int update = redPacketDao.decreaseRedPacketByVersion(redPacketId, redPacket.getVersion());    // 再次传入线程保存的version旧值给SQL判断，是否有其他线程修改过数据                
 6                if(update == 0) {                     // 如果没有数据更新，说明已经有其他线程修改过数据，则继续抢红包
                        continue;
 7                }
 8            ....
 9            }
10            ...
11        }
```
使用乐观锁有助于提高并发性能，但是由于版本号冲突，乐观锁导致多次请求服务失败的概率大大提高，而我们通过重入（按时间戳或者按次数限定）来提高成功的概率，这样对于乐观锁而言实现的方式就相对复杂了，其性能也会随着版本号冲突的概率提升而提升，并不稳定。使用乐观锁的弊端在于， 导致大量的SQL被执行，对于数据库的性能要求较高，容易引起数据库性能的瓶颈，而且对于开发还要考虑重入机制，从而导致开发难度加大。

**方案四：微信红包系统的高并发解决方案**
1. 系统垂直 SET 化，分而治之
微信红包用户发一个红包时，微信红包系统生成一个 ID 作为这个红包的唯一标识。接下来这个红包的所有发红包、抢红包、拆红包、查询红包详情等操作，都根据这个 ID 关联。

红包系统根据这个红包 ID，按一定的规则（如按 ID 尾号取模等），垂直上下切分。切分后，一个垂直链条上的逻辑 Server 服务器、DB 统称为一个 SET。

各个 SET 之间相互独立，互相解耦。并且同一个红包 ID 的所有请求，包括发红包、抢红包、拆红包、查详情详情等，垂直 stick 到同一个 SET 内处理，高度内聚。通过这样的方式，系统将所有红包请求这个巨大的洪流分散为多股小流，互不影响，分而治之。

这个方案解决了同时存在海量事务级操作的问题，将海量化为小量。

2. 逻辑 Server 层将请求排队，解决 DB 并发问题
红包系统是资金交易系统，DB 操作的事务性无法避免，所以会存在“并发抢锁”问题。但是如果到达 DB 的事务操作（也即拆红包行为）不是并发的，而是串行的，就不会存在“并发抢锁”的问题了。

按这个思路，为了使拆红包的事务操作串行地进入 DB，只需要将请求在 Server 层以 FIFO（先进先出）的方式排队，就可以达到这个效果。从而问题就集中到 Server 的 FIFO 队列设计上。

微信红包系统设计了分布式的、轻巧的、灵活的 FIFO 队列方案。其具体实现如下：

**首先，将同一个红包 ID 的所有请求 stick 到同一台 Server。**

上面 SET 化方案已经介绍，同个红包 ID 的所有请求，按红包 ID stick 到同个 SET 中。不过在同个 SET 中，会存在多台 Server 服务器同时连接同一台 DB（基于容灾、性能考虑，需要多台 Server 互备、均衡压力）。

为了使同一个红包 ID 的所有请求，stick 到同一台 Server 服务器上，在 SET 化的设计之外，微信红包系统添加了一层基于红包 ID hash 值的分流。

**其次，设计单机请求排队方案。**

将 stick 到同一台 Server 上的所有请求在被接收进程接收后，按红包 ID 进行排队。然后串行地进入 worker 进程（执行业务逻辑）进行处理，从而达到排队的效果。

**最后，增加 memcached 控制并发。**

为了防止 Server 中的请求队列过载导致队列被降级，从而所有请求拥进 DB，系统增加了与 Server 服务器同机部署的 memcached，用于控制拆同一个红包的请求并发数。

具体来说，利用 memcached 的 CAS 原子累增操作，控制同时进入 DB 执行拆红包事务的请求数，超过预先设定数值则直接拒绝服务。用于 DB 负载升高时的降级体验。

通过以上三个措施，系统有效地控制了 DB 的“并发抢锁”情况。

3. 双维度库表设计，保障系统性能稳定

红包系统的分库表规则，初期是根据红包 ID 的 hash 值分为多库多表。随着红包数据量逐渐增大，单表数据量也逐渐增加。而 DB 的性能与单表数据量有一定相关性。当单表数据量达到一定程度时，DB 性能会有大幅度下降，影响系统性能稳定性。采用冷热分离，将历史冷数据与当前热数据分开存储，可以解决这个问题。

处理微信红包数据的冷热分离时，系统在以红包 ID 维度分库表的基础上，增加了以循环天分表的维度，形成了双维度分库表的特色。

具体来说，就是分库表规则像 db_xx.t_y_dd 设计，其中，xx/y 是红包 ID 的 hash 值后三位，dd 的取值范围在 01~31，代表一个月天数最多 31 天。

通过这种双维度分库表方式，解决了 DB 单表数据量膨胀导致性能下降的问题，保障了系统性能的稳定性。同时，在热冷分离的问题上，又使得数据搬迁变得简单而优雅。

综上所述，微信红包系统在解决高并发问题上的设计，主要采用了 SET 化分治、请求排队、双维度分库表等方案，使得单组 DB 的并发性能提升了 8 倍左右，取得了很好的效果。

### 支付宝五福开奖
- 支付宝五福开奖的时候是怎么抗住接近10亿的并发量的，有朋友知道吗？分享一下嘛
  开奖前算好，开奖时链路优化到只有db操作，依托于机器量做并发，部分数据提前预热到机器上，开奖时个壳子，开奖后再批量发奖。
  限流分批处理，提前把奖开好，这不就是纯查询了，上缓存，加机器，分集群，理论可以无限扩容

开奖并发量很大，是业界极少出现的数字，并发量到好几百万，并且依然会一定程度上触发限流。如：放100w头部用户进来，剩下的全部走降级开得一分钱

提前根据uid分桶，提前降级，甚至可以在深夜把一部分开奖结果提前算好，加密缓存到本地。
钱就几个档还提前算啥啊，把VIP客户的钱单独算好存一下就行了，目测都不超过10万
搞个布隆过滤器，把VIP客户的uid提前存下。没命中的穷逼客户uid直接走随机路由，1.88还是0.88就看命了

### 红包雨
红包雨特点
1、开始时间：设置开始时间后，直播间点击领取红包的图标，会显示距离红包雨开始的时间。
2、持续时间：红包雨下落的场景持续时间
3、每人领取概率：单个人能够领到红包的概率
4、最多领取次数：单个人能够领取红包的最多次数。
5、设置固定红包时：观众最终获得红包的金额是已设置单个红包金额的整数倍。
6、设置随机红包时：观众会获得设置次数且金额不等的红包，展示出的金额是本轮获取的红包总金额。

- 相关的接口
查询红包雨活动：用户进入场景，发现红包发射器，客户端连接netty+ webSocket，马上调用该接口。查询当前场景是否存在活动.
红包雨活动倒计时: 用户查询过红包雨活动后，会登记进入本红包雨场景。
红包雨活动红包信息: 当红包雨活动开始后，服务端会服务端会以1次/秒的频率向端侧推送红包雨信息.
红包雨活动结束: 当红包雨活动结束后，服务端会向端侧推送一条红包雨结束消息.
抢红包： set ex nx 加分布式锁抢红包，锁也是Hash结构缓存在Redis
推送红包被抢消息： 当同场次其他用户抢红包后，服务器向用户推送某些红包已经被抢走的消息。

### 设计点赞功能
功能要求：
- 用户应该可以对上的内容进行评价 喜欢 like 或者 不喜欢 dislike
- 用户应该可以取消之前的操作 取消喜欢，取消不喜欢。
- 我们的服务应能够记录内容的统计信息，例如喜欢/不喜欢的数据。

非功能需求：
- 该系统应高度可靠，上传的统计信息确保真实可靠。
- 该系统应具有很高的可用性。一致性可能会受到影响（为了可用性）；如果用户没有马上看到其评价，也是可以接受的。
- 挑战：最受欢迎的热门内容如何处理性能瓶颈， 恶意的重复操作。

**容量估算和约束**
假设我们有13亿总用户，其中8亿是日常活动用户（DAU）。

如果用户平均每天评价15个内容，则每秒的总数将为： 800M * 15/86400秒 => 138K URL/秒

考虑到观看和评价20：1的比率，每秒的观看内容将为： 20 * 138K URL/秒 => 2760K URL/秒

存储空间估算：不考虑内容大小，只考虑点赞需求。我们需要50个字节来存储带有每条推文的元数据（例如ID，时间戳，用户ID，喜欢的总数，不喜欢的总数等。假设每天每个活动用户用户有10个内容上传。上传的内容所需的总存储空间为：

800M DAU * 10 个内容 * 50 Byte => 400 GB /天

带宽估算值： 对于评价(写入)请求，由于我们预计每秒138K 个评价，因此我们服务的总传入数据将约相当于 7 MB /秒。

138K URL/秒 * 50 字节 = 7 MB /s

对于观看点赞数的读取请求，由于我们预计每秒进行约 2760K 访问, 因此我们服务的总传出数据将约相当于 14 MB /秒。

2760K URL/秒 * 50 字节 = 14 MB /s

**挑战问题：**

Q：如果某个资源变得受欢迎，该怎么办？拥有该资源的服务器上可能有很多查询；这可能会造成性能瓶颈。这也将影响我们服务的整体性能。
A：重新分区/重新分配数据，要么使用一致的哈希来平衡服务器之间的负载。

Q：恶意的重复操作
A：设置风控规则，对用户封禁、限流

**数据的冷热分离**

Hot（热数据）
- 被频繁查询或更新 （假设为 一个月内：喜欢和不喜欢的执行总次数为10 万以上）
- 对访问的响应时间要求很高，通常在10毫秒以内

Cold（冷数据）
- 不允许更新，偶尔被查询
- 对访问的响应时间要求不高，通常在1~10秒内都可以接受


### 微博feed流或微信朋友圈
Feed流实现的三种方式：

- 拉模式：发布者的内容仅仅写入自己的发件箱里，当用户访问自己的收件箱时，首先遍历这个用户的关系列表，然后再从关系列表的发件箱里读取数据，最后按照规则进行过滤和排序，这种方式的优点是写数据快，但读数据就要慢很多，适合于大V模式；
- 推模式：发布者的内容不仅写入自己的发件箱里，通过批处理任务，还需要写到对应关系人的收件箱里，当用户访问自己的收件箱时，直接从自己的收件箱里读取数据，最后按照规则进行过滤和排序，这种方式的优点是写数据很慢，但读数据就要快很多，适合于关系社交；
- 推拉结合模式：大V与普通用户分开维护。

> Feed流的两个核心：即存储系统、推送系统。

存储系统目的有两个，一个是数据不丢失，一个是数据可以永久保存。早期微的系统使用Redis内存数据库来提高产品的响应速度，但由于成本增长非常快，因此总要有一些其他的方案来进行替代。Feed流系统虽然数据量级很大、对数据的可靠性要求很高，但它有一个很显著的特点，就是“关系极其简单”，这对于我们的技术选型提供了实现的思路。针对这种情况，我们可以设计如下的三种解决方案：

- Mysql分库分表方案，非常稳定可靠，如果数据量级不大，采用这种方案能够有效的降低开发时间及运维时间，由于发展历史很长，技术也很成熟；
- NoSQL方案，典型的代表是HBase，key-value型的数据库天然的适合二元关系场景，能够存储海量的数据，由于发展历史也很长了，因此可靠性是有保障的，但HBase的问题在于需要一定的运维成本；
- 商业方案，TableStore(表格存储)是阿里云2013年推出的一款分布式数据库，存储账号关系是一个比较好的选择。

推送系统目的是解决消息的时效性。Feed流系统有一个很显著的特点，即读写是非常不平衡的，绝大多数情况下，用户都在“读”，而内容只有少量的人来“写”。因此，推送系统会面临如下几个方面的压力：

- TPS/QPS是千万级的；
- 读写操作对于延迟非常敏感；
- 消息严格意义上是不能丢失的；
- 对于主键自增有很强烈的诉求。

因此，推送系统最好是一个性能不错，又能主键自增的系统，而内存数据库Redis就能比较好的满足这些要求。但Redis由于属于内存数据库，成本非常好，而且Redis单机的特性，会带来不小的运维复杂性。对于工程师而言，思考的就是如何尽可能的少向Redis中写数据，比如只计算ID的数据，然后去存储系统获取Feed的信息。


### 楼中楼，嵌套评论怎么实现(如贴吧、微博等)
**表设计：**
用户表(略)
评论表：
```
id	int	自增，主键
uid	int	用户 id
replyid	int	该评论回复的评论 id，没有则为 0
pid	int	该评论所在的父级 id，没有则为 0
aid	varchar(100)	文章的标识(文章的 id/或唯一标识)
content	varchar(300)	评论内容
createtime	int	评论时间的时间戳
```
楼中楼评论条数统计信息
楼中楼评论如有需要显示被评论人名称，可随replyid一起加上对应user相关信息。
楼中楼评论删除

评论展示说明：
- 根据 aid 就能拿到当前文章所有的评论
- 根据pid=0 拿到直接对文章进行的评论，pid 为其他数据的，必然是属于某个评论之下，应当作为楼中楼展示。
- 获取楼中楼评论：根据pid=x 获取楼中楼评论，注意分页

发表评论说明：
- 直接对文章发表评论，pid 与 replyid 为空；
- 对一级评论进行回复，pid 与 replyid 均为一级评论的 id;
- 对楼中楼进行回复，pid 为一级评论的 id，replyid 为你回复的评论的 id

**拓展：帖子id、评论id怎么保证唯一**
自增主键

### 活跃用户数的统计
难点：
- BitMap如何进行相关操作
- 用户id非自增该如何生成（理解成怎么获取的把，扯了会话管理那一套）
   --- 为什么需要拦截器去拦截每次请求

**redis 位图相关命令**
位图（bitmap），就是用位(bit)来表示存放的某种状态，如开关，有无。在redis中，字符串是以二进制的形式存储的，因此位图在redis中并不是一种数据类型，而是一种字符串的表现形式。位图中每个元素在内存中占用1位，所以可以节省存储空间。

1. SETBIT key offset value：对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
   - 位的设置或清除取决于 value 参数，可以是 0 也可以是 1 。
   - 当 key 不存在时，自动生成一个新的字符串值。
   - 字符串会进行伸展以确保它可以将 value 保存在指定的偏移量上。当字符串值进行伸展时，空白位置以 0 填充。
   - offset 参数必须大于或等于 0 ，小于 2^32 (bit 映射被限制在 512 MB 之内)。

2. BITOP operation destkey key [key …]：对一个或多个保存二进制位的字符串 key 进行位元操作，并将结果保存到 destkey 上。
   - operation 可以是 AND 、 OR 、 NOT 、 XOR 这四种操作中的任意一种：
   - BITOP AND destkey key [key ...] ，对一个或多个 key 求逻辑并，并将结果保存到 destkey 。 
   - BITOP OR destkey key [key ...] ，对一个或多个 key 求逻辑或，并将结果保存到 destkey 。 
   - BITOP XOR destkey key [key ...] ，对一个或多个 key 求逻辑异或，并将结果保存到 destkey 。 
   - BITOP NOT destkey key ，对给定 key 求逻辑非，并将结果保存到 destkey 。

3. BITCOUNT key [start] [end]：计算给定字符串中，被设置为 1 的比特位的数量。
   - 一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。
   - start 和 end 参数的设置和 GETRANGE key start end 命令类似，都可以使用负数值： 比如 -1 表示最后一个字节， -2 表示倒数第二个字节，以此类推。
   - 不存在的 key 被当成是空字符串来处理，因此对一个不存在的 key 进行 BITCOUNT 操作，结果为 0 。

为了统计每日登录的用户数，建立了一个bitmap, 每一位标识一个用户ID。
示例：
```
1000 1101 0010 0101
最左边1代表user_id=15，最右边1代表user_id=0 
```
当某个用户登录了网站或执行了某个操作，就在bitmap中把标识此用户的位置为1，未登录默认为0，

然后进行and操作，即可统计活跃用户数。

redis key可以设计成：时间周；如mon，tue，wed，thu，fri，sat，sun

offset 用user_id。如：100、101、102、103 代表用户user_id

用户户登录数据如下：
```
周一：{100,101,102,103}
周二：{101,102,103}
周三：{101,102}
周四：{100,101,102}
周五：{100,101,102,103}
周六：{101,102,103}
周日：{100,101,102}
一周内连续登录的用户有2个:{101,102}
```

统计一周内连续登录用户操作：
```
赋值：
SETBIT mon 100 1
SETBIT mon 101 1
SETBIT mon 102 1
SETBIT mon 103 1

SETBIT tue 101 1
...

统计求和：
BITOP AND result mon tue wed thu fri sat sun

计算总数：
BITCOUNT result
```
使用 bitmap 实现用户上线次数统计： 可以使用 SETBIT key offset value 和 BITCOUNT key [start] [end] 来实现。

比如说，每当用户在某一天上线的时候，我们就使用 SETBIT key offset value ，以用户名作为 key ，将那天所代表的网站的上线日作为 offset 参数，并将这个 offset 上的value设置为 1 。

举个例子，如果今天是网站上线的第 100 天，而用户 peter 在今天阅览过网站，那么执行命令 SETBIT peter 100 1 ；如果明天 peter 也继续阅览网站，那么执行命令 SETBIT peter 101 1 ，以此类推。

当要计算 peter 总共以来的上线次数时，就使用 BITCOUNT key [start] [end] 命令：执行 BITCOUNT peter ，得出的结果就是 peter 上线的总天数。


### 设计订单系统
实现目标：日万级~日千万级的订单量如何设计、重构

一般来说，订单系统架构存在着这样几个问题：下单服务处理接单慢；数据库压力大；数据异构延迟高、缓存数据质量差等：
- 关键逻辑不要使用读写分离的查询方式，避免从库同步延迟造成订单查询异常，比如创建订单之后要创建支付单。但是，在反查订单的时候由于主从延迟，没查询到订单信息，这就可能会造成创建支付单失败。
- 关键逻辑也不要使用缓存来进行订单的查询，这样做，同时是为了避免因为缓存延迟造成订单反查的失败。
- 订单补偿不要粗暴地使用消息队列的方式，避免中间件引发的订单丢失。比如在进行订单状态的修改时，如果处理失败，就将这个订单信息插入到消息队列中，重新消费，以此完成订单的补偿，这种方式在发送消息和接收消息时有可能存在丢消息的情况。
- 接收消息处理失败时一定要让消息重试，避免丢失，尤其注意 return、continue 等关键字。比如，一次消费多条订单记录，一条条地进行处理，如果修改订单成功，就继续处理下一条；如果修改失败，可能会因为 retrun 或 cotinue 关键字将其余的消息都丢失掉了。
- 不能过重依赖于数据库，而且这个数据库还是订单库，持续读写请求会给数据库造成很大的压力，比如，修改订单状态时就需要反查数据库，并进行订单状态的更新，这些操作在高并发写请求下，会造成数据库资源的抢占，从而影响系统的稳定性。
- 为了避免数据不一致，请求访问主要集中在主库，这样主库压力就会很大，因此，就需要实现分库分表的部署架构，下单服务为此也必须改造支持分库分表的架构设计，但由于热点数据的存在，可能导致数据库出现数据倾斜问题，引发提早的数据库扩容。
- 由于下单服务耦合业务过重，使得即使是多集群的部署架构，也很难实现快速的处理响应，更何况不同业务的订单处理流程还不一样，使得系统维护性也会越来愈差。比如，创建订单时由于业务不同，数码、3C、图书等订单包含的信息是不一样的，这就需要特殊处理，这种特殊处理逻辑与创建订单耦合在一起，系统自然就会变得越来越慢。
- 由于数据库存储量的增大，还会导致数据异构性能（数据的结构不同）的直线下降，以及缓存存储容量的不断扩大，这都会极大的影响查询的性能，而且，还可能出现业务间的相互影响等问题。

一般设计思路： 
**下单服务进行了服务拆分**
使用单独的接单服务处理接单，使用订单引擎和订单管道处理订单业务逻辑，
- 接单服务：统一入口，参数处理，服务限流，进行不同业务请求筛选分离（当用户在结算页点击提交订单之后，接单服务会在同一个事务里，将订单插入接单库，将首任务插入任务库，再交由订单引擎进行任务调度）
  - 什么是任务？任务就是执行订单操作的步骤，比如写订单缓存、减库存、发送订单通知等，以及前面提到不同的特殊业务流程，这些都是一个个的任务。我们通过将整个订单处理流程分解成一个一个的任务，逐个单独处理，来应对日千万级的订单处理压力。
- 订单引擎服务：进行任务创建、任务调度，并导向订单处理管道。（可以引入任务状态机，对任务调度进行重试、监控、报警等。）
  - 任务的创建方式：任务库由订单引擎驱动执行，任务通过订单引擎的服务编排能力生成任务队列，首任务执行成功之后，会插入第二个任务，或者，会同时插入第二个和第三个任务。如果插入任务失败，订单引擎会重新执行当前任务，执行成功之后，继续执行插入操作，这里就需要每个任务的业务处理都需要保证幂等性。
  - 任务的调度方式：任务使用多线程的异步方式进行调度，并根据配置选择是串行执行还是并发执行。如果任务执行失败，订单引擎是如何重新执行失败任务的呢？这就是通过任务状态机来实现的，任务状态机就如同一个系统的守护线程，任务状态机通过识别任务的状态，来判断每个任务是执行完成，还是执行失败的，并根据状态来进行任务调度，并且，会多次执行失败的任务，重试调度的频次也会逐渐递减，当超过一定的重试次数之后，就会告警通知人工干预。
- 订单管道服务：分离不同业务线进行不同业务处理，每条业务线由多个业务处理模块组成，业务模块可以在不同业务线之间实现复用。
  - 订单引擎调度订单管道，订单管道去调度远程真实的服务来执行的，其原因在于任务引擎本身就是多线程的设计架构，对线程占用就比较高，而远程调度会注册很多的服务，服务调度也会启动多线程去执行，如果共同部署在同一系统，就会出现线程数过多，造成 CPU 飙升的情况
  - 订单管道请求服务之后，将处理结果返回给任务引擎

场景总结：
用户在结算页点击结算，结算页调用后台的接单服务；

接单服务接收到下单请求之后，会负责接单，并将订单插入到接单库，同时在一个事务里将首任务插入任务库， 并通知调度起订单引擎开始执行任务。

订单引擎，根据任务编号依次进行任务调度，更新任务状态，并由任务状态机进行任务校验补偿处理。 订单引擎通过调度订单管道，实现真实服务的远程调度，订单管道请求服务之后，将处理结果返回给任务引擎。最后，订单中心会在接单服务创建订单时，异步写一份订单缓存在订单中心，然后再通过数据异构的方式，再次写一份数据在订单缓存中。

### 设计定时任务系统
设计要点：
- 单机还是集群
- 多实例任务类型：是同一时间只能在一个应用实例上执行的定时任务；还是可以多个实例同时执行，多实例任务重复执行，产生的脏数据如何处理（可以采用任务处理版本号类似乐观锁等）
- 锁争抢机制：例如，通过 for update语句实现数据库行级锁。 也可以通过Redis提供的分布式锁，不过如果本身应用系统比较简单，还需要搭建Redis集群，解决方案较重。
- 外部信号调度规则：通过一个统一的任务调度框架，发送任务处理信号，可以通过F5等负载均衡路由到某个应用实例上。 但是，无法达到所有实例，都运行某个任务的功能。
- 定时任务时间间隔：如果时间过长会导致任务执行无法即时生效，时间过段可能占用过多应用资源。
- 待执行数据优先级：如果待执行任务的数据分为不同类别，可能需要均衡FIFO或者RAMDOM等策略，防止执行任务不均匀。例如一直在执行C类数据，而D类数据一直堆积。
- 定时任务单条效率：如果单条任务执行时间过程，也会一直抢占线程，导致无法释放资源处理其它数据。
- 定时任务动态配置/可弹性伸缩
- 定时任务执行的状态监控、告警，以及后补偿机制


**操作系统实现定时任务**
1. 维护一个带过期时间的任务链表
```
简单且行之有效的方式。在一个全局链表中维护一个定时任务链。每次 tick 中断来临，遍历该链表找到 expire 到期的任务。
如果将任务以 expire 排序，每次只用找到链头的元素即可，时间复杂度为 O(1)。
这种方式对于早期的 Linux 系统来说没有问题，随着现在的系统复杂度渐渐变化，它无法支撑如今的网络流量暴增时代的需求。
```
2. 时间轮（Timing-Wheel）算法
```
时间轮很容易理解，圆环有 n 个 bucket，每一个 bucket 表示一秒，当前 bucket 表示当前这一秒到来之后要触发的事件。
每个 bucket 会对应着一个链表，链表中存储的就是当前时刻到来要处理的事件。
那这里有个问题来了，如果有个定时任务需要在 16 小时后执行，换算成秒就是 57600s，难道我们的时间轮也要这么多个 bucket 吗。几万个对内存也是一种损耗。
为了减少 bucket 的数量，时间轮算法提供了一个扩展算法，即 Hierarchy 时间轮。

Hierarchy 很好理解，层级制度。既然一个时间轮可能会导致 bucket 过多，那么为什么不能多弄几个轮子来代替时分秒呢？
基于时、分、秒各自实现一个 wheel，每个 wheel 维护一个自己的 cursor，在 Hour 数组中，每个 bucket 代表一个小时。
Minute 数组中每一个 bucket 代表 1 分钟，Second 数组中每个 bucket 代表 1 秒。
采用分层时间轮，我们只需要 24+60+60=144 个 bucket 就可以表示所有的时间。
完全模拟到时钟的用法，Second wheel 每转完 60 个 bucket ，要联动 Minute wheel 转动一格，同理 Minite wheel 转动 60 个 bucket 也要联动 Hours wheel 转动一格。
```
3. 维护一个基于小根堆算法的定时任务
```
小根堆的性质是满足除了根节点以外的每个节点都不小于其父节点的堆。基于这种性质从根节点开始遍历每个节点能保证获取到一个最小优先级的队列。

那么应用到定时器中，每次只用获取当前最小堆的 root 节点看是否到期即可。最小堆的插入时间复杂度为 O(lgn)，获取头结点时间复杂度为 O(1)。
```

### 设计调用链监控系统
调⽤链基本元素：基本所有的调用系统都是这些元素，可能名称叫法不同。
1. 事件： 请求处理过程当中的具体动作（吃饭是一个事件，夹筷子是吃饭的子事件，张嘴是吃饭的子事件。嘴动是吃饭的子事件。都是嵌套关系。）
2. 节点：请求所经过的系统节点，即事件的空间属性。
3. 时间：事件的开始和结束时间
4. 关系：事件与上⼀个事件关系。 （0.1和0.1.1，0.1.2是父子关系 ，0.2和0.2.3，0.2.1，0.2.2是是父子关系。）

跟写作文一样，时间，地点，人物，事件。（事件是一个串一个，还要保证他们之间的关系不是错综复杂的，还要并发的情况这些调用事件不是错乱的才算完成，理论很简单，但是真正要搞明白，在生产环境使用还要解决很多很多问题。）
1. 什么时间？
2. 在什么节点上？
3. 发⽣了什么事情？
4. 这个事情由谁触发？

**事件捕捉方式**：（其实就是输出信息）
1. 硬编码埋点捕捉
2. AOP埋点捕捉
3. 公开组件埋点捕捉
4. 字节码插桩捕捉

**事件串联**：（事件串联的⽬的）
1. 所有事件都关联到同⼀个调⽤
2. 各个事件之间是有层级关系的

为了到达这两个⽬的地，⼏乎所有的调⽤链系统都会有以下两个属性：
- trackID:在整个系统中唯⼀，该值相同的事件表示同⼀次调⽤。
- eventID（spanID）:在⼀次调⽤中唯⼀、并展出事件的层级关系

串联要解决的问题：
1. 怎么⽣成TrackID和eventID
2. 怎么传递参数
3. 怎么并发情况下不允响传递的结果

串联的过程：
1. 由跟踪的起点⽣成⼀个TrackId，⼀直传递⾄所有节点，并保存在事件属性值当中。
2. 由跟踪的起点⽣成初始EventId（SpanID），每捕捉⼀个事件ID加1，每传递⼀次，层级加1。
3. trackId与eventId 的传递及多线程问题：解决办法是每个跟踪请求创建⼀个互相独⽴的会话，EventId的⾃增都基于该会话实现。通常会话对象的存储基于ThreadLocal实现。

**事件的开始与结束**

⼀个事件是⼀个时间段内系统执⾏的若⼲动作，所以对于事件捕捉必须包含开启监听和结束监听两个动作？如果⼀个事件在⼀个⽅法内完成的，这个问题是⽐较好解决的，我们只要在⽅法的开始创建⼀个Event对象，在⽅法结束时调⽤该对像的close ⽅法即可。

public void addUser(){ // 方法的开始处，开启一个监听 Event event=new Event(); //业务代码执行 ..... ..... // 方法的结束处，关闭一个监听 event.close(); }

但如果⼀个事件的开始和结束触发分布在多个对象或⽅法当中，情况就会变得异常复杂。⽐如⼀个JDBC执⾏事件，应该是在构建 Statement 时开始，在Statement 关闭时结束。怎样把这两个触发动作对应到同⼀个事件当中去呢（即传递Event对象）？在这⾥的解决办法是对返回结果进⾏动态代理，把Event放置到代理对象的属性当中，以达到付递的⽬标。当这个⽅法只是适应JDBC这⼀个场景，其它场景需要重新设计Event 传递路径，⽬前还没有通⽤的解决办法。



### 设计监控告警系统
监控告警系统是一个软件系统，给用户提供监控、告警、通知的功能。例如普罗米修斯

监控：监控系统采集并存储监控对象的一个或者多个指标。这里提到了几个名词，稍加解释：
- 监控系统。对下采集一个或者多个监控对象的指标数据并存储，对上暴露接口供上层做应用图形化展示、告警评估、报表；
- 监控对象。在互联网和软件行业，可能是服务器、虚拟机等基础设施，也可能是apiserver、消息队列、数据库等软件；
- 监控指标。监控对象的某一特征，例如服务器的CPU利用率、apiserver的RPS等。一般会周期性的采集，采集方式包括但不限于：Agent主动推送到Server、Server从Agent拉取、Agent发布Server订阅等方式，其值跟时间相关，类似下面的数据：

告警：告警系统根据设定的规则，周期性评估所有规则是否满足条件，并输出评估结果。这里解释一下几个概念：
- 告警规则。告警规则是一个或者多个监控指标运算表达式。例如：以一分钟为评估周期，内存使用率峰值大于60%；
- 评估。对所有的告警规则进行计算；
- 评估结果。评估的结果有三种情况：
  - 满足。表达式成立。例如：内存使用率峰值>60%；
  - 不满足。表达式不成立。例如：内存使用率峰值<=60%;
  - 数据不足。采集的数据无法支撑表达式的计算。例如最近一分钟内没有采集到内存使用率的数据

通知：大多数监控告警系统，会把告警评估后的动作并入告警的范畴，动作可以是执行某个操作，但更多的情况是通知某对象，由某对象来执行具体的操作。通知模块负责将告警评估的结果发布出去，涉及到几个关键部分：
- 发布方式。以何种方式发布？例如：电视墙、大屏、短信、企业微信、邮件、电话、报警铃声等等
- 发布范围。发布的范围？例如：手机或者邮件的收件人列表

### 设计日志采集系统

### 各种海量数据处理

#### 怎么快速的往map里put十万元素，并发除外
1. 初始化map的时候，预分配足够的空间（避免resize，map重建会造成性能损失）
2. 对元素类重写hash方法确保唯一避免冲突（要求：耗时短，冲突概率低，区分度高）
3. 多线程并行插入

**难点就在如何保证没有hash冲突，或者如何解决hash冲突**

#### mysql海量数据扩容、分库分表
思路：停机扩容(不推荐），不停机扩容(在线双写)

#### MQ海量消息堆积ya
海量数据的top K问题。海量数据存储于多个文件，任何一条数据都可能存在于任何一个文件当中，现需要筛选出现的次数最多的k条数据。

一般思路：
1）、 依次遍历这些文件，通过hash映射，将每个文件的每条数据映射到新构造的多个小文件中（设生成了n个小文件）；
2）、依次统计每个小文件中出现次数最多的k条数据，构成hash表，hash表中每个键值对的形式为 dataItem: count；
3）、利用堆排序，依次遍历这些hash表，在n∗k条数据中，找出count值最大的k个；

#### 海量日志分析，提取出某日访问某网站次数最多的那个IP
思路：分而治之/hash映射 + hash统计 + 堆/快速/归并排序，就是先映射，后统计，最后排序。

#### 多个海量文件之间对比查重：A和B两个大文件，每个文件都存储着海量数据，要求给出A，B中重复的数据 
一般思路：

1. 遍历A中的所有数据，通过hash映射将他们分布存储在n个小文件中，记为{a1,a2,…,an}；
2. 遍历B中的所有数据，通过hash映射将他们分布存储在n个小文件中，记为{b1,b2,…,bn}；
3. 根据hash函数性质可知，A和B中的相同数据一定被映射到序号相同的小文件，所以我们依次比较{ai,bi}即可；
4. 如果问题更进一步，要求返回重复次数最多的k条数据，则可以将对比小文件找到的数据存入hash表，键为数据，值为该数据出现的次数。再用大小为k的堆，排序找出即可。


#### 如何在1亿个数中找出最大的100个数
a. 如果这1亿个书里面有很多重复的数，先通过Hash法，把这1亿个数字去重复，这样如果重复率很高的话，会减少很大的内存用量，从而缩小运算空间。
b. 将1亿个数据分成100份，每份100万个数据，
c. 统计每份文件中数据出现的kv值，找到每份数据中最大的100个。
d. 堆/快速排序归并获取最大的100个数



#### 1兆能存下100万个值在1千万～9千万的数吗
算下简单存储：10000000~90000000，最近的 2^27=134217728，假如用Int 4字节32位存储，结果是100w*4=400w字节，400w/1024/1024=3.8147兆

很明显存不下。

那有没有别的方式能够存储呢，可以参考es倒排索引，倒排链：保存了每个term对应的docId的列表，采用skipList的结构保存，用于快速跳跃。

压缩算法bitmap可以存下1兆内，比如0～8区间的数，1个字节8位就足够了，100w/1024/1024=0.9537兆

bitmap是使用bit位来存储数据的一种结构,当数据有明确的上下界时,我们可以转换到bitmap去存储,比如0～8区间的数,如果使用int来存,则需要耗费4字节32位大小,如果使用位来存,只需要花费1个字节大小,相差32倍,在大数据量的情况下,比较节约空间,而且索引效率高
bitmap的缺点也很明显，首先，当数据比较稀疏时，bitmap显然比较浪费空间，如果要存储整个int32的数据，则需要512MB的空间大小，其次，无法对重复数据进行排序和查找。

为了解决bitmap在稀疏数据集下浪费空间的问题，出现了几种改进算法：
- RLE bitmap：
  run-length encoding（RLE）编码，其大致思想是对于bitmap中重复的值（很多个0或者很多个1），采用值加上重复出现的次数表示，从而起到压缩的目的。这种算法包括Oracle’s BBC、WAH、EWAH、 Concise等。我们从WAH开始，介绍一下压缩思路的改进
  WAH（Word Aligned Hybrid）是在Fastbit项目中提出的，这个算法的核心在于两点：Word Aligned和Hybrid，其中，Word Aligned利用现代CPU特性：操作Word比Byte效率高，所以首先会以Word为单元对bitmap数组进行分割，而Hybrid思想是对一个Word的数据格式进行分类处理。
- Roaring_bitmap
  当插入或者查询时，必须decompress之后才能check
  某些情况下，业务需要对两个bimap求位运算，这时不能跳过大部分全0或者全1的区间。
  而Roaring算法提供了另外的思路来处理压缩的问题，其思路为：

  对于32位的整数，每2^16个数分成一组，称为一个容器
  一个容器如果包含的数小于4096个，认为其比较稀疏，用有序数组容器(ArrayContainer)进行保存。
  对于元素个数大于4096的容器，认为是一个密集数据块，使用2^16bit的bitmap进行保存。
  每个 Roaring 容器都会维护一个计数器用以存储该容器的基数，从而能够通过计数器求和快速获得一个Roaring Bitmap的基数大小。容器计数器的存在，还能够快速确定一个整型所属的容器，以及支持 bitmap
  某个位置值的高效查询。


#### excel超千万条数据，如何快速写入mysql
关键点：多线程读写，如何确定多线程边界

1. 首先，将Excel文件转换为CSV文件。这可以使用Excel或脚本完成。
   Excel转换为csv，文件大小可以压缩30%
   什么是CSV
   逗号分隔值（Comma-Separated Values，CSV），其文件以纯文本形式存储表格数据（数字和文本），文件的每一行都是一个数据记录。每个记录由一个或多个字段组成，用逗号分隔。使用逗号作为字段分隔符是此文件格式的名称的来源，因为分隔字符也可以不是逗号，有时也称为字符分隔值。

2. **使用没 MySQL导入命令 `LOAD DATA INFILE`**: 此命令对于将CSV文件中的数据批量加载到MySQL表中非常有效。

Step 1: Convert Excel to CSV 您可以手动将Excel文件另存为CSV文件，或使用脚本自动执行此过程。
Step 2: Use `LOAD DATA INFILE` in MySQL
    1. **Prepare the MySQL Table**:确保MySQL表已准备好接收数据。
    2. **Load Data Using `LOAD DATA INFILE`**:使用“LOAD DATA INFILE”命令将CSV数据加载到MySQL表中。

以下是一个SQL导入示例：
   ```sql
   LOAD DATA INFILE '/path/to/your/file.csv'
   INTO TABLE my_table
   FIELDS TERMINATED BY ','
   ENCLOSED BY '"'
   LINES TERMINATED BY '\n'
   IGNORE 1 ROWS;
   ```
参数：
    - `/path/to/your/file.csv`: csv文件的路径。
    - `FIELDS TERMINATED BY ','`: 指定CSV中的字段用逗号分隔。
    - `ENCLOSED BY '"'`: 指定字段用双引号括起来。
    - `LINES TERMINATED BY '\n'`: 指定行以换行符结尾。
    - `IGNORE 1 ROWS`: 忽略CSV文件中的第一行（标题）。

**自动化流程的示例脚本**
这是一个Python脚本，使用`pandas`将Excel转换为CSV，然后使用`mysql connector Python`执行`LOAD DATA INFILE`命令：
```python
import pandas as pd
import mysql.connector

# Convert Excel to CSV
excel_file = 'path/to/your/file.xlsx'
csv_file = 'path/to/your/file.csv'
df = pd.read_excel(excel_file)
df.to_csv(csv_file, index=False)

# Connect to MySQL
conn = mysql.connector.connect(
    host='your_host',
    user='your_username',
    password='your_password',
    database='your_database'
)
cursor = conn.cursor()

# Load data into MySQL
load_data_query = f"""
LOAD DATA INFILE '{csv_file}'
INTO TABLE my_table
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\\n'
IGNORE 1 ROWS;
"""
cursor.execute(load_data_query)
conn.commit()

# Close the connection
cursor.close()
conn.close()
```
此脚本将：
1.将Excel文件转换为CSV文件。
2.连接MySQL数据库。
3.执行“LOAD DATA INFILE”命令，将CSV数据加载到MySQL表中。
确保根据需要调整文件路径和数据库连接详细信息。


## 算法

### 二叉树的最大直径

### 递归和非递归前序遍历二叉树

### 用1小时的绳子烧出来1小时15分钟

### 统计小岛个数 DFS

### 能看见楼的数量，高的楼会挡住矮的
比如[5,3,8,3,2,5]就是[3,3,5,4,4,4].8可以看到5,3,8,3,5, 2被挡住了







