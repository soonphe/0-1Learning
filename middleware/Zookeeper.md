# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Zookeeper

1.下载tar压缩包，解压

2.Windows下安装

把下载的zookeeper的文件解压到指定目录
D:\machine\zookeeper-3.3.6>

修改conf下增加一个zoo.cfg
内容如下：
# The number of milliseconds of each tick  心跳间隔 毫秒每次
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting anacknowledgement
syncLimit=5
# the directory where the snapshot isstored.  //镜像数据位置
dataDir=D:\\data\\zookeeper
#日志位置
dataLogDir=D:\\logs\\zookeeper
# the port at which the clients willconnect  客户端连接的端口
clientPort=2181
注：如果启动有报错提示cfg文件有错误，可以用zoo_sample.cfg内内容替代也是可以的

进入到bin目录，并且启动zkServer.cmd，这个脚本中会启动一个java进程
D:\machine\zookeeper-3.3.6>cd bin
D:\machine\zookeeper-3.3.6\bin>
D:\machine\zookeeper-3.3.6\bin >zkServer.cmd（linux为sh）
启动后jps可以看到QuorumPeerMain的进程
D:\machine\zookeeper-3.3.6\bin >jps

启动客户端运行查看一下
D:\machine\zookeeper-3.3.6\bin>zkCli.cmd -server 127.0.0.1:2181

实际集群可参考——linux安装zookeeper.txt

伪集群：
在 一台机器上通过伪集群运行时可以修改 zkServer.cmd 文件在里面加入
set ZOOCFG=..\conf\zoo1.cfg  这行，另存为  zkServer-1.cmd

多台部署以此类推，配置多个zooX.cfg（这里clientPort=2181使用不同端口，实际多台机器集群使用相同端口）,
在dataDir=D:\\data\\zookeeper\X 建立myid文件，里面写入对应的X（多台机器相同文件路径也要写入不同的的值，值与server.X相同）
server.1=127.0.0.1:2888:3888
server.2=127.0.0.1:2889:3889
server.3=127.0.0.1:2890:3890


————————————————————————————————————————————————————————
windows下查看zookeeper节点：

进入到安装目录的bin下 
例如：C:\resources\zookeeper\zookeeper-3.4.9\bin

按着Shift再点击鼠标右键选择：在此处打开命令窗口
执行
zkCli.cmd -server 192.168.18.191:2181
1
再执行 ls / 下就能看到节点了
