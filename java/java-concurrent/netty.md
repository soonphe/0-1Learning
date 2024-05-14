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


### Java引用netty

#### netty服务端-bind绑定端口
依赖：
```
		<dependency>
			<groupId>io.netty</groupId>
			<artifactId>netty-all</artifactId>
			<version>4.1.104.Final</version>
			<scope>compile</scope>
		</dependency>
```

定义Socket服务端处理器
```
@Slf4j
@Component
@ChannelHandler.Sharable
public class SocketHandler extends ChannelInboundHandlerAdapter {
    public static final ChannelGroup clients = new DefaultChannelGroup(GlobalEventExecutor.INSTANCE);
    public static final Gson gson = new Gson();

	//这里的Sender为自定义发送消息
    private final Sender sender;
    @Autowired
    private  Client client;
    @Autowired
    private RecordService recordService;

    @Autowired
    public SocketHandler( Sender sender) {
        this.sender = sender;
    }

    /**
     * 读取到客户端发来的消息
     *
     * @param ctx ChannelHandlerContext  上下文对象
     * @param msg msg
     * @throws Exception e
     */
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    	...
    	业务逻辑
    	...
    }
    
    @Override
    public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
        log.info("新的客户端链接：" + ctx.channel().id().asShortText());
        clients.add(ctx.channel());
    }

    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
        clients.remove(ctx.channel());
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        log.error("链接异常：" + ctx.channel().id().asShortText(), cause);
        ctx.channel().close();
        clients.remove(ctx.channel());
    }
```

Socket服务端初始化器
```
@Component
public class SocketInitializer extends ChannelInitializer<SocketChannel> {

    private final SocketHandler socketHandler;

    @Autowired
    public SocketInitializer(SocketHandler socketHandler) {
        this.socketHandler = socketHandler;
    }

    @Override
    protected void initChannel(SocketChannel socketChannel) throws Exception {
        ChannelPipeline pipeline = socketChannel.pipeline();
        // 添加对byte数组的编解码，netty提供了很多编解码器，你们可以根据需要选择
        pipeline.addLast(new JsonObjectDecoder());
        pipeline.addLast(new StringDecoder());
        pipeline.addLast(new StringEncoder());
        pipeline.addLast(new LineEncoder());
        // 添加上自己的处理器
        pipeline.addLast(socketHandler);
    }
}
```

定义Socket服务端
```
@Slf4j
@Component
public class SocketServer {
    @Resource
    private SocketInitializer socketInitializer;

    @Getter
    private ServerBootstrap serverBootstrap;

    /**
     * netty服务监听端口
     */
    @Value("${netty.port:8088}")
    private int port;
    /**
     * 主线程组数量
     */
    @Value("${netty.bossThread:1}")
    private int bossThread;

    /**
     * 启动netty服务器
     */
    public void start() {
        this.init();
        this.serverBootstrap.bind(this.port);
        log.info("Netty started on port: {} (TCP) with boss thread {}", this.port, this.bossThread);
    }

    /**
     * 初始化netty配置
     */
    private void init() {
        // 创建两个线程组，bossGroup为接收请求的线程组，一般1-2个就行
        NioEventLoopGroup bossGroup = new NioEventLoopGroup(this.bossThread);
        // 实际工作的线程组
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();
        this.serverBootstrap = new ServerBootstrap();
        this.serverBootstrap.group(bossGroup, workerGroup) // 两个线程组加入进来
                .channel(NioServerSocketChannel.class)  // 配置为nio类型
                .childHandler(this.socketInitializer); // 加入自己的初始化器
    }
}
```

定义启动时启动SocketServer
```
@Component
public class NettyStartListener implements ApplicationRunner {
    @Resource
    private SocketServer socketServer;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        this.socketServer.start();
    }
}
```

发送数据：
```java

```

#### netty客户端-connect连接端口
依赖：
```
		<dependency>
			<groupId>io.netty</groupId>
			<artifactId>netty-all</artifactId>
			<version>4.1.104.Final</version>
			<scope>compile</scope>
		</dependency>
```

客户端处理器
```java
/**
 * @author Gjing
 *
 * 客户端处理器
 **/
@Slf4j
public class NettyClientHandler extends ChannelInboundHandlerAdapter {
	@Override
	public void channelActive(ChannelHandlerContext ctx) throws Exception {
		log.info("客户端Active .....");
	}
	@Override
	public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
		log.info("客户端收到消息: {}", msg.toString());
	}
	@Override
	public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
		cause.printStackTrace();
		ctx.close();
	}
}
```

