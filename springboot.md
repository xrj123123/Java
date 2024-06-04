# springboot入门



### 一、创建springboot项目



①：创建新模块，选择Spring Initializr，并配置模块相关基础信息

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220208142943430.png" alt="image-20220208142943430" style="zoom:80%;" />





②：选择当前模块需要使用的技术集

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220208142955543.png" alt="image-20220208142955543" style="zoom:80%;" />



③：开发控制器类

```java
// @RestController 相当于 @Controller+@ResponseBody
@RestController
@RequestMapping("/books")
public class BookController {

    // @GetMapping等价于@RequestMapping(method = RequestMethod.Get )
    @GetMapping
    public  String getById(){
        System.out.println("springboot is running...2");
        return "springboot is running...2";
    }
}
```



④：运行自动生成的Application类

```java
@SpringBootApplication
public class Springboot09ProfilesApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot09ProfilesApplication.class, args);
    }
}
```





### 二、springboot配置

##### parent

- 开发SpringBoot程序要继承spring-boot-starter-parent

- spring-boot-starter-parent中定义了若干个依赖管理

- 继承parent模块可以避免多个依赖使用相同技术时出现依赖版本冲突

- 继承parent的形式也可以采用引入依赖的形式实现效果

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.3</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```





**starter**

- SpringBoot中常见项目名称，定义了当前项目使用的所有依赖坐标，以达到减少依赖配置的目的

  

 **parent**

- 所有SpringBoot项目要继承的项目，定义了若干个坐标版本号（依赖管理，而非依赖），以达到减少依赖冲突的目的

- spring-boot-starter-parent各版本间存在着诸多坐标版本不同

  

**实际开发**

- 使用任意坐标时，仅书写GAV中的G和A，V由SpringBoot提供，除非SpringBoot未提供对应版本V 

- 如发生坐标错误，再指定Version（要小心版本冲突）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



##### 引导类 

- SpringBoot的引导类是Boot工程的执行入口，运行main方法就可以启动项目

- SpringBoot工程运行后初始化Spring容器，扫描引导类所在包加载bean



启动方式

```java
@SpringBootApplication
public class Springboot01QuickstartApplication {
    public static void main(String[] args) {
        SpringApplication.run(Springboot01QuickstartApplication.class, args);
    }
}
```



##### 内嵌tomcat

- 内嵌Tomcat服务器是SpringBoot辅助功能之一

- 内嵌Tomcat工作原理是将Tomcat服务器作为对象运行，并将该对象交给Spring容器管理





### 三、基础配置



##### 属性配置

- 修改服务器端口

SpringBoot默认配置文件application.properties，通过键值对配置对应属性

```xml
server.port=80
```



- 关闭运行日志图标（banner） 

```xml
spring.main.banner-mode=off
```



- 设置日志文件

```xml
#logging.level.root=error
#logging.level.root=debug
logging.level.root=info
```





##### SpringBoot提供了多种属性配置方式

- application.**properties**

```xml
server.port=80
```



- application.**yml	**		（主流格式）

```xml
server:
  port: 81
```



- application.**yaml**

```xml
server:
  port: 82
```



application.**properties >** application.**yml >** application.**yaml**



不同配置文件中相同配置按照加载优先级相互覆盖，不同配置文件中不同配置全部保留





##### yaml

YAML（YAML Ain't Markup Language），一种数据序列化格式

###### 优点

- 容易阅读
- 容易与脚本语言交互
- 以数据为核心，重数据轻格式



###### YAML文件扩展名

- yml（主流）

- yaml



###### **yaml**语法规则

- 大小写敏感

- 属性层级关系使用多行描述，每行结尾使用冒号结束

- 使用缩进表示层级关系，同层级左侧对齐，只允许使用空格（不允许使用Tab键）

- 属性值前面添加空格（属性名与属性值之间使用冒号+空格作为分隔）

- （#） 表示注释

- 核心规则：**数据前面要加空格与冒号隔开**



###### **yaml**数据读取



**读取单个数据**



- 使用@Value读取单个数据，属性名引用方式：${一级属性名 . 二级属性名……}

```java
@Value("${country}")
private String country1;
```



- 在配置文件中可以使用属性名引用方式引用属性

```xml
baseDir: c:\windows

# 使用${属性名}引用数据
tempDir: ${baseDir}\temp
```



- 属性值中如果出现转移字符，需要使用双引号包裹

```xml
# 使用引号包裹的字符串，其中的转义字符可以生效
tempDir2: "${baseDir}\temp \t1 \t2 \t3"
```





**读取全部数据**

封装全部数据到Environment对象

```yml
server:
  port: 81

country: china

user:
  name0: xrj
  age: 23

likes:
  - game
  - music
  - yy

baseDir: c:\windows

# 使用${属性名}引用数据
tempDir: ${baseDir}\temp

# 使用引号包裹的字符串，其中的转义字符可以生效
tempDir2: "${baseDir}\temp \t1 \t2 \t3"
```

```java
package com.xrj.controller;

