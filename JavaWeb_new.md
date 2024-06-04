## 一、HTML

### 1、html介绍

> HTML是由一系列标签组成的，标签是通过一组尖括号+标签名的方式来定义的。
>
> 标签分为**单标签**和**双标签**。单标签例如`<br/>`表示换行，双标签有一个开始标签和一个结束标签，`<p>HTML is a very popular fore-end technology.</p>`。这里`<p>`是开始标签，`</p>`是结束标签



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221628012.png)

**HTML文件结构**

- 文档类型声明

  > HTML文件中第一行的内容，用来告诉浏览器当前HTML文档的基本信息

  `<!DOCTYPE html>`这是`HTML5`版本的文档类型声明

- 根标签

  > html标签是整个文档的根标签，所有其他标签都必须放在html标签里面。上面的文档类型不能当做普通标签看待。

- 头部

  > head标签用于定义文档的头部，其他头部元素都放在head标签里。头部元素包括title标签、script标签、style标签、link标签、meta标签等等。

- 主体

  > body标签定义网页的主体内容，在浏览器窗口内显示的内容都定义到body标签内。





### 2、常用标签

| 标签名称 | 功能                   |
| -------- | ---------------------- |
| h1~h6    | 1级标题~6级标题        |
| p        | 段落                   |
| a        | 超链接                 |
| ul/li    | 无序列表               |
| img      | 图片                   |
| div      | 定义一个前后有换行的块 |
| span     | 定义一个前后无换行的块 |



**`<br/>`**表示换行，是一个单标签



**`&nbsp`**表示空格



**`p`**是段落标签，段落标签前后有换行

`<p>这里是一个段落</p>`   



**`img`**是图片标签

`<img src="imgs\A.jpg" width="57" height="73" alt="这里是一张图片"/>`

>  `src`属性表示图片文件的路径
>
>  `width`和`height`表示图片的大小
>
>  `alt`表示图片的提示, 当图片显示错误时会提示



**`h1-h6`**是标题标签

```html
<html>
    <head>
        <title>first html</title>
        <meta charset="utf-8">
    </head>
    <body>
        <h1>段落1</h1>
        <h2>段落2</h2>
        <h3>段落3</h3>
        <h4>段落4</h4>
        <h5>段落5</h5>
        <h6>段落6</h6>
    </body>

</html>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221651045.png)



**列表标签：**

- `ul` 无序列表  ` type disc(default) , circle , square`

```html
<ul type = "disc">
    <li>Apple</li>
    <li>Banana</li>
    <li>Grape</li>
</ul>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221652870.png)

- ` ol` 有序列表    `start` 表示从*开始，`type` 显示的类型：`A` `a` `I` `i` `1`(deafult)

  ```html
  <ol type = "1" start = "3">
      <li>Apple</li>
      <li>Banana</li>
      <li>Grape</li>
  </ol>
  ```

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221657716.png)

 		

**u 下划线    b 粗体    i 斜体**

```html
你是<b><i><u>喜欢</u></i></b>是<b>甜</b>月饼还是<i>咸</i><u>月饼</u>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221659819.png)



**上标 sup  下标 sub**

```html
水分子的化学式:  H<sub>2</sub>O <br/>
a的平方:  a<sup>2</sup><br/>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221700867.png)





**HTML中的实体**

- 小于 `&lt;` 
- 大于 `&gt;`
- 小于等于 `&le;`
- 大于等于 `&ge;` 

```html
5 &lt; 10   <br/>
6 &gt; 3    <br/>
7 &le; 8    <br/>
1 &le; 3    <br/>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221703300.png)





**块**

> 块并不是为了显示文章内容的，而是为了方便结合`CSS`对页面进行布局。块有两种，div是前后有换行的块，span是前后没有换行的块。span 不换行的块标记

```html
<div style="border: 1px solid black;width: 100px;height: 100px;">This is a div block</div>
<div style="border: 1px solid black;width: 100px;height: 100px;">This is a div block</div>

<span style="border: 1px solid black;width: 100px;height: 100px;">This is a span block</span>
<span style="border: 1px solid black;width: 100px;height: 100px;">This is a span block</span>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221705881.png)





**超链接**

`a`标签表示超链接

```html
<a href="http://www.baidu.com" target="_self">百度一下</a>

href 表示链接的地址
target:
	_self 在本窗口打开
	_blank 在一个新窗口打开
	_parent 在父窗口打开
	_top  在顶层窗口打开
```

   

​    



**表格**

- `tr`表示行
- `td`表示列
- `th`表示表头列



table中有如下属性【已淘汰】

`border`：表格边框的粗细

`width`: 表格的宽度

`cellspacing`：单元格间距

`cellpadding`：单元格填充

`tr`中有一个属性：` align` -> `center` , `left` , `right` 

`rowspan` : 行合并

` colspan` : 列合并

```HTML
<table border="1" width="600" cellspacing="0" cellpadding="4">
    <tr align="center">
        <th>姓名</th>
        <th>门派</th>
        <th>成名绝技</th>
        <th>内功值</th>
    </tr>
    <tr align="center">
        <td>乔峰</td>
        <td>丐帮</td>
        <td>少林长拳</td>
        <td>5000</td>
    </tr>
    <tr align="center">
        <td>虚竹</td>
        <td>灵鹫宫</td>
        <td>北冥神功</td>
        <td>15000</td>
    </tr>
    <tr align="center">
        <td>扫地僧</td>
        <td>少林寺</td>
        <td>七十二绝技</td>
        <td>未知</td>
    </tr>
</table>
<hr/>
<table border="1" cellspacing="0" cellpadding="4" width="600">
    <tr>
        <th>名称</th>
        <th>单价</th>
        <th>数量</th>
        <th>小计</th>
        <th>操作</th>
    </tr>
    <tr align="center">
        <td>苹果</td>
        <td rowspan="2">5</td>
        <td>20</td>
        <td>100</td>
        <td><img src="imgs/del.jpg" width="24" height="24"/></td>
    </tr>
    <tr align="center">
        <td>菠萝</td>
        <td>15</td>
        <td>45</td>
        <td><img src="imgs/del.jpg" width="24" height="24"/></td>
    </tr>
    <tr align="center">
        <td>西瓜</td>
        <td>6</td>
        <td>6</td>
        <td>36</td>
        <td><img src="imgs/del.jpg" width="24" height="24"/></td>
    </tr>
    <tr align="center">
        <td>总计</td>
        <td colspan="4">181</td>
    </tr>
</table>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221711025.png)







**from表单**

```html
<html>
	<head>
		<title>表单标签的学习</title>
		<meta charset="UTF-8">
		
	</head>
	<body>
		<form action="Demo04.html" method="post">
			昵称：<input type="text" name="Nickname" value="请输入你的昵称"/><br/>
			密码：<input type="password" name="pwd"/><br/>
			性别：<input type="radio" name="gender" value="male"/>男
	  			  <input type="radio" name="gender" value="female" checked/>女<br/>
			爱好：<input type="checkbox" name="hobby" value="basketball"/>篮球
				  <input type="checkbox" name="hobby" value="football" checked/>足球
				  <input type="checkbox" name="hobby" value="earth" checked/>地球<br/>
			星座：<select name="star">
					<option value="1">白羊座</option>
					<option value="2" selected>金牛座</option>
					<option value="3">双子座</option>
					<option value="4">天蝎座</option>
					<option value="5">天秤座</option>
				  </select><br/>
			备注：<textarea name="remark" rows="4" cols="50"></textarea><br/>
			<input type="submit" value=" 注 册 "/>
			<input type="reset" value="重置"/>
			<input type="button" value="这是一个普通按钮"/>
		</form>
	</body>
</html>
```



- `input type="text"` 表示文本框 ， 其中 **`name`**属性必须要指定，否则这个文本框的数据将来是不会发送给服务器的

-  `input type="password"` 表示密码框

- `input type="radio"` 表示单选按钮。需要注意的是，name属性值保持一致，这样才会有互斥的效果;  可以通过checked属性设置默认选中的项

- `input type="checkbox"` 表示复选框。name属性值建议保持一致，这样将来我们服务器端获取值的时候获取的是一个数组

- `select` 表示下拉列表。每一个选项是option，其中value属性是发送给服务器的值 , selected表示默认选中的项

- `textarea` 表示多行文本框（或者称之为文本域）,它的value值就是开始结束标签之间的内容

- `input type="submit" `表示提交按钮

- `input type="reset"` 表示重置按钮，重置会表单初始状态

- `input type="button"` 表示普通按钮，点击后无效果，需要通过JavaScript绑定单击响应函数
- `action="Demo04.html"`表示将表单数据发送给`Demo04.html`
- `method="post"`表示提交方式为`post`。如果是`get`方式提交，提交的信息会显示在地址栏，而`post`方式提交不会显示【静态页面使用post会报405，所以这里用get才不会报错】



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305221748382.png)

> 提交之后，在`Demo04.html`可以收到发送的信息，如果没写`name`属性，这里就收不到对应信息







## 二、`CSS`

> `CSS`是用来对`html`显示的内容进行装饰的

- `CSS`样式由选择器和声明组成，而声明又由属性和值组成。
- 属性和值之间用冒号隔开。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231445621.png)







### 1、 设置`CSS`样式的三种方式

#### 1.1 在HTML标签内设置

仅对当前标签有效

```html
<div style="border: 1px solid black;width: 100px; height: 100px;">&nbsp;</div>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231440905.png)





#### 1.2 在head标签内设置

对当前页面有效

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style type="text/css">
        .one {
            border: 1px solid black;
            width: 100px;
            height: 100px;
            background-color: lightgreen;
            margin-top: 5px;
        }
    </style>
</head>
<body>

    <div style="border: 1px solid black;width: 100px; height: 100px;">&nbsp;</div>

    <div class="one">&nbsp;</div>
    <div class="one">&nbsp;</div>
    <div class="one">&nbsp;</div>

</body>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231442225.png)



#### 1.3 引入外部`CSS`样式

- 创建css文件

  ```css
  .two {
      border: 1px solid black;
      width: 100px;
      height: 100px;
      background-color: yellow;
      margin-top: 5px;
  }
  ```

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231442210.png)

- 引入外部`CSS`文件

```html
<html>
	<head>
		<meta charset="utf-8">
		<link rel="stylesheet" type="text/css" href="/aaa/pro01-HTML/style/example.css" />
	</head>
	<body>
		<div class="two">&nbsp;</div>
        <div class="two">&nbsp;</div>
        <div class="two">&nbsp;</div>
	</body>
</html>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231444473.png)



### 2、`CSS`选择器

#### 2.1 标签选择器

`HTML`代码

```css
<p>Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
```

`CSS`代码

```css
p {
    color: blue;
    font-weight: bold;
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231446406.png)



#### 2.2 id选择器

`HTML`代码

```html
<p>Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
<p id="special">Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
<p>Hello, this is a p tag.</p>
```

`CSS`代码

```css
#special {
    font-size: 20px;
    background-color: aqua;
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231447170.png)



#### 2.3 类选择器

`HTML`代码

```html
<div class="one">&nbsp;</div>
<div class="one">&nbsp;</div>
<div class="one">&nbsp;</div>
```

`CSS`代码

```css
.one {
    border: 1px solid black;
    width: 100px;
    height: 100px;
    background-color: lightgreen;
    margin-top: 5px;
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231448449.png)





## 三、JavaScript

> js是弱类型语言，JavaScript中也有明确的数据类型，但是声明一个变量后它可以接收任何类型的数据，并且会在程序执行过程中根据上下文自动转换类型。



### 1、HelloWorld

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>HelloWorld</title>
	</head>
	<body>
		<!-- 在HTML代码中定义一个按钮 -->
		<button type="button" id="helloBtn">SayHello</button>
	</body>
	
	<!-- 在script标签中编写JavaScript代码 -->
	<script type="text/javascript">
		
		// document对象代表整个HTML文档
		// document对象调用getElementById()方法表示根据id查找对应的元素对象
		var btnElement = document.getElementById("helloBtn");
		
		// 给按钮元素对象绑定单击响应函数
		btnElement.onclick = function(){
			// 弹出警告框
			alert("hello");
		};
	</script>
</html>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305231630924.png)





### 2、JavaScript语法

#### 2.1 js代码嵌入方式

**1、html文档内部**

- JavaScript代码要写在script标签内
- script标签可以写在文档内的任意位置
- 为了能够方便查询或操作HTML标签（元素）script标签可以写在body标签后面



**2、引入外部js文件**

在script标签内通过src属性指定外部xxx.js文件的路径即可。但是要注意以下两点：

- 引用外部JavaScript文件的script标签里面不能写JavaScript代码
- 先引入，再使用
- **script标签不能写成单标签**

```html
<body>
</body>

<!-- 使用script标签的src属性引用外部JavaScript文件，和Java中的import语句类似 -->
<!-- 引用外部JavaScript文件的script标签里面不能写JavaScript代码 -->
<!-- 引用外部JavaScript文件的script标签不能改成单标签 -->
<!-- 外部JavaScript文件一定要先引入再使用 -->
<script src="/pro02-JavaScript/scripts/outter.js" type="text/javascript" charset="utf-8"></script>

<script type="text/javascript">
	// 调用外部JavaScript文件中声明的方法
	showMessage();
</script>
```





#### 2.2 声明和使用变量

- 基本数据类型

  - 数值型：JavaScript不区分整数、小数

  - 字符串：JavaScript不区分字符、字符串；单引号、双引号意思一样。

  - 布尔型：true、false

    在JavaScript中，其他类型和布尔类型的自动转换。

    true：非零的数值，非空字符串，非空对象

    false：零，空字符串，null，undefined

    例如："false"放在if判断中

    ```javascript
    // "false"是一个非空字符串，直接放在if判断中会被当作『真』处理
    if("false"){
    	alert("true");
    }else{
    	alert("false");
    }
    ```

- 引用类型
  - 所有new出来的对象
  - 用[]声明的数组
  - 用{}声明的对象



**变量**

- 关键字：var

- 数据类型：JavaScript变量可以接收任意类型的数据

- 标识符：严格区分大小写

- 变量使用规则

  - 如果使用了一个没有声明的变量，那么会在运行时报错

    `Uncaught ReferenceError: b is not defined`

  - 如果声明一个变量没有初始化，那么这个变量的值就是undefined





#### 2.3 函数

**内置函数**

- 弹出警告框

  ```javascript
  alert("警告框内容");
  ```

- 弹出确认框

  用户点击『确定』返回true，点击『取消』返回false

  ```javascript
  var result = confirm("老板，你真的不加个钟吗？");
  if(result) {
  	console.log("老板点了确定，表示要加钟");
  }else{
  	console.log("老板点了确定，表示不加钟");
  }
  ```

- 在控制台打印日志

  ```javascript
  console.log("日志内容");
  ```





**声明函数**

**写法1**

```javascript
function sum(a, b) {
    return a+b;
}
```



**写法2**

```javascript
var total = function() {
    return a+b;
};
```

> 写法2可以这样解读：声明一个函数，相当于创建了一个『函数对象』，将这个对象的『引用』赋值给变量total。最后加的分号不是给函数声明加的，而是给整体的赋值语句加的分号。
>





**调用函数**

JavaScript中函数本身就是一种对象，函数名就是这个**『对象』**的**『引用』**。而调用函数的格式是：**函数引用()**。

```javascript
function sum(a, b) {
    return a+b;
}

var result = sum(2, 3);
console.log("result="+result);
```

或

```javascript
var total = function() {
    return a+b;
}

var totalResult = total(3,6);
console.log("totalResult="+totalResult);
```





### 3、DOM

> DOM是**D**ocument **O**bject **M**odel的缩写，意思是**『文档对象模型』**——将HTML文档抽象成模型，再封装成对象方便用程序操作



**DOM树**

- 浏览器把HTML文档从服务器上下载下来之后就开始按照**『从上到下』**的顺序**『读取HTML标签』**。每一个标签都会被封装成一个**『对象』**。

- 而第一个读取到的肯定是根标签html，然后是它的子标签head，再然后是head标签里的子标签……所以从html标签开始，整个文档中的所有标签都会根据它们之间的**『父子关系』**被放到一个**『树形结构』**的对象中。

- 这个包含了所有标签对象的整个树形结构对象就是JavaScript中的一个**可以直接使用的内置对象**：**document**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305241605712.png)





#### 3.1 组成部分

> 整个文档中的一切都可以看做Node。各个具体组成部分的具体类型可以看做Node类型的子类。

| 组成部分         | 节点类型 | 具体类型 |
| ---------------- | -------- | -------- |
| 整个文档         | 文档节点 | Document |
| HTML标签         | 元素节点 | Element  |
| HTML标签内的文本 | 文本节点 | Text     |
| HTML标签内的属性 | 属性节点 | Attr     |
| 注释             | 注释节点 | Comment  |





#### 3.2 DOM操作

