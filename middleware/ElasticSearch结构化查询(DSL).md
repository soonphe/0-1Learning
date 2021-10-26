# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## ElasticSearch结构化查询（DSL）

es中的查询请求有两种方式，一种是简易版的查询，另外一种是使用JSON完整的请求体，叫做结构化查询（DSL）。
由于DSL查询更为直观也更为简易，所以大都使用这种方式。
DSL查询是POST过去一个json，由于post的请求是json格式的，所以存在很多灵活性，也有很多形式


### 1. 获取所有

GET /ad/phone/_search
{
  "query": {
    "match_all": {}
  }
}

### 2. 分页查询，从第二条开始，查两条（不要使用from，size进行深度分页，会有性能问题）
GET /ad/phone/_search
{
  "query": {
    "match_all": {}
  },
  "from": 1,
  "size": 2
}
这种分页方式如果进行深度分页，比如到100页，每页十条数据，它会从每个分片都查询出100*10条数据，假设有五个分片，就是5000条数据，然后在内存中进行排序，然后返回拍过序之后的集合中的第1000-1010条数据

### 3. 指定查询出来的数据返回的字段
GET /ad/phone/_search
{
  "query": {
    "match_all": {}
  },
  "_source": ["name","price"]
}
返回的数据中只返回name和price字段

### 4. ad字段中包含单词white
GET /ad/phone/_search
{
  "query": {
    "match": {
      "ad": "white"
    }
  }
}
返回的结果中元字段_score有评分，说明使用query会计算评分

### 5. ad字段中包含单词white，并按照价格升序排列
GET /ad/phone/_search
{
  "query": {
    "match": {
      "ad": "white"
    }
  }, 
  "sort": [
    {
      "price": {
        "order": "asc"
      }
    }
  ]
}

### 6. 价格字段大于5000
GET /ad/phone/_search
{
  "query": {
    "bool": {
      "filter": {
        "range": {
          "price": {
            "gt": 5000
          }
        }
      }
    }
  }
}

### 7. ad字段中包含单词white，价格字段大于5000
GET /ad/phone/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ad": "white"
          }
        }
      ], 
      "filter": {
        "range": {
          "price": {
            "gt": 5000
          }
        }
      }
    }
  }
}

### 8. 查询count数量——查询name字段包含单词phone的文档的数量
GET /ad/phone/_count
{
  "query": {
    "match": {
      "name": "phone"
    }
  }
}

### 9. 根据查询条件求和sum
有三中办法：
1. 用 stats
```
GET /ad/phone/_count
{
  "query": {
    "match" : { "content" : "在" }
  },
  "aggs" : {
    "total_amount" : { "stats" : { "field" : "amount" } }
  }
}
说明：total_amount 就是个名字，随便起都行。stats、field 不能修改。可以修改的是 amount 字段
```
2. 用extended_stats
```
GET /ad/phone/_count
{
  "query": {
    "match" : { "content" : "在" }
  },
  "aggs" : {
    "total_amount" : { "extended_stats" : { "field" : "amount" } }
  }
}
```
3. 用sum
```
GET /ad/phone/_count
{
  "query": {
    "match" : { "content" : "在" }
  },
  "aggs" : {
    "total_amount" : { "sum" : { "field" : "amount" } }
  }
}
```
说明：当query和aggs一起存在时，会先执行query的主查询，主查询query执行完后会搜出一批结果，而这些结果才会被拿去aggs拿去做聚合，另外要注意aggs后面会先接一层自定义的这个聚合的名字，然后才是接上要使用的聚合桶

### 10.使用SQL语句查询Elasticsearch索引数据
POST /_sql?format=json
{
    "query": "SELECT * FROM indexName"
}

---
## 关键词详解

### 1. match_all查询
查询简单的匹配所有文档

GET /ad/phone/_search
{
  "query": {
    "match_all": {}
  }
}

### 2. match查询
支持全文搜索和精确查询，取决于字段是否支持全文检索


全文检索：

GET /ad/phone/_search
{
  "query": {
    "match": {
      "ad": "a red"
    }
  }
}
全文检索会将查询的字符串先进行分词，a red会分成为a和red，然后在倒排索引中进行匹配，所以这条语句会将三条文档都查出来
{
  "query": {
    "match": {
        "content" : {
            "query" : "我的宝马多少马力"
        }
    }
  }
}
上面的查询匹配就会进行分词，比如"宝马多少马力"会被分词为"宝马、 多少 、马力", 所有有关"宝马、 多少、 马力", 那么所有包含这三个词中的一个或多个的文档就会被搜索出来


