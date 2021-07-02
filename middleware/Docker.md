# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Docker
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

### Docker的基本概念
Docker 包含三个基本概念，分别是
镜像（Image）
容器（Container）
仓库（Repository）

镜像是 Docker 运行容器的前提，仓库是存放镜像的场所，可见镜像更是Docker的核心。
可以使用一个images启动多个容器，镜像启动会启动容器，容器的修改可以更新或者新建一个容器

### docker环境安装
1.安装yum-utils：
yum install -y yum-utils device-mapper-persistent-data lvm2

2.为yum源添加docker仓库位置：
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

3.安装docker:
yum install docker-ce

4.启动docker:
systemctl start docker


### 1.Docker 镜像常用命令
service docker start：如果service命令启动不了用下面的
启动docker：systemctl start docker
关闭Docker服务：systemctl stop docker
查看Docker版本：docker version

搜索镜像：docker search java
下载镜像：docker pull java:8(docker pull nginx:1.17.0)
列出镜像：docker images
删除镜像
• 指定名称删除镜像：docker rmi java:8
• 指定名称删除镜像（强制）：docker rmi -f java:8
• 强制删除所有镜像：docker rmi -f $(docker images)

docker ps -a或-l：无参数则是查询当前正在运行的容器  -a列出所有容器	-l列出最新创建的容器
docker logs 容器名	查询容器日志
-f 跟踪日志的变化
-t 在返回的结果上加上时间戳
--tail 结尾处多少数量，默认all所有
查询容器内进程:docker top 

### 2.Docker 容器常用命令

#### 新建并启动容器：docker run -p 80:80 --name nginx -d nginx:1.17.0
• -p选项：指定端口映射，格式为：hostPort:containerPort
• --name选项：指定运行后容器的名字为nginx,之后可以通过名字来操作容器
• -d选项：表示后台运行

#### 列出容器
• 列出运行中的容器：docker ps
• 列出所有容器：docker ps -a

#### 启动、停止容器
启动容器(已存在的容器)：docker start $ContainerName(或者$ContainerId)
停止容器：
1. docker stop $ContainerName(或者$ContainerId)（$ContainerName及$ContainerId可以用docker ps命令查询出来）
2. 比如：docker stop nginx  或者  docker stop c5f5d5125587
强制停止容器：docker kill $ContainerName(或者$ContainerId)
启动已停止的容器：docker start $ContainerName(或者$ContainerId)

#### 进入容器
sudo docker attach 44fc0f0582d9  
但是它有一个缺点，只要这个连接终止，或者使用了exit命令，容器就会退出后台运行

• 先查询出容器的pid：
docker inspect --format "{{.State.Pid}}" $ContainerName(或者$ContainerId)
• 根据容器的pid进入容器：
nsenter --target "$pid" --mount --uts --ipc --net --pid

#### 删除容器
• 删除指定容器：docker rm $ContainerName(或者$ContainerId)
• 强制删除所有容器：docker rm -f $(docker ps -a -q)
查看容器的日志：docker logs $ContainerName(或者$ContainerId)
查看容器的IP地址：docker inspect --format '{{ .NetworkSettings.IPAddress }}' $ContainerName(或者$ContainerId)

#### 设置容器端口映射：
docker run -p 80 -i -t ubuntu /bin/bash		指定容器端口
docker run -p 8080:80 -i -t ubuntu /bin/bash	指定宿主机和容器端口
docker run -p 0.0.0.0:80 -i -t ubuntu /bin/bash	指定ip和容器端口
docker run -p 0.0.0.0:8080:80 -i -t ubuntu /bin/bash	指定ip宿主机端口和容器端口

#### 同步宿主机时间到容器：
docker cp /etc/localtime $ContainerName(或者$ContainerId):/etc/

#### 在宿主机查看docker使用cpu、内存、网络、io情况
• 查看指定容器情况：docker stats $ContainerName(或者$ContainerId)
• 查看所有容器情况：docker stats -a

#### 进入Docker容器内部的bash：
docker exec -it $ContainerName /bin/bash

#### 修改Docker镜像的存放位置
• 查看Docker镜像的存放位置：
docker info | grep "Docker Root Dir"
• 关闭Docker服务：systemctl stop docker
• 移动目录到目标路径：mv /var/lib/docker /mydata/docker：
建立软连接：ln -s /mydata/docker /var/lib/docker

### 构建镜像
我们使用命令 docker build ， 从零开始来创建一个新的镜像。
为此，我们需要创建一个 Dockerfile 文件，其中包含一组指令来告诉 Docker 如何构建我们的镜像。
```
FROM    centos:6.7
MAINTAINER      Fisher "fisher@sudops.com"

RUN     /bin/echo 'root:123456' |chpasswd
RUN     useradd runoob
RUN     /bin/echo 'runoob:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
```
每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
第一条FROM，指定使用哪个镜像源
RUN 指令告诉docker 在镜像内执行命令，安装了什么。。。
然后，我们使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像。

docker build -t runoob/centos:6.7 .
-t ：指定要创建的目标镜像名
. ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

### 镜像和容器的介绍：
https://www.cnblogs.com/light-train-union/p/11934309.html


