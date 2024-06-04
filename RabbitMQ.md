## 一、MQ介绍

### 1、异步调用

异步调用方式其实就是基于消息通知的方式，一般包含三个角色：

- 消息发送者：投递消息的人，就是原来的调用方
- 消息Broker：管理、暂存、转发消息，你可以把它理解成微信服务器
- 消息接收者：接收和处理消息的人，就是原来的服务提供方



<img src="https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1686990662733-65b0eac8-f65f-4024-a581-6d5761c4c5a4.jpeg" style="zoom:67%;" />





在异步调用中，发送者不再直接同步调用接收者的业务接口，而是发送一条消息投递给消息Broker。然后接收者根据自己的需求从消息Broker那里订阅消息。每当发送方发送消息后，接受者都能获取消息并处理。这样，发送消息的人和接收消息的人就完全解耦了。



**以余额支付业务为例**

> 1、用户支付完毕后，需要扣减余额，更新支付流水，这里必须是串行的。
>
> 2、执行完后，我们可以拿到支付成功还是失败的结果。如果支付成功，那么就将这个消息发送到消息队列中，更新订单状态、短信通知用户的业务我们用一个异步线程去执行，因此支付业务，只需要将前两步执行完即可。

<img src="https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1686990257816-4f0b5ddd-7618-4095-b797-25b92f0bf2a5.jpeg" style="zoom:50%;" />



**异步调用优点**

- 耦合度更低
- 性能更好
- 业务拓展性强
- 故障隔离，避免级联失败

**异步调用缺点**

- 完全依赖于Broker的可靠性、安全性和性能
- 架构复杂，后期维护和调试麻烦





### 2、安装RabbitMQ

1、装完docker后，将docker镜像导入，然后加载该镜像

```
sudo docker load -i mq.tar
```

2、运行该镜像的实例

> 其中，有两个映射端口
>
> - 15672：`RabbitMQ`提供的管理控制台的端口
> - 5672：`RabbitMQ`的消息发送处理接口
>
> 安装完成后，我们访问` http://192.168.198.130:15672`【这个ip是虚拟机ip】即可看到管理控制台。首次访问需要登录，默认的用户名和密码在配置文件中已经指定了。

```
docker run \
 -e RABBITMQ_DEFAULT_USER=xrj \
 -e RABBITMQ_DEFAULT_PASS=990918 \
 -v mq-plugins:/plugins \
 --name mq \
 --hostname mq \
 -p 15672:15672 \
 -p 5672:5672 \
 --network xrj-mq \		# 网络名称，没有这个网络需要新建网络  docker network ls查看网络  docker network create xrj-mq创建网络
 -d \
 rabbitmq:3.8-management
```





`RabbitMQ`对应的架构如图：
<img src="https://cdn.nlark.com/yuque/0/2023/png/27967491/1687136827222-52374724-79c9-4738-b53f-653cc0805d22.png#averageHue=%23e8d7b3&clientId=u6a529863-cf4b-4&from=paste&height=495&id=ub8dd8df6&originHeight=614&originWidth=1458&originalType=binary&ratio=1.2395833730697632&rotation=0&showTitle=false&size=104273&status=done&style=none&taskId=uc0c132a5-73a3-4024-819f-61241da2511&title=&width=1176.2016429676203" alt="image.png" style="zoom:67%;" />



- `publisher`：生产者，也就是发送消息的一方
- `consumer`：消费者，也就是消费消息的一方
- `queue`：队列，存储消息。生产者投递的消息会暂存在消息队列中，等待消费者处理
- `exchange`：交换机，负责消息路由。生产者发送的消息由交换机决定投递到哪个队列。
- `virtual host`：虚拟主机，起到数据隔离的作用。每个虚拟主机相互独立，有各自的exchange、queue。这样每个项目使用自己的`virtual host`就不会造成数据混乱





### 3、收发消息

**交换机**

我们在`RabbitMQ`的控制台界面点击Exchanges，就可以看到很多的交换机，我们点击任意交换机，可以进入交换机详情页面，我们点击`Publish message`可以利用交换机发送一条消息，但是现在没有消费者，所以消息就丢失了，说明交换机没有存储消息的能力。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181406904.png)



**队列**

我们打开`Queues`选项，可以看到已经创建的队列，点击`Add a new queue`可以新建一个队列。此时，我们再次向`amq.fanout`交换机发送一条消息，会发现消息依然没有到达队列。这是因为，我们创建了队列来接收消息，但是交换机并没有绑定队列，所以我们需要使交换机绑定我们创建的队列，队列才能收到交换机的消息。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181408401.png)





**绑定关系**

点击`Exchanges`选项，点击`amq.fanout`交换机，进入交换机详情页，然后点击`Bindings`菜单，在表单中填写要绑定的队列名称。

绑定完队列之后，在通过控制台对该交换机发送消息，回到`Queue`页面，可以发现，绑定的队列中都收到了消息，此时如果有消费者监听了`MQ`的`hello.queue1`或`hello.queue2`队列，就能接收到消息了。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181411016.png)







### 4、数据隔离

**用户管理**

点击`Admin`选项卡，首先会看到`RabbitMQ`控制台的用户管理界面：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181414000.png)

这里的用户都是`RabbitMQ`的管理或运维人员。目前只有安装`RabbitMQ`时添加的`xrj`这个用户。

- `Name`：`xrj`，也就是用户名
- `Tags`：`administrator`，说明`xrj`用户是超级管理员，拥有所有权限
- `Can access virtual host`： `/`，可以访问的`virtual host`，这里的`/`是默认的`virtual host`



对于小型企业而言，出于成本考虑，我们通常只会搭建一套`MQ`集群，公司内的多个不同项目同时使用。这个时候为了避免互相干扰， 我们会利用`virtual host`的隔离特性，将不同项目隔离。一般会做两件事情：

- 给每个项目创建独立的运维账号，将管理权限分离。
- 给每个项目创建不同的`virtual host`，将每个项目的数据隔离。



比如，我们给商城项目创建一个新的用户，命名为`xyy`，通过`Add a user`选项添加完用户后，此时的用户没有任何`virtual host`的访问权限，然后我们来授权

1、退出登录，切换到刚刚创建的`xyy`用户登录，然后点击`Virtual Hosts`菜单，进入`virtual host`管理页，默认情况下，只有第一行`/`，`/xyy`是我们创建好的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181419434.png)

2、我们通过`Add a new virtual host`选项，为当前用户创建一个虚拟主机，创建后如上图所示。然后回到`users`菜单，就会发现当前用户已经具备了对`/xyy`这个`virtual host`的访问权限了。现在切换不同的用户，看到的队列是不同的，数据被隔离起来了。







## 二、SpringAMQP

> 由于`RabbitMQ`采用了`AMQP`协议，因此它具备跨语言的特性。任何语言只要遵循`AMQP`协议收发消息，都可以与`RabbitMQ`交互。并且`RabbitMQ`官方也提供了各种不同语言的客户端。
>
> 但是，`RabbitMQ`官方提供的Java客户端编码相对复杂，而Spring的官方基于`RabbitMQ`提供了一套消息收发的模板工具：`SpringAMQP`。并且还基于`SpringBoot`对其实现了自动装配。



`SpringAMQP`提供了三个功能：

- 自动声明队列、交换机及其绑定关系
- 基于注解的监听器模式，异步接收消息
- 封装了`RabbitTemplate`工具，用于发送消息





### 1、快速入门

> 在我们的工程中，有两个子工程，一个是生产者，一个是消费者，他们的pom文件都是继承自父工程，所以父工程导入对应依赖即可

**父工程依赖**

```xml
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <!--AMQP依赖，包含RabbitMQ-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
    <!--单元测试-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
</dependencies>
```





之前的案例中，我们都是经过交换机发送消息到队列，然后消费者从队列取消息，由于这里是测试使用，所以直接向队列发送消息，跳过交换机。

我们在控制台新建一个队列`simple.queue`，一会使生产者发送消息到这个队列，消费者从这个队列获取消息。



![](https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1687261777988-23fff732-dcfa-499a-a8a1-a66328fe05e7.jpeg)







#### 1.1 消息发送

> 使用Java客户端发送完消息后，我们在`RabbitMQ`的控制台上就可以看到消息已经发送到队列中

**配置MQ地址**

```yml
spring:
  rabbitmq:
    host: 192.168.198.130
    port: 5672
    virtual-host: /xyy
    username: xyy
    password: 990918
```



**发送消息**

```java
@SpringBootTest
public class testSendMsg {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void test01() {
        String queue = "simple.queue";
        String msg = "hello mq123456";
        rabbitTemplate.convertAndSend(queue, msg);
    }
}
```





#### 1.2 消息接收

**配置MQ地址**

```yml
spring:
  rabbitmq:
    host: 192.168.198.130
    port: 5672
    virtual-host: /xyy
    username: xyy
    password: 990918
```



**接收消息**

