# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")


## knowledge(知识点整理)

### JDK相关：
Mac下查看已安装的jdk版本及其安装目录
```
查看JDK信息：/usr/libexec/java_home -V
移除Oracle JDK：sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
查看mysql数据库所有信息：show variables 
查看mysql字符串相关信息：show variables  like "%char%";
```

### Mapstruct：
```
实体属性相同、名称不同转换：
@Mappings({ @Mapping(source="grade", target="level") })
属性、常量转换：
@Mapping(target="ordType",constant="3")
```
基本属性与枚举互相转换：
```
自定义CustomMapper转换类，并在mapper接口中引用
@Mapper(uses={DateMapper.class,CustomMapper.class})
public interface OverTimeOrderMapper {
    ...
}

//自定义mapper转换类
public class CustomMapper {
    // 枚举转基础字段
    public Integer asOrderStateInteger(OverTimeOrderState state){
        return state.getCode();
    }
    // 基础字段转枚举
    public OverTimeOrderState asEnumState(Integer state){
        for(OverTimeOrderStateoverTimeOrderState:OverTimeOrderState.values()){
            if(overTimeOrderState.getCode().equals(state)){
                return overTimeOrderState;
            }
        }
    }
}
```

### 时间、日期加减：
```
Instant.now().minus(50,ChronoUnit.DAYS).toEpochMilli()
Instant.now().plus(10,ChronoUnit.DAYS).toEpochMilli()
```

### P6Spy集成：
文档相关：https://p6spy.readthedocs.io/en/latest/install.html
1.maven依赖：
```
<dependency>
<groupId>p6spy</groupId>
<artifactId>p6spy</artifactId>
<version>3.9.1</version>
</dependency>
```
2.新增配置spy.prroperties
配置文件添加JDBC驱动
```
driverlist=com.mysql.jdbc.Driver
。。。
```
3.修改properties中数据源配置：
```
driver-class-name:com.p6spy.engine.spy.P6SpyDriver
```

### QueryDSL集成：
Querydsl 是一个框架，它可以为多个后端（包括 JPA、MongoDB 和 Java 中的 SQL）构建类型安全的 SQL 类查询。
文档：https://hub.fastgit.org/querydsl/querydsl
http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html#jpa_integration
查询类型：
QCustomer customer = QCustomer.customer;
QCustomer customer = new QCustomer("myCustomer");
查询：
QCustomer customer = QCustomer.customer;
Customer bob = queryFactory.selectFrom(customer)
  .where(customer.firstName.eq("Bob"))
  .fetchOne();

