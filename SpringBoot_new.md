# 一、SpringBoot基础入门

## 1、Hello world程序

> 浏览发送/hello请求，响应 “Hello，Spring Boot 2”

**1、引入依赖**

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.3.4.RELEASE</version>
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```



**2、创建主程序**

```java
/**
 * 主程序类
 * @SpringBootApplication, 这是一个springboot应用
 */
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }
}
```



**3、编写业务**

```java
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello Spring Boot2";
    }
}
```



**4、测试**

- 运行`MainApplication`类
- 浏览器输入`http://localhost:8888/hello`，将会输出`Hello, Spring Boot 2!`。





**设置配置**

maven工程的resource文件夹中创建`application.properties`文件。

```properties
# 设置端口号
server.port=8888
```

[更多配置信息](https://docs.spring.io/spring-boot/docs/2.3.7.RELEASE/reference/html/appendix-application-properties.html#common-application-properties-server)



**打包部署**

在pom.xml添加

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

> 1、以前在springmvc项目中，我们的项目必须打包成war包，然后放在Tomcat容器中运行才可以访问。但是在SpringBoot中，它内置了tomcat，因此他直接打包成jar包，就包含了Tomcat，然后直接运行jar即可。
>
> 2、在IDEA的Maven插件上点击运行 clean 、package，把helloworld工程项目的打包成jar包，打包好的jar包被生成在helloworld工程项目的target文件夹内。用cmd运行`java -jar boot-01-helloworld-1.0-SNAPSHOT.jar`，就可以运行helloworld工程项目。将jar包直接在目标服务器执行即可。







## 2、SpringBoot的依赖管理

**父项目做依赖管理**

在我们SpringBoot项目的pom.xml文件中，都会导入如下配置，即导入他的一个父工程

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>
```

他的父工程又会依赖以下父工程，在`spring-boot-dependencies`中，定义了各种依赖的版本，用一个`<dependencyManagement>`标签管理，该标签只声明依赖的版本，但并不会实际引入这些依赖，在我们自己的项目中，只要导入依赖，不需要指定版本，会直接使用SpringBoot为我们提供的版本。

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>
```





**开发导入starter场景启动器**

1. 见到很多 `spring-boot-starter-*` ： *表示某种场景，例如我们是web开发，导入的就是`spring-boot-starter-web`

   ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308171817895.png)

2. 只要引入starter，这个场景的所有常规需要的依赖我们都自动引入

3. [更多SpringBoot所有支持的场景](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)

4. 见到的 ` *-spring-boot-starter`： 第三方为我们提供的简化开发的场景启动器。

5. 所有场景启动器最底层的依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter</artifactId>
	<version>2.3.4.RELEASE</version>
	<scope>compile</scope>
</dependency>
```





**无需关注版本号，自动版本仲裁**

1. 引入依赖默认都可以不写版本
2. 引入非版本仲裁的jar，要写版本号。



**可以修改默认版本号**

1. 查看`spring-boot-dependencies`里面规定当前依赖的版本 用的 key。

2. 在当前项目里面重写配置，如下面的代码。

   ```xml
   <properties>
   	<mysql.version>5.1.43</mysql.version>
   </properties>
   ```

   



## 3、SpringBoot的自动配置

**自动配好Tomcat**

> 在我们导入`spring-boot-starter-web`这个依赖后，会将他的所有依赖都导入，其中就有`spring-boot-starter-tomcat`，即Tomcat的依赖，因此`SpringBoot`项目可以直接打包成jar包运行，因为他嵌入了Tomcat。





**自动配好SpringMVC**.

> 在我们导入`spring-boot-starter-web`这个依赖后，也会导入`spring-webmvc`，同时会将其组件都放入ioc容器中

- 引入SpringMVC全套组件
- 自动配好SpringMVC常用组件（功能）





**自动配好Web常见功能**

> 在springmvc项目中，我们首先需要在web.xml中配置`DispatcherServlet`，然后配置`CharacterEncodingFilter`、`HiddenHttpMethodFilter`等。
>
> 但在SpringBoot中，它已经帮我们配置好了所有web开发的常见场景，例如`DispatcherServlet`、`multipartResolver`等等
>
> 在我们的主程序中`SpringApplication.run(MainApplication.class, args)`的返回值就是ioc容器，我们以前需要手动配置ioc容器，现在我们用到什么，SpringBoot就会帮我们自动配置

```java
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        // 1.返回ioc容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        // 2、查看容器里面的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }
    }
}
```





**默认的包结构**

- 主程序**所在包及其下面的所有子包**里面的组件都会被默认扫描进来，然后放入ioc容器中

- 无需以前的包扫描配置`component-scan`

- 想要改变扫描路径

  - `@SpringBootApplication(scanBasePackages="com.xrj")`

  - `@ComponentScan` 指定扫描路径

    ```java
    @SpringBootApplication
    等同于
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan("com.xrj")
    ```





**各种配置拥有默认值**

> 默认配置最终都是映射到某个类上，如：`MultipartProperties`。配置文件的值最终会绑定每个类上，这个类会在容器中创建对象，我们如果在`application.properties`文件中修改其默认值，那么创建的对象的值就会被修改





**按需加载所有自动配置项**

> SpringBoot中有非常多的starter，我们引入了哪些场景这个场景的自动配置才会开启，SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面，即我们使用了，他就会自动为我们配置，不使用就不配置。









## 4、底层注解

### 4.1 @Configuration

> `@Configuration`可以将某个类标识为配置类，即全注解开发，不需要写配置文件来配置ioc容器
>
> - 配置 类组件之间**无依赖关系**用Lite模式加速容器启动过程，减少判断
> - 配置 类组件之间**有依赖关系**，方法会被调用得到之前单实例组件，用Full模式（默认）

```java
/**
 * 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
 * 2、配置类本身也是组件
 * 3、proxyBeanMethods：代理bean的方法
 *      Full(proxyBeanMethods = true)（保证每个@Bean方法被调用多少次返回的组件都是单实例的）（默认）
 *      Lite(proxyBeanMethods = false)（每个@Bean方法被调用多少次返回的组件都是新创建的）
 */
@Configuration(proxyBeanMethods = false)
public class MyConfig {

    // 给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    @Bean
    public User user01() {
        return new User("xrj", 20);
    }

    @Bean
    public Pet pet01(){
        return new Pet();
    }
}
```



**测试**

> 默认情况下，Spring使用了CGLIB来创建@Bean方法返回的对象的代理，以便在容器内部进行一些额外的处理。但是，如果将proxyBeanMethods属性设置为false，就会禁用这种代理机制。当proxyBeanMethods属性设置为false时，如果在@Configuration类中的某个方法上使用@Bean注解，并且该方法直接被调用，那么该方法的返回值将不会经过代理，直接返回原始的对象。这意味着，如果在同一个容器中多次调用该方法，每次调用都会返回一个新的实例，而不是使用容器中已经存在的实例。为了解决这个问题，可以将proxyBeanMethods属性设置为true，以启用代理机制，确保在容器中只有一个实例，并且每次调用该方法都会返回同一个实例。

```java
@SpringBootApplication(scanBasePackages = "com.xrj")
public class MainApplication {
    public static void main(String[] args) {
        // 1.返回ioc容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        // 2、查看容器里面的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }

        // 通过ioc容器获取的对象是单例的
        User user1 = run.getBean("user01", User.class);
        User user2 = run.getBean("user01", User.class);
        System.out.println("组件: " + (user1 == user2));  // true

        MyConfig myConfig = run.getBean(MyConfig.class);
        // proxyBeanMethods = true, com.xrj.boot.config.MyConfig$$EnhancerBySpringCGLIB$$4c32adb@77e80a5e
        // proxyBeanMethods = false, com.xrj.boot.config.MyConfig@3af9aa66
        System.out.println(myConfig);

        /*
            如果@Configuration(proxyBeanMethods = true)代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有
            如果有，就从容器中去取，保持单例, 而且这个MyConfig对象在ioc容器中也存在，他是cglib代理的
            如果@Configuration(proxyBeanMethods = false)，那么每次通过该类中的方法获取对象时，都会重新创建一个，而不会去ioc容器查找是否存在
         */
        User user01 = myConfig.user01();
        User user02 = myConfig.user01();
        // proxyBeanMethods = true，这里就是同一个对象。如果proxyBeanMethods = false，这里就不是同一个对象
        System.out.println(user01 == user02);
    }
}
```



**使用@Configuration+@Bean添加组件和使用@Component添加组件的区别**

在Spring Boot应用程序初始化时，会自动扫描并加载带有`@Configuration`注解的配置类。这些配置类用于定义和初始化应用程序的各种组件，包括服务、存储库、控制器等。

另一方面，带有`@Component`注解的普通组件也会在应用程序初始化时被自动扫描和加载。这些普通组件可以是服务、存储库、控制器或其他类型的组件。

无论是`@Configuration`还是`@Component`注解，它们都会被Spring扫描并将其标记为bean，使其成为应用程序上下文中的可用组件。

需要注意的是，`@Configuration`注解的类通常用于定义配置类，而`@Component`注解可以用于各种组件的通用标记。所以，`@Configuration`注解更加明确地表明该类是用于配置的，而`@Component`注解则更加通用，可以用于任何类型的组件。



**@Configuration 中所有带 @Bean 注解的方法都会被动态代理（cglib），因此调用该方法返回的都是同一个实例。因为proxyBeanMethods默认为true**

**而 @Conponent 修饰的类不会被代理，每实例化一次就会创建一个新的对象。**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";

    boolean proxyBeanMethods() default true;
}
```







### 4.2 @Import

> @Bean、@Component、@Controller、@Service、@Repository，它们是Spring的基本标签，在Spring Boot中并未改变它们原来的功能。
>
> @Import注解可以向ioc容器中导入对应的组件类型，该注解写在类上即可，例如`@Import({User.class, DBHelper.class})`就给容器中**自动创建出这两个类型的组件**，默认组件的名字就是全类名

```java
@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false)
public class MyConfig {

    // 给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    @Bean
    public User user01() {
        return new User("xrj", 20);
    }

    @Bean
    public Pet pet01(){
        return new Pet();
    }
}
```



**测试**

```java
@SpringBootApplication(scanBasePackages = "com.xrj")
public class MainApplication {
    public static void main(String[] args) {
        // 返回ioc容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        // 获取所有User类型的组件的名字
        String[] beanNamesForType = run.getBeanNamesForType(User.class);

        /*
        	com.xrj.boot.bean.User 通过import导入的
			user01	通过@Bean导入的
        */
        for (String s : beanNamesForType) {
            System.out.println(s);
        }

        DBHelper bean1 = run.getBean(DBHelper.class);
        // ch.qos.logback.classic.db.DBHelper@1d269ed7
        System.out.println(bean1);
    }
}
```







### 4.3 @Conditional

> `@Conditional`：条件装配，满足Conditional指定的条件，则进行组件注入。他有许多子类注解，例如`@ConditionalOnBean`表示有这个Bean对象才进行装配。如果该注解用在类上，表示满足这个条件，那么这个类中所有对象才会装配。如果用在方法上，那么满足条件该方法的对象才会装配。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308191634326.png)

```java
@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = true)
public class MyConfig {

    @ConditionalOnBean(value = Pet.class) // 有Pet类型的组件时，才会装配User组件
    @Bean
    public User user01() {
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(pet01());
        return zhangsan;
    }

   // @Bean(name = "pet")
    public Pet pet01(){
        return new Pet();
    }
}
```



**测试**

```java
@SpringBootApplication(scanBasePackages = "com.xrj")
public class MainApplication {
    public static void main(String[] args) {
        // 1.返回ioc容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        boolean pet = run.containsBean("pet");
        System.out.println("容器中pet组件：" + pet); //false

        boolean user01 = run.containsBean("user01");
        System.out.println("容器中user01组件：" + user01); //false
    }
}
```









### 4.4 @ImportResource

> 如果项目中有spring的xml文件，里面是使用配置文件方式配置的组件，我们如果不加载该配置文件，其中的组件是不会被加入到ioc容器中的，如果我们希望将配置文件中的组件通过注解方式也加入到ioc容器中，可以使用`@ImportResource`注解，只要将该注解写在任意一个组件上即可，就会被加载到ioc容器中



**bean.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="haha" class="com.xrj.boot.bean.User"/>

    <bean id="hehe" class="com.xrj.boot.bean.Pet"/>

</beans>
```

**配置类**

```java
@ImportResource("classpath:bean.xml")
@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = true)
public class MyConfig {

    // 给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    @ConditionalOnBean(value = Pet.class) // 有Pet类型的组件时，才会装配User组件
    @Bean
    public User user01() {
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(pet01());
        return zhangsan;
    }

   // @Bean(name = "pet")
    public Pet pet01(){
        return new Pet();
    }
}
```

**测试**

```java
@SpringBootApplication(scanBasePackages = "com.xrj")
public class MainApplication {
    public static void main(String[] args) {
        // 返回ioc容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        boolean u1 = run.containsBean("haha");
        System.out.println("容器中haha组件：" + u1);

        boolean p1 = run.containsBean("hehe");
        System.out.println("容器中p1组件：" + p1); 
    }
}
```







### 4.5 @ConfigurationProperties

> 我们一般会将频繁改变的属性写到配置文件中，如果想将他封装到JavaBean中，传统方法需要读取该文件，然后根据不同字段名再做判断，非常麻烦。而SpringBoot为我们提供了绑定配置，可以直接将配置文件中的属性绑定到组件对象中。



**1、@ConfigurationProperties + @Component**

假设有配置文件application.properties

```properties
mycar.brand=BYD
mycar.price=100000
```

只有在ioc容器中的组件，才会拥有SpringBoot提供的功能。通过这两个注解标识后，获取Car对象时，里边的属性都会被赋值

```java
@Component
@ConfigurationProperties("mycar")
public class Car {
    private String brand;
    private Integer price;

    public Car() {
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }
}
```



**2、@EnableConfigurationProperties + @ConfigurationProperties**

> 在组件的类上不使用`@Component`标记，仅使用`@ConfigurationProperties(prefix = "mycar")`

```java
// @Component
@ConfigurationProperties(prefix = "mycar")
public class Car {
    private String brand;
    private Integer price;

    public Car() {
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }
}
```



> 在配置类上必须使用`@EnableConfigurationProperties(Car.class)`标识，该注解的作用：①开启Car配置绑定功能 ②把Car这个组件自动注册到ioc容器中

```java
@ImportResource("classpath:bean.xml")
@Import({User.class, DBHelper.class})
@EnableConfigurationProperties(Car.class)
// 1.开启Car配置绑定功能
// 2.把Car这个组件自动注册到ioc容器中
public class MyConfig {

    // 给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    @ConditionalOnBean(value = Pet.class) // 有Pet类型的组件时，才会装配User组件
    @Bean
    public User user01() {
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(pet01());
        return zhangsan;
    }

   // @Bean(name = "pet")
    public Pet pet01(){
        return new Pet();
    }
}
```



> 在程序入口处需要使用`@Import(MyConfig.class)`

```java
@Import(MyConfig.class)
@SpringBootApplication(scanBasePackages = "com.xrj")
public class MainApplication {
    public static void main(String[] args) {
        // 返回ioc容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
    }
}
```





### 4.6 编程式注册bean

> 在容器加载完毕后，我们也可以将bean对象放入ioc容器中，如果遇到bean的id重复了，那么就会替换
>
> 这里返回的ioc容器类型必须是`AnnotationConfigApplicationContext`

```java
public class App5 {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
        //上下文容器对象已经初始化完毕后，手工加载bean
        ctx.register(Mouse.class);
    }
}
```

 



### 4.7 ImportSelector

> 1、如果一个类上通过`@import`注解导入了一个类，并且这个类实现了`ImportSelector`接口，那么这个类中返回的组件的全类名会被加载到ioc容器
>
> 2、`AnnotationMetadata`意为注解元数据，元数据就是描述数据的数据
>
> 3、例如，test类通过`@import`注解导入了`MyImportSelector`类，而`MyImportSelector`类实现了`ImportSelector`接口，那么参数中的元数据`metadata`描述的就是test类位置的信息，通过这个`metadata`可以获取test类上的所有信息，例如类名、使用了哪些注解等。然后就可以通过元数据获取到的信息根据条件决定加载哪个bean对象

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata metadata) {
        //各种条件的判定，判定完毕后，决定是否装载指定的bean
        boolean flag = metadata.hasAnnotation("org.springframework.context.annotation.Configuration");
        if(flag){
            return new String[]{"com.itheima.bean.Dog"};
        }
        return new String[]{"com.itheima.bean.Cat"};
    }
}
```



```java
@import(MyImportSelector.class)
public class test(){
}
```





### 4.8 ImportBeanDefinitionRegistrar

上一种方式提供了给定类全路径类名控制bean加载的形式。

spring中定义了一个叫做**BeanDefinition**的东西，它才是控制bean初始化加载的核心。

`BeanDefinition`接口中给出了若干种方法，可以控制bean的相关属性。例如创建的对象是单例还是非单例，在`BeanDefinition`中定义了`scope`属性就可以控制这个。

我们可以通过定义一个类，然后实现`ImportBeanDefinitionRegistrar`接口的方式定义bean，并且还可以对bean的初始化进行更加细粒度的控制。

```java
public class MyRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
        BeanDefinition beanDefinition = 	
            BeanDefinitionBuilder.rootBeanDefinition(Dog.class).getBeanDefinition();
        registry.registerBeanDefinition("dog", beanDefinition);
    }
}
```





### 4.9 BeanDefinitionRegistryPostProcessor

> 1、我们通过实现`ImportBeanDefinitionRegistrar`接口的方式注册的bean，可以将通过`@Component`等方式注册的bean覆盖掉。但是如果有两个类实现了`ImportBeanDefinitionRegistrar`接口，它们注册的bean也重复了，此时后配置的生效
>
> 2、`BeanDefinitionRegistryPostProcessor`，全称bean定义后处理器。在所有bean注册都完成后，它再执行。所以他注册bean的优先级是最高的

```java
public class MyPostProcessor implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        BeanDefinition beanDefinition = 
            BeanDefinitionBuilder.rootBeanDefinition(BookServiceImpl4.class).getBeanDefinition();
        registry.registerBeanDefinition("bookService",beanDefinition);
    }
}
```









## 5、自动配置原理

> SpringBoot的主程序使用了`@SpringBootApplication`注解标记。
>
> 而`@SpringBootApplication`相当于是`@SpringBootConfiguration`、`@EnableAutoConfiguration`和`@ComponentScan`的合成注解，所以主要分析的就是这三个注解

**SpringBoot的启动类**

```java
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }
}
```

**@SpringBootApplication**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```





### 5.1 @SpringBootConfiguration

> 该注解使用`@Configuration`标记，表示该类是一个配置类，说明SpringBoot的启动类是一个配置类

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```





### 5.2 @ComponentScan

> `@ComponentScan`用来标识扫描那些包

```java
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```





### 5.3 @EnableAutoConfiguration

> `@EnableAutoConfiguration`被`@AutoConfigurationPackage`和`@Import({AutoConfigurationImportSelector.class})`标注，因此主要分析这两个注解

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```





#### **5.3.1 @AutoConfigurationPackage**

> 该注解直译为：自动配置包，指定了默认的包规则

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};
}
```



> 其中，`@Import({Registrar.class})`表示将`Registrar.class`中的组件导入ioc容器中
>
> 1、利用`Registrar`通过**BeanDefinition**的方式给容器中导入一系列组件
>
> 2、将指定的一个包下的所有组件导入进MainApplication所在包下。
>
> 其中，metadata中保存的就有启动类MainApplication的信息，`(new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames()`就是获取启动类所在包名，然后将该包下所有的组件都导入ioc容器

**Registrar**

```java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
    Registrar() {
    }

    public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
        // (new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames().toArray(new String[0])的值是com.xrj.boot
        // 利用metadata元数据获得启动类所在的包名，然后通过registry进行bean注册
        AutoConfigurationPackages.register(registry, (String[])(new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames().toArray(new String[0]));
    }

    public Set<Object> determineImports(AnnotationMetadata metadata) {
        return Collections.singleton(new AutoConfigurationPackages.PackageImports(metadata));
    }
}
```

**register方法**

```java
public static void register(BeanDefinitionRegistry registry, String... packageNames) {
    // 这里BEAN的值为 "org.springframework.boot.autoconfigure.AutoConfigurationPackages"
    // 判断当前是否注册了这个bean，默认没有注册
    if (registry.containsBeanDefinition(BEAN)) {
        BeanDefinition beanDefinition = registry.getBeanDefinition(BEAN);
        ConstructorArgumentValues constructorArguments = beanDefinition.getConstructorArgumentValues();
        constructorArguments.addIndexedArgumentValue(0, addBasePackages(constructorArguments, packageNames));
    } else {
        GenericBeanDefinition beanDefinition = new GenericBeanDefinition();
        beanDefinition.setBeanClass(AutoConfigurationPackages.BasePackages.class);
        beanDefinition.getConstructorArgumentValues().addIndexedArgumentValue(0, packageNames);
        beanDefinition.setRole(2);
        // 通过4.9中的方式注册bean，其中BEAN就是bean的id，beanDefinition就是bean对象
        registry.registerBeanDefinition(BEAN, beanDefinition);
    }

}
```





#### 5.3.2 @Import(AutoConfigurationImportSelector.class)

> 该注解用于将Spring Boot自动配置类导入到ioc容器中，也就是SpringBoot用到了什么场景器，他就将什么导入ioc容器
>
> `AutoConfigurationImportSelector`类实现了`DeferredImportSelector`接口、`xxxAware`接口，以及`Ordered`接口
>
> - `DeferredImportSelector`接口继承了`ImportSelector`，在`DeferredImportSelector`中定义了一个接口`Group`，最终要执行的就是`process`方法
> - `xxxAware`接口中都有一个方法`setXxx`，实现这个接口，就可以直接使用xxx对象了
> - `Ordered`接口定义了bean的加载顺序

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
	...
```

```java
public interface DeferredImportSelector extends ImportSelector {
    @Nullable
    default Class<? extends DeferredImportSelector.Group> getImportGroup() {
        return null;
    }

    public interface Group {
        void process(AnnotationMetadata var1, DeferredImportSelector var2);
        ...
```



在`AutoConfigurationImportSelector`类中执行`process`方法

**process方法**

```java
public void process(AnnotationMetadata annotationMetadata, DeferredImportSelector deferredImportSelector) {
    Assert.state(deferredImportSelector instanceof AutoConfigurationImportSelector, () -> {
        return String.format("Only %s implementations are supported, got %s", AutoConfigurationImportSelector.class.getSimpleName(), deferredImportSelector.getClass().getName());
    });
    // 通过 getAutoConfigurationEntry 方法获取所有的 autoConfigurationEntry
    AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = ((AutoConfigurationImportSelector)deferredImportSelector).getAutoConfigurationEntry(annotationMetadata);
    // 将拿到的autoConfigurationEntry设置进去
    this.autoConfigurationEntries.add(autoConfigurationEntry);
    Iterator var4 = autoConfigurationEntry.getConfigurations().iterator();

    while(var4.hasNext()) {
        String importClassName = (String)var4.next();
        this.entries.putIfAbsent(importClassName, annotationMetadata);
    }

}
```



**getAutoConfigurationEntry方法**

> 在该方法中，通过`List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);`获得所有的配置，然后经过去重、排除等操作后，将得到的配置返回，因此主要关注的就是`getCandidateConfigurations`方法

```java
protected AutoConfigurationImportSelector.AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    // 判断annotationMetadata是否可用，当前是可用的
    if (!this.isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    } else {
        // 通过注解元数据获得exclude和excludeName的信息，即希望排除的依赖
        // @EnableAutoConfiguration注解标在了@SpringBootApplication注解上，所以拿到的是这里的信息
        AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
        // 获取所有候选配置，即先拿到所有的配置，然后按需加载
        List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
        configurations = this.removeDuplicates(configurations);
        Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
        this.checkExcludedClasses(configurations, exclusions);
        configurations.removeAll(exclusions);
        configurations = this.getConfigurationClassFilter().filter(configurations);
        this.fireAutoConfigurationImportEvents(configurations, exclusions);
        return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
    }
}
```



**getCandidateConfigurations方法**

> 在`getCandidateConfigurations`方法中，通过`List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());`利用工厂加载，然后得到所有的组件的名字。在`loadFactoryNames`方法中，最后会调用`loadSpringFactories`方法，通过组件名字来加载所有的组件

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    // 通过 loadFactoryNames 方法获取所有配置
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```



**loadFactoryNames方法**

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    // factoryTypeName 为 org.springframework.boot.autoconfigure.EnableAutoConfiguration
    String factoryTypeName = factoryType.getName();
    // 执行 loadSpringFactories 方法获取所有配置
    return (List)loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
}
```



**loadSpringFactories方法**

> 1、`Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");`可以看到，最终是通过加载`META-INF/spring.factories`文件来加载组件的。
>
> 2、因此SpringBoot就会默认扫描我们当前系统里面所有`META-INF/spring.factories`位置的文件，将其中的组件进行加载
>
> 3、`spring-boot-autoconfigure-2.3.4.RELEASE.jar`包里面也有`META-INF/spring.factories`，该文件中写死了spring-boot一启动就要给容器中加载的所有配置类，一共有127个，但是很多配置都带有`@ConditionalOnClass`注解，即满足条件才装配。
>
> 4、虽然我们127个场景的所有自动配置启动的时候默认全部加载，但是`xxxxAutoConfiguration`按照条件装配规则（`@Conditional`），最终会按需配置。

```java
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
    if (result != null) {
        return result;
    } else {
        try {
            // 通过在 "META-INF/spring.factories" 文件下加载所有的配置
            Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
            LinkedMultiValueMap result = new LinkedMultiValueMap();

            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                Iterator var6 = properties.entrySet().iterator();

                while(var6.hasNext()) {
                    Entry<?, ?> entry = (Entry)var6.next();
                    String factoryTypeName = ((String)entry.getKey()).trim();
                    String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                    int var10 = var9.length;

                    for(int var11 = 0; var11 < var10; ++var11) {
                        String factoryImplementationName = var9[var11];
                        result.add(factoryTypeName, factoryImplementationName.trim());
                    }
                }
            }

            cache.put(classLoader, result);
            return result;
        } catch (IOException var13) {
            throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
        }
    }
}
```



​		





### 5.4 自动配置流程

> 在`spring-boot-autoconfigure`包下，因为我们目前是web开发，所以web模块下的各种组件都加载了



**DispatcherServlet**

> 1、`DispatcherServletAutoConfiguration`，可以看到在该类中，如果该类能被加载，首先需要满足当前web模式是servlet的，然后必须有`DispatcherServlet.class`这个类，这样才能满足条件去加载

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308212030807.png)



> 2、在内部类`DispatcherServletConfiguration`中，满足一系列条件后，才会加载该类下的各种组件，同时`@EnableConfigurationProperties({WebMvcProperties.class})`设置了`WebMvcProperties`中的属性和`application.properties`相对应，可以通过修改配置文件来修改其属性，又因为`dispatcherServlet`的参数为`webMvcProperties`，所以可以通过修改配置文件来设置`DispatcherServlet`的属性。然后会将`DispatcherServlet`放入ioc容器，其id就是`dispatcherServlet`
>
> 3、最后还会将`MultipartResolver`文件上传解析器导入ioc容器。他必须保证，有`MultipartResolver`类型的bean，同时没有名字为`multipartResolver`的bean，因为在springmvc阶段我们知道，文件上传解析器的名字必须设置为`multipartResolver`。
>
> 给@Bean标注的方法传入了对象参数，这个参数的值就会从容器中找。所以在SpringBoot中，假如我们没有将文件上传解析器设置为这个名字，他会去ioc容器找到这个组件，然后直接返回，也就是将这个组件加入ioc容器，名字设置为`multipartResolver`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308212038576.png)







**CharacterEncodingFilter**

> 在`HttpEncodingAutoConfiguration`类中，SpringBoot会为我们加载设置编码过滤器的组件。
>
> `@EnableConfigurationProperties({ServerProperties.class})`表示该类中的属性，可以通过配置文件来修改，由`private final Encoding properties; `可以知道`properties`属性就是通过该类得到的，所以可以通过配置文件来修改编码以及其他属性
>
> SpringBoot的组件上一般都有`@ConditionalOnMissingBean`注解，表示如果没有这个组件，才去配置，所以如果我们自己配置了该组件，SpringBoot就不会在配置了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308212042806.png)





**总结**：

- SpringBoot先加载所有的自动配置类  xxxxxAutoConfiguration
- 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值。（xxxxProperties里面读取，xxxProperties和配置文件进行了绑定）
- 生效的配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了
- 定制化配置
  - 用户直接自己@Bean替换底层的组件
  - 用户去看这个组件是获取的配置文件什么值就去修改。







## 6、编写SpringBoot程序

- 引入场景依赖
  - [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)
- 查看自动配置了哪些（选做）
  - 自己分析，引入场景对应的自动配置一般都生效了
  - 配置文件中debug=true开启自动配置报告。
    - Negative（不生效）
    - Positive（生效）
- 是否需要修改
  - 参照文档修改配置项
    - [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)
    - 自己分析。xxxxProperties绑定了配置文件的哪些。
  - 自定义加入或者替换组件
    - @Bean、@Component...





### 6.1 Lombok

> 使用Lombok可以帮助我们在创建pojo类时，不需要写各种构造器、set、get方法等，在编译时，他会自动生成然后去运行
>
> SpringBoot管理了lombok，直接引入依赖，然后在IDEA中下载lombok插件即可

