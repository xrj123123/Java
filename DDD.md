# 一、DDD理论

## 1、DDD介绍

### 1.1 名词介绍

**领域模型**

领域模型是对特定业务领域知识的精确表述，它包括业务中的实体（Entities）、值对象（Value Objects）、服务（Services）、聚合（Aggregates）、聚合根（Aggregate Roots）等概念。领域模型是DDD的核心，它反映了业务专家的语言和决策。



**限界上下文（Bounded Context）**

限界上下文是明确界定的系统边界，在这个边界内部有一套统一的模型和语言。不同的限界上下文之间可能有不同的模型，它们通过上下文映射（Context Mapping）来进行交互和集成。



**聚合（Aggregate）**

聚合是一组相关对象的集合，它们被视为数据修改的单元。每个聚合都有一个聚合根，它是外部对象与聚合内部对象交互的唯一入口。



**领域服务（Domain Services）**

当某些行为不自然属于任何实体或值对象时，这些行为可以被定义为领域服务。领域服务通常表示领域中的一些操作或业务逻辑。



**应用服务（Application Services）**

应用服务是软件的一部分，它们协调领域对象来执行任务。它们负责应用程序的工作流程，但不包含业务规则或知识。



**基础设施（Infrastructure）**

基础设施包括为领域模型提供持久化机制（如数据库）、消息传递、应用程序的配置等技术组件



**领域事件（Domain Events）**

领域事件是领域中发生的有意义的业务事件，它们可以触发其他子系统的反应或流程。



**战略设计**

主要应对复杂的业务需求，通过抽象、分治的过程，合理的拆分为独立的多个微服务，分而治之。与之评价拆分的是否合理，则是在需求开发上线时候，是否每次都大量操作多个微服务开发和上线。这样的战略设计是一种失败的微服务单体设计。所以少数几个中等规模的单体应用，周围环绕着一个服务生态系统，这更有意义



**战术设计**：

在这个范畴下，主要以讨论如何基于面向对象思维，运用领域模型来表达业务概念。通常在不做领域模型设计的架构，也就是通常映射到 MVC 三层架构下，Service + 数据模型的开发模式，会让 Service 扁平的、大量的，平铺出非常复杂的业务逻辑代码。再加上行为对象与功能逻辑的分离，贫血模型的开发方式，让行为对象不断交叉使用，让系统不断增加复杂度，难以维护。所以这一阶段要设计每一个可以表达领域概念的模型，并运用实体、聚合、领域服务来承载







### 1.2 DDD概念

#### 1. 充血模型

将对象的属性信息与行为逻辑聚合到一个类中，常用的手段如在对象内提供属于当前对象的信息校验、拼装缓存Key、不含服务接口调用的逻辑处理等

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408062324307.png" style="zoom:50%;" />

- 这样的方式可以在使用一个对象时，就顺便拿到这个对象的提供的一系列方法信息，所有使用对象的逻辑方法，都不需要自己再次处理同类逻辑。
- 但不要只是把充血模型，仅限于一个类的设计和一个类内的方法设计。充血还可以是整个包结构，一个包下包括了用于实现此包 Service 服务所需的各部件（模型、仓储、工厂），也可以被看做充血模型。
- 同时我们还会在一个同类的类下，提供对应的内部类，如用户实名，包括了，通信类、实名卡、银行卡、四要素等。它们都被写进到一个用户类下的内部子类，这样在代码编写中也会清晰的看到子类的所属信息，更容易理解代码逻辑，也便于维护迭代。





#### 2. 领域模型

指特定业务领域内，业务规则、策略以及业务流程的抽象和封装。在设计手段上，通过风暴模型拆分领域模块，形成界限上下文。最大的区别在于把原有的众多 Service + 数据模型的方式，**拆分为独立的有边界的领域模块**。每个领域内创建自身所属的领域对象（实体、聚合、值对象）、仓储服务(DAO 操作)、工厂、端口适配器Port（调用外部接口的手段）等。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408062327931.png" style="zoom:50%;" />

- 在原本的 Service + 贫血的数据模型开发指导下，Service 串联调用每一个功能模块。这些基础设施（对象、方法、接口）是被相互掉调用的。这也是因为贫血模型并没有面向对象的设计，所有的需求开发只有详细设计。

- 换到充血模型下，现在我们以一个领域功能为聚合，拆分一个领域内所需的 Service 为领域服务，VO、Req、Res 重新设计为领域对象，DAO、Redis 等持久化操作为仓储等。例如，一套账户服务中的授信认证、开户、提额等，每一个都是一个独立的领域，在每个独立的领域内，创建自身领域所需的各项信息。

- 领域模型还有一个特点，它自身只关注业务功能实现，不与外部任何接口和服务直连。如，不会直接调用 DAO 操作库，也不会调用缓存操作 Redis，更不会直接引入 RPC 连接其他微服务。而是通过仓储和端口适配器，定义调用外部数据的含有出入参对象的接口标准，让基础设施层做具体的调用实现。通过这样的方式让领域只关心业务实现，同时做好防腐。





DDD 领域驱动设计的中心，主要在于领域模型的设计，以领域所需驱动功能实现和数据建模。一个领域服务下面会有多个领域模型，每个领域模型都是一个充血结构。**一个领域模型 = 一个充血结构**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092306820.png)







