# ElasticSearch

## 1 三个基本概念

索引：类比为Mysql中的数据库

类型：类比为Mysql的数据表，这个类型在7版本之后就会弃用了，为了提高检索时字段的冲突

文档：类比为Mysql中的数据，为json数据格式

## 2 安装配置

[官方的安装配置地址](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html)

windows下载解压之后进入配置文件设置`network.host`

其他的也是类似，下载安装之后设置配置文件：linux在：`/usr/share/elasticsearch/config/elasticsearch.yml`

然后可以下载配套kibana作为可视化管理工具管理es，注意两者版本需要是一样的，而且kibana需要配置ELASTICSEARCH_HOSTS为es的访问地址，注意容器和wsl下需要设置为指定的ip，使用localhost不能访问导致kiban启动不了

## 3 ES教程

### 3.0 入门操作

检索：`_cat`

```http
GET /_cat/nodes           // 查看所有节点
GET /_cat/health          // 查看es的健康状况
GET /_cat/master          // 查看主节点
GET /_cat/indices         // 查看所有索引
```

索引文档：

```http
GET /index/type/id
```

`_seq_no和_primary_term`用来做并发控制

### 3.1 增删改

增：

```http
POST logs-my_app-default/_doc{}         // 插入单条数据，如果id存在就是修改，允许不带id
PUT logs-my_app-default/_bulk{}{}{}           // 插入多条数据
PUT customer/1  {}                // 向customer索引下插入id为1的文档信息，id存在就是修改，必须带上id

POST logs-my_app-default/_doc
{
  "@timestamp": "2099-05-06T16:21:15.000Z",
  "event": {
    "original": "192.0.2.42 - - [06/May/2099:16:21:15 +0000] \"GET /images/bg.jpg HTTP/1.0\" 200 24736"
  }
}

PUT logs-my_app-default/_bulk
{ "create": { } }
{ "@timestamp": "2099-05-07T16:24:32.000Z", "event": { "original": "192.0.2.242 - - [07/May/2020:16:24:32 -0500] \"GET /images/hm_nbg.jpg HTTP/1.0\" 304 0" } }
{ "create": { } }
{ "@timestamp": "2099-05-08T16:25:42.000Z", "event": { "original": "192.0.2.255 - - [08/May/2099:16:25:42 +0000] \"GET /favicon.ico HTTP/1.0\" 200 3638" } }
```

删：

```http
delete /index/id           // 删除文档数据
delete /index              // 删除索引
```

改：

```http
post /index/type/id                              // 不检查数据是否变更
put  /index/type/id
post /index/type/id/_update{"_doc":{}}           // 明确指定更新数据，不会增加version版本号，noop，会进行数据对比，如果数据一样不会改变版本号序列号
```

批量操作：

```
_bulk         // 这是批量操作的api
POST customer/external/_bulk
{"index":{"id":"1"}}
{"name":"df"}
{}
{}

API:
POST      _bulk
{actions:{}}
{请求体}
```



### 3.2 查询

两种查询方式：

```http
// 第一种，带有query参数
GET /_search
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search"        }},
        { "match": { "content": "Elasticsearch" }}
      ],
      "filter": [ 
        { "term":  { "status": "published" }},
        { "range": { "publish_date": { "gte": "2015-01-01" }}}
      ]
    }
  }
}

// 第二种直接在插叙uri中加入查询参数
GET bank/_search?q=*&sort=accout_num:asc
```

查询具体的字段

```http
GET logs-my_app-default/_search
{
  "query": {
    "match_all": { }
  },
  "fields": [
    "@timestamp"
  ],
  "_source": false,
  "sort": [
    {
      "@timestamp": "desc"
    }
  ]
}
```

查询指定范围内的数据：

```http
GET logs-my_app-default/_search
{
  "query": {
    "range": {
      "@timestamp": {
        "gte": "2099-05-05",
        "lt": "2099-05-08"
      }
    }
  },
  "fields": [
    "@timestamp"
  ],
  "_source": false,
  "sort": [
    {
      "@timestamp": "desc"
    }
  ]
}
```

复合查询，结合query和filter的查询方式

```http
GET /_search
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search"        }},
        { "match": { "content": "Elasticsearch" }}
      ],
      "filter": [ 
        { "term":  { "status": "published" }},
        { "range": { "publish_date": { "gte": "2015-01-01" }}}
      ]
    }
  }
}
```

