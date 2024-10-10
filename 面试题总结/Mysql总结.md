## 一、sql相关

1、【强制】超过三个表禁止 join。需要join的字段，数据类型必须绝对一致； 多表关联查询时，保证被关联的字段需要有索引。

说明：即使双表join也要注意表索引、SQL性能。

> - mysql在表join查询时候，使用嵌套循环方式（nested-loop algorithm）进行数据匹配。就是拿驱动表每一条和被驱动表所有数据依次进行匹配。假如有t1、t2、t3三个表进行关联，t1上有range范围筛选，t1和t2关联有ref类型索引关联，执行过程如下 
>
>   ```sql
>   for each row in t1匹配范围数据 
>   	for each row in t2匹配索引值 
>   		for each row in t3 
>   			t3满足连接条件
>   ```
>
> - 假设表A有M行记录，表B有N行记录。则需要M乘N次比较取交集。表B被扫描M*N次。这些都是磁盘全表扫描。如果M和N都比较大的时候会比较慢 
>
> - 为了提高join的效率，mysql在嵌套循环比对的时候进行了一定的修改优化。使用一个变种块嵌套循环（Block Nested-Loop Join Algorithm）。每次读取驱动表的时候读取多条，存放到一个join buffer 块里。这样每次多条和被驱动表的数据进行比较，这样就将原来的多次磁盘读取比较转移到了内存比较。这样被驱动表全表扫描次数变成了：`(M/buffer rows)*N`。一次读取的行数（buffer rows），由每行需要的数据大小和`join_buffer_size`大小两个因素决定。join_buffer_size的大小默认是256KB 
> - 因此如果驱动表的行数越少，join_buffer_size越大，扫描被驱动表的次数越少。这也是为什么要使用小表驱动大表的原因。如果驱动表join数据可以一次放到join_buffer_size中，则被驱动表只需要一次全表扫描。如果不能，驱动表会被分成多次读取到join_buffer_size中，然后再扫描被驱动表。 



2、Multi-Range Read

当在某个二级索引上进行范围查找时，通常在回表查询的过程中会发生大量的随机读。比如下面的语句

```sql
select * from user where uname like '王%';
```

- uname列上建有索引，可以从uname索引上获取主键值，然后再回表根据主键逐条从聚簇索引上查询行数据。这个时候获取的主键是无序的，会造成大量的随机读。 
- MRR(Multi-Range Read)优化就是为了解决这种场景，MRR处理过程是在根据索引获取到主键后不是立即进行回表查询，而是先将主键值放入到一个read_rnd_buffer中，然后对其进行排序，最后再有序的去聚簇索引检索行数据。如果对应主键索引值足够密集，这样可以大大减少随机读。 





## 二、分库分表

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012043786.jpg" style="zoom:80%;" />

- 只分表：将db库中的user表拆分为2个分表，user_0和user_1，这两个表还位于同一个库中。  

- 只分库：将db库拆分为db_0和db_1两个库，同时在db_0和db_1库中各自新建一个user表，db_0.user表和db_1.user表中各自只存原来的db.user表中的部分数据。

- 分库分表：将db库拆分为db_0和db_1两个库，db_0中包含user_0、user_1两个分表，db_1中包含user_2、user_3两个分表。



### 1、分库分表优点

- 分库的优点

  分库往往部署在多套集群中，也就意味着降低了单个集群的负载压力，提升整体的读写性能。

- 分表的优点 

  1. 提高数据操作的效率。举个例子说明，比如user表中现在有4000w条数据，此时我们需要在这个表中增加（insert）一条新的数据，insert完毕后，数据库会针对这张表重新建立索引，4000w行数据建立索引的系统开销还是不容忽视的。 
  2. 假如我们将这个大表分成4个分表，从user_0到user_3，4000w行数据平均下来，每个子表里只有1000W行数据，对1000W行的表中insert数据，建立索引的时间就会下降，从而提高了DB的运行时效率，进而提高并发量。

  3. 除了提高写的效率，更重要的是提高读效率，提高查询的性能。当然分表的好处还不止这些，还有诸如减少写操作时锁范围等，都会带来很多明显的优点。




### 2、分库分表的挑战

