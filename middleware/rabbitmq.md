## rabbitmq

### brew 命令安装 RabbitMQ 
- 前置条件：安装 erlang：brew install erlang
- 安装 rabbitmq：brew install rabbitmq
- 配置 RabbitMQ 环境变量
```
vi ~/.bash_profile
export RABBIT_HOME=/usr/local/Cellar/rabbitmq/3.13.1
export PATH=$PATH:$RABBIT_HOME/sbin
source ~/.bash_profile
```
- 到brew安装目录下的sbin目录：cd /usr/local/Cellar/rabbitmq/3.13.1/sbin
- 安装 RabiitMQ 的可视化监控插件：sudo rabbitmq-plugins enable rabbitmq_management
- 后台启动 RabbitMQ：sudo rabbitmq-server -detached
- 查看状态：sudo rabbitmqctl status
- 访问可视化监控插件的界面：浏览器内输入http://localhost:15672，默认的用户名密码都是 guest，登录后可以在 Admin 那一列菜单内添加自己的用户
- 后台关闭：sudo rabbitmqctl stop

### Rabbit 用户操作
1、添加 Rabbit用户：Admin——Add a User
2、创建 Virtual Hosts：Admin——Virtual Host——add Virtual Host
3、选中 Admin 用户设置权限

### Rabbit 队列操作
1、添加队列： Queues and Streams——Add a new queue（可设置对应Virtual host）

type：此queue的类型，默认为classic 主队列，也可以设置为quorum 从队列
name：此queue的名称
durability：queue中的消息是否要持久化到硬盘
auto delete：如果此queue没有绑定到任何一个exchange，是否自动删除此queue
arguments：设置一些其它参数
exchange、queue的消息持久化能力，保证了rabbitmq的高可靠性。

### rabbitmqctl命令行操作
- 查看状态：./rabbitmqctl status     或者使用：service rabbitmq-server status
- 创建用户：rabbitmqctl add_user f15mqwuhan 'w3.f15#mq!'
- 设置vhost：rabbitmqctl add_vhost bd
- 设置用户绑定vhost：rabbitmqctl set_permissions [-p bd] {f15mqwuhan}
例：rabbitmqctl set_permissions -p bd f15mqwuhan '.*' '.*' '.*'    //如果不加vhost，使用/则表示可以访问所有服务器

- 关闭应用：rabbitmqctl stop_app
- 清除：rabbitmqctl reset
- 再次启动：rabbitmqctl start_app

- 添加vhost：rabbitmqctl add_vhost <vhost>
- 删除vhost：rabbitmqctl delete_vhost <vhost>
- 查看所有vhost：rabbitmqctl list_vhosts [vhostinfoitem ...]

- 查看所有用户：rabbitmqctl list_users
- 验证用户和密码：rabbitmqctl authenticate_user f15mqwuhan 'w3.f15#mq!'
- 设置用户标签：rabbitmqctl set_user_tags f15mqwuhan management
- 查看所有队列：rabbitmqctl list_queues

- 查看所有queue及消息总数：rabbitmqctl list_queues name messages

### 关于交换机
在RabbitMQ中，生产者发送消息不会直接将消息投递到队列中，而是先将消息投递到交换机中，在由交换机转发到具体的队列，队列再将消息以推送或者拉取方式给消费者进行消费
```
        创建消息            路由键           pull/push
生产者------------>交换机------------>队列------------>消费者
```

在RabbitMQ中，一个有4中Exchange，分别是direct、topic、fanout、headers。还有一个默认的交换机，称为default exchange，其本质也是一个direct exchange。

简单模式
生产者，一个队列一个或多个消费者，当多个消费者同时监听一个队列时，他们并不能同时消费一条消息，而是随机消费消息，即一个队列中一条消息，只能被一个消费者消费。

