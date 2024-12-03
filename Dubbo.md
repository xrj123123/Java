## 一、分布式介绍

- **响应时间**：指执行一个请求从开始到最后收到响应数据所花费的总体时间。

- **并发数**：指系统同时能处理的请求数量。

  - **并发连接数**：指的是客户端向服务器发起请求，并建立了TCP连接。每秒钟服务器连接的总TCP数量
  - **请求数**：也称为QPS(Query Per Second) 指每秒多少请求
  - **并发用户数**：单位时间内有多少用户

- **吞吐量**：指单位时间内系统能处理的请求数量。

  - **QPS**：`Query Per Second` 每秒查询数。 

  - **TPS**：`Transactions Per Second` 每秒事务数。 

    > 一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。

  - 一个页面的一次访问，只会形成一个TPS；但一次页面请求，可能产生多次对服务器的请求，就会有多个QPS

- 集群：很多人一起 ，干一样的事。

  > 一个业务模块，部署在多台服务器上。 

- 分布式：很多人一起，干不一样的事。这些不一样的事，合起来是一件大事。

  > 一个大的业务系统，拆分为小的业务模块，分别部署在不同的机器上。 





## 二、Dubbo

### 1、Dubbo介绍

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202410282329871.png)

- **Provider**：暴露服务的服务提供方
- **Container**：服务运行容器
- **Consumer**：调用远程服务的服务消费方
- **Registry**：服务注册与发现的注册中心
- **Monitor：**统计服务的调用次数和调用时间的监控中心





### 2、使用

- 服务提供方：将接口注册到远程的注册中心，然后将提供的接口抽取为一个模块，作为jar包发布到maven仓库

  ```java
  import org.apache.dubbo.config.annotation.Service;
  
  @Service
  public class UserServiceImpl implements UserService {
      @Override
      public User getUserById(String userId) {
          // 具体的业务逻辑
      }
  }
  ```

- 服务调用方：导入服务提供方暴露接口的jar包，然后使用时通过`@Reference`注入bean对象使用

  ```java
  import org.apache.dubbo.config.annotation.Reference;
  
  public class OrderService {
  
      @Reference
      private UserService userService;
  
      public void createOrder(String userId) {
          User user = userService.getUserById(userId);
          // 具体的业务逻辑
      }
  }
  ```

  



### 3、高级特性

#### 3.1 序列化

- dubbo 内部已经将序列化和反序列化的过程内部封装了

- 我们只需要在定义pojo类时实现`Serializable`接口即可



#### 3.2 地址缓存

**注册中心挂了，服务是否可以正常访问？**

- 可以，因为dubbo服务消费者在第一次调用时，会将服务提供方地址缓存到本地，以后在调用则不会访问注册中心。

- 当服务提供者地址发生变化时，注册中心会通知服务消费者。即执行上方Dubbo架构图的3操作Notify。



#### 3.3 超时与重试

- 服务消费者在调用服务提供者的时候发生了阻塞、等待的情形，这个时候，服务消费者会一直等待下去。在某个峰值时刻，大量的请求都在同时请求服务消费者，会造成线程的大量堆积，势必会造成雪崩。

- dubbo 可以设置一个超时时间，在这个时间段内，无法完成服务访问，则自动断开连接。使用`timeout`属性配置超时时间，默认值1000，单位毫秒。

  ```
  服务提供方注解上配置超时：@Service(timeout=3000)
  服务消费方注解上配置超时：@Reference(timeout=2000)
  如果服务消费方和提供方同时配置了超时时间，那么以时间较短者作为超时判断标准。
  ```

- 如果出现网络抖动，则这一次请求就会失败。Dubbo 提供重试机制来避免类似问题的发生。通过 `retries` 属性来设置重试次数。默认为 2 次。

  ```
  @Service(timeout = 3000, reties = 3)，表示重试3次，加上第一次请求，一共是4次
  ```

  

#### 3.4 负载均衡

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202410292315346.png" style="zoom:67%;" />

在Dubbo中，服务提供者的负载均衡分为四种类型，默认是`Random`

- `Random`：按照权重随机，符合正态分布，权重高的访问概率更大。
- `RoundRobin`：按权重轮询，例如三台机器的权重分别为100/200/100，那么访问顺序可能为：1、2、3、2
- `LeastActive`：最少活跃调用次数，相同活跃数的随机， 消费者会记录各个消费提供者最后一次响应所需的时间，选择响应时间最短的进行访问。
- `ConsistentHash`：一致性Hash，相同的参数总是会发到同一个服务提供者

```
服务提供者P1：@Service(weight = 100 )
服务提供者P2：@Service(weight = 200 )
服务提供者P3：@Service(weight = 100 )
消费者C：@Reference(loadbalance="random")
```



#### 3.5 集群容错

```java
@Reference(cluster = "failover")
```

- `Failover Cluster`（默认）：失败重试，当出现失败，尝试连接集群中其他服务，默认重试2次，读取retries配置，一般用在读操作。
- `Failfast Cluster`：快速失败，仅发起一次调用，失败立即报错，通常用于写操作。
- `Failsafe Cluster`：安全失败，出现异常时候，直接忽略，返回一个空结果，常用于写入日志操作等。
- `Failback Cluster`：失败自动恢复，后台记录失败请求，定时重发。
- `Forking Cluster`：并行调用多个服务器，只要一个成功即返回。
- `Broadcast Cluster`：广播调用所有提供者，逐个调用，任意一个报错就报错。



#### 3.6 服务降级

- `mock="force:return null"`表示消费方如果调用该服务，直接就返回null，压根就不发起远程调用，可以用来屏蔽一些不重要的服务，如日志服务等。
- `mock="fail:return null"`表示如果调用出错，返回null，不抛出异常，用来容忍不重要服务不稳定时对调用放的影响。





### 4、Dubbo原理

https://blog.csdn.net/wender/article/details/125233339