1. 分布式id：在分库分表后，我们不能再使用mysql的自增主键。因为在插入记录的时候，不同的库生成的记录的自增id会出现冲突。因此需要有一个全局的id生成器。 
2. 分布式事物：分库分表涉及到同时更新多个数据库，要么同时成功，要么同时失败。关于分布式事务，mysql支持XA事务，但是效率较低。柔性事务是目前比较主流的方案，柔性事务包括：最大努力通知型、可靠消息最终一致性方案以及TCC两阶段提交。https://cloud.tencent.com/developer/article/1543016 
3. 动态扩容：动态扩容指的是增加分库分表的数量。例如原来的user表拆分到2个库的4张表上。现在我们希望将分库的数量变为4个，分表的数量变为8个。这种情况下一般要伴随着数据迁移。例如在4张表的情况下，id为7的记录，7%4=3，因此这条记录位于user_3这张表上。但是现在分表的数量变为了8个，而7%8=7，而user_7这张表上根本就没有id=7的这条记录，因此如果不进行数据迁移的话，就会出现记录找不到的情况。 

> zebra提供一种动态扩容时，不需要数据迁移数据的方案（有局限）https://km.sankuai.com/page/28364249
>
> - 一个表通过id进行路由，该方案在原有id后面加上几位额外的数字，其中黑色的是原有id，蓝色表示分库编号，红色表示分表编号。分库和分表的编号各两位，表示最多100个分库，每个库最多100个分表。如果取一位，则分库和分表最多10个 
>
>   <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012042818.jpg" style="zoom:40%;" />
>
> - 对于一个id，我们取其倒数第3和倒数第4位数字，确定其在哪一个库，倒数后两位确定其在哪一个表。这样的话我们可以通过配置文件设置其路由规则。将id对100取整然后对100取模，即得到分库编号，将id对100取模，即得到分表编号。此时分库有2个，分表有2个，因此分库编号范围为0-1，分表编号范围为0-1 
>
>   ```sql
>   shard-dimension dbRule="#id#.intValue().intdiv(100)%100" 
>   dbIndexes="id[0-1]" 
>   tbRule="#id#.intValue() % 100" 
>   tbSuffix="everydb:[0,1]"
>   ```
>
> - 当我们扩容为4个分库，每个分库有3个分表时，只需要修改配置文件中分库和分表编号范围即可。这样新插入的数据可以插入新表中，原始数据查询时还会去之前的表进行查询 
>
>   ```sql
>   shard-dimension dbRule="#id#.intValue().intdiv(100)%100" 
>   dbIndexes="id[0-3]" 
>   tbRule="#id#.intValue() % 100" 
>   tbSuffix="everydb:[0,2]"
>   ```

4. 数据迁移：对于新的应用，如果预估到未来数据量比较大，可以提前进行分库分表。但是对于一些老的应用，单表数据量已经比较大了，这个时候就涉及到数据迁移的过程 





### 3、数据库中间件

典型的数据库中间件设计方案有2种：服务端代理(proxy：代理数据库)、客户端代理(datasource：代理数据源)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012042299.jpg" style="zoom:60%;" />





- 服务端代理(proxy：代理数据库)中：       

  我们独立部署一个代理服务，这个代理服务背后管理多个数据库实例。而在应用中，我们通过一个普通的数据源(c3p0、druid、dbcp等)与代理服务器建立连接，所有的sql操作语句都是发送给这个代理，由这个代理去操作底层数据库，得到结果并返回给应用。在这种方案下，分库分表和读写分离的逻辑对开发人员是完全透明的。开源中间件如：mycat、mysql-proxy、阿里的cobar

- 客户端代理(datasource：代理数据源)：       

  应用程序需要使用一个特定的数据源，其作用是代理，内部管理了多个普通的数据源(c3p0、druid、dbcp等)，每个普通数据源各自与不同的库建立连接。应用程序产生的sql交给数据源代理进行处理，数据源内部对sql进行必要的操作，如sql改写等，然后交给各个普通的数据源去执行，将得到的结果进行合并，返回给应用。数据源代理通常也实现了JDBC规范定义的API，因此能够直接与orm框架整合。在这种方案下，用户的代码需要修改，使用这个代理的数据源，而不是直接使用c3p0、druid、dbcp这样的连接池。开源中间件如：sharding-jdbc（仅支持java）