#### 3. 实体、聚合、值对象

> 原本在贫血模型下的开发，常常是不会特别在意一个方法的出入参对象的，也经常是很多个服务共用一个VO对象作为入参，只要这个对象能把我需要的属性信息带进来就可以了。
>
> 但在 DDD 的领域模型设计下，领域对象的设计是非常面向对象的。而且在整个风暴事件的四色建模过程也是在以领域对象为驱动进行的。



实体、聚合、值对象，三者位于每个领域下的领域对象内，服务于领域内的领域服务。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408062341726.png" style="zoom:60%;" />



**实体**

> 依托于持久化层数据以领域服务功能目标为指导设计的领域对象。持久化PO对象是原子类对象，不具有业务语义，而实体对象是具有业务语义且有**唯一标识**的对象，跟随于领域服务方法的全生命周期对象。

- 概念：实体 = 唯一标识 + 状态属性 + 行为动作（功能），是DDD中的一个基本构建块，它代表了具有唯一标识的领域对象。实体不仅仅包含数据（状态属性），还包含了相关的行为（功能），并且它的标识在整个生命周期中保持不变。

- 特征：
  - 唯一标识：实体具有一个可以区分其他实体的标识符。这个标识符可以是一个ID、一个复合键或者是一个自然键，关键是它能够唯一地标识实体实例。
  - 领域标识：实体的标识通常来源于业务领域，例如用户ID、订单ID等。这些标识符在业务上有特定的含义，并且在系统中是唯一的。
  - 委派标识：在某些情况下，实体的标识可能是由ORM（对象关系映射）框架自动生成的，如数据库中的自增主键。这种标识符虽然可以唯一标识实体，但它并不直接来源于业务领域。

- 用途

  - 表达业务概念：实体用于在软件中表达具体的业务概念，如用户、订单、交易等。通过实体的属性和行为，可以描述这些业务对象的特征和能力。

  - 封装业务逻辑：实体不仅仅承载数据，还封装了业务规则和逻辑。这些逻辑包括验证数据的有效性、执行业务规则、计算属性值等。这样做的目的是保证业务逻辑的集中和一致性。

  - 保持数据一致性：实体负责维护自身的状态和数据一致性。它确保自己的属性和关联关系在任何时候都是正确和完整的，从而避免数据的不一致性。

- 实现手段：

  - 定义实体类：在代码中定义一个类，该类包含实体的属性、构造函数、方法等。

  - 实现唯一标识**：为实体类提供一个唯一标识的属性，如ID，并确保在实体的生命周期中这个标识保持不变。**
  - 封装行为：在实体类中实现业务逻辑的方法，这些方法可以操作实体的状态，并执行相关的业务规则。
  - 使用ORM框架**：利用ORM框架将实体映射到数据库表中，这样可以简化数据持久化的操作。**
  - 实现领域服务：对于跨实体或跨聚合的操作，可以实现领域服务来处理这些操作，而不是在实体中直接实现。
  - 使用领域事件：当实体的状态发生变化时，可以发布领域事件，这样可以通知其他部分的系统进行相应的处理。





**值对象**

在领域服务方法的生命周期过程内是不可变对象，也没有唯一标识。它通常是配合实体对象使用。如为实体对象提供对象属性值的描述。比如：一个公司雇员的级别值对象，一个下单的商品收货的四级地址信息对象。所以在开发值对象的时候，通常不会提供 setter 方法，而是提供构造函数或者 Builder 方法来实例化对象。这个对象通常不会独立作为方法的入参对象，但可以独立作为出参对象使用。

- 概念：值对象是由一组属性组成的，它们共同描述了一个领域概念。与实体（Entity）不同，值对象不需要有一个唯一的标识符来区分它们。值对象通常是不可变的，这意味着一旦创建，它们的状态就不应该改变。

- 特征：
  - 不可变性（Immutability）：值对象一旦被创建，它的状态就不应该发生变化。这有助于保证领域模型的一致性和线程安全性。
  - 等价性（Equality）：值对象的等价性不是基于身份或引用，而是基于对象的属性值。如果两个值对象的所有属性值都相等，那么这两个对象就被认为是等价的。
  - 替换性（Replaceability）：由于值对象是不可变的，任何需要改变值对象的操作都会导致创建一个新的值对象实例，而不是修改现有的实例。
  - 侧重于描述事物的状态：值对象通常用来描述事物的状态，而不是事物的唯一身份。
  - 可复用性（Reusability）：值对象可以在不同的领域实体或其他值对象中重复使用。

- 用途：
  - 金额和货币（如价格、工资、费用等）
  - 度量和数据（如重量、长度、体积等）
  - 范围或区间（如日期范围、温度区间等）
  - 复杂的数学模型（如坐标、向量等）
  - 任何其他需要封装的属性集合
- 实现手段：
  - 定义不可变类：确保类的所有属性都是私有的，并且只能通过构造函数来设置。
  - 重写equals和hashCode方法：这样可以确保值对象的等价性是基于它们的属性值，而不是对象的引用。
  - 提供只读访问器：只提供获取属性值的方法，不提供修改属性值的方法。
  - 使用工厂方法或构造函数创建实例：这有助于确保值对象的有效性和一致性。
  - 考虑序列化支持：如果值对象需要在网络上传输或存储到数据库中，需要提供序列化和反序列化的支持。





