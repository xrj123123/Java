## 一、注册中心

### 1、微服务介绍

**1、单体架构**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221511877.png" style="zoom: 50%;" />

**优点：**

- 架构简单
- 部署成本低

**缺点：**

- 耦合度高（维护困难、升级困难）





**2、分布式架构**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221512052.png" style="zoom: 50%;" />

**优点：**

- 降低服务耦合
- 有利于服务升级和拓展

**缺点：**

- 服务调用关系错综复杂





**3、微服务**

微服务是在给分布式架构制定一个标准，进一步降低服务之间的耦合度，提供服务的独立性和灵活性。做到高内聚，低耦合。

因此，可以认为**微服务**是一种经过良好架构设计的**分布式架构方案** 。

微服务的架构特征：

- 单一职责：微服务拆分粒度更小，每一个服务都对应唯一的业务能力，做到单一职责
- 自治：团队独立、技术独立、数据独立，独立部署和交付
- 面向服务：服务提供统一标准的接口，与语言和技术无关
- 隔离性强：服务调用做好隔离、容错、降级，避免出现级联问题

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221514552.png" style="zoom:70%;" />





### 2、服务拆分和远程调用

微服务拆分的几个原则：

- 不同微服务，不要重复开发相同业务
- 微服务数据独立，不要访问其它微服务的数据库
- 微服务可以将自己的业务暴露为接口，供其它微服务调用

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221517566.png" style="zoom:60%;" />







**远程调用**

cloud-demo：父工程，管理依赖

- order-service：订单微服务，负责订单相关业务。使用8080端口，可以通过`/order/id`查询订单信息，但是订单信息中有用户的信息，我们不能像往常那样，调用用户的service方法，因为当前订单是单独的一个服务。

  ```java
  @GetMapping("{orderId}")
  public Order queryOrderByUserId(@PathVariable("orderId") Long orderId) {
      // 根据id查询订单并返回
      return orderService.queryOrderById(orderId);
  }
  
  public Order queryOrderById(Long orderId) {
      // 查询订单
      Order order = orderMapper.findById(orderId);
      // 返回，此时order中user信息为null
      return order;
  }
  ```

- user-service：用户微服务，负责用户相关业务。使用8081端口，可以通过`/user/id`查询用户信息

  ```java
  @GetMapping("/{id}")
  public User queryById(@PathVariable("id") Long id) {
      return userService.queryById(id);
  }
  
  public User queryById(Long id) {
      return userMapper.findById(id);
  }
  ```

  

我们现在查询订单信息时，返回的信息中用户信息为null，因此我们需要在order-service方法中，向user-service发起一个`http`的请求，调用`http://localhost:8081/user/{userId}`这个接口拿到user对象，然后返回，步骤如下：

- 注册一个`RestTemplate`的实例到Spring容器
- 修改order-service服务中的`OrderService`类中的`queryOrderById`方法，根据Order对象中的`userId`查询User
- 将查询的User填充到Order对象，一起返回



**注册`RestTemplate`**

```java
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```



**实现远程调用**

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;
    @Autowired
    private RestTemplate restTemplate;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        // 2.查询user
        // 2.1拼接url
        String url = "http://localhost:8081/user/" + order.getUserId();
        User user = restTemplate.getForObject(url, User.class);
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```





### 3、Eureka注册中心

> 我们上面实现远程调用是通过硬编码的方式实现的，但是这样会产生许多问题
>
> - order-service在发起远程调用的时候，该如何得知user-service实例的`IP`地址和端口？
> - 有多个user-service实例地址，order-service调用时该如何选择？
> - order-service如何得知某个user-service实例是否依然健康，是不是已经宕机？
>
> 因此就需要使用Eureka

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221546433.png" style="zoom:50%;" />



问题1：`order-service`如何得知`user-service`实例地址？

获取地址信息的流程如下：

- user-service服务实例启动后，将自己的信息注册到eureka-server（Eureka服务端）。这个叫服务注册
- eureka-server保存服务名称到服务实例地址列表的映射关系
- order-service根据服务名称，拉取实例地址列表。这个叫服务发现或服务拉取



问题2：`order-service`如何从多个`user-service`实例中选择具体的实例？

- order-service从实例列表中利用负载均衡算法选中一个实例地址
- 向该实例地址发起远程调用



问题3：`order-service`如何得知某个`user-service`实例是否依然健康，是不是已经宕机？

- user-service会每隔一段时间（默认30秒）向eureka-server发起请求，报告自己状态，称为心跳
- 当超过一定时间没有发送心跳时，eureka-server会认为微服务实例故障，将该实例从服务列表中剔除
- order-service拉取服务时，就能将故障实例排除了





#### 3.1 创建eureka-server服务

创建一个子工程eureka-server

**1、引入eureka依赖**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```



**2、编写启动类**

需要加上`@EnableEurekaServer`注解

```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```



**3、编写配置文件**

这里eureka将自己也配置进去了

```yml
server:
  port: 8888
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8888/eureka
```



**4、启动服务**

浏览器访问：`http://127.0.0.1:8888`，其中`Instances currently registered with Eureka`中就是Eureka管理的实例

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221713142.png)





#### 3.2 服务注册

**1、引入依赖**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```



**2、配置文件**

```yml
spring:
  application:
    name: userservice
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8888/eureka
```



**3、启动服务**

> 我们将`userservice`启动了两个实例，因此eureka中可以看到是有两个实例的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311221810280.png)







#### 3.3 服务发现

**1、引入依赖**

服务发现、服务注册统一都封装在eureka-client依赖，因此这一步与服务注册时一致。

在order-service的pom文件中，引入下面的eureka-client依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```



**2、配置文件**

服务发现也需要知道eureka地址，因此第二步与服务注册一致，都是配置eureka信息

```yml
spring:
  application:
    name: orderservice
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8888/eureka
```



**3、服务拉取**

修改访问的`url`路径，用**服务名**代替`ip`、端口：

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;
    @Autowired
    private RestTemplate restTemplate;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        // 2.查询user
        // 2.1拼接url
        String url = "http://userservice/user/" + order.getUserId();
        User user = restTemplate.getForObject(url, User.class);
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```



**4、负载均衡**

在order-service的`OrderApplication`中，给`RestTemplate`这个Bean添加一个`@LoadBalanced`注解，可以实现负载均衡

```java
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```





### 4、负载均衡原理

我们在`RestTemplate`上添加了`@LoadBalanced`注解后，就可以实现负载均衡，他的底层是使用了`Ribbon`组件来实现负载均衡的。

我们发送一个请求`http://userservice/user/1`，会被`LoadBalancerInterceptor`拦截到，它首先拿到对应的URI，然后拿到服务名去eureka注册中心拿到对应的服务列表，最后最后根据负载均衡规则选择一个服务执行我们的请求。



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311231559140.png" style="zoom:60%;" />



#### 4.1 源码分析

1、通过`restTemplate`发起请求后被`LoadBalancerInterceptor`拦截到，执行`intercept`方法

```java
@Override
public ClientHttpResponse intercept(final HttpRequest request, final byte[] body,
      final ClientHttpRequestExecution execution) throws IOException {
   // 拿到请求的uri
   final URI originalUri = request.getURI();
   // 通过uri拿到服务名
   String serviceName = originalUri.getHost();
   Assert.state(serviceName != null,
         "Request URI does not contain a valid hostname: " + originalUri);
   // 执行execute方法
   return this.loadBalancer.execute(serviceName,
         this.requestFactory.createRequest(request, body, execution));
}
```

```java
@Override
public <T> T execute(String serviceId, LoadBalancerRequest<T> request)
      throws IOException {
   return execute(serviceId, request, null);
}
```

2、执行`execute`方法，其中`serviceId`就是上一步拿到的服务名

```java
public <T> T execute(String serviceId, LoadBalancerRequest<T> request, Object hint)
      throws IOException {
   // 拿到loadBalancer，它的属性allServerList就是通过服务名去eureka拿到的服务列表
   ILoadBalancer loadBalancer = getLoadBalancer(serviceId);
   // 选择一个服务来执行这次请求
   Server server = getServer(loadBalancer, hint);
   if (server == null) {
      throw new IllegalStateException("No instances available for " + serviceId);
   }
   RibbonServer ribbonServer = new RibbonServer(serviceId, server,
         isSecure(server, serviceId),
         serverIntrospector(serviceId).getMetadata(server));

   return execute(serviceId, ribbonServer, request);
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311231558415.png)

3、执行`loadBalancer.chooseServer(hint != null ? hint : "default");` 因为`hint`属性为null，所以进入`chooseServer`方法后，直接就执行第一个if

```java
protected Server getServer(ILoadBalancer loadBalancer, Object hint) {
   if (loadBalancer == null) {
      return null;
   }
   // Use 'default' on a null hint, or just pass it on?
   return loadBalancer.chooseServer(hint != null ? hint : "default");
}

@Override
public Server chooseServer(Object key) {
    if (!ENABLED.get() || getLoadBalancerStats().getAvailableZones().size() <= 1) {
        logger.debug("Zone aware logic disabled or there is only one zone");
        // 执行这行代码
        return super.chooseServer(key);
    }
    Server server = null;
    try {
        LoadBalancerStats lbStats = getLoadBalancerStats();
        Map<String, ZoneSnapshot> zoneSnapshot = ZoneAvoidanceRule.createSnapshot(lbStats);
        logger.debug("Zone snapshots: {}", zoneSnapshot);
        if (triggeringLoad == null) {
            triggeringLoad = DynamicPropertyFactory.getInstance().getDoubleProperty(
                "ZoneAwareNIWSDiscoveryLoadBalancer." + this.getName() + ".triggeringLoadPerServerThreshold", 0.2d);
        }

        if (triggeringBlackoutPercentage == null) {
            triggeringBlackoutPercentage = DynamicPropertyFactory.getInstance().getDoubleProperty(
                "ZoneAwareNIWSDiscoveryLoadBalancer." + this.getName() + ".avoidZoneWithBlackoutPercetage", 0.99999d);
        }
        Set<String> availableZones = ZoneAvoidanceRule.getAvailableZones(zoneSnapshot, triggeringLoad.get(), triggeringBlackoutPercentage.get());
        logger.debug("Available zones: {}", availableZones);
        if (availableZones != null &&  availableZones.size() < zoneSnapshot.keySet().size()) {
            String zone = ZoneAvoidanceRule.randomChooseZone(zoneSnapshot, availableZones);
            logger.debug("Zone chosen: {}", zone);
            if (zone != null) {
                BaseLoadBalancer zoneLoadBalancer = getLoadBalancer(zone);
                server = zoneLoadBalancer.chooseServer(key);
            }
        }
    } catch (Exception e) {
        logger.error("Error choosing server using zone aware logic for load balancer={}", name, e);
    }
    if (server != null) {
        return server;
    } else {
        logger.debug("Zone avoidance logic is not invoked.");
        return super.chooseServer(key);
    }
}
```

4、`key`为null，当前rule是`IRule`类型的，默认为`RoundRobinRule`，是`AbstractLoadBalancerRule`的子类，表示轮询。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311231624962.png)

```java
public Server chooseServer(Object key) {
    if (counter == null) {
        counter = createCounter();
    }
    counter.increment();
    if (rule == null) {
        return null;
    } else {
        try {
            // 执行这行代码，选择一个服务返回
            return rule.choose(key);
        } catch (Exception e) {
            logger.warn("LoadBalancer [{}]:  Error choosing server for key {}", name, key, e);
            return null;
        }
    }
}
```

`IRule`是一个接口，有4个实现类，有随机规则的，也有轮询规则的，通过指定规则选择一个服务返回

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311231555635.png)



**总结**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311231626146.png" style="zoom:60%;" />

基本流程如下：

- 拦截我们的`RestTemplate`请求`http://userservice/user/1`
- `RibbonLoadBalancerClient`会从请求`url`中获取服务名称，也就是user-service
- `DynamicServerListLoadBalancer`根据user-service到eureka拉取服务列表
- eureka返回列表，localhost:8081、localhost:8082
- `IRule`利用内置负载均衡规则，从列表中选择一个，例如`localhost:8081`
- `RibbonLoadBalancerClient`修改请求地址，用`localhost:8081`替代`userservice`，得到`http://localhost:8081/user/1`，发起真实请求







#### 4.2 负载均衡策略

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311231629436.png" style="zoom:67%;" />

| **内置负载均衡规则类**    | **规则描述**                                                 |
| ------------------------- | ------------------------------------------------------------ |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则。 |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略：   （1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加。  （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule规则的客户端也会将其忽略。并发连接数的上限，可以由客户端的<clientName>.<clientConfigNameSpace>.ActiveConnectionsLimit属性进行配置。 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。 |
| **ZoneAvoidanceRule**     | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询。 |
| BestAvailableRule         | 忽略那些短路的服务器，并选择并发数较低的服务器。             |
| RandomRule                | 随机选择一个可用的服务器。                                   |
| RetryRule                 | 重试机制的选择逻辑                                           |

默认的实现就是`ZoneAvoidanceRule`，是一种轮询方案，根据zone选择服务列表，但是我们平时不设置zone，因此默认就是轮询方式





#### 4.3 自定义负载均衡

**1、自定义组件**

> 我们自定义一个`IRule`添加到ioc容器后，就会使用我们这个`IRule`执行，这种是全局配置，即对于所有的服务，他都使用这个规则

```java
@Configuration
public class cloudConfig {

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
    
    @Bean
    public IRule iRule() {
        return new RandomRule();
    }
}
```



**2、修改配置文件**

> 通过修改`application.yml`文件，我们可以针对某个微服务，指定对应的负载均衡策略，这样设置的粒度更小

```yml
userservice: # 给某个微服务配置负载均衡规则，这里是userservice服务
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则
```





#### 4.4 饥饿加载

> Ribbon默认是采用懒加载，即第一次访问时才会去创建`LoadBalanceClient`，所以第一次请求时间会很长
>
> 饥饿加载会在项目启动时创建，降低第一次访问的耗时，可以通过下面配置开启饥饿加载

```yml
ribbon:
  eager-load:
    enabled: true
    clients: userservice  # 这里是个列表，可以设置多个服务
```







## 二、Nacos

[Nacos](https://nacos.io/)是阿里巴巴的产品，现在是[SpringCloud](https://spring.io/projects/spring-cloud)中的一个组件。相比[Eureka](https://github.com/Netflix/eureka)功能更加丰富，在国内受欢迎程度较高。



**安装nacos**

> 1、在github下载nacos后，解压，可以通过conf/application.properties修改其端口，默认为为8848
>
> 2、在bin目录下，执行`startup.cmd -m standalone`启动nacos，浏览器访问`http://127.0.0.1:8848/nacos`即可





### 1、服务注册

> Nacos是SpringCloudAlibaba的组件，而SpringCloudAlibaba也遵循SpringCloud中定义的服务注册、服务发现规范。因此使用Nacos和使用Eureka对于微服务来说，并没有太大区别。
>
> 主要差异在于：
>
> - 依赖不同
> - 服务地址不同



**父工程导入依赖**

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```



**在user-service和order-service中引入`nacos-discovery`依赖**

> 注释掉eureka依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



**配置nacos地址**

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
```



启动实例后，就可以在nacos控制台看到启动的服务

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261534696.png)





### 2、服务分级存储模型

> 一个**服务**可以有多个**实例**，例如我们的user-service，可以有:
>
> - `127.0.0.1:8081`
> - `127.0.0.1:8082`
> - `127.0.0.1:8083`
>
> 假如这些实例分布于全国各地的不同机房，例如：
>
> - `127.0.0.1:8081`，在上海机房
> - `127.0.0.1:8082`，在上海机房
> - `127.0.0.1:8083`，在杭州机房
>
> Nacos就将同一机房内的实例划分为一个**集群**。



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261537182.png" style="zoom:50%;" />



微服务互相访问时，应该尽可能访问同集群实例，因为本地访问速度更快。当本集群内不可用时，才访问其它集群。例如：

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261538292.png" style="zoom:67%;" />





#### 2.1 user-service配置集群

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: SH # 集群名称
```



我们将8081和8082配置在HZ集群，将8083配置在SH集群

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261543177.png)





#### 2.2 同集群的优先负载均衡

默认的`ZoneAvoidanceRule`并不能实现根据同集群优先来实现负载均衡。

因此Nacos中提供了一个`NacosRule`的实现，可以优先从同集群中挑选实例。



**修改order-service的配置文件**

将order-service也放入HZ集群

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```



**修改负载均衡规则**

修改后，order-service会优先使用本集群内部的服务，如果本集群内部没有服务可用，会使用其他集群的服务，同时产生警告信息

```yml
userservice: # 给某个微服务配置负载均衡规则，这里是userservice服务
  ribbon:
    # NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule 
```





#### 2.3 权重配置

实际部署中服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求。但默认情况下NacosRule是同集群内随机挑选，不会考虑机器的性能问题。因此，Nacos提供了权重配置来控制访问频率，权重越大则访问频率越高。



- Nacos控制台可以设置实例的权重，0-1之间
- 同集群之间的多个实例，权重越高被访问频率越高
- 权重设置为0则不会被访问

在nacos控制台，找到user-service的实例列表，点击编辑，即可修改权重，如果权重修改为0，表示不访问。这样在做服务升级时，我们可以一个一个服务升级，将其权重设置为0，这样没有请求过来，我们就可以对其进行服务升级。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261604494.png" style="zoom:80%;" />







#### 2.4 环境隔离

Nacos提供了namespace来实现环境隔离功能。

- nacos中可以有多个namespace
- namespace下可以有group、service等
- 不同namespace之间相互隔离，例如不同namespace的服务互相不可见

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202403311801491.png" style="zoom: 50%;" />



**1、创建namespace**

默认情况下，所有的实例都在同一个namespace，名为public，我们可以新建一个namespace

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261614403.png)



**2、给服务配置namespace**

此时`user-service`在public空间下，`order-service`在dev空间下，因此`order-service`调用`user-service`会报错，提示找不到，因为不同的namespace下是相互隔离的

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ
        namespace: a7897ccd-8c4b-4e14-b840-075053ead820 # namespace处配置的是对应的id
```







### 3、Nacos与Eureka的区别

Nacos的服务实例分为两种类型：

- 临时实例：如果实例宕机超过一定时间，会从服务列表剔除，默认的类型。

- 非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。一般是采用临时实例，非临时实例会增加服务器压力



**配置一个服务实例为非临时实例**

```yml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 设置为非临时实例
```



**1、Nacos与eureka的共同点**

- 都支持服务注册和服务拉取
- 都支持服务提供者心跳方式做健康检测【服务提供者每隔一段时间【默认30s】向注册中心发起请求，证明自己是正常的】

**2、Nacos与Eureka的区别**

- Nacos支持服务端主动检测服务提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式

- 临时实例心跳不正常会被剔除，非临时实例则不会被剔除

- Nacos支持服务列表变更的消息推送模式，服务列表更新更及时。

  Eureka中消费者向注册中心拉取服务提供者实例时，会缓存起来，每隔30s重新拉取一次，但是可能在30秒内有一个实例挂了，那么消费者是无法感知的。但是在Nacos中，如果服务提供者挂了，即处于不健康状态，那么注册中心会主动推送消息通知服务消费者。

- Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261631086.png" style="zoom:67%;" />









### 4、Nacos配置管理

#### 4.1 统一配置管理

当微服务部署的实例越来越多，达到数十、数百时，逐个修改微服务配置就会很麻烦，而且很容易出错。我们需要一种统一配置管理方案，可以集中管理所有实例的配置。

Nacos一方面可以将配置集中管理，另一方可以在配置变更时，及时通知微服务，实现配置的热更新。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261647850.png)





#### 4.2 在nacos中添加配置文件

> 1、在nacos控制台可以新建一个配置，其中`Data ID`表示配置名称，一般采用`服务名-profile.后缀`，分组默认，然后可以在该配置文件中修改配置内容
>
> 2、项目的核心配置，需要热更新的配置才有放到nacos管理的必要。基本不会变更的一些配置还是保存在微服务本地比较好。



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261649840.png)





#### 4.3 从nacos拉取配置

微服务要拉取nacos中管理的配置，并且与本地的application.yml配置合并，才能完成项目启动。

但如果尚未读取application.yml，是无法得知nacos的地址的，因此spring引入了一种新的配置文件：`bootstrap.yaml`文件，会在application.yml之前被读取，流程如下：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261701455.png)





**1、引入nacos-config依赖**

在`user-service`和`order-service`中都引入该依赖

```xml
<!--nacos配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```



**2、添加bootstrap.yaml**

这里会根据`spring.cloud.nacos.server-addr`获取`nacos`地址

再根据`${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}`作为文件id，去nacos中读取配置。

```yml
spring:
  application:
    name: userservice # 服务名称
  profiles:
    active: dev # 开发环境，这里是dev 
  cloud:
    nacos:
      server-addr: localhost:8848 # Nacos地址
      config:
        file-extension: yaml # 文件后缀名
```



**3、读取nacos配置**

```java
@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {
    @Value("${pattern.dateformat}")
    private String format;

    @GetMapping("/now")
    public String now(){
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(format));
    }
}
```





#### 4.4 配置热更新

我们希望通过修改nacos的配置，微服务中无需重启也能生效，也就是配置热更新。



**方式一**

在`@Value`注入的变量所在类上添加注解`@RefreshScope`

```java
@Slf4j
@RestController
@RequestMapping("/user")
@RefreshScope
public class UserController {
    @Value("${pattern.dateformat}")
    private String format;

    @GetMapping("/now")
    public String now(){
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(patternConfig.getDateformat()));
    }
}
```



**方式二**

创建一个类，使用`@ConfigurationProperties`注解自动读取配置中的信息，然后使用的时候通过`@Autowired`将该类注入后使用

```java
@Data
@Component
@ConfigurationProperties(prefix = "pattern")
public class PatternConfig {
    private String dateformat;
}
```

```java
@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private PatternConfig patternConfig;

    @GetMapping("/now")
    public String now(){
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(patternConfig.getDateformat()));
    }
}
```





#### 4.5 配置共享

微服务启动时，会去nacos读取多个配置文件，例如：

- `[spring.application.name]-[spring.profiles.active].yaml`，例如：userservice-dev.yaml

- `[spring.application.name].yaml`，例如：userservice.yaml

而`[spring.application.name].yaml`不包含环境，因此可以被多个环境共享。



我们在nacos中新建一个环境，名为`userservice.yaml`，此时nacos中有两个环境，一个是`userservice.yaml`，一个是`userservice-dev.yaml`

此时我们启动一个服务，其开发环境为dev，两个配置文件他都可以读到。启动另一个服务，开发环境为`test`，他只能读取到`userservice.yaml`的配置文件





**配置优先级**

`服务名-profile.yaml`  > `服务名.yaml` > 本地配置





#### 4.6 搭建Nacos集群

**集群结构图**

在Nacos集群中注册服务时，可以选择连接到任何一个可用的Nacos节点进行注册。当你在其中一个节点上注册服务时，Nacos会自动将该注册信息同步到其他节点，以确保集群中的所有节点都具有相同的服务注册信息。Nacos使用一种称为**集群通信**的机制来实现节点之间的数据同步。当一个节点上的数据发生变化时（例如注册新的服务），它会将变更信息广播给其他节点。其他节点收到变更信息后，会更新自己的数据存储，以保持数据的一致性。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311261804198.png" style="zoom:67%;" />





搭建集群的基本步骤：

- 搭建数据库，初始化数据库表结构
- 下载nacos安装包
- 配置nacos
- 启动nacos集群
- nginx反向代理



**1、初始化数据库**

Nacos默认数据存储在内嵌数据库Derby中，不属于生产可用的数据库。

官方推荐的最佳实践是使用带有主从的高可用数据库集群，这里我们使用单点的数据库

首先新建一个数据库，命名为`nacos`，然后导入`SQL`



**2、配置nacos**

- 进入`nacos`的`conf`目录，修改配置文件`cluster.conf.example`，重命名为`cluster.conf`，这个文件用于指定集群中的节点信息。在该文件中，你需要指定每个节点的 IP 地址和端口号，以及节点的角色（如主节点、从节点等）。这样，Nacos节点就能够根据这些配置信息来建立集群通信和数据同步。

- 然后添加内容

  ```txt
  192.168.92.1:8845   # 192.168.92.1是本机ip
  192.168.92.1:8846
  192.168.92.1:8847
  ```

- 修改`application.properties`文件，添加数据库配置

  ```xml
  spring.datasource.platform=mysql
  
  db.num=1
  
  db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
  db.user.0=root
  db.password.0=123
  ```

  

**3、启动**

将nacos文件夹复制三份，分别命名为：nacos1、nacos2、nacos3



然后分别修改三个文件夹中的application.properties，

nacos1:

```properties
server.port=8845
```

nacos2:

```properties
server.port=8846
```

nacos3:

```properties
server.port=8847
```



然后分别启动三个nacos节点：

**注意：**`nacos1.4.6`需要将`startup.cmd`中需要将`set "NACOS_CONFIG_OPTS=--spring.config.additional-location=optional:%CUSTOM_SEARCH_LOCATIONS%"`**修改为**`set "NACOS_CONFIG_OPTS=--spring.config.additional-location=%CUSTOM_SEARCH_LOCATIONS%"`，即删除`optional:`，否则设置端口不会生效

```sh
startup.cmd
```



**4、nginx反向代理**

修改`conf/nginx.conf`文件，然后启动nginx

```nginx
upstream nacos-cluster {
    server 127.0.0.1:8845;
	server 127.0.0.1:8846;
	server 127.0.0.1:8847;
}

server {
    listen       88;
    server_name  localhost;

    location /nacos {
        proxy_pass http://nacos-cluster;
    }
}
```





**5、修改application.yml**

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:88 # Nacos地址
```







## 三、Feign远程调用

这是我们使用`RestTemplate`发起远程调用的代码，需要在代码中拼接url，代码可读性差，同时对于复杂的url难以维护。Feign是一个声明式的http客户端，作用就是帮助我们优雅的实现http请求的发送，解决上面提到的问题

```java
String url = "http://userservice/user/" + order.getUserId();
User user = restTemplate.getForObject(url, User.class);
```





### 1、Feign代替RestTemplate

