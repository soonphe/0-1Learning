# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Kubernetes 
kubernetes，简称K8s，是用8代替8个字符“ubernete”而成的缩写。
是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。 
官网：https://www.kubernetes.org.cn/docs


### docker和k8s的区别
希望楼主搞清楚docker和k8s的区别以及和其他容器产品的区别。
docker负责构建镜像和运行容器，k8s负责编排和调度，本来就是上下层的关系不存在竞争。docker公司出的swarm和k8s是竞争关系，这个竞争不过很正常。 由于有多种不同容器产品，k8s希望大家提供统一的接口便于用户切换，所以业界搞了CRI标准，容器产品只要实现这个协议就能被k8s调度。由于之前没有CRI，k8s只能主动适配容器，而docker最火所以直接依赖了docker的接口，是强耦合的。有了CRI，就是容器去适配k8s。所以docker从代码里剥离了容器最核心的部分，实现CRI，这个东西叫containerd

也可以说containerd是docker的一部分。docker这个公司确实比较可惜，这是商业上的问题。k8s通过CRI屏蔽了容器运行时，docker公司从行业开创者变成了一个可选的分包商。之前有新闻说k8s后续不再支持docker了，其本质就是把那坨耦合的代码去掉，都走CRI。即使走CRI，docker的containerd在可以预见的未来也是大部分人的首选

crio是标准，containerd是实现，docker已经被架空了，现在他死活已经不重要，推出containerd的时候就注定死了，日常操作里还有多少是敲docker命令的

### 安装kubernetes
安装k8s大致有2种方式，minikube和Docker Desktop

使用Docker Desktop安装k8s：
步骤1：安装 Docker Desktop
步骤2：下载 Kubernetes 镜像并启动运行
步骤3：下载 kubectl 工具
步骤4：启用 Dashboard（可选）使用 kubectl 命令来创建简单的 kubernetes-dashboard 服务

### 安装k8s客户端：
brew install kubernetes-cli
brew link kubernetes-cli
