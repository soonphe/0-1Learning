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
        // 将JSON对象和数组的字节流拆分为单独的对象/数组，并将其传递到ChannelPipeline
        pipeline.addLast(new JsonObjectDecoder());
        pipeline.addLast(new StringDecoder());
        pipeline.addLast(new StringEncoder());
        //对请求的字符串应用行分隔符，并将其编码为ByteBuf
        pipeline.addLast(new LineEncoder());
        // 添加上自己的处理器
        pipeline.addLast(socketHandler);
    }
}
```
关于netty解码器：
抽象解码器
- ByteToMessageDecoder: 用于将字节转为消息，需要检查缓冲区是否有足够的字节,用于将接收到的二进制数据(Byte)解码，得到完整的请求报文(Message)。
- ReplayingDecoder: 继承ByteToMessageDecoder，不需要检查缓冲区是否有足够的字节,但是 ReplayingDecoder速度略慢于ByteToMessageDecoder,同时不是所有的ByteBuf都支持。
  - 选择：项目复杂性高则使用ReplayingDecoder，否则使用 ByteToMessageDecoder
  - ReplayingDecoder是byte-to-message解码的一种特殊的抽象基类，byte-to-message解码读取缓冲区的数据之前需要检查缓冲区是否有足够的字节，使用
  - ReplayingDecoder就无需自己检查；若ByteBuf中有足够的字节，则会正常读取；若没有足够的字节则会停止解码。
  - 也正因为这样的包装使得ReplayingDecoder带有一定的局限性。
    1. 不是所有的操作都被ByteBuf支持，如果调用一个不支持的操作会抛出DecoderException。
    2. ByteBuf.readableBytes()大部分时间不会返回期望值
- MessageToMessageDecoder: 用于从一种消息解码为另外一种消息（例如POJO到POJO）
  - ByteToMessageDecoder是将二进制流进行解码后，得到有效报文。而MessageToMessageDecoder则是将一个本身就包含完整报文信息的对象转换成另一个Java对象。
  - 举例: 前面介绍了ByteToMessageDecoder的部分子类解码后，会直接将包含了报文完整信息的ByteBuf实例交由之后的ChannelInboundHandler处理，此时，你可以在ChannelPipeline中，再添加一个MessageToMessageDecoder，将ByteBuf中的信息解析后封装到Java对象中，简化之后的ChannelInboundHandler的操作

ByteToMessageDecoder提供的一些常见的实现类：
```
FixedLengthFrameDecoder：定长协议解码器，我们可以指定固定的字节数算一个完整的报文
LineBasedFrameDecoder：  行分隔符解码器，遇到\n或者\r\n，则认为是一个完整的报文
DelimiterBasedFrameDecoder：    分隔符解码器，与LineBasedFrameDecoder类似，只不过分隔符可以自己指定
LengthFieldBasedFrameDecoder：长度编码解码器，将报文划分为报文头/报文体，根据报文头中的Length字段确定报文体的长度，因此报文提的长度是可变的
JsonObjectDecoder：json格式解码器，当检测到匹配数量的"{" 、”}”或”[””]”时，则认为是一个完整的json对象或者json数组。
```

关于MessageToMessageDecoder：
```
MessageToMessageDecoder的类声明如下：
/**
  * 其中泛型参数I表示我们要解码的消息类型。例前面，我们在ToIntegerDecoder中，把二进制字节流转换成了一个int类型的整数。
  */
public abstract class MessageToMessageDecoder<I> extends ChannelInboundHandlerAdapter

类似的，MessageToMessageDecoder也有一个decode方法需要覆盖 ，如下：
/**
* 参数msg，需要进行解码的参数。例如ByteToMessageDecoder解码后的得到的包含完整报文信息ByteBuf
* List<Object> out参数：将msg经过解析后得到的java对象，添加到放到List<Object> out中
*/
protected abstract void decode(ChannelHandlerContext ctx, I msg, List<Object> out) throws Exception;
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

