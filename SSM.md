# 一、MyBatis

## 1、搭建MyBatis

**1、核心配置文件**

主要用于配置连接数据库的环境以及MyBatis的全局配置信息，核心配置文件存放的位置是src/main/resources目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--设置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>
</configuration>
```





**2、创建mapper接口**

> MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类。

```java
package com.xrj.mybatis.mapper;

public interface UserMapper {
    int addUser();
}
```





**3、MyBatis映射文件**

ORM（Object Relationship Mapping）对象关系映射。 

对象：Java的实体类对象 

关系：关系型数据库 

映射：二者之间的对应关系

> 1、映射文件的命名规则： 表所对应的实体类的类名+Mapper.xml 
>
> 例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml。
>
> 因此一个映射文件对应一个实体类，对应一张表的操作。
>
> MyBatis映射文件用于编写SQL，访问以及操作表中的数据。
>
> MyBatis映射文件存放的位置是src/main/resources/mappers目录下 
>
> 2、 MyBatis中可以面向接口操作数据，要保证两个一致： 
>
> ① mapper接口的全类名和映射文件的命名空间（namespace）保持一致 
>
> ② mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.xrj.mybatis.mapper.UserMapper">
    <!--
        mapper接口和映射文件要保证两个一致
        1.mapper接口的全类名和映射文件的namespace保持一致
        2.mapper接口中的方法名要和映射文件中的sql的id保持一致
    -->

    <!--int addUser();-->
    <insert id="addUser">
        insert into t_user values(null,'admin','123456',23,'男','12345@qq.com')
    </insert>
</mapper>
```







**4、通过junit测试**

```java
package com.xrj.mybatis.test;

import com.xrj.mybatis.mapper.UserMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisTest {
    @Test
    public void testInsert() throws IOException {
        // 获取核心配置文件的输入流
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取sqlSession, 是mybatis操作数据库的对象, openSession参数设置为true就是事务自动提交
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 获取UserMapper的代理实现类对象
        // 这里userMapper相当于是UserMapper接口的实现类对象, 该对象根据接口的全类名找到UserMapper.xml, 根据addUser方法找到xml文件中对应的sql语句
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        // 执行addUser方法
        int i = userMapper.addUser();
        /*
            这是sqlSession.getMapper(UserMapper.class).addUser()的底层实现, userMapper是代理实现类对象,实现了UserMapper接口
            实际上是直接通过sqlSession调用insert方法,里边的参数就是UserMapper.xml文件中对应方法的sql语句

            int i = sqlSession.insert("com.xrj.mybatis.mapper.UserMapper.addUser");
        */
        // 事务提交
        sqlSession.commit();
        System.out.println(i);
        sqlSession.close();
    }
}
```





**5、加入logj日志功能**

①加入依赖

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



②加入log4j的配置文件

> log4j的配置文件名为log4j.xml，存放的位置是src/main/resources目录下
>
> 日志的级别：
>
>  FATAL(致命) > ERROR(错误) > WARN(警告) > INFO(信息) > DEBUG(调试) 
>
> 从左到右打印的内容越来越详细

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}
%m (%F:%L) \n" />
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug" />
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>
    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>
</log4j:configuration>
```





## 2、MyBatis的增删改查

**新增**

```xml
<insert id="addUser">
    insert into t_user values(null,'admin','123456',23,'男','12345@qq.com')
</insert>
```



**删除**

```xml
<delete id="delUser">
    delete from t_user where id=7
</delete>
```





**修改**

```xml
<update id="updateUser">
    update t_user set username="xrj", age=18 where id=4
</update>
```





**查询单条数据**

```xml
<select id="getUserById" resultType="com.xrj.mybatis.pojo.User">
    select * from t_user where id = 3
</select>
```





**查询多条数据**

```xml
<select id="getAllUser" resultType="com.xrj.mybatis.pojo.User">
    select * from t_user
</select>
```



> 注意： 
>
> 1、查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射关系 
>
> resultType：自动映射，用于属性名和表中字段名一致的情况
>
> resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况







## 3、核心配置文件详解

**environments**

> environments：设置数据库的环境，属性default表示默认使用的环境的id，所以在environments中可以有多个环境
>
> environment：配置某个具体的环境，属性id表示连接数据库的环境的唯一标识
>
> transactionManager：设置事务管理方式。一共两种方式：①JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事务的提交或回滚需要手动处理。②MANAGED：被管理，例如Spring
>
> dataSource：配置数据源。属性type表示设置数据源的类型。共三种类型：①POOLED：表示使用数据库连接池缓存数据库连接。②UNPOOLED：表示不使用数据库连接池。③JNDI：表示使用上下文中的数据源

```xml
<environments default="development">
    <environment id="development">
        <!--
            transactionManager：设置事务管理方式
            属性：
            type="JDBC|MANAGED"
            JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事务的提交或回滚需要手动处理
            MANAGED：被管理，例如Spring
        -->
        <transactionManager type="JDBC"/>
        <!--
            dataSource：配置数据源
            属性：
            type：设置数据源的类型
            type="POOLED|UNPOOLED|JNDI"
            POOLED：表示使用数据库连接池缓存数据库连接
            UNPOOLED：表示不使用数据库连接池
            JNDI：表示使用上下文中的数据源
        -->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"/>
            <property name="username" value="root"/>
            <property name="password" value="root"/>
        </dataSource>
    </environment>
</environments>
```





**properties**

> 可以用过properties标签引入外部文件，例如jdbc的配置文件。可以通过${}方式获取数据

```xml
<properties resource="jdbc.properties"/>

<dataSource type="POOLED">
    <property name="driver" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</dataSource>
```





**typeAliases**

> 通过typeAliases可以设置类型的别名，在我们的select语句中，需要设置`resultType`或`resultMap`，这里的属性设置的是全类名，我们可以通过typeAliases将全类名设置一个别名
>
> `<typeAlias type="com.xrj.mybatis.pojo.User" alias="abc"></typeAlias>`，通过`typeAlias`标签设置，type为全类名，alias为设置的别名，如果不设置alias属性，就会有默认的别名，即类名【不区分大小写】
>
> 当实体类很多的时候，设置别名会写很多的typeAlias，因此可以指定将整个包设置别名。`<package name="com.xrj.mybatis.pojo"/>`，通过`package`标签将整个包设置别名，默认为类名

```xml
<!--设置类型别名-->
<typeAliases>
    <!--
        typeAlias：设置某个类型的别名
        属性：
        type：设置需要设置别名的类型
        alias：设置某个类型的别名，若不设置该属性，那么该类型拥有默认的别名，即类名, 且不区分大小写
     -->
    <!--<typeAlias type="com.xrj.mybatis.pojo.User" alias="abc"></typeAlias>-->
    <!--<typeAlias type="com.xrj.mybatis.pojo.User"></typeAlias>-->
    
    <!--以包为单位，将包下所有的类型设置默认的类型别名，即类名且不区分大小写-->
    <package name="com.xrj.mybatis.pojo"/>
</typeAliases>
```





**mapper**

> 通过`mapper`标签引入映射文件，`<mapper resource="mapper/UserMapper.xml"/>`，一个实体类对应一个`mapper`接口，一个`mapper`接口对应一个mapper映射文件，当映射文件很多时，可以通过引入包的方式引入映射文件。
>
> `<package name="com.xrj.mybatis.mapper"/>`，通过`package`标签将mapper包下所有的映射文件引入。但是有两点要求：①mapper接口所在的包要和映射文件所在的包一致。②mapper接口要和映射文件的名字一致。 在resources下建立和java目录下相同的包结构，在编译后的classes文件夹下，mapper接口和映射文件是在同一个目录下的

```xml
<!--引入映射文件-->
<mappers>
    <!--<mapper resource="mapper/UserMapper.xml"/>-->
    <!--
        以包的方式引入映射文件
        要求：
        1、mapper接口所在的包要和映射文件所在的包一致
        2、mapper接口要和映射文件的名字一致
    -->
    <package name="com.xrj.mybatis.mapper"/>
</mappers>
```





需要注意的是：在mybatis中，各个标签是有指定顺序的，必须以下面的顺序设置标签

```xml
properties?,settings?,typeAliases?,typeHandlers?,
objectFactory?,objectWrapperFactory?,reflectorFactory?,
plugins?,environments?,databaseIdProvider?,mappers
```







## 4、SQL语句传参

MyBatis获取参数值的两种方式：**${}**和**#{}**

- **${}**的本质就是**字符串拼接**，**#{}**的本质就是**占位符赋值** 

- ${}使用**字符串拼接**的方式拼接sql，若为**字符串**类型或**日期类型**的字段进行赋值时，需要手动**加单引号**；

- #{}使用**占位符赋值**的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号
- 在SQL语句中，数据库表的表名不确定，需要外部动态传入，此时不能使用#{}，因为 SQL 语法不允许表名位置使用问号占位符，此时只能使用${}。



### 4.1 单个字面量类型的参数

> 若mapper接口中的方法参数为单个的字面量类型 ，此时可以使用${}和#{}**以任意的名称**获取参数的值，注意${}需要手动加单引号

**使用#{}**

> 实际执行的sql语句是`select * from t_user where username = ?`，Mybatis会在运行过程中，把配置文件中的SQL语句里面的**#{}**转换为“**?**”占位符，发送给数据库执行。#{}里边内容可以随便写

```xml
<select id="findUserByName" resultType="User">
    <!--
        使用#{} MyBatis解析后的sql语句, #{}里边的参数可以随便写
        select * from t_user where username = ?
    -->
    select * from t_user where username = #{username}
</select>
```





**使用${}**

> 实际执行的sql语句是`select * from t_user where username = 'xrj'`，这是字符串拼接的方式，${}如果是字符串或者日期类型需要手动加单引号。${}里边内容可以随便写

```xml
<select id="findUserByName" resultType="User">
    <!--
        使用${} MyBatis解析后的sql语句, ${}里边的参数可以随便写, 但是如果是字符串或日期需要手动加上单引号
        select * from t_user where username = 'xrj'
    -->
    select * from t_user where username = '${username}'
</select>
```





### 4.2 多个字面量类型的参数

> 若mapper接口中的方法参数为多个时，此时MyBatis会自动将这些参数放在一个map集合中，以arg0,arg1...为键，以参数为值；或者以 param1,param2...为键，以参数为值；
>
> 因此只需要通过${}和#{}访问map集合的键就可以获取相 对应的值，注意${}需要手动加单引号

```xml
<select id="checkLogin" resultType="User">
    select * from t_user where username = #{arg0} and password = #{arg1} 
    <!-- select * from t_user where username = #{param1} and password = #{param2} -->
    <!-- select * from t_user where username = '${param1}' and password = '${param2}' -->
</select>
```





### 4.3 map集合类型的参数

> 若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在 map中
>
> 只需要通过${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号

```xml
<select id="checkLoginByMap" resultType="User">
    select * from t_user where username = #{username} and password = #{password}
    <!-- select * from t_user where username = '${username}' and password = '${password}' -->
</select>
```

```java
@Test
public void testCheckLoginByMap(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    Map<String, String> map = new HashMap<>();
    map.put("username", "xrj");
    map.put("password", "123456");
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User xrj = mapper.checkLoginByMap(map);
    System.out.println(xrj);
}
```





### 4.4 使用@Param标识参数

> 可以通过@Param注解标识mapper接口中的方法参数，此时，会将这些参数放在map集合中，以@Param注解的value属性值为键，以参数为值；或者以 param1,param2...为键，以参数为值
>
> 只需要通过${}和#{}访问map集合的键就可以获取相对应 的值， 注意${}需要手动加单引号

**mapper接口**

```java
User checkLoginByParam(@Param("username") String username, @Param("password") String password);
```

**映射文件**

```xml
<select id="checkLoginByParam" resultType="User">
    select * from t_user where username = #{username} and password = #{password}
    <!-- select * from t_user where username = #{param1} and password = #{param2} -->