> 1、我们将当前类交给spring管理
>
> 2、使用`@RabbitListener`注解声明监听的队列，可以监听多个队列
>
> 3、方法参数位置就是消息体内容，一旦队列中有消息，就会被该方法接收到。

```java
@Component
public class SpringRabbitListener {

    // 利用RabbitListener来声明要监听的队列信息
    // 将来一旦监听的队列中有了消息，就会推送给当前服务，调用当前方法，处理消息。
    // 可以看到方法体中接收的就是消息体的内容
    @RabbitListener(queues = "simple.queue")
    public void listenSimpleQueueMessage(String msg) {
        System.out.println("spring 消费者接收到消息：【" + msg + "】");
    }
}
```







### 2、WorkQueues模型

> Work queues，任务模型。就是**让多个消费者绑定到一个队列，共同消费队列中的消息**。
>
> 当消息处理比较耗时的时候，可能生产消息的速度会远远大于消息的消费速度。长此以往，消息就会堆积越来越多，无法及时处理。
> 此时就可以使用work 模型，**多个消费者共同处理消息，消息处理的速度就能大大提高**了。



<img src="https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1687261956699-4b3c9999-ee86-4dda-a795-1ea5f4f9eef3.jpeg" style="zoom:50%;" />



**消息发送**

> 此时我们向队列连续发送50条消息

```java
@Test
public void testWorkQueue() {
    String queue = "work.queue";
    for (int i = 1; i <= 50; i++) {
        String msg = "hello mq__" + i;
        rabbitTemplate.convertAndSend(queue, msg);
    }
}
```





**消息接收**

> 1、我们使用两个消费者来接收队列的消息。消费者1，每隔20ms处理一条消息，消费者2，每隔200ms处理一条消息。
>
> 2、测试结果：消费者1和消费者2每人消费了25条消息。
>
> - 消费者1很快完成了自己的25条消息
> - 消费者2却在缓慢的处理自己的25条消息。
>
> 3、也就是说消息是平均分配给每个消费者，并没有考虑到消费者的处理能力。导致1个消费者空闲，另一个消费者忙的不可开交。没有充分利用每一个消费者的能力，最终消息处理的耗时远远超过了1秒。这样显然是有问题的。

```java
@RabbitListener(queues = "work.queue")
public void listenSimpleQueueMessage1(String msg) throws InterruptedException {
    System.out.println("消费者1接收到消息：" + msg);
    Thread.sleep(20);
}

@RabbitListener(queues = "work.queue")
public void listenSimpleQueueMessage2(String msg) throws InterruptedException {
    System.err.println("消费者2.....接收到消息：" + msg);
    Thread.sleep(200);
}
```





**能者多劳**

> 我们在消费者的配置文件中，加上如下属性，`prefetch=1`表示消费者每次只能获取一条消息，处理完成才能获取下一个消息，而不是队列不管消费者是否消费完，就将消息平均分给各个消费者。这样，处理消息速度快的，可以处理更多的消息，性能得到提升。

```yml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1	# 每次只能获取一条消息，处理完成才能获取下一个消息
```







### 3、交换机

> 在上边的案例中，我们没有使用交换机，而是生产者直接将消息发送到队列，消费者从队列获取消息消费。
>
> 如果我们现在是一个下单业务，用户支付完毕，扣减余额成功后，下单操作就完成了。此时会发送一个支付成功的消息，然后创建订单的微服务会监听这个队列，同时增加积分的微服务也会监听这个队列，但是这个消息只会被消费一次，所以这时就需要使用交换机了。

<img src="https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1687264784359-de7cbc4a-ec60-461d-a6a4-3474ba52e0d0.jpeg" style="zoom:50%;" />



**Exchange（交换机）只负责转发消息，不具备存储消息的能力**，因此如果没有任何队列与Exchange绑定，或者没有符合路由规则的队列，那么消息会丢失。

交换机的类型有四种：

- **Fanout**：广播，将消息交给所有绑定到交换机的队列。
- **Direct**：订阅，基于`RoutingKey`（路由key）发送给订阅了消息的队列
- **Topic**：通配符订阅，与Direct类似，只不过`RoutingKey`可以使用通配符
- **Headers**：头匹配，基于MQ的消息头匹配，用的较少。





#### 3.1 Fanout交换机

> Fanout交换机是一种广播模型，他收到消息后，会将消息发送给它绑定的所有队列。
>
> 针对上边的下单业务，我们对创建订单的微服务创建一个队列，对增加积分的微服务创建一个队列，这两个队列都绑定到Fanout交换机上，当用户下单成功后就发送消息到这个Fanout交换机，然后交换机会把消息发送给所有队列。创建订单的微服务的消费者会从他的队列中获取消息，增加积分的微服务会从它的队列中获取消息，这样所有的服务都可以获取到消息了。
>
> 如果对于创建订单的微服务想要有多个消费者，那么启动多个实例即可，因为代码都是一样的，启动多个实例，就是多个消费者在监听这个队列，然后多个消费者一起获取消息。



我们创建两个队列`fanout.queue1`、`fanout.queue2`，将他们绑定到同一个`fanout`交换机上，我们使用Java程序向这个交换机发送消息，然后这两个队列都会收到消息。



**消息发送**

```java
@Test
public void testFanout() {
    String fanoutName = "xyy.fanout";
    String msg = "hello everyone";
    // 第一个参数为交换机name，第二个参数为routingKey，第三个参数为发送的消息
    rabbitTemplate.convertAndSend(fanoutName, null, msg);
}
```



**消息接收**

```java
@RabbitListener(queues = "fanout.queue1")
public void fanoutQueueMessage1(String msg) {
    System.out.println("fanout1 消费者接收到消息：" + msg);
}

@RabbitListener(queues = "fanout.queue2")
public void fanoutQueueMessage(String msg) {
    System.out.println("fanout2 消费者接收到消息：" + msg);
}
```







#### 3.2 Direct交换机

> 1、在Fanout模式中，一条消息，会被所有绑定的队列都消费。但是，在某些场景下，我们希望不同的消息被不同的队列消费。例如我们支付成功希望调用增加积分和修改支付状态的操作，但是支付失败只执行修改支付状态的操作。这时就要用到Direct类型的Exchange。
>
> 2、在Direct模型下
>
> - 队列与交换机的绑定，不能是任意绑定了，而是要指定一个`RoutingKey`（路由key）
> - 消息的发送方在 向 Exchange发送消息时，也必须指定消息的 `RoutingKey`。
> - Exchange不再把消息交给每一个绑定的队列，而是根据消息的`Routing Key`进行判断，只有队列的`Routingkey`与消息的 `Routing key`完全一致，才会接收到消息

<img src="https://cdn.nlark.com/yuque/0/2023/png/27967491/1687182404437-027a5191-b037-4033-baab-6bafd998161d.png#averageHue=%23fbf5f5&clientId=u0fe93ba5-a0ba-4&from=paste&height=430&id=uf5b6a678&originHeight=533&originWidth=1686&originalType=binary&ratio=1.2395833730697632&rotation=0&showTitle=false&size=93278&status=done&style=none&taskId=ud6ffb209-4207-40a6-a7ab-4977cab3b5d&title=&width=1360.1344101806637" alt="image.png" style="zoom:60%;" />



创建两个队列`direct.queue1`、`direct.queue2`，创建一个`direct`类型的交换机，将`direct.queue1`绑定到交换机上，`Routing key`是`red`和`blue`，将`direct.queue2`绑定到交换机上，`Routing key`是`red`和`yellow`。生产者向交换机发送消息，同时指定`routingKey`，只有队列的`Routing key`和发送消息时携带的`routingKey`相同，交换机才会将消息发送给该队列

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181559098.png)



**发送消息**

```java
@Test
public void testDirect() {
    String fanoutName = "xyy.direct";
    String msg = "hello yellow";
    rabbitTemplate.convertAndSend(fanoutName, "yellow", msg);
}
```



**接收消息**

```java
@RabbitListener(queues = "direct.queue1")
public void directQueueMessage1(String msg) {
    System.out.println("direct1 消费者接收到消息：" + msg);
}

@RabbitListener(queues = "direct.queue2")
public void directQueueMessage(String msg) {
    System.out.println("direct2 消费者接收到消息：" + msg);
}
```







#### 3.3 Topic交换机

> `Topic`类型的`Exchange`与`Direct`相比，都是可以根据`RoutingKey`把消息路由到不同的队列。不过`Topic`类型的`Exchange`可以让队列在绑定`BindingKey` 的时候使用通配符



`BindingKey` 一般都是有一个或多个单词组成，多个单词之间以`.`分割，例如： `item.insert`

通配符规则：

- `#`：匹配0个或多个词
- `*`：匹配1个词

举例：

- `item.#`：能够匹配`item.spu.insert` 或者 `item.spu`
- `item.*`：只能匹配`item.spu`



我们创建两个队列`topic.queue1`、`topic.queue2`，创建一个`topic`类型的交换机。将`topic.queue1`绑定到该交换机上，`Routing key`设置为`china.#`。将`topic.queue2`绑定到该交换机上，`Routing key`设置为`#.news`。这样`topic.queue1`可以接收`routingKey`以`china`开头的任何消息，`topic.queue2`可以接收`routingKey`以`news`结尾的任何消息。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181620173.png)



