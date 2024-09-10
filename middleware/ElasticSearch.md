# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## ElasticSearch
Elasticsearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java语言开发的，并作为Apache许可条款下的开放源码发布，是一种流行的企业级搜索引擎。

### mac版Elasticsearch安装、Kibana安装
*Mac下载安装步骤：*
brew search elasticsearch   //搜索es软件
brew install elasticsearch  //安装es
brew services start elasticsearch   //启动es
http://localhost:9200/      //访问

brew search kibana   //搜索kibana软件
brew install kibana  //安装kibana
brew services start kibana   //启动kibana
http://localhost:5601/      //访问

### Elasticsearch安装、Kibana安装

官方下载地址：https://www.elastic.co/downloads/elasticsearch

Windows下载安装步骤：

    1.下载Elasticsearch6.2.2的zip包，并解压到指定目录，下载地址：https://www.elastic.co/cn/downloads/past-releases/elasticsearch-6-2-2
    2.安装中文分词插件，在elasticsearch-6.2.2\bin目录下执行以下命令：
    elasticsearch-plugin install 
    https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.2/elasticsearch-analysis-ik-6.2.2.zip
    3.下载kibana做为es客户端
    https://artifacts.elastic.co/downloads/kibana/kibana-6.2.2-windows-x86_64.zip
    4.启动ES和kibana，
    访问http://localhost:5601 即可打开Kibana的用户界面

Linux安装步骤：

    解压文件 elasticsearch-6.2.2
    
    修改配置文件（也可以不改动，直接启动）
    elasticsearch-6.2.2  cat  config /elasticsearch .yml | grep  - v  "#"
    cluster.name: rainbow
    network.host: 127.0.0.1
    http.port: 9200
    
    配置说明
    cluster.name表示es集群的名称，可以自定义一个自己需要的集群名称
    http.port 表示对外提供http服务时的http端口。
    network.host 表示本地监听绑定的ip地址，此处为测试环境，直接使用本机的ip地址 127.0.0.1.
    
    启动elasticsearch
    nohup bin/elasticsearch  &
    访问：http://localhost:9200/

    启动kibana
    nohup bin/kibana &  
    访问：http://localhost:5601/

### Elasticsearch启动监听两个端口，9300和9200
9300端口是使用tcp客户端连接使用的端口；（后台client访问）
9200端口是通过http协议连接es使用的端口；（网页或curl访问）

### kibana使用
**dev_tools**
dev_tools功能：控制台，可以使用dsl操作ES
例：
```
GET _search
{
  "query": {
    "match_all": {}
  }
}
```

**创建看板**
- 创建索引模式：Stack Management——Index Patterns——Create index pattern(如果存在，则将在列表中显示)
- 添加Index成功后，点击“discover”面板，查看对应索引增加的数据，

### Elasticsearch文档、类型、索引及映射
1、文档 （document）
Elasticsearch是面向文档的，这意味着索引和搜索数据的最小单位是文档。
在Elasticsearch中文档有几个重要的属性。
它是自我包含的。一篇文档同时包含字段和它们的取值。
它可以是层次的。文档中还包含新的文档，字段还可以包含其他字段和取值。例如，“location”字段可以同时包含“city”和“street“两个字段。
它拥有灵活的结构。文档不依赖于预先定义的模式。并非所有的文档都需要拥有相同的字段，它们不受限于同一个模式。

2、类型 （type）
类型是文档的逻辑容器，类似于表格是行的容器。在不同的类型中，最好放入不同结构的文档。例如，可以用一个类型定义聚会时的分组，而另一个类型定义人们参加的活动。

3、索引 （index）
索引是映射类型的容器。一个Elasticsearch索引是独立的大量的文档集合。 每个索引存储在磁盘上的同组文件中，索引存储了所有映射类型的字段，还有一些设置。

4、映射（mapping）
所有文档在写入索引前都将被分析，用户可以设置一些参数，决定如何将输入文本分割为词条，哪些词条应该被过滤掉，或哪些附加处理有必要被调用（比如移除HTML标签）。这就是映射扮演的角色：存储分析链所需的所有信息。

### Elasticsearch节点、集群、分片及副本
1、节点 （node）
一个节点是一个Elasticsearch的实例。

在服务器上启动Elasticsearch之后，就拥有了一个节点。如果在另一台服务器上启动Elasticsearch，这就是另一个节点。甚至可以通过启动多个Elasticsearch进程，在同一台服务器上拥有多个节点。

2、集群（cluster）
多个协同工作的Elasticsearch节点的集合被称为集群。

在多节点的集群上，同样的数据可以在多台服务器上传播。这有助于性能。这同样有助于稳定性，如果每个分片至少有一个副本分片，那么任何一个节点宕机后，Elasticsearch依然可以进行服务，返回所有数据。

但是它也有缺点：必须确定节点之间能够足够快速地通信，并且不会产生脑裂（集群的2个部分不能彼此交流，都认为对方宕机了）。

3、分片 （shard）
集群允许系统存储的数据总量超过单机容量。为了满足这个需求，Elasticsearch将数据散布到多个物理的Lucene索引上去。这些Lucene索引被称为分片，而散布这些分片的过程叫作分片处理（sharding）。Elasticsearch会自动完成分片处理，并且让用户看来这些分片更像是一个大的索引。

除了Elasticsearch本身自动进行分片处理外，用户为具体的应用进行参数调优也是至关重要的，因为分片的数量在创建索引的时就被配置好了，之后无法改变，除非创建一个新索引并重新索引全部数据。

4、副本（replica）
分片处理允许用户推送超过单机容量的数据至Elasticsearch集群。副本则解决了访问压力过大时单机无法处理所有请求的问题。

分片可以是主分片，也可以是副本分片，其中副本分片是主分片的完整副本。副本分片用于搜索，或者是在原有的主分片丢失后成为新的主分片。

注意：可以在任何时候改变每个分片的副本分片的数量，因为副本分片总是可以被创建和移除的。这并不适用于索引划分为主分片的数量，在创建索引之前，必须决定主分片的数量。过少的分片将限制可扩展性，但是过多的分片会影响性能。默认设置的5份是一个不错的开始。


