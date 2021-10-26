# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## JPA

### 引入配置
配置解析：
1. 依赖配置
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-start-data-jpa</artifactId>
    </dependency>
2. 配置文件
~~~~
spring.jpa.database=MYSQL
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.hibernate.naming-strategy=org.hibernate.cfg.ImprovedNamingStrategy
spring.jpa.hibernate.dialect=org.hibernate.dialect.MYSQL5Dialect
~~~~

### 使用步骤：
1. 让持久层接口 Dao（以UserDao）  继承 Repository 接口。
例：继承 CrudRepository，它会自动为域对象创建增删改查方法。

2. 使用 CrudRepository 也有副作用，它可能暴露了你不希望暴露给业务层的方法。比如某些接口你只希望提供增加的操作而不希望提供删除的方法。针对这种情况，开发者只能退回到 Repository 接口，然后到 CrudRepository 中把希望保留的方法声明复制到自定义的接口中即可.

3. 分页查询和排序是持久层常用的功能，Spring Data 为此提供了 PagingAndSortingRepository 接口，它继承自 CrudRepository 接口，在 CrudRepository 基础上新增了两个与分页有关的方法。但是，我们很少会将自定义的持久层接口直接继承自 PagingAndSortingRepository，而是在继承 Repository 或 CrudRepository 的基础上，在自己声明的方法参数列表最后增加一个 Pageable 或 Sort 类型的参数，用于指定分页或排序信息即可，这比直接使用 PagingAndSortingRepository 提供了更大的灵活性。

4. JpaRepository 是继承自 PagingAndSortingRepository 的针对 JPA 技术提供的接口，它在父接口的基础上，提供了其他一些方法，比如 flush()，saveAndFlush()，deleteInBatch() 等。如果有这样的需求，则可以继承该接口。



### 查询方式
1. 通过解析方法名创建查询
如 find、findBy、read、readBy、get、getBy

2. 使用 @Query 创建查询
@Query 注解的使用非常简单，只需在声明的方法上面标注该注解，同时提供一个 JP QL 查询语句即可，如下所示：
 @Query("select a from AccountInfo a where a.accountId = ?1") 
 public AccountInfo findByAccountId(Long accountId); 

3. 通过调用 JPA 命名查询语句创建查询
 public List<AccountInfo> findTop5();

4. nativeQuery方式：
    @SuppressWarnings("JpaQlInspection")
    @Modifying
    @Query(value = "UPDATE lw_voucher lc SET lc.state=:state WHERE lc.id=:id", nativeQuery = true)	
    void setStateById(@Param("state") int state, @Param("id") int id);

5. @NameQuery在entity前编写命名方法：
    1.@NamedQueries(value = { 
             @NamedQuery(name = User.QUERY_FIND_BY_LOGIN, 
                                            query = "select u from User u where u." + User.PROP_LOGIN 
                                                    + " = :username"), 
            @NamedQuery(name = "getUsernamePasswordToken", 
                            query = "select new com.aceona.weibo.vo.TokenBO(u.username,u.password) from User u where u." + User.PROP_LOGIN 
                                + " = :username")}) 
    2：在自己实现的DAO的Repository接口里面定义一个同名的方法，示例如下：
    public List<UserModel> findByAge(int age);
    3：然后就可以使用了，Spring会先找是否有同名的NamedQuery，如果有，那么就不会按照接口定义的方法来解析。
    
### 分页查询
原生分页查询：这里的Repostory extends JpaRepository
JPA页码从0开始，默认提供的页码都是0开始
JpaRepository<T, ID extends Serializable> extends PagingAndSortingRepository 
~~~~
Page<T> findAll(Pageable var1);	//——需要传入一个Pageable
Pageable pageable =new PageRequest(0, 5);	//PageRequest是Page的子类：
Page<User> datas = userService.findAll(pageable);
~~~~


### 存储过程：
在entity前调用存储过程，以后直接使用name调用
@NamedStoredProcedureQueries({
        //name是JPA中的存储过程的名字, procedureName是数据库存储过程的名字
        @NamedStoredProcedureQuery(name = "proc_voucher_deleteById", procedureName = "proc_voucher_deleteById", parameters = {
                @StoredProcedureParameter(mode = ParameterMode.IN, name = "id", type = Integer.class)
        }),
        @NamedStoredProcedureQuery(name = "in_and_out_test", procedureName = "test_pkg.in_and_out_test", parameters = {
//                @StoredProcedureParameter(mode = ParameterMode.IN, name = "inParam1", type = String.class),
//                @StoredProcedureParameter(mode = ParameterMode.OUT, name = "outParam1", type = String.class)
        })
})