import com.xrj.MyDataSource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.env.Environment;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

// @RestController 相当于 @Controller+@ResponseBody
@RestController
@RequestMapping("/books")
public class BookController {

    // 读取yml文件全部数据
    // 使用自动装配将所有的数据封装到一个对象
    @Autowired
    private Environment environment;


    // @GetMapping等价于@RequestMapping(method = RequestMethod.Get )
    @GetMapping
    public  String getById(){
        System.out.println(environment.getProperty("server.port"));
        System.out.println(environment.getProperty("user.name0"));
        return "springboot is running...";
    }

}
```





**读取指定数据**

自定义对象封装指定数据

1. 使用@ConfigurationProperties注解绑定配置信息到封装类中

2. 封装类需要定义为Spring管理的bean，否则无法进行属性注入

```xml
# 创建类，用于封装以下数据
# 由spring帮我们去加载数据到对象中，一定要告诉spring加载这组信息
# 使用时候从spring中直接获取信息使用

datasource:
  driver: com.mysql.jdbc.Driver
  url: jdbc://mysql://localhost/test
  username: root
  password: Xu990918
```

```java
// 1.定义数据模型，封装yaml文件中对应的数据
// 2.定义为spring管控的bean
@Component
// 3.指定加载的数据
@ConfigurationProperties(prefix = "datasource")
public class MyDataSource {
    private String driver;
    private String url;
    private String username;
    private String password;

    @Override
    public String toString() {
        return "MyDataSource{" +
                "driver='" + driver + '\'' +
                ", url='" + url + '\'' +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }

    public String getDriver() {
        return driver;
    }

    public void setDriver(String driver) {
        this.driver = driver;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
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

```java
// 加载指定数据
@Autowired
private MyDataSource myDataSource;
```







### 四、整合第三方技术

##### 整合JUnit

```java
@SpringBootTest
class Springboot03JunitApplicationTests {

    // 1.注入你要测试的对象
    @Autowired
    private BookDao bookDao;
    // 2.执行要测试的对象对应的方法

    @Test
    void contextLoads() {
        bookDao.save();
    }
}
```





- 测试类如果存在于引导类所在包或子包中无需指定引导类

- 测试类如果不存在于引导类所在的包或子包中需要通过classes属性指定引导类

```java
@SpringBootTest(classes = Springboot03JunitApplication.class)
```





##### 整合MyBatis 

①选择当前模块需要使用的技术集（MyBatis、MySQL）

②设置数据源参数

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?serverTimezone=UTC
    username: root
    password: Xu990918
```

③定义数据层接口与映射配置

```java
@Mapper
public interface UserDao {
    @Select("select * from user where id = #{id}")
    public User getById(Integer id);
}
```

④测试类中注入dao接口，测试功能

```java
@SpringBootTest
class Springboot04MybatisApplicationTests {

    @Autowired
    private UserDao userDao;

    @Test
    void contextLoads() {
        System.out.println(userDao.getById(1));
    }
}
```



##### 整合MyBatis-Plus

①手动添加SpringBoot整合MyBatis-Plus的坐标，可以通过mvnrepository获取

```xml
<dependency>
   <groupId>com.baomidou</groupId>
   <artifactId>mybatis-plus-boot-starter</artifactId>
   <version>3.4.3</version>
</dependency>
```

②定义数据层接口与映射配置，继承**BaseMapper**

```java
@Mapper
public interface UserDao extends BaseMapper<User> {
}
```

③其他同SpringBoot整合MyBatis





##### 整合Druid

①指定数据源类型

```xml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?serverTimezone=UTC
    username: root
    password: Xu990918
    type: com.alibaba.druid.pool.DruidDataSource
```



②导入Druid对应的starter

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.2.6</version>
</dependency>
```

变更Druid的配置方式

```xml
spring:
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/test?serverTimezone=UTC
      username: root
      password: Xu990918
```

③其他同SpringBoot整合MyBatis







# 基于SpringBoot的SSMP整合案例



案例实现方案分析

- 实体类开发————使用Lombok快速制作实体类

- Dao开发————整合MyBatisPlus，制作数据层测试类

- Service开发————基于MyBatisPlus进行增量开发，制作业务层测试类

- Controller开发————基于Restful开发，使用PostMan测试接口功能
-  Controller开发————前后端开发协议制作

- 页面开发————基于VUE+ElementUI制作，前后端联调，页面数据处理，页面消息处理

- 列表、新增、修改、删除、分页、查询

- 项目异常处理

- 按条件查询————页面功能调整、Controller修正功能、Service修正功能





### 实体类开发

Lombok，一个Java类库，提供了一组注解，简化POJO实体类开发

```xml
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
</dependency>
```



常用注解：@Data

```java
@Data
public class Book {
    private Integer id;
    private String type;
    private String name;
    private String description;

}
```

为当前实体类在编译期设置对应的get/set方法，toString方法，hashCode方法，equals方法等





### 数据层开发

①导入MyBatisPlus与Druid对应的starter

②配置数据源与MyBatisPlus对应的基础配置

```yml
server:
  port: 80

spring:
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/test?serverTimezone=UTC
      username: root
      password: Xu990918
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
  #为方便调试可以开启MyBatisPlus的日志
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

③继承BaseMapper并指定泛型

```java
@Mapper
public interface BookDao extends BaseMapper<Book> {
}
```

④制作测试类测试结果

```java
@SpringBootTest
public class BookDaoTestCase {

    @Autowired
    private BookDao bookDao;
    
    @Test
    void testGetById(){
        System.out.println(bookDao.selectById(1));
    }

    @Test
    void testSave(){
        Book book=new Book();
        book.setType("测试数据1");
        book.setName("睿杰爱艳艳");
        book.setDescription("睿杰很爱很爱艳艳");
        bookDao.insert(book);
    }

    @Test
    void testUpdate(){
        Book book=new Book();
        book.setId(7);
        book.setType("测试数据2");
        book.setName("睿杰爱爱艳艳");
        book.setDescription("睿杰很爱很爱非常爱艳艳");
        bookDao.updateById(book);
    }

    @Test
    void testDelete(){
        bookDao.deleteById(8);
    }

    @Test
    void testGetAll(){
        bookDao.selectList(null);   // queryWrapper 为查询条件
    } 
}
```



##### 分页功能

分页操作需要设定分页对象IPage

```java
@Test
void testGetPage(){
    // select * from ??? limit ? , ?
    // 使用分页需要提供拦截器
    IPage page=new Page(1,5);   // 第一页的数据，这一页显示五条
    bookDao.selectPage(page,null);
    System.out.println(page.getCurrent());
    System.out.println(page.getPages());
    System.out.println(page.getRecords());
    System.out.println(page.getSize());
    System.out.println(page.getTotal());
}
```



IPage对象中封装了分页操作中的所有数据

- 数据

- 当前页码值

- 每页数据总量

- 最大页码值

- 数据总量



分页操作是在MyBatisPlus的常规操作基础上增强得到，内部是动态的拼写SQL语句，因此需要增强对应的功能，

使用MyBatisPlus拦截器实现

```java
@Configuration  // 加上后 SSMPApplication引导类所在的包及其子包都会加载拦截器
public class MPConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor interceptor=new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return interceptor;
    }
}
```



##### 条件查询功能

使用QueryWrapper对象封装查询条件，推荐使用LambdaQueryWrapper对象，所有查询操作封装成方法调用

```java
@Test
void testGetBy(){
    // select * from book where name like %Spring%
    QueryWrapper<Book> queryWrapper = new QueryWrapper<>();
    queryWrapper.like("name","Spring"); // 属性名是name  属性值是spring
    bookDao.selectList(queryWrapper);
}