**1、引入依赖**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```



**2、添加注解**

启动类上添加`@EnableFeignClients`注解

```java
@SpringBootApplication
@EnableFeignClients
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```



**3、编写Feign客户端**

```java
@FeignClient("userservice")
public interface UserClient {
    @GetMapping("user/{id}")
    User getUserById(@PathVariable("id") Long id);
}
```



这个客户端主要是基于SpringMVC的注解来声明远程调用的信息，比如：

- 服务名称：userservice
- 请求方式：GET
- 请求路径：/user/{id}
- 请求参数：Long id
- 返回值类型：User

这样，Feign就可以帮助我们发送http请求，无需自己使用RestTemplate来发送了。



**4、测试**

```java
public class OrderService {
    @Autowired
    private OrderMapper orderMapper;
    @Autowired
    private UserClient userClient;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        // 2.查询user
        User user = userClient.getUserById(order.getUserId());
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```





### 2、自定义配置

Feign可以支持很多的自定义配置：

| 类型                     | 作用             | 说明                                                   |
| ------------------------ | ---------------- | ------------------------------------------------------ |
| **`feign.Logger.Level`** | 修改日志级别     | 包含四种不同的级别：NONE、BASIC、HEADERS、FULL         |
| `feign.codec.Decoder`    | 响应结果的解析器 | http远程调用的结果做解析，例如解析json字符串为java对象 |
| `feign.codec.Encoder`    | 请求参数编码     | 将请求参数编码，便于通过http请求发送                   |
| `feign.Contract`         | 支持的注解格式   | 默认是SpringMVC的注解                                  |
| `feign.Retryer`          | 失败重试机制     | 请求失败的重试机制，默认是没有，不过会使用Ribbon的重试 |

一般情况下，默认值就能满足我们使用，如果要自定义时，只需要创建自定义的@Bean覆盖默认Bean即可。



**配置文件方式**

全局配置

```yml
feign:
  client:
    config:
      default:  # default表示全局配置
        loggerLevel: FULL # 日志级别
```

针对单个微服务的配置

```yml
feign:
  client:
    config:
      userservice: # 针对某个微服务的配置
        loggerLevel: FULL # 日志级别
```

日志的级别分为四种：

- NONE：不记录任何日志信息，这是默认值。
- BASIC：仅记录请求的方法，URL以及响应状态码和执行时间
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。





**Java代码**

先声明一个类，然后声明一个`Logger.Level`的对象：

```java
public class DefaultFeignConfiguration {
    @Bean
    public Logger.Level feignLogLevel() {
        return Logger.Level.BASIC;
    }
}
```



全局生效：在启动类的`@EnableFeignClients`注解属性上添加

```java
@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration.class)
```



局部生效：在对应的`@FeignClient`这个注解中添加

```java
@FeignClient(value = "userservice", configuration = DefaultFeignConfiguration .class) 
```







### 3、Feign使用优化

Feign底层发起http请求，依赖于其它的框架。其底层客户端实现包括：

- `URLConnection`：默认实现，不支持连接池

- `Apache HttpClient `：支持连接池

- `OKHttp`：支持连接池



因此提高Feign的性能主要手段就是使用**连接池**代替默认的`URLConnection`



**使用`HttpClient`**

1、引入依赖

```xml
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
</dependency>
```



2、配置连接池

```yml
feign:
  client:
    config:
      default:  # default表示全局配置
        loggerLevel: BASIC # 日志级别
  httpclient:
    enabled: true
    max-connections: 200 # 最大连接数
    max-connections-per-route: 50 # 单条路径最大连接数
```



3、在`FeignClientFactoryBean`中的`loadBalance`中，可以看到获取到的`client`就是`ApacheHttpClient`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271521309.png)



**总结**

Feign的优化：

1、日志级别尽量用basic

2、使用`HttpClient`或`OKHttp`代替`URLConnection`

- 引入`feign-httpClient`依赖

- 配置文件开启`httpClient`功能，设置连接池参数





### 4、最佳实践

观察可以发现，Feign的客户端与服务提供者的controller代码非常相似

**feign客户端**

```java
@FeignClient("userservice")
public interface UserClient {
    @GetMapping("user/{id}")
    User getUserById(@PathVariable("id") Long id);
}
```

**服务提供者**

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id) {
    return userService.queryById(id);
}
```





#### 4.1 继承

一样的代码可以通过继承来共享：

1）定义一个API接口，利用定义方法，并基于SpringMVC注解做声明。

2）Feign客户端和Controller都基于接口实现

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271533449.png" style="zoom:80%;" />

优点：

- 简单
- 实现了代码共享

缺点：

- 服务提供方、服务消费方紧耦合

- 参数列表中的注解映射并不会继承，因此Controller中必须再次声明方法、参数列表、注解





#### 4.2 抽取方式

当我们很多个服务都需要调用`userservice`时，每个服务都需要写一个Feign接口，因此可以将其抽取出来

将Feign的Client抽取为独立模块，并且把接口有关的POJO、默认的Feign配置都放到这个模块中，提供给所有消费者使用。

例如，将UserClient、User、Feign的默认配置都抽取到一个feign-api包中，所有微服务引用该依赖包，即可直接使用。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271535491.png" style="zoom:60%;" />



**1、抽取**

创建一个module，命名为`feign-api`，然后导入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

将`orderservice`中编写的`UserClient`、`User`、`DefaultFeignConfiguration`都复制到`feign-api`项目中

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271558632.png)





**2、order-service中使用feign-api**

在`orderservice`的pom文件中导入`feign-api`的依赖

```xml
<dependency>
    <groupId>cn.itcast.demo</groupId>
    <artifactId>feign-api</artifactId>
    <version>1.0</version>
</dependency>
```



**3、解决扫描包问题**

因为`SpringBoot`默认扫描的是它所在包及其子包下的对象并将其放入ioc容器，因此无法扫描到我们的`UserClient`，可以通过两种方式使其扫描到`UserClient`

- 指定`Feign`应该扫描的包。这种方式会将这个包下所有的bean都加载进来

  ```java
  @EnableFeignClients(clients = {UserClient.class}, defaultConfiguration = DefaultFeignConfiguration.class)
  ```

- 指定需要加载的Client接口。这种方式只会将`UserClient`加载进来

  ```java
  @EnableFeignClients(clients = {UserClient.class})
  ```

  







## 四、Gateway服务网关

Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor 等响应式编程和事件流技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。



**网关的功能：**

- 身份认证、权限校验：网关作为微服务入口，需要校验用户是是否有请求资格，如果没有则进行拦截。

- 服务路由、负载均衡：一切请求都必须先经过gateway，但网关不处理业务，而是根据某种规则，把请求转发到某个微服务，这个过程叫做路由。当然路由的目标服务有多个时，还需要做负载均衡。

- 请求限流：当请求流量过高时，在网关中按照下流的微服务能够接受的速度来放行请求，避免服务压力过大。

  

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271624200.png" style="zoom:57%;" />

在`SpringCloud`中网关的实现包括两种：

- gateway
- zuul

Zuul是基于Servlet的实现，属于阻塞式编程。而SpringCloudGateway则是基于Spring5中提供的WebFlux，属于响应式编程的实现，具备更好的性能。





### 1、gateway入门

**1、创建gateway模块**

创建gateway模块，并引入依赖

```xml
<!--网关-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!--nacos服务发现依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



**2、编写启动类**

```java
@SpringBootApplication
public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```



**3、编写配置文件**

我们将符合`Path` 规则的一切请求，都代理到 `uri`参数指定的地址。

本例中，我们将 `/user/**`开头的请求，代理到`lb://userservice`，lb是负载均衡，根据服务名拉取服务列表，实现负载均衡。

路由配置包括：

- 路由id：路由的唯一标示

- 路由目标（uri）：路由的目标地址，http代表固定地址，lb代表根据服务名负载均衡

- 路由断言（predicates）：判断路由的规则

- 路由过滤器（filters）：对请求或响应做处理

```yml
server:
  port: 10010 # 网关端口
spring:
  application:
    name: gateway # 服务名称
  cloud:
    nacos:
      server-addr: localhost:8848 # nacos地址
    gateway:
      routes: # 网关路由配置
        - id: user-service # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://userservice # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
        - id: order-service
          uri: lb://orderservice
          predicates:
            - Path=/order/**
```



**4、测试**

启动网关，访问`http://localhost:10010/user/1`时，符合`/user/**`规则，请求转发到`uri：http://userservice/user/1`，得到了结果



**5、访问流程**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271635975.png" style="zoom:67%;" />







### 2、断言工厂

我们在配置文件中写的断言规则只是字符串，这些字符串会被`Predicate Factory`读取并处理，转变为路由判断的条件

例如`Path=/user/**`是按照路径匹配，这个规则是由

`org.springframework.cloud.gateway.handler.predicate.PathRoutePredicateFactory`类来处理的，像这样的断言工厂在`SpringCloudGateway`还有十几个

| **名称**   | **说明**                       | **示例**                                                     |
| ---------- | ------------------------------ | ------------------------------------------------------------ |
| After      | 是某个时间点后的请求           | -  After=2037-01-20T17:42:47.789-07:00[America/Denver]       |
| Before     | 是某个时间点之前的请求         | -  Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]       |
| Between    | 是某两个时间点之前的请求       | -  Between=2037-01-20T17:42:47.789-07:00[America/Denver],  2037-01-21T17:42:47.789-07:00[America/Denver] |
| Cookie     | 请求必须包含某些cookie         | - Cookie=chocolate, ch.p                                     |
| Header     | 请求必须包含某些header         | - Header=X-Request-Id, \d+                                   |
| Host       | 请求必须是访问某个host（域名） | -  Host=**.somehost.org,**.anotherhost.org                   |
| Method     | 请求方式必须是指定方式         | - Method=GET,POST                                            |
| **Path**   | 请求路径必须符合指定规则       | - Path=/red/{segment},/blue/**                               |
| Query      | 请求参数必须包含指定参数       | - Query=name, Jack或者-  Query=name                          |
| RemoteAddr | 请求者的ip必须是指定范围       | - RemoteAddr=192.168.1.1/24                                  |
| Weight     | 权重处理                       |                                                              |







### 3、过滤器工厂

`GatewayFilter`是网关中提供的一种过滤器，可以对**进入网关的请求**和**微服务返回的响应**做处理

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271705700.png" style="zoom:80%;" />



Spring提供了31种不同的路由过滤器工厂。例如：

| **名称**             | **说明**                     |
| -------------------- | ---------------------------- |
| AddRequestHeader     | 给当前请求添加一个请求头     |
| RemoveRequestHeader  | 移除请求中的一个请求头       |
| AddResponseHeader    | 给响应结果中添加一个响应头   |
| RemoveResponseHeader | 从响应结果中移除有一个响应头 |
| RequestRateLimiter   | 限制请求的流量               |





#### **3.1 请求头过滤器**

**修改配置**

```yml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service 
          uri: lb://userservice 
          predicates: 
            - Path=/user/** 
          filters: # 过滤器
            - AddRequestHeader=name, xrj123456
```



**controller**

由于在gateway中添加了请求头，因此我们在controller中就可以获取到请求头的信息

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id, @RequestHeader("name") String name) {
    System.out.println(name);
    return userService.queryById(id);
}
```







#### 3.2 默认过滤器

如果要对所有的路由都生效，则可以将过滤器工厂写到default下，这样`userservice`和`orderservice`都会经过这个过滤器

```yml
spring:
  cloud:
    gateway:
      routes: # 网关路由配置
        - id: user-service # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://userservice # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
        - id: order-service
          uri: lb://orderservice
          predicates:
            - Path=/order/**
            - Before=2037-01-20T17:42:47.789-07:00[America/Denver]
      default-filters: # 默认过滤器
          - AddRequestHeader=name, xrj123456
```







### 4、全局过滤器

网关提供了31种过滤器，但每一种过滤器的作用都是固定的。如果我们希望拦截请求，做自己的业务逻辑则没办法实现。

全局过滤器的作用也是处理一切进入网关的请求和微服务响应，与`GatewayFilter`的作用一样。区别在于`GatewayFilter`通过配置定义，处理逻辑是固定的；而`GlobalFilter`的逻辑需要自己写代码实现。

定义方式是实现`GlobalFilter`接口

```java
public interface GlobalFilter {
    /**
     *  处理当前请求，有必要的话通过{@link GatewayFilterChain}将请求交给下一个过滤器处理
     *
     * @param exchange 请求上下文，里面可以获取Request、Response等信息
     * @param chain 用来把请求委托给下一个过滤器 
     * @return {@code Mono<Void>} 返回标示当前过滤器业务结束
     */
    Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
}
```





#### 4.1 自定义全局过滤器

- 需求：定义全局过滤器，拦截请求，判断请求的参数是否满足下面条件：

  - 参数中是否有authorization，

  - authorization参数值是否为admin

  如果同时满足则放行，否则拦截

- 可以通过`@Order`注解设置拦截器顺序，值越小优先级越高；也可以通过实现`Ordered`接口，重写`getOrder`方法来实现

```java
// @Order(-1)
@Component
public class AuthorizeFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 1.获取请求参数
        MultiValueMap<String, String> params = exchange.getRequest().getQueryParams();
        // 2.获取authorization参数
        String auth = params.getFirst("authorization");
        // 3.校验
        if ("admin".equals(auth)) {
            // 4.放行
            return chain.filter(exchange);
        }
        // 5.1设置状态码
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
        // 5.2拦截
        return exchange.getResponse().setComplete();
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```





#### 4.2 过滤器执行顺序

1、请求进入网关会碰到三类过滤器：当前路由的过滤器、`DefaultFilter`、`GlobalFilter`

2、请求路由后，会将当前路由过滤器和`DefaultFilter`、`GlobalFilter`，合并到一个过滤器链（集合）中，排序后依次执行每个过滤器

3、其中 当前路由的过滤器和`DefaultFilter`都是通过配置文件设置的，他们都是实现的`GatewayFilter`接口，而`GlobalFilter`是我们自定义的，在`FilteringWebHandler`类中有一个内部类`GatewayFilterAdapter`可以将`GlobalFilter`转为`GatewayFilterAdapter`，而`GatewayFilterAdapter`又实现了`GatewayFilter`，因此这三种类型的过滤器是可以合并成为一个过滤器链的

```java
public class FilteringWebHandler implements WebHandler {
    ...
    private static class GatewayFilterAdapter implements GatewayFilter {
        private final GlobalFilter delegate;

        GatewayFilterAdapter(GlobalFilter delegate) {
            this.delegate = delegate;
        }

        public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
            return this.delegate.filter(exchange, chain);
        }

        public String toString() {
            StringBuilder sb = new StringBuilder("GatewayFilterAdapter{");
            sb.append("delegate=").append(this.delegate);
            sb.append('}');
            return sb.toString();
        }
    }
    ...
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202311271740307.png" style="zoom:80%;" />



**排序的规则**

- 每一个过滤器都必须指定一个int类型的order值，**order值越小，优先级越高，执行顺序越靠前**。
- `GlobalFilter`通过实现`Ordered`接口，或者添加`@Order`注解来指定`order`值，由我们自己指定
- **路由过滤器**和**`defaultFilter`**的`order`由`Spring`指定，默认是按照声明顺序从**1**递增。
- 当过滤器的`order`值一样时，会按照 `defaultFilter` > 路由过滤器 > `GlobalFilter`的顺序执行。



详细内容，可以查看源码：

`org.springframework.cloud.gateway.route.RouteDefinitionRouteLocator#getFilters()`方法是先加载`defaultFilters`，然后再加载某个route的filters，然后合并。

`org.springframework.cloud.gateway.handler.FilteringWebHandler#handle()`方法会加载全局过滤器，与前面的过滤器合并后根据order排序，组织过滤器链







### 5、跨域问题

跨域：域名不一致就是跨域，主要包括：

- 域名不同： `www.taobao.com` 和 `www.taobao.org` 和` www.jd.com `和 `miaosha.jd.com`

- 域名相同，端口不同：`localhost:8080`和`localhost8081`

跨域问题：浏览器禁止请求的发起者与服务端发生跨域`ajax`请求，请求被浏览器拦截的问题



解决方案：**CORS**    https://www.ruanyifeng.com/blog/2016/04/cors.html



**解决跨域问题**

在gateway服务的`application.yml`文件中，添加下面的配置：

```yml
spring:
  cloud:
    gateway:
      globalcors: # 全局的跨域处理
        add-to-simple-url-handler-mapping: true # 解决options请求被拦截问题
        corsConfigurations:
          '[/**]':
            allowedOrigins: # 允许哪些网站的跨域请求 
              - "http://localhost:8090"
            allowedMethods: # 允许的跨域ajax的请求方式
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowedHeaders: "*" # 允许在请求中携带的头信息
            allowCredentials: true # 是否允许携带cookie
            maxAge: 360000 # 这次跨域检测的有效期
```









## 五、微服务保护

### 1、Sentinel

#### 1.1 雪崩问题

1、微服务中，服务间调用关系错综复杂，一个微服务往往依赖于多个其它微服务。如果服务提供者D发生了故障，当前的应用的部分业务因为依赖于D服务，因此也会被阻塞。此时，其它不依赖于D服务的业务似乎不受影响。

2、但是，依赖D服务的业务请求被阻塞，用户不会得到响应，则tomcat的这个线程不会释放，于是越来越多的用户请求到来，越来越多的线程会阻塞：

3、服务器支持的线程和并发数有限，请求一直阻塞，会导致服务器资源耗尽，从而导致所有其它服务都不可用，那么当前服务也就不可用了。那么，依赖于当前服务的其它服务随着时间的推移，最终也都会变的不可用，形成级联失败，雪崩就发生了

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071533532.png" style="zoom:70%;" />



#### 1.2 处理方式

##### 1. 超时处理

超时处理：设定超时时间，请求超过一定时间没有响应就返回错误信息，不会无休止等待。假如设置超时时间为1s，则服务调用者1秒没收到响应就返回异常，但是如果1秒内有大量请求进入，还是会发送雪崩现象

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071536179.png" style="zoom:67%;" />



##### 2. 仓壁模式

船舱都会被隔板分离为多个独立空间，当船体破损时，只会导致部分空间有水进入，将故障控制在一定范围内，避免整个船体都被淹没。

于此类似，我们可以限定每个业务能使用的线程数，避免耗尽整个tomcat的资源，因此也叫线程隔离。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071539836.png" style="zoom:67%;" />







##### 3. 断路器

断路器模式：由**断路器**统计业务执行的异常比例，如果超出阈值则会**熔断**该业务，拦截访问该业务的一切请求。

断路器会统计访问某个服务的请求数量，异常比例，当发现访问服务D的请求异常比例过高时，认为服务D有导致雪崩的风险，会拦截访问服务D的一切请求，形成熔断

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071540875.png" style="zoom:67%;" />



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071540560.png" style="zoom:67%;" />



##### 4. 限流

**流量控制**：限制业务访问的QPS，避免服务因流量的突增而故障。假设一个服务最大QPS为100，此时有1000个请求发送过来，那么Sentinel就会限制访问该服务的请求数，避免同时有大量请求

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071542239.png" style="zoom:60%;" />



##### 5. 总结

**雪崩问题**：微服务之间相互调用，因为调用链中的一个服务故障，引起整个链路都无法访问的情况。

- **限流**是对服务的保护，避免因瞬间高并发流量而导致服务故障，进而避免雪崩。是一种**预防**措施。

- **超时处理、线程隔离、降级熔断**是在部分服务故障时，将故障控制在一定范围，避免雪崩。是一种**补救**措施。





#### 1.3 服务保护技术

目前国内实用最广泛的是阿里的Sentinel框架

|                  | **Sentinel**                                   | **Hystrix**                   |
| ---------------- | ---------------------------------------------- | ----------------------------- |
| **隔离策略**     | 信号量隔离                                     | 线程池隔离/信号量隔离         |
| **熔断降级策略** | 基于慢调用比例或异常比例                       | 基于失败比率                  |
| 实时指标实现     | 滑动窗口                                       | 滑动窗口（基于 RxJava）       |
| 规则配置         | 支持多种数据源                                 | 支持多种数据源                |
| 扩展性           | 多个扩展点                                     | 插件的形式                    |
| 基于注解的支持   | 支持                                           | 支持                          |
| **限流**         | 基于 QPS，支持基于调用关系的限流               | 有限的支持                    |
| **流量整形**     | 支持慢启动、匀速排队模式                       | 不支持                        |
| 系统自适应保护   | 支持                                           | 不支持                        |
| **控制台**       | 开箱即用，可配置规则、查看秒级监控、机器发现等 | 不完善                        |
| 常见框架的适配   | Servlet、Spring Cloud、Dubbo、gRPC  等         | Servlet、Spring Cloud Netflix |

- 信号量隔离：Sentinel不使用线程池，而是通过限制线程数量（即信号量隔离）来减少不稳定资源的影响。当资源的响应时间变长时，线程就会开始被占用。当线程数量累积到一定数量时，新确定的请求将被拒绝。反之亦然，当资源恢复稳定后，占用的线程也被占用释放，并接受新的请求。通过限制线程而不是线程池，不再需要预先分配线程池的大小，从而可以避免排队、调度和上下文切换等计算花费。







#### 1.4 Sentinel使用

**安装**

可以在[Github](https://github.com/alibaba/Sentinel/releases)下载Sentinel，下载后就是一个jar包，是一个SpringBoot程序，通过`java -jar sentinel-dashboard-1.8.1.jar`命令启动，也可以设置一些启动参数`java -Dserver.port=8090 -jar sentinel-dashboard-1.8.1.jar`

| **配置项**                       | **默认值** | **说明**   |
| -------------------------------- | ---------- | ---------- |
| server.port                      | 8080       | 服务端口   |
| sentinel.dashboard.auth.username | sentinel   | 默认用户名 |
| sentinel.dashboard.auth.password | sentinel   | 默认密码   |

启动后，访问`localhost:8080`，就可以进入Sentinel控制台





**微服务整合Sentinel**

我们在order-service中整合sentinel，并连接sentinel的控制台

1）添加依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

2）配置Sentinel

```yml
spring:
  cloud: 
    sentinel:
      transport:
        dashboard: localhost:8080
```

3）访问order-service的任意端点

当我们访问order-service的任意端点【Controller方法】后，就可以在Sentinel控制台看到效果

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071551416.png)







### 2、流量控制

限流是避免服务因突发的流量而发生故障，是对微服务雪崩问题的预防。

#### 2.1 簇点链路

当请求进入微服务时，首先会访问DispatcherServlet，然后进入Controller、Service、Mapper，这样的一个调用链就叫做**簇点链路**。簇点链路中被监控的每一个接口就是一个**资源**。

默认情况下sentinel会监控SpringMVC的每一个端点（Endpoint，也就是Controller中的方法），因此SpringMVC的每一个端点（Endpoint）就是调用链路中的一个资源。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071634899.png)

流控、熔断等都是针对簇点链路中的资源来设置的，因此我们可以点击对应资源后面的按钮来设置规则：

- 流控：流量控制
- 降级：降级熔断
- 热点：热点参数限流，是限流的一种
- 授权：请求的权限控制





#### 2.2 流量控制测试

我们为`/order/{orderId}`这个资源添加流控规则，设置QPS为5，即每秒最多5个请求。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071636957.png)

我们使用JMeter进行压测，20个请求，2秒发完，即QPS为10，通过结果我们可以看到，每秒最多只有5个请求会成功响应，其他请求直接被拦截

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071640385.png)





#### 2.3 流控模式

在添加限流规则时，点击高级选项，可以选择三种**流控模式**：

- 直接：统计当前资源的请求，触发阈值时对当前资源直接限流，也是默认的模式
- 关联：统计与当前资源相关的另一个资源，触发阈值时，对当前资源限流
- 链路：统计从指定链路访问到本资源的请求，触发阈值时，对指定链路限流

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071641785.png)



##### 1. 关联模式

此时我们有两个请求，一个是`order/query`是查询请求，另一个是`order/update`是修改请求，我们使用关联模式，当`update`请求的QPS达到5时，对`query`请求进行限流。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071642720.png)

```java
@GetMapping("/query")
public String query() {
    return "查询成功";
}

@GetMapping("update")
public String update() {
    return "更新成功";
}
```

我们使用JMeter进行测试，对`/order/update`发送请求，QPS为10，通过结果可以发现，`update`请求没有受到任何影响，而`query`请求被限流了，因为`update`请求的QPS超过了设定的阈值





##### 2. 链路模式

**链路模式**：只针对从指定链路访问到本资源的请求做统计，判断是否超过阈值。



**需求：**有查询订单和创建订单业务，两者都需要查询商品。针对从查询订单进入到查询商品的请求统计，并设置限流

现在有两条请求链路：

- /query --> /queryGoods

- /save --> /queryGoods



**controller**

```java
@GetMapping("/query")
public String query() {
    orderService.queryGoods();
    System.out.println("查询订单");
    return "查询成功";
}

@GetMapping("/save")
public String save() {
    orderService.queryGoods();
    System.out.println("新增订单");
    return "新增成功";
}
```

**service**

默认情况下，`OrderService`中的方法是不被Sentinel监控的，需要我们自己通过注解来标记要监控的方法。

给`OrderService`的`queryGoods`方法添加`@SentinelResource`注解

```java
@SentinelResource("goods")
public void queryGoods() {
    System.out.println("查询商品");
}
```



链路模式中，是对不同来源的两个链路做监控。但是sentinel默认会给进入SpringMVC的所有请求设置同一个root资源，会导致链路模式失效。

我们需要关闭这种对SpringMVC的资源聚合，修改order-service服务的application.yml文件

修改之后，可以在Sentinel控制台的簇点链路中看到`query`和`save`分别是两个资源了

