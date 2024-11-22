# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## jira
官网地址：https://www.atlassian.com/

### jira下载地址
下载地址：https://www.atlassian.com/zh/software/jira/download

### 安装jira
1.下载解压
> atlassian-jira-software-8.4.1-standalone

2.Jira 配置
打开终端Terminal，进入JIRA 的bin 目录cd /usr/local/atlassian-jira-software-8.4.1-standalone/bin
输入./config.sh按Enter键，进入配置界面，按照提示进行配置
```
JIRA Home Directory：选择JIRA 的数据存储目录，这里选择默认的目录
Database Connection：选择数据库连接方式，jira支持多种数据库，如h2、mysql等
Web Server：Web 服务器配置，如端口号等
Advanced：高级设置
```

### 配置mysql数据库
创建数据库，字符串编码指定为utf8，排序规则指定为utf8_general_ci

Database Connection选择mysql，输入数据库连接信息，设置关联的数据库，此处就用到了上述MySQLWorkbench中创建的jira表，填写相关信息并测试是否连接成功（MySQL打开），save；

后续如果要修改则在JIRA_HOME目录下的`dbconfig.xml`文件中修改，如：启动失败，提示不支持字符集，可将mysql57修改为mysql

下载并配置MySQL驱动：https://dev.mysql.com/downloads/connector/j/5.1.html

### 破解jira
破解版下载：https://pan.baidu.com/s/1ggtAR0v

把破解包里面的atlassian-extras-3.2.jar和mysql-connector-java-5.1.39-bin.jar两个文件复制到/usr/local/atlassian-jira-software-8.4.1-standalone/atlassian-jira/WEB-INF/lib目录下。

其中atlassian-extras-3.2.jar是用来替换原来的atlassian-extras-3.2.jar文件，用作破解jira系统的。

而mysql-connector-java-5.1.39-bin.jar是用来连接mysql数据库的驱动软件包。

```
jira8.4.1 
├── atlassian-extras-3.2.jar ：和license相关
└── mysql-connector-java-5.1.39-bin.jar：jira连接mysql数据库相关的jar包
把破解包里的文件复制到/opt/atlassian/jira/atlassian-jira/WEB-INF/lib/目录下 
```

### 启动JIRA
运行/usr/local/atlassian-jira-software-8.4.1-standalone/bin下的start-jira.sh脚本，
如果未看到`Tomcat started`，看先运行shutdown.sh，再运行start-jira.sh

jira默认8080端口 http://localhost:8080/

### 配置密钥
第一次启动需要配置密钥，使用邮件创建密钥即可，根据id生成一个License(点击生成JIRA试用许可证)。

密钥修改
```
1.打开JIRA，点击右上角的设置按钮，选择系统
2.在系统设置页面，选择JIRA软件
3.在JIRA软件设置页面，选择许可证 
```

### jira 界面
- 仪表盘：
  - 系统仪表盘：默认System Dashboard
  - 用户仪表盘

- 项目：
  - 创建项目：
    - Software：Scrum开发方法，Kanban开发方法，基本软件开发
    - Business：项目管理，任务管理，流程管理
      - 项目内容
        - 左侧：看板 Backlog 活动的Sprint 发布版本 报告 问题 模块
        - 中间：问题列表，问题详情、问题操作、问题日志
        - 右侧：人员、问题统计、问题过滤器、问题属性
- 问题：
  - 搜索问题：搜索框，支持搜索语法
  - 最近的问题
  - 筛选器：配置筛选器，项目内共享
    
- 面板：
  - 最近的面板：配置筛选器，项目内共享，
  - 查看全部面板
    - Sprint：代表一个迭代，描述一个开发周期

- 新建
  - 故事：代表一个用户故事，描述一个用户的需求
  - 任务：代表一个任务，描述一个具体的工作
  - 故障：代表一个故障，描述一个系统的问题
  - 子任务：代表一个子任务，描述一个任务的子任务
  - Epic：代表一个史诗，描述一个大的需求
    

## confluence

### confluence下载地址
- 查看所有的版本：https://www.atlassian.com/zh/software/confluence/download-archives
- 可以直接用这个官网地址下载8.8.1版本：https://product-downloads.atlassian.com/software/confluence/downloads/atlassian-confluence-8.8.1-x64.bin

### 安装confluence
```
chmod +x atlassian-confluence-8.8.1-x64.bin
./atlassian-confluence-8.8.1-x64.bin
```
注:安装流程会让你指定端口和安装路径，前面可以直接回车使用默认选项，最后一步输入n ，然后回车，不要启动。

激活与优化
获取序列号，永久激活，支持插件激活

启动confluence
```
#重启confluence
service confluence start
```

注:数据库可以选择mysql8.0 或 postgresql。mysql8.0数据库创建语法如下
```
CREATE DATABASE confluence CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
```