对于精确值的查询，可以使用 filter 语句来取代 query，因为 filter 将会被缓存
operator操作：
match 查询还可以接受 operator 操作符作为输入参数，默认情况下该操作符是 or 。我们可以将它修改成 and 让所有指定词项都必须匹配
GET /ad/phone/_search
{
  "query": {
    "match": {
      "ad": {
        "query": "a red",
        "operator": "and"
      }
    }
  }
}

精确度匹配：
match 查询支持 minimum_should_match 最小匹配参数， 可以指定必须匹配的词项数用来表示一个文档是否相关。我们可以将其设置为某个具体数字（指需要匹配倒排索引的词的数量），更常用的做法是将其设置为一个百分数，因为我们无法控制用户搜索时输入的单词数量
GET /ad/phone/_search
{
  "query": {
    "match": {
      "ad": {
        "query": "a red",
        "minimum_should_match": "2"
      }
    }
  }
}
只会返回匹配上a和red两个词的文档返回，如果minimum_should_match是1，则只要匹配上其中一个词，文档就会返回

### 3. multi_match查询
多字段查询，比如查询color和ad字段包含单词red的文档
GET /ad/phone/_search
{
  "query": {
    "multi_match": {
      "query": "red",
      "fields": ["color","ad"]
    }
  }
}


### 4. range过滤

range过滤允许我们按照指定范围查找一批数据：

{ 
    "range": { 
        "age": { 
            "gte":  20, 
            "lt":   30 
        } 
    } 
}
范围操作符包含：

gt :: 大于
gte:: 大于等于
lt :: 小于
lte:: 小于等于


### 5. term查询
精确值查询
查询price字段等于6000的文档
GET /ad/phone/_search
{
  "query": {
    "term": {
      "price": {
        "value": "6000"
      }
    }
  }
}
查询name字段等于phone 8的文档
GET /ad/phone/_search
{
  "query": {
    "term": {
      "name": {
        "value": "phone 8"
      }
    }
  }
}
返回值如下，没有查询到名称为phone 8的文档
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 0,
    "max_score": null,
    "hits": []
  }
}
为什么没有查到phone 8的这个文档那，这里需要介绍一下term的查询原理
​	term查询会去倒排索引中寻找确切的term,它并不会走分词器，只会去配倒排索引 ，而name字段的type类型是text，会进行分词，将phone 8 分为phone和8，我们使用term查询phone 8时倒排索引中没有phone 8，所以没有查询到匹配的文档

term查询与match查询的区别

term查询时，不会分词，直接匹配倒排索引
match查询时会进行分词，查询phone 8时，会先分词成phone和8，然后去匹配倒排索引，所以结果会将phone 8和xiaomi 8两个文档都查出来

还有一点需要注意，因为term查询不会走分词器，但是回去匹配倒排索引，所以查询的结构就跟分词器如何分词有关系，比如新增一个/ad/phone类型下的文档，name字段赋值为Oppo，这时使用term查询Oppo不会查询出文档，这时因为es默认是用的standard分词器，它在分词后会将单词转成小写输出，所以使用oppo查不出文档，使用小写oppo可以查出来
GET /ad/phone/_search
{
  "query": {
    "term": {
      "name": {
        "value": "Oppo" //改成oppo可以查出新添加的文档
      }
    }
  }
}
这里说的并不是想让你了解standard分词器，而是要get到所有像term这类的查询结果跟选择的分词器有关系，了解选择的分词器分词方式有助于我们编写查询语句


### 6. terms查询
terms查询与term查询一样，但它允许你指定多直进行匹配，如果这个字段包含了指定值中的任何一个值，那么这个文档满足条件
GET /ad/phone/_search
{
  "query": {
    "terms": {
      "ad": ["red","blue"]
    }
  }
}

### 7. exists 查询和 missing 查询
用于查找那些指定字段中有值 (exists) 或无值 (missing) 的文档
指定name字段有值：
GET /ad/phone/_search
{
  "query": {
    "bool": {
      "filter": {
        "exists": {
          "field": "name"
        }
      }
    }
  }
}
指定name字段无值：
GET /ad/phone/_search
{
  "query": {
    "bool": {
      "filter": {
        "missing": {
          "field": "name"
        }
      }
    }
  }
}

### 8. match_phrase查询
短语查询，精确匹配，查询a red会匹配ad字段包含a red短语的，而不会进行分词查询，也不会查询出包含a 其他词 red这样的文档
GET /ad/phone/_search
{
  "query": {
    "match_phrase": {
      "ad": "a red"
    }
  }
}

比如上面一个例子，一个文档"我的保时捷马力不错"也会被搜索出来，那么想要精确匹配所有同时包含"宝马 多少 马力"的文档怎么做？就要使用 match_phrase 了
{
  "query": {
    "match_phrase": {
        "content" : {
            "query" : "我的宝马多少马力"
        }
    }
  }
}
完全匹配可能比较严，我们会希望有个可调节因子，少匹配一个也满足，那就需要使用到slop

