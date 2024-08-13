## 一、Kafka基础

> 1、Kafka软件最初的设计就是专门用于数据传输的消息系统，类似功能的软件有RabbitMQ、ActiveMQ、RocketMQ等。这些软件的核心功能是传输数据，而Java中如果想要实现数据传输功能，那么这个软件一般需要遵循Java消息服务技术规范`JMS（Java Message Service）`。前面提到的ActiveMQ软件就完全遵循了JMS技术规范，而RabbitMQ是遵循了类似`JMS`规范并兼容`JMS`规范的跨平台的`AMQP（Advanced Message Queuing Protocol）`规范
>
> 2、Kafka借鉴了JMS规范的思想，但是却并没有完全遵循JMS规范。这也恰恰是软件名称为Kafka，而不是KafkaMQ的原因



### 1、JMS规范

`JMS`是Java平台的消息中间件通用规范，定义了主要用于消息中间件的标准接口。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404222103901.png)

- `JMS Provider`：JMS消息提供者。其实就是实现JMS接口和规范的消息中间件，也就是我们提供消息服务的软件系统，比如RabbitMQ、ActiveMQ、Kafka。

- `JMS Message`：JMS消息。这里的消息指的就是数据。一般采用Java数据模型进行封装，其中包含消息头，消息属性和消息主体内容。

- `JMS Producer`：JMS消息生产者。所谓的生产者，就是生产数据的客户端应用程序，这些应用通过JMS接口发送JMS消息。

- `JMS Consumer`：JMS消息消费者。所谓的消费者，就是从消息提供者（**JMS** **Provider**）中获取数据的客户端应用程序，这些应用通过JMS接口接收JMS消息。





### 2、消息模型

`JMS`支持两种消息发送和接收模型：一种是P2P（Peer-to-Peer）点对点模型，另外一种是发布/订阅（Publish/Subscribe）模型。

- P2P模型：P2P模型是基于队列的，消息生产者将数据发送到消息队列中，消息消费者从消息队列中接收消息。因为队列的存在，消息的异步传输成为可能。P2P模型的规定就是每一个消息数据，只有一个消费者，当发送者发送消息以后，不管接收者有没有运行都不影响消息发布到队列中。接收者在成功接收消息后会向发送者发送接收成功的消息

- 发布 / 订阅模型：事先将传输的数据进行分类，这个数据的分类称之为主题（Topic）。生产者发送消息时，会根据主题进行发送。比如消息中有一个分类是NBA，那么生产者在生产消息时，就可以将NBA篮球消息数据发送到NBA主题中，这样，对NBA消息主题感兴趣的消费者就可以申请订阅NBA主题，然后从该主题中获取消息。这样，一个消息是允许被多个消费者同时消费的。这里生产者发送消息，称为发布消息，消费者从主题中获取消息，称为订阅消息。**Kafka采用就是这种模型**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202404222116437.png" style="zoom:80%;" />





### 3、kafka结构

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404222117245.png)

- kafka中消息提供者称为`Broker`

- 消息基于内存是不可靠的，因此kafka的消息会保存到磁盘的日志文件中
- 为了保证消息消费的有序性，每条消息都会有一个编号，消费者消费消息时，会根据编号消费







### 4、kafka安装

安装kafka并解压后，其目录如下

| 文件夹      | 作用                        |
| ----------- | --------------------------- |
| bin         | linux系统下可执行脚本文件   |
| bin/windows | windows系统下可执行脚本文件 |
| config      | 配置文件                    |
| libs        | 依赖类库                    |
| licenses    | 许可信息                    |
| site-docs   | 文档                        |
| logs        | 服务日志                    |





#### 4.1 启动zookeeper

Kafka内部依赖`ZooKeeper`进行多节点协调调度，所以启动Kafka软件之前，需要先启动ZooKeeper。Kafka本身内置了ZooKeeper，直接调用脚本命令启动即可

- 修改`config/zookeeper.properties`配置文件

```properties
# 修改dataDir配置，用于设置ZooKeeper数据存储位置，该路径如果不存在会自动创建
dataDir=F:/Java/kafka/local/data/zk
```

- 为了方便启动，编写脚本文件启动`ZooKeeper`

```sh
# 调用启动命令，且同时指定配置文件。
call bin/windows/zookeeper-server-start.bat config/zookeeper.properties
```





#### 4.2 启动kafka

- 进入config目录，修改`server.properties`配置文件

```properties
# 配置Kafka数据的存放位置，如果文件目录不存在，会自动生成。
log.dirs=F:/Java/kafka/local/data/kafka
```

- 为了方便启动，编写脚本文件启动`kafka`

```sh
# 调用启动命令，且同时指定配置文件。
call bin/windows/kafka-server-start.bat config/server.properties
```





### 5、topic

**创建主题**

```
kafka-topics.bat --bootstrap-server localhost:9092 --create --topic test
```

- `bootstrap-serve `: kafka服务器地址，我们在本机启动Kafka服务进程，默认端口为9092

- `create` : 表示对主题的创建操作

- `topic` : 主题的名称



**查询主题**

```
kafka-topics.bat --bootstrap-server localhost:9092 --list
```



**查看指定主题信息**

```
kafka-topics.bat --bootstrap-server localhost:9092 --describe --topic test
```

- `bootstrap-serve `: kafka服务器地址，我们在本机启动Kafka服务进程，默认端口为9092

- `describe` : 查看主题的详细信息

- `topic` : 主题的名称



**修改主题**

```
kafka-topics.bat --bootstrap-server localhost:9092 --topic test --alter --partitions 2
```

- `bootstrap-serve `: kafka服务器地址，我们在本机启动Kafka服务进程，默认端口为9092

- `alter`: 对主题的修改操作

- `topic` : 主题的名称

- `partitions` : 修改的配置参数：分区数量





### 6、Java API

#### 1、添加依赖

```xml
<dependency>
   <groupId>org.apache.kafka</groupId>
   <artifactId>kafka-clients</artifactId>
   <version>3.6.1</version>
</dependency>
```



#### 2、生产者

```java
public class KafkaProducerTest {
    public static void main(String[] args) {
        // 1.配置属性集合
        Map<String, Object> configMap = new HashMap<>();
        // 1.1 Kafka服务器地址
        configMap.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        // 1.2 Kafka生产的数据为KV对，数据通过网络传输，所以在生产数据进行传输前需要分别对K,V进行对应的序列化操作
        configMap.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        configMap.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        // 2.创建Kafka生产者对象，建立Kafka连接，构造对象时，需要传递配置参数
        KafkaProducer<String, String> producer = new KafkaProducer<>(configMap);
        for (int i = 0; i < 10; i++) {
            // 2.1 准备数据,定义泛型 构造对象时需要传递 【Topic主题名称】，【Key】，【Value】三个参数
            ProducerRecord<String, String> record = new ProducerRecord("test", "key" + i, "value" + i);
            // 2.2 发送数据
            producer.send(record);
        }
        // 2.3 关闭生产者连接
        producer.close();
    }
}
```



#### 3、消费者

> 消费者是从kafka拉取数据的，而不是kafka将数据推送给消费者

```java
public class KafkaConsumerTest {
    public static void main(String[] args) {
        // 1.配置属性集合
        Map<String, Object> configMap = new HashMap();
        // 1.1 Kafka服务器地址
        configMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        // 1.2 Kafka传输的数据为KV对，所以需要对获取的数据分别进行反序列化
        configMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        configMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        // 1.3 读取数据的位置 ，取值为earliest（最早），latest（最晚）
        configMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        // 1.4 消费者组
        configMap.put(ConsumerConfig.GROUP_ID_CONFIG, "xrj01");
        // 1.5 自动提交偏移量
        configMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "true");
        KafkaConsumer<String, String> consumer = new KafkaConsumer(configMap);

        // 2.消费者订阅指定主题的数据
        consumer.subscribe(Collections.singletonList("test"));

        // 3.接收数据
        while ( true ) {
            // 3.1 每隔100毫秒，抓取一次数据
            ConsumerRecords<String, String> records =
                    consumer.poll(Duration.ofMillis(100));
            // 3.2 打印抓取的数据
            for (ConsumerRecord<String, String> record : records) {
                System.out.println("K = " + record.key() + ", V = " + record.value());
            }
        }
    }
}
```







