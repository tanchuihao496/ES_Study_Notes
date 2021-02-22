++++++++搜索引擎ElasticSearch学习笔记++++++++
注意：Github README文件编辑方法以及解决无法显示图片问题

修改hosts文件添加一下内容（C:\Windows\System32\drivers\etc\hosts）

```
199.232.68.133 raw.githubusercontent.com
199.232.68.133 githubusercontent.com
```



# 一、第一节ElasticSearch概述

#### P1

##### 1.1ElasticSearch是什么？

ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTfulweb接口。ElasticSearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。构建在全文检索开源软件Lucene之上的Elasticsearch，不仅能对海量规模的数据完成分布式索引与检索，还能提供数据聚合分析。据国际权威的数据库产品评测机构DB Engines的统计，在2016年1月，Elasticsearch已超过Solr等，成为排名第一的搜索引擎类应用

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

(1)下载中文分词器 https://github.com/medcl/elasticsearch-analysis-ik

```
下载elasticsearch-analysis-ik-master.zip
```

(2)解压elasticsearch-analysis-ik-master.zip

unzip elasticsearch-analysis-ik-master.zip

(3)进入elasticsearch-analysis-ik-master，编译源码

mvn clean install -Dmaven.test.skip=true

(4)在es的plugins文件夹下创建目录ik

(5)将编译后生成的elasticsearch-analysis-ik-版本.zip移动到ik下，并解压

(6)解压后的内容移动到ik目录下

