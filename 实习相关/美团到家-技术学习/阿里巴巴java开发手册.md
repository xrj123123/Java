## 1、命名和代码格式规范

1、【强制】 POJO类中布尔类型的变量，都不要加 is前缀，否则部分框架解析会引起序列化错误。

反例：定义为基本数据类型 Boolean isDeleted的属性，它的方法也是 isDeleted()，RPC框架在反向解析的时候，“误以为”对应的属性名称是 deleted，导致属性获取不到，进而抛出异常



2、领域模型命名规约

- 数据对象：xxxDO，xxx即为数据表名。 
- 数据传输对象：xxxDTO，xxx为业务领域相关的名称。 
- 展示对象：xxxVO，xxx一般为网页名称。 
- POJO是DO/DTO/BO/VO的统称，禁止命名成 xxxPOJO。 



3、常量的复用层次有五层：跨应用共享常量、应用内共享常量、子工程内共享常量、包内共享常量、类内共享常量。

1） 跨应用共享常量：放置在二方库中，通常是 client.jar中的constant目录下。

2） 应用内共享常量：放置在一方库中，通常是子模块中的constant目录下。

> 反例：易懂变量也要统一定义成应用内共享常量，两个人在两个类中分别定义了表示“是”的变量：
>
> 类A中：`public static final String YES = "yes"`
>
> 类B中：`public static final String YES = "y";`
>
> `A.YES.equals(B.YES)`，预期是true，但实际返回为false，导致线上问题。

3） 子工程内部共享常量：即在当前子工程的 constant目录下。

4） 包内共享常量：即在当前包下单独的 constant目录下。

5） 类内共享常量：直接在类内部 private static final定义。



4、【强制】单行字符数限制不超过 120 个，超出需要换行，换行时遵循如下原则：

1） 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例。

2） 运算符与下文一起换行。

3） 方法调用的点符号与下文一起换行。

4） 方法调用中的多个参数需要换行时，在逗号后进行。

5） 在括号前不要换行，见反例

> 正例：
>
> ```java
> StringBuffer sb = new StringBuffer(); 
> // 超过 120个字符的情况下，换行缩进 4个空格，点号和方法名称一起换行 
> sb.append("zi").append("xin")... 
>     .append("huang")... 
>     .append("huang")... 
>     .append("huang");
> ```
>
> 反例：
>
> ```java
> StringBuffer sb = new StringBuffer(); 
> // 超过 120个字符的情况下，不要在括号前换行 
> sb.append("zi").append("xin")...append 
>     ("huang"); 
> // 参数很多的方法调用可能超过 120个字符，不要在逗号前换行 
> method(args1, args2, args3, ... 
>        , argsX);
> ```





## 2、OOP规约

1、【强制】避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增加编译器解析成本，直接用类名来访问即可。

> 不管使用对象还是直接使用类调用静态方法，都是执行invokestatic指令，只不过使用对象调用时，会先执行aload_1，然后执行pop，即先加载对象到操作数栈，然后直接弹出。所以调用静态方法时，最好直接使用类名调用，这样会少执行两条指令



2、【强制】Object的equals方法容易抛空指针异常，应使用常量或确定有值的对象来调用equals。

> 正例：`"test".equals(object);`
>
> 反例：`object.equals("test");`



3、【强制】所有的相同类型的包装类对象之间值的比较，全部使用equals方法比较。

> 对于Integer var = ? 在-128至 127范围内的赋值，Integer对象是在IntegerCache.cache产生，会复用已有对象，这个区间内的 Integer值可以直接使用==进行判断，但是这个区间之外的所有数据，都会在堆上产生，并不会复用已有对象，推荐使用equals方法进行判断。



4、关于基本数据类型与包装数据类型的使用标准如下：

1） 【强制】所有的 POJO类属性必须使用包装数据类型。

2） 【强制】RPC方法的返回值和参数必须使用包装数据类型。

3） 【推荐】所有的局部变量使用基本数据类型。