### 4、分库分表配置

```XML
<?xml version="1.0" encoding="UTF-8"?> 
<router-rule> 
	<table-shard-rule table="welife_users" global="false"> 
		<shard-dimension dbRule="#uid#%8" dbIndexes="welife[0-7]" tbRule="(#uid#.intdiv(8))%16" tbSuffix="alldb:[0,127]" isMaster="true"> 
		</shard-dimension> 
	</table-shard-rule> 
</router-rule>
```

- router-rule：router-rule下可以有多个表的分库分表规则。
  - table-shard-rule: 每个table-shard-rule表示一个表的分库分表规则。
    - table：表示逻辑表名（welife_users就是在SQL中将要使用的表名）的分表配置 
    - global：用于指定该表是全局表（小表），主要用于Join中的小表广播（后面会详细介绍），默认填false； 
  - shard-dimension: 分库分表维度
    - dbRule: 指定库名的路由规则表达式。zebra会解析出SQL中#uid#维度和值，并将该值带入该表达式计算出最终落到的数据库的index。支持多个字段组成的单维度。 
    - dbIndexes: 指定所有拆分的库的GroupDataSource的jdbcRef 
    - tbRule: 指定分表的路由规则表达式。zebra会解析出SQL中#uid#维度和值，并将该值带入该表达式计算出最终落到的数据库中表名的后缀index。支持多个字段组成的单维度。 
    - tbSuffix: 表的后缀命名规则，解析后会生成一个物理表名的链表。alldb表示全部的表后缀为0-127，也可以使用everydb:[0, 15]，即每个库都有0-15的后缀表 





### 5、分库分表路由原理

`ShardDataSource`在启动时会解析物理库索引(dbIndexes)和表名后缀(tbSuffix)，然后生成一个库和表的链表。比如在上面的例子中，解析welife[0-7]会得到welife0、welife1、......、welife7共8个分库的JdbcRef(如果使用本地配置，配置里应是dataSourcePool里的key)。对于alldb[0,127]，是指在8个分库中每个库都有16张分表，分表后缀递增。所以解析完库和表的配置后可以得到这样一个链表

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012042995.jpg" style="zoom:50%;" />

`ShardDataSource`在进行路由时会根据库和表的路由规则计算出一个索引，然后拿这个索引到库和表的链表中寻找对应下标的物理库和物理表，在进行表的路由时，最终算的是某个确定的库上的第几个表，因此在tbRule的配置里，计算结果是根据dbRule找到某个库中的某个表的索引。例如当uid = 17时，#uid#%8=1，(#uid#).intdiv(8)%16=2，所以会路由到welife1的表welife_users18中。



### 6、分库分表的join

分库分表后，就不能再和单表一样进行Join了

#### 6.1 小表广播

适用在一些配置表，或者一般不怎么变更的小表上，然后分表需要和这个小表进行Join。对于小表需要在每个分库上复制一个，所有对这张表的Join就会变成单库Join了。另外，对于这张表的任何变更，zebra后端会使用binlog的方式自动同步到每一个分库上去。所以这种方式叫做小表广播。

**注意：小表广播需要单独配置同步服务，另外同步会有一定的延迟！**



#### 6.2 Binding Table

适用在若干个分表上进行Join，前提是这些分表的分表逻辑都是一样的，意味着所有的Join都可以在同一个数据库上进行。

比如：表a和表b同时都要分表，而且都是使用UserID进行分表，分表的个数和分库的个数都是一样的。



### 7、分片数的选择

#### 7.1 表数目决策

- 一般情况下，建议单个物理分表的容量不超过1000万行数据。通常可以预估2到5年的数据增长量，用估算出的总数据量除以总的物理分库数，再除以建议的最大数据量1000万，即可得出每个物理分库上需要创建的物理分表数 
- 表的数量不宜过多，涉及到聚合查询或者分表键在多个表上的SQL语句，就会并发到更多的表上进行查询。举个例子，分了4个表和分了2个表两种情况，一种需要并发到4表上执行，一种只需要并发到2张表上执行，显然后者效率更高。 •表的数目不宜过少，少的坏处在于一旦容量不够就又要扩容了，而分库分表的库想要扩容是比较麻烦的。一般建议一次分够。建议表的数目是2的幂次个数，方便未来可能的迁移。 