```yml
spring:
  cloud:
    sentinel:
      web-context-unify: false # 关闭context整合
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071655088.png)





现在统计从/query进入到/queryGoods的请求，如下配置：我们对goods这个资源做限流，它只统计从`/order/query`进入的请求，设置QPS为2，超过则限流

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071656682.png)



我们实验JMeter进行测试，分别对`/order/query`和`/order/save`发起请求，QPS为4，可以发现，对`/order/save`的请求不受影响，而`/order/query`由于设置了限流，因此每秒QPS只能为2，超过的就被拒绝了









#### 2.4 流控效果

在流控的高级选项中，还有一个流控效果选项

流控效果是指请求达到流控阈值时应该采取的措施，包括三种

- 快速失败：达到阈值后，新的请求会被立即拒绝并抛出`FlowException`异常。是默认的处理方式。

- warm up：预热模式，对超出阈值的请求同样是拒绝并抛出异常。但这种模式阈值会动态变化，从一个较小值逐渐增加到最大阈值。

- 排队等待：让所有的请求按照先后次序排队执行，两个请求的间隔不能小于指定时长



##### 1. warm up

阈值一般是一个微服务能承担的最大QPS，但是一个服务刚刚启动时，一切资源尚未初始化（**冷启动**），如果直接将QPS跑到最大值，可能导致服务瞬间宕机。

warm up也叫**预热模式**，是应对服务冷启动的一种方案。请求阈值初始值是 `maxThreshold / coldFactor`，持续指定时长后，逐渐提高到`maxThreshold`值。而`coldFactor`的默认值是3.

例如，设置QPS的`maxThreshold`为10，预热时间为5秒，那么初始阈值就是 10 / 3 ，也就是3，然后在5秒后逐渐增长到10.

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071702926.png" style="zoom:80%;" />



**需求**：给`/order/{orderId}`这个资源设置限流，最大QPS为10，利用warm up效果，预热时长为5秒



**1、配置流控规则**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071703669.png" style="zoom:80%;" />

**2、JMeter测试**

我们设置JMeter20秒内发送200个请求，即QPS为10

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071704025.png)

通过结果我们可以看到，刚开始每秒的QPS为3，因为使用预热模式，初始时，QPS为10/3，而随着时间推移，QPS在慢慢增加，直到增加至10为止

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071706358.png)







##### 2. 排队等待

> 当请求超过QPS阈值时，快速失败和warm up 会拒绝新的请求并抛出异常。
>
> 而排队等待则是让所有请求进入一个队列中，然后按照阈值允许的时间间隔依次执行。后来的请求必须等待前面执行完成，如果请求预期的等待时间超出最大时长，则会被拒绝。
>

**工作原理**

例如：QPS = 5，意味着每200ms处理一个队列中的请求；timeout = 2000，意味着**预期等待时长**超过2000ms的请求会被拒绝并抛出异常。

比如现在一下子来了12 个请求，因为每200ms执行一个请求，那么：

- 第6个请求的**预期等待时长** =  200 * （6 - 1） = 1000ms
- 第12个请求的预期等待时长 = 200 * （12-1）= 2200ms



使用排队等待之后，QPS曲线就会比较平滑，如果平时QPS很小，突然来了很多请求，那么QPS会一下子增上去，而如果使用了排队等待模式，不管请求有多少，都会以平稳的速度来执行，QPS曲线也比较平滑



**需求：**给`/order/{orderId}`这个资源设置限流，最大QPS为10，利用排队的流控效果，超时时长设置为5s



**1、配置流控规则**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071712857.png)

**2、JMeter测试**

设置JMeter的QPS为15，我们查看Sentinel控制台可以发现，虽然每秒有15个请求，但是设置的QPS为10，使用排队等待模式，QPS曲线是平稳的，而等待时间超过5秒的请求就会请求失败

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071712911.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071713959.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071714261.png)





#### 2.5 热点参数限流

之前的限流是统计访问某个资源的所有请求，判断是否超过QPS阈值。而热点参数限流是**分别统计参数值相同的请求**，判断是否超过QPS阈值。



##### 1. 全局参数限流

现在有一个根据id查询商品的请求，访问`/goods/{id}`的请求中，`id`参数值会有变化，热点参数限流会根据参数值分别统计QPS，统计结果

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071717578.png" style="zoom:80%;" />

当id=1的请求触发阈值被限流时，id值不为1的请求不受影响。



对hot这个资源的0号参数（第一个参数）做统计，每1秒**相同参数值**的请求数不能超过5

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071718604.png)



##### 2. 热点参数限流

刚才的配置中，对查询商品这个接口的所有商品一视同仁，QPS都限定为5

而在实际中，可能部分商品是热点商品，例如秒杀商品，我们希望这部分商品的QPS限制与其它商品不一样，高一些。那就需要配置热点参数限流的高级选项了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071721529.png)

结合上一个配置，这里的含义是对0号的long类型参数限流，每1秒相同参数的QPS不能超过5，有两个例外：

- 如果参数值是100，则每1秒允许的QPS为10

- 如果参数值是101，则每1秒允许的QPS为15





##### 3. 测试

**需求：**给`/order/{orderId}`这个资源添加热点参数限流

- 默认的热点参数规则是每1秒请求量不超过2

- 给102这个参数设置例外：每1秒请求量不超过4

- 给103这个参数设置例外：每1秒请求量不超过10

**注意**：热点参数限流对默认的SpringMVC资源无效【Controller方法】，需要利用`@SentinelResource`注解标记资源【service方法】

- 因为所有请求都会被Sentinel中的拦截器所拦截，对于Controller方法，Sentinel是在`preHandler`方法创建entry，在`afterCompletion`中将entry退出，而在`preHandler`方法中，执行通过`Entry entry = SphU.entry(resourceName, 1, EntryType.IN);`执行链路树，其中没有带参数，所以无法做热点参数限流。

- 在Service方法中，因为我们使用`@SentinelResource`注解标记后，是通过AOP对方法增强，他在执行的时候是通过`entry = SphU.entry(resourceName, resourceType, entryType, pjp.getArgs());` 这里是带了参数的，因此可以做热点参数限流



**1、添加注解**

```java
@SentinelResource("hot")
@GetMapping("{orderId}")
public Order queryOrderByUserId(@PathVariable("orderId") Long orderId, @RequestHeader(value = "name", required = false) String name) {
    System.out.println(name);
    // 根据id查询订单并返回
    return orderService.queryOrderById(orderId);
}
```



**2、设置热点规则**

对所有第一个参数相同的所有请求设置QPS为2，但是对于第一个参数为102的请求，QPS设置为4，对于第一个参数为103的请求，QPS设置为10

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071724057.png" style="zoom:80%;" />



**3、JMeter测试**

设置JMeter访问`/order/101`、`/order/102`、`/order/103`的QPS都为5，通过结果可以看出，对于101的请求，QPS只能为2，102的QPS为4，103的QPS为5，小于上限10不受影响

​                                                 ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071731600.png)![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071732631.png)![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071732206.png)







#### 2.6 Sentinel与Gateway的限流区别

> 限流算法常见的有三种实现：滑动时间窗口、令牌桶算法、漏桶算法。

1、Gateway则采用了基于Redis实现的令牌桶算法。

2、而Sentinel内部却比较复杂：

- 默认限流模式是基于滑动时间窗口算法
- 排队等待的限流模式则基于漏桶算法
- 而热点参数限流则是基于令牌桶算法





#### 2.7 滑动窗口

**固定窗口计数器**

- 将时间划分为多个窗口，窗口时间跨度称为Interval，例如1000ms；

- 每个窗口维护一个计数器，每有一次请求就将计数器加一，限流就是设置计数器阈值，例如3

- 如果计数器超过了限流阈值，则超出阈值的请求都被丢弃。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312171905914.png)

但是固定滑动窗口是存在问题的，例如4500-5000来了3个请求，5000-5500来了3个请求，根据我们划分的窗口，请求都可以通过，但是4500-5500是一秒之间的，显然超过了限流阈值3



**滑动窗口计数器**

滑动窗口计数器算法会将一个窗口划分为n个更小的区间

- 假设窗口时间跨度Interval为1秒；区间数量 n = 2 ，则每个小区间时间跨度为500ms

- 限流阈值为3，时间窗口（1秒）内请求超过阈值时，超出的请求被限流

- 窗口会根据当前请求所在时间（currentTime）移动，窗口范围是从（currentTime-Interval）之后的第一个时区开始，到currentTime所在时区结束

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312171908955.png)

例如1400ms来了一个请求，此时计算`currentTime-Interval`为400，他后边第一个区间是500-1000，此时500-1500这里两个区间中有4个请求，那么这个请求就会被丢弃。但是图中1250、1300、1600一共是3个请求，2100又来了一个请求，此时计算的区间为1500-2500，因此2100这个请求可以放行，但是在1100-2100之间是有4个请求的，依然超过了限流阈值。所以滑动创建计数器算法在区间划分很小的情况下可以起到限流的作用。





#### 2.8 令牌桶

- 以固定的速率生成令牌，存入令牌桶中，如果令牌桶满了以后，多余令牌丢弃

- 请求进入后，必须先尝试从桶中获取令牌，获取到令牌后才可以被处理

- 如果令牌桶中没有令牌，则请求等待或丢弃

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312171913180.png" style="zoom:70%;" />

令牌桶在代码实现上并不是创建一个桶并生成令牌。而是记录上一个请求到达的时间，以及当前请求的数量。假设上一次请求达到的时间为1s，令牌桶每秒生成4个令牌，当前请求在1.5s到达，那么时间间隔为0.5s，这0.5s内可以生成2个令牌，只需要判断当前请求数是否小于2即可，如果小于则放行，大于的请求就拦截

令牌桶的问题在于：假如在第一秒的时候生成了4个令牌，但是没有请求过来，第二秒的时候同时到达8个请求，那么桶里的4个令牌让4个请求通行，同时令牌桶还会生成4个令牌让后4个请求同行，这样1s内就执行了8个请求。所以我们在设置桶大小时，可以设置低于我们服务的请求上限，比如服务QPS为5，我们设置桶的大小为3，这样即使一下子到很多请求，服务也不会受到太大影响。同时这样做可以应对突发情况，同时来大量请求，令牌桶还是可以处理，不会丢弃



#### 2.9 漏桶

- 将每个请求视作"水滴"放入"漏桶"进行存储；

- "漏桶"以固定速率向外"漏"出请求来执行，如果"漏桶"空了则停止"漏水”；

- 如果"漏桶"满了则多余的"水滴"会被直接丢弃。

- 可以理解成请求在桶内排队等待

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312171920224.png" style="zoom:70%;" />

漏桶方式来用于限流可以让到达的请求平滑的处理，发送端请求数忽高忽低，但是经过漏桶后，处理的请求数曲线是平滑的





**三种算法对比**

|                          | **滑动时间窗口**                             | **令牌桶**                                                   | **漏桶**                                       |
| ------------------------ | -------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------- |
| **能否保证流量曲线平滑** | 不能，但窗口内区间越小，流量控制越平滑       | 基本能，在请求量持续高于令牌生成速度时，流量平滑。但请求量在令牌生成速率上下波动时，无法保证曲线平滑 | 能，所有请求进入桶内，以恒定速率放行，绝对平滑 |
| **能否应对突增流量**     | 不能，突增流量，只要高出限流阈值都会被拒绝。 | 能，桶内积累的令牌可以应对突增流量                           | 能，请求可以暂存在桶内                         |
| **流量控制精确度**       | 低，窗口区间越小，精度越高                   | 高                                                           | 高                                             |







### 3、隔离和降级

限流是一种预防措施，虽然限流可以尽量避免因高并发而引起的服务故障，但服务还会因为其它原因而故障。

而要将这些故障控制在一定范围，避免雪崩，就要靠**线程隔离**（舱壁模式）和**熔断降级**手段了。



**线程隔离**：调用者在调用服务提供者时，给每个调用的请求分配独立线程池，出现故障时，最多消耗这个线程池内资源，避免把调用者的所有资源耗尽。

**熔断降级**：在调用方这边加入断路器，统计对服务提供者的调用，如果调用的失败比例过高，则熔断该业务，不允许访问该服务的提供者了。



不管是线程隔离还是熔断降级，都是对**客户端**（调用方）的保护。需要在**调用方** 发起远程调用时做线程隔离、或者服务熔断。

而我们的微服务远程调用都是基于Feign来完成的，因此我们需要将Feign与Sentinel整合，在Feign里面实现线程隔离和服务熔断。



#### 3.1 Feign整合Sentinel

**开启Sentinel功能**

修改`OrderService`的`application.yml`文件，开启Feign的Sentinel功能：

```yml
feign:
  sentinel:
    enabled: true # 开启feign对sentinel的支持
```



**编写失败降级逻辑**

业务失败后，不能直接报错，而应该返回用户一个友好提示或者默认结果，这个就是失败降级逻辑。

给FeignClient编写失败后的降级逻辑

- FallbackClass，无法对远程调用的异常做处理

- FallbackFactory，可以对远程调用的异常做处理【推荐】



1、在`feing-api`项目中定义类，实现`FallbackFactory`接口

```java
@Slf4j
public class UserClientFallbackFactory implements FallbackFactory<UserClient> {
    @Override
    public UserClient create(Throwable throwable) {
        return new UserClient() {
            @Override
            public User getUserById(Long id) {
                log.error("查询用户异常", throwable);
                return new User();
            }
        };
    }
}
```



2、将`UserClientFallbackFactory`注册为`bean`

```java
@Bean
public UserClientFallbackFactory userClientFallbackFactory() {
    return new UserClientFallbackFactory();
}
```



3、在`UserClient`接口中使用`UserClientFallbackFactory`

```java
@FeignClient(value = "userservice", fallbackFactory = UserClientFallbackFactory.class)
public interface UserClient {
    @GetMapping("user/{id}")
    User getUserById(@PathVariable("id") Long id);
}
```



4、重启后，访问一次订单查询业务，然后查看sentinel控制台，可以看到新的簇点链路

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072017621.png)





#### 3.2 线程隔离（舱壁模式）

线程隔离有两种方式实现：

- 线程池隔离：给每个服务调用业务分配一个线程池，利用线程池本身实现隔离效果

  当tomcat中一个线程的请求到来后，会去对应服务的线程池拿到线程在执行业务

- 信号量隔离（Sentinel默认采用）：不创建线程池，而是计数器模式，记录业务使用的线程数量，达到信号量上限时，禁止新的请求。

  当tomcat中一个线程的请求到来后，继续使用这个线程



**两者优缺点**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072019306.png" style="zoom:50%;" />

Hystix默认是基于线程池实现的线程隔离，每一个被隔离的业务都要创建一个独立的线程池，线程过多会带来额外的CPU开销，性能一般，但是隔离性更强。

Sentinel是基于信号量（计数器）实现的线程隔离，不用创建线程池，性能较好，但是隔离性一般。



**Sentinel线程隔离**

**需求**：给 `order-service`服务中的`UserClient`的查询用户接口设置流控规则，线程数不能超过 2。然后利用jemeter测试。

**1、配置隔离规则**

设置线程隔离只需要在添加流控规则时阈值类型选择**线程数**即可

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072023639.png)



**2、JMeter测试**

使用JMeter一下发送10个请求，即同时有10个线程发送请求，通过结果可以发现，只有两个请求的远程调用成功了，成功返回了用户数据，但是其余8个请求远程调用失败，由于我们编写了失败策略，因此其余8个请求返回的User均为空

​         ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072026492.png)                                                   ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072028794.png)       



#### 3.3 熔断降级

熔断降级是解决雪崩问题的重要手段。其思路是由**断路器**统计服务调用的异常比例、慢请求比例，如果超出阈值则会**熔断**该服务。即拦截访问该服务的一切请求；而当服务恢复时，断路器会放行访问该服务的请求。

断路器控制熔断和放行是通过状态机来完成的

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072030795.png" style="zoom:67%;" />

状态机包括三个状态：

- closed：关闭状态，断路器放行所有请求，并开始统计异常比例、慢请求比例。超过阈值则切换到open状态
- open：打开状态，服务调用被**熔断**，访问被熔断服务的请求会被拒绝，快速失败，直接走降级逻辑。Open状态时间到之后会进入half-open状态
- half-open：半开状态，放行一次请求，根据执行结果来判断接下来的操作。
  - 请求成功：则切换到closed状态
  - 请求失败：则切换到open状态



断路器熔断策略有三种：慢调用、异常比例、异常数



##### 1. 慢调用

业务的响应时长（RT）大于指定时长的请求认定为慢调用请求。在指定时间内，如果请求数量超过设定的最小数量，慢调用比例大于设定的阈值，则触发熔断。



**需求**：给 UserClient的查询用户接口设置降级规则，慢调用的RT阈值为50ms，统计时间为1秒，最小请求数量为5，失败阈值比例为0.4，熔断时长为5



1、设置降级规则，统计1秒内至少收到5个请求，且请求时长超过50ms的比例达到0.4，那么触发熔断，熔断时长为5s

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072032544.png)



2、设置慢调用，当请求的id为1时，触发慢调用。

此时访问`http://localhost:8084/order/101`因为需要访问`userservice`且id为1，所以请求很慢，而访问`http://localhost:8084/order/102`，由于访问的用户id为2，所以访问速度很快

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id) throws InterruptedException {
    if (id == 1) {
        Thread.sleep(60);
    }
    return userService.queryById(id);
}
```



3、测试

在浏览器访问`http://localhost:8084/order/101`快速刷新5次，可以发现此时触发了熔断，无论访问用户的id为1还是2都返回null，而5秒过后，变为正常

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072039978.png)





##### 2. 异常比例、异常数

统计指定时间内的调用，如果调用次数超过指定请求数，并且出现异常的比例达到设定的比例阈值（或超过指定异常数），则触发熔断。



**需求**：给 UserClient的查询用户接口设置降级规则，统计时间为1秒，最小请求数量为5，失败阈值比例为0.4，熔断时长为5s



1、设置降级规则，统计1s内至少收到5个请求，异常比例达到0.4，就触发熔断，熔断时长为5s

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072041280.png)



2、设置异常请求，此时访问的用户id为2就触发异常

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id) throws InterruptedException {
    if (id == 1) {
        Thread.sleep(60);
    }
    if (id == 2) {
        throw new RuntimeException("测试的异常");
    }
    return userService.queryById(id);
}
```



3、测试

在浏览器访问`http://localhost:8084/order/102`，快速刷新5次，此时触发熔断，此时访问正常的103，因为远程调用熔断了，所以也无法查到用户信息

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072045636.png)







### 4、授权规则

授权规则可以对请求方来源做判断和控制。

前边我们通过gateway网关可以进行身份的校验，用户访问网关，网关对身份校验后路由到对应的微服务，但是如果用户知道了微服务的地址，直接去访问微服务，那么网关的校验就失效了。因此可以通过Sentinel对请求的来源进行判断，例如只有通过gateway网关来的请求我们才放行。



#### 4.1 添加授权规则

授权规则可以对调用方的来源做控制，有白名单和黑名单两种方式。

- 白名单：来源（origin）在白名单内的调用者允许访问

- 黑名单：来源（origin）在黑名单内的调用者不允许访问



**需求**：我们允许请求从gateway到order-service，不允许浏览器访问order-service，那么白名单中就要填写**网关的来源名称（origin）**。



**1、获取origin**

Sentinel是通过`RequestOriginParser`这个接口的`parseOrigin`来获取请求的来源的。

这个方法的作用就是从request对象中，获取请求者的origin值并返回。默认情况下，sentinel不管请求者从哪里来，返回值永远是**default**，也就是说一切请求的来源都被认为是一样的值default。

```java
public interface RequestOriginParser {
    /**
     * 从请求request对象中获取origin，获取方式自定义
     */
    String parseOrigin(HttpServletRequest request);
}
```



因此，我们需要自定义这个接口，在`order-service`中创建`HeaderOriginParser`类实现`RequestOriginParser`接口，它获取请求头中`origin`的值，然后返回

```java
@Component
public class HeaderOriginParser implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest request) {
        String origin = request.getHeader("origin");
        if (StringUtils.isEmpty(origin)) {
            origin = "blank";
        }
        return origin;
    }
}
```



**2、通过网关添加请求头**

我们设置的`origin`的获取方式是从`reques-header`中获取`origin`值，所以必须让所有从gateway路由到微服务的请求都带上origin头。

因此通过`GatewayFilter`的`AddRequestHeaderGatewayFilter`来对通过网关的请求增加一个请求头。

```yml
spring:
  application:
    name: gateway # 服务名称
  cloud:
    nacos:
      server-addr: localhost:8848 # nacos地址
    gateway:
      routes: # 网关路由配置
        # ...
      default-filters: # 默认过滤器
          - AddRequestHeader=origin, gateway  # 添加请求头
```



**3、设置授权规则**

我们这里通过Sentinel控制台添加授权规则，保护的资源名为`/order/{ordrrId}`，流控应用设置`gateway`白名单，这样通过gateway访问的请求都会正常响应，其他请求都会被限流。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312081155431.png)





#### 4.2 自定义异常结果

默认情况下，发生限流、降级、授权拦截时，都会抛出异常到调用方。异常结果都是`flow limmiting`（限流）。这样不够友好，无法得知是限流还是降级还是授权拦截。



**1、异常类型**

自定义异常时的返回结果，需要实现`BlockExceptionHandler`接口

```java
public interface BlockExceptionHandler {
    /**
     * 处理请求被限流、降级、授权拦截时抛出的异常：BlockException
     */
    void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception;
}
```

- `HttpServletRequest request`：request对象
- `HttpServletResponse response`：response对象
- `BlockException e`：被Sentinel拦截时抛出的异常



`BlockException`的子类

| **异常**               | **说明**           |
| ---------------------- | ------------------ |
| `FlowException`        | 限流异常           |
| `ParamFlowException`   | 热点参数限流的异常 |
| `DegradeException`     | 降级异常           |
| `AuthorityException`   | 授权规则异常       |
| `SystemBlockException` | 系统规则异常       |



**2、自定义异常处理**

我们在`order-service`中编写`SentinelExceptionHandler`类，实现`BlockExceptionHandler`接口，根据不同异常类型，返回不同结果

```java
@Component
public class SentinelExceptionHandler implements BlockExceptionHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception {
        String msg = "未知异常";
        int status = 429;
        if (e instanceof FlowException) {
            msg = "请求被限流了";
        } else if (e instanceof ParamFlowException) {
            msg = "请求被热点参数限流";
        } else if (e instanceof DegradeException) {
            msg = "请求被降级了";
        } else if (e instanceof AuthorityException) {
            msg = "没有权限访问";
            status = 401;
        }
        response.setContentType("application/json;charset=utf-8");
        response.setStatus(status);
        response.getWriter().println("{\"msg\": " + msg + ", \"status\": " + status + "}");
    }
}
```



**3、测试**

当QPS超过我们设置的上限时，继续访问就会显示如下结果

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312081228162.png)

当我们没有通过gateway网关访问时就会显示如下结果

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312081227157.png)





### 5、规则持久化

Sentinel的所有规则都是内存存储，重启服务后所有规则都会丢失。在生产环境下，我们必须确保这些规则的持久化，避免丢失。



规则是否能持久化，取决于规则管理模式，sentinel支持三种规则管理模式：

- 原始模式：Sentinel的默认模式，将规则保存在内存，重启服务会丢失。
- pull模式
- push模式



#### 5.1 pull模式

集群模式下，会有多个Sentinel客户端管理不同的微服务，控制台将配置的规则推送到其中一台Sentinel客户端，而该客户端会将配置规则保存在本地文件或数据库中。Sentinel客户端会定时去本地文件或数据库中查询，更新本地规则。

**缺点**：时效性差

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312081230478.png" style="zoom:80%;" />





#### 5.2 push模式

Sentinel控制台配置规则后，将规则推送到远程配置中心，而所有的Sentinel客户端都会监听远程配置中心，一旦发现配置变化，就会更新自己的规则

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312081231855.png" style="zoom:80%;" />





## 六、分布式事务

在数据库水平拆分、服务垂直拆分之后，一个业务操作通常要跨多个数据库、服务才能完成。例如电商行业中比较常见的下单付款案例，包括下面几个行为：

- 创建新订单
- 扣减商品库存
- 从用户账户余额扣除金额

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101440476.png" style="zoom:67%;" />

订单的创建、库存的扣减、账户扣款在每一个服务和数据库内是一个本地事务，可以保证ACID原则。

但是当我们把三件事情看做一个"业务"，要满足保证“业务”的原子性，要么所有操作全部成功，要么全部失败，不允许出现部分成功部分失败的现象，这就是**分布式系统下的事务**了。

此时ACID难以满足，这是分布式事务要解决的问题





### 1、理论基础

#### 1.1 CAP定理

1998年，加州大学的计算机科学家 Eric Brewer 提出，分布式系统有三个指标。这三个指标不可能同时做到。这个结论叫做 **CAP 定理**。

> - Consistency（一致性）
> - Availability（可用性）
> - Partition tolerance （分区容错性）

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101443529.png" style="zoom:67%;" />



**一致性**

Consistency（一致性）：用户访问分布式系统中的任意节点，得到的数据必须一致。

比如现在包含两个节点，其中的初始数据是一致的，当我们修改其中一个节点的数据时，两者的数据产生了差异，要想保住一致性，就必须实现`node01` 到 `node02`的数据 同步

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101444412.png" style="zoom:40%;" /><img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401022318742.png" style="zoom:40%;" /><img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101446086.png" style="zoom:40%;" />





**可用性**

Availability （可用性）：用户访问集群中的任意健康节点，必须能得到响应，而不是超时或拒绝。

有三个节点的集群，访问任何一个都可以及时得到响应，当有部分节点因为网络故障或其它原因无法访问时，代表节点不可用：



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101447476.png" style="zoom:45%;" /><img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101447041.png" style="zoom:45%;" />





**分区容错**

Partition（分区）：因为网络故障或其它原因导致分布式系统中的部分节点与其它节点失去连接，形成独立分区。

Tolerance（容错）：在集群出现分区时，整个系统也要持续对外提供服务

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101449766.png" style="zoom:67%;" />





**矛盾**

在分布式系统中，系统间的网络不能100%保证健康，一定会有故障的时候，而服务又必须对外保证服务。因此Partition Tolerance不可避免。

当节点接收到新的数据变更时，就会出现问题了

- 如果此时要保证**一致性**，就必须等待网络恢复，完成数据同步后，整个集群才对外提供服务，服务处于阻塞状态，不可用。

- 如果此时要保证**可用性**，就不能等待网络恢复，那`node01`、`node02`与`node03`之间就会出现数据不一致。

所以，在P一定会出现的情况下，A和C之间只能实现一个。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101457924.png" style="zoom:50%;" />





#### 1.2 Base理论

BASE理论是对CAP的一种解决思路，包含三个思想：

- **Basically Available** **（基本可用）**：分布式系统在出现故障时，允许损失部分可用性，即保证核心可用。
- **Soft State（软状态）：**在一定时间内，允许出现中间状态，比如临时的不一致状态。
- **Eventually Consistent（最终一致性）**：虽然无法保证强一致性，但是在软状态结束后，最终达到数据一致。





**解决分布式事务问题**

分布式事务最大的问题是各个子事务的一致性问题，因此可以借鉴CAP定理和BASE理论，有两种解决思路：

- AP模式：各子事务分别执行和提交，允许出现结果不一致，然后采用弥补措施恢复数据即可，实现最终一致。

- CP模式：各个子事务执行后互相等待，同时提交，同时回滚，达成强一致。但事务等待过程中，处于弱可用状态。