**聚合**

> 当你对数据库的操作需要使用到多个实体时，可以创建聚合对象。一个聚合对象，代表着一个数据库事务，具有事务一致性。聚合中的实体可以由聚合提供创建操作，实体也被称为聚合根对象。一个订单的聚合，会涵盖下单用户实体、订单实体、订单明细实体和订单收货四级地址值对象。而那个作为入参的购物车实体对象，已经被转换为实体对象了。
>
> **—— 聚合内事务一致性，聚合外最终一致性。**

- 概念：聚合是领域模型中的一个关键概念，它是一组具有内聚性的相关对象的集合，这些对象一起工作以执行某些业务规则或操作。聚合定义了一组对象的边界，这些对象可以被视为一个单一的单元进行处理。

- 特征：
  - 一致性边界：聚合确保其内部对象的状态变化是一致的。当对聚合内的对象进行操作时，这些操作必须保持聚合内所有对象的一致性。
  - 根实体：每个聚合都有一个根实体（Aggregate Root），它是聚合的入口点。根实体拥有一个全局唯一的标识符，其他对象通过根实体与聚合交互。
  - 事务边界：聚合也定义了事务的边界。在聚合内部，所有的变更操作应该是原子的，即它们要么全部成功，要么全部失败，以此来保证数据的一致性。

- 用途：

  - 封装业务逻辑：聚合通过将相关的对象和操作封装在一起，提供了一个清晰的业务逻辑模型，有助于业务规则的实施和维护。

  - 保证一致性：聚合确保内部状态的一致性，通过定义清晰的边界和规则，聚合可以在内部强制执行业务规则，从而保证数据的一致性。

  - 简化复杂性：聚合通过组织相关的对象，简化了领域模型的复杂性。这有助于开发者更好地理解和扩展系统。

- 实现手段：

  - 定义聚合根：选择合适的聚合根是实现聚合的第一步。聚合根应该是能够代表整个聚合的实体，并且拥有唯一标识。

  - 限制访问路径：只能通过聚合根来修改聚合内的对象，不允许直接修改聚合内部对象的状态，以此来维护边界和一致性。

  - 设计事务策略：在聚合内部实现事务一致性，确保操作要么全部完成，要么全部回滚。对于聚合之间的交互，可以采用领域事件或其他机制来实现最终一致性。

  - 封装业务规则：在聚合内部实现业务规则和逻辑，确保所有的业务操作都遵循这些规则。

  - 持久化：聚合根通常与数据持久化层交互，以保存聚合的状态。这通常涉及到对象-关系映射（ORM）或其他数据映射技术。





#### 4. **仓储和适配器**

在 DDD 的设计方法中，领域层做到了只关心领域服务实现。最能体现这样设计的就是仓库和适配器的设计。通常在 Service + 数据模型的设计中，会在 Service 中引入 Redis、RPC、配置中心等各类其他外部服务。但在 DDD 中，通过仓储和适配器以及基础设施层的定义，解耦了这部分内容。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408070006241.png" style="zoom:60%;" />

- 特征
  - 封装持久化操作：Repository负责封装所有与数据源交互的操作，如创建、读取、更新和删除（CRUD）操作。这样，领域层的代码就可以避免直接处理数据库或其他存储机制的复杂性。
  - 领域对象的集合管理：Repository通常被视为领域对象的集合，提供了查询和过滤这些对象的方法，使得领域对象的获取和管理更加方便。
  - 抽象接口：Repository定义了一个与持久化机制无关的接口，这使得领域层的代码可以在不同的持久化机制之间切换，而不需要修改业务逻辑。

- 用途
  - 数据访问抽象：Repository为领域层提供了一个清晰的数据访问接口，使得领域对象可以专注于业务逻辑的实现，而不是数据访问的细节。
  - 领域对象的查询和管理：Repository使得对领域对象的查询和管理变得更加方便和灵活，支持复杂的查询逻辑。
  - 领域逻辑与数据存储分离：通过Repository模式，领域逻辑与数据存储逻辑分离，提高了领域模型的纯粹性和可测试性。
  - 优化数据访问：Repository实现可以包含数据访问的优化策略，如缓存、批处理操作等，以提高应用程序的性能。

- 实现手段
  - 定义Repository接口：在领域层定义一个或多个Repository接口，这些接口声明了所需的数据访问方法。
  - 实现Repository接口：在基础设施层或数据访问层实现这些接口，具体实现可能是使用ORM（对象关系映射）框架，如MyBatis、Hibernate等，或者直接使用数据库访问API，如JDBC等。
  - 依赖注入：在应用程序中使用依赖注入（DI）来将具体的Repository实现注入到需要它们的领域服务或应用服务中。这样做可以进一步解耦领域层和数据访问层，同时也便于单元测试。

> 1. 仓储解耦的手段使用了依赖倒置的设计，所有领域需要的外部服务，不在直接引入外部的服务，而是通过定义接口的方式，让基础设施层实现领域层接口（仓储/适配器）的方式来处理。
>
> 2. 基础设施层负责对接数据库、缓存、配置中心、RPC接口、HTTP接口、MQ推送等各项资源，并承接领域服务的接口调用各项服务为领域层提供数据能力。
> 3. 同时这也体现出，领域层的实现是具有业务语义的，而基础设施层则没有业务语义，都是原子的方法。通过原子方法的组合为领域业务语义提供支撑。





