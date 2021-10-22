# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## SpringBatch批处理
一个典型的批处理应用程序大致如下：
从数据库，文件或队列中读取大量记录。
以某种方式处理数据。
以修改之后的形式写回数据。

### 总体架构
![alt text](../../static/java/springbatch.png "")
在spring batch中一个job可以定义很多的步骤step，
每一个step里面可以定义其专属的ItemReader用于读取数据，ItemProcesseor用于处理数据，ItemWriter用于写数据，
而每一个定义的job则都在JobRepository里面，我们可以通过JobLauncher来启动某一个job。

### 什么是Job
Job和Step是spring batch执行批处理任务最为核心的两个概念。

在Job这个接口当中定义了五个方法，它的实现类主要有两种类型的job，一个是simplejob，另一个是flowjob。
在spring batch当中，job是最顶层的抽象，除job之外我们还有JobInstance以及JobExecution这两个更加底层的抽象。

一个job是我们运行的基本单位，它内部由step组成。job本质上可以看成step的一个容器。一个job可以按照指定的逻辑顺序组合step，并提供了我们给所有step设置相同属性的方法，例如一些事件监听，跳过策略。
Spring Batch以SimpleJob类的形式提供了Job接口的默认简单实现，它在Job之上创建了一些标准功能。


### 什么是JobInstance
```
public interface JobInstance {
	/**
	 * Get unique id for this JobInstance.
	 * @return instance id
	 */
	public long getInstanceId();
	/**
	 * Get job name.
	 * @return value of 'id' attribute from <job>
	 */
	public String getJobName();	
}
```
他的方法很简单，一个是返回Job的id，另一个是返回Job的名字。

JobInstance指的是job运行当中，作业执行过程当中的概念。Instance本就是实例的意思。

比如说现在有一个批处理的job，它的功能是在一天结束时执行行一次。我们假定这个批处理job的名字为'EndOfDay'。在这个情况下，那么每天就会有一个逻辑意义上的JobInstance, 而我们必须记录job的每次运行的情况。

### 什么是JobParameters
在上文当中我们提到了，同一个job每天运行一次的话，那么每天都有一个jobIntsance，但他们的job定义都是一样的，那么我们怎么来区别一个job的不同jobinstance了。 不妨先做个猜想，虽然jobinstance的job定义一样，但是他们有的东西就不一样，例如运行时间。

spring batch中提供的用来标识一个jobinstance的东西是：JobParameters。 JobParameters对象包含一组用于启动批处理作业的参数，它可以在运行期间用于识别或甚至用作参考数据。我们假设的运行时间，就可以作为一个JobParameters。

例如, 我们前面的'EndOfDay'的job现在已经有了两个实例，一个产生于1月1日，另一个产生于1月2日，那么我们就可以定义两个JobParameter对象：一个的参数是01-01, 另一个的参数是01-02。 因此，识别一个JobInstance的方法可以定义为：
JobInstance = Job + JobP

### 什么是JobExecution
JobExecution指的是单次尝试运行一个我们定义好的Job的代码层面的概念。 job的一次执行可能以失败也可能成功。只有当执行成功完成时，给定的与执行相对应的JobInstance才也被视为完成。

还是以前面描述的EndOfDay的job作为示例，假设第一次运行01-01-2019的JobInstance结果是失败。 那么此时如果使用与第一次运行相同的Jobparameter参数（即01-01-2019）作业参数再次运行，那么就会创建一个对应于之前jobInstance的一个新的JobExecution实例,JobInstance仍然只有一个。

每一个方法的注释已经解释的很清楚，这里不再多做解释。只提一下BatchStatus，JobExecution当中提供了一个方法getBatchStatus用于获取一个job某一次特地执行的一个状态。BatchStatus是一个代表job状态的枚举类，其定义如下：
```
public enum BatchStatus {STARTING, STARTED, STOPPING,
STOPPED, FAILED, COMPLETED, ABANDONED }
```