| 功能               | API                                       | 返回值             |
| ------------------ | ----------------------------------------- | ------------------ |
| 根据id值查询       | `document.getElementById(“id值”)`         | 一个具体的元素节点 |
| 根据标签名查询     | `document.getElementsByTagName(“标签名”)` | 元素节点数组       |
| 根据name属性值查询 | `document.getElementsByName(“name值”)`    | 元素节点数组       |









## 四、Tomcat

**Tomcat目录结构**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305241713224.png)

> tomcat是一个WebContainer，即web容器，在服务器端，我们启动tomcat，并将我们的项目放入tomcat容器中，即可通过ip+端口访问到tomcat服务器，然后根据指定目录即可访问到我们的项目

> 启动tomcat后，他会默认监听8080端口，当有程序要访问本机8080端口时，tomcat就会响应请求。因为启动tomcat后，访问localhost:8080就会进入tomcat主页，我们的项目都是放在webapps文件夹下，比如访问a项目下的index.html，就是访问localhost:8080/a/index.html





### 4.1 IDEA关联Tomcat

如果想将一个项目设置为web项目，需要在`Project Structure`中的`Facets`下将该项目添加为Web项目

一个web项目如果想要部署到tomcat上去，就是把它的部署包部署到tomcat上去，这个部署包就称为Artifact，因此需要生成Artifact包

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251130179.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251642273.png)

> Artiface中，Web Application: Exploded 是将整个项目不压缩，放入webapps中；Web Application: Archive是将项目打包成一个war包放入webapps中，tomcat会自动将war解压



创建好的项目如下，web目录的图标上有个蓝色的圆点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251132319.png)



如果我们创建完了`Artiface`后，然后又添加了`lib`，比如添加了`mysql`的jar包，我们在`Module`的`Dependences`中添加了这个包后，此时这个jar包并没有添加到部署包中，因此在`Project Structure`中的`Problem`中会有提示，将jar包添加进去。

另一种方法：如果直接将lib文件夹放在WEB-INF下就不用添加了，但是不好的地方是这个jar包只有该`mudule`独享





**web项目目录结构**

| 目录或文件名            | 功能                                                         |
| ----------------------- | ------------------------------------------------------------ |
| src目录                 | 存放Java源文件                                               |
| web目录                 | 存放Web开发相关资源                                          |
| web/WEB-INF目录         | 存放web.xml文件、classes目录、lib目录                        |
| web/WEB-INF/web.xml文件 | 别名：部署描述符deployment descriptor 作用：Web工程的核心配置文件 |
| web/WEB-INF/classes目录 | 存放编译得到的*.class字节码文件                              |
| web/WEB-INF/lib目录     | 存放第三方jar包                                              |





**在IDEA中将项目部署到Tomcat**

> 此时启动项目默认打开的地址是http://localhost:8080/web01/add.html，如果我们改为http://localhost:8080/web01/，表明我们访问的是index.html.
>
> 在tomcat的conf下的web.xml可以看到该配置，首先寻找index.html，没有就去找index.htm，最后找index.jsp，都没有就报404错误，因此我们可以通过修改`<welcome-file-list>`来配置我们默认打开的页面，可以在tomcat下设置，也可以在WEB-INF下的web.xml文件中设置

```xml
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251137905.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251141469.png)



IDEA 使用 tomcat 部署项目后不会把编译后的项目复制到 tomcat 的 webapps 目录下，而是复制一份配置文件到指定目录下（把编译好的项目路径告诉Tomcat），让 tomcat 找到这个项目，也就是说每个项目都有属于自己的一份 tomcat 配置，互不干扰

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251142420.png)







### 4.2 Servlet

**Servlet介绍**

> Servlet=Server+applet  
>
> Server：服务器
>
> applet：小程序
>
> Servlet含义是服务器端的小程序

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251549533.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251718588.png)



**Servlet案例**

前端html代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="add" method="post">
        名称：<input type="text" name="fname"/><br/>
        价格：<input type="text" name="price"/><br/>
        库存：<input type="text" name="fcount"/><br/>
        备注：<input type="text" name="remark"/><br/>
        <input type="submit" value="添加" />
    </form>
</body>
</html>
```



Servlet代码

```java
package com.xrj.servlets;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class AddServlet extends HttpServlet{
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String fName = req.getParameter("fname");
        int price = Integer.parseInt(req.getParameter("price"));
        int fCount = Integer.parseInt(req.getParameter("fcount"));
        String remark = req.getParameter("remark");

        System.out.println("fName = " + fName);
        System.out.println("price = " + price);
        System.out.println("fCount = " + fCount);
        System.out.println("remark = " + remark);
    }
}
```



xml配置

```xml
<servlet>
    <servlet-name>AddServlet</servlet-name>
    <servlet-class>com.xrj.servlets.AddServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>AddServlet</servlet-name>
    <url-pattern>/add</url-pattern>
</servlet-mapping>
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251540854.png)

> 这里我们向服务器请求访问`add.html`页面，服务端将该页面的内容发送回来，浏览器接收到后，会将html代码解析为相应的页面，在该页面上，有一个form表单，action = " add ", post方式提交
>
> 我们输入完数据后，点击添加，会将添加的内容封装到一个`HttpServletRequest`对象中，然后将该对象发送到服务端的add，因此在服务端我们需要写一个程序用于接收客户端发来的数据，在tomcat中，我们使用servlet来接收。
>
> 首先我们创建一个`AddServlet`类，他必须继承`HttpServlet`，这个类我们导入tomcat的jar包即可，因为客户端是post方式提交，因此我们需要重新`doPost`方法，该方法有两个参数`HttpServletRequest req` 和 `HttpServletResponse resp`，第一个参数`req`就是客户端发来的请求对象，我们可以通过`req.getParameter`方法获取对应的数据。但是客户端发送给的是add，我们的类名为`AddServlet`，因此需要将其对应起来。
>
> 这时需要去`web.xml`文件中配置一下，将`AddServlet`类与`/add`这样的url对应起来，这样，前端发送数据到add，就会在web.xml文件中`servlet-mapping`找这个url，找到后，会找他对应的的`servlet-name`，然后去`servlet`中根据`servlet-name`找到对应的`servlet-class`，即我们编写的接收数据的类







### 4.3 Servlet设置编码

- 在`tomcat8`之前，设置编码

  - get方式：

    ```java
    //如果是get请求发送的中文数据，转码稍微有点麻烦（tomcat8之前）
    String fname = request.getParameter("fname");
    //1.将字符串打散成字节数组，tomcat默认是ISO-8859-1编码
    byte[] bytes = fname.getBytes("ISO-8859-1");
    //2.将字节数组按照设定的编码重新组装成字符串
    fname = new String(bytes,"UTF-8");
    ```

  - post方式

    ```java
    request.setCharacterEncoding("UTF-8");
    ```

- 从`toncat8`开始：get方式无需设置编码，只需要对post方式设置编码

  ```java
  request.setCharacterEncoding("UTF-8");
  ```

  **注意**：设置编码(post)这一句代码必须在所有的获取参数动作之前







### 4.4 Servlet继承关系



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261632989.png)



> servlet接口中有5个方法，其中`init()`是初始化方法，`service()`是服务方法，`destory()`是销毁方法。当客户端有请求发过来时，tomcat会自动调用`serveice()`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261633594.png)



> 在HttpServlet类中实现了`Service()`方法，`String method = req.getMethod();`首先获取客户端的请求方式，根据不同的请求方式去不同的do方法中执行，如果客户端请求方式为get，则会进入`doGet()`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261637664.png)



> `doGet()`方法首先得到一个msg的字符串，该字符串是从`lStrings`中根据key获取的。然后执行`this.sendMethodNotAllowed(req, resp, msg);`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261638193.png)



> `String protocol = req.getProtocol();`获取客户端请求使用的协议名称和版本号。如果客户端使用的协议版本号不是 0.9 或 1.0，则向客户端发送一个状态码为 405 的“方法不允许”的响应，响应消息为 `msg` 指定的错误消息。否则，向客户端发送一个状态码为 400 的“错误请求”的响应，响应消息同样为 `msg` 指定的错误消息。 `protocol.endsWith()` 方法判断客户端使用的协议版本号是否为0.9 或 1.0。如果客户端使用的协议版本号以这两个字符串结尾，则说明客户端使用的是 HTTP/0.9 或 HTTP/1.0 协议，这两个协议不支持发送状态码为 405 的响应，因此需要发送状态码为 400 的响应。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261642729.png)



> 因此当我们客户端向服务端发送一个请求时，如果我们不重写相应的do方法，`doGet()`、`doPost()`等，他就会直接去执行父类的相应方法，就会报405错误。因此在新建Servlet时，我们才会去考虑请求方法，从而决定重写哪个do方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261644199.png)





### 4.5 Servlet生命周期

> 从出生到死亡的过程就是生命周期。对应Servlet中的三个方法：`init()`，`service()`，`destroy()`

```java
public class Demo01Servlet extends HttpServlet {
    public Demo01Servlet(){
        System.out.println("实例化");
    }

    @Override
    public void init() throws ServletException {
        System.out.println("初始化");
    }

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("服务");
    }

    @Override
    public void destroy() {
        System.out.println("销毁");
    }
}
```

我们重写了`init()`、`service()`、`destroy()`方法，默认情况下，第一次接收请求时，这个Servlet会进行**实例化**(调用构造方法)、**初始化**(调用`init()`)、然后**服务**(调用`service()`)。从第二次请求开始，每一次都是服务(调用`service()`)，当容器关闭时，其中的所有的servlet实例会被销毁，调用销毁方法(`destroy()`)

servlet的实例化是tomcat底层通过**反射**创建的，`web.xml`文件有实现servlet类的路径，如果把构造方法设为`private`，发起请求时就会报错，因为反射无法访问私有成员。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261722721.png)



  - Servlet实例tomcat只会创建一个，所有的请求都是这个实例去响应。
        - 默认情况下，第一次请求时，Servlet才会去实例化，初始化，然后再服务。这样的好处是：可以提高系统的启动速度 。 这样的缺点是： 第一次请求时，耗时较长。【第一个用户请求需要等待较长时间，因为需要创建实例】
        - 因此得出结论： 如果需要提高系统的启动速度，当前默认情况就是这样。如果需要提高响应速度，我们应该设置Servlet的初始化时机。



  -  Servlet的初始化时机：
    - 默认是第一次接收请求时，Servlet进行实例化，初始化
    - 我们可以通过`<load-on-startup>`来设置servlet启动的先后顺序，数字越小，启动越靠前，最小值0



> 将web.xml文件的`<load-on-startup>`配置后，表示tomcat启动后，就创建Servlet实例，而不需要等到用户发请求，这样虽然启动慢了，但是对于第一个用户来说访问快了

```xml
<servlet>
    <servlet-name>Demo01Servlet</servlet-name>
    <servlet-class>com.xrj.servlets.Demo01Servlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>Demo01Servlet</servlet-name>
    <url-pattern>/Demo01</url-pattern>
</servlet-mapping>
```



- Servlet在容器中是：**单例的**、**线程不安全的**

  - 单例：所有的请求都是同一个实例去响应

  - 线程不安全：一个线程需要根据这个实例中的某个成员变量值去做逻辑判断。但是在中间某个时机，另一个线程改变了这个成员变量的值，从而导致第一个线程的执行路径发生了变化

  - 因为servlet是线程不安全的，所以 尽量不要在servlet中定义成员变量。如果不得不定义成员变量，那么：①不要去修改成员变量的值 ②不要去根据成员变量的值做一些逻辑判断





**总结**

- 默认情况下：Servlet在**第一次接收到请求**的时候才创建对象
- 创建对象后，所有的URL地址匹配的请求都由这同一个对象来处理
- Tomcat中，每一个请求会被分配一个线程来处理，所以可以说：Servlet是**单实例，多线程**方式运行的。
- 既然Servlet是多线程方式运行，所以有线程安全方面的可能性，所以不能在处理请求的方法中修改公共属性





### 4.6 HTTP协议

> HTTP超文本传输协议【除了传输文本，还传输图片，音频等，因此叫做超文本，协议就是一个规则，定义了规则后，发送方和请求方就根据这一规则来发送和接收数据】。HTTP是无状态的，它确定了请求和响应数据的格式。浏览器发送给服务器的数据：请求报文；服务器返回给浏览器的数据：响应报文。



#### 请求报文

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261756357.png)




请求包含三个部分： 

- 请求行：请求行包含是三个信息： 1. 请求的方式 ； 2.请求的URL ； 3.请求的协议（一般都是HTTP1.1）
- 请求消息头

​		作用：通过具体的参数对本次请求进行详细的说明

​		格式：键值对，键和值之间使用冒号隔开

| 名称           | 功能                                                 |
| -------------- | ---------------------------------------------------- |
| Host           | 服务器的主机地址                                     |
| Accept         | 声明当前请求能够接受的『媒体类型』                   |
| Referer        | 当前请求来源页面的地址                               |
| Content-Length | 请求体内容的长度                                     |
| Content-Type   | 请求体的内容类型，这一项的具体值是媒体类型中的某一种 |
| Cookie         | 浏览器访问服务器时携带的Cookie数据                   |

- 请求体
    - `get`方式，没有请求体，但是有一个queryString
    - `post`方式，有请求体，form data
    - `json`格式，有请求体，request payload







#### 响应报文

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261800666.png)


响应报文也包含三个部分

- 响应行：响应行包含三个信息：1. 协议    2.响应状态码(200)     3.响应状态(ok)

- 响应头：包含了服务器的信息；服务器发送给浏览器的信息（内容的媒体类型、编码、内容长度等）

  | 名称           | 功能                                                 |
  | -------------- | ---------------------------------------------------- |
  | Content-Type   | 响应体的内容类型                                     |
  | Content-Length | 响应体的内容长度                                     |
  | Set-Cookie     | 服务器返回新的Cookie信息给浏览器                     |
  | location       | 在**重定向**的情况下，告诉浏览器访问下一个资源的地址 |

- 响应体：响应的实际内容（比如请求add.html页面时，响应的内容就是`<html><head><body><form....`）
  







### 4.7 Cookie

- 在浏览器端临时存储数据，客户端有了 Cookie 后，每次请求都发送给服务器。 
- 键值对
- 键和值都是字符串类型
- 每个 Cookie 的大小不能超过 4kb 





**Cookie的创建**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211211161313710.png" alt="image-20211211161313710" style="zoom:70%;" />

```java
package com.xrj.cookie.servler;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.IOException;

@WebServlet("/cookie01")
public class Cookie01 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Cookie cookie = new Cookie("uname", "xrj");
        response.addCookie(cookie);
        request.getRequestDispatcher("hello.html").forward(request, response);
    }
}
```



在浏览器第一次请求，request中的cookie只有一个默认的Idea参数，服务器返回的response中的`Set-Cookie`会携带参数，然后客户端将该数据保存到cookie中

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306161524066.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306161526763.png)



第二次访问时，客户端的Cookie是有uname这个数据的，所以request请求中的Cookie会携带该数据去访问服务器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306161527138.png)







**Cookie的时效性**

- 会话级Cookie

  - 服务器端并没有明确指定Cookie的存在时间
  - 在浏览器端，Cookie数据存在于内存中
  - 只要浏览器还开着，Cookie数据就一直都在
  - 浏览器关闭，内存中的Cookie数据就会被释放

- 持久化Cookie

  - 服务器端明确设置了Cookie的存在时间

  - 在浏览器端，Cookie数据会被保存到硬盘上

  - Cookie在硬盘上存在的时间根据服务器端限定的时间来管控，不受浏览器关闭的影响

  - 持久化Cookie到达了预设的时间会被释放

    

**`setMaxAge(time) `**

正数，表示在指定的秒数后过期 

负数，表示浏览器一关，Cookie 就会被删除（默认值是-1） 

零，表示马上删除 Cookie





**Cookie的domain和path**

> 上网时间长了，本地会保存很多Cookie。对浏览器来说，访问互联网资源时不能每次都把所有Cookie带上。浏览器会使用Cookie的domain和path属性值来和当前访问的地址进行比较，从而决定是否携带这个Cookie。
>
> Cookie 的 path 属性可以有效的过滤哪些 Cookie 可以发送给服务器。哪些不发。 
>
> path 属性是通过请求的地址来进行有效的过滤。 

`CookieA `	path=/工程路径 

`CookieB` 		path=/工程路径/abc 



请求地址如下： 

`http://ip:port/工程路径/a.html `

`CookieA` 发送 

`CookieB` 不发送 



`http://ip:port/工程路径/abc/a.html `

`CookieA `发送 

`CookieB` 发送







### 4.8 Session会话

 **1） Http是无状态的**

- HTTP 无状态 ：服务器无法判断这两次请求是同一个客户端发过来的，还是不同的客户端发过来的

   - 无状态带来的现实问题：第一次请求是添加商品到购物车，第二次请求是结账；如果这两次请求服务器无法区分是同一个用户的，那么就会导致混乱
   - 通过会话跟踪技术来解决无状态的问题。

   

