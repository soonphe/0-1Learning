使用Homebrew安装Hadoop
brew install hadoop

①.根据上图输出的Hadoop安装路径执行下面代码修改core-site.xml文件，修改hadoop存储元数据的目录，即hadoop.tmp.dir

vim /usr/local/Cellar/hadoop/3.3.1/libexec/etc/hadoop/core-site.xml

在<configuration></configuration>标签内追加以下代码块
```
  <property>
     <name>hadoop.tmp.dir</name>
     <value>file:/usr/local/Cellar/hadoop/3.3.1/libexec/tmp</value>
  </property>
  <property>
     <name>fs.defaultFS</name>
     <value>hdfs://localhost:8020</value>
  </property>
  ```

②.修改hdfs-site.xml文件
修改hadoop副本数量，即dfs.replication
修改hdfs系统存放fsimage文件的目录，即dfs.namenode.name.dir
修改hdfs系统存放数据文件的目录，即dfs.datanode.data.dir（作用是存放hadoop的数据节点datanode里的多个数据块）

vim /usr/local/Cellar/hadoop/3.3.1/libexec/etc/hadoop/hdfs-site.xml

在<configuration></configuration>标签内追加代码块
```
    <property>
         <name>dfs.replication</name>
         <value>1</value>
    </property>
    <property>
         <name>dfs.namenode.name.dir</name>
         <value>file:/usr/local/Cellar/hadoop/3.3.1/libexec/tmp/dfs/name</value>
    </property>
    <property>
         <name>dfs.datanode.data.dir</name>
         <value>file:/usr/local/Cellar/hadoop/3.3.1/libexec/tmp/dfs/data</value>
    </property>
```

4.修改Hadoop环境变量
执行vim ~/.bash_profile，追加以下内容（路径跟随自己安装的路径变化）
```
export HADOOP_HOME=/usr/local/Cellar/hadoop/3.3.1/libexec
export HADOOP_COMMON_HOME=$HADOOP_HOME
export PATH=$JAVA_HOME/bin:$PATH:$HADOOP_HOME/bin:/usr/local/Cellar/scala/bin
```

执行source ~/.bash_profile，刷新环境变量

运行Hadoop自带的示例文件WordCount

1.初始化namenode节点

首先执行cd /usr/local/Cellar/hadoop/3.3.1/bin

第一次启动得先格式化： 进入到hadoop的bin目录下执行./hdfs namenode -format


2.启动hadoop

执行cd /usr/local/Cellar/hadoop/3.3.1/sbin

进入到hadoop的sbin目录下执行./start-dfs.sh

然后执行jps查看是否有NameNode和DataNode、SecondaryNameNode，有即启动成功

在浏览器输入http://localhost:9870/dfshealth.html#tab-overview也能查看

---

配置启动YARN

从上图看看出我们的MapReduce是运行在YARN上的，而YARN是运行在HDFS之上的，我们已经安装了HDFS现在来配置启动YARN，然后运行一个WordCount程序。

3.修改yarn配置文件mapred-site.xml

vim /usr/local/Cellar/hadoop/3.3.1/libexec/etc/hadoop/mapred-site.xml

在<configuration></configuration>标签内追加以下内容
```
<property> 
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
</property>
```

4.修改yarn配置文件yarn-site.xml

vim /usr/local/Cellar/hadoop/3.3.1/libexec/etc/hadoop/yarn-site.xml

在<configuration></configuration>标签内追加以下内容，以为是伪分布式，所以yarn.resourcemanager.hostname设置为localhost
```
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.env-whitelist</name>
    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
  </property>
  <property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>20480</value>
  </property>
  <property>
    <name>yarn.scheduler.minimum-allocation-mb</name>
    <value>2048</value>
  </property>
  <property>
    <name>yarn.nodemanager.vmem-pmem-ratio</name>
    <value>2.1</value>
  </property>
  <property> 
    <name>yarn.nodemanager.disk-health-checker.min-healthy-disks</name> 
    <value>0.0</value> 
  </property> 
  <property> 
    <name>yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage</name>
    <value>100.0</value> 
  </property>

```

5.启动yarn

执行cd /usr/local/Cellar/hadoop/3.3.1/sbin

进入到hadoop的sbin目录下，执行./start-yarn.sh

使用jps查看是否出现：NodeManager和ResourceManager

或在浏览器输入http://localhost:8088/cluster  即可访问yarn界面

yarn启动后用jps查看没有resourcemanager 
解决：
vim /usr/local/Cellar/hadoop/3.3.1/libexec/etc/hadoop/yarn-env.sh
添加
```
export YARN_RESOURCEMANAGER_OPTS="--add-opens java.base/java.lang=ALL-UNNAMED"
export YARN_NODEMANAGER_OPTS="--add-opens java.base/java.lang=ALL-UNNAMED"
```


6.运行wordcount文件

①使用hdfs创建/input文件夹以及测试输入文件

hadoop fs -mkdir /input

输入hadoop fs -ls /查看刚才创建的input文件夹是否成功，如果成功会看到输出input文件夹的信息（读写权限、组等）

mkdir ~/hadoopTest

进入到~/hadoopTest下执行touch test.txt，然后vim test.txt，随便输入测试语句，例如输入Hello World!，保存退出

执行exit，退出root用户
然后执行hadoop fs -put ~/hadoopTest/test.txt /input，将刚才创建的测试文件上传到hdfs分布式文件系统中

执行hadoop fs -ls /input查看test.txt是否成功上传到hdfs分布式文件系统中

②执行wordcount
```
hadoop jar /usr/local/Cellar/hadoop/3.3.1/libexec/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount /input/test.txt /output
```
wordcount是主类名
/input/test.txt是输入文件目录
/output是输出文件目录，输出文件必须是一个不存在的目录，防止hadoop重写已存在的文件

我们通过浏览器访问和下载查看结果：




停止hdfs：sbin/stop-dfs.sh
停止yarn：sbin/stop-yarn.sh


HDFS shell
查看帮助：hadoop fs -help <cmd>
上传：hadoop fs -put <linux上文件>  <hdfs上的路径>
查看文件内容：hadoop fs -cat <hdfs上的路径>
查看文件列表：hadoop fs -ls /
下载文件：hadoop fs -get <hdfs上的路径>  <linux上文件>



