# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Zookeeper
官方资源包：http://zookeeper.apache.com/
国内压缩包地址：https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.6.3/apache-zookeeper-3.6.3-bin.tar.gz

### brew 安装zookeeper
brew install zookeeper

启动文件： /usr/local/Cellar/zookeeper/3.4.10/bin/
配置文件： /usr/local/etc/zookeeper/

启动zookeeper：nohup zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &

查看zookeeper启动状态： ps -ef|grep zookeeper

### 单机环境搭建
操作
```
1. 下载压缩包

2.tar –xvf 命令将包解压到你想要的路径：
tar -zxf apache-zookeeper-3.6.3-bin.tar.gz -C /usr/local

3.创建两个文件夹，路径如图：
mkdir data
mkdir logs

4.你可以在conf文件里面复制zoo_sample.cfg,并重命名为zoo.cfg,或者创建一个文件命名为zoo.cfg
tickTime=2000
dataDir=/usr/local/apache-zookeeper-3.6.3-bin/data
dataLogDir=/usr/local/apache-zookeeper-3.6.3-bin/logs
clientPort=2181
dataDir和dataLogDir的路径是你创建的文件夹的路径

5.命令
./zkServer.sh start
./zkServer.sh stop
./zkServer.sh restart
./zkServer.sh status

./zkServer.sh start-foreground查看相关启动信息
./zkCli.sh -server 127.0.0.1:2185  利用客户端测试连接（ls 还能查看所有节点）

6.启动前利用命令：netstat -tunlp|grep 端口号  查看端口有没有被占用，有的话kill掉，或者改用其他端口
```

常见问题
- 可能端口被占用，端口被占用就换
- 也有可能因为jdk问题，jdk的问题可能是你的环境变量没配好
- 连接zookeeper的时候要注意防火墙有没有开

### 集群环境搭建

操作步骤：
```
1. 新增myid文件
cd /opt
mkdir zk
vim myid
1
将1 写入文件，各机器依次递增

2.修改zoo.cfg配置
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg

dataDir=/opt/zk
# the port at which the clients will connect
clientPort=2181
server.1=192.168.161.224:2888:3888
server.2=192.168.161.44:2888:3888
server.3=192.168.161.112:2888:3888
```

### 配置文件说明
```
# The number of milliseconds of each tick       心跳间隔 毫秒每次
tickTime=2000
# The number of ticks that the initial          LF初始通信时限（集群中的follower服务器(F)与leader服务器(L)之间 初始连接 时能容忍的最多心跳数（tickTime的数量））
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between     LF同步通信时限（集群中的follower服务器(F)与leader服务器(L)之间 请求和应答 之间能容忍的最多心跳数（tickTime的数量）。）
# sending a request and getting anacknowledgement
syncLimit=5
# the directory where the snapshot isstored.    数据文件和日志文件地址
dataDir=/usr/local/apache-zookeeper-3.6.3-bin/data
dataLogDir=/usr/local/apache-zookeeper-3.6.3-bin/data
# the port at which the clients willconnect     客户端连接的端口
clientPort=2181
```


### java如何接入zookeeper
zookeeper 常用的3种java客户端：
- zookeeper原生Java API
- ZkClient
- Apache curator(推荐使用)

#### 1. zookeeper原生Java API
Zookeeper客户端提供了基本的操作，比如，创建会话、创建节点、读取节点、更新数据、删除节点和检查节点是否存在等。但对于开发人员来说，Zookeeper提供的基本操纵还是有一些不足之处。

Zookeeper API不足之处

（1）Session超时之后没有实现重连机制，需要手动操作；
（2）Watcher注册是一次性的，每次触发之后都需要重新进行注册；
（3）不支持递归创建节点；
（4）异常处理繁琐，Zookeeper提供了很多异常，对于开发人员来说可能根本不知道该如何处理这些异常信息；
（5）只提供了简单的byte[]数组的接口，没有提供针对对象级别的序列化；
（6）创建节点时如果节点存在抛出异常，需要自行检查节点是否存在；
（7）删除节点无法实现级联删除；

基于以上原因，直接使用Zookeeper原生API的人并不多。

#### 2、ZkClient
```
<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.10</version>
</dependency>
```
ZkClient是一个开源客户端，在Zookeeper原生API接口的基础上进行了包装，更便于开发人员使用。解决如下问题：

