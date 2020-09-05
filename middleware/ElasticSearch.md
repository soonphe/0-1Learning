# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## ElasticSearch

1.下载Elasticsearch6.2.2的zip包，并解压到指定目录，下载地址：https://www.elastic.co/cn/downloads/past-releases/elasticsearch-6-2-2
2.安装中文分词插件，在elasticsearch-6.2.2\bin目录下执行以下命令：
elasticsearch-plugin install 
https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.2/elasticsearch-analysis-ik-6.2.2.zip
3.下载kibana做为es客户端
https://artifacts.elastic.co/downloads/kibana/kibana-6.2.2-windows-x86_64.zip
4.启动ES和kibana，
访问http://localhost:5601 即可打开Kibana的用户界面

注解说明：
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







SpringData方式的数据操作：
1.继承ElasticsearchRepository接口可以获得常用的数据操作方法
2.衍生查询
在接口中直接指定查询方法名称便可查询，无需进行实现，如商品表中有商品名称、标题和关键字，直接定义以下查询，就可以对这三个字段进行全文搜索。
Page<EsProduct> findByNameOrSubTitleOrKeywords(String name, String subTitle, String keywords, Pageable page);
3.使用@Query注解可以用Elasticsearch的DSL语句进行查询
@Query("{"bool" : {"must" : {"field" : {"name" : "?0"}}}}")
 Page<EsProduct> findByName(String name,Pageable pageable);

依赖：
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-data-elasticsearch<artifactId>
 </dependency>


配置文件：
data: 
  elasticsearch: 
    repositories: 
      enabled: true 
    cluster-nodes: 127.0.0.1:9300  # es的连接地址及端口号 
    cluster-name: elasticsearch # es集群的名称

调试：
添加所有/单个，更新=添加
删除
简单搜索：多字段关键字匹配（已完成）
搜索：分页，多字段关键字匹配+权重分配，排序
商品推荐：查询商品并匹配此商品相关商品

综合搜索+权重+排序：
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



ELK收集处理日志
ELK即Elasticsearch、Logstash、Kibana,组合起来可以搭建线上日志系统


原生查询语句：
{
  "query": {
    "match_all": {}
  },
  "size": 100
}