**发送消息**

```java
@Test
public void testTopic() {
    String fanoutName = "xyy.topic";
    String msg = "china news";
    rabbitTemplate.convertAndSend(fanoutName, "china.news", msg);
}
```



**接收消息**

```java
@RabbitListener(queues = "topic.queue1")
public void topicQueueMessage1(String msg) {
    System.out.println("topic1 消费者接收到消息：" + msg);
}

@RabbitListener(queues = "topic.queue2")
public void topicQueueMessage(String msg) {
    System.out.println("topic2 消费者接收到消息：" + msg);
}
```







### 4、声明队列和交换机

> 在之前我们都是基于`RabbitMQ`控制台来创建队列、交换机。但是在实际开发时，队列和交换机是程序员定义的，将来项目上线，又要交给运维去创建。那么程序员就需要把程序中运行的所有队列和交换机都写下来，交给运维。在这个过程中是很容易出现错误的。
>
> 因此推荐的做法是由程序启动时检查队列和交换机是否存在，如果不存在自动创建。



`SpringAMQP`提供了一个Queue类，用来创建队列。还提供了一个Exchange接口，来表示所有不同类型的交换机。

我们可以自己创建队列和交换机【直接`new`】，不过`SpringAMQP`还提供了`ExchangeBuilder`来简化这个过程

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310181654237.png)







#### 4.1 使用代码声明

**Fanout**

```java
@Configuration
public class FanoutConfig {

    /**
     * 声明交换机
     */
    @Bean
    public FanoutExchange fanoutExchange() {
        // return ExchangeBuilder.fanoutExchange("xyy.fanout").build();
        return new FanoutExchange("xyy.fanout");
    }

    /**
     * 声明第一个队列
     */
    @Bean
    public Queue fanoutQueue1() {
        // 这里duranle表示持久化，直接new的话，该属性默认为true
        // return QueueBuilder.durable("fanout.queue1").build();
        return new Queue("fanout.queue1");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue1(Queue fanoutQueue1, FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(fanoutQueue1).to(fanoutExchange);
    }

    /**
     * 声明第二个队列
     */
    @Bean
    public Queue fanoutQueue2() {
        // return QueueBuilder.durable("fanout.queue2").build();
        return new Queue("fanout.queue2");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue2() {
        return BindingBuilder.bind(fanoutQueue2()).to(fanoutExchange());
    }
}
```



**Direct**

> 使用代码绑定`Direct`类型的交换机和队列时，因为要指定`Routing Key`，而一个`Bean`只能指定一个，所以如果想要指定多个`Routing Key`，就需要创建多个`Bean`

```java
@Configuration
public class DirectConfig {

    /**
     * 声明第一个交换机
     */
    @Bean
    public DirectExchange directExchange(){
        // return ExchangeBuilder.directExchange("xyy.direct").build();
        return new DirectExchange("xyy.direct");
    }

    /**
     * 声明第一个队列
     */
    @Bean
    public Queue directQueue1() {
        return new Queue("direct.queue1");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding queueBinding1() {
        return BindingBuilder.bind(directQueue1()).to(directExchange()).with("red");
    }
    @Bean
    public Binding queueBinding2() {
        return BindingBuilder.bind(directQueue1()).to(directExchange()).with("blue");
    }
}
```



**Topic**

```java
@Configuration
public class TopicConfig {

    /**
     * 声明交换机
     */
    @Bean
    public TopicExchange topicExchange() {
        return new TopicExchange("xyy.topic");
    }

    /**
     * 声明队列
     */
    @Bean
    public Queue topicQueue() {
        return new Queue("topic.queue1");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding topicBindingQueue1(){
        return BindingBuilder.bind(topicQueue()).to(topicExchange()).with("#.news");
    }
}
```





#### 4.2 使用注解声明

在使用`@RabbitListener`注解声明消费者监听的队列时，可以直接设置`bindings`属性，他的类型是`@QueueBinding`注解类型的数组，即可以声明多个。

`value`属性指定该方法绑定的队列名

`exchange`属性指定该队列绑定的交换机，其中`value`属性指定交换机名，`type`属性指定交换机类型

`key`属性：对于`direct`和`topic`类型的交换机可以通过该属性绑定`Routing Key`

```java
@Component
public class SpringRabbitListener {
    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("fanout.queue1"),
            exchange = @Exchange(value = "xyy.fanout", type = ExchangeTypes.FANOUT)
    ))
    public void fanoutQueueMessage1(String msg) {
        System.out.println("fanout1 消费者接收到消息：" + msg);
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("fanout.queue2"),
            exchange = @Exchange(value = "xyy.fanout", type = ExchangeTypes.FANOUT)
    ))
    public void fanoutQueueMessage(String msg) {
        System.out.println("fanout2 消费者接收到消息：" + msg);
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("direct.queue1"),
            exchange = @Exchange(value = "xyy.direct", type = ExchangeTypes.DIRECT),
            key = {"red", "blue"}
    ))
    public void directQueueMessage1(String msg) {
        System.out.println("direct1 消费者接收到消息：" + msg);
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("direct.queue2"),
            exchange = @Exchange(value = "xyy.direct", type = ExchangeTypes.DIRECT),
            key = {"red", "yellow"}
    ))
    public void directQueueMessage(String msg) {
        System.out.println("direct2 消费者接收到消息：" + msg);
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("topic.queue1"),
            exchange = @Exchange(value = "xyy.topic", type = ExchangeTypes.TOPIC),
            key = "#.news"
    ))
    public void topicQueueMessage1(String msg) {
        System.out.println("topic1 消费者接收到消息：" + msg);
    }

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("topic.queue2"),
            exchange = @Exchange(value = "xyy.topic", type = ExchangeTypes.TOPIC),
            key = "china.#"
    ))
    public void topicQueueMessage(String msg) {
        System.out.println("topic2 消费者接收到消息：" + msg);
    }
}
```





### 5、消息转换器

#### 5.1 默认消息转换器

> 1、我们创建一个队列`object.queue`，交换机`xyy.object`，将该队列绑定到交换机上，先不使用消费者接收消息。
>
> 2、生产者发送一条消息，是`Object`类型的，此时在`RabbitMQ`的控制台查看消息，发现消息的格式是序列的格式，消息内容也是序列化后的内容。这是因为在数据传输时，会把发送的消息序列化为字节发送给`MQ`，接收消息的时候，还会把字节反序列化为Java对象。
>
> 3、只不过，默认情况下Spring采用的序列化方式是`JDK`序列化。但是`JDK`序列化存在下列问题：
>
> - 数据体积过大
> - 有安全漏洞
> - 可读性差

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310191459896.png)

```java
@Test
public void testObject() {
    String objectName = "xyy.object";
    Map<String, Object> msg = new HashMap<>(2);
    msg.put("name", "xrj");
    msg.put("age", 20);
    rabbitTemplate.convertAndSend(objectName, "", msg);
}
```





**源码分析**

1、执行`rabbitTemplate.convertAndSend`方法后，最后会执行`convertMessageIfNecessary`方法，参数`object`就是我们发送的消息。

2、如果`object`是`Message`类型，那么就直接返回，否则通过`getRequiredMessageConverter()`拿到消息转换器，默认情况下使用的是`SimpleMessageConverter`，然后执行`toMessage`方法。`toMessage`方法最终会调用`createMessage`方法

```java
protected Message convertMessageIfNecessary(Object object) {
    return object instanceof Message ? (Message)object : this.getRequiredMessageConverter().toMessage(object, new MessageProperties());
}
```

3、在`createMessage`方法中，首先判断`object`是不是`byte[]`类型的，是的话直接返回；然后判断是不是`String`类型的，是的话就返回`String`的字节数组，最后判断`object`是不是可序列化的，是的话就执行`serialize`方法。

```java
protected Message createMessage(Object object, MessageProperties messageProperties) throws MessageConversionException {
    byte[] bytes = null;
    if (object instanceof byte[]) {
        bytes = (byte[])object;
        messageProperties.setContentType("application/octet-stream");
    } else if (object instanceof String) {
        try {
            bytes = ((String)object).getBytes(this.defaultCharset);
        } catch (UnsupportedEncodingException var6) {
            throw new MessageConversionException("failed to convert to Message content", var6);
        }

        messageProperties.setContentType("text/plain");
        messageProperties.setContentEncoding(this.defaultCharset);
    } else if (object instanceof Serializable) {
        try {
            bytes = SerializationUtils.serialize(object);
        } catch (IllegalArgumentException var5) {
            throw new MessageConversionException("failed to convert to serialized Message content", var5);
        }

        messageProperties.setContentType("application/x-java-serialized-object");
    }

    if (bytes != null) {
        messageProperties.setContentLength((long)bytes.length);
        return new Message(bytes, messageProperties);
    } else {
        throw new IllegalArgumentException(this.getClass().getSimpleName() + " only supports String, byte[] and Serializable payloads, received: " + object.getClass().getName());
    }
}
```