</select>
```





### 4.5 实体类型的参数

> 若mapper接口中的方法参数为实体类对象时 
>
> 此时可以使用${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号
>
> Mybatis会根据#{}中传入的数据，加工成getXxx()方法，通过反射在实体类对象中调用这个方法，从而获取到对应的数据。填充到#{}这个位置

**mapper接口**

```java
void addUser(User user);
```

**映射文件**

```xml
<insert id="addUser">
    insert into t_user values(null, #{username}, #{password}, #{age}, #{gender}, #{email})
</insert>
```







## 5、MyBatis的各种select

**MyBatis会根据mapper接口的返回值类型调用相应的select方法，得到不同的返回值**



### 5.1 查询一个实体类对象

**mapper接口**

```java
User selectUserById(Integer id);
```

**映射文件**

```xml
<select id="selectUserById" resultType="User">
    select * from t_user where id = #{id}
</select>
```

**junit测试**

```java
@Test
public void testSelectUserById(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    User user = mapper.selectUserById(1);
    System.out.println(user);
}
```





### 5.2 查询一个list集合

> 当查询的数据为多条时，不能使用实体类作为返回值，否则会抛出异常 TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值。

**mapper接口**

```java
List<User> selectAllUser();
```

**映射文件**

```xml
<select id="selectAllUser" resultType="user">
    select * from t_user
</select>
```

**junit测试**

```java
@Test
public void testSelectAllUse(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<User> users = mapper.selectAllUser();
    System.out.println(users);
}
```







### 5.3 查询单个数据

**mapper接口**

```java
int getCount();
```

**映射文件**

```xml
<!-- int也可以写为Integer, 且不区分大小写, 因为int和Integer都是Integer的别名 
     在MyBatis中，对于Java中常用的类型都设置了类型别名
     例如： java.lang.Integer -> int|integer
     例如： int -> _int|_integer
     例如： Map -> map, List->list
-->
<select id="getCount" resultType="int">
    select count(*) from t_user
</select>
```

**junit测试**

```java
@Test
public void testGetCount(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    int count = mapper.getCount();
    System.out.println(count);
}
```







### 5.4 查询单条数据为map集合

> 在查询数据时，有时我们需要查询的是一条数据中的几个字段，查询的结果没有相应的实体类，因此就需要返回一个map集合，key就是字段名，value就是字段的值

**mapper接口**

```java
Map<String, Object> selectUserByIdToMap(Integer id);
```

**映射文件**

```xml
<select id="selectUserByIdToMap" resultType="map">
    select * from t_user where id = #{id}
</select>
```

**junit测试**

```java
@Test
public void testSelectUserByIdToMap(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<String, Object> stringObjectMap = mapper.selectUserByIdToMap(2);
    // {password=123456, gender=男, id=2, age=23, email=12345@qq.com, username=admin}
    System.out.println(stringObjectMap);
}
```







### 5.5 查询多条数据为map集合

> 查询多条数据为map集合，有两种方式
>
> 1、将查询的每条数据作为一个map，多条数据就会有多个map，然后将所有的map放入一个list中
>
> 2、将查询的每条数据作为一个map，将多个查询结果放到一个大的map中，因此需要指定大map的key，我们使用@MapKey("xx")，将查询结果的某个字段作为大的map的key，查询出的单条结果作为value，因为key不能重复，所有key通常设为id



**方式一**

**mapper接口**

```java
List<Map<String, Object>> getAllUserToMap1();
```

**映射文件**

```xml
<select id="getAllUserToMap1" resultType="map">
    select id, username, password, age from t_user
</select>
```

**junit测试**

```java
@Test
public void testGetAllUserToMap1(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<Map<String, Object>> allUserToMap1 = mapper.getAllUserToMap1();
    /*
        [{password=123456, id=1, age=23, username=admin},
        {password=123456, id=2, age=23, username=admin},
        {password=123456, id=3, age=18, username=xrj},
        {password=123456, id=5, age=23, username=admin},
     */
    System.out.println(allUserToMap1);
}
```





**方式二**

**mapper接口**

```java
@MapKey("id")
Map<Integer, Object> getAllUserToMap2();
```

**映射文件**

```xml
<select id="getAllUserToMap2" resultType="map">
    select id, username, password, age from t_user
</select>
```

**junit测试**

```java
@Test
public void testGetAllUserToMap2(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<Integer, Object> allUserToMap2 = mapper.getAllUserToMap2();
    /*
        {1={password=123456, id=1, age=23, username=admin},
        2={password=123456, id=2, age=23, username=admin},
        3={password=123456, id=3, age=18, username=xrj},
        5={password=123456, id=5, age=23, username=admin}}
     */
    System.out.println(allUserToMap2);
}
```





## 6、特殊SQL的执行

### 6.1 模糊查询

模糊查询时，不能使用`'%#{xx}%'`, 因为`#{}`会被解析为`?`，变为`'%?%'`是一个字符串



有三种方式：

- 使用`${xx}`拼接字符串的方式
- 使用concat函数进行字符串拼接
- 使用`"%"#{xx}"%"`方式



**mapper接口**

```java
List<User> getUserByLike(@Param("mohu") String mohu);
```

**映射文件**

```xml
<select id="getUserByLike" resultType="User">
    <!-- 模糊查询时, 不能使用'%#{xx}%', 因为#{}会被解析为?, 变为'%?%'是一个字符串 -->
    <!-- select * from t_user where username like '%#{mohu}%' -->

    <!-- 第一种方法: 使用${xx}拼接字符串的方式 -->
    <!-- select * from t_user where username like '%${mohu}%' -->

    <!-- 第二种方法: 使用concat函数进行字符串拼接 -->
    <!-- select * from t_user where username like concat('%', #{mohu}, '%') -->

    <!-- 第三种方法: 使用"%"#{xx}"%"方式 -->
    select * from t_user where username like "%"#{mohu}"%"
</select>
```

**junit测试**

```java
@Test
public void testSelectUserByLike(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<User> x = mapper.getUserByLike("x");
    System.out.println(x);
}
```







### 6.2 批量删除

批量删除的语句：`delete from t_user where id = 5 or id = 6` 或者 `delete from t_user where id in(7, 8, 9)`

使用第二种方式在传参数时，传的是一个字符串，不能使用`#{}`获取值，因为他会自动加上单引号，所以需要使用`${}`获取值



**mapper接口**

```java
void addUser(User user);
```

**映射文件**

```xml
<delete id="delUser">
    <!--
        这里不能使用#{}的方式获取, 因为传递的参数是字符串, #{}会自动加上单引号
        delete from t_user where id in (#{strId})
    -->
    delete from t_user where id in (${strId})
</delete>
```

**junit测试**

```java
@Test
public void testDelUser(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    mapper.delUser("11, 12, 13");
}
```







### 6.3 动态设置表名

当表名不确定时，表名作为参数传递，此时获取表名只能通过`${}`，不能使用`#{}`，因为`#{}`会自动添加单引号

**mapper接口**

```java
List<User> getUserByTable(@Param("tableName") String tableName);
```

**映射文件**

```xml
<select id="getUserByTable" resultType="user">
    select * from ${tableName}
</select>
```

**junit测试**

```java
@Test
public void testGetUserByTable(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<User> t_user = mapper.getUserByTable("t_user");
    System.out.println(t_user);
}
```





### 6.4 获取自增主键

> 当我们的主键id为自增列时，我们在插入数据的时候，不需要设置该列，但是我们又需要获取这一列数据的时候，就需要获取自增主键了。
>
> Q: 为什么不能通过查询最大主键的方式获得id？   A：假如用户A创建一个班级后，还没准备查询，用户B也创建了一个班级，此时A查询的结果就不正确了
>
> 比如新建一个班级，班级号为自增列，然后需要为班级添加学生，即设置学生的班级号，因此在完成新增班级操作后，就需要获取该班级的自增id
>
> 获取自增主键是jdbc里边的操作，MyBatis也对该操作进行了封装



**mapper接口**

```java
void insertUser(User user);
```

**映射文件**

```xml
<!--
    useGeneratedKeys表示使用生成的主键
    keyProperty属性可以指定主键在实体类中对应的属性名, MyBatis插入数据后将自增的主键存入这个属性
    因为insert操作的返回值只能是受影响的行数, 所以自增主键不能通过返回值获取
 -->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values(null, username, password, age, gender, email)
</insert>
```

**junit测试**

```java
@Test
public void testInsertUser(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    User user = new User(null, "xiaoming", "123", 15, "男", "123@qq.com");
    mapper.insertUser(user);
    // User{id=16, username='xiaoming', password='123', age=15, gender='男', email='123@qq.com'}
    System.out.println(user);
}
```





## 7、自定义映射ResultMap

### 7.1 字段名和属性名不一致

字段名和属性名不一致的情况：

- 为查询的字段设置别名，和属性名保持一致
- 当字段名符合mysql的要求使用下划线连接xx_xx，而属性名符合java的要求使用驼峰xxXx，此时可以在MyBatis的核心配置文件中设置一个全局设置, 自动将下划线映射为驼峰
- 使用resultMap自定义映射处理



#### 7.1.1 设置别名

**mapper**

```java
Emp getEmpById(@Param("empId") Integer empId);
```

**映射文件**

```xml
<select id="getEmpById" resultType="emp">
    select emp_id empId, emp_name empName, age, gender, dept_id deptId from t_emp where emp_id = #{empId}
</select>
```

**junit测试**

```java
@Test
public void testGetEmpById(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    Emp empById = mapper.getEmpById(1);
    System.out.println(empById);
}
```



#### 7.1.2 MyBatis全局配置

在`mybatis-config.xml`文件中加入如下配置

```xml
<settings>
    <!-- 将xxx_xxx这样的列名自动映射到xxXxx这样驼峰式命名的属性名 -->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```



**映射文件**

```xml
<select id="getEmpById" resultType="emp">
    select * from t_emp where emp_id = #{empId}
</select>
```



#### 7.1.3 使用resultMap

声明一个`resultMap`指定`column`到`property`之间的对应关系

`type`：处理映射关系的实体类类型

`id`：设置主键列和主键属性之间的对应关系

`result`：设置普通字段和Java实体类属性之间的关系

`column`：用于指定字段名

`property`：用于指定Java实体类属性名

**映射文件**

```xml
<resultMap id="empResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="dept_id" property="deptId"/>
</resultMap>

<select id="getEmpById" resultMap="empResultMap">
    select * from t_emp where emp_id = #{empId}
</select>
```





### 7.2 多对一映射

> 多对一映射，在数据库中，是作为外键映射的主表的id，但是在java中，一般是映射为对象。而一对多对应的是集合。

处理多对一映射关系：

- 级联方式处理
- `association`
- 分步查询



**Emp类**

> 数据库中它对应Dept表的id，这里直接对应Dept对象

```java
package com.xrj.mybatis.pojo;

public class Emp {
    private Integer empId;
    private String empName;
    private Integer age;
    private String gender;
    private Dept dept;

    public Emp() {
    }

    public Emp(Integer empId, String empName, Integer age, String gender, Dept dept) {
        this.empId = empId;
        this.empName = empName;
        this.age = age;
        this.gender = gender;
        this.dept = dept;
    }

    public Integer getEmpId() {
        return empId;
    }

    public void setEmpId(Integer empId) {
        this.empId = empId;
    }

    public String getEmpName() {
        return empName;
    }

    public void setEmpName(String empName) {
        this.empName = empName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public Dept getDept() {
        return dept;
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }

    @Override
    public String toString() {
        return "Emp{" +
                "empId=" + empId +
                ", empName='" + empName + '\'' +
                ", age=" + age +
                ", gender='" + gender + '\'' +
                ", dept=" + dept +
                '}';
    }
}
```



**mapper接口**

```java
Emp getEmpAndDeptById(@Param("empId") Integer empId);
```



#### 7.2.1 级联方式处理

**映射文件**

```xml
<resultMap id="EmpAndDeptResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="dept_id" property="dept.deptId"/>
    <result column="dept_name" property="dept.deptName"/>
</resultMap>

<select id="getEmpAndDeptById" resultMap="EmpAndDeptResultMap">
    select * from t_emp left join t_dept on t_emp.dept_id = t_dept.dept_id where emp_id = #{empId}
</select>
```



**junit测试**

```java
@Test
public void testGetEmpAndDeptById(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    Emp emp = mapper.getEmpAndDeptById(1);
    // Emp{empId=1, empName='zs', age=20, gender='男', dept=Dept{deptId=1, deptName='A'}}
    System.out.println(emp);
}
```





#### 7.2.2 association标签

**映射文件**

`association`: 处理多对一的映射关系（处理实体类类型的属性） 

`property`: 设置需要处理映射关系的属性的属性名

` javaType`: 设置需要处理映射关系的属性的类型

使用`association`时, 其他同名属性不能省略, 也要设置

```xml
<resultMap id="EmpAndDeptResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
    <association property="dept" javaType="Dept">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
    </association>
</resultMap>

<select id="getEmpAndDeptById" resultMap="EmpAndDeptResultMap">
    select * from t_emp left join t_dept on t_emp.dept_id = t_dept.dept_id where emp_id = #{empId}
</select>
```





#### 7.2.3 分步查询

根据`empId`查询员工信息以及部门信息，可以先查询到员工信息，然后根据查询到的`deptId`去查询部门信息



**EmpMapper**

```java
Emp getEmpAndDeptByIdStepOne(@Param("empId") Integer empId);
```

**Emp映射文件**

`property`: 设置需要处理映射关系的属性的属性名

`select`: 设置分步查询的sql的唯一标识

`column`: 将查询出的某个字段作为分步查询的sql的条件

```xml
<resultMap id="getEmpAndDeptByIdStep" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
    <association property="dept"
                 select="com.xrj.mybatis.mapper.DeptMapper.getEmpAndDeptByIdStepTwo"
                 column="dept_id">
    </association>
</resultMap>

<select id="getEmpAndDeptByIdStepOne" resultMap="getEmpAndDeptByIdStep">
    select * from t_emp where emp_id = #{empId}
</select>
```



**DeptMapper**

```java
Dept getEmpAndDeptByIdStepTwo(@Param("deptId") Integer deptId);
```

**Dept映射文件**

```xml
<select id="getEmpAndDeptByIdStepTwo" resultType="dept">
    select * from t_dept where dept_id = #{deptId}
</select>
```



**junit测试**

```java
@Test
public void testGetEmpAndDeptByIdStep(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    Emp emp = mapper.getEmpAndDeptByIdStepOne(1);
    // Emp{empId=1, empName='zs', age=20, gender='男', dept=Dept{deptId=1, deptName='A'}}
    System.out.println(emp);
}
```







### 7.3 一对多映射

> 一对多映射时，在java实体类中通常用一个`List`集合表示，即一个A对应多个B，在A中用一个`List`集合存储B

在MyBatis中，一对多映射有两种方式

- `collection`
- 分步查询



#### 7.3.1 collection

**mapper**

```java
Dept getDeptAndEmpByDeptId(@Param("deptId") Integer deptId);
```

**映射文件**

```xml
<resultMap id="DeptAndEmpResultMap" type="dept">
    <id column="dept_id" property="deptId"/>
    <result column="dept_name" property="deptName"/>
    <!--
        collection用来处理集合类型, 处理一对多
        property是当前查询的java类的属性
        ofType设置集合类型中存储的数据类型
     -->
    <collection property="emps" ofType="emp">
        <id column="emp_id" property="empId"/>
        <result column="emp_name" property="empName"/>
        <result column="age" property="age"/>
        <result column="gender" property="gender"/>
    </collection>
</resultMap>

<select id="getDeptAndEmpByDeptId" resultMap="DeptAndEmpResultMap">
    select * from t_dept left join t_emp on t_dept.dept_id = t_emp.dept_id where t_dept.dept_id = #{deptId}
</select>
```

**junit测试**

```java
@Test
public void testGetDeptAndEmpById(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
    Dept deptAndEmpByDeptId = mapper.getDeptAndEmpByDeptId(1);
    System.out.println(deptAndEmpByDeptId);
}
```





#### 7.3.2 分步查询

> 先查询部门信息，然后在根据部门号查询员工信息

**deptMapper**

```java
Dept getDeptAndEmpByStepOne(@Param("deptId") Integer deptId);
```

**deptMapper映射**

```xml
<resultMap id="DeptAndEmpByStep" type="dept">
    <id column="dept_id" property="deptId"/>
    <result column="dept_name" property="deptName"/>
    <collection property="emps"
                 select="com.xrj.mybatis.mapper.EmpMapper.getEmpByDeptId"
                 column="dept_id">
    </collection>
</resultMap>

<select id="getDeptAndEmpByStepOne" resultMap="DeptAndEmpByStep">
    select * from t_dept where dept_id = #{deptId}
</select>
```



**empMapper**

```java
List<Emp> getEmpByDeptId(@Param("deptId") Integer deptId);
```

**empMapper映射**

```xml
<select id="getEmpByDeptId" resultType="emp">
    select * from t_emp where dept_id = #{deptId}
</select>
```



**junit测试**

```java
@Test
public void testGetDeptAndEmpByStep(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
    Dept deptAndEmpByStepOne = mapper.getDeptAndEmpByStepOne(1);
    System.out.println(deptAndEmpByStepOne);
}
```









### 7.4 延迟加载

> 对于多对一映射时，我们使用分步查询，第一步查询基本信息，第二步查询关联信息。有时我们查询后只需要基本信息，而不需要那些关联的信息，查询关联信息也需要执行sql语句，会浪费资源，这时就可以开启**延时加载**，
>
> **延迟加载**的概念：对于实体类关联的属性到**需要使用**时才查询。也叫**懒加载**



在`mybatis-config`的全局配置中，我们可以设置**延迟加载**，一般我们还会设置`aggressiveLazyLoading`属性，当他为`true`时，表示只要调用了就会加载所有的属性，为`false`时就是按需加载，需要某个属性，才会去加载，默认为`false`

```xml
<settings>
    <!-- 开启延迟加载, 对于关联的属性, 只有调用的时候才去执行对应sql语句 -->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!-- 激进的懒惰加载, 当开启时, 任何方法的调用都会加载该对象的所有属性, 否则, 每个属性按需加载, 默认为false -->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```



当我们设置了延迟加载的环境后，对于某一分步查询我们可以通过`fetchType`来指定是延迟加载还是立即加载，因为全局环境是延迟加载，所有这里如果不设置他也是默认延迟加载，`fetchType`设置为`eager`表示立即加载，设置为`lazy`表示延迟加载

```xml
<resultMap id="getEmpAndDeptByIdStep" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
    <!--
        property: 设置需要处理映射关系的属性的属性名
        select: 设置分步查询的sql的唯一标识
        column: 将查询出的某个字段作为分步查询的sql的条件
        fetchType: 在指定了延迟加载的环境中, 可以指定某一步分步查询为延迟加载还是立即加载
    -->
    <association property="dept" fetchType="eager"
                 select="com.xrj.mybatis.mapper.DeptMapper.getEmpAndDeptByIdStepTwo"
                 column="dept_id">
    </association>
</resultMap>

<select id="getEmpAndDeptByIdStepOne" resultMap="getEmpAndDeptByIdStep">
    select * from t_emp where emp_id = #{empId}
</select>
```









## 8、动态SQL

> Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。
>
> 比如通过客户端选择的条件来进行查询，有时候选择两个条件，有时候是三个条件，那么我们如果手动来拼接sql语句是相当麻烦的。
>
> 如果没有提交表单，服务器去获取数据，得到的就是`null`，如果点击提交表单，但是表单内容为空，那么服务器获取的就是空字符串。
>
> 注意：单选框和复选框如果什么都没选提交后，服务器获取数据为`null`



### 8.1 if

> if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之 标签中的内容不会执行



**mapper**

```java
List<Emp> getEmpByCondition(Emp emp);
```

**映射文件**

```xml
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp where
    <if test="empName != null and empName != ''">
        emp_name = #{empName}
    </if>
    <if test="age != null and age != ''">
        and age = #{age}
    </if>
    <if test="gender != null and gender != ''">
        and gender = #{gender}
    </if>
</select>
```

**junit测试**

```java
@Test
public void testGetEmpByCondition(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    empMapper mapper = sqlSession.getMapper(empMapper.class);
    List<Emp> empByCondition = mapper.getEmpByCondition(new Emp(null, "zs", 20, "男"));
    System.out.println(empByCondition);
}
```

> 此时查询语句中的`empName`、`age`、`gender`都是非空，MyBatis执行的sql语句为`select * from t_emp where emp_name = ? and age = ? and gender = ? `，可以正确查询。但是当`empName`为空时，sql语句变为`select * from t_emp where and age = ? and gender = ?`，会多一个`and`；当所有条件都为空，sql语句变为`select * from t_emp where`



解决方法：将映射文件的sql语句添加上`1=1`的条件，这样，不管缺少那个条件，sql语句都可以正常执行。另一种解决方法是使用`where`标签

```xml
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp where 1=1
    <if test="empName != null and empName != ''">
        and emp_name = #{empName}
    </if>
    <if test="age != null and age != ''">
        and age = #{age}
    </if>
    <if test="gender != null and gender != ''">
        and gender = #{gender}
    </if>
</select>
```





### 8.2 where

> 1、如果where标签中有条件成立，会自动生成where关键字
>
> 2、会自动将where标签体内前面多余的and/or 去掉
>
> 3、如果where标签内没有任何一个条件成立，则不会生成where关键字

**mapper**

```xml
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp
    <where>
        <if test="empName != null and empName != ''">
            emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="gender != null and gender != ''">
            and gender = #{gender}
        </if>
    </where>
</select>
```







### 8.3 trim

使用trim标签控制条件部分两端是否包含某些字符

- prefix属性：指定要动态添加的前缀
- suffix属性：指定要动态添加的后缀
- prefixOverrides属性：指定要动态去掉的前缀，使用“|”分隔有可能的多个值
- suffixOverrides属性：指定要动态去掉的后缀，使用“|”分隔有可能的多个值

**mapper**

```xml
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="empName != null and empName != ''">
            emp_name = #{empName} and
        </if>
        <if test="age != null and age != ''">
            age = #{age} and
        </if>
        <if test="gender != null and gender != ''">
            gender = #{gender}
        </if>
    </trim>
</select>
```







### 8.4 choose、when、otherwise

在多个分支条件中，仅执行一个。

- 从上到下依次执行条件判断
- 遇到的第一个满足条件的分支会被执行
- 被执行分支后面的分支都将不被考虑
- 如果所有的when分支都不满足，那么就执行otherwise分支，相当于`if, else, else if`



**mapper**

```java
List<Emp> getEmpByChoose(Emp emp);
```

**映射文件**

```xml
<select id="getEmpByChoose" resultType="emp">
    select * from t_emp
    <where>
        <choose>
            <when test="empName != null and empName != ''">
                emp_name = #{empName}
            </when>
            <when test="age != null and age != ''">
                age = #{age}
            </when>
            <when test="gender != null and gender != ''">
                gender = #{gender}
            </when>
        </choose>
    </where>
</select>
```

**junt测试**

```java
@Test
public void testGetEmpByChoose(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    empMapper mapper = sqlSession.getMapper(empMapper.class);
    // select * from t_emp WHERE emp_name = ? 
    List<Emp> empByCondition = mapper.getEmpByChoose(new Emp(null, "zs", 20, "男"));
    System.out.println(empByCondition);
}
```





### 8.5 foreach

> 如果参数是list集合，MyBatis会将他放到Map中，以list为键，以list集合【当前参数】为值；如果是数组，MyBatis也会将他放到Map中，以Array为键，以当前参数为值。所以我们最好用`@Param()`注解来设置参数名。

`collection`：设置循环的数组或集合

`item`：遍历集合的过程中得到的每一个具体对象，在item属性中设置一个名字，将来通过这个名字引用遍历出来的对象

`separator`：设置每次循环数据之间的分隔符，前后会自动添加空格

`open`：指定整个循环把字符串拼好后，字符串整体的前面要添加的字符串

`close`：指定整个循环把字符串拼好后，字符串整体的后面要添加的字符串

`index`：这里起一个名字，便于后面引用

- 遍历List集合，这里能够得到List集合的索引值

- 遍历Map集合，这里能够得到Map集合的key

  ```XML
  <foreach collection="empList" item="emp" separator="," open="values" index="myIndex">
      <!-- 在foreach标签内部如果需要引用遍历得到的具体的一个对象，需要使用item属性声明的名称 -->
      (#{emp.empName},#{myIndex},#{emp.empSalary},#{emp.empGender})
  </foreach>
  ```

  



**批量添加**

**mapper**

```java
void addEmpMore(@Param("emps") List<Emp> emps);
```

**mapper**

```xml
<insert id="addEmpMore">
    insert into t_emp values
    <foreach collection="emps" item="emp" separator=",">
        (null, #{emp.empName}, #{emp.age}, #{emp.gender}, null)
    </foreach>
</insert>
```

**junit测试**

```java
@Test
public void testAddUserMore(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    empMapper mapper = sqlSession.getMapper(empMapper.class);
    Emp emp1 = new Emp(null, "小明1", 20, "男");
    Emp emp2 = new Emp(null, "小明2", 20, "男");
    Emp emp3 = new Emp(null, "小明3", 20, "男");
    List<Emp> emps = Arrays.asList(emp1, emp2, emp3);
    mapper.addEmpMore(emps);
}
```





**批量删除**

**mapper**

```java
void delEmpMore(@Param("empIds") Integer[] empIds);
```

**映射文件**

```xml
<delete id="delEmpMore">
    <!--
    delete from t_emp where emp_id in
    (
    <foreach collection="empIds" item="empId" separator=",">
        #{empId}
    </foreach>
    )
    -->
    <!-- 可以使用open和close标签设置开始和结束符号 -->
    <!--
    delete from t_emp where emp_id in
    <foreach collection="empIds" item="empId" separator="," open="(" close=")">
        #{empId}
    </foreach>
    -->

    delete from t_emp where
    <foreach collection="empIds" item="empId" separator="or">
        emp_id = #{empId}
    </foreach>
</delete>
```

**junit测试**

```java
@Test
public void testDelUserMore(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    empMapper mapper = sqlSession.getMapper(empMapper.class);
    Integer[] empIds = {5, 6, 7};
    mapper.delEmpMore(empIds);
}
```





### 8.6 sql

`sql`标签可以记录一段sql，在需要的地方使用`include`标签进行引用

```xml
<sql id="empColumns">
    emp_id, emp_name, age, gender, dept_id
</sql>
```

```xml
<select id="getEmp" resultType="emp">
    select <include refid="empColumns"/> from t_emp
</select>
```







## 9、MyBatis的缓存

> MyBatis中，如果两次查询的条件完全相同，那么在第一次查询后，会将数据缓存，第二次查询时会直接去缓存中获取数据。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307021636565.png)



**MyBatis中，缓存的本质是一个Map**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307021658286.png)





### 9.1 一级缓存

> MyBatis的一级缓存是sqlSession级别的，即使用同一个sqlSession查询条件相同时，会从缓存获取数据。一级缓存默认开启。
>
> 一级缓存中，查询结果会被缓存到HashMap中，查询语句和查询参数作为key，查询结果作为value。



**一级缓存失效的情况**

- 不是同一个SqlSession【这里缓存其实没有失效，只是因为是不同的sqlSession】
- 同一个SqlSession但是查询条件发生了变化【这里缓存没有失效，只是因为是不同的查询语句】
- 同一个SqlSession两次查询期间执行了任何一次增删改操作
- 同一个SqlSession两次查询期间手动清空了缓存



**mapper**

```java
public interface CacheMapper {
    Emp getEmpById(@Param("EmpId") Integer EmpId);

    void addEmp(Emp emp);
}
```

**映射文件**

```xml
<mapper namespace="com.xrj.mybatis.mapper.CacheMapper">
    <select id="getEmpById" resultType="Emp">
        select * from t_emp where emp_id = #{EmpId}
    </select>

    <insert id="addEmp">
        insert into t_emp values(null, #{empName}, #{age}, #{gender}, null)
    </insert>
</mapper>
```

**junit测试**

```java
@Test
public void testGetEmpById(){
    SqlSession sqlSession1 = sqlSessionUtil.getSqlSession();
    CacheMapper mapper1 = sqlSession1.getMapper(CacheMapper.class);
    // 这里只会执行一次sql语句，第二次查询时数据是从缓存中获取的, 两次查询的emp的hashCode相同
    Emp emp1 = mapper1.getEmpById(1);
    
    // 两次查询之间执行增删改操作会清空缓存, 因为查询的数据可能被修改, 此时查询出的两个emp的hashCode不同
    // mapper1.addEmp(new Emp(null, "aa", 20, "男"));

    // 两次查询期间，手动清除缓存，一级缓存失效
    // sqlSession1.clearCache();
    
    Emp emp2 = mapper1.getEmpById(1);
    System.out.println(emp1.hashCode());
    System.out.println(emp2.hashCode());

    // 因为是不同的sqlSession, 所以这里会再次执行sql语句
    SqlSession sqlSession2 = sqlSessionUtil.getSqlSession();
    CacheMapper mapper2 = sqlSession2.getMapper(CacheMapper.class);
    Emp emp3 = mapper2.getEmpById(1);
    System.out.println(emp3.hashCode());
}
```





### 9.2 二级缓存

> 二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取。二级缓存的实现是将查询结果序列化后存储在内存中，当需要查询相同的数据时，直接从缓存中获取序列化后的数据，因此实体类必须实现序列化接口
>
> 默认情况下，MyBatis的二级缓存使用内存存储，即将查询结果序列化后存储在JVM内存中。
>
> 如果使用磁盘存储，可以整合第三方框架`EHCache`



**二级缓存开启的条件：**

- 在核心配置文件中，设置全局配置属性`cacheEnabled="true"`，默认为true，不需要设置 
- 在映射文件中设置标签 `<cache/>`
- 二级缓存必须在SqlSession关闭或提交之后有效 
- 查询的数据所转换的实体类类型必须实现序列化的接口 



**使二级缓存失效的情况：** 两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效



**junit测试**

> SqlSession关闭的时候，一级缓存中的内容会被存入二级缓存，所以二级缓存必须在SqlSession关闭或提交之后有效 

```java
@Test
public void testCache() throws IOException {
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
    SqlSession sqlSession1 = sqlSessionFactory.openSession(true);
    SqlSession sqlSession2 = sqlSessionFactory.openSession(true);

    CacheMapper mapper1 = sqlSession1.getMapper(CacheMapper.class);
    CacheMapper mapper2 = sqlSession2.getMapper(CacheMapper.class);

    Emp emp1 = mapper1.getEmpById(1);
    sqlSession1.close();  // 必须提交或者关闭后二级缓存才会生效
    Emp emp2 = mapper2.getEmpById(1);
    sqlSession2.close();
    System.out.println(emp1.hashCode());
    System.out.println(emp2.hashCode());
}
```







### 9.3 二级缓存的相关配置

在mapper配置文件中添加的cache标签可以设置一些属性： 

①eviction属性：缓存回收策略，默认的是 LRU。 

LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。 

FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。 

SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象

WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。 

②flushInterval属性：刷新间隔，单位毫秒，默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新 

③size属性：引用数目，正整数 代表缓存最多可以存储多少个对象，太大容易导致内存溢出 

④**readOnly属性**：只读， true/false 

true：只读缓存；会给所有调用者返回缓存对象的相同实例【即直接将缓存中对象的地址传递过去】。因此这些对象不能被修改。这提供了很重 要的性能优势。 

false：读写缓存；**会返回缓存对象的拷贝（通过序列化）**。这会慢一些，但是安全，因此默认是 false。所以在二级缓存中，两次查询即使是使用了缓存，两次查询结果的hashCode也不同。







### 9.4 MyBatis缓存查询的顺序

先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。 

如果二级缓存没有命中，再查询一级缓存 

如果一级缓存也没有命中，则查询数据库 

SqlSession关闭之后，一级缓存中的数据会写入二级缓存







## 10、逆向工程

- 正向工程：先创建Java实体类，由框架负责根据实体类生成数据库表。Hibernate是支持正向工程的。
- 逆向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源：
  - Java实体类
  - Mapper接口
  - Mapper配置文件



**创建逆向工程步骤**

**1、添加插件和依赖**

```xml
<!-- 控制Maven在构建过程中相关配置 -->
<build>
    <!-- 构建过程中用到的插件 -->
    <plugins>
        <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.0</version>
            <!-- 插件的依赖 -->
            <dependencies>
                <!-- 逆向工程的核心依赖 -->
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.2</version>
                </dependency>
                <!-- MySQL驱动 -->
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.20</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```



**2、创建逆向工程的配置文件**

> 文件名必须是：generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
        targetRuntime: 执行生成的逆向工程的版本
        MyBatis3Simple: 生成基本的CRUD（清新简洁版）
        MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"
                        userId="root"
                        password="root">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.xrj.mybatis.pojo" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.xrj.mybatis.mapper" targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.xrj.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
```



配置完后，直接执行插件，就会自动创建pojo类，mapper接口以及mapper映射文件







## 11、分页查询

> 如果我们手动设置分页查询，sql语句写着比较容易，但是页面展示过于麻烦。首先如果当前是第一页，那么不能显示上一页和首页，如果是最后一页不能显示下一个和末页，同时在当前页显示导航分页也很麻烦【比如在第4页，显示2 3 4 5 6的页码】。所以我们使用分页插件帮我们实现分页



**分页插件使用步骤**

**1、添加依赖**

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
```

**2、配置分页插件**

在MyBatis的核心配置文件中配置插件

```xml
<plugins>
    <!--设置分页插件-->
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```



**junit测试**

**在查询功能之前使用`PageHelper.startPage(int pageNum, int pageSize)`开启分页功能**

执行了两条sql语句，首先查询所有记录条数，然后进行分页查询

如果查询的是第一页，`limit`后会省略第一个参数

`SELECT count(0) FROM t_emp`

`select emp_id, emp_name, age, gender, dept_id from t_emp LIMIT ?, ?`

```java
@Test
public void testPage(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    // 查询之前设置分页, 第2页, 每页两条数据
    PageHelper.startPage(2, 2);
    List<Emp> empList = mapper.selectByExample(null);
    System.out.println(empList);
}
```



**在查询获取list集合之后，使用`PageInfo pageInfo = new PageInfo<>(List list, int navigatePages)`获取分页相关数据**

> list：分页之后的数据 
>
> navigatePages：导航分页的页码数

```java
@Test
public void testPage(){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    // 查询之前设置分页, 返回值page包含一部分和分页相关的数据, Page类继承了ArrayList
    Page<Object> page = PageHelper.startPage(2, 2);
    List<Emp> empList = mapper.selectByExample(null);
    /*
        Page{count=true, pageNum=2, pageSize=2, startRow=2, endRow=4, total=6, pages=3, reasonable=false,pageSizeZero=false}
        [Emp{empId=3, empName='ww', age=30, gender='男', deptId=3}, Emp{empId=4, empName='zl', age=18, gender='女',deptId=1}]
    */
    System.out.println(page);

    // 查询功能过后, 可以获取分页相关的所有数据, 第一个参数是查询出来的list数据, 第二个参数是导航的页码数量
    PageInfo<Emp> empPageInfo = new PageInfo<>(empList, 2);
    /*
      PageInfo{pageNum=2, pageSize=2, size=2, startRow=3, endRow=4, total=6, pages=3, 
      list=Page{count=true, pageNum=2, pageSize=2, startRow=2, endRow=4, total=6, pages=3, reasonable=false, pageSizeZero=false}
      [Emp{empId=3, empName='ww', age=30, gender='男', deptId=3}, Emp{empId=4, empName='zl', age=18, gender='女', deptId=1}],
      prePage=1, nextPage=3, isFirstPage=false, isLastPage=false, hasPreviousPage=true, hasNextPage=true, 
      navigatePages=2, navigateFirstPage=1, navigateLastPage=2, navigatepageNums=[1, 2]}
    */ 
    System.out.println(empPageInfo);
}
```











# 二、Spring

**spring介绍**

Spring Framework：Spring 的基础框架，可以视为 Spring 基础设施，基本上任何其他 Spring 项目都是以 Spring Framework 为基础的。



**Spring Framework优良特性**

- 非侵入式：使用 Spring Framework 开发应用程序时，Spring 对应用程序本身的结构影响非常小。对领域模型可以做到零污染；对功能性组件也只需要使用几个简单的注解进行标记，完全不会破坏原有结构，反而能将组件结构进一步简化。这就使得基于 Spring Framework 开发应用程序时结构清晰、简洁优雅。
- 控制反转：IOC——Inversion of Control，翻转资源获取方向。把自己创建资源、向环境索取资源变成环境将资源准备好，我们享受资源注入。
- 面向切面编程：AOP——Aspect Oriented Programming，在不修改源代码的基础上增强代码功能。
    - 抽取重复代码：将方法内部重复的代码抽取出来
    - 代码增强：使用抽取出来的代码套用到某个独立功能上，就对这个独立功能进行了增强
- 容器：Spring IOC 是一个容器，因为它包含并且管理组件对象的生命周期。组件享受到了容器化的管理，替程序员屏蔽了组件创建过程中的大量细节，极大的降低了使用门槛，大幅度提高了开发效率。
- 组件化：Spring 实现了使用简单的组件配置组合成一个复杂的应用。在 Spring 中可以使用 XML 和 Java 注解组合这些对象。这使得我们可以基于一个个功能明确、边界清晰的组件有条不紊的搭建超大型复杂应用系统。
- 声明式：很多以前需要编写代码才能实现的功能，现在只需要声明需求即可由框架实现。
- 一站式：在IOC和 AOP 的基础上可以整合各种企业级应用的开源框架和优秀的第三方类库。而且 Spring 旗下的项目已经覆盖了广泛领域，很多方面的功能性需求可以在 Spring Framework 的基础上全部使用 Spring 来实现。