**2） 会话跟踪技术**

   - 客户端第一次发请求给服务器，服务器获取session，获取不到，则创建新的，然后响应给客户端

   - 下次客户端给服务器发请求时，会把sessionID带给服务器，那么服务器就能获取到了，那么服务器就判断这一次请求和上次某次请求是同一个客户端，从而能够区分开客户端

        - 常用的API：
        
         `request.getSession()`：  获取当前的会话，没有则创建一个新的会话
        
         `request.getSession(true) `： 效果和不带参数相同
        
         `request.getSession(false)` ：获取当前会话，没有则返回null，不会创建新的
        
         `session.getId()` ： 获取sessionID
        
         `session.isNew()` ： 判断当前session是否是新的
        
         ` session.getMaxInactiveInterval()` ： session的非激活间隔时长【如果1800秒不操作，会话就会失效】，默认1800秒
        
         `session.setMaxInactiveInterval()`
        
         `session.invalidate()` ： 强制性让会话立即失效【例如退出的时候，就让会话失效】



```java
package com.xrj.servlets;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class Demo02Session extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        System.out.println(session.getId());
    }
}
```



> 客户端第一次发送请求时，是没有Session的，因此服务端收到请求后，会创建一个Session，并给客户端发送回去。可以看到session的`isNew = true`表示是一个新的session。同时`Response Headers`中有一个`Set-Cookie`表示服务端生成Session发送给客户端

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305271526832.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305271528158.png)





> 客户端第二次发送请求时，这时是有session的，因此服务端获取到session后，`isNew`属性为false。同时客户端的`Request Headers`中是带着Cookie的，里边的value就是SessionID

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305271529922.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305271529239.png)



> cookie就是服务端通知客户端保存的键值对。Session是客户端和服务端的一个会话。
>
> 浏览器在每次访问服务端的时候，都会带着自己的Cookie去发请求，如果第一次请求且cookie为空，此时服务端创建一个Session对象，将Session对象以cookie的方式发送给客户端，key是`JSESIONID`，value是session的ID，客户端收到后，会将该session的ID添加到自己的cookie中，下次访问时，客户端的cookie会带着这个session去发送请求，服务端通过这个cookie就可以获取到其sessionID【只有服务端获取session时，发现没有session才会创建并给客户端返回，服务端不获取session，即使cookie中没有session，服务端也不会返回sessionId】
>
> 不同的应用程序在客户端保存的cookie是不会共享的，如果访问百度，百度给客户端发回一个cookie，又去访问谷歌，谷歌也给客户端发回来一个cookie，但他们不是共享的，是独立的。





   **3） session保存作用域**

> session保存作用域是和具体的某一个session对应的。比如当前浏览器访问服务器，服务器为他创建了一个session，然后向session作用域中存了很多数据【key-value】，这些数据是和这个sessionID对应的。客户端第二次访问时，如果sessionID和这个是一样的，那么可以获取数据，如果不同就获取不到，因为它的session域中没有存放数据【不是同一个会话】



- 常用的API：

  `void session.setAttribute(k,v)`

  `Object session.getAttribute(k)`

  `void removeAttribute(k)`



```java
public class Demo03Servlet extends HttpServlet {
    // 向Session域中存数据
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.getSession().setAttribute("name", "xrj");
    }
}
```

```java
public class Demo04Servlet extends HttpServlet {
    // 从Session中取数据
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object name = req.getSession().getAttribute("name");
        System.out.println(name);
    }
}
```

> 使用谷歌浏览器访问`Demo03Servlet`，会创建一个session，并向session域中存放数据，此时谷歌浏览器访问`Demo04Servlet`，因为sessionID是相同的，所以可以获取到数据。
>
> 如果使用edge访问`Demo04Servlet`，由于sessionID不同，所以无法获取到数据





**4）session有效期**

> session的有效期默认为30分钟，但是只要与系统交互，就会刷新这个30分钟，如果30分钟内没有任何操作，session就会过期



**Session 技术，底层其实是基于 Cookie 技术来实现的**

![image-20211212165316707](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211212165316707.png)









### 4.9 转发和重定向

#### **服务端转发**

服务器收到请求后，从一个资源跳转到另一个服务器资源的操作叫请求转发。

> 客户端向服务端A发送一个请求，A收到请求后发现它无法解决，因此他将这个问题交给服务端B去解决，而不需要客户端操作，客户端只有这一次请求。

` request.getRequestDispatcher("...").forward(request,response);` 



**请求转发的特点**

- 浏览器地址栏没有变化

- 他们是一次请求，对客户端来说，内部经过多少次转发是不知道的
- 全程使用的是同一个request对象
- 目标资源可以在WEB-INF目录下
- 目标资源仅限于本应用内部





#### **客户端重定向**

客户端给服务器发请求，然后服务器告诉客户端，我给你一个新的地址，你去新地址访问

> 客户端向服务端A发送一个请求，A收到请求后发现它无法解决，他将B的地址发送给客户端，让客户端去向B发送请求，客户端有两次请求。



第一种方式【推荐】

`response.sendRedirect("....");`



第二种方式

`response.setStatus(302); // 设置状态码`

`response.sendRedirect("Demo06"); // 设置重定向地址`



**客户端重定向特点**

- 浏览器地址栏有变化
- 是两次请求
- 全程使用的是不同的request对象
- 目标资源不能在WEB-INF目录下
- 目标资源可以是外部资源



```java
public class Demo05Servlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("这里是服务端5");

        // 请求转发
        // req.getRequestDispatcher("Demo06").forward(req, resp);

        // 客户端重定向
        // 第一种方式【推荐】
        resp.sendRedirect("Demo06");

        // 第二种方式
        // resp.setStatus(302); // 设置状态码
        // resp.sendRedirect("Demo06"); // 设置重定向地址

    }
}
```

```java
public class Demo06Servlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("这里是服务端6");
    }
}
```



> 下边服务端请求转发的方式可以看到，只有一次请求，且地址栏不变

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305271703073.png)



> 下边是客户端重定向方式，可以看到这里发送了两次请求，第一次请求Demo05时，状态码为302，表示重定向，且`Response Header`中有一个`Location`表示重定向位置，且地址栏发送变化

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305271657128.png)







### 4.10 Thymeleaf 

> Thymeleaf 是视图模板技术，是将从数据库中查询到的信息，渲染到前端页面上。他必须经过服务器才能够渲染数据，如果直接跳转到静态页面是无法通过`Thymeleaf`渲染数据的
>
> Thymeleaf获取属性的值都是通过get方法来获取的，因为属性一般都是private修饰的。如果属性是public的，那么就直接访问属性，而不需要通过get方法获取



**使用步骤**

1、添加Thymeleaf的jar包

2、新建一个Servlet类`ViewBaseServlet`【复制即可】

3、在web.xml文件中添加配置

- 配置前缀 view-prefix
- 配置后缀 view-suffix
- 根据逻辑视图名称 得到 物理视图名称。物理视图名称 = 前缀 + 逻辑视图 + 后缀

```xml
<!-- 在上下文参数中配置视图前缀和视图后缀 -->
<context-param>
    <param-name>view-prefix</param-name>
    <param-value>/</param-value>
</context-param>
<context-param>
    <param-name>view-suffix</param-name>
    <param-value>.html</param-value>
</context-param>
```

4、使我们的Servlet继承`ViewBaseServlet`

​	获取到数据后，将数据添加到session域，然后`super.processTemplate("index", req, resp);`跳转到前端页面

```java
// servlet从3.0版本开始支持注解方式的注册
@WebServlet("/index")
public class indexServlet extends ViewBaseServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        FruitDAOImpl fruitDAO = new FruitDAOImpl();
        List<Fruit> fruitList = fruitDAO.getFruitList();

        HttpSession session = req.getSession();
        session.setAttribute("fruitList", fruitList);

        super.processTemplate("index", req, resp);
    }
}
```

5、在前端页面通过Thymeleaf 将数据渲染上去

```html
<html xmlns:th="http://www.thymeleaf.org">
   <head>
      <meta charset="utf-8">
      <link rel="stylesheet" href="css/index.css">
   </head>
   <body>
      <div id="div_container">
         <div id="div_fruit_list">
            <p class="center f30">欢迎使用水果库存后台管理系统</p>
            <table id="tbl_fruit">
               <tr>
                  <th class="w20">名称</th>
                  <th class="w20">单价</th>
                  <th class="w20">库存</th>
                  <th>操作</th>
               </tr>
               <tr th:if="${#lists.isEmpty(session.fruitList)}">
                  <td colspan="4">对不起，库存为空！</td>
               </tr>
               <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
                  <td th:text="${fruit.fname}">苹果</td>
                  <td th:text="${fruit.price}">5</td>
                  <td th:text="${fruit.fcount}">20</td>
                  <td><img src="imgs/del.jpg" class="delImg"/></td>
               </tr>
            </table>
         </div>
      </div>
   </body>
</html>
```





#### Thymeleaf语法

**修改标签文本值**

```html
<p th:text="标签体新值">标签体原始值</p>
```



**`th:text`作用**

- 不经过服务器解析，直接用浏览器打开HTML文件，看到的是『标签体原始值』
- 经过服务器解析，Thymeleaf引擎根据th:text属性指定的『标签体新值』去**替换**『标签体原始值』





**修改指定属性值**

```html
<input type="text" name="username" th:value="文本框新值" value="文本框旧值" />
```

> 任何HTML标签原有的属性，前面加上『th:』就都可以通过Thymeleaf来设定新值。







### 4.11 保存作用域

域对象有四种，但是`pageContext`是jsp的页面，现在已不使用，所以现在的作用域分为三种

- `request`请求域：一次请求响应范围
- `session`会话域：一次会话范围有效
- `application`应用域：一次应用程序范围有效



**请求域**

> 在请求转发的场景下，我们可以借助`HttpServletRequest`对象内部给我们提供的存储空间，帮助我们携带数据，把数据发送给转发的目标资源。



> 在请求域中，数据只在一次请求中有效，下边代码中，如果使用重定向方式到Demo02，获取不到name属性，因为是两次请求。而转发的方式可以获取到，是一次请求

```java
@WebServlet("/Demo01")
public class Demo01request extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setAttribute("name", "xrj");

        //resp.sendRedirect("Demo02");
        req.getRequestDispatcher("Demo02").forward(req, resp);
    }
}
```

```java
@WebServlet("/Demo02")
public class Demo02request extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object name = req.getAttribute("name");
        System.out.println(name);
    }
}
```







**会话域**

> 在会话域中，数据是在一次会话中有效的，当前用谷歌访问，可以获取到数据，换成edge访问就获取不到了，不是同一个客户端访问的，也就不是同一个会话

```java
@WebServlet("/Demo03")
public class Demo03session extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.getSession().setAttribute("name", "xrj");

        resp.sendRedirect("Demo04");
        //req.getRequestDispatcher("Demo04").forward(req, resp);
    }
}
```

```java
@WebServlet("/Demo04")
public class Demo04session extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object name = req.getSession().getAttribute("name");
        System.out.println(name);
    }
}
```







**应用域**

> 在应用域中，数据在本次应用范围内都是有效的，除非关闭tomcat服务，数据才会失效

```java
@WebServlet("/Demo05")
public class Demo05application extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // ServletContext 是 Servlet上下文
        ServletContext application = req.getServletContext();
        application.setAttribute("name", "xrj");

        resp.sendRedirect("Demo06");
        //req.getRequestDispatcher("Demo06").forward(req, resp);
    }
}
```

```java
@WebServlet("/Demo06")
public class Demo06application extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext application = req.getServletContext();
        Object name = application.getAttribute("name");
        System.out.println(name);
    }
}
```







### 4.12 路径问题

> 在项目中可以使用相对路径，也可以使用绝对路径，但是使用相对路径会出现很多`../`，项目结构改变路径就会出错，所以尽量使用绝对路径



**html中base标签**

```html
<!-- 当前页面上所有的路径都以这个为基础 -->
<base href = "http://localhost:8080/web05/" /> 

<!-- 下边语句的路径为http://localhost:8080/web05/css/index.css -->
<link gref = "css/index.css">

<!-- 在Thymeleaf中@{}就相当于http://localhost:8080/web05/, 根据项目的根目录来设置路径-->
th:href = "@{}"
```





## 五、水果库存系统

### 1. 展示所有水果信息

> 访问`http://localhost:8080/web04/index`后会跳转到index这个`servlet`，这里通过`dao`层查询所有水果信息，并将结果存放到session域中，然后通过`thymeleaf`进行渲染，在`index.html`页面展示

```java
@WebServlet("/index")
public class indexServlet extends ViewBaseServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        FruitDAOImpl fruitDAO = new FruitDAOImpl();
        List<Fruit> fruitList = fruitDAO.getFruitList();

        HttpSession session = req.getSession();
        session.setAttribute("fruitList", fruitList);
        super.processTemplate("index", req, resp);
    }
}
```

```HTML
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">欢迎使用水果库存后台管理系统</p>
         <table id="tbl_fruit">
            <tr>
               <th class="w20">名称</th>
               <th class="w20">单价</th>
               <th class="w20">库存</th>
               <th>操作</th>
            </tr>
            <tr th:if="${#lists.isEmpty(session.fruitList)}">
               <td colspan="4">对不起，库存为空！</td>
            </tr>
            <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
               <td th:text="${fruit.fname}">苹果</td>
               <td th:text="${fruit.price}">5</td>
               <td th:text="${fruit.fcount}">20</td>
               <td><img src="imgs/del.jpg" class="delImg"/></td>
            </tr>
         </table>
      </div>
   </div>
</body>
```



### 2. 编辑和修改库存信息

在水果的名称上加上超链接`<td> <a th:text="${fruit.fname}" th:href="@{/edit.do(fid=${fruit.fid})}" >苹果</a> </td>`，注意使用`thymeleaf`渲染的话，需要将`th:text="${fruit.fname}"`加到a标签里边，这样替换的才是  " 苹果 " , 超链接指向项目目录下的`/edit.do`，并将水果的id作为参数传递过去。

```HTML
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">欢迎使用水果库存后台管理系统</p>
         <table id="tbl_fruit">
            <tr>
               <th class="w20">名称9</th>
               <th class="w20">单价</th>
               <th class="w20">库存</th>
               <th>操作</th>
            </tr>
            <tr th:if="${#lists.isEmpty(session.fruitList)}">
               <td colspan="4">对不起，库存为空！</td>
            </tr>
            <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
               <!-- <td> <a th:text="${fruit.fname}" th:href="@{'/edit.do?fid=' + ${fruit.fid}}" >苹果</a> </td>
                  '/edit.do?fid=' + ${fruit.fid}  '/edit.do?fid='是一个字符串，${fruit.fid}是需要thymeleaf解析的内容
                  这样是通过字符串的方式用get请求传递参数，一般是通过下边的方式，传递的参数用一个()包起来
                  @{/edit.do(fid=${fruit.fid}), fname=${fruit.fname}}
               -->
               <td> <a th:text="${fruit.fname}" th:href="@{/edit.do(fid=${fruit.fid})}" >苹果</a> </td>
               <td th:text="${fruit.price}">5</td>
               <td th:text="${fruit.fcount}">20</td>
               <td><img src="imgs/del.jpg" class="delImg" /></td>
            </tr>
         </table>
      </div>
   </div>
</body>
```



在`editServlet`中，先通过水果的id查询到水果信息，然后将该信息保存到`request`域中，通过`thymeleaf`渲染到`edit.html`页面，` processTemplate("edit", req, resp);`相当于服务器转发，所以可以访问`request`域的对象

```java
@WebServlet("/edit.do")
public class editServlet extends ViewBaseServlet{
    private FruitDAO fruitDAO = new FruitDAOImpl();
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String fidStr = req.getParameter("fid");
        if (!"".equals(fidStr)){
            int fid = Integer.parseInt(fidStr);
            Fruit fruit = fruitDAO.getFruitById(fid);
            req.setAttribute("fruit", fruit);
            processTemplate("edit", req, resp);
        }
    }
}
```



在`edit.html`页面上，通过`thymeleaf`将原始数据渲染上去，同时将表单数据发送到`/update.do`下

```html
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">编辑库存记录</p>
         <!-- 用th标签 @{}让update.do每次都是去项目根目录下找 -->
         <form th:action="@{/update.do}" method="post" th:object="${fruit}">
            <!-- 需要发送fid信息，但是又不希望在页面显示，就将type设置为hidden -->
            <input type="hidden" name="fid" th:value="*{fid}"/>
            <table id="tbl_fruit">
               <tr>
                  <th class="w20">名称</th>
                  <!--<td><input type="text" name="fname" th:value="${fruit.fname}"/></td>-->
                  <td><input type="text" name="fname" th:value="*{fname}"/></td>
               </tr>
               <tr>
                  <th class="w20">单价</th>
                  <td><input type="text" name="price" th:value="*{price}"/></td>
               </tr>
               <tr>
                  <th class="w20">库存</th>
                  <td><input type="text" name="fcount" th:value="*{fcount}"/></td>
               </tr>
               <tr>
                  <th class="w20">备注</th>
                  <td><input type="text" name="remark" th:value="*{remark}"/></td>
               </tr>
               <tr>
                  <th colspan="2">
                     <input type="submit" value="修改"/>
                  </th>
               </tr>
            </table>
         </form>
      </div>
   </div>
</body>
```



