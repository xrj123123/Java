## 一、ElasticSearch介绍

- `elasticsearch`是一个开源的分布式搜索引擎，可以用来实现搜索、日志统计、分析、系统监控等功能。提供Restful接口，可被任何语言调用

- `elastic stack（ELK）`是以`elasticsearch`为核心的技术栈，包括`beats`、`Logstash`、`kibana`、`elasticsearch`，负责存储、搜索、分析数据。

- `elasticsearch`底层是基于**`Lucene`**来实现的。**`Lucene`**是一个Java语言的搜索引擎类库





### 1、倒排索引

倒排索引是基于`MySQL`这样的正向索引而言的。例如在`MySql`中如果要查询`title`中包含手机的记录，那么就是模糊查询，索引失效，会扫描全表

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031218621.png" style="zoom:60%;" />





ES中使用的是**倒排索引**

- 文档（`Document`）：用来搜索的数据，其中的每一条数据就是一个文档。例如一个网页、一个商品信息
- 词条（`Term`）：对文档数据或用户搜索数据，利用某种算法分词，得到的具备含义的词语就是词条。例如：我是中国人，就可以分为：我、是、中国人、中国、国人这样的几个词条



**创建倒排索引**是对正向索引的一种特殊处理

- 将每一个文档的数据利用算法分词，得到一个个词条
- 创建表，每行数据包括词条、词条所在文档id、位置等信息
- 因为词条唯一性，可以给词条创建索引，例如hash表结构索引

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031221248.png" style="zoom:67%;" />



倒排索引的**搜索流程**如下：

1）用户输入条件`"华为手机"`进行搜索。

2）对用户输入内容**分词**，得到词条：`华为`、`手机`。

3）拿着词条在倒排索引中查找，可以得到包含词条的文档id：1、2、3。

4）拿着文档id到正向索引中查找具体文档。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031221414.png" style="zoom:50%;" />

先查询倒排索引，再查询正向索引，词条、文档id都建立了索引，无需全表扫描，查询速度快



**正向索引**：

- 优点：
  - 可以给多个字段创建索引
  - 根据索引字段搜索、排序速度非常快
- 缺点：
  - 根据非索引字段，或者索引字段中的部分词条查找时，只能全表扫描。

**倒排索引**：

- 优点：
  - 根据词条搜索、模糊搜索时，速度非常快
- 缺点：
  - 只能给词条创建索引，而不是字段
  - 无法根据字段做排序





### 2、ES概念

#### 2.1 文档和字段

`elasticsearch`是面向**文档（Document）**存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为json格式后存储在`elasticsearch`中。而Json文档中往往包含很多的**字段（Field）**，类似于数据库中的列。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031224300.png" style="zoom:50%;" />





#### 2.2 索引和映射

**索引（Index）**，就是相同类型的文档的集合。

- 所有用户文档，就可以组织在一起，称为用户的索引；
- 所有商品的文档，可以组织在一起，称为商品的索引；
- 所有订单的文档，可以组织在一起，称为订单的索引；

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031223529.png" style="zoom:67%;" />

因此，我们可以把索引当做是数据库中的表。

数据库的表会有约束信息，用来定义表的结构、字段的名称、类型等信息。因此，索引库中就有**映射（mapping）**，是索引中文档的字段约束信息，类似表的结构约束。





**mysql与elasticsearch**

| **MySQL** | **Elasticsearch** | **说明**                                                     |
| --------- | ----------------- | ------------------------------------------------------------ |
| Table     | Index             | 索引(index)，就是文档的集合，类似数据库的表(table)           |
| Row       | Document          | 文档（Document），就是一条条的数据，类似数据库中的行（Row），文档都是JSON格式 |
| Column    | Field             | 字段（Field），就是JSON文档中的字段，类似数据库中的列（Column） |
| Schema    | Mapping           | Mapping（映射）是索引中文档的约束，例如字段类型约束。类似数据库的表结构（Schema） |
| SQL       | DSL               | DSL是elasticsearch提供的JSON风格的请求语句，用来操作elasticsearch，实现CRUD |



- Mysql：擅长事务类型操作，可以确保数据的安全和一致性
- `Elasticsearch`：擅长海量数据的搜索、分析、计算



因此在企业中，往往是两者结合使用：

- 对安全性要求较高的写操作，使用mysql实现
- 对查询性能要求较高的搜索需求，使用elasticsearch实现
- 两者再基于某种方式，实现数据的同步，保证一致性





### 3、安装Elasticsearch

> 部署单点ES



**创建网络**

因为我们还需要部署`kibana`容器，需要让`es`和`kibana`容器互联。这里先创建一个网络

```sh
docker network create es-net
```



**加载镜像**

将es的镜像包导入后加载

```sh
docker load -i es.tar
```



**运行**

```sh
docker run -d \
	--name es \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
    -e "discovery.type=single-node" \
    -v es-data:/usr/share/elasticsearch/data \
    -v es-plugins:/usr/share/elasticsearch/plugins \
    --privileged \
    --network es-net \
    -p 9200:9200 \
    -p 9300:9300 \
elasticsearch:7.12.1
```

- `-e "cluster.name=es-docker-cluster"`：设置集群名称
- `-e "http.host=0.0.0.0"`：监听的地址，可以外网访问
- `-e "ES_JAVA_OPTS=-Xms512m -Xmx512m"`：内存大小
- `-e "discovery.type=single-node"`：非集群模式
- `-v es-data:/usr/share/elasticsearch/data`：挂载逻辑卷，绑定es的数据目录
- `-v es-logs:/usr/share/elasticsearch/logs`：挂载逻辑卷，绑定es的日志目录
- `-v es-plugins:/usr/share/elasticsearch/plugins`：挂载逻辑卷，绑定es的插件目录
- `--privileged`：授予逻辑卷访问权
- `--network es-net` ：加入一个名为es-net的网络中
- `-p 9200:9200`：端口映射配置



浏览器输入`http://192.168.198.130:9200`即可查看es响应结果





### 4、部署kibana

> `kibana`可以给我们提供一个`elasticsearch`的可视化界面，便于我们使用。



**加载镜像**

将`kibana`镜像包导入后，加载镜像

```
docker load -i kibana.tar
```



**运行**

```sh
docker run -d \
    --name kibana \
    -e ELASTICSEARCH_HOSTS=http://es:9200 \
    --network=es-net \
    -p 5601:5601  \
    kibana:7.12.1
```

- `--network es-net` ：加入一个名为es-net的网络中，与`elasticsearch`在同一个网络中
- `-e ELASTICSEARCH_HOSTS=http://es:9200"`：设置`elasticsearch`的地址，因为`kibana`已经与`elasticsearch`在一个网络，因此可以用容器名直接访问`elasticsearch`
- `-p 5601:5601`：端口映射配置



浏览器输入`http://192.168.198.130:5601`即可通过可视化界面操作es





### 5、安装IK分词器

我们在`kibnan`的可视化界面中打开`DevTools`，发送以下请求，分词器使用的是标准分词器`standard`，分词后的结果`java`是一个词，但是中文是一个字分成了一次词，使用`english`、`chinese`分词器也是这样，因此我们需要使用ik分词器

```json
GET /_analyze
{
  "analyzer": "standard",
  "text": "在B站学习java太棒了"
}
```





**离线安装ik插件**

安装插件需要知道`elasticsearch`的`plugins`目录位置，而我们用了数据卷挂载，因此需要查看`elasticsearch`的数据卷目录

```sh
docker volume inspect es-plugins
```

显示结果

```JSON
[
    {
    "CreatedAt": "2023-12-03T13:04:56+06:00",
    "Driver": "local",
    "Labels": null,
    "Mountpoint": "/var/lib/docker/volumes/es-plugins/_data",
    "Name": "es-plugins",
    "Options": null,
    "Scope": "local"
    }
]
```

因此我们将ik分词器文件放入`/var/lib/docker/volumes/es-plugins/_data`，然后重启容器即可



**测试**

IK分词器包含两种模式：

* `ik_smart`：最少切分【粗粒度】
* `ik_max_word`：最细切分【细粒度】



此时我们使用`ik_smart`模式分词器，就会对中文进行分词，`ik_smart`是最少切分，`ik_max_word`为最细切分，例如`太棒了`分为一个词后，还会将`太棒`和`了`继续划分

```JSON
GET /_analyze
{
  "analyzer": "ik_smart",
  "text": "在B站学习java太棒了"
}
```





### 6、拓展和停用词典

随着互联网的发展，出现了很多新的词语，在原有的词汇列表中并不存在。比如：“奥力给”，“传智教育” 等。因此使用ik分词器无法对其进行分词

所以我们的词汇也需要不断的更新，IK分词器提供了扩展词汇的功能。同时对于`了`，`的`等这些无用的词，我们不希望对其进行分词，也可以通过IK分词器进行设置



**1、修改配置文件**

打开IK分词器`config`目录，在`IKAnalyzer.cfg.xml`配置文件内容添加：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典 *** 添加扩展词典-->
        <entry key="ext_dict">ext.dic</entry>
    	<!--用户可以在这里配置自己的扩展停止词字典  *** 添加停用词词典-->
        <entry key="ext_stopwords">stopword.dic</entry>
</properties>
```



**2、在ext.dic添加扩展词**

```properties
传智教育
奥力给
白嫖
```



**3、在stopword.dic 添加停用词**

```properties
了
的
```



**3、重启es容器**

```sh
# 重启服务
docker restart elasticsearch
docker restart kibana

# 查看 日志
docker logs -f es
```



**4、测试**

我们修改了IK分词器的扩展词和停用词字典后，`传智教育`、`白嫖`、`奥利给`这些词就会被分词了，同时`了`、`的`等不会被分词

```json
GET /_analyze
{
  "analyzer": "ik_smart",
  "text": "在B站学习java太棒了,传智教育可以白嫖，奥利给"
}
```







## 二、索引库操作

索引库就类似数据库表，mapping映射就类似表的结构。

我们要向es中存储数据，必须先创建“库”和“表”。



### 1、mapping映射属性

mapping是对索引库中文档的约束，常见的mapping属性包括：

- type：字段数据类型，常见的简单类型有：
  - 字符串：text（可分词的文本）、keyword（精确值，例如：品牌、国家、ip地址）
  - 数值：long、integer、short、byte、double、float、
  - 布尔：boolean
  - 日期：date
  - 对象：object
- index：是否创建索引，默认为true
- analyzer：使用哪种分词器【配合text类型使用】
- properties：该字段的子字段

```json
{
    "age": 21,
    "weight": 52.1,
    "isMarried": false,
    "info": "西安Java讲师",
    "email": "zy@itcast.cn",
    "score": [99.1, 99.5, 98.9],
    "name": {
        "firstName": "云",
        "lastName": "赵"
    }
}
```

对应的每个字段映射（mapping）：

- age：类型为 integer；参与搜索，因此需要index为true；无需分词器
- weight：类型为float；参与搜索，因此需要index为true；无需分词器
- isMarried：类型为boolean；参与搜索，因此需要index为true；无需分词器
- info：类型为字符串，需要分词，因此是text；参与搜索，因此需要index为true；分词器可以用ik_smart
- email：类型为字符串，但是不需要分词，因此是keyword；不参与搜索，因此需要index为false；无需分词器
- score：虽然是数组，但是我们只看元素的类型，类型为float；参与搜索，因此需要index为true；无需分词器
- name：类型为object，需要定义多个子属性
  - name.firstName；类型为字符串，但是不需要分词，因此是keyword；参与搜索，因此需要index为true；无需分词器
  - name.lastName；类型为字符串，但是不需要分词，因此是keyword；参与搜索，因此需要index为true；无需分词器







### 2、创建索引库和映射

**基本语法：**

- 请求方式：PUT
- 请求路径：/索引库名，可以自定义
- 请求参数：mapping映射

```json
PUT /索引库名称
{
  "mappings": {
    "properties": {
      "字段名":{
        "type": "text",
        "analyzer": "ik_smart"
      },
      "字段名2":{
        "type": "keyword",
        "index": "false"
      },
      "字段名3":{
        "properties": {
          "子字段": {
            "type": "keyword"
          }
        }
      },
      // ...略
    }
  }
}

