# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## key words
`0-1Learning` 名词收集整理    
Github排行榜：https://www.githubs.cn/top


### backend   
1. Base framework
- JDK：1.8
- 容器：spring + springboot + spring-cloud
- 依赖：maven
- 业务模型：MVC + AOP + DDD
- aop切面支持：springboot-aop
- web功能：springboot-web
- redis功能支持：spring-boot-starter-data-redis
- es功能支持：spring-boot-starter-data-elasticsearch
- 数据校验：spring-boot-starter-validation
- 通用工具包：spring-data-commons
- 认证授权：Spring Security + OAuth
- 登录支持：JWT(Json Web Token)
- 数据库：mysql
- 数据库持久层框架：mybatis
- 数据库连接池：druid
- web API支持：swagger2
- RPC解析：fastjson
- 日志工具：logback
- alibab微服务组件：dubbo，Nacos：服务发现，配置管理 seata：分布式事务 Sentinel：熔断降级
- springcloud微服务组件：gateway网关 eureka服务发现 feign服务调用 ribbon hystrix断路器  config配置中心
- 异步Nio框架：netty(封装Java nio api)    Mina

2. 功能模块（function module）
- 常见系统搭配：EFK+K8s，prometheus+K8s，skywalking+K8s+es存储
- ELK：Elasticsearch，Kibana，logstash
- EFK：Elasticsearch，Filebeat or Fluentd(*)，Kibana
- Elastic Search：全文搜索引擎    
- Kibana：Es系统可视化工具
- logstash：日志收集工具
- xxl-job:分布式定时任务(基于数据库)
- Elastic-Job：分布式定时任务(基于zookeeper)
- zipkin：日志收集与查看
- minio：文件存储
- Redission/RedisCluster/Codis：分布式redis支持
- Kafka/rocketMQ：分布式消息队列系统

3. 工具模块（tool module）
- 分页工具：pagehelper
- 代码生成工具：mybatis-generator
- 代码生成插件：mybatis-generator-maven-plugin
- java爬虫（webmagic）/Python爬虫（crawl✔）
- 工具包：Hutool
- 汉字转拼音：pinyin4j
- excel支持：poi
- 推送：极光Jpush
- 短信：aliyun sms
- 对象存储：aliyun oss、MinIO

4. 运维支持devops module
- 虚拟化容器：docker、kubernates
- 运维工具：ansible
- 自动构建工具：jenkins
- 服务监控治理：Moss（拓展SpringCloudAdmin）
- 调用链监控：skywalking
- 可视化平台：Grafana
- mysql binlog二进制日志增量订阅：canal
- 数据库集群管理工具：mycat
- ES Web管理工具：Cerebro


### frontend
1. Base framework
- Node服务端：Koa/Express
- 语法要求：Es6
- 技术选型：vue
- 路由管理：router
- 网络请求：axios
- 状态管理：vuex
- 包管理：webpack
- 国际化：vue-i18n
- 代码检查：eslint
- cookie管理：js-cookie
- 加密工具：js-md5

2. module
- api：网络请求
- assets：基础静态资源(图片和css)
- components：自定义控件组件(首页面包屑，错误日志，github引流，左部菜单展开收缩控件，全屏控件)
- directive：自定义指令集
- lang：国际化组件
- router：路由组件
- styles：全局CSS
- utils：自定义工具类
- views：UI组件
- vuex：状态管理

3. view
- UI框架：element-ui
- CSS：less + sass
- 图表：echarts
- 富文本：vue-editor/vue-quill-editor
- 图标：font-awesome
- 换肤：chalk


### android
1. Framework 
   - JVM
   - 四大组件（启动流程，生命周期）
   - Handler
   - SharedPreferences
   - Choreographer
   - ActivityThread
   - AndroidView体系
2. Jetpack
   - LifeCycle
   - LiveData
   - MVVM
   - DataBinding

3. else
   - RPC数据解析：gson + fastjson
   - 日志：logger
   - 网络监控：fackbook stetho
   - 组件通信：EventBus
   - 数据库：Litepal
   - 网页加载：agentweb + 腾讯X5内核
   - 热修复+更新：bugly + tinker
   - JDK：1.8 + retrolambda表达式
   - Android组件化，插件化模式开发
   - 卡顿优化

4. UI控件库
   - 图片加载：Glide + 头像circleimageview
   - 图片选择：知乎matisse
   - 控件徽章：BadgeView
   - 控件切换：SwitchButton
   - 列表加载：BaseRecyclerViewAdapterHelper + easyrecyclerview
   - 进度条：WangAvi
   - 弹出框：material-dialogs
   - 菜单列表：FlycoTabLayout_Lib + VerticalTabLayout
   - 轮播图：banner

5. 开源三方SDK
   - 上传下载：okgo + okserver
   - 图片压缩：Luban
   - 上拉刷新，下拉加载更多：SmartRefreshLayout
   - 小说阅读器：HwTxtReader
   - PDF阅读器：android-pdf-viewer
   - 表单验证：saripaar
   - 动态权限请求：rxpermissions
   - 动画库：nineoldandroids
   - 音视频播放器：jiaozivideoplayer
   - 中文转拼音：jpinyin
   - html解析：jsoup
   - 工具类：blankj
   - 组件依赖注入：Dagger
   - view依赖注入：butterknife
   - 网络框架：okhttp3 + retrofit2
   - 异步框架：rxjava2
   - 推送：极光推送、个推、友盟
   - 定位：高德+citypicker 
   - 热修复：Tinker 
   - 即时通讯、音视频：环信、融云 
   - OBS直播：腾讯云、网易云 
   - 三方登录、分享：友盟 
   - 验证码：极验

### ios
1. 系统架构
网络抽象：moya
网络请求： Alamofire
图片加载：kingfisher
数据解析：objectmapper（json）
数据库：realm

### else