## 二、kafka架构

### 1、kafka架构

1、`kafka`中，`broker`上`topic`中的数据都会保存在日志文件中，即我们配置`kafka`时设置的数据存放位置

2、`kafka`中对于一个`broker`中的一个`topic`，可以有多个生产者发送数据，也可以有多个消费者【消费者组】拉取数据，因此只有一个`broker`必然会有性能瓶颈

3、为了解决`kafka`的性能瓶颈，出现了`kafka`集群，即部署多个`broker`，每个`broker`上都有同一个`topic`，即对`topic`进行分区，分为多个`partition`。这样，生产者可以向任意一个`broker`的`topic`发送数据，消费者可以从任何一个`broker`的`topic`拉取数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232103998.png)

4、如果某一个`broker`挂掉了，那么即使数据存到了log文件中，那么也无法消费。因此可以将数据文件进行备份，`Kafka`中称之为副本，多个副本同时只能有一个提供数据的读写操作，其他副本只是用于备份。具有读写能力的副本称之为Leader副本，作为备份的副本称之为Follower副本。这样即使一个`broker`挂了，那么他的log文件可以被其他`broker`使用



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232101537.png)

5、`kafka`使用的是主从架构，多个`broker`中存在一个master节点，称为`Controller`控制器，在`Zookeeper`的帮助下管理和协调控制整个`Kafka`集群。当`Controller`节点挂了之后，会通过`zookeeper`选举其他的节点作为新的`Controller`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232142825.png)





6、kafka基础组件如下

- 多个`broker`中有一个为`Controller`控制器
- 每个`broker`中都有一个`SocketServer`，作为`kafka`服务端连接使用，同时也都有一个`NetworkClient`网络客户端用来连接其他`broker`
- 每个`broker`都通过一个`zookeeper`客户端连接`zookeeper`服务器
- 每个`broker`都有一个`LogManager`用来管理日志文件
- 每个`broker`都有一个`replication Manager`，用来管理日志副本

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232144903.png)



7、启动一个`zookeeper`，一个`zookeeper`下可以有多个`broker`，创建`topic`时可以指定多个分区，假设有3个`broker`，`topic`指定了3个分区，那么每个`broker`上都有这个`topic`的分区，假设每个`topic`有3个副本，那么其中一个为leader副本，另外两个为follow副本







### 2、Zookeepeer

- 在`ZooKeeper`中可以创建节点`Node`，创建一个`Node`时，我们会设定这个节点是持久化创建，还是临时创建。持久化创建，就是`Node`一旦创建后会一直存在，而临时创建，是根据当前的客户端连接创建的临时节点`Node`，一旦客户端连接断开，那么这个临时节点`Node`也会被自动删除，这样的节点称之为临时节点。
- `ZooKeeper`节点是不允许有重复的，所以多个客户端创建同一个节点，只能有一个创建成功。
- 客户端可以在`ZooKeeper`的节点上增加监听器，用于监听节点的状态变化，一旦监听的节点状态发生变化，那么监听器就会触发响应，实现特定监听功能。
- 当`kafka`启动后，就会在`zookeeper`上创建对应的节点，会有一个Controller节点，其他都是从节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232235873.png)





**Zookeeper实现Controller节点选取**

- 第一次启动`Kafka`集群时，会同时启动多个`Broker`节点，每一个`Broker`节点都会连接`ZooKeeper`，并尝试创建一个临时节点 **/controller**

- 因为`ZooKeeper`中一个节点不允许重复创建，所以多个`Broker`节点，最终只能有一个`Broker`节点可以创建成功，那么这个创建成功的`Broker`节点就会自动作为`Kafka`集群`Controller`节点，用于管理整个`Kafka`集群。

- 没有选举成功的其他`Slave`节点会创建`Node`监听器，用于监听 **/controller**节点的状态变化。

- 一旦`Controller`节点出现故障或挂掉了，那么对应的`ZooKeeper`客户端连接就会中断。`ZooKeeper`中的 **/controller** 节点就会自动被删除，而其他的那些`Slave`节点因为增加了监听器，所以当监听到 **/controller** 节点被删除后，就会马上向`ZooKeeper`发出创建 **/controller** 节点的请求，一旦创建成功，那么该`Broker`就变成了新的`Controller`节点了。







### 3、Controller和Broker通信

> 1、`Broker`启动时，会通过`Zookeepeer`客户端向`Zookeepeer`注册当前的`Broker` 节点ID，注册创建的`Zookeepeer`节点为临时节点。如果当前Broker的`Zookeepeer`客户端断开和`Zookeepeer`的连接，注册的节点会被删除。
>
> 2、每一个`Broker`在启动时会创建自己的网络通信对象（**SocketServer**），用于和其他`Broker`之间进行通信，其中包含了Java用于NIO通信的Channel、Selector对象



**启动第一个Broker**

- 注册`Broker`节点，注册到`/broker/ids`下
- 监听`controller`节点【当前还不存在】
- 注册`controller`节点，因为现在不存在`controller`节点，所以创建成功
- 选举成为`controller`，同时监听`broker/ids`节点，监听`broker`的新增或减少

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232310348.png)



**启动第二个broker**

- 注册`Broker`节点，注册到`/broker/ids`下
- 监听`controller`节点【当前存在】
- 注册`controller`节点，因为现在存在`controller`节点，所以创建失败
- 由于`controller`节点监听了`/broker/ids`节点，所以新增`broker`节点时，会被`controller`监听到
- `controller`连接新增的`broker`，发送集群相关数据【`controller`信息、集群中其他节点信息等】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232310131.png)



**启动第三个broker**

- 注册`Broker`节点，注册到`/broker/ids`下
- 监听`controller`节点【当前存在】
- 注册`controller`节点，因为现在存在`controller`节点，所以创建失败
- 由于`controller`节点监听了`/broker/ids`节点，所以新增`broker`节点时，会被`controller`监听到
- `controller`连接所有的`broker`，发送集群相关数据【`controller`信息、集群中其他节点信息等】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232310678.png)



**Controller节点删除**

- 因为所有的`Broker`节点都监听了`Controller`，所以当`Controller`节点删除后，所有的`Broker`都会收到通知
- 此时所有的`Broker`都会去注册`Controller`节点，但只有一个`Broker`注册成功
- 完成`Controller`的选举后，`Controller`节点会去监听`broker/ids`节点
- `Controller`节点连接所有的`Broker`，发送集群相关数据【`Controller`信息，集群、分区信息等】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404232334324.png)







## 三、kafka原理

### 1、Topic

> 1、`Kafka`是分布式消息传输系统，采用的数据传输方式为**发布，订阅**模式，也就是说由消息的生产者发布消息，消费者订阅消息后获取数据。为了对消费者订阅的消息进行区分，所以对消息在逻辑上进行了分类，这个分类称为主题`Topic`。消息的生产者必须将消息数据发送到某一个主题，而消费者必须从某一个主题中获取消息，并且消费者可以同时消费一个或多个主题的数据。Kafka集群中可以存放多个主题的消息数据。
>
> 2、分区`Partition`：一个主题从物理上分成几块，然后将不同的数据块均匀地分配到不同的`broker`节点上，这样就可以缓解单节点的负载问题。这个主题的分块称为分区`partition`，默认情况下，`topic`主题创建时分区数量为1，也就是一块分区
>
> 3、副本`Replication`：
>
> - 一个`topic`划分了多个分区`partition`，这些分区就会均匀地分布在不同的`broker`节点上，一旦某一个`broker`节点出现了问题，那么在这个节点上的分区就会出现问题，那么`Topic`的数据就不完整了。所以为了防止出现数据丢失的情况，我们会给分区数据设定多个备份，这里的备份，称为副本`Replication`
>
> - `Kafka`中，所有的文件都称之为副本，会选择其中的一个文件作为主文件，称为`Leader`副本，其他的文件作为备份文件，称为`Follower`副本。在`Kafka`中，这里的文件就是分区，每一个分区都可以存在1个或多个副本，只有`Leader`副本才能进行数据的读写，`Follower`副本只做备份使用。
>
> 4、日志：`Kafka`接收到的消息数据最终都是存储在log日志文件中的，底层存储数据的文件的扩展名就是log。
>
> 5、在`kafka`的`server.properties`文件中配置参数`auto.create.topics.enable=true`时，如果访问的主题不存在，那么`kafka`就会自动创建主题，但是分区、副本数都是默认值1



