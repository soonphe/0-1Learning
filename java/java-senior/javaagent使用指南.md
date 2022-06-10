# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## javaagent使用指南


### Javaagent 是什么？

Javaagent是java命令的一个参数。参数 javaagent 可以用于指定一个 jar 包，并且对该 java 包有2个要求：

1. 这个 jar 包的 MANIFEST.MF 文件必须指定 Premain-Class 项。
2. Premain-Class 指定的那个类必须实现 premain() 方法。

premain 方法，从字面上理解，就是运行在 main 函数之前的的类。

当Java 虚拟机启动时，在执行 main 函数之前，JVM 会先运行-javaagent所指定 jar 包内 Premain-Class 这个类的 premain 方法 。

### 为什么选择javaagent，而不是Aop
Spring AOP 是基于反射实现的，植入点必须是Bean，且仅支持方法拦截，无论是性能还是功能明显不如 Agent 字节码植入。

AOP局限太多，MQ、ES、Redis、Mongo等怎么做？本地缓存、跨线程？并且推进难度也很大，如果是全链路压测，涉及到的应用起码有大几十个，还有各种历史包袱，各种版本的jar包，业务系统本身有开发任务，升级jar包、配合调试的意愿非常低。


### Javaagent介绍
在命令行输入 java可以看到相应的参数，其中有 和 java agent相关的：
```
-agentlib:<libname>[=<选项>] 加载本机代理库 <libname>, 例如 -agentlib:hprof
另请参阅 -agentlib:jdwp=help 和 -agentlib:hprof=help
-agentpath:<pathname>[=<选项>]
按完整路径名加载本机代理库
-javaagent:<jarpath>[=<选项>]
加载 Java 编程语言代理, 请参阅 java.lang.instrument
```
在上面-javaagent参数中提到了参阅java.lang.instrument，这是在rt.jar 中定义的一个包，该路径下有两个重要的类：
- ClassFileTransformer
- Instrumentation

该包提供了一些工具帮助开发人员在 Java 程序运行时，动态修改系统中的 Class 类型。其中，使用该软件包的一个关键组件就是 Javaagent。从名字上看，似乎是个 Java 代理之类的，而实际上，他的功能更像是一个Class 类型的转换器，他可以在运行时接受重新外部请求，对Class类型进行修改。

从本质上讲，Java Agent 是一个遵循一组严格约定的常规 Java 类。 上面说到 javaagent命令要求指定的类中必须要有premain()方法，并且对premain方法的签名也有要求，签名必须满足以下两种格式：
```
public static void premain(String agentArgs, Instrumentation inst)
    
public static void premain(String agentArgs)
```
JVM 会优先加载 带 Instrumentation 签名的方法，加载成功忽略第二种，如果第一种没有，则加载第二种方法。这个逻辑在sun.instrument.InstrumentationImpl 类中.

Instrumentation 类 定义如下：
```
public interface Instrumentation {
    
    //增加一个Class 文件的转换器，转换器用于改变 Class 二进制流的数据，参数 canRetransform 设置是否允许重新转换。
    void addTransformer(ClassFileTransformer transformer, boolean canRetransform);

    //在类加载之前，重新定义 Class 文件，ClassDefinition 表示对一个类新的定义，如果在类加载之后，需要使用 retransformClasses 方法重新定义。addTransformer方法配置之后，后续的类加载都会被Transformer拦截。对于已经加载过的类，可以执行retransformClasses来重新触发这个Transformer的拦截。类加载的字节码被修改后，除非再次被retransform，否则不会恢复。
    void addTransformer(ClassFileTransformer transformer);

    //删除一个类转换器
    boolean removeTransformer(ClassFileTransformer transformer);

    boolean isRetransformClassesSupported();

    //在类加载之后，重新定义 Class。这个很重要，该方法是1.6 之后加入的，事实上，该方法是 update 了一个类。
    void retransformClasses(Class<?>... classes) throws UnmodifiableClassException;

    boolean isRedefineClassesSupported();

    
    void redefineClasses(ClassDefinition... definitions)
        throws  ClassNotFoundException, UnmodifiableClassException;

    boolean isModifiableClass(Class<?> theClass);

    @SuppressWarnings("rawtypes")
    Class[] getAllLoadedClasses();

  
    @SuppressWarnings("rawtypes")
    Class[] getInitiatedClasses(ClassLoader loader);

    //获取一个对象的大小
    long getObjectSize(Object objectToSize);


   
    void appendToBootstrapClassLoaderSearch(JarFile jarfile);

    
    void appendToSystemClassLoaderSearch(JarFile jarfile);

    
    boolean isNativeMethodPrefixSupported();

    
    void setNativeMethodPrefix(ClassFileTransformer transformer, String prefix);
}
```