在`updateServlet`中，获取表单发来的数据，然后调用dao进行数据更新，最后需要重定向到`index`，如果转发到`index.html`，其中的session数据还是旧的，所以需要重新向`indexServlet`发一次请求，重新获取数据，覆盖旧的session，然后在通过`thymeleaf`跳转到`index.html`就会显示新的数据

```java
@WebServlet("/update.do")
public class updateServlet extends ViewBaseServlet{
    FruitDAO fruitDAO = new FruitDAOImpl();
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.设置编码
        req.setCharacterEncoding("utf-8");
        // 2.获取参数
        int fid = Integer.parseInt(req.getParameter("fid"));
        String fname = req.getParameter("fname");
        int price = Integer.parseInt(req.getParameter("price"));
        int fcount = Integer.parseInt(req.getParameter("fcount"));
        String remark = req.getParameter("remark");
        System.out.println(fname + " " + price + " " + fcount + " " + remark);

        // 3.执行更新
        fruitDAO.updateById(new Fruit(fid, fname, price, fcount, remark));

        // 4.资源跳转
        // processTemplate("index", req, resp);
        // req.getRequestDispatcher("index.html").forward();
        /*
            processTemplate("index", req, resp);相当于服务器转发，因为index获取数据是从session获取的，session中的数据还是以前老的
            所以需要使用重定向，重新向index发一次请求，重新获取数据覆盖到session上，这样数据才是最新的
         */
        resp.sendRedirect("index");
    }
}
```





### 3. 删除和添加

**删除**

可以通过给删除的图片添加一个超链接，跳转到`delete.do`同时带上该水果的id

或者通过js实现，在图片上添加一个单击事件`delFruit`，同时带上水果的id，在js中通过`confirm`确认是否删除，确认的话，就跳转到`delete.do`同时带上水果id

```javascript
function delFruit(fid){
    if (confirm("是否确认删除?")){
        window.location.href="delete.do?fid="+fid
    }
}
```

```html
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">欢迎使用水果库存后台管理系统</p>
         <table id="tbl_fruit">
            <tr>
               <th class="w20">名称9</th>
               <th class="w20">单价</th>
               <th class="w20">库存</th>
               <th>操作</th>
            </tr>
            <tr th:if="${#lists.isEmpty(session.fruitList)}">
               <td colspan="4">对不起，库存为空！</td>
            </tr>
            <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
               <!-- <td> <a th:text="${fruit.fname}" th:href="@{'/edit.do?fid=' + ${fruit.fid}}" >苹果</a> </td>
                  '/edit.do?fid=' + ${fruit.fid}  '/edit.do?fid='是一个字符串，${fruit.fid}是需要thymeleaf解析的内容
                  这样是通过字符串的方式用get请求传递参数，一般是通过下边的方式，传递的参数用一个()包起来
                  @{/edit.do(fid=${fruit.fid}), fname=${fruit.fname}}
               -->
               <td> <a th:text="${fruit.fname}" th:href="@{/edit.do(fid=${fruit.fid})}" >苹果</a> </td>
               <td th:text="${fruit.price}">5</td>
               <td th:text="${fruit.fcount}">20</td>
               <!--   通过超链接的方式实现删除
               <td><a th:href="@{delete.do(fid=${fruit.fid})}"><img src="imgs/del.jpg" class="delImg"/></a></td>
               -->
               <!--
                  通过js进行删除,|delFruit(${fruit.fid})|加上||后thymeleaf会自动解析字符串和表达式
               -->
               <td><img src="imgs/del.jpg" class="delImg" th:onclick="|delFruit(${fruit.fid})|"/></td>
            </tr>
         </table>
      </div>
   </div>
</body>
```



在`deleteServlet`中，通过获取水果id将水果删除，然后通过重定向跳转到`index.html`页面

```java
@WebServlet("/delete.do")
public class deleteServlet extends ViewBaseServlet{
    private FruitDAO fruitDAO = new FruitDAOImpl();
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        int fid = Integer.parseInt(req.getParameter("fid"));
        fruitDAO.deleteById(fid);
        resp.sendRedirect("index");
    }
}
```



**添加**

`<a th:href="@{/add.do}" style="border:0px solid blue;margin-bottom:4px;">添加新库存记录</a>`  通过一个a标签，跳转到`/add.do`下

如果直接跳转到`add.html`下的话，`add.html`是一个静态页面，不能使用`thymeleaf`解析数据，在表单提交的时候就只能`<form action="add.do" method="post">`这么写

```html
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">欢迎使用水果库存后台管理系统</p>
         <div style="border:0px solid red;width:60%;margin-left:20%;text-align:right;">
            <a th:href="@{/add.do}" style="border:0px solid blue;margin-bottom:4px;">添加新库存记录</a>
         </div>
         <table id="tbl_fruit">
            <tr>
               <th class="w20">名称9</th>
               <th class="w20">单价</th>
               <th class="w20">库存</th>
               <th>操作</th>
            </tr>
            <tr th:if="${#lists.isEmpty(session.fruitList)}">
               <td colspan="4">对不起，库存为空！</td>
            </tr>
            <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
               <!-- <td> <a th:text="${fruit.fname}" th:href="@{'/edit.do?fid=' + ${fruit.fid}}" >苹果</a> </td>
                  '/edit.do?fid=' + ${fruit.fid}  '/edit.do?fid='是一个字符串，${fruit.fid}是需要thymeleaf解析的内容
                  这样是通过字符串的方式用get请求传递参数，一般是通过下边的方式，传递的参数用一个()包起来
                  @{/edit.do(fid=${fruit.fid}), fname=${fruit.fname}}
               -->
               <td> <a th:text="${fruit.fname}" th:href="@{/edit.do(fid=${fruit.fid})}" >苹果</a> </td>
               <td th:text="${fruit.price}">5</td>
               <td th:text="${fruit.fcount}">20</td>
               <!--   通过超链接的方式实现删除
               <td><a th:href="@{delete.do(fid=${fruit.fid})}"><img src="imgs/del.jpg" class="delImg"/></a></td>
               -->
               <!--
                  通过js进行删除,|delFruit(${fruit.fid})|加上||后thymeleaf会自动解析字符串和表达式
               -->
               <td><img src="imgs/del.jpg" class="delImg" th:onclick="|delFruit(${fruit.fid})|"/></td>
            </tr>
         </table>
      </div>
   </div>
</body>
```



因为添加水果直接去静态页面`add.html`下添加就行，不需要向编辑一样，需要`thymeleaf`渲染数据，所以如果不通过`servlet`跳转到`add.html`页面的话，是无法通过`thymeleaf`解析` <form th:action="@{/add.do}" method="post">`的。如果跳转到`servlet`，然后通过`servlet`使用`thymeleaf`渲染到`add.html`的话就可以通过`thymeleaf`来解析数据了。

```java
@WebServlet("/add.do")
public class addServlet extends ViewBaseServlet{
    private FruitDAO fruitDAO = new FruitDAOImpl();

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        processTemplate("add", req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        String fname = req.getParameter("fname");
        int price = Integer.parseInt(req.getParameter("price"));
        int fcount = Integer.parseInt(req.getParameter("fcount"));
        String remark = req.getParameter("remark");

        fruitDAO.addFruit(new Fruit(0, fname, price, fcount, remark));
        System.out.println(fname + " " + price + " " + fcount + " " + remark);
        resp.sendRedirect("index");
    }
}
```



```html
<div id="div_fruit_list">
    <p class="center f30">添加库存记录4</p>
    <!--
    <form th:action="@{/add.do}" method="post">
    这个页面是静态的html页面，无法解析thymeleaf代码，而edit.html是通过editServlet渲染跳转过去的，所以可以解析thymeleaf代码
    而这个页面是直接点击过来的，没有经过thymeleaf渲染
    解决方法1: <form action="add.do" method="post">  直接跳转，不使用thymeleaf解析,默认是当前目录下的add.do
    解决方法2: 在index页面上先跳转到addServlet,重写doGet方法,然后渲染跳转到该页面
    -->
    <!--  <form action="add.do" method="post">  -->
    <form th:action="@{/add.do}" method="post">
        <table id="tbl_fruit">
            <tr>
                <th class="w20">名称</th>
                <td><input type="text" name="fname"/></td>
            </tr>
            <tr>
                <th class="w20">单价</th>
                <td><input type="text" name="price"/></td>
            </tr>
            <tr>
                <th class="w20">库存</th>
                <td><input type="text" name="fcount"/></td>
            </tr>
            <tr>
                <th class="w20">备注</th>
                <td><input type="text" name="remark"/></td>
            </tr>
            <tr>
                <th colspan="2">
                    <input type="submit" value="添加"/>
                </th>
            </tr>
        </table>
    </form>
</div>
```





### 4. 分页

如果第一次访问`indexServlet`，那么`request`和`session`域中都没有`pageNo`信息，`pageNo`就是1，默认查询第一页，然后将`pageNo`信息保存到`session`中，同时查询出所有记录条数，计算出总页数，存到`session`中，然后查询到第一页数据后，放入`session`中，再渲染到`index.html`页面上。

```java
@WebServlet("/index")
public class indexServlet extends ViewBaseServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        FruitDAOImpl fruitDAO = new FruitDAOImpl();
        HttpSession session = req.getSession();
        int pageNo = 1;

        String page = req.getParameter("pageNo");
        if (page != null){
            pageNo = Integer.parseInt(page);
        }

        session.setAttribute("pageNo", pageNo);

        // 得到总条数
        int tot = fruitDAO.getFruitCount();
        // 得到总页数
        int totPage = (tot + 4) / 5;
        session.setAttribute("totPage", totPage);

        List<Fruit> fruitList = fruitDAO.getFruitList(pageNo);

        session.setAttribute("fruitList", fruitList);

        super.processTemplate("index", req, resp);
    }
}
```



如果点击首页，就是访问第1页，如果下一页就是当前页+1，上一页就是当前页-1，尾页就是最后一页，如果处于第一页，就不能点击首页和上一页，处于尾页就不能点击下一个和尾页，这里通过js函数来控制单击事件

```javascript
function page(pageNo){
    window.location.href="index?pageNo="+pageNo
}
```

```html
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">欢迎使用水果库存后台管理系统</p>
         <div style="border:0px solid red;width:60%;margin-left:20%;text-align:right;">
            <a th:href="@{/add.do}" style="border:0px solid blue;margin-bottom:4px;">添加新库存记录</a>
         </div>
         <table id="tbl_fruit">
            <tr>
               <th class="w20">名称9</th>
               <th class="w20">单价</th>
               <th class="w20">库存</th>
               <th>操作</th>
            </tr>
            <tr th:if="${#lists.isEmpty(session.fruitList)}">
               <td colspan="4">对不起，库存为空！</td>
            </tr>
            <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
               <!-- <td> <a th:text="${fruit.fname}" th:href="@{'/edit.do?fid=' + ${fruit.fid}}" >苹果</a> </td>
                  '/edit.do?fid=' + ${fruit.fid}  '/edit.do?fid='是一个字符串，${fruit.fid}是需要thymeleaf解析的内容
                  这样是通过字符串的方式用get请求传递参数，一般是通过下边的方式，传递的参数用一个()包起来
                  @{/edit.do(fid=${fruit.fid}), fname=${fruit.fname}}
               -->
               <td> <a th:text="${fruit.fname}" th:href="@{/edit.do(fid=${fruit.fid})}" >苹果</a> </td>
               <td th:text="${fruit.price}">5</td>
               <td th:text="${fruit.fcount}">20</td>
               <!--   通过超链接的方式实现删除
               <td><a th:href="@{delete.do(fid=${fruit.fid})}"><img src="imgs/del.jpg" class="delImg"/></a></td>
               -->
               <!--
                  通过js进行删除,|delFruit(${fruit.fid})|加上||后thymeleaf会自动解析字符串和表达式
               -->
               <td><img src="imgs/del.jpg" class="delImg" th:onclick="|delFruit(${fruit.fid})|"/></td>
            </tr>
         </table>
         <div style="width:60%;margin-left:20%;border:0px solid red;padding-top:4px;" class="center">
            <!--   disabled为true表示当前按钮被禁用    -->
            <input type="button" class="btn" value="首 页" th:onclick="page(1)" th:disabled="${session.pageNo==1}">
            <input type="button" class="btn" value="上一页" th:onclick="|page(${session.pageNo} - 1)|" th:disabled="${session.pageNo==1}">
            <input type="button" class="btn" value="下一页" th:onclick="|page(${session.pageNo} + 1)|" th:disabled="${session.pageNo==session.totPage}">
            <input type="button" class="btn" value="尾 页" th:onclick="|page(${session.totPage})|" th:disabled="${session.pageNo==session.totPage}">
         </div>
      </div>
   </div>
</body>
```







### 5. 根据关键字查询

`index.html`页面上加一个`form`表单，表单只要提交就会带一个`oper`参数，表示当前请求是通过搜索框发出的

```html
<form th:action="@{/index}" method="post" style="float:left;width:60%;margin-left:20%;">
    <input type="hidden" name="oper" value="search"/>
    请输入关键字<input type="text" name="keyword" th:value="${session.keyword}"/>
    <input type="submit" value="查询" class="btn"/>
</form>
```



```html
<body>
   <div id="div_container">
      <div id="div_fruit_list">
         <p class="center f30">欢迎使用水果库存后台管理系统</p>
         <div style="border:0px solid red;width:60%;margin-left:20%;text-align:right;">
            <form th:action="@{/index}" method="post" style="float:left;width:60%;margin-left:20%;">
               <input type="hidden" name="oper" value="search"/>
               请输入关键字<input type="text" name="keyword" th:value="${session.keyword}"/>
               <input type="submit" value="查询" class="btn"/>
            </form>
            <a th:href="@{/add.do}" style="border:0px solid blue;margin-bottom:4px;">添加新库存记录</a>
         </div>
         <table id="tbl_fruit">
            <tr>
               <th class="w20">名称9</th>
               <th class="w20">单价</th>
               <th class="w20">库存</th>
               <th>操作</th>
            </tr>
            <tr th:if="${#lists.isEmpty(session.fruitList)}">
               <td colspan="4">对不起，库存为空！</td>
            </tr>
            <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit : ${session.fruitList}">
               <!-- <td> <a th:text="${fruit.fname}" th:href="@{'/edit.do?fid=' + ${fruit.fid}}" >苹果</a> </td>
                  '/edit.do?fid=' + ${fruit.fid}  '/edit.do?fid='是一个字符串，${fruit.fid}是需要thymeleaf解析的内容
                  这样是通过字符串的方式用get请求传递参数，一般是通过下边的方式，传递的参数用一个()包起来
                  @{/edit.do(fid=${fruit.fid}), fname=${fruit.fname}}
               -->
               <td> <a th:text="${fruit.fname}" th:href="@{/edit.do(fid=${fruit.fid})}" >苹果</a> </td>
               <td th:text="${fruit.price}">5</td>
               <td th:text="${fruit.fcount}">20</td>
               <!--   通过超链接的方式实现删除
               <td><a th:href="@{delete.do(fid=${fruit.fid})}"><img src="imgs/del.jpg" class="delImg"/></a></td>
               -->
               <!--
                  通过js进行删除,|delFruit(${fruit.fid})|加上||后thymeleaf会自动解析字符串和表达式
               -->
               <td><img src="imgs/del.jpg" class="delImg" th:onclick="|delFruit(${fruit.fid})|"/></td>
            </tr>
         </table>
         <div style="width:60%;margin-left:20%;border:0px solid red;padding-top:4px;" class="center">
            <!--   disabled为true表示当前按钮被禁用    -->
            <input type="button" class="btn" value="首 页" th:onclick="page(1)" th:disabled="${session.pageNo==1}">
            <input type="button" class="btn" value="上一页" th:onclick="|page(${session.pageNo} - 1)|" th:disabled="${session.pageNo==1}">
            <input type="button" class="btn" value="下一页" th:onclick="|page(${session.pageNo} + 1)|" th:disabled="${session.pageNo==session.totPage}">
            <input type="button" class="btn" value="尾 页" th:onclick="|page(${session.totPage})|" th:disabled="${session.pageNo==session.totPage}">
         </div>
      </div>
   </div>
</body>
```



`indexServlet`收到查询的请求后通过`doPost`调用`doGet`方法，`doGet`方法首先获取`oper`参数，如果`oper`不为空，且等于`search`说明是从搜索框哪里传来的数据，这时将`pageNo`设置为1，查询第一页，然后获取`keyword`参数，如果为空，就赋值为空字符串，相当于查询全部，如果不赋为空字符串，在dao层相当于查询`%null%`，而赋为空字符串查询的就是`%%`，然后将`keyword`存储到session中，以便通过点击下一页查询。