netty测试客户端：
```
public class JsonClient {
    private final static Logger LOGGER = LoggerFactory.getLogger(JsonClient.class);
    public static final String SERVER_IP = "127.0.0.1";
    public static final int SERVER_PORT = 9034;
 
    public static void main(String[] args) {
        Bootstrap b = new Bootstrap();
        NioEventLoopGroup workGroup = new NioEventLoopGroup();
        try {
            b.group(workGroup);
            b.channel(NioSocketChannel.class);
            b.remoteAddress(SERVER_IP, SERVER_PORT);
            b.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
            b.option(ChannelOption.CONNECT_TIMEOUT_MILLIS,10000);
            b.option(ChannelOption.SO_KEEPALIVE, true);
            b.handler(new ChannelInitializer<SocketChannel>() {
                @Override
                protected void initChannel(SocketChannel socketChannel) throws Exception {
                    /**
                     * LengthFieldPrepender是Netty中的一个编码器，用于在消息前面添加一个表示消息长度的字段。它可以将消息转换为字节数组，并在消息前面添加一个表示消息长度的字段，通常是4个字节。这个字段的值表示消息的长度，使得接收方可以根据这个长度来正确地解析消息。
                     * LengthFieldPrepender通常与LengthFieldBasedFrameDecoder一起使用，后者是一个解码器，用于从字节数组中解析出消息。LengthFieldPrepender和LengthFieldBasedFrameDecoder一起使用可以实现TCP粘包和拆包的处理，确保接收方可以正确地解析出发送方发送的消息。
                     * lengthFieldLength：设置长度字段为4
                     */
//                    socketChannel.pipeline().addLast(new LengthFieldPrepender(4));
                    socketChannel.pipeline().addLast(new StringEncoder(CharsetUtil.UTF_8));
                    socketChannel.pipeline().addLast(new JsonMsgEncoder());
                }
            });
            ChannelFuture channelFuture = b.connect();
            channelFuture.addListener((ChannelFuture futureListener) -> {
                if (futureListener.isSuccess()) {
                    LOGGER.info("JsonClient客户端连接成功!");
                } else {
                    LOGGER.info("JsonClient客户端连接失败!");
                }
            });
            channelFuture.sync();
            Channel channel = channelFuture.channel();
            Scanner scanner = new Scanner(System.in);
            LOGGER.info("请输入发送内容:");
            // 发送回调监听
            GenericFutureListener sendCallBack = future -> {
                if (future.isSuccess()) {
                    LOGGER.info("发送成功!");
                } else {
                    LOGGER.info("发送失败!");
                }
            };
 
            while (scanner.hasNext()) {
                //获取输入的内容
                String next = scanner.next();
                JsonMsgDto jsonMsgDto = new JsonMsgDto();
                jsonMsgDto.setContent(next);
                ChannelFuture writeAndFlushFuture = channel.writeAndFlush(jsonMsgDto);
                writeAndFlushFuture.addListener(sendCallBack);
                LOGGER.info("请输入发送内容:");
            }
        } catch (Exception ex) {
            LOGGER.error(ExceptionUtil.stacktraceToString(ex));
        }finally {
            workGroup.shutdownGracefully();
        }
    }
}
```

### ChannelOption参数详解
- ChannelOption.ALLOCATOR 解决大量短连接时，频繁创建和销毁ByteBuf对象导致的内存泄漏问题，可设置为 PooledByteBufAllocator.DEFAULT
- ChannelOption.SO_BACKLOG ：对应的是tcp/ip协议listen函数中的backlog参数，函数listen(int socketfd,int backlog)用来初始化服务端可连接队列，服务端处理客户端连接请求是顺序处理的，所以同一时间只能处理一个客户端连接，多个客户端来的时候，服务端将不能处理的客户端连接请求放在队列中等待处理，backlog参数指定了队列的大小
- ChannelOption.SO_REUSEADDR ：对应于套接字选项中的SO_REUSEADDR，这个参数表示允许重复使用本地地址和端口，比如，某个服务器进程占用了TCP的80端口进行监听，此时再次监听该端口就会返回错误，使用该参数就可以解决问题，该参数允许共用该端口，这个在服务器程序中比较常使用，比如某个进程非正常退出，该程序占用的端口可能要被占用一段时间才能允许其他进程使用，而且程序死掉以后，内核一需要一定的时间才能够释放此端口，不设置SO_REUSEADDR就无法正常使用该端口。
- ChannelOption.SO_KEEPALIVE ：对应于套接字选项中的SO_KEEPALIVE，该参数用于设置TCP连接，当设置该选项以后，连接会测试链接的状态，这个选项用于可能长时间没有数据交流的连接。当设置该选项以后，如果在两小时内没有数据的通信时，TCP会自动发送一个活动探测数据报文
- ChannelOption.SO_SNDBUF：对应于套接字选项中的SO_SNDBUF，发送缓冲区的大小，发送缓冲区用于保存发送数据，直到发送成功。
- ChannelOption.SO_RCVBUF对应于套接字选项中的SO_RCVBUF，操作接收缓冲区大小，接收缓冲区用于保存网络协议站内收到的数据，直到应用程序读取成功
- ChannelOption.SO_LINGER：对应于套接字选项中的SO_LINGER,Linux内核默认的处理方式是当用户调用close（）方法的时候，函数返回，在可能的情况下，尽量发送数据，不一定保证会发生剩余的数据，造成了数据的不确定性，使用SO_LINGER可以阻塞close()的调用时间，直到数据完全发送
- ChannelOption.TCP_NODELAY：对应于套接字选项中的TCP_NODELAY,该参数的使用与Nagle算法有关,Nagle算法是将小的数据包组装为更大的帧然后进行发送，而不是输入一次发送一次,因此在数据包不足的时候会等待其他数据的到了，组装成大的数据包进行发送，虽然该方式有效提高网络的有效负载，但是却造成了延时，而该参数的作用就是禁止使用Nagle算法，使用于小数据即时传输，于TCP_NODELAY相对应的是TCP_CORK，该选项是需要等到发送的数据量最大的时候，一次性发送数据，适用于文件传输。
- IP_TOS：IP参数，设置IP头部的Type-of-Service字段，用于描述IP包的优先级和QoS选项。
- ALLOW_HALF_CLOSURE：Netty参数，一个连接的远端关闭时本地端是否关闭，默认值为False。值为False时，连接自动关闭；为True时，触发ChannelInboundHandler的userEventTriggered()方法，事件为ChannelInputShutdownEvent。


四、Netty的future.channel().closeFuture().sync();到底有什么用？
主线程执行到这里就 wait 子线程结束，子线程才是真正监听和接受请求的，closeFuture()是开启了一个channel的监听器，负责监听channel是否关闭的状态，如果监听到channel关闭了，子线程才会释放，syncUninterruptibly()让主线程同步等待子线程结果