### 如何使用javaagent？

使用 javaagent 需要几个步骤：
1. 定义一个 MANIFEST.MF 文件，必须包含 Premain-Class 选项，通常也会加入Can-Redefine-Classes 和 Can-Retransform-Classes 选项。
2. 创建一个Premain-Class 指定的类，类中包含 premain 方法，方法逻辑由用户自己确定。
3. 将 premain 的类和 MANIFEST.MF 文件打成 jar 包。
4. 使用参数 -javaagent: jar包路径 启动要代理的方法。

在执行以上步骤后，JVM 会先执行 premain 方法，大部分类加载都会通过该方法，注意：是大部分，不是所有。
当然，遗漏的主要是系统类，因为很多系统类先于 agent 执行。
这个方法是在 main 方法启动前拦截大部分类的加载活动，既然可以拦截类的加载，那么就可以去做重写类这样的操作，结合第三方的字节码编译工具，比如ASM，javassist，cglib等等来改写实现类。

### 代码实现
实现 javaagent 你需要搭建两个工程，一个工程是用来承载 javaagent类，单独的打成jar包；一个工程是javaagent需要去代理的类。即javaagent会在这个工程中的main方法启动之前去做一些事情。

1.首先来实现javaagent工程。

工程目录结构如下：
```
-java-agent
----src
--------main
--------|------java
--------|----------com.rickiyang.learn
--------|------------PreMainTraceAgent
--------|resources
-----------META-INF
--------------MANIFEST.MF
```
第一步是需要创建一个类，包含premain 方法：
```
import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.lang.instrument.Instrumentation;
import java.security.ProtectionDomain;

public class PreMainTraceAgent {

    public static void premain(String agentArgs, Instrumentation inst) {
        System.out.println("agentArgs : " + agentArgs);
        inst.addTransformer(new DefineTransformer(), true);
    }

    static class DefineTransformer implements ClassFileTransformer{

        @Override
        public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException {
            System.out.println("premain load Class:" + className);
            return classfileBuffer;
        }
    }
}
```
实现了带Instrumentation参数的premain()方法。调用addTransformer()方法对启动时所有的类进行拦截。

然后在 resources 目录下新建目录：META-INF，在该目录下新建文件：MANIFEST.MF：
```
Manifest-Version: 1.0
Can-Redefine-Classes: true
Can-Retransform-Classes: true
Premain-Class: PreMainTraceAgent

```
注意到第5行有空行。

说一下MANIFEST.MF文件的作用，这里如果你不去手动指定的话，直接 打包，默认会在打包的文件中生成一个MANIFEST.MF文件：
```
Manifest-Version: 1.0
Implementation-Title: test-agent
Implementation-Version: 0.0.1-SNAPSHOT
Built-By: yangyue
Implementation-Vendor-Id: com.rickiyang.learn
Spring-Boot-Version: 2.0.9.RELEASE
Main-Class: org.springframework.boot.loader.JarLauncher
Start-Class: com.rickiyang.learn.LearnApplication
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Created-By: Apache Maven 3.5.2
Build-Jdk: 1.8.0_151
Implementation-URL: https://projects.spring.io/spring-boot/#/spring-bo
 ot-starter-parent/test-agent
```
这是默认的文件，包含当前的一些版本信息，当前工程的启动类，它还有别的参数允许你做更多的事情，可以用上的有：

> Premain-Class ：包含 premain 方法的类（类的全路径名）
> Agent-Class ：包含 agentmain 方法的类（类的全路径名）
> Boot-Class-Path ：设置引导类加载器搜索的路径列表。查找类的特定于平台的机制失败后，引导类加载器会搜索这些路径。按列出的顺序搜索路径。列表中的路径由一个或多个空格分开。路径使用分层 URI 的路径组件语法。如果该路径以斜杠字符（“/”）开头，则为绝对路径，否则为相对路径。相对路径根据代理 JAR 文件的绝对路径解析。忽略格式不正确的路径和不存在的路径。如果代理是在 VM 启动之后某一时刻启动的，则忽略不表示 JAR 文件的路径。（可选）
> Can-Redefine-Classes ：true表示能重定义此代理所需的类，默认值为 false（可选）
> Can-Retransform-Classes ：true 表示能重转换此代理所需的类，默认值为 false （可选）
> Can-Set-Native-Method-Prefix： true表示能设置此代理所需的本机方法前缀，默认值为 false（可选）