```xml
 <dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
</dependency>
```



- `@Data`：生成所有非静态字段的`get`和`set`方法，`equals()` 方法，`hashCode()` 方法，`toString()` 方法，以及无参构造器

  如果自己写了其他的构造方法，那么`@Data`就不会生成无参构造了

- `@NoArgsConstructor`：无参构造器

- `@AllArgsConstructor`：全参构造器

- `@ToString`：用于定制生成的 `toString()` 方法的格式

- `@EqualsAndHashCode`：用于定制生成的`hashCode()` 和 `equals()` 方法

```java
@NoArgsConstructor  // 无参构造器
@AllArgsConstructor // 全参构造器
@Data   // get、set等
@ToString // toString方法
@EqualsAndHashCode  // equal、hashCode
@ConfigurationProperties(prefix = "mycar")
public class Car {
    private String brand;
    private Integer price;
}
```





**简化日志开发**

> 使用`@Slf4j`注解后，即可使用日志功能，这样我们就不需要使用输出语句打印了。

```JAVA
@Controller
@ResponseBody
@Slf4j
public class HelloController {

    @Autowired
    private Car car;

    @RequestMapping("/hello")
    public String hello(String name){
        log.info("hello " + name);
        return "hello spring boot2" + name;
    }
}
```







### 6.2 Spring Initailizr

创建项目时，直接通过Spring Initailizr创建，会自动为我们创建一个springboot项目，我们可以选择我们使用的场景器，他们自动帮我们添加依赖，同时会帮我们将项目结构搭建好。在`src/main/resources`下的`static`文件夹中存放的使我们web开发的静态资源，templates文件夹存放的是我们的页面。







# 二、SpringBoot核心

## 1、yaml配置文件

> 同以前的properties用法。YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。 创建配置文件时也可以简写为yml
>
> **非常适合用来做以数据为中心的配置文件**。



application.**properties >** application.**yml >** application.**yaml**

不同配置文件中相同配置按照加载优先级相互覆盖，不同配置文件中不同配置全部保留



### 1.1 基本语法

- `key: value`	kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- 字符串无需加引号
  - 如果不加或者加单引号，则里边的内容会被转义，`\n`就会输出`\n`，而不是空格
  - 如果加双引号，里边的内容不会被转移，`\n`直接就是回车被输出





**字面量** ：单个的、不可再分的值。date、boolean、string、number、null

```yml
k: v
```



**对象**：键值对的集合。map、hash、set、object 

```yml
#行内写法：  
k: {k1:v1,k2:v2,k3:v3}

#或
k: 
  k1: v1
  k2: v2
  k3: v3
```



**数组**：一组按次序排列的值。array、list、queue

```yml
#行内写法：  
k: [v1,v2,v3]

#或者
k:
 - v1
 - v2
 - v3
```





### 1.2 实例

**java对象**

```JAVA
@ConfigurationProperties(prefix = "person")
@Data
@Component
public class Person {
    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;
}

@Data
public class Pet {
    private String name;
    private Double weight;
}
```



**yml**

> java对象通过`@ConfigurationProperties(prefix = "person")`与配置文件绑定后，在`application.yml`文件中设置默认值后，从ioc容器中拿到的对象就带有设置的默认值了

```YML
person:
  userName: xrj
  boss: false
  birth: 1999/10/26
  age: 25
  interests: [篮球, 足球, 乒乓球]
  animal:
    - 小狗
    - 小猫
    - 小鸡
  score: {english: 80, chinese: 90, math: 100}
  salarys: [23.3, 100.3, 52.1]
  pet:
    name: xyy
    weight: 50
  allPets:
    health:
      - {name: 小狗, weight: 20}
      - {name: 小猫, weight: 30}
    unhealth: [name: 小鸡, weight: 50, name: 小猪, weight: 80]

```





## 2、web开发

SpringBoot将SpringMVC的大多数场景都配置好了，不用我们自己配置



### 2.1 静态资源规则

> 请求进来，先被DispatcherServlet拦截到，去Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器【SpringBoot帮我们自动配置了】。静态资源也找不到则响应404页面。
>
> SpringBoot规定，只要静态资源放在指定目录下就可以访问到，默认路径为类路径下的`/static` 、`/public` 、 `/resources` 、`/META-INF/resources`
>
> 只要静态资源在这四个目录下，不管他还有多少层目录，都可以访问到，因为`spring.mvc.static-path-pattern: /**`，`/**`是默认值，也就是说静态资源只要在这几个目录中就可以访问到
>
> 访问：当前项目根路径/ + 静态资源名 



**改变静态资源访问前缀**

> 修改静态资源的访问前缀后，静态资源只要在默认的四个目录中，且访问的url为`当前项目 + static-path-pattern + 静态资源名`就可以找到

```YML
spring:
  mvc:
    static-path-pattern: /res/**
```





### 2.2 welcome与favicon

**欢迎页**

只要`index.html`在静态资源目录下，即`/static` 、`/public` 、 `/resources` 、`/META-INF/resources`下，那么访问`localhost:8080/`就会默认访问该页面

注意：`index.html`只能在这几个目录下，不能有深层目录，且不能配置静态资源访问前缀，否则该页面访问不到





**Favicon**

指定网页的图标

将图片命名为`favicon.ico`放在静态资源目录下即可

注意：`favicon.ico`只能放在静态资源目录下，不能有深层目录，且不能配置静态资源访问前缀，否则访问不到







### 2.3 静态资源原理

- SprintBoot启动默认加载 xxxAutoConfiguration 类（自动配置类）
- SpringMVC功能的自动配置类`WebMvcAutoConfiguration`，在加载时会生效



> 1、`WebMvcAutoConfiguration`类下有个内部类`WebMvcAutoConfigurationAdapter`，他使用注解`@EnableConfigurationProperties({WebMvcProperties.class, WebProperties.class})`标注了，表示他会将`WebMvcProperties`和`WebProperties`两个类放入ioc容器，同时将他们与配置文件绑定
>
> 2、该类只有一个有参构造方法，则有参构造的所有参数都会从ioc容器中获取，例如`WebProperties`和`WebMvcProperties`都通过上边的注解将他们放入了ioc容器，所以这里直接从容器获取

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242010800.png)



> 在`WebMvcAutoConfigurationAdapter`的`addResourceHandlers`方法中，他会去配置静态资源访问路径
>
> 1、设置pattern为`/webjars/**`，表示如果访问路径为`/webjars + 静态资源`，那么就会去`classpath:/META-INF/resources/webjars/`下找，webjars就是将各种web相关的组件通过jar包导入项目中
>
> 2、`this.mvcProperties.getStaticPathPattern()`默认为`/**`，我们可以在配置文件中修改它，即修改静态资源的访问前缀
>
> `this.resourceProperties.getStaticLocations()`获取到的数据就是`new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};`  也就是我们默认的静态资源访问路径，因此默认情况下，静态资源放在这四个目录下，我们访问路径为`/**`就可以访问到静态资源，因为和配置文件相关联，所以可以通过配置文件来修改对应配置

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242013022.png)







**欢迎页配置**

> 在该配置中，会创建一个`WelcomePageHandlerMapping`对象

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242020224.png)



> 在`WelcomePageHandlerMapping`类中，它会判断当前是否有欢迎页，同时当前的静态资源访问前缀是不是`/**`，如果满足，那么就跳转到`index.html`页面。否则，它会去寻找一个index的Controller组件来处理。**所以在设置欢迎页时，静态资源访问前缀必须为`/**`才会生效**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242021331.png)





**favicon**

网页图标和代码无关，是浏览器去项目的静态资源目录下寻找的，就是在`/**`下，所以如果设置静态资源访问前缀，那么图标就不会生效







### 2.4 HiddenHttpMethodFilter

> 1、在SpringMVC中，`HiddenHttpMethodFilter`过滤器用于浏览器发送`put`和`delete`请求，需要保证表单提交方式是`post`，且携带一个`_method`参数，其value是发送的请求方式
>
> ```html
> <form action="/user" method="post">
>     <input type="hidden" name="_method" value="put">
>     <input type="submit" value="putUser">
> </form>
> <form action="/user" method="post">
>     <input type="hidden" name="_method" value="delete">
>     <input type="submit" value="deleteUser">
> </form>
> ```
>
> 2、在SpringBoot中，`WebMvcAutoConfiguration`为我们自动配置了该过滤器，但是开启的条件中，`spring.mvc.hiddenmethod.filter`默认为`false`即不开启，因此我们想要使用该过滤器，需要在配置文件中将该配置改为`true`，这样过滤器就会生效。原理和SpringMVC中一致
>
> ```yml
> spring:
>   mvc:
>     hiddenmethod:
>       filter:
>         enabled: true
> ```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242105074.png)





**注意**：该过滤器仅仅是为了使表单提交可以发送`put`和`delete`请求，因为表单提交默认只有`get`和`post`请求。但如果使用postman或其他客户端方式，直接就可以发送`put`和`delete`请求，在经过该过滤器时，`request.getMethod()`获取到的就是`put`和`delete`，所以就会直接放行

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242108991.png)







**改变默认的_method**

> 在`HiddenHttpMethodFilter`，他的`methodParam`默认是`_method`，但是我们可以通过`setMethodParam`方法来修改该默认值，因为SpringBoot
>
> 的`@ConditionalOnMissingBean({HiddenHttpMethodFilter.class})`这个注解保证，当没有配置这个Bean的时候，他才帮我们自己配置，所以只要我们自己配置这个过滤器，然后把`methodParam`修改了就可以了

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242124292.png)



> 我们自己创建了`HiddenHttpMethodFilter`，将他的`MethodParam`设置为了`_m`，并将其放入ioc容器中。所以在表单提交时，带的默认参数名现在就要改为`_m`

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig {
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }
}
```





### 2.5 请求映射原理

> 和SpringMVC一样，最终所有请求都会被DispatcherServlet拦截，并且通过`doDispatch`方法执行，其底层原理和SpringMVC一样
>
> 通过`mappedHandler = this.getHandler(processedRequest);`找到执行该请求的handle方法，这里返回的是`HandlerExecutionChain`，即当前handle方法以及拦截器等。

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
                     // 这里通过请求的路径找到对应的handle方法
                     mappedHandler = this.getHandler(processedRequest);
                     if (mappedHandler == null) {
                         this.noHandlerFound(processedRequest, response);
                         return;
                     }
 
                     HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
                     String method = request.getMethod();
                     boolean isGet = HttpMethod.GET.matches(method);
                     if (isGet || HttpMethod.HEAD.matches(method)) {
                         long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                         if ((new ServletWebRequest(request, response)).checkNotModified(lastModified) && isGet) {
                             return;
                         }
                     }
 
                     if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                         return;
                     }
 
                     mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                     if (asyncManager.isConcurrentHandlingStarted()) {
                         return;
                     }
 
                     this.applyDefaultViewName(processedRequest, mv);
                     mappedHandler.applyPostHandle(processedRequest, response, mv);
                 } catch (Exception var20) {
                     dispatchException = var20;
                 } catch (Throwable var21) {
                     dispatchException = new NestedServletException("Handler dispatch failed", var21);
                 }
 
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



> 在SpringBoot中，这里会有6个`handlerMapping`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242225365.png)



> 第一个`RequestMappingHandlerMapping`对应的handler方法就是我们自己写的各种Controller方法，先让这个handlerMapple通过请求的url找到对应的handler方法，找不到再用后面的handlerMapple去找

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242227428.png)



> 第二个是`WelcomePageHandlerMapping`，是用来寻找欢迎页的，我们的请求路径为`/`，他就会通过这个handlerMapping帮我们找到对应的handler方法，即跳转到index.html页面

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308242231093.png)



- 我们也可以自己给容器中放**HandlerMapping**。自定义 **HandlerMapping**







### 2.6 获取请求参数

> SpringBoot中获取请求参数和SpringMVC中是一样的，因为底层都是通过DispatcherServlet拦截请求。

注解：

- `@PathVariable` 路径变量，可以使用一个`Map<String, String>`来接收全部参数
- `@RequestHeader` 获取请求头，可以使用一个`Map<String, String>`来接收全部请求头信息
- `@RequestParam` 获取请求参数（指问号后的参数，url?a=1&b=2），可以使用一个`Map<String, String>`来接收全部请求参数
- `@CookieValue` 获取Cookie值
- `@RequestBody` 获取请求体
- `@RequestAttribute` 获取request域属性
- `@MatrixVariable` 矩阵变量【SpringBoot特有】



```java
@RestController
public class ParameterTestController {
    
      @GetMapping("/car/{id}/owner/{username}")
      public Map<String,Object> getCar(@PathVariable("id") Integer id,
                                     @PathVariable("username") String name,
                                     @PathVariable Map<String,String> pv,
                                     @RequestHeader("User-Agent") String userAgent,
                                     @RequestHeader Map<String,String> header,
                                     // Integer age,
                                     // String[] inters,  多个同名参数，如果不写@RequestParam 只能用数组或者字符串接收
                                     @RequestParam("age") Integer age,
                                     @RequestParam("inters") List<String> inters,
                                     @RequestParam Map<String,String> params){

        Map<String,Object> map = new HashMap<>();

        map.put("id",id);
        map.put("name",name);
        map.put("pv",pv);
        map.put("userAgent",userAgent);
        map.put("headers",header);
        map.put("age",age);
        map.put("inters",inters);
        map.put("params",params);
        return map;
    }


    @PostMapping("/save")
    public Map postMethod(@RequestBody String content){
        Map<String,Object> map = new HashMap<>();
        map.put("content",content);
        return map;
    }
}
```



 

#### @RequestAttribute

> @RequestAttribute是用来获取请求体中的数据的，也可以直接通过request获取

```java
@Controller
public class RequestController {

    @GetMapping("/goto")
    public String goToPage(HttpServletRequest request){

        request.setAttribute("msg","成功了...");
        request.setAttribute("code",200);
        return "forward:/success";  //转发到  /success请求
    }

    @ResponseBody
    @GetMapping("/success")
    public Map success(@RequestAttribute(value = "msg",required = false) String msg,
                       @RequestAttribute(value = "code",required = false)Integer code,
                       HttpServletRequest request){

        Object msg1 = request.getAttribute("msg");
        Object code1 = request.getAttribute("code");

        Map<String,Object> map = new HashMap<>();

        map.put("reqMethod_msg", msg1);
        map.put("annotation_msg", msg);
        map.put("reqMethod_code", code1);
        map.put("annotation_code", code);

        return map;
    }
}
```







#### @MatrixVariable

> 页面开发时，如果cookie被禁用了，那么session的内容怎么获取？
>
> 我们知道，浏览器每次向服务器发请求都是携带着cookie的，如果服务端设置了session，就会将session的id返回给浏览器，然后浏览器将其设置到cookie的jsessionid字段中，这样每次请求都会拿到对应的session会话。如果cookie被禁用，那么我们无法传递sessionId，也就无法获取会话中的数据了，所以我们可以通过矩阵变量，将jsessionId的值通过矩阵变量的方式进行传递。而如果通过请求参数的方式传递，jsessionId的值是明文传递的，不安全。



- 矩阵变量需要在SpringBoot中手动开启
- 根据RFC3986的规范，矩阵变量应当**绑定在路径变量中**
- 若是有多个矩阵变量，应当使用英文符号`;`进行分隔。
- 若是一个矩阵变量有多个值，应当使用英文符号`,`进行分隔，或者命名多个重复的key即可。
- 如：`/cars/sell;low=34;brand=byd,audi,yd`





##### **手动开启矩阵变量的功能**

> SpringBoot中在`WebMvcAutoConfiguration`中的`WebMvcAutoConfigurationAdapter`类中的`configurePathMatch`类中创建了`UrlPathHelper`
>
> 其中`removeSemicolonContent`属性默认为`true`表示移除逗号后边的内容，我们使用矩阵变量的方式设置url时，会有逗号，所以默认情况下，逗号后边内容都被移除了，矩阵变量也就不会生效。因此我们想要使用矩阵变量，必须手动开启。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308251845853.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308251846554.png)



**1、实现`WebMvcConfigurer`接口**

> 因为`UrlPathHelper`所在的类就实现了`WebMvcConfigurer`接口，所以我们也实现这个接口，然后重写`configurePathMatch`方法，将`UrlPathHelper`的`removeSemicolonContent`设置为`false`即可

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig implements WebMvcConfigurer {
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        urlPathHelper.setRemoveSemicolonContent(false);
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
```



**2、创建返回`WebMvcConfigurer`的Bean**

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig {
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }

    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMatch(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                urlPathHelper.setRemoveSemicolonContent(false);
                configurer.setUrlPathHelper(urlPathHelper);
            }
        };
    }
}
```



##### 矩阵变量的用法

```java
@RestController
public class RequestController {
    // /cars/sell;low=34;brand=byd,audi,yd
    @GetMapping("/cars/{path}")
    public Map carsSell(@MatrixVariable("low") Integer low,
                        @MatrixVariable("brand") List<String> brand,
                        @PathVariable("path") String path){
        Map<String,Object> map = new HashMap<>();

        map.put("low",low);		// 34
        map.put("brand",brand);	// ["byd","audi","yd"]
        map.put("path",path);	// sell
        return map;
    }

    // /boss/1;age=20/2;age=10
    @GetMapping("/boss/{bossId}/{empId}")
    public Map boss(@MatrixVariable(value = "age",pathVar = "bossId") Integer bossAge,
                    @MatrixVariable(value = "age",pathVar = "empId") Integer empAge){
        Map<String,Object> map = new HashMap<>();

        map.put("bossAge",bossAge);
        map.put("empAge",empAge);
        return map;
    }
}
```





### 2.7 参数解析原理

#### 各种类型参数解析原理

1、客户端发请求被DispatcherServlet拦截到后，在`doDispatch`方法中进行处理，首先通过`mappedHandler = this.getHandler(processedRequest);`这行代码找到处理该请求的handler方法，放在一个调用链中

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



2、执行`doDispatch`方法中的这行代码`HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());`  找到一个`HandlerAdapter`用来执行当前的handler方法。该方法中首先拿到所有的`handlerAdapter`，然后循环遍历看哪一个`handlerAdapters`可以处理当前的handler方法，就将其返回

```java
protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
    if (this.handlerAdapters != null) {
        // 拿到所有的HandlerAdapter
        Iterator var2 = this.handlerAdapters.iterator();

        while(var2.hasNext()) {
            HandlerAdapter adapter = (HandlerAdapter)var2.next();
            // 看哪个HandlerAdapter可以处理当前handler方法
            if (adapter.supports(handler)) {
                return adapter;
            }
        }
    }

    throw new ServletException("No adapter for handler [" + handler + "]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
}
```

一共有这些`HandlerAdapter`：

0、支持方法上标注`@RequestMapping` 

1、支持函数式编程的

2、...

3、...

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308252145576.png)

遍历每一个`HandlerAdapter`，通过`if (adapter.supports(handler)) `这行代码判断这个`HandlerAdapter`是否支持当前的handler方法，如果满足该条件表示支持，那么就将该`HandlerAdapter`返回

```java
public final boolean supports(Object handler) {
    return handler instanceof HandlerMethod && this.supportsInternal((HandlerMethod)handler);
}
```



3、拿到`HandlerAdapter`后，就通过`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`这行代码开始执行目标方法

```java
@Nullable
public final ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    // 进到handle方法后，执行handleInternal方法，最后的返回值是ModelAndView
    return this.handleInternal(request, response, (HandlerMethod)handler);
}
```

```java
protected ModelAndView handleInternal(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
    this.checkRequest(request);
    // 先创建一个ModelAndView作为返回值
    ModelAndView mav;
    if (this.synchronizeOnSession) {
        HttpSession session = request.getSession(false);
        if (session != null) {
            Object mutex = WebUtils.getSessionMutex(session);
            synchronized(mutex) {
                mav = this.invokeHandlerMethod(request, response, handlerMethod);
            }
        } else {
            // 通过这行代码来执行目标方法
            mav = this.invokeHandlerMethod(request, response, handlerMethod);
        }
    } else {
        mav = this.invokeHandlerMethod(request, response, handlerMethod);
    }

    if (!response.containsHeader("Cache-Control")) {
        if (this.getSessionAttributesHandler(handlerMethod).hasSessionAttributes()) {
            this.applyCacheSeconds(response, this.cacheSecondsForSessionAttributeHandlers);
        } else {
            this.prepareResponse(response);
        }
    }

    return mav;
}
```



4、执行`invokeHandlerMethod`方法

```java
@Nullable
protected ModelAndView invokeHandlerMethod(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
    ServletWebRequest webRequest = new ServletWebRequest(request, response);

    ModelAndView var15;
    try {
        WebDataBinderFactory binderFactory = this.getDataBinderFactory(handlerMethod);
        ModelFactory modelFactory = this.getModelFactory(handlerMethod, binderFactory);
        // 将目标handler方法handlerMethod，包装为invocableMethod
        ServletInvocableHandlerMethod invocableMethod = this.createInvocableHandlerMethod(handlerMethod);
        // 这里为目标方法添加参数解析器
        if (this.argumentResolvers != null) {
            invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
        }
		// 为目标方法添加返回值处理器
        if (this.returnValueHandlers != null) {
            invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
        }

        invocableMethod.setDataBinderFactory(binderFactory);
        invocableMethod.setParameterNameDiscoverer(this.parameterNameDiscoverer);
        ModelAndViewContainer mavContainer = new ModelAndViewContainer();
        mavContainer.addAllAttributes(RequestContextUtils.getInputFlashMap(request));
        modelFactory.initModel(webRequest, mavContainer, invocableMethod);
        mavContainer.setIgnoreDefaultModelOnRedirect(this.ignoreDefaultModelOnRedirect);
        AsyncWebRequest asyncWebRequest = WebAsyncUtils.createAsyncWebRequest(request, response);
        asyncWebRequest.setTimeout(this.asyncRequestTimeout);
        WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
        asyncManager.setTaskExecutor(this.taskExecutor);
        asyncManager.setAsyncWebRequest(asyncWebRequest);
        asyncManager.registerCallableInterceptors(this.callableInterceptors);
        asyncManager.registerDeferredResultInterceptors(this.deferredResultInterceptors);
        Object result;
        if (asyncManager.hasConcurrentResult()) {
            result = asyncManager.getConcurrentResult();
            mavContainer = (ModelAndViewContainer)asyncManager.getConcurrentResultContext()[0];
            asyncManager.clearConcurrentResult();
            LogFormatUtils.traceDebug(this.logger, (traceOn) -> {
                String formatted = LogFormatUtils.formatValue(result, !traceOn);
                return "Resume with async result [" + formatted + "]";
            });
            invocableMethod = invocableMethod.wrapConcurrentResult(result);
        }

        // 这行代码继续执行目标方法
        invocableMethod.invokeAndHandle(webRequest, mavContainer, new Object[0]);
        if (asyncManager.isConcurrentHandlingStarted()) {
            result = null;
            return (ModelAndView)result;
        }

        var15 = this.getModelAndView(mavContainer, modelFactory, webRequest);
    } finally {
        webRequest.requestCompleted();
    }

    return var15;
}
```

为目标方法添加的参数解析器，其中有`RequestParam...`，用来解析被`@RequestParam`标注的参数；`PathVariable...`用来解析被`@PathVariable`标注的参数；原生的Servlet参数会使用`Servlet...`的解析器解析，例如`HttpRequest`类型的参数会使用`ServletRequestMethodArgumentResolver`解析器解析

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308252156512.png)

为目标方法添加的返回值处理器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308252157302.png)



5、执行`invokeAndHandle`方法

```java
public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
    // 通过invokeForRequest执行目标方法，并获得返回值。这里就是通过反射执行了
    Object returnValue = this.invokeForRequest(webRequest, mavContainer, providedArgs);
    this.setResponseStatus(webRequest);
    if (returnValue == null) {
        if (this.isRequestNotModified(webRequest) || this.getResponseStatus() != null || mavContainer.isRequestHandled()) {
            this.disableContentCachingIfNecessary(webRequest);
            mavContainer.setRequestHandled(true);
            return;
        }
    } else if (StringUtils.hasText(this.getResponseStatusReason())) {
        mavContainer.setRequestHandled(true);
        return;
    }

    mavContainer.setRequestHandled(false);
    Assert.state(this.returnValueHandlers != null, "No return value handlers");

    try {
        // 拿到返回值之后执行
        this.returnValueHandlers.handleReturnValue(returnValue, this.getReturnValueType(returnValue), mavContainer, webRequest);
    } catch (Exception var6) {
        if (logger.isTraceEnabled()) {
            logger.trace(this.formatErrorForReturnValue(returnValue), var6);
        }

        throw var6;
    }
}
```



6、执行`invokeForRequest`方法

```java
@Nullable
public Object invokeForRequest(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
    // 获取该方法的所有参数值
    Object[] args = this.getMethodArgumentValues(request, mavContainer, providedArgs);
    if (logger.isTraceEnabled()) {
        logger.trace("Arguments: " + Arrays.toString(args));
    }
	
    // 通过反射调用该方法，args就是上边获取的参数值
    return this.doInvoke(args);
}
```



7、执行`getMethodArgumentValues`方法获取到所有的参数

```java
protected Object[] getMethodArgumentValues(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
    // 获取当前方法的所有参数名
    MethodParameter[] parameters = this.getMethodParameters();
    // 如果当前方法不需要参数，就返回空
    if (ObjectUtils.isEmpty(parameters)) {
        return EMPTY_ARGS;
    } else {
        // 创建一个Object类型的数组，长度就是parameters.length，用来放请求的参数值，这里和javaweb手写springmvc类似
        Object[] args = new Object[parameters.length];

        // 遍历目标方法的每一个参数
        for(int i = 0; i < parameters.length; ++i) {
            MethodParameter parameter = parameters[i];
            parameter.initParameterNameDiscovery(this.parameterNameDiscoverer);
            args[i] = findProvidedArgument(parameter, providedArgs);
            if (args[i] == null) {
                // 判断当前是否存在一个参数解析器支持当前的参数
                if (!this.resolvers.supportsParameter(parameter)) {
                    throw new IllegalStateException(formatArgumentError(parameter, "No suitable resolver"));
                }

                try {
                    // 参数解析器支持当前参数，就继续执行，通过参数解析器获取到当前参数对应的值，然后放到args数组中
                    args[i] = this.resolvers.resolveArgument(parameter, mavContainer, request, this.dataBinderFactory);
                } catch (Exception var10) {
                    if (logger.isDebugEnabled()) {
                        String exMsg = var10.getMessage();
                        if (exMsg != null && !exMsg.contains(parameter.getExecutable().toGenericString())) {
                            logger.debug(formatArgumentError(parameter, exMsg));
                        }
                    }

                    throw var10;
                }
            }
        }
		// 返回获取到的所有参数的值
        return args;
    }
}
```



8、执行`if (!this.resolvers.supportsParameter(parameter))`

```java
public boolean supportsParameter(MethodParameter parameter) {
    // 在supportsParameter方法中通过getArgumentResolver(parameter)方法判断当前参数解析器是否支持当前参数
    return this.getArgumentResolver(parameter) != null;
}
```

```java
@Nullable
private HandlerMethodArgumentResolver getArgumentResolver(MethodParameter parameter) {
    // 先看缓存中能否找到支持该参数的解析器
    HandlerMethodArgumentResolver result = (HandlerMethodArgumentResolver)this.argumentResolverCache.get(parameter);
    if (result == null) {
        // 拿到了当前所有的参数解析器
        Iterator var3 = this.argumentResolvers.iterator();
		
        // 循环遍历每一个参数解析器
        while(var3.hasNext()) {
            HandlerMethodArgumentResolver resolver = (HandlerMethodArgumentResolver)var3.next();
            // 判断该解析器是否支持当前参数，如果支持，就将该解析器返回，同时将该参数解析器和该参数加入缓存，下次就从缓存取
            if (resolver.supportsParameter(parameter)) {
                result = resolver;
                this.argumentResolverCache.put(parameter, resolver);
                break;
            }
        }
    }

    return result;
}
```



9、执行` if (resolver.supportsParameter(parameter))`判断该参数解析器是否支持当前参数

```java
public boolean supportsParameter(MethodParameter parameter) {
    // 因为第一个参数解析器是RequestParam的参数解析器，所以就判断当前参数是否有RequestParam类型的注解，满足的话，就返回true，表示当前解析器可以解析
    // 如果不满足，就循环后边的解析器，看哪个解析器可以解析
    if (parameter.hasParameterAnnotation(RequestParam.class)) {
        if (!Map.class.isAssignableFrom(parameter.nestedIfOptional().getNestedParameterType())) {
            return true;
        } else {
            RequestParam requestParam = (RequestParam)parameter.getParameterAnnotation(RequestParam.class);
            return requestParam != null && StringUtils.hasText(requestParam.name());
        }
    } else if (parameter.hasParameterAnnotation(RequestPart.class)) {
        return false;
    } else {
        parameter = parameter.nestedIfOptional();
        if (MultipartResolutionDelegate.isMultipartArgument(parameter)) {
            return true;
        } else {
            // 如果没有标任何注解，就直接将参数名设置为请求参数名，那么会使用RequestParamMethodArgumentResolver这个参数解析器，一共有两个这样的参数				 解析器，第二个参数解析器的useDefaultResolution属性为true，表示可以使用默认的解析器。但第一个RequestParamMethodArgumentResolver				这个属性为false，则无法使用
            return this.useDefaultResolution ? BeanUtils.isSimpleProperty(parameter.getNestedParameterType()) : false;
        }
    }
}
```



10、获取到参数解析器之后，返回7，就执行`args[i] = this.resolvers.resolveArgument(parameter, mavContainer, request, this.dataBinderFactory);`这行代码，通过参数解析器去获取相应的参数

```java
@Nullable
public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
    // 先从缓存中拿到参数解析器
    HandlerMethodArgumentResolver resolver = this.getArgumentResolver(parameter);
    if (resolver == null) {
        throw new IllegalArgumentException("Unsupported parameter type [" + parameter.getParameterType().getName() + "]. supportsParameter should be called first.");
    } else {
        // 通过这行代码，用拿到的参数解析器，去获取当前参数的值
        return resolver.resolveArgument(parameter, mavContainer, webRequest, binderFactory);
    }
}
```



11、执行`resolveArgument`方法，解析参数的值

```java
@Nullable
public final Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
    // 获取当前的参数名
    AbstractNamedValueMethodArgumentResolver.NamedValueInfo namedValueInfo = this.getNamedValueInfo(parameter);
    MethodParameter nestedParameter = parameter.nestedIfOptional();
    // 拿到参数名
    Object resolvedName = this.resolveEmbeddedValuesAndExpressions(namedValueInfo.name);
    if (resolvedName == null) {
        throw new IllegalArgumentException("Specified name must not resolve to null: [" + namedValueInfo.name + "]");
    } else {
        // 拿到参数名后，通过resolveName方法去获取当前参数名对应的参数
        Object arg = this.resolveName(resolvedName.toString(), nestedParameter, webRequest);
        if (arg == null) {
            if (namedValueInfo.defaultValue != null) {
                arg = this.resolveEmbeddedValuesAndExpressions(namedValueInfo.defaultValue);
            } else if (namedValueInfo.required && !nestedParameter.isOptional()) {
                this.handleMissingValue(namedValueInfo.name, nestedParameter, webRequest);
            }

            arg = this.handleNullValue(namedValueInfo.name, arg, nestedParameter.getNestedParameterType());
        } else if ("".equals(arg) && namedValueInfo.defaultValue != null) {
            arg = this.resolveEmbeddedValuesAndExpressions(namedValueInfo.defaultValue);
        }

        if (binderFactory != null) {
            WebDataBinder binder = binderFactory.createBinder(webRequest, (Object)null, namedValueInfo.name);

            try {
                arg = binder.convertIfNecessary(arg, parameter.getParameterType(), parameter);
            } catch (ConversionNotSupportedException var11) {
                throw new MethodArgumentConversionNotSupportedException(arg, var11.getRequiredType(), namedValueInfo.name, parameter, var11.getCause());
            } catch (TypeMismatchException var12) {
                throw new MethodArgumentTypeMismatchException(arg, var12.getRequiredType(), namedValueInfo.name, parameter, var12.getCause());
            }

            if (arg == null && namedValueInfo.defaultValue == null && namedValueInfo.required && !nestedParameter.isOptional()) {
                this.handleMissingValueAfterConversion(namedValueInfo.name, nestedParameter, webRequest);
            }
        }

        // 空方法
        this.handleResolvedValue(arg, namedValueInfo.name, parameter, mavContainer, webRequest);
        // 将拿到的参数返回
        return arg;
    }
}
```

```java
@Nullable
protected Object resolveName(String name, MethodParameter parameter, NativeWebRequest request) throws Exception {
    Map<String, String> uriTemplateVars = (Map)request.getAttribute(HandlerMapping.URI_TEMPLATE_VARIABLES_ATTRIBUTE, 0);
    // 通过原生的servlet和参数名获取请求参数，并返回
    return uriTemplateVars != null ? uriTemplateVars.get(name) : null;
}
```



12、获取完所有的参数就返回6，通过反射执行当前handler方法，然后回到5，拿到返回值后，执行`this.returnValueHandlers.handleReturnValue(returnValue, this.getReturnValueType(returnValue), mavContainer, webRequest);`

```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception {
    HandlerMethodReturnValueHandler handler = this.selectHandler(returnValue, returnType);
    if (handler == null) {
        throw new IllegalArgumentException("Unknown return value type: " + returnType.getParameterType().getName());
    } else {
        handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);
    }
}
```

```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {
    mavContainer.setRequestHandled(true);
    ServletServerHttpRequest inputMessage = this.createInputMessage(webRequest);
    ServletServerHttpResponse outputMessage = this.createOutputMessage(webRequest);
    this.writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage);
}
```



13、处理完返回值后，在5中会返回一个ModelAndView对象，然后一直将该对象返回到`doDispatch`方法中，至此目标方法就执行完毕







#### Model、Map、ModelMap

> `Model`类型的参数，使用`ModelMethodProcessor`参数解析器解析。
>
> `Map`和`ModelAndMap`类型的参数，使用`MapMethodProcessor`参数解析器解析。
>
> 同时，他们中的数据最后会被存放到`request`域中

```java
@RequestMapping(value = "/params", method = RequestMethod.GET)
public String testParam(Model model, Map map, ModelMap modelMap) {
    model.addAttribute("hello01", "world01");
    map.put("hello02", "world02");
    modelMap.addAttribute("hello03", "world03");
    return "forward:/success";
}
```

```java
@ResponseBody
@GetMapping("/success")
public Map success(HttpServletRequest request){
    Object hello01 = request.getAttribute("hello01");
    Object hello02 = request.getAttribute("hello02");
    Object hello03 = request.getAttribute("hello03");

    Map<String,Object> map = new HashMap<>();

    map.put("hello01", hello01);
    map.put("hello02", hello02);
    map.put("hello03", hello03);

    return map;
}
```



对于`Model`类型的参数，使用`ModelMethodProcessor`参数解析器解析。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308281931167.png)

对于`Map`类型的参数，使用`MapMethodProcessor`参数解析器解析。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308281929605.png)

对于`ModelAndMap`类型的参数，使用`MapMethodProcessor`参数解析器解析。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308281930255.png)



拿到参数解析器后，进入`resolveArgument`方法

```java
@Nullable
    public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
        HandlerMethodArgumentResolver resolver = this.getArgumentResolver(parameter);
        if (resolver == null) {
            throw new IllegalArgumentException("Unsupported parameter type [" + parameter.getParameterType().getName() + "]. supportsParameter should be called first.");
        } else {
            // 使用拿到的解析器解析参数
            return resolver.resolveArgument(parameter, mavContainer, webRequest, binderFactory);
        }
    }
