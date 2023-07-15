# 1.ElasticSearch基础操作

为了方便理解,会用关系型数据库和es做类比

## 1.1 索引操作

### 1.1 创建索引

PUT请求 ：http:{ip}/9200/{索引名称}

```
响应结果
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "shopping"
}
```

### 1.2 查看索引信息

GET请求 :http:{ip}/9200/{索引名称}

```
响应内容
{
    "shopping": {
        "aliases": {},
        "mappings": {
            "properties": {
                "base_areaid": {
                    "type": "integer"
                },
                "name": {
                    "type": "text"
                },
                "parentid": {
                    "type": "integer"
                },
                "vieworder": {
                    "type": "integer"
                }
            }
        },
        "settings": {
            "index": {
                "creation_date": "1668084144994",
                "number_of_shards": "1",
                "number_of_replicas": "1",
                "uuid": "BBeM31eZSECBGNvKegQHYA",
                "version": {
                    "created": "7060299"
                },
                "provided_name": "shopping"
            }
        }
    }
}
```

### 1.3 查看ES中所有的索引

GET请求 :http:{ip}/9200/_cat/indices?v

```
相应内容
health status index                    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   test2                    mbvWV8UJRPOZXfXG0fendg   1   1          0            0       283b           283b
green  open   .kibana_task_manager_1   NoVFMUdETZOr41FJleDe-A   1   0          2            0     41.3kb         41.3kb
green  open   .apm-agent-configuration N3OHUX94SJqEpBCph6CAHw   1   0          0            0       283b           283b
yellow open   test1                    2Tn_pGOXSyCTQuuLBcHkKQ   1   1          1           11        5kb            5kb
green  open   .kibana_1                bpoL40ivTEeGRnbWGTz4iw   1   0         12            2     20.9kb         20.9kb
yellow open   shopping                 BBeM31eZSECBGNvKegQHYA   1   1          0            0       230b           230b

```

### 1.4删除某一个索引

DELETE请求 :http:{ip}/9200/{索引名称}

```
响应
{
    "acknowledged": true
}
```

## 1.2 文档操作 插入

### 1.1创建文档

POST请求 ：http://121.4.23.51:9200/{文档名}/_doc

```
#入参
{
    "title":"小米手机",
    "category":"小米",
    "images":"https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
    "price":3399.00
}
#响应
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "3uaaYYQBkkgS0D0XZraG",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}
```

### 1.2自定义ID

POST请求 ： http://121.4.23.51:9200/{文档名字}/_doc/{id}

## 1.3简单查询

### 1.1根据主键id查询

GET请求 ： http://121.4.23.51:9200/{文档名字}/_doc/{id}

```
找到是这样的
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1001011",
    "_version": 2,
    "_seq_no": 4,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "title": "小米手机",
        "category": "小米",
        "images": "https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
        "price": 3399.00
    }
}
没找到
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1001011111",
    "found": false
}
```

### 1.2查询索引下所有数据

GET请求：http://121.4.23.51:9200/{文档名字}/_search

```
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 1.0,
        //命中的结果
        "hits": [
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "3uaaYYQBkkgS0D0XZraG",
                "_score": 1.0,
                "_source": {
                    "title": "小米手机",
                    "category": "小米",
                    "images": "https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
                    "price": 3399.00
                }
            },
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "3-abYYQBkkgS0D0XjbYD",
                "_score": 1.0,
                "_source": {
                    "title": "小米手机",
                    "category": "小米",
                    "images": "https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
                    "price": 3399.00
                }
            },
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "4OabYYQBkkgS0D0XkLaa",
                "_score": 1.0,
                "_source": {
                    "title": "小米手机",
                    "category": "小米",
                    "images": "https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
                    "price": 3399.00
                }
            },
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "1001011",
                "_score": 1.0,
                "_source": {
                    "title": "小米手机",
                    "category": "小米",
                    "images": "https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
                    "price": 3399.00
                }
            }
        ]
    }
}
```

## 1.4简单修改

### 1.1完全覆盖修改 根据id

