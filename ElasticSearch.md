# ElasticSearch

## 基础

###  认识

- ES具有支持分布式，可水平扩展的优势，同时提供restful接口，可被任何语言调用

- 官方链接 https://www.elastic.co/cn/

- ES结合kibana，Logstash，Beats是一套技术栈，叫做ELK，被广泛应用在日志数据分析，实时监控等领域，通常kibna用于数据可视化，ES用于存储，计算，搜索数据，其他两个用于数据的抓取

- 是一款开源搜索引擎，可以帮助我们从海量数据中快速找到需要的内容

- 文档：elasticsearch是面向文档存储的，可以是数据库中一条商品数据，一个订单信息，文档数据会被序列化为json格式后存储在elasticsearch中

- 索引：相同类型文档的集合，映射Mapping相当于表结构

### ES的安装

1. 拉取镜像

```
docker pull elasticsearch:7.4.0
```

2. 创建容器

```
docker run -id --name elasticsearch -d --restart=always -p 9200:9200 -p 9300:9300 -v D:\App\elasticsearch-7.4.0\plugins:/usr/share/elasticsearch/plugins -e "discovery.type=single-node" elasticsearch:7.4.0
```

3. 下载kibana https://www.elastic.co/cn/downloads/kibana

4. 配置kibana.yml文件

```
elasticsearch.hosts: ["http://localhost:9200"]
```

5. 运行kibana访问 http://localhost:5601
6. 安装分词器 https://release.infinilabs.com/analysis-ik/stable/

### 倒排索引

![D3E17318B9C8EA560A890C7B3E810FB7](D:\Application\1648053382\FileRecv\MobileFile\D3E17318B9C8EA560A890C7B3E810FB7.png)





























