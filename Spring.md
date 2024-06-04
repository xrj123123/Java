# 一、Spring简介

### 1、Spring简介

##### 1.1、Spring是什么

Spring是分层的 Java SE/EE应用 full-stack 轻量级开源框架，以 **IoC**（Inverse Of Control：反转控制）和**AOP**（Aspect Oriented Programming：面向切面编程）为内核。提供了**展现层 SpringMVC** 和**持久层 Spring JDBCTemplate** 以及**业务层事务管理**等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE 企业应用开源框架。



##### 1.2、Spring的优势

**1）方便解耦，简化开发**

通过 Spring 提供的 IoC容器，可以将对象间的依赖关系交由 Spring 进行控制，避免硬编码所造成的过度耦合。

用户也不必再为单例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。

**2）AOP 编程的支持**

通过 Spring的 AOP 功能，方便进行面向切面编程，许多不容易用传统 OOP 实现的功能可以通过 AOP 轻松实现。

**3）声明式事务的支持**

可以将我们从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活的进行事务管理，提高开发效率和质量。

**4）方便程序的测试**

可以用非容器依赖的编程方式进行几乎所有的测试工作，测试不再是昂贵的操作，而是随手可做的事情。

**5）方便集成各种优秀框架**

Spring对各种优秀框架（Struts、Hibernate、Hessian、Quartz等）的支持。

**6）降低 JavaEE API 的使用难度**

Spring对 JavaEE API（如 JDBC、JavaMail、远程调用等）进行了薄薄的封装层，使这些 API 的使用难度大为降低。

**7）Java 源码是经典学习范例**

Spring的源代码设计精妙、结构清晰、匠心独用，处处体现着大师对Java 设计模式灵活运用以及对 Java技术的高深

造诣。它的源代码无意是 Java 技术的最佳实践的范例。





### 2、Spring快速入门

##### 2.1、Spring程序开发步骤



​	**com.xrj.service.UserServiceImpl** 																							**com.xrj.dao.UserDaoImpl**

![image-20211223160411836](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211223160411836.png)



①导入Spring 开发的基本包坐标

```html
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.5.RELEASE</version>
    </dependency>
</dependencies>
```

②编写Dao接口和实现类

```java
package com.xrj.dao;

public interface UserDao {
    public void save();
}
```

```java
package com.xrj.dao.impl;

public class UserDaoImpl implements com.xrj.dao.UserDao {

    public void save() {
        System.out.println("save running.....");
    }
}
```



③创建Spring 核心配置文件

在类路径下（resources）创建applicationContext.xml配置文件

```html
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 			  
       					   http://www.springframework.org/schema/beans/spring-beans.xsd">


</beans>
```

④在Spring 配置文件中配置UserDaoImpl 

```html
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 	
          				   http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl" scope="singleton" init-method="init" destroy-method="destory"></bean>


</beans>
```

⑤使用Spring 的API 获得Bean实例

```java
package com.xrj.demo;

import com.xrj.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserDaoDemo {
    public static void main(String[] args) {
        //spring的客户端代码
        ApplicationContext app=new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) app.getBean("userDao");
        userDao.save();

    }
}

```





### 3、Spring配置文件

##### 3.1 Bean标签基本配置

用于配置对象交由Spring来创建。

默认情况下它调用的是类中的无参构造函数，如果没有无参构造函数则不能创建成功。

基本属性：

- id：Bean实例在Spring容器中的唯一标识

- class：Bean的全限定名称





##### 3.2 Bean标签范围配置



`<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl" scope="singleton" ></bean>`

scope：指对象的作用范围，取值如下：

- singleton 						默认值，单例的
- prototype 				      多例的
- request                           WEB 项目中，Spring 创建一个Bean 的对象，将对象存入到request 域中
- session                            WEB 项目中，Spring 创建一个Bean 的对象，将对象存入到session 域中
- global session                WEB 项目中，应用在Portlet环境，如果没有Portlet环境那么globalSession相当于session



**1）当scope的取值为singleton时：**

​	Bean的实例化个数：1个

​	Bean的实例化时机：当Spring核心文件被加载时，实例化配置的Bean实例

​	Bean的生命周期：

​						对象创建：当应用加载，创建容器时，对象就被创建了

​						对象运行：只要容器在，对象一直活着

​						对象销毁：当应用卸载，销毁容器时，对象就被销毁了



**2）当scope的取值为prototype时**

Bean的实例化个数：多个

Bean的实例化时机：当调用getBean()方法时实例化Bean

​						对象创建：当使用对象时，创建新的对象实例

​						对象运行：只要对象在使用中，就一直活着

​						对象销毁：当对象长时间不用时，被Java的垃圾回收器回收了



##### 3.3 Bean生命周期配置

- init-method：指定类中的初始化方法名称

- destroy-method：指定类中销毁方法名称

`<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl" scope="singleton" init-method="init" destroy-method="destory"></bean>`



```java
package com.xrj.dao.impl;

import com.xrj.domain.User;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class UserDaoImpl implements com.xrj.dao.UserDao {

    public UserDaoImpl() {
        System.out.println("UserDaoImpl创建...");
    }
    public void init(){
        System.out.println("初始化方法 ");
    }

    public void destory(){

        System.out.println("销毁方法 ");
    }

    public void save() {
        System.out.println("save running.....");
    }
}
```



##### 3.4 Bean实例化三种方式

- 无参构造方法实例化

- 工厂静态方法实例化

- 工厂实例方法实例化



1）使用无参构造方法实例化

它会根据默认无参构造方法来创建类对象，如果bean中没有默认无参构造函数，将会创建失败

`<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl"></bean>`



2）工厂静态方法实例化

工厂的静态方法返回Bean实例



xml配置

```html
 <!--   使用工厂静态模式创建bean对象-->
 <!--    不需要先有工厂对象，然后调方法，静态的可以直接调-->
 <bean id="userDao" class="com.xrj.factory.StaticFactory" factory-method="getUserDao"></bean>
```



```java
package com.xrj.factory;

import com.xrj.dao.UserDao;
import com.xrj.dao.impl.UserDaoImpl;

public class StaticFactory {
    public static UserDao getUserDao(){
        return new UserDaoImpl();
    }
}
```





3）工厂实例方法实例化

工厂的非静态方法返回Bean实例



xml配置：

```html
<!--   使用工厂动态模式创建bean对象-->
<!--    必须先有动态工厂对象，然后才能调用方法-->
<bean id="factory" class="com.xrj.factory.DynamicFactory"></bean> 	<!--创建工厂对象-->
<bean id="userDao" factory-bean="factory" factory-method="getUserDao"></bean> <!--从工厂对象获得getUserDao方法-->
```



```java
package com.xrj.factory;

import com.xrj.dao.UserDao;
import com.xrj.dao.impl.UserDaoImpl;

public class DynamicFactory {
    public UserDao getUserDao(){
        return new UserDaoImpl();
    }
}
```





##### 3.5 Bean的依赖注入入门



①创建UserService，UserService 内部在调用UserDao的save() 方法

```java
package com.xrj.service.impl;

import com.xrj.dao.UserDao;
import com.xrj.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/*
 * servece层 调用Userdao
 * @param
 * @return
 */
public class UserServiceImpl implements UserService {
	@Override
    public void save() {
        ApplicationContext app=new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) app.getBean("userDao");
        userDao.save();
   }
}
```



②将UserServiceImpl 的创建权交给Spring

`<bean id="userService" class="com.xrj.service.impl.UserServiceImpl"></bean>`



③从Spring 容器中获得UserService 进行操作

```java
package com.xrj.demo;

import com.xrj.service.UserService;
import com.xrj.service.impl.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;


//由于没有配tomcat服务器，UserController充当web层
//调用service层，service又包含了dao，进而调用dao
public class UserController {
    public static void main(String[] args) {
//        UserService userService=new UserServiceImpl();
//        userService.save();

        //使用spring获取
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = (UserService) applicationContext.getBean("userService");
        userService.save();
    }
}
```





##### 3.6 Bean的依赖注入分析

目前UserService实例和UserDao实例都存在于Spring容器中，当前的做法是在容器外部获得UserService 

实例和UserDao实例，然后在程序中进行结合。

![image-20211223164209388](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211223164209388.png)



##### 3.7 Bean的依赖注入概念

依赖注入（Dependency Injection）：它是Spring 框架核心IOC 的具体实现。



在编写程序时，通过控制反转，把对象的创建交给了Spring，但是代码中不可能出现没有依赖的情况。

IOC 解耦只是降低他们的依赖关系，但不会消除。例如：业务层仍会调用持久层的方法。



那这种业务层和持久层的依赖关系，在使用Spring 之后，就让Spring 来维护了。

简单的说，就是坐等框架把持久层对象传入业务层，而不用我们自己去获取。



##### 3.8 Bean的依赖注入方式

怎么将UserDao怎样注入到UserService内部呢？

- 构造方法

- set方法



###### 1）set方法注入

在UserServiceImpl中添加setUserDao方法

```java
package com.xrj.service.impl;

import com.xrj.dao.UserDao;
import com.xrj.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/*
 * servece层 调用Userdao
 * @param
 * @return
 */
public class UserServiceImpl implements UserService {

    //将dao注入service，下面两句代码就不用写了
    //ApplicationContext app=new ClassPathXmlApplicationContext("applicationContext.xml");
    //UserDao userDao = (UserDao) app.getBean("userDao");
    private UserDao userDao;

    // set方法注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    
	@Override
    public void save() {
        userDao.save();
   }
}
```



配置Spring容器调用set方法进行注入

`<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl"/>`



```html
<bean id="userService" class="com.xrj.service.impl.UserServiceImpl"></bean>

<!--告诉spring，将dao通过set方法注入service-->
    <bean id="userService" class="com.xrj.service.impl.UserServiceImpl">
<!--name指的是setUserDao，这里去掉set，并将首字母小写  ref表示注给userDao bean的id-->
        <property name="userDao" ref="userDao"></property>
    </bean>
```



P命名空间注入本质也是set方法注入，但比起上述的set方法注入更加方便，主要体现在配置文件中，如下：

首先，需要引入P命名空间：xmlns:p="http://www.springframework.org/schema/p"

其次，需要修改注入方式

```html
<!--    使用p命名空间来注入-->
<bean id="userService" class="com.xrj.service.impl.UserServiceImpl" p:userDao-ref="userDao"/>
```



###### 2）构造方法注入

创建有参构造

```java
package com.xrj.service.impl;

import com.xrj.dao.UserDao;
import com.xrj.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/*
 * servece层 调用Userdao
 * @param
 * @return
 */
public class UserServiceImpl implements UserService {

    //将dao注入service，下面两句代码就不用写了
    //ApplicationContext app=new ClassPathXmlApplicationContext("applicationContext.xml");
    //UserDao userDao = (UserDao) app.getBean("userDao");
    private UserDao userDao;

    //构造方法注入
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    public UserServiceImpl() {
    }
    
    
    public void save() {
//        ApplicationContext app=new ClassPathXmlApplicationContext("applicationContext.xml");
//        UserDao userDao = (UserDao) app.getBean("userDao");
        userDao.save();
   }
}
```



配置Spring容器调用有参构造时进行注入

```html
<!--告诉spring，将dao通过有参构造方法注入service-->
    <bean id="userService" class="com.xrj.service.impl.UserServiceImpl">
<!--name="userDao"是构造内部的参数名  ref="userDao"表示引用容器中bean的id-->
        <constructor-arg name="userDao" ref="userDao"></constructor-arg>
    </bean>
```



##### 3.9 Bean的依赖注入的数据类型

上面的操作，都是注入的引用Bean，除了对象的引用可以注入，普通数据类型，集合等都可以在容器中进行注入。

注入数据的三种数据类型

- 普通数据类型

- 引用数据类型

- 集合数据类型

其中引用数据类型，此处就不再赘述了，之前的操作都是对UserDao对象的引用进行注入的，下面将以set方法注入为

例，演示普通数据类型和集合数据类型的注入。



###### 1）普通数据类型的注入

```java
package com.xrj.dao.impl;

import com.xrj.domain.User;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class UserDaoImpl implements com.xrj.dao.UserDao {

    //注入普通数据类型
    private String username;
    private int age;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(int age) {
        this.age = age;
    }


    public void save() {
        System.out.println(username+"======="+age);
        System.out.println("save running.....");
    }
}
```



xml配置

```html
    <bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl">
<!-- 这里不需要ref，因为ref是对象引用，这里是普通数据类型用value-->
        <property name="username" value="xrj"/>
        <property name="age" value="22"/>
    </bean>
```



此时运行	 System.out.println(username+"======="+age);		会输出		xrj=======22





###### 2）集合数据类型（List<String>）的注入

```java
package com.xrj.dao.impl;

import com.xrj.domain.User;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class UserDaoImpl implements com.xrj.dao.UserDao {

    //集合的注入
    private List<String> strList;
   
    public void setStrList(List<String> strList) {
        this.strList = strList;
    }


    public void save() {
        System.out.println(strList);
        System.out.println("save running.....");
    }
}

```



xml配置

```html
<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl">
        <!-- 这里是集合list，用list-->
        <property name="strList">
            <list>
<!--list中是string，普通对象，因此用value，如果是对象就用ref-->
                <value>aaa</value>
                <value>bbb</value>
                <value>ccc</value>
            </list>
        </property>
</bean> 
```



此时运行	System.out.println(strList);    则会输出	[aaa, bbb, ccc]



###### 3）集合数据类型（Map）的注入

```java
package com.xrj.dao.impl;

import com.xrj.domain.User;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class UserDaoImpl implements com.xrj.dao.UserDao {

    //集合的注入
    private Map<String, User> userMap;

    public void setUserMap(Map<String, User> userMap) {
        this.userMap = userMap;
    }

    public void save() {
        System.out.println(userMap);
        System.out.println("save running.....");
    }
}
```



xml配置文件

```html
<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl">

<!--这里是map注入-->
        <property name="userMap">
<!--map内部是键值对，因此用entry-->
            <map>
                <entry key="u1" value-ref="user1"></entry>
                <entry key="u2" value-ref="user2"></entry>
            </map>
        </property>  
    </bean>

<bean id="user1" class="com.xrj.domain.User">
        <property name="name" value="xu"></property>
        <property name="age" value="22"></property>
        <property name="address" value="北京"></property>
    </bean>

    <bean id="user2" class="com.xrj.domain.User">
        <property name="name" value="xxx"></property>
        <property name="age" value="22"></property>
        <property name="address" value="河南"></property>
    </bean>
```



运行	 System.out.println(userMap);	会输出	{u1=User{name='xu', age=22, address='北京'}, u2=User{name='xxx', age=22, address='河南'}}



###### 4) 集合数据类型（Properties）的注入

```java
package com.xrj.dao.impl;

import com.xrj.domain.User;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class UserDaoImpl implements com.xrj.dao.UserDao {

    //集合的注入
    private Properties properties;

    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    public void save() {
        System.out.println(properties);
        System.out.println("save running.....");
    }
}
```



xml配置

```html
    <bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl">

        <!--这里是properties注入-->
        <property name="properties">
            <props>
                <prop key="xrj1">xrj111</prop>
                <prop key="xrj2">xrj222</prop>
                <prop key="xrj3">xrj333</prop>
            </props>
        </property>

    </bean>
```



运行	System.out.println(properties);	输出	{xrj3=xrj333, xrj2=xrj222, xrj1=xrj111}





##### 3.9 引入其他配置文件（分模块开发）

实际开发中，Spring的配置内容非常多，这就导致Spring配置很繁杂且体积很大，所以，可以将部分配置拆解到其他

配置文件中，而在Spring主配置文件通过import标签进行加载

<import resource="applicationContext-xxx.xml"/>





### 4、Spring相关API

##### 4.1 ApplicationContext的继承体系

**applicationContext：**接口类型，代表应用上下文，可以通过其实例获得 Spring 容器中的 Bean 对象





##### **4.2 ApplicationContext的实现类**

1）ClassPathXmlApplicationContext

它是从类的根路径下加载配置文件 推荐使用这种