> POJO类属性没有初值是提醒使用者在需要使用时，必须自己显式地进行赋值，任何NPE问题，或者入库检查，都由使用者来保证。
>
> 正例：数据库的查询结果可能是 null，因为自动拆箱，用基本数据类型接收有 NPE风险。
>
> 反例：比如显示成交总额涨跌情况，即正负 x%，x为基本数据类型，调用的 RPC服务，调用不成功时，返回的是默认值，页面显示为0%，这是不合理的，应该显示成中划线。所以包装数据类型的null值，能够表示额外的信息，如：远程调用失败，异常退出。



5、【强制】禁止在POJO类中，同时存在对应属性` xxx的isXxx()`和`getXxx()`方法。

> 框架在调用属性 xxx 的提取方法时，并不能确定哪个方法一定是被优先调用到。
>
> JavaBean规范的属性访问规则
>
> - 对于boolean类型的属性xxx，规范允许使用isXxx()作为其getter方法。 
> - 对于非boolean类型的属性，或者Boolean包装类型的属性，使用getXxx()作为getter方法。 
> - 对于所有属性，使用setXxx(Type value)作为setter方法。 
>
> 当一个POJO类对于同一个属性同时提供了isXxx()和getXxx()方法时，框架在通过反射自动调用getter方法获取属性值的时候，可能无法确定应该调用哪一个方法
>
> - 反射机制的属性识别：许多框架使用反射来动态识别和调用getter和setter方法。如果存在两个方法都符合getter方法的命名规则，框架可能没有明确的规则来决定优先调用哪一个 
> - 框架的实现差异：不同的框架可能有不同的实现逻辑。一些框架可能优先调用isXxx()方法，而另一些框架可能优先调用getXxx()方法，或者根据方法的声明顺序来决定。这种实现上的差异增加了歧义 



6、【推荐】慎用Object的clone方法来拷贝对象。

对象的clone方法默认是浅拷贝，若想实现深拷贝需要重写 clone方法实现域对象的深度遍历式拷贝。



## 3、集合处理

1、【强制】关于hashCode和equals的处理，遵循如下规则： 

1） 只要重写 equals，就必须重写hashCode。因为两个对象通过equals比较相等，那么hashcode一定相等

2） 因为 Set存储的是不重复的对象，依据 hashCode和equals进行判断，所以 Set存储的对象必须重写这两个方法。

3） 如果自定义对象作为Map的键，那么必须重写 hashCode和equals。



2、【强制】 ArrayList的subList结果不可强转成ArrayList，否则会抛出`ClassCastException`异常，即`java.util.RandomAccessSubList cannot be cast to java.util.ArrayList`。

> subList 返回的是 ArrayList 的内部类 SubList，并不是 ArrayList，而是ArrayList的一个视图，对于SubList子列表的所有操作最终会反映到原列表上，例如修改，添加、删除等。



3、【强制】在subList场景中，高度注意对原集合元素的增加或删除，均会导致子列表的遍历、增加、删除产生ConcurrentModificationException 异常。



4、【强制】使用集合转数组的方法，必须使用集合的 toArray(T[] array)，传入的是类型完全一样的数组，大小就是list.size()。

> - 使用toArray带参方法，入参分配的数组空间不够大时，toArray方法内部将重新分配内存空间，并返回新数组地址 
> - 如果数组元素个数大于实际所需，下标为[ list.size() ] 的数组元素将被置为 null，其它数组元素保持原值，因此最好将方法入参数组大小定义与集合元素个数一致。 
>
> 反例：直接使用toArray无参方法存在问题，此方法返回值只能是 Object[]类，若强转其它类型数组将出现ClassCastException错误。



5、【强制】使用工具类 Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方法，它的add/remove/clear方法会抛出`UnsupportedOperationException`异常。

> asList的返回对象是一个 Arrays内部类，并没有实现集合的修改方法。 Arrays.asList体现的是适配器模式，只是转换接口，后台的数据仍是数组。
>
> ```java
> String[] str = new String[] { "you", "wu" }; 
> List list = Arrays.asList(str);
> ```
>
> 第一种情况：`list.add("yangguanbao");` 运行时异常。
>
> 第二种情况：`str[0] = "gujin";` 那么list.get(0)也会随之修改。



