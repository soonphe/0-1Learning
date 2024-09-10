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