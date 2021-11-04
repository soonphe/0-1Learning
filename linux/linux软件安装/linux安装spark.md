# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux安装spark

1.下载 安装（这里选择了待Hadoop的版本）
spark-2.1.1-bin-hadoop2.7
 
————————————————————————————————————————————————————————————
启动只需要在master上启动就行了
因为spark是依赖于hadoop提供的分布式文件系统的，所以在启动spark之前，先确保hadoop在正常运行。Hadoop2.8.0的安装和启动，请参考该博文：
 
在hadoop正常运行的情况下，在hserver1（也就是hadoop的namenode，spark的marster节点）上执行命令：
cd   /opt/spark/spark-2.1.1-bin-hadoop2.7/sbin
 
执行启动脚本：
./start-all.sh
 
 
2.解压
tar   -zxvf   spark-2.1.1-bin-hadoop2.7.tgz
 
3.添加环境变量
export  SPARK_HOME=/opt/spark/spark-2.1.1-bin-hadoop2.7  
      
上面的变量添加完成后编辑该文件中的PATH变量，添加
${SPARK_HOME}/bin  
 
4.刷新环境变量
source /etc/profile
 
5.新建spark-env.h文件
 
执行命令，进入到/opt/spark/spark-2.1.1-bin-hadoop2.7/conf目录内：
 
cd    /opt/spark/spark-2.1.1-bin-hadoop2.7/conf
 
以spark为我们创建好的模板创建一个spark-env.h文件，命令是：
 
cp    spark-env.sh.template   spark-env.sh
 
编辑文件：
export SCALA_HOME=/opt/scala/scala-2.12.2  
export JAVA_HOME=/opt/java/jdk1.8.0_121  
export HADOOP_HOME=/opt/hadoop/hadoop-2.8.0  
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop  
export SPARK_HOME=/opt/spark/spark-2.1.1-bin-hadoop2.7  
export SPARK_MASTER_IP=hserver1  
export SPARK_EXECUTOR_MEMORY=1G  
 
 
6.新建slave文件
 
执行命令，进入到/opt/spark/spark-2.1.1-bin-hadoop2.7/conf目录内：
 
cd   /opt/spark/spark-2.1.1-bin-hadoop2.7/conf
 
以spark为我们创建好的模板创建一个slaves文件，命令是：
 
cp    slaves.template   slaves
 
编辑文件
hserver2  
hserver3  
 
 
 
 