#### 7.2 库数目决策

- 计算公式：按照存储容量来计算 = （3到5年内的存储容量）/  单个库建议存储容量   （单个库建议存储容量  <300G以内） 
- DBA的操作，一般情况下，会把若干个分库放到一台实例上去。未来一旦容量不够，要发生迁移，通常是对数据库进行迁移。所以库的数目才是最终决定容量大小。 
- 最差情况，所有的分库都共享数据库机器。最优情况，每个分库都独占一台数据库机器。一般建议一个数据库机器上存放8个数据库分库。



#### 7.3 分表策略

| 分表方式 | 解释                                                         | 优点                                                         | 缺点                                                         | 试用场景                                       |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------------- |
| Hash     | 拿分表键的值Hash取模进行路由。最常用的分表方式               | 1、数据量散列均衡，每个表的数据量大致相同。<br>2、请求压力散列均衡，不存在访问热点 | 一旦现有的表数据量需要再次扩容时，需要涉及到数据移动，比较麻烦。所以一般建议是一次性分够。 | 在线服务。一般均以UserID或者ShopID等进行hash。 |
| Range    | 拿分表键按照ID范围进行路由，比如id在1-10000的在第一个表中，10001-20000的在第二个表中，依次类推。这种情况下，分表键只能是数值类型。 | 1、数据量可控，可以均衡，也可以不均衡 <br>2、扩容比较方便，因为如果ID范围不够了，只需要调整规则，然后建好新表即可 | 无法解决热点问题，如果某一段数据访问QPS特别高，就会落到单表上进行操作。 | 离线服务                                       |
| 时间     | 拿分表键按照时间范围进行路由，比如时间在1月的在第一个表中，在2月的在第二个表中，依次类推。这种情况下，分表键只能是时间类型。 | 1、扩容比较方便，因为如果时间范围不够了，只需要调整规则，然后建好新表即可。 | 1、数据量不可控，有可能单表数据量特别大，有可能单表数据量特别小 <br>2、无法解决热点问题，如果某一段数据访问QPS特别高，就会落到单表上进行操作。 | 离线服务。比如线下运营使用的表、日志表等等     |



#### 7.4 自定义分片策略

> zebra的分库分表规则使用的是groovy脚本，理论上可以支持定制各种复杂的路由规则

有两个库testdb0、testdb1要对表TestTable按Id进行分表，Id<=10000的会落到testdb0上，hash到4个分表上，10000<Id<=50000的落到testdb1上，hash到8个分表上，Id > 50000的落到testdb1的第9张表(默认表，和逻辑表名相同)。

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<router-rule> 
    <table-shard-rule table="TestTable" global="false" generatedPK="Id"> 
        <shard-dimension 
             dbRule="def result; if (#Id# <= 10000) { result = 0; } else { result = 1; }; return result;" 
             dbIndexes="testdb[0-1]" 
             tbRule="def result; def param = #Id#; if (param <= 10000) { result = param % 4; } else if (param <= 50000) { result = param % 8; } else { result = 8; }; return result;" tbSuffix="testdb0:{0,3}&testdb1:{0,7}&testdb1:[$]" 
             isMaster="true"> 
        </shard-dimension> 
    </table-shard-rule> 
