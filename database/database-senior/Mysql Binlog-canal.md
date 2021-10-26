# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Mysql Binlog-canal
canal是阿里巴巴 MySQL binlog 增量订阅&消费组件

基于日志增量订阅和消费的业务包括

* 数据库镜像
* 数据库实时备份
* 索引构建和实时维护(拆分异构索引、倒排索引等)
* 业务 cache 刷新
* 带业务逻辑的增量数据处理

当前的 canal 支持源端 MySQL 版本包括 5.1.x , 5.5.x , 5.6.x , 5.7.x , 8.0.x

### 工作原理
MySQL主备复制原理
* MySQL master 将数据变更写入二进制日志( binary log, 其中记录叫做二进制日志事件binary log events，可以通过 show binlog events 进行查看)
* MySQL slave 将 master 的 binary log events 拷贝到它的中继日志(relay log)
* MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

canal 工作原理
* canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送dump 协议
* MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
* canal 解析 binary log 对象(原始为 byte 流)

### QuickStart

### 准备
对于自建 MySQL , 需要先开启 Binlog 写入功能，配置 binlog-format 为 ROW 模式，my.cnf 中配置如下
```
[mysqld]
log-bin=mysql-bin # 开启 binlog
binlog-format=ROW # 选择 ROW 模式
server_id=1 # 配置 MySQL replaction 需要定义，不要和 canal 的 slaveId 重复
```
>注意：针对阿里云 RDS for MySQL , 默认打开了 binlog , 并且账号默认具有 binlog dump 权限 , 不需要任何权限或者 binlog 设置,可以直接跳过这一步
授权 canal 链接 MySQL 账号具有作为 MySQL slave 的权限, 如果已有账户可直接 grant
```
CREATE USER canal IDENTIFIED BY 'canal';  
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
-- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ;
FLUSH PRIVILEGES;
```


### 启动
下载 canal, 访问 release 页面 , 选择需要的包下载, 如以 1.0.17 版本为例
```
wget https://github.com/alibaba/canal/releases/download/canal-1.0.17/canal.deployer-1.0.17.tar.gz
```
解压缩
```
mkdir /tmp/canal
tar zxvf canal.deployer-$version.tar.gz  -C /tmp/canal
```
解压完成后，进入 /tmp/canal 目录，可以看到如下结构
```
drwxr-xr-x 2 jianghang jianghang  136 2013-02-05 21:51 bin
drwxr-xr-x 4 jianghang jianghang  160 2013-02-05 21:51 conf
drwxr-xr-x 2 jianghang jianghang 1.3K 2013-02-05 21:51 lib
drwxr-xr-x 2 jianghang jianghang   48 2013-02-05 21:29 logs
```
配置修改
```
vi conf/example/instance.properties
```
```
## mysql serverId
canal.instance.mysql.slaveId = 1234
#position info，需要改成自己的数据库信息
canal.instance.master.address = 127.0.0.1:3306 
canal.instance.master.journal.name = 
canal.instance.master.position = 
canal.instance.master.timestamp = 
#canal.instance.standby.address = 
#canal.instance.standby.journal.name =
#canal.instance.standby.position = 
#canal.instance.standby.timestamp = 
#username/password，需要改成自己的数据库信息
canal.instance.dbUsername = canal  
canal.instance.dbPassword = canal
canal.instance.defaultDatabaseName =
canal.instance.connectionCharset = UTF-8
#table regex
canal.instance.filter.regex = .\*\\\\..\*
```
canal.instance.connectionCharset 代表数据库的编码方式对应到 java 中的编码类型，比如 UTF-8，GBK , ISO-8859-1
如果系统是1个 cpu，需要将 canal.instance.parser.parallel 设置为 false
启动
```
sh bin/startup.sh
```
查看 server 日志
```
vi logs/canal/canal.log</pre>
2013-02-05 22:45:27.967 [main] INFO  com.alibaba.otter.canal.deployer.CanalLauncher - ## start the canal server.
2013-02-05 22:45:28.113 [main] INFO  com.alibaba.otter.canal.deployer.CanalController - ## start the canal server[10.1.29.120:11111]
2013-02-05 22:45:28.210 [main] INFO  com.alibaba.otter.canal.deployer.CanalLauncher - ## the canal server is running now ......
```
查看 instance 的日志
```
vi logs/example/example.log
2013-02-05 22:50:45.636 [main] INFO  c.a.o.c.i.spring.support.PropertyPlaceholderConfigurer - Loading properties file from class path resource [canal.properties]
2013-02-05 22:50:45.641 [main] INFO  c.a.o.c.i.spring.support.PropertyPlaceholderConfigurer - Loading properties file from class path resource [example/instance.properties]
2013-02-05 22:50:45.803 [main] INFO  c.a.otter.canal.instance.spring.CanalInstanceWithSpring - start CannalInstance for 1-example 
2013-02-05 22:50:45.810 [main] INFO  c.a.otter.canal.instance.spring.CanalInstanceWithSpring - start successful....
```
关闭
```
sh bin/stop.sh
```