### 什么是Step

每一个Step对象都封装了批处理作业的一个独立的阶段。 事实上，每一个Job本质上都是由一个或多个步骤组成。 每一个step包含定义和控制实际批处理所需的所有信息。 任何特定的内容都由编写Job的开发人员自行决定。 一个step可以非常简单也可以非常复杂。 例如，一个step的功能是将文件中的数据加载到数据库中，那么基于现在spring batch的支持则几乎不需要写代码。 更复杂的step可能具有复杂的业务逻辑，这些逻辑作为处理的一部分。 与Job一样，Step具有与JobExecution类似的StepExecution，

### 什么是StepExecution

StepExecution表示一次执行Step, 每次运行一个Step时都会创建一个新的StepExecution，类似于JobExecution。 但是，某个步骤可能由于其之前的步骤失败而无法执行。 且仅当Step实际启动时才会创建StepExecution。

一次step执行的实例由StepExecution类的对象表示。` 每个StepExecution都包含对其相应步骤的引用以及JobExecution和事务相关的数据，例如提交和回滚计数以及开始和结束时间。 此外，每个步骤执行都包含一个ExecutionContext，其中包含开发人员需要在批处理运行中保留的任何数据，例如重新启动所需的统计信息或状态信息。

### 什么是ExecutionContext
ExecutionContext即每一个StepExecution 的执行环境。它包含一系列的键值对。我们可以用如下代码获取ExecutionContext


### 什么是JobRepository
JobRepository是一个用于将上述job，step等概念进行持久化的一个类。 它同时给Job和Step以及下文会提到的JobLauncher实现提供CRUD操作。 首次启动Job时，将从repository中获取JobExecution，并且在执行批处理的过程中，StepExecution和JobExecution将被存储到repository当中。

@EnableBatchProcessing注解可以为JobRepository提供自动配置。

### 什么是JobLauncher
JobLauncher这个接口的功能非常简单，它是用于启动指定了JobParameters的Job，为什么这里要强调指定了JobParameter，原因其实我们在前面已经提到了，jobparameter和job一起才能组成一次job的执行。下面是代码实例：
```
public interface JobLauncher {
 
public JobExecution run(Job job, JobParameters jobParameters)
            throws JobExecutionAlreadyRunningException, JobRestartException,
                   JobInstanceAlreadyCompleteException, JobParametersInvalidException;
}
```
上面run方法实现的功能是根据传入的job以及jobparamaters从JobRepository获取一个JobExecution并执行Job。

### 什么是Item Reader

ItemReader是一个读数据的抽象，它的功能是为每一个Step提供数据输入。 当ItemReader以及读完所有数据时，它会返回null来告诉后续操作数据已经读完。Spring Batch为ItemReader提供了非常多的有用的实现类，比如JdbcPagingItemReader，JdbcCursorItemReader等等。

ItemReader支持的读入的数据源也是非常丰富的，包括各种类型的数据库，文件，数据流，等等。几乎涵盖了我们的所有场景。


### 什么是Item Writer
既然ItemReader是读数据的一个抽象，那么ItemWriter自然就是一个写数据的抽象，它是为每一个step提供数据写出的功能。写的单位是可以配置的，我们可以一次写一条数据，也可以一次写一个chunk的数据，关于chunk下文会有专门的介绍。ItemWriter对于读入的数据是不能做任何操作的。

Spring Batch为ItemWriter也提供了非常多的有用的实现类，当然我们也可以去实现自己的writer功能。

### 什么是Item Processor
ItemProcessor对项目的业务逻辑处理的一个抽象, 当ItemReader读取到一条记录之后，ItemWriter还未写入这条记录之前，I我们可以借助temProcessor提供一个处理业务逻辑的功能，并对数据进行相应操作。如果我们在ItemProcessor发现一条数据不应该被写入，可以通过返回null来表示。ItemProcessor和ItemReader以及ItemWriter可以非常好的结合在一起工作，他们之间的数据传输也非常方便。我们直接使用即可。

### chunk 处理流程
spring batch提供了让我们按照chunk处理数据的能力，一个chunk的示意图如下：
由于我们一次batch的任务可能会有很多的数据读写操作，因此一条一条的处理并向数据库提交的话效率不会很高，因此spring batch提供了chunk这个概念，我们可以设定一个chunk size，spring batch 将一条一条处理数据，但不提交到数据库，只有当处理的数据数量达到chunk size设定的值得时候，才一起去commit.

### skip策略和失败处理
一个batch的job的step，可能会处理非常大数量的数据，难免会遇到出错的情况，出错的情况虽出现的概率较小，但是我们不得不考虑这些情况，因为我们做数据迁移最重要的是要保证数据的最终一致性。spring batch当然也考虑到了这种情况，并且为我们提供了相关的技术支持，请看如下bean的配置：

我们需要留意这三个方法，分别是skipLimit(),skip(),noSkip(),
skipLimit方法的意思是我们可以设定一个我们允许的这个step可以跳过的异常数量，假如我们设定为10，则当这个step运行时，只要出现的异常数目不超过10，整个step都不会fail。注意，若不设定skipLimit，则其默认值是0.
skip方法我们可以指定我们可以跳过的异常，因为有些异常的出现，我们是可以忽略的。
noSkip方法的意思则是指出现这个异常我们不想跳过，也就是从skip的所以exception当中排除这个exception，从上面的例子来说，也就是跳过所有除FileNotFoundException的exception。那么对于这个step来说，FileNotFoundException就是一个fatal的exception，抛出这个exception的时候step就会直接fail

### 如何默认不启动job
在使用java config使用spring batch的job时，如果不做任何配置，项目在启动时就会默认去跑我们定义好的批处理job。那么如何让项目在启动时不自动去跑job呢？
spring batch的job会在项目启动时自动run，如果我们不想让他在启动时run的话，可以在application.properties中添加如下属性：
> spring.batch.job.enabled=false

### 在读数据时内存不够
在使用spring batch做数据迁移时，发现在job启动后，执行到一定时间点时就卡在一个地方不动了，且log也不再打印，等待一段时间之后，得到如下错误：

红字的信息为：Resource exhaustion event：the JVM was unable to allocate memory from the heap.

翻译过来的意思就是项目发出了一个资源耗尽的事件，告诉我们java虚拟机无法再为堆分配内存。

造成这个错误的原因是: 这个项目里的batch job的reader是一次性拿回了数据库里的所有数据，并没有进行分页，当这个数据量太大时，就会导致内存不够用。解决的办法有两个:
- 调整reader读数据逻辑，按分页读取，但实现上会麻烦一些，且运行效率会下降
- 增大service内存

---

### Springbatch建表语句
```sql
CREATE TABLE BATCH_JOB_INSTANCE (
JOB_INSTANCE_ID BIGINT NOT NULL PRIMARY KEY ,
VERSION BIGINT ,
JOB_NAME VARCHAR(100) NOT NULL,
JOB_KEY VARCHAR(32) NOT NULL,
constraint JOB_INST_UN unique (JOB_NAME, JOB_KEY)
) ENGINE=InnoDB;