#### 5. 领域编排

- 在 DDD 中，每一个领域都是界限上下文拆分的独立结果，而实现业务流程的功能则需要串联各个领域模块提供一整条链路的完整服务。所以也常说领域内事务一致性，领域外最终一致性。

- 这些领域模块因为是独立的，所以也可以被复用。在不同的场景功能诉求下，可以选择不同的领域模块进行组装，这个过程就像搭积木一样。

- 这里有一个取舍，如果项目相对来说并不大，也没有太多的编排处理。那么可以直接让触发器层对接领域层，减少编排层后，编码会更加便捷。





#### 6. 触发器

- 所有的模型都定义完成后，领域业务被串联了。那么接下来则是使用，而使用的方式可以包括；接口（http/rpc）、消息监听、定时任务等方式，这些方式统一被定义为触发动作。

- 由触发发起对编排功能的调用处理。如定时任务做信贷的计息、开户成功消息通知返利优惠券、提供接口让外部调用授信逻辑等。这些都是触发动作。











## 2、聚合、实体、值对象

### 2.1 聚合

聚合是领域模型中的一个关键概念，它是一组具有内聚性的相关对象的集合，这些对象一起工作以执行某些业务规则或操作。聚合定义了一组对象的边界，这些对象可以被视为一个单一的单元进行处理。**聚合内实现事务一致性、聚合外实现最终一致性。**



**聚合示例**

> 创建一个订单系统，其中包含订单聚合（Order Aggregate）和订单项（OrderItem）作为内部实体。订单聚合根（Order）将封装所有业务规则，并通过聚合根进行所有的交互。
>



**定义实体和值对象的基类**

```java
//实体基类
public abstract class BaseEntity{
	protected Long id;
	public Long getId(){
		return id;
	}
}

//值对象基类
public abstract class ValueObject{
	//值对象通常是不可变的，所以没有setter方法
}
```



**定义订单项（OrderItem）作为实体**

```java
public class OrderItem extends BaseEntity {
    private String productName;
    private int quantity;
    private double price;

    public Order Item(StringproductName, intquantity, doubleprice) {
        this.productName = productName;
        this.quantity = quantity;
        this.price = price;
    }

    public double getTotalPrice() {
        return quantity * price;
    }

    //省略getter和setter方法
}
```





**定义订单聚合根（Order）**

```java
import java.util.ArrayList;
import java.util.List;

public class OrderAggregate extends BaseEntity {
    private List<OrderItem> orderItems;
    private String customerName;
    private boolean isPaid;

    public Order Aggregate(String customerName) {
        this.customerName = customerName;
        this.orderItems = new ArrayList<>();
        this.isPaid = false;
    }

    public void addItem(OrderItem item) {
        //业务规则：订单未支付时才能添加订单项
        if (!isPaid) {
            orderItems.add(item);
        } else {
            throw new IllegalStateException("Can not add items to apaid order.");
        }
    }

    public double getTotalAmount() {
        return orderItems.stream().mapToDouble(OrderItem::getTotalPrice).sum();
    }

    public void markAsPaid() {
        //业务规则：订单总金额必须大于0才能标记为已支付
        if (getTotalAmount() > 0) {
            isPaid = true;
        } else {
            throw new IllegalStateException("Order total must begreater than 0 to bepaid.");
        }
    }

    //省略getter和setter方法
}
```



**创建订单**

> 在这个例子中，订单聚合根（Order）确保了订单项（OrderItem）的一致性，并且只有通过聚合根才能修改订单的状态。这个例子还展示了如何在聚合内部实现事务一致性，例如，订单项只能在订单未支付时添加，订单必须有一个大于0的总金额才能标记为已支付。

```java
public class Order Demo{
    public static void main(String[] args){
    //创建订单聚合
    OrderAggregate orderAggregate= new OrderAggregate("XiaoFuGe");

    //添加订单项
    orderAggregate.addItem(new OrderItem("手机",1,1000.00));
    orderAggregate.addItem(new OrderItem("数据线",2,25.00));

    //获取订单总金额
    System.out.println("Total amount:" + orderAggregate.getTotalAmount());

    //标记订单为已支付
    order Aggregate.markAsPaid();
    }
}
```







### 2.2 实体

实体 = 唯一标识 + 状态属性 + 行为动作（功能），是DDD中的一个基本构建块，它代表了具有唯一标识的领域对象。实体不仅仅包含数据（状态属性），还包含了相关的行为（功能），并且它的标识在整个生命周期中保持不变。





**实体示例**

> 创建一个User实体，它代表了一个用户，具有唯一的用户ID、姓名和电子邮件地址，并且可以执行一些基本的行为

- `User`类代表了用户实体，它有一个唯一的标识符id，这个标识符在实体的整个生命周期中保持不变。
- `name`和`email`是用户的状态属性
- `updateName`和`updateEmail`是封装了业务逻辑的行为方法。
- `equals`和`hashCode`方法基于唯一标识符实现，以确保实体的正确比较和散列。
- `UserDemo`类演示了如何创建和使用User实体。

