# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## Prometheus
官网安装包下载：https://prometheus.io/download/
github地址：https://github.com/prometheus/prometheus

Prometheus基本原理是通过HTTP协议周期性抓取被监控组件的状态，这样做的好处是任意组件只要提供HTTP接口就可以接入监控系统，不需要任何SDK或者其他的集成过程。这样做非常适合虚拟化环境比如VM或者Docker 。
Prometheus应该是为数不多的适合Docker、Mesos、Kubernetes环境的监控系统之一。
输出被监控组件信息的HTTP接口被叫做exporter 。目前互联网公司常用的组件大部分都有exporter可以直接使用，比如Varnish、Haproxy、Nginx、MySQL、Linux 系统信息 (包括磁盘、内存、CPU、网络等等)，具体支持的源看：https://github.com/prometheus。

### mac使用brew安装：brew install prometheus
- 安装：brew install prometheus
- 启动：brew services start prometheus
- 重启：brew services restart prometheus
- 停止：brew services stop prometheus
- 启动-指定配置文件：prometheus --config.file=/usr/local/etc/prometheus.yml
```
默认情况下，Prometheus 将其数据库存储在 ./data（标志 --storage.tsdb.path）。如果要更改此目录，请使用 --storage.tsdb.path 标志。例如：
./prometheus --storage.tsdb.path=/tmp/tsdb
```

- prometheus控制台：http://localhost:9090
- 查看prometheus指标：http://localhost:9090/metrics
- 查看prometheus图表：http://localhost:9090/graph
- 查询prometheus实例：http://localhost:9090/targets

- **prometheus默认配置所在路径：/usr/local/etc/prometheus.yml**

### docker安装启动
```
docker pull prom/prometheus

docker run -p 9090:9090 -v /Users/xxx/path/to/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

### docker运行Prometheus+exporter+grafana+alertmanger
监控本机，只需要一个exporter：*node_exporter* – 用于机器系统数据收集
Grafana是一个开源的功能丰富的数据可视化平台，通常用于时序数据的可视化。它内置了prometheus等数据源的支持

1. 下载拉取镜像
   docker search prometheus
   docker pull prom/node-exporter
   docker pull prom/prometheus
   docker pull grafana/grafana

2. 启动node-exporter
```
docker run -d -p 9100:9100 \
--name="node-exporter" \
-v "/proc:/host/proc:ro" \
-v "/sys:/host/sys:ro" \
-v "/:/rootfs:ro" \
prom/node-exporter

```
3. 访问url
```
http://127.0.0.1:9100/metrics
```
界面上都是收集到数据，有了它就可以做数据展示了

4. 启动prometheus
   新建目录prometheus，编辑配置文件prometheus.yml
```
mkdir /opt/prometheus
cd /opt/prometheus/
vim prometheus.yml
```
prometheus.yml文件内容：
```
global:
  scrape_interval:     60s
  evaluation_interval: 60s
 
scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['localhost:9090']
        labels:
          instance: prometheus
 
  - job_name: linux
    static_configs:
      - targets: ['10.10.30.64:9100']
        labels:
          instance: localhost
```
启动prometheus
```
docker run  -d \
--name="prometheus" \
-p 9090:9090 \
-v /opt/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml  \
prom/prometheus

docker run -d --name="prometheus" -p 9090:9090 -v /Users/luoxiaosheng/path/to/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus

```
访问url：http://192.168.91.132:9090/graph
访问targets：http://192.168.91.132:9090/targets

5. 启动grafana
```
   mkdir /opt/grafana-storage
   chmod 777 -R /opt/grafana-storage
```
因为grafana用户会在这个目录写入文件，直接设置777，比较简单粗暴！

docker启动grafana
```
docker run -d \
--name="grafana" \
  -p 3000:3000 \
  --name=grafana \
  -v /opt/grafana-storage:/var/lib/grafana \
  grafana/grafana  