**Spring Framework五大功能模块**

| 功能模块                | 功能介绍                                                    |
| ----------------------- | ----------------------------------------------------------- |
| Core Container          | 核心容器，在 Spring 环境下使用任何功能都必须基于 IOC 容器。 |
| AOP&Aspects             | 面向切面编程                                                |
| Testing                 | 提供了对 junit 或 TestNG 测试框架的整合。                   |
| Data Access/Integration | 提供了对数据访问/集成的功能。                               |
| Spring MVC              | 提供了面向Web应用程序的集成功能。                           |







## 1、IOC容器

> Servlet 容器能够管理 Servlet、Filter、Listener 这样的组件的一生，所以它是一个复杂容器。
>
>  IOC 容器也是一个复杂容器。它们不仅要负责创建组件的对象、存储组件的对象，还要负责调用组件的方法让它们工作，最终在特定情况下销毁组件。
>
> 因此容器可以帮我们管理各种组件的生命周期，创建、销毁都由容器来完成，不需要我们操作。



> IOC：Inversion of Control，反转控制。
>
> 和javaWeb中一样，各种对象的创建由ioc容器操作，将创建对象的控制权交给了ioc容器
>
> DI：Dependency Injection，依赖注入。
>
> 将容器中的对象需要的资源注入进去





### **1.1 IOC在spring中的实现**

> IOC 容器中管理的组件也叫做 bean。在创建 bean 之前，首先需要创建 IOC 容器。Spring 提供了 IOC 容器的两种实现方式：

- BeanFactory接口

  > 这是 IOC 容器的基本实现，是 Spring 内部使用的接口。面向 Spring 本身，不提供给开发人员使用。

- ApplicationContext接口

  > BeanFactory 的子接口，提供了更多高级特性。面向 Spring 的使用者，几乎所有场合都使用 ApplicationContext 而不是底层的 BeanFactory



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307031756732.png)

| 类型名                          | 简介                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| ClassPathXmlApplicationContext  | 通过读取类路径下的 XML 格式的配置文件创建 IOC 容器对象       |
| FileSystemXmlApplicationContext | 通过文件系统路径读取 XML 格式的配置文件创建 IOC 容器对象     |
| ConfigurableApplicationContext  | ApplicationContext 的子接口，包含一些扩展方法 refresh() 和 close() ，让 ApplicationContext 具有启动、关闭和刷新上下文的能力。 |
| WebApplicationContext           | 专门为 Web 应用准备，基于 Web 环境创建 IOC 容器对象，并将对象引入存入 ServletContext 域中。 |





## 2、基于xml管理bean

### 2.1 入门案例

**1、导入依赖**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```



**2、创建实体类**

```java
public class HelloWorld {
    public void sayHello(){
        System.out.println("hello spring");
    }
}
```



**3、创建spring的配置文件**

> 在里边配置bean，id是bean的唯一标识，class是bean的全类名，用于spring通过反射创建对象，因此实体类必须有**无参构造**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="helloWorld" class="com.xrj.spring.pojo.HelloWorld"></bean>
</beans>
```



**4、junit测试**

```java
@Test
public void testHelloWorld(){
    // 创建ioc容器
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 获取ioc容器中的bean
    HelloWorld helloWorld = (HelloWorld) ioc.getBean("helloWorld");
    helloWorld.sayHello();
}
```





### 2.2 获取bean

**方式一：根据id获取**

> 由于 id 属性指定了 bean 的唯一标识，所以根据 bean 标签的 id 属性可以精确获取到一个组件对象

```java
@Test
public void testIoc(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 获取Bean的第一种方式: 根据id获取
    Student student1 = (Student) ioc.getBean("student");
    System.out.println(student1);
}
```





**方式二：根据类型获取**【常用】

`applicationContext`中配置的bean唯一

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.Student"></bean>
</beans>
```

```java
@Test
public void testIoc(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 获取Bean的第二种方式: 根据Class的类型获取
    Student student2 = ioc.getBean(Student.class);
    System.out.println(student2);
}
```



`applicationContext`中配置的bean不唯一

> 此时不能通过类型获取bean，因为spring不知道获取的是哪个实例对象

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.Student"></bean>
    <bean id="student2" class="com.xrj.spring.pojo.Student"></bean>
</beans>
```





**方式三：根据id和类型**

> 此时，即便xml中配置了相同的bean，也不会有问题

```java
@Test
public void testIoc(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 获取Bean的第三种方式: 根据id+类型获取
    Student student3 = ioc.getBean("student", Student.class);
    System.out.println(student3);
}
```





**注意：**根据类型来获取bean时，在满足bean唯一性的前提下，其实只是看：『对象 instanceof 指定的类型』的返回结果，只要返回的是true就可以认定为和类型匹配，能够获取到。

> 根据需要获取的对象的父类或父接口也可以得到

```java
@Test
public void testIoc(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 根据需要获取对象的父类以及父接口也可以获取到, student类实现了Person接口
    Person student4 = ioc.getBean(Person.class);
    System.out.println(student4);
}
```







### 2.3 依赖注入

#### 1. set注入

> 为bean对象的属性赋值
>
> `property`标签：通过组件类的`setXxx()`方法给组件对象设置属性
>
> `name`属性：指定属性名（这个属性名是`getXxx()`、`setXxx()`方法定义的，和成员变量无关
>
> `value`属性：指定属性值 -

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.Student">
        <property name="name" value="xrj"/>
        <property name="age" value="20"/>
        <property name="gender" value="男"/>
    </bean>
</beans>
```



```java
@Test
public void testValue(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    Student student = ioc.getBean("student", Student.class);
    // Student{name='xrj', age=20, gender='男'}
    System.out.println(student);
}
```





#### 2. 构造器注入

> 当前set、get方法已写好，现在有两个带三个参数的构造器
>
> `constructor-arg`表示是通过构造器进行赋值，value是构造器的参数值，name是参数名
>
> 假设不指定name属性，那么当前构造器注入的属性中18可以赋值给age，也可以赋值给score，那么哪个构造器在后边，这个属性就会赋值给谁。
>
> 如果想要指定18赋值给age属性，就需要用name标签来指定
>
> 使用构造器注入时，ioc创建对象是通过有参构造创建的，所以不需要无参构造

```java
public class Student implements Person{
    private String name;
    private Integer age;
    private String gender;
    private Double score;
    
    public Student(String name, String gender, Integer age) {
        this.name = name;
        this.gender = gender;
        this.age = age;
    }

    public Student(String name, String gender, Double score) {
        this.name = name;
        this.gender = gender;
        this.score = score;
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="studentTwo" class="com.xrj.spring.pojo.Student">
        <constructor-arg value="xyy"/>
        <constructor-arg value="女"/>
        <constructor-arg value="18" name="age"/>
    </bean>
</beans>
```





#### 3. 特殊值注入

```xml
<!-- 使用value属性给bean的属性赋值时，Spring会把value属性的值看做字面量, 将"张三"这个内容赋值给name属性 -->
<property name="name" value="张三"/>
```



**注入null**

```xml
<property name="name">
    <null />
</property>
```

> ```xml
> <property name="name" value="null"></property
> ```
>
> 以上写法，为name所赋的值是字符串null



**xml实体**

> xml中有的标签比如`<`不能直接用作属性来赋值，因为它表示标签的开始，小于号就使用`&lt;`  

```xml
<!-- 小于号在XML文档中用来定义标签的开始，不能随便使用 -->
<!-- 解决方案一：使用XML实体来代替 -->
<property name="expression" value="a &lt; b"/>
```



**CDATA节**

> CDATA中的C代表Character，是文本、字符的含义，
>
> CDATA就表示纯文本数据 
>
> CDATA中的所有数据都会被赋值

```xml
<property name="name">
    <value><![CDATA[<xrj>]]></value>
</property>
```





#### 4. 类属性赋值

> 新建班级类，学生类中有一个属性是班级
>
> ref属性：引用IOC容器中某个bean的id，将所对应的bean为属性赋值



**方式一：引用外部已声明的bean**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.Student">
        <property name="name" value="xrj"/>
        <property name="age" value="20"/>
        <property name="gender" value="男"/>
        <property name="clazz" ref="clazz"/>
    </bean>

    <bean id="clazz" class="com.xrj.spring.pojo.Clazz">
        <property name="cId" value="1"/>
        <property name="cName" value="一班"/>
    </bean>
</beans>
```





**方式二：内部bean**

> 在一个bean中再声明一个bean就是内部bean
>
> 内部bean只能用于给属性赋值，不能在外部通过IOC容器获取，因此可以省略id属性

```xml
<bean id="studentTwo" class="com.xrj.spring.pojo.Student">
    <property name="name" value="xrj"/>
    <property name="age" value="20"/>
    <property name="gender" value="男"/>
    <property name="clazz">
        <bean id="clazzInner" class="com.xrj.spring.pojo.Clazz">
            <property name="cId" value="2"/>
            <property name="cName" value="二班"/>
        </bean>
    </property>
</bean>
```





**方式三：级联属性赋值**

> 级联属性赋值时，必须先为该属性利用引用赋值，否则该属性为null，无法为其属性赋值

```xml
<bean id="studentThree" class="com.xrj.spring.pojo.Student">
    <property name="name" value="xrj"/>
    <property name="age" value="20"/>
    <property name="gender" value="男"/>
    <!-- 一定先引用某个bean为属性赋值，才可以使用级联方式更新属性 -->
    <property name="clazz" ref="clazz"/>
    <property name="clazz.cId" value="3"/>
    <property name="clazz.cName" value="三班"/>
</bean>
```





#### 5. 为数组类型属性赋值

> student类中添加hobby属性
>
> `private String[] hobby;`
>
> 为数组赋值时，需要使用`array`标签，数组的数据如果是普通类型就使用`value`，如果是引用类型就使用`ref`

```xml
<bean id="studentFore" class="com.xrj.spring.pojo.Student">
    <property name="name" value="xrj"/>
    <property name="age" value="20"/>
    <property name="gender" value="男"/>
    <property name="clazz" ref="clazz"/>
    <property name="hobby">
        <array>
            <value>足球</value>
            <value>篮球</value>
            <value>羽毛球</value>
        </array>
    </property>
</bean>
```







#### 6. 为集合类型属性赋值

> Clazz中有多个学生，因此多个学生作为一个集合
>
> `private List<Student> students;`



**方式一**

> 为list集合属性赋值，使用`list`标签，里边的数据如果是普通数据，就使用`value`标签，引用数据就使用`ref`标签
>
> 若为Set集合类型属性赋值，只需要将其中的list标签改为set标签即可

```xml
<bean id="clazz" class="com.xrj.spring.pojo.Clazz">
    <property name="cId" value="1"/>
    <property name="cName" value="一班"/>
    <property name="students">
        <list>
            <ref bean="student"/>
            <ref bean="studentFore"/>
        </list>
    </property>
</bean>
```



**方式二**

> 使用util命名空间下的list标签

```xml
<bean id="clazz" class="com.xrj.spring.pojo.Clazz">
    <property name="cId" value="1"/>
    <property name="cName" value="一班"/>
    <property name="students" ref="studentList"/>
</bean>

<util:list id="studentList">
    <ref bean="student"/>
    <ref bean="studentFore"/>
</util:list>
```







#### 7. 为Map类型属性赋值

> student类中新增一个Map属性
>
> `private Map<String, Teacher> teacherMap;`



**方式一**

> 为Map赋值时，使用`map`标签，里边是一个个`entry`，普通数据类型和引用数据类型分别用`key`、`key-ref`和`value`、`value-ref`标签

```xml
<bean id="teacher1" class="com.xrj.spring.pojo.Teacher">
    <property name="teacherId" value="1"/>
    <property name="teacherName" value="xyy"/>
</bean>

<bean id="teacher2" class="com.xrj.spring.pojo.Teacher">
    <property name="teacherId" value="2"/>
    <property name="teacherName" value="zs"/>
</bean>

<bean id="studentFive" class="com.xrj.spring.pojo.Student">
    <property name="name" value="xrj"/>
    <property name="age" value="20"/>
    <property name="gender" value="男"/>
    <property name="teacherMap">
        <map>
            <entry key="1" value-ref="teacher1"/>
            <entry key="2" value-ref="teacher2"/>
        </map>
    </property>
</bean>
```



**方式二**

> 使用util命名空间下的map标签

```xml
<bean id="studentFive" class="com.xrj.spring.pojo.Student">
    <property name="name" value="xrj"/>
    <property name="age" value="20"/>
    <property name="gender" value="男"/>
    <property name="teacherMap" ref="teacherMap"/>
</bean>

<util:map id="teacherMap">
    <entry key="1" value-ref="teacher1"/>
    <entry key="2" value-ref="teacher2"/>
</util:map>
```





#### 8. p命名空间

> 使用 p 名称空间的方式可以省略子标签 property，将组件属性的设置作为 bean 标签的属性来完成
>
> 引入p命名空间后，可以通过以下方式为bean的各个属性赋值

```xml
<bean id="studentSix" class="com.xrj.spring.pojo.Student" p:name="xrj" p:age="20"></bean>
```





#### 9. 引入外部属性文件

> 我们的对象可以交给ioc容器管理，那么我们创建的数据源也可以交给ioc容器去管理，然后通过getBean的方式获取数据源对象
>
> 在配置数据源的属性时，一般情况下数据库连接信息都会放在一个properties文件中，因为我们需要引入外部文件
>
> 通过`context`命名空间的`property-placeholder`可以引入外部文件



```xml
<!-- 引入外部属性文件 -->
<context:property-placeholder location="jdbc.properties"/>

<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
```

**junit测试**

```java
@Test
public void testDataSource() throws SQLException {
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    DruidDataSource dataSource = ioc.getBean(DruidDataSource.class);
    System.out.println(dataSource.getConnection());
}
```







### 2.4 bean的作用域

在Spring中可以通过配置bean标签的scope属性来指定bean的作用域范围

| 取值              | 含义                                        | 创建对象的时机   |
| ----------------- | ------------------------------------------- | ---------------- |
| singleton【默认】 | 在 IOC 容器中，这个 bean 的对象始终为单实例 | IOC 容器初始化时 |
| prototype         | 这个 bean 在 IOC 容器中有多个实例           | 获取 bean 时     |

如果是在WebApplicationContext环境下还会有另外两个作用域（但不常用）：

| 取值    | 含义                 |
| ------- | -------------------- |
| request | 在一个请求范围内有效 |
| session | 在一个会话范围内有效 |







### 2.5 bean的生命周期

1、bean 对象创建（调用无参构造器）

2、给 bean 对象设置属性（调用属性对应的 setter 方法）

3、bean 对象初始化之前操作（由 bean 的后置处理器负责）

4、bean 对象初始化（需在配置 bean 时指定初始化方法）

5、bean 对象初始化之后操作（由 bean 的后置处理器负责）

6、bean 对象就绪可以使用

7、bean 对象销毁（需在配置 bean 时指定销毁方法）

8、IOC 容器关闭



如果是单例模式，在ioc容器创建后，前4个生命周期就执行了。如果是多例模式，在获取bean的时候才会执行。



**student类**

> 以下仅类中部分方法
>
> 1、其中ioc容器创建bean对象是通过反射调用无参构造，所以首先会打印 `1、对象实例化`。
>
> 2、在ioc容器的配置中，设置了属性的注入，所以会调用set方法，因此会打印`2、设置属性`。
>
> 3、配置bean时配置了初始化方法，那么会执行`initMethod`方法。
>
> 4、获取对象实例
>
> 5、如果关闭ioc容器并且在配置bean时配置了销毁方法，那么会先执行`destroyMethod`方法，然后关闭ioc容器

```java
package com.xrj.spring.pojo;

import java.util.Arrays;
import java.util.Map;

public class Student implements Person{
    private String name;
    private Integer age;
    private String gender;
    private Double score;

    public Student() {
        System.out.println("1、对象实例化");
    }

    public void setName(String name) {
        System.out.println("2、设置属性");
        this.name = name;
    }

    public void initMethod(){
        System.out.println("3、初始化");
    }

    public void destroyMethod(){
        System.out.println("5、销毁");
    }
}
```

**ioc配置**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.Student" scope="prototype" init-method="initMethod" destroy-method="destroyMethod">
        <property name="name" value="xrj"/>
        <property name="age" value="20"/>
        <property name="gender" value="男"/>
    </bean>
</beans>
```

**junit测试**

> `ConfigurableApplicationContext`是`ApplicationContext`的子接口，它拥有关闭ioc和刷新ioc的方法，因此想要关闭ioc容器，需要使用该接口类型的ioc容器

```java
@Test
public void testLive(){
    ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("bean-live.xml");
    Student student = ioc.getBean("student", Student.class);
    System.out.println("4、获取bean并使用  " + student);
    ioc.close();
}
```





**bean的后置处理器**

> bean的后置处理器会在生命周期的初始化前后添加额外的操作，需要实现BeanPostProcessor接口， 且配置到IOC容器中，需要注意的是，bean后置处理器不是单独针对某一个bean生效，而是针对IOC容 器中所有bean都会执行



**bean的后置处理器**

> `postProcessBeforeInitialization`方法在bean初始化前执行
>
> `postProcessAfterInitialization`方法在bean初始化后执行

```java
package com.xrj.spring.pojo;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPostProcessor implements BeanPostProcessor {
    // bean 对象初始化之前操作, 这里的参数bean就是准备获取的bean对象, 可以在初始化之前做一些操作
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("bean初始化前的操作");
        return bean;
    }

    // bean 对象初始化之后操作
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("bean初始化后的操作");
        return bean;
    }
}
```

**配置到xml中**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.Student" scope="prototype" init-method="initMethod" destroy-method="destroyMethod">
        <property name="name" value="xrj"/>
        <property name="age" value="20"/>
        <property name="gender" value="男"/>
    </bean>

    <bean class="com.xrj.spring.pojo.MyBeanPostProcessor"></bean>
</beans>
```







### 2.6 FactoryBean

> FactoryBean是Spring提供的一种整合第三方框架的常用机制。
>
> 和普通的bean不同，配置一个 FactoryBean类型的bean，在获取bean的时候得到的并不是class属性中配置的这个类的对象，而是 `getObject()`方法的返回值。也就是说我们在ioc中配置一个FactoryBean，获取bean对象时，他会给我们返回这个工厂创建的对象，而不是说先获取工厂类对象，再通过工厂类对象去获取实例。
>
> 通过这种机制，Spring可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。 
>
> 整合Mybatis时，Spring就是通过FactoryBean机制来帮我们创建SqlSessionFactory对象的。



**FactoryBean**

> 实现了`FactoryBean`接口，里边的泛型指定的就是工厂创建的对象类型，`getObject()`方法就是获取该对象，`getObjectType()`获取对象的类型

```java
package com.xrj.spring.pojo;

import org.springframework.beans.factory.FactoryBean;

public class UserFactoryBean implements FactoryBean<Student> {

    @Override
    public Student getObject() throws Exception {
        return new Student();
    }

    @Override
    public Class<?> getObjectType() {
        return Student.class;
    }
}
```

**ioc配置**

> 这里如果注入属性，也是通过set方法给UserFactoryBean里的属性赋值

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.xrj.spring.pojo.UserFactoryBean"></bean>
</beans>
```

**junit测试**

```java
@Test
public void testFactoryBean(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("bean-live.xml");
    Student student = (Student) ioc.getBean("student");
    System.out.println(student);
}
```







### 2.7 基于xml的自动装配

根据指定的策略，在IOC容器中匹配某一个bean，自动为指定的bean中所依赖的**类**类型或**接口**类型属性赋值

- XML使用autowire属性设置自动装配，必须提供set方法以被调用，底层通过set方法进行属性注入；

- @Autowire放在成员变量上不需要set方法，底层直接通过反射获取成员变量再赋值



**创建`UserController`、`UserService`和`UserDao`**

```java
public class UserController {
    private UserService userService;

    public UserService getUserService() {
        return userService;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    public void save(){
        userService.save();
    }
}

public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public UserDao getUserDao() {
        return userDao;
    }

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        userDao.save();
    }
}

public class UserDaoImpl implements UserDao {
    @Override
    public void save() {
        System.out.println("保存成功");
    }
}
```



**配置ioc**

> 在bean中配置了`autowire`自动装配后，bean对象所需要的依赖会自动注入
>
> 自动装配有两种方式
>
> 1、byType ：根据类型匹配IOC容器中的某个兼容类型的bean，为属性自动赋值 
>
> 若在IOC中，没有任何一个兼容类型的bean能够为属性赋值，则该属性不装配，即值为默认值 null 
>
> 若在IOC中，有多个兼容类型的bean能够为属性赋值，则抛出异常 NoUniqueBeanDefinitionException
>
> 2、byName：将自动装配的属性的属性名作为bean的id，在IOC容器中匹配相对应的bean进行赋值

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="userController" class="com.xrj.spring.controller.UserController" autowire="byType"></bean>

    <bean id="userService" class="com.xrj.spring.service.impl.UserServiceImpl" autowire="byType"></bean>

    <bean id="userDao" class="com.xrj.spring.dao.impl.UserDaoImpl"></bean>
</beans>
```



**junit测试**

```java
@Test
public void testAutowire(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("spring-autowire-xml.xml");
    UserController userController = ioc.getBean(UserController.class);
    userController.save();
}
```







## 3、基于注解管理bean

### 3.1 标记与扫描

**注解**

和 XML 配置文件一样，注解本身并不能执行，注解本身仅仅只是做一个标记，具体的功能是框架检测 到注解标记的位置，然后针对这个位置按照注解标记的功能来执行具体操作。

本质上：所有一切的操作都是Java代码来完成的，XML和注解只是告诉框架中的Java代码如何执行。



**扫描**

Spring 为了知道程序员在哪些地方标记了什么注解，就需要通过扫描的方式，来进行检测。然后根据注解进行后续操作。



**标识组件的常用注解**

@Component：将类标识为普通组件 

@Controller：将类标识为控制层组件 

@Service：将类标识为业务层组件 

@Repository：将类标识为持久层组件

> @Controller、@Service、@Repository这三个注解只是在@Component注解 的基础上起了三个新的名字。 
>
> 对于Spring使用IOC容器管理这些组件来说没有区别。所以@Controller、@Service、@Repository这 三个注解只是给开发人员看的，让我们能够便于分辨组件的作用。
>
> 虽然它们本质上一样，但是为了代码的可读性，为了程序结构严谨不能随便胡乱标记





**组件扫描**

> context命名空间下的component-scan可以用来扫描包，如果检测到类上有标识组件的注解，就会自动将其配置为一个bean对象

情况一：最基本的扫描方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.xrj.spring"></context:component-scan>
</beans>
```

情况二：指定排除的组件

```xml
<context:component-scan base-package="com.xrj.spring">
    <!-- context:exclude-filter标签：指定排除规则 -->
    <!--
        type: 设置排除或包含的依据
        type="annotation", 根据注解排除, expression中设置要排除的注解的全类名
        type="assignable", 根据类型排除, expression中设置要排除的类型的全类名
    -->
    <context:exclude-filter type="assignable" expression="com.xrj.spring.controller.UserController"/>
</context:component-scan>
```

情况三：仅扫描指定组件

```xml
<context:component-scan base-package="com.xrj.spring" use-default-filters="false">
    <!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
    <!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 -->
    <!--
        type：设置排除或包含的依据
        type="annotation"，根据注解扫描，expression中设置要扫描的注解的全类名
        type="assignable"，根据类型扫描，expression中设置要扫描的类型的全类名
    -->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```



**junit测试**

```java
@Test
public void testScan(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserController userController = ioc.getBean(UserController.class);
    System.out.println(userController);
    UserServiceImpl userService = ioc.getBean(UserServiceImpl.class);
    System.out.println(userService);
    UserDaoImpl userDao = ioc.getBean(UserDaoImpl.class);
    System.out.println(userDao);
}
```





**组件所对应的bean的id**

> 在我们使用XML方式管理bean的时候，每个bean都有一个唯一标识，便于在其他地方引用。现在使用 注解后，每个组件仍然应该有一个唯一标识。

- 默认情况：类名首字母小写就是bean的id。例如：UserController类对应的bean的id就是userController。 

- 自定义bean的id：可通过标识组件的注解的value属性设置自定义的bean的id。

```java
@Service("userService")
public class UserServiceImpl implements UserService {}
```





### 3.2 基于注解的自动装配

使用@Autowired注解可以实现自动装配



**方式一：成员变量上**

> 在成员变量上直接标记@Autowired注解即可完成自动装配，不需要提供setXxx()方法。

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;

    @Override
    public void save() {
        userDao.save();
    }
}
```





