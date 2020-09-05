# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## hadoop

1.hadoop安装
https://www.jianshu.com/p/99b4f48c09c0
http://www.linuxidc.com/Linux/2017-03/142050.htm
hdfs端口：50070——9870  api端口 9000
开启hdfs：sbin/start-dfs.sh
开启yarn：sbin/start-yarn.sh
yarn界面：http://localhost:8088/
hdfs界面：http://192.168.31.242:9870
相关命令：HDFS shell
hdfs dfs -copyFromLocal /local/data /hdfs/data：将本地文件上传到 hdfs 上（原路径只能是一个文件）
hdfs dfs -put /tmp/ /hdfs/ ：put 原路径可以是文件夹等
hadoop fs -ls / ：查看根目录文件
hadoop fs -ls /tmp/data：查看/tmp/data目录
hadoop fs -cat <hdfs上的路径>:查看文件内容



1.Hadoop
3台机器，一个namenode，两个datanode
HDFS负责数据存储
MapReduce负责数据处理

yarn：资源协调管理框架（可以将HDFS应用与mapreduce或spark计算框架中）

安装：
内存1G
关闭防火墙
	永久关闭防火墙：
	执行：service iptables stop
	再次执行：chkconfig iptables off
配置主机名
配置host文件
配置免密码登录
安装JDK和hadoop

对于HDFS，namenode节点只存储和管理元数据信息，不存储具体的文件块
datanode用来存储文件块信息

HDFS技术指令：
查看元数据：hadoop fsck /park/1.txt -files -blocks -locations -racks 
查看1.txt这个文件的block信息以及机架信息，
包括文件名，文件大小，文件块数量，文件块编号，文件块存储的datano

HDFS特点：
支持超大文件
监测和快速应对硬件故障
流式数据访问——不适于低延迟的数据访问
简化的一致型模型——适合一次写入，多次读取——不允许对上传到HDFS中的文件做修改
高容错性
商用硬件

API：
eclipse通过插件创建map/Reduce Project

相关依赖：
jar包参考：
https://github.com/chubbyjiang/MapReduce
https://blog.csdn.net/qq_1290259791/article/details/78718392
        <!-- 大数据相关 -->
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-mapreduce-client-core</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.9.0</version>
        </dependency>


2018-12-07工作计划：
1.Hadoop测试连接和HDFS上下载文件
Configuration conf=new Configuration();
FileSystem fs=FileSystem.get(new URI("hadoop部署地址"), conf);
InputStream in=fs.open(new Path("/park/1.txt"));	//获取hadoop上的文件
OutputStream out=new FileOutputStream(new File("demo1.txt"));	//保存到本地
IOUtils.copyBytes(in, out, conf);
fs.close();

2.本地上传文件
Configuration conf=new Configuration();
FileSystem fs=FileSystem.get(new URI("hadoop部署地址"), conf);
InputStream in=new FileInputStream(new File("2.txt"));
OutputStream out=fs.create(new Path("/park/2.txt"));	//路径要写到文件级别
IOUtils.copyBytes(in, out, conf);
fs.close();

3.使用copy方法上传下载
Configuration conf=new Configuration();
FileSystem fs=FileSystem.get(new URI("hadoop部署地址"), conf);
fs.copyFromLocalFile(new Path("本地路径", new Path("HDFS路径")));	//上传
fs.copyToLocalFile(new Path("HDFS路径", new Path("本地路径")));	//下载
fs.close();

4.创建目录
fs.mkdirs(new Path("目录路径"));

5.删除目录
fs.delete(new Path("路径"));	//已过时
fs.delete(new Path("路径"),true);	//true 强制删除  false只能删除为空的目录

6.查看目录及文件信息
FileStatus[] status = fs.listStatus(new Path("路径"));

7.查看目录信息及目录下的文件信息
RemoteIterator<LocatedFileStatus> it = fs.listFiles(new Path("路径"), true);	//递归打印指定目录及文件，且只打印不为空的目录
RemoteIterator<LocatedFileStatus> it = fs.listFiles(new Path("路径"), false);	//不会递归打印，目录有文件才打印

8.重命名
fs.rename(new Path("路径"),new Path("路径"))

9.获取文件块信息
BlockLocation[] bl = fs.getFileBlockLocations(new Path("路径"), 起始0, length 0)——Integer.MAX_VALUE 最大数字
0,12,hadoop01——切块数，字节大小b，切块名
134217728=128M