订阅与发布模式(fanout)
生产者，一个交换机(fanoutExchange)，没有路由规则，多个队列，多个消费者。生产者将消息不是直接发送到队列，而是发送到X交换机，然后由交换机发送给两个队列，两个消费者各自监听一个队列，来消费消息。

路由模式(direct)
生产者，一个交换机(directExchange)，路由规则，多个队列，多个消费者。主要根据定义的路由规则决定消息往哪个队列发送。

主题模式(topic)
生产者，一个交换机(topicExchange)，模糊匹配路由规则，多个队列，多个消费者。

订阅模式(headers)
不依赖于routing key与binding key的匹配规则来路由消息，而是根据发送的消息内容中的headers属性进行匹配。 在绑定Queue与Exchange时指定一组键值对；当消息发送到Exchange时，RabbitMQ会取到该消息的headers（也是一个键值对的形式），对比其中的键值对是否完全匹配Queue与Exchange绑定时指定的键值对；如果完全匹配则消息会路由到该Queue，否则不会路由到该Queue。

总结：
```
订阅模式，路由模式，主题模式，他们的相同点就是都使用了交换机，只不过在发送消息给队列时，添加了不同的路由规则。

订阅模式没有路由规则，路由模式为完全匹配规则，主题模式为模糊匹配（正则表达式，完全匹配规则）。

在交换机模式下：队列和路由规则有很大关系，生产者只用关心交换机与路由规则即可，无需关心队列。

消费者不管在什么模式下：永远不用关心交换机和路由规则，消费者永远只关心队列，消费者直接和队列交互。
```

### Ack和Nack 和Reject的区别
在服务器端的客户端页面从队列中获取消息是一个危险的动作，生产环境一定要了解业务之后再做操作

Act Mode
- Nack message requeue true 获取消息，但是不做ack应答确认，消息重新入队【队列是先进先出，拿到了消息这个消息就出队列了，现在这句话就是拿到了消息，然后又重新将消息给丢到队列里面去，其他人还可以获取到】
- Ack message requeue false 获取消息，应答确认，消息不重新入队，将会从队列中删除
- reject requeue true 拒绝获取消息，消息重新入队
- reject requeue false 拒绝获取消息，消息不重新入队，将会被删除

Encoding
AMQP消息负载可以包含任何的二进制内容，因此他们很难再浏览器中展示，编码的选         项含义有如下内容：string/base64,如果消息负载可以使用UTF-8字符串编码，就执行此 操作，否则就按照base64编码进行返回。

Messages
定义一次从队列中获取的消息数量

消息确认可以让RabbitMQ知道消费者已经接受并处理完消息。但是如果消息本身或者消息的处理过程出现问题怎么办？需要一种机制，通知RabbitMQ，这个消息，我无法处理，请让别的消费者处理。这里就有两种机制，Reject和Nack。
1）Ack对于明确要消费的消息。可以通过Ack的方式告知mq，mq就会发送下一条消息给消费者
2）Nack 告知mq，这样的消息我不处理，丢弃掉，此时消息会成为死信。
3）Reject reject与nack的用法相同，但是与nack只有一个区别，nack一次可以拒签多条消息（multiple:true）,但是reject一次只能拒签一条消息。

DLX（死信队列）
DLX定义 DLX为Dead Letter Exchange，死信队列。当一个消息在一个队列中变成死信（dead message）之后，它能重新publish到另一个Exchange，那么这个让消息变为死信的Exchange就是DLX（死信队列）。
消息变成死信的几种情况 1、消息被拒绝，ack为false，并且 requeue=false; 2、消息TTL(Time To Live)过期，指消息达到了过期时间； 3、队列达到最大长度。

### Springboot集成
依赖：
```
        <dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-amqp</artifactId>
		</dependency>
```
配置
```
spring: 
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: username
    password: w3.f15#mq!
    virtual-host: bd
    prefix: wuhan-
    
    #设置消费端手动 ack   none不确认  auto自动确认  manual手动确认（默认为自动确认，即消息发送给消费者后立即确认）
    listener.simple.acknowledge-mode=manual
```

