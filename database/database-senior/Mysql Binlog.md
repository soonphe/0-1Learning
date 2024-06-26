# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Mysql Binlog

MySQL 的二进制日志 binlog 可以说是 MySQL 最重要的日志，它记录了所有的 DDL 和 DML 语句（除了数据查询语句select、show等），以事件形式记录，还包含语句所执行的消耗的时间。
MySQL的二进制日志是事务安全型的。
binlog 的主要目的是复制和恢复。

### MySQL日志介绍
MySQL中一般有以下几种日志：

|日志类型|	写入日志的信息|
|---|---|
|错误日志	    |记录在启动，运行或停止mysqld时遇到的问题|
|通用查询日志	|记录建立的客户端连接和执行的语句|
|二进制日志	|记录更改数据的语句|
|中继日志	    |从复制主服务器接收的数据更改|
|慢查询日志	|记录所有执行时间超过 long_query_time 秒的所有查询或不使用索引的查询|
|DDL日志（元数据日志）	|元数据操作由DDL语句执行|

### Binlog日志的两个最重要的使用场景
* MySQL主从复制：MySQL Replication在Master端开启binlog，Master把它的二进制日志传递给slaves来达到master-slave数据一致的目的
* 数据恢复：通过使用 mysqlbinlog工具来使恢复数据

### 启用 Binlog
一般来说开启binlog日志大概会有1%的性能损耗。

启用binlog，通过配置 /etc/my.cnf 或 /etc/mysql/mysql.conf.d/mysqld.cnf 配置文件的 log-bin 选项：

在配置文件中加入 log-bin 配置，表示启用binlog，如果没有给定值，写成 log-bin=，则默认名称为主机名。（注：名称若带有小数点，则只取第一个小数点前的部分作为名称）
```
[mysqld]
log-bin=my-binlog-name
```
也可以通过 SET SQL_LOG_BIN=1 命令来启用 binlog，通过 SET SQL_LOG_BIN=0 命令停用 binlog。启用 binlog 之后须重启MySQL才能生效。

### 常用的Binlog操作命令
```
# 是否启用binlog日志
show variables like 'log_bin';

# 查看详细的日志配置信息
show global variables like '%log%';

# mysql数据存储目录
show variables like '%dir%';

# 查看binlog的目录
show global variables like "%log_bin%";

# 查看当前服务器使用的biglog文件及大小
show binary logs;

# 查看主服务器使用的biglog文件及大小

# 查看最新一个binlog日志文件名称和Position
show master status;


# 事件查询命令
# IN 'log_name' ：指定要查询的binlog文件名(不指定就是第一个binlog文件)
# FROM pos ：指定从哪个pos起始点开始查起(不指定就是从整个文件首个pos点开始算)
# LIMIT [offset,] ：偏移量(不指定就是0)
# row_count ：查询总条数(不指定就是所有行)
show binlog events [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count];

# 查看 binlog 内容
show binlog events;

# 查看具体一个binlog文件的内容 （in 后面为binlog的文件名）
show binlog events in 'master.000003';

# 设置binlog文件保存事件，过期删除，单位天
set global expire_log_days=3; 

# 删除当前的binlog文件
reset master; 

# 删除slave的中继日志
reset slave;

# 删除指定日期前的日志索引中binlog日志文件
purge master logs before '2019-03-09 14:00:00';

# 删除指定日志文件
purge master logs to 'master.000003';
```

### 写 Binlog 的时机

对支持事务的引擎如InnoDB而言，必须要提交了事务才会记录binlog。binlog 什么时候刷新到磁盘跟参数 sync_binlog 相关。

* 如果设置为0，则表示MySQL不控制binlog的刷新，由文件系统去控制它缓存的刷新；
* 如果设置为不为0的值，则表示每 sync_binlog 次事务，MySQL调用文件系统的刷新操作刷新binlog到磁盘中。
* 设为1是最安全的，在系统故障时最多丢失一个事务的更新，但是会对性能有所影响。

如果 sync_binlog=0 或 sync_binlog大于1，当发生电源故障或操作系统崩溃时，可能有一部分已提交但其binlog未被同步到磁盘的事务会被丢失，恢复程序将无法恢复这部分事务。

在MySQL 5.7.7之前，默认值 sync_binlog 是0，MySQL 5.7.7和更高版本使用默认值1，这是最安全的选择。一般情况下会设置为100或者0，牺牲一定的一致性来获取更好的性能。

### Binlog 文件以及扩展
binlog日志包括两类文件:
* 二进制日志索引文件（文件名后缀为.index）用于记录所有有效的的二进制文件
* 二进制日志文件（文件名后缀为.00000*）记录数据库所有的DDL和DML语句事件

