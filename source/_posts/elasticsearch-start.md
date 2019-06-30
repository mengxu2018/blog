---
title: elasticsearch quickstart
date: 2019-02-17 22:51:52
tags:
---

## Environment Setup

```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.6.0.tar.gz
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.6.0-linux-x86_64.tar.gz

# if you want to access kibana remotely, please update `server.host` to real IP in
# `kibana-6.6.0-linux-x86_64/config/kibana.yml` like 192.168.77.140

# You should run elasticsearch in non-root user
bin/elasticsearch -d
bin/kibana or `nohup bin/kibana &`

# now you can access http://192.168.77.140:5601
```

## elasticsearch http verb
elasticsearch 按照HTTP协议下RestFul框架的交互格式就像这样：   
```
POST /uri 创建
DELETE /uri/xxx 删除
PUT /uri/xxx 更新或创建
GET /uri/xxx 查看
```

## sample case
```
PUT /customer?pretty

PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}

GET /customer/_doc/1?pretty

DELETE /customer?pretty
GET /_cat/indices?v

PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}

PUT /customer/_doc/1?pretty
{
  "name": "Jane Doe"
}

PUT /customer/_doc/2?pretty
{
  "name": "Jane Doe"
}

POST /customer/_doc?pretty
{
  "name": "Jane Doe"
}

GET /customer/_search?q=Doe

GET /customer/_search
{
    "query" : {
        "match": { "name": "Doe" }
    },
    "highlight" : {
        "fields" : {
            "name" : {}
        }
    }
}

GET /_search
{
    "query" : {
        "match": { "name": "Doe" }
    },
    "highlight" : {
        "fields" : {
            "name" : {}
        }
    }
}

//这种更新是删除再增加，version变化了
PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}

//这样更新是真的更新，version不变
POST /customer/_doc/1/_update?pretty
{
  "doc": { "name": "Jane Doe", "age": 20 }
}

//get all index
curl "localhost:9200/_cat/indices?v"

curl -X GET "localhost:9200/bank/_search?q=*&sort=account_number:asc&pretty"
curl -X GET "localhost:9200/bank/_search?q=Fitzgerald&sort=account_number:asc&pretty"

//查询某个index
GET /twitter/_search
{
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}

// 查询所有的index
GET /_search
{
    "from" : 0, "size" : 10,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}

```