2）FileSystemXmlApplicationContext

它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。



3）AnnotationConfigApplicationContext

当使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解。



##### 4.3 getBean()方法使用



```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");

UserService userService1 = (UserService) applicationContext.getBean("userService");

UserService userService2 = applicationContext.getBean(UserService.class);
```



其中，当参数的数据类型是字符串时，表示根据Bean的id从容器中获得Bean实例，返回是Object，需要强转。

当参数的数据类型是Class类型时，表示根据类型从容器中匹配Bean实例，当容器中相同类型的Bean有多个时，

则此方法会报错。







# 二、IOC和注解开发



### 1、Spring配置数据源



##### 1.1 数据源（连接池）的作用

• 数据源(连接池)是提高程序性能如出现的

• 事先实例化数据源，初始化部分连接资源

• 使用连接资源时从数据源中获取

• 使用完毕后将连接资源归还给数据源

常见的数据源(连接池)：DBCP、C3P0、BoneCP、Druid等



**1.2 数据源的开发步骤**

① 导入数据源的坐标和数据库驱动坐标

② 创建数据源对象

③ 设置数据源的基本连接数据

④ 使用数据源获取连接资源和归还连接资源



##### 1.2 数据源的手动创建

①导入c3p0和druid的坐标

```xml
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```



①导入mysql坐标

```xml
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```



②创建c3p0连接池

```JAVA
@Test
//测试手动创建c3p0数据源
public void test1() throws Exception {
    //创建数据源
    ComboPooledDataSource dataSource=new ComboPooledDataSource();
    //设置驱动
    dataSource.setDriverClass("com.mysql.jdbc.Driver");
    //操作book数据库，设置数据库地址
    dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/book?serverTimezone=UTC");
    //设置用户名
    dataSource.setUser("root");
    //设置密码
    dataSource.setPassword("Xu990918");

    Connection connection = dataSource.getConnection();
    System.out.println(connection);
    connection.close();
}
```



②创建druid连接池

```java
@Test
//测试手动创建druid数据源
public void test2() throws SQLException {
    //创建数据源
    DruidDataSource dataSource=new DruidDataSource();
    //设置驱动
    dataSource.setDriverClassName("com.mysql.jdbc.Driver");
    //操作book数据库，设置数据库地址
    dataSource.setUrl("jdbc:mysql://localhost:3306/book?serverTimezone=UTC");
    //设置用户名
    dataSource.setUsername("root");
    //设置密码
    dataSource.setPassword("Xu990918");

    DruidPooledConnection connection=dataSource.getConnection();
    System.out.println(connection);
    connection.close();
}
```



③提取jdbc.properties配置文件

创建jdbc.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/book?serverTimezone=UTC
jdbc.username=root
jdbc.password=Xu990918
```



④读取jdbc.properties配置文件创建连接池

```java
@Test
//测试手动创建c3p0数据源,加载properties配置文件形式
public void test3() throws Exception {
    //读取配置文件      getBundle中的name名是resources下的，只需要基本名即可
    ResourceBundle resourceBundle = ResourceBundle.getBundle("jdbc");
    //读取参数
    String driver=resourceBundle.getString("jdbc.driver");
    String url=resourceBundle.getString("jdbc.url");
    String username=resourceBundle.getString("jdbc.username");
    String password=resourceBundle.getString("jdbc.password");

    //创建数据源
    ComboPooledDataSource dataSource=new ComboPooledDataSource();
    dataSource.setDriverClass(driver);
    dataSource.setJdbcUrl(url);
    dataSource.setUser(username);
    dataSource.setPassword(password);

    Connection connection = dataSource.getConnection();
    System.out.println(connection);
    connection.close();

}
```





##### 1.3 spring创建数据源

可以将DataSource的创建权交由Spring容器去完成

- DataSource有无参构造方法，而Spring默认就是通过无参构造方法实例化对象的

- DataSource要想使用需要通过set方法设置数据库连接信息，而Spring可以通过set方法进行字符串注入

```xml
 <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/book"></property>
        <property name="user" value="root"></property>
        <property name="password" value="Xu990918"></property>
    </bean>
```



测试从容器当中获取数据源

```java
@Test
//测试spring容器创建c3p0数据源,加载properties配置文件形式
public void test4() throws Exception {
    ApplicationContext app =new ClassPathXmlApplicationContext("applicationContext.xml");
    DataSource dataSource = app.getBean(DataSource.class);

    Connection connection = dataSource.getConnection();
    System.out.println(connection);
    connection.close();

}
```





##### 1.4 抽取jdbc配置文件

```txt
applicationContext.xml加载jdbc.properties配置文件获得连接信息
首先，需要引入context命名空间和约束路径
    命名空间：xmlns：context=“http://www.springframework.org/schema/context"
    约束路径：http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd
```

```xml
<!--    加载外部的properties文件-->
<context:property-placeholder location="classpath:jdbc.properties"/>

<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"></property>
    <property name="jdbcUrl" value="${jdbc.url}"></property>
    <property name="user" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
```



##### 1.5 spring加载properties文件

```xml
<context:property-placeholder location="classpath:xxx.properties"/>`
<property name="" value="${key}"/>
```





### 2、spring注解开发



##### 2.1spring原始注解

Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以注解开发是一种趋势，注解代替xml配置

文件可以简化配置，提高开发效率。

Spring原始注解主要是替代<Bean>的配置



@Component 											   使用在类上用于实例化Bean

@Controller 												  使用在web层类上用于实例化Bean

@Service 													    使用在service层类上用于实例化Bean

@Repository 												 使用在dao层类上用于实例化Bean

@Autowired 												  使用在字段上用于根据类型依赖注入

@Qualifier 													 结合@Autowired一起使用用于根据名称进行依赖注入

@Resource 													相当于@Autowired+@Qualifier，按照名称进行注入

@Value 														   注入普通属性

@Scope 														  标注Bean的作用范围

@PostConstruct 											使用在方法上标注该方法是Bean的初始化方法

@PreDestroy 												 使用在方法上标注该方法是Bean的销毁方法



**注意：**

使用注解进行开发时，需要在applicationContext.xml中配置组件扫描，作用是指定哪个包及其子包下的Bean

需要进行扫描以便识别使用注解配置的类、字段和方法



```xml
<!-- spring会扫描com.xrj下的所有包及其子包-->
 <context:component-scan base-package="com.xrj"/>
```



- 使用@Compont或@Repository标识UserDaoImpl需要Spring进行实例化。

```java
//<bean id="userDao" class="com.xrj.dao.impl.UserDaoImpl"></bean>
//@Component("userDao") //相当于上一行在xml中的配置文件
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    @Override
    public void save() {
        System.out.println("save running.....");
    }
}
```





- 使用@Compont或@Service标识UserServiceImpl需要Spring进行实例化

- 使用@Autowired或者@Autowired+@Qulifier或者@Resource进行userDao的注入



```java
//<bean id="userService" class="com.xrj.service.impl.UserServiceImpl"> </bean>
// @Component("userService")
@Service("userService")
//@Scope("prototype")
@Scope("singleton")
public class UserServiceImpl implements UserService {

    //<property name="userDao" ref="userDao"></property>
    //注入dao
//    @Autowired  //自动注入，单独写Autowired也可以，他是按照数据类型从spring容器中进行匹配的
//    @Qualifier("userDao")   //写入注入bean的id   是按照id值从容器中进行匹配  但是要结合@Autowired一起用
    @Resource(name = "userDao")    //也可以直接用@Resource注入，@Resource相当于@Autowired+@Qualifier
    private UserDao userDao;
    //使用注解方式，set方法可以不写
//    public void setUserDao(UserDao userDao) {
//        this.userDao = userDao;
//    }

    @Override
    public void save() {
        userDao.save();
    }
}
```





- 使用@Value进行字符串的注入

  ```java
  @Repository("userDao")
  public class UserDaoImpl implements UserDao {
      @Value("注入普通数据")
  	private String str;
  	@Value("${jdbc.driver}")
  	private String driver;
      
      @Override
      public void save() {
          System.out.println(str);
  		System.out.println(driver);
          System.out.println("save running.....");
      }
  }
  
  ```





- 使用@Scope标注Bean的范围    

    ```java
    //@Scope("prototype")
    @Scope("singleton")
    public class UserDaoImpl implements UserDao {
    	//此处省略代码
    }
    ```
    
    




- 使用@PostConstruct标注初始化方法，使用@PreDestroy标注销毁方法

```java
@PostConstruct  //表示在构造之后
public void init(){
    System.out.println("Service对象的初始化方法");
}

@PreDestroy     //销毁之前
public void destory(){
    System.out.println("Service对象的销毁方法");
}
```







##### 2.2 spring新注解

使用上面的注解还不能全部替代xml配置文件，还需要使用注解替代的配置如下：

- 非自定义的Bean的配置：**<bean>**

- 加载properties文件的配置：**<context:property-placeholder>**

- 组件扫描的配置：**<context:component-scan>**

- 引入其他文件：**<import>**



@Configuration 										用于指定当前类是一个 Spring 配置类，当创建容器时会从该类上加载注解

@ComponentScan								   用于指定 Spring 在初始化容器时要扫描的包。作用和在 Spring 的 xml 配置文件中的

​																	<context:component-scan base-package="com.xrj"/>一样

@Bean 													   使用在方法上，标注将该方法的返回值存储到 Spring 容器中

@PropertySource 									用于加载.properties 文件中的配置

@Import 													用于导入其他配置类









- @Configuration

- @ComponentScan

- @Import

```java
package com.xrj.config;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.*;

//标志该类是spring核心配置类
@Configuration
//<context:component-scan base-package="com.xrj"/>
@ComponentScan("com.xrj")
//<import resource=""/>
//加载分配置文件
//多个的时候可以写多个import，也可以@Import({DataSourceConfiguration.class,xx.class})数组形式
@Import(DataSourceConfiguration.class)
public class SpringConfiguration {

}
```





- @PropertySource

- @value

```java
//分开写，这里写数据源相关配置
// <context:property-placeholder location="classpath:jdbc.properties"/>
@PropertySource("classpath:jdbc.properties")  //加载外部的properties文件
public class DataSourceConfiguration {

    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    }
```





- @Bean

  ```java
  //分开写，这里写数据源相关配置
  // <context:property-placeholder location="classpath:jdbc.properties"/>
  @PropertySource("classpath:jdbc.properties")  //加载外部的properties文件
  public class DataSourceConfiguration {
  
      @Value("${jdbc.driver}")
      private String driver;
      @Value("${jdbc.url}")
      private String url;
      @Value("${jdbc.username}")
      private String username;
      @Value("${jdbc.password}")
      private String password;
  
      //spring会将当前方法的返回值以指定名称存储到spring容器中，名字为dataSource
      @Bean("dataSource")
      public ComboPooledDataSource getDataSource() throws Exception {
          ComboPooledDataSource dataSource=new ComboPooledDataSource();
          dataSource.setDriverClass(driver);
          dataSource.setJdbcUrl(url);
          dataSource.setUser(username);
          dataSource.setPassword(password);
          return dataSource;
      }
  }
  ```





测试加载核心配置类创建Spring容器

```java
public class UserController {
    public static void main(String[] args) {
        //ClassPathXmlApplicationContext app=new ClassPathXmlApplicationContext("applicationContext.xml");

        //这里用注解的方式代替xml文件
        ApplicationContext app=new AnnotationConfigApplicationContext(SpringConfiguration.class);
        UserService userService = (UserService) app.getBean("userService");
        userService.save();
    }
}
```





### 3、spring整合Junit



##### 3.1、原始Junit测试Spring的问题

在测试类中，每个测试方法都有以下两行代码：

ApplicationContext ac = **new** ClassPathXmlApplicationContext(**"bean.xml"**);

IAccountService as = ac.getBean("accountService",IAccountService.**class**);



这两行代码的作用是获取容器，如果不写的话，直接会提示空指针异常。所以又不能轻易删掉





##### 3.2、解决上述问题

- 让SpringJunit负责创建Spring容器，但是需要将配置文件的名称告诉它

- 将需要进行测试Bean直接在测试类中进行注入





##### 3.3、Spring集成Junit步骤

① 导入spring集成Junit的坐标

② 使用@Runwith注解替换原来的运行期

③ 使用@ContextConfiguration指定配置文件或配置类

④ 使用@Autowired注入需要测试的对象

⑤ 创建测试方法进行测试



##### 3.4、Spring集成Junit代码实现



① 导入spring集成Junit的坐标

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```



② 使用@Runwith注解替换原来的运行期

```java
@RunWith(SpringJUnit4ClassRunner.class)
public class SpringJunitTest {
}
```



③ 使用@ContextConfiguration指定配置文件或配置类

```java
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")     使用配置文件方式
@ContextConfiguration(classes = {SpringConfiguration.class})    //使用注解方式
public class SpringJunitTest {
}
```



④ 使用@Autowired注入需要测试的对象

```java
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")     使用配置文件方式
@ContextConfiguration(classes = {SpringConfiguration.class})    //使用注解方式
public class SpringJunitTest {

    @Autowired
    private UserService userService;

    @Autowired
    private DataSource dataSource;

}
```



⑤ 创建测试方法进行测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")     使用配置文件方式
@ContextConfiguration(classes = {SpringConfiguration.class})    //使用注解方式
public class SpringJunitTest {

    @Autowired
    private UserService userService;

    @Autowired
    private DataSource dataSource;

    @Test
    public void test1() throws SQLException {
        userService.save();
        System.out.println(dataSource.getConnection());
    }

}
```







# 三、SpringMVC



### 1.spring集成web环境



##### 1.1ApplicationContext应用上下文获取方式



应用上下文对象是通过**new ClasspathXmlApplicationContext(spring配置文件)** 方式获取的，但是每次从

容器中获得Bean时都要编写**new ClasspathXmlApplicationContext(spring配置文件)** ，这样的弊端是配置

文件加载多次，应用上下文对象创建多次。



在Web项目中，可以使用**ServletContextListener**监听Web应用的启动，我们可以在Web应用启动时，就加

载Spring的配置文件，创建应用上下文对象**ApplicationContext**，在将其存储到最大的域**servletContext**域

中，这样就可以在任意位置从域中获得应用上下文**ApplicationContext**对象了。



##### 1.2 Spring提供获取应用上下文的工具

上面的分析不用手动实现，Spring提供了一个监听器**ContextLoaderListener**就是对上述功能的封装，该监

听器内部加载Spring配置文件，创建应用上下文对象，并存储到**ServletContext**域中，提供了一个客户端工

具**WebApplicationContextUtils**供使用者获得应用上下文对象。



所以我们需要做的只有两件事：

① 在**web.xml**中配置**ContextLoaderListener**监听器（导入spring-web坐标）

② 使用**WebApplicationContextUtils**获得应用上下文对象**ApplicationContext**



##### 1.3 导入Spring集成web的坐标

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-web</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
```



##### 1.4 配置ContextLoaderListener监听器

```xml
<!--全局初始化参数-->
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<!--配置监听器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
```





##### 1.5 通过工具获得应用上下文对象

```java
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        
        ServletContext servletContext = req.getServletContext();
     
       // 通过spring配的listenter来创建上下文对象
        WebApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        UserServce userServce = app.getBean(UserServce.class);
        userServce.save();
    }
}
```





### 2.SpringMVC简介

##### 2.1 SpringMVC概述



**SpringMVC** 是一种基于 Java 的实现 **MVC 设计模型**的请求驱动类型的轻量级 **Web 框架**，属于

**SpringFrameWork** 的后续产品，已经融合在 Spring Web Flow 中。

SpringMVC 已经成为目前最主流的MVC框架之一，并且随着Spring3.0 的发布，全面超越 Struts2，成为最优

秀的 MVC 框架。它通过一套注解，让一个简单的 Java 类成为处理请求的控制器，而无须实现任何接口。同时

它还支持 **RESTful** 编程风格的请求。



##### 2.2 SpringMVC快速入门

![image-20220111103102662](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220111103102662.png)

需求：客户端发起请求，服务器端接收请求，执行逻辑并进行视图跳转。

开发步骤：

① 导入SpringMVC相关坐标

② 配置SpringMVC核心控制器DispathcerServlet

③ 创建Controller类和视图页面

④ 使用注解配置Controller类中业务方法的映射地址

⑤ 配置SpringMVC核心文件 spring-mvc.xml

⑥ 客户端发起请求测试





① 导入Spring和SpringMVC的坐标

```xml
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.0.5.RELEASE</version>
    </dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
