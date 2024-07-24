# 0-1Learning

<p align="center">
  <img src="static/common/0-1Learning.png" width=1100 />
</p>
<p align="center">
  <a href="https://mp.weixin.qq.com/mp/profile_ext?action=home&__biz=MzI4MjM3MTA2Mw==#wechat_redirect"><img src="static/common/svg/luoxiaosheng.svg" alt="Wechat public account" /></a>
  <a href="https://space.bilibili.com/352882592"><img src="static/common/svg/luoxiaosheng_learning.svg" alt="video" /></a>
</p>
<div align="center">
  <h3>
    <a href="https://github.com/soonphe/0-1Learning/blob/master/README.md">Chinese</a> | <strong>English</strong>
  </h3>
</div>

## 目录
- [Project Introduction](#Project Introduction)
    - [Highly available distributed system architecture](#Highly available distributed system architecture)
    - [System Technology Stack](#System Technology Stack)
    - [Continuous delivery pipeline](#Continuous delivery pipeline)
    - [Project structure)](#Project structure)
- [What the author wants to say](#What the author wants to say)
- [Where can you watch my articles for free](#Where can you watch my articles for free)
    - [github](#github)
    - [Wechat public account](#Wechat public account)
- [Where can you watch my videos for free](#Where can you watch my videos for free)
  - [YouTube](#YouTube)
  - [tiktok](#tiktok)
  - [bilibili](#bilibili)
- [Thanks for other people's open source projects](#Thanks for other people's open source projects)


## Project Introduction

`0-1Learning` The project is committed to helping developers cross the technical barriers from layman to layman and achieve **0-1** technological breakthroughs.


### Highly available distributed system architecture
Distributed is no longer a new vocabulary. A mature distributed system will inevitably involve high-availability design to ensure high-availability of services under limited resources.

As follows, these technologies may not be very unfamiliar to you, but maybe you are still a novice and have not been exposed to the following, so please remember the following, and you will inevitably have a lot of dealings in the future.

![高可用分布式系统架构](static/architecture/highly_available_architecture.png "高可用分布式系统架构")

> This picture basically covers the core components involved in a distributed system at this stage, and the expandable components can also be directly added. The fact that there is only one component in the figure does not mean that there is only one single node. Each component can be a single node or a cluster to provide external services.


### System Technology Stack

![系统技术栈](static/architecture/system_technology_stack.png "系统技术栈")

> Originally, I also drew a `Learning roadmap` (Learning roadmap) to help novice friends quickly find a road to the technology god. After thinking about it, there is too much overlap with the technology stack, so I simply combined the two into one, and the roadmap is directly referred to. Just follow the picture below.


### Continuous delivery pipeline
![持续交付流水线图](static/architecture/continuous_delivery_pipeline.png "持续交付流水线图")

> Compared with the general `project development flow chart` in front of thousands of people, I think this picture of continuous delivery is more meaningful. How to develop the project process ultimately depends on people, and people can't be regarded as a stable node. computational.
> For continuous development delivery, there should be such an automated process. In the continuous iteration of requirements, version control, continuous integration and empowerment, automatic packaging and deployment, environment initialization and redistribution, and finally a visual operation panel is formed. The process is controllable and transparent, and it is directly presented to users.


### Project structure
``````
0-1Learning
├── algorithm -- 算法
    ├── case -- 常见算法案例
    ├── logical-question    逻辑算法问题
    └── sort -- 常见排序算法
├── android -- 安卓
    ├── 0-1java -- 从0到1学android
        ├── 01认识Android
        ├── 02四大组件——活动Activity
        ├── 03四大组件——服务Service
        ├── 04四大组件——内容提供者content provider
        ├── 05四大组件——广播接收器broadcast receiver
        ├── 06UI和控件
        ├── 07碎片
        ├── 08数据存储
        ├── 09多媒体技术
        ├── 10网络技术
        └── 11Android特色开发
    ├── android-senior -- android高级
    ├── android-source-code -- android源码
    ├── android-ui -- android UI
    └── android-widget -- android 小组件
├── bigdata -- 大数据
    ├── hadoop -- hadoop/hdfs
    ├── hive -- sql操作大数据
    ├── scala -- 函数编程
    └── spark -- 计算引擎
├── blockchain -- 区块链
    ├── solidity -- 智能合约编程语言
    └── web3 -- 框架
├── computer-network -- 计算机网络结构
├── computer-os -- 计算操作系统
├── data-structure -- 数据结构
├── database -- 数据库
    ├── 0-1database -- 从0到1学database
        ├── 01数据库基础
        ├── 02编写简单的查询语句
        ├── 03条件查询和数据排序
        ├── 04单行函数
        ├── 05多表查询
        ├── 06分组函数
        ├── 07子查询
        ├── 08数据操作与事务控制
        ├── 09表和约束
        └── 10数据库其他对象
    ├── database-senior -- database高级
    ├── database-sql-case -- database数据库案例
    └── mysql开发规范
├── design -- 设计
├── design-pattern -- 设计模式
├── document -- 文档管理桂芬 
├── git -- 版本控制
├── html -- html网页
    ├── 0-1html -- 从0到1学html
    ├── 0-1vue -- 从0到1学vue
        ├── 01数据库基础
        ├── 02编写简单的查询语句
        ├── 03条件查询和数据排序
        ├── 04单行函数
        ├── 05多表查询
        ├── 06分组函数
        ├── 07子查询
        ├── 08数据操作与事务控制
        ├── 09表和约束
        └── 10数据库其他对象
    ├── vue-senior -- vue高级 
    ├── vue-widget -- vue小组件
├── interview -- 面试题和面试经验
    ├── interview-case -- 面试题与面试案例 
    └── interview-summary -- 面试总结
└── ios -- ios技术栈
    ├── 0-1ios -- 从0到1学ios
        ├── 01认识Ios
        ├── 02用户界面
        ├── 03界面优化
        ├── 04系统功能
        ├── 05数据存储
        ├── 06多媒体技术
        ├── 07网络技术
        └── 08IOS特色开发
    └── swift语法
└── java -- java技术栈
    ├── 0-1java -- 从0到1学java
        ├── 01认识Java
        ├── 02Java虚拟机简介
        ├── 03变量和运算符
        ├── 04流程控制语句
        ├── 05数组
        ├── 06函数
        ├── 07面对对象基础
        ├── 08面对对象高级特性
        ├── 09异常处理
        ├── 10工具类
        ├── 11集合
        ├── 12文件与流IO
        ├── 13多线程编程
        └── 14网络编程
    ├── java-concurrent -- java并发编程
    ├── java-expand -- java拓展
    ├── java-reading-notes -- java读书笔记
    ├── java-senior -- java高级
    ├── java-sourch-code -- java源码
    ├── java-spring -- 
    ├── java-spring-boot -- 
    ├── java-spring-cloud -- 
    ├── jvm -- java虚拟机
    └── java编程规范 --
├── linux -- linux常用操作和命令
    ├── linux软件安装 -- 
    └── linux常用命令 -- 
├── middleware -- 中间件
    ├── docker -- 容器化技术
    ├── elasticSearch -- 搜索引擎
    ├── Grafana -- 可视化工具
    ├── Grafana -- 分布式消息队列
    ├── Kibana -- ES是可视化管理工具
    ├── knife4j -- swagger文档聚合
    ├── minio -- 对象存储中间件
    ├── nacos -- alibaba服务发现与配置管理
    ├── Nginx -- nginx安装、配置
    ├── Prometheus -- Prometheus系统监控报警
    ├── skywalking -- 调用链监控
    ├── xxl-job -- 分布式调度任务
    ├── kafka -- 分布式消息队列
    ├── mongodb -- nosql数据库
    ├── redis -- redis缓存
    └── zookeeper -- 分布式调度
├── other -- 其他
├── tools -- 工具整理
    ├── Mac-homebrew使用
    ├── Mac-IDEA快捷键
    ├── Mac-Xcode快捷键
    ├── Mac快捷键
    ├── oh-my-zsh命令行工具
    ├── vpn代理说明
    └── 常用软件及安装
└── static -- 静态文件包
``````

## What the author wants to say



## Where can you watch my articles for free

### github
connection address：https://github.com/soonphe/0-1Learning


### Wechat public account

The public account has published some articles of `0-1Learning`, and there are more good articles, waiting for your exploration, pay attention to the public account "**Luo Xiaosheng**" to get it as soon as possible.

![公众号图片](static/common/luoxiaosheng_wechat_common.jpg)


## Where can you watch my videos for free

After all, the expression of text is limited, and the effect of video will be much better than that of text.

**The content of the video can be shot wherever you want, whatever is fun, and you don’t want to set a content for yourself. Who knows what will happen in the future! **

Try to contribute on all platforms, Youtube, tiktok, station B, etc., pay attention to "**罗晓胜**" to get it as soon as possible.

If you have any questions, leave me a message and I will reply to the best of my ability! Remember to interact and support!

> `Attention, the author will not take the initiative to contact anyone`, **Anyone who pretends to be the author takes the initiative to contact you is a liar! ! ! **
>
> `Attention, if it involves money transactions, please verify and then operate after verification, so as not to be deceived`


## Thanks for other people's open source projects


## license

Copyright (c) 2020 soonphe