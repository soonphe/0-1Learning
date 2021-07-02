# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

### Kibana
Kibana是一个针对Elasticsearch的开源分析及可视化平台，用来搜索、查看交互存储在Elasticsearch索引中的数据。这里是Linux系统安装说明，想了解windows安装说明，可以参考《Kibana 安装（Windows）》

### mac下安装（依赖于ElasticSearch）
brew安装
brew search kibana   //搜索kibana软件
brew install kibana  //安装kibana
brew services start kibana   //启动kibana
http://localhost:5601/      //访问
安装完后配置文件地址：/usr/local/etc/kibana/kibana.yml

### 压缩包模式安装：
* 1.下载解压
  Kibana官方下载地址：https://www.elastic.co/cn/downloads/kibana
  根据ElasticSearch版本及安装环境下载相应的Kibana安装包。
  示例：kibana-5.5.2-linux-x86_64.tar.gz
  将安装包上次到服务器，然后解压安装包，例如解压到：/run/
```
  tar –zxvf kibana-5.5.2-linux-x86_64.tar.gz–C /run/
```

* 2.配置kibana.yml
   到kibana安装目录的config下，编辑kibana.yml配置文件，添加如下配置：
```
  #配置本机ip
  server.host: "192.168.252.129"
  #配置es集群url
  elasticsearch.url: "http://192.168.252.129:9200"
```
* 3.启动kibana
  切换到kibana安装目录的bin目录下，执行kibana文件
```
  cd /run/kibana-5.5.2-linux-x86_64/bin
  ./kibana &
```
  主要使用&命令启动后，退出当前窗口时需要使用exit退出
  成功启动后，可以访问：http://localhost:5601 来访问kibana，ip为kibana安装节点ip，端口默认为5601，可以在config/kibana.yml中配置

### 使用教程
官网教程：https://www.elastic.co/cn/products/kibana

1.Management添加Index patterns
配置在kibana的management菜单中配置Create index pattern，先填写索引名，时间过滤可以选择不使用。
例：
```
index-name-*    //查看所有以index-name开头的索引数据
```
      
2.查看index Maping（mapping就是属性字段，字段类型不可改，但可以设置format格式化）

3.Discover数据浏览检索
在Discover菜单中，CHANGE INDEX PATTERN可以查询刚添加的Index patterns，选中即可预览查询数据
Search：可以搜索相关联的信息，还支持语法搜索，
Show dates：可以选择时间搜索
Add a filter：可以通过添加过滤条件来筛选数据
Selected fields：只查看哪些属性
Available fields：可以提供的属性

4.Dev tools可以直接执行es的DSL语句
```
GET _search
{
  "query": {
    "match_all": {}
  }
}
```
5.创建视图Visualize
先选择可视化图形类型，再选择数据源index，根据不同类型的可视化图形选择相应的数据指标，配置完后，添加Save保存视图。

6.创建仪表盘Dashboard
先选择Dashboard菜单，点击add新建仪表盘，添加已保存的可视化图标到仪表盘，再点击Save保存仪表盘。



使用方式参考文档：https://blog.csdn.net/cb2474600377/article/details/78963247