```



① 导入Servlet和Jsp的坐标

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>3.0.1</version>
</dependency>
<dependency>
  <groupId>javax.servlet.jsp</groupId>
  <artifactId>javax.servlet.jsp-api</artifactId>
  <version>2.2.1</version>
</dependency>
<dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.3</version>
    </dependency>
```





② 在web.xml配置SpringMVC的核心控制器

```xml
<!--配置springMVC的前端控制器-->
<!--load-on-startup代表服务器启动就创建对象，不加就第一次访问时创建对象-->
<servlet>
  <servlet-name>DispatcherServlet</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <load-on-startup>1</load-on-startup>
</servlet>

<!--<url-pattern>/</url-pattern>  代表每次访问时任何请求都要走这个servlet-->
<servlet-mapping>
  <servlet-name>DispatcherServlet</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```



③ 创建Controller和业务方法

```java

public class UserController {

    public String save(){
        System.out.println("Controller save running...");
        //return 是要跳转的视图
        return "success.jsp";
    }
}
```



③ 创建视图页面success.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>Success</h1>
</body>
</html>
```



④ 配置注解

```java
@Controller
// @RequestMapping("/xxx")
// 请求地址  http://localhost:8080/xxx/quick
public class UserController {

    // 请求地址  http://localhost:8080/quick
    // 访问quick时会映射到save方法
    @RequestMapping("/quick")
    public String save(){
        System.out.println("Controller save running...");
        //return 是要跳转的视图
        return "success.jsp";
    }
}
```



⑤ 创建spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--Controller的组件扫描-->
    <context:component-scan base-package="com.xrj.controller"/>

</beans>
```



创建spring-mvc.xml还需要告诉spring配置文件在哪，spring-mvc.xml是核心控制器使用，因此在配置springMVC前端控制器时告诉控制器spring-mvc.xml在哪，applicationContext.xml通过配置全局初始化参数使用listener监听器创建，因此spring-mvc.xml也需要配置全局初始化参数

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!--配置springMVC的前端控制器-->
  <!--load-on-startup代表服务器启动就创建对象，不加就第一次访问时创建对象-->
  <servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml </param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <!--<url-pattern>/</url-pattern>  代表每次访问时任何请求都要走这个servlet-->
  <servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!--全局初始化参数-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>

  <!--配置监听器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <servlet>
    <servlet-name>UserServlet</servlet-name>
    <servlet-class>com.xrj.web.UserServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>UserServlet</servlet-name>
    <url-pattern>/userServlet</url-pattern>
  </servlet-mapping>
</web-app>
```





⑥ 访问测试地址









##### 2.3 SpringMVC流程图示



代码角度分析：

首先输入访问地址后，去找tomcat，因为servlet配置的是全局的（“/”），所以tomcat去找这个servlet，然后servlet根据quick找到对应资源

![image-20220111104926405](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220111104926405.png)



逻辑角度分析：



![image-20220108211806820](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220108211806820.png)







### 3.SpringMVC 组件解析



##### 3.1 SpringMVC的执行流程

![image-20220111110106745](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220111110106745.png)



① 用户发送请求至前端控制器DispatcherServlet。 

② DispatcherServlet收到请求调用HandlerMapping处理器映射器 （去查找对应的资源）

③ 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果

有则生成)一并返回给DispatcherServlet。 

④ DispatcherServlet调用HandlerAdapter处理器适配器	（执行该资源）

⑤ HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。 

⑥ Controller执行完成返回ModelAndView。 

⑦ HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。 

⑧ DispatcherServlet将ModelAndView传给ViewReslover视图解析器。

⑨ ViewReslover解析后返回具体View。 

⑩ DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。DispatcherServlet响应用户。





##### 3.2 SpringMVC注解解析

**@RequestMapping**

作用：用于建立请求 URL 和处理请求方法之间的对应关系

位置：

- 类上，请求URL 的第一级访问目录。此处不写的话，就相当于应用的根目录

- 方法上，请求 URL 的第二级访问目录，与类上的使用@ReqquestMapping标注的一级目录一起组成访问虚拟路径

属性：

```java
@Controller
// @RequestMapping("/xxx")
// 请求地址  http://localhost:8080/xxx/quick
public class UserController {

    // 请求地址  http://localhost:8080/quick
    // 访问quick时会映射到save方法
    @RequestMapping("/quick")
    public String save(){
        System.out.println("Controller save running...");
        //return 是要跳转的视图
        return "success.jsp";
    }
}
```



- **value**：用于指定请求的URL。它和path属性的作用是一样的

- **method**：用于指定请求的方式

- **params**：用于指定限制请求参数的条件。它支持简单的表达式。要求请求参数的key和value必须和配置的一模一样

例如：

- **params = {"accountName"}**，表示请求参数必须有accountName

- **params = {"moeny!100"}**，表示请求参数中money不能是100



```java
@RequestMapping(value = "/quick",method = RequestMethod.GET,params = {"username"})
public String save(){
    System.out.println("Controller save running...");
    //return 是要跳转的视图
    return "success.jsp";
}
```





##### 3.3 SpringMVC的XML配置解析



**REDIRECT_URL_PREFIX** = **"redirect:"** 		 重定向前缀

**FORWARD_URL_PREFIX** = **"forward:"** 		转发前缀（默认值）

**prefix** = **""**; 		视图名称前缀

**suffix** = **""**; 		视图名称后缀



forward:

```java
@RequestMapping(value = "/quick",method = RequestMethod.GET,params = {"username"})
public String save(){
    System.out.println("Controller save running...");
    //return 是要跳转的视图
    return "forward:success.jsp";
    //默认省略forward，是请求转发
}
```



redirect:

```java
@RequestMapping(value = "/quick",method = RequestMethod.GET,params = {"username"})
public String save(){
    System.out.println("Controller save running...");
    //return 是要跳转的视图
    return "redirect:success.jsp";
    //redirect是重定向
}
```



**视图解析器**

我们可以通过属性注入的方式修改视图的的前后缀

在spring-mvc中配置

```xml
<!--配置内部资源视图解析器-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"> 
    <property name="prefix" value="/jsp"></property> 
    <property name="suffix" value=".jsp"></property>
</bean>
```



```java
public String save(){
    System.out.println("Controller save running...");
    //		/jsp/success.jsp
    return "forward:success.jsp";
}
```







# 四、SpringMVC的请求和响应

### 1.SpringMVC的数据响应

##### 1.1 SpringMVC的数据响应方式

1） 页面跳转

- 直接返回字符串				return "success.jsp";

- 通过ModelAndView对象返回

2） 回写数据

- 直接返回字符串

- 返回对象或集合



##### 1.2 页面跳转

**1. 返回字符串形式**

直接返回字符串：此种方式会将返回的字符串与视图解析器的前后缀拼接后跳转

![image-20220112095028873](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220112095028873.png)

返回带有前缀的字符串：

转发：**forward:/WEB-INF/views/index.jsp**

重定向：**redirect:/index.jsp**		（客户端再次请求，WEB-INF属于受保护的文件夹，外界不能直接访问，因此想要重定向，资源必须处															  于一个有权限访问的位置）



**2. 返回ModelAndView对象**

```java
// 返回ModelAndView对象
@RequestMapping(value = "/quick2")
public ModelAndView save2(){
    /*
        Model：模型    作用：封装数据
        View：视图     作用：展示数据
     */
    ModelAndView modelAndView = new ModelAndView();
    //设置模型数据1
    modelAndView.addObject("username","xrj");
    //设置视图名称
    modelAndView.setViewName("success.jsp");
    return modelAndView;
}

// 返回ModelAndView对象
@RequestMapping(value = "/quick3")
public ModelAndView save3(ModelAndView modelAndView){
    modelAndView.addObject("username","xrj111");
    modelAndView.setViewName("success.jsp");
    return modelAndView;
}
```



**3. 向request域存储数据**

在进行转发时，往往要向request域中存储数据，在jsp页面中显示，那么Controller中怎样向request

域中存储数据呢？

① 通过SpringMVC框架注入的request对象setAttribute()方法设置

```java
//model是spring封装好的，request是javaweb对象，tomcat产生的
// 使用原始方法往request域中存放数据
@RequestMapping(value = "/quick5")
public String save5(HttpServletRequest request){   //这里是model
    request.setAttribute("username","yyyyy");
    return "success.jsp";           //这里是view视图
}
```



② 通过ModelAndView的addObject()方法设置

```java
@RequestMapping(value = "/quick2")
public ModelAndView save2(){
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("username","xrj");
    modelAndView.setViewName("success.jsp");
    return modelAndView;
}
```



##### 1.3 回写数据

**1. 直接返回字符串**

Web基础阶段，客户端访问服务器端，如果想直接回写字符串作为响应体返回的话，只需要使用

response.getWriter().print(“hello world”) 即可，那么在Controller中想直接回写字符串该怎样呢？



① 通过SpringMVC框架注入的response对象，使用response.getWriter().print(“hello world”) 回写数

据，此时不需要视图跳转，业务方法返回值为void。

```java
@RequestMapping(value = "/quick6")
public void save6(HttpServletResponse response) throws IOException {   //这里是model
    response.getWriter().println("hello xrj");
}
```



② 将需要回写的字符串直接返回，但此时需要通过**@ResponseBody**注解告知SpringMVC框架，方法

返回的字符串不是跳转是直接在http响应体中返回

```java
@RequestMapping(value = "/quick7")
@ResponseBody		// 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public String save7() throws IOException {   //这里是model
    return "hello xyy";
}
```



③在异步项目中，客户端与服务器端往往要进行json格式字符串交互，此时我们可以手动拼接json字符串返回。

```java
@RequestMapping(value = "/quick8")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public String save8() throws IOException {
    return "{\"username\":\"xrj\",\"age\":18}";
}
```





④上述方式手动拼接json格式字符串的方式很麻烦，开发中往往要将复杂的java对象转换成json格式的字符串，

我们可以使用web阶段学习过的json转换工具jackson进行转换，导入jackson坐标。

```xml
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-core</artifactId>
  <version>2.9.5</version>
</dependency>
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.9.5</version>
</dependency>
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-annotations</artifactId>
  <version>2.9.5</version>
</dependency>
```

通过jackson转换json格式字符串，回写字符串。

```java
@RequestMapping(value = "/quick9")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public String save9() throws IOException {
    User user=new User();
    user.setUsername("xrj");
    user.setAge(30);

    //使用json转换工具将对象转换成json格式字符串再返回
    ObjectMapper objectMapper=new ObjectMapper();
    String json = objectMapper.writeValueAsString(user);

    return json;
}
```



**2. 返回对象或集合**

通过SpringMVC帮助我们对对象或集合进行json字符串的转换并回写，为处理器适配器配置消息转换参数，

指定使用jackson进行对象或集合的转换，因此需要在spring-mvc.xml中进行如下配置：

```xml
<!--配置处理器映射器-->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <list>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
        </list>
</property>
</bean>
```



```java
@RequestMapping(value = "/quick10")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
//期望SpringMVC自动将User转换成json格式的字符串
public User save10() throws IOException {
    User user=new User();
    user.setUsername("xrj");
    user.setAge(30);

    return user;
}
```

 



在方法上添加**@ResponseBody**就可以返回json格式的字符串，但是这样配置比较麻烦，配置的代码比较多，

因此，我们可以使用mvc的注解驱动代替上述配置。

```xml
<!--mvc注解驱动   代替上边配置-->
<mvc:annotation-driven/>
```

在 SpringMVC 的各个组件中，**处理器映射器**、**处理器适配器**、**视图解析器**称为 SpringMVC 的三大组件。

使用<mvc:annotation-driven/>自动加载 RequestMappingHandlerMapping（处理映射器）和

RequestMappingHandlerAdapter（ 处 理 适 配 器 ），可用在Spring-xml.xml配置文件中使用

<mvc:annotation-driven>替代注解处理器和适配器的配置。

同时使用<mvc:annotation-driven>默认底层就会集成jackson进行对象或集合的json格式字符串的转换。





### 2.SpringMVC获得请求数据

##### 2.1 获得请求参数

客户端请求参数的格式是：**name=value&name=value… …**

服务器端要获得请求的参数，有时还需要进行数据的封装，SpringMVC可以接收如下类型的参数：

- 基本类型参数

-  POJO类型参数

- 数组类型参数

- 集合类型参数



##### 2.2 获得基本类型参数

Controller中的业务方法的参数名称要与请求参数的name一致，参数值会自动映射匹配。

http://localhost:8080/spring_MVC/quick11?username=xrj&age=22

```java
@RequestMapping(value = "/quick11")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save11(String username,int age) throws IOException {
    System.out.println(username);
    System.out.println(age);
}
```





##### 2.3 获得POJO类型参数

Controller中的业务方法的POJO参数的属性名与请求参数的name一致，参数值会自动映射匹配。

http://localhost:8080/spring_MVC/quick12?username=xrj&age=23

```java
public class User {
    private String username;
    private int age;

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", age=" + age +
                '}';
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

```java
@RequestMapping(value = "/quick12")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save12(User user) throws IOException {
    System.out.println(user);
}
```



##### 2.4 获得数组类型参数

Controller中的业务方法数组名称与请求参数的name一致，参数值会自动映射匹配。

http://localhost:8080/spring_MVC/quick13?strs=111&strs=222&strs=333

```java
@RequestMapping(value = "/quick13")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save13(String[] strs) throws IOException {
    System.out.println(Arrays.asList(strs)); //数组默认打印的是地址，因此转换一下输出
}
```



##### 2.5 获得集合类型参数

获得集合参数时，要将集合参数包装到一个POJO中才可以。

首先写一个提交数据的jsp页面

```jsp
<body>
    <form action="${pageContext.request.contextPath}/quick14" method="post">
        <%-- 表明是第一个User对象的username和age --%>
        <input type="text" name="userList[0].username"><br>
        <input type="text" name="userList[0].age"><br>
        <%-- 表明是第二User对象的username和age --%>
        <input type="text" name="userList[1].username"><br>
        <input type="text" name="userList[1].age"><br>
        <input type="submit" value="提交">
    </form>
</body>
```



新建一个VO类

```java
public class VO {
  private List<User> userList;

  @Override
  public String toString() {
    return "VO{" +
            "userList=" + userList +
            '}';
  }

  public List<User> getUserList() {
    return userList;
  }

  public void setUserList(List<User> userList) {
    this.userList = userList;
  }
}
```



```java 
// 获得集合类型参数
@RequestMapping(value = "/quick14")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save14(VO vo) throws IOException {
    System.out.println(vo);
}
```





##### 2.5 获得集合类型参数

当使用ajax提交时，可以指定contentType为json形式，那么在方法参数位置使用@RequestBody可以

直接接收集合数据而无需使用POJO进行包装。



首先引入jquery

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220113114501261.png" alt="image-20220113114501261" style="zoom:80%;" />

然后编写jsp页面使用ajax请求

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <%--引入jquery--%>
    <script src="${pageContext.request.contextPath}/js/jquery-3.3.1.js"></script>
    <script>
        var userList=new Array();
        userList.push({username:"xrj",age:22});
        userList.push({username:"xyy",age:20});

        $.ajax({
            type:"POST",
            url:"${pageContext.request.contextPath}/quick15",
            data:JSON.stringify(userList),
            contentType:"application/json;charset=utf-8"
        });

