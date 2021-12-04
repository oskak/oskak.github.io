---
title: 本地搭建一个 Elasticsearch 集群
tags: [Elasticsearch]
---

## 节点 & 集群
集群中包含很多服务器，一个节点就是其中的一个服务器。作为集群的一部分，它存储数据，参与集群的索引和搜索功能。

配置服务器集群时，集群中节点数量没有限制，大于等于2个节点就可以看做是集群了。一般出于高性能及高可用方面来考虑，集群中节点数量都是3个以上。

一个 Elasticsearch 集群有一个唯一的名字标识，这个名字默认是 `elasticsearch`。这个名字是重要的，因为一个节点只能通过指定某个集群的名字来加入这个集群。

用 Elasticsearch 集群，将单个索引的分片放到多个不同物理机器上分布式存储，从而实现高可用、容错性等。Elasticsearch 会自动选举实现主备服务器。

## 分片
分片（Shard），是 Elasticsearch 中的最小存储单元。

- Shards分片：代表索引分片，Elasticsearch 可以把一个完整的索引分成多个分片，这样的好处是可以把一个大的索引拆分成多个，分布到不同的节点上，构成分布式搜索。分片的数量只能在索引创建前指定，并且索引创建后不能更改。
- Replicas分片：代表副本分片，Elasticsearch 可以设置多个索引的副本，副本的作用一是提高系统的容错性，当某个节点某个分片损坏或丢失时可以从副本中恢复。二是提高 Elasticsearch 的查询效率，Elasticsearch 会自动对搜索请求进行负载均衡。

## 本地搭建一个 Elasticsearch 集群
新建一个 `es_cluster` 目录，在内部复制三个 elasticsearch 服务：
```
~/es_cluster  $ ls
elasticsearch-7.15.1 node1	elasticsearch-7.15.1 node2	elasticsearch-7.15.1 node3
```

`es_cluster/elasticsearch-7.15.1 node1/config/elasticsearch.yml`:
```
# 集群名称
cluster.name: es-cluster

# 节点名称
node.name: es01

# 可以成为主节点
node.master: true

# 需要存储数据
node.data: true

# 数据文件
path.data: ~/elasticsearch/data

# 日志文件
path.logs: ~/elasticsearch/logs

# 锁定物理内存地址，防止es内存被交换出去，也就是避免es使用swap交换分区，频繁的交换会导致IOPS变高
bootstrap.memory_lock: true

# 服务地址
network.host : localhost

# 端口,默认为9200
http.port : 9201

# 内部节点之间沟通端口
transport.tcp.port : 9301

# 服务发现种子主机，只有配置这个可访问的地址，es才能发现各个节点
discovery.seed_hosts: ["localhost:9301" ,"localhost:9302", "localhost:9303"]
# 初始主节点,用于选举
cluster.initial_master_nodes: ["es01", "es02", "es03"]

# 集群中自动发现其它节点时ping连接超时时间，默认为3秒，对于比较差的网络环境可以高点的值来防止自动发现时出错
discovery.zen.ping_timeout: 120s

# 设置这个参数来保证集群中的节点可以知道其它N个有master资格的节点
discovery.zen.minimum_master_nodes: 2

# 跨域设置
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-methods: OPTIONS, HEAD, GET, POST, PUT, DELETE
http.cors.allow-headers: "X-Requested-With, Content-Type, Content-Length, X-User"
```

`es_cluster/elasticsearch-7.15.1 node2/config/elasticsearch.yml`:
```
# 节点名称
node.name: es02

# 端口,默认为9200
http.port : 9202

# 内部节点之间沟通端口
transport.tcp.port : 9302

# 其他配置同 es_cluster/elasticsearch-7.15.1 node1/config/elasticsearch.yml
```

`es_cluster/elasticsearch-7.15.1 node3/config/elasticsearch.yml`:
```
# 节点名称
node.name: es03

# 端口,默认为9200
http.port : 9203

# 内部节点之间沟通端口
transport.tcp.port : 9303

# 其他配置同 es_cluster/elasticsearch-7.15.1 node1/config/elasticsearch.yml
```

在 `es_cluster/elasticsearch-7.15.1 node<x>` 目录下分别启动相应节点：
```
./bin/elasticsearch
```

查看命令行输出信息可知 `es02` 选举成为主节点：
```
[2021-12-01T17:07:52,369][INFO ][o.e.c.s.ClusterApplierService] [es02] master node changed {previous [], current [{es02}...
```

向集群中的 `es01` 节点增加索引：
```
$ curl -X PUT localhost:9201/store
{"acknowledged":true,"shards_acknowledged":true,"index":"store"}  
```

向集群中的 `es02` 节点查询索引：
```
$ curl localhost:9202/store 
{"store":{"aliases":{},"mappings":{},"settings":{"index":{"routing":{"allocation":{"include":{"_tier_preference":"data_content"}}},"number_of_shards":"1","provided_name":"store","creation_date":"1638350523550","number_of_replicas":"1","uuid":"lRkjZCSySe6eenrAsUQhJg","version":{"created":"7150199"}}}}}
```

## master 节点的选举
Elasticsearch 的任意一个节点都可以设置 `node.master` 和 `node.data` 属性，该属性的意义如下表所示：

|master / data | true	| false  |
|--------------|-------|-------|
|true	| 既是 master eligible，又是 data 节点 | 单纯的 master eligible 节点 |
|false	| 单纯的 data 节点	| 纯粹的 Coordinating Node，协调节点负责查询时的数据收集、合并以及聚合等操作，ES 中所有节点都是协调节点 |

ES 针对当前集群中所有的 master eligible 节点进行选举得到 master 节点，为了避免出现 **Split-brain** 现象，ES 选择了分布式系统常见的 quorum（多数派）思想，也就是只有获得了超过半数选票的节点才能成为 master。在 ES 中使用 `discovery.zen.minimum_master_nodes` 属性设置 quorum，这个属性一般设置为 `eligibleNodesNum / 2 + 1`。

当满足如下条件时，集群内就会发生一次 master 选举：
- 当前 master eligible 节点不是 master
- 当前 master eligible 节点与其它的节点通信无法发现 master
- 集群中无法连接到 master 的 master eligible 节点数量已达到 `discovery.zen.minimum_master_nodes` 所设定的值

## References
- [服务化01-Elasticsearch集群](https://hongker.github.io/2021/03/21/service-elasticsearch/)
- [Elasticsearch 是如何选举出 master 的](https://www.nosuchfield.com/2019/03/18/How-Elasticsearch-elected-the-master/)
