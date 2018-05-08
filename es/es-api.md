**Elasticsearch 版本是 6.2.3**

[如何安装es](https://www.jianshu.com/p/6f302e26ef5d)

#### API

```
curl -X GET http://localhost:9200/_count

{
  "count": 0,
  "_shards": {
    "total": 0,
    "successful": 0,
    "skipped": 0,
    "failed": 0
  }
}
```

##### 添加数据

```
curl -X PUT http://localhost:9200/megacorp/employee/1 -H 'Content-Type: application/json' -d'

{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}'
```


##### 获得数据

```
curl -X GET http://localhost:9200/megacorp/employee/1?pretty

{
  "_index" : "megacorp",
  "_type" : "employee",
  "_id" : "1",
  "_version" : 3,
  "found" : true,
  "_source" : {
    "first_name" : "John",
    "last_name" : "Smith",
    "age" : 25,
    "about" : "I love to go rock climbing",
    "interests" : [
      "sports",
      "music"
    ]
  }
}
```


##### 查询


```
# 轻量搜索
curl -X GET http://localhost:9200/megacorp/employee/_search

# 轻量搜索
curl -X GET http://localhost:9200/megacorp/employee/_search?q=last_name:Smith

# 轻量搜索
curl -X GET http://localhost:9200/megacorp/employee/_search -H -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match" : {
            "last_name" : "Fir"
        }
    }
}'

# 条件搜索
curl -X GET http://localhost:9200/megacorp/employee/_search -H 'Content-Type: application/json' -d'
{
    "query" : {
        "bool": {
            "must": {
                "match" : {
                    "last_name" : "smith"
                }
            },
            "filter": {
                "range" : {
                    "age" : { "gt" : 30 }
                }
            }
        }
    }
}'

# 全文搜索
curl -XGET http://localhost:9200/megacorp/employee/_search -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}'

# 短语搜索
curl -X GET http://localhost:9200/megacorp/employee/_search -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}'

# 高亮搜索
curl -X GET http://localhost:9200/megacorp/employee/_search -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}'
```


#### 分析聚合 aggregations

**聚合搜索可能会报错**

> Fielddata is disabled on text fields by default. Set fielddata=true on [interests] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead.

> 应该是es5.x后对 排序/聚合 这些操作用单独的数据结构(fielddata)缓存到内存里了, 需要单独开启

```
curl -X PUT http://localhost:9200/megacorp/_mapping/employee/ -H 'Content-Type: application/json' -d'
{
  "properties": {
    "interests": {
      "type":     "text",
      "fielddata": true
    }
  }
}'
```

##### 聚合查询

```
curl -XGET "http://localhost:9200/megacorp/employee/_search" -H 'Content-Type: application/json' -d'
{
  "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}'

# 附带查询条件的聚合查询
curl -X GET http://localhost:9200/megacorp/employee/_search  -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "last_name": "smith"
    }
  },
  "aggs": {
    "all_interests": {
      "terms": {
        "field": "interests"
      }
    }
  }
}'

# 分级汇总, 比如 查询特定兴趣爱好员工的平均年龄:

curl -X GET http://localhost:9200/megacorp/employee/_search -H 'Content-Type: application/json' -d'
{
    "aggs" : {
        "all_interests" : {
            "terms" : { "field" : "interests" },
            "aggs" : {
                "avg_age" : {
                    "avg" : { "field" : "age" }
                }
            }
        }
    }
}'
```

##### 通配符查询(模糊查询)

```
curl -X GET http://localhost:9200/eth/address/_search -H 'Content-Type: application/json' -d'
{
    "query" : {
        "wildcard" : {
            "address": "0x0000*"
        }
    }
}'
```

##### 文档得分的计算过程

```
# 文档得分的计算过程
curl -X GET http://localhost:9200/megacorp/employee/1/_explain  -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match" : {
            "last_name" : "smith"
        }
    }
}'
```

---

参考:

https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/bulk.html
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/search-lite.html
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/running-elasticsearch.html#running-elasticsearch
https://www.elastic.co/guide/en/elasticsearch/reference/current/fielddata.html
