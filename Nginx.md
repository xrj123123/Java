## 一、Nginx介绍

### 1、背景介绍

Nginx（“engine x”）一个具有高性能的【HTTP】和【反向代理】的【WEB服务器】，同时也是一个【POP3/SMTP/IMAP代理服务器】，是由伊戈尔·赛索耶夫(俄罗斯人)使用C语言编写的，Nginx的第一个版本是2004年10月4号发布的0.1.0版本。伊戈尔·赛索耶夫将Nginx的源码进行了开源，这也为Nginx的发展提供了良好的保障。



**1、WEB服务器**：WEB服务器也叫网页服务器，英文名叫Web Server，主要功能是为用户提供网上信息浏览服务。

**2、HTTP**：HTTP是超文本传输协议的缩写，是用于从WEB服务器传输超文本到本地浏览器的传输协议，也是互联网上应用最为广泛的一种网络协议。HTTP是一个客户端和服务器端请求和应答的标准，客户端是终端用户，服务端是网站，通过使用Web浏览器、网络爬虫或者其他工具，客户端发起一个到服务器上指定端口的HTTP请求。

**3、POP3/SMTP/IMAP**：

- POP3(Post Offic Protocol 3)邮局协议的第三个版本，
- SMTP(Simple Mail Transfer Protocol)简单邮件传输协议，
- IMAP(Internet Mail Access Protocol)交互式邮件存取协议，

**4、正向代理**

我们知道需要访问的目标的，但是访问过程中出现了一个代理去帮我们完成这个请求并将结果返回。 此时服务器只知道请求来自哪个代理服务器，而不知道是哪个客户端发起的请求，**正向代理模式屏蔽或者隐藏了真实客户端的信息**。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411051143056.png)

**5、反向代理**

用户对代理是无感知的，我们只需要将请求发送至反向代理服务器，由反向代理服务器帮我们决定需要访问哪个资源并且将资源返回给我们。此时反向代理服务器和目标服务器对外就是服务器，但是只对外暴露了代理服务器的地址，**目标服务器的地址是隐藏的**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411051143855.png)



### 2、Nginx优点

**1、速度更快、并发更高**

单次请求或者高并发请求的环境下，Nginx都会比其他Web服务器响应的速度更快。一方面在正常情况下，单次请求会得到更快的响应，另一方面，在高峰期，Nginx比其他Web服务器更快的响应请求。Nginx之所以有这么高的并发处理能力和这么好的性能原因在于Nginx采用了**多进程**和**I/O多路复用(epoll)**的底层实现。

**2、配置简单，扩展性强**

Nginx的设计极具扩展性，它本身就是由很多模块组成，这些模块的使用可以通过配置文件的配置来添加。这些模块有官方提供的也有第三方提供的模块，如果需要完全可以开发服务自己业务特性的定制模块。

**3、高可靠性**

Nginx采用的是多进程模式运行，其中有一个master主进程和N多个worker进程，worker进程的数量我们可以手动设置，每个worker进程之间都是相互独立提供服务，并且master主进程可以在某一个worker进程出错时，快速去"拉起"新的worker进程提供服务。

**4、热部署**

现在互联网项目都要求以7*24小时进行服务的提供，针对于这一要求，Nginx也提供了热部署功能，即可以在Nginx不停止的情况下，对Nginx进行文件升级、更新配置和更换日志文件等功能。

**5、成本低、BSD许可证**

BSD是一个开源的许可证，世界上的开源许可证有很多，现在比较流行的有六种分别是GPL、BSD、MIT、Mozilla、Apache、LGPL。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411051148329.png" style="zoom:50%;" />

Nginx本身是开源的，不仅可以免费的将Nginx应用在商业领域，而且还可以在项目中直接修改Nginx的源码来定制自己的特殊要求。OpenRestry [Nginx+Lua]   Tengine[淘宝]



### 3、Nginx特性及功能

#### 3.1 基本HTTP服务

Nginx可以提供基本HTTP服务，可以作为HTTP代理服务器和反向代理服务器，支持通过缓存加速访问，可以完成简单的负载均衡和容错，支持包过滤功能，支持SSL等。

- 处理静态文件、处理索引文件以及支持自动索引
- 提供反向代理服务器，并可以使用缓存加上反向代理，同时完成负载均衡和容错
- 提供对FastCGI、memcached等服务的缓存机制，同时完成负载均衡和容错
- 使用Nginx的模块化特性提供过滤器功能。Nginx基本过滤器包括gzip压缩、ranges支持、chunked响应、XSLT、SSI以及图像缩放等。其中针对包含多个SSI的页面，经由FastCGI或反向代理，SSI过滤器可以并行处理
- 支持HTTP下的安全套接层安全协议SSL
- 支持基于加权和依赖的优先权的HTTP/2



#### 3.2 高级HTTP服务

- 支持基于名字和IP的虚拟主机设置
- 支持HTTP/1.0中的KEEP-Alive模式和管线(PipeLined)模型连接
- 自定义访问日志格式、带缓存的日志写操作以及快速日志轮转
- 提供3xx~5xx错误代码重定向功能
- 支持重写（Rewrite)模块扩展
- 支持重新加载配置以及在线升级时无需中断正在处理的请求
- 支持网络监控
- 支持FLV和MP4流媒体传输



#### 3.3 邮件服务

Nginx提供邮件代理服务也是其基本开发需求之一，主要包含以下特性：

- 支持IMPA/POP3代理服务功能
- 支持内部SMTP代理服务功能



#### 3.4 Nginx常用的功能模块

```
静态资源部署
Rewrite地址重写
	正则表达式
反向代理
负载均衡
	轮询、加权轮询、ip_hash、url_hash、fair
Web缓存
环境部署
	高可用的环境
用户认证模块...
```

Nginx的核心组成

```
nginx二进制可执行文件
nginx.conf配置文件
error.log错误的日志记录
access.log访问日志记录
```



### 4、Nginx目录结构

**conf:nginx**

- `mime.types`：记录的是HTTP协议中的Content-Type的值和文件后缀名的对应关系
- `mime.types.default`：mime.types的备份文件
- `nginx.conf`：这个是Nginx的核心配置文件，这个文件非常重要，也是我们即将要学习的重点
- `nginx.conf.default`：nginx.conf的备份文件

**htm**

- `50x.html`：访问失败后的失败页面
- `index.html`：成功访问的默认首页

**logs**

- `access.log `：访问日志
- `error.log`：错误日志
- `nginx.pid`：nginx的PID

**sbin**

- `nginx`：控制Nginx的启动和停止



### 5、Nginx服务器命令

#### 4.1 Nginx服务的信号控制

Nginx默认采用的是多进程的方式来工作的，当将Nginx启动后，通过`ps -ef | grep nginx`命令可以查看到如下内容

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411072350538.png)

Nginx后台进程中包含**一个master进程和多个worker进程**

- master进程主要用来管理worker进程，包含接收外界的信息，并将接收到的信号发送给各个worker进程，监控worker进程的状态，当worker进程出现异常退出后，会自动重新启动新的worker进程
- worker进程是专门用来处理用户请求的，各个worker进程之间是平等的并且相互独立，处理请求的机会也是一样的

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411072352165.png" style="zoom:80%;" />

我们现在作为管理员，只需要通过**给master进程发送信号**就可以来控制Nginx

（1）获取master进程ID

- 通过`ps -ef | grep nginx`；

- `/usr/local/nginx/logs/nginx.pid`，`nginx.pid`文件中可以查看master进程ID

（2）信号：调用命令为`kill -signal PID`，`signal`为信号，`PID`为获取到的master进程ID

| 信号       | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| `TERM/INT` | 立即关闭整个服务                                             |
| `QUIT`     | "优雅"地关闭整个服务，不再接收新请求，当前请求处理完毕后关闭 |
| `HUP`      | 修改配置文件后，无需重启Nginx，可以使其直接使用新的配置文件，会重新创建worker进程 |
| `USR1`     | 重新打开日志文件，可以用来进行日志切割                       |
| `USR2`     | 平滑升级到最新版的nginx                                      |
| `WINCH`    | 所有子进程不在接收处理新连接，相当于给work进程发送QUIT指令   |

1. 发送`TERM/INT`信号给master进程，会将Nginx服务立即关闭

```sh
kill -TERM 36682 
kill -TERM `cat /usr/local/nginx/logs/nginx.pid`
kill -INT 36682 
kill -INT `cat /usr/local/nginx/logs/nginx.pid`
```

2. 发送`QUIT`信号给master进程，master进程会控制所有的work进程不再接收新的请求，等所有请求处理完后，关闭进程

```sh
kill -QUIT 36682 
```

3. 发送`HUP`信号给master进程，master进程会控制旧的work进程不再接收新的请求，等处理完请求后将旧的work进程关闭掉，然后根据nginx的配置文件重新启动新的work进程

```sh
kill -HUP 36682 
```

4. 发送`USR1`信号给master进程，告诉Nginx重新开启日志文件

```sh
kill -USR1 36682 
```

5. 发送`USR2`信号给master进程，告诉master进程要平滑升级，这时会创建新的master进程和work进程，整个系统中将会有两个master进程，并且新的master进程的PID记录在`/usr/local/nginx/logs/nginx.pid`，旧的master进程PID会记录在`/usr/local/nginx/logs/nginx.pid.oldbin`中，升级完成后，发送`QUIT`信号给旧的master进程，让其处理完请求后再进行关闭

```sh
kill -USR2 36682 
kill -QUIT 36682 

kill -USR2 `cat /usr/local/nginx/logs/nginx.pid`
kill -QUIT `cat /usr/local/nginx/logs/nginx.pid.oldbin`
```

6. 发送`WINCH`信号给master进程，让master进程控制所有的work进程不再接收新的请求，请求处理完后关闭work进程。master进程不会被关闭掉

```sh
kill -WINCH 36682 
kill -WINCH`cat /usr/local/nginx/logs/nginx.pid`
```



#### 4.2 Nginx的命令行控制

这种方式是通过Nginx安装目录下的`sbin`下的可执行文件`nginx`来进行Nginx状态的控制，我们可以通过`nginx -h`来查看都有哪些参数可以使用

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411080008112.png" style="zoom:80%;" />

- `-?`和`-h`：显示帮助信息

- `-v`：打印版本号信息并退出
- `-V`：打印版本号信息和配置信息并退出
- `-t`：测试nginx的配置文件语法是否正确并退出
- `-T`：测试nginx的配置文件语法是否正确并列出用到的配置文件信息然后退出
- `-q`：在使用`-t`测试期间只显示错误信息
- `-s : signal`信号，后面可以跟 
  - `stop`：快速关闭，类似于`TERM/INT`信号的作用
  - `quit`：优雅的关闭，类似于`QUIT`信号的作用]
  - `reopen`：重新打开日志文件，类似于`USR1`信号的作用]
  - `reload`：类似于`HUP`信号的作用

- `-p : prefix`，指定Nginx的安装路径，(默认为: /usr/local/nginx/)

- `-c : filename`，指定Nginx的配置文件路径，(默认为: conf/nginx.conf)

- `-g`：用来补充Nginx配置文件，向Nginx服务指定启动时应用全局的配置



### 6、Nginx版本升级和新增模块

> 如果想对Nginx的版本进行更新，或者要应用一些新的模块，最简单的做法就是停止当前的Nginx服务，然后开启新的Nginx服务。但是这样会导致在一段时间内，用户是无法访问服务器。为了解决这个问题，就需要用到Nginx服务器提供的平滑升级功能。使用这种方式，可以使Nginx在7*24小时不间断的提供服务了。
>

```
需求：Nginx的版本最开始使用的是Nginx-1.14.2,由于服务升级，需要将Nginx的版本升级到Nginx-1.16.1,要求Nginx不能中断提供服务。
```



#### 6.1 环境准备

（1）先准备两个版本的Nginx分别是 1.14.2和1.16.1

（2）使用Nginx源码安装的方式将1.14.2版本安装成功并正确访问

```
进入安装目录
./configure
make && make install
```

（3）将Nginx1.16.1进行参数配置和编译，不需要进行安装

```
进入安装目录
./configure
make 
```



#### 6.1 使用Nginx服务信号进行升级

第一步:将1.14.2版本的sbin目录下的nginx进行备份

```
cd /usr/local/nginx/sbin
mv nginx nginxold
```

第二步:将Nginx1.16.1安装目录编译后的objs目录下的nginx文件，拷贝到原来`/usr/local/nginx/sbin`目录下

```
cd ~/nginx/core/nginx-1.16.1/objs
cp nginx /usr/local/nginx/sbin
```

第三步:发送信号`USR2`给Nginx的1.14.2版本对应的master进程

第四步:发送信号`QUIT`给Nginx的1.14.2版本对应的master进程

```
kill -QUIT `more /usr/local/logs/nginx.pid.oldbin`
```



#### 6.3 使用Nginx安装目录的make命令完成升级

第一步:将1.14.2版本的sbin目录下的nginx进行备份

```
cd /usr/local/nginx/sbin
mv nginx nginxold
```

第二步:将Nginx1.16.1安装目录编译后的objs目录下的nginx文件，拷贝到原来`/usr/local/nginx/sbin`目录下

```
cd ~/nginx/core/nginx-1.16.1/objs
cp nginx /usr/local/nginx/sbin
```

第三步:进入新版本nginx的安装目录，执行`make upgrade`

> `make upgrade`命令本质上还是执行了`-USR2`和`-QUIT`命令

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411080035622.png)





## 二、Nginx核心配置文件

Nginx的核心配置文件默认是放在`/usr/local/nginx/conf/nginx.conf`

- `nginx.conf`配置文件中默认有三大块：全局块、events块、http块

- http块中可以配置多个server块，每个server块可以配置多个location块

```json
# 全局块，主要设置Nginx服务器整体运行的配置指令
worker_processes  1;  

# events块,主要设置Nginx服务器与用户的网络连接,这一部分对Nginx服务器的性能影响较大
events {
    worker_connections  1024;
}

#http块，是Nginx服务器配置中的重要部分，代理、缓存、日志记录、第三方模块配置
http {
    # 指令名	指令值;
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    # server块，是Nginx配置和虚拟主机相关的内容
    server {
    	# 指令名	指令值;
        listen       80; # 监听的端口
        server_name  localhost; # 服务器域名
        location / {  # 访问路径为"/"就定位到这个location下
            root   html; # 在html目录下
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html; # 出现500,502,503,504错误就访问/50.html
        location = /50x.html {
            root   html;
        }
    }

}
```



### 1、全局块

#### 1.1 user

用于配置运行Nginx服务器的worker进程的用户和用户组。使用`user`指令可以指定启动运行工作进程的用户及用户组，这样对于系统的权限访问控制的更加精细，也更加安全。

| 语法   | user 用户 [用户组] |
| ------ | ------------------ |
| 默认值 | nobody             |
| 位置   | 全局块             |

> 该属性也可以在编译的时候指定，`./configure --user=user --group=group`,如果两个地方都进行了设置，最终生效的是配置文件中的配置。
>



**使用步骤**

(1) 创建一个用户

```
useradd www
```

(2) 修改user属性

```
user www;
```

