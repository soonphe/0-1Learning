# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## ASM字节码增强技术

asm是字节码增强技术，通过asm可以生成新的class文件，也可以动态的修改即将要装载入jvm的类信息。

### 一、什么是ASM

ASM是一个java字节码操纵框架，它能被用来动态生成类或者增强既有类的功能。ASM 可以直接产生二进制 class 文件，也可以在类被加载入 Java 虚拟机之前动态改变类行为。Java class 被存储在严格格式定义的 .class文件里，这些类文件拥有足够的元数据来解析类中的所有元素：类名称、方法、属性以及 Java 字节码（指令）。ASM从类文件中读入信息后，能够改变类行为，分析类信息，甚至能够根据用户要求生成新类。asm字节码增强技术主要是用来反射的时候提升性能的，如果单纯用jdk的反射调用，性能是非常低下的，而使用字节码增强技术后反射调用的时间已经基本可以与直接调用相当了

使用ASM框架需要导入asm的jar包，下载链接：asm-3.2.jar。

### 二、如何使用ASM

ASM框架中的核心类有以下几个：

①  ClassReader:该类用来解析编译过的class字节码文件。

②  ClassWriter:该类用来重新构建编译后的类，比如说修改类名、属性以及方法，甚至可以生成新的类的字节码文件。

③  ClassAdapter:该类也实现了ClassVisitor接口，它将对它的方法调用委托给另一个ClassVisitor对象。

### 三、 ASM字节码处理框架是用Java开发的而且使用基于访问者模式生成字节码及驱动类到字节码的转换，通俗的讲，它就是对class文件的CRUD，经过CRUD后的字节码可以转换为类。ASM的解析方式类似于SAX解析XML文件，它综合运用了访问者模式、职责链模式、桥接模式等多种设计模式，相对于其他类似工具如BCEL、SERP、Javassist、CGLIB，它的最大的优势就在于其性能更高，其jar包仅30K。Hibernate和Spring都使用了cglib代理，而cglib本身就是使用的ASM，可见ASM在各种开源框架都有广泛的应用。
ASM是一个强大的框架，利用它我们可以做到：
1. 获得class文件的详细信息，包括类名、父类名、接口、成员名、方法名、方法参数名、局部变量名、元数据等
2. 对class文件进行动态修改，如增加、删除、修改类方法、在某个方法中添加指令等

CGLIB（动态代理）是对ASM的封装，简化了ASM的操作，降低了ASM的使用门槛，

其中，hibernate的懒加载使用到了asm，spring的AOP也使用到了。你建立一个hibernate映射对象并使用懒加载配置的时候，在内存中生成的对象使用的不再是你实现的那个类了，而是hibernate根据字节码技术已你的类为模板构造的一个新类，证明就是当你获得那个对象输出类名是，不是你自己生成的类名了。spring可能是proxy$xxx，hibernate可能是<你的类名>$xxx$xxx之类的名字。


### 为什么要动态生成 Java 类？
动态生成 Java 类与 AOP 密切相关的。AOP 的初衷在于软件设计世界中存在这么一类代码，零散而又耦合：零散是由于一些公有的功能（诸如著名的 log 例子）分散在所有模块之中；同时改变 log 功能又会影响到所有的模块。出现这样的缺陷，很大程度上是由于传统的 面向对象编程注重以继承关系为代表的“纵向”关系，而对于拥有相同功能或者说方面 （Aspect）的模块之间的“横向”关系不能很好地表达。

代表不同的事务，派生自不同的父类，很难在高层上加入关于 Security Checker 的共有功能.对于没有多继承的 Java 来说，更是如此。传统的解决方案是使用 Decorator 模式，它可以在一定程度上改善耦合，而功能仍旧是分散的 —— 每个需要 Security Checker 的类都必须要派生一个 Decorator，每个需要 Security Checker 的方法都要被包装（wrap）。

在这个简单的例子里，改造一个类的一个方法还好，如果是变动整个模块，Decorator 很快就会演化成另一个噩梦。动态改变 Java 类就是要解决 AOP 的问题，提供一种得到系统支持的可编程的方法，自动化地生成或者增强 Java 代码。这种技术已经广泛应用于最新的 Java 框架内，如 Hibernate，Spring 等。


