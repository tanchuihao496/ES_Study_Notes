++++++++++++++++++++++++++++搜索引擎ElasticSearch学习笔记+++++++++++++++++++++++++++++++

# 一、第一节ElasticSearch概述

#### P1

##### 1.1ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTfulweb接口。ElasticSearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。构建在全文检索开源软件Lucene之上的Elasticsearch，不仅能对海量规模的数据完成分布式索引与检索，还能提供数据聚合分析。据国际权威的数据库产品评测机构DB Engines的统计，在2016年1月，Elasticsearch已超过Solr等，成为排名第一的搜索引擎类应用

概括:基于Restful标准的高扩展高可用的实时数招分析的全文搜索工具

优势：

海量级的搜索、实时性好，延迟最多是1s，另外便于扩展，方便增加节点，高可用。

##### 1.2ElasticSearch的基本概念

Index（索引）

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

##### 1.3Elasticsearch的架构

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/ES_Architecture_diagram.png)

ElasticSearch是基于Lucene开发的分布式搜索框架，包含如下特性：

1. 分布式索引、搜索。
2. 索引自动分片、负载均衡。
3. 自动发现机器、组建集群。
4. 支持Restful 风格接口。
5. 配置简单等

#### P2

##### 1.4RESTfull API

一种软件架构风格、设计风格，而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。在目前主流的三种Web服务交互方案中，REST相比于SOAP(Simple Object Acess protocol，简单对象访问协议)以及XML-RPC更加简单明了。

意思就是：表述性状态传递

它使用典型的HTTP方法，诸如GET\POST\DELETE\PUT来实现资源的获取，添加，修改，删除等操作。即通过HTTP动词来实现资源的状态扭转复制代码

GET 获取资源

POST用来新建资源（也可以用来更新资源）

DELETE用来删除资源

PUT用来更新资源

##### 1.5CRUL命令

以命令的方式执行HTTP协议的请求GET/POST/PUT/DELETE

curl www.baidu.com

curl -o tt.html www.baidu.com

显示响应的头信息

curl -i www.baidu.com

显示一次HTTP请求的通行过程

curl -v www.baidu.com

执行GET/POST/PUT/DETELE操作

curl -Ｘ GET/POST/PUT/DETELE url

#### P3

##### 1.6CentOS7下安装ELasticSearch6.2.4

(1)安装JDK

 **下载安装包jdk-8u261-linux-x64.tar.gz：** 

 https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html 

```
# cd /app

# tar xf jdk-8u261-linux-x64.tar.gz

# vim /etc/profile

添加：

#set java environment

export JAVA_HOME=/app/jdk1.8.0_261

export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

export PATH=$JAVA_HOME/bin:$PATH

# source /etc/profile

测试：

# java -version

```

（2）创建ES用户

```
# useradd es -p es
```

（3）配置运行ES需要的系统环境变量

```
# vim /etc/security/limits.conf

*为ES用户

添加：

* soft nofile 65536

* hard nofile 65536

* soft nproc 4096

* hard nproc 4096

```

```
# vim /etc/sysctl.conf

添加：

vm.max_map_count=262144

# sysctl -p
```

（4）安装elasticsearch

```
# cd /app

# wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.4.tar.gz

# tar xf elasticsearch-6.2.4.tar.gz

# chown -R es.es /app/elasticsearch-6.2.4

# su - es

$ /app/elasticsearch-6.2.4/bin//elasticsearch -d

测试：

$ curl 127.0.0.1:9200


```

#### P4

如果要实现远程访问：**

修改配置文件elasticsearch.yml的配置项network.host,然后重启elasticsearch：

```
$ vim /app/elasticsearch-6.2.4/config/elasticsearch.yml

network.host: 192.168.125.135
```

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/ES_Remote_access_test_diagram.png)



##### 1.7安装中文分词器

下载中文分词器 https://github.com/medcl/elasticsearch-analysis-ik

##### 

#### P4

##### 1.8安装Head插件

Head是elasticsearch的集群管理工具，可以用于数据的浏览和查询.

(1)elasticsearch-head是一款开源软件，被托管在github上面，所以如果我们要使用它，必须先安装git，通过git获取elasticsearch-head

(2)运行elasticsearch-head会用到grunt，而grunt需要npm包管理器，所以nodejs是必须要安装的

(3)elasticsearch5.0之后，elasticsearch-head不做为插件放在其plugins目录下了。使用git拷贝elasticsearch-head到本地

1.编译并安装git :   下载：git-2.17.0.tar.gz

```

tar -xvf git-2.17.0.tar.gz

cd git-2.17.0

执行如下命令：编译并指定安装目录进行安装

[root@cib129 git-2.17.0]# ./configure --prefix=/usr/local/git-2.17.0 && make install

[root@cib129 git-2.17.0]# vi /etc/profile  在最后添加如下环境变量：

export PATH=$PATH:/usr/local/git-2.17.0/bin

[root@cib129 git-2.17.0]# source /etc/profile

查看版本号：[root@cib129 git-2.17.0]#  git --version
```

 安装成功后删除git的解压目录：[root@cib129 hadoop]# rm -rf git-2.17.0 

2.安装nodejs  下载 （node-v10.0.0-linux-x64.tar.xz） 

```
[root@cib129 hadoop]# xz -d node-v10.0.0-linux-x64.tar.xz

[root@cib129 hadoop]# tar -xvf node-v10.0.0-linux-x64.tar

[root@cib129 hadoop]# mv node-v10.0.0-linux-x64 /usr/local/node

[root@cib129 node]# vi /etc/profile

在尾部添加export PATH=$PATH:/usr/local/node/bin

[root@cib129 node]# source /etc/profile

查看nodejs的版本： [root@cib129 node]# node -v

查看npm的版本:  [root@cib129 node]# npm -v

安装cnpm:   因为npm安装依赖包太慢（都是国外的），所以使用淘宝的镜像吧，安装cnpm

[root@cib129 node]# npm install -g cnpm --registry=https://registry.npm.taobao.org

```

 ![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/Head_install_cnpm.png) 

**注意上面的安装过程很慢，不要着急；**

查看cnpm版本，如下说明安装成功： [root@cib129 node]#  cnpm -v

 ![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/Head_install_cnpm_test.png) 

3.安装elasticsearch-head

**通过如下命令克隆远程elasticsearch-head到本地/usr/local/下**

[root@cib129 local]# git clone git://github.com/mobz/elasticsearch-head.git

 ![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/Head_install_cnpm_copy.png) 