即在该文件中主要定义了程序运行相关的配置信息，程序运行前会先检测该文件中的配置项。

jar编译、打包命令：
```
    javac PreMainTraceAgent.java
    jar cf MyProgram.jar PreMainTraceAgent
```

一个java程序中-javaagent参数的个数是没有限制的，所以可以添加任意多个javaagent。所有的java agent会按照你定义的顺序执行，例如：
```
java -javaagent:agent1.jar -javaagent:agent2.jar -jar MyProgram.jar

```

程序执行的顺序将会是：
MyAgent1.premain -> MyAgent2.premain -> MyProgram.main

另外的再说一种不去手动写MANIFEST.MF文件的方式，使用maven插件：
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
        <archive>
            <!--自动添加META-INF/MANIFEST.MF -->
            <manifest>
                <addClasspath>true</addClasspath>
            </manifest>
            <manifestEntries>
                <Premain-Class>com.rickiyang.learn.PreMainTraceAgent</Premain-Class>
                <Agent-Class>com.rickiyang.learn.PreMainTraceAgent</Agent-Class>
                <Can-Redefine-Classes>true</Can-Redefine-Classes>
                <Can-Retransform-Classes>true</Can-Retransform-Classes>
            </manifestEntries>
        </archive>
    </configuration>
</plugin>
```

用这种插件的方式也可以自动生成该文件。

agent代码就写完了，下面再重新开一个工程，你只需要写一个带 main 方法的类即可：
```
public class TestMain {

    public static void main(String[] args) {
        System.out.println("main start");
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("main end");
    }
}
```

很简单，然后需要做的就是将上面的 代理类 和 这个测试类关联起来。有两种方式：

- 如果你用的是idea，那么你可以点击菜单： run-debug configuration，然后将你的代理类包 指定在 启动参数VM options:中即可：

- 另一种方式是不用 编译器，采用命令行的方法。与上面大致相同，将 上面的测试类编译成 class文件，然后 运行该类即可：
```
#将该类编译成class文件
 > javac TestMain.java
 
 #指定agent程序并运行该类
 > java -javaagent:c:/alg.jar TestMain
