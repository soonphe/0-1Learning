# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## MySQL5.7版本sql_mode=only_full_group_by问题解决办法

在服务器数据库查询使用了 GROUP BY 居然报出了
```
1055 - Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'csc_risk.a.DefaultDate' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by, Time: 0.035000s 
```
于是把部分数据迁移到本地进行测试, 发现sql语句可以执行并查看数据库版本

原因分析：MySQL5.7版本默认设置了 mysql sql_mode = only_full_group_by 属性，导致报错。

> 下载安装的是最新版的mysql5.7.x版本，默认是开启了 only_full_group_by 模式的，但开启这个模式后，原先的 group by 语句就报错。
>
> 一旦开启 only_full_group_by ，只能获取受到其影响的字段信息，无法和其他未受其影响的字段共存，这样，group by 的功能将变得十分狭窄了


为了验证是不是数据版本问题, 于是查询版本
```
SELECT VERSION()
```
查询数据版本发现确实是5.7版本

> 5.7.24

知道原因就好办, 查询怎么解决.  


### 1、查看sql_mode
SELECT @@sql_mode;
查询出来的值为：

ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

### 2、去掉ONLY_FULL_GROUP_BY，重新设置值。
SET @@global.sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';

### 3、上面是改变了全局sql_mode，对于新建的数据库有效。对于已存在的数据库，则需要在对应的数据下执行：
SET sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
 
 
> *BUT,以上方式只是临时解决，数据库重启会失效*

### 解决重启数据库失效问题

需修改mysql配置文件，通过手动添加sql_mode的方式强制指定不需要ONLY_FULL_GROUP_BY属性，

my.cnf位于etc文件夹下，添加如下：
```
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```
重启mysql服务，顺利解决。 

mac没有my.cnf需要自己创建，权限使用sudo，my.cnf参考如下

### my.cnf
```
# Example MySQL config file for medium systems.  
  #  
  # This is for a system with little memory (32M - 64M) where MySQL plays  
  # an important part, or systems up to 128M where MySQL is used together with  
  # other programs (such as a web server)  
  #  
  # MySQL programs look for option files in a set of  
  # locations which depend on the deployment platform.  
  # You can copy this option file to one of those  
  # locations. For information about these locations, see:  
  # http://dev.mysql.com/doc/mysql/en/option-files.html  
  #  
  # In this file, you can use all long options that a program supports.  
  # If you want to know which options a program supports, run the program  
  # with the "--help" option.  
  # The following options will be passed to all MySQL clients  
  [client]
  default-character-set=utf8
  #password   = your_password  
  port        = 3306  
  socket      = /tmp/mysql.sock   
  # Here follows entries for some specific programs  
  # The MySQL server  
  [mysqld]
  character-set-server=utf8
  init_connect='SET NAMES utf8
  port        = 3306  
  socket      = /tmp/mysql.sock  
  skip-external-locking  
  key_buffer_size = 16M  
  max_allowed_packet = 1M  
  table_open_cache = 64  
  sort_buffer_size = 512K  
  net_buffer_length = 8K  
  read_buffer_size = 256K  
  read_rnd_buffer_size = 512K  
  myisam_sort_buffer_size = 8M  
  character-set-server=utf8  
  init_connect='SET NAMES utf8' 
  sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
# Don't listen on a TCP/IP port at all. This can be a security enhancement,  
# if all processes that need to connect to mysqld run on the same host.  
# All interaction with mysqld must be made via Unix sockets or named pipes.  
# Note that using this option without enabling named pipes on Windows  
# (via the "enable-named-pipe" option) will render mysqld useless!  
#   
#skip-networking  
  
  # Replication Master Server (default)  
  # binary logging is required for replication  
  log-bin=mysql-bin  
    
    # binary logging format - mixed recommended  
    binlog_format=mixed  
      
      # required unique id between 1 and 2^32 - 1  
      # defaults to 1 if master-host is not set  
      # but will not function as a master if omitted  
      server-id   = 1  
        
    # Replication Slave (comment out master section to use this)  
    #  
    # To configure this host as a replication slave, you can choose between  
    # two methods :  
    #  
    # 1) Use the CHANGE MASTER TO command (fully described in our manual) -  
    #    the syntax is:  
    #  
    #    CHANGE MASTER TO MASTER_HOST=<host>, MASTER_PORT=<port>,  
    #    MASTER_USER=<user>, MASTER_PASSWORD=<password> ;  
    #  
    #    where you replace <host>, <user>, <password> by quoted strings and  
    #    <port> by the master's port number (3306 by default).  
    #  
    #    Example:  
    #  
    #    CHANGE MASTER TO MASTER_HOST='125.564.12.1', MASTER_PORT=3306,  
    #    MASTER_USER='joe', MASTER_PASSWORD='secret';  
    #  
    # OR  
    #  
    # 2) Set the variables below. However, in case you choose this method, then  
    #    start replication for the first time (even unsuccessfully, for example  
    #    if you mistyped the password in master-password and the slave fails to  
    #    connect), the slave will create a master.info file, and any later  
    #    change in this file to the variables' values below will be ignored and  
    #    overridden by the content of the master.info file, unless you shutdown  
    #    the slave server, delete master.info and restart the slaver server.  
    #    For that reason, you may want to leave the lines below untouched  
    #    (commented) and instead use CHANGE MASTER TO (see above)  
    #  
    # required unique id between 2 and 2^32 - 1  
    # (and different from the master)  
    # defaults to 2 if master-host is set  
    # but will not function as a slave if omitted  
    #server-id       = 2  
    #  
    # The replication master for this slave - required  
    #master-host     =   <hostname>  
    #  
    # The username the slave will use for authentication when connecting  
    # to the master - required  
    #master-user     =   <username>  
    #  
    # The password the slave will authenticate with when connecting to  
    # the master - required  
    #master-password =   <password>  
    #  
    # The port the master is listening on.  
    # optional - defaults to 3306  
    #master-port     =  <port>  
    #  
    # binary logging - not required for slaves, but recommended  
    #log-bin=mysql-bin  
      
      # Uncomment the following if you are using InnoDB tables  
      #innodb_data_home_dir = /usr/local/mysql/data  
      #innodb_data_file_path = ibdata1:10M:autoextend  
      #innodb_log_group_home_dir = /usr/local/mysql/data  
      # You can set .._buffer_pool_size up to 50 - 80 %  
      # of RAM but beware of setting memory usage too high  
      #innodb_buffer_pool_size = 16M  
      #innodb_additional_mem_pool_size = 2M  
      # Set .._log_file_size to 25 % of buffer pool size  
      #innodb_log_file_size = 5M  
      #innodb_log_buffer_size = 8M  
      #innodb_flush_log_at_trx_commit = 1  
      #innodb_lock_wait_timeout = 50  
        
        [mysqldump]  
        quick  
        max_allowed_packet = 16M  
          
          [mysql]  
          no-auto-rehash  
          # Remove the next comment character if you are not familiar with SQL  
          #safe-updates  
          default-character-set=utf8   
            
        [myisamchk]  
        key_buffer_size = 20M  
        sort_buffer_size = 20M  
        read_buffer = 2M  
        write_buffer = 2M  
          
          [mysqlhotcopy]  
          interactive-timeout
```

