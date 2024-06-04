## 1、数据库

**命令行连接mysql**：`mysql -h 主机名 -P 端口 -u 用户名 -p密码` 	如果不写`-h`，默认为本机`loaclhost`。如果不写`-P`，默认端口号为3306



### 1.1 数据库三层结构

1、安装数据库，就是在主机安装一个数据库管理系统【DBMS】，这个管理系统可以管理多个数据库

2、一个数据库可以创建多个表，以保存信息

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304261457586.png)



> 启动MySql服务后，他会在3306端口【可以在配置文件my.ini修改】监听，DBMS就是一个数据库管理系统，他有很多个数据库【可以在data文件夹下看到】，对于每个数据库，他有很多张表【在data文件夹下对应数据库中可以看到】，普通表的本质仍然是文件。当我们的客户端、DOS终端或者Java程序想要连接数据库，就向3306端口发送请求【就和socket通信一样】，连接建立后，客户端发送一系列sql命令，数据库服务端会解析语句，并执行相应的动作





**SQL语句分类**

- `DDL`：数据定义语言【create表，库...】
- `DML`：数据操作语言【insert、update、delete】
- `DQL`：数据查询语言【select】
- `DCL`：数据控制语句【管理数据库：比如用户权限 grant、revoke】





### 1.2 创建数据库

```CREATE DATABASE [IF NOT EXISTS] db_name ```

`[DEFAULT] CHARACTER SET character_name`

`[DEFAULT] COLLATE collate_name`

- `CHARACTER SET`：指定数据库采用的字符集，如果不指定，默认`utf8`
- `COLLATE`：指定数据库字符集采用的校对规则【常用的`utf8_bin(区分大小写)`和`utf8_general_ci(不区分大小写)`】，如果不指定，默认为`utf8_general_ci`

```sql
# 创建db01数据库
CREATE DATABASE IF NOT EXISTS db01;

# 删除db01数据库
DROP DATABASE db01

# 创建db02数据库，指定utf8字符集
CREATE DATABASE db02 CHARACTER SET utf8

DROP DATABASE db02

# 创建db03数据库，指定utf8字符集，utf8_bin校对规则[区分大小写]
CREATE DATABASE db03 CHARACTER SET utf8 COLLATE utf8_bin
```





### 1.3 查看、删除数据库

- `SHOW DATABASES`：显示数据库
- `SHOW CREATE DATABASE db_name`：显示创建的数据库
- `DROP DATABASE [IF EXISTS] db_name`：删除数据库

```sql
# 显示当前的数据库
SHOW DATABASES

# 查看db02数据库的信息
SHOW CREATE DATABASE db02

# 删除db01数据库
DROP DATABASE IF EXISTS db01
```





### 1.4 备份恢复数据库

- `mysqldump -u 用户名 -p密码 -B 数据库1 数据库2 数据库n > 文件名.sql`：备份数据库【在DOS下执行】
- `source 文件名.sql`：恢复数据库【进入mysql命令行执行】

```sql
# 备份, 要在Dos下执行, mysqldump 指令其实在 mysql 安装目录\bin
# 这个备份的文件，就是对应的 sql 语句
mysqldump -u root -p -B db02 db03 > d:\\data.sql

# 恢复数据库(需要进入mysql命令行)
source d:\\data.sql
# 也可以将data.sql文件内容复制到查询编辑器中，然后执行
```





### 1.5 备份恢复数据库的表

`mysqldump -u 用户名 -p密码 数据库 表1 表2 表n > 文件名.sql`：备份数据库表【在DOS下执行】

`source 文件名.sql`：恢复数据库表【在对应数据库的命令行下执行】

```sql
# 备份数据库表(Dos下执行)
mysqldump -u root -p db03 users age > d:\\table.sql

# 恢复数据库表(在对应数据库的命令行下执行)
source d:\\table.sql
```





## 2、表

### 2.1 创建表

```sql
CREATE TABLE table_name
(
	field1 datatype,
	field2 datatype,
	field3 datatype
)character set 字符集 collate 校对规则 engine 存储引擎
field: 指定列名		
datatype: 指定列类型(字段类型)
character set: 如不指定则为所在数据库字符集
collate: 如不指定则为所在数据库校对规则
engine: 引擎
```



```SQL
# 创建表
# id 		整型
# name 		字符串
# password 	字符串
# birthday 	日期
create table `user` (
	id INT,
	`name` VARCHAR(255),
	`password` VARCHAR(255),
	birthday DATE)
	CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB
```





### 2.2 Mysql常用数据类型

![image-20230426165218521](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20230426165218521.png)





#### 2.2.1 整数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304271545994.png)



```sql
# tinyint 范围 有符号[-128, 127]  无符号[0, 255]
# 表的字符集，校验规则，存储引擎，默认和数据库创建时是一致的
# 1.如果没有指定unsigned，则tinyint就是有符号
# 2.如果指定unsigned，tinyint就是无符号 0-255
CREATE TABLE t1(
		id TINYINT);
	
INSERT INTO T1 VALUE(-128);
INSERT INTO T1 VALUE(127); 
# INSERT INTO T1 VALUE(128); 报错，越界

CREATE TABLE T2(
		id TINYINT UNSIGNED);
INSERT INTO T2 VALUE(255);
# INSERT INTO T2 VALUE(-1);  报错，越界
```





#### 2.2.2 bit

- `bit`字段显示时，按照位的方式【二进制】显示
- 查询的时候可以用数值查询
- `bit(M)`：位类型，M指定位数，默认为1，范围为 1 - 64

```sql
# bit
CREATE TABLE t3(
		id bit(2));
INSERT INTO t3 VALUES(3);
# INSERT INTO T3 VALUES(4); 报错 2位只能存0-3
INSERT INTO t3 VALUES(2); 

SELECT * FROM t3; # 显示11 和 10 【二进制形式显示】
SELECT * FROM T3 WHERE id=3 # 11
```





#### 2.2.3 小数

1、`FLOAT / DOUBLE [UNSIGNED]`：`FLOAT`单精度， `DOUBLE`双精度，加上`UNSIGNED`就是无符号小数

2、`DECIMAL[M,D] [UNSIGNED]`：可以支持更加精确的小数位，M是小数位数的总数，D是小数点后边的位数

​	如果D是0，则没有小数部分。M最大为65，D最大为30

​	D默认为0，M默认为10

```sql
# 小数
CREATE TABLE t4 (
	num1 FLOAT,
	num2 DOUBLE,
	num3 DECIMAL(30,20));
	
INSERT INTO t4 VALUES(156.1234567891234546, 156.1234567891234546,156.1234567891234546)
SELECT * FROM t4;	# 156.123  156.12345678912345    156.12345678912345460000

# DECIMAL可以放很大的数
CREATE TABLE t5(
	num decimal(65))
INSERT INTO t5 VALUES(156123456789123454615612345678912345461561234567891234546)
select * from t5
```





#### 2.2.4 字符串

1、`CHAR(size)`：固定长度字符串，最大**255字符**

2、`VARCHAR(size)`：可变长度字符串，最大**65532字节**【1-3个字符用来记录字符串大小】

​	`utf8`编码最大21844个字符【65532 / 3】，`gbk`最大32766个字符【65532 / 2】

```sql
# 字符串
CREATE TABLE t6(
	name CHAR(4))
# INSERT INTO t6 VALUES('12345') 只能插入4个字符
INSERT INTO t6 VALUES('abcd') 
SELECT * FROM t6

CREATE TABLE t7(
	name VARCHAR(21844)) # 最多21844个字符，因为表默认和数据库字符集一样是utf8
	
CREATE TABLE t8(
	name VARCHAR(32766) CHARSET GBK) # gbk编码最多32766个字符
```





**字符串使用细节**

**细节1**

- `char(4)`  这个4表示字符数（最大255），不是字节数，不管是中文还是字母都是放4个，按字符计算
- `varchar(4)` 这个4表示字符数，不管是字母还是中文都以定义好的表的编码来存放数据



**细节2**

- `char(4)`是定长的，即使插入的是`'aa'`两个字符，也会占用分配的4个字符的空间
- `varchar(4)`是变长的，如果插入的是`‘aa'`，实际占用的大小就是`'aa'`的大小加上1-3个字节【用来存放字符串的长度】



**细节3**

- 如果数据是定长，推荐使用`char`，比如md5的密码，邮编、手机号、身份证等
- 如果一个字段的长度是不确定的，使用`varchar`，比如留言、文章
- 查询速度 `char` > `varchar`



**细节4**

- 在存放文本时，也可以使用`Text`数据类型，可以将`TEXT`列视为`VARCHAR`列，`TEXT`不能有默认值，大小0-2^16字节
- 如果希望存放更多字符，可以选择`MEDIUMTEXT`【0-2^24】或者`LONGTEXT`【0-2^32】



```sql
CREATE TABLE t9(
	name CHAR(4))
INSERT INTO t9 values('北京洛阳')

CREATE TABLE t10(
	name VARCHAR(4))
INSERT INTO t9 values('abcd') # 最多4个字符

CREATE TABLE t11(
	content1 TEXT,
	content2 MEDIUMTEXT,
	content3 LONGTEXT)
	
INSERT INTO t11 VALUES('adwadwadwadwad', 'dwaaaaaaaaaaaaaaaaaaaa', 'sad13wa1d5w6ad14w5a13d')
SELECT * FROM t11
```



**细节5**

- `MySQL` 规定除了` TEXT`、`BLOBs` 这种大对象类型之外，其他所有的列（不包括隐藏列和记录头信息）占用的字节长度加起来不能超过 65535 个字节，同时包含了**变长字段长度列表**和**NULL 值列表**所占用的字节数





#### 2.2.5 日期类型

1、`DATE`：年 / 月 /日

2、`DATETIME`：年 / 月 / 日  时：分：秒

3、`TIMESTAMP`：时间戳

```sql
# 日期
CREATE TABLE t12(
	birthday DATE,
	job_time DATETIME,
	login_time TIMESTAMP 
	NOT NULL DEFAULT CURRENT_TIMESTAMP  # 该字段非空  默认为当前时间戳
	ON UPDATE CURRENT_TIMESTAMP)	# 更新的时候该字段更新为当前时间戳
INSERT INTO t12(birthday,job_time) VALUES('2023-1-1','2023-2-2 10:01:10')
select * from t12
```







### 2.2 修改表

**1、添加列**

```sql
ALTER TABLE tablename 
	ADD (column datatype [DEFAULT expr], ...)

# 添加多列
ALTER TABLE employee 
	ADD n3 VARCHAR(32) NOT NULL DEFAULT '',
	ADD n1 int,
	ADD n2 int

ALTER TABLE employee 
	ADD (n10 int, n11 int, n12 int)
```



**2、修改列**

```sql
ALTER TABLE emp
	MODIFY (column datatype [DEFAULT expr], ...)

# 修改多列
ALTER TABLE employee
	MODIFY job VARCHAR(50), 
	MODIFY n1 float
```



**3、删除列**

```sql
ALTER TABLE emp 
	DROP (column)
```



```sql
desc 表名  # 查看表的列

# 修改表名
RENAME TABLE 表名 TO 新表名

# 修改表字符集
ALTER TABLE 表名 CHARACTER SET 字符集

# 修改列名
ALTER TABLE 表名 
	CHANGE 列名 新列名 datatype
```



**创建表并修改**

```sql
CREATE TABLE emp(
	id int,
	`name` VARCHAR(32),
	sex CHAR(1),
	birthday DATE,
	entry_date DATETIME,
	job VARCHAR(32),
	Salary DOUBLE,
	resume TEXT) CHARACTER SET utf8 COLLATE utf8_bin
	
INSERT INTO emp values(100, 'xrj', '男', '1999-10-26', '2022-10-1 10:01:10', '学生', 23555.5, '徐睿杰上班了')

select * from emp

# 添加image列，非空，默认为''，添加到name后
ALTER TABLE emp 
	ADD image VARCHAR(32) 
	NOT NULL DEFAULT '' AFTER `name`  # 该字段非空 默认为''，添加到name字段后 
	
# 修改job字段长度为60
ALTER TABLE emp
	MODIFY job VARCHAR(60)
	NOT NULL DEFAULT ''
	
# 删除sex
ALTER TABLE emp 
	DROP sex
	
# 表名修改为employee
RENAME TABLE emp TO employee

# 修改表的字符集为utf8
ALTER TABLE employee CHARACTER SET utf8

# 将列名name修改为username
ALTER TABLE employee
	CHANGE `name` username VARCHAR(64)

# 查看列信息
desc employee
```







## 3、CRUD

### 3.1 insert

```sql
INSERT INTO table_name [(column1, column2...)]
	VALUES(value1, value2...)
```



```sql
CREATE TABLE goods(
	id int,
	goods_name varchar(10),
	price double)
	
INSERT INTO goods
	VALUES(100, '蔬菜', 20.5)
	
INSERT INTO goods(id, goods_name, price)
	VALUES(100, '小米', 2000)
	
INSERT INTO goods(id, goods_name)
	VALUES(100, '华为')


INSERT INTO employee (id, username, image, birthday, entry_date, job, Salary, resume)
	VALUES(200, 'xx', 'image0', '2022-10-1', '2020-5-5 10:2:1', '程序员', 2000, '无')
```



1、插入的数据应与字段的数据类型相同

2、数据的长度应在列的规定范围内。例如：不能将一个长度为80的字符串加入到长度为40的列

3、在 values 中列出的数据位置必须与被加入的列的排列位置相对应。

4、字符和日期型数据应包含在单引号中

5、列可以插入空值【前提是该字段允许为空】

6、`insert into tab_name (列名..) values (),(),() `形式添加多条记录

```sql
INSERT INTO goods(id, goods_name)
	VALUES(200, '苹果'), (300, '三星')
```

7、如果是给表中的所有字段添加数据，可以不写前面的字段名称

8、当不给某个字段值时，如果有默认值就会添加默认值，否则报错 

​	 如果某个列 没有指定 `not null` , 那么当添加数据时，没有给定值，则会默认给 null

​     如果我们希望指定某个列的默认值，可以在创建表时指定



### 3.2 update

```sql
UPDATE table_name
	SET col_name1 = expr1 [, col_name2 = expr2...]
	WHERE ...
```



```sql
# 将employee表所有员工薪水改为5000
UPDATE employee
	SET Salary = 5000
	
# 将姓名为xrj的员工薪水改为10000
UPDATE employee
	SET Salary = 10000
	WHERE username='xrj'
	
# 将xx的薪水在原有基础上加1000
UPDATE employee
	SET Salary=Salary+1000
	WHERE username='xx'
	
# 修改多个列的值
UPDATE employee
	SET image='1.png', job='医生'
	WHERE username='xrj'
```





### 3.3 delete

```SQL
DELETE FROM tbale_name
	[WHERE ...]
```



```sql
# 删除表中名字为xx的记录
DELETE FROM employee
	WHERE username='xx'

# 删除表中所有记录
DELETE FROM employee
```



- 如果不使用`where`子句，将删除表中所有数据
- 使用`delete`仅删除记录，不删除表本身





### 3.4 select

```sql
SELECT [DISTINCT] *|{column1, column2, column3...}
		FROM table_name
```

`DISTINCT`可选，表示显示结果时，是否去掉重复数据

```sql
# 查询表中所有学生信息
SELECT * FROM student

# 查询表中所有学生的姓名和英语成绩
SELECT name, english from student

# 过滤表中重复数据
# 查询的记录,每个字段都相同才会去重
SELECT DISTINCT english from student
```





**使用表达式对查询的列进行运算**

`SELECT *|{column1 | expression, column2 | expression, ...} FROM tablename `

**在`select`语句中使用as语句起别名**

`SELECT column as 别名 from 表名`



```sql
# 统计每个学生总分
SELECT name, (chinese+english+math) FROM student

# 统计所有学生总分+10
SELECT name, (chinese+english+math+10) as total FROM student
```





#### **`where`子句中常用运算符**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305031733999.png)

```sql
-- 查询姓名为赵云的学生成绩
SELECT * FROM student
	WHERE name='赵云'

-- 查询英语成绩大于90的同学
SELECT * FROM student
	WHERE english > 90

-- 查询总分大于200的同学
SELECT * FROM student
	WHERE (chinese+math+english) > 200
	

-- 查询math大于60且id大于5的学生成绩
SELECT * FROM student 
	WHERE math > 90 and id > 5
	
# 查询英语成绩大于语文成绩的学生
SELECT * FROM student
	WHERE english > chinese
	
-- 查询总分大于200 且 数学成绩小于语文成语 姓赵的学生
-- 赵% 表示 名字以赵开头的
SELECT * FROM student 
	WHERE (chinese+math+english) > 200 and math<chinese and name LIKE '赵%'
	
-- 查询英语在80-90的学生
SELECT * FROM student
	WHERE english BETWEEN 80 AND 90
	
-- 查询数学分数为 89,90,91 的同学
SELECT * FROM student 
	WHERE math in (89, 90, 91)
	
-- 查询所有姓张的学生成绩
SELECT * FROM student
	WHERE name LIKE '张%'
	
-- 查询数学大于80，语文大于90的学生
SELECT * FROM student 
	WHERE math>80 and chinese>90
	
-- 查询所有姓张或姓赵的学生
SELECT * FROM student
	WHERE name LIKE '张%' OR name LIKE '赵%'
```