(3) 创建`/root/html/index.html`页面，添加如下内容

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
<p><em>I am WWW</em></p>
</body>
</html>
```

(4) 修改`nginx.conf`

```
location / {
	root   /root/html;
	index  index.html index.htm;
}
```

(5) 测试启动访问，页面会报403拒绝访问的错误

(6) 原因：当前用户没有访问/root/html目录的权限

(7) 将文件创建到 `/home/www/html/index.html`，修改配置

```
location / {
	root   /home/www/html;
	index  index.html index.htm;
}
```

(8) 再次测试启动访问，正常访问。



#### 1.2 work_process

`master_process`：用来指定是否开启工作进程。

| 语法   | master_process on\|off; |
| ------ | ----------------------- |
| 默认值 | master_process on;      |
| 位置   | 全局块                  |

`worker_processes`：配置Nginx生成工作进程的数量，这是Nginx服务器实现并发处理服务的关键所在。理论上`worker_process`的值越大，可以支持的并发处理量也越多，但这个值的设定是需要受到来自服务器自身的限制，建议将该值和服务器CPU的内核数保存一致。

| 语法   | worker_processes  num/auto; |
| ------ | --------------------------- |
| 默认值 | 1                           |
| 位置   | 全局块                      |



#### 1.3 其他指令

`daemon`：设定Nginx是否以守护进程的方式启动。

> 守护式进程是linux后台执行的一种服务进程，特点是独立于控制终端，不会随着终端关闭而停止。
>

| 语法   | daemon on\|off; |
| ------ | --------------- |
| 默认值 | daemon on;      |
| 位置   | 全局块          |



`pid`：用来配置Nginx当前master进程的进程号ID存储的文件路径。该属性也可以通过`./configure --pid-path=PATH`来指定

| 语法   | pid file;                         |
| ------ | --------------------------------- |
| 默认值 | `/usr/local/nginx/logs/nginx.pid` |
| 位置   | 全局块                            |



`error_log`：用来配置Nginx的错误日志存放路径。该属性也可以通过`./configure --error-log-path=PATH`来指定

| 语法   | error_log  file [日志级别];       |
| ------ | --------------------------------- |
| 默认值 | `error_log logs/error.log error;` |
| 位置   | 全局块、http、server、location    |

> 其中日志级别有：`debug|info|notice|warn|error|crit|alert|emerg`，分别为|信息|通知|警告|错误|临界|警报|紧急，建议设置的时候不要设置成info以下的等级，因为会带来大量的磁盘I/O消耗，影响Nginx的性能。
>



`include`：用来引入其他配置文件，使Nginx的配置更加灵活

| 语法   | include file; |
| ------ | ------------- |
| 默认值 | 无            |
| 位置   | any           |



### 2、events块

#### 2.1 accept_mutex

用来设置Nginx网络连接序列化

| 语法   | accept_mutex on\|off; |
| ------ | --------------------- |
| 默认值 | `accept_mutex on;`    |
| 位置   | events                |

> 这个配置主要用来解决"惊群"问题。在某一个时刻，客户端发来一个请求连接，Nginx后台是以多进程的工作模式，有多个worker进程会被同时唤醒，但是最终只会有一个进程可以获取到连接，如果每次唤醒的进程数目太多，就会影响Nginx的整体性能。如果将上述值设置为on(开启状态)，将会对多个Nginx进程接收连接进行序列号，一个个来唤醒接收，就防止了多个进程对连接的争抢。



#### 2.2 multi_accept

是否允许同时接收多个网络连接

| 语法   | multi_accept on\|off; |
| ------ | --------------------- |
| 默认值 | `multi_accept off;`   |
| 位置   | events                |

如果`multi_accept`被禁止了，nginx一个工作进程只能同时接受一个新的连接。否则，一个工作进程可以同时接受所有的新连接



####  2.3 worker_connections

单个worker进程最大的连接数。这里的连接数不仅仅包括和前端用户建立的连接数，而是包括所有可能的连接数。同时，number值不能大于操作系统支持打开的最大文件句柄数量。

| 语法   | worker_connections number; |
| ------ | -------------------------- |
| 默认值 | `worker_commections 512;`  |
| 位置   | events                     |



#### 2.4 use

设置Nginx服务器选择哪种事件驱动来处理网络消息。所选择事件处理模型是Nginx优化部分的一个重要内容，method的可选值有`select/poll/epoll/kqueue`等

这些值的选择也可以在编译的时候使用`--with-select_module`、`--without-select_module`、` --with-poll_module`、` --without-poll_module`来设置

| 语法   | use  method;   |
| ------ | -------------- |
| 默认值 | 根据操作系统定 |
| 位置   | events         |



### 3、http块

#### 3.1 定义MIME-Type

> 浏览器中可以显示的内容有HTML、XML、GIF等种类繁多的文件、媒体等资源，浏览器为了区分这些资源，需要使用MIME Type，MIME Type是网络资源的媒体类型。Nginx作为web服务器，也需要能够识别前端请求的资源类型。
>

在Nginx的配置文件中，默认有两行配置

```
include mime.types;
default_type application/octet-stream;
```

- `include mime.types`，把`mime.types`文件中MIMT类型与相关类型文件的文件后缀名的对应关系加入到当前的配置文件中

- `default_type`：用来配置Nginx响应前端请求默认的MIME类型

| 语法   | default_type mime-type;    |
| ------ | -------------------------- |
| 默认值 | `default_type text/plain;` |
| 位置   | http、server、location     |



请求某些接口的时候需要返回指定的文本字符串或者json字符串，如果逻辑非常简单或者干脆是固定的字符串，那么可以使用nginx快速实现，就不用编写程序响应请求了，可以减少服务器资源占用并且响应性能非常快

```
location /get_text {
	#这里也可以设置成text/plain
    default_type text/html;
    return 200 "This is nginx's text";
}
location /get_json{
    default_type application/json;
    return 200 '{"name":"TOM","age":18}';
}
```



#### 3.2 自定义服务日志

Nginx中日志的类型分`access.log`、`error.log`

- `access.log`：用来记录用户所有的访问请求。

- `error.log`：记录nginx本身运行时的错误信息，不会记录用户的访问请求。



Nginx服务器支持对服务日志的格式、大小、输出等进行设置，需要使用`access_log`和`log_format`指令

- `access_log`：用来设置用户访问日志的相关属性。

| 语法   | access_log path[format[buffer=size]]   |
| ------ | -------------------------------------- |
| 默认值 | `access_log logs/access.log combined;` |
| 位置   | `http`, `server`, `location`           |

- `log_format`用来指定日志的输出格式。

| 语法   | log_format name [escape=default\|json\|none] string....; |
| ------ | -------------------------------------------------------- |
| 默认值 | `log_format combined "...";`                             |
| 位置   | http                                                     |



#### 3.3 其他配置指令

- `sendfile`：设置Nginx服务器是否使用`sendfile()`零拷贝的方式传输文件，该属性可以大大提高Nginx处理静态资源的性能

| 语法   | sendfile on\|off；     |
| ------ | ---------------------- |
| 默认值 | `sendfile off;`        |
| 位置   | http、server、location |

- `keepalive_timeout`：设置长连接的超时时间

| 语法   | keepalive_timeout time; |
| ------ | ----------------------- |
| 默认值 | `keepalive_timeout 75;` |
| 位置   | http、server、location  |

- `keepalive_requests`：设置一个keep-alive连接使用的次数

| 语法   | keepalive_requests number; |
| ------ | -------------------------- |
| 默认值 | `keepalive_requests 100;`  |
| 位置   | http、server、location     |





## 三、Nginx静态资源部署

通过浏览器发送一个HTTP请求实现从客户端发送请求到服务器端，获取所需要内容后并把内容回显展示在页面。这个时候，我们所请求的内容就分为两种类型

- **静态资源**：在服务器端真实存在并且能直接拿来展示的一些文件，比如常见的html页面、css文件、js文件、图 片、视频等资源
- **动态资源**：在服务器端真实存在但是要想获取需要经过一定的业务逻辑处理，根据不同的条件展示在页面不同这 一部分内容，比如报表数据展示、根据当前登录用户展示相关具体数据等资源



### 1、静态资源的配置指令

#### 1.1 listen

`listen`：用来配置监听端口

| 语法   | listen address[:port] [default_server]...;<br/>listen port [default_server]...; |
| ------ | ------------------------------------------------------------ |
| 默认值 | `listen *:80 \| *:8000`                                      |
| 位置   | server                                                       |



**示例**

```
listen 127.0.0.1:8000; // listen localhost:8000 监听指定的IP和端口
listen 127.0.0.1;	监听指定IP的所有端口
listen 8000;	监听指定端口上的连接
listen *:8000;	监听指定端口上的连接
```



`default_server`属性是标识符，用来将此虚拟主机设置成默认主机。默认主机指的是如果没有匹配到对应的`address:port`，则会默认执行。如果不指定默认，那么使用的是第一个server

> 1、nginx的server配置如下，此时客户端访问`192.168.123.23:8080`【nginx的主机ip】，匹配不到任何一个server。如果没有`default_server`，此时就会访问第一个server。如果有`default_server`，那么就会访问这个默认的server。
>
> 2、nginx监听8080端口。无论请求来自哪个IP地址，只要目标端口是8080【访问的ip或域名必须是nginx主机ip】，nginx都会接收到这个请求。因此客户端访问8080端口，即使`server_name`不匹配，也会被nginx监听到。但如果访问的不是80端口，nginx就收不到该请求

```nginx
server{
	listen 8080;
	server_name 127.0.0.1;
	location /{
		root html;
		index index.html;
	}
}
server{
	listen 8080 default_server;
	server_name localhost;
	default_type text/plain;
	return 444 'This is a error request';
}
```



#### 1.2 server_name

用来设置虚拟主机服务名称。例如：`127.0.0.1` 、` localhost `、域名`www.baidu.com `、`www.jd.com`

| 语法   | server_name  name ...;<br/>name可以提供多个中间用空格分隔 |
| ------ | --------------------------------------------------------- |
| 默认值 | `server_name  "";`                                        |
| 位置   | server                                                    |



##### 1.2.1 精确匹配

```nginx
server {
	listen 80;
	server_name www.itcast.cn www.itheima.cn;
	...
}
```

> 域名要收取一定的费用，所以我们可以使用修改hosts文件来制作一些虚拟域名使用。修改 `/etc/hosts`文件，此时在本机访问`www.itcast.cn`或`www.itheima.cn`就会访问`127.0.0.1`，从而访问到nginx
>

```
vim /etc/hosts
127.0.0.1 www.itcast.cn
127.0.0.1 www.itheima.cn
```



##### 1.2.2 通配符配置

`server_name`中支持通配符`*`，但通配符不能出现在域名的中间，只能出现在首段或尾段

```nginx
server {
	listen 80;
	server_name  *.itcast.cn	www.itheima.*;
	# www.itcast.cn abc.itcast.cn www.itheima.cn www.itheima.com
	...
}
```

下面的配置就会报错

```nginx
server {
	listen 80;
	server_name  www.*.cn www.itheima.c*
	...
}
```



##### 1.2.3 正则表达式配置

`server_name`中可以使用正则表达式，并且使用`~`作为正则表达式字符串的开始标记。

| 代码  | 说明                                                       |
| ----- | ---------------------------------------------------------- |
| ^     | 匹配搜索字符串开始位置                                     |
| $     | 匹配搜索字符串结束位置                                     |
| .     | 匹配除换行符\n之外的任何单个字符                           |
| \     | 转义字符，将下一个字符标记为特殊字符                       |
| [xyz] | 字符集，与任意一个指定字符匹配                             |
| [a-z] | 字符范围，匹配指定范围内的任何字符                         |
| \w    | 与以下任意字符匹配 A-Z a-z 0-9 和下划线,等效于[A-Za-z0-9_] |
| \d    | 数字字符匹配，等效于[0-9]                                  |
| {n}   | 正好匹配n次                                                |
| {n,}  | 至少匹配n次                                                |
| {n,m} | 匹配至少n次至多m次                                         |
| *     | 零次或多次，等效于{0,}                                     |
| +     | 一次或多次，等效于{1,}                                     |
| ?     | 零次或一次，等效于{0,1}                                    |

```nginx
server{
        listen 80;
        server_name ~^www\.(\w+)\.com$;
        default_type text/plain;
        return 200 $1  $2 ..;
}
# 注意 ~后面不能加空格，加括号可以通过$取值
```



##### 1.2.4 匹配执行顺序

`server_name`支持通配符和正则表达式，在包含多个虚拟主机的配置文件中，可能会出现一个名称被多个虚拟主机的`server_name`匹配成功，匹配的顺序为

1. 准确匹配`server_name`

2. 通配符在开始时匹配`server_name`

3. 通配符在结束时匹配`server_name`

4. 正则表达式匹配`server_name`

5. 被默认的default_server处理，如果没有指定默认找第一个server



#### 1.3 location

用来设置请求的URI

| 语法   | location [  =  \|   ~  \|  ~*   \|   ^~   \|@ ] uri{...} |
| ------ | -------------------------------------------------------- |
| 默认值 | —                                                        |
| 位置   | server,location                                          |

uri变量是待匹配的请求字符串，可以不包含正则表达式，也可以包含正则表达式，那么nginx服务器在搜索匹配location的时候，是先使用不包含正则表达式进行匹配，找到一个匹配度最高的一个，然后在通过包含正则表达式的进行匹配，如果能匹配到直接访问，匹配不到，就使用刚才匹配度最高的那个location来处理请求。



**不带符号：**要求必须以指定模式开始

```nginx
server {
	listen 80;
	server_name 127.0.0.1;
	location /abc{
		default_type text/plain;
		return 200 "access success";
	}
}
以下访问都是正确的
http://192.168.200.133/abc
http://192.168.200.133/abc?p1=TOM
http://192.168.200.133/abc/
http://192.168.200.133/abcdef
```



**`=`**： 用于不包含正则表达式的uri前，必须与指定的模式精确匹配

```nginx
server {
	listen 80;
	server_name 127.0.0.1;
	location =/abc{
		default_type text/plain;
		return 200 "access success";
	}
}
可以匹配到
http://192.168.200.133/abc
http://192.168.200.133/abc?p1=TOM
匹配不到
http://192.168.200.133/abc/
http://192.168.200.133/abcdef
```



**`~ `：**用于表示当前uri中包含了正则表达式，并且区分大小写

**`~*`：**  用于表示当前uri中包含了正则表达式，并且不区分大小写

```nginx
server {
	listen 80;
	server_name 127.0.0.1;
	location ~^/abc\w${
		default_type text/plain;
		return 200 "access success";
	}
}
server {
	listen 80;
	server_name 127.0.0.1;
	location ~*^/abc\w${
		default_type text/plain;
		return 200 "access success";
	}
}
```



**`^~`：** 用于不包含正则表达式的uri前，功能和不加符号的一致，不同的是，如果模式匹配，那么就停止搜索其他模式了。而不加符号的在模式匹配后，会继续向后搜索

```nginx
server {
	listen 80;
	server_name 127.0.0.1;
	location ^~/abc{
		default_type text/plain;
		return 200 "access success";
	}
}
```



#### 1.4 root / alias

**root：**设置请求的根目录，path为Nginx服务器接收到请求以后查找资源的根目录路径。

| 语法   | root path;             |
| ------ | ---------------------- |
| 默认值 | `root html;`           |
| 位置   | http、server、location |



**alias：**用来更改location的URI，path为修改后的根路径。

| 语法   | alias path; |
| ------ | ----------- |
| 默认值 | —           |
| 位置   | location    |



**root和alias的区别**

（1）在`/usr/local/nginx/html`目录下创建一个 `images`目录，并在目录下放入一张图片`mv.png`图片

```nginx
location /images {
	root /usr/local/nginx/html;
}
```

访问图片的路径为:

```
http://192.168.200.133/images/mv.png
```

（2）如果把root改为alias

```nginx
location /images {
	alias /usr/local/nginx/html;
}
```

再次访问上述地址，页面会出现404的错误，查看错误日志会发现是因为地址不对

- root的处理结果：root路径+location路径，`/usr/local/nginx/html/images/mv.png`
- alias的处理结果：使用alias路径替换location路径，`/usr/local/nginx/html/images`

因此需要在alias后面路径改为

```nginx
location /images {
	alias /usr/local/nginx/html/images;
}
```

（3）如果location路径是以`/`结尾，则alias也必须是以`/`结尾，root没有要求

将上述配置修改为

```nginx
location /images/ {
	alias /usr/local/nginx/html/images/;
}
```



**总结**

- root的处理结果：root路径+location路径
- alias的处理结果：使用alias路径替换location路径
- alias是一个目录别名的定义，root则是最上层目录的含义。
- 如果location路径是以`/`结尾，则alias也必须是以`/`结尾，root没有要求



#### 1.5 index

设置网站的默认首页

| 语法   | index file ...;        |
| ------ | ---------------------- |
| 默认值 | `index index.html;`    |
| 位置   | http、server、location |

index后面可以跟多个设置，如果访问的时候没有指定具体访问的资源，则会依次进行查找，找到第一个为止。

```nginx
location / {
	root /usr/local/nginx/html;
	index index.html index.htm;
}
访问该location的时候，可以通过 http://ip:port/，地址后面如果不添加任何内容，则默认依次访问index.html和index.htm，找到第一个来进行返回
```



#### 1.6 error_page

设置网站的错误页面，当出现对应的响应code后，如何来处理。

| 语法   | error_page code ... [=[response]] uri; |
| ------ | -------------------------------------- |
| 默认值 | —                                      |
| 位置   | http、server、location......           |



**指定具体跳转的地址**

```nginx
server {
	error_page 404 http://www.itcast.cn;
}
```

**指定重定向地址**

> 出现404后，重定向到`/50x.html`这个location

```nginx
server{
	error_page 404 /50x.html;
	error_page 500 502 503 504 /50x.html;
	location =/50x.html{
		root html;
	}
}
```

**使用location的@符号完成错误信息展示**

```nginx
server{
	error_page 404 @jump_to_error;
	location @jump_to_error {
		default_type text/plain;
		return 404 'Not Found Page...';
	}
}
```

**`=[response]`的作用是用来修改状态码**

> 出现404后，将状态码修改为200，然后重定向到`/50.html`

```nginx
server{
	error_page 404 =200 /50x.html;
	location =/50x.html{
		root html;
	}
}
这样的话，当返回404找不到对应的资源的时候，在浏览器上可以看到，最终返回的状态码是200，这块需要注意下，编写error_page后面的内容，404后面需要加空格，200前面不能加空格
```



### 2、静态资源优化

#### 2.1 sendﬁle

> 通过零拷贝的方式来优化文件传输性能，`sendfile`函数可以指定发往哪个socket，因此可以直接将数据从内核缓冲区发送到socket缓冲区

| 语法   | sendﬁle on \|oﬀ;          |
| ------ | ------------------------- |
| 默认值 | sendﬁle oﬀ;               |
| 位置   | http、server、location... |



####  2.2 tcp_nopush

该指令必须在`sendfile`打开的状态下才会生效，主要是用来提升网络包的**传输效率**，拿到数据后不是立刻发送，而是等数据达到一定大小时再发送

| 语法   | tcp_nopush on\|off;    |
| ------ | ---------------------- |
| 默认值 | tcp_nopush oﬀ;         |
| 位置   | http、server、location |



#### 2.3 tcp_nodelay

该指令必须在`keep-alive`连接开启的情况下才生效，来提高网络包传输的**实时性**，拿到数据就立马发送，不会等数据到达一定大小再发送

| 语法   | tcp_nodelay on\|off;   |
| ------ | ---------------------- |
| 默认值 | tcp_nodelay on;        |
| 位置   | http、server、location |



`tcp_nopush`和`tcp_nodelay`看起来是互斥的，但是可以将这两个值都打开，在linux2.5.9后的版本中两者是可以兼容的，三个指令都开启的好处是，`sendfile`可以开启高效的文件传输模式，`tcp_nopush`开启可以确保在发送到客户端之前数据包已经充分“填满”， 大大减少了网络开销，并加快了文件发送的速度。 然后，当它到达最后一个可能因为没有“填满”而暂停的数据包时，Nginx会忽略`tcp_nopush`参数， 然后，`tcp_nodelay`强制socket发送数据。因此`TCP_NOPUSH`可以与`TCP_NODELAY`一起设置，它比单独配置`TCP_NODELAY`具有更强的性能。

```
sendfile on;
tcp_nopush on;
tcp_nodelay on;
```



### 3、静态资源压缩

Nginx的配置文件中可以通过配置`gzip`来对静态资源进行压缩，相关的指令可以配置在http块、server块和location块中，Nginx可以通过`ngx_http_gzip_module`模块、`ngx_http_gzip_static_module`模块和`ngx_http_gunzip_module`模块对这些指令进行解析和处理


#### 3.1 Gzip模块配置

下边的指令都来自`ngx_http_gzip_module`模块，该模块会在nginx安装的时候内置到nginx的安装环境中，可以直接使用这些指令。

**gzip**

开启或者关闭gzip功能，该指令为打开状态时，后边的指令才有效

| 语法   | gzip on\|off;             |
| ------ | ------------------------- |
| 默认值 | gzip off;                 |
| 位置   | http、server、location... |

```nginx
http{
   gzip on;
}
```



**gzip_types**

根据响应页的MIME类型选择性地开启Gzip压缩功能

| 语法   | gzip_types mime-type ...; |
| ------ | ------------------------- |
| 默认值 | gzip_types text/html;     |
| 位置   | http、server、location    |

所选择的值可以从`mime.types`文件中进行查找，使用`*`代表所有。

```nginx
http{
	gzip_types application/javascript;
}
```



**gzip_comp_level**

设置Gzip压缩程度，级别从1-9，1表示压缩程度最低，压缩效率最高，9表示压缩程度最高，但效率最低

| 语法   | gzip_comp_level level; |
| ------ | ---------------------- |
| 默认值 | gzip_comp_level 1;     |
| 位置   | http、server、location |

```nginx
http{
	gzip_comp_level 6;
}
```



**gzip_vary**

设置使用Gzip进行压缩发送是否携带`Vary:Accept-Encoding`的响应头。主要是告诉接收方，所发送的数据经过了Gzip压缩处理

| 语法   | gzip_vary on\|off;     |
| ------ | ---------------------- |
| 默认值 | gzip_vary off;         |
| 位置   | http、server、location |



**gzip_buffers**

处理请求压缩的缓冲区数量和大小，`number`表示Nginx服务器向系统申请缓存空间个数，`size`表示每个缓存空间的大小。这个值的设定一般会和服务器的操作系统有关，建议此项不设置，使用默认值即可。

| 语法   | gzip_buffers number size;  |
| ------ | -------------------------- |
| 默认值 | gzip_buffers 32 4k\|16 8k; |
| 位置   | http、server、location     |

```
gzip_buffers 4 16K;	  #缓存空间大小
```



**gzip_disable**

针对不同种类客户端发起的请求，可以选择性地开启和关闭Gzip功能。

| 语法   | gzip_disable regex ...; |
| ------ | ----------------------- |
| 默认值 | —                       |
| 位置   | http、server、location  |

`regex`：根据客户端的浏览器标识`user-agent`来设置，支持使用正则表达式。指定的浏览器标识不使用Gzip。该指令一般是用来排除一些明显不支持Gzip的浏览器。

```
gzip_disable "MSIE [1-6]\.";
```



**gzip_http_version**

针对不同的HTTP协议版本，可以选择性地开启和关闭Gzip功能。该指令指定使用Gzip的HTTP最低版本，一般采用默认值即可。

| 语法   | gzip_http_version 1.0\|1.1; |
| ------ | --------------------------- |
| 默认值 | gzip_http_version 1.1;      |
| 位置   | http、server、location      |



**gzip_min_length**

针对传输数据的大小，可以选择性地开启和关闭Gzip功能

> nignx计量大小的单位：bytes[字节] / kb[千字节] / M[兆]
> 例如: 1024 / 10k|K / 10m|M

| 语法   | gzip_min_length length; |
| ------ | ----------------------- |
| 默认值 | gzip_min_length 20;     |
| 位置   | http、server、location  |

Gzip压缩功能对大数据的压缩效果明显，但是如果要压缩的数据比较小的化，可能出现越压缩数据量越大的情况，因此我们需要根据响应内容的大小来决定是否使用Gzip功能，响应页面的大小可以通过头信息中的`Content-Length`来获取。但是如何使用了Chunk编码动态压缩，该指令将被忽略。建议设置为1K或以上。



**gzip_proxied**

设置是否对服务端返回的结果进行Gzip压缩。

| 语法   | gzip_proxied  off\|expired\|no-cache\|<br/>no-store\|private\|no_last_modified\|no_etag\|auth\|any; |
| ------ | ------------------------------------------------------------ |
| 默认值 | gzip_proxied off;                                            |
| 位置   | http、server、location                                       |

`off`：关闭Nginx服务器对后台服务器返回结果的Gzip压缩

`expired` ：启用压缩，如果header头中包含 `Expires`头信息

`no-cache` ：启用压缩，如果header头中包含 `Cache-Control:no-cache` 头信息

`no-store` ：启用压缩，如果header头中包含 `Cache-Control:no-store` 头信息

`private` ：启用压缩，如果header头中包含 `Cache-Control:private` 头信息

`no_last_modified` ：启用压缩,如果header头中不包含 `Last-Modified` 头信息

`no_etag` ：启用压缩 ,如果header头中不包含 `ETag` 头信息

`auth` ：启用压缩 , 如果header头中包含`Authorization` 头信息

`any` ：无条件启用压缩



#### 3.2 Gzip和sendfile共存问题

开启sendfile以后，读取磁盘上的静态资源文件时，可以减少拷贝的次数，不经过用户进程将静态文件通过网络设备发送出去，但是Gzip要想对资源压缩，是需要经过用户进程进行操作的。所以需要解决这两个设置的共存问题。

可以使用`ngx_http_gzip_static_module`模块的`gzip_static`指令来解决。

**gzip_static**

检查与访问资源同名的`.gz`文件时，并返回该`.gz`文件的内容。假设有`a.html`和`a.html.gz`两个文件，开启该配置后，就会返回`a.html.gz`这个文件

| 语法   | **gzip_static** on \| off \| always; |
| ------ | ------------------------------------ |
| 默认值 | gzip_static off;                     |
| 位置   | http、server、location               |



### 4、静态资源的缓存

#### 4.1 浏览器缓存

为了节约网络的资源，浏览器在用户磁盘上对最近请求过的文档进行存储，当客户端再次请求这个页面时，浏览器就可以从本地磁盘获取数据，加速页面的阅览



**HTTP协议中缓存字段**

| header            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| Expires           | 缓存过期的时间，绝对时间                                     |
| Cache-Control     | 缓存过期的时间，相对时间                                     |
| If-Modified-Since | 当资源过期了，发现响应头中有Last-Modified，则再次发起请求时这个值就是Last-Modified |
| Last-Modified     | 请求资源最后修改时间                                         |
| If-None-Match     | 当资源过期时，浏览器发现响应头里有Etag，则再次向服务器发起请求时，会将请求头lf-None-Match值设置为Etag 的值 |
| ETag              | 唯一标识响应资源，比如文件的MD5值                            |

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411172241735.png" style="zoom:67%;" />

#### 4.2 浏览器缓存

##### 4.2.1 expires

该指令可以控制HTTP应答中的`Expires`和`Cache-Control`

| 语法   | expires   [modified] time<br/>expires epoch\|max\|off; |
| ------ | ------------------------------------------------------ |
| 默认值 | expires off;                                           |
| 位置   | http、server、location                                 |

`time`：可以整数也可以是负数，指定过期时间，如果是负数，`Cache-Control`则为`no-cache`，如果为整数或0，则`Cache-Control`的值为`max-age=time;`

`epoch`：指定`Expires`的值为`1 January,1970,00:00:01 GMT'(1970-01-01 00:00:00)`，`Cache-Control`的值`no-cache`