### 为什么选择 ASM ？
最直接的改造 Java 类的方法莫过于直接改写 class 文件。Java 规范详细说明了 class 文件的格式，直接编辑字节码确实可以改变 Java 类的行为。直到今天，还有一些 Java 高手们使用最原始的工具，如 UltraEdit 这样的编辑器对 class 文件动手术。是的，这是最直接的方法，但是要求使用者对 Java class 文件的格式了熟于心：小心地推算出想改造的函数相对文件首部的偏移量，同时重新计算 class 文件的校验码以通过 Java 虚拟机的安全机制。

Java 5 中提供的 Instrument 包也可以提供类似的功能：启动时往 Java 虚拟机中挂上一个用户定义的 hook 程序，可以在装入特定类的时候改变特定类的字节码，从而改变该类的行为。但是其缺点也是明显的：

- Instrument 包是在整个虚拟机上挂了一个钩子程序，每次装入一个新类的时候，都必须执行一遍这段程序，即使这个类不需要改变。
- 直接改变字节码事实上类似于直接改写 class 文件，无论是调用 ClassFileTransformer. transform(ClassLoader loader, String className, Class classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer)，还是Instrument.redefineClasses(ClassDefinition[] definitions)，都必须提供新 Java 类的字节码。也就是说，同直接改写 class 文件一样，使用 Instrument 也必须了解想改造的方法相对类首部的偏移量，才能在适当的位置上插入新的代码。

尽管 Instrument 可以改造类，但事实上，Instrument 更适用于监控和控制虚拟机的行为。

一种比较理想且流行的方法是使用 java.lang.ref.proxy。

Proxy 编程是面向接口的。下面我们会看到，Proxy 并不负责实例化对象，和 Decorator 模式一样，要把 Account定义成一个接口，然后在AccountImpl里实现Account接口，接着实现一个 InvocationHandlerAccount方法被调用的时候，虚拟机都会实际调用这个InvocationHandler的invoke方法：
```
class SecurityProxyInvocationHandler implements InvocationHandler { 
	 private Object proxyedObject; 
	 public SecurityProxyInvocationHandler(Object o) { 
		 proxyedObject = o; 
	 } 
		
	 public Object invoke(Object object, Method method, Object[] arguments) 
		 throws Throwable { 			
		 if (object instanceof Account && method.getName().equals("opertaion")) { 
			 SecurityChecker.checkSecurity(); 
		 } 
		 return method.invoke(proxyedObject, arguments); 
	 } 
 }
最后，在应用程序中指定 InvocationHandler生成代理对象：

 public static void main(String[] args) { 
	 Account account = (Account) Proxy.newProxyInstance( 
		 Account.class.getClassLoader(), 
		 new Class[] { Account.class }, 
		 new SecurityProxyInvocationHandler(new AccountImpl()) 
	 ); 
	 account.function(); 
 }
```

其不足之处在于：

- Proxy 是面向接口的，所有使用 Proxy 的对象都必须定义一个接口，而且用这些对象的代码也必须是对接口编程的：Proxy 生成的对象是接口一致的而不是对象一致的：例子中Proxy.newProxyInstance生成的是实现Account接口的对象而不是 AccountImpl的子类。这对于软件架构设计，尤其对于既有软件系统是有一定掣肘的。
- Proxy 毕竟是通过反射实现的，必须在效率上付出代价：有实验数据表明，调用反射比一般的函数开销至少要大 10 倍。而且，从程序实现上可以看出，对 proxy class 的所有方法调用都要通过使用反射的 invoke 方法。因此，对于性能关键的应用，使用 proxy class 是需要精心考虑的，以避免反射成为整个应用的瓶颈。

ASM 能够通过改造既有类，直接生成需要的代码。增强的代码是硬编码在新生成的类文件内部的，没有反射带来性能上的付出。同时，ASM 与 Proxy 编程不同，不需要为增强代码而新定义一个接口，生成的代码可以覆盖原来的类，或者是原始类的子类。它是一个普通的 Java 类而不是 proxy 类，甚至可以在应用程序的类框架中拥有自己的位置，派生自己的子类。

相比于其他流行的 Java 字节码操纵工具，ASM 更小更快。ASM 具有类似于 BCEL 或者 SERP 的功能，而只有 33k 大小，而后者分别有 350k 和 150k。同时，同样类转换的负载，如果 ASM 是 60% 的话，BCEL 需要 700%，而 SERP 需要 1100% 或者更多。