**方式二：set方法上**

> 此时自动装配是通过set方法进行的

```java
@Service
public class UserServiceImpl implements UserService {

    private UserDao userDao;

    @Autowired
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        userDao.save();
    }
}
```





**方式三：构造器上**

> 此时不需要无参构造，因为有参构造上加了@Autowired注解，所以Spring通过反射调用的是有参构造创建对象并进行自动装配的

```java
@Service
public class UserServiceImpl implements UserService {
    
    private UserDao userDao;

    @Autowired
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        userDao.save();
    }
}
```





### 3.3 Autowired工作流程

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307051636159.png)



首先根据所需要的**组件类型**到IOC容器中查找 

- 能够找到唯一的bean：直接执行装配 

- 如果完全找不到匹配这个类型的bean：装配失败 

- 和所需类型匹配的bean不止一个 

  - 没有@Qualifier注解：根据@Autowired标记位置成员变量的变量名作为bean的id进行 匹配 
    - 能够找到：执行装配 
    - 找不到：装配失败 

  - 使用@Qualifier注解：根据@Qualifier注解中指定的名称作为bean的id进行匹配 

    - 能够找到：执行装配 
    - 找不到：装配失败

    

```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    @Qualifier("dao")
    private UserDao userDao;

    @Override
    public void save() {
        userDao.save();
    }
}
```

> @Autowired中有属性required，默认值为true，因此在自动装配无法找到相应的bean时，会装配失败。可以将属性required的值设置为false，则表示能装就装，装不上就不装，此时自动装配的属性为 默认值。但是实际开发时，基本上所有需要装配组件的地方都是必须装配的，用不上这个属性。





### 3.4 @Autowire与@Resource

`@Autowire`

- `@Autowired` 是 Spring 定义的注解

- 可用于构造方法，成员变量以及set方法【可以有多个参数，并且参数上可以使用`@Qualifies`进行标注】
- 先通过type查询，如果有多个type相同的bean，就通过name查询



`@Resource`

- `@Resource`是 Java 定义的注解（JDK自带）
- 用于成员变量以及set方法【只能是单个参数的setter方法】
- 先按照name属性值注入，若未找到，则按type属性值注入
  - 如果指定了name，则从上下文中查找名称匹配的bean进行装配，找不到则抛出异常
  - 如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
  - 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配到，就通过byType的方式进行装配







## 4、AOP

### 4.1 场景模拟

**声明接口**

> 声明计算器接口Calculator，包含加减乘除的抽象方法
>

```java
public interface Calculator {
    int add(int a, int b);

    int sub(int a, int b);

    int mul(int a, int b);

    int div(int a, int b);
}
```



**创建实现类**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307062202994.png)

```java
package com.xrj.spring.proxy;

public class CalculatorImpl implements Calculator{
    @Override
    public int add(int a, int b) {
        int result = a + b;
        System.out.println("方法内部 result = " + result);
        return result;
    }

    @Override
    public int sub(int a, int b) {
        int result = a - b;
        System.out.println("方法内部 result = " + result);
        return result;
    }

    @Override
    public int mul(int a, int b) {
        int result = a * b;
        System.out.println("方法内部 result = " + result);
        return result;
    }

    @Override
    public int div(int a, int b) {
        int result = a / b;
        System.out.println("方法内部 result = " + result);
        return result;
    }
}
```



**创建带日志功能的实现类**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307062202482.png)
```java
public class CalculatorLogImpl implements Calculator {
    
    @Override
    public int add(int i, int j) {
    
        System.out.println("[日志] add 方法开始了，参数是：" + i + "," + j);
    
        int result = i + j;
    
        System.out.println("方法内部 result = " + result);
    
        System.out.println("[日志] add 方法结束了，结果是：" + result);
    
        return result;
    }
    
    @Override
    public int sub(int i, int j) {
    
        System.out.println("[日志] sub 方法开始了，参数是：" + i + "," + j);
    
        int result = i - j;
    
        System.out.println("方法内部 result = " + result);
    
        System.out.println("[日志] sub 方法结束了，结果是：" + result);
    
        return result;
    }
    
    @Override
    public int mul(int i, int j) {
    
        System.out.println("[日志] mul 方法开始了，参数是：" + i + "," + j);
    
        int result = i * j;
    
        System.out.println("方法内部 result = " + result);
    
        System.out.println("[日志] mul 方法结束了，结果是：" + result);
    
        return result;
    }
    
    @Override
    public int div(int i, int j) {
    
        System.out.println("[日志] div 方法开始了，参数是：" + i + "," + j);
    
        int result = i / j;
    
        System.out.println("方法内部 result = " + result);
    
        System.out.println("[日志] div 方法结束了，结果是：" + result);
    
        return result;
    }
}
```




**存在的问题**

- 现有代码缺陷
  - 针对带日志功能的实现类，对核心业务功能有干扰，导致在开发核心业务功能时分散了精力
  - 附加功能分散在各个业务功能方法中，不利于统一维护

- 解决方法：解决这两个问题的核心就是：解耦。我们需要把附加功能从业务功能代码中抽取出来

- 解决问题的困难：要抽取的代码在方法内部，靠以前把子类中的重复代码抽取到父类的方式没法解决。 所以需要引入新的技术。





### 4.2 代理模式

**介绍**

> 代理模式是二十三种设计模式中的一种，属于结构型模式。它的作用就是通过提供一个代理类，让我们在调用目标方法的时候，不再是直接对目标方法进行调用，而是通过代理类间接调用。让不属于目标方法核心逻辑的代码从目标方法中剥离出来——解耦。调用目标方法时先调用代理对象的方法，减少对目标方法的调用和打扰，同时让附加功能能够集中在一起也有利于统一维护。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307062204415.png)





#### 4.2.1 静态代理

**创建静态代理类**

```java
public class CalculatorStaticProxy implements Calculator{
    private Calculator target;

    public CalculatorStaticProxy(Calculator target) {
        this.target = target;
    }

    @Override
    public int add(int a, int b) {
        System.out.println("[日志] add 方法开始了，参数是：" + a + "," + b);
        int result = target.add(a, b);
        System.out.println("[日志] add 方法结束了，结果是：" + result);
        return result;
    }

    @Override
    public int sub(int a, int b) {
        System.out.println("[日志] sub 方法开始了，参数是：" + a + "," + b);
        int result = target.sub(a, b);
        System.out.println("[日志] sub 方法结束了，结果是：" + result);
        return result;
    }

    @Override
    public int mul(int a, int b) {
        System.out.println("[日志] mul 方法开始了，参数是：" + a + "," + b);
        int result = target.mul(a, b);
        System.out.println("[日志] mul 方法结束了，结果是：" + result);
        return result;
    }

    @Override
    public int div(int a, int b) {
        System.out.println("[日志] div 方法开始了，参数是：" + a + "," + b);
        int result = target.div(a, b);
        System.out.println("[日志] div 方法结束了，结果是：" + result);
        return result;
    }
}
```

**junit测试**

> 有了静态代理类后，调用Calculate的方法时，就是通过调用代理类，然后代理类去调用对应方法

```java
@Test
public void testProxy(){
    CalculatorStaticProxy staticProxy = new CalculatorStaticProxy(new CalculatorImpl());
    int result = staticProxy.add(1, 2);
    System.out.println(result);
}
```



> 静态代理确实实现了解耦，但是由于代码都写死了，完全不具备任何的灵活性。
>
> 首先，静态代理类实现了Calculate接口，只能实现这一个静态代理类。并且静态代理类中的属性是Calculator类型的，也是固定的
>
> 对于日志功能来说，将来其他地方也需要附加日志，那还得再声明更多个静态代理类，那就产生了大量重复的代码，日志功能还是分散的，没有统一管理。 
>
> 提出进一步的需求：将日志功能集中到一个代理类中，将来有任何日志需求，都通过这一个代理类来实现。这就需要使用动态代理技术了。





#### 4.2.2 动态代理

**动态代理有两种:**

1、**JDK动态代理**，要求必须有接口，最终生成的代理类和目标类实现相同的接口，在com.sun.proxy包下, 类名为$proxy+数字

2、**cglib动态代理**，最终生成的代理类会继承目标类，并且和目标类在相同的包下

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307062208452.png)



**生产代理对象的工厂类**

> 动态代理类是根据目标对象的类型动态创建的，在ProxyFactory中，泛型T必须是目标对象实现的接口类型，不是接口类型会报错。
>
> ProxyFactory中的属性target就是代理的目标类，通过有参构造可以将target进行初始化
>
> 通过ProxyFactory的`getProxy`方法获取动态代理类，JDK中提供的Proxy类可以通过反射为我们创建动态代理类，`Proxy.newProxyInstance(classLoader, interfaces, invocationHandler);`  
>
> 第一个参数是一个类加载器，创建一个类需要经过类加载过程，所以必须有一个类加载器，通过任意一个类的Class对象都可以获取一个类加载器。
>
> 第二个参数是目标对象实现的所有接口的class对象组成的数组，因为代理类要和目标类实现相同的接口
>
> 第三个参数是设置代理对象实现目标对象方法的过程，即代理类如何重写接口中的抽象方法。`InvocationHandler`是一个接口，里边有一个抽象方法，我们通过匿名内部类实现，里边的`invoke`方法的第一个参数`proxy`是代理对象，第二个参数就是我们当前重写的方法，第三个是method对应的参数。我们通过反射可以执行目标对象的方法`method.invoke(target, args);`  并且在该方法执行前、执行后、发生异常、执行完毕时都可以执行一些操作，从而实现了功能的增强。

```java
package com.xrj.spring.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

// 泛型T要求是目标对象实现的接口类型，本代理类根据这个接口来进行代理
public class ProxyFactory<T> {
    // 将被代理的目标对象声明为成员变量
    private T target;

    public ProxyFactory(T target) {
        this.target = target;
    }

    public T getProxy(){
        /**
         * newProxyInstance()：创建一个代理实例
         * 其中有三个参数：
         * 1、classLoader：加载动态生成的代理类的类加载器
         * 2、interfaces：目标对象实现的所有接口的class对象所组成的数组
         * 3、invocationHandler：设置代理对象实现目标对象方法的过程，即代理类中如何重写接口中的抽象方法
         */
        ClassLoader classLoader = this.getClass().getClassLoader();
        Class<?>[] interfaces = target.getClass().getInterfaces();
        InvocationHandler invocationHandler = new InvocationHandler() {
            /**
             * proxy：代理对象
             * method：代理对象需要实现的方法，即其中需要重写的方法
             * args：method所对应方法的参数
            **/
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                Object result = null;
                try {
                    System.out.println("[日志] " + method.getName() + " 方法开始了，参数是：" + Arrays.toString(args));
                    result = method.invoke(target, args);
                    System.out.println("[日志] " + method.getName() + " 方法结束了，结果是：" + result);
                } catch (Exception e) {
                    System.out.println("[日志] " + method.getName() + " 方法产生异常, " + e);
                } finally {
                    System.out.println("[日志] " + method.getName() + " 方法执行完毕");
                }
                return result;
            }
        };
        return (T) Proxy.newProxyInstance(classLoader, interfaces, invocationHandler);
    }
}
```

**junit测试**

> 使用ProxyFactory创建动态代理类时，我们传入什么类型的对象，就会对该对象进行功能的增强

```java
@Test
public void testDynamicProxy(){
    // 创建能够生产代理对象的工厂对象
    ProxyFactory<Calculator> proxyFactory = new ProxyFactory<Calculator>(new CalculatorImpl());
    // 通过工厂对象生产目标对象的代理对象，因为代理对象是动态生成的，不知道他的类型，所以可以使用它实现的接口类型来接收
    Calculator proxy = proxyFactory.getProxy();
    proxy.div(1, 0);
}
```







### 4.3 AOP概念

> AOP（Aspect Oriented Programming）是一种设计思想，是软件设计领域中的面向切面编程，它是面向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况下给程序动态统一添加额外功能的一种技术。
>
> 面向对象是**纵向继承**机制，而面向切面编程是**横向抽取**机制
>
> AOP：将目标对象的非核心代码抽取出来，抽取出来的部分叫做**横切关注点**，将抽取出来的非核心代码即**横切关注点**放到一个类中，这个类就是**切面**，每个横切关注点在切面中都是一个方法，这个方法叫做**通知**。抽取非核心代码的位置叫做**连接点**，非核心代码抽取出来还需要将其套进核心代码中，通过**切入点**定位连接点的位置，将非核心代码套进核心代码中。



**横切关注点**

> 从每个方法中抽取出来的同一类非核心业务。
>
> 在同一个项目中，我们可以使用多个**横切关注点**对相关方法进行多个不同方面的增强。 
>
> 这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个**横切关注点**。



**通知**

> 每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法

- 前置通知：在被代理的目标方法前执行 

- 返回通知：在被代理的目标方法成功结束后执行 

- 异常通知：在被代理的目标方法异常结束后执行 

- 后置通知：在被代理的目标方法最终结束后执行

- 环绕通知：使用try...catch...finally结构围绕整个被代理的目标方法，等价于上面四种通知对应的所有位置

  

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307072011669.png)



**切面**

> 封装通知方法的类



**目标**

> 被代理的目标对象



**代理**

> 向目标对象应用通知之后创建的代理对象



**连接点**

> 抽取横切关注点的位置。这是一个纯逻辑概念，不是语法定义的。



**切入点**

> 定位连接点的方式。 
>
> 每个类的方法中都包含多个连接点，所以连接点是类中客观存在的事物（从逻辑上来说）。 
>
> Spring 的 AOP 技术可以通过切入点定位到特定的连接点。切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。







### 4.4 基于注解的AOP

> Spring中通过AspectJ实现AOP
>
> AspectJ注解底层的实现涉及到**JDK动态代理**和**CGLIB**两种技术。
>
> 在使用AspectJ注解时，如果被代理的类实现了接口，那么AspectJ会使用JDK动态代理来生成代理类。如果被代理的类没有实现接口，那么AspectJ会使用CGLIB来生成代理类。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307072015030.png)



- 动态代理（InvocationHandler）：JDK原生的实现方式，需要被代理的目标类必须实现接口。因为这个技术要求代理对象和目标对象实现同样的接口
- cglib：通过继承被代理的目标类实现代理，所以不需要目标类实现接口。
- AspectJ：本质上是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最终效果是动态的。weaver就是织入器。Spring只是借用了AspectJ中的注解。





**1、导入依赖**

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>
```



**2、准备被代理的目标资源**

**接口**

```java
public interface Calculator {
    int add(int a, int b);

    int sub(int a, int b);

    int mul(int a, int b);

    int div(int a, int b);
}
```



**实现类**

```java
@Component
public class CalculatorImpl implements Calculator {
    @Override
    public int add(int a, int b) {
        int result = a + b;
        System.out.println("方法内部 result = " + result);
        return result;
    }

    @Override
    public int sub(int a, int b) {
        int result = a - b;
        System.out.println("方法内部 result = " + result);
        return result;
    }

    @Override
    public int mul(int a, int b) {
        int result = a * b;
        System.out.println("方法内部 result = " + result);
        return result;
    }

    @Override
    public int div(int a, int b) {
        int result = a / b;
        System.out.println("方法内部 result = " + result);
        return result;
    }
}
```



**3、创建切面类**

**(1) 在切面中，需要通过指定的注解将方法标识为通知方法**

 * @Before：前置通知，在目标对象方法执行之前执行
 * @After：后置通知，在目标对象方法的finally字句中执行
 * @AfterReturning：返回通知，在目标对象方法返回值之后执行

- @AfterThrowing：异常通知，在目标对象方法的catch字句中执行



**(2) 切入点表达式：设置在标识通知的注解的value属性中**

`execution(public int com.xrj.spring.aop.annotation.CalculatorImpl.add(int, int))`

`execution(* com.atguigu.spring.aop.annotation.CalculatorImpl.*(..)`

第一个`*`表示任意的访问修饰符和返回值类型

第二个`*`表示类中任意的方法

`..`表示任意的参数列表

类的地方也可以使用*，表示包下所有的类

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307072244118.png)



**(3) 重用切入点表达式**

> 同一个类中可以使用引用，不同类中也可以，只需要指定全类名即可，因为重用切入点就是一个方法，上边加了注解

`@Pointcut`声明一个公共的切入点表达式

```JAVA
@Pointcut("execution(* com.atguigu.spring.aop.annotation.CalculatorImpl.*(..))")
public void pointCut(){}
```

使用方式：`@Before("pointCut()")`




**(4) 获取连接点的信息**

在通知方法的参数位置，设置`JoinPoint`类型的参数，就可以获取连接点所对应方法的信息

```JAVA
Signature signature = joinPoint.getSignature();   //获取连接点所对应方法的签名信息
```

```JAVA
Object[] args = joinPoint.getArgs();   //获取连接点所对应方法的参数
```



**(5) 切面的优先级**

> 如果两个切面类优先级相同，则根据他们注册的顺序执行

```java
@Component
@Aspect
@Order(value = 1)
public class ValidateAspect {
    @Before("com.xrj.spring.aop.annotation.LoggerAspect.pointCut()")
    public void beforeMethod(){
        System.out.println("valid 前置方法执行");
    }
}
```

可以通过@Order注解的value属性设置优先级，默认值Integer的最大值

@Order注解的value属性值越小，优先级越高




```java
package com.xrj.spring.aop.annotation;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;
import java.util.Arrays;

// @Component注解保证这个切面类能够放入IOC容器
@Component
// @Aspect表示这个类是一个切面类
@Aspect
public class LoggerAspect {

    @Pointcut("execution(* com.xrj.spring.aop.annotation.CalculatorImpl.*(..))")
    public void pointCut(){};

    // @Before注解：声明当前方法是前置通知方法
    // value属性：指定切入点表达式，由切入点表达式控制当前通知方法要作用在哪一个目标方法上
    @Before("pointCut()")
    public void beforeMethod(JoinPoint joinPoint){
        Signature signature = joinPoint.getSignature();
        Object[] args = joinPoint.getArgs();
        System.out.println("前置通知, " + signature.getName() + ", 参数 " + Arrays.toString(args));
    }

    @After("pointCut()")
    public void afterMethod(JoinPoint joinPoint){
        Signature signature = joinPoint.getSignature();
        System.out.println("后置通知, " + signature.getName() + ", 方法执行完毕 ");
    }

    /**
     * 在返回通知中如果要获取对象方法的返回值
     * 只需要通过@AfterReturning注解的returning属性
     * 就可以将通知方法的某个参数指定为接收目标对象方法的返回值参数
     */
    @AfterReturning(value = "pointCut()", returning = "result")
    public void alterReturnMethod(JoinPoint joinPoint, Object result){
        Signature signature = joinPoint.getSignature();
        System.out.println("返回通知, 方法" + signature.getName() + ", 结果: " + result);
    }

    /**
     * 在异常通知中如果要获取对象方法的异常
     * 只需要通过@AfterThrowing注解的throwing属性
     * 就可以将通知方法的某个参数指定为接收目标对象方法的异常值参数
     */
    @AfterThrowing(value = "pointCut()", throwing = "ex")
    public void throwing(JoinPoint joinPoint, Exception ex){
        Signature signature = joinPoint.getSignature();
        System.out.println("异常通知, 方法" + signature.getName() + ", 异常: " + ex);
    }
}
```



**4、配置**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--
        基于注解的AOP的实现：
        1、将目标对象和切面交给IOC容器管理（注解+扫描）
        2、开启AspectJ的自动代理，为目标对象自动生成代理
        3、将切面类通过注解@Aspect标识
    -->
    <context:component-scan base-package="com.xrj.spring.aop.annotation"></context:component-scan>

    <aop:aspectj-autoproxy/>
</beans>
```



**环绕通知**

> 环绕通知相当于上边四种通知的组合。
>
> 环绕通知更类似于动态代理，调用目标方法实际是通过动态代理来调用，所以返回值也是通过动态代理返回

```java
// 环绕通知, 环绕通知更类似于动态代理，调用目标方法实际是通过动态代理来调用，所以返回值也是通过动态代理返回
// 环绕通知必须有返回值，返回值就是目标方法的返回值
@Around(value = "pointCut()")
public Object aroundMethod(ProceedingJoinPoint joinPoint){
    Object result = null;
    try {
        System.out.println("环绕通知 -> 前置通知");
        // 目标方法的执行
        result = joinPoint.proceed();
        System.out.println("环绕通知 -> 返回通知");
    } catch (Throwable e) {
        e.printStackTrace();
        System.out.println("环绕通知 -> 异常通知");
    } finally {
        System.out.println("环绕通知 -> 后置通知");
    }
    return result;
}
```



**junit测试**

> 因为当前是实现接口的方式，所以获取bean对象时，根据接口的类型就可以获取到动态代理对象
>
> 在通过AOP的切面对目标方法实现功能增强后，在ioc中就不能直接获取目标对象了，必须通过代理对象去执行相应的方法

```java
@Test
public void testAspect(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("aop-annotation.xml");
    Calculator bean = ioc.getBean(Calculator.class);
    bean.add(1, 8);
}
```







### 4.5 基于xml的AOP

> 基于xml的AOP就是将注解中的所有配置使用xml的方式进行配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.xrj.spring.aop.xml"></context:component-scan>

    <aop:config>
        <aop:pointcut id="pointCut" expression="execution(* com.xrj.spring.aop.xml.CalculatorImpl.*(..))"/>
        <aop:aspect ref="loggerAspect">
            <aop:around method="aroundMethod" pointcut-ref="pointCut"></aop:around>
            <aop:before method="beforeMethod" pointcut-ref="pointCut"></aop:before>
            <aop:after-returning method="alterReturnMethod" pointcut-ref="pointCut" returning="result"></aop:after-returning>
            <aop:after-throwing method="throwing" pointcut-ref="pointCut" throwing="ex"></aop:after-throwing>
            <aop:after method="afterMethod" pointcut-ref="pointCut"></aop:after>
        </aop:aspect>
        <aop:aspect ref="validateAspect" order="1">
            <aop:before method="beforeMethod" pointcut-ref="pointCut"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```









### 4.6 AOP对获取bean的影响

#### 获取bean

**动态代理**

- 声明一个接口
- 接口有一个实现类
- 创建一个切面类，对上面接口的实现类应用通知
    - 测试：根据接口类型获取bean【可以获取】
    - 测试：根据目标类获取bean【获取不到】

**原因：**

- 应用了切面后，真正放在IOC容器中的是代理类的对象

- 目标类并没有被放到IOC容器中，所以根据目标类的类型从IOC容器中是找不到的

  > 从内存分析的角度来说，IOC容器中引用的是代理对象，代理对象引用的是目标对象。IOC容器并没有直接引用目标对象，所以根据目标类本身在IOC容器范围内查找不到。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307081116920.png)



**cglib**

- 声明一个类

- 创建一个切面类，对上面的类应用通知
    - 测试：根据类获取 bean，能获取到
    
    > cglib是通过继承目标类的方式实现代理的，所以通过目标类就可以获取代理对象

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307081115505.png)





#### 自动装配

> 自动装配需先从 IOC 容器中获取到唯一的一个 bean 才能够执行装配。所以装配能否成功和装配底层的原理，和前面测试的获取 bean 的机制是一致的。



**情景一**

- 目标bean对应的类没有实现任何接口

- 根据bean本身的类型装配这个bean
    - 测试：IOC容器中同类型的bean只有一个

        正常装配
        
    - 测试：IOC容器中同类型的bean有多个
    
        @Autowired注解会先根据类型查找，此时会找到多个符合的bean，然后根据成员变量名作为bean的id进一步筛选，如果没有id匹配的，则会抛出NoUniqueBeanDefinitionException异常，表示IOC容器中这个类型的bean有多个



**情景二**

- 目标bean对应的类实现了接口，这个接口也只有这一个实现类，也没有被切面应用通知
    - 测试：根据接口类型装配bean

        正常
    - 测试：根据类装配bean

        正常



**情景三**

- 声明一个接口

- 接口有多个实现类

- 接口所有实现类都放入IOC容器
    - 测试：根据接口类型装配bean

        @Autowired注解会先根据类型查找，此时会找到多个符合的bean，然后根据成员变量名作为bean的id进一步筛选，如果没有id匹配的，则会抛出NoUniqueBeanDefinitionException异常，表示IOC容器中这个类型的bean有多个
        
    - 测试：根据类装配bean
    
        正常





**情景四**

- 声明一个接口

- 接口有一个实现类

- 创建一个切面类，对上面接口的实现类应用通知
    - 测试：根据接口类型装配bean

        正常
        
    - 测试：根据类装配bean
    
        此时获取不到对应的bean，所以无法装配





**情景五**

- 声明一个类
- 创建一个切面类，对上面的类应用通知
    - 测试：根据类装配bean

        正常







#### 总结

**对实现了接口的类应用切面**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307081128402.png)

**对没有实现接口的类应用切面**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307081128526.png)





## 5、声明式事务

### 5.1 JdbcTemplate

> Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作



**1、导入依赖**

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 持久化层支持jar包 -->
    <!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个jar包 -->
    <!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 测试相关 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.20</version>
    </dependency>
    <!-- 数据源 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.0.31</version>
    </dependency>
</dependencies>
```



**2、设置spring配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <bean class="org.springframework.jdbc.core.JdbcTemplate">
        <!-- 配置数据源 -->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
</beans>
```



**3、测试**

> 这里通过spring整合junit的方式来测试
>
> 以前测试时，我们要先获取ioc容器，然后从ioc容易再去获取相应的bean对象，然后执行。
>
> 使用spring整合junit后，不需要自己创建IOC容器，而且任何需要的bean都可以在测试类中直接享受自动装配

```java
// 指定当前测试类在spring的测试环境中执行，此时可以通过注入的方式直接获取ioc容器中的bean
@RunWith(SpringJUnit4ClassRunner.class)
// 设置spring测试环境的配置文件
@ContextConfiguration("classpath:spring-transaction.xml")
public class testJdbcTemplate {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void testInsert(){
        String sql = "insert into t_user values(null, ?, ?, ?, ?, ?)";
        jdbcTemplate.update(sql, "admin", "123456", 20, "男", "123@qq.com");
    }
    @Test
    public void testDelete(){
        String sql = "delete from t_user where id = ?";
        jdbcTemplate.update(sql, 1);
    }
    @Test
    public void testUpdate(){
        String sql = "update t_user set age = ? where id = ?";
        jdbcTemplate.update(sql, 18, 2);
    }
    @Test
    public void selectById(){
        String sql = "select * from t_user where id = ?";
        User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), 2);
        System.out.println(user);
    }
    @Test
    public void selectAllUser(){
        String sql = "select * from t_user";
        List<User> users = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(User.class));
        System.out.println(users);
    }
    @Test
    public void selectOne(){
        String sql = "select count(*) from t_user";
        Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
        System.out.println(count);
    }
}
```







### 5.2 声明式事务

**编程式事务**

> 事务功能的相关操作全部通过自己编写代码来实现

```java
Connection conn = ...;
try {
	// 开启事务：关闭事务的自动提交
	conn.setAutoCommit(false);
	// 核心操作
	// 提交事务
	conn.commit();
}catch(Exception e){
	// 回滚事务
	conn.rollBack();
}finally{
	// 释放数据库连接
	conn.close();
}
```

编程式的实现方式存在缺陷： 

- 细节没有被屏蔽：具体操作过程中，所有细节都需要程序员自己来完成，比较繁琐。 
- 代码复用性不高：如果没有有效抽取出来，每次实现功能都需要自己编写代码，代码就没有得到复用。



**声明式事务**

既然事务控制的代码有规律可循，代码的结构基本是确定的，所以框架就可以将固定模式的代码抽取出 来，进行相关的封装。 

封装起来后，我们只需要在配置文件中进行简单的配置即可完成操作。 

- 好处1：提高开发效率 

- 好处2：消除了冗余的代码 

- 好处3：框架会综合考虑相关领域中在实际开发环境下有可能遇到的各种问题，进行了健壮性、性能等各个方面的优化 

  

**编程式：自己写代码实现功能** 

**声明式：通过配置让框架实现功能**





**事物管理器**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307151828097.png)

我们现在要使用的事务管理器是org.springframework.jdbc.datasource.DataSourceTransactionManager，将来整合 Mybatis 用的也是这个类。

DataSourceTransactionManager类中的主要方法：

- doBegin()：开启事务
- doSuspend()：挂起事务
- doResume()：恢复挂起的事务
- doCommit()：提交事务
- doRollback()：回滚事务





#### 5.2.1 基于注解的声明式事务

> 模拟买书功能，用户买书时的操作：先查询这本书的价钱，然后将书的库存减一，同时将将用户的余额减去。如果价钱和库存使用int类型，那么为负数时也不会抛异常，所以我们可以将该字段设置为无符号的，这样只要为负就会抛异常。或者每次执行时先判断库存或者余额是否够，不够就手动抛异常
>
> 如果没有使用事务，当用户余额不够时，书的库存减了，但是用户余额却没有减。



**设置spring配置文件**

> 1、设置事物管理器。事务管理器就是spring帮我们实现的切面和通知，因此我们只需要设置切入点即可。
>
> 2、开启事物的注解驱动。使用`transaction-manager`属性指定当前使用的事务管理器。然后在我们需要添加事物操作的方法或类上加上`@Transactional`注解，那么该方法或类【类中所有方法】执行时，就会有事物操作。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
    <context:property-placeholder location="jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <bean class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <context:component-scan base-package="com.xrj.spring"/>

    <!--
        设置事务管理器, 这个相当于是我们的切面, spring已经帮我们把切面和通知实现好了, 我们只需要设置切入点即可
    -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--
        开启事务的注解驱动
        通过注解@Transactional所标识的方法或标识的类中所有的方法，都会被事务管理器管理事务
        使用transaction-manager属性指定当前使用是事务管理器的bean
    -->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```



