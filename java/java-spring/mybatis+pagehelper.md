# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## mybatis相关

### MyBatis @Param 注解，使用场景

**Mybatis @Param用和不用的区别**
- 单个参数
  - 基本数据类型：SQL语句中不论有没有动态SQL，加不加@Param都可（动态sql为包含在<if test=...中的语句） 
  - 对象：SQL语句中不论有没有动态SQL，
    - （1）加@Param，test和#{}中获取方式是对象.属性，因为绑定的是对象。如：#{user.username}
    - （2）不加@Param，test和#{}中获取方式直接属性即可。如：#{username}

- 多个参数
  - 基本数据类型
    - 1）不加@Param注解，可以写 param1、param2 … 这种的参数表示形式，也可以参数写一致（因为mybatis-plus高版本之后（经测试3.1.2必须要加）允许不加 @Param指定参数名称，自动会以入参的名称作为param key，低版本必须要加上@Param）
    - 2）加了@param之后，xml中就可以直接输入参数了，但是经测试写param1、param2 … 这种的参数也没问题，不过最好保持参数一致。
  - 参数包含对象(List<User> getUser(String role,User user);)：加不加@Param均可，都是通过对象.属性获取，如：#{user.username}

- 集合（针对低版本，经测试mybatis-plus3.1.2版本必须要加）
  - 1、list作为参数，不加@Param的话，<foreach>标签内的collection必须为list，否则报错。或者不加@Param，collection中使用list也可以。
  - 2、数组作为参数，不加@Param的话，<foreach>标签内的collection必须为array。
  - 3、map作为参数，直接用属性值即可。如： UserInfo selectByUserIdAndStatusMap(Map<String,Object> map);


补充：需要加@Param注解的场景：
- 方法有多个参数，且非顺序获取参数时，需要 @Param 注解。(Mybatis自动解析的Map参数列表为[arg0,arg1,param1,param2,@param注解参数...])
- 方法参数要取别名，需要 @Param 注解：User getUserByUsername(@Param("name") String username);
- XML 中的 SQL 使用了 $ ，那么参数中也需要 @Param 注解，如：order by ${order_by} desc ($ 会有注入漏洞的问题，但是有的时候你不得不使用 $ 符号，例如要传入列名或者表名的时候，这个时候必须要添加 @Param 注解)


### mybatis 配置多数据源
参考文档：https://mp.weixin.qq.com/s/qN1b5iSkkIhkA03RtppiJg

步骤：
- Pom 包就不贴了比较简单该依赖的就依赖
- 配置文件 配置两个数据库连接
```
mybatis:
  mapper-locations:
    - classpath:mapper/*.xml
    - classpath*:com/**/mapper/*.xml
  type-aliases-package: com.soonphe.timber.portal.entity

spring:
  batchDatasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.160.178:3306/test1?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.jdbc.Driver
    hikari:
      maximum-pool-size: 50
  datasource:
    username: testgroup
    password: 123456
    url: jdbc:mysql://192.168.102.115:3306/test2?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.jdbc.Driver
```
- 多数据源配置Config
```
/**
 * 首先创建 DataSource，然后创建 SqlSessionFactory 再创建事务，最后包装到 SqlSessionTemplate 中。
 * 指定扫描的 mapper xml文件地址，以及分库dao层代码
 * @Primary 指定主库（必须指定主库，不然会报错）
 */
@Configuration
@MapperScan(basePackages = "com.soonphe.timber.dao.test1", sqlSessionTemplateRef = "test1SqlSessionTemplate")
public class DataSource1Config {

    @Bean(name = "test1DataSource")
    @ConfigurationProperties(prefix = "spring.datasource")
    @Primary
    public DataSource testDataSource() {

        return DataSourceBuilder.create().build();
    }

    @Bean(name = "test1SqlSessionFactory")
    @Primary
    public SqlSessionFactory testSqlSessionFactory(@Qualifier("test1DataSource") DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath:mybatis/mapper/test1/*.xml"));
        return bean.getObject();
    }

    @Bean(name = "test1TransactionManager")
    @Primary
    public DataSourceTransactionManager testTransactionManager(@Qualifier("test1DataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    @Bean(name = "test1SqlSessionTemplate")
    @Primary
    public SqlSessionTemplate testSqlSessionTemplate(@Qualifier("test1SqlSessionFactory") SqlSessionFactory sqlSessionFactory) throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory);
    }

}
```
第二个数据源配置
```
@Configuration
@MapperScan(basePackages = "com.soonphe.timber.dao.test2", sqlSessionTemplateRef = "test2SqlSessionTemplate")
public class DataSource1Config {

    @Bean(name = "test1DataSource")
    @ConfigurationProperties(prefix = "spring.batchDatasource")
    public DataSource testDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "test2SqlSessionFactory")
    public SqlSessionFactory testSqlSessionFactory(@Qualifier("test1DataSource") DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath:mybatis/mapper/test1/*.xml"));
        return bean.getObject();
    }

    @Bean(name = "test2TransactionManager")
    public DataSourceTransactionManager testTransactionManager(@Qualifier("test1DataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    @Bean(name = "test2SqlSessionTemplate")
    public SqlSessionTemplate testSqlSessionTemplate(@Qualifier("test1SqlSessionFactory") SqlSessionFactory sqlSessionFactory) throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory);
    }

}
```
- dao 层和 xml层


