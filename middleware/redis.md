# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Redis
Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API

### Redis安装
brew安装：
brew search redis   //搜索redis软件
brew install redis  //安装redis
brew services start redis   //启动redis
brew services stop redis   //停止redis

docker安装：
docker search redis   //搜索redis软件
docker pull redis  //安装redis
docker run -p 6379:6379 --name redis -d redis   //启动redis容器
docker ps -a        //列出所有容器
docker stop redis   //停止redis容器

二进制安装：
官网地址：https://redis.io/download
wget方式下载：wget http://download.redis.io/releases/redis-5.0.7.tar.gz
解压：tar -zvxf redis-5.0.7.tar.gz
编译：make
安装：make install
bin目录下启动：./redis-server
指定配置文件启动：./redis-server & ./redis.conf


### Redis支持的数据类型：String，Hash，List，Set（String无序集合），zset（有序集合，实现方式是跳表+字典）
常用操作：
Set String
hmset Hash
lpush name
sadd set

### Redis为什么快
1. 纯内存操作
2. io多路复用
3. 单线程避免加锁

### Redis持久化
* 持久化原理：使用fork子线程，存入buffer，通过策略刷入硬盘
* 持久化策略：RDB(默认),AOF

~~~~
* RDB(默认) :Redis DataBase 指定时间内生成数据集的时间点快照（默认）
    父进程在保存RDB文件时fork一个子进程
    命令：save和bgsave（调用fork子进程）
    默认情况，持久化到dump.rdb文件，在redis重启后，自动读取其中文件

* AOF:Append Only File 记录服务器执行的所有写命令

    appendonly no 是否开启AOP
    appendfsync everysec/always/no 默认每秒写入
    AOF在文件体积过大会自动进行重写rewire，
    redis-check-aof会修复不完整的命令
~~~~
* 操作方式：
    １. 使用shell脚本+redis命令
    ２. 使用dump.rdb或appendonly.aof文件
* 注意区分：冷迁移和热迁移：


### Redis淘汰机制：maxmemory 为0时表示对redis使用内存没有限制
Redis提供了下面几种淘汰策略供用户选择，其中默认的策略为noeviction策略：
·         noeviction：当内存使用达到阈值的时候，所有引起申请内存的命令会报错。
·         allkeys-lru：在主键空间中，优先移除最近未使用的key。
·         volatile-lru：在设置了过期时间的键空间中，优先移除最近未使用的key。
·         allkeys-random：在主键空间中，随机移除某个key。
·         volatile-random：在设置了过期时间的键空间中，随机移除某个key。
·         volatile-ttl：在设置了过期时间的键空间中，具有更早过期时间的key优先移除。


### redis事务：
使用MULTI开启事务
由EXEC触发事务
DISCARD取消事务
注：没有回滚

### redis常见架构模式：
* 单机：单节点
* 主从：默认配置下，master节点可以进行读和写，slave节点只能进行读操作，写操作被禁止，
    slave节点挂了不影响其他slave节点的读和master节点的读和写，重新启动后会将数据从master节点同步过来
    master节点挂了以后，redis就不能对外提供写服务了，因为剩下的slave不能成为master
* 哨兵sentinel：监控检查主和从服务器是否正常，故障提醒，自动故障迁移
    缺点：主从切换需要时间，会丢失数据；还是没有解决主节点写的压力；
* 集群proxy型：
    缺点：增加了新的 proxy，需要维护其高可用。
* 集群直连型：Redis-Cluster采用无中心结构，每个节点保存数据和整个集群状态,每个节点都和其他所有节点连接。
    缺点：1、资源隔离性较差，容易出现相互影响的情况。   2、数据通过异步复制,不保证数据的强一致性

### Redis分布式锁和异步队列
* Redis分布式锁：拿setnx(如果key存在返回0)争抢锁，抢到后加一个expire过期时间防止忘记释放
* Redis做异步队列：用list做队列，rpush生产消息，lpop消费消息、使用pub/sub主题订阅者模式，实现1：N的消息队列

### Redis避免缓存穿透：
1.对查询结果为空的情况也进行缓存，过期时间设置短一点 
2.过滤一定不存在的key查询

### Redis做消息队列
调用convertAndSend方法就可以产生队列
例：redisTemplate.convertAndSend(messageQueueDto.getTopic(),messageQueueDto.getMessage());



### redis实现LRU，基于 HashMap 和 双向链表实现 LRU 
可以使用 HashMap 存储 key，这样可以做到 save 和 get key的时间都是 O(1)，而 HashMap 的 Value 指向双向链表实现的 LRU 的 Node 节点，

### Jedis
Jedis是Redis官方推荐的面向Java的操作Redis的客户端，而RedisTemplate是SpringDataRedis中对JedisApi的高度封装。
SpringDataRedis相对于Jedis来说可以方便地更换Redis的Java客户端，比Jedis多了自动管理连接池的特性，方便与其他Spring框架进行搭配使用如：SpringCache

### redis实现消息队列
redis自带pub和sub机制：即发布者和订阅者
Redis的PUSH/POP机制：利用的Redis的列表(lists)数据结构。
比较好的使用模式是，生产者lpush消息，消费者brpop消息，并设定超时时间，可以减少redis的压力

架构：异步写入
先抽象出对象访问层，对外屏蔽数据来源（redis，mysql，others）
写的请求直接写入缓存，然后前端的请求直接走缓存，这样存入的数据就有意义了，
然后是数据落地，简单的读取RDB文件（实时性不高），复杂一点的可以实现Redis的主从同步协议（实时性高）

命令行写入 LPUSH list1 a
取值： RPOP list1 0


### redis中set、setnx、setex区别
1、SET key value：直接覆写旧值，无视类型。

2、SETEX key seconds value：覆写旧值并将 key 的生存时间设为 seconds (以秒为单位)

3、SETNX key value：将 key 的值设为 value ，当且仅当 key 不存在。若给定的 key 已经存在，则 SETNX 不做任何动作。设置成功，返回 1 。设置失败，返回 0 。

4、GETSET key value：将给定 key 的值设为 value ，并返回 key 的旧值(old value)，key 不存在时，返回 null


### 常见问题
1.Redis从客户端登录服务器
本地客户端访问192.168.56.56远程数据库服务 (主机为 192.168.56.56，端口为 6379 ，密码为aabbcc 的 redis 服务上)
[root@localhost src]# ./redis-cli -h  192.168.56.56 -p 6379 -a "aabbcc"


注1：设置redis服务的密码，详见：http://blog.csdn.net/fly43108622/article/details/52972308

注2：在客户端登录远程服务时，redis服务有保护模式，需要关闭，详见：http://blog.csdn.net/fly43108622/article/details/52972433