`max`：指定`Expires`的值为`'31 December2037 23:59:59GMT' (2037-12-31 23:59:59)` ，`Cache-Control`的值为10年

`off`：默认不缓存



##### 4.2.2 add_header

添加指定的响应头和响应值。

| 语法   | add_header name value [always]; |
| ------ | ------------------------------- |
| 默认值 | —                               |
| 位置   | http、server、location...       |

`Cache-Control`作为响应头信息，可以设置如下指令

```nginx
Cache-control: must-revalidate
Cache-control: no-cache
Cache-control: no-store
Cache-control: no-transform
Cache-control: public
Cache-control: private
Cache-control: proxy-revalidate
Cache-Control: max-age=<seconds>
Cache-control: s-maxage=<seconds>
```

| 指令               | 说明                                           |
| ------------------ | ---------------------------------------------- |
| `must-revalidate`  | 可缓存但必须再向源服务器进行确认               |
| `no-cache`         | 缓存前必须确认其有效性                         |
| `no-store`         | 不缓存请求或响应的任何内容                     |
| `no-transform`     | 代理不可更改媒体类型                           |
| `public`           | 可向任意方提供响应的缓存                       |
| `private`          | 仅向特定用户返回响应                           |
| `proxy-revalidate` | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| `max-age=<秒>`     | 响应最大Age值                                  |
| `s-maxage=<秒>`    | 公共缓存服务器响应的最大Age值                  |



### 5、Nginx的跨域问题

#### 5.1 同源策略

- 浏览器的同源策略：是一种约定，是浏览器最核心也是最基本的安全功能，如果浏览器少了同源策略，则浏览器的正常功能可能都会受到影响。

- 同源:  协议、域名(IP)、端口相同即为同源

  ```
  http://192.168.200.131/user/1
  https://192.168.200.131/user/1
  不满足
  
  http://192.168.200.131/user/1
  http://192.168.200.131:8080/user/1
  不满足
  
  http://www.nginx.com/user/1
  http://www.nginx.org/user/1
  不满足
  
  http://www.nginx.org:80/user/1
  http://www.nginx.org/user/1
  满足
  ```

  

#### 5.2 跨域问题

有两台服务器分别为A和B，如果从服务器A的页面发送**异步请求**到服务器B获取数据，如果服务器A和服务器B不满足同源策略，则就会出现跨域问题。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411180956484.png)

（1）nginx的html目录下新建一个a.html

```html
<html>
  <head>
        <meta charset="utf-8">
        <title>跨域问题演示</title>
        <script src="jquery.js"></script>
        <script>
            $(function(){
                $("#btn").click(function(){
                        $.get('http://192.168.200.133:8080/getUser',function(data){
                                alert(JSON.stringify(data));
                        });
                });
            });
        </script>
  </head>
  <body>
        <input type="button" value="获取数据" id="btn"/>
  </body>
</html>

```

（2）在nginx.conf配置如下内容

```nginx
server{
        listen  8080;
        server_name localhost;
        location /getUser{
                default_type application/json;
                return 200 '{"id":1,"name":"TOM","age":18}';
        }
}
server{
	listen 	80;
	server_name localhost;
	location /{
		root html;
		index index.html;
	}
}
```

(3)通过浏览器访问测试

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411180957501.png)



#### 5.3 跨域问题解决

使用`add_header`指令，来添加一些头信息

| 语法   | add_header name  value... |
| ------ | ------------------------- |
| 默认值 | —                         |
| 位置   | http、server、location    |

解决跨域问题，需要添加两个头信息

- `Access-Control-Allow-Origin`：允许跨域访问的源地址信息，可以配置多个(多个用逗号分隔)，也可以使用`*`代表所有源
- `Access-Control-Allow-Methods`：允许跨域访问的请求方式，值可以为 GET、POST、PUT、DELETE等，也可以全部设置

```nginx
location /getUser{
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE;
    default_type application/json;
    return 200 '{"id":1,"name":"TOM","age":18}';
}
```



### 6、静态资源防盗链

#### 6.1 资源盗链

资源盗链指的是此内容不在自己服务器上，而是通过技术手段，绕过别人的限制将别人的内容放到自己页面上最终展示给用户。以此来盗取大网站的空间和流量。

```nginx
京东:https://img14.360buyimg.com/n7/jfs/t1/101062/37/2153/254169/5dcbd410E6d10ba22/4ddbd212be225fcd.jpg

百度:https://pics7.baidu.com/feed/cf1b9d16fdfaaf516f7e2011a7cda1e8f11f7a1a.jpeg?token=551979a23a0995e5e5279b8fa1a48b34&s=BD385394D2E963072FD48543030030BB

准备一个页面，在页面上引入这两个图片查看效果，可以发现，下面的图片地址添加了防盗链的功能，京东这边可以直接使用其图片
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411181001146.png" style="zoom:80%;" />





#### 6.2 防盗链实现原理

`Referer`：HTTP的头信息，当浏览器向web服务器发送请求的时候，通过`Referer`来告诉浏览器该网页是从哪个页面链接过来的。后台服务器可以根据获取到的这个`Referer`信息来判断是否为自己信任的网站地址，如果是则放行继续访问，如果不是则可以返回403(服务端拒绝访问)的状态信息。



**Nginx防盗链的实现**

`valid_referers`：nginx通过查看`referer`和`valid_referers`后面的内容进行匹配，如果匹配到了就将`$invalid_referer`变量置0，如果没有匹配到，则将`\$invalid_referer`变量置为1，匹配的过程中不区分大小写。

| 语法   | valid_referers none\|blocked\|server_names\|string... |
| ------ | ----------------------------------------------------- |
| 默认值 | —                                                     |
| 位置   | server、location                                      |

`none`：如果Header中的Referer为空，允许访问

`blocked`：Header中的Referer不为空，但是该值被防火墙或代理进行伪装过，如不带"http://" 、"https://"等协议头的资源允许访问。

`server_names`：指定具体的域名或者IP

`string`：可以支持正则表达式和*的字符串。如果是正则表达式，需要以`~`开头表示

```nginx
location ~*\.(png|jpg|gif){
           valid_referers none blocked www.baidu.com 192.168.200.222 *.example.com example.*  www.example.org  ~\.google\.;
           if ($invalid_referer){
                return 403;
           }
           root /usr/local/nginx/html;
}
```



#### 6.3 针对目录进行防盗链

```nginx
location /images {
           valid_referers none blocked www.baidu.com 192.168.200.222 *.example.com example.*  www.example.org  ~\.google\.;
           if ($invalid_referer){
                return 403;
           }
           root /usr/local/nginx/html;
}
```

这样可以对一个目录下的所有资源进行防盗链操作。但是`Referer`的限制比较粗，比如随意加一个Referer，此时我们需要用到Nginx的第三方模块`ngx_http_accesskey_module`



### 7、Rewrite

`Rewrite`是Nginx服务器提供的一个重要基本功能，主要用来实现URL的重写。

> Nginx服务器的`Rewrite`功能实现依赖于**PCRE**的支持，因此在编译安装Nginx服务器之前，需要安装PCRE库。Nginx使用的是`ngx_http_rewrite_module`模块来解析和处理`Rewrite`功能的相关配置



**重写和转发的区别**

- 地址重写浏览器地址会发生变化，而地址转发不会变化
- 地址重写会产生两次请求，地址转发只会产生一次请求
- 地址重写到的页面必须是一个完整的路径而地址转发则不需要
- 地址重写因为是两次请求，所以request范围内属性不能传递给新页面，地址转发是一次请求所以可以传递值。地址转发速度快于地址重写



#### 7.1 set

设置一个新的变量

| 语法   | set $variable value; |
| ------ | -------------------- |
| 默认值 | —                    |
| 位置   | server、location、if |

`variable`：变量的名称，该变量名称要用`$`作为变量的第一个字符，且不能与Nginx服务器预设的全局变量同名

`value`：变量的值，可以是字符串、其他变量或者变量的组合等



**Rewrite常用全局变量**

| 变量               | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| $args              | 存放了请求URL中的请求参数。比如`http://192.168.200.133:8080?arg1=value1&args2=value2`中的`arg1=value1&arg2=value2`，功能和`$query_string`一样 |
| $http_user_agent   | 用户访问服务器的代理信息(如果通过浏览器访问，记录的是浏览器的相关版本信息) |
| $host              | 访问服务器的`server_name`值                                  |
| $document_uri      | 当前访问地址的URI。比如`http://192.168.200.133/server?id=10&name=zhangsan`中的`/server`，功能和`$uri`一样 |
| $document_root     | 当前请求对应`location`的`root`值，如果未设置，默认指向Nginx自带html目录所在位置 |
| $content_length    | 请求头中的Content-Length的值                                 |
| $content_type      | 请求头中的Content-Type的值                                   |
| $http_cookie       | 客户端的cookie信息，可以通过`add_header Set-Cookie 'cookieName=cookieValue'`来添加cookie数据 |
| $limit_rate        | Nginx服务器对网络连接速率的限制，也就是Nginx配置中对limit_rate指令设置的值，默认是0，不限制。 |
| $remote_addr       | 客户端的IP地址                                               |
| $remote_port       | 客户端与服务端建立连接的端口号                               |
| $remote_user       | 客户端的用户名，需要有认证模块才能获取                       |
| $scheme            | 访问协议                                                     |
| $server_addr       | 服务端的地址                                                 |
| $server_name       | 客户端请求到达的服务器的名称                                 |
| $server_port       | 客户端请求到达服务器的端口号                                 |
| $server_protocol   | 客户端请求协议的版本，比如"HTTP/1.1"                         |
| $request_body_file | 发给后端服务器的本地文件资源的名称                           |
| $request_method    | 客户端的请求方式，比如"GET","POST"等                         |
| $request_filename  | 当前请求的资源文件的路径名                                   |
| $request_uri       | 当前请求的URI，并且携带请求参数，比如`http://192.168.200.133/server?id=10&name=zhangsan`中的`/server?id=10&name=zhangsan` |



#### 8.2 if

条件判断，并根据条件判断结果选择不同的Nginx配置

| 语法   | if  (condition){...} |
| ------ | -------------------- |
| 默认值 | —                    |
| 位置   | server、location     |

condition为判定条件（if 和括号之间要有空格），可以支持以下写法：

1. 变量名。如果变量名对应的值为空或者是0，if判断为false，其他条件为true。

```nginx
if ($param){
}
```

2. 使用`=`和`!=`比较变量和字符串是否相等，满足条件为true，不满足为false。(字符串不需要添加引号)

```nginx
if ($request_method = POST){
	return 405;
}
```

3. 使用正则表达式对变量进行匹配，匹配成功返回true，否则返回false。变量与正则表达式之间使用`~`，`~*`，`!~`，`!~\*`来连接

- `~`表示匹配正则表达式过程中区分大小写，

- `~\*`代表匹配正则表达式过程中不区分大小写

- `!~`和`!~\*`和上面取相反值，如果匹配返回false，不匹配返回true

> 正则表达式字符串一般不需要加引号，但是如果字符串中包含`}`或者是`;`等字符时，需要把引号加上

```nginx
if ($http_user_agent ~ MSIE){
	# $http_user_agent的值中是否包含MSIE字符串，如果包含返回true
}
```

4. 判断请求的文件是否存在使用`-f`和`!-f`


- `-f`：如果请求的文件存在返回true，不存在返回false

- `!-f`：如果请求文件不存在，但该文件所在目录存在返回true，文件和目录都不存在返回false，如果文件存在返回false

```nginx
if (-f $request_filename){
	# 判断请求的文件是否存在
}
if (!-f $request_filename){
	# 判断请求的文件是否不存在
}
```

5. 判断请求的目录是否存在使用`-d`和`!-d`


- `-d`：如果请求的目录存在，if返回true，如果目录不存在则返回false

- `!-d`：如果请求的目录不存在但该目录的上级目录存在则返回true，该目录和它上级目录都不存在则返回false，如果请求目录存在也返回false

5. 判断请求的目录或者文件是否存在使用`-e`和`!-e`


- `-e`：如果请求的目录或者文件存在时，if返回true，否则返回false

- `!-e`：如果请求的文件和文件所在路径上的目录都不存在返回true，否则返回false

5. 判断请求的文件是否可执行使用`-x"和"!-x`


- `-x`：如果请求的文件可执行，if返回true，否则返回false

- `!-x`：如果请求文件不可执行，返回true，否则返回false



#### 8.3 break

该指令用于中断当前相同作用域中的其他Nginx配置（跳出当前作用域）。与该指令处于同一作用域的Nginx配置中，位于它前面的指令配置生效，位于后面的指令配置无效。并且break还会终止当前的匹配并把当前的URI在本location进行重定向访问处理

| 语法   | break;               |
| ------ | -------------------- |
| 默认值 | —                    |
| 位置   | server、location、if |

```nginx
location /testbreak {
    default_type text/plain;
    set $username TOM;
	if ($args){
		set $username JERRY;
		break;
		set $username ROSE; # 无效
	}
    add_header username $username;
    return 200 $username;
}
```



#### 8.4 return

该指令用于完成对请求的处理，直接向客户端返回响应状态代码。在return后的所有Nginx配置都是无效的。

| 语法   | return code [text];<br/>return code URL;<br/>return URL; |
| ------ | -------------------------------------------------------- |
| 默认值 | —                                                        |
| 位置   | server、location、if                                     |

`code`：返回给客户端的HTTP状态码。可以返回的状态代码为0~999的任意HTTP状态代理

`text`：返回给客户端的响应体内容，支持变量的使用

`URL`：返回给客户端的URL地址

```nginx
location / {
    default_type text/plain;
    return 200 "欢迎使用 Nginx";
}

location /baidu {
    return 302 https://www.baidu.com;
}
```



#### 8.5 rewrite

该指令通过正则表达式的使用来改变URI。可以同时存在一个或者多个指令，按照顺序依次对URL进行匹配和处理。

| 语法   | rewrite regex replacement [flag]; |
| ------ | --------------------------------- |
| 默认值 | —                                 |
| 位置   | server、location、if              |

`regex`：用来匹配URI的正则表达式

`replacement`：匹配成功后，用于替换URI中被截取内容的字符串。如果该字符串是以`http://`或者`https://`开头的，则不会继续向下对URI进行其他处理，而是直接返回重写后的URI给客户端。

```nginx
# ......
listen 8081;
location /rewrite {
    rewrite ^/rewrite/url\w*$ https://www.baidu.com;
    rewrite ^/rewrite/(test)\w*$ /$1;   # 如果是 /rewrite/testxxx，则重写 url 为 /test
    rewrite ^/rewrite/(demo)\w*$ /$1;   # 如果是 /rewrite/demoxxx，则重写 url 为 /demo
}
location /test {   # 重写后的 url 如果为 test，触发 location
    default_type text/plain;
    return 200 test_sucess;
}
location /demo {   # 重写后的 url 如果为 demo，触发 location
    default_type text/plain;
    return 200 demo_sucess;
}

# 访问 http://192.168.200.113/8081/rewrite/urlxxx，跳转到 https://www.baidu.com。
# 访问 http://192.168.200.113/8081/rewrite/testxxx，返回 test_sucess。
# 访问 http://192.168.200.113/8081/rewrite/demoxxx，返回 demo_sucess。
```



`flag`：用来设置rewrite对URI的处理行为

- `last`：终止继续在本location块中处理接收到的URI，并将此处重写的URI作为一个新的URI，使用各location块进行处理。该标志将重写后的URI重写在server块中执行，为重写后的URI提供了转入到其他location块的机会。**重写地址后访问其他的 location 块，浏览器地址栏 URL 地址不变**

  ```nginx
  # ......
  listen 8081;
  location /rewrite {
      rewrite ^/rewrite/(test)\w*$ /$1 last;   # 如果是 /rewrite/testxxx，则重写 url 为 /test
      rewrite ^/rewrite/(demo)\w*$ /$1 last;   # 如果是 /rewrite/demoxxx，则重写 url 为 /demo
  }
  location /test {   # 重写后的 url 如果为 test，触发 location
      default_type text/plain;
      return 200 test_sucess;
  }
  location /demo {   # 重写后的 url 如果为 demo，触发 location
      default_type text/plain;
      return 200 demo_sucess;
  }
  
  # 访问 http://192.168.200.113/8081/rewrite/testxxx，返回 test_sucess。
  # 访问 http://192.168.200.113/8081/rewrite/demoxxx，返回 demo_sucess。
  ```

- `break`：将此处重写的URI作为一个新的URl,在本块中继续进行处理。该标志将重写后的地址在当前的location块中执行，不会将新的URI转向其他的location块。**仅仅重写地址，不会触发其他 location 块，浏览器地址栏 URL 地址不变**

  ```nginx
  # ......
  listen 8081;
  location /rewrite {
      rewrite ^/rewrite/(test)\w*$ /$1 break;   # 如果是 /rewrite/testxxx，则重写 url 为 /test
      rewrite ^/rewrite/(demo)\w*$ /$1 break;   # 如果是 /rewrite/demoxxx，则重写 url 为 /demo
      
      # /test 和 /demo 就在当前块进行处理，所以会在当前的 location 块找到如下 html 页面：
      # /usr/local/nginx/html/test/index.html
      # /usr/local/nginx/html/demo/index.html
  }
  location /test {   # 重写后的 url 如果为 test，触发 location
      default_type text/plain;
      return 200 test_sucess;
  }
  location /demo {   # 重写后的 url 如果为 demo，触发 location
      default_type text/plain;
      return 200 demo_sucess;
  }
  ```

- `redirect`：将重写后的URI返回给客户端，状态码为302，指明是临时重定向URI，主要用在`replacement`变量不是以`http://`或者`https://`开头的情况

  > 特点是重定向。如发送请求 `/testxxx`，它会重定向到 `/test`，触发第二个 location 块，浏览的地址栏也会由 `/testxxx` 变成 `/test`

  ```nginx
  # ......
  listen 8081;
  location /rewrite {
      rewrite ^/rewrite/(test)\w*$ /$1 redirect;   # 如果是 /rewrite/testxxx，则重写 url 为 /test
      rewrite ^/rewrite/(demo)\w*$ /$1 redirect;   # 如果是 /rewrite/demoxxx，则重写 url 为 /demo
  }
  location /test {   # 重写后的 url 如果为 test，触发 location
      default_type text/plain;
      return 200 test_sucess;
  }
  location /demo {   # 重写后的 url 如果为 demo，触发 location
      default_type text/plain;
      return 200 demo_sucess;
  }
  ```