**Java代码**

> 在`kafka`中通过`Admin`创建`topic`时，会先指定一个`broker`的地址，这个`broker`不一定是`Controller`，因此会先通过这个`broker`获取到`kafka`的集群信息，得到`Controller`信息后，会向`Controller`发起创建主题的网络请求，然后通过`KafkaApis`向`Zookeepeer`发起请求，创建`topic`。然后校验主题参数，例如分区数、副本数等，根据指定策略计算每个分区副本的位置，然后在指定的`broker`上创建副本

```java
public class KafkaAdminTest {
    public static void main(String[] args) {
        // 1.配置对象
        Map<String, Object> configMap = new HashMap<>();
        configMap.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");

        // 2.创建管理者对象
        final Admin admin = Admin.create(configMap);

        // 3.创建主题
        // 3.1 主题名
        String topicName = "test0";
        // 3.2 分区数
        int partitionCount = 1;
        // 3.3 副本数量
        short replicationCount = 1;
        // 3.4 定义topic
        NewTopic topic0 = new NewTopic(topicName, partitionCount, replicationCount);

        String topicName1 = "test1";
        int partitionCount1 = 2;
        short replicationCount1 = 2;
        NewTopic topic1 = new NewTopic(topicName1, partitionCount1, replicationCount1);

        // 3.5 创建topic
        final CreateTopicsResult topics = admin.createTopics(
                Arrays.asList(
                        topic0, topic1
                )
        );
        // 4.关闭管理者对象
        admin.close();
    }
}
```







### 2、Topic分区副本分配策略

**假设Topic分区的副本按以下方式分配**

- 第一个`Topic`，分区数为1，副本数为2，leader副本在broker1上，follow副本在broker2上
- 第二个`Topic`，分区数为2，副本数为2，leader副本在broker1和broker2上，follow副本在broker1和broker2上
- 第三个`Topic`，分区数为3，副本数为3，leader副本在broker1、broker2和broker3上，follow副本在broker1、broker2和broker3上

可以看到broker1上，有3个leader副本，broker2上有2个leader副本，broker3上有1个leader副本，因为只有leader副本才会进行读写，follow副本只是起到备份作用，所以broker1的读写压力就可能比较大

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404242227593.png)



**假设Topic分区的副本按以下方式分配**

- 第一个`Topic`，分区数为1，副本数为2，leader副本在broker1上，follow副本在broker2上
- 第二个`Topic`，分区数为2，副本数为2，leader副本在broker2和broker3上，follow副本在broker2和broker3上
- 第三个`Topic`，分区数为3，副本数为3，leader副本在broker1、broker2和broker3上，follow副本在broker1、broker2和broker3上

这样分配之后，每个broker的leader副本数都为2，比较均衡，而kafka中也是通过一定的算法对副本进行分配的，以达到尽可能的均衡

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404242231993.png)



**kafka副本分配策略**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404242234724.png)







### 3、生产者发送数据

> `Topic`主题创建好后，我们就可以向该主题生产消息，Kafka发送数据前，需要将待发送的数据封装为指定的数据模型`ProducerRecord`
>
> 其中`topic`和`value`的值是必须要传递的。如果配置中开启了自动创建主题，那么Topic主题可以不存在。value就是我们需要真正传递的数据了，而`Key`可以用于数据的分区定位。

```java
public class ProducerRecord<K, V> {
    private final String topic;
    private final Integer partition;
    private final Headers headers;
    private final K key;
    private final V value;
    private final Long timestamp;
}
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404242351298.png)





#### 3.1 拦截器

生产者将数据封装为`ProducerRecord`对象后调用`send`方法后，首先会经过拦截器的处理，每个拦截器调用`onSend`方法对当前的数据进行处理。在发送数据之前，我们可以设置自定义的拦截器对数据进行处理

```java
public Future<RecordMetadata> send(ProducerRecord<K, V> record, Callback callback) {
    ProducerRecord<K, V> interceptedRecord = this.interceptors.onSend(record);
    return this.doSend(interceptedRecord, callback);
}
```

```java
public ProducerRecord<K, V> onSend(ProducerRecord<K, V> record) {
    ProducerRecord<K, V> interceptRecord = record;
    Iterator var3 = this.interceptors.iterator();

    while(var3.hasNext()) {
        ProducerInterceptor interceptor = (ProducerInterceptor)var3.next();

        try {
            interceptRecord = interceptor.onSend(interceptRecord);
        } catch (Exception var6) {
            if (record != null) {
                log.warn("Error executing interceptor onSend callback for topic: {}, partition: {}", new Object[]{record.topic(), record.partition(), var6});
            } else {
                log.warn("Error executing interceptor onSend callback", var6);
            }
        }
    }
    return interceptRecord;
}
```

```java
public interface ProducerInterceptor<K, V> extends Configurable, AutoCloseable {
    // onSend方法对ProducerRecord进行处理
    ProducerRecord<K, V> onSend(ProducerRecord<K, V> var1);

    void onAcknowledgement(RecordMetadata var1, Exception var2);

