# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## kafka

### 简介：
Kafka是最初由Linkedin公司开发，用scala语言编写的，是一个分布式、支持分区的（partition）、多副本的（replica），基于zookeeper协调的分布式消息系统。 Kafka以集群的方式运行，天生就是分布式的，特色在于其负载均衡能力和处理性能、容错能力。

### 为什么要使用消息队列
1. 解耦
2. 最终一致性
3. 广播（只关注消息是否送到队列，至于谁订阅，那是下游的事情）
4. 错峰和流控

### 主流MQ比较

|特性	| activeMQ	| rabbitMQ	| rocketMQ	| kafka |
| ---- | ---- | ---- | ---- | ---- |
|单机吞吐量	|万/秒	|万/秒	|10万/秒	|10万/秒|
|topic对吞吐量的影响	|无	|无	|topic达到几百/几千个级别，吞吐量会有小幅下降；这是rocket的最大优势所以非常适用于支撑大批量topic场景	topic可以达到几十/几百个级别，吞吐量会有大幅下降  |kafka不适用大批量topic场景，除非加机器|
|时效性	|毫秒	|微秒 这是rabbit 最大优势，延迟低|	毫秒	|毫秒|
|可用性	|高。主从架构|	高。主从架构	|非常高。分布式。	|非常高。分布式。数据多副本，不会丢数据，不会不可用。|
|可靠性	|有较低概率丢失数据|	----	|经配置优化可达到0丢失|	经配置优化可达到0丢失|
|功能特性|	功能齐全，但已不怎么维护|	erlang开发，并发强，性能极好，延迟低|	MQ功能较为齐全，扩展好|	功能简单，主要用于大数据实时计算和日志采集，事实标准|

Active：官方社区现在对ActiveMQ 5.x维护越来越少，较少在大规模吞吐的场景中使用。

RabbitMQ：结合erlang语言本身的并发优势，性能较好，社区活跃度也比较高，但是不利于做二次开发和维护，適合数据量没有那么大，小公司优先选择功能比较完备的RabbitMQ。
erlang开发，很难去看懂源码，基本职能依赖于开源社区的快速维护和修复bug，不利于做二次开发和维护
RabbitMQ确实吞吐量会低一些，这是因为他做的实现机制比较重。
需要学习比较复杂的接口和协议，学习和维护成本较高。

RocketMQ：分开源和商业版，商业版为阿里云提供，已在国内企业级应用有一定市场。天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的
支持的客户端语言不多，目前是java及c++，其中c++不成熟；
社区活跃度一般；
没有在 mq 核心中去实现JMS等接口，有些系统要迁移需要修改大量代码 。

Kafka：开源消息系统，Kafka主要特点是基于Pull的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输
性能卓越，单机写入TPS约在百万条/秒，最大的优点，就是吞吐量高。
时效性：ms级
可用性：非常高，kafka是分布式的，通过控制能够保证所有消息被消费且仅被消费一次;
在日志领域比较成熟，被多家公司和多个开源项目使用；
功能支持：在大数据领域的实时计算以及日志采集被大规模使用
缺点：
Kafka单机超过64个队列/分区，Load会发生明显的飙高现象，队列越多，load越高，发送消息响应时间变长；
使用短轮询方式，实时性取决于轮询间隔时间；
消费失败不支持重试；
支持消息顺序，但是一台代理宕机后，就会产生消息乱序；
社区更新较慢

### brew安装kafka
- mac直接使用brew安装，安装过程会自动安装zookeeper（windows使用二进制包安装即可）
brew install kafka

- 安装位置：
/usr/local/Cellar/zookeeper
/usr/local/Cellar/kafka

- 配置文件位置：
/usr/local/etc/kafka/server.properties
/usr/local/etc/kafka/zookeeper.properties

- 启动zookeeper服务：
brew启动：brew services start zookeeper
sh启动：nohup zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &
windows则使用：./zookeeper-server-start.sh  config/server.properties &
如果没有zookeeper服务，则可以启动kafka自带的zookeeper服务：> nohup bin/zookeeper-server-start.sh /usr/local/etc/kafka/server.properties &

- 启动kafka服务：
brew启动：brew services start kafka
sh启动：nohup kafka-server-start /usr/local/etc/kafka/server.properties &
启动kafka，指定配置文件，后台启动并打印日志到 /usr/local/etc/kafka/kafka.log ：nohup kafka-server-start /usr/local/etc/kafka/server.properties > /usr/local/etc/kafka/kafka.log 2>&1 &

- 停止kafka和zookeeper服务（先关闭Kafka,等关闭完之后再关闭Zookeeper，否则，Kafka brokers无法关闭）
kafka-server-stop
zookeeper-server-stop

### kafka相关命令
- 查看创建的topic
对于 Kafka 2.7.x 及以前版本：kafka-topics --list --zookeeper localhost:2181
对于 Kafka 2.8.x 及更高版本：kafka-topics --list --bootstrap-server localhost:9092

- 创建topic（以下皆以3.7.0版本）（示例名称topic_a）
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic topic_a

- 查看指定topic 详细信息（分区数、副本数）
kafka-topics --bootstrap-server localhost:9092 --describe --topic topic_a

- 删除指定topic
kafka-topics --delete --bootstrap-server localhost:9092 --topic topic_a

- 修改分区数
kafka-topics --bootstrap-server localhost:9092 --alter --topic topic_a  --partitions 2   //修改为2个

- 终端1发送消息
kafka-console-producer --broker-list localhost:9092 --topic topic_a

- 终端2接受消息
kafka-console-consumer --bootstrap-server localhost:9092 --topic topic_a --from-beginning
--from-beginning 选项会从头开始消费消息，如果只想消费新的消息，可以去掉这个选项。
此时，在终端1中输入信息回车，终端2会立即显示在屏幕上面，如有结果输出，即安装成功

**命令补充拓展：**
- 控制台指定topic 的内部生产消息
  kafka-console-producer --broker-list localhost:9092 --topic topic_a

- 控制台指定topic 的内部消费消息
  kafka-console-consumer --bootstrap-server localhost:9092 --topic topic_a

- 查询指定topic内容：
  kafka-console-consumer --bootstrap-server localhost:9092 --topic topic_a --from-beginning

- 指定分区消费消息
  kafka-console-consumer --bootstrap-server localhost:9092 --topic topic_a --partition 1 --from-beginning

- 查询指定topic内容并使用grep匹配字符串：
kafka-console-consumer --bootstrap-server localhost:9092 --topic topic_a --from-beginning | grep '1234'

- 查看kafka的group列表
kafka-consumer-groups --bootstrap-server localhost:9092 --list

- 选择一个group(例如test)，查看其中的topic列表
kafka-consumer-groups --bootstrap-server localhost:9092 --group test --describe

- 选择一个topic，查看是否包含某个字段(用来查看kafka是否收到某条消息)
kafka-consumer-groups --bootstrap-server localhost:9092 --topic topic_a --from-beginning | grep '1234'

- 查看topic对应的log日志、index日志(需要传入日志文件地址，多个用逗号分隔，由于kafka是二进制保存日志文件的，因此需要下方命令才能查看)
kafka-run-class kafka.tools.DumpLogSegments --files /usr/local/var/lib/kafka-logs/topic_a-0/00000000000000000000.log  --print-data-log

- 查看topic对应的日志，并使用grep匹配字符串
kafka-run-class kafka.tools.DumpLogSegments --files /usr/local/var/lib/kafka-logs/topic_a-0/00000000000000000000.log  --print-data-log | grep '1234'

