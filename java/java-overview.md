# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## java-overview总述

### jdk包结构（linux 1.8版本）
- **bin目录**: JDK开发工具的可执行文件
- **lib目录**: 开发工具使用的归档包文件
- **jre**:  Java 运行时环境的根目录，包含Java虚拟机，运行时的类包和Java应用启动器， 但不包含开发环境中的开发工具
- demo: 含有源代码的程序示例
- include: 包含C语言头文件,支持Java本地接口与Java虚拟机调试程序接口的本地编程技术

### jdk中jar包结构功能树（linux 1.8版本）
- rt.jar：Java基础类库，也就是Java doc里面看到的所有的类的class文件。
- tools.jar：是系统用来编译一个类的时候用到的，即执行javac的时候用到。
- dt.jar：dt.jar是关于运行环境的类库，主要是swing包。
- i18n.jar: 字符转换类及其它与国际化和本地化有关的类。

### JDK内置命令行工具
JDK 内置了许多命令行工具，它们可用来获取目标 JVM 不同方面、不同层次的信息。

- jinfo - 用于实时查看和调整目标 JVM 的各项参数。
- jstack - 用于获取目标 Java 进程内的线程堆栈信息，可用来检测死锁、定位死循环等。
- jmap - 用于获取目标 Java 进程的内存相关信息，包括 Java 堆各区域的使用情况、堆中对象的统计信息、类加载信息等。
- jstat - 一款轻量级多功能监控工具，可用于获取目标 Java 进程的类加载、JIT 编译、垃圾收集、内存使用等信息。
- jcmd - 相比 jstat 功能更为全面的工具，可用于获取目标 Java 进程的性能统计、JFR、内存使用、垃圾收集、线程堆栈、JVM 运行时间等信息。

使用示例：
```
jstat -gc <pid>：显示垃圾收集信息，包括堆内存的使用情况。
S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
26176.0 26176.0  0.0   21463.1 209792.0 76414.7   786432.0   36711.4   86504.0 81289.3 11356.0 10504.3     21    0.461   6      0.115    0.576

- S0C：第一个幸存区的大小
- S1C：第二个幸存区的大小
- S0U：第一个幸存区的使用大小
- S1U：第二个幸存区的使用大小
- EC：Eden区的大小
- EU：Eden区的使用大小（重点关注）
- OC：老年代大小
- OU：老年代使用大小（重点关注）
- MC：元空间大小
- MU：元空间使用大小
- CCSC：压缩类空间大小
- CCSU：压缩类空间使用大小
- YGC：年轻代垃圾回收次数（重点关注）
- YGCT：年轻代垃圾回收消耗时间
- FGC：老年代垃圾回收次数（重点关注）
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

jmap -heap <pid>：显示堆的内存映射并输出到文件（调用失败）
```

#### jmap

* jmap（linux下特有，也是很常用的一个命令）观察运行中的jvm物理内存的占用情况。
  参数如下：  
  -heap：打印jvm heap的情况  
  -histo：打印jvm heap的直方图。其输出信息包括类名，对象数量，对象占用大小。  
  -histo：live ：同上，但是只答应存活对象的情况  
  -permstat：打印permanent generation heap情况

#### jstack

* jstack（linux下特有）可以观察到jvm中当前所有线程的运行情况和线程当前状态

#### jconsole

jconsole：Jconsole （Java Monitoring and Management Console），一种基于JMX的可视化监视、管理工具。
JConsole 基本包括以下基本功能：概述、内存、线程、类、VM概要、MBean

- 内存监控
  内存页签相对于可视化的jstat 命令，用于监视受收集器管理的虚拟机内存。

- 线程监控
  如果上面的“内存”页签相当于可视化的jstat命令的话，“线程”页签的功能相当于可视化的jstack命令，遇到线程停顿时可以使用这个页签进行监控分析。线程长时间停顿的主要原因主要有：等待外部资源（数据库连接、网络资源、设备资
  源等）、死循环、锁等待（活锁和死锁）

#### jvisualvm
jvisualvm：VisualVM（All-in-One Java Troubleshooting Tool）;功能最强大的运行监视和故障处理程序
- 显示虚拟机进程以及进程的配置、环境信息（jps、jinfo）。
- 监视应用程序的CPU、GC、堆、方法区(1.7及以前)，元空间（JDK1.8及以后）以及线程的信息（jstat、jstack）。
- dump以及分析堆转储快照（jmap、jhat）。
- 方法级的程序运行性能分析，找出被调用最多、运行时间最长的方法。
- 离线程序快照：收集程序的运行时配置、线程dump、内存dump等信息建立一个快照

平时启动jvisualvm
1. /usr/libexec/java_home -V
2. cd /Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/bin
3. jvisualvm

