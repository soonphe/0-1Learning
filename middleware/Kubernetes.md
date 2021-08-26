# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Kubernetes 
kubernetes，简称K8s，是用8代替8个字符“ubernete”而成的缩写。
是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。 
官网：https://www.kubernetes.org.cn/docs


### 安装kubernetes
安装k8s大致有2种方式，minikube和Docker Desktop

使用Docker Desktop安装k8s：
步骤1：安装 Docker Desktop
步骤2：下载 Kubernetes 镜像并启动运行
步骤3：下载 kubectl 工具
步骤4：启用 Dashboard（可选）使用 kubectl 命令来创建简单的 kubernetes-dashboard 服务

### 安装k8s客户端：
brew install kubernetes-cli
brew link kubernetes-cli