### Elasticsearch查询配置、集群信息
http://127.0.0.1:9200 打开可获取Elasticsearch配置信息
````
{
  "name" : "sc-stable-new-luban-001",   //节点名
  "cluster_name" : "sc-stable-new-luban",   //集群名
  "cluster_uuid" : "FzFgJB6nQzqgmJLhLPREjA",    
  "version" : {
    "number" : "6.5.3", //ES版本
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "159a78a",
    "build_date" : "2018-12-06T20:11:28.826501Z",
    "build_snapshot" : false,
    "lucene_version" : "7.5.0", //lucene版本
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
````

1.查看集群的健康状态。
http://127.0.0.1:9200/_cat/health?v

    status：集群的状态，red红表示集群不可用，有故障。yellow黄表示集群不可靠但可用，一般单节点时就是此状态。green正常状态，表示集群一切正常。
    node.total：节点数，表示该集群有多少个节点。
    node.data：存储数据节点数
    shards：分片数，表示我们把数据分成多少块存储。
    pri：primary shards，主分片数，实际上是分片数的两倍，因为有一个副本，如果有两个副本，这里的数量应该是分片数的三倍
    active_shards_percent：激活的分片百分比，只有加载所有的分片数，集群才算正常启动，在启动的过程中，如果我们不断刷新这个页面，我们会发现这个百分比会不断加大

2.查看集群的索引数。
http://127.0.0.1:9200/_cat/indices?v

    health：索引健康，green为正常，yellow表示索引不可靠（单节点），red索引不可用。与集群健康状态一致。
    status：状态，表明索引是否打开。
    index：索引名称，这里有.kibana和school。
    uuid：索引内部随机分配的名称，表示唯一标识这个索引。
    pri：主分片，.kibana为1，school为5，加起来主分片数为6，这个就是集群的主分片数。
    rep：副本数量
    docs.count：文档数，school在之前的演示添加了两条记录，所以这里的文档数为2。
    docs.deleted：已删除文档数，这里统计了被删除文档的数量。
    store.size：索引存储的总容量，这里school索引的总容量为6.4kb，是主分片总容量的两倍，因为存在一个副本。
    pri.store.size：主分片的总容量，这里school的主分片容量是7kb，是索引总容量的一半。

3.查看集群所在磁盘的分配状况
http://127.0.0.1:9200/_cat/allocation?v

    分片数（shards），集群中各节点的分片数相同，都是6，总数为12，所以集群的总分片数为12。
    索引所占空间（disk.indices），该节点中所有索引在该磁盘所点的空间。
    磁盘使用容量（disk.used），已经使用空间41.6gb
    磁盘可用容量（disk.avail），可用空间4.3gb
    磁盘总容量（disk.total），总共容量45.9gb
    磁盘使用率（disk.percent），磁盘使用率90%。

4.查看集群的节点
http://127.0.0.1:9200/_cat/nodes?v

    master列，带*星号表明该节点是主节点。
    带-表明该节点是从节点。
    另外还是heap.percent堆内存使用情况
    ram.percent运行内存使用情况，cpu使用情况。

5.查看集群的其它信息。
http://127.0.0.1:9200/_cat/

    =^.^=
    /_cat/allocation
    /_cat/shards
    /_cat/shards/{index}
    /_cat/master
    /_cat/nodes
    /_cat/tasks
    /_cat/indices
    /_cat/indices/{index}
    /_cat/segments
    /_cat/segments/{index}
    /_cat/count
    /_cat/count/{index}
    /_cat/recovery
    /_cat/recovery/{index}
    /_cat/health
    /_cat/pending_tasks
    /_cat/aliases
    /_cat/aliases/{alias}
    /_cat/thread_pool
    /_cat/thread_pool/{thread_pools}
    /_cat/plugins
    /_cat/fielddata
    /_cat/fielddata/{fields}
    /_cat/nodeattrs
    /_cat/repositories
    /_cat/snapshots/{repository}
    /_cat/templates
    其实很多都可以见词知义的，相当于获得查看集群信息的目录

### Elasticsearch索引和数据 
- 查询索引信息：curl -XGET 'http://localhost:9200/log_index'
```
{
    log_index: {            索引名称
        aliases: { },       索引别名
        mappings: {         mapping集合
            properties: {
                createTime1: {                          属性名称
                    properties: {
                        type: {
                            type: "text",               文本类型(text会分词，keyword不会分词)
                            fields: {                   自动创建的动态映射(dynamic mappings)：
                                keyword: {
                                    type: "keyword",
                                    ignore_above: 256
                                }
                            }
                        }
                    }
            },
            ...
            }   
        },
        settings: {
            index: {
                creation_date: "1666333913993",
                number_of_shards: "5",
                number_of_replicas: "1",
                uuid: "zcxUnFBzTQGxToiyIUdnRA",
                version: {
                    created: "7100299"
                },
                provided_name: "log_index"
            }
        }
    }
}
```

- 查询索引下所有数据：curl -XGET 'http://localhost:9200/log_index/_search?pretty'

### Elasticsearch支持的字段类型
|一级分类	|二级分类	|具体类型|
|---|---|---|
|核心类型|字符串类型|string,text,keyword|
|---|整数类型|integer,long,short,byte |
|---|浮点类型|double,float,half_float,scaled_float|
|---|逻辑类型|boolean|
|---|日期类型|date|
|---|范围类型|range|
|---|二进制类型|binary|
|复合类型|数组类型|array|
|---|对象类型|object|
|---|嵌套类型|nested|
|地理类型|地理坐标类型|geo_point|
|---|地理地图|geo_shape|
|特殊类型|IP类型|ip|
|---|范围类型|completion|
|---|令牌计数类型|token_count|
|---|附件类型|attachment|
|---|抽取类型|percolator|

说明：
ES5.0及以后的版本取消了string类型，将原先的string类型拆分为text和keyword两种类型。它们的区别在于text会对字段进行分词处理而keyword则不会。
ElasticSearch对字符串拥有两种完全不同的搜索方式. 你可以按照整个文本进行匹配, 即关键词搜索(keyword search), 也可以按单个字符匹配, 即全文搜索(full-text search).
- text： 会分词，然后进行索引；支持模糊、精确查询；不支持聚合；text用于全文搜索的
- keyword： 不进行分词，直接索引； 支持模糊、精确查询；支持聚合；而keyword用于关键词搜索

### Elasticsearch创建索引、删除索引、设置mapping、增加数据、查询数据

#### 索引创建
请求结构说明：**http://localhost:9200/{_index}/{_type}/{_id}**

- 参数选填示例1-PUT请求-只创建索引：指定index为log_index，指定分片数据和副本数量（请求头为application/json）(分片20，副本1)
```
https://localhost:9200/log_index?pretty
{
	"settings": {
		"number_of_shards": 5,
		"number_of_replicas": 1
	}
}
```
- 参数选填示例2-PUT请求-创建索引和mapping：指定index为log_index，指定分片数据和副本数量(分片20，副本1，type为text进行分词，type为keyword不进行分词)，mapping分别定义；
```
https://localhost:9200/log_index?pretty
{
    "settings": {
        "number_of_shards": 20,
        "number_of_replicas": 1
    },
   "mappings": {
        "properties": {
            "measureInfo": {
                "type": "text"
            },
            "extraOtherInfo": {
                "type": "text"
            },
            "commodity_id": {
                "type": "long"
            },
            "commodity_name": {
                "type": "text"
            },
            "picture_url": {
                "type": "keyword"
            },
            "price": {
                "type": "double"
            }
        }
    }
}
```


#### 索引删除和索引数据删除
删除索引：curl -XDELETE localhost:9200/log_index

删除数据：curl -XDELETE localhost:9200/log_index/_doc/_id

根据条件删除数据：
```
POST localhost:9200/log_index?requests_per_second
{
  "slice":{   #手动分片删除
    "id":1,    #两次删除需要修改id
    "max":2 #分为两批次删除
  }
  "query": { 
    "match_all": {}
  }
}
```

#### 创建索引并添加数据
curl -XPOST 'http://localhost:9200/{_index}/{_type}/{_id}' -d'{"a":"avalue","b":"bvalue"}'
```
限定index + type添加：（不添加id会自动创建随机字符串id）
curl -H "Content-Type: application/json" -XPOST 'http://localhost:9200/log_index/log_type' -d'{"a":"avalue","b":"bvalue"}'

限定index + type + id添加（ID相同会覆盖）：
curl -H "Content-Type: application/json" -XPOST 'http://localhost:9200/log_index/log_type/log_id' -d'{"a":"avalue","b":"bvalue"}'
```

#### 创建索引并设置type 和 mapping
创建索引说明：
- 如果指定到index级别，只需要put就行，
- 如果是type级别，则为post请求，
- 如果指定id，则为添加数据；

elasticsearch 7.x相较于elasticsearch 6.x在有所不同：7.x中设置mapping删去了_type，如果在7.x中用6.x的方法定义会出现错误
> "error": "Incorrect HTTP method for uri [/log_index/_type] and method [PUT], allowed: [POST]"

- 参数选填示例3-POST请求-添加mapping字段：指定type则必须为post请求，如果type创建时没有指定，则默认为_doc，否则报错
> 如指定为_type："reason": "mapping type name [_type] can't start with '_' unless it is called [_doc]"
> 如指定为type1："reason": "Rejecting mapping update to [log_index] as the final mapping would have more than 1 type: [_doc, type1]"
```
http://localhost:9200/log_index/_doc
{  	
  "createTime" : {
    "type" : "long"
  },
  "name" : {
    "type" : "text"
  }
}
```

- 参数选填示例4-POST请求-指定ID更新mapping：指定id为1，若原来的id存在，则会覆盖原来的mapping数据，若不存在指定id数据，则新建一条数据，并指定mapping
```
http://localhost:9200/log_index/_doc/1
{

	"properties" : {
	  "createTime" : {
		"type" : "long"
	  },
	  "name" : {
		"type" : "text"
	  }
	}
}
```
说明：
如果不指定类型，ElasticSearch字符串将默认被同时映射成text和keyword类型，会自动创建下面的动态映射(dynamic mappings)：
```
{
properties
    "name": {
        "type": "text",
        "fields": {
            "keyword": {
                "type": "keyword",
                "ignore_above": 256
            }
        }
    }
}
这就是造成部分字段还会自动生成一个与之对应的“.keyword”字段的原因。
```

**Index和Type使用的注意事项**
- Index
  - Index 存储在多个分片中，其中每一个分片都是一个独立的 Lucene Index。这就意味着添加新 index 应该有个限度：每个 Lucene Index 都需要消耗一些磁盘，内存和文件描述符。因此，一个大的 index 比多个小 index 效率更高： Lucene Index 的固定开销被摊分到更多文档上了。
  - 另一个重要因素是你准备怎么搜索你的数据。在搜索时，每个分片都需要搜索一次， 然后 ES 会合并来自所有分片的结果。例如，你要搜索 10 个 index，每个 index 有 5 个分片，那么协调这次搜索的节点就需要合并 5x10=50 个分片的结果。这也是一个你需要注意的地方：如果有太多分片的结果需要合并，或者你发起了一个结果巨大的搜索请求，合并任务会需要大量 CPU 和内存资源。这是第二个让 index 少一些的理由。

- Type
  - 使用 type 允许我们在一个 index 里存储多种类型的数据，这样就可以减少 index 的数量了。在使用时，向每个文档加入 _type 字段，在指定 type 搜索时就会被用于过滤。使用 type 的一个好处是，搜索一个 index 下的多个 type，和只搜索一个 type 相比没有额外的开销 —— 需要合并结果的分片数量是一样的。
  - 不同 type 里的字段需要保持一致。例如，一个 index 下的不同 type 里有两个名字相同的字段，他们的类型（string, date 等等）和配置也必须相同。
  - 只在某个 type 里存在的字段，在其他没有该字段的 type 中也会消耗资源。这是 Lucene Index 带来的常见问题：它不喜欢稀疏。由于连续文档之间的差异太大，稀疏的 posting list 的压缩效率不高。这个问题在 doc value 上更为严重：为了提高速度，doc value 通常会为每个文档预留一个固定大小的空间，以便文档可以被高速检索。这意味着，如果 Lucene 确定它需要一个字节来存储某个数字类型的字段，它同样会给没有这个字段的文档预留一个字节。未来版本的 ES 会在这方面做一些改进，但是我仍然建议你在建模的时候尽量避免稀疏。[1]
  - 得分是由 index 内的统计数据来决定的。也就是说，一个 type 中的文档会影响另一个 type 中的文档的得分。

> 这意味着，只有同一个 index 的中的 type 都有类似的映射 (mapping) 时，才应该使用 type。否则，使用多个 type 可能比使用多个 index 消耗的资源更多。


#### 索引新增数据-单条写入
支持put/post两种请求方式：
   - put：curl -X PUT 'http://127.0.0.1:9200/log_index/_doc/id'(**必须指定数据ID**，同一条数据首次插入是created，再次插入会updated）
   - put：curl -X PUT 'http://127.0.0.1:9200/log_index/_create/id'(**必须指定数据ID**，同一条数据首次插入是created，再次插入会报错）
   - 
   - post：curl -X POST 'http://127.0.0.1:9200/log_index/_doc'（**数据ID可不指定**，不指定id则自动生成ID，相同数据插入不会覆盖更新；指定id若对应数据存在则更新）
   - post：curl -X POST 'http://127.0.0.1:9200/log_index/_create'（**数据ID可不指定**，不指定id则自动生成ID，相同数据插入不会覆盖更新；指定id若对应数据存则插入失败））
   - 补充
     - _doc:同一条数据首次插入是created，再次插入会updated
     - _create:同一条数据首次插入是created，再次插入会报错

示例1-put方式+_doc请求：插入/更新一条数据(id指定为1，id重复则会覆盖，**同一名称字段类型必须相同**)
```
http://127.0.0.1:9200/log_index/_doc/1
{
  "name":1
}
```
示例2-put方式+_create请求：插入一条数据(id指定为1，id重复则会报错)
```
http://127.0.0.1:9200/log_index/_create/1
{
  "name":1
}
```
示例3-post方式+_doc请求：插入一条数据(id不指定，重新生成一条新id数据)
```
http://127.0.0.1:9200/log_index/_doc
{
  "name":1
}
```
示例4-post方式+_doc请求：插入一条数据(id指定1，更新覆盖)
```
http://127.0.0.1:9200/log_index/_doc/1
{
  "name":1
}
```

批量写入_bulk:
```
POST _bulk
{index:{"log_index":"company-staff-001", "_id": "1"}}
...
{index:{"log_index":"company-staff-001", "_id": "2"}}
...
{index:{"log_index":"company-staff-001", "_id": "3"}}
...
```


5. 查询
获取索引所有数据：curl -XGET 'http://localhost:9200/log_index/_search?pretty'

索引下信息：curl -XGET 'http://localhost:9200/{_index}/{_type}/{_id}'
```
curl -XGET 'http://localhost:9200/_index/_type/_id?pretty'
```

6. 获取条件查询数据：
```
curl -XGET 'http://localhost:9200/{index}/{type}/_search' -d '{
    "query" : {
        "term" : { "user" : "kimchy" } //查所有 "match_all": {}
    },
	"sort" : [{ "age" : {"order" : "asc"}},{ "name" : "desc" } ],
	"from":0,
	"size":100
}
```

7. 模糊查询
按时间范围、状态、用户ID、词条匹配、前缀匹配、模糊搜索，统计金额汇总，搜索结果排序
```
{
  "query": {
    "bool": {
      "must": [  
         {"range": { "createTime": { "gte":  "2021-09-01T19:54:30","lte":  "2021-09-13T19:59:30"} }},
         {"term": { "orderState": 20 }},
         {"term": { "userId": 114773809 }},
         {"match": {"stationName": "顺义 red"}},
         {"prefix": {"stationName.keyword": "北京市朝阳"}},
         {"wildcard": {"stationName.keyword": "北京市朝阳*"}}
      ]
    }
  },
  "aggs" : {
   "total_amount" : { "sum" : { "field" : "amount" } },
   "total_act_amount" : { "sum" : { "field" : "actAmount" } }
  },
  "sort": [
    {
      "createTime": {
        "order": "desc"
      },
      "_id": {
        "order": "desc"
      }
    }
  ]
}        
```


参数分析：
- -d标识要传递的参数
- log_index是索引index
- log_type是索引类型type（索引和类型的名称在文件中如果有定义，可以省略；如果没有则必须要指定）
- _bulk是rest的命令，可以批量执行多个操作（操作是在json文件中定义的，原理可以参考之前的翻译）
- pretty是将返回的信息以可读的JSON形式返回。


### Elasticsearch索引别名
es可以对一个或者多个索引指定别名,通过别名可以查到一个或者多个索引的内容。
在内部,es会把别名映射到相应的索引上。
可以对别名编写过滤器或者路由,在系统中别名不能重复,也不能和索引名重复。es中的别名机制有点像数据库中的视图。

例子:为test1增加一个别名alias1
- 请求:POST http://127.0.0.1:9200/_aliases
- 参数:{"actions":[{"add":{"index":"secisland","alias":"alias1"}}] }

**删除别名，请求与上述一样,参数不同**
```
{
    "actions":[{"remove":{"index":"test1","alias":"alias1"}}]
}
```
注意:别名没有修改语法,修改别名时,可以先删除别名,再添加
```
{
    "actions":[
        {"remove":{"index","test1","alias":"alias1"}},
        {"add":{"index":"test1","alias":"alias2"}}
    ]
}
```
一个别名也可以关联多个索引
```
{
    "actions":[
        {"add":{"index":"test1","alias":"alias1"}},
        {"add":{"index":"test2","alias":"alias1"}}
    ]
}
```
也可以用以下语法
```
{ "actions":[ {"add":{"indices":["test1","test2"],"alias":"alias1"}} ] }
```
或者使用通配符
```
{
    "actions":[
        {"add":{"index":"test*","alias":"all_test_indecies"}}
    ]
}
```
注意:通配符指定的索引只在当前索引生效,后续添加索引不会被自动添加到别名上。对某一别名做索引的时候,如果该别名添加多个索引就会报错

**过滤索引别名**
该过滤去可以使用查询DSL来定义适用于所有的搜索,计数,查询删除等,以及更多类似这样的与此别名的操作。

创建过滤别名,请求:POST http://127.0.0.1:9200/_aliases
```
{
	"actions":[
		{"add":{"index":"test1","alias":"alias1","filter":{
					"term":{"user":"kimchy"}}}}
		}	
	]
}
```

通过别名也可以和路由关联,此功能可以和过滤别名命令一起使用
请求:POST http://127.0.0.1:9200/_aliases
```
{
	"actions":[
		{"add":{"index":"test1","alias":"alias1","routing":"1"}}
	]
}
```

同时可以指定搜索路由和查询路由
```
{
	"actions":[
		{"add":{"index":"test","alias":"alias2","search_routing":"1,2","index_routing":"2"}}
	]
}
```
注意:搜索路由可以指定多个值,索引路由只能指定一个值.如果使用路由别名操作的同时还有路由参数,则结果时别名路由和路由的交集

### Elasticsearch迁移
问题：如何解决不停服切换索引

当前系统在创建好一个 index 时, 下面的字段类型以及分词器是不支持修改的,如果修改只得去先删除索引，然后再重新按照新的字段去创建新的 index，最后导入数据后再进行操作。

在生产环境中，这样做是及其不现实的，某些索引数据量大，再重新导入时间需要消耗大量的时间。而且在这个过程中，集群是没有办法对外提供这个索引的搜索服务。

好在 ES 为我们提供了别名的概念 aliases，也就是我们在生成一个索引的时候，比如叫 my_index_v1，我们是可以做一个别名 my_index 指向它。

当我们在查询 my_index 的时间时，实际上查询是 my_index_v1 这个索引的数据。

那么在遇到有字段调整或者分词器发生变化时，我们就可以这样来做了。

我们可以先生成一个别名 my_index 指向 my_index_v1，业务系统去查询时直接使用 my_index 去做查询，当后台字段发生变化，或者字段类型和分词器等发生变化，我们就可以再生成一个新的 index，比如叫 my_index_v2 。

然后 把 my_index_v1 的数据 导入到 my_index_v2 中。最后删除 my_index 对于 my_index_v1 的指向，重新把 my_index 指向到 my_index_v2 。

在这个过程中，应用系统在切换完成前查询的旧数据，切换后查询的是新数据，应用就可以做到不停机。

对于我们之前的当前业务系统来说，暂时只是一个别名指向到一个 index 中。虽然别名可以指向到多个实际的 index 。


### ES为什么不能做主数据库（如何更新mapping字段类型）
es没有事务，而且是近实时。成本也比数据库高，几乎靠吃内存提高性能。最逆天的是，mapping不能改（要么新增mapping字段做迁移，要么重建新的index做拷贝）。 ES团队不推荐完全采用ES作为主要存储，缺乏访问控制还有一些数据丢失和污染的问题。

- es index的mapping在建好之后是不可以更改字段类型的，所以直接将mapping类型是行不通的。
  - 解决：
    - 重建mapping
    这是大多推荐的方式，重建新的index，然后建好全部字段的mapping，再把数据拷贝完成重新mapping。（如果index的type少、数据量也不大，可使用这种方式。如果线上库index里各种type都有，牵扯的业务繁多，而改的只是其中的一个type，谨慎使用）
    - 新建mapping字段
    虽然index的mapping在建好之后不可以更改字段类型，但是可以添加新的字段，将原有值重新赋值到新字段中


### Elasticsearch查询索引信息
查询方式：IP:端口/索引
http://127.0.0.1:9200/log_index?pretty

````
{
	"log_index": {  //索引
		"aliases": {},
		"mappings": {
			"doc": {    //类型（表名）
				"properties": { //属性集合
					"@timestamp": {
						"type": "date"
					},
					"@version": {
                    。。。
                    省略剩余属性字段
                    。。。
					}
				}
			}
		},
		"settings": {   //配置信息，基本见名知意
			"index": {
				"creation_date": "1605172001863",
				"number_of_shards": "5",
				"number_of_replicas": "1",
				"uuid": "7n38yZ9zS2uEw_gXoqi5Tg",
				"version": {
					"created": "6050399"
				},
				"provided_name": "log_index"
			}
		}
	}
}
````

### Elasticsearch查询索引下所有数据（请求参数方式（http GET））
查询方式：IP:端口/索引/_search
http://127.0.0.1:9200/log_index/_search?pretty

````
{
  "took" : 13,  //花费时长（毫秒）
  "timed_out" : false,  //是否超时  
  "_shards" : {     //分片信息  
    "total" : 5,    //总分片大小
    "successful" : 5,   //成功数
    "skipped" : 0,      //跳过数
    "failed" : 0        //失败数
  },
  "hits" : {        //命中的数据
    "total" : 5971204,  //总大小（当前一页只展示10条）
    "max_score" : 1.0,  //最大命中分值
    "hits" : [          //数据集合
      {
        "_index" : "log_index",     //索引信息
        "_type" : "doc",                //类型（表名）
        "_id" : "0-ydv3UBmFDDms1zn_tc", //数据ID
        "_score" : 1.0,                 //分值
        "_source" : {                   //元数据
          "a" : "avalue",
          "b" : "bvalue"
        }
      },
      。。。省略剩余数据
    ]
  }
}
````


### Elasticsearch查询索引下单条数据（请求参数方式（http GET））
查询方式：IP:端口/索引/类型（表）/ID
http://127.0.0.1:9200/log_index/log_type/nZjV33UBmFDDms1zHk59

````
{
    "_index":"log_index",  //索引
    "_type":"doc",              //类型(表)
    "_id":"nZjV33UBmFDDms1zHk59",//数据ID
    "_version":1,
    "found":true,               //是否找到结果
    "_source":{                 //查询到的数据
        "platform":1,"@timestamp":"2020-11-19T09:28:07.361Z","versionCode":"15700","sendTime":1605778087297,"logType":"10010","uid":"CNFfqy0VdR2v","requestId":"979ac6c4-c6f0-4632-a2d4-85f963fa2faf","name":"短视频","packageName":"com.coloros.yoli","udid":"A00000890A375C","timestamp":"1605778065669","@version":"1","umid":"","appName":"PLATFORM_CJCYP_ANDROID","certSha1":"58E1A75D9A9083EE226AFDEAA7A71997955122FC","ip":"172.17.32.11","pid":"68369fc334a90b1f8978c58a000960a3","versionName":"1.5.7","projectId":"cheyipai"
    }
}
````

### Elasticsearch多索引，多type搜索（请求参数方式（http GET））

在URL中指定特殊的索引和类型进行多索引，多type搜索
````
/_search：在所有的索引中搜索所有的类型
/school/_search：在 school 索引中搜索所有的类型
/school,ad/_search：在 school 和ad索引中搜索所有的类型
/s*,a*/_search：在所有以g和a开头的索引中所有所有的类型
/school/student/_search：在school索引中搜索student类型
/school,ad/student,phone/_search：在school和ad索引上搜索student和phone类型
/_all/student,phone/_search：在所有的索引中搜索student和phone类型
````


### Elasticsearch条件查询（请求参数方式（http GET）

命令：GET /school/student/_search?q=name:houyi
查询name是houyi的记录


### Elasticsearch查询索引下所有数据（请求体方式）（推荐）
````
curl -H "Content-Type: application/json" -X POST '127.0.0.1:9200/log_index/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'


复合查询,比如一个bool语句，包括must，must_not，should和filter语句（多条件组合查询）
GET /ad/phone/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "name": "phone"
        }}
      ]
      , "must_not": [
        {"match": {
          "color": "red"
        }}
      ]
      , "should": [
        {"match": {
          "price": 5000
        }}
      ]
      , "filter": {
          "term": {
            "label": "phone"
          }
      }
    }
  }
}

