# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Hbase
HBase是一个分布式的、面向列的开源数据库
官网：https://hbase.apache.org/

### 安装部署
基于brew安装
```
brew install hbase

启动命令
hbase-master
hbase-regionserver
其中，`hbase-master`用于启动主节点，`hbase-regionserver`用于启动 RegionServer。

启动成功后，我们可以通过以下命令来查看HBase的运行状态：
hbase-master status
hbase-regionserver status
```

二进制安装地址：
https://dlcdn.apache.org/hbase/

#### 配置修改

直接修改配置 hbase-env.sh
```
export JAVA_HOME=/usr/local/develop/java/zulu-jdk17.0.7
#1.8后 新增配置
export HBASE_OPTS="${HBASE_SHELL_OPTS} --add-opens java.base/java.lang=ALL-UNNAMED --add-opens java.base/java.lang=ALL-UNNAMED --add-opens java.base/sun.nio.ch=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED"
```

配置hbase-site.xml
这里我们直接使用 本地文件路径 而不是hdfs 分布式文件系统
```
<!-- 强制运行在本地的文件系统 而不是hdfs 这个必须要开 不然会报错 -->
<property>
   <name>hbase.unsafe.stream.capability.enforce</name>
   <value>false</value>
</property>
  
<!-- 单体部署 -->
<property>
    <name>hbase.cluster.distributed</name>
    <value>false</value>
</property>
  
<property>
   <name>hbase.rootdir</name>
   <!-- 修改为自己的hbase的路径-->
   <value>file:///usr/local/develop/hbase</value>
 </property>
```

启动
运行bin目录下的 start-hbase.sh 可直接启动

验证
- 第一个办法，看看日志
- 第二个 使用 hbase自带的控制页面来观察 http://localhost:16010/master-status
- 第三个 使用hbase shell 还是在bin目录下 直接运行 hbase shell 
- 第四个 使用jps 命令查看是否存在 HMaster

关闭 hbase stop-hbase.sh

### 使用独立的zookeeper
hbase自带的zk 会伴随着主进程一起启动 但是启动中 可能存在其他的问题 我们单独部署zk 并启动

启动zookeeper bin/zkServer.sh start >../out.log 2>&1 &

修改 hbase的 /conf/hbase-env.sh 配置文件
```
# Tell HBase whether it should manage its own instance of ZooKeeper or not.
# 使用 自己的zk 而不是 hbase自带的zk
export HBASE_MANAGES_ZK=false
```

修改hbase的 /conf/
```
 <property>
     <name>hbase.zookeeper.quorum</name>
     <!-- 注意单点hbase 并且分布式开关闭（hbase.cluster.distributed=false）这种情况只能配置单个zk 不支持集群 -->
     <value>localhost:2181</value>
 </property>

 <property>
     <name>hbase.zookeeper.property.dataDir</name>
     <!-- 修改为自己的 hbase 保存 zk的数据目录 -->
     <value>/usr/local/develop/hbase-3.0.0-alpha-4/zookeeper</value>
 </property>
```

**如果报错：找不到或无法加载主类 Cannot**
到配置文件底部，将# export HBASE_DISABLE_HADOOP_CLASSPATH_LOOKUP="true"前的注释（#号）删除即可，删除后保存并退出即可

在查看HBase版本时遇到错误"找不到或无法加载主类 org.apache.hadoop.hbase.util.GetJavaProperty"通常是由于HBase无法正确加载所需的Java属性导致的。这可能是由于HBase无法正确设置或获取Java属性所致。

通过修改hbase-env.sh配置文件中的HBASE_DISABLE_HADOOP_CLASSPATH_LOOKUP属性，实际上禁用了HBase对Hadoop类路径的查找。这个属性的作用是告诉HBase不要依赖于Hadoop来设置类路径，而是使用HBase自己的类路径设置。


### 基础使用
4.1 Create a table. 创建表
Use the create command to create a new table. You must specify the table name and the ColumnFamily name.
```
hbase(main):001:0> create 'test', 'cf'
0 row(s) in 0.4170 seconds
=> Hbase::Table - test
```

4.2 List Information About your Table 查看表
Use the list command to confirm your table exists
```
hbase(main):002:0> list 'test'
TABLE
test
1 row(s) in 0.0180 seconds

=> ["test"]
```
4.3 查看表的详情
Now use the describe command to see details, including configuration defaults
```
hbase(main):003:0> describe 'test'
Table test is ENABLED
test
COLUMN FAMILIES DESCRIPTION
{NAME => 'cf', VERSIONS => '1', EVICT_BLOCKS_ON_CLOSE => 'false', NEW_VERSION_BEHAVIOR => 'false', KEEP_DELETED_CELLS => 'FALSE', CACHE_DATA_ON_WRITE =>
'false', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', MIN_VERSIONS => '0', REPLICATION_SCOPE => '0', BLOOMFILTER => 'ROW', CACHE_INDEX_ON_WRITE => 'f
alse', IN_MEMORY => 'false', CACHE_BLOOMS_ON_WRITE => 'false', PREFETCH_BLOCKS_ON_OPEN => 'false', COMPRESSION => 'NONE', BLOCKCACHE => 'true', BLOCKSIZE
=> '65536'}
1 row(s)
Took 0.9998 seconds
```

