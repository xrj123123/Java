## 一、Redis入门

Redis是一种键值型的NoSql数据库

其中**键值型**，是指Redis中存储的数据都是以key、value对的形式存储，而value的形式多种多样，可以是字符串、数值、甚至json



### 1.1 NoSQL

**NoSql**可以翻译做Not Only Sql（不仅仅是SQL），或者是No Sql（非Sql的）数据库。是相对于传统关系型数据库而言，有很大差异的一种特殊的数据库，因此也称之为非关系型数据库。





**结构化与非结构化**

传统关系型数据库是结构化数据，每一张表都有严格的约束信息：字段名、字段数据类型、字段约束等等信息，插入的数据必须遵守这些约束

而NoSql则对数据库格式没有严格约束，往往形式松散，自由。可以是键值型、文档型、图格式





**关联和非关联**

传统数据库的表与表之间往往存在关联，例如外键

而非关系型数据库不存在关联关系，要维护关系要么靠代码中的业务逻辑，要么靠数据之间的耦合





**查询方式**

传统关系型数据库会基于Sql语句做查询，语法有统一标准；

而不同的非关系数据库查询语法差异极大，五花八门各种各样。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309181708908.png" style="zoom: 80%;" />





**事务**

传统关系型数据库能满足事务ACID的原则。

而非关系型数据库往往不支持事务，或者不能严格保证ACID的特性，只能实现基本的一致性。







**总结**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309181710824.png" style="zoom: 50%;" />

- 存储方式
  - 关系型数据库基于磁盘进行存储，会有大量的磁盘IO，对性能有一定影响
  - 非关系型数据库，他们的操作更多的是依赖于内存来操作，内存的读写速度会非常快，性能自然会好一些

* 扩展性
  * 关系型数据库集群模式一般是主从，主从数据一致，起到数据备份的作用，称为垂直扩展。
  * 非关系型数据库可以将数据拆分，存储在不同机器上，可以保存海量数据，解决内存大小有限的问题。称为水平扩展。
  * 关系型数据库因为表之间存在关联关系，如果做水平扩展会给数据查询带来很多麻烦







### 1.2 Redis介绍

> Redis是一个基于内存的键值型NoSQL数据库

- 键值（key-value）型，value支持多种不同数据结构，功能丰富
- 单线程，每个命令具备原子性
- 低延迟，速度快（基于内存、IO多路复用、良好的编码）。
- 支持数据持久化
- 支持主从集群、分片集群
- 支持多语言客户端



安装完Redis后，在Redis目录下

- redis-cli：是redis提供的命令行客户端
- redis-server：是redis的服务端启动脚本
- redis-sentinel：是redis的哨兵启动脚本



在`redis.conf`文件中可以修改`Redis`的一些配置：

```properties
# 允许访问的地址，默认是127.0.0.1，会导致只能在本地访问。修改为0.0.0.0则可以在任意IP访问，生产环境不要设置为0.0.0.0
bind 0.0.0.0
# 守护进程，修改为yes后即可后台运行
daemonize yes 
# 密码，设置后访问Redis必须输入密码
requirepass 123321
```

```properties
# 监听的端口
port 6379
# 工作目录，默认是当前目录，也就是运行redis-server时的命令，日志、持久化等文件会保存在这个目录
dir .
# 数据库数量，设置为1，代表只使用1个库，默认有16个库，编号0~15
databases 1
# 设置redis能够使用的最大内存
maxmemory 512mb
# 日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```



启动redis

```
redis-server redis.conf
```

停止redis

```
redis-cli shutdown
```







### 1.3 Redis客户端

- 命令行客户端
- 图形化桌面客户端
- 编程客户端



**命令行客户端**

> Redis安装完成后就自带了命令行客户端：`redis-cli`

```
redis-cli [options] [commonds]
```

其中常见的`options`有：

- `-h 127.0.0.1`：指定要连接的redis节点的IP地址，默认是127.0.0.1
- `-p 6379`：指定要连接的redis节点的端口，默认是6379
- `-a 123321`：指定redis的访问密码 

其中的`commonds`就是Redis的操作命令，例如：

- `ping`：与redis服务端做心跳测试，服务端正常会返回`pong`

不指定commond时，会进入`redis-cli`的交互控制台





**桌面客户端**

> github可以安装redis的桌面客户端`RESP`，打开后可以连接redis服务器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309191517669.png)

Redis默认有16个仓库，编号从0至15.  通过配置文件可以设置仓库数量，但是不超过16，并且不能自定义仓库名称。

如果是基于redis-cli连接Redis服务，可以通过select命令来选择数据库：

```sh
# 选择 0号库
select 0
```









## 二、Redis基础

### 2.1 数据结构

Redis是典型的`key-value`数据库，`key`一般是字符串，而`value`包含很多不同的数据类型

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309191457061.png" style="zoom:67%;" />





### 2.2 Redis通用命令

- `KEYS`：查看符合模板的所有key

  ```sh
  keys pattern  # 查找当前符合pattern模式的所有key
  
  keys * # 查看所有的key
  ```

- `DEL`：删除一个指定的key

  ```sh
  del key1 [key...]  # 通过del指定key的名称，可以删除该键值对，可以删除多个
  ```

- `EXISTS`：判断key是否存在

  ```sh
  exists key1 [key...]  # 查看指定的key是否存在
  ```

- `EXPIRE`：给一个key设置有效期，有效期到期时该key会被自动删除

  ```sh
  expire k1 10  # 将k1这个键值对设置有效期为10秒，10秒后过期，如果set进去不设置有效期，默认为-1，表示永久
  ```

- `TTL`：查看一个KEY的剩余有效期

  ```sh
  ttl k1  # 查看k1的剩余有效期，如果是永久的就返回-1
  ```

通过`help [command] `可以查看一个命令的具体用法







### 2.3 String类型

String类型，也就是字符串类型，是Redis中最简单的存储类型。

其value是字符串，不过根据字符串的格式不同，又可以分为3类：

- string：普通字符串
- int：整数类型，可以做自增、自减操作
- float：浮点类型，可以做自增、自减操作

不管是哪种格式，底层都是字节数组形式存储，只不过是编码方式不同。字符串类型的最大空间不能超过512m



#### **常见命令**

- `SET`：添加或者修改已经存在的一个String类型的键值对

- `GET`：根据key获取String类型的value

- `MSET`：批量添加多个String类型的键值对

- `MGET`：根据多个key获取多个String类型的value

- `INCR`：让一个整型的key自增1

- `INCRBY`：让一个整型的key自增并指定步长

  ```sh
  incrby k1 2 # 让k1增加2，如果这里写负数就是减小
  ```

- `DECR`：让一个整型的key自减1

- `DECRBY`：让一个整型的key自减并指定步长

- `INCRBYFLOAT`：让一个浮点类型的数字自增并指定步长

- `SETNX`：添加一个String类型的键值对，前提是这个key不存在，否则不执行

  ```sh
  setnx name xrj  # 当name不存在时才会添加进去
  
  set name xrj nx  # setnx相当于这条命令
  ```

- `SETEX`：添加一个String类型的键值对，并且指定有效期

  ```sh
  setex name 20 xrj # 设置name:xrj这个键值对，有效期为20秒
  
  set name xrj ex 20 # setex相当于这条命令
  ```

  



#### key结构

Redis没有类似MySQL中的Table的概念

例如，需要存储用户、商品信息到redis，有一个用户id是1，有一个商品id恰好也是1，此时如果使用id作为key，那就会冲突了

我们可以通过给key添加前缀加以区分，不过这个前缀不是随便加的，有一定的规范：

Redis的key允许有多个单词形成层级结构，多个单词之间用`:`隔开，格式如下：

```
项目名:业务名:类型:id
```

这个格式并非固定，也可以根据自己的需求来删除或添加词条。这样以来，我们就可以把不同类型的数据区分开了。从而避免了key的冲突问题。

例如我们的项目名称叫 `pro1`，有`user`和`product`两种不同类型的数据，我们可以这样定义`key`：

- user相关的key：**pro1:user:1**

- product相关的key：**pro1:product:1**



如果value是一个Java对象，例如一个User对象，则可以将对象序列化为JSON字符串后存储：

`set pro1:user:1 '{"id":1,  "name": "Jack", "age": 21}'`

`set pro1:product:1 '{"id":1,  "name": "mi11", "price": 4999}'`

| **KEY**        | **VALUE**                                |
| -------------- | ---------------------------------------- |
| pro1:user:1    | {"id":1,  "name": "Jack", "age": 21}     |
| pro1:product:1 | {"id":1,  "name": "mi11", "price": 4999} |



并且，在Redis的桌面客户端中，还会以相同前缀作为层级结构，让数据看起来层次分明

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309191613177.png)





### 2.4 Hash类型

Hash类型，也叫散列，其value是一个无序字典，类似于Java中的HashMap结构。

String结构是将对象序列化为JSON字符串后存储，当需要修改对象某个字段时很不方便

Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309191615534.png)

Hash的常见命令有：

- `HSET key field value`：添加或者修改hash类型key的field的值

  ```sh
  hset pro1:user:1 id 1
  hset pro1:user:1 name xrj
  hset pro1:user:1 age 20
  ```

- `HGET key field`：获取一个hash类型key的field的值

  ```sh
  hget pro1:user:1 name
  ```

- `HMSET`：批量添加多个hash类型key的field的值

  ```sh
  hmset pro1:user:2 id 2 name xyy age 18
  ```

- `HMGET`：批量获取多个hash类型key的field的值

  ```sh
  hmget pro1:user:1 id name age
  ```

- `HGETALL`：获取一个hash类型的key中的所有的field和value

  ```sh
  hgetall pro1:user:1
  ```

- `HKEYS`：获取一个hash类型的key中的所有的field

  ```sh
  hkeys pro1:user:1
  ```

- `HVALS`：获取一个hash类型的key中的所有的value

  ```sh
  hvals pro1:user:1
  ```

- `HINCRBY`：让一个hash类型key的字段值自增并指定步长

  ```sh
  hincrby pro1:user:1 age 5
  ```

- `HSETNX`：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行

  ```sh
  hsetnx pro1:user:1 name xx
  ```

  





### 2.5 List类型

Redis中的List类型与Java中的`LinkedList`类似，可以看做是一个双向链表结构。既可以支持正向检索和也可以支持反向检索。

特征也与LinkedList类似：

- 有序
- 元素可以重复
- 插入和删除快
- 查询速度一般

常用来存储一个有序数据，例如：朋友圈点赞列表，评论列表等。



List的常见命令有：

- `LPUSH key value ... `：向列表左侧插入一个或多个元素

  ```sh
  lpush users 1 2 3  # 插入后顺序为3 2 1
  ```

- `LPOP key`：移除并返回列表左侧的第一个元素，没有则返回nil

  ```sh
  lpop users # 删除最左侧元素并返回
  ```

- `RPUSH key value ... `：向列表右侧插入一个或多个元素

  ```sh
  rpush users 4 5 6 # 插入后顺序为 4 5 6
  ```

- `RPOP key`：移除并返回列表右侧的第一个元素，没有则返回nil

  ```sh
  rpop users # 删除最右侧元素并返回
  ```

- `LRANGE key start end`：返回`[start, end]`内的所有元素

  ```sh
  lrange users 0 3 # 返回[0, 3]中所有元素
  ```

- `BLPOP和BRPOP`：与`LPOP`和`RPOP`类似，只不过在没有元素时等待指定时间，而不是直接返回nil

  ```sh
  blpop users 50 # 获取users中最左边的元素，最多等待50秒
  ```

  

​		





### 2.6 Set类型

Redis的Set结构与Java中的HashSet类似，可以看做是一个value为null的HashMap。因为也是一个hash表，因此具备与HashSet类似的特征：

- 无序

- 元素不可重复

- 查找快

- 支持交集、并集、差集等功能



Set的常见命令有：

- `SADD key value ... `：向set中添加一个或多个元素

  ```sh
  sadd zs lisi wangwu zhaoliu  # 张三的好友
  sadd ls wangwu mazi ergou # 李四的好友
  ```

- `SREM key value ... `: 移除set中的指定元素

  ```sh
  srem zs lisi # 将李四从张三的好友列表中移除
  ```

- `SCARD key`： 返回set中元素的个数

  ```sh
  scard zs # 计算张三的好友有几人
  ```

- `SISMEMBER key value`：判断一个元素是否存在于set中

  ```sh
  sismember ls zhangsan # 判断张三是否是李四的好友
  ```

- `SMEMBERS`：获取set中的所有元素

  ```sh
  smembers zs # 查找张三的所有好友
  ```

- `SINTER key1 key2 ... `：求key1与key2的交集

  ```sh
  sinter zs ls # 张三和李四有哪些共同好友
  ```

- `SDIFF key1 key2 ...`：求key1与key2的差集

  ```sh
  sdiff zs ls # 哪些人是张三的好友却不是李四的好友
  ```

- `SUNION key1 key2 ...`：求key1与key2的并集

  ```sh
  sunion zs ls # 张三和李四的好友总共有哪些人
  ```







### 2.7 SortedSet类型

Redis的`SortedSet`是一个可排序的set集合，与Java中的TreeSet有些类似，但底层数据结构却差别很大。

`SortedSet`中的每一个元素都带有一个`score`属性，可以基于`score`属性对元素排序，底层的实现是一个跳表（SkipList）加 hash表。

SortedSet具备下列特性：

- 可排序
- 元素不重复
- 查询速度快

因为SortedSet的可排序特性，经常被用来实现排行榜这样的功能。



SortedSet的常见命令有：

- `ZADD key score member`：添加一个或多个元素到sorted set ，如果已经存在则更新其score值

  ```sh
  zadd students 85 jack 89 lucy 82 rose 95 tom 78 jerry 92 amy 76 miles
  ```

- `ZREM key member`：删除sorted set中的一个指定元素

  ```shell
  zrem students tom # 删除tom
  ```

- `ZSCORE key member`: 获取sorted set中的指定元素的score值

  ```sh
  zscore students amy  # 获取amy的分数
  ```

- `ZRANK key member`：获取sorted set 中的指定元素的排名，从0开始

  ```sh
  zrank students rose # 获取rose的排名
  ```

- `ZCARD key`：获取sorted set中的元素个数

  ```sh
  zcard students # 查询班级中学生数量
  ```

- `ZCOUNT key min max`：统计score值在给定范围内的所有元素的个数

  ```sh
  zcount students 0 80 # 查询80分及以下有几个学生
  ```

- `ZINCRBY key increment member`：让sorted set中的指定元素自增，步长为指定的increment值

  ```sh
  zincrby students 2 amy # 给amy加2分
  ```

- `ZRANGE key min max`：按照score排序后，获取指定排名范围内的元素

  ```sh
  zrange students 0 2 # 查出成绩前3名的同学
  ```

- `ZRANGEBYSCORE key min max`：按照score排序后，获取指定score范围内的元素

  ```sh
  zrangebyscore students 0 80 # 查出成绩80分及以下的所有同学
  ```

- `ZDIFF、ZINTER、ZUNION`：求差集、交集、并集

注意：所有的排名默认都是升序，如果要降序则在命令的`Z`后面添加`REV`即可，例如：

- **升序**获取sorted set 中的指定元素的排名：`ZRANK key member`

- **降序**获取sorted set 中的指定元素的排名：`ZREVRANK key memeber`







## 三、Java客户端

> 在Redis官网中提供了各种语言的客户端，地址：https://redis.io/docs/clients/
>
> 标记为`*`的就是推荐使用的Java客户端，包括：
>
> - `Jedis`和`Lettuce`：这两个主要是提供了Redis命令对应的API，方便我们操作Redis，SpringDataRedis又对这两种做了抽象和封装。
>   - `Jedis`：以Redis命令作为方法名称，但是Jedis实例是线程不安全的，多线程环境下需要基于连接池来使用
>   - `Lettuce`：基于Netty实现，支持同步、异步和响应式编程方式，并且是线程安全的。支持Redis的哨兵模式、集群模式和管道模式
> - `Redisson`：是在Redis基础上实现了分布式的可伸缩的java数据结构，例如Map、Queue等，而且支持跨进程的同步机制：Lock、Semaphore等待，比较适合用来实现特殊的功能需求。



### 3.1 Jedis客户端

#### 快速使用

**1、导入依赖**

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0</version>
</dependency>
```



**2、建立连接**

```java
private Jedis jedis;

@BeforeEach
void setup(){
    // 1.建立连接
    jedis = new Jedis("127.0.0.1", 6379);
    // 2.选择库, 默认为第一个
    jedis.select(0);
}
```



**3、测试**

```java
public class testJedis {
    private Jedis jedis;

    @BeforeEach
    void setup(){
        // 1.建立连接
        jedis = new Jedis("127.0.0.1", 6379);
        // 2.选择库, 默认为第一个
        jedis.select(0);
    }

    @Test
    void testString(){
        String result = jedis.set("user:name", "xrj");
        System.out.println("result: " + result);    // result: OK
        String name = jedis.get("user:name");
        System.out.println(name);   // xrj
    }

    @Test
    void testHash(){
        jedis.hset("person:1", "name", "xrj");
        Map<String, String> map = new HashMap<>();
        map.put("name", "xyy");
        map.put("age", "20");
        jedis.hmset("person:2", map);
        Map<String, String> res1 = jedis.hgetAll("person:1");
        Map<String, String> res2 = jedis.hgetAll("person:2");
        System.out.println(res1);   // {name=xrj}
        System.out.println(res2);   // {name=xyy, age=20}
    }

    @AfterEach
    void close(){
        if (jedis != null) {
            jedis.close();
        }
    }
}
```



**4、释放资源**

```java
@AfterEach
void close(){
    if (jedis != null) {
        jedis.close();
    }
}
```





#### 连接池

`Jedis`本身是线程不安全的，并且频繁的创建和销毁连接会有性能损耗，因此我们一般使用`Jedis`连接池代替`Jedis`的直连方式。

```java
public class JedisConnectionFactory {
    private static JedisPool jedisPool;
    static {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        // 最大连接
        jedisPoolConfig.setMaxTotal(8);
        // 最大空闲连接
        jedisPoolConfig.setMaxIdle(8);
        // 最小空闲连接
        jedisPoolConfig.setMinIdle(0);
        // 设置最长等待时间，即等待多少ms后仍没有可用连接就报错
        jedisPoolConfig.setMaxWaitMillis(1000);
        jedisPool = new JedisPool(jedisPoolConfig, "127.0.0.1", 6379, 1000);
    }

    public static Jedis getJedisConnect(){
        return jedisPool.getResource();
    }
}
```



> 此时`Jedis`的连接就可以直接从连接池获取

```java
@BeforeEach
void setup(){
    // 1.建立连接
    jedis = JedisConnectionFactory.getJedisConnect();
    // 2.选择库, 默认为第一个
    jedis.select(0);
}
```



**Jedis的close方法**

> 如果我们使用直接创建`Jedis`的方式，去close时，走的是else分支，即`this.dataSource`为`null`
>
> 如果我们使用连接池的方式获取`Jedis`连接，去close时，走的是`pool.returnResource(this);`即归还连接

```java
public void close() {
    if (this.dataSource != null) {
        JedisPoolAbstract pool = this.dataSource;
        this.dataSource = null;
        if (this.isBroken()) {
            pool.returnBrokenResource(this);
        } else {
            pool.returnResource(this);
        }
    } else {
        super.close();
    }

}
```







### 3.2 SpringDataRedis

SpringData是Spring中数据操作的模块，包含对各种数据库的集成，其中对Redis的集成模块就叫做SpringDataRedis

- 提供了对不同Redis客户端的整合（Lettuce和Jedis）
- 提供了RedisTemplate统一API来操作Redis
- 支持Redis的发布订阅模型
- 支持Redis哨兵和Redis集群
- 支持基于Lettuce的响应式编程
- 支持基于JDK、JSON、字符串、Spring对象的数据序列化及反序列化。【可以将java对象序列化后存入redis】
- 支持基于Redis的JDKCollection实现



SpringDataRedis中提供了RedisTemplate工具类，其中封装了各种对Redis的操作。并且将不同数据类型的操作API封装到了不同的类型中：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309201554486.png)







#### 快速使用

**1、导入依赖**

创建SpringBoot项目，导入`Redis`的依赖，同时导入连接池的依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- 引入连接池 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```



**2、配置Redis**

> SpringBoot默认导入的是`lettuce`，如果我们想使用`Jedis`需要将其依赖导入

```yml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 0
        max-wait: 1000
```



**3、测试**

> `RedisTemplate`操作的key和value都是Object类型的，底层可以将对象自动序列化

```java
@SpringBootTest
class Redis02SpringbootDataRedisApplicationTests {

    @Autowired
    RedisTemplate redisTemplate;

    @Test
    void testRedis() {
        // 操作String
        ValueOperations ops = redisTemplate.opsForValue();
        ops.set("name", 123);
        Object name = ops.get("name");
        System.out.println(name);
    }
}
```





#### 自定义序列化

> 上边我们使用`RedisTemplate`往redis中存数据，但是在Redis中查看时发现，我们存入的数据`name, 123`变成了下边这样。
>
> 这是因为`RedisTemplate`操作的key和value都是Object类型的，在写入Redis之前，他会将Object类型数据序列化为字节形式。默认是采用JDK序列化【底层就是`ObjectOutputStream`通过`writeObject`将对象序列化】，然后再存到redis中，因此实际存到Redis中的数据是序列化后的字节形式
>
> 这样存储可读性差，而且内存占用大

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309201630157.png)



```java
if (this.defaultSerializer == null) {
    this.defaultSerializer = new JdkSerializationRedisSerializer(this.classLoader != null ? this.classLoader : this.getClass().getClassLoader());
}

...
    
if (this.keySerializer == null) {
    this.keySerializer = this.defaultSerializer;
    defaultUsed = true;
}

if (this.valueSerializer == null) {
    this.valueSerializer = this.defaultSerializer;
    defaultUsed = true;
}

...
```





**自定义序列化**

> 1、默认的`RedisTemplate`的key和value都是Object类型，且使用的是默认的序列化器。
>
> 2、我们自定义一个`RedisTemplate`并加入ioc容器中，key使用`String`，value使用`Object`，key的序列化使用`RedisSerializer.string()`，即`UTF-8`编码，value的序列化使用`jsonRedisSerializer`，即json的格式，因此我们还需要jackson的依赖
>
> 3、SpringBoot的自动配置类配置`RedisTemplate`时设置了`@ConditionalOnMissingBean(name = {"redisTemplate"})`，即没有`redisTemplate`这个bean时，他才会为我们配置，我们手动配置后，他就不会帮我们配置了，因此使用的就是我们自定义的这个`redisTemplate`，这样key就是String类型，value就会序列化为json格式，然后存入redis
>
> 4、这里将java对象序列化时，java对象不需要实现序列化接口，因为这里是使用Jackson库将Java对象序列化和反序列化的。Jackson能够直接将Java对象转换为JSON格式，并且能够将JSON格式的数据反序列化为Java对象，他是通过反射实现的，因此不需要实现序列化接口。

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        // 创建RedisTemplate对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 设置连接工厂
        template.setConnectionFactory(connectionFactory);
        // 创建JSON序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        // 设置Key的序列化
        template.setKeySerializer(RedisSerializer.string());  // StringRedisSerializer.UTF_8
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置Value的序列化
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);
        // 返回
        return template;
    }
}
```



```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
```







**存入java对象**

> 我们自定义了`RedisTemplate`后，key序列化为String类型，value序列化为json
>
> 当我们存储java对象时，java对象会被序列化为json然后存入redis中
>
> 取数据的时候，将json数据反序列化为java对象，根据json中的`"@class": "com.xrj.redis02springbootdataredis.pojo.User"`就拿到了我们的java对象类型

```java
@Test
void testObject(){
    ValueOperations ops = redisTemplate.opsForValue();
    ops.set("user:10", new User("张三", 20));
    User o = (User) ops.get("user:10");
    System.out.println(o); // User(name=张三, age=20)
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309201650499.png)









#### StringRedisTemplate

> 1、上边采用了JSON序列化来代替默认的JDK序列化方式。整体可读性有了很大提升，并且能将Java对象自动的序列化为JSON字符串，并且查询时能自动把JSON反序列化为Java对象。不过，其中记录了序列化时对应的class名称，目的是为了查询时实现自动反序列化。这会带来额外的内存开销。
>
> 2、为了节省内存空间，我们可以不使用JSON序列化器来处理value，而是统一使用String序列化器，要求只能存储String类型的key和value。当需要存储Java对象时，手动完成对象的序列化和反序列化。因为存入和读取时的序列化及反序列化都是我们自己实现的，SpringDataRedis就不会将class信息写入Redis了
>
> 3、因此SpringDataRedis就提供了`RedisTemplate`的子类：`StringRedisTemplate`，它的key和value的序列化方式默认就是String方式。

```java
@SpringBootTest
class Redis02SpringbootDataRedisApplicationTests {

    @Autowired
    StringRedisTemplate stringRedisTemplate;

    @Autowired
    // JSON序列化工具
    private static final ObjectMapper mapper = new ObjectMapper();

    @Test
    void testString() {
        ValueOperations<String, String> ops = stringRedisTemplate.opsForValue();
        ops.set("name", "aaaaaaaa");
        Object name = ops.get("name");
        System.out.println(name);
    }

    @Test
    void testObject() throws JsonProcessingException {
        ValueOperations<String, String> ops = stringRedisTemplate.opsForValue();
        User user = new User("张三", 20);
        // 手动序列化
        String s = mapper.writeValueAsString(user);
        ops.set("user:200", s);

        String s1 = ops.get("user:200");
        // 手动反序列化
        User user1 = mapper.readValue(s1, User.class);
        System.out.println(user1); // User(name=张三, age=20)
    }

}
```





**操作hash类型数据**

```java
@Test
void testHash(){
    HashOperations<String, Object, Object> ops = stringRedisTemplate.opsForHash();
    ops.put("user:300", "name", "xrj");
    ops.put("user:300", "age", "20");
    Object name = ops.get("user:300", "name");
    System.out.println(name);
    Object age = ops.get("user:300", "age");
    System.out.println(age);
}
```







## 四、Redis实战

### 1、短信登录

手机或者app端发起请求，请求我们的nginx服务器，nginx基于七层模型走的是HTTP协议，可以实现基于Lua直接绕开tomcat访问redis，也可以作为静态资源服务器，轻松扛下上万并发， 负载均衡到下游tomcat服务器，打散流量，我们都知道一台4核8G的tomcat，在优化和处理简单业务的加持下，大不了就处理1000左右的并发， 经过nginx的负载均衡分流后，利用集群支撑起整个项目，同时nginx在部署了前端项目后，更是可以做到动静分离，进一步降低tomcat服务的压力，这些功能都得靠nginx起作用，所以nginx是整个项目中重要的一环。

在tomcat支撑起并发流量后，我们如果让tomcat直接去访问Mysql，根据经验Mysql企业级服务器只要上点并发，一般是16或32 核心cpu，32 或64G内存，像企业级mysql加上固态硬盘能够支撑的并发，大概就是4000起~7000左右，上万并发， 瞬间就会让Mysql服务器的cpu，硬盘全部打满，容易崩溃，所以我们在高并发场景下，会选择使用mysql集群，同时为了进一步降低Mysql的压力，同时增加访问的性能，我们也会加入Redis，同时使用Redis集群使得Redis对外提供更好的服务。



> 1、这里我们前端放在nginx服务器上，后端放在tomcat上。
>
> 2、我们后端项目使用的是8081端口，前端nginx使用的是8080端口，我们访问nginx服务器，nginx会将请求负载到8081端口
>
> 3、当我们启动SpringBoot项目，同时启动nginx服务，`start nginx.exe`，通过访问`http://localhost:8080`就可以访问项目了

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309271718780.png" style="zoom: 80%;" />

#### 1.1 基于Session实现

> 以下是通过Session实现验证码登录的流程



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309271721634.png)





**1、发送验证码：**

> 用户在提交手机号后，会校验手机号是否合法，如果不合法，则要求用户重新输入手机号
>
> 如果手机号合法，后台此时生成对应的验证码，同时将验证码进行保存，然后再通过短信的方式将验证码发送给用户

**Controller**

```java
@PostMapping("code")
public Result sendCode(@RequestParam("phone") String phone, HttpSession session) {
    // 发送短信验证码并保存验证码
    return userService.sendCode(phone, session);
}
```

**Service**

```java
@Override
public Result sendCode(String phone, HttpSession session) {
    // 1.校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果没通过，返回错误信息
        return Result.fail("手机号不正确");
    }

    // 3.生成验证码, 我们导入的hutool-all依赖中有许多工具类，RandomUtil用来随机生成
    String code = RandomUtil.randomNumbers(6);

    // 4.保存到session
    session.setAttribute("code", code);

    // 5.这里模拟验证码的发送
    log.info("验证码发送成功，验证码为{}", code);
    return Result.ok();
}
```





**2、短信验证码登录、注册：**

> 1、用户将验证码和手机号进行输入，后台从session中拿到当前验证码，然后和用户输入的验证码进行校验，如果不一致，则无法通过校验，如果一致，则后台根据手机号查询用户，如果用户不存在，则为用户创建账号信息，保存到数据库，无论是否存在，都会将用户信息保存到session中，方便后续获得当前登录信息。
>
> 2、这里有个小bug，这里没有验证手机号是否是之前发验证码的手机号，后边通过redis可以解决

**Controller**

```java
@PostMapping("/login")
public Result login(@RequestBody LoginFormDTO loginForm, HttpSession session){
    // 实现登录功能
    return userService.login(loginForm, session);
}
```

```java
@GetMapping("/me")
public Result me(){
    // 获取当前登录的用户并返回
    User user = UserHolder.getUser();
    return Result.ok(user);
}
```

**Service**

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    String phone = loginForm.getPhone();
    // 1.验证手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 如果没通过，返回错误信息
        return Result.fail("手机号不正确");
    }

    // 2.检验验证码
    String cacheCode = (String) session.getAttribute("code");
    String code = loginForm.getCode();
    if (cacheCode == null || !cacheCode.equals(code)) {
        // 3.验证码不正确返回错误
        return Result.fail("验证码不正确");
    }

    // 4.根据手机号查询用户, 这里使用MyBatisPlus
    User user = query().eq("phone", phone).one();

    if (user == null) {
        // 5.用户为空，创建用户
        user = createUserByPhone(phone);
    }

    // 6.将用户保存到session
    session.setAttribute("user", user);
    return Result.ok();
}

private User createUserByPhone(String phone) {
    User user = new User();
    user.setPhone(phone);
    user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX + RandomUtil.randomString(6));
    save(user);
    return user;
}
```





**3、校验登录状态:**

> 1、用户在请求时候，会从cookie中携带着`JsessionId`到后台，后台通过`JsessionId`从session中拿到用户信息，如果没有session信息，则进行拦截，如果有session信息，则将用户信息保存到`ThreadLocal`中，并且放行
>
> 2、我们在拦截器中检查用户是否登录，如果登录了，就将用户信息保存在`ThreadLocal`中，这样，后续请求使用用户信息就不需要通过session获取，可以直接从`ThreadLocal`中获取，请求结束，即页面渲染完成，我们将用户信息从`ThreadLocal`删除

**Interceptor**

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        HttpSession session = request.getSession();
        User user = (User) session.getAttribute("user");
        if (user == null) {
            // 返回一个未授权状态码，并拦截该请求
            response.setStatus(401);
            return false;
        }

        // 将用户信息保存在Threadlocal中，这样拦截器放行后，后边的请求可以直接从Threadlocal中拿到用户信息，而不需要重新通过session查询
        UserHolder.saveUser(user);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```

**配置拦截器**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor()).excludePathPatterns(
                "/user/code",
                "/user/login",
                "/blog/hot",
                "/shop/**",
                "/shop-type/**",
                "/upload/**",
                "/voucher/**"
        );
    }
}
```







**tomcat的运行原理**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309271734062.png" style="zoom:80%;" />

1、当用户发起请求时，会访问我们向tomcat注册的端口，任何程序想要运行，都需要有一个线程对当前端口号进行监听，tomcat也不例外，当监听线程知道用户想要和tomcat连接时，会由监听线程创建socket连接，socket都是成对出现的，用户通过socket互相传递数据

2、当tomcat端的socket接受到数据后，此时监听线程会从tomcat的线程池中取出一个线程执行用户请求，在我们的服务部署到tomcat后，线程会找到用户想要访问的工程，然后用这个线程转发到工程中的controller，service，dao中，并且访问对应的DB，在用户执行完请求后，再统一返回，再找到tomcat端的socket，再将数据写回到用户端的socket，完成请求和响应

3、通过以上我们可以得知 每个请求都是去找tomcat线程池中的一个线程来完成工作的， 使用完成后再进行回收，既然每个请求都是独立的，所以在每个用户去访问我们的工程时，我们可以使用`threadlocal`来做到线程隔离，每个线程操作自己的一份数据。





**4、隐藏用户敏感信息**

> 我们将用户信息保存在session后，此时保存的是全部信息，包括手机号、密码等，这样不安全，同时也不需要这么多信息。
>
> 所以我们应当在返回用户信息之前，将用户的敏感信息进行隐藏，使用一个`UserDto`对象，这个`UserDto`对象就没有敏感信息了，我们在返回前，将有用户敏感信息的User对象转化成没有敏感信息的UserDto对象，那么就能够避免这个问题了

**login**

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    String phone = loginForm.getPhone();
    // 1.验证手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 如果没通过，返回错误信息
        return Result.fail("手机号不正确");
    }

    // 2.检验验证码
    String cacheCode = (String) session.getAttribute("code");
    String code = loginForm.getCode();
    if (cacheCode == null || !cacheCode.equals(code)) {
        // 3.验证码不正确返回错误
        return Result.fail("验证码不正确");
    }

    // 4.根据手机号查询用户, 这里使用MyBatisPlus
    User user = query().eq("phone", phone).one();

    if (user == null) {
        // 5.用户为空，创建用户
        user = createUserByPhone(phone);
    }

    // 6.将用户保存到session, 此时我们不需要存储全部信息，只需要存储id，nikeName, icon即可
    // 通过hutool-all中的工具类实现Bean对象的复制
    session.setAttribute("user", BeanUtil.copyProperties(user, UserDTO.class));
    return Result.ok();
}
```

**interceptor**

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

    HttpSession session = request.getSession();
    UserDTO user = (UserDTO) session.getAttribute("user");
    if (user == null) {
        // 返回一个未授权状态码，并拦截该请求
        response.setStatus(401);
        return false;
    }

    // 将用户信息保存在Threadlocal中，这样拦截器放行后，后边的请求可以直接从Threadlocal中拿到用户信息，而不需要重新通过session查询
    UserHolder.saveUser(user);
    return true;
}
```







#### 1.2 Session共享问题

每个tomcat中都有一份属于自己的session，假设用户第一次访问第一台tomcat，并且把自己的信息存放到第一台服务器的session中，但是第二次这个用户访问到了第二台tomcat，那么在第二台服务器上，肯定没有第一台服务器存放的session，所以此时 整个登录拦截功能就会出现问题，我们能如何解决这个问题呢？早期的方案是session拷贝，就是说虽然每个tomcat上都有不同的session，但是每当任意一台服务器的session修改时，都会同步给其他的Tomcat服务器session，这样的话，就可以实现session的共享了

但是这种方案具有两个大问题

1、每台服务器中都有完整的一份session数据，服务器压力过大。

2、session拷贝数据时，可能会出现延迟



所以在集群环境下，我们就使用redis来完成，我们把session换成redis，redis数据本身就是共享的，就可以避免session共享的问题了

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309271746574.png" style="zoom:80%;" />





#### 1.3 Redis代替session的流程

**1、保存验证码**

> 1、使用session时，我们需要保存验证码到session中，其中`key`用code即可，每一个用户的sessionId都不同，所以`key`使用相同的也没问题
>
> 2、但是现在我们需要将验证码保存到redis中，所有用户都是用的这一个redis，如果`key`相同就会造成覆盖。因此对于保存到redis中的`key`我们可以使用手机号，因为手机号是不会重复的。`value`使用string结构即可
>
> 3、同时，我们在进行登录校验时，通过手机号从redis中拿到验证码信息，如果发送验证码的手机号和当前登录的手机号不同，是无法获取验证码信息的，因此使用redis就可以解决上边这个问题了。



**2、保存用户信息**

> 1、使用session时，我们在完成登录之后，需要将用户信息保存到session中，和保存验证码一样，所有用户保存在session中的key相同是没有问题的，但是保存在redis中就不行了，因此这里我们使用token作为`key`。
>
> 2、将用户信息保存到redis中，`key`使用后台随机生成的token，`value`可以使用string，也可以使用hash结构，使用hash结构可以便于我们对每个数据进行crud。
>
> 3、在将用户信息保存到redis时，我们随机生成的token要返回给前端，然后前端发送任何请求时都需要带着这个token去访问，这样请求在经过拦截器时，才可以通过token获取到用户信息



**login的ajax请求**

> 向`/user/login`发送完请求后，通过data可以获取到后端返回的信息，然后将获取到的token信息保存sessionStorage中

```js
login(){
    if(!this.radio){
    this.$message.error("请先确认阅读用户协议！");
    return
    }
    if(!this.form.phone || !this.form.code){
    this.$message.error("手机号和验证码不能为空！");
    return
    }
    axios.post("/user/login", this.form)
    .then(({data}) => {
    if(data){
    // 保存用户信息到session
    sessionStorage.setItem("token", data);
    }
    // 跳转到首页
    location.href = "/info.html"
    })
    .catch(err => this.$message.error(err))
    },
```

**request拦截器**

> 在发送每一个请求之前，我们都将token信息携带着，这样就可以通过token去redis中获取响应数据了

```js
// request拦截器，将用户token放入头中
let token = sessionStorage.getItem("token");
axios.interceptors.request.use(
  config => {
    if(token) config.headers['authorization'] = token
    return config
  },
  error => {
    console.log(error)
    return Promise.reject(error)
  }
)
```



**Redis短信登录流程**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309271800422.png" style="zoom:80%;" />







#### 1.4 基于Redis实现

**将验证码保存到redis**

> 生成验证码后，我们将验证码保存到redis中，key使用一个前缀加手机号，并设置有效期为2分钟

```java
@Override
public Result sendCode(String phone, HttpSession session) {
    // 1.校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果没通过，返回错误信息
        return Result.fail("手机号不正确");
    }

    // 3.生成验证码, 我们导入的hutool-all依赖中有许多工具类，RandomUtil用来随机生成
    String code = RandomUtil.randomNumbers(6);

    // 4.保存到redis,有效期2分钟
    stringRedisTemplate.opsForValue()
                    .set(RedisConstants.LOGIN_CODE_KEY + phone, code, RedisConstants.LOGIN_CODE_TTL, TimeUnit.MINUTES);

    // 5.这里模拟验证码的发送
    log.info("验证码发送成功，验证码为{}", code);
    return Result.ok();
}
```



**将用户信息保存到redis**

> 1、从redis中通过手机号获取验证码
>
> 2、将用户信息保存到redis中，其中key使用随机生成的token，value使用的是hash结构，因此我们需要将用户信息转为Map然后存入redis。需要注意的是，因为我们使用的`stringRedisTemplate`，他的key和value都必须是String才可以，但是user的id为Long类型，所以必须将他们转为String再保存
>
> 3、将token返回给前端

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    String phone = loginForm.getPhone();
    // 1.验证手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 如果没通过，返回错误信息
        return Result.fail("手机号不正确");
    }

    // 2.检验验证码
    String cacheCode = stringRedisTemplate.opsForValue().get(RedisConstants.LOGIN_CODE_KEY + phone);
    String code = loginForm.getCode();
    if (cacheCode == null || !cacheCode.equals(code)) {
        // 3.验证码不正确返回错误
        return Result.fail("验证码不正确");
    }

    // 4.根据手机号查询用户, 这里使用MyBatisPlus
    User user = query().eq("phone", phone).one();

    if (user == null) {
        // 5.用户为空，创建用户
        user = createUserByPhone(phone);
    }

    // 6.将用户保存到redis
    // 6.1 key使用一个随机的token
    String token = UUID.randomUUID().toString(true);
    // 6.2 隐藏用户敏感信息
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    // 6.3 将userDTO转为Map，因为是stringRedisTemplate，所以字段都必须是String
    Map<String, Object> userMap = BeanUtil.beanToMap(userDTO, new HashMap<>(), CopyOptions.create()
                                                     .setIgnoreNullValue(true).setFieldValueEditor((filename, fileValue) -> fileValue.toString()));
    // 6.4 将用户信息保存到redis
    stringRedisTemplate.opsForHash().putAll(RedisConstants.LOGIN_USER_KEY + token, userMap);

    // 7.返回token
    return Result.ok(token);
}
```



**拦截器**

> 1、从请求头中获取token信息
>
> 2、通过拿到的token信息，去redis中获取用户信息。拿到的是一个Map，将Map转为UserDTO对象，然后存储到ThreadLocal中
>
> 3、刷新token的有效期

```java
public class LoginInterceptor implements HandlerInterceptor {

    private StringRedisTemplate stringRedisTemplate;

    public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            // 返回一个未授权状态码，并拦截该请求
            response.setStatus(401);
            return false;
        }
        // 通过token获取user
        String key  = RedisConstants.LOGIN_USER_KEY + token;
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(key);
        if (userMap.isEmpty()) {
            // 返回一个未授权状态码，并拦截该请求
            response.setStatus(401);
            return false;
        }

        // 将Map转为UserDTO对象
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);

        // 将用户信息保存在Threadlocal中，这样拦截器放行后，后边的请求可以直接从Threadlocal中拿到用户信息，而不需要重新通过session查询
        UserHolder.saveUser(userDTO);

        // 刷新token有效期
        stringRedisTemplate.expire(token, RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```







#### 1.5 优化

> 1、当前我们设置的拦截器，会在每次请求到达的时候拦截，并在Redis中刷新用户的有限期。但是如果用户登录后，一直访问的页面都是不经过拦截器拦截的，那么有效期到了后，用户登录的信息就过期了。
>
> 2、为了解决这个问题，我们再设置一个拦截器，这个拦截器专门用于刷新Redis中用户登录信息的有效期。
>
> 3、我们在设置一个`RefreshRedisInterceptor`，这个拦截器拦截所有请求，在这个拦截器中，我们去Redis中获取用户登录信息，如果获取到了，就保存到ThreadLocal中，然后放行。如果获取不到，直接放行。
>
> 4、在`LoginInterceptor`拦截器中，我们直接从ThreadLocal中获取用户登录信息，如果获取到了就放行，获取不到就拦截。



**RefreshRedisInterceptor**

```java
public class RefreshRedisInterceptor implements HandlerInterceptor {

    private StringRedisTemplate stringRedisTemplate;

    public RefreshRedisInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            return true;
        }
        // 通过token获取user
        String key  = RedisConstants.LOGIN_USER_KEY + token;
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(key);
        if (userMap.isEmpty()) {
            return true;
        }

        // 将Map转为UserDTO对象
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);

        // 将用户信息保存在Threadlocal中，这样拦截器放行后，后边的请求可以直接从Threadlocal中拿到用户信息，而不需要重新通过session查询
        UserHolder.saveUser(userDTO);

        // 刷新token有效期
        stringRedisTemplate.expire(token, RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);
        return true;
    }
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```



**LoginInterceptor**

```java
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 通过ThreadLocal获取user对象
        if (UserHolder.getUser() == null) {
            response.setStatus(401);
            return false;
        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```



**配置类**

> `RefreshRedisInterceptor`拦截器的优先级比`LoginInterceptor`高，并且拦截所有请求。

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor()).excludePathPatterns(
                "/user/code",
                "/user/login",
                "/blog/hot",
                "/shop/**",
                "/shop-type/**",
                "/upload/**",
                "/voucher/**"
        ).order(1);
        registry.addInterceptor(new RefreshRedisInterceptor(stringRedisTemplate)).order(0);
    }
```









### 2、缓存

> 缓存数据存储于代码中，而代码运行在内存中，内存的读写性能远高于磁盘，缓存可以大大降低**用户访问并发量带来的**服务器读写压力
>



**缓存的作用**

- 降低后端负载
- 提高读写效率，降低响应时间

**缓存的成本**

- 数据一致性成本
- 代码维护成本
- 运维成本





#### 2.1 添加商户缓存

> 1、在我们查询商户信息时，我们是直接操作从数据库中去进行查询的，这样查询比较慢，所以我们需要增加缓存
>
> 2、标准的操作方式就是查询数据库之前先查询缓存，如果缓存数据存在，则直接从缓存中返回，如果缓存数据不存在，再查询数据库，然后将数据存入redis。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309301810628.png)





**Controller**

```java
@GetMapping("/{id}")
public Result queryShopById(@PathVariable("id") Long id) {
    return Result.ok(shopService.queryById(id));
}
```



**Service**

```java
@Override
public Object queryById(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    // 1.通过redis查询商铺信息
    String shopJson = stringRedisTemplate.opsForValue().get(key);

    // 2.如果在redis中查询到了直接返回
    if (StrUtil.isNotBlank(shopJson)) {
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    }

    // 3.去数据库中查询
    Shop shop = getById(id);

    // 4.数据库没查到，返回
    if (shop == null) {
        return Result.fail("该商铺不存在");
    }

    // 5.将商铺信息保存到redis
    stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop));

    // 6.返回商铺信息
    return Result.ok(shop);
}
```





#### 2.2 添加商铺类型缓存

> 首页上展示了各种类型的商铺，这些数据一般是不会更新的，所以我们也对它多一个缓存策略
>
> 这里我们使用的是List的数据类型



**Controller**

```java
@GetMapping("list")
public Result queryTypeList() {
    return typeService.queryList();
}
```



**Service**

```java
@Override
public Result queryList() {
    // 1.从redis中查询商铺类型
    List<String> shopTypeListJson = stringRedisTemplate.opsForList().range(RedisConstants.CACHE_SHOP_LIST_KEY, 0, -1);

    // 2.在redis查询到了后直接返回
    if (!shopTypeListJson.isEmpty()) {
        List<ShopType> shopTypeList = new ArrayList<>();
        for (String shopTypeJson : shopTypeListJson) {
            ShopType shopType = JSONUtil.toBean(shopTypeJson, ShopType.class);
            shopTypeList.add(shopType);
        }
        return Result.ok(shopTypeList);
    }

    // 3.去数据库查询
    List<ShopType> shopTypeList = query().orderByAsc("sort").list();

    // 4.如果不存在，返回错误
    if (shopTypeList.isEmpty()) {
        return Result.fail("商铺类型不存在");
    }

    // 5.写入redis
    for (ShopType shopType : shopTypeList) {
        String jsonStr = JSONUtil.toJsonStr(shopType);
        stringRedisTemplate.opsForList().rightPush(RedisConstants.CACHE_SHOP_LIST_KEY, jsonStr);
    }

    // 6.返回数据
    return Result.ok(shopTypeList);
}
```







#### 2.3 缓存更新策略

> 缓存更新是redis为了节约内存而设计出来，主要是因为内存数据宝贵，当我们向redis插入太多数据，此时就可能会导致缓存中的数据过多，所以redis会对部分数据进行更新
>

**内存淘汰：**redis自动进行，当redis内存达到我们设定的`max-memery`的时候，会自动触发淘汰机制，淘汰掉一些不重要的数据(可以自己设置策略方式)

**超时剔除：**当我们给redis设置了过期时间`ttl`之后，redis会将超时的数据进行删除，方便我们继续使用缓存

**主动更新：**我们可以手动调用方法把缓存删掉，通常用于解决缓存和数据库不一致问题

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309301816341.png)



**数据库缓存不一致的问题**

> 由于我们**缓存的数据来自于数据库**，而数据库的**数据是会发生变化的**。因此，如果当数据库中**数据发生变化，而缓存却没有同步**，此时就会有**一致性问题存在**

有如下几种方案：

- `Cache Aside Pattern` 人工编码方式：缓存调用者在更新完数据库后再去更新缓存，也称之为双写方案

- `Read/Write Through Pattern` : 由系统本身完成，数据库与缓存的问题交由系统本身去处理

- `Write Behind Caching Pattern `：调用者只操作缓存，其他线程去异步处理数据库，实现最终一致



我们一般采用第一个方案，因为方案2和方案3需要我们定制相应的系统，比较麻烦

对于方案一，有三个问题需要解决：

- 删除缓存还是更新缓存？

  * 更新缓存：每次更新数据库都更新缓存，无效写操作较多
  * 删除缓存：更新数据库时让缓存失效，查询时再更新缓存【推荐】

- 如何保证缓存与数据库的操作的同时成功或失败？

  * 单体系统，将缓存与数据库操作放在一个事务
  * 分布式系统，利用TCC等分布式事务方案

- 先操作缓存还是先操作数据库

  - 先删除缓存，再操作数据库

    > 1、如果线程1执行更新操作，那么他先删除缓存，然后再更新数据库。之后，线程2查询数据时，去数据库查询，并将数据写到redis中，这样没有任何问题
    >
    > 2、如果线程1删除缓存后，还没来得及更新数据库，因为更新数据库涉及读写操作，比较慢。此时线程2查询数据，去数据库查询完后，写入redis，然后线程1去更新数据库，这时就造成了数据库和缓存数据不一致的情况

  - 先操作数据库，再删除缓存【推荐】

    > 1、线程1先更新数据库，然后去删除缓存。之后线程2查询数据时，去数据库查询，并写入redis，这样操作没有问题
    >
    > 2、如果出现了缓存失效的情况，在线程2查询数据时，直接去数据库查询，然后还没有写入redis时，线程1去修改数据库，然后删除缓存。之后线程2再次将数据写入redis，这样就造成了数据不一致的情况。但是这样的概率很低，因为写redis的操作速度很快，而修改数据库的操作很慢，所以这种情况基本不会出现。





#### 2.4 实现商铺数据库缓存一致

> 1、查询商铺：先去redis中查询，如果查询到了直接返回。如果查询不到就去数据库查询，然后将数据写入redis，同时设置超时时间
>
> 2、修改商铺：先修改数据库，然后删除缓存



**查询商铺**

> 将数据写入redis后设置超时时间

```java
@Override
public Object queryById(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    // 1.通过redis查询商铺信息
    String shopJson = stringRedisTemplate.opsForValue().get(key);

    // 2.如果在redis中查询到了直接返回
    if (StrUtil.isNotBlank(shopJson)) {
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    }

    // 3.去数据库中查询
    Shop shop = getById(id);

    // 4.数据库没查到，返回
    if (shop == null) {
        return Result.fail("该商铺不存在");
    }

    // 5.将商铺信息保存到redis, 并设置超时时间
    stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    // 6.返回商铺信息
    return Result.ok(shop);
}
```





**修改商铺**

> 先修改数据库，然后删除缓存
>
> 这里通过postman发请求来操作

```java
@Override
@Transactional
public Result update(Shop shop) {
    Long id = shop.getId();
    if (id == null) {
        return Result.fail("商铺id不能为空");
    }
    // 1.更新数据库
    updateById(shop);

    // 2.删除缓存
    stringRedisTemplate.delete(RedisConstants.CACHE_SHOP_KEY + id);
    return Result.ok();
}
```







#### 2.5 缓存穿透

> 缓存穿透是指客户端请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库。
>

常见的解决方案有两种：

* 缓存空对象

  * 优点：实现简单，维护方便
  * 缺点：
    * 额外的内存消耗
    * 可能造成短期的不一致

  > 当我们客户端访问不存在的数据时，先请求redis，但是此时redis中没有数据，此时会访问到数据库，但是数据库中也没有数据，这个数据穿透了缓存，直击数据库，此时我们将一个空值缓存到redis中，这样，下次用户过来访问这个不存在的数据，那么在redis中也能找到这个数据就不会去数据库查询了

* 布隆过滤

  * 优点：内存占用较少，没有多余key
  * 缺点：
    * 实现复杂
    * 存在误判可能

  > 布隆过滤器采用的是哈希思想来解决这个问题，通过一个庞大的二进制数组，通过哈希去判断当前这个要查询的这个数据是否存在，如果布隆过滤器判断存在，则放行，这个请求会去访问redis，哪怕此时redis中的数据过期了，但是数据库中一定存在这个数据，在数据库中查询出来这个数据后，再将其放入到redis中，假设布隆过滤器判断这个数据不存在，则直接返回。但是存在误判的可能：基于哈希思想，就可能存在哈希冲突





**布隆过滤器**

1、布隆过滤器的主要原理是使用一组哈希函数，将元素进行哈希计算后映射到数组中的位置，将该位置置为1。当要检查一个元素是否在集合中时，将该元素进行哈希处理，然后查看哈希值在数组中对应位置的值是否为1。如果哈希值对应的数组位置的值都为1，那么这个元素可能在集合中，否则这个元素肯定不在集合中。

2、由于会出现哈希冲突，因此会使用多个哈希函数，将元素通过多个哈希计算后，在数组对应位置赋值为1，这样查询某个元素时，只有经过所有哈希函数计算后的位置都为1，那么这个元素存在，否则就是不存在，使用多个哈希函数后，即使一个哈希函数产生哈希碰撞，还需要经过其他哈希函数的判断，降低了误判率，但是误判率越低，意味着使用的hash函数越多，数组长度越大

3、由于布隆过滤器的设计原理，无法精确判断某个元素是否在集合中，只能判断它可能存在或一定不存在

4、谷歌的guava框架实现好了布隆过滤器，同时Redisson通过BitMap也实现了布隆过滤器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311191606043.jpg)







#### 2.6 解决店铺查询存在的缓存穿透问题

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310061706318.png" style="zoom:60%;" />



> 1、在redis中查询数据后通过`StrUtil.isNotBlank(shopJson)`判断是否为空，该方法只有在字符串不为空才返回`true`。因此如果是空字符串，会返回`false`，我们就继续判断返回的字符串是否为`null`即可，如果不是`null`，那么就是空字符串，此时我们就返回错误信息。
>
> 2、在数据库中也没查到信息，我们往redis中写入一个空值

```java
@Override
public Object queryById(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    // 1.通过redis查询商铺信息
    String shopJson = stringRedisTemplate.opsForValue().get(key);

    // 2.如果在redis中查询到了直接返回
    if (StrUtil.isNotBlank(shopJson)) {
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    }

    // 如果查询到的是空值
    if (shopJson != null) {
        return Result.fail("该商铺不存在");
    }

    // 3.去数据库中查询
    Shop shop = getById(id);

    // 4.数据库没查到，返回
    if (shop == null) {
        // 将空值写入redis
        stringRedisTemplate.opsForValue().set(key, "", RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);
        // 返回错误信息
        return Result.fail("该商铺不存在");
    }

    // 5.将商铺信息保存到redis, 并设置超时时间
    stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    // 6.返回商铺信息
    return Result.ok(shop);
}
```









#### 2.7 缓存雪崩

> 缓存雪崩是指在同一时段大量的缓存key同时失效或者Redis服务宕机，导致大量请求到达数据库，带来巨大压力。
>

解决方案：

* 给不同的Key的TTL添加随机值
* 利用Redis集群提高服务的可用性
* 给缓存业务添加降级限流策略
* 给业务添加多级缓存

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310061800310.png" style="zoom:80%;" />











#### 2.8 缓存击穿

> 缓存击穿问题也叫热点Key问题，就是一个被高并发访问并且缓存重建业务较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击。
>
> 逻辑分析：假设线程1在查询缓存之后，本来应该去查询数据库，然后把这个数据重新加载到缓存的，此时只要线程1走完这个逻辑，其他线程就都能从缓存中加载这些数据了，但是假设在线程1没有走完的时候，后续的线程2，线程3，线程4同时过来访问当前这个方法， 那么这些线程都不能从缓存中查询到数据，那么他们就会同一时刻来访问查询缓存，都没查到，接着同一时间去访问数据库，同时的去执行数据库代码，对数据库访问压力过大

常见的解决方案有两种：

* **互斥锁**

  假设现在线程1过来访问，他查询缓存没有命中，但是此时他得到了锁的资源，那么线程1就会一个人去执行逻辑，假设现在线程2过来，线程2在执行过程中，并没有获得到锁，那么线程2就可以进行到休眠，直到线程1把锁释放后，线程2获得到锁，然后再来执行逻辑，此时就能够从缓存中拿到数据了。

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310061804509.png" style="zoom:80%;" />

* **逻辑过期**

  方案分析：我们之所以会出现这个缓存击穿问题，主要原因是在于我们对key设置了过期时间，假设我们不设置过期时间，其实就不会有缓存击穿的问题，但是不设置过期时间，这样数据就一直占用我们的内存，我们可以采用逻辑过期方案。

  我们把过期时间设置在 redis的value中，这个过期时间并不会直接作用于redis，而是我们后续通过逻辑去处理。假设线程1去查询缓存，然后从value中判断出来当前的数据已经过期了，此时线程1去获得互斥锁，那么其他线程会进行阻塞，获得了锁的线程他会开启一个 线程去进行 以前的重构数据的逻辑，直到新开的线程完成这个逻辑后，才释放锁， 而线程1直接返回旧数据，假设现在线程3过来访问，由于线程线程2持有着锁，所以线程3无法获得锁，线程3也直接返回旧数据，只有等到新开的线程2把重建数据构建完后，其他线程才能走返回正确的数据。

  这种方案缺点在于在构建完缓存之前，返回的都是脏数据。

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310061806861.png" style="zoom:67%;" />





| 解决方案 | 优点                                                    | 缺点                                                  |
| -------- | ------------------------------------------------------- | ----------------------------------------------------- |
| 互斥锁   | 1、没有额外的内存消耗 <br>2、保证一致性 <br>3、实现简单 | 1、线程需要等待，性能受影响<br>2、可能有死锁风险      |
| 逻辑过期 | 线程无需等待，性能较好                                  | 1、不能保证一致性<br>2、有额外内存消耗<br>3、实现复杂 |







#### 2.9 互斥锁

> 1、相较于原来从缓存中查询不到数据后直接查询数据库而言，现在的方案是进行查询之后，如果从缓存没有查询到数据，则进行互斥锁的获取，获取互斥锁后，判断是否获得到了锁，如果没有获得到，则休眠，过一会再进行尝试，直到获取到锁为止，才能进行查询。如果线程获取到了锁，就去数据库查询，查询后将数据写入redis，再释放锁，返回数据，利用互斥锁就能保证只有一个线程去执行操作数据库的逻辑，防止缓存击穿
>
> 2、这里互斥锁我们使用redis中提供的`setnx`功能，即redis中不存在这个key，我们才去添加。获取锁就判断我们能否通过`setnx`设置key，释放锁就是将这个key删除

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310061817080.png" style="zoom:50%;" />





**获取锁和释放锁**

```java
public boolean tryLock(String key) {
    Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
    return BooleanUtil.isTrue(flag);
}

public void unLock(String key) {
    stringRedisTemplate.delete(key);
}
```



**使用互斥锁查询**

```java
public Shop queryWithMutex(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    // 1.通过redis查询商铺信息
    String shopJson = stringRedisTemplate.opsForValue().get(key);

    // 2.如果在redis中查询到了直接返回
    if (StrUtil.isNotBlank(shopJson)) {
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return shop;
    }

    // 如果查询到的是空值
    if (shopJson != null) {
        return null;
    }

    // 3.redis中未命中，尝试获取互斥锁
    String lockKey = "lock:shop" + id;
    Shop shop = null;
    try {
        if (!tryLock(lockKey)) {
            // 3.1获取锁失败, 休眠一段时间
            Thread.sleep(50);
            // 休眠后重新查询
            return queryWithMutex(id);
        }

        // 模拟数据库查询速度慢
        Thread.sleep(10000);
        // 3.2获取锁成功，去数据库中查询
        shop = getById(id);

        // 4.数据库没查到，返回
        if (shop == null) {
            // 将空值写入redis
            stringRedisTemplate.opsForValue().set(key, "", RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);
            // 返回错误信息
            return null;
        }

        // 5.将商铺信息保存到redis, 并设置超时时间
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    } finally {
        // 6.释放互斥锁
        unLock(lockKey);
    }

    // 7.返回商铺信息
    return shop;
}
```









#### 2.10 逻辑过期

> 当用户开始查询redis时，判断是否命中，如果没有命中则直接返回空数据，不查询数据库。而一旦命中后，将value取出，判断value中的过期时间是否满足，如果没有过期，则直接返回redis中的数据，如果过期，也返回之前的数据，然后开启一个新的线程去重构数据，重构完成后释放互斥锁。
>

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310061903607.png" style="zoom:60%;" />





现在向redis中存储的数据不仅需要包含店铺信息，同时应该包含一个过期时间，但是当前店铺信息类中没有这个数据，因此我们重构一个类，其中包含店铺信息以及过期时间

```java
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}
```



**向redis写数据**

```java
public void saveShop2Redis(long id, long expireSecond) {
    // 1.查询店铺信息
    Shop shop = getById(id);

    // 2.构建RedisDate数据
    RedisData<Shop> redisData = new RedisData();
    redisData.setData(shop);
    redisData.setExpireTime(LocalDateTime.now().plusSeconds(expireSecond));

    // 3.将数据保存到redis
    stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id, JSONUtil.toJsonStr(redisData));
}
```



**查询操作**

> 对于这种热点Key，我们一开始就在redis存储了，且没有设置过期时间，如果在redis中没有查到数据，说明查询的不是我们希望查询的，就直接返回空

```java
// 线程池
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

public Shop queryWithLogicalExpire(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    // 1.通过redis查询商铺信息
    String shopJson = stringRedisTemplate.opsForValue().get(key);

    // 2.如果在redis中没有查询到返回空
    if (StrUtil.isBlank(shopJson)) {
        return null;
    }

    // 3.在redis中查询到数据,将数据进行反序列化
    RedisData redisData = JSONUtil.toBean(shopJson, RedisData.class);
    Shop shop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);
    LocalDateTime expireTime = redisData.getExpireTime();

    // 4.判断是否过期
    if (expireTime.isAfter(LocalDateTime.now())) {
        // 4.1未过期，直接返回数据
        return shop;
    }

    // 4.2 数据过期，获取互斥锁
    String lockKey = RedisConstants.LOCK_SHOP_KEY + id;
    if (tryLock(lockKey)) {
        try {
            // 模拟数据库操作
            Thread.sleep(10000);
            // 4.1.3获取互斥锁成功，用一个新的线程去查询数据并写入redis
            CACHE_REBUILD_EXECUTOR.submit(() -> {
                this.saveShop2Redis(id, RedisConstants.LOCK_SHOP_TTL);
            });
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            unLock(lockKey);
        }
    }

    // 4.3.未获取到锁或者获取锁失败，返回旧数据
    return shop;
}
```







#### 2.11 封装Redis工具类

基于`StringRedisTemplate`封装一个缓存工具类，满足下列需求：

* 方法1：将任意Java对象序列化为json并存储在string类型的key中，并且可以设置TTL过期时间
* 方法2：将任意Java对象序列化为json并存储在string类型的key中，并且可以设置逻辑过期时间，用于处理缓存击穿问题

* 方法3：根据指定的key查询缓存，并反序列化为指定类型，利用缓存空值的方式解决缓存穿透问题
* 方法4：根据指定的key查询缓存，并反序列化为指定类型，需要利用逻辑过期解决缓存击穿问题



**工具类**

```java
@Component
public class CacheClient {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

    public void set(String key, Object value, Long time, TimeUnit unit) {
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value), time, unit);
    }

    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit unit) {
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
        // 写入redis
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
    }

    public<T, ID> T queryWithNull(String keyPrefix, ID id, Class<T> type, Function<ID, T> dbFallback, Long time, TimeUnit unit){
        String key = keyPrefix + id;
        // 1.通过redis查询商铺信息
        String json = stringRedisTemplate.opsForValue().get(key);

        // 2.如果在redis中查询到了直接返回
        if (StrUtil.isNotBlank(json)) {
            T t = JSONUtil.toBean(json, type);
            return t;
        }

        // 如果查询到的是空值
        if (json != null) {
            return null;
        }

        // 3.去数据库中查询，因为这里使用的都是泛型，所以需要使用函数式编程
        T t = dbFallback.apply(id);

        // 4.数据库没查到，返回
        if (t == null) {
            // 将空值写入redis
            stringRedisTemplate.opsForValue().set(key, "", time, unit);
            // 返回错误信息
            return null;
        }

        // 5.将商铺信息保存到redis, 并设置超时时间
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(t), time, unit);

        // 6.返回商铺信息
        return t;
    }

    public<T, ID> T queryWithLogicalExpire(String keyPrefix, ID id, Class<T> type, Function<ID, T> dbFallback, Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        // 1.通过redis查询商铺信息
        String json = stringRedisTemplate.opsForValue().get(key);

        // 2.如果在redis中没有查询到返回空
        if (StrUtil.isBlank(json)) {
            return null;
        }

        // 3.在redis中查询到数据,将数据进行反序列化
        RedisData redisData = JSONUtil.toBean(json, RedisData.class);
        T t = JSONUtil.toBean((JSONObject) redisData.getData(), type);
        LocalDateTime expireTime = redisData.getExpireTime();

        // 4.判断是否过期
        if (expireTime.isAfter(LocalDateTime.now())) {
            // 4.1未过期，直接返回旧数据
            return t;
        }

        // 4.2 数据过期，获取互斥锁
        String lockKey = RedisConstants.LOCK_SHOP_KEY + id;
        if (tryLock(lockKey)) {
            try {
                // 模拟数据库操作
                Thread.sleep(10000);
                // 4.1.3获取互斥锁成功，
                // 查询数据库
                T newT = dbFallback.apply(id);
                // 写入redis
                this.setWithLogicalExpire(key, newT, time, unit);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                unLock(lockKey);
            }
        }

        // 4.3.未获取到锁或者获取锁失败，返回旧数据
        return t;
    }

    public boolean tryLock(String key) {
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return BooleanUtil.isTrue(flag);
    }

    public void unLock(String key) {
        stringRedisTemplate.delete(key);
    }
}
```



**service方法**

```java
@Override
public Result queryById(Long id) {
    // 解决缓存穿透
    // Shop shop = queryWithNull(id);
    // Shop shop = cacheClient.queryWithNull(RedisConstants.CACHE_SHOP_KEY, id,
    //         Shop.class, this::getById, RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    // 利用互斥锁解决缓存击穿
    // Shop shop = queryWithMutex(id);

    // 利用逻辑过期解决缓存击穿
    // Shop shop = queryWithLogicalExpire(id);
    Shop shop = cacheClient.queryWithLogicalExpire(RedisConstants.CACHE_SHOP_KEY, id,
            Shop.class, this::getById, RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    if (shop == null) {
        return Result.fail("商铺不存在");
    }
    return Result.ok(shop);
}
```







### 3、优惠券秒杀

#### 3.1 全局唯一ID

每个店铺都可以发布优惠券，当用户抢购时，就会生成订单并保存到`tb_voucher_order`这张表中，而订单表如果使用数据库自增ID就存在一些问题：

* id的规律性太明显

  > 如果我们的id具有太明显的规则，用户或者说商业对手很容易猜测出来我们的一些敏感信息，比如商城在一天时间内，卖出了多少单，这明显不合适。

* 受单表数据量的限制

  > 随着我们商城规模越来越大，mysql的单表的容量不宜超过500W，数据量过大之后，我们要进行拆库拆表，但拆分表了之后，他们从逻辑上讲他们是同一张表，所以他们的id是不能一样的， 于是乎我们需要保证id的唯一性。





**全局ID生成器**是一种在分布式系统下用来生成全局唯一ID的工具，一般要满足下列特性

- 唯一性
- 高可用
- 高性能
- 递增型
- 安全性



对于这些特性要求，我们可以使用Redis来实现，为了增加ID的安全性，我们可以不直接使用Redis自增的数值，而是拼接一些其它信息：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310071633843.png)

- 符号位永远为0，表示正数

- 时间戳：31bit，以秒为单位，可以使用69年，表示的是距离某个日期的秒数
- 序列号：32bit，计数器，可以产生2^32个不同id







#### 3.2 Redis实现全局唯一ID

> 对于Redis生成的序列号，我们每次调用他都会自增长1，但是序列号一共32位，最多2^32个序列号。因此存入Redis的key不能使用同一个，我们可以根据前缀+日期来作为key，这样生成的序列号既不会超范围，而且我们还可以根据日期统计每天、每月的销售量

```java
@Component
public class RedisIdWork {
    /**
     * 开始时间戳
     */
    private static final long BEGIN_TIMESTAMP = 1640995200L;
    /**
     * 序列号的位数
     */
    private static final int COUNT_BITS = 32;

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    public long nextId(String keyPrefix) {
        // 1.生成时间戳
        long nowTimestamp = LocalDateTime.now().toEpochSecond(ZoneOffset.UTC);
        long timestamp = nowTimestamp - BEGIN_TIMESTAMP;

        // 2.生成序列号
        // 2.1获取当天日期，精确到天
        String date = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        // 2.2自增长
        Long count = stringRedisTemplate.opsForValue().increment("incr:" + keyPrefix + ":" + date);

        // 3.拼接
        return timestamp << COUNT_BITS | count;
    }
}
```







#### 3.3 添加优惠券

每个店铺都可以发布优惠券，分为**平价券**和**特价券**。平价券可以任意购买，而特价券需要秒杀抢购。

`tb_voucher`：优惠券的基本信息，优惠金额、使用规则等

`tb_seckill_voucher`：优惠券的库存、开始抢购时间，结束抢购时间。特价优惠券才需要填写这些信息

平价卷由于优惠力度并不是很大，所以是可以任意领取

而特价券由于优惠力度大，所以就有限制数量，从表结构上也能看出，特价卷除了具有优惠卷的基本信息以外，还具有库存，抢购时间，结束时间等字段



**Controller**

> 添加优惠券，其中参数`voucher`是普通券类型，但是也包含了特价券的字段，对于特价券的字段设置了`@TableField(exist = false)`表示在表中不存在

```java
@PostMapping("seckill")
public Result addSeckillVoucher(@RequestBody Voucher voucher) {
    voucherService.addSeckillVoucher(voucher);
    return Result.ok(voucher.getId());
}
```



**Service**

> 1、保存普通券信息
>
> 2、将普通券中的特价券信息抽取出来，保存特价券

```java
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);
}
```



**postman测试**

```json
{
    "shopId":1,
    "title":"100元代金券",
    "subTitle":"周一至周五可用",
    "rules":"全场通用\\n无需预约\\n可无限叠加",
    "payValue":8000,
    "actualValue":10000,
    "type":1,
    "stock":100,
    "beginTime":"2023-10-07T00:00:00",
    "endTime":"2023-10-31T23:59:59"
}
```







#### 3.4 购买优惠券

下单时需要判断两点：

* 秒杀是否开始或结束，如果尚未开始或已经结束则无法下单
* 库存是否充足，不足则无法下单

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310071724659.png)





**实现**

> 这里订单Id我们使用的是Redis生成的全局唯一ID

```java
@Autowired
private ISeckillVoucherService seckillVoucherService;
@Autowired
private RedisIdWork redisIdWork;

@Override
@Transactional
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券信息
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);

    // 2.1判断秒杀是否开始
    if (LocalDateTime.now().isBefore(voucher.getBeginTime())) {
        return Result.fail("秒杀尚未开始");
    }

    // 2.2.判断秒杀是否结束
    if (LocalDateTime.now().isAfter(voucher.getEndTime())) {
        return Result.fail("秒杀已经结束");
    }

    // 3.判断库存是否充足
    if (voucher.getStock() < 1) {
        return Result.fail("库存不足");
    }

    // 4.修改库存
    boolean success = seckillVoucherService.update().setSql("stock = stock - 1").eq("voucher_id", voucherId).update();
    if (!success) {
        return Result.fail("库存不足");
    }

    // 5.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    long orderId = redisIdWork.nextId("order");
    voucherOrder.setId(orderId);
    voucherOrder.setUserId(UserHolder.getUser().getId());
    voucherOrder.setVoucherId(voucherId);
    save(voucherOrder);

    // 6.返回订单id
    return Result.ok(orderId);
}
```







#### 3.5 库存超卖问题

> 对于上述购买逻辑，如果有大量请求进来，就会发生超卖现象。
>
> 例如有一个线程判断完库存充足之后，还没来得及修改库存，又有非常多线程判断库存充足，然后他们都去执行修改库存的操作，就会发生超卖现象。



解决超卖问题的方案就是加锁：

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310081549771.png" style="zoom:80%;" />



**悲观锁：**

悲观锁可以实现对于数据的串行化执行，比如synchronized和lock都是悲观锁的代表，同时，悲观锁中又可以再细分为公平锁，非公平锁，可重入锁，等等

**乐观锁：**

乐观锁：会有一个版本号，每次操作数据会对版本号+1，再提交回数据时，会去校验是否比之前的版本大1 ，如果大1 ，则进行操作成功，这套机制的核心逻辑在于，如果在操作过程中，版本号只比原来大1 ，那么就意味着操作过程中没有人对他进行过修改，他的操作就是安全的，如果不大1，则数据被修改过。

乐观锁还有一些变种的处理方式比如**cas**，利用cas进行无锁化机制加锁。在我们的业务中，我们可以不用加版本号，在每次修改库存时，只需要判断当前库存数和我们查询的库存相等，此时就可以进行修改库存的操作。



**修改后的代码**

> 逻辑修改后，我们再用多线程环境测试会发现，购买成功的比例很低，最后会剩余很多优惠券。原因在于如果有多个线程同时判断库存充足后，其中第一个执行修改的线程可以成功购买，但是后边其他的线程就会出现购买失败的情况。

```java
boolean success = seckillVoucherService
        .update().setSql("stock = stock - 1")
        .eq("voucher_id", voucherId).eq("stock", voucher.getStock())
        .update();
```



**优化代码**

> 基于以上情况，针对我们的业务，我们可以修改为只要库存大于0，我们就可以购买，这样我们的逻辑就是正确的了。

```java
boolean success = seckillVoucherService
        .update().setSql("stock = stock - 1")
        .eq("voucher_id", voucherId).gt("stock", 0)
        .update();
```







#### 3.6 实现一人一单

> 1、我们上边实现的功能，是一个人可以购买多张优惠券，但实际上我们应该限制一个人只能购买一张优惠券。因此在判断库存充足后，在修改库存操作之前，我们应该在`tb_voucher_order`表中根据当前用户id和优惠券id查询，判断用户是否已经购买过，如果购买过了，就不能在继续购买。
>
> 2、但是这样实现在高并发情况下，如果多个线程的用户id一样，同时发起请求，在将数据写入数据库表之前判断用户是否购买过都可以通过，因此还是存在多买行为。
>
> 3、针对这种情况，我们必须使用悲观锁，即对用户购买的操作加锁。我们可以将修改库存、创建订单的操作提取为一个方法，但是直接对该方法加锁，就是串行执行了，而我们希望的是对每个用户加锁，因此我们使用`synchronized`对每个用户的id加锁，因为用户id是`Long`类型，所以我们需要转为字符串，然后比较其常量池的内容`userId.toString().intern()`
>
> 4、此时事务操作只需要加在创建订单这个方法上即可。这样又会出现一个问题：对用户加锁，用户操作完成后，释放锁，此时事务还没有提交，即还未写入数据库，如果这时另一个与该用户id相同的线程进来，又会产生多买行为。所以我们的锁应该在整个事务结束后才释放。
>
> 5、这样加锁后，`return createOrder(voucherId);`其实调用的就是`this.createOrder(voucherId);` `this`就是当前类对象，我们知道，spring实现事务操作是基于AOP的代理对象实现的，我们使用原生对象是没有事务操作的，因此我们需要先获取到被Spring代理的对象，然后调用代理对象的方法才可以实现事务操作。
>
> ```java
> synchronized (userId.toString().intern()) {
> 	return createOrder(voucherId);
> }
> ```
>
> 6、我们通过`AopContext`获取当前的代理对象，然后通过代理对象调用创建订单的方法，这样就可以有事物的功能了。
>
> ```java
> synchronized (userId.toString().intern()) {
>     	IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
>     	return proxy.createOrder(voucherId);
> }
> ```
>
> 7、为了获取当前代理对象，我们需要添加对应的依赖，同时在启动类上添加`@EnableAspectJAutoProxy(exposeProxy = true)`表示暴露代理对象，这样我们才可以获取到代理对象。
>
> ```xml
> <dependency>
>        <groupId>org.aspectj</groupId>
>        <artifactId>aspectjweaver</artifactId>
> </dependency>	
> ```



```java
@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券信息
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);

    // 2.1判断秒杀是否开始
    if (LocalDateTime.now().isBefore(voucher.getBeginTime())) {
        return Result.fail("秒杀尚未开始");
    }

    // 2.2.判断秒杀是否结束
    if (LocalDateTime.now().isAfter(voucher.getEndTime())) {
        return Result.fail("秒杀已经结束");
    }

    // 3.判断库存是否充足
    if (voucher.getStock() < 1) {
        return Result.fail("库存不足");
    }

    Long userId = UserHolder.getUser().getId();
    synchronized (userId.toString().intern()) {
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createOrder(voucherId);
    }
}

@Transactional
public Result createOrder(Long voucherId) {
    // 4.判断当前用户是否已经下过单
    Long userId = UserHolder.getUser().getId();

    int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
    if (count > 0) {
        return Result.fail("该用户已下单过");
    }

    // 5.修改库存
    boolean success = seckillVoucherService
            .update().setSql("stock = stock - 1")
            .eq("voucher_id", voucherId).gt("stock", 0)
            .update();
    if (!success) {
        return Result.fail("库存不足");
    }

    // 6.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    long orderId = redisIdWork.nextId("order");
    voucherOrder.setId(orderId);
    voucherOrder.setUserId(userId);
    voucherOrder.setVoucherId(voucherId);
    save(voucherOrder);

    // 6.返回订单id
    return Result.ok(orderId);
}
```







#### 3.7 集群环境下的并发问题

> 通过上边加锁的操作，我们可以实现一人一单的问题，但是这仅仅是在单体项目下，假如我们的项目部署在了多台服务器上，那么锁就会失效了。



1、我们通过IDEA将项目设置了两个启动项，第一个端口时8081，第二个端口时8082

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310091426841.png)

2、我们修改了nginx的配置，当前nginx服务器监听8080端口，通过`/api`请求进行反向代理，代理到`http://backend`下，而`http://backend`分别指向了我们服务端的8081和8082端口，实现负载均衡

```json
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/json;

    sendfile        on;
    
    keepalive_timeout  65;

    server {
        listen       8080;
        server_name  localhost;
        # 指定前端项目所在的位置
        location / {
            root   html/hmdp;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        location /api {  
            default_type  application/json;
            #internal;  
            keepalive_timeout   30s;  
            keepalive_requests  1000;  
            #支持keep-alive  
            proxy_http_version 1.1;  
            rewrite /api(/.*) $1 break;  
            proxy_pass_request_headers on;
            #more_clear_input_headers Accept-Encoding;  
            proxy_next_upstream error timeout;  
            #proxy_pass http://127.0.0.1:8081;
            proxy_pass http://backend;
        }
    }

    upstream backend {
        server 127.0.0.1:8081 max_fails=5 fail_timeout=10s weight=1;
        server 127.0.0.1:8082 max_fails=5 fail_timeout=10s weight=1;
    }  
}
```





- 此时我们通过启动两个Tomcat模拟了集群环境，这时我们使用同一个用户对购买优惠券发起两次请求，第一次请求是8081端口的服务器接收，第二次请求是8082端口的服务器接收，此时他们都没有被锁住，因此就产生了并发安全问题。
- 因为第一台服务器有自己的JVM环境，它的锁监视器监听到了线程1获取锁。但是第二台服务器的JVM又是一个全新的环境，在它当中并没有锁被获取的信息，因此锁就失效了。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310091431498.png" style="zoom:65%;" />











### 4、分布式锁

#### 4.1 分布式锁原理

**分布式锁**：满足分布式系统或集群模式下多进程可见并且互斥的锁。

> 分布式锁的核心思想就是让所有线程都使用同一把锁，只要大家使用的是同一把锁，那么我们就能锁住线程，不让线程进行，让程序串行执行，这就是分布式锁的核心思路

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310091513095.png" style="zoom:65%;" />







**常见的分布式锁**

- Mysql：mysql本身就带有锁机制，但是由于mysql性能本身一般，所以采用分布式锁的情况下，其实使用mysql作为分布式锁比较少见

- Redis：redis作为分布式锁是非常常见的一种使用方式，现在企业级开发中基本都使用redis或者zookeeper作为分布式锁，利用setnx这个方法，如果插入key成功，则表示获得到了锁，如果有人插入成功，其他人插入失败则表示无法获得到锁，利用这套逻辑来实现分布式锁

- Zookeeper：zookeeper也是企业级开发中较好的一个实现分布式锁的方案







#### 4.2 Redis实现分布式锁

实现分布式锁时需要实现的两个基本方法：

* 获取锁：

  * 互斥：确保只能有一个线程获取锁
  * 非阻塞：尝试一次，成功返回true，失败返回false
* 释放锁：

  * 手动释放
  * 超时释放：获取锁时添加一个超时时间

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310091516042.png" style="zoom:70%;" />



**锁接口**

```java
public interface ILock {
    public boolean tryLock(long time);
    public void unlock();
}
```



**Redis实现分布式锁**

```java
public class RedisLock implements ILock{

    private StringRedisTemplate stringRedisTemplate;
    private String name;  // 业务名称，作为key使用
    private final String PREFIX_KEY = "lock:";

    public RedisLock(StringRedisTemplate stringRedisTemplate, String name) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.name = name;
    }

    @Override
    public boolean tryLock(long time) {
        long id = Thread.currentThread().getId();
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent(PREFIX_KEY + name, id + "", time, TimeUnit.SECONDS);
        // success需要进行拆箱操作，避免出现空指针
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        stringRedisTemplate.delete(PREFIX_KEY + name);
    }
}
```



**修改业务功能**

```java
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券信息
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);

    // 2.1判断秒杀是否开始
    if (LocalDateTime.now().isBefore(voucher.getBeginTime())) {
        return Result.fail("秒杀尚未开始");
    }

    // 2.2.判断秒杀是否结束
    if (LocalDateTime.now().isAfter(voucher.getEndTime())) {
        return Result.fail("秒杀已经结束");
    }

    // 3.判断库存是否充足
    if (voucher.getStock() < 1) {
        return Result.fail("库存不足");
    }

    Long userId = UserHolder.getUser().getId();

    // 获取redis锁
    RedisLock redisLock = new RedisLock(stringRedisTemplate, "order:" + userId);
    boolean success = redisLock.tryLock(1200);
    // 获取锁失败
    if (!success) {
        return Result.fail("不允许重复下单");
    }
    // 获取锁成功
    try {
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createOrder(voucherId);
    } finally {
        redisLock.unlock();
    }
}
```







#### 4.3 Redis分布式锁误删问题

逻辑说明：

持有锁的线程在锁的内部出现了阻塞，导致他的锁自动释放，这时其他线程，线程2来尝试获得锁，就拿到了这把锁，然后线程2在持有锁执行过程中，线程1反应过来，继续执行，而线程1执行过程中，走到了删除锁逻辑，此时就会把本应该属于线程2的锁进行删除，这就是误删别人锁的情况说明

解决方案：解决方案就是在每个线程释放锁的时候，去判断一下当前这把锁是否属于自己，如果属于自己，则进行锁的删除，假设还是上边的情况，线程1卡顿，锁自动释放，线程2进入到锁的内部执行逻辑，此时线程1反应过来，然后删除锁，但是线程1，一看当前这把锁不属于自己，于是不进行删除锁逻辑，当线程2走到删除锁逻辑时，如果没有过自动释放锁的时间点，则判断当前这把锁是属于自己的，于是删除这把锁。



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310091543529.png" style="zoom:80%;" />



**优化后代码**

> 1、之前我们使用的是线程id作为value存入redis，但是在集群环境下，可能会出现两台服务器使用同一个id的线程。因此我们的value使用一个UUID拼接线程id，这样就不会产生重复。
>
> 2、在获取锁时，将UUID和线程id作为value存入redis。
>
> 3、在释放锁时，判断当前value和从redis中取出的value是否一致，一致才去释放锁。

```java
public class RedisLock implements ILock{

    private StringRedisTemplate stringRedisTemplate;
    private String name;  // 业务名称，作为key使用

    private static final String PREFIX_KEY = "lock:";
    private static final String ID_PREFIX = UUID.randomUUID().toString() + "-";

    public RedisLock(StringRedisTemplate stringRedisTemplate, String name) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.name = name;
    }

    @Override
    public boolean tryLock(long time) {
        String id = ID_PREFIX + Thread.currentThread().getId();
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent(PREFIX_KEY + name, id, time, TimeUnit.SECONDS);
        // success需要进行拆箱操作，避免出现空指针
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        // 获取线程标识
        String id = ID_PREFIX + Thread.currentThread().getId();
        // 获取锁的标识
        String value = stringRedisTemplate.opsForValue().get(PREFIX_KEY + name);
        // 判断标识是否一致
        if (id.equals(value)) {
            stringRedisTemplate.delete(PREFIX_KEY + name);
        }
    }
}
```







#### 4.4 分布式锁的原子性问题

更为极端的误删逻辑说明：

线程1现在持有锁之后，在执行业务逻辑过程中，他正准备删除锁，而且已经走到了条件判断的过程中，比如他已经拿到了当前这把锁确实是属于他自己的，正准备删除锁，但是此时他的锁到期了，那么此时线程2进来，但是线程1他会接着往后执行，当他卡顿结束后，他直接就会执行删除锁那行代码，相当于条件判断并没有起到作用，这就是删锁时的原子性问题，之所以有这个问题，是因为线程1的拿锁，比锁，删锁，实际上并不是原子性的，我们要防止刚才的情况发生，就需要**将判断锁的标识和删除锁同时执行**。



Redis提供了Lua脚本功能，在一个脚本中编写多条Redis命令，确保多条命令执行时的原子性。

**Redis提供的调用函数**

```lua
redis.call('命令名称', 'key', '其它参数', ...)

# 执行 set name jack
redis.call('set', 'name', 'jack')

# 先执行 set name jack
redis.call('set', 'name', 'jack')
# 再执行 get name
local name = redis.call('get', 'name')
# 返回
return name
```





**Redis执行脚本**

```lua
EVAL script key [key...] arg [arg...]

# 执行redis.call('set', 'name', 'jack') 
EVAL "redis.call('set', 'name', 'jack')" 0  # 0表示脚本需要的key类型的参数个数

# 脚本中变量通过参数传递，1表示有1个key，KEYS[1]就表示name，ARGS[1]就表示xrj
EVAL "redis.call('set', KEYS[1], ARGS[1])" 1 name xrj  
```





**编写lua脚本**

```lua
-- 这里的 KEYS[1] 就是锁的key，这里的ARGV[1] 就是当前线程标识
-- 获取锁中的标识，判断是否与当前线程标示一致
if (redis.call('GET', KEYS[1]) == ARGV[1]) then
    -- 一致，则删除锁
    return redis.call('DEL', KEYS[1])
end
-- 不一致，则直接返回
return 0
```



**java调用lua脚本**

```java
private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;
static {
    UNLOCK_SCRIPT = new DefaultRedisScript<>();
    UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
    UNLOCK_SCRIPT.setResultType(Long.class);
}
@Override
public void unlock() {
    stringRedisTemplate.execute(UNLOCK_SCRIPT,
            Collections.singletonList(PREFIX_KEY + name), # key参数
            ID_PREFIX + Thread.currentThread().getId());  # args参数
}
```









### 5、分布式锁-Redission

基于`setnx`实现的分布式锁存在下面的问题：

- **重入问题**：重入问题是指 获得锁的线程可以再次进入到相同的锁的代码块中，可重入锁的意义在于防止死锁，比如HashTable这样的代码中，他的方法都是使用synchronized修饰的，假如他在一个方法内，调用另一个方法，那么此时如果是不可重入的，不就死锁了吗？所以可重入锁他的主要意义是防止死锁，我们的synchronized和Lock锁都是可重入的。

- **不可重试**：是指目前的分布式只能尝试一次，我们认为合理的情况是：当线程在获得锁失败后，他应该能再次尝试获得锁。

- **超时释放：**我们在加锁时增加了过期时间，这样的我们可以防止死锁，但是如果卡顿的时间超长，虽然我们采用了lua表达式防止删锁的时候，误删别人的锁，但是毕竟没有锁住，有安全隐患

- **主从一致性：** 如果Redis提供了主从集群，当我们向集群写数据时，使用的是主节点，读数据使用的是从节点，同时主节点需要异步的将数据同步给从节点，而万一在同步过去之前，主节点宕机了，此时会选一个从节点，作为主节点，但是这时从节点上是没有写入的这个数据的。





#### 5.1 Redisson介绍

> Redisson是一个在Redis的基础上实现的Java驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的Java常用对象，还提供了许多分布式服务，其中就包含了各种分布式锁的实现。



Redission提供了分布式锁的多种多样的功能

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310091728062.png)









#### 5.2 Redisson使用

**1、导入依赖**

```xml
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.13.6</version>
</dependency>
```



**2、配置Redisson**

```java
@Configuration
public class RedissonConfig {
    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        // useSingleServer()表示当前使用的是单个redis服务，如果redis是集群模式，可以使用useClusterServers
        config.useSingleServer().setAddress("redis://127.0.0.1:6379");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}
```



**3、使用Redisson**

```java
@Autowired
private RedissonClient redissonClient;

@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券信息
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);

    // 2.1判断秒杀是否开始
    if (LocalDateTime.now().isBefore(voucher.getBeginTime())) {
        return Result.fail("秒杀尚未开始");
    }

    // 2.2.判断秒杀是否结束
    if (LocalDateTime.now().isAfter(voucher.getEndTime())) {
        return Result.fail("秒杀已经结束");
    }

    // 3.判断库存是否充足
    if (voucher.getStock() < 1) {
        return Result.fail("库存不足");
    }

    Long userId = UserHolder.getUser().getId();

    // 获取redis锁
    // RedisLock lock = new RedisLock(stringRedisTemplate, "order:" + userId);
    // 获取可重入锁
    RLock lock = redissonClient.getLock("lock:order:" + userId);
    // tryLock可以添加三个参数，分别是: 获取锁的最大等待时间(超时重试，默认-1表示失败就返回)、锁自动释放时间(默认30s)、时间单位
    boolean success = lock.tryLock();
    // 获取锁失败
    if (!success) {
        return Result.fail("不允许重复下单");
    }
    // 获取锁成功
    try {
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createOrder(voucherId);
    } finally {
        lock.unlock();
    }
}
```







#### 5.3 可重入锁原理

> 我们通过Redis的`setnx`命令实现的分布式锁，是不可重入的，因为通过`setnx`命令获取锁后，即便当前线程想再次获取锁也是不可以的。
>
> 在Lock锁中，他是借助于底层的一个voaltile的一个state变量来记录重入的状态的，比如当前没有人持有这把锁，那么state=0，假如有人持有这把锁，那么state=1，如果持有这把锁的人再次持有这把锁，那么state就会+1 
>
> 对于synchronized而言，他在c语言代码中会有一个count，原理和state类似，也是重入一次就加一，释放一次就-1 ，直到减少成0 时，表示当前这把锁没有被人持有。  
>
> 在`redission`中，也支持可重入锁。在分布式锁中，他采用**hash结构**用来存储锁，其中`key`表示这把锁是否存在，`field`表示当前这把锁被哪个线程持有，`value`表示这把锁被重入的次数。
>
> **获取锁：**
>
> 首先判断锁是否存在，如果锁不存在，那么就获取锁，并将field字段设置为当前线程标识，value字段设置为1，然后设置有效期。如果锁存在，那么就判断field字段和自己当前线程标识是否相等，相等表示锁是自己的，就将value字段的值+1，否则，获取锁失败。
>
> **释放锁：**
>
> 判断锁是否是自己的，如果是自己的，将value字段的值-1，然后判断value字段的值是否为0，如果为0，就释放锁，否则，重置有效期，继续执行业务。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310101542825.png" style="zoom:70%;" />





**Redisson中tryLock源码**

> 可以看到Redission获取锁底层也是执行lua脚本
>
> `KEYS[1]`： 锁名称
>
> `ARGV[1]`：  锁失效时间
>
> `ARGV[2]`： ` id + ":" + threadId`
>
> 1、判断当前锁是否存在，如果不存在，就将key设置为`KEYS[1]`，field设置为`ARGV[2]`，value加1，刚开始不存在就创建然后设置为1，同时设置过期时间
>
> 2、如果当前锁存在，同时这把锁是当前线程的，那么就将value字段+1，然后设置过期时间
>
> 3、如果这两个条件都不满足，表示获取锁失败，就返回当前锁的过期时间，单位为毫秒

```java
<T> RFuture<T> tryLockInnerAsync(long waitTime, long leaseTime, TimeUnit unit, long threadId, RedisStrictCommand<T> command) {
    this.internalLockLeaseTime = unit.toMillis(leaseTime);
    return this.evalWriteAsync(this.getName(), LongCodec.INSTANCE, command, 
           "if (redis.call('exists', KEYS[1]) == 0) then 
               redis.call('hincrby', KEYS[1], ARGV[2], 1); 
               redis.call('pexpire', KEYS[1], ARGV[1]); 
               return nil; 
           end; 
           if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then 
               redis.call('hincrby', KEYS[1], ARGV[2], 1); 
               redis.call('pexpire', KEYS[1], ARGV[1]); 
               return nil; 
           end; 
           return redis.call('pttl', KEYS[1]);", 
           Collections.singletonList(this.getName()), 
           this.internalLockLeaseTime, this.getLockName(threadId));
}
```



**Redisson中unlock源码**

> Redisson释放锁的源码也是通过执行lua脚本实现的
>
> 1、判断当前这把锁是否是当前线程的，如果不是，就返回nil
>
> 2、当前线程持有锁，将value字段的值-1，如果counter的值-1后仍然大于0，设置过期时间，返回0
>
> 3、当前线程持有锁，将value字段的值-1后值为0，释放锁，同时publish通知其他线程，锁被释放。

```java
protected RFuture<Boolean> unlockInnerAsync(long threadId) {
    return this.evalWriteAsync(this.getName(), LongCodec.INSTANCE, RedisCommands.EVAL_BOOLEAN, 
           "if (redis.call('hexists', KEYS[1], ARGV[3]) == 0) then 
               return nil;
           end; 
           local counter = redis.call('hincrby', KEYS[1], ARGV[3], -1); 
           if (counter > 0) then 
               redis.call('pexpire', KEYS[1], ARGV[2]); 
               return 0; 
           else 
               redis.call('del', KEYS[1]); 
               redis.call('publish', KEYS[2], ARGV[1]); 
               return 1; 
           end; 
           return nil;", 
           Arrays.asList(this.getName(), this.getChannelName()), 
           LockPubSub.UNLOCK_MESSAGE, this.internalLockLeaseTime, this.getLockName(threadId));
}
```







#### 5.4 Redisson锁重试和WatchDog机制

##### Redisson锁重试

1、此时调用`tryLock`函数时，第一个参数是等待时间，且未设置锁超时时间

```java
success = lock.tryLock(1, TimeUnit.SECONDS);
```

2、获取锁的操作

```java
public boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException {
    // 将等待时间转为毫秒
    long time = unit.toMillis(waitTime);
    // 记录当前时间
    long current = System.currentTimeMillis();
    // 记录当前线程id
    long threadId = Thread.currentThread().getId();
    // 获取锁，如果返回的ttl为空，表示获取锁成功，不为空，表示获取锁失败
    Long ttl = this.tryAcquire(waitTime, leaseTime, unit, threadId);
    // 获取锁成功
    if (ttl == null) {
        return true;
    // 获取锁失败
    } else {
        // 剩余的等待时间，用最初的time减去获取锁消耗的时间
        time -= System.currentTimeMillis() - current;
        // 如果剩余时间<=0，就直接退出，获取锁失败
        if (time <= 0L) {
            this.acquireFailed(waitTime, unit, threadId);
            return false;
        // 剩余时间>0
        } else {
            // 获取当前时间
            current = System.currentTimeMillis();
            // 在释放锁的lua脚本中，如果释放锁了，会通过publish发布一个通知，这里subscribe就是订阅这个通知
            RFuture<RedissonLockEntry> subscribeFuture = this.subscribe(threadId);
            // 当前订阅等待这个通知，最多等待时间为time，如果在time时间内等到了，就返回true，否则返回false
            if (!subscribeFuture.await(time, TimeUnit.MILLISECONDS)) {
                // 在time时间内没有等待通知，那么就取消订阅，同时获取锁失败
                if (!subscribeFuture.cancel(false)) {
                    subscribeFuture.onComplete((res, e) -> {
                        if (e == null) {
                            this.unsubscribe(subscribeFuture, threadId);
                        }

                    });
                }

                this.acquireFailed(waitTime, unit, threadId);
                return false;
            // 在time时间内等到了通知
            } else {
                boolean var14;
                try {
                    // 计算剩余的时间
                    time -= System.currentTimeMillis() - current;
                    // 如果剩余>0，那么就继续去获取锁
                    if (time > 0L) {
                        boolean var16;
                        // 只要剩余时间>0，就一直循环
                        do {
                            // 记录当前时间
                            long currentTime = System.currentTimeMillis();
                            // 尝试获取锁，返回一个ttl
                            ttl = this.tryAcquire(waitTime, leaseTime, unit, threadId);
                            // ttl为空，表示获取锁成功
                            if (ttl == null) {
                                var16 = true;
                                return var16;
                            }
							
                            // 后边表示获取锁失败，重新剩余时间
                            time -= System.currentTimeMillis() - currentTime;
                            // 剩余时间<=0，获取锁失败
                            if (time <= 0L) {
                                this.acquireFailed(waitTime, unit, threadId);
                                var16 = false;
                                return var16;
                            }

                            // 剩余时间>0
                            currentTime = System.currentTimeMillis();
                            if (ttl >= 0L && ttl < time) {
                                // 这里采用信号量的方式等待，如果ttl<time，最多等待ttl的时间
                                ((RedissonLockEntry)subscribeFuture.getNow()).getLatch().tryAcquire(ttl, TimeUnit.MILLISECONDS);
                            } else {
                                // ttl>=time，剩余时间小，那么最多time时间
                                ((RedissonLockEntry)subscribeFuture.getNow()).getLatch().tryAcquire(time,TimeUnit.MILLISECONDS);
                            }
							
                            // 重新计算剩余时间
                            time -= System.currentTimeMillis() - currentTime;
                        } while(time > 0L);

                        // 剩余时间<=0，获取锁失败
                        this.acquireFailed(waitTime, unit, threadId);
                        var16 = false;
                        return var16;
                    }
					// 剩余时间<=0，获取锁失败
                    this.acquireFailed(waitTime, unit, threadId);
                    var14 = false;
                } finally {
                    this.unsubscribe(subscribeFuture, threadId);
                }

                return var14;
            }
        }
    }
}
```





##### WatchDog机制

1、我们在获取锁时，如果没有传入超时时间，超时时间默认会被置为30s，同时会执行更新过期时间的操作

```java
private <T> RFuture<Long> tryAcquireAsync(long waitTime, long leaseTime, TimeUnit unit, long threadId) {
    // leaseTime为锁释放时间，如果我们不指定，默认为-1
    if (leaseTime != -1L) {
        // 如果锁释放时间不是-1，那么就执行下边这个获取锁操作，leaseTime就是我们指定的时间
        return this.tryLockInnerAsync(waitTime, leaseTime, unit, threadId, RedisCommands.EVAL_LONG);
    } else {
        // 锁释放时间是-1，我们采用默认的看门狗时间【30秒】，然后去获取锁
        RFuture<Long> ttlRemainingFuture = this.tryLockInnerAsync(waitTime, this.commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout(), TimeUnit.MILLISECONDS, threadId, RedisCommands.EVAL_LONG);
        // 如果我们没有指定锁释放时间，会继续执行下边的代码
        // 当上边代码执行完毕后，就执行下边代码，ttlRemaining是执行的结果，e是异常
        ttlRemainingFuture.onComplete((ttlRemaining, e) -> {
            if (e == null) {
                // 获取锁成功
                if (ttlRemaining == null) {
                    // 执行过期时间更新的一个操作
                    this.scheduleExpirationRenewal(threadId);
                }

            }
        });
        return ttlRemainingFuture;
    }
}
```

2、当前有一个静态的Map集合，`key`是`id:name`，name就是当前锁的名称，`value`是一个`entry`。在这个`entry`内部，有一个`Map`，其`key`是线程id，`value`是当前线程重入的次数。

如果此时map非空，那么就直接返回他的value，即oldEntry，然后执行`addThreadId`方法，就是将当前线程id重入次数+1

如果此时map为空，那么我们就添加元素，key是`id:name`，`value`是一个空的entry，然后执行`addThreadId`操作，同时执行`renewExpiration`方法，刷新锁过期时间

```java
private void scheduleExpirationRenewal(long threadId) {
    RedissonLock.ExpirationEntry entry = new RedissonLock.ExpirationEntry();
    // EXPIRATION_RENEWAL_MAP这个map的key是String，value就是创建的entry类型，this.getEntryName()的值为 id:name，name是当前锁的名称 
    // 对于当前线程来说，如果这个map是空的，那么我们就往map中添加，key是id:name，value是一个空的entry，然后返回null，如果map不空，那么我们就不添加，同时返回oldEntry，这样保证了不管这把锁被当前线程重入了多少次，拿到的是同一个entry
    RedissonLock.ExpirationEntry oldEntry = 
        (RedissonLock.ExpirationEntry)EXPIRATION_RENEWAL_MAP.putIfAbsent(this.getEntryName(), entry); 
    if (oldEntry != null) {
        // oldEntry不为空表示当前线程不是第一次来获取锁了，将线程id加入entry中
        // 这里addThreadId操作其实就是将线程重入次数+1，ExpirationEntry里也维护了一个map，以线程id作为key，以重入次数作为value，addThreadId操作就是将value值+1
        oldEntry.addThreadId(threadId);
    } else {
        // oldEntry为空，表示当前线程是第一次获取锁，那么就将线程id加入entry
        entry.addThreadId(threadId);
        // 更新有效期
        this.renewExpiration();
    }

}
```

3、因为默认的锁过期时间为30s，所以每经过10s，就会通过lua脚本去重置锁过期时间，并且递归调用这个方法，即不停的刷新锁过期时间。最后将这个刷新任务也放入entry中。所以只要当前线程没有执行完，锁就不会释放。

但是假设我们的线程出现了宕机他就不再去刷新过期时间了，因为没有人再去调用`renewExpiration`这个方法，所以等到时间之后自然就释放了。

```java
private void renewExpiration() {
    // 获取到当前锁的entry
    RedissonLock.ExpirationEntry ee = (RedissonLock.ExpirationEntry)EXPIRATION_RENEWAL_MAP.get(this.getEntryName());
    if (ee != null) {
        // 拿到一个超时任务，经过 this.internalLockLeaseTime/3L 时间后就执行，internalLockLeaseTime就是看门狗时间30s，即经过10秒后，就执行任务
        Timeout task = this.commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
            public void run(Timeout timeout) throws Exception {
                // 拿到当前这个entry
                RedissonLock.ExpirationEntry ent = (RedissonLock.ExpirationEntry)RedissonLock.EXPIRATION_RENEWAL_MAP.get(RedissonLock.this.getEntryName());
                if (ent != null) {
                    // 获取到线程id
                    Long threadId = ent.getFirstThreadId();
                    if (threadId != null) {
                        // 执行renewExpirationAsync方法，这个方法就是通过lua脚本刷新过期时间
                        RFuture<Boolean> future = RedissonLock.this.renewExpirationAsync(threadId);
                        // 任务执行完成后
                        future.onComplete((res, e) -> {
                            if (e != null) {
                                RedissonLock.log.error("Can't update lock " + RedissonLock.this.getName() + " expiration", e);
                            } else {
                                if (res) {
                                    // 再次调用renewExpiration方法，递归调用，即每过10秒就刷新一次过期时间，这样只有当前线程执行完毕，主动释放锁									   时，锁才会被释放
                                    RedissonLock.this.renewExpiration();
                                }

                            }
                        });
                    }
                }
            }
        }, this.internalLockLeaseTime / 3L, TimeUnit.MILLISECONDS);
        // 将这个任务放入entry中
        ee.setTimeout(task);
    }
}
```

```java
protected RFuture<Boolean> renewExpirationAsync(long threadId) {
    return this.evalWriteAsync(this.getName(), LongCodec.INSTANCE, RedisCommands.EVAL_BOOLEAN, 
    	"if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then 
            redis.call('pexpire', KEYS[1], ARGV[1]); 
            return 1; 
        end; 
        return 0;", 
        Collections.singletonList(this.getName()), 
        this.internalLockLeaseTime, 
        this.getLockName(threadId));
}
```



**释放锁**

> 如果我们不指定锁过期时间，对于某一个线程，如果他任务没有执行完，锁是不会被释放的，直到任务执行完毕，它主动执行释放锁的操作，会将锁释放了，然后取消刷新过期时间这个任务。

```java
public RFuture<Void> unlockAsync(long threadId) {
    RPromise<Void> result = new RedissonPromise();
    // 通过lua脚本执行释放锁的操作
    RFuture<Boolean> future = this.unlockInnerAsync(threadId);
    future.onComplete((opStatus, e) -> {
        // 取消刷新过期时间的任务
        this.cancelExpirationRenewal(threadId);
        if (e != null) {
            result.tryFailure(e);
        } else if (opStatus == null) {
            IllegalMonitorStateException cause = new IllegalMonitorStateException("attempt to unlock lock, not locked by current thread by node id: " + this.id + " thread-id: " + threadId);
            result.tryFailure(cause);
        } else {
            result.trySuccess((Object)null);
        }
    });
    return result;
}


void cancelExpirationRenewal(Long threadId) {
    // 拿到当前的entry
    RedissonLock.ExpirationEntry task = (RedissonLock.ExpirationEntry)EXPIRATION_RENEWAL_MAP.get(this.getEntryName());
    if (task != null) {
        if (threadId != null) {
            // 将线程id移除
            task.removeThreadId(threadId);
        }
	
        if (threadId == null || task.hasNoThreads()) {
            // 拿到这个超时任务
            Timeout timeout = task.getTimeout();
            if (timeout != null) {
                // 取消任务
                timeout.cancel();
            }

            EXPIRATION_RENEWAL_MAP.remove(this.getEntryName());
        }

    }
}
```







#### 5.5 Redisson-MutiLock原理

> 1、为了提高redis的可用性，我们会搭建集群或者主从，以主从为例。此时我们去执行写命令，写在主机上， 主机会将数据同步给从机，但是假设在主机还没有来得及把数据写入到从机去的时候，此时主机宕机，哨兵会发现主机宕机，并且选举一个从机变成主机，而此时新的主机中实际上并没有锁信息，此时锁信息就已经丢掉了。		
>
> 2、为了解决这个问题，redisson提出来了**MutiLock锁**，使用这把锁就不使用主从了，每个节点的地位都是一样的， 这把锁加锁的逻辑需要写入到每一个主节点上，只有所有的主节点都写入成功，此时才是加锁成功，假设现在某个主节点挂了，将它的从节点作为主节点。那么去获得锁的时候，只要有一个节点拿不到锁，都不能算是加锁成功，就保证了加锁的可靠性。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310102007136.png" style="zoom:60%;" />





**MutiLock锁源码**

```java
public boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException {
    long newLeaseTime = -1L;
    if (leaseTime != -1L) {
        if (waitTime == -1L) {
            newLeaseTime = unit.toMillis(leaseTime);
        } else {
            newLeaseTime = unit.toMillis(waitTime) * 2L;
        }
    }

    // 记录当前时间
    long time = System.currentTimeMillis();
    long remainTime = -1L;
    if (waitTime != -1L) {
        // 如果设置了等待时间，就将等待时间赋值给remainTime
        remainTime = unit.toMillis(waitTime);
    }

    // 锁等待时间，这里就是直接return的remainTime
    long lockWaitTime = this.calcLockWaitTime(remainTime);
    // 获取锁失败的限制，默认为0
    int failedLocksLimit = this.failedLocksLimit();
    // 成功获取锁的集合
    List<RLock> acquiredLocks = new ArrayList(this.locks.size());
    // 当前所有锁的迭代器
    ListIterator iterator = this.locks.listIterator();

    // 遍历当前所有的锁
    while(iterator.hasNext()) {
        // 拿到当前这把锁
        RLock lock = (RLock)iterator.next();

        boolean lockAcquired;
        // 获取锁
        try {
            if (waitTime == -1L && leaseTime == -1L) {
                lockAcquired = lock.tryLock();
            } else {
                long awaitTime = Math.min(lockWaitTime, remainTime);
                lockAcquired = lock.tryLock(awaitTime, newLeaseTime, TimeUnit.MILLISECONDS);
            }
        } catch (RedisResponseTimeoutException var21) {
            this.unlockInner(Arrays.asList(lock));
            lockAcquired = false;
        } catch (Exception var22) {
            lockAcquired = false;
        }
	
        // 获取锁成功就将这把锁加入到acquiredLocks中
        if (lockAcquired) {
            acquiredLocks.add(lock);
        // 获取锁失败
        } else {
            // 当前获取锁失败的数量==限制的数量，就退出循环返回true
            if (this.locks.size() - acquiredLocks.size() == this.failedLocksLimit()) {
                break;
            }

            if (failedLocksLimit == 0) {
                // 将获取的所有锁释放
                this.unlockInner(acquiredLocks);
                // 如果waitTime==-1表示获取锁失败就不再重试，那么直接返回false
                if (waitTime == -1L) {
                    return false;
                }

                failedLocksLimit = this.failedLocksLimit();
                acquiredLocks.clear();

                // 将锁的迭代器指针指向第一个
                while(iterator.hasPrevious()) {
                    iterator.previous();
                }
            } else {
                --failedLocksLimit;
            }
        }

        // remainTime就是等待时间
        if (remainTime != -1L) {
            // 获取完一把锁后就计算剩余等待时间
            remainTime -= System.currentTimeMillis() - time;
            time = System.currentTimeMillis();
            // 如果剩余等待时间<=0，释放锁，并返回false
            if (remainTime <= 0L) {
                this.unlockInner(acquiredLocks);
                return false;
            }
        }
    }

  	// 如果我们设置了锁超时时间，那么获取完所有的锁后，重新设置一下他们的超时时间，因为获取锁时会消耗前边锁的时间
    if (leaseTime != -1L) {
        List<RFuture<Boolean>> futures = new ArrayList(acquiredLocks.size());
        Iterator var24 = acquiredLocks.iterator();

        while(var24.hasNext()) {
            RLock rLock = (RLock)var24.next();
            RFuture<Boolean> future = ((RedissonLock)rLock).expireAsync(unit.toMillis(leaseTime), TimeUnit.MILLISECONDS);
            futures.add(future);
        }

        var24 = futures.iterator();

        while(var24.hasNext()) {
            RFuture<Boolean> rFuture = (RFuture)var24.next();
            rFuture.syncUninterruptibly();
        }
    }

    return true;
}
```







### 6、秒杀优化

目前我们的下单流程是这样的：用户发起请求，此时会先请求Nginx，Nginx反向代理到Tomcat，而Tomcat中的程序，会进行串行操作，分为如下几个步骤：

1、查询优惠券

2、判断库存是否足够

3、查询订单

4、判断该用户是否购买过

5、扣减库存

6、创建订单

在这六个步骤中，查询优惠券、查询订单、扣减库存、创建订单都需要操作数据库，而且是串行执行的，因此效率很低。



**优化思路：**我们将耗时较短的逻辑判断放到Redis中，例如：库存是否充足，是否一人一单这样的操作，只要满足这两条操作，那我们是一定可以下单成功的，不用等数据真的写进数据库，我们直接告诉用户下单成功就好了。然后后台再开一个线程，后台线程再去慢慢执行队列里的消息，这样我们就能很快的完成下单业务。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310111101111.png" style="zoom:67%;" />







#### 6.1 Redis判断秒杀资格

> 1、对于优惠券库存，我们使用redis的string结构即可，key是优惠券id，vlaue是库存
>
> 2、对于一人一单，我们使用redis的set结构，key是优惠券id，value是下过单的用户id
>
> 3、业务流程：当用户下单后，我们首先去Redis中查询库存是否充足，如果库存不够，那么返回1。否则，根据优惠券id查询该用户是否下过单，如果已经下过单那么返回2。这两个操作都通过后，表示用户可以下单，那么就将redis中优惠券库存-1，同时将用户id添加进去，最后返回0表示下单成功。而这些操作需要保证原子性，因此我们使用lua脚本来实现。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310111109167.png" style="zoom:67%;" />





**添加优惠券时保存到Redis**

```java
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);

    // 将优惠券库存信息保存到redis中
    stringRedisTemplate.opsForValue().set(RedisConstants.SECKILL_STOCK_KEY + voucher.getId(), voucher.getStock().toString());
}
```



**lua脚本**

```lua
-- 需要的参数
-- 优惠券id
local voucherId = ARGV[1]
-- 用户id
local userId = ARGV[2]
-- 优惠券key
local voucherKey = "seckill:stock:" .. voucherId
-- 订单key
local orderKey = "seckill:order:" .. voucherId

-- 1.判断库存是否充足
if (tonumber(redis.call("get", voucherKey)) <= 0) then
    return 1
end

-- 2.判断用户是否下过单
if (redis.call("sismember", orderKey, userId) == 1) then
    return 2
end

-- 3.库存-1
redis.call("incrby", voucherKey, -1)

-- 4.保存用户id
redis.call("sadd", orderKey, userId)
return 0
```



**下单功能**

```java
@Override
public Result seckillVoucher(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    // 1.执行lua脚本
    Long result = stringRedisTemplate.execute(SECKILL_SCRIPT,
            Collections.emptyList(),
            voucherId.toString(), userId.toString());

    // 2.判断结果是否为0
    if (result.intValue() != 0) {
        return Result.fail(result.intValue() == 1 ? "库存不足" : "不能重复下单");
    }
    long orderId = redisIdWork.nextId("order");

    // 3.TODO 将优惠券信息保存到阻塞队列

    // 4.返回订单id
    return Result.ok(orderId);
}
```







#### 6.2 阻塞队列实现下单

修改下单的操作，我们在下单时，是通过Lua表达式去原子执行判断逻辑，如果判断结果不为0，返回错误信息，如果判断结果为0，则将下单的逻辑保存到队列中去，然后异步执行



**需求：**

- 如果秒杀成功，则将优惠券id和用户id封装后存入阻塞队列

- 开启线程任务，不断从阻塞队列中获取信息，实现异步下单功能



**1、创建阻塞队列**

> 阻塞队列：当一个线程尝试从阻塞队列里获取元素的时候，如果没有元素，那么该线程就会被阻塞，直到队列中有元素，才会被唤醒，并去获取元素
>
> 这里只要用户下单成功，我们就将其订单信息保存到阻塞队列中

```java
// 创建阻塞队列，并指定队列大小
private BlockingQueue<VoucherOrder> orderTasks = new ArrayBlockingQueue<>(1024 * 1024);

@Override
public Result seckillVoucher(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    // 1.执行lua脚本
    Long result = stringRedisTemplate.execute(SECKILL_SCRIPT,
                                              Collections.emptyList(),
                                              voucherId.toString(), userId.toString());

    // 2.判断结果是否为0
    if (result.intValue() != 0) {
        return Result.fail(result.intValue() == 1 ? "库存不足" : "不能重复下单");
    }

    // 3. 将优惠券信息保存到阻塞队列
    VoucherOrder voucherOrder = new VoucherOrder();
    long orderId = redisIdWork.nextId("order");
    voucherOrder.setId(orderId);
    voucherOrder.setUserId(userId);
    voucherOrder.setVoucherId(voucherId);
    orderTasks.add(voucherOrder);

    try {
        // 这里先获取到代理对象，以供异步线程使用
        proxy = (IVoucherOrderService) AopContext.currentProxy();
    } catch (IllegalStateException e) {
        e.printStackTrace();
    }

    // 4.返回订单id
    return Result.ok(orderId);
}
```



**2、异步下单功能**

> 1、我们创建线程池，用这个异步线程去完成创建订单的操作
>
> 2、我们使用`@PostConstruct`注解标注`init`方法，表示在类加载后就执行这个方法，在`init`方法中，我们使用这个异步线程去执行创建订单的这个任务
>
> 3、`VoucherOrderHandler`创建订单的任务，我们首先从阻塞队列中获取订单信息，然后通过代理对象去调用创建订单的方法执行创建订单的操作。注意：`AopContext`中获取代理对象也是通过`ThreadLocal`获取的，但是现在是异步线程，所以我们在主线程获取到代理对象后，将其作为该类的属性直接使用。
>
> ```java
> private static final ThreadLocal<Object> currentProxy = new NamedThreadLocal("Current AOP proxy");
> ```

```java
// 创建线程池，异步的从阻塞队列获取元素并完成下单操作
private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();
// 将代理对象作为类的属性，因此异步线程执行时，无法获取代理对象，所以在主线程中获取到代理对象后，在异步线程中可以直接使用
private IVoucherOrderService proxy;

// @PostConstruct表示在类初始化后，就立刻执行这个方法
// 因此类初始化后，这个异步线程就开始执行下单任务，但是如果阻塞队列没有元素，这个线程就会阻塞，不会消耗cpu资源
@PostConstruct
public void init() {
    SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
}

// 创建线程任务
private class VoucherOrderHandler implements Runnable {
    @Override
    public void run() {
        try {
            // 1.获取订单信息
            VoucherOrder voucherOrder = orderTasks.take();
            // 2.完成下单任务
            // 获取锁
            RLock lock = redissonClient.getLock("lock:order:" + voucherOrder.getUserId());
            // tryLock可以添加三个参数，分别是: 获取锁的最大等待时间(超时重试，默认-1表示失败就返回)、锁自动释放时间(默认30s)、时间单位
            boolean success = lock.tryLock();
            // 获取锁失败
            if (!success) {
                log.error("不允许重复下单");
            }
            // 获取锁成功
            try {
                // 因为AopContext中也是通过ThreadLocal获取元素的，这里是异步线程，所以获取不到代理对象
                proxy.createOrder(voucherOrder);
            } finally {
                lock.unlock();
            }
        } catch (Exception e) {
            log.error("下单操作异常:", e);
        }
    }
}

@Transactional
@Override
public void createOrder(VoucherOrder voucherOrder) {
    // 1.现在是异步线程，所以通过ThreadLocal是获取不到用户的
    Long userId = voucherOrder.getUserId();
    Long voucherId = voucherOrder.getVoucherId();

    // 下边两个操作不需要判断了，但是防止redis出问题，所以我们再次判断
    // 2.判断用户是否下过单
    int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
    if (count > 0) {
        log.error("不能重复下单");
    }

    // 3.修改库存
    boolean success = seckillVoucherService
        .update().setSql("stock = stock - 1")
        .eq("voucher_id", voucherId).gt("stock", 0)
        .update();
    if (!success) {
        log.error("库存不足");
    }

    // 4.创建订单
    save(voucherOrder);
}
```





**完整代码**

```java
@Service
@Slf4j
public class VoucherOrderServiceImpl extends ServiceImpl<VoucherOrderMapper, VoucherOrder> implements IVoucherOrderService {

    @Autowired
    private ISeckillVoucherService seckillVoucherService;
    @Autowired
    private RedisIdWork redisIdWork;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private RedissonClient redissonClient;

    private static final DefaultRedisScript<Long> SECKILL_SCRIPT;
    static {
        SECKILL_SCRIPT = new DefaultRedisScript<>();
        SECKILL_SCRIPT.setLocation(new ClassPathResource("seckillVoucher.lua"));
        SECKILL_SCRIPT.setResultType(Long.class);
    }

    // 创建阻塞队列，并指定队列大小
    // 当一个线程尝试从阻塞队列里获取元素的时候，如果没有元素，那么该线程就会被阻塞，直到队列中有元素，才会被唤醒，并去获取元素
    private BlockingQueue<VoucherOrder> orderTasks = new ArrayBlockingQueue<>(1024 * 1024);
    // 创建线程池，异步的从阻塞队列获取元素并完成下单操作
    private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();
    // 将代理对象作为类的属性，因此异步线程执行时，无法获取代理对象，所以在主线程中获取到代理对象后，在异步线程中可以直接使用
    private IVoucherOrderService proxy;

    // @PostConstruct表示在类初始化后，就立刻执行这个方法
    // 因此类初始化后，这个异步线程就开始执行下单任务，但是如果阻塞队列没有元素，这个线程就会阻塞，不会消耗cpu资源
    @PostConstruct
    public void init() {
        SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
    }

    // 创建线程任务
    private class VoucherOrderHandler implements Runnable {
        @Override
        public void run() {
            try {
                // 1.获取订单信息
                VoucherOrder voucherOrder = orderTasks.take();
                // 2.完成下单任务
                // 获取锁
                RLock lock = redissonClient.getLock("lock:order:" + voucherOrder.getUserId());
                // tryLock可以添加三个参数，分别是: 获取锁的最大等待时间(超时重试，默认-1表示失败就返回)、锁自动释放时间(默认30s)、时间单位
                boolean success = lock.tryLock();
                // 获取锁失败
                if (!success) {
                    log.error("不允许重复下单");
                }
                // 获取锁成功
                try {
                    // 因此AopContext中也是通过ThreadLocal获取元素的，这里是异步线程，所以获取不到代理对象
                    proxy.createOrder(voucherOrder);
                } finally {
                    lock.unlock();
                }
            } catch (Exception e) {
                log.error("下单操作异常:", e);
            }
        }
    }

    @Override
    public Result seckillVoucher(Long voucherId) {
        Long userId = UserHolder.getUser().getId();
        // 1.执行lua脚本
        Long result = stringRedisTemplate.execute(SECKILL_SCRIPT,
                Collections.emptyList(),
                voucherId.toString(), userId.toString());

        // 2.判断结果是否为0
        if (result.intValue() != 0) {
            return Result.fail(result.intValue() == 1 ? "库存不足" : "不能重复下单");
        }

        // 3. 将优惠券信息保存到阻塞队列
        VoucherOrder voucherOrder = new VoucherOrder();
        long orderId = redisIdWork.nextId("order");
        voucherOrder.setId(orderId);
        voucherOrder.setUserId(userId);
        voucherOrder.setVoucherId(voucherId);
        orderTasks.add(voucherOrder);

        try {
            // 这里先获取到代理对象，以供异步线程使用
            proxy = (IVoucherOrderService) AopContext.currentProxy();
        } catch (IllegalStateException e) {
            e.printStackTrace();
        }

        // 4.返回订单id
        return Result.ok(orderId);
    }

    @Transactional
    @Override
    public void createOrder(VoucherOrder voucherOrder) {
        // 1.现在是异步线程，所以通过ThreadLocal是获取不到用户的
        Long userId = voucherOrder.getUserId();
        Long voucherId = voucherOrder.getVoucherId();

        // 下边两个操作不需要判断了，但是防止redis出问题，所以我们再次判断
        // 2.判断用户是否下过单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        if (count > 0) {
            log.error("不能重复下单");
        }

        // 3.修改库存
        boolean success = seckillVoucherService
                .update().setSql("stock = stock - 1")
                .eq("voucher_id", voucherId).gt("stock", 0)
                .update();
        if (!success) {
            log.error("库存不足");
        }

        // 4.创建订单
        save(voucherOrder);
    }
}
```





#### 6.4 阻塞队列存在的问题

1、内存限制问题：

- 我们现在使用的是JDK里的阻塞队列，它使用的是JVM的内存，如果在高并发的条件下，无数的订单都会放在阻塞队列里，可能就会造成内存溢出，所以我们在创建阻塞队列时，设置了一个长度，但是如果真的存满了，再有新的订单来往里塞，那就塞不进去了，存在内存限制问题

2、数据安全问题：

- 经典服务器宕机了，因此创建订单的任务就结束了。用户明明下单了，但是数据库里没看到







### 7、消息队列

消息队列，字面意思就是存放消息的队列，最简单的消息队列模型包括3个角色

- 消息队列：存储和管理消息，也被称为消息代理（Message Broker）

- 生产者：发送消息到消息队列

- 消费者：从消息队列获取消息并处理消息

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310111601921.png" style="zoom:80%;" />



那么在这种场景下我们的秒杀就变成了：在我们下单之后，利用Redis去进行校验下单的结果，然后在通过队列把消息发送出去，然后在启动一个线程去拿到这个消息，完成解耦，同时也加快我们的响应速度。这样实现效果和阻塞队列相同，但是，消息队列是JVM之外的一个服务，不受内存限制，并且消息队列是可持久化的，也保证了数据安全。

我们可以直接使用一些现成的(MQ)消息队列，如kafka，rabbitmq等，这里我们使用Redis提供的MQ方案







#### 7.1 基于List的消息队列

Redis的list数据结构是一个双向链表，很容易模拟出队列效果。

队列是入口和出口不在一边，因此我们可以利用：`LPUSH` 结合 `RPOP`、或者 `RPUSH` 结合 `LPOP`来实现。

不过要注意的是，当队列中没有消息时`RPOP`或`LPOP`操作会返回`null`，并不像JVM的阻塞队列那样会阻塞并等待消息。因此这里应该使用**BRPOP**或者**BLPOP**来实现阻塞效果。



基于List的消息队列

**优点：**

* 利用Redis存储，不受限于JVM内存上限
* 基于Redis的持久化机制，数据安全性有保证
* 可以满足消息有序性

**缺点：**

* 无法避免消息丢失，取出数据后，redis服务宕机，这条数据就丢失了
* 只支持单消费者







#### 7.2 基于PubSub的消息队列

PubSub(发布订阅)是Redis2.0版本引入的消息传递模型。顾名思义，消费者可以订阅一个或多个channel，生产者向对应channel发送消息后，所有订阅者都能收到相关消息

- `SUBSCRIBE channel [channel]`：订阅一个或多个频道
- `PUBLISH channel msg`：向一个频道发送消息
- `PSUBSCRIBE pattern [pattern]`：订阅与pattern格式匹配的所有频道

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310121039461.png" style="zoom:67%;" />

**优点：**

* 采用发布订阅模型，支持多生产、多消费

**缺点：**

* 不支持数据持久化
* 无法避免消息丢失【如果当前channel没有人订阅，那么发送的消息就会丢失】
* 消息堆积有上限，超出时数据丢失【消费者的缓存空间有上限，如果突然收到大量消息，空间不够就会丢失】







#### 7.3 基于Stream的消息队列

Stream 是 Redis 5.0 引入的一种新数据类型【因为它是一种数据类型，所以可以实现持久化】，可以实现一个功能非常完善的消息队列。



**发送消息**

> 1、NOMKSTREAM
>
> - 如果队列不存在，是否自动创建队列，默认是自动创建
>
> 2、[MAXLEN|MINID [=!~] threshold [LIMIT count]]
>
> - 设置消息队列的最大消息数量，不设置则无上限
>
> 3、*|ID
>
> - 消息的唯一id，*代表由Redis自动生成。格式是"时间戳-递增数字"，例如"114514114514-0"
>
> 4、field value [field value …]
>
> - 发送到队列中的消息，称为Entry。格式就是多个key-value键值对

```redis
XADD key [NOMKSTREAM] [MAXLEN|MINID [=!~] threshold [LIMIT count]] *|ID field value [field value ...]
```

```redis
# 创建名为users的队列，并向其中发送一个消息，内容是{name=jack, age=21}，并且使用Redis自动生成ID
XADD users * name jack age 21
```





**读取消息**

> 1、[COUNT count]
>
> - 每次读取消息的最大数量
>
> 2、[BLOCK milliseconds]
>
> - 当没有消息时，是否阻塞，阻塞时长，0表示永久阻塞
>
> 3、STREAMS key [key …]
>
> - 要从哪个队列读取消息，key就是队列名
>
> 4、ID [ID …]
>
> - 起始ID，只返回大于该ID的消息
>   - 0：表示从第一个消息开始
>   - $：表示从最新的消息开始

```
XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] ID [ID ...]
```

```
# 读取第一条消息
xread count 1 streams users 0

# XREAD阻塞方式，读取最新消息
XREAD COUNT 1 BLOCK 10000 STREAMS users $
```





**注意**

当我们指定起始ID为`$`时，代表只能读取到最新消息，如果当我们在处理一条消息的过程中，又有超过1条以上的消息到达队列，那么下次获取的时候，也只能获取到最新的一条，会出现**漏读消息**的问题



STREAM类型消息队列的`XREAD`命令特点

- 消息可回溯【消息读取后不会删除】

- 一个消息可以被多个消费者读取

- 可以阻塞读取

- 有漏读消息的风险







#### 7.4 Stream-消费者组

消费者组（Consumer Group）：将多个消费者划分到一个组中，监听同一个队列。具备下列特点：

1、**消息分流**

- 队列中的消息会分流给组内的不同消费者，而不是重复消费者，从而加快消息处理的速度。如果想多次读取，在创建一个组读即可

2、**消息标识**

- 消费者会维护一个标识，记录最后一个被处理的消息，哪怕消费者宕机重启，还会从标识之后读取消息，确保每一个消息都会被消费

3、**消息确认**

- 消费者获取消息后，消息处于pending状态，并存入一个`pending-list`，当处理完成后，需要通过`XACK`来确认消息，标记消息为已处理，才会从pending-list中移除





**创建消费者组**

> 1、key：队列名称
>
> 2、groupName：消费者组名称
>
> 3、ID：起始ID标识，`$`代表队列中的最后一个消息，`0`代表队列中的第一个消息
>
> 4、MKSTREAM：队列不存在时自动创建队列

```
# 创建消费者组
XGROUP CREATE key groupName ID [MKSTREAM]

# 删除消费者组
XGROUP DESTORY key groupName

# 给指定的消费者组添加消费者
XGROUP CREATECONSUMER key groupName consumerName

# 删除消费者组中指定的消费者
XGROUP DELCONSUMER key groupName consumerName
```





**从消费者组中读取消息**

> 1、group：消费者组名称
>
> 2、consumer：消费者名，如果消费者不存在，会自动创建一个消费者
>
> 3、count：本次查询的最大数量
>
> 4、BLOCK milliseconds：当前没有消息时的最大等待时间
>
> 5、NOACK：无需手动ACK，获取到消息后自动确认（一般不用，我们都是手动确认）
>
> 6、STREAMS key：指定队列名称
>
> 7、ID：获取消息的起始ID
>
> - `>`：从下一个未消费的消息开始
> - 其他：根据指定id从pending-list中获取已消费但未确认的消息，例如0，是从pending-list中的第一个消息开始

```
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [keys ...] ID [ID ...]
```



```
# 创建一个消费者组，队列是s1，组名是g1，从队列中第一条消息开始读
xgroup create s1 g1 0

# 消费者c1在队列s1，组g1中读取消息，一次读1条，阻塞时间为2s，此时消息读取完之后放在了pending-list中
xreadgroup group g1 c1 count 1 block 2000 streams s1 >

# 查看队列s1，组g1中pending-list的消息，(start,end)表示从队列取出后未消费的时间范围，- + 表示任意时刻的消息，10表示最多取10条
xpending s1 g1 - + 10

# 将队列s1，组g1中id为1697100057380-0的消息消费完毕后，确认，此时该消息会从pending-list移出
xack s1 g1 1697100057380-0

# 从队列s1，组g1的pending-list中读取第1条消息
xreadgroup group g1 c1 count 1 block 2000 streams s1 0
```





**java代码实现消费者监听消息**

```java
while(true){
    // 尝试监听队列，使用阻塞模式，最大等待时长为2000ms
    Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >")
    if(msg == null){
        // 没监听到消息，重试
        continue;
    }
    try{
        //处理消息，完成后要手动确认ACK，ACK代码在handleMessage中编写
        handleMessage(msg);
    } catch(Exception e){
        while(true){
            //0表示从pending-list中的第一个消息开始，如果前面都ACK了，那么这里就不会监听到消息
            Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 STREAMS s1 0");
            if(msg == null){
                //null表示没有异常消息，所有消息均已确认，结束循环
                break;
            }
            try{
                //说明有异常消息，再次处理
                handleMessage(msg);
            } catch(Exception e){
                //再次出现异常，记录日志，继续循环
                log.error("..");
                continue;
            }
        }
    }
}
```





**STREAM**类型消息队列的`XREADGROUP`命令的特点

- 消息可回溯

- 可以多消费者争抢消息，加快消费速度

- 可以阻塞读取

- 没有消息漏读风险

- 有消息确认机制，保证消息至少被消费一次



|              |                   List                    |       PubSub       | Stream                                                  |
| :----------: | :---------------------------------------: | :----------------: | ------------------------------------------------------- |
|  消息持久化  |                   支持                    |       不支持       | 支持                                                    |
|   阻塞读取   |                   支持                    |        支持        | 支持                                                    |
| 消息堆积处理 | 受限于内存空间， 可以利用多消费者加快处理 | 受限于消费者缓冲区 | 受限于队列长度， 可以利用消费者组提高消费速度，减少堆积 |
| 消息确认机制 |                  不支持                   |       不支持       | 支持                                                    |
|   消息回溯   |                  不支持                   |       不支持       | 支持                                                    |







#### 7.5 消息队列优化秒杀业务

**需求**

1、创建一个Stream类型的消息队列，名为stream.orders。`xgroup create stream.orders g1 0 MKSTREAM`

2、修改之前的秒杀下单Lua脚本，在认定有抢购资格后，直接向stream.orders中添加消息，内容包含voucherId、userId、orderId

3、项目启动时，开启一个线程任务，尝试获取stream.orders中的消息，完成下单



**lua脚本**

```lua
-- 需要的参数
-- 优惠券id
local voucherId = ARGV[1]
-- 用户id
local userId = ARGV[2]
-- 订单id
local orderId = ARGV[3]
-- 优惠券key
local voucherKey = "seckill:stock:" .. voucherId
-- 订单key
local orderKey = "seckill:order:" .. voucherId

-- 1.判断库存是否充足
if (tonumber(redis.call("get", voucherKey)) <= 0) then
    return 1
end

-- 2.判断用户是否下过单
if (redis.call("sismember", orderKey, userId) == 1) then
    return 2
end

-- 3.库存-1
redis.call("incrby", voucherKey, -1)

-- 4.保存用户id
redis.call("sadd", orderKey, userId)

-- 5.将订单信息发送到消息队列中
redis.call("xadd", "stream.orders", "*", "userId", userId, "voucherId", voucherId, "id", orderId)
return 0
```



**优化业务代码**

```java
@Service
@Slf4j
public class VoucherOrderServiceImpl extends ServiceImpl<VoucherOrderMapper, VoucherOrder> implements IVoucherOrderService {

    @Autowired
    private ISeckillVoucherService seckillVoucherService;
    @Autowired
    private RedisIdWork redisIdWork;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private RedissonClient redissonClient;

    private static final DefaultRedisScript<Long> SECKILL_SCRIPT;
    static {
        SECKILL_SCRIPT = new DefaultRedisScript<>();
        SECKILL_SCRIPT.setLocation(new ClassPathResource("seckillVoucher.lua"));
        SECKILL_SCRIPT.setResultType(Long.class);
    }

    // 创建线程池，异步的从队列获取元素并完成下单操作
    private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();
    // 将代理对象作为类的属性，因此异步线程执行时，无法获取代理对象，所以在主线程中获取到代理对象后，在异步线程中可以直接使用
    private IVoucherOrderService proxy;

    // @PostConstruct表示在类初始化后，就立刻执行这个方法
    // 因此类初始化后，这个异步线程就开始执行下单任务，但是如果阻塞队列没有元素，这个线程就会阻塞，不会消耗cpu资源
    @PostConstruct
    public void init() {
        SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
    }

    private class VoucherOrderHandler implements Runnable {
        private String queueName = "stream.orders";

        @Override
        public void run() {
            while (true) {
                try {
                    // 1.从消息队列中读取消息  xreadgroup group g1 c1 count 1 block 2000 streams stream.orders >
                    List<MapRecord<String, Object, Object>> records = stringRedisTemplate.opsForStream().read(
                            Consumer.from("g1", "c1"),
                            StreamReadOptions.empty().count(1).block(Duration.ofSeconds(2)),
                            StreamOffset.create(queueName, ReadOffset.lastConsumed()));

                    // 2.没有读到消息，就continue
                    if (records == null || records.isEmpty()) {
                        continue;
                    }

                    // 3.处理消息
                    // 3.1将订单信息从records中解析出来，其3个参数分别为id,key,value
                    Map<Object, Object> value = records.get(0).getValue();
                    VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);

                    // 3.2处理消息
                    proxy.createOrder(voucherOrder);

                    // 4.手动ack  xack s1 g1 id
                    stringRedisTemplate.opsForStream().acknowledge(queueName, "g1", records.get(0).getId());
                } catch (Exception e) {
                    log.error("订单处理异常:", e);
                    // 去pending-list中处理消息
                    handlePendingList();
                }
            }
        }

        private void handlePendingList() {
            while (true) {
                try {
                    // 1.从消息队列中读取消息  xreadgroup group g1 c1 count 1 streams stream.orders 0
                    List<MapRecord<String, Object, Object>> records = stringRedisTemplate.opsForStream().read(
                            Consumer.from("g1", "c1"),
                            StreamReadOptions.empty().count(1),
                            StreamOffset.create(queueName, ReadOffset.from("0")));

                    // 2.没有读到消息，说明pending-list中没有异常消息，直接break
                    if (records == null || records.isEmpty()) {
                        break;
                    }

                    // 3.处理消息
                    // 3.1将订单信息从records中解析出来，其3个参数分别为id,key,value
                    Map<Object, Object> value = records.get(0).getValue();
                    VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);

                    // 3.2处理消息
                    proxy.createOrder(voucherOrder);

                    // 4.手动ack  xack s1 g1 id
                    stringRedisTemplate.opsForStream().acknowledge(queueName, "g1", records.get(0).getId());
                } catch (Exception e) {
                    log.error("pending-list处理异常:", e);
                }
            }
        }
    }

    @Override
    public Result seckillVoucher(Long voucherId) {
        Long userId = UserHolder.getUser().getId();
        long orderId = redisIdWork.nextId("order");
        // 1.执行lua脚本
        Long result = stringRedisTemplate.execute(SECKILL_SCRIPT,
                Collections.emptyList(),
                voucherId.toString(), userId.toString(), String.valueOf(orderId));

        // 2.判断结果是否为0
        if (result.intValue() != 0) {
            return Result.fail(result.intValue() == 1 ? "库存不足" : "不能重复下单");
        }

        try {
            // 这里先获取到代理对象，以供异步线程使用
            proxy = (IVoucherOrderService) AopContext.currentProxy();
        } catch (IllegalStateException e) {
            e.printStackTrace();
        }

        // 4.返回订单id
        return Result.ok(orderId);
    }

    @Transactional
    @Override
    public void createOrder(VoucherOrder voucherOrder) {
        // 1.现在是异步线程，所以通过ThreadLocal是获取不到用户的
        Long userId = voucherOrder.getUserId();
        Long voucherId = voucherOrder.getVoucherId();

        // 下边两个操作不需要判断了，但是防止redis出问题，所以我们再次判断
        // 2.判断用户是否下过单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        if (count > 0) {
            log.error("不能重复下单");
        }

        // 3.修改库存
        boolean success = seckillVoucherService
                .update().setSql("stock = stock - 1")
                .eq("voucher_id", voucherId).gt("stock", 0)
                .update();
        if (!success) {
            log.error("库存不足");
        }

        // 4.创建订单
        save(voucherOrder);
    }
}
```





#### 7.6 使用MQ优化

1、执行lua脚本，判断库存是否充足、用户是否重复下单

2、如果判断用户可以下单，那么将消息发送到消息队列，然后返回订单id

3、消费者监听`order.queue`队列，监听到消息后，执行创建订单的操作。

```java
@Service
@Slf4j
public class VoucherOrderServiceImpl extends ServiceImpl<VoucherOrderMapper, VoucherOrder> implements IVoucherOrderService {

    @Autowired
    private ISeckillVoucherService seckillVoucherService;
    @Autowired
    private RedisIdWork redisIdWork;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private RedissonClient redissonClient;
    @Autowired
    private RabbitTemplate rabbitTemplate;

    private static final DefaultRedisScript<Long> SECKILL_SCRIPT;
    static {
        SECKILL_SCRIPT = new DefaultRedisScript<>();
        SECKILL_SCRIPT.setLocation(new ClassPathResource("seckillVoucher-2.lua"));
        SECKILL_SCRIPT.setResultType(Long.class);
    }

    // 将代理对象作为类的属性，因此异步线程执行时，无法获取代理对象，所以在主线程中获取到代理对象后，在异步线程中可以直接使用
    private IVoucherOrderService proxy;

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(value = "order.queue", durable = "true"),
            exchange = @Exchange(value = "order.direct", durable = "true", type = ExchangeTypes.DIRECT),
            key = "order"
    ))
    public void listenerOrder(VoucherOrder voucherOrder) {
        System.out.println(voucherOrder.toString());
        proxy.createOrder(voucherOrder);
    }

    @Override
    public Result seckillVoucher(Long voucherId) {
        Long userId = UserHolder.getUser().getId();
        long orderId = redisIdWork.nextId("order");
        // 1.执行lua脚本
        Long result = stringRedisTemplate.execute(SECKILL_SCRIPT,
                Collections.emptyList(),
                voucherId.toString(), userId.toString(), String.valueOf(orderId));

        // 2.判断结果是否为0
        if (result.intValue() != 0) {
            return Result.fail(result.intValue() == 1 ? "库存不足" : "不能重复下单");
        }

        // 3.发送消息到消息队列
        VoucherOrder voucherOrder = new VoucherOrder();
        voucherOrder.setUserId(userId);
        voucherOrder.setVoucherId(voucherId);
        voucherOrder.setId(orderId);
        rabbitTemplate.convertAndSend("order.direct", "order", voucherOrder);

        try {
            // 这里先获取到代理对象，以供异步线程使用
            proxy = (IVoucherOrderService) AopContext.currentProxy();
        } catch (IllegalStateException e) {
            e.printStackTrace();
        }

        // 4.返回订单id
        return Result.ok(orderId);
    }

    @Transactional
    @Override
    public void createOrder(VoucherOrder voucherOrder) {
        // 1.现在是异步线程，所以通过ThreadLocal是获取不到用户的
        Long userId = voucherOrder.getUserId();
        Long voucherId = voucherOrder.getVoucherId();

        // 下边两个操作不需要判断了，但是防止redis出问题，所以我们再次判断
        // 2.判断用户是否下过单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        if (count > 0) {
            log.error("不能重复下单");
        }

        // 3.修改库存
        boolean success = seckillVoucherService
                .update().setSql("stock = stock - 1")
                .eq("voucher_id", voucherId).gt("stock", 0)
                .update();
        if (!success) {
            log.error("库存不足");
        }

        // 4.创建订单
        save(voucherOrder);
    }
}
```









### 8、达人探店

#### 8.1 发布笔记

> 探店笔记类似点评网站的评价，往往是图文结合。对应的表有两个：
>
> tb_blog：探店笔记表，包含笔记中的标题、文字、图片等
>
> tb_blog_comments：其他用户对探店笔记的评价
>
> 这里我们将上传图片和发布笔记做成了两个接口，即上传图片后会直接发起`upload/blog`请求，将图片保存到服务器，然后点击发布按钮就发起`blog`请求，保存笔记。



**上传图片接口**

```java
@PostMapping("blog")
public Result uploadImage(@RequestParam("file") MultipartFile image) {
    try {
        // 获取原始文件名称
        String originalFilename = image.getOriginalFilename();
        // 生成新文件名
        String fileName = createNewFileName(originalFilename);
        // 保存文件
        image.transferTo(new File(SystemConstants.IMAGE_UPLOAD_DIR, fileName));
        // 返回结果
        log.debug("文件上传成功，{}", fileName);
        return Result.ok(fileName);
    } catch (IOException e) {
        throw new RuntimeException("文件上传失败", e);
    }
}
```



**发布笔记**

```java
@PostMapping
public Result saveBlog(@RequestBody Blog blog) {
    // 获取登录用户
    UserDTO user = UserHolder.getUser();
    blog.setUserId(user.getId());
    // 保存探店博文
    blogService.save(blog);
    // 返回id
    return Result.ok(blog.getId());
}
```





#### 8.2 查看笔记

> 点击笔记会调用`blog/id`接口，查询笔记详情，然后返回。
>
> 查询到blog后，我们通过blog中的userId，将作者信息也添加进去

```java
@Override
public Result getBlogById(Integer id) {
    // 查询blog
    Blog blog = query().eq("id", id).one();

    // 查询user
    Long userId = blog.getUserId();
    User user = userService.getById(userId);
    blog.setName(user.getNickName());
    blog.setIcon(user.getIcon());

    return Result.ok(blog);
}
```







#### 8.3 点赞功能

> 1、我们点击点赞按钮，会发起`blog/like/id`请求，对相应的blog进行点赞，但是一个用户可以无限点赞，而实际情况是，一个用户只能对一篇blog点赞一次。
>
> 2、为了解决这个问题，我们使用redis来解决。对于一篇blog，我们将对其点赞的用户保存到redis中。其中key是blog的id，value是一个set，set中保存了用户的id。每次点赞时，我们查询redis，如果用户未点赞，那么我们将该blog点赞数+1，同时将用户id保存到redis中；如果用户点过赞了，就是取消点赞，那么就将该blog点赞数-1，同时将用户id从redis中删除。
>
> 3、为了前端方便展示当前用户是否对某个blog点过赞，我们在Blog的实体类中添加一个`isLike`属性，表示当前登录用户是否对该blog点赞，如果点赞了，那么就高亮显示，没点赞就不高亮。因此在查询blog时，我们还需要查询当前用户是否对blog点赞了，点过赞就将`isLike`字段设为`true`



**点赞**

```java
@Override
public Result likeBlog(Long id) {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    String key = RedisConstants.BLOG_LIKED_KEY + id;

    // 2.在redis中判断当前用户是否对当前blog点赞
    Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
    if (!isMember) {
        // 3.没有点赞
        boolean success = update().setSql("liked = liked + 1").eq("id", id).update();
        if (success) {
            stringRedisTemplate.opsForSet().add(key, userId.toString());
        }
    } else {
        // 4.点过赞
        boolean success = update().setSql("liked = liked - 1").eq("id", id).update();
        if (success) {
            stringRedisTemplate.opsForSet().remove(key, userId.toString());
        }
    }
    return Result.ok();
}
```



**判断用户是否点赞**

> 在通过id查询blog，和分页查询blog时，调用该方法。

```java
private void userLikeBlog(Blog blog) {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    String key = RedisConstants.BLOG_LIKED_KEY + blog.getId();

    // 2.判断当前用户是否对当前blog点赞
    Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
    blog.setIsLike(BooleanUtil.isTrue(isMember));
}
```







#### 8.4 点赞排行榜

> 1、当我们点击探店笔记详情页面时，应该按点赞顺序展示点赞用户【类似朋友圈点赞】，比如显示最早点赞的TOP5，形成点赞排行榜
>
> 2、之前的点赞是放到Set集合中，但是Set集合又不能排序，所以这个时候，我们就可以改用`SortedSet`
>
> - 由于`ZSet`没有`isMember`方法，所以这里只能通过查询score来判断集合中是否有该元素，如果有该元素，则返回值是对应的score，如果没有该元素，则返回值为null
>
> 3、显示点赞列表的接口是` http://localhost:8080/api/blog/likes/id`，这里我们显示最早点赞的5个人
>
> - 需要注意的是，我们从redis中获取到前5个人的id后，此时id的顺序是按点赞先后顺序排的，保存在ids中，查询数据库的用户信息时，执行的sql语句为` select * from tb_user where id in(5, 1) `，虽然在`in(5, 1)`中id顺序是对的，但是从数据库中查出的结果却是`(1, 5)`这样排的。因此我们需要通过`order by`去排序，` select * from tb_user where id in(5, 1) order by field(id, 5, 1)`。



**点赞功能**

```java
@Override
public Result likeBlog(Long id) {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    String key = RedisConstants.BLOG_LIKED_KEY + id;

    // 2.在redis中判断当前用户是否对当前blog点赞
    Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
    if (score == null) {
        // 3.没有点赞
        boolean success = update().setSql("liked = liked + 1").eq("id", id).update();
        if (success) {
            stringRedisTemplate.opsForZSet().add(key, userId.toString(), System.currentTimeMillis());
        }
    } else {
        // 4.点过赞
        boolean success = update().setSql("liked = liked - 1").eq("id", id).update();
        if (success) {
            stringRedisTemplate.opsForZSet().remove(key, userId.toString());
        }
    }
    return Result.ok();
}
```



**判断用户是否点赞**

```java
private void userLikeBlog(Blog blog) {
    UserDTO user = UserHolder.getUser();
    if (user == null)
        return;

    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    String key = RedisConstants.BLOG_LIKED_KEY + blog.getId();

    // 2.判断当前用户是否对当前blog点赞
    Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
    blog.setIsLike(BooleanUtil.isTrue(score != null));
}
```





**查看点赞列表**

```java
// controller层
@GetMapping("/likes/{id}")
public Result likes(@PathVariable("id") Long id) {
    return blogService.likes(id);
}

// service层
@Override
public Result likes(Long id) {
    String key = RedisConstants.BLOG_LIKED_KEY + id;
    Set<String> top5 = stringRedisTemplate.opsForZSet().range(key, 0, 4);
    if (top5 == null || top5.isEmpty()) {
        return Result.ok(Collections.emptyList());
    }

    List<Long> ids = top5.stream().map(Long::valueOf).collect(Collectors.toList());
    String strIds = StrUtil.join(",", ids);
    // select * from tb_user where id in(5, 1) order by field(id, 5, 1)
    // 如果不通过order by指定的话，我们查询条件时in(5,1)，但是结果却是先显示1，在显示5
    List<UserDTO> userDTOS = userService.query().in("id", ids).last("order by field(id," + strIds + ")").
        list().stream().
        map(user -> BeanUtil.copyProperties(user, UserDTO.class)).
        collect(Collectors.toList());

    return Result.ok(userDTOS);
}
```







### 9、好友关注

#### 9.1 关注和取关

> 当我们查看笔记详情时，可以选择关注用户，和取消关注用户。如果当前已关注，那么在点一下就是取消关注，如果当前未关注，再点一下就是关注。因此在进入这个页面时，首先会发起一个请求，判断当前用户是否关注该作者，然后前端根据是否关注，进行页面的渲染。
>
> 我们将用户的关注信息放在`tb_follow`表中，`user_id`是粉丝id，`follow_user_id`是关注的用户



**Controller**

```java
@RestController
@RequestMapping("/follow")
public class FollowController {

    @Resource
    private IFollowService followService;

    // 关注或取关操作
    @PutMapping("{id}/{isFollow}")
    public Result follow(@PathVariable("id") Long id, @PathVariable("isFollow") Boolean isFollow) {
        return followService.follow(id, isFollow);
    }

    // 判断当前用户是否关注当前作者
    @GetMapping("or/not/{id}")
    public Result isFollow(@PathVariable("id") Long id) {
        return followService.isFollow(id);
    }
}
```



**Service**

```java
@Service
public class FollowServiceImpl extends ServiceImpl<FollowMapper, Follow> implements IFollowService {
    @Autowired
    private IUserService userService;

    @Override
    public Result follow(Long id, Boolean isFollow) {
        Long userId = UserHolder.getUser().getId();

        if (isFollow) {
            // 关注
            Follow follow = new Follow();
            follow.setUserId(userId);
            follow.setFollowUserId(id);
            save(follow);
        } else {
            // 取关
            remove(new QueryWrapper<Follow>()
                    .eq("user_id", userId).eq("follow_user_id", id));
        }
        return Result.ok();
    }

    @Override
    public Result isFollow(Long id) {
        Long userId = UserHolder.getUser().getId();
        Integer count = query().eq("user_id", userId).eq("follow_user_id", id).count();
        return Result.ok(count > 0);
    }
}
```





#### 9.2 共同关注

> 当我们进入某个用户首页时，我们可以查看该用户基本信息，发布的笔记，以及共同关注的用户。
>
> 对于共同关注，我们可以将每个用户的关注通过redis放入set中，然后查找共同关注时，只需要查看两个set的交集即可。



**根据id查询用户**

```java
@GetMapping("{id}")
public Result getUserById(@PathVariable("id") Long id) {
    User user = userService.getById(id);
    if (user == null) {
        return Result.fail("用户不存在");
    }

    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    return Result.ok(userDTO);
}
```



**根据用户id查询笔记**

```java
@GetMapping("/of/user")
public Result queryBlogByUserId(
        @RequestParam(value = "current", defaultValue = "1") Integer current,
        @RequestParam("id") Long id) {
    // 根据用户查询
    Page<Blog> page = blogService.query()
            .eq("user_id", id).page(new Page<>(current, SystemConstants.MAX_PAGE_SIZE));
    // 获取当前页数据
    List<Blog> records = page.getRecords();
    return Result.ok(records);
}
```



**共同关注**

```java
@Service
public class FollowServiceImpl extends ServiceImpl<FollowMapper, Follow> implements IFollowService {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private IUserService userService;

    @Override
    public Result follow(Long id, Boolean isFollow) {
        Long userId = UserHolder.getUser().getId();

        if (isFollow) {
            // 关注
            Follow follow = new Follow();
            follow.setUserId(userId);
            follow.setFollowUserId(id);
            boolean success = save(follow);

            if (success) {
                stringRedisTemplate.opsForSet().add("follow:user:" + userId, id.toString());
            }
        } else {
            // 取关
            boolean success = remove(new QueryWrapper<Follow>()
                    .eq("user_id", userId).eq("follow_user_id", id));
            if (success) {
                stringRedisTemplate.opsForSet().remove("follow:user:" + userId, id.toString());
            }
        }
        return Result.ok();
    }

    @Override
    public Result isFollow(Long id) {
        Long userId = UserHolder.getUser().getId();
        Integer count = query().eq("user_id", userId).eq("follow_user_id", id).count();
        return Result.ok(count > 0);
    }

    @Override
    public Result commonFollow(Long id) {
        Long userId = UserHolder.getUser().getId();

        Set<String> common = stringRedisTemplate.opsForSet().
                intersect("follow:user:" + userId.toString(), "follow:user:" + id.toString());

        if (common == null || common.isEmpty()) {
            return Result.ok(Collections.emptyList());
        }

        // 3.解析id集合
        List<Long> ids = common.stream().map(Long::valueOf).collect(Collectors.toList());
        // 4.查询用户
        List<UserDTO> users = userService.listByIds(ids)
                .stream()
                .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
                .collect(Collectors.toList());
        return Result.ok(users);
    }
}
```







#### 9.3 feed流

> 1、当我们关注了用户后，这个用户发了动态，那么我们应该把这些数据推送给用户，这个需求，叫做Feed流，直译为投喂。为用户持续的提供“沉浸式”的体验，通过无限下拉刷新获取新的信息。
>
> 2、对于传统的模式的内容解锁：我们是需要用户去通过搜索引擎或者是其他的方式去解锁想要看的内容
>
> 3、对于新型的Feed流的的效果：不需要我们用户再去搜索信息，而是系统分析用户到底想要什么，然后直接把内容推送给用户，从而使用户能够更加的节约时间，不用主动去寻找



**Feed流的两种常见模式：**

`Timeline`：不做内容筛选，简单的按照内容发布时间排序，常用于好友或关注。例如朋友圈

* 优点：信息全面，不会有缺失。并且实现也相对简单
* 缺点：信息噪音较多，用户不一定感兴趣，内容获取效率低

智能排序：利用智能算法屏蔽掉违规的、用户不感兴趣的内容，推送用户感兴趣信息来吸引用户

* 优点：投喂用户感兴趣信息，用户粘度很高，容易沉迷
* 缺点：如果算法不精准，可能起到反作用



在该项目中，用户可以查看其关注的博主发送的全部笔记，这里我们使用`Timeline`模式，该模式的实现方案有三种：

* 拉模式
* 推模式
* 推拉结合



**拉模式**

> 也叫读扩散。该模式的核心含义就是：当张三和李四和王五发了消息后，都会保存在自己的发件箱中，假设赵六要读取信息，那么他会读取他自己的收件箱，此时系统会从他关注的人群中，把他关注人的信息全部都进行拉取，然后在进行排序
>
> 优点：比较节约空间，因为赵六在读信息时，并没有重复读取，而且读取完之后可以把他的收件箱进行清理。
>
> 缺点：延迟较大，当用户读取数据时才去关注的人的收件箱里读取数据，假设用户关注了大量的用户，那么此时就会拉取海量的内容，对服务器压力巨大。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310151728119.png" style="zoom:67%;" />







**推模式**

> 也叫写扩散。推模式是没有写邮箱的，当张三写了一个内容后，他会主动的把写的内容发送到他的粉丝收件箱中去，假设此时李四再来读取，就不用再去临时拉取了
>
> 优点：时效快，不用临时拉取
>
> 缺点：内存压力大，假设一个大V写信息，很多人关注他， 就会写很多份数据到粉丝那边去

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310151730775.png" style="zoom:67%;" />



**推拉结合模式**

> 也叫做读写混合，兼具推和拉两种模式的优点。
>
> 推拉模式是一个折中的方案，站在发件人这一段，如果是个普通的人，那么我们采用写扩散的方式，直接把数据写入到他粉丝的收件箱中去，因为普通的人他的粉丝关注量比较小，所以这样做没有压力，如果是大V，那么他是直接将数据先写到发件箱，然后再直接写一份到活跃粉丝收件箱里边去。
>
> 站在收件人这端来看，如果是活跃粉丝，那么大V和普通的人发的都会直接写入到自己收件箱里边来，而如果是普通的粉丝，由于他们上线不是很频繁，所以等他们上线时，再从发件箱里边去拉信息。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202310151731338.png" style="zoom:67%;" />







#### 9.4 推送到粉丝收件箱

> 现在我们的需求是：
>
> 1、在保存blog到数据库的同时，将blog推送到粉丝的收件箱
>
> 2、收件箱满足可以根据时间戳排序，必须用Redis的数据结构实现
>
> 3、查询收件箱数据时，可以实现分页查询
>
> 因为Feed流中的数据是不断变化的，所以不能采用传统的分页模式。例如，在t1时刻，查询到了最新的5条数据，然后突然新增2条新数据，此时查询第二页时，就会有2条是查询过的。
>
> Feed流的滚动分页：我们需要记录每次操作的最后一条，然后从这个位置开始去读取数据，所以我们使用redis中的`SortdSet`



```java
@Override
public Result saveBlog(Blog blog) {
    // 1.获取登录用户
    UserDTO user = UserHolder.getUser();
    blog.setUserId(user.getId());
    // 2.保存探店博文
    boolean success = save(blog);
    if (!success) {
        return Result.fail("保存blog失败");
    }

    // 3.获取登录用户的所有粉丝
    List<Follow> follows = followService.query().eq("follow_user_id", user.getId()).list();

    // 4.将blog推送给所有粉丝
    for (Follow follow : follows) {
        Long userId = follow.getUserId();
        stringRedisTemplate.opsForZSet().
                add(RedisConstants.FEED_KEY + userId, blog.getId().toString(), System.currentTimeMillis());
    }

    // 5.返回id
    return Result.ok(blog.getId());
}
```







#### 9.5 分页查询收件箱

> 需求：在个人主页的关注栏中，查询并展示推送的Blog信息

具体步骤如下

1、每次查询完成之后，我们要分析出查询出的最小时间戳，这个值会作为下一次的查询条件

2、我们需要找到与上一次查询相同的查询个数，并作为偏移量，下次查询的时候，跳过这些查询过的数据，拿到我们需要的数据（例如时间戳8 6 6 5 5 4，我们每次查询3个，第一次是8 6 6，此时最小时间戳是6，如果不设置偏移量，会从第一个6之后开始查询，那么查询到的就是6 5 5，而不是5 5 4）

3、因此我们的请求参数中需要携带`lastId`和`offset`，即上一次查询时的最小时间戳和偏移量



**redis的`SortedSet`根据分数查询**

> max是分数最大值，min是分数最小值
>
> `WITHSCORES`表示是否查询分数
>
> `LIMIT OFFSET COUNT`中，`OFFSET`表示偏移量，即从小于等于`max`的第几个数开始，`COUNT`为查询的数量
>
> 因为我们的分数是通过时间戳指定的，所以min的值直接取0即可，而max的值，第一次查询时，设置为当前时间戳，后边查询时，指定为上次查询的时间戳最小值。`offset`在第一次查询时，设定为0，即从小于等于max的第一个数开始，后边查询时，设置为上次查询到的最小值的个数。

```redis
ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT OFFSET COUNT]
```



**定义返回的实体类**

```java
@Data
public class ScrollResult {
    private List<?> list;
    private Long minTime;
    private Integer offset;
}
```



**Controller**

```java
@GetMapping("/of/follow")
public Result queryBlogOfFollow(
        @RequestParam("lastId") Long max, @RequestParam(value = "offset", defaultValue = "0") Integer offset) {
    return blogService.queryBlogOfFollow(max, offset);
}
```



**Service**

```java
@Override
public Result queryBlogOfFollow(Long max, Integer offset) {
    // 1.查询当前用户
    Long userId = UserHolder.getUser().getId();

    // 2.查询当前用户收件箱 ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT OFFSET COUNT]
    Set<ZSetOperations.TypedTuple<String>> typedTuples = stringRedisTemplate.opsForZSet().reverseRangeByScoreWithScores(
            RedisConstants.FEED_KEY + userId, 0, max, offset, 2);

    if (typedTuples == null || typedTuples.isEmpty()) {
        return Result.ok(Collections.emptyList());
    }

    // 3.解析数据
    List<Long> ids = new ArrayList<>(typedTuples.size());
    long minTime = 0;
    int cnt = 1;
    for (ZSetOperations.TypedTuple<String> typedTuple : typedTuples) {
        ids.add(Long.valueOf(typedTuple.getValue()));

        long time = typedTuple.getScore().longValue();
        if (time == minTime) {
            cnt++;
        } else {
            minTime = time;
            cnt = 1;
        }
    }

    // 解决SQL的in不能排序问题，手动指定排序为传入的ids
    String strIds = StrUtil.join(",", ids);
    List<Blog> blogs = query().in("id", ids).last("order by field(id," + strIds + ")").list();

    // 4.查询blog的用户以及是否喜欢
    for (Blog blog : blogs) {
        // 查询user
        Long id = blog.getUserId();
        User user = userService.getById(id);
        blog.setName(user.getNickName());
        blog.setIcon(user.getIcon());

        // 判断当前用户是否对该blog点赞
        userLikeBlog(blog);
    }

    // 5.返回数据
    ScrollResult result = new ScrollResult();
    result.setList(blogs);
    result.setMinTime(minTime);
    result.setOffset(cnt);
    return Result.ok(result);
}
```









### 10、附近商户

#### 10.1 GEO数据结构用法

> GEO就是Geolocation的简写形式，代表地理坐标。Redis在3.2版本中加入了对GEO的支持，允许存储地理坐标信息，帮助我们根据经纬度来检索数据
>
> GEO本质上就是一个`SortedSet`，其中`value`就是指定的member，`score`就是通过经纬度计算的



**常用命令**

- `GEOADD`：添加一个地理空间信息，包含：经度（longitude）、纬度（latitude）、值（member）

  ```
  GEOADD key longitude latitude member [longitude latitude member …]
  
  # key为china，添加了上海和北京的经纬度
  GEOADD china 13.361389 38.115556 'shanghai' 15.087269 37.502669 "beijing"
  ```

- `GEODIST`：计算指定的两个点之间的距离并返回

  ```
  GEODIST key member1 member2 [m|km|ft|mi]
  
  # 计算北京和上海之间的距离，单位默认是m
  GEODIST china beijing shanghai
  ```

- `GEOHASH`：将指定member的坐标转化为hash字符串形式并返回

  ```
  GEOHASH key member [member …]
  
  # 得到北京坐标的hash值
  GEOHASH china beijing
  ```

- `GEOPOS`：返回指定member的坐标

  ```
  GEOPOS key member [member …]
  
  # 得到北京的坐标
  GEOPOS china beijing
  ```

- `GEOGADIUS`：指定圆心、半径，找到该圆内包含的所有member，并按照与圆心之间的距离排序后返回，6.2之后已废弃

  ```sh
  GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count [ANY]] [ASC|DESC] [STORE key] [STOREDIST key]
  ```

- `GEOSEARCH`：在指定范围内搜索member，并按照与指定点之间的距离排序后返回，范围可以使圆形或矩形，6.2的新功能

  > 1、`FROMMEMBER member` 和 `FROMLONLAT longitude latitude`：`FROMMEMBER member`表示从member中选择一个作为圆心，`FROMLONLAT longitude latitude`表示根据指定的经纬度作为圆心
  >
  > 2、`BYRADIUS radius m|km|ft|mi`：使用圆形，指定半径
  >
  > 3、`BYBOX width height m|km|ft|mi`：使用矩形，指定长和宽
  >
  > 4、`ASC|DESC`：根据距离的大小升序/降序排序
  >
  > 5、`COUNT count`：返回的数量，默认返回所有元素
  >
  > 6、`WITHCOORD`：将位置元素的经度和纬度也一并返回
  >
  > 7、`WITHDIST`：在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回
  >
  > 8、`WITHHASH`：返回位置元素经过原始 geohash 编码的有序集合分值

  ```
  GEOSEARCH key [FROMMEMBER member] [FROMLONLAT longitude latitude] [BYRADIUS radius m|km|ft|mi] [BYBOX width height m|km|ft|mi] [ASC|DESC] [COUNT count [ANY]] [WITHCOORD] [WITHDIST] [WITHHASH]
  
  # 在china这个key中，查找以(20.2,18.0)为圆心，半径5000km内的所有member
  GEOSEARCH china FROMLONLAT 20.2 18.0 BYRADIUS 5000.0 km WITHDIST
  
  1) 1) "shanghai"
     2) "2334.0271"
  2) 1) "beijing"
     2) "2225.8039"
  ```

- `GEOSEARCHSTORE`：与`GEOSEARCH`功能一致，不过可以把结果存储到一个指定的key，也是6.2的新功能

  > `destination `：目标键名，用于存储地理位置数据
  >
  > `source`：源键名，用于指定要复制地理位置数据的源键

  ```
  GEOSEARCHSTORE destination source [FROMMEMBER member] [FROMLONLAT longitude latitude] 
  [BYRADIUS radius m|km|ft|mi] [BYBOX width height m|km|ft|mi] 
  [ASC|DESC] [COUNT count [ANY]] [STOREDIST]
  ```

  





#### 10.2 将商铺数据导入redis

> 1、当我们点击美食后，会出现一系列的商家，他们可以按照多种方式排序，此时我们想要的是根据距离排序，我们可以使用GEO来实现该功能，以当前坐标为圆心，查看附近商户。
>
> 2、将数据库中的数据导入到Redis中去，GEO在Redis中就是一个member和一个经纬度，经纬度对应的就是tb_shop中的x和y，而member，我们用shop_id来存
>
> 3、但是此时还有一个问题，我们在redis中没有存储shop_type，无法根据店铺类型来对数据进行筛选，解决办法就是将type_id作为key，存入同一个GEO集合即可



**将数据导入redis**

```java
@Test
public void loadData() {
    // 1.读取所有店铺信息
    List<Shop> shops = shopService.list();

    // 2.对店铺进行分组  key是店铺类型，value是该类型所有店铺
    Map<Long, List<Shop>> map = shops.stream().collect(Collectors.groupingBy(shop -> shop.getTypeId()));

    // 3.将店铺信息导入redis
    for (Map.Entry<Long, List<Shop>> entry : map.entrySet()) {
        Long typeId = entry.getKey();
        String key = "shop:geo:" + typeId;

        List<Shop> list = entry.getValue();
        List<RedisGeoCommands.GeoLocation<String>> locations = new ArrayList<>(list.size());

        for (Shop shop : list) {
            // stringRedisTemplate.opsForGeo().add(key, new Point(shop.getX(), shop.getY()), shop.getId().toString());
            locations.add(new RedisGeoCommands.GeoLocation<>(shop.getId().toString(), new Point(shop.getX(), shop.getY())));
        }
        stringRedisTemplate.opsForGeo().add(key, locations);
    }
}
```







#### 10.3 实现附近商户功能

> SpringDataRedis的2.3.9版本并不支持Redis 6.2提供的GEOSEARCH命令，因此我们需要提升其版本，修改自己的pom.xml文件

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <exclusions>
        <exclusion>
            <artifactId>spring-data-redis</artifactId>
            <groupId>org.springframework.data</groupId>
        </exclusion>
        <exclusion>
            <artifactId>lettuce-core</artifactId>
            <groupId>io.lettuce</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>2.6.2</version>
</dependency>
<dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <version>6.1.6.RELEASE</version>
</dependency>
```



**Controller**

```java
@GetMapping("/of/type")
public Result queryShopByType(
        @RequestParam("typeId") Integer typeId,
        @RequestParam(value = "current", defaultValue = "1") Integer current,
        @RequestParam(value = "x", required = false) Double x, @RequestParam(value = "y", required = false) Double y
) {
    return shopService.quertShopByType(typeId, current, x, y);
}
```



**Service**

```java
@Override
public Result quertShopByType(Integer typeId, Integer current, Double x, Double y) {
    // 1.判断是否需要根据距离查询
    if (x == null || y == null) {
        // 根据类型分页查询
        Page<Shop> page = query()
                .eq("type_id", typeId)
                .page(new Page<>(current, SystemConstants.DEFAULT_PAGE_SIZE));
        return Result.ok(page);
    }

    // 2.计算分页查询参数
    int from = (current - 1) * SystemConstants.DEFAULT_PAGE_SIZE;
    int end = current * SystemConstants.DEFAULT_PAGE_SIZE;
    String key = RedisConstants.SHOP_GEO_KEY + typeId;

    // 3.查询redis，按照距离排序、分页；结果: shopId，distance
    // GEOSEARCH key FROMLONLAT x y BYRADIUS 5000 m WITHDIST
    GeoResults<RedisGeoCommands.GeoLocation<String>> results = stringRedisTemplate.opsForGeo().search(
            key,
            GeoReference.fromCoordinate(x, y),
            new Distance(5000),
            RedisGeoCommands.GeoSearchCommandArgs.newGeoSearchArgs().includeDistance().limit(end)
    );
    if (results == null) {
        return Result.ok(Collections.emptyList());
    }

    // 4.解析shopId
    List<GeoResult<RedisGeoCommands.GeoLocation<String>>> list = results.getContent();
    if (list.size() < from) {
        // 需要的数据是从from-end，如果现在的数据总条数小于等于from，就返回空
        return Result.ok(Collections.emptyList());
    }
    ArrayList<Long> ids = new ArrayList<>(list.size());
    HashMap<String, Distance> distanceMap = new HashMap<>(list.size());

    // 4.1现在得到的是前end条数据，我们想要的是from-end之间的数据，对数据进行截取
    list.stream().skip(from).forEach(result -> {
        String shopIdStr = result.getContent().getName();
        ids.add(Long.valueOf(shopIdStr));
        Distance distance = result.getDistance();
        distanceMap.put(shopIdStr, distance);
    });

    // 5.根据shopId查询店铺数据
    String idsStr = StrUtil.join(",", ids);
    List<Shop> shops = query().in("id", ids).last("ORDER BY FIELD( id," + idsStr + ")").list();
    for (Shop shop : shops) {
        //设置shop的距离属性，从distanceMap中根据shopId查询
        shop.setDistance(distanceMap.get(shop.getId().toString()).getValue());
    }
    //6. 返回
    return Result.ok(shops);
}
```







### 11、用户签到

#### 11.1 BitMap介绍

> 1、数据库中，有`tb_sign`表，用户签到一次，就往数据库表中添加一条记录。假如有1000W用户，每人每年签到10次，那这张表一年的数据量就有1亿条
>
> 2、我们可以使用二进制位来记录每天的签到情况，签到记录为1，未签到记录为0，这样一个人一个月的数据也就是31个bit
>
> 3、Redis中是利用String来实现BitMap，因此最大上限是512M，转换为bit则是2^32个bit位



**BitMap常用命令**

- `SETBIT`：向指定位置（offset）存入一个0或1

  > offset是偏移量，即二进制的第几位，value为0或1

  ```
  SETBIT key offset value
  ```

- `GETBIT`：获取指定位置（offset）的bit值

  ```
  GETBIT key offset
  ```

- `BITCOUNT`：统计BitMap中值为1的bit位的数量

  ```
  BITCOUNT key [start end]
  ```

- `BITFIELD`：操作（查询、修改、自增）BitMap中bit数组中的指定位置（offset）的值，一般用来查询，可查询多个

  > `type`用来指定取几位，以及返回值类型【`i`为有符号，`u`为无符号】，`offset`表示从第几位开始取

  ```
  BITFIELD key [GET type offset] [SET type offset value] [INCRBY type offset increment] [OVERFLOW WRAP|SAT|FAIL]
  
  # 从第1位开始取，取3位，返回值为无符号整数
  bitfield bm1 get u3 1
  ```

- `BITFIELD_RO`：获取BitMap中bit数组，并以十进制形式返回

- `BITOP`：将多个BitMap的结果做位运算（与、或、异或）

- `BITPOS`：查找bit数组中指定范围内第一个0或1出现的位置





#### 11.2 用户签到

> 用户签到的请求地址是`http://localhost:8081/user/sign`，请求参数为空。因为用户签到时，只需要获取当前用户id，以及当前时间即可，将当前`用户id+年月`作为bitMap的key，当前`天`作为offset，签到就赋值true即可。



**Controller**

```JAVA
@PostMapping("/sign")
public Result sign() {
    return userService.sign();
}
```



**Service**

```java
@Override
public Result sign() {
    // 1.获取当前用户
    Long userId = UserHolder.getUser().getId();
    
    // 2.获取当前日期
    LocalDateTime now = LocalDateTime.now();

    // 3.拼接key
    String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = RedisConstants.USER_SIGN_KEY + userId + keySuffix;
    
    // 4.获取当前天, 返回的是1-31
    int day = now.getDayOfMonth();

    // 5.存入redis
    stringRedisTemplate.opsForValue().setBit(key, day - 1, true);
    return Result.ok();
}
```







#### 11.3 连续签到统计

> 我们希望统计出用户连续签到的天数，即从今天开始往前数，连续签到的天数。
>
> 我们可以通过`BITFIELD`命令获取用户当前月签到的记录，返回值是一个十进制数字，我们判断其二进制下，最后一位依次往前连续1的个数即可



**Controller**

```java
@GetMapping("/sign/count")
public Result signCount() {
    return userService.signCount();
}
```



**Service**

```java
@Override
public Result signCount() {
    // 1.获取当前用户
    Long userId = UserHolder.getUser().getId();

    // 2.获取当前日期
    LocalDateTime now = LocalDateTime.now();

    // 3.拼接key
    String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = RedisConstants.USER_SIGN_KEY + userId + keySuffix;

    // 4.获取当前天, 返回的是1-31
    int day = now.getDayOfMonth();

    // 5.获取当前月从0开始到今天所有签到记录，返回值是十进制  bitfield sign:1015:202310 get u17 0
    List<Long> list = stringRedisTemplate.opsForValue().bitField(
            key,
            BitFieldSubCommands.create().get(BitFieldSubCommands.BitFieldType.unsigned(day)).valueAt(0)
    );
    if (list == null || list.isEmpty()) {
        return Result.ok(0);
    }

    Long signs = list.get(0);
    if (signs == null || signs == 0) {
        return Result.ok(0);
    }

    // 6.统计连续签到天数
    int cnt = 0;
    while (true) {
        if ((signs & 1) == 0) {
            break;
        } else {
            cnt++;
        }
        signs >>>= 1;
    }
    return Result.ok(cnt);
}
```







### 12、UV统计

* **UV**：全称Unique Visitor，也叫独立访客量，是指通过互联网访问、浏览这个网页的自然人。1天内同一个用户多次访问该网站，只记录1次。
* **PV**：全称Page View，也叫页面访问量或点击量，用户每访问网站的一个页面，记录1次PV，用户多次打开页面，则记录多次PV。往往用来衡量网站的流量。



> 1、UV统计在服务端做会比较麻烦，因为要判断该用户是否已经统计过了，需要将统计过的用户信息保存。但是如果每个访问的用户都保存到Redis中，数据量会非常恐怖。
>
> 2、`Hyperloglog(HLL)`是从Loglog算法派生的概率算法，用于确定非常大的集合的基数，而不需要存储其所有值。相关算法原理：https://juejin.cn/post/6844903785744056333#heading-0
>
> 3、Redis中的HLL是基于string结构实现的，单个HLL的内存**永远小于16kb**，作为代价，其测量结果是概率性的，**有小于0.81％的误差**。不过对于UV统计来说，这完全可以忽略。



**常用命令：**

- `PFADD key element [element...]`：添加元素，元素不会重复

  ```
  pfadd hl1 e1 e2 e3 e3
  ```

- `PFCOUNT key [key...]`：计算`key`中元素个数

  ```
  pfcount hl1
  ```

- `PFMERGE destkey sourcekey [sourcekey...]`：将多个`key`合并



**模拟百万数据的统计**

> 我们插入1000000条数据，实际统计到了997593条，误差为0.24%可以接受，占用内存大小12kb

```java
@Test
public void hyperloglog() {
    String[] users = new String[1000];
    for (int i = 0; i < 1000000; i++) {
        int j = i % 1000;
        users[j] = "user_" + i;
        if (j == 999) {
            stringRedisTemplate.opsForHyperLogLog().add("hl1", users);
        }
    }
    Long count = stringRedisTemplate.opsForHyperLogLog().size("hl1");
    System.out.println(count);  // 997593
}
```







## 五、分布式缓存

单机的Redis存在四个问题：

- 数据丢失
- 并发能力问题
- 存储能力问题
- 故障恢复问题

为了解决以上问题，就需要使用Redis集群

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111350512.png" style="zoom:50%;" />





### 1、Redis持久化

Redis有两种持久化方案：

- RDB持久化
- AOF持久化



#### 1.1 RDB持久化

RDB全称Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。简单来说就是把内存中的所有数据都记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。快照文件称为RDB文件，默认是保存在当前运行目录。



RDB持久化在四种情况下会执行

- 执行save命令

  save命令会导致主进程执行RDB，这个过程中其它所有命令都会被阻塞。只有在数据迁移时可能用到。

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111352828.png" style="zoom:50%;" />

- 执行bgsave命令

  命令执行后会开启独立进程完成RDB，主进程可以持续处理用户请求，不受影响。

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111353352.png" style="zoom:50%;" />

- Redis停机时

  Redis停机时会执行一次save命令，实现RDB持久化。下图是我们使用`redis-cli shutdown`命令关闭Redis服务时，控制台的输出，可以看到是通过RDB将数据保存到磁盘上

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111150528.png)

  通过命令`redis-server redis.conf`启动Redis时，通过加载RDB文件，将数据加载到内存

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111151489.png)

- 触发RDB条件时

  Redis内部有触发RDB的机制，可以在redis.conf文件中找到，`save 3600 1`表示3600s内，至少有一个key被修改，则执行bgsave。我们设置为5s内，有一个key被修改，就执行bgsave。`save ""`表示禁用RDB

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111206142.png)

  当我们通过`set`命令添加或修改key时，由于我们设置了`save 5 1`，所以Redis控制台就会将数据保存到RDB文件

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111216781.png)



RDB的其他配置也可以在`redis.conf`文件设置

```properties
# 是否压缩 ,建议不开启，压缩也会消耗cpu，磁盘的话不值钱
rdbcompression yes

# RDB文件名称
dbfilename dump.rdb  

# 文件保存的路径目录
dir ./ 
```





#### 1.2 RDB原理

bgsave开始时会fork主进程得到子进程，子进程共享主进程的内存数据。完成fork后读取内存数据并写入 RDB 文件。

fork采用的是`copy-on-write`技术：

- 当主进程执行读操作时，访问共享内存；
- 当主进程执行写操作时，则会拷贝一份数据，执行写操作。最坏情况下，子进程在写磁盘时，主进程将Redis中所有数据均修改一遍，此时就会将Redis中数据复制一遍，占用两倍的内存

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111426399.png" style="zoom:57%;" />

RDB方式bgsave的基本流程

- fork主进程得到一个子进程，共享内存空间
- 子进程读取内存数据并写入新的RDB文件
- 用新RDB文件替换旧的RDB文件

RDB会在什么时候执行？save 60 1000代表什么含义？

- 默认是服务停止时
- 代表60秒内至少执行1000次修改则触发RDB

RDB的缺点？

- RDB执行间隔时间长，两次RDB之间写入数据有丢失的风险
- fork子进程、压缩、写出RDB文件都比较耗时







#### 1.3 AOF持久化

AOF全称为Append Only File（追加文件）。Redis处理的每一个写命令都会记录在AOF文件，可以看做是命令日志文件。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111430385.png" style="zoom:70%;" />



**AOF配置**

AOF默认是关闭的，需要修改redis.conf配置文件来开启AOF

```properties
# 是否开启AOF功能，默认是no
appendonly yes
# AOF文件的名称
appendfilename "appendonly.aof"
```

AOF的命令记录的频率也可以通过redis.conf文件来配

```properties
# 表示每执行一次写命令，立即记录到AOF文件
appendfsync always 
# 写命令执行完先放入AOF缓冲区，然后后台线程每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendfsync everysec 
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111437891.png" style="zoom:87%;" />





我们开启AOF功能后，执行以下命令，然后打开`appendonly.aof`文件就可以看到其中记录了我们执行的命令

```sh
set name xrj
set age 20
set name xyy
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111434445.png)

然后我们重启Redis服务，可以发现重启后Redis会去加载AOF文件来恢复数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111434005.png)







#### 1.4 AOF文件重写

因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过执行`bgrewriteaof`命令，可以让AOF文件执行重写功能，用最少的命令达到相同效果。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111439243.png)



Redis也会在触发阈值时自动去重写AOF文件。在redis.conf中可以配置，默认配置如下

```properties
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写 
auto-aof-rewrite-min-size 64mb 
```



我们执行`bgrewriteaof`命令后，再次查看`appendonly.aof`文件，可以发现里边原本的三条命令被重写了，同时进行了压缩

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111440532.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111441628.png)





**RDB和AOF对比**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111443162.png" style="zoom:80%;" />









## 六、Redis主从

主从结构，读写分离，主节点写数据，从节点读数据

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111502701.png" style="zoom:60%;" />





### 1、集群搭建

#### 1.1 准备实例

共包含三个节点，一个主节点，两个从节点。我们在同一台虚拟机中开启3个redis实例，模拟主从集群，信息如下

|       IP        | PORT |  角色  |
| :-------------: | :--: | :----: |
| 192.168.150.101 | 7001 | master |
| 192.168.150.101 | 7002 | slave  |
| 192.168.150.101 | 7003 | slave  |



**1、创建目录**

我们创建三个文件夹，名字分别叫7001、7002、7003，然后将redis.conf中持久化方式改为RDB，将配置文件拷贝到每个文件夹下

```sh
echo 7001 7002 7003 | xargs -t -n 1 cp redis-6.2.4/redis.conf
```



**2、修改每个实例的端口和工作目录**

修改每个文件夹内的配置文件，将端口分别修改为7001、7002、7003，将RDB文件保存位置都修改为自己所在目录

```sh
sed -i -e 's/6379/7001/g' -e 's/dir .\//dir \/home\/xrj\/redis\/7001\//g' 7001/redis.conf
sed -i -e 's/6379/7002/g' -e 's/dir .\//dir \/home\/xrj\/redis\/7002\//g' 7002/redis.conf
sed -i -e 's/6379/7003/g' -e 's/dir .\//dir \/home\/xrj\/redis\/7003\//g' 7003/redis.conf
```



**3、修改每个实例的声明ip**

虚拟机本身有多个IP，为了避免将来混乱，我们需要在redis.conf文件中指定每一个实例的绑定ip信息

```properties
# redis实例的声明 IP
replica-announce-ip 192.168.198.130
```

执行命令修改

```sh
sed -i '1a replica-announce-ip 192.168.198.130' 7001/redis.conf
sed -i '1a replica-announce-ip 192.168.198.130' 7002/redis.conf
sed -i '1a replica-announce-ip 192.168.198.130' 7003/redis.conf
```



**4、遇到的错误**

- 如果主节点有密码，从节点`redis.conf`文件中的`masterauth xxx`应该设置主节点密码
- 从节点连接主节点时，报错`Failed opening the RDB file dump.rdb`，修改工作目录的权限即可





#### 1.2 启动

```sh
# 第1个
redis-server 7001/redis.conf
# 第2个
redis-server 7002/redis.conf
# 第3个
redis-server 7003/redis.conf
```





#### 1.3 开启主从关系

现在三个实例还没有任何关系，要配置主从可以使用`replicaof `或者`slaveof`（5.0以前）命令。

有临时和永久两种模式：

- 修改配置文件（永久生效）

  - 在`redis.conf`中添加一行配置：```slaveof <masterip> <masterport>```

- 使用`redis-cli`客户端连接到redis服务，执行`slaveof`命令（重启后失效）：

  ```sh
  slaveof <masterip> <masterport>
  ```



我们将7002和7003配置为从节点

```sh
# 登录7002
redis-cli -p 7002 -a 123456
# 设置主从关系
slaveof 192.168.198.130 7001

# 登录7003
redis-cli -p 7003 -a 123456
# 设置主从关系
slaveof 192.168.198.130 7001
```



设置完后，我们登录7001主节点，可以通过`info replication`查看主从关系，可以看到当前7001为主节点，有两个从节点7002和7003

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111620081.png)



#### 1.4 测试

我们向主节点通过`set`修改或写入数据，在从节点可以查看到对应数据，但是在从节点写入数据，会报错`READONLY You can't write against a read only replica.`  说明只有主节点可以写数据，从节点只能读数据







### 2、主从数据同步原理

#### 2.1 全量同步

主从第一次建立连接时，会执行**全量同步**，将master节点的所有数据都拷贝给slave节点

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111641217.png" style="zoom:70%;" />





**master如何得知salve是第一次来连接**

- `Replication Id`：简称replid，是数据集的标记，id一致则说明是同一数据集。每一个master都有唯一的replid，slave则会继承master节点的replid
- `offset`：偏移量，随着记录在repl_baklog中的数据增多而逐渐增大。slave完成同步时也会记录当前同步的offset。如果slave的offset小于master的offset，说明slave数据落后于master，需要更新。



> 因此slave做数据同步，必须向master声明自己的replication id 和offset，master才可以判断到底需要同步哪些数据。
>
> 因为slave原本也是一个master，有自己的replid和offset，当第一次变成slave，与master建立连接时，发送的replid和offset是自己的replid和offset。
>
> master判断发现slave发送来的replid与自己的不一致，说明这是一个全新的slave，就知道要做全量同步了。
>
> master会将自己的replid和offset都发送给这个slave，slave保存这些信息。以后slave的replid就与master一致了。
>
> 因此，**master判断一个节点是否是第一次同步的依据，就是看replid是否一致**。



**设置主从时的日志**

主节点：

1、收到来自`192.168.198.130:7002`的请求

2、判断replid是否一致，因为是第一次来，replid不一致，所以将自己的replid和offset发送过去

3、执行`bgsave`创建RDB文件，发送给从节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111645350.png)

从节点：

1、连接主节点

2、请求增量更新，带上自己的replid，因为是第一次连接，所以会失败

3、收到主节点的全量更新及其replid

4、开始同步数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111645348.png)



完整流程：

- slave节点请求增量同步
- master节点判断replid，发现不一致，拒绝增量同步
- master将完整内存数据生成RDB，发送RDB到slave
- slave清空本地数据，加载master的RDB
- master将RDB期间的命令记录在repl_baklog，并持续将log中的命令发送给slave
- slave执行接收到的命令，保持与master之间的同步







#### 2.2 增量同步

全量同步需要先做RDB，然后将RDB文件通过网络传输给slave，成本太高。因此除了第一次做全量同步，其它大多数时候slave与master都是做**增量同步**。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111701877.png" style="zoom:67%;" />





**repl_backlog原理**

`repl_backlog`文件是一个固定大小的数组，只不过数组是环形，也就是说**角标到达数组末尾后，会再次从0开始读写**，这样数组头部的数据就会被覆盖【类似redolog】

`repl_baklog`中会记录Redis处理过的命令日志及offset，包括master当前的offset，和slave已经拷贝到的offset

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111703977.png)

slave与master的offset之间的差异，就是salve需要增量拷贝的数据了。

随着不断有数据写入，master的offset逐渐变大，slave也不断的拷贝，追赶master的offset



如果slave出现网络阻塞，导致master的offset远远超过了slave的offset，如果master继续写入新数据，其offset就会覆盖旧的数据，直到将slave现在的offset也覆盖，棕色框中的红色部分，就是尚未同步，但是却已经被覆盖的数据。此时如果slave恢复，需要同步，却发现自己的offset都没有了，无法完成增量同步了。只能做全量同步。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111704472.png)





**主从同步优化**

主从同步可以保证主从数据的一致性，可以从以下几个方面来优化Redis主从集群：

- 在master中配置`repl-diskless-sync yes`启用无磁盘复制，避免全量同步时的磁盘IO，这样全量同步时，就不会将数据写入RDB在发送，而是直接通过网络发送，前提是网络通畅
- Redis单节点上的内存占用不要太大，减少RDB导致的过多磁盘IO
- 适当提高repl_baklog的大小，发现slave宕机时尽快实现故障恢复，尽可能避免全量同步
- 限制一个master上的slave节点数量，如果实在是太多slave，则可以采用主-从-从链式结构，减少master压力

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111718186.png" style="zoom:50%;" />











### 3、Redis哨兵

Redis提供了哨兵（Sentinel）机制来实现主从集群的自动故障恢复。



#### 3.1 哨兵原理

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111743513.png" style="zoom: 50%;" />

哨兵的作用

- **监控**：Sentinel 会不断检查master和slave是否按预期工作
- **自动故障恢复**：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主
- **通知**：Sentinel充当Redis客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给Redis的客户端【因为Redis客户端写数据时需要写到主节点】





**集群监控原理**

Sentinel基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送ping命令：

- 主观下线：如果某sentinel节点发现某实例未在规定时间响应，则认为该实例**主观下线**。

- 客观下线：若超过指定数量（quorum）的sentinel都认为该实例主观下线，则该实例**客观下线**。quorum值最好超过Sentinel实例数量的一半。





**集群故障恢复原理**

一旦发现master故障，sentinel需要在salve中选择一个作为新的master，选择依据如下：

- 首先会判断slave节点与master节点断开时间长短，如果超过指定值（down-after-milliseconds * 10）则会排除该slave节点
- 然后判断slave节点的slave-priority值【默认都是1】，越小优先级越高，如果是0则永不参与选举
- 如果slave-prority一样，则判断slave节点的offset值，越大说明数据越新，优先级越高
- 最后是判断slave节点的运行id大小，越小优先级越高【此时相当于随机选一个，因为offset一样，数据也是一样的】。



当选出一个新的master后

- sentinel给备选的slave1节点发送`slaveof no one`命令，让该节点成为master
- sentinel给所有其它slave发送`slaveof 192.168.150.101 7002` 命令，让这些slave成为新master的从节点，开始从新的master上同步数据。对于故障节点来说，是直接修改其配置文件
- 最后，sentinel将故障节点标记为slave，当故障节点恢复后会自动成为新的master的slave节点

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111749006.png" style="zoom:50%;" />



#### 3.2 搭建哨兵集群

**1、集群结构**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111743513.png" style="zoom: 50%;" />

三个sentinel实例信息如下：

| 节点 |       IP        | PORT  |
| ---- | :-------------: | :---: |
| s1   | 192.168.198.130 | 27001 |
| s2   | 192.168.198.130 | 27002 |
| s3   | 192.168.198.130 | 27003 |





**2、准备实例和配置**

要在同一台虚拟机开启3个实例，准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。我们创建三个文件夹，名字分别叫s1、s2、s3



然后我们在s1目录创建一个sentinel.conf文件，添加下面的内容

```ini
port 27001
sentinel announce-ip 192.168.198.130
sentinel monitor mymaster 192.168.198.130 7001 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "/home/xrj/redis/s1"
sentinel auth-pass mymaster 123456
```

- `port 27001`：是当前sentinel实例的端口
- `sentinel monitor mymaster 192.168.198.130 7001 2`：指定主节点信息，从节点信息可以根据主节点获取
  - `mymaster`：主节点名称，自定义，任意写
  - `192.168.198.130 7001`：主节点的ip和端口
  - `2`：选举master时的quorum值

在s2和s3文件夹下同样创建该文件，不过需要将`port`和`dir`属性进行修改



**3、启动**

```sh
# 第1个
redis-sentinel s1/sentinel.conf
# 第2个
redis-sentinel s2/sentinel.conf
# 第3个
redis-sentinel s3/sentinel.conf
```

启动后可以看到Redis哨兵监听到了主节点，通过主节点又监听到了两个从节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111833104.png)







**4、测试**

此时我们将7001的主节点停掉。在哨兵的日志信息中，我们可以看到

1、通过`+sdown master mymaster 192.168.198.130 7001`发现主节点挂了，主观下线

2、`+odown master mymaster 192.168.198.130 7001 #quorum 2/2`两个哨兵都发现主节点挂了，此时是客观下线。

3、`+vote-for-leader e1bd4dbb8f9580e9cb2aac1a416f00a3525e6a9a 1`三个哨兵进行选主，谁先发现主节点挂了，谁去进行选举，此时第二个哨兵被选中，那么他去执行选主的操作

3、`+selected-slave slave 192.168.198.130:7002 192.168.198.130 7002 @ mymaster 192.168.198.130 7001`选7002作为主节点

4、`+failover-state-send-slaveof-noone slave 192.168.198.130:7002 192.168.198.130 7002 @ mymaster 192.168.198.130 7001`，通过`send-slaveof-noone`通知7002他现在是主节点

5、`+failover-state-reconf-slaves master mymaster 192.168.198.130 7001`，通过`reconf`标记7001为从节点

6、`+slave-reconf-sent slave 192.168.198.130:7003 192.168.198.130 7003 @ mymaster 192.168.198.130 7001`通知7003主节点当前为7002

7、当7001恢复后，他会作为从节点，主节点是7002

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111839099.png)



**7002的日志**

7002收到`send-slaveof-noone`命令后，会将自己变为主节点，通过`MASTER MODE enabled `可以看出

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111850425.png)



**7003的日志**

7003收到`slave-reconf-sent slave`命令后，会将7002作为自己的主节点，通过`REPLICAOF 192.168.198.130:7002 enabled`可以看出

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312111851330.png)







### 4、RedisTemplate

在Sentinel集群监管下的Redis主从集群，其节点会因为自动故障转移而发生变化，Redis的客户端必须感知这种变化，及时更新连接信息。Spring的RedisTemplate底层利用**lettuce**实现了节点的感知和自动切换。



在redis-demo项目中演示



**1、导入依赖**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



**2、配置redis地址**

这里配置的是哨兵的地址，通过哨兵我们可以得到Redis主节点以及从节点的地址

```yml
spring:
  redis:
    sentinel:
      master: mymaster
      nodes:
        - 192.168.198.130:27001
        - 192.168.198.130:27002
        - 192.168.198.130:27003
    password: 123456
```



**3、配置读写分类**

```java
@Bean
public LettuceClientConfigurationBuilderCustomizer clientConfigurationBuilderCustomizer() {
    return new LettuceClientConfigurationBuilderCustomizer() {
        @Override
        public void customize(LettuceClientConfiguration.LettuceClientConfigurationBuilder clientConfigurationBuilder) {
            clientConfigurationBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
        }
    };
}
```

- `MASTER`：从主节点读取
- `MASTER_PREFERRED`：优先从master节点读取，master不可用才读取replica
- `REPLICA`：从slave（replica）节点读取
- `REPLICA _PREFERRED`：优先从slave（replica）节点读取，所有的slave都不可用才读取master



**4、测试**

```java
@GetMapping("/get/{key}")
public String hi(@PathVariable String key) {
    return redisTemplate.opsForValue().get(key);
}

@GetMapping("/set/{key}/{value}")
public String hi(@PathVariable String key, @PathVariable String value) {
    redisTemplate.opsForValue().set(key, value);
    return "success";
}
```



我们有两个方法，一个是读操作，一个是写操作。

我们启动项目发送第一次请求时

1、与哨兵建立连接

2、通过哨兵发现主节点与从节点，与主节点以及从节点都建立连接

3、如果当前是写操作，就通过主节点写，如果是读操作，就按照我们配置的方式，优先使用从节点进行读







## 七、分片集群

主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：

- 海量数据存储问题

- 高并发写的问题

使用分片集群可以解决上述问题

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121431398.png" style="zoom:60%;" />

分片集群特征：

- 集群中有多个master，每个master保存不同数据

- 每个master都可以有多个slave节点

- master之间通过ping监测彼此健康状态【因此就不需要哨兵了】

- 客户端请求可以访问集群任意节点，最终都会被转发到正确节点





### 1、搭建分片集群

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121435000.png" style="zoom:70%;" />

这里我们用3个主节点，每个主节点包含一个从节点

|       IP        | PORT |  角色  |
| :-------------: | :--: | :----: |
| 192.168.198.130 | 7001 | master |
| 192.168.198.130 | 7002 | master |
| 192.168.198.130 | 7003 | master |
| 192.168.198.130 | 8001 | slave  |
| 192.168.198.130 | 8002 | slave  |
| 192.168.198.130 | 8003 | slave  |



#### 1.1 准备实例和配置

重新创建出7001、7002、7003、8001、8002、8003目录，7001、7002、7003是主节点，8001、8002、8003是从节点

在`/home/xrj/redis`目录下准备一个新的redis.conf文件，内容如下

```properties
port 6379
# 开启集群功能
cluster-enabled yes
# 集群的配置文件名称，不需要我们创建，由redis自己维护
cluster-config-file /home/xrj/redis/6379/nodes.conf
# 节点心跳失败的超时时间
cluster-node-timeout 5000
# 持久化文件存放目录
dir /home/xrj/redis/6379
# 绑定地址
bind 0.0.0.0
# 让redis后台运行
daemonize yes
# 注册的实例ip
replica-announce-ip 192.168.198.130
# 保护模式
protected-mode no
# 数据库数量
databases 1
# 日志
logfile /home/xrj/redis/6379/run.log
```

将这个文件拷贝到每一个目录

```sh
echo 7001 7002 7003 8001 8002 8003 | xargs -t -n 1 cp redis.conf
```

修改每个目录下的redis.conf，将其中的6379修改为与所在目录一致

```sh
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t sed -i 's/6379/{}/g' {}/redis.conf
```





#### 1.2 启动

```sh
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t redis-server {}/redis.conf
```



查看redis状态

```sh
ps -ef | grep redis
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121439257.png)





#### 1.3 创建集群

虽然服务启动了，但是目前每个服务之间都是独立的，没有任何关联。我们需要执行命令来创建集群

```sh
redis-cli --cluster create --cluster-replicas 1 192.168.198.130:7001 192.168.198.130:7002 192.168.198.130:7003 192.168.198.130:8001 192.168.198.130:8002 192.168.198.130:8003
```

- `redis-cli --cluster`：代表集群操作命令
- `create`：代表是创建集群
- `--cluster-replicas 1` ：指定集群中每个master的副本个数为1，此时`节点总数 / (replicas + 1)` 得到的就是master的数量。因此节点列表中的前n个就是master，其它节点都是slave节点，随机分配到不同master

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121443977.png)

输入yes，集群开始创建：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121444688.png)

查看集群状态

```sh
redis-cli -p 7001 cluster nodes
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121445373.png)







### 2、散列插槽

#### 2.1 插槽原理

Redis会把每一个master节点映射到0~16383共16384个【默认】插槽（hash slot）上，查看集群信息时就能看到7001的插槽为0-5460，7002的插槽为5461-10922,7003的插槽为70923-16383

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121458369.png)



数据key不是与节点绑定，而是与插槽绑定。redis会根据key的有效部分计算插槽值，分两种情况：

- key中包含`{}`，且`{}`中至少包含1个字符，`{}`中的部分是有效部分
- key中不包含`{}`，整个key都是有效部分

计算方式是利用CRC16算法得到一个hash值，然后对16384取余，得到的结果就是slot值。根据slot值就可以找到对应的节点【这样即使某一个master节点挂了，只需要将他的插槽移动到其他节点，下次根据插槽值会找到其他节点】



```sh
# 集群操作时，必须加上-c参数
redis-cli -c -p 7001

# key是name，我们登录的是7001，经过计算后，发现插槽在7002上，他就帮我们重定向到7002的节点，将数据存到7002上
set name xrj
# 现在我们在7002，操作的key为a，计算得到的插槽在7003，所以重定向到7003上
set a 1
# 现在我们在7003，操作的key为age，计算得到的插槽在7001，所以重定向到7001上
set age 20
# 下边get命令也是如此
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121501083.png)



**将同一类数据固定的保存在同一个Redis实例**

例如，我们希望将同一类别的数据保存到同一个节点上，此时我们就可以使用`{}`，里边填上相同的有效部分，例如key都以{typeId}为前缀，这样数据都会保存到同一个节点上







### 3、集群伸缩

`redis-cli --cluster`提供了很多操作集群的命令，可以通过`redis-cli --cluster help`查看



**需求**：

向集群中添加一个新的master节点，并向其中存储 num = 10

- 启动一个新的redis实例，端口为7004
- 添加7004到之前的集群，并作为一个master节点
- 给7004节点分配插槽，使得num这个key可以存储到7004实例



#### **3.1 创建redis实例**

1、创建文件夹7004

2、在7004中添加配置文件

```properties
port 7004
# 开启集群功能
cluster-enabled yes
# 集群的配置文件名称，不需要我们创建，由redis自己维护
cluster-config-file /home/xrj/redis/7004/nodes.conf
# 节点心跳失败的超时时间
cluster-node-timeout 5000
# 持久化文件存放目录
dir /home/xrj/redis/7004
# 绑定地址
bind 0.0.0.0
# 让redis后台运行
daemonize yes
# 注册的实例ip
replica-announce-ip 192.168.198.130
# 保护模式
protected-mode no
# 数据库数量
databases 1
# 日志
logfile /home/xrj/redis/7004/run.log
```

3、启动

```sh
redis-server 7004/redis.conf
```





#### 3.2 添加新节点到集群

添加节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121550610.png)

- `new host:new port`表示新节点的地址和端口
- `existing_host:existing_port`表示集群中任意一个节点的地址和端口

- `--cluster-slave`表示当前节点作为从节点【如果省略表示当前节点为主节点】
- `--cluster-master-id`表示如果当前节点是从节点，他的主节点是谁





**执行命令**

```sh
redis-cli --cluster add-node  192.168.198.130:7004 192.168.198.130:7001
```

查看节点状态可以发现，7004是主节点，但是没有分配插槽

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121554210.png)







#### 3.3 插槽转移

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121555920.png)

现在key为`num`的数据在2765这个插槽上，这个插槽属于7001，因此我们将7001的前3000个插槽转移到7004上



执行命令

```sh
redis-cli --cluster reshard 192.168.198.130:7001
```

1、移动多少个插槽，输入3000

2、哪个node接收这些插槽，输入7004的id

3、从哪里移动3000个插槽，输入7001的id

- all：代表全部，也就是三个节点各转移一部分
- 具体的id：目标节点的id
- done：没有了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121601704.png)



完成之后，我们再次查看，7004就拥有了0-2999的插槽

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121603921.png)







#### 3.4 转移插槽并删除节点

我们现在将7004的3000个插槽还给7001，并将7004从集群中删除。

移动插槽的操作和上边相同，这里就是将插槽从7004移动到7001



**删除节点**

这里带上需要删除节点的ip、端口以及节点的ID即可删除

```sh
redis-cli --cluster del-node 192.168.198.130:7004 e0e29788776056c24a293860bca57e5329c0dac5
```







### 4、故障转移

当前集群状态如下，7001、7002、7003为主节点，8001是7001的从节点，8002是7002的从节点，8003是7003的从节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121613900.png)



#### 4.1 自动故障转移

我们让7002节点停止

```sh
redis-cli -p 7002 shutdown
```



我们将7002节点停止后，可以发现当前7002状态变为`fail`，同时他的从节点8002变为了主节点，重启7002后，7002变为8002的从节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121617307.png)







#### 4.2 手动故障转移

利用`cluster failover`命令可以手动让集群中的某个master宕机，切换到执行`cluster failover`命令的这个slave节点，实现无感知的数据迁移。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121622911.png" style="zoom:80%;" />



`failover`命令可以指定三种模式：

- 缺省：默认的流程，如图1~6歩
- force：省略了对offset的一致性校验
- takeover：直接执行第5歩，忽略数据一致性、忽略master状态和其它master的意见



**需求**：

在7002这个slave节点执行手动故障转移，变回master节点



1、登录7002节点

2、执行`cluster failover`

3、执行后再次查看，发现7002变为主节点，8002变为7002的从节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121624519.png)







### 5、RedisTemplate

RedisTemplate底层同样基于lettuce实现了分片集群的支持，使用的步骤与哨兵模式基本一致：

1）引入redis的starter依赖

2）配置分片集群地址

只有第二步与哨兵模式有区别，哨兵模式配置的是哨兵地址，而分片集群配置的是所有redis节点地址

```yml
spring:
  redis:
    cluster:
      nodes:
        - 192.168.198.130:7001
        - 192.168.198.130:7002
        - 192.168.198.130:7003
        - 192.168.198.130:8001
        - 192.168.198.130:8002
        - 192.168.198.130:8003
```

3）配置读写分离

4）程序执行时，会先对key计算插槽，找到对应节点，如果是写操作就在主节点执行，读操作就在从节点执行







## 八、多级缓存

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121719878.png" style="zoom:67%;" />

传统的缓存策略一般是请求到达Tomcat后，先查询Redis，如果未命中则查询数据库

- 请求要经过Tomcat处理，Tomcat的性能成为整个系统的瓶颈

- Redis缓存失效时，会对数据库产生冲击



多级缓存就是充分利用请求处理的每个环节，分别添加缓存，减轻Tomcat压力，提升服务性能：

- 浏览器访问静态资源时，优先读取浏览器本地缓存
- 访问非静态资源（ajax查询数据）时，访问服务端
- 请求到达Nginx后，优先读取Nginx本地缓存，在多级缓存架构中，Nginx内部需要编写本地缓存查询、Redis查询、Tomcat查询的业务逻辑，因此这样的nginx服务不再是一个**反向代理服务器**，而是一个编写**业务的Web服务器了**。因此这样的业务Nginx服务也需要搭建集群来提高并发，再有专门的nginx服务来做反向代理
- 如果Nginx本地缓存未命中，则去直接查询Redis（不经过Tomcat）
- 如果Redis查询未命中，则查询Tomcat，Tomcat服务也可以部署为集群模式
- 请求进入Tomcat后，优先查询JVM进程缓存
- 如果JVM进程缓存未命中，则查询数据库

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121722643.png" style="zoom:50%;" />





### 1、JVM进程缓存

#### 1.1 Caffeine

- 分布式缓存，例如Redis：
  - 优点：存储容量更大、可靠性更好、可以在集群间共享
  - 缺点：访问缓存有网络开销
  - 场景：缓存数据量较大、可靠性要求较高、需要在集群间共享
- 进程本地缓存，例如HashMap、GuavaCache：
  - 优点：读取本地内存，没有网络开销，速度更快
  - 缺点：存储容量有限、可靠性较低、无法共享
  - 场景：性能要求较高，缓存数据量较小

**Caffeine**是一个基于Java8开发的，提供了近乎最佳命中率的高性能的本地缓存库。目前Spring内部的缓存使用的就是Caffeine。GitHub地址：https://github.com/ben-manes/caffeine



**Caffeine的使用**

```java
@Test
void testBasicOps() {
    // 构建cache对象
    Cache<String, String> cache = Caffeine.newBuilder().build();

    // 存数据
    cache.put("gf", "xyy");

    // 取数据
    String gf = cache.getIfPresent("gf");
    System.out.println("gf = " + gf);

    // 取数据，包含两个参数：
    // 参数一：缓存的key
    // 参数二：Lambda表达式，表达式参数就是缓存的key，方法体是查询数据库的逻辑
    // 优先根据key查询JVM缓存，如果未命中，则执行参数二的Lambda表达式，然后将数据保存到缓存
    String defaultGF = cache.get("defaultGF", key -> {
        // 根据key去数据库查询数据
        return "xrj";
    });
    System.out.println("defaultGF = " + defaultGF);
}
```





**Caffeine的缓存清除策略**

- **基于容量**：设置缓存的数量上限

  ```java
  // 创建缓存对象
  Cache<String, String> cache = Caffeine.newBuilder()
      .maximumSize(1) // 设置缓存大小上限为 1
      .build();
  ```

- **基于时间**：设置缓存的有效时间

  ```java
  // 创建缓存对象
  Cache<String, String> cache = Caffeine.newBuilder()
      // 设置缓存有效期为 10 秒，从最后一次写入开始计时 
      .expireAfterWrite(Duration.ofSeconds(10)) 
      .build();
  
  ```

- **基于引用**：设置缓存为软引用或弱引用，利用GC来回收缓存数据。性能较差，不建议使用。

> **注意**：在默认情况下，当一个缓存元素过期的时候，Caffeine不会自动立即将其清理和驱逐。而是在一次读或写操作后，或者在空闲时间完成对失效数据的驱逐







### 2、lua

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121805562.png" style="zoom:67%;" />



#### 2.1 声明变量

Lua声明变量的时候无需指定数据类型，而是用local来声明变量为局部变量：

```lua
-- 声明字符串，可以用单引号或双引号，
local str = 'hello'
-- 字符串拼接可以使用 ..
local str2 = 'hello' .. 'world'
-- 声明数字
local num = 21
-- 声明布尔类型
local flag = true
```



Lua中的table类型既可以作为数组，又可以作为Java中的map来使用。数组就是特殊的table，key是数组角标而已：

```lua
-- 声明数组 ，key为角标的 table
local arr = {'java', 'python', 'lua'}
-- 声明table，类似java的map
local map =  {name='Jack', age=21}
```

Lua中的数组角标是从1开始，访问的时候与Java中类似：

```lua
-- 访问数组，lua数组的角标从1开始
print(arr[1])
```

Lua中的table可以用key来访问：

```lua
-- 访问table
print(map['name'])
print(map.name)
```







#### 2.2 循环

对于table，我们可以利用for循环来遍历。不过数组和普通table遍历略有差异。

遍历数组：

```lua
-- 声明数组 key为索引的 table
local arr = {'java', 'python', 'lua'}
-- 遍历数组
for index,value in ipairs(arr) do
    print(index, value) 
end
```

遍历普通table

```lua
-- 声明map，也就是table
local map = {name='Jack', age=21}
-- 遍历table
for key,value in pairs(map) do
   print(key, value) 
end
```







#### 2.3 条件控制、函数

**函数**

定义函数的语法：

```lua
function 函数名( argument1, argument2..., argumentn)
    -- 函数体
    return 返回值
end
```



例如，定义一个函数，用来打印数组：

```lua
function printArr(arr)
    for index, value in ipairs(arr) do
        print(value)
    end
end
```



**条件控制**

类似Java的条件控制，例如if、else语法：

```lua
if(布尔表达式)
then
   --[ 布尔表达式为 true 时执行该语句块 --]
else
   --[ 布尔表达式为 false 时执行该语句块 --]
end
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312121805257.png" style="zoom:70%;" />







### 3、实现多级缓存

#### 3.1 OpenResty

OpenResty® 是一个基于 Nginx的高性能 Web 平台，用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。具备下列特点：

- 具备Nginx的完整功能
- 基于Lua语言进行扩展，集成了大量精良的 Lua 库、第三方模块
- 允许使用Lua**自定义业务逻辑**、**自定义库**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141516665.png" style="zoom:60%;" />

- windows上的nginx用来做反向代理服务，将前端的查询商品的ajax请求代理到OpenResty集群

- OpenResty集群用来编写多级缓存业务

- 请求到达OpenResty后，OpenResty先查询Nginx本地缓存，如果查询不到，去Redis查，Redis查询不到，再去Tomcat查，Tomcat有进程缓存，会先查进程缓存，查不到才去查数据库，从数据库查到的数据先存到Tomcat的进程缓存，然后返回给Nginx，保存到Nginx的本地缓存，最后返回给客户端结果
- Tomcat也需要做负载均衡，但如果是轮询的方式，第一次查数据去第一台Tomcat，然后数据保存到进程缓存，第二次去第二个Tomcat查，进程缓存还是空的，所以又去数据库查。因此我们希望对于同一类型同一id的数据，我们让他去同一台Tomcat上查询，这样进程缓存就会生效了，因此可以使用hash的轮询方案，他会对请求路径进行hash计算，然后hash值对Tomcat服务器的数量取模，这样就能定位到某一个Tomcat上，进行缓存就会生效







### 4、缓存同步

**缓存同步策略**

- 设置有效期：给缓存设置有效期，到期后自动删除。再次查询时更新

  - 优势：简单、方便

  - 缺点：时效性差，缓存过期之前可能不一致

  - 场景：更新频率较低，时效性要求低的业务


- 同步双写：在修改数据库的同时，直接修改缓存

  - 优势：时效性强，缓存与数据库强一致

  - 缺点：有代码侵入，耦合度高；

  - 场景：对一致性、时效性要求较高的缓存数据


- 异步通知：修改数据库时发送事件通知，相关服务监听到通知后修改缓存数据
  - 优势：低耦合，可以同时通知多个缓存服务
  - 缺点：时效性一般，可能存在中间不一致状态
  - 场景：时效性要求一般，有多个服务需要同步





异步通知可以基于MQ实现，也可以基于Canal实现

1、基于MQ的异步通知

- 商品服务完成对数据的修改后，只需要发送一条消息到MQ中。
- 缓存服务监听MQ消息，然后完成对缓存的更新
- 依然有少量的代码侵入

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141628738.png" style="zoom:50%;" />

2、基于Canal的通知

- 商品服务完成商品修改后，业务直接结束，没有任何代码侵入
- Canal监听MySQL变化，当发现变化后，立即通知缓存服务
- 缓存服务接收到Canal通知，更新缓存
- 代码零侵入

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141629558.png" style="zoom:50%;" />





#### 4.1 Canal

> Canal是基于mysql的主从同步来实现的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141631732.png)

- MySQL master 将数据变更写入二进制日志( binary log），其中记录的数据叫做binary log events
- MySQL slave 将 master 的 binary log events拷贝到它的中继日志(relay log)
- MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据



Canal就是把自己伪装成MySQL的一个slave节点，从而监听master的binary log变化。再把得到的变化信息通知给Canal的客户端，进而完成对其它数据库的同步。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141632324.png" style="zoom:80%;" />



#### 4.2 安装Canal

**1、开启MySql主从**

- 开启binlog
- 添加一个用于数据同步的用户



**2、创建Canal容器**

```sh
docker run -p 11111:11111 --name canal \
-e canal.destinations=heima \
-e canal.instance.master.address=mysql:3306  \
-e canal.instance.dbUsername=canal  \
-e canal.instance.dbPassword=canal  \
-e canal.instance.connectionCharset=UTF-8 \
-e canal.instance.tsdb.enable=true \
-e canal.instance.gtidon=false  \
-e canal.instance.filter.regex=heima\\..* \
--network heima \
-d canal/canal-server:v1.1.5
```

- `-p 11111:11111`：这是canal的默认监听端口
- `-e canal.instance.master.address=mysql:3306`：数据库地址和端口，如果不知道mysql容器地址，可以通过`docker inspect 容器id`来查看
- `-e canal.instance.dbUsername=canal`：数据库用户名
- `-e canal.instance.dbPassword=canal` ：数据库密码
- `-e canal.instance.filter.regex=`：要监听的表名称





#### 4.3 监听Canal

当Canal监听到binlog变化时，会通知Canal的客户端。利用Canal提供的Java客户端，监听Canal通知消息。当收到变化的消息时，完成对缓存的更新。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141636836.png" style="zoom:50%;" />



**1、引入依赖**

```xml
<dependency>
    <groupId>top.javatool</groupId>
    <artifactId>canal-spring-boot-starter</artifactId>
    <version>1.2.1-RELEASE</version>
</dependency>
```



**2、编写配置**

```yml
canal:
  destination: heima # canal的集群名字，要与安装canal时设置的名称一致
  server: 192.168.150.101:11111 # canal服务地址
```



**3、修改Item实体类**

通过`@Id`、`@Column`、`@Transient`等注解完成Item与数据库表字段的映射

- `@Id`标识主键id
- `@Column`数据库字段名和实体类名做映射
- `@Transient`数据库不存在的字段

```java
@Data
@TableName("tb_item")
public class Item {
    @TableId(type = IdType.AUTO)
    @Id
    private Long id;//商品id
    @Column(name = "name")
    private String name;//商品名称
    private String title;//商品标题
    private Long price;//价格（分）
    private String image;//商品图片
    private String category;//分类名称
    private String brand;//品牌名称
    private String spec;//规格
    private Integer status;//商品状态 1-正常，2-下架
    private Date createTime;//创建时间
    private Date updateTime;//更新时间
    @TableField(exist = false)
    @Transient
    private Integer stock;
    @TableField(exist = false)
    @Transient
    private Integer sold;
}
```



**4、编写监听器**

通过实现`EntryHandler<T>`接口编写监听器，监听Canal消息

- 实现类通过`@CanalTable("tb_item")`指定监听的表信息
- `EntryHandler`的泛型是与表对应的实体类，当Canal监听到数据库数据变化时，会将变化的数据传给这个类中方法的参数位置

```java
@CanalTable("tb_item")
@Component
public class ItemHandler implements EntryHandler<Item> {

    @Autowired
    private RedisHandler redisHandler;
    @Autowired
    private Cache<Long, Item> itemCache;

    @Override
    public void insert(Item item) {
        // 写数据到JVM进程缓存
        itemCache.put(item.getId(), item);
        // 写数据到redis
        redisHandler.saveItem(item);
    }

    @Override
    public void update(Item before, Item after) {
        // 写数据到JVM进程缓存
        itemCache.put(after.getId(), after);
        // 写数据到redis
        redisHandler.saveItem(after);
    }

    @Override
    public void delete(Item item) {
        // 删除数据到JVM进程缓存
        itemCache.invalidate(item.getId());
        // 删除数据到redis
        redisHandler.deleteItemById(item.getId());
    }
}
```









## 九、Redis高级

### 1、Redis键值设计

#### 1.1 优雅的key结构

Redis的Key可以自定义，但最好遵循下面的几个最佳实践约定：

- 遵循基本格式：[业务名称]:[数据名]:[id]
- 长度不超过44字节
- 不包含特殊字符

例如：登录业务，保存用户信息，其key可以设计成如下格式：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201429635.png)

这样设计的好处：

- 可读性强

- 避免key冲突

- 方便管理

- 更节省内存： key是string类型，底层编码包含int、embstr和raw三种。

  embstr在小于等于44字节使用，采用连续内存空间，内存占用更小。当字节数大于44字节时，会转为raw模式存储，在raw模式下，内存空间不是连续的，而是采用一个指针指向了另外一段内存空间，在这段空间里存储SDS内容，这样空间不连续，访问的时候性能也就会受到影响，还有可能产生内存碎片

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201440164.png)

通过`object encoding key`可以查看这个key对应value的编码类型，可以看到当name为44个a时，编码类型为embstr，当name为45个a时，编码类型为raw。这里只是用value做演示，因为key和value一样都是基于string存储的







#### 1.2 BigKey

BigKey是指一个Key对应的value占用内存比较大，或者key中成员的数量过多

- Key本身的数据量过大：一个String类型的Key，它的值为5 MB
- Key中的成员数过多：一个ZSET类型的Key，它的成员数量为10,000个
- Key中成员的数据量过大：一个Hash类型的Key，它的成员数量虽然只有1,000个但这些成员的Value（值）总大小为100 MB

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201445081.png)



在Redis中可以使用`memory usage key`命令查看指定key及其value占用大小，不过该指令对cpu使用率较高，一般不使用

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201450084.png)





**BigKey的危害**

- 网络阻塞：对BigKey执行读请求时，少量的QPS就可能导致带宽使用率被占满，导致Redis实例，乃至所在物理机变慢
- 数据倾斜：BigKey所在的Redis实例内存使用率远超其他实例，无法使数据分片的内存资源达到均衡
- Redis阻塞：对元素较多的hash、list、zset等做运算会耗时较久，使主线程被阻塞
- CPU压力：对BigKey的数据序列化和反序列化会导致CPU的使用率飙升，影响Redis实例和本机其它应用





**发现BigKey**

1、`redis-cli --bigkeys`

利用redis-cli提供的`--bigkeys`参数，可以遍历分析所有key，并返回Key的整体统计信息与**每个数据的Top1的big key**

> 这个命令会扫描Redis 中的所有 key ，会对 Redis 的性能有一点影响。并且，这种方式只能找出每种数据结构 top 1 bigkey（占用内存最大的 String 数据类型，包含元素最多的复合数据类型）。然而，一个 key 的元素多并不代表占用内存也多，需要我们根据具体的业务情况来进一步判断。



2、`scan`扫描

`SCAN` 命令可以按照一定的模式和数量返回匹配的 key。获取了 key 之后，可以利用 `STRLEN`、`HLEN`、`LLEN`等命令返回其长度或成员数量。

```sh
scan cursor [match pattern] [COUNT count] [TYPE type]
```

- cursor：游标参数，初始为0
- match pattern：指定要匹配的键的模式
- count：每次扫描的数量，默认10
- type：返回的键的类型

| 数据结构   | 命令   | 复杂度 | 结果（对应 key）   |
| ---------- | ------ | ------ | ------------------ |
| String     | STRLEN | O(1)   | 字符串值的长度     |
| Hash       | HLEN   | O(1)   | 哈希表中字段的数量 |
| List       | LLEN   | O(1)   | 列表元素数量       |
| Set        | SCARD  | O(1)   | 集合元素数量       |
| Sorted Set | ZCARD  | O(1)   | 有序集合的元素数量 |

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201506127.png)

scan 命令调用完后每次会返回2个元素，第一个是下一次迭代的光标，第一次光标会设置为0，当最后一次scan 返回的光标等于0时，表示整个scan遍历结束了，第二个返回的是List，一个匹配的key的数组。我们可以通过Java代码循环的扫描key，然后统计key的长度或成员数量

```java
public class JedisTest {
    private Jedis jedis;

    @BeforeEach
    void setUp() {
        // 1.建立连接
        // jedis = new Jedis("192.168.198.130", 6379);
        jedis = JedisConnectionFactory.getJedis();
        // 2.设置密码
        jedis.auth("123456");
        // 3.选择库
        jedis.select(0);
    }

    final static int STR_MAX_LEN = 10 * 1024;
    final static int HASH_MAX_LEN = 500;

    @Test
    void testScan() {
        int maxLen = 0;
        long len = 0;

        String cursor = "0";
        do {
            // 扫描并获取一部分key
            ScanResult<String> result = jedis.scan(cursor);
            // 记录cursor
            cursor = result.getCursor();
            List<String> list = result.getResult();
            if (list == null || list.isEmpty()) {
                break;
            }
            // 遍历
            for (String key : list) {
                // 判断key的类型
                String type = jedis.type(key);
                switch (type) {
                    case "string":
                        len = jedis.strlen(key);
                        maxLen = STR_MAX_LEN;
                        break;
                    case "hash":
                        len = jedis.hlen(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "list":
                        len = jedis.llen(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "set":
                        len = jedis.scard(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "zset":
                        len = jedis.zcard(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    default:
                        break;
                }
                if (len >= maxLen) {
                    System.out.printf("Found big key : %s, type: %s, length or size: %d %n", key, type, len);
                }
            }
        } while (!cursor.equals("0"));
    }
    
    @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }

}
```



3、使用开源工具

利用第三方工具，如 Redis-Rdb-Tools 分析RDB快照文件，全面分析内存使用情况，https://github.com/sripathikrishnan/redis-rdb-tools



4、借助公有云的 Redis 分析服务

例如阿里云搭建的云服务器就有相关监控页面

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201512606.png" style="zoom:80%;" />







#### 1.3 删除BigKey

BigKey内存占用较多，即便是删除这样的key也需要耗费很长时间，导致Redis主线程阻塞，引发一系列问题。

- Redis 3.0 及以下版本，使用`scan`和`del`命令结合来分批次删除

  - 如果是集合类型，则遍历BigKey的元素，先逐个删除子元素，最后删除BigKey

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201516834.png" style="zoom:75%;" />

- Redis 4.0以后
  - Redis在4.0后提供了异步删除的命令：unlink，可以删除一个或多个key







#### 1.4 恰当的数据结构

##### **需求一**

存储一个User对象，我们有三种存储方式

1、json字符串

| user:1 | {"name": "Jack", "age": 21} |
| :----: | :-------------------------: |

优点：实现简单

缺点：数据耦合，不够灵活



2、字段打散

| user:1:name | Jack |
| :---------: | :--: |
| user:1:age  |  21  |

优点：可以灵活访问对象任意字段

缺点：占用空间大、没办法做统一控制



3、hash**（推荐）**

<table>
	<tr>
		<td rowspan="2">user:1</td>
        <td>name</td>
        <td>jack</td>
	</tr>
	<tr>
		<td>age</td>
		<td>21</td>
	</tr>
</table>


优点：底层使用ziplist，空间占用小，可以灵活访问对象的任意字段

缺点：代码相对复杂





##### 需求二

假如有hash类型的key，其中有100万对field和value，field是自增id，这个key存在什么问题？如何优化？

<table>
	<tr style="color:red">
		<td>key</td>
        <td>field</td>
        <td>value</td>
	</tr>
	<tr>
		<td rowspan="3">someKey</td>
		<td>id:0</td>
        <td>value0</td>
	</tr>
    <tr>
		<td>.....</td>
        <td>.....</td>
	</tr>
    <tr>
        <td>id:999999</td>
        <td>value999999</td>
    </tr>
</table>

存在的问题：

- hash的entry数量超过512时【默认】，会使用哈希表而不是ZipList，内存占用较多

- 可以通过`hash-max-ziplist-entries`配置entry上限。但是如果entry过多就会导致BigKey问题



1、拆分为string类型

<table>
	<tr style="color:red">
		<td>key</td>
        <td>value</td>
	</tr>
	<tr>
		<td>id:0</td>
        <td>value0</td>
	</tr>
    <tr>
		<td>.....</td>
        <td>.....</td>
	</tr>
    <tr>
        <td>id:999999</td>
        <td>value999999</td>
    </tr>
</table>


存在的问题：

- string结构底层没有太多内存优化，内存占用较多，key越多，元信息越多 
- 想要批量获取这些数据比较麻烦



2、拆分为小的hash，将 id / 100 作为key， 将id % 100 作为field，这样每100个元素为一个Hash，一共有10000个hash

<table>
	<tr style="color:red">
		<td>key</td>
        <td>field</td>
        <td>value</td>
	</tr>
	<tr>
        <td rowspan="3">key:0</td>
		<td>id:00</td>
        <td>value0</td>
	</tr>
    <tr>
		<td>.....</td>
        <td>.....</td>
	</tr>
    <tr>
        <td>id:99</td>
        <td>value99</td>
    </tr>
    <tr>
        <td rowspan="3">key:1</td>
		<td>id:00</td>
        <td>value100</td>
	</tr>
    <tr>
		<td>.....</td>
        <td>.....</td>
	</tr>
    <tr>
        <td>id:99</td>
        <td>value199</td>
    </tr>
    <tr>
    	<td colspan="3">....</td>
    </tr>
    <tr>
        <td rowspan="3">key:9999</td>
		<td>id:00</td>
        <td>value999900</td>
	</tr>
    <tr>
		<td>.....</td>
        <td>.....</td>
	</tr>
    <tr>
        <td>id:99</td>
        <td>value999999</td>
    </tr>
</table>








### 2、批处理优化

客户端向Redis服务端发送命令，需要通过网络将命令发送过去，然后Redis服务端执行命令，再将结果返回，Redis执行命令的速度是很快的，而发送命令是比较耗时的，如果每次发送一条命令，发N条，就需要N次网络往返传输，非常耗时

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201621756.png" style="zoom:80%;" />

如果使用批处理的方式，一次发送N条命令，那么N条命令只需要一次往返的网络传输，大大节约了时间，需要注意的是，不要在一次批处理中传输太多命令，否则单次命令占用带宽过多，导致网络阻塞

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201626547.png" style="zoom:80%;" />





#### 2.1 MSET

Redis提供了很多Mxxx这样的命令，可以实现批量插入数据

- mset：只能操作string
- hmset：只能针对一个key，插入filed和value

利用mset批量插入10万条数据

```java
@Test
void testMxx() {
    String[] arr = new String[2000];
    int j;
    long b = System.currentTimeMillis();
    for (int i = 1; i <= 100000; i++) {
        j = (i % 1000) << 1;
        arr[j] = "test:key_" + i;
        arr[j + 1] = "value_" + i;
        if (j == 0) {
            jedis.mset(arr);
        }
    }
    long e = System.currentTimeMillis();
    System.out.println("time: " + (e - b));
}
```





#### 2.2 Pipeline

MSET批处理只能操作部分数据类型，因此如果有对复杂数据类型的批处理需要，需要使用Pipeline

```java
@Test
void testPipeline() {
    // 创建管道
    Pipeline pipeline = jedis.pipelined();
    long b = System.currentTimeMillis();
    for (int i = 1; i <= 100000; i++) {
        // 放入命令到管道
        pipeline.set("test:key_" + i, "value_" + i);
        if (i % 1000 == 0) {
            // 每放入1000条命令，批量执行
            pipeline.sync();
        }
    }
    long e = System.currentTimeMillis();
    System.out.println("time: " + (e - b));
}
```



- `Mxxx`操作是redis内置命令，`Mxxx`操作中的多组key和value会将其作为一个原子操作，一次性都执行完

- pipeline执行时，这一组命令是一起发送到Redis，但不一定是一起执行的，因为pinpLine中命令传输是有先后的，而且传输时，其他客户端也可以向Redis发送命令，这些命令到达Redis是有先后顺序的，Redis线程依次执行这些命令





#### 2.3 集群下的批处理

`MSET`或`Pipeline`这样的批处理需要在一次请求中携带多条命令，而此时如果Redis是一个集群，那批处理命令的多个key必须落在同一个节点的插槽中，否则就会导致执行失败。因为在不同节点的插槽上执行命令，同样需要多次网络传输给不同的Redis节点。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211604809.png" style="zoom:67%;" />

- 串行执行，执行起来简单，缺点就是耗时过久。

- 串行slot，执行前，客户端先计算一下对应的key的slot，一样slot的key就放到一个组里边，不同的，就放到不同的组里边，然后对每个组执行pipeline的批处理，他就能串行执行各个组的命令，这种做法比第一种方法耗时要少，但是比较复杂

- **并行slot**，第二种方案，在分组完成后串行执行，这种方案变成了并行执行各个命令，所以他的耗时非常短，实现也更加复杂。

- hash_tag，redis计算key的slot的时候，其实是根据key的有效部分来计算的，通过这种方式就能一次处理所有的key，这种方式耗时最短，实现也简单，但是如果通过操作key的有效部分，那么就会导致所有的key都落在一个节点上，产生数据倾斜的问题，所以推荐使用第三种方式。



**Spring在集群环境下向Redis插入数据**

使用Spring整合的`RedisTemplate`批量插入数据时，我们不需要额外操作，Spring帮我们实现了并行的slot

```java
@Test
void testMSetInCluster() {
    Map<String, String> map = new HashMap<>(3);
    map.put("name", "Rose");
    map.put("age", "21");
    map.put("sex", "Female");
    stringRedisTemplate.opsForValue().multiSet(map);
}
```



**源码分析**

在`RedisAdvancedClusterAsyncCommandsImpl `类的`mset`方法中，首先根据`slotHash`算出来一个`partitioned`的map，map中的key就是slot，而他的value就是相同slot的key对应的数据，通过 `RedisFuture<String> mset = super.mset(op);`进行异步的消息发送

```java
public RedisFuture<String> mset(Map<K, V> map) {
    // partitioned的key是对key做hash计算出来的slot，value就是这个节点上的key的集合
    Map<Integer, List<K>> partitioned = SlotHash.partition(this.codec, map.keySet());
    if (partitioned.size() < 2) {
        return super.mset(map);
    } else {
        Map<Integer, RedisFuture<String>> executions = new HashMap();
        Iterator var4 = partitioned.entrySet().iterator();
		
        // 遍历partitioned
        while(var4.hasNext()) {
            Entry<Integer, List<K>> entry = (Entry)var4.next();
            // 将同一个slot上插入的数据收集起来
            Map<K, V> op = new HashMap();
            ((List)entry.getValue()).forEach((k) -> {
                op.put(k, map.get(k));
            });
            // 异步的批量插入
            RedisFuture<String> mset = super.mset(op);
            executions.put(entry.getKey(), mset);
        }

        return MultiNodeExecution.firstOfAsync(executions);
    }
}
```







### 3、慢查询优化

在Redis执行时耗时超过某个阈值的命令，称为慢查询。

慢查询的危害：由于Redis是单线程的，所以当客户端发出指令后，他们都会进入到redis底层的queue来执行，如果此时有一些慢查询的数据，就会导致大量请求阻塞，从而引起超时报错，所以需要解决慢查询问题。



- 慢查询的阈值可以通过配置指定：

​		`slowlog-log-slower-than`：慢查询阈值，单位是微秒。默认是10000，建议1000

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201732990.png)

- 慢查询会被放入慢查询日志中，日志的长度有上限，可以通过配置指定：

​		`slowlog-max-len`：慢查询日志（本质是一个队列）的长度。默认是128，建议1000

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312201732490.png)

通过`config get xxx`可以拿到这些配置，通过`config set xxx`可以设置配置，但是Redis重启就会失效，如果需要永久生效，需要修改redis.conf文件





**查看慢查询**

* `slowlog len`：查询慢查询日志长度
* `slowlog get [n]`：读取n条慢查询日志 
* `slowlog reset`：清空慢查询列表

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211604946.png" style="zoom:67%;" />





### 4、Redis安全配置

> 一般情况下Redis会绑定在0.0.0.0:6379，这样将会将Redis服务暴露到公网上，而Redis如果没有做身份认证，会出现严重的安全漏洞.
> 漏洞重现方式：https://cloud.tencent.com/developer/article/1039000



这里出现了不需要密码也能够登录linux服务器，主要是linux有ssh免秘钥登录的方式，生成一对公钥和私钥，私钥放在本地，公钥放在linux服务器，当我们登录服务器时，他会去解析公钥和私钥，实现免密登录。但是Redis的漏洞在于在不登录的情况下，也能把秘钥送到Linux服务器，从而产生漏洞

漏洞出现的核心的原因有以下几点：

* Redis未设置密码
* 利用了Redis的config set命令动态修改Redis配置
* 使用了Root账号权限启动Redis



解决方案

* Redis一定要设置密码
* 禁止线上使用下面命令：keys、flushall、flushdb、config set等命令。可以利用rename-command禁用。
* bind：限制网卡，禁止外网网卡访问
* 开启防火墙
* 不要使用Root账户启动Redis
* 尽量不是有默认的端口







### 5、Redis内存划分

当Redis内存不足时，可能导致Key频繁被删除、响应时间变长、QPS不稳定等问题。当内存使用率达到90%以上时就需要我们警惕，并快速定位到内存占用的原因。可以通过`info memory`或者`memory stats`命令查看Redis内存分配情况



**碎片问题**

Redis底层分配并不是这个key有多大，他就会分配多大，而是有他自己的分配策略，比如8,16,20等等，假定当前key只需要10个字节，此时分配8肯定不够，那么他就会分配16个字节，多出来的6个字节就不能被使用，这就是我们常说的 碎片问题



**缓冲区内存问题分析：**

一般包括客户端缓冲区、AOF缓冲区、复制缓冲区等。客户端缓冲区又包括输入缓冲区和输出缓冲区两种。这部分内存占用波动较大，所以这片内存也是需要重点分析的内存问题。



| **内存占用** | **说明**                                                     |
| ------------ | ------------------------------------------------------------ |
| 数据内存     | 是Redis最主要的部分，存储Redis的键值信息。主要问题是BigKey问题、内存碎片问题 |
| 进程内存     | Redis主进程本身运⾏肯定需要占⽤内存，如代码、常量池等等；这部分内存⼤约⼏兆，在⼤多数⽣产环境中与Redis数据占⽤的内存相⽐可以忽略。 |
| 缓冲区内存   | 一般包括客户端缓冲区、AOF缓冲区、复制缓冲区等。客户端缓冲区又包括输入缓冲区和输出缓冲区两种。这部分内存占用波动较大，不当使用BigKey，可能导致内存溢出。 |



内存缓冲区常见的有三种：

* 复制缓冲区：主从复制的repl_backlog_buf，如果太小可能导致频繁的全量复制，影响性能。通过replbacklog-size来设置，默认1mb
* AOF缓冲区：AOF刷盘之前的缓存区域，AOF执行rewrite的缓冲区。无法设置容量上限
* 客户端缓冲区：分为输入缓冲区和输出缓冲区，输入缓冲区最大1G且不能设置【客户端发送的命令放入输入缓冲区】。输出缓冲区可以设置【Redis服务端执行完命令返回的结果放入输出缓冲区】







### 6、集群优化

**1、在Redis的默认配置中，如果发现任意一个插槽不可用，则整个集群都会停止对外服务。**

假如Redis集群中有一个节点不可用，那么这个节点上的插槽也不可用。会导致整个集群都不可用。因为Redis中部分插槽不可用后，当有key操作这个插槽时就会报错

在redis.conf文件中，通过`cluster-require-full-coverage yes`设置，将该配置改为no，即使有插槽不可用，redis集群中其他插槽仍然是可用的



**2、集群带宽问题**

集群节点之间会不断的互相Ping来确定集群中其它节点的状态。每次Ping携带的信息至少包括：

* 插槽信息
* 集群状态信息

集群中节点越多，集群状态信息数据量也越大，10个节点的相关信息可能达到1kb，此时每次集群互通需要的带宽会非常高，这样会导致集群中大量的带宽都会被ping信息所占用，所以需要去解决这样的问题

**解决途径：**

* 避免大集群，集群节点数不要太多，最好少于1000，如果业务庞大，则建立多个集群。
* 避免在单个物理机中运行太多Redis实例
* 配置合适的`cluster-node-timeout`值，每个节点会在`cluster-node-timeout / 2`的时间间隔向其他节点ping



**3、lua和事务的问题**

lua和事务都是要保证原子性问题，如果你的key不在一个节点，那么是无法保证lua的执行和事务的特性的，所以在集群模式是没有办法执行lua和事务的



**总结**

单体Redis（主从Redis）已经能达到万级别的QPS，并且也具备很强的高可用特性。如果主从能满足业务需求的情况下，不是在万不得已的情况下，尽量不搭建Redis集群









## 十、Redis原理

### 1、Redis数据结构

#### 1.1 动态字符串

> Redis中保存的Key是字符串，value往往是字符串或者字符串的集合。可见字符串是Redis中最常用的一种数据结构。
>
> 不过Redis没有直接使用C语言中的字符串，因为C语言字符串存在很多问题：
>
> - 获取字符串长度的需要通过运算：字符串都以`\0`结尾，因此计算长度时，需要遍历一遍，直到读到`\0`
> - 非二进制安全：C语言不允许字符串中字符有`\0`，因为有的话会被当做字符串结束标识
> - 不可修改：字符串在内存常量池，不能修改



Redis构建了一种新的字符串结构，称为简单动态字符串（Simple Dynamic String），简称**SDS**

例如：我们执行`set name xrj`这条命令，Redis将在底层创建两个SDS，其中一个是包含`name`的SDS，另一个是包含`xrj`的SDS。



**Redis中SDS源码**

Redis中的SDS有5种类型，根据占用的空间，使用不同的类型，例如`sdshdr8`中`len`和`alloc`都是`uint8_t`类型的，表示最多255个字节，其中有一个字节是`\0`，因此最多存储254个字节数据，`flags`字段就表示当前SDS是那种类型的，`buf`中存储的就是真正的字符串

```C
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* buf已保存的字符串字节数，不包含结束标识 */
    uint8_t alloc; /* buf申请的总字节数 */
    unsigned char flags; /* 不同的SDS类型（0,1,2,3,4） */
    char buf[]; /* 真正存放字符串的地方 */
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};

#define SDS_TYPE_5  0
#define SDS_TYPE_8  1
#define SDS_TYPE_16 2
#define SDS_TYPE_32 3
#define SDS_TYPE_64 4
```



一个包含字符串`name`的sds结构如下：第一次申请时，`len`和`alloc`长度是一样的，他们记录的值不包括`\0`，因此实际数据占用5个字节

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211442298.png)



**SDS的扩容**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211444960.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211445306.png)

一个内容为`hi`的字符串，初始时`len`和`alloc`都为2，此时要给SDS追加一段字符串`,Amy`，这里首先会申请新内存空间：

- 如果新字符串小于1M，则新空间为扩展后字符串长度的两倍+1，这里+1其实是给`\0`使用的

- 如果新字符串大于1M，则新空间为扩展后字符串长度+1M+1。称为内存预分配，因为申请内存需要从用户态转为内核态，非常消耗性能，采用内存预分配后，下次再追加字符串，只要不超过最大长度，就不用去申请内存，提升性能

因此原字符串`hi`追加`,Amy`后长度为6个字节，小于1M，所以申请新内存空间为13字节，此时`len`为6表示字符串占用空间，`alloc`为12表示申请的空间，不包括`\0`的这一个字节





**SDS优点**

- 获取字符串长度的时间复杂度为`O(1)`，直接读取`len`字段即可
- 支持动态扩容
- 减少内存分配次数
- 二进制安全，读取字符串时，按照`len`指定长度读取，即使读取到`\0`也不会有影响





#### 1.2 intset

> IntSet是Redis中set集合的一种实现方式，基于整数数组来实现，并且具备长度可变、有序等特征。

```c
typedef struct intset {
    uint32_t encoding;  // 编码方式，支持存放16位，32位，64位整数，下边有定义
    uint32_t length;	// 元素个数
    int8_t contents[];	// 整数数组，contents类型为int8_t原因是它是一个指针，指向的是数组首地址，数组中元素的数据类型是通过encoding指定的
} intset;
```

```c
#define INTSET_ENC_INT16 (sizeof(int16_t))
#define INTSET_ENC_INT32 (sizeof(int32_t))
#define INTSET_ENC_INT64 (sizeof(int64_t))
```



为了方便查找，Redis会将intset中所有的整数按照升序依次保存在contents数组中

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211549891.png" style="zoom:67%;" />

现在，数组中每个数字都在`int16_t`的范围内，因此采用的编码方式是`INTSET_ENC_INT16`

- `encoding`：4字节
- `length`：4字节
- `contents`：2字节 * 3  = 6字节



此时，我们向其中添加一个数字：50000，这个数字超出了`int16_t`的范围，intset会自动升级编码方式到合适的大小。

* 升级编码为`INTSET_ENC_INT32`, 每个整数占4字节，并按照新的编码方式及元素个数扩容数组
* 倒序依次将数组中的元素拷贝到扩容后的正确位置
* 将待添加的元素放入数组末尾
* 最后，将inset的`encoding`属性改为`INTSET_ENC_INT32`，将`length`属性改为4

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211549739.png" style="zoom:80%;" />





**源码分析**

inset添加元素

```c
// is是当前inset集合，value是添加的元素
intset *intsetAdd(intset *is, int64_t value, uint8_t *success) {
    // 获取当前元素编码
    uint8_t valenc = _intsetValueEncoding(value);
    // 记录添加的位置
    uint32_t pos;
    if (success) *success = 1;

    // 判断编码是不是超过了当前inset的编码
    if (valenc > intrev32ifbe(is->encoding)) {
        // 超出编码，需要升级
        return intsetUpgradeAndAdd(is,value);
    } else {
        // 通过二分在inset中查找当前元素，如果当前元素存在，就直接退出，保证元素唯一，不存在，pos记录大于当前元素的第一个值
        if (intsetSearch(is,value,&pos)) {
            if (success) *success = 0;
            return is;
        }
		
        // 数组扩容，扩容为length+1
        is = intsetResize(is,intrev32ifbe(is->length)+1);
        // 移动数组，将pos之后的元素往后移动一个位置
        if (pos < intrev32ifbe(is->length)) intsetMoveTail(is,pos,pos+1);
    }
	// 插入新元素
    _intsetSet(is,pos,value);
    // 重置元素长度
    is->length = intrev32ifbe(intrev32ifbe(is->length)+1);
    return is;
}
```

升级编码

```c
static intset *intsetUpgradeAndAdd(intset *is, int64_t value) {
    // 拿到当前inset集合的编码
    uint8_t curenc = intrev32ifbe(is->encoding);
    // 插入元素的编码
    uint8_t newenc = _intsetValueEncoding(value);
    // inset集合的长度
    int length = intrev32ifbe(is->length);
    // 如果value小于0，那么应该插入到所有元素之前，大于0插入所有元素之后
    int prepend = value < 0 ? 1 : 0;

    // 修改inset集合的编码
    is->encoding = intrev32ifbe(newenc);
    // 数组扩容为length+1
    is = intsetResize(is,intrev32ifbe(is->length)+1);

    // 倒序拷贝元素，length+prepend就是要拷贝的位置，如果prepend为0表示新元素插入数组最后，为1表示插入数组第一个位置
    while(length--)
        _intsetSet(is,length+prepend,_intsetGetEncoded(is,length,curenc));

    // 插入新元素
    if (prepend)
        _intsetSet(is,0,value);
    else
        _intsetSet(is,intrev32ifbe(is->length),value);
    // 设置长度
    is->length = intrev32ifbe(intrev32ifbe(is->length)+1);
    return is;
}
```







#### 1.3 Dict

Redis是一个键值型的数据库，我们可以根据键实现快速的增删改查。而键与值的映射关系是通过Dict来实现的。但是他内存不连续，一个指针8字节，浪费内存

Dict由三部分组成：

- 哈希表（DictHashTable）

  ```c
  typedef struct dictht {
      // entry数组，table是指向DictEntry数组的指针
      dictEntry **table;
      // 哈希表大小
      unsigned long size;
      // 哈希表大小的掩码，size-1，与key的hash值做与运算找到key在哈希表中的位置
      unsigned long sizemask;
      // entry个数，可能大于size，因为会出现hash冲突
      unsigned long used;
  } dictht;
  ```

- 哈希节点（DictEntry）

  ```c
  typedef struct dictEntry {
      void *key; // 键
      union {
          void *val;
          uint64_t u64;
          int64_t s64;
          double d;
      } v; // 值
      struct dictEntry *next; // 下一个Entry的指针
  } dictEntry;
  ```

- 字典（Dict）

  ```c
  typedef struct dict {
      dictType *type; // dict类型，内置不同的hash函数
      void *privdata; // 私有数据，做特殊hash运算时使用
      dictht ht[2];   // 一个Dict包含两个hash表，其中一个存储当前数据，另一个一般为空，rehash时使用
      long rehashidx; // rehash进度，-1表示未进行
      int16_t pauserehash; // rehash是否暂停，1暂停，0继续
  } dict;
  ```



当我们向Dict添加键值对时，Redis首先根据key计算出hash值（h），然后利用` h & sizemask`来计算元素应该存储到数组中的哪个索引位置。

> 我们存储k1=v1，假设k1的哈希值h =1，则1&3 =1，因此k1=v1要存储到数组角标1位置。
>
> 存储k2=v2，假设k2的哈希值h =1，则1&3 =1，因此k2=v2要存储到数组角标1位置，此时发生hash冲突，采用头插法将k2=v2这个entry插入链表头部

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211630698.png" style="zoom:80%;" />







**Dict结构**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211632792.png" style="zoom:80%;" />



 



**Dict的扩容**

Dict中的HashTable就是数组结合单向链表的实现，当集合中元素较多时，必然导致哈希冲突增多，链表过长，则查询效率会大大降低。

Dict在每次新增键值对时都会检查负载因子（LoadFactor = used/size） ，满足以下两种情况时会触发哈希表扩容：

- 哈希表的 LoadFactor >= 1，并且服务器没有执行 `BGSAVE` 或者 `BGREWRITEAOF `等后台进程，因为这些进程需要大量的IO，可能导致进程阻塞
- 哈希表的 LoadFactor > 5 ；

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312211736317.png" style="zoom:67%;" />

```c
int _dictExpand(dict *d, unsigned long size, int* malloc_failed)
{
    if (malloc_failed) *malloc_failed = 0;

    /* 如果正在做rehash或者当前hash表中entry数量大于size，报错 */
    if (dictIsRehashing(d) || d->ht[0].used > size)
        return DICT_ERR;

    // 新的hash表
    dictht n; /* the new hash table */
    // 找到大于等于size的第一个2^n
    unsigned long realsize = _dictNextPower(size);

    /* Detect overflows */
    if (realsize < size || realsize * sizeof(dictEntry*) < realsize)
        return DICT_ERR;

    /* 扩容大小和原大小一样，报错 */
    if (realsize == d->ht[0].size) return DICT_ERR;

    /* 重置hash表的大小和掩码 */
    n.size = realsize;
    n.sizemask = realsize-1;
    if (malloc_failed) {
        n.table = ztrycalloc(realsize*sizeof(dictEntry*));
        *malloc_failed = n.table == NULL;
        if (*malloc_failed)
            return DICT_ERR;
    } else // 分配内存
        n.table = zcalloc(realsize*sizeof(dictEntry*));

    // 已使用初始化为0
    n.used = 0;

    /* 如果是第一次，直接把n赋值给ht[0]即可 */
    if (d->ht[0].table == NULL) {
        d->ht[0] = n;
        return DICT_OK;
    }

    /* 否则，执行rehash */
    d->ht[1] = n;
    d->rehashidx = 0; // 表示rehash进度
    return DICT_OK;
}
```



Dict在删除元素的时候，删除成功后，也需要检查是否需要重置Dict的大小，如果**size大于hash表初始大小同时负载因子小于0.1**，那么就对hash表进行收缩

```c
...
if (dictDelete((dict*)o->ptr, field) == C_OK) {
    deleted = 1;

    // 删除成功后，检查是否需要重置Dict大小，如果需要就调用dictResize方法重置
    if (htNeedsResize(o->ptr)) dictResize(o->ptr);
}
...
    
    
// htNeedsResize方法
int htNeedsResize(dict *dict) {
    long long size, used;
	
    size = dictSlots(dict); // hash表大小
    used = dictSize(dict);  // entry个数
    // size大于4并且 used*100/size < 10 就返回true
    return (size > DICT_HT_INITIAL_SIZE &&
            (used*100/size < HASHTABLE_MIN_FILL));
}


// dictResize方法
int dictResize(dict *d)
{
    unsigned long minimal;
	// 正在做bgsave或者bgrewriteof或者rehash，就返回错误
    if (!dict_can_resize || dictIsRehashing(d)) return DICT_ERR;
    // 获取entry数量
    minimal = d->ht[0].used;
    // 如果minimal小于4，则重置为4
    if (minimal < DICT_HT_INITIAL_SIZE)
        minimal = DICT_HT_INITIAL_SIZE;
    // 通过dictExpand重置hash表大小
    return dictExpand(d, minimal);
}
```





**Dict的rehash**

不管是扩容还是收缩，必定会创建新的哈希表，导致哈希表的`size`和`sizemask`变化，而key的查询与`sizemask`有关。因此必须对哈希表中的每一个key重新计算索引，插入新的哈希表，这个过程称为**rehash**

1、计算新hash表的realeSize，值取决于当前要做的是扩容还是收缩：

* 如果是扩容，则新size为第一个大于等于`dict.ht[0].used + 1`的2^n
* 如果是收缩，则新size为第一个大于等于`dict.ht[0].used`的`2^n` （不得小于4）

2、按照新的realeSize申请内存空间，创建dictht，并赋值给dict.ht[1]

3、设置dict.rehashidx = 0，标示开始rehash

4、将dict.ht[0]中的每一个dictEntry都rehash到dict.ht[1]

5、将dict.ht[1]赋值给dict.ht[0]，给dict.ht[1]初始化为空哈希表，释放原来的dict.ht[0]的内存

6、将rehashidx赋值为-1，代表rehash结束

7、在rehash过程中，新增操作，则直接写入ht[1]，查询、修改和删除则会在dict.ht[0]和dict.ht[1]依次查找并执行。这样可以确保ht[0]的数据只减不增，随着rehash最终为空





Dict的rehash不是一次性完成的，因为如果Dict中包含数百万个entry，要在一次rehash中完成，可能导致主线程阻塞。因此Dict的rehash是分多次、渐进式的完成的，称为**渐进式rehash**，在上边第4步reshah时，按如下流程：

1、每次执行增、删、改、查操作时，都检查一下`dict.rehashidx`是否大于-1，如果是就将`dict.ht[0].table[rehashidx]`的entry链表rehash到dict.ht[1]，并将rehashidx++，直到dict.ht[0]中所有数据都rehash到dict.ht[1]

2、将dict.ht[1]赋值给dict.ht[0]，给dict.ht[1]初始化为空哈希表，释放原来的dict.ht[0]的内存

3、将rehashidx赋值为-1，代表rehash结束

4、在rehash过程中，新增操作，则直接写入ht[1]，查询、修改和删除则会在dict.ht[0]和dict.ht[1]依次查找并执行。这样可以确保ht[0]的数据只减不增，随着rehash最终为空









#### 1.4 ZipList

ZipList 是一种特殊的 “双端链表” ，由一系列特殊编码的**连续内存块**组成。可以在任意一端进行压入/弹出操作, 并且该操作的时间复杂度为 O(1)。

- `zlbytes`固定4字节，记录整个压缩列表长度

- `zltail`固定4字节，记录压缩列表尾节点距离压缩列表的起始地址有多少字节
- `zllen`固定2字节，记录entry个数
- `zlend`固定1字节，内容为`0xff`

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212133564.png" style="zoom:80%;" />



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212129213.png" style="zoom:70%;" />

| **属性** | **类型 ** | **长度               ** | **用途**                                                     |
| -------- | --------- | ----------------------- | ------------------------------------------------------------ |
| zlbytes  | uint32_t  | 4 字节                  | 记录整个压缩列表占用的内存字节数                             |
| zltail   | uint32_t  | 4 字节                  | 记录压缩列表尾节点距离压缩列表的起始地址有多少字节，通过这个偏移量，可以确定表尾节点的地址。 |
| zllen    | uint16_t  | 2 字节                  | 记录了压缩列表包含的节点数量。 最大值为UINT16_MAX （65534），如果超过这个值，此处会记录为65535，但节点的真实数量需要遍历整个压缩列表才能计算得出。 |
| entry    | 列表节点  | 不定                    | 压缩列表包含的各个节点，节点的长度由节点保存的内容决定。     |
| zlend    | uint8_t   | 1 字节                  | 特殊值 0xFF （十进制 255 ），用于标记压缩列表的末端。        |





**ZipListEntry**

ZipList 中的Entry并不像普通链表那样记录前后节点的指针，因为记录两个指针要占用16个字节，浪费内存。而是采用了下面的结构：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212140081.png)

* `previous_entry_length`：前一节点的字节数，占1个或5个字节。
  * 如果前一节点的长度小于254字节，则采用1个字节来保存这个长度值
  * 如果前一节点的长度大于等于254字节，则采用5个字节来保存这个长度值，第一个字节为0xfe【254】，后四个字节才是真实长度数据
* `encoding`：编码属性，记录content的数据类型（字符串还是整数）以及长度，占用1个、2个或5个字节
* `contents`：负责保存节点的数据，可以是字符串或整数

`previous_entry_length`和`encoding`的长度可以确定，`content`长度可以根据`encoding`得出，因此这个entry的整体长度可以求出，知道当前这个entry的地址，就可以知道下一个entry的地址了，实现正序遍历。同时，知道当前这个entry的地址后，通过`previous_entry_length`知道上一个entry的长度，可以知道	上一个entry的起始地址，从而实现倒序遍历。



> ZipList中所有存储长度的数值均采用**小端字节序**，即低位字节在前，高位字节在后。
>
> 例如：数值0x1234，采用小端字节序后实际存储值为：0x3412





**Encoding编码**

ZipListEntry中的encoding编码分为字符串和整数两种：

- 字符串：如果encoding是以`00`、`01`或者`10`开头，则证明content是字符串

| **编码**                                             | **编码长度** | **字符串大小**      |
| ---------------------------------------------------- | ------------ | ------------------- |
| \|00pppppp\|                                         | 1 bytes      | <= 63 bytes         |
| \|01pppppp\|qqqqqqqq\|                               | 2 bytes      | <= 16383 bytes      |
| \|10000000\|qqqqqqqq\|rrrrrrrr\|ssssssss\|tttttttt\| | 5 bytes      | <= 4294967295 bytes |

例如，我们要保存字符串：“ab”和 “bc”

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212140917.png)



* 整数：如果encoding是以`11`开始，则证明content是整数，且encoding固定只占用1个字节。因为整数最大是long类型，8字节，所以encoding最大记录8，一字节就够用了，因为整数就这几种类型，所以可以根通过不同编码表示不同类型整数

| **编码** | **编码长度** | **整数类型**                                               |
| -------- | ------------ | ---------------------------------------------------------- |
| 11000000 | 1            | int16_t（2 bytes）                                         |
| 11010000 | 1            | int32_t（4 bytes）                                         |
| 11100000 | 1            | int64_t（8 bytes）                                         |
| 11110000 | 1            | 24位有符整数(3 bytes)                                      |
| 11111110 | 1            | 8位有符整数(1 bytes)                                       |
| 1111xxxx | 1            | 直接在xxxx位置保存数值，范围从0001~1101，减1后结果为实际值 |

> `1111xxxx`编码：当content中存储数字太小的时候，可以直接存储在encoding的后四位上，这样可以节省一个字节，0000-1111，由于0000和1110已经被使用，所以范围就是0001-1101，是1到13，因为是从0开始存，所以0001-1101实际存储的范围为0-12

例如，我们要保存整数：2和5，因为2和5都是小于12，所以直接通过`1111xxxx`的形式存储，2就存储为`11110011`，5存储为`11110110`，存储结构如下图：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212141074.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212141989.png)







#### 1.5 ZipList的连锁更新问题

> ZipList的每个Entry都通过`previous_entry_length`来记录上一个节点的大小，长度是1个或5个字节
>
> - 如果前一节点的长度小于254字节，则采用1个字节来保存这个长度值
>
> - 如果前一节点的长度大于等于254字节，则采用5个字节来保存这个长度值，第一个字节为0xfe，后四个字节才是真实长度数据



现在，假设我们有N个连续的、长度为250~253字节之间的entry，因此entry的`previous_entry_length`属性用1个字节即可表示，如图所示

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212218419.png)



现在往头部插入了一个长度为254字节的entry，所以原来第一个entry的`previous_entry_length`需要使用5个字节来存储，然后这个entry的整体大小变为254字节，从而导致他的下一个entry也需要使用4字节的`previous_entry_length`来保存，后边都是如此，发生连锁更新问题。这样会导致每个entry都需要向后移动，如果内存不够，还会频繁申请内存，用户态和内核态频繁切换，导致性能下降，但是这种情况出现的概率很低

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212218671.png)







#### 1.6 QuickList

> 1、ZipList虽然节省内存，但申请内存必须是连续空间，如果内存占用较多，申请内存效率很低。
>
> - 为了缓解这个问题，我们必须限制ZipList的长度和entry大小。
>
> 2、我们要存储大量数据，超出了ZipList最佳的上限该怎么办
>
> - 可以创建多个ZipList来分片存储数据。
>
> 3、数据拆分后比较分散，不方便管理和查找，这多个ZipList如何建立联系？
>
> - Redis在3.2版本引入了新的数据结构QuickList，它是一个双端链表，只不过链表中的每个节点都是一个ZipList。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212256543.png)

这样每个节点的ziplist内存连续，不同节点的内存是非连续的

为了避免QuickList中的每个ZipList中entry过多，Redis提供了一个配置项：`list-max-ziplist-size`来限制。

- 如果值为正，则代表ZipList的允许的entry个数的最大值

- 如果值为负，则代表ZipList的最大内存大小，默认值为-2

  * -1：每个ZipList的内存占用不能超过4kb

  * -2：每个ZipList的内存占用不能超过8kb

  * -3：每个ZipList的内存占用不能超过16kb

  * -4：每个ZipList的内存占用不能超过32kb

  * -5：每个ZipList的内存占用不能超过64kb




**quicklist源码**

```c
typedef struct quicklist {
    // 头结点指针
    quicklistNode *head;
    // 尾结点指针
    quicklistNode *tail;
    // 所有zipList的entry数量
    unsigned long count;        /* total count of all entries in all ziplists */
    // ziplist总数量
    unsigned long len;          /* number of quicklistNodes */
    // ziplist的entry上限，默认为-2
    int fill : QL_FILL_BITS;              /* fill factor for individual nodes */
    // 首位不压缩的节点个数，默认为0，全都不压缩，大于0，就表示前后有几个节点不压缩，中间的压缩
    unsigned int compress : QL_COMP_BITS; /* depth of end nodes not to compress;0=off */
    unsigned int bookmark_count: QL_BM_BITS;
    quicklistBookmark bookmarks[];
} quicklist;
```

**quicklistNode源码**

```c
typedef struct quicklistNode {
    // 前一个节点指针
    struct quicklistNode *prev;
    // 后一个节点指针
    struct quicklistNode *next;
    // 当前节点ziplist的指针
    unsigned char *zl;
    // 当前节点ziplist的大小
    unsigned int sz;             /* ziplist size in bytes */
    // 当前节点的ziplist的entry个数
    unsigned int count : 16;     /* count of items in ziplist */
    // 编码方式：1.ziplist  2.lzf压缩模式
    unsigned int encoding : 2;   /* RAW==1 or LZF==2 */
    unsigned int container : 2;  /* NONE==1 or ZIPLIST==2 */
    unsigned int recompress : 1; /* was this node previous compressed? */
    unsigned int attempted_compress : 1; /* node can't compress; too small */
    unsigned int extra : 10; /* more bits to steal for future usage */
} quicklistNode;
```





这里compress为1，表示前后1个entry不压缩，中间的entry都压缩

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312212257536.png)



**QuickList的特点**

* 是一个节点为ZipList的双端链表
* 节点采用ZipList，解决了传统链表的内存占用问题
* 控制了ZipList大小，解决连续内存空间申请效率问题
* 中间节点可以压缩，进一步节省了内存







#### 1.7 SkipList

skipList（跳表）首先是链表，但与传统链表相比有几点差异：

- 元素按照升序【每个元素是一个SDS字符串，是根据得分进行排序】排列存储
- 节点可能包含多个指针，指针跨度不同【这样就不用像普通链表一样，访问元素需要一个一个遍历，可以类似于二分的方式直接到中间】。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312221127729.png)



**zskiplist**

```c
typedef struct zskiplist {
    // 头尾指针节点
    struct zskiplistNode *header, *tail;
    // 节点数量
    unsigned long length;
    // 最大索引层数，默认是1
    int level;
} zskiplist;
```

**zskiplistNode**

```c
typedef struct zskiplistNode {
    sds ele; // 节点存储的值
    double score; // 节点分数，用于排序，查找
    struct zskiplistNode *backward; // 前一个节点指针
    struct zskiplistLevel {
        struct zskiplistNode *forward; // 下一个节点指针
        unsigned long span; // 索引跨度
    } level[]; // 多级索引数组
} zskiplistNode;
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312221144804.png)



**SkipList的特点：**

* 跳跃表是一个双向链表，每个节点都包含score和ele值
* 节点按照score值排序，score值一样则按照ele字典排序
* 每个节点都可以包含多层指针，层数是1到32之间的随机数【通过算法选择最佳层数】
* 不同层指针到下一个节点的跨度不同，层级越高，跨度越大
* 增删改查效率与红黑树基本一致，实现却更简单







#### 1.8 RedisObject

Redis中的任意数据类型的键和值都会被封装为一个`RedisObject`，也叫做Redis对象

> RedisObject的头信息占用了16字节，如果使用string类型，一个string类型的数据就有16字节的头信息。而如果使用list这些集合结构，那么就只会有一个16字节的头信息

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312221202844.png)



- 从Redis的使用者的角度来看，⼀个Redis节点包含多个database（非cluster模式下默认是16个，cluster模式下只能是1个），而一个database维护了从key space到object space的映射关系。这个映射关系的key是固定的string类型，⽽value可以是多种数据类型，比如：`string`，`list`，`hash`，`set`，`sorted`

- 从Redis内部实现的⾓度来看，database内的这个映射关系是用⼀个dict来维护的。dict的key固定用⼀种数据结构来表达就够了，这就是动态字符串sds。而value则比较复杂，为了在同⼀个dict内能够存储不同类型的value，这就需要⼀个通⽤的数据结构，这个通用的数据结构就是robj，全名是redisObject。



**Redis的编码方式**

Redis中会根据存储的数据类型不同，选择不同的编码方式，共包含11种不同类型：

| **编号** | **编码方式**            | **说明**               |
| -------- | ----------------------- | ---------------------- |
| 0        | OBJ_ENCODING_RAW        | raw编码动态字符串      |
| 1        | OBJ_ENCODING_INT        | long类型的整数的字符串 |
| 2        | OBJ_ENCODING_HT         | hash表（字典dict）     |
| 3        | OBJ_ENCODING_ZIPMAP     | 已废弃                 |
| 4        | OBJ_ENCODING_LINKEDLIST | 双端链表               |
| 5        | OBJ_ENCODING_ZIPLIST    | 压缩列表               |
| 6        | OBJ_ENCODING_INTSET     | 整数集合               |
| 7        | OBJ_ENCODING_SKIPLIST   | 跳表                   |
| 8        | OBJ_ENCODING_EMBSTR     | embstr的动态字符串     |
| 9        | OBJ_ENCODING_QUICKLIST  | 快速列表               |
| 10       | OBJ_ENCODING_STREAM     | Stream流               |



**五种数据结构**

Redis中会根据存储的数据类型不同，选择不同的编码方式。基本数据类型就是这5种，像`bitMap`、`Hyperloglog`都是基本string实现的，`geo`是基于zset实现的

| **数据类型** | **编码方式**                                       |
| ------------ | -------------------------------------------------- |
| OBJ_STRING   | int、embstr、raw                                   |
| OBJ_LIST     | LinkedList和ZipList(3.2以前)、QuickList（3.2以后） |
| OBJ_SET      | intset、HT                                         |
| OBJ_ZSET     | ZipList、HT、SkipList                              |
| OBJ_HASH     | ZipList、HT                                        |





### 2、五种数据类型

#### 2.1 String

String是Redis中最常见的数据存储类型

- 其基本编码方式是`RAW`，基于简单动态字符串（SDS）实现，存储上限为512mb。申请内存时，需要申请两次，Object头一次，SDS字符串一次

- 如果存储的SDS长度小于等于**44字节**，则会采用`EMBSTR`编码，此时object head与SDS是一段连续空间。申请内存时只需要调用一次内存分配函数，效率更高。


**为什么是44字节？**

1、因为现在字符串长度小于等于44字节，所以`len`和`alloc`都占用1字节，`flags`占用1字节，结束位占用1字节，加上字符串的44字节，一共是48字节。48字节加上Object头的16字节，刚好是64字节。

2、Redis底层内存分配算法使用的是`jemalloc`，分配内存时会以`2^n`进行分配，64恰好是一个分片大小，不会产生内存碎片，因此申请一次内存就可以存储Object头和字符串的内容了

3、如果存储的字符串是整数值，并且大小在LONG_MAX范围内，则会采用INT编码：直接将数据保存在RedisObject的ptr指针位置（刚好8字节），不再需要SDS了。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312221236093.png)





> 1、String在Redis中是⽤⼀个robj来表示的。用来表示String的robj可能编码成3种内部表⽰：`OBJ_ENCODING_RAW`，`OBJ_ENCODING_EMBSTR`，`OBJ_ENCODING_INT`
>
> 2、其中前两种编码使⽤的是`sds`来存储，最后⼀种`OBJ_ENCODING_INT`编码直接把string存成了long型。
>
> 3、在对string进行`incr`, `decr`等操作的时候，如果它内部是`OBJ_ENCODING_INT`编码，那么可以直接行加减操作；如果它内部是`OBJ_ENCODING_RAW`或`OBJ_ENCODING_EMBSTR`编码，那么Redis会先试图把sds存储的字符串转成long型，如果能转成功，再进行加减操作。
>
> 4、对⼀个内部表示成long型的string执行`append`, `setbit`, `getrange`这些命令，针对的仍然是string的值（即⼗进制表示的字符串），而不是针对内部表⽰的long型进⾏操作。比如字符串”32”，如果按照字符数组来解释，它包含两个字符，它们的ASCII码分别是0x33和0x32。当我们执行命令`setbit key 7 0`的时候，相当于把字符0x33变成了0x32，这样字符串的值就变成了”22”。⽽如果将字符串”32”按照内部的64位long型来解释，那么它是0x0000000000000020，在这个基础上执⾏setbit位操作，结果就完全不对了。因此，在这些命令的实现中，会把long型先转成字符串再进行相应的操作。





#### 2.2 List

Redis的List类型可以从首、尾操作列表中的元素

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231454692.png)

* `LinkedList `：普通链表，可以从双端访问，内存占用较高【指针占用内存】，内存碎片较多
* `ZipList` ：压缩列表，可以从双端访问，内存占用低，存储上限低
* `QuickList`：`LinkedList + ZipList`，可以从双端访问，内存占用较低，包含多个ZipList，存储上限高

在3.2版本之前，Redis采用ZipList和LinkedList来实现List，当元素数量小于512并且元素大小小于64字节时采用ZipList编码，超过则采用LinkedList编码。

在3.2版本之后，Redis统一采用QuickList来实现List

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231449923.png)





**源码分析**

执行`lpush`或者`rpush`命令后，都是执行`pushGenericCommand`命令，只不过插入位置不同

```c
void lpushCommand(client *c) {
    pushGenericCommand(c,LIST_HEAD,0);
}

void rpushCommand(client *c) {
    pushGenericCommand(c,LIST_TAIL,0);
}
```

`pushGenericCommand`函数

```c
/*
	xx为true表示只有当前这个list存在，才会push，否则不会push，xx为false，插入时list不存在会自动创建
	redis的客户端和服务端建立连接后都会被封装为一个client对象，包含客户端的各种信息，包括客户端要执行的命令
*/
void pushGenericCommand(client *c, int where, int xx) {
    int j;

    // lpush key v1 v2 v3
    // j=2，就是从v1开始，遍历插入的元素，判断元素大小是否超过LIST_MAX_ITEM_SIZE(1<<32 -1024)
    for (j = 2; j < c->argc; j++) {
        if (sdslen(c->argv[j]->ptr) > LIST_MAX_ITEM_SIZE) {
            addReplyError(c, "Element too large");
            return;
        }
    }

    // 找到key对应的list，c->db表示客户端使用的是哪个数据库，c->argv[1]就是key，根据key找value，value就是List，封装为robj
    robj *lobj = lookupKeyWrite(c->db, c->argv[1]);
    // 检查类型是否正确
    if (checkType(c,lobj,OBJ_LIST)) return;
    // 检测是否为空
    if (!lobj) {
        // 如果list为空，同时xx为true就不能插入
        if (xx) {
            addReply(c, shared.czero);
            return;
        }

        // 否则创建Quicklist
        lobj = createQuicklistObject();
        /*
            对Quicklist做一些限制
            server.list_max_ziplist_size表示每个ziplist的大小，默认-2，即8kb
            server.list_compress_depth表示头尾不压缩的个数，默认为0，不压缩
        */ 
        quicklistSetOptions(lobj->ptr, server.list_max_ziplist_size,
                            server.list_compress_depth);
        // 将key对应的value设置为创建的Quicklist
        dbAdd(c->db,c->argv[1],lobj);
    }

    // 将所有的value插入Quicklist
    for (j = 2; j < c->argc; j++) {
        listTypePush(lobj,c->argv[j],where);
        server.dirty++;
    }

    addReplyLongLong(c, listTypeLength(lobj));

    char *event = (where == LIST_HEAD) ? "lpush" : "rpush";
    signalModifiedKey(c,c->db,c->argv[1]);
    notifyKeyspaceEvent(NOTIFY_LIST,event,c->argv[1],c->db->id);
}

robj *createQuicklistObject(void) {
    // 申请内存并初始化quicklist
    quicklist *l = quicklistCreate();
    // 创建RedisObject，type为OBJ_LIST，ptr指向quicklist
    robj *o = createObject(OBJ_LIST,l);
    // 设置编码为QUICKLIST
    o->encoding = OBJ_ENCODING_QUICKLIST;
    return o;
}

int checkType(client *c, robj *o, int type) {
    /* A NULL is considered an empty key */
    if (o && o->type != type) {
        addReplyErrorObject(c,shared.wrongtypeerr);
        return 1;
    }
    return 0;
}
```







#### 2.3 Set

Set是Redis中的单列集合

* 不保证有序性
* 保证元素唯一
* 求交集、并集、差集



Set集合在添加元素时需要判断元素是否存在，对查询元素的效率要求非常高，因此使用`Dict`结构。

- Dict中的key用来存储元素，value统一为null。
- 当存储的所有数据都是整数，并且元素数量不超过`set-max-intset-entries`【默认512，可以在服务端设置】时，Set会采用`IntSet`编码，以节省内存



**源码分析**

1、创建Set集合

```c
robj *setTypeCreate(sds value) {
    // 判断添加元素的类型，如果是long，就创建INTSET
    if (isSdsRepresentableAsLongLong(value,NULL) == C_OK)
        return createIntsetObject();
   	// 否则创建Set
    return createSetObject();
}

// 创建INTSET
robj *createIntsetObject(void) {
    intset *is = intsetNew();
    robj *o = createObject(OBJ_SET,is);
    o->encoding = OBJ_ENCODING_INTSET;
    return o;
}

//创建Set
robj *createSetObject(void) {
    dict *d = dictCreate(&setDictType,NULL);
    robj *o = createObject(OBJ_SET,d);
    o->encoding = OBJ_ENCODING_HT;
    return o;
}
```

2、添加元素

- 如果当前是HT类型，那么元素直接添加进去
- 如果当前是`INTSET`
  - 当前元素为long类型，直接添加进去，如果元素数量超过`set_max_intset_entries`【默认512】，就转为HT类型
  - 否则，将`INTSET`转为`HT`结构，然后添加元素

```c
int setTypeAdd(robj *subject, sds value) {
    long long llval;
    if (subject->encoding == OBJ_ENCODING_HT) { // 已经是HT结构，直接添加元素
        dict *ht = subject->ptr;
        dictEntry *de = dictAddRaw(ht,value,NULL);
        if (de) {
            dictSetKey(ht,de,sdsdup(value));
            dictSetVal(ht,de,NULL);
            return 1;
        }
    } else if (subject->encoding == OBJ_ENCODING_INTSET) {  // INTSET结构
        // 判断value是否是long
        if (isSdsRepresentableAsLongLong(value,&llval) == C_OK) {
            uint8_t success = 0;
            // 是整数，直接添加元素到set
            subject->ptr = intsetAdd(subject->ptr,llval,&success);
            if (success) {
                /* Convert to regular set when the intset contains
                 * too many entries. */
                // 当元素数量超出set_max_intset_entries，转为HT
                size_t max_entries = server.set_max_intset_entries;
                /* limit to 1G entries due to intset internals. */
                if (max_entries >= 1<<30) max_entries = 1<<30;
                if (intsetLen(subject->ptr) > max_entries)
                    setTypeConvert(subject,OBJ_ENCODING_HT);
                return 1;
            }
        // vlaue不是long类型，将INSET结构转为HT结构
        } else {
            /* Failed to get integer from object, convert to regular set. */
            setTypeConvert(subject,OBJ_ENCODING_HT);

            /* The set *was* an intset and this value is not integer
             * encodable, so dictAdd should always work. */
            serverAssert(dictAdd(subject->ptr,sdsdup(value),NULL) == DICT_OK);
            return 1;
        }
    } else {
        serverPanic("Unknown set encoding");
    }
    return 0;
}

// 添加元素，key就是插入的元素
dictEntry *dictAddRaw(dict *d, void *key, dictEntry **existing)
{
    long index;
    dictEntry *entry;
    dictht *ht;

    if (dictIsRehashing(d)) _dictRehashStep(d);

    /* Get the index of the new element, or -1 if
     * the element already exists. */
    // 找到key应该插入的位置，如果key已经存在，就返回-1退出
    if ((index = _dictKeyIndex(d, key, dictHashKey(d,key), existing)) == -1)
        return NULL;

    // 如果正在rehash，就添加到ht[1]，否则添加到ht[0]
    ht = dictIsRehashing(d) ? &d->ht[1] : &d->ht[0];
    // 创建entry
    entry = zmalloc(sizeof(*entry));
    // 头插法
    entry->next = ht->table[index];
    ht->table[index] = entry;
    ht->used++;

    /* Set the hash entry fields. */
    dictSetKey(d, entry, key);
    return entry;
}
```



> 初始为`INTSET`，执行`sadd s1 m1`后，将`INTSET`转为HT

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231514631.png)







#### 2.4 ZSET

ZSet也就是SortedSet，其中每一个元素都需要指定一个score值和member值

* 可以根据score值排序
* member必须唯一
* 可以根据member查询分数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231536612.png)

因此，ZSET底层数据结构必须满足**键值存储**、**键唯一**、**可排序**这几个需求。

* `SkipList`：可以排序，并且可以同时存储score和ele值（member）
* `HT（Dict）`：可以键值存储，并且可以根据key找value

所以ZSET底层采用的是`SkipList`和`Dict`结合的方式实现，不过缺点是内存占用大，一份数据存了两次，属于空间换时间

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231539457.png" style="zoom:80%;" />



当元素数量不多时，HT和SkipList的优势不明显，而且更耗内存。因此zset还会采用ZipList结构来节省内存，不过需要同时满足两个条件：

* 元素数量小于`zset_max_ziplist_entries`，默认值128
* 每个元素都小于`zset_max_ziplist_value`字节，默认值64

ziplist本身没有排序功能，而且没有键值对的概念，因此需要由zset通过编码实现：

* ZipList是连续内存，因此score和element是紧挨在一起的两个entry， element在前，score在后
* score越小越接近队首，score越大越接近队尾，按照score值升序排列

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231539665.png)







**源码分析**

1、zset结构

```c
typedef struct zset {
    dict *dict;
    zskiplist *zsl;
} zset;
```

2、创建zset

- 通过key找value，如果value不存在
  - 如果`zset_max_ziplist_entries`为0或者`zset_max_ziplist_value`小于添加元素的大小，就创建`zset`
  - 否则创建`ziplist`

```c
void zaddGenericCommand(client *c, int flags) {
	...
    robj *key = c->argv[1];
    robj *zobj;
    // zadd添加元素时，先根据key找到zset
    zobj = lookupKeyWrite(c->db,key);
    if (checkType(c,zobj,OBJ_ZSET)) goto cleanup;
    // 如果zset不存在，就创建
    if (zobj == NULL) {
        if (xx) goto reply_to_client; /* No key + XX option: nothing to do. */
        // 如果zset_max_ziplist_entries为0，表示不会创建ziplist，如果当前添加元素的大小大于zset_max_ziplist_value，那么就创建zset
        if (server.zset_max_ziplist_entries == 0 ||
            server.zset_max_ziplist_value < sdslen(c->argv[scoreidx+1]->ptr))
        {
            zobj = createZsetObject();
        } else { // 否则创建ziplist
            zobj = createZsetZiplistObject();
        }
        dbAdd(c->db,key,zobj);
    }
    ...
}

// 创建zset
robj *createZsetObject(void) {
    zset *zs = zmalloc(sizeof(*zs));
    robj *o;

    zs->dict = dictCreate(&zsetDictType,NULL);
    zs->zsl = zslCreate();
    o = createObject(OBJ_ZSET,zs);
    o->encoding = OBJ_ENCODING_SKIPLIST;
    return o;
}

// 创建ziplist
robj *createZsetZiplistObject(void) {
    unsigned char *zl = ziplistNew();
    robj *o = createObject(OBJ_ZSET,zl);
    o->encoding = OBJ_ENCODING_ZIPLIST;
    return o;
}
```

3、添加元素

```c
int zsetAdd(robj *zobj, double score, sds ele, int in_flags, int *out_flags, double *newscore) {
    
    // 如果当前是ZIPLIST类型
    if (zobj->encoding == OBJ_ENCODING_ZIPLIST) {
        unsigned char *eptr;
		
        // 判断当前元素是否存在，如果存在，直接更新score值即可
        if ((eptr = zzlFind(zobj->ptr,ele,&curscore)) != NULL) {
            /* NX? Return, same element already exists. */
            if (nx) {
                *out_flags |= ZADD_OUT_NOP;
                return 1;
            }

            /* Prepare the score for the increment if needed. */
            if (incr) {
                score += curscore;
                if (isnan(score)) {
                    *out_flags |= ZADD_OUT_NAN;
                    return 0;
                }
            }

            /* GT/LT? Only update if score is greater/less than current. */
            if ((lt && score >= curscore) || (gt && score <= curscore)) {
                *out_flags |= ZADD_OUT_NOP;
                return 1;
            }

            if (newscore) *newscore = score;

            /* Remove and re-insert when score changed. */
            if (score != curscore) {
                zobj->ptr = zzlDelete(zobj->ptr,eptr);
                zobj->ptr = zzlInsert(zobj->ptr,ele,score);
                *out_flags |= ZADD_OUT_UPDATED;
            }
            return 1;
        // 当前元素不存在
        } else if (!xx) {
            // 添加元素后，元素数量如果大于server.zset_max_ziplist_entries  或者 当前元素大小大于 server.zset_max_ziplist_value
            // 或者元素总大小超过阈值，就将ziplist转为Dict+Skiplist的结构
            if (zzlLength(zobj->ptr)+1 > server.zset_max_ziplist_entries ||
                sdslen(ele) > server.zset_max_ziplist_value ||
                !ziplistSafeToAdd(zobj->ptr, sdslen(ele)))
            {
                zsetConvert(zobj,OBJ_ENCODING_SKIPLIST);
            } else { // 否则添加元素
                zobj->ptr = zzlInsert(zobj->ptr,ele,score);
                if (newscore) *newscore = score;
                *out_flags |= ZADD_OUT_ADDED;
                return 1;
            }
        } else {
            *out_flags |= ZADD_OUT_NOP;
            return 1;
        }
    }

    // 当前编码为SKIPLIST，无需转换
    if (zobj->encoding == OBJ_ENCODING_SKIPLIST) {
        zset *zs = zobj->ptr;
        zskiplistNode *znode;
        dictEntry *de;

        de = dictFind(zs->dict,ele);
        if (de != NULL) {
            /* NX? Return, same element already exists. */
            if (nx) {
                *out_flags |= ZADD_OUT_NOP;
                return 1;
            }

            curscore = *(double*)dictGetVal(de);

            /* Prepare the score for the increment if needed. */
            if (incr) {
                score += curscore;
                if (isnan(score)) {
                    *out_flags |= ZADD_OUT_NAN;
                    return 0;
                }
            }

            /* GT/LT? Only update if score is greater/less than current. */
            if ((lt && score >= curscore) || (gt && score <= curscore)) {
                *out_flags |= ZADD_OUT_NOP;
                return 1;
            }

            if (newscore) *newscore = score;

            /* Remove and re-insert when score changes. */
            if (score != curscore) {
                znode = zslUpdateScore(zs->zsl,curscore,ele,score);
                /* Note that we did not removed the original element from
                 * the hash table representing the sorted set, so we just
                 * update the score. */
                dictGetVal(de) = &znode->score; /* Update score ptr. */
                *out_flags |= ZADD_OUT_UPDATED;
            }
            return 1;
        } else if (!xx) {
            ele = sdsdup(ele);
            znode = zslInsert(zs->zsl,score,ele);
            serverAssert(dictAdd(zs->dict,ele,&znode->score) == DICT_OK);
            *out_flags |= ZADD_OUT_ADDED;
            if (newscore) *newscore = score;
            return 1;
        } else {
            *out_flags |= ZADD_OUT_NOP;
            return 1;
        }
    } else {
        serverPanic("Unknown sorted set encoding");
    }
    return 0; /* Never reached. */
}
```





#### 2.5 Hash

Hash结构与Redis中的Zset非常类似

* 都是键值存储
* 都需求根据键获取值
* 键必须唯一

区别如下

* zset的键是member，值是score；hash的键和值都是任意值
* zset要根据score排序；hash则无需排序



由于hash结构不需要排序，默认采用ZipList编码，用以节省内存。 ZipList中相邻的两个entry 分别保存field和value

- 当Hash中数据满足以下条件，使用ziplist进⾏存储数据，否则使用Dict
  - 元素数量小于`hash-max-ziplist-entries`，默认值512
  - 每个元素都小于`hash-max-ziplist-value`字节，默认值64




Redis的hash之所以这样设计，是因为当ziplist变得很⼤的时候，它有如下几个缺点：

* 每次插⼊或修改引发的realloc操作会有更⼤的概率造成内存拷贝，从而降低性能。
* ⼀旦发生内存拷贝，内存拷贝的成本也相应增加，因为要拷贝更⼤的⼀块数据。
* 当ziplist数据项过多的时候，在它上⾯查找指定的数据项就会性能变得很低，因为ziplist上的查找需要进行遍历。

总之，ziplist本来就设计为各个数据项挨在⼀起组成连续的内存空间，这种结构并不擅长做修改操作。⼀旦数据发⽣改动，就会引发内存realloc，可能导致内存拷贝。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312231627525.png)





**源码分析**

```c
void hsetCommand(client *c) {
    int i, created = 0;
    robj *o;

    if ((c->argc % 2) == 1) {
        addReplyErrorFormat(c,"wrong number of arguments for '%s' command",c->cmd->name);
        return;
    }

    // hset user name xrj age 20，argv[1]就是user，通过key判断hash结构是否存在，不存在就创建一个新的，默认采用ZipList编码
    if ((o = hashTypeLookupWriteOrCreate(c,c->argv[1])) == NULL) return;
    // 判断是否需要把ziplist转为Dict
    hashTypeTryConversion(o,c->argv,2,c->argc-1);

    // 遍历每一对field和value，执行hset命令
    for (i = 2; i < c->argc; i += 2)
        created += !hashTypeSet(o,c->argv[i]->ptr,c->argv[i+1]->ptr,HASH_SET_COPY);

    /* HMSET (deprecated) and HSET return value is different. */
    char *cmdname = c->argv[0]->ptr;
    if (cmdname[1] == 's' || cmdname[1] == 'S') {
        /* HSET */
        addReplyLongLong(c, created);
    } else {
        /* HMSET */
        addReply(c, shared.ok);
    }
    signalModifiedKey(c,c->db,c->argv[1]);
    notifyKeyspaceEvent(NOTIFY_HASH,"hset",c->argv[1],c->db->id);
    server.dirty += (c->argc - 2)/2;
}

robj *hashTypeLookupWriteOrCreate(client *c, robj *key) {
    // 通过key查找robj
    robj *o = lookupKeyWrite(c->db,key);
    if (checkType(c,o,OBJ_HASH)) return NULL;
    // 不存在就创建
    if (o == NULL) {
        o = createHashObject();
        dbAdd(c->db,key,o);
    }
    return o;
}
robj *createHashObject(void) {
    // 默认采用ziplist编码
    unsigned char *zl = ziplistNew();
    robj *o = createObject(OBJ_HASH, zl);
    o->encoding = OBJ_ENCODING_ZIPLIST;
    return o;
}

void hashTypeTryConversion(robj *o, robj **argv, int start, int end) {
    int i;
    size_t sum = 0;
	// 本身就不是ziplist编码，直接退出
    if (o->encoding != OBJ_ENCODING_ZIPLIST) return;

    // 遍历插入的field和value
    for (i = start; i <= end; i++) {
        // 如果不是sds类型，跳过
        if (!sdsEncodedObject(argv[i]))
            continue;
        size_t len = sdslen(argv[i]->ptr);
        // 如果field或者value的长度大于hash_max_ziplist_value,就转为HT
        if (len > server.hash_max_ziplist_value) {
            hashTypeConvert(o, OBJ_ENCODING_HT);
            return;
        }
        sum += len;
    }
    // 如果总大小大于1G，也转为HT
    if (!ziplistSafeToAdd(o->ptr, sum))
        hashTypeConvert(o, OBJ_ENCODING_HT);
}
```



```c
int hashTypeSet(robj *o, sds field, sds value, int flags) {
    int update = 0;
	// 当前编码为ziplist
    if (o->encoding == OBJ_ENCODING_ZIPLIST) {
        unsigned char *zl, *fptr, *vptr;

        zl = o->ptr;
        // 查询head指针
        fptr = ziplistIndex(zl, ZIPLIST_HEAD);
        if (fptr != NULL) { // head不为空，说明ziplist不为空，查找key
            fptr = ziplistFind(zl, fptr, (unsigned char*)field, sdslen(field), 1);
            if (fptr != NULL) { // 如果key存在，就更新value
                /* Grab pointer to the value (fptr points to the field) */
                vptr = ziplistNext(zl, fptr);
                serverAssert(vptr != NULL);
                update = 1;

                /* Replace value */
                zl = ziplistReplace(zl, vptr, (unsigned char*)value,
                        sdslen(value));
            }
        }
		// 如果key不存在，就添加进入
        if (!update) {
            /* Push new field/value pair onto the tail of the ziplist */
            zl = ziplistPush(zl, (unsigned char*)field, sdslen(field),
                    ZIPLIST_TAIL);
            zl = ziplistPush(zl, (unsigned char*)value, sdslen(value),
                    ZIPLIST_TAIL);
        }
        o->ptr = zl;

        /* 插入新元素，检查长度是否超出，超出就转为HT结构 */
        if (hashTypeLength(o) > server.hash_max_ziplist_entries)
            hashTypeConvert(o, OBJ_ENCODING_HT);
    // 当前编码为HT，直接插入即可
    } else if (o->encoding == OBJ_ENCODING_HT) {
        dictEntry *de = dictFind(o->ptr,field);
        if (de) {
            sdsfree(dictGetVal(de));
            if (flags & HASH_SET_TAKE_VALUE) {
                dictGetVal(de) = value;
                value = NULL;
            } else {
                dictGetVal(de) = sdsdup(value);
            }
            update = 1;
        } else {
            sds f,v;
            if (flags & HASH_SET_TAKE_FIELD) {
                f = field;
                field = NULL;
            } else {
                f = sdsdup(field);
            }
            if (flags & HASH_SET_TAKE_VALUE) {
                v = value;
                value = NULL;
            } else {
                v = sdsdup(value);
            }
            dictAdd(o->ptr,f,v);
        }
    } else {
        serverPanic("Unknown hash encoding");
    }

    /* Free SDS strings we did not referenced elsewhere if the flags
     * want this function to be responsible. */
    if (flags & HASH_SET_TAKE_FIELD && field) sdsfree(field);
    if (flags & HASH_SET_TAKE_VALUE && value) sdsfree(value);
    return update;
}
```







## 十一、Redis网络模型

### 1、用户态和内核态

> 1、ubuntu和Centos 都是Linux的发行版，发行版可以看成对linux包了一层壳，任何Linux发行版，其系统内核都是Linux。我们的应用都需要通过Linux内核与硬件交互
>
> 2、计算机硬件包括，如cpu，内存，网卡等等，内核（通过寻址空间）可以操作硬件的，但是内核需要不同设备的驱动，有了这些驱动之后，内核就可以去对计算机硬件去进行 内存管理，文件系统的管理，进程的管理等等。
>
> 3、我们想要用户的应用来访问，计算机就必须要通过对外暴露的一些接口，才能访问到，从而实现对内核的操控，但是内核本身上来说也是一个应用，所以他本身也需要一些内存，cpu等设备资源，用户应用本身也在消耗这些资源，如果不加任何限制，用户去随意的操作我们的资源，就有可能导致一些冲突，甚至有可能导致我们的系统出现无法运行的问题，因此我们需要把用户和**内核隔离开**
>
> 4、进程的寻址空间划分成两部分：**内核空间、用户空间**。我们的应用程序也好，还是内核空间也好，都是没有办法直接去操作物理内存的，而是通过分配一些虚拟内存映射到物理内存中，我们的内核和应用程序去访问虚拟内存的时候，就需要一个虚拟地址，这个地址是一个无符号的整数，比如一个32位的操作系统，他的带宽就是32，他的虚拟地址就是2的32次方，也就是说他寻址的范围就是0~2^32， 这片寻址空间对应的就是2^32个字节，就是4GB，这个4GB，会有3个GB分给用户空间，会有1GB给内核系统

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251126833.png" style="zoom:60%;" /> <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251128944.png" style="zoom:67%;" />





在linux中，权限分成两个等级，0和3，用户空间只能执行受限的命令（Ring3），而且不能直接调用系统资源，必须通过内核提供的接口来访问

内核空间可以执行特权命令（Ring0），调用一切系统资源，所以一般情况下，用户的操作是运行在用户空间，而内核运行的数据是在内核空间的，而有的情况下，一个应用程序需要去调用一些特权资源，去调用一些内核空间的操作，所以此时他俩需要在用户态和内核态之间进行切换。

比如：

Linux系统为了提高IO效率，会在用户空间和内核空间都加入缓冲区：

写数据时，要把用户缓冲数据拷贝到内核缓冲区，然后写入设备

读数据时，要从设备读取数据到内核缓冲区，然后拷贝到用户缓冲区

针对这个操作：用户在写读数据时，会去向内核态申请，想要读取内核的数据，而内核数据要去等待驱动程序从硬件上读取数据，当从磁盘上加载到数据之后，内核会将数据写入到内核的缓冲区中，然后再将数据拷贝到用户态的buffer中，然后再返回给应用程序，整体而言，速度慢，就是这个原因，为了加速，我们希望read也好，还是wait for data，最好都不要等待，或者时间尽量的短。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251341784.png" style="zoom:80%;" />



5种IO模型：

* 阻塞IO（Blocking IO）
* 非阻塞IO（Nonblocking IO）
* IO多路复用（IO Multiplexing）
* 信号驱动IO（Signal Driven IO）
* 异步IO（Asynchronous IO）





### 2、阻塞IO

应用程序想要去读取数据，他是无法直接去读取磁盘数据的，他需要先到内核里边去等待内核操作硬件拿到数据，这个过程就是1，是需要等待的。等到内核从磁盘上把数据加载出来之后，再把这个数据写给用户的缓存区，这个过程是2，如果是阻塞IO，那么整个过程中，用户从发起读请求开始，一直到读取到数据，都是一个阻塞状态。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251352584.png)





**阻塞IO流程**

用户去读取数据时，会去先发起`recvform`一个命令，去尝试从内核上加载数据，如果内核没有数据，那么用户就会等待，此时内核会去从硬件上读取数据，内核读取数据之后，会把数据拷贝到用户态，并且返回ok，整个过程，都是阻塞等待的，这就是阻塞IO

阶段一

- 用户进程尝试读取数据（比如网卡数据）
- 此时数据尚未到达，内核需要等待数据
- 此时用户进程也处于阻塞状态

阶段二

* 数据到达并拷贝到内核缓冲区，代表已就绪
* 将内核数据拷贝到用户缓冲区
* 拷贝过程中，用户进程依然阻塞等待
* 拷贝完成，用户进程解除阻塞，处理数据

阻塞IO模型中，用户进程在两个阶段都是阻塞状态。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251352554.png" style="zoom:67%;" />







### 3、非阻塞IO

非阻塞IO的`recvfrom`操作会立即返回结果而不是阻塞用户进程。



阶段一：

* 用户进程尝试读取数据（比如网卡数据）
* 此时数据尚未到达，内核需要等待数据
* 返回异常给用户进程
* 用户进程拿到error后，再次尝试读取
* 循环往复，直到数据就绪

阶段二：

* 将内核数据拷贝到用户缓冲区
* 拷贝过程中，用户进程依然阻塞等待
* 拷贝完成，用户进程解除阻塞，处理数据

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251355770.png" style="zoom:67%;" />

非阻塞IO模型中，用户进程在第一个阶段是非阻塞，第二个阶段是阻塞状态。虽然是非阻塞，但性能并没有得到提高。而且忙等机制会导致CPU空转，CPU使用率暴增。







### 4、IO多路复用

无论是阻塞IO还是非阻塞IO，用户应用在一阶段都需要调用`recvfrom`来获取数据

- 如果调用`recvfrom`时，恰好没有数据，阻塞IO会使CPU阻塞，非阻塞IO使CPU空转，都不能充分发挥CPU的作用。
- 如果调用`recvfrom`时，恰好有数据，则用户进程可以直接进入第二阶段，读取并处理数据

在单线程情况下，只能依次处理IO事件，如果正在处理的IO事件恰好未就绪（数据不可读或不可写），线程就会被阻塞，所有IO事件都需要等待，性能会很差。



IO多路复用就是，哪个socket的数据准备好了，那么我就去读取对应数据

> 文件描述符（File Descriptor）：简称FD，是一个从0 开始的无符号整数，用来关联Linux中的一个文件。在Linux中，一切皆文件，例如常规文件、视频、硬件设备等，当然也包括网络套接字（Socket）。
>
> 通过FD，我们的网络模型可以利用一个线程监听多个FD，并在某个FD可读、可写时得到通知，从而避免无效的等待，充分利用CPU资源。



阶段一：

* 用户进程调用select，指定要监听的FD集合
* 内核监听FD对应的多个socket
* 任意一个或多个socket数据就绪则返回readable
* 此过程中用户进程阻塞

阶段二：

* 用户进程找到就绪的socket
* 依次调用recvfrom读取数据
* 内核将数据拷贝到用户空间
* 用户进程处理数据

当用户去读取数据的时候，不再去直接调用`recvfrom`了，而是调用`select`函数，`select`函数会将需要监听的数据交给内核，由内核去检查这些数据是否就绪了，如果说这个数据就绪了，就会通知应用程序数据就绪，然后来读取数据，再从内核中把数据拷贝给用户态，完成数据处理，如果N多个FD一个都没处理完，此时就进行等待。

用IO多路复用模式，可以确保去读数据的时候，数据是一定存在的，他的效率比原来的阻塞IO和非阻塞IO性能都要高

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251420751.png" style="zoom:80%;" />





IO多路复用是利用单个线程来同时监听多个FD，并在某个FD可读、可写时得到通知，从而避免无效的等待，充分利用CPU资源。不过监听FD的方式、通知的方式又有多种实现，常见的有：

- select
- poll
- epoll

select和pool相当于是当被监听的数据准备好之后，他会把你监听的FD整个数据都发给你，你需要到整个FD中去找，哪些是处理好了的，需要通过遍历的方式，所以性能也并不是那么好。而epoll，则相当于内核准备好了之后，他会把准备好的数据，直接发给你，省去了遍历的动作。





#### 4.1 select

我们把需要处理的数据封装成FD，然后在用户态创建一个fd的集合（这个集合的大小是要监听的那个FD的最大值+1，但是大小整体是有限制的 ），这个集合的长度大小是有限制的，同时在这个集合中，标明出来我们要监控哪些数据，

下面是select的源码，其中`fd_set`是要监听的fd集合，是一个大小为32的数组，而数组元素是`__fd_mask`类型的，`__fd_mask`是32位大小，因此`fd_set`数组大小为32，但是可以表示1024个bit位，一个bit位就代表一个fd，因此最多可以存储1024的fd

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251447031.png" style="zoom:80%;" />





**执行流程**

1、创建`fd_set`，大小为1024bit

2、假如要监听的数据是1，2，5，将1，2，5三个数据的位置置位1，然后执行select函数，同时将整个fd发给内核态

3、内核态会去遍历用户态传递过来的数据，如果发现这里边的数据都没有就绪，就休眠，直到有数据准备好时，就会被唤醒，唤醒之后，再次遍历一遍，看看谁准备好了，然后处理掉没准备好的数据，最后再将这个FD集合写回到用户态中去，返回就绪的数量

4、此时用户态就知道有数据准备好了，但是对于用户态而言，并不知道谁处理好了，所以用户态也需要去进行遍历，然后找到对应准备好数据的节点，再去发起读请求。

5、继续执行步骤2，使用select监听未准备好的数据

​                     <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251445337.jpg" style="zoom:80%;" />        <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251447400.png" style="zoom:80%;" />





**select模式缺点**

- 需要将整个`fd_set`从用户空间拷贝到内核空间，select结束还要再次拷贝回用户空间
- select无法得知哪个fd准备好了，需要遍历整个`fd_set`
- `fd_set`监听的fd数量不能超过1024





#### 4.2 poll

poll模式对select模式做了简单改进，但性能提升不明显。

调用`poll`函数时，需要创建多个`pollfd`结构体，形成数组传进去，此时`pollfd`只需指定`fd`和`events`，内核监听到数据后，将发生的事件传入`revents`中，然后拷贝给用户空间，如果`poll`超时未监听到数据就绪，就将`revents`置位0，表示没有事件发生

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251521206.png" style="zoom:80%;" />

IO流程：

* 创建`pollfd`数组，向其中添加监听的fd信息，数组大小自定义
* 调用`poll`函数，将`pollfd`数组拷贝到内核空间，转链表存储，无上限
* 内核遍历`pollfd`数组，判断是否就绪
* 数据就绪或超时后，拷贝`pollfd`数组到用户空间，返回就绪fd数量n
* 用户进程判断n是否大于0，大于0则遍历`pollfd`数组，找到就绪的fd



**与select对比**

* select模式中的fd_set大小固定为1024，而pollfd在内核中采用链表，理论上无上限
* 监听FD越多，每次遍历消耗时间也越久，性能反而会下降





#### 4.3 epoll

epoll模式是对select和poll的改进

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251541644.jpg" style="zoom:80%;" />        <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251542352.jpg" style="zoom:80%;" />



1、`eventpoll`：内部包含两个元素

- 红黑树：记录要监听的FD

- 链表：记录就绪的FD

2、`epoll_create`：调用该函数，会在内核中创建`eventpoll`的结构体，返回对应的句柄

3、紧接着调用`epoll_ctl`操作，将要监听的数据添加到红黑树上去，并且给每个fd设置一个`ep_poll_callback`，这个函数会在fd数据就绪时触发，数据准备好了，就将fd的数据添加到`list_head`中去

4、调用`epoll_wait`函数等待，在用户态创建一个空的events数组，当就绪之后，我们的回调函数会把数据添加到`list_head`中去，当调用这个函数的时候，会去检查`list_head`，这个过程需要参考配置的等待时间，可以等一定时间，也可以一直等， 如果在此过程中，检查到了`list_head`中有数据会将数据添加到链表中，此时将数据放入到events数组中，并且返回对应的操作的数量，用户态此时收到响应后，从events中拿到对应准备好的数据的节点，再去调用方法去拿数据。



`select`模式存在的三个问题

* 能监听的FD数量最大不超过1024
* 每次select都需要把所有要监听的FD都拷贝到内核空间，同时内核态监听到数据就绪后，需要将所有的FD拷贝回用户空间
* 每次都要遍历所有FD来判断就绪状态。当数据就绪后，内核态需要遍历所有的FD，以判断是哪个FD就绪，然后将所有FD拷贝回用户空间



`poll`模式的问题

* poll利用链表解决了select中监听FD上限的问题，但依然要遍历所有FD，如果监听较多，性能会下降



`epoll`模式

* 基于epoll实例中的红黑树保存要监听的FD，理论上无上限，而且增删改查效率都非常高
* 每个FD只需要执行一次`epoll_ctl`添加到红黑树，以后每次`epol_wait`无需传递任何参数，无需重复拷贝FD到内核空间。不用像`select`那样，每次都需要将需要监听的FD拷贝到内核空间
* 利用`ep_poll_callback`机制来监听FD状态，只要数据就绪，就将对应的FD放入`list_head`，无需遍历所有FD，因此性能不会随监听的FD数量增多而下降
* 调用`epoll_wait`时，将就绪的FD拷贝到用户空间的`events`中，每次只拷贝就绪的FD，不像`select`一样拷贝所有FD





#### 4.4 epoll中的ET和LT

当FD有数据可读时，我们调用`epoll_wait`（或者select、poll）可以得到通知。事件通知的模式有两种：

* `EdgeTriggered`：简称ET，也叫做边沿触发。只有在某个FD有状态变化时，调用`epoll_wait`才会被通知。
* `LevelTriggered`：简称LT，也叫做水平触发。只要某个FD中有数据可读，每次调用`epoll_wait`都会得到通知。



例如：

1、假设一个客户端socket对应的FD已经注册到了epoll实例中

2、客户端socket发送了2kb的数据

3、服务端调用`epoll_wait`，得到通知FD就绪

4、服务端从FD读取了1kb数据

5、回到步骤3（再次调用`epoll_wait`，形成循环）

如果是LT模式，重复调用`epoll_wait`都会得到通知，如果是ET模式，只有第一次调用`epoll_wait`才会得到通知





调用`epoll_wait`在数据拷贝之前，会将数据从链表中断开，然后完成拷贝的动作。之后根据不同的模式执行不同操作

- ET：直接将数据从链表删除，因此再次调用`epoll_wait`就不会通知，如果第一次没有读取完数据，下次在读就读取不到残留数据

  解决方法：

  - 调用`epoll_wait`后，FD中还有数据，手动将FD添加到就绪列表中，调用`epoll_ctl`函数，修改FD上的状态，发现FD上还有就绪的数据，就会重新添加回就绪队列
  - 循环读取，一次性读取全部数据。注意：不能使用阻塞IO，使用阻塞IO如果读到FD中没有数据了，他会阻塞在这里等待，导致进行阻塞

- LT：如果发现数据还未读取完成，会重新将就绪的数据添加回链表，因此再次调用`epoll_wait`还会收到通知

  LT产生的问题：

  - 重复通知，效率有影响
  - 可能出现惊群现象：假设有n个进程同时监听同一个FD，调用`epoll_wait`读取数据，数据就绪后，这些进程都会被通知到可以读取数据，可能前一两个进程就将数据读取完毕，所以后续这些进程就没有必要去读取

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251542352.jpg" style="zoom:80%;" />







#### 4.5 epoll的服务端流程

1、服务器启动以后，服务端会去调用`epoll_create`，创建一个epoll实例，epoll实例中包含两个数据

- 红黑树（为空）：rb_root 用来去记录需要被监听的FD

- 链表（为空）：list_head，用来存放已经就绪的FD

2、创建好了之后，会去调用`epoll_ctl`函数，此函数会将需要监听的数据添加到rb_root中去，并且对当前这些存在于红黑树的节点设置回调函数，当这些被监听的数据一旦准备完成，就会被调用，而调用的结果就是将红黑树的fd添加到`list_head`中去(但是此时并没有完成)

3、当第二步完成后，就会调用`epoll_wait`函数，这个函数会去校验是否有数据准备完毕（因为数据一旦准备就绪，就会被回调函数添加到list_head中），在等待了一段时间后(可以进行配置)，没有FD就绪，就再次调用`epoll_wait`。如果有FD就绪，则进一步判断当前是什么事件，如果是建立连接事件，则调用`accept()` 接受客户端socket，拿到建立连接的socket，然后建立起来连接，同时将其FD注册到epoll中。如果是其他事件，则进行数据读写 

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251639734.png" style="zoom:67%;" />









### 5、信号驱动

信号驱动IO是与内核建立SIGIO的信号关联并设置回调，当内核有FD就绪时，会发出SIGIO信号通知用户，期间用户应用可以执行其它业务，无需阻塞等待。

阶段一：

* 用户进程调用sigaction，注册信号处理函数
* 内核返回成功，开始监听FD
* 用户进程不阻塞等待，可以执行其它业务
* 当内核数据就绪后，回调用户进程的SIGIO处理函数

阶段二：

* 收到SIGIO回调信号
* 调用recvfrom，读取
* 内核将数据拷贝到用户空间
* 用户进程处理数据

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251727385.png" style="zoom:80%;" />



**缺点**

当有大量IO操作时，信号较多，SIGIO处理函数不能及时处理可能导致信号队列溢出，而且内核空间与用户空间的频繁信号交互性能也较低。





### 6、异步IO

这种方式，用户态在试图读取数据后，不阻塞，当内核的数据准备完成后，也不会阻塞

他会由内核将所有数据处理完成后，由内核将数据写入到用户态中，然后才算完成，所以性能极高，不会有任何阻塞，全部都由内核完成，异步IO模型中，用户进程在两个阶段都是非阻塞状态。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251728983.png" style="zoom:67%;" />



**缺点**

用户进程调用`aio_read`后，去执行新的用户请求，新的用户请求又要调用`aio_read`去通知内核进行数据的拷贝，高并发情况下，内核积累的IO任务会很多，导致系统占用内存过多导致系统崩溃，所以使用异步IO必须做好对并发访问的限流，实现比较复杂





### 7、对比

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312251734502.png" style="zoom:80%;" />







### 8、Redis是单线程的吗？

**Redis是单线程还是多线程？**

* 如果仅仅聊Redis的核心业务部分（命令处理），答案是单线程
* 如果是聊整个Redis，那么答案就是多线程



在Redis版本迭代过程中，在两个重要的时间节点上引入了多线程的支持：

* Redis v4.0：引入多线程异步处理一些耗时较久的任务，例如异步删除命令unlink
* Redis v6.0：在核心网络模型中引入 多线程，进一步提高对于多核CPU的利用率

因此，对于Redis的核心网络模型，在Redis 6.0之前确实都是单线程。是利用epoll（Linux系统）这样的IO多路复用技术在事件循环中不断处理客户端情况。



**为什么Redis要选择单线程？**

* Redis是纯内存操作，执行速度非常快，它的性能瓶颈是网络延迟而不是执行速度，因此多线程并不会带来巨大的性能提升。
* 多线程会导致过多的上下文切换，带来不必要的开销
* 引入多线程会面临线程安全问题，必然要引入线程锁这样的安全手段，实现复杂度增高，而且性能也会大打折扣







### 9、单线程多线程网络模型变更

Redis通过**IO多路复用**来提高网络性能，并且支持各种不同的多路复用实现，并且将这些实现进行封装， 提供了统一的高性能事件库API库 AE，下边就是Redis对`epoll`、`poll`、`select`等操作使用统一API的封装

- `aeApiCreate`：创建多路复用程序，例如`epoll_create`
- `aeApiAddEvent`：注册FD，例如`epoll_ctl`
- `aeApiPoll`：等待FD就绪，比如`epoll_wait`、`select`、`poll`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312261604312.png)



在`ae.c`文件中，通过不同的模式导入不同的文件，这样调用API时就是执行的对应模式的操作

```c
#ifdef HAVE_EVPORT
#include "ae_evport.c"
#else
    #ifdef HAVE_EPOLL
    #include "ae_epoll.c"
    #else
        #ifdef HAVE_KQUEUE
        #include "ae_kqueue.c"
        #else
        #include "ae_select.c"
        #endif
    #endif
#endif
```





**Redis单线程网络模型流程**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312261612886.png)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312261631441.png" style="zoom:100%;" /><img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312261633982.png" style="zoom:100%;" />



1、`main`函数中首先执行`initServer()`初始化服务

- 调用`aeCreateEventLoop()`方法创建epoll实例，类似于`epoll_create`
- `listenToPort()`方法，监听TCP端口，创建`ServerSocker`，得到FD
- 将这个FD注册到epoll实例中，然后绑定一个`acceptTcpHandler`用于处理当前FD【redis服务端】的客户端连接请求
  - `acceptTcpHandler`是用来处理Redis客户端连接请求，首先接收当前socket的连接，得到FD，然后创建connection关联这个FD
  - 执行`connSetReadHandler`，首先将当前这个FD注册到epoll实例中，绑定`readQueryFromClient`方法处理客户端的读请求
- 通过`aeSetBeforeSleepProc`注册`aeApiPoll`方法前的处理器

2、执行`aeMain`方法开始监听事件循环

- 循环监听事件，执行`aeProcessEvents`方法
  - 调用前置处理器`beforeSleep`
  - 调用`aeApiPoll`方法，返回FD就绪的数量
  - 遍历所有就绪的FD，调用对应的处理器，初始时，这里就绪的FD就只有我们的Redis服务端，他收到数据一定是Redis客户端的连接请求，他会执行`acceptTcpHandler`方法处理这个请求

3、监听到数据后，如果是客户端的连接请求，那么执行`acceptTcpHandler`方法，将FD注册到epoll实例，同时通过`readQueryFromClient`方法绑定读处理器

- `readQueryFromClient`方法中，首先获取当前客户端，然后将请求的数据读取到`c->querybuf`缓冲区中，此时缓冲区中的数据是各种redis命令组成的，然后解析缓冲区中的数据，将其转为Redis命令，存入`c->argv`数组，例如`set name xrj`，最后通过`processCommand`方法执行该命令
- `processCommand`中，首先通过命令名称即`c->argv[0]`查找对应的command，Redis中将各种命令都映射为`xxCommand`，然后通过`c->cmd->proc(c)`执行命令，并得到返回结果，最后一步就是将返回结果写回客户端
- `addReply`方法中先将结果写到缓冲区中，然后将客户端添加到`server.clients_pending_write`这个队列中，最后写回客户端的操作是由之前`aeSetBeforeSleepProc`方法执行

4、`beforeSleep`方法中，通过迭代器从头遍历`server.clients_pending_write`这个队列，拿到对应客户端后，对该客户端绑定`sendReplyToClient`写处理器，用于将Redis的响应写回客户端socket

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312261634296.png)





**整体流程**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271540153.png)





**多线程网络模型**

Redis 6.0版本中引入了多线程，目的是为了提高IO读写效率。

- 在收到客户端命令时，需要将命令写入缓冲区，并解析命令，对于这个操作，采用多线程
- 在向客户端写回响应结果时，采用多线程的方式来写

而核心的命令执行、IO多路复用模块依然是由主线程执行。



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271539249.png)









## 十二、Redis通信协议

Redis是一个CS架构的软件，通信一般分两步（不包括pipeline和PubSub）：

- 客户端（client）向服务端（server）发送一条命令

- 服务端解析并执行命令，返回响应结果给客户端

因此客户端发送命令的格式、服务端响应结果的格式必须有一个规范，这个规范就是通信协议。



Redis中采用的是RESP（Redis Serialization Protocol）协议

- Redis 1.2版本引入了RESP协议

- Redis 2.0版本中成为与Redis服务端通信的标准，称为RESP2

- Redis 6.0版本中，从RESP2升级到了RESP3协议，增加了更多数据类型并且支持6.0的新特性：客户端缓存。但目前，默认使用的依然是RESP2协议





### 1、RESP协议

在RESP中，通过首字节的字符来区分不同数据类型，常用的数据类型包括5种：

- 单行字符串：首字节是` + `，后面跟上单行字符串，以`CRLF（ "\r\n" ）`结尾。例如返回`"OK"`： `"+OK\r\n"`，是二进制不安全的，字符串中不能出现`\r\n`

- 错误（Errors）：首字节是 `-` ，与单行字符串格式一样，只是字符串是异常信息，例如：`"-Error message\r\n"`，通常用于Redis服务端返回结果

- 数值：首字节是 `:` ，后面跟上数字格式的字符串，以CRLF结尾。例如：`":10\r\n"`

- 多行字符串：首字节是 `$` ，表示二进制安全的字符串，他会指定字符串的长度，因此根据长度读取即可，不需要读取到`\r\n`截止，最大支持512MB

  - 如果大小为0，则代表空字符串：`"$0\r\n\r\n"`

  - 如果大小为-1，则代表不存在：`"$-1\r\n"`

- 数组：首字节是 `*`，后面跟上数组元素个数，再跟上元素，元素数据类型不限

  - ```sh
    *3\r\n  # 数组元素个数为3
    $3\r\nset\r\n	# 数组第一个元素，多行字符串，长度为3
    $4\r\nname\r\n	# 数组第二个元素，多行字符串，长度为4
    $6\r\n小明\r\n   # 数组第三个元素，多行字符串，因为一个中文字符占3字节，所以长度为0
    ```

  - ```sh
    *3\r\n
    :10\r\n
    $5\r\nhello\r\n
    *2\r\n$3\r\nage\r\n:10\r\n
    ```

    





## 十三、Redis内存回收

### 1、过期Key处理

Redis性能强，主要的原因就是基于内存存储。然而单节点的Redis其内存大小不宜过大，会影响持久化或主从同步性能。

当内存使用达到上限时，就无法存储更多数据了。为了解决这个问题，Redis提供了一些策略实现内存回收：**内存过期策略**



可以通过`expire`命令给Redis的key设置TTL，当key的TTL到期以后，再次访问返回的是nil，说明这个key已经不存在了，对应的内存也得到释放。从而起到内存回收的目的。

Redis本身是一个典型的key-value内存存储数据库，因此所有的key、value都保存在`Dict`结构中。`redisDb`就是Redis中的一个个数据库，一共有16个，每个`redisDb`中

- `*dict`是Dict结构，存放的是所有的key和value
- `*expires`是Dict结构，存放的是每一个key，对应的TTL时间，只有设置了TTL的key，才会在`*expire`中存在

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271543315.png" style="zoom:80%;" />





- `*dict`是Dict结构，`ht[0]`存放的就是所有的key和value，不过这里的key和value都是`redisObject`，entry中存放的是key和value对应的`redisObject`的指针
- `*expires`是Dict结构，他的key存放的也是对应`redisObject`的指针，value存放的是这个key的过期时间，因此Redis就是通过这个过期时间判断key是否过期的

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271541319.png" style="zoom:60%;" />







#### 1.1 惰性删除

惰性删除是指并不是在TTL到期后就立刻删除，而是在访问一个key的时候，检查该key的存活时间，如果已经过期才执行删除。

从下边源码中可以看到，当Redis执行读或写操作时，首先会检查key是否过期，如果过期就先删除key

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271541459.png)





#### 1.2 周期删除

> 周期删除是指通过一个定时任务，周期性的抽样部分过期的key，然后执行删除。
>
> - Redis服务初始化函数`initServer()`中设置定时任务，按照`server.hz`的频率来执行过期key清理，模式为**SLOW**
> - Redis的每个事件循环前会调用`beforeSleep()`函数，执行过期key清理，模式为**FAST**



**slow模式**

1. `initServer`方法中，会创建定时器，关联回调函数`serverCron`，处理周期`server.hz`默认为10

2. `serverCron`方法中，会执行过期key的处理，然后返回`1000/server.hz`，默认是100，表示后边每100ms执行一次清理key的操作

3. `databasesCron`方法中，通过`activeExpireCycle`方法执行清理过期key的操作

**fast模式**

1. 在每次调用`n = aeApiPoll()`时，也就是执行`epoll_wait`操作之前，都会执行`beforeSleep`方法，这个方法内部，也会执行`activeExpireCycle`方法，不过此时是`FAST`模式

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271603037.png)



**SLOW模式：**

* 执行频率受`server.hz`影响，默认为10，即每秒执行10次，每个执行周期100ms。
* 执行清理耗时不超过一次执行周期的25%，默认slow模式耗时不超过25ms
* 清理时，逐个遍历db，逐个遍历db中的bucket【即hash表中每个位置的链表】，抽取20个key判断是否过期
* 如果没达到时间上限（25ms）并且过期key比例大于10%，再进行一次抽样，否则结束



**FAST模式规则**

* 执行频率受`beforeSleep()`调用频率影响，但两次FAST模式间隔不低于2ms
* 执行清理耗时不超过1ms
* 逐个遍历db，逐个遍历db中的bucket，抽取20个key判断是否过期
* 如果没达到时间上限（1ms）并且过期key比例大于10%，再进行一次抽样，否则结束







### 2、内存淘汰策略

就是当Redis内存使用达到设置的上限时，会主动挑选部分key进行删除以释放更多内存。

Redis会在处理客户端命令的方法`processCommand()`中尝试做内存淘汰

`int out_of_memory = (performEvictions() == EVICT_FAIL) `这行代码首先通过`performEvictions()`执行内存淘汰，如果返回`EVICT_FAIL`说明内存淘汰失败，那么`out_of_memory `就为1，后边就拒绝执行这条命令

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271728582.png)





**淘汰策略**

Redis支持8种不同策略来选择要删除的key

* `noeviction`： 不淘汰任何key，但是内存满时不允许写入新数据，默认就是这种策略。
* `volatile-ttl`： 对设置了TTL的key，比较key的剩余TTL值，TTL越小越先被淘汰
* `allkeys-random`：对全体key ，随机进行淘汰。也就是直接从`db->dict`中随机挑选
* `volatile-random`：对设置了TTL的key ，随机进行淘汰。也就是从`db->expires`中随机挑选。
* `allkeys-lru`： 对全体key，基于LRU算法进行淘汰
* `volatile-lru`： 对设置了TTL的key，基于LRU算法进行淘汰
  * LRU（Least Recently Used），最少最近使用。用当前时间减去最后一次访问时间，这个值越大则淘汰优先级越高。

* `allkeys-lfu`： 对全体key，基于LFU算法进行淘汰
* `volatile-lfu`： 对设置了TTL的key，基于LFI算法进行淘汰
  * LFU（Least Frequently Used），最少频率使用。会统计每个key的访问频率，值越小淘汰优先级越高。






`RedisObject`中的`lru`字段，记录该对象最后一次被访问的时间，LFU也是复用的这个字段。

- 当使用LRU时，它保存的是上次访问的时间
- 使用LFU时，高16位以分钟为单位记录最近一次访问时间，低8位记录逻辑访问时间

LFU的访问次数称为逻辑访问次数，是因为并不是每次key被访问都计数，而是通过运算：

* 生成0~1之间的随机数R
* 计算   `1 / (旧次数 * lfu_log_factor + 1) `，记录为P，`lfu_log_factor`默认为10
* 如果 R < P ，则计数器 + 1，且最大不超过255
* 访问次数会随时间衰减，距离上一次访问时间每隔 `lfu_decay_time` 分钟【默认为1】，计数器 -1

```c
typedef struct redisObject {
    unsigned type:4;		// 对象类型
    unsigned encoding:4;	// 编码类型
    unsigned lru:LRU_BITS; /* LRU：以秒为单位记录最近一次访问时间，长度为24bit
                            * LFU：高16位以分钟为单位记录最近一次访问时间，低8位记录逻辑访问时间
                            */
    int refcount;		// 引用计数，计数为0则可以回收
    void *ptr;			// 数据指针，指向真实数据
} robj;
```



**执行流程**

1、执行命令前先判断当前内存是否充足，如果充足就执行命令，然后退出，否则继续判断

2、判断当前策略是否是`noeviction`，即不淘汰任何key，如果是，那么直接退出，会报错，否则继续执行

3、判断当前是否是`AllKeys`模式，是的话就从`db-dict`中淘汰，否则从`db->entries`中淘汰

4、判断内存策略，如果是`RANDOM`，那么就随机选一个key删除，然后继续判断内存是否充足

5、如果是`LRU`、`LFU`、`TTL`的话，准备一个`eviction_poll`，从第一个数据库开始，随机挑选`maxmemory_samples`【默认为5】个key，基于不同的内存策略计算`idleTime`

- `TTL`：`idleTime = maxTTL - TTL`
- `LRU`：`idleTime = now - LRU` 
- `LFU`：`idleTime = 255 - LFU`

6、判断这些key是否可以放入`eviction_poll`

- 如果`eviction_poll`没满，就放进去
- 如果满了，就判断里边`idleTime`最小的是否比当前要放进去的小，是的话，就将当前的key放进去，小的key出来，将`eviction_poll`中元素升序排序，然后继续遍历下一个数据库

7、取出`eviction_poll`中`idleTime`最大的key，将他删除，然后判断内存是否充足，充足的话就结束，否则继续删除

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312271753328.png" style="zoom:80%;" />
