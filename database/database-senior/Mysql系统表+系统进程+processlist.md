# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## MySQL数据库迁移+动态扩容缩容的分库分表

### mysql系统表
MySQL系统中自带mysql库和information_schema库
- mysql：系统用户、权限、存储过程等设置
- information_schema：提供了数据库元数据及访问方式

#### 1.mysql.*表
在mysql数据库中，有mysql_install_db脚本初始化权限表，存储权限的表有：
-- 用户列、权限列、安全列、资源控制列
mysql.user
-- 用户列、权限列
mysql.db
-- 存储主机对数据库的操作权限
mysql.host
-- 表权限
mysql.table_priv
-- 列权限
mysql.columns_priv
-- 存储权限
mysql.proc_priv
-- 存储过程
select name from mysql.proc where type= 'PROCEDURE';

#### 2.information_schema.*表
--查看当前数据库连接ip信息
select * from information_schema.processlist
--当前mysql实例中所有数据库的信息
select * from information_schema.schemata
--数据库中的表信息
select * from information_schema.tables
-- 数据库中视图信息
select * from information_schema.tables where  TABLE_TYPE='VIEW';
--当前数据库中的列信息
select * from information_schema.columns
--数据库中的索引信息
select * from information_schema.statistics
--用户权限
select * from information_schema.user_privileges
--方案权限
select * from information_schema.schema_privileges
--表权限
select * from information_schema.table_privileges
--列权限
select * from information_schema.column_privileges
--字符集
select * from information_schema.character_sets
--字符集 对照信息
select * from information_schema.collations
--存在约束的表信息
select * from information_schema.table_constraints
--存在约束的列信息
select * from information_schema.key_column_usage
--数据库中的视图
select * from information_schema.views
--触发器信息
select * from information_schema.triggers

#### 3.查看存储过程和函数
查询数据库中的存储过程和函数
select `name` from mysql.proc where db = 'xx' and `type` = 'PROCEDURE' ;  //存储过程
select * from mysql.proc where db = 'xx' and `type` = 'PROCEDURE' and name='xx';
select `name` from mysql.proc where db = 'xx' and `type` = 'FUNCTION'  ;  //函数
show procedure status; //存储过程
show function status;     //函数

#### 4.查看存储过程或函数的创建代码
show create procedure proc_name;
show create function func_name;

#### 5.查看视图
SELECT * from information_schema.VIEWS   //视图
SELECT * from information_schema.TABLES   //表

#### 6.查看触发器
SHOW TRIGGERS [FROM db_name] [LIKE expr];
SELECT * FROM  information_schema.triggers T WHERE trigger_name=”mytrigger”;


### show语句
show语句是`mysql.*`和`information_schema.*`表查询的简写方式。

-- 所有数据库名
show DATABASES;
-- 所有表名
show TABLES;
-- abc数据库中的所有表名
show tables from abc;
-- 表信息
desc 表名;
-- 表字段
show columns from 表名;
-- 表信息
describe 表名;
-- 表创建语句
show create table 表名;
-- 显示数据库 信息
show create database 数据库名;
-- 数据库状态
show table status from 数据库名;
-- 显示当前数据库中所有表的名称
show tables 或show tables from database_name;
-- 显示mysql中所有数据库的名称
show databases;
-- 显示系统中正在运行的所有进程，也就是当前正在执行的查询。
show processlist;
-- 显示当前使用或者指定的database中的每个表的信息。信息包括表类型和表的最新更新时间
show table status;
show table status from ods;
show table status where name like 'temp%';
-- 显示表中列名称
show columns from table_name from database_name;
-- 显示表中列名称
show columns from database_name.table_name;
-- 显示一个用户的权限，显示结果类似于grant 命令
show grants for user_name@localhost;
-- 显示表的索引
show index from table_name;
--解释：显示一些系统特定资源的信息，例如，正在运行的线程数量
show status;
-- 显示系统变量的名称和值
show variables;
--解释：显示服务器所支持的不同权限
show privileges;
-- 显示create database 语句是否能够创建指定的数据库
show create database database_name ;
-- 显示create database 语句是否能够创建指定的数据库
show create table table_name;
-- 显示create view
show create view view_name;
-- 显示安装以后可用的存储引擎和默认引擎。
show engies;
-- 显示存储引擎信息
show ENGINES;
-- 显示BDB存储引擎的日志
show logs;
--显示最后一个执行的语句所产生的错误、警告和通知
show warnings;
-- 只显示最后一个执行语句所产生的错误
show errors;
--显示被锁的表
show open tables where in_use >=1;