1）session会话超时重连
2）解决Watcher反复注册
3）简化API开发

虽然 ZkClient 对原生 API 进行了封装，但也有它自身的不足之处：

几乎没有参考文档；
异常处理简化（抛出RuntimeException）；
重试机制比较难用；
没有提供各种使用场景的实现；


#### 3、Apache Curator
Curator是Netflix公司开源的一套Zookeeper客户端框架，和ZkClient一样，解决了非常底层的细节开发工作，包括连接重连、反复注册Watcher和NodeExistsException异常等。目前已经成为 Apache 的顶级项目。

其特点：

Apache 的开源项目
解决Watch注册一次就会失效的问题
提供一套Fluent风格的 API 更加简单易用
提供更多解决方案并且实现简单，例如：分布式锁
提供常用的ZooKeeper工具类
编程风格更舒服
除此之外，Curator中还提供了Zookeeper各种应用场景（Recipe，如共享锁服务、Master选举机制和分布式计算器等）的抽象封装。

### Apache Curator介绍
Curator 说明
官网：http://curator.apache.org/index.html

Apache Curator是Apache ZooKeeper的Java / JVM客户端库，Apache ZooKeeper是一种分布式协调服务。它包括一个高级API框架和实用程序，使Apache ZooKeeper更容易和更可靠。它还包括常见用例和扩展（如服务发现和Java 8异步DSL）的配方。

**Curator项目组件 （下载官方源码就可以看到以下组件）**

|组名名称|	用途|
|---|---|
|Client|	Zookeeper 客户端的封装，用于取代原生的 Zookeeper 客户端， 提供了一些底层处理和相关的工具方法。|
|Framework|	简化 Zookeeper 高级功能的使用，并增加了一些新的功能，比如 Zookeeper 集群连接、重试等。|
|Recipes|	Zookeeper 所有的典型应用场景的实现（除了两阶段提交外） ，该组件依赖 Client 和 Framework 。 包括 监听、各种分布式锁（可重入锁、排他锁、共享锁、信号锁等）、缓存、队列、选举、分布式 atomic（分布式计数器）、分布式Barrier 等等。|
|Utilities|	为 Zookeeper 提供的各种工具类。|
|Errors|	Curator 异常处理, 连接, 恢复等。|


**Maven依赖**
> 地址:https://search.maven.org/search?q=org.apache.curator

|GroupID/Org|	ArtifactID/Name|	描述|
|---|---|---|
|org.apache.curator|	curator-recipes|	所有典型应用场景。需要依赖client和framework，需设置自动获取依赖。|
|org.apache.curator|	curator-framework|	同组件中framework介绍。|
|org.apache.curator|	curator-client|	同组件中client介绍。|
|org.apache.curator|	curator-test|	包含TestingServer、TestingCluster和一些测试工具。|
|org.apache.curator|	curator-examples|	各种使用Curator特性的案例。|
|org.apache.curator|	curator-x-discovery|	在framework上构建的服务发现实现。|
|org.apache.curator|	curator-x-discoveryserver|	可以和Curator Discovery一起使用的RESTful服务器。|
|org.apache.curator|	curator-x-rpc|	Curator framework和recipes非java环境的桥接。|

#### 依赖接入：
```
        <!-- Zookeeper 客户端的封装，用于取代原生的 Zookeeper 客户端， 提供了一些底层处理和相关的工具方法。 -->
		<dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-client</artifactId>
			<version>5.1.0</version>
		</dependency>
        <!-- 简化 Zookeeper 高级功能的使用，并增加了一些新的功能，比如 Zookeeper 集群连接、重试等。 -->
		<dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-framework</artifactId>
			<version>5.1.0</version>
		</dependency>
		<!-- Zookeeper 所有的典型应用场景的实现（除了两阶段提交外） ，该组件依赖 Client 和 Framework 。 -->
		<dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-recipes</artifactId>
			<version>5.1.0</version>
		</dependency>
```

#### 1.使用静态工程方法创建客户端

```
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
CuratorFramework client =
CuratorFrameworkFactory.newClient(
                        connectionInfo,
                        5000,
                        3000,
                        retryPolicy);
```
newClient静态工厂方法包含四个主要参数：