    void close();
}
```





#### 3.2 序列化

经过拦截器处理后，会继续执行`doSend`方法，这个方法会通过我们设置的`keySerializer`和`valueSerializer`对`key`和`value`进行序列化，虽然参数位置有`topic`、`headers`、`key`、`value`，但其只对`key`和`value`做序列化处理

```JAVA
private Future<RecordMetadata> doSend(ProducerRecord<K, V> record, Callback callback) {
    KafkaProducer.AppendCallbacks appendCallbacks = new KafkaProducer.AppendCallbacks(callback, this.interceptors, record);

    try {

        ...

        try {
            serializedKey = this.keySerializer.serialize(record.topic(), record.headers(), record.key());
        } catch (ClassCastException var21) {
            throw new SerializationException("Can't convert key of class " + record.key().getClass().getName() + " to class " + this.producerConfig.getClass("key.serializer").getName() + " specified in key.serializer", var21);
        }

        byte[] serializedValue;
        try {
            serializedValue = this.valueSerializer.serialize(record.topic(), record.headers(), record.value());
        } catch (ClassCastException var20) {
            throw new SerializationException("Can't convert value of class " + record.value().getClass().getName() + " to class " + this.producerConfig.getClass("value.serializer").getName() + " specified in value.serializer", var20);
        }

       ...
}
```





#### 3.3 分区器

> `Kafka`中`Topic`是对数据逻辑上的分类，而`Partition`才是数据真正存储的物理位置。所以在生产数据时，如果只是指定`Topic`的名称，其实`Kafka`是不知道将数据发送到哪一个`Broker`节点的。我们可以在构建数据传递`Topic`参数的同时，也可以指定数据存储的分区编号

如果不指定分区，`Kafka`会根据集群元数据中的主题分区来通过算法来计算分区编号

- 如果指定了分区，直接使用

- 如果指定了自己的分区器，通过分区器计算分区编号，如果有效，直接使用

- 如果指定了数据Key，且使用Key选择分区的场合，采用`murmur2`非加密散列算法计算数据`Key`序列化后的值的散列值，然后对主题分区数量模运算取余，最后的结果就是分区编号

- 如果未指定数据Key，或不使用Key选择分区，那么Kafka会采用优化后的**粘性分区策略**进行分区选择







#### 3.4 数据收集器

> 用于收集，转换我们产生的数据，类似于生产者消费者模式下的缓冲区。为了优化数据的传输，Kafka并不是生产一条数据就向Broker发送一条数据，而是通过合并单条消息，进行批量（批次）发送，提高吞吐量，减少带宽消耗

- 默认情况下，一个发送批次的数据容量为16K，这个可以通过参数`batch.size`进行修改

- 批次是和分区进行绑定的。也就是说发往同一个分区的数据会进行合并，形成一个批次。

- 如果当前批次能容纳数据，那么直接将数据追加到批次中即可，如果不能容纳数据，那么会产生新的批次放入到当前分区的批次队列中，这个队列使用的是Java的双端队列`Deque`。旧的批次关闭不再接收新的数据，等待发送
- 如果数据在缓冲池停留的时间超过阈值，也会发送





#### 3.5 数据发送器Sender

> 线程对象，用于从收集器对象中获取数据，向服务节点发送。类似于生产者消费者模式下的消费者。因为是线程对象，所以启动后会不断轮询获取数据收集器中已经关闭的批次数据。对批次进行整合后再发送到Broker节点中

- 因为数据真正发送的地方是`Broker`节点，不是分区。所以需要将从数据收集器中收集到的批次数据按照可用`Broker`节点重新组合成List集合。

- 将组合后的`<节点，List<批次>>`的数据封装成客户端请求（请求键为：Produce）发送到网络客户端对象的缓冲区，由网络客户端对象通过网络发送给Broker节点。

- Broker节点获取客户端请求，并根据请求键进行后续的数据处理：向分区中增加数据。





#### 3.6 回调方法

> Kafka发送数据时，可以同时传递回调对象`Callback`用于对数据的发送结果进行对应处理，具体代码实现采用匿名类或Lambda表达式都可以。

```java
public class KafkaProducerASynTest {
    public static void main(String[] args) {
        Map<String, Object> configMap = new HashMap<>();
        configMap.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configMap.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        configMap.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        KafkaProducer<String, String> producer = new KafkaProducer<>(configMap);

        for ( int i = 0; i < 1; i++ ) {
            ProducerRecord<String, String> record = new ProducerRecord<>("test", "key" + i, "value" + i);
            producer.send(record, new Callback() {
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    System.out.println("数据发送成功：" + recordMetadata.timestamp());
                }
            });
        }
        producer.close();
    }
}
```





#### 3.7 异步发送

> Kafka发送数据时，底层的实现类似于生产者消费者模式。底层会由主线程代码作为生产者向缓冲区中放数据，而数据发送线程会从缓冲区中获取数据进行发送。`Broker`接收到数据后进行后续处理。
>
> 如果Kafka通过主线程代码将一条数据放入到缓冲区后，无需等待数据的后续发送过程，就直接发送一下条数据的场合，我们就称之为异步发送。

```java
public class KafkaProducerASynTest {
    public static void main(String[] args) {
        Map<String, Object> configMap = new HashMap<>();
        configMap.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configMap.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        configMap.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        KafkaProducer<String, String> producer = new KafkaProducer<>(configMap);

        for ( int i = 0; i < 10; i++ ) {
            ProducerRecord<String, String> record = new ProducerRecord<>("test", "key" + i, "value" + i);
            producer.send(record, new Callback() {
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    System.out.println("数据发送成功：" + recordMetadata.timestamp());
                }
            });
            System.out.println("发送数据");
        }
        producer.close();
    }
}
```



**执行结果**

> 可以看到，数据发送后的回调函数是在最后执行的，因为每发送一条数据，这条数据都会在缓冲区等待Sender线程进行发送，而发送线程无需等待回调结果就可以发送下一条数据

```
发送数据
发送数据
发送数据
发送数据
发送数据
发送数据
发送数据
发送数据
发送数据
发送数据
数据发送成功：1714052675501
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675513
数据发送成功：1714052675514

Process finished with exit code 0
```





#### 3.8 同步发送

> 如果Kafka通过主线程代码将一条数据放入到缓冲区后，需等待数据的后续发送操作的应答状态，才能发送一下条数据的场合，我们就称之为同步发送。所以这里的所谓同步，就是生产数据的线程需要等待发送线程的应答结果，kafka的同步发送采用`Future`接口的`get`方法实现

```java
public class KafkaProducerASynTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Map<String, Object> configMap = new HashMap<>();
        configMap.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configMap.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        configMap.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        KafkaProducer<String, String> producer = new KafkaProducer<>(configMap);

        for ( int i = 0; i < 10; i++ ) {
            ProducerRecord<String, String> record = new ProducerRecord<>("test", "key" + i, "value" + i);
            Future<RecordMetadata> send = producer.send(record, new Callback() {
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    System.out.println("数据发送成功：" + recordMetadata.timestamp());
                }
            });
            System.out.println("发送数据");
            send.get();
        }
        producer.close();
    }
}
```



**执行结果**

> 可以发现，同步发送时，当数据发送成功后，才会发送下一条，因为这里使用的`Future`的`get`方法，等待有结果后才会继续执行

```
发送数据
数据发送成功：1714053000454
发送数据
数据发送成功：1714053000503
发送数据
数据发送成功：1714053000507
发送数据
数据发送成功：1714053000512
发送数据
数据发送成功：1714053000517
发送数据
数据发送成功：1714053000523
发送数据
数据发送成功：1714053000528
发送数据
数据发送成功：1714053000535
发送数据
数据发送成功：1714053000540
发送数据
数据发送成功：1714053000548

