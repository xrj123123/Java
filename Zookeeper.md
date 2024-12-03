### 1、Zookeeper概念

- Zookeeper 是 Apache Hadoop 项目下的一个子项目，是一个**树形目录**服务。
- Zookeeper 是一个分布式的、开源的分布式应用程序的**协调服务**。

- Zookeeper 提供的主要功能包括

  - 配置管理

  - 分布式锁

  - 集群管理




### 2、Zookeeper数据模型

- ZooKeeper 是一个树形目录服务，其数据模型和Unix的文件系统目录树很类似，拥有一个层次化结构。

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202410302229735.png)

- 这里面的每一个节点都被称为： **ZNode**，每个节点上都会保存自己的**数据**和**节点信息**。

  > 数据：每个节点可以存储数据
  >
  > 节点：每个节点会有子节点

- 节点可以拥有子节点，同时也允许少量（1MB）数据存储在该节点之下。

- 节点可以分为四大类：

  - PERSISTENT 持久化节点

  - EPHEMERAL 临时节点 ：-e

    > 客户端断开会话，那么节点就被删除

  - PERSISTENT_SEQUENTIAL 持久化顺序节点 ：-s

    > 创建的节点带编号，顺序的

  - EPHEMERAL_SEQUENTIAL 临时顺序节点 ：-es





### 3、**ZooKeeper** **命令**

#### 3.1 服务端命令

- 启动 ZooKeeper 服务: `./zkServer.sh start`

- 查看 ZooKeeper 服务状态: `./zkServer.sh status`

- 停止 ZooKeeper 服务: `./zkServer.sh stop `

- 重启 ZooKeeper 服务: `./zkServer.sh restart `



#### 3.2 客户端命令

- 连接ZooKeeper服务端：`./zkCli.sh –server** **ip:port`
- 断开连接：`quit`
- 显示指定目录下节点：`ls 目录`
- 创建节点：`create /节点path value`
- 获取节点值：`get /节点path`

- 设置节点值：`set /节点path value`
- 删除单个节点：`delete /节点path`
- 删除带有子节点的节点：`deleteall /节点path`

- 创建临时节点：`create -e /节点path value`
- 创建顺序节点：`create -s /节点path value`
- 查询节点详细信息：`ls –s /节点path`





### 4、Java API操作

#### 4.1 Curator介绍

Curator 是 Apache ZooKeeper 的Java客户端库，目标是简化 ZooKeeper 客户端的使用

**导入依赖**

```xml
<dependency>
	<groupId>org.apache.curator</groupId>
	<artifactId>curator-framework</artifactId>
	<version>4.0.0</version>
</dependency>

<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.0.0</version>
</dependency>
```



#### 4.2 连接建立

```java
public void testConnect() {

    /*
     * @param connectString       连接字符串。zk server 地址和端口 "192.168.149.135:2181"
     * @param sessionTimeoutMs    会话超时时间 单位ms
     * @param connectionTimeoutMs 连接超时时间 单位ms
     * @param retryPolicy         重试策略
     */
   /* 
   //重试策略
    RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000,10);
    //1.第一种方式
    CuratorFramework client = CuratorFrameworkFactory.newClient("192.168.149.135:2181",
            60 * 1000, 15 * 1000, retryPolicy);*/
    //重试策略
    RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
    //2.第二种方式
    client = CuratorFrameworkFactory.builder()
            .connectString("10.211.55.5:2181")
            .sessionTimeoutMs(60 * 1000)
            .connectionTimeoutMs(15 * 1000)
            .retryPolicy(retryPolicy)
            .namespace("xrj") // 添加namespace后，之后所有操作都是在/xrj下操作的
            .build();

    //开启连接
    client.start();
}
```



#### 4.3 创建节点

创建节点：`create` 持久 临时 顺序 数据

  * 基本创建 ：`create().forPath("")`

  ```java
  @Test
  public void testCreate2() throws Exception {
      // 1. 基本创建
      // 如果创建节点，没有指定数据，则默认将当前客户端的ip作为数据存储
      String path = client.create().forPath("/app1");
      System.out.println(path);
  }
  
  [zk: localhost:2181(CONNECTED) 21] ls /
  [xrj, zookeeper]
  [zk: localhost:2181(CONNECTED) 22] ls /xrj
  [app1]
  [zk: localhost:2181(CONNECTED) 23] 
  ```

- 创建节点 带有数据：`create().forPath("",data)`

  ```java
  @Test
  public void testCreate() throws Exception {
      // 2. 创建节点 带有数据
      String path = client.create().forPath("/app2", "hehe".getBytes());
      System.out.println(path);
  }
  
  [zk: localhost:2181(CONNECTED) 23] ls /xrj
  [app1, app2]
  [zk: localhost:2181(CONNECTED) 24] get /xrj/app2
  hehe
  ```