```java
import java.util.Objects;
import java.util.UUID;
    //UserEntity实体类
    public class UserEntity {
		//实体的唯一标识符
        private final UUIDid;
        //用户的状态属性
        private String name;
        private String email;

        //构造函数，用于创建实体实例
        public UserEntity(UUID id, String name, String email) {
            this.id = id;
            this.name = name;
            this.email = email;
            //可以在这里添加验证逻辑，确保创建的实体是有效的
        }

        //实体的行为方法，例如更新用户的姓名
        public void updateName(String newName) {
            //可以在这里添加业务规则，例如验证姓名的格式
            this.name = newName;
        }

        //实体的行为方法，例如更新用户的电子邮件地址
        public void updateEmail(String newEmail) {
            //可以在这里添加业务规则，例如验证电子邮件地址的格式
            this.email = newEmail;
        }

        //Getter方法
        public UUID getId() {
            returnid;
        }

        public String getName() {
            return name;
        }

        public String getEmail() {
            return email;
        }

        //实体的equals和hashCode方法，基于唯一标识符实现
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            UserEntity user = (UserEntity) o;
            return id.equals(user.id);
        }

        @Override
        public int hashCode() {
            return Objects.hash(id);
        }

        //toString方法，用于打印实体信息
        @Override
        public String toString() {
            return "UserEntity{" +
                "id=" + id +
                ",name='" + name + '\'' +
                ",email='" + email + '\'' +
                '}';
        }
    }

//使用实体的示例
public class UserEntityDemo {
    public static void main(String[] args) {
        //创建一个新的用户实体
        UserEntity user = new UserEntity(UUID.randomUUID(), "XiaoFuGe", "xiaofuge@qq.com");

        //打印用户信息
        System.out.println(user);

        //更新用户的姓名
        user.updateName("XiaoFuGe");

        //打印更新后的用户信息
        System.out.println(user);

        //更新用户的电子邮件地址
        user.updateEmail("xiaofuge@qq.com");

        //打印更新后的用户信息
        System.out.println(user);
    }
}
```







### 2.3 值对象

值对象（Value Object）用于封装和表示领域中的概念，它们描述了领域中的某些属性或度量，但不具有唯一标识。值对象通常是不可变的，这意味着一旦创建，它们的状态就不应该改变。值对象 = 值 + 对象，用于描述对象属性的值，表示具体固定不变的属性值信息。



**枚举实现值对象**

> 以订单状态为例，可以定义一个值对象来表示不同的状态

- `OrderStatusVO`是一个枚举类型的值对象，它封装了订单状态的代码和描述。它是不可变的，并且提供了基于属性值的等价性。通过定义一个枚举，我们可以确保订单状态的值是受限的，并且每个状态都有一个明确的含义。
- 在数据库中，订单状态可能会以整数形式存储（例如，0表示下单，1表示支付等）。在应用程序中，我们可以使用`OrderStatusVO`枚举来确保我们在代码中使用的是类型安全的值，而不是裸露的整数。这样可以减少错误，并提高代码的可读性和可维护性。
- 当需要将订单状态存储到数据库中时，我们可以存储枚举的code值。当从数据库中读取订单状态时，我们可以使用`fromCode`方法来将整数值转换回`OrderStatusVO`枚举，这样我们就可以在代码中使用丰富的枚举类型而不是简单的整数。

```java
public enum OrderStatusVO {
    PLACED(0, "下单"),
    PAID(1, "支付"),
    COMPLETED(2, "完成"),
    CANCELLED(3, "退单");

    private final int code;
    private final String description;

    OrderStatusVO(intcode, String description) {
        this.code = code;
        this.description = description;
    }

    public int getCode() {
        return code;
    }

    public String getDescription() {
        return description;
    }

    //根据code获取对应的OrderStatus
    public static OrderStatusVOfromCode(int code) {
        for (OrderStatusVO status : OrderStatusVO.values()) {
            if (status.getCode() == code) {
                return status;
            }
        }
        throw newIllegalArgumentException("Invalid code for OrderStatus:" + code);
    }
}
```



**不可变类实现值对象**

> `AddressVO`是一个不可变的值对象，它封装了一个地址的所有部分。它提供了只读访问器，并且重写了`equals`和`hashCode`方法以确保基于属性值的等价性。这样的设计有助于确保地址的一致性，并且可以在不同的实体之间重复使用，例如用户和商店都可能有地址。

```java
public final classAddressVO {
    private final Stringstreet ;
    private final Stringcity ;
    private final StringzipCode ;
    private final Stringcountry ;

    publicAddress(Stringstreet, Stringcity, StringzipCode, Stringcountry) {
        //这里可以添加验证逻辑以确保地址的有效性
        this.street = street;
        this.city = city;
        this.zipCode = zipCode;
        this.country = country;
    }

    //只读访问器
    public String getStreet () {
        returnstreet;
    }

    public String getCity () {
        returncity;
    }

    public String getZipCode () {
        return zipCode;
    }

    public String getCountry () {
        return country;
    }

    //重写equals和hashCode方法
    @Override
    public boolean equals (Objecto) {
        if (this == o) returntrue;
        if (o == null || getClass() != o.getClass()) returnfalse;
        Address address = (Address) o;
        return street.equals(address.street) &&
            city.equals(address.city) &&
            zipCode.equals(address.zipCode) &&
            country.equals(address.country);
    }

    @Override
    public int hashCode () {
        return Objects.hash(street, city, zipCode, country);
    }
}
```