**创建组件**

```java
@Controller
public class UserController {
    @Autowired
    private UserService userService;

    public void buyBook(Integer userId, Integer bookId){
        userService.buyBook(userId, bookId);
    }
}
```

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;
    @Autowired
    private BookDao bookDao;

    @Transactional
    @Override
    public void buyBook(Integer userId, Integer bookId) {
        // 查询图书价格
        int price = bookDao.getPrice(bookId);
        // 修改库存
        bookDao.updateStock(bookId);
        // 修改余额
        userDao.updateBalance(userId, price);
    }
}
```

```java
@Repository
public class BookDaoImpl implements BookDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public int getPrice(Integer bookId) {
        String sql = "select price from t_book where book_id = ?";
        return jdbcTemplate.queryForObject(sql, Integer.class, bookId);
    }

    @Override
    public void updateStock(Integer bookId) {
        String sql = "update t_book set stock = stock - 1 where book_id = ?";
        jdbcTemplate.update(sql, bookId);
    }
}
```

```java
@Repository
public class UserDaoImpl implements UserDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public void updateBalance(Integer userId, Integer price) {
        String sql = "update t_user set balance = balance - ? where user_id = ?";
        jdbcTemplate.update(sql, price, userId);
    }
}
```



**junit测试**

> 在service层加入了事物后，只要任何一个操作出现异常，所有的操作都会回滚
>
> 因为我们在service层加入了事物操作，相当于使用AOP对service层做了功能增强，因为这里是实现了接口，所以使用的是jdk动态代理，因此通过bookServiceImpl类无法获取bean对象

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:tx-transaction.xml")
public class testTransaction {
    @Autowired
    private UserController userController;

    @Test
    public void test(){
       userController.buyBook(1, 1);
    }
}
```





**@Transactional注解标识的位置**

@Transactional标识在方法上，只会影响该方法 

@Transactional标识的类上，影响类中所有的方法

> 类级别标记的 @Transactional 注解中设置的事务属性会延续影响到方法执行时的事务属性。除非在方法上又设置了 @Transactional 注解。
>
> 对一个方法来说，离它最近的 @Transactional 注解中的事务属性设置生效





#### 5.2.2 事务属性

##### 1. 只读

> 对一个查询操作来说，如果我们把它设置成只读，就能够明确告诉数据库，这个操作不涉及写操作。这样数据库就能够针对查询操作来进行优化【例如：不加锁】。
>
> 将`@Transactional`注解的`readOnly`属性设置为true就表示只读
>
> 如果设置了只读后，出现了增删改操作，就会抛出异常`Caused by: java.sql.SQLException: Connection is read-only. Queries leading to data modification are not allowed`

```java
@Transactional(readOnly = true)
@Override
public void buyBook(Integer userId, Integer bookId) {
    // 查询图书价格
    int price = bookDao.getPrice(bookId);
    // 修改库存
    bookDao.updateStock(bookId);
    // 修改余额
    userDao.updateBalance(userId, price);
    //System.out.println(1/0);
}
```





##### 2. 超时

> 事务在执行过程中，有可能因为遇到某些问题，导致程序卡住，从而长时间占用数据库资源。而长时间 占用资源，大概率是因为程序运行出现了问题（可能是Java程序或MySQL数据库或网络连接等等）。 此时这个很可能出问题的程序应该被回滚，撤销它已做的操作，事务结束，把资源让出来，让其他正常程序可以执行。
>
> 即超时就回滚

```java
@Transactional(timeout = 3)
@Override
public void buyBook(Integer userId, Integer bookId) {
    try {
        TimeUnit.SECONDS.sleep(5);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    // 查询图书价格
    int price = bookDao.getPrice(bookId);
    // 修改库存
    bookDao.updateStock(bookId);
    // 修改余额
    userDao.updateBalance(userId, price);
}
```





##### 3. 回滚策略

> 声明式事务默认只针对运行时异常回滚，编译时异常不回滚。 

可以通过@Transactional中相关属性设置回滚策略 

- rollbackFor属性：需要设置一个Class类型的对象 
- rollbackForClassName属性：需要设置一个字符串类型的全类名 
- noRollbackFor属性：需要设置一个Class类型的对象 
- noRollbackForClassName属性：需要设置一个字符串类型的全类名

> 声明式事务默认情况下针对所有的运行时异常都会回滚。
>
> 如果使用`rollbackFor`或`rollbackForClassName`指定了特定异常回滚后，没有指定的运行时异常产生后就不会回滚
>
> 使用`noRollbackFor`或`noRollbackForClassName`指定了异常后，产生该异常就不会回滚

```java
@Transactional(
    // noRollbackFor = ArithmeticException.class
    noRollbackForClassName = "java.lang.ArithmeticException"
)
@Override
public void buyBook(Integer userId, Integer bookId) {
    // 查询图书价格
    int price = bookDao.getPrice(bookId);
    // 修改库存
    bookDao.updateStock(bookId);
    // 修改余额
    userDao.updateBalance(userId, price);
    System.out.println(1/0);
}
```





##### 4. 事务隔离级别

> 数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响，避免各种并发问题。
>
> 一个事务与其他事务隔离的程度称为隔离级别。
>
> SQL标准中规定了多种事务隔离级别，不同隔离级别对应不同的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱。



隔离级别一共有四种： 

- 读未提交：READ UNCOMMITTED 

  > 允许Transaction01读取Transaction02未提交的修改。 

- 读已提交：READ COMMITTED

  > 要求Transaction01只能读取Transaction02已提交的修改。 

- 可重复读：REPEATABLE READ 

  > 确保Transaction01可以多次从一个字段中读取到相同的值，即Transaction01执行期间禁止其它事务对这个字段进行更新。 

- 串行化：SERIALIZABLE 

  > 确保Transaction01可以多次从一个表中读取到相同的行，在Transaction01执行期间，禁止其它事务对这个表进行添加、更新、删除操作。可以避免任何并发问题，但性能十分低下。



> 在Mysql中，可重复读的隔离级别不会出现幻读，所以mysql默认隔离级别就是可重复读

| 隔离级别         | 脏读 | 不可重复读 | 幻读 |
| ---------------- | ---- | ---------- | ---- |
| READ UNCOMMITTED | 有   | 有         | 有   |
| READ COMMITTED   | 无   | 有         | 有   |
| REPEATABLE READ  | 无   | 无         | 有   |
| SERIALIZABLE     | 无   | 无         | 无   |



**使用方式**

```java
@Transactional(isolation = Isolation.DEFAULT)			//使用数据库默认的隔离级别
@Transactional(isolation = Isolation.READ_UNCOMMITTED)	//读未提交
@Transactional(isolation = Isolation.READ_COMMITTED)	//读已提交
@Transactional(isolation = Isolation.REPEATABLE_READ)	//可重复读
@Transactional(isolation = Isolation.SERIALIZABLE)		//串行化
```





##### 5. 事务传播行为

> 当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。
>
> 例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行
>
> 结账操作：结账操作买了两本书，结账操作有事物管理，买书也有事物管理，买第一本书是正常的，买第二本书产生异常，此时整个checkOut操作都会回滚
>
> 如果将买书的`@Transactional`的`propagation`属性设置为` Propagation.REQUIRES_NEW)`，表示开启一个新的事物，那么第一本书正常买，买第二本书时发生异常，只会回滚买第二本书的操作，买第一本书的操作不会被回滚。`propagation`属性默认是`Propagation.REQUIRED`
>
> 注意：结账操作和买书操作要在不同的类中实现，才会有这样的效果。
>
> 因为将两个方法分别放在不同的类中，则Spring 会为每个类生成一个代理对象，并在代理对象中实现事务管理。因此，每个方法都有自己独立的事务管理器，互不影响。
>
> 但是如果将两个方法放在同一个类中，则 Spring 只会为该类生成一个代理对象，并在代理对象中实现事务管理。因此，所有方法都将共享同一个事务管理器。如果其中一个方法出现异常导致事务回滚，其他方法中的操作也会被回滚。



**结账的service**

```java
@Service
public class checkOutServiceImpl implements checkOutService {
    @Autowired
    private UserService userService;

    @Transactional
    @Override
    public void checkOut(Integer userId, Integer[] bookIds) {
        for (Integer bookId : bookIds){
            userService.buyBook(userId, bookId);
        }
    }
}
```

**买书的service**

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;
    @Autowired
    private BookDao bookDao;

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    @Override
    public void buyBook(Integer userId, Integer bookId) {
        // 查询图书价格
        int price = bookDao.getPrice(bookId);
        // 修改库存
        bookDao.updateStock(bookId);
        // 修改余额
        userDao.updateBalance(userId, price);
    }
}
```

**junit测试**

> 此时买书操作的`propagation = Propagation.REQUIRES_NEW`，所以买第一本书成功，第二本书产生异常会回滚

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:tx-transaction.xml")
public class testTransaction {
    @Autowired
    private UserController userController;

    @Test
    public void test(){
        userController.checkOut(1, new Integer[]{1, 2});
    }
}
```



| 名称                       | 含义                                                         |
| -------------------------- | ------------------------------------------------------------ |
| REQUIRED  <br>默认值       | 当前方法必须工作在事务中  <br>如果当前线程上有已经开启的事务可用，那么就在这个事务中运行  <br>如果当前线程上没有已经开启的事务，那么就自己开启新事务，在新事务中运行  <br>所以当前方法有可能和其他方法共用事务  <br>在共用事务的情况下：当前方法会因为其他方法回滚而受连累 |
| REQUIRES_NEW  <br>建议使用 | 当前方法必须工作在事务中  <br>不管当前线程上是否有已经开启的事务，都要开启新事务  <br>在新事务中运行  <br>不会和其他方法共用事务，避免被其他方法连累 |



**REQUIRED 模式**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307161727726.png)

**REQUIRES_NEW 模式**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307161726407.png)







#### 5.2.2 基于xml的声明式事务

**1、加入依赖**

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>
```

**2、修改Spring配置文件**

> 即使需要事务功能的目标方法已经被切入点表达式涵盖到了，但是如果没有给它配置事务属性，那么这个方法就还是没有事务。所以**事务属性必须配置**。

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

<bean class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"/>
</bean>

<context:component-scan base-package="com.xrj.spring"/>

<!-- 设置事务管理器, 这个相当于是我们的切面, spring已经帮我们把切面和通知实现好了, 我们只需要设置切入点即可 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!-- tx:advice标签：配置事务通知 -->
<!-- id属性：给事务通知标签设置唯一标识，便于引用 -->
<!-- transaction-manager属性：关联事务管理器 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!-- tx:method标签：配置具体的事务方法 -->
        <!-- name属性：指定方法名，可以使用星号代表多个字符 -->
        <tx:method name="get*" read-only="true"/>
        <tx:method name="query*" read-only="true"/>
        <tx:method name="find*" read-only="true"/>
    
        <!-- read-only属性：设置只读属性 -->
        <!-- rollback-for属性：设置回滚的异常 -->
        <!-- no-rollback-for属性：设置不回滚的异常 -->
        <!-- isolation属性：设置事务的隔离级别 -->
        <!-- timeout属性：设置事务的超时属性 -->
        <!-- propagation属性：设置事务的传播行为 -->
        <tx:method name="save*" read-only="false" rollback-for="java.lang.Exception" propagation="REQUIRES_NEW"/>
        <tx:method name="update*" read-only="false" rollback-for="java.lang.Exception" propagation="REQUIRES_NEW"/>
        <tx:method name="delete*" read-only="false" rollback-for="java.lang.Exception" propagation="REQUIRES_NEW"/>
    </tx:attributes>
</tx:advice>

<aop:config>
    <!-- 配置切入点表达式，将事务功能定位到具体方法上 -->
    <aop:pointcut id="txPoincut" expression="execution(* *..*Service.*(..))"/>
    
    <!-- 将事务通知和切入点表达式关联起来 -->
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoincut"/>
</aop:config>
```









# 三、SpringMVC

> MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分 
>
> M：Model，模型层，指工程中的JavaBean，作用是处理数据 
>
> JavaBean分为两类： 
>
> - 一类称为实体类Bean：专门存储业务数据的，如 Student、User 等 
> - 一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。 
>
> V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据 
>
> C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器 
>
> MVC的工作流程： 用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller 调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果 找到相应的View视图，渲染数据后最终响应给浏览器



## 3.1 springmvc入门

**1、引入依赖**

```xml
<dependencies>
  <!-- SpringMVC -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.1</version>
  </dependency>
  <!-- 日志 -->
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
  </dependency>
  <!-- ServletAPI -->
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
  </dependency>
  <!-- Spring5和Thymeleaf整合包 -->
  <dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
    <version>3.0.12.RELEASE</version>
  </dependency>
</dependencies>
```



**2、配置web.xml**

> web.xml中需要通过配置文件的方式配置DispatcherServlet，它接收客户端发来的所有请求。
>
> DispatcherServlet接收到请求后，根据请求的地址找到其ioc容器中对应controller的对应方法，然后执行
>
> DispatcherServlet的ioc容器即springmvc的配置文件，如果在web.xml中不使用`contextConfigLocation`指定，就默认放在WEB-INF下，文件名为`<servlet-name>-servlet.xml`。一般配置文件都放在resource文件夹下，所以配置的路径需要加上`classpath:`。
>
> 1、通过init-param标签设置SpringMVC配置文件的位置和名称
>
> 2、通过load-on-startup标签设置 SpringMVC前端控制器DispatcherServlet的初始化时间。如果不设置，servlet就是在第一次请求时才初始化，那么第一次请求会很慢。

> `<url-pattern>`标签中使用`/`和`/*`的区别： `/`所匹配的请求可以是`/login`或`.html`或`.js`或`.css`方式的请求路径，但是`/`不能匹配`.jsp`请求路径的请求 。因为`.jsp`请求在tomcat的web.xml中已经配置了jspServlet来处理，所以不能用DispatcherServlet来处理
>
> 因此就可以避免在访问`jsp`页面时，该请求被DispatcherServlet处理，从而找不到相应的页面 
>
> `/*`则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用`/*`的写 法

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>spring-mvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring-mvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```



**3、springmvc的配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.xrj.controller"/>

    <!-- 配置Thymeleaf视图解析器 -->
    <bean id="viewResolver"
          class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8" />
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
</beans>
```



**4、创建请求控制器**

> 由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即请求控制器 
>
> 请求控制器中每一个处理请求的方法称为控制器方法 
>
> 因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识 为一个控制层组件，交给Spring的IOC容器管理，此时SpringMVC才能够识别控制器的存在
>
> 通过`@RequestMapping`注解设置请求地址，当客户端请求的地址和`@RequestMapping`一致时，就是执行对应的方法

```java
@Controller
public class HelloController {
    @RequestMapping("/")
    public String index(){
        return "index";
    }
    @RequestMapping("/hello")
    public String success(){
        return "success";
    }
}
```



**5、前端页面**

> 因为设置了`Thymeleaf`视图解析，因此在`controller`层返回字符串后，即逻辑地址，然后拼接前缀和后缀后得到物理地址，最后根据物理地址找到对应的资源

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    	<h1>hello world</h1>
    	<a th:href="@{/hello}">springmvc测试</a>
    	<a href="/hello">绝对路径</a>
    </body>
</html>
```



> 浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器 DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器， 将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面







## 3.2 @RequestMapping

> @RequestMapping注解的作用就是将请求和处理请求的控制器方法关联 起来，建立映射关系
>
> SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求



### 3.2.1 注解的位置

- @RequestMapping标识一个类：设置映射请求的请求路径的初始信息
- @RequestMapping标识一个方法：设置映射请求请求路径的具体信息



请求路径现在就变为`http://localhost:8080/springmvc02/test/`

```java
@Controller
@RequestMapping("/test")
public class ProtalController {
    @RequestMapping("/")
    public String index(){
        return "index";
    }
}
```





### 3.2.2 value属性

- @RequestMapping注解的value属性通过请求的请求地址匹配请求映射

- value属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址所对应的请求

- value属性必须设置，至少通过请求地址匹配请求映射



访问路径为`http://localhost:8080/springmvc02/test/`或`http://localhost:8080/springmvc02/test/abc`都可以

```java
@Controller
@RequestMapping("/test")
public class ProtalController {
    @RequestMapping(value = {"/", "/abc"})
    public String index(){
        return "index";
    }
}
```







### 3.2.3 method属性

- @RequestMapping注解的method属性通过请求的请求方式（get或post）匹配请求映射
- method属性是一个RequestMethod类型的数组，表示该请求映射能够匹配 多种请求方式的请求
- 若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错 `405：Request method 'POST' not supported`



只有post请求才可以访问

```JAVA
@Controller
public class ProtalController {
    @RequestMapping(value = "/", method = RequestMethod.POST)
    public String index(){
        return "index";
    }
}
```



> 1、对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解 
>
> - 处理get请求的映射-->@GetMapping 
>
> - 处理post请求的映射-->@PostMapping 
>
> - 处理put请求的映射-->@PutMapping 
>
> - 处理delete请求的映射-->@DeleteMapping 
>
> 2、常用的请求方式有get，post，put，delete 
>
> 但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符串（put或delete），则按照默认的请求方式get处理





### 3.2.4 params属性

- @RequestMapping注解的params属性通过请求的请求参数匹配请求映射 

- params属性是一个字符串类型的数组，可以通过四种表达式设置请求参数 和请求映射的匹配关系 
- `"param"`：要求请求映射所匹配的请求必须携带param请求参数
- `"!param"`：要求请求映射所匹配的请求必须不能携带param请求参数 
- `"param=value"`：要求请求映射所匹配的请求必须携带param请求参数且param=value
-  `"param!=value"`：要求请求映射所匹配的请求要么不携带param请求参数，如果携带那么param!=value



请求参数必须携带`username`属性，必须携带`gender`属性，且值必须为`男`，不能携带`password`属性，不携带`age`或者`age`不等于10

```java
@Controller
public class ProtalController {
    @RequestMapping(value = "/", method = RequestMethod.GET, params = {"username", "gender=男", "!password", "age!=10"})
    public String index(){
        return "index";
    }
}
```

> 若当前请求满足@RequestMapping注解的value和method属性，但是不满足params属性，此时 页面回报错`400：Parameter conditions xxx`





### 3.2.5 headers属性

- @RequestMapping注解的headers属性通过请求的请求头信息匹配请求映射
- headers属性是一个字符串类型的数组，可以通过四种表达式设置请求头信 息和请求映射的匹配关系
- `"header"`：要求请求映射所匹配的请求必须携带header请求头信息 
- `"!header"`：要求请求映射所匹配的请求必须不能携带header请求头信息 
- `"header=value"`：要求请求映射所匹配的请求必须携带header请求头信息且header=value 
- `"header!=value"`：要求请求映射所匹配的请求要么不携带header信息，如果携带header那么header!=value 

> 若当前请求满足@RequestMapping注解的value和method属性，但是不满足headers属性，此时页面 显示404错误，即资源未找到





### 3.2.6 ant风格的路径

- `？`：表示任意的单个字符 

  ```JAVA
  @RequestMapping(value = "/t?st/demo/success")
  public String success(){
      return "success";
  }
  ```

- `*`：表示任意的0个或多个字符 

  ```java
  @RequestMapping(value = "/t*st/demo/success")
  public String success(){
      return "success";
  }
  ```

