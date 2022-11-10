# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


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

### zet跳表结构
跳表实际上是一种增加了前向指针的链表，是一种随机化的数据结构，实质上是可以进行二分查找的有序链表；跳表在原来的有序链表上加上了**多级索引**，通过索引来快速查找；可以支持快速的删除、插入和查找操作。
随机0-64层
第二级索引：   1          12
第一级索引：   1     7     12
原始有序连表： 1  3  7  8  12  15

总结：
普通链表是一个节点一个节点按顺序的排序，而跳表是为每个节点随机出一个层数，每一层指向的节点可能会不同。
并且，越高层的链表节点数越少。这样查找的话，会从高层开始查找，一层一层往下找，最终在第一层找出其精准位置。
这样的查找方式可以跳过很多下层节点数，大大加快查找速度。
而对于插入和删除等，也只需要修改对应节点前后指针而已，这就比平衡树更有优势。

为什么采用跳表，而不使用哈希表或平衡树实现呢？
1. skiplist和各种平衡树（如AVL、红黑树等）的元素是有序排列的，而哈希表不是有序的。因此，在哈希表上只能做单个key的查找，不适宜做范围查找。所谓范围查找，指的是查找那些大小在指定的两个值之间的所有节点。
2. 在做范围查找的时候，平衡树比skiplist操作要复杂。在平衡树上，我们找到指定范围的小值之后，还需要以中序遍历的顺序继续寻找其它不超过大值的节点。如果不对平衡树进行一定的改造，这里的中序遍历并不容易实现。而在skiplist上进行范围查找就非常简单，只需要在找到小值之后，对第1层链表进行若干步的遍历就可以实现。
3. 平衡树的插入和删除操作可能引发子树的调整，逻辑复杂，而skiplist的插入和删除只需要修改相邻节点的指针，操作简单又快速。
4. 从内存占用上来说，skiplist比平衡树更灵活一些。一般来说，平衡树每个节点包含2个指针（分别指向左右子树），而skiplist每个节点包含的指针数目平均为1/(1-p)，具体取决于参数p的大小。如果像Redis里的实现一样，取p=1/4，那么平均每个节点包含1.33个指针，比平衡树更有优势。

### zset跳表与压缩列表
zset 底层的存储结构有两种：ziplist 和 skiplist。其中，skiplist 就是“跳跃表”结构（简称跳表）。
关于一个 zset 何时使用 ziplist，何时使用 skiplist，有如下的判断条件：（zset当数据量大于128个或者字节数大于64转为跳表，否则为压缩列表）
- 有序集合保存的元素数量小于 128 个
- 有序集合保存的所有元素的长度小于 64 字节

- zset-max-ziplist-entries 128:zset采用压缩列表时，元素个数最大值。默认值为128。
- zset-max-ziplist-value 64:zset采用压缩列表时，每个元素的字符串长度最大值。默认值为64。

当满足以下任一条件时，Redis便会将zset的底层实现由压缩列表转为跳跃表。
1. zset中元素个数大于zset_max_ziplist_entries；
2. 插入元素的字符串长度大于zset_max_ziplist_value。
zset在转为跳跃表之后，即使元素被逐渐删除，也不会重新转为压缩列表。

压缩列表ziplist本质上就是一个字节数组，是Redis为了节约内存而设计的一种线性数据结构，可以包含多个元素，每个元素可以是一个字节数组或一个整数。

因为ziplist是紧凑存储，没有冗余空间，意味着新插入元素，就需要扩展内存：
1. 分配新的内存，将原数据拷贝到新内存；
2. 扩展原有内存;

所以ziplist 不适合存储大型字符串，存储的元素也不宜过多。

zset zrange用法：与单值查找不同，范围查找不能直接按照 score 从高到低排序后，通过比较缩小范围，最终定位到一个节点。我们需要的是类似 sql 中 limit 0,100 这类的效果。


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

### Redis到底是多线程还是单线程？

我们通常说的 Redis 单线程指的是「接收客户端请求->解析请求 ->进行数据读写等操作->发送数据给客户端」这个过程是由一个线程（主线程）来完成的，这也是我们常说 Redis 是单线程的原因。