</router-rule>
```



msc_deliver分片规则

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012041879.jpg)



talos分片规则

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012041412.jpg)

`result = shardByDay(toDate(#account_date#), date('2024-01-01 00:00:00'));` 按天分表，每一天分一张表，起始日期是2024-01-01 00:00:00



### 8、动态扩缩容

> 整体按范围分片，保证扩容时老数据不需要迁移。在范围内，按取模分片，让每个范围内的数据分布大致均匀。

- 结合范围分片和取模分片的优点，订单系统先按范围分片，10月业务量小，分两个节点node1和node2，节点数表示对数据拆分的粒度，orderId%10，得到最终存放的节点位置node1或node2，如结果为0，2，4，6，8的在路由映射表中指定存到node1结点。 
- 11月大促订单增多，假设比10月多加一个节点node3，为了使数据分布更均匀，调整拆分粒度（节点数）为20，使11月的数据更均匀的落在node1, node2, node3。 
- node1和node2不仅存了10月的数据，还存了11月的数据，那它们的数据会比node3更多，node3只存了11月数据的1/3，可以给node3映射多一点。这些配置信息存到redis里， shardingsphere分片时，通过自定义的算法去获取这些配置信息。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012041815.jpg" style="zoom:50%;" />





### 9、弹性伸缩

- 马上要到七夕节了，现有的分库分表扛不住了，此时需要对分库、集群数量进行扩容 
- 分库分表现在分了16个库，占了8个集群，资源利用率很低，成本太高，此时就需要缩容 



#### 9.1 弹性扩容

弹性扩容主要适用于当前分库分表无法承担当前或未来的业务流量，需要进行扩展。当前面向分库分表的扩容主要有两种方式

**1、路由规则不变，分库资源扩容**

分表的路由规则不会变，但是分库会变更，比如原来4个分库在2个集群，经过2倍扩容后4个分库会落在4个集群。

此种方式只需要把分表规则中的物理库索引替换为新的jdbcref即可。

> 资源（如CPU、内存、磁盘I/O）是与物理机器直接相关的，而集群的划分方式主要影响资源的分配和管理方式。将4个分库从2个集群切换到4个集群，主要区别在于资源隔离、负载均衡、故障隔离（集群故障，影响范围小）

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012040896.jpg" style="zoom:50%;" />



**2、分表重新路由**

以下图为例：分表的路由规则将会更改，比如原来有1024张分表分布在两个分库，经过扩容后，1024张分表将会分布在4个分库，每个分库256张分表。

此种方式相比第一种，分库路由和分表路由都要做修改，但是分表的映射关系是可以保持不变的。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012040702.jpg" style="zoom:50%;" />





#### 9.2 弹性缩容

主要适用于当前分库分表资源利用率过低，成本高。当前面向分库分表的缩容主要有两种方式：和扩容方式类似。

**1、路由规则不变，分库资源缩容**

以下图为例：分表的路由规则不会变，但是分库会变更，比如原来4个分库在4个集群，经过2倍缩容后4个分库会落在2个集群。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012040873.jpg" style="zoom:40%;" />



**2、分表重新路由**

以下图为例：分表的路由规则将会更改，比如原来有1024张分表分布在4个分库，经过缩容后，1024张分表将会分布在2个分库，每个分库512张分表。

此种方式相比第一种，分库路由和分表路由都要做修改，但是分表的映射关系是可以保持不变的。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012040610.jpg" style="zoom: 50%;" />





## 三、Mysql相关

### 1、事物失效的场景

1、对非public的方法使用事物，事物会失效。因为Spring的事物是使用aop动态代理实现的，有jdk动态代理和cglib动态代理，Spring的事物默认只在public方法上生效。`allowPublicMethodsOnly()`默认返回true，即只允许public方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012040803.jpg)



2、final方法无法使用事物。因为对final方法使用事物，使用的是cglib动态代理，cglib是通过继承目标类实现动态代理，而final方法无法被继承

3、方法中抛出非`RuntimeException`异常，事物默认是不会回滚的。`@Transactional`针对`RuntimeException`和`Error`才会回滚，如果有非`RuntimeException`，需要手动使用`@Transactional(rollbackFor = Exception.class)`指定

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202409012039092.jpg)



4、代码中捕获了异常。如果代码中的代码块捕获了可能存在的异常，相当于是自己处理了代码发生的一切未知错误，这时候，事务是不能发现出现问题，从而导致代码不会回滚。

5、方法内部直接调用事务的方法。`updateStatus`方法拥有事务的能力是因为spring aop生成代理了对象，但此时是直接调用this对象的方法，所以`updateStatus`方法没有事务功能。必须先通过`AopContext.currentProxy()`获取代理对象，然后调用

```java
@Service 
public class UserService { 
    @Autowired 
    private UserMapper user; 
    
    public void add(UserModel userModel) { 
        usermapper.insert(userModel); 
        updateStatus(userModel); 
    } 
    
    @Transactional 
    public void updateStatus(UserModel userModel) { 
        doSomeThing(); 
    } 
}
```