4、在`serialize`中，是通过输出流将`object`对象序列化的，所以在`RabbitMQ`控制台看到的内容是序列化后的字节数组。

```java
public static byte[] serialize(Object object) {
    if (object == null) {
        return null;
    } else {
        ByteArrayOutputStream stream = new ByteArrayOutputStream();

        try {
            (new ObjectOutputStream(stream)).writeObject(object);
        } catch (IOException var3) {
            throw new IllegalArgumentException("Could not serialize object of type: " + object.getClass(), var3);
        }

        return stream.toByteArray();
    }
}
```







#### 5.2 配置json转换器

> 为了使发送到`RabbitMQ`上的内容不是字节数组，我们自定义一个消息转换器，不使用默认的`SimpleMessageConverter`，而是使用`json`转换器，这样将`object`数据转为`json`格式，然后发送给`RabbitMQ`。



**1、引入依赖**

> 在生产者和消费者中都要引入依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.9.10</version>
</dependency>
```



**2、配置消息转换器**

> 当生产者配置了消息转换器后，消费者接收消息也需要配置消息转换器。否则消费者还是使用的默认的消息转换器，是无法解析json格式数据的。

```java
@Configuration
public class MessageConfig {
    @Bean
    public MessageConverter messageConverter(){
        Jackson2JsonMessageConverter messageConverter = new Jackson2JsonMessageConverter();
        // 自动创建消息id，用于识别不同消息，也可以在业务中基于ID判断是否是重复消息
        messageConverter.setCreateMessageIds(true);
        return messageConverter;
    }
}
```



配置完消息转换器后，再次发送消息，`RabbitMQ`中查看就是转为json格式的内容了，因为现在使用的消息转换器就是我们配置的json的转换器。



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310191520958.png)







## 三、MQ高级

在我们的商城业务中，支付成功后利用`RabbitMQ`通知交易服务，更新业务订单状态为已支付。但是如果这里`MQ`通知失败，那么支付服务中支付流水显示支付成功，而交易服务中的订单状态却显示未支付，数据出现了不一致，所以我们需要保证MQ消息的可靠性。



消息从生产者到消费者的每一步都可能导致消息丢失：

- 发送消息时丢失：
  - 生产者发送消息时连接MQ失败
  - 生产者发送消息到达MQ后未找到`Exchange`
  - 生产者发送消息到达MQ的`Exchange`后，未找到合适的`Queue`
  - 消息到达MQ后，处理消息的进程发生异常
- MQ导致消息丢失：
  - 消息到达MQ，保存到队列后，尚未消费就突然宕机
- 消费者处理消息时：
  - 消息接收后尚未处理突然宕机
  - 消息接收后处理过程中抛出异常

综上，我们要解决消息丢失问题，保证MQ的可靠性，就必须从3个方面入手：

- 确保生产者一定把消息发送到MQ
- 确保MQ不会将消息弄丢
- 确保消费者一定要处理消息





### 1、发送者可靠性

#### 1.1 生产者重试机制

> 生产者发送消息时，出现了网络故障，导致与`MQ`的连接中断。
>
> 为了解决这个问题，`SpringAMQP`提供了消息发送时的重试机制。即：当`RabbitTemplate`与`MQ`连接超时后，多次重试。在MQ的配置中加入如下配置即可。

```yml
spring:
  rabbitmq:
    connection-timeout: 1s  # 设置MQ的连接超时时间
    template:
      retry:
        enabled: true # 开启超时重试机制
        initial-interval: 1000ms  # 失败后的初始等待时间
        multiplier: 1 # 失败后下次的等待时长倍数，下次等待时长 = initial-interval * multiplier
        max-attempts: 3 # 最大重试次数
```



**注意**：当网络不稳定的时候，利用重试机制可以有效提高消息发送的成功率。不过`SpringAMQP`提供的重试机制是**阻塞式**的重试，也就是说多次重试等待的过程中，当前线程是被阻塞的。如果对于业务性能有要求，建议禁用重试机制。如果一定要使用，请合理配置等待时长和重试次数，当然也可以考虑使用异步线程来执行发送消息的代码





#### 1.2 生产者确认机制

一般情况下，只要生产者与`MQ`之间的网络连接顺畅，基本不会出现发送消息丢失的情况，因此大多数情况下我们无需考虑这种问题。不过，在少数情况下，也会出现消息发送到`MQ`之后丢失的现象，比如：

- MQ内部处理消息的进程发生了异常
- 生产者发送消息到达`MQ`后未找到`Exchange`
- 生产者发送消息到达`MQ`的`Exchange`后，未找到合适的`Queue`，因此无法路由



针对上述情况，`RabbitMQ`提供了生产者消息确认机制，包括`Publisher Confirm`和`Publisher Return`两种。在开启确认机制的情况下，当生产者发送消息给`MQ`后，`MQ`会根据消息处理的情况返回不同的**回执**。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/27967491/1690366611659-d5c7f355-7ab1-4eb8-8488-13e1d98843ce.png#averageHue=%23faf7f7&clientId=ucb403171-cc9e-4&from=paste&height=376&id=ue3c6e070&originHeight=466&originWidth=1434&originalType=binary&ratio=1.2395833730697632&rotation=0&showTitle=false&size=81765&status=done&style=none&taskId=ue6af669a-1775-4a0f-ad77-cd9bc059880&title=&width=1156.8402990504578)



- 当消息投递到`MQ`，但是路由失败时【`routing key`写的不对或者队列绑定有问题】，通过**Publisher Return**返回异常信息，同时返回**ACK**的确认信息，代表投递成功
- 临时消息投递到了`MQ`，并且入队成功，返回**ACK**，告知投递成功
- 持久消息投递到了`MQ`，并且入队完成持久化，返回**ACK**，告知投递成功
- 其它情况都会返回**NACK**，告知投递失败



其中`ack`和`nack`属于**Publisher Confirm**机制，`ack`是投递成功；`nack`是投递失败。而`return`则属于**Publisher Return**机制。默认两种机制都是关闭状态，需要通过配置文件来开启。





#### 1.3 实现生产者确认机制

**1、开启生产者确认**

```yml
spring:
  rabbitmq:
    publisher-returns: true # 开启publisher return机制
    publisher-confirm-type: correlated   # 开启publisher confirm机制，并设置confirm类型
```

这里`publisher-confirm-type`有三种模式可选：

- `none`：关闭confirm机制
- `simple`：同步阻塞等待`MQ`的回执
- `correlated`：MQ异步回调返回回执，即生产者发送完消息后就结束了，等回调消息后续通知

一般我们推荐使用`correlated`，回调机制。





**2、定义ReturnCallback**

每个`RabbitTemplate`只能配置一个`ReturnCallback`，因此我们可以在配置类中统一设置。对ioc容器中的`RabbitTemplate`设置`ReturnsCallback`，打印异常信息。

```java
@Slf4j
@Configuration
public class MqConfig {
    private final RabbitTemplate rabbitTemplate;