- 把kafka日志从二进制文件转存到txt中
kafka-run-class kafka.tools.DumpLogSegments --files /usr/local/var/lib/kafka-logs/topic_a-0/00000000000000000000.log --print-data-log > ~/Downloads/kafka-topic_a.txt

**kafka配置文件示例：**
```
############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=0

############################# Socket Server Settings #############################

# The address the socket server listens on. It will get the value returned from 
# java.net.InetAddress.getCanonicalHostName() if not configured.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://:9092

# Hostname and port the broker will advertise to producers and consumers. If not set, 
# it uses the value for "listeners" if configured.  Otherwise, it will use the value
# returned from java.net.InetAddress.getCanonicalHostName().
#advertised.listeners=PLAINTEXT://your.host.name:9092

# Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details
#listener.security.protocol.map=PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# The number of threads that the server uses for receiving requests from the network and sending responses to the network
num.network.threads=3

# The number of threads that the server uses for processing requests, which may include disk I/O
num.io.threads=8

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600


############################# Log Basics #############################

# A comma separated list of directories under which to store log files
log.dirs=/usr/local/var/lib/kafka-logs

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"
# For anything other than development testing, a value greater than 1 is recommended to ensure availability such as 3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to excessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log unless the remaining
# segments drop below log.retention.bytes. Functions independently of log.retention.hours.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000

############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=localhost:2181

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=18000


############################# Group Coordinator Settings #############################

# The following configuration specifies the time, in milliseconds, that the GroupCoordinator will delay the initial consumer rebalance.
# The rebalance will be further delayed by the value of group.initial.rebalance.delay.ms as new members join the group, up to a maximum of max.poll.interval.ms.
# The default value for this is 3 seconds.
# We override this to 0 here as it makes for a better out-of-the-box experience for development and testing.
# However, in production environments the default value of 3 seconds is more suitable as this will help to avoid unnecessary, and potentially expensive, rebalances during application startup.
group.initial.rebalance.delay.ms=0
```
连接相关配置说明：
- listeners配置的是kafka的tcp侦听ip地址；
- advertised.listeners配置的是kafka的broker ip。
- listeners不配置则会调用java.net.InetAddress.getCanonicalHostName()获取IP
- 在没有配置advertised.listeners的情况下，默认取值为kafka所在机器的主机名，端口与listeners中配置的端口一致。也就是kafka的broker ip是kafka所在机器的主机名。很多情况下，与kafka连接成功但无法正确生产消费的原因就是kafka的主机名无法被正确解析，最常见的就是kafka的主机名为localhost。
- 另外，kafka成功注册zookeeper后，会将broker ip写入到kafka中。这样kafka集群中的每个节点都能知道其他所有节点的broker ip。因此，kafka的客户端无论连接到集群的哪个节点上，都能正确获取到整个集群的元数据信息。

log日志配置说明：
- log.dirs=/usr/local/var/lib/kafka-logs  #日志存储地址
- log.retention.hours=168  #日志文件最少保存时长
- log.segment.bytes=1073741824  #Segment存储达到1G，就构建一个新的Segment

zookeeper配置说明：
- zookeeper.connect=localhost:2181
- zookeeper.connection.timeout.ms=18000

### kafka 底层存储——Segment
topic是逻辑上的概念，而partition是物理上的概念，即一个topic划分为多个partition，每个partition对应一个log文件，

为了防止log文件过大，将每个partition分为多个segment， 每个segment包括：.index文件、 .log文件、 .timeindex文件等，这些文件位于一个文件夹下，文件夹命名规则：topic名称+分区序号。

例：segment文件示例：topic为topic_a，选取segment topic_a-0文件

topic_a-0为一个segment文件夹，就是以topic和第一条消息的offset命名，其中包含00000000000000000000.index、00000000000000000000.log、​00000000000000000000.timeindex
```
-rw-r--r--  1 luoxiaosheng  admin    10M 10  9 14:38 00000000000000000000.index
-rw-r--r--  1 luoxiaosheng  admin   213B 10  9 14:41 00000000000000000000.log
-rw-r--r--  1 luoxiaosheng  admin    10M 10  9 14:38 00000000000000000000.timeindex
```

Segment设计思想：将一个分区中的数据划分到不同的文件当中去，每一个文件按顺序存储一部分数据，所有分区的第一个Segment就是用20位0来编号的Segment文件。该文件达到一定条件之后不再存，开始往下一个Segment文件进行存储。

这么做的意义：
1. 加快查询效率
   - 通过将分区的数据根据 Offset 划分到多个比较小的Segment文件，在检索数据时，可以根据Offset 快速定位数据所在的Segment
   - 加载单个Segment文件查询数据，可以提高查询效率

2. 删除数据时减少IO
   - 删除数据时，Kafka 以 Segment 为单位删除某个Segment的数据，避免一条一条删除，增加 IO 负载

Kafka将一个分区的文件是按照片段来存储的，一个片段的默认大小为1GB，可以在server.properties配置文件中修改片段大小，并且同时维护了index索引文件。

Segment 的划分规则：满足任何一个条件都会划分segment
1. 按照时间周期生成
```
#默认7天。如果达到7天，重新生成一个新的Segment
log.roll.hours = 168
```
2. 按照文件大小生成
```
#默认大小是1个G。如果一个Segment存储达到1G，就构建一个新的Segment
log.segment.bytes = 1073741824
```

kafka将一个partiton分割成很多个segment文件，segment下分为几部分
- .index/.timeindex 文件：索引文件，与log文件有一定的关联关系，.index为.timeindex为时间戳文件
- .log文件：真正存储数据的文件

其文件名的规则如下：
- 同一个segment下，index和log名字相同
- 下一个segment是上一个segment的lastOffset

### kafka 底层存储——Segment log和index文件

.log文件示例：
```
Starting offset: 0
baseOffset: 0 lastOffset: 0 count: 1 baseSequence: -1 lastSequence: -1 producerId: -1 producerEpoch: -1 partitionLeaderEpoch: 0 isTransactional: false isControl: false position: 0 CreateTime: 1665297701410 size: 70 magic: 2 compresscodec: NONE crc: 1160496349 isvalid: true
| offset: 0 isValid: true crc: null keySize: -1 valueSize: 2 CreateTime: 1665297701410 baseOffset: 0 lastOffset: 0 baseSequence: -1 lastSequence: -1 producerEpoch: -1 partitionLeaderEpoch: 0 batchSize: 70 magic: 2 compressType: NONE position: 0 sequence: -1 headerKeys: [] payload: 12
baseOffset: 1 lastOffset: 1 count: 1 baseSequence: -1 lastSequence: -1 producerId: -1 producerEpoch: -1 partitionLeaderEpoch: 0 isTransactional: false isControl: false position: 70 CreateTime: 1665297704669 size: 72 magic: 2 compresscodec: NONE crc: 4055451736 isvalid: true
| offset: 1 isValid: true crc: null keySize: -1 valueSize: 4 CreateTime: 1665297704669 baseOffset: 1 lastOffset: 1 baseSequence: -1 lastSequence: -1 producerEpoch: -1 partitionLeaderEpoch: 0 batchSize: 72 magic: 2 compressType: NONE position: 70 sequence: -1 headerKeys: [] payload: 3333
baseOffset: 2 lastOffset: 2 count: 1 baseSequence: -1 lastSequence: -1 producerId: -1 producerEpoch: -1 partitionLeaderEpoch: 0 isTransactional: false isControl: false position: 142 CreateTime: 1665297716279 size: 71 magic: 2 compresscodec: NONE crc: 155080469 isvalid: true
| offset: 2 isValid: true crc: null keySize: -1 valueSize: 3 CreateTime: 1665297716279 baseOffset: 2 lastOffset: 2 baseSequence: -1 lastSequence: -1 producerEpoch: -1 partitionLeaderEpoch: 0 batchSize: 71 magic: 2 compressType: NONE position: 142 sequence: -1 headerKeys: [] payload: 444
```
.log文件解读：
```
baseOffset：初始偏移量
lastOffset：消息文本结束偏移量
offset：表示的是相对于该分区的记录偏移量，指的是第几条记录，比如0代表第一条记录。
position：表示该记录相对于当前片段文件的偏移量。
CreateTime:记录创建的时间。
isvalid：记录是否有效。
keysize：表示key的长度。
valuesize：表示value的长度
magic：表示本次发布kafka服务程序协议版本号。
compresscodec：压缩工具。
producerId：生产者ID（用于幂等机制）。
sequence：消息的序列号（用于幂等机制）。
payload：表示具体的消息
...
```