    </script>
</head>
<body>

</body>
</html>
```



```java
// 获得集合类型参数
@RequestMapping(value = "/quick15")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save15(@RequestBody List<User> userList) throws IOException {
    System.out.println(userList);
}
```



注意：通过谷歌开发者工具抓包发现，没有加载到jquery文件，原因是SpringMVC的前端控制器

DispatcherServlet的url-pattern配置的是/ ,代表对所有的资源都进行过滤操作，我们可以通过以下两种

方式指定放行静态资源：

- 在spring-mvc.xml配置文件中指定放行的资源

```xml
<!--开放资源的访问权限-->
<!--mapping代表映射地址 location代表哪个目录下的资源是对外开放的-->
<mvc:resources mapping="/js/**" location="/js/"/>
```

- 使用<mvc:default-servlet-handler/>标签

```xml
<!--也可以写为;  表示如果springmvc找不到资源，就交给原始容器tomcat去寻找资源-->
<mvc:default-servlet-handler/>
```



##### 2.6 请求乱码问题

当post请求时，数据会出现乱码，我们可以设置一个过滤器来进行编码的过滤。

```xml
<!--配置全局过滤的filter-->
<filter>
  <filter-name>CharacterEncodingFilter</filter-name>
  <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  <init-param>
    <param-name>encoding</param-name>
    <param-value>UTF-8</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>CharacterEncodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```





##### 2.7 参数绑定注解@requestParam

当请求的参数名称与Controller的业务方法参数名称不一致时，就需要通过@RequestParam注解显示的绑定

http://localhost:8080/spring_MVC/quick16?name=xrj

```java
@RequestMapping(value = "/quick16")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save16(@RequestParam(value = "name") String username) throws IOException {
    System.out.println(username);
}
```



注解@RequestParam还有如下参数可以使用：

- **value**：与请求参数名称

- **required**：此在指定的请求参数是否必须包括，默认是true，提交时如果没有此参数则报错

- **defaultValue**：当没有指定请求参数时，则使用指定的默认值赋值

```java
@RequestMapping(value = "/quick17")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save17(@RequestParam(value = "name",required = false,defaultValue = "xyy") String username) throws IOException {
    System.out.println(username);
}
```





##### 2.8 获得Restful风格的参数

**Restful**是一种软件**架构风格**、**设计风格**，而不是标准，只是提供了一组设计原则和约束条件。主要用于客户端和服务

器交互类的软件，基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存机制等。



REST风格的优点：

- 隐藏资源的访问行为，无法通过地址得知对资源是何种操作
- 书写简化





按照REST风格访问资源时使用**行为动作**区分对资源进行了何种操作

- http://localhost/users									查询全部用户信息 GET （查询）
- http://localhost/users/1                                查询指定用户信息 GET （查询）		
- http://localhost/users									添加用户信息 POST （新增/保存）
- http://localhost/users									修改用户信息 PUT （修改/更新）
- http://localhost/users/1                                 删除用户信息 DELETE （删除）		



**Restful**风格的请求是使用**“url+请求方式”**表示一次请求目的的，HTTP 协议里面四个表示操作方式的动词如下：

- GET：       用于获取资源

- POST：    用于新建资源

- PUT：      用于更新资源

- DELETE：用于删除资源

例如：

- /user/1 GET ： 	  得到 id = 1 的 user

- /user/1 DELETE：  删除 id = 1 的 user

- /user/1 PUT：        更新 id = 1 的 user

- /user POST：         新增 user



上述url地址/user/1中的1就是要获得的请求参数，在SpringMVC中可以使用占位符进行参数绑定。地址/user/1可以写成

/user/{id}，占位符{id}对应的就是1的值。在业务方法中我们可以使用@PathVariable注解进行占位符的匹配获取工作。

http://localhost:8080/spring_MVC/quick18/xrj

```java
//localhost:8080/quick18/xrj
@RequestMapping(value = "/quick18/{name}")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save18(@PathVariable(value = "name") String name) throws IOException {
    System.out.println(name);
}
```



##### 2.9 自定义类型转换器

- SpringMVC 默认已经提供了一些常用的类型转换器，例如客户端提交的字符串转换成int型进行参数设置。

- 但是不是所有的数据类型都提供了转换器，没有提供的就需要自定义转换器，例如：日期类型的数据就需要自

​		定义转换器。

自定义类型转换器的开发步骤：

① 定义转换器类实现Converter接口

```java
public class DateConverter implements Converter<String, Date> { //String转Date

    public Date convert(String dateStr) {
        //将日期字符串转换成日期对象，返回即可
        SimpleDateFormat format=new SimpleDateFormat("yyyy-MM-dd");
        Date date=null;
        try {
            date = format.parse(dateStr);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
```

② 在配置文件中声明转换器

```xml
<!--声明转换器  通过转换器工厂，让他帮我引用转换器-->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.xrj.converter.DateConverter"></bean>
        </list>
    </property>
</bean>
```

③ 在<annotation-driven>中引用转换器

```xml
<!--mvc注解驱动   代替上边配置-->     <!-- conversion-service="conversionService" 工厂转换器-->
<mvc:annotation-driven conversion-service="conversionService"/>
```



例如日期： 只能转换格式为2018/12/12的  输出 Wed Dec 12 00:00:00 CST 2018

配置后，可以转换2018-12-12

http://localhost:8080/spring_MVC/quick19?date=2018-12-12

```java
@RequestMapping(value = "/quick19")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save19(Date date) throws IOException {
    System.out.println(date);   //Wed Dec 12 00:00:00 CST 2018
}
```



##### 2.10 获得Servlet相关API

SpringMVC支持使用原始ServletAPI对象作为控制器方法的参数进行注入，常用的对象如下：

- HttpServletRequest

- HttpServletResponse

- HttpSession

只需要在想要获取方法的参数位置注入相应的形参

```java
@RequestMapping(value = "/quick20")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save20(HttpServletRequest request, HttpServletResponse response, HttpSession session) throws IOException {
    System.out.println(request);
    System.out.println(response);
    System.out.println(session);
}
```





##### 2.11 获得请求头

**1. @RequestHeader**

使用@RequestHeader可以获得请求头信息，相当于web阶段学习的request.getHeader(name)

@RequestHeader注解的属性如下：

- **value**：请求头的名称

- **required**：是否必须携带此请求头

```java
@RequestMapping(value = "/quick21")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save21(@RequestHeader(value = "User-Agent",required = false) String user_agent) throws IOException {
    System.out.println(user_agent);
}
```



**2. @CookieValue**

使用@CookieValue可以获得指定Cookie的值

@CookieValue注解的属性如下：

- **value**：指定cookie的名称

- **required**：是否必须携带此cookie

```java
@RequestMapping(value = "/quick22")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save22(@CookieValue(value = "JSESSIONID",required = false) String jsessionId) throws IOException {
    System.out.println(jsessionId);
}
```





##### 2.12 文件上传

**1. 文件上传客户端三要素**

- 表单项type=“file”

- 表单的提交方式是post

- 表单的enctype属性是多部分表单形式，及enctype=“multipart/form-data”

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="${pageContext.request.contextPath}/quick23" method="post" enctype="multipart/form-data">
        名称<input type="text" name="username"><br>
        文件<input type="file" name="upload"><br>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```



**2. 文件上传原理**

- 当form表单修改为多部分表单时，request.getParameter()将失效。

- enctype=“application/x-www-form-urlencoded”时，form表单的正文内容格式是：

**key=value&key=value&key=value**

- 当form表单的enctype取值为Mutilpart/form-data时，请求正文内容就变成多部分形式：



![image-20220114114203049](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220114114203049.png)



##### 2.13 单文件上传步骤

① 导入fileupload和io坐标

② 配置文件上传解析器

③ 编写文件上传代码



##### 2.14 单文件上传实现

① 导入fileupload和io坐标

```xml
<dependency>
  <groupId>commons-fileupload</groupId>
  <artifactId>commons-fileupload</artifactId>
  <version>1.3.1</version>
</dependency>
<dependency>
  <groupId>commons-io</groupId>
  <artifactId>commons-io</artifactId>
  <version>2.3</version>
</dependency>
```



② 配置文件上传解析器

```xml
<!--配置文件上传解析器-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!--编码       -->
    <property name="defaultEncoding" value="utf-8"></property>
    <!--上传文件总大小-->
    <property name="maxUploadSize" value="50000"></property>
</bean>
```



③ 编写文件上传代码

文件上传的文件，springMVC把文件封装成对象MultipartFile，对象名字要和form表单上传文件名（name)一致

```java
@RequestMapping(value = "/quick23")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save23(String username, MultipartFile uploadFile) throws IOException {
    System.out.println(username);
    //获得上传文件名称
    String filename = uploadFile.getOriginalFilename();
    //文件保存到 F://Download
    uploadFile.transferTo(new File("F:\\Download\\"+filename));
}
```



##### 2.15 多文件上传实现

多文件上传，只需要将页面修改为多个文件上传项

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="${pageContext.request.contextPath}/quick24" method="post" enctype="multipart/form-data">
        名称<input type="text" name="username"><br>
        文件<input type="file" name="uploadFile"><br>
        文件<input type="file" name="uploadFile2"><br>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```



```java
//多文件上传
@RequestMapping(value = "/quick24")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save24(String username, MultipartFile uploadFile,MultipartFile uploadFile2) throws IOException {
    System.out.println(username);
    //获得上传文件名称
    String filename = uploadFile.getOriginalFilename();
    //文件保存到 F://Download
    uploadFile.transferTo(new File("F:\\Download\\"+filename));
    //获得上传文件名称
    String filename2 = uploadFile2.getOriginalFilename();
    //文件保存到 F://Download
    uploadFile.transferTo(new File("F:\\Download\\"+filename2));
}
```



或者将方法参数MultipartFile类型修改为MultipartFile[]即可

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="${pageContext.request.contextPath}/quick25" method="post" enctype="multipart/form-data">
    名称<input type="text" name="username"><br>
    文件<input type="file" name="uploadFile"><br>
    文件<input type="file" name="uploadFile"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>

</body>
</html>
```

```java
//多文件上传
@RequestMapping(value = "/quick25")
@ResponseBody       // 告知SpringMVC框架，该方法不进行视图跳转 直接进行数据响应
public void save25(String username, MultipartFile[] uploadFile) throws IOException {
    System.out.println(username);
    for (MultipartFile multipartFile : uploadFile) {
        String filename = multipartFile.getOriginalFilename();
        System.out.println(filename);
        multipartFile.transferTo(new File("F:\\Download\\"+filename));
    }
}
```







# 五、JdbcTemplate

### 1. Spring JdbcTemplate基本使用



##### 1.1 JdbcTemplate概述

它是spring框架中提供的一个对象，是对原始繁琐的Jdbc API对象的简单封装。spring框架为我们提供了很多的操作

模板类。例如：操作关系型数据的JdbcTemplate和HibernateTemplate，操作nosql数据库的RedisTemplate，操

作消息队列的JmsTemplate等等。



##### 1.2 JdbcTemplate开发步骤

① 导入spring-jdbc和spring-tx坐标

② 创建数据库表和实体

③ 创建JdbcTemplate对象

④ 执行数据库操作



##### 1.3 JdbcTemplate快速入门

① 导入spring-jdbc和spring-tx坐标

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
```



② 创建数据库表和实体

```java
public class account {
    private String name;
    private double money;

    @Override
    public String toString() {
        return "account{" +
                "name='" + name + '\'' +
                ", money=" + money +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getMoney() {
        return money;
    }

    public void setMoney(double money) {
        this.money = money;
    }
}
```

![image-20220115123429813](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220115123429813.png)





③ 创建JdbcTemplate对象

④ 执行数据库操作

```java
public class JdbcTemplateTest {

    @Test
    // 测试JdbcTemplate开发步骤
    public void test1() throws PropertyVetoException {

        //创建数据源对象
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test?serverTimezone=UTC");
        dataSource.setUser("root");
        dataSource.setPassword("Xu990918");

        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        //设置数据源对象，知道数据库在哪
        jdbcTemplate.setDataSource(dataSource);
        //执行操作
        int row=jdbcTemplate.update("insert into account value(?,?)","xrj","5000");
        System.out.println(row);

    }
}
```



##### 1.4 Spring产生JdbcTemplate对象

我们可以将JdbcTemplate的创建权交给Spring，将数据源DataSource的创建权也交给Spring，在Spring容器内部将

数据源DataSource注入到JdbcTemplate模版对象中，配置如下：

```xml
<!--配置数据源对象-->
<bean id="dateSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTCr"/>
    <property name="user" value="root"/>
    <property name="password" value="Xu990918"/>
</bean>

<!--配置jdbc模板对象-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dateSource"/>
</bean>
```



从容器中获得JdbcTemplate进行添加操作

```java
@Test
// 测试spring产生JdbcTemplate对象
public void test2() throws PropertyVetoException {
    ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
    JdbcTemplate jdbcTemplate = app.getBean(JdbcTemplate.class);
    int row=jdbcTemplate.update("insert into account value(?,?)","xyy","8000");
    System.out.println(row);
}
```



**抽取配置文件，最终配置：**

jdbc.properties：

```xml
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC
jdbc.username=root
jdbc.password=Xu990918
```

```xml
<!--加载jdbc.properties-->
<context:property-placeholder location="classpath:jdbc.properties"/>


<!--配置数据源对象-->
<bean id="dateSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

<!--配置jdbc模板对象-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dateSource"/>
</bean>
```



##### 1.5 JdbcTemplate的常用操作

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class JdbcTemplateCRUDTest {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    // 修改操作
    @Test
    public void testUpdate(){
        int i = jdbcTemplate.update("update account set money=? where name =?", 10000, "xrj");
        System.out.println(i);
    }

    // 删除操作
    @Test
    public void testDelete(){
        int i = jdbcTemplate.update("delete from account where name =?",  "xyy1");
        System.out.println(i);
    }

    // 添加操作
    @Test
    public void testInsert(){
        int i = jdbcTemplate.update("insert into account value(?,?)","xyy1","8000");
        System.out.println(i);
    }

    // 查询多个操作
    @Test
    public void testQueryAll(){
        List<account> accountList = jdbcTemplate.query("select * from account", new BeanPropertyRowMapper<account>(account.class));
        System.out.println(accountList);
    }

    // 查询单个操作
    @Test
    public void testQueryOne(){
        account query = jdbcTemplate.queryForObject("select * from account where name=?", new BeanPropertyRowMapper<account>(account.class), "xrj");
        System.out.println(query);
    }

    // 聚合查询 查询总条数，返回结果不是javabean，是一个数字，因此不需要实体封装
    @Test
    public void testQueryCount(){
        Long query = jdbcTemplate.queryForObject("select count(*) from account", long.class);
        System.out.println(query);
    }
}

```





# 六、Spring练习



### 1. Spring练习环境搭建

##### 1.1 Spring环境搭建步骤

① 创建工程（Project&Module） 

② 导入静态页面（见资料jsp页面）

③ 导入需要坐标（见资料中的pom.xml） 

④ 创建包结构（controller、service、dao、domain、utils） 

⑤ 导入数据库脚本（见资料test.sql） 

⑥ 创建POJO类（见资料User.java和Role.java） 

⑦ 创建配置文件（applicationContext.xml、spring-mvc.xml、jdbc.properties、log4j.properties）



##### 1.2 角色列表的展示步骤分析

① 点击角色管理菜单发送请求到服务器端（修改角色管理菜单的url地址）

② 创建RoleController和showList()方法

③ 创建RoleService和showList()方法

④ 创建RoleDao和findAll()方法

⑤ 使用JdbcTemplate完成查询操作

⑥ 将查询数据存储到Model中 

⑦ 转发到role-list.jsp页面进行展示



##### 1.3 角色添加的步骤分析

① 点击列表页面新建按钮跳转到角色添加页面

② 输入角色信息，点击保存按钮，表单数据提交服务器

③ 编写RoleController的save()方法

④ 编写RoleService的save()方法

⑤ 编写RoleDao的save()方法

⑥ 使用JdbcTemplate保存Role数据到sys_role

⑦ 跳转回角色列表页面



##### **1.4 用户列表的展示步骤分析**

① 点击用户管理菜单发送请求到服务器端（修改用户管理菜单的url地址）

② 创建RoleController和showList()方法

③ 创建RoleService和showList()方法

④ 创建RoleDao和findAll()方法

⑤ 使用JdbcTemplate完成查询操作

⑥ 将查询数据存储到Model中 

⑦ 转发到user-list.jsp页面进行展示



##### **1.5 用户添加的步骤分析**

① 点击列表页面新建按钮跳转到角色添加页面

② 输入角色信息，点击保存按钮，表单数据提交服务器

③ 编写RoleController的save()方法

④ 编写RoleService的save()方法

⑤ 编写RoleDao的save()方法

⑥ 使用JdbcTemplate保存Role数据到sys_role

⑦ 跳转回角色列表页面



##### 1.6 **删除用户的步骤分析**

① 点击用户列表的删除按钮，发送请求到服务器端

② 编写UserController的deleteById()方法

③ 编写UserService的deleteById()方法

④ 编写UserDao的deleteById()方法

⑤ 编写UserDao的deleteRelByUid()方法

⑥ 跳回当前用户列表页面





# 七、SpringMVC拦截器

### 1.1 拦截器（interceptor）的作用

Spring MVC 的**拦截器**类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行**预处理**和**后处理**。

将拦截器按一定的顺序联结成一条链，这条链称为**拦截器链（Interceptor Chain）**。在访问被拦截的方

法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。拦截器也是AOP思想的具体实现。



### 1.2 拦截器和过滤器区别

**区别** 													**过滤器（Filter）	**								**拦截器（Interceptor）**



​										 是 servlet 规范中的一部分，任何							是 SpringMVC 框架自己的，只有使用了

**使用范围	**					  Java Web 工程都可以使用										SpringMVC 框架的工程才能用

​									



​										在 url-pattern 中配置了/*之后，							 在<mvc:mapping path=“”/>中配置了/**之

**拦截范围**						可以对所有要访问的资源拦截									后，也可以多所有资源进行拦截，但是可以通

​																															 过<mvc:exclude-mapping path=“”/>标签

​																															 排除不需要拦截的资源



### 1.3 拦截器快速入门

① 创建拦截器类实现HandlerInterceptor接口

```java
public class MyInterceptor1 implements HandlerInterceptor {

    //在目标方法执行之前 执行  返回true表示放行，返回false表示不放行
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
        System.out.println("preHandle.....");
        String param = request.getParameter("param");
        if("yes".equals(param)){
            return true;            //返回true表示放行
        }else {
            request.getRequestDispatcher("/error.jsp").forward(request,response);
            return false;           //返回false表示不放行
        }
    }

    //在目标方法执行之后 视图对象返回之前执行
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {

        modelAndView.addObject("name","xyy");
        System.out.println("postHandle...");
    }

    //在流程都执行完毕后 执行
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        System.out.println("afterCompletion....");
    }
}
```

② 配置拦截器

```xml
<!--配置拦截器-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--对哪些资源执行拦截操作-->
        <mvc:mapping path="/**"/>
        <bean class="com.xrj.interceptor.MyInterceptor1"/>
    </mvc:interceptor>
</mvc:interceptors>
```

③ 测试拦截器的拦截效果







# 八、SpringMVC异常处理机制

### 1.异常处理的思路

系统中异常包括两类：预期异常和运行时异常RuntimeException，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试等手段减少运行时异常的发生。 



系统的Dao、Service、Controller出现都通过throws Exception向上抛出，最后由SpringMVC前端控制器交 由异常处理器进行异常处理，如下图：

![image-20220119183414391](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220119183414391.png)





### 2.异常处理两种方式

- 使用Spring MVC提供的简单异常处理器SimpleMappingExceptionResolver 
- 实现Spring的异常处理接口HandlerExceptionResolver 自定义自己的异常处理器





### 3.简单异常处理器SimpleMappingExceptionResolver

SpringMVC已经定义好了该类型转换器，在使用时可以根据项目情况进行相应异常与视图的映射配置

```xml
<!--配置异常处理器-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <!--<property name="defaultErrorView" value="error"/>-->    <!-- 默认错误视图  -->
    <property name="exceptionMappings">
        <map>
            <!--key表示异常类型 value是跳转的错误视图-->
            <entry key="java.lang.ClassCastException" value="error1"/>
            <entry key="com.xrj.exception.MyException" value="error2"/>
        </map>
    </property>
</bean>
```



### 4.自定义异常处理步骤

① 创建异常处理器类实现HandlerExceptionResolver

```java
public class MyExceptionResolver implements HandlerExceptionResolver {
    @Override
   /*
        参数Exception 异常对象
        返回值 ModerAndView    跳转到错误视图信息
    */
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView modelAndView = new ModelAndView();

        if(e instanceof MyException){
            modelAndView.addObject("info","自定义异常");
        }else if (e instanceof ClassCastException){
            modelAndView.addObject("info","类转换异常");
        }

        modelAndView.setViewName("error");

        return modelAndView;
    }
}
```

② 配置异常处理器

```xml
<!--自定义异常处理器-->
<bean class="com.xrj.resolver.MyExceptionResolver"/>
```

③ 编写异常页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>自定义异常处理器</h1>
</body>
</html>
```

④ 测试异常跳转









# 九、面向切面编程AOP



### 1.Spring的Aop简介

##### 1.1什么是Aop

AOP 为 Aspect Oriented Programming 的缩写，意思为面向切面编程，是通过预编译方式和运行期动态代理 实现程序功能的统一维护的一种技术。