PUT	请求 ： http://121.4.23.51:9200/{索引}/_doc/{id}

```
#直接全部改了
{
    "title":"iphone13",
    "category":"iphone",
    "images":"https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
    "price":9999.00
}

#响应
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1001011",
    "_version": 3,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 5,
    "_primary_term": 1
}
```

### 1.2局部修改

PUT	请求 ： http://121.4.23.51:9200/{索引}/_update/{id}

```
#入参
{
    "doc":{
        "title":"华为"   
    }
}
```

## 1.5 简单删除

DELETE请求 ：http://121.4.23.51:9200/{索引}/_doc/{id}

```
#响应
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "3uaaYYQBkkgS0D0XZraG",
    "_version": 2,
    //找到了就是deleted 没有就是not_found
    "result": "deleted",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 7,
    "_primary_term": 1
}
```

### 1.5条件查询

GET请求 ： http://121.4.23.51:9200/{文档名字}/_search?q={条件}:{value}

```
例：http://121.4.23.51:9200/shopping/_search?q=title:华为
{
    "took": 41,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 1,
            "relation": "eq"
        },
        "max_score": 2.7103386,
        "hits": [
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "4OabYYQBkkgS0D0XkLaa",
                "_score": 2.7103386,
                "_source": {
                    "title": "华为",
                    "category": "小米",
                    "images": "https://www.mi.com/?g_utm=Thirdparty.Baidu1.L1.3.ProductUnion",
                    "price": 3399.0
                }
            }
        ]
    }
}
```

也可以这样写

GET: http://121.4.23.51:9200/{文档名字}/_search

```
#入参 效果一样
{
    "query":{
        "match":{
            "title":"华为"
        }
    }
}
#查询全部也可以这么写
{
    "query":{
        "match_all":{
        }
    }
}
```

### 1.6 分页

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        "match_all":{
        }
    },
    //页码
    "from":0,
    //每页条数
    "size":2
}
```

### 1.3过滤字段

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        "match_all":{
        }
    },
    "_source":["title"]
}
```

等价于 select title from {文档名}

### 1.4排序

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        "match_all":{
        }
    },
    "sort":{
        "price":{
            "order":"desc"        
        }    
    }
}
```

### 1.5多条件查询 AND OR

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        //条件
        "bool":{
            //and = must , or = should
            "should":[
            {
                "match":{
                    "category":"小米"                                                    
                }            
            },{
                "match":{
                    "title":"华为"                
                }            
            }
            ]                    
        }    
    }
}
```

### 1.6范围

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        //条件
        "bool":{
            "filter":{
                "range":{
                    //范围字段
                    "price":{
                        "gt":3000                    
                    }                
                }            
            }                 
        }    
    }
}
```

### 1.7全匹配

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        "match_phrase":{
            "category":"内容"        
        } 
    }
}
```

### 1.8高亮显示

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    "query":{
        "match_phrase":{
            "category":"内容"        
        } 
    },
    "highlight":{
        //高亮字段
        "fields":{
             "category":{}        
        }           
    }
}
```

## 1.6聚合操作

GET: http://121.4.23.51:9200/{文档名字}/_search

```
{
    //聚合操作
    "aggs":{
        //"price_avg" 平均值 price_sum 求和 
        "price_group":{
            //"avg"平均值 sum 求和 
            "terms":{
                //分组字段
                "field":"price"            
            }
        }            
    },
    //如果你只是想聚合,不要详细数据,就用这个
    "size":0
}
```

## 1.7.映射关系

我们尝试创建一个索引以及他的结构信息

当然,你需要先创建这个索引,然后再给他写入规则

PUT请求 :  http://121.4.23.51:9200/{文档名字}/_mapping

```
{
    "properties":{
        "name":{
            // type test 表示可以分词,index 表示是否可以索引查询
            "type":"text",
            "index":true        
        },
        "sex":{
            // type keyword 表示不可以分词,必须完整匹配 ,index 表示是否可以索引查询
            "type":"keyword",
            "index":true        
        },   
        "tel":{
            "type":"keyword",
            "index":false        
        }      
    }
}
```



```
#响应
{
    "acknowledged": true
}
#成功了,这个时候我们可以get请求查看信息,结果如下
{
    "user": {
        "mappings": {
            "properties": {
                "name": {
                    "type": "text"
                },
                "sex": {
                    "type": "keyword"
                },
                "tel": {
                    "type": "keyword",
                    "index": false
                }
            }
        }
    }
}