6、【强制】泛型通配符`<? extends T>`来接收返回的数据，此写法的泛型集合不能使用 add方法，而`<? super T>`不能使用get方法，作为接口调用赋值时易出错。

> `PECS(Producer Extends Consumer Super)`原则：
>
> - 频繁往外读取内容的，适合用`<? extends T>`。 
> - 经常往里插入的，适合用`<? super T>`。 



## 二、并发处理

1、【强制】线程资源必须通过线程池提供，不允许在应用中自行显式创建线程。 使用线程池的好处是减少在创建和销毁线程上所消耗的时间以及系统资源的开销，解决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。



2、【强制】线程池不允许使用 Executors去创建，而是通过 ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

Executors返回的线程池对象的弊端如下：

- FixedThreadPool和SingleThreadPool：使用的是无界的LinkedBlockingQueue，允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。 
- CachedThreadPool：使用的是同步队列SynchronousQueue，允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。 
- ScheduledThreadPool：使用的是延迟阻塞队列DelayedWorkQueue，队列满了会扩容为原来的1.5倍，允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。同时他的最大线程数为Integer.MAX_VALUE，但是由于DelayedWorkQueue的存在，因此最多corePoolSize个线程运行 



3、【强制】高并发时，同步调用应该去考量锁的性能损耗。能用无锁数据结构，就不要用锁；能锁区块，就不要锁整个方法体；能用对象锁，就不要用类锁。尽可能使加锁的代码块工作量尽可能的小，避免在锁代码块中调用 RPC 方法。



4、【推荐】在并发场景下，通过双重检查锁（double-checked locking）实现延迟初始化的优化问题隐患，需要将目标属性声明为 volatile型。

> 字节码指令在执行时，可能会出现指令重排序。指令重排序在单线程下时没有问题的，但是在多线程下就会出现线程安全问题。
>
> 创建一个对象的流程：
>
> 1. 分配内存 
> 2. 初始化对象 
> 3. 使该对象指向分配的内存 假设发生了指令重排序，执行顺序为1、3、2，此时线程1执行了1、3，然后线程2来获取对象，发现对象不为null，然后直接使用，但此时对象还未初始化。因此需要将该对象使用volatile修饰，通过读写屏障禁止指令重排序
>
> ```java
> class LazyInitDemo { 
>     private volatile Helper helper = null; 
>     
>     public Helper getHelper() { 
>         if (helper == null) { 
>             synchronized(this) { 
>                 if (helper == null) { 
>                     helper = new Helper(); 
>                 } 
>             } 
>         } 
>         return helper; 
>     } 
> }
> ```





## 三、异常

1、【强制】有try块放到了事务代码中，catch异常后，如果需要回滚事务，一定要注意手动回滚事务。 



2、【强制】finally块必须对资源对象、流对象进行关闭，有异常也要做 try-catch。

> 说明：如果JDK7及以上，可以使用`try-with-resources`方式。



3、【强制】不要在finally块中使用return。

> 说明：finally块中的return返回后方法结束执行，不会再执行 try块中的return语句。同时finnally中有rerturn会吞掉异常





## 四、日志

1、【强制】应用中不可直接使用日志系统（Log4j、Logback）中的API，而应依赖使用日志框架SLF4J中的API，使用门面模式的日志框架，有利于维护和各个类的日志处理方式统一。

```java
import org.slf4j.Logger; 
import org.slf4j.LoggerFactory; 

private static final Logger logger = LoggerFactory.getLogger(Abc.class);
```



2、【强制】对trace/debug/info级别的日志输出，必须使用条件输出形式或者使用占位符的方式。