 AOP 是 OOP 的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率



##### 1.2 Aop的作用及其优势

- 作用：在程序运行期间，在不修改源码的情况下对方法进行功能增强 
- 优势：减少重复代码，提高开发效率，并且便于维护

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220119210255506.png" alt="image-20220119210255506" style="zoom:80%;" />



##### 1.3 AOP 的底层实现 

实际上，AOP 的底层是通过 Spring 提供的的动态代理技术实现的。在运行期间，Spring通过动态代理技术动态 的生成代理对象，代理对象方法执行时进行增强功能的介入，在去调用目标对象的方法，从而完成功能的增强



##### 1.4 AOP 的动态代理技术 

常用的动态代理技术 （不需要会写，看懂就行，spring直接配置）

- JDK 代理 : 基于接口的动态代理技术
- cglib 代理：基于父类的动态代理技术

![image-20220119210508928](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220119210508928.png)

##### 1.5 JDK的动态代理

① 目标类接口

```java
public interface TargetInterface {

    public void save();
}
```



② 目标类

```java
public class Target implements TargetInterface {
    @Override
    public void save() {
        System.out.println("save running....");
    }
}
```



③增强方法

```java
public class Advice {

    public void before(){
        System.out.println("前置增强");
    }

    public void afterReturn(){
        System.out.println("后置增强");
    }
}
```



④ 动态代理代码

```java
public class ProxyTest {

  public static void main(String[] args) {

      //创建目标对象
      final Target target = new Target(); //目标对象

      //增强对象
      final Advice advice=new Advice();

    // 返回值就是动态生成的代理对象   目标对象和代理对象是兄弟关系，用父类接收
    TargetInterface proxy =(TargetInterface)Proxy.newProxyInstance(
            target.getClass().getClassLoader(),  //目标对象类加载器
            target.getClass().getInterfaces(),      // 目标对象相同的接口字节码对象数组
            new InvocationHandler() {
              @Override
              // 调用代理对象的任何方法，实质执行的都是invoke方法
              // proxy代理对象  method要执行的目标方法的方法字节码对象
              public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //前值增强
                advice.before();

                //执行目标对象
                Object invoke = method.invoke(target, args);

                  //后置增强
                advice.afterReturn();

                return invoke;
              }
            });

    //调用代理对象的方法
    proxy.save();

  }
}
```





##### 1.6 cglib 的动态代理

动态代理代码：

```java
public class ProxyTest {

  public static void main(final String[] args) {

      //创建目标对象
      final Target target = new Target(); //目标对象

      //增强对象
      final Advice advice=new Advice();

      // 返回值就是动态生成的代理对象   目标对象和代理对象是兄弟关系，用父类接收
      // 基于cjlib

      //1.创建增强器 cjlib内部提供
      Enhancer enhancer=new Enhancer();
      //2.设置父类（目标）
      enhancer.setSuperclass(Target.class);
      //3.设置回调
      enhancer.setCallback(new MethodInterceptor() {
          @Override
          public Object intercept(Object proxy, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
              //执行前置
              advice.before();
              //执行目标
              Object invoke = method.invoke(target, args);
              //执行后置
              advice.afterReturn();
              return invoke;
          }
      });
      //4.创建代理对象    基于父类的代理对象，生成的是 子，可以用父来接
      Target proxy = (Target) enhancer.create();
      proxy.save();

  }
}
```





##### 1.7 AOP 相关概念

 Spring 的 AOP 实现底层就是对上面的动态代理的代码进行了封装，封装后我们只需要对需要关注的部分进行代码编 写，并通过配置的方式完成指定目标的方法增强。 

在正式讲解 AOP 的操作之前，我们必须理解 AOP 的相关术语，常用的术语如下：

- Target（目标对象）：代理的目标对象 
- Proxy （代理）：一个类被 AOP 织入增强后，就产生一个结果代理类 
- Joinpoint（连接点）：所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法，因为spring只支持方 法类型的连接点 （可以被增强的方法）
- Pointcut（切入点）：所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义 。（可以配置被增强的方法）
- Advice（通知/ 增强）：所谓通知是指拦截到 Joinpoint 之后所要做的事情就是通知。（增强的方法）
- Aspect（切面）：是切入点和通知（引介）的结合 
-  Weaving（织入）：是指把增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织入，而 AspectJ采用编译期织入和类装载期织入。（将切点和通知结合的过程）



##### 1.8 AOP 开发明确的事项 

1.需要编写的内容 

- 编写核心业务代码（目标类的目标方法） （被增强的方法）
- 编写切面类，切面类中有通知(增强功能方法) 
- 在配置文件中，配置织入关系，即将哪些通知与哪些连接点进行结合 



2.AOP 技术实现的内容 

Spring 框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的 代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。



3.AOP 底层使用哪种代理方式

 在 spring 中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式





### 2.基于 XML 的 AOP 开发

##### 2.1 快速入门 

① 导入 AOP 相关坐标

② 创建目标接口和目标类（内部有切点） 

③ 创建切面类（内部有增强方法） 

④ 将目标类和切面类的对象创建权交给 spring 

⑤ 在 applicationContext.xml 中配置织入关系 

⑥ 测试代码





① 导入 AOP 相关坐标

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.2.9.RELEASE</version>
</dependency>
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.9.6</version>
</dependency>
```

② 创建目标接口和目标类（内部有切点） 

```java
public interface TargetInterface {

    public void save();
}
```

```java
public class Target implements TargetInterface {
    @Override
    public void save() {
        System.out.println("save running....");
    }
}
```

③ 创建切面类（内部有增强方法） 

```java
public class MyAspect {

    public void before(){
        System.out.println("前置增强");
    }
}
```

④ 将目标类和切面类的对象创建权交给 spring 

```xml
<!--目标对象-->
<bean id="target" class="com.xrj.aop.Target"></bean>

<!--切面对象-->
<bean id="myAspect" class="com.xrj.aop.MyAspect"></bean>
```

⑤ 在 applicationContext.xml 中配置织入关系 

```xml
<!--配置织入，告诉spring框架，哪些方法（切点）需要进行哪些增强（前置、后置..）,先引入aop命名空间-->
<aop:config>
    <!--声明切面-->
    <aop:aspect ref="myAspect">
        <!--切面：切点+通知-->
        <!--当访问save方法时，会先调用before方法进行前置增强-->
        <aop:before method="before" pointcut="execution(public void com.xrj.aop.Target.save())"></aop:before>
    </aop:aspect>
</aop:config>
```

⑥ 测试代码

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AopTest {

    @Autowired
    private TargetInterface target;

    @Test
    public void test1(){
        target.save();
    }


}
```

输出：	前置增强
				save running....



##### 2.2 XML 配置 AOP 详解

1.切点表达式的写法

表达式语法：

```java
execution([修饰符] 返回值类型 包名.类名.方法名(参数)）	// [修饰符]表示可写可不写
```

- 访问修饰符可以省略

- 返回值类型、包名、类名、方法名可以使用星号* 代表任意 
- 包名与类名之间一个点 . 代表当前包下的类，两个点 .. 表示当前包及其子包下的类 
- 参数列表可以使用两个点 .. 表示任意个数，任意类型的参数列表



例如：

```xml
execution(public void com.xrj.aop.Target.save())	 <!-- 表示com.xrj.aop包下的Target类的save方法 -->
execution(public void com.xrj.aop.Target.*(..))	 <!-- 表示com.xrj.aop包下的Target类的任意方法，任意参数 -->
execution(* com.xrj.aop.*.*(..))	 <!-- 表示返回值任意，com.xrj.aop包下的任意类的任意方法，任意参数 -->  最常用
execution(* com.xrj.aop..*.*(..))	 <!-- 表示返回值任意，com.xrj.aop包及其子包下的任意类的任意方法，任意参数 -->
execution(* *..*.*(..))	 <!-- 表示返回值任意，任意包及其任意子下的任意类的任意方法，任意参数 -->
```





2.通知的类型

通知的配置语法：

```java
<aop:通知类型 method=“切面类中方法名” pointcut=“切点表达式"></aop:通知类型>
```

![image-20220120115923667](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220120115923667.png)



3.切点表达式的抽取

当多个增强的切点表达式相同时，可以将切点表达式进行抽取，在增强中使用 pointcut-ref 属性代替 pointcut 属性来引用抽 取后的切点表达式。

```xml
<!--配置织入，告诉spring框架，哪些方法（切点）需要进行哪些增强（前置、后置..）,先引入aop命名空间-->
<aop:config>
    <!--声明切面-->
    <aop:aspect ref="myAspect">
        <!--抽取切点表达式-->
        <!--切面：切点+通知-->
        <aop:pointcut id="myPointcut" expression="execution(* com.xrj.aop.*.*(..))"/>
       
        <aop:around method="around" pointcut-ref="myPointcut"></aop:around>
        <aop:after method="after" pointcut-ref="myPointcut"/>
    </aop:aspect>
</aop:config>
```





### 3.基于注解的 AOP 开发

##### 3.1 快速入门 

基于注解的aop开发步骤： 

① 创建目标接口和目标类（内部有切点） 

② 创建切面类（内部有增强方法） 

③ 将目标类和切面类的对象创建权交给 spring 

④ 在切面类中使用注解配置织入关系 

⑤ 在配置文件中开启组件扫描和 AOP 的自动代理 

⑥ 测试



① 创建目标接口和目标类（内部有切点） 

```java
public interface TargetInterface {

    public void save();
}
```

```java
@Component("target")
public class Target implements TargetInterface {
    @Override
    public void save() {
        // int i=1/0;
        System.out.println("save running....");
    }
}
```

② 创建切面类（内部有增强方法）

```java
@Component("myAspect")
@Aspect //标注当前MyAspect是一个切面类
public class MyAspect {

    //配置前置增强
    @Before("execution(* com.xrj.anno.*.*(..))")
    public void before(){
        System.out.println("前置增强");
    }

    public void afterReturnning(){
        System.out.println("后置增强");
    }

    //Proceeding JoinPoint  :    正在执行的连接点==切点
    public Object around(ProceedingJoinPoint pjd) throws Throwable {
        System.out.println("环绕前增强");
        //切点方法
        Object proceed = pjd.proceed();
        System.out.println("环绕后增强");
        return proceed;
    }

    public void afterThrowing(){
        System.out.println("异常抛出增强...");
    }

    public void after(){
        System.out.println("最终增强...");
    }
}
```

③ 将目标类和切面类的对象创建权交给 spring 

④ 在切面类中使用注解配置织入关系 

⑤ 在配置文件中开启组件扫描和 AOP 的自动代理 

```xml
<!--开启组件扫描-->
<context:component-scan base-package="com.xrj.anno"/>

<!--aop自动代理-->
<acp:aspectj-autoproxy/>
```

⑥ 测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext-anno.xml")
public class AnnoTest {

    @Autowired
    private TargetInterface target;

