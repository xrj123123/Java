# 一、Maven基础

## 1、仓库

> 仓库用于存储资源，包含各种jar包

仓库的分类：

- 本地仓库：自己电脑上存储资源的仓库，连接远程仓库获取资源
- 远程仓库：非本机电脑上的仓库，为本地仓库提供资源
  - 中央仓库：Maven团队维护，存储所有资源的仓库
  - 私服：部门/公司范围内存储资源的仓库，从中央仓库获取资源


私服的作用：

- 保存具有版权的资源，包含购买或自主研发的jar
  - 中央仓库中的jar都是开源的，不能存储具有版权的资源
- 一定范围内共享资源,仅对内部开放，不对外共享

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202306271133498.png" style="zoom:80%;" />







## 2、坐标

> maven中的坐标用于描述仓库中资源的位置。`https://repo1.maven.org/maven2/`



maven坐标的构成：

- groupId：定义当前maven项目隶属组织名称（通常是域名反写，例如org.mybatis）
- artifactId：定义当前maven项目名称
- version：定义当前项目版本号







## 3、创建maven项目

**maven项目构建命令**

`mvn compile`			编译

`mvn clear`				清理

`mvn test`				  测试

`mvn package`			打包

`mvn install`			安装到本地仓库





**maven创建web项目**

> web项目是需要放到tomcat容器中才可以运行的，所以我们的web项目一般都需要配置tomcat。在maven中，我们在pom.mxl文件中直接导入tomcat的插件，插件加载后直接运行tomcat即可。在`configuration`中可以配置tomcat的信息，端口号以及项目默认访问目录等



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202306271147130.png" style="zoom:80%;" />

```xml
<plugins>
  <plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.0</version>
    <configuration>
      <port>80</port>
      <path>/</path>
    </configuration>
  </plugin>
</plugins>
```







## 4、依赖管理

**依赖具有传递性**

- 直接依赖：在当前项目通过依赖配置建立的依赖关系
- 间接依赖：依赖的资源如果依赖其他资源，对其他资源就是间接依赖





**依赖传递冲突问题**

- 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
- 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
- 特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的【在pom文件中】





**可选依赖**

> 如果其他项目依赖于当前项目，该项目中的依赖如果将`optional`设置为`true`，那么这个资源不会传递过去

```xml
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.11</version>
  <optional>true</optional>
</dependency>
```





**排除依赖**

主动断开依赖的资源，排除依赖不需要指定版本

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```









**依赖范围**

依赖的jar默认情况下可以在任何地方使用，可以通过`scope`标签设定其作用范围

作用范围：

- 主程序范围有效（main文件夹范围内）
- 测试程序范围有效（test文件夹范围内）
- 是否参与打包（package指令范围内）

| scope               | 主代码 | 测试代码 | 打包 | 范例        |
| ------------------- | ------ | -------- | ---- | ----------- |
| **compile（默认）** | √      | √        | √    | log4j       |
| **test**            |        | √        |      | junit       |
| **provided**        | √      | √        |      | servlet-api |
| **runtime**         |        |          | √    | jdbc        |







**依赖范围传递性**

带有依赖范围的资源在进行传递时，作用范围会受到影响

| 间接依赖 \| 直接依赖 | compile | test | provided | runtime |
| -------------------- | ------- | ---- | -------- | ------- |
| **compile**          | complie | test | provided | runtime |
| **test**             |         |      |          |         |
| **provided**         |         |      |          |         |
| **runtime**          | runtime | test | provided | runtime |











