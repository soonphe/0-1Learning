# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## spring-cloud-config配置中心

1.配置中心服务server
pom配置
new project(选择
Cloud Discover——Eureka Discoverry 
Cloud config——config server

注解
@EnableConfigServer

环境参数
spring.application.name=config-server
server.port=8888


spring.cloud.config.server.git.uri=https://github.com/forezp/SpringcloudConfig/
spring.cloud.config.server.git.searchPaths=respo
spring.cloud.config.label=master
spring.cloud.config.server.git.username=your username
spring.cloud.config.server.git.password=your password


——————————————————————————————————————————————
远程仓库



2.配置中心client
pom配置
Web——web
Cloud config——config client


环境参数
spring.application.name=config-client
spring.cloud.config.label=master
spring.cloud.config.profile=dev
spring.cloud.config.uri= http://localhost:8888/
server.port=8881

============================================================================================

高可用配置中心——将配置中心作为一个微服务，将其集群化