```



```java
@Nullable
    public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
        Assert.state(mavContainer != null, "ModelAndViewContainer is required for model exposure");
        // 通过mavContainer拿到当前的Model参数
        return mavContainer.getModel();
    }
```



在`ModelAndViewContainer`类中，`getModel`方法获取的就是一个`BindingAwareModelMap()`类型的对象，对于`Model`、`Map`、`ModelMap`三种类型的参数，都是从这里获取到一个空的`BindingAwareModelMap`，然后传递给方法的形参

```java
public class ModelAndViewContainer {
    ...
    private final ModelMap defaultModel = new BindingAwareModelMap();
	...

    public ModelMap getModel() {
        if (this.useDefaultModel()) {
            return this.defaultModel;
        } else {
            if (this.redirectModel == null) {
                this.redirectModel = new ModelMap();
            }

            return this.redirectModel;
        }
    }
    ...
}
```





**将拿到的数据存储到request域中**

最终返回值是一个`ModelAndView`对象，其中`view`属性中存储了要跳转的页面，`model`属性存储了数据

执行完`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`拿到返回值之后，会去执行`processDispatchResult`方法进行页面渲染，`this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);`



在`processDispatchResult`方法中，收先判断当前是否存在异常，然后mv不为空就执行`this.render(mv, request, response);`进行渲染

```java
private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv, @Nullable Exception exception) throws Exception {
    boolean errorView = false;
    if (exception != null) {
        if (exception instanceof ModelAndViewDefiningException) {
            this.logger.debug("ModelAndViewDefiningException encountered", exception);
            mv = ((ModelAndViewDefiningException)exception).getModelAndView();
        } else {
            Object handler = mappedHandler != null ? mappedHandler.getHandler() : null;
            mv = this.processHandlerException(request, response, handler, exception);
            errorView = mv != null;
        }
    }

    if (mv != null && !mv.wasCleared()) {
        // 执行这行代码，进行页面渲染
        this.render(mv, request, response);
        if (errorView) {
            WebUtils.clearErrorRequestAttributes(request);
        }
    } else if (this.logger.isTraceEnabled()) {
        this.logger.trace("No view rendering, null ModelAndView returned.");
    }

    if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
        if (mappedHandler != null) {
            mappedHandler.triggerAfterCompletion(request, response, (Exception)null);
        }

    }
}
```



```java
protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
    Locale locale = this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale();
    response.setLocale(locale);
    String viewName = mv.getViewName();
    View view;
    if (viewName != null) {
        view = this.resolveViewName(viewName, mv.getModelInternal(), locale, request);
        if (view == null) {
            throw new ServletException("Could not resolve view with name '" + mv.getViewName() + "' in servlet with name '" + this.getServletName() + "'");
        }
    } else {
        view = mv.getView();
        if (view == null) {
            throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a View object in servlet with name '" + this.getServletName() + "'");
        }
    }

    if (this.logger.isTraceEnabled()) {
        this.logger.trace("Rendering view [" + view + "] ");
    }

    try {
        if (mv.getStatus() != null) {
            request.setAttribute(View.RESPONSE_STATUS_ATTRIBUTE, mv.getStatus());
            response.setStatus(mv.getStatus().value());
        }
		
        // 执行render方法
        view.render(mv.getModelInternal(), request, response);
    } catch (Exception var8) {
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Error rendering view [" + view + "]", var8);
        }

        throw var8;
    }
}
```



```java
public void render(@Nullable Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
    if (this.logger.isDebugEnabled()) {
        this.logger.debug("View " + this.formatViewName() + ", model " + (model != null ? model : Collections.emptyMap()) + (this.staticAttributes.isEmpty() ? "" : ", static attributes " + this.staticAttributes));
    }

    Map<String, Object> mergedModel = this.createMergedOutputModel(model, request, response);
    this.prepareResponse(request, response);
    // 将model对象封装为mergedModel后执行该行代码
    this.renderMergedOutputModel(mergedModel, this.getRequestToExpose(request), response);
}
```



```java
protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
    // 执行该行代码，意为将模型数据暴露到request域
    this.exposeModelAsRequestAttributes(model, request);
    this.exposeHelpers(request);
    String dispatcherPath = this.prepareForRendering(request, response);
    RequestDispatcher rd = this.getRequestDispatcher(request, dispatcherPath);
    if (rd == null) {
        throw new ServletException("Could not get RequestDispatcher for [" + this.getUrl() + "]: Check that the corresponding file exists within your web application archive!");
    } else {
        if (this.useInclude(request, response)) {
            response.setContentType(this.getContentType());
            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Including [" + this.getUrl() + "]");
            }

            rd.include(request, response);
        } else {
            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Forwarding to [" + this.getUrl() + "]");
            }

            rd.forward(request, response);
        }

    }
}
```

最终在该方法中，将model中的所有数据都存放到request域中

```java
protected void exposeModelAsRequestAttributes(Map<String, Object> model, HttpServletRequest request) throws Exception {
    model.forEach((name, value) -> {
        if (value != null) {
            request.setAttribute(name, value);
        } else {
            request.removeAttribute(name);
        }

    });
}
```







#### 自定义参数绑定

> 1、将前端的请求参数，绑定为一个POJO对象，传递给后端。此时参数解析器使用的是`ServletModelAttributeMethodProcessor`
>
> 2、然后使用该参数解析器去解析数据时，会创建一个`WebDataBinder`的对象，用来将请求的数据保存到pojo类型的参数中，`WebDataBinder binder = binderFactory.createBinder(webRequest, attribute, name);` 这里`attribute`就是一个空的pojo对象
>
> 3、`bindRequestParameters(binder, webRequest);`通过`WebDataBinder`将请求的数据绑定到创建的pojo对象中
>
> 4、`WebDataBinder` 利用它里面的 `Converters` ，找到一个合适的`Converter`将请求数据转成指定的数据类型【例如String -> Integer】再次封装到JavaBean中



```html
<form action="/saveUser" method="post">
    姓名： <input name="userName" value="zhangsan"/> <br/>
    年龄： <input name="age" value="18"/> <br/>
    生日： <input name="birth" value="2019/12/10"/> <br/>
        宠物姓名：<input name="pet.name" value="阿猫"/><br/>
        宠物年龄：<input name="pet.age" value="5"/>
    <input type="submit" value="保存"/>
</form>
```

```java
@RequestMapping("/saveUser")
@ResponseBody
public Person saveUser(Person person) {
    return person;
}
```







#### 自定义Converter

> 当我们不使用级联的方式输入pet的数据时，例如直接将pet的所有数据在一个输入框输入，这样SpringBoot默认为我们加载的转换器，就无法解析数据了，此时就需要我们自定义参数转换器，将`"啊猫,3"`的字符串转换为`pet`类型数据

```html
<form action="/saveUser" method="post">
    姓名： <input name="userName" value="zhangsan"/> <br/>
    年龄： <input name="age" value="18"/> <br/>
    生日： <input name="birth" value="2019/12/10"/> <br/>
	宠物： <input name="pet" value="啊猫,3"/>
    <input type="submit" value="保存"/>
</form>
```

```java
@RequestMapping("/saveUser")
@ResponseBody
public Person saveUser(Person person) {
    return person;
}
```



```java
@Configuration(proxyBeanMethods = false)
public class WebConfig {
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }

    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMatch(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                urlPathHelper.setRemoveSemicolonContent(false);
                configurer.setUrlPathHelper(urlPathHelper);
            }
            @Override
            public void addFormatters(FormatterRegistry registry){
                registry.addConverter(new Converter<String, Pet>() {
                    @Override
                    public Pet convert(String source) {
                        if (!StringUtils.isEmpty(source)) {
                            Pet pet = new Pet();
                            String[] split = source.split(",");
                            pet.setName(split[0]);
                            pet.setAge(Integer.parseInt(split[1]));
                            return pet;
                        }
                        return null;
                    }
                });
            }
        };
    }
}
```





#### 返回值处理器

如果是重定向视图，那么返回的mv中model为空，只有视图名，因为重定向是再发一次请求，即使model有数据也没有用。

如果是转发视图，只要控制层方法的形参位置有对象类型的参数，那么这个数据最终就会被放到model中，基本类型数据不会放进去

重定向视图`this.useDefaultModel()`为false，转发视图`this.useDefaultModel()`为true

```java
public ModelMap getModel() {
    if (this.useDefaultModel()) {
        return this.defaultModel;
    } else {
        if (this.redirectModel == null) {
            this.redirectModel = new ModelMap();
        }
        return this.redirectModel;
    }
}
```

```java
private boolean useDefaultModel() {
    // 1.如果this.redirectModelScenario为false，即不重定向，那么方法直接返回true
    // 2.如果this.redirectModel为空并且ignoreDefaultModelOnRedirect为false，即不忽略defaultModel，那么返回true
    // 因为我们现在是重定向，所以返回false，即拿到一个空的model。如果不返回空的model，重定向时再发一次请求，model中数据存到request中也失效了
    return (!this.redirectModelScenario || (this.redirectModel == null && !this.ignoreDefaultModelOnRedirect));
}
```



当后端想要给前端返回json数据，需要导入相关依赖，在SpringMVC中，我们需要导入`jackson`的依赖，但是在SpringBoot中，我们导入了web场景，`jackson`依赖已经被导入了。因此控制层方法只要使用`@ResponseBody`注解标注，那么返回的数据就会以json格式返回。

返回json数据时，`DispatcherServlet`执行目标handler方法后返回的`ModelAndView`对象为空，直接将json数据写给了浏览器。如果控制层方法往`Model`对象写入了数据，那么该数据也不会存到`request`域中，因为返回的是json数据，返回结束后，当前请求就结束了，所有请求域的数据也就失效了，所以返回的`ModelAndView`为空，也就不会往`request`域存储数据

```java
@ResponseBody
@RequestMapping("/test/ret")
public Person testRet(){
    Person person = new Person();
    person.setUserName("xrj");
    person.setAge(20);
    person.setBirth(new Date());
    return person;
}
```



**返回值处理器`ReturnValueHandler`原理：**

1. 返回值处理器判断是否支持这种类型返回值 `supportsReturnType`
2. 返回值处理器调用 `handleReturnValue` 进行处理
3. `RequestResponseBodyMethodProcessor` 可以处理返回值标了`@ResponseBody` 注解的。
   - 利用 `MessageConverters` 进行处理 将数据写为json
     1. 内容协商（浏览器默认会以请求头的方式告诉服务器他能接受什么样的内容类型）
     2. 服务器最终根据自己自身的能力，决定服务器能生产出什么样内容类型的数据，
     3. SpringMVC会挨个遍历所有容器底层的 `HttpMessageConverter` ，看谁能处理？
        1. 得到`MappingJackson2HttpMessageConverter`可以将对象写为json
        2. 利用`MappingJackson2HttpMessageConverter`将对象转为json再写出去



在执行`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`这行代码时，在`invokeHandlerMethod`方法中，会添加参数解析器，以及各种返回值处理器。例如第0个可以处理返回值为`ModelAndView`类型，第1个可以处理返回值为`Model`类型，第2个可以处理返回值为`View`类型...

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308252157302.png)



然后进入`invokeAndHandle`方法执行目标handler方法，并得到返回值

```java
public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
    // 执行目标handler方法，并得到返回值，此时的返回值是一个Person对象
    Object returnValue = this.invokeForRequest(webRequest, mavContainer, providedArgs);
    this.setResponseStatus(webRequest);
    if (returnValue == null) {
        if (this.isRequestNotModified(webRequest) || this.getResponseStatus() != null || mavContainer.isRequestHandled()) {
            this.disableContentCachingIfNecessary(webRequest);
            mavContainer.setRequestHandled(true);
            return;
        }
    } else if (StringUtils.hasText(this.getResponseStatusReason())) {
        mavContainer.setRequestHandled(true);
        return;
    }

    mavContainer.setRequestHandled(false);
    Assert.state(this.returnValueHandlers != null, "No return value handlers");

    try {
        // 执行这行代码，进行返回值的处理，returnValue是返回的Person对象，this.getReturnValueType(returnValue)是返回值类型
        this.returnValueHandlers.handleReturnValue(returnValue, this.getReturnValueType(returnValue), mavContainer, webRequest);
    } catch (Exception var6) {
        if (logger.isTraceEnabled()) {
            logger.trace(this.formatErrorForReturnValue(returnValue), var6);
        }

        throw var6;
    }
}
```

```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception {
    // 找到一个可以处理当前返回值类型的返回值处理器
    HandlerMethodReturnValueHandler handler = this.selectHandler(returnValue, returnType);
    if (handler == null) {
        throw new IllegalArgumentException("Unknown return value type: " + returnType.getParameterType().getName());
    } else {
        // 拿到返回值处理器后，用该返回值处理器来处理返回值
        handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);
    }
}
```

```java
@Nullable
private HandlerMethodReturnValueHandler selectHandler(@Nullable Object value, MethodParameter returnType) {
    // 判断是否是异步类型
    boolean isAsyncValue = this.isAsyncReturnValue(value, returnType);
    // 拿到当前所有的返回值处理器
    Iterator var4 = this.returnValueHandlers.iterator();

    HandlerMethodReturnValueHandler handler;
    do {
        do {
            if (!var4.hasNext()) {
                return null;
            }

            handler = (HandlerMethodReturnValueHandler)var4.next();
        } while(isAsyncValue && !(handler instanceof AsyncHandlerMethodReturnValueHandler));
        // 遍历每一个返回值处理器，找到一个可以处理当前返回值类型的
    } while(!handler.supportsReturnType(returnType));

    return handler;
}
```

```java
public boolean supportsReturnType(MethodParameter returnType) {
    // 最终找到RequestResponseBodyMethodProcessor,这个处理器可以处理标注了ResponseBody注解的返回值类型
    return AnnotatedElementUtils.hasAnnotation(returnType.getContainingClass(), ResponseBody.class) || returnType.hasMethodAnnotation(ResponseBody.class);
}
```



```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {
    mavContainer.setRequestHandled(true);
    // 将webRequest包装为request
    ServletServerHttpRequest inputMessage = this.createInputMessage(webRequest);
    // 将webRequest包装为response
    ServletServerHttpResponse outputMessage = this.createOutputMessage(webRequest);
    // 通过writeWithMessageConverters方法来处理返回值
    this.writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage);
}
```

```java
protected <T> void writeWithMessageConverters(@Nullable T value, MethodParameter returnType, ServletServerHttpRequest inputMessage, ServletServerHttpResponse outputMessage) throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {
    Object body;
    Class valueType;
    Object targetType;
    // 如果当前value是字符串类型
    if (value instanceof CharSequence) {
        body = value.toString();
        valueType = String.class;
        targetType = String.class;
    } else {
        // Person对象
        body = value;
        // Person
        valueType = this.getReturnValueType(value, returnType);
        // Person
        targetType = GenericTypeResolver.resolveType(this.getGenericType(returnType), returnType.getContainingClass());
    }
    
	// 判断是否是资源类型
    if (this.isResourceType(value, returnType)) {
        ...
    }
	
    // 创建一个空的媒体类型
    MediaType selectedMediaType = null;
    // 看当前响应之前是否已经明确了媒体类型
    MediaType contentType = outputMessage.getHeaders().getContentType();
    boolean isContentTypePreset = contentType != null && contentType.isConcrete();
    if (isContentTypePreset) {
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Found 'Content-Type:" + contentType + "' in response");
        }

        selectedMediaType = contentType;
    } else {
        // 拿到request对象
        HttpServletRequest request = inputMessage.getServletRequest();

        List acceptableTypes;
        try {
            // 得到当前浏览器支持的媒体类型，这个就是浏览器在发请求时请求头信息，会携带他支持什么类型的数据
            acceptableTypes = this.getAcceptableMediaTypes(request);
        } catch (HttpMediaTypeNotAcceptableException var20) {
            ...
        }

        // 得到服务器当前可以产生的媒体类型
        List<MediaType> producibleTypes = this.getProducibleMediaTypes(request, valueType, (Type)targetType);
        if (body != null && producibleTypes.isEmpty()) {
            throw new HttpMessageNotWritableException("No converter found for return value of type: " + valueType);
        }

        List<MediaType> mediaTypesToUse = new ArrayList();
        Iterator var15 = acceptableTypes.iterator();

        MediaType mediaType;
        // 遍历游览器可以接收的媒体类型
        while(var15.hasNext()) {
            mediaType = (MediaType)var15.next();
            Iterator var17 = producibleTypes.iterator();
			
            // 遍历当前可以产生的媒体类型
            while(var17.hasNext()) {
                MediaType producibleType = (MediaType)var17.next();
                // 如果当前可以产生的媒体类型，浏览器可以接收，那么就加入到mediaTypesToUse中
                if (mediaType.isCompatibleWith(producibleType)) {
                    mediaTypesToUse.add(this.getMostSpecificMediaType(mediaType, producibleType));
                }
            }
        }

        ...
        MediaType.sortBySpecificityAndQuality(mediaTypesToUse);
        var15 = mediaTypesToUse.iterator();
		
        // 拿到当前可以使用的媒体类型
        while(var15.hasNext()) {
            mediaType = (MediaType)var15.next();
            if (mediaType.isConcrete()) {
                selectedMediaType = mediaType;
                break;
            }

            if (mediaType.isPresentIn(ALL_APPLICATION_MEDIA_TYPES)) {
                selectedMediaType = MediaType.APPLICATION_OCTET_STREAM;
                break;
            }
        }
		...
    }
	
    // 消息转换器
    HttpMessageConverter converter;
    GenericHttpMessageConverter genericConverter;
    label183: {
        if (selectedMediaType != null) {
            selectedMediaType = selectedMediaType.removeQualityValue();
            // 拿到当前所有的消息转换器
            Iterator var23 = this.messageConverters.iterator();
	
            // 遍历所有的消息转换器，看那个消息转换器可以将返回值类型【Person】转为目标类型【selectedMediaType，当前就是json数据】
            while(var23.hasNext()) {
                converter = (HttpMessageConverter)var23.next();
                genericConverter = converter instanceof GenericHttpMessageConverter ? (GenericHttpMessageConverter)converter : null;
                // 判断当前的消息转换器是否可以将valueType转为selectedMediaType，这里最终拿到了MappingJackson2HttpMessageConverter
                // 这个转换器可以将对象写为json
                if (genericConverter != null) {
                    if (((GenericHttpMessageConverter)converter).canWrite((Type)targetType, valueType, selectedMediaType)) {
                        break label183;
                    }
                } else if (converter.canWrite(valueType, selectedMediaType)) {
                    break label183;
                }
            }
        }

        if (body != null) {
            Set<MediaType> producibleMediaTypes = (Set)inputMessage.getServletRequest().getAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE);
            if (!isContentTypePreset && CollectionUtils.isEmpty(producibleMediaTypes)) {
                throw new HttpMediaTypeNotAcceptableException(this.getSupportedMediaTypes(body.getClass()));
            }

            throw new HttpMessageNotWritableException("No converter for [" + valueType + "] with preset Content-Type '" + contentType + "'");
        }

        return;
    }

    body = this.getAdvice().beforeBodyWrite(body, returnType, selectedMediaType, converter.getClass(), inputMessage, outputMessage);
    if (body != null) {
        LogFormatUtils.traceDebug(this.logger, (traceOn) -> {
            return "Writing [" + LogFormatUtils.formatValue(body, !traceOn) + "]";
        });
        // 添加各种响应头信息
        this.addContentDispositionHeader(inputMessage, outputMessage);
        if (genericConverter != null) {
            // 将body【Person对象】写为json数据
            genericConverter.write(body, (Type)targetType, selectedMediaType, outputMessage);
        } else {
            converter.write(body, selectedMediaType, outputMessage);
        }
    } else if (this.logger.isDebugEnabled()) {
        this.logger.debug("Nothing to write: null body");
    }

}
```

```java
public final void write(final T t, @Nullable final Type type, @Nullable MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
    HttpHeaders headers = outputMessage.getHeaders();
    // 添加响应头，Content-Type:"application/json"
    this.addDefaultHeaders(headers, t, contentType);
    if (outputMessage instanceof StreamingHttpOutputMessage) {
        StreamingHttpOutputMessage streamingOutputMessage = (StreamingHttpOutputMessage)outputMessage;
        streamingOutputMessage.setBody((outputStream) -> {
            this.writeInternal(t, type, new HttpOutputMessage() {
                public OutputStream getBody() {
                    return outputStream;
                }

                public HttpHeaders getHeaders() {
                    return headers;
                }
            });
        });
    } else {
        // 将Person对象，写为json
        this.writeInternal(t, type, outputMessage);
        outputMessage.getBody().flush();
    }

}
```

**浏览器可接收的媒体类型**

> 这里的q表示权重，即优先接收哪一种类型

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308291612163.png)

**服务端可以产生的媒体类型**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308291612819.png)

**可以使用的媒体类型**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308291611493.png)

**所有的消息转换器**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308291615472.png)

**消息转换器是一个接口**

```java
public interface HttpMessageConverter<T> {
    boolean canRead(Class<?> var1, @Nullable MediaType var2);

    boolean canWrite(Class<?> var1, @Nullable MediaType var2);

    List<MediaType> getSupportedMediaTypes();

    T read(Class<? extends T> var1, HttpInputMessage var2) throws IOException, HttpMessageNotReadableException;

