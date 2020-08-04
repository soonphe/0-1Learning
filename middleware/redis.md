# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Redis

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
单机，
主从，
哨兵sentinel：监控检查主和从服务器是否正常，故障提醒，自动故障迁移
集群proxy型：
集群直连型：

### Redis分布式锁和异步队列
* Redis分布式锁：拿setnx(如果key存在返回0)争抢锁，抢到后加一个expire过期时间防止忘记释放
* Redis做异步队列：用list做队列，rpush生产消息，lpop消费消息、使用pub/sub主题订阅者模式，实现1：N的消息队列

### Redis避免缓存穿透：
1.对查询结果为空的情况也进行缓存，过期时间设置短一点 
2.过滤一定不存在的key查询

### Redis做消息队列
调用convertAndSend方法就可以产生队列
例：        redisTemplate.convertAndSend(messageQueueDto.getTopic(),messageQueueDto.getMessage());



### redis实现LRU，基于 HashMap 和 双向链表实现 LRU 
可以使用 HashMap 存储 key，这样可以做到 save 和 get key的时间都是 O(1)，而 HashMap 的 Value 指向双向链表实现的 LRU 的 Node 节点，

### Jedis
Jedis是Redis官方推荐的面向Java的操作Redis的客户端，而RedisTemplate是SpringDataRedis中对JedisApi的高度封装。
SpringDataRedis相对于Jedis来说可以方便地更换Redis的Java客户端，比Jedis多了自动管理连接池的特性，方便与其他Spring框架进行搭配使用如：SpringCache
