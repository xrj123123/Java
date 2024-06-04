# 正则表达式 RegExp

正则表达式是描述字符串模型的对象，用于对字符串模式匹配及检索替换

语法：var patt =new RegExp(pattern,modifiers);

或者：var patt =/pattern/modifiers;

<script type="text/javascript">


表示字符串中，是否包含字母e

var patt = new RegExp("e");

var patt=/e/;     	//也是正则表达式对象



表示要求字符串中，是否包含a或b或c

var patt=/ [abc] /;  



表示要求字符串中，是否包含任意小写字母

var patt=/ [a-z] /;  



表示要求字符串中，是否包含任意大写字母

var patt=/ [A-Z] /;  



表示要求字符串中，是否包含任意的数字

var patt=/ [0-9] /;  



查找给定集合外的任意字符

var patt=/ ^[abc] /;  



元字符

\w   元字符用于查找单词字符 

单词字符包括：a-z，A-Z，0-9，以及下划线

var patt=/ \w /;  



量词

var patt=/a+ /;  		//表示要求字符串中至少包含一个a

var patt=/a* /;  		//表示要求字符串中是否包含零个 或 多个a

var patt=/a？ /;  		//表示要求字符串中是否包含零个 或 一个a

var patt=/ a{3} /;  		//表示要求字符串中是否包含至少连续三个a

var patt=/ a{3，5} /;  	//表示要求字符串中是否包含最少三个连续的，最多5个连续的a

var patt=/ a{3，} /;  	//表示要求字符串中是否包含最少三个连续的a

var patt=/ a$ /;  		//表示要求字符串必须以a结尾

var patt=/ ^a /;  		//表示要求字符串必须以a开头

var patt=/ a{3，5} /;  	//表示要求字符串中是否 包含 最少三个连续的a （输入超过5个也没事）

var patt=/ ^a{3，5}$ /;  	//表示要求字符串从头到尾必须完全匹配



alter ( patt );			//输出/e/

var str="abcd";

alter( patt.test(str));  	 //输出false

</script>



# jQuery核心函数的四个作用

1.传入参数为 [函数] 时，在文档加载完成后执行这个函数

相当于window.onload=function(){}

2.传入参数为 [HTML字符串] 时，根据字符串创建元素节点对象

3.传入参数为 [选择器字符串] 时，根据这个字符串查找元素节点对象

$("#id属性值")';	id选择器，根据id查询选择对象

$("标签名");		标签名选择器，根据指定的标签名查询对象

$(".class属性值");	类型选择器，可以根据class属性值查询标签对象

4.传入参数为 [DOM对象] 时，将DOM对象包装为jQuery对象返回



jQuery的本质：jQuery是DOM对象的数组+jQuery提供的一系列功能函数

​    **jQuery不能使用DOM对象的属性和方法，DOM也不能使用jQuery对象的属性和方法**

### DOM对象和jQuery对象互转

##### 1.dom->jQuery

​		1.现有DOM对象

​		2.$(DOM对象) 就可以转换为jQuery对象

##### 2.jQuery->DOM

​		1.现有jQuery对象

​		2.jQuery对象[下标]去除相应的dom对象





# jQuery

1.使用jQuery一定要引入jQuery库

2.jQuery中的$，他是一个函数

3.怎么为按钮添加点击响应函数

（1）使用jQuery查询到标签对象

（2）使用标签对象.click（function(){})

### **jQuery方法：**

<script type="text/javascript">


