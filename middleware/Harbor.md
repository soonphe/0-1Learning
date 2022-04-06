# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Harbor

###一、Harbor介绍
Docker容器应用的开发和运行离不开可靠的镜像管理，虽然Docker官方也提供了公共的镜像仓库，但是从安全和效率等方面考虑，部署私有环境内的Registry也是非常必要的。Harbor是由VMware公司开源的企业级的Docker Registry管理项目，它包括权限管理(RBAC)、LDAP、日志审核、管理界面、自我注册、镜像复制和中文支持等功能

### 二、环境准备
Harbor的所有服务组件都是在Docker中部署的，所以官方安装使用Docker-compose快速部署，所以需要安装Docker、Docker-compose。由于Harbor是基于Docker Registry V2版本，所以就要求Docker版本不小于1.10.0，Docker-compose版本不小于1.6.0

#### 1）、安装并启动Docker
```
安装所需的包。yum-utils提供了yum-config-manager 效用，并device-mapper-persistent-data和lvm2由需要 devicemapper存储驱动程序
[root@localhost ~]# yum install -y yum-utils device-mapper-persistent-data lvm2

设置稳定存储库
[root@localhost ~]# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

安装Docker CE
[root@localhost ~]# yum install -y docker-ce docker-ce-cli containerd.io
```

#### 2）、安装Docker-compose
```
下载指定版本的docker-compose
[root@localhost ~]# curl -L https://github.com/docker/compose/releases/download/1.13.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

对二进制文件赋可执行权限
[root@localhost ~]# chmod +x /usr/local/bin/docker-compose

测试下docker-compose是否安装成功
[root@localhost ~]# docker-compose --version
docker-compose version 1.13.0, build 1719ceb
```

### 三、Harbor服务搭建及启动
