# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## mysql
MySQL 是最流行的关系型数据库管理系统,在 WEB 应用方面 MySQL 是最好的 RDBMS(Relational Database Management System:关系数据库管理系统)应用软件之一

### brew方式安装
- brew search mysql   //搜索软件包
- brew install mysql  //安装软件包
- brew services start mysql   //启动mysql
- brew services stop mysql   //关闭mysql

### docker方式安装
- docker search mysql //搜索
- docker pull mysql:5.7    //拉取镜像
- docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7    //启动容器

### 压缩包方式安装
先检查系统是否安装有mysql
```
yum list installed mysql*
rpm –qa|grep mysql*

查看有没有安装包
yum list mysql* 
```
下载并安装MySQL官方的 Yum Repository
```
[root@BrianZhu /]# wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
[root@BrianZhu /]# yum -y install mysql57-community-release-el7-10.noarch.rpm
[root@BrianZhu /]# yum -y install mysql-community-server
```

### mysql常用命令
```
vim /etc/my.cnf --配置文件地址
service mysqld start      --启动mysql
service mysqld stop       --关闭mysql
service mysqld status     --查看运行状态
systemctl status mysqld               //systemctl查看mysqld服务
systemctl enable mysqld               //systemctl设置开机启动
systemctl daemon-reload               //systemctl重载服务

mysql -u root -p    //进入mysql容器中

grep "password" /var/log/mysqld.log     //找到初始密码 
mysqladmin -u root password 密码  //创建root管理员
```

 
### linux下MySql数据库常用的基本操作（一行结束必须要加 ; ）
- 显示数据库： show databases;
- 选择数据库：use 数据库名;
- 显示数据库中的表：show tables;
- 显示数据表的结构：describe 表名;
- 建库：create databse 库名;
- 删库：drop database 库名;
- 建表：create table 表名 (字段设定列表)；示例：create table name(id int auto_increment not null primary key ,uname char(8));
- 删表：drop table 表名
- 插入：insert into name(uname,gender,birthday) values('张三','男','1971-10-01');
- 修改：update name set birthday='1971-01-10' where uname='张三';
- 删除：delete from name where uname='张三';
- 数据库备份：mysqldump -u root -p --opt 数据库名>备份名; //进入到库目录
- 恢复：mysql -u root -p 数据库名<备份名; //恢复时数据库必须存在，可以为空数据库
— 修改root账户密码：ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password'; 
- 允许远程访问（改表法）：UPDATE user SET `Host` = '%' WHERE `User` = 'root' LIMIT 1; flush privileges;
- 新建用户：CREATE USER 'monitor'@'%' IDENTIFIED BY 'admin';
- 数据库授权：grant select on 数据库.* to 用户名@登录主机 identified by "密码"
```
如果没有密码
    查看端口（window常用为3306，linux常用为63306，端口可以在/etc/my.cnf中配置修改）
    netstat -nltp;
    修改my.cnf文件，在[mysqld]下加入skip-grant-tables 跳过密码验证
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

有密码，这里root@"%"也可以实现远程登录（授权法）
    mysql -uroot -p123456
    授予全部权限 
    grant all privileges on *.* to root@"%" identified by '123456';
    授予部分权限    
    grant select,insert,update,delete on test.* to user002@localhost identified by "123456";
    flush privileges;
```
 
### mysql主从配置：

####一、主服务器配置
1. 创建同步账户并指定服务器地址
```
[root@localhost ~]mysql -uroot -p
mysql>use mysql
mysql>grant replication slave on *.* to 'root'@'192.168.1.102' identified by '123456';
mysql>flush privileges #刷新权限
```
授权用户testuser只能从192.168.1.102这个地址访问主服务器192.168.1.101的数据库，并且只具有数据库备份的权限

2. 修改/etc/my.cnf配置文件
```
vi /etc/my.cnf

[mysqld]下添加以下参数，若文件中已经存在，则不用添加
server-id=1  （可以设置为服务器ip尾缀）
log-bin=mysql-bin.log  #启动MySQL二进制日志系统，
binlog-do-db=ourneeddb  #需要同步的数据库
binlog-ignore-db=mysql  #不同步mysql系统数据库，若还有其它不想同步的，继续添加

[root@localhost ~]/etc/init.d/mysqld restart #重启服务
```
 
3. 查看主服务器master状态(注意File与Position项，从服务器需要这两项参数)
```
mysql> show master status;
+------------------+----------+--------------+------------------+
| File            | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000012 |      120 | ourneeddb| mysql            |
```
4. 导出相关DB备份到从服务器
```
mysqldump -uroot -p --all-databases --lock-all-tables > ~/master_db.sql

-u ：用户名
-p ：示密码
--all-databases ：导出所有数据库
--lock-all-tables ：执行操作时锁住所有表，防止操作时有数据修改
~/master_db.sql :导出的备份数据（sql文件）位置，可自己指定
```

 
#### 二、从服务器配置
1. 修改配置文件
```
vi /etc/my.cnf

[mysqld]下添加以下参数，若文件中已经存在，则不用添加
server-id=2  #设置从服务器id，必须于主服务器不同
log-bin=mysql-bin  #启动MySQ二进制日志系统
replicate-do-db=ourneeddb  #需要同步的数据库名
replicate-ignore-db=mysql  #不同步mysql系统数据库

[root@localhost~ ]/etc/init.d/mysqld restart #重启服务
```
 
2. 导入数据库
  
3. 配置主从同步
```
[root@localhost~ ]mysql -uroot -p
mysql>use mysql 
mysql>stop slave;

连接到matser主服务器
mysql>change master to
      master_host='192.168.1.101',
      master_user='root',
      master_password='123456',
      master_log_file='mysql-bin.000012',        
      master_log_pos=120;  #log_file与log_pos是主服务器master状态下的File与Position

开启同步
mysql>start slave;
mysql>show slave status\G;

注意查看
Slave_IO_Running: Yes  
Slave_SQL_Running: Yes 
这两项必须为Yes 以及Log_File、Log_Pos要于master状态下的File,Position相同
```
如果都是正确的，则说明配置成功！



