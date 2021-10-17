---
title: 初识 Elasticsearch
tags: [Elasticsearch, Search]
---

## Elasticsearch 简单介绍
Elasticsearch 是一个近实时分布式搜索和分析引擎，可以用在全文搜索、结构化搜索、分析。Elasticsearch 基于 Apache Lucene。

Elasticsearch 也使用 Java 开发并使用 Lucene 作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的 RESTful API 来隐藏 Lucene 的复杂性，从而让全文搜索变得简单。

一般传统数据库，全文检索的实现都很鸡肋，因为一般也没人用数据库存文本字段。进行全文检索需要扫描整个表，如果数据量大的话，即使对 SQL 的语法优化，也收效甚微。分析得出，在一些生产环境中，使用常规的搜索方式，性能是非常差的:
- 搜索的数据对象是大量的非结构化的文本数据
- 文件记录量达到数十万或数百万个甚至更多
- 支持大量基于交互式文本的查询
- 需求非常灵活的全文搜索查询

## Lucene 简介
Lucene 是一个开放源代码的全文检索引擎工具包，但它不是一个完整的全文检索引擎，而是一个全文检索引擎的架构，提供了完整的查询引擎、索引引擎和部分文本分析引擎。

## 什么是全文搜索引擎？
全文搜索引擎是名副其实的搜索引擎，比如Google。从搜索结果来源的角度，全文搜索引擎又可细分为两种，一种是拥有自己的检索程序（Indexer），俗称“蜘蛛”（Spider）程序或“机器人”（Robot）程序，并自建网页数据库，搜索结果直接从自身的数据库中调用；另一种则是租用其他引擎的数据库。

## Download ELK
- [Elasticsearch](https://www.elastic.co/downloads/elasticsearch)
- [Kibana](https://www.elastic.co/downloads/kibana)
- [Logstash](https://www.elastic.co/downloads/logstash)

```
./bin/elasticsearch
./bin/kibana
./bin/logstash
```

## JSON
JSON（JavaScript Object Notation）是一种轻量级的数据交换格式。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。JSON 是 JS 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串。见 [JSON](https://baike.baidu.com/item/JSON/2462549)。

## 倒排索引
什么是正向索引？

| id | content |
| --- | ------|
| 1   | ...   |

倒排索引?

| keyword | id |
|---------|----|
| hello   | 1, 2 |

## 创建索引
```
$ curl -X PUT localhost:9200/store
```

Output:
```
{"acknowledged":true,"shards_acknowledged":true,"index":"store"}  
```

## 插入数据
```
$ curl -X POST -H "Content-Type: application/json" \
> -d '{"Name":"Apple","Price":5,"Unit":"Kg"}' \
> localhost:9200/store/_doc
```

插入同一条数据但是自定义 `id`，因为 ES 默认生成的 `id` 无过多实际意义：
```
$ curl -X POST -H "Content-Type: application/json" \
-d '{"Name":"Apple","Price":5,"Unit":"Kg"}' \
localhost:9200/store/_doc/1
```

## 一个音乐相关的数据集
[Music Dataset: Lyrics and Metadata from 1950 to 2019](https://data.mendeley.com/datasets/3t9vbwxgr5/3)

## References
- [Elasticsearch](https://zh.wikipedia.org/zh-cn/Elasticsearch)
- [从 0 到 1 学习 elasticsearch ，这一篇就够了！(建议收藏)](https://zhuanlan.zhihu.com/p/358744225)