但是，Redis 程序并不是单线程的，Redis 在启动的时候，是会启动后台线程（BIO）的：

Redis 在 2.6 版本，会启动 2 个后台线程，分别处理关闭文件、AOF 刷盘这两个任务；
Redis 在 4.0 版本之后，新增了一个新的后台线程，用来异步释放 Redis 内存，也就是 lazyfree 线程。例如执行 unlink key / flushdb async / flushall async 等命令，会把这些删除操作交给后台线程来执行，好处是不会导致 Redis 主线程卡顿。因此，当我们要删除一个大 key 的时候，不要使用 del 命令删除，因为 del 是在主线程处理的，这样会导致 Redis 主线程卡顿，因此我们应该使用 unlink 命令来异步删除大key。

之所以 Redis 为「关闭文件、AOF 刷盘、释放内存」这些任务创建单独的线程来处理，是因为这些任务的操作都是很耗时的，如果把这些任务都放在主线程来处理，那么 Redis 主线程就很容易发生阻塞，这样就无法处理后续的请求了。

后台线程相当于一个消费者，生产者把耗时任务丢到任务队列中，消费者（BIO）不停轮询这个队列，拿出任务就去执行对应的方法即可。

关闭文件、AOF 刷盘、释放内存这三个任务都有各自的任务队列：
- BIO_CLOSE_FILE，关闭文件任务队列：当队列有任务后，后台线程会调用 close(fd) ，将文件关闭；
- BIO_AOF_FSYNC，AOF刷盘任务队列：当 AOF 日志配置成 everysec 选项后，主线程会把 AOF 写日志操作封装成一个任务，也放到队列中。当发现队列有任务后，后台线程会调用 fsync(fd)，将 AOF 文件刷盘，
- BIO_LAZY_FREE，lazy free 任务队列：当队列有任务后，后台线程会 free(obj) 释放对象 / free(dict) 删除数据库所有对象 / free(skiplist) 释放跳表对象；

### redis大key问题
思考一下为什么redis有大key问题 mysql没有

对延迟的预期不同吧，mysql一次查询几百ms不一定是问题，能确保不阻塞其它查询就行，redis如果一次查询（单线程）耗100ms，所有其它查询都排队受影响，这种影响会是灾难性的

主要是redis一个卡住后面的全停了 mysql不会全卡住

mysql存储text，bolb也会有性能问题


### Redis从客户端登录服务器
本地客户端访问192.168.56.56远程数据库服务 (主机为 192.168.56.56，端口为 6379 ，密码为aabbcc 的 redis 服务上)
[root@localhost src]# ./redis-cli -h  192.168.56.56 -p 6379 -a "aabbcc"

注1：设置redis服务的密码，详见：http://blog.csdn.net/fly43108622/article/details/52972308

注2：在客户端登录远程服务时，redis服务有保护模式，需要关闭，详见：http://blog.csdn.net/fly43108622/article/details/52972433

---

### redis分布式锁的问题
synchronized关键字加锁有很大的局限性，只能保证单个节点的互斥性。如果需要在多个节点中保持互斥性，就需要用redis分布式锁。

但redis分布式锁也有一些问题：
1. 非原子操作
2. 忘了释放锁
3. 释放了别人的锁
4. 大量失败请求
5. 锁重入问题
6. 锁竞争问题
7. 锁超时问题

#### 1 非原子操作
使用redis的分布式锁，我们首先想到的可能是setNx命令。
```text
if (jedis.setnx(lockKey, val) == 1) {
   jedis.expire(lockKey, timeout);
}
```
加锁操作和后面的设置超时时间是分开的，并非原子操作。

假如加锁成功，但是设置超时时间失败了，该lockKey就变成永不失效。假如在高并发场景中，有大量的lockKey加锁成功了，但不会失效，有可能直接导致redis内存空间不足。

#### 2 忘了释放锁
redis中还有set命令，该命令可以指定多个参数。
```
String result = jedis.set(lockKey, requestId, "NX", "PX", expireTime);
if ("OK".equals(result)) {
return true;
}
return false;
```
其中：