HDFS体系结构
HDFS读流程图
HDFS写流程图
PipLine设计的目的是充分利用每台机器的带宽，最小化降低数据推送的延迟
将文件块chunk打散成一个个128kb大小的数据包packet进行传输

ack：命令正确应答
chunk：厚厚的一块

14*8=112
11.2*4=44
7*10=70


Datanode相关体系：
BlockSender BlockReceiver（数据的发送和接收类）
平衡工具balancer：（sbin目录下命令）sh start-balancer.sh -t 10%(磁盘使用偏差率)

DatanodeProtocol：namenode和datanode通信的类
sendheartbeat发送心跳包方法


Namenode相关体系：
实际控制两张表信息
1.文件——文件块信息
2.文件块信息——存储这个文件块机器列表信息（每次namenode重启，表则重新建立，保证数据的准确性）

LRU ... Least Recent Used，淘汰掉最不经常使用的

FSNameSystem：是HDFS文件系统实际执行的核心，提供各种文件增删查文件接口
FSDirectory：HDFS目录管理
FSimage：把文件和目录的元数据信息持久化的存储到fsimage中，是一个二进制文件
HeartbeatManager：心跳检测


1.Hadoop fs -lsr /  查询hadoop文件目录信息
perm：permission 权限
rwx：
r read
w write
x 可执行
分布式编程容易出现的文件
1.容易死锁
2.活锁——是死锁的变种（由于某一条件的发生，产生碰撞，导致重新执行，如此循环往复）
活锁的危害，谁也获取不到资源，活锁下的线程都是运行状态，导致白白耗费CPU资源


1.MapReduce：计算类型框架
map：映射
reduce：规约
 
namenode——resourcemanager(2.0)/jobtracker(1.0)
datanode——nodemanager(2.0)/tasktracker(1.0)

1.MR用集群的思想来计算和处理数据
2.MR需要一套机制来做任务的分配，即RM，NM是负责干活的
3.MR会对待处理的数据文件做逻辑切分，MR是按照128MB做逻辑切分，
切分后，一个切片（inputsplit）对应一个map任务
4.MR对于数据的处理方式默认是安装按行进行处理，一行一行读取
（非行首尾的文件追溯问题，zebra会向前或向后追溯，MR这里不需要考虑）
5.MR通过RPC心跳机制来监测集群中节点的变化


2018-12-17工作计划：
1.MapReduce有两个组件（mapper组件和reduce组件）
mapper组件：
XXX extends Mapper<keyin,valuein,kenout,valueout>
XXX extends Mapper<LongWritable,Text,LongWritable,Text>
//重写map方法
map(LongWritable key,Text value,Mapper<LongWritable,Text,LongWritable,Text>.Context context)

//#####LongWritable，IntWritable为Hadoop中的类型
//Mapper组件通过map方法，将每行的行首偏移量和内容传给我们
//第一个形参为Mapper输入的key类型，这个类型一般固定，LongWritable
//第二个形参为Mapper输入的value类型，对应的文件，Text
//第三个形参为Mapper输出的key类型，这个类型用户自定义，LongWritable
//第四个形参为Mapper输出的value类型，这个类型用户自定义，LongWritable
//最主要的要的是每行的内容

//打印key和value：
context.write(key,value);	//key是每行行首的偏移量，value是每行的内容

编写Driver类型
//测试类main方法
Configuration conf=new Configuration();
//Job代表MR的一个job任务
Job job=Job.getInstance(conf);
job.setMapperClass(自定义Mapper类);
//设置mapper输出的key类型
job.setMapOutputKeyClass(LongWritable.class);
//设置mapper输出的value类型
job.setMapOutputKeyClass(Text.class);
//指定处理的文件路径，可指定到目录或单文件级别
FileInputFormat.setInputPaths(job,new Path("待处理的文件路径"));
//指定输出结果的目录，会自动生成
FileOutputFormat.setOutputPaths(job,new Path("存放路径"));
//提交或运行一个job
job.waitForCompletion(true);

①使用jar包运行mr任务：
导出jar包，上传至hadoop，使用mapReduce运行jar包
jps查看HDFS进程
进入hadoop安装目录下的sbin目录
启动mapReduce相关进程（start-yarn.sh）(存在ResourceManager和NodeManger就证明启动成功)
运行jar包：hadoop jar 路径

- sz命令发送文件到本地: # sz filename 
rz命令本地上传文件到服务器

②使用插件运行mr任务： 
inpuPaths和outputPaths需要设置为绝对路径（hdfs://IP:端口/路径）


