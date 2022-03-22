# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## knowledge(知识点整理)

### JDK相关：
Mac下查看已安装的jdk版本及其安装目录
```
查看JDK信息：/usr/libexec/java_home -V
移除Oracle JDK：sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
查看mysql数据库所有信息：show variables 
查看mysql字符串相关信息：show variables  like "%char%";
```

### JDK相关工具使用
```
查看JDK信息：/usr/libexec/java_home -V
JDK bin目录：cd /Library/Java/JavaVirtualMachines/jdk1.8.0_241.jdk/Contents/Home/bin/

jconsole：Jconsole （Java Monitoring and Management Console），一种基于JMX的可视化监视、管理工具。
JConsole 基本包括以下基本功能：概述、内存、线程、类、VM概要、MBean
1.3.1 内存监控
内存页签相对于可视化的jstat 命令，用于监视受收集器管理的虚拟机内存。
1.3.2 线程监控
如果上面的“内存”页签相当于可视化的jstat命令的话，“线程”页签的功能相当于可视化的jstack命令，遇到线程停顿时可以使用这个页签进行监控分析。线程长时间停顿的主要原因主要有：等待外部资源（数据库连接、网络资源、设备资
源等）、死循环、锁等待（活锁和死锁）

jvisualvm：VisualVM（All-in-One Java Troubleshooting Tool）;功能最强大的运行监视和故障处理程序
- 显示虚拟机进程以及进程的配置、环境信息（jps、jinfo）。
- 监视应用程序的CPU、GC、堆、方法区(1.7及以前)，元空间（JDK1.8及以后）以及线程的信息（jstat、jstack）。
- dump以及分析堆转储快照（jmap、jhat）。
- 方法级的程序运行性能分析，找出被调用最多、运行时间最长的方法。
- 离线程序快照：收集程序的运行时配置、线程dump、内存dump等信息建立一个快照

平时启动jvisualvm
1./usr/libexec/java_home -V
2.cd /Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/bin
3.jvisualvm

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
权限不够，chmod a+x  /opt/apache-maven-3.2.2/bin/mvn(a:所有用户 +:增加权限 x:执行权限)

### Instant.now().toEpochMilli()和System.currentTimeMillis()用法
Instant.now()：当前时间戳
System.nanoTime()：当前时间戳（纳秒）
Instant.now().toEpochMilli()：当前时间戳毫秒
System.currentTimeMillis()：当前时间戳毫秒

### Jenkins流水线自动化：
git: Git
git branch: 'master', credentialsId: 'gitlab', url: 'http://192.168.102.34/new-platform/new-order.git'

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
    //参数：corePoolSize，maximumPoolSize，keepAliveTime，TimeUnit，BlockingQueue等待对了
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

虚拟类AbstractOrderFilter判断责任链Seletor处理（doFilter，handle）
DefaultFilterChain责任链判断实现

1.FilterChain责任链接口
```
//OrderFilter作为业务处理
public interface OrderFilter<T extends OrderContext> {
  void doFilter(T var1, OrderFilterChain var2);
}

//OrderFilterChain责任链式处理
public interface OrderFilterChain<T extends OrderContext> {
  void handle(T var1);

  void fireNext(T var1);
}
```
2.实现责任链接口
```
public class DefaultFilterChain<T extends OrderContext> implements OrderFilterChain<T> {
  //下一个filterChain引用
  private OrderFilterChain<T> next;
  //filter业务处理
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
```
3.责任链初始化
```
//第一种形式使用Pipline链式添加
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
//添加引用pipline
FilterChainPipeline pipeline = new FilterChainPipeline();
pipeline.addFilter("创建订单附表", orderExtensionAuthFilter(syncService, meterRegistry))
	.addFilter...
return new IotCuoHePreAuthProvider(pipeline.getFilterChain(), iotCuoHeAuthReqConverter,
	producerServer, redisServer);

//第二种形式
//1.直接使用filterChain链式添加
FilterChain filterChain7=new DefaultFilterChain(null, orderExtensionAuthFilter);
FilterChain filterChain6=new DefaultFilterChain(filterChain7, orderExtensionAuthFilter);
return new IotCuoHePreAuthProvider(filterChain1, iotCuoHeAuthReqConverter, producerServer, redisServer);

```
4. 添加seletor选择执行责任链
```
//Filter选择器
public interface FilterSelector {
  //匹配Filter
  boolean matchFilter(String var1);

  List<String> getFilterNames();
}

//Filter选择器实现
public class LocalListBasedFilterSelector implements FilterSelector {
  private List<String> filterNames = Lists.newArrayList();

  public LocalListBasedFilterSelector() {
  }

  public boolean matchFilter(String classSimpleName) {
    return this.filterNames.stream().anyMatch((s) -> {
      return Objects.equals(s, classSimpleName);
    });
  }

  public List<String> getFilterNames() {
    return this.filterNames;
  }

  public void addFilter(String clsNames) {
    this.filterNames.add(clsNames);
  }
}

//实际业务使用
public class FeeItemDomainService {

  private final FilterChainPipeline orderFilterChainPipeline;

  public FeeItemDomainService(
      FilterChainPipeline orderFilterChainPipeline) {
    this.orderFilterChainPipeline = orderFilterChainPipeline;
  }

  /**
   * 构造context对象
   * 
   * @param request
   * @return
   */
  public FeeItemResult getFeeItemResult(OrderCreateRequest request){
    CalculateContext calculateContext = new CalculateContext(BizEnum.OVER_TIME_ORDER,feeFilter(),request);
    orderFilterChainPipeline.getFilterChain().handle(calculateContext);
    return calculateContext.getFeeItemResult();
  }

  /**
   * 构造filter选择器
   *
   * @param request
   * @return
   */
  private LocalListBasedFilterSelector feeFilter(){
    LocalListBasedFilterSelector selector = new LocalListBasedFilterSelector();
    selector.addFilter(OverTimeFeeItemCalculateFilter.class.getSimpleName());
    selector.addFilter(RequestSaveFilter.class.getSimpleName());
    return selector;
  }

}
```
5. context对象
```
//通用Context对象
public interface OrderContext {
  BizEnum getBizCode();