#### `order by` 排序查询结果

```sql
SELECT column1, column2,...
	FROM table
	order by column asc|desc
```



1、`order by`指定排序的列，排序的列既可以是表中的列名，也可以是`select`语句后指定的别名

2、`asc`升序【默认】，`desc`降序

3、`order by`子句应位于`SELECT`语句尾部



```sql
-- 对数学成绩排序后输出【升序】
SELECT * FROM student
	ORDER BY math

-- 对总分按降序输出
SELECT name, (math+chinese+english) as total FROM student
	ORDER BY total DESC

-- 对姓韩的学生成绩升序输出
SELECT name, (chinese+math+english) as total FROM student
	WHERE name like '韩%'
	ORDER BY total
```





#### `like`

**`like`操作符**

`%`：表示0到多个字符

`_`：表示单个字符

```SQL
-- 查询1992.1.1后入职的员工
SELECT * FROM emp
	WHERE hiredate > '1992.1.1'
	
-- 显示首字符为S的员工姓名和工资
SELECT ename, sal FROM emp
	WHERE ename LIKE 'S%'
	
-- 显示第三个字符为大写O的所有员工的姓名和工资
SELECT ename, sal FROM emp
	WHERE ename LIKE '__O%'
	
-- 显示没有上级的雇员
SELECT * FROM emp
	WHERE mgr IS NULL
	
-- 查询表结构
DESC emp

-- 按工资从低到高显示雇员信息
SELECT * FROM emp
	ORDER BY sal
	
-- 按部门号升序，工资降序，显示信息
SELECT * FROM emp
 ORDER BY deptno ASC, sal DESC
```






#### 分页查询

`SELECT ...limit start, rows`：表示从`start+1`行开始取，取出rows行，start从0开始计算

```sql
-- 按雇员id升序取出，每页显示3条记录，显示第1页，第2页，第3页
SELECT * FROM emp
	ORDER BY empno
	limit 0, 3

SELECT * FROM emp
	ORDER BY empno
	limit 3, 3

SELECT * FROM emp
	ORDER BY empno
	limit 6, 3
	
-- 按雇员的empno号降序取出，每页显示5条记录，显示第1页第3页
SELECT * FROM emp
	ORDER BY empno DESC
	limit 0, 5

SELECT * FROM emp
	ORDER BY empno DESC
	limit 10, 5
```







### 3.5 合计/统计函数

**count**

> count返回行的总数
>
> count(*) 返回满足条件的记录的行数
>
> count(列): 统计满足条件的某列有多少个，会排除 为 null 的情况

```sql
SELECT *|[colunm,...] from table_name 
	WHERE...
```



```sql
-- 统计一个班级有多少学生
SELECT COUNT(*) FROM student

-- 统计数学成绩大于90的学生有多少个
SELECT COUNT(*) FROM student
	WHERE math>90
	
-- 统计总分大于250的人数
SELECT COUNT(*) FROM student
	WHERE (chinese+math+english)>250
	
CREATE TABLE t13 (
`name` VARCHAR(20));
INSERT INTO t13 VALUES('tom');
INSERT INTO t13 VALUES('jack');
INSERT INTO t13 VALUES('mary');
INSERT INTO t13 VALUES(NULL);
SELECT * FROM t13;

SELECT COUNT(*) FROM t13 -- 4
SELECT COUNT(name) FROM t13 -- 3
```





**sum**

> sum函数返回满足where条件的行和列，一般用在数值列

```sql
SELECT sum(列名) {, sum(列名)} from table_name
	where...
```



```sql
- 统计一个班数学总成绩
SELECT SUM(math) from student

-- 统计一个班语文、英文、数学各科总成绩
SELECT SUM(chinese) as ch_tot, SUM(english) as en_tot, SUM(math) as math_tot from student

-- 统计一个班语文、英语、数学成绩综合
SELECT SUM(chinese+english+math) as total FROM student

-- 统计一个班语文成绩平均分
SELECT SUM(chinese)/COUNT(*) FROM student
```





**avg**

> avg函数返回满足where条件的一列的平均值

`SLEECT avg(列名) {, avg(列名)...} FROM tablename WHERE...`

```sql
-- 求一个班级数学平均分
SELECT AVG(math) FROM student

-- 求一个班级总分平均分
SELECT AVG(math+chinese+english) FROM student
```





**max/min**

> max/min函数返回满足where条件的一列的最大/最小值

`SELECT max(列名) FROM tablename WHERE...`

```sql
-- 求班级最高分和最低分
SELECT MAX(math+chinese+english), MIN(math+chinese+english) FROM student

-- 求出班级数学最高分和最低分
SELECT MAX(math), MIN(math) FROM student
```





### 3.6 分组统计

**group by 对列进行分组**

`SELECT column1, column2... FROM tablename group by column`



**having对分组结果过滤**

`SELECT column1, column2,... FROM tablename group by column having...`



> group by 用于对查询的结果分组统计
>
> having子句用于限制分组显示结果



```sql
-- 查询每个部门的平均工资和最高工资
-- 使用数学方法，对小数点进行处理
SELECT FORMAT(AVG(sal), 2), FORMAT(MAX(sal), 2), deptno FROM emp
	GROUP BY(deptno)

-- 查询示每个部门的每种岗位的平均工资和最低工资
SELECT AVG(sal), MIN(sal), deptno, job FROM emp
	GROUP BY deptno, job
	
-- 查询平均工资低于 2000 的部门号和它的平均工资
SELECT deptno, AVG(sal) as avg_sal FROM emp
	group by deptno
	having avg_sal < 2000
```



```sql
-- 显示每种岗位的雇员总数，平均工资
SELECT job, COUNT(*), AVG(sal) FROM emp
	GROUP BY job

-- 显示雇员总数以及获得补助的雇员数
SELECT COUNT(*), COUNT(comm) FROM emp

-- 统计雇员总数以及没有获得补助的雇员数
SELECT COUNT(*), COUNT(IF(comm IS NULL, 1, NULL)) FROM emp

SELECT COUNT(*), COUNT(*) - COUNT(comm) FROM emp

-- 显示管理者的总人数
SELECT COUNT(DISTINCT mgr) FROM emp

-- 显示雇员工资的最大差额
SELECT MAX(sal) - MIN(sal) FROM emp;
```





**数据分组的总结**

- 如果`select`语句同时包含有`group by`，`having`，`limit`，`order by`，那么他们的顺序是`where`、`group by`，`having`，`order by`，`limit`

```sql
-- 统计各个部门的平均工资，且大于1000，从高到低排序，取出前两行
SELECT deptno, AVG(sal) as avg_sal FROM emp
	WHERE xxx
	GROUP BY deptno
	HAVING avg_sal > 1000
	ORDER BY avg_sal DESC
	limit 0, 2
```





### 3.7 字符串相关函数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305031832061.png)



```SQL
-- 首字母小写的方式显示员工姓名
SELECT CONCAT(LCASE(LEFT(ename, 1)), SUBSTRING(ename, 2)) FROM emp

-- CHARSET(str) 返回字串字符集
SELECT CHARSET(ename) FROM emp -- utf8

-- - CONCAT (string2 [,... ]) 连接字串, 将多个列拼接成一列
SELECT CONCAT(ename,' 工作是 ',job) FROM emp

-- INSTR (string ,substring ) 返回 substring 在 string 中出现的位置,没有则返回0
-- DUAL 亚元表，系统表 可以作为测试表使用
SELECT INSTR('xuruijie', 'jie') FROM DUAL

-- UCASE(str) 转大写
SELECT UCASE('abcde')

-- LCASE(str) 转小写
SELECT LCASE('ABCDE')

SELECT LCASE(ename) FROM emp

-- LEFT (string2 ,length )从 string2 中的左边起取 length 个字符
-- RIGHT (string2 ,length ) 从 string2 中的右边起取 length 个字符
SELECT LEFT('abcdefg', 3) 

-- LENGTH (string )string 长度[按照字节]
SELECT LENGTH('ABC') -- 3
SELECT LENGTH('你好') -- 6


-- REPLACE (str, search_str, replace_str) 
-- 在str中用replace_str替换search_str
SELECT ename, REPLACE(job, 'MANAGER', '经理') FROM emp

-- STRCMP (string1 ,string2 ) 逐字符比较两字串大小
SELECT STRCMP('abc', 'acd')

-- SUBSTRING (str , position [,length ]
-- 从str的position开始【从1开始算】，截取length个字符
SELECT SUBSTRING(ename, 1, 2) FROM emp

-- LTRIM(string2)   RTRIM(string2)  TRIM(string)
-- 去除前端空格、后端空格、两端空格
SELECT LTRIM('  A   C  ')
SELECT RTRIM('  A   C  ')
SELECT TRIM('  A   C  ')
```





### 3.8 数学相关函数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305031949488.png)

```SQL
-- ABS(num) 绝对值
SELECT abs(-10)

-- BIN(num) 十进制转二进制
SELECT BIN(10)

-- CEILING  向上取整
SELECT CEILING(-1.1) -- -1
SELECT CEILING(4.2) -- 5

-- FLOOR  向下取整
SELECT FLOOR(2.3)  -- 2
SELECT FLOOR(-2.3)  -- -3

-- CONV(num, from_base, to_base)  进制转换
SELECT CONV(8, 10, 2)

-- FORMAT(num, decimal_places) 保留小数点位数(四舍五入)
SELECT FORMAT(78.12345, 2)

-- HEX(num) 转16进制
SELECT HEX(12)

-- LEAST(num1, num2,...)  求最小值
SELECT LEAST(0, 5, -5, 8)

-- MOD(num, mod) 求余数
SELECT MOD(10, 3)

-- RAND([seed])  返回0-1的随机数
SELECT RAND(1)
```







### 3.9 时间日期相关函数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305031956233.png)



```sql
-- 当前日期
SELECT CURRENT_DATE

-- 当前时间
SELECT CURRENT_TIME

-- 当前时间戳
SELECT CURRENT_TIMESTAMP

CREATE TABLE mes(
	id INT ,
	content VARCHAR(30), 
   	send_time DATETIME);

-- NOW()和CURRENT_TIMESTAMP()一样都是返回当前时间
-- 但是CURRENT_TIMESTAMP()在创建表时可以设置更新表时，时间戳自动更新
INSERT INTO mes VALUES(1, '北京新闻', CURRENT_TIMESTAMP());
INSERT INTO mes VALUES(2, '上海新闻', NOW());
INSERT INTO mes VALUES(3, '广州新闻', NOW());

SELECT now()

-- 显示所有新闻信息，发布日期只显示 日期，不用显示时间.
SELECT content, DATE(send_time) FROM mes

-- 查询10分钟内发布的消息
SELECT * FROM mes
	WHERE DATE_SUB(NOW(), INTERVAL 10 MINUTE) < send_time

SELECT * FROM mes
	WHERE DATE_ADD(send_time, INTERVAL 10 MINUTE) > NOW()
	
-- 求出 2011-11-11 和 1990-1-1 相差多少
SELECT DATEDIFF('2011-11-11', '1990-1-1')

-- 求出活了多少天
SELECT DATEDIFF(NOW(), '1999-10-26')

-- 如果能活 80 岁，求出你还能活多少天.
SELECT DATEDIFF(DATE_ADD('1999-10-26', INTERVAL 80 YEAR), NOW())

-- 求时间差
SELECT TIMEDIFF('10:10:10', '11:10:9')

-- 求当前时间的年份
SELECT YEAR(NOW())

-- 求当前时间的月
SELECT MONTH(NOW())

-- 求当前时间的日
SELECT DAY(NOW())

-- : 返回的是 1970-1-1 到现在的秒数
SELECT UNIX_TIMESTAMP()

-- FROM_UNIXTIME：可以把一个 unix_timestamp 秒数[时间戳]，转成指定格式的日期
SELECT FROM_UNIXTIME(UNIX_TIMESTAMP(), '%Y-%m-%d')
```





### 3.10 加密和系统函数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305032035290.png)



```sql
-- USER() 查询用户
-- 可以查看登录到mysql的有哪些用户，以及登陆的ip
SELECT USER() 	-- 用户名@ip

-- DATABASE() 查询当前使用的数据库名称
SELECT DATABASE()

-- MD5(str)为字符串计算一个32位的密文
SELECT MD5('root')
```





### 3.11 流程控制函数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305032046682.png)



```sql
SELECT IF(TRUE, '北京', '上海')
SELECT IF(FALSE, '北京', '上海')

SELECT IFNULL(NULL, 'ABC')
SELECT IFNULL('123', 'ABC')

SELECT CASE
	WHEN TRUE THEN 'xrj'
	WHEN FALSE THEN 'xxx'
	ELSE '123' END
	
	
-- 查询emp表，如果comm是null，则显示0.0
SELECT ename, IF(comm is NULL, 0.0, comm) FROM emp
SELECT ename, IFNULL(comm, 0.0) FROM emp

-- 如果emp表的job是CLERK则显示职员，如果是MANAGER则显示经理，如果是SALESMAN则显示销售人员，其他正常显示
SELECT ename, (SELECT CASE
	WHEN job='CLERK' THEN '职员'
	WHEN job='MANAGER' THEN '经理'
	WHEN job='SALESMAN' THEN '销售人员'
	ELSE job END) AS job 
	FROM emp
```





### 3.12 mysql多表查询

```SQL
-- 显示雇员名，雇员工资以及所在部门名字
SELECT ename, sal, dname FROM emp, dept
	WHERE emp.deptno = dept.deptno
	
-- 显示部门号为10的部门名、员工名和工资
SELECT emp.deptno, dname, ename, sal FROM emp, dept
	WHERE emp.deptno = 10 and emp.deptno = dept.deptno
	
-- 显示各个员工的姓名、工资、工资级别
SELECT ename, sal, grade FROM emp, salgrade
	WHERE sal BETWEEN losal and hisal

-- 显示雇员名、雇员工资以及所在部门名字，并按部门排序【降序】
SELECT ename, sal, dname FROM emp, dept
	WHERE emp.deptno = dept.deptno
	ORDER BY dname DESC
```





#### 自连接

> 自连接是指在同一张表的连接查询【将一张表看做两张表】

```sql
-- 显示公司员工和他上级名字
SELECT worker.ename as 'work', boss.ename as 'boss'
	FROM emp worker, emp boss
	WHERE worker.mgr = boss.empno
```





#### 子查询

> 子查询是指嵌入在其它 sql 语句中的 select 语句,也叫嵌套查询



**单行子查询**

> 单行子查询是指只返回一行数据的子查询语句

```sql
-- 显示与SMITH一个部门的所有员工
SELECT * FROM emp
	WHERE deptno = (SELECT deptno FROM emp WHERE ename = 'SMITH')
```



**多行子查询**

> 多行子查询指返回多行数据的子查询, 使用关键字 `in`

```SQL
-- 查询和部门10的工作相同的雇员的名字、岗位、工资、部门号，但是不包含10号部门
SELECT ename, job, sal, deptno FROM emp
	WHERE job in (SELECT DISTINCT job FROM emp WHERE deptno = 10) AND deptno != 10
```





**子查询当做临时表使用**

```sql
-- 查询ecsshop中各个类别，价格最高的商品名称
SELECT cat_id, MAX(shop_price) FROM ecs_goods
	GROUP BY cat_id
	
-- 将上边的表当做一个临时表
SELECT goods_id, ecs_goods.cat_id, goods_name, shop_price
	FROM (SELECT cat_id, MAX(shop_price) as high_price FROM ecs_goods 
				GROUP BY cat_id) AS temp, ecs_goods
	WHERE temp.cat_id = ecs_goods.cat_id AND temp.high_price = ecs_goods.shop_price
```





**all**

```SQL
-- 显示工资比部门30所有员工的工资都高的员工的姓名、工资、部门号
SELECT ename, sal, deptno FROM emp WHERE sal > all(SELECT sal FROM emp WHERE deptno = 30)

SELECT ename, sal, deptno FROM emp WHERE sal > (SELECT MAX(sal) FROM emp WHERE deptno = 30)
```





**any**

```SQL
-- 显示工资比部门 30 的其中一个员工的工资高的员工的姓名、工资和部门号
SELECT ename, sal, deptno FROM emp WHERE sal > any(SELECT sal FROM emp WHERE deptno = 30)

SELECT ename, sal, deptno FROM emp WHERE sal > (SELECT MIN(sal) FROM emp WHERE deptno = 30)
```





**多列子查询**

```sql
-- 查询和WARD的部门和岗位相同的 所有员工，不含WARD本人
-- 使用临时表的方式
SELECT * FROM emp, (SELECT deptno, job FROM emp WHERE ename = 'WARD') AS temp
	WHERE emp.deptno = temp.deptno AND emp.job = temp.job AND ename != 'WARD'

-- 使用多列子查询
SELECT * FROM emp
	WHERE (deptno, job) = (SELECT deptno, job FROM emp WHERE ename = 'WARD')
	AND ename != 'WARD'
	
	
-- 查询和宋江数学、语文、英语成绩完全相同的学生
SELECT * FROM student 
	WHERE (math, chinese, english) = (SELECT math, chinese, english FROM student WHERE name = '宋江')
	AND name != '宋江'
```







### 3.13 表复制

**表复制**

**删除表的重复记录**