6、多线程调用。方法中开了线程去做一部分事情，这部分事情发生了异常，也不会回滚。同一个事务，是指同一个数据库连接，只有拥有同一个数据库连接才能同时提交和回滚。如果在不同的线程，拿到的数据库连接肯定是不一样的，所以是不同的事务。

```java
public void add(UserModel userModel) { 
    usermapper.insert(userModel); 
    new Thread(() -> { 
        updateStatus(userModel); 
    }).start(); 
}
```



### 2、大事物如何处理

1、少用`@Transactional`注解

- 使用`@Transactional`注解相当于声明式事物，一般加在某个业务方法上，会导致整个业务方法都在同一个事务中，粒度太粗，不好控制事务范围，是出现大事务问题的最常见的原因 

- 可以使用编程式事务，在spring项目中使用`TransactionTemplate`类的对象，手动执行事务

  ```java
  @Autowired 
  privateTransactionTemplate transactionTemplate; 
  
  public void save(Entity entity) { 
      selectData(); 
      transactionTemplate.execute((obj) => { 
          addData(entity ); 
          updateData(entity ); 
          returnBoolean.TRUE; 
      }) 
  }
  ```

2、将查询(select)方法放到事务外

3、事物中避免RPC调用

4、避免一次性处理太多数据。比如批量更新时，可以分批次进行更新

5、使用异步。比如保存订单和发货，不需要在一个事物中执行，订单保存后直接发送MQ进行发货即可





### 3、多数据源

#### 3.1 数据源切换原理

通过扩展Spring提供的抽象类`AbstractRoutingDataSource`，可以实现切换数据源。

- `resolvedDataSources`：当Spring容器创建`AbstractRoutingDataSource`对象时，通过调用`afterPropertiesSet`复制上述目标数据源。因此一旦数据源实例对象创建完毕，业务无法再添加新的数据源。 
- `determineCurrentLookupKey`：该方法为抽象方法，通过扩展这个方法来实现数据源的切换。目标数据源的结构为：`Map<Object, DataSource>`，其中key为`lookup key`。`lookup key`通常是绑定在线程上下文中，根据这个key去`resolvedDataSources`中取出DataSource。 



##### 3.1.1 创建数据源

**数据源配置**

```yaml
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource 
spring.datasource.driverClassName=com.mysql.jdbc.Driver # 主数据源 
spring.datasource.druid.master.url=jdbcUrl 
spring.datasource.druid.master.username=*** 
spring.datasource.druid.master.password=*** # 其他数据源 
spring.datasource.druid.second.url=jdbcUrl 
spring.datasource.druid.second.username=*** 
spring.datasource.druid.second.password=***
```



**注册bean**

```java
@Configuration 
public class DynamicDataSourceConfig { 
    @Bean @ConfigurationProperties("spring.datasource.druid.master") 
    
    public DataSource firstDataSource() { 
        return DruidDataSourceBuilder.create().build(); 
    } 
    
    @Bean @ConfigurationProperties("spring.datasource.druid.second") 
    public DataSource secondDataSource() { 
        return DruidDataSourceBuilder.create().build(); 
    } 
    
    @Bean 
    @Primary 
    public DynamicDataSource dataSource(DataSource firstDataSource, DataSource secondDataSource) { 
        Map<Object, Object> targetDataSources = new HashMap<>(5); 
        targetDataSources.put(DataSourceNames.FIRST, firstDataSource); 
        targetDataSources.put(DataSourceNames.SECOND, secondDataSource); 
        return new DynamicDataSource(firstDataSource, targetDataSources); 
    } 
}
```



##### 3.1.2 AOP处理

在业务方法添加`@SwitchDataSource`注解，更新当前线程上下文`DataSourceContextHolder`中存储的key，即可实现数据源切换。

```JAVA
@Documented 
@Retention(RetentionPolicy.RUNTIME) 
@Target({ElementType.METHOD}) 
public @interface SwitchDataSource { 
    String value(); 
}
```



#### 3.2 多数据源实现事物

采用一种类似于“双提交”的策略来解决。首先，让两个数据库执行所需的操作，然后立即提交。接下来，如果整个方法执行成功，就提交这两个数据库的事务。如果在方法执行过程中出现了问题，我们会回滚这两个数据库的事务。