- lockKey：锁的标识
- requestId：请求id
- NX：只在键不存在时，才对键进行设置操作。
- PX：设置键的过期时间为 millisecond 毫秒。
- expireTime：过期时间
  set命令是原子操作，加锁和设置超时时间，一个命令就能轻松搞定。

分布式锁更合理的用法是：
- 手动加锁
- 业务操作
- 手动释放锁
- 如果手动释放锁失败了，则达到超时时间，redis会自动释放锁。

如何释放锁呢？
需要捕获业务代码的异常，然后在finally中释放锁。换句话说就是：无论代码执行成功或失败了，都需要释放锁。

#### 3 释放了别人的锁
假如线程A和线程B，都使用lockKey加锁。
线程A加锁成功了，但是由于业务功能耗时时间很长，超过了设置的超时时间。
这时候，redis会自动释放lockKey锁。此时，线程B就能给lockKey加锁成功了，接下来执行它的业务操作。
恰好这个时候，线程A执行完了业务功能，接下来，在finally方法中释放了锁lockKey。这不就出问题了，线程B的锁，被线程A释放了。

如何解决这个问题呢？
不知道你们注意到没？在使用set命令加锁时，除了使用lockKey锁标识，还多设置了一个参数：requestId，为什么要需要记录requestId呢？
答：requestId是在释放锁的时候用的。
```
if (jedis.get(lockKey).equals(requestId)) {
    jedis.del(lockKey);
    return true;
}
return false;
```

在释放锁的时候，先获取到该锁的值（之前设置值就是requestId），然后判断跟之前设置的值是否相同，如果相同才允许删除锁，返回成功。如果不同，则直接返回失败。

这里为什么要用requestId，用userId不行吗？
答：如果用userId的话，对于请求来说并不唯一，多个不同的请求，可能使用同一个userId。而requestId是全局唯一的，不存在加锁和释放锁乱掉的情况。

#### 4 大量失败请求

在秒杀场景下，会有什么问题？

答：每1万个请求，有1个成功。再1万个请求，有1个成功。如此下去，直到库存不足。这就变成均匀分布的秒杀了，跟我们想象中的不一样。

如何解决这个问题呢？

此外，还有一种场景：

比如，有两个线程同时上传文件到sftp，上传文件前先要创建目录。假设两个线程需要创建的目录名都是当天的日期，比如：20210920，如果不做任何控制，直接并发的创建目录，第二个线程必然会失败。

答：使用自旋锁。
```
try {
  Long start = System.currentTimeMillis();
  while(true) {
     String result = jedis.set(lockKey, requestId, "NX", "PX", expireTime);
     if ("OK".equals(result)) {
        if(!exists(path)) {
           mkdir(path);
        }
        return true;
     }
     
     long time = System.currentTimeMillis() - start;
      if (time>=timeout) {
          return false;
      }
      try {
          Thread.sleep(50);
      } catch (InterruptedException e) {
          e.printStackTrace();
      }
  }
} finally{
    unlock(lockKey,requestId);
}  
return false;
```
在规定的时间，比如500毫秒内，自旋不断尝试加锁（说白了，就是在死循环中，不断尝试加锁），如果成功则直接返回。如果失败，则休眠50毫秒，再发起新一轮的尝试。如果到了超时时间，还未加锁成功，则直接返回失败。

#### 5 锁重入问题

我们都知道redis分布式锁是互斥的。假如我们对某个key加锁了，如果该key对应的锁还没失效，再用相同key去加锁，大概率会失败。

没错，大部分场景是没问题的。

为什么说是大部分场景呢？

因为还有这样的场景：

假设在某个请求中，需要获取一颗满足条件的菜单树或者分类树。我们以菜单为例，这就需要在接口中从根节点开始，递归遍历出所有满足条件的子节点，然后组装成一颗菜单树。

需要注意的是菜单不是一成不变的，在后台系统中运营同学可以动态添加、修改和删除菜单。为了保证在并发的情况下，每次都可能获取最新的数据，这里可以加redis分布式锁。