不管是哪一种模式，都需要在子系统事务之间互相通讯，协调事务状态，也就是需要一个**事务协调者(TC)**。这里的子系统事务，称为**分支事务**；有关联的各个分支事务在一起称为**全局事务**。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101501850.png" style="zoom:50%;" />







### 2、Seata

Seata是 2019 年 1 月份蚂蚁金服和阿里巴巴共同开源的分布式事务解决方案。致力于提供高性能和简单易用的分布式事务服务，为用户打造一站式的分布式解决方案。官网地址：http://seata.io/



#### 2.1 Seata的架构

Seata事务管理中有三个重要的角色：

- **TC (Transaction Coordinator) -** **事务协调者：**维护全局和分支事务的状态，协调全局事务提交或回滚。

- **TM (Transaction Manager) -** **事务管理器：**定义全局事务的范围、开始全局事务、提交或回滚全局事务。

- **RM (Resource Manager) -** **资源管理器：**管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101505872.png" style="zoom:60%;" />

Seata基于上述架构提供了四种不同的分布式事务解决方案：

- XA模式：强一致性分阶段事务模式，牺牲了一定的可用性，无业务侵入
- TCC模式：最终一致的分阶段事务模式，有业务侵入
- AT模式：最终一致的分阶段事务模式，无业务侵入，也是Seata的默认模式
- SAGA模式：长事务模式，有业务侵入

无论哪种方案，都离不开TC，也就是事务的协调者。





#### 2.2 部署TC服务

**1、下载**

下载`seata-server`包，地址`http://seata.io/zh-cn/blog/download.html`



**2、修改配置**

解压后修改`conf`目录下的`registry.conf`文件：

```properties
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  # tc服务的注册中心类，这里选择nacos，也可以是eureka、zookeeper等
  type = "nacos"

  nacos {
    # seata tc 服务注册到 nacos的服务名称，可以自定义
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "DEFAULT_GROUP"
    namespace = ""
    cluster = "SH"
    username = "nacos"
    password = "nacos"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  # 读取tc服务端的配置文件的方式，这里是从nacos配置中心读取，这样如果tc是集群，可以共享配置
  type = "nacos"

  # 配置nacos地址等信息
  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "DEFAULT_GROUP"
    username = "nacos"
    password = "nacos"
    dataId = "seataServer.properties"
  }
}
```



**3、nacos中添加配置**

为了让tc服务的集群可以共享配置，选择nacos作为统一配置中心。因此服务端配置文件`seataServer.properties`文件需要在nacos中配好。

```properties
# 数据存储方式，db代表数据库
store.mode=db
store.db.datasource=druid
store.db.dbType=mysql
store.db.driverClassName=com.mysql.cj.jdbc.Driver
store.db.url=jdbc:mysql://localhost:3306/seata?serverTimezone=UTC
store.db.user=root
store.db.password=root
store.db.minConn=5
store.db.maxConn=30
store.db.globalTable=global_table
store.db.branchTable=branch_table
store.db.queryLimit=100
store.db.lockTable=lock_table
store.db.maxWait=5000
# 事务、日志等配置
server.recovery.committingRetryPeriod=1000
server.recovery.asynCommittingRetryPeriod=1000
server.recovery.rollbackingRetryPeriod=1000
server.recovery.timeoutRetryPeriod=1000
server.maxCommitRetryTimeout=-1
server.maxRollbackRetryTimeout=-1
server.rollbackRetryTimeoutUnlockEnable=false
server.undo.logSaveDays=7
server.undo.logDeletePeriod=86400000

# 客户端与服务端传输方式
transport.serialization=seata
transport.compressor=none
# 关闭metrics功能，提高性能
metrics.enabled=false
metrics.registryType=compact
metrics.exporterList=prometheus
metrics.exporterPrometheusPort=9898
```



**4、创建数据库表**

tc服务在管理分布式事务时，需要记录事务相关数据到数据库中，需要提前创建好这些表。这些表主要记录全局事务、分支事务、全局锁信息

```sql
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- 分支事务表
-- ----------------------------
DROP TABLE IF EXISTS `branch_table`;
CREATE TABLE `branch_table`  (
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `transaction_id` bigint(20) NULL DEFAULT NULL,
  `resource_group_id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `resource_id` varchar(256) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `branch_type` varchar(8) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `status` tinyint(4) NULL DEFAULT NULL,
  `client_id` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `application_data` varchar(2000) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `gmt_create` datetime(6) NULL DEFAULT NULL,
  `gmt_modified` datetime(6) NULL DEFAULT NULL,
  PRIMARY KEY (`branch_id`) USING BTREE,
  INDEX `idx_xid`(`xid`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- 全局事务表
-- ----------------------------
DROP TABLE IF EXISTS `global_table`;
CREATE TABLE `global_table`  (
  `xid` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `transaction_id` bigint(20) NULL DEFAULT NULL,
  `status` tinyint(4) NOT NULL,
  `application_id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `transaction_service_group` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `transaction_name` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `timeout` int(11) NULL DEFAULT NULL,
  `begin_time` bigint(20) NULL DEFAULT NULL,
  `application_data` varchar(2000) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `gmt_create` datetime NULL DEFAULT NULL,
  `gmt_modified` datetime NULL DEFAULT NULL,
  PRIMARY KEY (`xid`) USING BTREE,
  INDEX `idx_gmt_modified_status`(`gmt_modified`, `status`) USING BTREE,
  INDEX `idx_transaction_id`(`transaction_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

SET FOREIGN_KEY_CHECKS = 1;
```



**5、启动TC服务**

进入bin目录，运行其中的`seata-server.bat`即可，启动后，可以在nacos的服务列表看到TC服务

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101524335.png)





#### 2.4 微服务集成seata

**1、引入依赖**

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    <exclusions>
        <!--版本较低，1.3.0，因此排除-->
        <exclusion>
            <artifactId>seata-spring-boot-starter</artifactId>
            <groupId>io.seata</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>io.seata</groupId>
    <artifactId>seata-spring-boot-starter</artifactId>
    <!--seata starter 采用1.4.2版本-->
    <version>1.4.2</version>
</dependency>
```



**2、配置TC地址**

我们将订单服务、余额服务、库存服务全都放在一个事务组，然后这个事务组映射到TC的集群中，这样这三个服务就在一个事务组中

```yml
seata:
  registry: # TC服务注册中心的配置，微服务根据这些信息去注册中心获取tc服务地址
    type: nacos # 注册中心类型 nacos
    nacos:
      server-addr: 127.0.0.1:8848 # nacos地址
      namespace: "" # namespace，默认为空
      group: DEFAULT_GROUP # 分组，默认是DEFAULT_GROUP
      application: seata-tc-server # seata服务名称
      username: nacos
      password: nacos
  tx-service-group: seata-demo # 事务组名称
  service:
    vgroup-mapping: # 事务组与cluster的映射关系
      seata-demo: SH
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101537378.png" style="zoom:67%;" />

结合起来，TC服务的信息就是：`public@DEFAULT_GROUP@seata-tc-server@SH`，这样就能确定TC服务集群了。然后就可以去Nacos拉取对应的实例信息了。







#### 2.5 XA模式

XA 规范 是 X/Open 组织定义的分布式事务处理（DTP，Distributed Transaction Processing）标准，XA 规范 描述了全局的TM与局部的RM之间的接口，几乎所有主流的数据库都对 XA 规范 提供了支持。



##### **1、**两阶段提交

XA是规范，目前主流数据库都实现了这种规范，实现的原理都是基于两阶段提交。



**正常情况**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101551871.png" style="zoom:70%;" />

**异常情况**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101550913.png" style="zoom:70%;" />

一阶段：

- 事务协调者通知每个事物参与者执行本地事务
- 本地事务执行完成后报告事务执行状态给事务协调者，此时事务不提交，继续持有数据库锁

二阶段：

- 事务协调者基于一阶段的报告来判断下一步操作
  - 如果一阶段都成功，则通知所有事务参与者，提交事务
  - 如果一阶段任意一个参与者失败，则通知所有事务参与者回滚事务



 

##### 2、Seata的XA模型

Seata对原始的XA模式做了简单的封装和改造，以适应自己的事务模型，相当于对数据库的RM做了个代理

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101548290.png" style="zoom: 65%;" />

RM一阶段的工作：

​	① 注册分支事务到TC

​	② 执行分支业务sql但不提交

​	③ 报告执行状态到TC

TC二阶段的工作：

- TC检测各分支事务执行状态
  - 如果都成功，通知所有RM提交事务
  - 如果有失败，通知所有RM回滚事务


RM二阶段的工作：

- 接收TC指令，提交或回滚事务



**优点**

- 事务的强一致性，满足ACID原则。
- 常用数据库都支持，实现简单，并且没有代码侵入

**缺点**

- 因为一阶段需要锁定数据库资源，等待二阶段结束才释放，性能较差
- 依赖关系型数据库实现事务







##### 3、实现XA模式

Seata的starter已经完成了XA模式的自动装配，实现非常简单

- 修改`application.yml`文件（每个参与事务的微服务），开启XA模式：

  ```yml
  seata:
    data-source-proxy-mode: XA
  ```

- 给发起全局事务的入口方法添加`@GlobalTransactional`注解:

  ```java
  @Override
  @GlobalTransactional
  public Long create(Order order) {
      // 创建订单
      orderMapper.insert(order);
      try {
          // 扣用户余额
          accountClient.deduct(order.getUserId(), order.getMoney());
          // 扣库存
          storageClient.deduct(order.getCommodityCode(), order.getCount());
  
      } catch (FeignException e) {
          log.error("下单失败，原因:{}", e.contentUTF8(), e);
          throw new RuntimeException(e.contentUTF8(), e);
      }
      return order.getId();
  }
  ```

- 测试

  - 当前数据库中库存为8，余额为800，我们发送请求`http://localhost:8082/order?userId=user202103032042012&commodityCode=100202003032041&count=10&money=200`，这个请求需要扣减库存数为10，money为200，当我们没有开启分布式事务时，库存扣减失败回滚，而余额扣减成功，订单创建成功不会回滚。开启分布式事务后，三个事务都会执行失败。

  - 通过余额服务的日志可以发现，第一阶段先扣减库存，但是没有提交事务，第二阶段进行了事务的回滚

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101617586.png)







#### 2.6 AT模式

AT模式同样是分阶段提交的事务模型，不过缺弥补了XA模型中资源锁定周期过长的缺陷。



##### **1、Seata的AT模型**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101637049.png" style="zoom:67%;" />

阶段一RM的工作：

- 注册分支事务
- 记录undo-log（数据快照）
- 执行业务sql并提交
- 报告事务状态

阶段二提交时RM的工作：

- 删除undo-log即可

阶段二回滚时RM的工作：

- 根据undo-log恢复数据到更新前





##### **2、流程**

现在有一个数据库表，记录用户余额：

| **id** | **money** |
| ------ | --------- |
| 1      | 100       |

其中一个分支业务要执行的SQL为：

```sql
update tb_account set money = money - 10 where id = 1
```



AT模式下，当前分支事务执行流程如下：

一阶段：

1）TM发起并注册全局事务到TC

2）TM调用分支事务

3）分支事务准备执行业务SQL

4）RM拦截业务SQL，根据where条件查询原始数据，形成快照。

```json
{
    "id": 1, "money": 100
}
```

5）RM执行业务SQL，提交本地事务，释放数据库锁。此时 `money = 90`

6）RM报告本地事务状态给TC



二阶段：

1）TM通知TC事务结束

2）TC检查分支事务状态

​	 a）如果都成功，则立即删除快照

​	 b）如果有分支事务失败，需要回滚。读取快照数据（`{"id": 1, "money": 100}`），将快照恢复到数据库。此时数据库再次恢复为100

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101640389.png" style="zoom:67%;" />





**AT与XA的区别**

- XA模式一阶段不提交事务，锁定资源；AT模式一阶段直接提交，不锁定资源。
- XA模式依赖数据库机制实现回滚；AT模式利用数据快照实现数据回滚。
- XA模式强一致；AT模式最终一致







##### 3、脏写问题

在多线程并发访问AT模式的分布式事务时，有可能出现脏写问题

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101643265.png" style="zoom:50%;" />

1、事务1获取DB锁【行锁】，保存快照

2、事务1执行sql，将money修改为90

3、事务1提交事务，此时行锁被释放

4、事务2获取DB锁，保存快照

5、事务2执行sql，将money改为80

6、事务2提交事务，释放行锁

7、事务1的二阶段回滚事务，获取DB锁，根据快照进行恢复数据，money改为100，此时事务2做的操作就失效了



因此AT模式中，引入了全局锁，在释放DB锁之前，先拿到全局锁。避免同一时刻有另外一个事务来操作当前数据。DB锁是行锁，获取之后其他事务时无法访问这一行数据的，而AT的全局锁是TC管理的，当前事务获取到了TC的全局锁后，如果另一个事务也被TC管理，那么他无法获取锁，如果他不是被TC管理的，那他仍可以获取锁

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101647522.png" style="zoom:50%;" />

1、事务1获取DB锁【行锁】，保存快照

2、事务1执行sql，将money修改为90

3、事务1先获取全局锁，然后提交事务，此时行锁被释放

4、事务2获取DB锁，保存快照

5、事务2执行sql，将money改为80

6、事务2获取全局锁失败，会重试，300ms后超时就回滚

7、事务1的二阶段首先获取DB锁，此时如果被事务2所持有，那么他也会重试，但是他的重试时间比全局锁的时间长，获取DB锁后，根据快照进行恢复数据，然后释放全局锁。此时数据不会受到影响





如果事务1是被Seata管理的，而事务2没有被Seata管理，那么此时全局锁对事务2不起作用，这时就用到了乐观锁的机制，事务1记录下执行sql前后的数据`before-image`和`after-image`，在二阶段如果执行回滚，先判断当前数据和`after-image`是否一致的，如果一致那么就可以回滚，否则说明中间有其他事务对数据进行了操作，记录异常，人工介入

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101652602.png" style="zoom:60%;" />





AT模式的优点：  

- 一阶段完成直接提交事务，释放数据库资源，性能比较好
- 利用全局锁实现读写隔离
- 没有代码侵入，框架自动完成回滚和提交

AT模式的缺点：

- 两阶段之间属于软状态，属于最终一致
- 框架的快照功能会影响性能，但比XA模式要好很多





##### 4、实现AT模式

AT模式中的快照生成、回滚等动作都是由框架自动完成，没有任何代码侵入，因此实现非常简单。

只不过，AT模式需要一个表来记录全局锁、另一张表来记录数据快照undo_log。



1、导入数据库表，记录全局锁

其中lock_table导入到TC服务关联的数据库，undo_log表导入到微服务关联的数据库

```sql
-- ----------------------------
-- Table structure for undo_log
-- ----------------------------
DROP TABLE IF EXISTS `undo_log`;
CREATE TABLE `undo_log`  (
  `branch_id` bigint(20) NOT NULL COMMENT 'branch transaction id',
  `xid` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT 'global transaction id',
  `context` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT 'undo_log context,such as serialization',
  `rollback_info` longblob NOT NULL COMMENT 'rollback info',
  `log_status` int(11) NOT NULL COMMENT '0:normal status,1:defense status',
  `log_created` datetime(6) NOT NULL COMMENT 'create datetime',
  `log_modified` datetime(6) NOT NULL COMMENT 'modify datetime',
  UNIQUE INDEX `ux_undo_log`(`xid`, `branch_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = 'AT transaction mode undo table' ROW_FORMAT = Compact;

-- ----------------------------
-- Table structure for lock_table
-- ----------------------------
DROP TABLE IF EXISTS `lock_table`;
CREATE TABLE `lock_table`  (
  `row_key` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `xid` varchar(96) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `transaction_id` bigint(20) NULL DEFAULT NULL,
  `branch_id` bigint(20) NOT NULL,
  `resource_id` varchar(256) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `table_name` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `pk` varchar(36) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `gmt_create` datetime NULL DEFAULT NULL,
  `gmt_modified` datetime NULL DEFAULT NULL,
  PRIMARY KEY (`row_key`) USING BTREE,
  INDEX `idx_branch_id`(`branch_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;
```



2、修改`application.yml`文件，将事务模式修改为AT模式即可：

```YML
seata:
  data-source-proxy-mode: AT
```



3、业务方法加上`@GlobalTransactional`注解



4、测试

- 此时数据库中库存为6，余额为600，我们发送请求`http://localhost:8082/order?userId=user202103032042012&commodityCode=100202003032041&count=10&money=200`，会发送回滚

- 在余额服务的日志中可以看到使用的是AT模式，第一阶段执行完直接提交事务，第二阶段通过`undo_log`进行回滚

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401030906961.png)







#### 2.7 TCC模式

TCC模式与AT模式非常相似，每阶段都是独立事务，不同的是TCC通过人工编码来实现数据恢复。需要实现三个方法：

- `Try`：资源的检测和预留； 
- `Confirm`：完成资源操作业务；要求 Try 成功 Confirm 一定要能成功。
- `Cancel`：预留资源释放，可以理解为try的反向操作。



假设账户A原来余额是100，需要余额扣减30元。

- **阶段一（ Try ）**：检查余额是否充足，如果充足则冻结金额增加30元，可用余额扣除30

初始余额：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101800786.png)

余额充足，可以冻结：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101801113.png)

此时，总金额 = 冻结金额 + 可用金额，数量依然是100不变。事务直接提交无需等待其它事务。



- **阶段二（Confirm)**：假如要提交（Confirm），则冻结金额扣减30

确认可以提交，不过之前可用金额已经扣减过了，这里只要清除冻结金额就好了：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101801805.png)

此时，总金额 = 冻结金额 + 可用金额 = 0 + 70  = 70元



- **阶段二(Canncel)**：如果要回滚（Cancel），则冻结金额扣减30，可用余额增加30

需要回滚，那么就要释放冻结金额，恢复可用金额：

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101801134.png)







##### 1、Seata的TCC模型

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101803720.png" style="zoom:67%;" />

TCC模式的三个阶段

- Try：资源检查和预留
- Confirm：业务执行和提交
- Cancel：预留资源的释放

TCC的优点

- 一阶段完成直接提交事务，释放数据库资源，性能好
- 相比AT模型，无需生成快照，无需使用全局锁，性能最强
- 不依赖数据库事务，而是依赖补偿操作，可以用于非事务型数据库

TCC的缺点

- 有代码侵入，需要人为编写try、Confirm和Cancel接口，太麻烦
- 软状态，事务是最终一致
- 需要考虑Confirm和Cancel的失败情况，做好幂等处理







##### 2、事务悬挂和空回滚

空回滚：当某分支事务的try阶段**阻塞**时，可能导致全局事务超时而触发二阶段的cancel操作。在未执行try操作时先执行了cancel操作，这时cancel不能做回滚，就是**空回滚**。执行cancel操作时，应当判断try是否已经执行，如果尚未执行，则应该空回滚。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101805870.png" style="zoom:67%;" />



业务悬挂：对于已经空回滚的业务，之前被阻塞的try操作恢复，继续执行try，就永远不可能confirm或cancel ，事务一直处于中间状态，这就是**业务悬挂**。

执行try操作时，应当判断cancel是否已经执行过了，如果已经执行，应当阻止空回滚后的try操作，避免悬挂





##### 3、实现TCC模式

分布式事务中多个业务可以使用不同的Seata模型，因此我们对余额服务使用TCC模式，而订单服务是添加订单，无法使用TCC模式，因此还使用AT模式

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101809692.png" style="zoom:67%;" />



1）创建数据库表

```sql
CREATE TABLE `account_freeze_tbl` (
  `xid` varchar(128) NOT NULL,
  `user_id` varchar(255) DEFAULT NULL COMMENT '用户id',
  `freeze_money` int(11) unsigned DEFAULT '0' COMMENT '冻结金额',
  `state` int(1) DEFAULT NULL COMMENT '事务状态，0:try，1:confirm，2:cancel',
  PRIMARY KEY (`xid`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ROW_FORMAT=COMPACT;
```

- xid：是全局事务id
- freeze_money：用来记录用户冻结金额
- state：用来记录事务状态



2）实现思路

- Try业务：
  - 记录冻结金额和事务状态到account_freeze表
  - 扣减account表可用金额
- Confirm业务
  - 根据xid删除account_freeze表的冻结记录
- Cancel业务
  - 修改account_freeze表，冻结金额为0，state为2
  - 修改account表，恢复可用金额
- 如何判断是否空回滚？
  - cancel业务中，根据xid查询account_freeze，如果为null则说明try还没做，需要空回滚
- 如何避免业务悬挂？
  - try业务中，根据xid查询account_freeze ，如果已经存在则证明Cancel已经执行，拒绝执行try业务



3）声明TCC接口

- `@TwoPhaseBusinessAction`注解中，`name`属性表示的是`try`业务的方法名，`commitMethod`表示`confirm`业务的方法名，`rollbackMethod`表示`cancel`业务的方法名，`@BusinessActionContextParameter`注解标识参数后，在`confirm`和`cancel`方法中，可以通过`BusinessActionContext`获取该参数

```java
@LocalTCC
public interface TccService {
    @TwoPhaseBusinessAction(name = "deduct", commitMethod = "confirm", rollbackMethod = "cancel")
    void deduct(@BusinessActionContextParameter(paramName = "userId") String userId,
                @BusinessActionContextParameter(paramName = "money")int money);

    boolean confirm(BusinessActionContext ctx);

    boolean cancel(BusinessActionContext ctx);
}
```



4）编写实现类

之后controller就调用`TccServiceImpl`的`deduct`方法

```java
@Service
public class TccServiceImpl implements TccService {

    @Autowired
    private AccountMapper accountMapper;
    @Autowired
    private AccountFreezeMapper freezeMapper;

    @Override
    public void deduct(String userId, int money) {
        // 1.获取事务id
        String xid = RootContext.getXID();

        // 避免业务悬挂
        AccountFreeze oldFreeze = freezeMapper.selectById(xid);
        if (oldFreeze != null) {
            return;
        }

        // 2.扣减可用余额
        accountMapper.deduct(userId, money);
        // 3.记录冻结余额
        AccountFreeze freeze = new AccountFreeze();
        freeze.setXid(xid);
        freeze.setUserId(userId);
        freeze.setFreezeMoney(money);
        freeze.setState(AccountFreeze.State.TRY);
        freezeMapper.insert(freeze);
    }

    @Override
    public boolean confirm(BusinessActionContext ctx) {
        // 1.获取事务id
        String xid = ctx.getXid();
        // 2.删除该记录
        int cnt = freezeMapper.deleteById(xid);
        return cnt == 1;
    }

    @Override
    public boolean cancel(BusinessActionContext ctx) {
        // 1.获取事务id
        String xid = ctx.getXid();
        // 2.空回滚判断
        AccountFreeze freeze = freezeMapper.selectById(xid);
        if (freeze == null) {
            // try没执行，需要空回滚
            String userId = ctx.getActionContext().get("userId").toString();
            freeze = new AccountFreeze();
            freeze.setXid(xid);
            freeze.setUserId(userId);
            freeze.setFreezeMoney(0);
            freeze.setState(AccountFreeze.State.CANCEL);
            freezeMapper.insert(freeze);
            return true;
        }

        // 幂等判断
        if (freeze.getState() == AccountFreeze.State.CANCEL) {
            return true;
        }

        // 3.恢复可用金额
        accountMapper.refund(freeze.getUserId(), freeze.getFreezeMoney());
        // 4.将冻结表余额修改为0，状态修改为cancel
        freeze.setFreezeMoney(0);
        freeze.setState(AccountFreeze.State.CANCEL);
        int cnt = freezeMapper.updateById(freeze);
        return cnt == 1;
    }
}
```



5、测试

此时库存为4，发送请求`http://localhost:8082/order?userId=user202103032042012&commodityCode=100202003032041&count=10&money=200`，由于库存不足会发生回滚，可以看到`branchType=TCC`是TCC模式

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101921830.png)







#### 2.8 SAGA模式

在 Saga 模式下，分布式事务内有多个参与者，每一个参与者都是一个服务，需要用户根据业务场景实现其正向操作和逆向回滚操作。

分布式事务执行过程中，依次执行各参与者的正向操作，如果所有正向操作均执行成功，那么分布式事务提交。如果任何一个正向操作执行失败，那么分布式事务会去退回去执行前面各参与者的逆向回滚操作，回滚已提交的参与者，使分布式事务回到初始状态。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101936768.png" style="zoom:80%;" />

Saga也分为两个阶段：

- 一阶段：直接提交本地事务
- 二阶段：成功则什么都不做；失败则通过编写补偿业务来回滚



优点：

- 事务参与者可以基于事件驱动实现异步调用，吞吐高
- 一阶段直接提交事务，无锁，性能好
- 不用编写TCC中的三个阶段，实现简单

缺点：

- 软状态持续时间不确定，时效性差
- 没有锁，没有事务隔离，会有脏写





#### 2.8 四种模式对比

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101937174.png" style="zoom:60%;" />







### 3、高可用

Seata的TC服务作为分布式事务核心，一定要保证集群的高可用性。

#### 3.1 高可用架构

搭建TC服务集群，启动多个TC服务，注册到nacos即可。但集群并不能确保100%安全，万一集群所在机房故障，一般都会做异地多机房容灾。比如一个TC集群在上海，另一个TC集群在杭州

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312101955337.png" style="zoom:60%;" />

微服务基于事务组（`tx-service-group`)与TC集群的映射关系，来查找当前应该使用哪个TC集群。当SH集群故障时，只需要将`vgroup-mapping`中的映射关系改成HZ。则所有微服务就会切换到HZ的TC集群了。





#### 3.2 实现高可用

**1、模拟异地容灾的TC集群**

| 节点名称 | ip地址    | 端口号 | 集群名称 |
| -------- | --------- | ------ | -------- |
| seata    | 127.0.0.1 | 8091   | SH       |
| seata2   | 127.0.0.1 | 8092   | HZ       |

之前我们已经启动了一台seata服务，端口是8091，集群名为SH。现在，将seata目录复制一份，起名为seata2，改为HZ集群

然后启动seata

```
seata-server.bat -p 8092
```

在nacos中可以看到seata的集群信息

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312102002301.png)





**2、将事务组映射配置到nacos**

将`tx-service-group`与cluster的映射关系都配置到nacos配置中心。

