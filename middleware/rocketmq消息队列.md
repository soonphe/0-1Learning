# 0-1Learning

![alt ](../static/common/svg/luoxiaosheng.svg "公众号")
![alt ](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt ](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## rocketmq消息队列

### 简介
rocket消息队列（Message Queue，简称MQ）是企业级互联网架构的核心产品，服务于整个阿里巴巴集团已超过 10 年，经过阿里巴巴交易核心链路反复打磨与历年双十一购物狂欢节的严苛考验，是一个真正具备低延迟、高并发、高可用、高可靠，可支撑万亿级数据洪峰的分布式消息中间件。

### 快速入门
使用流程：
1. 开通消息队列服务（RocketMQ产品页）
2. 为RAM用户授权(RAM控制台)
3. 创建实例（RocketMQ控制台）
4. 创建Topic（RocketMQ控制台）
5. 创建HTTP/TCP Group ID （RocketMQ控制台）
6. 获取HTTP/TCP接入点（RocketMQ控制台）
7. 下载并安装HTTP/TCP SDK
8. 运行HTTP/TCP示例代码收发消息

说明：
- RocketMQ版提供的TCP协议客户端SDK和HTTP协议客户端SDK不同，因此您所创建的同一个Group ID不能混用于TCP协议和HTTP协议。
- 同一个消息队列RocketMQ版实例既有TCP协议接入点，又有HTTP协议的接入点，您需分别获取TCP协议和HTTP协议的SDK来使用对应协议的接入点，不能混用。
- TCP协议客户端接入点仅在公网地域有公网接入点，其余地域只提供内网接入点，HTTP协议在各地域均提供公网和内网接入点。
- 如果您的应用有跨地域使用消息队列RocketMQ版的场景，推荐您使用HTTP协议。

### mac安装rocketmq
1. 下载: http://rocketmq.apache.org/docs/quick-start/, 直接下载源代码版本
2. 以守护进程的方式启动mqnamesrv和broker
```
nohup sh bin/mqnamesrv &   
这个命令可能会nohup: ignoring input and appending output to ‘nohup.out’,
如果出现这个,执行:
tail -f nohup.out

启动Broker
sh bin/mqbroker -n localhost:9876

使用JPS命令查看启动状态：
21746 NamesrvStartup
21818 BrokerStartup
出现即为成功
```
3. 测试发送消息
```
测试发送消息
export NAMESRV_ADDR=localhost:9876

可以看到终端输出大量日志，已经发送不了很多消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
```
4. 测试接受消息
```
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer

消息内容如下：
ConsumeMessageThread_1 Receive New Messages: [MessageExt [brokerName=MacBook-Pro.local, queueId=3, storeSize=204, queueOffset=120, sysFlag=0, bornTimestamp=1724380066042, bornHost=/192.168.1.30:62897, storeTimestamp=1724380066043, storeHost=/192.168.1.30:10911, msgId=C0A8011E00002A9F0000000000017DC8, commitLogOffset=97736, bodyCRC=532952986, reconsumeTimes=0, preparedTransactionOffset=0, toString()=Message{topic='TopicTest', flag=0, properties={MIN_OFFSET=0, MAX_OFFSET=121, KEYS=key, CONSUME_START_TIME=1724380484565, UNIQ_KEY=C0A8011E5DBF3AF49F1C738AA4FA0042, CLUSTER=DefaultCluster, WAIT=true, TAGS=tag}, body=[109, 101, 115, 115, 97, 103, 101, 66, 111, 100, 121], transactionId='null'}]]

可以看到对应的topic和key：body为加密的消息
topic='TopicTest',  KEYS=key，body=[109, 101, 115, 115, 97, 103, 101, 66, 111, 100, 121],
```

### 安装rocketmq控制台 rocketmq-console
1.下载
在 GitHub 中搜索 rocketmq-externals，其中 rocketmq-console 就是 RocketMQ 可视化控制台，我们可以将源码克隆下来，然后自己 mvn package，然后运行 jar 包。
或者直接下载官方提供的 1.0.0 版本的 rocketmq-console
> https://github.com/apache/rocketmq-externals/releases/tag/rocketmq-console-1.0.0

2.修改配置文件
进入rocketmq-console的src/main/resources/文件夹
修改配置文件application.properties
```
rocketmq.config.namesrvAddr=127.0.0.1:9876
注：需要修改端口的可自行修改
```
保存修改后的配置文件，返回rocketmq-console目录
使用maven打包命令打包
```
mvn clean package -Dmaven.test.skip=true
```
打包完成后进入target目录
rocketmq-console-ng-1.0.0.jar即为打包后得到的jar包

3.启动程序
```
nohup java -jar rocketmq-console-ng-1.0.0.jar &
```
访问 http://127.0.0.1:8080/

4.测试topic创建
消息发送
```
发送消息报错：
org.apache.rocketmq.client.exception.MQClientException: Send [3] times, still failed, cost [5]ms, Topic: TopicTest, BrokersSent: [MacBook-Pro.local, MacBook-Pro.local, MacBook-Pro.local]
See http://rocketmq.apache.org/docs/faq/ for further details.

解决办法：系统剩余磁盘必须大于10%
```

### 使用HTTP协议的SDK收发消息集成
参考文档：https://help.aliyun.com/document_detail/44711.html?spm=a2c4g.11186623.6.599.372a35e8LQkvOK

1. 开通消息队列服务并授权（略）
2. 创建资源实例（根据地域选择创建）
3. 创建topic（普通消息）
	- Topic不能跨实例使用，例如在实例A中创建的Topic A不能在实例B中使用。
	- 在同一实例中Topic名称必须唯一。
	- 您可创建不同的Topic来发送不同类型的消息，例如用Topic A发送普通消息，Topic B发送事务消息，Topic C发送定时和延时消息。
	- 创建的普通消息的Topic不能用来收发其他类型的消息，包括定时、延时、顺序和事务消息。换言之，切勿混用不同消息类型的Topic。
4. 创建Group ID
	- 创建完实例和Topic后，您需要为消息的消费者（或生产者）创建客户端ID，即Group ID作为标识。
	- Group ID的使用说明如下：
		-	在同一实例中Group ID必须唯一。
		- Group ID和Topic的关系是N:N，即一个消费者可以订阅多个Topic，同一个Topic也可以被多个消费者订阅；一个生产者可以向多个Topic发送消息，同一个Topic也可以接收来自多个生产者的消息。
5. 获取接入点（即ons-addr）
	- 在控制台创建好资源后，您需通过消息队列RocketMQ版控制台获取实例的接入点。在收发消息时，您需要为生产端和消费端配置该接入点，以接入某个具体实例或地域的服务。
6. 获取AccessKey
	-	阿里云账号（主账号）和RAM用户创建一个访问密钥（AccessKey）。在调用阿里云API时您需要使用AccessKey完成身份验证。
	- AccessKey包括AccessKey ID和AccessKey Secret。
	-	AccessKey ID：用于标识用户。
	-	AccessKey Secret：用于验证用户的密钥。AccessKey Secret必须保密。
	- 控制台右上方的账号图标，单击AccessKey管理。在安全提示对话框，选择使用阿里云账号AccessKey或RAM用户AccessKey。
7. http协议SDK集成
```
    <dependency>
      <groupId>com.aliyun.openservices</groupId>
      <artifactId>ons-client</artifactId>
      <version>1.8.4.Final</version>
    </dependency>
    
    
    或者使用apche相关依赖
    <!-- in your <dependencies> block -->
  <dependency>
      <groupId>org.apache.rocketmq</groupId>
      <artifactId>rocketmq-client</artifactId>
      <version>4.9.4</version>
  </dependency>
  
  <dependency>
      <groupId>org.apache.rocketmq</groupId>
      <artifactId>rocketmq-acl</artifactId>
      <version>4.9.4</version>
  </dependency>
```
8. yml配置文件
```
#启动测试之前请替换如下 XXX 为您的配置
rocketmq: 
	accessKey: XXX
	secretKey: XX
	nameSrvAddr: XXX
#业务topic，groupId，tag配置1
	topic: XXX
	groupId: XXX
	tag: *
#业务topic，groupId，tag配置2
	orderTopic: XXX
	orderGroupId: XXX
	orderTag: *
```
9. 调用HTTP协议的SDK发送普通消息
```
/**
 * 读取MQ配置文件
 *
 * @author soonphe
 * @since 1.0
 */
@Data
@Configuration
@ConfigurationProperties(prefix = "rocketmq")
public class MqConfig {

    private String accessKey;
    private String secretKey;
    private String nameSrvAddr;
    private String topic;
    private String groupId;
    private String tag;
    private String orderTopic;
    private String orderGroupId;
    private String orderTag;

    public Properties getMqPropertie() {
        Properties properties = new Properties();
        properties.setProperty(PropertyKeyConst.AccessKey, this.accessKey);
        properties.setProperty(PropertyKeyConst.SecretKey, this.secretKey);
        properties.setProperty(PropertyKeyConst.NAMESRV_ADDR, this.nameSrvAddr);
        return properties;
    }
}


/**
 * ProducerBean配置
 *
 * @author soonphe
 * @since 1.0
 */
@Configuration
public class ProducerClient {

    @Autowired
    private MqConfig mqConfig;

    @Bean(initMethod = "start", destroyMethod = "shutdown")
    public ProducerBean buildProducer() {
        ProducerBean producer = new ProducerBean();
        producer.setProperties(mqConfig.getMqPropertie());
        return producer;
    }
}


/**
 * mq发送示例
 *
 * @author soonphe
 * @since 1.0
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class SqlProducerTest {

    //普通消息的Producer 已经注册到了spring容器中，后面需要使用时可以直接注入到其它类中
    @Autowired
    private ProducerBean producer;

    @Autowired
    private MqConfig mqConfig;

    @Test
    public void testSend() {

        //循环发送消息
        for (int i = 0; i < 100; i++) {
            String tag;
            int div = i % 3;
            if (div == 0) {
                tag = "TagA";
            } else if (div == 1) {
                tag = "TagB";
            } else {
                tag = "TagC";
            }

            Message msg = new Message( //
                    // Message所属的Topic
                    mqConfig.getTopic(),
                    // Message Tag 可理解为Gmail中的标签，对消息进行再归类，方便Consumer指定过滤条件在MQ服务器过滤
                    tag,
                    // Message Body 可以是任何二进制形式的数据， MQ不做任何干预
                    // 需要Producer与Consumer协商好一致的序列化和反序列化方式
                    "Hello MQ".getBytes());
            // 设置代表消息的业务关键属性，请尽可能全局唯一
            // 以方便您在无法正常收到消息情况下，可通过MQ 控制台查询消息并补发
            // 注意：不设置也不会影响消息正常收发
            msg.setKey("ORDERID_100");

            // 设置自定义属性，该属性可用于做SQL属性过滤
            msg.putUserProperties("a", String.valueOf(i));

            // 发送消息，只要不抛异常就是成功
            try {
                SendResult sendResult = producer.send(msg);
                assert sendResult != null;
                System.out.println(sendResult);
            } catch (ONSClientException e) {
                System.out.println("发送失败");
                //出现异常意味着发送失败，为了避免消息丢失，建议缓存该消息然后进行重试。
            }
        }

    }

}
```

10. 调用HTTP协议的SDK消费普通消息
```
/**
 * mq消费配置
 *
 * @author soonphe
 * @since 1.0
 */
@Configuration
public class ConsumerClient {

    @Autowired
    private MqConfig mqConfig;

    @Autowired
    private DemoMessageListener messageListener;

    @Bean(initMethod = "start", destroyMethod = "shutdown")
    public ConsumerBean buildConsumer() {
        ConsumerBean consumerBean = new ConsumerBean();
        //配置文件
        Properties properties = mqConfig.getMqPropertie();
        properties.setProperty(PropertyKeyConst.GROUP_ID, mqConfig.getGroupId());
        //将消费者线程数固定为20个 20为默认值
        properties.setProperty(PropertyKeyConst.ConsumeThreadNums, "20");
        consumerBean.setProperties(properties);
        //订阅关系
        Map<Subscription, MessageListener> subscriptionTable = new HashMap<Subscription, MessageListener>();
        Subscription subscription = new Subscription();
        subscription.setTopic(mqConfig.getTopic());
        subscription.setExpression(mqConfig.getTag());
        subscriptionTable.put(subscription, messageListener);
        //订阅多个topic如上面设置

        consumerBean.setSubscriptionTable(subscriptionTable);
        return consumerBean;
    }

}

/**
 * Listen监听器配置
 *
 * @author soonphe
 * @since 1.0
 */
@Component
public class DemoMessageListener implements MessageListener {

    @Override
    public Action consume(Message message, ConsumeContext context) {

        System.out.println("Receive: " + message);
        try {
            //do something..
            return Action.CommitMessage;
        } catch (Exception e) {
            //消费失败
            return Action.ReconsumeLater;
        }
    }
}
```