```sql
CREATE TABLE my_tab01 ( 
	id INT, 
	`name` VARCHAR ( 32 ), 
	sal DOUBLE, 
	job VARCHAR ( 32 ), 
	deptno INT );
	
DESC my_tab01 

SELECT * FROM my_tab01

-- 将emp表数据复制到my_tab01
INSERT INTO my_tab01
	SELECT empno, ename, sal, job, deptno FROM emp
	
-- 自我复制
INSERT INTO my_tab01
	SELECT * FROM my_tab01
	
	
-- 删除一张表重复记录
-- 1.先创建一张重复表
CREATE TABLE my_tab02 LIKE emp -- 把emp表的结构复制到my_tab02

DESC my_tab02

INSERT INTO my_tab02
	SELECT * FROM emp

SELECT * FROM my_tab02

-- 2.去重
-- (1)创建一张临时表temp
CREATE TABLE temp LIKE my_tab02

-- (2)将my_tab02的数据DISTINC后复制到temp
INSERT INTO temp
	SELECT DISTINCT * FROM my_tab02
	
SELECT * FROM temp
	
-- (3)删除my_tab02的数据
DELETE FROM my_tab02

-- (4)将temp数据复制到my_tab02
INSERT INTO my_tab02
	SELECT * FROM temp

-- (5)删除temp表
DROP TABLE temp
```





### 3.14 合并查询

> 合并多个`select`语句的结果，可以使用集合操作符号`union`，`union all`

1、`union all`：取得两个结果集的并集，不去重

2、`union`：和`union all`一样，但是会去重

```sql
SELECT ename,sal,job FROM emp WHERE sal > 2500

SELECT ename,sal,job FROM emp WHERE job='MANAGER'

-- union all 将两个查询结果合并，不会去重
SELECT ename,sal,job FROM emp WHERE sal > 2500
UNION ALL
SELECT ename,sal,job FROM emp WHERE job='MANAGER'

-- union 将两个查询结果合并，并去重
SELECT ename,sal,job FROM emp WHERE sal > 2500
UNION 
SELECT ename,sal,job FROM emp WHERE job='MANAGER'
```





### 3.15 外连接

> 前边的多表查询是利用`where`子句对两张表或多张表形成的笛卡尔积进行筛选，根据关联条件，显示匹配的记录，匹配不上的不显示

**左外连接：**左侧的表完全显示

**右外连接：**右侧的表完全显示

```sql
CREATE TABLE stu (
id INT, `name` VARCHAR(32));

INSERT INTO stu VALUES(1, 'jack'),(2,'tom'),(3, 'kity'),(4, 'nono');

SELECT * FROM stu;

CREATE TABLE exam(
id INT, grade INT);

INSERT INTO exam VALUES(1, 56),(2,76),(11, 8);

SELECT * FROM exam

-- 显示所有人的成绩，如果没有成绩，也要显示该人的姓名和 id 号,成绩显示为空
SELECT stu.id, name, grade 
	FROM stu LEFT JOIN exam
	ON stu.id = exam.id
	
-- 显示所有成绩，如果没有名字匹配，显示空
SELECT stu.id, name, grade
	FROM stu RIGHT JOIN exam
	ON stu.id = exam.id
	
-- 内连接
SELECT stu.id, name, grade
	FROM stu INNER JOIN exam
	ON stu.id = exam.id



-- 列出部门名称和这些部门的员工信息(名字和工作),同时列出那些没有员工的部门名

-- 左连接
SELECT dname, ename, job
	FROM dept LEFT JOIN emp
	ON dept.deptno = emp.deptno
	
-- 右连接
SELECT dname, ename, job
	FROM emp RIGHT JOIN dept
	ON dept.deptno = emp.deptno
	
-- 内连接, 显示的是非空的
SELECT dname, ename, job
	FROM emp INNER JOIN dept
	ON dept.deptno = emp.deptno
```







### 3.16 mysql约束

> 约束用于确保数据库的数据满足特定的商业规则。在`mysql`中，约束包括：`not null`、`unique`、`primary key`、`foreign key`、`check`



#### `primary key`

`字段名 字段类型 primary key`



- `primary key`不能重复且不能为`null`
- 一张表只能有一个主键，但可以有复合主键
- 主键指定方式有两种
  - 直接在字段名后指定：`字段名 primary key`
  - 在表定义最后写：`primary key(列名)`

```sql
CREATE TABLE t14
	(id INT PRIMARY KEY, -- 表示 id 列是主键
	`name` VARCHAR(32),
	email VARCHAR(32));
	

-- 主键字段不能重复
INSERT INTO t14
	VALUES(1, 'xrj', 'xrj@163.com')
	
INSERT INTO t14
	VALUES(2, 'xyy', 'xyy@163.com')
	
-- 下面语句报错	
INSERT INTO t14
	VALUES(1, 'abc', 'abc@163.com')

-- 主键字段不能为空，下面语句报错
INSERT INTO t14 
	VALUES(NULL, 'zs', '123@163.com')



-- 一张表只能有一个主键，可以有复合主键
-- 下面的表id和name是复合主键，只有当id和name同时相同才不能插入
CREATE TABLE t15
	(id INT , 
	`name` VARCHAR(32), 
	email VARCHAR(32),
	PRIMARY KEY(id, `name`));
	
INSERT INTO t15
	VALUES(1, 'tom', 'tom@sohu.com');

INSERT INTO t15
	VALUES(1, 'jack', 'jack@sohu.com');

INSERT INTO t15
	VALUES(1, 'tom', 'xx@sohu.com'); -- 这里就违反了复合主键
	
SELECT * FROM t15;
	

-- 可以在表定义最后指定主键
CREATE TABLE t16
	(id INT , 
	`name` VARCHAR(32) , 
	email VARCHAR(32), 
	PRIMARY KEY(`name`));
	
DESC t16
```





#### `not null`

> 如果字段后定义了`not null`，插入数据时，该字段不能为空

`字段名 字段类型 nut null`





#### `unique`

> 当定义了唯一约束后，该字段的值不能重复，但是可以为null，可以有多个null

`字段名 字段类型 unique`



- 如果没有指定`nut null`，则`unique`字段可以有多个`null`
- 一张表可以有多个`unique`字段

```SQL
CREATE TABLE t17
	(id INT UNIQUE , -- 表示 id 列是不可以重复的. 
	`name` VARCHAR(32), 
	email VARCHAR(32));

INSERT INTO t17
	VALUES(1, 'tom', 'tom.163.com')
	
INSERT INTO t17
	VALUES(2, 'jack', 'jack.163.com')
	
-- 报错，id不能重复
INSERT INTO t17
	VALUES(1, 'xrj', 'xrj.163.com')
	
SELECT * FROM t17

-- 如果没有指定not null, unique字段可以有多个null
INSERT INTO t17
	VALUES(NULL, 'aaa', 'aaa.163.com')
	
INSERT INTO t17
	VALUES(NULL, 'bbb', 'bbb.163.com')
	
	
-- 一张表可以有多个unique字段
CREATE TABLE t18
	( id INT UNIQUE,
		`name` VARCHAR(32) UNIQUE,
		email VARCHAR(32));

DESC t18
```





#### `foreign key`

> 用于定义主表和从表之间的关系：外键约束定义在从表上，主表必须有主键约束或是`unique`约束。当定义外键约束后，要求外键列数据必须在主表的主键列存在或为null

`FOREIGN KEY(本表字段名) REFERENCES 主表名(主键名或unique字段)`



- 外键指向的表的字段，要求是`primary key`或者是`unique`
- 表的类型是`innodb`【引擎】，这样的表才支持外键
- 外键字段的类型要和主键字段的类型一致【长度可以不一样】
- 外键字段的值，必须在主键字段中出现过，或者为`null`【前提是外键字段允许为`null`】
- 建立外键关系后，数据就不能随意删除



```sql
-- 主表
CREATE TABLE myclass (
	id INT PRIMARY KEY , 
	`name` VARCHAR(32) NOT NULL DEFAULT ''); 


-- 从表 
CREATE TABLE mystu (
	id INT PRIMARY KEY ,
	`name` VARCHAR(32) NOT NULL DEFAULT '', 
	class_id INT,
	FOREIGN KEY(class_id) REFERENCES myclass(id));
	
INSERT INTO myclass
	VALUES(100, 'java'), (200, 'web'), (300, 'php');
	
SELECT * FROM myclass
	
INSERT INTO mystu
	VALUES(1, 's1', 100);

INSERT INTO mystu
	VALUES(2, 's2', 200);
	
INSERT INTO mystu
	VALUES(3, 's3', 300);
	
-- 报错，myclass的id没有400
INSERT INTO mystu
	VALUES(4, 's4', 400);
	
SELECT * FROM mystu

-- 外键字段可以用null
INSERT INTO mystu
	VALUES(5, 's5', NULL)

-- 此时100班级有mystu使用，所以无法删除，除非将mystu中使用100的数据删除掉，这个数据才能删除
DELETE FROM myclass
	WHERE `name` = 100
```





#### `check`

> 用于强制数据必须满足的条件

`字段名 字段类型 check (check 条件)`

```sql
CREATE TABLE t19 (
	id INT PRIMARY KEY, 
	`name` VARCHAR(32), 
	sex VARCHAR(6) CHECK (sex in('man', 'woman')),
	sal DOUBLE CHECK (sal > 1000 AND sal < 2000));
	
INSERT INTO t19
	VALUES(100, 'xyy', 'woman', 1500)
	
-- 报错
INSERT INTO t19
	VALUES(200, 'xrj', '男', 5000)
	
SELECT * FROM t19
```





### 3.17 自增长

> 在某张表中，存在一个id列（整数），我们希望在添加记录的时候，该列从1开始，自动增长，这时就用到了`mysql`的自增长

`字段名 INT PRIMARY KEY AUTO_INCREMENT`



添加自增长的字段方式

`insert into table_name (字段1, 字段2...) values(null, 值1...)`

`insert into table_name (字段2...) values(值2...)`

`insert into table_name values(null, 值1...)`



- 一般来说自增长是和`PRIMARY KEY`配合使用的
- 自增长也可以单独使用【但是要配合一个`UNIQUE`，否则会报错】

- 自增长修饰的字段为整型【小数也可以】
- 自增长默认从1开始。可以通过`alter table 表名 auto_creament = 新的开始值` 修改
- 如果添加数据时，给自增长字段指定的有值，则以指定的值为准。继续添加时，自增长字段会以表中该字段最大值开始继续增长



```sql
CREATE TABLE t20 (
	id INT PRIMARY KEY AUTO_INCREMENT, 
	email VARCHAR(32)NOT NULL DEFAULT '', 
	`name` VARCHAR(32)NOT NULL DEFAULT '');

SELECT * FROM t20

INSERT INTO t20
	VALUES(NULL, 'aaa', 'xrj')
	
INSERT INTO t20(email, name)
	VALUES('BBB', 'XYY')
	
INSERT INTO t20(id, email, name)
	VALUES(NULL, 'CCC', 'CCC')
	
ALTER TABLE t20 AUTO_INCREMENT = 500
```







## 4、索引

### 4.1 索引介绍

> 当我们查找一张数据量很大的表时，因为数据太多，查询时一条一条查询，速度会很慢，这时就需要使用索引来加速查找

```sql
CREATE TABLE dept( /*部门表*/
deptno MEDIUMINT   UNSIGNED  NOT NULL  DEFAULT 0,
dname VARCHAR(20)  NOT NULL  DEFAULT "",
loc VARCHAR(13) NOT NULL DEFAULT ""
) ;

#创建表EMP雇员
CREATE TABLE emp
(empno  MEDIUMINT UNSIGNED  NOT NULL  DEFAULT 0, /*编号*/
ename VARCHAR(20) NOT NULL DEFAULT "", /*名字*/
job VARCHAR(9) NOT NULL DEFAULT "",/*工作*/
mgr MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,/*上级编号*/
hiredate DATE NOT NULL,/*入职时间*/
sal DECIMAL(7,2)  NOT NULL,/*薪水*/
comm DECIMAL(7,2) NOT NULL,/*红利*/
deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0 /*部门编号*/
) ;

#工资级别表
CREATE TABLE salgrade
(
grade MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
losal DECIMAL(17,2)  NOT NULL,
hisal DECIMAL(17,2)  NOT NULL
);

#测试数据
INSERT INTO salgrade VALUES (1,700,1200);
INSERT INTO salgrade VALUES (2,1201,1400);
INSERT INTO salgrade VALUES (3,1401,2000);
INSERT INTO salgrade VALUES (4,2001,3000);
INSERT INTO salgrade VALUES (5,3001,9999);

delimiter $$

#创建一个函数，名字 rand_string，可以随机返回我指定的个数字符串
create function rand_string(n INT)
returns varchar(255) #该函数会返回一个字符串
begin
#定义了一个变量 chars_str， 类型  varchar(100)
#默认给 chars_str 初始值   'abcdefghijklmnopqrstuvwxyzABCDEFJHIJKLMNOPQRSTUVWXYZ'
 declare chars_str varchar(100) default
   'abcdefghijklmnopqrstuvwxyzABCDEFJHIJKLMNOPQRSTUVWXYZ'; 
 declare return_str varchar(255) default '';
 declare i int default 0; 
 while i < n do
    # concat 函数 : 连接函数mysql函数
   set return_str =concat(return_str,substring(chars_str,floor(1+rand()*52),1));
   set i = i + 1;
   end while;
  return return_str;
  end $$


#这里我们又自定了一个函数,返回一个随机的部门号
create function rand_num( )
returns int(5)
begin
declare i int default 0;
set i = floor(10+rand()*500);
return i;
end $$

 #创建一个存储过程， 可以添加雇员
create procedure insert_emp(in start int(10),in max_num int(10))
begin
declare i int default 0;
#set autocommit =0 把autocommit设置成0
 #autocommit = 0 含义: 不要自动提交
 set autocommit = 0; #默认不提交sql语句
 repeat
 set i = i + 1;
 #通过前面写的函数随机产生字符串和部门编号，然后加入到emp表
 insert into emp values ((start+i) ,rand_string(6),'SALESMAN',0001,curdate(),2000,400,rand_num());
  until i = max_num
 end repeat;
 #commit整体提交所有sql语句，提高效率
   commit;
 end $$

 #添加8000000数据
call insert_emp(100001,8000000)$$

#命令结束符，再重新设置为;
delimiter ;



-- 不加索引进行查找 9秒，此时表的大小为524M
SELECT * FROM emp
	WHERE empno = 100002  
	
-- 对empno字段创建索引,创建索引后表的大小变为655M。创建的索引只对当前字段有效
CREATE INDEX empno_index ON emp(empno)

-- 创建索引后再次查找 0.098秒
SELECT * FROM emp
	WHERE empno = 100002  
	
	
-- 对ename字段进行查找 5.2秒
SELECT * FROM emp
	WHERE ename = 'krcokj'
	
-- 对ename字段创建索引，创建索引后表的大小变为827M
CREATE INDEX ename_index ON emp(ename)

-- 创建索引后再次查找 0.383秒
SELECT * FROM emp
	WHERE ename = 'krcokj'
```







### 4.2 索引的原理

**索引好处**

没有索引查找时很慢：因为要对全表进行扫描

使用索引后查找变快：形成一个索引的数据结构，比如二叉树

**`mysql`中索引底层是B+树**



**索引的代价**

1、磁盘占用

2、执行`update`、`insert`、`delete`语句时会影响效率【因为需要修改索引的结构】



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305061629836.png)



> 在任何数据库当中，任何一张表的任何一条记录在硬盘存储上都有一个硬盘的物理存储编号。
>
> 在`mysql`当中，索引是一个单独的对象，不同的存储引擎以不同的形式存在，在`MyISAM`存储引擎中，索引存储在一个`.MYI`文件中。
>
> 在`InnoDB`存储引擎中，索引存储在一个逻辑名称叫做`tablespace`的当中。
>
> 在`MEMORY`存储引擎当中，索引被存储在内存当中。
>
> 不管索引存储在哪里，索引在`mysql`当中都是一个树的形式存在。
>
> 当对一个字段建立索引后，通过该字段进行查询，会直接通过他的索引查询，索引在建立时，根据B+树来建立，同时会带上该行数据的磁盘地址，所以通过索引查询到数据后，就直接拿到了该数据在磁盘的位置，即可直接读取到数据





### 4.3 索引的类型

- 主键索引：主键自动作为索引（类型`PRIMARY KEY`）
- 唯一索引（`UNIQUE`）
- 普通索引（`INDEX`）
- 全文索引（`FULLTEXT`）【适用于`MylSAM`】。一般不使用`mysql`自带的全文索引，而是使用全文搜索`Solr`和`ElasticSearch (ES)`

```sql
CREATE TABLE t22 (
	id INT PRIMARY KEY, -- 主键，同时也是索引
	name VARCHAR(32));
	
CREATE TABLE t23 (
	id INT UNIQUE, -- 唯一性约束修饰的字段，也是索引
	name VARCHAR(32));
```



**添加索引**

`CREATE [UNIQUE] INDEX inedex_name ON table_name(列名) `

`ALTER TABLE table_name ADD INDEX [index_name](列名)`



**添加主键索引**

`ALTER TABLE table_name ADD PRIMARY KEY(列名)`



**删除索引**

`DROP INDEX index_name ON 表名`

`ALTER TABLE table_name DROP INDEX index_name`



