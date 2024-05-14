## Spring-boot配置大全

### pom.xml配置
```
<!-- 设置项目编码 -->
<properties>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
       <java.version>1.8</java.version>
</properties>

<!-- 配置java插件版本 -->
<plugin>
       <groupid>org.apache.maven.plugins</groupid>
       <artifactid>maven-compiler-plugin</artifactid>
       <version>3.6</version>
       <configuration>
              <source>1.8</source>
              <target>1.8</target>
       </configuration>
</plugin>
```

### 属性文件配置
```
# 配置项目的名称：
spring.application.name=项目名称
#切换启动环境配置 test:测试环境/dev:开发环境/prod:生产环境
spring.profiles.active=test/dev/prod

#启动端口
server.port=8080 
#当项目出错时跳转的页面
server.error.path=/error
#session失效时间，30m表示30分钟
server.servlet.session.timeout=30m
#项目路径名称，默认为/
server.servlet.context-path=/
#配置tomcat请求编码
server.tomcat.uri-encoding=utf-8
#Tomcat最大线程数
server.tomcat.max-threads=500
#存放Tomcat运行日志和临时文件的目录，若不配值则默认使用系统的临时目录
server.tomcat.basedir=/home/tmp

#Http编码相关的配置
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true
```

### 数据库连接配置
**Mysql**
(1) 引入jar包依赖环境
```
<dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <scope>runtime</scope>
</dependency>

#连接驱动
#spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#连接url
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
#账号
spring.datasource.username=root
#密码
spring.datasource.password=root
```
**Oracle**
```
<!-- https://mvnrepository.com/artifact/com.oracle/ojdbc6 -->
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>11.2.0.3</version>
</dependency>

#oracle配置
spring.datasource.driverClassName=oracle.jdbc.driver.OracleDriver
spring.datasource.url=jdbc:oracle:thin:@127.0.0.1:1521:test
spring.datasource.username=root
spring.datasource.password=123456
```
**PostgreSQL连接配置**
```
<!-- postgresql驱动 -->
<dependency>
       <groupId>org.postgresql</groupId>
       <artifactId>postgresql</artifactId>
</dependency>

#PostgreSQL配置
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://127.0.0.1:5432/test
spring.datasource.username=postgres
spring.datasource.password=123456
```

### 发送邮件配置
```
<dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
<dependency>
       <groupId>com.sun.mail</groupId>
       <artifactId>javax.mail</artifactId>
       <version>RELEASE</version>
</dependency>
```
(2)属性文件配置
```
#163邮箱
spring.mail.host=smtp.163.com
spring.mail.username=xxx@163.com
spring.mail.password=xxx
  
#qq邮箱
#spring.mail.host=smtp.qq.com
#spring.mail.username=xxx@qq.com
#spring.mail.password=xxx
spring.mail.default-encoding=UTF-8
  
#其他邮箱配置类似
```

### 常用Redis配置
(1) 引入jar包依赖环境
```
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
(2)application.properties属性文件配置
```
# REDIS
# Redis数据库索引（默认为0）
spring.redis.database=0
# Redis服务器地址
spring.redis.host=localhost
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制） 默认 8
spring.redis.lettuce.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
spring.redis.lettuce.pool.max-wait=-1
# 连接池中的最大空闲连接 默认 8
spring.redis.lettuce.pool.max-idle=8
# 连接池中的最小空闲连接 默认 0
spring.redis.lettuce.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.timeout=1000
```

### 配置Druid数据源
(1) 引入jar依赖环境
```
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
 <groupId>com.alibaba</groupId>
 <artifactId>druid</artifactId>
 <version>1.1.17</version>
</dependency>
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
 <groupId>log4j</groupId>
 <artifactId>log4j</artifactId>
 <version>1.2.17</version>
</dependency>
```
(2) application.properties属性文件配置

这里以MySQL为例，如下：
```
# 驱动配置信息
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
spring.datasource.username=root
spring.datasource.password=root

# 连接池的配置信息
# 初始化大小，最小，最大
spring.datasource.initialSize=5
spring.datasource.minIdle=5
spring.datasource.maxActive=20
# 配置获取连接等待超时的时间
spring.datasource.maxWait=60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.timeBetweenEvictionRunsMillis=60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.minEvictableIdleTimeMillis=300000
spring.datasource.validationQuery=SELECT 1 FROM DUAL
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
spring.datasource.filters=stat,wall,log4j
# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
```
(3) 代码配置
```
import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.util.HashMap;
import java.util.Map;