- `permanent`：将重写后的URI返回给客户端，状态码为301，指明是永久重定向URI，主要用在`replacement`变量不是以`http://`或者`https://`开头的情况

  ```nginx
  # ......
  listen 8081;
  location /rewrite {
      rewrite ^/rewrite/(test)\w*$ /$1 permanent;   # 如果是 /rewrite/testxxx，则重写 url 为 /test
      rewrite ^/rewrite/(demo)\w*$ /$1 permanent;   # 如果是 /rewrite/demoxxx，则重写 url 为 /demo
  }
  location /test {   # 重写后的 url 如果为 test，触发 location
      default_type text/plain;
      return 200 test_sucess;
  }
  location /demo {   # 重写后的 url 如果为 demo，触发 location
      default_type text/plain;
      return 200 demo_sucess;
  }
  ```



**flag总结**

- break 与 last 都停止处理后续重写规则，只不过 last 会重新发起新的请求并使用新的请求路由匹配location，但 break 不会。所以当请求 break 时，如匹配成功，则请求成功，返回 200；如果匹配失败，则返回 404
- 服务器配置好 redirect 和 permanent 之后，打开浏览器分别访问这两个请求地址，然后停止 Nginx 服务。这时再访问 redirect 请求会直接报出无法连接的错误。但是 permanent 请求是永久重定向，浏览器会忽略原始地址直接访问永久重定向之后的地址，所以请求仍然成功。（这个验证不能禁用浏览器的缓存，否则即使是 permanent 重定向，浏览器仍然会向原始地址发出请求验证之前的永久重定向是否有效）
- 对于搜索引擎来说，搜索引擎在抓取到 301 永久重定向请求响应内容的同时也会将原始的网址替换为重定向之后的网址，而对于 302 临时重定向请求则仍然会使用原始的网址并且可能会被搜索引擎认为有作弊的嫌疑。所以对于线上正式环境来讲，尽量避免使用 302 跳转
- 如果 replacement 以 「 http:// 」或「 https:// 」或「 $scheme 」开始，处理过程将终止，并将这个重定向直接返回给客户端



#### 8.6 rewrite_log指令

该指令配置是否开启URL重写日志的输出功能。开启后，URL重写的相关日志将以notice级别输出到error_log指令配置的日志文件汇总。

| 语法   | rewrite_log on\|off;       |
| ------ | -------------------------- |
| 默认值 | rewrite_log off;           |
| 位置   | http、server、location、if |

```nginx
location /rewrite_log {
    rewrite_log on;    # 开启重写日志
	error_log logs /error.log notice;   # 切换为 notice 模式，因为只支持这个模式
    return 200 '开启了重写日志';
}
```



### 8、Rewrite案例

#### 8.1 域名跳转

> 如果想访问京东，可以输入`www.jd.com`，但是输入`www.360buy.com`同样也能访问到京东。京东刚开始的时候域名就是www.360buy.com，后面把自己的域名换成了www.jd.com，虽然说域名变量，但是对于以前只记住了www.360buy.com的用户来说，我们如何把这部分用户也迁移到我们新域名的访问上来，针对于这个问题，可以使用Nginx中Rewrite的域名跳转来解决。
>



**具体操作**

1、准备两个域名  www.360buy.com | www.jd.com

```
vim /etc/hosts
```

```
127.0.0.1 www.360buy.com
127.0.0.1 www.jd.com
```

2、在`/usr/local/nginx/html/hm`目录下创建一个访问页面

```html
<html>
	<title></title>
	<body>
		<h1>欢迎来到我们的网站</h1>
	</body>
</html>
```

3、通过Nginx实现当访问`www.jd.com`访问到系统的首页

```
server {
	listen 80;
	server_name www.jd.com;
	location /{
		root /usr/local/nginx/html/hm;
		index index.html;
	}
}
```

4、通过Rewrite将`www.360buy.com`的请求跳转到`www.jd.com`

```
server {
	listen 80;
	server_name www.360buy.com;
	rewrite ^/ http://www.jd.com permanent;
}
```



**在域名跳转的过程中携带请求的URI**

> 比如 `www.360buy.com?part=显示器` 变成 `www.jd.com?part=显示器`

```nginx
server {
	listen 80;
	server_name www.360buy.com;
	rewrite ^(.*) http://www.jd.com$1 permanent; 
    # 括号里是 www.360buy.com 后面出现 0 次或 多次不以 \n（换行）结尾的值，该值赋给 $1。
}
```



**通过Rewrite来实现多个域名的跳转**

1、添加域名

```
vim /etc/hosts
192.168.200.133 www.jingdong.com
```

2、修改配置信息

```
server{
	listen 80;
	server_name www.360buy.com www.jingdong.com;
	rewrite ^(.*) http://www.jd.com$1 permanent;
}
```



#### 8.2 域名镜像

> `www.360buy.com` 和 `www.jingdong.com` 都能跳转到 `www.jd.com`，那么 `www.jd.com` 可以把它叫主域名，其他两个就是镜像域名，如果我们不想把整个网站做镜像，只想为其中某一个子目录下的资源做镜像，比如用户可以跳到首页 Web下，而管理员跳转到后台 Web，我们可以在 location 块中配置 Rewrite 功能。

```nginx
server {
	listen 80;
	server_name rewrite.myweb.com;
	location ^~ /user {
		rewrite ^/user(.*) http://www.myweb.com/index$1 last;  # 用户跳到首页
	}
	location ^~ /manage {
		rewrite ^/manage(.*) http://www.myweb.com/manage$1 last;  # 管理员跳到后台
	}
}
```



#### 8.3 独立域名

> 一个完整的项目包含多个模块，比如购物网站有商品搜索模块、商品详情模块、购物车模块等，那么如何为每一个模块设置独立的域名。

**需求**

- `http://search.product.com`：访问商品搜索模块
- `http://item.product.com`：访问商品详情模块
- `http://cart.product.com`：访问商品购物车模块

```nginx
server{
	listen 80;
	server_name search.product.com;
	rewrite ^(.*) http://www.shop.com/search$1 last;
}
server{
	listen 81;
	server_name item.product.com;
	rewrite ^(.*) http://www.shop.com/item$1 last;
}
server{
	listen 82;
	server_name cart.product.com;
	rewrite ^(.*) http://www.shop.com/cart$1 last;
}
```



#### 8.4 目录自动添加"/"

> 有时候访问的地址要求后面以 `/` 结尾，那么我们需要解决如果用户忘记输入 `/`，Nginx 自动加上 `/`。针对访问目录的情况，访问具体页面不加`/`没有影响

**示例**

```nginx
server {
	listen	80;
	server_name localhost;
	location / {
		root html;
		index index.html;
	}
}
```

通过 `http://192.168.200.133` 可以直接访问以上资源，地址后面不需要加 `/`，但是如果将上述的配置修改为如下内容

```nginx
server {
	listen	80;
	server_name localhost;
	location /frx {
		root html;
		index index.html;
	}
}
```

这时，要想访问上述资源，可以通过 `http://192.168.200.133/frx/` 来访问，但是如果地址后面不加`/`，比如 `http://192.168.200.133/frx`，页面就会出问题。如果不加斜杠，Nginx 服务器内部会自动做一个 **301 重定向**，重定向的地址有一个指令 `server_name_in_redirect` 来决定重定向的地址

- 如果该指令为 on：重定向的地址为：`http://server_name/目录名/`
- 如果该指令为 off：重定向的地址为：`http://原URL中的域名/目录名/`

所以访问 `http://192.168.200.133/frx` 如果不加斜杠

- 如果 `server_name_in_redirect` 为 on，则 301 重定向地址变为 `http://localhost/frx/`，IP 发生改变，地址出现了问题
- 如果 `server_name_in_redirect` 为 off，则 301 重定向地址变为 `http://192.168.200.133/frx/`，没有问题

>  `server_name_in_redirect` 指令在 Nginx 的 0.8.48 版本之前默认都是 on，之后改成了 off，所以现在我们这个版本不需要考虑这个问题，但是如果是 0.8.48 以前的版本并且 server_name_in_redirect 设置为 on，我们可以通过 Rewrite 来解决这个问题

```nginx
server {
	listen	80;
	server_name localhost;
	server_name_in_redirect on;
	location /frx {
		if (-d $request_filename){   # 如果请求的资源目录存在
			rewrite ^/(.*)([^/])$ http://$host/$1$2/ permanent; # $2 获取第二个括号的值：/
		}
	}
}
```



#### 8.5 合并目录

> 搜索引擎优化(SEO)是一种利用搜索引擎的搜索规则来提供目的网站的有关搜索引擎内排名的方式。我们在创建自己的站点时，可以通过很多中方式来有效的提供搜索引擎优化的程度。其中有一项就包含 URL 的目录层级，一般不要超过三层，否则的话不利于搜索引擎的搜索，也给客户端的输入带来了负担，但是将所有的文件放在一个目录下，又会导致文件资源管理混乱，并且访问文件的速度也会随着文件增多而慢下来，这两个问题是相互矛盾的，那么使用 Rewrite 如何解决这些问题呢？

网站中有一个资源文件的访问路径 `/server/11/22/33/44/20.html`，也就是说 20.html 存在于第 5 级目录下，如果想要访问该资源文件，客户端的 URL 地址就要写成 `http://www.web.com/server/11/22/33/44/20.html`，并且在配置文件进行如下配置

```nginx
server {
	listen 80;
	server_name www.web.com;
	location /server {
		root html;
	}
}
```

但是这个是非常不利于 SEO 搜索引擎优化的，同时客户端也不好记。使用 Rewrite 的正则表达式，我们可以进行如下配置

```nginx
server {
    listen 80;
    server_name www.web.com;
    location /server {
        rewrite ^/server-([0-9]+)-([0-9]+)-([0-9]+)-([0-9]+)\.html$  /server/$1/$2/$3/$4/$5.html last;
    }
}
```

这样配置后，客户端只需要输入 `http://www.web.com/server-11-22-33-44-20.html` 就可以访问到 20.html 页面了



#### 8.6 防盗链

> 通过 Rewrite 可以将防盗链的功能进行完善，当出现防盗链的情况，我们可以使用 Rewrite 将请求转发到自定义的一张图片和页面，给用户比较好的提示信息

**根据文件类型实现防盗链配置**

```nginx
server{
	listen 80;
	server_name www.web.com;
	locatin ~* ^.+\.(gif|jpg|png|swf|flv|rar|zip)$ {
		valid_referers none blocked server_names *.web.com; # server_names 后指定具体的域名或者 IP
		if ($invalid_referer){
			rewrite ^/ http://www.web.com/images/forbidden.png;  # 跳转到默认地址
		}
	}
}
```

**根据目录实现防盗链配置**

```nginx
server{
	listen 80;
	server_name www.web.com;
	location /file {
		root /server/file;  # 资源在 server 目录下的 file 目录里
		valid_referers none blocked server_names *.web.com; # server_names 后指定具体的域名或者 IP
		if ($invalid_referer){
			rewrite ^/ http://www.web.com/images/forbidden.png;  # 跳转到 file 目录下的图片
		}
	}
}
```





## 四、反向代理

正向代理代理的对象是客户端，反向代理代理的是服务端。Nginx 即可以实现正向代理，也可以实现反向代理。



### 1、正向代理

<img src="https://cdn.jsdelivr.net/gh/xustudyxu/image-hosting1@master/20220802/image.53oygoz19gc0.webp" alt="image" style="zoom:80%;" />

**1、服务端设置**

```nginx
http {
    log_format main 'client send request=>clientIp=$remote_addr serverIp=$host';
	server{
		listen 80;
		server_name	localhost;
		access_log logs/access.log main;
		location / {
			root html;
			index index.html index.htm;
		}
	}
}
```

**2、客户端访问**

使用客户端访问服务端：`http://192.168.200.133`，打开日志查看结果

![image](https://cdn.jsdelivr.net/gh/xustudyxu/image-hosting1@master/20220802/image.16ifxal7swyk.webp)

**3、代理服务器设置**

```nginx
server {
    listen  82;
    resolver 8.8.8.8;   # 设置 DNS 的 IP，用来解析 proxy_pass 中的域名
    location / {
        proxy_pass http://$host$request_uri;   # proxy_pass 实现正向代理
    }
}
```

**4、配置代理服务器的**

配置代理服务器的 IP  `192.168.200.146`和 Nginx 配置监听的端口82

![image](https://cdn.jsdelivr.net/gh/xustudyxu/image-hosting1@master/20220802/image.3yx93qbazra0.webp)

**5、设置完成后，再次通过浏览器访问服务端**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411221118753.png)

使用代理后，服务端能看到的只是代理发送过去的请求，这样就使用 Nginx 实现了正向代理的设置。



### 2、反向代理语法配置

Nginx 反向代理模块的指令是由 `ngx_http_proxy_module` 模块进行解析，该模块在安装 Nginx 的时候已经自动加载到 Nginx 中了，反向代理中的常用指令如下

- `proxy_pass`：配置代理的服务器地址
- `proxy_set_header`：转发给被代理服务器时，设置一些请求头信息
- `proxy_redirect`：防止客户端可以看到被代理服务器的地址



#### 2.1 proxy_pass

该指令用来设置被代理服务器的地址，可以是主机名称、IP 地址加端口号形式，没有默认值。

| 语法            | 默认值 | 位置     |
| --------------- | ------ | -------- |
| proxy_pass URL; | —      | location |

`URL`：被代理的服务器地址，包含传输协议(`http`、`https://`)、主机名称或 IP 地址加端口号、URI 等要素。

```nginx
server {
	listen 80;
	server_name localhost;
	location / {
        # 下面两个地址加不加斜杠，效果都一样，因为 location 后的 / 会添加在代理地址后面
		proxy_pass http://192.168.200.146;
		proxy_pass http://192.168.200.146/;
	}
}

server{
	listen 80;
	server_name localhost;
	location /server {
        # 下面两个地址必须加斜杠，因为 location 后的 /server 会添加在代理地址后面，第一个将没有 / 结尾
		# proxy_pass http://192.168.200.146;
		proxy_pass http://192.168.200.146/;
	}
}
# 上面的 location：当客户端访问 http://localhost/server/index.html
# 第一个 proxy_pass 就变成了 http://localhost/server/index.html
# 第二个 proxy_pass 就变成了 http://localhost/index.html 效果就不一样了。
```

- 第一个 location：当客户端访问 `http://localhost/index.html`，两个 `proxy_pass` 效果是一样的，因为 location 后的 `/` 会添加在代理地址后面，所以有没有 `/`，效果都一样。

- 第二个 location：当客户端访问 `http://localhost/server/index.html`，这个时候，第一个 proxy_pass 就变成了 `http://192.168.200.146/server/index.html`，第二个 proxy_pass 就变成了 `http://192.168.200.146/index.html` 效果就不一样了

  如果不以 `/` 结尾，则 location 后的 `/server` 会添加在地址后面，所以第一个 proxy_pass 因为没有 `/` 结尾而被加上 `/server`，而第二个自带了 `/` ，所以不会添加 `/server`。

- 访问任意请求如 `/server` 时，想要代理到其他服务器的首页，则加 `/`，如果真的想访问 `/server` 下的资源，那么不要加 `/`。所以加了 `/` 后，请求的是服务器根目录下的资源。



#### 2.2 proxy_set_header

该指令可以更改Nginx服务器接收到的客户端请求的请求头信息，然后将新的请求头发送给代理的服务器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411221143744.png)

| 语法   | proxy_set_header field value;                                |
| ------ | ------------------------------------------------------------ |
| 默认值 | proxy_set_header Host $proxy_host;<br/>proxy_set_header Connection close; |
| 位置   | http、server、location                                       |

**示例**

1、被代理服务器： 服务器B [192.168.200.146]

```nginx
server {
    listen  8080;
    server_name localhost;
    default_type text/plain;
    return 200 $http_username;    # 获取代理服务器发送过来的 http 请求头的 username 值
}
```

2、代理服务器: 服务器A [192.168.200.133]

```nginx
server {
    listen  8080;
    server_name localhost;
    location /server {           # 访问 /server 触发代理
        proxy_pass http://192.168.200.146:8080/;  # 配置服务器 B 的地址
        proxy_set_header username TOM;  # 发送 key 为 username，value 为 TOM 的请求头给服务器
    }
}
```

3、访问测试

