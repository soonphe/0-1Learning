# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Jvm相关问题
~~~~
问：堆和栈有什么区别
答：堆是存放对象的，但是对象内的临时变量是存在栈内存中，如例子中的methodVar是在运行期存放到栈中的。栈是跟随线程的，有线程就有栈，堆是跟随JVM的，有JVM就有堆内存。

问：堆内存中到底存在着什么东西？
​答：对象，包括对象变量以及对象方法。

问：类变量和实例变量有什么区别？
答：静态变量是类变量，非静态变量是实例变量，直白的说，有static修饰的变量是静态变量，没有static修饰的变量是实例变量。静态变量存在方法区中，实例变量存在堆内存中。

问：Java的方法（函数）到底是传值还是传址？
答：都不是，是以传值的方式传递地址，具体的说原生数据类型传递的值，引用类型传递的地址。对于原始数据类型，JVM的处理方法是从Method Area或Heap中拷贝到Stack，然后运行frame中的方法，运行完毕后再把变量指拷贝回去。

问：为什么会产生OutOfMemory产生？
答：一句话：Heap内存中没有足够的可用内存了。这句话要好好理解，不是说Heap没有内存了，是说新申请内存的对象大于Heap空闲内存，比如现在Heap还空闲1M，但是新申请的内存需要1.1M，于是就会报OutOfMemory了，可能以后的对象申请的内存都只要0.9M，于是就只出现一次OutOfMemory，GC也正常了，看起来像偶发事件，就是这么回事。       但如果此时GC没有回收就会产生挂起情况，系统不响应了。

问：OutOfMemory错误分几种？
答：分两种，分别是“OutOfMemoryError:java heap size”和”OutOfMemoryError: PermGen space”，两种都是内存溢出，heap size是说申请不到新的内存了，这个很常见，检查应用或调整堆内存大小。
“PermGen space”是因为永久存储区满了，这个也很常见，一般在热发布的环境中出现，是因为每次发布应用系统都不重启，久而久之永久存储区中的死对象太多导致新对象无法申请内存，一般重新启动一下即可。

问：为什么会产生StackOverflowError？
答：因为一个线程把Stack内存全部耗尽了，一般是递归函数造成的。

问：一个机器上可以看多个JVM吗？JVM之间可以互访吗？
答：可以多个JVM，只要机器承受得了。JVM之间是不可以互访，你不能在A-JVM中访问B-JVM的Heap内存，这是不可能的。在以前老版本的JVM中，会出现A-JVM Crack后影响到B-JVM，现在版本非常少见。

问：为什么Java要采用垃圾回收机制，而不采用C/C++的显式内存管理？
答：为了简单，内存管理不是每个程序员都能折腾好的。

问：为什么你没有详细介绍垃圾回收机制？
答：垃圾回收机制每个JVM都不同，JVM Specification只是定义了要自动释放内存，也就是说它只定义了垃圾回收的抽象方法，具体怎么实现各个厂商都不同，算法各异，这东西实在没必要深入。

问：JVM中到底哪些区域是共享的？哪些是私有的？
答：Heap和Method Area是共享的，其他都是私有的，
 
问：什么是JIT，你怎么没说？
答：JIT是指Just In Time，有的文档把JIT作为JVM的一个部件来介绍，有的是作为执行引擎的一部分来介绍，这都能理解。Java刚诞生的时候是一个解释性语言，别嘘，即使编译成了字节码（byte code）也是针对JVM的，它需要再次翻译成原生代码(native code)才能被机器执行，于是效率的担忧就提出来了。Sun为了解决该问题提出了一套新的机制，好，你想编译成原生代码，没问题，我在JVM上提供一个工具，把字节码编译成原生码，下次你来访问的时候直接访问原生码就成了，于是JIT就诞生了，就这么回事。

问：JVM还有哪些部分是你没有提到的？
答：JVM是一个异常复杂的东西，写一本砖头书都不为过，还有几个要说明的：
    常量池（constant pool）：按照顺序存放程序中的常量，并且进行索引编号的区域。比如int i =100，这个100就放在常量池中。
    安全管理器（Security Manager）：提供Java运行期的安全控制，防止恶意攻击，比如指定读取文件，写入文件权限，网络访问，创建进程等等，Class Loader在Security Manager认证通过后才能加载class文件的。
    方法索引表（Methods table），记录的是每个method的地址信息，Stack和Heap中的地址指针其实是指向Methods table地址。
 
问：为什么不建议在程序中显式的声明System.gc()？
答：因为显式声明是做堆内存全扫描，也就是Full GC，是需要停止所有的活动的（Stop  The World Collection），你的应用能承受这个吗？

问：JVM有哪些调整参数？
答：非常多，自己去找，堆内存、栈内存的大小都可以定义，甚至是堆内存的三个部分、新生代的各个比例都能调整。
~~~~