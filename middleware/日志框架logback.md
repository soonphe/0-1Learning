# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


### 日志系统设计

### 参考配置
```
<?xml version="1.0" encoding="UTF-8"?>
<!--scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。
scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。
debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。-->
<configuration scan="true">
    <!--
    设置变量，可设置多个，定义日志文件的存储位置，勿在 LogBack 的配置中使用相对路径
     -->
    <springProperty scop="context" name="springAppName" source="spring.application.name"
                    defaultValue=""/>
    <springProperty scope="context" name="LOG_PATH" source="log.path"/>
    <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>

    <!-- 日志颜色 -->
    <property name="CONSOLE_LOG_PATTERN"
              value="%date{yyyy-MM-dd HH:mm:ss} | %highlight(%-5level) | %boldYellow(%thread) | %boldGreen(%logger) | %msg%n"/>
    <!--
    appender规定打印格式，过滤器，滚动
    1.ConsoleAppender：把日志添加到控制台
    2.FileAppender：把日志添加到文件，可添加rollingPolicy或triggeringPolicy属性，下同
    3.RollingFileAppender extends FileAppender<E>：日志添加到文件，且可滚动
    -->
    <!-- 控制台日志 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- encoder格式化日志， 默认配置为PatternLayoutEncoder -->
        <encoder>
            <!-- 即时刷新，设为false会提升日志吞吐量，内存中会缓存8K的日志数据 -->
            <!--<ImmediateFlush>true</ImmediateFlush>-->
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <!--            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>-->
<!--            <charset class="java.nio.charset.Charset">GBK</charset>-->
        </encoder>

    </appender>

    <!-- 自定义日志配置——每天生成文件 -->
    <appender name="sysLogAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--文件名，相对目录/绝对目录，如果上级目录不存在会自动创建，没有默认值-->
        <file>${LOG_HOME}/debug.log</file>
        <!--append：日志被追加到文件结尾，如果是 false，清空现存文件，默认是true-->
        <append>true</append>
        <!-- 根据时间和大小滚动，超过30日就覆盖最早的日志 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/%d{yyyy-MM, aux}/debug.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <maxFileSize>50MB</maxFileSize>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>[${springAppName},%X{X-B3-TraceId:-},%X{X-B3-SpanId:-},%X{X-B3-ParentSpanId:-}] %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level [%logger{50}] %file:%line - %msg%n</pattern>
        </encoder>

        <!--filter级别过滤器  onMatch：匹配，onMismatch：不匹配-->
        <!--过滤器的日志级别配置为INFO，所有INFO级别的日志交给appender处理，非INFO级别的日志，被过滤掉&ndash;&gt;-->
        <!--<filter class="ch.qos.logback.classic.filter.LevelFilter">-->
        <!--<level>INFO</level>-->
        <!--<onMatch>ACCEPT</onMatch>-->
        <!--<onMismatch>DENY</onMismatch>-->
        <!--</filter>-->
        <!--临界值过滤器： 过滤掉 TRACE 和 DEBUG 级别的日志-->
        <!--<filter class="ch.qos.logback.classic.filter.ThresholdFilter">-->
        <!--<level>WARN</level>-->
        <!--</filter>-->
        <!--求值过滤器 过滤掉所有日志消息中不包含“billing”字符串的日志-->
        <!-- 默认为 ch.qos.logback.classic.boolex.JaninoEventEvaluator -->
        <!--<filter class="ch.qos.logback.core.filter.EvaluatorFilter">-->
        <!--<evaluator>-->
        <!--<expression>return message.contains("billing");</expression>-->
        <!--</evaluator>-->
        <!--<OnMatch>ACCEPT </OnMatch>-->
        <!--<OnMismatch>DENY</OnMismatch>-->
        <!--</filter>-->
    </appender>

    <appender name="error" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_HOME}/error.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/%d{yyyy-MM}/error.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <maxFileSize>50MB</maxFileSize>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>[${springAppName},%X{X-B3-TraceId:-},%X{X-B3-SpanId:-},%X{X-B3-ParentSpanId:-}] %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level [%logger{50}] %file:%line - %msg%n</pattern>
        </encoder>
        <!-- 定义日志级别 -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
    </appender>

    <!-- 指定多日志名打印
    name指定包中的日志文件
    level不设置则集成root
    additivity为true表示向上级logger传递 默认是true——即传递给root中的logger打印  -->
    <logger name="sysLog" additivity="false" level="ERROR">
        <appender-ref ref="sysLogAppender"/>
    </logger>
    <!--<logger name="daoLog" additivity="false" level="ERROR">-->
    <!--<appender-ref ref="daoLogAppender"/>-->
    <!--</logger>-->

    <!-- dev,test,prod多环境配置-->
    <springProfile name="dev">
        <!-- 根log: 默认打印格式，level: 设置打印级别，
        trace(优先级最低) debug(默认) info warn error 打印level同级及以上的所有日志-->
        <root level="DEBUG">
            <appender-ref ref="STDOUT"/>
            <appender-ref ref="sysLogAppender"/>
            <appender-ref ref="error"/>
        </root>
    </springProfile>
    <springProfile name="test,prod">
        <root level="info">
            <appender-ref ref="sysLogAppender"/>
            <appender-ref ref="error"/>
        </root>
    </springProfile>

</configuration>
```

### logback动态配置日志路径
定义变量
- 在 logback.xml 中定义
```
  <property name="LOGPATH" value="/home/admin/logs" />
```
- 在VM变量中定义
```
  -DLOG_PATH="/Users/luoxiaosheng/logs"
```
- 引入properties/yml文件
```
  <property resource="bootstrap.yml" />
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
  
#配置文件中
LOG_PATH: /Users/luoxiaosheng/logs/
```
- 引入的spring配置变量springProperty️（对比上一种不用引入配置文件，直接引入单个属性就行）
```
  <springProperty scope="context" name="LOG_PATH" source="log.path"></springProperty>
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
  
#配置文件application-*.yml中：
log:
  level: info
  path: /Users/luoxiaosheng/logs/
```

使用变量：${var}
```
<file>/${home}/myApp.log</file>
```
变量的默认值：
在引用一个变量时，如果该变量未定义，需要为其指定默认值，写法是：*${变量名:-默认值}*
```
  <springProperty scop="context" name="springAppName" source="spring.application.name"
    defaultValue=""/>
  <property name="LOG_HOME" value="${LOG_PATH:-.}/${springAppName}"/>
```