如果`oper`参数为空表示当前是通过url地址或者点击下一页过来的，这时如果有`pageNo`参数，就设置，然后获取`keyword`，进行查询

```java
@WebServlet("/index")
public class indexServlet extends ViewBaseServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        FruitDAOImpl fruitDAO = new FruitDAOImpl();
        HttpSession session = req.getSession();
        int pageNo = 1;

        // 如果oper参数不为空，说明是从上边的查询表单过来的, 这时查询的是第一页，且通过keyword查
        String oper = req.getParameter("oper");
        String keyword = null;
        if (oper != null && "search".equals(oper)) {
            pageNo = 1;
            keyword = req.getParameter("keyword");
            if (keyword == null)
                keyword = "";
            session.setAttribute("keyword", keyword);
        }else{ // oper为空说明是通过点击下一页过来的
            String page = req.getParameter("pageNo");
            if (page != null){
                pageNo = Integer.parseInt(page);
            }

            Object keyword1 = session.getAttribute("keyword");
            if (keyword1 != null)
                keyword = (String) keyword1;
            else
                keyword = "";
        }

        session.setAttribute("pageNo", pageNo);

        // 得到总条数
        int tot = fruitDAO.getFruitCount(keyword);
        // 得到总页数
        int totPage = (tot + 4) / 5;
        session.setAttribute("totPage", totPage);

        List<Fruit> fruitList = fruitDAO.getFruitList(keyword, pageNo);

        session.setAttribute("fruitList", fruitList);

        super.processTemplate("index", req, resp);
    }
}
```





## 六 、Servlet优化【MVC】

### 1. 反射优化servlet

> 在fruitServlet的service方法中，最初是根据`operation`参数的不同选择不同的方法执行，用的是`switch...case`，如果方法太多，`switch...case`会太长，因此通过反射对其进行优化

```java
@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.setCharacterEncoding("utf-8");
    String operate = req.getParameter("operation");

    if (operate == null)
        operate = "index";

    Method[] methods = this.getClass().getDeclaredMethods();
    for (Method m : methods) {
        if (operate.equals(m.getName()))
            try {
                m.invoke(this, req, resp);
                return;
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            }
    }

    throw new RuntimeException("operation值非法");
}
```





### 2. 前端控制器

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202306031631117.png" style="zoom:75%;" />



> 目前项目中只有一个fruitServlet，但是当项目中有多个不同模块的servlet时，在每个servlet中都需要获取参数，根据不同的`operation`进行不同的方法调用，因此可以将这些操作抽取出来，放在一个`DispatcherServlet`中，这些`fruitServlet`就变成一个普通的类，也就是`controller`层，前端的所有请求都通过前端控制器来接收，他根据前端的不同请求，去不同的`controller`层中调用不同的方法。前端传来请求，这里通过`xml`文件来进行映射。



**applicationContext.xml**

> 如果前端请求的是`fruit.do`，最初是通过`web.xml`配置使其映射到`fruitServlet`中，这里通过`applicationContext.xml`将前端请求与`controller`层进行映射

```xml
<?xml version="1.0" encoding="utf-8" ?>

<beans>
    <bean id="fruit" class="com.xrj.fruit.Controller.fruitController"/>
</beans>
```



**`DispatcherServlet`**

> `init`方法是初始化方法，因为`DispatcherServlet`现在是一个`servlet`，执行时，先实例化，然后执行`init`方法，在`init`中，会解析`applicationContext.xml`文件，将其放入一个map中，key是前端请求的`controller`，value就是对应的实例。
>
> 在service方法中，解析`servletPath`，然后通过这个path找到对应的`controller`，根据`operation`的不同值，通过反射获取对应方法，然后执行。
>
> 因为此时`fruitServlet`不是`servlet`了，而他需要通过`thymeleaf`进行页面渲染，需要得到`servletContext`，而他又没有`servletContext`，所以需要在这里将`servletContext`传过去

```java
@WebServlet("*.do")
public class DispatcherServlet extends HttpServlet {
    Map<String, Object> beanMap = new HashMap<>();

    public DispatcherServlet(){
    }

    public void init(){
        try {
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("applicationContext.xml");
            //1.创建DocumentBuilderFactory
            DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
            //2.创建DocumentBuilder对象
            DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder() ;
            //3.创建Document对象
            Document document = documentBuilder.parse(inputStream);

            NodeList beanNodeList = document.getElementsByTagName("bean");
            for (int i = 0; i < beanNodeList.getLength(); i++) {
                Node beanNode = beanNodeList.item(i);
                if (beanNode.getNodeType() == Node.ELEMENT_NODE) {
                    Element elementBean = (Element) beanNode;
                    String beanId = elementBean.getAttribute("id");
                    String className = elementBean.getAttribute("class");
                    Class beanClass = Class.forName(className);
                    Object bean = beanClass.newInstance();
                    // 因为fruitController现在不是servlet，所以他没有servletContext对象，因此需要在这里将servletContext传过去
                    Method method = beanClass.getDeclaredMethod("setServletContext", ServletContext.class);
                    method.invoke(bean, this.getServletContext());

                    beanMap.put(beanId, bean);
                }
            }
        } catch (SAXException e) {
            e.printStackTrace();
        } catch (ParserConfigurationException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException | NoSuchMethodException | InvocationTargetException e) {
            e.printStackTrace();
        }
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws IOException {
        request.setCharacterEncoding("utf-8");
        String servletPath = request.getServletPath();
        servletPath = servletPath.substring(1);
        int idx = servletPath.lastIndexOf(".do");
        servletPath = servletPath.substring(0, idx);

        Object beanObj = beanMap.get(servletPath);
        String operate = request.getParameter("operation");
        if (operate == null)
            operate = "index";

        try {
            Method method = beanObj.getClass().getDeclaredMethod(operate, HttpServletRequest.class, HttpServletResponse.class);
            if (method != null){
                method.setAccessible(true);
                method.invoke(beanObj, request, response);
            }else{
                throw new RuntimeException("operation值非法");
            }
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
    }
}
```



**`fruitServlet`**

```java
public class fruitController extends ViewBaseServlet {
    /*
        这里现在不是servlet了，是一个普通的类，所以执行的时候不会调用init方法，而且也没有servletContext对象了，所以会报空指针
     */
    FruitDAOImpl fruitDAO = new FruitDAOImpl();
    ServletContext servletContext;

    public void setServletContext(ServletContext servletContext) throws ServletException {
        this.servletContext = servletContext;
        init(servletContext);
    }
}
```







### 3. mvc优化

> 目前在`fruitController`中，它已经不是一个`servlet`，但是每个方法又需要进行页面跳转和`thymeleaf`渲染，所以我们可以将这些动作一起交给前端控制器进行处理。在`fruitController`中，在最后的页面跳转和`thymeleaf`渲染，都变成返回一个字符串，`DispatcherServlet`通过反射执行其中方法后根据不同的返回值来进行不同的操作。
>
> `fruitController`中每个方法都需要获取参数，获取参数的动作也让`DispatcherServlet`来执行，这样`fruitController`只需负责执行其相应的业务操作即可，接收前端请求和视图转发交给前端控制器即可。
>
> 注意：在service方法中获取method的参数名时，默认的参数名为`arg0`，`arg1`，而我们需要的是方法的参数名，所以在IDEA中应该在如下处添加参数，使其在编译时，参数名显示的是方法的真实名字

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306041542537.png)

**`fruitController`**

```java
private String del(Integer fid){
    fruitDAO.deleteById(fid);
    // resp.sendRedirect("fruit.do");
    return "redirect:fruit.do";
}

private String update(Integer fid, String fname, Integer price, Integer fcount, String remark) {
    // 执行更新
    fruitDAO.updateById(new Fruit(fid, fname, price, fcount, remark));
    // resp.sendRedirect("fruit.do");
    return "redirect:fruit.do";
}
```



**`DispatcherServlet`**

```java
@Override
protected void service(HttpServletRequest request, HttpServletResponse response) throws IOException {
    request.setCharacterEncoding("utf-8");
    String servletPath = request.getServletPath();
    servletPath = servletPath.substring(1);
    int idx = servletPath.lastIndexOf(".do");
    servletPath = servletPath.substring(0, idx);

    Object beanObj = beanMap.get(servletPath);
    String operate = request.getParameter("operation");
    if (operate == null)
        operate = "index";

    try {
        Method[] methods = beanObj.getClass().getDeclaredMethods();
        for (Method method : methods) {
            if (operate.equals(method.getName())) {
                // 1.获取method的参数
                Parameter[] parameters = method.getParameters();
                // 存放参数的值
                Object[] parameterValue = new Object[parameters.length];

                // 遍历每一个参数
                for (int i = 0; i < parameters.length; i++) {
                    Parameter parameter = parameters[i];
                    // 当前参数名
                    String parameterName = parameter.getName();
                    // 如果参数名是request、response、session
                    if ("request".equals(parameterName))
                        parameterValue[i] = request;
                    else if ("response".equals(parameterName))
                        parameterValue[i] = response;
                    else if ("session".equals(parameterName))
                        parameterValue[i] = request.getSession();
                    else {
                        // 从请求中通过参数名获取参数
                        String paraVal = request.getParameter(parameterName);
                        String typeName = parameter.getType().getName();
                        Object paraObj = paraVal;
                        if (paraObj != null && "java.lang.Integer".equals(typeName)){
                            paraObj = Integer.parseInt(paraVal);
                        }
                        parameterValue[i] = paraObj;
                    }
                }

                method.setAccessible(true);
                Object stringObj = method.invoke(beanObj, parameterValue);

                // 视图处理
                String returnVal = (String) stringObj;
                if (returnVal.startsWith("redirect:")){
                    returnVal = returnVal.substring("redirect:".length());
                    response.sendRedirect(returnVal);
                }else{
                    super.processTemplate(returnVal, request, response);
                }
            }
        }
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }
}
```







## 七、Servlet

### 7.1 Servlet初始化方法

> `Servlet`生命周期：实例化、初始化、服务、销毁



`Servlet`中初始化方法有两个，如果我们想要在`Servlet`初始化时做一些准备工作，那么我们可以重写`init`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306031752561.png)



我们可以通过如下步骤去获取初始化设置的数据
   - 获取config对象：`ServletConfig config = getServletConfig();`
   - 获取初始化参数值： `config.getInitParameter(key);`



**`serlvetContext`**

- 获取上下文参数

​			在初始化方法中：` ServletContxt servletContext = getServletContext();` 

​			在服务方法中也可以通过`request`对象获取：`request.getServletContext()`

​			也可以通过session获取：`session.getServletContext()`

- 获取上下文初始化值：`servletContext.getInitParameter();`



```java
/*
@WebServlet(urlPatterns = {"/Demo01"},
        initParams = {
            @WebInitParam(name = "xrj", value = "123"),
            @WebInitParam(name = "hello", value = "world")
        })
 */
public class Demo01 extends HttpServlet {
    @Override
    public void init() throws ServletException {
        ServletConfig config = getServletConfig();
        System.out.println(config.getInitParameter("xrj"));
        System.out.println(config.getInitParameter("hello"));

        ServletContext servletContext = getServletContext();
        System.out.println(servletContext.getInitParameter("contextConfigLocation"));
    }
}
```



**通过xml文件配置初始化参数**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- ServletContext参数 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>Demo01</servlet-name>
        <servlet-class>com.xrj.servlet.Demo01</servlet-class>
        <init-param>
            <param-name>xrj</param-name>
            <param-value>123</param-value>
        </init-param>
        <init-param>
            <param-name>hello</param-name>
            <param-value>world</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>Demo01</servlet-name>
        <url-pattern>/Demo01</url-pattern>
    </servlet-mapping>
</web-app>
```



**也可以通过注解配置**

> `String[] urlPatterns() default {};`可以接收多个url，即多个url映射到同一个`servlet`
>
> `WebInitParam[] initParams() default {};`接收多个初始化参数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306031801362.png)





### 7.2 ServletConfig类

> `ServletConfig`类是`Servlet`程序的配置信息类
>
> `Servlet`程序和`ServletConfig`对象都是由Tomcat负责创建
>
> `Servlet`程序默认是第一次访问的时候创建，`ServletConfig`是每个`Servlet`程序创建时，就创建一个对应的`ServletConfig`对象



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306032038312.png)

| 方法名                  | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| getServletName()        | 获取<servlet-name>HelloServlet</servlet-name>定义的Servlet名称 |
| **getServletContext()** | 获取ServletContext对象                                       |
| getInitParameter()      | 获取配置Servlet时设置的『初始化参数』，根据名字获取值        |
| getInitParameterNames() | 获取所有初始化参数名组成的Enumeration对象                    |





### 7.3 ServletContext

> `ServletContext`是一个接口，它表示`Servlet`上下文对象
>
> 一个web工程，只有一个`ServletContext`对象实例
>
> `ServletContext`是在web工程部署启动的时候创建，在web工程停止的时候销毁
>
> `ServletContext`对象是一个域对象



功能：

- 获取某个资源的真实路径：`getRealPath()`
- 获取整个Web应用级别的初始化参数：`getInitParameter()`
- 作为Web应用范围的域对象
  - 存入数据：`setAttribute()`
  - 取出数据：`getAttribute()`





![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306032047492.png)







## 八、MVC

### mvc

**MVC** : Model（模型）、View（视图）、Controller（控制器）

- 视图层：用于做数据展示以及和用户交互的一个界面
- 控制层：能够接受客户端的请求，具体的业务功能还是需要借助于模型组件来完成
- 模型层：模型分为很多种：有比较简单的pojo/vo(value object)，有业务模型组件，有数据访问层组件
  - pojo/vo : 值对象
  - DAO ： 数据访问对象
  - BO ： 业务对象



**区分业务对象【service】和数据访问对象【dao】**：

-  DAO中的方法都是单精度方法或者称之为细粒度方法。一个方法只考虑一个操作，比如添加，那就是insert操作、查询那就是select操作

-  BO中的方法属于业务方法，实际的业务是比较复杂的，因此业务方法的粒度是比较粗的

  > 注册这个功能属于业务功能，也就是说注册这个方法属于业务方法。那么这个业务方法中包含了多个DAO方法。也就是说注册这个业务功能需要通过多个DAO方法的组合调用，从而完成注册功能的开发。
  >
  > **注册：**
  >
  > 1、检查用户名是否已经被注册 - DAO中的select操作
  >
  > 2、向用户表新增一条新用户记录 - DAO中的insert操作
  >
  > 3、向用户积分表新增一条记录（新用户默认初始化积分100分） - DAO中的insert操作
  >
  > 4、向系统消息表新增一条记录（某某某新用户注册了，需要根据通讯录信息向他的联系人推送消息） - DAO中的insert操作

将项目增加service层





### IOC

> 耦合/依赖：依赖指的是xxx 离不开 xxx
>
> 在软件系统中，层与层之间是存在依赖的。我们也称之为耦合。
>
> 我们系统架构或者是设计的一个原则是： 高内聚低耦合：层内部的组成应该是高度聚合的，而层与层之间的关系应该是低耦合的，最理想的情况0耦合（就是没有耦合）
>
> IOC - 控制反转 / DI - 依赖注入



在上边的项目加入了service层后，可以发现，controller层必须依赖于service层，service层必须依赖于Dao层，这样他们之间的耦合度就比较高，如果删除的dao层，service层就会报错，删除service层后，controller层就会报错，因为每层都会有`FruitDAO fruitDAO = new FruitDAOImpl();` 所以会产生依赖。

`spring`的核心就是ioc，即在项目启动时，就将所有的组件【service、dao等】放在他的ioc容器中，哪里需要使用，那么我就从ioc容器中给他，减少了代码的耦合，因此每一层可以写为`fruitService fruitService = null;` 赋为`null`后，他就不会依赖于其他层了。而这个`fruitService`我们就需要从我们的容器中给他赋值。和前端控制器哪里一样，我们将所有的组件存放到一个`beanMap`中，然后通过反射去给它赋值



**`applicationContext.xml`**

在这里我们将`fruitDao`和`fruitService`也配到`bean`中，同时`fruitService`的属性`fruitDao`我们用`property`标签表示，`fruitController`的属性`fruitService`也用`property`标签表示，其中`name`表示其属性名，`ref`表示其引用的bean对象

> ```xml
> <?xml version="1.0" encoding="utf-8" ?>
> 
> <beans>
>     <bean id="fruitDao" class="com.xrj.fruit.dao.impl.FruitDaoImpl"/>
>     <bean id="fruitService" class="com.xrj.fruit.service.Impl.fruitServiceImpl">
>         <!-- property标签用来表示fruitService的属性，name是属性名，ref是引用其他bean的id -->
>         <property name="fruitDao" ref="fruitDao"/>
>     </bean>
>     <bean id="fruit" class="com.xrj.fruit.Controller.fruitController">
>         <property name="fruitService" ref="fruitService"/>
>     </bean>
> </beans>
> 
> <!--
> 在xml中Node是一个接口，Element和Text是其子接口，Element是元素节点，Text是文本节点
> 例如: 下边bean是一个Element节点，它有3个子节点，①property前的换行和空格,这是一个文本节点 ②property节点,这是一个Element节点
> ③property之后的空格和换行,是一个Text文本节点
> <bean id="fruit" class="com.xrj.fruit.Controller.fruitController">
>         <property name="fruitService" ref="fruitService"/>
> </bean>
> 
> Node 节点
>     Element 元素节点
>     Text 文本节点
> -->
> ```



**`BeanFactory`**

```java
public interface BeanFactory {
    public Object getBean(String id);
}
```



**`ClassPathXmlApplicationContext`**

> 我们将上一个版本`DispatcherServlet`中解析`application.xml`文件的代码放到这里来，因此在`DispatcherServlet`中获取`controller`对象就直接通过`getBean(String id)`获取。这里我们需要额外设置bean之间的依赖关系。对于得到的bean节点，通过遍历他的子节点，找到property的节点，然后获取property节点的name和ref，通过`BeanMap`得到ref对象实例，然后将ref对象通过反射设置给当前bean对象。



`InputStream inputStream = getClass().getClassLoader().getResourceAsStream("applicationContext.xml");`

这里是通过类加载器来读取文件的，它和直接通过`inputstream`读是有区别的

- **资源路径的解析方式不同**：使用 `getResourceAsStream()` 方法时，路径参数是相对于**类加载器的根路径**进行解析的。这意味着可以直接使用类路径`（Classpath）`中的相对路径来引用资源文件，而无需考虑文件系统中的具体位置。而直接使用 `InputStream` 则需要提供资源文件在文件系统中的绝对路径或相对路径。

- **获取资源文件的方式不同**：`getResourceAsStream()` 方法会使用类加载器来加载资源文件，并将其作为输入流返回。这种方式更适合于获取类路径下的资源文件，例如配置文件或类文件。而直接使用 `InputStream` 需要通过文件输入流或网络流等方式来获取数据。

- 配置文件直接放在`src`目录下的话，默认的项目配置中，编译器会将 `src` 目录中的资源文件复制到输出目录的根路径下，使它们可以直接被类加载器访问。可以看到`src`目录下的文件编译后放在了`out/production`下，可以直接被类加载器获取

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306122016767.png)

```java
public class ClassPathXmlApplicationContext implements BeanFactory{
    Map<String, Object> beanMap = new HashMap<>();