@Test
void testGetBy2(){
    String name="1";
    LambdaQueryWrapper<Book> lambdaQueryWrapper = new LambdaQueryWrapper<>();
    // if(name!=null){
    //     lambdaQueryWrapper.like(Book::getName,name);
    // }
    lambdaQueryWrapper.like(name!=null,Book::getName,name); // 属性名是name  属性值是spring
    bookDao.selectList(lambdaQueryWrapper);
}
```





### 业务层开发

**接口定义：**

```java
public interface BookService {
    Boolean save(Book book);
    Boolean update(Book book);
    Boolean delete(Integer id);
    Book getById(Integer id);
    List<Book> getAll();
    IPage<Book> getPage(int currentPage , int pageSize);
}
```



**实现类：**

```java
package com.xrj.service.impl;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.xrj.dao.BookDao;
import com.xrj.domain.Book;
import com.xrj.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class BookServiceImpl implements BookService {

    @Autowired
    private BookDao bookDao;

    @Override
    public Boolean save(Book book) {
        return bookDao.insert(book)>0;      // >0 表示成功
    }

    @Override
    public Boolean update(Book book) {
        return bookDao.updateById(book)>0;
    }

    @Override
    public IPage<Book> getPage(int currentPage, int pageSize) {
        IPage page=new Page(currentPage,pageSize);
        bookDao.selectPage(page,null);
        return page;
    }

    @Override
    public Boolean delete(Integer id) {
        return bookDao.deleteById(id) > 0;
    }

    @Override
    public Book getById(Integer id) {
        return bookDao.selectById(id);
    }

    @Override
    public List<Book> getAll() {
        return bookDao.selectList(null);
    }
}
```





**测试类：**

```java
package com.xrj.service;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.xrj.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class BookServiceTestCase {

    @Autowired
    private BookService bookService;

    @Test
    void testGetById(){
        System.out.println(bookService.getById(4));
    }

    @Test
    void testSave(){
        Book book=new Book();
        book.setType("测试数据1");
        book.setName("睿杰爱艳艳");
        book.setDescription("睿杰很爱很爱艳艳");
        bookService.save(book);
    }

    @Test
    void testUpdate(){
        Book book=new Book();
        book.setId(9);
        book.setType("测试数据2");
        book.setName("睿杰爱爱艳艳");
        book.setDescription("睿杰很爱很爱非常爱艳艳");
        bookService.update(book);
    }

    @Test
    void testDelete(){
        bookService.delete(9);
    }

    @Test
    void testGetAll(){
        bookService.getAll();
    }

    @Test
    void testGetPage(){
        IPage<Book> page = bookService.getPage(2, 5);
        System.out.println(page.getCurrent());
        System.out.println(page.getPages());
        System.out.println(page.getRecords());
        System.out.println(page.getSize());
        System.out.println(page.getTotal());
    }
}
```





### 业务层开发————快速开发



**快速开发方案**

- 使用MyBatisPlus提供有业务层通用接口（ISerivce<T>）与业务层通用实现类（ServiceImpl<M,T>） 

- 在通用类基础上做功能重载或功能追加

- 注意重载时不要覆盖原始操作，避免原始提供的功能丢失



**接口定义**

```java
public interface IBookService extends IService<Book> {
}
```



```java
public interface IBookService extends IService<Book> {
    