![image](https://cdn.jsdelivr.net/gh/xustudyxu/image-hosting1@master/20220802/image.398ksbw6rv20.webp)

客户端访问的是服务器 A，服务器 A 会将请求转发给服务器 B，服务器 B 返回打印 TOM 的页面给服务器 A，服务器 A 最后返回给客户端



#### 2.3 proxy_redirect

该指令是用来重置头信息中的` Location`和` Refresh `的值，防止客户端可以看到被代理服务器的地址。

| 语法   | proxy_redirect redirect replacement;<br/>proxy_redirect default;<br/>proxy_redirect off; |
| ------ | ------------------------------------------------------------ |
| 默认值 | proxy_redirect default;                                      |
| 位置   | http、server、location                                       |

**示例**

客户端通过代理服务器 A 访问服务器 B 的资源，但是服务器 B 不存在该资源，则会报错。此时我们不希望它直接返回报错页面给客户端，我们希望服务器 B 返回的是它的欢迎页面。

- 首先在服务器 B 进行判断是否存在资源，不存在则返回自己的欢迎页面，即重定向到自己的欢迎页面地址并返回，此时浏览器的地址将会发生改变
- 代理服务器 A 收到服务器 B 的欢迎页面和地址，但是我们不能直接返回给客户端，因为它会暴露服务器 B 的地址，这是重定向的原因

- 此时用到 `proxy_redirect` 指令，重置服务器 B 返回过来的`Location `和`Refresh`值，将两个值改为代理服务器 A 的某个地址
- 因为改为了代理服务器 A 的某个地址，所以代理服务器 A 根据这个地址又去获取理服务器 B 的欢迎页面地址，返回给客户端



**服务端 B `192.168.200.146`**

```nginx
server {
    listen  8081;
    server_name localhost;
    if (!-f $request_filename){
    	return 302 http://192.168.200.146;   #  2.如果请求的资源不存在，则重定向到服务器 B 首页
    }
}
```

**代理服务端 A `192.168.200.133`**

第 6 行代码，当服务器 B 返回的是 `http://192.168.200.146`，为了不让它出现在浏览器的地址栏上，我们需要利用 `proxy_redirect` 将它修改为代理服务器 A 的地址，这个地址会以自己的地址重新访问服务器 B 的欢迎页面，最后返回给客户端。

```nginx
server {
	listen  8081;
	server_name localhost;
	location / {
		proxy_pass http://192.168.200.146:8081/;  # 1.转发给服务器 B
		proxy_redirect http://192.168.200.146 http://192.168.200.133; # 3.修改服务器 B 的地址
	}
}
# 该 server 去请求服务器 B 的欢迎页面
server {
	listen  80;
	server_name 192.168.200.133;
	location / {
		proxy_pass http://192.168.200.146;  # 4.重新发送请求给服务器 B，获取欢迎页面
	}
}
```



**该指令的三组选项**

1、`proxy_redirect redirect replacement;`

- `redirect`：被代理服务器返回的 Location 值
- `replacement`：要替换 Location 的值

2、`proxy_redirect default;`

- `default`：相比较第一组选项，default 仅仅提供了 `redirect` 和 `replacement` 的默认值。将本范围 location 块的 uri 变量作为 `replacement`。将 `proxy_pass` 变量作为 `redwadairect`

  ```nginx
  server {
  	listen  8081;
  	server_name localhost;
  	location /server { 
  		proxy_pass http://192.168.200.146:8081/;
  		proxy_redirect default;  # redirect 是 proxy_pass 的值：http://192.168.200.146:8081/
          						 # replacement 是 location 后的值：/server
          # 等价于：proxy_redirect http://192.168.200.146:8081/ /server
  	}
  }
  ```

3、`proxy_redirect off;`：关闭 proxy_redirect 的功能



### 3、斜杠总结

发送 `http://192.168.199.27/frx/xu` 请求

**不带字符串情况**

> `proxy_pass` 的 ip:port 后加了 `/`，代表去除掉请求和 location 的匹配的字符串，不加则追加全部请求到地址后面。

| 案例 | localtion | proxy_pass             | 匹配    |
| ---- | --------- | ---------------------- | ------- |
| 1    | /frx      | http://192.168.199.27  | /frx/xu |
| 2    | /frx/     | http://192.168.199.27  | /frx/xu |
| 3    | /frx      | http://192.168.199.27/ | //xu    |
| 4    | /frx/     | http://192.168.199.27/ | /xu     |

**带字符串情况**

> `proxy_pass` 的 ip:port 后加了字符串，Nginx 会将匹配 location 的请求从「原请求路径」中剔除，将不匹配的字符串拼接到 proxy_pass 后生成「新请求路径」，然后将「新请求路径」转交给其他地址。
>
> 案例 2 中，`proxy_pass` 的 ip:port 后接了字符串，因此将 location 的 `/frx/` 从原请求路径 `/frx/xu` 中剔除，变为 `xu`，然后将 `xu` 拼接到 `http://192.168.1.48/bing` 后生成了新请求，因此其他地址收到的请求就是 `/bingxu`。

| 案例 | localtion | proxy_pass                  | 匹配      |
| ---- | --------- | --------------------------- | --------- |
| 1    | /frx      | http://192.168.199.27/bing  | /bing/xu  |
| 2    | /frx/     | http://192.168.199.27/bing  | /bingxu   |
| 3    | /frx      | http://192.168.199.27/bing/ | /bing//xu |
| 4    | /frx/     | http://192.168.199.27/bing/ | /bing/xu  |





## 五、安全控制

### 1、安全隔离

通过代理分开了客户端到应用程序服务器端的连接，实现了安全措施。在反向代理之前设置防火墙，仅留一个入口供代理服务器访问。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411221246144.png" style="zoom:80%;" />



### 2、SSL加密

- HTTPS 是一种通过计算机网络进行安全通信的传输协议。它经由 HTTP 进行通信，利用 SSL/TLS 建立全通信，加密数据包，确保数据的安全性。

- Nginx 默认不支持 https 开头的协议，如果要想使用 SSL，需要添加 `--with-http_ssl_module`模块，而该模块在编译的过程中又需要 OpenSSL 的支持。



**ssl**

`ssl` 指令用于在指定的服务器开启 HTTPS，默认关闭。也可以使用 `listen 443 ssl`开启

| 语法            | 默认值   | 位置         |
| --------------- | -------- | ------------ |
| ssl <on \|off>; | ssl off; | http、server |

> ssl 默认监听的是 443 端口，所以使用下面的指令和 `ssl on` 效果一致

```nginx
server{
	listen 443 ssl;
}
```



**ssl_certificate**

`ssl_certificate` 指令是为当前这个虚拟主机指定一个带有 PEM 格式证书的证书。

| 语法                    | 默认值 | 位置         |
| ----------------------- | ------ | ------------ |
| ssl_certificate <file>; | —      | http、server |



**ssl_certificate_key**

`ssl_certificate_key` 指令用来指定 PEM secret key 文件的路径

| 语法                         | 默认值 | 位置         |
| ---------------------------- | ------ | ------------ |
| ssl_ceritificate_key <file>; | —      | http、server |



**ssl_session_cache**

`ssl_session_cache` 指令用来配置用于 SSL 会话的缓存

| 语法                                                         | 默认值                  | 位置         |
| ------------------------------------------------------------ | ----------------------- | ------------ |
| ssl_sesion_cache <off \| none \| [builtin[:size]] [shared:name:size]> | ssl_session_cache none; | http、server |

- `off`：严格禁止使用会话缓存：Nginx 明确告诉客户端会话不能被重用
- `none`：禁止使用会话缓存，Nginx 告诉客户端会话可以被重用，但实际上并不在缓存中存储会话参数
- `builtin`：内置 OpenSSL 缓存，仅在一个工作进程中使用。缓存大小在会话中指定。如果未给出大小，则等于 20480 个会话。使用内置缓存可能会导致内存碎片
- `shared`：所有工作进程之间共享缓存，缓存的相关信息用 name 和 size 来指定，同 name 的缓存可用于多个虚拟服务器。name 是允许缓存的数据名，size 是允许缓存的数据大小，以字节为单位

```nginx
ssl_session_cache builtin:1000 shared:SSL:10m; # m是兆
```



**ssl_session_timeout**

`ssl_session_timeout` 指令用于开启 SSL 会话功能后，设置客户端能够反复使用储存在缓存中的会话参数时间，默认值超时时间是 5 秒

| 语法                        | 默认值                  | 位置         |
| --------------------------- | ----------------------- | ------------ |
| ssl_session_timeout <time>; | ssl_session_timeout 5m; | http、server |



**ssl_ciphers**

`ssl_ciphers` 指令指出允许的密码，密码指定为 OpenSSL 支持的格式

| 语法                   | 默认值                        | 位置         |
| ---------------------- | ----------------------------- | ------------ |
| ssl_ciphers <ciphers>; | ssl_ciphers HIGH:!aNULL:!MD5; | http、server |



**ssl_prefer_server_ciphers**

`ssl_prefer_server_ciphers` 指令指定是否服务器密码优先客户端密码，默认关闭，建议开启。

| 语法                                   | 默认值                         | 位置         |
| -------------------------------------- | ------------------------------ | ------------ |
| ssl_perfer_server_ciphers <on \| off>; | ssl_perfer_server_ciphers off; | http、server |



### 3、SSL实例模板

**Nginx 配置文件**

```nginx
server {
    listen       80;
    # ......
}
server {
    listen       443 ssl;		# 开启 SSL 功能
    server_name  localhost;     # 如果是购买的域名，这里加上该域名

    ssl_certificate      /root/cert/server.cert; # 生成的 cert 或者 pem 证书路径，根据需求修改
    ssl_certificate_key  /root/cert/server.key; # 生成的 key 证书路径，根据需求修改
    ssl_session_timeout 5m; 
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4; # 表示使用的加密套件的类型
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;  # 表示使用的TLS协议的类型
    ssl_prefer_server_ciphers on;

    location / {
        root   html;
        index  index.html index.htm;
    }
}
```

配置完 SSL 证书后，如果直接输入 `www.frx.com`，会默认加上` http:// ` 而不是`https:// `，可以使用`Rewrite`修改为`https:// `

```nginx
server {
    listen 443 ssl;
    server_name www.frx.com;   # 如果是 www.frx.com 发送请求
    
    location / {
        # ......
        rewrite ^(.*)$ https://www.frx.com$1;  # 则改为 https 方式
        # ......
	}
    # ......
}
```



### 4、反向代理系统调优

反向代理值 `Buffer` 和 `Cache`

**相同点**

- 两种方式都是用来提供IO吞吐效率，都是用来提升Nginx代理的性能。

**不同点**

- 缓冲主要用来解决不同设备之间数据传递速度不一致导致的性能低的问题，缓冲中的数据一旦此次操作完成后，就可以删除。
- 缓存主要是备份，将被代理服务器的数据缓存一份到代理服务器，这样的话，客户端再次获取相同数据的时候，就只需要从代理服务器上获取，效率较高，缓存中的数据可以重复使用，只有满足特定条件才会删除.



**Proxy Buffer 相关指令**

`proxy_buffering` ：用来开启或者关闭代理服务器的缓冲区，默认开启。

| 语法                         | 默认值              | 位置                   |
| ---------------------------- | ------------------- | ---------------------- |
| proxy_buffering <on \| off>; | proxy_buffering on; | http、server、location |



`proxy_buffers` ：指定单个连接从代理服务器读取响应的缓存区的个数和大小。

| 语法                           | 默认值                                    | 位置                   |
| ------------------------------ | ----------------------------------------- | ---------------------- |
| proxy_buffers <number> <size>; | proxy_buffers 8 4k \| 8K;(与系统平台有关) | http、server、location |

- number：缓冲区的个数
- size：每个缓冲区的大小，缓冲区的总大小就是 number * size



`proxy_buffer_size` ：设置从被代理服务器获取的第一部分响应数据的大小。保持与 proxy_buffers 中的 size 一致即可，也可以更小。

| 语法                      | 默认值                                      | 位置                   |
| ------------------------- | ------------------------------------------- | ---------------------- |
| proxy_buffer_size <size>; | proxy_buffer_size 4k \| 8k;(与系统平台有关) | http、server、location |



`proxy_busy_buffers_size` ：限制同时处于 BUSY 状态的缓冲总大小。

| 语法                            | 默认值                             | 位置                   |
| ------------------------------- | ---------------------------------- | ---------------------- |
| proxy_busy_buffers_size <size>; | proxy_busy_buffers_size 8k \| 16K; | http、server、location |



`proxy_temp_path` ：当缓冲区存满后，仍未被 Nginx 服务器完全接受，响应数据就会被临时存放在磁盘文件上（该指令设置文件路径，path 最多设置三层）

| 语法                    | 默认值                      | 位置                   |
| ----------------------- | --------------------------- | ---------------------- |
| proxy_temp_path <path>; | proxy_temp_path proxy_temp; | http、server、location |



`proxy_temp_file_write_size` ：设置磁盘上缓冲文件的大小。

| 语法                               | 默认值                                | 位置                   |
| ---------------------------------- | ------------------------------------- | ---------------------- |
| proxy_temp_file_write_size <size>; | proxy_temp_file_write_size 8K \| 16K; | http、server、location |





## 六、负载均衡

系统的扩展可以分为纵向扩展和横向扩展。

- 纵向扩展是从单机的角度出发，通过增加系统的硬件处理能力来提升服务器的处理能力
- 横向扩展是通过添加机器来满足大型网站服务的处理能力

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252207141.png" style="zoom:57%;" />

负载均衡涉及到两个重要的角色分别是**应用集群**和**负载均衡器**

- 应用集群：将同一应用部署到多台机器上，组成处理集群，接收负载均衡设备分发的请求，进行处理并返回响应的数据
- 负载均衡器：将用户访问的请求根据对应的负载均衡算法，分发到集群中的一台服务器进行处理

**负载均衡作用**

- 解决服务器的高并发压力，提高应用程序的处理性能
- 提供故障转移，实现高可用
- 通过添加或减少服务器数量，增强网站的可扩展性
- 在负载均衡器上进行过滤，可以提高系统的安全性



### 1、负载均衡常用方式

#### 1.1 用户手动选择

> 这种方式比较原始，主要实现的方式就是在网站主页上面提供不同线路、不同服务器链接方式，让用户来选择自己访问的具体服务器，来实现负载均衡。
>
> 如下图，用户点击不同的下载方式，就会跳转到不同的下载地址。这是主动式的负载均衡，我们无法控制用户的选择。如果用户全部点击第一个下载方式，那么服务器的压力将非常大。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252210202.png)



#### 1.2 DNS轮询

- DNS是一种分布式网络目录服务，主要用于域名与 IP 地址的相互转换。

- 大多域名注册商都支持对同一个域名添加多条记录（IP），这就是 DNS 轮询，DNS 服务器将解析请求按照 记录的顺序，随机分配到不同的 IP 上，这样就能完成简单的负载均衡。DNS 轮询的成本非常低，在一些不重要的服务器，被经常使用。

> 客户端如果想访问服务器集群，首先去 DNS 服务器获取我们曾经在 DNS 服务器保存的「记录表」，这个「记录表」将会把某个服务器的地址返回给客户端，客户端再根据这个地址，访问指定的服务器。这个「记录表」在开始期间需要我们去 DNS 服务器进行添加

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252218691.png" style="zoom:50%;" />

记录表如下所示，主机记录 www，添加了2个IP地址，用 2 台服务器来做负载均衡。其中两个记录值都绑定了 `www.nginx521.cn` 地址。(一个域名可以绑定多个IP地址)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252219098.png)

使用 DNS 来实现轮询，不需要投入过多的成本，但是 DNS 负载均衡存在明显的缺点：

- 可靠性低：假设一个域名 DNS 轮询多台服务器，如果其中的一台服务器发生故障，那么所有的访问该服务器的请求将不会有所回应，即使你将该服务器的 IP 从 DNS 中去掉，但是由于各大宽带接入商将众多的 DNS 存放在缓存中，以节省访问时间，导致 DNS 不会实时更新。所以 DNS 轮流上一定程度上解决了负载均衡问题，但是却存在可靠性不高的缺点。

- 负载均衡不均衡：DNS 负载均衡采用的是简单的轮询负载算法，不能区分服务器的差异，不能反映服务器的当前运行状态，不能做到为性能好的服务器多分配请求，另外本地计算机也会缓存已经解析的域名到 IP 地址的映射，这也会导致使用该 DNS 服务器的用户在一定时间内访问的是同一台 Web 服务器，从而引发 Web 服务器减的负载不均衡。



#### 1.3 四/七层负载均衡

OSI七层网络模型如下

> - 应用层：为应用程序提供网络服务。
> - 表示层：对数据进行格式化、编码、加密、压缩等操作
> - 会话层：建立、维护、管理会话连接
> - 传输层：建立、维护、管理端到端的连接，常见的有 TCP/UDP
> - 网络层：IP 寻址和路由选择
> - 数据链路层：控制网络层与物理层之间的通信
> - 物理层：比特流传输

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252223497.png" style="zoom:80%;" />

**四层负载均衡**

四层负载均衡指的是 OSI 七层模型中的**传输层**，主要是基于 **IP + PORT** 的负载均衡

实现四层负载均衡的方式：

- 硬件：F5 BIG-IP、Radware 等，性能好，成本高、无法扩展
- 软件：LVS、Nginx、Hayproxy 等，性能较好，成本低、可以扩展

**七层负载均衡**

七层负载均衡指的是在**应用层**，主要是基于虚拟的 URL 或主机 IP 的负载均衡

实现七层负载均衡的方式：

- 软件：Nginx、Hayproxy 等

**四层和七层负载均衡的区别**

- 四层负载均衡的数据包在底层进行分发，七层负载均衡的数据包在最顶端进行分发，所以四层负载均衡的效率比七层负载均衡的要高

  > 四层比七层少，速度块，效率高，但是可能请求丢失

- 四层负载均衡不识别域名，而七层负载均衡识别域名

- 除了四层和七层负载以外，其实还有二层、三层负载均衡，二层是在数据链路层基于 mac 地址来实现负载均衡，三层是在网络层一般采用虚拟 IP 地址的方式实现负载均衡。

- 实际环境采用的是：四层负载(LVS) + 七层负载(Nginx)



### 2、七层负载均衡

> Nginx 要实现七层负载均衡需要用到 `proxy_pass` 代理模块配置。Nginx 默认安装支持这个模块，Nginx 的负载均衡是在 Nginx 反向代理的基础上把用户的请求根据指定的算法分发到一组`upstream` 虚拟服务池

#### 2.1 upstream

定义一组服务器，它们可以是监听不同端口的服务器，也可以是同时监听 TCP 和 Unix socket 的服务器。服务器可以指定不同的权重，默认为 1

| 语法                  | 默认值 | 位置 |
| --------------------- | ------ | ---- |
| upstream <name> {...} | —      | http |



#### 2.2 server

用来指定后端服务器的名称和一些参数，可以使用域名、IP、端口或者 Unix socket。server 后的 name 就是 upstream 后的 name，两者保持一致

| 语法                        | 默认值 | 位置     |
| --------------------------- | ------ | -------- |
| server <name> [paramerters] | —      | upstream |



#### 2.3 七层负载均衡案例

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252233574.png" style="zoom:60%;" />

**服务器设置**

以三个端口代替三个服务器

```nginx
# 服务器 1
server {
    listen   9001;
    server_name localhost;
    default_type text/html;
    location /{
    	return 200 '<h1>192.168.200.146:9001</h1>';
    }
}
# 服务器 2
server {
    listen   9002;
    server_name localhost;
    default_type text/html;
    location /{
    	return 200 '<h1>192.168.200.146:9002</h1>';
    }
}
# 服务器 3
server {
    listen   9003;
    server_name localhost;
    default_type text/html;
    location / {
    	return 200 '<h1>192.168.200.146:9003</h1>';
    }
}
```

**负载均衡器设置**

```nginx
upstream backend{
	server 192.168.200.146:9091;
	server 192.168.200.146:9092;
	server 192.168.200.146:9093;
}
server {
	listen 8083;
	server_name localhost;
	location / {
		proxy_pass http://backend;   # backend 要对应上 upstream 后的值，根据需求修改
	}
}
```

访问负载均衡器的地址，如 `http://192.168.200.133:8083`，它会找到 `proxy_pass` 后的地址，根据`backend`找到对应的`upstream`，通过负载均衡算法选择一个服务器进行访问



#### 2.4 七层负载均衡状态

代理服务器在负责均衡调度中的状态有如下几个

| 状态         | 概述                                |
| ------------ | ----------------------------------- |
| down         | 当前的 server 暂时不参与负载均衡    |
| backup       | 预留的备份服务器                    |
| max_fails    | 允许请求失败的次数                  |
| fail_timeout | 经过 max_fails 失败后，服务暂停时间 |
| max_conns    | 限制最大的接收连接数                |



##### 2.4.1 down

`down` 指令将该服务器标记为永久不可用，那么负载均衡器将不参与该服务器的负载均衡。该状态一般会对需要停机维护的服务器进行设置

```nginx
upstream backend{
	server 192.168.200.146:9001 down;
	server 192.168.200.146:9002
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location / {
		proxy_pass http://backend;
	}
}
```



##### 2.4.2 backup

`backup` 指令将该服务器标记为备份服务器，当主服务器不可用时，才使用备份服务器来传递请求。` backp` 是临时禁止，当主服务器不可用后，临时禁止的服务器就会站出来。

```nginx
upstream backend{
	server 192.168.200.146:9001 down;
	server 192.168.200.146:9002 backup;
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```

>  9001 服务器永久禁止，而 9002 服务器是备份服务器，所以 9003 服务器是主服务器



##### 2.4.3 max_conns

`max_conns` 指令用来限制同时连接到 `upstream` 负载上的单个服务器的最大连接数。默认为 0，表示不限制，使用该配置可以根据后端服务器处理请求的并发量来进行设置，防止后端服务器被压垮。

| 语法               | 默认值 | 位置     |
| ------------------ | ------ | -------- |
| max_conns=<number> | 0      | upstream |