````

### ELK——Elasticsearch、Logstash、Kibana收集处理日志
ELK即Elasticsearch、Logstash、Kibana,组合起来可以搭建线上日志系统

---

### ElasticSearch客户端（ SpringData-ElasticSearch代码实现）
编写es客户端提供者类，对es连接做简单的封装

#### 实体注解说明
注解说明：
@Document(indexName = "pms", type = "advert",shards = 1,replicas = 0)

    @Document：映射到es文档的领域对象
    //索引库名次，mysql中数据库的概念
        String indexName();
      //文档类型，mysql中表的概念
        String type() default "";
      //默认分片数
        short shards() default 5;
      //默认副本数量
        short replicas() default 1;

@Id：mysql中表行的概念

@Field：

      //文档中字段的类型
        FieldType type() default FieldType.Auto;
      //是否建立倒排索引
        boolean index() default true;
      //是否进行存储
        boolean store() default false;
      //分词器名次
        String analyzer() default "";
      //为文档自动指定元数据类型FieldType
        public enum FieldType {
            Text,//会进行分词并建了索引的字符类型
            Integer,
            Long,
            Date,
            Float,
            Double,
            Boolean,
            Object,
            Auto,//自动判断字段类型
            Nested,//嵌套对象类型
            Ip,
            Attachment,
            Keyword//不会进行分词建立索引的类型
}