    // 追加的操作与原始操作通过名称区分，功能类似
    Boolean saveBook(Book book);

    Boolean modify(Book book);

    Boolean delete(Integer id);
}
```





**实现类定义**

```java
@Service
public class BookServiceImpl extends ServiceImpl<BookDao, Book> implements IBookService {
}
```



**实现类追加功能**

```java
@Service
public class BookServiceImpl extends ServiceImpl<BookDao, Book> implements IBookService {

    @Autowired
    private BookDao bookDao;

    @Override
    public Boolean modify(Book book) {
        return bookDao.updateById(book)>0;
    }

    @Override
    public Boolean delete(Integer id) {
        return bookDao.deleteById(id)>0;
    }

    @Override
    public Boolean saveBook(Book book) {
        return bookDao.insert(book)>0;
    }
}
```





### 表现层开发

- 基于Restful进行表现层接口开发

- 使用Postman测试表现层接口功能

```java
@RestController
@RequestMapping("/books")
public class BookController {

    @Autowired
    private IBookService bookService;

    @GetMapping
    public List<Book> getAll(){
        return bookService.list();
    }

    @PostMapping
    public Boolean save(@RequestBody Book book){
        return bookService.save(book);
    }

    @PutMapping
    public Boolean update(@RequestBody Book book){
        return bookService.modify(book);
    }

    @DeleteMapping("{id}")
    public Boolean delete(@PathVariable Integer id){
        return bookService.delete(id);
    }

    // http://localhost/books/2
    @GetMapping("{id}")
    public Book getById(@PathVariable Integer id){
        return bookService.getById(id);
    }

    @GetMapping("{currentPage}/{pageSize}")
    public IPage<Book> getPage(@PathVariable int currentPage,@PathVariable int pageSize){
        return bookService.getPage(currentPage,pageSize);
    }

}
```



1. 基于Restful制作表现层接口

- 新增：POST

- 删除：DELETE

- 修改：PUT

- 查询：GET

2. 接收参数

- 实体数据：@RequestBody

- 路径变量：@PathVariable





### 表现层消息一致性处理



- 设计统一的返回值结果类型便于前端开发读取数据

- 返回值结果类型可以根据需求自行设定，没有固定格式

- 返回值结果模型类用于后端与前端进行数据格式统一，也称为前后端数据协议

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220224184905439.png" alt="image-20220224184905439" style="zoom:70%;" />





<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220224184918343.png" alt="image-20220224184918343" style="zoom:70%;" />





- 设计表现层返回结果的模型类，用于后端与前端进行数据格式统一，也称为**前后端数据协议**

```java
@Data
public class R {
    private Boolean flag;
    private Object data;

    public R(){}

    public R(Boolean flag){
        this.flag=flag;
    }

    public R(Boolean flag,Object data){
        this.flag=flag;
        this.data=data;
    }
}
```



- 表现层接口统一返回值类型结果

```java
@RestController
@RequestMapping("/books")
public class BookController {

    @Autowired
    private IBookService bookService;

    @GetMapping
    public R getAll(){
        return new R(true,bookService.list());
    }

    @PostMapping
    public R save(@RequestBody Book book){
        // R r = new R();
        // Boolean flag=bookService.save(book);
        // r.setFlag(flag);
        return new R(bookService.save(book));
    }

    @PutMapping
    public R update(@RequestBody Book book){
        return new R(bookService.modify(book));
    }

    @DeleteMapping("{id}")
    public R delete(@PathVariable Integer id){
        return new R(bookService.delete(id));
    }

    // http://localhost/books/2
    @GetMapping("{id}")
    public R getById(@PathVariable Integer id){
        return new R(true,bookService.getById(id));
    }