- `**`：表示任意层数的任意目录 

  注意：在使用`**`时，只能使用`/**/xxx`的方式

  ```java
  @RequestMapping(value = "/test/**/success")
  public String success(){
      return "success";
  }
  ```





### 3.2.7 路径中的占位符

> SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的`@RequestMapping`注解的value属性中通过占位符`{xxx}`表示传输的数据，在 通过`@PathVariable`注解，将占位符所表示的数据赋值给控制器方法的形参

**原始方式**

```html
<a th:href="@{test/demo/success(username=xrj, id=1)}">123</a>
```

地址栏：`test/demo/success?username=xrj&id=1 `



**rest方式**

```html
<a th:href="@{test/rest/xrj/1}">456</a>
```

```java
@RequestMapping(value = "test/rest/{username}/{id}")
public String rest(@PathVariable("username") String username, @PathVariable("id") Integer id){
    System.out.println(username);
    System.out.println(id);
    return "success";
}
```

地址栏：``test/rest/xrj/1``







## 3.3 获取请求参数

### 3.3.1 ServletAPI获取

> 将HttpServletRequest作为控制器方法的形参，此时HttpServletRequest类型的参数表示封装了当前请求的请求报文的对象。在Dispatcher中央控制器收到请求调用该方法时，会对该方法的参数进行赋值，所以如果有HttpServletRequest，就会直接将它的request赋值，然后通过反射进行调用

**html**

```html
<form action="param/servletAPI" method="post">
    用户名: <input type="text" name="username"><br>
    密码: <input type="password" name="password"><br>
    <input type="submit" value="提交">
</form>
```

**Controller**

```java
@RequestMapping("param/servletAPI")
public String paramServlet(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println(username);
    System.out.println(password);
    return "success";
}
```







### 3.3.2 通过控制器方法的形参获取请求参数

> 在控制器方法的形参位置，设置和请求参数同名的形参，当浏览器发送请求，匹配到请求映射时，在 DispatcherServlet中就会将请求参数赋值给相应的形参，然后通过反射进行调用

**html**

```html
<form action="param" method="post">
    用户名: <input type="text" name="username"><br>
    密码: <input type="password" name="password"><br>
    <input type="submit" value="提交">
</form>
```

**controller**

```java
@RequestMapping("param")
public String param(String username, String password){
    System.out.println(username);
    System.out.println(password);
    return "success";
}
```



> 若请求所传输的请求参数中有多个同名的请求参数，此时可以在控制器方法的形参中设置**字符串数组**或者**字符串**类型的形参接收此请求参数 
>
> 若使用**字符串数组**类型的形参，此参数的数组中包含了每一个数据 
>
> 若使用**字符串**类型的形参，此参数的值为每个数据中间使用逗号拼接的结果







### 3.3.3 @RequestParam

@RequestParam是将请求参数和控制器方法的形参创建映射关系 

@RequestParam注解一共有三个属性：

- value：指定为形参赋值的请求参数的参数名 
- required：设置是否必须传输此请求参数，默认值为true 
  - 若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置 defaultValue属性，则页面报错`400：Required String parameter 'xxx' is not present；`
  - 若设置为 false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为 null 

- defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值



```java
@RequestMapping("param")
public String param(@RequestParam(value = "username", required = false, defaultValue = "xrj") String username, String password){
    System.out.println(username);
    System.out.println(password);
    return "success";
}
```



> 如果使用`@RequestParam`的话，多个同名参数可以使用`List`集合接收，但是如果不是用该注解，只能使用数组或者字符串接收







### 3.3.4 @RequestHeader

@RequestHeader是将请求头信息和控制器方法的形参创建映射关系 

@RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```java
@RequestMapping("param")
public String param(String username, String password, @RequestHeader("referer") String referer){
    System.out.println(username);
    System.out.println(password);
    System.out.println(referer);
    return "success";
}
```





### 3.3.5 @CookieValue

@CookieValue是将cookie数据和控制器方法的形参创建映射关系 

@CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```java
@RequestMapping("param")
public String param(String username, String password,@CookieValue("JSESSIONID") String sessionId){
    System.out.println(username);
    System.out.println(password);
    System.out.println(sessionId);
    return "success";
}
```





### 3.3.6 通过POJO获取请求参数

可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实体类中的属性名一致，那么springmvc就会通过setXxx方法为此属性赋值

**html**

```html
<form action="param/pojo" method="post">
    用户名: <input type="text" name="username"><br>
    密码: <input type="password" name="password"><br>
    <input type="submit" value="提交">
</form>
```

**controller**

```java
@RequestMapping("param/pojo")
public String paramPojo(User user){
    System.out.println(user);
    return "success";
}
```







### 3.3.7 请求参数乱码问题

> tomcat8之前，get和post方式都会出现中文乱码问题。在tomcat8之后，只有post方式会出现中文乱码问题。解决乱码问题需要在获取参数之前设置`request.setCharacterEncoding("UTF-8");`
>
> 但是在我们的controller是通过Dispatcher中央控制器调用的，在Dispatcher中已经获取过参数了，所以在controller中设置编码不会生效
>
> 因此就通过过滤器设置编码，springmvc为我们提供了编码过滤器CharacterEncodingFilter，在web.xml注册即可



**web.xml**

> 第一个初始化参数，设置编码类型。第二个初始化参数，表示request和response请求都设置编码

```xml
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



> springmvc提供的CharacterEncodingFilter过滤器是通过`doFilterInternal`方法实现过滤的。
>
> 其中`String encoding = this.getEncoding();`就是获取我们在web.xml中设置的encoding
>
> 如果不设置`forceEncoding`为`true`，那么只有request会设置编码，设置之后，request和response都会设置编码
>
> `this.isForceRequestEncoding()`和`this.isForceResponseEncoding()`获取的都是`forceEncoding`的值

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307211744954.png)



> SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效。
>
> SpringMVC中还有一个处理请求的过滤器，该过滤器会获取请求参数，如果设置在处理编码的过滤器之前，那么设置编码就不会生效了









## 3.4 域对象共享数据

> 1、HttpServletRequest和HttpServletResponse都是接口，Web容器（Tomcat）会实现HttpServletRequest接口，并在接收到HTTP请求时创建一个HttpServletRequest对象，并将该对象作为参数传递给对应的Servlet的service()方法。在Tomcat中，HttpServletRequest的实现类是org.apache.catalina.connector.Request类。
>
> 2、HttpServletRequest对象是Tomcat服务器负责创建的。用户发送请求的时候，遵循了HTTP协议，发送的是HTTP的请求协议，Tomcat服务器将HTTP协议中的信息以及数据全部解析出来，然后把这些信息封装到HttpServletRequest对象当中。编写程序时调用相关方法就可以获取到用户请求的信息了



> 1、`request.setAttribute()`方法是将数据存储到request作用域中，它的底层是将数据存储到`attribute map`中，是一个Map类型的对象
>
> 2、`ModelAndView的Model`可以存储数据，将数据存储到一个`ModelMap`对象中，也是Map类型。当请求被处理完毕后，Spring MVC会将ModelAndView对象中的模型数据复制到request作用域中，以便在视图层中使用。因此ModelAndView存储的数据最终是存储在request作用域中的





### 3.4.1 使用servlet存数据

```java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
    request.setAttribute("testScope", "hello,servletAPI");
    return "success";
}
```





### 3.4.2 ModelAndView

> `ModelAndView的Model`可以存储数据，将数据存储到一个`ModelMap`对象中，也是Map类型。当请求被处理完毕后，Spring MVC会将ModelAndView对象中的模型数据复制到request作用域中【复制到request的`attribute map`中】，以便在视图层中使用。因此ModelAndView存储的数据最终是存储在request作用域中的
>
> `ModelAndView`中的数据最终放在`request`作用域中的前提是，该Controller方法的返回值必须是这个`ModelAndView`，如果还是通过返回字符串跳转页面，那么数据是不会复制到`request`域中的

`ModelAndView`有`Model`和`View`的功能 

- Model主要用于向请求域共享数据 
- View主要用于设置视图，实现页面跳转 

```java
@RequestMapping("test/ModleAndView")
public ModelAndView testModelAndView(){
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("name", "hello, ModelAndView");
    modelAndView.setViewName("success");
    return modelAndView;
}
```





### 3.4.3 Model

> Model对象是DispatcherServlet创建的，在调用该方法时，它将一个空的Model对象传进来，当Controller方法返回时，DispatcherServlet会将Model对象中的数据复制到request作用域中，以便在视图层中使用。

```java
@RequestMapping("test/Model")
public String testModel(Model model){
    // org.springframework.validation.support.BindingAwareModelMap
    System.out.println(model.getClass().getName());
    model.addAttribute("name", "hello model");
    return "success";
}
```





### 3.4.4 ModelMap

> ModelMap也是DispatcherServlet创建，最后将其中的数据复制到request中

```java
@RequestMapping("test/ModelMap")
public String testModelMap(ModelMap modelMap){
    // org.springframework.validation.support.BindingAwareModelMap
    System.out.println(modelMap.getClass().getName());
    modelMap.addAttribute("name", "hello ModelMap");
    return "success";
}
```





### 3.4.5 Map

> Map也是DispatcherServlet创建，最后将其中的数据复制到request中

```java
@RequestMapping("test/Map")
public String testMap(Map<String, Object> map){
    // org.springframework.validation.support.BindingAwareModelMap
    System.out.println(map.getClass().getName());
    map.put("name", "hello map");
    return "success";
}
```



> 1、Model、ModelMap、Map类型的参数其实本质上都是 **BindingAwareModelMap** 类型的
>
> 2、不管使用哪种技术【ModelAndView、Model、ModelMap、Map】，数据最终都会被封装到ModelAndView中。
>
> 3、在DispatcherServlet调用controller方法时，最终都会调用`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`  返回的mv对象就是`ModelAndView`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221541719.png)

> 4、这几种方法，最终的数据都会被保存到request域中。在org.thymeleaf.context.WebEngineContext 的内部类 RequestAttributesVariablesMap的setVariable(String name, Object value)方法中可以看到最终数据是存储到了request中

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221539654.png" style="zoom:80%;" />







### 3.5 session域共享数据

> session域是一次会话内有效，也就是如果关闭浏览器，那么当前session就失效了
>
> 如果服务器重新部署，但是没有关闭浏览器，session是不会失效的。
>
> 因为session的钝化，会话中的数据将被序列化并写入到磁盘上的文件中
>
> 会话活化是将会话从磁盘或其他持久性存储介质中还原回内存。在服务器启动时会将数据重新保存到session中。
>
> 所以如果session域中有实体类对象，那么想要实现钝化和活化，该类必须实现序列化接口。
>
> 在IDEA中需要将Preserve session across restarts and redeploys选项勾上才能实现钝化

```java
@RequestMapping("test/session")
public String testSession(HttpSession session){
    session.setAttribute("name", "hello, session");
    return "success";
}
```





### 3.6 application域共享数据

> application域是只要服务器不关闭，就不会失效

```java
@RequestMapping("test/application")
public String testApplication(HttpSession session){
    ServletContext application = session.getServletContext();
    application.setAttribute("name", "hello, application");
    return "success";
}
```









## 3.5 SpringMVC的视图

> SpringMVC中的视图是View接口，视图的作用渲染数据，将模型Model中的数据展示给用户
>
> SpringMVC视图的种类很多，默认有**转发视图**和**重定向视图** 
>
> 当工程引入jstl的依赖，转发视图会自动转换为JstlView 
>
> 若使用的视图技术为Thymeleaf，在SpringMVC的配置文件中配置了Thymeleaf的视图解析器，由此视图解析器解析之后所得到的是ThymeleafView

​	

### 3.5.1 ThymeleafView

> 当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过转发的方式实现跳转

**controller**

```java
@RequestMapping("view/thymeleaf")
public String testThymeleaf(){
    return "success";
}
```

1、在DispatcherServlet调用控制层方法后，会得到一个ModelAndView类型的mv对象，然后执行`processDispatchResult`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221601240.png)

2、在`processDispatchResult`方法中，如果mv不为空，那么就执行`this.render(mv, request, response);`进行视图渲染

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221600815.png)

3、在`render`方法中，首先得到了`viewName`视图名，即我们控制层方法返回的逻辑地址，然后通过`resolveViewName`方法得到view对象，我们在springmvc的配置文件中配置了Thymeleaf的视图解析器`ThymeleafViewResolver`，此时返回的视图名称没有任何前缀，因此会被`ThymeleafView`进行解析，然后返回对应的物理地址

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221602104.png)









### 3.5.2 转发视图

> SpringMVC中默认的转发视图是`InternalResourceView` 
>
> SpringMVC中创建转发视图的情况：当控制器方法中所设置的视图名称以`forward:`为前缀时，创建`InternalResourceView`视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀`forward:`去掉，剩余部分作为最终路径通过转发的方式实现跳转
>
> 例如"forward:/"，"forward:/employee



**controller**

```JAVA
@RequestMapping("view/forward")
public String testForward(){
    return "forward:/test/Model";
}
```

这里在获取`viewName`后，通过`resolveViewName`获取`view`，获取的`view`就是`InternalResourceView`，然后在进行转发到`test/Model`下，这个方法返回的是`success`，所以还会被`ThymeleafView`解析

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221637684.png)



`ThymeleafView`和普通的转发视图都是实现转发功能，但是如果我们使用了`Thymeleaf`作为视图解析技术，我们一般会使用`ThymeleafView`，因为如果我们使用普通的转发请求转发到前端页面，前端页面上的`Thymeleaf`语法就无法被解析了。而我们使用`ThymeleafView`转发后，会被`ThymeleafView`解析，同时前端页面的`Thymeleaf`语法也会被解析







### 3.5.3 重定向视图

> SpringMVC中默认的重定向视图是`RedirectView`
>
> 当控制器方法中所设置的视图名称以`redirect:`为前缀时，创建`RedirectView`视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀`redirect:`去掉，剩余部分作为最终路径通过重定向的方式实现跳转。
>
> SpringMVC中，重定向时加上`redirect:`后边跟上重定向的地址，浏览器会将`/`解析为`localhost:8080`，而springmvc会为我们自动加上上下文路径
>
> 例如"redirect:/"，"redirect:/employee"



**controller**

```java
@RequestMapping("view/redirect")
public String testRedirect(){
    return "redirect:/test/Model";
}
```

这里获取了viewName后，通过viewName去获取view，获取到的view就是`RedirectView`，是一个重定向视图，然后重定向到`/test/Model`下，这个方法返回值是`success`，所以最后会被`ThymeleafView`解析

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307221653413.png)







### 3.5.4 视图控制器view-controller

当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示



> 此时index页面控制层方法就不需要写了，因为他仅仅是实现页面跳转
>
> 当SpringMVC中设置任何一个view-controller时，其他控制器中的请求映射将全部失效，此时需要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签：`<mvc:annotation-driven></mvc:annotation-driven>`

```xml
<mvc:annotation-driven></mvc:annotation-driven>
<!--
    path：设置处理的请求地址
    view-name：设置请求地址所对应的视图名称
-->
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>
```







## 3.6 RESTful

> REST：Representational State Transfer，表现层资源状态转移。



### 3.6.1 RESTful的实现

四个表示操作方式的动词：**GET、POST、PUT、DELETE。** 

它们分别对应四种基本操作：

- GET 用来获取资源
- POST 用来新建资源
- PUT 用来更新资源
- DELETE 用来删除资源。 

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

| 操作     | 传统方式         | REST风格                |
| -------- | ---------------- | ----------------------- |
| 查询操作 | getUserById?id=1 | user/1-->  get请求方式  |
| 保存操作 | saveUser         | user-->post请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 | updateUser       | user-->put请求方式      |





### 3.6.2 HiddenHttpMethodFilter

由于浏览器只支持发送get和post方式的请求，因此 SpringMVC 提供了 `HiddenHttpMethodFilter` 帮助我们将 `POST` 请求转换为` DELETE `或 `PUT `请求

`HiddenHttpMethodFilter` 处理put和delete请求的条件： 

- 当前请求的请求方式必须为`post` 
- 当前请求必须传输请求参数`_method`，其`value`值就是我们想要设置的请求方式

满足以上条件，`HiddenHttpMethodFilter` 过滤器就会将当前请求的请求方式转换为请求参数 `_method`的值，因此请求参数`_method`的值才是最终的请求方式 



在web.xml中注册HiddenHttpMethodFilter

```XML
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



> 目前为止，SpringMVC中提供了两个过滤器：`CharacterEncodingFilter`和 `HiddenHttpMethodFilter `
>
> 在web.xml中注册时，必须先注册`CharacterEncodingFilter`，再注册`HiddenHttpMethodFilter`
>
> 原因： 在 CharacterEncodingFilter 中通过 `request.setCharacterEncoding(encoding) `方法设置字 符集， `request.setCharacterEncoding(encoding) `方法要求前面不能有任何获取请求参数的操作，而`HiddenHttpMethodFilter` 恰恰有一个获取请求方式的操作：`String paramValue = request.getParameter(this.methodParam);`



1、在`HiddenHttpMethodFilter`的`doFilterInternal`方法中执行过滤操作，首先判断当前请求是否是post请求，如果不是，直接通过。所以设置put和delete请求时，请求方式必须设置为post。

2、`String paramValue = request.getParameter(this.methodParam);`其中`methodParam`的值为`_method`，通过这行代码获取`_method`的值，如果他的值为空那么说明是普通的post请求，直接通过。

3、`String method = paramValue.toUpperCase(Locale.ENGLISH);`将`_method`的值转为大写，然后通过`ALLOWED_METHODS.contains(method)`判断`method`是否是`PUT`、`DELETE`、`PATCHA`中的一种。

如果是，那么执行`requestToUse = new HiddenHttpMethodFilter.HttpMethodRequestWrapper(request, method);`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261132444.png)

4、`HttpMethodRequestWrapper`方法中，传递过来两个参数，第一个就是request请求，第二个是当前请求的方式。将method设置为当前请求的请求方式，重写`getMethod`方法，然后将这个新的request请求通过过滤器，这样请求方式就被修改了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261132420.png)







### 3.6.3 四种请求方式映射

#### 1、get方式

> 查询用户信息

```html
<a th:href="@{/user}">查询全部用户</a><br/>
<a th:href="@{/user/1}">根据id查询用户</a><br/>
```

```java
// @RequestMapping(value = "/user", method = RequestMethod.GET)
@GetMapping("/user")
public String getUser(){
    System.out.println("查询全部用户信息");
    return "success";
}

// @RequestMapping(value = "/user/{id}", method = RequestMethod.GET)
@GetMapping("/user/{id}")
public String getUserById(@PathVariable("id") Integer id){
    System.out.println("根据id查询用户信息, id = " + id);
    return "success";
}
```

​            



#### 2、post方式

> 添加用户信息

```html
<form th:action="@{/emp}" method="post">
    <table>
        <tr>
            <th colspan="2">添加员工</th>
        </tr>
        <tr>
            <td>姓名</td>
            <td>
                <input type="text" name="empName">
            </td>
        </tr>
        <tr>
            <td>年龄</td>
            <td>
                <input type="text" name="age">
            </td>
        </tr>
        <tr>
            <td>性别</td>
            <td>
                <input type="radio" name="gender" value="男">男
                <input type="radio" name="gender" value="女">女
            </td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="submit" value="提交">
            </td>
        </tr>
    </table>
</form>
```

```java
// @RequestMapping(value = "/emp", method = RequestMethod.POST)
@PostMapping("/emp")
public String addUser(Emp emp){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    mapper.addEmp(emp);
    return "redirect:/emp";
}
```





#### 3、put方式

> 修改用户信息

```html
<form th:action="@{/emp}" method="post">
    <input type="hidden" name="_method" value="put">
    <input type="hidden" name="empId" th:value="${emp.empId}">
    <table>
        <tr>
            <th colspan="2">修改员工信息</th>
        </tr>
        <tr>
            <td>姓名</td>
            <td>
                <input type="text" name="empName" th:value="${emp.empName}">
            </td>
        </tr>
        <tr>
            <td>年龄</td>
            <td>
                <input type="text" name="age" th:value="${emp.age}">
            </td>
        </tr>
        <tr>
            <td>性别</td>
            <td>
                <input type="radio" name="gender" value="男" th:field="${emp.gender}">男
                <input type="radio" name="gender" value="女" th:field="${emp.gender}">女
            </td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="submit" value="提交">
            </td>
        </tr>
    </table>
</form>
```

```java
// @RequestMapping(value = "/emp", method = RequestMethod.PUT)
@PutMapping("/emp")
public String updateEmp(Emp emp){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    mapper.updateEmp(emp);
    return "redirect:/emp";
}
```





#### 4、delete方式

> 删除用户信息
>
> 由于restful风格的delete请求，需要通过一个表单提交，但是删除操作通常是点击一个链接，因此我们对单击删除按钮做一个单击事件，当点击删除时，执行这个js函数，js函数中，拿到form表单，同时将删除的链接赋值给form表单的action属性，然后提交，同时阻止a标签的默认行为。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>emp list</title>
    <link rel="stylesheet" th:href="@{/static/css/index_work.css}">
</head>
<body>

<div id="app">
    <table>
        <tr>
            <th colspan="5">员工列表</th>
        </tr>
        <tr>
            <th>id</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>性别</th>
            <th>操作 (<a th:href="@{/to/add}">添加</a>) </th>
        </tr>
        <tr th:each="emp : ${allEmp}">
            <td th:text="${emp.empId}"></td>
            <td th:text="${emp.empName}"></td>
            <td th:text="${emp.age}"></td>
            <td th:text="${emp.gender}"></td>
            <td>
                <a th:href="@{|emp/${emp.empId}|}">修改</a>
                <a @click="deleteEmp()" th:href="@{|emp/${emp.empId}|}">删除</a>
            </td>
        </tr>
    </table>
    <form method="post">
        <input type="hidden" name="_method" value="delete">
    </form>
</div>

<script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            deleteEmp(){
                // 获取form表单
                var form = document.getElementsByTagName("form")[0];
                // 将超链接的href属性赋值给form的action属性
                // event.target表示当前触发事件的标签
                form.action = event.target.href;
                // 表单提交
                form.submit();
                // 阻止a标签的默认行为，即跳转href
                event.preventDefault();
            }
        }
    })
</script>
</body>
</html>
```

```java
// @RequestMapping(value = "/emp/{id}", method = RequestMethod.DELETE)
@DeleteMapping("/emp/{id}")
public String deleteEmp(@PathVariable("id") Integer id){
    SqlSession sqlSession = sqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    mapper.deleteEmp(id);
    return "redirect:/emp";
}
```

> 重定向路径是相对于当前请求路径的，因此如果当前的请求路径为 `/emp/{id}`，而重定向路径为 `redirect:emp`，则重定向后的路径就是 `/emp/emp`。
>
> 因此重定向路径最好都加上`/`，即写为`redirect:/emp`这种形式







### 3.6.4 处理静态资源

当前我们`DispatcherServlet`的拦截路径为`/`，表示拦截除了`.jsp`以外的所有请求，tomcat也有一个web.xml，在tomcat的web.xml中有一个`JspServlet`用来处理jsp请求，同时他还有一个`DefaultServlet`，他的拦截路径为`/`，也是拦截所有请求，tomcat的web.xml配置是对容器中所有工程生效，在我们的工程中的web.xml仅仅是对当前工程生效，tomcat的web.xml的所有配置都会传递给我们工程的web.xml，当工程的配置和tomcat的配置冲突时，会使用工程的配置。因此我们的`DispatcherServlet`的拦截路径为`/`，tomcat的`DefaultServlet`拦截路径也是`/`，那么就会使用`DispatcherServlet`来处理请求。

所以`<link rel="stylesheet" th:href="@{/static/css/index_work.css}">`我们的静态资源访问路径会被`DispatcherServlet`拦截到，但是`DispatcherServlet`中没有对应的`@RequestMapping`路径，所以会报404，而tomcat的`DefaultServlet`可以处理静态资源，但是却被`DispatcherServlet`覆盖了。

在之前javaweb中，我们的css样式可以生效是因为，当时的`DispatcherServlet`拦截的路径为`*.do`，不会拦截静态资源，所以静态资源会被tomcat的`DefaultServlet`拦截到，所以可以正常显示。

在springmvc.xml文件中，若配置了`<mvc:default-servlet-handler/>`，此时浏览器的所有请求都会被`DefaultServlet`处理

若配置了`<mvc:default-servlet-handler/>`和`<mvc:annotation-driven/>`，浏览器发送的请求会先被`DispatcherServlet`处理，无法处理再交给`DefaultServlet`处理【即请求的资源没有`@RequestMapping`注解】



**Tomcat的DefaultServlet**

```xml
<servlet>
    <servlet-name>default</servlet-name>
    <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
    <init-param>
        <param-name>debug</param-name>
        <param-value>0</param-value>
    </init-param>
    <init-param>
        <param-name>listings</param-name>
        <param-value>false</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<!-- The mapping for the default servlet -->
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```





**springmvc.xml**

配置这两个后，前端的css文件就可以被访问到了

```xml
<mvc:default-servlet-handler/>