客户端初始化器：
```java
/**
 * @author Gjing
 * 客户端初始化器
 **/
public class NettyClientInitializer extends ChannelInitializer<SocketChannel> {
	@Override
	protected void initChannel(SocketChannel socketChannel) throws Exception {
		socketChannel.pipeline().addLast("decoder", new StringDecoder());
		socketChannel.pipeline().addLast("encoder", new StringEncoder());
		socketChannel.pipeline().addLast(new NettyClientHandler());
	}
}
```
编写客户端：
```java
/**
 * @author Gjing
 **/
@Component
@Slf4j
public class NettyClient {
	public void start() {
		EventLoopGroup group = new NioEventLoopGroup();
		Bootstrap bootstrap = new Bootstrap()
				.group(group)
				//该参数的作用就是禁止使用Nagle算法，使用于小数据即时传输
				.option(ChannelOption.TCP_NODELAY, true)
				.channel(NioSocketChannel.class)
				.handler(new NettyClientInitializer());
		try {
			ChannelFuture future = bootstrap.connect("127.0.0.1", 8090).sync();
			log.info("客户端成功....");
			//发送消息
			future.channel().writeAndFlush("你好啊");
			// 等待连接被关闭
			future.channel().closeFuture().sync();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}finally {
			group.shutdownGracefully();
		}
	}
}
```
启动类
```java
@SpringBootApplication
public class ClientApplication {
	public static void main(String[] args) {
		SpringApplication.run(ClientApplication.class, args);
		//启动netty客户端
		NettyClient nettyClient = new NettyClient();
		nettyClient.start();
	}
}
```

#### 网页端websocket代码
依赖
```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-websocket</artifactId>
			<version>2.1.12.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.java-websocket</groupId>
			<artifactId>Java-WebSocket</artifactId>
			<version>1.3.8</version>
		</dependency>
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
            <version>2.1.12.RELEASE</version>
        </dependency>
```

直接继承TextWebSocketHandler，重写方法
```
public class PublishVideo extends TextWebSocketHandler {

    private static final Logger logger = LoggerFactory.getLogger(PublishVideo.class);

    private static final ConcurrentHashMap<String,WebSocketSession> deviceList = new ConcurrentHashMap();
    private static final ConcurrentHashMap<String,WebSocketSession> userList = new ConcurrentHashMap();
    private static String qiniuAccessKey="eBveY-tK55bg-hT1am_0AkZpfnsOrGXqbgkzRykL";
    private static String qiniuSecretKey="JRR-WtWvv90rCoCv_4eFWnvTRjIaNMGJcd8pbf8X";
    private static Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
    private static StreamManager streamManager = new StreamManager(auth);
    private static String namespaceId = "static1";

    @Autowired
    private AmqpTemplate amqpTemplate;

    @Override
    public void afterConnectionEstablished(WebSocketSession session)
            throws Exception {
        super.afterConnectionEstablished(session);
        logger.info("WebSocketSession建立连接：session={}",session.getId());
        ...
        session.sendMessage(new TextMessage(JSON.toJSONString(result)));
    }
    
    @Override
    protected void handleTextMessage(WebSocketSession session,
                                     TextMessage message) throws Exception {
		logger.info("接受到数据：{},session={}",message.getPayload(),session.getId());
	}
	
	@Override
    public void afterConnectionClosed(WebSocketSession session,
                                      CloseStatus status) throws Exception {
        if (session.isOpen()){
            session.sendMessage(new TextMessage("连接已断开"));
        }
        super.afterConnectionClosed(session, status);
    }
```

#### web端websocket代码
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>测试 WebSocket 的使用</title>
</head>
<body>
    <input type="text" id="message">
    <button id="send-button">发送</button>

    <script>
        // 创建一个 WebSocket 实例，连接到服务器的 /test 路径
        // let websocket = new WebSocket("ws://localhost:8081/videoMonitor");
		let websocket = new WebSocket("ws://116.140.211.84:81/videoMonitor");

        // WebSocket 连接建立成功的回调函数
        websocket.onopen = function () {
            console.log("WebSocket 连接成功！");
        }

        // WebSocket 收到消息的回调函数
        websocket.onmessage = function (message) {
            console.log("WebSocket 收到消息：" + message.data);
        }

        // WebSocket 连接断开的回调函数
        websocket.onclose = function () {
            console.log("WebSocket 连接断开！");
        }

        // WebSocket 连接异常的回调函数
        websocket.onerror = function () {
            console.log("WebSocket 连接异常！");
        }

        // 获取页面元素
        let messageInput = document.querySelector("#message");
        let sendButton = document.querySelector('#send-button');

        // 发送按钮的点击事件
        sendButton.onclick = function(){
            console.log("WebSocket 发送消息：" + messageInput.value);
            websocket.send(messageInput.value); // 向服务器发送消息
        }
    </script>
