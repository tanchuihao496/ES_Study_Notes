++++++++++++++++++++++++++++搜索引擎ElasticSearch学习笔记+++++++++++++++++++++++++++++++

# 一、第一节ElasticSearch概述

1.1ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTfulweb接口。ElasticSearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。构建在全文检索开源软件Lucene之上的Elasticsearch，不仅能对海量规模的数据完成分布式索引与检索，还能提供数据聚合分析。据国际权威的数据库产品评测机构DB Engines的统计，在2016年1月，Elasticsearch已超过Solr等，成为排名第一的搜索引擎类应用

概括:基于Restful标准的高扩展高可用的实时数招分析的全文搜索工具

优势：

海量级的搜索、实时性好，延迟最多是1s，另外便于扩展，方便增加节点，高可用。

1.2ElasticSearch的基本概念

Index

类似于mysql数据库中的database。

Type

类似于mysql数据库中的table表，es中可以在Index中建立type（table），通过mapping进行映射。

Document

由于es存储的数据是文档型的，一条数盘对应一篇文档即相当于mysql数据库中的一行数据row，一个文档中可以有多个字段也就是mysql数据库一行可以有多列。 

Fied

 es中一个文档中对应的多个列与mysql数据库中每一列时应。

Mapping

可以理解为mysql或者solr中对应的xchema，只不过有些时候es中的mapping增加了动态识射功能，尽觉很强大的样子，其实实际生产环境上不建议使用，最好还是开始制定好了对应的schema为主。

Mapping
可以理解为mysql或者solr中对应的xchema，只不过有些时候es中的mapping增加了动态识射功能，尽觉很强大的样子，其实实际生产环垝上不建议便用，最好还是开始制定好了对应的schema为主。

indexed
就是名义上的建立索引。mysql中一般会对经常使用的列增缺相应的索引用于提高查询速度，而在es中默认都是会加上索引的，除非你特殊制定不建立索引只是进行存储用于展示。这个需要看你具体的需求和业务进行设定了。

Query DSL

类似于mysql的sql语句，只不过在es中是使用的json格式的查询语句，专业术语就叫：QueryDSL

GET/PUT/POST/DELETE

分别类似与mysql中的select/update/delete......

