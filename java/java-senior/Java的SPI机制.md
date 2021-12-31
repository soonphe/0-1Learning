# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java的SPI SPI-ServiceLoader详解

### SPI是什么
SPI的全名为Service Provider Interface.
我们系统里抽象的各个模块，往往有很多不同的实现方案，比如日志模块的方案，xml解析模块、jdbc模块的方案等。
面向的对象的设计里，我们一般推荐模块之间基于接口编程，模块之间不对实现类进行硬编码。一旦代码里涉及具体的实现类，就违反了可拔插的原则，
如果需要替换一种实现，就需要修改代码。为了实现在模块装配的时候能不在程序里动态指明，这就需要一种服务发现机制。

Java SPI就是提供这样的一个机制：为某个接口寻×××实现的机制。


### Java SPI的约定

当服务的提供者，提供了服务接口的一种实现之后，在jar包的META-INF/services/目录里同时创建一个以服务接口命名的文件。
该文件里就是实现该服务接口的具体实现类。而当外部程序装配这个模块的时候，就能通过该jar包META-INF/services/里的配置文件找到具体的实现类名，并装载实例化，完成模块的注入。

基于这样一个约定就能很好的找到服务接口的实现类，而不需要再代码里制定。
jdk提供服务实现查找的一个工具类：java.util.ServiceLoader

前面说过SPI是针对厂商或者第三方插件的，但是那样不便于举例，不能很好的引导读者，我们先来个单机的。
```
服务(接口)：一个IProgrammer接口
服务提供者（接口实现）：两个实现类 GJProgrammer和JGSProgrammer
资源目录：META-INF/services
```

### 示例
接口与实现类
```
public interface IProgrammer {
    String title();
}
public class GJProgrammer implements IProgrammer {
    @Override
    public String title() {
        return "高级程序员";
    }
}
public class JGSProgrammer implements IProgrammer {
    static {
        System.out.println("加载了");
    }
    @Override
    public String title() {
        return "架构师";
    }
}
```

就是META-INF/services里边有一个名为IProgrammer限定名的文件，内容就是两个实现了类的限定名。
```
com.alipay.campus.demos.spi.GJProgrammer
com.alipay.campus.demos.spi.JGSProgrammer
```

测试类：
```
public static void main(String[] args) {
        ServiceLoader<IProgrammer> serviceLoader = ServiceLoader.load(IProgrammer.class);
        Iterator<IProgrammer> it = serviceLoader.iterator();
        while(it.hasNext()){
            System.out.println("Iterator<IProgrammer> next()方法调用..");
            IProgrammer iProgrammer = it.next();
            String title = iProgrammer.title();
            System.out.println("programmer title="+title);
        }
    }
```

输出结果：
Iterator< IProgrammer> next()方法调用..
programmer title=高级程序员
Iterator< IProgrammer> next()方法调用..
加载了
programmer title=架构师

**分析**
我们只是将实现类放到了META-INF/services下，文件名命名为IProgrammer接口限定名，内容放上两个实现类的限定名，然后使用`ServiceLoader.loader` 就能获取到所有实现类的实例，并能调用相应的接口方法。

此时我们自己是开发者也是使用者，我们完全可以自己new出这两个实现类，然后调用相应的方法。


### JDK和Mysql使用SPI原理

我们先来看下jdk是如何使用
DriverManager.getConnection(url,username,password)
DriverManager有一个静态代码块：
```
static {
    loadInitialDrivers();
    println("JDBC DriverManager initialized");
}

private static void loadInitialDrivers() {
        String drivers;
        try {
            drivers = AccessController.doPrivileged(new PrivilegedAction<String>() {
                public String run() {
                    return System.getProperty("jdbc.drivers");
                }
            });
        } catch (Exception ex) {
            drivers = null;
        }
        AccessController.doPrivileged(new PrivilegedAction<Void>() {
            public Void run() {
                //ServiceLoader的使用
                ServiceLoader<Driver> loadedDrivers = ServiceLoader.load(Driver.class);
                Iterator<Driver> driversIterator = loadedDrivers.iterator();
                try{
                    while(driversIterator.hasNext()) {
                        driversIterator.next();
                    }
                } catch(Throwable t) {
                // Do nothing
                }
                return null;
            }
        });

        println("DriverManager.initialize: jdbc.drivers = " + drivers);

        if (drivers == null || drivers.equals("")) {
            return;
        }
        String[] driversList = drivers.split(":");
        println("number of Drivers:" + driversList.length);
        for (String aDriver : driversList) {
            try {
                println("DriverManager.Initialize: loading " + aDriver);
                Class.forName(aDriver, true,
                        ClassLoader.getSystemClassLoader());
            } catch (Exception ex) {
                println("DriverManager.Initialize: load failed: " + ex);
            }
        }
    }
```

mysql驱动放在META-INF/services下
```
com.mysql.jdbc.Driver
com.mysql.fabric.jdbc.FabricMySQLDriver
```