</body>
</html>

```

### ChannelOption参数详解
1、ChannelOption.SO_BACKLOG
ChannelOption.SO_BACKLOG对应的是tcp/ip协议listen函数中的backlog参数，函数listen(int socketfd,int backlog)用来初始化服务端可连接队列，服务端处理客户端连接请求是顺序处理的，所以同一时间只能处理一个客户端连接，多个客户端来的时候，服务端将不能处理的客户端连接请求放在队列中等待处理，backlog参数指定了队列的大小

2、ChannelOption.SO_REUSEADDR
ChanneOption.SO_REUSEADDR对应于套接字选项中的SO_REUSEADDR，这个参数表示允许重复使用本地地址和端口，比如，某个服务器进程占用了TCP的80端口进行监听，此时再次监听该端口就会返回错误，使用该参数就可以解决问题，该参数允许共用该端口，这个在服务器程序中比较常使用，比如某个进程非正常退出，该程序占用的端口可能要被占用一段时间才能允许其他进程使用，而且程序死掉以后，内核一需要一定的时间才能够释放此端口，不设置SO_REUSEADDR就无法正常使用该端口。

3、ChannelOption.SO_KEEPALIVE
Channeloption.SO_KEEPALIVE参数对应于套接字选项中的SO_KEEPALIVE，该参数用于设置TCP连接，当设置该选项以后，连接会测试链接的状态，这个选项用于可能长时间没有数据交流的连接。当设置该选项以后，如果在两小时内没有数据的通信时，TCP会自动发送一个活动探测数据报文

4、ChannelOption.SO_SNDBUF和ChannelOption.SO_RCVBUF
ChannelOption.SO_SNDBUF参数对应于套接字选项中的SO_SNDBUF，ChannelOption.SO_RCVBUF参数对应于套接字选项中的SO_RCVBUF这两个参数用于操作接收缓冲区和发送缓冲区的大小，接收缓冲区用于保存网络协议站内收到的数据，直到应用程序读取成功，发送缓冲区用于保存发送数据，直到发送成功。

5、ChannelOption.SO_LINGER
ChannelOption.SO_LINGER参数对应于套接字选项中的SO_LINGER,Linux内核默认的处理方式是当用户调用close（）方法的时候，函数返回，在可能的情况下，尽量发送数据，不一定保证会发生剩余的数据，造成了数据的不确定性，使用SO_LINGER可以阻塞close()的调用时间，直到数据完全发送

6、ChannelOption.TCP_NODELAY
ChannelOption.TCP_NODELAY参数对应于套接字选项中的TCP_NODELAY,该参数的使用与Nagle算法有关,Nagle算法是将小的数据包组装为更大的帧然后进行发送，而不是输入一次发送一次,因此在数据包不足的时候会等待其他数据的到了，组装成大的数据包进行发送，虽然该方式有效提高网络的有效负载，但是却造成了延时，而该参数的作用就是禁止使用Nagle算法，使用于小数据即时传输，于TCP_NODELAY相对应的是TCP_CORK，该选项是需要等到发送的数据量最大的时候，一次性发送数据，适用于文件传输。

7、IP_TOS
IP参数，设置IP头部的Type-of-Service字段，用于描述IP包的优先级和QoS选项。

8、ALLOW_HALF_CLOSURE
Netty参数，一个连接的远端关闭时本地端是否关闭，默认值为False。值为False时，连接自动关闭；为True时，触发ChannelInboundHandler的userEventTriggered()方法，事件为ChannelInputShutdownEvent。

四、Netty的future.channel().closeFuture().sync();到底有什么用？
主线程执行到这里就 wait 子线程结束，子线程才是真正监听和接受请求的，closeFuture()是开启了一个channel的监听器，负责监听channel是否关闭的状态，如果监听到channel关闭了，子线程才会释放，syncUninterruptibly()让主线程同步等待子线程结果