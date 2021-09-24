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


### elasticjob和xxl-job
共同点： E-Job和X-job都有广泛的用户基础和完整的技术文档，都能满足定时任务的基本功能需求。
不同点：
X-Job 侧重的业务实现的简单和管理的方便，学习成本简单，失败策略和路由策略丰富。推荐使用在“用户基数相对少，服务器数量在一定范围内”的情景下使用
E-Job 关注的是数据，增加了弹性扩容和数据分片的思路，以便于更大限度的利用分布式服务器的资源。但是学习成本相对高些，推荐在“数据量庞大，且部署服务器数量较多”时使用


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