不需要中文分词的字段设置成@Field(type = FieldType.Keyword)类型，需要中文分词的设置成@Field(analyzer = "ik_max_word",type = FieldType.Text)类型。

#### SpringData方式的数据操作：
1.继承ElasticsearchRepository接口可以获得常用的数据操作方法
2.衍生查询
在接口中直接指定查询方法名称便可查询，无需进行实现，如商品表中有商品名称、标题和关键字，直接定义以下查询，就可以对这三个字段进行全文搜索。
Page<EsProduct> findByNameOrSubTitleOrKeywords(String name, String subTitle, String keywords, Pageable page);
3.使用@Query注解可以用Elasticsearch的DSL语句进行查询
@Query("{"bool" : {"must" : {"field" : {"name" : "?0"}}}}")
 Page<EsProduct> findByName(String name,Pageable pageable);

#### 依赖（注意Springboot版本和ES的版本）
````
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-data-elasticsearch<artifactId>
 <version>2.0.6.RELEASE</version>
</dependency>
````

#### 配置文件
````
data: 
  elasticsearch: 
    repositories: 
      enabled: true 
    cluster-nodes: 127.0.0.1:9300  # es的连接地址及端口号 
    cluster-name: elasticsearch # es集群的名称
````

#### 调试
添加所有/单个，更新=添加
删除
简单搜索：多字段关键字匹配（已完成）
搜索：分页，多字段关键字匹配+权重分配，排序
商品推荐：查询商品并匹配此商品相关商品