    public MqConfig(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    @PostConstruct
    public void init() {
        rabbitTemplate.setReturnsCallback(new RabbitTemplate.ReturnsCallback() {
            @Override
            public void returnedMessage(ReturnedMessage returnedMessage) {
                log.error("触发return callback,");
                log.debug("exchange: {}", returnedMessage.getExchange());
                log.debug("routingKey: {}", returnedMessage.getRoutingKey());
                log.debug("message: {}", returnedMessage.getMessage());
                log.debug("replyCode: {}", returnedMessage.getReplyCode());
                log.debug("replyText: {}", returnedMessage.getReplyText());
            }
        });
    }
}

// 也可以是如下写法，实现ApplicationContextAware接口，那么启动后，就可以使用ApplicationContext来获取bean
@Slf4j
@Configuration
public class MqConfig1 implements ApplicationContextAware {
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        RabbitTemplate rabbitTemplate = applicationContext.getBean(RabbitTemplate.class);
        rabbitTemplate.setReturnsCallback(new RabbitTemplate.ReturnsCallback() {
            @Override
            public void returnedMessage(ReturnedMessage returnedMessage) {
                log.error("触发return callback,");
                log.debug("exchange: {}", returnedMessage.getExchange());
                log.debug("routingKey: {}", returnedMessage.getRoutingKey());
                log.debug("message: {}", returnedMessage.getMessage());
                log.debug("replyCode: {}", returnedMessage.getReplyCode());
                log.debug("replyText: {}", returnedMessage.getReplyText());
            }
        });
    }
}
```





**3、定义ConfirmCallback**

由于每个消息发送时的处理逻辑不一定相同，因此`ConfirmCallback`需要在每次发消息时定义。具体来说，是在调用`RabbitTemplate`中的`convertAndSend`方法时，多传递一个参数`CorrelationData`

这里的`CorrelationData`中包含两个核心的东西：

- `id`：消息的唯一标示，`MQ`对不同的消息的回执以此做判断，避免混淆，这个id主要是用来做ack确认的
- `SettableListenableFuture`：回执结果的Future对象，将来`MQ`的回执就会通过这个`Future`来返回，我们可以提前给`CorrelationData`中的`Future`添加回调函数来处理消息回执

```JAVA
public void convertAndSend(String exchange, String routingKey, Object object, @Nullable CorrelationData correlationData) throws AmqpException {
    this.send(exchange, routingKey, this.convertMessageIfNecessary(object), correlationData);
}
```



**测试**

> 1、正常情况下，发送消息成功收到ack确认
>
> 2、假如我们的`routing key`写错了，那么也会返回ack确认【因为消息收到了，只是没有队列能接收】，同时会收到`ReturnCallback`的异常信息
>
> 3、假如我们的交换机name写错了，那么就会收到nack，因为消息发送失败

```java
@Test
public void testConfirm() {
    // 1.创建CorrelationData，并设置id
    CorrelationData cd = new CorrelationData(UUID.randomUUID().toString());
    // 2.给Future添加ConfirmCallback
    cd.getFuture().addCallback(new ListenableFutureCallback<CorrelationData.Confirm>() {
        @Override
        public void onFailure(Throwable ex) {
            // 2.1.Future发生异常时的处理逻辑，即回调失败，基本不会触发
            log.error("send message fail", ex);
        }

        @Override
        public void onSuccess(CorrelationData.Confirm result) {
            // 2.2.Future接收到回执的处理逻辑，参数中的result就是回执内容
            if(result.isAck()){ // result.isAck()，boolean类型，true代表ack回执，false 代表 nack回执
                log.debug("发送消息成功，收到 ack!");
            }else{ // result.getReason()，String类型，返回nack时的异常描述
                log.error("发送消息失败，收到 nack, reason : {}", result.getReason());
            }
        }
    });

    // 3.发送消息
    String directName = "xyy.direct";
    String msg = "hello red";
    rabbitTemplate.convertAndSend(directName, "red", msg, cd);
}
```



**注意**：

开启生产者确认比较消耗`MQ`性能，一般不建议开启。

- 路由失败：一般是因为`RoutingKey`错误导致，往往是编程导致
- 交换机名称错误：同样是编程错误导致
- `MQ`内部故障：这种需要处理，但概率往往较低。因此只有对消息可靠性要求非常高的业务才需要开启，而且仅仅需要开启`ConfirmCallback`处理`nack`就可以了。





### 2、MQ的可靠性

默认情况下，`RabbitMQ`会将接收到的消息保存在内存中以降低消息收发的延迟。这样会导致两个问题

- 一旦MQ宕机，内存中的消息会丢失
- 内存空间有限，当消费者故障或处理过慢时，会导致消息积压，直到触发内存预警上限，此时`RabbitMQ`会将内存消息刷到磁盘上，这个行为称为`PageOut`. `PageOut`会耗费一段时间，并且会阻塞队列进程。因此在这个过程中`RabbitMQ`不会再处理新的消息，生产者的所有请求都会被阻塞。



#### 2.1 数据持久化

为了提升性能，默认情况下MQ的数据都是在内存存储的临时数据，重启后就会消失。为了保证数据的可靠性，必须配置数据持久化，包括：

- 交换机持久化
- 队列持久化
- 消息持久化

我们使用Java代码创建的交换机和队列，默认都是持久化的。在控制台创建时，`Durability`属性指定为`Durable`就是持久化的，`Transient`是非持久化的



在控制台发送消息时，如果消息类型`Delivery mode`为`1-Non-persistent`那么消息就是非持久化的，会保存在内存中，当内存满了后，会触发`PageOut`操作，导致`MQ`阻塞；如果消息类型是`2-Persistent`那么就是持久化的，会直接将消息写入磁盘，同时也会写入内存，当内存满了后，就会直接清空内存，不会出现`PageOut`操作，因此`MQ`不会阻塞。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310191808765.png)







#### 2.2 LazyQueue

从`RabbitMQ`的3.6.0版本开始，就增加了`Lazy Queues`的模式，也就是惰性队列。

而在3.12版本之后，`LazyQueue`已经成为所有队列的默认格式。因此官方推荐升级`MQ`为3.12版本或者所有队列都设置为`LazyQueue`模式。

惰性队列的特征如下：

- 接收到消息后直接存入磁盘而非内存【内存只保留最近的消息，默认2048条】
- 消费者要消费消息时才会从磁盘中读取并加载到内存（也就是懒加载）
- 支持数百万条的消息存储



**控制台配置lazy模式**

> 在添加队列时，添加`x-queue-mod=lazy`参数即可设置队列为Lazy模式

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310191819055.png)





**代码配置Lazy模式**

在利用`SpringAMQP`声明队列的时候，添加`x-queue-mod=lazy`参数也可设置队列为Lazy模式：

```java
@Bean
public Queue lazyQueue(){
    return QueueBuilder
            .durable("lazy.queue")
            .lazy() // 开启Lazy模式
            .build();
}
```

通过`QueueBuilder`的`lazy()`配置Lazy模式，底层源码就是设置的`x-queue-mod=lazy`参数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310191826425.png)





基于注解来声明队列并设置为Lazy模式

```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(value = "fanout.queue1", durable = "true", arguments = @Argument(name = "x-queue-mode", value = "lazy")),
    exchange = @Exchange(value = "xyy.fanout", type = ExchangeTypes.FANOUT)
))
public void fanoutQueueMessage1(String msg) {
    System.out.println("fanout1 消费者接收到消息：" + msg);
}
```







### 3、消费者的可靠性

当`RabbitMQ`向消费者投递消息以后，需要知道消费者的处理状态如何。因为消息投递给消费者并不代表就一定被正确消费了，可能出现的故障有很多，比如：

- 消息投递的过程中出现了网络故障
- 消费者接收到消息后突然宕机
- 消费者接收到消息后，因处理不当导致异常

一旦发生上述情况，消息也会丢失。因此，`RabbitMQ`必须知道消费者的处理状态，一旦消息处理失败才能重新投递消息。





#### 3.1 消费者确认机制

为了确认消费者是否成功处理消息，`RabbitMQ`提供了消费者确认机制（**Consumer Acknowledgement**）。即：当消费者处理消息结束后，应该向`RabbitMQ`发送一个回执，告知`RabbitMQ`自己消息处理状态。回执有三种可选值：

- **ack**：成功处理消息，`RabbitMQ`从队列中删除该消息
- **nack**：消息处理失败，`RabbitMQ`需要再次投递消息
- **reject**：消息处理失败并拒绝该消息，`RabbitMQ`从队列中删除该消息



> 一般reject方式用的较少，除非是消息格式有问题，那就是开发问题了。因此大多数情况下我们需要将消息处理的代码通过`try catch`机制捕获，消息处理成功时返回ack，处理失败时返回nack.



由于消息回执的处理代码比较统一，因此`SpringAMQP`帮我们实现了消息确认。并允许我们通过配置文件设置ACK处理方式，有三种模式：

- **none**：不处理。即消息投递给消费者后立刻ack，消息会立刻从`MQ`删除。非常不安全，不建议使用
- **manual**：手动模式。需要自己在业务代码中调用api，发送`ack`或`reject`，存在业务入侵，但更灵活
- **auto**：自动模式。`SpringAMQP`利用`AOP`对我们的消息处理逻辑做了环绕增强，当业务正常执行时则自动返回`ack`；当业务出现异常时，根据异常判断返回不同结果：
  - 如果是**业务异常**，会自动返回`nack`；
  - 如果是**消息处理或校验异常**，自动返回`reject`;



> 1、当我们设置为none时，消费者接收到消息后，`MQ`立刻将消息删除，不管消费者是否发生异常
>
> 2、当我们设置为auto时，消费者接收到消息后，如果正常执行完毕，给`MQ`发送一个ack，此时`MQ`将消息删除；如果消费者业务异常，返回一个nack，此时`MQ`重新投递消息；如果消息处理或转换异常，那么消费者返回一个reject，同时`MQ`删除消息。

```yml
spring:
  rabbitmq:
    host: 192.168.198.130
    port: 5672
    virtual-host: /xyy
    username: xyy
    password: 990918
    listener:
      simple:
        prefetch: 1 # 消费者每次只能获取一条消息，处理完成才能获取下一个消息
        acknowledge-mode: auto	# 设置ack处理方式