### netty关闭连接
netty关闭连接：
* 		通过Channel 实例关闭连接：
Channel channel = ...; // 获取到的Channel实例
channel.close();
* 		通过ChannelHandlerContext 实例关闭连接：
ChannelHandlerContext ctx = ...; // 获取到的ChannelHandlerContext实例
ctx.close();
* 		如果你想关闭所有的连接，可以关闭ChannelGroup 中的所有通道：
ChannelGroup channelGroup = ...; // 获取到的ChannelGroup实例
channelGroup.close();
* 		如果你想在一定的时间后关闭连接，可以使用scheduleAtFixedRate 方法来定时关闭：
Channel channel = ...; // 获取到的Channel实例
ScheduledFuture<?> future = ctx.executor().schedule(() -> {
channel.close();
}, 5, TimeUnit.SECONDS);

### netty报错java.io.IOException: Connection reset by peer
常见原因：
1）服务器的并发连接数超过了其承载量，服务器会将其中一些连接关闭； 如果知道实际连接服务器的并发客户数没有超过服务器的承载量，则有可能是中了病毒或者木马，引起网络流量异常。可以使用netstat -an查看网络连接情况。 
 2）客户关掉了浏览器，而服务器还在给客户端发送数据； 
 3）浏览器端按了Stop； 这两种情况一般不会影响服务器。但是如果对异常信息没有特别处理，有可能在服务器的日志文件中，重复出现该异常，造成服务器日志文件过大，影响服务器的运行。可以对引起异常的部分，使用try…catch捕获该异常，然后不输出或者只输出一句提示信息，避免使用e.printStackTrace();输出全部异常信息。 
 4）防火墙的问题； 如果网络连接通过防火墙，而防火墙一般都会有超时的机制，在网络连接长时间不传输数据时，会关闭这个TCP的会话，关闭后在读写，就会导致异常。 如果关闭防火墙，解决了问题，需要重新配置防火墙，或者自己编写程序实现TCP的长连接。实现TCP的长连接，需要自己定义心跳协议，每隔一段时间，发送一次心跳协议，双方维持连接。 
 5）JSP的buffer问题。 JSP页面缺省缓存为8k，当JSP页面数据比较大的时候，有可能JSP没有完全传递给浏览器。这时可以适当调整buffer的大小。

常见原因及解决方案：
超时、socketIO 发送的数据量太大、客户端关闭连接、服务端关闭连接、网络问题、防火墙问题（是否存在白名单）、JSP的buffer问题等。

### netty回调方法顺序
调用顺序：
* handlerAdded
- channelRegistered
- channelActive
- channelReadComplete
- channelInactive
- channelUnregistered
* handlerRemoved

### netty发送消息添加状态监听
```
  tcpChannel.writeAndFlush(response).addListener(future -> {
  if (future.isSuccess()) {
  log.info("===下发S02消息成功===");
  } else {
  log.error("===下发S02消息失败===");
  }
  });
```

### netty处理消息结构
字符串或json结构：
```
public class SocketHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        // 由于我们配置的是 字节数组 编解码器，所以这里取到的用户发来的数据是 byte数组
//        byte[] data = (byte[]) msg;
        String data = (String) msg;

        // 给其他人转发消息
//        for (Channel client : clients) {
//            if (!client.equals(ctx.channel())) {
//                client.writeAndFlush(data);
//            }
//        }
        ReqObj reqObj = gson.fromJson(data, ReqObj.class);
        // check msgId
        String msgId = reqObj.getMsgId();
        switch (msgId) {
            case "S01":
                handleS01(ctx, reqObj);
                break;
            case "R02":
                handleR02(ctx, reqObj);
                break;
            default:
                log.error("unknown msgId: {}", msgId);
                ctx.channel().close();
        }
    }

```