### 4、sql关键字顺序

如果select语句同时包含有group by，having，order by，limit，那么他们的顺序是where、group by，having，order by，limit
where用于对行进行条件筛选，having是分组过滤，表示对分组后的结果操作

```sql
-- 统计各个部门的平均工资，且大于1000，从高到低排序，取出前两行 

SELECT deptno, AVG(sal) as avg_sal 
	FROM emp 
	WHERE xxx 
	GROUP BY deptno 
	HAVING avg_sal > 1000 
	ORDER BY avg_sal DESC 
	limit 0, 2
```



### 5、group by原理

通过对`group by`的sql语句使用`explain`命令可以发现，Extra字段有`Using temporary`和`Using filesort`，即使用了临时表和文件排序

针对下边的表使用group by分析

```SQL
CREATE TABLE `staff` ( 
    `id` bigint(11) NOT NULL AUTO_INCREMENT COMMENT '主键id', 
    `id_card` varchar(20) NOT NULL COMMENT '身份证号码', 
    `name` varchar(64) NOT NULL COMMENT '姓名', 
    `age` int(4) NOT NULL COMMENT '年龄', 
    `city` varchar(64) NOT NULL COMMENT '城市', 
    PRIMARY KEY (`id`) 
) ENGINE=InnoDB AUTO_INCREMENT=15 DEFAULT CHARSET=utf8 COMMENT='员工表';

```

`explain select city ,count(*) as num from staff group by city;  `



**group by执行流程**

- 创建内存临时表，表里有两个字段city和num； 
- 全表扫描staff的记录，依次取出`city = 'X'`的记录。判断临时表中是否有为` city='X'`的行，没有就插入一个记录` (X,1)`，如果临时表中有`city='X'`的行的行，就将x 这一行的num值加 1； 
- 遍历完成后，再根据字段city做排序，得到结果集返回给客户端。 



**where和having的区别**

- having子句用于分组后筛选，where子句用于行条件筛选 
- having一般都是配合group by 和聚合函数一起出现如`(count(),sum(),avg(),max(),min())` 
- where条件子句中不能使用聚集函数，而having子句可以
- having只能用在group by之后，where执行在group by之前 
- 如果group by不配合聚合函数使用，那么返回的就是该分组的第一条数据 



**group by的优化**

- group by后的字段加索引：group by的语义逻辑就是统计不同的值出现的个数。如果这些值一开始就是有序的，直接往下扫描统计就好了，就不用临时表来记录并统计结果。因此如果group by后的字段建立了索引，那么就不需要使用临时表，同时也不会造成文件排序





### 6、深分页问题

https://juejin.cn/post/7012016858379321358



#### 6.1 为什么深分页会变慢？

**表结构**

```sql
CREATE TABLE account (
  id int(11) NOT NULL AUTO_INCREMENT COMMENT '主键Id',
  name varchar(255) DEFAULT NULL COMMENT '账户名',
  balance int(11) DEFAULT NULL COMMENT '余额',
  create_time datetime NOT NULL COMMENT '创建时间',
  update_time datetime NOT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (id),
  KEY idx_name (name),
  KEY idx_update_time (update_time) //索引
) ENGINE=InnoDB AUTO_INCREMENT=1570068 DEFAULT CHARSET=utf8 ROW_FORMAT=REDUNDANT COMMENT='账户表';

```



**执行sql**

```sql
select id, name, balance from account where update_time> '2020-09-19' limit 100000, 10;
```



**sql执行流程**

1、通过**二级索引**`idx_update_time`，过滤`update_time`条件，找到满足条件的记录ID。

2、通过ID，回到**主键索引**，找到满足记录的行，然后取出展示的列（**回表**）

3、扫描满足条件的100010行，然后扔掉前100000行，返回。



**sql变慢的原因**

- limit语句会先扫描`offset+n`行，然后再丢弃掉前offset行，返回后n行数据。也就是说`limit 100000,10`，就会扫描100010行，而`limit 0,10`，只扫描10行。

- `limit 100000,10` 扫描更多的行数，也意味着**回表**更多的次数。



#### 6.2 优化方案

##### 6.2.1 子查询优化