    void write(T var1, @Nullable MediaType var2, HttpOutputMessage var3) throws IOException, HttpMessageNotWritableException;
}
```







#### 内容协商原理

> 1、根据客户端接收能力的不同，服务端会返回不同媒体类型的数据
>
> 2、例如上边的例子，浏览器发送请求，它的请求头中默认可以接收任意数据【有`*/*`】，但是xml的权重为0.9，json权重为0.8，他会优先接收xml数据，但是服务端默认是不能发送xml数据的，所以浏览器接收的就是json数据
>
> 3、引入xml依赖后，服务端就具备了发送xml数据的能力了，它会多一个消息转换器。这样我们使用postman测试，请求头的`Accept`字段设置为xml，就接收xml数据，设置为json，就接收json数据
>
> 4、第一次遍历消息转换器的目的是，得到当前服务端能够将返回值转化为哪些类型。第二次遍历消息转换器的目的是，判断哪个消息转换器可以将将返回值转换为我们选择的客户端需要的类型

**引入xml依赖**

```xml
<dependency>
     <groupId>com.fasterxml.jackson.dataformat</groupId>
     <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```

​	

在`writeWithMessageConverters`方法中，首先通过`acceptableTypes = this.getAcceptableMediaTypes(request);`获得客户端接受的媒体类型

```java
private List<MediaType> getAcceptableMediaTypes(HttpServletRequest request) throws HttpMediaTypeNotAcceptableException {
    return this.contentNegotiationManager.resolveMediaTypes(new ServletWebRequest(request));
}
```

```java
public List<MediaType> resolveMediaTypes(NativeWebRequest request) throws HttpMediaTypeNotAcceptableException {
    // 获取到所有的内容协商策略，默认只有一个，基于请求头的策略
    Iterator var2 = this.strategies.iterator();

    List mediaTypes;
    do {
        if (!var2.hasNext()) {
            return MEDIA_TYPE_ALL_LIST;
        }

        ContentNegotiationStrategy strategy = (ContentNegotiationStrategy)var2.next();
        // 遍历所有的策略，通过当前策略获取当前客户端接受的媒体类型
        mediaTypes = strategy.resolveMediaTypes(request);
    } while(mediaTypes.equals(MEDIA_TYPE_ALL_LIST)); // 如果媒体类型不为空就退出

    return mediaTypes;
}
```

```java
public List<MediaType> resolveMediaTypes(NativeWebRequest request) throws HttpMediaTypeNotAcceptableException {
    // 通过request的getHeaderValues方法获取请求头信息
    String[] headerValueArray = request.getHeaderValues("Accept");
    if (headerValueArray == null) {
        return MEDIA_TYPE_ALL_LIST;
    } else {
        List headerValues = Arrays.asList(headerValueArray);

        try {
            List<MediaType> mediaTypes = MediaType.parseMediaTypes(headerValues);
            MediaType.sortBySpecificityAndQuality(mediaTypes);
            return !CollectionUtils.isEmpty(mediaTypes) ? mediaTypes : MEDIA_TYPE_ALL_LIST;
        } catch (InvalidMediaTypeException var5) {
            throw new HttpMediaTypeNotAcceptableException("Could not parse 'Accept' header " + headerValues + ": " + var5.getMessage());
        }
    }
}
```



通过`List<MediaType> producibleTypes = this.getProducibleMediaTypes(request, valueType, (Type)targetType);`获取当前服务端可以产生的媒体类型

```java
protected List<MediaType> getProducibleMediaTypes(HttpServletRequest request, Class<?> valueClass, @Nullable Type targetType) {
    Set<MediaType> mediaTypes = (Set)request.getAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE);
    if (!CollectionUtils.isEmpty(mediaTypes)) {
        return new ArrayList(mediaTypes);
    } else {
        List<MediaType> result = new ArrayList();
        // 拿到当前所有消息转换器，因为添加了xml的依赖，所以会多出MappingJackson2XmlHttpMessageConverter的消息转换器
        Iterator var6 = this.messageConverters.iterator();

        while(true) {
            while(var6.hasNext()) {
                // 遍历所有的消息转换器
                HttpMessageConverter<?> converter = (HttpMessageConverter)var6.next();
                if (converter instanceof GenericHttpMessageConverter && targetType != null) {
                    // 如果当前消息转换器可以转换当前返回值类型的数据
                    if (((GenericHttpMessageConverter)converter).canWrite(targetType, valueClass, (MediaType)null)) {
                        // 那么就将当前转换器可以转化的类型加入result中
                        result.addAll(converter.getSupportedMediaTypes(valueClass));
                    }
                } else if (converter.canWrite(valueClass, (MediaType)null)) {
                    result.addAll(converter.getSupportedMediaTypes(valueClass));
                }
            }
			// 最终的result就是服务端可以转换的媒体类型
            return (List)(result.isEmpty() ? Collections.singletonList(MediaType.ALL) : result);
        }
    }
}
```







#### 基于请求参数的内容协商

> 使用postman发请求，我们可以设置请求头中的`Accept`字段，从而控制我们返回的数据类型。但是浏览器的`Accept`字段我们是无法修改的，它优先是使用xml格式的数据，但如果我们导入了xml依赖，又希望浏览器返回json数据，这时我们就需要使用**基于请求参数的内容协商**



在`writeWithMessageConverters`方法中通过`acceptableTypes = this.getAcceptableMediaTypes(request);`获得客户端接受的媒体类型

默认情况下，使用的是基于请求头的策略，这样就会通过请求头获取`Accept`参数，得到客户端接受的媒体类型

```java
private List<MediaType> getAcceptableMediaTypes(HttpServletRequest request) throws HttpMediaTypeNotAcceptableException {
    // contentNegotiationManager 内容协商管理器，默认使用基于请求头的策略
    return this.contentNegotiationManager.resolveMediaTypes(new ServletWebRequest(request));
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308291807150.png)





**开启浏览器参数方式内容协商功能**

> 将该字段设置为true后，表示开启基于请求参数的内容协商功能。
>
> 这样在发请求时，后边带上参数`format=json`，内容协商管理器中就会多出一个`ParameterContentNegotiationStrategy`的策略，基于参数的形式，通过设置`format`的值就可以设置不同的媒体类型

```yml
spring:
    contentnegotiation:
      favor-parameter: true
```

`http://localhost:8080/test/ret?format=xml`

`http://localhost:8080/test/ret?format=json`



开启该功能后，内容协商管理器就会多一个`ParameterContentNegotiationStrategy`的策略

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308291817886.png)



```java
public List<MediaType> resolveMediaTypes(NativeWebRequest request) throws HttpMediaTypeNotAcceptableException {
    // 得到所有的内容协商策略，开启参数方式的内容协商功能后，这里第一个策略就是基于参数的
    Iterator var2 = this.strategies.iterator();

    List mediaTypes;
    do {
        if (!var2.hasNext()) {
            return MEDIA_TYPE_ALL_LIST;
        }

        ContentNegotiationStrategy strategy = (ContentNegotiationStrategy)var2.next();
        // 通过当前策略获得媒体类型
        mediaTypes = strategy.resolveMediaTypes(request);
    } while(mediaTypes.equals(MEDIA_TYPE_ALL_LIST));

    return mediaTypes;
}
```

```java
public List<MediaType> resolveMediaTypes(NativeWebRequest webRequest) throws HttpMediaTypeNotAcceptableException {
    // 如果浏览器的format=xml，this.getMediaTypeKey(webRequest)的值就是xml
    return this.resolveMediaTypeKey(webRequest, this.getMediaTypeKey(webRequest));
}
```

```java
// 当前的key就是format的值，返回客户端指定的媒体类型
public List<MediaType> resolveMediaTypeKey(NativeWebRequest webRequest, @Nullable String key) throws HttpMediaTypeNotAcceptableException {
    if (StringUtils.hasText(key)) {
        // 通过key将其封装为媒体类型，然后返回
        MediaType mediaType = this.lookupMediaType(key);
        if (mediaType != null) {
            this.handleMatch(key, mediaType);
            return Collections.singletonList(mediaType);
        }

        mediaType = this.handleNoMatch(webRequest, key);
        if (mediaType != null) {
            this.addMapping(key, mediaType);
            return Collections.singletonList(mediaType);
        }
    }

    return MEDIA_TYPE_ALL_LIST;
}
```







#### 自定义MessageConverter

```java
/**
 * 我们现在可以使服务端返回json格式数据、xml格式数据等
 * 但是如果我们想要返回我们自定义格式的数据，SpringBoot为我们提供的默认的消息转换器是做不到的，此时就需要我们自定义消息转换器
 */
@ResponseBody
@RequestMapping("/test/ret")
public Person testRet(){
    Person person = new Person();
    person.setUserName("xrj");
    person.setAge(20);
    person.setBirth(new Date());
    return person;
}
```



自定义消息转换器相当于还是个性化SpringBoot的配置，所以还是在我们的配置类中，添加上一个Bean，`WebMvcConfigurer`，让他实现`extendMessageConverters`方法，即扩展消息转换器，默认的还会存在

**配置类**

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig {
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                converters.add(new MyConverter());
            }
        };
    }
}
```

**自定义的消息转换器**

```java
public class MyConverter implements HttpMessageConverter<Person> {
    /**
     * 判断该转换器是否可以读取clazz类型的对象，并将其转换为mediaType类型，用于参数解析的时候
     */
    @Override
    public boolean canRead(Class clazz, MediaType mediaType) {
        return false;
    }

    /**
     * 判断该转换器是否可以将clazz类型的参数写为mediaType类型，用于处理返回值
     * 这里我们只判断是否为Person类型的数据
     */
    @Override
    public boolean canWrite(Class clazz, MediaType mediaType) {
        return clazz.isAssignableFrom(Person.class);
    }

    /**
     * 服务器要统计所有MessageConverter都能写出哪些内容类型
     */
    @Override
    public List<MediaType> getSupportedMediaTypes() {
        return MediaType.parseMediaTypes("application/myType");
    }

    @Override
    public Person read(Class<? extends Person> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException {
        return null;
    }

    @Override
    public void write(Person person, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
        // 自定义数据的写出
        String data = person.getUserName() + ";" + person.getAge();
        OutputStream body = outputMessage.getBody();
        body.write(data.getBytes());
    }
}
```



当我们配置了自定义的消息转换器后，我们使用postman发请求，`Accept`字段设置为`application/myType`，就会返回我们自定义格式的数据

**注意**：这里`Accept`字段前缀一定要带上`application`、`text`或`image`等，因为`Accept` 头字段用于指示客户端期望接收的响应内容类型。它通常使用 MIME 类型来表示，MIME 类型的格式通常是 `type/subtype`。`type` 表示媒体类型的大类（如 `application`、`text`、`image` 等），而 `subtype` 表示具体的子类型。





当我们自定义了`MessageConverter`后，使用postman发请求，可以自定义`Accept`字段，即使用**基于请求头**的策略。如果我们使用浏览器发请求，想要设置返回的媒体类型，只能使用**基于参数**的策略，但是默认情况下，基于参数的策略只有`json`和`xml`两种类型，想要设置其他类型，我们需要自定义配置

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308301802892.png)



```java
@Configuration(proxyBeanMethods = false)
public class WebConfig /*implements WebMvcConfigurer*/ {
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
   
            /**
             * 添加参数解析器
             * @param registry
             */
            @Override
            public void addFormatters(FormatterRegistry registry){
                registry.addConverter(new Converter<String, Pet>() {
                    @Override
                    public Pet convert(String source) {
                        if (!StringUtils.isEmpty(source)) {
                            Pet pet = new Pet();
                            String[] split = source.split(",");
                            pet.setName(split[0]);
                            pet.setAge(Integer.parseInt(split[1]));
                            return pet;
                        }
                        return null;
                    }
                });
            }

            /**
             * 添加消息转换器
             * @param converters
             */
            @Override
            public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                converters.add(new MyConverter());
            }

            /**
             * 自定义内容协商策略
             * @param configurer
             */
            @Override
            public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
                // Map<String, MediaType> mediaTypes
                Map<String, MediaType> mediaTypes = new HashMap<>();
                mediaTypes.put("json",MediaType.APPLICATION_JSON);
                mediaTypes.put("xml",MediaType.APPLICATION_XML);
                // 自定义媒体类型
                mediaTypes.put("myType", MediaType.parseMediaType("application/myType"));
                // 指定支持解析哪些参数对应的哪些媒体类型
                ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypes);
                // parameterStrategy.setParameterName("ff"); // 可以修改参数format的名字

                // 因为这里是重新配置了内容协商策略，因此默认的会被覆盖
                // 如果不添加请求头策略，使用postman测试时，请求头不会生效，所以会返回*/*，即客户端接收任意类型数据，优先使用json数据
                // 所以还需添加请求头处理策略，否则accept:application/json、application/xml则会失效
                HeaderContentNegotiationStrategy headerStrategy = new HeaderContentNegotiationStrategy();

                configurer.strategies(Arrays.asList(parameterStrategy, headerStrategy));
            }
        };
    }
}
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308301812490.png)









### 2.8 整合Thymeleaf

> 1、在SpringBoot中，想要使用Thymeleaf的话，直接将Thymeleaf的starter导入即可
>
> 2、导入对应的starter后，SpringBoot启动时就会自动配置Thymeleaf，并将其配置与`ThymeleafProperties.class`绑定
>
> 3、在`ThymeleafProperties.class`中，它与项目的配置文件绑定，并定义了许多默认值，例如页面前缀是`classpath:/templates/`，页面后缀是`.html`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

```java
@AutoConfiguration(
    after = {WebMvcAutoConfiguration.class, WebFluxAutoConfiguration.class}
)
@EnableConfigurationProperties({ThymeleafProperties.class})
@ConditionalOnClass({TemplateMode.class, SpringTemplateEngine.class})
@Import({ReactiveTemplateEngineConfiguration.class, DefaultTemplateEngineConfiguration.class})
public class ThymeleafAutoConfiguration {
```

```java
@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
    private Charset encoding;
    private boolean cache;
    private Integer templateResolverOrder;
    private String[] viewNames;
    private String[] excludedViewNames;
    private boolean enableSpringElCompiler;
    private boolean renderHiddenMarkersBeforeCheckboxes;
    private boolean enabled;
    private final ThymeleafProperties.Servlet servlet;
    private final ThymeleafProperties.Reactive reactive;
```







### 2.9 web简单项目

> 我们使用Thymeleaf做视图解析
>
> 将所有的静态资源放在了resources/static下，页面放在了resource/templates下



**登录控制层**

> 当访问路径为`/`时，默认跳转到`login.html`页面，在登录页面输入用户名、密码后，将表单提交到`/login`中，然后这里校验用户名密码，成功后，将用户登录信息保存在session中，同时，重定向到`/main.html`请求下，`/main.html`请求中，判断如果当前session中有用户登录信息，那么跳转到`main.html`页面，否则返回登录页面
>
> **注意**：如果在`/login`请求下，我们直接跳转到`main.html`页面上，因为是转发，所以地址栏不会变，那么我们刷新页面时，永远都是给`/login`发请求，造成表单重复提交，所以这里使用重定向方式，这样重定向过后，地址栏就改变了，刷新页面就是刷新的`/main.html`请求。

```java
@Controller
public class IndexController {
    @RequestMapping("/")
    public String index(){
        return "login";
    }

    @RequestMapping("/login")
    public String login(User user, HttpSession session, Model model){
        if ("xrj".equals(user.getUsername()) && "123456".equals(user.getPassword())) {
            session.setAttribute("loginUser", user);
            return "redirect:/main.html";
        } else {
            model.addAttribute("msg", "用户名密码不正确");
            return "login";
        }
    }

    @RequestMapping("/main.html")
    public String mainPage(HttpSession session, Model model) {
        User user = (User) session.getAttribute("loginUser");
        if (user != null) {
            return "main";
        } else {
            model.addAttribute("msg", "请重新登陆");
            return "login";
        }
    }
}
```







**抽取公共页面**

公共页面`/templates/common.html`

```HTML
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>公共页面</title>

    <div th:fragment="commonheader">
        <!--common-->
        <link href="css/style.css" rel="stylesheet">
        <link href="css/style-responsive.css" rel="stylesheet">

        <!-- HTML5 shim and Respond.js IE8 support of HTML5 elements and media queries -->
        <!--[if lt IE 9]>
        <script src="js/html5shiv.js"></script>
        <script src="js/respond.min.js"></script>
        <![endif]-->
    </div>

</head>
<body>

<!-- header section start-->
<div th:fragment="headermenu" class="header-section">
...
</div>
<!-- header section end-->

<!-- left side start-->
<div id="leftmenu" class="left-side sticky-left-side">
...
</div>
<!-- left side end-->

<div id="commonfooter">
    <!-- Placed js at the end of the document so the pages load faster -->
    <script src="js/jquery-1.10.2.min.js"></script>
    <script src="js/jquery-ui-1.9.2.custom.min.js"></script>
    <script src="js/jquery-migrate-1.2.1.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/modernizr.min.js"></script>
    <script src="js/jquery.nicescroll.js"></script>
    <!--common scripts for all pages-->
    <script src="js/scripts.js"></script>
</div>
</body>
</html>
```



**在对应的页面使用该片段**

`/templates/table/basic_table.html`

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
    <meta name="description" content="">
    <meta name="author" content="ThemeBucket">
    <link rel="shortcut icon" href="#" type="image/png">

    <div th:replace="common :: commonheader"></div>

    <title>Basic Table</title>
</head>

<body class="sticky-header">

<section>
    <div th:replace="common :: #leftmenu"></div>
    <!-- main content start-->
    <div class="main-content">
        <div th:replace="common :: headermenu"></div>

        <!-- page heading start-->
        <div class="page-heading">
          ...
        </div>
        <!-- page heading end-->

        <!--body wrapper start-->
        <div class="wrapper">
  			...
        </div>
        <!--body wrapper end-->

        <!--footer section start-->
        <footer>
            2020 &copy; AdminEx by ThemeBucket </a>
        </footer>
        <!--footer section end-->


    </div>
    <!-- main content end-->
</section>
<div th:replace="common :: #commonfooter"></div>
</body>
</html>
```







### 2.10 视图解析

> 在SpringMVC配置了Thymeleaf后
>
> 1、当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过转发的方式实现跳转
>
> 2、当控制器方法中所设置的视图名称以`forward:`为前缀时，创建**`InternalResourceView`**视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀`forward:`去掉，剩余部分作为最终路径通过转发的方式实现跳转
>
> 3、当控制器方法中所设置的视图名称以`redirect:`为前缀时，创建**`RedirectView`**视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀`redirect:`去掉，剩余部分作为最终路径通过重定向的方式实现跳转



**视图解析**：

- 返回值以 `forward:` 开始： `new InternalResourceView(forwardUrl);` -->  转发`request.getRequestDispatcher(path).forward(request, response);` 
- 返回值以 `redirect:` 开始： `new RedirectView()` --> 重定向`response.sendRedirect(encodedURL);`
- 返回值是普通字符串：`new ThymeleafView()`--->



**handler方法**

```java
@RequestMapping("/login")
public String login(User user, HttpSession session, Model model){
    if ("xrj".equals(user.getUsername()) && "123456".equals(user.getPassword())) {
        session.setAttribute("loginUser", user);
        return "redirect:/main.html";
    } else {
        model.addAttribute("msg", "用户名密码不正确");
        return "login";
    }
}
```



```java
@Nullable
protected ModelAndView invokeHandlerMethod(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
    ServletWebRequest webRequest = new ServletWebRequest(request, response);

    ModelAndView var15;
    try {
        WebDataBinderFactory binderFactory = this.getDataBinderFactory(handlerMethod);
        ModelFactory modelFactory = this.getModelFactory(handlerMethod, binderFactory);
        ServletInvocableHandlerMethod invocableMethod = this.createInvocableHandlerMethod(handlerMethod);
        if (this.argumentResolvers != null) {
            invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
        }

        if (this.returnValueHandlers != null) {
            invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
        }

        ...

            // 执行handler方法
            invocableMethod.invokeAndHandle(webRequest, mavContainer, new Object[0]);
        if (asyncManager.isConcurrentHandlingStarted()) {
            result = null;
            return (ModelAndView)result;
        }
        // 执行完handler方法后，mavContainer对象中有返回的视图名，以及model中的数据，但是当前方法中，model为空。
        // 如果handelr方法没有给model设置值，并且handler方法的参数是一个自定义类型对象（从请求参数中确定的），就把他重新放在 				      				ModelAndViewContainer，这里User对象就在其中
        var15 = this.getModelAndView(mavContainer, modelFactory, webRequest);
    } finally {
        webRequest.requestCompleted();
    }

    return var15;
}
```



```java
public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
    // 这里returnValue的值为"redirect:/main.html"
    Object returnValue = this.invokeForRequest(webRequest, mavContainer, providedArgs);
    this.setResponseStatus(webRequest);
    if (returnValue == null) {
        if (this.isRequestNotModified(webRequest) || this.getResponseStatus() != null || mavContainer.isRequestHandled()) {
            this.disableContentCachingIfNecessary(webRequest);
            mavContainer.setRequestHandled(true);
            return;
        }
    } else if (StringUtils.hasText(this.getResponseStatusReason())) {
        mavContainer.setRequestHandled(true);
        return;
    }

    mavContainer.setRequestHandled(false);
    Assert.state(this.returnValueHandlers != null, "No return value handlers");

    try {
        // 返回值处理
        this.returnValueHandlers.handleReturnValue(returnValue, this.getReturnValueType(returnValue), mavContainer, webRequest);
    } catch (Exception var6) {
        if (logger.isTraceEnabled()) {
            logger.trace(this.formatErrorForReturnValue(returnValue), var6);
        }

        throw var6;
    }
}
```

```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception {
    // 现在返回值为"redirect:/main.html"，选择一个可以处理该返回值的返回值处理器，最终拿到的返回值处理器为ViewNameMethodReturnValueHandler
    HandlerMethodReturnValueHandler handler = this.selectHandler(returnValue, returnType);
    if (handler == null) {
        throw new IllegalArgumentException("Unknown return value type: " + returnType.getParameterType().getName());
    } else {
        // 使用拿到的返回值处理器处理返回值
        handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);
    }
}
```

```java
// ViewNameMethodReturnValueHandler的supportsReturnType方法
public boolean supportsReturnType(MethodParameter returnType) {
    Class<?> paramType = returnType.getParameterType();
    // 返回值类型为空或者是字符串类型就使用这个返回值处理器
    return Void.TYPE == paramType || CharSequence.class.isAssignableFrom(paramType);
}
```



```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception {
    // 如果当前返回值是字符串
    if (returnValue instanceof CharSequence) {
        // 拿到视图名"redirect:/main.html"
        String viewName = returnValue.toString();
        // 设置ModelAndView的视图名
        mavContainer.setViewName(viewName);
        // 判断当前是否为转发视图
        if (this.isRedirectViewName(viewName)) {
            // 如果是转发视图，就将该字段设置为true
            mavContainer.setRedirectModelScenario(true);
        }
    } else if (returnValue != null) {
        throw new UnsupportedOperationException("Unexpected return type: " + returnType.getParameterType().getName() + " in method: " + returnType.getMethod());
    }

}
```

```java
protected boolean isRedirectViewName(String viewName) {
    return PatternMatchUtils.simpleMatch(this.redirectPatterns, viewName) || viewName.startsWith("redirect:");
}
```



这里执行完就会返回`invokeHandlerMethod`，去执行`var15 = this.getModelAndView(mavContainer, modelFactory, webRequest);`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308311527167.png)

```java
@Nullable
// 这里参数中的mavContainer包含返回的视图名，同时defaultModel就是我们传入handler方法的model，如果为空那么里边含有handler方法中的自定义参数对象
private ModelAndView getModelAndView(ModelAndViewContainer mavContainer, ModelFactory modelFactory, NativeWebRequest webRequest) throws Exception {
    modelFactory.updateModel(webRequest, mavContainer);
    if (mavContainer.isRequestHandled()) {
        return null;
    } else {
        // 拿到model，现在是重定向，所以获取到一个空的model
        ModelMap model = mavContainer.getModel();
        // 创建一个ModelAndView，将拿到的model设置进去，并且设置视图名"redirect:/main.html"
        ModelAndView mav = new ModelAndView(mavContainer.getViewName(), model, mavContainer.getStatus());
        if (!mavContainer.isViewReference()) {
            mav.setView((View)mavContainer.getView());
        }

        if (model instanceof RedirectAttributes) {
            Map<String, ?> flashAttributes = ((RedirectAttributes)model).getFlashAttributes();
            HttpServletRequest request = (HttpServletRequest)webRequest.getNativeRequest(HttpServletRequest.class);
            if (request != null) {
                RequestContextUtils.getOutputFlashMap(request).putAll(flashAttributes);
            }
        }
		// 返回该ModelAndView
        return mav;
    }
}
```

```java
public ModelMap getModel() {
    if (this.useDefaultModel()) {
        return this.defaultModel;
    } else {
        if (this.redirectModel == null) {
            this.redirectModel = new ModelMap();
        }
        return this.redirectModel;
    }
}
```

```java
private boolean useDefaultModel() {
    // 1.如果this.redirectModelScenario为false，即不重定向，那么方法直接返回true
    // 2.如果this.redirectModel为空并且ignoreDefaultModelOnRedirect为false，即不忽略defaultModel，那么返回true
    // 因为我们现在是重定向，所以返回false，即拿到一个空的model。如果不返回空的model，重定向时再发一次请求，model中数据存到request中也失效了
    return (!this.redirectModelScenario || (this.redirectModel == null && !this.ignoreDefaultModelOnRedirect));
}
```



然后就通过`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`拿到了`ModelAndView`对象，这个mv中存储了视图名，和Model数据

然后执行`this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);`处理视图

```java
private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv, @Nullable Exception exception) throws Exception {
    boolean errorView = false;
    // 判断是否有异常
    if (exception != null) {
        if (exception instanceof ModelAndViewDefiningException) {
            this.logger.debug("ModelAndViewDefiningException encountered", exception);
            mv = ((ModelAndViewDefiningException)exception).getModelAndView();
        } else {
            Object handler = mappedHandler != null ? mappedHandler.getHandler() : null;
            mv = this.processHandlerException(request, response, handler, exception);
            errorView = mv != null;
        }
    }

    if (mv != null && !mv.wasCleared()) {
        // 执行该方法进行视图渲染
        this.render(mv, request, response);
        if (errorView) {
            WebUtils.clearErrorRequestAttributes(request);
        }
    } else if (this.logger.isTraceEnabled()) {
        this.logger.trace("No view rendering, null ModelAndView returned.");
    }

    if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
        if (mappedHandler != null) {
            mappedHandler.triggerAfterCompletion(request, response, (Exception)null);
        }

    }
}
```

```java
protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
    // 国际化的东西
    Locale locale = this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale();
    response.setLocale(locale);
    // 拿到视图名 "redirect:/main.html"
    String viewName = mv.getViewName();
    View view;
    if (viewName != null) {
        // 通过解析当前视图名，拿到视图解析器，这里是RedirectView
        view = this.resolveViewName(viewName, mv.getModelInternal(), locale, request);
        if (view == null) {
            throw new ServletException("Could not resolve view with name '" + mv.getViewName() + "' in servlet with name '" + this.getServletName() + "'");
        }
    } else {
        view = mv.getView();
        if (view == null) {
            throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a View object in servlet with name '" + this.getServletName() + "'");
        }
    }

    if (this.logger.isTraceEnabled()) {
        this.logger.trace("Rendering view [" + view + "] ");
    }

    try {
        if (mv.getStatus() != null) {
            request.setAttribute(View.RESPONSE_STATUS_ATTRIBUTE, mv.getStatus());
            response.setStatus(mv.getStatus().value());
        }
		// 用当前的视图解析器来渲染，这里是重定向视图，所以底层最终调用的就是response.sendRedirect(encodedURL);
        view.render(mv.getModelInternal(), request, response);
    } catch (Exception var8) {
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Error rendering view [" + view + "]", var8);
        }

        throw var8;
    }
}
```