    @Test
    public void test1(){
        target.save();
    }

}
```



##### 3.2 注解配置 AOP 详解

1.通知的配置语法：@通知注解(“切点表达式")

![image-20220120144138486](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220120144138486.png)



2.切点表达式的抽取

同 xml 配置 aop 一样，我们可以将切点表达式抽取。抽取方式是在切面内定义方法，在该方法上使用@Pointcut 注解定义切点表达式，然后在在增强注解中进行引用。具体如下：

```java
@Component("myAspect")
@Aspect //标注当前MyAspect是一个切面类
public class MyAspect {

    //配置前置增强
   // @Before("execution(* com.xrj.anno.*.*(..))")
    public void before(){
        System.out.println("前置增强");
    }

    public void afterReturnning(){
        System.out.println("后置增强");
    }

    //Proceeding JoinPoint  :    正在执行的连接点==切点
    @Around("pointcut()")
    public Object around(ProceedingJoinPoint pjd) throws Throwable {
        System.out.println("环绕前增强");
        //切点方法
        Object proceed = pjd.proceed();
        System.out.println("环绕后增强");
        return proceed;
    }

    public void afterThrowing(){
        System.out.println("异常抛出增强...");
    }

    @After("MyAspect.pointcut()")
    public void after(){
        System.out.println("最终增强...");
    }

    // 定义一个切点表达式
    @Pointcut("execution(* com.xrj.anno.*.*(..))")
    public void pointcut(){}
}
```





# 十、声明式事务控制



### 1.基于 XML 的声明式事务控制



**声明式事务控制的配置要点**

- 平台事务管理器配置

- 事务通知的配置

- 事务aop织入的配置





##### 1.1 什么是声明式事务控制

Spring 的声明式事务顾名思义就是采用声明的方式来处理事务。这里所说的声明，就是指在配置文件中声明

，用在 Spring 配置文件中声明式的处理事务来代替代码式的处理事务。



##### 声明式事务处理的作用

- 事务管理不侵入开发的组件。具体来说，业务逻辑对象就不会意识到正在事务管理之中，事实上也应该如

此，因为事务管理是属于系统层面的服务，而不是业务逻辑的一部分，如果想要改变事务管理策划的话，

也只需要在定义文件中重新配置即可

- 在不需要事务管理的时候，只要在设定文件上修改一下，即可移去事务管理服务，无需改变代码重新编译

，这样维护起来极其方便

**注意**：Spring 声明式事务控制底层就是AOP。





##### 1.2 声明式事务控制的实现

声明式事务控制明确事项：

- 谁是切点？		业务方法（ 也是就转账方法）

- 谁是通知？        事务控制功能

- 配置切面？        



① 引入tx命名空间

```xml
xmlns:tx="http://www.springframework.org/schema/tx"
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd         
```



② 配置事务增强

```xml
<!--配置一个平台事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dateSource"/>
</bean>

<!--通知  事物的增强 -->
<!--transaction-manager=平台事务管理器-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="*"/>
    </tx:attributes>
</tx:advice>
```



③ 配置事务 AOP 织入

```xml
<!--配置Aop的事物织入-->
<aop:config>
    <!--事物增强用advisor-->
    <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.xrj.service.impl.*.*(..))"></aop:advisor>
</aop:config>
```



④ 测试事务控制转账业务代码

```java
@Override
public void transfer(String outMan, String inMan, double money) {
    // 现在转出和转入是两个事物，如果执行完out报错，in就无法执行，就出错了，因此要变为一个事物
    accountDao.out(outMan,money);
    int i=1/0;
    accountDao.in(inMan,money);
}
```

报错之后钱不会改变



##### 1.3 切点方法的事务参数的配置

```xml
<!--通知  事物的增强 -->
<!--transaction-manager=平台事务管理器-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--设置事物的属性信息-->
    <tx:attributes>
        <!--method代表哪些方法被增强  * 代表任何方法-->
        <!--isolation隔离级别，propagation传播行为，timeout失效时间，read-only是否只读-->
        <tx:method name="transfer" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
        <tx:method name="save" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>
        <tx:method name="findAll" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true"/>
        <tx:method name="*"/>
    </tx:attributes>
</tx:advice>
```

- name：切点方法名称

- isolation:事务的隔离级别

- propogation：事务的传播行为

- timeout：超时时间

- read-only：是否只读



### 2. 基于注解的声明式事务控制

##### 2.1 使用注解配置声明式事务控制

1. 编写 AccoutDao

   ```java
   @Repository("accountDao")
   public class AccountDaoImpl implements AccountDao {
   
       @Autowired
       private JdbcTemplate jdbcTemplate;
   
       @Override
       public void out(String outMan,double money) {
           jdbcTemplate.update("update account set money=money-? where name =?",money,outMan);
       }
   
       @Override
       public void in(String inMan,double money) {
           jdbcTemplate.update("update account set money=money+? where name =?",money,inMan);
       }
   }
   ```



2. 编写 AccoutService

   ```java
   @Service("accountService")
   // @Transactional(isolation = Isolation.READ_COMMITTED,propagation = Propagation.REQUIRED)
   public class AccountServiceImpl implements AccountService {
   
       @Autowired
       private AccountDao accountDao;
   
       @Transactional(isolation = Isolation.READ_COMMITTED,propagation = Propagation.REQUIRED)
       @Override
       public void transfer(String outMan, String inMan, double money) {
           // 现在转出和转入是两个事物，如果执行完out报错，in就无法执行，就出错了，因此要变为一个事物
           accountDao.out(outMan,money);
           int i=1/0;
           accountDao.in(inMan,money);
       }
   }
   ```



3. 编写 applicationContext.xml 配置文件

```xml
<!--组件扫描-->
<context:component-scan base-package="com.xrj"/>

<context:property-placeholder location="jdbc.properties"/>

<bean id="dateSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dateSource"></property>
</bean>

<!--配置一个平台事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dateSource"/>
</bean>

<!--事物注解驱动-->
<tx:annotation-driven transaction-manager="transactionManager"/>
```



##### 2.2 注解配置声明式事务控制解析

① 使用 @Transactional 在需要进行事务控制的类或是方法上修饰，注解可用的属性同 xml 配置方式，例如隔离

级别、传播行为等。

② 注解使用在类上，那么该类下的所有方法都使用同一套注解参数配置。

③ 使用在方法上，不同的方法可以采用不同的事务参数配置。

④ Xml配置文件中要开启事务的注解驱动<tx:annotation-driven />







# 十一、MyBatis入门操作



### 1.Mybatis简介

##### 1.1 原始jdbc操作的分析

原始jdbc开发存在的问题如下：

① 数据库连接创建、释放频繁造成系统资源浪费从而影响系统性能

② sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变java代码。

③ 查询操作时，需要手动将结果集中的数据手动封装到实体中。插入操作时，需要手动将实体的数据设置到sql语句的占位

符位置

应对上述问题给出的解决方案：

① 使用数据库连接池初始化连接资源

② 将sql语句抽取到xml配置文件中

③ 使用反射、内省等底层技术，自动将实体与表进行属性与字段的自动映射



##### 1.2 什么是Mybatis

- mybatis 是一个优秀的基于java的持久层框架，它内部封装了

​		jdbc，使开发者只需要关注sql语句本身，而不需要花费精力

​		去处理加载驱动、创建连接、创建statement等繁杂的过程。

- mybatis通过xml或注解的方式将要执行的各种 statement配

​		置起来，并通过java对象和statement中sql的动态参数进行

​		映射生成最终执行的sql语句。

- 最后mybatis框架执行sql并将结果映射为java对象并返回。采

​		用ORM思想解决了实体和数据库映射的问题，对jdbc 进行了

​		封装，屏蔽了jdbc api 底层访问细节，使我们不用与jdbc api

​		打交道，就可以完成对数据库的持久化操作。





### 2. Mybatis的快速入门

##### 2.1 MyBatis开发步骤

MyBatis开发步骤：

① 添加MyBatis的坐标

② 创建user数据表

③ 编写User实体类

④ 编写映射文件UserMapper.xml

⑤ 编写核心文件SqlMapConfig.xml

⑥ 编写测试类



##### 2.2 环境搭建

① 添加MyBatis的坐标

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.20</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
```



② 创建user数据表

![image-20220122115041455](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220122115041455.png)



③ 编写User实体类

```java
public class User {

  private int id;
  private String username;
  private String password;

  @Override
  public String toString() {
    return "User{" +
            "id=" + id +
            ", username='" + username + '\'' +
            ", password='" + password + '\'' +
            '}';
  }

  public int getId() {
    return id;
  }

  public void setId(int id) {
    this.id = id;
  }

  public String getUsername() {
    return username;
  }

  public void setUsername(String username) {
    this.username = username;
  }

  public String getPassword() {
    return password;
  }

  public void setPassword(String password) {
    this.password = password;
  }
}
```



④ 编写映射文件UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="userMapper">
    <!--通过userMapper.findAll找到查询方法，将结果集封装为User对象-->
    <select id="findAll" resultType="com.xrj.domain.User">
        select * from user
    </select>
</mapper>
```



⑤ 编写核心文件SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--配数据源环境-->
    <environments default="developement">   <!-- 默认环境 -->
        <environment id="developement">
            <transactionManager type="JDBC"></transactionManager>   <!-- 事务管理器 -->
            <dataSource type="POOLED">   <!-- 数据源类型，池化 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="Xu990918"/>
            </dataSource>
        </environment>
    </environments>

    <!--加载映射文件-->
    <mappers>
        <mapper resource="com/xrj/mapper/UserMapper.xml"></mapper>
    </mappers>


</configuration>
```



⑥ 编写测试类

```java
public class MyBatisTest {

    @Test
    public void test1() throws IOException {
        // 获得核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
        // 获得session工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获得session会话对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 执行操作  参数就是namespace+id
        List<User> userList = sqlSession.selectList("userMapper.findAll");
        // 打印数据
        System.out.println(userList);
        // 释放资源
        sqlSession.close();
    }
}
```

输出：

```
[User{id=1, username='xrj', password='123456'}, User{id=2, username='xyy', password='123'}, User{id=3, username='xrj1', password='123456'}, User{id=4, username='xyy1', password='123'}]

```





##### 2.3 MyBatis的映射文件概述

![image-20220122121301987](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220122121301987.png)



##### 2.4 MyBatis的增删改查操作

###### 插入：

1. 编写UserMapper映射文件

```xml
<mapper namespace="userMapper">
	<!--parameterType参数类型-->
	<insert id="save" parameterType="com.xrj.domain.User">
    	insert into user values(#{id},#{username},#{password})
	</insert>
</mapper>
```



2. 编写插入实体User的代码

```java
public void test2() throws IOException {

    // 模拟user对象
    User user = new User();
    user.setUsername("tom");
    user.setPassword("abc");

    // 获得核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    // 获得session工厂对象
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    // 获得session会话对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    // 执行操作  参数就是namespace+id
    sqlSession.insert("userMapper.save", user);

    //mybatis执行更新操作，需要执行事物，mybatis默认不执行事物
    sqlSession.commit();

    // 释放资源
    sqlSession.close();
}
```



3. 插入操作注意问题

- 插入语句使用insert标签

- 在映射文件中使用parameterType属性指定要插入的数据类型

- Sql语句中使用#{实体属性名}方式引用实体中的属性值

- 插入操作使用的API是sqlSession.insert(“命名空间.id”,实体对象);

- 插入操作涉及数据库数据变化，所以要使用sqlSession对象显示的提交事务，即sqlSession.commit() 



###### 更新：

1. 编写UserMapper映射文件

```xml
<mapper namespace="userMapper">
    <!--修改-->
    <update id="update" parameterType="com.xrj.domain.User">
        update user set username=#{username},password=#{password} where id =#{id}
    </update>
</mapper>
```



2. 编写修改实体User的代码

```java
// 更新操作
@Test
public void test3() throws IOException {

    // 模拟user对象
    User user = new User();
    user.setId(5);
    user.setUsername("lucy");
    user.setPassword("123");

    // 获得核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    // 获得session工厂对象
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    // 获得session会话对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    // 执行操作  参数就是namespace+id
    sqlSession.update("userMapper.update",user);

    //mybatis执行更新操作，需要执行事物，mybatis默认不执行事物
    sqlSession.commit();

    // 释放资源
    sqlSession.close();
}
```



3. 修改操作注意问题

• 修改语句使用update标签

• 修改操作使用的API是sqlSession.update(“命名空间.id”,实体对象);





###### 删除：

1. 编写UserMapper映射文件

   ```xml
   <mapper namespace="userMapper">
       <!--删除-->
       <delete id="delete" parameterType="java.lang.Integer">
           delete from user where id=#{id}
       </delete>
   </mapper>
   ```



2. 编写删除数据的代码