```properties
# 事务组映射关系
service.vgroupMapping.seata-demo=HZ

service.enableDegrade=false
service.disableGlobalTransaction=false
# 与TC服务的通信配置
transport.type=TCP
transport.server=NIO
transport.heartbeat=true
transport.enableClientBatchSendRequest=false
transport.threadFactory.bossThreadPrefix=NettyBoss
transport.threadFactory.workerThreadPrefix=NettyServerNIOWorker
transport.threadFactory.serverExecutorThreadPrefix=NettyServerBizHandler
transport.threadFactory.shareBossWorker=false
transport.threadFactory.clientSelectorThreadPrefix=NettyClientSelector
transport.threadFactory.clientSelectorThreadSize=1
transport.threadFactory.clientWorkerThreadPrefix=NettyClientWorkerThread
transport.threadFactory.bossThreadSize=1
transport.threadFactory.workerThreadSize=default
transport.shutdown.wait=3
# RM配置
client.rm.asyncCommitBufferLimit=10000
client.rm.lock.retryInterval=10
client.rm.lock.retryTimes=30
client.rm.lock.retryPolicyBranchRollbackOnConflict=true
client.rm.reportRetryCount=5
client.rm.tableMetaCheckEnable=false
client.rm.tableMetaCheckerInterval=60000
client.rm.sqlParserType=druid
client.rm.reportSuccessEnable=false
client.rm.sagaBranchRegisterEnable=false
# TM配置
client.tm.commitRetryCount=5
client.tm.rollbackRetryCount=5
client.tm.defaultGlobalTransactionTimeout=60000
client.tm.degradeCheck=false
client.tm.degradeCheckAllowTimes=10
client.tm.degradeCheckPeriod=2000

# undo日志配置
client.undo.dataValidation=true
client.undo.logSerialization=jackson
client.undo.onlyCareUpdateColumns=true
client.undo.logTable=undo_log
client.undo.compress.enable=true
client.undo.compress.type=zip
client.undo.compress.threshold=64k
client.log.exceptionRate=100
```





**3、微服务读取nacos配置**

```YML
seata:
  config:
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      group: SEATA_GROUP
      data-id: client.properties
```



**4、测试**

微服务启动后读取nacos中的配置，在SH集群，因此使用的是`seata`  SH集群的这个服务。

当我们通过nacos修改配置为HZ集群后，我们的微服务会重新连接到HZ集群的这个`seata2`上

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312102014769.png)









## 七、Nacos原理

### **1、SpringCloud常见组件**

常用组件包括：

- 注册中心组件：Eureka、Nacos等

- 负载均衡组件：Ribbon

- 远程调用组件：OpenFeign

- 网关组件：Zuul、Gateway

- 服务保护组件：Hystrix、Sentinel

- 服务配置管理组件：SpringCloudConfig、Nacos





### 2、Nacos的服务注册表

#### 2.1 分级存储模型

Nacos采用了数据的分级存储模型，最外层是Namespace，用来隔离环境。然后是Group，用来对服务分组。接下来就是服务（Service），一个服务包含多个实例，但是可能处于不同机房，因此Service下有多个集群（Cluster），Cluster下是不同的实例（Instance）。

对应到Java代码中，Nacos采用了一个多层的Map来表示。结构为Map<String, Map<String, Service>>，其中最外层Map的key就是`namespaceId`，值是一个Map。内层Map的key是group拼接`serviceName`，值是Service对象。Service对象内部又是一个Map，key是集群名称，值是Cluster对象。而Cluster对象内部维护了Instance的集合。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312151501530.png" style="zoom:45%;" />





#### 2.2 Nacos源码分析

我们使用的Nacos是打包好的Nacos服务端，直接启动就能使用。分析源码需要去Github下载Nacos的源码包https://github.com/alibaba/nacos，自己编译运行



**1、导入Nacos源码**

在我们的Demo工程中，有一个`orderservice`和一个`userservice`，`orderservice`会通过Feign远程调用`userservice`，我们将Nacos的源码导入项目

​                     ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312151505016.png)![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312151508312.png)          



**2、proto编译**

Nacos底层的数据通信会基于`protobuf`对数据做序列化和反序列化。并将对应的proto文件定义在了consistency这个子模块中，我们需要先将proto文件编译为对应的Java代码。

> `protobuf`的全称是Protocol Buffer，是Google提供的一种数据序列化协议，是一种轻便高效的结构化数据存储格式，可以用于结构化数据序列化，很适合做数据存储或 RPC 数据交换格式。它可用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化结构数据格式。
>
> `protobuf`可以跨语言，就是数据定义的格式为`.proto`格式，需要基于`protoc`编译为对应的语言。



`Protobuf`的GitHub地址：https://github.com/protocolbuffers/protobuf/releases

安装完`Protobuf`后，在`main`目录下执行以下命令，会编译`.proto`文件，在consistency模块中的`entity`包下编译出Java代码：

```sh
protoc --java_out=./java ./proto/consistency.proto
protoc --java_out=./java ./proto/Data.proto
```

![image-20231215151234469](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20231215151234469.png)





**3、运行**

在`console`包下，有一个`Nacos`的SpringBoot启动类，默认情况下，启动是集群模式启动的，因此我们启动时需要添加`-Dnacos.standalone=true`参数，指定单机启动，启动后，我们访问`localhost:8848`就可以访问Nacos的控制台了



**4、源码分析**

Nacos本身就是一个SpringBoot项目，我们访问`localhost:8848/nacos`端口，会给一个控制台页，这些页面就在console模块下resources中，在控制台页面的各种操作，都会发送请求对应的Controller

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312161802726.png)



Nacos官网文档的`open-api`中我们可以看到实例注册对应的请求地址是`/nacos/v1/ns/instance`，请求方式为`post`，请求参数如下

| 名称        | 类型    | 是否必选 | 描述         |
| :---------- | :------ | :------- | ------------ |
| ip          | 字符串  | 是       | 服务实例IP   |
| port        | int     | 是       | 服务实例port |
| namespaceId | 字符串  | 否       | 命名空间ID   |
| weight      | double  | 否       | 权重         |
| enabled     | boolean | 否       | 是否上线     |
| healthy     | boolean | 否       | 是否健康     |
| metadata    | 字符串  | 否       | 扩展信息     |
| clusterName | 字符串  | 否       | 集群名       |
| serviceName | 字符串  | 是       | 服务名       |
| groupName   | 字符串  | 否       | 分组名       |
| ephemeral   | boolean | 否       | 是否临时实例 |



console模块的xml文件中，还引入了`nacos-config`和`nacos-naming`的依赖，`nacos-config`是管理配置的，`nacos-naming`是做服务注册的，这两个模块都是在nacos源码包下的，在`nacos-naming`模块的`InstanceController`中，我们可以看到他的`RequestMapping`就是`/v1/ns/instance`，在console模块中，通过`server.servlet.contextPath=/nacos`执行了路径前缀为`nacos`，因此`InstanceController`就是用来做服务注册的。



`InstanceController`的`register`方法

```java
@CanDistro
@PostMapping
@Secured(parser = NamingResourceParser.class, action = ActionTypes.WRITE)
public String register(HttpServletRequest request) throws Exception {

    // 获取namespaceId，其中DEFAULT_NAMESPACE_ID就是public
    final String namespaceId = WebUtils
        .optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
    // 获取服务名称
    final String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
    // 对serviceName进行校验，这里serviceName就是组名称@@服务名， groupName@@serviceName
    NamingUtils.checkServiceNameFormat(serviceName);
	
    // 将请求中的数据解析为Instance对象
    final Instance instance = parseInstance(request);

    // 注册实例
    serviceManager.registerInstance(namespaceId, serviceName, instance);
    return "ok";
}
```



通过`serviceManager.registerInstance`注册实例，其中`serviceManager`是`ServiceManager`类型的对象，他当中有一个`serviceMap`，key就是namespace，value也是一个Map，这个map的key是`group::serviceName`，value是Service对象

在Service中，也有一个Map，key是集群名，value是一个Cluster对象

Cluster类中有一个Set，其中的元素是Instance类型的对象

```java
@Component
public class ServiceManager implements RecordListener<Service> { 
    /**
     * Map(namespace, Map(group::serviceName, Service)).
     */
    private final Map<String, Map<String, Service>> serviceMap = new ConcurrentHashMap<>();
}

@JsonInclude(Include.NON_NULL)
public class Service extends com.alibaba.nacos.api.naming.pojo.Service implements Record, RecordListener<Instances> {
    ...
    private Map<String, Cluster> clusterMap = new HashMap<>();
}

public class Cluster extends com.alibaba.nacos.api.naming.pojo.Cluster implements Cloneable {
	...
    @JsonIgnore
    private Set<Instance> persistentInstances = new HashSet<>();
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312161823530.png" style="zoom:50%;" />







### 3、Nacos支撑服务注册压力

> 1、为Nacos建立集群
>
> 2、Nacos内部接收到注册的请求时，不会立即写数据，而是将服务注册的任务放入一个阻塞队列就立即响应给客户端。然后利用线程池读取阻塞队列中的任务，异步来完成实例更新，从而提高并发写能力【仅针对临时实例，因此实例默认是临时的，因此永久实例要保证强一致性】



1、执行`register`方法进行服务注册

```java
@CanDistro
@PostMapping
@Secured(parser = NamingResourceParser.class, action = ActionTypes.WRITE)
public String register(HttpServletRequest request) throws Exception {

    // 获取namespaceId，其中DEFAULT_NAMESPACE_ID就是public
    final String namespaceId = WebUtils
        .optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
    // 获取服务名称
    final String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
    // 对serviceName进行校验，这里serviceName就是组名称@@服务名， groupName@@serviceName
    NamingUtils.checkServiceNameFormat(serviceName);
	
    // 将请求中的数据解析为Instance对象
    final Instance instance = parseInstance(request);

    // 注册实例
    serviceManager.registerInstance(namespaceId, serviceName, instance);
    return "ok";
}
```

2、获取到`namespaceId`和`serviceName`后，执行`registerInstance`进行实例的注册

```java
// 现在有了namespaceId和serviceName以及instance，需要将instance组装成Service，然后放入Map集合
public void registerInstance(String namespaceId, String serviceName, Instance instance) throws NacosException {
    // 如果是第一次就创建空的服务
    createEmptyService(namespaceId, serviceName, instance.isEphemeral());
    // 拿到Service
    Service service = getService(namespaceId, serviceName);

    if (service == null) {
        throw new NacosException(NacosException.INVALID_PARAM,
                                 "service not found, namespace: " + namespaceId + ", service: " + serviceName);
    }
    // 将实例添加到Service中
    addInstance(namespaceId, serviceName, instance.isEphemeral(), instance);
}
```

2.1 先执行`createEmptyService`创建`Service`

- 首先通过`namespaceId`和`serviceName`从`serviceMap`【服务注册表】获取Service
- 如果`Service`为空，说明该服务是第一次注册，创建新的Service
- 执行`putServiceAndInit(service);`

```java
public void createEmptyService(String namespaceId, String serviceName, boolean local) throws NacosException {
    createServiceIfAbsent(namespaceId, serviceName, local, null);
}

public void createServiceIfAbsent(String namespaceId, String serviceName, boolean local, Cluster cluster)
        throws NacosException {
    // 首先通过namespaceId和serviceName从serviceMap获取Service
    Service service = getService(namespaceId, serviceName);
    if (service == null) {
        // 如果为空，说明该服务是第一次注册，创建新的Service
        Loggers.SRV_LOG.info("creating empty service {}:{}", namespaceId, serviceName);
        service = new Service();
        service.setName(serviceName);
        service.setNamespaceId(namespaceId);
        // 从serviceName获取GroupName，其实就是通过@@进行截取，获得第一个元素
        service.setGroupName(NamingUtils.getGroupName(serviceName));
        // 记录服务最后一次更新时间
        service.setLastModifiedMillis(System.currentTimeMillis());
        service.recalculateChecksum();
        if (cluster != null) {
            cluster.setService(service);
            service.getClusterMap().put(cluster.getName(), cluster);
        }
        service.validate();
		// 将服务放入serviceMap
        putServiceAndInit(service);
        if (!local) {
            addOrReplaceService(service);
        }
    }
}

private void putServiceAndInit(Service service) throws NacosException {
    // 将服务放入serviceMap
    putService(service);
    service = getService(service.getNamespaceId(), service.getName());
    // 对服务初始化，健康检测
    service.init();
    // 服务状态变更的监听，当服务发送变化就会触发监听机制，更新本地服务列表
    consistencyService // 临时实例
            .listen(KeyBuilder.buildInstanceListKey(service.getNamespaceId(), service.getName(), true), service);
    consistencyService // 永久实例
            .listen(KeyBuilder.buildInstanceListKey(service.getNamespaceId(), service.getName(), false), service);
    Loggers.SRV_LOG.info("[NEW-SERVICE] {}", service.toJson());
}
```

2.2 服务创建好之后，执行`addInstance`将实例添加进到`Service`中

```java
public void addInstance(String namespaceId, String serviceName, boolean ephemeral, Instance... ips)
        throws NacosException {
    // 给当前服务生成一个唯一标识，ephemeral表示是否是临时实例，如果ephemeral为true，则这个key的前缀中会多出一个"ephemeral."
    String key = KeyBuilder.buildInstanceListKey(namespaceId, serviceName, ephemeral);
    // 从注册表拿到Service
    Service service = getService(namespaceId, serviceName);
    // 如果是同一个服务，那么多个实例会一个个的注册
    synchronized (service) {
        // 拷贝注册表中旧的实例列表，结合新的实例，得到最终实例列表
        List<Instance> instanceList = addIpAddresses(service, ephemeral, ips);

        // Instances类内部有一个List<Instance>，封装新实例列表
        Instances instances = new Instances();
        instances.setInstanceList(instanceList);

        // 更新注册表（更新本地注册表，同步给nacos集群中其他节点）
        consistencyService.put(key, instances);
    }
}
```

2.2.1 执行`consistencyService.put(key, instances);`更新注册表

```java
@Override
public void put(String key, Record value) throws NacosException {
    // 异步，更新本地注册表
    onPut(key, value);
    // 异步，将数据同步给nacos集群中其他节点
    distroProtocol.sync(new DistroKey(key, KeyBuilder.INSTANCE_LIST_KEY_PREFIX), DataOperation.CHANGE,
            globalConfig.getTaskDispatchPeriod() / 2);
}
```

- `onPut`方法：先判断是否是临时实例，是的话就将实例列表封装为`Datum`对象，然后缓存起来，再将这个任务添加到一个阻塞队列中，使用异步线程池【单线程】去执行这个任务

  ```java
  public void onPut(String key, Record value) {
      // 判断是否是临时实例
      if (KeyBuilder.matchEphemeralInstanceListKey(key)) {
          // 将实例列表封装到Datum
          Datum<Instances> datum = new Datum<>();
          datum.value = (Instances) value;
          datum.key = key;
          datum.timestamp.incrementAndGet();
          // 以ServiceId为key，Datum为值，缓存起来
          dataStore.put(key, datum);
      }
  
      if (!listeners.containsKey(key)) {
          return;
      }
      // 把serviceId和 当前的操作类型 存入notifier
      notifier.addTask(key, DataOperation.CHANGE);
  }
  
  public void addTask(String datumKey, DataOperation action) {
      if (services.containsKey(datumKey) && action == DataOperation.CHANGE) {
          return;
      }
      if (action == DataOperation.CHANGE) {
          services.put(datumKey, StringUtils.EMPTY);
      }
      // 把serviceId和操作事件 放入阻塞队列
      tasks.offer(Pair.with(datumKey, action));
  }
  
  // DistroConsistencyServiceImpl类的构造方法，是提交一个任务
  @PostConstruct
  public void init() {
      // 利用线程池，执行notifier，notifier实现了Runnable接口，是可执行的
      GlobalExecutor.submitDistroNotifyTask(notifier);
  }
  
  // notifier的run方法
  @Override
  public void run() {
      Loggers.DISTRO.info("distro notifier started");
  
      for (; ; ) {
          try {
              // 从阻塞队列获取任务，因为是阻塞队列，所以不会占用cpu
              Pair<String, DataOperation> pair = tasks.take();
              // 执行任务
              handle(pair);
          } catch (Throwable e) {
              Loggers.DISTRO.error("[NACOS-DISTRO] Error while handling notifying task", e);
          }
      }
  }
  
  // handle方法，通过serviceId去dataStore中获取我们要更新的实例列表，然后执行操作
  private void handle(Pair<String, DataOperation> pair) {
      try {
          // 获得serviceId
          String datumKey = pair.getValue0();
          // 获得操作事件
          DataOperation action = pair.getValue1();
          services.remove(datumKey);
          int count = 0;
          if (!listeners.containsKey(datumKey)) {
              return;
          }
          // 这个listener就是我们的service，因为前边通过consistencyService.listen已经注册进来了
          for (RecordListener listener : listeners.get(datumKey)) {
              count++;
              try {
                  // 如果是CHANGE操作，那么就执行服务注册
                  if (action == DataOperation.CHANGE) {
                      listener.onChange(datumKey, dataStore.get(datumKey).value);
                      continue;
                  }
                  if (action == DataOperation.DELETE) {
                      listener.onDelete(datumKey);
                      continue;
                  }
              } catch (Throwable e) {
                  Loggers.DISTRO.error("[NACOS-DISTRO] error while notifying listener of key: {}", datumKey, e);
              }
          }
          ...
      } catch (Throwable e) {
          Loggers.DISTRO.error("[NACOS-DISTRO] Error while handling notifying task", e);
      }
  }
  ```

- `distroProtocol.sync`：获取集群中除了自己的所有节点，对每个节点创建一个任务，放入延迟队列中，使用异步线程去执行

  ```java
  public void sync(DistroKey distroKey, DataOperation action, long delay) {
      // 获取Nacos集群中所有节点，除了我自己，然后遍历
      for (Member each : memberManager.allMembersWithoutSelf()) {
          DistroKey distroKeyWithTarget = new DistroKey(distroKey.getResourceKey(), distroKey.getResourceType(),
                  each.getAddress());
          // 创建一个延迟任务，包含了当前要更新的任务
          DistroDelayTask distroDelayTask = new DistroDelayTask(distroKeyWithTarget, action, delay);
          // 异步的执行这个任务
          distroTaskEngineHolder.getDelayTaskExecuteEngine().addTask(distroKeyWithTarget, distroDelayTask);
          if (Loggers.DISTRO.isDebugEnabled()) {
              Loggers.DISTRO.debug("[DISTRO-SCHEDULE] {} to {}", distroKey, each.getAddress());
          }
      }
  }
  ```







### 4、Nacos避免并发读写冲突

Nacos在更新实例列表时，会采用`CopyOnWrite`技术，首先将旧的实例列表拷贝一份，然后更新拷贝的实例列表，再用更新后的实例列表来覆盖旧的实例列表。

这样在更新的过程中，就不会对读实例列表的请求产生影响，也不会出现脏读问题了。

而在写的过程中，使用`Synchronize`对`service`加锁，保证只有一个线程对同一个服务执行写操作



1、`handler`方法

```java
private void handle(Pair<String, DataOperation> pair) {
    try {
        // 获得serviceId
        String datumKey = pair.getValue0();
        // 获得操作事件
        DataOperation action = pair.getValue1();
        services.remove(datumKey);
        int count = 0;
        if (!listeners.containsKey(datumKey)) {
            return;
        }
        // 这个listener就是我们的service，因为前边通过consistencyService.listen已经注册进来了
        for (RecordListener listener : listeners.get(datumKey)) {
            count++;
            try {
                // 如果是CHANGE操作，那么就执行服务注册
                if (action == DataOperation.CHANGE) {
                    listener.onChange(datumKey, dataStore.get(datumKey).value);
                    continue;
                }
                if (action == DataOperation.DELETE) {
                    listener.onDelete(datumKey);
                    continue;
                }
            } catch (Throwable e) {
                Loggers.DISTRO.error("[NACOS-DISTRO] error while notifying listener of key: {}", datumKey, e);
            }
        }
        ...
    } catch (Throwable e) {
        Loggers.DISTRO.error("[NACOS-DISTRO] Error while handling notifying task", e);
    }
}
```

2、`onChange`方法

```java
@Override
public void onChange(String key, Instances value) throws Exception {
    
    Loggers.SRV_LOG.info("[NACOS-RAFT] datum is changed, key: {}, value: {}", key, value);
    
    // value.getInstanceList()就是前边拷贝的旧的实例和新的实例的合并，这里是对这个合并的实例列表（新创建的）进行权值调整
    for (Instance instance : value.getInstanceList()) {
        
        if (instance == null) {
            // Reject this abnormal instance list:
            throw new RuntimeException("got null instance " + key);
        }
        
        if (instance.getWeight() > 10000.0D) {
            instance.setWeight(10000.0D);
        }
        
        if (instance.getWeight() < 0.01D && instance.getWeight() > 0.0D) {
            instance.setWeight(0.01D);
        }
    }
    // 更新
    updateIPs(value.getInstanceList(), KeyBuilder.matchEphemeralInstanceListKey(key));
    
    recalculateChecksum();
}
```

3、`updateIPs`方法

```java
// instances是旧实例和新实例的合并列表
public void updateIPs(Collection<Instance> instances, boolean ephemeral) {
   // 创建一个新的ipMap，key是集群名，value是该集群的实例列表 
    Map<String, List<Instance>> ipMap = new HashMap<>(clusterMap.size());
    // 遍历旧的集群列表，将集群名设置到ipMap中
    for (String clusterName : clusterMap.keySet()) {
        ipMap.put(clusterName, new ArrayList<>());
    }
    
    // 遍历每个实例，遍历完后，ipMaps中就包含了当前需要更新的全部实例
    for (Instance instance : instances) {
        try {
            if (instance == null) {
                Loggers.SRV_LOG.error("[NACOS-DOM] received malformed ip: null");
                continue;
            }
            // 如果这个实例的集群名为空，就设置为默认的 "DEFAULT"
            if (StringUtils.isEmpty(instance.getClusterName())) {
                instance.setClusterName(UtilsAndCommons.DEFAULT_CLUSTER_NAME);
            }
            // 如果当前的注册表中没有这个集群名，那么就创建一个新的集群，并添加进去
            if (!clusterMap.containsKey(instance.getClusterName())) {
                Loggers.SRV_LOG
                        .warn("cluster: {} not found, ip: {}, will create new cluster with default configuration.",
                                instance.getClusterName(), instance.toJson());
                Cluster cluster = new Cluster(instance.getClusterName(), this);
                cluster.init();
                getClusterMap().put(instance.getClusterName(), cluster);
            }
            
            // 通过集群名拿到ipMap中的实例列表，如果为空，就新建一个
            List<Instance> clusterIPs = ipMap.get(instance.getClusterName());
            if (clusterIPs == null) {
                clusterIPs = new LinkedList<>();
                ipMap.put(instance.getClusterName(), clusterIPs);
            }
            // 将当前实例添加进去
            clusterIPs.add(instance);
        } catch (Exception e) {
            Loggers.SRV_LOG.error("[NACOS-DOM] failed to process ip: " + instance, e);
        }
    }
    
    // 遍历ipMap，每个entry的key是集群名，value是实例列表
    for (Map.Entry<String, List<Instance>> entry : ipMap.entrySet()) {
        //make every ip mine
        List<Instance> entryIPs = entry.getValue();
        // 更新
        clusterMap.get(entry.getKey()).updateIps(entryIPs, ephemeral);
    }
    
    setLastModifiedMillis(System.currentTimeMillis());
    getPushService().serviceChanged(this);
    StringBuilder stringBuilder = new StringBuilder();
    
    for (Instance instance : allIPs()) {
        stringBuilder.append(instance.toIpAddr()).append("_").append(instance.isHealthy()).append(",");
    }
    
    Loggers.EVT_LOG.info("[IP-UPDATED] namespace: {}, service: {}, ips: {}", getNamespaceId(), getName(),
            stringBuilder.toString());
    
}
```

4、`updateIps`方法

```java
// ips是当前最新的实例列表
public void updateIps(List<Instance> ips, boolean ephemeral) {
    // 判断是否是临时实例，拿到本地实例列表
    Set<Instance> toUpdateInstances = ephemeral ? ephemeralInstances : persistentInstances;
    
    HashMap<String, Instance> oldIpMap = new HashMap<>(toUpdateInstances.size());
    
    // 将本地实例列表的数据都拷贝到oldIpMap中
    for (Instance ip : toUpdateInstances) {
        oldIpMap.put(ip.getDatumKey(), ip);
    }
    // 拿到需要更新的实例列表
    List<Instance> updatedIPs = updatedIps(ips, oldIpMap.values());
    if (updatedIPs.size() > 0) { 
        for (Instance ip : updatedIPs) {
            Instance oldIP = oldIpMap.get(ip.getDatumKey());
            
            // do not update the ip validation status of updated ips
            // because the checker has the most precise result
            // Only when ip is not marked, don't we update the health status of IP:
            if (!ip.isMarked()) {
                ip.setHealthy(oldIP.isHealthy());
            }

            if (ip.isHealthy() != oldIP.isHealthy()) {
                // ip validation status updated
                Loggers.EVT_LOG.info("{} {SYNC} IP-{} {}:{}@{}", getService().getName(),
                        (ip.isHealthy() ? "ENABLED" : "DISABLED"), ip.getIp(), ip.getPort(), getName());
            }
            
            if (ip.getWeight() != oldIP.getWeight()) {
                // ip validation status updated
                Loggers.EVT_LOG.info("{} {SYNC} {IP-UPDATED} {}->{}", getService().getName(), oldIP.toString(),
                        ip.toString());
            }
        }
    }
    // 拿到需要添加的实例列表
    List<Instance> newIPs = subtract(ips, oldIpMap.values());
    if (newIPs.size() > 0) {
        Loggers.EVT_LOG
                .info("{} {SYNC} {IP-NEW} cluster: {}, new ips size: {}, content: {}", getService().getName(),
                        getName(), newIPs.size(), newIPs.toString());
        
        for (Instance ip : newIPs) {
            HealthCheckStatus.reset(ip);
        }
    }
    // 拿到需要删除的实例列表
    List<Instance> deadIPs = subtract(oldIpMap.values(), ips);
    
    if (deadIPs.size() > 0) {
        Loggers.EVT_LOG
                .info("{} {SYNC} {IP-DEAD} cluster: {}, dead ips size: {}, content: {}", getService().getName(),
                        getName(), deadIPs.size(), deadIPs.toString());
        
        for (Instance ip : deadIPs) {
            HealthCheckStatus.remv(ip);
        }
    }
    
    toUpdateInstances = new HashSet<>(ips);
    // 直接将旧实例数据替换为新实例数据
    if (ephemeral) {
        ephemeralInstances = toUpdateInstances;
    } else {
        persistentInstances = toUpdateInstances;
    }
}
```





### 5、Nacos与Eureka的区别

- **接口方式**：`Nacos`与Eureka都对外暴露了Rest风格的API接口，用来实现服务注册、发现等功能
- **实例类型**：`Nacos`的实例有永久和临时实例之分；而Eureka只支持临时实例
- **健康检测**：`Nacos`对临时实例采用心跳模式检测【`Nacos`客户端定时发送心跳请求给服务端】，对永久实例采用主动请求来检测【服务端定时检测】；Eureka只支持心跳模式
- **服务发现**：`Nacos`支持定时拉取【客户端定时向`Nacos`服务端发送请求，以获取所有的服务】和订阅推送【当服务变更或者不健康时，服务端都会发送一个服务变更的通知`getPushService().serviceChanged(service);`  服务端会监听这个通知，监听到后会通知所有监听这个服务的微服务，然后把最新的服务返回，并放在一个缓存表中，下次可以直接从缓存获取】两种模式；Eureka只支持定时拉取模式



#### 5.1 Nacos客户端注册流程

> Nacos服务端接收注册请求地址为`/nacos/v1/ns/instance`，`post`请求
>
> 我们的模块引入`spring-cloud-starter-alibaba-nacos-discovery`依赖后，就会通过`NacosServiceRegistryAutoConfiguration`为我们实现`Nacos`服务注册的自动装配

1、`NacosServiceRegistryAutoConfiguration`

- 注册了`NacosAutoServiceRegistration`的bean对象
  - `NacosAutoServiceRegistration`继承自`AbstractAutoServiceRegistration`
  - `AbstractAutoServiceRegistration`实现了`ApplicationListener<WebServerInitializedEvent>`，监听web服务的初始化事件
- `ApplicationListener<WebServerInitializedEvent>`类中的`bind`方法来实现服务注册
- `bind`方法执行`start()`方法
- `start()`方法执行`register()`方法

```java
public class NacosServiceRegistryAutoConfiguration {
	...
    @Bean
    @ConditionalOnBean({AutoServiceRegistrationProperties.class})
    public NacosAutoServiceRegistration nacosAutoServiceRegistration(NacosServiceRegistry registry, AutoServiceRegistrationProperties autoServiceRegistrationProperties, NacosRegistration registration) {
        // Nacos服务自动注册
        return new NacosAutoServiceRegistration(registry, autoServiceRegistrationProperties, registration);
    }
}