```java
@Nullable
protected View resolveViewName(String viewName, @Nullable Map<String, Object> model, Locale locale, HttpServletRequest request) throws Exception {
    if (this.viewResolvers != null) {
        // 拿到所有的视图解析器
        Iterator var5 = this.viewResolvers.iterator();

        while(var5.hasNext()) {
            // 遍历每一个视图解析器
            ViewResolver viewResolver = (ViewResolver)var5.next();
            // 用当前的视图解析器去解析该视图名，这里第一个视图解析器包含了后边的四种，所以第一个就可以解析
            View view = viewResolver.resolveViewName(viewName, locale);
            if (view != null) {
                return view;
            }
        }
    }

    return null;
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202308311536749.png)



```java
@Nullable
public View resolveViewName(String viewName, Locale locale) throws Exception {
    RequestAttributes attrs = RequestContextHolder.getRequestAttributes();
    Assert.state(attrs instanceof ServletRequestAttributes, "No current ServletRequestAttributes");
    // 拿到浏览器支持的媒体类型
    List<MediaType> requestedMediaTypes = this.getMediaTypes(((ServletRequestAttributes)attrs).getRequest());
    if (requestedMediaTypes != null) {
        // 拿到可以使用的视图解析器
        List<View> candidateViews = this.getCandidateViews(viewName, locale, requestedMediaTypes);
        // 拿到最合适的视图解析器，这里是RedirectView
        View bestView = this.getBestView(candidateViews, requestedMediaTypes, attrs);
        if (bestView != null) {
            return bestView;
        }
    }

    String mediaTypeInfo = this.logger.isDebugEnabled() && requestedMediaTypes != null ? " given " + requestedMediaTypes.toString() : "";
    if (this.useNotAcceptableStatusCode) {
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Using 406 NOT_ACCEPTABLE" + mediaTypeInfo);
        }

        return NOT_ACCEPTABLE_VIEW;
    } else {
        this.logger.debug("View remains unresolved" + mediaTypeInfo);
        return null;
    }
}
```







### 2.11 拦截器

- 编写一个拦截器实现`HandlerInterceptor`接口

- 拦截器注册到容器中（实现`WebMvcConfigurer`的`addInterceptors()`）

- 指定拦截规则（<span style="color:red;">注意</span>，如果是拦截所有`/**`，静态资源也会被拦截】



**编写拦截器**

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        Object loginUser = session.getAttribute("loginUser");
        if (loginUser != null) {
            return true;
        }

        request.setAttribute("msg", "请先登录");
        request.getRequestDispatcher("/").forward(request, response);
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```



**注册拦截器**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/", "/login", "/css/**", "/fonts/**", "/images/**", "/js/**");
    }
}
```



> 拦截器原理，见SpringMVC

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309011455269.png" style="zoom:67%;" />









### 2.12 文件上传

> 和SSM中文件上传方式一样
>
> 1、form表单的请求方式必须为`post`，并且添加属性**`enctype="multipart/form-data" `**
>
> 2、handler方法中将上传的文件封装到`MultipartFile`对象中，通过此对象可以获取文件相关信息

**文件提交表单**

> 单文件上传，直接将type设为file即可。多文件上传时，需要加上`multiple`参数

```html
<form role="form" th:action="@{/file/upload}" method="post" enctype="multipart/form-data">
    <div class="form-group">
        <label for="exampleInputEmail1">邮箱</label>
        <input type="email" name="email" class="form-control" id="exampleInputEmail1" placeholder="Enter email">
    </div>
    <div class="form-group">
        <label for="exampleInputPassword1">姓名</label>
        <input type="text" name="username" class="form-control" id="exampleInputPassword1" placeholder="Password">
    </div>
    <div class="form-group">
        <label for="exampleInputFile">头像</label>
        <input type="file" name="actor" id="exampleInputFile">
        <p class="help-block">Example block-level help text here.</p>
    </div>
    <div class="form-group">
        <label for="exampleInputFile">照片</label>
        <input type="file" name="photos" id="exampleInputFile1" multiple>
        <p class="help-block">Example block-level help text here.</p>
    </div>
    <div class="checkbox">
        <label>
            <input type="checkbox"> 提交
        </label>
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```



**handler方法**

```java
@RequestMapping("/file/upload")
public String fileUpload(@RequestParam("email") String email,
                         @RequestParam("username") String username,
                         @RequestPart("actor") MultipartFile actor,
                         @RequestPart("photos") MultipartFile[] photos) throws IOException {
    log.info("email = {}", email);
    log.info("username = {}", username);
    log.info("actor size = {}", actor.getSize());
    log.info("photos length = {}", photos.length);

    if (!actor.isEmpty()) {
        String filename = actor.getOriginalFilename();
        actor.transferTo(new File("E:\\" + filename));
    }

    for (MultipartFile photo : photos) {
        String filename = photo.getOriginalFilename();
        photo.transferTo(new File("E:\\" + filename));
    }

    return "main";
}
```





> SpringMVC中我们需要在容器中配置文件上传解析器，SpringBoot已经帮我们自动配置好了，并将其配置与`MultipartProperties.class`相关联

```java
@AutoConfiguration
@ConditionalOnClass({Servlet.class, StandardServletMultipartResolver.class, MultipartConfigElement.class})
@ConditionalOnProperty(
    prefix = "spring.servlet.multipart",
    name = {"enabled"},
    matchIfMissing = true
)
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@EnableConfigurationProperties({MultipartProperties.class})
public class MultipartAutoConfiguration {
```



> 在`MultipartProperties`中，将其属性与application.yml配置文件相关联，在配置文件中可以修改其默认属性

```java
@ConfigurationProperties(
    prefix = "spring.servlet.multipart",
    ignoreUnknownFields = false
)
public class MultipartProperties {
    private boolean enabled = true;
    private String location;
    private DataSize maxFileSize = DataSize.ofMegabytes(1L);
    private DataSize maxRequestSize = DataSize.ofMegabytes(10L);
    private DataSize fileSizeThreshold = DataSize.ofBytes(0L);
    private boolean resolveLazily = false;
```



> 默认配置的文件上传解析器，单个文件大小限制为1MB，多文件时最大上传的大小为10MB，可以在application.yml文件修改他们

```yml
spring:
  servlet:
    multipart:
      max-file-size: 2MB
      max-request-size: 20MB
```





#### 文件上传原理

> 在`doDispatch`方法中，首先会判断当前请求是否为一个文件上传请求

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;  // 先拿到原生request
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;  // 标识当前请求是否为文件上传请求
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
        try {
            ModelAndView mv = null;
            Object dispatchException = null;

            try {
                // 检查当前request请求是否为文件上传请求
                processedRequest = this.checkMultipart(request);
                // 如果是文件上传请求，那么processedRequest!=request，multipartRequestParsed就为true
                multipartRequestParsed = processedRequest != request;
                mappedHandler = this.getHandler(processedRequest);
                if (mappedHandler == null) {
                    this.noHandlerFound(processedRequest, response);
                    return;
                }
```



```java
protected HttpServletRequest checkMultipart(HttpServletRequest request) throws MultipartException {
    // 判断文件上传解析器是否存在，并且文件上传解析器能否解析当前请求
    if (this.multipartResolver != null && this.multipartResolver.isMultipart(request)) {
        if (WebUtils.getNativeRequest(request, MultipartHttpServletRequest.class) != null) {
            if (DispatcherType.REQUEST.equals(request.getDispatcherType())) {
                this.logger.trace("Request already resolved to MultipartHttpServletRequest, e.g. by MultipartFilter");
            }
        } else if (this.hasMultipartException(request)) {
            this.logger.debug("Multipart resolution previously failed for current request - skipping re-resolution for undisturbed error rendering");
        } else {
            try {
                // 用文件上传解析器解析当前request请求
                return this.multipartResolver.resolveMultipart(request);
            } catch (MultipartException var3) {
                if (request.getAttribute("javax.servlet.error.exception") == null) {
                    throw var3;
                }
            }

            this.logger.debug("Multipart resolution failed for error dispatch", var3);
        }
    }

    return request;
}
```

```java
public boolean isMultipart(HttpServletRequest request) {
    return StringUtils.startsWithIgnoreCase(request.getContentType(), this.strictServletCompliance ? "multipart/form-data" : "multipart/");
}
```

```java
public MultipartHttpServletRequest resolveMultipart(HttpServletRequest request) throws MultipartException {
    // 将当前request请求封装为一个StandardMultipartHttpServletRequest，里边携带了文件数据
    return new StandardMultipartHttpServletRequest(request, this.resolveLazily);
}
```



> 如果是文件上传请求，最终返回的`processedRequest`就是封装了request的一个请求，里边携带了文件数据，key是参数名，value就是文件数据
>
> 然后执行handler方法时，用解析文件的参数解析器直接通过key去取值







### 2.13 错误处理

- 默认情况下，Spring Boot提供`/error`处理所有错误的映射

- 机器客户端，它将生成JSON响应，其中包含错误，HTTP状态和异常消息的详细信息。

```json
{
    "timestamp": "2023-09-01T08:44:12.543+00:00",
    "status": 404,
    "error": "Not Found",
    "message": "No message available",
    "path": "/madwadawd"
}
```

- 对于浏览器客户端，响应一个“ whitelabel”错误视图，以HTML格式呈现相同的数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309011645654.png)

- `/templates/error/`下的4xx，5xx页面会被自动解析





#### 底层组件功能分析

> 在`ErrorMvcAutoConfiguration`类中定义了异常的处理规则
>
> 容器中的组件如下



##### **DefaultErrorAttributes**

```java
@Bean
@ConditionalOnMissingBean(
    value = {ErrorAttributes.class},
    search = SearchStrategy.CURRENT
)
public DefaultErrorAttributes errorAttributes() {
    return new DefaultErrorAttributes();
}
```



> 这个组件定义了错误页面中可以包含的数据（异常明细，堆栈信息等）

```java
@Order(-2147483648)
public class DefaultErrorAttributes implements ErrorAttributes, HandlerExceptionResolver, Ordered {
    private static final String ERROR_ATTRIBUTE = DefaultErrorAttributes.class.getName() + ".ERROR";
    private final Boolean includeException;
	
    ...

    private void storeErrorAttributes(HttpServletRequest request, Exception ex) {
        request.setAttribute(ERROR_ATTRIBUTE, ex);
    }

    public Map<String, Object> getErrorAttributes(WebRequest webRequest, ErrorAttributeOptions options) {
        Map<String, Object> errorAttributes = this.getErrorAttributes(webRequest, options.isIncluded(Include.STACK_TRACE));
        if (Boolean.TRUE.equals(this.includeException)) {
            options = options.including(new Include[]{Include.EXCEPTION});
        }

        if (!options.isIncluded(Include.EXCEPTION)) {
            errorAttributes.remove("exception");
        }

        if (!options.isIncluded(Include.STACK_TRACE)) {
            errorAttributes.remove("trace");
        }

        if (!options.isIncluded(Include.MESSAGE) && errorAttributes.get("message") != null) {
            errorAttributes.put("message", "");
        }

        if (!options.isIncluded(Include.BINDING_ERRORS)) {
            errorAttributes.remove("errors");
        }

        return errorAttributes;
    }

    /** @deprecated */
    @Deprecated
    public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
        Map<String, Object> errorAttributes = new LinkedHashMap();
        errorAttributes.put("timestamp", new Date());
        this.addStatus(errorAttributes, webRequest);
        this.addErrorDetails(errorAttributes, webRequest, includeStackTrace);
        this.addPath(errorAttributes, webRequest);
        return errorAttributes;
    }
    ...
```





##### **BasicErrorController**

> **处理默认 `/error` 路径的请求**，页面响应 `new ModelAndView("error", model);`
>
> - 容器中有组件 `View`->id是error；（响应默认错误页）
> - 容器中有组件 `BeanNameViewResolver`（视图解析器）；按照返回的视图名作为组件的id去容器中找`View`对象。

```java
@Bean
@ConditionalOnMissingBean(
    value = {ErrorController.class},
    search = SearchStrategy.CURRENT
)
public BasicErrorController basicErrorController(ErrorAttributes errorAttributes, ObjectProvider<ErrorViewResolver> errorViewResolvers) {
    return new BasicErrorController(errorAttributes, this.serverProperties.getError(), (List)errorViewResolvers.orderedStream().collect(Collectors.toList()));
}
```



> `BasicErrorController`是一个Controller组件，默认情况下，发送`/error`请求，会执行这个方法，如果没有配置`server.error.path`，也没有配置`error.path`，就是`/error`

```java
@Controller
@RequestMapping({"${server.error.path:${error.path:/error}}"})
public class BasicErrorController extends AbstractErrorController {
    private final ErrorProperties errorProperties;
    
	...

    // 这个方法处理html的错误页请求
    @RequestMapping(
        produces = {"text/html"}
    )
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = this.getStatus(request);
        Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        // 用错误解析器解析该异常
        ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
        // 最终返回的是一个ModelAndView
        return modelAndView != null ? modelAndView : new ModelAndView("error", model);
    }

    // 这个方法是以json数据方式返回错误信息
    @RequestMapping
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        HttpStatus status = this.getStatus(request);
        if (status == HttpStatus.NO_CONTENT) {
            return new ResponseEntity(status);
        } else {
            Map<String, Object> body = this.getErrorAttributes(request, this.getErrorAttributeOptions(request, MediaType.ALL));
            return new ResponseEntity(body, status);
        }
    }

    ...
}
```



发生错误后，找不到可以处理当前异常的异常解析器后，selrvlet会向`/error`发送请求，`BasicErrorController`会拦截该请求，然后使用异常解析器来解析当前异常，默认情况下，使用`DefaultErrorViewResolver`来解析，解析不了就返回一个`ModelAndView`对象，其中视图名为`error`，model数据为异常信息，在进行视图渲染时，需要视图解析器来解析。在`ErrorMvcAutoConfiguration`类中，配置了的`BeanNameViewResolver`，会去解析该视图，并返回一个默认的视图`defaultErrorView`

```java
@Conditional({ErrorMvcAutoConfiguration.ErrorTemplateMissingCondition.class})
protected static class WhitelabelErrorViewConfiguration {
    private final ErrorMvcAutoConfiguration.StaticView defaultErrorView = new ErrorMvcAutoConfiguration.StaticView();

    protected WhitelabelErrorViewConfiguration() {
    }

    @Bean(
        name = {"error"}
    )
    @ConditionalOnMissingBean(
        name = {"error"}
    )
    public View defaultErrorView() {
        return this.defaultErrorView;
    }

    @Bean
    @ConditionalOnMissingBean
    public BeanNameViewResolver beanNameViewResolver() {
        BeanNameViewResolver resolver = new BeanNameViewResolver();
        resolver.setOrder(2147483637);
        return resolver;
    }
}
```



返回的`defaultErrorView`就是`StaticView`这个视图对象，他解析视图是以render方法，可以看到他返回的数据就是一个白页

```JAVA
private static class StaticView implements View {
    private static final MediaType TEXT_HTML_UTF8;
    private static final Log logger;

    private StaticView() {
    }

    public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
        if (response.isCommitted()) {
            String message = this.getMessage(model);
            logger.error(message);
        } else {
            response.setContentType(TEXT_HTML_UTF8.toString());
            StringBuilder builder = new StringBuilder();
            Object timestamp = model.get("timestamp");
            Object message = model.get("message");
            Object trace = model.get("trace");
            if (response.getContentType() == null) {
                response.setContentType(this.getContentType());
            }

            builder.append("<html><body><h1>Whitelabel Error Page</h1>").append("<p>This application has no explicit mapping for /error, so you are seeing this as a fallback.</p>").append("<div id='created'>").append(timestamp).append("</div>").append("<div>There was an unexpected error (type=").append(this.htmlEscape(model.get("error"))).append(", status=").append(this.htmlEscape(model.get("status"))).append(").</div>");
            if (message != null) {
                builder.append("<div>").append(this.htmlEscape(message)).append("</div>");
            }

            if (trace != null) {
                builder.append("<div style='white-space:pre-wrap;'>").append(this.htmlEscape(trace)).append("</div>");
            }

            builder.append("</body></html>");
            response.getWriter().append(builder.toString());
        }
    }
```







##### **DefaultErrorViewResolver**

> **如果发生异常错误，会以HTTP的状态码 作为视图页地址（viewName），找到真正的页面**
>
> - 先在静态资源路径下找 error/404.html、error/500.html等页面，找不到再去找error/4xx、error/5xx.html页面。如果我们在静态资源路径下的error文件夹下放了我们的错误处理页面，那么返回的就是我们设置的页面
> - 都找不到时，返回的ModelAndView中，视图名就是error，model数据就是异常信息，该视图默认情况下是一个白页。

```java
public class DefaultErrorViewResolver implements ErrorViewResolver, Ordered {
    private static final Map<Series, String> SERIES_VIEWS;
    private ApplicationContext applicationContext;
    private final Resources resources;
    private final TemplateAvailabilityProviders templateAvailabilityProviders;
    private int order = 2147483647;

    public DefaultErrorViewResolver(ApplicationContext applicationContext, Resources resources) {
        Assert.notNull(applicationContext, "ApplicationContext must not be null");
        Assert.notNull(resources, "Resources must not be null");
        this.applicationContext = applicationContext;
        this.resources = resources;
        this.templateAvailabilityProviders = new TemplateAvailabilityProviders(applicationContext);
    }

    DefaultErrorViewResolver(ApplicationContext applicationContext, Resources resourceProperties, TemplateAvailabilityProviders templateAvailabilityProviders) {
        Assert.notNull(applicationContext, "ApplicationContext must not be null");
        Assert.notNull(resourceProperties, "Resources must not be null");
        this.applicationContext = applicationContext;
        this.resources = resourceProperties;
        this.templateAvailabilityProviders = templateAvailabilityProviders;
    }

    // 解析错误视图
    public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
        // 调用reslove方法，String.valueOf(status.value())是当前请求的响应状态码，例如404,500
        ModelAndView modelAndView = this.resolve(String.valueOf(status.value()), model);
        if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
            // 调用reslove方法，(String)SERIES_VIEWS.get(status.series())是4xx,5xx等
            modelAndView = this.resolve((String)SERIES_VIEWS.get(status.series()), model);
        }
		// 返回一个modelAndView
        return modelAndView;
    }

    private ModelAndView resolve(String viewName, Map<String, Object> model) {
        // errorViewName就是"error/"拼接上状态码
        String errorViewName = "error/" + viewName;
        TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName, this.applicationContext);
        return provider != null ? new ModelAndView(errorViewName, model) : this.resolveResource(errorViewName, model);
    }
```





#### 异常处理流程

- 执行handler方法产生异常后，返回的mv为null
- 捕获到异常后，进行视图渲染，因为存在异常，所以用异常解析器去解析当前异常。默认情况下，第一个会将异常信息存储到request中，然后返回null，其他的也都无法解析
- 默认情况下，没有一个异常解析器可以解析当前异常，servlet就会发送一个`/error`请求
- 这个请求最终会被`BasicErrorController`处理，因为是浏览器发送请求，他就执行对应方法，先拿到相应信息。然后通过异常解析器解析当前异常，默认情况下只有`DefaultErrorViewResolver`，他会先去静态资源路径下找`error/404.html`、`error/500.html`等页面。找不到就去找`error/4xx.html`、`error/5xx.html`页面。如果都找不到，`BasicErrorController`最终会返回一个ModelAndView，视图名为error，model数据为异常信息

- 执行完`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`拿到了ModelAndView，视图名为error，model数据为异常信息

- 执行`this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);`进行视图渲染。通过视图解析器来解析视图，最后被`BeanNameViewResolver`解析，返回的视图就是它默认的视图`StaticView`，这个视图是`ErrorMvcAutoConfiguration`类下的`StaticView`，即返回的是一个白页



**handler方法**

```java
@RequestMapping("/dynamic_table")
public String dynamic_table(Model model){
    int a = 10 / 0;
    List<User> list = new ArrayList<>();
    list.add(new User("xrj", "123"));
    list.add(new User("xyy", "111"));
    list.add(new User("zs", "222"));
    list.add(new User("ls", "333"));
    model.addAttribute("users", list);
    return "table/dynamic_table";
}
```



1、`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`通过这行代码执行目标handler方法，首先获取参数，然后通过`invokeForRequest`方法，调用`return this.doInvoke(args);`通过反射来执行。

最终执行到`dynamic_table`方法后，产生异常，然后判断该异常为运行时异常，直接抛出

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202309031501430.png" style="zoom:75%;" />



2、因为执行目标方法时，产生了异常，然后通过`throw`抛出一个运行时异常，在`doDispatch`方法中被捕获到

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
                mappedHandler = this.getHandler(processedRequest);
                if (mappedHandler == null) {
                    this.noHandlerFound(processedRequest, response);
                    return;
                }

                HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
                String method = request.getMethod();
                boolean isGet = HttpMethod.GET.matches(method);
                if (isGet || HttpMethod.HEAD.matches(method)) {
                    long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                    if ((new ServletWebRequest(request, response)).checkNotModified(lastModified) && isGet) {
                        return;
                    }
                }

                if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                    return;
                }

                // 执行目标方法产生异常，产生异常后，这里返回的mv就是null，直接进入catch块
                mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                if (asyncManager.isConcurrentHandlingStarted()) {
                    return;
                }

                this.applyDefaultViewName(processedRequest, mv);
                mappedHandler.applyPostHandle(processedRequest, response, mv);
            } catch (Exception var20) {
                // 捕获到异常，这里的异常是java.lang.ArithmeticException: / by zero
                dispatchException = var20;
            } catch (Throwable var21) {
                dispatchException = new NestedServletException("Handler dispatch failed", var21);
            }
			
            // 捕获到异常后，继续执行这行代码，进行视图渲染，这里传入的mv为null，(Exception)dispatchException为数学运算异常
            // 默认情况下，没有异常解析器能够处理，因此也会抛出异常，被下边的catch捕获到
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



3、进入`processDispatchResult`方法进行视图渲染

```java
private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv, @Nullable Exception exception) throws Exception {
    boolean errorView = false;
    // 此时这里的异常不为空
    if (exception != null) {
        // 判断是否是ModelAndView定义异常
        if (exception instanceof ModelAndViewDefiningException) {
            this.logger.debug("ModelAndViewDefiningException encountered", exception);
            mv = ((ModelAndViewDefiningException)exception).getModelAndView();
        // 最终会进入else
        } else {
            // handler就是目标方法
            Object handler = mappedHandler != null ? mappedHandler.getHandler() : null;
            // 通过原生request、response、handler方法和异常对象执行该方法
            mv = this.processHandlerException(request, response, handler, exception);
            errorView = mv != null;
        }
    }

    if (mv != null && !mv.wasCleared()) {
        this.render(mv, request, response);
        if (errorView) {
            WebUtils.clearErrorRequestAttributes(request);
        }
    } else if (this.logger.isTraceEnabled()) {
        this.logger.trace("No view rendering, null ModelAndView returned.");
    }

    if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
        if (mappedHandler != null) {
            mappedHandler.triggerAfterCompletion(request, response, (Exception)null);
        }

    }
}
```



4、

```java
@Nullable
protected ModelAndView processHandlerException(HttpServletRequest request, HttpServletResponse response, @Nullable Object handler, Exception ex) throws Exception {
    request.removeAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE);
    ModelAndView exMv = null;
    if (this.handlerExceptionResolvers != null) {
        // 获取到所有的异常解析器
        Iterator var6 = this.handlerExceptionResolvers.iterator();

        while(var6.hasNext()) {
            HandlerExceptionResolver resolver = (HandlerExceptionResolver)var6.next();
            // 遍历每一个异常解析器，看哪个异常解析器可以处理当前异常，默认情况下，都无法处理，这里返回null
            exMv = resolver.resolveException(request, response, handler, ex);
            if (exMv != null) {
                break;
            }
        }
    }

    if (exMv != null) {
        if (exMv.isEmpty()) {
            request.setAttribute(EXCEPTION_ATTRIBUTE, ex);
            return null;
        } else {
            if (!exMv.hasView()) {
                String defaultViewName = this.getDefaultViewName(request);
                if (defaultViewName != null) {
                    exMv.setViewName(defaultViewName);
                }
            }

            if (this.logger.isTraceEnabled()) {
                this.logger.trace("Using resolved error view: " + exMv, ex);
            } else if (this.logger.isDebugEnabled()) {
                this.logger.debug("Using resolved error view: " + exMv);
            }

            WebUtils.exposeErrorRequestAttributes(request, ex, this.getServletName());
            return exMv;
        }
    } else { 
        // 默认情况下，所有的异常解析器都无法处理，这里就会抛出异常 java.lang.ArithmeticException: / by zero
        throw ex;
    }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309031509903.png)

> 1、这里第一个异常解析器是`DefaultErrorAttributes`，它是`ErrorMvcAutoConfiguration`自动配好的组件。他的功能是将异常信息保存到`request`域中，直接返回null，因此返回得到的`exMV`属性为null，会继续使用下一个异常解析器来解析
>
> ```java
> public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex{
>        this.storeErrorAttributes(request, ex);
>        return null;
> }
>                                      
> private void storeErrorAttributes(HttpServletRequest request, Exception ex) {
>     	request.setAttribute(ERROR_INTERNAL_ATTRIBUTE, ex);
> }                                     
> ```
>
> 2、第二个异常解析器是3个异常解析器的组合，这3个异常解析器也无法处理，因此最终返回为null





5、因为没有任何一个异常解析器可以处理当前异常，所以servlet底层就会发送一个`/error`的请求，再次执行到`doDispatch`方法，通过`mappedHandler = this.getHandler(processedRequest);`找到目标handler，最终找到`BasicErrorController`，这个组件就是`ErrorMvcAutoConfiguration`为我们配置好的，然后执行`BasicErrorController`中的方法，由于我们现在是浏览器发送的请求，因此最终执行如下方法

```java
@RequestMapping(
    produces = {"text/html"}
)
public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
    // 响应状态码，现在是服务器内部错误，所以是500对应的类对象
    HttpStatus status = this.getStatus(request);
    // model拿到所有的异常信息
    Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
    response.setStatus(status.value());
    // 然后执行这行代码，进行异常视图解析
    // 默认情况下，最终返回null
    ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
    // 最后返回一个ModelAndView，视图名为error，model为异常信息
    return modelAndView != null ? modelAndView : new ModelAndView("error", model);
}
```

```java
protected ModelAndView resolveErrorView(HttpServletRequest request, HttpServletResponse response, HttpStatus status, Map<String, Object> model) {
    // 获取所有的异常视图解析器，默认情况下只有一个DefaultErrorViewResolver
    Iterator var5 = this.errorViewResolvers.iterator();

    ModelAndView modelAndView;
    do {
        if (!var5.hasNext()) {
            return null;
        }

        ErrorViewResolver resolver = (ErrorViewResolver)var5.next();
        // 用当前的异常解析器来处理，model是异常信息
        modelAndView = resolver.resolveErrorView(request, status, model);
    } while(modelAndView == null);

    // 默认情况下，最终返回null
    return modelAndView;
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309031523420.png)

```java
public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
    // 通过resolve方法进行解析，这里因为是服务器内部错误，所以String.valueOf(status.value())的值就是500，model保存的是异常信息
    // 第一次执行resolve方法执行完后，这里返回null
    ModelAndView modelAndView = this.resolve(String.valueOf(status.value()), model);
    // SERIES_VIEWS的值为4xx、5xx，所以包含status.series()
    if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
        // (String)SERIES_VIEWS.get(status.series())返回的是5xx，然后第二次执行resolve方法
        modelAndView = this.resolve((String)SERIES_VIEWS.get(status.series()), model);
    }

    // 因为我们没有配置具体错误页面，也没配置4xx、5xx页面，所以这里返回null
    return modelAndView;
}

private ModelAndView resolve(String viewName, Map<String, Object> model) {
    // 第一次进来错误视图名就是"error/500"
    // 第二次进来错误视图名为"error/5xx"
    String errorViewName = "error/" + viewName;
    // 判断该视图模板是否可用，第一次这里返回的provider为null，第二次也为null
    TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName, this.applicationContext);
    // 因为provider为null，所以执行this.resolveResource(errorViewName, model)
    // 两次resolveResource方法最终返回null
    return provider != null ? new ModelAndView(errorViewName, model) : this.resolveResource(errorViewName, model);
}
```

```java
private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
    /*
    	var3的值为
    	classpath:/META-INF/resources/
    	classpath:/resources/
    	classpath:/static/
    	classpath:/public/
    */
    String[] var3 = this.resources.getStaticLocations();
    int var4 = var3.length;

    for(int var5 = 0; var5 < var4; ++var5) {
        // location为遍历的每个var3的值，表示去默认的四个静态资源路径下找当前的静态资源，因此我们将错误页面放在静态资源下的error的文件夹中就会被找到
        String location = var3[var5];  

        try {
            Resource resource = this.applicationContext.getResource(location);
            // 将viewName拼接上.html后缀
            // 第一次找的是"error/500.html"
            // 第二次找的是"error/5xx.html"
            resource = resource.createRelative(viewName + ".html");
            // 判断该资源是否存在
            if (resource.exists()) {
                return new ModelAndView(new DefaultErrorViewResolver.HtmlResourceView(resource), model);
            }
        } catch (Exception var8) {
        }
    }
	// 由于我们目前没有配置任何错误页面，所以返回null
    return null;
}
```





第二次发送的`/error`请求，最终执行完`mv = ha.handle(processedRequest, response, mappedHandler.getHandler());`返回的mv，视图名是`error`，model是异常对象。然后执行`processDispatchResult`方法，此时没有异常，直接进入render方法进行视图渲染

```java
protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
    Locale locale = this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale();
    response.setLocale(locale);
    // 当前视图名为error
    String viewName = mv.getViewName();
    View view;
    if (viewName != null) {
        // mv.getModelInternal()拿到的是model数据，即异常信息
        // 通过视图解析器来解析视图，最后被BeanNameViewResolver解析，返回的视图就是它默认的视图StaticView
        // 这个视图是ErrorMvcAutoConfiguration类下的StaticView，即返回的是一个白页
        view = this.resolveViewName(viewName, mv.getModelInternal(), locale, request);
        if (view == null) {
            throw new ServletException("Could not resolve view with name '" + mv.getViewName() + "' in servlet with name '" + this.getServletName() + "'");
        }
    } else {
        view = mv.getView();
        if (view == null) {
            throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a View object in servlet with name '" + this.getServletName() + "'");
        }
    }

    if (this.logger.isTraceEnabled()) {
        this.logger.trace("Rendering view [" + view + "] ");
    }

    try {
        if (mv.getStatus() != null) {
            request.setAttribute(View.RESPONSE_STATUS_ATTRIBUTE, mv.getStatus());
            response.setStatus(mv.getStatus().value());
        }
	
        // 当前的视图就是ErrorMvcAutoConfiguration类下的StaticView，执行其render方法
        view.render(mv.getModelInternal(), request, response);
    } catch (Exception var8) {
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Error rendering view [" + view + "]", var8);
        }

        throw var8;
    }
}
```

```java
private static class StaticView implements View {
    private static final MediaType TEXT_HTML_UTF8;
    private static final Log logger;

    private StaticView() {
    }

    // 这个render方法，就是返回一个白页
    public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
        if (response.isCommitted()) {
            String message = this.getMessage(model);
            logger.error(message);
        } else {
            response.setContentType(TEXT_HTML_UTF8.toString());
            StringBuilder builder = new StringBuilder();
            Object timestamp = model.get("timestamp");
            Object message = model.get("message");
            Object trace = model.get("trace");
            if (response.getContentType() == null) {
                response.setContentType(this.getContentType());
            }

            builder.append("<html><body><h1>Whitelabel Error Page</h1>").append("<p>This application has no explicit mapping for /error, so you are seeing this as a fallback.</p>").append("<div id='created'>").append(timestamp).append("</div>").append("<div>There was an unexpected error (type=").append(this.htmlEscape(model.get("error"))).append(", status=").append(this.htmlEscape(model.get("status"))).append(").</div>");
            if (message != null) {
                builder.append("<div>").append(this.htmlEscape(message)).append("</div>");
            }

            if (trace != null) {
                builder.append("<div style='white-space:pre-wrap;'>").append(this.htmlEscape(trace)).append("</div>");
            }

            builder.append("</body></html>");
            response.getWriter().append(builder.toString());
        }
    }
