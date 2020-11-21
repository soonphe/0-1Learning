# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## ElasticSearch

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
    访问http://localhost:5601 即可打开Kibana的用户界面

Linux安装步骤：

    解压文件 elasticsearch-2.4.0.zip
    
    修改配置文件
    elasticsearch-2.4.0  cat  config /elasticsearch .yml | grep  - v  "#"
    cluster.name: rainbow
    network.host: 127.0.0.1
    http.port: 9200
    
    配置说明
    cluster.name表示es集群的名称，可以自定义一个自己需要的集群名称
    http.port 表示对外提供http服务时的http端口。
    network.host 表示本地监听绑定的ip地址，此处为测试环境，直接使用本机的ip地址 127.0.0.1.
    
    启动说明
    elasticsearch-2.4.0  nohup  . /bin/elasticsearch  &

### Elasticsearch启动监听两个端口，9300和9200
9300端口是使用tcp客户端连接使用的端口；（后台client访问）
9200端口是通过http协议连接es使用的端口；（网页或curl访问）

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


### Elasticsearch查询配置信息
127.0.0.1:9200 打开可获取Elasticsearch配置信息

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

### Elasticsearch查询集群状态
1.查看集群的健康状态。
http://127.0.0.1:9200/_cat/health?v

    集群的状态（status）：red红表示集群不可用，有故障。yellow黄表示集群不可靠但可用，一般单节点时就是此状态。green正常状态，表示集群一切正常。
    节点数（node.total）：节点数，这里是2，表示该集群有两个节点。
    数据节点数（node.data）：存储数据的节点数，这里是2。数据节点在Elasticsearch概念介绍有。
    分片数（shards）：这是12，表示我们把数据分成多少块存储。
    主分片数（pri）：primary shards，这里是6，实际上是分片数的两倍，因为有一个副本，如果有两个副本，这里的数量应该是分片数的三倍，这个会跟后面的索引分片数对应起来，这里只是个总数。
    激活的分片百分比（active_shards_percent）：这里可以理解为加载的数据分片数，只有加载所有的分片数，集群才算正常启动，在启动的过程中，如果我们不断刷新这个页面，我们会发现这个百分比会不断加大

2.查看集群的索引数。
http://127.0.0.1:9200/_cat/indices?v

    索引健康（health），green为正常，yellow表示索引不可靠（单节点），red索引不可用。与集群健康状态一致。
    状态（status），表明索引是否打开。
    索引名称（index），这里有.kibana和school。
    uuid，索引内部随机分配的名称，表示唯一标识这个索引。
    主分片（pri），.kibana为1，school为5，加起来主分片数为6，这个就是集群的主分片数。
    文档数（docs.count），school在之前的演示添加了两条记录，所以这里的文档数为2。
    已删除文档数（docs.deleted），这里统计了被删除文档的数量。
    索引存储的总容量（store.size），这里school索引的总容量为6.4kb，是主分片总容量的两倍，因为存在一个副本。
    主分片的总容量（pri.store.size），这里school的主分片容量是7kb，是索引总容量的一半。

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


### Elasticsearch增加数据
增加数据（http PUT）：
````
curl -XPUT localhost:9200 /user_idx/type_tags/12  -d
'{"name" : "Mr.YF", "tags" : ["Go","Java","Lua","C++","Tcl","..."]}'
这里是返回的结果：
{ "_index" : "user_idx" , "_type" : "type_tags" , "_id" : "12" , "_version" :1, "_shards" :{ "total" :2, "successful" :1, "failed" :0}, "created" : true }

还可以添加json数据：
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary  @accounts.json
````
参数分析：

    -d标识要传递的参数
    bank是索引的名称
    account是类型的名称
    索引和类型的名称在文件中如果有定义，可以省略；如果没有则必须要指定
    _bulk是rest的命令，可以批量执行多个操作（操作是在json文件中定义的，原理可以参考之前的翻译）
    pretty是将返回的信息以可读的JSON形式返回。


### Elasticsearch查询索引信息
查询方式：IP:端口/索引
http://127.0.0.1:9200/logtype-10010?pretty

````
{
	"logtype-10011": {  //索引
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
				"provided_name": "logtype-10011"
			}
		}
	}
}
````

### Elasticsearch查询索引下所有数据（请求参数方式（http GET））
查询方式：IP:端口/索引/_search
http://127.0.0.1:9200/logtype-10011/_search?pretty

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
        "_index" : "logtype-10011",     //索引信息
        "_type" : "doc",                //类型（表名）
        "_id" : "0-ydv3UBmFDDms1zn_tc", //数据ID
        "_score" : 1.0,                 //分值
        "_source" : {                   //元数据
          "platform" : 1,
          "@timestamp" : "2020-11-13T03:19:37.476Z",
          "versionCode" : "10",
          "sendTime" : 1605237577308,
          "logType" : "10011",
          "uid" : "CNAmw3b0uziL",
          "requestId" : "d61af6cb-1d85-4936-a735-733a2af32acb",
          "name" : "交通微云卡",
          "packageName" : "com.vfuchongcontrol",
          "udid" : "867520049610057",
          "timestamp" : "1605237571577",
          "@version" : "1",
          "umid" : "",
          "appName" : "PLATFORM_CJCYP_ANDROID",
          "certSha1" : "252E6073B3C34A025019F592396AFC16B0BEFFEF",
          "ip" : "172.17.32.11",
          "pid" : "68369fc334a90b1f8978c58a000960a3",
          "versionName" : "1.3.0",
          "projectId" : "cheyipai"
        }
      },
      。。。省略剩余数据
    ]
  }
}
````


### Elasticsearch查询索引下单条数据（请求参数方式（http GET））
查询方式：IP:端口/索引/类型（表）/ID
http://127.0.0.1:9200/logtype-10011/doc/nZjV33UBmFDDms1zHk59

````
{
    "_index":"logtype-10011",  //索引
    "_type":"doc",              //类型(表)
    "_id":"nZjV33UBmFDDms1zHk59",//数据ID
    "_version":1,
    "found":true,               //是否找到结果
    "_source":{                 //查询到的数据
        "platform":1,"@timestamp":"2020-11-19T09:28:07.361Z","versionCode":"15700","sendTime":1605778087297,"logType":"10011","uid":"CNFfqy0VdR2v","requestId":"979ac6c4-c6f0-4632-a2d4-85f963fa2faf","name":"短视频","packageName":"com.coloros.yoli","udid":"A00000890A375C","timestamp":"1605778065669","@version":"1","umid":"","appName":"PLATFORM_CJCYP_ANDROID","certSha1":"58E1A75D9A9083EE226AFDEAA7A71997955122FC","ip":"172.17.32.11","pid":"68369fc334a90b1f8978c58a000960a3","versionName":"1.5.7","projectId":"cheyipai"
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
curl -H "Content-Type: application/json" -X POST '127.0.0.1:9200/logtype-10011/_search?pretty' -d '
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

---

### Elasticsearch客户端（编写java代码访问es）
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


### ELK——Elasticsearch、Logstash、Kibana收集处理日志
ELK即Elasticsearch、Logstash、Kibana,组合起来可以搭建线上日志系统


### Kibana
kibana默认端口5601

使用方式参考文档：https://blog.csdn.net/cb2474600377/article/details/78963247