  FilterSelector getFilterSelector();

  List<String> getDynamicCloseFilters();
}

public abstract class AbstractOrderContext implements OrderContext {
  private final BizEnum bizEnum;
  private final FilterSelector selector;

  public AbstractOrderContext(BizEnum bizEnum, FilterSelector selector) {
    this.bizEnum = bizEnum;
    this.selector = selector;
  }

  public BizEnum getBizCode() {
    return this.bizEnum;
  }

  public FilterSelector getFilterSelector() {
    return this.selector;
  }
}

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
1. 将项目打包（jar/war），将打包结果放到项目下的 target 目录下
2. 同时将上述打包结果放到本地仓库的相应目录中，供其他项目或模块引用
Maven package 打包指令，其就做了一件事：
1. 将项目打包（jar/war），将打包结果放到项目下的 target 目录下


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


### Vue - 引入本地图片的两种方式
第一种，只引入单个图片，这种引入方法在异步中引入则会报错。 比如需要遍历出很多图片展示时
<image :src = require('图片的路径') />

第二种，可引入多个图片，也可引入单个图片。 vuelic3版本没有static文件夹。可将静态图片存放到public目录下，直接引入即可
<image src="/public/image/logo.png"/>

第三种：使用相对路径形式@/assets/icon/group_4.png
```
<!--    <img class="card-panel-icon" src="@/assets/icon/group_4.png" alt="404" style="width: 48px">-->

    <img class="card-panel-icon" :src=imgSrc(item.icon) alt="404" style="width: 48px">

    imgSrc: function(index) {
      return require('@/assets/icon/' + (index) + '.png')
    },
```


### IDEA打包出现错误，使用mvn调试
```
mvn compile
mvn clean
mvn compile -X		#打包，发生jar的冲突显示冲突的原因
mvn spring-boot:run #已springboot方式启动

其他命令：
mvn dependency:tree 	#显示依赖树
mvn -e		#查看错误的详细信息
mvn compile	#编译源代码
mvn test		#运行测试代码
mvn package	#打包项目
mvn clean	#清除项目
mvn clean install -DskipTests	打包项目到本地仓库
mvn clean package ****  -DskipTests -DskipRat	打包项目跳过测试

mvn -U 		#强制刷新本地仓库不存在release版和所有的snapshots版本。
mvn clean install -P test 						#-P test的意思是使用 test profile 进行项目的构建
mvn clean install -Dmaven.test.skip=true 
mvn clean package -Dmaven.test.skip=true -P dev	#使用dev环境打包 
```
或者查看maven helper插件是否存在、升级


### nacos不同服务、同一端口是否可以服务发现
可以


### gitlab push报错
git push -u gitlab  --all

> 提示内容：GitLab: You are not allowed to push code to this project. fatal: Could not r

解决
1. 确认用户名、邮箱
2. 查看是否存在项目权限
3. 查看仓库链接方式，如果是SSH，确认是否配置ssh key，建议直接配置http测试推送


### Micrometer
官网文档：https://micrometer.io/docs
Micrometer为最流行的监控系统提供了一个简单的仪表客户端外观，允许仪表化JVM应用，而无需关心是哪个供应商提供的指标。它的作用和SLF4J类似，只不过它关注的不是Logging（日志），而是application metrics（应用指标）。简而言之，它就是应用监控界的SLF4J。

1.依赖
Micrometer记录的应用程序指标用于观察、告警和对环境当前/最近的操作状态做出反应。

为了使用Micrometer，首先要添加你所选择的监视系统的依赖。以Prometheus为例：
```
<dependency>
 	<groupId>io.micrometer</groupId>
   	<artifactId>micrometer-registry-prometheus</artifactId>
   	<version>${micrometer.version}</version>
</dependency>
```
2.1.  Registry

Meter是收集关于你的应用的一系列指标的接口。Meter是由MeterRegistry创建的。每个支持的监控系统都必须实现MeterRegistry。

Micrometer中包含一个SimpleMeterRegistry，它在内存中维护每个meter的最新值，并且不将数据导出到任何地方。如果你还没有一个首选的监测系统，你可以先用SimpleMeterRegistry：
```
MeterRegistry registry = new SimpleMeterRegistry();
注意：如果你用Spring的话，SimpleMeterRegistry是自动注入的
@Autowired
private MeterRegistry meterRegistry;
```

2.2.  Meters

Micrometer提供一系列原生的Meter，包括Timer , Counter , Gauge , DistributionSummary , LongTaskTimer , FunctionCounter , FunctionTimer , TimeGauge。不同的meter类型导致有不同的时间序列指标值。例如，单个指标值用Gauge表示，计时事件的次数和总时间用Timer表示。

每一项指标都有一个唯一标识的名字和维度。“维度”和“标签”是一个意思，Micrometer中有一个Tag接口，仅仅因为它更简短。一般来说，应该尽可能地使用名称作为轴心。

（PS：指标的名字很好理解，维度怎么理解呢？如果把name想象成横坐标的话，那么dimension就是纵坐标。Tag是一个key/value对，代表指标的一个维度值） 

2.3.  Naming meters（指标命名）

Micrometer使用了一种命名约定，用.分隔小写单词字符。不同的监控系统有不同的命名约定。每个Micrometer的实现都要负责将Micrometer这种以.分隔的小写字符命名转换成对应监控系统推荐的命名。你可以提供一个自己的NamingConvention来覆盖默认的命名转换：
```
registry.config().namingConvention(myCustomNamingConvention);
```
有了命名约定以后，下面这个timer在不同的监控系统中看起来就是这样的：
```
registry.timer("http.server.requests"); 
//在Prometheus中，它是http_server_requests_duration_seconds
```
2.3.1.  Tag naming

假设，我们想要统计HTTP请求数和数据库调用次数，那么可以这样写：
```
registry.counter("database.calls", "db", "users");       // 数据库调用次数
registry.counter("http.requests", "uri", "/api/users");  // HTTP请求数

//第一个参数是name，后面的是tags
public Timer timer(String name, String... tags) {
public Counter counter(String name, String... tags) {
```


2.3.2.  Common tags

Common tags可以被定义在registry级别，并且会被添加到每个监控系统的报告中

预定义的Tags有host , instance , region , stack等
```
registry.config().commonTags("stack", "prod", "region", "us-east-1");
registry.config().commonTags(Arrays.asList(Tag.of("stack", "prod"), Tag.of("region", "us-east-1"))); // 二者等价
```
2.3.4.  Tag values

Tag values must be non-null
标签值必须非空

2.5.  Counters（计数器）

Counter接口允许以固定的数值递增，该数值必须为正数。
```
MeterRegistry registry = new SimpleMeterRegistry();

 //  写法一
 Counter counter = registry.counter("counter");

 //  写法二
 Counter counter = Counter
     .builder("counter")
     .baseUnit("beans") // optional
     .description("a description of what this counter does") // optional
     .tags("region", "test") // optional
     .register(registry);
```

2.5.1.  Function-tracking counters

跟踪单调递增函数的计数器
```
1 Cache cache = ...; // suppose we have a Guava cache with stats recording on
2 registry.more().counter("evictions", tags, cache, c -> c.stats().evictionCount()); // evictionCount()是一个单调递增函数，用于记录缓存被剔除的次数
```

2.6.  Gauges

gauge是获取当前值的句柄。典型的例子是，获取集合、map、或运行中的线程数等。

MeterRegistry接口包含了用于构建gauges的方法，用于观察数字值、函数、集合和map。
```
1 List<String> list = registry.gauge("listGauge", Collections.emptyList(), new ArrayList<>(), List::size); //监视非数值对象
2 List<String> list2 = registry.gaugeCollectionSize("listSize2", Tags.empty(), new ArrayList<>()); //监视集合大小
3 Map<String, Integer> map = registry.gaugeMapSize("mapGauge", Tags.empty(), new HashMap<>());
```
还可以手动加减Gauge
```
1 AtomicInteger n = registry.gauge("numberGauge", new AtomicInteger(0));
2 n.set(1);
3 n.set(2);
```

2.7.  Timers（计时器）

Timer用于测量短时间延迟和此类事件的频率。所有Timer实现至少将总时间和事件次数报告为单独的时间序列。

例如，可以考虑用一个图表来显示一个典型的web服务器的请求延迟情况。服务器可以快速响应许多请求，因此定时器每秒将更新很多次。
```
1 // 方式一
2 public interface Timer extends Meter {
3     ...
4     void record(long amount, TimeUnit unit);
5     void record(Duration duration);
6     double totalTime(TimeUnit unit);
7 }
8
9 // 方式二
10 Timer timer = Timer
11     .builder("my.timer")
12     .description("a description of what this timer does") // optional
13     .tags("region", "test") // optional
14     .register(registry);
```

实际使用
```
//监控调用第三方接口时间的开始
Timer.Sample sample = Timer.start(meterRegistry);
//监控调用第三方接口时间的结束
sample.stop(meterRegistry.timer("online_green_certificate_service_timer", "green_certificate_service_success", "online_green_certificate_service"));
//计数
meterRegistry
	.counter("online_show_userinfo_counter", "show_userinfo_fail", "online_show_userinfo")
	.increment();
```

### vue-element-admin项目
官网文档：https://panjiachen.github.io/vue-element-admin-site/zh/

### git提交PR
1. fork别人仓库
2. clone自己的仓库到本地，并与两个远程仓库分别建立链接;
3. 将修改提交到自己的远程仓库;
4. 别的仓库中点击Pull Requests，再点击New pull requests按钮;
5. Comparing changes界面，Compare需要注意。
6. 填写相关信息，在点击Create pull request按钮即可。

### timestamp和datetime区别
1. 取值范围 
   - timestamp存储需要四个字节，它的取值范围为“1970-01-01 00:00:01” UTC ~ “2038-01-19 03:14:07” （和时区有关） 
   - datetime取值范围为“1000-01-01 00:00:00” ~ “9999-12-31 23:59:59”（和时区无关，怎么存入怎么返回，对程序员友好）
2. timestamp类型还有一个很大的特殊点，就是时间是根据时区来显示的。
例如，在东八区插入的timestamp类型为2009-09-30 14:21:25，在东七区显示时，时间部门就变成了13:21:25，在东九区显示时，时间部门就变成了15:21:25。
3. 需要显示日期与时间，timestamp类型需要根据不同地区的时区来转换时间，但是，timestamp类型的范围太小，其最大时间为2038-01-19 11:14:07。
如果插入时间的比这个大，将会数据库插入0000-00-00 00:00:00。所以需要的时间范围比较大，还是选择dateTime类型比较安全。


### tinyint和bit
TINYINT是8位整数值，BIT字段可以存储1位BIT（1）和64位BIT（64）之间。对于布尔值，BIT（1）很常见。
说明：
如果确定只有2个状态，可以用bit，否则就用tinyint，以防今后就多个状态的可能。
bit 不能建索引，TINYINT可以建索引。

### Spring @Bean注解的使用
自定义bean的命名
默认情况下bean的名称和方法名称相同，你也可以使用name属性来指定

### 常见的spring注入方法
一：目前使用最广泛的 @Autowired和@Resource
```
@Service
public class BaseInfoCompanyFareServiceImpl implements BaseInfoCompanyFareService {

    @Autowired
    private BaseInfoCompanyFareDao baseInfoCompanyFareDao;
```
说明：
@Autowired按byType自动注入，而@Resource默认按 byName自动注入罢了
由于@Autowired 默认第一按照byType(类的类型),第二byName(l类名\类ID)来加载类，所以当存在类型相同,多个beanname时，想注入某个类，就必须指定根据什么beanName查找（使用@Qualifier注解指定），如果不用@Qualifier注解指定，则会以变量名为为beanName进行查找；
备注：@Autowired实现方式是通过 *反射* 来设置属性值

二：构造器注入
```
@Service
public class BaseInfoCompanyPayServiceImpl implements BaseInfoCompanyPayService {

    private final BaseInfoCompanyPayDao baseInfoCompanyPayDao;

    public BaseInfoCompanyPayServiceImpl(BaseInfoCompanyPayDao baseInfoCompanyPayDao) {
        this.baseInfoCompanyPayDao = baseInfoCompanyPayDao;
    }
}
```
通过有参的构造函数注入
备注：构造注入是一种高内聚的体现，特别是针对有些属性需要在对象在创建时候赋值，且后续不允许修改（不提供setter方法）。

三：属性注入
```
@Service
public class BaseInfoCompanyPayServiceImpl implements BaseInfoCompanyPayService {

    private final BaseInfoCompanyPayDao baseInfoCompanyPayDao;

    public BaseInfoCompanyPayServiceImpl( ) {
    }
    
    private void setBaseInfoCompanyPayDao(BaseInfoCompanyPayDao baseInfoCompanyPayDao){
    	this.baseInfoCompanyPayDao = baseInfoCompanyPayDao;
    }
}
```
通过无参构造函数+setter方法注入
备注：SpringContext利用无参的构造函数创建一个对象，然后利用setter方法赋值。所以如果无参构造函数不存在，Spring上下文创建对象的时候便会报错。

四：lombok提供的@RequiredArgsConstructor方式
```
@Service
@RequiredArgsConstructor
public class BaseInfoCompanyServiceImpl implements BaseInfoCompanyService {

    final BaseInfoCompanyDao baseInfoCompanyDao;
```
说明：在注入时生成具有所需参数的构造函数（原理还是利用的构造注入）。必需参数是最终字段和具有约束的字段，例如final和@NonNull注解
在我们写controller或者Service层的时候，需要注入很多的mapper接口或者另外的service接口，这时候就会写很多的@AutoWired注解，@RequiredArgsConstructor避免代码看起来很乱


### logback动态配置日志路径
定义变量
- 在 logback.xml 中定义
```
  <property name="LOGPATH" value="/home/admin/logs" />
```
- 在VM变量中定义
```
  -DLOG_PATH="/Users/luoxiaosheng/logs"
```
- 引入properties/yml文件
```
  <property resource="bootstrap.yml" />
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
  
#配置文件中
LOG_PATH: /Users/luoxiaosheng/logs/
```
- 引入的spring配置变量springProperty️（对比上一种不用引入配置文件，直接引入单个属性就行）
```
  <springProperty scope="context" name="LOG_PATH" source="log.path"></springProperty>
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
  
#配置文件application-*.yml中：
log:
  level: info
  path: /Users/luoxiaosheng/logs/
```

使用变量：${var}
```
<file>/${home}/myApp.log</file>
```
变量的默认值：
在引用一个变量时，如果该变量未定义，需要为其指定默认值，写法是：*${变量名:-默认值}*
```
  <springProperty scop="context" name="springAppName" source="spring.application.name"
    defaultValue=""/>
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
```


### jenkins打包vue 全局工具中找不到node版本
解决办法
去官网找个文件 （文末带百度云共享文件 2018年12月22日10:20:07）

hudson.plugins.nodejs.tools.NodeJSInstaller

这个文件就是nodejs文件版本信息文件

放在jenkins 的目录下 /var/jenkins_home/updates

docker cp ./hudson.plugins.nodejs.tools.NodeJSInstaller jenkins:/var/jenkins_home/updates


### 状态的单词有 status 和 state的区别
status ： 用来描述操作的结果，比如： 成功/失败
state： 用来描述过程的某个阶段，比如 进行中/ 已发送； 处理完成后 “进行中” 就变成 “已发送” 了

### 域名和IP和端口
域名是只能80端口吗
“准确的说,域名什么端口也绑定不了,只能绑定ip地址。你说的80端口是web端口,浏览器访问的默认端口。所以,你用浏览器,浏览器当然只会访问80端口了。
这个不是因为域名解析的问题。web服务器设置的问题。如果80端口已被占用，那就没有办法了。
除非你使用隐藏的域名转发，但是实际还要加 :端口

一台服务器可以被2个域名访问，但一个域名不能同时访问2台服务器。

### java使用okhttp
引入依赖：
```
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>okhttp</artifactId>
  <version>4.9.1</version>
</dependency>
```

工具类：
```
/**
 * okHttp工具类
 *
 * @author soonphe
 * @since 1.0
 */
public class HttpUtil {

    private static final Logger logger = LoggerFactory.getLogger(HttpUtil.class);

    private static OkHttpClient client;
    /**
     * 默认请求json格式
     */
    private static final String DEFAULT_MEDIA_TYPE = "application/json; charset=utf-8";
    /**
     * 连接超时
     */
    private static final int CONNECT_TIMEOUT = 60;
    /**
     * 读取超时
     */
    private static final int READ_TIMEOUT = 100;
    /**
     * 写入超时
     */
    private static final int WRITE_TIMEOUT = 60;

    private static final String GET = "GET";
    private static final String POST = "POST";

    /**
     * 单例模式  获取类实例
     *
     * @return client
     */
    private static OkHttpClient getInstance() {
        if (client == null) {
            synchronized (OkHttpClient.class) {
                if (client == null) {
                    client = new OkHttpClient.Builder()
                            .connectTimeout(CONNECT_TIMEOUT, TimeUnit.SECONDS)
                            .readTimeout(READ_TIMEOUT, TimeUnit.SECONDS)
                            .writeTimeout(WRITE_TIMEOUT, TimeUnit.SECONDS)
                            .connectionPool(new ConnectionPool(32,5,TimeUnit.MINUTES))
                            .build();
                }
            }
        }
        return client;
    }

    /**
     * Get请求
     *
     * @param url
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static String doGet(String url, String callMethod) throws HttpStatusException {

        try {
            long startTime = System.currentTimeMillis();
            addRequestLog(GET, callMethod, url, null, null);

            Request request = new Request.Builder().url(url).build();
            // 创建一个请求
            Response response = getInstance().newCall(request).execute();
            int httpCode = response.code();
            String result;
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new RuntimeException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }

    /**
     * post 请求
     *
     * @param url 请求路径
     * @param postBody body对象
     * @param mediaType 请求类型，默认为application/json
     * @param callMethod 回调方法
     * @return
     * @throws HttpStatusException
     */
    public static String doPost(String url, String postBody, String mediaType, String callMethod) throws HttpStatusException {
        try {
            long startTime = System.currentTimeMillis();
            addRequestLog(POST, callMethod, url, postBody, null);

            MediaType createMediaType = MediaType.parse(mediaType == null ? DEFAULT_MEDIA_TYPE : mediaType);
            Request request = new Request.Builder()
                    .url(url)
                    .post(RequestBody.create(createMediaType, postBody))
                    .build();

            Response response = getInstance().newCall(request).execute();
            int httpCode = response.code();
            String result;
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new IOException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }

    /**
     * Okhttp异步请求
     *
     * @param url
     * @param postBody
     * @param mediaType
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static void doPostAsyc(String url, String postBody, String mediaType, Callback callMethod) throws HttpStatusException {
        try {
            addRequestLog(POST, callMethod.toString(), url, postBody, null);

            MediaType createMediaType = MediaType.parse(mediaType == null ? DEFAULT_MEDIA_TYPE : mediaType);
            Request request = new Request.Builder()
                .url(url)
                .post(RequestBody.create(createMediaType, postBody))
                .build();

            Call call=getInstance().newCall(request);
            call.enqueue(callMethod);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
        }
    }


    /**
     * post请求，form/data数据
     *
     * @param url
     * @param parameterMap
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static String doPost(String url, Map<String, String> parameterMap, String callMethod) throws HttpStatusException {
        try {
            long startTime = System.currentTimeMillis();
            List<String> parameterList = new ArrayList<>();
            FormBody.Builder builder = new FormBody.Builder();
            if (parameterMap.size() > 0) {
                for (Map.Entry<String, String> entry : parameterMap.entrySet()) {
                    String parameterName = entry.getKey();
                    String value = entry.getValue();
                    builder.add(parameterName, value);
                    parameterList.add(parameterName + ":" + value);
                }
            }

            addRequestLog(POST, callMethod, url, null, Arrays.toString(parameterList.toArray()));

            FormBody formBody = builder.build();
            Request request = new Request.Builder()
                    .url(url)
                    .post(formBody)
                    .build();

            Response response = getInstance().newCall(request).execute();
            String result;
            int httpCode = response.code();
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new IOException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);

        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }


    private static void addRequestLog(String method, String callMethod, String url, String body, String formParam) {
        logger.info("===========================request begin================================================");
        logger.info("URI          : {}", url);
        logger.info("Method       : {}", method);
        if (StringUtils.isNotBlank(body)) {
            logger.info("Request body : {}", body);
        }
        if (StringUtils.isNotBlank(formParam)) {
            logger.info("Request param: {}", formParam);
        }
        logger.info("===========================request end================================================");
    }

    private static void addResponseLog(int httpCode, String result, long startTime) {
        logger.info("===========================response begin================================================");
        long endTime = System.currentTimeMillis();
        logger.info("Status       : {}", httpCode);
        logger.info("Response     : {}", result);
        logger.info("Time Cost         : {} ms", endTime - startTime);
        logger.info("===========================response end================================================");
    }

    private static String handleHttpResponse(int httpCode, String result) throws HttpStatusException {
        if (httpCode == HttpStatus.OK.value()) {
            return result;
        }
        HttpStatus httpStatus = HttpStatus.valueOf(httpCode);
        throw new HttpStatusException(httpStatus);
    }

    private static void handleHttpThrowable(Exception ex, String url) throws HttpStatusException {
        if (ex instanceof HttpStatusException) {
            throw (HttpStatusException) ex;
        }
        logger.error("OkHttp error url: " + url, ex);
        if (ex instanceof SocketTimeoutException) {
            throw new RuntimeException("request time out of OkHttp when do url:" + url);
        }
        throw new RuntimeException(ex);
    }

}


/**
 * json解析工具类
 *
 * @author soonphe
 * @since 1.0
 */
public class JsonUtil {

    private JsonUtil() {
    }

    /**
     * 避免指令重排序导致拿到半初始化的对象，保证线程可见性
     */
    private static volatile Gson gson;

    private static Gson getGson() {
        if (gson == null) {
            synchronized (JsonUtil.class) {
                if (gson == null) {
                    gson = new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create();
                }
            }
        }
        return gson;
    }

    public static <T> String toJson(T object) {
        return getGson().toJson(object);
    }


    public static <T> T fromJson(String json, Type type) {
        return getGson().fromJson(json, type);
    }

    public static <T> T fromJson(String json, Class<T> clazz) {
        return getGson().fromJson(json, clazz);
    }
}

/**
 * HttpStatusException
 *
 * @author soonphe
 * @since 1.0
 */
public class HttpStatusException extends Exception {

    private HttpStatus httpStatus;


    private String result;

    public HttpStatusException(HttpStatus httpStatus) {
        this.httpStatus = httpStatus;
    }

    public HttpStatusException(HttpStatus httpStatus, String result) {
        this.httpStatus = httpStatus;

    }

    public HttpStatus getHttpStatus() {
        return httpStatus;
    }

    public String getResult() {
        return result;
    }
}
```
请求示例
```
//同步请求
HttpUtil.doPost(url,JsonUtil.toJson(orderQueryRequest), null, null);

//异步请求
HttpUtil.doPostAsyc(url,
	JsonUtil.toJson(orderQueryRequest), null, new Callback() {
		@Override
		public void onFailure(Call call, IOException e) {
			log.error("fail", e.toString());
		}

		@Override
		public void onResponse(Call call, Response response) throws IOException {
			log.info("success", response.body());
			String result;
			ResponseBody body = response.body();
			if (body != null) {
				result = body.string();
				log.info("result:" + result);
			}
		}
	});
```


### SpringBoot中CommandLineRunner的作用
> 平常开发中有可能需要实现在项目启动后执行的功能，SpringBoot提供的一种简单的实现方案就是添加一个model并实现CommandLineRunner接口，实现功能的代码放在实现的run方法中

代码实现
``` java
package org.springboot.sample.runner;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class MyStartupRunner implements CommandLineRunner {

	@Override
	public void run(String... args) throws Exception {
		System.out.println(">>>>>>>>>>>>>>>服务启动执行，执行加载数据等操作<<<<<<<<<<<<<");
	}

}
```

### @Bean(initMethod = "init")的作用
init-method="init"  destroy-method="close" 作用：

init-method="init"是指bean被初始化时执行的方法，当bean实例化后,执行init-method用于初始化数据库连接池。

destroy-method="close" 是指bean被销毁时执行的方法   Spring容器关闭时调用该方法即调用close()将连接关闭。

### Spring JdbcTemplate 方法详解
JdbcTemplate主要提供以下五类方法：
- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

JdbcTemplate类支持的回调类：
预编译语句及存储过程创建回调：用于根据JdbcTemplate提供的连接创建相应的语句；
PreparedStatementCreator：通过回调获取JdbcTemplate提供的Connection，由用户使用该Conncetion创建相关的PreparedStatement；
CallableStatementCreator：通过回调获取JdbcTemplate提供的Connection，由用户使用该Conncetion创建相关的CallableStatement；
预编译语句设值回调：用于给预编译语句相应参数设值；
PreparedStatementSetter：通过回调获取JdbcTemplate提供的PreparedStatement，由用户来对相应的预编译语句相应参数设值；
BatchPreparedStatementSetter：；类似于PreparedStatementSetter，但用于批处理，需要指定批处理大小；
自定义功能回调：提供给用户一个扩展点，用户可以在指定类型的扩展点执行任何数量需要的操作；
ConnectionCallback：通过回调获取JdbcTemplate提供的Connection，用户可在该Connection执行任何数量的操作；
StatementCallback：通过回调获取JdbcTemplate提供的Statement，用户可以在该Statement执行任何数量的操作；
PreparedStatementCallback：通过回调获取JdbcTemplate提供的PreparedStatement，用户可以在该PreparedStatement执行任何数量的操作；
CallableStatementCallback：通过回调获取JdbcTemplate提供的CallableStatement，用户可以在该CallableStatement执行任何数量的操作；
结果集处理回调：通过回调处理ResultSet或将ResultSet转换为需要的形式；
RowMapper：用于将结果集每行数据转换为需要的类型，用户需实现方法mapRow(ResultSet rs, int rowNum)来完成将每行数据转换为相应的类型。
RowCallbackHandler：用于处理ResultSet的每一行结果，用户需实现方法processRow(ResultSet rs)来完成处理，在该回调方法中无需执行rs.next()，该操作由JdbcTemplate来执行，用户只需按行获取数据然后处理即可。
ResultSetExtractor：用于结果集数据提取，用户需实现方法extractData(ResultSet rs)来处理结果集，用户必须处理整个结果集；


### @Component默认是单例还是多例？
@Component注解默认实例化的对象是单例，如果想声明成多例对象可以使用@Scope("prototype")
@Repository默认单例
@Service默认单例
@Controller默认多例


### SpringMVC 默认创建 Bean 是单例的，在高并发情况下是如何保证性能的？
spring中的bean默认是单例的，通常对单例进行多线程访问时，为了线程安全而采用同步机制，以时间换空间的方式，而Spring中是利用ThreadLocal来以空间换取时间，为每一个线程提供变量副本，来保证变量副本对于某一线程都是线程安全的。

用ThreadLocal是为了保证线程安全，实际上ThreadLoacal的key就是当前线程的Thread实例。

虽然spring对象是单例的，但类里面方法对每个线程来说都是独立运行的，不存在多线程问题，只有成员变量有多线程问题，所以方法里面如果有用到成员变量就要考虑用安全的数据结构。

单例模式是spring推荐的配置，单利模式因为大大节省了实例的创建和销毁，它在高并发下能极大的节省资源，提高服务抗压能力。


### mac打印
隔空打印：在“隔空打印”协议下，可通过 Wi-Fi、USB 和以太网络访问打印机的打印和扫描选项（若特定的打印机支持这些功能）。你无需下载或安装打印机软件就能使用支持“隔空打印”的打印机。支持“隔空打印”协议的打印机类型广泛，包括 Aurora、Brother、Canon、Dell、Epson、Fuji、Hewlett Packard、Samsung、Xerox 等等。 
互联网打印协议 - IPP：现代打印机和打印服务器使用此协议；
行式打印机监控程序 - LPD：旧式打印机和打印服务器可能使用此协议；
HP Jetdirect – Socket：HP 和其他许多打印机制造商都使用此协议。


### Spring FactoryBean和BeanFactory 区别
1. BeanFactory 是ioc容器的底层实现接口，是顶层容器（根容器），不能被实例化，不允许我们直接操作 BeanFactory bean工厂，它定义了所有 IoC 容器 必须遵从 的⼀套原则，具体的容器实现可以增加额外的功能。