#### jstat
* jstat最后要重点介绍下这个命令。这是jdk命令中比较重要，也是相当实用的一个命令，可以观察到classloader，compiler，gc相关信息
  具体参数如下：
  -class：统计class loader行为信息  
  -compile：统计编译行为信息  
  -gc：统计jdk gc时heap信息  
  -gccapacity：统计不同的generations（不知道怎么翻译好，包括新生区，老年区，permanent区）相应的heap容量情况  
  -gccause：统计gc的情况，（同-gcutil）和引起gc的事件  
  -gcnew：统计gc时，新生代的情况  
  -gcnewcapacity：统计gc时，新生代heap容量  
  -gcold：统计gc时，老年区的情况  
  -gcoldcapacity：统计gc时，老年区heap容量  
  -gcpermcapacity：统计gc时，permanent区heap容量  
  -gcutil：统计gc时，heap情况  
  -printcompilation：不知道干什么的，一直没用过。



------

### 常见问题处理
#### * 例:一次排查线上问题进行解答。
~~~~
输入jps，获得进程号。
top -Hp pid 获取本进程中所有线程的CPU耗时性能
jstack pid命令查看当前java进程的堆栈状态
或者 jstack -l pid > /tmp/output.txt 把堆栈信息打到一个txt文件。
jstack -l 951610 > output.txt
可以使用fastthread 堆栈定位，fastthread.io/
~~~~

#### 如果发生OOM，日志很多，该如何处理
查看dump日志
```
java -jar 
-verbose:gc 
-XX:+PrintGCDetails 
-XX:+PrintGCTimeStamps 
-Xloggc:/usr/local/gclog/gc.log         #gc日志 
-XX:+HeapDumpOnOutOfMemoryError         #配置在OOM时自动生成dump文件
-XX:HeapDumpPath=/usr/local/gclog/dump.hprof    #dump存储路径
```

如果日志过多，几十G上百G，分析起来可能也不太现实，所以可以使用dump分析：

java获取内存dump中的数据的几种方式：
1. 获取内存详情：jmap -dump:format=b,file=e.bin pid号
这种方式可以用jvisualvm.exe进行内存分析，或者采用Eclipse Memory Analysis Tools（MAT）这个工具

2. 获取内存dump:jmap -histo:live pid号
这个方式会先fullgc，如果不希望触发fullgc可以使用jmap -histo pid号

3. 第三种方式：jdk启动加参数
-XX：+HeapDumpBeforeFullGC
-XX：HeapDumpPath=/httx/logs/dump
这种方式会产生dump日志，在通过jvisualvm.exe或者Eclipse Memory Analysis Tools工具进行分析。

最后，可以看一下GC日志，观测老年代溢出，还是元空间溢出。
老年代溢出需要看dump文件中哪些对象没有释放，从而分析代码。
元空间溢出排查比较麻烦，dump看不出来，一般是因为重复加载class太多，可以分析问题代码，针对性压测，观察class数量


### json及诶西集中方式
- jackson：反射+反射缓存、良好的stream支持、高效的内存管理
- fastjson：
  - jvm虚拟机：通过ASM库运行时生成parser字节码，支持的field不能超过200个。参考：FastJson使用ASM反序列化。
  - android虚拟机：反射的方式。
- gson：反射+反射缓存、支持部分stream、内存性能较差（gc问题）。

#### Jackson
依赖
```
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.9.6</version>
</dependency>
```
使用代码
```
private static ObjectMapper mapper=new ObjectMapper();
String userJson = "{\"name\": \"jack\",\"age\": \"20\"}";

//JSON字符串->Java对象
User user = objectMapper.readValue(userJson, User.class);
//JSON数组字符串->Java对象数组
User[] users = objectMapper.readValue(usersJson, User[].class);

//Java对象转JSON字符串
String jack = objectMapper.writeValueAsString(new User("jack", 20));

//转二进制数组
byte[] jacks = objectMapper.writeValueAsBytes(new User("jack", 20));
```


#### Gson
```
Gson gson = new Gson();
Person person = gson.fromJson(jsonData, Person.class);
```

#### fastjson
```
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>fastjson</artifactId>
                <version>${fastjson.version}</version>
            </dependency>
```

JSONObject解析
```
例：
{
  "code": 200,
  "message": "操作成功！",
  "data": {[
    "address": "TEfgYPLn3Z8RJ3mTJiwwHtkukZspUWPreF",
    "list": []
  ]}
}
//java对象转json字符串
String result = JSON.toJSONString(dingMsg);
//截取字符串
String realResult = result.substring(4, result.length());
//字符串转换JSONObject 对象：
JSONObject data = JSON.parseObject(realResult);
//解析对象：
Class class = JSON.parseObject(realResult , xxx.class);
//解析对象数组
JSONObject jsonObject = data.getJSONObject("data");
List<Class> trxToCNYRate = JSONObject.parseArray(jsonObject.toJSONString(), Class.class);

//获取JSONObject中的对象并解析
JSONObject jsonObject = data.getJSONObject("data");
Class class = JSON.parseObject(jsonObject.toJSONString(), xxx.class);
//解析JSONObject中的string
String obj = data.getJSONObject("data").getString("address");
```