// NacosAutoServiceRegistration这个类继承了AbstractAutoServiceRegistration
// AbstractAutoServiceRegistration实现了ApplicationListener<WebServerInitializedEvent>，监听web服务的初始化
public class NacosAutoServiceRegistration extends AbstractAutoServiceRegistration<Registration> {
    private static final Logger log = LoggerFactory.getLogger(NacosAutoServiceRegistration.class);
    private NacosRegistration registration;
 	...
}

public abstract class AbstractAutoServiceRegistration<R extends Registration>
		implements AutoServiceRegistration, ApplicationContextAware,
		ApplicationListener<WebServerInitializedEvent> {
        ...
        @Deprecated
	public void bind(WebServerInitializedEvent event) {
		ApplicationContext context = event.getApplicationContext();
		if (context instanceof ConfigurableWebServerApplicationContext) {
			if ("management".equals(((ConfigurableWebServerApplicationContext) context)
					.getServerNamespace())) {
				return;
			}
		}
		this.port.compareAndSet(0, event.getWebServer().getPort());
        // 执行start方法
		this.start();
	}
        ...
}

public void start() {
    if (!isEnabled()) {
        if (logger.isDebugEnabled()) {
            logger.debug("Discovery Lifecycle disabled. Not starting");
        }
        return;
    }

    // only initialize if nonSecurePort is greater than 0 and it isn't already running
    // because of containerPortInitializer below
    if (!this.running.get()) {
        this.context.publishEvent(
            new InstancePreRegisteredEvent(this, getRegistration()));
        // 通过register方法实现服务注册
        register();
        if (shouldRegisterManagement()) {
            registerManagement();
        }
        this.context.publishEvent(
            new InstanceRegisteredEvent<>(this, getConfiguration()));
        this.running.compareAndSet(false, true);
    }

}
```



2、`NacosServiceRegistry`的`register`方法

- 获取`Service`服务、`serviceId`、`group`、实例，进行实例的注册
- 拼接`groupName`和`serviceName`，如果是临时实例，客户端还需要发送心跳检测，然后进行实例注册
- `registerService`方法就是向`Nacos`服务端发送post请求来注册实例

```java
public void register(Registration registration) {
    if (StringUtils.isEmpty(registration.getServiceId())) {
        log.warn("No service to register for nacos client...");
    } else {
        // 获取Service服务
        NamingService namingService = this.namingService();
        // 获取serviceId
        String serviceId = registration.getServiceId();
        // 获取group名
        String group = this.nacosDiscoveryProperties.getGroup();
        // 获取实例
        Instance instance = this.getNacosInstanceFromRegistration(registration);

        try {
            // 注册
            namingService.registerInstance(serviceId, group, instance);
            log.info("nacos registry, {} {} {}:{} register finished", new Object[]{group, serviceId, instance.getIp(), instance.getPort()});
        } catch (Exception var7) {
            if (this.nacosDiscoveryProperties.isFailFast()) {
                log.error("nacos registry, {} register failed...{},", new Object[]{serviceId, registration.toString(), var7});
                ReflectionUtils.rethrowRuntimeException(var7);
            } else {
                log.warn("Failfast is false. {} register failed...{},", new Object[]{serviceId, registration.toString(), var7});
            }
        }

    }
}

@Override
public void registerInstance(String serviceName, String groupName, Instance instance) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);
    // 拼接groupName和serviceName，拼接为groupName@@serviceName
    String groupedServiceName = NamingUtils.getGroupedName(serviceName, groupName);
    // 如果是临时实例，客户端需要发送心跳检测
    if (instance.isEphemeral()) {
        BeatInfo beatInfo = beatReactor.buildBeatInfo(groupedServiceName, instance);
        beatReactor.addBeatInfo(groupedServiceName, beatInfo);
    }
    // 注册服务
    serverProxy.registerService(groupedServiceName, groupName, instance);
}

