# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Netty
netty 是一个基于nio的客户、服务器端编程框架,
netty提供异步的,事件驱动的网络应用程序框架和工具,可以快速开发高可用的客户端和服务器。
netty是基于nio的,它封装了jdk的nio,让我们使用起来更加方法灵活。
netty其优势在于自身良好的线程模型架构，主要处理方式有三种线程模型，1、 单线程 2、主从线程 3、主从多线程

### Netty 的优点：


API 使用简单，开发门槛低；
功能强大，预置了多种编解码功能，支持多种主流协议；
定制能力强，可以通过 ChannelHandler 对通信框架进行灵活的扩展；
性能高，通过与其它业界主流的 NIO 框架对比，Netty 的综合性能最优；
社区活跃，版本迭代周期短，发现的 BUG 可以被及时修复，同时，更多的新功能会被加入；
经历了大规模的商业应用考验，质量得到验证。在互联网、大数据、网络游戏、企业应用、电信软件等众多行业得到成功商用，证明了它完全满足不同行业的商用标准


首先它是个框架，是个“半成品”，不能开箱即用，
你必须得拿过来做点定制，利用它开发出自己的应用程序，然后才能运行（就像使用Spring那样）。 
一个更加知名的例子就是阿里巴巴的Dubbo了，这个RPC框架的底层用的就是Netty。 

### Netty各模块
netty各模块
org
└── jboss
    └── netty
		├── bootstrap 配置并启动服务的类
		├── buffer 缓冲相关类，对NIO Buffer做了一些封装
		├── channel 核心部分，处理连接
		├── container 连接其他容器的代码
		├── example 使用示例
		├── handler 基于handler的扩展部分，实现协议编解码等附加功能
		├── logging 日志
		└── util 工具类


### Netty中的buffer机制

netty结构最底层是buffer机制。
buffer中文名又叫缓冲区，按照维基百科的解释，是"在数据传输时，在内存里开辟的一块临时保存数据的区域"。它其实是一种化同步为异步的机制，可以解决数据传输的速率不对等以及不稳定的问题。

Channel：通道
ChannelHandler：业务逻辑处理
ChannelPipeline：用户存放ChannelHandler的容器
ChannelContext：通信管道上下文


### 高性能IO设计模式：Reactor和Proactor。

在Reactor模式中，会先对每个client注册感兴趣的事件，然后有一个线程专门去轮询每个client是否有事件发生，当有事件发生时，便顺序处理每个事件，当所有事件处理完之后，便再转去继续轮询

在Proactor模式中，当检测到有事件发生时，会新起一个异步操作，然后交由内核线程去处理，当内核线程完成IO操作之后，发送一个通知告知操作已完成，可以得知，异步IO模型采用的就是Proactor模式。Java AIO使用的这种。