### 注解说明
1. 实体注解
    @Entity
说明：@Entity说明这个class是实体类，并且使用默认的orm规则，即class名即数据库表中表名，class字段名即表中的字段名
如果想改变这种默认的orm规则，就要使用@Table来改变class名与数据库中表名的映射规则，@Column来改变class中字段名与db中表的字段名的映射规则

@SecondaryTable 的使用：一个实体类对应两张或多张表

2.表名和字段注解
    @Table(name = "lw_order")
说明：@Table来改变class名与数据库中表名的映射规则

    @Column(name = "name",updatable=false)
说明：@Column对应数据库中字段 ——name指定字段名，updatable是否可以被更新

3.主键标识为
    @Id,
    @GeneratedValue(strategy = GenerationType.IDENTITY)
其生成规则由@GeneratedValue设定的.这里的@id和@GeneratedValue都是JPA的标准用法, 
public enum GenerationType{    
    TABLE,    	//使用一个特定的数据库表格来保存主键。 
    SEQUENCE,    //根据底层数据库的序列来生成主键，条件是数据库支持序列
    IDENTITY,    //主键由数据库自动生成（主要是自动增长型） 
    AUTO   	//主键由程序控制。 
}  


说明：生成uuid：

    @GeneratedValue(generator = "system-uuid")
    @GenericGenerator(name = "system-uuid", strategy = "uuid")
自定义主键生成策略，由@GenericGenerator实现。 

hibernate在JPA的基础上进行了扩展，可以用一下方式引入hibernate独有的主键生成策略，就是通过@GenericGenerator加入

	name属性指定生成器名称。 
	strategy属性指定具体生成器的类名。 
~~~~
hibernate提供多种主键生成策略，有点是类似于JPA，有的是hibernate特有：
native: 对于 oracle 采用 Sequence 方式，对于MySQL 和 SQL Server 采用identity（自增主键生成机制），native就是将主键的生成工作交由数据库完成，hibernate不管（很常用）。 
uuid: 采用128位的uuid算法生成主键，uuid被编码为一个32位16进制数字的字符串。占用空间大（字符串类型）。 
hilo: 使用hilo生成策略，要在数据库中建立一张额外的表，默认表名为hibernate_unique_key,默认字段为integer类型，名称是next_hi（比较少用）。 
assigned: 在插入数据的时候主键由程序处理（很常用），这是 <generator>元素没有指定时的默认生成策略。等同于JPA中的AUTO。 
identity: 使用SQL Server 和 MySQL 的自增字段，这个方法不能放到 Oracle 中，Oracle 不支持自增字段，要设定sequence（MySQL 和 SQL Server 中很常用）。 
          等同于JPA中的INDENTITY。 
select: 使用触发器生成主键（主要用于早期的数据库主键生成机制，少用）。 
sequence: 调用底层数据库的序列来生成主键，要设定序列名，不然hibernate无法找到。 
seqhilo: 通过hilo算法实现，但是主键历史保存在Sequence中，适用于支持 Sequence 的数据库，如 Oracle（比较少用） 
increment: 插入数据的时候hibernate会给主键添加一个自增的主键，但是一个hibernate实例就维护一个计数器，所以在多个实例运行的时候不能使用这个方法。 
foreign: 使用另外一个相关联的对象的主键。通常和<one-to-one>联合起来使用。 
guid: 采用数据库底层的guid算法机制，对应MYSQL的uuid()函数，SQL Server的newid()函数，ORACLE的rawtohex(sys_guid())函数等。 
uuid.hex: 看uuid，建议用uuid替换。 
sequence-identity: sequence策略的扩展，采用立即检索策略来获取sequence值，需要JDBC3.0和JDK4以上（含1.4）版本 
~~~~

4. @Transient	非持久字段或属性，表中不存在的字段注解

5. @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm") 入参

6. @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm")	出参

7. @JoinColumn(name="addressID")//注释本表中指向另一个表的外键。

8. @PrePersist	//在持久化之前

9. @PreUpdate	//在更新操作之前

10. @Temporal(DATE)	//JPA 持续性提供程序应只为 java.util.Date 和 java.util.Calendar 类型的字段或属性持久保存的数据库类型