ASM 已经被广泛应用于一系列 Java 项目：AspectWerkz、AspectJ、BEA WebLogic、IBM AUS、OracleBerkleyDB、Oracle TopLink、Terracotta、RIFE、EclipseME、Proactive、Speedo、Fractal、EasyBeans、BeanShell、Groovy、Jamaica、CGLIB、dynaop、Cobertura、JDBCPersistence、JiP、SonarJ、Substance L&F、Retrotranslator 等。Hibernate 和 Spring 也通过 cglib，另一个更高层一些的自动代码生成工具使用了 ASM。

### Java 类文件概述 

所谓 Java 类文件，就是通常用 javac 编译器产生的 .class 文件。这些文件具有严格定义的格式。为了更好的理解 ASM，首先对 Java 类文件格式作一点简单的介绍。Java 源文件经过 javac 编译器编译之后，将会生成对应的二进制文件。每个合法的 Java 类文件都具备精确的定义，而正是这种精确的定义，才使得 Java 虚拟机得以正确读取和解释所有的 Java 类文件。

Java 类文件是 8 位字节的二进制流。数据项按顺序存储在 class 文件中，相邻的项之间没有间隔，这使得 class 文件变得紧凑，减少存储空间。在 Java 类文件中包含了许多大小不同的项，由于每一项的结构都有严格规定，这使得 class 文件能够从头到尾被顺利地解析。下面让我们来看一下 Java 类文件的内部结构，以便对此有个大致的认识。
java文件：
```
 public class HelloWorld { 
	 public static void main(String[] args) { 
		 System.out.println("Hello world"); 
	 } 
 }
```
编译成class：javac HelloWorld.java
查看class文件：javap -verbose HelloWorld.java
```
Classfile /Users/luoxiaosheng/Downloads/test/HelloWorld.class
  Last modified 2022-1-18; size 425 bytes
  MD5 checksum 032c2265e463c04f483fa44d16f07bcd
  Compiled from "HelloWorld.java"
public class HelloWorld
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#15         // java/lang/Object."<init>":()V
   #2 = Fieldref           #16.#17        // java/lang/System.out:Ljava/io/PrintStream;
   #3 = String             #18            // Hello world
   #4 = Methodref          #19.#20        // java/io/PrintStream.println:(Ljava/lang/String;)V
   #5 = Class              #21            // HelloWorld
   #6 = Class              #22            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               main
  #12 = Utf8               ([Ljava/lang/String;)V
  #13 = Utf8               SourceFile
  #14 = Utf8               HelloWorld.java
  #15 = NameAndType        #7:#8          // "<init>":()V
  #16 = Class              #23            // java/lang/System
  #17 = NameAndType        #24:#25        // out:Ljava/io/PrintStream;
  #18 = Utf8               Hello world
  #19 = Class              #26            // java/io/PrintStream
  #20 = NameAndType        #27:#28        // println:(Ljava/lang/String;)V
  #21 = Utf8               HelloWorld
  #22 = Utf8               java/lang/Object
  #23 = Utf8               java/lang/System
  #24 = Utf8               out
  #25 = Utf8               Ljava/io/PrintStream;
  #26 = Utf8               java/io/PrintStream
  #27 = Utf8               println
  #28 = Utf8               (Ljava/lang/String;)V
{
  public HelloWorld();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 1: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #3                  // String Hello world
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 3: 0
        line 4: 8
}
SourceFile: "HelloWorld.java"
```

从上图中可以看到，一个 Java 类文件大致可以归为 10 个项：