CREATE TABLE BATCH_JOB_EXECUTION (
JOB_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY ,
VERSION BIGINT ,
JOB_INSTANCE_ID BIGINT NOT NULL,
CREATE_TIME DATETIME NOT NULL,
START_TIME DATETIME DEFAULT NULL ,
END_TIME DATETIME DEFAULT NULL ,
STATUS VARCHAR(10) ,
EXIT_CODE VARCHAR(2500) ,
EXIT_MESSAGE VARCHAR(2500) ,
LAST_UPDATED DATETIME,
JOB_CONFIGURATION_LOCATION VARCHAR(2500) NULL,
constraint JOB_INST_EXEC_FK foreign key (JOB_INSTANCE_ID)
references BATCH_JOB_INSTANCE(JOB_INSTANCE_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_JOB_EXECUTION_PARAMS (
JOB_EXECUTION_ID BIGINT NOT NULL ,
TYPE_CD VARCHAR(6) NOT NULL ,
KEY_NAME VARCHAR(100) NOT NULL ,
STRING_VAL VARCHAR(250) ,
DATE_VAL DATETIME DEFAULT NULL ,
LONG_VAL BIGINT ,
DOUBLE_VAL DOUBLE PRECISION ,
IDENTIFYING CHAR(1) NOT NULL ,
constraint JOB_EXEC_PARAMS_FK foreign key (JOB_EXECUTION_ID)
references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_STEP_EXECUTION (
STEP_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY ,
VERSION BIGINT NOT NULL,
STEP_NAME VARCHAR(100) NOT NULL,
JOB_EXECUTION_ID BIGINT NOT NULL,
START_TIME DATETIME NOT NULL ,
END_TIME DATETIME DEFAULT NULL ,
STATUS VARCHAR(10) ,
COMMIT_COUNT BIGINT ,
READ_COUNT BIGINT ,
FILTER_COUNT BIGINT ,
WRITE_COUNT BIGINT ,
READ_SKIP_COUNT BIGINT ,
WRITE_SKIP_COUNT BIGINT ,
PROCESS_SKIP_COUNT BIGINT ,
ROLLBACK_COUNT BIGINT ,
EXIT_CODE VARCHAR(2500) ,
EXIT_MESSAGE VARCHAR(2500) ,
LAST_UPDATED DATETIME,
constraint JOB_EXEC_STEP_FK foreign key (JOB_EXECUTION_ID)
references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_STEP_EXECUTION_CONTEXT (
STEP_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
SHORT_CONTEXT VARCHAR(2500) NOT NULL,
SERIALIZED_CONTEXT TEXT ,
constraint STEP_EXEC_CTX_FK foreign key (STEP_EXECUTION_ID)
references BATCH_STEP_EXECUTION(STEP_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_JOB_EXECUTION_CONTEXT (
JOB_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
SHORT_CONTEXT VARCHAR(2500) NOT NULL,
SERIALIZED_CONTEXT TEXT ,
constraint JOB_EXEC_CTX_FK foreign key (JOB_EXECUTION_ID)
references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_STEP_EXECUTION_SEQ (
ID BIGINT NOT NULL,
UNIQUE_KEY CHAR(1) NOT NULL,
constraint UNIQUE_KEY_UN unique (UNIQUE_KEY)
) ENGINE=InnoDB;

INSERT INTO BATCH_STEP_EXECUTION_SEQ (ID, UNIQUE_KEY) select * from (select 0 as ID, '0' as UNIQUE_KEY) as tmp where not exists(select * from BATCH_STEP_EXECUTION_SEQ);

CREATE TABLE BATCH_JOB_EXECUTION_SEQ (
ID BIGINT NOT NULL,
UNIQUE_KEY CHAR(1) NOT NULL,
constraint UNIQUE_KEY_UN unique (UNIQUE_KEY)
) ENGINE=InnoDB;

INSERT INTO BATCH_JOB_EXECUTION_SEQ (ID, UNIQUE_KEY) select * from (select 0 as ID, '0' as UNIQUE_KEY) as tmp where not exists(select * from BATCH_JOB_EXECUTION_SEQ);

CREATE TABLE BATCH_JOB_SEQ (
ID BIGINT NOT NULL,
UNIQUE_KEY CHAR(1) NOT NULL,
constraint UNIQUE_KEY_UN unique (UNIQUE_KEY)
) ENGINE=InnoDB;

INSERT INTO BATCH_JOB_SEQ (ID, UNIQUE_KEY) select * from (select 0 as ID, '0' as UNIQUE_KEY) as tmp where not exists(select * from BATCH_JOB_SEQ);
```

### SpringBatch代码实现

#### 依赖
```
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-batch</artifactId>
		</dependency>
		<!--batch任务多台部署时加锁解决重复执行-->
		<dependency>
			<groupId>net.javacrumbs.shedlock</groupId>
			<artifactId>shedlock-spring</artifactId>
			<version>4.1.0</version>
		</dependency>
		<dependency>
			<groupId>net.javacrumbs.shedlock</groupId>
			<artifactId>shedlock-provider-jdbc-template</artifactId>
			<version>4.1.0</version>
		</dependency>
```

#### 配置文件
配置了多数据源，配置了migrate作为springBatch分页、分片的大小，还配置elastic job作为调度，
```
spring:
  batchDatasource:
    username: root
    password: Sgcc_1234
    url: jdbc:mysql://192.168.160.178:3306/batch?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.jdbc.Driver
    hikari:
      maximum-pool-size: 50
  datasource:
    username: testgroup
    password: Sgcc_1234
    url: jdbc:mysql://192.168.102.115:3306/testgroup?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.jdbc.Driver
    hikari:
      maximum-pool-size: 50
      max-lifetime: 120000
      idleTimeout: 130000
    tomcat:
      test-on-borrow: true
      validation-query: SELECT 1
  flyway:
    enabled: false
  batch:
    job:
      enabled: false
debug: true
migrate:
  config:
    pageSize: 10000
    chunkSize: 1
    threadSize: 4
gx:
  web:
    enable: true
    base-package: "com.sgcc.ywzt.controller"
    version: 1.0.0
    description: "repairsrv web api"
elasticjob:
  tracing:
    type: RDB
  regCenter:
    serverLists: 192.168.161.224:2181,192.168.161.44:2181,192.168.161.112:2181
    namespace: cluster
```


#### 全局Configuration
启动类注解：@EnableBatchProcessing(modular = true)
```
@SpringBootApplication
@EnableDiscoveryClient
@EnableBatchProcessing(modular = true)
public class BootstrapApplication {

  public static void main(String[] args) {
    SpringApplication.run(BootstrapApplication.class, args);
  }

}
```
@EnableBatchProcessing是打开Batch。如果要实现多Job的情况，需要把EnableBatchProcessing注解的modular设置为true，让每个Job使用自己的ApplicationConext。


#### BatchConfig 
批处理配置：定义JobRepository，Reader，定义Step，Writer，Job（层级递进关系）
```
@Slf4j
@Configuration
public class BatchConfig extends DefaultBatchConfigurer {

  @Autowired
  private JobBuilderFactory jobBuilderFactory;

  @Autowired
  private StepBuilderFactory stepBuilderFactory;

  @Autowired
  @Qualifier("primaryDatasource")
  private DataSource primaryDatasource;

  @Autowired
  @Qualifier("batchDatasource")
  private DataSource batchDatasource;

  @Autowired
  private MigrateConfig config;

  @Autowired
  private DiscountProcessor processor;

  @Autowired
  private IOrderService orderService;

  /**
   * 定义JobRepository
   *
   * @return
   * @throws Exception
   */
  @Override
  protected JobRepository createJobRepository() throws Exception {
    JobRepositoryFactoryBean factory = new JobRepositoryFactoryBean();
    factory.setDataSource(batchDatasource);
    factory.setTransactionManager(this.getTransactionManager());
    factory.afterPropertiesSet();
    factory.setIsolationLevelForCreate("ISOLATION_REPEATABLE_READ");
    return factory.getObject();
  }



  /**
   * 数据读取 根据id 查询保证性能 分页读取
   * Reader顾名思义就是从数据源读取数据。
   * Spring Batch给我们提供了很多好用实用的reader，基本能满足我们所有需求。比如FlatFileItemReader，JdbcCursorItemReader，JpaPagingItemReader等。也可以自己实现Reader。
   *
   * 本例子里面，数据源是文本文件，所以我们就使用FlatFileItemReader。FlatFileItemReader是从文件里面一行一行的读取数据。
   * 首先需要设置文件路径，也就是设置resource。
   * 因为我们需要把一行文本映射为Message类，所以我们需要自己设置并实现LineMapper。
   */
  @Bean
  @StepScope
  public JdbcPagingItemReader<OrderDiscountsDetailEntity> discountReader(@Value("#{jobParameters[startTime]}") String startTime, @Value("#{jobParameters[endTime]}") String endTime)
      throws Exception {
    JdbcPagingItemReader<OrderDiscountsDetailEntity> reader = new JdbcPagingItemReader();
    MySqlDRDSPagingQueryProvider provider =  new MySqlDRDSPagingQueryProvider();
    provider.setSelectClause(" * ");
    provider.setFromClause("from ord_order_discounts_detail");
    provider.setWhereClause("preferential_type in (50,51) and order_state = 20 and fin_clearing_discount_id is null and state =1  and update_time >= '" + startTime + "' and update_time <= '" + endTime + "' and comment != '"+UPDATE_COMMENT+"'" );
    Map<String, Order> keys = new LinkedHashMap();
    keys.put("id", Order.ASCENDING);
    provider.setSortKeys(keys);
    reader.setQueryProvider(provider);
    reader.setDataSource(primaryDatasource);
    reader.setPageSize(config.getPageSize());
    reader.setRowMapper(new OrderDiscountsRowMapper());
    reader.afterPropertiesSet();
    reader.setSaveState(true);
    return reader;
  }

  /**
   * 定义Step
   * stepBuilderFactory是注入进来的，然后get里面是Step的名字。
   * 我们的Step中可以构建很多东西，比如reader，processer，writer，listener等等。
   * @param discountReader
   * @return
   */
  @Bean
  public Step migrateOrderDiscountConsumedStep(@Qualifier("discountReader") JdbcPagingItemReader<OrderDiscountsDetailEntity> discountReader) {
    return this.stepBuilderFactory.get("migrateOrderDiscountStep")
        .<OrderDiscountsDetailEntity, OrderUnionEntity>chunk(config.getChunkSize())
        .reader(discountReader)
        .processor(processor)
        .writer(marketResultWriter())
        .taskExecutor(new SimpleAsyncTaskExecutor("migrate_thread"))
        .throttleLimit(config.getThreadSize())
        .build();
  }


  @Bean
  public ItemWriter<? super OrderUnionEntity> marketResultWriter() {
    return new MarketResultWriter(orderService);
  }


  /**
   * 定义Job，这个job只有一个step
   * jobBuilderFactory是注入进来的，get里面的就是job的名字。
   *
   * @param step
   * @return
   */
  @Bean("migrateOrderDiscountJob")
  public Job migrateOrderDiscountJob(@Qualifier("migrateOrderDiscountConsumedStep") Step step) {
    return this.jobBuilderFactory.get("migrateOrderDiscountJob")
        .start(step)
        .incrementer(new RunIdIncrementer())
        .build();
  }
}

```

#### Chunk
Spring batch在配置Step时采用的是基于Chunk的机制，即每次读取一条数据，再处理一条数据，累积到一定数量后再一次性交给writer进行写入操作。这样可以最大化的优化写入效率，整个事务也是基于Chunk来进行。

比如我们定义chunk size是50，那就意味着，spring batch处理了50条数据后，再统一向数据库写入。
这里有个很重要的点，chunk前面需要定义数据输入类型和输出类型，由于我们输入是Message，输出也是Message，所以两个都直接写Message了。
如果不定义这个类型，会报错。
```
.<Message, Message>chunk(CHUNK_SIZE)
```


#### Line Mapper
LineMapper的输入就是获取一行文本，和行号，然后转换成Message。
在本例子里面，一行文本就是一个json对象，所以我们使用JsonParser来转换成Message。
```
public class MessageLineMapper implements LineMapper<Message> {
    private MappingJsonFactory factory = new MappingJsonFactory();

    @Override
    public Message mapLine(String line, int lineNumber) throws Exception {   
        JsonParser parser = factory.createParser(line);
        Map<String, Object> map = (Map) parser.readValueAs(Map.class);
        Message message = new Message();
        ... // 转换逻辑
        return message;
    }
}
```

#### Processor
Reader和Writer中间处理过程：从它的接口可以看出，需要定义输入和输出的类型，把输入I通过某些逻辑处理之后，返回输出O。
```
@Slf4j
@Component
public class DiscountProcessor implements
    ItemProcessor<OrderDiscountsDetailEntity, OrderUnionEntity> {

  @Autowired
  private IOrderService orderService;
  @Value("${package.url}")
  private String packageUrl;

  @Override
  public OrderUnionEntity process(OrderDiscountsDetailEntity orderDiscountsDetailEntity)
      throws Exception {
    OrdOrderConsumeEntity byId = orderService.findById(orderDiscountsDetailEntity.getTradeFlowNo());
    if (Objects.isNull(byId)){
      return null;
    }
    PackageConsumeReq packageConsumeReq = new PackageConsumeReq();
    packageConsumeReq.setThirdTradeNo(byId.getTradeFlowNo());
    packageConsumeReq.setOrderAmount(byId.getAmount());
    packageConsumeReq.setElecCharge(byId.getTotalElecty());
    packageConsumeReq.setElecAmount(byId.getElecAmount());
    packageConsumeReq.setServiceAmount(byId.getServiceAmount());

    String result = HttpUtil.doPost(getOkHttpUrlbyQuerySrvEs(packageUrl),
        JsonUtil.toJson(packageConsumeReq), null, null);
    if (StringUtils.isNotBlank(result)){
      log.info("构造PackageConsumeResp");
      PackageConsumeResp packageConsumeResp = JsonUtil.fromJson(result, PackageConsumeResp.class);
      OrderUnionEntity orderUnionEntity = new OrderUnionEntity();
      orderUnionEntity.setOrdOrderConsume(byId);
      orderUnionEntity.setPackageConsumeResp(packageConsumeResp);
      return orderUnionEntity;
    }
    return null;
  }
}
```

#### Writer
Writer顾名思义就是把数据写入到目标数据源里面。
Spring Batch同样给我们提供很多好用实用的writer。比如ItemWriter，JpaItemWriter，FlatFileItemWriter，HibernateItemWriter，JdbcBatchItemWriter等。同样也可以自定义。

ItemWriter处理list数据
```
@Slf4j
@RequiredArgsConstructor
public class MarketResultWriter implements ItemWriter<OrderUnionEntity> {

  private final IOrderService orderService;
  @Autowired
  private EventBus eventBus;

  @Override
  public void write(List<? extends OrderUnionEntity> list) throws Exception {

    log.info("进入write：list Size：{}", list.size());
    if (CollectionUtils.isNotEmpty(list)){
      for (OrderUnionEntity item: list){
        if (Objects.nonNull(item)){
          OrderDiscountsDetailEntity orderDiscountsDetailEntity = orderService.addPackageOrderDisCountDetail(item.getOrdOrderConsume(), item.getPackageConsumeResp());
//          orderService.discountsUpdate(orderDiscountsDetailEntity);
//          orderService.consumeUpdate(orderDiscountsDetailEntity.getTradeFlowNo());
          //异步更新DiscountsDetail
          eventBus.post(
              UpdateDiscountsEvent.builder()
                  .orderDiscountsDetailEntity(orderDiscountsDetailEntity)
                  .build()
          );
        }

      }
    }
  }
}
```


#### Listener
Spring Batch同样实现了非常完善全面的listener，listener很好理解，就是用来监听每个步骤的结果。比如可以有监听step的，有监听job的，有监听reader的，有监听writer的。没有你找不到的listener，只有你想不到的listener。

在本例子里面，我只关心，read的时候有没有出错，和write的时候有没有出错，所以，我只实现了ReadListener和WriteListener。
在read出错的时候，把错误结果写入一个单独的error列表文件中。
```
public class MessageItemReadListener implements ItemReadListener<Message> {
    private Writer errorWriter;

    public MessageItemReadListener(Writer errorWriter) {
        this.errorWriter = errorWriter;
    }

    @Override
    public void beforeRead() {
    }

    @Override
    public void afterRead(Message item) {
    }

    @Override
    public void onReadError(Exception ex) {
         errorWriter.write(format("%s%n", ex.getMessage()));
    }
}
```
在write出错的时候，也做同样的事情，把出错的原因写入单独的日志中。
```
public class MessageWriteListener implements ItemWriteListener<Message> {

    @Autowired
    private Writer errorWriter;

    @Override
    public void beforeWrite(List<? extends Message> items) {
    }

    @Override
    public void afterWrite(List<? extends Message> items) {
    }

    @Override
    public void onWriteError(Exception exception, List<? extends Message> items) {
        errorWriter.write(format("%s%n", exception.getMessage()));
        for (Message message : items) {
            errorWriter.write(format("Failed writing message id: %s", message.getObjectId()));
        }
    }
}
```

#### Skip
Spring Batch提供了skip的机制，也就是说，如果出错了，可以跳过。如果你不设置skip，那么一条数据出错了，整个job都会挂掉。
设置skip的时候一定要设置什么Exception才需要跳过，并且跳过多少条数据。如果失败的数据超过你设置的skip limit，那么job就会失败。
你可以分别给reader和writer等设置skip机制。
```
writer(messageItemWriter).faultTolerant().skip(Exception.class).skipLimit(SKIP_LIMIT)
```

#### Retry
这个和Skip是一样的原理，就是失败之后可以重试，你同样需要设置重试的次数。
同样可以分别给reader，writer等设置retry机制。

如果同时设置了retry和skip，会先重试所有次数，然后再开始skip。比如retry是10次，skip是20，会先重试10次之后，再开始算第一次skip。
```
.writer(marketResultWriter()).faultTolerant().skip(Exception.class).retry(Exception.class).retryLimit(RETRY_LIMIT)
```

#### 运行Job

运行就是在main方法里面用JobLauncher去运行你制定的job。
```
//需要注入jobLauncher和Job
jobLauncher.run(migrateOrderDiscountJob, params);
//第二种：通过Spring application得到jobRegistry，然后通过job的名字找到对应的job。

```
也可以构造elasticJob当做springbatch的调度器，这样就能自如的控制springbatch的启停。

在运行的时候会传一些参数，比如你job里面需要的文件路径或者文件日期等，就可以通过这个jobParameters传进去。如果没有参数，可以默认传当前时间进去。
```
/**
 * 构造JobParameters传参
 * 
 */
private static JobParameters createJobParams() {
    //构造字符串
    JobParameters params = new JobParametersBuilder()
        .addString("startTime", DateUtil.getTimeString(startTime))
        .addString("endTime", DateUtil.getTimeString(endTime))
        .addString("random",getParameter(jobParameterMap,"random"))
        .toJobParameters();
    //构造日期
    return new JobParametersBuilder().addDate("date", new Date()).toJobParameters();
}
```

#### Spring Batch数据表
springBatch共有15张表，重点关注以下三张表：

- batch_job_instance：这张表能看到每次运行的job名字。
- batch_job_execution：这张表能看到每次运行job的开始时间，结束时间，状态，以及失败后的错误消息是什么。
- batch_step_execution：这张表你能看到更多关于step的详细信息。比如step的开始时间，结束时间，提交次数，读写次数，状态，以及失败后的错误信息等。