## 3、仓储

> Repository（仓储）模式是一种设计模式，它用于将数据访问逻辑封装起来，使得领域层可以通过一个简单、一致的接口来访问聚合根或实体对象。这个模式的关键在于提供了一个抽象的接口，领域层通过这个接口与数据存储层进行交互，而不需要知道背后具体的实现细节。
>
> `domain`定义好仓储层接口，`infrastructure`层导入`domain`的jar包，然后实现接口，并将实现类放入`ioc`容器，这样`domain`中可以直接通过`@Autowire`注入对应仓储的实现



**仓储示例**

> 创建一个用户管理系统，其中包含用户实体和用户仓储接口，以及一个基于内存的仓储实现。



**领域层定义用户实体**

```java
public class User {
    private Long id;
    private String username;
    private String email;

    // 构造函数、getter和setter省略
}
```



**领域层定义用户仓储的接口**

```java
public interface UserRepository {
    User findById(Long id);
    List<User> findAll();
    void save(User user);
    void delete(User user);
}
```



**仓储层实现接口**

```java
@Repository
public class InMemoryUserRepository implements UserRepository {
    private Map<Long, User> database = new HashMap<>();
    private AtomicLong idGenerator = new AtomicLong();

    @Override
    public User findById(Long id) {
        return database.get(id);
    }

    @Override
    public List<User> findAll() {
        return new ArrayList<>(database.values());
    }

    @Override
    public void save(User user) {
        if (user.getId() == null) {
            user.setId(idGenerator.incrementAndGet());
        }
        database.put(user.getId(), user);
    }

    @Override
    public void delete(User user) {
        database.remove(user.getId());
    }
}
```



**领域层使用**

```java
public class UserService {
    @Autowired
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUserById(Long id) {
        return userRepository.findById(id);
    }

    public void createUser(String username, String email) {
        User newUser = new User();
        newUser.setUsername(username);
        newUser.setEmail(email);
        userRepository.save(newUser);
    }

    // 其他业务逻辑方法...
}
```







## 4、适配器和端口

> 1、在领域驱动设计（DDD）的上下文中，适配器（Adapter）模式扮演着至关重要的角色。适配器模式允许将不兼容的接口转换为另一个预期的接口，从而使原本由于接口不兼容而不能一起工作的类可以协同工作。
>
> 2、在DDD中，适配器【实现类】通常与端口（Port）【接口】概念结合使用，形成"端口和适配器"（Ports and Adapters）架构，也称为"六边形架构"（Hexagonal Architecture）。这种架构风格旨在将应用程序的核心逻辑与外部世界的交互解耦。
>
> 3、Port 在这种架构中代表了应用程序的一个入口或出口点。它定义了一个与外部世界交互的接口，但不关心具体的实现细节。端口可以是驱动端口（Driving Ports，通常是输入端口）或被驱动端口（Driven Ports，通常是输出端口）。



**特征**

- 抽象性：端口提供了服务行为的抽象描述，明确了服务的功能和外部依赖。
- 独立性：端口独立于具体实现，允许服务实现的灵活替换或扩展。

- 灵活性：可以为同一端口提供不同的适配器实现，以适应不同的运行环境或需求。





### 4.1 **实现**

- 定义端口：在领域层定义清晰的接口，这些接口代表了应用程序与外部世界的交互点。

- 创建适配器：在基础层或应用层实现适配器，这些适配器负责将端口的抽象操作转换为具体的外部调用。

- 依赖倒置：应用程序的核心逻辑依赖于端口接口，而不是适配器的具体实现。这样，适配器可以随时被替换，而不影响核心逻辑。

- 配置和组装：在应用程序启动时，根据需要将适配器与相应的端口连接起来。



**示例**

> 创建一个支付系统，其中包含一个支付端口和一个适配器，该适配器负责调用外部支付服务的接口。



**支付端口**

> 定义一个支付端口（Port），它是一个接口，描述了支付服务应该提供的操作

```java
public interface PaymentPort {
    boolean processPayment(double amount);
}
```



**适配器**

> 适配器，它实现了支付端口，并负责调用外部支付服务的接口

```java
public class ExternalPaymentService {
    public boolean makePayment(double amount) {
        // 这里是外部支付服务的具体调用逻辑
        System.out.println("Calling external payment service for amount: " + amount);
        // 假设支付总是成功
        return true;
    }
}

public class PaymentAdapter implements PaymentPort {
    private ExternalPaymentService externalPaymentService;

    public PaymentAdapter(ExternalPaymentService externalPaymentService) {
        this.externalPaymentService = externalPaymentService;
    }

    @Override
    public boolean processPayment(double amount) {
        // 调用外部支付服务的接口
        return externalPaymentService.makePayment(amount);
    }
}
```



**使用**

> `PaymentAdapter` 负责调用外部的支付接口 `ExternalPaymentService.makePayment`。`PaymentService` 使用 `PaymentPort `接口与外部世界交互，这样就实现了领域逻辑与外部服务之间的解耦。如果需要更换支付服务提供商，我们只需要实现一个新的 `PaymentAdapter`，而不需要修改 `PaymentService `的代码。

