# Elasticsearch安装

## 1. Elasticsearch安装 <a id="elasticsearch&#x5B89;&#x88C5;"></a>

Elasticsearch官网：[https://www.elastic.co/cn/products/elasticsearch](https://www.elastic.co/cn/products/elasticsearch)

### 1.1.1. Elasticsearch介绍 <a id="elasticsearch&#x4ECB;&#x7ECD;"></a>

Elasticsearch（ES）是一个基于Lucene构建的开源、分布式、RESTful接口的全文搜索引擎。Elasticsearch还是一个分布式文档数据库，其中每个字段均可被索引，而且每个字段的数据均可被搜索，ES能够横向扩展至数以百计的服务器存储以及处理PB级的数据。可以在极短的时间内存储、搜索和分析大量的数据。通常作为具有复杂搜索场景情况下的核心发动机。

### 1.1.2. 下载 <a id="&#x4E0B;&#x8F7D;"></a>

官方网站下载链接：[https://www.elastic.co/cn/downloads/elasticsearch](https://www.elastic.co/cn/downloads/elasticsearch)

请根据自己的需求下载对应的版本。

### 1.1.3. 安装 <a id="&#x5B89;&#x88C5;"></a>

将上一步下载的压缩包解压，下图以Windows为例。

### 1.1.4. 启动 <a id="&#x542F;&#x52A8;"></a>

执行bin\elasticsearch.bat启动，默认在本机的9200端口启动服务。

使用浏览器访问elasticsearch服务，可以看到类似下面的信息。

