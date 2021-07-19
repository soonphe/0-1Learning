# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Prometheus

### prometheus安装使用：(记录和报警、可视化)
官网安装包下载：https://prometheus.io/download/
github地址：https://github.com/prometheus/prometheus


### 1.下载并解压安装包（也可以使用docker）
tar xvfz prometheus-*.tar.gz
cd prometheus-*


docker search prometheus
docker pull prom/prometheus


### 2.promethes.yml配置
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
      # - alertmanager:9093

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
      password: "Sgcc_1234"
    static_configs:
      - targets: ['192.168.160.178:3306']
        labels:
          instance: mysql
```

### 3、创建prometheus的用户及数据存储目录（不是必须）
为了安全，使用普通用户来启动prometheus服务。作为一个时序型的数据库产品，prometheus的数据默认会存放在应用所在目录下。
```
	• 创建用户，并指定家目录
[root@prometheus /]# groupadd prometheus
[root@prometheus /]# useradd -g prometheus -m -d /var/lib/prometheus -s /sbin/nologin prometheus
	• 创建数据目录
[root@prometheus ~]# mkdir /export/prometheus/data -p
	• 修改目录属主
[root@prometheus /]# chown prometheus.prometheus -R /usr/local/prometheus
```

### 4、启动prometheus（默认端口9090）
```
前台启动
[root@prometheus ~]# cd /usr/local/prometheus/
[root@prometheus prometheus]# ./prometheus
后台启动
[root@prometheus prometheus]# nohup ./prometheus &
配置文件启动
./prometheus --config.file=prometheus.yml
```
默认情况下，Prometheus 将其数据库存储在 ./data（标志 --storage.tsdb.path）。

docker启动：
```
docker run -p 9090:9090 -v /Users/luoxiaosheng/path/to/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```
查看prometheus指标：http://localhost:9090/metrics
查看prometheus图表：http://localhost:9090/graph
查询prometheus实例：http://localhost:9090/targets

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

### 保存热加载prometheus：
curl  -XPOST localhost:9090/-/reload


### prometheus报警、规则配置案例
官网地址：https://awesome-prometheus-alerts.grep.to/
github地址：https://github.com/samber/awesome-prometheus-alerts

示例：
配置mysql报警

可以使用groups聚合
```
groups:
- name: MySQLStatsAlert
  rules:
  - alert: MysqlDown
  。。。

```

2.1.1. MySQL down
MySQL instance is down on {{ $labels.instance }}[copy]
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