PUT /test01 
{
  "mappings": {
    "properties": {
      "info": {
        "type": "text",
        "analyzer": "ik_smart"
      },
      "email": {
        "type": "keyword",
        "index": false
      },
      "name": {
        "type": "object",
        "properties": {
          "firstName": {
            "type": "keyword"
          },
          "lastName": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
```







### 3、查询索引库

**基本语法：**

- 请求方式：GET

- 请求路径：/索引库名

- 请求参数：无

```json
# 查询
GET /test01
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031652172.png" style="zoom:%;" />







### 4、修改索引库

倒排索引结构虽然不复杂，但是一旦数据结构改变（比如改变了分词器），就需要重新创建倒排索引。因此索引库**一旦创建，无法修改mapping**。

虽然无法修改mapping中已有的字段，但是却允许添加新的字段到mapping中，因为不会对倒排索引产生影响。



**语法说明**

```json
PUT /索引库名/_mapping
{
  "properties": {
    "新字段名":{
      "type": "integer"
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031653855.png)







### 5、删除索引库

**语法：**

- 请求方式：DELETE
- 请求路径：/索引库名
- 请求参数：无

```
DELETE /test01
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031654452.png)







## 三、文档操作

### 1、新增文档

语法：

```json
POST /索引库名/_doc/文档id
{
    "字段1": "值1",
    "字段2": "值2",
    "字段3": {
        "子属性1": "值3",
        "子属2": "值4"
    },
    // ...
}

# 插入文档
POST /test01/_doc/1
{
  "info": "xrj在西安上学",
  "emain": "12345@qq.com",
  "name": {
    "firstName": "徐",
    "lastName": "睿杰"
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031710404.png)





### 2、查询文档

**语法：**

```
GET /{索引库名称}/_doc/{id}
```

```sh
GET /test/_doc/1
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031711395.png)





### 3、删除文档

**语法：**

```
DELETE /{索引库名}/_doc/id值
```

```
# 删除文档
DELETE /test01/_doc/1
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031712627.png)







### 4、修改文档

修改有两种方式：

- 全量修改：直接覆盖原来的文档
- 增量修改：修改文档中的部分字段



#### **全量修改**

全量修改是覆盖原来的文档，其本质是：

- 根据指定的id删除文档
- 新增一个相同id的文档

**注意**：如果根据id删除时，id不存在，第二步的新增也会执行，也就从修改变成了新增操作了。



**语法**

```json
PUT /{索引库名}/_doc/文档id
{
    "字段1": "值1",
    "字段2": "值2",
    // ... 略
}
```

```json
# 全量修改文档
PUT /test01/_doc/2
{
  "info": "xrj在西安上学",
  "emain": "xrj123@qq.com",
  "name": {
    "firstName": "徐",
    "lastName": "睿杰"
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031714540.png)







#### 增量修改

增量修改是只修改指定id匹配的文档中的部分字段。

**语法**

```json
POST /{索引库名}/_update/文档id
{
    "doc": {
         "字段名": "新的值",
    }
}
```

```json
POST /test01/_update/1
{
  "doc": {
    "email": "1503855522@163.com"
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031717602.png)







## 四、RestAPI

ES官方提供了各种不同语言的客户端，用来操作ES。这些客户端的本质就是组装DSL语句，通过http请求发送给ES。官方文档地址：`https://www.elastic.co/guide/en/elasticsearch/client/index.html`



### 1、导入项目

导入项目并导入数据库对应的数据

```sql
CREATE TABLE `tb_hotel` (
  `id` bigint(20) NOT NULL COMMENT '酒店id',
  `name` varchar(255) NOT NULL COMMENT '酒店名称；例：7天酒店',
  `address` varchar(255) NOT NULL COMMENT '酒店地址；例：航头路',
  `price` int(10) NOT NULL COMMENT '酒店价格；例：329',
  `score` int(2) NOT NULL COMMENT '酒店评分；例：45，就是4.5分',
  `brand` varchar(32) NOT NULL COMMENT '酒店品牌；例：如家',
  `city` varchar(32) NOT NULL COMMENT '所在城市；例：上海',
  `star_name` varchar(16) DEFAULT NULL COMMENT '酒店星级，从低到高分别是：1星到5星，1钻到5钻',
  `business` varchar(255) DEFAULT NULL COMMENT '商圈；例：虹桥',
  `latitude` varchar(32) NOT NULL COMMENT '纬度；例：31.2497',
  `longitude` varchar(32) NOT NULL COMMENT '经度；例：120.3925',
  `pic` varchar(255) DEFAULT NULL COMMENT '酒店图片；例:/img/1.jpg',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```





### 2、mapping映射分析

创建索引库，最关键的是mapping映射，而mapping映射要考虑的信息包括：

- 字段名
- 字段数据类型
- 是否参与搜索
- 是否需要分词
- 如果分词，分词器是什么？

其中：

- 字段名、字段数据类型，可以参考数据表结构的名称和类型
- 是否参与搜索要分析业务来判断，例如图片地址，就无需参与搜索
- 是否分词要看内容，内容如果是一个整体就无需分词，反之则要分词
- 分词器，我们可以统一使用ik_max_word



**hotel索引库结构**

```json
PUT /hotel
{
  "mappings": {
    "properties": {
      "id": {
        "type": "keyword"
      },
      "name": {
        "type": "text",
        "analyzer": "ik_max_word",
        "copy_to": "all"
      },
      "address": {
        "type": "keyword",
        "index": false
      },
      "price": {
        "type": "integer"
      },
      "score": {
        "type": "integer"
      },
      "brand": {
        "type": "keyword",
        "copy_to": "all"
      },
      "city": {
        "type": "keyword",
        "copy_to": "all"
      },
      "star_name": {
        "type": "keyword"
      },
      "business": {
        "type": "keyword"
      },
      "location": {
        "type": "geo_point"
      },
      "pic": {
        "type": "keyword",
        "index": false
      },
      "all": {
        "type": "text",
        "analyzer": "ik_max_word"
      }
    }
  }
}
```



**特殊字段：**

- location：地理坐标，里面包含经度、纬度

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031751371.png)

- all：一个组合字段，其目的是将多字段的值 利用copy_to合并，提供给用户搜索【类似联合索引】

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312031751520.png)





### 3、初始化RestClient

**1、导入依赖**

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
</dependency>
```

因为我们使用的es版本是7.12.1，而`SpringBoot`默认管理的是高版本的，所以我们需要手动覆盖默认的ES版本

```xml
<properties>
    <java.version>1.8</java.version>
    <elasticsearch.version>7.12.1</elasticsearch.version>
</properties>
```



**2、初始化`RestHighLevelClient`**

```java
public class HotelIndexTest {
    private RestHighLevelClient restHighLevelClient;

    @BeforeEach
    public void setup(){
        this.restHighLevelClient = new RestHighLevelClient(RestClient.builder(
            HttpHost.create("http://192.168.198.130:9200")
        ));
    }

    @AfterEach
    public void destroy() throws IOException {
        this.restHighLevelClient.close();
    }
}
```





### 4、索引库的CRUD

#### 4.1 创建索引库

```java
@Test
public void createHotelIndex() throws IOException {
    // 1.创建request对象, hotel就是DSL语句中创建索引库的名称
    CreateIndexRequest request = new CreateIndexRequest("hotel");
    // 2.准备请求的参数：HOTEL_MAPPING就是准备好的创建索引库的DSL语句
    request.source(HOTEL_MAPPING, XContentType.JSON);
    // 3.发送请求, indices()方法中包含索引库操作的所有方法
    restHighLevelClient.indices().create(request, RequestOptions.DEFAULT);
}
```

```java
public class ESConstants {
    public static final String HOTEL_MAPPING = "{\n" +
            "  \"mappings\": {\n" +
            "    \"properties\": {\n" +
            "      \"id\": {\n" +
            "        \"type\": \"keyword\"\n" +
            "      },\n" +
            "      \"name\": {\n" +
            "        \"type\": \"text\",\n" +
            "        \"analyzer\": \"ik_max_word\",\n" +
            "        \"copy_to\": \"all\"\n" +
            "      },\n" +
            "      \"address\": {\n" +
            "        \"type\": \"keyword\",\n" +
            "        \"index\": false\n" +
            "      },\n" +
            "      \"price\": {\n" +
            "        \"type\": \"integer\"\n" +
            "      },\n" +
            "      \"score\": {\n" +
            "        \"type\": \"integer\"\n" +
            "      },\n" +
            "      \"brand\": {\n" +
            "        \"type\": \"keyword\",\n" +
            "        \"copy_to\": \"all\"\n" +
            "      },\n" +
            "      \"city\": {\n" +
            "        \"type\": \"keyword\",\n" +
            "        \"copy_to\": \"all\"\n" +
            "      },\n" +
            "      \"star_name\": {\n" +
            "        \"type\": \"keyword\"\n" +
            "      },\n" +
            "      \"business\": {\n" +
            "        \"type\": \"keyword\"\n" +
            "      },\n" +
            "      \"location\": {\n" +
            "        \"type\": \"geo_point\"\n" +
            "      },\n" +
            "      \"pic\": {\n" +
            "        \"type\": \"keyword\",\n" +
            "        \"index\": false\n" +
            "      },\n" +
            "      \"all\": {\n" +
            "        \"type\": \"text\",\n" +
            "        \"analyzer\": \"ik_max_word\"\n" +
            "      }\n" +
            "    }\n" +
            "  }\n" +
            "}";
}
```



#### 4.2 查询索引库

```java
@Test
public void existsHotelIndex() throws IOException {
    // 1.创建request对象
    GetIndexRequest request = new GetIndexRequest("hotel");
    // 2.发送请求
    boolean exists = restHighLevelClient.indices().exists(request, RequestOptions.DEFAULT);
    System.out.println(exists ? "索引库存在" : "索引库不存在");
}
```



#### 4.3 删除索引库

```java
@Test
public void deleteHotelIndex() throws IOException {
    // 1.创建request对象
    DeleteIndexRequest request = new DeleteIndexRequest("hotel");
    // 2.发送请求
    restHighLevelClient.indices().delete(request, RequestOptions.DEFAULT);
}
```





### 5、文档的CRUD

#### 5.1 新增文档

> 我们要将数据库的酒店数据查询出来，写入elasticsearch中。

因为数据库中查询出来的对象其类型和我们在es中设置的类型不同，所以通过数据库查询出对象后，需要将其转换为我们在es中设置的对象类型



**DSL语句**

```json
POST /{索引库名}/_doc/1
{
    "name": "Jack",
    "age": 21
}
```

**Java**

```java
@Test
public void insert() throws IOException {
    // 1.去数据库查数据
    Hotel hotel = hotelService.getById(36934);
    // 2.将hotel转为对应文档类型
    HotelDoc hotelDoc = new HotelDoc(hotel);
    // 3.创建request请求
    IndexRequest request = new IndexRequest("hotel").id(hotelDoc.getId().toString());
    // 4.准备json文档
    request.source(JSON.toJSONString(hotelDoc), XContentType.JSON);
    // 5.发送请求
    client.index(request, RequestOptions.DEFAULT);
}
```





#### 5.2 查询文档

**DSL语句**

```json
GET /hotel/_doc/{id}
```

**Java**

通过`client.get`拿到查询结果后，会得到很多额外信息，而我们需要的信息是在`_source`下，所以通过`response.getSourceAsString()`获得我们所需要的信息

```java
@Test
public void select() throws IOException {
    GetRequest request = new GetRequest("hotel", "36934");
    GetResponse response = client.get(request, RequestOptions.DEFAULT);
    String source = response.getSourceAsString();
    HotelDoc hotelDoc = JSON.parseObject(source, HotelDoc.class);
    System.out.println(hotelDoc);
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041129879.png)



#### 5.3 修改文档

- 全量修改：本质是先根据id删除，再新增
  - 在`RestClient`的API中，全量修改与新增的API完全一致，判断依据是ID：
    - 如果新增时，ID已经存在，则修改
    - 如果新增时，ID不存在，则新增
- 增量修改：修改文档中的指定字段值

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041136441.png)



**Java**

```java
@Test
public void update() throws IOException {
    // 1.准备request请求
    UpdateRequest request = new UpdateRequest("hotel", "36934");
    // 2.准备请求参数
    request.doc(
            "price", "999",
            "score", "100"
    );
    // 3.发送请求
    client.update(request, RequestOptions.DEFAULT);
}
```





#### 5.4 删除文档

**DSL**

```json
DELETE /hotel/_doc/{id}
```

**Java**

```java
@Test
public void delete() throws IOException {
    // 1.准备request请求
    DeleteRequest request = new DeleteRequest("hotel", "36934");
    // 2.发送请求
    client.delete(request, RequestOptions.DEFAULT);
}
```





#### 5.5 批量操作

批量处理`BulkRequest`，其本质就是将多个普通的CRUD请求组合在一起发送。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041153029.png)