    @GetMapping("{currentPage}/{pageSize}")
    public R getPage(@PathVariable int currentPage,@PathVariable int pageSize){
        return new R(true,bookService.getPage(currentPage,pageSize));
    }

}
```







### 前后端协议联调

- 前后端分离结构设计中页面归属前端服务器

- 单体工程中页面放置在resources目录下的static目录中（建议执行clean）



**前端发送异步请求，调用后端接口**

```java
getAll() {
    //发送异步请求
    axios.get("/books").then((res)=>{
        console.log(res.data);
    });
},
```



**列表页**

```java
getAll() {
    //发送异步请求
    axios.get("/books").then((res)=>{
        // console.log(res.data);
        this.dataList = res.data.data;  // 第二个data使我们自定义的（flag，data）
    });
},
```



**弹出添加窗口**

```java
//弹出添加窗口
handleCreate() {
    this.dialogFormVisible = true;
    this.resetForm();
},
```



**清除数据**

```java
//重置表单
resetForm() {
    this.formData = {};
},
```



**添加**

```java
//添加
handleAdd () {
    axios.post("/books",this.formData).then((res)=>{
        //判断当前操作是否成功
        if(res.data.flag){
            //1.关闭弹层
            this.dialogFormVisible = false;
            this.$message.success(res.data.msg);
        }else{
            this.$message.error(res.data.msg);
        }
    }).finally(()=>{
        //2.重新加载数据
        this.getAll();
    });
},
```



**取消添加**

```java
//取消
cancel(){
    this.dialogFormVisible = false;
    this.dialogFormVisible4Edit = false;
    this.$message.info("当前操作取消");
},
```



**删除**

```java
// 删除
handleDelete(row) {
    // console.log(row);
    this.$confirm("此操作永久删除当前信息，是否继续？","提示",{type:"info"}).then(()=>{
        axios.delete("/books/"+row.id).then((res)=>{
            if(res.data.flag){
                this.$message.success("删除成功");
            }else{
                this.$message.error("数据同步失败，自动刷新");
            }
        }).finally(()=>{
            //2.重新加载数据
            this.getAll();
        });
    }).catch(()=>{
        this.$message.info("取消操作");
    });
},
```



**弹出修改窗口**

```
//弹出编辑窗口
handleUpdate(row) {
    axios.get("/books/"+row.id).then((res)=>{
        if(res.data.flag && res.data.data != null ){
            this.dialogFormVisible4Edit = true;
            this.formData = res.data.data;
        }else{
            this.$message.error("数据同步失败，自动刷新");
        }
    }).finally(()=>{
        //2.重新加载数据
        this.getAll();
    });
},

//修改
handleEdit() {
    axios.put("/books",this.formData).then((res)=>{
        //判断当前操作是否成功
        if(res.data.flag){
            //1.关闭弹层
            this.dialogFormVisible4Edit = false;
            this.$message.success("修改成功");
        }else{
            this.$message.error("修改失败");
        }
    }).finally(()=>{
        //2.重新加载数据
        this.getAll();
    });
},
```



**对异常进行统一处理，出现异常后，返回指定信息**

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {
    // 拦截所有的异常信息
    @ExceptionHandler(Exception.class)
    public R doException(Exception ex){
        // 记录日志
        // 通知运维
        // 通知开发
        ex.printStackTrace();
        return new R("服务器故障，请稍后再试!");
    }

}
```



**修改表现层返回结果的模型类，封装出现异常后对应的信息**

- flag：false

- Data: null

- 消息(msg): 要显示信息

```java
@Data
public class R {
    private Boolean flag;
    private Object data;
    private String msg;

    public R(){}

    public R(Boolean flag){
        this.flag=flag;
    }

    public R(Boolean flag,Object data){
        this.flag=flag;
        this.data=data;
    }

    public R(Boolean flag, String msg) {
        this.flag = flag;
        this.msg = msg;
    }

    public R(String msg){
        this.flag=false;
        this.msg=msg;
    }
}
```



**可以在表现层Controller中进行消息统一处理**

```java
@PostMapping
public R save(@RequestBody Book book){
    // R r = new R();
    // Boolean flag=bookService.save(book);
    // r.setFlag(flag);
    Boolean flag=bookService.save(book);
    return new R(flag,flag ? "添加成功^_^" : "添加失败-_-!");
}
```



**页面使用el分页组件添加分页功能**

```html
<!--分页组件-->
<div class="pagination-container">

    <el-pagination
            class="pagiantion"

            @current-change="handleCurrentChange"

            :current-page="pagination.currentPage"

            :page-size="pagination.pageSize"

            layout="total, prev, pager, next, jumper"

            :total="pagination.total">

    </el-pagination>

</div>
```





**定义分页组件需要使用的数据并将数据绑定到分页组件**

```
pagination: {//分页相关模型数据
    currentPage: 1,//当前页码
    pageSize:5,//每页显示的记录数
    total:0,//总记录数
}
```



**分页查询**