/**
* @ClassName: DruidConfiguration
* @Author: 
* @Description:   Druid数据库配置
* @Date: 2019/6/6 19:57
  */
  @Configuration
  public class DruidConfiguration {

private static final Logger log = LoggerFactory.getLogger(DruidConfiguration.class);

@Bean
public ServletRegistrationBean druidServlet() {
log.info("init Druid Servlet Configuration ");
ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean();
servletRegistrationBean.setServlet(new StatViewServlet());
servletRegistrationBean.addUrlMappings("/druid/*");
Map<String, String> initParameters = new HashMap<>();
initParameters.put("loginUsername", "admin");// 用户名
initParameters.put("loginPassword", "admin");// 密码
initParameters.put("resetEnable", "false");// 禁用HTML页面上的“Reset All”功能
initParameters.put("allow", ""); // IP白名单 (没有配置或者为空，则允许所有访问)
//initParameters.put("deny", "192.168.20.1");// IP黑名单 (存在共同时，deny优先于allow)
servletRegistrationBean.setInitParameters(initParameters);
return servletRegistrationBean;
}

@Bean
public FilterRegistrationBean filterRegistrationBean() {
FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
filterRegistrationBean.setFilter(new WebStatFilter());
filterRegistrationBean.addUrlPatterns("/*");
filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
return filterRegistrationBean;
}

@ConfigurationProperties(prefix = "spring.datasource")  //属性前缀
@Bean
public DataSource druid(){
return new DruidDataSource();
}

}
```
### 配置Swagger2接口文档
(1) 引入jar依赖环境
```
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
 <groupId>io.springfox</groupId>
 <artifactId>springfox-swagger2</artifactId>
 <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
 <groupId>io.springfox</groupId>
 <artifactId>springfox-swagger-ui</artifactId>
 <version>2.9.2</version>
</dependency>
```
(2) 代码配置
```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;


@Configuration
@EnableSwagger2
@Profile(value = {"dev", "test"})   //指定运行环境（开发，测试）
public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("你项目的包路径"))  //此处必须修改
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder().title("Api接口接口")
                .description("api接口描述信息")
                .contact(new Contact("作者", "url", "email"))
                .termsOfServiceUrl("https://swagger.io/swagger-ui/")
                .version("1.0")
                .build();
    }
}
```

### JPA相关的配置
```
spring.jpa.database=mysql
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect

spring.jpa.database=sql_server
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.SQLServer2012Dialect
spring.jpa.hibernate.ddl-auto=none
```

### logback日志的配置
(1)在pom.xml文件中引入依赖
```
<!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
<dependency>
 <groupId>ch.qos.logback</groupId>
 <artifactId>logback-classic</artifactId>
 <version>1.2.3</version>
 <scope>test</scope>