### 管理系统进程

#### 1.查看系统进程
show processlist;  

结果字段说明:
- Id：进程标识,当我们发现这个线程有问题的时候，可以通过 kill 命令，加上这个Id值将这个线程杀掉。
- User：指启动这个线程的用户
- Host：显示语句从哪个ip的端口上发出的
- db：数据库
- Command：显示当前连接的执行的命令，一般就是休眠（sleep），查询（query），连接（connect）。
- Time：表示该线程处于当前状态的时间
- State：线程的状态，和 Command 对应
- info：一般记录的是线程执行的语句。默认只显示前100个字符，也就是你看到的语句可能是截断了的，要看全部信息，需要使用 show full processlist

#### 2.操作合集
kill Id； 杀掉进程
show full processlist; 列出全部进程
show open tables;查看当前有那些表是打开的
show status like '%lock%';  查看服务器状态。
show engine innodb status; 查看innodb引擎的运行时信息
show variables like '%timeout%'; 查看服务器配置参数

#### 3.查看状态
show status;

结果值说明：
```
Aborted_clients 由于客户没有正确关闭连接已经死掉，已经放弃的连接数量。
Aborted_connects 尝试已经失败的MySQL服务器的连接的次数。
Connections 试图连接MySQL服务器的次数。
Created_tmp_tables 当执行语句时，已经被创造了的隐含临时表的数量。
Delayed_insert_threads 正在使用的延迟插入处理器线程的数量。
Delayed_writes 用INSERT DELAYED写入的行数。
Delayed_errors 用INSERT DELAYED写入的发生某些错误(可能重复键值)的行数。
Flush_commands 执行FLUSH命令的次数。
Handler_delete 请求从一张表中删除行的次数。
Handler_read_first 请求读入表中第一行的次数。
Handler_read_key 请求数字基于键读行。
Handler_read_next 请求读入基于一个键的一行的次数。
Handler_read_rnd 请求读入基于一个固定位置的一行的次数。
Handler_update 请求更新表中一行的次数。
Handler_write 请求向表中插入一行的次数。
Key_blocks_used 用于关键字缓存的块的数量。
Key_read_requests 请求从缓存读入一个键值的次数。
Key_reads 从磁盘物理读入一个键值的次数。
Key_write_requests 请求将一个关键字块写入缓存次数。
Key_writes 将一个键值块物理写入磁盘的次数。
Max_used_connections 同时使用的连接的最大数目。
Not_flushed_key_blocks 在键缓存中已经改变但是还没被清空到磁盘上的键块。
Not_flushed_delayed_rows 在INSERT DELAY队列中等待写入的行的数量。
Open_tables 打开表的数量。
Open_files 打开文件的数量。
Open_streams 打开流的数量(主要用于日志记载）
Opened_tables 已经打开的表的数量。
Questions 发往服务器的查询的数量。
Slow_queries 要花超过long_query_time时间的查询数量。
Threads_connected 当前打开的连接的数量。
Threads_running 不在睡眠的线程数量。
Uptime 服务器工作了多少秒。
```

### 拓展
#### 查询数据库,表大小
1.查询所有数据的大小(MB)：
select concat(round(sum(data_length/1024/1024),2),'MB') as data_size from information_schema.tables;

2.查看指定数据库(schema)的大小(MB)：
select concat(round(sum(data_length/1024/1024),2),'MB') as DB_size from information_schema.tables where table_schema='timber';

3.查看指定数据库的指定表(table)的大小
select table_name,table_schema,substring(create_time,1,10)	as create_date,sum(table_rows)	as table_rows
,concat(round(sum(data_length/1024/1024),2),'MB') as table_size
,concat(round(sum(data_length/1024/1024/1024),2),'GB') as table_size2
from information_schema.tables
where table_schema='timber'
group by table_name,SUBSTRING(CREATE_TIME,1,10)
order by  round(sum(data_length/1024/1024/1024),2) desc ;