```





#### 3.2 失败重试机制

> 当消费者出现异常后，消息会不断requeue（重入队）到队列，再重新发送给消费者。如果消费者再次执行依然出错，消息会再次requeue到队列，再次投递，直到消息处理成功为止。
>
> 极端情况就是消费者一直无法执行成功，那么消息`requeue`就会无限循环，导致`MQ`的消息处理飙升，带来不必要的压力



为了应对上述情况Spring又提供了消费者失败重试机制：在消费者出现异常时利用**本地重试**，而不是无限制的`requeue`到`MQ`队列。

在消费者的配置文件中配置如下内容

```yml
spring:
  rabbitmq:
    host: 192.168.198.130
    port: 5672
    virtual-host: /xyy
    username: xyy
    password: 990918
    listener:
      simple:
        prefetch: 1 # 消费者每次只能获取一条消息，处理完成才能获取下一个消息
        acknowledge-mode: auto
        retry:
          enabled: true # 开启消费者失败重试
          initial-interval: 1000ms # 初始的失败等待时长为1秒
          multiplier: 1 # 失败的等待时长倍数，下次等待时长 = multiplier * last-interval
          max-attempts: 3 # 最大重试次数
          stateless: true # true无状态；false有状态。如果业务中包含事务，这里改为false
```



重启`consumer`服务，重复之前的测试。可以发现：

- 消费者在失败后消息没有重新回到`MQ`无限重新投递，而是在本地重试了3次
- 本地重试3次以后，抛出了`AmqpRejectAndDontRequeueException`异常。查看`RabbitMQ`控制台，发现消息被删除了，说明最后`SpringAMQP`返回的是`reject`

结论：

- 开启本地重试时，消息处理过程中抛出异常，不会requeue到队列，而是在消费者本地重试
- 重试达到最大次数后，Spring会返回reject，消息会被丢弃





#### 3.3 失败处理策略

在之前的测试中，本地测试达到最大重试次数后，消息会被丢弃。这在某些对于消息可靠性要求较高的业务场景下，不太合适。

因此Spring允许我们自定义重试次数耗尽后的消息处理策略，这个策略是由`MessageRecovery`接口来定义的，它有3个不同实现：

-  `RejectAndDontRequeueRecoverer`：重试耗尽后，直接`reject`，丢弃消息。默认就是这种方式 
-  `ImmediateRequeueMessageRecoverer`：重试耗尽后，返回`nack`，消息重新入队 
-  `RepublishMessageRecoverer`：重试耗尽后，将失败消息投递到指定的交换机，交给人工处理



**`RepublishMessageRecoverer`**

在消费者中定义错误处理的交换机、队列、`RepublishMessageRecoverer`，即当消费者处理消息失败后，在本地重试，重试次数耗尽消息也没有处理成功，那么就将消息发送到`error.direct`这个交换机，这个交换机会将消息投递到`error.queue`队列，等待人工处理。

```java
@Configuration
public class ErrorConfig {
    @Bean
    public DirectExchange errorExchange() {
        return new DirectExchange("error.direct");
    }

    @Bean
    public Queue errorQueue() {
        return new Queue("error.queue");
    }

    @Bean
    public Binding errorBinding(){
        return BindingBuilder.bind(errorQueue()).to(errorExchange()).with("error");
    }

