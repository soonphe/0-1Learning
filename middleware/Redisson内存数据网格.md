# 0-1Learning

![alt ](../static/common/svg/luoxiaosheng.svg "公众号")
![alt ](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt ](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Redission
Redisson是架设在Redis基础上的一个Java驻内存数据网格（In-Memory Data Grid）。【Redis官方推荐】

### springboot引入redission依赖：
```
<dependency>
   <groupId>org.redisson</groupId>
   <artifactId>redisson</artifactId>
   <version>3.16.1</version>
</dependency>  
```
### config及配置文件
```
redisson:
	nodes:
		-192.168.161.68:7001
		-192.168.161.68:7002
		-192.168.161.68:7003
		-192.168.161.68:7004
		-192.168.161.68:7005
		-192.168.161.68:7006
	password:evcsr2020%1dSP
	mode:cluster
```
```
  @Bean(destroyMethod = "shutdown")
  public RedissonClient redissonClient(RedissonProperties redisProperties) throws IOException {
    Config config = new Config();
    List<String> nodes = redisProperties.getNodes();
    if (Objects.equals(MODE_SINGLE, redisProperties.getMode())) {
      config.useSingleServer().setAddress(nodes.get(0))
          .setPassword(redisProperties.getPassword());
    } else {
      config.useClusterServers().addNodeAddress(nodes.stream().map(s -> "redis://" + s).collect(
          Collectors.toList()).toArray(new String[nodes.size()]))
          .setPassword(redisProperties.getPassword());
    }
    return Redisson.create(config);
  }
```

### 3.快速使用
```
// 1. 创建配置对象
Config config = new Config();
config.useClusterServers()
       // use "rediss://" for SSL connection
      .addNodeAddress("redis://127.0.0.1:7181");
// 或从文件中读取配置
config = Config.fromYAML(new File("config-file.yaml")); 

// 2. 创建RedissonClient 对象
RedissonClient redisson = Redisson.create(config);

// 3. 获取基于Redis的java.util.concurrent.ConcurrentMap的实现
//存值
	//这一步可以理解是给map取了个名字"cacheInfo" 我们到其他地方可以用这个名字调出这个map
    RMap<String, String> map = redissonClient.getMap("cacheInfo");
	//将数据存入缓存
	map.put("cacheInfo", cacheInfo);
	//设置缓存的时间
	map.expire(1, TimeUnit.DAYS);
	return map.get("cacheInfo");
        
//取值
    //这里就是通过上面方法给map取的名字"cacheInfo"调到对应的map
	RMap<String, String> map = redissonClient.getMap("cacheInfo");
	if (!StringUtil.isNotEmpty(map.get("cacheInfo))) {
		throw new ParameterValidationException("获取缓存失败，请重新登陆！！");
	}
	return map.get(user.getId());

// 4. 获取基于Redis的java.util.concurrent.locks.Lock实现
RLock lock = redisson.getLock("myLock");
//加锁
lock.lock(RedisKeyConstants.getAuthKeyTimeOut(),TimeUnit.SECONDS);
	
// 5. 获取基于Redis的java.util.concurrent.ExecutorService的实现
RExecutorService executorService = redisson.getExecutorService("myExecutorService");
//执行CallableTask
Future<Long> future = executorService.submit(new CallableTask());
//执行RunnableTask
Future<Long> future = executorService.submit(new RunnableTask(123));
Long result = future.get();

public class CallableTask implements Callable<Long> {
public class RunnableTask implements Runnable {
	//@RInject注释来为任务实时注入Redisson实例依赖。
	@RInject
    private RedissonClient redissonClient;

```

### redisson多种连接模式
1. 集群模式
```
    Config config = new Config();
	config.useClusterServers()
    .setScanInterval(2000) // cluster state scan interval in milliseconds
    .addNodeAddress("redis://127.0.0.1:7000", "redis://127.0.0.1:7001")
    .addNodeAddress("redis://127.0.0.1:7002");
    RedissonClient redisson = Redisson.create(config);
```
2. 单例模式
```
    RedissonClient redisson = Redisson.create();
    Config config = new Config();
	config.useSingleServer().setAddress("myRedisServer:6379");
    RedissonClient redisson = Redisson.create(config);
```
3. 哨兵模式
```
    Config config = new Config();
	config.useSentinelServers()
    .setMasterName("mymaster")
    .addSentinelAddress("redis://127.0.0.1:26389", "redis://127.0.0.1:26379")
    .addSentinelAddress("redis://127.0.0.1:26319");
    RedissonClient redisson = Redisson.create(config);
```
4. 主从模式
```
    Config config = new Config();
	config.useMasterSlaveServers()
	.setMasterAddress("redis://127.0.0.1:6379")
    .addSlaveAddress("redis://127.0.0.1:6389", "redis://127.0.0.1:6332", "redis://127.0.0.1:6419")
    .addSlaveAddress("redis://127.0.0.1:6399");
    RedissonClient redisson = Redisson.create(config);
```

### 分布式相关工具
#### 1. 支持使用的分布式对象
   包含通用对象桶（Object Bucket）、二进制流（Binary Stream）、地理空间对象桶（Geospatial Bucket）、BitSet、原子整长形（AtomicLong）、原子双精度浮点数（AtomicDouble）、话题（订阅分发）、模糊话题、布隆过滤器（Bloom Filter）、基数估计算法（HyperLogLog）
1. 分布式Object
```
    RBucket<AnyObject> bucket = redisson.getBucket("anyObject");
	bucket.set(new AnyObject(1));
    AnyObject obj = bucket.get();
    bucket.trySet(new AnyObject(3));
    bucket.compareAndSet(new AnyObject(4), new AnyObject(5));
    bucket.getAndSet(new AnyObject(6));
```
2、分布式BitSet
```
    RBitSet set = redisson.getBitSet("simpleBitset");
    set.set(0, true);
    set.set(1812, false);
    set.clear(0);
    set.addAsync("e");
    set.xor("anotherBitset");
```
3、分布式Lock
```
    Redisson redisson = Redisson.create();
    RLock lock = redisson.getLock("anyLock");
	lock.lock();
	lock.lock(10, TimeUnit.SECONDS);
    boolean res = lock.tryLock(100, 10, TimeUnit.SECONDS);
    lock.unlock();
```
4、分布式MultiLock
```
    RLock lock1 = redissonInstance1.getLock("lock1");
    RLock lock2 = redissonInstance2.getLock("lock2");
    RLock lock3 = redissonInstance3.getLock("lock3");
    RedissonMultiLock lock = new RedissonMultiLock(lock1, lock2, lock3);
	lock.lock();
```
5、分布式ReadWriteLock
```
    RReadWriteLock rwlock = redisson.getLock("anyRWLock");

    rwlock.readLock().lock();
    rwlock.writeLock().lock();

    // Lock time-to-live support
    // releases lock automatically after 10 seconds
    // if unlock method not invoked
    rwlock.readLock().lock(10, TimeUnit.SECONDS);
    rwlock.writeLock().lock(10, TimeUnit.SECONDS);

    // Wait for 100 seconds and automatically unlock it after 10 seconds
    boolean res = rwlock.readLock().tryLock(100, 10, TimeUnit.SECONDS);
    boolean res = rwlock.writeLock().tryLock(100, 10, TimeUnit.SECONDS);
    ....
    
    lock.unlock();
```
6、分布式Semaphore
```
   RSemaphore semaphore = redisson.getSemaphore("semaphore");
    semaphore.acquire();
    semaphore.acquire(23);
    semaphore.tryAcquire();
    semaphore.tryAcquire(23, TimeUnit.SECONDS);
    semaphore.release(10);
    semaphore.release();
```
7、分布式AtomicLong
```
    RAtomicLong atomicLong = redisson.getAtomicLong("myAtomicLong");
    atomicLong.set(3);
    atomicLong.incrementAndGet();
    atomicLong.get();
```
8、分布式AtomicDouble
```
    RAtomicDouble atomicDouble = redisson.getAtomicDouble("myAtomicDouble");
    atomicDouble.set(2.81);
    atomicDouble.addAndGet(4.11);
    atomicDouble.get();
```
9、分布式CountDownLatch
```
    RCountDownLatch latch = redisson.getCountDownLatch("anyCountDownLatch");
    latch.trySetCount(1);
    latch.await();
    // in other thread or other JVM
    RCountDownLatch latch = redisson.getCountDownLatch("anyCountDownLatch");
	latch.countDown();
```
10、分布式Topic
```
    RTopic<SomeObject> topic = redisson.getTopic("anyTopic");
	topic.addListener(new MessageListener<SomeObject>() {
        @Override
        public void onMessage(String channel, SomeObject message) {
            //...
        }
    });
    // in other thread or JVM
    RTopic<SomeObject> topic = redisson.getTopic("anyTopic");
    long clientsReceivedMessage = topic.publish(new SomeObject());
```
11、分布式Topic patttern
```
    // subscribe to all topics by `topic1.*` pattern
    RPatternTopic<Message> topic1 = redisson.getPatternTopic("topic1.*");
    int listenerId = topic1.addListener(new PatternMessageListener<Message>() {
        @Override
        public void onMessage(String pattern, String channel, Message msg) {
            Assert.fail();
        }
    });
```


#### 2. 分布式集合
   包含映射（Map）、映射（Map）、多值映射（Multimap）、集（Set）、有序集（SortedSet）、计分排序集（ScoredSortedSet）、字典排序集（LexSortedSet）、列表（List）、列队（Queue）、双端队列（Deque）、阻塞队列（Blocking Queue）、有界阻塞列队（Bounded Blocking Queue）、阻塞双端列队（Blocking Deque）、阻塞公平列队（Blocking Fair Queue）、延迟列队（Delayed Queue）、优先队列（Priority Queue）、优先双端队列（Priority Deque）

1、分布式Map
```
   RMap<String, SomeObject> map = redisson.getMap("anyMap");
    SomeObject prevObject = map.put("123", new SomeObject());
    SomeObject currentObject = map.putIfAbsent("323", new SomeObject());
    SomeObject obj = map.remove("123");
	map.fastPut("321", new SomeObject());
	map.fastRemove("321");
    Future<SomeObject> putAsyncFuture = map.putAsync("321");
    Future<Void> fastPutAsyncFuture = map.fastPutAsync("321");
	map.fastPutAsync("321", new SomeObject());
	map.fastRemoveAsync("321");
```
2、Map eviction
```
RMapCache<String, SomeObject> map = redisson.getMapCache("anyMap");
// ttl = 10 minutes,
map.put("key1", new SomeObject(), 10, TimeUnit.MINUTES);
// ttl = 10 minutes, maxIdleTime = 10 seconds
map.put("key1", new SomeObject(), 10, TimeUnit.MINUTES, 10, TimeUnit.SECONDS);
// ttl = 3 seconds
map.putIfAbsent("key2", new SomeObject(), 3, TimeUnit.SECONDS);
// ttl = 40 seconds, maxIdleTime = 10 seconds
map.putIfAbsent("key2", new SomeObject(), 40, TimeUnit.SECONDS, 10, TimeUnit.SECONDS);
```
3、分布式Set
除此之外还有，还支持Set eviction， SortedSet, ScoredSortedSet, LexSortedSet
```
RSet<SomeObject> set = redisson.getSet("anySet");
set.add(new SomeObject());
set.remove(new SomeObject());
```
4、分布式List
```
RList<SomeObject> list = redisson.getList("anyList");
list.add(new SomeObject());
list.get(0);
list.remove(new SomeObject());
```
5、分布式Blocking Queue
还支持Queue, Deque, Blocking Deque
```
RBlockingQueue<SomeObject> queue = redisson.getBlockingQueue("anyQueue");
queue.offer(new SomeObject());
SomeObject obj = queue.peek();
SomeObject someObj = queue.poll();
SomeObject ob = queue.poll(10, TimeUnit.MINUTES);
```


#### 3. 分布式锁（Lock）和同步器（Synchronizer）
   包含可重入锁（Reentrant Lock）公平锁（Fair Lock）联锁（MultiLock）红锁（RedLock）读写锁（ReadWriteLock）信号量（Semaphore）可过期性信号量（PermitExpirableSemaphore）闭锁（CountDownLatch）。

#### 4. 分布式服务
   包含分布式远程服务（Remote Service）、分布式实时对象（Live Object）服务、分布式执行服务（Executor Service）、分布式调度任务服务（Scheduler Service）、分布式映射归纳服务（MapReduce）

#### 5. 额外功能
   包含对Redis节点的操作、复杂多维对象结构和对象引用的支持、命令的批量执行、脚本执行、底层Redis客户端



### redisson其他使用
布隆过滤器:
“判断一个元素是否存在于一个大数据量的集合中”，这个集合用布隆过滤器构造一个10万或者100万的集合，然后分别查询指定的元素是否在该集合中，将检测结果输出。
```
   @Test
    public void test2() throws Exception {
        // 定义缓存中的key
        String key = "myBloomFilterData";
        // 定义一千万的数据
        Long total = 1_000_0L;
        // 创建布隆过滤器
        RBloomFilter<Integer> rBloomFilter = redissonClient.getBloomFilter(key);
        rBloomFilter.tryInit(total,0.01F);
        for (int i = 0; i < total; i++) {
             rBloomFilter.add(i);
        }

        // 开始测试特定的元素是否在布隆过滤器中
        log.info("布隆过滤器是否包含1：{}",rBloomFilter.contains(1));
        log.info("布隆过滤器是否包含-11：{}",rBloomFilter.contains(-11));
        log.info("布隆过滤器是否包含100000000：{}",rBloomFilter.contains(100000000));
        log.info("布隆过滤器是否包含10：{}",rBloomFilter.contains(10));
        
        //如果是判断引用过滤
        //log.info("该布隆过滤器是否包含数据(1,\"2\")：{}",bloomFilter.contains (new BloomDTO(1,"a")));

    }
```
发布订阅主题
```
//发布消息
            String key = "redisson:sendTopic";
            // 根据key从Redisson拿到RTopic,RTopic去异步发送主题
            RTopic rTopic = redissonClient.getTopic(key,new SerializationCodec());
            rTopic.publish(dto);
            
// 监听主题
            RTopic rTopic = redissonClient.getTopic("redisson:sendTopic",new SerializationCodec());
            rTopic.addListener(UserLoginDto.class, new MessageListener<UserLoginDto>() {
                @Override
                public void onMessage(CharSequence charSequence, UserLoginDto userLoginDto) {
                    log.info("监听到用户登录成功后的消息");
                    if (userLoginDto != null) {
                        SysLog sysLog = new SysLog();
                        sysLog.setUserId(userLoginDto.getUserId());
                        sysLog.setCreateTime(new Date());
                        sysLog.setModule("用户登录");
                        sysLog.setData("用户登录成功");
                        try {
                            sysLogService.recordLog(sysLog);
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    }
                }
            });

            


```
数据类型映射之Map
```
        String key = "redissonRmap";
        RMapDto rMapDto1 = new RMapDto(1,"a");
        
        // 从redis中获取map
        RMap<String,Object> rMap = redissonClient.getMap(key);
        // 正常添加元素
        rMap.put(String.valueOf(rMapDto1.getId()),rMapDto1);

        // 异步的方式添加元素
        rMap.putAsync(String.valueOf(rMapDto2.getId()),rMapDto2);

        // 添加元素之前判断元素是否存在，如果不存在才添加，否则不添加
        rMap.putIfAbsent(String.valueOf(rMapDto3.getId()),rMapDto3);

        // 添加元素之前判断元素是否存在，如果不存在才添加，否则不添加(异步)
        rMap.putIfAbsentAsync(String.valueOf(rMapDto4.getId()),rMapDto4);

        // 以下是快速的方式
        // 快速添加元素
        rMap.fastPut(String.valueOf(rMapDto5.getId()),rMapDto5);

        // 快速异步的方式添加
        rMap.fastPutAsync(String.valueOf(rMapDto6.getId()),rMapDto6);

        // 快速添加方式（判断是否存在）
        rMap.fastPutIfAbsent(String.valueOf(rMapDto7.getId()),rMapDto7);

        // 快速添加元素并且判断是否存在（异步）
        rMap.fastPutIfAbsentAsync(String.valueOf(rMapDto8.getId()),rMapDto8);
```
获取元素和删除元素：
```
        String key = "redissonRmap";

        // 从redis中获取map
        RMap<String,Object> rMap = redissonClient.getMap(key);

        // 获取所有的key集合
        Set<String> ids = rMap.keySet();
        Map<String,Object> map = rMap.getAll(ids);
        log.info("元素列表：{}",map);

        // 删除指定元素id
        String removeId = "1";
        rMap.remove(removeId);
        // 重新赋值为Map
        map = rMap.getAll(rMap.keySet());

        // 删除指定的元素ID集合
        String[] removeIds = new String[]{"2","3","4"};
        rMap.fastRemove(removeIds);
        map = rMap.getAll(rMap.keySet());
        log.info("移除{}后的元素集合为{}",removeIds,map);
```
说明：
开源中间件redisson还为RMap提供了一系列不同功能特性的数据类型，这些数据类型按照特性分成三大类
1. 元素淘汰：允许针对一个映射中的每个元素单独设定有效时间和最常闲置时间。
2. 本地缓存：指的是可以将部分数据保存到本地，从而将数据提取数据提高最多45倍。所有本地同名本地缓存公用 一个发布-订阅话题，所有更新和过期消息都通过该话题共享。仅适用于Redis的集群环境，该映射结构也叫集群分布式映射
3. 数据分片：可以使得单一的映射结构突破Redis自身的容量限制，让其可以随着容量增大而增大，并且在扩容的同时读写性能和元素淘汰的处理能力随之呈线性增长

元素淘汰：
```
        // 元素淘汰
        rMapCache.putIfAbsent(rMapDto1.getId(), rMapDto1);
        // 设置有效时间10秒
        rMapCache.putIfAbsent(rMapDto2.getId(), rMapDto2, 20, TimeUnit.SECONDS);
        // 设置有效时间20秒
        rMapCache.putIfAbsent(rMapDto3.getId(), rMapDto3, 10, TimeUnit.SECONDS);
```
本地缓存：
```
        // 定义储存与缓存中间件的Redis的Key
        String key = "myRedissonRLocalCachedMap";
        // 获取RMapCache
        RLocalCachedMap<Integer, List<RMapDto>> rLocalCachedMap = redissonClient.getLocalCachedMap(key,LocalCachedMapOptions.defaults());
        // 本地缓存加入分组结果
        rLocalCachedMap.putAll(map);
        // 再从本地缓存里边取数据
        List<RMapDto> result = rLocalCachedMap.get(1);
```
数据类型集合之Set：
Redisson的功能组件RSet则主要是实现了Java中的Set，该功能组件可以保证集合中的每一个元素不重复，并且还提供了相应的有序集合SortedSet，计分功能排序组件ScoredSortedSet以及字典排序集合功能组件LexSortedSet等。  

SortedSet
```
        String key = "myRedissonSortedSet";
        // 获取有序集合实例
        RSortedSet<RSetDTO> setDTOS = redissonClient.getSortedSet(key);
        // 设置排序规则
        setDTOS.trySetComparator(new RSetComparator());
        // 将对象元素添加到集合中
        setDTOS.add(dto1);
        setDTOS.add(dto2);
        /查看此时有序集合的元素列表
        Collection<RSetDTO> result =  setDTOS.readAll();


/**
 *  //集合RSet数据组件的自定排序
 * @author Administrator
 */

public class RSetComparator implements Comparator<RSetDTO> {
    /**
     * 自定义排序的逻辑
     * @param o1 待比较的数据元素1
     * @param o2 待比较的数据元素2
     */
    @Override
    public int compare(RSetDTO o1, RSetDTO o2) {
        //表示后添加的数据元素如果age更大，则排得越前
        return o2.getAge().compareTo(o1.getAge());
    }
}
```
计分功能排序组件ScoredSortedSet
```
    @Test
    public void testSortedSetScoredSortedSet() {
        String key = "myRedissonScoredSortedSet";
        // 创建对象实例
        RSetDTO dto4 = new RSetDTO(1,"N1",10.0D);
        RSetDTO dto3 = new RSetDTO(2,"N2",2.0D);
        RSetDTO dto2 = new RSetDTO(3,"N3",8.0D);
        RSetDTO dto1 = new RSetDTO(4,"N4",6.0D);
        // 定义得分排序集合ScoredSortedSet实例
        RScoredSortedSet<RSetDTO> rScoredSortedSet = redissonClient.getScoredSortedSet(key);
        // 往得分排序集合ScoredSortedSet添加对象元素
        rScoredSortedSet.add(dto1.getScore(),dto1);
        rScoredSortedSet.add(dto2.getScore(),dto2);
        rScoredSortedSet.add(dto3.getScore(),dto3);
        rScoredSortedSet.add(dto4.getScore(),dto4);
        // 查看此时得分排序集合ScoredSortedSet的元素列表
        // 可以通过SortOrder指定读取出的元素是正序还是倒序
        Collection<RSetDTO> result = rScoredSortedSet.readAll();

        log.info("此时得分排序集合ScoredSortedSet的元素列表-从小到大：{} ", result);
    }
```