Process finished with exit code 0
```





#### 3.9 消息可靠性

> 生产者发送的数据，消息可能会因为某些故障或问题导致丢失。但是大多数场景中，我们需要保证数据发送成功且Kafka正确接收，也就是要保证数据不丢失，即保证消息可靠性。
>
> 消息可靠性一般是通过Kafka给我们返回的响应结果【ACK应答】来保证的，Kafka提供了3种应答处理，可以通过配置对象进行配置`configMap.put(ProducerConfig.ACKS_CONFIG, "-1");`

- **ACK = 0**

  > 1、生产者发送数据时，将数据通过网络客户端发送到网络数据流中的时候，Kafka就对当前的数据请求进行了响应（确认应答），如果是同步发送数据，此时就可以发送下一条数据了。如果是异步发送数据，回调方法就会被触发。
  >
  > 2、这种应答方式，数据已经走网络给Kafka发送了，但这其实并不能保证Kafka能正确地接收到数据，在传输过程中如果网络出现了问题，那么数据就丢失了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404252231912.png)

- **ACK = 1**

  > 1、生产者发送数据时，`Kafka`的`Leader`副本收到数据并写入到了日志文件后，就会对当前的数据请求进行响应（确认应答），如果是同步发送数据，此时就可以发送下一条数据了。如果是异步发送数据，回调方法就会被触发。
  >
  > 2、这种应答方式，数据已经存储到了分区Leader副本中，数据相对来讲比较安全。但是现在只有一个节点存储了数据，而数据并没有来得及进行备份到follower副本，那么一旦当前存储数据的broker节点出现了故障，数据也依然会丢失。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404252233942.png)

- **ACK = -1（默认）**

  > 1、生产者发送数据时，`Kafka`的`Leader`副本和`Follower`副本都收到数据并写入到日志文件后，再对当前的数据请求进行响应（确认应答）
  >
  > 2、这种应答方式，数据已经同时存储到了分区`Leader`副本和`follower`副本中，可靠性是最高的。此时，如果Leader副本出现了故障，那么follower副本能够开始起作用，数据不会丢失。
  >
  > 3、并不是所有的`Follower`副本都收到数据才进行响应。有一个同步副本列表`In Syn Replica`，称为ISR。Kafka只要保证ISR中所有的副本接收到了数据，就可以对数据请求进行响应了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404252236381.png)







#### 3.10 消息幂等/有序

**消息重复**

当消息丢失时，`Producer`会重新将数据放入到数据收集器中进行发送

- 由于网络或服务节点的故障，Kafka在传输数据时，可能会导致数据丢失，所以设置ACK应答机制，尽可能提高数据的可靠性。

- 但其实在某些场景中，数据的丢失并不是真正地丢失，而是“虚假丢失”，比如ACK应答设置为1，一旦`Leader`副本将数据写入文件后，`Kafka`就可以对请求进行响应。此时，如果网络故障，`Kafka`并没有成功将`ACK`应答信息发送给`Producer`，那么`Producer`会一直等待，直到超过时间阈值，`Producer`会尝试对超时的数据进行重试操作，将数据再次发送给`Kafka`，如果此时发送成功，那么`Kafka`就又收到了数据，导致数据重复。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404252315489.png)



**消息乱序**

数据重试除了可能会导致数据重复以外，还可能会导致数据乱序

- 假设我们需要将编号为1，2，3的三条连续数据发送给`Kafka`，每条数据会对应于一个连接请求。此时，如果第一个数据的请求出现了故障，而第二个数据和第三个数据的请求正常，那么`Broker`就收到了第二个数据和第三个数据并将其写入日志文件，然后进行应答。为了保证数据的可靠性，此时，`Kafka`的`Producer`会将第一条数据重新放回到缓冲区进行重试操作，如果重试成功，`Broker`收到第一条数据，然后将第一条数据写入日志文件，此时数据的顺序为2、3、1，数据顺序被打乱。





**数据幂等性**

> 1、为了解决Kafka传输数据时，所产生的数据重复和乱序问题，Kafka引入了幂等性操作，即同样的一条数据，无论向Kafka发送多少次，kafka都只会存储一条
>
> 2、幂等性默认是不起作用的，需要在生产者对象的配置中开启幂等性配置
>
> 3、kafka幂等性要求ack为-1，同时开启重试机制



- 开启幂等性后，为了保证数据不会重复，需要给每一个请求批次的数据增加唯一性标识，kafka中，每条消息的标识由**生产者ID**和**消息的序列号seqnum**组成
- `Broker`中会给每一个分区记录生产者的生产状态：采用队列的方式缓存最近的5个批次数据【5是固定值，无法修改】。队列中的数据按照seqnum进行升序排列
- 如果Borker当前新的请求批次数据在缓存的5个旧的批次中存在相同的，说明有重复，当前批次数据不做任何处理
- 如果Broker当前的请求批次数据在缓存中没有相同的，那么判断当前新的请求批次的序列号是否为缓存的最后一个批次的序列号加1，如果是，说明是连续的，顺序没乱。否则，将乱序的数据通过`Producer`重新发送
- 如果请求批次不重复，且有序，那么更新缓冲区中的批次数据。将当前的批次放置再队列的结尾，将队列的第一个移除，保证队列中缓冲的数据最多5个

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404252330338.png)





**注意**

- kafka的幂等性只能保证单个分区上的幂等性，即单个分区的消息有序不重复，多分区无法保证幂等性。

- 只能保持生产者单个会话的幂等性，无法实现跨会话的幂等性。如果一个`producer`挂掉再重启，那么重启前和重启后的`producer`对象会被当成两个独立的生产者，从而获取两个不同的独立的生产者ID，导致broker端无法获取之前的状态信息，所以无法实现跨会话的幂等。【可以通过生产者的事务机制解决，开始事务后，生产者多次发送消息时，其ID不变】







### 4、kafka存储数据

1、生产者Producer将数据发送给Kafka集群，当Kafka接收到数据后，会将数据写入本地文件中

2、当我们把数据写入文件系统之后，其实数据在操作系统的`PageCache`（页缓冲）里面，并没有刷到磁盘上。如果操作系统挂了，数据就丢失了。应用程序可以调用`fsync`系统调用来强制刷盘。同时，操作系统有后台线程定时刷盘。

- `log.flush.interval.messages` ：达到消息数量时，会将数据`flush`到日志文件中。

- `log.flush.interval.ms `：间隔多少时间(ms)，执行一次强制的`flush`操作。

- `flush.scheduler.interval.ms`：所有日志刷新到磁盘的频率

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404262354272.png)

3、Kafka是用来传输数据的，而不是保存数据。因此当数据保存到kafka日志文件后，默认的数据保存时间为7天，一旦超过时间，数据就会被删除。也可以配置日志文件达到多大时进行删除。







#### 4.1 副本同步

Kafka中，分区的某个副本会被指定为` Leader`，负责响应客户端的读写请求。分区中的其他副本自动成为 `Follower`，主动拉取（同步）`Leader` 副本中的数据，写入自己本地日志，确保所有副本上的数据是一致的。`Follower`节点会启动数据同步线程`ReplicaFetcherThread`，从`Leader`副本节点同步数据。

线程运行后，会不断重复两个操作：**截断**（truncate）和**抓取**（fetch）。

- 截断：为了保证分区副本的数据一致性，当分区存在`Leader Epoch`值时，会将副本的本地日志截断到`Leader Epoch`对应的最新位移处，如果分区不存在对应的 Leader Epoch 记录，那么依然使用原来的高水位机制，将日志调整到高水位值处。

- 抓取：向Leader同步最新的数据。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271217470.png)

- **LEO**：日志末端位移（Log End Offset），表示下一条待写入消息的offset，每个分区副本都会记录自己的LEO。对于Follower副本而言，它能读取到Leader副本 LEO 值以下的所有消息。
- **HW**：高水位值（High Watermark），定义了消息可见性，标识了一个特定的消息偏移量（offset），消费者只能拉取到这个水位offset之前的消息，同时这个偏移量还可以帮助Kafka完成副本数据同步操作。





#### 4.2 数据一致性

1、kafka一般是分布式的，多台机器可以同时提供读写，并且需要为数据的存储做冗余备份。一旦Leader副本挂了，Follower副本可以选举成为新的Leader副本， 这样就提升了分区可用性。

2、假设一个分区有3个副本，一个Leader和两个Follower。Leader副本作为数据的读写副本，所以生产者的数据都会发送给leader副本，而两个follower副本会周期性地同步leader副本的数据，但是因为网络，资源等因素的制约，同步数据的过程是有一定延迟的，所以3个副本之间的数据可能是不同的。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271234435.png" style="zoom:80%;" />



3、假设leader副本挂了，那么Kafka为了提高分区可用性，此时会选择2个follower副本中的一个作为Leader对外提供数据服务。此时如果是follow-1作为新的Leader，那么对于消费者而言，之前leader副本能访问的数据是D，但是重新选择leader副本后，能访问的数据就变成了C，这样消费者会认为数据丢失了，也就是数据不一致。



4、为了提升数据的一致性，Kafka引入了**高水位（HW ：High Watermark）机制**，Kafka在不同的副本之间维护了一个水位线，消费者只能读取到水位线以下的的数据。消费者一开始在消费Leader的时候，虽然Leader副本中已经有a、b、c、d 共4条数据，但是由于高水位线的限制，所以也只能消费到a、b这两条数据。即使leader挂掉了，但是对于消费者来讲，消费到的数据其实还是一样的，因为它能看到的数据是一样的，也就是说，消费者不会认为数据不一致

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271237537.png" style="zoom:80%;" />

5、Follower会不断重复Fetch数据的过程，最终，follower副本和Leader副本的数据和偏移量保持一致。





#### 4.3 ISR列表变化

1、`Kafka`的分区副本中只有`Leader`副本具有数据写入的功能，而`Follower`副本需要不断向`Leader`发出申请，进行数据的同步。这里所有同步的副本会形成一个列表，称为同步副本列表（In-Sync Replicas），简称ISR。除了ISR以外，还有已分配的副本列表（Assigned Replicas），简称AR。AR不仅仅包含ISR，还包含了没有同步的副本列表（Out-of-Sync Replicas），简称OSR

2、生产者`Producer`生产数据时，ACKS应答机制如果设置为all（-1），那此时就需要保证同步副本列表ISR中的所有副本全部接收完毕后，Kafka才会进行确认应答。

3、假设一个topic分区的副本有4个，那么刚开始创建topic时，会通过一定的算法，计算这四个副本分别在哪个broker上，假设计算结果为`[3，2，4，1]`，这个就是ISR列表，那么3号节点的副本即为leader，其余三个为follow。假设此时leader节点即3号节点挂了，那么ISR列表变为`[2，4,1]`，2号副本变为leader，因为kafka通过水位线保证了集群节点的一致性，所以直接使用2号作为leader即可，此时如果3号节点恢复，那么ISR变为`[2，4,1,3]`

4、如果一个follow副本长时间不向leader副本住抓取数据，那么这个follow副本就会被移出ISR，默认30s

5、follow在同步数据时，会判断副本状态，如果满足扩大ISR条件，就会将其加入ISR列表







### 5、消费者拉取数据

1、消费者拉取数据时，需要通过偏移量定位kafka日志中的数据，然后进行拉取。默认情况下，消费者拉取数据时偏移量是`LEO`的值，即下一条待写入消息的offset，因此如果当前kafka中有消息，然后启动消费者去拉取数据，是拉取不到的。

2、kafka消费时，需要指定消费者组，如果一个消费者组进行消费之后，他的offset会记录在kafka中，第二次使用这个消费者消费时，即使重新配置了偏移量，也是不生效的。这是为了保证在消费者出现故障重启时可以从上次的offset继续消费

```java
// 默认值，从offset为LEO开始消费
configMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "latest");
// 从kafka中最小的offset开始消费
configMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
```

3、消费者的offset会自动保存在kafka中，但是默认情况下，是每隔5秒保存一次，假设消费者消费3s后宕机，此时偏移量还没有提交，因此重启后还是从3秒前的offset开始消费。我们可以将消费者offset的自动提交关闭，变为手动提交

```java
public class KafkaConsumerTest {
    public static void main(String[] args) {
        // 1.配置属性集合
        Map<String, Object> configMap = new HashMap();
        // 1.1 配置属性：Kafka集群地址
        configMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        // 1.2 Kafka传输的数据为KV对，所以需要对获取的数据分别进行反序列化
        configMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        configMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        // 1.3 读取数据的位置 ，取值为earliest（最早），latest（最晚）
        configMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        // 1.4 消费者组
        configMap.put(ConsumerConfig.GROUP_ID_CONFIG, "xrj04");
        // 1.5 关闭自动提交偏移量
        configMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
        KafkaConsumer<String, String> consumer = new KafkaConsumer(configMap);

        // 2.消费者订阅指定主题的数据
        consumer.subscribe(Collections.singletonList("test"));

        // 3.接收数据
        while ( true ) {
            // 3.1 每隔100毫秒，抓取一次数据
            ConsumerRecords<String, String> records =
                    consumer.poll(Duration.ofMillis(100));
            // 3.2 打印抓取的数据
            for (ConsumerRecord<String, String> record : records) {
                System.out.println("K = " + record.key() + ", V = " + record.value());
            }
            // 手动提交偏移量
            consumer.commitAsync(); // 异步提交，不需要等待提交完成，可以直接拉取后续数据
            // consumer.commitSync();  // 同步提交，提交完成后才能拉取下一条数据
        }
    }
}
```

4、偏移量自动提交可能导致消息重复消费。偏移量手动提交可能导致消息漏消费，当偏移量手动提交后，数据还未处理，消费者挂掉，那么重启后，是从最新偏移量出开始消费消息，前边的消费就漏掉了。【此时需要与第三方软件配合，将消费数据与偏移量提交作为一个原子操作】





#### 5.1 消费者组

消费者消费kafka中的数据是以消费者组的形式进行的，如果消费者组只有一个消费者，那这一个消费者需要拉取所有分区的数据，当数据量很大时，那么消费的时间就会很长。对于kafka来讲，数据就需要长时间的进行存储，那么对Kafka集群资源的压力就非常大

因此可以使用多个消费者来消费数据，多个消费者当成一个整体，即消费者组。

- 一个消费者可以消费多个分区，但是一个分区只能被消费者组中的一个消费者消费
- 如果消费者组中消费者的数量大于topic的分区数，多出来的消费者不会消费数据，但是当某个消费者挂掉时，多出来的消费者就可以顶替上去
- kafka采用的是发布订阅模型，消费者组是根据偏移量offset来消费数据的，数据是保存在kafka的日志文件中，消费完后并不会删除
- kafka中有一个内部主题`consumer_offset`，默认有50个分区，保存了消费者组消费的偏移量，因此当某个消费者挂了之后，其他的消费者就可以获取其偏移量，从对应位置继续消费
- 一个消费者组可以订阅多个Topic

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271815127.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271819503.png)





#### 5.2 消费者分配策略

> 1、消费者想要拉取数据，必须要加入到一个组中，成为消费组中的一员。如果消费者出现了问题，也应该从消费者组中剥离。加入组和退出组都是由消费者组**调度器**（Group Coordinator）处理。`Group Coordinator`是Broker上的一个组件，用于管理和调度消费者组的成员、状态、分区分配、偏移量等信息。每个Broker都有一个`Group Coordinator`对象，负责管理多个消费者组，但每个消费者组只有一个`Group Coordinator`
>
> 2、消费者组中的每个消费者消费topic的哪一个分区，这个分配策略是由消费者的`Leader`决定的，这个Leader称为群主。群主是多个消费者中，第一个加入组中的消费者，其他消费者我们称之为Follower
>
> 3、当消费者加入群组的时候，会发送一个`JoinGroup`请求。群主负责给每一个消费者分配分区。每个消费者只知道自己的分配信息，只有群主知道群组内所有消费者的分配信息。



**分区分配策略**

- `RoundRobinAssignor`（轮询分配策略）

消费者组中的每个消费者都会含有一个自动产生的UUID作为`memberid`。轮询策略中会将每个消费者按照`memberid`进行排序，所有member消费的主题分区根据主题名称进行排序。将主题分区轮询分配给对应的消费者，未订阅当前主题的消费者会跳过。

**缺点：**分配的不够均衡

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271905919.png)



- `RangeAssignor`（范围分配策略）

按照每个`topic`的`partition`数计算出每个消费者应该分配的分区数量，然后分配，分配的原则就是一个主题的分区尽可能的平均分，如果不能平均分，按顺序向前补齐即可

**缺点**

1、针对单个`Topic`情况下显得比较均衡，但是`Topic`多的话，member排序靠前的可能会比member排序靠后的负载多很多

2、如果新增或移除消费者成员，那么会导致每个消费者都需要去建立新的分区节点的连接，更新本地的分区缓存，效率比较低

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271923500.png)



- `StickyAssignor`（粘性分区）

在第一次分配后，每个组成员都保留分配给自己的分区信息。如果有消费者加入或退出，那么在进行分区再分配时（一般情况下，消费者退出45s后，才会进行再分配，因为需要考虑可能又恢复的情况），尽可能保证消费者原有的分区不变，重新对加入或退出消费者的分区进行分配。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271909169.png)



- `CooperativeStickyAssignor`

`CooperativeStickyAssignor`是在粘性分配策略的基础上，优化了重分配的过程，使用`COOPERATIVE`协议，在整个再分配的过程中，粘性分区分配策略分配的会更加均匀和高效一些，`COOPERATIVE`协议将一次全局重平衡，改成每次小规模重平衡，直至最终收敛平衡的过程



- Kafka消费者默认的分区分配是**RangeAssignor**，**CooperativeStickyAssignor**







#### 5.3 消费者leader选举

当消费者1发起`JOIN_GROUP`请求时，他会加入组，并成为leader，然后同步分配策略。当消费者2也申请加入组时，此时会将消费者1从组里踢出，然后和消费者2一起重新加入组，先加入的成为leader，然后同步分配策略。第三个消费者加入时也是一样的流程

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404271943174.png)







## 四、kafka高级

### 1、集群脑裂问题

- `Controller`节点的作用是在`Zookeeper`的帮助下管理和协调控制整个Kafka集群。

- 集群中的任意一台`Broker`都能充当`Controller`的角色，但是，在整个集群运行过程中，只能有一个`Broker`成为`Controller`。
- 最先在`Zookeeper`上创建临时节点`/controller`成功的`Broker`就是`Controller`。如果`Controller`节点宕掉了。那么`zookeeper`的临时节点`/controller`就会因为会话超时而自动删除。而监控这个节点的`Broker`就会收到通知而向`ZooKeeper`发出创建`/controller`节点的申请，一旦创建成功，那么创建成功的`Broker`节点就成为了新的`Controller`
- `Controller`节点会向其他`broker`发送集群相关数据【`controller`信息、集群中其他节点信息等】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202404292316752.png)



假设`Controller`节点没有宕掉，而是因为网络的抖动，不稳定，导致和`ZooKeeper`之间的会话超时，那么此时，整个`Kafka`集群就会认为之前的`Controller`已经下线，从而选举出新的`Controller`【新的`Controller`会向集群中其他`Broker`发送集群相关信息】，而当之前`Controller`的网络恢复后，以为自己还是`Controller`，继续管理整个集群，那么此时，整个Kafka集群就有两个`controller`进行管理，这种情况，称为**脑裂现象**

为了解决这个问题，Kafka通过**`epoch`纪元**的方式来解决，在`zookeeper`中保存的有纪元信息。每当一个`Broker`当选`Controller`时，会告诉当前`Broker`是第几任`Controller`，一旦重新选举时，这个任期会自动加1，`zookeeper`中保存的是最新的`epoch`信息。不同任期的`Controller`的`epoch`值是不同的，旧的`controller`一旦发现集群中有新任`controller的`时候，那么它就会完成退出操作（清空缓存，中断和broker的连接，并重新加载最新的缓存），让自己重新变成一个普通的`Broker`。



​          



### 2、零拷贝

**页缓存**

- 页缓存是操作系统实现的一种磁盘缓存，用来减少对磁盘I/O的操作。就是把磁盘中的数据缓存到内存中，把对磁盘的访问变为对内存的访问。

- 当一个进程准备读取磁盘上的文件内容时，操作系统会先查看待读取的数据所在的页（page）是否在页缓存（page cache）中，如果存在则直接返回数据，从而避免了对物理磁盘I/O操作；如果没有命中，则操作系统会向磁盘发起读取请示并将读取的数据页写入页缓存，之后再将数据返回进程。

- 如果一个进程需要将数据写入磁盘，那么操作系统也会检测数据对应的页是否在页缓存中，如果不存在，则会先在页缓存中添加相应的页，最后将数据写入对应的页。被修改过后的页也就变成了脏页，操作系统会在合适的时间把脏页中的数据写入磁盘，以操作数据的一致性。

> Kafka中大量使用了页缓存，这是Kafka实现高吞吐的重要因素之一。虽然消息都是先被写入页缓存，然后由操作系统负责具体的刷盘任务，但在Kafka中同样提供了同步刷盘及间断性强制刷盘（fsync）的功能，这些功能可以通过`log.flush.interval.message`、`log.flush.interval.ms`等参数来控制。同步刷盘可以提高消息的可靠性，防止由于机器掉电等异常造成处于页缓存而没有及时写入磁盘的消息丢失。但一般不建议这么做，刷盘任务就应交由操作系统去调配，消息的可靠性应该由多副本机制来保障，而不是由同步刷盘这种严重影响性能的行为来保障。





kafka中，消费者消费数据时，使用的是**零拷贝**技术。

- 如果不使用零拷贝，就是左边的流程：kafka先从磁盘读取数据加载到页缓存，将页缓存的数据发送给`Broker`，然后`Broker`需要将数据发送给消费者，先将数据发送给块缓存，然后将块缓存中的数据写出。这个过程会从内核态切换为用户态，然后从用户态切换到内核态。如果消费者频繁读取数据，会存在大量用户态和内核态的切换操作，IO性能就会急剧下降
- 使用零拷贝技术后，就是右边的流程：在内核态时，将数据读取到页缓存后，不需要发送给`Broker`，而是直接发送给块缓存，然后写出给消费者。这样就避免了用户态和内核态的频繁切换，提高了性能

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202404292332645.png" style="zoom: 80%;" />                      <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202404292333374.png" style="zoom:80%;" />      







### 3、顺写日志

- 在`Kafka`中，一个`topic`可以分为多个`partition`，`topic `是逻辑上的概念，`patition`是物理上的概念，每个`patition`对应一个log文件，log文件中存储的就是`producer`生产的数据，`patition`生产的数据会被不断的添加到log文件的末端，且每条数据都有自己的offset。

- Kafka底层采用的是`FileChannel.wrtieTo`进行数据的写入，写的时候并不是直接写入文件，而是写入`ByteBuffer`，当缓冲区满了，再将数据顺序写入文件，无需定位文件中的某一个位置进行写入，那么就减少了磁盘查询，数据定位的过程。所以性能要比随机写入，效率高得多。









### 4、SpringBoot集成Kafka

**1、添加依赖**

```xml
<dependency>
	<groupId>org.springframework.kafka</groupId>
	<artifactId>spring-kafka</artifactId>