```java
@GetMapping("{currentPage}/{pageSize}")
public R getPage(@PathVariable int currentPage,@PathVariable int pageSize){
    IPage<Book> page = bookService.getPage(currentPage, pageSize);
    // 如果当前页码值大于了总页码值，那么重新执行查询操作，使用最大页码值作为当前页码值
    if(currentPage > page.getPages()){
        page=bookService.getPage((int)page.getPages(),pageSize);
    }
    return new R(true,page);
}
```



**加载分页数据**

```
//分页查询
getAll() {
    //发送异步请求
    axios.get("/books/"+this.pagination.currentPage+"/"+this.pagination.pageSize+param).then((res)=>{
        this.pagination.pageSize = res.data.data.size;
        this.pagination.currentPage = res.data.data.current;
        this.pagination.total = res.data.data.total;

        this.dataList = res.data.data.records;
    });
},
```



**分页页码值切换**

```java
//切换页码
handleCurrentChange(currentPage) {
    //修改页码值为当前选中的页码值
    this.pagination.currentPage = currentPage;
    //执行查询
    this.getAll();
},
```



**对查询结果进行校验，如果当前页码值大于最大页码值，使用最大页码值作为当前页码值重新查询**

```java
@GetMapping("{currentPage}/{pageSize}")
public R getPage(@PathVariable int currentPage,@PathVariable int pageSize,Book book){
    // System.out.println(book);

    IPage<Book> page = bookService.getPage(currentPage, pageSize,book);
    // 如果当前页码值大于了总页码值，那么重新执行查询操作，使用最大页码值作为当前页码值
    if(currentPage > page.getPages()){
        page=bookService.getPage((int)page.getPages(),pageSize,book);
    }
    return new R(true,page);
}
```





##### 条件查询功能



**查询条件数据封装**

```java
pagination: {//分页相关模型数据
    currentPage: 1,//当前页码
    pageSize:5,//每页显示的记录数
    total:0,//总记录数
    type: "",
    name: "",
    description: ""
}
```



**页面数据模型绑定**

```java
<div class="filter-container">
    <el-input placeholder="图书类别" v-model="pagination.type" style="width: 200px;" class="filter-item"></el-input>
    <el-input placeholder="图书名称" v-model="pagination.name" style="width: 200px;" class="filter-item"></el-input>
    <el-input placeholder="图书描述" v-model="pagination.description" style="width: 200px;" class="filter-item"></el-input>
    <el-button @click="getAll()" class="dalfBut">查询</el-button>
    <el-button type="primary" class="butT" @click="handleCreate()">新建</el-button>
</div>
```



**组织数据成为get请求发送的数据**

```java
getAll() {
    //组织参数，拼接url请求地址
    // console.log(this.pagination.type);
    param = "?type="+this.pagination.type;
    param +="&name="+this.pagination.name;
    param +="&description="+this.pagination.description;
    // console.log(param);
},
```



**Controller接收参数**

```java
@GetMapping("{currentPage}/{pageSize}")
public R getPage(@PathVariable int currentPage,@PathVariable int pageSize,Book book){
    // System.out.println(book);

    IPage<Book> page = bookService.getPage(currentPage, pageSize,book);
    // 如果当前页码值大于了总页码值，那么重新执行查询操作，使用最大页码值作为当前页码值
    if(currentPage > page.getPages()){
        page=bookService.getPage((int)page.getPages(),pageSize,book);
    }
    return new R(true,page);
}
```



**业务层接口功能开发**

```java
public interface BookService {
    Boolean save(Book book);
    Boolean update(Book book);
    Boolean delete(Integer id);
    Book getById(Integer id);
    List<Book> getAll();
    IPage<Book> getPage(int currentPage , int pageSize);
}
```



```java
@Override
public IPage<Book> getPage(int currentPage, int pageSize, Book book) {
    LambdaQueryWrapper<Book> lambdaQueryWrapper = new LambdaQueryWrapper<Book>();
    lambdaQueryWrapper.like(Strings.isNotEmpty(book.getType()),Book::getType,book.getType());
    lambdaQueryWrapper.like(Strings.isNotEmpty(book.getName()),Book::getName,book.getName());
    lambdaQueryWrapper.like(Strings.isNotEmpty(book.getDescription()),Book::getDescription,book.getDescription());

    IPage page=new Page(currentPage,pageSize);
    bookDao.selectPage(page,lambdaQueryWrapper);
    return page;
}
```



**Controller调用业务层分页条件查询接口**

```java
@GetMapping("{currentPage}/{pageSize}")
public R getPage(@PathVariable int currentPage,@PathVariable int pageSize,Book book){
    // System.out.println(book);

    IPage<Book> page = bookService.getPage(currentPage, pageSize,book);
    // 如果当前页码值大于了总页码值，那么重新执行查询操作，使用最大页码值作为当前页码值
    if(currentPage > page.getPages()){
        page=bookService.getPage((int)page.getPages(),pageSize,book);
    }
    return new R(true,page);
}
```



**页面回显数据**