```

关键词 keyword 必须完全匹配,Index 为 false时,不能被当做查询字段

# 2.JavaApi

### 2.1原生

#### 2.1.1环境准备

```
<dependencies>
    <dependency>
        <groupId>org.elasticsearch</groupId>
        <artifactId>elasticsearch</artifactId>
        <version>7.8.0</version>
    </dependency>
    <!-- elasticsearch 的客户端 -->
    <dependency>
        <groupId>org.elasticsearch.client</groupId>
        <artifactId>elasticsearch-rest-high-level-client</artifactId>
        <version>7.8.0</version>
    </dependency>
    <!-- elasticsearch 依赖 2.x 的 log4j -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.9</version>
    </dependency>
    <!-- junit 单元测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>

```

#### 2.1.2 创建链接

```
RestHighLevelClient esClient = new RestHighLevelClient(
        RestClient.builder(new HttpHost("121.4.23.51",9200,"http"))
);
```

#### 2.1.3 操作索引，创建，删除

```
//新增索引
CreateIndexResponse users = esClient.indices().create(new CreateIndexRequest("users"), RequestOptions.DEFAULT);
//响应状态，是否成功
boolean acknowledged = users.isAcknowledged();
System.out.println("操作"+acknowledged);

//删除索引
DeleteIndexRequest users = new DeleteIndexRequest("users");
AcknowledgedResponse delete = esClient.indices().delete(users, RequestOptions.DEFAULT);
System.out.println(delete.isAcknowledged());
```

#### 2.1.4 数据CURD

```
//客户端
RestHighLevelClient esClient = new RestHighLevelClient(
        RestClient.builder(new HttpHost("121.4.23.51",9200,"http"))
);
//新增
User user = new User("沙瑞金","男士","18037977200");
IndexRequest indexRequest = new IndexRequest()
        .index("user")
        .id("10086")
        .source(JSON.toJSONString(user), XContentType.JSON);
IndexResponse index = esClient.index(indexRequest, RequestOptions.DEFAULT);

//更新（局部）
UpdateRequest updateRequest = new UpdateRequest()
        .index("user")
        .id("10087")
        .doc(XContentType.JSON,"name","丁义诊");
esClient.update(updateRequest,RequestOptions.DEFAULT);

//更新（全量）
User user1 = new User("高育良","男士","18037977200");
UpdateRequest updateRequest1 = new UpdateRequest()
        .index("user")
        .id("10087")
        .doc(JSON.toJSONString(user),XContentType.JSON);
esClient.update(updateRequest1,RequestOptions.DEFAULT);

//查询
GetRequest getRequest = new GetRequest().index("user").id("10086");
GetResponse documentFields = esClient.get(getRequest, RequestOptions.DEFAULT);
User user2 = JSON.parseObject(documentFields.getSourceAsString(), User.class);
System.out.println(user2.toString());

//删除
DeleteRequest deleteRequest = new DeleteRequest().index("user").id("10086");
DeleteResponse delete = esClient.delete(deleteRequest, RequestOptions.DEFAULT);
System.out.println(delete.toString());

