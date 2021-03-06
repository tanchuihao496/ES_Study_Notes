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

##### 2.4.使用Bulk API实现批量操作

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

**1、内部版本控制：**

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







#### P16

##### 2.6.1Object数据类型及手动创建mapping

 **1.multivalue field** 

```
PUT my_index/my_type/1
{
  "tags":[
    "tag1","tag2"
    ]
}
```

建立索引与string是一样的，数据类型不能混。

**2、empty field**
null , [] , [null]

**3、object field**

```
PUT company/employee/1
{
  "adress":{
    "country":"China",
    "province":"shanghai",
    "city":"shanghai"
  },
  "name":"jack",
  "age":27,
  "join_work":"2019-09-01"
}
```

 运行结果 

```
{
  "_index": "company",
  "_type": "employee",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```

 查看es自动建立的mapping 

```
{
  "company": {
    "mappings": {
      "employee": {
        "properties": {
          "adress": {
            "properties": {
              "city": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              },
              "country": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              },
              "province": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              }
            }
          },
          "age": {
            "type": "long"
          },
          "join_work": {
            "type": "date"
          },
          "name": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          }
        }
      }
    }
  }
}
```

 **在object类型在 es 底层会变成这样** 

```
{
    "name":[jack],
    "age":[27],
    "join_work":[2019-09-01],
    "address.country":[China],
    "address.province":[shanghai],
    "address.city":[shanghai]
}
```

 **如果我们的数据是数组包含对象** 

```
{
    "authors":[
       {"name":"jack","age":27},
       {"name":"Tom","age":28},
       {"name":"Lily","age":28}
    ]
}
```

 **es底层会将其转化成下面的格式(横式变成列式存储)** 

```
{
    "authors.name":[jack,Tom,Lily],
    "authors.age":[27,28,28]
}
```





#### P17-P18-P19

##### 2.7基本查询（Query查询）

**2.7.1数据准备**

```
PUT /ib3 
{"settings":{"number_of shards":3,"number of replicas ":0},"mappings":{"user":{"properties":{"name":{"type":"text"},"address":{"type":"text "},"age":{"type ":"integer "},"interests ":{"type ":"text"},"birthday":{"type":"date"}}}}}
```

```
GET /lib3/user/_search?q=name:lisi
```

```
GET /lib3/user/_search?q=name:zhaoliu&sort=age:desc
```

**2.7.2term查询和terms查询**

term query会去倒排索引中寻找确切的term，它并不知遵分词器的存在。这种查询适合keyword , numeric、date.

term：查询某个字段里含有某个关键词的文档

```
GET /lib3/user/_search/ {" query":{"term":{"interests":"changge"}}}
```

terms：查询某个字段里含有多个关键词的文档

```
GET /lib3/user/_search {"query":{"terms":{"interests":["hejiu","changge"]}}}
```

**2.7.3控制查询返回的数量**

from：从哪一个文档开始 

size：需要的个数

```
GET /lib3/user/_search {"from":0,"size":2,query":{"terms":{"interests":["hejiu","changge"]}}}
```

**2.7.4返回版本号**

```
GET /lib3/user/_serarch {"version":true,query":{"terms":{"interests":["hejiu","changge"]}}}
```

**2.7.5match查询**

match query知道分词器的存在，会对filed进行分词操作，然后再查询

```
GET /lib3/user/_search {"query":{"match":{"name":"zhaoliu"}}}
```

```
GET /lib3/user/_search {"query":{"match":{"age":20}}}
```

match_all：查询所有文档

```
GET /lib3/user/_search {"query":{"match_all":{}}}
```

multi_match：可以指定多个字段

```
GET /lib3/user/_search {"query":{"multi_match":{"query":"lvyou","fields":["interests":"name"]}}}
```

match_phrase：短语匹配查询

ElasticSearch引擎首先分析（analyze）查询字符串，从分析后的文本中构建短语查询，这意味着必须匹配短语中的所有分词，并且保证各个分词的相对位置不变：