</dependency>
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>3.6.1</version>
</dependency>
```



**2、配置kafka信息**

```yml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      acks: all
      batch-size: 16384
      buffer-memory: 33554432 # 缓冲区大小
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
      retries: 0
    consumer:
      group-id: test # 消费者组
      auto-offset-reset: earliest # 从头开始消费
      enable-auto-commit: true # 是否自动提交偏移量offset
      auto-commit-interval: 1s # 前提是 enable-auto-commit=true。自动提交的频率
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      max-poll-records: 2
      properties:
        #如果在这个时间内没有收到心跳，该消费者会被踢出组并触发{组再平衡 rebalance}
        session.timeout.ms: 120000
        #最大消费时间。此决定了获取消息后提交偏移量的最大时间，超过设定的时间（默认5分钟），服务端也会认为该消费者失效。踢出并再平衡
        max.poll.interval.ms: 300000
        #配置控制客户端等待请求响应的最长时间。
        #如果在超时之前没有收到响应，客户端将在必要时重新发送请求，
        #或者如果重试次数用尽，则请求失败。
        request.timeout.ms: 60000
        #订阅或分配主题时，允许自动创建主题。0.11之前，必须设置false
        allow.auto.create.topics: true
        #poll方法向协调器发送心跳的频率，为session.timeout.ms的三分之一
        heartbeat.interval.ms: 40000
        #每个分区里返回的记录最多不超max.partitions.fetch.bytes 指定的字节
        #0.10.1版本后 如果 fetch 的第一个非空分区中的第一条消息大于这个限制
        #仍然会返回该消息，以确保消费者可以进行
        #max.partition.fetch.bytes=1048576  #1M
    listener:
      # 当enable.auto.commit的值设置为false时，该值会生效；为true时不会生效
      # manual_immediate: 需要手动调用Acknowledgment.acknowledge()后立即提交
      # ack-mode: manual_immediate
      missing-topics-fatal: true # 如果至少有一个topic不存在，true启动失败。false忽略
      # type: single # 单条消费？批量消费？ # 批量消费需要配合 consumer.max-poll-records
      type: batch
      concurrency: 2 # 配置多少，就为为每个消费者实例创建多少个线程。多出分区的线程空闲
    template:
      default-topic: "test"