.index文件示例
```
offset: 0 position: 0
```
.index文件解读
```
offset：消息在log文件中的相对offset，相对于当前index文件命名offset的偏移量，这样能确保offset的值占用的控件不会过大，因此能将offset的值控制在固定大小 
position：当前消息物理偏移量，对应log文件中的position
```
index文件有什么用呢？其实就是一个索引，记录了一条消息在log文件中的位置，查找消息的时候先从index获取位置，然后就可以定位到消息在log文件具体哪个地方
index采用了稀疏索引的方式去存储，不是每来一条消息就记录一个索引，而是当消息大于某个值的时候，就会记录一次索引，默认是4KB
稀疏存储也就是选取一些消息的offset以及position进行存储，因为如果把对应片段的所有消息的索引都存储，那么必然会占用大量的内存。

索引分为两类：一种是全量索引，一种是稀疏索引。
（1）全量索引指的是：每一条数据对应一条索引；
（2）稀疏索引指的是：部分数据有索引，有一部分数据是没有索引。
优点：减少了索引存储的数据量，加快索引的检索效率。

**index和log写文件流程**：
（举个例子，假设segment配置了10KB(log.segment.bytes)，每1KB记录一次索引(log.index.interval.bytes参数)，topic下只有1个partition，一个消息大小为43）

此时发送多个消息，直到发送了24个消息的时候，此时log文件大小为1032，由于每1KB创建一个索引，大于log.index.interval.bytes指定的值，需要增加索引
```
offset 0 position 0
offset 24 position 1032
```
当发送了238个消息后，这时log文件的大小为10234，由于配置的segment大小为10KB（10240），那么就是说，当下一个消息过来，必须要新建segment文件

新文件名以238结尾，这个238即为上一个segment文件最后一个消息的offset(这时候索引建立过程和第一个segment一样)
注意：index文件计算message在log中的位置的公式为offset-baseOffset
offset代表全局的message数，baseOffset为相对数量(即上面第二个segmemnt就为238)，那么相减后，index文件内容就和第一个segment一样了

总结一下segment的命名格式：index和log文件以当前segment第一条消息的offset命名（示例：设置segment大小设置为10K，包含2个segment：000.和238)
0000.log + 0000.index：存储第1条message到第238条message
0238.log + 0238.index：存储第239条message到第476条message，以上一个segment最后一条message的offset作为文件名
以此类推

**index和log读文件流程**
```
举个例子：文件如下，在log文件的定位到offset=600的record
Segment-0 [offset:0-521] 00000000000000000000.index  00000000000000000000.log
Segment-1 [offset:522-1044] 00000000000000000522.index  00000000000000000522.log
```
1. 根据目标offset定位segment文件（如要找到offset=600则定位到Segment-1 [offset:522-1044]中）
2. 找到小于等于目标offset的最大offset对应的索引项（index存的都是相对位移，且为稀疏索引，找到小于等于600的最大offset索引项）
3. 定位log文件
4. 向下遍历找到record

### kafka组成结构
1. 生产者Producer：将向Kafka topic发布消息的程序成为producers.
   - 消息发送过程
      1. 创建 ProducerRecord 对象（可以指定键或分区）。
      2. 键和值对象序列化成字节数组。
      3. producer发送的消息分发到不同的partition中，确定分区。如果指定了分区，以指定的优先。如果没有，分区器会根据键选择一个分区。
      4. 数据记录添加到记录批次。同一个批次里的所有消息会被发送到相同的主题和分区上。由独立的线程负责把这些记录批次发送到相应的 broker上。
   - 生产者三个核心配置：1. bootstrap.servers （发送到哪里）2. key.serializer （键序列化器）3. value.serializer （值序列化器）
   - 三种发送模式：1.发送并忘(不关心发送结果)，2.同步发送：根据返回的Future，同步等待结果；3.异步发送：通过回调的方式获取发送的结果。
```
ProducerRecord<String, String> record = new ProducerRecord<>("CustomerCountry", "Precision Products", "France");
 
try { 
    producer.send(record);
} catch (Exception e) { 
    e.printStackTrace();
}
```
ProducerRecord的三个构造参数是，topic，key，value

如果键值为 null，并且使用了默认的分区器，那么记录将使用轮询(Round Robin)算法发送到主题内各个可用的分区上。

如果键不为空，并且使用了默认的分区器，那么 Kafka 会对键进行散列，然后根据散列值把消息映射到特定的分区上。

在不改变主题分区数量的情况下，键与分区之间的映射才能保持不变。在创建主题的时候就把分区规划好，不要增加新分区。

2. 消费者Consumer：将订阅topics并消费消息的程序成为consumer.
   - 和Producer一样是逻辑上的概念，是Kafka实现单播和广播两种消息模型的手段
   - 同一个topic的数据，会广播给不同的group(消费者组)
   - 同一个group中的Consumer，只有一个worker能拿到这个数据。*对于同一个topic，每个group都可以拿到同样的所有数据，但是数据进入group后只能被其中的一个worker消费*
   - consumer接收数据的时候是按照group来接收，kafka确保每个partition只能同一个group中的其中一个consumer消费，如果想要重复消费，那么需要其他的组来消费
   - 例：1. 主题 T1 有4个分区，仅存在消费者 C1， C1 将收到全部4个分区的消息；在群组 G1 里新增消费者 C2，那么每个消费者将分别从两个分区接收消息；如果群组 G1 有 4 个消费者，那么每个消费者可以分配到一个分区；如果我们往群组里添加更多的消费者，超过主题的分区数量，那么有一部分消费者就会被闲置，不会接收到任何消息；5. 新增一个只包含一个消费者的群组 G2，那么这个消费者将从主题 T1 上接收所有的消息，与群组 G1 之间互不影响。
   - 核心配置：1. bootstrap.servers （从哪里获取）2. group.id（属于哪个群组）3. key.deserializer(键反序列化器) 4. value.deserializer(值反序列化器)
   - group内的worker可以使用多线程或多进程来实现，也可以将进程分散在多台机器上，worker的数量通常不超过partition的数量，且二者最好保持整数倍关系，因为Kafka在设计时假定了一个partition只能被一个worker消费（同一group内）