```
GET lib3/user/_search
{"query":{"match_phrase":{"interests":"duanlian,shouxiangsheng"}}}
```

**2.7.6指定返回的字段**

```
GET /lib3/userl_search {"_source":{"address":"name"}," query ":{"match ":{"interests ":"唱歌”"}}}
```

**2.7.7控制加载的字段**

GET /lib3/user/_search 
{
	"query": {
		"match_all": {}
	},

```
 	"_source": {
		"includes": ["name", "address"],
		"excludes": ["age", "birthday"]
	}
```

}

使用通配符*

GET /lib3/user/_search 

{
	"_source": {
		"includes": "addr*",
		"excludes": ["name", "bir*"]

```
	},
	"query": {
		"match_all": {}
	}
```

}

**2.7.8排序**

使用sort实现排序：desc降序，asc升序

```
GET /lib3/user/_search {"query":{"match_all":{}},"sort":[{"age":{"order ":"asc"}}]}
```

```
GET /lib3/user/_search {"query":{"match_all":{}},"sort":[{"age":{"order ":"desc"}}]}
```

**2.7.9前缀匹配查询**

```
GET /lib3/user/_search {"query":{"match_phrase_prefix":{"name":{"query":"赵"}}}}
```

**2.7.10范围查询**

range：实现范围查询

参数：from，to，include_lower，include_upper，boost

include_lower：是否包含范围左边界，默认是true

include_upper：是否包含范围右边界，默认是true

```
GET /lib3/user/_search {"query":{"range":{"birthday":{"from":"1990-10-10","to":"2018-05-01"}}}}
```

```
GET /lib3/user/_search {"query":{"range":{"age":{"from":20,"to":25,"include_lower":true,"include_upper":false}}}}
```

**2.7.11wildcard查询**

```
允许使用通配符*和?来进行查询
*代表0个或多个字符
?代表任意一个字符
GET /lib3/user/_search {"query":{"wildcard":{"name":"赵*"}}}
GET /lib3/user/_search {"query":{"wildcard":{"name":"li?i"}}}

```

**2.7.12fuzzy实现模糊查询**

value：查询的关键字

boost：查询的权值，默认值是1.0

min_similarity：设置匹配的最小相似度，默认值头0.5，对于字符串，取值为0-1(包括0和1);对于数值，取值可能大于1；对于日期型取值为1d,1m等，1d就代表1天

prefix_length：指明区分词项的共同前缀长度，默认是0

max_expansions：查询中的词项可以扩展的数目，默认可以无限大

```
GET /lib3/user/_search {"query":{"fuzzy":{"interests":"唱歌"}}}
```

```
GET /lib3/user/_search {"query":{"fuzzy":{"interests":{"value":"喝酒"}}}}
```





#### P20

##### 2.8.Filter查询

filter是不计算相关性的，同时可以cache。因此，filter速度要快于query。

```
POST /lib4/items/_bulk {"index":{"_id":1}}
{"price":40,"itemID":"ID100123"}
{"index":{"_id":2}}
{"price":50,"itemID":"ID100124"}
{"index":{"_id":3}}
{"price":25,"itemID":"ID100124"}
{"index":{"_id":4}}
{"price":30,"itemID":"ID100125"}
{"index":{"_id":5}}
{"price":null,"itemID":"ID100127"}
```

**2.8.1简单的过滤查询**

```
GET /lib4/items/_search {"post_filter":{"item":{"price":40}}}
GET /lib4/items/_search {"post_filter":{"item":{"price":[25,40]}}}
GET /lib4/items/_search {"post_filter":{"item":{"itemID":"ID100123"}}}
```

查询分词器分析的结果：

```
GET /lib4/_mapping
```

不希望商品id字段被分词，则重新创建映射

```
DELETE /lib4
```

```
PUT /lib4 {"mapping":{"items":{"properties":{"itemID":{"type":"text","index":false}}}}}
```

**2.8.2bool过滤查询**

可以实现组合过滤查询

格式：

```
{"bool":{"must":[],"should":[],"must_not":[]}}
```

must：必须满足的条件---and

should：可以满足也可以不满足的条件---or

must_not：不需要满足的条件--not

GET /lib4/items/_search {"post_filter":{"bool":{"should":[{"term":{"price":25}},{"term":{"itemID":"ID100123"}}

```
			],
			"must_not": {
				"term": {
					"price": 30
				}
			}
		}
	}
```

}

嵌套使用bool：

```
GET /lib4/items/_search {"post_filter":{"bool":{"should":[{"term":{"itemID":"id100123"}},{"bool":{"must":[{"term":{"itemID":"id100124"}},{"item":{"price":40}}]}}]}}}
```

**2.8.3范围过滤**

gt：>

lt：<

gte：>=

lte：<=

```
GET/lib4/items/_search {"post_filter":{"range":{"price":{"gt":25,"lt ":50}}}}
```

**2.8.4过滤非空**

```
GET/lib4/items/_search {"query":{"bool":{"filter":{"exists":{"field":"price"}}}}}
GET/lib4/items/_search {"query":{"constant_score":{"filter":{"exists":{"field":"price"}}}}}
```

**2.8.5过滤器缓存**

ElasticSearch提供了一种特殊的缓存，即过滤器缓存（filter cache)，用来存储过滤器的结果，被缓存的过滤器并不需要消耗过多的内存(因为它们只存储了哪些文档能与过滤器相匹配的相关信息)，而且可供后续所有与之相关的查询重复使用，从而极大地提高了查询性能。

 官网地址：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-cache.html 

上面描述的以及之前讨论过的的可以简单把Query Cache总结为以下几点

\1.  Query Cache是节点级别的，每个节点上的所有分片共享一份缓存

\2.  Query Cache采用LRU缓存失效策略

\3.  Query Cache只能在Filter Context中被使用

\4.  Query Cache可以通过 indices.queries.cache.size来设置缓存占用大小，默认10%，可手动设置比如512mb

\5.  Query Cache实际缓存的是Bitset（位图），一个Query clause对应一个Bitset

\6.  缓存要生效，必须满足两个条件

​    a）查询对应的segments所持有的文档数必须大于10000

​    b）查询对应的segments所持有的文档数必须大于整个索引size的3%

\7.  当新索引文档时，Query Cache不会重新计算，而是判断索引的文档是否符合缓存对应的Query clause，满足则加入BitSet中



注意:ElasticSearch并不是默认缓存所有过滤器，以下过滤器默认不缓存：

numeric_range、script、geo_bbox、geo_distance、geo_distance_range、geo_polygon、geo_shape、and、or、not

默认是开启缓存的过滤器：
exists,missing,range,term,terms

开启方式：在filter查询语句后边加上

```
"_catch":true
```



#### P21

##### 2.9.聚合查询

 sum、min、max、avg、cardinality:求基数、terms:分组 

 以求和为例： 

```

GET /lib4/items/_search
{
  "size":0,
  "aggs": {
    "price_sum": { //名字自定义
      "sum": {
        "field": "price"
      }
    }
  }

```

 执行结果 

```

{
  "took": 9,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 5,
    "max_score": 0,
    "hits": []
  },
  "aggregations": {
    "price_sum": {
      "value": 185
    }
  }
}
```



#### P22

##### 2.10.复合查询

将多个基本查询组合成单一查询的查询

**2.10.1使用bool查询**

接收以下参数：

must：文档必须匹配这些条件才能被包含进来。

must_not：文档必须不匹配这些条件才能被包含进来。

should：如果满足这些语句中的任意语句，将增加_score，否则，无任何影响。它们主要用于修正每个文档的相关性得分。

filter：filter:必须匹配，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。

filter:必须匹配，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。
相关性得分是如何组合的。每一个子查询都独自地计算文档的相关性得分。一旦他们的得分被计算出来，bool查询就将这些得分进行合并并且返回一个代表整个布尔操作的得分。

下面的查询用于查找 title字段匹配how to make milions并且不被标识为spam的文档。那些被标识为starred或在2014之后的文档，将比另外那些文档拥有更高的排名。如果两者都满足，那么它排名将更高：

```
{"bool":{"must":{"match":{"title":"how to make millions"}},"must_not":{"match":{"tag":"spam"}},"should":[{"match":{"tag":"strred"}},{"range":{"date":{"gte":"2014-01-01"}}}]}}
```

如果没有must 语句，那么至少需要能够匹配其中的一条should 语句。但，如果存在至少一条must语句，则对 should语句的匹配没有要求。如果我们不想因为文档的时间而影响得分，可以用filter语句来重写前面的例子：

```
{"bool":{"must":{"match":{"title":"how to make millions"}},"must_not":{"match":{"tag":"spam"}},"should":[{"match":{"tag":"strred"}}],"filter":{"range":{"date":{"gte":"2014-01-01"}}}}}
```

通过将range查询移到 fiter语句中，我们将它转成不评分的查询，将不再影响文档的相关性排名。由于它现在是一个不评分的查询，可以使用各种对flter查询有效的优化手段来提升性能。

bool查词本身也可以被用做不评分的查询。简单地将它放置到filter语句中并在内部构建布尔逻辑：

```
{"bool":{"must":{"match":{"title":"how to make millions"}},"must_not":{"match":{"tag":"spam"}},"should":[{"match":{"tag":"starred"}}],"filter":{"bool":{"must":[{"range":{"date":{"gte":"2014-01-01"}}},{"range":{"price":{"lte ":"29.99 "}}}],"must_not ":[{"term ":{"category ":"ebooks "}}]}}}}
```

**2.10.2constant_score查询**

```
{"constant_score ":{"filter":{"term":{"category":"ebooks"}}}}
```

term查询被放置在constant_score中，转成不评分的filter，这种方式可以用来取代只有filter语句的bool查询。 







# 第三节 ElasticSearch原理



#### P23

##### 3.1.解析es的分布式架构

**3.1.1分布式架构的透明隐藏特性**

ElasticSearch是一个分布式系统，隐藏了复杂的处理机制
分片机制：我们不用关心数据是按照什么机制分片的、最后放入到哪个分片中
分片的副本：
集群发现机制(cluster discovery):比如当前我们启动了一个es进程，当启动了第二个es进程时，这个进程作为一个node自动就发现了集群，并且加入了进去。
shard负载均衡：比如现在有10shard，集群中有3个节点，es会进行均衡的进行分配，以保持每个节点均衡的负载请求。
请求路由



**3.1.2扩容机制**

垂直扩容：购置新的机器，替换已有的机器
水平扩容：直接增加机器

**3.2.3rebalance**

增加或减少节点时会自动均衡

**3.1.4master节点**

主节点的主要职责是和集群操作相关的内容，如创建或删除索引，跟踪哪些节点是群集的一部分，并决定哪些分片分配给相关的节点。稳定的主节点对集群的健康是非常重要的。

**3.1.5节点对等**

每个节点都能接收请求每个节点接收到请求后都能把该请求路由到有相关数据的其它节点上接收原始请求的节点负责采集数据并返回给客户端





#### P24

##### 3.2.分片和副本机制

1.index包含多个shard

2.每个shard都是一个最小工作单元，承载部分数据；每个shard都是一个lucene实例，有完整的建立索引和处理请求的能力

3.增减节点时，shard会自动在nodes中负载均衡

4.primary shard和replica shard，每个document肯定只会存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard

5.replica shard是primary shard的副本，负责容错，以及承担读请求负载

6.primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改

7.primary shard的默认数量是5，replica默认是1，默认有10个shard，5个primary shard，5个replica shard



##### 3.3.单节点环境下创建索引分析

PUT /myindex { "settings" : { "number_of_shards": 3, "number_of_replicas" : 1}}

这个时候，只会将3个primary shard分配到仅有的一个node上去，另外3个replica shard是无法分配的（一个shard的副本replica，他们两个是不能在同一个节点的)。集群可以正常工作，但是一旦出现节点宕机，数据全部丢失，而且集群不可用，无法接收任何请求。



##### 3.4.两个节点环境下创建索引分析

将3个primary shard分配到一个node上去，另外3个replica shard分配到另一个节点上



#### P25

##### 两个节点环境下replica shard是如何分配的



#### P26

##### 3.5.水平扩容的过程

1.扩容后primary shard和replica shard会自动的负载均衡

⒉.扩容后每个节点上的shard会减少，那么分配给每个shard的CPU，内存，IO资源会更多，性能提高

3.扩容的极限，如果有6个shard，扩容的极限就是6个节点，每个节点上一个shard，如果想超出扩容的极限，比女亚说扩容到9个节点，那么可以增加replicashard的个数

4.6个shard，3个节点，最多能承受几个节点所在的服务器宕机?(容错性)任何一台服务器宕机都会丢失部分数据
为了提高容错性，增加shard的个数: 9个shard，(3个primary shard，6个replicashard)，这样就能容忍最多两台服务器宕机了总结:扩容是为了提高系统的吞吐量，同时也要考虑容错性，也就是让尽可能多的服务器宕机还能保证数据不丢失



#### P27

##### 3.6.ElasticSearch的容错机制

以9个shard，3个节点为例:
1.如果master node 宕机，此时不是所有的primary shard都是Active status，所以此时的集群状态是red。

容错处理的第一步:是选举—台服务器作为master容错处理的第二步:新选举出的master会把挂掉的primary shard的某个replicashard提升为nprimary sharc,此时集群的状态为yellow，因为少了一个replica shard，并不是所有的replica shard都是active status

容错处理的第三步∶重启故障机，新master会把所有的副本都复制一份到该节点上，(同步一下宕机后发生的修改)），此时集群的状态为green，因为所有的primary shard和replica shard都是Active status



#### P28

##### 3.7.文档的核心元数据

1._index:
说明了一个文档存储在哪个索引中
同一个索引下存放的是相似的文档(文档的field多数是相同的)索引名必须是小写的，不能以下划线开头，不能包括逗号

![Image text](https://github.com/tanchuihao496/ES_Study_Notes/blob/master/img/yuanshuju.png)

```
（1）代表一个document存放在哪个index中
（2）类似的数据放在一个索引，非类似的数据放不同索引：product index（包含了所有的商品），sales index（包含了所有的商品销售数据），inventory index（包含了所有库存相关的数据）。如果你把比如product，sales，human resource（employee），全都放在一个大的index里面，比如说company index，不合适的。
```

2._type:

表示文档属于索引中的哪个类型—个索引下只能有一个type
类型名可以是大写也可以是小写的，不能以下划线开头，不能包括逗

```
（1）代表document属于index中的哪个类别（type）
（2）一个索引通常会划分为多个type，逻辑上对index中有些许不同的几类数据进行分类：因为一批相同的数据，可能有很多相同的fields，但是还是可能会有一些轻微的不同，可能会有少数fields是不一样的，举个例子，就比如说，商品，可能划分为电子商品，生鲜商品，日化商品，等等。
（3）type名称可以是大写或者小写，但是同时不能用下划线开头，不能包含逗号
```



3._id:
文档的唯一标识，和索引，类型组合在一起唯—标识了一个文档
可以手动指定值，也可以由es来生成这个值

```
根据应用情况来说，是否满足手动指定document id的前提：
一般来说，是从某些其他的系统中，导入一些数据到es时，会采取这种方式，就是使用系统中已有数据的唯一 标识，作为es中document的id。举个例子，比如说，我们现在在开发一个电商网站，做搜索功能，或者是OA系 统，做员工检索功能。这个时候，数据首先会在网站系统或者IT系统内部的数据库中，会先有一份，此时就肯定会 有一个数据库的primary key（自增长，UUID，或者是业务编号）。如果将数据导入到es中，此时就比较适合采用数据在数据库中已有的primary key。

如果说，我们是在做一个系统，这个系统主要的数据存储就是es一种，也就是说，数据产生出来以后，可能就没有id，直接就放es一个存储，那么这个时候，可能就不太适合说手动指定document id的形式了，因为你也不知道id应该是什么，此时可以采取下面要讲解的让es自动生成id的方式。

（4）自动生成的id，长度为20个字符，URL安全，base64编码，GUID，分布式系统并行生成时不可能会发生冲突
```



#### P29

##### 3.8.文档id生成方式

1.手动指定

put /index/type/66

通常是把其它系统的已有数据导入到es时

2.由es生成id值

post /index/type

es生成的id长度为20个字符，使用的是base64编码，URL安全，使用的是GUID算法，分布式下并发生成id值时不会冲突



#### P30

##### 3.9._source元数据分析

其实就是我们在添加文档时request body中的内容指定返回的结果中含有哪些字段:
get /index/type/1?_source=name

```
PUT /test_index/test_type/1
{
  "test_field1":"test_field1",
  "test_field2":"test_field2"
}
GET /test_index/test_type/1
{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "test_field1": "test_field1",
    "test_field2": "test_field2"
  }
}
```

 _source元数据：就是说，我们在创建一个document的时候，使用的那个放在request body中的json串，默认情况下，在get的时候，会原封不动的给我们返回回来。 





#### P31

##### 3.10.改变文档内容原理解析

替换方式:

PUT/lib/user/4 { "first_name" : "Jane","last_name" : "Lucy",

"age" : 24,

"about": "l like to collect rock albums",

"interests" : [ "music” ] }

修改方式(partial update):

POST /lib/user/2/_update ( "doc":{ "age":26}}

删除文档:标记为deleted，随着数据量的增加，es会选择合适的时间蒯除掉



#### P32

##### 3.11.基于groovy脚本进行部分更新

es有内置的脚本支持，可以基于groovy脚本实现复杂的操作

1.修改年龄

POST /lib/user/4/_update {"script":"ctx._source.age+=1"}

2.修改名字

POST /lib/user/4/_update  {"script ":"ctx._source.last_name+ ='hehe"}

3.添加爱好

POST /lib/user/4/_update  {"script ":{"source":" ctx. source.interests.add(params.tag)","params":{"tag":"picture"}}}

4.删除爱好

POST /ib/user/4/_update {"script":{"souce":"ctx.source interests.remove(ctx._source.interests.indexOfiPparams.tagl)","params ":{"tag":"picture"}}}

5.删除文档

POST/lib/user/4/_update {"script":{"source":"ct.op = ctx.source.age==params.count?'delete':none","params":{"count":29}}}



#### P33

##### 3.12.更新文档对并发问题的处理

1：乐观锁控制

```
很乐观，每次读取数据时，都认为别人不会修改数据，因此也不锁定数据，只有在提交数据时，才会检查数据完整性。这种方式可以省去锁的开销，进而提高吞吐量。
```

2：悲观锁控制

```
很悲观，每一次去读取数据的时候，都认为别人可能会修改数据，所以屏蔽一切可能破坏数据完整性的操作。关系型数据库中，悲观锁使用较多，例如行锁、表锁等等。
```

在 es 中，实际上使用的就是乐观锁。



#### P34

##### 3.13.文档数据路由原理解析

1.文档路由到分片上:

一个索引由多个分片构成，当添加(删除，修改)一个文档时，es就需要决定这个文档存储在哪个分片上，这个过程就称为数据路由(routing)

⒉路由算法:

```
shard=hash(routing) % number_of_pirmary_shards
```

示例:一个索引，3个primary shard

(1每次增删改查时，都有一个routing值，默认是文档的_id的值

(2)对这个routing值使用哈希函数进行计算

(3)计算出的值再和主分片个数取余数

余数肯定在0--- (number_of_pirmary_shards-1）之间，文档就在对应的shard上

routing值默认是文档的_id的值，也可以手动指定一个值，手动指定对于负载均衡以及提高批量读取的性能都有帮助

3.primary shard个数一旦确定就不能修改了



#### P35

##### 3.14.文档增删改内部原理

1:发送增删改请求时，可以选择任意一个节点，该节点就成了协调节点(coordinating node)

⒉.协调节点使用路由算法进行路由，然后将请求转到primary shard所在节点，该节点处理请求，并把数据同步到它的replica shard

3.协调节点对客户端做出响应



#### P36

##### 3.15.写一致性原理和quorum机制

1.任何一个增删改操作都可以跟上一个参数consistency

可以给该参数指定的值:
one: (primary shard)只要有一个primary shard是活跃的就可以执行

all: (all shard)所有的primary shard和replica shard都是活跃的才能执行

quorum: (default)默认值，大部分shard是活跃的才能执行（例如共有6个shard，至少有3个shard是活跃的才能执行写操作)



2.quorum机制:

多数shard都是可用的，
int((primary+number_of_replica)/2)+1

例如:3个primary shard，1个replica

int((3+1)/2)+1=3

至少3个shard是活跃的

注意:可能出现shard不能分配齐全的情况

比如:1个primary shard,1个replica int(1+1)/2)+1=2但是如果只有一个节点，因为primary shard和replica shard不能在同一个节点上，所以仍然不能执行写操作

再举例: 1个primary shard,3个replica,2个节点

int((1+3)/2)+1=3

最后:当活跃的shard的个数没有达到要求时，es默认会等待一分钟，如果在等待的期间活跃的shard的个数没有增加，则显示timeout

put /index/type/id?timeout=60s





#### P37

##### 3.16.文档查询内部原理

第一步:查询请求发给任意一个节点，该节点就成了coordinating node，该节点使用路由算法算出文档所在的primary shard

第二步∶协调节点把请求转发给primary shard也可以转发给replica shard(使用轮询调度算法(Round-Robin Scheduling，把请求平均分配至primary shard和replica shard)

第三步:处理请求的节点把结果返回给协调节点，协调节点再返回给应用程序

特殊情况:请求的文档还在建立索引的过程中，primary shard上存在，但replica shar上不存在，但是请求被转发到了replica shard上,这时就会提示找不到文档



#### P38

##### 3.17bulk批量操作的json格式解析

1.1 bulk的基础概念

 `bulk`是`es`提供的一种`批量增删改`的操作API。 

1.2 bulk的语法

 `bulk`对`JSON串`的有着严格的要求。每个JSON串`不能换行`，只能放在同一行，同时，`相邻的JSON串之间必须要有换行`（Linux下是\n；Window下是\r\n）。bulk的每个操作必须要`一对JSON串`（delete语法除外）。 

```
{ action: { metadata }}
{ request body        }
{ action: { metadata }}
{ request body        }
```

 例如，加入现在要给`example`的`docs`中新增一个文档。其表示如下： 

```
POST _bulk
{"create": {"_index": "example", "_type": "docs", "_id": 11}}
{"name": "test_bulk", "counter":"100"}


#查询example所有数据,发现id为11的已经添加成功
GET example/docs/_search
{
  "query": {
    "match_all": {}
  }
}
```

1.3 bulk的操作类型

```
- `create` 如果文档不存在就创建，但如果文档存在就返回错误
- `index` 如果文档不存在就创建，如果文档存在就更新
- `update` 更新一个文档，如果文档不存在就返回错误
- `delete` 删除一个文档，如果要删除的文档id不存在，就返回错误
```

 其实可以看得出来`index`是比较常用的。还有bulk的操作，某一个操作失败，是不会影响其他文档的操作的，它会在返回结果中告诉你失败的详细的原因。

2.1 测试数据准备

 索引及映射结构准备 

```
PUT example
PUT example/docs/_mapping
{
  "properties": {
    "id": {"type": "long"},
    "name": {"type": "text"},
    "counter": {"type": "integer"},
    "tags": {"type": "text"}
  }
}
```

2.1 批量插入

插入的`action`为`index`。

示例语法

```
POST example/docs/_bulk
{"index": {"_id": 1}}
{"id":1, "name": "admin", "counter":"10", "tags":["red", "black"]}
{"index": {"_id": 2}}
{"id":2, "name": "张三", "counter":"20", "tags":["green", "purple"]}
{"index": {"_id": 3}}
{"id":3, "name": "李四", "counter":"30", "tags":["red", "blue"]}
{"index": {"_id": 4}}
{"id":4, "name": "tom", "counter":"40", "tags":["orange"]}
```

返回结果

从下面的结果可以看出，四个文档均新建成功。

```
{
  "took": 18,
  "errors": false,
  "items": [
    {
      "index": {
        "_index": "example",
        "_type": "docs",
        "_id": "1",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 2,
          "failed": 0
        },
        "_seq_no": 0,
        "_primary_term": 1,
        "status": 201
      }
    },
    {
      "index": {
        "_index": "example",
        "_type": "docs",
        "_id": "2",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 2,
          "failed": 0
        },
        "_seq_no": 0,
        "_primary_term": 1,
        "status": 201
      }
    },
    {
      "index": {
        "_index": "example",
        "_type": "docs",
        "_id": "3",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 2,
          "failed": 0
        },
        "_seq_no": 0,
        "_primary_term": 1,
        "status": 201
      }
    },
    {
      "index": {
        "_index": "example",
        "_type": "docs",
        "_id": "4",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 2,
          "failed": 0
        },
        "_seq_no": 1,
        "_primary_term": 1,
        "status": 201
      }
    }
  ]
}
```

2.2 批量修改

修改的`action`为`update`。

示例语法

```
POST example/docs/_bulk
{"update": {"_id": 1}}
{"doc": {"id":1, "name": "admin-02", "counter":"11"}}
{"update": {"_id": 2}}
{"script":{"lang":"painless","source":"ctx._source.counter += params.num","params": {"num":2}}}
{"update":{"_id": 3}}
{"doc": {"name": "test3333name", "counter": 999}}
{"update":{"_id": 4}}
{"doc": {"name": "test444name", "counter": 888},  "doc_as_upsert" : true}
```

 返回结果 

```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 4,
    "max_score": 1,
    "hits": [
      {
        "_index": "example",
        "_type": "docs",
        "_id": "2",
        "_score": 1,
        "_source": {
          "id": 2,
          "name": "张三",
          "counter": "202",
          "tags": [
            "green",
            "purple"
          ]
        }
      },
      {
        "_index": "example",
        "_type": "docs",
        "_id": "4",
        "_score": 1,
        "_source": {
          "id": 4,
          "name": "test444name",
          "counter": 888,
          "tags": [
            "orange"
          ]
        }
      },
      {
        "_index": "example",
        "_type": "docs",
        "_id": "1",
        "_score": 1,
        "_source": {
          "id": 1,
          "name": "admin-02",
          "counter": "11",
          "tags": [
            "red",
            "black"
          ]
        }
      },
      {
        "_index": "example",
        "_type": "docs",
        "_id": "3",
        "_score": 1,
        "_source": {
          "id": 3,
          "name": "test3333name",
          "counter": 999,
          "tags": [
            "red",
            "blue"
          ]
        }
      }
    ]
  }
}
```

 由上面示例我们可以看出，批量更新支持参数选项：`doc`（部分文档），`upsert`，`doc_as_upsert`，`脚本，``params`（用于脚本），`lang`（用于脚本）和`_source`。 

2.3 批量删除

修改的`action`为`delete`。

示例如下。

```
POST example/docs/_bulk
{"delete": {"_id": 1}}
{"delete": {"_id": 2}}
{"delete": {"_id": 3}}
{"delete": {"_id": 4}}
```

2.4 批量的混合操作

```
POST _bulk
{ "index" : { "_index" : "example", "_type" : "docs", "_id" : "1" } }
{ "name" : "value11111" }
{ "delete" : { "_index" : "example", "_type" : "docs", "_id" : "2" } }
{ "create" : { "_index" : "example", "_type" : "docs", "_id" : "3" } }
{ "tags" : "value333" }
{ "update" : {"_id" : "4", "_type" : "docs", "_index" : "example"} }
{ "doc" : {"name" : "混合444"} 
```