### java集成多数据源
**方式一：mybatis-plus**
依赖：
```
        <dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.5.1</version>
		</dependency>
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-core</artifactId>
			<version>3.5.1</version>
		</dependency>
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-generator</artifactId>
			<version>3.5.1</version>
		</dependency>
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>dynamic-datasource-spring-boot-starter</artifactId>
			<version>3.5.1</version>
		</dependency>
```
配置文件：
```
datasource:
  dynamic:
    primary: master
    datasource:
      master:
        url: jdbc:postgresql://192.168.0.1:3306/test1?ApplicationName=${spring.application.name}
        username: root
        password: 123456
      slave1:
        url: jdbc:postgresql://192.168.0.2:6003/test2?ApplicationName=${spring.application.name}
        username: root
        password: 123456
```
在Mapper上注解：
```
@DS("slave1")
```

### Mapstruct转换
Mapstruct：
```
实体属性相同、名称不同转换：
@Mappings({ @Mapping(source="grade", target="level") })
属性、常量转换：
@Mapping(target="ordType",constant="3")
```

基本属性与枚举互相转换：
```
自定义CustomMapper转换类，并在mapper接口中引用
@Mapper(uses={DateMapper.class,CustomMapper.class})
public interface OverTimeOrderMapper {
    
    PrepaidGatewayBase converterPrepaidGatewayBase(PayOnlineContext context);
    
    ...
}
```

//自定义mapper转换类
```
public class DateMapper {
    public DateMapper() {
    }

    public Long asLong(Instant date) {
        return date.toEpochMilli();
    }

    public Instant asInstant(Long date) {
        return Instant.ofEpochMilli(date);
    }
}
```

```
public class CustomMapper {
    // 枚举转基础字段
    public Integer asOrderStateInteger(OverTimeOrderState state){
        return state.getCode();
    }
    // 基础字段转枚举
    public OverTimeOrderState asEnumState(Integer state){
        for(OverTimeOrderStateoverTimeOrderState:OverTimeOrderState.values()){
            if(overTimeOrderState.getCode().equals(state)){
                return overTimeOrderState;
            }
        }
    }
}
```

### Java面向对象的三个特征与含义
* 继承：继承是从已有类得到继承信息创建新类的过程。提供继承信息的类被称为父类（超类、基类）；得到继承信息的类被称为子类（派生类）。继承让变化中的软件系统有了一定的延续性，同时继承也是封装程序中可变因素的重要手段。
* 封装：通常认为封装是把数据和操作数据的方法绑定起来，对数据的访问只能通过已定义的接口。面向对象的本质就是将现实世界描绘成一系列完全自治、封闭的对象。我们在类中编写的方法就是对实现细节的一种封装；我们编写一个类就是对数据和数据操作的封装。可以说，封装就是隐藏一切可隐藏的东西，只向外界提供最简单的编程接口（可以想想普通洗衣机和全自动洗衣机的差别，明显全自动洗衣机封装更好因此操作起来更简单；我们现在使用的智能手机也是封装得足够好的，因为几个按键就搞定了所有的事情）。
* 多态：多态性是指允许不同子类型的对象对同一消息作出不同的响应。简单的说就是用同样的对象引用调用同样的方法但是做了不同的事情。多态性分为编译时的多态性和运行时的多态性。如果将对象的方法视为对象向外界提供的服务，那么运行时的多态性可以解释为：当A系统访问B系统提供的服务时，B系统有多种提供服务的方式，但一切对A系统来说都是透明的（就像电动剃须刀是A系统，它的供电系统是B系统，B系统可以使用电池供电或者用交流电，甚至还有可能是太阳能，A系统只会通过B类对象调用供电的方法，但并不知道供电系统的底层实现是什么，究竟通过何种方式获得了动力）。方法重载（overload）实现的是编译时的多态性（也称为前绑定），而方法重写（override）实现的是运行时的多态性（也称为后绑定）。运行时的多态是面向对象最精髓的东西，要实现多态需要做两件事：1. 方法重写（子类继承父类并重写父类中已有的或抽象的方法）；2. 对象造型（用父类型引用引用子类型对象，这样同样的引用调用同样的方法就会根据子类对象的不同而表现出不同的行为）。

### java多态的实现原理
* 当JVM执行Java字节码时，类型信息会存储在方法区中，为了优化对象的调用方法的速度，方法区的类型信息会增加一个指针，该指针指向一个记录该类方法的方法表，方法表中的每一个项都是对应方法的指针。