Reduce组件：会接收Mapper组件的输出结果
XXX extends Reducer<keyin,valuein,kenout,valueout>
XXX extends Mapper<LongWritable,Text,Text,Text>
//第一个形参为Mapper输出的key类型
//第二个形参为Mapper输出的value类型
//第三个形参为自定义输出的key类型
//第四个形参为自定义输出的value类型

//在Driver中添加Reduce
job.setReducerClass(XXXMapper.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(Text.class);

知识点：
1.reducer会接收mapper的输出结果，并且按照mapper输出的key做合并，并封装到Iterable通过reduce函数传过来
2.引入reduce组件后，输出结果是reducer的输出结果
3.可以只有mapper组件，但是不是只有Reducer组件

元数据：
hello world
hello hadoop
hello 1606
hello world

mapper生成数据（按行读取，每行按空格切分）：
hello 	1
world	1
hello 	1
hadoop	1
hello 	1
1606	1
hello 	1
world	1

reducer生成数据（接收mapper数据，并对Iterable中的数据循环打印1，）：
1606 	1,
hadoop	1,
hello	1,1,1,1,
world 	1,1, 

例：
单词统计案例——mapper每行按空格分，reducer记录Iterable中出现的次数
数据去重——reducer打印key和null
平均值——mapper每行按空格分（人名和分数），reducer按人名key对分数求平均值
最大值——
流量统计——自定义对象并可序列化（mapper输出Flow，reducer接收flow，再输出flow）
entity实现Writable接口，重写write和readFields方法（要注意顺序保持一致）

相互转换：
Text.toString==Text
IntWritable.get()==int
NullWritable.get()==null

分区：
mapper的任务数量取决于切片数量
hadoop默认就一个reducer
设置分区数量：
设置多个reducer：job.setNumReduceTasks(分区数量);
key.hashCode()&integer.max_value%2：hadoop的默认分区策略为hash散列算法
如果有多个分区，Hadoop默认使用哈希散列算法，对Mapper中的key的散列值进行分区，确保同一key落到同一个分区
有几个分区(reduce)，就会生成几个结果文件
默认分区弊病：会出现数据倾斜（某个文件结果很多，某个很少）

自定义分区：
1.新建class extends Partitioner<mapper输出的key,mapper输出的value>
2.实现gerPartition方法（key,value,numPartitions分区数量——从job里拿到的numReduce）
3.编写业务代码，选择分区编号——注意分区编号，编号从0开始，如果是3个分区，编号是0·2
if(value.getAddr().equals("bj")){
return 0
}if(value.getAddr().equals("sz")){
return 1
}
4.driver类中指定分区类，如果不设置，默认用hashPartitioner
job.setPartitionerClass(XXXClass.class);

——）——）——）——）——）
Hadoop先分区后合并

Writable序列化只适用于JAVA平台
Avro实现的序列化和反序列化——解决跨平台编程

job任务执行流程：

Hadoop任务分派策略：数据的本地化策略
Hadoop有个思想：移动的是运算，而不是数据（jar包存储在某个datanode节点上，就相当于存储在HDFS上）

例：计算成绩：三门学科，三个三件，三个学生，三个月份(统计每个人三个月每个学科的总成绩)
zhang [name=zhang,chinese=2000,english=300,math=500]
Chinese——English——Math
1 zhang 58
2 zhang 58
3 zhang 58
1 wang 58
2 wang 58
3 wang 58
1 li 58
2 li 58
3 li 58

获取文件名：
FileSplit split = (FileSplit)context.getInputSplit();
split.getPath();	//这里获取到的全路径
split.getPath().getName();	//这里获取到的文件名
Mapper类：
1.按“”切分，组装到Student类中
2.判断文件名，分别存储到Student中的Chinese，English，Math字段中
3.输出key和value（学生姓名，学生对象）
Reduce类：
1.new一个新的学生对象，传入学生成绩，对学生的成绩进行累加
2.输出key和value（学生姓名，学生对象）
 
核心原理：
mapper封装key值和Object对象
reduce合并对象，再进行下一步对象

例：排序：——MR会对Mapper输出的Key做排序
例：名称 排序
电影1 78
电影2 98
电影3 58
1.定义Movie对象并实现WritableComparable<Movie>接口，重写compareTo方法
字段：name hot
实现序列化与比较方法
compareTo(Movie o){
    return o.hot-this.hot;	//降序排序
	//相等0 指定数小于参数1 指定数大于参数-1
}
2.编写Mapper<LongWritable,TextWritable,Movie,NullWritable>	//key为Movie是为了实现排序
3.Mapper实现排序，不需要reduce，如果要去重，则可以添加reduce

例：全排序——一堆混乱数据（单文件有序，总文件有序）
0~100 0分区
100~1000 1分区
1000以上 2分区
生成三个结果文件，每个文件中按照数字大小排序升序，统计每个数字出现的次数
1.编写Mapper<LongWritable,TextWritable,IntWritable,IntWritable>
输出数字和1
2.编写Reduce<IntWritable,IntWritable,IntWritable,IntWritable>
编写词频统计逻辑
3.实现自定义分区 extends partitioner<IntWritable,IntWritable>
判断分区逻辑（if else判断/正则判断）
4.编写driver类
job.setNumReduceTasks(3);
job.setPartitionerClass(自定义分区类);

多级mr处理：——每个人三个月总利润排序
月份 人名 总收入 总支出
1 ls 2580 100
2 ls 2580 100
3 ls 2580 100
1 zs 2580 100
2 zs 2580 100
3 zs 2580 100
一个mr无法处理，将一个mr结果当作数据源给另一个mr
1.一级mapper
输出每个月人名，利润
2.一级reducer
合并3个月每个人总利润
3.编写人类implement WritableComparable<People>{
字段 name profit
实现序列化与compareTo方法
4.二级mapper
读取一级mr数据(如果按空格切  可能会出现异常，这时可以考虑tap切分)

hadoop默认的输出结果，key和value之间是以制表符tap分隔


combine过程：Hadoop默认没有combine——Mapper和Reduce交互，使每一个mapper中的数据合并发生在mapper中
将reduce合并key的过程分配到每个mapper任务中（hello 3，hello4），最后再合并（hello 7）
1.编写XXCombineClass extends Reducer<Mapperkey(Text),IntWritable,Text,IntWritable>
2.重写reduce方法，编写合并逻辑
3.job.setCombinerClass(XXCombineClass);		//设置Combiner
使用combine原则：加入combine之后，不能改变最终结果
如：hello 1 hello 1 hello 1 => hello 1,1,1
combine不能变成 hello 3

Shuffle过程：
1.map任务——分区+排序
2.当mapper存放在内存中的数据满了之后，出发spill溢写（已分区+排序），但多个spill总体文件不是分区+排序的
如果最后缓冲区还有数据，会调用flush输出，如果map的输出结果不到80mb，就不会发生spill溢写过程
3.缓冲区去默认大小：100mb，溢写阈值：80%
4.merge合并：生成最后的结果文件，分区+排序

Hadoop性能调优：
1.调大缓冲区大小，减小磁盘IO
2.加入combine模块，减少数据量，延缓spill过程
3.fetch线程数量，线程数量尽可能等于mapper数量
4.reduce什么时候开启fetch数据（默认是mapper完成比例 5%）可以调小，使reduce更快的开启任务
5.hadoop默认的merge因子：10
50个
5=>1
45=>4	余5
1+4+5=10
4
40个
4=>1
36=3 	余6
1+3+6=10



mr特性总结：
1.数据划分和计算任务调度
2.数据/代码互定位（数据本地化策略）——一个计算节点尽可能处理其本地磁盘上所分布存储的数据，实现代码向数据的迁移
3.系统优化
4.出错检测和恢复

Hadoop生态圈：
HDFS：hadoop分布式文件系统
Mapreduce：分布式计算框架
HBase：分布式列存数据库
Hive：数据仓库，运行存储再hadoop上的查询语句
Zookeeper：分布式协作服务
Flume：日志收集工具
Mahout：数据挖掘算法库
Yarn：分布式资源管理器（第二代Hadoop计算平台）

脑裂：某台master节点挂掉以后，恢复过来和另一个正在运行的master节点竞争
避免脑裂：再master节点中封装一层zookeeper结构，形成 slave-master/master-zookeeper 双层架构

源码解析：
数据处理引擎
MapperTask.run()——启动和执行一个Map任务，初始化和启动map任，调用的是run()方法
MapperRunner
	MapOutputCollector
		MapOutputBuffer
			SpillThread
	MapTask.mergeParts()

ReduceTask.run()——三个阶段，分别是：copy，sort，reduce
	MapOutPutCollector
		MapOutputCopier

hadoop参数控制与性能调优：
hdfs-site.xml：hdfs配置文件
mapred-site.xml：mapper配置文件

MR调优策略
Map Task和Reduce Task调优的一个原则就是
1.减少数据的传输量
2. 尽量使用内存
3. 减少磁盘 IO 的次数
4. 增大任务并行数
5. 除此之外还有根据自己集群及网络的实际情况来调优。


Hadoop集群：
hadoop2中，有两个namenode（目前只支持两个，不是secondarynamenode），
分别是active和standby，之间同步通信用的JournalNode的独立进程进行通信
使用6台搭建分布式环境：参考第五天内容
Park01 
Zookeeper  
NameNode(active)
Resourcemanager (active)
Park02
Zookeeper 
NameNode (standby)
Park03
Zookeeper 
ResourceManager(standby)
Park04
DataNode 
NodeManager 
JournalNode
Park05
DataNode
NodeManager
JournalNode
Park06
DataNode 
NodeManager 
JournalNode



自定义格式输入：
1.新建格式化输入类：
AuthInputFormat extends FileInputFormat<IntWritable输入的key,Text输入的value>	//根据实际业务调整
2.实现createRecordReader方法，方法中new一个格式读取器——即new AuthReader();
//Hadoop默认使用LineRecordReader，它是把每行的行首偏移量和内容作为key和value传给map

3.创建格式读取器
AuthReader extends RecordReader<IntWritable,value>	//对应format中的泛型
private FileSplit fs;
private IntWritable key;
private Text value;
//导包为hadoop.util
private LineReader reader;
private int count;
初始化方法：initialize()：
fs=(FileSplit) split;
Path path=fs.getPath();
Configuration conf =new Configuration();
FileSystem system = path.getFileSystem(conf);
FSDataInputStream in = system.open(path);
reader =new lineReader(in);
方法：nextKeyValue()	//会被调用多次，
//这个方法的返回值如果是true，就会被调用一次，直到false停止调用
//每当此方法被调用，getCurrentKey()和getCurrentValue()也会被跟着调用一次
//getCurrentKey()是给map传key的，getCurrentValue()是给map传value的
key=new IntWritabel();
value = new Text();
Text temp = new Text();
int length = reader.readLine(temp);
if(length == 0){	//当无文件可读，返回false
    return false
}else{
    //key value赋值
    value= temp;
    count++;
    key.set(count);
    return true;
}
方法：getCurrentKey() 返回key
方法：getCurrentValue() 返回value

##########
在Driver类中设置自定义的格式类，hadoop默认使用TextInputFormat这个类中的LineRecordReader读取器
job.setInputFormatClass(AuthInputFormat.class);

例：案例——统计每个人的成绩
tom
math 90
english 80
rose
math 85
english 75
。。。
要求输出结果：key为人名 value为学科成绩
tom math 90 english 80
rose math 85 english 75
编码解析：一次读三行，第一行为key，第二行和第三行为value
1.格式化输入：FormatScoreFormat extends FileInputFormat
RecordReader方法中new一个FormatScoreReader();
在nextKeyValue()方法中
key=new Text();
value = new Text();
Text temp = new Text();
int length = reader.readLine(temp);
if(length == 0){	//第一次读取人名
    return false;
}else{
    //key value赋值
    //key= temp;	//这里不能直接赋值，相当于值的拼接
    key.set(temp);
    //这里连读两行,获取所有该学生所有成绩
    for(int i=0; i<2; i++){
	reader.readLine(temp);
	//追加字符串
	value.append(temp.getBytes(), 0, temp.getLength())
	//追加空格
	value.append(" ".getBytes(), 0, " ".getLength())
    }
    return true;
}


设置多输入源：——多输入源不能设置job.setMapperClass();
例：两个文件：
MultipleInputs.addInputPath(job, new Path("文件级别路径"), inputFormatClass, mapperClass)
MultipleInputs.addInputPath(job, new Path("文件级别路径"), 使用默认TextInputFormat.class, mapperClass)

自定义格式输出：
1.新建格式化出类：
AuthOutputFormat extends FileOutputFormat<IntWritable输出的key,Text输出的value>	//根据实际业务调整
2.实现RecordWriter方法
//拿到输出结果路径
Path paht = super.getDefaultWorkFile(job, "");
Configuration conf =new Configuration();
FileSystem system = path.getFileSystem(conf);
FsdataOutPutStream out =system.create(path);
return new AuthOutputWriter<K,V>(out, "|", "\r\n");	//key和value之间的分隔符, 行之间的分隔符
3.创建格式输出器器
AuthOutputWriter<K,V> extends RecordWriter<K,V>
private FsDataOutputStream out;
private String keyValueSeparator;
private String lineSeparator;
方法 AuthOutputWriter中：
this.out=out;
this.keyValueSeparato=string;
this.lineSeparator=string2;
方法 write
//注意输出的顺序
out.write(key.toString().getBytes());
out.write(keyValueSeparator.getBytes());
out.write(value.toString().getBytes());
out.write(lineSeparator.getBytes());
Driver类中设置
job.setOutputFormatClass(对应的class);

多输出源：——多输出源不能设置job.setMapperClass();
例：根据人名将数据发送到三个不同结果文件中
1.新建FormatScoreReducer extends Reducer
private MutipleOutputs<Text,Text> mos;
方法：reduce
if(key.toString().equals("tom")){
	for(Text value:values){
	//如果用多输出源输出，就不用context输出
	mos.write("tom",key,value);
	}
}
。。。其他人名验证

Driver中设置：
MultipleOutputs.addNameOutput(job, "tom", TextOutputFormatClass, key, value)
MultipleOutputs.addNameOutput(job, "rose", TextOutputFormatClass, key, value)
MultipleOutputs.addNameOutput(job, "jary", TextOutputFormatClass, key, value)

自定义分区和多输出源区别：
1.自定义分区在PatitionClass文件中编写，多输出源在Reducer文件中编写
2.多输出源可以自定义文件名


Hadoop压缩机制：
1.gzip：不支持split
压缩：gzip 文件路径
解压：gzip -d 文件路径
2.bzip：支持split
压缩：bzip 文件路径
解压：bzip -d 文件路径

上传文件：hadoop fs -put 压缩文件路径 上传文件目录

生成结果文件压缩：
//hadoop默认不开启输出结果的压缩机制
FileOutputFormat.setCompressOutput(job,true)
//设置压缩实现类，一般常用gzip和bzip
FileOutputFormat.setOutputCompressorClass(job,GzipCoderC.class)

map任务的输入结果进行压缩：
conf.setBoolean("mapred.compress.map.output",true);
//指定压缩方式
conf.setClass("mapred.map.output.compression.codec",GzipCodec.class,CompressionCodec.class);

Hadoop JVM重用：
——不是指同一job的两个或两个以上的task可以同时运行于同一个jvm上，而是排队顺序执行
参数设置：mapred-site.xml 默认是2，指的是同一个job的task数量
hadoop2.0在yarn中配置：uber，默认禁止uber组件，即不允许JVM重用
在yarn-site.xml配置mapreduce.job.ubertask.enable 为true	//启用后会将小job的所有task任务在同一个JVM中执行
mapreduce.job.ubertask.maxmaps 9	//map数量小于9就会认为是小任务。reduce数量必须为1个，如果reduce大于2 ，就不是小job了
mapreduce.job.ubertask.maxreduces 1	//配置启动uber模式的最大reduce数 ，yarn不支持设置>1
同一个job的mapper的jvm可以复用，如果task属于不同的job，jvm重用机制无效

小文件代码处理：
——————————————————————————
CombineFileInputFormat是一种新的inputformat，用于将多个文件合并成一个单独的split，它会考虑数据的存储位置，
此外:在数据处理的最前端（预处理、采集）就将小文件合并为大文件然后再上传到hdfs
 
已经是大量的小文件在HDFS中了，可以使用另一种inputformat来做切片（CombineFileInputformat），
它的切片逻辑跟FIleinputformat不同： 

它可以将多个小文件从逻辑上规划到一个切片中，这样，多个小文件就可以交给一个maptask了
——————————————————————————
如：
word.txt	hello hadoop
word.txt	hello 1607
word.txt	hello world
word.txt - 副本	hello world
word.txt - 副本	hello world
word.txt - 副本	hello world
例：工具类文件：第六天 cn.tarena.small下
实现步骤：
在COmbineSmallfileImputFormat中实现RecordReader方法，方法中new一个格式读取器
注意：——这里一定要通过CombineFileRecordReader来创建一个RecordReaderActor模型
CombineFileSplit combinewFileSplit = (CombineFileSplit) split;
CombineFileRecordReader<LongWritable,BytesWrittable> recordReader = 
new CombineFileRecordReader<LongWritable,BytesWrittable>(combineFileSplit, context, CombineSmallFileRecord.class-这里是自定义的reader);
不同于上面的格式输入的直接new方式

















