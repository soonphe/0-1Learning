# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## linux安装JDK

### 方法一、yum一键安装
这种办法简单粗暴，就像盖伦丢技能一样。废话不多说，直接开始操作。

1. 首先执行以下命令查看可安装的jdk版本：
```
yum -y list java*
```
2. 选择自己需要的jdk版本进行安装，比如这里安装1.8，执行以下命令：
```
yum install -y java-1.8.0-openjdk-devel.x86_64
```
等待安装完成即可。

3. 安装完成之后，查看安装的jdk版本，输入以下指令：
```
java -version
```
此处便可以看到自己安装的jdk版本信息。
你如果好奇这个自动安装把jdk安装到哪里去了，其实你可以在usr/lib/jvm下找到它们。

 
### 方法二手动解压JDK的压缩包，然后设置环境变量
 
1.在/usr/目录下创建java目录
 
[root@localhost ~]# mkdir/usr/java
[root@localhost ~]# cd /usr/java
 
2.下载jdk,然后解压
 
[root@localhost java]# curl -O http://download.Oracle.com/otn-pub/java/jdk/7u79-b15/jdk-7u79-linux-x64.tar.gz 
[root@localhost java]# tar -zxvf jdk-7u79-linux-x64.tar.gz
 
3.设置环境变量
 
[root@localhost java]# vi /etc/profile
 
在profile中添加如下内容:
 
#set java environment
JAVA_HOME=/usr/java/jdk1.8.0_131
JRE_HOME=/usr/java/jdk1.8.0_131/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
 
让修改生效:
 
[root@localhost java]# source /etc/profile
 
4.验证JDK有效性
 
[root@localhost java]# java -version
java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)