**删除主键索引**

`ALTER TABLE table_name DROP PRIMARY KEY`



**修改索引**：先删除，再添加



**查询索引**

`SHOW INDEX[ES] FROM table_name`

`SHOW KEYS FROM table_name`

```sql
-- 主键索引
CREATE TABLE t22 (
	id INT PRIMARY KEY, -- 主键，同时也是索引
	name VARCHAR(32));
	
SHOW INDEX FROM t22


CREATE TABLE t24(
	id INT,
	name VARCHAR(32));

-- 添加主键索引
ALTER TABLE t24 ADD PRIMARY KEY(id); -- id设置为主键，即是主键索引
-- 删除主键索引
ALTER TABLE t24 DROP PRIMARY KEY;

-- 添加普通索引
CREATE INDEX name_index ON t24(name)
-- 删除普通索引
ALTER TABLE t24 DROP INDEX name_index


-- 唯一索引
CREATE TABLE t23 (
	id INT UNIQUE, -- 唯一性约束修饰的字段，也是索引
	name VARCHAR(32));

SHOW INDEX FROM t23

CREATE TABLE t25 (
	id INT,
	NAME varchar(32));

SHOW INDEX FROM t25;

CREATE UNIQUE INDEX id_index ON t25(id) -- 创建唯一索引，同时id字段被添加UNIQUE约束

ALTER TABLE t25 ADD UNIQUE INDEX name_index(NAME) -- 创建唯一索引，同时name字段被添加UNIQUE约束
```



- 较频繁的作为查询条件的字段应创建索引
- 唯一性太差的字段不适合创建索引
- 更新非常频繁的字段不适合创建索引
- 不会出现在`where`条件里的字段不该创建索引





### 4.4 索引失效

在`mysql`中，可以通过`explain select * from emp`，`explain`可以查看该条语句如何查询，`type`为`ALL`即是全表查询

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305091517364.png)

建立索引后，再查看，`type`为`ref`，即索引查询

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305091520084.png)





**索引失效**

1、当模糊匹配中以`%`开头

```sql
EXPLAIN SELECT * FROM emp WHERE ename LIKE '%M'; -- ALL
```

2、使用`or`的时候如果`or`的两边都有索引，那么会通过索引查询，否则，索引会失效

```sql
EXPLAIN SELECT * FROM emp WHERE ename = 'SMITH' or empno = '7369';   -- ename和empno都有索引，不会失效
EXPLAIN SELECT * FROM emp WHERE ename = 'SMITH' or job = 'MANAGER'; -- job没有索引，所以索引失效
```

3、使用复合索引的时候，没有使用左侧的列查找，索引失效

```sql
CREATE INDEX job_sal_index ON emp(job, sal);
EXPLAIN SELECT * FROM emp WHERE job = 'MANAGER';  -- ref
EXPLAIN SELECT * FROM emp WHERE sal > 5000;  -- ALL
```

4、在where当中索引列参加了运算，索引失效

```sql
CREATE INDEX sal_index ON emp(sal);
EXPLAIN SELECT * FROM emp WHERE sal = 5000; -- ref
EXPLAIN SELECT * FROM emp WHERE sal + 1000 = 5000; -- ALL
```

5、在where当中索引列使用了函数

```sql
EXPLAIN SELECT * FROM emp WHERE lower(ename) = 'smith'; -- ALL
```



```sql
EXPLAIN SELECT * FROM emp

SELECT * FROM emp

CREATE INDEX id_index ON emp(empno);
EXPLAIN SELECT * FROM emp WHERE empno = 7369;  -- ref


CREATE INDEX name_index ON emp(ename);
EXPLAIN SELECT * FROM emp WHERE ename = 'SM%' -- ref

-- 当模糊匹配中以%开头，索引就失效了
EXPLAIN SELECT * FROM emp WHERE ename LIKE '%M'; -- ALL


-- 使用`or`的时候如果`or`的两边都有索引，那么会通过索引查询，否则，索引会失效
EXPLAIN SELECT * FROM emp WHERE ename = 'SMITH' or empno = '7369';   -- ename和empno都有索引，不会失效
EXPLAIN SELECT * FROM emp WHERE ename = 'SMITH' or job = 'MANAGER'; -- job没有索引，所以索引失效


-- 使用复合索引的时候，没有使用左侧的列查找，索引失效
CREATE INDEX job_sal_index ON emp(job, sal);
EXPLAIN SELECT * FROM emp WHERE job = 'MANAGER';  -- ref
EXPLAIN SELECT * FROM emp WHERE sal > 5000;  -- ALL


-- 在where当中索引列参加了运算，索引失效。
CREATE INDEX sal_index ON emp(sal);
EXPLAIN SELECT * FROM emp WHERE sal = 5000; -- ref
EXPLAIN SELECT * FROM emp WHERE sal + 1000 = 5000; -- ALL


-- 在where当中索引列使用了函数
EXPLAIN SELECT * FROM emp WHERE lower(ename) = 'smith'; -- ALL


SHOW INDEX FROM emp;
```





## 5、事务

### 5.1 事务介绍

> 事务用于保证数据的一致性，他由一组相关的dml【update、delete、insert】语句组成，该组的dml语句要么全部成功，要么全部失败。例如：转账

​	

**事务和锁**

> 当执行事务操作的时候（dml语句），`mysql`会在表上加锁，防止其他用户修改表的数据



`mysql`数据库控制事务的几个操作

- `start transaction`：开始一个事务
- `savepoint 保存点名`：设置保存点
- `rollback to 保存点名`：回退事务

- `rollback`：回退全部事务
- `commit`：提交事务，所有操作生效，不能回退

```sql
CREATE TABLE t22( 
	id INT, 
	`name` VARCHAR(32));
	
-- 1.开始事务
START TRANSACTION

-- 2.设置保存点
SAVEPOINT a

-- 3.执行dml操作
INSERT INTO t22
	VALUES(1, 'xrj');
	
-- 4.设置保存点
SAVEPOINT b

-- 5.执行dml操作
INSERT INTO t22
	VALUES(2, 'xyy')
	
-- 回退到b
ROLLBACK TO b

-- 回退到a
ROLLBACK TO a

-- 回退到事务开始的状态
ROLLBACK

-- 提交事务，提交后，就不能再回滚
COMMIT

SELECT * FROM t22
```



**回退事务**

> 保存点是事务中的点，用于回滚部分操作。当结束事务时`commit`，会自动删除该事务所定义的所有保存点，当执行回退事务时，可以通过指定的保存点回退到指定的点



**提交事务**

> 使用`commit`语句可以提交事务。当执行了`commit`语句后，会确认事务的变化、结束事务、删除保存点、释放锁，数据生效。当使用`commit`语句结束事务后，其他会话【其他连接】将可以看到事务变化后的新数据【所有数据正式生效】





**事务细节**

- 如果不开始事务，默认情况下，`dml`操作是自动提交的，不能回滚
- 如果开始一个事物，但是没有创建保存点，可以执行`rollback`，默认回滚到事务开始的状态
- 可以在事务中创建多个保存点。`savepoint a;    执行dml	savepoint b;`
- 可以在事务没有提交前，选择回退到哪个保存点
- `mysql`的事务机制需要`innodb`的存储引擎才可以使用，`myisam`不能使用
- 开始一个事务：`start transaction`或者`set autocommit=off`





### 5.2 事务隔离级别

1、多个连接开启各自事务操作数据库中数据时，数据库系统要负责隔离操作，以保证各个连接在获取数据时的准确性

2、如果不考虑隔离性，会有如下问题

 - 脏读：当一个事务读取另一个事务尚未提交的操作【`delete`、`update`、`insert`】时，产生脏读

   > 脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。
   >
   > 例如：
   > 张三的工资为5000,事务A中把他的工资改为8000,但事务A尚未提交。
   > 与此同时，
   > 事务B正在读取张三的工资，读取到张三的工资为8000。
   > 随后，
   > 事务A发生异常，而回滚了事务。张三的工资又回滚为5000。
   > 最后，
   > 事务B读取到的张三工资为8000的数据即为脏数据，事务B做了一次脏读。

 - 不可重复读：同一查询在同一事务中执行多次，由于其他**提交事务**所做的修改或删除，每次返回不同的结果集，此时发生不可重复读

   > 在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。
   >
   > 例如：
   > 在事务A中，读取到张三的工资为5000，操作没有完成，事务还没提交。
   > 与此同时，
   > 事务B把张三的工资改为8000，并提交了事务。
   > 随后，
   > 在事务A中，再次读取张三的工资，此时工资变为8000。在一个事务中前后两次读取的结果不一致，导致了不可重复读。

 - 幻读：同一查询在同一事务执行多次，由于其他**提交事务**所做的插入操作，每次返回不同的结果集，此时发生幻读

   > 事务 A 对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。此时，突然事务 B 插入了一条数据并提交了，当事务 A 提交了修改数据操作之后，再次读取全部数据，结果发现还有一条数据未更新，给人感觉好像产生了幻觉一样



**事务隔离级别**

| 隔离级别                  | 脏读   | 不可重复读 | 幻读   | 加锁读 |
| ------------------------- | ------ | ---------- | ------ | ------ |
| 读未提交 Read uncommitted | 允许   | 允许       | 允许   | 不加锁 |
| 读已提交 Read committed   | 不允许 | 允许       | 允许   | 不加锁 |
| 可重复读 Repeatable read  | 不允许 | 不允许     | 不允许 | 不加锁 |
| 可串行化 Serializable     | 不允许 | 不允许     | 不允许 | 加锁   |

> 读未提交：假如A的隔离级别是读未提交，当B事务操作时，还没有提交事务，A是可以读取到的，会产生脏读
>
> 读已提交：假设A的隔离级别是读已提交，只有当B提交事务后，A才可以读到。A在B提交事务前读取了数据，在B提交事务后又读取了数据，两次可能不一致，会产生不可重复读
>
> 可重复读：假设A的隔离级别是可重复读，那么当A对数据操作时，他会对这些数据加锁，B无法操作，但是A只对数据加锁，并没有对表加锁，所以B可以添加数据，会产生幻读。但是在Mysql中，可重复读隔离级别保证了每个事务在操作时，数据一定是本次事务的，所以避免了幻读。
>
> `Serializable`：当A事务在执行且未提交时，B事务如果执行命令，会卡住，需要等待A事务commit后B事务的命令才可以执行。即A会对表加锁



`mysql`默认的隔离级别是`Repeatable read`

```SQL
-- 查看当前隔离级别
select @@transaction_isolation;
-- 查看系统当前隔离界别
select @@global.transaction_isolation;

+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+

-- 设置会话隔离级别
SET SESSION TRANSACTION ISOLATION LEVEL Read uncommitted
-- 设置系统隔离级别
SET GLOBAL TRANSACTION ISOLATION LEVEL Read uncommitted

-- 隔离级别全局修改
修改my.ini配置文件，在最后加上
[mysqld]
transaction-isolation = REPEATABLE READ
-- 可选参数有Read uncommitted、Read committed、Repeatable read、Serializable
```





### 5.3 事务的ACID特性

**1、原子性**

事务被视为不可分割的最小单元，事务的所有操作要么全部提交成功，要么全部失败回滚。
回滚可以用回滚日志来实现，回滚日志记录着事务所执行的修改操作，在回滚时反向执行这些修改操作即可。

**2、一致性**

数据库在事务执行前后都保持一致性状态。在一致性状态下，所有事务对一个数据的读取结果都是相同的。

**3、隔离性**

一个事务所做的修改在最终提交以前，对其它事务是不可见的。

**4、持久性**

一旦事务提交，则其所做的修改将会永远保存到数据库中。即使系统发生崩溃，事务执行的结果也不能丢失。使用重做日志来保证持久性。





### 5.4 mysql的锁

`InnoDB`存储引擎实现了如下两种标准的行级锁

- 共享锁（S Lock）：允许事务读一行数据
- 排它锁（X Lock）：允许事务删除或更新一行数据

|      | X      | S      |
| ---- | ------ | ------ |
| X    | 不兼容 | 不兼容 |
| S    | 不兼容 | 兼容   |





## 6、存储引擎

### 6.1 基本介绍

1、`mysql`的表类型由存储引擎决定，主要包括`MylSAM`、`innoDB`、`Memory`等

2、`mysql`表类型分为两类，一类是 **事务安全型**，比如`innoDB`。其余都属于第二类，**非事务安全型**



显示数据库支持的存储引擎
`show engines;`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305071648737.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305071649549.png)



**`MyISAM`**

> `MylSAM`不支持事务、也不支持外键，但其访问速度快，对事务完整性没有要求



**`InnoDB`**

> `InnoDB`存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全。但是比起`MylSAM`存储引擎，`InnoDB`写的处理效率差一些并且会占用更多的磁盘空间以保留数据和索引.
>



**`MEMORY`**

> `MEMORY`存储引擎使用存在内存中的内容来创建表。`MEMORY`类型的表访问非常得快，因为它的数据是放在内存中的，并且默认使用HASH索引。但是一旦`MySQL`服务关闭，表中的数据就会丢失掉，表的结构还在



```sql
-- 显示数据库支持的存储引擎
show engines;

-- innodb 存储引擎
-- 1.支持事务  2.支持外键  3.支持行级锁

-- myisam 存储索引
CREATE TABLE t23(
	id INT,
	`name` VARCHAR(32)) ENGINE MYISAM;
-- 1. 添加速度快 2. 不支持外键和事务 3. 支持表级锁

-- 不能使用事务操作
START TRANSACTION;
SAVEPOINT t;
INSERT INTO t23 VALUES(1, 'jack');
SELECT * FROM t23;
ROLLBACK TO t1

-- memory 存储引擎
-- 1. 数据存储在内存中【关闭了Mysql服务，数据丢失, 但是表结构还在】
-- 2. 执行速度很快(没有IO读写)  3. 默认支持索引(hash表)

CREATE TABLE t24 (
	id INT, 
	`name` VARCHAR(32)) ENGINE MEMORY;
	
DESC t24;

INSERT INTO t24
	VALUES(1,'tom'), (2,'jack'), (3, 'hsp');
	
SELECT * FROM t24;
```



1、如果你的应用不需要事务，处理的只是基本的CRUD操作，那么就选`MylSAM`，速度快

2、如果需要支持事务，选择`InnoDB`。

3、`Memory`存储引擎就是将数据存储在内存中，由于没有磁盘I/O的等待，速度极快。但由于是内存存储，所做的任何修改在服务器重启后都将消失。【经典用法：用户的在线状态】



- 修改存储引擎：`ALTER TABLE 表名 ENGIAN=存储引擎`





## 7、视图

一个需求

> emp表的列信息很多，有些信息是个人重要信息，不希望普通用户查看。那么我们就需要视图



1、视图是一个虚拟表，其内容由查询定义。和真实的表一样，视图包含列，数据来自其对应的真实表

2、视图是根据基表（一个或多个）创建的。通过视图可以修改基表的数据，基表的改变，也会影响视图的数据

3、创建视图后，到数据库去看，对应视图只有一个视图结构文件(形式: 视图名.frm)

4、视图中可以再使用视图



**视图的基本使用**

1、`create view 视图名 as select语句`：创建视图

2、`alter view 视图名 as select语句`：更新成新的视图

3、`SHOW CREATE VIEW 视图名`：查看创建的视图

4、`drop view 视图名1, 视图名2`：删除视图

```sql
-- 创建一个视图 emp_view，只能查询 emp 表的(empno、ename, job 和 deptno ) 信息
CREATE VIEW emp_view AS 
	SELECT empno, ename, job, deptno FROM emp;
	
-- 查看视图
DESC emp_view

SELECT * FROM emp_view;
SELECT empno, job FROM emp_view;


-- 查看创建的视图
SHOW CREATE VIEW emp_view;

-- 删除视图
DROP VIEW emp_view;


-- 修改视图，基表也会改变
UPDATE emp_view
	SET job = 'stu' 
	WHERE empno = '7369';
SELECT * FROM emp;

-- 修改基本表，会影响视图
UPDATE emp
	SET job = 'aaa'
	WHERE empno = 7369
	
-- 视图中可以再使用视图
CREATE VIEW emp_view0 
	AS SELECT empno, job FROM emp_view;
	
SELECT * FROM emp_view0

-- 针对 emp ，dept , 和 salgrade 三张表.创建一个视图 emp_view03，
-- 可以显示雇员编号，雇员名，雇员部门名称和 薪水级别[即使用三张表，构建一个视图
CREATE VIEW emp_view03 
	AS SELECT empno, ename, dname, grade 
	FROM emp, dept, salgrade
	WHERE emp.deptno = dept.deptno AND emp.sal BETWEEN salgrade.losal AND salgrade.hisal;
	
SElECT * FROM emp_view03
```







**视图用处**

1、**安全**。一些数据表有着重要的信息。有些字段是保密的，不能让用户直接看到。这时就可以创建一个视图，在这张视图中只保留一部分字段。这样，用户就可以查询自己需要的字段,不能查看保密的字段。

2、**性能**。关系数据库的数据常常会分表存储，使用外键建立这些表的之间关系。这时数据库查询通常会用到连接（JOIN)。这样做不但麻烦，效率相对也比较低。如果建立一个视图，将相关的表和字段组合在一起，就可以避免使用JOIN查询数据。

