# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux安装hadoop

1.下载hadoop安装包
2.使用tar zxvf 命令解压
修改环境变量
 
# 修改配置文件
vi /etc/profile
# 在最后下添加
 
export HADOOP_HOME=/opt/soft/hadoop-2.7.2
export PATH=$PATH:$HADOOP_HOME/bin
 
# 刷新配置文件
source /etc/profile
 
3.配置host文件，准备地址映射
进入/etc/hosts，配置主机名和ip的映射， 这里是集群的每个机子都需要配置，这里我的logsrv02是主机（master），其余两台是从机（slave）
[root@logsrv03 /]# vi /etc/hosts  
172.16.100.111 master
172.16.100.112 slave1  
172.16.100.113 slave2
 
4.JDK安装
jdk下载，解压到某个目录下，然后到/etc/profile中配置环境变量，在执行Java -version验证是否安装成功
 
5、配置SSH免密码登陆
这里所说的免密码登录是相对于主机master来说的，master和slave之间需要通信，配置好后，master和slave进行ssh登陆的时候不需要输入密码。
如果系统中没有ssh的需要安装，然后执行：
cd /root/.ssh/
ssh-keygen -t rsa  
 
将公钥拷贝都其他机器上：ssh-copy-id slave1
shh登录测试：ssh slave1（这个时候是不需要输密码的）
 
6.修改配置文件
配置文件全部位于 /opt/soft/Hadoop-2.7.2/etc/hadoop 文件夹下
core-site.xml
hdfs-site.xml
 
mapred-site.xml 
必须先
mv mapred-site.xml.template mapred-site.xml
 
yarn-site.xml
 
7.复制到其他主机
复制/etc/hosts(因为少了这个导致secondarynamenode总是在slave1启动不起来)
 
scp /etc/hosts slave1:/etc/
scp /etc/hosts slave2:/etc/
复制/etc/profile (记得要刷新环境变量 source /etc/profile)
 
scp /etc/profile slave1:/etc/
scp /etc/profile slave2:/etc/
复制/opt/soft
 
scp -r /etc/soft slave1:/opt/
scp -r /etc/soft slave2:/opt/
记得在slave1和slave2上刷新环境变量
 
启动
 
第一次启动得格式化
 
./bin/hdfs namenode -format
启动dfs
 
./sbin/start-dfs.sh
启动yarn
 
./sbin/start-yarn.sh
 
 
 
3、YARN的启动与停止
 
启动
 
./sbin/start-yarn.sh
如下：
 
 