综合搜索+权重+排序：
````
        Pageable pageable = PageRequest.of(pageNum, pageSize);
        NativeSearchQueryBuilder nativeSearchQueryBuilder = new NativeSearchQueryBuilder();
        //分页
        nativeSearchQueryBuilder.withPageable(pageable);
        //过滤
            BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
            boolQueryBuilder.must(QueryBuilders.termQuery("brandId", brandId));
            nativeSearchQueryBuilder.withFilter(boolQueryBuilder);
        //搜索***********相关度和权重
            List<FunctionScoreQueryBuilder.FilterFunctionBuilder> filterFunctionBuilders = new ArrayList<>();
            filterFunctionBuilders.add(new FunctionScoreQueryBuilder.FilterFunctionBuilder(QueryBuilders.matchQuery("name", keyword),
                    ScoreFunctionBuilders.weightFactorFunction(10)));	//配置权重
            filterFunctionBuilders.add(new FunctionScoreQueryBuilder.FilterFunctionBuilder(QueryBuilders.matchQuery("subTitle", keyword),
                    ScoreFunctionBuilders.weightFactorFunction(5)));
            filterFunctionBuilders.add(new FunctionScoreQueryBuilder.FilterFunctionBuilder(QueryBuilders.matchQuery("keywords", keyword),
                    ScoreFunctionBuilders.weightFactorFunction(2)));
            FunctionScoreQueryBuilder.FilterFunctionBuilder[] builders = new FunctionScoreQueryBuilder.FilterFunctionBuilder[filterFunctionBuilders.size()];
            filterFunctionBuilders.toArray(builders);
            FunctionScoreQueryBuilder functionScoreQueryBuilder = QueryBuilders.functionScoreQuery(builders)
                    .scoreMode(FunctionScoreQuery.ScoreMode.SUM)
                    .setMinScore(2);
            nativeSearchQueryBuilder.withQuery(functionScoreQueryBuilder);
        //排序
            nativeSearchQueryBuilder.withSort(SortBuilders.fieldSort("id").order(SortOrder.DESC));	//按新品从新到旧
            nativeSearchQueryBuilder.withSort(SortBuilders.scoreSort().order(SortOrder.DESC));	//按相关度
        //执行查询
        NativeSearchQuery searchQuery = nativeSearchQueryBuilder.build();
        return productRepository.search(searchQuery);