  	BeanFactory 接口又衍生出以下接口
    - ApplicationContext，ApplicationContext包含BeanFactory的所有功能,同时还进行更多的扩展， 
      - ClassPathXmlApplicationContext 包含了解析 xml 等⼀系列的内容
      - AnnotationConfigApplicationContext 则是包含了注解解析等⼀系列的内容。Spring IoC 容器继承体系

2. FactoryBean 是spirng提供的工厂bean的一个接口
FactoryBean 接口提供三个方法，用来创建对象。 
FactoryBean 具体返回的对象是由getObject 方法决定的。 
```
public interface FactoryBean<T> {

	//创建的具体bean对象的类型
	@Nullable
	T getObject() throws Exception;

	//工厂bean 具体创建具体对象是由此getObject()方法来返回的
	@Nullable
	Class<?> getObjectType();
	
 	//是否单例
	default boolean isSingleton() {
		return true;
	}

}
```

### Java8之Consumer、Supplier、Predicate和Function

这几个接口都在 java.util.function 包下的，分别是
- Consumer<T>（消费型）

  Consumer<T>接口就是一个消费型的接口，实现 accept 的方法，T：入参类型；没有出参。
  ```
  Consumer<String> consumer = new Consumer<String>() {
	
                @Override
                public void accept(String s) {
                    System.out.println(s);
                }
            };
    //lambda表达式
	Consumer<String> consumer1 = (s) -> System.out.println(s);//lambda表达式返回的就是一个Consumer接口
  ```
  
- Supplier<T>（供给型）

