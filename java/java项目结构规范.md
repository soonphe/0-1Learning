# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java项目结构规范
"设计项目目录结构"，就和"代码编码风格"一样，属于个人风格问题。
不规范的项目结构会花费数倍的时间去让别人熟悉、理解，规范化的项目结构能更好的控制程序结构，让程序具有更高的可读性、可维护性。

"项目目录结构"其实也是属于"可读性和可维护性"的范畴，我们设计一个层次清晰的目录结构，就是为了达到以下两点:
1.可读性高: 不熟悉这个项目的代码的人，一眼就能看懂目录结构，知道程序启动脚本是哪个，测试目录在哪儿，配置文件在哪儿等等。从而非常快速的了解这个项目。
2.可维护性高: 定义好组织规则后，维护者就能很明确地知道，新增的哪个文件和代码应该放在什么目录之下。这个好处是，随着时间的推移，代码/配置的规模增加，项目结构不会混乱，仍然能够组织良好。


### 目录组织方式
提供一份我常用的项目目录方式，使用java语言开发，基于springboot，微服务的项目架构目录，如果是单体可以直接参考service部分。
这里标注了命令规则和中文释义，实际使用可以进行适当的调整修改。
```
demo
├── document -- 文档模块
├── demo-api -- 服务接口模块contract、公共
    └── com.xxx.xxx -- 包名
        ├── constans -- 常量池
        ├── enums -- 公共枚举
        ├── request -- 请求传参
        ├── response -- 响应返参
        └── service -- 接口定义
├── demo-service -- 服务实现模块
    ├── com.xxx.xxx -- 包名
        ├── anotaion -- 注解层
        ├── config -- 配置项
        ├── constans -- 常量池
            └── enums -- 公共枚举
        ├── controller -- 内置Controller层（看情况使用）
        ├── domian -- 领域层
        ├── events -- 事件层
        ├── exception -- 异常
        ├── job -- 分布式任务
        ├── mapper -- Dao层
        ├── entity/model -- 实体
        ├── mq -- mq层
        ├── pojo -- pojo层
            ├── dto -- 服务间传输dto对象
            └── vo -- 用户查询vo对象
        ├── service -- service层
            └── impl -- service层
        ├── spi -- 服务调用层
        └── utils -- 公共工具层
    └── resources -- vo对象
        ├── mapper -- mapper.xml
        ├── application.yml -- 配置文件
        ├── application.dev -- 配置文件dev
        ├── application.test -- 配置文件test
        └── logback-spring.xml -- 日志配置
├── common module -- 公共模块
├── job module -- 分布式任务模块
├── elasticsearch module -- 搜索引擎模块
├── else module -- 其他模块
├── web module -- 独立的controller模块
└── README -- 项目说明
```

### 分层领域模型规约
•  DO(Data Object):与数据库表结构一一对应，通过 DAO 层向上传输数据源对象。
•  DTO(Data Transfer Object):数据传输对象，Service 和 Manager 向外传输的对象。
•  BO(Business Object):业务对象。可以由 Service 层输出的封装业务逻辑的对象。
•  QUERY:数据查询对象，各层接收上层的查询请求。注:超过 2 个参数的查询封装，禁止使用 Map 类来传输。
•  VO(View Object):显示层对象，通常是 Web 向模板渲染引擎层传输的对象。


### 关于README的内容
每个项目都应该有的一个文件，目的是能简要描述该项目的信息，让读者快速了解这个项目。
它需要说明以下几个事项:
```
1. 软件定位，软件的基本功能。
2. 运行代码的方法: 安装环境、启动命令等。
3. 简要的使用说明。
4. 代码目录结构说明，更详细点可以说明软件的基本原理。
5. 常见问题说明。
```
在软件开发初期，由于开发过程中以上内容可能不明确或者发生变化，并不是一定要在一开始就将所有信息都补全。但是在项目完结的时候，是需要撰写这样的一个文档的。
可以参考Redis源码中Readme的写法，这里面简洁但是清晰的描述了Redis功能和源码结构。