```
使用上面两种方式都可以运行,输出结果如下：


```
D:\soft\jdk1.8\bin\java.exe -javaagent:c:/alg.jar "-javaagent:D:\soft\IntelliJ IDEA 2019.1.1\lib\idea_rt.jar=54274:D:\soft\IntelliJ IDEA 2019.1.1\bin" -Dfile.encoding=UTF-8 -classpath D:\soft\jdk1.8\jre\lib\charsets.jar;D:\soft\jdk1.8\jre\lib\deploy.jar;D:\soft\jdk1.8\jre\lib\ext\access-bridge-64.jar;D:\soft\jdk1.8\jre\lib\ext\cldrdata.jar;D:\soft\jdk1.8\jre\lib\ext\dnsns.jar;D:\soft\jdk1.8\jre\lib\ext\jaccess.jar;D:\soft\jdk1.8\jre\lib\ext\jfxrt.jar;D:\soft\jdk1.8\jre\lib\ext\localedata.jar;D:\soft\jdk1.8\jre\lib\ext\nashorn.jar;D:\soft\jdk1.8\jre\lib\ext\sunec.jar;D:\soft\jdk1.8\jre\lib\ext\sunjce_provider.jar;D:\soft\jdk1.8\jre\lib\ext\sunmscapi.jar;D:\soft\jdk1.8\jre\lib\ext\sunpkcs11.jar;D:\soft\jdk1.8\jre\lib\ext\zipfs.jar;D:\soft\jdk1.8\jre\lib\javaws.jar;D:\soft\jdk1.8\jre\lib\jce.jar;D:\soft\jdk1.8\jre\lib\jfr.jar;D:\soft\jdk1.8\jre\lib\jfxswt.jar;D:\soft\jdk1.8\jre\lib\jsse.jar;D:\soft\jdk1.8\jre\lib\management-agent.jar;D:\soft\jdk1.8\jre\lib\plugin.jar;D:\soft\jdk1.8\jre\lib\resources.jar;D:\soft\jdk1.8\jre\lib\rt.jar;D:\workspace\demo1\target\classes;E:\.m2\repository\org\springframework\boot\spring-boot-starter-aop\2.1.1.RELEASE\spring-
...
...
...
1.8.11.jar;E:\.m2\repository\com\google\guava\guava\20.0\guava-20.0.jar;E:\.m2\repository\org\apache\commons\commons-lang3\3.7\commons-lang3-3.7.jar;E:\.m2\repository\com\alibaba\fastjson\1.2.54\fastjson-1.2.54.jar;E:\.m2\repository\org\springframework\boot\spring-boot\2.1.0.RELEASE\spring-boot-2.1.0.RELEASE.jar;E:\.m2\repository\org\springframework\spring-context\5.1.3.RELEASE\spring-context-5.1.3.RELEASE.jar com.springboot.example.demo.service.TestMain
agentArgs : null
premain load Class     :java/util/concurrent/ConcurrentHashMap$ForwardingNode
premain load Class     :sun/nio/cs/ThreadLocalCoders
premain load Class     :sun/nio/cs/ThreadLocalCoders$1
premain load Class     :sun/nio/cs/ThreadLocalCoders$Cache
premain load Class     :sun/nio/cs/ThreadLocalCoders$2
premain load Class     :java/util/jar/Attributes
premain load Class     :java/util/jar/Manifest$FastInputStream
...
...
...
premain load Class     :java/lang/Class$MethodArray
premain load Class     :java/lang/Void
main start
premain load Class     :sun/misc/VMSupport
premain load Class     :java/util/Hashtable$KeySet
premain load Class     :sun/nio/cs/ISO_8859_1$Encoder
premain load Class     :sun/nio/cs/Surrogate$Parser
premain load Class     :sun/nio/cs/Surrogate
...
...
...
premain load Class     :sun/util/locale/provider/LocaleResources$ResourceReference
main end
premain load Class     :java/lang/Shutdown
premain load Class     :java/lang/Shutdown$Lock

Process finished with exit code 0

```
执行上面的程序我们能够发现：

- 执行main方法之前会加载所有的类，包括系统类和自定义类；
- 在ClassFileTransformer中会去拦截系统类和自己实现的类对象；
- 如果你有对某些类对象进行改写，那么在拦截的时候抓住该类使用字节码编译工具即可实现。

### 使用javassist来动态将某个方法替换掉
```
package com.rickiyang.learn;

import javassist.*;

import java.io.IOException;
import java.lang.instrument.ClassFileTransformer;
import java.security.ProtectionDomain;