```

6. grafana添加数据源
   添加仪表盘
   查看
   node_load15  #系统15分钟的负载
   node_memory_MemTotal_bytes  #总内存
   node_cpu_seconds_total
   node_cpu_seconds_totalnode_filesystem_free_bytes
   node_filesystem_free_bytes
   prometheus_engine_query_duration_seconds #Prometheus的一些指标

**可以使用docker-compose安装，省去大量配置 ，这里推荐直接从gitee上克隆已经配置好的docker-compose文件**
```
git clone https://gitee.com/linge365/docker-prometheus.git
cd docker-prometheus
docker-compose up -d
当然以上这一步需要安装git,也可以直接访问https://gitee.com/linge365/docker-prometheus.git 下载对应的压缩包,解压后移动到/data目录下即可
```

### prometheus表达式语句
官网文档：https://prometheus.io/docs/prometheus/latest/querying/basics/
promhttp_metric_handler_requests_total：Prometheus 服务器已服务的请求总数
promhttp_metric_handler_requests_total{code="200"}： HTTP请求为200
count(promhttp_metric_handler_requests_total)：计算返回的时间序列的数量
rate(promhttp_metric_handler_requests_total{code="200"}[1m])：每秒 HTTP 请求率返回状态代码 200 的图表

在 Prometheus 的表达式语言中，表达式或子表达式可以计算为以下四种类型之一：
- 即时向量- 一组时间序列，每个时间序列包含一个样本，所有时间序列都共享相同的时间戳
- 范围向量- 一组时间序列，包含每个时间序列随时间变化的数据点范围
- 标量- 一个简单的数字浮点值
- String - 一个简单的字符串值；目前未使用


### Prometheus架构概览
![alt text](../static/middleware/prometheus.jpg "")
它的服务过程是这样的Prometheus daemon负责定时去目标上抓取metrics(指标) 数据，每个抓取目标需要暴露一个http服务的接口给它定时抓取。

- Prometheus：支持通过配置文件、文本文件、zookeeper、Consul、DNS SRV lookup等方式指定抓取目标。支持很多方式的图表可视化，例如十分精美的Grafana，自带的Promdash，以及自身提供的模版引擎等等，还提供HTTP API的查询方式，自定义所需要的输出。
- Alertmanager：是独立于Prometheus的一个组件，可以支持Prometheus的查询语句，提供十分灵活的报警方式。
- PushGateway：这个组件是支持Client主动推送metrics到PushGateway，而Prometheus只是定时去Gateway上抓取数据。

### prometheus数据模型
Prometheus从根本上所有的存储都是按时间序列去实现的，相同的metrics(指标名称) 和label(一个或多个标签) 组成一条时间序列，不同的label表示不同的时间序列。为了支持一些查询，有时还会临时产生一些时间序列存储。

metrics name&label指标名称和标签
每条时间序列是由唯一的”指标名称”和一组”标签（key=value）”的形式组成。
- 指标名称：一般是给监测对像起一名字，例如http_requests_total这样，它有一些命名规则，可以包字母数字_之类的的。通常是以应用名称开头_监测对像_数值类型_单位这样。例如：push_total、userlogin_mysql_duration_seconds、app_memory_usage_bytes。
- 标签：就是对一条时间序列不同维度的识别了，例如一个http请求用的是POST还是GET，它的endpoint是什么，这时候就要用标签去标记了。最终形成的标识便是这样了：http_requests_total{method=”POST”,endpoint=”/api/tracks”}。

记住，针对http_requests_total这个metrics name无论是增加标签还是删除标签都会形成一条新的时间序列。
查询语句就可以跟据上面标签的组合来查询聚合结果了。

如果以传统数据库的理解来看这条语句，则可以考虑http_requests_total是表名，标签是字段，而timestamp是主键，还有一个float64字段是值了。（Prometheus里面所有值都是按float64存储）。

### Prometheus四种数据类型
- Counter
Counter用于累计值，例如记录请求次数、任务完成数、错误发生次数。一直增加，不会减少。重启进程后，会被重置。
例如：http_response_total{method=”GET”,endpoint=”/api/tracks”} 100，10秒后抓取http_response_total{method=”GET”,endpoint=”/api/tracks”} 100。

- Gauge
Gauge常规数值，例如 温度变化、内存使用变化。可变大，可变小。重启进程后，会被重置。
例如： memory_usage_bytes{host=”master-01″} 100 < 抓取值、memory_usage_bytes{host=”master-01″} 30、memory_usage_bytes{host=”master-01″} 50、memory_usage_bytes{host=”master-01″} 80 < 抓取值。

- Histogram
Histogram（直方图）可以理解为柱状图的意思，常用于跟踪事件发生的规模，例如：请求耗时、响应大小。它特别之处是可以对记录的内容进行分组，提供count和sum全部值的功能。
例如：{小于10=5次，小于20=1次，小于30=2次}，count=7次，sum=7次的求和值。
  - Histogram 类型的样本会提供三种指标，分别是_count，sum，bucket
    - bucket:样本的值分布在 bucket 中的数量，命名为 _bucket{le="<上边界>"}，表示指标值小于等于上边界的所有样本数量
      - 例：在总共2次请求当中。http 请求响应时间 <=0.025 秒 的请求次数为0 io_namespace_http_requests_latency_seconds_histogram_bucket{path="/",method="GET",code="200",le="0.025",} 0.0
    - sum：所有样本值的大小总和，命名为 _sum。
      - 例：发生的2次 http 请求总的响应时间为 13.107670803000001 秒 io_namespace_http_requests_latency_seconds_histogram_sum{path="/",method="GET",code="200",} 13.107670803000001```
    - count：样本总数，命名为 _count。值和 _bucket{le="+Inf"} 相同。
      - 例：实际含义： 当前一共发生了 2 次 http 请求 io_namespace_http_requests_latency_seconds_histogram_count{path="/",method="GET",code="200",} 2.0
    