```java
@Test
public void testBulk() throws IOException {
    // 去数据库批量查询数据
    List<Hotel> hotels = hotelService.list();

    // 1.创建request
    BulkRequest request = new BulkRequest();
    // 2.批量添加数据
    for (Hotel hotel : hotels) {
        HotelDoc hotelDoc = new HotelDoc(hotel);
        request.add(new IndexRequest("hotel")
                    .id(hotelDoc.getId().toString())
                    .source(JSON.toJSONString(hotelDoc), XContentType.JSON));
    }
    // 3.发送请求
    client.bulk(request, RequestOptions.DEFAULT);
}
```







## 五、DSL查询文档

### 1、DSL查询分类

`Elasticsearch`提供了基于`JSON`的`DSL`（[Domain Specific Language](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)）来定义查询。常见的查询类型包括：

- **查询所有**：查询出所有数据，一般测试用。例如：`match_all`

- **全文检索（full text）查询**：利用分词器对用户输入内容分词，然后去倒排索引库中匹配。例如：
  - `match_query`
  - `multi_match_query`
- **精确查询**：根据精确词条值查找数据，一般是查找keyword、数值、日期、boolean等类型字段。例如：
  - `ids`
  - `range`
  - `term`
- **地理（geo）查询**：根据经纬度查询。例如：
  - `geo_distance`
  - `geo_bounding_box`
- **复合（compound）查询**：复合查询可以将上述各种查询条件组合起来，合并查询条件。例如：
  - `bool`
  - `function_score`



**查询语法**

```json
GET /indexName/_search
{
  "query": {
    "查询类型": {
      "查询条件": "条件值"
    }
  }
}
```



```json
// 查询所有
GET /indexName/_search
{
  "query": {
    "match_all": {
    }
  }
}
```

- 查询类型为match_all
- 没有查询条件







### 2、全文检索查询

全文检索查询的基本流程如下：

- 对用户搜索的内容做分词，得到词条
- 根据词条去倒排索引库中匹配，得到文档id
- 根据文档id找到文档，返回给用户

比较常用的场景包括：

- 商城的输入框搜索
- 百度输入框搜索

因为是拿着词条去匹配，因此参与搜索的字段也必须是可分词的text类型的字段。



**基本语法**

常见的全文检索查询包括：

- match查询：单字段查询

  ```json
  GET /indexName/_search
  {
    "query": {
      "match": {
        "FIELD": "TEXT"
      }
    }
  }
  ```

- multi_match查询：多字段查询，任意一个字段符合条件就算符合查询条件

  ```json
  GET /indexName/_search
  {
    "query": {
      "multi_match": {
        "query": "TEXT",
        "fields": ["FIELD1", " FIELD12"]
      }
    }
  }
  ```





**match**

这里`all`字段是我们创建索引库时指定的，它是`name`、`brand`、`city`的组合

```json
# match查询
GET /hotel/_search 
{
  "query": {
    "match": {
      "all": "如家外滩"
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041419659.png)



**multi_match**

这里我们分别查询`name`、`brand`、`city`，和查询`all`结果是一致的，但是，搜索字段越多，对查询性能影响越大，因此建议采用`copy_to`，然后单字段查询的方式。

```json
# multi_match
GET /hotel/_search 
{
  "query": {
    "multi_match": {
      "query": "如家外滩",
      "fields": ["name", "brand", "city"]
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041420811.png)







### 3、精确查询

精确查询一般是查找keyword、数值、日期、boolean等类型字段。所以**不会**对搜索条件分词。常见的有：

- term：根据词条精确值查询

  ```json
  // term查询
  GET /indexName/_search
  {
    "query": {
      "term": {
        "FIELD": {
          "value": "VALUE"
        }
      }
    }
  }
  ```

- range：根据值的范围查询

  ```json
  // range查询
  GET /indexName/_search
  {
    "query": {
      "range": {
        "FIELD": {
          "gte": 10, // 这里的gte代表大于等于，gt则代表大于
          "lte": 20 // lte代表小于等于，lt则代表小于
        }
      }
    }
  }
  ```

  



**term查询**

```json
GET /hotel/_search
{
  "query": {
    "term": {
      "city": {
        "value": "北京"
      }
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041430646.png)





**range查询**

- `gte`表示大于等于
- `lte`表示小于等于
- `gt`表示大于
- `lt`表示小于

```json
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 100,
        "lte": 200
      }
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041431511.png)







### 4、地理坐标查询

地理坐标查询，其实就是根据经纬度查询，官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-queries.html

常见的使用场景包括：

- 携程：搜索我附近的酒店
- 滴滴：搜索我附近的出租车
- 微信：搜索我附近的人



#### 4.1 矩形范围查询

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041440515.gif)

查询时，需要指定矩形的**左上**、**右下**两个点的坐标，然后画出一个矩形，落在该矩形内的都是符合条件的点。

```json
# 矩形范围查询
GET /hotel/_search 
{
  "query": {
    "geo_bounding_box": {
      "location": {
        "top_left": {
          "lat": 31.1,
          "lon": 121.5
        },
        "bottom_right": {
          "lat": 30.9,
          "lon": 121.7
        }
      }
    }
  }
}
```





#### 4.2 附近查询

查询到指定中心点小于某个距离值的所有文档。地图上找一个点作为圆心，以指定距离为半径，画一个圆，落在圆内的坐标都算符合条件

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041441154.gif)

```json
GET /hotel/_search 
{
  "query": {
    "geo_distance": {
      "distance": "2km",  // 半径
      "location": "31.21, 121.5" // 圆心
    }
  }
}
```







### 5、复合查询

复合（compound）查询：复合查询可以将其它简单查询组合起来，实现更复杂的搜索逻辑。常见的有两种：

- `fuction score`：算分函数查询，可以控制文档相关性算分，控制文档排名
- `bool query`：布尔查询，利用逻辑关系组合多个其它的查询，实现复杂搜索



#### 5.1 相关性算分

当我们利用match查询时，文档结果会根据与搜索词条的关联度打分（_score），返回结果时按照分值降序排列。

例如，我们搜索 "虹桥如家"，结果如下：

```json
[
  {
    "_score" : 17.850193,
    "_source" : {
      "name" : "虹桥如家酒店真不错",
    }
  },
  {
    "_score" : 12.259849,
    "_source" : {
      "name" : "外滩如家酒店真不错",
    }
  },
  {
    "_score" : 11.91091,
    "_source" : {
      "name" : "迪士尼如家酒店真不错",
    }
  }
]
```



在`elasticsearch`中，早期使用的打分算法是`TF-IDF`算法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041506606.png)

在后来的5.1版本升级中，`elasticsearch`将算法改进为**BM25**算法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041506840.png)





#### 5.2 算分函数查询

利用`elasticsearch`中的`function score` 可以控制相关性算分



**语法**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041518548.png)





`function score` 查询中包含四部分内容：

- **原始查询**条件：query部分，基于这个条件搜索文档，并且基于BM25算法给文档打分，**原始算分**（query score)
- **过滤条件**：filter部分，符合该条件的文档才会重新算分
- **算分函数**：符合filter条件的文档要根据这个函数做运算，得到的**函数算分**（function score），有四种函数
  - weight：函数结果是常量
  - field_value_factor：以文档中的某个字段值作为函数结果
  - random_score：以随机数作为函数结果
  - script_score：自定义算分函数算法
- **运算模式**：算分函数的结果、原始查询的相关性算分，两者之间的运算方式，包括：
  - multiply：相乘
  - replace：用function score替换query score
  - 其它，例如：sum、avg、max、min



> function score的运行流程如下：
>
> - 根据**原始条件**查询搜索文档，并且计算相关性算分，称为**原始算分**（query score）
> - 根据**过滤条件**，过滤文档【决定那些文档的算分被修改】
> - 符合**过滤条件**的文档，基于**算分函数**运算，得到**函数算分**（function score）
> - 将**原始算分**（query score）和**函数算分**（function score）基于**运算模式**做运算，得到最终结果，作为相关性算分。
>