ByteBuf结构：
```
public class ClientHandler extends SimpleChannelInboundHandler<ByteBuf> {
    private static final String TAG = "ClientHandler";
    ConcurrentHashMap<Integer, ConcurrentHashMap<Integer, ByteBuf>> subPackages = new ConcurrentHashMap<Integer, ConcurrentHashMap<Integer, ByteBuf>>(10);

    @Override
    protected void channelRead0(ChannelHandlerContext channelHandlerContext, ByteBuf in) {
        SubPacketAttr subPacketAttr = null;
        String isSplit = "0";
        if (in != null && in.capacity() > 15) {
            //logger.debug("转前后\n{}", ByteBufUtil.prettyHexDump(in));
            // MyLog.e("转义前\n{}", ByteBufUtil.prettyHexDump(in));
            ByteBuf jt808Buf = Unpooled.buffer(in.capacity());
            // 转义
            int length = 0;
            int headLength = 16;
            while (in.isReadable()) {
                int tmp = in.readByte();
                if (tmp == 0x7d) {
                    int next = in.readByte();
                    if (next == 0x01) {
                        jt808Buf.writeByte(tmp);
                    } else {
                        jt808Buf.writeByte(0x7e);
                    }
                } else {
                    jt808Buf.writeByte(tmp);
                }
                length++;
            }
//            MyLog.d("转义后\n{}", ByteBufUtil.hexDump(jt808Buf));
            // 检查检验位
            jt808Buf.resetReaderIndex();
            int first = jt808Buf.readByte();
            while (jt808Buf.isReadable()) {
                if (jt808Buf.readerIndex() < length - 1) {
                    first = first ^ jt808Buf.readByte();
                } else {
                    break;
                }
            }
            int tmp2 = jt808Buf.readByte();
            if (first != tmp2) {
                MyLog.e("校验位错误,{},{}", first + "  " + tmp2);
            } else {
                jt808Buf.resetReaderIndex();
                //logger.debug("{}", ByteBufUtil.prettyHexDump(jt808Buf));
                BaseHead head = new BaseHead();
                Integer msgId = jt808Buf.getUnsignedShort(1);
                head.setMsgId(msgId);
                String sb = org.apache.commons.lang3.StringUtils.leftPad(Integer.toBinaryString(jt808Buf.getShort(3)), 16, "0");
                int bodyLength = Integer.parseInt(sb.substring(6), 2);
                byte[] dst = new byte[8];
                jt808Buf.getBytes(5, dst);
                String mobile = BCDUtils.bcd2Str(dst);
                //TODO
                //mobile=StringUtils.stripStart(mobile, "0");
              /*  MyLog.e("消息体",
                        "ID=0x" + Integer.toHexString(msgId) +
                                "属性=" + sb +
                                "长度=" + sb.substring(6) +
                                "是否分包=" + sb.substring(2, 3)
                                + "长度=" + bodyLength +
                                "手机号=" + mobile);*/
                isSplit = sb.substring(2, 3);


                ByteBuf jt808Buf2 = null;
                //判断是否分包
                if ("1".equals(isSplit)) {
                    headLength = 20;
                    jt808Buf2 = Unpooled.buffer(in.capacity());
                    subPacketAttr = new SubPacketAttr();
                    subPacketAttr.setSum(jt808Buf.getShort(16));
                    subPacketAttr.setNumber(jt808Buf.getShort(18));
//                    logger.info("总包数{},第{}分包", subPacketAttr.getSum(), subPacketAttr.getNumber());
                    jt808Buf.readerIndex(headLength);
                    if (subPacketAttr.getNumber() == 1) {
                        //第一包建MAP
                        ConcurrentHashMap<Integer, ByteBuf> subData = new ConcurrentHashMap<Integer, ByteBuf>(subPacketAttr.getSum());
                        subData.put(subPacketAttr.getNumber(), jt808Buf.readBytes(bodyLength));
                        subPackages.put(msgId, subData);
//                        logger.info("合并分包 ,第{}包，{},分包为：\n{}", subPacketAttr.getNumber(), subData.size());
                    } else {
                        ConcurrentHashMap<Integer, ByteBuf> subData = subPackages.get(msgId);
                        if (subData != null) {
                            subData.put(subPacketAttr.getNumber(), jt808Buf.readBytes(bodyLength));
//                            logger.info("合并分包 ,第{}包，{},分包为：\n{}", subPacketAttr.getNumber(), subData.size());
                        }
                    }


                }
//                logger.debug("headLength={}", headLength);
                jt808Buf.readerIndex(headLength);
                switch (msgId) {
                    case 0X8001:
                        // 服务器通用应答
                        int LineNum = jt808Buf.readUnsignedShort();
                        int id = jt808Buf.readUnsignedShort();
                        int result = jt808Buf.readUnsignedByte();
                        MyLog.d("通用应答", "流水号=" + LineNum + ",msgid=" + Integer.toHexString(id)  + ",结果=" + result);
                        Handler handler = new Handler(Looper.getMainLooper());
                        handler.post(new Runnable() {

                            @Override
                            public void run() {
                                InfomationHandler infomationHandler1 = AppConfig.tcpMap.get("8001_" + LineNum);
                                if (null == infomationHandler1) return;
                                if (result == 0) {
                                    infomationHandler1.success(LineNum + "");
                                } else {
                                    infomationHandler1.fail(result);
                                }
                                AppConfig.tcpMap.remove("8001_" + LineNum);
                            }
                        });


                        break;
                    case 0x8100:
```
原始结构数据与解析后结构数据：
```
原始数据
FFFFFF80 FFFFFF80 01 00 05 00 00 01 36 27 58 68 FFFFFF85 FFFFFF88 4C 3C 33 66 01 02 00 0F 

UnpooledHeapByteBuf(ridx: 0, widx: 22, cap: 22)
ID=0x8001属性=0000000000000101长度=0000000101是否分包=0长度=5手机号=0000013627586885
8080010005000001362758688588c33c33d501020033
说明：
ID= getUnsignedShort(1); 第1位short, 即为：FFFFFF80 01
属性= jt808Buf.getShort(3); 第3位short，转成16位，即为：00 05
长度= sb.substring(6); 为属性截取的字符串 
是否分包= sb.substring(2, 3); 也是属性截取的字符串 
手机号= getBytes(5, dst);   第5位byte，即为：01 36 27 58 68 FFFFFF85
后面为流水号：16位
最后面为00

//获取此缓冲区中指定绝对索引处的16位短整数。此方法不会修改此缓冲区的readerIndex或writerIndex。
jt808Buf.getShort(3);
//获取此缓冲区中指定绝对索引处的无符号16位短整数。此方法不会修改此缓冲区的readerIndex或writerIndex。
jt808Buf.getUnsignedShort(1);
byte[] dst = new byte[8];
//从指定的绝对索引开始，将此缓冲区的数据传输到指定的目标。此方法不会修改此缓冲区的readerIndex或writerIndex
jt808Buf.getBytes(5, dst);
//直接转义打印
ByteBufUtil.hexDump(jt808Buf));

```