3、**灵活**。如果系统中有一张旧的表，这张表由于设计的问题，即将被废弃。然而，很多应用都是基于这张表，不易修改。这时就可以建立一张视图,视图中的数据直接映射到新建的表。这样，就可以少做很多改动，也达到了升级数据表的目的。







## 8、Mysql管理

`mysql`中的用户，都存储在数据库`mysql`的`user`表

其中，`user`表的字段：

- `host`：允许登录的位置，`localhost`表示该用户只允许本机登录，也可以指定ip
- `user`：用户名
- `authentication_string`：密码，存储的是通过`mysql`加密后的密码



**创建用户**

`create user ‘用户名’@'允许登录位置' identified by ‘密码’`：创建用户，同时指定密码



**删除用户**

`drop user ‘用户名’@'允许登录位置'`



**用户修改密码**

`set password = 'xxx'` ：修改自己的密码

`set password for 用户名@登录位置 = 'xxx'`：修改他人密码【需要有修改用户密码的权限】





### 8.1 mysql中的权限

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305082148740.png)



**给用户授权**

`grant 权限列表 on 库.对象名 to '用户名'@'登录位置' [identified by 密码]`

> 1、权限列表，多个权限用逗号分开
>
> `grant select on...`
>
> `grant select, insert on...`
>
> `grant all on... `  表示赋予该用户在该对象上的所有权限
>
> 2、库.对象名
>
> \*.\*：表示本系统中所有数据库的所有对象（表、视图、存储过程）
>
> 库.*：表示某个数据库中的所有数据对象（表、视图、存储过程等）
>
> 3、`identified by 密码 ` 可以省略，也可以写出
>
> - 如果用户存在，就是修改用户密码
> - 如果用户不存在，就是创建该用户



**回收用户权限**

`revoke 权限列表 on 库.对象名 from 用户名@登录位置`



**细节说明**

1、在创建用户时，如果不指定`host`，则为`%`，`%`表示所有ip都有连接权限

2、`create user xxx@192.168.1.% `：表示xxx用户在192.168.1.*的ip可以登录

3、在删除用户时，如果`host`不是`%`，需要明确指定 `用户@host值`

```sql
select * from mysql.user

-- 创建用户
CREATE USER 'xrj'@'localhost' identified by '990918'

-- 修改用户密码
set password for xrj@localhost = '123456'

-- 删除用户
drop user xrj@localhost



-- 创建用户 ruijie 密码 123 , 从本地登录
CREATE USER ruijie@localhost IDENTIFIED BY '123'

-- 使用root用户创建testdb，表news
CREATE DATABASE testdb
CREATE TABLE testdb.news(
	id INT,
	content VARCHAR(32));

insert into testdb.news values(100, '北京新闻');

SELECT * FROM testdb.news

-- 给ruijie添加select insert权限
grant select, insert 
	on testdb.news
	to ruijie@localhost
	
grant update 
	on testdb.news 
	to ruijie@localhost 
	
revoke update 
	on testdb.news 
	from ruijie@localhost
	
revoke all 
	on table testdb.news 
	from ruijie@localhost
	
-- 创建用户的时候，如果不指定 Host, 则为% , %表示表示所有ip都可连接
-- 没设置密码，不需输密码直接登录
create user jack;

drop user jack;

create user jack@'192.168.1.%';

drop user jack@'192.168.1.%';
```







## 9、数据库三大范式

**第一范式**：要求任何一张表必须有主键，每一个字段原子性不可再分

```
第一范式
	最核心，最重要的范式，所有表的设计都需要满足。
	必须有主键，并且每一个字段都是原子性不可再分。

	学生编号 	学生姓名	 联系方式
	------------------------------------------
	1001		张三		zs@gmail.com,1359999999
    1002		李四		ls@gmail.com,13699999999
    1001		王五		ww@163.net,13488888888

    以上是学生表，满足第一范式吗？
        不满足，第一：没有主键。第二：联系方式可以分为邮箱地址和电话

    学生编号(pk) 		学生姓名	邮箱地址			联系电话
    ----------------------------------------------------
    1001				张三		zs@gmail.com	1359999999
    1002				李四		ls@gmail.com	13699999999
    1003				王五		ww@163.net		13488888888
```



**第二范式**：建立在第一范式之上，要求所有非主键字段完全依赖主键，不要产生部分依赖

```
4.4、第二范式：
	建立在第一范式的基础之上，
	要求所有非主键字段必须完全依赖主键，不要产生部分依赖。
	
	学生编号 	学生姓名	教师编号 教师姓名
----------------------------------------------------
    1001		张三		001		王老师
    1002		李四		002		赵老师
    1003		王五		001		王老师
    1001		张三		002		赵老师

    这张表描述了学生和老师的关系：（1个学生可能有多个老师，1个老师有多个学生）
    这是非常典型的：多对多关系！

    分析以上的表是否满足第一范式？
        不满足第一范式。

    怎么满足第一范式呢？修改

    学生编号+教师编号(pk)	  学生姓名 		 教师姓名
    ----------------------------------------------------
    1001	001				张三			王老师
    1002	002				李四			赵老师
    1003	001				王五			王老师
    1001	002				张三			赵老师

    学生编号 教师编号，两个字段联合做主键，复合主键（PK: 学生编号+教师编号）
    经过修改之后，以上的表满足了第一范式。但是满足第二范式吗？
        不满足，“张三”依赖1001，“王老师”依赖001，显然产生了部分依赖。
        产生部分依赖有什么缺点？
            数据冗余了。空间浪费了。“张三”重复了，“王老师”重复了。

    为了让以上的表满足第二范式，你需要这样设计：
        使用三张表来表示多对多的关系！！！！
        学生表
        学生编号(pk)		学生名字
        ------------------------------------
        1001				张三
        1002				李四
        1003				王五

        教师表
        教师编号(pk)		教师姓名
        --------------------------------------
        001					王老师
        002					赵老师

        学生教师关系表
        id(pk)			学生编号(fk)			教师编号(fk)
        ------------------------------------------------------
        1				1001					001
        2				1002					002
        3				1003					001
        4				1001					002
        
多对多怎么设计？
	多对多，三张表，关系表两个外键
```





**第三范式**：建立在第二范式之上，要求所有非主键字段直接依赖主键，不要产生传递依赖

	4.5、第三范式
		第三范式建立在第二范式的基础之上
		要求所有非主键字段必须直接依赖主键，不要产生传递依赖。
		
	  学生编号（PK） 	学生姓名 班级编号    班级名称
	---------------------------------------------------------
		1001		张三		01		一年一班
		1002		李四		02		一年二班
		1003		王五		03		一年三班
		1004		赵六		03		一年三班
	
	以上表的设计是描述：班级和学生的关系。很显然是1对多关系！
	一个教室中有多个学生。
	
	分析以上表是否满足第一范式？
		满足第一范式，有主键。
	
	分析以上表是否满足第二范式？
		满足第二范式，因为主键不是复合主键，没有产生部分依赖。主键是单一主键。
	
	分析以上表是否满足第三范式？
		第三范式要求：不要产生传递依赖！
		一年一班依赖01，01依赖1001，产生了传递依赖。
		不符合第三范式的要求。产生了数据的冗余。
	
	那么应该怎么设计一对多呢？
	
		班级表：一
		班级编号(pk)	 班级名称
		----------------------------------------
		01				一年一班
		02				一年二班
		03				一年三班
	
		学生表：多
		学生编号（PK） 学生姓名   班级编号(fk)
		-------------------------------------------
		1001		 张三			01			
		1002		 李四			02			
		1003		 王五			03			
		1004		 赵六			03		
		
	一对多
		两张表，多的表加外键



> 数据库设计三范式是理论上的。
>
> 实践和理论有的时候有偏差。
>
> 最终的目的都是为了满足客户的需求，有的时候会拿冗余换执行速度。
>
> 因为在sql当中，表和表之间连接次数越多，效率越低。（笛卡尔积）
>
> 有的时候可能会存在冗余，但是为了减少表的连接次数，这样做也是合理的，
>
> 并且对于开发人员来说，sql语句的编写难度也会降低。







## 10、JDBC

### 10.1 JDBC介绍

> 1、JDBC为访问不同的数据库提供了统一的接口，为使用者屏蔽了细节问题
>
> 2、使用JDBC时，可以连接任何提供了JDBC驱动程序的数据库系统，从而完成对数据库的各种操作

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305111511268.png)



**模拟JDBC**

```java
package com.xrj.level1.jdbc.myjdbc;

public interface jdbcInterface {
    // 连接
    public Object getConnect();
    // crud
    public void crud();
    // 关闭连接
    public void close();
}


package com.xrj.level1.jdbc.myjdbc;

public class MysqlJdbcImpl implements jdbcInterface{

    @Override
    public Object getConnect() {
        System.out.println("得到mysql的连接");
        return null;
    }

    @Override
    public void crud() {
        System.out.println("执行mysql的crud");
    }

    @Override
    public void close() {
        System.out.println("关闭mysql连接");
    }
}


package com.xrj.level1.jdbc.myjdbc;

public class OracleJdbcImpl implements jdbcInterface{
    @Override
    public Object getConnect() {
        System.out.println("得到oracle的连接");
        return null;
    }

    @Override
    public void crud() {
        System.out.println("执行oracle的增删改查");
    }

    @Override
    public void close() {
        System.out.println("关闭oracle数据库");
    }
}


package com.xrj.level1.jdbc.myjdbc;

public class TestJdbc {
    public static void main(String[] args) {
        // 对mysql的操作
        jdbcInterface jdbc = new MysqlJdbcImpl();
        jdbc.getConnect();
        jdbc.crud();
        jdbc.close();

        // 对oracle的操作
        jdbc = new OracleJdbcImpl();
        jdbc.getConnect();
        jdbc.crud();
        jdbc.close();
    }
}
```







### 10.2 JDBC API

> JDBC API是一系列的接口，它统一和规范了应用程序与数据库的连接、执行sql语句、得到返回结果等各类操作，相关类和接口在java.sql和javax.sql包中

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305111523553.png)







### 10.3 JDBC步骤

1、注册驱动  -- 加载`Driver`类

2、获取连接  -- 得到`Connection`连接

3、执行增删改查  -- 发送sql语句给数据库执行

4、释放资源  --  关闭连接

```java
package com.xrj.level1.jdbc;

import com.mysql.cj.jdbc.Driver;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JDBC01 {
    public static void main(String[] args) throws SQLException {
        // 在项目下创建一个文件夹libs
        // 将 mysql.jar 拷贝到该目录下，点击 add as library ..加入到项目中

        // 1.注册驱动
        Driver driver = new Driver();

        // 2.得到连接
        /*
            (1) jdbc:mysql 定好协议，通过 jdbc 的方式连接 mysql
            (2) localhost 数据库所在的主机,也可以是ip地址
            (3) 3306  mysql监听的端口
            (4) db02  准备连接的数据库
         */
        String url = "jdbc:mysql://localhost:3306/db02";
        // 将用户名和密码放入properties
        Properties properties = new Properties();
        properties.setProperty("user", "root");
        properties.setProperty("password", "root");
        Connection connect = driver.connect(url, properties);

        // 3.执行sql
        // String sql = "insert into actor values(null, 'xrj', '男', '1999-10-11', '110')";
        // String sql = "update actor set name = 'xyy' where id = 1";
        String sql = "delete from actor where id = 1";
        // statement 用于执行静态 SQL 语句并返回其生成的结果的对象
        Statement statement = connect.createStatement();
        int rows = statement.executeUpdate(sql); // dml语句返回的是影响的行数

        System.out.println(rows > 0 ? "成功" : "失败");

        // 4.释放资源
        statement.close();
        connect.close();
    }
}
```





### 10.4 获取数据库连接的五种方式

**方式1**

```java
public void con01() throws SQLException {
    Driver driver = new Driver();

    String url = "jdbc:mysql://localhost:3306/db02";
    // 将用户名和密码放入properties
    Properties properties = new Properties();
    properties.setProperty("user", "root");
    properties.setProperty("password", "root");
    Connection connect = driver.connect(url, properties);

    System.out.println(connect);
}
```



**方式2**

直接使用`Driver driver = new com.mysql.cj.jdbc.Driver();`数据静态加载，灵活性差，因此使用反射加载

```java
public void con02() throws SQLException, ClassNotFoundException, InstantiationException, IllegalAccessException {
    Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
    Driver driver = (Driver) aClass.newInstance();

    String url = "jdbc:mysql://localhost:3306/db02";
    // 将用户名和密码放入properties
    Properties properties = new Properties();
    properties.setProperty("user", "root");
    properties.setProperty("password", "root");
    Connection connect = driver.connect(url, properties);

    System.out.println(connect);
}
```



**方式3**

使用`DriverManager`替换`Driver`，不需要创建`properties`对象

```java
public void con03() throws SQLException, ClassNotFoundException, InstantiationException, IllegalAccessException {
    // 使用DriverManager替换Driver
    Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
    Driver driver = (Driver) aClass.newInstance();

    String url = "jdbc:mysql://localhost:3306/db02";
    // 将用户名和密码放入properties
    String user = "root";
    String password = "root";

    DriverManager.registerDriver(driver);
    Connection connect = DriverManager.getConnection(url, user, password);

    System.out.println(connect);
}
```





**方式4**

`com.mysql.cj.jdbc.Driver`类在加载的时候就自动完成了驱动注册

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305111715449.png)

```java
public void con04() throws SQLException, ClassNotFoundException {
    Class.forName("com.mysql.cj.jdbc.Driver");
    /*
        Driver类中的静态代码块，在类加载的时候会自动执行，该静态代码块完成了驱动的注册
        static {
            try {
                DriverManager.registerDriver(new Driver());
            } catch (SQLException var1) {
                throw new RuntimeException("Can't register driver!");
            }
        }
     */
    String url = "jdbc:mysql://localhost:3306/db02";
    // 将用户名和密码放入properties
    String user = "root";
    String password = "root";

    Connection connect = DriverManager.getConnection(url, user, password);

    System.out.println(connect);
}
```

> 不写`Class.forName("com.mysql.cj.jdbc.Driver");`也可以
>
> 从`jdk1.5`以后使用了`jdbc4`，不需要显式调用`class.forname()`注册驱动，而是会自动调用驱动程序jar包下`META-INF/services/java.sql.Driver`文本中的类名称去注册
>
> 建议写上`Class.forName("com.mysql.cj.jdbc.Driver");`，更加明确





**方式5**

使用配置文件，更加灵活

```java
public void con05() throws SQLException, ClassNotFoundException, IOException {
    Properties properties = new Properties();
    properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));
    String user = properties.getProperty("user");
    String password = properties.getProperty("password");
    String url = properties.getProperty("url");
    String driver = properties.getProperty("driver");
    Class.forName(driver);
    Connection connect = DriverManager.getConnection(url, user, password);

    System.out.println(connect);
}
```







### 10.5 ResultSet

1、表示数据库结果集的数据表，通过执行查询数据库的语句生成

2、`ResultSet`对象保持一个光标并指向当前行数据，最初光标位于第一行数据之前

3、`next`方法将光标移动到下一行，并且移动到空时返回`false`，因此可以通过`while`来遍历

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305111756688.png)

> 查询完数据后，数据存放在`resultSet`的`rowData`中，`rows`中存放了每行的数据，在每一行的`internalRowDate`中存放的是每一列的数据，用的是`ascll`码存放

```JAVA
package com.xrj.level1.jdbc;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.*;
import java.util.Date;
import java.util.Properties;

public class ResultSet_ {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));

        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");

        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);
        Statement statement = connection.createStatement();

        String sql = "select * from actor";
        ResultSet resultSet = statement.executeQuery(sql);

        while (resultSet.next()) {
            int id = resultSet.getInt(1);
            String name = resultSet.getString(2);
            String sex = resultSet.getString(3);
            Date date = resultSet.getDate(4);
            String phone = resultSet.getString(5);
            System.out.println(id + " " + name + " " + sex + " " + date + " " + phone);
        }

        statement.close();
        connection.close();
    }
}
```







### 10.6 Statement

1、`Statement`对象用于执行静态`sql`语句并返回其生成结果的对象

2、在连接建立后，需要对数据库进行访问，执行 命令 或者 `sql`语句，可以通过

- `Statement`【存在`sql`注入】
- `PreparedStatement`【预处理】
- `CallableStatement`【存储过程】

3、`Statement`对象执行`sql`语句，存在**sql注入**风险

4、**sql注入**是利用某些系统没有对用户输入的数据进行充分检查，而在用户输入数据中注入非法的sql语句或命令，恶意攻击数据库

5、使用`PreparedStatement`就可以防止**sql注入**

```sql
-- sql注入
CREATE TABLE admin ( -- 管理员表
	NAME VARCHAR(32) NOT NULL UNIQUE, 
	pwd VARCHAR(32) NOT NULL DEFAULT '') CHARACTER SET utf8;
	
INSERT INTO admin VALUES('tom', '123')

-- 查找某个用户
SELECT * FROM admin WHERE name = 'tom' AND pwd = '123'

-- sql注入
-- 输入用户名为 1' or
-- 输入密码为 or '1' = '1
SELECT * FROM admin
	WHERE name = '1' or' AND pwd = 'or '1' = '1'
```