```nginx
upstream backend{
	server 192.168.200.146:9001 down;
	server 192.168.200.146:9002 backup;
	server 192.168.200.146:9003 max_conns=2; # 最大能被 2 个客户端请求
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



##### 2.4.4 max_fails/fail_timeout

`max_fails` ：设置允许请求代理服务器失败的次数，默认为 1

`fail_timeout` ：经过 `max_fails` 次失败后，服务暂停的时间，默认是 10 秒

| 语法                | 默认值 | 位置     |
| ------------------- | ------ | -------- |
| max_fails=<number>  | 1      | upstream |
| fail_timeout=<time> | 10 秒  | upstream |

```nginx
upstream backend{
	server 192.168.200.133:9001 down;
	server 192.168.200.133:9002 backup;
	server 192.168.200.133:9003 max_fails=3 fail_timeout=15;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



#### 2.5 七层负载均衡策略

| 算法名称   | 说明              |
| ---------- | ----------------- |
| 轮询       | 默认方式          |
| weight     | 权重方式          |
| ip_hash    | 依据 IP 分配方式  |
| least_conn | 依据最少连接方式  |
| url_hash   | 依据 URL 分配方式 |
| fair       | 依据响应时间方式  |



##### 2.5.1 轮询

这是 `upstream` 模块负载均衡默认的策略。每个请求会按时间顺序逐个分配到不同的后端服务器。轮询不需要额外的配置

```nginx
upstream backend{
	server 192.168.200.146:9001;
	server 192.168.200.146:9002;
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



##### 2.5.2 weight加权

`weight` 指令用来设置服务器的权重，默认为 1，权重数据越大，被分配到请求的几率越大；该权重值，主要是针对实际工作环境中不同的后端服务器硬件配置进行调整的，所有此策略比较适合服务器的硬件配置差别比较大的情况。

| 语法            | 默认值 | 位置     |
| --------------- | ------ | -------- |
| weight=<number> | 1      | upstream |

```nginx
upstream backend{
	server 192.168.200.146:9001 weight=10;
	server 192.168.200.146:9002 weight=5;
	server 192.168.200.146:9003 weight=3;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



##### 2.5.3 ip_hash

`ip_hash` 指令能够将某个客户端 IP 的请求通过哈希算法定位到同一台后端服务器上。这样，当来自某一个 IP 的用户在后端 Web 服务器 A 上登录后，在访问该站点的其他 URL，能保证其访问的还是后端 Web 服务器 A

```nginx
upstream backend{
	ip_hash;
	server 192.168.200.146:9001;
	server 192.168.200.146:9002;
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



##### 2.5.4 least_conn

有些请求占用的时间很长，会导致其所在的后端负载较高。这种情况下，`least_conn` 会把请求转发给连接数较少的后端服务器，达到更好的负载均衡效果。

```nginx
upstream backend{
	least_conn;
	server 192.168.200.146:9001;
	server 192.168.200.146:9002;
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



##### 2.5.5 url_hash

按访问 URL 的 hash 结果来分配请求，使每个 URL 定向到同一个后端服务器，配合缓存命中来使用。

> - 当出现同一个资源多次请求，可能会到达不同的服务器上，导致不必要的多次下载，缓存命中率不高，以及一些资源时间的浪费时，使用 `url_hash`，可以使得同一个 URL（也就是同一个资源请求）会到达同一台服务器，一旦缓存住了资源，再此收到请求，就可以从缓存中读取。
>
> - 发送相同的请求时，希望只有一个服务器处理该请求，使用 `uri_hash`。因为 URL 相同，则哈希值相同，那么哈希值对应的服务器也相同。

```nginx
upstream backend{
	hash &request_uri;
	server 192.168.200.146:9001;
	server 192.168.200.146:9002;
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



##### 2.5.6 fair

`fair` 指令可以根据页面大小、加载时间长短智能的进行负载均衡。但使用的是第三方模块的 fair 负载均衡策略，需要添加 `nginx-upstream-fair` 模块

```nginx
nupstream backend{
	fair;
	server 192.168.200.146:9001;
	server 192.168.200.146:9002;
	server 192.168.200.146:9003;
}
server {
	listen 8083;
	server_name localhost;
	location /{
		proxy_pass http://backend;
	}
}
```



### 3、四层负载均衡

Nginx 在 1.9 之后，增加了一个 stream 模块，用来实现四层协议的转发、代理、负载均衡等。stream 模块的用法跟 http 的用法类似，允许我们配置一组 TCP 或者 UDP 等协议的监听，然后通过 proxy_pass 来转发我们的请求，通过 upstream 添加多个后端服务，实现负载均衡。

> 四层协议负载均衡的实现，一般都会用到 LVS、HAProxy、F5 等，要么很贵要么配置很麻烦，而 Nginx 的配置相对来说更简单，更能快速完成工作。Nginx 默认是没有编译这个模块的，需要使用到 stream 模块，那么需要在编译的时候加上 `--with-stream`



#### 3.1 stream

| 语法           | 默认值 | 位置 |
| -------------- | ------ | ---- |
| stream { ... } | —      | main |

```nginx
http {
    server {
        listen 80;
        # ......
    }
}
stream {
    upstream backend{
        server 192.168.200.146:6379;
        server 192.168.200.146:6378;
    }
    server {
        listen 81;
        proxy_pass backend;
    }
}
```



#### 3.2 upstream

该指令和七层负载均衡的 upstream 指令是类似的



#### 3.3 四层负载均衡案例

准备两台服务器，服务器 A 的 IP 为 `192.168.200.146`，服务器 B 的IP 为 `192.168.200.133`，服务器 A 存放 Redis 和 Tomcat，服务器 B 作为负载均衡器，对服务器 A 的端口进行负载均衡处理

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202411252255811.png" style="zoom:50%;" />

```nginx
stream {
    upstream redisbackend {
        server 192.168.200.146:6379;    # 服务器 B 的 6379 端口
        server 192.168.200.146:6378;    # 服务器 B 的 6378 端口
    }
    upstream tomcatbackend {
        server 192.168.200.146:8080;   # 服务器 B 的 8080 端口
    }
    server {
        listen  81;
        proxy_pass redisbackend; # redis 的负载均衡
    }
    server {
        listen	82;
        proxy_pass tomcatbackend;  # tomcat 的负载均衡
    }
}
```





## 七、Nginx缓存

> Nginx作为Web缓存服务器，介于客户端和服务器之间，当用户通过浏览器访问一个URL时，web缓存服务器会去应用服务器获取要展示给用户的内容，将内容缓存到自己的服务器上，当下一次请求到来时，如果访问的是同一个URL，web缓存服务器就会直接将之前缓存的内容返回给客户端，而不是向应用服务器再次发送请求。web缓存降低了应用服务器、数据库的负载，减少了网络延迟，提高了用户访问的响应速度，增强了用户的体验。

Nginx从0.7.48版本开始提供缓存功能。Nginx是基于`Proxy Store`来实现的，其原理是把URL及相关组合当做Key，再使用MD5算法对Key进行哈希，得到硬盘上对应的哈希目录路径，从而将缓存内容保存在该目录中。它可以支持任意URL连接，同时也支持404/301/302这样的非200状态码。Nginx既可以支持对指定URL或者状态码设置过期时间，也可以使用`purge`命令来手动清除指定URL的缓存。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411282319142.png)



### 1、缓存相关指令

Nginx 的 Web 缓存服务主要是使用 `ngx_http_proxy_module` 模块相关指令集来完成，接下来我们把常用的指令来进行介绍下。

#### 1.1 proxy_cache_path

该指定用于设置缓存文件的存放路径

| 语法                                                         | 默认值 | 位置 |
| ------------------------------------------------------------ | ------ | ---- |
| proxy_cache_path path [levels=number] <br/>keys_zone=zone_name:zone_size [inactive=time]\[max_size=size]; | —      | http |

- `path`：缓存路径地址，例如`/usr/local/proxy_cache`

- `levels`：指定该缓存空间 `path` 基础上新建的目录，最多可以设置 3 层，每层取 1 到 2 个字母作为目录名

  ```nginx
  levels=1:2   缓存空间有两层目录，第一次是1个字母，第二次是2个字母
  举例说明:
  abcde[key]       通过MD5加密以后的值为 43c8233266edce38c2c9af0694e2107d
  levels=1:2   	 最终的存储路径为/usr/local/proxy_cache/d/07
  levels=2:1:2 	 最终的存储路径为/usr/local/proxy_cache/7d/0/21
  levels=2:2:2 	 最终的存储路径为/usr/local/proxy_cache/7d/10/e2
  ```

- `keys_zone`：为缓存区设置名称和指定大小

  ```nginx
  keys_zone=kele:200m  # 缓存区的名称是 kele，大小为 200M，1M 大概能存储 8000 个 keys
  ```

- `inactive`：指定的时间内未访问的缓存数据会从缓存中删除，默认情况下，`inactive` 设置为 10 分钟

  ```nginx
  inactive=1d   # 缓存数据在 1 天内没有被访问就会被删除
  ```

- `max_size`：设置最大缓存空间，如果缓存空间存满，默认会覆盖缓存时间最长的资源，默认单位为兆

  ```nginx
  max_size=20g    # 最大缓存空间为 20G
  ```

**实例配置**

```nginx
http{
	proxy_cache_path /usr/local/proxy_cache keys_zone=kele:200m levels=1:2:1 inactive=1d max_size=20g;
}
```



#### 1.2 proxy_cache

该指令用来开启或关闭代理缓存，如果是开启则指定使用哪个缓存区来进行缓存。默认关闭。

| 语法                            | 默认值           | 位置                   |
| ------------------------------- | ---------------- | ---------------------- |
| proxy_cache <zone_name \| off>; | proxy_cache off; | http、server、location |

- `zone_name`：指定使用缓存区的名称

  > 缓存区的名称必须是 `proxy_cache_path` 里的 `keys_zone` 生成的缓存名



#### 1.3 proxy_cache_key

该指令用来设置 Web 缓存的 key 值，Nginx 会根据 key 值利用 MD5 计算出哈希值并缓存起来，作为缓存目录名的参考。

| 语法                   | 默认值                                  | 位置                   |
| ---------------------- | --------------------------------------- | ---------------------- |
| proxy_cache_key <key>; | proxy_cache_key proxy_host$request_uri; | http、server、location |



#### 1.4 proxy_cache_valid

该指令用来对不同返回状态码的 URL 设置不同的缓存时间

| 语法                                     | 默认值 | 位置                   |
| ---------------------------------------- | ------ | ---------------------- |
| proxy_cache_valid [code ...... ] <time>; | —      | http、server、location |

```nginx
proxy_cache_valid 200 302 10m; # 为 200 和 302 的响应 URL 设置 10 分钟缓存时间
proxy_cache_valid 404 1m;      # 为 404 的响应 URL 设置 1 分钟缓存时间
proxy_cache_valid any 1m;      # 对所有响应状态码的URL都设置 1 分钟缓存时间
```



#### 1.5 proxy_cache_min_uses

该指令用来设置资源被访问多少次后才会被缓存。默认是 1 次。

| 语法                           | 默认值                  | 位置                   |
| ------------------------------ | ----------------------- | ---------------------- |
| proxy_cache_min_uses <number>; | proxy_cache_min_uses 1; | http、server、location |



#### 1.6. proxy_cache_methods

设置缓存哪些 HTTP 方法的请求资源。默认缓存 HTTP 的 GET 和 HEAD 方法的请求资源，不缓存 POST 方法的请求资源

| 语法                                       | 默认值                        | 位置                   |
| ------------------------------------------ | ----------------------------- | ---------------------- |
| proxy_cache_methods <GET \| HEAD \| POST>; | proxy_cache_methods GET HEAD; | http、server、location |



#### 1.7 案例

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202411282347379.png)

**nginx配置**

```nginx
http{
    proxy_cache_path /usr/local/proxy_cache levels=2:1 keys_zone=bing:200m inactive=1d max_size=20g;
    upstream backend{
        server 192.168.200.146:8080;   # 服务器 A 的地址
    }
    server {
        listen       8080;     			# 监听 8080 端口
        server_name  localhost; 		# 监听 localhost 的IP
        location / {					# 监听包含 / 的请求
            proxy_cache bing;    		# 开启 bing 缓存区，和第 2 行的 keys_zone 对应
            proxy_cache_key kele;  		# 缓存的 key 值，会被 MD5 解析成字符串用于生成缓存的目录
            proxy_cache_min_uses 5; 	# 资源被访问 5 次后才会被缓存
            proxy_cache_valid 200 5d;	# 为 200 响应 URL 设置 5 天缓存时间
            proxy_cache_valid 404 30s;  # 为 404 的响应 URL 设置 30 秒缓存时间
            proxy_cache_valid any 1m;	# 为除了上方的任意响应 URL 设置 1 分钟缓存时间
            add_header nginx-cache "$upstream_cache_status";  # 将缓存的状态放到请求头里
            proxy_pass http://backend/js/;  # 代理 backend，将 /js/ 追加到 backend 模块里的地址后面
        }
    }
}
```



### 2、Nginx缓存清除

#### 2.1 删除缓存目录

```
rm -rf /usr/local/proxy_cache/......
```



#### 2.2 ngx_cache_purge

使用第三方扩展模块 `ngx_cache_purge` 进行删除缓存。安装nginx的时候需要安装`ngx_cache_purge`模块。然后在nginx中进行如下配置

```nginx
server{
    location ~/purge(/.*) {
        proxy_cache_purge bing kele;
    }
}
```

**`proxy_cache_purge` 指令**

| 语法                            | 默认值 | 位置                   |
| ------------------------------- | ------ | ---------------------- |
| proxy_cache_purge <cache> <key> | -      | http、server、location |

- `cache` 是 `proxy_cache`，需要删除的缓冲区名称
- `key` 是 `proxy_cache_key`，缓存的key值



### 3、Nginx资源不缓存

对于一些经常发生变化的数据，如果进行缓存的话，就很容易出现用户访问到的数据不是服务器真实的数据。所以对于这些资源我们在缓存的过程中就需要进行过滤，不进行缓存。

Nginx 可以使用如下两个指令设置

- `proxy_no_cache`
- `proxy_cache_bypass`



#### 3.1 proxy_no_cache

该指令是用来定义不将数据进行缓存的条件，也就是不缓存指定的数据

| 语法                             | 默认值 | 位置                   |
| -------------------------------- | ------ | ---------------------- |
| proxy_no_cache <string> ...... ; | —      | http、server、location |

可以设置多个 string

```nginx
proxy_no_cache $cookie_nocache $arg_nocache $arg_comment;
```



#### 3.2 proxy_cache_bypass

该指令是用来设置不从缓存中获取数据的条件，虽然缓存了指定的资源，但请求过来也不会去获取它，而是去服务器里获取资源。

| 语法                                 | 默认值 | 位置                   |
| ------------------------------------ | ------ | ---------------------- |
| proxy_cache_bypass <string> ...... ; | —      | http、server、location |

可设置多个 string

```nginx
proxy_cache_bypass $cookie_nocache $arg_nocache $arg_comment;
```



> 上述两个指令都有一个指定的条件，这个条件可以是多个，**并且多个条件中至少有一个不为空且不等于「0」，则条件满足成立。**



#### 3.3 常用不缓存变量

- `$cookie_nocache`：当前请求的 cookie 中 key 为 nocache 的 value 值
- `$arg_nocache`：当前请求的参数中属性名为 nocache 对应的属性值
- `$arg_comment`：当前请求的参数中属性名为 comment 对应的属性值

**案例**

- 如果访问的是 js 文件，则不会缓存该 js 文件
- 如果 `$nocache` `$cookie_nocache` `$arg_nocache` `$arg_comment` 任意不为空或 0，则访问的资源不进行缓存

```nginx
server {
    listen	8080;
    server_name localhost;
    location / {
        if ($request_uri ~ /.*\.js$){
            set $nocache 1;
        }
        proxy_no_cache $nocache $cookie_nocache $arg_nocache $arg_comment;
        proxy_cache_bypass $nocache $cookie_nocache $arg_nocache $arg_comment;
    }
}
```





## 八、集群部署

Nginx 在高并发场景和处理静态资源是非常高性能的，但是在实际项目中除了静态资源还有就是后台业务代码模块，一般后台业务都会被部署在 Tomcat、weblogic 或者是 websphere 等 Web 服务器上。因此可以使用 Nginx 接收用户的请求并把请求转发到后台 Web 服务器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021501066.png)

直接通过 Tomcat 就可以访问，而多加一个 Nginx的好处如下：

- 使用 Nginx 实现动静分离
- 使用 Nginx 搭建 Tomcat 的集群



### 1、动静分离

> - 动：后台应用程序的业务处理
> - 静：网站的静态资源（html，javaScript，css，images 等文件）
> - 分离：将两者进行分开部署访问，提供用户进行访问。
>
> **所有和静态资源相关的内容都交给 Nginx 来部署访问，非静态内容则交给 Tomcat 服务器来部署访问。**Nginx 在处理静态资源的时候，效率是非常高的， Tomcat 则相对比较弱一些，把静态资源交给 Nginx 后，可以减轻 Tomcat 服务器的访问压力并提高静态资源的访问速度。实现动静分离的方式很多，比如静态资源可以部署到 CDN、Nginx 等服务器上，动态资源可以部署到 Tomcat、weblogic 或者 websphere 上



#### 1.1 需求分析

因为 Nginx 处理静态资源性能高，所以我们把静态资源放在 Nginx 服务器上，然后把动态资源放到 Tomcat 服务器上。当访问 Nginx 的静态资源时，Nginx 会去访问 Tocmat 获取动态资源。实现动静分离。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021507414.png" style="zoom:50%;" />

**实现步骤**

1、将 Tomcat上demo.war 项目中的静态资源（两个图片）都删除掉，重新打包生成一个 War 包。这时候 War 包只留下动态资源，而静态资源要放到 Nginx 上

2、将新的 War 包部署到 Tomcat 中，把之前部署的内容删除掉

- 进入到 tomcat 的 webapps 目录下，将之前的 demo 目录和 demo.war 包删除掉
- 将新的 War 包复制到 webapps 下
- 将 tomcat 启动

- 在 Nginx 所在的服务器 B 上创建如下目录，并将对应的静态资源放入指定的位置

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021509473.png)

  ```html
  <!-- index.html的内容 -->
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script src="js/jquery.min.js"></script>
      <script>
          $(function(){
             $.get('http://192.168.200.133/demo/getAddress',function(data){
                 $("#msg").html(data);
             });
          });
      </script>
  </head>
  <body>
      <img src="images/logo.png"/>
      <h1>Nginx如何将请求转发到后端服务器</h1>
      <h3 id="msg"></h3>
      <img src="images/mv.png"/>
  </body>
  </html>
  ```

3、Nginx配置文件

```nginx
upstream webservice{
    server 192.168.200.146:8080;  # 服务器 A 的 Tocmat
}
server {
    listen       80;
    server_name  localhost;

    # 动态资源从 Tomcat 获取
    location /demo {     		 # index.html 第 9 行代码触发该 location
        proxy_pass http://webservice;
    }
    # 静态资源从自己身上获取
    location ~/.*\.(png|jpg|gif|js){
        root html/web;
        gzip on;
    }

    location / {
        root   html/web;
        index  index.html index.htm;
    }
}
```

4、启动测试，访问 `http://192.168.200.133`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021513915.png)如果 Tomcat 服务器宕机了，我们再次访问 Nginx，会得到如下效果

> 用户还是能看到页面，只是缺失 Tomcat 的动态资源，这就是前后端耦合度降低的效果，并且整个请求只和后的服务器交互了一次，js 和 images 都直接从 Nginx 服务器里返回，提供了效率，降低了后端服务器的压力

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021514614.png)



### 2、Nginx集群

需要两台以上的 Nginx 服务器对外提供服务，这样的话就可以解决其中一台宕机了，另外一台还能对外提供服务，但是如果是两台 Nginx 服务器的话，会有两个 IP 地址，用户该访问哪台服务器，用户怎么知道哪台是好的，哪台是宕机了的？



#### 2.1 Keepalived

Keepalived 软件由 C 编写的，最初是专为 LVS 负载均衡软件设计的，Keepalived 软件主要是通过 VRRP 协议实现高可用功能。



#### 2.2 VRRP介绍

> VRRP（Virtual Route Redundancy Protocol）协议，翻译过来为**虚拟路由冗余协议**。VRRP 协议将两台或多台路由器设备虚拟成一个设备，对外提供虚拟路由器 IP。在路由器组内部，实际拥有这个对外 IP 的路由器如果工作正常的话就是 MASTER，MASTER 实现针对虚拟路由器IP的各种网络功能。其他设备不拥有该虚拟 IP，状态为 BACKUP，除了接收 MASTER 的 VRRP 状态通告信息以外，不执行对外的网络功能。当主机失效时，BACKUP 将接管原先 MASTER 的网络功能。

如下图，VRRP 把两个 Nginx 分成两个路由（VRRP 路由 1 和 VRRP 路由 2），并生成一个 Virtual 路由，用户访问的是 Virtual 路由，该路由会去访问两个 Nginx 生成的 VRRP 路由。VRRP 会给两个路由分配角色，一个是 Master，另一个是 Backup，所以访问的是 Master 角色的路由，当 Master 角色路由宕机了，才会找到 Backup 备份路由。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021532788.png)

VRRP 是一种协议，这个协议有两个功能

- 选择协议：VRRP 可以把一个虚拟路由器的责任动态分配到局域网上的 VRRP 路由器中的一台。其中的虚拟路由即 Virtual 路由是由 VRRP 路由群组创建的一个不真实存在的路由，这个虚拟路由也是有对应的 IP 地址。而且 VRRP 路由 1 和 VRRP 路由 2 之间会有竞争选择，通过选择会产生一个 Master 路由和一个 Backup 路由

- 路由容错协议：Master 路由和 Backup 路由之间会有一个心跳检测，Master 会定时告知 Backup 自己的状态，如果在指定的时间内，Backup 没有接收到这个通知内容，Backup 就会替代 Master 成为新的 Master。Master 路由有一个特权就是虚拟路由和后端服务器都是通过 Master 进行数据传递交互的，而备份节点则会直接丢弃这些请求和数据，不做处理，只是去监听 Master 的状态



**用了 Keepalived 后，如下图所示**

> VIP 是虚拟路由，、一旦用户发送请求到 VIP，VIP 就会发送给 Master的 Nginx，如果 Master的Nginx 宕机了，才会发送给 Backup的Nginx 路由

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021536373.png)