参数名	说明
connectionString	服务器列表，格式host1:port1,host2:port2,...
retryPolicy	重试策略,内建有四种重试策略,也可以自行实现RetryPolicy接口
sessionTimeoutMs	会话超时时间，单位毫秒，默认60000ms
connectionTimeoutMs	连接创建超时时间，单位毫秒，默认60000ms

#### 2.使用Fluent风格的Api创建会话
核心参数变为流式设置，一个列子如下：
```
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        CuratorFramework client =
        CuratorFrameworkFactory.builder()
                .connectString(connectionInfo)
                .sessionTimeoutMs(5000)
                .connectionTimeoutMs(5000)
                .retryPolicy(retryPolicy)
                .build();
```
#### 3.创建包含隔离命名空间的会话
为了实现不同的Zookeeper业务之间的隔离，需要为每个业务分配一个独立的命名空间（NameSpace），即指定一个Zookeeper的根路径（官方术语：为Zookeeper添加“Chroot”特性）。例如（下面的例子）当客户端指定了独立命名空间为“/base”，那么该客户端对Zookeeper上的数据节点的操作都是基于该目录进行的。通过设置Chroot可以将客户端应用与Zookeeper服务端的一课子树相对应，在多个应用共用一个Zookeeper集群的场景下，这对于实现不同应用之间的相互隔离十分有意义。
```
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        CuratorFramework client =
        CuratorFrameworkFactory.builder()
                .connectString(connectionInfo)
                .sessionTimeoutMs(5000)
                .connectionTimeoutMs(5000)
                .retryPolicy(retryPolicy)
                .namespace("base")
                .build();
```

#### 启动客户端
当创建会话成功，得到client的实例然后可以直接调用其start( )方法：
```
client.start();
```

#### 数据节点操作
- 创建数据节点
Zookeeper的节点创建模式：

PERSISTENT：持久化
PERSISTENT_SEQUENTIAL：持久化并且带序列号
EPHEMERAL：临时
EPHEMERAL_SEQUENTIAL：临时并且带序列号

- 创建一个节点，初始内容为空
```
client.create().forPath("path");
```
注意：如果没有设置节点属性，节点创建模式默认为持久化节点，内容默认为空

- 创建一个节点，附带初始化内容
```
client.create().forPath("path","init".getBytes());
```
- 创建一个节点，指定创建模式（临时节点），内容为空
```
client.create().withMode(CreateMode.EPHEMERAL).forPath("path");
```
- 创建一个节点，指定创建模式（临时节点），附带初始化内容
```
client.create().withMode(CreateMode.EPHEMERAL).forPath("path","init".getBytes());
```
- 创建一个节点，指定创建模式（临时节点），附带初始化内容，并且自动递归创建父节点
```
client.create()
.creatingParentContainersIfNeeded()
.withMode(CreateMode.EPHEMERAL)
.forPath("path","init".getBytes());
```
这个creatingParentContainersIfNeeded()接口非常有用，因为一般情况开发人员在创建一个子节点必须判断它的父节点是否存在，如果不存在直接创建会抛出NoNodeException，使用creatingParentContainersIfNeeded()之后Curator能够自动递归创建所有所需的父节点。

- 删除一个节点
```
client.delete().forPath("path");
```
注意，此方法只能删除叶子节点，否则会抛出异常。

- 删除一个节点，并且递归删除其所有的子节点
```
client.delete().deletingChildrenIfNeeded().forPath("path");
```
- 删除一个节点，强制指定版本进行删除
```
client.delete().withVersion(10086).forPath("path");
```
- 删除一个节点，强制保证删除
```
client.delete().guaranteed().forPath("path");
```
guaranteed()接口是一个保障措施，只要客户端会话有效，那么Curator会在后台持续进行删除操作，直到删除节点成功。

注意：上面的多个流式接口是可以自由组合的，例如：
```
client.delete().guaranteed().deletingChildrenIfNeeded().withVersion(10086).forPath("path");
```
- 读取一个节点的数据内容
```
client.getData().forPath("path");
```
注意，此方法返的返回值是byte[ ];