   ```java
   // 删除操作
   @Test
   public void test4() throws IOException {
   
       // 获得核心配置文件
       InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
       // 获得session工厂对象
       SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
       // 获得session会话对象
       SqlSession sqlSession = sqlSessionFactory.openSession();
       // 执行操作  参数就是namespace+id
       sqlSession.delete("userMapper.delete",4);
   
       //mybatis执行更新操作，需要执行事物，mybatis默认不执行事物
       sqlSession.commit();
   
       // 释放资源
       sqlSession.close();
   }
   ```



3. 删除操作注意问题

- 删除语句使用delete标签
- Sql语句中使用#{任意字符串}方式引用传递的单个参数
- 删除操作使用的API是sqlSession.delete(“命名空间.id”,Object);



### 3.MyBatis的核心配置文件概述

##### 3.1MyBatis核心配置文件层级关系

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220123102528180.png" alt="image-20220123102528180" style="zoom:80%;" />



##### 3.2 MyBatis常用配置解析

###### 1. environments标签

数据库环境的配置，支持多环境配置![image-20220123102616860](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220123102616860.png)

其中，事务管理器（transactionManager）类型有两种：

- JDBC：这个配置就是直接使用了JDBC 的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。

- MANAGED：这个配置几乎没做什么。它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如JEE 

​		应用服务器的上下文）。 默认情况下它会关闭连接，然而一些容器并不希望这样，因此需要将 closeConnection 属性设置

​		为 false 来阻止它默认的关闭行为。



其中，数据源（dataSource）类型有三种：

- UNPOOLED：这个数据源的实现只是每次被请求时打开和关闭连接。

- POOLED：这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来。

- JNDI：这个数据源的实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置

​		一个 JNDI 上下文的引用





###### 2. mapper标签

该标签的作用是加载映射的，加载方式有如下几种：

- 使用相对于类路径的资源引用，例如：<mapper resource="org/mybatis/builder/AuthorMapper.xml"/>

- 使用完全限定资源定位符（URL），例如：<mapper url="file:///var/mappers/AuthorMapper.xml"/>      （常用）

- 使用映射器接口实现类的完全限定类名，例如：<mapper class="org.mybatis.builder.AuthorMapper"/>

- 将包内的映射器接口实现全部注册为映射器，例如：<package name="org.mybatis.builder"/>                         （扫包）





###### 3. Properties标签

实际开发中，习惯将数据源的配置信息单独抽取成一个properties文件，该标签可以加载额外配置的properties文件

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220123103404102.png" alt="image-20220123103404102" style="zoom:80%;" />



###### 4. typeAliases标签

类型别名是为Java 类型设置一个短的名字。原来的类型名称配置如下

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220123103556209.png" alt="image-20220123103556209" style="zoom:67%;" />



配置typeAliases（放在properties标签下边），为com.xrj.domain.User定义别名为user

```xml
<typeAliases>
    <typeAlias type="com.xrj.domain.User" alias="user"></typeAlias>
</typeAliases>
```

```xml
<select id="findAll" resultType="user">
    select * from user
</select>
```



### 4.MyBatis相应API



##### 4.1 SqlSession工厂构建器SqlSessionFactoryBuilder

常用API：SqlSessionFactory build(InputStream inputStream)

通过加载mybatis的核心文件的输入流的形式构建一个SqlSessionFactory对象

```java
// 获得核心配置文件
InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
// 获得session工厂对象
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
```

其中， Resources 工具类，这个类在 org.apache.ibatis.io 包中。Resources 类帮助你从类路径下、文件系统或

一个 web URL 中加载资源文件



##### 4.2 SqlSession工厂对象SqlSessionFactory

SqlSessionFactory 有多个个方法创建 SqlSession 实例。常用的有如下两个：

- openSession() 															会默认开启一个事务，但事务不会自动提交，也就意味着需要手动提交该事务，更																				      新操作数据才会持久化到数据库中
- openSession(booleanautoCommit)                        参数为是否自动提交，如果设置为true，那么不需要手动提交事务





##### 4.3 SqlSession会话对象

SqlSession 实例在 MyBatis 中是非常强大的一个类。在这里你会看到所有执行语句、提交或回滚事务和获取映射器实例的方法。

执行语句的方法主要有：

```java
<T> T selectOne(String statement, Object parameter)
<E> List<E> selectList(String statement, Object parameter)
int insert(String statement, Object parameter)
int update(String statement, Object parameter)
int delete(String statement, Object parameter)
```



操作事务的方法主要有：

```java
void commit()
void rollback()
```







# 十二、MyBatis的Dao层实现方式



### 1 传统开发方式

1.编写dao层

```java
public interface UserDao {
    public List<User> findAll() throws IOException;
}
```

```java
public class UserDaoImpl implements UserDao {
    @Override
    public List<User> findAll() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        List<User> userList = sqlSession.selectList("userMapper.findAll");
        return userList;
    }
}
```



2.service层测试

```java
public class ServiceDemo {

  public static void main(String[] args) throws IOException {

    //创建dao层对象  当前dao层实现是手动创建
    UserDaoImpl userDao = new UserDaoImpl();
    List<User> all=userDao.findAll();
    System.out.println(all);
  }
}
```



### 2 代理开发方式

**1. 代理开发方式介绍**

采用 Mybatis 的代理开发方式实现 DAO 层的开发，这种方式是我们后面进入企业的主流。

Mapper 接口开发方法只需要程序员编写Mapper 接口（相当于Dao 接口），由Mybatis 框架根据接口定义创建接

口的动态代理对象（接口的实现），代理对象的方法体同上边Dao接口实现类方法。

Mapper 接口开发需要遵循以下规范：

1、 Mapper.xml文件中的namespace与mapper接口的全限定名相同

2、 Mapper接口方法名和Mapper.xml中定义的每个statement的id相同

3、 Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql的parameterType的类型相同

4、 Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同



**2. 编写UserMapper接口**

![image-20220124105827989](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220124105827989.png)



**3. 测试代理方式**

```java
public static void main(String[] args) throws IOException {

  InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
  SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
  SqlSession sqlSession = sessionFactory.openSession();

  // 获得代理对象
  UserMapper mapper = sqlSession.getMapper(UserMapper.class);
  List<User> all = mapper.findAll();
  System.out.println(all);

}
```







# 十三、MyBatis映射文件深入



### 1.动态sql语句



##### 1. 动态sql语句概述

Mybatis 的映射文件中，前面我们的 SQL 都是比较简单的，有些时候业务逻辑复杂时，我们的 SQL是动态变化的，

此时在前面的学习中我们的 SQL 就不能满足要求了。





##### 2. 动态 SQL 之 < if > 

我们根据实体类的不同取值，使用不同的 SQL语句来进行查询。比如在 id如果不为空时可以根据id查询，如果

username 不同空时还要加入用户名作为条件。这种情况在我们的多条件组合查询中经常会碰到。

```xml
<select id="findByCondition" parameterType="user" resultType="user">
    select * from user
    <where>
        <if test="id!=0">
            and id =#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
        <if test="password!=null">
            and password=#{password}
        </if>
    </where>
</select>
```



当查询条件id和username都存在时，控制台打印的sql语句如下：

```java
@Test
public void test1() throws IOException {

    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sessionFactory.openSession();

    // 获得代理对象
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    // 模拟条件user
    User condition=new User();
    condition.setId(1);
    condition.setUsername("xrj");
    // condition.setPassword("123456");

    List<User> userList = mapper.findByCondition(condition);
    System.out.println(userList);
}

// 输出[User{id=1, username='xrj', password='123456'}]
```



当查询条件只有password存在时，控制台打印的sql语句如下：

```java
@Test
public void test1() throws IOException {

    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sessionFactory.openSession();

    // 获得代理对象
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    // 模拟条件user
    User condition=new User();
    // condition.setId(1);
    // condition.setUsername("xrj");
    condition.setPassword("123456");

    List<User> userList = mapper.findByCondition(condition);
    System.out.println(userList);
}

// 输出：[User{id=1, username='xrj', password='123456'}, User{id=3, username='xrj1', password='123456'}]
```



##### 3.动态 SQL 之< foreach >

循环执行sql的拼接操作，例如：select * from user where id in (1,2,5)。 

```xml
<select id="findByIds" parameterType="list" resultType="user">
    select * from user
    <where>
       	<!--open,以id in(开始，以)结束，遍历元素为id，separator为分隔符-->
        <foreach collection="list" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
```



测试代码

```java
@Test
public void test1() throws IOException {

    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sessionFactory.openSession();

    // 获得代理对象
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    // 模拟ids的数据
    List<Integer> ids=new ArrayList<>();
    ids.add(1);
    ids.add(2);
    ids.add(3);

    List<User> byIds = mapper.findByIds(ids);

    System.out.println(byIds);
}

// 输出：[User{id=1, username='xrj', password='123456'}, User{id=2, username='xyy', password='123'}, User{id=3, username='xrj1', password='123456'}]

```



foreach标签的属性含义如下：

<foreach>标签用于遍历集合，它的属性：

- collection：代表要遍历的集合元素，注意编写时不要写#{}

- open：代表语句的开始部分

- close：代表结束部分

- item：代表遍历集合的每个元素，生成的变量名

- separator：代表分隔符



###### SQL片段抽取

Sql 中可将重复的 sql 提取出来，使用时用 include 引用即可，最终达到 sql 重用的目的

```xml
<!--sql语句的抽取-->
<sql id="selectUser">
    select * from user
</sql>

<select id="findByCondition" parameterType="user" resultType="user">
    <include refid="selectUser"></include>
    <where>
        <if test="id!=0">
            and id =#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
        <if test="password!=null">
            and password=#{password}
        </if>
    </where>
</select>
```





# 十四、MyBatis核心配置文件深入



### 1. typeHandlers标签

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用

类型处理器将获取的值以合适的方式转换成 Java 类型。

 



你可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。具体做法为：实现

org.apache.ibatis.type.TypeHandler 接口， 或继承一个很便利的类 org.apache.ibatis.type.BaseTypeHandler， 然

后可以选择性地将它映射到一个JDBC类型。例如需求：一个Java中的Date数据类型，我想将之存到数据库的时候存成一

个1970年至今的毫秒数，取出来时转换成java的Date，即java的Date与数据库的varchar毫秒值之间转换。



开发步骤：

① 定义转换类继承类BaseTypeHandler<T>

② 覆盖4个未实现的方法，其中setNonNullParameter为java程序设置数据到数据库的回调方法，getNullableResult

为查询时 mysql的字符串类型转换成 java的Type类型的方法

③ 在MyBatis核心配置文件中进行注册

④ 测试转换是否正确





① 定义转换类继承类BaseTypeHandler<T>

② 覆盖4个未实现的方法

```java
public class DateTypeHandler extends BaseTypeHandler<Date> {

    @Override
    // 将java类型转换成数据库需要的类型
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Date date, JdbcType jdbcType) throws SQLException {
        // i是参数的位置
        long time = date.getTime(); // date转为毫秒值
        preparedStatement.setLong(i,time);
    }

    @Override
    // 将数据库中类型转换成java类型
    // String参数 是要转换的字段名称
    // ResultSet 查询出的结果集
    public Date getNullableResult(ResultSet resultSet, String s) throws SQLException {
        // 获取结果集中需要的数据（long)转换成date类型，并返回
        long aLong = resultSet.getLong(s);
        Date date=new Date(aLong);
        return date;
    }

    @Override
    // 将数据库中类型转换成java类型
    public Date getNullableResult(ResultSet resultSet, int i) throws SQLException {
        // i 是字段位置
        long aLong = resultSet.getLong(i);
        Date date=new Date(aLong);
        return date;
    }

    @Override
    // 将数据库中类型转换成java类型
    public Date getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        long aLong = callableStatement.getLong(i);
        Date date=new Date(aLong);
        return date;
    }
}
```



③ 在MyBatis核心配置文件中进行注册

```xml
<!--注册类型处理器-->
<typeHandlers>
    <typeHandler handler="com.xrj.handler.DateTypeHandler"/>
</typeHandlers>
```



④ 测试转换是否正确

```java
@Test
public void test1() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sessionFactory.openSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);


    //创建user
    User user = new User();
    user.setUsername("xyyyyyy");
    user.setPassword("abc");
    user.setBirthday(new Date());
    //执行保存操作
    mapper.save(user);

    sqlSession.commit();
    sqlSession.close();
}

  @Test
  public void test2() throws IOException {
      InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
      SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
      SqlSession sqlSession = sessionFactory.openSession();
      UserMapper mapper = sqlSession.getMapper(UserMapper.class);


      User user=mapper.findById(7);
      System.out.println("user中的birthday: "+user.getBirthday());
      // 输出：user中的birthday: Tue Jan 25 11:16:13 CST 2022

      sqlSession.close();
  }
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220125112809756.png" alt="image-20220125112809756" style="zoom:80%;" />



### 2. plugins标签

MyBatis可以使用第三方的插件来对功能进行扩展，分页助手PageHelper是将分页的复杂操作进行封装，使用简单的方式即

可获得分页的相关数据

开发步骤：

① 导入通用PageHelper的坐标

② 在mybatis核心配置文件中配置PageHelper插件

③ 测试分页数据获取





① 导入通用PageHelper的坐标

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.0</version>
</dependency>
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>4.2</version>
</dependency>
```



② 在mybatis核心配置文件中配置PageHelper插件

```xml
<!--配置分页助手插件-->
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```



③ 测试分页数据获取

```java
@Test
//查询全部
public void test3() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sessionFactory.openSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    // 设置分页的相关参数  当前页和每页显示的条数
    //第一页，每页显示3条
    PageHelper.startPage(2,3);

    List<User> userList = mapper.findAll();

    for (User user : userList) {
        System.out.println(user);
    }

    //获得与分页相关的参数
    PageInfo<User> pageInfo=new PageInfo<>(userList);
    System.out.println("当前页："+pageInfo.getPageNum());
    System.out.println("每页显示条数："+pageInfo.getPageSize());
    System.out.println("总条数："+pageInfo.getTotal());
    System.out.println("总页数："+ pageInfo.getPages());
    System.out.println("上一页："+pageInfo.getPrePage());
    System.out.println("下一页："+pageInfo.getNextPage());
    System.out.println("是否是第一页："+pageInfo.isIsFirstPage());
    System.out.println("是否是最后一页："+pageInfo.isIsLastPage());

    sqlSession.close();
}
```

**输出：**

User{id=5, username='lucy', password='123'}
User{id=6, username='tom', password='abc'}
User{id=7, username='xyyyyyy', password='abc'}
当前页：2
每页显示条数：3
总条数：6
总页数：2
上一页：1
下一页：0
是否是第一页：false
是否是最后一页：true







# 十五、MyBatis的多表操作



### 1. 一对一查询



##### 1. 一对一查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对一查询的需求：查询一个订单，与此同时查询出该订单所属的用户

![image-20220126114001047](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126114001047.png)



##### 2. 一对一查询的语句

对应的sql语句：select *,o.id oid,u.id uid from `orders` o,`user` u where o.uid=u.id

查询的结果如下：

![image-20220126122158109](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126122158109.png)





##### 3. 创建Order和User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    // 当前订单属于那一个用户
    private User user;
}
```

```java
public class User {

    private int id;
    private String username;
    private String password;
    private Date birthday;

}
```



##### 4. 创建OrderMapper接口

```java
public interface OrderMapper {
    public List<Order> findAll();
}
```



##### 5. 配置OrderMapper.xml

```xml
<mapper namespace="com.xrj.mapper.OrderMapper">

    <resultMap id="orderMap" type="order">
        <!--手动指定字段与实体属性的映射关系
            column：数据表的字段名称
            property：实体的属性名称
        -->
        <id column="oid" property="id"></id>
        <result column="ordertime" property="ordertime"></result>
        <result column="total" property="total"></result>
        <!--<result column="uid" property="user.id"></result>-->
        <!--<result column="username" property="user.username"></result>-->
        <!--<result column="password" property="user.password"></result>-->
        <!--<result column="birthday" property="user.birthday"></result>-->

        <!--property:当前实体中的属性名称（order中的user），
            javaType：当前实体（order）中的属性类型（user）（定义别名了）
        -->
        <association property="user" javaType="user">
            <id column="uid" property="id"></id>
            <result column="username" property="username"></result>
            <result column="password" property="password"></result>
            <result column="birthday" property="birthday"></result>
        </association>
    </resultMap>
    
    <select id="findAll" resultMap="orderMap">
        SELECT *,o.id oid,u.id uid FROM `orders` o,`user` u WHERE o.uid=u.id
    </select>
```



##### 6. 测试结果

```java
@Test
public void test1() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();

    OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
    List<Order> all = mapper.findAll();
    for (Order order : all) {
        System.out.println(order);
    }

    sqlSession.close();
}
```

![image-20220126122813991](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126122813991.png)





### 2. 一对多查询



##### 1. 一对多查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对多查询的需求：查询一个用户，与此同时查询出该用户具有的订单

![image-20220126141325488](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126141325488.png)



##### 2. 一对多查询的语句

对应的sql语句：select *,o.id oid from `user` u,`orders` o where u.id=o.uid

查询的结果如下：

![image-20220126141449960](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126141449960.png)





##### 3. 修改User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    // 当前订单属于那一个用户
    private User user;

}
```

```java
public class User {

    private int id;
    private String username;
    private String password;
    private Date birthday;

    //描述的是当前用户存在哪些订单
    private List<Order> orderList;

}
```



##### 4. 创建UserMapper接口

```java
public interface UserMapper {

    public List<User> findAll();

}
```



##### 5. 配置UserMapper.xml

```xml
<mapper namespace="com.xrj.mapper.UserMapper">

    <resultMap id="userMap" type="user">
        <id column="uid" property="id"></id>
        <result column="username" property="username"></result>
        <result column="password" property="password"></result>
        <result column="birthday" property="birthday"></result>

        <!--配置集合信息
            property:当前实体中的属性名称，集合名称
            ofType：当前集合中的数据类型
        -->
        <collection property="orderList" ofType="order">
            <!--封装order数据-->
            <id column="oid" property="id"></id>
            <result column="ordertime" property="ordertime"></result>
            <result column="total" property="total"></result>
        </collection>

    </resultMap>
    
    <select id="findAll" resultMap="userMap">
        select *,o.id oid from `user` u,`orders` o where u.id=o.uid
    </select>
    
</mapper>
```



##### 6. 测试结果

```java
@Test
public void test2() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> userList = mapper.findAll();
    for (User user : userList) {
        System.out.println(user);
    }

    sqlSession.close();
}
```

User{id=1, username='xrj', password='123456', birthday=null, orderList=[Order{id=1, ordertime=Sun Feb 15 14:25:25 CST 2015, total=3000.0, user=null}, Order{id=2, ordertime=Fri Mar 23 18:20:03 CST 2018, total=5000.0, user=null}]}

User{id=2, username='xyy', password='123', birthday=null, orderList=[Order{id=3, ordertime=Wed Jan 26 03:05:45 CST 2022, total=10000.0, user=null}]}







### 3. 多对多查询



##### 1.多对多查询的模型

用户表和角色表的关系为，一个用户有多个角色，一个角色被多个用户使用

多对多查询的需求：查询用户同时查询出该用户的所有角色

![image-20220126141835956](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126141835956.png)



##### 2. 多对多查询的语句

对应的sql语句：select *,o.id oid,u.id uid FROM `orders` o,`user` u WHERE o.uid=u.id

