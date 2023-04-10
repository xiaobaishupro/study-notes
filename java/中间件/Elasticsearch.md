## Elasticsearch

### 1.简介

Elasticsearch是一个基于Apache Lucene(TM)的开源搜索引擎，无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。 
但是，Lucene只是一个库。想要发挥其强大的作用，你需使用Java并要将其集成到你的应用中。Lucene非常复杂，你需要深入的了解检索相关知识来理解它是如何工作的。 
Elasticsearch也是使用Java编写并使用Lucene来建立索引并实现搜索功能，但是它的目的是通过简单连贯的RESTful API让全文搜索变得简单并隐藏Lucene的复杂性。

### 2.为什么使用ElasticSearch

#### **1.ElasticSearch的使用案例**

- 2013年初，GitHub抛弃了Solr，采取ElasticSearch 来做PB级的搜索。 “GitHub使用ElasticSearch搜索20TB
  的数据，包括13亿文件和1300亿行代码”
- 维基百科：启动以elasticsearch为基础的核心搜索架构
- SoundCloud：“SoundCloud使用ElasticSearch为1.8亿用户提供即时而精准的音乐搜索服务”
- 百度：百度目前广泛使用ElasticSearch作为文本数据分析，采集百度所有服务器上的各类指标数据及用户自定义数据，通过对各种数据进行多维分析展示，辅助定位分析实例异常或业务层面异常。目前覆盖百度内部20多个业务线（包括casio、云分析、网盟、预测、文库、直达号、钱包、风控等），单集群最大100台机器，200个ES节点，每天导入30TB+数据
- 新浪使用ES 分析处理32亿条实时日志
- 阿里使用ES 构建挖财自己的日志采集和分析体系

#### 2. ElasticSearch与solr的对比

- Solr 利用 Zookeeper 进行分布式管理，而 Elasticsearch 自身带有分布式协调管理功能;
- Solr 支持更多格式的数据，而 Elasticsearch 仅支持json文件格式；
- Solr 官方提供的功能更多，而 Elasticsearch 本身更注重于核心功能，高级功能多有第三方插件提供；
- Solr 在传统的搜索应用中表现好于 Elasticsearch，但在**处理实时搜索应用时效率明显低于 Elasticsearch**

#### 3.ElasticSearch下载

`ES和` Kibana`官网下载地址： https://www.elastic.co/downloads/past-releases

es目录结构

```yml
bin    启动文件
config   配置文件
    log4j2  日志配置文件
    jvm.options  JVM 虚拟机相关配置
    elasticsearch.yml    elasticsearch 的配置文件！ 默认 9200端口！    跨域！
     
lib       相关jar包
logs      日志！
modules   功能模块
plgins    插件！ ik
```

bin下elasticsearch.bat

访问9200端口访问测试

#### 4.ES核心概念

1、索引

2、字段类型（mapping）

3、文档

4、分片（documents）（倒排索引）

> 概述

<mark>集群，节点，索引，类型，文档，分片，映射是什么？</mark>

> ElasticSearch 是面向文档，关系型数据库 和 ElasticSearch 客观的对比！ 一切都是JSON ！

| Relational DB 关系型数据库 |  ElasticSearch  |
| :------------------------: | :-------------: |
|     数据库（database）     | 索引（indices） |
|   表（tables） 表（表）    |   types 类型    |
|         行（rows）         | documents 文件  |
|      字段（columns）       |  fields 菲尔兹  |

ElasticSearch（集群）中可以包含多个索引（数据库），每个索引中可以包含多个类型（表），每个类型下又包含多个文档（行），每个文档中又包含多个字段（列）。

### 物理设计：

elasticsearch 在后台把每个 **索引划分成多个分片**，每片分片 可以在集群中的不同服务器间迁移。

一个 就是一个集群！默认的集群名称就是 elasticsearch。

### 逻辑设计：

一个索引类型中，包含多个文档，比如说文档1，文档2。当我们索引一篇文档时，可以通过这样的一各顺序找到它：索引 -> 类型 -> 文档ID，通过这个组合我们就能索引到某个具体的文档。注意：ID不必是整数，实际上它是个字符串。

就是一条一条的数据

```
user``1`  `zhangsan ``18``2`  `kingtl  ``20
```

- elasticsearch是面向文档的，那么就意味着索引和搜索数据的最小单位是文档，elasticsearch中，文档有几个 重要属性：
  - 自我包含，一篇文档同时包含字段和对应的值，也就是同时包含 key：value！
  - 可以是层次型的，一个文档中包含子文档，复杂的逻辑实体就是这么来的！
  - 灵活的结构，文档不依赖预先定义的模式，关系型数据库中，要提前定义字段才能使用，在elasticsearch中，对于字段是非常灵活的，有时候，我们可以忽略该字段，或者动态的添加一个新的字段。
- 尽管可以随意的新增或者忽略某个字段，但是，每个字段的类型非常重要，比如一个年龄字段类型，可以是字符串也可以是整形。因为elasticsearch会保存字段和类型之间的映射即其他的设置。这种映射具体到每个映射的每种类型，这也是为什么在elasticsearch中，类型有时候也成为映射类型。

> 类型

- 类型是文档的逻辑容器，就像关系型数据库一样，表格是行的容器。类型中对于字段的定义称为映射，比如 name 映射为字符串类型。文档时无模式的，不需要拥有映射中所定义的所有字段，比如新增一个字段，那么elasticsearch是怎么做的呢？
- ElasticSear会自动的将新字段加入映射，但是这个字段的不确定它是什么类型，elasticsearch就开始才，如果这个值时18，那么ElasticSearch会认为它是整形，但是elasticsearch也可能猜不对，所以最安全的方式就是提前定义好所需要的的映射，这点跟关系型数据库殊途同归了，先定义好字段，然后再使用。

> 索引

就是数据库！

- 索引是映射；类型的容器，elasticsearch中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他配置。然后它们被存储到了各个分片上了。

> 倒排索引

- elasticsearch使用的是一种成为倒排索引的结构，采用Lucene倒排索作为底层。这种结构适用于快速的全文搜索，一个索引由文档中所有不重复的列表构成，对于每一个词，都有一个包含它的文档列表。

## Elasticsearch整合springboot

博客学习：https://juejin.cn/post/7155004642458337317
