# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## ElasticJob
官网地址：https://shardingsphere.apache.org/elasticjob/
github地址：https://github.com/apache/shardingsphere-elasticjob

### 简介
ElasticJob 是一个分布式调度解决方案，由 2 个相互独立的子项目 ElasticJob-Lite 和 ElasticJob-Cloud 组成。

ElasticJob-Lite 定位为轻量级无中心化解决方案，使用jar的形式提供分布式任务的协调服务；
ElasticJob-Cloud 使用 Mesos 的解决方案，额外提供资源治理、应用分发以及进程隔离等服务。

ElasticJob 的各个产品使用统一的作业 API，开发者仅需要一次开发，即可随意部署。

### 功能列表
- 弹性调度
  - 支持任务在分布式场景下的分片和高可用
  - 能够水平扩展任务的吞吐量和执行效率
  - 任务处理能力随资源配备弹性伸缩

- 资源分配
  - 在适合的时间将适合的资源分配给任务并使其生效
  - 相同任务聚合至相同的执行器统一处理
  - 动态调配追加资源至新分配的任务

- 作业治理
  - 失效转移
  - 错过作业重新执行
  - 自诊断修复

- 作业依赖(TODO)
  - 基于有向无环图（DAG）的作业间依赖
  - 基于有向无环图（DAG）的作业分片间依赖
  
- 作业开放生态
  - 可扩展的作业类型统一接口
  - 丰富的作业类型库，如数据流、脚本、HTTP、文件、大数据等
  - 易于对接业务作业，能够与 Spring 依赖注入无缝整合

- 可视化管控端
  - 作业管控端
  - 作业执行历史数据追踪
  - 注册中心管理

elasticjob和xxl-job
- 共同点： 
  - E-Job和X-job都有广泛的用户基础和完整的技术文档，都能满足定时任务的基本功能需求。
- 不同点：
  - X-Job 侧重的业务实现的简单和管理的方便，学习成本简单，失败策略和路由策略丰富。推荐使用在“用户基数相对少，服务器数量在一定范围内”的情景下使用
  - E-Job 关注的是数据，增加了弹性扩容和数据分片的思路，以便于更大限度的利用分布式服务器的资源。但是学习成本相对高些，推荐在“数据量庞大，且部署服务器数量较多”时使用


### 所需资源

#### 网页控制台
- 网页控制台
官网地址：https://github.com/apache/shardingsphere-elasticjob-ui
操作步骤
```bash
git clone https://github.com/apache/shardingsphere-elasticjob-ui.git
cd shardingsphere-elasticjob-ui/
mvn clean package -Prelease
```
- 获取压缩包 `shardingsphere-elasticjob-ui/shardingsphere-elasticjob-ui-distribution/shardingsphere-elasticjob-lite-ui-bin-distribution/target/apache-shardingsphere-${latest.release.version}-shardingsphere-elasticjob-lite-ui-bin.tar.gz`

连接数据库
受协议限制，一些数据库的JDBC驱动程序无法直接添加到项目中，用户需要自行添加。有两种方法：