**需求：**给“如家”这个品牌的酒店排名靠前一些

- 原始条件：可以任意变化
- 过滤条件：`brand = "如家"`
- 算分函数：可以直接给固定的算分结果，`weight`
- 运算模式：比如求和

```json
GET /hotel/_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "all": "外滩"
        }
      },
      "functions": [
        {
          "filter": {
            "term": {
              "brand": "如家"
            }
          },
          "weight": 10
        }
      ],
      "boost_mode": "sum"
    }
  }
}
```





#### 5.3 布尔查询

布尔查询是一个或多个查询子句的组合，每一个子句就是一个**子查询**。子查询的组合方式有：

- `must`：必须匹配每个子查询，类似“与”
- `should`：选择性匹配子查询，类似“或”
- `must_not`：必须不匹配，**不参与算分**，类似“非”
- `filter`：必须匹配，**不参与算分**



比如在搜索酒店时，除了关键字搜索外，我们还可能根据品牌、价格、城市等字段做过滤。每一个不同的字段，其查询的条件、方式都不一样，必须是多个不同的查询，而要组合这些查询，就必须用bool查询了。

需要注意的是，搜索时，参与**打分的字段越多，查询的性能也越差**。因此这种多条件查询时

- 搜索框的关键字搜索，是全文检索查询，使用must查询，参与算分
- 其它过滤条件，采用filter查询。不参与算分



**基本语法**

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {"city": "上海" }}
      ],
      "should": [
        {"term": {"brand": "皇冠假日" }},
        {"term": {"brand": "华美达" }}
      ],
      "must_not": [
        { "range": { "price": { "lte": 500 } }}
      ],
      "filter": [
        { "range": {"score": { "gte": 45 } }}
      ]
    }
  }
}
```





**需求**：搜索名字包含“如家”，价格不高于400，在坐标31.21,121.5周围10km范围内的酒店。

分析：

- 名称搜索，属于全文检索查询，应该参与算分。放到must中
- 价格不高于400，用`range`查询，属于过滤条件，不参与算分。放到must_not中
- 周围10km范围内，用`geo_distance`查询，属于过滤条件，不参与算分。放到filter中

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "name": "如家"
        }}
      ],
      "must_not": [
        {"range": {
          "price": {
            "gte": 400
          }
        }}
      ],
      "filter": [
        {"geo_distance": {
          "distance": "10km",
          "location": {
            "lat": 31.21,
            "lon": 121.5
          }
        }}
      ]
    }
  }
}
```







### 6、查询结果处理

#### 6.1 排序

`elasticsearch`默认是根据相关度算分（_score）来排序，但是也支持自定义方式对搜索[结果排序](https://www.elastic.co/guide/en/elasticsearch/reference/current/sort-search-results.html)。可以排序字段类型有：keyword类型、数值类型、地理坐标类型、日期类型等。排序后，es就不会再去计算得分。



**普通字段排序**

排序条件是一个数组，可以写多个排序条件。按照声明的顺序，当第一个条件相等时，再按照第二个条件排序，以此类推

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "FIELD": "desc"  // 排序字段、排序方式ASC、DESC
    }
  ]
}
```



根据酒店得分降序排序，得分相同根据价格升序排序

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "score":  "desc"
    },
    {
      "price": "asc"
    }
  ]
}
```





**地理坐标排序**

- 指定一个坐标，作为目标点
- 计算每一个文档中，指定字段（必须是geo_point类型）的坐标 到目标点的距离是多少
- 根据距离排序

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance" : {
          "FIELD" : "纬度，经度", // 文档中geo_point类型的字段名、目标坐标点
          "order" : "asc", // 排序方式
          "unit" : "km" // 排序的距离单位
      }
    }
  ]
}
```



实现对酒店数据按照到你的位置坐标的距离升序排序，假设位置是：31.034661，121.612282，寻找我周围距离最近的酒店。

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance": {
        "location": {
          "lat": 31.034661,
          "lon": 121.612282
        },
        "order": "asc",
        "unit": "km"
      }
    }
  ]
}
```





#### 6.2 分页

`elasticsearch` 默认情况下只返回`top10`的数据。而如果要查询更多数据就需要修改分页参数了。`elasticsearch`中通过修改`from`、`size`参数来控制要返回的分页结果：

- from：从第几个文档开始
- size：总共查询几个文档

类似于`mysql`中的`limit ?, ?`



**基本语法**

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0, // 分页开始的位置，默认为0
  "size": 10, // 期望获取的文档总数
  "sort": [
    {"price": "asc"}
  ]
}
```



**深度分页问题**

如果要查询990~1000的数据，查询逻辑要这么写。这里是查询990开始的数据，也就是 第990~第1000条 数据。不过，`elasticsearch`内部分页时，必须先查询 0~1000条，然后截取其中的990 ~ 1000的这10条

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "from": 990, // 分页开始的位置，默认为0
  "size": 10, // 期望获取的文档总数
  "sort": [
    {"price": "asc"}
  ]
}
```



> 查询`TOP1000`，如果es是单点模式，这并无太大影响。
>
> 但是`elasticsearch`将来一定是集群，例如集群有5个节点，查询`TOP1000`的数据，并不是每个节点查询200条就可以了。因为节点A的`TOP200`，在另一个节点可能排到10000名以外了。
>
> 因此要想获取整个集群的`TOP1000`，必须先查询出每个节点的`TOP1000`，汇总结果后，重新排名，重新截取`TOP1000`。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041626527.png" style="zoom:80%;" />

当查询分页深度较大时，汇总数据过多，对内存和CPU会产生非常大的压力，因此`elasticsearch`会禁止from+ size 超过10000的请求。

针对深度分页，ES提供了两种解决方案，[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html)：

- search after：分页时需要排序，原理是从上一次的排序值开始，查询下一页数据。官方推荐使用的方式。
- scroll：原理将排序后的文档id形成快照，保存在内存。官方已经不推荐使用。



**总结**

- `from + size`：
  - 优点：支持随机翻页
  - 缺点：深度分页问题，默认查询上限（from + size）是10000
  - 场景：百度、京东、谷歌、淘宝这样的随机翻页搜索
- `after search`：
  - 优点：没有查询上限（单次查询的size不超过10000）
  - 缺点：只能向后逐页查询，不支持随机翻页
  - 场景：没有随机翻页需求的搜索，例如手机向下滚动翻页

- `scroll`：
  - 优点：没有查询上限（单次查询的size不超过10000）
  - 缺点：会有额外内存消耗，并且搜索结果是非实时的
  - 场景：海量数据的获取和迁移。从ES7.1开始不推荐，建议用 after search方案。





#### 6.3 高亮

我们在百度，京东搜索时，关键字会变成红色，比较醒目，这就是高亮显示

高亮显示的实现分为两步：

- 1）给文档中的所有关键字都添加一个标签，例如`<em>`标签
- 2）页面给`<em>`标签编写css样式



**基本语法**

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "FIELD": "TEXT" // 查询条件，高亮一定要使用全文检索查询
    }
  },
  "highlight": {
    "fields": { // 指定要高亮的字段
      "FIELD": {
        "pre_tags": "<em>",  // 用来标记高亮字段的前置标签
        "post_tags": "</em>" // 用来标记高亮字段的后置标签
      }
    }
  }
}
```

**注意：**

- 高亮是对关键字高亮，因此**搜索条件必须带有关键字**，而不能是范围这样的查询。
- 默认情况下，**高亮的字段，必须与搜索指定的字段一致**，否则无法高亮
- 如果要对非搜索字段高亮，则需要添加一个属性：`required_field_match=false`
- es中默认高亮就是使用的`<em>`标签修饰，因此可以不用写



```json
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家"
    }
  },
  "highlight": {
    "fields": {
      "name": {  
        "require_field_match": "false"
      }
    }
  }
}
```





## 六、RestClient查询文档

文档的查询同样使用`RestHighLevelClient`对象，基本步骤包括：

- 准备Request对象
- 准备请求参数
- 发起请求
- 解析响应





### 1、matchAll

**发送请求**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041717583.png)

- 创建`SearchRequest`对象，指定索引库名

- 利用`request.source()`构建`DSL`，`DSL`中可以包含查询、分页、排序、高亮等
  - `query()`：代表查询条件，利用`QueryBuilders.matchAllQuery()`构建一个`match_all`查询的`DSL`
- 利用`client.search()`发送请求，得到响应





**解析响应**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312041726002.png)

`elasticsearch`返回的结果是一个`JSON`字符串，结构包含：

- `hits`：命中的结果
  - `total`：总条数，其中的value是具体的总条数值
  - `max_score`：所有结果中得分最高的文档的相关性算分
  - `hits`：搜索结果的文档数组，其中的每个文档都是一个json对象
    - `_source`：文档中的原始数据，也是json对象

因此，我们解析响应结果，就是逐层解析`JSON`字符串

- `SearchHits`：通过`response.getHits()`获取，就是`JSON`中的最外层的hits，代表命中的结果
  - `SearchHits.getTotalHits().value`：获取总条数信息
  - `SearchHits.getHits()`：获取`SearchHit`数组，也就是文档数组
    - `hit.getSourceAsString()`：获取文档结果中的_source，也就是原始的json文档数据



```java
@Test
public void testMatchAll() throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().query(QueryBuilders.matchAllQuery());
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);

    // 4.解析数据
    SearchHits hits = response.getHits();
    // 4.1获得总条数
    long total = hits.getTotalHits().value;
    // 4.2文档数据
    SearchHit[] searchHits = hits.getHits();
    // 4.3遍历
    for (SearchHit hit : searchHits) {
        // 获得文档resource
        String json = hit.getSourceAsString();
        // 反序列化
        HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);
        System.out.println(hotelDoc);
    }
}
```





### 2、match查询

全文检索的`match`和`multi_match`查询与`match_all`的`API`基本一致。差别是查询条件，也就是query的部分。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312042327294.png)



因此，Java代码上的差异主要是`request.source().query()`中的参数。同样是利用`QueryBuilders`提供的方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312042327325.png)

```java
@Test
public void testMatch() throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().query(QueryBuilders.matchQuery("all", "如家"));
    // request.source().query(QueryBuilders.multiMatchQuery("如家", "name", "business"));
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析数据
    handleResponse(response);
}
```





### 3、精确查询

精确查询主要是两者：

- term：词条精确匹配
- range：范围查询

与之前的查询相比，差异同样在查询条件，其它都一样。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312042334750.png)





### 4、布尔查询

布尔查询是用must、must_not、filter等方式组合其它查询

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051040203.png)

```java
@Test
public void testBool() throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");

    // 2.准备DSL
    // 2.1准备BoolQuery
    BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    // 2.2添加must
    boolQuery.must(QueryBuilders.termQuery("city", "北京"));
    // 2.3添加filter
    boolQuery.filter(QueryBuilders.rangeQuery("price").lte(500));
    request.source().query(boolQuery);

    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);

    // 4.解析数据
    handleResponse(response);
}
```





### 5、排序、分页

搜索结果的排序和分页是与`query`同级的参数，因此同样是使用`request.source()`来设置。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051110251.png)

```java
@Test
public void testPageAndSort() throws IOException {
    int page = 2, size = 5;
    // 1.准备1request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().query(QueryBuilders.matchQuery("name", "如家"));
    // 3.排序
    request.source().sort("price", SortOrder.ASC);
    // 4.分页
    request.source().from((page - 1) * size).size(size);
    // 5.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 6.解析数据
    handleResponse(response);
}
```







### 6、高亮

- 查询的DSL：其中除了查询条件，还需要添加高亮条件，同样是与query同级。
- 结果解析：结果除了要解析`_source`文档数据，还要解析`高亮结果`

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051114510.png" style="zoom:60%;" />

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051111516.png" style="zoom:60%;" />

- 从结果中获取`source`。`hit.getSourceAsString()`，这部分是非高亮结果，`json`字符串。还需要反序列为`HotelDoc`对象
- 获取高亮结果。`hit.getHighlightFields()`，返回值是一个`Map`，`key`是高亮字段名称，值是`HighlightField`对象，代表高亮值
- 从map中根据高亮字段名称，获取高亮字段值对象`HighlightField`
- 从`HighlightField`中获取`Fragments`，并且转为字符串。这部分就是真正的高亮字符串了
- 用高亮的结果替换`HotelDoc`中的非高亮结果



```java
@Test
public void testHighlight() throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().query(QueryBuilders.matchQuery("all", "如家"));
    // 3.设置高亮
    request.source().highlighter(new HighlightBuilder().field("name").requireFieldMatch(false));
    // 4.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 5.解析数据
    handleResponse(response);
}