### 其他方式
canal 的 docker 模式快速启动，参考：Docker QuickStart
canal 链接 Aliyun RDS for MySQL，参考：Aliyun RDS QuickStart
canal 消息投递给 kafka/RocketMQ，参考：Canal-Kafka-RocketMQ-QuickStart

---
---

### Canal Admin QuickStart 

### 准备
canal-admin的限定依赖：

MySQL，用于存储配置和节点等相关数据
canal版本，要求>=1.1.4 (需要依赖canal-server提供面向admin的动态运维管理接口)

### 部署
1. 下载 canal-admin, 访问 release 页面 , 选择需要的包下载, 如以 1.1.4 版本为例
```
wget https://github.com/alibaba/canal/releases/download/canal-1.1.4/canal.admin-1.1.4.tar.gz
```
2. 解压缩
```
mkdir /tmp/canal-admin
tar zxvf canal.admin-$version.tar.gz  -C /tmp/canal-admin
```
解压完成后，进入 /tmp/canal 目录，可以看到如下结构
```
drwxr-xr-x   6 agapple  staff   204B  8 31 15:37 bin
drwxr-xr-x   8 agapple  staff   272B  8 31 15:37 conf
drwxr-xr-x  90 agapple  staff   3.0K  8 31 15:37 lib
drwxr-xr-x   2 agapple  staff    68B  8 31 15:26 logs
```
3. 配置修改
```
vi conf/application.yml
```
```
server:
  port: 8089
spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8

spring.datasource:
  address: 127.0.0.1:3306
  database: canal_manager
  username: canal
  password: canal
  driver-class-name: com.mysql.jdbc.Driver
  url: jdbc:mysql://${spring.datasource.address}/${spring.datasource.database}?useUnicode=true&characterEncoding=UTF-8&useSSL=false
  hikari:
    maximum-pool-size: 30
    minimum-idle: 1

canal:
  adminUser: admin
  adminPasswd: admin
```
4. 初始化元数据库
```
mysql -h127.1 -uroot -p

# 导入初始化SQL
> source conf/canal_manager.sql
```
a. 初始化SQL脚本里会默认创建canal_manager的数据库，建议使用root等有超级权限的账号进行初始化 
b. canal_manager.sql默认会在conf目录下，也可以通过链接下载 [canal_manager.sql](https://raw.githubusercontent.com/alibaba/canal/master/canal-admin/canal-admin-server/src/main/resources/canal_manager.sql)

5. 启动
```
sh bin/startup.sh
```
查看 admin 日志
```
vi logs/admin.log
```
```
2019-08-31 15:43:38.162 [main] INFO  o.s.boot.web.embedded.tomcat.TomcatWebServer - Tomcat initialized with port(s): 8089 (http)
2019-08-31 15:43:38.180 [main] INFO  org.apache.coyote.http11.Http11NioProtocol - Initializing ProtocolHandler ["http-nio-8089"]
2019-08-31 15:43:38.191 [main] INFO  org.apache.catalina.core.StandardService - Starting service [Tomcat]
2019-08-31 15:43:38.194 [main] INFO  org.apache.catalina.core.StandardEngine - Starting Servlet Engine: Apache Tomcat/8.5.29
....
2019-08-31 15:43:39.789 [main] INFO  o.s.w.s.m.m.annotation.ExceptionHandlerExceptionResolver - Detected @ExceptionHandler methods in customExceptionHandler
2019-08-31 15:43:39.825 [main] INFO  o.s.b.a.web.servlet.WelcomePageHandlerMapping - Adding welcome page: class path resource [public/index.html]
```
此时代表canal-admin已经启动成功，可以通过 http://127.0.0.1:8089/ 访问，默认密码：admin/123456

6. 关闭
```
sh bin/stop.sh
canal-server端配置
```
使用canal_local.properties的配置覆盖canal.properties
```
# register ip
canal.register.ip =

# canal admin config
canal.admin.manager = 127.0.0.1:8089
canal.admin.port = 11110
canal.admin.user = admin
canal.admin.passwd = 4ACFE3202A5FF5CF467898FC58AAB1D615029441
# admin auto register
canal.admin.register.auto = true
canal.admin.register.cluster =
```
启动admin-server即可。

或在启动命令中使用参数：sh bin/startup.sh local 指定配置

just have fun!

---
---

### ClientSample

### 直接使用canal.example工程
1. 首先启动Canal Server，可参见QuickStart
2.
    1. 可以在eclipse里，直接打开com.alibaba.otter.canal.example.SimpleCanalClientTest，直接运行
    2. 在工程的example目录下运行命令行：
    ```
    mvn exec:java -Dexec.mainClass="com.alibaba.otter.canal.example.SimpleCanalClientTest"
    ```
    3. 下载example包: https://github.com/alibaba/canal/releases，解压缩后，直接运行sh startup.sh脚本
3. 触发数据变更 d. 在控制台或者logs中查看，可以看到如下信息 ：
```
================> binlog[mysql-bin.002579:508882822] , name[retl,xdual] , eventType : UPDATE , executeTime : 1368607728000 , delay : 4270ms
-------> before
ID : 1    update=false
X : 2013-05-15 11:43:42    update=false
-------> after
ID : 1    update=false
X : 2013-05-15 16:48:48    update=true
```

### 从头创建工程
1. 依赖配置：
```
<dependency>
    <groupId>com.alibaba.otter</groupId>
    <artifactId>canal.client</artifactId>
    <version>1.1.0</version>
</dependency>
```

2. ClientSample代码
```
package com.alibaba.otter.canal.sample;
import java.net.InetSocketAddress;
import java.util.List;


import com.alibaba.otter.canal.client.CanalConnectors;
import com.alibaba.otter.canal.client.CanalConnector;
import com.alibaba.otter.canal.common.utils.AddressUtils;
import com.alibaba.otter.canal.protocol.Message;
import com.alibaba.otter.canal.protocol.CanalEntry.Column;
import com.alibaba.otter.canal.protocol.CanalEntry.Entry;
import com.alibaba.otter.canal.protocol.CanalEntry.EntryType;
import com.alibaba.otter.canal.protocol.CanalEntry.EventType;
import com.alibaba.otter.canal.protocol.CanalEntry.RowChange;
import com.alibaba.otter.canal.protocol.CanalEntry.RowData;


public class SimpleCanalClientExample {


public static void main(String args[]) {
    // 创建链接
    CanalConnector connector = CanalConnectors.newSingleConnector(new InetSocketAddress(AddressUtils.getHostIp(),
                                                                                        11111), "example", "", "");
    int batchSize = 1000;
    int emptyCount = 0;
    try {
        connector.connect();
        connector.subscribe(".*\\..*");
        connector.rollback();
        int totalEmptyCount = 120;
        while (emptyCount < totalEmptyCount) {
            Message message = connector.getWithoutAck(batchSize); // 获取指定数量的数据
            long batchId = message.getId();
            int size = message.getEntries().size();
            if (batchId == -1 || size == 0) {
                emptyCount++;
                System.out.println("empty count : " + emptyCount);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                }
            } else {
                emptyCount = 0;
                // System.out.printf("message[batchId=%s,size=%s] \n", batchId, size);
                printEntry(message.getEntries());
            }

            connector.ack(batchId); // 提交确认
            // connector.rollback(batchId); // 处理失败, 回滚数据
        }

        System.out.println("empty too many times, exit");
    } finally {
        connector.disconnect();
    }
}

private static void printEntry(List<Entry> entrys) {
    for (Entry entry : entrys) {
        if (entry.getEntryType() == EntryType.TRANSACTIONBEGIN || entry.getEntryType() == EntryType.TRANSACTIONEND) {
            continue;
        }

        RowChange rowChage = null;
        try {
            rowChage = RowChange.parseFrom(entry.getStoreValue());
        } catch (Exception e) {
            throw new RuntimeException("ERROR ## parser of eromanga-event has an error , data:" + entry.toString(),
                                       e);
        }

        EventType eventType = rowChage.getEventType();
        System.out.println(String.format("================&gt; binlog[%s:%s] , name[%s,%s] , eventType : %s",
                                         entry.getHeader().getLogfileName(), entry.getHeader().getLogfileOffset(),
                                         entry.getHeader().getSchemaName(), entry.getHeader().getTableName(),
                                         eventType));

        for (RowData rowData : rowChage.getRowDatasList()) {
            if (eventType == EventType.DELETE) {
                printColumn(rowData.getBeforeColumnsList());
            } else if (eventType == EventType.INSERT) {
                printColumn(rowData.getAfterColumnsList());
            } else {
                System.out.println("-------&gt; before");
                printColumn(rowData.getBeforeColumnsList());
                System.out.println("-------&gt; after");
                printColumn(rowData.getAfterColumnsList());
            }
        }
    }
}

private static void printColumn(List<Column> columns) {
    for (Column column : columns) {
        System.out.println(column.getName() + " : " + column.getValue() + "    update=" + column.getUpdated());
    }
}

}
```

4. 运行Client

首先启动Canal Server，可参见QuickStart

启动Canal Client后，可以从控制台从看到类似消息：
```
empty count : 1
empty count : 2
empty count : 3
empty count : 4
```

此时代表当前数据库无变更数据

5. 触发数据库变更
```
mysql> use test;
Database changed
mysql> CREATE TABLE `xdual` (
    ->   `ID` int(11) NOT NULL AUTO_INCREMENT,
    ->   `X` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    ->   PRIMARY KEY (`ID`)
    -> ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 ;
Query OK, 0 rows affected (0.06 sec)
mysql> insert into xdual(id,x) values(null,now());Query OK, 1 row affected (0.06 sec)
```
可以从控制台中看到：
```
empty count : 1
empty count : 2
empty count : 3
empty count : 4
================> binlog[mysql-bin.001946:313661577] , name[test,xdual] , eventType : INSERT
ID : 4    update=true
X : 2013-02-05 23:29:46    update=true
```



