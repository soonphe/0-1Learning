# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux安装mysql

### yum安装
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
service mysqld stop       --关闭mysql·
lsof -i:3306              --数据库端口是否开启
systemctl status mysqld               //查看mysqld服务
systemctl status mysqld.serbvice               //查看mysqld服务
systemctl enable mysqld               //设置开机启动
systemctl daemon-reload               //重载服务

service mysqld start    #启动mysql
service mysqld stop     #关闭mysql
service mysqld status   #查看运行状态

grep "password" /var/log/mysqld.log     //找到初始密码 
chkconfig --add mysqld      //设置开机启动mysql服务
mysqladmin -u root password 密码  //创建root管理员
mysql -u root -p    //进入mysql容器中
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password'; //修改root账户密码
use mysql;
UPDATE user SET `Host` = '%' WHERE `User` = 'root' LIMIT 1; //设置允许远程访问,mysql增加权限：mysql库中的user表新增一条记录host为“%”，user为“root”。

```


### 手动安装
```
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
```