- Magic：该项存放了一个 Java 类文件的魔数（magic number）和版本信息。一个 Java 类文件的前 4 个字节被称为它的魔数。每个正确的 Java 类文件都是以 0xCAFEBABE 开头的，这样保证了 Java 虚拟机能很轻松的分辨出 Java 文件和非 Java 文件。
- Version：该项存放了 Java 类文件的版本信息，它对于一个 Java 文件具有重要的意义。因为 Java 技术一直在发展，所以类文件的格式也处在不断变化之中。类文件的版本信息让虚拟机知道如何去读取并处理该类文件。
- Constant Pool：该项存放了类中各种文字字符串、类名、方法名和接口名称、final 变量以及对外部类的引用信息等常量。虚拟机必须为每一个被装载的类维护一个常量池，常量池中存储了相应类型所用到的所有类型、字段和方法的符号引用，因此它在 Java 的动态链接中起到了核心的作用。常量池的大小平均占到了整个类大小的 60% 左右。
- Access_flag：该项指明了该文件中定义的是类还是接口（一个 class 文件中只能有一个类或接口），同时还指名了类或接口的访问标志，如 public，private, abstract 等信息。
- This Class：指向表示该类全限定名称的字符串常量的指针。
- Super Class：指向表示父类全限定名称的字符串常量的指针。
- Interfaces：一个指针数组，存放了该类或父类实现的所有接口名称的字符串常量的指针。以上三项所指向的常量，特别是前两项，在我们用 ASM 从已有类派生新类时一般需要修改：将类名称改为子类名称；将父类改为派生前的类名称；如果有必要，增加新的实现接口。
- Fields：该项对类或接口中声明的字段进行了细致的描述。需要注意的是，fields 列表中仅列出了本类或接口中的字段，并不包括从超类和父接口继承而来的字段。
- Methods：该项对类或接口中声明的方法进行了细致的描述。例如方法的名称、参数和返回值类型等。需要注意的是，methods 列表里仅存放了本类或本接口中的方法，并不包括从超类和父接口继承而来的方法。使用 ASM 进行 AOP 编程，通常是通过调整 Method 中的指令来实现的。
- Class attributes：该项存放了在该文件中类或接口所定义的属性的基本信息。

事实上，使用 ASM 动态生成类，不需要像早年的 class hacker 一样，熟知 class 文件的每一段，以及它们的功能、长度、偏移量以及编码方式。ASM 会给我们照顾好这一切的，我们只要告诉 ASM 要改动什么就可以了 —— 当然，我们首先得知道要改什么：对类文件格式了解的越多，我们就能更好地使用 ASM 这个利器。


### ASM 3.0 编程框架
ASM 通过树这种数据结构来表示复杂的字节码结构，并利用 Push 模型来对树进行遍历，在遍历过程中对字节码进行修改。所谓的 Push 模型类似于简单的 Visitor 设计模式，因为需要处理字节码结构是固定的，所以不需要专门抽象出一种 Vistable 接口，而只需要提供 Visitor 接口。所谓 Visitor 模式和 Iterator 模式有点类似，它们都被用来遍历一些复杂的数据结构。Visitor 相当于用户派出的代表，深入到算法内部，由算法安排访问行程。Visitor 代表可以更换，但对算法流程无法干涉，因此是被动的，这也是它和 Iterator 模式由用户主动调遣算法方式的最大的区别。

在 ASM 中，提供了一个 ClassReader类，这个类可以直接由字节数组或由 class 文件间接的获得字节码数据，它能正确的分析字节码，构建出抽象的树在内存中表示字节码。它会调用accept方法，这个方法接受一个实现了ClassVisitor接口的对象实例作为参数，然后依次调用 ClassVisitor接口的各个方法。字节码空间上的偏移被转换成 visit 事件时间上调用的先后，所谓 visit 事件是指对各种不同 visit 函数的调用，ClassReader知道如何调用各种 visit 函数。在这个过程中用户无法对操作进行干涉，所以遍历的算法是确定的，用户可以做的是提供不同的 Visitor 来对字节码树进行不同的修改。ClassVisitor会产生一些子过程，比如visitMethod会返回一个实现MethordVisitor接口的实例，visitField会返回一个实现FieldVisitor接口的实例，完成子过程后控制返回到父过程，继续访问下一节点。因此对于ClassReader来说，其内部顺序访问是有一定要求的。实际上用户还可以不通过ClassReader类，自行手工控制这个流程，只要按照一定的顺序，各个 visit 事件被先后正确的调用，最后就能生成可以被正确加载的字节码。当然获得更大灵活性的同时也加大了调整字节码的复杂度。

各个 ClassVisitor通过职责链 （Chain-of-responsibility） 模式，可以非常简单的封装对字节码的各种修改，而无须关注字节码的字节偏移，因为这些实现细节对于用户都被隐藏了，用户要做的只是覆写相应的 visit 函数。

