---
title: 全文搜索(1) - 最小单位查询 - Elasticsearch Java High Level Rest Client
tags: [Elasticsearch]
---

## 最小单位查询
对传入的关键字会作为一个整体进行查询，不会进行分词。

```
public class Search {
    public static void main (String[] args) throws IOException {
        final CredentialsProvider provider = new BasicCredentialsProvider();
        provider.setCredentials(AuthScope.ANY,
                new UsernamePasswordCredentials("elastic", "elastic"));

        RestClientBuilder clientBuilder = RestClient.builder(new HttpHost("localhost", 9200));

        clientBuilder.setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
                httpClientBuilder.disableAuthCaching();
                return httpClientBuilder.setDefaultCredentialsProvider(provider);
            }
        });

        RestHighLevelClient client = new RestHighLevelClient(clientBuilder);

        // 构建查询请求对象 默认执行的是查询所有操作
        SearchRequest searchRequest = new SearchRequest("music_trendd");
        // 创建查询构建器
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        // 设置查询对象
        sourceBuilder.query(QueryBuilders.termQuery("lyrics", "safe"));

        // 关联查询方式到请求对象中
        searchRequest.source(sourceBuilder);
        // 执行查询操作
        SearchResponse response = client.search(searchRequest, RequestOptions.DEFAULT);
        // 获取查询的数据结果
        SearchHits hits = response.getHits();
        TotalHits totalHits = hits.getTotalHits();
        System.out.println("满足查询条件的总记录数:" + totalHits);

        SearchHit[] searchHits = hits.getHits();
        for (SearchHit searchHit : searchHits) {
            // 获取查询结果数据 json格式字符串
            String sourceAsString = searchHit.getSourceAsString();
            System.out.println(sourceAsString);
        }
        client.close();
    }
}
// 满足查询条件的总记录数:435 hits
```

```
sourceBuilder.query(QueryBuilders.termQuery("lyrics", "sad"));
// 满足查询条件的总记录数:0 hits
```

```
sourceBuilder.query(QueryBuilders.termQuery("lyrics", "light"));
// 满足查询条件的总记录数:2884 hits
```

可以把 Elasticsearch 中的 Term 查询用在 `text` 类型上，但是不建议这样做，因为返回的结果可能会较少。建议在 `keyword` 类型（通常是单个词汇的精确“布尔”查询）上使用 Term 查询。

## 非最小单位查询
会对字段进行**分词处理**，然后对每个词项进行查询，最后将结果进行合并，并根据算分结果将结果进行返回。

```
sourceBuilder.query(QueryBuilders.matchQuery("lyrics","fly me to the moon"));
// 满足查询条件的总记录数:1332 hits
```

```
sourceBuilder.query(QueryBuilders.matchQuery("lyrics","fly me to the moon").minimumShouldMatch("2"));
// 满足查询条件的总记录数:31 hits
```

## Debug
(1) Description:
```
Exception in thread "main" ElasticsearchStatusException[Elasticsearch exception [type=security_exception, reason=missing authentication credentials for REST request
```

需要使用认证的方式进行访问。更多请见[这里](https://blog.csdn.net/qq_33689414/article/details/91880700)。

(2) Description:
```
ERROR StatusLogger Log4j2 could not find a logging implementation. Please add log4j-core to the classpath. Using SimpleLogger to log to the console...
```

缺少了日志类Log4j2相应的包。

在配置文件 `pom.xml` 添加相应的依赖：
```
 <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.14.1</version>
</dependency>
```

在项目 `resources` 下配置日志输出格式文件 `log4j2.xml`：
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.opensymphony.xwork2" level="debug"/>
        <Logger name="org.apache.struts2" level="debug"/>
        <Root level="warn">
            <AppenderRef ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

## References
- [Term query](https://www.elastic.co/guide/en/elasticsearch/reference/7.15/query-dsl-term-query.html)
- [Elasticsearch集群,java操作es](https://www.mryhl.com.cn/bxws/a4ab.html)