private void handleResponse(SearchResponse response) {
    // 4.解析数据
    SearchHits hits = response.getHits();
    // 4.1获得总条数
    long total = hits.getTotalHits().value;
    // 4.2文档数据
    SearchHit[] searchHits = hits.getHits();
    // 4.3遍历
    for (SearchHit hit : searchHits) {
        // 获得文档resource
        String json = hit.getSourceAsString();
        // 反序列化
        HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);

        // 获得高亮结果
        Map<String, HighlightField> highlightFields = hit.getHighlightFields();
        if (!CollectionUtils.isEmpty(highlightFields)) {
            // 根据字段名获取高亮结果
            HighlightField highlight = highlightFields.get("name");
            if (highlight != null) {
                // 获取高亮值
                String value = highlight.fragments()[0].string();
                // 覆盖非高亮结果
                hotelDoc.setName(value);
            }
        }
        System.out.println(hotelDoc);
    }
}
```







## 七、黑马旅游

### 1、搜索和分页

前端点击搜索会向`/hotel/list`发送请求，请求参数如下：

- `key`：搜索关键字
- `page`：页码
- `size`：每页大小
- `sortBy`：排序

返回值：分页查询，需要返回分页结果`PageResult`，包含两个属性：

- `total`：总条数
- `List<HotelDoc>`：当前页的数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051344869.png)



**定义实体类**

其中`RequestParams`接收的是前端的请求参数，`PageResult`是返回结果

```java
@Data
public class RequestParams {
    private String key;
    private Integer page;
    private Integer size;
    private String sortBy;
}
```

```java
@Data
public class PageResult {
    private Long total;
    private List<HotelDoc> hotels;

    public PageResult() {
    }

    public PageResult(Long total, List<HotelDoc> hotels) {
        this.total = total;
        this.hotels = hotels;
    }
}
```



**controller**

```java
@RestController
@RequestMapping("/hotel")
public class HotelController {
    @Autowired
    private IHotelService hotelService;

    @RequestMapping("/list")
    public PageResult search(@RequestBody RequestParams params) throws IOException {
        return hotelService.search(params);
    }

}
```



**service**

```java
@Service
public class HotelService extends ServiceImpl<HotelMapper, Hotel> implements IHotelService {
    @Autowired
    private RestHighLevelClient client;

    @Override
    public PageResult search(RequestParams params) throws IOException {
        // 1.准备request
        SearchRequest request = new SearchRequest("hotel");
        // 2.准备DSL语句
        String key = params.getKey();
        if (key == null || "".equals(key)) {
            request.source().query(QueryBuilders.matchAllQuery());
        } else {
            request.source().query(QueryBuilders.matchQuery("all", key));
        }
        // 3.分页
        request.source().from((params.getPage() - 1) * params.getSize()).size(params.getSize());
        // 4.发送请求
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        return handleResponse(response);
    }