    public ClassPathXmlApplicationContext(){
        try {
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("applicationContext.xml");
            //1.创建DocumentBuilderFactory
            DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
            //2.创建DocumentBuilder对象
            DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder() ;
            //3.创建Document对象
            Document document = documentBuilder.parse(inputStream);

            //4.获取所有的bean节点
            NodeList beanNodeList = document.getElementsByTagName("bean");
            for (int i = 0; i < beanNodeList.getLength(); i++) {
                Node beanNode = beanNodeList.item(i);
                if (beanNode.getNodeType() == Node.ELEMENT_NODE) {
                    Element elementBean = (Element) beanNode;
                    String beanId = elementBean.getAttribute("id");
                    String className = elementBean.getAttribute("class");
                    Class beanClass = Class.forName(className);
                    Object bean = beanClass.newInstance();
                    beanMap.put(beanId, bean);
                }
            }

            //5.组装bean之间的依赖关系
            for (int i = 0; i < beanNodeList.getLength(); i++) {
                Node beanNode = beanNodeList.item(i);
                if (beanNode.getNodeType() == Node.ELEMENT_NODE) {
                    Element elementBean = (Element) beanNode;
                    String beanId = elementBean.getAttribute("id");
                    // elementBean现在就是一个bean节点,先得到他所有的子节点
                    NodeList childNodes = elementBean.getChildNodes();
                    for (int j = 0; j < childNodes.getLength(); j++) {
                        Node propertyNode = childNodes.item(j);
                        // 如果当前节点是元素节点并且节点名为property,那么获取name和ref
                        if (propertyNode.getNodeType() == Node.ELEMENT_NODE && "property".equals(propertyNode.getNodeName())){
                            Element elementNode = (Element) propertyNode;
                            // propertyName是beanObj中的属性名, propertyRef是beanObj中属性名对应的引用bean
                            String propertyName = elementNode.getAttribute("name");
                            String propertyRef = elementNode.getAttribute("ref");
                            // 获取ref对应的对象实例，然后将该实例赋值给beanObj
                            Object beanObj = getBean(beanId);
                            Object refObj = getBean(propertyRef);
                            Class beanClass = beanObj.getClass();
                            Field fieldName = beanClass.getDeclaredField(propertyName);
                            fieldName.setAccessible(true);
                            fieldName.set(beanObj, refObj);
                        }
                    }
                }
            }
        } catch (SAXException e) {
            e.printStackTrace();
        } catch (ParserConfigurationException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException | NoSuchFieldException e) {
            e.printStackTrace();
        }
    }
    @Override
    public Object getBean(String id) {
        return beanMap.get(id);
    }
}
```





**`IOC` ：控制反转** 

- 之前在`Servlet`中，我们创建service对象 ， `FruitService fruitService = new FruitServiceImpl();`
  这句话如果出现在servlet中的某个方法内部，那么这个fruitService的作用域（生命周期）应该就是这个方法级别；
  如果这句话出现在servlet的类中，也就是说fruitService是一个成员变量，那么这个fruitService的作用域（生命周期）应该就是这个servlet实例级别

- 之后我们在`applicationContext.xml`中定义了这个`fruitService`。然后通过解析XML，产生`fruitService`实例，存放在`beanMap`中，这个`beanMap`在一个`BeanFactory`【ioc容器】中。因此，我们转移（改变）了之前的service实例、dao实例等等他们的生命周期。**控制权从程序员转移到`BeanFactory`**。这个现象我们称之为**控制反转**



**DI：依赖注入：**

- 之前我们在控制层出现代码：`FruitService fruitService = new FruitServiceImpl()；`
  那么，控制层和service层存在耦合。

- 之后，我们将代码修改成`FruitService fruitService = null ;`

  然后，在配置文件中配置，通过反射将其所需要的属性进行赋值

  ```xml
  <bean id="fruit" class="FruitController">
       <property name="fruitService" ref="fruitService"/>
  </bean>
  ```

  





## 九、Filter过滤器

> Filter 过滤器是 JavaWeb 的三大组件之一。三大组件分别是：Servlet 程序、Listener 监听器、Filter 过滤器 
>
> Filter 过滤器它是 JavaEE 的规范。也就是接口 
>
> Filter 过滤器它的作用是：拦截请求，过滤响应。 



**Filter开发步骤：**

新建类实现Filter接口，然后实现其中的三个方法：`init`、`doFilter`、`destroy`

**`doFilter`**

- 在`doFilter()`方法中执行过滤
- 如果满足过滤条件使用 `chain.doFilter(request, response);`放行
- 如果不满足过滤条件转发或重定向请求



配置Filter，可以用注解`@WebFilter`，也可以使用`xml`文件` <filter> <filter-mapping>`【和配置servlet一样】

> 在filter拦截到请求时，先执行`filterChain.doFilter(servletRequest, servletResponse);`之前的代码，然后进入`servlet`执行，`servlet`执行完之后，执行`filterChain.doFilter(servletRequest, servletResponse);`之后的代码，不管`servlet`有没有给客户端发回响应

```java
package com.xrj.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter("*.do")  // 可以使用注解，也可以使用xml配置文件
public class Demo03Filter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("hello C1");
        filterChain.doFilter(servletRequest, servletResponse);
        System.out.println("hello C2");
    }

    @Override
    public void destroy() {

    }
}
```

> 下面的过滤器执行顺序就是1 3 2 ，按照`filter-mapping`的顺序来

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <filter>
        <filter-name>Demo01Filter</filter-name>
        <filter-class>com.xrj.filter.Demo01Filter</filter-class>
    </filter>
    <filter>
        <filter-name>Demo02Filter</filter-name>
        <filter-class>com.xrj.filter.Demo02Filter</filter-class>
    </filter>
    <filter>
        <filter-name>Demo03Filter</filter-name>
        <filter-class>com.xrj.filter.Demo03Filter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>Demo01Filter</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>Demo03Filter</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>
    <filter-mapping>
        <filter-name>Demo02Filter</filter-name>
        <url-pattern>*.do</url-pattern>
    </filter-mapping>
</web-app>
```





**Filter的生命周期**

> 和`Servlet`生命周期类比，Filter生命周期的关键区别是：**在Web应用启动时创建对象**

Filter 的生命周期包含几个方法 

1、构造器方法 

2、`init` 初始化方法 

​	第 1，2 步，在 web 工程启动的时候执行（Filter 已经创建） 

3、doFilter 过滤方法 

​	第 3 步，每次拦截到请求，就会执行 

4、destroy 销毁 

​	第 4 步，停止 web 工程的时候，就会执行（停止 web 工程，也会销毁 Filter 过滤器）

| 生命周期阶段 | 执行时机         | 执行次数 |
| ------------ | ---------------- | -------- |
| 创建对象     | Web应用启动时    | 一次     |
| 初始化       | 创建对象后       | 一次     |
| 拦截请求     | 接收到匹配的请求 | 多次     |
| 销毁         | Web应用卸载前    | 一次     |





**FilterConfig**

`FilterConfig` 是 Filter 过滤器的配置文件类。 

Tomcat 每次创建 Filter 的时候，也会同时创建一个 FilterConfig 类，这里包含了 Filter 配置文件的配置信息。 



FilterConfig 类的作用是获取 filter 过滤器的配置内容 

1、获取 Filter 的名称 filter-name 的内容 

2、获取在 Filter 中配置的 init-param 初始化参数 

3、获取`ServletContext `对象

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306051838127.png)





**`FilterChain`**

![image-20211214171059418](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20211214171059418.png)

> Filter过滤器在执行时，如果使用xml文件配置，过滤顺序是按照配置的顺序；如果使用注解，过滤顺序是按照全类名的先后顺序
>







**Filter 的拦截路径** 

- 精确匹配 

`<url-pattern>/hello.html</url-pattern> `

以上配置的路径，表示请求地址必须为：`http://ip:port/工程路径/hello.html`



- 目录匹配 

`<url-pattern>/admin/*</url-pattern> `

以上配置的路径，表示请求地址必须为：`http://ip:port/工程路径/admin/* `



- 后缀名匹配 

`<url-pattern>*.html</url-pattern> `

以上配置的路径，表示请求地址必须以`.html `结尾才会拦截到 



`<url-pattern>*.do</url-pattern> `

以上配置的路径，表示请求地址必须以`.do` 结尾才会拦截到 



Filter 过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在









## 十、ThreadLocal

> 我们在dao层如果有很多条sql语句，我们会开启事务，将dao层所有sql语句都执行成功后再提交，有一条失败就回滚。
>
> 但是如果在service层中有很多个dao，每个dao都开启了事物，此时第一个dao执行成功，提交了，第二个dao执行失败回滚了，第三个dao执行成功提交了。那么当前service层的业务方法是成功还是失败？所以我们需要将事务控制提前，dao层是单精度方法，service层是粗精度方法，我们应该让service的一个业务方法中的所有dao都执行成功，才可以提交，有一个dao执行失败就要回滚。
>
> 因此我们可以将事务控制提前到service层，但是service层主要做的是业务功能，事务操作放到这里，太过混乱，并且每个service都需要进行事务控制，造成代码冗余，所以我们将事务的一系列操作提前到filter过滤器上。在收到一个请求后，会被过滤器拦截，在过滤器中开始事务，然后放行，如果没有异常，就提交事务，如果有异常就回滚。
>
> 需要注意的是，如果想让filter能够catch到异常，那么dao层、service层遇到异常必须向上抛出【最好抛一个运行时异常】，因为如果他们自己catch了，那么filter就收不到异常，也就不会回滚了。
>
> 现在需要做的就是让一个业务方法中所有的dao操作用的connection连接是相同的，这样才可以使用事务管理，可以将connection作为参数参进去，也可以使用`ThreadLocal`【推荐】



`ConnUtil`用来获取`ThreadLocal`的连接，这时在`BaseDao`中的连接也要从`ThreadLocal`获取，并且执行完`sql`语句不能关闭连接，这样业务层中的每个`dao`用的连接都是相同的。

```java
package com.xrj.fruit.utils;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConnUtil {
    public static ThreadLocal<Connection> threadLocal = new ThreadLocal<>();
    public static final String DRIVER = "com.mysql.cj.jdbc.Driver" ;
    public static final String URL = "jdbc:mysql://localhost:3306/fruitdb?useUnicode=true&characterEncoding=utf-8&useSSL=false";
    public static final String USER = "root";
    public static final String PWD = "root" ;

    public static Connection createConn()  {
        try {
            Class.forName(DRIVER);
            Connection connection = DriverManager.getConnection(URL, USER, PWD);
            return connection;
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }

    public static Connection getConn(){
        Connection conn = threadLocal.get();
        if (conn == null) {
            conn = createConn();
            threadLocal.set(conn);
        }
        return conn;
    }

    public static void close() throws SQLException {
        Connection connection = threadLocal.get();
        if (!connection.isClosed()){
            connection.close();
            threadLocal.set(null);
        }
    }
}
```



`TransactionManager`对事务进行管理，在`commit`或者`rollback`后关闭连接

```java
public class TransactionManager {
    public static void beginTrans() throws SQLException {
        ConnUtil.getConn().setAutoCommit(false);
    }

    public static void commit() throws SQLException {
        ConnUtil.getConn().commit();
        ConnUtil.close();
    }

    public static void rollback() throws SQLException {
        ConnUtil.getConn().rollback();
        ConnUtil.close();
    }
}
```



`OpenSessionInViewFilter`过滤器来开启事务，有异常就回滚

```java
@WebFilter("*.do")
public class OpenSessionInViewFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        try {
            // 开启事务
            TransactionManager.beginTrans();
            System.out.println("开启事务...");
            // 放行
            filterChain.doFilter(servletRequest, servletResponse);
            // 提交事务
            TransactionManager.commit();
            System.out.println("提交事务...");
        } catch (Exception e) {
            e.printStackTrace();
            // 回滚事务
            try {
                TransactionManager.rollback();
                System.out.println("回滚事务");
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
        }
    }

    @Override
    public void destroy() {

    }
}
```





**`ThreadLocal`**

> `ThreadLocal`用来提供线程内部的局部变量，他可以保存connection，也可以保存其他数据



**`set`方法**

> `set`方法，首先获取当前线程，然后通过`ThreadLocalMap map = getMap(t);`获取当前线程维护的`ThreadLocalMap`，每一个线程都维护各自的一个容器`ThreadLocalMap`。如果当前的`map`为空，就去初始化。否则就往`map`添加数据，其中`key`是当前的`ThreadLocal`对象，`value`是添加的值。一个线程有一个`threadlocalMap`，而`threadlocalMap`中有多个`ThreadLocal`对象，每个`ThreadLocal`可以保存不同的值，所以`set`的时候`key`是当前`ThreadLocal`对象。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306061123385.png)

> 如果`ThreadLocalMap`为空，那么就根据当前线程，去新建一个`ThreadLocalMap`，同时将当前数据加入，`key`是当前`ThreadLocal`对象，`value`是保存的值

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306061125145.png)







**`get方法`**

> 首先获取当前线程，根据当前线程获取`ThreadLocalMap`，如果`map`不为空，那么`key`就是当前`ThreadLocal`对象，根据`key`获取保存的值

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306061129134.png)









## 十一、Listener监听器

> 监听器是设计模式中的观察者模式

- 观察者：监控『被观察者』的行为，一旦发现『被观察者』触发了事件，就会调用事先准备好的方法执行操作。
- 被观察者：『被观察者』一旦触发了被监控的事件，就会被『观察者』发现。





**监听器的分类**

- 域对象监听器
- 域对象的属性域监听器
- Session域中数据的监听器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306061526818.png)





### 域对象监听器

- `ServletContextListener ` ： 监听`ServletContext`对象的创建和销毁的过程

| 方法名                                      | 作用                     |
| ------------------------------------------- | ------------------------ |
| contextInitialized(ServletContextEvent sce) | ServletContext创建时调用 |
| contextDestroyed(ServletContextEvent sce)   | ServletContext销毁时调用 |

> `ServletContextEvent`对象代表从`ServletContext`对象身上捕获到的事件，通过这个事件对象我们可以获取到`ServletContext`对象



- `HttpSessionListener` ：监听`HttpSession`对象的创建和销毁的过程

| 方法名                                 | 作用                      |
| -------------------------------------- | ------------------------- |
| sessionCreated(HttpSessionEvent hse)   | HttpSession对象创建时调用 |
| sessionDestroyed(HttpSessionEvent hse) | HttpSession对象销毁时调用 |

