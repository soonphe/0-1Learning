# 0-1Learning

![alt ](../static/common/svg/luoxiaosheng.svg "公众号")
![alt ](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt ](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## dubbo


### 配置
//服务应用名称
Dubbo.application.name
//注册中心的协议和地址
//通信协议和接口
//连接监控中心
Dubbo.monitor.protocol

### 服务方
服务提供方使用@Service注解暴露服务
```
@Service(timeout = 2000)
Public class UserService extends …
```

### 消费方
服务消费方使用@Reference注解来引用服务
```
@Reference
UserService userService
```
