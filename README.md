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

安装步骤：

1、elasticsearch-head是基于nodejs开发的，所以需要安装nodejs环境

2、下载nodejs 安装包

进入nodejs官网

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/nodejs.png)

右键复制下载链接 https://nodejs.org/dist/v10.13.0/node-v10.13.0-linux-x64.tar.xz

root用户下进入/usr/local/src目录

```
cd /usr/local/src
##下载nodejs安装包
wget https://nodejs.org/dist/v10.13.0/node-v10.13.0-linux-x64.tar.xz
```

3、安装nodejs 

```
##创建nodejs安装目录
mkdir /usr/local/nodejs
##解压nodejs安装包到/usr/local/nodejs目录
tar -xvf node-v10.13.0-linux-x64.tar.xz -C /usr/local/nodejs/
```

4、配置nodejs环境变量 

 vim /etc/profile 

```
##配置nodejs 的HOME目录
export NODEJS_HOME=/usr/local/nodejs/node-v10.13.0-linux-x64
##加入nodejs的环境变量
export PATH=${JAVA_HOME}/bin:${NODEJS_HOME}/bin:$PATH
```

5、 让修改后的文件立即生效 

```
source /etc/profile
```

6、 测试nodejs的版本 

```
node -v
```

7、 安装git 用户从github下载elasticsearch-head插件 

```
yum -y install git
```

8、 下载elasticsearch-head 

```
cd /usr/local/es/
git clone git://github.com/mobz/elasticsearch-head.git
```

9、 进入到elasticsearch-head目录。进行安装 

 由于在执行npm install 时候报如下错误 

```
yum -y install epel-release

cd /usr/local/es/elasticsearch-head

npm install cnpm -g --registry=https://registry.npm.taobao.org

cnpm install -g
```

10、 修改Gruntfile.js文件 

```
cd /usr/local/es/elasticsearch-head/
vim ./Gruntfile.js
```

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/vim_Gruntfile.png)

11、 修改elasticsearch-head默认连接地址 

```
cd /usr/local/es/elasticsearch-head/_site/
vim app.js
```

打开文件 在命令行模式输入 “/this.base_uri” 进行搜索

然后修改为

 this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://you ip address:9200"; 

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/vim_base_url.png)

12、 修改elasticsearch服务配置文件允许跨域（在elasticsearch.yml文件中添加） 

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/vim_slaseticsearch_yml.png)

13、 启动elasticsearch 

 /usr/local/es/node-1/bin/elasticsearch -d 

 14、启动elasticsearch-head服务 

/usr/local/es/elasticsearch-head/node_modules/grunt/bin/grunt server

如果要后台启动 nohup /usr/local/es/elasticsearch-head/node_modules/grunt/bin/grunt server & exit