```java
getAll() {
    //组织参数，拼接url请求地址
    // console.log(this.pagination.type);
    param = "?type="+this.pagination.type;
    param +="&name="+this.pagination.name;
    param +="&description="+this.pagination.description;
    // console.log(param);

    //发送异步请求
    axios.get("/books/"+this.pagination.currentPage+"/"+this.pagination.pageSize+param).then((res)=>{
        this.pagination.pageSize = res.data.data.size;
        this.pagination.currentPage = res.data.data.current;
        this.pagination.total = res.data.data.total;

        this.dataList = res.data.data.records;
    });
},
```









# Spring Boot运维实用篇



### 打包与运行



**SpringBoot项目快速启动（Windows版）**

①：对SpringBoot项目打包（执行Maven构建指令package）

```java
mvn package
```

②：运行项目（执行启动指令）

```cmd
java –jar springboot.jar
```



注意：jar支持命令行启动需要依赖maven插件支持，请确认打包时是否具有SpringBoot对应的maven插件

```xml
<plugins>
   <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
   </plugin>
</plugins>
```





### 配置高级

- 临时属性设置

- 配置文件分类

- 自定义配置文件





##### 临时属性设置

- 带属性数启动SpringBoot

```
java –jar springboot.jar –-server.port=80
```

- 携带多个属性启动SpringBoot，属性间使用空格分隔



##### 临时属性设置（开发环境）

- 带属性启动SpringBoot程序，为程序添加运行属性

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220301121401838.png" alt="image-20220301121401838" style="zoom:67%;" />





**配置文件分类**

1.SpringBoot中4级配置文件

​	1级： file（和jar文件在同一目录下） ：config/application.yml   		**【最高】** 

​	2级： file（和jar文件在同一目录下）：application.yml

​	3级：classpath：config/application.yml

​	4级：classpath：application.yml 						   							 	**【最低】**



2.作用： 

- 1级与2级留做系统打包后设置通用属性，1级常用于运维经理进行线上整体项目部署方案调控

- 3级与4级用于系统开发阶段设置通用属性，3级常用于项目经理进行整体项目属性调控



**配置文件分类**

1. 配置文件可以修改名称，通过启动参数设定

2. 配置文件可以修改路径，通过启动参数设定

3. 微服务开发中配置文件通过配置中心进行设置



- 通过启动参数加载配置文件（无需书写配置文件扩展名)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220301122016794.png" alt="image-20220301122016794" style="zoom:67%;" />



- 通过启动参数加载指定文件路径下的配置文件

- 通过启动参数加载指定文件路径下的配置文件时可以加载多个配置







### 多环境开发

##### 多环境开发（YAML版）

1. 多环境开发需要设置若干种常用环境，例如开发、生产、测试环境

2. yaml格式中设置多环境使用`---`区分环境设置边界

3. 每种环境的区别在于加载的配置属性不同

4. 启用某种环境时需要指定启动时使用该环境

```yml
# 应用环境
# 公共配置	（指定启动环境）
spring:
  profiles:
    active: dev

---
# 生产环境
spring:
  profiles: pro
server:
  port: 80

---
# 开发环境
spring:
  profiles: dev
server:
  port: 81

---
# 测试环境
spring:
  profiles: test
server:
  port: 82
```



##### 多环境开发文件版（YAML版）



- 主配置文件中设置公共配置（全局）

- 环境分类配置文件中常用于设置冲突属性（局部）



1.主启动配置文件application.yml

```yml
# 应用环境
spring:
  profiles:
    active: test
```



2.环境分类配置文件application**-pro**.yml

```yml
server:
  port: 8080
```



3.环境分类配置文件application**-dev**.yml

```yml
server:
  port: 8081
```



4.环境分类配置文件application**-test**.yml

```yml
server:
  port: 8082
```





##### 多环境开发文件版（Properties版）



properties文件多环境配置仅支持多文件格式



1.主启动配置文件application.properties

```properties
spring.profiles.active=test
```



2.环境分类配置文件application**-pro**.properties

```properties
server.port=9082
```



3.环境分类配置文件application**-dev**.properties

```properties
server.port=9081
```



4.环境分类配置文件application**-test**.properties

```properties
server.port=9083
```





##### 多环境开发独立配置文件书写技巧（二）



- 根据功能对配置文件中的信息进行拆分，并制作成独立的配置文件，命名规则如下

  1. application-devDB.yml

  2. application-devRedis.yml

  3. application-devMVC.yml

- 使用include属性在激活指定环境的情况下，同时对多个环境进行加载使其生效，多个环境间使用逗号分隔

```yml
spring:
  profiles:
    active: dev
    include: devMVC,devDB
```



注意：当主环境dev与其他环境有相同属性时，主环境属性生效；其他环境中有相同属性时，最后加载的环境属性生效



- 从Spring2.4版开始使用group属性替代include属性，降低了配置书写量

- 使用group属性定义多种主环境与子环境的包含关系

```yml
spring:
  profiles:
    active: dev
    group:
      "dev": devMVC,devDB
      "pro": proMVC,proDB
```





