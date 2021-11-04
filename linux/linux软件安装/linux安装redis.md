# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux安装redis 


1.2、redis下载
[root@mysqldb1 ~]# wget http://download.redis.io/releases/redis-3.0.5.tar.gz
 
1.3、解压
[root@mysqldb1 ~]# tar xf redis-3.0.5.tar.gz
 
这样就在当前目录下新建了一个包含发行版源代码的目录，必须cd进入这个目录以继续服务器的编译。
 
1.4、编译及安装
 
进入redis解压目录，执行如下命令编译Redis：
[root@mysqldb1 ~]# cd redis-3.0.5 
[root@mysqldb1 redis-3.0.5]# make && make install
 
也可以指定目录安装：
make prefix=/path/to/installdir install
安装tcmalloc包需指定参数，如make USE_TCMALLOC=yes FORCE_LIBC_MALLOC=yes
因为对一个基本的配置的编译，一般需要1分钟左右的时间，实际需要的时间因你的硬件和选择的模块数量会有很大不同。
 
1.5、配置
 
接着，复制redis.conf到/etc/下，修改配置文件，来配置Redis服务器。
1 [root@mysqldb1 redis-3.0.5]# cp redis.conf /etc/
 
1.6、参数参看
1234567891011121314 [root@mysqldb1 redis-3.0.5]# redis-server --help 
Usage: ./redis-server [/path/to/redis.conf] [options] 
  ./redis-server - (read config from stdin) 
  ./redis-server -v or --version 
  ./redis-server -h or --help 
  ./redis-server --test-memory <megabytes> 
Examples: 
  ./redis-server (run the server with default conf) 
  ./redis-server /etc/redis/6379.conf 
  ./redis-server --port 7777 
  ./redis-server --port 7777 --slaveof 127.0.0.1 8888 
  ./redis-server /etc/myredis.conf --loglevel verbose 
Sentinel mode: 
  ./redis-server /etc/sentinel.conf --sentinel
 
查询redis的进程：# ps -ef|grep redis
 
还有要注意的一点是记得安装gcc和gcc-c++，还要注意gcc的版本，用gcc -v来查看当前安装的gcc版本，版本过低（一般需要4.0以上）的话编译redis3.0以上的是会出错的。
 yum install gcc
 yum install gcc-c++
 
本地客户端访问本地redis数据库服务：./redis-cli
不是本地：./redis-cli -h  192.168.56.56 -p 6379 -a "aabbcc"
 
redis安装文件夹：usr/local/nginx/sbin/redis-stable
redis启动文件夹：usr/local/bin
redis.conf文件位置：/etc/redis.conf
启动命令：./redis-cli
 
启动服务
Redis-server redis.conf
./redis-server /etc/redis.conf
关闭服务
redis-cli 进入命令行 再shutdown
客户端启动
./redis-cli
远程启动
./redis-cli -h 192.168.107.128 -p 6379 -a "123456"
 
 
如果是用apt-get或者yum install安装的redis，可以直接通过下面的命令停止/启动/重启redis
 
/etc/init.d/redis-server stop
/etc/init.d/redis-server start
/etc/init.d/redis-server restart
如果是通过源码安装的redis，则可以通过redis的客户端程序redis-cli的shutdown命令来重启redis
 
redis-cli -h 127.0.0.1 -p 6379 shutdown
如果上述方式都没有成功停止redis，则可以使用终极武器 kill -9
 
 
原来是redis默认只能localhost登录，所以需要开启远程登录。解决方法如下：
 
protected-mode no：是客户端可以连接redis（3.2版本以后新增）
 
　　在redis的配置文件redis.conf中，找到bind localhost注释掉。
 
　　　　注释掉本机,局域网内的所有计算机都能访问。
 
　　　　band localhost   只能本机访问,局域网内计算机不能访问。
 
　　　　bind  局域网IP    只能局域网内IP的机器访问, 本地localhost都无法访问。
 
想让redis打印日志：
  在redis.conf中修改
loglevel notice ——debug
logfile "" ——文件路径，默认为redis所在盘符
 
 
获取所有Key命令：redis-cli keys ‘*’  ；
 
获取指定前缀的key：redis-cli KEYS “edu:*”
 
如果需要导出，可以redis-cli keys ‘*’ > /data/redis_key.txt
 
删除指定前缀的Key    redis-cli KEYS “edu:*” | xargs redis-cli DEL
------------------------------------------------------------------------------------
 
#Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
daemonize no
 
设置成yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程
设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出
 
--------------------------------------------------------------------------------
redis配置密码：
1.在配置文件修改
# requirepass foobared
2.使用命令行配置（重启后又使用配置文件中的密码）
redis 127.0.0.1:6379[1]> config set requirepass my_redis  
OK  
redis 127.0.0.1:6379[1]> config get requirepass  
1) "requirepass"  
2) "my_redis" 
 
 
redis主从复制：/etc/redis.conf
1.master禁用数据持久化，只需要注释掉master 配置文件中的所有save配置，然后只在slave上配置数据持久化。
在Master Redis的/etc/redis/redis_6379.conf文件中注释掉所有save。 
设置appendonly为yes。 
requirepass密码可选。
2.在slave服务器配置主服务器地址和端口：slaveof 47.94.108.123 6379
在slave的配置文件中增加类似下面这行的内容:slaveof localhost 6379
localhost 表示本机，6379表示master redis实例。
如果master redis配置了密码，还要添加如下内容：
masterauth 123456
