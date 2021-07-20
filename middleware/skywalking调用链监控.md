# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## skywalking
官网下载地址：https://skywalking.apache.org/downloads/
https://mirrors.tuna.tsinghua.edu.cn/apache/skywalking/8.6.0/apache-skywalking-apm-es7-8.6.0.tar.gz
github地址：https://github.com/apache/skywalking

### 启动oap项目
启动oap项目收集项目运行的数据，并持久化到介质中。

进入：apache-skywalking-apm-bin/config/application.yml目录下。

默认使用h2内存缓存来保存数据（数据不能持久化，无序本地安装h2。使用默认配置就可以开启skywalking）

下面使用mysql作为存储介质：
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

在apache-skywalking-apm-bin/bin中执行sh oapService.sh命令。

出现下面命令表示启动成功：
```
SkyWalking OAP started successfully!
```

### 启动UI项目
进入apache-skywalking-apm-bin/webapp/webapp.yml配置文件中。修改项目端口号：
```
server:
  port: 9001
```
启动项目：apache-skywalking-apm-bin/bin中执行sh webappService.sh命令。
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
-javaagent:/Users/luoxiaosheng/Downloads/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar  -Dskywalking.agent.service_name=xxl-jobExcutor
-javaagent:/user/local/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar  -Dskywalking.agent.service_name=new-order
```
tomcat添加示例
```
JAVA_OPTS="-javaagent:/usr/local/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar  -Dskywalking.agent.service_name=new-order"
```




