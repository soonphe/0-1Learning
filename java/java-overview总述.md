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

### JDK相关：
Mac下查看已安装的jdk版本及其安装目录
```
查看JDK信息：/usr/libexec/java_home -V
JDK bin目录：cd /Library/Java/JavaVirtualMachines/jdk1.8.0_241.jdk/Contents/Home/bin/
移除Oracle JDK：sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
```

### JDK相关工具使用

几种常用的内存调试工具：jmap、jstack、jconsole

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
或者 jstack -l > /tmp/output.txt 把堆栈信息打到一个txt文件。
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