ClassAdaptor类实现了 ClassVisitor接口所定义的所有函数，当新建一个 ClassAdaptor对象的时候，需要传入一个实现了 ClassVisitor接口的对象，作为职责链中的下一个访问者 （Visitor），这些函数的默认实现就是简单的把调用委派给这个对象，然后依次传递下去形成职责链。当用户需要对字节码进行调整时，只需从ClassAdaptor类派生出一个子类，覆写需要修改的方法，完成相应功能后再把调用传递下去。这样，用户无需考虑字节偏移，就可以很方便的控制字节码。

每个 ClassAdaptor类的派生类可以仅封装单一功能，比如删除某函数、修改字段可见性等等，然后再加入到职责链中，这样耦合更小，重用的概率也更大，但代价是产生很多小对象，而且职责链的层次太长的话也会加大系统调用的开销，用户需要在低耦合和高效率之间作出权衡。用户可以通过控制职责链中 visit 事件的过程，对类文件进行如下操作：

删除类的字段、方法、指令：只需在职责链传递过程中中断委派，不访问相应的 visit 方法即可，比如删除方法时只需直接返回 null，而不是返回由visitMethod方法返回的MethodVisitor对象。

```
 class DelLoginClassAdapter extends ClassAdapter { 
	 public DelLoginClassAdapter(ClassVisitor cv) { 
		 super(cv); 
	 } 

	 public MethodVisitor visitMethod(final int access, final String name, 
		 final String desc, final String signature, final String[] exceptions) { 
		 if (name.equals("login")) { 
			 return null; 
		 } 
		 return cv.visitMethod(access, name, desc, signature, exceptions); 
	 } 
 }
```
修改类、字段、方法的名字或修饰符：在职责链传递过程中替换调用参数。
```
 class AccessClassAdapter extends ClassAdapter { 
	 public AccessClassAdapter(ClassVisitor cv) { 
		 super(cv); 
	 } 

	 public FieldVisitor visitField(final int access, final String name, 
        final String desc, final String signature, final Object value) { 
        int privateAccess = Opcodes.ACC_PRIVATE; 
        return cv.visitField(privateAccess, name, desc, signature, value); 
    } 
 }
```
增加新的类、方法、字段

ASM 的最终的目的是生成可以被正常装载的 class 文件，因此其框架结构为客户提供了一个生成字节码的工具类 —— ClassWriter。它实现了ClassVisitor接口，而且含有一个toByteArray()函数，返回生成的字节码的字节流，将字节流写回文件即可生产调整后的 class 文件。一般它都作为职责链的终点，把所有 visit 事件的先后调用（时间上的先后），最终转换成字节码的位置的调整（空间上的前后），如下例：
```
ClassWriter  classWriter = new ClassWriter(ClassWriter.COMPUTE_MAXS);
ClassAdaptor delLoginClassAdaptor = new DelLoginClassAdapter(classWriter);
ClassAdaptor accessClassAdaptor = new AccessClassAdaptor(delLoginClassAdaptor);

ClassReader classReader = new ClassReader(strFileName);
classReader.accept(classAdapter, ClassReader.SKIP_DEBUG);
```

### 基于javaAgent和Java字节码注入技术的java探针工具技术原理

jdk1.5以后引入了javaAgent技术，javaAgent是运行方法之前的拦截器。我们利用javaAgent和ASM字节码技术， 在JVM加载class二进制文件的时候，利用ASM动态的修改加载的class文件，在监控的方法前后添加计时器功能，用于计算监控方法耗时，同时将方法耗时及内部调用情况放入处理器，处理器利用栈先进后出的特点对方法调用先后顺序做处理，当一个请求处理结束后，将耗时方法轨迹和入参map输出到文件中，然后根据map中相应参数或耗时方法轨迹中的关键代码区分出我们要抓取的耗时业务。最后将相应耗时轨迹文件取下来，转化为xml格式并进行解析，通过浏览器将代码分层结构展示出来，方便耗时分析。

Java探针工具功能点:

1、支持方法执行耗时范围抓取设置，根据耗时范围抓取系统运行时出现在设置耗时范围的代码运行轨迹。

2、支持抓取特定的代码配置，方便对配置的特定方法进行抓取，过滤出关系的代码执行耗时情况。

3、支持APP层入口方法过滤，配置入口运行前的方法进行监控，相当于监控特有的方法耗时，进行方法专题分析。

4、支持入口方法参数输出功能，方便跟踪耗时高的时候对应的入参数。

5、提供WEB页面展示接口耗时展示、代码调用关系图展示、方法耗时百分比展示、可疑方法凸显功能。