</dependency>
```
（2）在项目的resources目录下引入logback-spring.xml配置文件即可，内容如下：

方式一：
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
 <!-- 日志根目录-->
 <springProperty scope="context" name="LOG_HOME" source="logging.path" defaultValue="/logs/spring-boot-logback"/>

 <!-- 日志级别 -->
 <springProperty scope="context" name="LOG_ROOT_LEVEL" source="logging.level.root" defaultValue="DEBUG"/>

 <!--  标识这个"STDOUT" 将会添加到这个logger -->
 <springProperty scope="context" name="STDOUT" source="log.stdout" defaultValue="STDOUT"/>

 <!-- 日志文件名称-->
 <property name="LOG_PREFIX" value="spring-boot-logback" />

 <!-- 日志文件编码-->
 <property name="LOG_CHARSET" value="UTF-8" />

 <!-- 日志文件路径+日期-->
 <property name="LOG_DIR" value="${LOG_HOME}/%d{yyyyMMdd}" />

 <!--对日志进行格式化-->
 <property name="LOG_MSG" value="- | [%X{requestUUID}] | [%d{yyyyMMdd HH:mm:ss.SSS}] | [%level] | [${HOSTNAME}] | [%thread] | [%logger{36}] | --> %msg|%n "/>

 <!--文件大小，默认10MB-->
 <property name="MAX_FILE_SIZE" value="50MB" />

 <!-- 配置日志的滚动时间 ，表示只保留最近 10 天的日志-->
 <property name="MAX_HISTORY" value="10"/>

 <!--输出到控制台-->
 <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
 <!-- 输出的日志内容格式化-->
 <layout class="ch.qos.logback.classic.PatternLayout">
 <pattern>${LOG_MSG}</pattern>
 </layout>
 </appender>

 <!-- 定义 ALL 日志的输出方式:-->
 <appender name="FILE_ALL" class="ch.qos.logback.core.rolling.RollingFileAppender">
 <!--日志文件路径，日志文件名称-->
 <File>${LOG_HOME}/all_${LOG_PREFIX}.log</File>

 <!-- 设置滚动策略，当天的日志大小超过 ${MAX_FILE_SIZE} 文件大小时候，新的内容写入新的文件， 默认10MB -->
 <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

 <!--日志文件路径，新的 ALL 日志文件名称，“ i ” 是个变量 -->
<FileNamePattern>${LOG_DIR}/all_${LOG_PREFIX}%i.log</FileNamePattern>

 <!-- 配置日志的滚动时间 ，表示只保留最近 10 天的日志-->
<MaxHistory>${MAX_HISTORY}</MaxHistory>

 <!--当天的日志大小超过 ${MAX_FILE_SIZE} 文件大小时候，新的内容写入新的文件， 默认10MB-->
 <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
 <maxFileSize>${MAX_FILE_SIZE}</maxFileSize>
 </timeBasedFileNamingAndTriggeringPolicy>

 </rollingPolicy>

 <!-- 输出的日志内容格式化-->
 <layout class="ch.qos.logback.classic.PatternLayout">
 <pattern>${LOG_MSG}</pattern>
 </layout>
 </appender>

 <!-- 定义 ERROR 日志的输出方式:-->
 <appender name="FILE_ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
 <!-- 下面为配置只输出error级别的日志 -->
 <filter class="ch.qos.logback.classic.filter.LevelFilter">
 <level>ERROR</level>
 <OnMismatch>DENY</OnMismatch>
 <OnMatch>ACCEPT</OnMatch>
 </filter>
 <!--日志文件路径，日志文件名称-->
 <File>${LOG_HOME}/err_${LOG_PREFIX}.log</File>

 <!-- 设置滚动策略，当天的日志大小超过 ${MAX_FILE_SIZE} 文件大小时候，新的内容写入新的文件， 默认10MB -->
 <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

 <!--日志文件路径，新的 ERR 日志文件名称，“ i ” 是个变量 -->
<FileNamePattern>${LOG_DIR}/err_${LOG_PREFIX}%i.log</FileNamePattern>

 <!-- 配置日志的滚动时间 ，表示只保留最近 10 天的日志-->
<MaxHistory>${MAX_HISTORY}</MaxHistory>

 <!--当天的日志大小超过 ${MAX_FILE_SIZE} 文件大小时候，新的内容写入新的文件， 默认10MB-->
 <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
 <maxFileSize>${MAX_FILE_SIZE}</maxFileSize>
 </timeBasedFileNamingAndTriggeringPolicy>
 </rollingPolicy>

 <!-- 输出的日志内容格式化-->
 <layout class="ch.qos.logback.classic.PatternLayout">
 <Pattern>${LOG_MSG}</Pattern>
 </layout>
 </appender>

 <!--  配置以配置包下的所有类的日志的打印，级别是 ERROR-->
 <logger name="com.lhf.springboot" level="ERROR" />

 <!-- ${LOG_ROOT_LEVEL} 日志级别 -->
 <root level="${LOG_ROOT_LEVEL}">

 <!-- 标识这个"${STDOUT}"将会添加到这个logger -->
 <appender-ref ref="${STDOUT}"/>

 <!-- FILE_ALL 日志输出添加到 logger -->
 <appender-ref ref="FILE_ALL"/>

 <!-- FILE_ERROR 日志输出添加到 logger -->
 <appender-ref ref="FILE_ERROR"/>
 </root>

</configuration>
```

