# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## linux安装mysql

卸载MariaDB
CentOS7默认安装MariaDB而不是MySQL，而且yum服务器上也移除了MySQL相关的软件包。因为MariaDB和MySQL可能会冲突，故先卸载MariaDB。
 
查看已安装的MariaDB相关rpm包。
rpm -qa | grep mariadb
 
查看已安装的MariaDB相关yum包，包名需根据rpm命令的结果判断。
yum list mariadb-libs
 
移除已安装的MariaDB相关yum包，包名需根据yum list命令的结果判断。此步骤需要root权限。
yum remove mariadb-libs
 
下载mysql rpm包
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar
 
使用rpm包安装MySQL
 
以下步骤需要root权限。且因包之间的依赖关系，各rpm命令必须按序执行。***********************
 
mkdir mysql-5.7.18
tar -xv -f mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar -C mysql-5.7.18
cd mysql-5.7.18/
rpm -ivh mysql-community-common-5.7.18-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.18-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.18-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.18-1.el7.x86_64.rpm
安装成功后，也可把安装文件和临时文件删除。
 
cd ..
rm -rf mysql-5.7.18
rm mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar
 
 
mysql重新启动：service mysqld restart
查询mysql日志信息：journalctl |grep mysql
 
查看端口
    netstat -nltp;（简单的进程用kill -9 端口号杀死）
 
由于一开始并不知道密码，先修改配置文件/etc/my.cnf令MySQL跳过登录时的权限检验。加入一行：
skip-grant-tables
 
重启MySQL。
service mysqld restart
 
免密码登录MySQL。
mysql
 
在mysql客户端执行如下命令，修改root密码。
use mysql;
UPDATE user SET authentication_string = password('your-password') WHERE host = 'localhost' AND user = 'root';
quit;
 
修改配置文件/etc/my.cnf删除此前新增那一行skip-grant-tables，并重启MySQL。这一步非常重要，不执行可能导致严重的安全问题。
使用刚刚设置的密码登录。
mysql -u root -p
 
mysql需要你重置密码（不能是简单类型）：
ALTER USER root@localhost IDENTIFIED BY 'Admin@123';
 
------------------------------------------------------
#启动mysql
service mysqld start
#关闭mysql
service mysqld stop
#查看运行状态
service mysqld status
 
 
/*在linux下删mysql数据库*/
1、使用以下命令查看当前安装mysql情况
rpm -qa|grep -i mysql  
显示之前安装了：
MySQL-client-5.5.25a-1.rhel5
MySQL-server-5.5.25a-1.rhel5
 
2、停止mysql服务、删除之前安装的mysql
rpm -ev MySQL-client-5.5.25a-1.rhel5  
rpm -ev MySQL-server-5.5.25a-1.rhel5 
 
 
linux下MySql数据库常用的基本操作： /*一行结束必须要加 ; */
1、显示数据库： show databases;
2、选择数据库：use 数据库名;
3、显示数据库中的表：show tables;
4、显示数据表的结构：describe 表名;
5、建库：create databse 库名;
6、删库：drop database 库名;
 
建表：create table 表名 (字段设定列表)；
create table name(
    id int auto_increment not null primary key ,
    uname char(8),
    gender char(2),
    birthday date );
 
删表：drop table 表名
 
插入：insert into name(uname,gender,birthday) values('张三','男','1971-10-01');
 
修改：update name set birthday='1971-01-10' where uname='张三';
 
删除：delete from name where uname='张三';
 
数据库备份：mysqldump -u root -p --opt 数据库名>备份名; //进入到库目录
 
恢复：mysql -u root -p 数据库名<备份名; //恢复时数据库必须存在，可以为空数据库
 
数据库授权：grant select on 数据库.* to 用户名@登录主机 identified by "密码"
 //首先用以root用户连入MySQL，然后键入以下命令：
mysql>grant select,insert,update,delete on test.* to user002@localhost identified by "123456";  //123456为user002的密码
 
mysql远程访问：
1.查看端口（window常用为3306，linux常用为63306，端口可以在/etc/my.cnf中配置修改）
    netstat -nltp;（简单的进程用kill -9 端口号杀死）
    修改my.cnf文件，在[mysqld]下加入skip-grant-tables
    vi /etc/my.cnf
    skip-grant-tables
    开启mysql服务
    systemctl start mysqld.service;
    进入mysql
    mysql -uroot -p;
    再回车；
    修改mysql密码
    update mysql.user set Password=password('123456') where User='root';
    执行权限
    flush privileges;
2、给刚才的用户赋予所有权限    
    mysql -uroot -p123456
    grant all privileges on *.* to root@"%" identified by '123456';
 
mysql主从配置：
一、主服务器配置
1、创建同步账户并指定服务器地址
[root@localhost ~]mysql -uroot -p
mysql>use mysql
mysql>grant replication slave on *.* to 'root'@'192.168.1.102' identified by '123456';
mysql>flush privileges #刷新权限
授权用户testuser只能从192.168.1.102这个地址访问主服务器192.168.1.101的数据库，并且只具有数据库备份的权限
 
2、修改/etc/my.cnf配置文件vi /etc/my.cnf
[mysqld]下添加以下参数，若文件中已经存在，则不用添加
server-id=1  （可以设置为服务器ip尾缀）
log-bin=mysql-bin  #启动MySQL二进制日志系统，
binlog-do-db=ourneeddb  #需要同步的数据库（lwgk中lwgk数据库）
binlog-ignore-db=mysql  #不同步mysql系统数据库，若还有其它不想同步的，继续添加
[root@localhost ~]/etc/init.d/mysqld restart #重启服务
 
3、查看主服务器master状态(注意File与Position项，从服务器需要这两项参数)
 
mysql> show master status;
+------------------+----------+--------------+------------------+
| File            | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000012 |      120 | ourneeddb| mysql            |
 
4.导出相关DB
 
二、从服务器配置
1、修改配置文件
[mysqld]下添加以下参数，若文件中已经存在，则不用添加
server-id=2  #设置从服务器id，必须于主服务器不同
log-bin=mysql-bin  #启动MySQ二进制日志系统
replicate-do-db=ourneeddb  #需要同步的数据库名
replicate-ignore-db=mysql  #不同步mysql系统数据库
[root@localhost~ ]/etc/init.d/mysqld restart #重启服务
 
2.导入数据库
 
3.配置主从同步
[root@localhost~ ]mysql -uroot -p
mysql>use mysql 
mysql>stop slave;
mysql>change master to
      master_host='192.168.1.101',
      master_user='root',
      master_password='123456',
      master_log_file='mysql-bin.000012',        
      master_log_pos=120;  #log_file与log_pos是主服务器master状态下的File与Position
mysql>start slave;
mysql>show slave status\G;
 
注意查看Slave_IO_Running: Yes  Slave_SQL_Running: Yes 这两项必须为Yes 以及Log_File、Log_Pos要于master状态下的File,Position相同
 
如果都是正确的，则说明配置成功！