- Summary
Summary和Histogram十分相似，常用于跟踪事件发生的规模，例如：请求耗时、响应大小。同样提供 count 和 sum 全部值的功能。
例如：count=7次，sum=7次的值求值。 它提供一个quantiles的功能，可以按%比划分跟踪的结果。例如：quantile取值0.95，表示取采样值里面的95%数据。
  - < basename>样本值的分位数分布情况，命名为 {< basename>quantile="<φ>"}。 
    - 例：含义：这 12 次 http 请求中有 50% 的请求响应时间是 3.052404983s   io_namespace_http_requests_latency_seconds_summary{path="/",method="GET",code="200",quantile="0.5",} 3.052404983
  - _sum 所有样本值的大小总和，命名为 _sum。
    - 这12次 http 请求的总响应时间为 51.029495508s  io_namespace_http_requests_latency_seconds_summary_sum{path="/",method="GET",code="200",} 51.029495508
  - _count 样本总数，命名为 _count。
    - 例：含义：当前一共发生了 12 次 http 请求 io_namespace_http_requests_latency_seconds_summary_count{path="/",method="GET",code="200",} 12.0

**Histogram 与 Summary 的异同：**
它们都包含了 _sum 和 _count 指标
Histogram 需要通过 _bucket 来计算分位数，而 Summary 则直接存储了分位数的值。

### promethes.yml配置说明
```
#全局配置
global:
  scrape_interval:     15s # 设置抓取间隔，默认为1分钟
  evaluation_interval: 15s #估算规则的默认周期，每15秒计算一次规则。默认1分钟
  # scrape_timeout  #默认抓取超时，默认为10s

#Alertmanager相关配置
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - localhost:9093

#规则文件列表，使用'evaluation_interval' 参数去抓取
rule_files:
    #- "first_rules.yml"
    #- "second_rules.yml"
    - "mysql_rules.yml"

#远程写文件
remote_write:
  - url: http://remote1/push
    name: drop_expensive
    write_relabel_configs:
    - source_labels: [__name__]
      regex:         expensive.*
      action:        drop
    oauth2:
      client_id: "123"
      client_secret: "456"
      token_url: "http://remote1/auth"

  - url: http://remote2/push
    name: rw_tls
    tls_config:
      cert_file: valid_cert_file
      key_file: valid_key_file
    headers:
      name: value

# 远程读文件
remote_read:
  - url: http://remote1/read
    read_recent: true
    name: default
  - url: http://remote3/read
    read_recent: false
    name: read_special
    required_matchers:
      job: special
    tls_config:
      cert_file: valid_cert_file
      key_file: valid_key_file

#  抓取配置列表
scrape_configs:

  # prometheus抓取job
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']

  # mysql抓取job
  - job_name: mysql
    basic_auth:
      username: root
      password: "soonphe_1234"
    static_configs:
      - targets: ['192.168.160.178:3306']
        labels:
          instance: mysql
  # 抓取node-exporter
  - job_name: linux
    static_configs:
      - targets: ['127.0.0.1:9100']
        labels:
          instance: localhost
  # 抓取mysql-exporter
  - job_name: 'mysql'
    static_configs:
    - targets: ['localhost:9104']
```