> `HttpSessionEvent`对象代表从`HttpSession`对象身上捕获到的事件，通过这个事件对象我们可以获取到触发事件的`HttpSession`对象



- `ServletRequestListener` ： 监听`ServletRequest`对象的创建和销毁的过程

| 方法名                                      | 作用                         |
| ------------------------------------------- | ---------------------------- |
| requestInitialized(ServletRequestEvent sre) | ServletRequest对象创建时调用 |
| requestDestroyed(ServletRequestEvent sre)   | ServletRequest对象销毁时调用 |

> `ServletRequestEvent`对象代表从`HttpServletRequest`对象身上捕获到的事件，通过这个事件对象我们可以获取到触发事件的`HttpServletRequest`对象。另外还有一个方法可以获取到当前Web应用的`ServletContext`对象。





### 域对象的属性域监听器

- `ServletContextAttributeListener` ： 监听`ServletContext`的保存作用域的改动(add,remove,replace)

| 方法名                                               | 作用                                 |
| ---------------------------------------------------- | ------------------------------------ |
| attributeAdded(ServletContextAttributeEvent scab)    | 向ServletContext中添加属性时调用     |
| attributeRemoved(ServletContextAttributeEvent scab)  | 从ServletContext中移除属性时调用     |
| attributeReplaced(ServletContextAttributeEvent scab) | 当ServletContext中的属性被修改时调用 |

`ServletContextAttributeEvent`对象代表属性变化事件，它包含的方法如下：

| 方法名              | 作用                     |
| ------------------- | ------------------------ |
| getName()           | 获取修改或添加的属性名   |
| getValue()          | 获取被修改或添加的属性值 |
| getServletContext() | 获取ServletContext对象   |



- `HttpSessionAttributeListener` ： 监听`HttpSession`的保存作用域的改动(add,remove,replace)

| 方法名                                        | 作用                              |
| --------------------------------------------- | --------------------------------- |
| attributeAdded(HttpSessionBindingEvent se)    | 向HttpSession中添加属性时调用     |
| attributeRemoved(HttpSessionBindingEvent se)  | 从HttpSession中移除属性时调用     |
| attributeReplaced(HttpSessionBindingEvent se) | 当HttpSession中的属性被修改时调用 |

`HttpSessionBindingEvent`对象代表属性变化事件，它包含的方法如下：

| 方法名       | 作用                          |
| ------------ | ----------------------------- |
| getName()    | 获取修改或添加的属性名        |
| getValue()   | 获取被修改或添加的属性值      |
| getSession() | 获取触发事件的HttpSession对象 |



- `ServletRequestAttributeListener` ：监听`ServletRequest`的保存作用域的改动(add,remove,replace)

| 方法名                                               | 作用                                 |
| ---------------------------------------------------- | ------------------------------------ |
| attributeAdded(ServletRequestAttributeEvent srae)    | 向ServletRequest中添加属性时调用     |
| attributeRemoved(ServletRequestAttributeEvent srae)  | 从ServletRequest中移除属性时调用     |
| attributeReplaced(ServletRequestAttributeEvent srae) | 当ServletRequest中的属性被修改时调用 |

`ServletRequestAttributeEvent`对象代表属性变化事件，它包含的方法如下：

| 方法名               | 作用                             |
| -------------------- | -------------------------------- |
| getName()            | 获取修改或添加的属性名           |
| getValue()           | 获取被修改或添加的属性值         |
| getServletRequest () | 获取触发事件的ServletRequest对象 |





### Session域中数据的监听器

- `HttpSessionBindingListener` ： 监听某个对象在Session域中的创建与移除

| 方法名                                      | 作用                              |
| ------------------------------------------- | --------------------------------- |
| valueBound(HttpSessionBindingEvent event)   | 该类的实例被放到Session域中时调用 |
| valueUnbound(HttpSessionBindingEvent event) | 该类的实例从Session中移除时调用   |

`HttpSessionBindingEvent`对象代表属性变化事件，它包含的方法如下：

| 方法名       | 作用                          |
| ------------ | ----------------------------- |
| getName()    | 获取当前事件涉及的属性名      |
| getValue()   | 获取当前事件涉及的属性值      |
| getSession() | 获取触发事件的HttpSession对象 |



- `HttpSessionActivationListener` ： 监听某个对象在Session域中的序列化和反序列化

| 方法名                                    | 作用                                      |
| ----------------------------------------- | ----------------------------------------- |
| sessionWillPassivate(HttpSessionEvent se) | 该类实例和Session一起序列化到硬盘时调用   |
| sessionDidActivate(HttpSessionEvent se)   | 该类实例和Session一起反序列化到内存时调用 |

`HttpSessionEvent`对象代表事件对象，通过`getSession()`方法获取事件涉及的`HttpSession`对象。







### 监听器的使用

> 下面是`ServletContextListener`，在web项目启动时会创建`servletContext`，此时会被监听器监听到，会执行`contextInitialized`方法，项目关闭后，就会销毁`servletContext`，此时执行`contextDestroyed`方法
>
> 配置监听器可以使用注解`@WebListener`；也可以使用配置文件的方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <listener>
        <listener-class>com.xrj.listener.ListenerDemo01</listener-class>
    </listener>
</web-app>
```

```java
@WebListener
public class ListenerDemo01 implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("监听到servletContext创建");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("监听到servletContext销毁");
    }
}
```





**项目优化**

在我们的水果项目中，在`DispatcherServlet`的`init`方法中我们执行了`ClassPathXmlApplicationContext`，也就是说当有请求访问时，我们才去创建ioc容器，但是`DispatcherServlet`是mvc的组件，他的作用是接收请求，并做一系列的视图转发，对于ioc容器初始化这些工作应该在`servletContext`创建的时候【项目启动的时候】就创建好，而不是说项目启动了，有请求来访问，此时才去创建ioc容器，这样会导致请求的时间较长。而我们把ioc容器初始化的过程放在`servletContext`创建的时候执行，虽然项目启动慢了，但是请求速度变快了，而且ioc容器的返回也应该是处于`servletContext`这一级别的。

所以我们使用`ServletContextListener`监听器，只要监听到`servletContext`创建了，那么我就去创建ioc容器，并将该容器保存到`servletContext`域中，这样后边获取实例就可以从`servletContext`获取了。



**`ContextLoaderListener`**

> 为了不在`ClassPathXmlApplicationContext`直接使用硬编码的方式，即直接将配置文件名写入代码中，我们将`applicationContext.xml`写到配置文件中，作为`servletContext`的初始化参数，通过`ServletContext`获取到path后，将path传入`ClassPathXmlApplicationContext`进行ioc容器实例化。
>
> 这样以后修改文件名，不需要修改代码，只需要修改配置文件即可。

```java
@WebListener
public class ContextLoaderListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        // 1.获取servletContext对象
        ServletContext application = servletContextEvent.getServletContext();
        // 2.获取applicationContext.xml文件路径
        String path = application.getInitParameter("contextConfigLocation");
        // 3.获取beanFactory对象
        BeanFactory beanFactory = new ClassPathXmlApplicationContext(path);
        // 4.保存到servletContext域
        application.setAttribute("beanFactory", beanFactory);
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>applicationContext.xml</param-value>
    </context-param>
</web-app>
```



**`DispatcherServlet`**

```java
public void init() throws ServletException {
    super.init();
    // 通过servletContext域来获取beanFactory对象
    ServletContext application = getServletContext();
    Object beanFactoryObj = application.getAttribute("beanFactory");
    if (beanFactoryObj != null) {
        beanFactory = (BeanFactory) beanFactoryObj;
    }else{
        throw new RuntimeException("IOC容器获取失败");
    }
}
```









## 十二、Kaptcha验证码

`Kaptcha`验证码的使用

> `Kaptcha`本质上也是一个`servlet`，`kaptcha`验证码图片的各个属性在常量接口：Constants中

- 添加jar包

- 在`web.xml`文件中注册`KaptchaServlet`，并设置验证码图片的相关属性

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      <servlet>
          <servlet-name>KaptchaServlet</servlet-name>
          <servlet-class>com.google.code.kaptcha.servlet.KaptchaServlet</servlet-class>
          <init-param>
              <!-- 边框颜色 -->
              <param-name>kaptcha.border.color</param-name>
              <param-value>red</param-value>
          </init-param>
          <init-param>
              <!-- 验证码的字符范围 -->
              <param-name>kaptcha.textproducer.char.string</param-name>
              <param-value>abcdefg</param-value>
          </init-param>
          <init-param>
              <!-- 图片去除噪声 -->
              <param-name>kaptcha.noise.impl</param-name>
              <param-value>com.google.code.kaptcha.impl.NoNoise</param-value>
          </init-param>
      </servlet>
      <servlet-mapping>
          <servlet-name>KaptchaServlet</servlet-name>
          <url-pattern>/kaptch.jpg</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

- 在`html`页面上编写一个`img`标签，然后设置`src`等于`KaptchaServlet`对应的`url-pattern`

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
      <h1>Cookie 保存成功</h1>
      <img src="/web17/kaptch.jpg"/>
  </body>
  </html>
  ```

> `img`标签的`src`路径是`KaptchaServlet`的`urlPattern`，所以会直接访问对应的`servlet`，然后`KaptchaServlet`将对应的验证码图片返回给前端，同时将验证码信息保存到session中，key为`KAPTCHA_SESSION_KEY`。 因此，我们在注册请求时，首先将用户文本框中输入的验证码值和session中保存的值进行比较，相等，则进行注册







## 十三、JS中的正则表达式

**正则表达式三个用途**

- 模式验证: 检测某个字符串是否符合规则，例如检测手机号、身份证号等等是否符合规范

  ```js
  // 创建一个最简单的正则表达式对象
  var reg = /o/;
  
  // 创建一个字符串对象作为目标字符串
  var str = 'Hello World!';
  
  // 调用正则表达式对象的test()方法验证目标字符串是否满足我们指定的这个模式，返回结果true
  console.log("字符串中是否包含'o'=" + reg.test(str));
  ```

- 匹配读取:  将目标字符串中满足规则的部分**读取**出来，例如将整段文本中的邮箱地址读取出来

  ```js
  //匹配读取: 读取一个字符串中的所有'l'字母
  // g表示全文查找,如果不使用g那么就只能查找到第一个匹配的内容
  //1. 编写一个正则表达式
  var reg2 = /l/g
  //2. 使用正则表达式去读取字符串
  var arr = str.match(reg2);
  console.log(arr)
  ```

- 匹配替换:  将目标字符串中满足标准的部分**替换**为其他字符串

  ```js
  var newStr = str.replace(reg,'@');
  // 只有第一个o被替换了，说明我们这个正则表达式只能匹配第一个满足的字符串
  console.log("str.replace(reg)=" + newStr);//Hell@ World!
  // 原字符串并没有变化，只是返回了一个新字符串
  console.log("str=" + str);//str=Hello World!
  ```





**使用正则表达式的步骤：**

1、定义正则表达式对象

* 对象形式：`var reg = new RegExp("正则表达式")`  当正则表达式中有"/"那么就使用这种
* 直接量形式：`var reg = /正则表达式/`   一般使用这种声明方式 

2、定义待校验的字符串

3、校验



**匹配模式**

 - g 全局匹配
 - i 忽略大小写匹配
 - m 多行匹配
 - gim这三个可以组合使用，不区分先后顺序



提交注册时的`form`表单，加上`onsubmit="return checkRegist()`表示在提交时先进行js验证，如果返回true就可以提交，返回false就不提交

```html
<!-- 提交的时候先进行js验证，返回true就提交，返回false不提交 -->
<form th:action="@{user.do}" method="post" onsubmit="return checkRegist()">
  <input type="hidden" name="operate" value="regist">
  <div class="form-item">
    <div>
      <label>用户名称:</label>
      <!-- onblur表示失去焦点就触发ckUname函数 -->
      <input id="unameTex" type="text" name="uname" placeholder="请输入用户名" onblur="ckUname(this.value)"/>
    </div>
    <span id="unameSpan" class="errMess">用户名应为6~16位数组和字母组成</span>
  </div>
  <div class="form-item">
    <div>
      <label>用户密码:</label>
      <input id="pwdTex" type="password" name="pwd" placeholder="请输入密码" />
    </div>
    <span id="pwdSpan" class="errMess">密码的长度至少为8位</span>
  </div>
  <div class="form-item">
    <div>
      <label>确认密码:</label>
      <input id="pwdTex2" type="password" placeholder="请输入确认密码" />
    </div>
    <span id="pwdSpan2" class="errMess">密码两次输入不一致</span>
  </div>
  <div class="form-item">
    <div>
      <label>用户邮箱:</label>
      <input id="emailTex" type="text" name="email" placeholder="请输入邮箱" />
    </div>
    <span id="emailSpan" class="errMess">请输入正确的邮箱格式</span>
  </div>
  <div class="form-item">
    <div>
      <label>验证码:</label>
      <div class="verify">
        <input type="text" name="verifyCode" placeholder="" />
        <img th:src="@{/Kaptcha.jpg}" alt="" />
      </div>
    </div>
    <span class="errMess">请输入正确的验证码</span>
  </div>
  <button class="btn">注册</button>
</form>
```



注册校验的js代码

```js
function $(id){
    return document.getElementById(id);
}

function checkRegist(){
    var unameTex = $("unameTex");
    var uname = unameTex.value;
    var unameSpan = $("unameSpan");
    // 用户名应为6~16位数组和字母组成
    var unameReg = /^[0-9a-zA-Z]{6,16}$/;
    if (!unameReg.test(uname)){
        unameSpan.style.visibility="visible";
        return false;
    }else{
        unameSpan.style.visibility="hidden";
    }

    var pwdTex = $("pwdTex");
    var pwd = pwdTex.value;
    var pwdSpan = $("pwdSpan");
    // 密码的长度至少为8位
    var pwdReg = /.{8,}/;
    if (!pwdReg.test(pwd)){
        pwdSpan.style.visibility="visible";
        return false;
    }else{
        pwdSpan.style.visibility="hidden";
    }

    var pwd2 = $("pwdTex2").value;
    var pwdSpan = $("pwdSpan2");
    if (pwd2 != pwd){
        pwdSpan.style.visibility="visible";
        return false;
    }else{
        pwdSpan.style.visibility="hidden";
    }

    var emailTex = $("emailTex");
    var email = emailTex.value;
    var emailSpan = $("emailSpan");
    var emailReg = /^[a-zA-Z0-9_\.-]+@([a-zA-Z0-9-]+[\.]{1})+[a-zA-Z]+$/;
    if (!emailReg.test(email)){
        emailSpan.style.visibility="visible";
        return false;
    }else{
        emailSpan.style.visibility="hidden";
    }

    return true;
}
```







## 十四、Ajax异步请求

> AJAX 即`Asynchronous Javascript And XML`（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发 技术。
>
> ajax 是一种浏览器通过 js 异步发起请求，局部更新页面的技术。 
>
> Ajax 请求的局部更新，浏览器地址栏不会发生变化 
>
> 局部更新不会舍弃原来页面的内容
>
> 客户端向服务器发送请求，然后服务器向客户端发回响应，但是在发回响应的这段时间中，客户端的页面是空白，如果网速很慢，用户什么都不能做。比如在一个新闻页面上，用户可以浏览各种新闻，但是不登录是无法评论点赞的，当用户输入账号密码进行登录时，我们希望的是在这个页面上用户可以继续浏览，仅仅是登录的地方发送请求，而不是说直接向服务器发送请求，服务器在返回数据，这样网速慢的话，点击登录后页面为空白，用户什么都无法做。因此可以使用Ajax异步请求，即新开一个线程，将登录信息发送给服务器，如果登录成功，服务器只需返回登录成功的信息即可，其他html代码无需返回，也节约了资源。





**使用原生Ajax**

1、客户端发送异步请求；并绑定对结果处理的回调函数

> 这里我们进行用户名是否已被注册校验
>
> 当输入用户名的框失去焦点时就会触发`ckUname`事件

```html
<input id="unameTex" type="text" name="uname" placeholder="请输入用户名" onblur="ckUname(this.value)"/>
```



（1）创建`XMLHttpRequest`对象，调用该对象的open方法，参数分别为请求方式、请求的url以及是否是异步请求

（2）设置回调函数，状态发生改变就调用这个函数

（3）`xmlHttpRequest.send();`发送请求

在回调函数`ckUnameCB`中需要判断`XMLHttpRequest`对象的状态，如果满足就对服务端发回的响应进行处理

> 0 － （未初始化）还没有调用send()方法
> 1 － （载入）已调用send()方法，正在发送请求
> 2 － （载入完成）send()方法执行完成，已经接收到全部响应内容
> 3 － （交互）正在解析响应内容
> 4 － （完成）响应内容解析完成，可以在客户端调用了