binlog是一个二进制文件集合，每个binlog文件以一个4字节的魔数开头，接着是一组Events:
* 魔数：0xfe62696e对应的是0xfebin；
* Event：每个Event包含header和data两个部分；header提供了Event的创建时间，哪个服务器等信息，data部分提供的是针对该Event的具体信息，如具体数据的修改；
* 第一个Event用于描述binlog文件的格式版本，这个格式就是event写入binlog文件的格式；
* 其余的Event按照第一个Event的格式版本写入；
* 最后一个Event用于说明下一个binlog文件；
* binlog的索引文件是一个文本文件，其中内容为当前的binlog文件列表

当遇到以下3种情况时，MySQL会重新生成一个新的日志文件，文件序号递增：
* MySQL服务器停止或重启时
* 使用 flush logs 命令；
* 当 binlog 文件大小超过 max_binlog_size 变量的值时；

>max_binlog_size 的最小值是4096字节，最大值和默认值是 1GB (1073741824字节)。事务被写入到binlog的一个块中，所以它不会在几个二进制日志之间被拆分。因此，如果你有很大的事务，为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的日志都记录到当前日志文件中，直到事务结束，你可能会看到binlog文件大于 max_binlog_size 的情况。

### Binlog 的日志格式
记录在二进制日志中的事件的格式取决于二进制记录格式。支持三种格式类型：

* STATEMENT：基于SQL语句的复制（statement-based replication, SBR）
* ROW：基于行的复制（row-based replication, RBR）
* MIXED：混合模式复制（mixed-based replication, MBR）

在 MySQL 5.7.7 之前，默认的格式是 STATEMENT，在 MySQL 5.7.7 及更高版本中，默认值是 ROW。
日志格式通过 binlog-format 指定，如 binlog-format=STATEMENT、binlog-format=ROW、binlog-format=MIXED。

```
Statement
每一条会修改数据的sql都会记录在binlog中
优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO, 提高了性能。
缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的一些相关信息，以保证所有语句能在slave得到和在master端执行的时候相同的结果。另外mysql的复制，像一些特定函数的功能，slave与master要保持一致会有很多相关问题。

Row
5.1.5版本的MySQL才开始支持 row level 的复制,它不记录sql语句上下文相关信息，仅保存哪条记录被修改。
优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以row的日志内容会非常清楚的记录下每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题.
缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容。

注：将二进制日志格式设置为ROW时，有些更改仍然使用基于语句的格式，包括所有DDL语句，例如CREATE TABLE， ALTER TABLE，或 DROP TABLE。

Mixed
从5.1.8版本开始，MySQL提供了Mixed格式，实际上就是Statement与Row的结合。
在Mixed模式下，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则采用row格式保存binlog，MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择一种。
```

### mysqlbinlog 命令的使用
服务器以二进制格式将binlog日志写入binlog文件，如何要以文本格式显示其内容，可以使用 mysqlbinlog 命令。
```
# mysqlbinlog 的执行格式
mysqlbinlog [options] log_file ...

# 查看bin-log二进制文件（shell方式）
mysqlbinlog -v --base64-output=decode-rows /var/lib/mysql/master.000003

# 查看bin-log二进制文件（带查询条件）
mysqlbinlog -v --base64-output=decode-rows /var/lib/mysql/master.000003 \
    --start-datetime="2019-03-01 00:00:00"  \
    --stop-datetime="2019-03-10 00:00:00"   \
    --start-position="5000"    \
    --stop-position="20000"
```

截取一段进行分析：
```
# at 21019
#190308 10:10:09 server id 1  end_log_pos 21094 CRC32 0x7a405abc 	Query	thread_id=113	exec_time=0	error_code=0
SET TIMESTAMP=1552011009/*!*/;
BEGIN
/*!*/;
```
上面输出包括信息：
* position: 位于文件中的位置，即第一行的（# at 21019）,说明该事件记录从文件第21019个字节开始
* timestamp: 事件发生的时间戳，即第二行的（#190308 10:10:09）
* server id: 服务器标识（1）
* end_log_pos 表示下一个事件开始的位置（即当前事件的结束位置+1）
* thread_id: 执行该事件的线程id （thread_id=113）
* exec_time: 事件执行的花费时间
* error_code: 错误码，0意味着没有发生错误
* type:事件类型Query


### Binlog 事件类型
binlog 事件的结构主要有3个版本：