public class MyClassTransformer implements ClassFileTransformer {
    @Override
    public byte[] transform(final ClassLoader loader, final String className, final Class<?> classBeingRedefined,final ProtectionDomain protectionDomain, final byte[] classfileBuffer) {
        // 操作Date类
        if ("java/util/Date".equals(className)) {
            try {
                // 从ClassPool获得CtClass对象
                final ClassPool classPool = ClassPool.getDefault();
                final CtClass clazz = classPool.get("java.util.Date");
                CtMethod convertToAbbr = clazz.getDeclaredMethod("convertToAbbr");
                //这里对 java.util.Date.convertToAbbr() 方法进行了改写，在 return之前增加了一个 打印操作
                String methodBody = "{sb.append(Character.toUpperCase(name.charAt(0)));" +
                        "sb.append(name.charAt(1)).append(name.charAt(2));" +
                        "System.out.println(\"sb.toString()\");" +
                        "return sb;}";
                convertToAbbr.setBody(methodBody);

                // 返回字节码，并且detachCtClass对象
                byte[] byteCode = clazz.toBytecode();
                //detach的意思是将内存中曾经被javassist加载过的Date对象移除，如果下次有需要在内存中找不到会重新走javassist加载
                clazz.detach();
                return byteCode;
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
        // 如果返回null则字节码不会被修改
        return null;
    }
}
```

### JVM启动后动态Instrument
上面介绍的Instrumentation是在 JDK 1.5中提供的，开发者只能在main加载之前添加手脚，
在 Java SE 6 的 Instrumentation 当中，提供了一个新的代理操作方法：**agentmain**，可以在 main 函数开始运行之后再运行。

跟premain函数一样， 开发者可以编写一个含有agentmain函数的 Java 类：
```
//采用attach机制，被代理的目标程序VM有可能很早之前已经启动，当然其所有类已经被加载完成，这个时候需要借助Instrumentation#retransformClasses(Class<?>... classes)让对应的类可以重新转换，从而激活重新转换的类执行ClassFileTransformer列表中的回调
public static void agentmain (String agentArgs, Instrumentation inst)

public static void agentmain (String agentArgs)
```

同样，agentmain 方法中带Instrumentation参数的方法也比不带优先级更高。开发者必须在 manifest 文件里面设置“Agent-Class”来指定包含 agentmain 函数的类。

在Java6 以后实现启动后加载的新实现是Attach api。Attach API 很简单，只有 2 个主要的类，都在 com.sun.tools.attach 包里面：

1. VirtualMachine 字面意义表示一个Java 虚拟机，也就是程序需要监控的目标虚拟机，提供了获取系统信息(比如获取内存dump、线程dump，类信息统计(比如已加载的类以及实例个数等)， loadAgent，Attach 和 Detach （Attach 动作的相反行为，从 JVM 上面解除一个代理）等方法，可以实现的功能可以说非常之强大 。该类允许我们通过给attach方法传入一个jvm的pid(进程id)，远程连接到jvm上 。

    代理类注入操作只是它众多功能中的一个，通过loadAgent方法向jvm注册一个代理程序agent，在该agent的代理程序中会得到一个Instrumentation实例，该实例可以 在class加载前改变class的字节码，也可以在class加载后重新加载。在调用Instrumentation实例的方法时，这些方法会使用ClassFileTransformer接口中提供的方法进行处理。

2. VirtualMachineDescriptor 则是一个描述虚拟机的容器类，配合 VirtualMachine 类完成各种功能。


**attach实现动态注入的原理如下：**

通过VirtualMachine类的attach(pid)方法，便可以attach到一个运行中的java进程上，之后便可以通过loadAgent(agentJarPath)来将agent的jar包注入到对应的进程，然后对应的进程会调用agentmain方法。


既然是两个进程之间通信那肯定的建立起连接，VirtualMachine.attach动作类似TCP创建连接的三次握手，目的就是搭建attach通信的连接。而后面执行的操作，例如vm.loadAgent，其实就是向这个socket写入数据流，接收方target VM会针对不同的传入数据来做不同的处理。

我们来测试一下agentmain的使用：

工程结构和 上面premain的测试一样，编写AgentMainTest，然后使用maven插件打包 生成MANIFEST.MF。
```
package com.rickiyang.learn;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.lang.instrument.Instrumentation;
import java.security.ProtectionDomain;


public class AgentMainTest {

    public static void agentmain(String agentArgs, Instrumentation instrumentation) {
        instrumentation.addTransformer(new DefineTransformer(), true);
    }
    
    static class DefineTransformer implements ClassFileTransformer {

        @Override
        public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException {
            System.out.println("premain load Class:" + className);
            return classfileBuffer;
        }
    }
}
```
```
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-jar-plugin</artifactId>
  <version>3.1.0</version>
  <configuration>
    <archive>
      <!--自动添加META-INF/MANIFEST.MF -->
      <manifest>
        <addClasspath>true</addClasspath>
      </manifest>
      <manifestEntries>
        <Agent-Class>com.rickiyang.learn.AgentMainTest</Agent-Class>
        <Can-Redefine-Classes>true</Can-Redefine-Classes>
        <Can-Retransform-Classes>true</Can-Retransform-Classes>
      </manifestEntries>
    </archive>
  </configuration>
</plugin>
```
将agent打包之后，就是编写测试main方法。上面我们画的图中的步骤是：从一个attach JVM去探测目标JVM，如果目标JVM存在则向它发送agent.jar。我测试写的简单了些，找到当前JVM并加载agent.jar。
```
package com.rickiyang.learn.job;

import com.sun.tools.attach.*;

import java.io.IOException;
import java.util.List;

public class TestAgentMain {

    public static void main(String[] args) throws IOException, AttachNotSupportedException, AgentLoadException, AgentInitializationException {
        //获取当前系统中所有 运行中的 虚拟机
        System.out.println("running JVM start ");
        List<VirtualMachineDescriptor> list = VirtualMachine.list();
        for (VirtualMachineDescriptor vmd : list) {
            //如果虚拟机的名称为 xxx 则 该虚拟机为目标虚拟机，获取该虚拟机的 pid
            //然后加载 agent.jar 发送给该虚拟机
            System.out.println(vmd.displayName());
            if (vmd.displayName().endsWith("com.rickiyang.learn.job.TestAgentMain")) {
                VirtualMachine virtualMachine = VirtualMachine.attach(vmd.id());
                virtualMachine.loadAgent("/Users/yangyue/Documents/java-agent.jar");
                virtualMachine.detach();
            }
        }
    }

}
```

list()方法会去寻找当前系统中所有运行着的JVM进程，你可以打印vmd.displayName()看到当前系统都有哪些JVM进程在运行。因为main函数执行起来的时候进程名为当前类名，所以通过这种方式可以去找到当前的进程id。

注意：在mac上安装了的jdk是能直接找到 VirtualMachine 类的，但是在windows中安装的jdk无法找到，如果你遇到这种情况，请手动将你jdk安装目录下：lib目录中的tools.jar添加进当前工程的Libraries中。
运行main方法的输出为：
```
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 -javaagent:..
running JVM start 
premain load Class:...
premain load Class:...
premain load Class:...
```
可以看到实际上是启动了一个socket进程去传输agent.jar。先打印了“running JVM start”表名main方法是先启动了，然后才进入代理类的transform方法。


### instrument原理
instrument的底层实现依赖于JVMTI(JVM Tool Interface)，它是JVM暴露出来的一些供用户扩展的接口集合，JVMTI是基于事件驱动的，JVM每执行到一定的逻辑就会调用一些事件的回调接口（如果有的话），这些接口可以供开发者去扩展自己的逻辑。
JVMTIAgent是一个利用JVMTI暴露出来的接口提供了代理启动时加载(agent on load)、代理通过attach形式加载(agent on attach)和代理卸载(agent on unload)功能的动态库。
而instrument agent可以理解为一类JVMTIAgent动态库，别名是JPLISAgent(Java Programming Language Instrumentation Services Agent)，也就是专门为java语言编写的插桩服务提供支持的代理。

**启动时加载instrument agent过程：**
1. 创建并初始化 JPLISAgent；

2. 监听 VMInit 事件，在 JVM 初始化完成之后做下面的事情：

    1. 创建 InstrumentationImpl 对象 ；

    2. 监听 ClassFileLoadHook 事件 ；

    3. 调用 InstrumentationImpl 的loadClassAndCallPremain方法，在这个方法里会去调用 javaagent 中 MANIFEST.MF 里指定的Premain-Class 类的 premain 方法 ；

3. 解析 javaagent 中 MANIFEST.MF 文件的参数，并根据这些参数来设置 JPLISAgent 里的一些内容。

**运行时加载instrument agent过程：**
通过 JVM 的attach机制来请求目标 JVM 加载对应的agent，过程大致如下：

1. 创建并初始化JPLISAgent；
2. 解析 javaagent 里 MANIFEST.MF 里的参数；
3. 创建 InstrumentationImpl 对象；
4. 监听 ClassFileLoadHook 事件；
5. 调用 InstrumentationImpl 的loadClassAndCallAgentmain方法，在这个方法里会去调用javaagent里 MANIFEST.MF 里指定的Agent-Class类的agentmain方法。

**Instrumentation的局限性#**
大多数情况下，我们使用Instrumentation都是使用其字节码插桩的功能，或者笼统说就是类重定义(Class Redefine)的功能，但是有以下的局限性：

1. premain和agentmain两种方式修改字节码的时机都是类文件加载之后，也就是说必须要带有Class类型的参数，不能通过字节码文件和自定义的类名重新定义一个本来不存在的类。
2. 类的字节码修改称为类转换(Class Transform)，类转换其实最终都回归到类重定义Instrumentation#redefineClasses()方法，此方法有以下限制： 
   1. 新类和老类的父类必须相同；
   2. 新类和老类实现的接口数也要相同，并且是相同的接口；
   3. 新类和老类访问符必须一致。 新类和老类字段数和字段名要一致；
   4. 新类和老类新增或删除的方法必须是private static/final修饰的；
   5. 可以修改方法体。

除了上面的方式，如果想要重新定义一个类，可以考虑基于类加载器隔离的方式：创建一个新的自定义类加载器去通过新的字节码去定义一个全新的类，不过也存在只能通过反射调用该全新类的局限性。