//关闭客户端
esClient.close();
```

#### 2.1.5 批量操作

```
RestHighLevelClient esClient = new RestHighLevelClient(
        RestClient.builder(new HttpHost("121.4.23.51",9200,"http"))
);
//批量插入
BulkRequest bulkRequest = new BulkRequest();
bulkRequest.add( new IndexRequest().index("user").id("10000").source(JSON.toJSONString(new User("李达康", "男", "110")),XContentType.JSON));
bulkRequest.add( new IndexRequest().index("user").id("10007").source(JSON.toJSONString(new User("赵玉龙", "男", "110")),XContentType.JSON));
bulkRequest.add( new IndexRequest().index("user").id("10008").source(JSON.toJSONString(new User("程度", "男", "110")),XContentType.JSON));
bulkRequest.add( new IndexRequest().index("user").id("10009").source(JSON.toJSONString(new User("丁义诊", "男", "110")),XContentType.JSON));
bulkRequest.add( new IndexRequest().index("user").id("10010").source(JSON.toJSONString(new User("赵德汉", "男", "110")),XContentType.JSON));
BulkResponse bulk = esClient.bulk(bulkRequest, RequestOptions.DEFAULT);
System.out.println(bulk);

//批量删除
BulkRequest bulkRequest = new BulkRequest();
bulkRequest.add(new DeleteRequest().index("user").id("10007"));
esClient.bulk(bulkRequest,RequestOptions.DEFAULT);

//修改同理
BulkRequest bulkRequest = new BulkRequest();
bulkRequest.add(new UpdateRequest().index("user").id("10008").doc(JSON.toJSONString(new User("侯亮平","猴子","123123123")),XContentType.JSON));
esClient.bulk(bulkRequest,RequestOptions.DEFAULT);
esClient.close();