v1: 在 MySQL 3.23 中使用
v3: 在 MySQL 4.0.2 到 4.1 中使用
v4: 在 MySQL 5.0 及以上版本中使用
现在一般不会使用MySQL5.0以下版本，所以下面仅介绍v4版本的binlog事件类型。binlog 的事件类型较多，本文在此做一些简单的汇总

|事件类型	|说明|
|---|---|

UNKNOWN_EVENT	此事件从不会被触发，也不会被写入binlog中；发生在当读取binlog时，不能被识别其他任何事件，那被视为UNKNOWN_EVENT
START_EVENT_V3	每个binlog文件开始的时候写入的事件，此事件被用在MySQL3.23 – 4.1，MYSQL5.0以后已经被 FORMAT_DESCRIPTION_EVENT 取代
QUERY_EVENT	执行更新语句时会生成此事件，包括：create，insert，update，delete；
STOP_EVENT	当mysqld停止时生成此事件
ROTATE_EVENT	当mysqld切换到新的binlog文件生成此事件，切换到新的binlog文件可以通过执行flush logs命令或者binlog文件大于 max_binlog_size 参数配置的大小；
INTVAR_EVENT	当sql语句中使用了AUTO_INCREMENT的字段或者LAST_INSERT_ID()函数；此事件没有被用在binlog_format为ROW模式的情况下
LOAD_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL 3.23版本中使用
SLAVE_EVENT	未使用
CREATE_FILE_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0和4.1版本中使用
APPEND_BLOCK_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0版本中使用
EXEC_LOAD_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0和4.1版本中使用
DELETE_FILE_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0版本中使用
NEW_LOAD_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL4.0和4.1版本中使用
RAND_EVENT	执行包含RAND()函数的语句产生此事件，此事件没有被用在binlog_format为ROW模式的情况下
USER_VAR_EVENT	执行包含了用户变量的语句产生此事件，此事件没有被用在binlog_format为ROW模式的情况下
FORMAT_DESCRIPTION_EVENT	描述事件，被写在每个binlog文件的开始位置，用在MySQL5.0以后的版本中，代替了START_EVENT_V3
XID_EVENT	支持XA的存储引擎才有，本地测试的数据库存储引擎是innodb，所有上面出现了XID_EVENT；innodb事务提交产生了QUERY_EVENT的BEGIN声明，QUERY_EVENT以及COMMIT声明，如果是myIsam存储引擎也会有BEGIN和COMMIT声明，只是COMMIT类型不是XID_EVENT
BEGIN_LOAD_QUERY_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL5.0版本中使用
EXECUTE_LOAD_QUERY_EVENT	执行LOAD DATA INFILE 语句时产生此事件，在MySQL5.0版本中使用
TABLE_MAP_EVENT	用在binlog_format为ROW模式下，将表的定义映射到一个数字，在行操作事件之前记录（包括：WRITE_ROWS_EVENT，UPDATE_ROWS_EVENT，DELETE_ROWS_EVENT）
PRE_GA_WRITE_ROWS_EVENT	已过期，被 WRITE_ROWS_EVENT 代替
PRE_GA_UPDATE_ROWS_EVENT	已过期，被 UPDATE_ROWS_EVENT 代替
PRE_GA_DELETE_ROWS_EVENT	已过期，被 DELETE_ROWS_EVENT 代替
WRITE_ROWS_EVENT	用在binlog_format为ROW模式下，对应 insert 操作
UPDATE_ROWS_EVENT	用在binlog_format为ROW模式下，对应 update 操作
DELETE_ROWS_EVENT	用在binlog_format为ROW模式下，对应 delete 操作
INCIDENT_EVENT	主服务器发生了不正常的事件，通知从服务器并告知可能会导致数据处于不一致的状态
HEARTBEAT_LOG_EVENT	主服务器告诉从服务器，主服务器还活着，不写入到日志文件中


### Binlog 事件的结构
一个事件对象分为事件头和事件体，事件的结构如下：
```
+=====================================+
| event  | timestamp         0 : 4    |
| header +----------------------------+
|        | type_code         4 : 1    |
|        +----------------------------+
|        | server_id         5 : 4    |
|        +----------------------------+
|        | event_length      9 : 4    |
|        +----------------------------+
|        | next_position    13 : 4    |
|        +----------------------------+
|        | flags            17 : 2    |
|        +----------------------------+
|        | extra_headers    19 : x-19 |
+=====================================+
| event  | fixed part        x : y    |
| data   +----------------------------+
|        | variable part              |
+=====================================+
```
如果事件头的长度是 x 字节，那么事件体的长度为 (event_length - x) 字节；设事件体中 fixed part 的长度为 y 字节，那么 variable part 的长度为 (event_length - (x + y)) 字节



---

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