* 方法区：方法区和JAVA堆一样，是各个线程共享的内存区域，用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
* 运行时常量池：它是方法区的一部分，Class文件中除了有类的版本、方法、字段等描述信息外，还有一项信息是常量池，用于存放编译器生成的各种符号引用，这部分信息在类加载时进入方法区的运行时常量池中。
* 方法区的内存回收目标是针对常量池的回收及对类型的卸载。
* 方法表的构造
  * 由于java的单继承机制，一个类只能继承一个父类，而所有的类又都继承Object类，方法表中最先存放的是Object的方法，接下来是父类的方法，最后是该类本身的方法。如果子类改写了父类的方法，那么子类和父类的那些同名的方法共享一个方法表项。
  * 由于这样的特性，使得方法表的偏移量总是固定的，例如，对于任何类来说，其方法表的equals方法的偏移量总是一个定值，所有继承父类的子类的方法表中，其父类所定义的方法的偏移量也总是一个定值。
* 实例
  * 假设Class A是Class B的子类，并且A改写了B的方法的method()，那么B来说，method方法的指针指向B的method方法入口；对于A来说，A的方法表的method项指向自身的method而非父类的。
  * 流程：调用方法时，虚拟机通过对象引用得到方法区中类型信息的方法表的指针入口，查询类的方法表 ，根据实例方法的符号引用解析出该方法在方法表的偏移量，子类对象声明为父类类型时，形式上调用的是父类的方法，此时虚拟机会从实际的方法表中找到方法地址，从而定位到实际类的方法。
  * 注：所有引用为父类，但方法区的类型信息中存放的是子类的信息，所以调用的是子类的方法表。

### 堆和栈
* 栈
  是一种连续储存的数据结构，具有先进后出的性质。
  通常的操作有入栈（压栈），出栈和栈顶元素。想要读取栈中的某个元素，就是将其之间的所有元素出栈才能完成。
  栈溢出(StackOverflowError)：栈溢出就是方法执行是创建的栈帧超过了栈的深度。那么最有可能的就是方法递归调用产生这种结果

