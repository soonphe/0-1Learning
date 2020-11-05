# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Docker

### Docker的基本概念
Docker 包含三个基本概念，分别是
镜像（Image）
容器（Container）
仓库（Repository）

镜像是 Docker 运行容器的前提，仓库是存放镜像的场所，可见镜像更是Docker的核心。
可以使用一个images启动多个容器，镜像启动会启动容器，容器的修改可以更新或者新建一个容器

### docker基础命令
docker version:查看Docker版本
service docker start：如果service命令启动不了用下面的
启动docker：systemctl start docker
关闭Docker服务：systemctl stop docker

docker ps -a或-l：无参数则是查询当前正在运行的容器  -a列出所有容器	-l列出最新创建的容器
docker logs 容器名	查询容器日志
-f 跟踪日志的变化
-t 在返回的结果上加上时间戳
--tail 结尾处多少数量，默认all所有

docker top 查询容器内进程

### 1.Docker 镜像常用命令
搜索镜像：docker search java
下载镜像：docker pull java:8
docker pull nginx:1.17.0
列出镜像：docker images
删除镜像
• 指定名称删除镜像：docker rmi java:8
• 指定名称删除镜像（强制）：docker rmi -f java:8
• 强制删除所有镜像：docker rmi -f $(docker images)

### 2.Docker 容器常用命令

#### 新建并启动容器：docker run -p 80:80 --name nginx -d nginx:1.17.0
• -d选项：表示后台运行
• --name选项：指定运行后容器的名字为nginx,之后可以通过名字来操作容器
• -p选项：指定端口映射，格式为：hostPort:containerPort
docker start 重新启动已停止的容器

#### 列出容器
• 列出运行中的容器：docker ps
• 列出所有容器：docker ps -a

#### 停止容器
````
# $ContainerName及$ContainerId可以用docker ps命令查询出来
````
1. docker stop $ContainerName(或者$ContainerId)
2. 比如：docker stop nginx  或者  docker stop c5f5d5125587
强制停止容器：docker kill $ContainerName(或者$ContainerId)
启动已停止的容器：docker start $ContainerName(或者$ContainerId)

#### 进入容器
sudo docker attach 44fc0f0582d9  
但是它有一个缺点，只要这个连接终止，或者使用了exit命令，容器就会退出后台运行

• 先查询出容器的pid：
docker inspect --format "{{.State.Pid}}" $ContainerName(或者$ContainerId)
• 根据容器的pid进入容器：
5. nsenter --target "$pid" --mount --uts --ipc --net --pid

#### 删除容器
• 删除指定容器：docker rm $ContainerName(或者$ContainerId)
• 强制删除所有容器：docker rm -f $(docker ps -a -q)
查看容器的日志：docker logs $ContainerName(或者$ContainerId)
查看容器的IP地址：docker inspect --format '{{ .NetworkSettings.IPAddress }}' $ContainerName(或者$ContainerId)

#### 设置容器的端口映射：
docker run -p 80 -i -t ubuntu /bin/bash		指定容器端口
docker run -p 8080:80 -i -t ubuntu /bin/bash	指定宿主机和容器端口
docker run -p 0.0.0.0:80 -i -t ubuntu /bin/bash	指定ip和容器端口
docker run -p 0.0.0.0:8080:80 -i -t ubuntu /bin/bash	指定ip宿主机端口和容器端口

#### 同步宿主机时间到容器：docker cp /etc/localtime $ContainerName(或者$ContainerId):/etc/
在宿主机查看docker使用cpu、内存、网络、io情况
• 查看指定容器情况：docker stats $ContainerName(或者$ContainerId)
• 查看所有容器情况：docker stats -a
进入Docker容器内部的bash：docker exec -it $ContainerName /bin/bash

使用docker exec命令  在运行中的容器内启动新的进程
这个命令使用exit命令后，不会退出后台，一般使用这个命令，使用方法如下
docker exec -it db3 /bin/sh 或者 docker exec -it d48b21a7e439 /bin/sh
注:这里可以在新启动的容器中启动nginx服务等

#### 修改Docker镜像的存放位置
• 查看Docker镜像的存放位置：
docker info | grep "Docker Root Dir"
• 关闭Docker服务：systemctl stop docker
• 移动目录到目标路径：mv /var/lib/docker /mydata/docker：
建立软连接：ln -s /mydata/docker /var/lib/docker

镜像和容器的介绍：
https://www.cnblogs.com/light-train-union/p/11934309.html

### 使用Docker Compose的步骤
• 使用Dockerfile定义应用程序环境，一般需要修改初始镜像行为时才需要使用；
• 使用docker-compose.yml定义需要部署的应用程序服务，以便执行脚本一次性部署；
• 使用docker-compose up命令将所有应用服务一次性部署起来。

````
version: '3'
services:
# 指定服务名称
db:
# 指定服务使用的镜像
image: mysql:5.7
# 指定容器名称
container_name: mysql
# 指定服务运行的端口
ports:
- 3306:3306
# 指定容器中需要挂载的文件
volumes:
- /mydata/mysql/log:/var/log/mysql
- /mydata/mysql/data:/var/lib/mysql
- /mydata/mysql/conf:/etc/mysql
# 指定容器的环境变量
environment:
- MYSQL_ROOT_PASSWORD=root
# 指定服务名称
mall-tiny-docker-compose:
# 指定服务使用的镜像
image: mall-tiny/mall-tiny-docker-compose:0.0.1-SNAPSHOT
# 指定容器名称
container_name: mall-tiny-docker-compose
# 指定服务运行的端口
ports:
- 8080:8080
# 指定容器中需要挂载的文件
volumes:
- /etc/localtime:/etc/localtime
- /mydata/app/mall-tiny-docker-compose/logs:/var/logs
````