- 读取一个节点的数据内容，同时获取到该节点的stat
```
Stat stat = new Stat();
client.getData().storingStatIn(stat).forPath("path");
```
- 更新一个节点的数据内容
```
client.setData().forPath("path","data".getBytes());
```
注意：该接口会返回一个Stat实例

- 更新一个节点的数据内容，强制指定版本进行更新
```
client.setData().withVersion(10086).forPath("path","data".getBytes());
```
- 检查节点是否存在
```
client.checkExists().forPath("path");
```
注意：该方法返回一个Stat实例，用于检查ZNode是否存在的操作. 可以调用额外的方法(监控或者后台处理)并在最后调用forPath( )指定要操作的ZNode

- 获取某个节点的所有子节点路径
```
client.getChildren().forPath("path");
```
注意：该方法的返回值为List<String>,获得ZNode的子节点Path列表。 可以调用额外的方法(监控、后台处理或者获取状态watch, background or get stat) 并在最后调用forPath()指定要操作的父ZNode

- 事务
CuratorFramework的实例包含inTransaction( )接口方法，调用此方法开启一个ZooKeeper事务. 可以复合create, setData, check, and/or delete 等操作然后调用commit()作为一个原子操作提交。一个例子如下：
```
client.inTransaction().check().forPath("path")
.and()
.create().withMode(CreateMode.EPHEMERAL).forPath("path","data".getBytes())
.and()
.setData().withVersion(10086).forPath("path","data2".getBytes())
.and()
.commit();
```

- 异步接口
上面提到的创建、删除、更新、读取等方法都是同步的，Curator提供异步接口，引入了BackgroundCallback接口用于处理异步接口调用之后服务端返回的结果信息。BackgroundCallback接口中一个重要的回调值为CuratorEvent，里面包含事件类型、响应吗和节点的详细信息。

CuratorEventType

|事件类型|	对应CuratorFramework实例的方法|
|---|---|
|CREATE|	#create()|
|DELETE|	#delete()|
|EXISTS|	#checkExists()|
|GET_DATA|	#getData()|
|SET_DATA|	#setData()|
|CHILDREN|	#getChildren()|
|SYNC|	#sync(String,Object)|
|GET_ACL|	#getACL()|
|SET_ACL|	#setACL()|
|WATCHED|	#Watcher(Watcher)|
|CLOSING|	#close()|