  Supplier<T> 接口是一个供给型的接口，实现 get 方法。T：出参类型；没有入参。可以用来存储数据，然后可以供其他方法使用的这么一个接口
  ```
  Supplier<Integer> supplier = () -> new Random().nextInt();
  ```
  
- Predicate<T>（谓词型）

  Predicate<T> 接口是一个谓词型接口，实现一个 test 方法。T：入参类型；出参类型是Boolean。作用：类似于 bool 类型的判断的接口。
  ```
  Predicate<Integer> predicate = (t) -> t > 5;
  ```
  
- Function<T, R> （功能性）

  Function<T, R>  接口是一个功能型接口，实现 apply 方法。T：入参类型，R：出参类型。作用：将输入数据转换成另一种形式的输出数据。
  ```
  Function<String, Integer> function = new Function<String, Integer>() {
            @Override
            public Integer apply(String s) {
                return s.length();//获取每个字符串的长度，并且返回
            }
        };
  
  ```

### 反射操作如何得到对象、方法、参数
- 反射获取对象
```
//注意，要传完整类名，如：com.soonphe.timber.hello.Helloworld
Class cls = Class.forName("完整类名");
Object yourObj = cls.newInstance();
```
- 反射获取方法
```
Method methlist[] = cls.getDeclaredMethods();
for (int i = 0; i < methlist.length; i++) {
	Method m = methlist[i];
}
```
- 反射获取方法参数
```
//获取到方法对象,假设方法的参数是一个int,method名为setAge
Method sAge = cls.getMethod("setAge", new Class[] {int.class});
//构造参数Object
Object[] arguments = new Object[] { new Integer(37)};
//执行方法
sAge.invoke(yourObj , arguments);
```


### 反射时getConstructor()与getDeclaredConstructor()方法的区别及setAccessible()方法的作用
```
	@Test
	void testName() throws Exception {
		HelloWorld world=null;
		String className="hello.HelloWorld";
		Constructor con=Class.forName(className).getConstructor();
		//设置不检查是否可以访问
		con.setAccessible(true);
		Object obj=con.newInstance();
		world=(HelloWorld) obj;
		world.sayHello();
	}
```
**getConstructor()** 和 **getDeclaredConstructor()** 的作用：获取类的构造器	

不同点：
- Class类的getConstructor()方法,无论是否设置setAccessible(),都不可获取到类的私有构造器.
- Class类的getDeclaredConstructor()方法,可获取到类的私有构造器(包括带有其他修饰符的构造器），但在使用private的构造器时，必须设置setAccessible()为true,才可以获取并操作该Constructor对象。


### <? extends U> 和 <? super U>的区别：
- <? super U> ：规定元素的最小粒度下限，不影响往里存，但往外取只能放在Object对象里，如Number super Integer。
```
Box<? super RedApple> box3 = new Box<Fruit>();
```

- <? extends U>：规定元素的最大粒度上限，不影响取，存只能存指定类型的变量，如：Integer extends Number。
```
Box<? extends Food> box1 = new Box<Fruit>();
Box<? extends Food> box2 = new Box<Meat>();
```

### spring-boot-maven-plugin打两个包jar/exec.jar
参考文档：https://blog.csdn.net/guduyishuai/article/details/60968728

一、spring-boot-maven-plugin打包出来的jar是不可依赖的

         比如我有一个root工程，type为pom，下面两个spring-boot工程作为它的module，分别为moduleA和moduleB。假如moduleA依赖于moduleB。如果你在moduleB中使用了spring-boot-maven-plugin的默认配置build，或者在root中使用spring-boot-maven-plugin的默认配置build。很遗憾，你在clean package的时候会发现moduleA找不到moduleB中的类。原因就是默认打包出来的jar是不可依赖的。

         解决方案：

        1、调整你的代码，把spring-boot的东西从moduleB中移走。官方文档是这样说的，但是大部分人不会

              这么干吧。

        2、官方告诉我们，你如果不想移代码，好吧，我这样来给你解决，给你打两个jar包，一个用来直接执

              行，一个用来依赖。于是，你需要指定一个属性classifier，这个属性为可执行jar包的名字后缀。比

              如我设置<classifier>exec</classifier>，原项目名为Vehicle-business。

              那么我会得到两个jar：Vehicle-business.jar和Vehicle-bussiness-exec.jar

         官方文档位置：84.5 Use a Spring Boot application as a dependency

		总结：回到聚合maven上，如果你在root工程中使用了spring-boot-maven-plugin作为builder，那么你的依赖module一定要用解决方案二来设置。否则你不在root工程中用spring-boot-maven-plugin作为builder，而在需要打包的module上使用。


### 后台运行nohup & 和守护进程daemon 
1)守护进程已经完全脱离终端控制台了，而后台程序并未完全脱离终端，在终端未关闭前还是会往终端输出结果
2)守护进程在关闭终端控制台时不会受影响，而后台程序会随用户退出而停止，需要在以nohup xxx & 格式运行才能避免影响（&后台运行）
3)守护进程的会话组和当前目录，文件描述符都是独立的。后台运行只是终端进行了一次fork，让程序在后台执行，这些都没改变。