    @Bean
    public MessageRecoverer errorMessageRecoverer(RabbitTemplate rabbitTemplate) {
        return new RepublishMessageRecoverer(rabbitTemplate, "error.direct", "error");
    }
}
```







#### 3.4 业务幂等性

**幂等**是一个数学概念，用函数表达式来描述是这样的：`f(x) = f(f(x))`，例如求绝对值函数。

在程序开发中，则是指同一个业务，执行一次或多次对业务状态的影响是一致的。例如：

- 根据id删除数据
- 查询数据
- 新增数据

但数据的更新往往不是幂等的，如果重复执行可能造成不一样的后果。比如：

- 取消订单，恢复库存的业务。如果多次恢复就会出现库存重复增加的情况
- 退款业务。重复退款对商家而言会有经济损失。



所以，我们要尽可能避免业务被重复执行。然而在实际业务场景中，由于意外经常会出现业务被重复执行的情况，例如：

- 页面卡顿时频繁刷新导致表单重复提交
- 服务间调用的重试
- MQ消息的重复投递





我们在用户支付成功后会发送`MQ`消息到交易服务，修改订单状态为已支付，就可能出现消息重复投递的情况。如果消费者不做判断，很有可能导致消息被消费多次，出现业务故障。

> 例如：
>
> 1. 假如用户刚刚支付完成，并且投递消息到交易服务，交易服务更改订单为**已支付**状态。
> 2. 由于某种原因，例如网络故障导致生产者没有得到确认，隔了一段时间后**重新投递**给交易服务。
> 3. 但是，在新投递的消息被消费之前，用户选择了退款，将订单状态改为了**已退款**状态。
> 4. 退款完成后，新投递的消息才被消费，那么订单状态会被再次改为**已支付**。业务异常。

因此，我们必须想办法保证消息处理的幂等性。



##### 1、唯一消息ID

对于表单重复提交，我们就可以设置一个id来避免。

> 在进入表单之前，服务端向前端发送一个token，同时服务端将这个token保存到`redis`中，表单提交时需要携带这个token，表单提交的请求进来后，需要判断`redis`中是否有这个token，如果有那么就放行，同时从`redis`中删除这个token。如果`redis`中没有这个token，那么就拦截该请求。



对于消息的重复消费

- 每一条消息都生成一个唯一的id，与消息一起投递给消费者。

- 消费者接收到消息后处理自己的业务，业务处理成功后将消息ID保存到数据库

- 如果下次又收到相同消息，去数据库查询判断是否存在，存在则为重复消息放弃处理。



对于消息的id设置，我们在配置消息转换器时，可以将设置消息id的功能打开，这样每次发送的消息都会带有消息id

```java
@Configuration
public class MessageConfig {
    @Bean
    public MessageConverter messageConverter(){
        Jackson2JsonMessageConverter messageConverter = new Jackson2JsonMessageConverter();
        // 自动创建消息id，用于识别不同消息，也可以在业务中基于ID判断是否是重复消息
        messageConverter.setCreateMessageIds(true);
        return messageConverter;
    }
}
```



**源码分析**

> 发送消息执行`convertAndSend`时，会先使用消息转换器，对消息进行转换，在`toMessage`方法中，在`createMessage`之后，会通过`if (this.createMessageIds && messageProperties.getMessageId() == null) `判断，如果当前设置开启了创建消息id，并且当前消息id为空，那么就随机生成一个消息id。

```java
public final Message toMessage(Object object, @Nullable MessageProperties messagePropertiesArg, @Nullable Type genericType) throws MessageConversionException {
    MessageProperties messageProperties = messagePropertiesArg;
    if (messagePropertiesArg == null) {
        messageProperties = new MessageProperties();
    }

    Message message = this.createMessage(object, messageProperties, genericType);
    messageProperties = message.getMessageProperties();
    if (this.createMessageIds && messageProperties.getMessageId() == null) {
        messageProperties.setMessageId(UUID.randomUUID().toString());
    }

    return message;
}
```







##### 2、业务状态判断

1、业务判断就是基于业务本身的逻辑或状态来判断是否是重复的请求或消息，不同的业务场景判断的思路也不一样。

2、例如当前案例中，处理消息的业务逻辑是把订单状态从未支付修改为已支付。因此我们就可以在执行业务时判断订单状态是否是未支付，如果不是则证明订单已经被处理过，无需重复处理，直接return掉即可。

3、这样在消费者执行修改订单状态的业务流程就是：根据id查询当前订单，判断订单状态，如果不是未支付状态，直接return掉，否则去修改订单状态。

4、但是如果在高并发场景下，消费者同时收到两条修改订单状态的消息，同时判断是未支付状态，这样又会造成消息的重复消费。因此可以基于乐观锁的机制，我们修改订单状态的sql语句修改为`where id = ? and state = ?`，根据订单id修改支付状态时，同时需要判断当前状态是未支付时才可以修改。

相比较而言，消息ID的方案需要改造原有的数据库，而根据业务状态判断则不需要。





##### 3、兜底方案

1、虽然我们利用各种机制尽可能增加了消息的可靠性，但也不好说能保证消息100%的可靠。万一真的`MQ`通知失败，我们还需要一种兜底方案：既然`MQ`通知不一定发送到交易服务，那么交易服务就必须自己**主动去查询**支付状态。这样即便支付服务的`MQ`通知失败，我们依然能通过主动查询来保证订单状态的一致

2、通常我们采取的措施就是利用**定时任务**定期查询，例如每隔20秒就查询一次，并判断支付状态。如果发现订单已经支付，则立刻更新订单状态为已支付即可。





##### 4、总结

支付服务与交易服务之间的订单状态一致性是如何保证的？

- 首先，支付服务会正在用户支付成功以后利用`MQ`消息通知交易服务，完成订单状态同步。
- 其次，为了保证`MQ`消息的可靠性，我们采用了生产者确认机制、消费者确认、消费者失败重试等策略，确保消息投递的可靠性。同时开启了`MQ`的持久化，避免因服务宕机导致消息丢失。
- 接着，我们还在交易服务更新订单状态时，做了幂等判断，避免因消息重复消费导致订单状态异常。
- 最后，我们还在交易服务设置了定时任务，定期查询订单支付状态。这样即便`MQ`通知失败，还可以利用定时任务作为兜底方案，确保订单支付状态的最终一致性。









### 4、延迟消息

> 在电商的支付业务中，对于一些库存有限的商品，为了更好的用户体验，通常都会在用户下单时立刻扣减商品库存。例如电影院购票、高铁购票，下单后就会锁定座位资源，其他人无法重复购买。
>
> 但是这样就存在一个问题，假如用户下单后一直不付款，就会一直占有库存资源，导致其他客户无法正常交易，最终导致商户利益受损。因此，电商中通常的做法就是：**对于超过一定时间未支付的订单，应该立刻取消订单并释放占用的库存**。像这种在一段时间以后才执行的任务，我们称之为**延迟任务**，而要实现延迟任务，最简单的方案就是利用`MQ`的延迟消息。



**`RabbitMQ`中实现延迟消息的两种方案**

- 死信交换机+TTL
- 延迟消息插件



#### 4.1 死信交换机

当一个队列中的消息满足下列情况之一时，可以成为死信（dead letter）：

- 消费者使用`basic.reject`或 `basic.nack`声明消费失败，并且消息的`requeue`参数设置为false
- 消息是一个过期消息，超时无人消费
- 要投递的队列消息满了，无法投递

如果一个队列中的消息已经成为死信，并且这个队列通过`dead-letter-exchange`属性指定了一个交换机，那么队列中的死信就会投递到这个交换机中，而这个交换机就称为**死信交换机**（Dead Letter Exchange）。而此时将队列与死信交换机绑定，则最终死信就会被投递到这个队列中。



如图，有一组绑定的交换机（`ttl.fanout`）和队列（`ttl.queue`）。但是`ttl.queue`没有消费者监听，而是设定了死信交换机`hmall.direct`，而队列`direct.queue1`则与死信交换机绑定，`RoutingKey`是`blue`，此时生产者发送消息到`ttl.fanout`，同时带上过期时间，因为`ttl.queue`没有消费者，所以消息会过期，过期后消息被投递到`hmall.direct`，然后发送到`direct.queue1`中被消费者消费。这样就实现了延迟消息

<img src="https://cdn.nlark.com/yuque/0/2023/png/27967491/1687573175803-41a1c870-93bc-4307-974f-891de1b5a42d.png#averageHue=%23faf3f2&clientId=u76b62a19-f8dc-4&from=paste&height=340&id=u380f1403&originHeight=422&originWidth=1301&originalType=binary&ratio=1.2395833730697632&rotation=0&showTitle=false&size=59423&status=done&style=none&taskId=ucbcde27e-d210-43e8-8e35-7557d121729&title=&width=1049.546184842849" alt="image.png" style="zoom:67%;" />







我们创建交换机`ttl.direct`绑定队列`ttl.queue`，创建`ttl.queue`时通过`dead-letter-exchange`属性绑定交换机`dlq.direct`，该队列没有消费者。创建交换机`dlq.direct`绑定队列`dlp.queue`，有消费者监听

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202310221540807.png)

**生产者**

> 发送一个ttl为10s的消息到`ttl.direct`，此时消息被投递到`ttl.queue`中，因为没有消费者，所以这条消息变为死信，发送到`dlq.direct`下

```java
@Test
public void testTTL() {
    String ttlName = "ttl.direct";
    rabbitTemplate.convertAndSend(ttlName, "blue", "hello", new MessagePostProcessor() {
        @Override
        public Message postProcessMessage(Message message) throws AmqpException {
            message.getMessageProperties().setExpiration("10000");
            return message;
        }
    });
    log.info("消息发送成功");
}
```



**消费者**

> 在ttl到期后，`dlq.direct`会收到死信消息，消息会投递到`dlq.queue`，此时被消费者消费，实现了延迟消息。

```java
@RabbitListener(queues = "dlq.queue")
public void ttlQueueMessage(String msg) {
    log.info("object 消费者接收到消息：" + msg);
}
```





#### 4.2 DelayExchange插件

`RabbitMQ`提供了一个延迟消息插件来实现延迟消息的功能，[插件下载地址](https://github.com/rabbitmq/rabbitmq-delayed-message-exchange)



**安装**

1、因为我们是基于Docker安装，所以需要先查看`RabbitMQ`的插件目录对应的数据卷

```shell
[
    {
        "CreatedAt": "2023-10-17T14:58:33+06:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mq-plugins/_data",
        "Name": "mq-plugins",
        "Options": null,
        "Scope": "local"
    }
]
```

2、插件目录被挂载到了`/var/lib/docker/volumes/mq-plugins/_data`这个目录，我们上传插件到该目录下。

3、接下来执行命令，安装插件：

```shell
docker exec -it mq rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```



**声明延迟交换机**

- 基于注解方式：

```java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "delay.queue", durable = "true"),
        exchange = @Exchange(name = "delay.direct", delayed = "true"),
        key = "delay"
))
public void listenDelayMessage(String msg){
    log.info("接收到delay.queue的延迟消息：{}", msg);
}
```

- 基于Bean方式

```java
@Bean
public DirectExchange delayExchange(){
    return ExchangeBuilder
        .directExchange("delay.direct") // 指定交换机类型和名称
        .delayed() // 设置delay的属性为true
        .durable(true) // 持久化
        .build();
}
```



**发送消息**

```java
@Test
public void testDelay() {
    String ttlName = "delay.direct";
    rabbitTemplate.convertAndSend(ttlName, "blue", "hello", new MessagePostProcessor() {
        @Override
        public Message postProcessMessage(Message message) throws AmqpException {
            message.getMessageProperties().setDelay(10000);
            return message;
        }
    });
    log.info("消息发送成功");
}
```



**接收消息**

```java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(value = "delay.queue", durable = "true"),
        exchange = @Exchange(value = "delay.direct", delayed = "true"),
        key = "blue"
))
public void delayMessage(String msg) {
    log.info("接收到延迟消息, " + msg);
}
```





**注意：**延迟消息插件内部会维护一个本地数据库表，同时使用Elang Timers功能实现计时。如果消息的延迟时间设置较长，可能会导致堆积的延迟消息非常多，会带来较大的CPU开销，同时延迟消息的时间会存在误差。因此，**不建议设置延迟时间过长的延迟消息**。







#### 4.3 订单状态同步问题

> 当前业务为用户下单后，扣减库存，如果用户30分钟内支付订单，那么就创建订单。如果用户超过30分钟未支付，那么恢复库存。
>
> 1、订单超时支付时间为30分钟，理论上说我们应该在下单时发送一条延迟消息，延迟时间为30分钟。这样就可以在接收到消息时检验订单支付状态，关闭未支付订单。
>
> 2、但是大多数情况下用户支付都会在1分钟内完成，我们发送的消息却要在`MQ`中停留30分钟，额外消耗了`MQ`的资源。因此，我们最好多检测几次订单支付状态，而不是在最后第30分钟才检测。例如：我们在用户下单后的第10秒、20秒、30秒、45秒、60秒、1分30秒、2分、...30分分别设置延迟消息，如果提前发现订单已经支付，则后续的检测取消即可。这样就可以有效避免对`MQ`资源的浪费了。



<img src="https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1687593452790-58e296b7-0761-40f6-b4be-9c19bff9cd3e.jpeg" style="zoom:50%;" />



**业务流程**

1、用户下单后，发送一个`MQ`延迟消息，消息内容就是订单id。同时定义一个延迟时间数组，每次取出数组第一个元素并删除，用这个值作为当前延迟时间

2、消费者监听这个队列，拿到消息后，就获取到了订单id，去数据库中通过id查询该订单【用户支付后，会调用支付服务去修改订单状态】，如果订单是已支付状态，那么直接return即可

3、如果未支付，那么判断是否还有延迟时间，如果有，那么取出数组第一个元素作为延迟时间，再次发送延迟消息

4、如果没有延迟时间了，说明30分钟内用户未支付，那么将订单状态改为未支付，同时恢复库存







### 5、MQ集群

> `RabbitMQ`的是基于Erlang语言编写，而Erlang又是一个面向并发的语言，天然支持集群模式。`RabbitMQ`的集群有两种模式
>
> - **普通集群**：是一种分布式集群，将队列分散到集群的各个节点，从而提高整个集群的并发能力。但是如果有一个集群出现故障，那么该集群上的全部队列以及消息就会丢失
>
> - **镜像集群**：是一种主从集群，普通集群的基础上，添加了主从备份功能，提高集群的数据可用性。但主从同步并不是强一致的，某些情况下可能有数据丢失的风险。因此在`RabbitMQ`的3.8版本以后，推出了新的功能：**仲裁队列**来代替镜像集群，底层采用Raft协议确保主从的数据一致性。





#### 5.1 普通集群

普通集群，或者叫标准集群（classic cluster）

- 会在集群的各个节点间共享部分数据，包括：交换机、队列元信息。不包含队列中的消息
- 当访问集群某节点时，如果队列不在该节点，会从数据所在节点传递到当前节点并返回【因为每个节点会共享队列的元信息】
- 队列所在节点宕机，队列中的消息就会丢失

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141744938.png" style="zoom:80%;" />

消费者监听`test.queue1`，但是访问到了第三个节点上，因为第三个节点有`test.queue1`的元信息，因此会去第一个节点的这个队列取数据并返回给消费者





#### 5.2 普通集群部署

这里使用docker启动三个MQ镜像来模拟集群

| 主机名 | 控制台端口      | amqp通信端口    |
| ------ | --------------- | --------------- |
| mq1    | 8081 ---> 15672 | 8071 ---> 5672  |
| mq2    | 8082 ---> 15672 | 8072 ---> 5672  |
| mq3    | 8083 ---> 15672 | 8073  ---> 5672 |

集群中的节点标示默认都是：`rabbit@[hostname]`，因此以上三个节点的名称分别为：

- rabbit@mq1
- rabbit@mq2
- rabbit@mq3



**1、获取cookie**

RabbitMQ底层依赖于Erlang，而Erlang虚拟机就是一个面向分布式的语言，默认就支持集群模式。集群模式中的每个RabbitMQ 节点使用 cookie 来确定它们是否被允许相互通信。

要使两个节点能够通信，它们必须具有相同的共享密钥，称为**Erlang cookie**。cookie 只是一串最多 255 个字符的字母数字字符。

每个集群节点必须具有**相同的 cookie**。实例之间也需要它来相互通信。



获取一个启动的MQ的cookie

```sh
docker exec -it mq cat /var/lib/rabbitmq/.erlang.cookie
```



**2、准备集群配置**

在/tmp目录新建一个配置文件 rabbitmq.conf：

```nginx
loopback_users.guest = false
listeners.tcp.default = 5672
cluster_formation.peer_discovery_backend = rabbit_peer_discovery_classic_config
cluster_formation.classic_config.nodes.1 = rabbit@mq1
cluster_formation.classic_config.nodes.2 = rabbit@mq2
cluster_formation.classic_config.nodes.3 = rabbit@mq3
```



再创建一个文件，记录cookie

```sh
# 创建cookie文件
touch .erlang.cookie
# 写入cookie
echo "FXZMCVGLBIXZCDEMMVZQ" > .erlang.cookie
# 修改cookie文件的权限
chmod 600 .erlang.cookie
```



准备三个目录,mq1、mq2、mq3，然后拷贝rabbitmq.conf、cookie文件到mq1、mq2、mq3：

```sh
cp rabbitmq.conf mq1
cp rabbitmq.conf mq2
cp rabbitmq.conf mq3
cp .erlang.cookie mq1
cp .erlang.cookie mq2
cp .erlang.cookie mq3
```



**3、启动集群**

创建一个网络

```sh
docker network create mq-net
```



运行命令，分别通过mq1、mq2、mq3下的三个配置文件启动三个MQ实例

```sh
docker run -d --net mq-net \
-v ${PWD}/mq1/rabbitmq.conf:/etc/rabbitmq/rabbitmq.conf \
-v ${PWD}/.erlang.cookie:/var/lib/rabbitmq/.erlang.cookie \
-e RABBITMQ_DEFAULT_USER=itcast \
-e RABBITMQ_DEFAULT_PASS=123321 \
--name mq1 \
--hostname mq1 \
-p 8071:5672 \
-p 8081:15672 \
rabbitmq:3.8-management
```



**4、测试**

在mq1上创建一个队列

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141752081.png" style="zoom:65%;" />

在mq2和mq3上也可以看到这个队列，可以看到他是在`rabbit@mq1`这个节点上

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141752047.png" style="zoom:80%;" />

我们向mq1的这个队列发送一条消息，我们在mq2和mq3上都可以看到并获取这条消息，但是如果mq1节点故障了，此时mq2和mq3就看不到mq1上的队列以及消息了







#### 5.3 镜像集群

镜像集群：本质是主从模式

- 交换机、队列、队列中的消息会在各个mq的镜像节点之间同步备份。
- 创建队列的节点被称为该队列的**主节点，**备份到的其它节点叫做该队列的**镜像**节点。
- 一个队列的主节点可能是另一个队列的镜像节点【类似于ES的集群】
- 所有操作都是主节点完成，然后同步给镜像节点，当主节点接收到消费者的ACK时，所有镜像都会删除节点中的数据
- 主宕机后，镜像节点会替代成新的主

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141806682.png" style="zoom:80%;" />





镜像模式的配置

| ha-mode         | ha-params         | 效果                                                         |
| :-------------- | :---------------- | :----------------------------------------------------------- |
| 准确模式exactly | 队列的副本量count | 集群中队列副本（主服务器和镜像服务器之和）的数量。count如果为1意味着单个副本：即队列主节点。count值为2表示2个副本：1个队列主和1个队列镜像。换句话说：count = 镜像数量 + 1。如果群集中的节点数少于count，则该队列将镜像到所有节点。如果有集群总数大于count+1，并且包含镜像的节点出现故障，则将在另一个节点上创建一个新的镜像。 |
| all             | (none)            | 队列在群集中的所有节点之间进行镜像。队列将镜像到任何新加入的节点。镜像到所有节点将对所有群集节点施加额外的压力，包括网络I / O，磁盘I / O和磁盘空间使用情况。推荐使用exactly，设置副本数为（N / 2 +1）。 |
| nodes           | node names        | 指定队列创建到哪些节点，如果指定的节点全部不存在，则会出现异常。如果指定的节点在集群中存在，但是暂时不可用，会创建节点到当前客户端连接到的节点。 |



**exactly模式**

```
rabbitmqctl set_policy ha-two "^two\." '{"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}'
```

- `rabbitmqctl set_policy`：固定写法
- `ha-two`：策略名称，自定义
- `"^two\."`：匹配队列的正则表达式，符合命名规则的队列才生效，这里是任何以`two.`开头的队列名称
- `'{"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}'`: 策略内容
  - `"ha-mode":"exactly"`：策略模式，此处是exactly模式，指定副本数量
  - `"ha-params":2`：策略参数，这里是2，就是副本数量为2，1主1镜像
  - `"ha-sync-mode":"automatic"`：同步策略，默认是manual，即新加入的镜像节点不会同步旧的消息。如果设置为automatic，则新加入的镜像节点会把主节点中所有消息都同步，会带来额外的网络开销

**all模式**

```
rabbitmqctl set_policy ha-all "^all\." '{"ha-mode":"all"}'
```

- `ha-all`：策略名称，自定义
- `"^all\."`：匹配所有以`all.`开头的队列名
- `'{"ha-mode":"all"}'`：策略内容
  - `"ha-mode":"all"`：策略模式，此处是all模式，即所有节点都会称为镜像节点

**nodes模式**

```
rabbitmqctl set_policy ha-nodes "^nodes\." '{"ha-mode":"nodes","ha-params":["rabbit@nodeA", "rabbit@nodeB"]}'
```

- `rabbitmqctl set_policy`：固定写法
- `ha-nodes`：策略名称，自定义
- `"^nodes\."`：匹配队列的正则表达式，符合命名规则的队列才生效，这里是任何以`nodes.`开头的队列名称
- `'{"ha-mode":"nodes","ha-params":["rabbit@nodeA", "rabbit@nodeB"]}'`: 策略内容
  - `"ha-mode":"nodes"`：策略模式，此处是nodes模式
  - `"ha-params":["rabbit@mq1", "rabbit@mq2"]`：策略参数，这里指定副本所在节点名称





我们进入上边搭建的普通集群中的任意一个节点，执行命令，假设进入mq1这个节点执行

```sh
docker exec -it mq1 
rabbitmqctl set_policy ha-two "^two\." '{"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}'
```

> 此时在mq1中创建一个以`two`开头的队列`two.queue`，那么他还会创建一个镜像队列，此时查看mq2可以发现，他这里也有一个队列，是mq1的镜像。
>
> 现在我们让mq1宕机，此时mq2成为了`two.queue`的主节点，同时他会让mq3作为这个队列的镜像节点









#### 5.4 仲裁队列

仲裁队列是3.8版本以后才有的新功能，用来替代镜像队列，具备下列特征：

- 与镜像队列一样，都是主从模式，支持主从数据同步，默认1主4从
- 使用简单，没有复杂的配置
- 主从同步基于Raft协议，强一致



**添加仲裁队列**

在任意控制台添加一个队列，选择队列类型为Quorum类型。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141825053.png" style="zoom:80%;" />



在任意节点的控制台查看队列，因为现在只有3个节点，仲裁队列默认1主4从，所以会将另外两个从节点设为镜像节点

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312141826593.png" style="zoom:80%;" />





**Java代码创建仲裁队列**

```java
@Bean
public Queue quorumQueue() {
    return QueueBuilder
        .durable("quorum.queue") // 持久化
        .quorum() // 仲裁队列
        .build();
}
```



**SpringAMQP连接MQ集群**

这里通过`address`属性设置所有MQ节点的ip+端口即可

```yml
spring:
  rabbitmq:
    addresses: 192.168.150.105:8071, 192.168.150.105:8072, 192.168.150.105:8073
    username: xrj
    password: 123
    virtual-host: /
```

