3. broker：由一个或多个kafka服务(server)组成，每个服务叫做一个broker. 是消息的代理，Producers往Brokers里面的指定Topic中写消息，Consumers从Brokers里面拉取指定Topic的消息，然后进行业务处理，broker在中间起到一个代理保存消息的中转站
   - kafka中broker节点的作用，Kafka使用zookeeper来维护集群成员的信息。每个broker都有一个唯一标识符，这个标识符可以在配置文件中指定，也可以自动生成。在broker启动时，它通过建立临时节点把自己的ID注册到zookeeper。kafka组件订阅broker在zookeeper上的注册路径，当有broker进入或退出集群时，这些组件就可以获得通知。
   - Kafka生产者从数据生成到broker：broker会在它所监听的每个端口上运行一个Acceptor线程，这个线程会创建一个连接，并把它交给Processor线程去处理。Processor线程（网络线程）的数量是可配置的。网络线程负责从客户端获取请求消息，把它们放进请求队列，请求消息放入请求队列后，IO线程会处理它们，并将处理结果放入响应队列，然后网络线程从响应队列中获取响应消息，把它们发送给客户端。 生产请求和获取请求都必须发送给分区的首领副本。如果broker收到一个针对特定分区的请求，而该分区的首领在另一个broker上，那么发送请求的客户端会收到一个“非分区首领”的错误响应。同样获取请求时也会有同样的要求。kafka客户端要自己负责把生产请求和获取请求发送给正确的broker上。

4. Topic：消息的主题或分类，kafka对消息保存时根据Topic进行归类，每条消息都属于且只属于一个topic，
    - 生产者在发送消息时、消费者在接收消息时都必须指定消息的topic。
    - 一个topic中可以包含多个partition（每个 Topic 至少有一个分区），同一 topic 下的不同分区包含的消息是不同的。
    - 为Topic创建分区时，分区数最好是broker数量的整数倍，这样才能是一个Topic的分区均匀的分布在整个Kafka集群中，如果不是整数倍，会造成分步不均匀的问题
   
5. Partition：
    - 每个partition(分区)在Kafka集群的若干服务(server)中都有副本/备份，这样这些持有副本的服务可以共同处理数据和请求，副本数量是可以配置的。
    - 每个分区有一个leader服务器，若干个followers服务器，leader负责处理消息的读和写，followers则去复制leader，如果leader down了，followers中的一台则会自动成为leader。
    - 可以理解为一个partition物理上对应一个文件夹。
    - 一个partition只分布于一个broker上（不考虑备份的情况下），每个partition都是一个有序队列，分为多个大小相等的segment（对用户透明）,每个segment对应一个文件，而segment文件由一条条不可变的记录组成（消息发出后就不可变更了），这里面的数据记录就是生产者发送的消息内容。
    - 每个消息在被添加到分区时，都会被分配一个 offset（称之为偏移量），它是消息在此分区中的唯一编号，kafka 通过 offset保证消息在分区内的顺序，offset 的顺序不跨分区，即 kafka只保证在同一个分区内的消息是有序的
    - 默认情况下，Kafka根据传递消息的key来进行分区的分配，即hash(key) % numPartitions，这就保证了相同key的消息一定会被路由到相同的分区。
    - 如果没有指定key，Kafka几乎就是随机找一个分区发送无key的消息，然后把这个分区号加入到缓存中以备后面直接使用——当然了，Kafka本身也会清空该缓存（默认每10分钟或每次请求topic元数据时）
    - Kafka的分区设计：
      - 生产者写作的并行性受分区数量的限制。
      - 消费者消费并行度的程度也受到消费的分区数量的限制。假设分区数为20，则最大并发消费消费者数为20。
    - 为什么Kafka不能支持更多分区 
      - 每个分区都存储整个消息数据。尽管每个分区都按顺序写入磁盘，但随着并发写入分区数量的增加，从操作系统的角度来看，写入变得随机。
      - 由于分散的数据文件，很难使用Linux IO组提交机制。
      - 更多分区需要更多打开文件句柄
      - 更多分区可能会增加不可用性
      - 更多分区可能会增加端到端延迟
      - 更多分区可能需要在客户端中有更多内存

- 补充：
无论是kafka集群，还是producer和consumer都依赖于zookeeper来保证系统可用性，zookeeper保存kafka集群的meta（元数据）信息（broker/consumer）。

### kafka消息发布模式
对于kafka来说，发布消息通常有两种模式：
* 点对点/队列模式（queuing）：
    * 一般基于拉取或轮询来接收消息，在队列模式中，可以有多个consumers同时在queue（队列）中侦听同一消息，但一条消息只会被一个consumer接收。
      既支持即发即弃的消息传送方式也支持同步请求/应答的消息传送方式。
* 发布/订阅模式(publish-subscribe)：
    * 既可以支持推送来接收消息，也可以通过拉取或轮询的形式来接收。发布到一个topic（主题）的消息可以被多个订阅者接收，解耦能力更强。

发布-订阅模式中消息被广播到所有的consumer，Consumers可以加入一个consumer 组，共同竞争一个topic，topic中的消息将被分发到组中的一个成员中。同一组中的consumer可以在不同的程序中，也可以在不同的机器上。

如果所有的consumer都在一个组中，这就成为了传统的队列模式，在各consumer中实现负载均衡。

如果所有的consumer都不在不同的组中，这就成为了发布-订阅模式，所有的消息都被分发到所有的consumer中。

更常见的是，每个topic都有若干数量的consumer组，每个组都是一个逻辑上的“订阅者”，为了容错和更好的稳定性，每个组由若干consumer组成。这其实就是一个发布-订阅模式，只不过订阅者是个组而不是单个consumer。


### kafka的message格式
一个Kafka的Message组成：
    * 一个固定长度的header和
    * 一个变长的消息体body组成

* header部分由一个字节的magic(文件格式)和四个字节的CRC32(用于判断body消息体是否正常)构成。
当magic的值为1的时候，会在magic和crc32之间多一个字节的数据：attributes(保存一些相关属性，比如是否压缩、压缩格式等等);如果magic的值为0，那么不存在attributes属性

* body是由N个字节构成的一个消息体，包含了具体的key/value消息

### kafka 为什么那么快
Cache Filesystem Cache PageCache缓存

顺序写 由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机写内存还要快。 
顺序写原理：每个topic有不同的分区，而每个分区下包含若干个只能追加写的提交日志：新消息被追加到文件的最末端。最直接的证明就是Kafka源码中只调用了FileChannel.write(ByteBuffer)，而没有调用过带offset参数的write方法，说明它不会执行随机写操作。
磁盘顺序读或写的速度400M/s，能够发挥磁盘最大的速度。 随机读写，磁盘速度慢的时候十几到几百K/s。

**Zero-copy 零拷贝技术减少拷贝次数**
零拷贝原理：零拷贝并不是不需要拷贝，而是减少不必要的拷贝次数。通常是说在IO读写过程中。
传统IO：
1、第一次：将磁盘文件，读取到操作系统内核缓冲区；
2、第二次：将内核缓冲区的数据，copy到application应用程序的buffer；
3、第三步：将application应用程序buffer中的数据，copy到socket网络发送缓冲区(属于操作系统内核的缓冲区)；
4、第四次：将socket buffer的数据，copy到网卡，由网卡进行网络传输。
第二次和第三次数据copy 其实在这种场景下没有什么帮助反而带来开销，数据可以直接从读缓冲区传输到套接字缓冲区，这也正是零拷贝出现的意义
数据直接在内核完成输入和输出，不需要拷贝到用户空间再写出去。 kafka数据写入磁盘前，数据先写到进程的内存空间。

**Batching of Messages 批量量处理**。合并小的请求，然后以流的方式进行交互，直顶网络上限。

**Pull 拉模式** 使用拉模式进行消息的获取消费，与消费端处理能力相符。