### swagger注解
类注解：
@Api(tags="项目任务/风险")
方法注解：
@ApiOperation(value="任务列表",notes="任务列表")


### package-info.java：提供包级别注解、变量、注释

### JdbcTemplate方法详解
JdbcTemplate主要提供以下五类方法：
execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
query方法及queryForXXX方法：用于执行查询相关语句；
call方法：用于执行存储过程、函数相关语句。

### Okhttp连接超时
错误日志
```
2021-09-23 16:18:13.863||[HSFBizProcessor-DEFAULT-8-thread-19]||| OkHttp error url: http://xxx.gateway.com:9966/api/query/es/getOrderListByQuery
java.net.SocketTimeoutException: timeout
```
- 解决方案：
修改最大连接数：
```
newOkHttpClient.Builder()
.connectTimeout(CONNECT_TIMEOUT,TimeUnit.SECONDS)
.readTimeout(READ_TIMEOUT,TimeUnit.SECONDS)
.connectionPool(newConnectionPool(32,5,TimeUnit.MINUTES))
.build();
```
- 参考文档：
https://blog.csdn.net/Vincent2014Linux/article/details/98881462


### java date和LocalDateTime、LocalDate、LocalTime转换
LocalDateTime只能是日期和时间
LocalDate是日期
LocalTime是时间
```
DateTimeFormatter df=DateTimeFormatter.ofPattern("HH:mm:ss");
localTime.format(df)
```

