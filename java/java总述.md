# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## java总述

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
移除Oracle JDK：sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
查看mysql数据库所有信息：show variables 
查看mysql字符串相关信息：show variables  like "%char%";
```

### JDK相关工具使用
```
查看JDK信息：/usr/libexec/java_home -V
JDK bin目录：cd /Library/Java/JavaVirtualMachines/jdk1.8.0_241.jdk/Contents/Home/bin/

jconsole：Jconsole （Java Monitoring and Management Console），一种基于JMX的可视化监视、管理工具。
JConsole 基本包括以下基本功能：概述、内存、线程、类、VM概要、MBean
1.3.1 内存监控
内存页签相对于可视化的jstat 命令，用于监视受收集器管理的虚拟机内存。
1.3.2 线程监控
如果上面的“内存”页签相当于可视化的jstat命令的话，“线程”页签的功能相当于可视化的jstack命令，遇到线程停顿时可以使用这个页签进行监控分析。线程长时间停顿的主要原因主要有：等待外部资源（数据库连接、网络资源、设备资
源等）、死循环、锁等待（活锁和死锁）

jvisualvm：VisualVM（All-in-One Java Troubleshooting Tool）;功能最强大的运行监视和故障处理程序
- 显示虚拟机进程以及进程的配置、环境信息（jps、jinfo）。
- 监视应用程序的CPU、GC、堆、方法区(1.7及以前)，元空间（JDK1.8及以后）以及线程的信息（jstat、jstack）。
- dump以及分析堆转储快照（jmap、jhat）。
- 方法级的程序运行性能分析，找出被调用最多、运行时间最长的方法。
- 离线程序快照：收集程序的运行时配置、线程dump、内存dump等信息建立一个快照

平时启动jvisualvm
1./usr/libexec/java_home -V
2.cd /Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/bin
3.jvisualvm

```