{
  "query": {
    "match_phrase": {
        "content" : {
            "query" : "我的宝马多少马力",
            "slop" : 1
        }
    }
  }
}

### 9. scroll查询
类似于分页查询，不支持跳页查询，只能一页一页往下查询，scroll查询不是针对实时用户请求，而是针对处理大量数据，例如为了将一个索引的内容重新索引到具有不同配置的新索引中
POST /ad/phone/_search?scroll=1m
{
  "query": {
    "match_all": {}
  },
  "size": 1,
  "from": 0
}

返回值包含一个  "_scroll_id": "DnF1ZXJ5VGhlbkZldGNoBQAAAAAAAAAQFlV6T3VqY2NaVDBLRG5uZXdiZ0hFYUEAAAAAAAAAERZVek91amNjWlQwS0RubmV3YmdIRWFBAAAAAAAAABIWVXpPdWpjY1pUMEtEbm5ld2JnSEVhQQAAAAAAAAATFlV6T3VqY2NaVDBLRG5uZXdiZ0hFYUEAAAAAAAAAFBZVek91amNjWlQwS0RubmV3YmdIRWFB"
下次查询的时候使用_scroll_id就可以查询下一页的文档
POST /_search/scroll 
{
    "scroll" : "1m", 
    "scroll_id" : "DnF1ZXJ5VGhlbkZldGNoBQAAAAAAAAAYFlV6T3VqY2NaVDBLRG5uZXdiZ0hFYUEAAAAAAAAAGRZVek91amNjWlQwS0RubmV3YmdIRWFBAAAAAAAAABYWVXpPdWpjY1pUMEtEbm5ld2JnSEVhQQAAAAAAAAAXFlV6T3VqY2NaVDBLRG5uZXdiZ0hFYUEAAAAAAAAAFRZVek91amNjWlQwS0RubmV3YmdIRWFB" 
}

### 13. wildcard查询
支持通配符的模糊查询，？匹配单个字符，*匹配任何字符
为了防止极其缓慢通配符查询，*或?通配符项不应该放在通配符的开始
    GET /ad/phone/_search
    {
      "query": {
        "wildcard": {
          "color": "r?d"
        }
      }
    }






### 14. bool过滤

bool 过滤可以用来合并多个过滤条件查询结果的布尔逻辑，它包含一下操作符：

must :: 多个查询条件的完全匹配,相当于 and。
must_not :: 多个查询条件的相反匹配，相当于 not。
should :: 至少有一个查询条件匹配, 相当于 or。
这些参数可以分别继承一个过滤条件或者一个过滤条件的数组：

{ 
    "bool": { 
        "must":     { "term": { "folder": "inbox" }}, 
        "must_not": { "term": { "tag":    "spam"  }}, 
        "should": [ 
                    { "term": { "starred": true   }}, 
                    { "term": { "unread":  true   }} 
        ] 
    } 
}


### 15. bool查询

bool 查询与 bool 过滤相似，用于合并多个查询子句。不同的是，bool 过滤可以直接给出是否匹配成功， 而bool 查询要计算每一个查询子句的 _score （相关性分值）。

must:: 查询指定文档一定要被包含。
must_not:: 查询指定文档一定不要被包含。
should:: 查询指定文档，有则可以为文档相关性加分。
以下查询将会找到 title 字段中包含 "how to make millions"，并且 "tag" 字段没有被标为 spam。 如果有标识为 "starred" 或者发布日期为2014年之前，那么这些匹配的文档将比同类网站等级高：

{ 
    "bool": { 
        "must":     { "match": { "title": "how to make millions" }}, 
        "must_not": { "match": { "tag":   "spam" }}, 
        "should": [ 
            { "match": { "tag": "starred" }}, 
            { "range": { "date": { "gte": "2014-01-01" }}} 
        ] 
    } 
}

提示： 如果bool 查询下没有must子句，那至少应该有一个should子句。但是 如果有must子句，那么没有should子句也可以进行查询。



### 16. minimum_should_match

参数定义了至少满足几个子句，在一个Bool查询中，如果没有must或者filter，有一个或者多个should子句，那么只要满足一个就可以返回。


{
    "bool" : {
        "must" : {
            "term" : { "user" : "kimchy" }
        },
        "filter": {
            "term" : { "tag" : "tech" }
        },
        "must_not" : {
            "range" : {
                "age" : { "from" : 10, "to" : 20 }
            }
        },
        "should" : [
            {
                "term" : { "tag" : "wow" }
            },
            {
                "term" : { "tag" : "elasticsearch" }
            }
        ],
        "minimum_should_match" : 1,
        "boost" : 1.0
    }
}