响应码(#getResultCode())

|响应码|	意义|
|---|---|
|0|	OK，即调用成功|
|-4|	ConnectionLoss，即客户端与服务端断开连接|
|-110|	NodeExists，即节点已经存在|
|-112|	SessionExpired，即会话过期|

一个异步创建节点的例子如下：
```
Executor executor = Executors.newFixedThreadPool(2);
client.create()
.creatingParentsIfNeeded()
.withMode(CreateMode.EPHEMERAL)
.inBackground((curatorFramework, curatorEvent) -> {      System.out.println(String.format("eventType:%s,resultCode:%s",curatorEvent.getType(),curatorEvent.getResultCode()));
},executor)
.forPath("path");
```
注意：如果#inBackground()方法不指定executor，那么会默认使用Curator的EventThread去进行异步处理。

#### Curator食谱(高级特性)
提醒：首先你必须添加curator-recipes依赖，下文仅仅对recipes一些特性的使用进行解释和举例，不打算进行源码级别的探讨
重要提醒：强烈推荐使用ConnectionStateListener监控连接的状态，当连接状态为LOST，curator-recipes下的所有Api将会失效或者过期，尽管后面所有的例子都没有使用到ConnectionStateListener。

##### 缓存
Zookeeper原生支持通过注册Watcher来进行事件监听，但是开发者需要反复注册(Watcher只能单次注册单次使用)。Cache是Curator中对事件监听的包装，可以看作是对事件监听的本地缓存视图，能够自动为开发者处理反复注册监听。Curator提供了三种Watcher(Cache)来监听结点的变化。

Path Cache
Path Cache用来监控一个ZNode的子节点. 当一个子节点增加， 更新，删除时， Path Cache会改变它的状态， 会包含最新的子节点， 子节点的数据和状态，而状态的更变将通过PathChildrenCacheListener通知。

实际使用时会涉及到四个类：

PathChildrenCache
PathChildrenCacheEvent
PathChildrenCacheListener
ChildData
通过下面的构造函数创建Path Cache:

public PathChildrenCache(CuratorFramework client, String path, boolean cacheData)
想使用cache，必须调用它的start方法，使用完后调用close方法。 可以设置StartMode来实现启动的模式，

StartMode有下面几种：

NORMAL：正常初始化。
BUILD_INITIAL_CACHE：在调用start()之前会调用rebuild()。
POST_INITIALIZED_EVENT： 当Cache初始化数据后发送一个PathChildrenCacheEvent.Type#INITIALIZED事件
public void addListener(PathChildrenCacheListener listener)可以增加listener监听缓存的变化。

getCurrentData()方法返回一个List<ChildData>对象，可以遍历所有的子节点。

设置/更新、移除其实是使用client (CuratorFramework)来操作, 不通过PathChildrenCache操作：
```
public class PathCacheDemo {
 
    private static final String PATH = "/example/pathCache";
 
    public static void main(String[] args) throws Exception {
        TestingServer server = new TestingServer();
        CuratorFramework client = CuratorFrameworkFactory.newClient(server.getConnectString(), new ExponentialBackoffRetry(1000, 3));
        client.start();
        PathChildrenCache cache = new PathChildrenCache(client, PATH, true);
        cache.start();
        PathChildrenCacheListener cacheListener = (client1, event) -> {
            System.out.println("事件类型：" + event.getType());
            if (null != event.getData()) {
                System.out.println("节点数据：" + event.getData().getPath() + " = " + new String(event.getData().getData()));
            }
        };
        cache.getListenable().addListener(cacheListener);
        client.create().creatingParentsIfNeeded().forPath("/example/pathCache/test01", "01".getBytes());
        Thread.sleep(10);
        client.create().creatingParentsIfNeeded().forPath("/example/pathCache/test02", "02".getBytes());
        Thread.sleep(10);
        client.setData().forPath("/example/pathCache/test01", "01_V2".getBytes());
        Thread.sleep(10);
        for (ChildData data : cache.getCurrentData()) {
            System.out.println("getCurrentData:" + data.getPath() + " = " + new String(data.getData()));
        }
        client.delete().forPath("/example/pathCache/test01");
        Thread.sleep(10);
        client.delete().forPath("/example/pathCache/test02");
        Thread.sleep(1000 * 5);
        cache.close();
        client.close();
        System.out.println("OK!");
    }
}
```
注意：如果new PathChildrenCache(client, PATH, true)中的参数cacheData值设置为false，则示例中的event.getData().getData()、data.getData()将返回null，cache将不会缓存节点数据。

注意：示例中的Thread.sleep(10)可以注释掉，但是注释后事件监听的触发次数会不全，这可能与PathCache的实现原理有关，不能太过频繁的触发事件！

Node Cache
Node Cache与Path Cache类似，Node Cache只是监听某一个特定的节点。它涉及到下面的三个类：

NodeCache - Node Cache实现类
NodeCacheListener - 节点监听器
ChildData - 节点数据
注意：使用cache，依然要调用它的start()方法，使用完后调用close()方法。

getCurrentData()将得到节点当前的状态，通过它的状态可以得到当前的值。
```
public class NodeCacheDemo {
 
    private static final String PATH = "/example/cache";
 
    public static void main(String[] args) throws Exception {
        TestingServer server = new TestingServer();
        CuratorFramework client = CuratorFrameworkFactory.newClient(server.getConnectString(), new ExponentialBackoffRetry(1000, 3));
        client.start();
        client.create().creatingParentsIfNeeded().forPath(PATH);
        final NodeCache cache = new NodeCache(client, PATH);
        NodeCacheListener listener = () -> {
            ChildData data = cache.getCurrentData();
            if (null != data) {
                System.out.println("节点数据：" + new String(cache.getCurrentData().getData()));
            } else {
                System.out.println("节点被删除!");
            }
        };
        cache.getListenable().addListener(listener);
        cache.start();
        client.setData().forPath(PATH, "01".getBytes());
        Thread.sleep(100);
        client.setData().forPath(PATH, "02".getBytes());
        Thread.sleep(100);
        client.delete().deletingChildrenIfNeeded().forPath(PATH);
        Thread.sleep(1000 * 2);
        cache.close();
        client.close();
        System.out.println("OK!");
    }
}
```
注意：示例中的Thread.sleep(10)可以注释，但是注释后事件监听的触发次数会不全，这可能与NodeCache的实现原理有关，不能太过频繁的触发事件！

注意：NodeCache只能监听一个节点的状态变化。

Tree Cache
Tree Cache可以监控整个树上的所有节点，类似于PathCache和NodeCache的组合，主要涉及到下面四个类：

TreeCache - Tree Cache实现类
TreeCacheListener - 监听器类
TreeCacheEvent - 触发的事件类
ChildData - 节点数据
```
public class TreeCacheDemo {
 
    private static final String PATH = "/example/cache";
 
    public static void main(String[] args) throws Exception {
        TestingServer server = new TestingServer();
        CuratorFramework client = CuratorFrameworkFactory.newClient(server.getConnectString(), new ExponentialBackoffRetry(1000, 3));
        client.start();
        client.create().creatingParentsIfNeeded().forPath(PATH);
        TreeCache cache = new TreeCache(client, PATH);
        TreeCacheListener listener = (client1, event) ->
                System.out.println("事件类型：" + event.getType() +
                        " | 路径：" + (null != event.getData() ? event.getData().getPath() : null));
        cache.getListenable().addListener(listener);
        cache.start();
        client.setData().forPath(PATH, "01".getBytes());
        Thread.sleep(100);
        client.setData().forPath(PATH, "02".getBytes());
        Thread.sleep(100);
        client.delete().deletingChildrenIfNeeded().forPath(PATH);
        Thread.sleep(1000 * 2);
        cache.close();
        client.close();
        System.out.println("OK!");
    }
}
```
注意：在此示例中没有使用Thread.sleep(10)，但是事件触发次数也是正常的。

注意：TreeCache在初始化(调用start()方法)的时候会回调TreeCacheListener实例一个事TreeCacheEvent，而回调的TreeCacheEvent对象的Type为INITIALIZED，ChildData为null，此时event.getData().getPath()很有可能导致空指针异常，这里应该主动处理并避免这种情况。


Leader选举
在分布式计算中， leader elections是很重要的一个功能， 这个选举过程是这样子的： 指派一个进程作为组织者，将任务分发给各节点。 在任务开始前， 哪个节点都不知道谁是leader(领导者)或者coordinator(协调者). 当选举算法开始执行后， 每个节点最终会得到一个唯一的节点作为任务leader. 除此之外， 选举还经常会发生在leader意外宕机的情况下，新的leader要被选举出来。

在zookeeper集群中，leader负责写操作，然后通过Zab协议实现follower的同步，leader或者follower都可以处理读操作。

Curator 有两种leader选举的recipe,分别是LeaderSelector和LeaderLatch。

前者是所有存活的客户端不间断的轮流做Leader，大同社会。后者是一旦选举出Leader，除非有客户端挂掉重新触发选举，否则不会交出领导权。某党?

LeaderLatch
LeaderLatch有两个构造函数：
```
public LeaderLatch(CuratorFramework client, String latchPath)
public LeaderLatch(CuratorFramework client, String latchPath,  String id)
```

LeaderLatch的启动：

leaderLatch.start( );

一旦启动，LeaderLatch会和其它使用相同latch path的其它LeaderLatch交涉，然后其中一个最终会被选举为leader，可以通过hasLeadership方法查看LeaderLatch实例是否leader：

leaderLatch.hasLeadership( ); //返回true说明当前实例是leader

类似JDK的CountDownLatch， LeaderLatch在请求成为leadership会block(阻塞)，一旦不使用LeaderLatch了，必须调用close方法。 如果它是leader,会释放leadership， 其它的参与者将会选举一个leader。

```
public void await() throws InterruptedException,EOFException
/*Causes the current thread to wait until this instance acquires leadership
unless the thread is interrupted or closed.*/
public boolean await(long timeout,TimeUnit unit)throws InterruptedException
```

异常处理： LeaderLatch实例可以增加ConnectionStateListener来监听网络连接问题。 当 SUSPENDED 或 LOST 时, leader不再认为自己还是leader。当LOST后连接重连后RECONNECTED,LeaderLatch会删除先前的ZNode然后重新创建一个。LeaderLatch用户必须考虑导致leadership丢失的连接问题。 强烈推荐你使用ConnectionStateListener。

一个LeaderLatch的使用例子：
```java
public class LeaderLatchDemo extends BaseConnectionInfo {
    protected static String PATH = "/francis/leader";
    private static final int CLIENT_QTY = 10;
 
 
    public static void main(String[] args) throws Exception {
        List<CuratorFramework> clients = Lists.newArrayList();
        List<LeaderLatch> examples = Lists.newArrayList();
        TestingServer server=new TestingServer();
        try {
            for (int i = 0; i < CLIENT_QTY; i++) {
                CuratorFramework client
                        = CuratorFrameworkFactory.newClient(server.getConnectString(), new ExponentialBackoffRetry(20000, 3));
                clients.add(client);
                LeaderLatch latch = new LeaderLatch(client, PATH, "Client #" + i);
                latch.addListener(new LeaderLatchListener() {
 
                    @Override
                    public void isLeader() {
                        // TODO Auto-generated method stub
                        System.out.println("I am Leader");
                    }
 
                    @Override
                    public void notLeader() {
                        // TODO Auto-generated method stub
                        System.out.println("I am not Leader");
                    }
                });
                examples.add(latch);
                client.start();
                latch.start();
            }
            Thread.sleep(10000);
            LeaderLatch currentLeader = null;
            for (LeaderLatch latch : examples) {
                if (latch.hasLeadership()) {
                    currentLeader = latch;
                }
            }
            System.out.println("current leader is " + currentLeader.getId());
            System.out.println("release the leader " + currentLeader.getId());
            currentLeader.close();
 
            Thread.sleep(5000);
 
            for (LeaderLatch latch : examples) {
                if (latch.hasLeadership()) {
                    currentLeader = latch;
                }
            }
            System.out.println("current leader is " + currentLeader.getId());
            System.out.println("release the leader " + currentLeader.getId());
        } finally {
            for (LeaderLatch latch : examples) {
                if (null != latch.getState())
                CloseableUtils.closeQuietly(latch);
            }
            for (CuratorFramework client : clients) {
                CloseableUtils.closeQuietly(client);
            }
        }
    }
}
```
可以添加test module的依赖方便进行测试，不需要启动真实的zookeeper服务端：
```
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-test</artifactId>
            <version>2.12.0</version>
        </dependency>
```
首先我们创建了10个LeaderLatch，启动后它们中的一个会被选举为leader。 因为选举会花费一些时间，start后并不能马上就得到leader。
通过hasLeadership查看自己是否是leader， 如果是的话返回true。
可以通过.getLeader().getId()可以得到当前的leader的ID。
只能通过close释放当前的领导权。
await是一个阻塞方法， 尝试获取leader地位，但是未必能上位。

**LeaderSelector**

LeaderSelector使用的时候主要涉及下面几个类：

LeaderSelector
LeaderSelectorListener
LeaderSelectorListenerAdapter
CancelLeadershipException
核心类是LeaderSelector，它的构造函数如下：

```java
public LeaderSelector(CuratorFramework client, String mutexPath,LeaderSelectorListener listener)
public LeaderSelector(CuratorFramework client, String mutexPath, ThreadFactory threadFactory, Executor executor, LeaderSelectorListener listener)
```
类似LeaderLatch,LeaderSelector必须start: leaderSelector.start(); 一旦启动，当实例取得领导权时你的listener的takeLeadership()方法被调用。而takeLeadership()方法只有领导权被释放时才返回。 当你不再使用LeaderSelector实例时，应该调用它的close方法。

异常处理 LeaderSelectorListener类继承ConnectionStateListener。LeaderSelector必须小心连接状态的改变。如果实例成为leader, 它应该响应SUSPENDED 或 LOST。 当 SUSPENDED 状态出现时， 实例必须假定在重新连接成功之前它可能不再是leader了。 如果LOST状态出现， 实例不再是leader， takeLeadership方法返回。

重要: 推荐处理方式是当收到SUSPENDED 或 LOST时抛出CancelLeadershipException异常.。这会导致LeaderSelector实例中断并取消执行takeLeadership方法的异常.。这非常重要， 你必须考虑扩展LeaderSelectorListenerAdapter. LeaderSelectorListenerAdapter提供了推荐的处理逻辑。

下面的一个例子摘抄自官方：
```java
public class LeaderSelectorAdapter extends LeaderSelectorListenerAdapter implements Closeable {
    private final String name;
    private final LeaderSelector leaderSelector;
    private final AtomicInteger leaderCount = new AtomicInteger();
 
    public LeaderSelectorAdapter(CuratorFramework client, String path, String name) {
        this.name = name;
        leaderSelector = new LeaderSelector(client, path, this);
        leaderSelector.autoRequeue();
    }
 
    public void start() throws IOException {
        leaderSelector.start();
    }
 
    @Override
    public void close() throws IOException {
        leaderSelector.close();
    }
 
    @Override
    public void takeLeadership(CuratorFramework client) throws Exception {
        final int waitSeconds = (int) (5 * Math.random()) + 1;
        System.out.println(name + " is now the leader. Waiting " + waitSeconds + " seconds...");
        System.out.println(name + " has been leader " + leaderCount.getAndIncrement() + " time(s) before.");
        try {
            Thread.sleep(TimeUnit.SECONDS.toMillis(waitSeconds));
        } catch (InterruptedException e) {
            System.err.println(name + " was interrupted.");
            Thread.currentThread().interrupt();
        } finally {
            System.out.println(name + " relinquishing leadership.\n");
        }
    }
}
```

你可以在takeLeadership进行任务的分配等等，并且不要返回，如果你想要要此实例一直是leader的话可以加一个死循环。调用 leaderSelector.autoRequeue();保证在此实例释放领导权之后还可能获得领导权。 在这里我们使用AtomicInteger来记录此client获得领导权的次数， 它是”fair”， 每个client有平等的机会获得领导权。
```java
public class LeaderSelectorDemo {
 
    protected static String PATH = "/francis/leader";
    private static final int CLIENT_QTY = 10;
 
 
    public static void main(String[] args) throws Exception {
        List<CuratorFramework> clients = Lists.newArrayList();
        List<LeaderSelectorAdapter> examples = Lists.newArrayList();
        TestingServer server = new TestingServer();
        try {
            for (int i = 0; i < CLIENT_QTY; i++) {
                CuratorFramework client
                        = CuratorFrameworkFactory.newClient(server.getConnectString(), new ExponentialBackoffRetry(20000, 3));
                clients.add(client);
                LeaderSelectorAdapter selectorAdapter = new LeaderSelectorAdapter(client, PATH, "Client #" + i);
                examples.add(selectorAdapter);
                client.start();
                selectorAdapter.start();
            }
            System.out.println("Press enter/return to quit\n");
            new BufferedReader(new InputStreamReader(System.in)).readLine();
        } finally {
            System.out.println("Shutting down...");
            for (LeaderSelectorAdapter exampleClient : examples) {
                CloseableUtils.closeQuietly(exampleClient);
            }
            for (CuratorFramework client : clients) {
                CloseableUtils.closeQuietly(client);
            }
            CloseableUtils.closeQuietly(server);
        }
    }
}
```
对比可知，LeaderLatch必须调用close()方法才会释放领导权，而对于LeaderSelector，通过LeaderSelectorListener可以对领导权进行控制， 在适当的时候释放领导权，这样每个节点都有可能获得领导权。从而，LeaderSelector具有更好的灵活性和可控性，建议有LeaderElection应用场景下优先使用LeaderSelector。


#### 分布式锁
实现代码：
```
public class Test{

	public static void main(String[] args) {
	    String zkConnectionString = "localhost:2181";
	    RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
	    CuratorFramework client = CuratorFrameworkFactory.newClient(zkConnectionString, retryPolicy);
	    client.start();
	
	    try {
	    
	        //创建分布式锁, 锁空间的根节点路径为  /curator/lock
	        InterProcessMutex lock = new InterProcessMutex(client, "/curator/lock");
	        if ( lock.acquire(1000, TimeUnit.SECONDS) )  {
	            try {
	                // do some work inside of the critical section here
	                System.out.println("do some work inside of the critical section here");
	            }finally{
	                //完成业务流程, 释放锁
	                lock.release();
	            }
	        }
	
	    } catch (Exception e) {
	        e.printStackTrace();
	    }
	}
}
```

#### 分布式计数器

#### 分布式队列

#### 分布式屏障—Barrier





