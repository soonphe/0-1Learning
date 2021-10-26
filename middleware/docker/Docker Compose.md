# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Docker Compose
Docker Compose是一个用于定义和运行多个docker容器应用的工具。
使用Compose你可以用YAML文件来配置你的应用服务，然后使用一个命令，你就可以部署你配置的所有服务了。

### 安装
Docker Compose 包含在适用于 Windows 和 macOS 的 Docker 桌面中。

查看docker-compose版本：
docker-compose --version

如果没有则手动安装：
```
pip install docker-compose
```
Note: Docker Compose requires Python 3.6 or later.

### 使用Docker Compose的步骤
使用 Docker Compose 基本上是一个三步过程：
• 使用 Dockerfile 定义应用程序的环境，以便它可以在任何地方复制。
• 在 docker-compose.yml 中定义组成您的应用程序的服务，以便它们可以在隔离的环境中一起运行。
• 最后，运行 docker-compose up，Compose 将启动并运行您的整个应用程序。
说明：
Docker Compose将所管理的容器分为三层，工程、服务及容器。
docker-compose.yml中定义所有服务组成了一个工程，services节点下即为服务，服务之下为容器。
容器与容器直之间可以以服务名称为域名进行访问，比如在xxx-compose服务中可以通过jdbc:mysql://db:3306这个地址来访问db这个mysql服务。

Compose 文件示例：
```
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
  redis:
    image: redis
```

### docker-compose.yml结构
docker-compose.yml结构树
```
version: '3.8' --  docker-compose.yml版本
services: -- 服务
  elasticsearch: -- 服务名
    image: elasticsearch:7.8.0    -- 镜像名
    container_name: es    -- 容器名
    environment:    -- 环境
      discovery.type: single-node
      ES_JAVA_OPTS: "-Xms512m -Xmx512m"
    ports:    -- 端口
      - "9200:9200"
      - "9300:9300"
    networks -- 内部网络
      - elastic
    logstash:    -- logstash容器
    kibana: -- kibana容器
networks: -- 公用网络，设定局域网
  elastic:  -- 网络别名
    driver: bridge
```

详细说明：
```

image: mysql:5.7 指定运行的镜像名称：运行的是mysql5.7的镜像
container_name: mysql 配置容器名称容器名称为mysql）
ports:
  - 3306:3306 端口映射（HOST:CONTAINER）将宿主机的3306端口映射到容器的3306端口）
volumes:    将宿主机的文件或目录（HOST:CONTAINER）挂载到mysql容器中
  -/mydata/mysql/log:/var/log/mysql
  -/mydata/mysql/data:/var/lib/mysql
  -/mydata/mysql/conf:/etc/mysql
environment:    配置环境变量
  - MYSQL_ROOT_PASSWORD=root（设置mysqlroot帐号密码的环境变量）
links:
  - db:database 连接其他容器的服务（SERVICE:ALIAS）（可以以database为域名访问服务名称为db的容器）
```
  
### Docker Compose常用命令
docker-compose up -d：构建、创建、启动相关容器(# -d表示在后台运行)
docker-compose -f docker-compose.yml up -d  指定docker-compose.yml文件运行
docker-compose -f docker-compose.yml stop   停止服务
docker-compose -f docker-compose.yml down   停止并删除服务

docker-compose stop：停止所有相关容器
docker-compose ps：列出所有容器信息
docker-compose down：停止并移除容器

docker-compose stop nginx                    停止nignx容器
docker-compose start nginx                    启动nignx容器
docker-compose restart nginx： 重新启动nginx容器
docker-compose exec nginx bash            登录到nginx容器中
docker-compose build nginx                     构建镜像   
docker-compose logs  nginx                     查看nginx的日志 
docker-compose logs -f nginx                   查看nginx的实时日志

### Docker Compose安装ELK
参考网址：https://github.com/docker/awesome-compose

Docker-Compose使用示例：
ELK示例：https://github.com/deviantony/docker-elk

docker-compose-elk.yml文件
```
version: '3.8'

services:
  elasticsearch:
    image: elasticsearch:7.8.0
    container_name: es
    environment:
      discovery.type: single-node
      ES_JAVA_OPTS: "-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
      - "9300:9300"
    healthcheck:
      test: ["CMD-SHELL", "curl --silent --fail localhost:9200/_cluster/health || exit 1"]
      interval: 10s
      timeout: 10s
      retries: 3
    networks:
      - elastic
  logstash:
    image: logstash:7.8.0
    container_name: log
    environment:
      discovery.seed_hosts: logstash
      LS_JAVA_OPTS: "-Xms512m -Xmx512m"
    volumes:
      - ./logstash/pipeline/logstash-nginx.config:/usr/share/logstash/pipeline/logstash-nginx.config
      - ./logstash/nginx.log:/home/nginx.log
    ports:
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "5044:5044"
    depends_on:
      - elasticsearch
    networks:
      - elastic
    command: logstash -f /usr/share/logstash/pipeline/logstash-nginx.config
  kibana:
    image: kibana:7.8.0
    container_name: kib
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
    networks:
      - elastic
networks:
  elastic:
    driver: bridge

```
指定文件执行：docker-compose -f docker-compose-elk.yml up -d

### logstach使用说明：
官网地址：https://www.elastic.co/guide/en/logstash-versioned-plugins/current/index.html
特别说明：logstach需要配置pipline的配置项
    * input选择输入方式，
    * output选择services中配置的elasticsearch即可
参考文档：https://blog.csdn.net/qq330983778/article/details/105644835/

pipline配置文件位置：/logstash/pipeline/logstash-nginx.config/nginx.config
```
input {
  # 从控制台中输入来源
  stdin { } 
  # 端口监听
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 5000
    codec => json_lines
  }
  # 从文件中来
  file {
    #单一文件
    path => "/Users/luoxiaosheng/docker-compose/logstash/nginx.log/*" 

    #排除不想监听的文件
    exclude => "1.log"
    #设置新事件的标志
    delimiter => "\n"
    #设置多长时间扫描目录，发现新文件
    discover_interval => 15
    #设置多长时间检测文件是否修改
    stat_interval => 1

    #监听文件的起始位置，默认是end
    start_position => beginning

    #监听文件读取信息记录的位置
    #sincedb_path => "/Users/luoxiaosheng/docker-compose/logstash/test.txt"
    #设置多长时间会写入读取的位置信息
    #sincedb_write_interval => 15
  }
}
output {
  elasticsearch {
    hosts => "elasticsearch:9200"
    index => "logstash-%{+YYYY.MM.dd}"
  }
}
```

日志文件位置：/Users/luoxiaosheng/docker-compose/logstash/nginx.log/nginx.log文件
```
[{
	"id": 1,
	"name": "John",
	"age": 30,
	"class": 1
}, {
	"id": 2,
	"name": "JJJ",
	"age": 30,
	"class": 1
}]
```

logstach发送文件命令：
```
cat /Users/luoxiaosheng/docker-compose/logstash/nginx.log/nginx.log   | nc -c 127.0.0.1 5000
```
找不到nc（netcat）命令解决：yum install -y nc