### Prometheus ——exporter
exporter 导出系统中已经存在的指标。然后上传到Prometheus ，地址 https://prometheus.io/docs/instrumenting/exporters/
- mysql exporter 地址:https://github.com/prometheus/mysqld_exporter
- es exporter 地址：https://github.com/justwatchcom/elasticsearch_exporter
- rocketmq 地址：https://github.com/apache/rocketmq-exporter
- redis exporter 地址：https://github.com/oliver006/redis_exporter

>各种exporter 需要在服务器器上安装，按照安装⽂文档操作即可。

如安装mysql-exporter：
```
docker pull prom/mysqld-exporter

docker run -d -p 9104:9104 -v /Users/xxx/Downloads/my.cnf:/etc/mysql/my.cnf prom/mysqld-exporter  --config.my-cnf=/etc/mysql/my.cnf

my.cnf文件内容：
[client]
host = 127.0.0.1
port = 3306
user = root
password = 123456

prometheus添加指标监控：
scrape_configs:
  - job_name: 'mysql'
    static_configs:
    - targets: ['localhost:9104']
```

### prometheus报警规则配置
- 官网地址：https://awesome-prometheus-alerts.grep.to/
- github地址：https://github.com/samber/awesome-prometheus-alerts

**示例：配置mysql报警**
```
# prometheus.yml 的相关部分
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - "alertmanager:9093"
      
rule_files:
  - "alert_rules.yml"
 
# alert_rules.yml 的规则
groups:
- name: example
  rules:
  # 对任何实例超过30秒无法联系的情况发出警报
  - alert: 服务告警
    expr: up == 0
    for: 30s
    labels:
      severity: critical
    annotations:
      summary: "服务异常,实例:{{ $labels.instance }}"
      description: "{{ $labels.job }} 服务已关闭"
  # 当job="myjob"的请求的5分钟平均延迟超过0.5秒时，该规则会触发。告警将保持激活状态10分钟，并且会发送到配置的Alertmanager。
  - alert: HighErrorRate
    expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
    for: 10m
    labels:
      severity: page
    annotations:
      summary: High request latency
      description: Description of the problem.\nFurther details at [link](http://my.link)
  - alert: MysqlDown
    expr: mysql_up == 0
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: MySQL down (instance {{ $labels.instance }})
      description: "MySQL instance is down on {{ $labels.instance }}\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```
在告警规则文件中,我们可以将一组相关的规则设置定义在group下.在每一个group中我们可以定义多个告警规则(rule).一条告警规则主要由以下几部分组成:
- alert: 告警规则的名称
- expr: 基于PromQL表达式告警触发条件,用于计算是否有时间序列满足该条件
- for: 评估等待时间,可选参数.用于表示只有当前触发条件持续一段时间后在发送告警.在等待时间新产生的告警的状态为pending
- labels: 自定义标签,允许用户指定要附加到告警上的一组附加标签
- annotations: 用于指定一组附加信息,比如用于描述告警详情信息的文字等,annotations的内容在告警产生时会作为参数发送到Alertmanager

**重新加载配置**
curl -X POST http://localhost:9090/-/reload

请求接口后返回 Lifecycle API is not enabled. 那么就是启动的时候没有开启热更新配置，需要在启动的命令行增加参数： --web.enable-lifecycle
./prometheus --web.enable-lifecycle --config.file=prometheus.yml

**查看告警状态**
重启Prometheus后,用户可以通过Prometheus WEB界面中Alerts菜单查看当前Prometheus下的所有告警规则,以及当前所处的活动状态.

同时对于以及pending或者firing的告警,Prometheus也会将它们存储到时间序列ALERTS{}中.

可以通过表达式,查询告警实例:AlERTS{}

测试告警规则 在主机上运行以下命令 docker stop node-exporter

Prometheus首次检测到满足触发条件后,由于告警规则中设置了1分钟(for: 1m)的等待时间,告警状态由INACTIVE变为Pending,如下图所示:

如果1分钟后告警条件持续满足,告警转台从Pending变为FIRING,并且会把告警信息发送给Alertmanager.如下图所示:


