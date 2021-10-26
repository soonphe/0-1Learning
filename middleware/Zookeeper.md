# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Zookeeper
官方资源包：http://zookeeper.apache.com/
国内压缩包地址：
```
https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.6.3/apache-zookeeper-3.6.3-bin.tar.gz
```

### jdk环境
```
vim ~/.bash_profile

export JAVA_HOME=/opt/edas/jdk/jdk1.8.0_65
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=.:${JAVA_HOME}/bin:$PATH

source ~/.bash_profile
```

### 单机环境搭建
操作
```
1. 下载压缩包

2.tar –xvf 命令将包解压到你想要的路径：
tar -zxf apache-zookeeper-3.6.3-bin.tar.gz -C /usr/local

3.创建两个文件夹，路径如图：
mkdir data
mkdir logs

4.你可以在conf文件里面复制zoo_sample.cfg,并重命名为zoo.cfg,或者创建一个文件命名为zoo.cfg
tickTime=2000
dataDir=/usr/local/apache-zookeeper-3.6.3-bin/data
dataLogDir=/usr/local/apache-zookeeper-3.6.3-bin/logs
clientPort=2181
dataDir和dataLogDir的路径是你创建的文件夹的路径

5.命令
./zkServer.sh start
./zkServer.sh stop
./zkServer.sh restart
./zkServer.sh status

./zkServer.sh start-foreground查看相关启动信息
./zkCli.sh -server 127.0.0.1:2185  利用客户端测试连接（ls 还能查看所有节点）

6.启动前利用命令：netstat -tunlp|grep 端口号  查看端口有没有被占用，有的话kill掉，或者改用其他端口
```

常见问题
- 可能端口被占用，端口被占用就换
- 也有可能因为jdk问题，jdk的问题可能是你的环境变量没配好
- 连接zookeeper的时候要注意防火墙有没有开

### 集群环境搭建

操作步骤：
```
1. 新增myid文件
cd /opt
mkdir zk
vim myid
1
将1 写入文件，各机器依次递增

2.修改zoo.cfg配置
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg

dataDir=/opt/zk
# the port at which the clients will connect
clientPort=2181
server.1=192.168.161.224:2888:3888
server.2=192.168.161.44:2888:3888
server.3=192.168.161.112:2888:3888
```

### 配置文件说明
```
# The number of milliseconds of each tick       心跳间隔 毫秒每次
tickTime=2000
# The number of ticks that the initial          LF初始通信时限（集群中的follower服务器(F)与leader服务器(L)之间 初始连接 时能容忍的最多心跳数（tickTime的数量））
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between     LF同步通信时限（集群中的follower服务器(F)与leader服务器(L)之间 请求和应答 之间能容忍的最多心跳数（tickTime的数量）。）
# sending a request and getting anacknowledgement
syncLimit=5
# the directory where the snapshot isstored.    数据文件和日志文件地址
dataDir=/usr/local/apache-zookeeper-3.6.3-bin/data
dataLogDir=/usr/local/apache-zookeeper-3.6.3-bin/data
# the port at which the clients willconnect     客户端连接的端口
clientPort=2181
```