```js
// 如果想要发送异步请求，需要一个对象 XMLHttpRequest
var xmlHttpRequest;

// 创建XMLHttpRequest
function createXMLHttpRequest(){
    if(window.XMLHttpRequest){
        //符合DOM2标准的浏览器 ，xmlHttpRequest的创建方式
        xmlHttpRequest = new XMLHttpRequest() ;
    }else if(window.ActiveXObject){//IE浏览器
        try{
            xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");
        }catch (e) {
            xmlHttpRequest = new ActiveXObject("Msxml2.XMLHTTP");
        }
    }
}

function ckUname(name){
    createXMLHttpRequest();
    var url = "user.do?operate=ckUname&name=" + name;
    /*
        1. 调用open方法设置请求参数
            第一个参数表示get还是post
            第二个参数表示请求地址
            第三个参数表示是否是异步请求，true表示异步请求
     */
    xmlHttpRequest.open("GET", url, true);
    // 2. 设置回调函数
    xmlHttpRequest.onreadystatechange = ckUnameCB;
    // 3. 发送请求
    xmlHttpRequest.send();
}

function ckUnameCB() {
    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
        // xmlHttpRequest.responseText 表示 服务器端响应给客户端的文本内容
        // alert(xmlHttpRequest.responseText);
        var responseText = xmlHttpRequest.responseText ;
        // {'uname':'1'}
        alert(responseText);
        if(responseText=="{'name':'1'}"){
            alert("用户名已经被注册！");
        }else{
            alert("用户名可以注册！");
        }
    }
}
```



2、服务器端做校验，然后将校验结果响应给客户端

```java
public String ckUname(String name) {
    User user = userService.getUser(name);
    if (user != null) {
        // 不可以注册
        return "json:{'name':'1'}";
    }else{
        // 可以注册
        return "json:{'name':'0'}";
    }
}

// DispatcherServlet
// 视图处理
String returnVal = (String) stringObj;
if (returnVal.startsWith("redirect:")){
    returnVal = returnVal.substring("redirect:".length());
    response.sendRedirect(returnVal);
}else if (returnVal.startsWith("json:")){
    returnVal = returnVal.substring("json:".length());
    PrintWriter out = response.getWriter();
    out.print(returnVal);
    out.flush();
} else{
    super.processTemplate(returnVal, request, response);
}
```








## 十五、Vue

> `Vue`是一个`js`框架，`Thymeleaf`需要经过服务器进行页面渲染，而`Vue`在客户端就可以进行数据的渲染



**`Vue`的使用：**

`html`代码

```html
<!-- 使用{{}}格式，指定要被渲染的数据 -->
<div id="app">{{message}}</div>
```

`vue`代码

```javascript
// 1.创建一个JSON对象，作为new Vue时要使用的参数
var argumentJson = {
	
	// el用于指定Vue对象要关联的HTML元素。el就是element的缩写
	// 通过id属性值指定HTML元素时，语法格式是：#id
	"el":"#app",
	
	// data属性设置了Vue对象中保存的数据
	"data":{
		"message":"Hello Vue!"
	}
};

// 2.创建Vue对象，传入上面准备好的参数
var app = new Vue(argumentJson);
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306211714976.png)





### 1、绑定元素属性

**绑定元素属性：**`v-bind:HTML标签的原始属性名`，例如`v-bind:value=""`、`v-bind:src=""`

**双向绑定：**`v-model:标签`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script language="JavaScript" src="script/vue.js"></script>
    <script language="JavaScript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    msg:"hello",
                    name:"xrj"
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <!-- 在标签体内通过{{}}告诉Vue这里需要渲染 -->
        <span>{{msg}}</span>
        
        <!-- v-bind:value表示将value属性交给Vue来进行管理，也就是绑定到Vue对象 -->
        <!-- v-bind:value表示绑定value属性 , v-bind可以省略，也就是 :value -->
        <input type="text" v-bind:value="name"/>
        <input type="button" :value="name"/>

        <!--
            v-model: 双向绑定, 和v-bind一样, input框会显示msg的内容, 由于是双向绑定, 改变输入框内容, msg内容也会改变
            :value可以省略, 直接写为v-model=
            trim可以去除首尾空格
         -->
        <input type="text" v-model:value="msg">
        <input type="text" v-model="msg">
        <input type="text" v-model.trim="msg">
    </div>
</body>
</html>
```





### 2、条件渲染

> 根据数据属性的值来判断是否对HTML页面内容进行渲染

`v-if`和`v-else`

`v-show`：通过控制display属性来控制这个div是否显示

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" src="script/vue.js"></script>
    <script type="text/javascript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    num:"1"
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <input type="text" v-model:value="num">
        <!-- v-if和v-else中间不能有其他节点 -->
        <div v-if="num%2==0" style="width:200px;height:200px;background-color:blueviolet;">&nbsp;</div>
       <div v-else="num%2==0" style="width:200px;height:200px;background-color:coral;">&nbsp;</div>

        <!-- v-show是通过控制display属性来控制这个div是否显示 -->
        <div v-show="num%2==0" style="width:200px;height:200px;background-color:coral;">&nbsp;</div>
    </div>
</body>
</html>
```







### 3、列表渲染

`v-for`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script language="JavaScript" src="script/vue.js"></script>
    <script language="JavaScript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    fruitList:[
                        {fname:"苹果",price:5,fcount:100,remark:"苹果很好吃"},
                        {fname:"菠萝",price:3,fcount:120,remark:"菠萝很好吃"},
                        {fname:"香蕉",price:4,fcount:50,remark:"香蕉很好吃"},
                        {fname:"西瓜",price:6,fcount:100,remark:"西瓜很好吃"}
                    ]
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <table border="1" width="400" cellpadding="4" cellspacing="0">
            <tr>
                <th>名称</th>
                <th>单价</th>
                <th>库存</th>
                <th>备注</th>
            </tr>
            <!-- v-for表示迭代 -->
            <tr align="center" v-for="fruit in fruitList">
                <td>{{fruit.fname}}</td>
                <td>{{fruit.price}}</td>
                <td>{{fruit.fcount}}</td>
                <td>{{fruit.remark}}</td>
            </tr>
        </table>
    </div>
</body>
</html>
```





### 4、事件驱动

设置单击事件`v-on:事件类型`

注意：`v-on:click="myReverse"`  ，`js`函数不能加`()`，但是如果需要携带参数的话，就要加上`()`并带上参数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script language="JavaScript" src="script/vue.js"></script>
    <script language="JavaScript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    msg:"hello world!"
                },
                methods:{
                    myReverse:function(){
                        this.msg = this.msg.split("").reverse().join("")
                    }
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <span>{{msg}}</span><br/>
        <!-- v-on:click 表示绑定点击事件 -->
        <!-- v-on可以省略，变成 @click -->
        <input type="button" value="反转" v-on:click="myReverse"/>
        <input type="button" value="反转" @click="myReverse"/>
    </div>
</body>
</html>
```





### 5、侦听属性

> 所谓**『侦听』**就是对message属性进行监控，当监控的属性的值发生变化时，调用我们准备好的函数。



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script language="JavaScript" src="script/vue.js"></script>
    <script language="JavaScript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    num1:1,
                    num2:2,
                    num3:0
                },
                watch:{
                    //侦听属性num1和num2
                    //当num1的值有改动时，那么需要调用后面定义的方法 , newValue指的是num1的新值
                    num1:function (newNum){
                        this.num3 = parseInt(newNum) + parseInt(this.num2);
                    },
                    num2:function (newNum){
                        this.num3 = parseInt(newNum) + parseInt(this.num1);
                    }
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <input type="text" v-model="num1" size="2"/>
        +
        <input type="text" v-model="num2" size="2"/>
        =
        <span>{{num3}}</span>
    </div>
</body>
</html>
```







### 5、Vue对象生命周期

`Vue`对象的生命周期分为8个，分别是**`beforeCreate`**对象创建前，**`created`**对象创建后，**`beforeMount`**数据装载之前，**`mounted`**数据装载之后，**`beforeUpdate`**数据更新之前，**`updated`**数据更新之后，**`beforeDestroy`**对象销毁之前，`destroyed`对象销毁之后

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script language="JavaScript" src="script/vue.js"></script>
    <script language="JavaScript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    msg:1
                },
                methods:{
                    changeMsg:function(){
                        this.msg = this.msg + 1 ;
                    }

                },
                /*vue对象创建之前*/
                beforeCreate:function(){
                    console.log("beforeCreate:vue对象创建之前---------------");
                    console.log("msg:"+this.msg);
                },
                /*vue对象创建之后*/
                created:function(){
                    console.log("created:vue对象创建之后---------------");
                    console.log("msg:"+this.msg);
                },
                /*数据装载之前*/
                beforeMount:function(){
                    console.log("beforeMount:数据装载之前---------------");
                    console.log("span:"+document.getElementById("span").innerText);
                },
                /*数据装载之后*/
                mounted:function(){
                    console.log("mounted:数据装载之后---------------");
                    console.log("span:"+document.getElementById("span").innerText);
                },
                beforeUpdate:function(){
                    console.log("beforeUpdate:数据更新之前---------------");
                    console.log("msg:"+this.msg);
                    console.log("span:"+document.getElementById("span").innerText);
                },
                updated:function(){
                    console.log("Updated:数据更新之后---------------");
                    console.log("msg:"+this.msg);
                    console.log("span:"+document.getElementById("span").innerText);
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <span id="span">{{msg}}</span><br/>
        <input type="button" value="改变msg的值" @click="changeMsg"/>
    </div>
</body>
</html>
```



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306211743338.png)









## 十六、Axios

> `Axios`就是将`Ajax`进行了封装



### 客户端发送普通参数

1、前端页面导入相应的js环境

```html
<script type="text/javascript" src="/demo/static/vue.js"></script>
<script type="text/javascript" src="/demo/static/axios.min.js"></script>
```



2、发送普通请求参数

> 通过设置单击事件，单击按钮，触发axios01这个js函数，该函数内部发送一个`axios`请求，其中`method`是请求方式，`url`是请求的地址，`params`是请求的参数，可以看到所有请求参数都被放到URL地址后面了，尽管现在用的是POST请求方式。`.then`后是请求发送成功时执行的回调函数，`.catch`是请求发送失败时执行的回调函数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>客户端发送普通参数给服务端</title>
    <script type="text/javascript" src="script/vue.js"></script>
    <script type="text/javascript" src="script/axios.min.js"></script>
    <script type="text/javascript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    uname:"xrj",
                    pwd:"123"
                },
                methods:{
                    axios01:function (){
                        axios({
                            "method":"POST",
                            "url":"axios.do",
                            "params":{
                                "uname":vue.uname,
                                "pwd":vue.pwd
                            }
                        })
                        .then(function (value){ // 成功时执行的回调
                            console.log(value);
                        })
                        .catch(function(reason){ // 有异常时执行的回调
                            console.log(reason);
                        });
                    }
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <span>{{uname}}</span><br>

        uname: <input type="text" v-model:value="uname"><br>
        pwd: <input type="text" v-model:value="pwd"><br>

        <input type="button" value="发送普通参数的异步请求" @click="axios01"/>
    </div>
</body>
</html>
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306212207554.png)



3、后端代码

> 服务端收到请求后，给客户端返回数据，通过`response.getWriter();`进行返回

```java
package com.xrj.comtroller;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/axios.do")
public class AxiosController extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        String uname = request.getParameter("uname");
        String pwd = request.getParameter("pwd");
        System.out.println(uname);
        System.out.println(pwd);

        response.setCharacterEncoding("utf-8");
        response.setContentType("text/html:charset:UTF-8");
        PrintWriter writer = response.getWriter();
        writer.write(uname + "_" + pwd);

        // throw new NullPointerException("空指针");
    }
}
```



> 这是axios程序在正常执行时收到的数据【执行`then(function (value)`】，可以看到其中`request`请求的对象是`XMLHttpRequest`，且`readyState`为4，说明`Axios`底层封装了`Ajax`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306221556014.png)



> 如果发生异常，那么`axios`会执行`.catch(function(reason)`，这里`reason`包含的数据格式和正常执行时`value`中的是一样的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202306221555171.png)

| 属性名     | 作用                                             |
| ---------- | ------------------------------------------------ |
| config     | 调用axios(config对象)方法时传入的JSON对象        |
| data       | 服务器端返回的响应体数据                         |
| headers    | 响应消息头                                       |
| request    | 原生JavaScript执行Ajax操作时使用的XMLHttpRequest |
| status     | 响应状态码                                       |
| statusText | 响应状态码的说明文本                             |









### 客户端发送json格式数据

**前端代码**

> 在`axios`中不再使用`params`发送参数，而是使用`data`携带`json`格式的数据，而且使用`json`格式数据时，参数不会在地址栏显示了
>
> 这里在收到服务端传来的`json`字符串后，会将其解析为`json`对象，可以直接使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>客户端发送json格式数据给服务端</title>
    <script type="text/javascript" src="script/vue.js"></script>
    <script type="text/javascript" src="script/axios.min.js"></script>
    <script type="text/javascript">
        window.onload=function(){
            var vue = new Vue({
                "el":"#div0",
                data:{
                    uname:"xrj",
                    pwd:"123"
                },
                methods:{
                    axios01:function (){
                        axios({
                            "method":"POST",
                            "url":"axios02.do",
                            data:{
                                "uname":vue.uname,
                                "pwd":vue.pwd
                            }
                        })
                        .then(function (value){ // 成功时执行的回调
                            var data = value.data
                            // data对应的数据{uname: '张三', pwd: 'xrj123'}
                            // 因为服务端指定了发回的数据类型为json，所以这里将发回的字符串解析成了json对象，可以直接data.uname访问属性
                            console.log(data.uname + "_" + data.pwd);
                        })
                        .catch(function(reason){ // 有异常时执行的回调
                            console.log(reason);
                        });
                    }
                }
            });
        }
    </script>
</head>
<body>
    <div id="div0">
        <span>{{uname}}</span><br>

        uname: <input type="text" v-model:value="uname"><br>
        pwd: <input type="text" v-model:value="pwd"><br>

        <input type="button" value="发送json格式数据的异步请求" @click="axios01"/>
    </div>
</body>
</html>
```



**服务端代码**

> 服务端接收普通参数可以直接使用`request.getParameter();` 而接收`json`格式数据需要使用输入流才可以读取到，即`request.getReader()`。
>
> 将读取到的数据放入一个`StringBuilder`中，然后在转为字符串，字符串也是`json`格式的。如果想把`json`格式的字符串转为`java`对象，可以使用`Gson`包，导入`Gson`的jar包后，他的`fromJson(string, T)`方法可以将`json`格式的字符串转为`java`对象，`toJson(java Object)`方法可以将`java`对象转为`json`字符串。
>
> 如果需要将数据发回客户端，需要发送字符串，如果发送的是`json`格式的字符串，`response.setContentType("application/json;charset=utf-8");`指定数据格式后，`axios`的回调函数接收到后，会自动将`json`字符串转为`json`对象，进一步操作。

```java
package com.xrj.controller;

import com.google.gson.Gson;
import com.xrj.pojo.User;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/axios02.do")
public class Axios02 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // json格式的数据需要使用输入流来读
        BufferedReader reader = request.getReader();
        // 用一个StringBuilder来接收数据
        StringBuilder sb = new StringBuilder();
        String str = null;
        while ((str = reader.readLine()) != null) {
            sb.append(str);
        }
        str = sb.toString();
        System.out.println(str); // {"uname":"xrj","pwd":"123"}

        Gson gson = new Gson();
        //1.fromJson(string,T) 将字符串转化成java object
        //2.toJson(java Object) 将java object转化成json字符串，这样才能响应给客户端

        // 将str转为user对象
        User user = gson.fromJson(str, User.class);
        System.out.println(user);
        user.setUname("张三");
        user.setPwd("xrj123");

        // 将user对象转为json字符串
        String userStr = gson.toJson(user);
        System.out.println(userStr);

        // 将字符串发回给客户端
        response.setCharacterEncoding("utf-8");
        // 表示发回的数据类型是json格式
        response.setContentType("application/json;charset=utf-8");
        PrintWriter writer = response.getWriter();
        writer.write(userStr);
    }
}
```





### js中String和Json转化

需要注意的是：服务端可以通过`Gson`将`json`对象和`json`字符串相互转化。在`js`中，可以使用`JSON.stringify(object) `将`js`对象转为`json`字符串，`JSON.parse(string) `将`json`字符串转为`json`对象

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js中String和json转化</title>
    <script type="text/javascript">
        function Demo01(){
            // 1.js String -> js Json
            // var str = "{\"uname\":\"lina\",\"age\":20}";
            // var user = JSON.parse(str);
            // alert(typeof user)
            // alert(user.uname + "_" + user.age)

            // 2. js Json -> js String
            var user = {"uname":"xrj", "age":20};
            alert(typeof user);
            var userStr = JSON.stringify(user)
            alert(typeof userStr);
            alert(userStr);
        }
    </script>
</head>
<body>
    <div id="div0">
        <input type="button" value="确定" onclick="Demo01()"/>
    </div>
</body>
</html>
```