```









#### 处理异常的方法

**1、自定义错误页**

> 产生异常，异常解析器都无法处理。servlet发一个/error请求，然后跳转到这个错误页

`error/404.html `、 `error/5xx.html`。有精确的错误状态码页面就匹配精确，没有就找 `4xx.html`，如果都没有就触发白页





**2、`@ControllerAdvice`+`@ExceptionHandler`**

> 默认的异常处理解析器有两个，第二个是三个异常解析器的组合。其中`ExceptionHandlerExceptionResolver`是使用带了`@ExceptionHandler`注解的方法来处理异常，因此只要我们自定义了异常处理的方法，产生异常后，通过异常解析器就可以找到处理该异常的方法

```java
@ControllerAdvice
public class GlobalException {
    @ExceptionHandler({ArithmeticException.class, ClassNotFoundException.class})
    public String handleException(Exception e, Model model) {
        model.addAttribute("ex", e);
        System.out.println(e);
        return "error/5xx";
    }
}
```





**3、`@ResponseStatus`+自定义异常**

> 使用`@ResponseStatus`标注了一个自定义的异常后，我们在handler方法中抛出了这个异常。
>
> 使用异常解析器解析时，其中`ResponseStatusExceptionResolver`可以处理带`@ResponseStatus`注解的异常。
>
> 这个异常处理解析器，会拿到注解位置的`code`和`reason`信息，然后执行`response.sendError()`方法，表示当前请求结束。然后返回的是空的ModelAndView，servlet底层还会继续发送`/error`请求
>
> 异常解析器会返回一个空的`ModelAndView`对象，以指示异常已经被处理。但仍然需要一个具体的视图来展示错误信息给用户。为了实现这一点，通常会触发一个新的请求，通常是`/error`请求，用于渲染一个错误页面或者返回适当的错误响应。
>
> ```java
> protected ModelAndView applyStatusAndReason(int statusCode, @Nullable String reason, HttpServletResponse response) throws IOException {
>  if (!StringUtils.hasLength(reason)) {
>      	response.sendError(statusCode);
>  } else {
>      	String resolvedReason = this.messageSource != null ? this.messageSource.getMessage(reason, (Object[])null, reason, 	LocaleContextHolder.getLocale()) : reason;
>      	response.sendError(statusCode, resolvedReason);
>  }
> 
>  return new ModelAndView();
> }
> ```



**exception**

```java
@ResponseStatus(value= HttpStatus.FORBIDDEN, reason = "用户数量太多")
public class UserTooManyException extends RuntimeException {

    public  UserTooManyException(){}

    public  UserTooManyException(String message){
        super(message);
    }
}
```

**handler方法**

```java
@RequestMapping("/dynamic_table")
public String dynamic_table(Model model){
    List<User> list = new ArrayList<>();
    list.add(new User("xrj", "123"));
    list.add(new User("xyy", "111"));
    list.add(new User("zs", "222"));
    list.add(new User("ls", "333"));
    if (list.size() > 3)
        throw new UserTooManyException("我自己的异常");
    model.addAttribute("users", list);
    return "table/dynamic_table";
}
```





**4、Spring底层异常**

> 1、Spring底层异常如 ` org.springframework.web.bind.MissingServletRequestParameterException`【缺少参数】等，是由异常解析器中的`DefaultHandlerExceptionResolver` 处理的。
>
> ```java
> protected ModelAndView handleMissingServletRequestParameter(MissingServletRequestParameterException ex, HttpServletRequest request, HttpServletResponse response, @Nullable Object handler) throws IOException {
>     response.sendError(400, ex.getMessage());
>     return new ModelAndView();
> }
> ```
>
> 2、最终也是执行`response.sendError`方法，返回返回一个空的ModelAndView，最后servlet会发送一个`/error`请求







**5、自定义实现 `HandlerExceptionResolver` 处理异常**

**自定义异常处理器**

> 使用`@Component`将这个异常处理器组件放入ioc容器

```java
@Order(value= Ordered.HIGHEST_PRECEDENCE)  //优先级，数字越小优先级越高
@Component
public class CustomerHandlerExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response,
                                         Object handler, Exception ex) {

        try {
            response.sendError(511,"我喜欢的错误");
        } catch (IOException e) {
            e.printStackTrace();
        }
        return new ModelAndView();
    }
}
```



> 或者在一个配置类中，将该异常解析器作为一个Bean配置进去

```java
@Order(value= Ordered.HIGHEST_PRECEDENCE)  //优先级，数字越小优先级越高
public class CustomerHandlerExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response,
                                         Object handler, Exception ex) {

        try {
            response.sendError(511,"我喜欢的错误");
        } catch (IOException e) {
            e.printStackTrace();
        }
        return new ModelAndView();
    }
}

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/", "/login", "/css/**", "/fonts/**", "/images/**", "/js/**");
    }

    @Bean
    public CustomerHandlerExceptionResolver myException(){
        return new CustomerHandlerExceptionResolver();
    }
}
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309031851652.png)



> 我们可以自定义异常处理器，并将其放入ioc容器，这样产生异常时，如果默认的处理不了，可以使用我们自定义的异常处理器来处理，`@Order`是设置其优先级的。
>
> 这里我们也是使用`response.sendError(511,"我喜欢的错误");`发送了异常的状态码和异常信息，然后返回一个空的ModelAndView，最后servlet发送`/error`请求。









### 2.14 原生组件注入

> 1、java web中三大组件为：servlet、filter、listener。在SpringBoot中，我们也可以自己写这些组件。
>
> 2、SpringMVC中，所有请求都会被`DispatcherServlet`处理，但是我们也可以自己写一个servlet处理请求。
>
> 3、多个Servlet都能处理到同一层路径，精确优选原则。例如我们访问`/my`请求，他会被`DispatcherServlet`拦截，同时也会被我们自己的servlet拦截，但是我们自己的servlet拦截路径更精确，所以会使用我们自己的servlet处理该请求，而拦截器是SpringMVC中的，是`DispatcherServlet`执行`doDispatch`方法时执行的。所以使用我们自己的servlet处理请求时，不会经过拦截器。



#### 使用原生servlet注解

> 使用原生servlet注入时，在对应组件上加上`@WebServlet`、`@WebFilter`、`@WebListener`注解，同时需要在SpringBoot的启动类上添加`@ServletComponentScan`注解，这样我们自己的servlet组件才能被扫描到

**servlet**

```java
@WebServlet("/my")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("hello, xrj");
    }
}
```



**filter**

```java
@WebFilter(urlPatterns = {"/my", "/css/*"})
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("filter初始化");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filter工作");
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {
        System.out.println("filter销毁");
    }
}
```



**listener**

```java
@WebListener
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("listener 初始化");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("listener 销毁");
    }
}
```



**启动类**

```java
@SpringBootApplication
@ServletComponentScan
public class Springboot04Web02Application {

    public static void main(String[] args) {
        SpringApplication.run(Springboot04Web02Application.class, args);
    }
}
```





#### 使用RegistrationBean

> 1、我们可以直接编写对应的serlvet、filter、listener，而不需要使用任何注解，而且启动类上也不需要添加`@ServletComponentScan`注解。然后在一个配置类中，用配置类的方式将对应组件添加进去。
>
> 2、servlet对应`ServletRegistrationBean`，filter对应`FilterRegistrationBean`，listener对应`ServletListenerRegistrationBean`



**servlet**

```java
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("hello, xrj");
    }
}
```



**filter**

```java
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("filter初始化");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filter工作");
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {
        System.out.println("filter销毁");
    }
}
```



**listener**

```java
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("listener 初始化");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("listener 销毁");
    }
}
```



**配置类**

```java
@Configuration
public class MyRegistConfig {
    @Bean
    public ServletRegistrationBean myServlet(){
        MyServlet myServlet = new MyServlet();
        return new ServletRegistrationBean(myServlet, "/my", "/css/*");
    }

    @Bean
    public FilterRegistrationBean myFilter(){
        MyFilter myFilter = new MyFilter();
        // 可以直接过滤servlet，也可以过滤对应urlPattern
        // return new FilterRegistrationBean(myFilter, myServlet());
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(myFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my", "/css/*"));
        return filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MyListener myListener = new MyListener();
        return new ServletListenerRegistrationBean(myListener);
    }
}
```





1、在`DispatcherServletAutoConfiguration`类中，SpringBoot会自动配置`DispatcherServlet`。也是通过`ServletRegistrationBean`的方式。

2、在`dispatcherServletRegistration`方法中，他的返回值是`DispatcherServletRegistrationBean `，这个类是继承自`ServletRegistrationBean`的。

3、该方法就是创建一个`DispatcherServletRegistrationBean`对象，然后将`DispatcherServlet`放进去，和我们自己配的方法是一样的

```java
@Configuration(
    proxyBeanMethods = false
)
@Conditional({DispatcherServletAutoConfiguration.DispatcherServletRegistrationCondition.class})
@ConditionalOnClass({ServletRegistration.class})
@EnableConfigurationProperties({WebMvcProperties.class})
@Import({DispatcherServletAutoConfiguration.DispatcherServletConfiguration.class})
protected static class DispatcherServletRegistrationConfiguration {
    protected DispatcherServletRegistrationConfiguration() {
    }

    @Bean(
        name = {"dispatcherServletRegistration"}
    )
    @ConditionalOnBean(
        value = {DispatcherServlet.class},
        name = {"dispatcherServlet"}
    )
    public DispatcherServletRegistrationBean dispatcherServletRegistration(DispatcherServlet dispatcherServlet, WebMvcProperties webMvcProperties, ObjectProvider<MultipartConfigElement> multipartConfig) {
        DispatcherServletRegistrationBean registration = new DispatcherServletRegistrationBean(dispatcherServlet, webMvcProperties.getServlet().getPath());
        registration.setName("dispatcherServlet");
        registration.setLoadOnStartup(webMvcProperties.getServlet().getLoadOnStartup());
        multipartConfig.ifAvailable(registration::setMultipartConfig);
        return registration;
    }
}
```

```java
public class DispatcherServletRegistrationBean extends ServletRegistrationBean<DispatcherServlet> implements DispatcherServletPath {
    private final String path;

    public DispatcherServletRegistrationBean(DispatcherServlet servlet, String path) {
        super(servlet, new String[0]);
        Assert.notNull(path, "Path must not be null");
        this.path = path;
        super.addUrlMappings(new String[]{this.getServletUrlMapping()});
    }

    public String getPath() {
        return this.path;
    }

    public void setUrlMappings(Collection<String> urlMappings) {
        throw new UnsupportedOperationException("URL Mapping cannot be changed on a DispatcherServlet registration");
    }

    public void addUrlMappings(String... urlMappings) {
        throw new UnsupportedOperationException("URL Mapping cannot be changed on a DispatcherServlet registration");
    }
}
```







### 2.15 嵌入式servlet容器

- 默认支持的WebServer

  - `Tomcat`, `Jetty`, or `Undertow`。
  - `ServletWebServerApplicationContext `容器启动寻找`ServletWebServerFactory` 并引导创建服务器。

- 原理

  - SpringBoot应用启动发现当前是Web应用，web场景包-导入tomcat。
  - web应用会创建一个web版的IOC容器 `ServletWebServerApplicationContext` 。
  - `ServletWebServerApplicationContext`  启动的时候寻找 `ServletWebServerFactory` （Servlet 的web服务器工厂——>Servlet 的web服务器）。
  - SpringBoot底层默认有很多的WebServer工厂（`ServletWebServerFactoryConfiguration`内创建Bean），如：
    - `TomcatServletWebServerFactory`
    - `JettyServletWebServerFactory`
    - `UndertowServletWebServerFactory`
  - 底层直接会有一个自动配置类`ServletWebServerFactoryAutoConfiguration`。
  - `ServletWebServerFactoryAutoConfiguration`导入了`ServletWebServerFactoryConfiguration`（配置类）。
  - `ServletWebServerFactoryConfiguration  `根据动态判断系统中到底导入了那个Web服务器的包。（默认是web-starter导入tomcat包），容器中就有 `TomcatServletWebServerFactory`
  - `TomcatServletWebServerFactory `创建出Tomcat服务器并启动；`TomcatWebServer` 的构造器拥有初始化方法initialize——`this.tomcat.start();`
  - 内嵌服务器，与以前手动把启动服务器相比，改成现在使用代码启动（tomcat核心jar包存在）。



Spring Boot默认使用Tomcat服务器，若需更改其他服务器，则修改工程pom.xml：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```





**定制servlet容器**

- 修改配置文件 `server.xxx`
- 实现`WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>` 
  - 把配置文件的值和`ServletWebServerFactory`进行绑定
- 直接自定义 `ConfigurableServletWebServerFactory`。
  - `xxxxCustomizer`：定制化器，可以改变xxxx的默认规则









### 2.16 SpringBoot定制化原理

**场景starter** - `xxxxAutoConfiguration` - 导入xxx组件 - 绑定`xxxProperties` - 绑定配置文件项 - **修改配置文件**



**定制化常见方式**

1、修改配置文件

2、编写自定义的配置类  `xxxConfiguration` + `@Bean`替换或增加容器中默认组件

3、Web应用 编写一个配置类实现 `WebMvcConfigurer` 即可定制化web功能 + `@Bean`给容器中再扩展一些组件

```java
@Configuration
public class WebConfig implements WebMvcConfigurer{
}
```

4、配置类上带有`@EnableWebMvc`注解，全面接管`SpringMVC`，即`SpringBoot`所有的自动配置全部失效，这就是在`SpringMVC`阶段配置的mvc注解驱动

- `@EnableWebMvc` + `WebMvcConfigurer` — `@Bean`  可以全面接管SpringMVC，所有规则全部自己重新配置； 实现定制和扩展功能
- 原理：
  - `WebMvcAutoConfiguration`是SpringMVC的自动配置类，会默认配置各种功能，如静态资源、欢迎页等。
    1. 一旦使用 `@EnableWebMvc` ，会`@Import(DelegatingWebMvcConfiguration.class)`。
    2. `DelegatingWebMvcConfiguration`的作用，只保证SpringMVC最基本的使用
       - 把所有系统中的`WebMvcConfigurer`拿过来，所有功能的定制都是这些`WebMvcConfigurer`合起来一起生效。
       - 自动配置了一些非常底层的组件，如`RequestMappingHandlerMapping`，这些组件依赖的组件都是从容器中获取。
       - `public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport`。
    3. `WebMvcAutoConfiguration`里面的配置要能生效必须  `@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)`，即没有`WebMvcConfigurationSupport`这个类才会生效，但是我们使用`@EnableWebMvc`后，他会将`DelegatingWebMvcConfiguration`导入进来，而这个类就继承了`WebMvcConfigurationSupport`，因此自动配置类失效
    4. `@EnableWebMvc` 导致了`WebMvcAutoConfiguration`  没有生效。







## 3、数据访问

### 3.1 数据库场景整合

**导入JDBC场景**

> 导入jdbc场景后，它会默认给我们导入一个数据源`HikariCP`，以及数据库事务相关的功能`spring-tx`等
>
> 因为SpringBoot不知道我们使用什么类型的数据库，所以数据库的驱动需要我们自己导入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.20</version>
</dependency>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309051154385.png)



**JDBC的自动配置**

> 当我们导入了JDBC的场景后，在spring-boot-autoconfigure中会有jdbc相关的自动配置



1、`DataSourceAutoConfiguration`

> 1、这个类的配置和`DataSourceProperties`绑定，在`DataSourceProperties`中设置了数据源的配置信息，他和配置文件绑定，以`spring.datasource`作为前缀
>
> 2、在这个类中，springboot检测到我们没有自己配置数据源，他就会帮我们配数据源。它`import`了各种数据源，然后检测当前导入了哪个数据源，因为SpringBoot默认帮我们导入了`Hikari`数据源，所以默认情况下，他会帮我们配置这个数据源，并且可以通过`spring.datasource.type`在配置文件中设置数据源类型

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({DataSource.class, EmbeddedDatabaseType.class})
@ConditionalOnMissingBean(
    type = {"io.r2dbc.spi.ConnectionFactory"}
)
@EnableConfigurationProperties({DataSourceProperties.class})
@Import({DataSourcePoolMetadataProvidersConfiguration.class, DataSourceInitializationConfiguration.class})
public class DataSourceAutoConfiguration {
    
    ...
       
    @Configuration(
        proxyBeanMethods = false
    )
    @Conditional({DataSourceAutoConfiguration.PooledDataSourceCondition.class})
    @ConditionalOnMissingBean({DataSource.class, XADataSource.class})
    @Import({Hikari.class, Tomcat.class, Dbcp2.class, Generic.class, DataSourceJmxConfiguration.class})
    protected static class PooledDataSourceConfiguration {
        protected PooledDataSourceConfiguration() {
        }
    }
```



2、`DataSourceTransactionManagerAutoConfiguration`

> 在`DataSourceTransactionManagerAutoConfiguration`中，他帮我们自动配置好了事务管理器，并将ioc容器中的数据源设置进去

```java
@Bean
@ConditionalOnMissingBean({PlatformTransactionManager.class})
DataSourceTransactionManager transactionManager(DataSource dataSource, ObjectProvider<TransactionManagerCustomizers> transactionManagerCustomizers) {
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager(dataSource);
    transactionManagerCustomizers.ifAvailable((customizers) -> {
        customizers.customize(transactionManager);
    });
    return transactionManager;
}
```



3、`JdbcTemplateAutoConfiguration`

> 默认情况下，springboot帮我们自动配置好了`JdbcTemplate`，并将其放入ioc容器中。
>
> 在配置文件中可以通过`spring.jdbc`前缀来修改其配置

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({DataSource.class, JdbcTemplate.class})
@ConditionalOnSingleCandidate(DataSource.class)
@AutoConfigureAfter({DataSourceAutoConfiguration.class})
@EnableConfigurationProperties({JdbcProperties.class})
@Import({JdbcTemplateConfiguration.class, NamedParameterJdbcTemplateConfiguration.class})
public class JdbcTemplateAutoConfiguration {
    public JdbcTemplateAutoConfiguration() {
    }
}
```

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnMissingBean({JdbcOperations.class})
class JdbcTemplateConfiguration {
    JdbcTemplateConfiguration() {
    }

    @Bean
    @Primary
    JdbcTemplate jdbcTemplate(DataSource dataSource, JdbcProperties properties) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        Template template = properties.getTemplate();
        jdbcTemplate.setFetchSize(template.getFetchSize());
        jdbcTemplate.setMaxRows(template.getMaxRows());
        if (template.getQueryTimeout() != null) {
            jdbcTemplate.setQueryTimeout((int)template.getQueryTimeout().getSeconds());
        }

        return jdbcTemplate;
    }
}
```





**修改配置**

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ssm?serverTimezone=Asia/Shanghai
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root
```

**测试**

```java
@Slf4j
@SpringBootTest
class Springboot04Web02ApplicationTests {

    @Autowired
    private JdbcTemplate jdbcTemplate;
    @Test
    void contextLoads() {
        Integer count = jdbcTemplate.queryForObject("select count(*) from t_book", Integer.class);
        log.info("记录总数: {}", count);
    }
}
```





### 3.2 整合Druid

> SprintBoot给我们提供的默认的数据源是`Hikari`，如果我们想要使用`Druid`数据源，有两种方式，一种是自定义放，另一种就是引入Druid的starter的方式



#### **自定义方式**

**1、引入对应依赖**

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.17</version>
</dependency>
```

**2、将数据源添加到ioc容器**

> 1、当我们在配置类中，将`Druid`添加到ioc容器中时，springboot默认配置的`Hikari`数据源就不会在配置了。因为在配置`Hikari`时，当前没有配数据源时，他才会给我们配`@ConditionalOnMissingBean({DataSource.class, XADataSource.class})`
>
> 2、数据源配好后，以后获取的连接就是从`Druid`获取了。
>
> 3、对于`Druid`的监控页等功能，原来是在web.xml配置，现在就在配置类中设置对应的Bean即可

```java
@Configuration
public class DataSourceConfig {

    @Bean
    @ConfigurationProperties("spring.datasource")  // 复用配置文件的数据源配置
    public DataSource druidSource(){
        return new DruidDataSource();
    }
}
```







#### **starter整合方式**

**1、添加依赖**

> 引入`Druid`的starter后，SpringBoot会为我们自动配置一系列组件。在`DruidDataSourceAutoConfigure`为我们配置好，因此引入starter后，数据源的信息直接使用我们配置文件中配置的即可，然后就可以直接使用`Druid`了。

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.17</version>
</dependency>
```



**2、Druid配置**

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ssm?serverTimezone=Asia/Shanghai
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root

    druid:
      aop-patterns: com.atguigu.admin.*  # 监控SpringBean
      filters: stat,wall     # 底层开启功能，stat（sql监控），wall（防火墙）

      stat-view-servlet: # 配置监控页功能
        enabled: true
        login-username: admin	# 监控页登录用户名
        login-password: admin	# 监控页登录用户名
        resetEnable: false	

      web-stat-filter: # 监控web
        enabled: true
        urlPattern: /*
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'

      filter:
        stat: # 对上面filters里面的stat的详细配置
          slow-sql-millis: 1000
          logSlowSql: true
          enabled: true
        wall:
          enabled: true
          config:
            drop-table-allow: false
```







**Druid的自动配置**

> 1、在引入`Druid`的starter后，SpringBoot会帮我们配置很多组件，`DruidDataSourceAutoConfigure`类表示`Druid`数据源的自动配置
>
> 2、`@EnableConfigurationProperties({DruidStatProperties.class, DataSourceProperties.class})`表示`Druid`的各种配置是根据这两个类实现的。其中`DruidStatProperties`和配置文件绑定，以`spring.datasource.druid`作为前缀，` DataSourceProperties`和配置文件绑定，以`spring.datasource`作为前缀
>
> 3、`DruidDataSourceAutoConfigure`中通过`import`引入了4个配置
>
> - `DruidSpringAopConfiguration.class`,  监控SpringBean。配置文件前缀：`spring.datasource.druid.aop-patterns`
> - `DruidStatViewServletConfiguration.class`, 监控页的配置。`spring.datasource.druid.stat-view-servlet`默认开启。
> - `DruidWebStatFilterConfiguration.class`，web监控配置。`spring.datasource.druid.web-stat-filter`默认开启。
> - `DruidFilterConfiguration.class`所有Druid的filter的配置：

```java
@Configuration
@ConditionalOnClass({DruidDataSource.class})
@AutoConfigureBefore({DataSourceAutoConfiguration.class})
@EnableConfigurationProperties({DruidStatProperties.class, DataSourceProperties.class})
@Import({DruidSpringAopConfiguration.class, DruidStatViewServletConfiguration.class, DruidWebStatFilterConfiguration.class, DruidFilterConfiguration.class})
public class DruidDataSourceAutoConfigure {
    private static final Logger LOGGER = LoggerFactory.getLogger(DruidDataSourceAutoConfigure.class);

    public DruidDataSourceAutoConfigure() {
    }

    @Bean(
        initMethod = "init"
    )
    @ConditionalOnMissingBean
    public DataSource dataSource() {
        LOGGER.info("Init DruidDataSource");
        return new DruidDataSourceWrapper();
    }
}
```







### 3.3 整合MyBatis

**1、导入MyBatis的starter**

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```



**2、配置MyBatis**

> 使用SpringBoot整合MyBatis，可以不用写MyBatis的全局配置文件，如果写了就在配置文件下设置，如果不写就在`mybatis.configuration`设置即可【推荐】

```yml
mybatis:
  type-aliases-package: com.xrj.pojo	# 设置别名
  # mapper-locations: com/xrj/mapper/*.xml  # 设置mapper文件的位置，如果mapper文件和mapper接口路径一样，那么就不用设置
  configuration:
    map-underscore-to-camel-case: true	# 开启驼峰命名
```



**3、编写mapper接口和mapper映射文件**

> 注意Mapper接口必须要有`@Mapper`注解

```java
@Mapper
public interface BookMapper {
    Book getBookById(Integer id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.xrj.mapper.BookMapper">
    <select id="getBookById" resultType="book">
        select * from t_book where book_id = #{id}
    </select>
</mapper>
```



**4、使用MyBatis**

```java
@Controller
public class BookController {
    @Autowired
    BookService bookService;

    @RequestMapping("/book")
    @ResponseBody
    public Book getBookById(Integer id) {
        return bookService.getBookById(id);
    }
}

@Service
public class BookService {
    @Autowired
    BookMapper bookMapper;
    public Book getBookById(Integer id) {
        return bookMapper.getBookById(id);
    }
}
```







**自动配置原理**

> 当我们导入了`MyBatis`的Starter后，在`MybatisAutoConfiguration`中会为我们配置各种组件，其中配置参数与`MybatisProperties`绑定，这个类通过配置文件的前缀`mybatis`来设置属性

```java
@Configuration
@ConditionalOnClass({SqlSessionFactory.class, SqlSessionFactoryBean.class})
@ConditionalOnSingleCandidate(DataSource.class)
@EnableConfigurationProperties({MybatisProperties.class})
@AutoConfigureAfter({DataSourceAutoConfiguration.class, MybatisLanguageDriverAutoConfiguration.class})
public class MybatisAutoConfiguration implements InitializingBean {
```



**1、导入了`sqlSessionFactory`**

> 在`MybatisAutoConfiguration`类中会为我们配置`sqlSessionFactory`将其放入ioc容器中。这个就和我们在SpringMVC结果通过xml配置`SqlSessionFactoryBean`是一样的。
>
> 在配置`sqlSessionFactory`时，从ioc容器中获取数据源，并设置进去

```java
@Bean
@ConditionalOnMissingBean
public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
    SqlSessionFactoryBean factory = new SqlSessionFactoryBean();
    factory.setDataSource(dataSource);
    factory.setVfs(SpringBootVFS.class);
    if (StringUtils.hasText(this.properties.getConfigLocation())) {
        factory.setConfigLocation(this.resourceLoader.getResource(this.properties.getConfigLocation()));
    }
    ...
```



**2、配置了sqlSession**

> `sqlSessionTemplate`继承自`SqlSession`，他当中有SqlSession对象可以来操作数据库

```java
@Bean
@ConditionalOnMissingBean
public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
    ExecutorType executorType = this.properties.getExecutorType();
    return executorType != null ? new SqlSessionTemplate(sqlSessionFactory, executorType) : new SqlSessionTemplate(sqlSessionFactory);
}
```

```java
public class SqlSessionTemplate implements SqlSession, DisposableBean {
    private final SqlSessionFactory sqlSessionFactory;
    private final ExecutorType executorType;
    private final SqlSession sqlSessionProxy;
    private final PersistenceExceptionTranslator exceptionTranslator;
    ...
```



**3、配置了MapperScannerConfigurer**

> 这个就相当于在SpringMVC中配置的`MapperScannerConfigurer`，用于我们直接获取mapper接口的代理实现类对象，这样就可以直接从ioc容器获取mapper代理对象，来操作数据库。
>
> **注意：**我们的mapper接口必须使用`@Mapper`注解标注，才能扫描到，`AutoConfiguredMapperScannerRegistrar`类中有说明

```java
@Configuration
@Import({MybatisAutoConfiguration.AutoConfiguredMapperScannerRegistrar.class})
@ConditionalOnMissingBean({MapperFactoryBean.class, MapperScannerConfigurer.class})
public static class MapperScannerRegistrarNotFoundConfiguration implements InitializingBean {
    public MapperScannerRegistrarNotFoundConfiguration() {
    }

    public void afterPropertiesSet() {
        MybatisAutoConfiguration.logger.debug("Not found configuration for registering mapper bean using @MapperScan, MapperFactoryBean and MapperScannerConfigurer.");
    }
}
```

```java
public static class AutoConfiguredMapperScannerRegistrar implements BeanFactoryAware, ImportBeanDefinitionRegistrar {
    private BeanFactory beanFactory;

    public AutoConfiguredMapperScannerRegistrar() {
    }

    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        if (!AutoConfigurationPackages.has(this.beanFactory)) {
            MybatisAutoConfiguration.logger.debug("Could not determine auto-configuration package, automatic mapper scanning disabled.");
        } else {
            MybatisAutoConfiguration.logger.debug("Searching for mappers annotated with @Mapper");
            List<String> packages = AutoConfigurationPackages.get(this.beanFactory);
            if (MybatisAutoConfiguration.logger.isDebugEnabled()) {
                packages.forEach((pkg) -> {
                    MybatisAutoConfiguration.logger.debug("Using auto-configuration base package '{}'", pkg);
                });
            }

            BeanDefinitionBuilder builder = BeanDefinitionBuilder.genericBeanDefinition(MapperScannerConfigurer.class);
            builder.addPropertyValue("processPropertyPlaceHolders", true);
            builder.addPropertyValue("annotationClass", Mapper.class);
            builder.addPropertyValue("basePackage", StringUtils.collectionToCommaDelimitedString(packages));
            BeanWrapper beanWrapper = new BeanWrapperImpl(MapperScannerConfigurer.class);
            Set<String> propertyNames = (Set)Stream.of(beanWrapper.getPropertyDescriptors()).map(FeatureDescriptor::getName).collect(Collectors.toSet());
            if (propertyNames.contains("lazyInitialization")) {
                builder.addPropertyValue("lazyInitialization", "${mybatis.lazy-initialization:false}");
            }

            if (propertyNames.contains("defaultScope")) {
                builder.addPropertyValue("defaultScope", "${mybatis.mapper-default-scope:}");
            }

            registry.registerBeanDefinition(MapperScannerConfigurer.class.getName(), builder.getBeanDefinition());
        }
    }
```





**MyBatis注解版**

整合完MyBatis后，MyBatis的注解版和配置版可以混合使用



**Controller**

```java
@Controller
public class EmpController {

    @Autowired
    EmpService empService;

    @RequestMapping("/emp")
    @ResponseBody
    public Emp addEmp(Emp emp) {
        empService.addEmp(emp);
        return emp;
    }
}
```



**Service**

```java
@Service
public class EmpService {

    @Autowired
    EmpMapper empMapper;

    public void addEmp(Emp emp) {
        empMapper.addEmp(emp);
    }
}
```



**Mapper接口**

```java
@Mapper
public interface EmpMapper {

    @Insert("insert into t_emp values(null, #{empName}, #{age}, #{gender}, #{email})")
    @Options(useGeneratedKeys = true, keyProperty = "empId")
    public void addEmp(Emp emp);
}
```





每个Mapper接口都需要标`@Mapper`注解，也可以直接在启动类上标注`@MapperScan("com.xrj.mapper")`注解，指定扫描包即可，这样Mapper接口就不需要标注了









### 3.4 整合MyBatis-plus

`MyBatis-Plus`是一个 `MyBatis`的增强工具，在 `MyBatis` 的基础上只做增强不做改变，为简化开发、提高效率而生。



**1、导入依赖**

> 导入`mybatis-plus`的依赖之后，他会将`MyBatis`和`spring-boot-starter-data-jdbc`都导入

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
```



**2、修改配置**

```yml
mybatis-plus:
  type-aliases-package: com.xrj.pojo
  configuration:
    map-underscore-to-camel-case: true
```



**3、使用MyBatis-plus**

**controller**

```java
@Controller
public class DeptController {
    @Autowired
    DeptService deptService;

    @RequestMapping("/dept")
    @ResponseBody
    public Dept getDeptById(Integer id){
        return deptService.getDeptById(id);
    }
}
```



**service**

```java
@Service
public class DeptService {
    @Autowired
    DeptMapper deptMapper;

    public Dept getDeptById(Integer id) {
        return deptMapper.selectById(id);
    }
}
```



**mapper**

> 使用`MyBatis-plus`时，让mapper接口继承`BaseMapper`即可，在`BaseMapper`中提供了很多已经实现的增删改查方法。
>
> **注意：**使用`Mybatis-plus`根据id查询时，默认情况下，主键就是`id`字段

```java
@Mapper
public interface DeptMapper extends BaseMapper<Dept> {
}
```







**自动配置原理**

> 在`MybatisPlusAutoConfiguration`类中，自动配置了`MyBatisPlus`的各项属性。
>
> 他的属性是根据`MybatisPlusProperties`这个类来设置的。

```java
@Configuration
@ConditionalOnClass({SqlSessionFactory.class, SqlSessionFactoryBean.class})
@ConditionalOnSingleCandidate(DataSource.class)
@EnableConfigurationProperties({MybatisPlusProperties.class})
@AutoConfigureAfter({DataSourceAutoConfiguration.class, MybatisPlusLanguageDriverAutoConfiguration.class})
public class MybatisPlusAutoConfiguration implements InitializingBean {
```

> 在`MybatisPlusProperties`类中，它在配置文件中的前缀是`mybatis-plus`。
>
> 同时，他的`mapperLocations`有默认值`"classpath*:/mapper/**/*.xml"`，因此我们的mapper映射文件只要放在`resources/mapper`这个路径下就会被扫描到。
>
> 和MyBatis一样，mapper映射文件和mapper接口的路径一样也可以被扫描到。