方式二：
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

 <!--定义日志文件的存储地址 勿在 LogBack的配置中使用相对路径 -->
 <property name="LOG_HOME" value="/tmp/log" />

 <!-- 控制台输出 -->
 <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
 <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
 <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{30} - %msg%n</pattern>
 </encoder>
 </appender>

 <!-- 按照每天生成日志文件 -->
 <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
 <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
 <FileNamePattern>${LOG_HOME}/logs/smsismp.log.%d{yyyy-MM-dd}.log</FileNamePattern>
 <!--日志文件保留天数 -->
 <MaxHistory>30</MaxHistory>
 </rollingPolicy>
 <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
 <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符 -->
 <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{30} - %msg%n</pattern>
 </encoder>
 <!--日志文件最大的大小 -->
 <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
 <MaxFileSize>10MB</MaxFileSize>
 </triggeringPolicy>
 </appender>

 <!-- 日志输出级别 -->
 <root level="INFO">
 <appender-ref ref="STDOUT" />
 <appender-ref ref="FILE" />
 </root>

 <!-- 定义各个包的详细路径，继承root的值 -->
 <logger name="com.lhf.springboot" level="INFO" />

 <!-- 此值由 application.properties的spring.profiles.active=dev指定-->
 <springProfile name="dev">
 <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径 -->
 <property name="LOG_HOME" value="/tmp/log" />
 <logger name="com.lhf.springboot" level="DEBUG" />
 </springProfile>

 <springProfile name="prod">
 <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径 -->
 <property name="LOG_HOME" value="/home" />
 <logger name="com.lhf.springboot" level="INFO" />
 </springProfile>

</configuration>
```

### log4j2日志配置
(1)在pom.xml文件中引入依赖jar包，如下：
```
<dependency> <!-- 引入log4j2依赖 -->
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```
或者
```
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
<dependency>
 <groupId>org.apache.logging.log4j</groupId>
 <artifactId>log4j-core</artifactId>
 <version>2.12.0</version>
</dependency>
```
(2)在项目的resources目录下引入log4j2.xml配置文件，文件内容如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration monitorInterval="5">
 <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->

 <!--变量配置-->
 <Properties>
 <!-- 格式化输出：%date表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度 %msg：日志消息，%n是换行符-->
 <!-- %logger{36} 表示 Logger 名字最长36个字符 -->
 <property name="LOG_PATTERN" value="%date{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n" />
 <!-- 定义日志存储的路径，不要配置相对路径
 <property name="FILE_PATH" value="更换为你的日志路径" />
 <property name="FILE_NAME" value="更换为你的项目名" />
 -->
 <property name="FILE_PATH" value="E:\code\log" />
 <property name="FILE_NAME" value="spring-boot-log4j2" />
 </Properties>

 <appenders>
 <console name="Console" target="SYSTEM_OUT">
 <!--输出日志的格式-->
 <PatternLayout pattern="${LOG_PATTERN}"/>
 <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
 <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
 </console>

 <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，适合临时测试用-->
 <File name="Filelog" fileName="${FILE_PATH}/test.log" append="false">
 <PatternLayout pattern="${LOG_PATTERN}"/>
 </File>

 <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
 <RollingFile name="RollingFileInfo" fileName="${FILE_PATH}/info.log" filePattern="${FILE_PATH}/${FILE_NAME}-INFO-%d{yyyy-MM-dd}_%i.log.gz">
 <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
 <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
 <PatternLayout pattern="${LOG_PATTERN}"/>
 <Policies>
 <!--interval属性用来指定多久滚动一次，默认是1 hour-->
 <TimeBasedTriggeringPolicy interval="1"/>
 <SizeBasedTriggeringPolicy size="10MB"/>
 </Policies>
 <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
 <DefaultRolloverStrategy max="15"/>
 </RollingFile>

 <!-- 这个会打印出所有的warn及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
 <RollingFile name="RollingFileWarn" fileName="${FILE_PATH}/warn.log" filePattern="${FILE_PATH}/${FILE_NAME}-WARN-%d{yyyy-MM-dd}_%i.log.gz">
 <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
 <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
 <PatternLayout pattern="${LOG_PATTERN}"/>
 <Policies>
 <!--interval属性用来指定多久滚动一次，默认是1 hour-->
 <TimeBasedTriggeringPolicy interval="1"/>
 <SizeBasedTriggeringPolicy size="10MB"/>
 </Policies>
 <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
 <DefaultRolloverStrategy max="15"/>
 </RollingFile>

 <!-- 这个会打印出所有的error及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
 <RollingFile name="RollingFileError" fileName="${FILE_PATH}/error.log" filePattern="${FILE_PATH}/${FILE_NAME}-ERROR-%d{yyyy-MM-dd}_%i.log.gz">
 <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
 <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
 <PatternLayout pattern="${LOG_PATTERN}"/>
 <Policies>
 <!--interval属性用来指定多久滚动一次，默认是1 hour-->
 <TimeBasedTriggeringPolicy interval="1"/>
 <SizeBasedTriggeringPolicy size="10MB"/>
 </Policies>
 <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
 <DefaultRolloverStrategy max="15"/>
 </RollingFile>

 </appenders>

 <!--Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。-->
 <!--然后定义loggers，只有定义了logger并引入的appender，appender才会生效-->
 <loggers>

 <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
 <logger name="org.mybatis" level="info" additivity="false">
 <AppenderRef ref="Console"/>
 </logger>
 <!--监控系统信息-->
 <!--若是additivity设为false，则 子Logger 只会在自己的appender里输出，而不会在 父Logger 的appender里输出。-->
 <Logger name="org.springframework" level="info" additivity="false">
 <AppenderRef ref="Console"/>
 </Logger>

 <root level="info">
 <appender-ref ref="Console"/>
 <appender-ref ref="Filelog"/>
 <appender-ref ref="RollingFileInfo"/>
 <appender-ref ref="RollingFileWarn"/>
 <appender-ref ref="RollingFileError"/>
 </root>
 </loggers>

</configuration>
```
### 19. 在属性文件中设置上传文件的大小限制
```
# 设置上传文件的大小限制
spring.servlet.multipart.max-file-size=100MB
spring.servlet.multipart.max-request-size=100MB
```