自动创建队列
```
    //这里的Queue 所在包org.springframework.amqp.core;
    @Bean
    Queue myQueue() {
        return new Queue("xxx_yg_push_stream_result1", true); // true 表示持久化
    }
   
```
自动创建队列和交换机
```
    //声明队列，同时声明交换机，将队列绑定到交换机上(所有指定key的消息都会发送到这个队列)
    //@Queue("queue.test.001") 在项目启动时，会创建一个名为queue.test.001的队列。
    //@Exchange("directExchange") 在项目启动时，会创建一个名为directExchange的交换机，如果不指定类型，默认就是direct exchange。
    //key指定交换机和队列的绑定的routing key。
    //用Message接收需要自己解析body里面的内容
    //交换机directExchange就通过routing key“wuhan_yg_push_stream_down”和队列wuhan_yg_push_stream_result2绑定在一起了。
    //一旦生产者向交换机directExchange发送消息，并指定routing key为“wuhan_yg_push_stream_down”，消费者consumer就可以消费消息了。
    @RabbitListener(bindings = @QueueBinding(
            value = @org.springframework.amqp.rabbit.annotation.Queue(value = "wuhan_yg_push_stream_result2", durable = "true"),
            exchange = @Exchange(value = "train2"),
            key = "wuhan_yg_push_stream_down"
    ))
    public void onMessage(Message message) {
        //收到消息后处理...
    }
```

监听指定队列
```
    @RabbitListener(queues = "xxx_yg_push_stream_result1")
    public void processMessage(String content) {
        System.out.println("接收到消息：" + content);
    }
```
监听同时处理多个队列
```
    @RabbitListener(queues = {"xxx_yg_push_stream_result1", "xxx_yg_push_stream_result2"})
    public void processMessage(String content) {
        System.out.println("接收到消息：" + content);
    }
```
监听同时创建队列
```
    //queuesToDeclare 用于声明队列，如果不存在则创建，以队列名称作为路由密钥的默认交换
    @RabbitListener( queuesToDeclare = {@org.springframework.amqp.rabbit.annotation.Queue("wuhan_yg_push_stream_result3")})
    public void process(String content) {
            System.out.println("接收到消息：" + content);
    }
```
监听同时发送ACK
```
    //import org.springframework.amqp.core.Message;
    //import com.rabbitmq.client.Channel;
    @RabbitListener(queues ="publisher.addUser")
    public void addUser(Channel channel,Message message){
        long deliveryTag = message.getMessageProperties().getDeliveryTag();
        try {
            //手动ack  第二个参数为false是表示仅仅确认当前消息 true表示确认之前所有的消息
            channel.basicAck(deliveryTag,false);
        } catch (Exception e) {
            //手动nack 告诉rabbitmq该消息消费失败  第三个参数：如果被拒绝的消息应该被重新请求，而不是被丢弃或变成死信，则为true
            try {
                channel.basicNack(deliveryTag,false,true);
            } catch (IOException ex) {
                ex.printStackTrace();
            }
        }
    }
```

发送消息到指定队列
```
    //注入消息
    @Autowired
    private AmqpTemplate amqpTemplate;

amqpTemplate.convertAndSend("xxx_yg_push_stream_simulator", JSON.toJSONString(jsonObjectSend));
```

**交换机**
创建了两个队列wuhan_yg_push_stream_result2和wuhan_yg_push_stream_result3，并都将其通过相同的routing key “wuhan_yg_push_stream_down”绑定到同一个交换机directExchange上。