#### 2.3 环境搭建

| VIP IP          | Nginx IP                    | 主机名      | 主/从  |
| --------------- | --------------------------- | ----------- | ------ |
|                 | 192.168.200.133（服务器 A） | keepalived1 | Master |
| 192.168.200.222 |                             |             |        |
|                 | 192.168.200.122（服务器 B） | keepalived2 | Backup |

确保服务器 A 和服务器 B 的 Nginx 配置保持一致



##### **1. keepalived 安装**

> 1、从官方网站下载 keepalived， [https://keepalived.org/(opens new window)](https://keepalived.org/)
>
> 2、将下载的资源上传到服务器，这里是 `keepalived-2.0.20.tar.gz`
>
> 3、在 `/opt` 目录下创建 keepalived 目录，方便管理资源
>
> 4、将压缩文件进行解压缩，解压缩到指定的目录
>
> ```sh
> tar -zxf keepalived-2.0.20.tar.gz -C /opt/keepalived
> ```
>
> 5、对 keepalived 进行配置，编译和安装
>
> ```sh
> cd /opt/keepalived/keepalived-2.0.20
> 
> ./configure --sysconf=/etc --prefix=/usr/local   # 安装到 /usr/local 目录下，可修改
> 
> make && make install
> ```

安装完成后，有两个文件

- `/etc/keepalived/keepalived.conf`：keepalived 的系统配置文件，主要操作的就是该文件
- `/usr/local/sbin/keepalived`：这是系统配置脚本，用来启动和关闭 keepalived



##### 2. 配置文件

keepalived.conf 配置文件中有三部分

- global 全局配置
- vrrp 相关配置
- LVS 相关配置

```nginx
# global全局部分
global_defs {
    
   notification_email {  # 通知邮件，当 keepalived 切换 Master 和 Backup 时需要发 email 给具体的邮箱地址
     tom@itcast.cn
     jerry@itcast.cn
   }
   notification_email_from kele@youngkbt.com   # 设置发件人的邮箱信息
   
   smtp_server 192.168.200.1   # 指定 smpt 服务地址
   
   smtp_connect_timeout 30   # 指定 smpt 服务连接超时时间
   
   router_id LVS_DEVEL   # 运行 keepalived 服务器的一个标识，可以用作发送邮件的主题信息
   
   # 默认是不跳过检查。检查收到的 VRRP 通告中的所有地址可能会比较耗时，设置此命令的意思是，如果通告与接收的上一个通告来自相同的 master 路由器，则不执行检查(跳过检查)
   vrrp_skip_check_adv_addr
   
   vrrp_strict    # 严格遵守 VRRP 协议
  
   vrrp_garp_interval 0   # 在一个接口发送的两个免费 ARP 之间的延迟。可以精确到毫秒级。默认是 0
   
   vrrp_gna_interval 0  # 在一个网卡上每组消息之间的延迟时间，默认为 0
}
```



VRRP 部分包含以下四个子模块

- vrrp_script

- vrrp_sync_group

- garp_group

- vrrp_instance

我们会用到`vrrp_script`和`vrrp_instance`

**vrrp_instance**

> 第 3 行代码是说明当前 Nginx 服务器的角色是 Master 还是 Backup。分别在服务器 A 和 B 进行角色配置。
>
> 第 5 行代码是 VIP 的 ID，如果使用相同的虚拟路由 VIP，请保持 ID 一致。
>
> 第 6 行代码是优先级，请让 Master 服务器的优先级大于 Backup 服务器的优先级。如 100 > 90。
>
> 第 7 行代码是 Master 和 Backup 之间通信的间隔时间，如果无法通信，说明 Master 已经宕机，则切换为 Backup。
>
> 第 13 行代码是用户访问的虚拟 IP 地址，即 VIP，它会发送给 Nginx 服务器

```nginx
# 设置 keepalived 实例的相关信息，VI_1 为 VRRP 实例名称
vrrp_instance VI_1 {
    state MASTER  		  # 有两个值可选 MASTER 主，BACKUP 备
    interface ens33		  # vrrp 实例绑定的接口，用于发送 VRRP 包[当前服务器使用的网卡名称]
    virtual_router_id 51  # 指定 VRRP 实例 ID，范围是 0-255
    priority 100		  # 指定优先级，优先级高的将成为 MASTER
    advert_int 1		  # 指定发送 VRRP 通告的间隔，单位是秒。这里是心跳检查的时间
    authentication {	  # vrrp 之间通信的认证信息
        auth_type PASS	  # 指定认证方式。PASS 简单密码认证(推荐)
        auth_pass 1111	  # 指定认证使用的密码，最多 8 位
    }
    virtual_ipaddress {   # 虚拟 IP 地址设置虚拟 IP 地址，供用户访问使用，可设置多个，一行一个
        192.168.200.222
    }
}
```



**服务器配置**

> Keepalived 的具体配置内容如下

**服务器A**

```nginx
global_defs {
   notification_email {
        tom@itcast.cn
        jerry@itcast.cn
   }
   notification_email_from zhaomin@itcast.cn
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   router_id keepalived1
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.200.222
    }
}
```

**服务器B**

```nginx
global_defs {
   notification_email {
        tom@itcast.cn
        jerry@itcast.cn
   }
   notification_email_from zhaomin@itcast.cn
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   router_id keepalived2
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.200.222
    }
}
```



##### 3. 访问测试

1、启动 keepalived 之前，先使用命令 `ip a`，查看 `192.168.200.133` 和 `192.168.200.122` 这两台服务器的 IP 情况

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021629987.png)

2、分别启动两台服务器的 keepalived，再次通过 `ip a` 查看 IP

> 此时发现服务器 A 多出了 `192.168.200.222`，正是配置的虚拟路由 VIP，而服务器 B 并没有，说明服务器 A 是 Master，优先级高于服务器 B。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021630000.png)

3、当把 `192.168.200.133` 服务器 A 上的 keepalived 关闭后，再次查看 IP

> 说明当 Master 服务器 A 宕机后，服务器 B 由 Backup 晋升为 Master。
>
> 通过上述的测试，我们会发现，虚拟 IP(VIP)会在 Master 节点上，当 Master 节点上的 keepalived 出问题以后，因为 Backup 无法收到 Master 发出的 VRRP 状态通过信息，就会直接升为 Master 。VIP 也会「漂移」到新的 Master 。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021630199.png)

4、我们把 `192.168.200.133` 服务器 A 的 keepalived 再次启动下，由于它的优先级高于 `192.168.200.122` 服务器 B，所有它会再次成为 Master，VIP 也会「漂移」过去



##### 4. vrrp_script

> 1、keepalived 所在服务器的 Nginx 出现问题后，把 keepalived 关闭掉，就可以让 VIP 执行另外一台服务器。但是现在这所有的操作都是通过手动来完成的，我们需要让系统自动判断当前服务器的 Nginx 是否正确启动，如果没有，要能让 VIP 自动进行漂移
>
> 2、keepalived 只能做到对网络故障和 keepalived 本身的监控，即当出现网络故障或者 keepalived 本身出现问题时，进行切换。但是这些还不够，我们还需要监控 keepalived 所在服务器上的其他业务，比如 Nginx，如果 Nginx 出现异常了，而 keepalived 却保持正常，是无法完成系统的正常工作的，因此需要根据业务进程的运行状态决定是否需要进行主备切换，这个时候，我们可以通过编写脚本对业务进程进行检测监控。



keepalived 中 vrrp_script 的配置模板

```nginx
vrrp_script 脚本名称
{
    script "脚本位置"
    interval 3 # 执行时间间隔
    weight -20 # 动态调整 vrrp_instance 的优先级
}
```



**实现步骤**

1、编写脚本，脚本名为 `ck_nginx.sh`， `/etc/keepalived` 路径下

> - `ps `命令用于显示当前进程 (process) 的状态。
> - `-C`(command)：指定命令的所有进程
> - `--no-header`：排除标题

```	sh
#!/bin/bash
num=`ps -C nginx --no-header | wc -l`  # 查询 Nginx 的进程数

if [ $num -eq 0 ];then       	       # 如果 Nginx 的进程数等于 0
/usr/local/nginx/sbin/nginx			   # 则可执行文件 nginx，启动 Nginx 服务

sleep 2								   # 阻塞 2 秒 

if [ `ps -C nginx --no-header | wc -l` -eq 0 ]; then  # 再次查询 Nginx 的进程数
killall keepalived		# 如果 Nginx 的进程数不等于 0，则杀死 keepalived 进程

fi
fi
```

2、将脚本添加到 Master 服务器 A 的 keepalived 的配置文件里

```nginx
vrrp_script ck_nginx {
   script "/etc/keepalived/ck_nginx.sh" # 执行脚本的位置
   interval 2		# 执行脚本的周期，秒为单位
   weight -20		# 权重的计算方式，每次关闭后权重-20，降低作为master的权重
}
vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 10
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.200.111
    }
    track_script {
      ck_nginx
    }
}
```

3、通常如果 Master 服务死掉后，Backup 会变成 Master，但是当原来的 Master 服务又恢复了，它会和原来的 Backup 会抢占 VIP，这样就会发生两次切换，这对业务繁忙的网站来说是不好的。所以我们要在配置文件加入 `nopreempt` 非抢占，但是这个参数只能用于 Backup 的服务器，所以我们在用配置的时候，最好 Master 和 Backup 的 state 都设置成 Backup，这样它们只能通过 priority 优先级来竞争





## 九、Nginx站点与认证

这个是下载 Nginx 时访问的网站，该网站主要就是用来提供用户来下载相关资源的网站，就叫做下载网站

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021719610.png" style="zoom:80%;" />

如何制作一个下载站点

- Nginx 使用的是模块 `ngx_http_autoindex_module` 来实现的（自带），该模块处理以斜杠 ` / ` 结尾的请求，并生成目录列表
- Nginx 编译的时候会自动加载该模块，但是该模块默认是关闭的，我们需要使用以下指令来完成对应的配置



### 1、指令

**autoindex**

启用或禁用目录列表的输出

| 语法                   | 默认值         | 位置                   |
| ---------------------- | -------------- | ---------------------- |
| autoindex <on \| off>; | autoindex off; | http、server、location |



**autoindex_exact_size**

`autoindex_exact_size` 指令对应 HTLM 格式，指定是否在目录列表展示文件的详细大小。

- 默认为 on，显示出文件的确切大小，单位是 bytes。

- 改为 off 后，显示出文件的大概大小，单位是 kB 或者 MB 或者 GB。

| 语法                              | 默认值                   | 位置                   |
| --------------------------------- | ------------------------ | ---------------------- |
| autoindex_exact_size <on \| off>; | autoindex_exact_size on; | http、server、location |



**autoindex_format**

`autoindex_format` 指令设置目录列表的格式。只有当 `autoindex_format` 指令设置为 html 时，`autoindex_exact_size` 指令才会起作用。该指令在 1.7.9 及以后版本中出现。

| 语法                                             | 默认值                 | 位置                   |
| ------------------------------------------------ | ---------------------- | ---------------------- |
| autoindex_format <html \| xml \| json \| jsonp>; | autoindex_format html; | http、server、location |



**autoindex_localtime**

`autoindex_localtime` 指令对应 HTML 格式，是否在目录列表上显示时间。

- 默认为 off，显示的文件时间为 GMT 时间。

- 改为 on 后，显示的文件时间为文件的服务器时间。

| 语法                            | 默认值                   | 位置                   |
| ------------------------------- | ------------------------ | ---------------------- |
| autoindex_localtime <on \|off>; | autoindex_localtime off; | http、server、location |



### 2、案例

1、准备几个文件或者压缩包。创建一个目录，将压缩包放入其中，这里创建的路径是 `/opt/download`

2、Nginx 配置

```nginx
location /download {
    root /opt;                # 下载目录所在的路径，location 后面的 /download 拼接到 /opt 后面
    # 以这些后缀的文件点击后为下载，注释掉则 txt 等文件是在网页打开并查看内容
    if ($request_filename ~* ^.*?\.(txt|doc|pdf|rar|gz|zip|docx|exe|xlsx|ppt|pptx|conf)$){
			  add_header Content-Disposition 'attachment;';
		  }
    autoindex on;			  # 启用目录列表的输出
    autoindex_exact_size on;  # 在目录列表展示文件的详细大小
    autoindex_format html;	  # 设置目录列表的格式为 html
    autoindex_localtime on;   # 目录列表上显示系统时间
}
```

3、访问 `192.168.91.200/download`

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021737159.png" style="zoom:80%;" />



### 3、用户认证

> 对应系统资源的访问，往往需要限制谁能访问，谁不能访问。认证需要做的就是根据用户输入的用户名和密码来判定用户是否为合法用户，如果是则放行访问，如果不是则拒绝访问。

Nginx 对应用户认证这块是通过 `ngx_http_auth_basic_module` 模块来实现的，它允许通过使用「HTTP基本身份验证」协议验证用户名和密码来限制对资源的访问。默认情况下 Nginx 是已经安装了该模块，如果不需要则使用 `--without-http_auth_basic_module` 删除认证模块



**auth_basic**

`auth_basic` 指令使用「HTTP基本身份验证」协议启用用户名和密码的验证。默认关闭。

| 语法                        | 默认值          | 位置                                 |
| --------------------------- | --------------- | ------------------------------------ |
| auth_basic <string \| off>; | auth_basic off; | http、server、location、limit_except |

开启后，服务端会返回 401，指定的字符串会返回到客户端，给用户以提示信息，但是不同的浏览器对内容的展示不一致



**auth_basic_user_file**

`auth_basic_user_file` 指令指定用户名和密码所在文件，包括所在的路径。指定文件路径，该文件中设置用户名和密码，密码需要进行加密。可以采用工具自动生成

| 语法                   | 默认值 | 位置                                 |
| ---------------------- | ------ | ------------------------------------ |
| auth_basic_user_file ; | —      | http、server、location、limit_except |



**案例**

1、Nginx配置

```nginx
location /download{
    # 下载站点
    root /opt;                # 下载目录所在的路径，location 后面的 /download 拼接到 /opt 后面
    autoindex on;			  # 启用目录列表的输出
    autoindex_exact_size on;  # 在目录列表展示文件的详细大小
    autoindex_format html;	  # 设置目录列表的格式为 html
    autoindex_localtime on;   # 目录列表上显示系统时间

    # 认证模块
    auth_basic 'please input your auth';   # 启用户名和密码的验证，并在请求头插入数据
    auth_basic_user_file htpasswd;    # 存用户名和密码的文件路径
}
```

2、浏览器访问 `192.168.91.200/download`

> 上述方式虽然能实现用户名和密码的验证，但是所有的用户名和密码信息都记录在文件里面，如果用户量过大的话，这种方式就显得有点麻烦了，这时候就得通过后台业务代码来进行用户权限的校验了

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202412021742100.png" style="zoom:70%;" />



## 十、Nginx扩展模块

### 1、Lua

Lua 是一种轻量、小巧的脚本语言，用标准 C 语言编写并以源代码形式开发。设计的目的是为了嵌入到其他应用程序中，从而为应用程序提供灵活的扩展和定制功能



#### 1.1 第一个Lua程序

Lua 和 C/C++ 语法非常相似，整体上比较清晰，简洁。条件语句、循环语句、函数调用都与 C/C++ 基本一致。

Lua 有两种交互方式，分别是：交互式和脚本式



**交互式**

> 交互式是指可以在命令行输入程序，然后回车就可以看到运行的效果。Lua 交互式编程模式可以通过命令 `lua -i` 或 `lua` 来启用

```sh
[root@master lua-5.4.4]# lua
Lua 5.4.4  Copyright (C) 1994-2022 Lua.org, PUC-Rio
> print("HelloWorld")
HelloWorld
```



**脚本式**

> 脚本式是将代码保存到一个以 lua 为扩展名的文件中并执行的方式。创建一个lua文件hello.lua，在文件中添加要执行的代码，然后通过命令 `lua hello.lua` 来执行，会在控制台输出对应的结果。

```lua
print("HelloWorld")
```

```sh
[root@master lua_demo]# lua hello.lua
HelloWorld
```

> 将 hello.lua 做如下修改，第一行用来指定 Lua 解释器命令所在位置为 `/usr/local/bin/lua`，加上 # 号标记，解释器会忽略它。一般情况下 #! 就是用来指定用哪个程序来运行本文件

```lua
#!/usr/local/bin/lua
print("Hello World!!!")
```

```sh
[root@master lua_demo]# ./hello.lua
Hello World!!!
```

在 Lua 语言中，连续语句之间的分隔符并不是必须的，也就是说后面不需要加分号，当然加上也不会报错



#### 1.2 全局/局部变量

在 Lua 语言中，全局变量无须声明即可使用。在默认情况下，变量总是认为是全局的，如果未提前赋值，默认为 nil

```linux
[root@master lua_demo]# lua
Lua 5.4.4  Copyright (C) 1994-2022 Lua.org, PUC-Rio
> print(b)
nil
> b=100
> print(b)
100
> b=nil
> print(b)
nil
```

声明一个局部变量，需要使用 local 

```linux
[root@master lua_demo]# lua
Lua 5.4.4  Copyright (C) 1994-2022 Lua.org, PUC-Rio
> local a=100
> print(a)
nil
> local a=100 print(a)
100
```



#### 1.3 lua数据类型

可以使用 type 函数测试给定变量或者的类型

| 数据类型名 | 作用                |
| ---------- | ------------------- |
| nil        | 空，无效值          |
| boolean    | 布尔，true \| false |
| number     | 数值                |
| string     | 字符串              |
| function   | 函数                |
| table      | 表                  |
| thread     | 线程                |
| userdata   | 用户数据            |



**nil**

nil 是一种只有一个 nil 值的类型，它的作用可以用来与其他所有值进行区分。当想要移除一个变量时，只需要将该变量名赋值为 nil，垃圾回收就会会释放该变量所占用的内存。



**boolean**

boolean 类型具有两个值，true 和 false。boolean 类型一般被用来做条件判断的真与假。在 Lua 语言中，只会将 false 和 nil 视为假，其他的都视为真，**特别是在条件检测中 0 和空字符串都会认为是真**，这个和我们熟悉的大多数语言不太一样。



**number**

在 Lua5.3 版本开始，Lua 语言为数值格式提供了两种选择 integer（整型）和 float（双精度浮点型）



**string**

Lua 语言中的字符串即可以表示单个字符，也可以表示一整本书籍。在 Lua 语言中，操作 100K 或者 1M 个字母组成的字符串的程序很常见。可以使用单引号或双引号来声明字符串



**table**

table 是 Lua 语言中最主要和强大的数据结构。使用 table 表时，Lua 语言可以以一种简单、统一且高效的方式表示数组、集合、记录和其他很多数据结构。Lua 语言中的表本质上是一种辅助数组。这种数组比 Java 中的数组更加灵活，可以使用数值做索引，也可以使用字符串或其他任意类型的值作索引（除 nil 外）。**数组的下标默认是从 1 开始的**

```lua
arr = {"TOM","JERRY","ROSE"}

print(arr[0])		-- nil
print(arr[1])		-- TOM
print(arr[2])		-- JERRY
print(arr[3])		-- ROSE
```

```lua
arr = {}
arr["X"] = 10
arr["Y"] = 20
arr["Z"] = 30

-- 方式一
print(arr["X"])
print(arr["Y"])
print(arr["Z"])

-- 方式二
print(arr.X)
print(arr.Y)
print(arr.Z)
```

```lua
arr = {"TOM",X=10,"JERRY",Y=20,"ROSE",Z=30}

arr[1]       -- TOM
arr["X"]	 -- 10
arr.X   	 -- 10
arr[2]		 -- JERRY
arr["Y"]	 -- 20
arr.Y		 --20
arr[3]		 -- ROSE
```



**function**

在 Lua 语言中，函数（Function）是对语句和表达式进行抽象的主要方式。定义函数的语法为：

```lua
function functionName(params)

end
```

函数被调用的时候，传入的参数个数与定义函数时使用的参数个数不一致的时候，Lua 语言会通过抛弃多余参数和将不足的参数设为 nil 的方式来调整参数的个数