    private PageResult handleResponse(SearchResponse response) {
        // 4.解析数据
        SearchHits hits = response.getHits();
        // 4.1获得总条数
        long total = hits.getTotalHits().value;
        // 4.2文档数据
        SearchHit[] searchHits = hits.getHits();
        // 4.3遍历
        List<HotelDoc> result = new ArrayList<>();
        for (SearchHit hit : searchHits) {
            // 获得文档resource
            String json = hit.getSourceAsString();
            // 反序列化
            HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);

            // 获得高亮结果
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            if (!CollectionUtils.isEmpty(highlightFields)) {
                HighlightField highlight = highlightFields.get("name");
                if (highlight != null) {
                    String value = highlight.fragments()[0].string();
                    hotelDoc.setName(value);
                }
            }
            // 放入集合
            result.add(hotelDoc);
        }
        return new PageResult(total, result);
    }
}
```





### 2、搜索过滤

需求：添加品牌、城市、星级、价格等过滤功能



**修改实体类**

```java
@Data
public class RequestParams {
    private String key;
    private Integer page;
    private Integer size;
    private String sortBy;
    // 过滤字段
    private String city;
    private String brand;
    private String starName;
    private Integer minPrice;
    private Integer maxPrice;
}
```



**修改搜索业务**

在之前的业务中，只有match查询，根据关键字搜索，现在要添加条件过滤，包括：

- 品牌过滤：是keyword类型，用term查询
- 星级过滤：是keyword类型，用term查询
- 价格过滤：是数值类型，用range查询
- 城市过滤：是keyword类型，用term查询

多个查询条件组合，使用boolean查询来组合：

- 关键字搜索放到must中，参与算分
- 其它过滤条件放到filter中，不参与算分

```java
@Override
public PageResult search(RequestParams params) throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL语句
    BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    // 2.1 关键字搜索
    String key = params.getKey();
    if (key == null || "".equals(key)) {
        boolQuery.must(QueryBuilders.matchAllQuery());
    } else {
        boolQuery.must(QueryBuilders.matchQuery("all", key));
    }
    // 2.2 城市
    if (params.getCity() != null && !"".equals(params.getCity())) {
        boolQuery.filter(QueryBuilders.termQuery("city", params.getCity()));
    }
    // 2.3 品牌
    if (params.getBrand() != null && !"".equals(params.getBrand())) {
        boolQuery.filter(QueryBuilders.termQuery("brand", params.getBrand()));
    }
    // 2.4 星级
    if (params.getStarName() != null && !"".equals(params.getStarName())) {
        boolQuery.filter(QueryBuilders.termQuery("starName", params.getStarName()));
    }
    // 2.5 价格
    if (params.getMinPrice() != null && params.getMaxPrice() != null) {
        boolQuery.filter(QueryBuilders.rangeQuery("price").gte(params.getMinPrice()).lte(params.getMaxPrice()));
    }
    request.source().query(boolQuery);
    // 3.分页
    request.source().from((params.getPage() - 1) * params.getSize()).size(params.getSize());
    // 4.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    return handleResponse(response);
}
```





### 3、距离排序

我们点击定位后，会获取我们当前经纬度，然后向后端发起请求时会带上我们的位置信息，后端查询数据时应该将酒店按照离我的距离进行排序



**修改实体类**

实体类中添加地理坐标

```java
@Data
public class RequestParams {
    private String key;
    private Integer page;
    private Integer size;
    private String sortBy;
    private String city;
    private String brand;
    private String starName;
    private Integer minPrice;
    private Integer maxPrice;
    // 我当前的地理坐标
    private String location;
}
```





#### 3.1 距离排序API

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "price": "asc"  
    },
    {
      "_geo_distance" : {
          "FIELD" : "纬度，经度",
          "order" : "asc",
          "unit" : "km"
      }
    }
  ]
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051457010.png" style="zoom:60%;" />



#### 3.2 添加距离排序

```java
@Override
public PageResult search(RequestParams params) throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    builderBasicQuery(params, request);
    // 3.分页
    request.source().from((params.getPage() - 1) * params.getSize()).size(params.getSize());

    // 4.排序
    if (params.getLocation() != null && !"".equals(params.getLocation())) {
        request.source().sort(SortBuilders
                .geoDistanceSort("location", new GeoPoint(params.getLocation()))
                .order(SortOrder.ASC)
                .unit(DistanceUnit.KILOMETERS));
    }

    // 5.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    return handleResponse(response);
}
```





#### 3.3 距离显示

我们查询到附近酒店后还需要显示他离我的距离，其中通过距离排序后，在`sort`字段中的数据就是距离值，所以我们为`HotelDoc`实体类添加距离字段，然后将距离值添加进去后返回

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051500330.png)

```java
private PageResult handleResponse(SearchResponse response) {
    // 4.解析数据
    SearchHits hits = response.getHits();
    // 4.1获得总条数
    long total = hits.getTotalHits().value;
    // 4.2文档数据
    SearchHit[] searchHits = hits.getHits();
    // 4.3遍历
    List<HotelDoc> result = new ArrayList<>();
    for (SearchHit hit : searchHits) {
        // 获得文档resource
        String json = hit.getSourceAsString();
        // 反序列化
        HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);

        // 获得高亮结果
        Map<String, HighlightField> highlightFields = hit.getHighlightFields();
        if (!CollectionUtils.isEmpty(highlightFields)) {
            HighlightField highlight = highlightFields.get("name");
            if (highlight != null) {
                String value = highlight.fragments()[0].string();
                hotelDoc.setName(value);
            }
        }
        // 获取距离值
        Object[] sortValues = hit.getSortValues();
        if (sortValues.length > 0) {
            Object sortValue = sortValues[0];
            hotelDoc.setDistance(sortValue);
        }
        // 放入集合
        result.add(hotelDoc);
    }
    return new PageResult(total, result);
}
```







### 4、竞价排名

如果酒店掏了广告费，那么搜索时这个酒店的排名就会靠前，对这个掏了广告费的酒店通过`function_score`算分函数增加其得分，让其排名靠前。

因此我们给酒店增加一个`isAD`字段，如果为`true`表示掏了广告费，排名应该靠前



`function_score`：

- 过滤条件：判断isAD 是否为true
- 算分函数：可以用weight，固定加权值
- 加权方式：可以用默认的相乘，大大提高算分



**修改HotelDoc**

```java
public class HotelDoc {
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String location;
    private String pic;
    // 排序时的 距离值
    private Object distance;
    // 广告标记
    private Boolean isAD;
}
```



**添加广告标记**

我们选择几个酒店，为他们添加广告标记

```json
POST /hotel/_update/1975922994
{
    "doc": {
        "isAD": true
    }
}
POST /hotel/_update/2056126831
{
    "doc": {
        "isAD": true
    }
}
POST /hotel/_update/1989806195
{
    "doc": {
        "isAD": true
    }
}
POST /hotel/_update/2056105938
{
    "doc": {
        "isAD": true
    }
}
```





**添加算分函数**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051518473.png" style="zoom:60%;" />



```java
private void builderBasicQuery(RequestParams params, SearchRequest request) {
    // 2.准备DSL语句
    BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    // 2.1 关键字搜索
    String key = params.getKey();
    if (key == null || "".equals(key)) {
        boolQuery.must(QueryBuilders.matchAllQuery());
    } else {
        boolQuery.must(QueryBuilders.matchQuery("all", key));
    }
    // 2.2 城市
    if (params.getCity() != null && !"".equals(params.getCity())) {
        boolQuery.filter(QueryBuilders.termQuery("city", params.getCity()));
    }
    // 2.3 品牌
    if (params.getBrand() != null && !"".equals(params.getBrand())) {
        boolQuery.filter(QueryBuilders.termQuery("brand", params.getBrand()));
    }
    // 2.4 星级
    if (params.getStarName() != null && !"".equals(params.getStarName())) {
        boolQuery.filter(QueryBuilders.termQuery("starName", params.getStarName()));
    }
    // 2.5 价格
    if (params.getMinPrice() != null && params.getMaxPrice() != null) {
        boolQuery.filter(QueryBuilders.rangeQuery("price").gte(params.getMinPrice()).lte(params.getMaxPrice()));
    }

    // 算分函数
    FunctionScoreQueryBuilder functionScoreQuery =
        QueryBuilders.functionScoreQuery(
        // 原始查询
        boolQuery,
        // function score
        new FunctionScoreQueryBuilder.FilterFunctionBuilder[]{
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                // 过滤条件
                QueryBuilders.termQuery("isAD", true),
                // 算分函数
                ScoreFunctionBuilders.weightFactorFunction(10)
            )}).boostMode(CombineFunction.SUM); // 相加

    request.source().query(functionScoreQuery);
}
```









## 八、数据聚合

聚合常见的有三类：

- **桶（Bucket）**聚合：用来对文档做分组
  - `TermAggregation`：按照文档字段值分组，例如按照品牌值分组、按照国家分组
  - `Date Histogram`：按照日期阶梯分组，例如一周为一组，或者一月为一组

- **度量（Metric）**聚合：用以计算一些值，比如：最大值、最小值、平均值等
  - Avg：求平均值
  - Max：求最大值
  - Min：求最小值
  - Stats：同时求max、min、avg、sum等
- **管道（pipeline）**聚合：其它聚合的结果为基础做聚合





### 1、DSL实现聚合

#### 1.1 Bucket聚合

**基本语法**

```json
GET /hotel/_search
{
  "size": 0,  // 设置size为0，结果中不包含文档，只包含聚合结果
  "aggs": { // 定义聚合
    "brandAgg": { //给聚合起个名字
      "terms": { // 聚合的类型，按照品牌值聚合，所以选择term
        "field": "brand", // 参与聚合的字段
        "size": 20 // 希望获取的聚合结果数量
      }
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051552129.png)







**聚合结果排序**

默认情况下，Bucket聚合会统计Bucket内的文档数量，记为`_count`，并且按照`_count`降序排序。

我们可以指定order属性，自定义聚合的排序方式

```json
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 10,
        "order": {
          "_count": "asc"
        }
      }
    }
  }
}
```







**限定范围聚合**

默认情况下，Bucket聚合是对索引库的所有文档做聚合，但真实场景下，用户会输入搜索条件，因此聚合必须是对搜索结果聚合。那么聚合必须添加限定条件。

我们可以限定要聚合的文档范围，只要添加`query`条件即可

```json
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 300  // 只对300元以下的文档聚合
      }
    }
  }, 
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 10,
        "order": {
          "_count": "asc"
        }
      }
    }
  }
}
```







#### 1.2 Metric聚合

我们对酒店按照品牌分组后，形成了一个个桶。现在我们需要对桶内的酒店做运算，获取每个品牌的用户评分的min、max、avg等值。

这时需要用到Metric聚合，例如stat聚合：就可以获取min、max、avg等结果。

**基本语法**

```json
GET /hotel/_search
{
  "size": 0, 
  "aggs": {
    "brandAgg": { 
      "terms": { 
        "field": "brand", 
        "size": 20
      },
      "aggs": { // 是brands聚合的子聚合，也就是分组后对每组分别计算
        "scoreAgg": { // 聚合名称
          "stats": { // 聚合类型，这里stats可以计算min、max、avg等
            "field": "score" // 聚合字段，这里是score
          }
        }
      }
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051608466.png)





**排序**

可以通过聚合结果进行排序，例如根据平均分倒序排序

```json
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 10,
        "order": {
          "scoreAgg.avg": "desc"
        }
      },
      "aggs": {
        "scoreAgg": {
          "stats": {
            "field": "score"
          }
        }
      }
    }
  }
}
```







### 2、RestAPI实现聚合

**聚合**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051625631.png" style="zoom:60%;" />





**聚合结果的解析**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051626145.png" style="zoom:60%;" />

```java
@Test
public void testAgg() throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().size(0);
    request.source().aggregation(AggregationBuilders.terms("brandAgg").field("brand").size(10));
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析数据
    // 4.1 获得聚合结果
    Aggregations aggregations = response.getAggregations();
    // 4.2 获得terms
    Terms brandTerms = aggregations.get("brandAgg");
    // 4.3 获得buckets
    List<? extends Terms.Bucket> buckets = brandTerms.getBuckets();
    // 4.4 遍历所有的buckets
    for (Terms.Bucket bucket : buckets) {
        String key = bucket.getKeyAsString();
        System.out.println(key);
    }
}
```







### 3、业务实现

前端页面的品牌、城市等信息不应该是在页面写死，而是通过聚合索引库中的酒店数据得来，也就是存在那些酒店数据，通过聚合来显示对应的城市、星级、品牌。

同时我们在进行搜索后，城市、星级、品牌等信息，也应该随着我们设置的搜索条件而改变【避免出现搜的是上海的酒店，过滤信息中还有北京酒店信息】，因此我们在聚合时应该通过搜索信息对聚合结果进行限定

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051654806.png)



**controller**

```java
@PostMapping("/filters")
public Map<String, List<String>> getFilters(@RequestBody RequestParams params) {
    return hotelService.getFilters(params);
}
```

**service**

```java
@Override
public Map<String, List<String>> getFilters(RequestParams params) {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    // 2.1根据用户的搜索条件进行聚合的限定
    builderBasicQuery(params, request);
    // 2.2 不显示文档数据
    request.source().size(0);
    // 2.3 聚合
    request.source().aggregation(AggregationBuilders.terms("cityAgg").field("city").size(100));
    request.source().aggregation(AggregationBuilders.terms("brandAgg").field("brand").size(100));
    request.source().aggregation(AggregationBuilders.terms("starAgg").field("starName").size(100));
    // 3.发送请求
    SearchResponse response = null;
    try {
        response = client.search(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
        e.printStackTrace();
    }
    // 4.解析数据
    Map<String, List<String>> result = new HashMap<>();
    List<String> cityList = getAggByName(response, "cityAgg");
    List<String> brandList = getAggByName(response, "brandAgg");
    List<String> starList = getAggByName(response, "starAgg");
    result.put("city", cityList);
    result.put("brand", brandList);
    result.put("starName", starList);
    return result;
}

private List<String > getAggByName(SearchResponse response, String name) {
    List<String> list = new ArrayList<>();
    Aggregations aggregations = response.getAggregations();
    Terms terms = aggregations.get(name);
    List<? extends Terms.Bucket> cityBuckets = terms.getBuckets();
    for (Terms.Bucket cityBucket : cityBuckets) {
        String key = cityBucket.getKeyAsString();
        list.add(key);
    }
    return list;
}
```









## 九、自动补全

当用户在搜索框输入时，应该提示出与该字符有关的搜索项，可以根据用户输入的字母进行提示，因此这里还需要用到拼音分词功能



### 1、拼音分词器

在`GitHub`上`elasticsearch`的拼音分词插件。地址：https://github.com/medcl/elasticsearch-analysis-pinyin



安装方式与IK分词器一样

- 解压
- 上传到虚拟机中，`elasticsearch`的`plugin`目录
- 重启`elasticsearch`



**测试**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051722798.png)







### 2、自定义分词器

默认的拼音分词器会将每个汉字单独分为拼音，而我们希望的是每个词条形成一组拼音，需要对拼音分词器做个性化定制，形成自定义分词器。



`elasticsearch`中分词器（analyzer）的组成包含三部分：

- `character filters`：在`tokenizer`之前对文本进行处理。例如删除字符、替换字符
- `tokenizer`：将文本按照一定的规则切割成词条（term）。例如keyword，就是不分词；还有`ik_smart`
- `tokenizer filter`：将`tokenizer`输出的词条做进一步处理。例如大小写转换、同义词处理、拼音处理等

​	

文档分词时会依次由这三部分来处理文档：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051739492.png)



**自定义分词器**

自定义分词器是在创建索引库时指定。这里我们使用的是自定义的分词器`my_analyzer`，其中`tokenizer`使用`ik_max_word`，`filter`使用自定义的`py`。

创建`mappings`映射时，`name`字段的类型为`text`，创建时使用的分词器为自定义的分词器`my_analyzer`，这样创建时汉字和拼音都会建立倒排索引。

搜索时使用的分词器为`ik_smart`，即用户输入中文就通过中文搜索，输入拼音就通过拼音搜索

```json
PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": { // 自定义分词器
        "my_analyzer": {  // 分词器名称
          "tokenizer": "ik_max_word",
          "filter": "py"
        }
      },
      "filter": { // 自定义tokenizer filter
        "py": { // 过滤器名称
          "type": "pinyin", // 过滤器类型，这里是pinyin
		  "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "remove_duplicated_term": true,
          "none_chinese_pinyin_tokenize": false
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "my_analyzer",
        "search_analyzer": "ik_smart"
      }
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312051801949.png)







### 3、自动补全查询

`elasticsearch`提供了[Completion Suggester](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/search-suggesters.html)查询来实现自动补全功能。这个查询会匹配以用户输入内容开头的词条并返回。为了提高补全查询的效率，对于文档中字段的类型有一些约束：

- 参与补全查询的字段必须是`completion`类型。
- 字段的内容一般是用来补全的多个词条形成的数组。



**索引库**

```json
// 创建索引库
PUT /test
{
  "mappings": {
    "properties": {
      "title":{
        "type": "completion"
      }
    }
  }
}
```

**数据**