```java
public class PaymentService {
    private PaymentPort paymentPort;

    public PaymentService(PaymentPort paymentPort) {
        this.paymentPort = paymentPort;
    }

    public void processUserPayment(double amount) {
        if (paymentPort.processPayment(amount)) {
            System.out.println("Payment processed successfully.");
        } else {
            System.out.println("Payment failed.");
        }
    }
}
```







## 5、事件，触发异步消息

> 领域事件（Domain Events）是一种模型，用于表示领域中发生的有意义的事件。这些事件对业务来说是重要的，并且通常表示领域状态的变化。适配器（Adapter）在这个上下文中扮演着将领域事件与系统其他部分或外部系统连接起来的角色。



**特性**

- 意义明确：领域事件通常具有明确的业务含义，例如“用户已下单”、“商品已支付”等。

- 不可变性：一旦领域事件被创建，它的状态就不应该被改变。这有助于确保事件的一致性和可靠性。

- 时间相关性：领域事件通常包含事件发生的时间戳，这有助于追踪事件的顺序和时间线。

- 关联性：领域事件可能与特定的领域实体或聚合根相关联，这有助于完成事件的上下文。

- 可观察性：领域事件可以被其他部分的系统监听和响应，有助于实现系统间的解耦。



**用途**

- 解耦：领域事件可以帮助系统内部或系统间的不同部分解耦，因为它们提供了一种基于事件的通信机制。

- 业务逻辑触发：领域事件可以触发其他业务逻辑的执行，例如推送消息（优惠券到账）、更新其他聚合或生成数据流式报告等。

- 事件溯源：领域事件可以用于实现事件溯源（Event Sourcing），这是一种存储系统状态变化的方法，通过重放事件来恢复系统状态。

- 集成：领域事件可以用于系统与外部系统的集成，通过发布事件来通知外部系统领域中发生的变化。





### **5.1 实现**

**领域层**

- 定义事件接口：创建一个或多个接口来定义领域事件的结构和行为。

- 创建领域事件类：基于定义的接口，实现具体的领域事件类，包含必要的属性和方法。

- 触发领域事件：在领域逻辑中的适当位置，实例化并发布领域事件。



**基础层**

- 实现领域接口：使用消息队列（如RocketMQ或RabbitMQ）来实现领域事件的发布和订阅机制。



**触发器层/接口层**

- 监听领域事件消息：在系统的其他部分或外部系统中，监听领域事件并根据事件来执行相应的业务逻辑或集成逻辑。





### **5.2 示例**

> 以大营销项目为例



#### 5.2.1 领域层

##### 1. 定义事件接口

> 该抽象类是在types层下定义的

```java
@Data
public abstract class BaseEvent<T> {

    public abstract EventMessage<T> buildEventMessage(T data);

    public abstract String topic();

    @Data
    @Builder
    @AllArgsConstructor
    @NoArgsConstructor
    public static class EventMessage<T> {
        private String id;
        private Date timestamp;
        private T data;
    }

}
```





##### 2. 创建领域事件类

> 领域层创建对应的事件类

```java
@Component
public class ActivitySkuStockZeroMessageEvent extends BaseEvent<Long> {

    @Value("${spring.rabbitmq.topic.activity_sku_stock_zero}")
    private String topic;

    @Override
    public EventMessage<Long> buildEventMessage(Long sku) {
        return EventMessage.<Long>builder()
                .id(RandomStringUtils.randomNumeric(11))
                .timestamp(new Date())
                .data(sku)
                .build();
    }

    @Override
    public String topic() {
        return topic;
    }
}
```





##### 3. 领域层触发事件

> 在领域层中，创建事件，并通过MQ发送

```java
@Override
    public boolean subtractionActivitySkuStock(Long sku, String cacheKey, Date endDateTime) {
        long surplus = redisService.decr(cacheKey);
        if (surplus == 0) {
            // 库存消耗没了以后，发送MQ消息，更新数据库库存
            eventPublisher.publish(activitySkuStockZeroMessageEvent.topic(), activitySkuStockZeroMessageEvent.buildEventMessage(sku));
        } else if (surplus < 0) {
            // 库存小于0，恢复为0个
            redisService.setAtomicLong(cacheKey, 0);
            return false;
        }

        // 1.按照cacheKey decr 后的值，如 99、98、97 和 key 组成为库存锁的key进行使用。
        // 2.加锁为了兜底，如果后续有恢复库存，手动处理等【运营是人来操作，会有这种情况发放，系统要做防护】，也不会超卖。因为所有可用库存key，都被加锁了。
        // 3. 设置加锁时间为活动到期 + 延迟1天
        String lockKey = cacheKey + Constants.UNDERLINE + surplus;
        long expireMillis = endDateTime.getTime() - System.currentTimeMillis() + TimeUnit.DAYS.toMillis(1);
        Boolean lock = redisService.setNx(lockKey, expireMillis, TimeUnit.MILLISECONDS);
        if (!lock) {
            log.info("活动sku库存加锁失败 {}", lockKey);
        }
        return lock;
    }
```





#### 5.2.2 基础设施层

##### 1. 基础层发布事件

> 在`infrastructure`层通过MQ发送消息