### pageHelper分页是如何实现的
使用pageHelper一般的分页查询sql，我们可以看到会执行两条sql，分部是`select count(0)...`，和`select ... limit ?,?`

原理：
- 调用PageHelper.startPage(pageNo,pageLimit)的时候,就已经静悄悄地把我们的分页参数存储到一个变量 ThreadLocal<Page> LOCAL_PAGE;这样可以保证分页的时候，多线程参数互不影响
- 执行Mapper进行查询,实际上是被一个叫做PageInterceptor.java拦截到了,执行了它重写的interceptor方法. 这里涉及到MyBatis里的拦截器原理,本文不重点说明了,相信聪明你肯定已经知道啦;
  该方法里,主要是做了如下事情:
    - (1) 获取到MappedStatement, 拿到业务写好的sql, 将sql改造成select count(0) 并执行查询,并将执行结果存到了LOCAL_PAGE里的Page里的total属性.
    - (2) 获取到我们自己写在xml里的sql , 并append一些分页sql段,然后执行, 将执行结果存到了LOCAL_PAGE里的Page里的list属性.Page 其实是ArrayList的子类.
    - 可以看出,结果都是封装到了Page里, 最后交由PageInfo,可以获取到总条数,总页数,是否还有下一页等参数.


核心拼接转换代码：
- 改造sql
    - 统计总条数: this.dialect.getCountSql
    - 分页: this.dialect.getPageSql
    - 底层都是先借助PageHelper, 然后再落实到具体的实现类.
    - 重点关注this.dialect.getPageSql（数据库不同，改造sql也不同）

例：
mysql:
```
public String getPageSql(String sql, Page page, CacheKey pageKey) {
        StringBuilder sqlBuilder = new StringBuilder(sql.length() + 14);
        sqlBuilder.append(sql);
        if (page.getStartRow() == 0L) {
            sqlBuilder.append("\n LIMIT ? ");
        } else {
            sqlBuilder.append("\n LIMIT ?, ? ");
        }

        return sqlBuilder.toString();
    }
```

oracle
```
public String getPageSql(String sql, Page page, CacheKey pageKey) {
        StringBuilder sqlBuilder = new StringBuilder(sql.length() + 120);
        sqlBuilder.append("SELECT * FROM ( ");
        sqlBuilder.append(" SELECT TMP_PAGE.*, ROWNUM PAGEHELPER_ROW_ID FROM ( \n");
        sqlBuilder.append(sql);
        sqlBuilder.append("\n ) TMP_PAGE)");
        sqlBuilder.append(" WHERE PAGEHELPER_ROW_ID <= ? AND PAGEHELPER_ROW_ID > ?");
        return sqlBuilder.toString();
    }
```