4.4 Put data into your table. 向表中新增数据
To put data into your table, use the put command.
```
hbase(main):003:0> put 'test', 'row1', 'cf:a', 'value1'
0 row(s) in 0.0850 seconds

hbase(main):004:0> put 'test', 'row2', 'cf:b', 'value2'
0 row(s) in 0.0110 seconds

hbase(main):005:0> put 'test', 'row3', 'cf:c', 'value3'
0 row(s) in 0.0100 seconds
```
4.5 Scan the table for all data at once. 查看表里的所有数据
One of the ways to get data from HBase is to scan. Use the scan command to scan the table for data. You can limit your scan, but for now, all data is fetched.

```
hbase(main):006:0> scan 'test'
ROW                                      COLUMN+CELL
row1                                    column=cf:a, timestamp=1421762485768, value=value1
row2                                    column=cf:b, timestamp=1421762491785, value=value2
row3                                    column=cf:c, timestamp=1421762496210, value=value3
3 row(s) in 0.0230 seconds
Get a single row of data.
```

4.6 To get a single row of data at a time, use the get command. 查看第一行数据
```
hbase(main):007:0> get 'test', 'row1'
COLUMN                                   CELL
cf:a                                    timestamp=1421762485768, value=value1
1 row(s) in 0.0350 seconds
Disable a table.
```

4.7 如果要删除表或更改其设置以及在其他某些情况下，则需要使用Disable命令首先禁用表。您可以使用enable命令重新启用它。
```
hbase(main):008:0> disable 'test'
0 row(s) in 1.1820 seconds

hbase(main):009:0> enable 'test'
0 row(s) in 0.1770 seconds
Disable the table again if you tested the enable command above:

hbase(main):010:0> disable 'test'
0 row(s) in 1.1820 seconds
Drop the table.
```
4.8 To drop (delete) a table, use the drop command. 删除表
```
hbase(main):011:0> drop 'test'
0 row(s) in 0.1370 seconds
```
4.9 退出 shell
exit

### 数据结构
HBase的基本结构：HBase 的表、列和单元格

基本单位是列(column)，一列或多个列成行(row)，一个行有唯一行健(rowkey)确定存储，每个列可能有多个版本，多个版本存储在单元格(cell)中，行序是按照字典顺序进行排序的，意思是从左到右一次对比每一个键。

排列顺序可能跟预期的不一样，可以添加补键来获取正确的顺序，比如 row-1 永远小于 row-2，无论后面是什么，将始终按照这个顺序排列。

按照行健排序可以获得像RDBMS的主键索引一样的特性，也就是说，
- 行健总是唯一，并且只出现一次，否则你就是在更新同一行。
- 一行由若干列组成，若干列又构成一个列族(column family)，这不仅有助于构建数据的语义边界或者局部边界，还有助于我们给它们设置某些特性（如压缩），或者指示它们存储在内存中。
- 一个列族的所有列存储在同一个底层的存储文件李，这个存储文件叫做HFile。

HBase的设计中，行和列并不像经典的电子表格模型那样排列，而是采用了标签描述(tag metaphor)，也就是说，信息保存在一个特定标签下面。

每一个列的值或单元格的值都具有时间戳，默认由系统指定，也可以由用户显式设置。（时间戳可以用来区分不同版本的值），一个单元格的不同版本的值按照降序排列在一起，访问的时候优先读取最新的值。这种优化的目的在与让新值比老值更容易被读取。

HBase是按照BigTable模型实现的，是一个稀疏的、分布式的、持久化的、多维的映射，由行健，列建和时间戳索引。将上述特点联系在一起，我们就有了如下的数据存取格式。

> (Table, RowKey, Family, Column, Timestamp) -> Value


#### 自动分区
Hbase中扩展和负载均衡的基本单元称为region，region本质上是以行健排序的连续存储的区间。如果region太大，系统就会把它们动态拆分，相反，就把多个region合并，以减少存储文件数量。

一张表初始的时候只有一个region，用户开始向表种插入数据，系统会检测这个region的大小，确保其不超过这个配置最大值。如果超过了限制就会从中间键（middle key, region中间的那个行键）处将这个region拆分为大致相等的两个子region。

每一个region只能由一台region服务器(region server)加载，每一台region服务器可以同时加载多个region。

region拆分和服务相当于其他系统提供的自动分区(autosharding)，当一个服务器出现故障后，该服务器上面的region可以快速恢复，并获得细粒度的负载均衡，因为当服务于某个region的服务器当前负载过大、发生错误或者被停止使用导致不可用时，系统会将该region移到其他服务器上面。

region拆分的操作也非常快——接近瞬间，因为拆分后的region读取的仍然是原存储文件，直到合并把存储文件异步地写成独立的文件。

### java代码
连接HBase
接下来，我们将通过Java代码连接HBase数据库进行操作。首先，需要引入HBase的Java客户端依赖：
```
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>2.4.7</version>
</dependency>
```
然后，可以编写Java代码连接HBase数据库：
```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;

public class HBaseConnection {

    public static void main(String[] args) {
        Configuration config = HBaseConfiguration.create();
        config.set("hbase.zookeeper.quorum", "localhost");
        config.set("hbase.zookeeper.property.clientPort", "2181");

        try {
            Connection connection = ConnectionFactory.createConnection(config);
            System.out.println("Successfully connected to HBase!");
            connection.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
在上面的代码中，我们创建了一个HBase连接的示例。首先，我们需要创建一个Configuration对象，并设置ZooKeeper的地址和端口。然后，通过ConnectionFactory创建一个Connection对象，连接到HBase数据库。最后，我们打印出成功连接的消息，并关闭连接。




