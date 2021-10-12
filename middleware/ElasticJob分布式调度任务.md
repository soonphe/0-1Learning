# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

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

#### 网页控制台和zeekeeper集群
- 网页控制台
官网地址：https://github.com/apache/shardingsphere-elasticjob-ui
操作步骤
```bash
git clone https://github.com/apache/shardingsphere-elasticjob-ui.git
cd shardingsphere-elasticjob-ui/
mvn clean package -Prelease
```
- 获取lite压缩包 `shardingsphere-elasticjob-ui/shardingsphere-elasticjob-ui-distribution/shardingsphere-elasticjob-lite-ui-bin-distribution/target/apache-shardingsphere-${latest.release.version}-shardingsphere-elasticjob-lite-ui-bin.tar.gz`

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

#### zk集群
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

### 作业开发
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

### 作业配置
基于cron表达式
```
    JobConfiguration jobConfig = JobConfiguration.newBuilder("MyJob", 3).cron("0/5 * * * * ?").build();
```

### 作业调度
```
public class MyJobDemo {
    
    public static void main(String[] args) {
        new ScheduleJobBootstrap(createRegistryCenter(), new MyJob(), createJobConfiguration()).schedule();
    }
    
    private static CoordinatorRegistryCenter createRegistryCenter() {
        CoordinatorRegistryCenter regCenter = new ZookeeperRegistryCenter(new ZookeeperConfiguration("zk_host:2181", "my-job"));
        regCenter.init();
        return regCenter;
    }
    
    private static JobConfiguration createJobConfiguration() {
        // 创建作业配置
        // ...
    }
}
```


### 应用部署
1. 启动 ElasticJob-Lite 指定注册中心的 ZooKeeper。
2. 运行包含 ElasticJob-Lite 和业务代码的 jar 文件。不限于 jar 或 war 的启动方式。
3. 当作业服务器配置多网卡时，可通过设置系统变量 elasticjob.preferred.network.interface 指定网卡地址或 elasticjob.preferred.network.ip 指定IP。 ElasticJob 默认获取网卡列表中第一个非回环可用 IPV4 地址。


### 运维平台和 RESTFul API 部署(可选)
1. 解压缩 elasticjob-lite-console-${version}.tar.gz 并执行 bin\start.sh。
2. 打开浏览器访问 http://localhost:8899/ 即可访问控制台。8899 为默认端口号，可通过启动脚本输入 -p 自定义端口号。
3. 访问 RESTFul API 方法同控制台。
4. elasticjob-lite-console-${version}.tar.gz 可通过 mvn install 编译获取。

登录
控制台提供两种账户：管理员及访客。 
管理员拥有全部操作权限，访客仅拥有察看权限。 
默认管理员用户名和密码是 root/root，访客用户名和密码是 guest/guest，可通过 conf\application.properties 修改管理员及访客用户名及密码。