```java
package com.xrj.level1.jdbc.stateMent;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;
import java.util.Scanner;

public class Statement_ {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        Scanner scanner = new Scanner(System.in);
        String admin = scanner.nextLine();
        String pwd = scanner.nextLine();

        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));

        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");

        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);
        Statement statement = connection.createStatement();

        // SELECT * FROM admin WHERE name = '1' or' AND pwd = 'or '1' = '1'
        String sql = "select * from admin where name ='" + admin + "' and pwd = '" + pwd + "'";
        ResultSet resultSet = statement.executeQuery(sql);
        while (resultSet.next()) {
            System.out.println(resultSet.getString(1) + " " + resultSet.getString(2));
        }

        resultSet.close();
        statement.close();
        connection.close();
    }
}
```





### 10.7 PreparedStatement

1、`PreparedStatement`执行的`sql`语句中的参数用问号来表示，调用`PreparedStatement`对象的`setXxx()`方法来设置这些参数。`setXxx`有两个参数，第一个参数是要设置的`sql`语句中的参数的索引【从1开始】，第二个是设置的`sql`语句中的参数的值

2、调用`executeQuery()`：返回`ResultSet`对象

3、调用`executeUpdate()`：执行增、删、改，返回影响的行数



**预处理好处**

1、不再使用`+`拼接`sql`语句，减少语法错误

2、有效的解决了`sql`注入问题

3、大大减少了编译次数，效率更高

```java
package com.xrj.level1.jdbc.prepareStatement_;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;
import java.util.Scanner;

public class PrepareStatement_ {
    public static void main(String[] args) throws ClassNotFoundException, SQLException, IOException {

        Scanner scanner = new Scanner(System.in);
        String name = scanner.nextLine();
        String pwd = scanner.nextLine();

        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");

        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);
        String sql = "select * from admin where name = ? and pwd = ?";
        // preparedStatement的运行类型是实现了PreparedStatement接口的实现类的对象
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setString(1, name);
        preparedStatement.setString(2, pwd);
        ResultSet resultSet = preparedStatement.executeQuery();
        if (resultSet.next()) {
            System.out.println("查询成功");
        }else {
            System.out.println("查询失败");
        }

        resultSet.close();
        preparedStatement.close();
        connection.close();
    }
}
```



```java
package com.xrj.level1.jdbc.prepareStatement_;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;
import java.util.Scanner;

public class PrepareStatementDML_ {
    public static void main(String[] args) throws ClassNotFoundException, SQLException, IOException {

        Scanner scanner = new Scanner(System.in);
        String name = scanner.nextLine();
        String pwd = scanner.nextLine();

        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");

        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);
        // String sql = "insert into admin values (?, ?)";
        // String sql = "update admin set pwd = ? where name = ?";
        String sql = "delete from admin where name = ?";
        // preparedStatement的运行类型是实现了PreparedStatement接口的实现类的对象
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setString(1, name);
        // preparedStatement.setString(2, pwd);
        int i = preparedStatement.executeUpdate();
        System.out.println(i > 0 ? "执行成功" : "执行失败");


        preparedStatement.close();
        connection.close();
    }
}
```







### 10.8 JDBC相关API

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305121654371.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305121655149.png)







### 10.9 封装JDBCUtils

> 在jdbc操作中，获取连接和释放资源是经常用到的，可以将其封装到JDBC连接的工具类中

```java
package com.xrj.level1.jdbc.utils;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

public class JDBCUtils {
    // 定义类相关属性
    private static String user;
    private static String password;
    private static String url;
    private static String driver;

    static {
        try {
            Properties properties = new Properties();
            properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            url = properties.getProperty("url");
            driver = properties.getProperty("driver");
        } catch (IOException e) {
            // 实际开发中，我们将编译异常转换为运行时异常，然后抛出
            // 调用者可以选择处理该异常，也可以不管他，他会自动抛出
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection(){
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 关闭相关资源
     * @param set
     * @param statement  Statement 或者 PreparedStatement
     * @param connection
     */
    public static void close(ResultSet set, Statement statement, Connection connection){
        try {
            if (set != null)
                set.close();
            if (statement != null)
                statement.close();
            if (connection != null)
                connection.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

```java
package com.xrj.level1.jdbc.utils;

import org.junit.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JDBCUtils_Use {

    @Test
    public void testSelect(){
        String sql = "select * from admin";
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            resultSet = preparedStatement.executeQuery();
            while (resultSet.next()){
                System.out.println(resultSet.getString("name") + " " + resultSet.getString("pwd"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.close(resultSet, preparedStatement, connection);
        }
    }

    @Test
    public void testUpdate(){
        String sql = "update admin set pwd = ? where name = ?";
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1, "abc");
            preparedStatement.setString(2, "tom");
            int i = preparedStatement.executeUpdate();
            System.out.println(i > 0 ? "成功" : "失败");
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.close(null, preparedStatement, connection);
        }
    }
}
```







### 10.10 事务

**基本介绍**

1、`JDBC`程序中当一个`Connection`对象创建时，默认情况下是自动提交事务：每次执行一个`sql`语句，如果执行成功，就会提交数据库，不能回滚

2、`JDBC`程序中为了让多个`sql`语句作为一个整体执行，需要使用**事务**

3、使用`Connection`的`setAutoCommit(false)`可以取消事务自动提交

4、在所有的`sql`语句都执行成功后，调用`Connection`的`commit()`方法提交事务

5、在某个`sql`语句执行失败或出现异常时，调用`Connection`的`rollback()`方法回滚

6、`Connection.setSavepoint();`设置保存点



**模拟转账**

```java
package com.xrj.level1.jdbc.transaction_;

import com.xrj.level1.jdbc.utils.JDBCUtils;
import org.junit.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class Transaction_ {
    @Test
    // 没有使用事务
    public void noTransaction(){
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        String sql1 = "update account set money = money - 100 where name = ?";
        String sql2 = "update account set money = money + 100 where name = ?";

        try {
            connection = JDBCUtils.getConnection();
            // 执行第一条sql
            preparedStatement = connection.prepareStatement(sql1);
            preparedStatement.setString(1, "xrj");
            preparedStatement.executeUpdate();

            // 执行到这里时会发生异常，后边的代码不在执行，因此出现转账错误
            int num = 10 / 0;

            // 执行第二条sql
            preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.setString(1, "xyy");
            preparedStatement.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.close(null, preparedStatement, connection);
        }
    }

    @Test
    // 使用事务
    public void useTransaction(){
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        String sql1 = "update account set money = money - 100 where name = ?";
        String sql2 = "update account set money = money + 100 where name = ?";

        try {
            connection = JDBCUtils.getConnection();
            connection.setAutoCommit(false);
            // 执行第一条sql
            preparedStatement = connection.prepareStatement(sql1);
            preparedStatement.setString(1, "xrj");
            preparedStatement.executeUpdate();

            //int num = 10 / 0;

            // 执行第二条sql
            preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.setString(1, "xyy");
            preparedStatement.executeUpdate();

            // 全部sql语句执行完, commit
            connection.commit();
        } catch (Exception e) {
            // 如果发生异常，就回滚
            System.out.println("发生异常，回滚到最初状态");
            try {
                connection.rollback();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            JDBCUtils.close(null, preparedStatement, connection);
        }
    }
}
```







### 10.11 批处理

1、当需要成批插入或更新记录时，可以采用`Java`的批量更新机制，这一机制允许多条语句一次性提交给数据库批量处理。效率更高

2、`JDBC`的批量处理语句

​	`addBatch()`：添加需要批量处理的`SQL`语句或参数

​	`executeBatch()`：执行批量处理语句

​	`clearBatch()`：清空批量处理包的语句

3、`JDBC`连接`mysql`如果要使用批处理功能，需要在`url`中加上参数`?rewriteBatchedStatements=true`

4、批处理往往和`PreparedStatement`搭配使用，既可以减少编译次数，又减少运行次数，提升效率

```java
package com.xrj.level1.jdbc.batch_;

import com.xrj.level1.jdbc.utils.JDBCUtils;
import org.junit.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.Date;

public class Batch_ {

    @Test
    public void noBatch() throws SQLException {
        // 传统方法添加5000条数据到admin表
        Connection connection = JDBCUtils.getConnection();
        String sql = "insert into admin values(?, ?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            preparedStatement.setString(1, "xrj" + i);
            preparedStatement.setString(2, "xrj123  " + i);
            preparedStatement.executeUpdate();
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);    // 464135
    }

    @Test
    public void useBatch() throws SQLException {
        // 使用批处理方式添加5000条数据到admin表
        Connection connection = JDBCUtils.getConnection();
        String sql = "insert into admin values(?, ?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            preparedStatement.setString(1, "xrj" + i);
            preparedStatement.setString(2, "xrj123  " + i);
            preparedStatement.addBatch();
            if ((i + 1) % 1000 == 0) {
                preparedStatement.executeBatch();
                preparedStatement.clearBatch();
            }
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);    // 2552
    }
}
```



1、`preparedStatement.addBatch();`执行该语句，会进入`addBatch`方法中，首先执行`checkAllParametersSet()`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305122028630.png)

2、`checkAllParametersSet()`方法检查放入的数据是否合法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305122023292.png)

3、`checkAllParametersSet()`执行完之后，执行`addBatch`方法，该方法会创建一个`ArrayList`用来存放批量处理的数据，`ArrayList`初始大小为10，之后会按1.5倍扩充。按照`ArrayList`添加元素的方式将数据存放进去，最终数据是存放在一个`elementData`的`ArrayList`中

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305122024298.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305122037822.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305122036730.png)







## 11、数据库连接池

**传统方式连接数据库的问题**

1．传统的`JDBC`数据库连接使用 `DriverManager`来获取，每次向数据库建立连接的时候都要将 `Connection `加载到内存中，再验证IP地址，用户名和密码(0.05s~1s时间)。需要数据库连接的时候，就向数据库要求一个，频繁的进行数据库连接操作将占用很多的系统资源，容易造成服务器崩溃。

2、每一次数据库连接，使用完后都得断开，如果程序出现异常而未能关闭，将导致数据库内存泄漏，最终将导致重启数据库。

3、传统获取连接的方式，不能控制创建的连接数量，如连接过多，也可能导致内存泄漏，MySQL崩溃。

4、解决传统开发中的数据库连接问题,可以采用数据库连接池技术`connection pool`。

```JAVA
// 演示连接5000次数据库需要的时间
@Test
public void testCon() throws SQLException {
    long start = System.currentTimeMillis();
    for (int i = 0; i < 5000; i++) {
        Connection connection = JDBCUtils.getConnection();
        // 这里connect的运行类型是com.mysql.cj.jdbc.ConnectionImpl，因此调用close方法就是关闭连接
        JDBCUtils.close(null, null, connection);
    }
    long end = System.currentTimeMillis();
    System.out.println(end - start);    // 21293
}
```



**数据库连接池种类**

`JDBC`的数据库连接池使用`javax.sql.DataSource`来表示，`DataSource`是一个接口，该接口通常由第三方提供实现【第三方实现后提供一个jar包】

- **`C3P0`**：速度相对较慢，稳定性好
- `DBCP`：速度比`C3P0`快，但不稳定
- `Proxool`：有监控连接池状态的功能，稳定性比`C3P0`差一点
- `BoneCP`：速度快
- **`Druid`**：集`DBCP`、`C3P0`、`Proxool`优点于一身的数据库连接池





### 11.1 数据库连接池原理

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305131516369.png)

> 数据库连接池在程序执行时，会事先创建好许多数据库的连接，如果一个程序想要连接数据库，直接向数据库连接池索要即可，使用完之后，将连接归还，而不需要释放，因此数据库的连接和释放，就省去了每次都需要连接释放的时间，大大提高了效率。在连接池中的连接被使用完时，如果又有程序需要使用连接，就会在等待队列中等待，直到有连接可用。





### 11.2 C3P0

> 使用c3p0数据源需要将c3p0的jar包加入项目。
>
> 如果在程序中指定数据源参数就不需要配置文件，但是比较繁琐，因此可以将所有的配置信息写在c3p0的配置文件中，然后创建数据源的时候，参数设置为配置文件中的name，配置文件要放在src目录下
>
> 可以发现，使用数据源获取数据库连接后，连接释放的时间大大减少，因为使用数据源时，数据源预先创建好了连接，不需要我们自己去创建和释放，提高效率

`c3p0`配置文件

```xml
<c3p0-config>
    <!-- 数据源名称代表连接池 -->
    <named-config name="c3p0">
        <!-- 驱动类 -->
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <!-- url-->
        <property name="jdbcUrl">jdbc:mysql://127.0.0.1:3306/db02</property>
        <!-- 用户名 -->
        <property name="user">root</property>
        <!-- 密码 -->
        <property name="password">root</property>
        <!-- 每次增长的连接数-->
        <property name="acquireIncrement">5</property>
        <!-- 初始的连接数 -->
        <property name="initialPoolSize">10</property>
        <!-- 最小连接数 -->
        <property name="minPoolSize">5</property>
        <!-- 最大连接数 -->
        <property name="maxPoolSize">50</property>

        <!-- 可连接的最多的命令对象数 -->
        <!-- 最多可以创建Statements对象的个数,就是可以执行SQL语句的对象的个数 -->
        <property name="maxStatements">5</property>

        <!-- 每个连接对象可连接的最多的命令对象数 -->
        <property name="maxStatementsPerConnection">2</property>
    </named-config>
</c3p0-config>
```

```java
package com.xrj.level1.jdbc.dataSource;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;

import java.beans.PropertyVetoException;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

public class c3p0_ {
    // 数据源的参数在程序中指定
    @Test
    public void test01() throws IOException, PropertyVetoException, SQLException {
        // 1.创建一个数据源对象
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();

        // 2.获取连接信息
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/xrj/level1/jdbc/mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");

        // 3.给数据源设置参数
        comboPooledDataSource.setDriverClass(driver);
        comboPooledDataSource.setJdbcUrl(url);
        comboPooledDataSource.setUser(user);
        comboPooledDataSource.setPassword(password);

        // 初始化连接数
        comboPooledDataSource.setInitialPoolSize(10);
        // 最大连接数
        comboPooledDataSource.setMaxPoolSize(50);

        // 测试连接5000次需要的时间
        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            Connection connection = comboPooledDataSource.getConnection();
            connection.close();
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);    // 610
    }

    // 使用配置文件设置数据源参数,配置文件需要放在src下
    @Test
    public void test02() throws SQLException {
        // 这里参数写的是c3p0配置文件中的name
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("c3p0");

        // 测试连接5000次需要的时间
        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            Connection connection = comboPooledDataSource.getConnection();
            connection.close();
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);    // 650
    }
}
```







### 11.3 Druid

```properties
#key=value
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/db02?rewriteBatchedStatements=true
username=root
password=root
#initial connection Size
initialSize=10
#min idle connecton size
minIdle=5
#max active connection size
maxActive=50
#max wait time (5000 mil seconds)
maxWait=5000
```

```java
package com.xrj.level1.jdbc.dataSource;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.junit.Test;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.Connection;
import java.time.Period;
import java.util.Properties;

public class Druid_ {
    @Test
    public void testDruid() throws Exception {
        // 1.创建Druid数据库连接池工厂
        DruidDataSourceFactory druidDataSourceFactory = new DruidDataSourceFactory();

        // 2.读取配置文件
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/druid.properties"));

        // 3.创建数据源
        DataSource dataSource = druidDataSourceFactory.createDataSource(properties);

        long start = System.currentTimeMillis();
        for (int i = 0; i < 500000; i++) {
            Connection connection = dataSource.getConnection();
            // 这里connect的运行类型是com.alibaba.druid.pool.DruidPooledConnection，因此调用close方法就是将连接放回去，并不关闭
            connection.close();
        }
        long end = System.currentTimeMillis();
        System.out.println(end - start);    // 890
    }
}
```





**将JDBCUtils工具类改为Druid实现**

```java
package com.xrj.level1.jdbc.utils;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.*;
import java.util.Properties;


public class DruidUtils {

    private static DataSource dataSource = null;

