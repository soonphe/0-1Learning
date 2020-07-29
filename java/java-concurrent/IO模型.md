# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## IO模型
* IO即是in/out，相对于程序而言的输入(读取)和输出(写出)的过程。
* 在Java中，根据处理的数据单位不同，分为字节流和字符流
* in/out一定要区分方向 如下:
    * java代表java程序,  disk代表磁盘/硬盘
    * java <---in--- disk
    * java ---out--> disk


### IO模型
* 5种IO模型：
    * 阻塞I/O（blocking IO）
    * 非阻塞I/O（noblocking IO）
    * I/O复用    (IO multiplexing )
    * 信号驱动I/O (signal driven IO)
    * 异步I/O (asynchronous IO)
    
### IO分两阶段：
    1. 数据准备阶段
    2. 内核空间复制回用户进程缓冲区阶段
* 一般来讲：阻塞IO模型、非阻塞IO模型、IO复用模型(select/poll/epoll)、信号驱动IO模型都属于同步IO，因为阶段2是阻塞的(尽管时间很短)。
* 只有异步IO模型是符合异步IO操作含义的，不管在阶段1还是阶段2都可以干别的事。

#### IO框架主要用到什么设计模式
* JDK的I/O包中就主要使用到了两种设计模式：Adatper模式和Decorator模式。

#### 5种IO模型解析：
1. blocking IO（BIO）阻塞IO：
    * blockingIO的特点就是在IO执行的两个阶段（等待数据和拷贝数据两个阶段）都被block了。
    * 使用recv的默认参数一直等数据直到拷贝到用户空间，这段时间内进程始终阻塞。打个比方，A同学排队买票，他只能排队买上票才可以离开。这一过程就可以看成使用了阻塞       IO模型，因为如果在没买到票之前，他不能离开队伍做别的事情（离开等于白排队，回来又要重新排队）。很显然这种，I/O模型是同步的。

2. nonblocking IO（NIO）非阻塞IO：
    * 在非阻塞式IO中，用户进程其实是需要不断的主动询问kernel数据准备好了没有。
    * 在Java 1.4 中引入了NIO框架，对应 java.nio 包，提供了 Channel , Selector，Buffer等抽象
    * NIO中的N可以理解为Non-blocking，不单纯是New。它支持面向缓冲的，基于通道的I/O操作方法
    * 改变flags,让recv不管有没有获取到数据都返回，如果没有数据那么一段时间后再调用recv看看，如此循环。对比阻塞模型，相当于A同学买票过程中，采用了取号买票，再没有到他前，他可以不断的返回购票大厅看下是不是到了自己的号，中间的过程可以做其他事情。他就不用向之前一样一刻不能离开购票大厅。这就是非阻塞IO模型。但是它只有是检查无数据的时候是非阻塞的，在数据到达的时候依然要等待复制数据到用户空间(到自己的号买上票)，因此它还是同步IO。

3. IO multiplexing      IO多路复用：
    * select/epoll的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接。
    * 这里在调用recv前先调用select或者poll，这2个系统调用都可以在内核准备好数据(网络数据到达内核)时告知用户进程，这个时候再调用recv一定是有数据的。因此这一过程中它     是阻塞于select或poll，而没有阻塞于recv，有人将非阻塞IO定义成在读写操作时没有阻塞于系统调用的IO操作(不包括数据从内核复制到用户空间时的阻塞，因为这相对于网络IO       来说确实很短暂)，如果按这样理解，这种IO模型也能称之为非阻塞IO模型，但它也是同步IO，那么也和楼上一样称之为同步非阻塞IO吧。
    * 这种IO模型比较特别，分个段。因为它能同时监听多个文件描述符(fd)。举例A同学来北京到南京的车票，发现有一排售票窗口，售票服务人员告诉他这些窗口目前没有票，等有票告诉他。于是等啊等(select调用中)，过了一会售票服务人员告诉他有票了，但不知道是哪个窗口卖北京到南京的车票，自己看吧。于是A同学一个个窗口问，直到找到卖北京到南京车票的窗口买上票(recv)。这里再顺便说说鼎鼎大名的epoll(高性能的代名词啊)，epoll也属于IO复用模型，主要区别在于售票服务人员告诉他A同学哪几个窗口卖北京到南京的车票，不需要一个个去问了。
4. signal driven IO     信号驱动IO（在实际中并不常用）：
    * 通过调用sigaction注册信号函数，等内核数据准备好的时候系统中断当前程序，执行信号函数(在这里面调用recv)。A同学让舍售票服务人员等有票的时候通知他(注册信号函数)，    没多久A同学得知有票了，跑去买票。是不是很像异步IO？很遗憾，它还是同步IO(省不了买票的时间啊)。
5. asynchronous IO    异步IO：
    * AIO 也就是 NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2,它是异步非阻塞的IO模型
    * 调用aio_read，让内核等数据准备好，并且复制到用户进程空间后执行事先指定好的函数。A同学让售票服务人员帮他买好票后通知他。整个过程A同学都可以做别的事情(没有           recv)，这才是真正的异步IO。



#### select/epoll
* select/epoll，大概就都能明白了。有些地方也称这种IO方式为事件驱动IO(event driven IO)。
* 我们都知道，select/epoll的好处就在于单个process就可以同时处理多个网络连接的IO。它的基本原理就是select/epoll这个function会不断的轮询所负责的所有socket，当某个socket有数据到达了，就通知用户进程。
* 在多路复用模型中，对于每一个socket，一般都设置成为non-blocking，但是，如上图所示，整个用户的process其实是一直被block的。只不过process是被select这个函数block，而不是被socket IO给block。
* 结论:select的优势在于可以处理多个连接，不适用于单个连接

#### select、poll、epoll之间的区别
1. select==>时间复杂度O(n)
它仅仅知道了，有I/O事件发生了，却并不知道是哪那几个流（可能有一个，多个，甚至全部），我们只能无差别轮询所有流，找出能读出数据，或者写入数据的流，对他们进行操作。所以select具有O(n)的无差别轮询复杂度，同时处理的流越多，无差别轮询时间就越长。
2. poll==>时间复杂度O(n)
3. epoll==>时间复杂度O(1)
Linux下多路复用IO接口select/poll的增强版本
epoll可以理解为event poll，不同于忙轮询和无差别轮询，epoll会把哪个流发生了怎样的I/O事件通知我们。所以我们说epoll实际上是事件驱动（每个事件关联上fd）的，此时我们对这些流的操作都是有意义的。（复杂度降低到了O(1)）


#### NIO包有哪些结构？分别起到的作用？**


#### NIO针对什么情景会比IO有更好的优化？**