### mybatis-plus

#### 常用操作
普通查询
```
service.selectListByWrapper(new QueryWrapper<TmAdvert>()
                .lambda().like(TmAdvert::getName, "毛")
               .or(e -> e.like(TmAdvert::getName, "张")));
               
new QueryWrapper<TmAdvert>().like("name", "毛")  //普通查询
new QueryWrapper<TmAdvert>().lambda().like(TmAdvert::getName, "毛")  //同上
new LambdaQueryWrapper<TmAdvert>().like(TmAdvert::getName, "毛")   //同上
Wrappers.<TmAdvert>lambdaQuery().like(TmAdvert::getName, "毛") //同上
new QueryWrapper<WmsWalletFtHistory>().lambda()
                        .eq(WmsWalletFtHistory::getUid, uid)
                        .eq(type > 0, TmAdvert::getType, type); //匹配携带条件
                        .orderByDesc(TmAdvert::getId))).getRecords();


new QueryWrapper<TmAdvert>().inSql("name", "select id from role where id = 2")  //携带子查询
new QueryWrapper<TmAdvert>().nested(i -> i.eq("role_id", 2L).or().eq("role_id", 3L))   //嵌套查询
```

分页查询
```
IPage<TmAdvert> page1 = service.selectByAdvert(page, baseVo);

IPage<TmAdvert> page = service.page(new Page<>(0, 12),
```


#### mybatis-plus分库处理
分库处理有两种：
- 使用接口 ：@DS 切换数据源。
- 代码切换：DynamicDataSourceContextHolder
```
public final class DynamicDataSourceContextHolder {

    /**
     * 为什么要用链表存储(准确的是栈) * <pre> * 为了支持嵌套切换，如ABC三个service都是不同的数据源 * 其中A的某个业务要调B的方法，B的方法需要调用C的方法。一级一级调用切换，形成了链。 * 传统的只设置当前线程的方式不能满足此业务需求，必须模拟栈，后进先出。 * </pre>
     */
    @SuppressWarnings("unchecked")
    private static final ThreadLocal<Deque<String>> LOOKUP_KEY_HOLDER = new ThreadLocal() {
        @Override
        protected Object initialValue() {
            return new ArrayDeque();
        }
    };

    private DynamicDataSourceContextHolder() {
    }

    /**
     * 获得当前线程数据源
     * * @return 数据源名称
     */
    public static String peek() {
        return LOOKUP_KEY_HOLDER.get().peek();
    }

    /**
     * 设置当前线程数据源 * <p> * 如非必要不要手动调用，调用后确保最终清除 * </p> * * @param ds 数据源名称
     */
    public static void push(String ds) {
        LOOKUP_KEY_HOLDER.get().push(StringUtils.isEmpty(ds) ? "" : ds);
    }

    /**
     * 清空当前线程数据源 * <p> * 如果当前线程是连续切换数据源 只会移除掉当前线程的数据源名称 * </p>
     */
    public static void poll() {
        Deque<String> deque = LOOKUP_KEY_HOLDER.get();
        deque.poll();
        if (deque.isEmpty()) {
            LOOKUP_KEY_HOLDER.remove();
        }
    }

    /**
     * 强制清空本地线程 * <p> * 防止内存泄漏，如手动调用了push可调用此方法确保清除 * </p>
     */
    public static void clear() {
        LOOKUP_KEY_HOLDER.remove();
    }
}
```
首先通过调用改类的push方法将要传入的dbName，下面写你的业务的逻辑，完成后，最好再调用改类的poll方法或者clear方法（需要注意的是这个poll方法和clear方法是一定要调用的，最好写再final里面）
这样就可以通过代码来手动切换数据源啦。