```java
@ConfigurationProperties(
    prefix = "mybatis-plus"
)
public class MybatisPlusProperties {
    private static final ResourcePatternResolver resourceResolver = new PathMatchingResourcePatternResolver();
    private String configLocation;
    private String[] mapperLocations = new String[]{"classpath*:/mapper/**/*.xml"};
```



1、配置了`sqlSessionFactory`

> 在`MybatisPlusAutoConfiguration`类中会为我们配置`sqlSessionFactory`将其放入ioc容器中。这个就和我们在SpringMVC结果通过xml配置`SqlSessionFactoryBean`是一样的。
>
> 在配置`sqlSessionFactory`时，从ioc容器中获取数据源，并设置进去

```java
@Bean
@ConditionalOnMissingBean
public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
    MybatisSqlSessionFactoryBean factory = new MybatisSqlSessionFactoryBean();
    factory.setDataSource(dataSource);
    factory.setVfs(SpringBootVFS.class);
    ...
```



2、配置了`sqlSessionTemplate`

> 原理和`MyBatis`一样



3、配置了`AutoConfiguredMapperScannerRegistrar`

> 原理和`MyBatis`一样







#### MyBatis-plus设置分页

**1、设置分页插件**

> 将分页功能作为Bean对象放入ioc容器

```java
@Configuration
public class MyBatisConfig {
    @Bean
    public MybatisPlusInterceptor paginationInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        // 设置请求的页面大于最大页后操作， true跳回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join

        //这是分页拦截器
        PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor();
        paginationInnerInterceptor.setOverflow(true);
        paginationInnerInterceptor.setMaxLimit(500L);
        mybatisPlusInterceptor.addInnerInterceptor(paginationInnerInterceptor);

        return mybatisPlusInterceptor;
    }
}
```

 

**2、mapper接口**

> 使用`MyBatis-plus`，mapper接口直接继承`BaseMapper<>`即可，里边是对应泛型，`BaseMapper<>`里面以及实现了很多常用的crud操作

```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
}
```



**3、service层**

> service接口可以继承`IService<User>`接口，该接口同样定义了很多操作

```java
public interface UserService extends IService<User> {
}
```

> 但是在service接口的实现类中，如果我们直接实现service接口，那么它需要实现`IService`接口的所有方法，因此我们可以继承`ServiceImpl<UserMapper, User>`，其中第一个参数就是我们的mapper接口【这里就是`@Autowired`注入】，第二个参数就是我们当前操作的对象类型，这样service实现类也包含了很多已经实现的方法

```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
}
```



**4、controller层**

> controller层的方法中，先创建`Page`对象，参数为当前页码，每页显示的条数，然后调用service层的page方法查询，查询的结果`page`就包含了一系列的分页信息，将其保存到model中，然后就可以使用`Thymeleaf`来进行渲染

```java
@Controller
public class TableController {

    @Autowired
    UserService userService;

    @RequestMapping("/dynamic_table")
    public String dynamic_table(@RequestParam(value = "pn", defaultValue = "1") Integer pn, Model model){

        Page<User> userPage = new Page<>(pn, 3);
        Page<User> page = userService.page(userPage, null);
        model.addAttribute("users", page);

        return "table/dynamic_table";
    }
}
```



**5、删除用户信息**

> 因为删除完信息后，我们需要重定向到当前页面，这样才会重新向数据库发请求获取新的数据。
>
> 但是默认是跳到第一页，我们希望，删除后，还回到当前页。所以让前端将当前页码发送过来，然后我们可以将这个页码信息放在一个`RedirectAttributes`类型的对象中，这个就是专门存放重定向属性的，我们重定向到`/dynamic_table`页面后，`RedirectAttributes`里边的数据会拼接在重定向页面的url后边。

```java
@Controller
public class UserController {

    @Autowired
    UserService userService;

    @RequestMapping("/user/delete/{id}")
    public String deleteUser(@PathVariable("id") Integer id, @RequestParam("pn") Integer pn, RedirectAttributes ra) {
        userService.removeById(id);
        // RedirectAttributes是重定向参数，重定向后会将这里边的参数拼接到url后边
        ra.addAttribute("pn", pn);
        return "redirect:/dynamic_table";
    }
}
```





### 3.5 整合Redis

**1、导入Redis的依赖**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



**2、配置redis**

```yml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
```



**3、使用redis**

```java
@SpringBootTest
class Springboot04Web02ApplicationTests {

    @Autowired
    StringRedisTemplate redisTemplate;

    @Test
    void test01() {
        ValueOperations ops = redisTemplate.opsForValue();
        ops.set("hello", "world");
        Object hello = ops.get("hello");
        System.out.println(hello);
    }
}
```





**自动配置原理**

> 导入Redis的依赖后，在`RedisAutoConfiguration`类中会自动配置Redis相关组件
>
> 1、Redis的默认配置与`RedisProperties`类关联，该类在配置文件中的前缀为`spring.redis`
>
> 2、通过`@import`导入了`LettuceConnectionConfiguration`和`JedisConnectionConfiguration`的客户端连接。默认情况下，导入Redis的starter后，会给我们导入Lettuce的客户端连接，我们也可以在pom文件中导入Jedis，然后在配置文件中用`redis.client-type`来指定使用那种客户端
>
> 3、注入了`RedisTemplate<Object, Object>`组件，他的k、v都是`Object`类型
>
> 4、注入了`StringRedisTemplate`组件，他的k、v都是`string`类型
>
> 5、我们使用`StringRedisTemplate`、`RedisTemplate`就可以操作Redis

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({RedisOperations.class})
@EnableConfigurationProperties({RedisProperties.class})
@Import({LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class})
public class RedisAutoConfiguration {
    public RedisAutoConfiguration() {
    }

    @Bean
    @ConditionalOnMissingBean(
        name = {"redisTemplate"}
    )
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```





#### Redis统计访问次数

我们希望统计访问某个页面的次数，那么可以在一个拦截器中使用Redis统计访问当前uri的次数，以uri作为key，访问次数作为value



**拦截器**

> 拦截器必须放入ioc容器中，因为要使用`@Autowired`注入

```java
@Component
public class RedisInterceptor implements HandlerInterceptor {
    @Autowired
    StringRedisTemplate redisTemplate;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String uri = request.getRequestURI();
        ValueOperations<String, String> ops = redisTemplate.opsForValue();
        // 每次访问这个uri，这个value就会+1
        ops.increment(uri);
        return true;
    }
}
```



**配置拦截器**

> 在添加拦截器时，需要使用ioc容器中的拦截器，因为ioc容器中拦截器的属性redisTemplate会被注入，如果直接new一个是不会注入的

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    RedisInterceptor redisInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/", "/login", "/css/**", "/fonts/**", "/images/**", "/js/**");
        // 这里如果new一个redisInterceptor，那么里边的redisTemplate属性就不会自动注入了
        registry.addInterceptor(redisInterceptor)
                .addPathPatterns("/**")
                .excludePathPatterns("/", "/login", "/css/**", "/fonts/**", "/images/**", "/js/**");
    }
}
```



**Controller**

> 每次到`main.html`页面，都通过Redis查询访问对应uri的次数，然后放入model中，前端通过`Thymeleaf`就可以获取

```java
@Controller
public class IndexController {

    @Autowired
    StringRedisTemplate redisTemplate;
    
    @RequestMapping("/main.html")
    public String mainPage(HttpSession session, Model model) {
        ValueOperations<String, String> ops = redisTemplate.opsForValue();
        String s = ops.get("/main.html");
        String s1 = ops.get("/sql");
        model.addAttribute("mainCount", s);
        model.addAttribute("sqlCount", s1);
        return "main";
    }
}
```







## 4、单元测试

### 4.1 JUnit5

**JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**

- `JUnit Platform`: Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自制的测试引擎，其他测试引擎也都可以接入。

- `JUnit Jupiter`: JUnit Jupiter提供了JUnit5的新的编程模型，是JUnit5新特性的核心。内部包含了一个**测试引擎**，用于在Junit Platform上运行。

- `JUnit Vintage`: 由于JUint已经发展多年，为了照顾老的项目，JUnit Vintage提供了兼容JUnit4.x，JUnit3.x的测试引擎。

**注意**：

- SpringBoot 2.4 以上版本移除了默认对 Vintage 的依赖。如果需要兼容JUnit4需要自行引入（不能使用JUnit4的功能 @Test）
- JUnit 5’s Vintage已经从`spring-boot-starter-test`从移除。如果需要继续兼容Junit4需要自行引入Vintage依赖



使用Junit5，导入对应的starter即可

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



**以前JUnit4的单元测试**

> `Junit4`中`Test`是`org.junit.Test;`

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
```



**现在JUnit5的单元测试**

> `JUnit5`中的`Test`是`import org.junit.jupiter.api.Test;`
>
> `JUnit`类具有Spring的功能，可以使用`@Autowired`注入，使用`@Transactional`标注测试方法后，测试方法完成自动回滚
>
> 加上`@SpringBootTest	`注解就可以使用SpringBoot中的功能，如果不加，可以测试，但是不能使用SpringBoot的功能，例如@Autowire自动注入

```java
@SpringBootTest	
class Springboot04Web02ApplicationTests {
    @Autowired
    DeptMapper deptMapper;

    @Test
    void test01() {
        Dept dept = deptMapper.selectById(2L);
        System.out.println(dept);
    }
}
```





### 4.2 常用注解

- **@Test**：表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
- **@ParameterizedTest**：表示方法是参数化测试。
- **@RepeatedTest**：表示方法可重复执行。
- **@DisplayName**：为测试类或者测试方法设置展示名称。
- **@BeforeEach**：表示在**每个**单元测试**之前**执行。
- **@AfterEach**：表示在**每个**单元测试**之后**执行。
- **@BeforeAll**：表示在**所有**单元测试**之前**执行。
- **@AfterAll**：表示在**所有**单元测试**之后**执行。
- **@Tag**：表示单元测试类别，类似于JUnit4中的@Categories。
- **@Disabled**：表示测试类或测试方法不执行，类似于JUnit4中的@Ignore。
- **@Timeout**：表示测试方法运行如果超过了指定时间将会返回错误。
- **@ExtendWith**：为测试类或测试方法提供扩展类引用。`@SpringBootTest`注解就被这个注解标注了







### 4.3 断言机制

断言Assertion用来对测试需要满足的条件进行验证。这些断言方法都是`org.junit.jupiter.api.Assertions`的静态方法。检查业务逻辑返回的数据是否合理。所有的测试运行结束以后，会有一个详细的测试报告。



**简单断言**

用来对单个值进行简单的验证。如：

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

```java
@Test
@DisplayName("simple assertion")
public void simple() {
     assertEquals(3, 1 + 2, "simple math");
     assertNotEquals(3, 1 + 1);

     assertNotSame(new Object(), new Object());
     Object obj = new Object();
     assertSame(obj, obj);

     assertFalse(1 > 2);
     assertTrue(1 < 2);

     assertNull(null);
     assertNotNull(new Object());
}
```





**数组断言**

通过 assertArrayEquals 方法来判断两个对象或原始类型的数组是否相等。

```java
@Test
@DisplayName("array assertion")
public void array() {
	assertArrayEquals(new int[]{1, 2}, new int[] {1, 2});
}
```





**组合断言**

`assertAll()`方法接受多个 `org.junit.jupiter.api.Executable` 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言。

```java
@Test
@DisplayName("assert all")
public void all() {
 assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
 );
}
```





**异常断言**

在JUnit4时期，想要测试方法的异常情况时，需要用`@Rule`注解的`ExpectedException`变量还是比较麻烦的。而JUnit5提供了一种新的断言方式`Assertions.assertThrows()`，配合函数式编程就可以进行使用。

```java
@Test
@DisplayName("异常测试")
public void exceptionTest() {
    ArithmeticException exception = Assertions.assertThrows(
           //扔出断言异常
            ArithmeticException.class, () -> System.out.println(1 % 0));
}
```





**超时断言**

JUnit5还提供了Assertions.assertTimeout()为测试方法设置了超时时间。

```java
@Test
@DisplayName("超时测试")
public void timeoutTest() {
    //如果测试方法时间超过1s将会异常
    Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
```





**快速失败**

通过 fail 方法直接使得测试失败。

```java
@Test
@DisplayName("fail")
public void shouldFail() {
	fail("This should fail");
}
```





### 4.4 前置条件

JUnit 5 中的前置条件（`assumptions`【假设】）类似于断言，不同之处在于不满足的**断言assertions**会使得测试方法失败，而**不满足的前置条件只会使得测试方法的执行终止**。

前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。



```java
@DisplayName("前置条件")
public class AssumptionsTest {
    private final String environment = "DEV";

    @Test
    @DisplayName("simple")
    public void simpleAssume() {
        assumeTrue(Objects.equals(this.environment, "DEV"));
        assumeFalse(() -> Objects.equals(this.environment, "PROD"));
    }

    @Test
    @DisplayName("assume then do")
    public void assumeThenDo() {
        assumingThat(
            Objects.equals(this.environment, "DEV"),
            () -> System.out.println("In DEV")
        );
    }
}
```

`assumeTrue` 和 `assumFalse` 确保给定的条件为 `true` 或 `false`，不满足条件会使得测试执行终止。

`assumingThat` 的参数是表示条件的布尔值和对应的 Executable 接口的实现对象。只有条件满足时，`Executable` 对象才会被执行；当条件不满足时，测试执行并不会终止。





### 4.5 参数化测试

参数化测试使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。

利用`@ValueSource`等注解，指定入参，我们将可以使用不同的参数进行多次单元测试，而不需要每新增一个参数就新增一个单元测试，省去了很多冗余代码。

- `@ValueSource`：为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型
- `@NullSource`：表示为参数化测试提供一个null的入参
- `@EnumSource`：表示为参数化测试提供一个枚举入参
- `@CsvFileSource`：表示读取指定CSV文件内容作为参数化测试入参
- `@MethodSource`：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)



参数化测试需要标注`@ParameterizedTest`注解

```java
@ParameterizedTest
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
}
```







## 5、指标监控

### 5.1 SpringBoot Actuator

未来每一个微服务在云上部署以后，我们都需要对其进行监控、追踪、审计、控制等。SpringBoot就抽取了Actuator场景，使得我们每个微服务快速引用即可获得生产级别的应用监控、审计等功能。



**添加依赖**

> 导入依赖后，SpringBoot就会配置指标监控的一系列组件
>
> 加入依赖后，只要访问`http://localhost:8080/actuator/**`就可以查看监控的指标信息了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```





### 5.2 Endpoint

SpringBoot中有两种监控模式

- HTTP：默认只暴露`health`和`info`
- JMX：默认暴露所有的Endpoint，使用java的jconsole工具就是JMX模式



但是我们一般使用HTTP发请求，所以我们需要自己配置，使得HTTP模式暴露所有Endpoint，这样所有的Endpoint都会暴露，我们都可以通过`http://localhost:8080/actuator/**`查看

```yml
management:
  endpoints:
    enabled-by-default: true # 暴露所有端点信息，默认为true，如果为false，表示所有端点全部关闭。两种方式都无法访问
    # web配置的是哪些端点信息可以在web中展示
    web:
      exposure:
        include: '*'  # 以web方式暴露
```



**测试**

- `http://localhost:8080/actuator/beans`
- `http://localhost:8080/actuator/configprops`
- `http://localhost:8080/actuator/metrics`
- `http://localhost:8080/actuator/metrics/jvm.gc.pause`
- `http://localhost:8080/actuator/metrics/endpointName/detailPath`



下面的配置就是，关闭了所有的端点信息，只针对`beans`和`health`端点开启了，因此只能访问这两个的监控信息

```yml
management:
  endpoints:
    enabled-by-default: false # 关闭所有端点信息
    # 这里配置的是哪些信息在web中展示
    web:
      exposure:
        include: '*'  #以web方式暴露

  # 这里配置的是哪些端点开放
  endpoint:
    beans:
      enabled: true
    health:
      enabled: true
      show-details: always #总是显示详细信息。可显示每个模块的状态信息
```



其中最常用的Endpoint：

- Health：监控状况。健康检查端点，我们一般用于在云平台，平台会定时的检查应用的健康状况，我们就需要Health Endpoint可以为平台返回当前应用的一系列组件健康状况的集合。
  - health endpoint返回的结果，应该是一系列健康检查后的一个汇总报告。
  - 很多的健康检查默认已经自动配置好了，比如：数据库、redis等。
  - 可以很容易的添加自定义的健康检查机制。
- Metrics：运行时指标。提供详细的、层级的、空间指标信息，这些信息可以被pull（主动推送）或者push（被动获取）方式得到：
  - 通过Metrics对接多种监控系统。
  - 简化核心Metrics开发。
  - 添加自定义Metrics或者扩展已有Metrics。
- Loggers：日志记录







### 5.3 SpringBootAdmin

如果我们有很多应用程序都需要被监控，每次查看其监控信息需要一个个看，非常麻烦。

因此我们再新建一个程序用来监控这些程序

- 显示监控信息的服务器：用于获取服务信息，并显示对应的信息

- 运行的服务：启动时主动上报，告知监控服务器自己需要被监控，同时开放自己可以被监控到的信息



在监控服务器上展示的所有数据其实都是通过SpringBoot的`Actuator`提供的，通过获取端点信息展示在页面上

因为我们导入的`spring-boot-admin-starter-server`和`spring-boot-admin-starter-client`里边都包含了`spring-boot-starter-actuator`，所以可以监控到指标信息



**admin-server**

> 1、在创建一个SpringBoot项目时，可以在ops选项中添加上SpringBoot Admin(Server)功能，这样他就会默认为我们在pom文件中添加`spring-boot-admin-starter-server`。或者我们自己在pom文件中添加，但是需要注意的是，版本要和SpringBoot版本一致，而且必须是一个web项目
>
> 2、在SpringBoot的启动类上添加`@EnableAdminServer`注解，那么当前web应用就是一个监控服务器

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>2.7.4</version>
</dependency>
```

```java
@EnableAdminServer
@SpringBootApplication
public class Springboot05AdminServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot05AdminServerApplication.class, args);
    }
}
```

```yml
server:
  port: 8888
```





**admin-client**

> 1、添加`spring-boot-admin-starter-client`的依赖，在创建SpringBoot应用时或者自己添加都可以
>
> 2、配置监控服务器属性，以及当前应用想让监控服务器监控哪些信息，配置完后启动应用，就会被监控程序监控到

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.7.4</version>
</dependency>
```

```yml
server:
  port: 8081

spring:
  boot:
    admin:
      client:
        url: http://localhost:8888

management:
  endpoint:
    health:
      show-details: always  # 展示健康信息
  endpoints:
    web:
      exposure:
        include: "*"  # 暴露所有端点，默认只有healthy
```





### 5.4 定制info信息

> 编写了info信息后，可以通过`http://localhost:8080/actuator/info`查看，同时可以显示在监控服务器上
>
> 注意：在springboot2.7.2中，info端点默认是不启用的，所以我们要手动开启

- 编写配置文件

  ```yml
  management:
    endpoint:
      health:
        show-details: always  # 展示健康信息
    endpoints:
      web:
        exposure:
          include: "*"  # 暴露所有端点，默认只有healthy
    info:
      env:
        enabled: true  # 手动开启info
  
  info:
    appName: @project.artifactId@
    version: @project.version@
    author: xrj
  ```

- 编写`InfoContributor`组件

  ```java
  @Component
  public class MyInfoContributor implements InfoContributor {
      @Override
      public void contribute(Info.Builder builder) {
          builder.withDetail("name", "xrj");
          HashMap<String, Object> map = new HashMap<>();
          map.put("aaa", "111");
          map.put("bbb", "222");
          map.put("ccc", "333");
          map.put("ddd", "444");
          builder.withDetails(map);
      }
  }
  ```





### 5.5 定制health信息

> health信息是检查当前应用的状态
>
> 可以通过实现`HealthIndicator`接口或者继承`AbstractHealthIndicator`类来定制health信息

```java
@Component
public class MyHealthIndicator extends AbstractHealthIndicator {
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        boolean condition = true;
        if (condition) {    // 检查通过
            builder.status(Status.UP);
            builder.withDetail("name", "xrj");
            HashMap<String, Object> map = new HashMap<>();
            map.put("aaa", "111");
            map.put("bbb", "222");
            builder.withDetails(map);
        } else {
            builder.status(Status.DOWN);
            builder.withDetail("ex", "error");
        }
    }
}
```





### 5.6 定制Metrics信息

> 我们在service方法中，添加Metrics的信息，`metrics`信息中会有一项名为`method.running.counter`的指标。只要调用`getBookById`方法，`counter`属性就加1

```java
@Service
public class BookServiceImpl implements BookService {
    @Autowired
    BookMapper bookMapper;

    Counter counter;
    public BookServiceImpl(MeterRegistry meterRegistry){
        counter = meterRegistry.counter("method.running.counter");
    }
    public Book getBookById(Integer id) {
        counter.increment();
        return bookMapper.getBookById(id);
    }
}
```





### 5.7 定制Endpoint

> 通过`@Endpoint`注解标识当前类是一个`Endpoint`端点，id属性可以指定端点的id，`enableByDefault`设置为true表示该端点默认开启
>
> 访问`http://localhost:8080/actuator/pay`就会执行`@ReadOperation`注解标识的方法

```java
@Component
@Endpoint(id = "pay", enableByDefault = true)
public class MyEndpoint {
    @ReadOperation
    public Map pay(){
        return Collections.singletonMap("info","pay finish");
    }
}
```







## 6、多环境开发

### 6.1 Profile环境切换

为了方便多环境适配，Spring Boot简化了profile功能。

- 默认配置文件`application.yaml`任何时候都会加载。
- 指定环境配置文件`application-{env}.yaml`，`env`通常替代为`test`、`prod`等，
- 激活指定环境
  - 配置文件激活：`spring.profiles.active=prod`
  - 命令行激活：`java -jar xxx.jar --spring.profiles.active=prod  --person.name=xrj`（修改配置文件的任意值，**命令行优先**）
- 默认配置与环境配置同时生效
- 同名配置项，profile配置优先



**application.yml**

```yml
# 指定激活的环境
spring:
  profiles:
    active: test
server:
  port: 8080
```

**application-prod.yml**

```yml
person:
  name: prod-xyy
server:
  port: 8081
```

**application-test.yml**

```yml
person:
  name: test-xyy
server:
  port: 8082
```



**controller**

```java
@Controller
public class HelloController {

    // @Value从配置文件读数据，如果没有person.name默认为xyy
    @Value("${person.name:xyy}")
    private String name;

    @RequestMapping("/")
    @ResponseBody
    public String hello(){
        return "hello" + name;
    }
}
```







### 6.2 Profile环境装配

> `@Profile(xxx)`标注在类上，表示只有当前环境为`xxx`时，才加载这个类。
>
> `@Profile(xxx)`标注在方法上，表示只有当前环境为`xxx`时，才加载这个方法

```java
public interface Person {
}
```

**Boss**

```java
@Data
@ConfigurationProperties("person")
@Component
@Profile("test")  // test环境下加载该组件
public class Boss implements Person{
    private String name;
    private Integer age;
}
```

**Worker**

```java
@Data
@ConfigurationProperties("person")
@Component
@Profile("prod")  // prod环境下加载该组件
public class Worker implements Person{
    private String name;
    private Integer age;
}
```

**Controller**

```java
@Controller
public class HelloController {
    @Autowired
    private Person person;

    @RequestMapping("/")
    @ResponseBody
    public String hello(){
        // 激活prod环境返回Worker，激活test环境返回Boss
        return person.getClass().toString();
    }
}
```





### 6.3 Profile分组

> 可以通过`spring.profiles.group`对配置文件分组，激活环境时，指定组名，就会将该组全部配置文件加载
>
> 同一个组的两个配置相同时，优先使用后边的配置文件

**application.yml**

```yml
spring:
  profiles:
    active: test
    group:
      # prod: prod1,prod2
      prod[0]: prod1   	# prod组的第一个配置为prod1
      prod[1]: prod2	# prod组的第二个配置为prod2, 同名配置第二个覆盖第一个
      test[0]: test		# test组的第一个配置为test

server:
  port: 8080
```



**application-prod1yml**

```yml
person:
  name: prod-xyy

server:
  port: 8081
```



**application-prod2.yml**

```yml
person:
  age: 20

server:
  port: 8083
```



**application-test.yml**

```yml
person:
  name: test-xyy
  age: 30
server:
  port: 8082
```







### 6.4 yml多环境开发

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





### 6.5 配置优先级

配置文件查找位置

1、classpath 根路径。`classpath：application.yml 		`

2、classpath 根路径下config目录。`classpath：config/application.yml`

3、jar包当前目录。` file（和jar文件在同一目录下）：application.yml`

4、jar包当前目录的config目录。`file（和jar文件在同一目录下） ：config/application.yml  `



**加载顺序从上到下，即下边的配置会覆盖上边的配置**







## 7、SpringBoot高级特性

### 7.1 自定义starter

**starter启动原理**

```mermaid
graph LR
A[starter] -->B[autoconfigure]
B --> C[spring-boot-starter]
```

1、我们导入场景启动器【xx-starter】后，starter的pom.xml会引入`autoconfigure`依赖

2、`autoconfigure`包中配置使用`META-INF/spring.factories`中`EnableAutoConfiguration`的值，使得项目启动加载指定的自动配置类

3、编写自动配置类 `xxxAutoConfiguration` -> `xxxxProperties`

- `@Configuration`
- `@Conditional`
- `@EnableConfigurationProperties`
- `@Bean`
- ......

4、引入`starter `--- `xxxAutoConfiguration` --- 容器中放入组件 ---- `绑定xxxProperties` ---- 配置项





#### 1. 编写starter工程

> 该工程只有一个pom.xml文件，里边引入了我们的`autoconfiguration`的工程
>
> 到时候使用这个包时，只需要导入这个starter即可
>
> 也可以将starter与autoconfiguration自动配置类写在一个工程
>
> 通过clean和install安装到本地仓库，就可以在我们的项目中使用了

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.15</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.xrj</groupId>
    <artifactId>ip-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>ip-spring-boot-starter</name>
    <description>ip-spring-boot-starter</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>com.xrj</groupId>
            <artifactId>ip-spring-boot-starter-autoconfiguration</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>