```lua
-- 函数
function  f(a,b)
    print(a,b)
end

-- 调用函数
f()			--> nil  nil
f(2)		--> 2 nil
f(2,6)		--> 2 6
f(2.6.8)	--> 2 6 (8 被丢弃)
```

可变长参数函数

```lua
-- 函数
function add(...)
    a,b,c=...    -- 按顺序令 a,b,c 等于多个参数的前三个
    print(a)
    print(b)
    print(c)
end

-- 调用函数
add(1,2,3)  --> 1 2 3
```

函数返回值可以有多个

```lua
-- 函数
function f(a,b)
    return a,b
end

-- 调用函数
x,y = f(11,22)	--> x=11,y=22	
```



### 2、Nginx Lua扩展模块

> 1、淘宝开发的 `ngx_lua` 模块通过将 Lua 解释器集成进 Nginx，可以采用 Lua 脚本实现业务逻辑，由于 Lua 的紧凑、快速以及内建协程，所以在保证高并发服务能力的同时极大地降低了业务逻辑实现成本
>
> 2、OpenResty 是一个基于 Nginx 与 Lua 的高性能 Web 平台，其内部集成了大量精良的 Lua 库、第三方模块以及大多数的依赖项。用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。所以本身 OpenResty 内部就已经集成了 Nginx 和 Lua，所以我们使用起来会更加方便。



#### 2.1 ngx_lua指令

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202412022313392.png" style="zoom: 80%;" />

先来解释下 * 的作用

- `*`： 即 `xx_by_lua `指令，指令后面跟的是 lua 指令
- `*:_file`，即 `xx_by_lua_file` 指令，后面跟的是 lua 文件
- `*:_block`，即 `xx_by_lua_block` 指令，在 0.9.17 版后替换 `init_by_lua_file`



OpenResty 的执行阶段分为

- `init_by_lua*`：在每次 Nginx 重新加载配置时执行，可以用来完成一些耗时模块的加载，或者初始化一些全局配置

  > 这是一个公共模块，把所有都用到的代码放到这个模块里，避免重复使用相同的代码。比如每个模块都需要 MySQL 和 Redis，则在这个公共模块进行引用

  ```nginx
  init_by_lua_block{
      mysql = require "resty.mysql"
  	redis = require "resty.redis"
  }
  # 下方直接使用 MySQL 和 Redis 的 API
  ```

- `init_worker_by_lua*`：该指令用于启动一些定时任务，如心跳检查、定时拉取服务器配置等

  ```nginx
  init_worker_by_lua '
       local delay = 3  -- in seconds
       local new_timer = ngx.timer.at
       local log = ngx.log
       local ERR = ngx.ERR
       local check
  
       check = function(premature)
           if not premature then
               -- do the health check or other routine work
               local ok, err = new_timer(delay, check)
               if not ok then
                   log(ERR, "failed to create timer: ", err)
                   return
               end
           end
       end
  
       local ok, err = new_timer(delay, check)
       if not ok then
           log(ERR, "failed to create timer: ", err)
           return
       end
  ';
  ```

- `set_by_lua*` : 该指令用来做变量赋值，这个指令一次只能返回一个值，并将结果赋值给 Nginx 中指定的变量

  ```nginx
  set_by_lua $name "
  		local uri_args = ngx.req.get_uri_args()   -- 获取请求 ? 后的参数
  		name = uri_args['name']   -- 获取 key 为 name 的参数
  		return name..'先生'   -- 在 name 后面加上 先生，作为 $name 的 value 返回给客户端
  	";
  ```

- `rewrite_by_lua*` : 该指令用于执行内部 URL 重写或者外部重定向，典型的如伪静态化 URL 重写，本阶段在 Rewrite 处理阶段的最后默认执行。

  ```nginx
  location /foo {
      set $a 12; # 创建变量 $a
      set $b ""; # 创建变量 $b
      rewrite_by_lua '
           ngx.var.b = tonumber(ngx.var.a) + 1  # 此时 b = 13
           if tonumber(ngx.var.b) == 13 then
               return ngx.redirect("/bar");   # 重定向到 /bar
           end
       ';
      echo "res = $b";  # res = 13
  }
  ```

- `access_by_lua* `: IP 准入、接口权限等情况集中处理（例如配合 iptable 完成简单防火墙）

  ```nginx
  location / {
      access_by_lua '
          local res = ngx.location.capture("/auth")
  
          if res.status == ngx.HTTP_OK then
          return
          end
  
          if res.status == ngx.HTTP_FORBIDDEN then
          ngx.exit(res.status)
          end
  
          ngx.exit(ngx.HTTP_INTERNAL_SERVER_ERROR)
          ';
  
          # proxy_pass/fastcgi_pass/postgres_pass/...
  }
  ```

- `content_by_lua* `: **该指令是应用最多的指令**，大部分任务是在这个阶段完成的，其他的过程往往为这个阶段准备数据，正式处理基本都在本阶段

  ```nginx
  content_by_lua_block {
      set_by_lua $name "
          local uri_args = ngx.req.get_uri_args()   -- 获取请求 ? 后的参数
          name = uri_args['name']   -- 获取 key 为 name 的参数
          return name..'先生'   -- 在 name 后面加上 先生，作为 $name 的 value 返回给客户端
      ";
  }
  ```

- `header_filter_by_lua*` : 设置应答消息的头部信息

  ```nginx
  location / {
  	 proxy_pass http://mybackend;
  	 header_filter_by_lua 'ngx.header.username = "frx"';
  }
  ```

- `body_filter_by_lua* `: 对响应数据进行过滤，如截断、替换

  ```nginx
  location / {
       proxy_pass http://mybackend;
       body_filter_by_lua 'ngx.arg[1] = string.upper(ngx.arg[1])';  # 转小写
  }
  ```

- `log_by_lua*` : 会话完成后本地异步完成日志记录（日志可以记录在本地，还可以同步到其他机器）

  ```nginx
  server {
      location / {
          proxy_pass http://mybackend;
  
          log_by_lua '
              local log_dict = ngx.shared.log_dict
              local upstream_time = tonumber(ngx.var.upstream_response_time)
  
              local sum = log_dict:get("upstream_time-sum") or 0
              sum = sum + upstream_time
              log_dict:set("upstream_time-sum", sum)
  
              local newval, err = log_dict:incr("upstream_time-nb", 1)
              if not newval and err == "not found" then
              log_dict:add("upstream_time-nb", 0)
              log_dict:incr("upstream_time-nb", 1)
              end
              ';
      }
  }
  ```

  

#### 2.2 相关指令

**ngx.say**

返回结果给客户端

```nginx
location / {
    default_type 'text/plain';
    content_by_lua_block {
        ngx.say("Hello World")
    }
}
```



**ngx.print**

将输入参数合并发送给 HTTP 客户端 (作为 HTTP 响应体)。如果此时还没有发送响应头信息，函数将先发送 HTTP 响应头，再输出响应体。本函数为异步调用，将立即返回，不会等待所有数据被写入系统发送缓冲区。要以同步模式运行，在调用 `ngx.print` 之后调用 `ngx.flush`

```nginx
local table = {
     "hello, ",
     {"world: ", true, " or ", false,
         {": ", nil}}
 }
ok, err = ngx.print(table)

# 输出
hello, world: true or false: nil
```



**ngx.flush**

向客户端刷新响应输出。`ngx.flush` 接受一个布尔型可选参数 `wait` (默认值 `false`)。当通过默认参数（`false`）调用时，本函数发起一个异步调用。当把 `wait` 参数设置为 `true` 时，本函数将以同步模式执行。

- 异步调用下，直接将数据返回，不等待输出数据被写入系统发送缓冲区。
- 同步模式下，本函数不会立即返回，一直到所有输出数据被写入系统输出缓冲区，或到达发送超时 send_timeout 时间。

这个要和上方的 ngx.print 进行配合使用，开启同步模式，可以优化返回客户端多条数据的速度。

```nginx
local table = {
     "hello, ",
     {"world: ", true, " or ", false,
         {": ", nil}}
 }
ok, err = ngx.print(table)
ngx.flush(true) 		-- 开启同步模式
```



**ngx.req.get_uri_args**

返回一个 Lua table，包含当前请求的所有 URL 查询参数。

语法：`args = ngx.req.get_uri_args([max_args])`

```nginx
location = /test {
     content_by_lua '
         local args = ngx.req.get_uri_args()
         for key, val in pairs(args) do
             if type(val) == "table" then
                 ngx.say(key, ": ", table.concat(val, ", "))
             else
                 ngx.say(key, ": ", val)
             end
         end
     ';
 }
```

访问 `GET /test?foo=bar&bar=baz&bar=blah` 

> 多次出现同一个参数 key 时，将生成一个 Lua table，按顺序保存其所有 value

```
foo: bar
bar: baz, blah
```



**ngx.req.read_body**

同步读取客户端请求体，不阻塞 Nginx 事件循环

语法：`ngx.req.read_body()`。

```nginx
ngx.req.read_body()
local args = ngx.req.get_post_args()
```



**ngx.req.get_post_args**

返回一个 Lua table，包含当前请求的所有 POST 查询参数。

语法：`args, err = ngx.req.get_post_args(max_args?)`

> 使用 `ngx.req.get_post_args` 获取参数前，必须使用 `ngx.req.read_body` 读取请求体。

```nginx
location = /test {
     content_by_lua '
         ngx.req.read_body()
         local args, err = ngx.req.get_post_args()
         if not args then
             ngx.say("failed to get post args: ", err)
             return
         end
         for key, val in pairs(args) do
             if type(val) == "table" then
                 ngx.say(key, ": ", table.concat(val, ", "))
             else
                 ngx.say(key, ": ", val)
             end
         end
     ';
}

$ curl --data 'foo=bar&bar=baz&bar=blah' localhost/test
foo: bar
bar: baz, blah
```



#### 2.3 案例

发送请求：`http://192.168.91.200?name=张三&gender=1`

Nginx 接收到请求后，根据 gender 传入的值进行判断，如果 gender 传入的是 1，则在页面上展示张三先生，如果 gender 传入的是 0，则在页面上展示张三女士，如果未传或者传入的不是 1 和 2，则在页面上展示张三

```nginx
location /getByGender {
	default_type 'text/html';
    
	set_by_lua $name "
		local uri_args = ngx.req.get_uri_args()
		gender = uri_args['gender']
		name = uri_args['name']
		if gender=='1' then
			return name..'先生'
		elseif gender=='0' then
			return name..'女士'
		else
			return name
		end
	";
	header_filter_by_lua "
		ngx.header.aaa='bbb'
	";
	charset utf-8;
	return 200 $name;
}
```



#### 2.4 ngx_lua操作Redis

> 在 Nginx 核心系统中，Redis 是常备组件。Nginx 支持 3 种方法访问 Redis，分别是 HttpRedis 模块、HttpRedis2Module 模块、lua-resty-redis 库。
>
> - HttpRedis 模块提供的指令少，功能单一，适合做简单缓存
> - HttpRedis2Module 模块比 HttpRedis 模块操作更灵活，功能更强大
> - Lua-resty-redis 库是 OpenResty 提供的一个操作 Redis 的接口库，可根据自己的业务情况做一些逻辑处理，适合做复杂的业务逻辑

`lua-resty-redis` 提供了访问 Redis 的详细 API，包括创建对像、连接、操作、数据处理等。这些 API 基本上与 Redis 的操作一一对应

| API                                               | 作用                                                         |
| ------------------------------------------------- | ------------------------------------------------------------ |
| `redis = require "resty.redis"`                   | 引入 Redis 模块，类似于 Java 的 import。                     |
| `redis,err = redis:new()`                         | 创建一个 Redis 对象给 redis，err 记录创建失败的原因。        |
| `ok,err=redis:connect(host,port[,options_table])` | 设置连接 Redis 的连接信息。 ok：连接成功返回 1，连接失败返回 nil。 err：返回对应的错误信息。 |
| `redis:set_timeout(time)`                         | 设置请求操作 Redis 的超时时间，单位毫秒。                    |
| `ok,err = redis:close()`                          | 关闭当前连接。 ok：连接成功返回 1，连接失败返回 nil。 err：返回对应的错误信息。 |
| 原生 Redis 命令如 get、set、lpush 等              | 所有的 Redis 命令都有自己的方法，方法名字和命令名字相同，只是全部为小写。 |

```nginx
location /redis {
    default_type "text/html";
    content_by_lua_block {
        local redis = require "resty.redis" 	-- 引入 Redis
        local redisObj = redis:new()  			-- 创建 Redis 对象
        redisObj:set_timeout(1000) 				-- 设置超时数据为 1s
        local ok,err = redisObj:connect("192.168.91.200",6379) -- 设置 Redis 连接信息
        if not ok then 							-- 判断是否连接成功
            ngx.say("failed to connection redis",err)
            return
        end
        ok,err = redisObj:set("username","TOM")	-- 存入数据
        if not ok then 							-- 判断是否存入成功
            ngx.say("failed to set username",err)
            return
        end
        local res,err = redisObj:get("username") -- 从 Redis 中获取数据
        ngx.say(res)							 -- 将数据写会消息体中
        redisObj:close()						 -- 关闭 Redis 连接
    }
}
```



#### 2.5 ngx_lua操作Mysql

> 在 ngx_lua 中，MySQL 有两种访问模式
>
> - 用 `ngx_lua` 模块和 `lua-resty-mysql` 模块，这两个模块是安装 OpenResty 时默认安装的
> - 使用 `drizzle_nginx_module`（HttpDrizzleModule）模块，需要单独安装，这个库现不在 OpenResty 中

`lua-resty-mysql` 是 OpenResty 开发的模块，使用灵活、功能强大，适合复杂的业务场景，同时支持存储过程的访问

- `mysql = require "resty.mysql"`：引入 MySQL 模块，类似于 Java 的 import。

- `db,err = mysql:new()`：创建一个 MySQL 连接对象给 db，连接对象遇到错误时，db 为nil，err 为错误描述信息。

- `ok,err = db:connect(Options)`：尝试连接到一个MySQL服务器。Options 是一个参数的 Lua 表结构，里面包含数据库连接的相关信息

  **Options 选项**

  ```
  host：服务器主机名或IP地址
  port：服务器监听端口，默认为3306
  user：登录的用户名
  password：登录密码
  database：使用的数据库名
  ```

- `db:set_timeout(time)`：设置子请求的超时时间，单位毫秒

- `ok,err = db:close()`：关闭当前 MySQL 连接并返回状态

  ```
  ok：如果成功，则返回 1；如果出现任何错误，则将返回 nil。
  err：如果出现任何错误，返回错误描述。
  ```

- `bytes,err=db:send_query(sql)`：异步向远程 MySQL 发送一个查询。如果成功则返回成功发送的字节数；如果错误，则返回 nil 和错误描述

- `res, err, errcode, sqlstate = db:read_result([rows])`：从 MySQL 服务器返回结果中读取一行数据。`rows` 指定返回结果集的最大值，默认为 4，可不写

  ```
  res：操作的结果集，返回一个描述 OK 包或结果集包的 Lua 表
  err：错误信息
  errcode：MySQL 的错误码，比如 1064
  sqlstate：返回由 5 个字符组成的标准 SQL 错误码，比如 42000
  
  # 查询，则返回一个容纳多行的数组。每行是一个数据列的key-value对
  {
      {id=1,username="TOM",birthday="1988-11-11",salary=10000.0},
  	{id=2,username="JERRY",birthday="1989-11-11",salary=20000.0}
  }
  
  # 增删改
  {
      insert_id = 0,
      server_status=2,
      warning_count=1,
      affected_rows=2,
      message=nil
  }
  ```



**数据查询**

> 以下方式返回的是需要我们指定返回的数据，但是我们根本不知道查询的数据有多少条，长什么样子

```nginx
location /testMysql {
    content_by_lua_block{
        local mysql = require "resty.mysql"
        local db, err = mysql:new()  			-- 创建实例  
        if not db then  
            ngx.say("new mysql error : ", err)  
            return  
        end  
        
        local ok,err = db:connect{
            host="192.168.91.200",
            port=3306,
            user="root",
            password="12345678",
            database="nginx_db"
        }
        
        db:set_timeout(1000)			-- 设置超时时间(毫秒)  
        -- 查询语句
		local query_sql = "select * from users"
        db:send_query(query_sql)
        local res,err,errcode,sqlstate = db:read_result()
        
        -- 返回数据 .. 代表拼接
        ngx.say(res[1].id..","..res[1].name)
    	db:close()
    }

}
```



#### 2.6 lua-cjson处理查询结果

`read_result()` 得到的结果 res 都是 table 类型，要想在页面上展示，就必须知道 table 的具体数据结构才能进行遍历获取。处理起来比较麻烦。使用 cjson，就可以将 table 类型的数据转换成 Json 字符串，把 Json 字符串展示在页面上即可。

```nginx
location /testMysql {
    content_by_lua_block{

        local mysql = require "resty.mysql"
        local cjson = require "cjson"

        local db = mysql:new()

        local ok,err = db:connect{
            host="192.168.199.27",
            port=3306,
            user="root",
            password="123456",
            database="nginx_db"
        }
        db:set_timeout(1000)

        -- db:send_query("select * from users where id = 1")
        
        db:send_query("select * from users")
        local res,err,errcode,sqlstate = db:read_result()
        
        ngx.say(cjson.encode(res))   -- 转为 JSON 字符串格式

        for i,v in ipairs(res) do     -- 循环来返回数据
            ngx.say("返回的数据："，v.id..","..v.name)
        end
        
        db:close()
    }
}
```



#### 2.7 增删改操作

优化 `send_query` 和 `read_result`，两个可以变成一体。`res, err, errcode, sqlstate = db:query(sql[,rows])`

```nginx
location /testMysql {
    content_by_lua_block{

        local mysql = require "resty.mysql"

        local db = mysql:new()

        local ok,err = db:connect{
            host="192.168.91.200",
            port=3306,
            user="root",
            password="12345678",
            database="nginx_db",
            max_packet_size=1024,
            compact_arrays=false
        }
        
        db:set_timeout(1000)
        -- 查询操作
        local res,err,errcode,sqlstate = db:send_query("select * from users where id = 1")
        -- 修改操作
        local res,err,errcode,sqlstate = db:query("update users set name = 'bing' where id = 1")
        -- 删除操作
        local res,err,errcode,sqlstate = db:query("delete from users where id = 1")
        -- 插入操作
        local insert_sql = "insert into users(id,name) values(null,'kele')"
        db:close()
    }

}
```



#### 2.8 案例

使用 ngx_lua 模块完成查询 MySQL 数据，然后在 Redis 缓存预热

1、浏览器输入：`http://191.168.91.200?name=frx`

2、从MySQL 表中查询出符合条件的数据，此时获取的结果为 table 类型

3、使用 cjson 将 table 数据转换成 json 字符串

4、将查询的结果数据存入 Redis 中

```nginx
init_by_lua_block{
    redis = require "resty.redis"
    mysql = require "resty.mysql"
    cjson = require "cjson"
}
location /testMysql {
    default_type "text/html";
    content_by_lua_block{
        -- 获取请求的参数 name
        local param = ngx.req.get_uri_args()["name"]
        -- 建立 mysql 数据库的连接
        local db = mysql:new()
        local ok,err = db:connect{
            host="192.168.91.200",
            port=3306,
            user="root",
            password="12345678",
            database="nginx_db"
        }
        if not ok then
            ngx.say("failed connect to mysql:",err)
            return
        end
        
        -- 设置连接超时时间
        db:set_timeout(1000)
        
        -- 查询数据
        local sql = "";
        
        if not param then
            sql="select * from users"
        else
            sql="select * from users where name=" .. "'" .. param .."'" -- sql 注入 ，不建议
            sql="select * from users where name=" .. ngx.quote_sql_str(ch_param) -- 防止 sql 注入
        end
        
        local res,err,errcode,sqlstate=db:query(sql)
        
        if not res then
            ngx.say("failed to query from mysql:",err)
            return
        end
        
        -- 连接redis
        local rd = redis:new()
        ok,err = rd:connect("192.168.91.200",6379)
        
        if not ok then
            ngx.say("failed to connect to redis:",err)
            return
        end
        
        rd:set_timeout(1000)
        
        -- 循环遍历数据
        for i,v in ipairs(res) do
            rd:set("user_"..v.username,cjson.encode(v))
        end
        
        ngx.say("success")
        
        -- 关闭 MySQL 和 Rdis 的连接
        rd:close()
        db:close()
    }
}
```