// 这里就是向服务端发送一个post请求来注册实例
public void registerService(String serviceName, String groupName, Instance instance) throws NacosException {
        
    NAMING_LOGGER.info("[REGISTER-SERVICE] {} registering service {} with instance: {}", namespaceId, serviceName,
                       instance);

    // 准备请求参数
    final Map<String, String> params = new HashMap<String, String>(16);
    params.put(CommonParams.NAMESPACE_ID, namespaceId);
    params.put(CommonParams.SERVICE_NAME, serviceName);
    params.put(CommonParams.GROUP_NAME, groupName);
    params.put(CommonParams.CLUSTER_NAME, instance.getClusterName());
    params.put("ip", instance.getIp());
    params.put("port", String.valueOf(instance.getPort()));
    params.put("weight", String.valueOf(instance.getWeight()));
    params.put("enable", String.valueOf(instance.isEnabled()));
    params.put("healthy", String.valueOf(instance.isHealthy()));
    params.put("ephemeral", String.valueOf(instance.isEphemeral()));
    params.put("metadata", JacksonUtils.toJson(instance.getMetadata()));

    // 发送一个post请求，请求地址UtilAndComs.nacosUrlInstance就是/nacos/v1/ns/instance
    reqApi(UtilAndComs.nacosUrlInstance, params, HttpMethod.POST);
}
```





#### 5.2 临时实例心跳检测

**客户端发送心跳请求**

1、注册实例时的`registerInstance`方法，如果当前实例时临时实例，那么需要构建心跳信息，然后通过`addBeatInfo`发送

```java
@Override
public void registerInstance(String serviceName, String groupName, Instance instance) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);
    // 拼接groupName和serviceName，拼接为groupName@@serviceName
    String groupedServiceName = NamingUtils.getGroupedName(serviceName, groupName);
    // 如果是临时实例，客户端需要发送心跳检测
    if (instance.isEphemeral()) {
        // 构建心跳信息，服务名、ip、端口、集群名、是否为定时任务、心跳周期（默认5s）
        BeatInfo beatInfo = beatReactor.buildBeatInfo(groupedServiceName, instance);
        beatReactor.addBeatInfo(groupedServiceName, beatInfo);
    }
    // 注册服务
    serverProxy.registerService(groupedServiceName, groupName, instance);
}
```

2、`addBeatInfo`方法

```java
public void addBeatInfo(String serviceName, BeatInfo beatInfo) {
    NAMING_LOGGER.info("[BEAT] adding beat: {} to beat map.", beatInfo);
    // 构建出一个key
    String key = buildKey(serviceName, beatInfo.getIp(), beatInfo.getPort());
    BeatInfo existBeat = null;
    //fix #1733
    if ((existBeat = dom2Beat.remove(key)) != null) {
        existBeat.setStopped(true);
    }
    // 将心跳信息放入一个map中
    dom2Beat.put(key, beatInfo);
    // 开启一个定时任务，new BeatTask(beatInfo)就是心跳任务，beatInfo.getPeriod()为5s，延迟5s执行
    executorService.schedule(new BeatTask(beatInfo), beatInfo.getPeriod(), TimeUnit.MILLISECONDS);
    MetricsMonitor.getDom2BeatSizeMonitor().set(dom2Beat.size());
}
```

3、`BeatTask`的run方法

```java
@Override
public void run() {
    if (beatInfo.isStopped()) {
        return;
    }
    long nextTime = beatInfo.getPeriod();
    try {
        // 把心跳信息beatInfo发送出去
        JsonNode result = serverProxy.sendBeat(beatInfo, BeatReactor.this.lightBeatEnabled);
		...
    }


// 通过sendBeat方法将心跳信息发送出去，请求方式为PUT，请求地址为/nacos/v1/ns/instance/beat，这个地址就是服务端接收心跳请求的地址
public JsonNode sendBeat(BeatInfo beatInfo, boolean lightBeatEnabled) throws NacosException {  
    if (NAMING_LOGGER.isDebugEnabled()) {
        NAMING_LOGGER.debug("[BEAT] {} sending beat to server: {}", namespaceId, beatInfo.toString());
    }
    Map<String, String> params = new HashMap<String, String>(8);
    Map<String, String> bodyMap = new HashMap<String, String>(2);
    if (!lightBeatEnabled) {
        // 将心跳信息转为json字符串
        bodyMap.put("beat", JacksonUtils.toJson(beatInfo));
    }
    params.put(CommonParams.NAMESPACE_ID, namespaceId);
    params.put(CommonParams.SERVICE_NAME, beatInfo.getServiceName());
    params.put(CommonParams.CLUSTER_NAME, beatInfo.getCluster());
    params.put("ip", beatInfo.getIp());
    params.put("port", String.valueOf(beatInfo.getPort()));
    String result = reqApi(UtilAndComs.nacosUrlBase + "/instance/beat", params, bodyMap, HttpMethod.PUT);
    return JacksonUtils.toObj(result);
}
```





**服务端接收心跳请求**

1、接收到客户端发来的心跳请求

```java
@CanDistro
@PutMapping("/beat")
@Secured(parser = NamingResourceParser.class, action = ActionTypes.WRITE)
public ObjectNode beat(HttpServletRequest request) throws Exception {

    ObjectNode result = JacksonUtils.createEmptyJsonNode();
    result.put(SwitchEntry.CLIENT_BEAT_INTERVAL, switchDomain.getClientBeatInterval());
    // 拿到心跳的信息
    String beat = WebUtils.optional(request, "beat", StringUtils.EMPTY);
    RsInfo clientBeat = null;
    if (StringUtils.isNotBlank(beat)) {
        // 将心跳信息反序列化为clientBeat
        clientBeat = JacksonUtils.toObj(beat, RsInfo.class);
    }
    // 获得集群名称
    String clusterName = WebUtils
            .optional(request, CommonParams.CLUSTER_NAME, UtilsAndCommons.DEFAULT_CLUSTER_NAME);
    String ip = WebUtils.optional(request, "ip", StringUtils.EMPTY);
    int port = Integer.parseInt(WebUtils.optional(request, "port", "0"));
    if (clientBeat != null) {
        if (StringUtils.isNotBlank(clientBeat.getCluster())) {
            clusterName = clientBeat.getCluster();
        } else {
            // fix #2533
            clientBeat.setCluster(clusterName);
        }
        ip = clientBeat.getIp();
        port = clientBeat.getPort();
    }
    String namespaceId = WebUtils.optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
    String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
    NamingUtils.checkServiceNameFormat(serviceName);
    Loggers.SRV_LOG.debug("[CLIENT-BEAT] full arguments: beat: {}, serviceName: {}", clientBeat, serviceName);
    // 获取执行心跳的这个实例
    Instance instance = serviceManager.getInstance(namespaceId, serviceName, clusterName, ip, port);
    // 如果这个实例为空，则需要重新注册
    if (instance == null) {
        if (clientBeat == null) {
            result.put(CommonParams.CODE, NamingResponseCode.RESOURCE_NOT_FOUND);
            return result;
        }

        Loggers.SRV_LOG.warn("[CLIENT-BEAT] The instance has been removed for health mechanism, "
                + "perform data compensation operations, beat: {}, serviceName: {}", clientBeat, serviceName);

        instance = new Instance();
        instance.setPort(clientBeat.getPort());
        instance.setIp(clientBeat.getIp());
        instance.setWeight(clientBeat.getWeight());
        instance.setMetadata(clientBeat.getMetadata());
        instance.setClusterName(clusterName);
        instance.setServiceName(serviceName);
        instance.setInstanceId(instance.getInstanceId());
        instance.setEphemeral(clientBeat.isEphemeral());

        serviceManager.registerInstance(namespaceId, serviceName, instance);
    }
    // 获取该实例的服务
    Service service = serviceManager.getService(namespaceId, serviceName);

    if (service == null) {
        throw new NacosException(NacosException.SERVER_ERROR,
                "service not found: " + serviceName + "@" + namespaceId);
    }
    if (clientBeat == null) {
        clientBeat = new RsInfo();
        clientBeat.setIp(ip);
        clientBeat.setPort(port);
        clientBeat.setCluster(clusterName);
    }
    // 通过Service来处理心跳请求
    service.processClientBeat(clientBeat);

    result.put(CommonParams.CODE, NamingResponseCode.OK);
    if (instance.containsMetadata(PreservedMetadataKeys.HEART_BEAT_INTERVAL)) {
        result.put(SwitchEntry.CLIENT_BEAT_INTERVAL, instance.getInstanceHeartBeatInterval());
    }
    result.put(SwitchEntry.LIGHT_BEAT_ENABLED, switchDomain.isLightBeatEnabled());
    return result;
}
```

2、`service.processClientBeat(clientBeat);`处理心跳请求

```java
public void processClientBeat(final RsInfo rsInfo) {
    ClientBeatProcessor clientBeatProcessor = new ClientBeatProcessor();
    clientBeatProcessor.setService(this);
    clientBeatProcessor.setRsInfo(rsInfo);
    // 执行一个定时任务，clientBeatProcessor实现了Runnable接口
    HealthCheckReactor.scheduleNow(clientBeatProcessor);
}
```

3、`ClientBeatProcessor`的`run`方法

```java
@Override
public void run() {
    Service service = this.service;
    if (Loggers.EVT_LOG.isDebugEnabled()) {
        Loggers.EVT_LOG.debug("[CLIENT-BEAT] processing beat: {}", rsInfo.toString());
    }

    String ip = rsInfo.getIp();
    String clusterName = rsInfo.getCluster();
    int port = rsInfo.getPort();
    Cluster cluster = service.getClusterMap().get(clusterName);
    List<Instance> instances = cluster.allIPs(true);

    for (Instance instance : instances) {
        // 判断实例的ip、端口与心跳的实例是否一致，如果一致说明是当前实例的心跳
        if (instance.getIp().equals(ip) && instance.getPort() == port) {
            if (Loggers.EVT_LOG.isDebugEnabled()) {
                Loggers.EVT_LOG.debug("[CLIENT-BEAT] refresh beat: {}", rsInfo.toString());
            }
            // 更新心跳时间
            instance.setLastBeat(System.currentTimeMillis());
            if (!instance.isMarked()) {
                // 如果当前实例时不健康的，那么心跳请求来了，说明目前健康，就将其状态改为健康
                if (!instance.isHealthy()) {
                    instance.setHealthy(true);
                    Loggers.EVT_LOG
                            .info("service: {} {POS} {IP-ENABLED} valid: {}:{}@{}, region: {}, msg: client beat ok",
                                    cluster.getService().getName(), ip, port, cluster.getName(),
                                    UtilsAndCommons.LOCALHOST_SITE);
                    // 服务状态变更的事件
                    getPushService().serviceChanged(service);
                }
            }
        }
    }
```





**服务端判断实例不健康**

1、服务端创建服务并初始化时`service.init();`方法

```java
public void init() {
    // 执行一个定时任务，5s后执行，clientBeatCheckTask实现了Runnable接口
    HealthCheckReactor.scheduleCheck(clientBeatCheckTask);
    for (Map.Entry<String, Cluster> entry : clusterMap.entrySet()) {
        entry.getValue().setService(this);
        entry.getValue().init();
    }
}
```

2、`clientBeatCheckTask`的`run`方法

```java
@Override
public void run() {
    try {
        ...
        // 获取所有实例
        List<Instance> instances = service.allIPs(true);

        // 遍历实例，标记不健康实例
        for (Instance instance : instances) {
            // 实例的最后一次心跳时间距离现在的时间检测，如果大于15s
            if (System.currentTimeMillis() - instance.getLastBeat() > instance.getInstanceHeartBeatTimeOut()) {
                if (!instance.isMarked()) {
                    // 标记实例为不健康
                    if (instance.isHealthy()) {
                        instance.setHealthy(false);
                        Loggers.EVT_LOG
                                .info("{POS} {IP-DISABLED} valid: {}:{}@{}@{}, region: {}, msg: client timeout after {}, last beat: {}",
                                        instance.getIp(), instance.getPort(), instance.getClusterName(),
                                        service.getName(), UtilsAndCommons.LOCALHOST_SITE,
                                        instance.getInstanceHeartBeatTimeOut(), instance.getLastBeat());
                        // 发布服务状态变更的通知
                        getPushService().serviceChanged(service);
                        ApplicationUtils.publishEvent(new InstanceHeartbeatTimeoutEvent(this, instance));
                    }
                }
            }
        }
		...

        // 遍历实例
        for (Instance instance : instances) {

            if (instance.isMarked()) {
                continue;
            }
            // 实例的最后一次心跳时间距离现在的时间检测，如果大于30s
            if (System.currentTimeMillis() - instance.getLastBeat() > instance.getIpDeleteTimeout()) {
                // delete instance
                Loggers.SRV_LOG.info("[AUTO-DELETE-IP] service: {}, ip: {}", service.getName(),
                        JacksonUtils.toJson(instance));
                // 将该实例从服务列表删除
                deleteIp(instance);
            }
        }

    } catch (Exception e) {
        Loggers.SRV_LOG.warn("Exception while processing client beat time out.", e);
    }

}
```

3、`deleteIp`方法，发送一个异步的http请求，将实例从注册表删除

```java
private void deleteIp(Instance instance) {

    try {
        NamingProxy.Request request = NamingProxy.Request.newRequest();
        request.appendParam("ip", instance.getIp()).appendParam("port", String.valueOf(instance.getPort()))
                .appendParam("ephemeral", "true").appendParam("clusterName", instance.getClusterName())
                .appendParam("serviceName", service.getName()).appendParam("namespaceId", service.getNamespaceId());

        String url = "http://" + IPUtil.localHostIP() + IPUtil.IP_PORT_SPLITER + EnvUtil.getPort() + EnvUtil.getContextPath()
                + UtilsAndCommons.NACOS_NAMING_CONTEXT + "/instance?" + request.toUrl();

        // 发送一个异步的delete请求，删除实例
        HttpClient.asyncHttpDelete(url, null, null, new Callback<String>() {
            @Override
            public void onReceive(RestResult<String> result) {
                if (!result.ok()) {
                    Loggers.SRV_LOG
                            .error("[IP-DEAD] failed to delete ip automatically, ip: {}, caused {}, resp code: {}",
                                    instance.toJson(), result.getMessage(), result.getCode());
                }
            }

            @Override
            public void onError(Throwable throwable) {
                Loggers.SRV_LOG
                        .error("[IP-DEAD] failed to delete ip automatically, ip: {}, error: {}", instance.toJson(),
                                throwable);
            }

            @Override
            public void onCancel() {

            }
        });

    } catch (Exception e) {
        Loggers.SRV_LOG
                .error("[IP-DEAD] failed to delete ip automatically, ip: {}, error: {}", instance.toJson(), e);
    }
}
```





**非临时实例**

1、服务端创建服务并初始化时`service.init();`方法

```java
public void init() {
    // 初始化服务，开启心跳健康检测
    HealthCheckReactor.scheduleCheck(clientBeatCheckTask);
    for (Map.Entry<String, Cluster> entry : clusterMap.entrySet()) {
        entry.getValue().setService(this);
        // 对集群初始化
        entry.getValue().init();
    }
}
```

2、`entry.getValue().init();`

```java
public void init() {
    if (inited) {
        return;
    }
    checkTask = new HealthCheckTask(this);
    // 执行定时任务，checkTask实现了Runnable接口
    HealthCheckReactor.scheduleCheck(checkTask);
    inited = true;
}
```

3、`HealthCheckTask`的run方法

```java
@Override
public void run() {
    
    try {
        if (distroMapper.responsible(cluster.getService().getName()) && switchDomain
                .isHealthCheckEnabled(cluster.getService().getName())) {
            // 处理任务
            healthCheckProcessor.process(this);
            if (Loggers.EVT_LOG.isDebugEnabled()) {
                Loggers.EVT_LOG
                        .debug("[HEALTH-CHECK] schedule health check task: {}", cluster.getService().getName());
            }
        }
```

4、`TcpSuperSenseProcessor`类的`process`方法，这个类也实现了Runnable接口

```java
@Override
public void process(HealthCheckTask task) {
    // 得到集群中所有实例的ip
    List<Instance> ips = task.getCluster().allIPs(false);

    if (CollectionUtils.isEmpty(ips)) {
        return;
    }

    for (Instance ip : ips) {

        if (ip.isMarked()) {
            if (SRV_LOG.isDebugEnabled()) {
                SRV_LOG.debug("tcp check, ip is marked as to skip health check, ip:" + ip.getIp());
            }
            continue;
        }

        if (!ip.markChecking()) {
            SRV_LOG.warn("tcp check started before last one finished, service: " + task.getCluster().getService()
                    .getName() + ":" + task.getCluster().getName() + ":" + ip.getIp() + ":" + ip.getPort());

            healthCheckCommon
                    .reEvaluateCheckRT(task.getCheckRtNormalized() * 2, task, switchDomain.getTcpHealthParams());
            continue;
        }
        // 把当前实例的ip和健康检测的任务放入beat，然后将beat放入阻塞队列
        Beat beat = new Beat(ip, task);
        taskQueue.add(beat);
        MetricsMonitor.getTcpHealthCheckMonitor().incrementAndGet();
    }
}
```

5、`TcpSuperSenseProcessor`的run方法

```java
@Override
public void run() {
    while (true) {
        try {
            // 处理任务
            processTask();

            int readyCount = selector.selectNow();
            if (readyCount <= 0) {
                continue;
            }
			
            Iterator<SelectionKey> iter = selector.selectedKeys().iterator();
            while (iter.hasNext()) {
                SelectionKey key = iter.next();
                iter.remove();

                GlobalExecutor.executeTcpSuperSense(new PostProcessor(key));
            }
        } catch (Throwable e) {
            SRV_LOG.error("[HEALTH-CHECK] error while processing NIO task", e);
        }
    }
}

private void processTask() throws Exception {
    Collection<Callable<Void>> tasks = new LinkedList<>();
    do {
        // 从阻塞队列中取出任务执行
        Beat beat = taskQueue.poll(CONNECT_TIMEOUT_MS / 2, TimeUnit.MILLISECONDS);
        if (beat == null) {
            return;
        }

        tasks.add(new TaskProcessor(beat));
    } while (taskQueue.size() > 0 && tasks.size() < NIO_THREAD_COUNT * 64);

    for (Future<?> f : GlobalExecutor.invokeAllTcpSuperSenseTask(tasks)) {
        f.get();
    }
}
```









## 八、Sentinel原理

Sentinel实现限流、隔离、降级、熔断等功能，本质要做的就是两件事情：

- **统计数据**：统计某个资源的访问数据（QPS、RT等信息），**资源**就是希望被Sentinel保护的业务，例如项目中定义的controller方法就是默认被Sentinel保护的资源。
- **规则判断**：判断限流规则、隔离规则、降级规则、熔断规则是否满足





### 1、ProcessorSlotChain

实现上述功能的核心骨架是`ProcessorSlotChain`类。这个类基于责任链模式来设计，将不同的功能（限流、降级、系统保护）封装为一个个的Slot，请求进入后逐个执行即可。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181125830.png" style="zoom:60%;" />

责任链中的Slot分为两大类：

- 统计数据构建部分（statistic）
  - `NodeSelectorSlot`：负责构建簇点链路中的节点（`DefaultNode`），将这些节点形成链路树，ROOT是根节点，每个微服务都有一个ROOT，Entrance可以理解为Controller资源，如果Service也受保护，那么Default Node就是Service资源
  - `ClusterBuilderSlot`：负责构建某个资源的`ClusterNode`，`ClusterNode`可以保存资源的运行信息（响应时间、QPS、block 数目、线程数、异常数等）以及来源信息（origin名称）
  - `StatisticSlot`：负责统计实时调用数据，包括运行信息、来源信息等，统计完成后将数据写入前边的Node节点
- 规则判断部分（rule checking）
  - `AuthoritySlot`：负责授权规则（来源控制）
  - `SystemSlot`：负责系统保护规则
  - `ParamFlowSlot`：负责热点参数限流规则
  - `FlowSlot`：负责限流规则
  - `DegradeSlot`：负责降级规则





### 2、Node

Sentinel中的簇点链路是由一个个的Node组成的，Node是一个接口

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181129275.png" style="zoom:70%;" />

所有的节点都可以记录对资源的访问统计数据，所以都是StatisticNode的子类。

按照作用分为两类Node：

- `DefaultNode`：代表链路树中的每一个资源，一个资源出现在不同链路中时，会创建不同的`DefaultNode`节点。而树的入口节点叫`EntranceNode`，是一种特殊的`DefaultNode`
- `ClusterNode`：代表资源，一个资源不管出现在多少链路中，只会有一个`ClusterNode`。记录的是当前资源被访问的所有统计数据之和。



`DefaultNode`记录的是资源在当前链路中的访问数据，用来实现基于链路模式的限流规则。

`ClusterNode`记录的是资源在所有链路中的访问数据，实现默认模式、关联模式的限流规则。



例如：我们在一个SpringMVC项目中，有两个业务：

- 业务1：`controller`中的资源`/order/query`访问了service中的资源`/goods`
- 业务2：`controller`中的资源`/order/save`访问了service中的资源`/goods`

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181132633.png" style="zoom:50%;" />

其中，`goods`资源被两条链路访问，所以形成了两个`DefaultNode`，而`ClusterNode`是统计`goods`这个资源的全部访问情况，所以只会构建一个，同时对于`/order/save`和`/order/query`这两个节点也分别有一个`ClusterNode`





### 3、Entry

默认情况下，Sentinel会将Controller中的方法作为被保护资源，如果想要将其他方法作为资源交给Sentinel管理需要用Entry来表示。

```java
// 资源名可使用任意有业务语义的字符串，比如方法名、接口名或其它可唯一标识的字符串。
try (Entry entry = SphU.entry("resourceName")) {
  // 被保护的业务逻辑
  // do something here...
} catch (BlockException ex) {
  // 资源访问阻止，被限流或被降级
  // 在此处进行相应的处理操作
}
```



#### 3.1 自定义资源

**需求：**在order-service服务中，将`OrderService`的`queryOrderById()`方法标记为一个资源。

1）在order-service中引入sentinel依赖

```xml
<!--sentinel-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

2）配置Sentinel地址

```yaml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8080 
```

3）修改`OrderService`类的`queryOrderById`方法

```java
public Order queryOrderById(Long orderId) {
    // 创建Entry，标记资源，资源名为resource1
    try (Entry entry = SphU.entry("resource1")) {
        // 1.查询订单，这里是假数据
        Order order = Order.build(101L, 4999L, "小米 MIX4", 1, 1L, null);
        // 2.查询用户，基于Feign的远程调用
        User user = userClient.findById(order.getUserId());
        // 3.设置
        order.setUser(user);
        // 4.返回
        return order;
    }catch (BlockException e){
        log.error("被限流或降级", e);
        return null;
    }
}
```

4）访问localhost:8080，查看Sentinel控制台，可以看到我们自定义的资源现在也被Sentinel管理了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181411736.png)



#### 3.2 使用注解

我们在方法上使用`@SentinelResource`注解也可以将这个方法标记为Sentinel的资源

```java
@SentinelResource("resource")
public Order queryOrderById(Long orderId) {
    // 1.查询订单，这里是假数据
    Order order = Order.build(101L, 4999L, "小米 MIX4", 1, 1L, null);
    // 2.查询用户，基于Feign的远程调用
    User user = userClient.findById(order.getUserId());
    // 3.设置
    order.setUser(user);
    // 4.返回
    return order;
}
```



**源码分析**

我们导入Sentinel的依赖后，有一个`SentinelAutoConfiguration`的自动配置类，在这个类中有一个`SentinelResourceAspect`的bean

```java
@Bean
@ConditionalOnMissingBean
public SentinelResourceAspect sentinelResourceAspect() {
    return new SentinelResourceAspect();
}
```

`SentinelResourceAspect`这个类就是一个切面类，利用AOP对使用了`@SentinelResource`注解的方法做了增强，从而将资源交给Sentinel管理

```java
@Aspect
public class SentinelResourceAspect extends AbstractSentinelAspectSupport {
    public SentinelResourceAspect() {
    }
    
	// 切入点就是使用了@SentinelResource注解标记的方法
    @Pointcut("@annotation(com.alibaba.csp.sentinel.annotation.SentinelResource)")
    public void sentinelResourceAnnotationPointcut() {
    }
    
	// 环绕增强
    @Around("sentinelResourceAnnotationPointcut()")
    public Object invokeResourceWithSentinel(ProceedingJoinPoint pjp) throws Throwable {
        // 获取受保护的方法
        Method originMethod = this.resolveMethod(pjp);
        // 获取 @SentinelResource注解
        SentinelResource annotation = (SentinelResource)originMethod.getAnnotation(SentinelResource.class);
        if (annotation == null) {
            throw new IllegalStateException("Wrong state for SentinelResource annotation");
        } else {
            // 获取注解上的资源名称
            String resourceName = this.getResourceName(annotation.value(), originMethod);
            EntryType entryType = annotation.entryType();
            int resourceType = annotation.resourceType();
            Entry entry = null;

            try {
                Object var18;
                try {
                    // 创建资源 Entry
                    entry = SphU.entry(resourceName, resourceType, entryType, pjp.getArgs());
                    // 执行受保护的方法
                    Object result = pjp.proceed();
                    var18 = result;
                    return var18;
                } catch (BlockException var15) {
                    var18 = this.handleBlockException(pjp, annotation, var15);
                    return var18;
                } catch (Throwable var16) {
                    Class<? extends Throwable>[] exceptionsToIgnore = annotation.exceptionsToIgnore();
                    if (exceptionsToIgnore.length > 0 && this.exceptionBelongsTo(var16, exceptionsToIgnore)) {
                        throw var16;
                    } else if (this.exceptionBelongsTo(var16, annotation.exceptionsToTrace())) {
                        this.traceException(var16);
                        Object var10 = this.handleFallback(pjp, annotation, var16);
                        return var10;
                    } else {
                        throw var16;
                    }
                }
            } finally {
                if (entry != null) {
                    // 退出entry，此时会将调用链中所有插槽全都exit
                    entry.exit(1, pjp.getArgs());
                }

            }
        }
    }
}
```







### 4、Context

在Sentinel控制台上，我们发现簇点链路中除了Controller方法、Service方法两个资源外，还多了一个默认的入口节点：`sentinel_spring_web_context`，是一个`EntranceNode`类型的节点。这个节点是在初始化Context的时候由Sentinel帮我们创建的。



#### 4.1 Context概念

- Context 代表调用链路上下文，贯穿一次调用链路中的所有资源（ `Entry`），基于`ThreadLocal`。
- Context 维持着入口节点（`entranceNode`）、本次调用链路的 curNode（当前资源节点）、调用来源（`origin`）等信息。
- 后续的Slot都可以通过Context拿到`DefaultNode`或者`ClusterNode`，从而获取统计数据，完成规则判断
- Context初始化的过程中，会创建`EntranceNode`，`contextName`就是`EntranceNode`的名称



**创建context**

```java
// 创建context，包含两个参数：context名称、 来源名称
ContextUtil.enter("contextName", "originName");
```





#### 4.2 Context的初始化

导入Sentinel依赖后，会有一个`SentinelWebAutoConfiguration`的自动配置类

1、`SentinelWebAutoConfiguration`自动配置类

- 这个类实现了`WebMvcConfigurer`接口，可以做一些个性化配置

- 其中`addInterceptors`方法，添加了一个`sentinelWebInterceptorOptional`的拦截器

- `sentinelWebInterceptorOptional`继承自`AbstractSentinelInterceptor`

  ```java
  public class SentinelWebInterceptor extends AbstractSentinelInterceptor 
  ```

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication( type = Type.SERVLET)
@ConditionalOnProperty(
    name = {"spring.cloud.sentinel.enabled"},
    matchIfMissing = true
)
@ConditionalOnClass({SentinelWebInterceptor.class})
@EnableConfigurationProperties({SentinelProperties.class})
public class SentinelWebAutoConfiguration implements WebMvcConfigurer {
    private static final Logger log = LoggerFactory.getLogger(SentinelWebAutoConfiguration.class);
    @Autowired
    private SentinelProperties properties;
    @Autowired
    private Optional<UrlCleaner> urlCleanerOptional;
    @Autowired
    private Optional<BlockExceptionHandler> blockExceptionHandlerOptional;
    @Autowired
    private Optional<RequestOriginParser> requestOriginParserOptional;
    @Autowired
    private Optional<SentinelWebInterceptor> sentinelWebInterceptorOptional;

    public SentinelWebAutoConfiguration() {
    }

    public void addInterceptors(InterceptorRegistry registry) {
        if (this.sentinelWebInterceptorOptional.isPresent()) {
            Filter filterConfig = this.properties.getFilter();
            registry.addInterceptor((HandlerInterceptor)this.sentinelWebInterceptorOptional.get()).order(filterConfig.getOrder()).addPathPatterns(filterConfig.getUrlPatterns());
            log.info("[Sentinel Starter] register SentinelWebInterceptor with urlPatterns: {}.", filterConfig.getUrlPatterns());
        }
    }

    ...
}
```

2、`AbstractSentinelInterceptor`类

- `AbstractSentinelInterceptor`实现了`HandlerInterceptor`接口，是一个拦截器
- 在`preHandler`方法中，为到达的资源创建Entry，然后在`afterCompletion`方法中，将Entry退出，类似于环绕增强

```java
public abstract class AbstractSentinelInterceptor implements HandlerInterceptor {
    public static final String SENTINEL_SPRING_WEB_CONTEXT_NAME = "sentinel_spring_web_context";
    private static final String EMPTY_ORIGIN = "";
    private final BaseWebMvcConfig baseWebMvcConfig;

   ...

    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        try {
            // 获取资源名称，一般是controller方法的@RequestMapping路径，例如/order/{orderId}
            String resourceName = this.getResourceName(request);
            if (StringUtil.isEmpty(resourceName)) {
                return true;
            } else if (this.increaseReferece(request, this.baseWebMvcConfig.getRequestRefName(), 1) != 1) {
                return true;
            } else {
                // 从request中获取请求来源，将来做 授权规则 判断时会用
                String origin = this.parseOrigin(request);
                // 获取 contextName，默认是sentinel_spring_web_context
                String contextName = this.getContextName(request);
                // 创建 Context
                ContextUtil.enter(contextName, origin);
                // 创建资源，名称就是当前请求的controller方法的映射路径
                Entry entry = SphU.entry(resourceName, 1, EntryType.IN);
                // 将entry资源存入request中
                request.setAttribute(this.baseWebMvcConfig.getRequestAttributeName(), entry);
                return true;
            }
        } catch (BlockException var12) {
            BlockException e = var12;

            try {
                this.handleBlockException(request, response, e);
            } finally {
                ContextUtil.exit();
            }

            return false;
        }
    }

    protected abstract String getResourceName(HttpServletRequest var1);

    protected String getContextName(HttpServletRequest request) {
        return "sentinel_spring_web_context";
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        if (this.increaseReferece(request, this.baseWebMvcConfig.getRequestRefName(), -1) == 0) {
            // 在afterCompletion方法中，从request中获取到entry，然后退出
            Entry entry = this.getEntryInRequest(request, this.baseWebMvcConfig.getRequestAttributeName());
            if (entry == null) {
                RecordLog.warn("[{}] No entry found in request, key: {}", new Object[]{this.getClass().getSimpleName(), this.baseWebMvcConfig.getRequestAttributeName()});
            } else {
                this.traceExceptionAndExit(entry, ex);
                this.removeEntryInRequest(request);
                ContextUtil.exit();
            }
        }
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    }
    
    ...
}
```

3、`this.getContextName(request)`

- 获取`contextName`时，如果`webContextUnify`参数为true，那么就返回`sentinel_spring_web_context`，否则返回当前资源名
- `webContextUnify`默认为true，因此我们在控制台看到的入口节点的名称为`sentinel_spring_web_context`，这就是为什么我们在流量控制使用链路模式时，需要将`webContextUnify`设置为false，设置为false后，入口节点就是Controller的资源名称，这样不同方法走的就是不同链路了

```java
protected String getContextName(HttpServletRequest request) {
    return this.config.isWebContextUnify() ? super.getContextName(request) : this.getResourceName(request);
}

private boolean webContextUnify = true;
```

4、创建Context，`ContextUtil.enter(contextName, origin)`

```java
public static Context enter(String name, String origin) {
    if ("sentinel_default_context".equals(name)) {
        throw new ContextNameDefineException("The sentinel_default_context can't be permit to defined!");
    } else {
        return trueEnter(name, origin);
    }
}
```

```java
protected static Context trueEnter(String name, String origin) {
    // 尝试获取context
    Context context = (Context)contextHolder.get();
    if (context == null) {
        // 如果context为空，开始初始化
        Map<String, DefaultNode> localCacheNameMap = contextNameNodeMap;
        // 尝试获取入口节点
        DefaultNode node = (DefaultNode)localCacheNameMap.get(name);
        if (node == null) {
            if (localCacheNameMap.size() > 2000) {
                setNullContext();
                return NULL_CONTEXT;
            }
			// 入口节点为空，加锁初始化入口节点
            LOCK.lock();

            try {
                node = (DefaultNode)contextNameNodeMap.get(name);
                if (node == null) {
                    
                    if (contextNameNodeMap.size() > 2000) {
                        setNullContext();
                        Context var9 = NULL_CONTEXT;
                        return var9;
                    }
					// 入口节点为空，初始化入口节点 EntranceNode，name就是sentinel_spring_web_context
                    node = new EntranceNode(new StringResourceWrapper(name, EntryType.IN), (ClusterNode)null);
                    // 添加入口节点到 ROOT
                    Constants.ROOT.addChild((Node)node);
                    // 将入口节点放入缓存
                    Map<String, DefaultNode> newMap = new HashMap(contextNameNodeMap.size() + 1);
                    newMap.putAll(contextNameNodeMap);
                    newMap.put(name, node);
                    contextNameNodeMap = newMap;
                }
            } finally {
                LOCK.unlock();
            }
        }
		// 创建Context，参数为：入口节点 和 contextName
        context = new Context((DefaultNode)node, name);
        // 设置请求来源
        context.setOrigin(origin);
        // 放入ThreanLocal
        contextHolder.set(context);
    }

    return context;
}
```



**整体流程**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181520865.png" style="zoom:67%;" />







### 5、ProcessorSlotChain执行流程

1、在`AbstractSentinelInterceptor`方法中，创建好context，通过`Entry entry = SphU.entry(resourceName, 1, EntryType.IN);`创建entry资源，然后执行当前这个资源的责任链，最终执行`entryWithPriority`方法

```java
private Entry entryWithPriority(ResourceWrapper resourceWrapper, int count, boolean prioritized, Object... args) throws BlockException {
    // 获取 Context
    Context context = ContextUtil.getContext();
    if (context instanceof NullContext) {
        return new CtEntry(resourceWrapper, (ProcessorSlot)null, context);
    } else {
        if (context == null) {
            context = CtSph.InternalContextUtil.internalEnter("sentinel_default_context");
        }

        if (!Constants.ON) {
            return new CtEntry(resourceWrapper, (ProcessorSlot)null, context);
        } else {
            // 获取 Slot执行链，一个资源，会创建一个执行链，放入缓存
            ProcessorSlot<Object> chain = this.lookProcessChain(resourceWrapper);
            if (chain == null) {
                return new CtEntry(resourceWrapper, (ProcessorSlot)null, context);
            } else {
                CtEntry e = new CtEntry(resourceWrapper, chain, context);

                try {
                    // 执行 slotChain
                    chain.entry(context, resourceWrapper, (Object)null, count, prioritized, args);
                } catch (BlockException var9) {
                    e.exit(count, args);
                    throw var9;
                } catch (Throwable var10) {
                    RecordLog.info("Sentinel unexpected exception", var10);
                }

                return e;
            }
        }
    }
}
```

2、`chain.entry(context, resourceWrapper, (Object)null, count, prioritized, args);`

这个方法经过调用后最终执行的是`firstEntry()`方法

```java
public void entry(Context context, ResourceWrapper resourceWrapper, Object t, int count, boolean prioritized, Object... args) throws Throwable {
    this.first.transformEntry(context, resourceWrapper, t, count, prioritized, args);
}
```

```java
public void fireEntry(Context context, ResourceWrapper resourceWrapper, Object obj, int count, boolean prioritized, Object... args) throws Throwable {
    if (this.next != null) {
        this.next.transformEntry(context, resourceWrapper, obj, count, prioritized, args);
    }
}
```

在`fireEntry`方法中，通过`this.next.transformEntry`使用第一个slot来执行，`this.next`就是`NodeSelectorSlot`，第一个slot用于构建链路树

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181553281.png)



#### 5.1 NodeSelectorSlot

最终会执行`entry`方法

- `DefaultNode node = (DefaultNode)this.map.get(context.getName());`  这里是通过`context.getName()`上下文的名称来获取节点，因为每个资源都有各自的执行链，也都有各自的context，通过context的名称来获取可以使得从不同链路到同一资源的node是不同的
- 将`DefaultNode`放入缓存中，key是**contextName**，这样不同链路入口的请求，将会创建多个`DefaultNode`，相同链路则只有一个`DefaultNode`
- 将当前资源的`DefaultNode`设置为上一个资源的`childNode`
- 将当前资源的`DefaultNode`设置为Context中的`curNode`（当前节点）

```java
public void entry(Context context, ResourceWrapper resourceWrapper, Object obj, int count, boolean prioritized, Object... args) throws Throwable {
    // 尝试获取 当前资源的 DefaultNode
    DefaultNode node = (DefaultNode)this.map.get(context.getName());
    if (node == null) {
        synchronized(this) {
            node = (DefaultNode)this.map.get(context.getName());
            if (node == null) {
                // 如果为空，为当前资源创建一个新的 DefaultNode
                node = new DefaultNode(resourceWrapper, (ClusterNode)null);
                HashMap<String, DefaultNode> cacheMap = new HashMap(this.map.size());
                cacheMap.putAll(this.map);
                // 放入缓存中，注意这里的 key是contextName，
                // 这样不同链路进入相同资源，就会创建多个 DefaultNode
                cacheMap.put(context.getName(), node);
                this.map = cacheMap;
                // 当前节点加入上一节点的 child中，这样就构成了调用链路树
                ((DefaultNode)context.getLastNode()).addChild(node);
            }
        }
    }
	// context中的curNode（当前节点）设置为新的 node
    context.setCurNode(node);
    // 执行下一个 slot
    this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```



#### 5.2 ClusterBuilderSlot

`ClusterBuilderSlot`是为每个节点创建一个`ClusterNode`，不同链路的到达同一资源，只会创建一个`ClusterNode`

```java
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count, boolean prioritized, Object... args) throws Throwable {
    // 判空，注意ClusterNode是共享的成员变量，也就是说一个资源只有一个ClusterNode，与链路无关
    if (this.clusterNode == null) {
        synchronized(lock) {
            if (this.clusterNode == null) {
                // 创建 cluster node.
                this.clusterNode = new ClusterNode(resourceWrapper.getName(), resourceWrapper.getResourceType());
                HashMap<ResourceWrapper, ClusterNode> newMap = new HashMap(Math.max(clusterNodeMap.size(), 16));
                newMap.putAll(clusterNodeMap);
                // 放入缓存，可以是nodeId，也就是resource名称
                newMap.put(node.getId(), this.clusterNode);
                clusterNodeMap = newMap;
            }
        }
    }
	// 将资源的 DefaultNode与 ClusterNode关联
    node.setClusterNode(this.clusterNode);
    // 记录请求来源 origin 将 origin放入 entry
    if (!"".equals(context.getOrigin())) {
        Node originNode = node.getClusterNode().getOrCreateOriginNode(context.getOrigin());
        context.getCurEntry().setOriginNode(originNode);
    }
	// 继续下一个slot
    this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```





#### 5.3 StatisticSlot

`StatisticSlot`负责统计实时调用数据，包括运行信息（访问次数、线程数）、来源信息等。

`StatisticSlot`是实现限流的关键，其中基于**滑动时间窗口算法**维护了计数器，统计进入某个资源的请求次数。

```JAVA
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count, boolean prioritized, Object... args) throws Throwable {
    Iterator var8;
    ProcessorSlotEntryCallback handler;
    try {
        // 放行到下一个 slot，做限流、降级等判断
        this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
        // 请求通过了, 线程计数器 +1 ，用作线程隔离
        node.increaseThreadNum();
        // 请求计数器 +1 用作限流，记录QPS
        node.addPassRequest(count);
        if (context.getCurEntry().getOriginNode() != null) {
            // 如果有 origin，来源计数器也都要 +1
            context.getCurEntry().getOriginNode().increaseThreadNum();
            context.getCurEntry().getOriginNode().addPassRequest(count);
        }

        if (resourceWrapper.getEntryType() == EntryType.IN) {
            // 如果是入口资源，还要给全局计数器 +1
            Constants.ENTRY_NODE.increaseThreadNum();
            Constants.ENTRY_NODE.addPassRequest(count);
        }
		// 请求通过后的回调
        Iterator var13 = StatisticSlotCallbackRegistry.getEntryCallbacks().iterator();

        while(var13.hasNext()) {
            ProcessorSlotEntryCallback<DefaultNode> handler = (ProcessorSlotEntryCallback)var13.next();
            handler.onPass(context, resourceWrapper, node, count, args);
        }
    }
	...
}
```



对于计数器+1的`increaseThreadNum`方法，会对`DefaultNode`计数器+1，表示当前链路，对`clusterNode`计数器+1，表示当前资源计数器

```java
public void increaseThreadNum() {
    // DefaultNode的计数器，代表当前链路的 计数器
    super.increaseThreadNum();
    // ClusterNode计数器，代表当前资源的 总计数器
    this.clusterNode.increaseThreadNum();
}
```



`StatisticSlot`之后，就是规则校验相关的slot

- AuthoritySlot：负责授权规则（来源控制）
- SystemSlot：负责系统保护规则
- ParamFlowSlot：负责热点参数限流规则
- FlowSlot：负责限流规则
- DegradeSlot：负责降级规则







#### 5.4 AuthoritySlot

`AuthoritySlot`对请求来源做校验，我们可以在控制台上添加授权规则，设置白名单、黑名单，然后项目中实现`RequestOriginParser`这个接口，返回请求的来源信息。

在context初始化时，通过我们实现的`RequestOriginParser`方法获取了`origin`，并将`origin`放入了context中

```java
String origin = this.parseOrigin(request);
// 获取 contextName，默认是sentinel_spring_web_context
String contextName = this.getContextName(request);
// 创建 Context
ContextUtil.enter(contextName, origin);
```



```java
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count, boolean prioritized, Object... args) throws Throwable {
    // 校验黑白名单
    this.checkBlackWhiteAuthority(resourceWrapper, context);
    // 进入下一个 slot
    this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

```java
void checkBlackWhiteAuthority(ResourceWrapper resource, Context context) throws AuthorityException {
    Map<String, Set<AuthorityRule>> authorityRules = AuthorityRuleManager.getAuthorityRules();
    if (authorityRules != null) {
        // 获取授权规则
        Set<AuthorityRule> rules = (Set)authorityRules.get(resource.getName());
        if (rules != null) {
            Iterator var5 = rules.iterator();

            AuthorityRule rule;
            // 遍历规则并判断
            do {
                if (!var5.hasNext()) {
                    return;
                }
		
                rule = (AuthorityRule)var5.next();
            } while(AuthorityRuleChecker.passCheck(rule, context));
			// 规则不通过，直接抛出异常
            throw new AuthorityException(context.getOrigin(), rule);
        }
    }
}
```

```java
static boolean passCheck(AuthorityRule rule, Context context) {
    // 得到请求来源 origin
    String requester = context.getOrigin();
    // 来源为空，或者规则为空，都直接放行
    if (!StringUtil.isEmpty(requester) && !StringUtil.isEmpty(rule.getLimitApp())) {
        // rule.getLimitApp()得到的就是 白名单 或 黑名单 的字符串，这里先用 indexOf方法判断
        int pos = rule.getLimitApp().indexOf(requester);
        // pos大于-1说明origin在黑名单或白名单中
        boolean contain = pos > -1;
        
        if (contain) {
            // 如果包含 origin，还要进一步做精确判断，把名单列表以","分割，逐个判断
            boolean exactlyMatch = false;
            String[] appArray = rule.getLimitApp().split(",");
            String[] var7 = appArray;
            int var8 = appArray.length;

            for(int var9 = 0; var9 < var8; ++var9) {
                String app = var7[var9];
                if (requester.equals(app)) {
                    exactlyMatch = true;
                    break;
                }
            }

            contain = exactlyMatch;
        }

        int strategy = rule.getStrategy();
        // 如果是黑名单，并且包含origin，则返回false
        if (strategy == 1 && contain) {
            return false;
        } else {
            // 如果是白名单，并且不包含origin，返回false，否则返回true
            return strategy != 0 || contain;
        }
    } else {
        return true;
    }
}
```







#### 5.5 SystemSlot

`SystemSlot`是对系统保护的规则校验

```JAVA
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count, boolean prioritized, Object... args) throws Throwable {
    // 系统规则校验
    SystemRuleManager.checkSystem(resourceWrapper);
    // 进入下一个 slot
    this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

```java
public static void checkSystem(ResourceWrapper resourceWrapper) throws BlockException {
    if (resourceWrapper != null) {
        if (checkSystemStatus.get()) {
            // 只针对入口资源做校验，其它直接返回
            if (resourceWrapper.getEntryType() == EntryType.IN) {
                // 全局 QPS校验
                double currentQps = Constants.ENTRY_NODE == null ? 0.0D : Constants.ENTRY_NODE.successQps();
                if (currentQps > qps) {
                    throw new SystemBlockException(resourceWrapper.getName(), "qps");
                } else {
                    // 全局 线程数 校验
                    int currentThread = Constants.ENTRY_NODE == null ? 0 : Constants.ENTRY_NODE.curThreadNum();
                    if ((long)currentThread > maxThread) {
                        throw new SystemBlockException(resourceWrapper.getName(), "thread");
                    } else {
                        // 全局平均 RT校验
                        double rt = Constants.ENTRY_NODE == null ? 0.0D : Constants.ENTRY_NODE.avgRt();
                        if (rt > (double)maxRt) {
                            throw new SystemBlockException(resourceWrapper.getName(), "rt");
                        // 全局 系统负载 校验
                        } else if (highestSystemLoadIsSet && getCurrentSystemAvgLoad() > highestSystemLoad && !checkBbr(currentThread)) {
                            throw new SystemBlockException(resourceWrapper.getName(), "load");
                        // 全局 CPU使用率 校验
                        } else if (highestCpuUsageIsSet && getCurrentCpuUsage() > highestCpuUsage) {
                            throw new SystemBlockException(resourceWrapper.getName(), "cpu");
                        }
                    }
                }
            }
        }
    }
}
```





#### 5.6 ParamFlowSlot

`ParamFlowSlot`就是热点参数限流，热点限流仅针对我们自定义的资源可以使用

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071718604.png)

- 这里的单机阈值，就是最大令牌数量：maxCount
- 这里的统计窗口时长，就是统计时长：duration



```java
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node,
                  int count, boolean prioritized, Object... args) throws Throwable {
    // 如果没有设置热点规则，直接放行
    if (!ParamFlowRuleManager.hasRules(resourceWrapper.getName())) {
        fireEntry(context, resourceWrapper, node, count, prioritized, args);
        return;
    }
	// 热点规则判断
    checkFlow(resourceWrapper, count, args);
    // 进入下一个 slot
    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```



**令牌桶**

每个热点参数都有一个令牌桶

```java
static boolean passDefaultLocalCheck(ResourceWrapper resourceWrapper, ParamFlowRule rule, int acquireCount, Object value) {
    // 得到令牌桶
    ParameterMetric metric = getParameterMetric(resourceWrapper);
    // 用来记录剩余令牌数量
    CacheMap<Object, AtomicLong> tokenCounters = metric == null ? null : metric.getRuleTokenCounter(rule);
    // 用来记录上一个请求的时间
    CacheMap<Object, AtomicLong> timeCounters = metric == null ? null : metric.getRuleTimeCounter(rule);
    if (tokenCounters != null && timeCounters != null) {
        // 得到热点参数限流规则
        Set<Object> exclusionItems = rule.getParsedHotItems().keySet();
        // 得到最大令牌数量
        long tokenCount = (long)rule.getCount();
        if (exclusionItems.contains(value)) {
            tokenCount = (long)(Integer)rule.getParsedHotItems().get(value);
        }

        if (tokenCount == 0L) {
            return false;
        } else {
            // rule.getBurstCount()允许的突发阈值，一般为0
            long maxCount = tokenCount + (long)rule.getBurstCount();
            // acquireCount为到达请求数，一般为1，即需要的令牌数量，如果大于maxCount，就返回false
            if ((long)acquireCount > maxCount) {
                return false;
            } else {
                while(true) {
                    // 当前时间
                    long currentTime = TimeUtil.currentTimeMillis();
                    // 最近一次请求通过的时间
                    AtomicLong lastAddTokenTime = (AtomicLong)timeCounters.putIfAbsent(value, new AtomicLong(currentTime));
                    if (lastAddTokenTime == null) {
                        // 如果为null，表示当前请求是第一次来，然后将桶内令牌数减去acquireCount
                        tokenCounters.putIfAbsent(value, new AtomicLong(maxCount - (long)acquireCount));
                        return true;
                    }

                    // 距离上次请求的时间
                    long passTime = currentTime - lastAddTokenTime.get();
                    AtomicLong oldQps;
                    long restQps;
                    // getDurationInSec为统计窗口周期，如果与上次请求间隔时间超过了统计窗口周期
                    if (passTime > rule.getDurationInSec() * 1000L) {
                        // 如果这个请求参数是第一次来，那么就执行put，剩余令牌数就是maxCount-acquireCount，返回null
                        // 如果不是第一次来，就返回oldQps
                        oldQps = (AtomicLong)tokenCounters.putIfAbsent(value, new AtomicLong(maxCount - (long)acquireCount));
                        if (oldQps == null) {
                            // 这个请求之前没来过，设置访问时间
                            lastAddTokenTime.set(currentTime);
                            return true;
                        }
						
                        // 这个请求之前来过，拿到剩余令牌数
                        restQps = oldQps.get();
                        // 计算这段时间内可以生成的令牌数
                        long toAddCount = passTime * tokenCount / (rule.getDurationInSec() * 1000L);
                        // 这段时间生成的令牌数加上剩余令牌数如果超过maxCount，那么新的剩余令牌数就是maxCount-acquireCount
                        long newQps = toAddCount + restQps > maxCount ? maxCount - (long)acquireCount : restQps + toAddCount - (long)acquireCount;
                        // 新的剩余令牌数小于0，返回false
                        if (newQps < 0L) {
                            return false;
                        }

                        // 修改剩余令牌数并设置请求到达时间
                        if (oldQps.compareAndSet(restQps, newQps)) {
                            lastAddTokenTime.set(currentTime);
                            return true;
                        }

                        Thread.yield();
                    // 与上次请求的间隔时间小于窗口周期
                    } else {
                        // 剩余令牌
                        oldQps = (AtomicLong)tokenCounters.get(value);
                        if (oldQps != null) {
                            restQps = oldQps.get();
                            // 令牌不够，返回false
                            if (restQps - (long)acquireCount < 0L) {
                                return false;
                            }
							// 令牌充足，就重新设置令牌数
                            if (oldQps.compareAndSet(restQps, restQps - (long)acquireCount)) {
                                return true;
                            }
                        }

                        Thread.yield();
                    }
                }
            }
        }
    } else {
        return true;
    }
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312181818564.jpg" style="zoom:70%;" />









#### 5.7 FlowSlot

FlowSlot是负责限流规则的判断

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312071641785.png)

三种流控模式，从底层**数据统计**角度，分为两类：

- 对进入资源的所有请求（ClusterNode）做限流统计：直接模式、关联模式
- 对进入资源的部分链路（DefaultNode）做限流统计：链路模式



三种流控效果，从**限流算法**来看，分为两类：

- 滑动时间窗口算法：快速失败、warm up
- 漏桶算法：排队等待效果



**源码分析**

```java
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count, boolean prioritized, Object... args) throws Throwable {
    // 限流规则检测
    this.checkFlow(resourceWrapper, context, node, count, prioritized);
    // 放行
    this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

1、`this.checkFlow(resourceWrapper, context, node, count, prioritized);`

最终进入`checkFlow`方法

```java
public void checkFlow(Function<String, Collection<FlowRule>> ruleProvider, ResourceWrapper resource, Context context, DefaultNode node, int count, boolean prioritized) throws BlockException {
    if (ruleProvider != null && resource != null) {
        // 获取当前资源的所有限流规则
        Collection<FlowRule> rules = (Collection)ruleProvider.apply(resource.getName());
        if (rules != null) {
            Iterator var8 = rules.iterator();

            while(var8.hasNext()) {
                // FlowRule是限流规则接口，成员变量有阈值类型 (0: 线程, 1: QPS)，三种限流模式，三种流控效果等
                FlowRule rule = (FlowRule)var8.next();
                // 遍历，逐个规则做校验
                if (!this.canPassCheck(rule, context, node, count, prioritized)) {
                    throw new FlowException(rule.getLimitApp(), rule);
                }
            }
        }

    }
}
```

2、进入`canPassCheck`方法，我们是单点模式，所以进入`passLocalCheck`方法

- `selectedNode`就是通过我们设置的不同流控模式，判断选择`DefaultNode`还是`ClusterNode`

```java
private static boolean passLocalCheck(FlowRule rule, Context context, DefaultNode node, int acquireCount, boolean prioritized) {
    // selectNodeByRequesterAndStrategy就是判断当前是哪种模式，如果是链路模式就选择DefaultNode，如果是直连模式或者关联模式选择ClusterNode
    Node selectedNode = selectNodeByRequesterAndStrategy(rule, context, node);
    return selectedNode == null ? true : rule.getRater().canPass(selectedNode, acquireCount, prioritized);
}
```

选择完`selectedNode`后，根据不同的流控效果，使用不同的`canPass`方法

- `DefaultController`就是快速失败，使用滑动时间窗口算法
- `WarmUpController`就是预热模式，使用滑动时间窗口算法，只不过阈值是动态的
- `RateLimiterController`就是排队等待，使用漏桶算法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312191447430.png)

3、这里我们进入`DefaultController`的`canPass`方法

```java
public boolean canPass(Node node, int acquireCount, boolean prioritized) {
    // 计算目前为止滑动窗口内已经存在的请求量
    int curCount = this.avgUsedTokens(node);
    // 判断：已使用请求量 + 需要的请求量（1） 是否大于 窗口的请求阈值
    if ((double)(curCount + acquireCount) > this.count) {
        // 大于，说明超出阈值，返回false
        if (prioritized && this.grade == 1) {
            long currentTime = TimeUtil.currentTimeMillis();
            long waitInMs = node.tryOccupyNext(currentTime, acquireCount, this.count);
            if (waitInMs < (long)OccupyTimeoutProperty.getOccupyTimeout()) {
                node.addWaitingRequest(currentTime + waitInMs, acquireCount);
                node.addOccupiedPass(acquireCount);
                this.sleep(waitInMs);
                throw new PriorityWaitException(waitInMs);
            }
        }
        return false;
    } else {
        // 小于等于，说明在阈值范围内，返回true
        return true;
    }
}
```



**时间窗口请求量统计**

1、在`StatisticSlot`中，通过如下代码对请求计数，他需要对`DefaultNode`和`ClusterNode`都进行计数

```java
// 记录线程数
node.increaseThreadNum();
// 记录QPS
node.addPassRequest(count);
```

2、对`DefaultNode`和`ClusterNode`进行计数都是走这个代码

```java
public void addPassRequest(int count) {
    this.rollingCounterInSecond.addPass(count);
    this.rollingCounterInMinute.addPass(count);
}
```

其中`rollingCounterInSecond`是一个`ArrayMetric`，第一个参数是时间窗口的分隔数量，默认为 2，就是把 1秒分为 2个小时间窗，第二个参数是滑动窗口时间间隔，默认1s，

```java
public class StatisticNode implements Node {
    private transient volatile Metric rollingCounterInSecond;
    private transient Metric rollingCounterInMinute;
    private LongAdder curThreadNum;
    private long lastFetchTime;

    public StatisticNode() {
        // intervalInMs：是滑动窗口的时间间隔，默认为 1 秒
		// sampleCount: 时间窗口的分隔数量，默认为 2，就是把 1秒分为 2个小时间窗
        this.rollingCounterInSecond = new ArrayMetric(SampleCountProperty.SAMPLE_COUNT, IntervalProperty.INTERVAL);
        this.rollingCounterInMinute = new ArrayMetric(60, 60000, false);
        this.curThreadNum = new LongAdder();
        this.lastFetchTime = -1L;
    }
```

3、进入`addPass`方法

```java
public void addPass(int count) {
    // 获取当前时间所在的时间窗
    WindowWrap<MetricBucket> wrap = this.data.currentWindow();
    // 计数器 +1
    ((MetricBucket)wrap.value()).addPass(count);
}
```

`this.data.currentWindow();`其中，`data`是一个`LeapArray`

```java
public abstract class LeapArray<T> {
    // 小窗口的时间长度，默认是500ms ，值 = intervalInMs / sampleCount
    protected int windowLengthInMs;
    // 滑动窗口内的 小窗口 数量，默认为 2
    protected int sampleCount;
    // 滑动窗口的时间间隔，默认为 1000ms
    protected int intervalInMs;
    // 滑动窗口的时间间隔，单位为秒，默认为 1
    private double intervalInSecond;
```

`LeapArray`是一个环形数组，因为时间是无限的，数组长度不可能无限，因此数组中每一个格子放入一个时间窗（window），当数组放满后，角标归0，覆盖最初的window。因为滑动窗口最多分成`sampleCount`数量的小窗口，因此数组长度只要大于`sampleCount`，那么最近的一个滑动窗口内的2个小窗口就永远不会被覆盖，就不需要担心旧数据被覆盖的问题

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312191502488.png" style="zoom:80%;" />

4、`this.data.currentWindow();`  这个方法获取当前请求所在的时间窗口，找到后执行`((MetricBucket)wrap.value()).addPass(count);`将这个窗口的请求数加一

```java
// timeMillis是当前时间
public WindowWrap<T> currentWindow(long timeMillis) {
    if (timeMillis < 0L) {
        return null;
    } else {
        /**
        	计算当前时间对应的数组角标，windowLengthInMs就是当前小窗口的时间间隔，1个滑动窗口分为2个小窗口，就是500ms
        	当前时间除以windowLengthInMs，可以得到有多少个这样的小窗口，然后在对数组长度取模，就得到了在数组中的下标
        	private int calculateTimeIdx(long timeMillis) {
                long timeId = timeMillis / (long)this.windowLengthInMs;
                return (int)(timeId % (long)this.array.length());
            }	
        */
        int idx = this.calculateTimeIdx(timeMillis);
        
        /**
        	计算当前时间在这个窗口内的位置
            protected long calculateWindowStart(long timeMillis) {
                return timeMillis - timeMillis % (long)this.windowLengthInMs;
            }
        */
        long windowStart = this.calculateWindowStart(timeMillis);
        
        /*
         * 先根据角标获取数组中保存的 oldWindow 对象，可能是旧数据，需要判断.
         *
         * (1) oldWindow 不存在, 说明是第一次，创建新 window并存入，然后返回即可
         * (2) oldWindow的 starTime = 本次请求的 windowStar, 说明正是要找的窗口，直接返回.
         * (3) oldWindow的 starTime < 本次请求的 windowStar, 说明是旧数据，需要被覆盖，创建 
         *     新窗口，覆盖旧窗口
         */
        while(true) {
            while(true) {
                WindowWrap<T> old = (WindowWrap)this.array.get(idx);
                WindowWrap window;
                if (old == null) {
                    // 创建新 window
                    window = new WindowWrap((long)this.windowLengthInMs, windowStart, this.newEmptyBucket(timeMillis));
                    // 基于CAS写入数组，避免线程安全问题
                    if (this.array.compareAndSet(idx, (Object)null, window)) {
                        return window;
                    }
                    Thread.yield();
                } else {
                    if (windowStart == old.windowStart()) {
                        return old;
                    }
                    if (windowStart > old.windowStart()) {
                        if (this.updateLock.tryLock()) {
                            try {
                                // 获取并发锁，覆盖旧窗口并返回
                                window = this.resetWindowTo(old, windowStart);
                            } finally {
                                this.updateLock.unlock();
                            }
                            return window;
                        }
                        Thread.yield();
                    } else if (windowStart < old.windowStart()) {
                        return new WindowWrap((long)this.windowLengthInMs, windowStart, this.newEmptyBucket(timeMillis));
                    }
                }
            }
        }
    }
}
```





**滑动窗口QPS计算**

```java
public boolean canPass(Node node, int acquireCount, boolean prioritized) {
    // 计算目前为止滑动窗口内已经存在的请求量
    int curCount = this.avgUsedTokens(node);
    // 判断：已使用请求量 + 需要的请求量（1） 是否大于 窗口的请求阈值
    if ((double)(curCount + acquireCount) > this.count) {
        // 大于，说明超出阈值，返回false
        if (prioritized && this.grade == 1) {
            long currentTime = TimeUtil.currentTimeMillis();
            long waitInMs = node.tryOccupyNext(currentTime, acquireCount, this.count);
            if (waitInMs < (long)OccupyTimeoutProperty.getOccupyTimeout()) {
                node.addWaitingRequest(currentTime + waitInMs, acquireCount);
                node.addOccupiedPass(acquireCount);
                this.sleep(waitInMs);
                throw new PriorityWaitException(waitInMs);
            }
        }
        return false;
    } else {
        // 小于等于，说明在阈值范围内，返回true
        return true;
    }
}
```



1、通过`int curCount = this.avgUsedTokens(node);`计算得出滑动窗口内的请求量，因为我们统计QPS，所以会进入`passQps`方法

```java
public double passQps() {
    // 请求量 ÷ 滑动窗口时间间隔 ，得到的就是QPS
    return (double)this.rollingCounterInSecond.pass() / this.rollingCounterInSecond.getWindowIntervalInSec();
}
```

2、`this.rollingCounterInSecond.pass()`

```java
public long pass() {
    // 获取当前窗口
    this.data.currentWindow();
    long pass = 0L;
    // 获取 当前时间的 滑动窗口范围内 的所有小窗口
    List<MetricBucket> list = this.data.values();

    MetricBucket window;
    // 遍历，累加求和
    for(Iterator var4 = list.iterator(); var4.hasNext(); pass += window.pass()) {
        window = (MetricBucket)var4.next();
    }
	// 返回当前滑动窗口内请求数
    return pass;
}
```

3、`this.data.values();`通过这行代码获取滑动窗口内所有的小窗口

```java
public List<T> values(long timeMillis) {
    if (timeMillis < 0L) {
        return new ArrayList();
    } else {
        // 创建空集合，大小等于 LeapArray长度
        int size = this.array.length();
        List<T> result = new ArrayList(size);
		// 遍历 LeapArray
        for(int i = 0; i < size; ++i) {
            // 获取每一个小窗口
            WindowWrap<T> windowWrap = (WindowWrap)this.array.get(i);
             /** 
           		判断这个小窗口是否在 滑动窗口时间范围内（1秒内），在的话就添加进去。
           		当前时间减去这个窗口的起始时间，如果大于窗口时间间隔，说明这个窗口不在范围内
                public boolean isWindowDeprecated(long time, WindowWrap<T> windowWrap) {
                    return time - windowWrap.windowStart() > (long)this.intervalInMs;
                }
            */
            if (windowWrap != null && !this.isWindowDeprecated(timeMillis, windowWrap)) {
                result.add(windowWrap.value());
            }
        }

        return result;
    }
}
```





#### 5.8 漏桶

当我们使用排队等待的流控效果时，会执行`RateLimiterController`的`canPass`方法

```java
public boolean canPass(Node node, int acquireCount, boolean prioritized) {
    if (acquireCount <= 0) {
        return true;
    } else if (this.count <= 0.0D) {
        return false;
    } else {
        // 获取当前时间
        long currentTime = TimeUtil.currentTimeMillis();
        // 计算两次请求之间允许的最小时间间隔
        long costTime = Math.round(1.0D * (double)acquireCount / this.count * 1000.0D);
        // 计算本次请求 允许执行的时间点 = 最近一次请求的可执行时间 + 两次请求的最小间隔
        long expectedTime = costTime + this.latestPassedTime.get();
        // 如果允许执行的时间点小于当前时间，说明可以立即执行
        if (expectedTime <= currentTime) {
            // 更新上一次的请求的执行时间
            this.latestPassedTime.set(currentTime);
            return true;
        } else {
            // 计算预期等待时长
            long waitTime = costTime + this.latestPassedTime.get() - TimeUtil.currentTimeMillis();
            // 如果预期等待时间超出阈值，则拒绝请求
            if (waitTime > (long)this.maxQueueingTimeMs) {
                return false;
            } else {
                // 预期等待时间小于阈值，更新最近一次请求的可执行时间，加上costTime
                long oldTime = this.latestPassedTime.addAndGet(costTime);

                try {
                    // 再判断一次预期等待时间，是否超过阈值
                    waitTime = oldTime - TimeUtil.currentTimeMillis();
                    if (waitTime > (long)this.maxQueueingTimeMs) {
                        // 如果超过，则把刚才 加 的时间再 减回来
                        this.latestPassedTime.addAndGet(-costTime);
                        return false;
                    } else {
                        if (waitTime > 0L) {
                            // 预期等待时间在阈值范围内，休眠要等待的时间，醒来后继续执行
                            Thread.sleep(waitTime);
                        }

                        return true;
                    }
                } catch (InterruptedException var15) {
                    return false;
                }
            }
        }
    }
}
```





#### 5.9 DegradeSlot

`DegradeSlot`负责熔断降级

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072032544.png)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202312072030795.png" style="zoom:67%;" />



```java
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count, boolean prioritized, Object... args) throws Throwable {
    // 熔断降级规则判断
    this.performChecking(context, resourceWrapper);
    // 继续下一个slot
    this.fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

1、`performChecking`方法

```java
void performChecking(Context context, ResourceWrapper r) throws BlockException {
    // 获取当前资源所有的断路器 CircuitBreaker
    List<CircuitBreaker> circuitBreakers = DegradeRuleManager.getCircuitBreakers(r.getName());
    if (circuitBreakers != null && !circuitBreakers.isEmpty()) {
        Iterator var4 = circuitBreakers.iterator();

        CircuitBreaker cb;
        // 遍历断路器，逐个判断
        do {
            if (!var4.hasNext()) {
                return;
            }
            cb = (CircuitBreaker)var4.next();
        } while(cb.tryPass(context));

        throw new DegradeException(cb.getRule().getLimitApp(), cb.getRule());
    }
}
```

2、`tryPass`进行判断

```java
public boolean tryPass(Context context) {
    // 如果是closed状态，直接放行
    if (this.currentState.get() == State.CLOSED) {
        return true;
    // HALF_OPEN状态，直接拒绝请求，因为此时断路器正在尝试恢复服务的可用性，Half-Open状态放行一次请求，在Open状态下同时熔断时间结束时已经放行过了
    } else if (this.currentState.get() != State.OPEN) {
        return false;
    } else {
        /** 
        	open状态，断路器打开，判断OPEN时间窗是否结束，如果是则把状态从OPEN切换到 HALF_OPEN，返回true
        	protected boolean retryTimeoutArrived() {
        		// 判断当前时间是否大于熔断结束时间
                return TimeUtil.currentTimeMillis() >= this.nextRetryTimestamp;
            }
            protected void updateNextRetryTimestamp() {
            	// 熔断结束时间 = 当前时间 + 熔断时间
                this.nextRetryTimestamp = TimeUtil.currentTimeMillis() + (long)this.recoveryTimeoutMs;
            }
        */
        return this.retryTimeoutArrived() && this.fromOpenToHalfOpen(context);
    }
}
```

3、`fromOpenToHalfOpen`将`open`状态改为`half-open`

```java
protected boolean fromOpenToHalfOpen(Context context) {
    // 基于CAS修改状态，从 OPEN到 HALF_OPEN
    if (this.currentState.compareAndSet(State.OPEN, State.HALF_OPEN)) {
        // 状态变更的事件通知
        this.notifyObservers(State.OPEN, State.HALF_OPEN, (Double)null);
        // 得到当前资源
        Entry entry = context.getCurEntry();
        // 给资源设置监听器，在资源Entry销毁时（资源业务执行完毕时）触发
        entry.whenTerminate(new BiConsumer<Context, Entry>() {
            public void accept(Context context, Entry entry) {
                // 判断 资源业务是否异常
                if (entry.getBlockError() != null) {
                    // 如果异常，则再次进入OPEN状态
                    AbstractCircuitBreaker.this.currentState.compareAndSet(State.HALF_OPEN, State.OPEN);
                    AbstractCircuitBreaker.this.notifyObservers(State.HALF_OPEN, State.OPEN, 1.0D);
                }

            }
        });
        return true;
    } else {
        return false;
    }
}
```

4、请求经过所有插槽后，一定会执行exit方法，`DegradeSlot`的`exit`方法

```java
public void exit(Context context, ResourceWrapper r, int count, Object... args) {
    // 获取当前资源
    Entry curEntry = context.getCurEntry();
    // 如果当前资源已经被降级，执行下一个slot的Exit
    if (curEntry.getBlockError() != null) {
        this.fireExit(context, r, count, args);
    } else {
        // 获取当前资源所有的断路器 CircuitBreaker
        List<CircuitBreaker> circuitBreakers = DegradeRuleManager.getCircuitBreakers(r.getName());
        if (circuitBreakers != null && !circuitBreakers.isEmpty()) {
            if (curEntry.getBlockError() == null) {
                Iterator var7 = circuitBreakers.iterator();

                while(var7.hasNext()) {
                    CircuitBreaker circuitBreaker = (CircuitBreaker)var7.next();
                    //对每个断路器执行onRequestComplete方法看是否需要变更状态
                    circuitBreaker.onRequestComplete(context);
                }
            }
            this.fireExit(context, r, count, args);
        } else {
            this.fireExit(context, r, count, args);
        }
    }
}
```

5、`onRequestComplete`方法

```java
public void onRequestComplete(Context context) {
    // 获取资源 Entry
    Entry entry = context.getCurEntry();
    if (entry != null) {
        // 尝试获取 资源中的 异常
        Throwable error = entry.getError();
        // 获取计数器，同样采用了滑动窗口来计数
        ExceptionCircuitBreaker.SimpleErrorCounter counter = (ExceptionCircuitBreaker.SimpleErrorCounter)this.stat.currentWindow().value();
        if (error != null) {
            // 如果出现异常，则 error计数器 +1
            counter.getErrorCount().add(1L);
        }
		// 不管是否出现异常，total计数器 +1
        counter.getTotalCount().add(1L);
        // 判断异常比例是否超出阈值
        this.handleStateChangeWhenThresholdExceeded(error);
    }
}
```

6、判断异常比例是否超出阈值

```java
private void handleStateChangeWhenThresholdExceeded(Throwable error) {
    // 如果是Open状态，不做处理
    if (this.currentState.get() != State.OPEN) {
        // HALF_OPEN 状态，判断是否需要切换状态
        if (this.currentState.get() == State.HALF_OPEN) {
            // 如果这次请求没有出现异常，那么将状态从HALF_OPEN切换为Close
            if (error == null) {
                this.fromHalfOpenToClose();
            // 如果出现异常，将状态从HALF_OPEN切换为Open
            } else {
                this.fromHalfOpenToOpen(1.0D);
            }
		// Close状态
        } else {
            List<ExceptionCircuitBreaker.SimpleErrorCounter> counters = this.stat.values();
            long errCount = 0L;
            long totalCount = 0L;
			// 累加计算 异常请求数量、总请求数量
            ExceptionCircuitBreaker.SimpleErrorCounter counter;
            for(Iterator var7 = counters.iterator(); var7.hasNext(); totalCount += counter.totalCount.sum()) {
                counter = (ExceptionCircuitBreaker.SimpleErrorCounter)var7.next();
                errCount += counter.errorCount.sum();
            }
			// 如果总请求数达到了阈值，需要判断异常请求数或者异常比例是否达到阈值
            if (totalCount >= (long)this.minRequestAmount) {
                // 异常数
                double curCount = (double)errCount;
                // 如果使用的是异常比例模式，计算异常比例
                if (this.strategy == 1) {
                    curCount = (double)errCount * 1.0D / (double)totalCount;
                }
				// 超过阈值，将状态改为Open
                if (curCount > this.threshold) {
                    this.transformToOpen(curCount);
                }
            }
        }
    }
}
```