> 说明：`logger.debug("Processing trade with id: " + id + " and symbol: " + symbol);`
>
> 如果日志级别是warn，上述日志不会打印，但是会执行字符串拼接操作，如果 symbol是对象，会执行toString()方法，浪费了系统资源，执行了上述操作，最终日志却没有打印。
>
> 正例：（条件）建设采用如下方式
>
> ```java
> if (logger.isDebugEnabled()) { 
>     logger.debug("Processing trade with id: " + id + " and symbol: " + symbol); 
> }
> ```
>
> 正例：（占位符）
>
> ```java
> logger.debug("Processing trade with id: {} and symbol : {} ", id, symbol);
> ```



3、【强制】异常信息应该包括两类信息：案发现场信息和异常堆栈信息。如果不处理，那么通过关键字throws往上抛出。

> 正例：`logger.error(各类参数或者对象 toString() + "_" + e.getMessage(), e);`





## 五、MySQL

### 1、建表

1、【强制】表达是与否概念的字段，必须使用 is_xxx的方式命名，数据类型是 unsigned tinyint（1 表示是，0 表示否）。

> 说明：任何字段如果为非负数，必须是 unsigned。
>
> 注意：POJO类中的任何布尔类型的变量，都不要加 is前缀，所以，需要在<resultMap>设置从is_xxx到Xxx的映射关系。数据库表示是与否的值，使用 tinyint类型，坚持is_xxx的命名方式是为了明确其取值含义与取值范围。
>
> 正例：表达逻辑删除的字段名 is_deleted，1 表示删除，0 表示未删除。



2、【强制】主键索引名为 pk_字段名；唯一索引名为 uk_字段名；普通索引名则为 idx字段名。

> 说明：
>
> - pk 即primary key；
> - uk 即 unique key；
> - idx_ 即index的简称。



3、【强制】小数类型为 decimal，禁止使用float和double。

> 说明：float和double在存储的时候，存在精度损失的问题，很可能在值的比较时，得到不正确的结果。如果存储的数据范围超过 decimal的范围，建议将数据拆成整数和小数分开存储。



4、【推荐】单表行数超过 500万行或者单表容量超过 2GB，才推荐进行分库分表。

说明：如果预计三年后的数据量根本达不到这个级别，请不要在创建表时就分库分表。



### 2、索引

1、【强制】业务上具有唯一特性的字段，即使是多个字段的组合，也必须建成唯一索引。

> 说明：不要以为唯一索引影响了 insert速度，这个速度损耗可以忽略，但提高查找速度是明显的；另外，即使在应用层做了非常完善的校验控制，只要没有唯一索引，根据墨菲定律，必然有脏数据产生。



2、【强制】超过三个表禁止 join。需要join的字段，数据类型必须绝对一致； 多表关联查询时，保证被关联的字段需要有索引。

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
>
> - 因此如果驱动表的行数越少，join_buffer_size越大，扫描被驱动表的次数越少。这也是为什么要使用小表驱动大表的原因。如果驱动表join数据可以一次放到join_buffer_size中，则被驱动表只需要一次全表扫描。如果不能，驱动表会被分成多次读取到join_buffer_size中，然后再扫描被驱动表。 



3、Multi-Range Read

当在某个二级索引上进行范围查找时，通常在回表查询的过程中会发生大量的随机读。比如下面的语句

```sql
select * from user where uname like '王%';
```

- uname列上建有索引，可以从uname索引上获取主键值，然后再回表根据主键逐条从聚簇索引上查询行数据。这个时候获取的主键是无序的，会造成大量的随机读。 
- MRR(Multi-Range Read)优化就是为了解决这种场景，MRR处理过程是在根据索引获取到主键后不是立即进行回表查询，而是先将主键值放入到一个read_rnd_buffer中，然后对其进行排序，最后再有序的去聚簇索引检索行数据。如果对应主键索引值足够密集，这样可以大大减少随机读。 



4、【强制】在varchar字段上建立索引时，必须指定索引长度，没必要对全字段建立索引，根据实际文本区分度决定索引长度即可。

> 说明：索引的长度与区分度是一对矛盾体，一般对字符串类型数据，长度为 20的索引，区分度会高达90%以上，可以使用 `count(distinct left(列名, 索引长度))/count(*)`的区分度来确定。