### datetime转localDatetime
```
getPaidTime().toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime()
```
localDatetime转datetime:
```
Date.from(ordOrderConsume.getCreateTime().atZone(ZoneId.systemDefault()).toInstant())
```
LocalDateTime.now()：获取当前时间

### 更改启动组
更改启动组：chown -R admin:admin /home/admin/logs/

### CompletableFuture
使用Future获得异步执行结果时，要么调用阻塞方法get()，要么轮询看isDone()是否为true，这两种方法都不是很好，因为主线程也会被迫等待。

从Java 8开始引入了CompletableFuture，它针对Future做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。

### mybatis多数据源
https://mp.weixin.qq.com/s/qN1b5iSkkIhkA03RtppiJg

### springbatch配置mybatis数据源
MyBatisCursorItemReader reader = new MyBatisCursorItemReader();

### builder模式如何强制分步调用
builder已step为过程构造——以接口为返参，分步调用

### node-sass安装失败：
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/

### litepal操作
取：
LitePal.findAll(TGame.class);
LitePal.order("id desc").limit(1).findFirst(TDataVersion.class);
LitePal.where("phone = ?", phone + "").order("id desc").findFirst(TStats.class);
LitePal.findFirst(TAdvert.class) != null) {
LitePal.deleteAll(TAdvert.class);

存：object.save


#### MissingServletRequestParameterException报错
解决:不要将HttpServletRequest传递到任何异步方法中！


#### mysql中int(1)指定的是数据长度么？

不是，其实这个int(1)和varchar(1)是不一样的，对于int型，不管你设计多少长度，它永远需要占用4个字节，默认就是11位，所以其实这个int(1)控制的不是数据的长度，而是数据的显示长度，它指明了mysql最大可能显示的数字个数。

所以如果不是特别必要，数据库的int型不加上长度设计也是可以的。


#### MyBatis @Param 注解，使用场景

第一种：方法有多个参数，需要 @Param 注解

例如下面这样：

@Mapper
public interface UserMapper {
Integer insert(@Param("username") String username, @Param("address") String address);
}
对应的 XML 文件如下：
```
<insert id="insert" parameterType="org.javaboy.helloboot.bean.User">
    insert into user (username,address) values (#{username},#{address});
</insert>
```
这是最常见的需要添加 @Param 注解的场景。

第二种：方法参数要取别名，需要 @Param 注解
当需要给参数取一个别名的时候，我们也需要 @Param 注解，例如方法定义如下：
```
@Mapper
public interface UserMapper {
User getUserByUsername(@Param("name") String username);
}
```
对应的 XML 定义如下：
```
<select id="getUserByUsername" parameterType="org.javaboy.helloboot.bean.User">
    select * from user where username=#{name};
</select>
```

第三种：XML 中的 SQL 使用了 $ ，那么参数中也需要 @Param 注解
$ 会有注入漏洞的问题，但是有的时候你不得不使用 $ 符号，例如要传入列名或者表名的时候，这个时候必须要添加 @Param 注解，例如：
```
@Mapper
public interface UserMapper {
List<User> getAllUsers(@Param("order_by")String order_by);
}
```
对应的 XML 定义如下：
```
<select id="getAllUsers" resultType="org.javaboy.helloboot.bean.User">
    select * from user
 <if test="order_by!=null and order_by!=''">
        order by ${order_by} desc
 </if>
</select>
```
前面这三种，都很容易懂，相信很多小伙伴也都懂，除了这三种常见的场景之外，还有一个特殊的场景，经常被人忽略。

第四种，那就是动态 SQL ，如果在动态 SQL 中使用了参数作为变量，那么也需要 @Param 注解，即使你只有一个参数。
如果我们在动态 SQL 中用到了 参数作为判断条件，那么也是一定要加 @Param 注解的，例如如下方法：
```
@Mapper
public interface UserMapper {
List<User> getUserById(@Param("id")Integer id);
}
```
定义出来的 SQL 如下：
```
<select id="getUserById" resultType="org.javaboy.helloboot.bean.User">
    select * from user
 <if test="id!=null">
        where id=#{id}
 </if>
</select>
```
这种情况，即使只有一个参数，也需要添加 @Param 注解，而这种情况却经常被人忽略！





