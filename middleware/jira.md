# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## jira

### jira搭建：
官网地址：https://www.atlassian.com/
1.下载安装
```
atlassian-jira-software-7.3.8-x64_2.bin
[root@jira ~]# chmod +x atlassian-jira-software-7.3.8-x64_2.bin  #添加执行权限
[root@jira ~]# ./atlassian-jira-software-7.3.8-x64_2.bin   #安装
```
安装jira时配置指定数据库，jira支持多种数据库
2.破解jira
```
jira7.3 
├── atlassian-extras-3.2.jar ：和license相关
└── mysql-connector-java-5.1.39-bin.jar：jira连接mysql数据库相关的jar包
把破解包里的文件复制到/opt/atlassian/jira/atlassian-jira/WEB-INF/lib/目录下
[root@jira ~]# \cp -f ~/jira7.3/* /opt/atlassian/jira/atlassian-jira/WEB-INF/lib/ 
```
3.开启jira服务
```
[root@jira ~]# /opt/atlassian/jira/bin/start-jira.sh 
```
访问8080端口 http://192.168.13.142:8080/