$(function(){

var $btnObj = $("#btnId");		//表示按id查询标签对象

$btnObj.click(function(){			//绑定单击事件

alert("jQuery的单击事件“);

}）；

</script>

### js方法：

<script type="text/javascript">


window.onload = function(){

​			//1.查找#bj节点

​			document.getElementById("btn01").onclick = function () {

​				var bjObj = document.getElementById("bj");

​				alert(bjObj.innerHTML);

​			}

​	};

</script>

### **jQuery事件操作：**

$(function(){} );	和	window.onload=function(){} 的区别？

他们的触发顺序：

1.jQuery页面加载完成之后先执行

2.原生js的的页面加载完成之后

### **他们分别是在什么时候触发?**

1、jQuery的页面加载完成之后是浏览器的内核解析完页面的标签创建好DOM对象之后就会马上执行。

2、原生js的页面加载完成之后，除了要等浏览器内核解析完标签创建好DOM对象，还要等标签显示时需要的内容加载完成。

### **他们执行的次数？**

1.原生js的页面加载完成之后，只执行最后一次的复制函数

2.jQuery的页面加载完成之后是全部把注册的function函数，依次顺序执行



# XML

### 1. XML是可扩展标记性语言

### 2.xml的作用？

1.用来保存数据，而且这些数据具有自我描述性

2.他还可以作为项目或者模块的配置文件

3.还可以作为网络传输的数据格式(现在以json为主）







# tomcat

### **目录介绍：**

bin：专门用来存放tomcat服务器的可执行程序

conf：专门用来存放tomcat服务器的配置文件

lib：专门用来存放tomcat服务器jar包

logs：专门用来存放tomcat服务器运行时输出的日志信息

temp：专门用来存放tomcat服务器运行时产生的临时数据

webapps：专门用来存放部署的web工程

works：是tomcat工作时的目录，用来存放tomcat运行时jsp翻译为Servlet的源码，和Session钝化的			 

​      目录

### **如何启动tomcat服务器**

找到tomcat目录下bin目录下的startup.bat文件，双击就可以打开tomcat服务器

如何测试tomcat服务器启动成功？

打开浏览器，输入

1.http://localhost:8080

2.http://127.0.0.1:8080

3.http://真实ip:8080

### **如何修改tomcat的端口号**

mysql默认的端口号是：3306

tmocat默认的端口号是：8080

找到tomcat目录下的conf目录，找到server.配置文件，69行

### **如何部署web工程到tomcat中**

第一种方法：1.只需要把web工程的目录拷贝到tomcat的webapps目录下即可

2.如何访问项目？http://localhost:8080（访问到tomcat目录下的webapps目录）

http://localhost:8080/工程

第二种方法：找到tomcat下的目录conf目录/catalina/localhost/下，创建如下配置文件

Context表示一个工程上下文

pat表示工程的访问路径：/abc（abc是刚创建的xml文件）（不要写中文）

docBase表示你的工程目录

<Context path="/web03" docBase="E:\IdeaProjects\JavaWeb\out\artifacts\web03_war_exploded" />

### **ROOT的工程访问及默认index.html页面的访问**

当我们在浏览器地址栏中输入访问地址http://ip:port/  没有工程名的时候，默认访问的是ROOT工程

当我们在浏览器地址栏中输入访问地址http://ip:port/工程名  没有资源名的时候，默认访问index.html页面

### **IDEA整合Tomcat服务器**

File->Settings->Build,Execution,Deployment->Application Servers

### **IDEA中动态web工程的操作**

### **IDEA中如何创建动态web工程**

选中项目，创建一个新模块，选择Java EE Web application

### **动态web工程目录介绍**

05-web

src ：存自己编写的java源代码

web：web目录专门用来存放web工程的资源文件。比如html页面、css文件、js文件等等

WEB-INF：是一个受服务器保护的目录，浏览器无法直接访问到此目录的内容

web.xml：是整个动态web工程的配置部署描述文件，可以在这里配置很多web工程的组件，比如	

Servlet程序、  Filter过滤器、Listener监听器、Session超时等等

lib：存放第三方的jar包（IDEA还需要自己配置导入）

### **如何给javaweb工程添加额外jar包**

1.可以打开项目结构菜单，Libraries，添加一个java类库

2.添加你类库需要的jar包文件

3.选择你添加的类库，给哪个模块使用

4.选择Artifacts选项，将类库添加到打包部署中

### **如何在IDEA中部署工程到Tomcat上运行**

1.建议修改web工程对应的Tomcat运行实例名称

2.确认你的Tomcat实例中有你要部署运行的web工程模块

3.可以修改你的Tomcat实例启动后默认的访问地址

4.在IDEA中如何运行和停止Tomcat实例，05_web旁边小三角 

### **修改热部署**

Edit Configurations->On Frame deactivation  update class and resources

这样修改页面，网页会直接修改





# Servlet



### **Servlet技术**

a.什么是Servlet

1.servlet是JavaEE规范之一，规范就是接口

2.Servlet是JavaWeb三大组件之一，三大组件分别是Servlet程序、Filter过滤器、Listener监听器

3.Servlet是运行在服务器上的一个java小程序，他可以接收客户端发过来的请求，并响应数据给客户

端

b.手动实现Servlet程序

1.编写一个类去实现Servlet接口

2.实现service方法，处理请求，并响应数据

3.到web.xml中去配置servlet程序的访问地址 

### Servlet生命周期

1.执行Servlet构造器方法

2.执行init初始化方法

第一二步，是在第一次访问的时候创建Servlet程序会调用

3.执行service方法

第三步，每次访问都会调用

4.执行destroy销毁方法

第四步，在web工程停止的时候调用



### **get和post请求的分发处理**

**通过继承HttpServlet实现Servlet程序**

一般在实际项目开发中，都是使用继承HttpServlet类的方式Servlet程序

1.编写一个类去继承HttpServlet类

2.根据业务需要重写doGet或doPost方法

3.到web.xml中配置Servlet程序的访问地址

### **使用IDEA创建Servlet程序**

选择类的包名，然后new ->servlet

### **Servlet类的继承体系**

![img](https://cdn.jsdelivr.net/gh/xrj123123/Images/1.png)

 

### **ServletConfig类**

ServletConfig类从类名上来看，就知道是Servlet程序的配置信息类

Servlet程序和ServletConfig对象都是由Tomcat负责创建，我们负责使用

Servlet程序默认是第一次访问的时候创建，ServletConfig是每个Servlet程序创建时，就创建一个对应的ServletConfig对象

### **ServletConfig类的三大作用：**

1.可以获取Servlet程序的别名servlet-name的值

2.获取初始化参数init-param

3.获取ServletContext对象

### **ServletContext类**

##### a.什么是ServletContext？

1.ServletContext是一个接口，它表示Servlet上下文对象

2.一个web工程，只有一个ServletContext对象实例

3.ServletContext对象是一个域对象

4.ServletContext是在web工程部署启动的时候创建，在web工程停止的时候销毁

域对象：是可以像map一样存取数据的对象，这里的域指的是存取数据的操作范围

这里的域指的是存取数据的操作范围，整个web工程

​						存数据					取数据					删除数据

map			     put()			    		 get()					remove()

域对象		     setAttribute()	     getAttribute()		removeAttribute()

##### b.ServletContext类的四个作用？

1.获取web.xml中配置的上下文参数context-param

2.获取当前的工程路径，格式  :/工程路径

3.获取工程部署后在服务器磁盘上的绝对路径

4.像map一样存储数据





# http协议

### **a)http协议**

所谓http协议，就是指，客户端和服务器之间通信时，发送的数据，需要遵守的规则

http中的数据叫报文

### **b)请求的http协议格式**

客户端给服务器发送数据叫请求

服务器给客户端回传数据叫响应

请求又分为get请求和post请求

##### **1.get请求**

1.请求行

（1）请求的方式					GET

（2）请求的资源路径[+?+请求参数]

（3）请求的协议版本号				HTTP/1.1	

2.请求头		

key：value	组成		不同的键值对，表示不同的含义

##### GET请求有哪些？

1.form标签  method=get

2.a标签

3.link标签引入css

4.Script标签引入js文件

5.img标签引入图片

6.iframe引入html页面

7.在浏览器地址栏中输入地址后敲回车

##### **2.post请求**

1.请求行

（1）请求的方式					POS T

（2）请求的资源路径[+?+请求参数]

（3）请求的协议版本号				HTTP/1.1	

2.请求头		

key：value	组成		不同的键值对，表示不同的含义

空行

 	3.请求体	==>>> 就是发送给服务器的数据

##### POST请求有哪些？

8.form标签 method=post

### **c)响应的http协议格式**

1.请求行

（1）响应的协议和版本号		http/1.1

（2）响应状态码				200

（3）响应状态描述符			OK

2.响应头

key：value	组成		不同的键值对，表示不同的含义

空行

3.响应体	 --->>>		就是回传给客户端的数据

### **d)常见的响应码说明**

200			表示请求成功

302			表示请求重定向

404			表示请求服务器已经收到了，但是你要的数据不存在（请求地址错误）

500			表示服务器已经收到请求，但是服务器内部错误（代码错误）





# servlet2

### 1.HttpServletRequest类

##### a)HttpServletRequest类有什么作用？

每次只要有请求进入tomcat服务器，tomcat服务器就会把请求过来的HTTP协议信息解析到封装好的Request对象中，

然后传递到service方法（doGet和doPost）中给我们使用。我们可以通过HttpServletRequest对象，获取到所有请求的信息。



##### b)HttpServletRequest类的常用方法？

i. getRequestURI() 																  获取请求的资源路径 

ii. getRequestURL() 																获取请求的统一资源定位符（绝对路径） 

iii. getRemoteHost() 															   获取客户端的 ip 地址 

iv. getHeader() 																		获取请求头 

v. getParameter() 																   获取请求的参数 

vi. getParameterValues() 													  获取请求的参数（多个值的时候使用） 

vii. getMethod() 																	  获取请求的方式 GET 或 POST 

viii. setAttribute(key, value); 									   		 设置域数据 

ix. getAttribute(key); 															 获取域数据 

x. getRequestDispatcher() 												   获取请求转发对象 



#####  c)请求转发

什么是请求的转发？

请求转发是指，服务器收到请求后，从一个资源跳转到另一个服务器资源的操作叫请求转发。



![image-20211202151021004](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211202151021004.png)



请求转发的特点：1.浏览器地址栏没有变化

​							   2.他们是一次请求

​							   3.他们共享Request域中的数据

​							   4.可以转发到WEB-INF目录下

​							   5.不可以访问工程以外的资源



##### d)base标签的作用

![image-20211202171203583](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211202171203583.png)



##### e)Web中的相对路径和绝对路径

在 javaWeb 中，路径分为相对路径和绝对路径两种： 

相对路径是： 

. 	  表示当前目录 

.. 	表示上一级目录 

资源名 

表示当前目录/资源名 

绝对路径： 

http://ip:port/工程路径/资源路径 

在实际开发中，路径都使用绝对路径，而不简单的使用相对路径。 

1、绝对路径 

2、base+相对 



##### f)web中 / 斜杠的不同意义

在 web 中 / 斜杠 是一种绝对路径。 

/ 斜杠 如果被浏览器解析，得到的地址是：http://ip:port/ 

<a href =" /" >斜杠<a > 

/ 斜杠 如果被服务器解析，得到的地址是：http://ip:port/工程路径 

1、<**url-pattern**>/servlet1</**url-pattern**> 

2、servletContext.getRealPath(“/”); 

3、request.getRequestDispatcher(“/”); 

特殊情况： response.sendRediect(“/”); 

把斜杠发送给浏览器解析。得到 http://ip:port/





### 2.**HttpServletResponse** 类

##### **a)HttpServletResponse** **类的作用** 

HttpServletResponse 类和 HttpServletRequest 类一样。每次请求进来，Tomcat 服务器都会创建一个 Response 对象传递给 Servlet 程序去使用。HttpServletRequest 表示请求过来,HttpServletResponse 表示所有响应的信息， 

我们如果需要设置返回给客户端的信息，都可以通过 HttpServletResponse 对象来进行设置



##### b)两个输出流的说明。 

字节流 

getOutputStream(); 

常用于下载（传递二进制数据） 



字符流 

getWriter(); 

常用于回传字符串（常用） 

两个流同时只能使用一个。 

使用了字节流，就不能再使用字符流，反之亦然，否则就会报错。 



##### c)如何往客户端回传数据

要求 ： 往客户端回传 字符串 数据。 

**public class** ResponseIOServlet **extends** HttpServlet { 

@Override 

**protected void** doGet(HttpServletRequest req, HttpServletResponse resp) **throws** ServletException, 

IOException { 

*//* 

*要求 ： 往客户端回传 字符串 数据。* 

PrintWriter writer = resp.getWriter(); 

writer.write(**"response's content!!!"**); 

} 

} 





##### d)响应的乱码解决

解决响应中文乱码方案一（不推荐使用）： 

*//* *设置服务器字符集为* *UTF-8* 

resp.setCharacterEncoding(**"UTF-8"**); 

*//* *通过响应头，设置浏览器也使用* *UTF-8* *字符集* 

resp.setHeader(**"Content-Type"**, **"text/html; charset=UTF-8"**);



​	解决响应中文乱码方案二（推荐）： 

*//* *它会同时设置服务器和客户端都使用* *UTF-8* *字符集，还设置了响应头* 

*//* *此方法一定要在获取流对象之前调用才有效* 

resp.setContentType(**"text/html; charset=UTF-8"**); 





##### e)请求重定向

请求重定向，是指客户端给服务器发请求，然后服务器告诉客户端说。我给你一些地址。你去新地址访问。叫请求 重定向（因为之前的地址可能已经被废弃）。 

请求重定向的第一种方案： 

*//* *设置响应状态码* *302* *，表示重定向，（已搬迁）* 

resp.setStatus(302); 

*//* *设置响应头，说明 新的地址在哪里* 

resp.setHeader(**"Location"**, **"http://localhost:8080"**); 



请求重定向的第二种方案（推荐使用）： 

resp.sendRedirect(**"http://localhost:8080"**)



![image-20211203212650821](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211203212650821.png)





# jsp

### 1.什么是 jsp，它有什么用?

jsp 的全称是 java server pages。Java 的服务器页面。 

jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据。 

因为 Servlet 程序回传 html 页面数据是一件非常繁锁的事情。开发成本和维护成本都极高。 

Servlet 回传 html 页面数据的代码

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211205210627833.png" alt="image-20211205210627833" style="zoom:75%;" />





jsp 回传一个简单 html 页面的代码：

![image-20211205210759818](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211205210759818.png)





jsp 的小结： 

##### 1、如何创建 jsp 的页面?

​	在web下new jsp



##### 2、jsp 如何访问： 

jsp 页面和 html 页面一样，都是存放在 web 目录下。访问也跟访问 html 页面一样。 

比如：

在 web 目录下有如下的文件： 

web 目录

​	a.html 页面 

​	访问地址是 =======>>>>>> http://ip:port/工程路径/a.html 

​	b.jsp 页面 

​	访问地址是 =======>>>>>> http://ip:port/工程路径/b.jsp



### Servlet 程序 

![image-20211205212329425](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211205212329425.png)

总结：通过翻译的 java 源代码我们就可以得到结果：jsp 就是 Servlet 程序。 

大家也可以去观察翻译出来的 Servlet 程序的源代码，不难发现。其底层实现，也是通过输出流。把 html 页面数据回传 

给客户端





### 3.jsp的三种语法

##### a)jsp头部的page指令

jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为。 

<%@ **page** **contentType**="**text/html;charset=UTF-8**" **language**="**java**" %> 

i. language 属性 	表示 jsp 翻译后是什么语言文件。暂时只支持 java。 

ii. contentType 属性 	表示 jsp 返回的数据类型是什么。也是源码中 response.setContentType()参数值 

iii. pageEncoding 属性 	表示当前 jsp 页面文件本身的字符集。 

iv. import 属性 	跟 java 源代码中一样。用于导包，导类。 



========================两个属性是给 out 输出流使用============================= 

v. autoFlush 属性 	设置当 out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是 true。 

vi. buffer 属性 		设置 out 缓冲区的大小。默认是 8kb 

缓冲区溢出错误：

![image-20211206151653919](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211206151653919.png)







========================两个属性是给 out 输出流使用============================= 

vii. 	errorPage 属性 			设置当 jsp 页面运行时出错，自动跳转去的错误页面路径。 

*<!--* *errorPage* *表示错误后自动跳转去的路径* *<br/>* *这个路径一般都是以斜杠打头，它表示请求地址为* *http://ip:port/**工程路径**/**映射到代码的* *Web* *目录* 

*-->* 

viii. isErrorPage 属性 ，设置当前 jsp 页面是否是错误信息页面。默认是 false。如果是 true 可以获取异常信息。 

ix. session 属性 ，设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true。 

x. extends 属性 ，设置 jsp 翻译出来的 java 类默认继承谁。





##### **b)jsp** **中的常用脚本** 

###### **i.** 声明脚本(极少使用）

声明脚本的格式是： **<%!** 声明 java 代码  %>

作用：可以给 jsp 翻译出来的 java 类定义属性和方法甚至是静态代码块。内部类等。 

练习：

 1、声明类属性 

2、声明 static 静态代码块 

3、声明类方法 

4、声明内部类 



![image-20211206155612565](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211206155612565.png)

![image-20211206155621473](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211206155621473.png)

![image-20211206155721316](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211206155721316.png)



###### **ii.** 表达式脚本常用）

表达式脚本的格式是：**<%=**表达式**%>** 

表达式脚本的作用是：在 jsp 页面上输出数据。 

表达式脚本的特点： 

1、所有的表达式脚本都会被翻译到_jspService() 方法中 

2、表达式脚本都会被翻译成为 out.print()输出到页面上 

3、由于表达式脚本翻译的内容都在_jspService() 方法中,所以_jspService()方法中的对象都可以直接使用。 

4、表达式脚本中的表达式不能以分号结束。 

练习：

1. 输出整型 

2. 输出浮点型 

3. 输出字符串 

4. 输出对象





###### **iii.** **代码脚本** 

代码脚本的格式是： 

**<%** 

​	java 语句 

%>

代码脚本的作用是：可以在 jsp 页面中，编写我们自己需要的功能（写的是 java 语句）。 

代码脚本的特点是： 

1、代码脚本翻译之后都在_jspService 方法中 

2、代码脚本由于翻译到_jspService()方法中，所以在_jspService()方法中的现有对象都可以直接使用。 

3、还可以由多个代码脚本块组合完成一个完整的 java 语句。 

4、代码脚本还可以和表达式脚本一起组合使用，在 jsp 页面上输出数据 

练习：

1. 代码脚本----if 语句 

2. 代码脚本----for 循环语句 

3. 翻译后 java 文件中_jspService 方法内的代码都可以写 



##### **c)jsp** **中的三种注释** 

###### **i. html** **注释** 

*<!--* *这是* *html* *注释* *-->*

html 注释会被翻译到 java 源代码中。在_jspService 方法里，以 out.writer 输出到客户端。 

###### **ii. java** **注释** 

**<%** 

*//* *单行* *java* *注释* 

*/** *多行* *java* *注释* **/* 

**%>** 

java 注释会被翻译到 java 源代码中。 

###### **iii. jsp** **注释** 

*<%--* *这是* *jsp* *注释* *--%>* 

jsp 注释可以注掉，jsp 页面中所有代码。







### **4.jsp** **九大内置对象** 

jsp 中的内置对象，是指 Tomcat 在翻译 jsp 页面成为 Servlet 源代码后，内部提供的九大对象，叫内置对象。

![image-20211206170202219](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211206170202219.png)



### 5.jsp 四大域对象

四个域对象分别是： 

1.pageContext (PageContextImpl 类) 			当前 jsp 页面范围内有效 



2.request (HttpServletRequest 类)、 			一次请求内有效 



3.session (HttpSession 类)、 					   	一个会话范围内有效（打开浏览器访问服务器，直到关闭浏览器） 



4.application (ServletContext 类) 					整个 web 工程范围内都有效（只要 web 工程不停止，数据都在） 



域对象是可以像 Map 一样存取数据的对象。四个域对象功能一样。不同的是它们对数据的存取范围。 



虽然四个域对象都可以存取数据。在使用上它们是有优先顺序的。 

四个域在使用的时候，优先顺序分别是，他们从小到大的范围的顺序。 

pageContext ====>>> request ====>>> session ====>>> application





### 6.jsp中的out **输出和** response.getWriter输出的区别

response 中表示响应，我们经常用于设置返回给客户端的内容（输出） 

out 也是给用户做输出使用的。 



![image-20211207172042445](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211207172042445.png)



由于 jsp 翻译之后，底层源代码都是使用 out 来进行输出，所以一般情况下。我们在 jsp 页面中统一使用 out 来进行输出。避 

免打乱页面输出内容的顺序。 

out.write() 输出字符串没有问题 

out.print() 输出任意数据都没有问题（都转换成为字符串后调用的 write 输出） 

**深入源码，浅出结论：在 jsp 页面中，可以统一使用 out.print()来进行输出** 



### **7.jsp** **的常用标签** 



##### a)jsp静态包含

示例说明：

<%@ include file=""%> 就是静态包含

file 属性指定你要包含的 jsp 页面的路径 

地址中第一个斜杠 / 表示为 http://ip:port/**工程路径**/ 映射到代码的 web 目录 

静态包含的特点： 

1、静态包含不会翻译被包含的 jsp 页面。 

2、静态包含其实是把被包含的 jsp 页面的代码拷贝到包含的位置执行输出。

<%@ **include** **file**="**/include/footer.jsp**"%> 





##### **b)jsp** **动态包含** 

示例说明： 

<jsp:include page=""> </ jsp:include>

*这是动态包含* 

*page* *属性是指定你要包含的* *jsp* *页面的路径* 

*动态包含也可以像静态包含一样。把被包含的内容执行输出到包含位置* 

动态包含的特点： 

1、动态包含会把包含的 jsp 页面也翻译成为 java 代码 

2、动态包含底层代码使用如下代码去调用被包含的 jsp 页面执行输出。 

JspRuntimeLibrary.include(request, response, "/include/footer.jsp", out, false); 

3、动态包含，还可以传递参数

*--%>* 

main：

```java
<jsp:include page="/include/footer.jsp">
        <jsp:param name="username" value="xrj"/>
        <jsp:param name="password" value="root"/>
</jsp:include>
```

footer：

```java
<%=request.getParameter("password")%>
```

动态包含的底层原理

![image-20211207190433220](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211207190433220.png)





##### **c)jsp** 标签-转发 

示例说明： 

```java
<%--
<jsp:forward page=""></jsp:forward> 这就是请求转发标签，功能就是请求转发
page属性设置请求转发的路径
--%>
```

```java
<jsp:forward page="/scope2.jsp"></jsp:forward>
```



请求转发的说明

![img](file:///C:\Users\lenovo\AppData\Roaming\Tencent\Users\1739736649\QQ\WinTemp\RichOle\231~XV53OX44DO61`_VRT]8.png)





##### 8、什么是 Listener 监听器？ 

1、Listener 监听器它是 JavaWeb 的三大组件之一。JavaWeb 的三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监 

听器。

2、Listener 它是 JavaEE 的规范，就是接口 

3、监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理。 



###### 8.1、ServletContextListener 监听器

ServletContextListener 它可以监听 ServletContext 对象的创建和销毁。 

ServletContext 对象在 web 工程启动的时候创建，在 web 工程停止的时候销毁。 

监听到创建和销毁之后都会分别调用 ServletContextListener 监听器的方法反馈。 

两个方法分别是： 

![image-20211207201552687](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211207201552687.png)





###### 8.2如何使用 ServletContextListener 监听器监听 ServletContext 对象。 

使用步骤如下： 

1、编写一个类去实现 ServletContextListener 

2、实现其两个回调方法 

3、到 web.xml 中去配置监听器





# EL 表达式



### a)什么是EL 表达式，EL 表达式的作用?

EL 表达式的全称是：Expression Language。是表达式语言。 

EL 表达式的什么作用：EL 表达式主要是代替 jsp 页面中的表达式脚本在 jsp 页面中进行数据的输出。 

因为 EL 表达式在输出数据的时候，要比 jsp 的表达式脚本要简洁很多。



```java
<%
    request.setAttribute("key","值");
%>
表达式脚本输出key的值是：<%=request.getAttribute("key1")==null?"":request.getAttribute("key1")%> <br>
EL表达式输出key的值：${key1}
```



EL 表达式的格式是：${表达式} 

EL 表达式在输出 null 值的时候，输出的是空串。jsp 表达式脚本输出 null 值的时候，输出的是 null 字符串。



### b)EL 表达式搜索域数据的顺序 

EL 表达式主要是在 jsp 页面中输出数据。 

主要是输出域对象中的数据。 

当四个域中都有相同的 key 的数据的时候，EL 表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。





### c)EL 表达式输出 Bean 的普通属性，数组属性。List 集合属性，map 集合属性







### d)EL 表达式——运算

语法：${ 运算表达式 } ， EL 表达式支持如下运算符： 



##### 1）关系运算

![image-20211208170008454](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211208170008454.png)



##### 2）逻辑运算

![image-20211208170027926](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211208170027926.png)





##### 3）算数运算

![image-20211208170441641](D:\徐睿杰1808112025\note\image-20211208170441641.png)





##### 4） empty 运算 

empty 运算可以判断一个数据是否为空，如果为空，则输出 true,不为空输出 false。 

以下几种情况为空： 

1、值为 null 值的时候，为空 

2、值为空串的时候，为空 

3、值是 Object 类型数组，长度为零的时候 

4、list 集合，元素个数为零 

5、map 集合，元素个数为零



##### 5）三元运算

表达式 1？表达式 2：表达式 3 

如果表达式 1 的值为真，返回表达式 2 的值，如果表达式 1 的值为假，返回表达式 3 的值。示例：

${ 12 != 12 ? "国哥帅呆":"国哥又骗人啦" }



##### 6）“.”点运算 和[] **中括号运算符** 

.点运算，可以输出 Bean 对象中某个属性的值。 

[]中括号运算，可以输出有序集合中某个元素的值。 

并且[]中括号运算，还可以输出 map 集合中 key 里含有特殊字符的 key 的值。



### **e)EL** 表达式的 **11** **个隐含对象** 

EL 表达式中 11 个隐含对象，是 EL 表达式中自己定义的，可以直接使用。 

变量 																类型 														作用 

pageContext 										PageContextImpl 					它可以获取 jsp 中的九大内置对象 



pageScope 										   Map<String,Object> 				它可以获取 pageContext 域中的数据 

requestScope 									  Map<String,Object> 				它可以获取 Request 域中的数据 

sessionScope 									   Map<String,Object> 			    它可以获取 Session 域中的数据 

applicationScope 								 Map<String,Object> 				它可以获取 ServletContext 域中的数据 



param 													Map<String,String> 				 它可以获取请求参数的值 

paramValues 										Map<String,String[]> 			   它也可以获取请求参数的值，获取多个值的时候使用。 



header 												   Map<String,String> 				 它可以获取请求头的信息 

headerValues 									   Map<String,String[]> 			   它可以获取请求头的信息，它可以获取多个值的情况 



cookie 													Map<String,Cookie> 				它可以获取当前请求的 Cookie 信息initParam 

initParam											   Map<String,String> 				  它可以获取在 web.xml 中配置的<context-param>上下文参数 





##### **i. EL** **获取四个特定域中的属性** 

pageScope 			====== 			pageContext 域 

requestScope 	   ====== 			Request 域 

sessionScope         ====== 			Session 域 

applicationScope  ====== 			 ServletContext 域



##### **ii. pageContext** **对象的使用** 

1. 协议： 

2. 服务器 ip： 

3. 服务器端口： 

4. 获取工程路径： 

5. 获取请求方法： 

6. 获取客户端 ip 地址： 

7. 获取会话的 id 编号：



##### iii. EL 表达式其他隐含对象的使用 

param 						Map<String,String> 					它可以获取请求参数的值 



paramValues 			Map<String,String[]> 				  它也可以获取请求参数的值，获取多个值的时候使用。









# JSTL 标签库



JSTL 标签库 全称是指 JSP Standard Tag Library JSP 标准标签库。是一个不断完善的开放源代码的 JSP 标签库。

EL 表达式主要是为了替换 jsp 中的表达式脚本，而标签库则是为了替换代码脚本。这样使得整个 jsp 页面变得更佳简洁。





JSTL 由五个不同功能的标签库组成。 

功能范围 															URI 																					前缀 

核心标签库--重点							http://java.sun.com/jsp/jstl/core														c 

格式化 											http://java.sun.com/jsp/jstl/fmt 														 fmt 

函数 											    http://java.sun.com/jsp/jstl/functions 											  fn 

数据库(不使用) 							  http://java.sun.com/jsp/jstl/sql 														  sql 

XML(不使用) 								  http://java.sun.com/jsp/jstl/xml 														 x 





在 jsp 标签库中使用 taglib 指令引入标签库 

CORE 标签库 

<%@ taglib prefix=*"c"* uri=*"http://java.sun.com/jsp/jstl/core"* %> 



XML 标签库

<%@ taglib prefix=*"x"* uri=*"http://java.sun.com/jsp/jstl/xml"* %> 



FMT 标签库 

<%@ taglib prefix=*"fmt"* uri=*"http://java.sun.com/jsp/jstl/fmt"* %> 



SQL 标签库 

<%@ taglib prefix=*"sql"* uri=*"http://java.sun.com/jsp/jstl/sql"* %> 



FUNCTIONS 标签库 

<%@ taglib prefix=*"fn"* uri=*"http://java.sun.com/jsp/jstl/functions"* %>



### JSTL 标签库的使用步骤 

1、先导入 jstl 标签库的 jar 包。 

taglibs-standard-impl-1.2.1.jar 

taglibs-standard-spec-1.2.1.jar 



2、第二步，使用 taglib 指令引入标签库。 

<%@ **taglib** **prefix**="**c**" **uri**="**http://java.sun.com/jsp/jstl/core**" %> 



### core 核心库使用 

##### i. <c:set />（使用很少）

作用：set 标签可以往域中保存数据



##### ii. <c:if /> 

if 标签用来做 if 判断。



##### iii. `<c:choose> <c:when> <c:otherwise>`标签

作用：多路判断。跟 switch ... case .... default 非常接近



```
choose标签开始选择判断
when标签表示每一种判断情况
    test属性表示当前这种判断情况的值
otherwise标签表示剩下的情况

<c:choose> <c:when> <c:otherwise>标签使用时需要注意的点：
    1、标签里不能使用 html 注释，要使用 jsp 注释
    2、when 标签的父标签一定要是 choose 标签
```



##### iv. <c:forEach /> 

作用：遍历输出使用。

###### 1.遍历 1 到 10，输出



###### 2.  遍历 Object 数组 



###### 3. 遍历 Map 集合



4.遍历 List 集合---list 中存放 Student 类，有属性：编号，用户名，密码，年龄







# 文件的上传和下载



文件的上传和下载，是非常常见的功能。很多的系统中，或者软件中都经常使用文件的上传和下载。 

比如：QQ 头像，就使用了上传。 

邮箱中也有附件的上传和下载功能。 

OA 系统中审批有附件材料的上传。



### 1、文件的上传介绍

1、要有一个 form 标签，method=post 请求 

2、form 标签的 encType 属性值必须为 multipart/form-data 值 

3、在 form 标签中使用 input type=file 添加上传的文件 

4、编写服务器代码（Servlet 程序）接收，处理上传的数据。 





encType=multipart/form-data 表示提交的数据，以多段（每一个表单项一个数据段）的形式进行拼 

接，然后以二进制流的形式发送给服务器







##### 1.1、 文件上传，HTTP 协议的说明



![image-20211210192612808](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211210192612808.png)





##### 1.2、commons-fileupload.jar 常用 API 介绍说明 

**commons-fileupload.jar** **需要依赖** **commons-io.jar** **这个包，所以两个包我们都要引入。** 

**第一步，就是需要导入两个** **jar** **包：** 

commons-fileupload-1.2.1.jar 

commons-io-1.4.jar 



**commons-fileupload.jar** **和** **commons-io.jar** **包中，我们常用的类有哪些？** 

ServletFileUpload 类，用于解析上传的数据。 

FileItem 类，表示每一个表单项。 



boolean ServletFileUpload.isMultipartContent(HttpServletRequest request); 

判断当前上传的数据格式是否是多段的格式。 



public List<FileItem> parseRequest(HttpServletRequest request) 

解析上传的数据 



boolean FileItem.isFormField() 

判断当前这个表单项，是否是普通的表单项。还是上传的文件类型。 

true 表示普通类型的表单项 

false 表示上传的文件类型 



String FileItem.getFieldName() 

获取表单项的 name 属性值



String FileItem.getString() 

获取当前表单项的值。 



String FileItem.getName(); 

获取上传的文件名 



void FileItem.write( file ); 

将上传的文件写到 参数 file 所指向的硬盘位置 。







##### 1.3、fileupload **类库的使用：** 

上传文件的表单： 

```
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2021/12/10
  Time: 18:17
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/09_EL_JSTL/uploadServlet" method="post" enctype="multipart/form-data">
        用户名：<input type="text" name="username"/><br>
        头像：<input type="file" name="photo"> <br>
        <input type="submit" value="上传">
    </form>

</body>
</html>
```



解析上传的数据的代码： 

```
package com.xrj.servlet;

import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.FileUploadException;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;

import javax.servlet.ServletException;
import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.util.List;

/**
 * @auther xuruijie
 * @date 2021/12/10 18:23
 */
public class Uploadervlet extends HttpServlet {
    /*
     * 用来处理上传的数据
     * @param
     * @return
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //1.先判断上传的数据是否是多段数据（只有是多段数据才是文件上传的）
        if(ServletFileUpload.isMultipartContent(req)){

            //创建FileItemFactory工厂实现类
            FileItemFactory fileItemFactory=new DiskFileItemFactory();
            //创建用于解析上传数据的工具类ServletFileUpload
            ServletFileUpload servletFileUpload=new ServletFileUpload(fileItemFactory);

            try {
                //解析上传的数据，得到每一个表单项FileItem
                List<FileItem> list = servletFileUpload.parseRequest(req);

                //循环判断，每一个表单项，是普通类型还是上传的文件
                for (FileItem fileItem : list) {
                    if(fileItem.isFormField()){
                        //普通表单项
                        System.out.println("表单项的name属性值："+fileItem.getFieldName());
                        //参数utf-8解决乱码问题
                        System.out.println("表单项的value属性值："+fileItem.getString("utf-8"));
                    }else {
                        //上传的文件
                        System.out.println("表单项的name属性值："+fileItem.getFieldName());
                        System.out.println("上传的文件名："+fileItem.getName());

                        fileItem.write(new File("e:\\"+fileItem.getName()));
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }

        }
    }
}
```







### 2、文件下载

下载的常用 API 说明： 

response.getOutputStream(); 

servletContext.getResourceAsStream(); 

servletContext.getMimeType(); 

response.setContentType(); 

response.setHeader("Content-Disposition", "attachment; fileName=1.jpg"); 

这个响应头告诉浏览器。这是需要下载的。而 attachment 表示附件，也就是下载的一个文件。fileName=后面， 

表示下载的文件名。 

完成上面的两个步骤，下载文件是没问题了。但是如果我们要下载的文件是中文名的话。你会发现，下载无法正确显示出正确的中文名。 

原因是在响应头中，不能包含有中文字符，只能包含 ASCII 码。 







# cookie

### 什么是 Cookie? 

1、Cookie 翻译过来是饼干的意思。 

2、Cookie 是服务器通知客户端保存键值对的一种技术。 

3、客户端有了 Cookie 后，每次请求都发送给服务器。 

4、每个 Cookie 的大小不能超过 4kb 





### 如何创建Cookie？



![image-20211211161313710](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211211161313710.png)

### 服务器如何获取Cookie 

服务器获取客户端的 Cookie 只需要一行代码：req.getCookies()  	返回Cookie[]



![image-20211211162915510](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211211162915510.png)

### Cookie 值的修改

方案一： 

1、先创建一个要修改的同名（指的就是 key）的 Cookie 对象 

2、在构造器，同时赋于新的 Cookie 值。 

3、调用 response.addCookie( Cookie );



方案二： 

1、先查找到需要修改的 Cookie 对象 

2、调用 setValue()方法赋于新的 Cookie 值。 

3、调用 response.addCookie()通知客户端保存修改







### Cookie生命控制 

Cookie 的生命控制指的是如何管理 Cookie 什么时候被销毁（删除） 

setMaxAge() 

正数，表示在指定的秒数后过期 

负数，表示浏览器一关，Cookie 就会被删除（默认值是-1） 

零，表示马上删除 Cookie





### Cookie有效路径Path的设置

Cookie 的 path 属性可以有效的过滤哪些 Cookie 可以发送给服务器。哪些不发。 

path 属性是通过请求的地址来进行有效的过滤。 



CookieA 		path=/工程路径 

CookieB 		path=/工程路径/abc 



请求地址如下： 

http://ip:port/工程路径/a.html 

CookieA 发送 

CookieB 不发送 



http://ip:port/工程路径/abc/a.html 

CookieA 发送 

CookieB 发送







### Cookie 练习---免输入用户名登录

 

![image-20211212152921147](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211212152921147.png)









# Session

### 什么是 Session 会话? 

1、Session 就一个接口（HttpSession）。 

2、Session 就是会话。它是用来维护一个客户端和服务器之间关联的一种技术。 

3、每个客户端都有自己的一个 Session 会话。 

4、Session 会话中，我们经常用来保存用户登录之后的信息。（保存在服务器端）



### 如何创建 Session 和获取(id 号,是否为新)

如何创建和获取 Session。它们的 API 是一样的。 

request.getSession() 



第一次调用是：创建 Session 会话 

之后调用都是：获取前面创建好的 Session 会话对象。 



isNew(); 判断到底是不是刚创建出来的（新的） 

true 表示刚创建 

false 表示获取之前创建 



每个会话都有一个身份证号。也就是 ID 值。而且这个 ID 是唯一的。 

getId() 得到 Session 的会话 id 值。 





### Session 域数据的存取 









### Session 生命周期控制 

public void setMaxInactiveInterval(int interval) 设置 Session 的超时时间（以秒为单位），超过指定的时长，Session 

就会被销毁。



值为正数的时候，设定 Session 的超时时长。 

负数表示永不超时（极少使用） 





public int getMaxInactiveInterval()		获取 Session 的超时时间 

public void invalidate() 						   让当前 Session 会话马上超时无效。 



Session 默认的超时时长是多少！ 

Session 默认的超时时间长为 30 分钟。 

因为在 Tomcat 服务器的配置文件 web.xml中默认有以下的配置，它就表示配置了当前 Tomcat 服务器下所有的 Session 

超时配置默认时长为：30 分钟。 

<session-config> 

<session-timeout>30</session-timeout> 

</session-config>



如果说。你希望你的 web 工程，默认的 Session 的超时时长为其他时长。你可以在你自己的 web.xml 配置文件中做 

以上相同的配置。就可以修改你的 web 工程所有 Seession 的默认超时时长。





如果你想只修改个别 Session 的超时时长。就可以使用上面的 API。setMaxInactiveInterval(int interval)来进行单独的设置。

session.setMaxInactiveInterval(int interval)单独设置超时时长。 





Session 超时的概念介绍：

![image-20211212163644108](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211212163644108.png)





### 浏览器和 Session 之间关联的技术内幕

Session 技术，底层其实是基于 Cookie 技术来实现的。

![image-20211212165316707](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211212165316707.png)









# Filter 过滤器

### 什么是filter过滤器



1、Filter 过滤器它是 JavaWeb 的三大组件之一。三大组件分别是：Servlet 程序、Listener 监听器、Filter 过滤器 

2、Filter 过滤器它是 JavaEE 的规范。也就是接口 

3、Filter 过滤器它的作用是：拦截请求，过滤响应。 





拦截请求常见的应用场景有： 

1、权限检查 

2、日记操作  

3、事务管理 

……等等





### Filter 的初体验 

要求：在你的 web 工程下，有一个 admin 目录。这个 admin 目录下的所有资源（html 页面、jpg 图片、jsp 文件、等等）都必 须是用户登录之后才允许访问。 





思考：根据之前我们学过内容。我们知道，用户登录之后都会把用户登录的信息保存到 Session 域中。所以要检查用户是否 

登录，可以判断 Session 中否包含有用户登录的信息即可！！！ 



![image-20211213144151579](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211213144151579.png)





Filter 过滤器的使用步骤： 

1、编写一个类去实现 Filter 接口 

2、实现过滤方法 doFilter() 

3、到 web.xml 中去配置 Filter 的拦截路径





### Filter 的生命周期 

Filter 的生命周期包含几个方法 

1、构造器方法 

2、init 初始化方法 

​	第 1，2 步，在 web 工程启动的时候执行（Filter 已经创建） 

3、doFilter 过滤方法 

​	第 3 步，每次拦截到请求，就会执行 

4、destroy 销毁 

​	第 4 步，停止 web 工程的时候，就会执行（停止 web 工程，也会销毁 Filter 过滤器）





### FilterConfig 类 

FilterConfig 类见名知义，它是 Filter 过滤器的配置文件类。 

Tomcat 每次创建 Filter 的时候，也会同时创建一个 FilterConfig 类，这里包含了 Filter 配置文件的配置信息。 



FilterConfig 类的作用是获取 filter 过滤器的配置内容 

1、获取 Filter 的名称 filter-name 的内容 

2、获取在 Filter 中配置的 init-param 初始化参数 

3、获取 ServletContext 对象





### FilterChain 过滤器链 

Filter 	 过滤器 

Chain 	链，链条 

FilterChain 就是过滤器链（多个过滤器如何一起工作）



![image-20211214171059418](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211214171059418.png)







### Filter 的拦截路径 

--精确匹配 

<url-pattern>/target.jsp</url-pattern> 

以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/target.jsp 



--目录匹配 

<url-pattern>/admin/*</url-pattern> 

以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/admin/* 



--后缀名匹配 

<url-pattern>*.html</url-pattern> 

以上配置的路径，表示请求地址必须以.html 结尾才会拦截到 

<url-pattern>*.do</url-pattern> 

以上配置的路径，表示请求地址必须以.do 结尾才会拦截到 

<url-pattern>*.action</url-pattern> 

以上配置的路径，表示请求地址必须以.action 结尾才会拦截到 



Filter 过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在！！！







# JSON



### 什么是 JSON

JSON (JavaScript Object Notation) 是一种轻量级的数据交换格式。 易于人阅读和编写。同时也易于机器解析 

和生成。 它基于 JavaScript Programming Language, 

Standard ECMA-262 3rd Edition - December 1999 的一 

个子集。 JSON 采用完全独立于语言的文本格式，但是也使用了类似于 C 语言家族的习惯（包括 C, C++, C#, Java, 

JavaScript, Perl, Python 等）。 这些特性使 JSON 成为理想的数据交换语言。



json 是一种轻量级的数据交换格式。 

轻量级指的是跟 xml 做比较。 

数据交换指的是客户端和服务器之间业务数据的传递格式。





### JSON 在 JavaScript 中的使用。 

##### json 的定义 

json 是由键值对组成，并且由花括号（大括号）包围。每个键由引号引起来，键和值之间使用冒号进行分隔，多组键值对之间使用逗号进行分隔



``

```html
var jsonObj={
   "key1":12,
   "key2":"abc",
   "key2":true,
   "key2":[11,"arr",false],
   "key5": {
      "key5.1":551,
      "key5.2":"key5.2value"
   },
   "key6":[{
      "key6_1_1":6611,
      "key6_1_2":"key612value"
   },{
      "key6_2_1":6621,
      "key6_2_2":"key622value"
   }]
}
```



### json的访问 

json 本身是一个对象。 

json 中的 key 我们可以理解为是对象中的一个属性。 

json 中的 key 访问就跟访问对象的属性一样： json 对象.key





### json 的两个常用方法 

json 的存在有两种形式。 

一种是：对象的形式存在，我们叫它 json 对象。 

一种是：字符串的形式存在，我们叫它 json 字符串。 



一般我们要操作 json 中的数据的时候，需要 json 对象的格式。 

一般我们要在客户端和服务器之间进行数据交换的时候，使用 json 字符串。 



JSON.stringify() 				把 json 对象转换成为 json 字符串 

JSON.parse() 					把 json 字符串转换成为 json 对象



``

```html
var jsonObj={
   "key1":12,
   "key2":"abc",
   "key3":true,
   "key4":[11,"arr",false],
   "key5": {
      "key5.1":551,
      "key5.2":"key5.2value"
   },
   "key6":[{
      "key6_1_1":6611,
      "key6_1_2":"key612value"
   },{
      "key6_2_1":6621,
      "key6_2_2":"key622value"
   }]
}


// json对象转字符串

var jsonObjString=JSON.stringify(jsonObj);//特别想java中对象的toString
alert(jsonObjString);


// json字符串转json对象
var jsonObj2=JSON.parse(jsonObjString);
alert(jsonObj2); 
```





### JSON 在 java 中的使用



##### javaBean 和 json 的互转

``

```java
@Test
public void test1(){
    Person person=new Person(1,"徐睿杰");

    //创建Gson实例
    Gson gson=new Gson();
    //toJson方法可以把java对象转换为json字符串
    String s = gson.toJson(person);
    System.out.println(s);

    //fromJson把Json字符串转换回java对象
    //第一个参数是Json字符串
    // 第二个参数是转换回去的Java对象类型
    Person person1 = gson.fromJson(s, Person.class);
    System.out.println(person1);


}
```

##### List 和 json 的互转



##### map 和 json 的互转 





# AJAX 请求 



### 什么是 AJAX 请求 

AJAX 即“Asynchronous Javascript A*nd* X*ML*”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发 技术。

 

ajax 是一种浏览器通过 js 异步发起请求，局部更新页面的技术。 

Ajax 请求的局部更新，浏览器地址栏不会发生变化 

局部更新不会舍弃原来页面的内容







### 原生 AJAX 请求的示例：

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
   <head>
      <meta http-equiv="pragma" content="no-cache" />
      <meta http-equiv="cache-control" content="no-cache" />
      <meta http-equiv="Expires" content="0" />
      <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
      <title>Insert title here</title>
      <script type="text/javascript">
         // 在这里使用javaScript语言发起Ajax请求，访问服务器AjaxServlet中javaScriptAjax
         function ajaxRequest() {
		// 1、我们首先要创建XMLHttpRequest 
            var xmlhttprequest=new XMLHttpRequest();
		// 2、调用open方法设置请求参数
            //第一个参数表示get还是post
            //第二个参数表示请求地址
            //第三个参数表示是否是异步请求，true表示异步请求
            xmlhttprequest.open("GET","http://localhost:8080/16_json_ajax_i18n/ajaxServlet?action=javaScriptAjax",true)

		// 4、在send方法前绑定onreadystatechange事件，处理请求完成后的操作。
            xmlhttprequest.onreadystatechange=function (){
               if (xmlhttprequest.readyState==4&& xmlhttprequest.status==200){

                  //转为json对象
                  var jsonObj=JSON.parse(xmlhttprequest.responseText);

                  //把响应的数据显示在页面上
                  document.getElementById("div01").innerHTML="编号："+jsonObj.id+"姓名："+jsonObj.name;
               }
            }

		// 3、调用send方法发送请求
            xmlhttprequest.send();

         }
      </script>
   </head>
   <body> 
      <button onclick="ajaxRequest()">ajax request</button>
      <div id="div01">
      </div>
   </body>
</html>
```





### jQuery 中的 AJAX 请求 

##### $.ajax 方法 

​	url 		表示请求的地址 

​    type 	表示请求的类型 GET 或 POST 请求 

​	data 	表示发送给服务器的数据 

​			格式有两种： 

​					一：name=value&name=value 

​					二：{key:value} 

​	success 	请求成功，响应的回调函数 

​	dataType 	响应的数据类型 

​							常用的数据类型有： 

​								text 表示纯文本 

​								xml 表示 xml 数据 

​								json 表示 json 对象



##### $.get 方法和$.post 方法 

​	url 			请求的 url 地址 

​	data 	 	发送的数据 

​	callback    成功的回调函数 

​	type 		  返回的数据类型



##### $.getJSON 方法 

url 					 请求的 url 地址 

data 				  发送给服务器的数据 

callback 			成功的回调函数 





##### 表单序列化 serialize() 

serialize()可以把表单中所有表单项的内容都获取到，并以 name=value&name=value 的形式进行拼接