```



**3、生产者**

```java
@RestController
@RequestMapping("/kafka")
@Slf4j
public class KafkaProducerController {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    @ResponseBody
    @PostMapping(value = "/produce", produces = "application/json")
    public String produce(@RequestBody Object obj) {
        try {
            String obj2String = JSONUtil.toJsonStr(obj);
            kafkaTemplate.send(SpringBootKafkaConfig.TOPIC_TEST, obj2String);
            return "success";
        } catch (Exception e) {
            e.getMessage();
        }
        return "success";
    }
}
```



**4、消费者**

```java
@Component
@Slf4j
public class KafkaDataConsumer {
    // 订阅的topic和消费者组
    @KafkaListener(topics = SpringBootKafkaConfig.TOPIC_TEST, groupId = SpringBootKafkaConfig.GROUP_ID)
    public void topic_test(List<String> messages, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
        for (String message : messages) {
            final JSONObject entries = JSONUtil.parseObj(message);
            System.out.println(SpringBootKafkaConfig.GROUP_ID + " 消费了： Topic:" + topic + ",Message:" + entries.getStr("data"));
        }
    }
}
```







### 5、Controller选举

`kafka`的所有`broker`节点会监听`Zookeeper`中的一个`Controller`临时节点，如果这个节点没有创建，那么`broker`就会申请创建，一旦创建成功，那么创建成功的`broker`就会当选为集群的管理者`Controller`，一旦`Controller`失去了和`Zookeeper`的通信，那么临时节点就会消失，此时就会再次进行`Controller`的选举，选举的规则是完全一样的，一旦新的`Controller`选举完成，那么`Controller`纪元会被更新。





### 6、Producer消息重复或丢失

- `Producer`消息重复和消息丢失的原因，主要是kafka为了提高数据可靠性所提供的重试机制，如果禁用重试机制，那么一旦数据发送失败，数据就丢失了
- 数据重复，是因为开启重试机制后，如果因为网络阻塞或不稳定，导致数据重新发送。那么数据就有可能是重复的。所以kafka提供了幂等性操作解决数据重复，并且幂等性操作要求必须开启重试功能和ACK取值为-1，这样，数据就不会丢失了。

- kafka提供的幂等性操作只能保证同一个生产者会话中同一个分区中的数据不会重复，一旦数据发送过程中，生产者对象重启，那么幂等性操作就会失效。那么此时就需要使用Kafka的事务功能来解决跨会话的幂等性操作。但是跨分区的幂等性操作是无法实现的。





### 7、Consumer消息重复或丢失

这里主要是消费者提交偏移量的问题

- 消费者为了防止意外情况下，重启后不知道从哪里消费，所以会每5s时间自动保存偏移量。但是这种自动保存偏移量的操作是基于时间的，一旦未达到时间，消费者重启了，那么消费者就可能重复消费数据。

- Kafka也提供了手动保存偏移量的2种方式，一个是同步提交，一个是异步提交。本质上都是提交一批数据的最后一个偏移量的值，但是可能会出现，偏移量提交完毕，但是拉取的数据未处理完毕，消费者重启了。那么此时有的数据就消费不到了，也就是所谓的数据丢失。







### 8、Kafka数据如何保证有序

**生产有序**

- 生产有序是生产者对象需要给数据增加序列号用于标记数据的顺序，然后在服务端进行缓存数据的比对，一旦发现数据是乱序的，那么就需要让生产者客户端进行数据的排序，然后重新发送数据，保证数据的有序。
- 缓存数据的比对，最多只能有5条数据比对，所以生产者客户端需要配置参数，将在途请求缓冲区的请求队列数据设置为5，否则数据依然可能乱序。因为服务端的缓存数据是以分区为单位的，所以这就要求生产者客户端需要将数据发送到一个分区中，如果数据发送到多个分区，是无法保证顺序的。



**存储有序**

- 存储有序指的是kafka的服务端获取数据后会将数据顺序写入日志文件，这样就保证了存储有序，只能是保证一个分区的数据有序。



**消费有序**

- 消费有序是kafka在存储数据时会给数据增加一个访问的偏移量值，消费者只能按照偏移量的方式顺序访问，并且一个分区的数据只能被消费者组中的一个消费者消费，按照偏移量方式的读取就不会出现乱序的情况。





## 五、面试题

### 1、Kafka为什么这么快

**消息发送**

- 批量发送
- 异步发送
- 消息压缩
- 并行发送

**消息存储**

- 零拷贝
- 顺写日志
- 稀疏索引：Kafka存储消息是通过分段的日志文件，每个分段都有自己的索引文件。这些索引文件中的条目不是对分段中的每条消息都建立索引，而是每隔一定数量的消息建立一个索引点，这就构成了稀疏索引。稀疏索引减少了索引大小，使得加载到内存中的索引更小，提高了查找特定消息的效率。
- 页缓存：Kafka将其数据存储在磁盘中，在访问数据时，它会先将数据加载到操作系统的页缓存中，并在页缓存中保留一份副本，从而实现快速的数据访问。
- 分区和副本

**消息消费**

- 消费者组消费
- 并行消费：不同的消费者可以并行的消费不同的分区，实现消费的并行处理
- 批量拉取：消费者一次可以拉取多个消息进行消费





### 2、kafka如何保证消息不丢失

**producer**

- 设置回调，在发送失败时进行重试。`producer.send(msg, callback)`
- 开启ack机制

**broker**

- 持久化存储
- ISR复制机制

**Consumer**

- 消费者基于偏移量进行数据的消费，kafka会保存每个分区的偏移量，即使消费者重启了，下次还会从该偏移量的位置开始消费
- kafka消费者基于消费者组的形式进行消费。当一个消费者宕机不可用时，其他消费者会继续消费该分区，保证消息不丢失
- 可以采用手动提交偏移量的方式





### 3、kafka怎么保证数据只消费一次

- 在Kafka中，每个消费者都必须加入至少一个消费者组。同一个消费者组内的消费者可以共享消费者的负载。因此，如果一个消息被消费组中的任何一个消费者消费了，那么其他消费者就不会再收到这个消息了。
- 另外，消费者可以通过手动提交偏移量来控制消息的消费情况。通过手动提交位移，消费者可以跟踪已经消费的消息，确保不会重复消费同—消息。
- 客户端自己可以做一些幂等机制，防止消息的重复消费。



**kafka的消息语义**

- `At-least-once`

  `At-least-once`消费语义意味着消费者至少消费一次消息，但可能会重复消费同一消息。在`At-least-once`语义中，当消费者从Kafka服务器读取消息时，消息的偏移量会被记录下来。一旦消息被成功处理，消费者会将位移提交回Kafka服务器。如果消息处理失败，消费者不会提交位移。这意味着该消息将在下一次重试时再次被消费。

- `Exactly-once`
  - `Exactly-once`消费语义意味着每个消息仅被消费一次，且不会被重复消费。在`Exactly-once`语义中，Kafka保证消息只被处理一次，同时保持消息的顺序性。为了实现`Exactly-once`语义，Kafka引入了一个新的概念：事务。
  - 事务是一系列的读写操作，这些操作要么全部成功，要么全部失败。在Kafka中，生产者和消费者都可以使用事务，以保证消息的`Exactly-once`语义。消费者可以使用事务来保证消息的消费和位移提交是原子的，而生产者可以使用事务来保证消息的生产和位移提交是原子的。

- `At most once`：消息可能会丢，但绝不会重复传递





### 4、如何实现顺序消费

> kafka中同一个partition中的消息是有序的，但是跨partition，消息是无序的



**生产有序**

- 生产有序是生产者对象需要给数据增加序列号用于标记数据的顺序，然后在服务端进行缓存数据的比对，一旦发现数据是乱序的，那么就需要让生产者客户端进行数据的排序，然后重新发送数据，保证数据的有序。
- 缓存数据的比对，最多只能有5条数据比对，所以生产者客户端需要配置参数，将在途请求缓冲区的请求队列数据设置为5，否则数据依然可能乱序。因为服务端的缓存数据是以分区为单位的，所以这就要求生产者客户端需要将数据发送到一个分区中，如果数据发送到多个分区，是无法保证顺序的。



**存储有序**

- 存储有序指的是kafka的服务端获取数据后会将数据顺序写入日志文件，这样就保证了存储有序，只能是保证一个分区的数据有序。



**消费有序**

- 消费有序是kafka在存储数据时会给数据增加一个访问的偏移量值，消费者只能按照偏移量的方式顺序访问，并且一个分区的数据只能被消费者组中的一个消费者消费，按照偏移量方式的读取就不会出现乱序的情况。