交换机directExchange就通过routing key“wuhan_yg_push_stream_down”和队列wuhan_yg_push_stream_result2、wuhan_yg_push_stream_result2绑定在一起了。
一旦生产者向交换机directExchange发送消息，并指定routing key为“wuhan_yg_push_stream_down”，消费者consumer就可以消费消息了。
```
    @RabbitListener(bindings = @QueueBinding(
            value = @org.springframework.amqp.rabbit.annotation.Queue(value = "wuhan_yg_push_stream_result2", durable = "true"),
            exchange = @Exchange(value = "train2"),
            key = "wuhan_yg_push_stream_down"
    ))
    public void onMessage(Message message) {
        log.info("===Receiver:{}", message);
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @org.springframework.amqp.rabbit.annotation.Queue(value = "wuhan_yg_push_stream_result3", durable = "true"),
            exchange = @Exchange(value = "train2"),
            key = "wuhan_yg_push_stream_down"
    ))
    public void onMessage3(Message message) {
        log.info("===Receiver:{}", message);
    }
```

### 生产消息发送返回消息
如果需要在发送消息后获取返回的消息，可以使用RabbitTemplate的sendAndReceive方法，该方法会发送消息并等待返回的消息，返回的消息可以是同步的，也可以是异步的。
```
    //发送消息
    Message message = MessageBuilder.withBody("hello".getBytes()).build();
    Message reply = rabbitTemplate.sendAndReceive("exchange", "routingKey", message);
    System.out.println(new String(reply.getBody()));
```

如果是要在发送消息时routingKey找不到这种情况下 返回消息：
```
//配置文件
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: xxx
    password: xxx
    virtual-host: xx
    prefix: xxx-
    listener:
      simple:
        acknowledge-mode: manual   # 关闭自动ack，手动返回消息消费结果
    publisher-confirms: true  # 是否开启消息确认机制
    publisher-confirm-type: correlated  # SIMPLE 、CORRELATED、NONE 不允许发送确认消息
    publisher-returns: true #默认false 不返回消息发送到队列失败消息
```
代码配置
```
    @Bean
    public SimpleRabbitListenerContainerFactory simpleRabbitListenerContainerFactory(ConnectionFactory connectionFactory) {
        SimpleRabbitListenerContainerFactory simpleRabbitListenerContainerFactory =
                new SimpleRabbitListenerContainerFactory();
        //这个connectionFactory就是我们自己配置的连接工厂直接注入进来
        simpleRabbitListenerContainerFactory.setConnectionFactory(connectionFactory);
        //这边设置消息确认方式由自动确认变为手动确认
        simpleRabbitListenerContainerFactory.setAcknowledgeMode(AcknowledgeMode.MANUAL);
        //设置消息预取数量
//        simpleRabbitListenerContainerFactory.setPrefetchCount(1);
        return simpleRabbitListenerContainerFactory;
    }
    
    @Bean
//    @Scope("prototype")
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate template = new RabbitTemplate(connectionFactory);
        //成功回调
        template.setConfirmCallback(new RabbitTemplate.ConfirmCallback(){

            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                System.out.println("消息发送成功");
            }
        });
        // 开启mandatory模式（开启失败回调）
        template.setMandatory(true);
        //失败回调
        template.setReturnCallback(new RabbitTemplate.ReturnCallback(){
            @Override
            public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
                System.out.println("消息发送失败");
                log.info(JSON.toJSONString(message));
            }
        });
        return template;
    }
    
    //如果
```
这个当发送消息失败时，会自动返回消息到服务器

**错误案例**
报错：Only one ConfirmCallback is supported by each RabbitTemplate 解决办法

错误原因：
spring中Bean默认是使用的的单列模式，不巧的是RabbitTemplate 只能设置一个ConfirmCallback，所以造成了上面的BUG
如果直接使用系统默认的RabbitTemplate返回就回报这个错，必须自己重写RabbitTemplate Bean

RabbitTemplate 设置成多例模式（实际测试并不需要多例）
```
@Bean
@Scope("prototype")
public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
     RabbitTemplate template = new RabbitTemplate(connectionFactory);
        template.setMandatory(true);
        template.setMessageConverter(new SerializerMessageConverter());
        return template;
    }
```






