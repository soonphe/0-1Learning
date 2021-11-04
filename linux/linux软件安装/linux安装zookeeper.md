# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux安装zookeeper

编辑主机host名称
vi /etc/hosts
 
scp命令：
例：将/usr/cstor/zookeeper目录传到另外两台机器上。
 
 　　　　scp -r /usr/cstor/zookeeper root@slave2:/usr/cstor
 
 　　　　scp -r /usr/cstor/zookeeper root@slave3:/usr/cstor
 
SSH免密远程访问：（需配置免密登录）
ssh root@SparkMaster
 
1. 下载
 
官网：http://zookeeper.apache.org/releases.html 
下载：zookeeper-3.4.8.tar.gz
 
2. 安装
 
因为资源有限，所以我在同一个服务器上面创建3个目录 server1、server2、server3 来模拟3台服务器集群。 
cd server1 
tar -zxvf zookeeper-3.4.8.tar.gz 
mkdir data (我这里在/usr/local目录下创建，后面的myid也在这里设置)
mkdir dataLog 
data 为数据目录，dataLog 为日志目录。
 
3. 配置
 
cd zookeeper-3.4.8/conf 
创建文件 zoo.cfg，内容如下：
 
tickTime=2000 
initLimit=5
syncLimit=2
dataDir=/usr/local/data
dataLogDir=/usr/local/dataLog
clientPort=2181
server.1=127.0.0.1:2888:3888
server.2=127.0.0.1:2889:3889
server.3=127.0.0.1:2890:3890
修改host可配置别名
server.1=slave1:2888:3888
server.2=slave2:2888:3888
server.3=slave3:2888:3888
tickTime：zookeeper中使用的基本时间单位, 毫秒值。 
initLimit：这个配置项是用来配置 Zookeeper 接受客户端（这里所说的客户端不是用户连接 Zookeeper 服务器的客户端，而是 Zookeeper 服务器集群中连接到 Leader 的 Follower 服务器）初始化连接时最长能忍受多少个 tickTime 时间间隔数。这里设置为5表名最长容忍时间为 5 * 2000 = 10 秒。 
syncLimit：这个配置标识 Leader 与 Follower 之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就是 2 * 2000 = 4 秒。 
dataDir 和 dataLogDir 看配置就知道干吗的了，不用解释。 
clientPort：监听client连接的端口号，这里说的client就是连接到Zookeeper的代码程序。 
server.{myid}={ip}:{leader服务器交换信息的端口}:{当leader服务器挂了后, 选举leader的端口} 
maxClientCnxns：对于一个客户端的连接数限制，默认是60，这在大部分时候是足够了。但是在我们实际使用中发现，在测试环境经常超过这个数，经过调查发现有的团队将几十个应用全部部署到一台机器上，以方便测试，于是这个数字就超过了。
 
 
 
 
4.新建两个目录。(如果第一步已经新建，则这里就不需要)
　　　　mkdir /usr/local/data
　　　　mkdir /usr/data/dataLog
 
　　
　　将/usr/cstor/zookeeper目录传到另外两台机器上。
 
 　　　　scp -r /usr/local/zookeeper root@slave2:/usr/cstor
 
 　　　　scp -r /usr/local/zookeeper root@slave3:/usr/cstor
 
　　分别在三个节点上的/usr/local/data目录下创建一个文件：myid。
 
　　　　　　vi /usr/local/data/myid　
 
　　分别在myid上按照配置文件的server.<id>中id的数值，在不同机器上的该文 件中填写相应过的值，如下：
　　　　　　slave1   的myid内容为1
　　　　　　slave2   的myid内容为2
    　　　　slave3   的myid内容为3
 
5.启动ZooKeeper集群
 
　　　　分别在三个节点进入bin目录，启动ZooKeeper服务进程：
 
　　　　　　　　cd /usr/cstor/zookeeper/bin
 
　　　　　　　　./zkServer.sh start
关闭./zkServer.sh stop
 
 
　　　　在各机器上依次执行脚本，查看ZooKeeper状态信息，两个节点是follower状态，一个节点是leader状态：
 
　　　　　　　　./zkServer.sh status
 
　　　　在其中一台机器上执行客户端脚本：
 
　　　　　　　　./zkCli.sh -server slave1:2181,slave2:2181,slave3:2181
 
 
 
 