* 堆
  是一种非连续的树形储存数据结构，每个节点有一个值，整棵树是经过排序的。特点是根结点的值最小（或最大），且根结点的两个子树也是一个堆。常用来实现优先队列，存取随意。
  堆溢出(OutOfMemoryError）：堆中主要存储的是对象。如果不断的new对象则会导致堆中的空间溢出


### java语言的反射机制

* JAVA反射机制是在运行状态中, 动态获取的信息以及动态调用对象的方法
  * 对于任意一个类, 都能够知道这个类的所有属性和方法;
  * 对于任意一个对象, 都能够调用它的任意一个方法和属性;

* 主要作用有三：
  * 运行时取得类的方法和字段的相关信息。
  * 创建某个类的新实例(.newInstance())
  * 取得字段引用直接获取和设置对象字段，无论访问修饰符是什么。

* 用处如下：
  * 观察或操作应用程序的运行时行为。
  * 调试或测试程序，因为可以直接访问方法、构造函数和成员字段。
  * 通过名字调用不知道的方法并使用该信息来创建对象和调用方法。


### 泛型的优缺点
* 优点：
  * 使用泛型类型可以最大限度地重用代码、保护类型的安全以及提高性能。泛型最常见的用途是创建集合类。
* 缺点：
  * 在性能上不如数组快。
* 泛型常用特点，List`<String>`能否转为List`<Object>`**
  * 能，但是利用类都继承自Object，所以使用是每次调用里面的函数都要通过强制转换还原回原来的类，这样既不安全，运行速度也慢。

Java泛型中的标记符含义：
E - Element (在集合中使用，因为集合中存放的是元素)
T - Type（Java 类）
K - Key（键）
V - Value（值）
N - Number（数值类型）
？ -  表示不确定的java类型
S、U、V  - 2nd、3rd、4th typ

###集合

#### Collection包结构，与Collections的区别
* Collection是一个接口，它是Set、List等容器的父接口；
* Collections是一个工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、排序、线程安全化等等。

#### ArrayList、LinkedList、Vector的底层实现和区别
* 从同步性来看，ArrayList和LinkedList是不同步的，而Vector是的。所以线程安全的话，可以使用ArrayList或LinkedList，可以节省为同步而耗费的开销。但在多线程下，有时候就不得不使用Vector了。当然，也可以通过一些办法包装ArrayList、LinkedList，使我们也达到同步，但效率可能会有所降低。
* 从内部实现机制来讲ArrayList和Vector都是使用Object的数组形式来存储的。当你向这两种类型中增加元素的时候，如果元素的数目超出了内部数组目前的长度它们都需要扩展内部数组的长度，Vector缺省情况下自动增长原来一倍的数组长度，ArrayList是原来的50%，所以最后你获得的这个集合所占的空间总是比你实际需要的要大。如果你要在集合中保存大量的数据，那么使用Vector有一些优势，因为你可以通过设置集合的初始化大小来避免不必要的资源开销。
* ArrayList和Vector中，从指定的位置（用index）检索一个对象，或在集合的末尾插入、删除一个对象的时间是一样的，可表示为O(1)。但是，如果在集合的其他位置增加或者删除元素那么花费的时间会呈线性增长O(n-i)，其中n代表集合中元素的个数，i代表元素增加或移除元素的索引位置，因为在进行上述操作的时候集合中第i和第i个元素之后的所有元素都要执行(n-i)个对象的位移操作。LinkedList底层是由双向循环链表实现的，LinkedList在插入、删除集合中任何位置的元素所花费的时间都是一样的O(1)，但它在索引一个元素的时候比较慢，为O(i)，其中i是索引的位置，如果只是查找特定位置的元素或只在集合的末端增加、移除元素，那么使用Vector或ArrayList都可以。如果是对其它指定位置的插入、删除操作，最好选择LinkedList。

#### Map、HashMap、HashTable、LinkedHashMap、TreeMap的底层实现区别
* Map主要用于存储健值对，根据键得到值，因此不允许键重复(重复了覆盖了),但允许值重复。
* Hashmap 是一个最常用的Map,它根据键的HashCode 值存储数据,根据键可以直接获取它的值，具有很快的访问速度，遍历时，取得数据的顺序是完全随机的。HashMap最多只允许一条记录的键为Null;允许多条记录的值为 Null;HashMap不支持线程的同步，即任一时刻可以有多个线程同时写HashMap;可能会导致数据的不一致。如果需要同步，可以用 Collections的synchronizedMap方法使HashMap具有同步的能力，或者使用ConcurrentHashMap。
* Hashtable与 HashMap类似,它继承自Dictionary类，不同的是:它不允许记录的键或者值为空;它支持线程的同步，即任一时刻只有一个线程能写Hashtable,因此也导致了 Hashtable在写入时会比较慢。
* LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的.也可以在构造时用带参数，按照应用次数排序。在遍历的时候会比HashMap慢，不过有种情况例外，当HashMap容量很大，实际数据较少时，遍历起来可能会比LinkedHashMap慢，因为LinkedHashMap的遍历速度只和实际数据有关，和容量无关，而HashMap的遍历速度和他的容量有关。
* TreeMap实现SortMap接口，能够把它保存的记录根据键排序,默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的

#### Map、Set、List、Queue、Stack的特点与用法。**
* Collection 是对象集合，
* Collection 有两个子接口 List 和 Set
  * List 可以通过下标 (1,2..) 来取得值，值可以重复，而 Set 只能通过游标来取值，并且值是不能重复的

* ArrayList ， Vector ， LinkedList 是 List 的实现类
  * ArrayList 是线程不安全的， Vector 是线程安全的，这两个类底层都是由数组实现的
  * LinkedList 是线程不安全的，底层是由链表实现的

* Map 是键值对集合
  * HashTable 和 HashMap 是 Map 的实现类
  * HashTable 是线程安全的，不能存储 null 值
  * HashMap 不是线程安全的，可以存储 null 值
  * Stack类：继承自Vector，实现一个后进先出的栈。提供了几个基本方法，push、pop、peak、empty、search等。
  * Queue接口：提供了几个基本方法，offer、poll、peek等。已知实现类有LinkedList、PriorityQueue等。


#### Interface与abstract类的区别
* 抽象类和接口都不能够实例化，但可以定义抽象类和接口类型的引用。
* 一个类如果继承了某个抽象类或者实现了某个接口都需要对其中的抽象方法全部进行实现，否则该类仍然需要被声明为抽象类。
* 接口比抽象类更加抽象，因为抽象类中可以定义构造器，可以有抽象方法和具体方法，而接口中不能定义构造器而且其中的方法全部都是抽象方法。
* 抽象类中的成员可以是private、默认、protected、public的，而接口中的成员全都是public的。
* 抽象类中可以定义成员变量，而接口中定义的成员变量实际上都是常量。
* 有抽象方法的类必须被声明为抽象类，而抽象类未必要有抽象方法。


#### Static class 与non static class的区别
内部静态类不需要有指向外部类的引用。
但非静态内部类需要持有对外部类的引用。
非静态内部类能够访问外部类的静态和非静态成员。
静态类不能访问外部类的非静态成员。他只能访问外部类的静态成员。
一个非静态内部类不能脱离外部类实体被创建，一个非静态内部类可以访问外部类的数据和方法，因为他就在外部类里面。

#### foreach与正常for循环效率对比
直接for循环效率最高，其次是迭代器和 ForEach操作。
作为语法糖，其实 ForEach 编译成 字节码之后，使用的是迭代器实现的，反编译后，testForEach方法如下：

```
public static void testForEach(List list) {  
    for (Iterator iterator = list.iterator(); iterator.hasNext();) {  
        Object t = iterator.next();  
        Object obj = t;  
    }  
}  
```
可以看到，只比迭代器遍历多了生成中间变量这一步，因为性能也略微下降了一些。

### 代理模式
- 静态代理：运行之前，代理类.class文件就已经被创建了
- 动态代理：是在程序运行时通过反射机制动态创建的。

**实现方式：**
1. 设计模式：`代理模式`静态的实现AOP

2. aspectj静态代理实现AOP	//@Aspect	aspectj帮我们将功能，在编译的阶段将织入到被代理的class文件里面。因为是静态织入，所以性能比较不错
   静态代理总结：
   优点：可以做到在符合开闭原则的情况下对目标对象进行功能扩展。
   缺点：我们得为每一个服务都得创建代理类，工作量太大，不易管理。同时接口一旦发生改变，代理类也得相应修改。

3. jdk动态代理实现AOP	//implements InvocationHandle
~~~~
11 public class DynamicProxyHandler implements InvocationHandler {
12 
13     private Object object;
14 
15     public DynamicProxyHandler(final Object object) {
16         this.object = object;
17     }
18 
19     @Override
20     public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
21         System.out.println("买房前准备");
22         Object result = method.invoke(object, args);
23         System.out.println("买房后装修");
24         return result;
25     }
26 }