```json
POST test/_doc
{
  "title":  ["Sony",  "WH-1000XM3"]
}
POST test/_doc
{
  "title":  ["SK-II",  "PITERA"]
}
POST test/_doc
{
  "title":  ["Nintendo",  "switch"]
}
```

**查询语句**

```json
GET /test/_search
{
  "suggest": {
    "titleSuggest": { // 自定义名字
      "text": "so", // 关键字
      "completion": {
        "field": "title", // 补全查询的字段
        "skip_duplicates": true, // 跳过重复的
        "size": 10 // 获取前10条结果
      }
    }
  }
}
```







### 4、酒店搜索框自动补全

当前hotel索引库还没有设置拼音分词器，需要修改索引库中的配置。因此需要删除索引库然后重新创建。

另外，我们需要添加一个字段，用来做自动补全，将brand、business等都放进去，作为自动补全的提示。

- 修改hotel索引库结构，设置自定义拼音分词器

- 修改索引库的name、all字段，使用自定义分词器

- 索引库添加一个新字段suggestion，类型为completion类型，使用自定义的分词器

- 给`HotelDoc`类添加suggestion字段，内容包含brand、business

- 重新导入数据到hotel库



#### 4.1 修改索引库

> 1、自定义了两个分词器。第一个分词器先使用`ik_max_word`分词，然后使用`pinyin`分词器处理；第二个分词器使用`keyword`表示不分词，然后使用`pinyin`分词器处理。
>
> 2、第一个分词器用于全文检索，创建索引时使用`text_anlyzer`，即倒排索引中有分词后的中文和拼音，查询时使用`ik_max_word`，即输入中文就根据中文的倒排索引查询，输入拼音就根据拼音的倒排索引查询
>
> 3、第二个分词器用于自动补全，因为自动补全的字段是`completion`类型，且是数组形式，一般不需要分词，因此对于自动补全的字段来说，创建索引和搜索时都使用`completion_analyzer`，所以搜索时即使输入中文，也可以通过拼音来搜索
>
> 4、`name`和`all`字段建立索引时使用`text_anlyzer`，搜索时使用`ik_smart`
>
> 5、`suggestion`字段是用来补全的，他在Java实体中是一个List，包含了brand和city的信息，使用`completion_analyzer`分词器

```json
PUT /hotel
{
  "settings": {
    "analysis": {
      "analyzer": {
        "text_anlyzer": {
          "tokenizer": "ik_max_word",
          "filter": "py"
        },
        "completion_analyzer": {
          "tokenizer": "keyword",
          "filter": "py"
        }
      },
      "filter": {
        "py": {
          "type": "pinyin",
          "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "remove_duplicated_term": true,
          "none_chinese_pinyin_tokenize": false
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "id":{
        "type": "keyword"
      },
      "name":{
        "type": "text",
        "analyzer": "text_anlyzer",
        "search_analyzer": "ik_smart",
        "copy_to": "all"
      },
      "address":{
        "type": "keyword",
        "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword",
        "copy_to": "all"
      },
      "location":{
        "type": "geo_point"
      },
      "pic":{
        "type": "keyword",
        "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "text_anlyzer",
        "search_analyzer": "ik_smart"
      },
      "suggestion":{
          "type": "completion",
          "analyzer": "completion_analyzer"
      }
    }
  }
}

```





#### 4.2 修改HotelDoc实体

`HotelDoc`中要添加一个字段，用来做自动补全，内容可以是酒店品牌、商圈等信息。按照自动补全字段的要求，最好是这些字段的数组。

因此我们在`HotelDoc`中添加一个`suggestion`字段，类型为`List<String>`，然后将brand、business等信息放到里面。

```java
@Data
@NoArgsConstructor
public class HotelDoc {
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String location;
    private String pic;
    // 排序时的 距离值
    private Object distance;
    // 广告标记
    private Boolean isAD;
    private List<String> suggestion;

    public HotelDoc(Hotel hotel) {
        this.id = hotel.getId();
        this.name = hotel.getName();
        this.address = hotel.getAddress();
        this.price = hotel.getPrice();
        this.score = hotel.getScore();
        this.brand = hotel.getBrand();
        this.city = hotel.getCity();
        this.starName = hotel.getStarName();
        this.business = hotel.getBusiness();
        this.location = hotel.getLatitude() + ", " + hotel.getLongitude();
        this.pic = hotel.getPic();
        if (this.business.contains("/")) {
            String[] split = this.business.split("/");
            this.suggestion = new ArrayList<>();
            Collections.addAll(this.suggestion, split);
            this.suggestion.add(this.brand);
        } else {
            this.suggestion = Arrays.asList(this.brand, this.business);
        }
    }
}
```





#### 4.3 导入数据

将数据导入es后，进行测试



**自动补全查询**

```json
GET /hotel/_search 
{
  "suggest": {
    "mysuggest": {
      "text": "bj",
      "completion": {
        "field": "suggestion"
      }
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061456493.png)



**match查询**

我们对`name`和`all`字段建立索引时都使用了`text_anlyzer`，即先分词，然后pinyin处理，这样通过拼音也可以搜索到

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "name": "rujia"
    }
  }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061458079.png)







#### 4.4 JavaAPI

**查询**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061508919.png" style="zoom:50%;" />

**解析**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061509657.png" style="zoom:50%;" />

```java
@Test
public void testSuggest() throws IOException {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source()
        .suggest(new SuggestBuilder().addSuggestion("mySuggestion",
                                                    SuggestBuilders
                                                    .completionSuggestion("suggestion")
                                                    .prefix("bj").skipDuplicates(true).size(10)));
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析数据
    Suggest suggest = response.getSuggest();
    CompletionSuggestion mySuggestion = suggest.getSuggestion("mySuggestion");
    List<CompletionSuggestion.Entry.Option> options = mySuggestion.getOptions();
    for (CompletionSuggestion.Entry.Option option : options) {
        String value = option.getText().string();
        System.out.println(value);
    }
}
```







#### 4.5 搜索框自动补全

查看前端页面，可以发现当我们在输入框键入时，前端会发起`ajax`请求：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061534275.png)

**controller**

```java
@GetMapping("/suggestion")
public List<String> getSuggestion(String key) {
    return hotelService.getSuggestion(key);
}
```

**service**

```java
@Override
public List<String> getSuggestion(String key) {
    // 1.准备request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().suggest(new SuggestBuilder().addSuggestion("mySuggestion",
            SuggestBuilders
                    .completionSuggestion("suggestion")
                    .prefix(key)
                    .skipDuplicates(true)
                    .size(10)));
    // 3.发送请求
    SearchResponse response = null;
    try {
        response = client.search(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
        e.printStackTrace();
    }
    // 4.解析结果
    List<String> result = new ArrayList<>();
    Suggest suggest = response.getSuggest();
    CompletionSuggestion mySuggestion = suggest.getSuggestion("mySuggestion");
    List<CompletionSuggestion.Entry.Option> options = mySuggestion.getOptions();
    for (CompletionSuggestion.Entry.Option option : options) {
        String value = option.getText().string();
        result.add(value);
    }
    return result;
}
```







## 十、数据同步

`elasticsearch`中的酒店数据来自于`mysql`数据库，因此`mysql`数据发生改变时，`elasticsearch`也必须跟着改变，这个就是`elasticsearch`与`mysql`之间的**数据同步**。



### 1、常见的数据同步方式

#### 1.1 同步调用

- `hotel-demo`对外提供接口，用来修改`elasticsearch`中的数据
- 酒店管理服务在完成数据库操作后，直接调用`hotel-demo`提供的接口
- 优点：实现简单，粗暴
- 缺点：业务耦合度高

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061651862.png" style="zoom:50%;" />





#### 1.2 异步通知

- `hotel-admin`对`mysql`数据库数据完成增、删、改后，发送`MQ`消息
- `hotel-demo`监听`MQ`，接收到消息后完成`elasticsearch`数据修改
- 优点：低耦合，实现难度一般
- 缺点：依赖mq的可靠性

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061653437.png" style="zoom:50%;" />





#### 1.3 监听binlog

- 给mysql开启binlog功能
- mysql完成增、删、改操作都会记录在binlog中
- hotel-demo基于canal监听`binlog`变化，实时更新`elasticsearch`中的内容
- 优点：完全解除服务间耦合
- 缺点：开启binlog增加数据库负担、实现复杂度高

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061654261.png" style="zoom:50%;" />





### 2、实现数据同步

我们在`hotel_admin`中进行酒店数据的增删改时，发送一条消息到MQ中，`hotel_demo`监听队列，收到消息后执行相应操作。

对于修改和添加，因为在es中都是执行添加语句，因此这两个操作可以合并



#### 2.1 声明交换机

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061658913.png" style="zoom:60%;" />

在`hotel_demo`中声明交换机与队列

```java
public class MqConstants {
    /**
     * 交换机
     */
    public final static String HOTEL_EXCHANGE = "hotel.topic";
    /**
     * 监听新增和修改的队列
     */
    public final static String HOTEL_INSERT_QUEUE = "hotel.insert.queue";
    /**
     * 监听删除的队列
     */
    public final static String HOTEL_DELETE_QUEUE = "hotel.delete.queue";
    /**
     * 新增或修改的RoutingKey
     */
    public final static String HOTEL_INSERT_KEY = "hotel.insert";
    /**
     * 删除的RoutingKey
     */
    public final static String HOTEL_DELETE_KEY = "hotel.delete";
}
```

```java
@Configuration
public class MqConfig {
    @Bean
    public TopicExchange topicExchange() {
        return new TopicExchange(MqConstants.HOTEL_EXCHANGE, true, false);
    }

    @Bean
    public Queue insertQueue() {
        return new Queue(MqConstants.HOTEL_INSERT_QUEUE, true);
    }

    @Bean
    public Queue deleteQueue() {
        return new Queue(MqConstants.HOTEL_DELETE_QUEUE, true);
    }

    @Bean
    public Binding insertQueueBinding() {
        return BindingBuilder.bind(insertQueue()).to(topicExchange()).with(MqConstants.HOTEL_INSERT_KEY);
    }

    @Bean
    public Binding deleteQueueBinding() {
        return BindingBuilder.bind(deleteQueue()).to(topicExchange()).with(MqConstants.HOTEL_DELETE_KEY);
    }
}
```





#### 2.2 发送消息

在`hotel_admin`中，执行增删改动作后，发送消息到MQ中，消息内容就是hotel的id

```java
@PostMapping
public void saveHotel(@RequestBody Hotel hotel){
    hotel.setId(1l);
    hotelService.save(hotel);
    rabbitTemplate.convertAndSend(MqConstants.HOTEL_EXCHANGE, MqConstants.HOTEL_INSERT_KEY, hotel.getId());
}

@PutMapping()
public void updateById(@RequestBody Hotel hotel){
    if (hotel.getId() == null) {
        throw new InvalidParameterException("id不能为空");
    }
    hotelService.updateById(hotel);
    rabbitTemplate.convertAndSend(MqConstants.HOTEL_EXCHANGE, MqConstants.HOTEL_INSERT_KEY, hotel.getId());
}

