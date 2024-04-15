# 0-1Learning

![alt ](../static/common/svg/luoxiaosheng.svg "公众号")
![alt ](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt ](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## clickhouse

ClickHouse 是俄罗斯的Yandex(类似于百度等在我们国家的地位)于2016年开源的列式存储数据库（DBMS），主要用于在线分析处理查询（OLAP），能够使用SQL查询实时生成分析数据报告(优势:快))。

### 使用docker安装clickhouse

- 拉取clickhouse-server镜像
  docker pull yandex/clickhouse-server

- 启动临时容器，目的：拷贝容器内配置文件
  docker run -d --rm --name=temp yandex/clickhouse-server

// -d 后台运行
// --rm 启动临时容器，当容器停掉后，容器自动删除
// --name 容器名称

宿主机创建目录，用于存放配置文件、数据、日志（我是放在/usr/local/clickhouse下）
sudo mkdir -p /usr/local/clickhouse/conf /usr/local/clickhouse/data /usr/local/clickhouse/log

进入临时容器：
docker exec -it temp /bin/bash

将容器内配置文件拷贝到宿主机
docker cp temp:/etc/clickhouse-server/users.xml /usr/local/clickhouse/conf/users.xml
docker cp temp:/etc/clickhouse-server/config.xml /usr/local/clickhouse/conf/config.xml

直接启动命令：

```
docker run -d --name docker-clickhouse --ulimit nofile=262144:262144 -p 8123:8123 -p 9000:9000 -p 9009:9009 -v /Users/xxx/clickhouse/conf/config.xml:/etc/clickhouse-server/config.xml yandex/clickhouse-server
```

默认是没有账户和密码，直接连接就可以使用

### 修改连接用户名、密码（users.xml）

1.执行命令，生成SHA256密码

PASSWORD=$(base64 < /dev/urandom | head -c8); echo "$PASSWORD"; echo -n "$PASSWORD" | sha256sum | tr -d '-'

2.返回结果

XwCoKBgV  #密码明文

2c297a5ee6d922c0472dee50d3067ea1ce99dd54e765247e287f9ca262525a63  #密文

3.修改users.xml配置文件

```
<users>

    <root>          <password_sha256_hex>2c297a5ee6d922c0472dee50d3067ea1ce99dd54e765247e287f9ca262525a63</password_sha256_hex>

        <networks>

            <ip>::/0</ip>

        </networks>

        <profile>default</profile>

        <quota>default</quota>

    </root>

</users>

启动clickhouse容器
docker run -d --name clickhouse-server \
-p 8123:8123 \
-p 9009:9009 \
-p 9090:9000 \
--ulimit nofile=262144:262144 \
--volume=/usr/local/clickhouse/data:/var/lib/clickhouse \
--volume=/usr/local/clickhouse/log:/var/log/clickhouse-server \
--volume=/usr/local/clickhouse/conf/config.xml:/etc/clickhouse-server/config.xml \
--volume=/usr/local/clickhouse/conf/users.xml:/etc/clickhouse-server/users.xml \ yandex/clickhouse-server
```

到这里就安装成功了，可以用dbeaver连接使用了