#### prometheus告警规则示例
使用groups聚合，可以配置多个alert
```
groups:
- name: MySQLStatsAlert
  rules:
  - alert: MysqlDown
  - alert: MysqlXxx
```

2.1.1. MySQL down
MySQL instance is down on {{ $labels.instance }}
```
  - alert: MysqlDown
    expr: mysql_up == 0
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: MySQL down (instance {{ $labels.instance }})
      description: "MySQL instance is down on {{ $labels.instance }}\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"


```

2.1.2. MySQL too many connections (> 80%)
More than 80% of MySQL connections are in use on {{ $labels.instance }}
```
  - alert: MysqlTooManyConnections(>80%)
    expr: avg by (instance) (rate(mysql_global_status_threads_connected[1m])) / avg by (instance) (mysql_global_variables_max_connections) * 100 > 80
    for: 2m
    labels:
      severity: warning
    annotations:
      summary: MySQL too many connections (> 80%) (instance {{ $labels.instance }})
      description: "More than 80% of MySQL connections are in use on {{ $labels.instance }}\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

2.1.3. MySQL high threads running
More than 60% of MySQL connections are in running state on {{ $labels.instance }}
```
  - alert: MysqlHighThreadsRunning
    expr: avg by (instance) (rate(mysql_global_status_threads_running[1m])) / avg by (instance) (mysql_global_variables_max_connections) * 100 > 60
    for: 2m
    labels:
      severity: warning
    annotations:
      summary: MySQL high threads running (instance {{ $labels.instance }})
      description: "More than 60% of MySQL connections are in running state on {{ $labels.instance }}\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

### prometheus告警Alertmanager
Alertmanager是一个独立的告警模块，接收Prometheus等客户端发来的警报，之后通过分组、删除重复等处理，并将它们通过路由发送给正确的接收器。

- 下载地址：https://github.com/prometheus/alertmanager
- docker命令: docker pull prom/alertmanager

Prometheus的警报分为两个部分。Prometheus服务器中的警报规则将警报发送到Alertmanager。该Alertmanager 然后管理这些警报，包括沉默，抑制，聚集和通过的方法，如电子邮件发出通知，对呼叫通知系统，以及即时通讯平台。
设置警报和通知的主要步骤：
1) 设置并配置Alertmanager；
2) 配置Prometheus对Alertmanager访问；
3) 在普罗米修斯创建警报规则；
   
Alert的三种状态：
1) pending：警报被激活，但是低于配置的持续时间。这里的持续时间即rule里的FOR字段设置的时间，该状态下不发送报警。
2) firing：警报已被激活，而且超出设置的持续时间。该状态下发送报警。
3) inactive：既不是pending也不是firing的时候状态变为inactive。

Prometheus中的告警规则允许你基于PromQL表达式定义告警触发条件,Prometheus后端对这些触发规则进行周期性计算,当1满足触发条件后则会触发告警通知.默认情况下,用户可以通过Prometheus的Web界面查看这些告警规则以及告警的触发状态.当Prometheus与Alertmanager关联后,可以将告警发送到外部服务可以对这些告警进行进一步的处理.

Alertmanager主要负责对Prometheus产生的告警进行统一处理,因此在Alertmanager配置中一般会包含以下几个主要部分:
- 全局配置(global) : 用于定义一些全局的公共参数,如全局的SMTP配置,Slack配置等内容;
- 模板(templates) : 用于定义告警通知时的模板,如HTML模板,邮件模板等;
- 告警路由(route) : 根据标签匹配,确定当前告警应该如何处理;
- 接收人(receivers) : 接收人是一个抽象的概念,它可以是一个邮箱也可以是微信,Slack或者Webhook等,接收人一般告警路由使用;
- 抑制规则(inhibit_rules) : 合理设置抑制规则可以减少垃圾告警的产生