bool查询：

```http
POST _search
{
  "query": {
    "bool" : {
    // 必须条件
      "must" : {
        "term" : { "user.id" : "kimchy" }
      },
      // 过滤，不贡献分数score
      "filter": {
        "term" : { "tags" : "production" }
      },
      // 必须没有
      "must_not" : {
        "range" : {
          "age" : { "gte" : 10, "lte" : 20 }
        }
      },
      // 可有可无
      "should" : [
        { "term" : { "tags" : "env1" } },
        { "term" : { "tags" : "deployed" } }
      ],
      "minimum_should_match" : 1,
      "boost" : 1.0
    }
  }
}
```

全文索引检索：match全文检索字段匹配

```http
GET /_search
{
  "query": {
    "match": {
      "message": {
        "query": "this is a test",
        "operator": "and"
      }
    }
  }
}
```

短语匹配：必须包含这个短语

```http
GET /_search
{
  "query": {
    "match_phrase": {
      "message": "this is a test"
    }
  }
}
```

多字段匹配：

```http
GET /_search
{
  "query": {
    "multi_match" : {
      "query":    "this is a test", 
      "fields": [ "subject", "message" ] 
    }
  }
}
```

term查询：可用于查非文本字段匹配，匹配某个属性的值,keyword用来精确匹配

```http
GET bank/_search
{
	"query": {
		"term": {
		  "age":48
		}
	}
}

## 精确匹配
GET bank/_search
{
	"query": {
		"term": {
			"address.keyword": "789 Madison Street"
		}
	}
}
```



### 3.3 聚合操作

在查询到的数据的基础上进行聚合函数的操作，比如求平均，求极值，求最大，统计数据数量等等

聚合可以嵌套，在聚合里面还可以继续聚合

```http
GET /my-index-000001/_search
{
  "aggs": {
  // addAvg是聚合名,标识按照字段age进行聚合
    "aggAvg": {
      "terms": {
        "field": "age"
      }
    }
  }
}
```

### 3.4 映射

定义文档如何处理，也包含定义文档的数据类型，最开始插入时会自动猜测类型

映射是可以修改的

```http
GET bank/_mapping
```

插入指定mapping的索引index

```http
PUT /my_index
{
  "mappings": {
    "properties": {
      "age": {
        "type": "long",
        "index": true
      },
      "name": {
        "type": "text",
        "index": true
      }
    }
  }
}

GET /my_index/_mapping
```

插入映射：

```http
PUT /my_index/_mapping
{
    "properties": {
      "address":{
        "type": "text",
        "index": true
      }
    }
}
// 只能插入不存在的映射，不能修改映射
```

不能更新映射

### 3.5 数据迁移

先修改好指定的文档的映射之后，使用数据迁移的API进行修改即可

数据迁移可以做到映射的修改

```http
PUT /newbank
{
  "mappings" : {
      "properties" : {
        "account_number" : {
          "type" : "long"
        },
        "address" : {
          "type" : "text",
          "index": true
        },
        "age" : {
          "type" : "integer",
          "index": true
        },
        "balance" : {
          "type" : "long",
          "index": true
        },
        "city" : {
          "type" : "text",
          "index": true
        },
        "email" : {
          "type" : "text"
        },
        "employer" : {
          "type" : "text"
        },
        "firstname" : {
          "type" : "text",
          "index": true
        },
        "gender" : {
          "type" : "text"
        },
        "lastname" : {
          "type" : "text"
        },
        "state" : {
          "type" : "text",
          "index": true
      }
    }
  }
}

// 修改数据对应的映射，然后为后续数据迁移做准备
```

数据迁移

```
POST _reindex
{
  "source": {
    "index": "bank"
  },
  "dest": {
    "index": "newbank"
  }
}
// 指定旧索引和新索引，如果旧索引使用了类型，还需要指定type
```

## 4 分词

### 4.1 ik中文分词器

下载对应于elasticsearch版本的分词器，并将该插件解压到elasticsearch安装目录的plugins文件夹下

然后如果需要配置远程的字典的话，可以使用nginx保存字典信息，然后在ik分词器的config目录里面修改配置文件中的远程字典的位置即可



## 5 扁平化处理和内嵌式

扁平化处理是指将多个相同对象的字段提取出，将各个对象的该字段值提取出放入一个数组中进行存储

内嵌式则是跳过这样的一种处理方式，就只是按照对象来存储数据