**mmap文件映射**
虚拟映射只支持文件； 在进程 的非堆内存开辟一块内存空间，和OS内核空间的一块内存进行映射，
kafka数据写入、是写入这块内存空间，但实际这块内存和OS内核内存有映射，也就是相当于写在内核内存空间了，且这块内核空间、内核直接能够访问到，直接落入磁盘。
这里，我们需要清楚的是：内核缓冲区的数据，flush就能完成落盘。

### 零拷贝技术
1. JVM向OS发出read()系统调用，触发上下文切换，从用户态切换到内核态。
2. 从外部存储（如硬盘）读取文件内容，通过直接内存访问（DMA）存入内核地址空间的缓冲区。
3. 将数据从内核缓冲区拷贝到用户空间缓冲区，read()系统调用返回，并从内核态切换回用户态。
4. JVM向OS发出write()系统调用，触发上下文切换，从用户态切换到内核态。
5. 将数据从用户缓冲区拷贝到内核中与目的地Socket关联的缓冲区。
6. 数据最终经由Socket通过DMA传送到硬件（如网卡）缓冲区，write()系统调用返回，并从内核态切换回用户态。

“基础的”零拷贝机制：
通过上面的分析可以看出，第2、3次拷贝（也就是从内核空间到用户空间的来回复制）是没有意义的，数据应该可以直接从内核缓冲区直接送入Socket缓冲区。零拷贝机制就实现了这一点。


### kafka中的 zookeeper 起到什么作用

可以不用zookeeper么，当然，不可以

zookeeper 是一个分布式的协调组件，早期版本的kafka用zk做meta信息存储，consumer的消费状态，group的管理以及 offset的值。

考虑到zk本身的一些因素以及整个架构较大概率存在单点问题，新版本中逐渐弱化了zookeeper的作用。

新的consumer使用了kafka内部的group coordination协议，也减少了对zookeeper的依赖，但是broker依然依赖于ZK，zookeeper 在kafka中还用来选举controller 和 检测broker是否存活等等。


### Kafka follower与leader同步机制，ISR、AR代表什么？

- ISR:In-Sync Replicas 副本同步队列
- AR:Assigned Replicas 所有副本

**ISR的伸缩**
ISR是由leader维护，follower从leader同步数据有一些延迟（包括延迟时间replica.lag.time.max.ms和延迟条数replica.lag.max.messages两个维度, 当前最新的版本0.10.x中只支持replica.lag.time.max.ms这个维度），任意一个超过阈值都会把follower剔除出ISR, 存入OSR（Outof-Sync Replicas）列表，新加入的follower也会先存放在OSR中。AR=ISR+OSR。

**什么情况下一个 broker 会从 isr中踢出去**
leader会维护一个与其基本保持同步的Replica列表，该列表称为ISR(in-sync Replica)，每个Partition都会有一个ISR，而且是由leader动态维护 ，如果一个follower比一个leader落后太多，或者超过一定时间未发起数据复制请求，则leader将其重ISR中移除 。

**kafka follower如何与leader同步数据**
Kafka的复制机制既不是完全的同步复制，也不是单纯的异步复制。

完全同步复制要求All Alive Follower都复制完，这条消息才会被认为commit，这种复制方式极大的影响了吞吐率。

而异步复制方式下，Follower异步的从Leader复制数据，数据只要被Leader写入log就被认为已经commit，这种情况下，如果leader挂掉，会丢失数据，kafka使用ISR的方式很好的均衡了确保数据不丢失以及吞吐率。

Follower可以批量的从Leader复制数据，而且Leader充分利用磁盘顺序读以及send file(zero copy)机制，这样极大的提高复制性能，内部批量写磁盘，大幅减少了Follower与Leader的消息量差。

**kafka中的HW（High Watermark）水位线**
什么是高水位？ 在Kafka中，高水位是一个位置信息标记，它是用消息位移来表征的，比如某个副本中HW=4，就是这个副本的高水位在offset=4那个位置上。

HW的作用 ：在Kafka中，高水位的作用主要有2个
1. 定义消息的可见性，即用来标识分区下的哪些消息是可以被消费的，比如某个分区的HW（leader的HW）是8，那么这个分区只有 < 8 这些位置上消息可以被消费。即高水位之前的消息才被认为是已提交的消息，才可以被消费。

2. 帮助Kafka完成副本同步。

需要注意的是，位移值等于高水位的消息也属于未提交消息。也就是说，高水位上的消息是不能被消费者消费的

还有一个日志末端位移的概念，Log End Offset，缩写是LEO。它表示副本写入下一条消息的位移值。上图中LEO是15，即下一条新消息的位移是15，8-14这些位置上的消息就是未提交消息。同一个副本对象，其高水位值不会大于LEO值。

分区的高水位就是其leader副本的高水位。

### kafka producer 生产消息
**ack  为 0， 1， -1 的时候代表啥**
- 1（默认）  数据发送到Kafka后，经过leader成功接收消息的的确认，就算是发送成功了。在这种情况下，如果leader宕机了，则会丢失数据。
- 0 生产者将数据发送出去就不管了，不去等待任何返回。这种情况下数据传输效率最高，但是数据可靠性确是最低的。
- -1 producer需要等待ISR中的所有follower都确认接收到数据后才算一次发送完成，可靠性最高。当ISR中所有Replica都向Leader发送ACK时，leader才commit，这时候producer才能认为一个请求中的消息都commit了。

**kafka producer如何优化打入速度**
- 增加线程
- 提高 batch.size
- 增加更多 producer 实例
- 增加 partition 数
- 设置 acks=-1 时，如果延迟增大：可以增大 num.replica.fetchers（follower 同步数据的线程数）来调解；
- 跨数据中心的传输：增加 socket 缓冲区设置以及 OS tcp 缓冲区设置。

### kafka consumer 消费消息
**消费者提交消费位移时提交的是当前消费到的最新消息的offset还是offset+1?**
offset+1


#### kafka Consumer参数设置
consumer.poll(1000) 重要参数
新版本的Consumer的Poll方法使用了类似于Select I/O机制，因此所有相关事件（包括reblance，消息获取等）都发生在一个事件循环之中。
1000是一个超时时间，一旦拿到足够多的数据（参数设置），consumer.poll(1000)会立即返回 ConsumerRecords<String, String> records。
如果没有拿到足够多的数据，会阻塞1000ms，但不会超过1000ms就会返回。

session. timeout. ms <= coordinator检测失败的时间
默认值是10s
该参数是 Consumer Group 主动检测 (组内成员comsummer)崩溃的时间间隔。若设置10min，那么Consumer Group的管理者（group coordinator）可能需要10分钟才能感受到。太漫长了是吧。

max. poll. interval. ms <= 处理逻辑最大时间
这个参数是0.10.1.0版本后新增的，可能很多地方看不到喔。这个参数需要根据实际业务处理时间进行设置，一旦Consumer处理不过来，就会被踢出Consumer Group 。
注意：如果业务平均处理逻辑为1分钟，那么max. poll. interval. ms需要设置稍微大于1分钟即可，但是session. timeout. ms可以设置小一点（如10s），用于快速检测Consumer崩溃。

auto.offset.reset
该属性指定了消费者在读取一个没有偏移量后者偏移量无效（消费者长时间失效当前的偏移量已经过时并且被删除了）的分区的情况下，应该作何处理，默认值是latest，也就是从最新记录读取数据（消费者启动之后生成的记录），另一个值是earliest，意思是在偏移量无效的情况下，消费者从起始位置开始读取数据。

enable.auto.commit
对于精确到一次的语义，最好手动提交位移

fetch.max.bytes
单次获取数据的最大消息数。