```

#### 2.1.5 全量查询

```
//1.全量查询
SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
SearchRequest searchRequest = new SearchRequest().indices("user").source(query);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
//查询结果
SearchHits hits = search.getHits();
//条数
System.out.println(hits.getTotalHits());
for (SearchHit hit:hits) {
    //json
    System.out.println(hit.getSourceAsString());
}
###############结果###############
7 hits
{"name":"祁同伟","sex":"男","tel":"13838838388"}
{"name":"高小琴","sex":"女","tel":"13838838438"}
{"name":"高育良","sex":"男士","tel":"18037977200"}
{"name":"李达康","sex":"男","tel":"110"}
{"name":"丁义诊","sex":"男","tel":"110"}
{"name":"赵德汉","sex":"男","tel":"110"}
{"name":"侯亮平","sex":"猴子","tel":"123123123"}
```

#### 2.1.6 高级查询

 1.简单条件查询

```
//1.查询条件 参考上面的语法 matchPhraseQuery 全匹配，matchQuery 拆词
QueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "高小琴");
SearchSourceBuilder query = new SearchSourceBuilder().query(termQueryBuilder);
SearchRequest searchRequest = new SearchRequest().indices("user").source(query);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
//查询结果
SearchHits hits = search.getHits();
//条数
System.out.println(hits.getTotalHits());
for (SearchHit hit:hits) {
    //json
    System.out.println(hit.getSourceAsString());
}
```

 2.分页

```
//2.分页，我们查全部
SearchRequest searchRequest = new SearchRequest().indices("user");
SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//分页参数，语法参考json查询条件
//（当前页码-1）* 每页显示数据条数
query.from(0);
query.size(2);
searchRequest.source(query);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
SearchHits hits = search.getHits();
for (SearchHit hit:hits) {
    System.out.println(hit.getSourceAsString());
}
```

 3.排序

```
//3.排序
SearchRequest searchRequest = new SearchRequest().indices("user");
SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//排序字段与顺序
query.sort("age", SortOrder.DESC);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
SearchHits hits = search.getHits();
for (SearchHit hit:hits) {
    System.out.println(hit.getSourceAsString());
}
```

 4.过滤字段(指定字段查询)

```
//4.过滤字段
SearchRequest searchRequest = new SearchRequest().indices("user");
SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//排除字段
String[] excludes = {};
//包含字段
String[] includes = {"name"};
builder.fetchSource(includes,excludes);
searchRequest.source(builder);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
SearchHits hits = search.getHits();
for (SearchHit hit:hits) {
    System.out.println(hit.getSourceAsString());
}
```

 5.组合条件

```
//5.组合查询
SearchRequest searchRequest = new SearchRequest().indices("user");
//查询条件 must -> and ; should -> or
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
//这里相当于 select * from user where name ="高育良" or name = "李达康"  这部分选项相对于宽松 还可以选择mustNot之类,见名知意
boolQueryBuilder.should(QueryBuilders.termQuery("name","高育良"));
boolQueryBuilder.should(QueryBuilders.termQuery("name","李达康"));
SearchSourceBuilder builder = new SearchSourceBuilder().query(boolQueryBuilder);
searchRequest.source(builder);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
System.out.println(search.getTotalShards());
SearchHits hits = search.getHits();
for (SearchHit hit:hits) {
    System.out.println(hit.getSourceAsString());
}
```

 6.范围查询

```
//6.范围查询
SearchRequest searchRequest = new SearchRequest().indices("user");
//范围查询 gt 大于 lt 小于 e 等于 
RangeQueryBuilder age = QueryBuilders.rangeQuery("age");
age.gte(30);
age.lte(40);
SearchSourceBuilder builder = new SearchSourceBuilder().query(age);
searchRequest.source(builder);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
```

 7.模糊查询

```
//7.模糊查询
SearchRequest searchRequest = new SearchRequest().indices("user");
//fuzzyQuery 字段 内容，fuzziness 误差
FuzzyQueryBuilder fuzziness = QueryBuilders.fuzzyQuery("key", "value").fuzziness(Fuzziness.ONE);
SearchSourceBuilder builder = new SearchSourceBuilder().query(fuzziness);
searchRequest.source(builder);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
```

 8.高亮

```
//8.高亮
SearchRequest searchRequest = new SearchRequest().indices("user");
//先做个条件
MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("name", "高");
SearchSourceBuilder builder = new SearchSourceBuilder().query(matchQueryBuilder);
//自定义一个前后缀标签，然后定义字段，这样结果匹配会带着个标签
HighlightBuilder nameHigh = new HighlightBuilder().preTags("<font color='red'>").postTags("</font>").field("name");
builder.highlighter(nameHigh);
searchRequest.source(builder);
SearchResponse search = esClient.search(searchRequest, RequestOptions.DEFAULT);
SearchHits hits = search.getHits();
for (SearchHit hit:hits) {
    System.out.println(hit.getSourceAsString());
    //高亮在这里查看哦
    System.out.println(hit.getHighlightFields());
}
#########效果#############
{"name":"高小琴","sex":"女","tel":"13838838438"}
{name=[name], fragments[[<font color='red'>高</font>小琴]]}
{"name":"高育良","sex":"男士","tel":"18037977200"}
{name=[name], fragments[[<font color='red'>高</font>育良]]}
```

 9.聚合

AggregationBuilders方法如下

![](images/WEBRESOURCEdf9d13e6fa5e29ce6ce3f79905ecba1c.octet-stream)

```
//9.聚合
SearchRequest request = new SearchRequest().indices("user");
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.aggregation(AggregationBuilders.count("count").field("id"));
request.source(sourceBuilder);
// 客户端发送请求，获取响应对象
SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
###########################################
当然你可以分组
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.aggregation(AggregationBuilders.terms("age_groupby").field("age"));
request.source(sourceBuilder);
```

# 3.GoApi

#### 安装包

```
 go get  "github.com/olivere/elastic/v7"
```

#### 链接到es

```
package main

import (
   "context"
   "github.com/olivere/elastic/v7"
)

var Client *elastic.Client
var host = "http://121.4.23.51:9200"

//InitClient 初始化
func InitClient() {
   var err error
   //初始化客户端
   Client, err = elastic.NewClient(
      elastic.SetSniff(false),
      elastic.SetURL(host),
   )
   if err != nil {
      panic(err)
   }
   //连接服务测试
   _, _, err = Client.Ping(host).Do(context.Background())
   if err != nil {
      panic(err)
   }
   //获取服务端版本号
   _, err = Client.ElasticsearchVersion(host)
   if err != nil {
      panic(err)
   }
}

```

