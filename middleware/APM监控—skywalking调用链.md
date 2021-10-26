# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## APM
APM （Application Performance Management）是对企业的应用系统进行实时监控，它是用于实现对应用程序性能管理和故障管理的系统化的解决方案。

### APM主要解决的问题：
- 集中式度量系统
- 分布式全链接追踪系统
- 集中式日志系统（elk）

### 分布式调用追踪（APM）一览
1. google的Drapper--未开源，最早的APM
2. 阿里-鹰眼--未开源
3. 大众点评——CAT--跨服务的跟踪功能与点评内部的RPC框架集成，这部分未开源且项目在2014.1已经停止维护。服务粒度的监控，通过代码埋点的方式来实现监控，比如： 拦截器，注解，过滤器等，对代码的侵入性较大，集成成本较高。
4. Hydra-京东: 与dubbo框架集成，对于服务级别的跟踪统计，现有业务可以无缝接入。对于细粒度的兴趣点，需要业务人员手动添加.开源项目已于2013年6月停止维护
5. PinPoint-naver，字节码探针技术，代码无侵入，体系完善不易修改，支持java,技术栈支持dubbo.其他语言社区支援中
6. zipkin--java方便集成于springcloud，社区支持的插件也包括dubbo,rabbit,mysql,httpclient等(https://github.com/openzipkin/brave/tree/master/instrumentation)，同时支持php,go,js等语言客户端，界面功能较为简单，本身无告警功能，可能需要二次开发。代码入侵度小。
7. uber-jaeger， Jaeger支持java/c++/go/node/php，在界面上较为完善（对比zipkin），但是也无告警功能。代码入侵度小。dubbo目前无插件支持，可二次开发。
8. skywalking -华为，类似于PinPoint，目前还在apache孵化中，网上吞吐量对比中强于pinpoint,实际未验证。本身支持dubbo

### 方案对比

| |pinpoint| zipkin| jaeger |skywalking|
|---|---|---|---|---|
|OpenTracing兼容| 否| 是| 是| 是|
|客户端支持语言 |java、php| java,c#,go,php等| java,c#,go,php等| Java, .NET Core, NodeJS and PHP|
|存储| hbase| ES，mysql,Cassandra,内存| ES，kafka,Cassandra,内存| ES，H2,mysql,TIDB,sharding sphere|
|传输协议支持| thrift| http,MQ| udp/http| gRPC|
|ui丰富程度| 高| 低| 中| 中|
|实现方式-代码侵入性| 字节码注入，无侵入| 拦截请求，侵入| 拦截请求，侵入| 字节码注入，无侵入|
|扩展性| 低 |高 |高| 中|
|trace查询| 不支持| 支持 |支持| 支持|
|告警支持| 支持| 不支持 |不支持| 支持|
|jvm监控| 支持| 不支持 |不支持| 支持|
|性能损失| 高| 中| 中| 低|


---


### skywalking
官网下载地址：https://skywalking.apache.org/downloads/
国内镜像地址：https://mirrors.tuna.tsinghua.edu.cn/apache/skywalking/8.6.0/apache-skywalking-apm-es7-8.6.0.tar.gz
github地址：https://github.com/apache/skywalking

### 启动oap项目
启动oap项目收集项目运行的数据，并持久化到介质中。
进入：apache-skywalking-apm-bin/config/application.yml目录下。

默认使用h2内存缓存来保存数据（数据不能持久化，无需本地安装h2。使用默认配置就可以开启skywalking）

在apache-skywalking-apm-bin/bin中执行./oapService.sh命令。

出现下面命令表示启动成功：
```
SkyWalking OAP started successfully!
```

### 使用mysql作为存储介质：
```
storage:
  selector: ${SW_STORAGE:mysql}
  mysql:
    properties:
      jdbcUrl: ${SW_JDBC_URL:"jdbc:mysql://localhost:3306/sky"}
      dataSource.user: ${SW_DATA_SOURCE_USER:root}
      dataSource.password: ${SW_DATA_SOURCE_PASSWORD:123456}
      dataSource.cachePrepStmts: ${SW_DATA_SOURCE_CACHE_PREP_STMTS:true}
      dataSource.prepStmtCacheSize: ${SW_DATA_SOURCE_PREP_STMT_CACHE_SQL_SIZE:250}
      dataSource.prepStmtCacheSqlLimit: ${SW_DATA_SOURCE_PREP_STMT_CACHE_SQL_LIMIT:2048}
      dataSource.useServerPrepStmts: ${SW_DATA_SOURCE_USE_SERVER_PREP_STMTS:true}
    metadataQueryMaxSize: ${SW_STORAGE_MYSQL_QUERY_MAX_SIZE:5000}
    maxSizeOfArrayColumn: ${SW_STORAGE_MAX_SIZE_OF_ARRAY_COLUMN:20}
    numOfSearchableValuesPerTag: ${SW_STORAGE_NUM_OF_SEARCHABLE_VALUES_PER_TAG:2}
```
注意：需要将mysql的驱动包放入到apache-skywalking-apm-bin/oap-libs目录下，否则会出现找不到驱动的异常。


### 使用elastic作为存储介质（推荐）：
1. 部署ElasticSearch
2. 修改elasticsearch.yml文件，并设置cluster.name设置成CollectorDBCluster。此名称需要和collector配置文件一致。
3. 修改ES配置network.host值，将network.host的值修改成0.0.0.0。
4. 启动Elasticsearch
5. 解压并启动Skywalking Collector。修改application.yml中的参数
```
storage:
  selector: ${SW_STORAGE:elasticsearch}
  elasticsearch:
    nameSpace: ${SW_NAMESPACE:CollectorDBCluster}
    clusterNodes: ${SW_STORAGE_ES_CLUSTER_NODES:localhost:9200}
    protocol: ${SW_STORAGE_ES_HTTP_PROTOCOL:"http"}
    user: ${SW_ES_USER:""}
    password: ${SW_ES_PASSWORD:""}
```
6. 运行bin/startup.sh命令即可启动Skywalking Collector


### 启动UI项目
进入apache-skywalking-apm-bin/webapp/webapp.yml配置文件中。修改项目端口号：
```
server:
  port: 9001
```
启动项目：apache-skywalking-apm-bin/bin中执行./webappService.sh命令。
```
SkyWalking Web Application started successfully!
```
访问：http://localhost:9001/即可

### 监控实际项目
在实际项目中，启动项只需要配置--javaagent:/Users/xxx/Documents/apache-skywalking-apm-bin/agent/skywalking-agent.jar便可以完成skywalking数据的上报。

项目会读取skywalking-apm-bin/agent/config目录下的配置信息。

比较常用的配置
```
# The service name in UI（在UI上改服务展示的名字）
agent.service_name=${SW_AGENT_NAME:Your_ApplicationName}

# Backend service addresses（即oap项目的地址）.
collector.backend_service=${SW_AGENT_COLLECTOR_BACKEND_SERVICES:127.0.0.1:11800}
```
【注意：实际项目中，每一台服务器中只需要一个agent目录即可】。

service_name的配置可以在启动项中进行动态的配置。
```
-javaagent:/Users/xxx/Documents/apache-skywalking-apm-bin/agent/skywalking-agent.jar  -Dskywalking.agent.service_name=xx
```
示例
```
-javaagent:/user/local/apache-skywn-es7/agent/skywalking-agent.jar  -Dskywalking.agent.service_name=new-order

监控dubbo服务端、消费端：
java -jar -javaagent:$AGENT_PATH/skywalking-agent.jar -Dskywalking.agent.application_code=dubbo-provider -Dskywalking.collector.servers=localhost:11800 dubbo-provider.jar
java -jar -javaagent:/home/admin/skywalking/agent/skywalking-agent.jar  -Dskywalking.collector.backend_service=192.168.161.168:11800 -Dskywalking.agent.service_name=ord-qfjs-syncsrc-dev

```
tomcat添加示例
```
JAVA_OPTS="-javaagent:/usr/local/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar  -Dskywalking.agent.service_name=new-order"
```

覆盖优先级
探针配置 > 系统配置 >系统环境变量 > 配置文件中的值

探针配置：-javaagent:/path/to/skywalking-agent.jar=[option1]=[value1],[option2]=[value2]
系统配置：-Dskywalking.agent.service_name=skywalking_mysql
系统环境变量：agent.service_name=${SW_AGENT_NAME:Your_ApplicationName} ——这里指的是SW_AGENT_NAME
配置文件中的值：Your_ApplicationName


### 获取追踪ID
Skywalking提供我们Trace工具包，用于在追踪链路时进行信息的打印或者获取对应的追踪ID。我们使用Spring Boot编写一个案例，也可以直接使用资源下的 skywalking_plugins.jar 进行测试。

依赖
```
        <!--skywalking trace工具包-->
        <dependency>
            <groupId>org.apache.skywalking</groupId>
            <artifactId>apm-toolkit-trace</artifactId>
            <version>8.6.0</version>
            <scope>provided</scope>
        </dependency>
```
- TraceContext.traceId()可以打印出当前追踪的ID，方便在RocketBot中进行搜索。
- @Trace 注解修饰自己想要加入的跨度信息,即某个方法可以自定义返回值等等。

- ActiveSpan提供了三个方法进行信息的打印：ActiveSpan.debug
    - error方法会将本次调用变为失败状态，同时可以打印对应的堆栈信息和错误提示。
    - info方法打印info级别的信息。
    - debug方法打印debug级别的信息。

过滤指定的端点：
在开发过程中，有一些端点（接口）并不需要去进行监控，比如Swagger相关的端点。这个时候我们就可以使用Skywalking提供的过滤插件来进行过滤。在skywalking_plugins中编写两个接口进行测试：
代码实现：
```
//此接口不可被追踪
    @GetMapping("/exclude")
    public String exclude(){
        return "exclude";
    }
```

### skywalking 自定义插件
>使用skywalking时经常会出现断层，则是因为skywalking并没有提供对应的插件导致无法在链路图中体现调用流程。

Span:可理解为一次方法调用，一个程序块的调用，或一次RPC/数据库访问。只要是一个具有完整时间周期的程序访问，都可以被认为是一个span。SkyWalking Span 对象中的重要属性

|属性	|名称	|备注|
|---|---|---|
|component	|组件|	插件的组件名称，如：Lettuce，详见:ComponentsDefine.Class。|
|tag	|标签|	k-v结构，关键标签，key详见：Tags.Class。|
|peer	|对端资源|	用于拓扑图，若DB组件，需记录集群信息。|
|operationName	|操作名称|	若span=0，operationName将会搜索的下拉列表。|
|layer	|显示|	在链路页显示，详见SpanLayer.Class。|

Trace:调用链，通过归属于其的Span来隐性的定义。一条Trace可被认为是一个由多个Span组成的有向无环图（DAG图），在SkyWalking链路模块你可以看到，Trace又由多个归属于其的trace segment组成。

Trace segment:Segment是SkyWalking中的一个概念，它应该包括单个OS进程中每个请求的所有范围，通常是基于语言的单线程。由多个归属于本线程操作的Span组成。