15     public static void main(String[] args) {
16         BuyHouse buyHouse = new BuyHouseImpl();
17         BuyHouse proxyBuyHouse = (BuyHouse) Proxy.newProxyInstance(BuyHouse.class.getClassLoader(), new
18                 Class[]{BuyHouse.class}, new DynamicProxyHandler(buyHouse));
19         proxyBuyHouse.buyHosue();
20     }
~~~~

动态代理总结：虽然相对于静态代理，动态代理大大减少了我们的开发任务，同时减少了对业务接口的依赖，降低了耦合度。但是还是有一点点小小的遗憾之处，那就是它始终无法摆脱仅支持interface代理的桎梏，

4. cglib动态代理实现AOP	// implements MethodInterceptor
   在实际开发中，可能需要对没有实现接口的类增强，用JDK动态代理的方式就没法实现。采用Cglib动态代理可以对没有实现接口的类产生代理，实际上是生成了目标类的子类来增强
~~~~
    public void test2() {
        final LinkManDao linkManDao = new LinkManDao();
        // 创建cglib核心对象
        Enhancer enhancer = new Enhancer();
        // 设置父类
        enhancer.setSuperclass(linkManDao.getClass());
        // 设置回调
        enhancer.setCallback(new MethodInterceptor() {  //***
            /**
             * 当你调用目标方法时，实质上是调用该方法
             * intercept四个参数：
             * proxy:代理对象
             * method:目标方法
             * args：目标方法的形参
             * methodProxy:代理方法
            */
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy)
                    throws Throwable {
                System.out.println("记录日志");
                 Object result = method.invoke(linkManDao, args);
                return result;
            }
        });
        // 创建代理对象
        LinkManDao proxy = (LinkManDao) enhancer.create();
        proxy.save();
    }
~~~~
CGLIB代理总结： CGLIB创建的动态代理对象比JDK创建的动态代理对象的性能更高，但是CGLIB创建代理对象时所花费的时间却比JDK多得多。所以对于单例的对象，因为无需频繁创建对象，用CGLIB合适，反之使用JDK方式要更为合适一些。同时由于CGLib由于是采用动态创建子类的方法，对于final修饰的方法无法进行代理。


### Override和Overload的含义与区别

* Overload：顾名思义，就是Over(重新)——load（加载），所以中文名称是重载。它可以表现类的多态性，可以是函数里面可以有相同的函数名但是参数名、类型不能相同；或者说可以改变参数、类型但是函数名字依然不变。
* Override：就是ride(重写)的意思，在子类继承父类的时候子类中可以定义某方法与其父类有相同的名称和参数，当子类在调用这一函数时自动调用子类的方法，而父类相当于被覆盖（重写）了。
* 方法的重写Overriding和重载Overloading是Java多态性的不同表现。重写Overriding是父类与子类之间多态性的一种表现，重载Overloading是一个类中多态性的一种表现。如果在子类中定义某方法与其父类有相同的名称和参数，我们说该方法被重写 (Overriding)。子类的对象使用这个方法时，将调用子类中的定义，对它而言，父类中的定义如同被“屏蔽”了。如果在一个类中定义了多个同名的方法，它们或有不同的参数个数或有不同的参数类型，则称为方法的重载(Overloading)。Overloaded的方法是可以改变返回值的类型。


### Object有哪些公用方法？
1．clone方法
保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。
主要是JAVA里除了8种基本类型传参数是值传递，其他的类对象传参数都是引用传递，我们有时候不希望在方法里讲参数改变，这是就需要在类中复写clone方法。

2．getClass方法
final方法，获得运行时类型。

3．toString方法
该方法用得比较多，一般子类都有覆盖。

4．finalize方法
该方法用于释放资源。因为无法确定该方法什么时候被调用，很少使用。