查询的结果如下：

![image-20220126144557062](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220126144557062.png)



##### 3. 创建Role实体，修改User实体

```java
public class Role {

    private int id;
    private String roleName;
    private String roleDesc;
}
```

```java
public class User {

    private int id;
    private String username;
    private String password;
    private Date birthday;

    //描述的是当前用户存在哪些订单
    private List<Order> orderList;

    //描述的是当前用户具备那些角色

}
```



##### 4. 添加UserMapper接口方法

```java
public List<User> findUserAndRoleAll();
```



##### 5. 配置UserMapper.xml

```xml
<resultMap id="userRoleMap" type="user">
    <!--user的信息-->
    <id column="userId" property="id"></id>
    <result column="username" property="username"></result>
    <result column="password" property="password"></result>
    <result column="birthday" property="birthday"></result>
    <!--user内部的roleList信息-->
    <collection property="roleList" ofType="role">
        <id column="roleId" property="id"></id>
        <result column="roleName" property="roleName"></result>
        <result column="roleDesc" property="roleDesc"></result>
    </collection>
</resultMap>

<select id="findUserAndRoleAll" resultMap="userRoleMap">
    select * from user u,sys_user_role ur,sys_role r where u.id=ur.userId and ur.roleId=r.id
</select>
```



##### 6. 测试结果

```java
@Test
public void test3() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> userAndRoleAll = mapper.findUserAndRoleAll();
    for (User user : userAndRoleAll) {
        System.out.println(user);
    }

    sqlSession.close();
}
```

User{id=1, username='xrj', password='123456', birthday=null, orderList=null, roleList=[Role{id=1, roleName='院长', roleDesc='负责全面工作'}, Role{id=2, roleName='研究员', roleDesc='课程研发工作'}]}
User{id=2, username='xyy', password='123', birthday=null, orderList=null, roleList=[Role{id=2, roleName='研究员', roleDesc='课程研发工作'}, Role{id=3, roleName='讲师', roleDesc='授课工作'}]}
User{id=6, username='tom', password='abc', birthday=null, orderList=null, roleList=[Role{id=8, roleName='徐睿杰', roleDesc='邢艳艳'}]}





MyBatis多表配置方式：

一对一配置：使用<resultMap>做配置

一对多配置：使用<resultMap>+<collection>做配置

多对多配置：使用<resultMap>+<collection>做配置







# 十六、MyBatis注解开发



### 1.MyBatis的常用注解

@Insert：实现新增

@Update：实现更新

@Delete：实现删除

@Select：实现查询

@Result：实现结果集封装

@Results：可以与@Result 一起使用，封装多个结果集

@One：实现一对一结果集封装

@Many：实现一对多结果集封装



### 2 MyBatis的增删改查

修改MyBatis的核心配置文件，我们使用了注解替代的映射文件，所以我们只需要加载使用了注解的Mapper接口即可,不需要写配置文件

```xml
<!--加载映射关系 -->
<mappers>
    <!--指定接口所在的包-->
    <package name="com.xrj.mapper"></package>
</mappers>
```



UserMapper接口

```java
public interface UserMapper {

    @Insert("insert into user values(#{id},#{username},#{password},#{birthday})")
    public void save(User user);

    @Update("update user set username=#{username},password=#{password} where id =#{id}")
    public void update(User user);

    @Delete("delete from user where id=#{id}")
    public void delete(int id);

    @Select("select * from user where id=#{id}")
    public User findById(int id);

    @Select("select * from user")
    public List<User> findAll();
}
```

```java
public class MyBatisTest {

  private UserMapper mapper;

  @Before
  public void before() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    mapper = sqlSession.getMapper(UserMapper.class);
  }

  @Test
  public void testSave(){
    User user = new User();
    user.setUsername("zhangsan");
    user.setPassword("123000");
    mapper.save(user);

  }

  @Test
  public void testUpdate(){
    User user = new User();
    user.setId(10);
    user.setUsername("xxxxx");
    user.setPassword("123000");
    mapper.update(user);
  }

  @Test
  public void testDelete(){
    mapper.delete(10);
  }

  @Test
  public void testFindById(){
    User user = mapper.findById(6);
    System.out.println(user);
  }

  @Test
  public void testFindAll(){
    List<User> userList = mapper.findAll();
    for (User user : userList) {
      System.out.println(user);
    }
  }
}
```



### 3. MyBatis的注解实现复杂映射开发

实现复杂关系映射之前我们可以在映射文件中通过配置<resultMap>来实现，使用注解开发后，我们可以使用@Results注解

，@Result注解，@One注解，@Many注解组合完成复杂关系的配置



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220127154713170.png" alt="image-20220127154713170" style="zoom:67%;" />

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220127154728630.png" alt="image-20220127154728630" style="zoom:67%;" />



### 4. 一对一查询

##### 1. 一对一查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对一查询的需求：查询一个订单，与此同时查询出该订单所属的用户



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220127154812460.png" alt="image-20220127154812460" style="zoom:67%;" />



##### 2. 一对一查询的语句

```sql
select *,o.id oid from orders o ,user u where o.uid=u.id
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220127154908683.png" alt="image-20220127154908683" style="zoom:70%;" />



##### 3. 创建Order和User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    // 当前订单属于那一个用户
    private User user;
}
```

```java
public class User {

    private int id;
    private String username;
    private String password;
    private Date birthday;
}
```



##### 4. 创建OrderMapper接口

```java
public interface OrderMapper {

    @Select("select * from orders")
    @Results({
            @Result(column = "id",property = "id"),
            @Result(column = "ordertime",property = "ordertime"),
            @Result(column = "total",property = "total"),
            // 封user
            @Result(
                    property = "user",       // 要封装的属性名称
                    column = "uid",          // 根据哪个字段去查询user表的数据
                    javaType = User.class,   // 要封装的实体类型
                    // 一对一查询，select属性 代表查询哪个接口的方法去获得数据
                    one = @One(select = "com.xrj.mapper.UserMapper.findById")
            )
    })
    public List<Order> findAll();
    
    // @Select("select *,o.id oid from orders o ,user u where o.uid=u.id")
    // @Results({
    //         @Result(column = "oid",property = "id"),
    //         @Result(column = "ordertime",property = "ordertime"),
    //         @Result(column = "total",property = "total"),
    //         @Result(column = "uid",property = "user.id"),
    //         @Result(column = "username",property = "user.username"),
    //         @Result(column = "password",property = "user.password")
    // })
    // public List<Order> findAll();
}
```



##### 5.测试结果

```java
@Test
public void testFindAll(){
  List<Order> orderList = mapper.findAll();
  for (Order order : orderList) {
    System.out.println(order);
  }
```

Order{id=1, ordertime=Sun Feb 15 22:25:25 CST 2015, total=3000.0, user=User{id=1, username='xrj1', password='123456'}}
Order{id=2, ordertime=Sat Mar 24 02:20:03 CST 2018, total=5000.0, user=User{id=1, username='xrj1', password='123456'}}
Order{id=3, ordertime=Wed Jan 26 11:05:45 CST 2022, total=10000.0, user=User{id=2, username='xyy', password='123'}}





### 5. 一对多查询

##### 1. 一对多查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对多查询的需求：查询一个用户，与此同时查询出该用户具有的订单

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220127160909308.png" alt="image-20220127160909308" style="zoom:67%;" />



##### 2. 一对多查询的语句

```sql
select * from user;
select * from orders where uid=查询出用户的id;
```





##### 3.修改User实体

```java
public class User {

    private int id;
    private String username;
    private String password;
    private Date birthday;

    // 当前用户具有的订单
    private List<Order> orderList;
}
```



```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    // 当前订单属于那一个用户
    private User user;
}
```



##### 4. 创建UserMapper接口

```java
@Select("select * from user")
@Results({
        @Result(id = true,column = "id",property = "id"),   //id=true表示当前属性为主键
        @Result(column = "username",property = "username"),
        @Result(column = "password",property = "password"),
        //封order
        @Result(
                property = "orderList",
                column = "id",
                javaType = List.class,
                many = @Many(select = "com.xrj.mapper.OrderMapper.findByUid")
        )
})
public List<User> findUserAndOrder();
```



##### 5. 测试结果

```java
public class MyBatisTest3 {

  private UserMapper mapper;

  @Before
  public void before() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    mapper = sqlSession.getMapper(UserMapper.class);
  }

  @Test
  public void testFindAll(){
    List<User> userAndOrder = mapper.findUserAndOrder();
    for (User user : userAndOrder) {
      System.out.println(user);
    }
  }

}
```

User{id=1, username='xrj1', password='123456', birthday=null, orderList=[Order{id=1, ordertime=Sun Feb 15 22:25:25 CST 2015, total=3000.0, user=null}, Order{id=2, ordertime=Sat Mar 24 02:20:03 CST 2018, total=5000.0, user=null}]}
User{id=2, username='xyy', password='123', birthday=null, orderList=[Order{id=3, ordertime=Wed Jan 26 11:05:45 CST 2022, total=10000.0, user=null}]}
User{id=5, username='lucy', password='123', birthday=null, orderList=[]}
User{id=6, username='tom', password='abc', birthday=null, orderList=[]}
User{id=7, username='xyyyyyy', password='abc', birthday=null, orderList=[]}
User{id=8, username='xxxxx', password='123000', birthday=null, orderList=[]}





### 6. 多对多查询

##### 1.多对多查询的模型

用户表和角色表的关系为，一个用户有多个角色，一个角色被多个用户使用

多对多查询的需求：查询用户同时查询出该用户的所有角色

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220127171219095.png" alt="image-20220127171219095" style="zoom:67%;" />





##### 2. 多对多查询的语句

```sql
select * from user;
select * from sys_user_role ur,sys_role r where ur.roleId=r.id and ur.userId=#{uid}
```



##### 3. 创建Role实体，修改User实体

```java
public class Role {

    private int id;
    private String roleName;
    private String roleDesc;
}
```

```java
public class User {

    private int id;
    private String username;
    private String password;
    private Date birthday;

    //当前用户具备哪些角色
    private List<Role> roleList;

    // 当前用户具有的订单
    private List<Order> orderList;
}
```



##### 4. 添加UserMapper接口方法

```java
@Select("select * from user")
@Results({
        @Result(id = true,column = "id",property = "id"),
        @Result(column = "username",property = "username"),
        @Result(column = "password",property = "password"),
        //封Role
        @Result(
                property = "roleList",
                column = "id",
                javaType = List.class,
                many = @Many(select = "com.xrj.mapper.RoleMapper.findByUid")
        )
})
public List<User> findUserAndRoleAll();
```



##### 5.测试结果

```java
@Test
public void testFindAll(){
  List<User> all = mapper.findUserAndRoleAll();
  for (User user : all) {
    System.out.println(user);
  }
}
```

User{id=1, username='xrj1', password='123456', birthday=null, roleList=[Role{id=1, roleName='院长', roleDesc='负责全面工作'}, Role{id=2, roleName='研究员', roleDesc='课程研发工作'}], orderList=null}
User{id=2, username='xyy', password='123', birthday=null, roleList=[Role{id=2, roleName='研究员', roleDesc='课程研发工作'}, Role{id=3, roleName='讲师', roleDesc='授课工作'}], orderList=null}
User{id=5, username='lucy', password='123', birthday=null, roleList=[], orderList=null}
User{id=6, username='tom', password='abc', birthday=null, roleList=[Role{id=8, roleName='徐睿杰', roleDesc='邢艳艳'}], orderList=null}
User{id=7, username='xyyyyyy', password='abc', birthday=null, roleList=[], orderList=null}
User{id=8, username='xxxxx', password='123000', birthday=null, roleList=[], orderList=null}







# 十七、SSM整合



### 1. 原始方式整合

##### 1.创建数据库

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220128155045147.png" alt="image-20220128155045147" style="zoom:80%;" />



##### 2. 创建Maven工程

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220128155117725.png" alt="image-20220128155117725" style="zoom:67%;" />



##### 3. 编写实体类

```java
public class Account {

  private Integer id;
  private String name;
  private Double money;
}
```



##### 4. 编写Mapper接口

```java
public interface AccountMapper {

    public void save(Account account);

    public List<Account> findAll();
}
```



##### 5. 编写Service接口

```java
public interface AccountService {

    public void save(Account account) throws IOException;

    public List<Account> findAll();
}
```



##### 6. 编写Service接口实现

```java
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Override
    public void save(Account account)  {
        try {
            InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
            SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
            SqlSession sqlSession = sessionFactory.openSession();
            AccountMapper mapper = sqlSession.getMapper(AccountMapper.class);
            mapper.save(account);
            sqlSession.commit();
            sqlSession.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    @Override
    public List<Account> findAll() {

        try {
            InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
            SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
            SqlSession sqlSession = sessionFactory.openSession();
            AccountMapper mapper = sqlSession.getMapper(AccountMapper.class);
            List<Account> all = mapper.findAll();
            sqlSession.close();
            return all;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}

```





##### 7. 编写Controller

```java
@Controller
@RequestMapping("/account")
public class AccountController {

  @Autowired
  private AccountService accountService;

  //保存
  @RequestMapping(value = "/save",produces = "text/html;charset=utf-8")
  @ResponseBody   // 字符串展示，不跳转
  public String save(Account account) throws IOException {
    accountService.save(account);
    return "数据保存成功";
  }

  //查询
  @RequestMapping("findAll")
  public ModelAndView findAll(){
    List<Account> accountList = accountService.findAll();
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("AccountList",accountList);
    modelAndView.setViewName("accountList");
    return modelAndView;
  }
}
```



##### 8. 编写添加页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>添加账户信息表单</h1>
    <form name="accountForm" action="${pageContext.request.contextPath}/account/save" method="post">
        账户名称：<input type="text" name="name"><br>
        账户金额：<input type="text" name="money"><br>
        <input type="submit" value="保存"><br>
    </form>
</body>
</html>
```



##### 9. 编写列表页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>展示账户数据列表</h1>
    <table border="1">
        <tr>
            <th>账户id</th>
            <th>账户名称</th>
            <th>账户金额</th>
        </tr>
        <c:forEach items="${AccountList}" var="account">
        <tr>
            <td>${account.id}</td>
            <td>${account.name}</td>
            <td>${account.money}</td>
        </tr>
        </c:forEach>
    </table>
</body>
</html>
```



##### 10. 编写相应配置文件

- Spring配置文件：applicationContext.xml

- SprngMVC配置文件：spring-mvc.xml

- MyBatis映射文件：AccountMapper.xml

- MyBatis核心文件：sqlMapConfig.xml

- 数据库连接信息文件：jdbc.properties

- Web.xml文件：web.xml

- 日志文件：log4j.xml





### 2. Spring整合MyBatis

##### 1. 整合思路

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220128155658703.png" alt="image-20220128155658703" style="zoom:67%;" />



##### 2. 将SqlSessionFactory配置到Spring容器中

```xml
<!--加载properties文件-->
<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>

<!--配置数据源-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"></property>
    <property name="jdbcUrl" value="${jdbc.url}"></property>
    <property name="user" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>

<!--配置sessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
    <!--加载mybatis核心文件-->
    <property name="configLocation" value="classpath:sqlMapConfig-spring.xml"></property>
</bean>
```



##### 3. 扫描Mapper，让Spring容器产生Mapper实现类

```xml
<!--扫描mapper所在的包，会把mapper的实现放到容器中-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.xrj.mapper"></property>
</bean>
```



##### 4. 配置声明式事务控制

```xml
<!--声明式事务控制-->
<!--平台事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>

<!--配置事物增强-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*"/>
        </tx:attributes>
    </tx:advice>

<!--事物aop织入-->
<aop:config>
    <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.xrj.service.impl.*.*(..))"></aop:advisor>
</aop:config>
```



##### 5.修改Service实现类代码

```java
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountMapper accountMapper;

    @Override
    public void save(Account account)  {
        accountMapper.save(account);
    }

    @Override
    public List<Account> findAll() {
        List<Account> accountList = accountMapper.findAll();
        return accountList;
    }
}
```