    static {
        try {
            Properties properties = new Properties();
            properties.load(new FileInputStream("src/druid.properties"));
            dataSource = new DruidDataSourceFactory().createDataSource(properties);
        } catch (Exception e) {
            // 实际开发中，我们将编译异常转换为运行时异常，然后抛出
            // 调用者可以选择处理该异常，也可以不管他，他会自动抛出
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection(){
        try {
            return dataSource.getConnection();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 关闭相关资源
     * @param set
     * @param statement  Statement 或者 PreparedStatement
     * @param connection
     */
    public static void close(ResultSet set, Statement statement, Connection connection){
        try {
            if (set != null)
                set.close();
            if (statement != null)
                statement.close();
            if (connection != null)
                connection.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```







## 12、DBUtils

**`ResultSet`的弊端**

1、关闭`connection`后，`resultSet`结果集无法使用

2、`resultSet`不利于数据的管理

3、为了解决这个问题，我们就需要将`select`查询得到的结果保存到一个`list`中，以便后续的使用。因此我们首先需要创建一个和数据库对象对应的类，也叫做`JavaBean`、`PoJo`、`Domain`，查询得到的`resultSet`将其中每行数据封装成一个对象，然后放入`ArraysList`

4、**所有的 `POJO` 必须设置为包装类，这是因为数据为 null 的情况下，基本类型会有默认值，无论是在添加、修改和查询的时候，都会导致数据和实际要修改的数据不一致。**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141504715.png)



#### **传统方法解决**

```java
@Test
public void testSelectToArrayList(){
    Connection connection = null;
    String sql = "select * from actor where id > ?";
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;
    ArrayList<actor> actors = new ArrayList<>();
    try {
        connection = DruidUtils.getConnection();
        preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setInt(1, 2);
        resultSet = preparedStatement.executeQuery();

        while (resultSet.next()) {
            int id = resultSet.getInt(1);
            String name = resultSet.getString("name");
            String sex = resultSet.getString("sex");
            Date borndate = resultSet.getDate("borndate");
            String phone = resultSet.getString("phone");
            actors.add(new actor(id, name, sex, borndate, phone));
        }
        System.out.println(actors);
        
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        DruidUtils.close(resultSet, preparedStatement, connection);
    }
}
```





#### **使用dbutils**

1、`commons-dbutils` 是 `Apache`组织提供的一个开源`JDBC`工具类库，它是对`JDBC`的封装，使用`dbutils`能极大简化`jdbc`编码的工作量。

2、`QueryRunner`类：该类封装了`SQL`的执行，是线程安全的。可以实现增、删、改、查、批处理

3、`ResultSetHandler`接口：该接口用于处理`java.sql.ResultSet`，将数据按要求转换为另一种形式,



`ArrayHandler`：把结果集中的第一行数据转成对象数组。

`ArrayListHandler`：把结果集中的每一行数据都转成一个数组，再存放到`List`中

**`BeanHandler`**：将结果集中的第一行数据封装到一个对应的`JavaBean`实例中。

**`BeanListHandler`**：将结果集中的每一行数据都封装到一个对应的`JavaBean`实例中，存放到`List`里。

`ColumnListHandler`：将结果集中某一列的数据存放到`List`中。

`KeyedHandler(name)`：将结果集中的每行数据都封装到Map里，再把这些map再存到一个map里，其key为指定的key

`MapHandler`：将结果集中的第一行数据封装到一个Map里,key是列名，value就是对应的值。

`MapListHandler`：将结果集中的每一行数据都封装到一个Map里，然后再存放到List





**查询多行**

```java
// 返回多行数据，将每行数据封装成一个actor对象，然后放入ArraysList
@Test
public void DBUtilsQuery() throws SQLException {
    // 1.得到连接
    Connection connection = DruidUtils.getConnection();
    // 2.创建QueryRunner对象
    QueryRunner queryRunner = new QueryRunner();
    // 3.编写sql
    // String sql = "select * from actor where id > ?";
    String sql = "select id, name from actor where id > ?";
    /*
        1.query就是执行sql语句，得到的resultSet封装到ArraysList中
        2.connection 连接
        3.sql 执行的sql语句
        4.new BeanListHandler<>(actor.class) 将resultSet -> actor对象 -> 放入ArraysList
            底层使用反射机制获取actor的属性，进行封装
        5.最后是一个可变参数，是给sql语句中?赋值
        6.底层会通过connection创建prepareStatement，执行sql得到resultSet，结果封装到ArraysList后，将repareStatement和resultSet关闭
     */
    List<actor> query = queryRunner.query(connection, sql, new BeanListHandler<>(actor.class), 2);

    System.out.println(query);

    DruidUtils.close(null, null, connection);
}
```



1、执行`List<actor> query = queryRunner.query(connection, sql, new BeanListHandler<>(actor.class), 2);`会先创建`BeanListHandler`对象，并将`type`初始化

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141603963.png)

2、然后进入`query`方法，在该方法中，会创建`PreparedStatement`、`ResultSet`。`stmt = this.prepareStatement(conn, sql);`就是用`conn`创建一个`prepareStatement`，`this.fillStatement(stmt, params);`将参数传入`stmt中`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141604452.png)

3、执行`this.wrap(stmt.executeQuery());`得到一个`ResultSet`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141612094.png)

4、`result = rsh.handle(rs);`就是将`ResultSet`的每行结果转化为一个`Bean对象`，然后放入`ArraysList`中，这里`Bean`对象就是通过**反射**创建的，所以在创建`Bean`类时，**必须有一个无参构造器**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141607098.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141610327.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141609047.png)





**查询单行**

```java
// 返回单行数据,返回的是单个对象
@Test
public void testQuerySingle() throws SQLException {
    Connection connection = DruidUtils.getConnection();
    QueryRunner queryRunner = new QueryRunner();
    String sql = "select * from actor where id = ? ";
    // 得到的是单个对象，因此使用new BeanHandler<>(actor.class)
    actor query = queryRunner.query(connection, sql, new BeanHandler<>(actor.class), 2);
    System.out.println(query);

    DruidUtils.close(null, null, connection);
}
```





**查询单行数据的单列值**

```java
// 查询结果是单行单列，返回的是Object对象
@Test
public void testQueryScalar() throws SQLException {
    Connection connection = DruidUtils.getConnection();
    QueryRunner queryRunner = new QueryRunner();
    String sql = "select id from actor where name = ?";
    // 返回的是一个对象的一列，所以使用ScalarHandler()
    Object id = queryRunner.query(connection, sql, new ScalarHandler(), "王五");
    System.out.println(id);
    DruidUtils.close(null, null, connection);
}
```





**dml操作**

```java
// 完成dml操作
@Test
public void testDML() throws SQLException {
    Connection connection = DruidUtils.getConnection();
    QueryRunner queryRunner = new QueryRunner();
    // String sql = "insert into actor values(null, ?, ?, ?, ?)";
    // String sql = "update actor set phone = ? where id = ?";
    String sql = "delete from actor where id > ?";
    // 返回受影响的行数
    int rows = queryRunner.update(connection, sql,  3);
    System.out.println(rows > 0 ? "成功" : "失败");
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141613237.png)





**表和 JavaBean 的类型关系映射**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141631590.png)









## 13、BasicDao

`dbutils `+ `Druid`简化了`JDBC`开发，但还有不足

> 1、`sql`语句是固定的，不能通过参数传入，通用性不好
>
> 2、对于`select`操作，如果有返回值，返回类型不能固定，需要使用泛型

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305141657467.png)



- `DAO`：`data access object`
- 这样的通用类，称为`BasicDao`，是专门和数据库交互的
- 在`BasicDao`的基础上，一张表对应一个`Dao`



1、`utils`包：工具类

2、`domain`包：实体类

3、`dao`包：数据访问层

4、`test`包：测试类



**utils**

```java
package com.xrj.level1.jdbc.dao_.utils;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class DruidUtils {
    private static DataSource ds = null;

    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileInputStream("src/druid.properties"));
            ds = new DruidDataSourceFactory().createDataSource(properties);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection(){
        try {
            return ds.getConnection();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    public static void close(ResultSet set, Statement statement, Connection connection) {
        try {
            if (set != null)
                set.close();
            if (statement != null)
                statement.close();
            if (connection != null)
                connection.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```



**domain**

```java
package com.xrj.level1.jdbc.dao_.domain;

import java.util.Date;

public class actor {
    private Integer id;
    private String name;
    private String sex;
    private Date borndate;
    private String phone;

    public actor() {
    }

    public actor(Integer id, String name, String sex, Date borndate, String phone) {
        this.id = id;
        this.name = name;
        this.sex = sex;
        this.borndate = borndate;
        this.phone = phone;
    }

    @Override
    public String toString() {
        return "actor{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", borndate=" + borndate +
                ", phone='" + phone + '\'' +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Date getBorndate() {
        return borndate;
    }

    public void setBorndate(Date borndate) {
        this.borndate = borndate;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }
}
```



**dao**

```java
package com.xrj.level1.jdbc.dao_;

import com.xrj.level1.jdbc.dao_.utils.DruidUtils;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.apache.commons.dbutils.handlers.ScalarHandler;

import java.sql.Connection;
import java.sql.SQLException;
import java.util.List;

public class basicDao<T>{
    private QueryRunner qr = new QueryRunner();

    // dml
    public int update(String sql, Object... parameters){
        Connection connection = DruidUtils.getConnection();
        try {
            return qr.update(connection, sql, parameters);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            DruidUtils.close(null, null, connection);
        }
        return 0;
    }

    // 查询多行
    public List<T> queryMany(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = DruidUtils.getConnection();
        try {
            return qr.query(connection, sql, new BeanListHandler<>(clazz), parameters);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            DruidUtils.close(null, null, connection);
        }
        return null;
    }

    // 查询单行
    public T querySingle(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = DruidUtils.getConnection();
        try {
            return qr.query(connection, sql, new BeanHandler<>(clazz), parameters);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            DruidUtils.close(null, null, connection);
        }
        return null;
    }

    // 查询单行单列
    public Object queryScalar(String sql, Object... parameters){
        Connection connection = DruidUtils.getConnection();
        try {
            return qr.query(connection, sql, new ScalarHandler(), parameters);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            DruidUtils.close(null, null, connection);
        }
        return null;
    }
}
```

```java
package com.xrj.level1.jdbc.dao_.dao;

import com.xrj.level1.jdbc.dao_.basicDao;
import com.xrj.level1.jdbc.dao_.domain.actor;

public class actorDao extends basicDao<actor> {
}
```



**test**

```java
package com.xrj.level1.jdbc.dao_.test;

import com.xrj.level1.jdbc.dao_.dao.actorDao;
import com.xrj.level1.jdbc.dao_.domain.actor;
import org.junit.Test;

import java.util.List;

public class actorDaoTest {
    @Test
    public void testUpdate(){
        actorDao actorDao = new actorDao();
        // String sql = "insert into actor values(null, ?, ?, ?, ?)";
        String sql = "delete from actor where id = ?";
        int update = actorDao.update(sql, "6");
        System.out.println(update > 0 ? "成功" : "失败");
    }

    @Test
    public void testQuery(){
        actorDao actorDao = new actorDao();
        String sql = "select * from actor where id > ?";
        List<actor> actors = actorDao.queryMany(sql, actor.class, 1);
        for (actor p : actors)
            System.out.println(p);

        System.out.println("============");
        // 查单行
        sql = "select * from actor where id = ?";
        actor actor = actorDao.querySingle(sql, actor.class, 2);
        System.out.println(actor);

        System.out.println("============");
        // 查单行单列
        sql = "select name from actor where id = ?";
        Object o = actorDao.queryScalar(sql, 2);
        System.out.println(o);
    }
}
```





## 14、MHL细节

**实现的`dao`层只能进行单表查询，无法进行多表查询**

解决方法：

1、添加一个`multiTable`实体对象，该对象包含多个表的属性，然后实现他的`dao`层，需要多表查询时，就调用他的`dao`方法

2、使用`dbutils`的`MapListHandle`最终返回`List<map<String,Object>>`，将查询到的每个字段用一个`Map`存储，然后将其加入`List`中。

[DBUtils数据库连接池多表连接查询](https://blog.csdn.net/m1913843179/article/details/103170360)





**JavaBean的属性名是否要和数据库中属性名保持一致**

不一定要保持一致，**但是最好保持一致**。如果数据库中有一个数据为`name`，但是`JavaBean`对象的属性名为`name1`，此时如果该类的`set`方法名设置为`setName`就没有问题，但是如果`set`方法名设置为`setName1`查询后该字段就为空。因为`sql`语句查询后，查到的数据库中的字段，通过反射给对象赋值，调用的是`set`方法，数据库查询到的列名为`name`，就会自动调用`setName`方法去赋值，如果没有这个方法，就不会赋值，该字段也就为空，所以如果类的属性名和数据库字段名不一致，但是`set`方法名和数据库字段名保持一致是可以的。

如果是多表查询创建了一个`multiTable`实体，假如其中有两个`name`属性，此时两个`name`名字必须不同，但是数据库字段名都是`name`，此时就可以使用`as`将查询的字段名设置为我们类中属性的名字，和`set`方法保持一致即可









## 15、正则表达式

> 正则表达式是对字符串执行模式匹配的技术



### 15.1 正则表达式底层

```java
package com.level14.regexp;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegTheory {
    public static void main(String[] args) {
        // 找出所有四个数字连在一起的子串
        String content = "1998 年 12 月 8 日，第二代 Java 平台的企业版 J2EE 发布。1999 年 6 月，Sun 公司发布了" +
                "第二代 Java 平台（简称为 Java2）的 3 个版本：J2ME（Java2 Micro Edition，Java2 平台的微型" +
                "版），应用于移动、无线及有限资源的环境；J2SE（Java 2 Standard Edition，Java 2 平台的" +
                "标准版），应用于桌面环境；J2EE（Java 2Enterprise Edition，Java 2 平台的企业版），应" +
                "用 3443 于基于 Java 的应用服务器。Java 2 平台的发布，是 Java 发展过程中最重要的一个" +
                "里程碑，标志着 Java 的应用开始普及 9889 ";
        // 1.\\d表示任意一个数字
        String regStr = "\\d\\d\\d\\d";
        // 2.创建模式对象
        Pattern pattern = Pattern.compile(regStr);
        // 3.创建匹配器
        // 匹配器matcher，按照正则表达式的规则去匹配content
        Matcher matcher = pattern.matcher(content);

        // 4.开始匹配
        while (matcher.find()){
            System.out.println(matcher.group(0));
        }
    }
}
```



1、`matcher.find()`执行的操作：`Matcher`对象有一个`group`属性，在进行匹配时，找到第一个符合正则表达式的子串后，将子字符串的开始索引记录到`group[0]`中，将`结束索引+1`记录到`group[1]`中，同时，将`oldLast`属性赋值为子字符串`结束索引+1`的值，下次寻找就从`oldLast`开始。

第二次寻找时，找到符合的子串后，重新将开始索引记录到`group[0]`，`结束索引+1`记录到`group[1]`中

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305171736351.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305171739992.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305171743433.png)

2、`matcher.group(0)`  会返回`getSubSequence(groups[group * 2], groups[group * 2 + 1]).toString();`  即如果参数为0，那么返回的就是`group[0]`和`group[1]`范围内的子串

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305171747667.png)



```java
package com.level14.regexp;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegTheory {
    public static void main(String[] args) {
        // 找出所有四个数字连在一起的子串
        String content = "1998 年 12 月 8 日，第二代 Java 平台的企业版 J2EE 发布。1999 年 6 月，Sun 公司发布了" +
                "第二代 Java 平台（简称为 Java2）的 3 个版本：J2ME（Java2 Micro Edition，Java2 平台的微型" +
                "版），应用于移动、无线及有限资源的环境；J2SE（Java 2 Standard Edition，Java 2 平台的" +
                "标准版），应用于桌面环境；J2EE（Java 2Enterprise Edition，Java 2 平台的企业版），应" +
                "用 3443 于基于 Java 的应用服务器。Java 2 平台的发布，是 Java 发展过程中最重要的一个" +
                "里程碑，标志着 Java 的应用开始普及 9889 ";
        // 1.\\d表示任意一个数字
        String regStr = "(\\d\\d)(\\d\\d)";
        // 2.创建模式对象
        Pattern pattern = Pattern.compile(regStr);
        // 3.创建匹配器
        // 匹配器matcher，按照正则表达式的规则去匹配content
        Matcher matcher = pattern.matcher(content);

        // 4.开始匹配
        while (matcher.find()){
            System.out.println(matcher.group(0));
            System.out.println(matcher.group(1));
            System.out.println(matcher.group(2));
        }
    }
}
```

1、`String regStr = "(\\d\\d)(\\d\\d)";`几个括号表示分为几组。在进行匹配时，找到第一个符合正则表达式的子串后，将子字符串的开始索引记录到`group[0]`中，将`结束索引+1`记录到`group[1]`中，然后将第一组子字符串开始索引记录到`group[2]`中，将第一组子字符串的结束索引+1记录到`group[3]`中。将第二组子字符串的开始索引记录到`group[4]`中，将结束索引+1记录到`group[5]`中，以此类推

`matcher.group(0)`就是访问整个匹配的子字符串

`matcher.group(1)`访问第一个分组的子字符串

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305181042890.png)







### 15.2 正则表达式语法

正则表达式中元字符的功能分为

1、限定符

2、选择匹配符

3、分组组合和反向引用符

4、特殊字符

5、字符匹配符

6、定位符





#### 转义号`\\`

> 在Java中，两个`\\`代表其他语言的一个`\`
>
> 使用正则表达式去检索某些特殊字符时，需要使用转义字符，否则检索不到结果
>
> 需要用到转义符号的字符有：`.`、`+`、`(`、`)`、`$`、`/`、`\`、`?`、`[`、`]`、`^`、`{`、`}`

```java
package com.level14.regexp;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegExp01 {
    public static void main(String[] args) {
        String content = "abc$(a.bc(123( )";
        // 匹配$ => \\$
        // String regStr = "\\$";
        // 匹配( => \\(
        String regStr = "\\(";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
    }
}
```





#### 字符匹配符

| 符号 | 含义                                                     | 示例           | 解释                                                 | 匹配输入         |
| ---- | -------------------------------------------------------- | -------------- | ---------------------------------------------------- | ---------------- |
| [ ]  | 可接收的字符列表                                         | [efgh]         | e、f、g、h中任意一个字符                             |                  |
| [^]  | 不接收的字符列表                                         | [^abc]         | 除a、b、c之外的任意一个字符，包括数字和特殊符号      |                  |
| -    | 连字符                                                   | A-Z            | 任意单个大写字母                                     |                  |
| .    | 匹配除\n以外的任何字符                                   | a..b           | 以a开头，b结尾，中间包括2个任意字符的长度为4的字符串 | aaab、aekb、a3$b |
| \\\d | 匹配单个数字字符，相当于[0-9]                            | \\\d{3}{\\\d}? | 包含3个或4个数字的字符串                             | 123，8974        |
| \\\D | 匹配单个非数字字符，相当于 [ ^0-9]                       | \\\D(\\\d)*    | 以单个非数字字符开头，后接任意个数字字符串           | a、A123          |
| \\\w | 匹配单个数字、大小写字母、下划线，相当于[0-9a-zA-Z_]     | \\\d{3}\\\w{4} | 以3个数字字符开头跟上4个数字或字母下划线             | 123acvc、45612wd |
| \\\W | 匹配单个非数字、大小写字母、下划线，相当于[ ^0-9a-zA-Z_] | \\\W+\\\d{2}   | 以至少1个非数字字母下划线的字符开头，2个数字字符结尾 | %12、￥%45       |

```java
package com.level14.regexp;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegExp02 {
    public static void main(String[] args) {
        String content = "a11c8abc _ABCy @";
        // String regStr = "[a-z]"; // 匹配a-z之间任意一个字符
        // String regStr = "[A-Z]"; // 匹配A-Z之间任意一个字符
        // String regStr = "abc"; // 匹配abc字符串，默认区分大小写
        // String regStr = "(?i)abc"; // 匹配abc字符串，不区分大小写
        // String regStr = "[0-9]"; // 匹配0-9之间任意一个字符
        // String regStr = "[^a-z]"; // 匹配 不在 a-z 之间任意一个字符
        // String regStr = "[abcd]"; // 匹配 abcd 中任意一个字符
        // String regStr = "[\\d]"; // 匹配 任意一个数字
        // String regStr = "[\\D]"; // 匹配 任意一个非数字
        // String regStr = "[\\w]"; // 匹配 大小写英文字母, 数字，下划线
        // String regStr = "[\\W]"; // 匹配 非大小写英文字母, 数字，下划线
        // String regStr = "[\\s]"; // \\s 匹配任何空白字符(空格,制表符等)
        // String regStr = "[\\S]"; // \\S 匹配任何非空白字符 ,和\\s 刚好相反
        String regStr = "."; // 匹配除\n之外所有字符

        Pattern pattern = Pattern.compile(regStr);
        // Pattern pattern = Pattern.compile(regStr, Pattern.CASE_INSENSITIVE); 表示匹配时不区分大小写
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println("找到: " + matcher.group(0));
        }
    }
}
```





#### 选择匹配符

> 选择匹配时，既可以选这个，又可以选那个，使用`|`，注意不要使用`[]`括起来

```java
public class RegExp03 {
    public static void main(String[] args) {
        String content = "xuRuiJie 徐 许";
        String regStr = "xu|徐|许";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
    }
}
```







#### 限定符

**`java` 匹配默认贪婪匹配，即尽可能匹配多的**

**`[.?*]表示匹配的是字符本身`**

| 符号  | 含义                           | 示例        | 说明                                         | 匹配输入         |
| ----- | ------------------------------ | ----------- | -------------------------------------------- | ---------------- |
| *     | 指定字符重复0次或n次           | (abc)*      | 仅包含任意个abc的字符串，等效于\w*           | abc、abcabc      |
| +     | 指定字符重复1次或n次，至少一次 | m+(abc)*    | 以至少1个m开头，后接任意个abc的字符串        | m、mabc          |
| ?     | 指定字符重复0次或1次           | m+abc?      | 以至少1个m开头，后接ab或abc的字符            | mab、mabc、mmabc |
| {n}   | 只能输入n个字符                | [abcd]{3}   | 由abcd中字母组成的任意长度为3的字符串        | abc 、acd、bcd   |
| {n,}  | 至少n个匹配                    | [abcd]{3,}  | 由abcd中字母组成的任意长度至少为3的字符串    | aac、aaabcd      |
| {n,m} | 指定至少n个但不多于m个匹配     | [abcd]{3,5} | 由abcd中字母组成的任意长度在3到5之间的字符串 | abc、acdd、abcd  |

```java
public class RegExp04 {
    public static void main(String[] args) {
        String content = "a211111aaaaaahello";
        // String regStr = "a{3}"; // 匹配aaa
        // String regStr = "1{4}"; // 匹配1111
        // String regStr = "\\d{2}"; // 匹配任意两位数字
        // String regStr = "a{2,}"; // 匹配至少2个a【贪婪匹配，会尽力匹配多的】
        // String regStr = "a{2,3}"; // 匹配至少2个至多3个a【贪婪匹配，会尽力匹配多的】
        // String regStr = "\\d{2,5}"; // 匹配至少2个至多5个数字
        // String regStr = "1+"; // 匹配至少1个1
        // String regStr = "\\d+"; // 匹配1个或多个数字
        // String regStr = "1*"; // 匹配任意个1
        String regStr = "1?"; // 匹配0个或1个1
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println("找到  " + matcher.group(0));
        }
    }
}
```







#### 定位符

| 符号 | 含义                       | 示例                | 说明                                                         | 匹配输入   |
| ---- | -------------------------- | ------------------- | ------------------------------------------------------------ | ---------- |
| ^    | 指定起始字符，在[]表示取反 | ^[0-9]+[a-z]*       | 以至少1个数字开头，后边接任意多个小写字母【必须是字符串的开头】 | 123、9awda |
| $    | 指定结束字符               | ^[0-9]+\\\\-[a-z]+$ | 以至少 1 个数字开头, 后接 - ，并以至少一个小写字母结束       | 1-a        |
| \\\b | 匹配目标字符串的边界       | jie\\\b             | 字符串边界指子串之间有空格或者是目标串结束位置               |            |
| \\\B | 匹配目标字符串的非边界     | jie\\\B             | 和\\\b相反                                                   |            |

```java
public class RegExp05 {
    public static void main(String[] args) {
        String content = "xuruijiea jie abcjie";
        // String content = "132-abc";
        // String regStr = "^[0-9]+[a-z]*"; // 以至少1个数字开头，后接任意个小写字母的字符串
        // String regStr = "^[0-9]+\\-[a-z]+$"; // 以至少 1 个数字开头, 后接 - ，并以至少一个小写字母结束
        // String regStr = "jie\\b";  // 以jie为边界
        String regStr = "jie\\B";  // 不以jie为边界
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
    }
}
```







#### 捕获分组

- `(pattern)`：非命名捕获。捕获匹配的子字符串。`group[0]`为由整个正则表达式匹配的子串，`group[1]`为第一个分组，`group[2]`为第二个分组
- `(?<name>pattern)`：命名捕获。可以对`group`的分组命名，可以用`group[1]`返回或者用`group[name]`返回，用于`name`的字符串不能包含标点，不能以数字开头。可以使用单引号代替尖括号`(?'name'pattern)`

```java
public class RegExp06 {
    public static void main(String[] args) {
        String content = "xuRuiJie s1234xu5678xu";
        // 非命名分组
        // String regStr = "(\\d\\d)(\\d\\d)";
        // 命名分组
        String regStr = "(?<g1>\\d\\d)(?<g2>\\d\\d)";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.group(0));
            System.out.println(matcher.group(1));
            System.out.println(matcher.group(2));
            System.out.println("g1 = " + matcher.group("g1"));
            System.out.println("g2 = " + matcher.group("g2"));
        }
    }
}
```





#### 非捕获分组

- `(?:pattern)`：匹配pattern，但不捕获子表达式，即无法通过`group[0]`、`group[1]`等获取分组表达式
- `(?=pattern)`：例如`xuruijie(?=20|21|22)`，匹配`xuruijie22`中的`xuruijie`，但是不匹配`xuruijie10`中的`xuruijie`
- `(?!pattern)`：例如`xuruijie(?!20|21|22)`，不匹配`xuruijie22`中的`xuruijie`，但是匹配`xuruijie10`中的`xuruijie`

```java
public class RegExp07 {
    public static void main(String[] args) {
        String content = "xrj同学，dwadxrj老师14564wadxrj朋友";
        // 找到xrj同学、xrj老师、xrj朋友
        // String regStr = "xrj同学|xrj老师|xrj朋友";
        // String regStr = "xrj(同学|老师|朋友)"; // 可以用分组
        // String regStr = "xrj(?:同学|老师|朋友)"; // 不可以用分组

        // 找到xrj同学，xrj老师中的xrj
        // String regStr = "xrj(?=同学|老师)";

        // 找到不是xrj同学，xrj老师中的xrj
        String regStr = "xrj(?!同学|老师)";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
    }
}
```





#### 非贪婪匹配

> Java中正则表达式默认为贪婪匹配，即匹配尽量多的字符。
>
> 当' ? '   紧随任何其他限定符（\*、+、?、{n\}、{n,}、{n,m}）之后时，匹配模式是"非贪心的"。"非贪心的"模式匹配搜索到的、尽可能短的字符串。例如，在字符串"oooo"中，"o+?"只匹配单个"o"，而"o+"匹配所有"o"。



**练习**

```java
public class exercise01 {
    public static void main(String[] args) {
        // 1.验证汉字
        String content = "徐睿杰";
        String regStr = "^[\\u0391-\\uffe5]$+";

        // 2.邮政编码   要求是 1-9 开头的一个六位数. 比如：12389
        content = "123456";
        regStr = "^[1-9]\\d{5}$";

        // 3.QQ 号码 要求是 1-9 开头的一个(5 位数-10 位数) 比如: 12389 , 1345687 , 187698765
        content = "1234567";
        regStr = "^[1-9]\\d{4,9}$";

        // 4. 手机号码, 必须以 13,14,15,18 开头的 11 位数 , 比如 13588889999
        content = "13588889999";
        //regStr = "^13|14|15|18\\d{9}$";
        regStr = "^1(3|4|5|8)\\d{9}$";
        // regStr = "^[13|14|15|18]\\d{9}$";  使用|不要加[]，这个表示以1或|、3、4、5、8开头
        // regStr = "^1[?:3|4|5|8]\\d{9}$";

        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);
        if (matcher.find()) {
            System.out.println(matcher.group(0));
            System.out.println("匹配成功");
        }else {
            System.out.println("匹配失败");
        }
    }
}
```

```java
public class exercise02 {
    public static void main(String[] args) {
        // String content = "https://www.bilibili.com/video/BV1fh411y7R8?p=894&vd_source=dce029b15ce7bd573f7f0f69eb6255b2";
        String content = "http://edu.3dsmax.tech/yg/bilibili/my6652/pc/qg/05-51/index.html#201211-1?track_id=jMc0jn-hm-yHrNfVad37YdhOUh41XY" +
        "mjlss9zocM26gspY5ArwWuxb4wYWpmh2Q7GzR7doU0wLkViEhUlO1qNtukyAgake2jG1bTd23lR57XzV83E9bAXWkStcAh" +
        "4j9Dz7a87ThGlqgdCZ2zpQy33a0SVNMfmJLSNnDzJ71TU68Rc-3PKE7VA3kYzjk4RrKU";
        String regStr = "^((https|http)://)?([\\w-]+\\.)+[\\w-]+(/[\\w-?=.&#/]*)?$";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        if (matcher.find()) {
            System.out.println(matcher.group(0));
            System.out.println("匹配成功");
        }else
            System.out.println("匹配失败");
    }
}
```







### 15.3 正则表达式三个常用类

> `java.util.regex`包主要包括以下三个类：`Pattern`类，`Mathcer`类，`PatternSyntaxException`类

- `Pattern`类：`pattern`对象是一个正则表达式对象。`Pattern`类没有公共的构造方法。创建一个`Pattern`对象，需要调用其公共静态方法。该方法接收一个正则表达式对象作为第一个参数。`Pattern pattern = Pattern.compile(regStr);`
- `Mathcer`类：`Matcher` 对象是对输入字符串进行解释和匹配的引擎。与`Pattern`类一样，`Matcher`也没有公共构造方法。需要调用`Pattern`对象的`matcher `方法来获得一个`Matcher`对象
- `PatternSyntaxException`类：`PatternSyntaxException`是一个非强制异常类，它表示一个正则表达式模式中的语法错误。





#### `Pattern`

`Pattern.matches(regStr, content);`  `matches`方法用于整体匹配，判断`content`的内容是否满足`regStr`的规则，返回一个`boolean`值

`Pattern.matches`方法本质上还是创建一个`Pattern`对象，然后创建一个`matcher`调用他的`matches()`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305191420675.png)

```java
public class PatternMethod {
    public static void main(String[] args) {
        String content = "xrj12345xrjd adwa xrj";
        String regStr = "xrj.*";
        boolean matches = Pattern.matches(regStr, content);
        System.out.println(matches);

        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);
        boolean matches1 = matcher.matches();
        System.out.println(matches1);
    }
}
```





#### `Matcher`类常用方法

`matcher.start()`：找到每次匹配到的子字符串的起始索引

`matcher.end()`：找到每次匹配到的子字符串的结束索引+1

`matcher.replaceAll("aaa")`：将找到的子字符串全部替换为aaa

```java
public class PatternMethod2 {
    public static void main(String[] args) {

        String content = "xrj12345xrjd adwa xrj";
        String regStr = "xrj";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.start() + " " + matcher.end());
            System.out.println(content.substring(matcher.start(), matcher.end()));
        }

        // 返回的字符串是替换后的，原字符串不变
        String replaceAll = matcher.replaceAll("xyy");
        System.out.println(replaceAll);
    }
}
```







### 15.4 反向引用

1、分组：我们可以用圆括号组成一个比较复杂的匹配模式，那么一个圆括号的部分我们可以看作是一个子表达式/一个分组。

2、捕获：把正则表达式中子表达式/分组匹配的内容，保存到内存中以数字编号或显式命名的组里，方便后面引用，从左向右，以分组的左括号为标志,第一个出现的分组的组号为1，第二个为2，以此类推。组0代表的是整个正则式

3、反向引用：圆括号的内容被捕获后，可以在这个括号后被使用，从而写出一个比较实用的匹配模式，这个我们称为反向引用，这种引用既可以是在正则表达式内部，也可以是在正则表达式外部，内部反向引用**`\\分组号`**，外部反向引用**`$分组号`**

```java
public class RegExp09 {
    public static void main(String[] args) {
        String content = "1221345 4ADW 12333331212345-111222333444";
        // 找到2个连续相同的数字
        String regStr = "(\\d)\\1";

        // 找到5个连续相同的数字
        regStr = "(\\d)\\1{4}";

        // 找到4个连续的数字，第1位与第4位相同，第2位与第3位相同
        regStr = "(\\d)(\\d)\\2\\1";

        // 在字符串中检索商品编号，前边是5个数字，然后一个-，然后是一个9位数，连续的每3位相同
        regStr = "\\d{5}-(\\d)\\1{2}(\\d)\\2{2}(\\d)\\3{2}(\\d)\\4{2}";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);

        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
    }
}
```





**去除重复字符**

```java
public class exercise03 {
    public static void main(String[] args) {
        // 将以下内容改为我要学编程java
        String content = "我....我要....学学学学....编程java!";

        // 1.去掉.
        String regStr = "\\.";
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);
        content = matcher.replaceAll("");