5．equals方法
该方法是非常重要的一个方法。一般equals和==是不一样的，但是在Object中两者是一样的。子类一般都要重写这个方法。

6．hashCode方法
该方法用于哈希查找，可以减少在查找中使用equals的次数，重写了equals方法一般都要重写hashCode方法。这个方法在一些具有哈希功能的Collection中用到。
一般必须满足obj1.equals(obj2)==true。可以推出obj1.hashCode()==obj2.hashCode()，但是hashCode相等不一定就满足equals。不过为了提高效率，应该尽量使上面两个条件接近等价。
如果不重写hashCode(),在HashSet中添加两个equals的对象，会将两个对象都加入进去。

7．wait方法
* wait方法就是使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者被中断。wait(long timeout)设定一个超时间隔，如果在规定时间内没有获得锁就返回。
  调用该方法后当前线程进入睡眠状态，直到以下事件发生。
  1. 其他线程调用了该对象的notify方法。
  2. 其他线程调用了该对象的notifyAll方法。
  3. 其他线程调用了interrupt中断该线程。
  4. 时间间隔到了。此时该线程就可以被调度了，如果是被中断的话就抛出一个InterruptedException异常。

8．notify方法
该方法唤醒在该对象上等待的某个线程。

9．notifyAll方法
该方法唤醒在该对象上等待的所有线程。

### java 缩略图
```
<!-- 缩略图 -->
<dependency>
	<groupId>net.coobird</groupId>
	<artifactId>thumbnailator</artifactId>
	<version>0.4.14</version>
</dependency>

代码：
ByteArrayOutputStream stream = new ByteArrayOutputStream();
Thumbnails.of(new InputStream[]{is}).imageType(2).scalingMode(ScalingMode.PROGRESSIVE_BILINEAR).size(width, high).toOutputStream(stream);
```

### nginx限制文件上传大小
Nginx 默认的最大上传文件大小限制是1MB。如果你需要更改这个限制，你需要在 Nginx 配置文件中设置 client_max_body_size 指令。
client_max_body_size 100M;

### springboot限制文件上传大小
spring:
servlet:
multipart:
max-file-size: 100MB
max-request-size: 100MB

### tcp不间断重连(参考文档：https://blog.csdn.net/qq_41142992/article/details/139408581)（并未解决）
ubuntu 设置open files
临时生效：		ulimit -n 65535
永久设置（会在重启后保留）：
编辑/etc/security/limits.conf文件，并添加或修改以下行：
* soft nofile 65535
* hard nofile 65535
修改/etc/pam.d/common-session和/etc/pam.d/common-session-noninteractive文件，添加以下行：
session required pam_limits.so
重启系统

查看指定端口tcp连接信息（tcpdump tcp port 80）

### 接口返回文件流
```
// 设置响应的内容类型（根据你的文件类型设置）
response.setContentType("text/plain");
// 设置响应头，以告诉浏览器这是一个附件，应该被下载而不是显示
String fileName = "file.txt"; // 客户端将看到的文件名
response.setHeader("Content-Disposition", "attachment; filename=\"" + fileName + "\"");
// 读取文件并写入到输出流中
try (FileInputStream inputStream = new FileInputStream(filePath);
ServletOutputStream outputStream = response.getOutputStream()) {

        byte[] buffer = new byte[4096]; // 缓冲区大小
        int bytesRead;
        while ((bytesRead = inputStream.read(buffer)) != -1) {
            outputStream.write(buffer, 0, bytesRead);
        }

        // 输出流在try-with-resources块结束时会自动关闭
    } catch (FileNotFoundException e) {
        // 处理文件未找到的情况
        response.sendError(HttpServletResponse.SC_NOT_FOUND);
    }
```
首先设置响应的内容类型（对于文本文件，通常是text/plain），
然后设置Content-Disposition响应头，以告诉浏览器这是一个应该被下载的附件。
我们使用FileInputStream来读取文件的内容，并将其写入到ServletOutputStream中。
我们使用了一个缓冲区来提高效率，并在循环中读取和写入数据，直到文件的所有内容都被发送。最后，当try-with-resources块结束时，ServletOutputStream和FileInputStream都会自动关闭。

