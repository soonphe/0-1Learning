# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## web3j

### 要点

快速开始：http://docs.web3j.io/latest/quickstart/

### 使用步骤
安装Web3j CLI 或 使用Web3j插件（Maven/Gradle）


1.依赖
<dependency>
  <groupId>org.web3j</groupId>
  <artifactId>core</artifactId>
  <version>3.4.0</version>
</dependency>

2.启动客户端：
$ geth --rpcapi personal,db,eth,net,web3 --rpc --rinkeby