max.poll.records <= 吞吐量
单次poll调用返回的最大消息数，如果处理逻辑很轻量，可以适当提高该值。
一次从kafka中poll出来的数据条数,max.poll.records条数据需要在在session.timeout.ms这个时间内处理完
默认值为500

heartbeat. interval. ms <= 居然拖家带口
heartbeat心跳主要用于沟通交流，及时返回请求响应。这个时间间隔真是越快越好。因为一旦出现reblance,那么就会将新的分配方案或者通知重新加入group的命令放进心跳响应中。
connection. max. idle. ms <= socket连接
kafka会定期的关闭空闲Socket连接。默认是9分钟。如果不在乎这些资源开销，推荐把这些参数值为-1，即不关闭这些空闲连接。

request. timeout. ms
这个配置控制一次请求响应的最长等待时间。如果在超时时间内未得到响应，kafka要么重发这条消息，要么超过重试次数的情况下直接置为失败。
消息发送的最长等待时间.需大于session.timeout.ms这个时间

fetch.min.bytes
server发送到消费端的最小数据，若是不满足这个数值则会等待直到满足指定大小。默认为1表示立即接收。

fetch.wait.max.ms
若是不满足fetch.min.bytes时，等待消费端请求的最长等待时间

group.initial.rebalance.delay.ms <=牛逼了，我的kafka，防止成员加入请求后本应立即开启的rebalance
对于用户来说，这个改进最直接的效果就是新增了一个broker配置：group.initial.rebalance.delay.ms，
默认是3秒钟。
主要作用是让coordinator推迟空消费组接收到成员加入请求后本应立即开启的rebalance。在实际使用时，假设你预估你的所有consumer组成员加入需要在10s内完成，那么你就可以设置该参数=10000。


### 消息如何保证幂等性——重复消费怎么解决
为什么会出现重复消费？ 

场景：比如说消费端已经消费了 offset=2，offset=3，offset=4 的三条数据，正准备把这个 offset 的值传给 kafka，这时候消费端机器宕机了，这个数据没传过去；重启之后，消费端同步 kafka，kafka 那边消费的记录 offset 还是 1，那么 kafka 会认为之前的 2、3、4 都没有消费过，会把这几个数据在传给消费端；这样消费端这边就重复对这几条数据进行消费了。在数据库里面可能就多了很多重复的数据。 像其他的 MQ，也是一样，消费端再返回给 MQ 的时候，宕机了或者重启了，那么都会出现重复消费的问题。

幂等性：一个请求，不管重复来多少次，结果是不会改变的。

问题解决：每个消息都会有唯一的消息 id，将消息的唯一标识保存到外部介质中，每次消费时判断是否处理过即可。
1）、先查再保存 每次保存数据的时候，都先查一下，如果数据存在了那么就不保存。这个情况是并发不高的情况。

2）、业务表添加约束条件 如果你的数据库将来都不会分库分表，那么可以在业务表字段加上唯一约束条件（UNIQUE），这样相同的数据就不会保存为多份。

3）、添加消息表 再数据库里面，添加一张消息消费记录表，表字段加上唯一约束条件（UNIQUE），消费完之后就往表里插入一条数据。因为加了唯一约束条件，第二次保存的时候，mysql 就会报错，就插入不进去；通过数据库可以限制重复消费。

4）、使用 redis 如果你的系统是分布式的，又做了分库分表，那么可以使用 redis 来做记录，把消息 id 存在 redis 里，下次再有重复消息 id 在消费的时候，如果发现 redis 里面有了就不能进行消费。

5）、高并发下 如果你的系统并发很高，那么可以使用 redis 或者 zookeeper 的分布式对消息 id 加锁，然后使用上面的几个方法进行幂等性控制。

### kafka中的幂等和事务
幂等和事务是Kafka 0.11.0.0版本引入的两个特性，以此来实现EOS（exactly once semantics，精确一次处理语义）。

幂等，简单地说就是对接口的多次调用所产生的结果和调用一次是一致的。

生产者在进行重试的时候有可能会重复写入消息，而使用Kafka的幂等性功能之后就可以避免这种情况。

开启幂等性功能的方式很简单，只需要显式地将生产者客户端参数enable.idempotence设置为true即可（这个参数的默认值为false）。

Kafka是如何具体实现幂等的呢？Kafka为此引入了producer id（以下简称PID）和序列号（sequence number）这两个概念。每个新的生产者实例在初始化的时候都会被分配一个PID，这个PID对用户而言是完全透明的。

对于每个PID，消息发送到的每一个分区都有对应的序列号，这些序列号从0开始单调递增。生产者每发送一条消息就会将对应的序列号的值加1。

broker端会在内存中为每一对维护一个序列号。对于收到的每一条消息，只有当它的序列号的值（SN_new）比broker端中维护的对应的序列号的值（SN_old）大1（即SN_new = SN_old + 1）时，broker才会接收它。

如果SN_new< SN_old + 1，那么说明消息被重复写入，broker可以直接将其丢弃。如果SN_new> SN_old + 1，那么说明中间有数据尚未写入，出现了乱序，暗示可能有消息丢失，这个异常是一个严重的异常。

引入序列号来实现幂等也只是针对每一对而言的，也就是说，Kafka的幂等只能保证单个生产者会话（session）中单分区的幂等。幂等性不能跨多个分区运作，而事务可以弥补这个缺陷。

事务可以保证对多个分区写入操作的原子性。操作的原子性是指多个操作要么全部成功，要么全部失败，不存在部分成功、部分失败的可能。

为了使用事务，应用程序必须提供唯一的transactionalId，这个transactionalId通过客户端参数transactional.id来显式设置。事务要求生产者开启幂等特性，因此通过将transactional.id参数设置为非空从而开启事务特性的同时需要将enable.idempotence设置为true（如果未显式设置，则KafkaProducer默认会将它的值设置为true），如果用户显式地将enable.idempotence设置为false，则会报出ConfigException的异常。

transactionalId与PID一一对应，两者之间所不同的是transactionalId由用户显式设置，而PID是由Kafka内部分配的。


### 如何保证消息不丢失，可靠性传输
要保证消息不丢失，有三个场景：生产者投递消息到MQ，MQ存储消息，消费者从MQ消费消息，要分别确保上述三个过程都是成功的

**生产消息阶段**
Kafka消息发送有两种方式：同步（sync）和异步（async），默认是同步方式，可通过producer.type属性进行配置。

Kafka通过配置request.required.acks属性来确认消息的生产：
- 0---表示不进行消息接收是否成功的确认；
- 1---表示当Leader接收成功时确认；
- -1---表示Leader和Follower都接收成功时确认；

综上所述，有6种消息生产的情况，下面分情况来分析消息丢失的场景：
- acks=0，异步模式，不和Kafka集群进行消息接收确认，则当网络异常、缓冲区满了等情况时，消息可能丢失；
- acks=1(只保证写入leader成功)，同步模式下，只有Leader确认接收成功后但挂掉了，副本没有同步，数据可能丢失；

解决：
- 同步模式下，确认机制设置为-1，即让消息写入Leader和Follower之后再确认消息发送成功； 异步模式下，为防止缓冲区满，可以在配置文件设置不限制阻塞超时时间，当缓冲区满时让生产者一直处于阻塞状态；
- 发送时启用消息事务，channel发送失败就回滚。但是不推荐使用，因为事务时同步的，性能会下降。
- 发送时使用confirm模式（就是回调模式）。推荐使用，因为是异步的，吞吐量高。消息发送后，回调消息发送成功或者失败的接口。那么业务层面也就可以根据是否发送成功和失败做处理，比如发送前缓存到redis，发送成功后从redis中移除，对于在redis中一直没有处理的，再进行重发操作。