##### 多环境开发控制

Maven与SpringBoot多环境兼容

1.Maven中设置多环境属性

```xml
<!--设置多环境-->
<profiles>
   <profile>
      <id>env_dev</id>
      <properties>
         <profile.active>dev</profile.active>
      </properties>
      <activation>
         <activeByDefault>true</activeByDefault>
      </activation>
   </profile>
   <profile>
      <id>env_pro</id>
      <properties>
         <profile.active>pro</profile.active>
      </properties>
   </profile>
</profiles>
```



2.SpringBoot中引用Maven属性

```yml
spring:
  profiles:
    active: @profile.active@
    group:
      "dev": devMVC,devDB
      "pro": proMVC,devDB
```



3.执行Maven打包指令，并在生成的boot打包文件.jar文件中查看对应信息



- 当Maven与SpringBoot同时对多环境进行控制时，以Mavn为主，SpringBoot使用`@..@`占位符读取Maven对应的配置属性值

- 基于SpringBoot读取Maven配置属性的前提下，如果在Idea下测试工程时pom.xml每次更新需要手动compile方可生效







### 日志

##### 日志基础操作

- 日志（log）作用

  - 编程期调试代码

  - 运营期记录信息

    - 记录日常运营重要信息（峰值流量、平均响应时长……） 

    - 记录应用报错信息（错误堆栈）

    - 记录运维过程数据（扩容、宕机、报警……）



**代码中使用日志工具记录日志**

1.添加日志记录操作

```java
@RestController
@RequestMapping("/books")
public class BookController extends BaseClass {

    // 创建记录日志的对象
    private static final Logger log= LoggerFactory.getLogger(BookController.class);

    // @GetMapping等价于@RequestMapping(method = RequestMethod.Get )
    @GetMapping
    public  String getById(){
        System.out.println("springboot is running...2");

        log.debug("debug...");  // 级别太低，默认false未开启
        log.info("info...");
        log.warn("warn...");
        log.error("error...");

        return "springboot is running...2";
    }
}
```



- 日志级别

  - TRACE：运行堆栈信息，使用率低

  - DEBUG：程序员调试代码使用

  - INFO：记录运维过程数据

  - WARN：记录运维过程报警数据

  - ERROR：记录错误堆栈信息

  - FATAL：灾难信息，合并计入ERROR



2.设置日志输出级别

```yml
# 开启debug模式，输出调试信息，常用于检查系统运行状况
#debug: true

# 设置日志级别，root表示根节点，即整体应用日志级别
logging:
  level:
    root: info
```



3.设置日志组，控制指定包对应的日志输出级别，也可以直接控制指定包对应的日志输出级别

```yml
logging:
  # 设置分组
  group:
    ebank: com.xrj.controller,com.xrj.service,com.xrj.dao
    iservice: com.xrj
  level:
    root: info
    # 设置某个包的日志级别
    # com.xrj.controller: debug
    # 设置分组，对某个组设置日志级别
    ebank: warn
```





##### 使用lombok

使用lombok提供的注解@Slf4j简化开发，减少日志对象的声明操作

```java
@Slf4j
@RestController
@RequestMapping("/books")
public class BookController {

    @GetMapping
    public  String getById(){
        System.out.println("springboot is running...2");

        log.debug("debug...");  // 级别太低，默认false未开启
        log.info("info...");
        log.warn("warn...");
        log.error("error...");
        return "springboot is running...2";
    }
```



##### 日志输出格式控制

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220302145246095.png" alt="image-20220302145246095" style="zoom:80%;" />



- PID：进程ID，用于表明当前操作所处的进程，当多服务同时记录日志时，该值可用于协助程序员调试程序

- 所属类/接口名：当前显示信息为SpringBoot重写后的信息，名称过长时，简化包名书写为首字母，甚至直接删除





**设置日志输出格式**

```yml
# 设置日志模板格式
pattern:
# console: "%d - %m%n"  # 时间 消息 换行
# console: "%d %5p %n"   # %p代表日志级别   总宽度5位
# console: "%d %clr(%5p) %n"   # %p代表日志级别   总宽度5位   带上颜色
# console: "%d %clr(%5p) --- [%16t] %n"   # %t位线程名 thread
# console: "%d %clr(%5p) --- [%16t] %c %n"  # %c位类名
  console: "%d %clr(%5p) --- [%16t] %clr(%-40.40c){cyan} : %m %n"  # %c位类名  %m位消息 -40左对齐  .40内容截取
```





##### 日志文件

**设置日志文件**

```yml
logging:
  file:
    name: server.log
```



**日志文件详细配置**

```yml
logging:
  file:
    name: server.log
  logback:
  	rollingpolicy:
   	 	max-file-size: 4KB    # 最大大小为4kb
   	 	file-name-pattern: server.%d{yyyy-MM-dd}.%i.log   # 文件名格式
```





<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20220302145551227.png" alt="image-20220302145551227" style="zoom:80%;" />