````



---

### ElasticSearch分页查询代码实现：
ES 分页搜索一般有三种方案，from + size、search after、scroll api，这三种方案分别有自己的优缺点，下面将进行分别介绍。

1. from + size
   这是ES分页中最常用的一种方式，与MySQL类似，from指定起始位置，size指定返回的文档数。
```
GET kibana_sample_data_flights/_search
{
  "from": 10,
  "size": 2, 
  "query": {
    "match": {
      "DestWeather": "Sunny"
    }
  },
  "sort": [
    {
      "timestamp": {
        "order": "asc"
      }
    }
  ]
}
```
使用简单，且默认的深度分页限制是1万，from + size 大于 10000会报错，可以通过index.max_result_window参数进行修改。

2. search after
   search after 利用实时有游标来帮我们解决实时滚动的问题。第一次搜索时需要指定 sort，并且保证值是唯一的，可以通过加入 _id 保证唯一性。
```
GET kibana_sample_data_flights/_search
{
  "size": 2, 
  "query": {
    "match": {
      "DestWeather": "Sunny"
    }
  },
  "sort": [
    {
      "timestamp": {
        "order": "asc"
      },
      "_id": {
        "order": "desc"
      }
    }
  ]
}
```
在返回的结果中，最后一个文档有类似下面的数据，由于我们排序用的是两个字段，返回的是两个值。
```
"sort" : [
  1614561419000,
  "6FxZJXgBE6QbUWetnarH"
]
```
第二次搜索，带上这个sort的信息即可，如下
```
GET kibana_sample_data_flights/_search
{
  "size": 2,
  "query": {
    "match": {
      "DestWeather": "Sunny"
    }
  },
  "sort": [
    {
      "timestamp": {
        "order": "asc"
      },
      "_id": {
        "order": "desc"
      }
    }
  ],
  "search_after": [
    1614561419000,
    "6FxZJXgBE6QbUWetnarH"
  ]
}
```

3. scroll api
   创建一个快照，有新的数据写入以后，无法被查到。每次查询后，输入上一次的 scroll_id。目前官方已经不推荐使用这个API了，使用search_after即可。
```
GET kibana_sample_data_flights/_search?scroll=1m
{
  "size": 2,
  "query": {
    "match": {
      "DestWeather": "Sunny"
    }
  },
  "sort": [
    {
      "timestamp": {
        "order": "asc"
      },
      "_id": {
        "order": "desc"
      }
    }
  ]
}
```
在返回的数据中，有一个_scroll_id字段，下次搜索的时候带上这个数据，并且使用下面的查询语句。
```
POST _search/scroll
{
  "scroll" : "1m",
  "scroll_id" : "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAA6UWWVJRTk9TUXFTLUdnU28xVFN6bEM4QQ=="
}
```
上面的scroll指定搜索上下文保留的时间，1m代表1分钟，还有其他时间可以选择，有d、h、m、s等，分别代表天、时、分钟、秒。