其他示例：
```
FFFFFF80 FFFFFF89 00 00 1B 00 00 01 36 27 58 68 FFFFFF85 FFFFFF88 4D 3C 13 FFFFFF85 03 00 00 2C FFFFFF8B 54 30 30 30 30 30 30 30 30 30 30 30 30 31 37 39 00 00 00 00 17 
UnpooledHeapByteBuf(ridx: 0, widx: 44, cap: 44)
ID=0x8900属性=0000000000011011长度=0000011011是否分包=0长度=27手机号=0000013627586885

2024-10-12 15:15:04.872  9454-9715  原始消息    E  UnpooledHeapByteBuf(ridx: 0, widx: 44, cap: 44)
2024-10-12 15:15:04.886  9454-9715  消息体     E  ID=0x8900属性=0000000000011011长度=0000011011是否分包=0长度=27手机号=0000013627586885
2024-10-12 15:15:04.985  9454-9715  原始消息    E  UnpooledHeapByteBuf(ridx: 0, widx: 17, cap: 17)
2024-10-12 15:15:04.996  9454-9715  消息体     E  ID=0x8104属性=0000000000000000长度=0000000000是否分包=0长度=0手机号=0000013627586885
2024-10-12 15:15:05.089  9454-9715  原始消息    E  UnpooledHeapByteBuf(ridx: 0, widx: 22, cap: 22)
2024-10-12 15:15:05.100  9454-9715  消息体     E  ID=0x8001属性=0000000000000101长度=0000000101是否分包=0长度=5手机号=0000013627586885

不转换为16进制：
-128 -128 1 0 5 0 0 1 54 39 88 104 -123 -120 -108 60 51 -88 0 2 0 24
UnpooledHeapByteBuf(ridx: 0, widx: 22, cap: 22)
ID=0x8001属性=0000000000000101长度=0000000101是否分包=0长度=5手机号=0000013627586885
```

### netty ByteBuf
在netty中有一个重要的对象——ByteBuf，它其实等同于Java Nio中的ByteBuffer，但是 ByteBuf对Nio中的ByteBuffer的功能做了很多增强
和普通ByteBuffer最大的区别之一，就是ByteBuf可以自动扩容，默认长度是256，如果内容长度超过阈值时，会自动触发扩容
```
public class NettyByteBufExample {
 
    public static void main(String[] args) {
        ByteBuf buf=ByteBufAllocator.DEFAULT.buffer(); //构建一个ByteBuf
        System.out.println("=======before ======");
        log(buf);
        //构建一个字符串
        StringBuilder stringBuilder=new StringBuilder();
        for (int i = 0; i < 400; i++) {
            stringBuilder.append("-"+i);
        }
        buf.writeBytes(stringBuilder.toString().getBytes());
        System.out.println("=======after ======");
        buf.readShort(); //读取2个字节
        buf.readByte(); //读取一个字节
 
        log(buf);
    }
    private static void log(ByteBuf buf){
        StringBuilder sb=new StringBuilder();
        sb.append(" read index:").append(buf.readerIndex());  //读索引
        sb.append(" write index:").append(buf.writerIndex()); //写索引
        sb.append(" capacity :").append(buf.capacity()) ; //容量
        ByteBufUtil.appendPrettyHexDump(sb,buf);
        System.out.println(sb.toString());
    }
}
```

ByteBuf创建的方法有两种
1.第一种，创建基于堆内存的ByteBuf：
```
ByteBuf buffer=ByteBufAllocator.DEFAULT.heapBuffer(10);
```
2.第二种，创建基于直接内存（堆外内存）的ByteBuf（默认情况下用的是这种）：

Java中的内存分为两个部分，一部分是不需要jvm管理的直接内存，也被称为堆外内存。堆外内存就是把内存对象分配在JVM堆意外的内存区域，这部分内存不是虚拟机管理，而是由操作系统来管理，这样可以减少垃圾回收对应用程序的影响。
```
ByteBufAllocator.DEFAULT.directBuffer(10);
```
直接内存的好处是读写性能会高一些：

如果数据放在堆中，此时需要把java堆空间中的数据放到远程服务器，首先要把堆中的数据拷贝到直接内存（堆外内存）然后再发送。
如果是把数据直接存储到堆外内存中，发送的时候就少了一个复制步骤。
但是它也有缺点，由于缺少了JVM的内存管理，所以需要我们自己来维护堆外内存，防止内存溢出。

ByteBuf的存储结构
- 废弃字节
- 读指针：readIndex
- 可读字节
- 写指针：writeIndex
- 可写字节
- 可扩容字节

容量：capacity 包含（废弃字节，读指针：readIndex ，可读字节 ，写指针：writeIndex ，可写字节）
ByteBuf其实是一个字节容器，该容器中包含三个部分，
- 已经丢弃的字节，这部分数据是无效的可读字节，
- 可读字节，这部分数据是ByteBuf的主体数据，从ByteBuf里面读取的数据都来自这部分；
- 可写字节，所有写到ByteBuf的数据都会存储到这一段
- 可扩容字节，表示ByteBuf最多还能扩容多少容量。

