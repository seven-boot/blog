## 概述



## 安装

1、下载 Elasticsearch IK 分词器

https://github.com/medcl/elasticsearch-analysis-ik/releases

2、在 es 目录下的 plugins 目录中创建 ik 目录，并解压到该目录下

- create plugin folder `cd your-es-root/plugins/ && mkdir ik`
- unzip plugin to folder `your-es-root/plugins/ik`

3、重启 es

4、测试分词效果

`analyzer` 默认 `standard`，会把每个词都拆分

`ik_smart`：会做最粗粒度的拆分

`ik_max_word`：会将文本做最细粒度的拆分

```bash
POST _analyze
{
  "analyzer": "ik_max_word",
  "text": "我是中国人"
}

# 输出
{
  "tokens" : [
    {
      "token" : "我",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "CN_CHAR",
      "position" : 0
    },
    {
      "token" : "是",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "中国人",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "中国",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 3
    },
    {
      "token" : "国人",
      "start_offset" : 3,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 4
    }
  ]
}

```

## 指定 IK 分词器作为默认分词器

ES 的默认分词设置是：standard

```bash
PUT /es_db
{
  "settings": {
    "index": {
      "analysis.analyzer.default.type": "ik_max_word"
    }
  }
}
```