搜索上下文有过期自动删除，但如果自己知道什么时候该删，可以自己手动删除，减少资源占用。
```
DELETE /_search/scroll
{
  "scroll_id" : "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAA6UWWVJRTk9TUXFTLUdnU28xVFN6bEM4QQ=="
}
```

总结
from + size 的优点是简单，缺点是在深度分页的场景下系统开销比较大。

search after 可以实时高效的进行分页查询，但是它只能做下一页这样的查询场景，不能随机的指定页数查询。

scroll api 方案也很高效，但是它基于快照，不能用在实时性高的业务场景，且官方已不建议使用。


### Es原生查询：

#### 依赖
```
	<properties>
		<elasticsearch.version>7.3.2</elasticsearch.version>
	</properties>
	
		<!-- es -->
		<dependency>
			<groupId>org.elasticsearch</groupId>
			<artifactId>elasticsearch</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch.client</groupId>
			<artifactId>elasticsearch-rest-high-level-client</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch.client</groupId>
			<artifactId>elasticsearch-rest-client</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch</groupId>
			<artifactId>elasticsearch-core</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch</groupId>
			<artifactId>elasticsearch-x-content</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>

		<dependency>
			<groupId>org.apache.shardingsphere.elasticjob</groupId>
			<artifactId>elasticjob-lite-core</artifactId>
			<version>3.0.0-beta</version>
		</dependency>
		<dependency>
			<groupId>org.apache.shardingsphere.elasticjob</groupId>
			<artifactId>elasticjob-lite-spring-namespace</artifactId>
			<version>3.0.0-beta</version>
		</dependency>
		<!--es jenkins编译失败扫此包，手动加上-->
		<dependency>
			<groupId>org.elasticsearch</groupId>
			<artifactId>elasticsearch-secure-sm</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch.plugin</groupId>
			<artifactId>parent-join-client</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.apache.lucene</groupId>
			<artifactId>lucene-core</artifactId>
			<version>8.1.0</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch.plugin</groupId>
			<artifactId>lang-mustache-client</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch.plugin</groupId>
			<artifactId>rank-eval-client</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<dependency>
			<groupId>org.elasticsearch.plugin</groupId>
			<artifactId>aggs-matrix-stats-client</artifactId>
			<version>${elasticsearch.version}</version>
		</dependency>
		<!--结束-->
```

#### 配置：
```
es:
  nodes:
    - 192.168.161.215:9200
    - 192.168.161.33:9200
  username: elastic
  password: ddqc.12345
```

#### 配置类
配置读取：
```
@ConfigurationProperties(prefix = "es")
@Component
@Data
public class EsProperties {
  private List<String> nodes;
  private String username;
  private String password;
}
```

客户端配置：
```
@Configuration
public class RestClientConfig {

  @Autowired
  private EsProperties esProperties;

  @Bean
  public RestHighLevelClient elasticsearchClient() {
    RestHighLevelClient client = new RestHighLevelClient(buildRestClientBuilder());
    return client;
  }

  @Bean
  public RestClientBuilder buildRestClientBuilder() {
    final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
    credentialsProvider
        .setCredentials(AuthScope.ANY, new UsernamePasswordCredentials(esProperties.getUsername(),
            esProperties.getPassword()));
    HttpHost[] httpHosts = new HttpHost[esProperties.getNodes().size()];
    for (int i = 0; i < esProperties.getNodes().size(); i++) {
      String host = esProperties.getNodes().get(i);
      Iterable<String> nodeStr = Splitter.on(":").split(host);
      HttpHost httpHost = new HttpHost(Iterables.get(nodeStr, 0),
          Integer.parseInt(Iterables.get(nodeStr, 1)));
      httpHosts[i] = httpHost;
    }
    RestClientBuilder builder = RestClient.builder(httpHosts);
    builder.setHttpClientConfigCallback(b -> b.setDefaultCredentialsProvider(credentialsProvider));
    return builder;
  }
}
```

**执行查询三步骤：**

ScrollId游标查询
```
	@Autowire
	private RestHighLevelClient restHighLevelClient;

	//1.构造SearchSourceBuilder
    SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
    //ES term语句
    sourceBuilder.query(
        QueryBuilders.boolQuery().must(QueryBuilders.termQuery("id", id))
        QueryBuilders.boolQuery().must(QueryBuilders.rangeQuery("startTime").gte(orderQueryRequest.getStartTimeStart()));
    );
          
    //查询分页大小（from + size默认的深度分页限制是1万，大于1万使用scrollId）
    sourceBuilder.from(0);
    sourceBuilder.size(10);
    sourceBuilder.sort("createTime", SortOrder.DESC);
    //超时
    sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
    
	//2.构造SearchRequest
	SearchRequest searchRequest = new SearchRequest();
	//指定索引
    searchRequest.indices(IndexConstants.ORDER_CONSUME_INDEX);
    //关联游标
    Scroll scroll = new Scroll(TimeValue.timeValueMinutes(scrollTime));
    searchRequest.scroll(scroll);
    //关联sourceBuilder
    searchRequest.source(sourceBuilder);

	//3.查询并解析
	SearchResponse searchResponse = null;
	//第一种：判断小于10000使用内存分页，大于10000使用游标
	if(pageNum*pageSize <= 10000 && pageSize != -1){
		searchRequest.scroll((Scroll)null);
		sourceBuilder.from((pageNum-1)*pageSize);
		SearchResponse response = elasticClient.search(searchRequest, RequestOptions.DEFAULT);
	}
	
	//第二种：直接使用游标
	if(StringUtils.isEmpty(orderDetailRequest.getScrollId())){
		searchResponse=restHighLevelClient.search(searchRequest,RequestOptions.DEFAULT);
	}else {
        SearchScrollRequest scrollRequest = new SearchScrollRequest(orderDetailRequest.getScrollId());
        scrollRequest.scroll(scroll);
        searchResponse = restHighLevelClient.scroll(scrollRequest, RequestOptions.DEFAULT);
    }
	//解析数据
    SearchHits hits = response.getHits();
    SearchHit[] searchHits = hits.getHits();
    BigEsOrderModel model = null;
    for (SearchHit sh : searchHits) {
      model = JSON.parseObject(sh.getSourceAsString(), BigEsOrderModel.class);
    }
    //searchResponse.getHits().getTotalHits().value为记录总条数        
    PageResultResponse pageResult = PageResultResponse.of(responses,
            searchResponse.getHits().getTotalHits().value, orderDetailRequest.getPageSize(),
            orderDetailRequest.getPageNum(), searchResponse.getScrollId());
    return model;
```