在ByteBuf中，有两个指针：
- readerIndex： 读指针，每读取一个字节，readerIndex自增加1。ByteBuf里面总共有witeIndex-readerIndex个字节可读，当readerIndex和writeIndex相等的时候，ByteBuf不可读
- writeIndex： 写指针，每写入一个字节，writeIndex自增加1，直到增加到capacity后，可以触发扩容后继续写入。
- ByteBuf中还有一个maxCapacity最大容量，默认的值是 Integer.MAX_VALUE ，当ByteBuf写入数据时，如果容量不足时，会触发扩容，直到capacity扩容到maxCapacity。

### 读写方法
Netty的ByteBuf数据位置索引是从0开始的，类似于数组。
- getByte(int index)：从指定位置读出一字节，这个操作不会改变ByteBuf的readerIndex或者 writerIndex 的位置。这个操作也不受readerIndex的影响（例如，当前readerIndex是1，但照样可以用getByte(0)从位置0处得到数据）。如果index小于0，或者index + 1大于ByteBuf的容量，就会抛出IndexOutOfBoundsException异常。
- readByte()：从当前readerIndex 读出一字节，并且将readerIndex的值增加1。如果ByteBuf的readableBytes的值小于1，就会抛出IndexOutOfBoundsException异常。
- readBytes(byte[] dst)：从当前readerIndex 读出dst.length个字节到字节数组dst中，并将buffer的readerIndex增加dst.length。操作完成后，buffer和数组dst的内容修改互不影响。
- isReadable()：方法判断是否有可读的数据。当(this.writerIndex -this.readerIndex) 的值大于0，isReadable()返回true。
- writeShort(int value)：在ByteBuf的当前writerIndex位置开始写入一个16位的整数，并且将writerIndex增加2。因为输入参数是int型，占4个字节，高位的16位被丢弃。
- getShort(int index)：从ByteBuf的绝对位置index开始，读取1个16位的整数。这个方法不改变ByteBuf的readerIndex和writerIndex。
- getBytes(int index, byte[] dst)：从ByteBuf的位置index开始，拷贝部分字节的内容到数组dst中，拷贝的字节数等于dst数组的长度。拷贝以后，再修改本buffer或者数组dst的内容互不影响。
- buffer.release();  // 释放ByteBuf
- buf.readerIndex(0);// 确保当前缓冲区的读索引为0
- jt808Buf.resetReaderIndex(); //将当前readerIndex重新定位到此缓冲区中标记的readerIndex。
- jt808Buf.readerIndex(); //返回此缓冲区的readerIndex。
- ByteBufUtil.hexDump(jt808Buf)); //打印ByteBuf


打印byteBuf示例：
```
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
 
public class ByteBufPrinter {
    public static void main(String[] args) {
        // 创建一个示例ByteBuf
        ByteBuf buffer = Unpooled.buffer(256);
        for (int i = 0; i < 256; i++) {
            buffer.writeByte(i); // 填充缓冲区
        }
 
        // 完整打印ByteBuf的内容
        printByteBuf(buffer);
 
        // 释放ByteBuf
        buffer.release();
    }
 
    private static void printByteBuf(ByteBuf buf) {
        // 确保当前缓冲区的读索引为0
        buf.readerIndex(0);
        // 确保当前缓冲区的写索引为缓冲区的最大容量
        buf.writerIndex(buf.capacity());
 
        // 遍历缓冲区并打印每个字节
        for (int i = 0; i < buf.capacity(); i++) {
            byte b = buf.getByte(i);
            System.out.print(String.format("%02X ", b)); // 以16进制格式打印字节
        }
        System.out.println(); // 换行
    }
}
```

#### Write相关方法
对于write方法来说，ByteBuf提供了针对各种不同数据类型的写入，比如：

writeChar，写入char类型
writeInt，写入int类型
writeFloat，写入float类型
writeBytes， 写入nio的ByteBuffer
writeCharSequence， 写入字符串
```
    public static void main(String[] args) {
        ByteBuf buf=ByteBufAllocator.DEFAULT.buffer(); //构建一个ByteBuf
        System.out.println("=======before ======");
        log(buf);
        //构建一个字符串
        StringBuilder stringBuilder=new StringBuilder();
        for (int i = 0; i < 400; i++) {
            stringBuilder.append("-"+i);
        }
        buf.writeBytes(stringBuilder.toString().getBytes());
        System.out.println("=======after ======");
        buf.readShort(); //读取2个字节
        buf.readByte(); //读取一个字节
 
        log(buf);
    }
    private static void log(ByteBuf buf){
        StringBuilder sb=new StringBuilder();
        sb.append(" read index:").append(buf.readerIndex());  //读索引
        sb.append(" write index:").append(buf.writerIndex()); //写索引
        sb.append(" capacity :").append(buf.capacity()) ; //容量
        ByteBufUtil.appendPrettyHexDump(sb,buf);
        System.out.println(sb.toString());
    }

```

#### 扩容
当向ByteBuf写入数据时，发现容量不足时，会触发扩容，而具体的扩容规则是：

构建ByteBuf时，如不传入容量参数，默认容量大小为256

假设ByteBuf初始容量是10：
```
如果写入后数据大小未超过512个字节，则选择下一个16的整数倍进行库容。 比如写入数据后大小为12，则扩容后的capacity是16。
如果写入后数据大小超过512个字节，则选择下一个2n。 比如写入后大小是512字节，则扩容后的capacity是2的10次方=1024 。（因为2的9次方=512，长度已经不够了）
扩容不能超过max capacity，否则会报错。
```