### 关于文件流 InputStream的read方法
- read()：首先我们来看这个没有参数的read方法,从(来源)输入流中(读取的内容)读取数据的下一个字节到(去处)java程序内部中,返回值为0到255的int类型的值，返回值为字符的ASCII值(如a就返回97,n就返回110).如果没有可用的字节,因为已经到达流的末尾, -1返回的值,运行一次只读一个字节,所以经常与while((len = inputstream.read()) != -1)一起使用. 
- read(byte [] b )：从(来源)输入流中(读取内容)读取的一定数量字节数,并将它们存储到(去处)缓冲区数组b中,返回值为实际读取的字节数,运行一次读取一定的数量的字节数.java会尽可能的读取b个字节,但也有可能读取少于b的字节数.至少读取一个字节第一个字节存储读入元素b[0],下一个b[1],等等。读取的字节数是最多等于b的长度.如果没有可用的字节,因为已经到达流的末尾, -1返回的值 ,如果b.length==0,则返回0.
- read( byte [] b , int off , int len)：读取 len字节的数据从输入流到一个字节数组。试图读取多达 len字节,但可能读取到少于len字节。返回实际读取的字节数为整数。 第一个字节存储读入元素b[off],下一个b[off+1],等等。读取的字节数是最多等于len。k被读取的字节数,这些字节将存储在元素通过b[off+k-1]b[off],离开元素通过b[off+len-1]b[off+k]未受影响。read(byte[]b)就是相当于read(byte [] b , 0 , b.length).所以两者差不多.性质一样.

### SpringData
Spring Data : Spring 的一个子项目。用于简化数据库访问，支持NoSQL 和 关系数据存储。其主要目标是使数据库的访问变得方便快捷。
- SpringData 项目所支持 NoSQL 存储：
  - MongoDB （文档数据库）
  - Neo4j（图形数据库）
  - Redis（键/值存储）
  - Hbase（列族数据库）
- SpringData 项目所支持的关系数据存储技术：
  - JDBC
  - JPA

Spring Data中用到的分页：
```
Ipage分页
pagehelp分页
springData分页
Pageablepageable=PageRequest.of(pageNUmber,pageSize);
```

### Java相关问题
```
问：八种基本数据类型的大小，以及他们的封装类
答：
八种基本数据类型，int ,double ,long ,float, short,byte,character,boolean
对应的封装类型是：Integer ,Double ,Long ,Float, Short,Byte,Character,Boolean

问：Switch能否用string做参数？
答：在Java 5以前，switch(expr)中，expr只能是byte、short、char、int。从Java 5开始，Java中引入了枚举类型，expr也可以是enum类型，从Java 7开始，expr还可以是字符串（String），但是长整型（long）在目前所有的版本中都是不可以的。

switch匹配多参数：
case TIMING:
case COACH_LOGOUT:
case PHOTO_LSSUE:
    mainModel.initUploadPhoto(path, pictureType, null, faceMatching, null);
    break;

问：String、StringBuffer与StringBuilder的区别
答：其中String是只读字符串，也就意味着String引用的字符串内容是不能被改变的。
而StringBuffer和StringBulder类表示的字符串对象可以直接进行修改。
StringBuilder是JDK1.5引入的，它和StringBuffer的方法完全相同，
区别在于它是单线程环境下使用的，因为它的所有方面都没有被synchronized修饰，因此它的效率也比StringBuffer略高。

问：try catch finally，try里有return，finally还执行么？
答：会执行，在方法 返回调用者前执行。Java允许在finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是纪录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，这会对程序造成很大的困扰，C#中就从语法规定不能做这样的事。

问：Hashcode的作用。
答：
以Java.lang.Object来理解,JVM每new一个Object,它都会将这个Object丢到一个Hash哈希表中去,这样的话,下次做Object的比较或者取这个对象的时候,它会根据对象的hashcode再从Hash表中取这个对象。这样做的目的是提高取对象的效率。具体过程是这样: 
1. new Object(),JVM根据这个对象的Hashcode值,放入到对应的Hash表对应的Key上,如果不同的对象确产生了相同的hash值,也就是发生了Hash key相同导致冲突的情况,那么就在这个Hash key的地方产生一个链表,将所有产生相同hashcode的对象放到这个单链表上去,串在一起。 
2. 比较两个对象的时候,首先根据他们的hashcode去hash表中找他的对象,当两个对象的hashcode相同,那么就是说他们这两个对象放在Hash表中的同一个key上,那么他们一定在这个key上的链表上。那么此时就只能根据Object的equal方法来比较这个对象是否equal。当两个对象的hashcode不同的话，肯定他们不能equal. 

问：Excption与Error区别
答：Error表示系统级的错误和程序不必处理的异常，是恢复不是不可能但很困难的情况下的一种严重问题；比如内存溢出，不可能指望程序能处理这样的状况；Exception表示需要捕捉或者需要程序进行处理的异常，是一种设计或实现问题；也就是说，它表示如果程序运行正常，从不会发生的情况。

问：slf4j、Log4j和logback区别
slf4j是打日志的，可以使用各种日志系统存储，
Log4j和logback就是那个日志存储系统(它自带打日志，因为自己本身就是一个日志系统。所以不能够切换日志系统)。
但是slf4j 是可以随时切换到任何日志系统

问：关于金额字段为什么不用decimal类型
主要两个原因：
1. 避免不同语言对浮点数麻烦的计算，一不小心非常容易算错（存整数进行计算的话，计算完再除以精度比较方便）
2. 对于多个国家，对小数的精度要求是不同的，用 decimal 没办法方便控制

```





