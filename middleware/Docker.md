# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Docker


可以使用一个images启动多个容器
镜像启动会启动容器
容器的修改可以更新或者新建一个容器

ctrl +p+q  退出，这样可以保证容器在后台一直运行
docker ps -a或-l	
无参数则是查询当前正在运行的容器  -a列出所有容器	-l列出最新创建的容器

docker inspect  id或name  对容器进行检查，返回其配置信息
这里可以查询容器的ip地址——IPAddress

docker run --name=容器名称 -i -t ubuntu /bin/bash  重新定义容器名

docker run -d 镜像名  启动守护式容器，可以在后台运行

docker start 重新启动已停止的容器

docker rm  容器名  删除已停止的容器

进入容器：
sudo docker attach 44fc0f0582d9  
但是它有一个缺点，只要这个连接终止，或者使用了exit命令，容器就会退出后台运行

使用docker exec命令  在运行中的容器内启动新的进程
这个命令使用exit命令后，不会退出后台，一般使用这个命令，使用方法如下
docker exec -it db3 /bin/sh 或者 docker exec -it d48b21a7e439 /bin/sh
注:这里可以在新启动的容器中启动nginx服务等

docker logs 容器名	查询容器日志
-f 跟踪日志的变化
-t 在返回的结果上加上时间戳
--tail 结尾处多少数量，默认all所有

docker top 查询容器内进程

停止容器
docker stop	发送命令，等待停止
docker kill		直接停止

二、安装

1、取消selinux，因为它会干扰lxc的正常功能

sudo vim /etc/selinux/config

SELINUX=disabled

SELINUXTYPE=targeted

2、配置Fedora EPEL 源

[root@YTX_18_93 ~]# sudo yum install http://ftp.riken.jp/Linux/fedora/epel/6/i386/epel-release-6-8.noarch.rpm

3、配置hop5.in源

[root@YTX_18_93 ~]# cd /etc/yum.repos.d

[root@YTX_18_93 yum.repos.d]# sudo wget http://www.hop5.in/yum/el6/hop5.repo

4、安装docker-io

[root@YTX_18_93 ~]# yum install docker-io

5、检查安装情况

[root@YTX_18_93 ~]# docker -h

6、启动docker

[root@YTX_18_93 ~]# service docker start

Starting docker:                                          [  OK  ]

——————————————————————————————————————————————
可使用以下命令，查看Docker是否安装成功：

docker version


如果输出看Docker的版本号，则说明安装成功了，可通过以下命令启动Docker服务：

service docker start
如果service命令启动不了用下面的
systemctl start docker.service
————————————————————————————————————————————————
docker下载的镜像存放地址
/var/lib/docker

下载的centos
Digest: sha256:7192ec204ee4b953a9c9212ebd78575a334d041333d8f58387aa648f72a7fd8a

docker ps -a：查看所有容器——获取CONTAINER ID
docker images：查看当前所有的镜像：

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
创建 Java Web 镜像
使用以下命令，根据某个“容器 ID”来创建一个新的“镜像”：
docker commit 57c312bbaad1 huangyong/javaweb:0.1
该容器的 ID 是“57c312bbaad1”，所创建的镜像名是“huangyong/javaweb:0.1”，随后可使用镜像来启动 Java Web 容器。
————————————————————————————————————————————
三，应用Docker

1，获取Centos镜像
 >docker pull centos:latest
 
2，查看镜像运行情况
 >docker images centos
 
3，在容器下运行 shell bash
 >docker run -i -t centos /bin/bash

docker run -p 80 -- name web -i -t centos /bin/bash	设置容器端口，命名web启动容器

只需使用以下命令即可启动容器：
docker run -i -t -v /root/software/:/mnt/software/ 25c5298b1a36 /bin/bash
这条命令比较长，我们稍微分解一下，其实包含以下三个部分：

docker run <相关参数> <镜像 ID> <初始命令>
其中，相关参数包括：

-i：表示以“交互模式”运行容器
-t：表示容器启动后会进入其命令行
-v：表示需要将本地哪个目录挂载到容器中，格式：-v <宿主机目录>:<容器目录>
假设我们的所有安装程序都放在了宿主机的/root/software/目录下，现在需要将其挂载到容器的/mnt/software/目录下。

 
4，停止容器
 >docker stop <CONTAINER ID>
 
5，查看容器日志
 >docker logs -f <CONTAINER ID>
 
6，删除所有容器
 >docker rm $(docker ps -a -q)
 
7，删除镜像
 >docker rmi <image id/name>
 
8，提交容器更改到镜像仓库中
 >docker run -i -t centos /bin/bash
 >useradd myuser
 >exit
 >docker ps -a |more
 >docker commit <CONTAINER ID> myuser/centos
 
9，创建并运行容器中的 hello.sh
 >docker run -i -t myuser/centos /bin/bash
 >touch /home/myuser/hello.sh
 >echo "echo \"Hello,World!\"" > /home/myuser/hello.sh
 >chmod +x /home/myuser/hello.sh
 >exit
 >docker commit <CONTAINER ID> myuser/centos
 >docker run -i -t myuser/centos /bin/sh /home/myuser/hello.sh

使用一下命令运行成功
docker run -i -p 58080:8080 javaweb:0.1 /bin/sh  /root/run.sh
 
10，在容器中运行Nginx
 
在容器中安装好Nginx，并提交到镜像中
 >docker run -t -i -p 80:80 nginx/centos /bin/bash
 启动Nginx
 >/data/apps/nginx/sbin/nginx
 (还不清楚如何在后台运行!!!)
 
在浏览器访问宿主机80端口。
 
11，映射容器端口
 >docker run -d -p 192.168.9.11:2201:22 nginx/centos /usr/sbin/sshd -D
 
用ssh root@192.168.9.11 -p 2201 连接容器，提示：
 
Connection to 192.168.1.205 closed.(此问题还未解决!!!)
 

可能会遇到的问题：
 ##在容器里面修改用户密码的时候报错：
 /usr/share/cracklib/pw_dict.pwd: No such file or directory


设置容器的端口映射：
docker run -p 80 -i -t ubuntu /bin/bash		指定容器端口
docker run -p 8080:80 -i -t ubuntu /bin/bash	指定宿主机和容器端口
docker run -p 0.0.0.0:80 -i -t ubuntu /bin/bash	指定ip和容器端口
docker run -p 0.0.0.0:8080:80 -i -t ubuntu /bin/bash	指定ip宿主机端口和容器端口



