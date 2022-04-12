# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## mybatis相关

#### MyBatis @Param 注解，使用场景

第一种：方法有多个参数，需要 @Param 注解

例如下面这样：

@Mapper
public interface UserMapper {
Integer insert(@Param("username") String username, @Param("address") String address);
}
对应的 XML 文件如下：
```
<insert id="insert" parameterType="org.javaboy.helloboot.bean.User">
    insert into user (username,address) values (#{username},#{address});
</insert>
```
这是最常见的需要添加 @Param 注解的场景。

第二种：方法参数要取别名，需要 @Param 注解
当需要给参数取一个别名的时候，我们也需要 @Param 注解，例如方法定义如下：
```
@Mapper
public interface UserMapper {
User getUserByUsername(@Param("name") String username);
}
```
对应的 XML 定义如下：
```
<select id="getUserByUsername" parameterType="org.javaboy.helloboot.bean.User">
    select * from user where username=#{name};
</select>
```

第三种：XML 中的 SQL 使用了 $ ，那么参数中也需要 @Param 注解
$ 会有注入漏洞的问题，但是有的时候你不得不使用 $ 符号，例如要传入列名或者表名的时候，这个时候必须要添加 @Param 注解，例如：
```
@Mapper
public interface UserMapper {
List<User> getAllUsers(@Param("order_by")String order_by);
}
```
对应的 XML 定义如下：
```
<select id="getAllUsers" resultType="org.javaboy.helloboot.bean.User">
    select * from user
 <if test="order_by!=null and order_by!=''">
        order by ${order_by} desc
 </if>
</select>
```
前面这三种，都很容易懂，相信很多小伙伴也都懂，除了这三种常见的场景之外，还有一个特殊的场景，经常被人忽略。

第四种，那就是动态 SQL ，如果在动态 SQL 中使用了参数作为变量，那么也需要 @Param 注解，即使你只有一个参数。
如果我们在动态 SQL 中用到了 参数作为判断条件，那么也是一定要加 @Param 注解的，例如如下方法：
```
@Mapper
public interface UserMapper {
List<User> getUserById(@Param("id")Integer id);
}
```
定义出来的 SQL 如下：
```
<select id="getUserById" resultType="org.javaboy.helloboot.bean.User">
    select * from user
 <if test="id!=null">
        where id=#{id}
 </if>
</select>
```
这种情况，即使只有一个参数，也需要添加 @Param 注解，而这种情况却经常被人忽略！


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