config.yml
```
global:
  #163服务器
  smtp_smarthost: 'smtp.163.com:465'
  #发邮件的邮箱
  smtp_from: 'cdring@163.com'
  #发邮件的邮箱用户名，也就是你的邮箱　　　　　
  smtp_auth_username: 'cdring@163.com'
  #发邮件的邮箱密码
  smtp_auth_password: 'your-password'
  #进行tls验证
  smtp_require_tls: false

route:
  group_by: ['alertname']
  # 当收到告警的时候，等待group_wait配置的时间，看是否还有告警，如果有就一起发出去
  group_wait: 10s
  #  如果上次告警信息发送成功，此时又来了一个新的告警数据，则需要等待group_interval配置的时间才可以发送出去
  group_interval: 10s
  # 如果上次告警信息发送成功，且问题没有解决，则等待 repeat_interval配置的时间再次发送告警数据
  repeat_interval: 10m
  # 全局报警组，这个参数是必选的
  receiver: email

receivers:
- name: 'email'
  #收邮件的邮箱
  email_configs:
  - to: 'cdring@163.com'
inhibit_rules:
 - source_match:
     severity: 'critical'
   target_match:
     severity: 'warning'
   equal: ['alertname', 'dev', 'instance']

```

如果邮箱报错：
```
当传入发送邮箱正确的用户名和密码时，总是收到到：550 User has no permission这样的错误，

其实我们用Java发送邮件时相当于自定义客户端根据用户名和密码进行登录，然后使用SMTP服务发送邮件。但新注册的163邮件默认是不开启客户端授权验证的（对自定的邮箱大师客户端默认开启），

因此登录总是会被拒绝，验证没有权限。解决办法是进入163邮箱，进入邮箱中心——客户端授权密码，选择开启即可，如下截图

设置完毕后，在代码中用使用客户端授权密码代替原始的邮箱密码，这样就可以正确的发送邮件了。

QQ邮箱默认也是关闭服务的，也要在设置-账户里设置开启服务即可！
```

### prometheus监控java性能（springboot） 
1、依赖
```
        <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
                <version>2.5.9</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
                <version>2.5.9</version>
            </dependency>
            <dependency>
                <groupId>io.micrometer</groupId>
                <artifactId>micrometer-registry-prometheus</artifactId>
                <version>1.12.5</version>
            </dependency>
```

2、在项目启动主类添加如下来监控JVM
```
 @Bean
    MeterRegistryCustomizer<MeterRegistry> configurer(@Value("${spring.application.name}") String applicationName){
        return registry -> registry.config().commonTags("application", applicationName);
    }
```
3、项目配置文件
```
management:
  server:
    port: 8082
  endpoints:
    web:
      exposure:
        include: '*'
  metrics:
    tags:
      application: ${spring.application.name}
```
4、prometheus配置文件
```
job_name: "test_admin"
    metrics_path: /actuator/prometheus
    scrape_interval: 5s
    basic_auth: # Spring Security basic auth
       username: 'actuator'
       password: 'actuator'
    static_configs:
      - targets: ["localhost:8082"]
```

启动项目
- 查看项目相关端点：http://localhost:8082/actuator
- 查看项目prometheus指标：http://localhost:8082/actuator/prometheus


### 深入了解management.endpoints

management.endpoints是Spring Boot中的一个关键特性，它为应用程序提供了一组最终用户可以使用的接口。这些接口可以帮助管理员和运维团队查找应用程序的错误和诊断问题，还可以提供统计数据和度量。在本文中，我们将从以下几个方面深入了解management.endpoints。

一、management.endpoints的介绍
在Spring Boot应用中，management.endpoints是一个RESTful的端点，它提供了管理应用程序的接口。从Spring Boot 2.x开始，management.endpoints默认情况下是开启的，你可以在application.properties文件中通过配置"management.endpoints.enabled=false"来关闭它。

management.endpoints的API接口包括健康检查、审计数据、度量指标、配置属性、线程转储、http追踪、应用程序信息等。

二、管理界面
Spring Boot提供了一个内置的管理界面，称为Actuator，它是一个RESTful应用程序。Actuator可以通过http://localhost:8080/actuator访问，默认情况下，只有/health和/info两个接口是开放的。

Actuator通过配置文件来定义需要使用哪些端点。例如，添加如下代码可以激活所有的端点：

management.endpoints.web.exposure.include=*
然后通过http://localhost:8080/actuator访问，我们可以看到Actuator管理页面提供的所有端点。同时Actuator还提供了各种内置的监控工具，例如追踪HTTP请求、审计记录、内存数据等。

三、定制度量指标
度量指标可以帮助开发人员了解应用程序的性能和健康状况。在Spring Boot中，我们可以使用Micrometer来实现度量指标。