</project>
```





#### 2. 编写autoconfiguration工程

> 该工程是实现自动配置的关键，不需要主启动类
>
> 功能：统计ip访问次数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202309142026605.png)



**spring.factories**

> 我们将`spring.factories`文件放在`META-INF`文件下，里边写上我们需要自动配置的类，SpringBoot会扫描这个文件，通过这个类自动配置

```xml
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.xyy.auto.IpCountAutoConfiguration
```





**IpCountAutoConfiguration**

> 这个是自动配置类，该类中通过`@Bean`标签添加了一个bean对象到ioc容器中
>
> v1：使用`@EnableConfigurationProperties(IpProperties.class)`，不使用`@Import(IpProperties.class)`
>
> 我们使用`@EnableConfigurationProperties(IpProperties.class)`注解将`IpProperties`类添加到ioc容器中，同时这个类与配置文件中前缀为`tools.ip`的配置关联，但是通过这个注解在ioc容器中配置的bean对象的id为全类名，我们在`IpCountService`希望通过`#{ipProperties.cycle}`获取bean对象的值，因此必须将bean的id改为`ipProperties`
> v2：我们不使用`@EnableConfigurationProperties(IpProperties.class)`，而是通过`@Import(IpProperties.class)`将`IpProperties`对象放入ioc容器，同时在`IpProperties`上使用`@Component(value = "ipProperties")`设置bean的id，这样我们就可以获取ioc容器中bean的值了

```java
// 使用@EnableConfigurationProperties注解放入ioc容器的bean的id为全类名tools.ip-com.xrj.properties.IpProperties
// @EnableConfigurationProperties(IpProperties.class)
@Import(IpProperties.class)
@EnableScheduling  // 开启定时任务
public class IpCountAutoConfiguration {
    @Bean
    public IpCountService ipCountService() {
        return new IpCountService();
    }
}
```





**IpProperties**

> v1：没有`@Component(value = "ipProperties")`注解
>
> v2：使用`@Component(value = "ipProperties")`将组件放入ioc容器，并设置id为`ipProperties`
>
> 这里的文档注释，在我们配置了yml自动提示时会显示出来

```java
@Component(value = "ipProperties")
@ConfigurationProperties(prefix = "tools.ip")
public class IpProperties {
    /**
     * 多久输出一次, 默认5秒
     */
    private Integer cycle = 5;

    /**
     * 每次循环后，值是否重置
     */
    private Boolean reset = false;

    /**
     * 输出模式 detail:详细模式 simple:简易模式
     */
    private String mod = Model.DETAIL.value;

    public enum Model {
        DETAIL("detail"),
        SIMPLE("simple");
        private String value;

        Model(String value) {
            this.value = value;
        }

        public String getValue() {
            return value;
        }
    }

    public Integer getCycle() {
        return cycle;
    }

    public Boolean getReset() {
        return reset;
    }

    public String getMod() {
        return mod;
    }

    public void setCycle(Integer cycle) {
        this.cycle = cycle;
    }

    public void setReset(Boolean reset) {
        this.reset = reset;
    }

    public void setMod(String mod) {
        this.mod = mod;
    }
}
```





**IpCountService**

```java
public class IpCountService {

    private Map<String, Integer> map = new HashMap<>();

    // 当前request对象的注入工作由使用该starter的工程提供
    @Autowired
    HttpServletRequest request;

    @Autowired
    IpProperties ipProperties;

    public void Count(){
        String ip = request.getRemoteAddr();
        map.put(ip, map.getOrDefault(ip, 0) + 1);
    }

    @Scheduled(cron = "0/#{ipProperties.cycle} * * * * ?")  // 每5秒执行一次
    public void print(){
        if (ipProperties.getMod().equals(IpProperties.Model.DETAIL.getValue())) {
            for (Map.Entry<String, Integer> entry : map.entrySet()) {
                System.out.println("ip: " + entry.getKey() + "   num: " + entry.getValue());
            }
        } else if (ipProperties.getMod().equals(IpProperties.Model.SIMPLE.getValue())){
            for (Map.Entry<String, Integer> entry : map.entrySet()) {
                System.out.println("ip: " + entry.getKey());
            }
        }

        if (ipProperties.getReset()) {
            map.clear();
        }
    }
}
```





#### 3. 工程中导入starter

> 在我们的工程中导入starter对应坐标，就可以直接使用这个功能了

```xml
<dependency>
    <groupId>com.xrj</groupId>
    <artifactId>ip-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```



**配置文件**

> 可以通过配置文件来设置该组件对应的属性

```yml
tools:
  ip:
    mod: simple
    reset: false
    cycle: 1
```



**controller**

```java
@Controller
public class IndexController {

    @Autowired
    IpCountService ipCountService;

    @RequestMapping("/main.html")
    public String mainPage() {
        ipCountService.Count();
        return "main";
    }
}
```





#### 4. 自定义starter优化

> 在我们自定义的starter中添加一个拦截器，此时将`IpCountService`自动注入，因此拦截器也要放到ioc容器中，在配置类中拦截器也要从容器中获取，否则不会自动注入
>
> 这里不能new，因为`IpCountService`实现时，通过`@Autowired`注入了其他属性，如果new的话，`IpCountService`不被ioc管理，他的属性也不会自动注入了。

**拦截器**

```java
// @Component
public class IpInterceptor implements HandlerInterceptor {

    @Autowired
    IpCountService ipCountService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        ipCountService.Count();
        return true;
    }
}
```



**配置**

```java
@Configuration
public class IpConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(ipInterceptor()).addPathPatterns("/**");
    }

    @Bean
    IpInterceptor ipInterceptor(){
        return new IpInterceptor();
    }
}
```



**自动配置类**

> **注意**：自动配置类一定要将`Ipconfig`类通过`import`导入进来，因为工程中导入了这个starter后，SpringBoot自动扫描不到我们这个配置类，所以无法生效，而我们通过`@Import`导入后就生效了。
>
> 通过`@Import`将`IpConfig`导入，表示将其放入ioc容器中，因此`IpConfig`上不需要使用`@Configuration`注解也可以

```java
// 使用@EnableConfigurationProperties注解放入ioc容器的bean的id为全类名tools.ip-com.xrj.properties.IpProperties
// @EnableConfigurationProperties(IpProperties.class)
@Import({IpProperties.class, IpConfig.class})
@EnableScheduling  // 开启定时任务
public class IpCountAutoConfiguration {
    @Bean
    public IpCountService ipCountService() {
        return new IpCountService();
    }
}
```





#### 5. 增加yml提示功能

> 1、在其他工程导入我们的starter后，通过配置文件修改属性，没有提示功能。
>
> 2、因此，我们需要在自定义starter的pom文件中添加如下依赖，添加依赖后，重新clean、install，就会在target目录下的`META-INF`下生成一个`spring-configuration-metadata.json`文件，将这个文件放在我们的`resources/META-INF`下即可
>
> ```xml
> <dependency>
>     <groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-configuration-processor</artifactId>
>     <optional>true</optional>
> </dependency>
> ```
>
> 3、然后将pom文件中这个依赖删除，重新clean、install，将target下的`configuration-metadata.json`删除，否则会有两个，提示时会重复



**configuration-metadata.json**

> hints属性默认为空`[]`，我们希望设置`mod`属性时给提示，因此在hints属性中添加即可

```json
{
  "groups": [
    {
      "name": "tools.ip",
      "type": "com.xyy.properties.IpProperties",
      "sourceType": "com.xyy.properties.IpProperties"
    }
  ],
  "properties": [
    {
      "name": "tools.ip.cycle",
      "type": "java.lang.Integer",
      "description": "多久输出一次, 默认5秒",
      "sourceType": "com.xyy.properties.IpProperties",
      "defaultValue": 5
    },
    {
      "name": "tools.ip.mod",
      "type": "java.lang.String",
      "description": "输出模式 detail:详细模式 simple:简易模式",
      "sourceType": "com.xyy.properties.IpProperties"
    },
    {
      "name": "tools.ip.reset",
      "type": "java.lang.Boolean",
      "description": "每次循环后，值是否重置",
      "sourceType": "com.xyy.properties.IpProperties",
      "defaultValue": false
    }
  ],
  "hints": [{
    "name": "tools.ip.mod",
    "values": [
      {
        "value": "detail",
        "description": "详细模式."
      },
      {
        "value": "simple",
        "description": "极简模式."
      }
    ]
  }]
}
```









### 7.2 SpringBoot启动流程

> SpringBoot的启动类，执行`run`方法启动SpringBoot项目

```java
@SpringBootApplication
public class Springboot06ProfileApplication {
    public static void main(String[] args) {
        SpringApplication.run(Springboot06ProfileApplication.class, args);
    }
}
```



> 进入`run`方法后，首先会创建一个`SpringApplication`，然后通过`run`方法执行

```java
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
    return run(new Class[]{primarySource}, args);
}

public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
    return (new SpringApplication(primarySources)).run(args);
}
```

   

**1、创建`SpringApplication`**

- 保存了一些信息
- 判断当前应用类型
- `bootstrapRegistryInitializers`：初始化启动引导器，去`META-INF`下的`spring.factories`文件中找
- <font color='red'> `ApplicationContextInitializer` </font>：去`spring.factories`找`ApplicationContextInitializer`
- <font color='red'> `ApplicationListener` </font>：去`spring.factories`找`ApplicationListener`
- 找到主程序

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
    this.sources = new LinkedHashSet();
    this.bannerMode = Mode.CONSOLE;
    this.logStartupInfo = true;
    this.addCommandLineProperties = true;
    this.addConversionService = true;
    this.headless = true;
    this.registerShutdownHook = true;
    this.additionalProfiles = Collections.emptySet();
    this.isCustomEnvironment = false;
    this.lazyInitialization = false;
    this.applicationContextFactory = ApplicationContextFactory.DEFAULT;
    this.applicationStartup = ApplicationStartup.DEFAULT;
    this.resourceLoader = resourceLoader;
    Assert.notNull(primarySources, "PrimarySources must not be null");
    // primarySources就是我们主程序中的 Springboot06ProfileApplication.class，将Class类型的可变参数转为一个Set
    this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
    // WebApplicationType是枚举类，有NONE, SERVLET, REACTIVE;
    // 判断当前应用类型，因为当前是web应用，所以是servlet，后边就会创建web容器
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    
    // 初始启动引导器，去spring.factories文件中找org.springframework.boot.BootstrapRegistryInitializer，默认找到0个
    this.bootstrapRegistryInitializers = new ArrayList(this.getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
    
    // 去spring.factories找 ApplicationContextInitializer，并set进去
    this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
    
    // 去spring.factories找 ApplicationListener，初始化监听器，对初始化过程即运行过程进行干预
    this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
    
    // 通过deduceMainApplicationClass方法找到主程序，初始化引导类类名
    this.mainApplicationClass = this.deduceMainApplicationClass();
}
```

```java
private Class<?> deduceMainApplicationClass() {
    try {
        StackTraceElement[] stackTrace = (new RuntimeException()).getStackTrace();
        StackTraceElement[] var2 = stackTrace;
        int var3 = stackTrace.length;

        for(int var4 = 0; var4 < var3; ++var4) {
            StackTraceElement stackTraceElement = var2[var4];
            // 在堆栈中找到有main方法的就是主程序
            if ("main".equals(stackTraceElement.getMethodName())) {
                return Class.forName(stackTraceElement.getClassName());
            }
        }
    } catch (ClassNotFoundException var6) {
    }

    return null;
}
```





**2、运行SpringApplication**

```java
public ConfigurableApplicationContext run(String... args) {
    // 记录应用的启动时间，统计启动的时间
    long startTime = System.nanoTime();
    // 1.创建引导上下文（Context环境）createBootstrapContext()，获取到所有之前的 bootstrappers
    // 挨个执行 intitialize() 来完成对引导启动器上下文环境设置
    DefaultBootstrapContext bootstrapContext = this.createBootstrapContext();
    // 定义一个ioc容器，最后返回的就是这个ioc容器
    ConfigurableApplicationContext context = null;
    // 2.让当前应用进入headless模式
    this.configureHeadlessProperty();
    // 3.获取所有 SpringApplicationRunListener（运行监听器）,为了方便所有Listener进行事件感知
    SpringApplicationRunListeners listeners = this.getRunListeners(args);
    // 4.遍历所有SpringApplicationRunListener，调用 starting 方法. 相当于通知所有对系统正在启动过程的组件，项目正在 starting。
    listeners.starting(bootstrapContext, this.mainApplicationClass);

    try {
        // 5.保存命令行参数 ApplicationArguments
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
        // 6.将前期读取的数据加载成一个环境对象，用来描述信息
        ConfigurableEnvironment environment = this.prepareEnvironment(listeners, bootstrapContext, applicationArguments);
        this.configureIgnoreBeanInfo(environment);
        // 打印banner信息，即SpringBoot的图标
        Banner printedBanner = this.printBanner(environment);
        // 7.创建ioc容器
        context = this.createApplicationContext();
        context.setApplicationStartup(this.applicationStartup);
        // 8.准备ApplicationContext IOC容器的基本信息
        this.prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
        // 9.刷新ioc容器，重新加载容器配置和重新实例化所有受管理的bean的方法
        this.refreshContext(context);
        // 空实现
        this.afterRefresh(context, applicationArguments);
        // 停止计时
        Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
        if (this.logStartupInfo) {
            // 日志信息，打印启动信息，包含启动时间
            (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), timeTakenToStartup);
        }

        // 10.调用listener的started方法，表示ioc容器创建完成
        listeners.started(context, timeTakenToStartup);
        // 11.调用所有的runner
        this.callRunners(context, applicationArguments);
    } catch (Throwable var12) {
        // 12.如果有异常，就执行listener的failed方法
        this.handleRunFailure(context, var12, listeners);
        throw new IllegalStateException(var12);
    }

    try {
        Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
        // 13.调用所有listener的ready方法
        listeners.ready(context, timeTakenToReady);
        // 返回ioc容器
        return context;
    } catch (Throwable var11) {
        // 14.如果有异常，就执行listener的failed方法
        this.handleRunFailure(context, var11, (SpringApplicationRunListeners)null);
        throw new IllegalStateException(var11);
    }
}
```



1、创建引导上下文（Context环境）`createBootstrapContext()`，获取到所有之前的`bootstrappers`，挨个执行`intitialize()`来完成对引导启动器上下文环境设置

```java
private DefaultBootstrapContext createBootstrapContext() {
    DefaultBootstrapContext bootstrapContext = new DefaultBootstrapContext();
    this.bootstrapRegistryInitializers.forEach((initializer) -> {
        initializer.initialize(bootstrapContext);
    });
    return bootstrapContext;
}
```



2、让当前应用进入`Headless`模式

> Headless模式是应用的一种配置模式。因为项目一般都是放在远程服务器上的，而服务器没有显示设备、键盘、鼠标等外设，因此无法进行输入输出，假如代码中调用了输入输出，就会发生错误，造成程序崩溃，`Hewadless`就模拟了这样的输入输出过程
>
> headless模式是一种声明：现在不要指望硬件的支持了，只能使用系统的运算能力来做些非可视化的处理工作。

```java
private void configureHeadlessProperty() {
    // this.headless为true
    // 这行代码实际就是 System.setProperty("java.awt.headless", "true");
    System.setProperty("java.awt.headless", System.getProperty("java.awt.headless", Boolean.toString(this.headless)));
}
```



3、获取所有<font color='red'> `SpringApplicationRunListenerRunListener` </font>（运行监听器）

`getSpringFactoriesInstances` 方法是去`spring.factories`找 `SpringApplicationRunListener`

```java
private SpringApplicationRunListeners getRunListeners(String[] args) {
    Class<?>[] types = new Class[]{SpringApplication.class, String[].class};
    return new SpringApplicationRunListeners(logger, this.getSpringFactoriesInstances(SpringApplicationRunListener.class, types, this, args), this.applicationStartup);
}

private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
    ClassLoader classLoader = this.getClassLoader();
    Set<String> names = new LinkedHashSet(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
    List<T> instances = this.createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
    AnnotationAwareOrderComparator.sort(instances);
    return instances;
}
```



4、遍历 所有的`SpringApplicationRunListener `并调用 `starting` 方法

```java
void starting(ConfigurableBootstrapContext bootstrapContext, Class<?> mainApplicationClass) {
    this.doWithListeners("spring.boot.application.starting", (listener) -> {
        listener.starting(bootstrapContext);
    }, (step) -> {
        if (mainApplicationClass != null) {
            step.tag("mainApplicationClass", mainApplicationClass.getName());
        }

    });
}

private void doWithListeners(String stepName, Consumer<SpringApplicationRunListener> listenerAction, Consumer<StartupStep> stepAction) {
    StartupStep step = this.applicationStartup.start(stepName);
    this.listeners.forEach(listenerAction);
    if (stepAction != null) {
        stepAction.accept(step);
    }

    step.end();
}
```



5、保存命令行参数

```JAVA
public DefaultApplicationArguments(String... args) {
    Assert.notNull(args, "Args must not be null");
    this.source = new DefaultApplicationArguments.Source(args);
    this.args = args;
}
```



6、准备环境

```java
private ConfigurableEnvironment prepareEnvironment(SpringApplicationRunListeners listeners, DefaultBootstrapContext bootstrapContext, ApplicationArguments applicationArguments) {
    // 返回或者创建基础环境信息对象，如：StandardServletEnvironment, StandardReactiveWebEnvironment
    ConfigurableEnvironment environment = this.getOrCreateEnvironment();
    // 配置环境信息对象,读取所有的配置源的配置属性值
    this.configureEnvironment(environment, applicationArguments.getSourceArgs());
    // 绑定环境信息
    ConfigurationPropertySources.attach(environment);
    // 监听器调用 listener.environmentPrepared()；通知所有的监听器当前环境准备完成
    listeners.environmentPrepared(bootstrapContext, environment);
    DefaultPropertiesPropertySource.moveToEnd(environment);
    Assert.state(!environment.containsProperty("spring.main.environment-prefix"), "Environment prefix cannot be set via properties.");
    this.bindToSpringApplication(environment);
    if (!this.isCustomEnvironment) {
        EnvironmentConverter environmentConverter = new EnvironmentConverter(this.getClassLoader());
        environment = environmentConverter.convertEnvironmentIfNecessary(environment, this.deduceEnvironmentClass());
    }

    ConfigurationPropertySources.attach(environment);
    // 返回当前的环境
    return environment;
}
```



7、创建ioc容器，这里会根据当前项目类型创建对应的ioc容器，我们的项目时基于`servlet`的，所以`this.webApplicationType`的值为`servlet`，当前创建的是`AnnotationConfigServletWebServerApplicationContext`

```java
protected ConfigurableApplicationContext createApplicationContext() {
    return this.applicationContextFactory.create(this.webApplicationType);
}
```



8、准备ioc容器的基本信息

```java
private void prepareContext(DefaultBootstrapContext bootstrapContext, ConfigurableApplicationContext context, ConfigurableEnvironment environment, SpringApplicationRunListeners listeners, ApplicationArguments applicationArguments, Banner printedBanner) {
    // 保存环境信息
    context.setEnvironment(environment);
    // ioc容器的后置处理流程
    this.postProcessApplicationContext(context);
    // 8.1 应用初始化器
    this.applyInitializers(context);
    // 8.2 遍历所有的 listener 调用 contextPrepared
    listeners.contextPrepared(context);
    bootstrapContext.close(context);
    if (this.logStartupInfo) {
        this.logStartupInfo(context.getParent() == null);
        this.logStartupProfileInfo(context);
    }

    // 设置beanFactory
    ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
    // 注册组件
    beanFactory.registerSingleton("springApplicationArguments", applicationArguments);
    if (printedBanner != null) {
        // 将banner注册进去
        beanFactory.registerSingleton("springBootBanner", printedBanner);
    }

    if (beanFactory instanceof AbstractAutowireCapableBeanFactory) {
        ((AbstractAutowireCapableBeanFactory)beanFactory).setAllowCircularReferences(this.allowCircularReferences);
        if (beanFactory instanceof DefaultListableBeanFactory) {
            ((DefaultListableBeanFactory)beanFactory).setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
        }
    }

    if (this.lazyInitialization) {
        context.addBeanFactoryPostProcessor(new LazyInitializationBeanFactoryPostProcessor());
    }

    context.addBeanFactoryPostProcessor(new SpringApplication.PropertySourceOrderingBeanFactoryPostProcessor(context));
    Set<Object> sources = this.getAllSources();
    Assert.notEmpty(sources, "Sources must not be empty");
    this.load(context, sources.toArray(new Object[0]));
    // 8.3 所有的监听器调用 contextLoaded 方法，通知容器已经加载
    listeners.contextLoaded(context);
}
```



8.1 用之前拿到的`ApplicationContextInitializer`，依次执行`initialize`方法

```java
protected void applyInitializers(ConfigurableApplicationContext context) {
    Iterator var2 = this.getInitializers().iterator();

    while(var2.hasNext()) {
        ApplicationContextInitializer initializer = (ApplicationContextInitializer)var2.next();
        Class<?> requiredType = GenericTypeResolver.resolveTypeArgument(initializer.getClass(), ApplicationContextInitializer.class);
        Assert.isInstanceOf(requiredType, context, "Unable to call initializer.");
        initializer.initialize(context);
    }

}
```



8.2 遍历所有的`listener`，调用`contextPrepared`方法

```java
void contextPrepared(ConfigurableApplicationContext context) {
    this.doWithListeners("spring.boot.application.context-prepared", (listener) -> {
        listener.contextPrepared(context);
    });
}
```



8.3 遍历所有的`listener`，调用`contextLoaded`方法，通知容器已加载

```java
void contextLoaded(ConfigurableApplicationContext context) {
    this.doWithListeners("spring.boot.application.context-loaded", (listener) -> {
        listener.contextLoaded(context);
    });
}
```



9、刷新ioc容器，重新加载容器配置和重新实例化所有受管理的bean的方法

```java
private void refreshContext(ConfigurableApplicationContext context) {
    if (this.registerShutdownHook) {
        shutdownHook.registerApplicationContext(context);
    }

    this.refresh(context);
}
```

```java
public void refresh() throws BeansException, IllegalStateException {
    synchronized(this.startupShutdownMonitor) {
        StartupStep contextRefresh = this.applicationStartup.start("spring.context.refresh");
        this.prepareRefresh();
        ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
        this.prepareBeanFactory(beanFactory);

        try {
            this.postProcessBeanFactory(beanFactory);
            StartupStep beanPostProcess = this.applicationStartup.start("spring.context.beans.post-process");
            this.invokeBeanFactoryPostProcessors(beanFactory);
            this.registerBeanPostProcessors(beanFactory);
            beanPostProcess.end();
            this.initMessageSource();
            this.initApplicationEventMulticaster();
            this.onRefresh();
            this.registerListeners();
            this.finishBeanFactoryInitialization(beanFactory);
            this.finishRefresh();
        } catch (BeansException var10) {
            if (this.logger.isWarnEnabled()) {
                this.logger.warn("Exception encountered during context initialization - cancelling refresh attempt: " + var10);
            }

            this.destroyBeans();
            this.cancelRefresh(var10);
            throw var10;
        } finally {
            this.resetCommonCaches();
            contextRefresh.end();
        }

    }
}
```



10、调用所有监听器的`started`方法，表示ioc容器创建完毕

```java
void started(ConfigurableApplicationContext context, Duration timeTaken) {
    this.doWithListeners("spring.boot.application.started", (listener) -> {
        listener.started(context, timeTaken);
    });
}
```



11、调用所有的`runner`

获取容器中的<font color='red'> `ApplicationRunner` </font>

获取容器中的<font color='red'> `CommandLineRunner` </font>

`ApplicationRunner` 和 `CommandLineRunner`，它们的 `run` 方法都提供了一个扩展点，让开发者能够在应用程序**启动后**执行一些自定义的初始化或准备工作，

我们通过命令行启动这个项目后边带的参数，被`applicationArguments`拿到，最终就是传到这里了

```java
private void callRunners(ApplicationContext context, ApplicationArguments args) {
    List<Object> runners = new ArrayList();
    // 获取容器中的ApplicationRunner
    runners.addAll(context.getBeansOfType(ApplicationRunner.class).values());
    // 获取容器中的CommandLineRunner
    runners.addAll(context.getBeansOfType(CommandLineRunner.class).values());
    // 合并所有的runner并按照@Order排序
    AnnotationAwareOrderComparator.sort(runners);
    Iterator var4 = (new LinkedHashSet(runners)).iterator();

    // 遍历所有的runner，调用run方法
    while(var4.hasNext()) {
        Object runner = var4.next();
        if (runner instanceof ApplicationRunner) {
            this.callRunner((ApplicationRunner)runner, args);
        }

        if (runner instanceof CommandLineRunner) {
            this.callRunner((CommandLineRunner)runner, args);
        }
    }

}
```



12、如果有异常，就调用`listener`的`failed`方法

```java
private void handleRunFailure(ConfigurableApplicationContext context, Throwable exception, SpringApplicationRunListeners listeners) {
    try {
        try {
            this.handleExitCode(context, exception);
            if (listeners != null) {
                listeners.failed(context, exception);
            }
        } finally {
            this.reportFailure(this.getExceptionReporters(context), exception);
            if (context != null) {
                context.close();
                shutdownHook.deregisterFailedApplicationContext(context);
            }

        }
    } catch (Exception var8) {
        logger.warn("Unable to close ApplicationContext", var8);
    }

    ReflectionUtils.rethrowRuntimeException(exception);
}
```





### 7.3 自定义事件监听组件

**1、ApplicationContextInitializer**

```java
package com.xrj.springboot06profile.listener;

import org.springframework.context.ApplicationContextInitializer;
import org.springframework.context.ConfigurableApplicationContext;

public class MyApplicationContextInitializer implements ApplicationContextInitializer {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        System.out.println("MyApplicationContextInitializer initialize");
    }
}
```



**2、ApplicationListener**

> 只要实现了`ApplicationListener`接口的监听器，并在`spring.factories`中注册，这个监听器就会监听SpringBoot启动的所有事件
>
> 如果实现`ApplicationListener`不指定具体事件，默认监听的是`ApplicationEvent`，即所有事件。如果指定了具体的事件【`ApplicationEvent`的子类，通过泛型指定】，那么只会在该事件触发后才会执行
>
> `ApplicationListener` 是 Spring 框架中的通用接口，用于监听各种 Spring 事件

```java
package com.xrj.springboot06profile.listener;

import org.springframework.context.ApplicationEvent;
import org.springframework.context.ApplicationListener;

public class MyApplicationListener implements ApplicationListener {
    @Override
    public void onApplicationEvent(ApplicationEvent event) {
        System.out.println("MyApplicationListener onApplicationEvent");
    }
}
```



**3、SpringApplicationRunListener**

> `SpringApplicationRunListener` 是 Spring Boot 中的专用接口，用于监听应用程序的启动和停止事件，处理应用程序的底层事件和阶段

```java
package com.xrj.springboot06profile.listener;

import org.springframework.boot.ConfigurableBootstrapContext;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.SpringApplicationRunListener;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.core.env.ConfigurableEnvironment;

import java.time.Duration;

public class MySpringApplicationRunListener implements SpringApplicationRunListener {

    private SpringApplication application;
    public MySpringApplicationRunListener(SpringApplication application, String[] args){
        this.application = application;
    }

    @Override
    public void starting(ConfigurableBootstrapContext bootstrapContext) {
        System.out.println("MySpringApplicationRunListenerRunListener starting 1");
    }

    @Override
    public void environmentPrepared(ConfigurableBootstrapContext bootstrapContext, ConfigurableEnvironment environment) {
        System.out.println("MySpringApplicationRunListenerRunListener environmentPrepared 2");
    }

    @Override
    public void contextPrepared(ConfigurableApplicationContext context) {
        System.out.println("MySpringApplicationRunListenerRunListener contextPrepared 3");
    }

    @Override
    public void contextLoaded(ConfigurableApplicationContext context) {
        System.out.println("MySpringApplicationRunListenerRunListener contextLoaded 4");
    }

    @Override
    public void started(ConfigurableApplicationContext context, Duration timeTaken) {
        System.out.println("MySpringApplicationRunListenerRunListener started 5");
    }

    @Override
    public void ready(ConfigurableApplicationContext context, Duration timeTaken) {
        System.out.println("MySpringApplicationRunListenerRunListener ready 6");
    }

    @Override
    public void failed(ConfigurableApplicationContext context, Throwable exception) {
        System.out.println("MySpringApplicationRunListenerRunListener failed 7");
    }
}
```



**4、ApplicationRunner**

```java
package com.xrj.springboot06profile.listener;

import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class MyApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("MyApplicationRunner run");
    }
}
```



**5、CommandLineRunner**

```java
package com.xrj.springboot06profile.listener;

import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class MyCommandLineRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("MyCommandLineRunner run");
    }
}
```



由于`ApplicationContextInitializer`、`ApplicationListener`和`SpringApplicationRunListener`是从`META-INF/spring.factories`文件中获取的，因此我们在类路径下创建`spring.factories`文件，并仿照`SpringBoot`的写法，将这三个组件放进去。其余两个组件都是在创建ioc容器后，从ioc容器中获取的，因此将他们放入ioc容器即可

**spring.factories**

```
org.springframework.boot.SpringApplicationRunListener=\
  com.xrj.springboot06profile.listener.MySpringApplicationRunListener

org.springframework.context.ApplicationContextInitializer=\
  com.xrj.springboot06profile.listener.MyApplicationContextInitializer
  
org.springframework.context.ApplicationListener=\
  com.xrj.springboot06profile.listener.MyApplicationListener
```