**MQ持久化本身丢数据**
由于kafka的多分区机制，当数据从leader的partition同步到其他follower的partition时，刚好leader挂了，此时选举某个同步慢的follower为leader，此时未同步的数据就丢失。需要从如下三个方面进行配置：

解决：
1. 每个topic的partition副本应该大于1；以确保数据有备份；
2. min.insync.replicas=2，消息至少要被写入到这么多副本才算成功，leader能够感知到至少有一个副本follower与自己是快速同步不掉队的；以确保切换leader是可行的；
3. unclean.leader.election.enable=false 关闭unclean leader选举，即不允许非ISR中的副本被选举为leader，以避免数据丢失。
4. acks=all，数据在所有follower的partition中存储完成后，才给生成者发送ack消息；以确保所有partition的消息保持一致；
5. retries = 一个合理值，生产者可设置发送失败后无限重试（也就会卡住，消息不会丢）；

**消费者丢数据**
Kafka消息消费有两个consumer接口，Low-level API和High-level API：
- Low-level API：消费者自己维护offset等值，可以实现对Kafka的完全控制；
- High-level API：封装了对partition和offset的管理，使用简单；

如果使用高级接口High-level API，可能存在一个问题就是当消息消费者从集群中把消息取出来、并提交了新的消息offset值后，还没来得及消费就挂掉了，那么下次再消费时之前没消费成功的消息就“诡异”的消失了；    

解决：
- enable.auto.commit=false，关闭自动提交offset，改为处理完数据之后手动提交offset


### 如何保证消息队列是高可用的？
一个典型的Kafka集群中包含
- 若干Producer（可以是web前端产生的Page View，或者是服务器日志，系统CPU、Memory等）
- 若干broker（Kafka支持水平扩展，一般broker数量越多，集群吞吐率越高）
- 若干Consumer Group
- 一个Zookeeper集群

Kafka通过Zookeeper管理集群配置，选举leader，以及在Consumer Group发生变化时进行rebalance。

Producer使用push模式将消息发布到broker，Consumer使用pull模式从broker订阅并消费消息。


### kafka如何体现消息顺序性的？
kafka不保证全局有序，只能保证一个partition分区之内消息的有序性，在不同的分区之间是不可以的，这已经可以满足大部分应用的需求。

**kafka是怎么保证消息写入是有序的，即顺序落盘的**
因为是采用尾插法添加消息到分区的。

消费时，每个partition只能被每一个group中的一个consumer消费，不能投递给同组内两个consumer。

如果需要整个topic中所有消息的有序性，有两种方案：
- 让这个topic只有一个分区，即将partition调整为1，当然也就只有一个consumer组消费它。
- 生产者可以设定一个key，同一个key的可以发送到同一个partition中，这样同一个key的消息在partition中是保序；

上面也会有问题：如果kafka在消费端开启多线程,也会出现乱序。

解决方案：可以在消费端加队列，按照业务保序增加内存队列，这样队列中的消息与partition中顺序是一致的，然后多线程从队列中取数据，每次取一个完整顺序的消息进行处理即可。

### kafka如何实现延迟队列？

Kafka并没有使用JDK自带的Timer或者DelayQueue来实现延迟的功能，而是基于时间轮自定义了一个用于实现延迟功能的定时器（SystemTimer）。JDK的Timer和DelayQueue插入和删除操作的平均时间复杂度为O(nlog(n))，并不能满足Kafka的高性能要求，而基于时间轮可以将插入和删除操作的时间复杂度都降为O(1)。时间轮的应用并非Kafka独有，其应用场景还有很多，在Netty、Akka、Quartz、Zookeeper等组件中都存在时间轮的踪影。

底层使用数组实现，数组中的每个元素可以存放一个TimerTaskList对象。TimerTaskList是一个环形双向链表，在其中的链表项TimerTaskEntry中封装了真正的定时任务TimerTask.

Kafka中到底是怎么推进时间的呢？Kafka中的定时器借助了JDK中的DelayQueue来协助推进时间轮。具体做法是对于每个使用到的TimerTaskList都会加入到DelayQueue中。Kafka中的TimingWheel专门用来执行插入和删除TimerTaskEntry的操作，而DelayQueue专门负责时间推进的任务。再试想一下，DelayQueue中的第一个超时任务列表的expiration为200ms，第二个超时任务为840ms，这里获取DelayQueue的队头只需要O(1)的时间复杂度。如果采用每秒定时推进，那么获取到第一个超时的任务列表时执行的200次推进中有199次属于“空推进”，而获取到第二个超时任务时有需要执行639次“空推进”，这样会无故空耗机器的性能资源，这里采用DelayQueue来辅助以少量空间换时间，从而做到了“精准推进”。Kafka中的定时器真可谓是“知人善用”，用TimingWheel做最擅长的任务添加和删除操作，而用DelayQueue做最擅长的时间推进工作，相辅相成。


### 为什么Kafka不支持读写分离？
在 Kafka 中，生产者写入消息、消费者读取消息的操作都是与 leader 副本进行交互的，从 而实现的是一种主写主读的生产消费模型。

Kafka 并不支持主写从读，因为主写从读有 2 个很明 显的缺点:

1. 数据一致性问题。数据从主节点转到从节点必然会有一个延时的时间窗口，这个时间 窗口会导致主从节点之间的数据不一致。某一时刻，在主节点和从节点中 A 数据的值都为 X， 之后将主节点中 A 的值修改为 Y，那么在这个变更通知到从节点之前，应用读取从节点中的 A 数据的值并不为最新的 Y，由此便产生了数据不一致的问题。

2. 延时问题。类似 Redis 这种组件，数据从写入主节点到同步至从节点中的过程需要经 历网络→主节点内存→网络→从节点内存这几个阶段，整个过程会耗费一定的时间。而在 Kafka 中，主从同步会比 Redis 更加耗时，它需要经历网络→主节点内存→主节点磁盘→网络→从节 点内存→从节点磁盘这几个阶段。对延时敏感的应用而言，主写从读的功能并不太适用。

 
### 队列积压如何处理
当消费者出现异常，很容易引起队列积压，如果一秒钟1000个消息，那么一个小时就是几千万的消息积压，是非常可怕的事情，但是生产线上又有可能会出现；

另外，当消息积压来不及处理，如rabbitMQ如果设置了消息过期时间，那么就有可能由于积压无法及时处理而过期，这消息就被丢失了；

解决方案：
1. 修复consumer代码故障，确保consumer逻辑正确可以消费；
2. 停止consumer，新建topic，新建10倍20倍的partition个数；
    * 创建对应原topic的partition个数的临时的consumer程序，消费原来的topic，并把消息写入到扩建的新topic中；
    * 再开启对应新partition个数的consumer对新的topic进行消费；
    * 这种做法相当于通过物理资源扩充了10倍来快速消费；
3. 当消费完成后，需要恢复原有架构，开启原来的consumer进行正常消费；

以下分享常用解决消息堆积问题的策略：
- 每个topic多设置分区数，提高并行消费得力度。
- 消费线程每次拉取的消息数可以适当调大，但是不能超过两次POLL默认配置的时间5秒，因为超过5秒，kafka就会触发rebalance，会认为这个消费者消费能力不足，把消费者提出消费组。
- 消费者提交偏移量改为自动提交。
- 合理的运用线程池去提高消费者处理消息的效率。

### kafka消费太慢、太快如何处理
**消费太慢**

考虑增加Topic的分区数，并且同时提升消费组的消费者数量，消费者数=分区数。（两者缺一不可）

**消费太快**