例如，下面的代码将为应用程序添加一个计数器，它将跟踪页面访问的数量：
```
@Component
public class PageViewMetrics {
private final Counter pageViewCounter;

    public PageViewMetrics(MeterRegistry registry) {
        this.pageViewCounter = Counter.builder("page.view")
                .description("Total Page Views")
                .register(registry);
    }

    public void incr() {
        this.pageViewCounter.increment();
    }
}
```
在上面的代码中，我们定义了一个名为page.view的计数器，并将它注册到Spring Boot的度量指标图表中。在应用程序中调用上述incr()方法时，计数器将递增并更新统计信息。

四、开发自定义端点
如果你需要更多的管理功能，Spring Boot允许你自定义端点。开发自己的端点可以添加新的管理功能，例如执行自定义命令或调用远程系统。

步骤如下：

1. 定义端点代码：
```
@Endpoint(id = "example")
public class ExampleEndpoint {

    @ReadOperation
    public String example() {
        return "This is an example of an endpoint";
    }

    @WriteOperation
    public void doSomething() {
        // Perform an action
    }
}
```
在上面的代码中，我们定义了一个名为example的端点，并在其中包含了两个操作：example和doSomething。example方法返回一个字符串，而doSomething方法可以执行一些操作（例如，那些与远程系统的交互）。

2. 开启端点：

management.endpoints.web.exposure.include=example
通过以上配置开启端点，然后就可以通过http://localhost:8080/actuator/example路径来访问了。

五、管理端点安全保护
在生产环境中，将端点暴露给公共网络是非常危险的。Spring Boot允许你配置端点的安全保护措施，以确保只有授权用户可以访问。

下面的配置将使用Spring Security授权用户名和密码：
```
management.endpoint.health.show-details=always
management.endpoint.metrics.enabled=true
management.endpoint.logfile.enabled=true
management.endpoints.web.exposure.include=health,metrics,logfile
spring.security.user.name=admin
spring.security.user.password=admin
```
在以上配置中，我们只将health、metrics、logfile端点暴露给公共网络，还使用Spring Security对这些端点进行安全保护。

六、结语
management.endpoints是Spring Boot中一个非常重要的特性，它提供了一些关键的管理功能以便于监控和诊断问题。在本文中，我们从不同的角度来深入了解management.endpoints，包括介绍、管理界面、定制度量指标、开发自定义端点、以及管理端点安全保护。

### prometheus自定义metrics（失败）
创建自定义Prometheus Metrics，你可以使用simpleclient_hotspot库，它是Prometheus SimpleClient for Java的一部分,展示了如何创建一个自定义的计数器（Counter）。
```
<dependencies>
    <dependency>
        <groupId>io.prometheus</groupId>
        <artifactId>simpleclient_hotspot</artifactId>
        <version>0.8.1</version>
    </dependency>
    <dependency>
        <groupId>io.prometheus</groupId>
        <artifactId>simpleclient_servlet</artifactId>
        <version>0.8.1</version>
    </dependency>
</dependencies>
```

然后，创建一个自定义的Metric：
```
import io.prometheus.client.Counter;
import io.prometheus.client.hotspot.DefaultExports;
 
public class MetricsService {
 
    static final Counter requestCounter = Counter.build()
            .name("custom_request_total")
            .labelNames("method")
            .help("Total requests.").register();
 
    public static void recordRequest(String method) {
        requestCounter.labels(method).inc();
    }
 
    public static void main(String[] args) {
        // 添加默认的Hotspot指标
        DefaultExports.initialize();
 
        // 模拟请求
        for (int i = 0; i < 10; i++) {
            recordRequest("GET");
        }
 
        // 让JVM退出
        try {
            Thread.sleep(30000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
我们创建了一个名为custom_request_total的计数器，它有一个标签method。在recordRequest方法中，我们通过调用.inc()方法增加计数器的值。

最后，你需要确保启动了一个HTTP服务器来暴露Prometheus端点，这通常是通过simpleclient_servlet库实现的。
```
import io.prometheus.client.exporter.HTTPServer;
 
public class MetricsServer {
    public static void main(String[] args) {
        HTTPServer server = new HTTPServer(8080);
    }
}
```
启动你的应用程序，然后访问http://localhost:8080/metrics，你将看到所有的metrics，包括你自定义的custom_request_total。