> 上边的SQL，回表了100010次，实际上，我们只需要10条数据，也就是我们只需要10次回表其实就够了。因此，我们可以通过**减少回表次数**来优化。
>
> 先通过子查询通过二级索引查询出对应的主键id，然后使用查询出的id直接通过主键索引查询，这样就避免了回表操作

```sql
select id, name, balance 
	FROM account 
	where id >= (
        select a.id 
        from account a 
        where a.update_time >= '2020-09-19' 
        limit 100000, 1) 
    LIMIT 10;
```



##### 6.2.2 INNER JOIN 延迟关联

> 延迟关联的优化思路，**跟子查询的优化思路其实是一样的**：都是把条件转移到主键索引树，然后减少回表。不同点是，延迟关联使用了`inner join`代替子查询

```sql
SELECT acct1.id, acct1.name, acct1.balance 
	FROM account acct1 
	INNER JOIN 
		(SELECT a.id 
            FROM account a 
            WHERE a.update_time >= '2020-09-19' 
            ORDER BY a.update_time 
         	LIMIT 100000, 10) AS acct2 
    on acct1.id = acct2.id;
```



##### 6.2.3 标签记录法

limit 深分页问题的本质原因是：偏移量（offset）越大，mysql就会扫描越多的行，然后再抛弃掉。这样就导致查询性能的下降。

**标签记录**，就是标记一下上次查询到哪一条了，下次再来查的时候，从该条开始往下扫描。

假设上一次记录到100000，则SQL可以修改为

```sql
select id, name, balance FROM account where id > 100000 order by id limit 10;
```





### 7、为什么不建议使用我数据库唯一键做幂等控制

在做幂等控制的时候，通常会选择基于数据库的唯一性约束来做，流程如下：

1. 根据幂等字段查询是否有历史记录

2. 如果有，则直接返回

3. 如果没有，则执行数据库操作，并捕获数据库唯一性约束冲突异常

4. 没有异常，返回成功

5. 捕获到异常，反查一下数据，如果真的成功，则返回成功



**但是不建议这么做**

1、依赖`insert`，以上的方案，有一个局限性，就是需要在做`insert`的时候才行，这种情况下会因为幂等操作导致重复记录，而触发唯一性约束冲突。

2、依赖异常，这里是通过catch了`DuplicateKeyException`依赖来做的处理，这样就对异常有了依赖，不建议在代码中根据异常来控制业务流程，另外，这里和`DuplicateKeyException`绑定了，一旦有一天换了数据库、换了ORM框架，换了Spring版本等等，这个异常就可能会发生改变，也许就不再抛这个异常了，那么就会出现问题。

3、通过异常控制业务流程，存在性能问题，异常的生成和处理涉及到填充栈跟踪信息，频繁的抛出和捕获异常会导致性能下降





### 8、唯一索引和普通索引哪个插入效率更高

**答：普通索引**

1、当需要更新一个数据页时，如果数据页在内存中就直接更新，而如果这个数据页还没有在内存中的话，在不影响数据一致性的前提下，InnoDB会将这些更新操作缓存在`change buffer`中，这样就不需要从磁盘中读入这个数据页了。在下次查询需要访问这个数据页的时候，将数据页读入内存，然后执行`change buffer`中与这个页有关的操作。通过这种方式就能保证这个数据逻辑的正确性。`change buffer`在内存中有拷贝，也会被写入到磁盘上。

2、将`change buffer`中的操作应用到原数据页，得到最新结果的过程称为merge。除了访问这个数据页会触发merge外，系统有后台线程会定期merge。在数据库正常关闭（shutdown）的过程中，也会执行merge操作。

3、对于唯一索引来说，所有的更新操作都要先判断这个操作是否违反唯一性约束。比如，要插入(4,400)这个记录，就要先判断现在表中是否已经存在k=4的记录，因此必须要将该数据页读入内存才能判断。如果已经读入到内存了，那直接更新内存会更快，就没必要使用change buffer了。因此，唯一索引的更新就不能使用`change buffer`，只有普通索引可以使用。

4、`change buffer`适用于读多写少的业务，页面在写完以后马上被访问到的概率比较小，`change buffer`记录的数据会很多，然后一次性做merge

5、`change buffer`的变更也会记录到`redo log`中，保证其可靠性

