@DeleteMapping("/{id}")
public void deleteById(@PathVariable("id") Long id) {
    hotelService.removeById(id);
    rabbitTemplate.convertAndSend(MqConstants.HOTEL_EXCHANGE, MqConstants.HOTEL_DELETE_KEY, id);
}
```





#### 2.3 接收消息

在`hotel_demo`中监听MQ消息，对于修改和新增操作，我们先通过id去数据库查到对应数据后，将其添加到es中

```java
@Component
public class HotelListener {

    @Autowired
    private HotelService hotelService;

    @RabbitListener(queues = MqConstants.HOTEL_INSERT_QUEUE)
    public void insertOrUpdateHotel(Long id) {
        hotelService.insertById(id);
    }

    @RabbitListener(queues = MqConstants.HOTEL_DELETE_QUEUE)
    public void deleteHotel(Long id) {
        hotelService.deleteById(id);
    }
}
```

**service**

```java
public void deleteById(Long id) {
    // 1.准备request
    DeleteRequest request = new DeleteRequest("hotel", id.toString());
    // 2.发送请求
    try {
        client.delete(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
        e.printStackTrace();
    }
}

public void insertById(Long id) {
    // 1.根据id查询酒店数据
    Hotel hotel = getById(id);
    // 2.将数据转为文档类型
    HotelDoc hotelDoc = new HotelDoc(hotel);
    // 3.准备request
    IndexRequest request = new IndexRequest("hotel").id(hotel.getId().toString());
    // 4.准备DSL
    request.source(JSON.toJSONString(hotelDoc), XContentType.JSON);
    // 5.发送请求
    try {
        client.index(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```







## 十一、ES集群

单机的`elasticsearch`做数据存储，有两个问题：海量数据存储问题、单点故障问题。

- 海量数据存储问题：将索引库从逻辑上拆分为N个分片（shard），存储到多个节点
- 单点故障问题：将分片数据在不同节点备份（replica ）

**ES集群相关概念**:

* 集群（cluster）：一组拥有共同的 cluster name 的 节点。

* <font color="red">节点（node)</font>   ：集群中的一个 Elasticearch 实例

* <font color="red">分片（shard）</font>：索引可以被拆分为不同的部分进行存储，称为分片。在集群环境下，一个索引的不同分片可以拆分到不同的节点中

  解决问题：数据量太大，单点存储量有限的问题。

* 主分片（Primary shard）：相对于副本分片的定义。

* 副本分片（Replica shard）每个主分片可以有一个或者多个副本，数据和主分片一样。



数据备份可以保证高可用，但是每个分片备份一份，所需要的节点数量就会翻一倍，成本太高

为了在高可用和成本间寻求平衡，可以这样做：

- 首先对数据分片，存储到不同节点
- 然后对每个分片进行备份，放到对方节点，完成互相备份

这样可以大大减少所需要的服务节点数量

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061720962.png" style="zoom:70%;" />





### 1、搭建ES集群

#### 1.1 创建ES集群

> 在单机上利用docker容器运行多个es实例来模拟es集群。生产环境推荐每一台服务节点仅部署一个es的实例。
>
> 部署es集群可以直接使用docker-compose来完成，要求你的Linux虚拟机至少有**4G**的内存空间

```sh
version: '2.2'
services:
  es01:
    image: elasticsearch:7.12.1
    container_name: es01
    environment:
      - node.name=es01 # 节点名称
      - cluster.name=es-docker-cluster  # 集群名称，不同es集群名称相同，他们会形成一个集群
      - discovery.seed_hosts=es02,es03  # 其他集群的ip，因为在同一个网络，所以可以使用节点名
      - cluster.initial_master_nodes=es01,es02,es03 # 选取主节点时的候选节点
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m" # 内存大小
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: elasticsearch:7.12.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data02:/usr/share/elasticsearch/data
    ports:
      - 9201:9200
    networks:
      - elastic
  es03:
    image: elasticsearch:7.12.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic
    ports:
      - 9202:9200
volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```



es运行需要修改一些linux系统权限，修改`/etc/sysctl.conf`文件

```sh
vi /etc/sysctl.conf
```

添加下面的内容，这个参数控制了单个进程能够拥有的内存区域数量的最大值

```sh
vm.max_map_count=262144
```

然后执行命令，让配置生效：

```sh
sysctl -p
```



通过docker-compose启动集群：

```sh
docker-compose up -d
```





#### 1.2 集群状态监控

`kibana`可以监控es集群，不过新版本需要依赖es的x-pack 功能，配置比较复杂。

这里推荐使用`cerebro`来监控es集群状态，官方网址：https://github.com/lmenezes/cerebro

在windows上将`cerebro`解压后，直接运行`cerebro.bat`文件，然后访问`http://localhost:9000/`进入管理界面，输入任意一个es的`ip:端口`即可进入。

绿色的条表示集群处于健康状态，这里会显示所有节点的信息，其中五角星的表示主节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061742420.png)





#### 1.3 创建索引库

**1、利用`kibana`的`DevTools`创建索引库**

在`DevTools`中输入指令

```json
PUT /test
{
  "settings": {
    "number_of_shards": 3, // 分片数量
    "number_of_replicas": 1 // 副本数量
  },
  "mappings": {
    "properties": {
      // mapping映射定义 ...
    }
  }
}
```





**2、利用cerebro创建索引库**

创建索引时有3个分片，每个分片有一个副本

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061811197.png)



创建后可以看到实心的是每个节点的主分片，虚线的是副本，主副分片在不同节点上

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312061812948.png)





### 2、集群脑裂问题

#### 2.1 集群职责划分

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062003541.png" style="zoom:50%;" />

默认情况下，集群中的任何一个节点都同时具备上述四种角色。



但是真实的集群一定要将集群职责分离：

- master节点：对CPU要求高，但是内存要求低
- data节点：对CPU和内存要求都高
- coordinating节点：对网络带宽、CPU要求高

职责分离可以让我们根据不同节点的需求分配不同的硬件去部署。而且避免业务之间的互相干扰。



**es集群职责划分如图**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062008925.png" style="zoom:50%;" />





#### 2.2 脑裂问题

脑裂是因为集群中的节点失联导致的。

例如一个集群中，主节点与其它节点失联

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062010229.png" style="zoom:50%;" />

此时，`node2`和`node3`认为`node1`宕机，就会重新选主

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062010438.png" style="zoom:50%;" />

当`node3`当选后，集群继续对外提供服务，`node2`和`node3`自成集群，`node1`自成集群，两个集群数据不同步，出现数据差异。

当网络恢复后，因为集群中有两个master节点，集群状态的不一致，出现脑裂的情况

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062011783.png" style="zoom:50%;" />



**解决脑裂的方案**

要求选票超过` (eligible节点数量 + 1）/ 2 `才能当选为主，因此eligible节点数量最好是奇数。对应配置项是`discovery.zen.minimum_master_nodes`，在ES7.0以后，已经成为默认配置，因此一般不会发生脑裂问题。

例如：3个节点形成的集群，选票必须超过 （3 + 1） / 2 ，也就是2票。node3得到node2和node3的选票，当选为主。node1只有自己1票，没有当选。集群中依然只有1个主节点，没有出现脑裂。





### 3、集群分布式存储

当新增文档时，应该保存到不同分片，保证数据均衡，而将数据保存到哪个分片以及去哪个分片查询数据是通过`coordinating node`确定的



我们向test索引库中插入3条数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062038921.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062037033.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062036536.png)



我们去索引库查询数据，可以发现3条数据分布在3个不同的节点上

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062040753.png)

```json
{
    "took": 968,
    "timed_out": false,
    "_shards": {
        "total": 3,
        "successful": 3,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 3,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_shard": "[test][0]",
                "_node": "LjRfgWPqSkWxoEPjpQuD8g",
                "_index": "test",
                "_type": "_doc",
                "_id": "5",
                "_score": 1.0,
                "_source": {
                    "title": "第5条数据"
                },
                "_explanation": {
                    "value": 1.0,
                    "description": "*:*",
                    "details": []
                }
            },
            {
                "_shard": "[test][1]",
                "_node": "MHGCR_NIQJmyByMl9Yu4Lw",
                "_index": "test",
                "_type": "_doc",
                "_id": "2",
                "_score": 1.0,
                "_source": {
                    "title": "第2条数据"
                },
                "_explanation": {
                    "value": 1.0,
                    "description": "*:*",
                    "details": []
                }
            },
            {
                "_shard": "[test][2]",
                "_node": "uKtAtBAdS8GCffb5xWVA7A",
                "_index": "test",
                "_type": "_doc",
                "_id": "1",
                "_score": 1.0,
                "_source": {
                    "title": "第1条数据"
                },
                "_explanation": {
                    "value": 1.0,
                    "description": "*:*",
                    "details": []
                }
            }
        ]
    }
}
```





#### 3.1 分片存储原理

`elasticsearch`会通过hash算法来计算文档应该存储到哪个分片

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062043997.png)

- _routing默认是文档的id
- 算法与分片数量有关，因此索引库一旦创建，分片数量不能修改



**新增文档流程**

（1）新增一个id=1的文档

（2）对id做hash运算，假如得到的是2，则应该存储到shard-2

（3）shard-2的主分片在node3节点，将数据路由到node3

（4）保存文档

（5）同步给shard-2的副本replica-2，在node2节点

（6）返回结果给coordinating-node节点

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062044563.png" style="zoom:50%;" />



**查询文档流程**

（1） 请求被发给了节点1

（2）节点1计算出该数据属于主分片2，这时候，有三个选择，分别是位于节点1的副本2， 节点2的副本2，节点3的主分片2， 假设节点1负载均衡，采用轮询的方式，选中了节点2，把请求转发。

（3） 节点2把数据返回给节点1， 节点1 最后返回给客户端。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062104536.png)






#### 3.2 集群分布式查询

当我们执行`match_all`查询全部数据时，`elasticsearch`的查询分成两个阶段：

- scatter phase：分散阶段，coordinating node会把请求分发到每一个分片

- gather phase：聚集阶段，coordinating node汇总data node的搜索结果，并处理为最终结果集返回给用户

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062046410.png" style="zoom:70%;" />







### 4、集群故障转移

集群的master节点会监控集群中的节点状态，如果发现有节点宕机，会立即将宕机节点的分片数据迁移到其它节点，确保数据安全，这个就是**故障转移**。



1、现在node1是主节点，node2和node3是从节点

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062047343.png" style="zoom:50%;" />

2、这时，node1节点宕机了

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062048877.png" style="zoom:50%;" />

3、宕机后需要重新选主，现在选中了node2

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062048932.png" style="zoom:50%;" />

4、node2成为主节点后，会检测集群监控状态，发现：shard-1、shard-0没有副本节点。因此需要将node1上的数据迁移到node2、node3

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312062049240.png" style="zoom:50%;" />

5、如果node1节点恢复后，主节点会再次将shard-1、shard-0分配给node1，但是此时主节点是node2

