### 20. 设置开启Debug模式
    application.debug.enabled=true

### 21. 设置访问URL前缀
    server.servlet.context-path=/xxl-job-admin

### 22. 几种数据源配置类型（以MySQL为例）
1. tomcat-jdbc

引入依赖jar包：
```
<dependency>
 <groupId>org.apache.tomcat</groupId>
 <artifactId>tomcat-jdbc</artifactId>
 <version>9.0.24</version>
</dependency>
```
属性文件配置
```
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?Unicode=true&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.datasource.type=org.apache.tomcat.jdbc.pool.DataSource
spring.datasource.tomcat.max-wait=10000
spring.datasource.tomcat.max-active=30
spring.datasource.tomcat.test-on-borrow=true
spring.datasource.tomcat.validation-query=SELECT 1
spring.datasource.tomcat.validation-interval=30000
```
2. spring-boot-starter-jdbc

引入依赖jar包
```
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```
属性文件配置：
```
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.hikari.username=root
spring.datasource.hikari.password=root
spring.datasource.hikari.jdbc-url=jdbc:mysql://127.0.0.1:3306/test?autoReconnect=true&characterEncoding=UTF-8&allowMultiQueries=true&useSSL=false
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.maximum-pool-size=15
spring.datasource.hikari.auto-commit=true
spring.datasource.hikari.idle-timeout=30002
spring.datasource.hikari.pool-name=DatebookHikariCP
spring.datasource.hikari.max-lifetime=500000
spring.datasource.hikari.connection-timeout=30001
spring.datasource.hikari.connection-test-query=SELECT 1
spring.datasource.hikari.validation-timeout=5000
```
3. druid

引入依赖jar包：
```
<dependency>
 <groupId>com.alibaba</groupId>
 <artifactId>druid</artifactId>
 <version>1.1.20</version>
</dependency>
```
属性文件配置：
```
# 驱动配置信息
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/lhf_springboot1?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
spring.datasource.username=root
spring.datasource.password=root

# 连接池的配置信息
# 初始化大小，最小，最大
spring.datasource.initialSize=5
spring.datasource.minIdle=5
spring.datasource.maxActive=20
# 配置获取连接等待超时的时间
spring.datasource.maxWait=60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.timeBetweenEvictionRunsMillis=60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.minEvictableIdleTimeMillis=300000
spring.datasource.validationQuery=SELECT 1 FROM DUAL
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
spring.datasource.filters=stat,wall,log4j
# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
```