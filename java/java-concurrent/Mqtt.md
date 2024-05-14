## Mqtt


### springboot集成mqtt
Spring Boot 整合 MQTT 可以使用 Spring Integration 和 Spring Boot 自动配置的功能。以下是一个基本的例子：

添加依赖到你的 pom.xml：
```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-integration</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.integration</groupId>
        <artifactId>spring-integration-stream</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.integration</groupId>
        <artifactId>spring-integration-mqtt</artifactId>
    </dependency>
</dependencies>
```

配置 MQTT 连接，在 application.properties 或 application.yml 中添加：
```
# MQTT 连接配置
spring.mqtt.username=your_username
spring.mqtt.password=your_password
spring.mqtt.url=tcp://your_mqtt_broker:port
spring.mqtt.client.id=clientId
spring.mqtt.default.topic=defaultTopic
```

创建配置类，设置 MQTT 消息的接收和发送：
```
@Configuration
@IntegrationComponentScan
public class MqttConfig {
 
    @Value("${spring.mqtt.url}")
    private String url;
 
    @Value("${spring.mqtt.client.id}")
    private String clientId;
 
    @Value("${spring.mqtt.username}")
    private String userName;
 
    @Value("${spring.mqtt.password}")
    private String password;
 
    @Value("${spring.mqtt.default.topic}")
    private String defaultTopic;
 
    @Bean
    public MqttPahoClientFactory mqttClientFactory() {
        DefaultMqttPahoClientFactory factory = new DefaultMqttPahoClientFactory();
        MqttConnectOptions options = new MqttConnectOptions();
        options.setServerURIs(new String[]{url});
        options.setUserName(userName);
        options.setPassword(password.toCharArray());
        factory.setConnectionOptions(options);
        return factory;
    }
 
    @Bean
    public MessageChannel mqttInputChannel() {
        return new DirectChannel();
    }
 
    @Bean
    @ServiceActivator(inputChannel = "mqttInputChannel")
    public MessageHandler mqttInbound() {
        MqttPahoMessageDrivenChannelAdapter adapter = new MqttPahoMessageDrivenChannelAdapter(clientId, mqttClientFactory(), defaultTopic);
        adapter.setCompletionTimeout(5000);
        adapter.setQos(2);
        adapter.setOutputChannel(mqttInputChannel());
        return adapter;
    }
 
    @Bean
    @ServiceActivator(inputChannel = "outboundMqttChannel")
    public MessageHandler mqttOutbound() {
        MqttPahoMessageHandler messageHandler = new MqttPahoMessageHandler(clientId, mqttClientFactory());
        messageHandler.setAsync(true);
        messageHandler.setDefaultTopic(defaultTopic);
        return messageHandler;
    }
 
    @Bean
    public MessageChannel outboundMqttChannel() {
        return new DirectChannel();
    }
}
```
使用 outboundMqttChannel 发送消息：
```
@Autowired
private MessageChannel outboundMqttChannel;
 
public void sendMessage(String payload) {
    outboundMqttChannel.send(MessageBuilder.withPayload(payload).build());
}
```
监听 mqttInputChannel 接收消息：
```
@Service
@MessagingGateway(defaultRequestChannel = "mqttInputChannel")
public interface MqttGateway {
    void receiveMessage(String payload);
}
```
当你启动 Spring Boot 应用程序时，它将自动连接到 MQTT 代理，并且可以开始接