实际使用：
@Generated("com.querydsl.codegen.EntitySerializer")
Public class QOverTimeFeeModel extends EntityPathBase<OverTimeFeeModel>{
	Public static final QOverTimeFeeModel overTimeFeeModel = new QOverTimeFeeModel("overTimeFeeModel");

QOverTimeFeeModel qOverTimeFeeModel=QOverTimeFeeModel.overTimeFeeModel;

Return jpaQueryFactory
.select(Projections.fields(OverTimeFeeModelStationVo.class,
	qOverTimeFeeModel.id.as("id"),qOverTimeStation.stationNo,qOverTimeStation.stationName,
	qOverTimeStation.operateState,qOverTimeStation.address,qOverTimeFeeModel.validStatus,
	qOverTimeFeeModel.feeRuleList))
.from(qOverTimeFeeModel)
.leftJoin(qStationFeeModel).on(qOverTimeFeeModel.id.eq(qStationFeeModel.feeModelId))
.leftJoin(qOverTimeStation)
.on(qOverTimeStation.stationNo.eq(qStationFeeModel.stationNo))
.where(predicate)
.offset((long)(query.getPage()-1)*query.getPageSize())
.limit(query.getPageSize())
.fetchResults();


### 分页为什么不要用offset和limit分页？
为了实现分页，每次收到分页请求时，数据库都需要进行低效的全表扫描。
解决：其实很简单，如果有主键，利用主键索引就够了！
Select * from table where id>10 limit 10

那如果我们的表没有主键，比如是具有多对多关系的表，那么就只能使用传统的 OFFSET/LIMIT 方式，但这样做存在潜在的慢查询问题。完全可以在需要分页的表中使用自动递增的主键，即使只是为了分页。

### maven引入本地jar：
        <dependency>
          <groupId>dingding</groupId>
          <artifactId>dingding</artifactId>
          <version>2.8</version>
          <scope>system</scope>
          <systemPath>${project.basedir}/lib/taobao-sdk-java.jar</systemPath>
        </dependency>
处理打包：
```
 <build>
   <resources>
    <resource>
      <directory>lib</directory>
      <targetPath>/BOOT-INF/lib/</targetPath>
      <includes>
        <include>**/*.jar</include>
      </includes>
    </resource>
   </resources>
 </build>
```

### maven安装本地jar到本地仓库：
mvn install:install-file
-Dfile=C:\Users\zw\Downloads\fastjson-1.2.4.jar
-DgroupId=com.alibaba
-DartifactId=fastjson
-Dversion=1.2.4
-Dpackaging=jar

### Mac上有3处可以设置环境变量：
/etc/profile ：系统全局变量，系统启动即加载该文件的配置（不建议添加）
/etc/bashrc：所有类型的bash shell 都会读取该文件的配置
~/.bash_profile：配置用户级环境变量，在系统用户文件夹下创建，当用户登录时，该文件会被执行且仅执行一次

### mvn -v提示Permission denied
权限不够，chmod a+x  /opt/apache-maven-3.2.2/bin/mvn(a:所有用户 +:增加权限 x:执行权限)

### Instant.now().toEpochMilli()和System.currentTimeMillis()用法
Instant.now()：当前时间戳
System.nanoTime()：当前时间戳（纳秒）
Instant.now().toEpochMilli()：当前时间戳毫秒
System.currentTimeMillis()：当前时间戳毫秒

### Jenkins流水线自动化：
git: Git
git branch: 'master', credentialsId: 'gitlab', url: 'http://192.168.102.34/new-platform/gx-new-order.git'

Sh: Shell Script
sh label: '', script: 'mvn clean package'

sshPublisher: Send build artifacts over SHH
sshPublisher(publishers: [sshPublisherDesc(configName: '192.168.161.227', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'tmp', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

Sshagent: SSH Agent
选择账户或添加账户

### Linux查找文件：
 find / -name war


### 关于金额字段为什么不用decimal类型
主要两个原因：
	1. 避免不同语言对浮点数麻烦的计算，一不小心非常容易算错（存整数进行计算的话，计算完再除以精度比较方便）
	2. 对于多个国家，对小数的精度要求是不同的，用 decimal 没办法方便控制

### @Validated和@Valid区别
1. 分组
@Validated：提供分组功能，可以在入参验证时，根据不同的分组采用不同的验证机制。
@Valid：没有分组功能。

2. 注解
@Validated：用在类型、方法和方法参数上。但不能用于成员属性（field)。
@Valid：可以用在方法、构造函数、方法参数和成员属性（field）上。

3. 嵌套验证
一个待验证的pojo类，其中还包含了待验证的对象，需要在待验证对象上注解@valid，才能验证待验证对象中的成员属性。

### mac使用public_key与private_key登录远程服务器：
1.在mac下生成public_key与private_key，生成的密钥在~/.ssh/下面
2.把mac下刚生成的public_key "id_rsa.pub"文件拷贝一份到远端服务器即将需要登录用户家目录下的.ssh/目录下，并命名为authorized_keys.
3. 最后修改本机mac下得配置文件，~/.ssh/config，格式如下图
Host test1
Hostname 192.168.35.125
Port 22
User root
IdentityFile ~/.ssh/id_rsa
4.直接执行 ssh test1即可达到所记录的远端服务器

用Terminus登录则创建Keys，选择私钥id_rsa即可

碰到问题：‘Permissions 0644 for '/Users/liuml/.ssh/id_rsa_tz' are too open.’
解决：chmod 600 *

### 解决“Jenkins 主机密钥验证失败”
1. ssh-keygen命令生成公钥私钥
2.去--> cat /var/lib/jenkins/.ssh/id_rsa.pub 从 id_rsa.pub 复制密钥（也可以使用scp命令）
3.文本复制
ssh登录目标服务器：ssh -l root 192.168.161.168
touch /root/.ssh/authorized_keys 
vi .ssh/authorized_keys  粘贴复制的密钥

4.scp复制
scp复制公钥：scp /root/.ssh/id_rsa.pub root@192.168.1.181:/root/.ssh/authorized_keys
由于还没有免密码登录的，所以要输入一次B机的root密码。

5.修改权限
需要特别注意的是：B主机的.ssh文件的所有者要是root，如果不是要改：
chown -R root:root .ssh
同时，B主机的authorized_keys文件，要是600权限的，如果不是，也要改：
chmod 600 authorized_keys
如果执行权限不够报错：-bash: ./startup.sh: 权限不够
使用：chmod u+x *

6.手动登录mercurial服务器
注意：请手动登录，否则 jenkins 会再次报错“主机验证失败”
ssh -l root 192.168.1.181


### mac安装Mysql启动报错（系统为BigSur版本）：
mysql启动：
sudo /usr/local/mysql/support-files/mysql.server start
报错可能是没有权限
sudo chmod -R a+rwx /usr/local/mysql/data/

### lombok注解：
@Data注解作用：
1）生成无参构造方法；
2）属性的set/get方法；
3）equals(), hashCode(), toString(), canEqual()方法。
@Value
1）有参构造方法；
2）只添加@Value注解，没有其他限制，那么类属性会被编译成final的，因此只有get方法，而没有set方法。


### Mysql解决select * from XX group by xxx;报错问题
select * from user group by age;
1. ERROR 1055 (42000): Expression #1 of SELECT list is not in GROUP BY clause 
2. and contains nonaggregated column 'test.user.user_id' which is not 
3. functionally dependent on columns in GROUP BY clause; this is 
4. incompatible with sql_mode=only_full_group_by

解决步骤：
1.查询mysql 相关mode
select @@global.sql_mode;
可以看到模式中包含了ONLY_FULL_GROUP_BY，只要没有这个配置即可。
我的Mysql版本是5.7.21，默认是带了ONLY_FULL_GROUP_BY模式。

ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,
ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION 

2.重设模式值
set @@global.sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
3.重启Mysql即可

### 在线接口数据示例：
参考：http://omdbapi.com/
搜索列表：http://omdbapi.com/?apikey=682d8365&s=Longest
详情：http://omdbapi.com/?apikey=682d8365&i=tt2726560

### for和foreach：
```
ArrayList<Object> objects=new ArrayList<>();
1.第一种
for (int j=0;j<objects.size();j++){
2.第二种
for(Object obj:objects){
}
3.第三种
objects.forEach(o->String.valueOf(o));
```

### Ajax：
Ajax：“Asynchronous JavaScript and XML”，翻译过来就是异步JavaScript和XML。
要创建Ajax，主角是XMLHttpRequest（下简称XHR）对象。
第一步：创建XHR对象
var xhr = new XMLHttpRequest();
第二步：向服务器发送请求
方法：open(method,url,async) 和 send(string)
open()方法传入三参数
	• method：请求的类型（GET/POST）
	• url：文件在服务器上的位置
	• async：布尔值，true表示异步，false表示同步（可选，默认为true）
1 xhr.open("GET","demo.asp?t=" + Math.random(),true);
2 xhr.send();
第三步：服务器响应
XMLHttpRequest对象的responseText和responseXML属性分别获得字符串形式的响应数据和XML形式的响应数据
还有三个关于响应状态的属性也经常用到：
	• readyState：存有XMLHttpRequest的状态。XHR对象会经历5种不同的状态
		○ 0：请求未初始化（new完后）；
		○ 1：服务器连接已建立（对象已创建并初始化，尚未调用send方法）；
		○ 2：请求已接收；
		○ 3：请求处理中；
		○ 4：请求已完成，响应就绪；
	• status：（HTTP状态码很多，请自行了解，举例常见的）
		○ 200：请求成功
		○ 404：未找到页面
	• onreadystatechange：存储函数（或函数名），每当readyState属性改变时，就会调用该函数。
1 xhr.onreadystatechange = function () {
2     if (xhr.readyState == 4 && xhr.status == 200) {
3     console.log(xhr.responseText);
4 };


### ES为什么不能做主数据库：
es没有事务，而且是近实时。成本也比数据库高，几乎靠吃内存提高性能。最逆天的是，mapping不能改。
ES团队不推荐完全采用ES作为主要存储，缺乏访问控制还有一些数据丢失和污染的问题


### spring StateMachine状态机：
1 引入依赖
```
<dependency>
<groupId>org.springframework.statemachine</groupId>
<artifactId>spring-statemachine-core</artifactId>
<version>2.0.1.RELEASE</version>
</dependency>
```
2 创建订单状态枚举类和状态转换枚举类
```
/**
* 订单状态
*/
public enum OrderStatus {
// 待支付，待发货，待收货，订单结束
WAIT_PAYMENT, WAIT_DELIVER, WAIT_RECEIVE, FINISH;
}
```
3 添加配置

4 添加订单状态监听器/拦截器


### 阿里云ONS接入：
参考文档：https://help.aliyun.com/document_detail/44711.html?spm=a2c4g.11186623.6.599.372a35e8LQkvOK
1.引入依赖
```
<dependency>
<groupId>com.aliyun.openservices</groupId>
<artifactId>ons-client</artifactId>
<version>1.8.4.Final</version>
</dependency>
```
2.MQ配置
```
mq:
  rocketmq:
	v2-access-key:62f49160c57b4f158a8ab3ebd2ff66cc
	v2-secret-key:J8H3v+Go55DIimy6gbKu3Bbnc0U=
	ons-addr:http://172.169.101.121:8080/rocketmq/nsaddr4broker-internal
	v2enabled:true
	productId:PID_order_dev
```
配置文件：
```
public static final String TOPIC = "您刚创建的Topic";
public static final String GROUP_ID = "您刚创建的Group ID";
public static final String ORDER_TOPIC = "您刚创建的用于收发顺序消息的Topic";
public static final String ORDER_GROUP_ID = "您刚创建的用于收发顺序消息的Group ID";
public static final String ACCESS_KEY = "您的阿里云账号的AccessKey ID";
public static final String SECRET_KEY = "您的阿里云账号的AccessKey Secret";
public static final String TAG = "您自定义的消息Tag属性";
public static final String NAMESRV_ADDR = "您刚创建的消息队列RocketMQ版实例的TCP接入点，可在消息队列RocketMQ版控制台的实例详情页面获取TCP协议客户端接入点";     
```
3.发送消息
说明：
1.发送/接收 普通消息
2.发送/接收 事务消息
3.发送/接收 顺序消息
```
@Autowired
privateProducerBeanproducerBean;

MessagesendMessage=newMessage();
OverTimeFeeModelCreateMsgoverTimeOrderBody=getOverTimeFeeModelCreateMsgBody(event.getStationFeeModel());
sendMessage.setBody(JSON.toJSONBytes(overTimeOrderBody));
sendMessage.setTopic(overTimeFeeModelCreatedProperties.getTopic());
sendMessage.setTag(overTimeFeeModelCreatedProperties.getExpressionCreate());
producerBean.send(sendMessage);
```
4.消费消息
```
@Component
@Slf4j
@AllArgsConstructor
Public class ReceiptPayOverTimeOrderListener implements MessageListener{
  
  //实现consume方法
  @Override
  public Action consume(Message message, ConsumeContext consumeContext) {
    log.info("===监听支付成功mq消息  订单状态修改成功!");
    return Action.CommitMessage;
  }
}


@Configuration
@RequiredArgsConstructor
@Profile(value = {"prod"})
@EnableConfigurationProperties(value = {ReceiptPayOrderProperties.class, DrawTheGunConsumerProperties.class})
public class OverTimeConsumerV3 {
  //自动注入的mq配置
  private final MqProperties mqProperties;
  //mq监听器
  private final ReceiptPayOverTimeOrderListener receiptPayOverTimeOrderListener;

  //绑定订阅和监听
  @Bean(initMethod = "start", destroyMethod = "shutdown")
  public ConsumerBean buildOrderSyncConsumer() {
    ConsumerBean consumerBean = new ConsumerBean();
    Properties properties = getMqProperties();
    properties.setProperty(PropertyKeyConst.GROUP_ID, mqProperties.getGroupId());
    properties.setProperty(PropertyKeyConst.ConsumerId, mqProperties.getGroupId());
    consumerBean.setProperties(properties);
    Map<Subscription, MessageListener> subscriptionTable = new HashMap<>(16);
    Subscription subscription = new Subscription();
    subscription.setTopic(receiptPayOrderProperties.getTopic());
    subscription.setExpression(receiptPayOrderProperties.getExpression());
    subscriptionTable.put(subscription, receiptPayOverTimeOrderListener);
    consumerBean.setSubscriptionTable(subscriptionTable);
    return consumerBean;
  }
```

### IDEA引入maven项目：
1.New——Module from Existing Source
2.如果没有显示为maven，模块的pom.xml上点击Add as maven project


### springboot引入redission：
1.依赖
```
<dependency>
   <groupId>org.redisson</groupId>
   <artifactId>redisson</artifactId>
   <version>3.16.0</version>
</dependency>  
```
2.配置文件
```
redisson:
	nodes:
		-192.168.161.68:7001
		-192.168.161.68:7002
		-192.168.161.68:7003
		-192.168.161.68:7004
		-192.168.161.68:7005
		-192.168.161.68:7006
	password:evcsr2020%1dSP
	mode:cluster
```
3.使用
```
Config config = new Config();
config.useClusterServers()
       // use "rediss://" for SSL connection
      .addNodeAddress("redis://127.0.0.1:7181");
// or read config from file
config = Config.fromYAML(new File("config-file.yaml")); 
//创建RedissonClient 对象
RedissonClient redisson = Redisson.create(config);
//获取分布式锁
RLock lock = redisson.getLock("myLock");
//加锁
lock.lock(RedisKeyConstants.getAuthKeyTimeOut(),TimeUnit.SECONDS);
```

### springboot引入nacos：
1.依赖
```
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-config-spring-boot-starter</artifactId>
    <version>0.2.7</version>
</dependency>
```
2.定义服务地址
```
nacos.config.server-addr=127.0.0.1:8848
```
3.使用@NacosPropertySource 加载资源
```
@SpringBootApplication @NacosPropertySource(dataId = "example", autoRefreshed = true) 
public class NacosConfigApplication { 
public static void main(String[] args) { 
SpringApplication.run(NacosConfigApplication.class, args); } }
```
4.使用@NacosValue 指定属性值
```
@NacosValue(value = "${useLocalCache:false}", autoRefreshed = true) private boolean useLocalCache;
```

### openresty搭建高性能web应用、网关：
官网地址：http://openresty.org/cn/


### soul网关（shenyu）：
dromara官网地址：https://dromara.org/
soul官网地址：https://github.com/apache/incubator-shenyu
官网地址：https://shenyu.apache.org/


### skywalking接入：
javaagent参数集成skywalking的agent服务功能，简而言之就是启动项目时一同启动skywalking-agent.jar这个服务

监控服务端启动：
https://github.com/apache/skywalking
下载、bin目录启动、http://localhost:8080/访问

客户端接入方式：
1. 系统配置方式
使用 -D参数设置应用名称，skywalking.agent.service_name是属性，=后面是值；skywalking.collector.backend_service对应的是收集服务的地址
```
java -javaagent:/apache-skywalking-apm-bin/agent/skywalking-agent.jar
-Dskywalking.agent.service_name=app-service 
-Dskywalking.collector.backend_service=127.0.0.1:11800
-jar app-service.jar &
```

2. 探针方式
在skywalking-agent.jar后直接追加 =agent.service_name=应用名称 
```
java -javaagent:/apache-skywalking-apm-bin/agent/skywalking-agent.jar=agent.service_name=app-service -jar app-service.jar &
```
3. 插件使用
默认情况agent是不支持对spring-cloud-gateway的监控的，需要插件的支持。我们要将optional-plugins下的插件apm-spring-cloud-gateway-2.x-plugin-6.5.0.jar拷贝到plugins下，使agent可以加载到该插件，其他一些需要额外插件支持的中间件和框架也是同理操作。


### Nexus接入：
下载地址：https://www.sonatype.com/products/repository-oss-download
https://download.sonatype.com/nexus/3/latest-mac.tgz
1.构建
```
git fetch --tags
git checkout -b release-3.29.2-02 origin/release-3.29.2-02 --
./mvnw clean install
```
2.解压运行
```
unzip -d target assemblies/nexus-base-template/target/nexus-base-template-*.zip
./target/nexus-base-template-*/bin/nexus console
```

### jira搭建：
官网地址：https://www.atlassian.com/
1.下载安装
```
atlassian-jira-software-7.3.8-x64_2.bin
[root@jira ~]# chmod +x atlassian-jira-software-7.3.8-x64_2.bin  #添加执行权限
[root@jira ~]# ./atlassian-jira-software-7.3.8-x64_2.bin   #安装
```
安装jira时配置指定数据库，jira支持多种数据库
2.破解jira
```
jira7.3 
├── atlassian-extras-3.2.jar ：和license相关
└── mysql-connector-java-5.1.39-bin.jar：jira连接mysql数据库相关的jar包
把破解包里的文件复制到/opt/atlassian/jira/atlassian-jira/WEB-INF/lib/目录下
[root@jira ~]# \cp -f ~/jira7.3/* /opt/atlassian/jira/atlassian-jira/WEB-INF/lib/ 
```
3.开启jira服务
```
[root@jira ~]# /opt/atlassian/jira/bin/start-jira.sh 
```
访问8080端口 http://192.168.13.142:8080/


### springboot使用eventbus：
事件驱动：
```
Google-Eventbus
        // EventBus对象创建
        EventBus eventBus = new EventBus("test");
        // 注册监听者（监听者@Subscribe 订阅时间）
        eventBus.register(new OrderEventListener());
        // 发布消息
        eventBus.post(new OrderMessage());
// 异步事件消息处理
EventBusbus=newAsyncEventBus(threadPoolExecutor);
```

1.依赖
```
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>28.1-jre</version>
</dependency>
```
2.EventBusConfig
```
/**
 * 事件监听配置
 * 
 * @author soonphe
 * @since 1.0
 */
@Configuration
public class EventBusConfig {

  /**
   * eventbus注册异步监听
   * @param eventListener
   * @return
   */
  @Bean
  public EventBus eventBus(AsyncEventListener eventListener) {
    Builder builder = new Builder().namingPattern("event-bus-threads");
    ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10, 10, 60L,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(100), builder.build());
    //同步
    //EventBus eventBus = new EventBus();
    //异步
    EventBus bus = new AsyncEventBus(threadPoolExecutor);
    bus.register(eventListener);
    return bus;
  }
}
```
3.事件监听类
```
@Slf4j
@Component
@AllArgsConstructor
public class AsyncEventListener {

/**
   * 监听操作日志时间
   * @param event
   */
  @Subscribe
  public void saveOperationRecordEvent(SaveOverTimeOperationRecordEvent event) {
    OverTimeOperationRecordDto operationRecordDto = OverTimeOperationRecordMapper.INSTANCE.eventToDto(event);
    try {
      Long record = operationService.createOverTimeOperationRecord(operationRecordDto);
      log.info("---event  新增操作记录成功! 记录id为{}", record);
    } catch (Exception e) {
      log.error("---event  新增操作记录表出现异常,异常信息为", e);
    }
  }
}
```
4.事件bean
```
@Getter
@Builder
public class SaveOverTimeOperationRecordEvent extends OverTimeOperationRecord {

  private Long operator;
  private String operatorAccount;
  private Integer operatorType;
  private Long operationTableId;
  private Integer operatorChannel;

}
```
5.注入eventbus，post测试发送事件
```
  @Autowired
  private EventBus eventBus;

  eventBus.post(overTimeFeeModelDeleteEvent);
```
备注：还可以定义一层handle处理eventbus的注册和注销操作
```java
@Component
@Slf4j
public class EventHandler {

    @Autowired
    private EventBus eventBus;

    @Autowired
    private EventListener eventListener;

    @PostConstruct
    public void init() {
        eventBus.register(eventListener);
    }

    @PreDestroy
    public void destroy() {
        eventBus.unregister(eventListener);
    }

    public void eventPost(){
        eventBus.post("test");
        log.info("post event");
    }
}
```


### 责任链实现：
1.FilterChain责任链接口（方法：preAuth前置鉴权，fireNext下一个鉴权）
```
//第一种形式
public interface OrderFilterChain<T extends OrderContext> {
  void handle(T var1);

  void fireNext(T var1);
}
//第二种形式
public interface FilterChain {

   /**
    * 前置鉴权
    * @param request
    */
   void preAuth(StandardPreAuthModel request);

   /**
    * 开启下一个鉴权
    * @param request
    */
   void fireNext(StandardPreAuthModel request);
}
```
2.实现责任链接口
```java
//第一种形式
public class DefaultFilterChain<T extends OrderContext> implements OrderFilterChain<T> {
  private OrderFilterChain<T> next;
  private OrderFilter<T> filter;

  public DefaultFilterChain(OrderFilterChain chain, OrderFilter filter) {
    this.next = chain;
    this.filter = filter;
  }

  public void handle(T context) {
    this.filter.doFilter(context, this);
  }

  public void fireNext(T ctx) {
    OrderFilterChain nextChain = this.next;
    if (Objects.nonNull(nextChain)) {
      nextChain.handle(ctx);
    }

  }
}
//第二种形式
public class DefaultFilterChain implements FilterChain {

  //下一个filterChain引用
  private FilterChain next;
  //filter鉴权（只有一个doAuth方法）
  private PreAuthFilter filter;

  public DefaultFilterChain(FilterChain chain, PreAuthFilter filter) {
    this.next = chain;
    this.filter = filter;
  }

  //调用filter的doAuth鉴权、传递next引用，鉴权完毕调用下一个fireNext
  @Override
  public void preAuth(StandardPreAuthModel request) {
    filter.doAuth(request, this);
  }

  //调用next的preAuth
  @Override
  public void fireNext(StandardPreAuthModel request) {
    FilterChain nextChain = this.next;
    if (Objects.nonNull(nextChain)) {
      nextChain.preAuth(request);
    }
  }
}
```
3.责任链初始化
```
//第一种形式
public class FilterChainPipeline<T extends OrderFilter> {
  private DefaultFilterChain last;

  public FilterChainPipeline() {
  }

  public DefaultFilterChain getFilterChain() {
    return this.last;
  }

  public FilterChainPipeline addFilter(T filter) {
    DefaultFilterChain newChain = new DefaultFilterChain(this.last, filter);
    this.last = newChain;
    return this;
  }

  public FilterChainPipeline addFilter(String desc, T filter) {
    DefaultFilterChain newChain = new DefaultFilterChain(this.last, filter);
    this.last = newChain;
    return this;
  }
}

//第二种形式
FilterChain filterChain7=new DefaultFilterChain(null, orderExtensionAuthFilter);
FilterChain filterChain6=new DefaultFilterChain(filterChain7, orderExtensionAuthFilter);

```


### underscore和jquery区别
underscore和jquery（jquey主要用于页面交互，封装了很多操作dom的方法，以及ajax，
underscore则可以理解为一个js的函数库，其中主要封装了一些常用的js方法）

### OkHttp基础请求方式：
```
public static final MediaType JSON = MediaType.get("application/json; charset=utf-8");
OkHttpClient client = new OkHttpClient();
String post(String url, String json) throws IOException {  
    RequestBody body = RequestBody.create(JSON, json);  
    Request request = new Request.Builder()      
                        .url(url)      
                        .post(body)      
                        .build();  
    try (Response response = client.newCall(request).execute()) {    
        return response.body().string();  
    }
}
```

### package.json中 ^ 和 ~ 的区别
指定版本号
(1)普通版本号: 表示安装此版本,比如"classnames": "2.2.5"，表示安装2.2.5的版本
(2)表示安装大版本的最小最新子版本: ~版本,比如 "babel-plugin-import": "~1.1.0",表示安装1.1.x的最新版本（不低于1.1.0），但是不安装1.2.x，也就是说安装时不改变大版本号和次要版本号
(3)表示安装大版本的最高中版本: ^版本,比如 "antd": "^3.1.4",，表示安装3.1.4及以上的版本，但是不安装4.0.0，也就是说安装时不改变大版本号。

### devDependencies和dependencies区别
devDependencies用于本地环境开发时候。
dependencies用户发布环境

### npm install 安装报错解决思路：
1、删除  package-lock.json文件
2、npm cache clean --force
3、npm config rm proxy    npm config rm https-proxy
最后试试更换源：
npm set registry https://registry.npmjs.org/

### vue axios get请求传参：
示例：
{token}：token: admin-token
token：0:token

### spring中重载bean
```
spring:
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
        # public环境
        namespace:
  main:
    allow-bean-definition-overriding: true
```

### jvm默认参数
-Xmx 用来设置你的应用程序(不是JVM)能够使用的最大内存数（相当于 -XX:MaxHeapSize）。
-Xms 用来设置程序初始化的时候内存栈的大小（相当于 -XX:MaxNewSize）。
-Xss 规定了每个线程堆栈的大小。一般情况下256K是足够了，该值影响了此进程中并发线程数大小（相当于 -XX:ThreadStackSize）。

一般来说，就JDK8而言：
-Xmx 的默认值为你当前机器最大内存的 1/4
-Xms 的默认值为你当前机器最大内存的 1/64 （这个值要反复测试并通过监控调整一个合适的值，是因为当Heap不够用时，会发生内存抖动，影响程序运行稳定性）
-Xss 的默认值好像和平台有关（不同平台默认值不同），我们最常用的Linux64位服务器默认值好像是1024k（这个我不确定）。在相同物理内存下，减小这个值能生成更多的线程，这个参数在高并发的情况下对性能影响比较明显，需要花比较长的时间进行严格的测试来定义一个合适的值（如果栈不深128k够用的，大的应用建议使用256k）。

```
java
    -Xms64m #JVM启动时的初始堆大小
    -Xmx128m #最大堆大小
    -Xmn64m #年轻代的大小，其余的空间是老年代
    -XX:MaxMetaspaceSize=128m #
    -XX:CompressedClassSpaceSize=64m #使用 -XX：CompressedClassSpaceSize 设置为压缩类空间保留的最大内存。
    -Xss256k #线程
    -XX:InitialCodeCacheSize=4m #
    -XX:ReservedCodeCacheSize=8m # 这是由 JIT（即时）编译器编译为本地代码的本机代码（如JNI）或 Java 方法的空间
    -XX:MaxDirectMemorySize=16m
    -jar app.jar
```

### linux 软链接和硬链接区别
【硬连接】
硬连接指通过索引节点来进行连接。在Linux的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。在Linux中，多个文件名指向同一索引节点是存在的。一般这种连接就是硬连接。硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

【软连接】
另外一种连接称之为符号连接（Symbolic Link），也叫软连接。软链接文件有类似于Windows的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。

硬链接文件有两个限制
1)、不允许给目录创建硬链接；
2)、只有在同一文件系统中的文件之间才能创建链接，而且只有超级用户才有建立硬链接权限。
对硬链接文件进行读写和删除操作时候，结果和软链接相同。但如果我们删除硬链接文件的源文件，硬链接文件仍然存在，而且保留了愿有的内容。
这时，系统就“忘记”了它曾经是硬链接文件。而把他当成一个普通文件。
软链接没有硬链接以上的两个限制，因而现在更为广泛使用，它具有更大的灵活性，甚至可以跨越不同机器、不同网络对文件进行链接

### Linux /usr/bin与/usr/local/bin区别: 
/usr/bin下面的都是系统预装的可执行程序，会随着系统升级而改变。
/usr/local/bin目录是给用户放置自己的可执行程序的地方，推荐放在这里，不会被系统升级而覆盖同名文件。
如果两个目录下有相同的可执行程序，谁优先执行受到PATH环境变量的影响。

### maven install和package区别
Maven install 安装指令，其做了两件事情：
1. 将项目打包（jar/war），将打包结果放到项目下的 target 目录下
2. 同时将上述打包结果放到本地仓库的相应目录中，供其他项目或模块引用
Maven package 打包指令，其就做了一件事：
1. 将项目打包（jar/war），将打包结果放到项目下的 target 目录下


### IDEA、WebStorm项目无法被识别为Git项目
VCS - Enable Version Control Intergration

### mac下-bash: mysql: command not found问题 
vim ~/.bash_profile 

加入export PATH=$PATH:/usr/local/mysql/bin  保存后关闭
source ~/.bash_profile 执行修改

### node环境更新
```
sudo npm install -g n 

# 最新版本
n lastest
# 稳定版本
n stable
# 安装指定版本
n 10.12.0
```
npm 更新
```
npm install -g npm 
```