searchAfter查询：
```
	//1.构造SearchSourceBuilder
    SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
    //ES term语句
    sourceBuilder.query(
        QueryBuilders.boolQuery().must(QueryBuilders.termQuery("id", id))
    );
    sourceBuilder.size(10);
    sourceBuilder.sort("createTime", SortOrder.DESC);
    //超时
    sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
    //判断添加searchAfter
    if(StringUtils.isNotBlank(orderDetailRequest.getScrollId())){
      sourceBuilder.searchAfter(orderDetailRequest.getScrollId);
    }
    
	//2.构造SearchRequest
	SearchRequest searchRequest = new SearchRequest();
	//指定索引
    searchRequest.indices(IndexConstants.ORDER_CONSUME_INDEX);
    //关联sourceBuilder
    searchRequest.source(sourceBuilder);

	//3.查询并解析
	SearchResponse searchResponse = null;
	//第一种：判断小于10000使用内存分页，大于10000使用游标
	if(pageNum*pageSize <= 10000 && pageSize != -1){
		searchRequest.scroll((Scroll)null);
		sourceBuilder.from((pageNum-1)*pageSize);
		SearchResponse response = elasticClient.search(searchRequest, RequestOptions.DEFAULT);
	}
	//解析数据
    SearchHits hits = response.getHits();
    SearchHit[] searchHits = hits.getHits();
    BigEsOrderModel model = null;
    for (SearchHit sh : searchHits) {
      model = JSON.parseObject(sh.getSourceAsString(), BigEsOrderModel.class);
    }
    //searchResponse.getHits().getTotalHits().value为记录总条数        
    PageResultResponse pageResult = PageResultResponse.of(responses,
            searchResponse.getHits().getTotalHits().value, orderDetailRequest.getPageSize(),
            orderDetailRequest.getPageNum(), searchResponse.getScrollId());
    return model;
```

### ES写数据
根据ID保存或更新：
```
	//查询对象并更新索引ID更新对象
    BigEsOrderModel model = bigOrderBuildBussiness.bigOrderBuilder(ordOrderConsumeEntity,null);
    IndexRequest request = new IndexRequest(IndexConstants.ORDER_CONSUME_INDEX)
        .id(String.valueOf(id))
        .source(JSON.toJSONString(model), XContentType.JSON);
    try {
      elasticClient.index(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
      log.error("更新文档失败", e);
    }
```

批量写数据
```
	//循环遍历list写数据
	BulkRequest request = new BulkRequest();
    list.forEach(item ->
        request.add(new IndexRequest(IndexConstants.ORDER_CONSUME_INDEX).id(item.getId().toString())
            .source(JSON.toJSONString(item), XContentType.JSON)));
    //批量提交
    try {
      elasticClient.bulk(request,RequestOptions.DEFAULT);
      log.info("批量同步索引成功");
    } catch (IOException e) {
      log.error("批量同步失败");
    }
```

批量写数据或更新数据：
```
	//IndexRequest定义的是添加，UpdateRequest如果已存在则更新，如果不存在则创建
	BulkRequest request = new BulkRequest();
    list.forEach(socBody ->{
      UpdateRequest updateRequest = new UpdateRequest(IndexConstants.ORDER_SOC_INDEX, MigrateConstants.ES_TYPE_DOC, socBody.getBatch());
      request.add(updateRequest
            .id(String.valueOf(socBody.getBatch()))
            .doc(JSON.toJSONString(socBody), XContentType.JSON));
    });
    //批量提交
    try {
      elasticClient.bulk(request,RequestOptions.DEFAULT);
      log.info("批量同步soc索引成功");
    } catch (IOException e) {
      log.error("批量同步soc失败");
    }
```

### ES创建索引和mapping
创建索引和分片：
```
public class ElasticSearchTest {

  private RestHighLevelClient client;

  @Before
  public void setUp() {
    RestClientBuilder builder = RestClient.builder(
        new HttpHost("192.168.161.215", 9200, "http"),
        new HttpHost("192.168.161.33", 9200, "http"));
    client = new RestHighLevelClient(builder);
  }

  @Test
  public void setCreateIndex() throws IOException {
    CreateIndexRequest request = new CreateIndexRequest("orderconsume");
    request.settings(Settings.builder()
        .put("index.number_of_shards", 5)
        .put("index.number_of_replicas", 1)
    );
    client.indices().create(request, RequestOptions.DEFAULT);
  }

```

创建mapping
```
@Test
public void testPutMapping() throws IOException {
  PutMappingRequest request = new PutMappingRequest("orderpro");
  Map<String, Object> jsonMap = new HashMap<>();
  Map<String, Object> message = new HashMap<>();
  message.put("type", "long");
  Map<String, Object> corp = new HashMap<>();
  corp.put("type", "keyword");
  corp.put("index",false);
  corp.put("doc_values", false);
  Map<String, Object> properties = new HashMap<>();
  properties.put("orderId", message);
  properties.put("corp",corp);
  Map<String,Object> orderAttr = new HashMap<>();
  Map<String,Object> itemMap = Maps.newHashMap();
  Map<String,Object> itemJsonMap = Maps.newHashMap();
  itemMap.put("type","integer");
  itemJsonMap.put("age",itemMap);
  Map<String,Object> nameMap = new HashMap<>();
  nameMap.put("type","text");
  itemJsonMap.put("name",nameMap);
  orderAttr.put("properties",itemJsonMap);
  properties.put("orderAttr",orderAttr);
  orderAttr.put("type", "nested");
  jsonMap.put("properties", properties);
  request.source(jsonMap);
  client.indices().putMapping(request, RequestOptions.DEFAULT);
}
```

### ElasticSearch监控工具-cerebro
1. 下载解压
   https://github.com/lmenezes/cerebro/releases

```
wget https://github.com/lmenezes/cerebro/releases/download/v0.8.1/cerebro-0.8.1.tgz
tar xzf cerebro-0.8.1.tgz
```
2. 启动
```
cerebro-0.8.1/bin/cerebro
[info] play.api.Play - Application started (Prod)
[info] p.c.s.AkkaHttpServer - Listening for HTTP on /0:0:0:0:0:0:0:0:9000
```

3. 指定端口
   bin/cerebro -Dhttp.port=8080

4. 配置服务器
   非必须：如果经常使用的话，可以先在conf/application.conf中配置好ElasticSearch服务器地址

```
hosts = [
  {
    host = "http://localhost:9200"
    name = "Some Cluster"
  },
  # Example of host with authentication
  #{
  #  host = "http://some-authenticated-host:9200"
  #  name = "Secured Cluster"
  #  auth = {
  #    username = "username"
  #    password = "secret-password"
  #  }
  #}
]
```

5. 使用
   浏览器打开连接http://192.168.58.101:9000

