加redis分布式锁的思路是对的。但接下来问题来了，在递归方法中递归遍历多次，每次都是加的同一把锁。递归第一层当然是可以加锁成功的，但递归第二层、第三层...第N层，不就会加锁失败了？
```text
private int expireTime = 1000;

public void fun(int level,String lockKey,String requestId){
  try{
     String result = jedis.set(lockKey, requestId, "NX", "PX", expireTime);
     if ("OK".equals(result)) {
        if(level<=10){
           this.fun(++level,lockKey,requestId);
        } else {
           return;
        }
     }
     return;
  } finally {
     unlock(lockKey,requestId);
  }
}
```
如果你直接这么用，看起来好像没有问题。但最终执行程序之后发现，等待你的结果只有一个：出现异常。

因为从根节点开始，第一层递归加锁成功，还没释放锁，就直接进入第二层递归。因为锁名为lockKey，并且值为requestId的锁已经存在，所以第二层递归大概率会加锁失败，然后返回到第一层。第一层接下来正常释放锁，然后整个递归方法直接返回了。

这下子，大家知道出现什么问题了吧？

没错，递归方法其实只执行了第一层递归就返回了，其他层递归由于加锁失败，根本没法执行。

那么这个问题该如何解决呢？

答：使用可重入锁。

我们以redisson框架为例，它的内部实现了可重入锁的功能。

伪代码如下：
```
private int expireTime = 1000;

public void run(String lockKey) {
  RLock lock = redisson.getLock(lockKey);
  this.fun(lock,1);
}

public void fun(RLock lock,int level){
  try{
      lock.lock(5, TimeUnit.SECONDS);
      if(level<=10){
         this.fun(lock,++level);
      } else {
         return;
      }
  } finally {
     lock.unlock();
  }
}
```

加锁步骤：
1. 先判断如果锁名不存在，则加锁。
2. 接下来，判断如果锁名和requestId值都存在，则使用hincrby命令给该锁名和requestId值计数，每次都加1。注意一下，这里就是重入锁的关键，锁重入一次值就加1。
3. 如果锁名存在，但值不是requestId，则返回过期时间。

说明：Redis Hincrby 命令：用于为哈希表中的字段值加上指定增量值。
增量也可以为负数，相当于对指定字段进行减法操作。
如果哈希表的 key 不存在，一个新的哈希表被创建并执行 HINCRBY 命令。
如果指定的字段不存在，那么在执行命令前，字段的值被初始化为 0 。
对一个储存字符串值的字段执行 HINCRBY 命令将造成一个错误。
本操作的值被限制在 64 位(bit)有符号数字表示之内。

redis Hincrby 命令基本语法如下：
redis 127.0.0.1:6379> HINCRBY KEY_NAME FIELD_NAME INCR_BY_NUMBER
redis> HSET myhash field 5
(integer) 1
redis> HINCRBY myhash field 1
(integer) 6
redis> HINCRBY myhash field -1
(integer) 5
redis> HINCRBY myhash field -10
(integer) -5

释放锁步骤：
1. 先判断如果锁名和requestId值不存在，则直接返回。
2. 如果锁名和requestId值存在，则重入锁减1。
3. 如果减1后，重入锁的value值还大于0，说明还有引用，则重试设置过期时间。
4. 如果减1后，重入锁的value值还等于0，则可以删除锁，然后发消息通知等待线程抢锁。

#### 6 锁竞争问题
如果有大量需要写入数据的业务场景，使用普通的redis分布式锁是没有问题的。

但如果有些业务场景，写入的操作比较少，反而有大量读取的操作。这样直接使用普通的redis分布式锁，会不会有点浪费性能？

我们都知道，锁的粒度越粗，多个线程抢锁时竞争就越激烈，造成多个线程锁等待的时间也就越长，性能也就越差。

所以，提升redis分布式锁性能的第一步，就是要把锁的粒度变细。

##### 6.1 读写锁
众所周知，加锁的目的是为了保证，在并发环境中读写数据的安全性，即不会出现数据错误或者不一致的情况。

但在绝大多数实际业务场景中，一般是读数据的频率远远大于写数据。而线程间的并发读操作是并不涉及并发安全问题，我们没有必要给读操作加互斥锁，只要保证读写、写写并发操作上锁是互斥的就行，这样可以提升系统的性能。