```java
@Slf4j
@Component
public class EventPublisher {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void publish(String topic, BaseEvent.EventMessage<?> eventMessage) {
        try {
            String messageJson = JSON.toJSONString(eventMessage);
            rabbitTemplate.convertAndSend(topic, messageJson);
            log.info("发送MQ消息 topic:{} message:{}", topic, messageJson);
        } catch (Exception e) {
            log.error("发送MQ消息失败 topic:{} message:{}", topic, JSON.toJSONString(eventMessage), e);
            throw e;
        }
    }

    public void publish(String topic, String eventMessageJSON){
        try {
            rabbitTemplate.convertAndSend(topic, eventMessageJSON);
            log.info("发送MQ消息 topic:{} message:{}", topic, eventMessageJSON);
        } catch (Exception e) {
            log.error("发送MQ消息失败 topic:{} message:{}", topic, eventMessageJSON, e);
            throw e;
        }
    }
}
```





#### 5.2.3 触发器层

##### 1. 触发器层监听事件

> 在`trigger`层执行监听MQ消息的动作

```java
@Slf4j
@Component
public class ActivitySkuStockZeroCustomer {

    @Value("${spring.rabbitmq.topic.activity_sku_stock_zero}")
    private String topic;

    @Resource
    private IRaffleActivitySkuStockService skuStock;

    @RabbitListener(queuesToDeclare = @Queue(value = "${spring.rabbitmq.topic.activity_sku_stock_zero}"))
    public void listener(String message) {
        try {
            log.info("监听活动sku库存消耗为0消息 topic: {} message: {}", topic, message);
            // 转换对象
            BaseEvent.EventMessage<Long> eventMessage = JSON.parseObject(message, new TypeReference<BaseEvent.EventMessage<Long>>() {
            }.getType());
            Long sku = eventMessage.getData();
            // 更新库存
            skuStock.clearActivitySkuStock(sku);
            // 清空队列 「此时就不需要延迟更新数据库记录了」todo 清空时，需要设定sku标识，不能全部清空。
            skuStock.clearQueueValue();
        } catch (Exception e) {
            log.error("监听活动sku库存消耗为0消息，消费失败 topic: {} message: {}", topic, message);
            throw e;
        }
    }
}
```







# 二、DDD架构

**整洁架构**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092309278.png" style="zoom:60%;" />





**六边形架构**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092309106.png" style="zoom:60%;" />







## 1、系统建模

**四色建模（风暴事件）**是整个 DDD 这套软件设计方法中用于工程拆分界限上下文的非常重要的实践手段。通过建模过程快速识别业务领域中的关键事件和核心流程，也是在这个过程中设计出领域对象的，为后面详细设计和代码开发做指导。



> 1、DDD 的建模过程，是以一个用户为起点，通过行为命令，发起行为动作，串联整个业务。而这个用户的起点最初来自于用例图的分析。用例图是用户与系统交互的最简表示形式，展现了用户和与他相关的用例之间的关系。通过用例图，我们可以分析出所有的行为动作。
>
> 2、在 DDD 中用于完成用户的行为命令和动作分析的过程，是一个四色建模的过程，也称作风暴模型。在使用 DDD 的标准对系统建模前，一堆人要先了解 DDD 的操作手段，这样才能让产品、研发、测试、运营等了解业务的伙伴，都能在同一个语言下完成系统建模。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092233655.png" style="zoom:55%;" />

该图是整个四色建模的指导图，通过寻找领域事件，发起事件命令，完成领域事件的过程，完成 DDD 工程建模。

- 蓝色 - 决策命令，是用户发起的行为动作，如：开始签到、开始抽奖、查看额度等。
- 黄色 - 领域事件，过去时态描述。如：签到完成、抽奖完成、奖品发放完成。它所阐述的都是这个领域要完成的终态。
- 粉色 - 外部系统，如你的系统需要调用外部的接口完成流程。
- 红色 - 业务流程，用于串联决策命令到领域事件，所实现的业务流程。一些简单的场景则直接从决策命令到领域事件就可以。
- 绿色 - 只读模型，做一些读取数据的动作，没有写库的操作。
- 棕色 - 领域对象，每个决策命令的发起，都是含有一个对应的领域对象。





## 2、寻找领域事件

### **2.1 梳理领域事件**

- 根据产品 PRD 文档，一起梳理有哪些领域事件。其实大多数领域事件一个人都可以想到，只是有些部分小的场景和将来可能产生的事件不一定覆盖全。所以要通过产品、测试、以及团队的架构师，一起讨论。
- 比如大营销的抽奖会包括如图所列举的事件。在列举这个阶段，可以是每个人准备好黄色便签纸，想到一个就贴到黑板上一个，只是穷举完成。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092237444.png)





### 2.2 串联流程

在确定了领域事件以后，接下来要做的就是通过决策命令串联领域事件，并填充上所需要的领域对象。这块的操作，新手可以分开处理，如先给领域事件添加决策命令、执行用户和领域对象，最后在串联流程。就像 **事件风暴**定义中的示意一样。

- 首先，通过用户的行为动作，也就是决策命令，串联到对应的领域事件上。并对复杂的流程提供出红色的业务流程。
- 之后，为决策命令添加领域对象，每一个领域在整个流程中都起到了至关重要的作用

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092240131.png)







### 2.3 划分领域边界

有了识别出来的领域角色的流程，就可以很容易的划分出领域边界了。先在事件风暴图上圈出领域边界，之后在单独提供领域划分

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202408092243649.png)