- 设置节点的类型：`create().withMode().forPath("", data)`

  ```java
  @Test
  public void testCreate3() throws Exception {
      // 3. 设置节点的类型
      // 默认类型：持久化
      String path = client.create().withMode(CreateMode.EPHEMERAL).forPath("/app3");
      System.out.println(path);
  }
  ```

- 创建多级节点  /app1/p1 ：`create().creatingParentsIfNeeded().forPath("", data)`

  ```java
  @Test
  public void testCreate4() throws Exception {
      // 4. 创建多级节点  /app1/p1
      // creatingParentsIfNeeded():如果父节点不存在，则创建父节点
      String path = client.create().creatingParentsIfNeeded().forPath("/app4/p1");
      System.out.println(path);
  }
  
  [zk: localhost:2181(CONNECTED) 25] ls /xrj
  [app1, app2, app4]
  [zk: localhost:2181(CONNECTED) 26] ls /xrj/app4
  [p1]
  ```



#### 4.4 查询节点

 * 查询数据：`get: getData().forPath()`

  ```java
  @Test
  public void testGet1() throws Exception {
      //1. 查询数据：get
      byte[] data = client.getData().forPath("/app1");
      System.out.println(new String(data));
  }
  ```

 * 查询子节点： `ls: getChildren().forPath()`

  ```java
  @Test
  public void testGet2() throws Exception {
      // 2. 查询子节点： ls
      List<String> path = client.getChildren().forPath("/");
      System.out.println(path);
  }
  ```

 * 查询节点状态信息：`ls -s: getData().storingStatIn(状态对象).forPath()`

  ```java
  @Test
  public void testGet3() throws Exception {
      Stat status = new Stat();
      System.out.println(status);
      //3. 查询节点状态信息：ls -s
      client.getData().storingStatIn(status).forPath("/app1");
      System.out.println(status);
  }
  ```

  

#### 4.5 修改节点

 * 基本修改数据：`setData().forPath()`

    ```java
    @Test
    public void testSet() throws Exception {
        client.setData().forPath("/app1", "itcast".getBytes());
    }
    ```

 * 根据版本修改（避免并发修改问题）：`setData().withVersion().forPath()`

    ```java
    @Test
    public void testSetForVersion() throws Exception {
    
        Stat status = new Stat();
        client.getData().storingStatIn(status).forPath("/app1");
    
        int version = status.getVersion();
        client.setData().withVersion(version).forPath("/app1", "hehe".getBytes());
    }
    ```



#### 4.6 删除节点

 * 删除单个节点：`delete().forPath("/app1");`

    ```java
    @Test
    public void testDelete() throws Exception {
        // 1. 删除单个节点
        client.delete().forPath("/app1");
    }
    ```

 * 删除带有子节点的节点：`delete().deletingChildrenIfNeeded().forPath("/app1");`

    ```java
    @Test
    public void testDelete2() throws Exception {
        // 2. 删除带有子节点的节点
        client.delete().deletingChildrenIfNeeded().forPath("/app4");
    }
    ```

 * 必须成功的删除：为了防止网络抖动。本质就是重试。  `guaranteed()`

    ```java
    @Test
    public void testDelete3() throws Exception {
        // 3. 必须成功的删除
        client.delete().guaranteed().forPath("/app2");
    }
    ```

 * 回调：`inBackground`

    ```java
    @Test
    public void testDelete4() throws Exception {
        // 4. 回调
        client.delete().guaranteed().inBackground(new BackgroundCallback(){
    
            @Override
            public void processResult(CuratorFramework client, CuratorEvent event) throws Exception {
                System.out.println("我被删除了~");
                System.out.println(event);
            }
        }).forPath("/app1");
    }
    ```



#### 4.7 watch监听

- ZooKeeper 允许用户在指定节点上注册一些`Watcher`，并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，该机制是 ZooKeeper 实现分布式协调服务的重要特性。

- ZooKeeper 中引入了Watcher机制来实现了发布/订阅功能能，能够让多个订阅者同时监听某一个对象，当一个对象自身状态变化时，会通知所有订阅者。

Curator引入了 Cache 来实现对 ZooKeeper 服务端事件的监听，ZooKeeper提供了三种Watcher

- `NodeCache` : 只是监听某一个特定的节点

- `PathChildrenCache` : 监控一个ZNode的子节点. 

- `TreeCache` : 可以监控整个树上的所有节点，类似于PathChildrenCache和NodeCache的组合



**NodeCache**

```java
@Test
public void testNodeCache() throws Exception {
    //1. 创建NodeCache对象
    final NodeCache nodeCache = new NodeCache(client, "/app1");
    //2. 注册监听
    nodeCache.getListenable().addListener(new NodeCacheListener() {
        @Override
        public void nodeChanged() throws Exception {
            System.out.println("节点变化了~");

            //获取修改节点后的数据
            byte[] data = nodeCache.getCurrentData().getData();
            System.out.println(new String(data));
        }
    });

    //3. 开启监听.如果设置为true，则开启监听是，加载缓冲数据
    nodeCache.start(true);
}
```