- 添加JDBC Driver依赖到 pom.xml然后编译
Add JDBC driver dependency to [shardingsphere-elasticjob-lite-ui/shardingsphere-elasticjob-lite-ui-backend/pom.xml](https://github.com/apache/shardingsphere-elasticjob-ui/blob/master/shardingsphere-elasticjob-lite-ui/shardingsphere-elasticjob-lite-ui-backend/pom.xml) and build.

- Add JDBC Driver JAR to ext-lib in Binary Distribution Package
1. Get `apache-shardingsphere-${latest.release.version}-shardingsphere-elasticjob-lite-ui-bin.tar.gz` and extract.
2. Add JDBC Driver (Such as `mysql-connector-java-8.0.13.jar`) to directory `ext-lib`.
3. Run application with `bin/start.sh`

2核2g即可，只是用于后台操作，能运行的最低配置	
用于任务管理控制台，只是负责启停，和其他的监控，不负责任务的执行

- 国内安装命令
```
wget https://mirrors.tuna.tsinghua.edu.cn/apache/shardingsphere/elasticjob-ui-3.0.0-beta/apache-shardingsphere-elasticjob-3.0.0-beta-lite-ui-bin.tar.gz
解压后进入bin 目录，然后启动./start.sh
```
控制台访问网址：http://127.0.0.1:8088/#/
用户名 root 密码root
注册中心配置-添加注册中心（输入注册中心名称、地址、命名空间）

注意：默认为h2数据库，即内存存储，修改数据库driver和登录账户密码操作application.properties配置即可

#### zeekeeper集群
3个节点的高可用zookeeper集群，负责elasticjob 任务的注册管理


### 依赖
```
        <dependency>
			<groupId>org.apache.shardingsphere.elasticjob</groupId>
			<artifactId>elasticjob-lite-core</artifactId>
			<version>3.0.0-beta</version>
		</dependency>
		<!-- 按需引入 -->
		<dependency>
            <groupId>org.apache.shardingsphere.elasticjob</groupId>
            <artifactId>elasticjob-cloud-executor</artifactId>
            <version>${latest.release.version}</version>
        </dependency>
        <!-- spring命名空间 -->
		<dependency>
			<groupId>org.apache.shardingsphere.elasticjob</groupId>
			<artifactId>elasticjob-lite-spring-namespace</artifactId>
			<version>3.0.0-beta</version>
		</dependency>
```

### 配置文件
```
elasticjob:
  tracing:
    type: RDB #追踪类型
  regCenter:
    serverLists: 192.168.161.224:2181,192.168.161.44:2181,192.168.161.112:2181  #ZooKeeper的访问地址
    namespace: cluster
    maxSleepTime: 40000
    baseSleepTime: 20000
    namespace: ${spring.application.name} #避免任务名称相同报错，哪怕实现类不同也会报错
```
注册中心配置项

|属性名	|类型	|缺省值	|描述|
|---|---|---|---|
|serverLists|	String	||	连接 ZooKeeper 服务器的列表|
|namespace	|String		||ZooKeeper 的命名空间|
|baseSleepTimeMilliseconds	|int	|1000	|等待重试的间隔时间的初始毫秒数|
|maxSleepTimeMilliseconds	|String	|3000	|等待重试的间隔时间的最大毫秒数|
|maxRetries|	String	|3	|最大重试次数
|sessionTimeoutMilliseconds	|boolean|	60000|	会话超时毫秒数|
|connectionTimeoutMilliseconds|	boolean	|15000	|连接超时毫秒数|
|digest	|String	|无需验证	|连接 ZooKeeper 的权限令牌|

**namespace说明**
ZooKeeper中注册任务的时候，真正冲突的并不纯粹是因为任务名称，而是namespace + 任务名称，全部一样，才会出现问题。
通常，我们在规划各个Spring Boot应用的时候，都会做好唯一性的规划，这样未来注册到Eureka、Nacos等注册中心的时候，也可以保证唯一。


### 作业开发
- 简单作业
```
public class MyJob implements SimpleJob {
    
    @Override
    public void execute(ShardingContext context) {
        switch (context.getShardingItem()) {
            case 0: 
                // do something by sharding item 0
                break;
            case 1: 
                // do something by sharding item 1
                break;
            case 2: 
                // do something by sharding item 2
                break;
            // case n: ...
        }
    }
}
```

- 数据流作业
```java
public class MyElasticJob implements DataflowJob<Foo> {
    
    @Override
    public List<Foo> fetchData(ShardingContext context) {
        switch (context.getShardingItem()) {
            case 0: 
                List<Foo> data = // get data from database by sharding item 0
                return data;
            case 1: 
                List<Foo> data = // get data from database by sharding item 1
                return data;
            case 2: 
                List<Foo> data = // get data from database by sharding item 2
                return data;
            // case n: ...
        }
    }
    
    @Override
    public void processData(ShardingContext shardingContext, List<Foo> data) {
        // process data
        // ...
    }
}
```
可通过属性配置 streaming.process 开启或关闭流式处理。

如果开启流式处理，则作业只有在 fetchData 方法的返回值为 null 或集合容量为空时，才停止抓取，否则作业将一直运行下去； 如果关闭流式处理，则作业只会在每次作业执行过程中执行一次 fetchData 和 processData 方法，随即完成本次作业。

如果采用流式作业处理方式，建议 processData 在处理数据后更新其状态，避免 fetchData 再次抓取到，从而使得作业永不停止。

- 脚本作业

支持 shell，python，perl 等所有类型脚本。 可通过属性配置 script.command.line 配置待执行脚本，无需编码。 执行脚本路径可包含参数，参数传递完毕后，作业框架会自动追加最后一个参数为作业运行时信息。

```shell
#!/bin/bash
echo sharding execution context is $*

```

- HTTP作业（3.0.0-beta 提供）

可通过属性配置http.url,http.method,http.data等配置待请求的http信息。 分片信息以Header形式传递，key为shardingContext，值为json格式。

```java
public class HttpJobMain {
    
    public static void main(String[] args) {
        
        new ScheduleJobBootstrap(regCenter, "HTTP", JobConfiguration.newBuilder("javaHttpJob", 1)
                .setProperty(HttpJobProperties.URI_KEY, "http://xxx.com/execute")
                .setProperty(HttpJobProperties.METHOD_KEY, "POST")
                .setProperty(HttpJobProperties.DATA_KEY, "source=ejob")
                .cron("0/5 * * * * ?").shardingItemParameters("0=Beijing").build()).schedule();
    }
}
```

### 作业配置
基于cron表达式
```
    JobConfiguration jobConfig = JobConfiguration.newBuilder("MyJob", 3).cron("0/5 * * * * ?").build();
```

#### 作业配置项
|属性名	|类型	|缺省值	|描述|
|---|---|---|---|
|jobName	|String	|	|作业名称|
|shardingTotalCount|	int	||	作业分片总数|
|cron	|String		||CRON 表达式，用于控制作业触发时间|
|timeZone	|String	||	CRON 的时区设置|
|shardingItemParameters	|String	||	个性化分片参数|
|jobParameter	|String	||	作业自定义参数|
|monitorExecution	|boolean	|true	|监控作业运行时状态|
|failover	|boolean	|false	|是否开启任务执行失效转移|
|misfire	|boolean	|true|	是否开启错过任务重新执行|
|maxTimeDiffSeconds|	int	|-1（不检查）|	最大允许的本机与注册中心的时间误差秒数|
|reconcileIntervalMinutes	|int|	10	|修复作业服务器不一致状态服务调度间隔分钟|
|jobShardingStrategyType	|String|	AVG_ALLOCATION	|作业分片策略类型|
|jobExecutorServiceHandlerType	|String	|CPU|	作业线程池处理策略|
|jobErrorHandlerType	|String	||	作业错误处理策略|
|description	|String		||作业描述信息|
|props	|Properties		||作业属性配置信息|
|disabled	|boolean|	false|	作业是否禁止启动|
|overwrite	|boolean|	false|	本地配置是否可覆盖注册中心配置|

核心配置项说明

shardingItemParameters:
分片序列号和参数用等号分隔，多个键值对用逗号分隔。 分片序列号从0开始，不可大于或等于作业分片总数。 如：0=a,1=b,2=c

jobParameter:
可通过传递该参数为作业调度的业务方法传参，用于实现带参数的作业 例：每次获取的数据量、作业实例从数据库读取的主键等。

monitorExecution:
每次作业执行时间和间隔时间均非常短的情况，建议不监控作业运行时状态以提升效率。 因为是瞬时状态，所以无必要监控。请用户自行增加数据堆积监控。并且不能保证数据重复选取，应在作业中实现幂等性。 每次作业执行时间和间隔时间均较长的情况，建议监控作业运行时状态，可保证数据不会重复选取。

maxTimeDiffSeconds:
如果时间误差超过配置秒数则作业启动时将抛异常。

reconcileIntervalMinutes:
在分布式的场景下由于网络、时钟等原因，可能导致 ZooKeeper 的数据与真实运行的作业产生不一致，这种不一致通过正向的校验无法完全避免。 需要另外启动一个线程定时校验注册中心数据与真实作业状态的一致性，即维持 ElasticJob 的最终一致性。
配置为小于 1 的任意值表示不执行修复。

jobShardingStrategyType:
详情请参见内置分片策略列表。

jobExecutorServiceHandlerType:
详情请参见内置线程池策略列表。

jobErrorHandlerType:
详情请参见内置错误处理策略列表。

props:
详情请参见作业属性配置列表。

disabled:
可用于部署作业时，先禁止启动，部署结束后统一启动。

overwrite:
如果可覆盖，每次启动作业都以本地配置为准。


### 作业调度
#### 定时调度
```
public class MyJobDemo {
    
  public static void main(String[] args) {
        // 调度基于 class 类型的作业
        new ScheduleJobBootstrap(createRegistryCenter(), new MyJob(), createJobConfiguration()).schedule();
        // 调度基于 type 类型的作业
        new ScheduleJobBootstrap(createRegistryCenter(), "MY_TYPE", createJobConfiguration()).schedule();
    }
    
    private static CoordinatorRegistryCenter createRegistryCenter() {
        CoordinatorRegistryCenter regCenter = new ZookeeperRegistryCenter(new ZookeeperConfiguration("zk_host:2181", "my-job"));
        regCenter.init();
        return regCenter;
    }
    
    private static JobConfiguration createJobConfiguration() {
        // 创建作业配置
        ...
        //JobConfiguration jobConfig = JobConfiguration.newBuilder("MyJob", 3).cron("0/5 * * * * ?").build();
    }
}
```
#### 一次性调度
```java
public class JobDemo {
    
    public static void main(String[] args) {
        OneOffJobBootstrap jobBootstrap = new OneOffJobBootstrap(createRegistryCenter(), new MyJob(), createJobConfiguration());
        // 可多次调用一次性调度
        jobBootstrap.execute();
        jobBootstrap.execute();
        jobBootstrap.execute();
    }
    
    private static CoordinatorRegistryCenter createRegistryCenter() {
        CoordinatorRegistryCenter regCenter = new ZookeeperRegistryCenter(new ZookeeperConfiguration("zk_host:2181", "elastic-job-demo"));
        regCenter.init();
        return regCenter;
    }
    
    private static JobConfiguration createJobConfiguration() {
        // 创建作业配置
        ...
    }
}
```

### 配置作业导出端口和配置错误处理策略
#### 配置作业导出端口 

使用 ElasticJob-Lite 过程中可能会碰到一些分布式问题，导致作业运行不稳定。

由于无法在生产环境调试，通过 dump 命令可以把作业内部相关信息导出，方便开发者调试分析；

导出命令的使用请参见运维指南。

以下示例用于展示如何通过 SnapshotService 开启用于导出命令的监听端口。
```
public class JobMain {
    
    public static void main(final String[] args) {
        SnapshotService snapshotService = new SnapshotService(regCenter, 9888).listen();
    }
    
    private static CoordinatorRegistryCenter createRegistryCenter() {
        // 创建注册中心
    }
}
```
#### 配置错误处理策略
使用 ElasticJob-Lite 过程中当作业发生异常后，可采用以下错误处理策略。

|错误处理策略名称	|说明	|是否内置	|是否默认	|是否需要额外配置|
|---|---|---|---|---|
|记录日志策略	|记录作业异常日志，但不中断作业执行|	是|	是|
|抛出异常策略	|抛出系统异常并中断作业执行|	是|	|	|
|忽略异常策略	|忽略系统异常且不中断作业执行|	是|	|	|
|邮件通知策略	|发送邮件消息通知，但不中断作业执行|		| |	是|
|企业微信通知策略	|发送企业微信消息通知，但不中断作业执行|	| |		是|
|钉钉通知策略	|发送钉钉消息通知，但不中断作业执行	|	| |	是|

记录日志策略
```java
public class JobDemo {
    
    public static void main(String[] args) {
        //  定时调度作业
        new ScheduleJobBootstrap(createRegistryCenter(), new MyJob(), createScheduleJobConfiguration()).schedule();
        // 一次性调度作业
        new OneOffJobBootstrap(createRegistryCenter(), new MyJob(), createOneOffJobConfiguration()).execute();
    }
    
    private static JobConfiguration createScheduleJobConfiguration() {
        // 创建定时作业配置， 并且使用记录日志策略
        return JobConfiguration.newBuilder("myScheduleJob", 3).cron("0/5 * * * * ?").jobErrorHandlerType("LOG").build();
    }

    private static JobConfiguration createOneOffJobConfiguration() {
        // 创建一次性作业配置， 并且使用记录日志策略
        return JobConfiguration.newBuilder("myOneOffJob", 3).jobErrorHandlerType("LOG").build();
    }

    private static CoordinatorRegistryCenter createRegistryCenter() {
        // 配置注册中心
        ...
    }
}
```

抛出异常策略
```java
public class JobDemo {
    
    public static void main(String[] args) {
        //  定时调度作业
        new ScheduleJobBootstrap(createRegistryCenter(), new MyJob(), createScheduleJobConfiguration()).schedule();
        // 一次性调度作业
        new OneOffJobBootstrap(createRegistryCenter(), new MyJob(), createOneOffJobConfiguration()).execute();
    }
    
    private static JobConfiguration createScheduleJobConfiguration() {
        // 创建定时作业配置， 并且使用抛出异常策略
        return JobConfiguration.newBuilder("myScheduleJob", 3).cron("0/5 * * * * ?").jobErrorHandlerType("THROW").build();
    }

    private static JobConfiguration createOneOffJobConfiguration() {
        // 创建一次性作业配置， 并且使用抛出异常策略
        return JobConfiguration.newBuilder("myOneOffJob", 3).jobErrorHandlerType("THROW").build();
    }

    private static CoordinatorRegistryCenter createRegistryCenter() {
        // 配置注册中心
        ...
    }
}
```

### 作业API使用Spring Boot Starter
ElasticJob-Lite 提供自定义的 Spring Boot Starter，可以与 Spring Boot 配合使用。 基于 ElasticJob Spring Boot Starter 使用 ElasticJob ，用户无需手动创建 CoordinatorRegistryCenter、JobBootstrap 等实例， 只需实现核心作业逻辑并辅以少量配置，即可利用轻量、无中心化的 ElasticJob 解决分布式调度问题。

作业配置

作业逻辑实现与 ElasticJob 的其他使用方式并没有较大的区别，只需将当前作业注册为 Spring 容器中的 bean。

在配置文件中指定 ElasticJob 所使用的 Zookeeper。配置前缀为 elasticjob.reg-center。

elasticjob.jobs 是一个 Map，key 为作业名称，value 为作业类型与配置。 Starter 会根据该配置自动创建 OneOffJobBootstrap 或 ScheduleJobBootstrap 的实例并注册到 Spring 容器中。

```
elasticjob:
  regCenter:
    serverLists: localhost:6181
    namespace: elasticjob-lite-springboot
  jobs:
    dataflowJob:
      elasticJobClass: org.apache.shardingsphere.elasticjob.dataflow.job.DataflowJob
      cron: 0/5 * * * * ?
      shardingTotalCount: 3
      shardingItemParameters: 0=Beijing,1=Shanghai,2=Guangzhou
    scriptJob:
      elasticJobType: SCRIPT
      cron: 0/10 * * * * ?
      shardingTotalCount: 3
      props:
        script.command.line: "echo SCRIPT Job: "

```

作业启动

定时调度
定时调度作业在 Spring Boot 应用程序启动完成后会自动启动，无需其他额外操作。

一次性调度
一次性调度的作业的执行权在开发者手中，开发者可以在需要调用作业的位置注入 OneOffJobBootstrap， 通过 execute() 方法执行作业。

OneOffJobBootstrap bean 的名称通过属性 jobBootstrapBeanName 配置，注入时需要指定依赖的 bean 名称。 具体配置请参考配置文档。
```
elasticjob:
  jobs:
    myOneOffJob:
      jobBootstrapBeanName: myOneOffJobBean
      ....

```

错误处理：
```
elasticjob:
  regCenter:
    ...
  jobs:
    ...
    jobErrorHandlerType: LOG 

```

### 作业监听器
#### 作业监听器配置项
分布式监听器配置项
可配置属性

|属性名	|类型	|缺省值	|描述|
|---|---|---|---|
|started-timeout-milliseconds	|long|	Long.MAX_VALUE	|最后一个作业执行前的执行方法的超时毫秒数|
|completed-timeout-milliseconds	|long|	Long.MAX_VALUE	|最后一个作业执行后的执行方法的超时毫秒数|

#### 常规监听器
若作业处理作业服务器的文件，处理完成后删除文件，可考虑使用每个节点均执行清理任务。 此类型任务实现简单，且无需考虑全局分布式任务是否完成，应尽量使用此类型监听器。

```

public class MyJobListener implements ElasticJobListener {
    
    @Override
    public void beforeJobExecuted(ShardingContexts shardingContexts) {
        // do something ...
    }
    
    @Override
    public void afterJobExecuted(ShardingContexts shardingContexts) {
        // do something ...
    }
    
    @Override
    public String getType() {
        return "simpleJobListener";
    }
}
```

#### 分布式监听器
若作业处理数据库数据，处理完成后只需一个节点完成数据清理任务即可。 此类型任务处理复杂，需同步分布式环境下作业的状态同步，提供了超时设置来避免作业不同步导致的死锁，应谨慎使用。

```
public class MyDistributeOnceJobListener extends AbstractDistributeOnceElasticJobListener {
    
    private static final long startTimeoutMills = 3000;
    private static final long completeTimeoutMills = 3000;

    public MyDistributeOnceJobListener() {
        super(startTimeoutMills, completeTimeoutMills);
    }
    
    
    @Override
    public void doBeforeJobExecutedAtLastStarted(ShardingContexts shardingContexts) {
        // do something ...
    }
    
    @Override
    public void doAfterJobExecutedAtLastCompleted(ShardingContexts shardingContexts) {
        // do something ...
    }
    
    @Override
    public String getType() {
        return "distributeOnceJobListener";
    }
}
```
#### 使用监听器
```java
public class JobMain {
    
    public static void main(String[] args) {
        new ScheduleJobBootstrap(createRegistryCenter(), createJobConfiguration()).schedule();
    }
    
    private static CoordinatorRegistryCenter createRegistryCenter() {
        CoordinatorRegistryCenter regCenter = new ZookeeperRegistryCenter(new ZookeeperConfiguration("zk_host:2181", "elastic-job-demo"));
        regCenter.init();
        return regCenter;
    }
    
    private static JobConfiguration createJobConfiguration() {
        JobConfiguration jobConfiguration = JobConfiguration.newBuilder("test", 2)
                                .jobListenerTypes("simpleListener", "distributeListener").build();
    }
}
```


### 事件追踪
ElasticJob 提供了事件追踪功能，可通过事件订阅的方式处理调度过程的重要事件，用于查询、统计和监控。 目前提供了基于关系型数据库的事件订阅方式记录事件，开发者也可以通过 SPI 自行扩展。

#### 事件追踪配置项
可配置属性
|属性名	|类型	|缺省值	|描述|
|---|---|---|---|
|type	|String	||	事件追踪存储适配器类型|
|storage	|泛型	||	事件追踪存储适配器对象|

ElasticJob-Lite 在配置中提供了 TracingConfiguration，目前支持数据库方式配置。 开发者也可以通过 SPI 自行扩展。
```
    // 初始化数据源
    DataSource dataSource = ...;
    // 定义日志数据库事件溯源配置
    TracingConfiguration tracingConfig = new TracingConfiguration<>("RDB", dataSource);
    // 初始化注册中心
    CoordinatorRegistryCenter regCenter = ...;
    // 初始化作业配置
    JobConfiguration jobConfig = ...;
    jobConfig.getExtraConfigurations().add(tracingConfig);
    new ScheduleJobBootstrap(regCenter, jobConfig).schedule();
```

#### 使用Spring Boot Starter
ElasticJob-Lite 的 Spring Boot Starter 集成了 TracingConfiguration 自动配置， 开发者只需注册一个 DataSource 到 Spring 容器中并在配置文件指定事件追踪数据源类型， Starter 就会自动创建一个 TracingConfiguration 实例并注册到 Spring 容器中。

引入 spring-boot-starter-jdbc 注册数据源或自行创建一个 DataSource Bean。
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <version>${springboot.version}</version>
</dependency>
```
配置
```
spring:
  datasource:
    url: jdbc:h2:mem:job_event_storage
    driver-class-name: org.h2.Driver
    username: sa
    password:

elasticjob:
  tracing:
    type: RDB

```
作业启动

指定事件追踪数据源类型为 RDB，TracingConfiguration 会自动注册到容器中，如果与 elasticjob-lite-spring-boot-starter 配合使用， 开发者无需进行其他额外的操作，作业启动器会自动使用创建的 TracingConfiguration。


#### 事件追踪表
事件追踪的 event_trace_rdb_url 属性对应库自动创建 JOB_EXECUTION_LOG 和 JOB_STATUS_TRACE_LOG 两张表以及若干索引。

**JOB_EXECUTION_LOG 字段含义**

|字段名称	|字段类型	|是否必填	|描述|
|----|---|---|---|
|id	|VARCHAR(40)	|是|	主键|
|job_name|	VARCHAR(100)|	是|	作业名称|
|task_id|	VARCHAR(1000)|	是	|任务名称,每次作业运行生成新任务|
|hostname|	VARCHAR(255)|	是	|主机名称|
|ip	|VARCHAR(50)	|是	|主机IP|
|sharding_item	|INT	|是	|分片项|
|execution_source|	VARCHAR(20)|	是	|作业执行来源。可选值为NORMAL_TRIGGER, MISFIRE, FAILOVER|
|failure_cause|	VARCHAR(2000)	|否	|执行失败原因|
|is_success	|BIT|	是|	是否执行成功|
|start_time	|TIMESTAMP	|是	|作业开始执行时间|
|complete_time	|TIMESTAMP	|否	|作业结束执行时间|

JOB_EXECUTION_LOG 记录每次作业的执行历史。 分为两个步骤：
  - 作业开始执行时向数据库插入数据，除 failure_cause 和 complete_time 外的其他字段均不为空。
  - 作业完成执行时向数据库更新数据，更新 is_success, complete_time 和 failure_cause(如果作业执行失败)。

**JOB_STATUS_TRACE_LOG 字段含义**

|字段名称	|字段类型	|是否必填	|描述|
|----|---|---|---|
|id|	VARCHAR(40)	是	主键
|job_name|	VARCHAR(100)	|是	作业名称|
|original_task_id|	VARCHAR(1000)	|是	原任务名称|
|task_id|	VARCHAR(1000)	|是	任务名称|
|slave_id	|VARCHAR(1000)	|是	执行作业服务器的名称，Lite版本为服务器的IP地址，Cloud版本为Mesos执行机主键|
|source|	VARCHAR(50)	|是	任务执行源，可选值为CLOUD_SCHEDULER, CLOUD_EXECUTOR, LITE_EXECUTOR|
|execution_type|	VARCHAR(20)	|是	任务执行类型，可选值为NORMAL_TRIGGER, MISFIRE, FAILOVER|
|sharding_item|	VARCHAR(255)	|是	分片项集合，多个分片项以逗号分隔|
|state|	VARCHAR(20)|	是|	任务执行状态，可选值为TASK_STAGING, TASK_RUNNING, TASK_FINISHED, TASK_KILLED, TASK_LOST, TASK_FAILED, TASK_ERROR|
|message|	VARCHAR(2000)|	是	|相关信息|
|creation_time|	TIMESTAMP|	是|	记录创建时间|

JOB_STATUS_TRACE_LOG 记录作业状态变更痕迹表。 可通过每次作业运行的 task_id 查询作业状态变化的生命周期和运行轨迹。



### 应用部署
1. 启动 ElasticJob-Lite 指定注册中心的 ZooKeeper。
2. 运行包含 ElasticJob-Lite 和业务代码的 jar 文件。不限于 jar 或 war 的启动方式。
3. 当作业服务器配置多网卡时，可通过设置系统变量 elasticjob.preferred.network.interface 指定网卡地址或 elasticjob.preferred.network.ip 指定IP。 ElasticJob 默认获取网卡列表中第一个非回环可用 IPV4 地址。
