## 概述

ES 提供了强大的查询语言（DSL），它可以允许我们进行更加强大、复杂的查询，Elasticsearch DSL 中有 Query 与 Filter 两种

### Query 方式查询

1、Query 方式查询，会在 ES 中索引的数据都存储一个 `_score` 分值，分值越高就代表越匹配。

- 精确查询：term 查询不会对字段进行分词查询，会采用精确匹配，相当于 MySQL 中的 =

  注意：采用 term 精确查询，查询字段映射类型属于为 keyword
  
- 模糊查询：match 查询会对字段进行分词后查询，相当于 MySQL 中的 like 查询

```bash
POST /es_db/_search
{
  "query": {
    "match": {
      "address": "浙江杭州"
    }
  }
}
```

- 还可以增加分页查询：

```bash
POST /es_db/_search
{
  "from": 0,
  "size": 2, 
  "query": {
    "match": {
      "address": "浙江杭州"
    }
  }
}

# 相当于 MySQL 中
select * from es_db where address like '%浙江杭州%' limit 0,2
```

- 多字段模糊匹配查询与精确查询

```bash
POST /es_db/_search
{
  "query": {
    "multi_match": {
      "query": "浙江杭州", 
      "fields": ["name","address"]
    }
  }
}
```

- 范围查询

  注：range：范围关键字

  ​		gte：大于等于

  ​		lte：小于等于

  ​		gt：大于

  ​		lt：小于

  ​		now：当前时间

```bash
# 查询年龄大于等于10小于等于30的文档
POST /es_db/_search
{
  "query": {
    "range": {
      "age": {
        "gte": 10,
        "lte": 30
      }
    }
  }
}
```

- 分页、输出字段、排序综合查询

```bash
POST /es_db/_search
{
  "from": 0,
  "size": 2,
  "query": {
    "range": {
      "age": {
        "gte": 10,
        "lte": 40
      }
    }
  },
  "_source": ["name", "age"],
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}
```

### Filter 过滤器方式查询