<!-- 开启mvc注解驱动 -->
<mvc:annotation-driven/>
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>emp list</title>
    <link rel="stylesheet" th:href="@{/static/css/index_work.css}">
</head>
<body>
<table>
    <tr>
        <th colspan="5">emp list</th>
    </tr>
    <tr>
        <th>id</th>
        <th>name</th>
        <th>age</th>
        <th>gender</th>
        <th>operate</th>
    </tr>
    <tr th:each="emp : ${allEmp}">
        <td th:text="${emp.empId}"></td>
        <td th:text="${emp.empName}"></td>
        <td th:text="${emp.age}"></td>
        <td th:text="${emp.gender}"></td>
        <td>
            <a href="">删除</a>
            <a href="">更新</a>
        </td>
    </tr>
</table>
</body>
</html>
```









## 3.7 SpringMVC处理ajax请求

### 3.7.1 ajax

使用`axios`框架执行`ajax`来发送异步请求，配合`vue`使用

```js
axios({
	"method":"",  	// 请求方式
	"url":"",		// 请求路径
	// 1、以name=value&name=value的方式发送请求参数
	// 2、不管使用get还是post方式，请求参数都会被拼接到请求地址后
	// 3、可以通过request.getParameter()获取参数，因此在springmvc中可以通过设置形参直接获取
	"params":{},	
	// 1、以json格式发送请求参数
	// 2、请求参数会保存到请求报文的请求体传输到服务器，因此必须是post、put、patch请求，一般都是使用post
	// 3、请求参数需要通过输入流读取，然后通过Gson将json格式字符串转为java对象
	"data":{}
})
.then(function (value){ // 成功时执行的回调
	console.log(value);
})
.catch(function(reason){ // 有异常时执行的回调
	console.log(reason);
});
```



1、一般在使用时，我们通过`axios.get()`来发送一个get请求，但是这样写参数只能通过`data`即json格式发送，所以get方式不能携带参数【因为get方式没有请求体】，如果仅仅是发送一个请求，没有请求参数，那么我们可以使用`axios.get()`

2、`axios.post()`可以发送一个post请求，他可以携带参数，参数是json格式，请求参数会保存到请求报文的请求体发送到服务器

3、如果希望使用`params`方式携带参数，那么我们可以直接在url后边拼接，因为传统`axios`的`params`的参数就是拼接在url地址后边的，例如：`"/springmvc04/test/ajax?id=1"`。需要注意的是：使用`axios`发送异步请求，请求地址是浏览器解析的，因此需要加上上下文路径

```js
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            testAjax(){
                axios.post(
                    "/springmvc04/test/ajax?id=1",
                    {username:"xrj", password:"123"}
                ).then(value=>{
                    console.log(value.data)
                });
            }
        }
    });
</script>
```





### 3.7.2 @RequestBody

> 通过`request.getParameter()`方式获取参数，参数都是拼接在url地址后边的，所以通过`axios`的`params`传递的参数在springmvc中就可以直接在控制器方法中，设置对应的形参获取。
>
> 但是`axios`将参数保存在`data`中通过json方式传递，参数是保存在请求体中的，这时`request.getParameter()`无法获取，因此需要通过`@RequestBody`注解，将请求体中的数据赋值给当前控制器方法设置的形参
>
> `@RequestBody`可以获取请求体信息，使用`@RequestBody`注解标识控制器方法的形参，当前请求的请求体就会为当前注解所标识的形参赋值



> 表单数据也是存放在请求体中的，`request.getParameter()` 方法可以获取的是HTTP请求中的参数，包括查询参数（query parameters）和表单数据，表单数据是`application/x-www-form-urlencoded`格式的，所以表单提交时是可以通过`request.getParameter()`获取的
>
> 但是`axios`发送`ajax`请求时，参数放在请求体中，数据格式是json的，无法通过`request.getParameter()`获取

```html
<form th:action="@{/test/RequestBody}" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input type="submit">
</form>
```

```java
@RequestMapping("/test/RequestBody")
public String testRequestBody(@RequestBody String data){
    // username=xrj&password=1234
    System.out.println(data);
    return "success";
}
```





**@RequestBody获取json格式的请求参数**

> 在使用了axios发送ajax请求之后，浏览器发送到服务器的请求参数有两种格式： 
>
> 1、`name=value&name=value...`，此时的请求参数可以通过`request.getParameter()`获取，对应 SpringMVC中，可以直接通过控制器方法的形参获取此类请求参数
>
> 2、`{key:value,key:value,...}`，此时无法通过`request.getParameter()`获取
>
> 之前我们使用操作 json的相关jar包gson或jackson处理此类请求参数，可以将其转换为指定的实体类对象或map集合
>
> 在SpringMVC中，直接使用`@RequestBody`注解标识控制器方法的形参即可将此类请求参数转换为java对象



1、导入jackson的依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

2、SpringMVC的配置文件中设置开启mvc的注解驱动

```xml
<mvc:annotation-driven/>
```

3、在控制器方法的形参位置，设置json格式的请求参数要转换成的java类型（实体类或map）的参数，并使用`@RequestBody`注解标识

```js
testRequestBody(){
    axios.post(
        "/springmvc04/test/requestBody",
        {username:"xrj", age:20, gender:"男"}
    ).then(response=>{
        console.log(response.data)
    });
},
```

**将发送的json数据转为User对象**

```java
@RequestMapping("/test/requestBody")
public void testRequestBody(@RequestBody User user, HttpServletResponse response) throws IOException {
    System.out.println(user);
    response.getWriter().write("hello requestBody");
}
```

**将发送的json数据转为Map集合**

```java
@RequestMapping("/test/requestBody")
public void testRequestBody(@RequestBody Map<String, Object> map, HttpServletResponse response) throws IOException {
    System.out.println(map);
    response.getWriter().write("hello requestBody");
}
```





### 3.7.3 @ResponseBody

> 在我们通过`axios`发送`ajax`请求后，我们每次都要通过`response`来回写数据发送给`ajax`的回调函数，控制层方法返回值也是`void`
>
> `@ResponseBody`用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

```java
@RequestMapping("/testResponseBody")
public String testResponseBody(){
    //此时会跳转到逻辑视图success所对应的页面，返回的是整个html页面，会被浏览器解析
    return "success";
}

@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    //此时响应浏览器数据success，返回的只有success，页面只显示一个success
    return "success";
}
```





**@ResponseBody响应浏览器json数据**

> 服务器处理ajax请求之后，大多数情况都需要向浏览器响应一个java对象，此时必须将java对象转换为 json字符串才可以响应到浏览器
>
> 之前我们使用操作json数据的jar包gson或jackson将java对象转换为 json字符串
>
> 在SpringMVC中，我们可以直接使用@ResponseBody注解实现此功能



1、导入jackson的依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

2、SpringMVC的配置文件中设置开启mvc的注解驱动

```xml
<mvc:annotation-driven/>
```

3、使用@ResponseBody注解标识控制器方法，在方法中，将需要转换为json字符串并响应到浏览器的java对象作为控制器方法的返回值，此时SpringMVC就可以将此对象直接转换为json字符串并响应到浏览器

```js
testResponseBody(){
    axios.post("/springmvc04/test/responseBody").then(response=>{console.log(response.data)})
}
```

**返回的数据是一个java对象**

```java
@RequestMapping ("/test/responseBody")
@ResponseBody
public User testResponseBody(){
    User user = new User(1001, "xrj", 20, "男");
    return user;
}
```

**返回的数据是一个map集合**

```java
@RequestMapping ("/test/responseBody")
@ResponseBody
public Map<String, Object> testResponseBody(){
    User user1 = new User(1001, "xrj1", 20, "男");
    User user2 = new User(1002, "xrj2", 20, "男");
    User user3 = new User(1003, "xrj3", 20, "男");
    HashMap<String, Object> map = new HashMap<>();
    map.put("1001", user1);
    map.put("1002", user2);
    map.put("1003", user3);
    return map;
}
```

**返回的数据是一个list集合**

```java
@RequestMapping ("/test/responseBody")
@ResponseBody
public List<User> testResponseBody(){
    User user1 = new User(1001, "xrj1", 20, "男");
    User user2 = new User(1002, "xrj2", 20, "男");
    User user3 = new User(1003, "xrj3", 20, "男");
    List<User> list = Arrays.asList(user1, user2, user3);
    return list;
}
```





### 3.7.4 @RestController注解

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了 @Controller注解，并且为其中的每个方法添加了@ResponseBody注解







## 3.8 文件上传和下载

### 3.8.1 文件下载

`ResponseEntity`用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文，使用`ResponseEntity`实现下载文件的功能

```html
<a th:href="@{/file/download}">文件下载</a>
```

```java
@RequestMapping("/file/download")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
    // 获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    // 获取服务器中文件的真实路径
    String realPath = servletContext.getRealPath("/static/img/1.jpg");
    // 创建输入流
    InputStream is = new FileInputStream(realPath);
    // 创建字节数组, available()返回可从输入流中读取的字节数
    byte[] bytes = new byte[is.available()];
    // 将流读到字节数组中
    is.read(bytes);
    // 创建HttpHeaders对象设置响应头信息
    MultiValueMap<String, String> headers = new HttpHeaders();
    // 设置要下载方式以及下载文件的名字
    /*
        Content-Disposition 是一个响应头，用于指定如何处理响应体的内容。
        当Content-Disposition 被设置为 "attachment" 时，表示响应体的内容应该被当做附件下载，而不是直接在浏览器中显示
     */
    headers.add("Content-Disposition", "attachment;filename=1.jpg");
    // 设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    // 创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, statusCode);
    // 关闭输入流
    is.close();
    return responseEntity;
}
```





### 3.8.2 文件上传

> 1、文件上传要求form表单的请求方式必须为`post`，并且添加属性**`enctype="multipart/form-data" `**，表单该属性默认值为`application/x-www-form-urlencoded`，即上传的是普通数据，放在了请求体内，这种格式数据是可以通过`request.getParameter()`获取的。
>
> 2、`encType=multipart/form-data` 表示提交的数据，以多段（每一个表单项一个数据段）的形式进行拼接，然后以二进制流的形式发送给服务器
>
> 3、SpringMVC中将上传的文件封装到`MultipartFile`对象中，通过此对象可以获取文件相关信息



**1、添加依赖**

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```



**2、在springmvc的配置文件中添加配置**

> 这里必须设置id，且id是固定的，因为springmvc通过ioc容器获取文件上传解析器是通过id获取的

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```



**3、上传文件表单**

```html
<form th:action="@{/test/upload}" method="post" enctype="multipart/form-data">
    <input type="text" name="username"/><br>
    <input type="file" name="photo"/> <br>
    <input type="submit" value="上传">
</form>
```



**4、controller方法**

> springmvc通过文件上传解析器将文件数据封装到一个`MultipartFile`对象中，供我们使用
>
> 同时为了避免上传的文件名重复，我们使用一个随机名字来保存

```java
@RequestMapping("/test/upload")
public String testUpload(String username, MultipartFile photo, HttpSession session) throws IOException {
    System.out.println(username);
    // 获取文件名
    String filename = photo.getOriginalFilename();
    // 获取文件后缀
    String hzName = filename.substring(filename.lastIndexOf("."));
    // 通过uuid获取随机字符串
    String uuid = UUID.randomUUID().toString();
    // 新的文件名
    filename = uuid + hzName;
    ServletContext application = session.getServletContext();
    // 获取当前工程路径
    String path = application.getRealPath("");
    // 得到当前文件存储的目录
    String filepath = path + File.separator + "photo";
    // 如果该路径不存在就创建
    File file = new File(filepath);
    if (!file.exists()){
        file.mkdir();
    }
    // 当前文件路径
    String realpath = filepath + File.separator + filename;
    // 将上传的文件存储到该路径下
    photo.transferTo(new File(realpath));
    return "success";
}
```



> 在浏览器的请求头中可以看到表单提交的数据类型，在请求体中可以看到提交的数据是以一段一段的形式提交的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261129694.png)

![image-20230725180611535](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307251806050.png)









## 3.9 拦截器

> SpringMVC中的拦截器用于拦截控制器方法的执行，拦截器在请求到达具体 控制层 方法前，统一执行检测



### 3.9.1 拦截器和过滤器区别

**相同点**

- 拦截：必须先把请求拦住，才能执行后续操作
- 过滤：拦截器或过滤器存在的意义就是对请求进行统一处理
- 放行：对请求执行了必要操作后，放请求过去，让它访问原本想要访问的资源



**不同点**

- 工作平台不同
    - 过滤器工作在 Servlet 容器中
    - 拦截器工作在 SpringMVC 的基础上
- 拦截的范围
    - 过滤器：能够拦截到的最大范围是整个 Web 应用
    - 拦截器：能够拦截到的最大范围是整个 SpringMVC 负责的请求
- IOC 容器支持
    - 过滤器：想得到 IOC 容器需要调用专门的工具方法，是间接的
    - 拦截器：它自己就在 IOC 容器中，所以可以直接从 IOC 容器中装配组件，也就是可以直接得到 IOC 容器的支持







### 3.9.2 使用拦截器

**1、实现`HandlerInterceptor`接口**

> `HandlerInterceptor`是一个接口，里边的三个方法都有默认的实现
>
> - `preHandle() `：在控制器方法执行之前执行，如果返回true表示放行，返回false表示拦截
> - `postHandle()` ：在控制器方法执行之后执行
> - `afterCompletion()`：在控制器方法执行后，且视图渲染完毕后执行

```java
@Component
public class FirstInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("first preHandle");
        return HandlerInterceptor.super.preHandle(request, response, handler);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("first postHandle");
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("first afterCompletion");
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```



**2、在springmvc的配置文件中配置拦截器**

> 有三种配置拦截器的方法
>
> 1、使用bean标签配置，class属性中设置拦截器的全类名
>
> 2、使用ref标签配置，此时拦截器必须被ioc管理才可以引用
>
> 3、使用`<mvc:interceptor>`配置，此时可以设置拦截的路径、不拦截的路径、以及拦截器
>
> 前两种方式默认对处理所有请求的方法拦截

```xml
<mvc:interceptors>
    <!-- bean和ref标签所配置的拦截器默认对DispatcherServlet处理的所有请求进行拦截 -->
    <!--<bean class="com.xrj.interceptor.FirstInterceptor"/>-->
    <!--<ref bean="firstInterceptor"/>-->
    <mvc:interceptor>
        <!-- 配置需要拦截的请求路径，/*表示单层目录，/**表示任意层目录 -->
        <mvc:mapping path="/**"/>
        <!-- 配置不拦截的路径 -->
        <mvc:exclude-mapping path="/abc"/>
        <!-- 配置拦截器 -->
        <ref bean="firstInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```





### 3.9.3 多个拦截器执行顺序

1、`preHandle() `在控制器方法执行之前执行，`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`是执行控制器方法，在他执行会先执行`mappedHandler.applyPreHandle(processedRequest, response)`，在该方法内部，会顺序执行每个拦截器的`preHandle()`方法。

如果拦截器方法返回false，那么就去执行`triggerAfterCompletion`方法，该方法就会执行拦截器的`afterCompletion()`，但是不包括该拦截器，`interceptorIndex`属性控制着执行到了哪一个拦截器，如果第k个拦截器的`preHandle()`方法返回false，那么就会从k-1到0，逆序执行前k-1个拦截器的`afterCompletion()`方法。 

即如果拦截器返回false，那么该控制器方法被拦截就不再执行了，也就不执行`postHandle()`方法，但是会执行前面拦截器的`afterCompletion()`方法，执行完毕后在`DispatcherServlet`中就会直接return掉，不再执行控制器方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261131696.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261131037.png)



2、`postHandle()` 在控制器方法执行之后执行，在执行完`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`后，会执行`mappedHandler.applyPostHandle(processedRequest, response, mv);`。该方法会逆序执行各个拦截器的`postHandle()`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261130129.png)



3、`afterCompletion()`在控制器方法执行后，且视图渲染完毕后执行。进行视图渲染时会执行`this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);`。在该方法中，执行完render方法进行视图渲染完毕之后，会执行`mappedHandler.triggerAfterCompletion(request, response, (Exception)null);`。在该方法中，会逆序执行各个拦截器的`afterCompletion`方法，`interceptorIndex`表示最后一个放行的拦截器的下标。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261130671.png)











## 3.10 异常处理器

> 在我们的控制器方法中，如果产生异常，我们就用springmvc为我们提供的异常解析器来统一处理异常

- 声明式管理异常：在配置文件中指定异常类型和视图之间的对应关系。在配置文件或注解类中统一管理

- 编程式管理异常：需要我们自己手动 try ... catch ... 捕获异常，然后再手动跳转到某个页面。



SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：`HandlerExceptionResolver`

`HandlerExceptionResolver`接口的实现类有：`DefaultHandlerExceptionResolver`和 `SimpleMappingExceptionResolver`

SpringMVC默认使用的是`DefaultHandlerExceptionResolver`，如果我们有自定义的异常，SpringMVC提供了自定义的异常处理器`SimpleMappingExceptionResolver`



 这是`DefaultHandlerExceptionResolver`中处理异常的方法，它的返回值是`ModelAndView`。我们的控制层方法如果正常执行完毕，返回的就是一个`ModelAndView`，如果产生异常，那么返回的也是`ModelAndView`。默认的异常处理器中只设置了处理的部分异常，如果想处理更多异常，需要使用`SimpleMappingExceptionResolver`类实现

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307261737350.png)





### 3.10.1 xml配置

> 在springmvc.xml中添加如下配置
>
> 如果控制层方法产生算术异常，那么就会被该异常处理器捕获到，跳转到error页面，同时将异常信息保存在请求域中，key值为ex

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <!--
            properties的键表示处理器方法执行过程中出现的异常
            properties的值表示若出现指定异常时，设置一个新的视图名称，跳转到指定页面
        -->
        <props>
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <!--
        使用 exceptionAttribute 属性配置将异常对象存入请求域时使用的属性名
        即将异常信息保存在ModelAndView的model中，model的键为ex
    -->
    <property name="exceptionAttribute" value="ex"></property>
</bean>
```





### 3.10.2 基于注解配置

> 创建一个类使用`@ControllerAdvice`标识，表示当前类为异常处理的组件。同时创建相应的处理异常的方法，使用`@ExceptionHandler`标识，注解的参数位置添加处理的异常数组，表示遇到该异常就通过此方法进行处理。在该方法中，可以直接通过形参获取异常，将异常存在model对象中，然后返回到error页面。**当同一个异常类型在基于 XML 和注解的配置中都能够找到对应的映射，那么以注解为准。**

```java
// @ControllerAdvice将当前类标识为异常处理的组件
@ControllerAdvice
public class ExceptionController {
    // @ExceptionHandler用于设置所标识方法处理的异常
    @ExceptionHandler(ArithmeticException.class)
    public String handleArithmeticEx(Exception ex, Model model){
        model.addAttribute("ex", ex);
        return "error";
    }
}
```



执行控制器方法有异常发生时，会被捕获到，在进行视图渲染时，会判断是否有异常，然后拿到异常处理器，如果我们设置了异常处理器，那么就会根据我们设置的异常处理器进行处理，然后获得我们设置的`ModelAndView`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307271021021.png)









## 3.11 注解配置SpringMVC

> 使用配置类和注解代替web.xml和SpringMVC配置文件



**1、创建初始化类，代替web.xml**

> 在Servlet3.0环境中，容器会在类路径中查找实现`javax.servlet.ServletContainerInitializer`接口的类， 如果找到的话就用它来配置Servlet容器即Tomcat。 
>
> Spring提供了这个接口的实现，名为 `SpringServletContainerInitializer`，这个类反过来又会查找实现`WebApplicationInitializer`的类并将配置的任务交给它们来完成。
>
> Spring3.2引入了一个便利的`WebApplicationInitializer`基础实现，名为 `AbstractAnnotationConfigDispatcherServletInitializer`，当我们的类扩展了 `AbstractAnnotationConfigDispatcherServletInitializer`并将其部署到Servlet3.0容器的时候，容器会自动发现它，并用它来配置Servlet上下文。

```JAVA
// 这个类相当于web.xml
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    /**
     * 指定spring的配置类
     */
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    /**
     * 指定springmvc的配置类
     */
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    @Override
    /**
     * 指定DispatcherServlet的映射规则，即url-pattern
     */
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * 添加过滤器
     * 这里没有设置url-pattern，因此它们将自动应用于所有传入的请求
     */
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter, hiddenHttpMethodFilter};
    }
}
```





**2、创建SpringConfig配置类，代替spring的配置文件**

```java
// @Configuration注解将类标识为配置类，这个类相当于applicationContext.xml
@Configuration
public class SpringConfig {
    // ssm整合之后，spring的配置信息写在此类中
}
```





**3、创建WebConfig配置类，代替SpringMVC的配置文件**

```java
// @Configuration注解将类标识为配置类， 这个类相当于springmvc.xml
// 扫描组件、视图解析器、默认的servlet、mvc注解驱动
// 视图控制器、文件上传解析器、拦截器、异常解析器
@Configuration
// 扫描组件
@ComponentScan("com.xrj.controller")
// 开启mvc注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    // 使用默认的servlet处理静态资源
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    // 配置视图控制器
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }

    // 文件上传解析器
    // @Bean注解可以将表示的方法的返回值作为bean进行管理，方法名就是bean的id
    @Bean
    public CommonsMultipartResolver multipartResolver(){
        return new CommonsMultipartResolver();
    }

    // 配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        FirstInterceptor firstInterceptor = new FirstInterceptor();
        registry.addInterceptor(firstInterceptor).addPathPatterns("/**").excludePathPatterns("/abd");
    }

    // 异常处理器
    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver simpleMappingExceptionResolver = new SimpleMappingExceptionResolver();
        Properties properties = new Properties();
        properties.setProperty("java.lang.ArithmeticException", "error");
        // 设置异常映射
        simpleMappingExceptionResolver.setExceptionMappings(properties);
        // 设置异常信息存储的键
        simpleMappingExceptionResolver.setExceptionAttribute("ex");
        resolvers.add(simpleMappingExceptionResolver);
    }

    // 配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext 的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }
    // 生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }
    // 生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
}
```







## 3.12 SpringMVC执行流程

### 1、SpringMVC常用组件

- DispatcherServlet：前端控制器，不需要工程师开发，由框架提供 

  作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求 

- HandlerMapping：处理器映射器，不需要工程师开发，由框架提供

  作用：根据请求的url、method等信息查找Handler，即控制器方法

- Handler：处理器，需要工程师开发，即controller方法

  作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理 

- HandlerAdapter：处理器适配器，不需要工程师开发，由框架提供 

  作用：通过HandlerAdapter对处理器（控制器方法）进行执行 

- ViewResolver：视图解析器，不需要工程师开发，由框架提供 

  作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、 RedirectView 

- View：视图 

  作用：将模型数据通过页面展示给用户





### 2、DispatcherServlet初始化过程

> DispatcherServlet 本质上是一个 Servlet，所以天然的遵循 Servlet 的生命周期。所以宏观上是 Servlet 生命周期来进行调度。



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202307271752259.png)







**DispatcherServlet** extends **FrameworkServlet** extends **HttpServletBean** extends **HttpServlet** extends **GenericServlet** implements **Servlet**



**（1）HttpServletBean的init方法**

> 最终执行`this.initServletBean();`  该方法由`FrameworkServlet`类实现

```java
public final void init() throws ServletException {
    PropertyValues pvs = new HttpServletBean.ServletConfigPropertyValues(this.getServletConfig(), this.requiredProperties);
    if (!pvs.isEmpty()) {
        try {
            BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(this);
            ResourceLoader resourceLoader = new ServletContextResourceLoader(this.getServletContext());
            bw.registerCustomEditor(Resource.class, new ResourceEditor(resourceLoader, this.getEnvironment()));
            this.initBeanWrapper(bw);
            bw.setPropertyValues(pvs, true);
        } catch (BeansException var4) {
            if (this.logger.isErrorEnabled()) {
                this.logger.error("Failed to set bean properties on servlet '" + this.getServletName() + "'", var4);
            }

            throw var4;
        }
    }

    this.initServletBean();
}
```



**（2）`FrameworkServlet`的`initServletBean()`方法**

```java
protected final void initServletBean() throws ServletException {
    this.getServletContext().log("Initializing Spring " + this.getClass().getSimpleName() + " '" + this.getServletName() + "'");
    if (this.logger.isInfoEnabled()) {
        this.logger.info("Initializing Servlet '" + this.getServletName() + "'");
    }

    long startTime = System.currentTimeMillis();

    try {
        // 初始化springmvc的ioc容器，即初始化DispatcherServlet的时候就要初始化springmvc的ioc容器
        this.webApplicationContext = this.initWebApplicationContext();
        // initFrameworkServlet()是空实现
        this.initFrameworkServlet();
    } catch (RuntimeException | ServletException var4) {
        this.logger.error("Context initialization failed", var4);
        throw var4;
    }

    if (this.logger.isDebugEnabled()) {
        String value = this.enableLoggingRequestDetails ? "shown which may lead to unsafe logging of potentially sensitive data" : "masked to prevent unsafe logging of potentially sensitive data";
        this.logger.debug("enableLoggingRequestDetails='" + this.enableLoggingRequestDetails + "': request parameters and headers will be " + value);
    }

    if (this.logger.isInfoEnabled()) {
        this.logger.info("Completed initialization in " + (System.currentTimeMillis() - startTime) + " ms");
    }

}
```



**（3）`FrameworkServlet`的`initWebApplicationContext()`方法**

```java
protected WebApplicationContext initWebApplicationContext() {
    // 拿到ioc容器的路径
    WebApplicationContext rootContext = WebApplicationContextUtils.getWebApplicationContext(this.getServletContext());
    WebApplicationContext wac = null;
    if (this.webApplicationContext != null) {
        wac = this.webApplicationContext;
        if (wac instanceof ConfigurableWebApplicationContext) {
            ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext)wac;
            if (!cwac.isActive()) {
                if (cwac.getParent() == null) {
                    cwac.setParent(rootContext);
                }

                this.configureAndRefreshWebApplicationContext(cwac);
            }
        }
    }
	// 如果springmvc的ioc容器为空就去找
    if (wac == null) {
        wac = this.findWebApplicationContext();
    }
	// 如果springmvc的ioc容器为空就创建
    if (wac == null) {
        wac = this.createWebApplicationContext(rootContext);
    }

    if (!this.refreshEventReceived) {
        synchronized(this.onRefreshMonitor) {
            // 刷新WebApplicationContext
            this.onRefresh(wac);
        }
    }

    if (this.publishContext) {
        String attrName = this.getServletContextAttributeName();
        // 将IOC容器在应用域共享
        this.getServletContext().setAttribute(attrName, wac);
    }

    return wac;
}
```




**（4）`FrameworkServlet`的`createWebApplicationContext`方法**

```java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
    Class<?> contextClass = this.getContextClass();
    if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
        throw new ApplicationContextException("Fatal initialization error in servlet with name '" + this.getServletName() + "': custom WebApplicationContext class [" + contextClass.getName() + "] is not of type ConfigurableWebApplicationContext");
    } else {
        // 通过反射创建 IOC 容器对象
        ConfigurableWebApplicationContext wac = (ConfigurableWebApplicationContext)BeanUtils.instantiateClass(contextClass);
        wac.setEnvironment(this.getEnvironment());
        // 设置父容器，springmvc的ioc容器的父容器就是spring的ioc容器
        wac.setParent(parent);
        String configLocation = this.getContextConfigLocation();
        if (configLocation != null) {
            wac.setConfigLocation(configLocation);
        }

        this.configureAndRefreshWebApplicationContext(wac);
        return wac;
    }
}
```



**（5）`DispatcherServlet`的`onRefresh`方法**

> `FrameworkServlet`创建WebApplicationContext后，刷新容器，调用`onRefresh(wac)`，此方法在 DispatcherServlet中进行了重写，调用了`initStrategies(context)`方法，初始化策略，即初始化 DispatcherServlet的各个组件

```java
protected void onRefresh(ApplicationContext context) {
    this.initStrategies(context);
}
```

**（6）`DispatcherServlet`的`initStrategies`方法**

```java
protected void initStrategies(ApplicationContext context) {
    this.initMultipartResolver(context);
    this.initLocaleResolver(context);
    this.initThemeResolver(context);
    this.initHandlerMappings(context);
    this.initHandlerAdapters(context);
    this.initHandlerExceptionResolvers(context);
    this.initRequestToViewNameTranslator(context);
    this.initViewResolvers(context);
    this.initFlashMapManager(context);
}
```





### 3、DispatcherServlet的service方法执行过程

1、在`HttpServlet`的`service`方法中，首先将`request`和`response`转为`HttpServletRequest`和`HttpServletResponse`，然后调用重载的`service`方法。在该`service`方法中，根据请求方式的不同，调用不同的方法。所以自己使用原始servlet时，需要重写doGet和doPost方法，否则报405错误



2、在`FrameworkServlet`中的`service`方法

如果当前请求是PATCH，那么调用`this.processRequest(request, response);`  否则调用父类的service方法，然后父类的service方法根据请求方式不同，调用对应的doGet或doPost等方法，但是在该类将所有的doXxx方法都重写了，都是执行`this.processRequest(request, response);`

```java
protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    HttpMethod httpMethod = HttpMethod.resolve(request.getMethod());
    if (httpMethod != HttpMethod.PATCH && httpMethod != null) {
        super.service(request, response);
    } else {
        this.processRequest(request, response);
    }

}
```



3、在`FrameworkServlet`中的`processRequest`方法

该方法会执行`this.doService(request, response);`  但是`doService`方法是一个抽象方法，因此会执行其子类DispatcherServlet的`doService`方法

```java
protected final void processRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    long startTime = System.currentTimeMillis();
    Throwable failureCause = null;
    LocaleContext previousLocaleContext = LocaleContextHolder.getLocaleContext();
    LocaleContext localeContext = this.buildLocaleContext(request);
    RequestAttributes previousAttributes = RequestContextHolder.getRequestAttributes();
    ServletRequestAttributes requestAttributes = this.buildRequestAttributes(request, response, previousAttributes);
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
    asyncManager.registerCallableInterceptor(FrameworkServlet.class.getName(), new FrameworkServlet.RequestBindingInterceptor());
    this.initContextHolders(request, localeContext, requestAttributes);

    try {
        // 执行服务，doService()是一个抽象方法，在DispatcherServlet中进行了重写
        this.doService(request, response);
    } catch (IOException | ServletException var16) {
        failureCause = var16;
        throw var16;
    } catch (Throwable var17) {
        failureCause = var17;
        throw new NestedServletException("Request processing failed", var17);
    } finally {
        this.resetContextHolders(request, previousLocaleContext, previousAttributes);
        if (requestAttributes != null) {
            requestAttributes.requestCompleted();
        }

        this.logResult(request, response, (Throwable)failureCause, asyncManager);
        this.publishRequestHandledEvent(request, response, startTime, (Throwable)failureCause);
    }

}
```



4、`DispatcherServlet`类的`doService`方法

当执行到`this.doDispatch(request, response);`时，调用`doDispatch`方法

```java
protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
    this.logRequest(request);
    Map<String, Object> attributesSnapshot = null;
    if (WebUtils.isIncludeRequest(request)) {
        attributesSnapshot = new HashMap();
        Enumeration attrNames = request.getAttributeNames();

        label120:
        while(true) {
            String attrName;
            do {
                if (!attrNames.hasMoreElements()) {
                    break label120;
                }

                attrName = (String)attrNames.nextElement();
            } while(!this.cleanupAfterInclude && !attrName.startsWith("org.springframework.web.servlet"));

            attributesSnapshot.put(attrName, request.getAttribute(attrName));
        }
    }

    request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, this.getWebApplicationContext());
    request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);
    request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
    request.setAttribute(THEME_SOURCE_ATTRIBUTE, this.getThemeSource());
    if (this.flashMapManager != null) {
        FlashMap inputFlashMap = this.flashMapManager.retrieveAndUpdate(request, response);
        if (inputFlashMap != null) {
            request.setAttribute(INPUT_FLASH_MAP_ATTRIBUTE, Collections.unmodifiableMap(inputFlashMap));
        }

        request.setAttribute(OUTPUT_FLASH_MAP_ATTRIBUTE, new FlashMap());
        request.setAttribute(FLASH_MAP_MANAGER_ATTRIBUTE, this.flashMapManager);
    }

    RequestPath requestPath = null;
    if (this.parseRequestPath && !ServletRequestPathUtils.hasParsedRequestPath(request)) {
        requestPath = ServletRequestPathUtils.parseAndCache(request);
    }

    try {
        // 处理请求和响应
        this.doDispatch(request, response);
    } finally {
        if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted() && attributesSnapshot != null) {
            this.restoreAttributesAfterInclude(request, attributesSnapshot);
        }

        if (requestPath != null) {
            ServletRequestPathUtils.clearParsedRequestPath(request);
        }

    }

}
```



5、`DispatcherServlet`类的`doDispatch`方法

该方法就是`DispatcherServlet`真正执行控制层方法的地方

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
        try {
            ModelAndView mv = null;
            Object dispatchException = null;

            try {
                processedRequest = this.checkMultipart(request);
                multipartRequestParsed = processedRequest != request;
                /*
                    mappedHandler：调用链
                    包含handler、interceptorList、interceptorIndex
                    handler：浏览器发送的请求所匹配的控制器方法
                    interceptorList：处理控制器方法的所有拦截器集合
                    interceptorIndex：拦截器索引，控制拦截器afterCompletion()的执行
                */
                mappedHandler = this.getHandler(processedRequest);
                if (mappedHandler == null) {
                    this.noHandlerFound(processedRequest, response);
                    return;
                }
				
                // 创建处理器适配器，用来执行控制器方法
                HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
                String method = request.getMethod();
                boolean isGet = "GET".equals(method);
                if (isGet || "HEAD".equals(method)) {
                    long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                    if ((new ServletWebRequest(request, response)).checkNotModified(lastModified) && isGet) {
                        return;
                    }
                }
				
                // 调用拦截器的preHandle()
                if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                    return;
                }
				
                // 由处理器适配器调用具体的控制器方法，最终获得ModelAndView对象
                mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                if (asyncManager.isConcurrentHandlingStarted()) {
                    return;
                }

                this.applyDefaultViewName(processedRequest, mv);
                // 调用拦截器的postHandle()
                mappedHandler.applyPostHandle(processedRequest, response, mv);
            } catch (Exception var20) {
                dispatchException = var20;
            } catch (Throwable var21) {
                dispatchException = new NestedServletException("Handler dispatch failed", var21);
            }
			// 后续处理：处理模型数据和渲染视图，同时在渲染完视图后，执行拦截器的afterCompletion()
            this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);
        } catch (Exception var22) {
            this.triggerAfterCompletion(processedRequest, response, mappedHandler, var22);
        } catch (Throwable var23) {
            this.triggerAfterCompletion(processedRequest, response, mappedHandler, new NestedServletException("Handler processing failed", var23));
        }

    } finally {
        if (asyncManager.isConcurrentHandlingStarted()) {
            if (mappedHandler != null) {
                mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
            }
        } else if (multipartRequestParsed) {
            this.cleanupMultipart(processedRequest);
        }

    }
}
```