        // 2.去掉重复字符
        /*
        regStr = "(.)\\1+";
        pattern = Pattern.compile(regStr);
        matcher = pattern.matcher(content);
        while (matcher.find())
            System.out.println(matcher.group(0));
        content = matcher.replaceAll("$1");
         */
        regStr = "(.)\\1+";
        content = Pattern.compile(regStr).matcher(content).replaceAll("$1");

        System.out.println(content);
    }
}
```







### 15.5 String中使用正则表达式

- 替换：`String`中的`replaceAll`底层就是使用正则表达式来进行替换的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305191516450.png)

- 判断功能：`String`的`matches`方法可以判断字符串是否符合这一规则，底层使用正则表达式

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305191517069.png)

- 分割功能：`String`的`split`方法用来进行字符串分割，底层也是使用正则表达式

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305191521413.png)

```java
public class RegExp10 {
    public static void main(String[] args) {
        String content = "2000 年 5 月，JDK1.3、JDK1.4 和 J2SE1.3 相继发布，几周后其" +
                "获得了 Apple 公司 Mac OS X 的工业标准的支持。2001 年 9 月 24 日，J2EE1.3 发" +
                "布。" +
                "2002 年 2 月 26 日，J2SE1.4 发布。自此 Java 的计算能力有了大幅提升";
        // 使用正则表达式将JDK1.3, JDK1.4替换为JDK
        String regStr = "JDK1\\.3|JDK1\\.4";
        content = content.replaceAll(regStr, "JDK");
        System.out.println(content);

        // 验证一个 手机号， 要求必须是以 138 139 开头
        content = "13838545689";
        regStr = "1(38|38)\\d{8}";
        System.out.println(content.matches(regStr));

        // 按照 # 或者 - 或者 ~ 或者 数字 来分割
        content = "hello#abc-jack123smith~北京";
        String[] split = content.split("#|-|~|\\d+");
        // String[] split = content.split("[-#~\\d+]");
        for (String s : split)
            System.out.println(s);
    }
}
```

