#### Reader相关方法
reader方法也同样针对不同数据类型提供了不同的操作方法：

readByte ，读取单个字节
readInt ， 读取一个int类型
readFloat ，读取一个float类型

读完一个字节后，这个字节就变成了废弃部分，再次读取的时候只能读取 未读取的部分数据。

另外，如果想重复读取哪些已经读完的数据，这里提供了两个方法来实现标记和重置：
```
public static void main(String[] args) {
        //由JVM来管理内存
        ByteBuf buf=ByteBufAllocator.DEFAULT.heapBuffer(); //堆内存
        buf.writeBytes(new byte[]{1,2,3,4});
        log(buf);
        buf.writeInt(5);
        log(buf);
        System.out.println("开始进行读取操作");
        buf.markReaderIndex(); //标记索引位置.  markWriterIndex()
        byte b=buf.readByte();
        System.out.println(b);
        buf.resetReaderIndex(); //重新回到标记位置
        log(buf);
    }

```

#### Unpooled
我们希望把header和body合并成一个ByteBuf，通常的做法是：
```
ByteBuf allBuf=Unpooled.buffer(header.readableBytes()+body.readableBytes());
allBuf.writeBytes(header);
allBuf.writeBytes(body);
```
在这个过程中，我们把header和body拷贝到了新的allBuf中，这个过程在无形中增加了两次数据拷贝操作。那有没有更高效的方法减少拷贝次数来达到相同目的呢？

#### CompositeByteBuf组件
在Netty中，提供了一个CompositeByteBuf组件，它提供了这个功能：
```
public static void main(String[] args) {
        ByteBuf header= ByteBufAllocator.DEFAULT.buffer();
        header.writeBytes(new byte[]{1,2,3,4,5});
        ByteBuf body=ByteBufAllocator.DEFAULT.buffer();
        body.writeBytes(new byte[]{6,7,8,9,10});
/*        ByteBuf total= Unpooled.buffer(header.readableBytes()+body.readableBytes());
        total.writeBytes(header);
        total.writeBytes(body);*/
        //从逻辑成面构建了一个总的buf数据。
        //第二个零拷贝实现
       /* CompositeByteBuf compositeByteBuf=Unpooled.compositeBuffer();
        compositeByteBuf.addComponents(true,header,body);
        log(compositeByteBuf);*/
        //Unpooled
        ByteBuf total=Unpooled.wrappedBuffer(header,body);
        log(total);
        header.setByte(2,9);
        log(total);
    }
```
之所以CompositeByteBuf能够实现零拷贝，是因为在组合header和body时，并没有对这两个数据进行复制，而是通过CompositeByteBuf构建了一个逻辑整体，里面仍然是两个真实对象，也就是有一个指针指向了同一个对象，所以这里类似于浅拷贝的实现。

#### wrappedBuffer
在Unpooled工具类中，提供了一个wrappedBuffer方法，来实现CompositeByteBuf零拷贝功能
```
public static void main(String[] args) {
        ByteBuf header= ByteBufAllocator.DEFAULT.buffer();
        header.writeBytes(new byte[]{1,2,3,4,5});
        ByteBuf body=ByteBufAllocator.DEFAULT.buffer();
        body.writeBytes(new byte[]{6,7,8,9,10});
        ByteBuf total=Unpooled.wrappedBuffer(header,body);
        log(total);
        header.setByte(2,9);
        log(total);
    }
```

#### copiedBuffer
copiedBuffer，和wrappedBuffer最大的区别是，该方法会实现数据复制
copiedBuffer和wrappedbuffer的区别，可以看到在 case 标注的位置中，修改了原始ByteBuf的值，并没有影响到allBb。
```
public static void main(String[] args) {
        ByteBuf header= ByteBufAllocator.DEFAULT.buffer();
        header.writeBytes(new byte[]{1,2,3,4,5});
        ByteBuf body=ByteBufAllocator.DEFAULT.buffer();
        body.writeBytes(new byte[]{6,7,8,9,10});
        ByteBuf allBb=Unpooled.copiedBuffer(header,body);
        log(allBb);
        header.setByte(2,9);
        log(allBb);
    }
```

#### 分包算法
分包算法是指在数据传输过程中，由于网络传输的限制，数据包的大小超过了网络传输的限制，需要将一个大的数据包拆分成多个小的数据包进行传输，接收端接收到数据包后，需要将多个小的数据包合并成一个大的数据包。
```
消息体消息：
ByteBuf out = Unpooled.buffer(256);
		out.writeInt(Integer.parseInt(gnss.getAlarm(), 2));
                out.writeInt(Integer.parseInt(gnss.getStatus(), 2));
                out.writeInt(gnss.getLat());
                out.writeInt(gnss.getLng());
                out.writeShort(gnss.getSpeedCar());
                out.writeShort(gnss.getSpeedGPS());
                out.writeShort(gnss.getAngle());
                out.writeBytes(BCDUtils.str2Bcd(gnss.getTimestamp()));
```