#### processlist拓展
1、按客户端 IP 分组，看哪个客户端的链接数最多
```
select client_ip,count(client_ip) as client_num from (select substring_index(host,':' ,1) as client_ip from information_schema.processlist ) as connect_info group by client_ip order by client_num desc;
```
　　
2、查看正在执行的线程，并按 Time 倒排序，看看有没有执行时间特别长的线程
```
select * from information_schema.processlist where Command != 'Sleep' order by Time desc;
```

3、找出所有执行时间超过 5 分钟的线程，拼凑出 kill 语句，方便后面查杀 （此处 5分钟 可根据自己的需要调整SQL标红处）

可复制查询结果到控制台，直接执行，杀死堵塞进程
```
select concat('kill ', id, ';') from information_schema.processlist where Command != 'Sleep' and Time > 300 order by Time desc;
```

4、查询线程及相关信息
```
show full processlist
```
ID 为此线程ID，Time为线程运行时间，Info为此线程SQL

5、一堆不怎么看得解释

show processlist 是显示用户正在运行的线程，需要注意的是，除了 root 用户能看到所有正在运行的线程外，其他用户都只能看到自己正在运行的线程，看不到其它用户正在运行的线程。除非单独个这个用户赋予了PROCESS 权限（授权后需要退出重新登录）。

show processlist 显示的信息都是来自MySQL系统库 information_schema 中的 processlist 表。所以使用下面的查询语句可以获得相同的结果：

select * from information_schema.processlist

了解这些基本信息后，下面我们看看查询出来的结果都是什么意思。
```
Id: 就是这个线程的唯一标识，当我们发现这个线程有问题的时候，可以通过 kill 命令，加上这个Id值将这个线程杀掉。前面我们说了show processlist 显示的信息时来自information_schema.processlist 表，所以这个Id就是这个表的主键。

User: 就是指启动这个线程的用户。

Host: 记录了发送请求的客户端的 IP 和 端口号。通过这些信息在排查问题的时候，我们可以定位到是哪个客户端的哪个进程发送的请求。

DB: 当前执行的命令是在哪一个数据库上。如果没有指定数据库，则该值为 NULL 。

Command: 是指此刻该线程正在执行的命令。这个很复杂，下面单独解释

Time: 表示该线程处于当前状态的时间。

State: 线程的状态，和 Command 对应，下面单独解释。

Info: 一般记录的是线程执行的语句。默认只显示前100个字符，也就是你看到的语句可能是截断了的，要看全部信息，需要使用 show full processlist。
```

下面我们单独看一下 Command 的值：
```
Binlog Dump: 主节点正在将二进制日志 ，同步到从节点

Change User: 正在执行一个 change-user 的操作

Close Stmt: 正在关闭一个Prepared Statement 对象

Connect: 一个从节点连上了主节点

Connect Out: 一个从节点正在连主节点

Create DB: 正在执行一个create-database 的操作

Daemon: 服务器内部线程，而不是来自客户端的链接

Debug: 线程正在生成调试信息

Delayed Insert: 该线程是一个延迟插入的处理程序

Drop DB: 正在执行一个 drop-database 的操作

Execute: 正在执行一个 Prepared Statement

Fetch: 正在从Prepared Statement 中获取执行结果

Field List: 正在获取表的列信息

Init DB: 该线程正在选取一个默认的数据库

Kill : 正在执行 kill 语句，杀死指定线程

Long Data: 正在从Prepared Statement 中检索 long data

Ping: 正在处理 server-ping 的请求

Prepare: 该线程正在准备一个 Prepared Statement

ProcessList: 该线程正在生成服务器线程相关信息

Query: 该线程正在执行一个语句

Quit: 该线程正在退出

Refresh：该线程正在刷表，日志或缓存；或者在重置状态变量，或者在复制服务器信息

Register Slave： 正在注册从节点

Reset Stmt: 正在重置 prepared statement

Set Option: 正在设置或重置客户端的 statement-execution 选项

Shutdown: 正在关闭服务器

Sleep: 正在等待客户端向它发送执行语句

Statistics: 该线程正在生成 server-status 信息

Table Dump: 正在发送表的内容到从服务器

Time: Unused
```