5、【推荐】如果有order by的场景，请注意利用索引的有序性。order by 最后的字段是组合索引的一部分，并且放在索引组合顺序的最后，避免出现 file_sort的情况，影响查询性能。

正例：`where a=? and b=? order by c;` 索引：a_b_c

反例：索引中有范围查找，那么索引有序性无法利用，如：`WHERE a>10 ORDER BY b; `索引a_b无法排序。因为在a相同的情况下b是有序的，而整体是无序的



### 3、SQL语句

1、【强制】使用ISNULL()来判断是否为NULL值。

> 说明：NULL与任何值的直接比较都为 NULL。
>
> 1） NULL<>NULL的返回结果是 NULL，而不是false。
>
> 2） NULL=NULL的返回结果是 NULL，而不是 true。
>
> 3） NULL<>1的返回结果是 NULL，而不是 true。



2、【强制】不得使用外键与级联，一切外键概念必须在应用层解决。

> 说明： 以学生和成绩的关系为例， 学生表中的student_id是主键，那么成绩表中的student_id则为外键。如果更新学生表中的 student_id，同时触发成绩表中的 student_id更新，即为级联更新。外键与级联更新适用于单机低并发，不适合分布式、高并发集群；级联更新是强阻塞，存在数据库更新风暴的风险；外键影响数据库的插入速度。



### 4、ORM映射

1、【强制】在表查询中，一律不要使用 * 作为查询的字段列表，需要哪些字段必须明确写明。

- 增加查询分析器解析成本。 
- 增减字段容易与 resultMap配置不一致。 
- 无用字段增加网络消耗，尤其是 text类型的字段。 



2、【强制】不允许直接拿 HashMap与Hashtable作为查询结果集的输出。

> 说明：`resultClass=”Hashtable”`，会置入字段名和属性值，但是值的类型不可控。



## 六、服务器

1、【推荐】高并发服务器建议调小 TCP协议的 time_wait超时时间。

> 说明：Linux系统默认 60 秒后，才会关闭处于 time_wait状态的连接，在高并发访问下，服务器端会因为处于time_wait的连接数太多，可能无法建立新的连接，所以需要在服务器上调小此等待值。
>
> 正例：在linux服务器上请通过变更`/etc/sysctl.conf`文件去修改该缺省值（秒）：`net.ipv4.tcp_fin_timeout = 30`



2、【推荐】在线上生产环境，JVM的`Xms`（堆初识大小）和`Xmx`（堆最大大小）设置一样大小的内存容量，避免在GC 后调整堆大小带来的压力。



## 七、设计规约

1、【推荐】类在设计与实现时要符合单一原则。

> 说明：单一原则最易理解却是最难实现的一条规则，随着系统演进，很多时候，忘记了类设计的初衷。 



2、【推荐】谨慎使用继承的方式来进行扩展，优先使用聚合/组合的方式来实现。

> 说明：不得已使用继承的话，必须符合里氏代换原则，此原则说父类能够出现的地方子类一定能够出现，比如，“把钱交出来”，钱的子类美元、欧元、人民币等都可以出现。
> 里氏替换：
>
> - 子类必须实现父类的抽象方法，但不得重写父类的非抽象方法 
> - 子类可以有自己的方法 
> - 当子类覆盖或实现父类的方法时，方法的输入参数可以比父类方法的输入参数更宽松 父类方法的输入形参类型为 T，子类方法输入形参类型为 S，那么根据里氏替换原则就要求 S 必须大于等于 T。也就是说，要么 S 和 T 是同一类型，要么 S 是 T 的父类。
> - 当子类覆盖或实现父类的方法时，方法的返回结果可以比父类方法的返回结果范围更严格 父类方法的返回类型为 T，子类的返回类型为 S，那么根据里氏替换原则就要求 S 必须小于等于 T。也就是说，要么S和T是同一类型，要么 T 是 S 的父类。 



3、【推荐】系统设计时，注意对扩展开放，对修改闭合。

> 说明：极端情况下，交付的代码都是不可修改的，同一业务域内的需求变化，通过模块或类的扩展来实现。