**PathChildrenCache**

```java
@Test
public void testPathChildrenCache() throws Exception {
    //1.创建监听对象, true表示缓存数据
    PathChildrenCache pathChildrenCache = new PathChildrenCache(client, "/app2", true);

    //2. 绑定监听器
    pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
            System.out.println("子节点变化了~");
            System.out.println(event);
            //监听子节点的数据变更，并且拿到变更后的数据
            // 1.获取类型
            PathChildrenCacheEvent.Type type = event.getType();
            // 2.判断类型是否是update
            if(type.equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)){
                System.out.println("数据变了！！！");
                byte[] data = event.getData().getData();
                System.out.println(new String(data));

            }
        }
    });
    //3. 开启
    pathChildrenCache.start();
}
```



**TreeCache**

```java
@Test
public void testTreeCache() throws Exception {
    //1. 创建监听器
    TreeCache treeCache = new TreeCache(client, /app2");

    //2. 注册监听
    treeCache.getListenable().addListener(new TreeCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, TreeCacheEvent event) throws Exception {
            System.out.println("节点变化了");
            System.out.println(event);
        }
    });

    //3. 开启
    treeCache.start();
}
```





### 5、分布式锁

**核心思想**：当客户端要获取锁，则创建节点，使用完锁，则删除该节点。

1、客户端获取锁时，在lock节点下创建**临时顺序**节点。

2、然后获取lock下面的所有子节点，客户端获取到所有的子节点之后，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁。使用完锁后，将该节点删除。

3、如果发现自己创建的节点并非lock所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到**比自己小**的那个节点，同时对其**注册事件监听器**，**监听删除事件**。

4、如果发现比自己小的那个节点被删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点是否是lock子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411021858980.png" style="zoom:67%;" />



#### 5.1 Curator实现分布式锁

在Curator中有五种锁方案

- `InterProcessSemaphoreMutex`：分布式排它锁（非可重入锁）
- `InterProcessMutex`：分布式可重入排它锁
- `InterProcessReadWriteLock`：分布式读写锁
- `InterProcessMultiLock`：将多个锁作为单个实体管理的容器
- `InterProcessSemaphoreV2`：共享信号量



#### 5.2 模拟12306买票

```java
public class Ticket12306 implements Runnable{

    private int tickets = 10;//数据库的票数

    private InterProcessMutex lock ;


    public Ticket12306(){
        // 重试策略
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
        CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString("10.211.55.5:2181")
                .sessionTimeoutMs(60 * 1000)
                .connectionTimeoutMs(15 * 1000)
                .retryPolicy(retryPolicy)
                .build();

        //开启连接
        client.start();

        lock = new InterProcessMutex(client,"/lock");
    }

    @Override
    public void run() {
        while(true){
            //获取锁
            try {
                lock.acquire(3, TimeUnit.SECONDS);
                if (tickets > 0){

                    System.out.println(Thread.currentThread()+":"+tickets);
                    Thread.sleep(100);
                    tickets--;
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                //释放锁
                try {
                    lock.release();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

        }
    }
}
```

```java
public class LockTest {

    public static void main(String[] args) {
        Ticket12306 ticket12306 = new Ticket12306();

        //创建客户端
        Thread t1 = new Thread(ticket12306,"携程");
        Thread t2 = new Thread(ticket12306,"飞猪");

        t1.start();
        t2.start();
    }
}
```





### 6、Zookeeper集群

#### 6.1 集群介绍

Leader选举

- `Serverid`：服务器ID

>  比如有三台服务器，编号分别是1,2,3。编号越大在选择算法中的权重越大。

- `Zxid`：数据ID

>  服务器中存放的最大数据ID，值越大说明数据越新，在选举算法中数据越新权重越大。

- 在Leader选举的过程中，如果某台ZooKeeper获得了超过半数的选票，则此ZooKeeper就可以成为Leader了。

如果集群中有3台机器，那么2号节点就是Leader，如果有5台，那么5号节点为Leader



#### 6.2 集群异常

- 3个节点的集群，从服务器挂掉，集群正常
- 个节点的集群，2个从服务器都挂掉，主服务器也无法运行。因为可运行的机器没有超过集群总数量的半数
- 集群中的主服务器挂了，集群中的其他服务器会自动进行选举状态，然后产生新得leader



#### 6.3 Zookeeper集群角色

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411021935404.png)

ZooKeeper集群服中务中有三个角色：

- Leader 

> 1. 处理事务请求
> 2. 集群内部各服务器的调度者

- Follower

> 1. 处理客户端非事务请求，转发事务请求给Leader服务器
>
> 2. 参与Leader选举投票

+ Observer 观察者

> 1. 处理客户端非事务请求，转发事务请求给Leader服务器