mv elasticsearch/* ./



#### P5

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

####  

#### P6

##### 1.9安装kibana

Kibana是一个针对Elasticsearch的开源分析及可视化平台，使用Kibana可以查询、查看并与存储在ES索引的数据进行交互操作，使用Kibana能执行高级的数据分析，并能以图表、表格和地图的形式查看数据

(1)下载Kibana https://www.elastic.co/downloads/kibana

(2)把下载好的压缩包拷贝到/soft目录下

(3)解压缩，并把解压后的目录移动到/user/local/kibana

(4)编辑kibana配置文件

```

[root@H32 ~]# cd kibana/config/
[root@H32 config]# vim kibana.yml
添加：
server.host: "192.168.80.32"
elasticsearch.url: "http://192.168.80.32:9200"
```

先启动ES，然后再启动

```
cd /usr/local/kibana530
bin/kibana
```

 　kibana必须是在root下运行，否则会报错，启动失败 





# 二、第二节ElasticSearch基本操作

#### P7 

##### 2.1倒排索引

 倒排索引（Inverted Index）也叫反向索引，有反向索引必有正向索引。通俗地来讲，正向索引是通过key找value，反向索引则是通过value找key。 

Elasticsearch使用一种称为倒排索引的结构，它适用于快速的全文搜索，一个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文档列表。

 先来回忆一下我们是怎么插入一条索引记录的： 

```
curl -X PUT "localhost:9200/user/_doc/1" -H 'Content-Type: application/json' -d
'{
    "name" : "Jack",
    "gender" : 1,
    "age" : 20
}'
```

 其实就是直接PUT一个JSON的对象，这个对象有多个字段，在插入这些数据到索引的同时，Elasticsearch还为这些字段建立索引——倒排索引，因为Elasticsearch最核心功能是搜索。



[Term Index] ======>  [Term Dictionary] ======> [Postion List]

**Term（单词）**：一段文本经过分析器分析以后就会输出一串单词，这一个一个的就叫做Term（直译为：单词）

**Term Dictionary（单词字典）**：顾名思义，它里面维护的是Term，可以理解为Term的集合

**Term Index（单词索引）**：为了更快的找到某个单词，我们为单词建立索引

**Posting List（倒排列表）**：倒排列表记录了出现过某个单词的所有文档的文档列表及单词在该文档中出现的位置信息，每条记录称为一个倒排项(Posting)。根据倒排列表，即可获知哪些文档包含某个单词。（PS：实际的倒排列表中并不只是存了文档ID这么简单，还有一些其它的信息，比如：词频（Term出现的次数）、偏移量（offset）等，可以想象成是Python中的元组，或者Java中的对象）

 **示例：**

假设有个user索引，它有四个字段：分别是name，gender，age，address。画出来的话，大概是下面这个样子，跟关系型数据库一样 

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/eg_user_table.png)

上面的例子，Elasticsearch建立的索引大致如下： 

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/eg_user_index.png)

Elasticsearch分别为每个字段都建立了一个倒排索引。比如，在上面“张三”、“北京市”、22 这些都是Term，而[1，3]就是Posting List。Posting list就是一个数组，存储了所有符合某个Term的文档ID。

只要知道文档ID，就能快速找到文档



参考：https://www.cnblogs.com/sha0830/p/8000242.html

#### P8

##### 2.1.1使用标准化规则（normalization）

建立倒排索引的时候，会对拆分出的各个单词进行相应的处理，以提升后面搜索的时候能够搜索到相关联的文档的概率



#### P9

##### 2.1.2分词器介绍及内置分词器

分词器：从一串文本中切分出一个一个的词条。并对每个词条进行标准化

包括三部分：

character filter：分词之前的预处理。过滤掉HTML标签，特殊符号转换等

tokenizer：分词

token filter：标准化

内置分词器：

standard 分词器：(默认的)他会将词汇单元转换成小写形式，并去除停用词和标点符号，支持中文采用的方法为单字切分。

simple分词器：首先会通过非字母字符来分割文本信息，然后将词汇单元统一为小写形式。该分析器会去掉数字类型的字符。

Whitespace分词器：仅仅是去除空格，对字符没有lowcase化，不支持中文；并且不对生成的词汇单元进行其他的标准化处理。

language分词器：特定语言的分词器，不支持中文。



#### P10

##### **2.1.3配置中文分词器**

**常用的中文分词器**：

Smart Chinese Analysis: 官方提供的中文分词器,

IKAnalyzer: 免费开源的java分词器,目前比较流行的中文分词器之一,简单,稳定,想要特别好的效果,需要自行维护词库,支持自定义词典

结巴分词 : 开源的python分词器,github有对应的java版本,有自行识别新词的功能,支持自定义词典

Ansj中文分词: 基于n-Gram+CRF+HMM的中文分词的java实现,免费开源,支持应用自然语言处理

hanlp: 免费开源,国人自然处理语言牛人无私风险的

 个人对以上分词器进行了一个粗略对比 ,如下图: 

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/smart_chinese_analysis.png)

截止到目前为止 ,他们的分词准确性从高到低依次是:

hanlp> ansj >结巴>IK>Smart Chinese Analysis

结合准确性来看，选用中文分词器基于以下考虑:

官方的 Smart Chinese Analysis直接可以不考虑了

对搜索要求不高的建议选用 IK 学习成本低，使用教程多，还支持远程词典

对新词识别要求高的选用结巴分词

Ansj和hanlp均基于自然处理语言，分词准确度高，活跃度来讲hanlp略胜一筹



 IKAnalyzer 和 hanlp分词器的使用 

 **IK Analyzer** 

 截止目前 ,IK分词器插件的优势是支持自定义热更新远程词典。 

安装 ik分词器插件

es插件安装教程参考这里

ik的es插件地址: https://github.com/medcl/elasticsearch-analysis-ik/releases

使用的 es版本是6.4.0，下载时要注意对应es版本

在线安装 ik es插件 命令:

\# /opt/apps/elasticsearch-6.4.0/bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.0/elasticsearch-analysis-ik-6.4.0.zip

查看插件安装列表

\# sudo /opt/apps/elasticsearch-6.4.0/bin/elasticsearch-plugin list

IK配置

ik安装完毕后配置文件在 {ES_HOME}/config目录下, 本例目录是 /opt/apps/elasticsearch-6.4.0/config/analysis-ik/IKAnalyzer.cfg.xml

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/IK_config.png)

参考：http://blog.itpub.net/31524777/viewspace-2647360/



#### P11

##### 2.2使用ElasticSearch API实现CRUD

2.2.1CRUD操作

 添加索引： 

```
# ib是索引名
PUT /lib/ 
{
  "settings":{
	 "index":{
		"number_of_shards": 5, # 分片数
		"number_of_replicas": 1 # 副本数
	 }
  }
}

# 这样创建的索引配置信息将是默认的
PUT lib2

# 返回结果
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "lib"
}
```

 查看索引的配置信息  

```
GET /lib/_settings

# 返回结果
{
  "lib":{
	 "settings":{
		"creation_date": "1525922783367",
		"number_of_shards": 5,
		"number_of_replicas": 1,
		"uuid": "ICnRur_NTn2s9sM04XE_rQ",
		"version": {
		   "created": "6020499"
		},
		"provided_name": "lib"
	 }
  }
}

# 查看所有索引的配置
GET _all/_setings
```

 添加文档 

```
# 分别为索引:_index、类型:_type、Id:_id,
# id不设置将会自动生成一个随机且唯一的字符串作为该文档的id（需要使用POST请求）
PUT /lib/user/1  
{
  "frist_name": "Jane",
  "last_name": "Smith",
  "age": 32,
  "about": "I like to collect rock albums",
  "interests": ["music"]
}

# 返回结果
{
  "_index": "lib",
  "_type": "user",
  "_id": "1",
  "_version": 1,
  "result": "created", # 返回请求结果
  "_shards": {
	 "total": 1,
	 "successful": 1,
	 "failed": 0
  },
  "_seq_no": 0,
  "_primary_trem": 1
}
```

 查看文档 

```
# 查看文档
GET /lib/user/1

# 返回结果
{
  "_index": "lib",
  "_type": "user",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
	 "frist_name": "Jane",
	 "last_name": "Smith",
	 "age": 32,
	 "about": "I like to collect rock albums",
	 "interests": [
		"music"
	 ]
  }
}

# 查看部分字段
GET /lib/user/1?_source=age,about

# 返回结果
{
  "_index": "lib",
  "_type": "user",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": { 
	 "about": "I like to collect rock albums",
	 "age": 32
  }
}
```

 更新文档 

```
# 覆盖式修改PUT
PUT /lib/user/1  
{
  "frist_name": "Jane",
  "last_name": "Smith",
  "age": 36, # 原本的age是32
  "about": "I like to collect rock albums",
  "interests": ["music"]
}

# 返回结果
{
  "_index": "lib",
  "_type": "user",
  "_id": "1",
  "_version": 2, # 更新版本发生了改变
  "result": "updated", # 返回请求结果发生了改变
  "_shards": {
	 "total": 1,
	 "successful": 1,
	 "failed": 0
  },
  "_seq_no": 1, //
  "_primary_trem": 1
}

# 直接更新
POST /lib/user/1/_update
{
  "doc":{
	 "age": 30
  }
}

# 返回结果
{
  "_index": "lib",
  "_type": "user",
  "_id": "1",
  "_version": 3, # 更新版本再次发生了改变
  "result": "updated", # 返回请求结果依然为updated
  "_shards": {
	 "total": 1,
	 "successful": 1,
	 "failed": 0
  },
  "_seq_no": 2,
  "_primary_trem": 1
}
```



#### P12

##### 2.3.批量获取文档

使用es提供的Multi Get API：

使用Multi Get API可以通过索引名、类型名、文档id一次得到一个文档集合，文档可以来自同一个索引库，也可以来自不同索引库

Multi Get API官方地址：https://www.elastic.co/guide/en/elasticsearch/client/java-api/6.2/java-docs-multi-get.html

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/multi_get_api.png)

1.通过一个ID获得
2.或通过具有相同索引/类型的ID列表
3.你也可以从另一个索引
4.遍历结果集
5.您可以检查文档是否存在
6.访问_source字段



可以指定具体的字段：

GET _mget 

{

```
	"docs": [
	{
		"_index": "lib",
		"_type": "user",
		"_id": 1
	}, 
	{
		"_index": "lib",
		"_type": "user",
		"_id": 2
	}]
```

}

获取同索引同类型下的不同文档：

GET /lib/user/_mget 

{

```
	"docs": [
	{
		"_id": 1
	},
    {
		"_type": "user",
		"_id": 2
	}]
```

}

再简化：

GET /lib/user/_mget 

{

```
	"ids": ["1","2"]
```

}





#### P14

##### 2.4使用Bulk API实现批量操作

bulk的格式：

```
{action:{metadata}}\n

{requstbody}\n
```

action：(行为)
create：文档不存在时创建
update：更新文档
index：创建新文档或替换已有文档
delete：册期除一个文档

metadata:

```
 _index,_type,_id
```

create 和index的区别
如果数据存在，使用create操作失败，会提示文档已经存在，使用index则可以成功执行。
示例:
{"delete":{"_index":"lib"," _type":" user"," _id":"1"}}

批量添加：

POST /lib2/books/_bulk

```
{"index":{"_id":1}}
{"title":"Java" ,"price":55}
{"index":{"_id":2}}
{"title":"Html5","price":45}
{"index":{"_id":33}}
{"title":"Php","price":35}
```

bulk一次最大处理多少数据量：

bulk会把将要处理的数据载入内存中，所以数据量是有限制的，最佳的数据量不是一个确定的数值，它取决于你的硬件，你的文档大小以及复杂性，你的索引一般建议是1000-5000个文档，大小建议是5-15MB，默认不能超过100M，可以再es的配置文件（即$ES_HOME下的config下的elasticsearch.yml）中。





#### P14

##### 2.5版本控制

ElasticSearch采用了乐观锁来保证数据的一致性，也就是说，当用户对document进行操作时，并不需要对该document作加锁和解锁的操作，只需要指定要操作的版本即可，当版本号一致时，ElasticSearch会允许该操作顺利执行，而当版本号存在冲突时，ElasticSearch会提示冲突并抛出异常（VersionConflictEngineException异常）。

ElasticSearch的版本号的取值范围为1到2^63 - 1。

内部版本控制：使用的是 _version

外部版本控制：ElasticSearch在处理外部版本号时会与内部版本号的处理有些不同。它不再是检查_version是否与请求中指定的数值相同，而检查当前的_version是否比指定的数值小，如果请求成功，那么外部的版本号就会被存储到文档中_versionz中。

为了保持_version与外部版本控制的数据一致，使用version_type = external。

###### 1、内部版本控制：

 （1）使用的是_version 版本号要与文档的版本号一致 

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/es_version_controll_1.png)

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/es_version_controll_2.png)

3．外部版本控制：elasticsearch在处理外部版本号时会对内部版本号的处理有些不同。他不在是检查_version是否与请求中指定的数值相同，而是检查当前的_version是否比指定的数值小。如果请求成功，那么外部的版本号就会被存储到文档中的_version中。

4．为保持_version与外部版本控制的数据一致，使用version_type=external

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/es_version_controll_3.png)



#### P15

##### 2.6实现映射mapping

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/CRUD_mapping.png)

 创建索引的时候，可以预先定义字段的类型以及相关属性，这样就能够把日期字段处理成日期，把数字字段处理成数字，把字符串字段处理字符串值等支持的数据类型： 

 （1）核心数据类型（Code datatypes） 

```

字符型：string，string类型包括
 
text和keyword
 
text类型被用来索引长文本，在建立索引前会将这些文本进行分词，转化为词的组合，建立索引，允许es来检索这些词语。text类型不能用来排序和聚合。
 
keyword 类型不需要进行分词，可以被用来检索过滤、排序和聚合。keyword类型字段只能用本身来进行检索。
 
数字型：long，integer，short，byte，double，float
 
日期型：date
 
布尔型：boolean
 
二进制型：binary
```

 （2）复杂数据类型（Complex dataypes） 

```
数组类型（Array datatype）；数组类型不需要专门制定数组元素的type，例如：
 
字符型数组：["one","two"]
 
整数数组：[1,2]
 
数组型整数：[1,[2,3]] 等价于 [1,2,3]
 
对象数组：[{"name": "Mary", "age":12},{"name" : "john" , "age" : 10}]
 
对象类型（Object datatype）：_object_ 用于单个JSON对象；
 
嵌套类型（Nested datatype）：_nested_用于JSON数组；
```

 （3）地理位置类型（Geo datatypes） 

```
地理坐标类型（Geo-point datatype）：_geo_point_ 用于经纬度坐标；
 
地理形状类型（Geo-Shape datatype）：_geo_shape_ 用于类似于多边形的复杂形状；
```

 （4）特定类型（Specialised datatype） 

```

IPv4类型（IPv4 datatype）：_ip_ 用于IPv4地址；
 
Completion类型（Completion datatype）：_ completion _ 提供自动补全建议；
 
Token count类型（Token count datatype）：_ token _ count _ 用于统计做了标记的字段的index数目，该值会一直增加，不会因为过滤条件而减少。mapper-murmur3
 
类型：通过插件，可以通过 _ murmur3 _ 来计算index的hash值；
 
附加类型（Attachment datatype）：采用mapper-attachments
 
插件，可支持 _ attachments _ 索引
```

 支持的属性： 

```
"type" : "text", #是数据类型一般文本使用text(可分词进行模糊查询)；keyword无法被分词(不需要执行分词器)，用于精确查找

    "analyzer" : "ik_max_word", #指定分词器，一般使用最大分词：ik_max_word

    "normalizer" : "normalizer_name", #字段标准化规则；如把所有字符转为小写；具体如下举例

    "boost" : 1.5, #字段权重；用于查询时评分，关键字段的权重就会高一些，默认都是1；另外查询时可临时指定权重

    "coerce" : true, #清理脏数据：1，字符串会被强制转换为整数 2，浮点数被强制转换为整数；默认为true

    "copy_to" : "field_name", #自定_all字段；指定某几个字段拼接成自定义；具体如下举例

    "doc_values" : true, #加快排序、聚合操作，但需要额外存储空间；默认true，对于确定不需要排序和聚合的字段可false

    "dynamic" : true, #新字段动态添加 true:无限制 false:数据可写入但该字段不保留 'strict':无法写入抛异常

    "enabled" : true, #是否会被索引，但都会存储;可以针对一整个_doc

    "fielddata" : false, #针对text字段加快排序和聚合（doc_values对text无效）；此项官网建议不开启，非常消耗内存

    "eager_global_ordinals": true, #是否开启全局预加载,加快查询；此参数只支持text和keyword，keyword默认可用，而text需要设置fielddata属性

    "format" : "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis" ,#格式化 此参数代表可接受的时间格式 3种都接受

    "ignore_above" : 100, #指定字段索引和存储的长度最大值，超过最大值的会被忽略

    "ignore_malformed" : false ,#插入文档时是否忽略类型 默认是false 类型不一致无法插入

    "index_options" : "docs" ,

    # 4个可选参数

    # docs（索引文档号）,

    # freqs（文档号 + 词频），

    # positions（文档号 + 词频 + 位置，通常用来距离查询），

    # offsets（文档号 + 词频 + 位置 + 偏移量，通常被使用在高亮字段）

    # 分词字段默认是position，其他的默认是docs

    "index" : true, #该字段是否会被索引和可查询 默认true

    "fields": {"raw": {"type": "keyword"}} ,#可以对一个字段提供多种索引模式，使用text类型做全文检索，也可使用keyword类型做聚合和排序

    "norms" : true, #用于标准化文档，以便查询时计算文档的相关性。建议不开启

    "null_value" : "NULL", #可以让值为null的字段显式的可索引、可搜索

    "position_increment_gap" : 0 ,#词组查询时可以跨词查询 既可变为分词查询 默认100

    "properties" : {}, #嵌套属性，例如该字段是音乐，音乐还有歌词，类型，歌手等属性

    "search_analyzer" : "ik_max_word" ,#查询分词器;一般情况和analyzer对应

    "similarity" : "BM25",#用于指定文档评分模型，参数有三个：

    # BM25 ：ES和Lucene默认的评分模型

    # classic ：TF/IDF评分

    # boolean：布尔模型评分

    "store" : true, #默认情况false,其实并不是真没有存储，_source字段里会保存一份原始文档。

    # 在某些情况下，store参数有意义，比如一个文档里面有title、date和超大的content字段，如果只想获取title和date

    "term_vector" : "no" #默认不存储向量信息，

    # 支持参数yes（term存储），

    # with_positions（term + 位置）,

    # with_offsets（term + 偏移量），

    # with_positions_offsets(term + 位置 + 偏移量)

    # 对快速高亮fast vector highlighter能提升性能，但开启又会加大索引体积，不适合大数据量用
```