### 4、SpringMVC执行流程

1、用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。 

2、DispatcherServlet对请求URL【带协议、ip地址、端口号】进行解析，得到请求资源标识符URI【不带协议、ip、端口号】，判断请求URI对应的映射：

- 不存在

  判断是否配置了`mvc:default-servlet-handler` 

  - 如果没配置，则控制台报映射查找不到，客户端展示404错误
  - 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404 错误

- 如果存在，则继续执行下面的流程

3、根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象、 Handler对象对应的拦截器和拦截器索引），最后以HandlerExecutionChain执行链对象的形式返回。 

> 首先DispatcherServlet收到请求后，通过`mappedHandler = this.getHandler(processedRequest);`这行代码获取到处理该请求的handle方法，即Controller方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242204266.png)



> 在`getHandler`方法中，他会通过`handlerMappings`去寻找对应的handle方法，目前有4个`handlerMappings`，他会一个个的找

```java
@Nullable
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
    if (this.handlerMappings != null) {
        Iterator var2 = this.handlerMappings.iterator();

        while(var2.hasNext()) {
            HandlerMapping mapping = (HandlerMapping)var2.next();
            HandlerExecutionChain handler = mapping.getHandler(request);
            if (handler != null) {
                return handler;
            }
        }
    }

    return null;
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242207254.png)

> 在每个`handlerMapping`中都会有自己可以处理的请求对应的handle方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242212095.png)



> `HandlerExecutionChain handler = mapping.getHandler(request);`执行这行代码就会通过当前的`handlerMapping`通过`getHandler`方法去找对应的handle方法，找到后，执行`HandlerExecutionChain executionChain = this.getHandlerExecutionChain(handler, request);`  将当前的handel方法以及拦截器都放在一个调用链中

![image-20230824221336060](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20230824221336060.png)



`org.springframework.web.servlet.handler.AbstractHandlerMapping`的`getHandlerExecutionChain()`方法

```java
protected HandlerExecutionChain getHandlerExecutionChain(Object handler, HttpServletRequest request) {
    // 创建调用链对象，handler是目标handler对象
    HandlerExecutionChain chain = handler instanceof HandlerExecutionChain ? (HandlerExecutionChain)handler : new HandlerExecutionChain(handler);
    Iterator var4 = this.adaptedInterceptors.iterator();

    while(var4.hasNext()) {
        HandlerInterceptor interceptor = (HandlerInterceptor)var4.next();
        if (interceptor instanceof MappedInterceptor) {
            MappedInterceptor mappedInterceptor = (MappedInterceptor)interceptor;
            if (mappedInterceptor.matches(request)) {
                // 添加拦截器
                chain.addInterceptor(mappedInterceptor.getInterceptor());
            }
        } else {
            chain.addInterceptor(interceptor);
        }
    }

    return chain;
}
```

4、DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。 

```java
// HandlerAdapter 这个适配器是将底层的 HTTP 报文、原生的 request 对象进行解析和封装，『适配』到我们定义的 handler 方法上
HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
```

5、如果成功获得HandlerAdapter，此时将开始执行拦截器的`preHandler(…)`方法【正向】 

6、提取Request中的模型数据，填充Handler入参【将请求的参数通过反射注入控制层方法的形参】，开始执行Handler（Controller)方法，处理请求。 在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作： 

- HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息 。例如：`@RequestBody`获取请求体信息，`@RequestHeader`获得请求头信息
- 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等 
- 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等 
- 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中 【现在都是前端通过js验证】

7、Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。 

8、此时将开始执行拦截器的`postHandle(...)`方法【逆向】。 

9、根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行 HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model 和View，来渲染视图。 

10、渲染视图完毕执行拦截器的`afterCompletion(…)`方法【逆向】。 

11、将渲染结果返回给客户端。





 



## 3.13 监听器

DispatcherServlet 加载 spring-mvc.xml，此时整个 Web 应用中只创建一个 IOC 容器。

将来整合Mybatis、配置声明式事务，以及业务层、持久层的bean全部在 spring-mvc.xml 配置文件中配置也是可以的。但是这样会导致配置文件太长，不容易维护。

所以把配置文件分开：

- spring-mvc.xml 配置文件：处理浏览器请求相关
- spring.xml 配置文件：配置业务层和持久层的bean、声明式事务、整合MyBatis



1、此时就会创建两个ioc容器，一个是springmvc创建的，一个是spring创建的。springmvc中的controller组件需要使用到service以及dao组件进行自动装配，此时他就用到了spring的ioc容器。

2、springmvc的ioc容器是在DispatcherServlet初始化的时候创建的【我们设置了`<load-on-startup>1</load-on-startup>`即服务器启动就创建servlet】，它需要用到spring的ioc容器中的组件，因此**spring的ioc容器必须在springmvc的ioc容器创建之前创建。**

3、为了使spring的ioc容器在springmvc的ioc容器创建之前创建，我们就使用一个监听器，在javaweb阶段我们的ioc容器就是在ServletContext创建的时候创建，因此使用了`ServletContextListener`，这里我们同样使spring的ioc容器在ServletContext创建的时候创建

4、Spring提供了监听器`ContextLoaderListener`，实现`ServletContextListener`接口，可监听 ServletContext的状态，在web服务器的启动时，读取Spring的配置文件，创建Spring的IOC容器。web 应用中必须在web.xml中配置





**配置ContextLoaderListener**

> springmvc的初始化参数是servlet中的，而监听器的初始化参数放在ServletContext域中

```xml
<listener>
    <!--
        配置Spring的监听器，在服务器启动时加载Spring的配置文件
        Spring配置文件默认位置和名称：/WEB-INF/applicationContext.xml
        可通过上下文参数自定义Spring配置文件的位置和名称
    -->
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
</context-param>
```



1、在`ContextLoaderListener`的`contextInitialized`方法中，即监听到了`ServletContext`的创建，那么他就执行`initWebApplicationContext`方法。在该方法中，创建了spring的ioc容器后，会将该容器保存在servletContext域中。

2、在springmvc的DispatcherServlet初始化时会创建springmvc的ioc容器，该容器也会放在servletContext域中。同时，他还会设置自己的父容器，即spring的ioc容器。**springmvc的ioc容器是子容器，spring的ioc容器是父容器，子容器可以访问父容器的bean，父容器不能访问子容器的bean**

- Tomcat 在读取 web.xml 之后，加载组件的顺序就是监听器、过滤器、Servlet。
- ContextLoaderListener 初始化时如果检查到有已经存在的根级别 IOC 容器，那么会抛出异常。
- DispatcherServlet 创建的 IOC 容器会在初始化时先检查当前环境下是否存在已经创建好的 IOC 容器。
    - 如果有：则将已存在的这个 IOC 容器设置为自己的父容器
    - 如果没有：则将自己设置为 root 级别的 IOC 容器

```java
public WebApplicationContext initWebApplicationContext(ServletContext servletContext) {
    if (servletContext.getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE) != null) {
        throw new IllegalStateException("Cannot initialize context because there is already a root application context present - check whether you have multiple ContextLoader* definitions in your web.xml!");
    } else {
        servletContext.log("Initializing Spring root WebApplicationContext");
        Log logger = LogFactory.getLog(ContextLoader.class);
        if (logger.isInfoEnabled()) {
            logger.info("Root WebApplicationContext: initialization started");
        }

        long startTime = System.currentTimeMillis();

        try {
            if (this.context == null) {
                this.context = this.createWebApplicationContext(servletContext);
            }

            if (this.context instanceof ConfigurableWebApplicationContext) {
                ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext)this.context;
                if (!cwac.isActive()) {
                    if (cwac.getParent() == null) {
                        ApplicationContext parent = this.loadParentContext(servletContext);
                        cwac.setParent(parent);
                    }

                    this.configureAndRefreshWebApplicationContext(cwac, servletContext);
                }
            }
			
            // 将ioc容器保存到servletContext域中
            servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, this.context);
            ClassLoader ccl = Thread.currentThread().getContextClassLoader();
            if (ccl == ContextLoader.class.getClassLoader()) {
                currentContext = this.context;
            } else if (ccl != null) {
                currentContextPerThread.put(ccl, this.context);
            }

            if (logger.isInfoEnabled()) {
                long elapsedTime = System.currentTimeMillis() - startTime;
                logger.info("Root WebApplicationContext initialized in " + elapsedTime + " ms");
            }

            return this.context;
        } catch (Error | RuntimeException var8) {
            logger.error("Context initialization failed", var8);
            servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, var8);
            throw var8;
        }
    }
}
```









# 四、SSM

**1、springmvc的配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <context:component-scan base-package="com.xrj.controller"/>

    <!--配置视图解析器-->
    <bean id="viewResolver"
          class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean
                            class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8" />
                    </bean>
                </property>
            </bean>
        </property>
    </bean>

    <mvc:annotation-driven/>
    <mvc:default-servlet-handler/>
    <mvc:view-controller path="/" view-name="index"/>

</beans>
```





**2、spring的配置文件**

> 这里通过spring整合MyBatis
>
> 1、通过配置SqlSessionFactoryBean的bean，通过ioc可以直接获取`SqlSessionFactory`对象，也可以在这里设置mybatis核心配置文件的内容
>
> 2、设置MapperScannerConfigurer的bean，可以将指定包下的所有mapper接口，通过sqlSession创建代理实现类对象，并交给ioc管理
>
> 注意：这里指定的包其实就是mapper接口的包和mapper配置文件的包。如果没有MapperScannerConfigurer这个bean，那么SqlSessionFactoryBean中的mapperLocations属性一定要设置，不管映射文件的包和mapper接口的包是否一致。但是设置了MapperScannerConfigurer这个bean后，只有mapper接口和配置所在包不一致时才需要设置这个属性

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.xrj">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driver" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!-- 这里配置的是工厂bean，可以直接得到SqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!--  这里也可以通过属性来设置，这里设置后，mybatis核心配置文件就可以不设置了 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 设置别名 -->
        <property name="typeAliasesPackage" value="com.xrj.pojo"/>
        <!-- 设置mapper文件路径，只有映射文件的包和mapper接口的包不一致时才需要设置 -->
        <!--<property name="mapperLocations" value="classpath:mappers/*.xml"/>-->
    </bean>

    <!-- 配置mapper接口的扫描，可以将指定包下的所有mapper接口，通过sqlSession创建代理实现类对象，并交给ioc管理 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.xrj.mapper"/>
    </bean>
</beans>
```





**设置导航分页**

**html**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>emp list</title>
    <link rel="stylesheet" th:href="@{/static/css/index_work.css}">
</head>
<body>

<div id="app">
    <table>
        <tr>
            <th colspan="6">员工列表</th>
        </tr>
        <tr>
            <th>流水号</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>性别</th>
            <th>email</th>
            <th>操作 (<a th:href="@{/to/add}">添加</a>) </th>
        </tr>
        <!-- status是一个辅助对象，给我们提供循环过程中的信息 -->
        <tr th:each="emp, status : ${pageInfo.list}">
            <td th:text="${status.count}"></td>
            <td th:text="${emp.empName}"></td>
            <td th:text="${emp.age}"></td>
            <td th:text="${emp.gender}"></td>
            <td th:text="${emp.email}"></td>
            <td>
                <a th:href="@{|emp/${emp.empId}|}">修改</a>
                <a @click="deleteEmp()" th:href="@{|emp/${emp.empId}|}">删除</a>
            </td>
        </tr>
    </table>
    <form method="post">
        <input type="hidden" name="_method" value="delete">
    </form>
</div>

<div style="text-align: center">
    <a th:if="!${pageInfo.isFirstPage}" th:href="@{/emp/page/1}">首页</a>
    <a th:if="${pageInfo.hasPreviousPage}" th:href="@{|/emp/page/${pageInfo.prePage}|}">上一页</a>

    <!-- 导航分页 -->
    <span th:each="num : ${pageInfo.navigatepageNums}">
        <a th:if="${pageInfo.pageNum} == ${num}" style="color: red" th:href="@{|/emp/page/${num}|}" th:text="|[${num}]|"></a>
        <a th:if="${pageInfo.pageNum} != ${num}" th:href="@{|/emp/page/${num}|}" th:text="${num}"></a>
    </span>

    <a th:if="${pageInfo.hasNextPage}" th:href="@{|/emp/page/${pageInfo.nextPage}|}">下一页</a>
    <a th:if="!${pageInfo.isLastPage}" th:href="@{|/emp/page/${pageInfo.pages}|}">尾页</a>
</div>

<script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            deleteEmp(){
                // 获取form表单
                var form = document.getElementsByTagName("form")[0];
                // 将超链接的href属性赋值给form的action属性
                // event.target表示当前触发事件的标签
                form.action = event.target.href;
                // 表单提交
                form.submit();
                // 阻止a标签的默认行为，即跳转href
                event.preventDefault();
            }
        }
    })
</script>
</body>
</html>
```



**controller**

```java
@RequestMapping(value = "/emp/page/{pageNum}", method = RequestMethod.GET)
public String getListByPage(@PathVariable("pageNum") Integer pageNum, Model model){
    PageInfo<Emp> pageInfo = empService.getPageInfo(pageNum);
    model.addAttribute("pageInfo", pageInfo);
    return "empList";
}
```



**service**

```java
@Override
public PageInfo<Emp> getPageInfo(Integer pageNum) {
    // 开启分页功能
    PageHelper.startPage(pageNum, 5);
    // 开启分页功能后，会自动在sql语句上添加limit关键字
    List<Emp> empList = empMapper.getEmpList();
    PageInfo<Emp> pageInfo = new PageInfo<>(empList, 5);
    return pageInfo;
}
```