读锁的伪代码如下：
```
RReadWriteLock readWriteLock = redisson.getReadWriteLock("readWriteLock");
RLock rLock = readWriteLock.readLock();
try {
rLock.lock();
//业务操作
} catch (Exception e) {
log.error(e);
} finally {
rLock.unlock();
}
```

写锁的伪代码如下：
```
RReadWriteLock readWriteLock = redisson.getReadWriteLock("readWriteLock");
RLock rLock = readWriteLock.writeLock();
try {
    rLock.lock();
    //业务操作
} catch (InterruptedException e) {
   log.error(e);
} finally {
    rLock.unlock();
}
```
将读锁和写锁分开，最大的好处是提升读操作的性能，因为读和读之间是共享的，不存在互斥性。而我们的实际业务场景中，绝大多数数据操作都是读操作。所以，如果提升了读操作的性能，也就会提升整个锁的性能。

下面总结一个读写锁的特点：

- 读与读是共享的，不互斥
- 读与写互斥
- 写与写互斥

##### 6.2 锁分段
此外，为了减小锁的粒度，比较常见的做法是加大锁：分段。

在java中ConcurrentHashMap，就是将数据分为16段，每一段都有单独的锁，并且处于不同锁段的数据互不干扰，以此来提升锁的性能。

放在实际业务场景中，我们可以这样做：

比如在秒杀扣库存的场景中，现在的库存中有2000个商品，用户可以秒杀。为了防止出现超卖的情况，通常情况下，可以对库存加锁。如果有1W的用户竞争同一把锁，显然系统吞吐量会非常低。

为了提升系统性能，我们可以将库存分段，比如：分为100段，这样每段就有20个商品可以参与秒杀。

在秒杀的过程中，先把用户id获取hash值，然后除以100取模。模为1的用户访问第1段库存，模为2的用户访问第2段库存，模为3的用户访问第3段库存，后面以此类推，到最后模为100的用户访问第100段库存。

如此一来，在多线程环境中，可以大大的减少锁的冲突。以前多个线程只能同时竞争1把锁，尤其在秒杀的场景中，竞争太激烈了，简直可以用惨绝人寰来形容，其后果是导致绝大数线程在锁等待。现在多个线程同时竞争100把锁，等待的线程变少了，从而系统吞吐量也就提升了。

#### 7 锁超时问题（锁自动续期）

如果线程A加锁成功了，但是由于业务功能耗时时间很长，超过了设置的超时时间，这时候redis会自动释放线程A加的锁。

有些朋友可能会说：到了超时时间，锁被释放了就释放了呗，对功能又没啥影响。

答：错，错，错。对功能其实有影响。

通常我们加锁的目的是：为了防止访问临界资源时，出现数据异常的情况。比如：线程A在修改数据C的值，线程B也在修改数据C的值，如果不做控制，在并发情况下，数据C的值会出问题。

假设线程A加redis分布式锁的代码，包含代码1和代码2两段代码。

由于该线程要执行的业务操作非常耗时，程序在执行完代码1的时，已经到了设置的超时时间，redis自动释放了锁。而代码2还没来得及执行。

此时，代码2相当于裸奔的状态，无法保证互斥性。假如它里面访问了临界资源，并且其他线程也访问了该资源，可能就会出现数据异常的情况。（PS：我说的访问临界资源，不单单指读取，还包含写入）

那么，如何解决这个问题呢？

答：如果达到了超时时间，但业务代码还没执行完，需要给锁自动续期。

我们可以使用TimerTask类，来实现自动续期的功能：
```
Timer timer = new Timer(); 
timer.schedule(new TimerTask() {
    @Override
    public void run(Timeout timeout) throws Exception {
      //自动续期逻辑
    }
}, 10000, TimeUnit.MILLISECONDS);
```

获取锁之后，自动开启一个定时任务，每隔10秒钟，自动刷新一次过期时间。这种机制在redisson框架中，有个比较霸气的名字：watch dog，即传说中的看门狗。

需要注意的地方是：在实现自动续期功能时，还需要设置一个总的过期时间，可以跟redisson保持一致，设置成30秒。如果业务代码到了这个总的过期时间，还没有执行完，就不再自动续期了。

