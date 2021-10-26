# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## redis对象操作工具类

### 代码实现 

```java
public class RedisUtil<K extends Serializable, V extends Serializable> {

    protected static final Logger LOGGER = LoggerFactory.getLogger(RedisUtil.class);

    private static RedisTemplate redisTemplate = SpringContextHolder.getBean("redisTemplate");

    /**
     * 自定义：redisTemplate序列化，默认key和value都是JdkSerializationRedisSerializer
     * 自定义：key使用字符串序列化,value使用二进制序列化
     * 说明：JdkSerializationRedisSerializer序列化后长度最小，Jackson2JsonRedisSerializer效率最高。
     */
    static {
        RedisSerializer stringSerializer = new StringRedisSerializer();
        redisTemplate.setKeySerializer(stringSerializer);
        redisTemplate.setValueSerializer(new SerializeUtil());
        redisTemplate.setHashKeySerializer(stringSerializer);
        redisTemplate.setHashValueSerializer(new SerializeUtil());
    }

    /**
     * redis存入数据
     *
     * @param key   键
     * @param value 值
     */
    public static void set(final String key, Object value) {
        //1.使用opsForValue操作对象
        redisTemplate.opsForValue().set(key, value);
        //2.使用execute操作对象
//        redisTemplate.execute((RedisCallback<Object>) connection -> {
//            connection.set(redisTemplate.getStringSerializer().serialize(key),
//                    redisTemplate.getValueSerializer().serialize(value));
//            return null;
//        });
    }

    /**
     * redis存入数据
     *
     * @param key   键
     * @param value 值
     * @param l     持久化时间/s
     */
    public static void set(final String key, Object value, final long l) {
        redisTemplate.opsForValue().set(key, value, l);

    }

    /**
     * redis取出对象
     *
     * @param key 键
     * @param <T>
     * @return
     */
    public static <T> T get(final String key) {
        return (T) redisTemplate.opsForValue().get(key);

    }

    /**
     * list元素进栈（左入）
     *
     * @param key   键
     * @param value 值
     */
    public static void pushListValue(final String key, Object value) {
        redisTemplate.opsForList().leftPush(key, value);
    }

    /**
     * list元素出栈（右出）
     *
     * @param key 键
     * @param l   超过等待时间仍没有元素则退出,单位s
     * @return
     */
    public static Object rightPullToList(final String key, final long l) {
        if (l > 0) {
            return redisTemplate.opsForList().rightPop(key, l, TimeUnit.SECONDS);
        }
        return redisTemplate.opsForList().rightPop(key);
    }

    /**
     * 右出左进
     *
     * @param key   键
     * @param value 值
     * @return
     */
    public static Object rightPopAndLeftPush(final String key, String value) {
        return redisTemplate.opsForList().rightPopAndLeftPush(key, value);
    }

    /**
     * 存入list对象（左入）
     *
     * @param key   键
     * @param value 值
     */
    public static void setList(final String key, List<Object> value) {
        redisTemplate.opsForList().leftPushAll(key, value);
    }

    /**
     * 获取list的size
     *
     * @param key list的键
     * @return
     */
    public static Long getListSize(final String key) {
        return redisTemplate.opsForList().size(key);
    }

    /**
     * 定位获取list中的元素
     *
     * @param key list的键
     * @param l   元素定位
     * @return
     */
    public static Object getListIndexValue(final String key, final long l) {
        return redisTemplate.opsForList().index(key, l);
    }

    /**
     * 取整个list
     *
     * @param key list的键
     * @return
     */
    public static List<Object> getList(final String key) {

        ListOperations<String, Object> listOperations = redisTemplate.opsForList();
        Long size = listOperations.size(key);
        List<Object> list = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            Object obj = listOperations.rightPop(key);
            list.add(obj);
        }
        return list;
    }

    /**
     * 追加map单个对象
     *
     * @param key      map对象的key
     * @param keyMap   map中的元素的键
     * @param valueMap map中的元素的值
     */
    public static void putMapValue(final String key, final String keyMap, Object valueMap) {
        redisTemplate.opsForHash().put(key, keyMap, valueMap);
    }

    /**
     * 存入map对象
     *
     * @param key 键
     * @param map map对象
     */
    public static void setMap(final String key, Map<String, Object> map) {
        redisTemplate.opsForHash().putAll(key, map);
    }

    /**
     * 取map中的单个对象
     *
     * @param key    键
     * @param keyMap map中的元素的键
     */
    public static Object getMap(final String key, final String keyMap) {
        return redisTemplate.opsForHash().get(key, keyMap);
//        //取map中所有的值
//        List<String>reslutMapList=redisTemplate.opsForHash().values("map1");
//        //取map中所有的键
//        Set<String>resultMapSet=redisTemplate.opsForHash().keys("map1");

    }

    /**
     * 取map中的所有对象
     *
     * @param key map的键
     * @return
     */
    public static Map<String, Object> getMap(final String key) {
        return redisTemplate.opsForHash().entries(key);
    }

}
```