调整参数：
- fetch.max.bytes ：单次获取数据的最大消息数。
- max.poll.records <= 吞吐量 
  - 单次poll调用返回的最大消息数，如果处理逻辑很轻量，可以适当提高该值。
  - 一次从kafka中poll出来的数据条数,max.poll.records条数据需要在在session.timeout.ms这个时间内处理完，默认值为500
- consumer.poll(1000) 重要参数
  - 新版本的Consumer的Poll方法使用了类似于Select I/O机制，因此所有相关事件（包括reblance，消息获取等）都发生在一个事件循环之中。
  1000是一个超时时间，一旦拿到足够多的数据（参数设置），consumer.poll(1000)会立即返回 ConsumerRecords<String, String> records。
  如果没有拿到足够多的数据，会阻塞1000ms，但不会超过1000ms就会返回。

错误方案示例：
- 方案一：减少 topic 分区数量，能降低总体并行消费速度，但对于topic瞬时流量没有降低（例：一般系统基于mysql连接池用的是长连接，一次性获取的消息条数不变的情况下，分区数量并不能太限制kafka的消费速度）

- 方案二：在应用端加个线程的Thread.sleep（效率问题严重，开销大，切换线程浪费资源严重，当代码运行到Thread.sleep时，当前线程进入time_wait状态，通常我们会说线程进入睡眠状态，此时该线程不需要占用CPU了，操作系统就会执行一次线程切换，将该线程的CPU时间给其他线程使用，这种切换叫做主动线程切换）
```
网上有人对线程上下文切换时间做过计算，每一次大概需要消耗5微妙左右CPU时间。而每一次sleep会造成2次线程切换，一次切出去， 一次切回来，那么就会消耗10微妙CPU时间。这个消耗虽然很小，但是架不住次数多，这是一种可耻的浪费。

与此同时，sleep除了会造成线程切换之外，它还是一个系统调用。要知道系统调用相对于普通代码执行，也是一项重量级操作，每执行一次系统调用，操作系统需要将该线程的状态从用户态切为内核态，完成后还需要切回来，这也涉及到执行现场的保存和恢复，设计到CPU上下文切换。 当然，系统调用的切换开销不及线程切换的开销大，据统计，每次系统调用，涉及到切换上下文的的消耗时间为200纳秒，远小于线程切换的5微妙，但是如果次数多了，消耗的总和也不可忽视
```

### kafka 消息过期、删除
全局配置：kafka默认的消息过期时间为168h(7天)
- log.retention.hours=168 (配置该参数即可)
- log.cleanup.policy=delete （默认，可不配置）

如果想针对特定topic设置过期时间：./kafka-configs.sh --zookeeper localhost:2181 --alter --entity-name mytopic--entity-type topics --add-config retention.ms=86400000    #时间设置一天 86400000ms = 1天

kafka一般不会删除消息，不管这些消息有没有被消费。只会根据配置的日志保留时间(log.retention.hours)确认消息多久被删除，默认保留最近一周的日志消息。kafka的性能与保留的消息数据量大小没有关系，因此保存大量的数据消息日志信息不会有什么影响。

kafka至少会保留1个工作segment保存消息。消息量超过单个文件存储大小就会新建segment。

kafka会定时扫描非工作segment，将该文件时间和设置的topic过期时间进行对比，如果发现过期就会将该segment文件（具体包括一个log文件和两个index文件）打上.deleted 的标记。

最后kafka中会有专门的删除日志定时任务过来扫描，发现.deleted文件就会将其从磁盘上删除，释放磁盘空间，至此kafka过期消息删除完成。

可以看出，kafka删除消息是以segment为维度的，而不是以具体的一条条消息为维度。一个segment包含了一段时期的全部消息并存储在一个文件中.

所以如果消息量不多没有超过一个segment的存储容量，由于kafka至少要保留一个segment用于存取消息，所以也不会去删除里面过期的消息。实际上，也存在着设置了消息7天过期，但是kafka里面仍存在着10天前的数据，这就是由kafka的删除特性决定的。

我们删除过期消息的目的是为了释放磁盘空间，既然消息很少没有突破一个segment的容量，那么即使多保留几天的旧消息又何妨，又不怎么占用磁盘空间。然而这种删除策略的设计换来的却是大消息量的topic整块消息删除的高性能和高效率！


### 项目集成示例
1. 依赖
```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-stream-kafka</artifactId>
			<version>2.2.0.RELEASE</version>
		</dependency>
```

2. 配置文件
消息发送者：
```
#kafka
spring.cloud.stream.kafka.binder.brokers=192.168.1.20:9092
spring.cloud.stream.kafka.binder.zk-nodes=192.168.1.20:2182
spring.cloud.stream.kafka.binder.auto-add-partitions=true
spring.cloud.stream.kafka.binder.auto-create-topics=true
spring.cloud.stream.kafka.binder.min-partition-count=1
 
spring.cloud.stream.bindings.member.destination=mytopic
spring.cloud.stream.bindings.member.content-type=text/plain
spring.cloud.stream.bindings.member.producer.partitionCount=1

# yml
  cloud:
    stream:
      kafka:
        binder:
          brokers:
            - 192.168.171.11:9092
            - 192.168.171.207:9092
            - 192.168.171.239:9092
#          zk-nodes: 192.168.1.20:2182
          auto-add-partitions: true
          auto-create-topics: true
          min-partition-count: 1
      bindings:
        rds1Output: # 输出的管道名
          destination: qfjs_jwtf_01-ord_order_consume # topic
          content-type: application/json # 类型
          producer:
            partitionCount: 1
```

消息接收者
```
spring.cloud.stream.kafka.binder.brokers=192.168.1.20:9092
spring.cloud.stream.kafka.binder.zk-nodes=192.168.1.20:2182
spring.cloud.stream.kafka.binder.auto-add-partitions=true
spring.cloud.stream.kafka.binder.auto-create-topics=true
 
#这个得跟发送消息端的名称一致
spring.cloud.stream.bindings.member.destination=mytopic
#加上就能接收到之前发送没接收到的消息.
spring.cloud.stream.bindings.member.group=s1
```

#### 编写一个接口,output里是通道名称,通过这个名称,消费者与接收者进行关联.
```
package com.buba.api.rabbitmq;
 
import org.springframework.cloud.stream.annotation.Output;
import org.springframework.messaging.SubscribableChannel;
 
public interface SendService {
 
    @Output("member")
    SubscribableChannel sendMember();
}
```

#### 启动类注解
启动类加上监听管道注解@EnableBinding,值为通道对应的接口class
下面为同时配置发送和监听通道：
```
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients(basePackages = {"com.soonphe.ywzt.adminauth.api"})
@EnableBinding({Sink.class, Source.class})
public class BootstrapApplication {

  public static void main(String[] args) {
    SpringApplication.run(BootstrapApplication.class, args);
  }

}
```

#### 发送消息
```
    @Autowired
    SendService sendService;
 
    @RequestMapping("sendMember")
    public String sendMember(){
        Message build = MessageBuilder.withPayload("hello,rabbitmq".getBytes()).build();
 
        Boolean send = sendService.sendMember().send(build);
 
        return send.toString();
    }
```

#### 接收消息
```
package com.buba.rabbitmq;
 
import org.springframework.cloud.stream.annotation.Input;
import org.springframework.messaging.SubscribableChannel;
 
public interface ReceiveService {
 
    @Input("member")
    SubscribableChannel subscribableChannel();
}
```
启动类加上监听管道注解,与发送消息那边的名称一致 
```
@EnableBinding(ReceiveService.class)
```