消息头消息：
```
ByteBuf extBody = Unpooled.buffer(256);
	extBody.writeShort(t.getExtMsgId());
        extBody.writeShort(t.getExtMsgAttr());
        extBody.writeShort(getJobNum());
        extBody.writeBytes(t.getDeviceId().getBytes());
        extBody.writeInt(tmp.readableBytes());  //这里tmp为消息体消息，写完消息头之后加上消息体
        extBody.writeBytes(tmp);
        // 扩展消息进行加密
        extBody.resetReaderIndex();
        byte[] ready = new byte[extBody.readableBytes()];
        extBody.readBytes(ready);
        extBody.writeBytes(CertUtil.sign(ready, 0, CertUtil.getPrivateKey(t.getKey(), t.getPwd())));
```

添加补充消息：
```
ByteBuf out = Unpooled.buffer(256);
            out.writeByte(0x13);// 0x13
            extend.resetReaderIndex();
            out.writeBytes(extend);
```

添加head头，计算分包数，生成List<ByteBuf>，最后遍历list逐条发送
主要实现：重写消息头，一致性msgId，添加其他数据
```
public List<ByteBuf> head(ByteBuf all, JT808Msg msg, io.netty.channel.Channel channel) {
        List<ByteBuf> ret = new ArrayList<ByteBuf>();
        int bodyLength = all.writerIndex();
        int packageNum = 1;
        if (bodyLength > 0) {
            packageNum = bodyLength / 1023 + (bodyLength % 1023 == 0 ? 0 : 1);
        }
        // Log.d("消息体长度，分包数", bodyLength + "-" + packageNum);
        all.resetReaderIndex();
        for (int i = 0; i < packageNum; i++) {
            ByteBuf tmp = Unpooled.buffer(1024 + 128);
            // 根据消息体属性
            StringBuffer lengthSB = new StringBuffer("00");
            if (packageNum > 1) {
                lengthSB.append("1");
            } else {
                lengthSB.append("0");
            }
            lengthSB.append("000");
            // 长度计算
            int readIndex = all.readableBytes();
            String binaryString = StringUtils.leftPad(
                    Integer.toBinaryString(readIndex > 1023 ? 1023 : readIndex % 1023), 10, "0");
            lengthSB.append(binaryString);

            // 消息头
            tmp.writeByte(128);
            tmp.writeShort(msg.getBaseHead().getMsgId());
            // 消息体属性
            // Log.d("消息体属性{}", lengthSB.toString());
            tmp.writeShort(Integer.parseInt(lengthSB.toString(), 2));
            // 手机号
            tmp.writeBytes(BCDUtils.str2Bcd(StringUtils.leftPad(msg.getBaseHead().getMobile(), 16, "0")));
            // 流水号
            int lineNo = getLineNum();

            msg.getBaseHead().setLineNum(lineNo);
            tmp.writeShort(lineNo);
            tmp.writeByte(0);
            if (packageNum > 1) {
                tmp.writeShort(packageNum);
                tmp.writeShort(i + 1);
            }
            //Log.d("{}-{}", "" + tmp.readableBytes() + all.readableBytes());
            if (i + 1 == packageNum) {
                all.readBytes(tmp, readIndex);
            } else {
                all.readBytes(tmp, 1023);
            }
            //Log.d("{}-{}", "" + tmp.readableBytes() + all.readableBytes());
            tmp.resetReaderIndex();
            // 生成校验位
            int check = tmp.readByte();
            while (tmp.isReadable()) {
                check = check ^ tmp.readByte();
            }
            tmp.writeByte(check);
            //Log.d("发送第{}包,流水号={}", "发送第" + i + 1 + "-" + lineNo);
            tmp.resetReaderIndex();
            // Log.d("基本消息头+体数据为\n{}\n-分包数{}", ByteBufUtil.prettyHexDump(tmp));
            ret.add(tmp);
        }
        return ret;
    }
```

发送消息：
```
                if (TcpClient.getInstance().channel != null && TcpClient.getInstance().channel.isOpen() && TcpClient.getInstance().channel.isActive()) {
                    this.channel.writeAndFlush(byteBuf).addListener(new ChannelFutureListener() {
                        @Override
                        public void operationComplete(ChannelFuture future) throws Exception {
                            MyLog.d("TcpClient", "数据发送成功" +msg.getBaseHead().getLineNum() + Integer.toHexString(msgId));
                        }
                    });
```


客户端上传json字符串：
```
        ReqObj req = new ReqObj();
        req.setMsgId("S01");
        req.setMsgBody("hello");
        String jsonReq = ServiceLocator.getGson().toJson(req);
		//将json字符串转换为ByteBuf
            ByteBuf byteBuf = Unpooled.copiedBuffer(jsonReq, CharsetUtil.UTF_8);
            if (this.channel != null && this.channel.isOpen() && this.channel.isActive()) {
                this.channel.writeAndFlush(byteBuf).addListener(new ChannelFutureListener() {
                    @Override
                    public void operationComplete(ChannelFuture future) throws Exception {
                        MyLog.d("LnTcpClient", "直连数据发送成功");
                    }
                });
            }
```

服务端解析json字符串：
```
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        // 由于我们配置的是 字节数组 编解码器，所以这里取到的用户发来的数据是 byte数组
        String data = (String) msg;
        ReqObj reqObj = gson.fromJson(data, ReqObj.class);
```

中国移动物联网平台JT/T808协议文档：
https://www.etsi.org/deliver/etsi_ts/102900_102999/102940/01.01.01_60/ts_102940v010101p.pdf

