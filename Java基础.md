## 1.Java基础知识

**1、跨平台**

一次编译，多次运行。将.java文件编译后变为.class文件，.class文件可以在各种操作系统运行，但是需要装java虚拟机

![image-20221102162910623](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20221102162910623.png)





**2、Java虚拟机**

JDK包括JRE和java开发工具(java、javac、javadoc等)，JRE包括JVM和核心类库，因此使用java需要安装jdk







**3、Java开发注意事项**

- 一个源文件只能有一个public类，其他类的个数不限
- 如果源文件包含一个public类，则文件名必须按该类名命名
- 一个源文件只能有一个public类，其他类的个数不限，也可以将main方法写在非public类中，然后指定运行非public类，这样入口就是非public的main方法





**4、Java转义字符**

```
\t				一个制表位，实现对齐功能
\n				换行
\\				一个\
\"				一个"
\'				一个'
\r				一个回车
```





**5、注释**

- 单行注释： // 

- 多行注释    /*  */

- 文档注释    /** */

  ```java
  /**
  * @author xrj
  * @version 1.0
  */
  ```

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20221102163842273.png" style="zoom:80%;" />









## 2. 变量



**程序中+的使用**

- 当左右两边都是数值类型时，做加法运算
- 当左右两边有一个是字符串类型，则做拼接运算

```java
System.out.println(100 + 98); 	// 198
System.out.println("100" + 98); 	// 10098
System.out.println(100 + 3 + "hello"); 	// 103hello
System.out.println("hello" + 3 + 100); 	// hello3100
```



### 2.1 数据类型

![image-20221102185631470](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20221102185631470.png)



**1、整型**

- Java各整数类型有固定的的范围和字段长度，不受具体OS的影响
- Java整型默认为int，声明long型常量后边要加 ‘ l ’ 或者 ‘ L ’ 



| 类型  | 占用内存 |
| ----- | -------- |
| byte  | 1字节    |
| short | 2字节    |
| int   | 4字节    |
| long  | 8字节    |

```java
public class test{
    public static void main(String[] args) {
        long a = 4l;
        // int b = 4l; 不对
        System.out.println(a);
    }
}
```





**2、浮点型**

- float占4字节，double占8字节
- 浮点数=符号位+指数位+尾数位
- 尾数部分可能丢失，造成精度损失(小数都是近似值)
- Java浮点类型有固定的的范围和字段长度，不受具体OS的影响
- Java浮点类型默认为double型，声明float型，后边要加 ‘ f ’  或 ‘ F ’
- Java的科学计数法默认double类型

```c++
public class test{
    public static void main(String[] args) {
        long a = 4l;
        // int b = 4l; 不对
        System.out.println(a);

        float c = 3.4f;
        // float d = 3.4;  不对
        double d = 3.4f;
        double e = 3.4;
        System.out.println(c);
        System.out.println(d);
        System.out.println(e);
    }
}
```







**3、字符类型**

​	字符类型可以表示单个字符,字符类型是 char，**char 是两个字节**(可以存放汉字)



​	**字符使用细节**

- Java中，char的本质是一个整数，在输出时，是unicode码对应的字符

- 可以直接给char赋一个整数，然后输出时，会按照对应的unicode字符输出，97 => a

- char类型是可以进行运算的，相当于一个整数，因为它都对应有Unicode码

  ```java
  public class CharDetail {
  	//编写一个 main 方法
  	public static void main(String[] args) {
  		//在 java 中，char 的本质是一个整数，在默认输出时，是 unicode 码对应的字符
  		//要输出对应的数字，可以(int)字符
  		char c1 = 97;
  		System.out.println(c1); // a
  		char c2 = 'a'; //输出'a' 对应的 数字
  		System.out.println((int)c2);
          
  		char c3 = '徐';
  		System.out.println((int)c3);//24464
  		char c4 = 24464;
  		System.out.println(c4);//徐
          
  		//char 类型是可以进行运算的，相当于一个整数，因为它都对应有 Unicode 码
  		char c5 = 'b' + 1;//98+1==> 99
  		System.out.println((int)c5); //99
  		System.out.println(c5); //99->对应的字符->编码表 ASCII(规定好的)=>c
  	}
  }
  
  ```
  
  
  
- 字符型存储到计算机中，需要将字符对应的码值（整数）找出来，比如 ‘ a '

  存储：' a ' ==> 码值97 ==> 二进制（110 0001）==>存储

  读取：二进制（110 0001）==> 97 ==> ' a ' => 显示

- Ascll码：用一个字节表示，一共128个字符，实际上可以表示256个，但只用了128个

- Unicode码：固定大小的编码，使用两个字节表示字符，字母和汉字统一都是占用两个字节，Unicode兼容Ascll编码

- utf-8编码：大小可变，字母使用1个字节，汉字使用3个字节

- GBK：字母使用1个字节，汉字使用2个字节





**4、布尔类型boolean**

- boolean类型数据只允许存放true和false
- boolean占一个字节
- boolean不能用0或1代替，逻辑运算也不能用0或1



**5、String**

String是引用类型

创建String有两种方式

`String str1 = "aa";`直接赋值的方法str1指向常量池中的aa（如果aa不存在就创建）

`String str2 = new String("hello");`new方式创建，现在堆区实例化一个对象，然后该对象指向常量池中的字符串

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230118203831189.png" alt="image-20230118203831189" style="zoom:80%;" />



### 2.2 基本数据类型转换

#### 2.2.1自动类型转换

当java程序在进行赋值或者运算时，精度小的类型会自动转换为精度大的数据类型，这就是自动类型转换

![image-20221103222025204](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20221103222025204.png)





**注意细节**

1. 有多种类型的数据混合运算时，系统首先自动将所有数据转换成容量最大的哪种数据类型，然后再进行计算
2. 当我们把精度（容量）大的数据类型转化为精度（容量）小的数据类型时，就会报错，反之不会
3. （byte、short）和char之间不会相互自动转换
4. byte、short、char三者可以计算，在计算时首先转换为int类型
5. boolean不参与转换
6. 自动提生原则：表达式结果的类型自动提升为操作数中最大的类型

```java
//自动类型转换细节
public class AutoConvertDetail {
	
	public static void main(String[] args) {
		//细节 1： 有多种类型的数据混合运算时，
		//系统首先自动将所有数据转换成容量最大的那种数据类型，然后再进行计算
		int n1 = 10; 
		//float d1 = n1 + 1.1;//错误 n1 + 1.1 => 结果类型是 double
		//double d1 = n1 + 1.1;//对 n1 + 1.1 => 结果类型是 double
		float d1 = n1 + 1.1F;//对 n1 + 1.1 => 结果类型是 float
        
        
		//细节 2: 当我们把精度(容量)大 的数据类型赋值给精度(容量)小 的数据类型时，
		//就会报错，反之就会进行自动类型转换。
		//int n2 = 1.1;//错误 double -> int
        
        
		//细节 3: (byte, short) 和 char 之间不会相互自动转换
		//当把具体数赋给 byte 时，(1)先判断该数是否在 byte 范围内，如果是就可以
		byte b1 = 10; //对 , -128-127
		// int n2 = 1; //n2 是 int
		// byte b2 = n2; //错误，原因： 如果是变量赋值，判断类型
		// char c1 = b1; //错误， 原因 byte 不能自动转成 char
		
	
		//细节 4: byte，short，char 他们三者可以计算，在计算时首先转换为 int 类型
		byte b2 = 1;
		byte b3 = 2;
		short s1 = 1;
		//short s2 = b2 + s1;//错, b2 + s1 => int
		int s2 = b2 + s1;//对, b2 + s1 => int
		//byte b4 = b2 + b3; //错误: b2 + b3 => int
}

```







#### **2.2.2 强制数据类型转换**

将容量大的数据类型转换为容量小的数据类型。使用时要加上强制转换符 ( )，但可能造成 精度降低或溢出

- 当进行数据的大小从大 = > 小，就需要用到强制类型转换
- char类型可以保存int的常量值，但不能保存int的变量值，需要强转



```java
public class ForceConvertDetail {

	public static void main(String[] args) {
		//演示强制类型转换
		//强转符号只针对于最近的操作数有效，往往会使用小括号提升优先级
		//int x = (int)10*3.5+6*1.5;//编译错误： double -> int
		int x = (int)(10*3.5+6*1.5);// (int)44.0 -> 44
		System.out.println(x);//44
        
		char c1 = 100; //ok
		int m = 100; //ok
		//char c2 = m; //错误
		char c3 = (char)m; //ok
		System.out.println(c3);//100 对应的字符, d 字符
	}
}
```







#### **2.2.3 基本数据类型和String的转换**

- 基本数据类型转String：将基本类型的值+“ ”即可
- String类型转基本类型：通过基本类型的包装类调用parseXX方法即可
- String获取单个字符：`s.charAt(0);`



```java
public class StringToBasic {

	public static void main(String[] args) {
		//基本数据类型->String
		int n1 = 100;
		float f1 = 1.1F;
		double d1 = 4.5;
		boolean b1 = true;
		String s1 = n1 + "";
		String s2 = f1 + "";
		String s3 = d1 + "";
		String s4 = b1 + "";
		System.out.println(s1 + " " + s2 + " " + s3 + " " + s4);
        
        
		//String->对应的基本数据类型
		String s5 = "123";    
		//使用 基本数据类型对应的包装类，的相应方法，得到基本数据类型
		int num1 = Integer.parseInt(s5);
		double num2 = Double.parseDouble(s5);
		float num3 = Float.parseFloat(s5);
		long num4 = Long.parseLong(s5);
		byte num5 = Byte.parseByte(s5);
		boolean b = Boolean.parseBoolean("true");
		short num6 = Short.parseShort(s5);
        
		System.out.println("===================");
		System.out.println(num1);//123
		System.out.println(num2);//123.0
		System.out.println(num3);//123.0
		System.out.println(num4);//123
		System.out.println(num5);//123
		System.out.println(num6);//123
		System.out.println(b);//true      
	}
}
```







## 3. 运算符

### 3.1 算术运算符

```java
// 面试题1
int i = 1;
i = i++;
System.out.println(i);	// 输出为1
/*
	(1)创建一个临时变量temp = i;
	(2)i = i + 1;
	(3)i = temp;
*/


// 面试题2
int i = 1;
i = ++i;
System.out.println(i);	// 输出为2
/*
	(1)i = i + 1;
	(2)temp = i;
	(3)i = temp;
*/

```





### 3.2 **逻辑运算符**

- a&b : & 叫逻辑与：规则：当 a 和 b 同时为 true ,则结果为 true, 否则为 false 

​		不管第一个条件是否为 false，第二个条件都要判断

- a&&b : && 叫短路与：规则：当 a 和 b 同时为 true ,则结果为 true,否则为 false 

​		如果第一个条件为 false，则第二个条件不会判断，最终结果为 false

- a|b : | 叫逻辑或，规则：当 a 和 b ，有一个为 true ,则结果为 true,否则为 false 

​		不管第一个条件是否为 true，第二个条件都要判断

- a||b : || 叫短路或，规则：当 a 和 b ，有一个为 true ,则结果为 true,否则为 false 

  如果第一个条件为 true，则第二个条件不会判断，最终结果为 true

- !a : 叫取反，或者非运算。当 a 为 true, 则结果为 false, 当 a 为 false 是，结果为 true
- a^b: 叫逻辑异或，当 a 和 b 不同时，则结果为 true, 否则为 false





### 3.3 **赋值运算符**

- 运算顺序从右往左 `int num = a + b + c;`

- 赋值运算符的左边 只能是变量,右边 可以是变量、表达式、常量值 

  `int num = 20; int num2= 78 * 34 - 10; int num3 = a; `

- **复合赋值运算符会进行类型转换**

```java
public class AssignOperator {
	public static void main(String[] args) {
		//复合赋值运算符会进行类型转换
		byte b = 3;
        // b = b + 3; 会报错，b + 3为int类型，不能赋值给byte
		b += 2; // 等价 b = (byte)(b + 2);
		b++; // b = (byte)(b + 1);
	}
}
```





**运算符优先级**

![image-20221105184605377](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20221105184605377.png)





### 3.4 **命名规范**

- 包名：多单词组成时所有字母都小写：com.xrj.dao
- 类名、接口名：多单词组成时，所有单词的首字母大写：XxxYyyZzz [大驼峰] 比如： TankShotGame 
- 变量名、方法名：多单词组成时，第一个单词首字母小写，第二个单词开始每个单词首字母大写：xxxYyyZzz [小驼峰] 比如tankShotGame 
- 常量名：所有字母都大写。多单词时每个单词用下划线连接：XXX_YYY_ZZZ 比如 ：定义一个所得税率 TAX_R







### 3.5 **输入**

1. 导入 java.util.*包
2. 创建该类对象
3. 调用功能

```java
import java.util.Scanner;

public class Demo01_scanner {
    public static void main(String[] args){

        Scanner sc = new Scanner(System.in);

        int a = sc.nextInt();	// 接收int
        float b = sc.nextFloat();	// 接收float
        double a = sc.nextDouble();	// 接收double
        String name = sc.next(); // 接收字符串

        System.out.println(a); 
        sc.close(); 
    }
}
```







### 3.6 位运算

- 二进制：0,1 ，以 0b 或 0B 开头。 
- 十进制：0-9 
- 八进制：0-7 ， 以数字 0 开头表示。 
- 十六进制： 以 0x 或 0X 开头表示。



- Java没有无符号数
- 0的反码，补码都是0
- **计算机运行时，都是以补码的方式运算**

> 当两个数字按位与或时，需要转为补码后再操作，操作后结果是补码，需要转为原码

```java
public class test{
    public static void main(String[] args) {
 
        System.out.println(~2);    // -3
        System.out.println(2 & 3); // 2
        System.out.println(2 | 3); // 3
        System.out.println(~-5);   // 4
        System.out.println(-3^3);  // -2
    }
}
```



按位与：&

按位或：|

按位异或：^

按位取反：~

算术右移 >>：低位溢出，符号位不变，并用符号位补溢出的高位

算术左移 <<：符号位不变，低位补 0

逻辑右移（无符号右移）>>>：低位溢出，高位补0

```java
public class test{
    public static void main(String[] args) {
  
        int a = 10 >> 2;    // 2
        int b = -1 >> 2;    // -1 
        int c = 1 << 2;     // 4
        int d = -1 << 2;    // -4
        int e = 8 >>> 2;    // 2
        int f = -8 >>> 2;   // 1073741822，无符号右移，-8的补码前边全是1，右移2位，前两位补0，变为正数
        
        System.out.println("a = " + a);
        System.out.println("b = " + b);
        System.out.println("c = " + c);
        System.out.println("d = " + d);
        System.out.println("e = " + e);
        System.out.println("f = " + f);
    }
}
```





## 4. 分支结构

### 4.1 switch分支

1. 表达式数据类型，应该和case后的常量类型一致，或者是可以自动转成可以相互比较的类型，比如输入字符，常量为int
2. switch(表达式)中表达式的返回值必须是：（byte、short、int、char、emum[枚举]、String）
3. case子句中的值必须是常量，不能是变量
4. Default子句是可选的，当没有匹配的case时，执行Default
5. break语句用来在执行完一个case分支后使程序跳出switch语句块；如果没有写break，程序会顺序执行到switch末尾，除非遇到break



### 4.2 break和continue的标签

1. break可以指定退出哪一层
2. abc1是标签，名字自己起
3. break后指定到那个标签就退出到哪里
4. 没有指定标签，就退出当前最近的循环体
5. continue也可以通过标签指定要跳过的是哪一层循环

```java
public class test{
    public static void main(String[] args) {
        abc1:
        for (int i = 0; i < 5; i++){
            abc2:
            for (int j = 1; i < 5; j++)
            {
                if (j == 4)
                    break abc1;
                System.out.println(j);
            }
        }
    }
}
```



```java
public class test{
    public static void main(String[] args) {
 
        abc1:
        for (int i = 0; i < 5; i++){
            abc2:
            for (int j = 1; i < 5; j++)
            {
                if (j == 2)
                    continue abc1;
                System.out.println(j);
            }
        }
    }
}
```





## 5. 数组

### 5.1 数组的使用

1、动态初始化

`数据类型 数组名[] = new 数据类型[大小];` 

`数据类型[] 数组名 = new 数据类型[大小]`

`int a[] = new int[5];`

`int[] a = new int[5];`



2、静态初始化

`数据类型 数组名[] = {元素1, 元素2...};`



3、数组创建后，如果没有赋值，有默认值

`int 0，short 0, byte 0, long 0, float 0.0,double 0.0，char \u0000，boolean false，String null`

引用数据类型默认值为null



4、数组名.length表示数组长度



**注意：**

```java
// int a[] = new int{1, 2, 3};     // 错误
int a[] = new int[]{1, 2, 3};      // 正确
// int b[] = new int[3]{1, 2, 3};  // 错误，这样定义后边不能加数字
```





### 5.2 数组赋值机制

1) 基本数据类型赋值，这个值就是具体的数据，而且相互不影响，值传递。

   ` int n1 = 2; int n2 = n1; `

2) 数组在默认情况下是引用传递，赋的值是地址



![image-20230116172227034](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230116172227034.png)



> 如果开辟两个数组，使用了两个空间，arr1和arr2，如果执行arr1 = arr2；让arr1指向arr2的地址，此时arr1本身的地址没有变量使用，因此jvm会将该内存空间回收掉





### 5.3 二维数组

**声明：**

`int a[][] = new int[3][3];`

`int[][] a = new int[3][];`

`int a[][] = {{1, 2, 3}, {2, 3}, {1}};`



**二维数组内存布局**

![image-20230116211953386](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230116211953386.png)



打印杨辉三角

```java
public class test{
    public static void main(String[] args) {

        int a[][] = new int[12][];
        
        for (int i = 0; i < a.length; i++){
            a[i] = new int[i + 1];
            for (int j = 0; j < a[i].length; j++){
                if (j == 0 || j == a[i].length - 1)
                    a[i][j] = 1;
                else
                    a[i][j] = a[i - 1][j - 1] + a[i - 1][j];
            }
        }

        for (int i = 0; i < a.length; i++){
            for (int j = 0; j < a[i].length; j++)
                System.out.print(a[i][j] + " ");
            System.out.println();
        }
    }
}
```





**二维数组声明方式：**

`int[][] a;`

`int a[][];`

`int[] a[];`



二维数组实际上是由多个一维数组组成的，它的各个一维数组的长度可以相同，也可以不相同





## 6. 类与对象基础

### 6.1 对象在内存中的形式

> 类是引用类型，因此在堆区开辟内存，类中的age为int型，直接放在堆区，但是String是引用类型，String放在方法区的常量池中

![image-20230118203256596](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230118203256596.png)





### 6.2 成员属性

- 成员属性有四种访问修饰符 public, proctected, 默认, private 
- 属性的定义类型可以为任意类型，包含**基本类型或引用类型** 
- 属性如果不赋值，有默认值，规则和数组一致。





### 6.3 对象内存分配机制

> 对象是引用类型，因此赋值时也是传递的地址
>
> Person p2类似于C++中Person* p2，创建一个指针指向p1的地址

```java
Person p1 = new Person();
p1.age = 10;
p1.name = "小明";
Person p2 = p1;
System.out,println(p2.age);	// 10
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230122162515663.png" alt="image-20230122162515663" style="zoom:77%;" />



**Java 内存的结构分析** 

1）栈： 一般存放基本数据类型(局部变量) 

2）堆： 存放对象(Cat cat , 数组等) 

3）方法区：常量池(常量，比如字符串)， 类加载信息 



**Java创建对象流程分析**

```java
Person p = new Person();
p.name = "xrj";
p.age = 10;
```

1）先加载 Person 类信息(属性和方法信息, **只会加载一次**) 

2）在堆中分配空间, 进行默认初始化

3）把地址赋给 p

4）进行指定初始化





### 6.4 方法调用机制

```java
Person p1 = new Person();
int res = p1.getSum(10, 20);
System.out.println(res);

public int getSum(int num1, int num2){
	int res = num1 + num2;
	return res;
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230122204120434.png" alt="image-20230122204120434" style="zoom:80%;" />



1）当程序执行到方法时，就会开辟一个独立的空间（栈空间）

2）当方法执行完毕，或者执行到return语句时，就会返回，同时这块栈空间会被释放，然后返回到调用方法的地方

3）返回后，继续执行方法后边的代码

4）当main方法执行完毕（栈空间释放），整个程序退出





### 6.5 方法使用

**方法的定义**

```java
访问修饰符 数据类型 方法名(形参列表){
	方法体
	return 返回值;
}
```



- 如果不写访问修饰符：默认访问，（public、private、protect、默认）

- 同一个类中的方法调用，直接调用即可
- 跨类中的方法A调用B类方法，需要通过对象名调用





### 6.6 成员方法传参

**基本数据类型传参**

基本数据类型传参是值传递，形参不会修改实参的值



**引用数据类型传参**

> 引用类型创建是在堆区创建，传递时传递的是地址，因此形参改变会影响实参，类似于C++中传指针或传引用







### 6.7 方法重载

1、方法名必须相同

2、形参列表：必须不同（数据类型或个数或顺序）

3、返回值类型无要求





### 6.8 可变参数

Java 允许将同一个类中**多个同名同功能**但**参数个数不同**的方法，封装成一个方法。 就可以通过可变参数实现

```java
访问修饰符 数据类型 方法名(数据类型... 形参){
}
```



```java
public class test {
    public static void main(String[] args) {
        Tool t = new Tool();
        System.out.println(t.sum(10, 23, 20));  // 53
        System.out.println(t.sum(10, 20));  // 30
    }
}

class Tool {
    // 1.int...表示接收的是可变参数，类型是int，即可以接收多个int(0-n)
    // 2.使用可变参数时，可以当做数组使用
    public int sum(int... nums) {
        int res = 0;
        for (int i = 0; i < nums.length; i++)
            res += nums[i];
        return res;
    }
}
```



- 可变参数的实参可以为0个或任意多个
- 可变参数的实参可以为数组
- 可变参数本质就是数组
- 可变参数可以和普通类型的参数一起放在形参列表，但可变参数必须在最后边
- 一个形参列表只能有一个可变参数





### 6.9 作用域

1、Java中，主要的变量就是属性（成员变量）和局部变量

2、局部变量一般是指在成员方法中定义的变量

3、全局变量也就是属性，作用域为整个类；局部变量是除了属性之外的其他变量，作用域为定义他的代码块

4、全局变量可以不赋值直接使用，因为有默认值；局部变量必须赋值后使用，因为他没有默认值

**5、成员方法中定义的数组可以不赋值，因为他是定义在堆区的，在堆区定义的变量都有默认值；类创建在堆区，会为属性在堆区开辟空间，因此属性即全局变量有默认值；成员方法调用时是在栈区开辟空间的，成员方法中的变量在方法执行完毕后都会释放，栈区的数据也没有默认值**





- 属性生命周期较长，伴随着对象的创建而创建，伴随着对象的销毁而销毁；局部变量生命周期较短，伴随着代码块的执行而创建，伴随着代码块的结束而销毁
- 全局变量（属性）：可以被本类使用，或其它类使用
- 局部变量：只能在本类中的本方法中使用
- 全局变量（属性）可以加修饰符；局部变量不可以加修饰符







### 6.10 构造方法/构造器

构造方法主要用来完成对象的初始化

```java
[修饰符] 方法名(形参列表){
	方法体
}
```

1、构造器的修饰符可以默认， 也可以是 public、 protected 、private 

2、构造器没有返回值 

3、方法名和类名必须一样 

4、参数列表和成员方法一样的规则

5、构造器的调用, 由系统完成。在创建对象时，系统会自动的调用该类的构造器完成对象的初始化



- 一个类可以定义多个不同的构造器，即构造器重载
- 构造器是完成对象的初始化，并不是创建对象
- 如果定义类的时候没有定义构造器，系统会给类生成一个默认的构造器（无参构造器），比如`Dog(){}`，使用javap指令反编译查看

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230128203206030.png" alt="image-20230128203206030" style="zoom:70%;" />



- 一旦定义了构造器，默认的构造器就被覆盖了，就不能使用默认的无参构造器。除非显示的定义，即`Dog(){}`，下边的类Dog定义了有参构造方法，用`javap`反编译后可以看到没有默认构造方法了

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230128204118314.png" alt="image-20230128204118314" style="zoom:80%;" />

> javap是JDK提供的一个命令行工具，javap能对给定的class文件提供的字节码进行反编译，可以将class字节码文件反编译为我们可以看懂的源文件





### 6.11 对象创建流程分析

```java
class Person{
	int age = 90;
    String name;
    Person(String n, int a){
        name = n;
        age = a;
    }
}
Person p = new Person("xrj", 20);
```

1、加载Person类信息（Person.class），只会加载一次

2、在堆中分配内存

3、完成对象初始化

​	3.1 默认初始化，分配空间后，int默认为0，String默认为null，`age = 0; name = null;`

​	3.2 显示初始化，`age = 90; name = null;`

​	3.3 构造器的初始化，`age = 20; name = "xrj";`

4、对象在堆中的地址返回给p。p是对象名，也是对象的引用





### 6.12 this关键字

Java虚拟机给每个对象分配this，指向当前对象

> Java中不能输出对象的地址，但是可以通过hashCode来判断，this的hashCode和对象的hashCode一定是相同的



1、this关键字可以用来访问本类的属性、方法、构造器

2、this用来区分当前类的属性和局部变量

3、访问成员方法：`this.方法名(参数);`

4、访问构造方法：`this(参数);`注意只能在构造器中使用（即只能在构造器中访问另外一个构造器,且必须放在第一条语句）

5、 this 不能在类定义的外部使用，只能在类定义的方法中使用。

```java
public class test {
    public static void main(String[] args) {
        Person p1 = new Person();
    }
}

class Person {
    String name;
    int age;

    public Person(){
        this("xrj", 20);
        System.out.println("Person()构造器被调用");
    }
    public Person(String name, int age) {
        System.out.println("Person(String name, int age)构造器被调用");    
    }
}
```







### 6.12 本章作业

分析以下代码输出结果

```java
public class test {
    int count = 9;
    public void count1(){
        count = 10;
        System.out.println(count);
    }
    public void count2(){
        System.out.println(count++);
    }
    public static void main(String[] args) {
        new test().count1();    // 10

        test t = new test();
        t.count2();     // 9
        t.count2();     // 10
    }
}
```

`new test().count1();`

`new test();`创建一个匿名对象，执行一次对象就被释放。执行时，在堆区开辟一个空间，属性count=9，但是没有指向该对象的引用，创建对象后立刻执行`count1()`方法，输出10，该语句执行完后，空间立即被释放。

`test t = new test();`创建一个test对象，属性count=9，用t指向他，然后执行两次count2()，输出9和10。







## 7. 类与对象进阶

### 7.1 包

**包的三大作用：**

1、区分相同名字的类

2、当类很多时，可以很好的管理类

3、控制访问范围



包基本语法

`package com.xrj;`

`package`	关键字表示打包

`com.xrj	`	表示包名



**包的本质**就是创建不同的文件夹/目录来保存类文件



**包的命名**

只能包含数字、字母、下划线、小圆点，但是不能是数字开头，不能是关键字或保留字



**常用的包**

`java.lang.*`	// lang包是基本包，默认引入

`java.util.*`	// util包，系统提供的工具包、工具类

`java.net.*`	  // 网络包，网络开发 

`java.awt.*`	  // 做java界面开发



**注意**

1、package的作用是声明当前类所在的包，需要放在类的最上面，一个类最多一个package

2、import指令位置在package下面，在类定义前面，可以有多条





### 7.2 访问修饰符

java 提供四种访问控制修饰符号，用于控制方法和属性(成员变量)的访问权限（范围）

- public：公开级别，对外公开 

- protected：受保护级别，对子类和同一个包中的类公开 

- 默认：没有修饰符号，向同一个包的类公开

- privatre：私有级别，只有类本身可以访问，不对外公开.

| 访问级别 | 访问修饰符 | 同类 | 同包 | 子类 | 不同包 |
| -------- | ---------- | ---- | ---- | ---- | ------ |
| 公开     | public     | √    | √    | √    | √      |
| 保护     | protected  | √    | √    | √    | ×      |
| 默认     |            | √    | √    | ×    | ×      |
| 私有     | private    | √    | ×    | ×    | ×      |



**注意**

1、修饰符可以用来修饰类中的属性，成员方法以及类

**2、只有默认和public才可以修饰类**

3、默认的属性和方法，子类不能访问是在不同包下不能访问，同一个包下可以访问





### 7.3 封装

> 封装就是把抽象出来的数据（属性）和对数据的操作（方法）封装在一起，数据被保护在内部，程序的其他部分只有通过被授权的操作（方法），才能对数据进行操作



封装的好处

1. 隐藏方法实现细节
2. 可以对数据进行验证，保证安全合理



**封装的步骤**

1、对属性私有化（不能直接修改属性）

2、提供一个公共的（public）set方法，用于对属性判断并赋值

3、提供一个公共的（public）get方法，用于获取属性的值



**将构造器与setXxx结合**

> 虽然通过set方法对属性赋值行进行了验证（比如年龄在18-60之间）。但是如果通过有参构造来创建对象，通过构造方法将年龄赋值为200，仍不合法，因此可以在构造方法内部调用set方法来进行赋值操作





### 7.4 继承

**继承的基本语法**

`class 子类 enxtends 父类{}`

- 子类会自动拥有父类定义的属性和方法
- 父类又叫超类、基类
- 子类又叫派生类



**注意**

1、子类继承了所有的属性和方法，非私有的属性和方法可以在子类直接访问，（默认级别的属性如果子类在同一个包下那么可以访问，如果不在同一个包下就不可以访问），但是私有属性和方法不能在子类直接访问，要通过父类提供公共的方法去访问

2、创建子类时，会先调用父类的构造器，完成父类的初始化

3、当创建子类对象时，不管使用子类的哪个构造器，**默认情况下**总会去调用父类的无参构造器，如果父类没有提供无参构造器，则必须在子类的构造器中用 super 去指定使用父类的哪个构造器完成对父类的初始化工作，否则，编译不会通过

`super();	// 调用无参构造器`

`super(”xrj");	//调用带一个String参数的构造器`

4、如果希望指定去调用父类的某个构造器，则显式的调用一下 : `super(参数列表);`

**5、super 在使用时，必须放在构造器第一行。(`super()` 只能在构造器中使用)。super和this都必须放在第一行，因此他们两个不能同时出现**

6、java 所有类都是 Object 类的子类, Object 是所有类的基类

7、父类构造器的调用不限于直接父类！将一直往上追溯直到 Object 类(顶级父类）

​	例如C继承自B，B继承自A，创建C对象会先创建A，然后创建B，然后创建C。

8、子类最多只能继承一个父类(指直接继承)，即 java 中是**单继承**机制。





#### 继承的本质

> Java创建子类对象时，会先调用父类构造方法，但是不会创建父类对象，没有为父类分配内存，只是调用了父类构造方法，代码中father和son输出的this是一样的，说明只创建了son对象



> 在调用子类属性时，按照查找关系来寻找
>
> （1）先看子类有没有这个属性，并看是否可以访问，可以就返回
>
> （2）如果子类没有这个属性，就看父类有没有这个属性，并看是否可以访问，可以就返回
>
> （3）父类没有就继续往上集寻找
>
> 注意：如果父类某个属性为private，但是爷类该属性为public，子类还是无法访问该属性，因为寻找到父类时发现无法访问，就结束了

```java
package com.extend_;

public class extendTheory {
    public static void main(String[] args) {
        Son son = new Son();
        System.out.println(son.name);
        System.out.println(son.age);
        System.out.println(son.hobby);
    }
}

class Grandpa{
    String name = "大头爷爷";
    String hobby = "旅游";
}

class Father extends Grandpa{
    String name = "大头爸爸";
    int age = 40;
    Father(){
        System.out.println("father: " + this);  // father: com.extend_.Son@1b6d3586
    }
}

class Son extends Father{
    String name = "大头儿子";
    Son(){
        System.out.println("Son: " + this); // Son: com.extend_.Son@1b6d3586
    }
}
```



**子类对象内存布局**

![image-20230209195855270](https://cdn.jsdelivr.net/gh/xrj123123/Images/image-20230209195855270.png)

> 1.先加载Object类，然后加载GrandPa，然后是Father，最后加载Son
>
> 2.先调用爷类构造器，然后是父类，然后调用子类构造器
>
> 3.创建完Son对象后，将其地址赋给son
>
> 4.堆内存中有三个String对象是因为，每个类都创建了一个String，而不是用的上一级的



**继承练习**

- 有了this就不会有默认的super()了

```java
public class A {
    A(){
      System.out.println("a");
    }
    A(String name){
      System.out.println("a name");
    }
}

class B extends A{
  B(){  // 这里有this就没有默认的super()了
    this("abc");
    System.out.println("b");
  }
  B(String name){
    System.out.println("b name");
  }
}

B b = new B();
/*
输出 a, b name, b
*/
```






### 7.5 super关键字

super代表父类的引用，用于访问父类的属性、方法、构造器

1、访问父类的属性，但是不能访问父类的private属性。`super.属性名;`

2、访问父类的方法，但是不能访问父类的private方法。`super.方法名(参数);`

3、访问父类的构造器。`super(参数列表);`只能放在构造器第一句



**注意：**

1、调用本类属性或方法时，使用this和默认访问效果是一样的，先从本类开始寻找，如果本类有就返回，没有就去父类寻找，直到Object类，如果寻找过程中找到了，但是无法访问，就报错。但是如果用super访问，会直接从父类开始找。

2、调用父类构造器的好处：分工明确，父类属性由父类构造器初始化，子类属性由子类初始化

3、当子类中有和父类的成员（属性或方法）重名时，为了访问父类的成员，必须通过super。如果没有重名，使用super、this、直接访问是一样的

4、super的访问不限于直接父类，如果爷类和本类有同名成员，也可以通过super去访问爷类成员。





### 7.6 方法重写

方法重写就是子类有一个方法和父类的某个方法的名称、参数、返回类型一样，就说子类方法重写父类方法



**注意：**

1、子类方法的形参列表、方法名必须和父类完全一样

2、子类方法的返回类型和父类方法返回类型一样，或者是父类方法返回类型的子类

3、子类方法不能缩小父类方法的访问权限。public > protected > 默认 > private



| 名称             | 发生范围 | 方法名 | 形参列表                     | 返回类型                               | 修饰符                   |
| ---------------- | -------- | ------ | ---------------------------- | -------------------------------------- | ------------------------ |
| 重载（overload） | 本类     | 相同   | 类型、个数、顺序至少一个不同 | 无要求                                 | 无要求                   |
| 重写（override） | 父子类   | 相同   | 相同                         | 子类与父类相同或者是父类返回类型的子类 | 子类不能缩小父类访问范围 |







### 7.7 多态

#### 7.7.1 多态

- 一个对象的编译类型和运行类型可以不一致
- 编译类型在定义对象时，就确定了，不能改变
- 运行类型可以变化
- 编译类型看定义时 = 号 的左边，运行类型看编译时 = 号 的右边



```java
package Poly;

public class Animal {
    public void cry(){
        System.out.println("动物在叫");
    }
}

package Poly;

public class Dog extends Animal{
    public void cry(){
        System.out.println("小狗汪汪叫");
    }
}

package Poly;

public class Cat extends Animal{
    public void cry(){
        System.out.println("小猫喵喵叫");
    }
}

package Poly;

public class Poly01 {
    public static void main(String[] args) {
        // Animal是编译类型，Dog是运行类型
        Animal animal = new Dog();
        animal.cry();
        // Animal是编译类型，Cat是运行类型
        animal = new Cat();
        animal.cry();
    }
}
```





**多态注意事项**

1、多态的前提是：两个对象（类）存在继承关系

2、多态的向上转型：

​		2.1、本质：父类的引用指向了子类的对象

​		2.2、语法：`父类类型 引用名 = new 子类类型();`

​		2.3、特点：编译类型看左边，运行类型看右边

​							可以调用父类所有成员（需遵守访问权限），不能调用子类特有成员，最终运行效果看子类实现

​							因为在编译阶段，能调用那些成员，由编译器决定，最终运行效果看子类的具体实现，从子类开始查找，子类没有就查找父类

3、多态向下转型：

​		3.1、语法：`子类类型 引用名 =  (子类类型)父类引用;`

​		3.2、只能强转父类的引用，不能强转父类的对象

​		3.3、要求父类的引用必须指向的是当前目标类型的对象

​		3.4、向下转型后，可以调用子类类型中的所有成员

​		**3.5、向下转型实际上是为对象又创建了一个引用名，不影响之前对象的引用**

```java
public class Poly {
    public static void main(String[] args) {
        Father father = new Son();  // father不能调用test2()
        Object obj = new Son(); // 也可以
        father.test1();
        Son son = (Son)father;
        son.test1();
        son.test2();
    }
}

class Father{
    int age = 40;
    public void test1(){
        System.out.println("father");
    }
}

class Son extends Father{
    public void test1(){
        System.out.println("son1");
    }
    public void test2(){
        System.out.println("son2");
    }
}
```

​	

**4、属性没有重写之说，属性的值看编译类型**

```java
package Poly;

public class Poly02 {
    public static void main(String[] args) {
        A a = new B();
        System.out.println(a.num);  // 10
        B b = (B)a;
        System.out.println(b.num);  // 20
    }
}

class A{
    public int num = 10;
}
class B extends A{
    public int num = 20;
}
```



5、`instanceOf`比较操作符，用于判断对象的**运行类型**是否为XX或者XX的子类

```java
package Poly;

public class Poly02 {
    public static void main(String[] args) {
        B b = new B();
        System.out.println(b instanceof A); // true
        System.out.println(b instanceof B); // true
        System.out.println(b instanceof Object); // true

        A a = new B();
        System.out.println(a instanceof A); // true
        System.out.println(a instanceof B); // true
    }
}

class A{
}
class B extends A{
}
```





**练习**

属性看编译类型，方法看运行类型

```java
package Poly;

public class Poly03 {
    public static void main(String[] args) {
        Sub s = new Sub();
        System.out.println(s.count);    // 20
        s.show();   // 20

        Base b = new Sub();
        System.out.println(b.count);    // 10
        b.show();   // 20
    }
}

class Base{
    int count = 10;
    public void show(){
        System.out.println(this.count);
    }
}

class Sub extends Base{
    int count = 20;
    public void show(){
        System.out.println(this.count);
    }
}
```





#### 7.7.2 Java的动态绑定机制

1、当调用对象方法的时候，该方法会和该对象的内存地址/运行类型绑定

2、当调用对象属性时，没有动态绑定机制，哪里声明，哪里使用

**方法看运行类型，属性看编译类型**



```java
package Poly.Dynamic;

public class DynamicBinding {
    public static void main(String[] args) {
        // 编译类型是A，运行类型是B
        A a = new B();
        // 40，将B类中sum方法注释掉，输出30
        System.out.println(a.sum());
        // 30， 将B中sum1方法注释掉，输出20
        System.out.println(a.sum1());
    }
}

class A {
    public int i = 10;

    public int sum() {
        return getI() + 10;
    }

    public int sum1() {
        return i + 10;
    }

    public int getI() {
        return i;
    }
}

class B extends A {
    public int i = 20;

//    public int sum() {
//        return getI() + 20;
//    }
//
//    public int sum1() {
//        return i + 10;
//    }

    public int getI() {
        return i;
    }
}
```





#### 7.7.3 多态的应用

**1、多态数组**

> 数组的定义类型为父类类型，里面保存的实际元素类型为子类类型

```java
package Poly.Polyarr;

public class Person {
    public String name;
    public int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String say(){
        return name + "\t" + age;
    }
}
package Poly.Polyarr;

public class Student extends Person{
    private double score;

    public Student(String name, int age, double score) {
        super(name, age);
        this.score = score;
    }

    public double getScore() {
        return score;
    }

    public void setScore(double score) {
        this.score = score;
    }

    @Override
    public String say() {
        return "学生 " + super.say()  + " score = " + score;
    }

    public void study(){
        System.out.println("学生 " + getName() + " 正在学习");
    }
}
package Poly.Polyarr;

public class Teacher extends Person{
    private double salary;

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public Teacher(String name, int age, double salary) {
        super(name, age);
        this.salary = salary;
    }

    @Override
    public String say() {
        return "老师 " + super.say() + " 薪水 = " + salary;
    }

    public void teach(){
        System.out.println("老师 " + getName() + "正在上课");
    }
}
package Poly.Polyarr;

public class PolyArray {
    public static void main(String[] args) {
        Person[] persons = new Person[5];
        persons[0] = new Person("x1", 20);
        persons[1] = new Student("x2", 18, 100);
        persons[2] = new Student("x3", 28, 80);
        persons[3] = new Teacher("x4", 40, 10000);
        persons[4] = new Teacher("x5", 42, 20000);

        for (int i = 0; i < 5; i++) {
            System.out.println(persons[i].say());
            if (persons[i] instanceof Student){
                Student s = (Student) persons[i];
                s.study();
            }else if (persons[i] instanceof Teacher){
                Teacher t = (Teacher) persons[i];
                t.teach();
            }else if (persons[i] instanceof Person){
            }else{
                System.out.println("输入有误");
            }
        }
    }
}
```



**2、多态参数**

> 方法定义的形参为父类类型，实参为子类类型





### 7.8 Object类讲解

#### 7.8.1 equals方法

`==`和`equals`对比

1、`==`：既可以判断基本类型，也可以判断引用类型

2、`==`：如果判断基本类型，判断的是值是否相等

3、`==`：如果判断引用类型，判断的是地址是否相等，即判断是否是一个对象

4、`equals`：是Object类中的方法，只能判断引用类型

5、`equals`：默认判断地址是否相等，子类中往往重写该方法，用于判断内容是否相等，比如`String`、`Integer`



**Object中默认的equals方法**

```java
public boolean equals(Object obj) {
	return (this == obj);
}
```





**自定义类重写equals方法**

```java
package Poly.ObjectTest;

public class Person {
    private String name;
    private int age;
    private char gender;

    public Person(String name, int age, char gender) {
        this.name = name;
        this.age = age;
        this.gender = gender;
    }

    public boolean equals(Object o) {
        // 如果比较的两个对象是同一个，返回true
        if (this == o)
            return true;
        // 比较类型
        if (o instanceof Person){
            Person p = (Person) o;
            return this.name.equals(p.name) && this.age == p.age && this.gender == p.gender;
        }
        return false;
    }
}

package Poly.ObjectTest;

public class Equals {
    public static void main(String[] args) {
        Person p1 = new Person("ton", 20, '男');
        Person p2 = new Person("ton", 20, '男');
        System.out.println(p1.equals(p2));
    }
}
```





#### 7.8.2 hashCode方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305251045211.png)

1、提高具有哈希结构容器的效率

2、两个引用，如果指向同一个对象，则哈希值一定相同

3、两个引用，如果指向的是不同对象，则哈希值是不同的

4、哈希值主要是根据地址号来的。但不能完全把哈希值等价于地址

5、hashCode要使用，也会重写





#### 7.8.3 toString方法

默认返回：`全类名+@+哈希值的十六进制`

1、子类往往重写toString方法，用于返回对象的属性信息

2、当直接输出一个对象时，toString方法会被默认调用



**Object 的 toString() 源码**

```java
// (1)getClass().getName() 类的全类名(包名+类名 )
// (2)Integer.toHexString(hashCode()) 将对象的 hashCode 值转成 16 进制字符串
public String toString() {
	return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```





#### 7.8.4 finalize方法

1、当对象被回收时，系统自动调用该对象的 finalize 方法。子类可以重写该方法，做一些释放资源的操作

2、 什么时候被回收：当某个对象没有任何引用时，则 jvm 就认为这个对象是一个垃圾对象，就会使用垃圾回收机制来 销毁该对象，在销毁该对象前，会先调用 finalize 方法。

3、 垃圾回收机制的调用，是由系统来决定(即有自己的 GC 算法), 也可以通过 System.gc() 主动触发垃圾回收机制。



```java
package com.Poly.HashCode_.Finalize;

public class Finalize_ {
    public static void main(String[] args) {
        Car car = new Car("xxx");
        car = null;
        System.gc();    // 不会阻塞程序
        System.out.println("结束");
    }
}

class Car{
    public String name;

    public Car(String name) {
        this.name = name;
    }

    @Override
    protected void finalize() throws Throwable {
        System.out.println("销毁了汽车" + name);
        System.out.println("释放了资源");
    }
}
```









## 8. 面向对象高级部分

### 8.1 类变量

```java
package com.level3.static_;

public class childPlay {
    public static void main(String[] args) {
        child x1 = new child("x1");
        x1.count++;
        child x2 = new child("x2");
        x2.count++;
        child x3 = new child("x3");
        x3.count++;
        System.out.println(child.count);
    }
}

class child {
    private String name;
    public static int count;

    public child(String name) {
        this.name = name;
    }
}
```



**类变量也叫静态变量**

- 静态变量被该类所有对象共享，任何一个该类的对象去访问他时，取到的都是相同的值，修改时也是修改的同一个变量。在类加载的时候就创建了
- 不管静态变量放在那里，都不影响其使用

> 在jdk8以前，静态变量在方法区的静态域中存放。在jdk8之后，静态变量在堆中存放，在类加载的时候，会在堆区生成一个class对象（反射），静态变量存放在class对象的尾部。



**类变量的定义**

`访问修饰符 static 数据类型 变量名;  `【推荐】

`static 访问修饰符 数据类型 变量名;`



**类变量的访问**

`类名.类变量名`

`对象名.类变量名` 【静态变量的访问修饰符权限和范围和普通属性是一样的】

```java
package com.level3.static_;

public class visitStatic {
    public static void main(String[] args) {

        // 类名.变量名
        // 说明：类变量是随着类的加载而创建，所以即使没有创建对象实例也可以访问
        System.out.println(A.name);
        // 通过对象名.类变量名
        A a = new A();
        System.out.println(a.name);
    }
}

class A {
    public static String name = "xrj";
}
```





**注意**

1、当我们需要让某个类所有对象都共享一个变量时，可以考虑使用类变量（静态变量）

2、类变量是该类的所有对象共享的，而实例变量是每个对象独有的

3、加上static称为类变量或静态变量，否则称为实例变量、普通变量、非静态变量

4、类变量可以通过 `类名.变量名`或者`对象名.变量名`访问。【前提是满足访问修饰符的权限和范围】

5、实例变量（非静态变量）不能通过`类名.变量名`访问

6、类变量在类加载时就创建了，因此不需要创建对象，只要类加载了，就可以访问

7、类变量的生命周期随着类的加载开始，随着类的消亡而销毁





### 8.2 类方法

**类方法也叫静态方法**



**定义**

`访问修饰符 static 数据类型 方法名(){}`【推荐】

`static 访问修饰符 数据类型 方法名(){}`



**类方法的调用**

`类名.方法名`

`对象名.方法名`



```java
package com.level3.static_;

public class staticMethod {
    public static void main(String[] args) {
        Stu tom = new Stu("tom");
        // tom.payFee(100);
        Stu.payFee(100);

        Stu jack = new Stu("jack");
        // jack.payFee(200);
        Stu.payFee(200);

        Stu.showFee();
        jack.showFee();
    }
}

class Stu{
    private String name;
    private static double fee = 0;

    public Stu(String name) {
        this.name = name;
    }
    public void say(){
        System.out.println(name);
        System.out.println(fee);
        payFee(100);
        showFee();
    }

    public static void payFee(double fee) {
        Stu.fee += fee;
    }

    public static void showFee(){
        System.out.println("总学费为：" + Stu.fee);
    }
}
```



1、当方法中不涉及任何和对象相关的成员，则可以将方法设计为静态方法，比如Math类，Arrays类

2、类方法和普通方法都是随着类的加载而加载，将结构信息存在方法区

3、类方法中没有this参数，普通方法中隐含着this

4、类方法可以通过类名调用，也可以通过对象名调用

5、类方法中不允许使用和对象有关的关键字，比如this和super，普通方法可以

6、类方法（静态方法）只能访问静态变量和静态方法

7、普通成员方法可以访问静态成员，也可以访问非静态成员

**8、static方法不能被重写，可以被继承**





### 8.3 深入理解main方法

`public static void main(String[] args){}`

1、main方法是Java虚拟机调用，Java虚拟机在调用main方法时和main方法不在同一个类，因此必须用public

2、Java虚拟机在执行main()方法时不必创建对象，所以必须是static

3、该方法接收String类型的数组参数，该数组中保存执行Java命令时传递给所运行的类的参数

```java
package com.level3.static_;

public class main_ {
    public static void main(String[] args) {
        for (int i = 0; i < args.length; i++) {
            System.out.println("第" + (i + 1) + "个参数 = " + args[i]);
        }
    }
}
```

`javac hello.java`

`java hello tom jack jerry`



**注意**

1、在main方法中，我们可以直接调用main方法所在类的静态方法或静态属性

2、但是，不能直接访问该类中的非静态成员，必须创建该类的一个实例对象后，才能通过这个对象去访问类中的非静态成员



IDEA中可以在`Edit Configurations`中设置参数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202305261424964.png)





### 8.4 代码块

> 代码块又叫初始化块，属于类的成员，类似于方法，将逻辑语句封装在方法体中，通过{}包围起来
>
> 但和方法不同，没有方法名，没有返回值，没有参数，只有方法体，而且不能通过对象或类显示调用，而是类加载时，或创建对象时隐式调用



**基本语法**

`[修饰符]{`

`	代码`

`};`

**注意**

1、修饰符可选，写的话只能写`static`

2、代码块分为两类，使用static修饰的叫静态代码块，没有static修饰的叫普通代码块/非静态代码块

3、逻辑语句可以为任何逻辑语句（输入、输出、方法调用、循环、判断等）

4、`;`可以写，也可以不写





- 代码块相当于另一种形式的构造器（对构造器的补充），可以做初始化操作
- 例如：如果多个构造器中有相同的语句，可以抽取到初始化块中，提高代码复用性

```java
package com.level3.static_.codeBlock;

public class movie {
    private String name;
    private double price;
    private String auther;

    // 构造方法中都有这三句话，造成冗余，可以将他们抽取出来
    // 创建对象时，先调用代码块，然后调用构造方法
    {
        System.out.println("屏幕打开...");
        System.out.println("播放广告...");
        System.out.println("广告结束...");
    }

    public movie(String name) {
//        System.out.println("屏幕打开...");
//        System.out.println("播放广告...");
//        System.out.println("广告结束...");
        System.out.println("调用了movie(String name)");
        this.name = name;
    }

    public movie(String name, double price) {
//        System.out.println("屏幕打开...");
//        System.out.println("播放广告...");
//        System.out.println("广告结束...");
        System.out.println("调用了movie(String name, double price)");
        this.name = name;
        this.price = price;
    }

    public movie(String name, double price, String auther) {
//        System.out.println("屏幕打开...");
//        System.out.println("播放广告...");
//        System.out.println("广告结束...");
        System.out.println("调用了movie(String name, double price, String auther)");
        this.name = name;
        this.price = price;
        this.auther = auther;
    }
}
```





**代码块细节**

1、static代码块也叫静态代码块，作用就是对类进行初始化，而且**它随着类的加载而执行**，并且只会执行一次。如果是普通代码块，每创建一个对象，就执行

**2、类什么时候被加载**

​	① 创建对象实例时（new）

​	② 创建子类对象实例，父类也会被加载

​	③ 使用类的静态成员时（静态属性，静态方法）

3、普通的代码块，在**创建对象实例**时，会被隐式的调用，被创建一次就调用一次。如果只是使用类的静态成员，普通代码块不会被执行

```java
package com.level3.static_.codeBlock;

public class codeBlockDetail1 {
    public static void main(String[] args) {
        // 1.创建对象实例时，类被加载
        // A a = new A();
        // 2.创建子类对象，父类被加载
        // A a2 = new A();
        // 3.使用类的静态成员，类被加载
        // System.out.println(Cat.n);

        // static代码块，类加载时执行，且只执行一次
        // D d1 = new D();
        // D d2 = new D();

        // 普通代码块创建对象时执行，创建一次执行一次
        // 如果只是调用静态成员，普通代码块不会执行
        System.out.println(D.n);
    }
}

class D {
    public static int n = 888;
    static {
        System.out.println("D的静态代码块被执行");
    }

    {
        System.out.println("D的普通代码块被执行");
    }
}

class Animal {
    static {
        System.out.println("Animal的静态代码块被执行");
    }
}

class Cat extends Animal{
    public static int n = 100;
    static {
        System.out.println("Cat的静态代码块被执行");
    }
}

class B {
    static {
        System.out.println("B的静态代码块被执行");
    }
}

class A extends B{
    static {
        System.out.println("A的静态代码块被执行");
    }
}
```



**4、创建一个对象时，在一个类的调用顺序**

① 调用静态代码块和静态属性初始化（静态代码块和静态属性初始化调用优先级一样，按他们定义顺序执行）

② 调用普通代码块和普通属性初始化（普通代码块和普通属性初始化调用优先级一样，按他们定义顺序执行）

③ 调用构造方法

```JAVA
package com.level3.static_.codeBlock;

public class codeBlockDetail2 {
    public static void main(String[] args) {
        AA a = new AA();// (1) AA 静态代码块 01 (2) getN1 被调用... (3)AA 普通代码块 01 (4)getN2 被调用... (5)AA() 构造器被调
    }
}

class AA {
    { //普通代码块
        System.out.println("AA 普通代码块 01");
    }

    private int n2 = getN2();//普通属性的初始化

    static { //静态代码块
        System.out.println("AA 静态代码块 01");
    }

    //静态属性的初始化
    private static int n1 = getN1();

    public static int getN1() {
        System.out.println("getN1 被调用...");
        return 100;
    }

    public int getN2() { //普通方法/非静态方法
        System.out.println("getN2 被调用...");
        return 200;
    }

    //无参构造器
    public AA() {
        System.out.println("AA() 构造器被调用");
    }
}
```



5、构造器前面其实隐藏了`super()`和调用普通代码块，静态代码块和静态属性初始化在类加载时就执行完毕

```java
class A {
    public A(){
    // 1.super();
    // 2.调用普通代码块
    System.out.println("OK");
	}
}
```

```java
package com.level3.static_.codeBlock;

public class codeBlockDetail3 {
    public static void main(String[] args) {
        new BBB();//(1)AAA 的普通代码块(2)AAA() 构造器被调用(3)BBB 的普通代码块(4)BBB() 构造器被调用
    }
}

class AAA { //父类 Object
    {
        System.out.println("AAA 的普通代码块");
    }

    public AAA() {
        //(1)super()
        //(2)调用本类的普通代码块
        System.out.println("AAA() 构造器被调用....");
    }
}

class BBB extends AAA {
    {
        System.out.println("BBB 的普通代码块...");
    }

    public BBB() {
        //(1)super()
        //(2)调用本类的普通代码块
        System.out.println("BBB() 构造器被调用....");
    }
}
```



6、创建一个子类对象时（继承关系），他们的静态代码块，静态属性初始化，普通代码块，普通属性初始化，构造方法调用顺序如下

​	① 父类的静态代码块和静态属性(优先级一样，按定义顺序执行)

​	② 子类的静态代码块和静态属性(优先级一样,按定义顺序执行)

​	③ 父类的普通代码块和普通属性初始化(优先级一样，按定义顺序执行)

​	④ 父类的构造方法

​	⑤ 子类的普通代码块和普通属性初始化(优先级一样，按定义顺序执行)

​	⑥ 子类构造方法

7、静态代码块只能调用静态成员（静态属性和静态方法），普通代码块可以调用任意成员

```java
package com.level3.static_.codeBlock;

public class codeBlockDetail4 {
    public static void main(String[] args) {
        //(1) 进行类的加载
        //1.1 先加载 父类 A02 1.2 再加载 B02
        //(2) 创建对象
        //2.1 从子类的构造器开始
        new B02();
        // new C02();
    }
}

class A02 { //父类
    private static int n1 = getVal01();

    static {
        System.out.println("A02 的一个静态代码块..");//(2)
    }

    {
        System.out.println("A02 的第一个普通代码块..");//(5)
    }

    public int n3 = getVal02();//普通属性的初始化

    public static int getVal01() {
        System.out.println("getVal01");//(1)
        return 10;
    }

    public int getVal02() {
        System.out.println("getVal02");//(6)
        return 10;
    }

    public A02() {//构造器
        //隐藏
        //super()
        //普通代码和普通属性的初始化......
        System.out.println("A02 的构造器");//(7)
    }
}

class B02 extends A02 {
    private static int n3 = getVal03();

    static {
        System.out.println("B02 的一个静态代码块..");//(4)
    }

    public int n5 = getVal04();

    {
        System.out.println("B02 的第一个普通代码块..");//(9)
    }

    public static int getVal03() {
        System.out.println("getVal03");//(3)
        return 10;
    }

    public int getVal04() {
        System.out.println("getVal04");//(8)
        return 10;
    }
}

class C02 {
    private int n1 = 100;
    private static int n2 = 200;

    private void m1() {
    }

    private static void m2() {
    }

    static {
        //静态代码块，只能调用静态成员
        //System.out.println(n1);错误
        System.out.println(n2);//ok
        //m1();//错误
        m2();
    }

    {
        //普通代码块，可以使用任意成员
        System.out.println(n1);
        System.out.println(n2);//ok
        m1();
        m2();
    }
}
```





### 8.5 单例设计模式

**单例：**

即单个的实例。单例设计模式就是采取一定方法在整个软件运行过程中，对某个类只能存在一个对象实例，并且该类只提供一个取得其实例的方法

单例模式有两种方式：1. 饿汉式	2. 懒汉式



**饿汉式**

1、构造器私有化。（防止new一个对象）

2、类的内部创建对象。（静态对象）

3、提供一个公共的静态方法，返回该对象

> 饿汉式：对象是静态的，所以类加载后，对象就创建了，但是此时可能还没有使用该对象。饿汉式可能造成资源浪费

```java
package com.level3.single_;

/**
 *  饿汉式
 */
public class single01 {
    public static void main(String[] args) {
        GirlFriend instance1 = GirlFriend.getInstance();
        System.out.println(instance1);
        GirlFriend instance2 = GirlFriend.getInstance();
        System.out.println(instance2);
        // System.out.println(GirlFriend.n); // 输出  构造器被调用，100
    }
}


class GirlFriend {
    private String name;
    public static int n = 100;

    // 为了能在静态方法中，返回gf对象，需要将其修饰为static
    private static GirlFriend gf = new GirlFriend("小邢");

    // [单例模式] 饿汉式
    // 1.将构造器私有化，这样就不能在外边new一个对象
    // 2.在类的内部创建一个对象
    // 3.提供一个static方法，返回gf对象，如果不加static还得需要创建对象才可以访问该方法
    private GirlFriend(String name) {
        System.out.println("构造器被调用");
        this.name = name;
    }
    public static GirlFriend getInstance() {
        return gf;
    }
}
```





**懒汉式**

1、构造器私有化

2、定义一个static属性对象

3、提供一个public的static方法，可以返回一个Cat对象

4、懒汉式，只有调用getInstance方法时，才会创建对象

```java
package com.level3.single_;

/**
 * 懒汉式
 */
public class single02 {
    public static void main(String[] args) {
        // System.out.println(Cat.n);  // 输出200，构造器没有被调用
        Cat cat1 = Cat.getInstance();
        System.out.println(cat1);
        Cat cat2 = Cat.getInstance();
        System.out.println(cat2);
    }
}

class Cat {
    private String name;
    public static int n = 200;

    private static Cat cat;

    // 1.构造器私有化
    // 2.定义一个static属性对象
    // 3.提供一个public的static方法，可以返回一个Cat对象
    // 4.懒汉式，只有调用getInstance方法时，才会创建对象
    private Cat (String name) {
        System.out.println("Cat(String name) 被调用");
        this.name = name;
    }

    public static Cat getInstance() {
        if (cat == null) {
            cat = new Cat("xyy");
        }
        return cat;
    }
}
```





**懒汉式与饿汉式的区别**

1、创建对象时机不同：饿汉式是在类加载后就创建了对象实例，懒汉式是在使用时创建

2、饿汉式不存在线程安全问题，懒汉式存在。（比如同时有三个线程进入了getInstance()方法，然后他们都会判断cat==null，就创建了三个对象）

3、饿汉式存在资源浪费情况。懒汉式不存在。

4、在javaSE标准类中，`java.lang.Runtime`就是单例模式





### 8.6 final关键字

**用到final的情况：**

1、当不希望类被继承时，可以用final修饰

2、当不希望父类某个方法被子类重写时，可以用final修饰

3、当不希望类的某个属性的值被修改，可以用final修饰

4、当不希望局部变量被修改，可以使用final修饰



**注意**

1、final修饰的属性又叫常量，一般用XX_XX_XX命名

2、final修饰的属性在定义时，必须赋初值，并且以后不能修改，赋值可以在以下位置之一：

​	① 定义时

​	② 构造器中

​	③ 代码块中

3、如果final修饰的属性是静态的，则初始化的位置只能是

​	① 定义时

​	② 在静态代码块中，不能再构造器中

4、final类不能被继承，但是可以实例化方法

5、如果类不是final类，但含有final方法，该方法不能被重写，但是该类可以被继承

6、final不能修饰构造方法

**7、final和static往往搭配使用，效率更高，不会导致类加载，底层编译器做了优化**

> static final只有修饰基本数据类型和String时，调用才不会加载类

```java
class Demo {
	public static final int i = 16;
	static {
		System.out.println("static代码块运行");
	}
}
// System.out.println(Demo.i); static代码块不会运行
```

8、包装类（Integer，Double，Float，Boolean，String）等都是final类







### 8.7 抽象类

> 当父类的某些方法，需要声明，但是又不确定如何实现时，可以将其声明为抽象方法，那么这个类就是抽象类



**抽象类介绍**

1、用`abstract`关键字来修饰一个类时，这个类就叫抽象类

2、用`abstract`关键字来修饰一个方法时，这个方法就叫抽象方法

```java
package com.level3.abstract_;

public class Abstract01 {
    public static void main(String[] args) {
        Cat cat = new Cat();
        cat.eat();
    }
}

abstract class Animal {
    private String name;
    private int age;
    public abstract void eat();
}

class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("猫吃鱼");
    }
}
```





**注意事项**

1、抽象类不能被实例化

2、抽象类不一定要包含`abstract`方法

3、一旦包含了`abstract`方法，则这个类必须声明为`abstract`

4、`abstract`只能修饰类和方法，不能修饰属性和其他的

5、抽象类可以有任意成员（抽象类还是类），比如：非抽象方法，构造器，静态属性等

6、抽象方法不能有主体，即不能实现

7、如果一个类继承了抽象类，则它必须实现抽象类的所有抽象方法，除非他自己也被声明为抽象类

8、抽象方法不能用`private、final、static`修饰，这些关键字和重写相违背



> 抽象类体现的就是一个模板模式的设计，抽象类作为多个子类的通用模板，子类在抽象类的基础上进行扩展、改造
>
> 当功能内部一部分实现是确定，一部分实现是不确定的，可以把不确定的部分暴露出去，让子类去实现





### 8.8 接口

> 接口就是	给出一些没有实现的方法，封装到一起，到某个类要使用的时候，再根据具体情况把这些方法写出来

```java
interface 接口名 {
	// 属性
	// 抽象方法
}
class 类名 implements 接口 {
	自己属性;
	自己方法;
	必须实现的接口的抽象方法
}
```

> 接口是更加抽象的抽象的类，抽象类里的方法可以有方法体，接口里的所有方法都没有方法体【JDK7】。JDK8之后接口类可以有**静态方法**，**默认方法**



- 实现接口的类不会继承接口的静态方法，因此子类创建对象不能调用接口的静态方法

```java
package com.level3.Interface_;

public class Interface01 {
    public static void main(String[] args) {
        A a = new A();
        System.out.println(A.n);
        System.out.println(a.n);
        a.hi();
        a.say();
        Inter01.cry();
        // a.cry(); // 接口中静态方法不能被继承
    }
}

interface Inter01 {
    // 写属性，默认为public static final int n = 10;
    int n = 10;
    // 写方法，默认为public abstract void hi();
    public void hi();

    // JKD8以后可以有默认实现方法
    default public void say() {
        System.out.println("你好");
    }

    // JDK8以后可以用静态方法实现
    public static void cry() {
        System.out.println("cry...");
    }

}

class A implements Inter01 {
    @Override
    public void hi() {
        System.out.println("hi");
    }
}
```





**注意事项**

1、接口不能被实例化

**2、接口中所有的方法都是public方法，接口中的抽象方法不需要用abstract修饰**

3、一个普通类实现接口，就必须将该接口的所有方法都实现

4、抽象类实现接口，可以不用实现接口的方法

**5、一个类可以实现多个接口。但是当多个接口有相同的抽象方法时，该类只会实现一个。如果多个接口有相同的属性，就不能通过创建对象去访问属性，只能通过`接口名.属性`来访问。如果多个接口有相同的`default`方法，那么类继承时必须重写该方法**

```java
package com.level3.Interface_;

public class Interface01 {
    public static void main(String[] args) {
        B b = new B();
        System.out.println(Inter01.n);
        System.out.println(Inter02.n);
        // System.out.println(b.n); 报错
        b.hi();
        b.say();
    }
}

interface Inter01 {
    // 写属性
    int n = 10;
    // 写方法
    public void hi();

    // JKD8以后可以有默认实现方法
    default public void say() {
        System.out.println("你好");
    }
}

interface Inter02 {
    int n = 20;
    public void hi();

    default public void say() {
        System.out.println("inter02");
    }
}

class A implements Inter01 {
    @Override
    public void hi() {
        System.out.println("hi");
    }
}

class B implements Inter01, Inter02 {
    @Override
    public void hi() {
        System.out.println("aaa");
    }

    @Override
    public void say() {
        Inter02.super.say();
    }
}
```



6、接口中属性，默认是`public static final`修饰

7、接口中属性的访问形式`接口名.属性名`

8、接口不能继承其他的类，但是可以继承其他接口

9、接口的修饰符只能是public和默认





### 8.9 接口的多态特性

**多态参数**

- 接口类型的变量可以指向实现了该接口的类的对象

```java
package com.level3.Interface_;

public class Interface02 {
    public static void main(String[] args) {
        IA a = new Monster();
        a = new Car();
        System.out.println(a instanceof IA);   // true
    }
}

interface IA {
}
class Monster implements IA {}
class Car implements IA {}
```



**多态数组**

```java
package com.level3.Interface_;

public class InterfacePolyArr {
    public static void main(String[] args) {
        // 多态数组
        USB[] usbs = new USB[2];
        usbs[0] = new Phone();
        usbs[1] = new Camera();

        for (int i = 0; i < usbs.length; i++) {
            usbs[i].work(); // 动态绑定
            if (usbs[i] instanceof Phone) { // 判断运行类型是不是Phone
                ((Phone) usbs[i]).call();
            }
        }
    }
}

interface USB {
    void work();
}

class Phone implements USB {
    @Override
    public void work() {
        System.out.println("手机工作");
    }

    public void call() {
        System.out.println("手机打电话");
    }
}

class Camera implements USB {
    @Override
    public void work() {
        System.out.println("相机工作");
    }
}
```



**多态传递**

```java
package com.level3.Interface_;

public class InterfacePolyPass {
    public static void main(String[] args) {
        ID id = new teacher();
        // 如果接口ID继承了IC，teacher类实现了ID接口，相当于也实现了IC接口
        IC ic = new teacher();
    }
}

interface IC {
    void hi();
}
interface ID extends IC{}

class teacher implements ID{
    @Override
    public void hi() {
    }
};
```





### 8.10 内部类（重点）

> 如果定义类在局部位置(方法中/代码块) :
>
> ​			(1) 局部内部类 (2) 匿名内部类 
>
> 定义在成员位置：
>
> ​			(1) 成员内部类 (2) 静态内部类



一个类的内部又嵌套了另一个类结构。被嵌套的类称为内部类，嵌套其他类的类称为外部类。类的五大成员（属性、方法，构造器、代码块、内部类）。

内部类的最大特点就是可以直接访问私有属性，并且可以体现类与类之间的包含关系



**基本语法**

```java
class Outer {	// 外部类
	class Inner {	// 内部类
	}
}
class Other {	// 外部其他类
}
```

```java
package com.level3.InnerClass;

public class InterClass01 {
}

class Outer {   // 外部类
    private int n = 10;
    private void m1() {
        System.out.println("Outer m1");
    }

    public Outer(int n) {
        this.n = n;
    }

    {
        System.out.println("代码块");
    }

    class Inner {   // 内部类

    }
}
```



**内部类的分类**

- 定义在外部类局部位置上（比如方法内）

  （1）局部内部类（有类名）

  **（2）匿名内部类（没有类名，重点）**

- 定义在外部类的成员位置上

  （1）成员内部类（没用static修饰）

  （2）静态内部类（用static修饰）





#### 8.10.1 局部内部类

> 局部内部类是定义在外部类的局部位置，比如方法中，并且有类名



**局部内部类的特点**

1、可以直接访问外部类的所有成员，包括私有的

2、不能添加访问修饰符，可以用final修饰

3、作用域：仅仅在定义它的方法或代码块中

4、局部内部类访问外部类的成员：直接访问

5、外部类访问局部类的成员，访问方式：创建对象，再访问（必须在作用域中）

6、外部其他类不能访问局部内部类（因为局部内部类的地位就是一个局部变量）

7、如果外部类和局部内部类的成员重名时，默认遵循就近原则，如果想访问外部类的成员，可以使用（`外部类名.this.成员`）访问

```java
package com.level3.InnerClass;

public class LocalInnerClass {
    public static void main(String[] args) {
        Outer02 outer02 = new Outer02();
        outer02.m1();
        System.out.println("Outer02的Hashcode = " + outer02.hashCode());
    }
}

class Outer02 {
    private int n = 100;
    private void m2() {
        System.out.println("Outer02 m2");
    }
    public void m1() {
         final class Inner02 {
            private int n = 500;
            public void f1() {
                // Outer02.this本质就是外部类的对象，即哪个类调用了m1，Outer02.this就是哪个类
                System.out.println("n = " + n + "  外部类的n = " + Outer02.this.n);
                System.out.println("Outer02.this的Hashcode = " + Outer02.this.hashCode());
                m2();
            }
        }
        Inner02 inner02 = new Inner02();
        inner02.f1();
    }
}
```





#### 8.10.2 匿名内部类（重要！！！）

> 匿名内部类是定义在外部类的局部位置，比如方法中，并且没有类名（表面没有类名，但是JDK底层分配的有类名，可以通过getClass()方法查看）

- 本质还是类
- 是内部类
- 该类没有名字
- 是一个对象



**匿名内部类语法**

```java
new 类或接口(参数列表) {
	类体
};
```



```java
package com.level3.InnerClass;

public class AnonymousInnerClass {
    public static void main(String[] args) {
        Outer04 outer04 = new Outer04();
        outer04.method();
    }
}

class Outer04 {
    private int n = 10;
    public void method() {
        // 基于接口的内部类
        // 1.需求：想使用IA接口，并创建对象
        // 2.传统方式，是写一个类，实现该接口，创建对象
        // 3.但是如果需求是tiger/Dog对象只使用一次，后面不在使用，像这样定义一个类比较繁琐
//        IA tiger = new tiger();
//        tiger.cry();
//        IA dog = new Dog();
//        dog.cry();
        // 4.使用匿名内部类简化开发
        // 5.tiger的编译类型：IA
        // 6.tiger的运行类型：就是匿名内部类 Outer04$1
        /*
            底层会分配类名 Outer04$1
            class Outer04$1 implements IA {
                @Override
                public void cry() {
                    System.out.println("老虎叫...");
                }
            }
         */
        // 7.JDK在底层创建匿名内部类Outer04$1，立即就创建了Outer04$1实例，并把地址返回给tiger
        // 8.匿名内部类只能使用一次,就不能使用（Outer04$1使用后就被销毁）
        IA tiger = new IA() {
            @Override
            public void cry() {
                System.out.println("老虎叫...");
            }
        };
        tiger.cry();
        tiger.cry();
        IA dog = new IA() {
            @Override
            public void cry() {
                System.out.println("小狗叫...");
            }
        };
        dog.cry();
        System.out.println("tiger的运行类型 = " + tiger.getClass()); // Outer04$1
        System.out.println("dog的运行类型 = " + dog.getClass()); // Outer04$2

        System.out.println("=================");
        // 基于类的匿名内部类
        // 分析
        // 1.father的编译类型：Father
        // 2.father的运行类型：Outer04$3
        // 3.底层会创建匿名内部类
        /*
            class Outer04$3 extends Father {
                @Override
                public void test() {
                    System.out.println("father匿名内部类");
                }
            }
        */
        // 4.同时直接返回匿名内部类Outer04$3的对象
        // 5.注意("xrj")参数列表会直接传递给Father的构造器
        Father father = new Father("xrj"){
            @Override
            public void test() {
                System.out.println("匿名内部类重写了test方法");
            }
        };
        father.test();
        System.out.println("father的运行类型 = " + father.getClass());   // Outer04$3


        System.out.println("====================");
        // 基于抽象类的匿名内部类,Animal是抽象类，因此抽象方法必须实现
        Animal cat = new Animal() {
            @Override
            void eat() {
                System.out.println("小猫吃鱼...");
            }
        };
        cat.eat();
        System.out.println("cat的运行类型 = " + cat.getClass()); // Outer04$4
    }
}

interface IA {
    public void cry();
}

//class tiger implements IA {
//    @Override
//    public void cry() {
//        System.out.println("老虎叫...");
//    }
//}
//class Dog implements IA {
//    @Override
//    public void cry() {
//        System.out.println("小狗汪汪叫...");
//    }
//}

class Father {
    public Father(String name) {
        System.out.println("接收到的name = " + name);
    }
    public void test() {}
}

abstract class Animal { //抽象类
    abstract void eat();
}
```



**注意事项**

1、匿名内部类既是一个类的定义，同时它本身也是一个对象，因此从语法上看，它既有定义类的特征，也有创建对象的特征。

**匿名内部类使用的两种方式：**

第一种方法，用一个父类的变量来接收，这样可以调用父类Person中的方法，但是无法调用自己特有方法，因为此时编译类型是Person，运行类型是匿名内部类，如果要调用自己特有方法，必须向下转型，很显然，匿名内部类执行一次就销毁了，无法得到类名，也就无法向下转型

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303021510704.png" style="zoom:75%;" />

第二种方法，匿名内部类本身就是一个对象，不使用变量去接收它，这时就可以调用自身特有方法

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303021510247.png" style="zoom:75%;" />

2、匿名内部可以直接访问外部类的所有成员，包括私有的

3、不能添加访问修饰符，因为它的地位就是一个局部变量

4、作用域：仅仅在定义它的方法和代码块中

5、外部其他类 不能访问 匿名内部类

6、如果外部类和匿名内部类的成员重名时，匿名内部类访问遵循就近原则，如下想访问外部类的成员，可以使用`外部类.this.成员`

```java
package com.level3.InnerClass;

public class AnonymousInnerClassDetail {
    public static void main(String[] args) {
        Outer05 outer05 = new Outer05();
        outer05.f1();
    }
}

class Outer05 {
    private int n = 10;

    public void f1() {
        // 创建匿名内部类
        // 通过对象接收
        Person p = new Person() {
            private int n = 100;
            @Override
            public void hi() {
                System.out.println("匿名内部类重写了hi");
                System.out.println("内部类n = " + n + " 外部类n = " + Outer05.this.n);
            }
        };
        p.hi();
        p.ok("xrj");


        System.out.println("==============");
        // 匿名内部类本身就是一个对象，可以不接收，直接调用其方法
        new Person(){
            public void test(){
                System.out.println("匿名内部类特有方法test");
            }
        }.test();
    }
}

class Person {
    public void hi() {
        System.out.println("Person hi");
    }
    public void ok(String str) {
        System.out.println("Person ok() + " + str);
    }
}
```





**匿名内部类最佳实践**

当做实参直接传递

```java
package com.level3.InnerClass;

public class AnonymousInnerClassExercise01 {
    public static void main(String[] args) {
        f1(new IB() {
            @Override
            public void show() {
                System.out.println("我是xrj");
            }
        });
    }
    public static void f1(IB ib) {
        ib.show();
    }
}

interface IB {
    void show();
}
```





> 需求：
>
> 1、有一个铃声接口Bell，里边有个ring方法
>
> 2、有一个手机类CellPhone，具有闹钟功能alarmClock，参数是Bell类型
>
> 3、测试手机类的闹钟功能，通过匿名内部类作为参数，打印：懒猪起床了
>
> 4、在传入另一个匿名内部类，打印“小伙伴上课了

```java
package com.level3.InnerClass;

public class AnonymousInnerClassExercise02 {
    public static void main(String[] args) {
        CellPhone cellPhone = new CellPhone();
        cellPhone.alarmClock(new Bell() {
            @Override
            public void ring() {
                System.out.println("懒猪起床了");
            }
        });

        cellPhone.alarmClock(new Bell() {
            @Override
            public void ring() {
                System.out.println("小伙伴上课了");
            }
        });
    }
}

interface Bell {
    void ring();
}

class CellPhone {
    public void alarmClock(Bell bell) {
        bell.ring();
    }
}
```





#### 8.10.3 成员内部类

> 成员内部类是定义在外部类的成员位置，并且没有static修饰

1、可以直接访问外部类的所有成员，包括私有的

2、可以添加任意访问修饰符（public、protected、默认、private），因为它的地位就是一个成员

3、作用域和外部类的其他成员一样，为整个类体

4、成员内部类 访问 外部类成员：直接访问

5、外部类 访问 成员内部类：创建对象，再访问

6、外部其他类 访问 成员内部类：三种方式

7、如果外部类和内部类的成员重名时，内部类访问的话遵循就近原则，如果想访问外部类成员，可以使用`外部类.this.成员`

```java
package com.level3.InnerClass;

public class MemberInnerClass01 {
    public static void main(String[] args) {
        Outer01 outer01 = new Outer01();
        outer01.show();

        // 外部其它类，使用成员内部类的三种方式
        // 第一种方式
        Outer01.Inner inner = outer01.new Inner();
        inner.say();

        // 第二种方式 外部类中写一个方法可以返回Inner对象实例
        Outer01.Inner inner1 = outer01.getInstance();
        inner1.say();

        // 第三种方式
        Outer01.Inner inner2 = new Outer01().new Inner();
        inner2.say();
    }
}

class Outer01 {
    private int n1 = 10;
    public String name = "xrj";
    class Inner {
        private int n1 =100;
        private int n2 = 20;
        public void say() {
            System.out.println("成员内部类的say方法 " + " 内部类n1 = " + n1 + "  name = " + name);
            System.out.println("外部类n1 = " + Outer01.this.n1);
        }
    }
    public void show() {
        Inner inner = new Inner();
        inner.say();
        System.out.println(inner.n2);
    }

    public Inner getInstance() {
        return new Inner();
    }
}
```





#### 8.10.4 静态内部类

> 静态内部类是定义在外部类的成员位置，并且有static修饰

1、可以直接访问外部类的所有静态成员，包含私有的，但是不能访问非静态成员

2、可以添加任意访问修饰符（public、protected、默认、private），因为它的地位就是一个成员

3、作用域：为整个类体

4、静态内部类 访问 外部类：直接访问所有静态属性

5、外部类 访问 静态内部类：创建对象，再访问

6、外部其他类 访问 静态内部类：两种方式

7、如果外部类和静态内部类成员重名时，静态内部类访问时，遵循就近原则，如果想访问外部类成员，使用`外部类名.成员`

```java
package com.level3.InnerClass;

public class StaticInnerClass {
    public static void main(String[] args) {
        Outer03 outer03 = new Outer03();
        outer03.m1();

        // 外部其他类 使用静态内部类
        // 方式一
        Outer03.Inner inner = new Outer03.Inner();
        inner.say();
        // 方式二 编写一个方法，返回对象实例
        Outer03.Inner instance = outer03.getInstance();
        instance.say();

        Outer03.Inner instance_ = Outer03.getInstance_();
        instance_.say();
    }
}

class Outer03 {
    private int n1 = 10;
    private static String name = "xrj";
    private static void cry(){
        System.out.println("Outer03 cry");
    }

    static class Inner {
        private String name = "xyy";
        public void say() {
            System.out.println(name + " 外部类name = " + Outer03.name);
            cry();
        }
    }
    public void m1() {
        Inner inner = new Inner();
        inner.say();
    }

    public Inner getInstance() {
        return new Inner();
    }

    public static Inner getInstance_() {
        return new Inner();
    }
}
```









## 9. 枚举和注解

> 先看一个需求：要求创建季节对象：有name和desc属性。传统方法就是创建一个Season类，然后创建四个季节的对象，但是此时还可以继续创建Season对象，尽管它不是季节，而且对于季节对象的属性，可以通过setXXX方法来修改。为了解决这个问题，因此出现了枚举类。



- 枚举是一组常量的组合
- 枚举有两种实现方式：1. 自定义类实现枚举     2. 使用enum关键字实现枚举



### 9.1 自定义类实现枚举

- 构造器私有化，防止new
- 不需要提供setXxx方法，枚举类对象值通常为只读
- 在类的内部创建对象
- 对枚举对象/属性使用final + static 修饰，实现底层优化

```java
package com.level4;

public class Enum01 {
    public static void main(String[] args) {
        System.out.println(Season.SPRING);
    }
}

class Season {
    private String name;
    private String desc;

    public final static Season SPRING = new Season("春天", "温暖");
    public final static Season SUMMER = new Season("夏天", "炎热");
    public final static Season AUTUMN = new Season("秋天", "凉爽");
    public final static Season WINTER = new Season("冬天", "寒冷");
    // 1.将构造器私有化，防止直接new
    // 2.去掉setXXX方法，防止属性被修改
    // 3.在Season内部，直接创建固定的对象
    // 4.优化，可以加上final修饰，虽然final static不会导致类加载，那只是针对普通数据类型和String，这里会new对象，所以类会加载
    // 但是可以防止季节变量更改它的引用
    private Season(String name, String desc) {
        this.name = name;
        this.desc = desc;
    }

    public void getName(String name) {
        this.name = name;
    }

    public String getDesc() {
        return desc;
    }

    @Override
    public String toString() {
        return "Season{" +
                "name='" + name + '\'' +
                ", desc='" + desc + '\'' +
                '}';
    }
}
```







### 9.2 enum关键字实现枚举

```java
package com.level4;

public class Enum02 {
    public static void main(String[] args) {
        System.out.println(Season2.SPRING);
        System.out.println(Season2.SUMMER);
    }
}

enum Season2 {
//    public final static Season SPRING = new Season2("春天", "温暖");
//    public final static Season SUMMER = new Season2("夏天", "炎热");
//    public final static Season AUTUMN = new Season2("秋天", "凉爽");
//    public final static Season WINTER = new Season2("冬天", "寒冷");

    // 如果使用了 enum 来实现枚举类
    // 1. 使用关键字 enum 替代 class
    // 2. public static final Season SPRING = new Season("春天", "温暖") 直接使用
    // 3. 如果有多个常量(对象)， 使用逗号间隔
    // 4. 如果使用 enum 来实现枚举，要求将定义常量对象，写在前面
    // 5. 如果我们使用的是无参构造器，创建常量对象，则可以省略 ()
    // 6. enum枚举类构造方法默认是private的
    SPRING("春天", "温暖"), SUMMER("夏天", "炎热"),
    AUTUMN("秋天", "凉爽"), WINTER("冬天", "寒冷");
    private String name;
    private String desc;

    Season2(String name, String desc) {
        this.name = name;
        this.desc = desc;
    }

    public String getDesc() {
        return desc;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "Season{" +
                "name='" + name + '\'' +
                ", desc='" + desc + '\'' +
                '}';
    }
}
```



**使用了 enum 来实现枚举类**

1、使用关键字 enum 替代 class。使用 enum 关键字开发一个枚举类时，默认会继承 Enum 类, 而且是final修饰的。使用javap命令反编译即可查看

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303041014497.png)

2、`public static final Season SPRING = new Season("春天", "温暖")`简化为`SPRING("春天", "温暖")`

3、如果我们使用的是无参构造器，创建常量对象，则可以省略 ()

4、如果有多个常量(对象)， 使用逗号间隔

5、枚举对象必须写在首行

**6、enum枚举类构造方法默认是private的**





### 9.3 enum类常用方法

> 使用关键字enum时，会隐式继承Enum类，可以使用Enum类相关方法



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303041053329.png)

1、toString: Enum 类已经重写过了，返回的是当前对象名, 子类可以重写该方法，用于返回对象的属性信息 

2、name：返回当前对象名（常量名），子类中不能重写 

3、ordinal：返回当前对象的位置号，默认从 0 开始

4、values：返回当前枚举类中所有的常量 

5、valueOf：将字符串转换成枚举对象，要求字符串必须 为已有的常量名，否则报异常！

6、compareTo：比较两个枚举常量，比较的就是编号

```java
package com.level4;

public class EnumMethod {
    public static void main(String[] args) {
        Season2 season1 = Season2.AUTUMN;
        Season2 season2 = Season2.SUMMER;
        System.out.println(season1); // Season{name='秋天', desc='凉爽'}

        System.out.println(season1.name());  // AUTUMN

        System.out.println(season1.ordinal());   // 2
        System.out.println(season2.ordinal());  // 1

        Season2[] values = Season2.values();
        for (Season2 season : values) {
            System.out.println(season);
        }

        Season2 season3 = Season2.valueOf("WINTER");
        System.out.println(season3);    // Season{name='冬天', desc='寒冷'}

        // 编号相减  1 - 3
        System.out.println(season1.compareTo(season3));
    }
}
```





### 9.4 实现接口

1、使用enum关键字后，就不能再继承其他的类了。因为 enum 会隐式继承 Enum，而 Java 是单继承

2、枚举类和普通类一样，可以实现接口

```java
package com.level4;

/**
 * @author XRJ
 * @version 1.0
 * @Data 2023/3/4
 */
public class EnumDetails {
    public static void main(String[] args) {
        Music.SING.playing();
    }
}

class A{}

// 使用 enum 关键字后，就不能再继承其它类了，因为enum会隐式继承Enum，而Java是单继承机制
//enum Music extens A{
//}

interface Play{
    void playing();
}

enum Music implements Play {
    SING;

    @Override
    public void playing() {
        System.out.println("播放音乐");
    }
}
```





### 9.5 注解

1、 注解(Annotation)也被称为元数据(Metadata)，用于修饰解释 包、类、方法、属性、构造器、局部变量等数据信息。

2、和注释一样，注解不影响程序逻辑，但注解可以被编译或运行，相当于嵌入在代码中的补充信息。 

3、在 JavaSE 中，注解的使用目的比较简单，例如标记过时的功能，忽略警告等。在 JavaEE 中注解占据了更重要的角 色，例如用来配置应用程序的任何切面，代替 java EE 旧版中所遗留的繁冗代码和 XML 配置等。



使用 Annotation 时要在其前面增加 @ 符号, 并把该 Annotation 当成一个修饰符使用。用于修饰它支持的程序元素 

**三个基本的 Annotation:**

- @Override: 限定某个方法，是重写父类方法, 该注解只能用于方法
- @Deprecated: 用于表示某个程序元素(类, 方法等)已过时 
-  @SuppressWarnings: 抑制编译器警告



#### 9.5.1 @Override

> @Override: 限定某个方法，是重写父类方法, 该注解只能用于方法



**使用说明：**

1、@Override表示指定重写父类的方法（从编译层面验证），如果父类没有该方法，就报错

2、如果不写@Override，子类重写后，仍构成重写

3、@Override只能修饰方法

4、@Override的源码为。**@interface是注解类，JDK5.0后加入的**

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```





#### 9.5.2 @Deprecated

> @Deprecated表示某个程序元素（类、方法等）已过时

```java
package com.level4.Annotation;

public class Deprecated_ {
    public static void main(String[] args) {
        A a = new A();
        System.out.println(a.n);
        a.hi();
    }
}

@Deprecated
class A{
    @Deprecated
    public int n = 10;
    @Deprecated
    public void hi(){};
}
```



**@Deprecated的说明**

1、用于表示某个成员元素（类，方法等）已过时。（但仍可以使用）

2、可以修饰方法、类、字段、包、参数等

3、@Deprecated可以用作新旧版本的兼容和过度

4、@Deprecated源码

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})
public @interface Deprecated {
}
```





#### 9.5.3 @SuppressWarnings

> @SuppressWarnings：抑制编译器警告
>
> 当我们不想看到编译器中警告时，可以使用这个注解

```java
package com.level4.Annotation;

import java.util.ArrayList;
import java.util.List;

public class SuppressWarnings_ {
    @SuppressWarnings("all")
    public static void main(String[] args) {
        List list = new ArrayList();
        list.add("jack");
        list.add("tom");
        list.add("mary");
        int i;
        System.out.println(list.get(1));
    }

    public void f1() {
        @SuppressWarnings({"rawtypes"})
        List list = new ArrayList();
        list.add("jack");
        list.add("tom");
        list.add("mary");
        @SuppressWarnings({"unused"})
        int i;
        System.out.println(list.get(1));
    }
}
```



1、在`{""}`中，写入希望抑制的警告信息

2、SuppressWarnings 作用范围是和放置的位置相关

3、@SuppressWarnings源码

```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    String[] value();
}
```

该注解有数组`String[] value();`，表示使用该注解时要传入一个字符串数组

- all 是忽略所有警告

- unchecked 是忽略没有检测的警告

- rawtypes 是忽略没有指定泛型的警告
- unused 是忽略没有使用某个变量的警告





#### 9.5.4 元注解

> 元注解是用来修饰注解的注解



1、Retention 			//指定注解的作用范围，三种 SOURCE,CLASS,RUNTIME 

2、Target 				// 指定注解可以在哪些地方使用 

3、Documented	 //指定该注解是否会在 javadoc 体现

4、Inherited 			//子类会继承父类注解



##### **9.5.4.1 @Retention** 	

> 只能用于修饰一个 Annotation 定义, 用于指定该 Annotation 可以保留多长时间, @Rentention 包含一个 RetentionPolicy 类型的成员变量, 使用 @Rentention 时必须为该 value 成员变量指定值:



**.java文件经过javac编译，生成.class文件，.class文件放在JVM上运行**

- `RetentionPolicy.SOURCE`: 编译器使用后，直接丢弃这种策略的注释

- `RetentionPolicy.CLASS`: 编译器将把注解记录在 class 文件中. 当运行 Java 程序时, JVM 不会保留注解。 这是默认 值

- `RetentionPolicy.RUNTIME`:编译器将把注解记录在 class 文件中. 当运行 Java 程序时, JVM 会保留注解. 程序可以 通过反射获取该注解

  

例如`@Override`的作用域在`SOURCE`，在编译器编译时生效，不会写入到.class文件中，也不会在运行时（runtime）生效





##### **9.5.4.2 @Target**

> 用于指定被修饰的注解能用于修饰哪些程序元素

**@Target源码**

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```



##### **9.5.4.3 @Documented**

> @Documented用于指定被该元注解修饰的注解在被javadoc工具提取生成文档时，是否可以看到该注解





##### **9.5.4.4 @Inherited **

> 被它修饰的注解具有继承性。如果某个类使用了被@Inherited 修饰的注解，则该类的子类也有该注解







## 10. 异常

### 10.1 异常介绍

> Java中，将程序执行中发生的不正常情况称为异常。（语法错误和逻辑错误不是异常）



```java
package com.level5.Exception;

public class Exception01 {
    public static void main(String[] args) {
        int num1 = 10;
        int num2 = 0;

        // int res = num1 / num2;
        // 1.当执行到 num1 / num2 因为 num2 = 0, 程序就会出现(抛出)异常 ArithmeticException
        // 2.当抛出异常后，程序就退出，崩溃了 , 下面的代码就不在执行
        // 3.但是这样并不好，不能因为一个小错误导致整个程序崩溃
        // 4.因此有了异常处理机制。当认为一段代码可能出现异常/问题，可以使用 try-catch 异常处理机制来解决
        // 5.如果进行异常处理，那么即使出现了异常，程序可以继续执行
        try {
            int res = num1 / num2;
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("程序结束");
    }
}
```





**执行过程中所发生的异常事件可以分为两大类：**

1、**Error**（错误）。Java虚拟机无法解决的严重错误。如：JVM系统内部错误、资源耗尽等严重情况。比如：`StackOverflowError`【栈溢出】和OOM（out of memory），Error是严重错误，程序会崩溃

2、**Exception**。其他因编程错误或偶然的外在因素导致的一般性问题，可以使用针对性的代码进行处理。例如：空指针访问，试图读取不存在的文件。`Exception`分为两大类：**运行时异常**【程序运行时发生的异常】和**编译时异常**【编程时，编译器检查出的异常】





**异常体系图**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303061105025.png)





**总结**

1、异常分为两大类，运行时异常和编译时异常

2、运行时异常，编译器检查不出来。一般是编程时的逻辑错误，是程序员应该避免出现的异常。`java.lang.RuntimeException`类及它的子类都是运行时异常

3、对于运行时异常，可以不做处理，因为这类异常很普遍，若全处理会对程序可读性和运行效率产生影响

4、编译时异常，是编译器要求必须处理的异常





### 10.2 常见的运行时异常

- `NullPointerException `		空指针异常
- `ArithmeticException	 `          数学运算异常
- `ArrayIndexOutOfBoundsException `     数组下标越界异常
- `ClassCastException `            类型转换异常
- `NumberFormatException `      数字格式不正确异常





**1、NullPointerException 空指针异常**

> 当应用程序试图在需要对象的地方使用 null 时，抛出该异常



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303061134259.png)



```java
package com.level5.Exception;

public class ExceptionNullPoint {
    public static void main(String[] args) {
        String name = null;
        System.out.println(name.length());
    }
}
```





**2、ArithmeticException 数学运算异常** 

> 当出现异常的运算条件时，抛出此异常。例如，一个整数“除以零”时，抛出此类的一个实例



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303061141847.png)





**3、ArrayIndexOutOfBoundsException 数组下标越界**

> 用非法索引访问数组时抛出的异常。如果索引为负或大于等于数组大小，则该索引为非法索引



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303061142148.png)



```java
package com.level5.Exception;

public class ExceptionArrayIndexOutOfBound {
    public static void main(String[] args) {
        int[] arr = {1,2,4};
        for (int i = 0; i <= arr.length; i++) {
            System.out.println(arr[i]);
        }
    }
}
```





**4、ClassCastException 类型转换异常**

> 当试图将对象强制转换为不是实例的子类时，抛出该异常



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303061144236.png)



```java
package com.level5.Exception;

public class ExceptionClassCast {
    public static void main(String[] args) {
        A b = new B();
        B b2 = (B)b;
        C b3 = (C)b;
    }
}

class A {}
class B extends A {}
class C extends A {}
```





**5、NumberFormatException 数字格式不正确异常**

> 当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出该异常 



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303061146231.png)



```java
package com.level5.Exception;

public class ExceptionNumberFormat {
    public static void main(String[] args) {
        String name = "xrj";
        int num = Integer.parseInt(name);
    }
}
```







### 10.3 编译异常

> 编译异常通常是指在编译期间，就必须处理的异常，否则代码不能通过编译



**常见的编译异常**

`SQLException`							 操作数据库时，查询表可能发生异常

`IOException`		  					 操作文件时发生的异常

`FileNotFoundException`		  操作一个不存在的文件时，发生的异常

`ClassNotFoundException`	    加载类，而类不存在时，异常

`EOFException`							 操作文件，到文件末尾，发送异常

`IllegalArgumentException`	参数异常



```java
package com.level5.Exception;

import java.io.FileInputStream;

public class ExceptionIO {
    public static void main(String[] args) {
        FileInputStream fis;
        try {
            fis = new FileInputStream("d:\\aa.jpg");
            int len;
            while ((len = fis.read()) != -1) {
                System.out.println(len);
            }
            fis.close();
        } catch (Exception e) {
            e.printStackTrace();
        } 
    }
}
```





### 10.4 异常处理

> 异常处理就是当异常发生时，对异常的处理方式



**异常处理方式**

- `try-catch-finally`

​		程序员在代码中捕获发生的异常，自行处理

- `throws`

​		将发生的异常抛出，交给调用者（方法）处理，最顶级的处理是JVM



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303071136004.png" style="zoom:67%;" />



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303071137028.png" style="zoom:67%;" />



> JVM调用了main方法，main调用f1方法，f1调用f2方法，此时f2方法产生一个异常，他要么自己try-catch，要么抛出给f1处理，如果抛出给f1，f1要么自己处理，要么抛出给main处理，main要么自己处理，要么抛出给JVM处理。JVM处理异常：打印异常信息，结束程序。如果没有try-catch，就会默认throws抛出（运行时异常默认throws抛出，但编译时异常需要手动处理），这也就是为什么没有try-catch时发生异常，程序直接终止的原因（一直throws交给JVM处理了）





### 10.5 try-catch异常处理

Java中提供`try-catch`来处理异常，try中用于包含可能出错的代码，catch用于处理try块中发生的异常。可以根据需要在程序中有多个`try-catch`



**基本语法**

```java
try {
	// 可能发生异常的代码
} catch(异常) {
	// 对异常的处理
} finaly {
    // 不管是否发送异常，都要执行
}
```





**注意：**

1、如果异常发生了，则异常后边的代码不会执行，直接进入catch块

2、如果异常没有发生，则顺序执行try中代码，不会进入catch

3、如果希望不管是否发生异常，都执行某块代码，则使用finally

```java
package com.level5.Exception.try_catch;

public class tryDetail {
    public static void main(String[] args) {
        try {
            String str = "11";
            int num = Integer.parseInt(str);
            System.out.println("num = " + num);
        } catch (NumberFormatException e) {
            System.out.println("发生异常");
        } finally {
            System.out.println("释放资源");
        }
        System.out.println("程序继续执行");
    }
}
```



4、可以有多个catch语句，捕获不同的异常，**要求父类异常在后，子类异常在前**，如果发生异常，只会匹配一个catch

```java
package com.level5.Exception.try_catch;

public class tryDetail02 {
    public static void main(String[] args) {
        try {
            Person person = new Person();
            person = null;
            System.out.println(person.getName());
            int n1 = 10;
            int n2 = 0;
            int res = n1 / n2;
        } catch (NullPointerException e) {
            System.out.println("空指针异常 = " + e.getMessage());
        } catch (ArithmeticException e) {
            System.out.println("算术异常 = " + e.getMessage());
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}

class Person {
    private String name = "jack";

    public String getName() {
        return name;
    }
}
```



5、可以进行`try-finally`配合使用，这种用法相当于没有捕获异常，因此程序会直接崩掉。应用场景：执行一段代码，不管是否发生异常，都必须执行某个业务逻辑

```java
package com.level5.try_catch;

public class tryDetail03 {
    public static void main(String[] args) {
        try {
            int n1 = 10;
            int n2 = 0;
            System.out.println(n1 / n2);
        } finally {
            System.out.println("程序资源释放");
        }
        System.out.println("程序继续执行");
    }
}
```





**异常处理练习**

**catch中有return或throws时（先计算完return的语句），必须先执行完finally后，然后继续执行这里的return和throws，但是如果finally中有return就直接结束了**



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303080933823.png" style="zoom:70%;" />



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303080935232.png" style="zoom:67%;" />



<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303080935408.png" style="zoom:70%;" />







### 10.6 throws异常处理

1、如果一个方法（中的执行语句）可能会发生异常，但是并不确定如何处理这种异常，则此方法应显示的声明抛出异常，表明该方法将不对这些异常进行处理，而由该方法的调用者处理

2、在方法声明中用`throws`语句可以声明抛出异常的列表，throws后边的异常类型可以是方法中产生的异常，也可以是它的父类



**注意**

1、对于编译异常，程序中必须处理，比如`try-catch`或`throws`

2、对于运行时异常，程序中如果没有处理，默认是throws方式处理

3、子类重写父类方法时，对抛出异常的规定，子类重写的方法，所抛出的异常类型要么和父类抛出的异常一致，要么为父类抛出异常类型的子类型

4、在throws过程中，如果有方法`try-catch`，就相当于处理异常，就可以不用`throws`

```java
package com.level5.throws_;

import java.io.FileInputStream;
import java.io.FileNotFoundException;

public class throwsDetail {
    public static void main(String[] args) {

    }

    public static void f1() throws FileNotFoundException{
        // 1.f2抛出的是一个编译异常
        // 2.即此时f1必须处理这个异常，要么try-catch要么throws
        f2();
    }

    public static void f2() throws FileNotFoundException {
        FileInputStream fis = new FileInputStream("d://aa.txt");
    }

    public static void f3() {
        // 1.f4方法抛出的是一个运行时异常
        // 2.因此f3会默认throws，不需要显示处理
        f4();
    }

    public static void f4() throws ArithmeticException{
    }
}

class Father {
    public void method() throws RuntimeException {
    }
}
class Son extends Father {
    // 子类抛出的异常必须和父类相同或者是父类的子类
    @Override
    public void method() throws ArithmeticException {
    }
}
```







### 10.7 自定义异常

> 当程序中出现了某些错误，但该错误并没有在Throwable子类中描述处理，这时可以自己设计异常类，用于描述该信息



**自定义异常步骤**

1、定义类：自定义异常类名，继承`Exception`或`RuntimeException`

2、如果继承`Exception`属于编译异常

3、如果继承`RuntimeException`属于运行异常（一般都是继承RuntimeException，因为如果继承Exception属于编译异常，此时抛出异常后调用者还需显示处理该异常

```java
package com.level5.customException;

public class CustomException {
    public static void main(String[] args)/*throws AgeException*/{

        int age = 18;
        if (!(20 <= age && age <= 120)) {
            throw new AgeException("年龄错误");
        }
        System.out.println("age = " + age);

    }
}

class AgeException extends RuntimeException {
    public AgeException(String message) {
        super(message);
    }
}
```





### 10.8 throw和throws区别

|        | 意义                     | 位置       | 后面跟的东西 |
| ------ | ------------------------ | ---------- | ------------ |
| throws | 异常处理的一种方式       | 方法声明处 | 异常类型     |
| throw  | 手动生成异常对象的关键字 | 方法体中   | 异常对象     |





**作业**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303081026151.png)



```java
package com.level5.homework;

public class homework01 {
    public static void main(String[] args) {
        try {
            if (args.length != 2) {
                throw new ArrayIndexOutOfBoundsException("参数个数不为2");
            }
            int n1 = Integer.parseInt(args[0]);
            int n2 = Integer.parseInt(args[1]);
            double res = cal(n1, n2);
            System.out.println("res = " + res);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println(e.getMessage());
        } catch (NumberFormatException e) {
            System.out.println("数据格式不正确，" + e.getMessage());
        } catch (ArithmeticException e) {
            System.out.println("算术异常，" + e.getMessage());
        }
        System.out.println("程序结束");
    }

    public static double cal(int n1, int n2) {
        return n1 / n2;
    }
}
```







## 11. 常用类

### 11.1 包装类

**包装类的分类**

- 针对八种基本数据类型相应的引用类型——包装类
- 有了类的特点，就可以调用类中的方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303081113248.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303081118068.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303081121408.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303081120909.png)





**包装类和基本数据类型的转换**

1、JDK5前的手动装箱和拆箱方式：装箱：基本类型==>包装类型，反之，拆箱

2、JDK5及之后，自动装箱和拆箱

3、自动装箱底层调用的是`valueOf`方法，比如`Integer.valueOf()`

```java
package com.level6.Wrapper;

public class Integer01 {
    public static void main(String[] args) {
        // int <= => Integer
        // jdk5之前是手动装箱和拆箱
        int n1 = 100;
        // 手动装箱 int => Integer
        Integer integer1 = new Integer(n1);
        Integer integer2 = Integer.valueOf(n1);

        // 手动拆箱 Integer => int
        int m1 = integer1.intValue();

        // jdk5之后，自动装箱和拆箱
        int n2 = 200;
        // 自动装箱 int => Integer
        Integer integer3 = n2;  // 底层是Integer.valueOf(n2)
        // 自动拆箱 Integer => int
        int m2 = integer3;  // 底层是intValue()方法
    }
}
```





**注意**

三目运算符是一个整体，因此第一个obj1会提升为Double类型

```java
package com.level6.Wrapper;

public class WrapperExercise01 {
    public static void main(String[] args) {
        Object obj1 = true ? new Integer(1) : new Double(2);    // 三元运算符是一个整体，因此会类型自动提升为Double
        System.out.println(obj1);   // 1.0
        Double a = (Double) obj1;
        System.out.println(a.doubleValue());

        Object obj2;
        if (true) {
            obj2 = new Integer(1);
        } else {
            obj2 = new Double(2);
        }
        System.out.println(obj2);   // 1
    }
}
```





### 11.2 包装类和String类型的相互转换

```java
package com.level6.Wrapper;

public class Wrapper2String {
    public static void main(String[] args) {
        // 包装类Integer => String
        Integer i = 100;
        // 方式一
        String s1 = i + "";
        // 方式二
        String s2 = i.toString();
        // 方式三
        String s3 = String.valueOf(i);

        // String => 包装类Integer
        String s4 = "1234";
        Integer i2 = Integer.parseInt(s4);  // Integer.parseInt(s4);返回的是int，int自动装箱
        Integer i3 = new Integer(s4);
    }
}
```





**Integer和Character类的常用方法**

```java
package com.level6.Wrapper;

public class WrapperMethod {
    public static void main(String[] args) {
        System.out.println(Integer.MIN_VALUE);              //返回最小值
        System.out.println(Integer.MAX_VALUE);              //返回最大值
        System.out.println(Character.isDigit('a'));     //判断是不是数字
        System.out.println(Character.isLetter('a'));    //判断是不是字母
        System.out.println(Character.isUpperCase('a')); //判断是不是大写
        System.out.println(Character.isLowerCase('a')); //判断是不是小写
        System.out.println(Character.isWhitespace('a'));//判断是不是空格
        System.out.println(Character.toUpperCase('a')); //转成大写
        System.out.println(Character.toLowerCase('A')); //转成小写
    }
}
```





### 11.3 Integer面试题

```java
package com.level6.Wrapper;

public class WrapperExercise02 {
    public static void main(String[] args) {
        Integer i = new Integer(1);
        Integer j = new Integer(1);
        System.out.println(i == j);    // False
        
        
        // 这里主要是看范围 -128 ~ 127 就是直接返回
        /*
        1. 如果 i 在 IntegerCache.low(-128)~IntegerCache.high(127),就直接从数组返回
        2. 如果不在 -128~127,就直接 new Integer(i)
        public static Integer valueOf(int i) {
            if (i >= IntegerCache.low && i <= IntegerCache.high)
                return IntegerCache.cache[i + (-IntegerCache.low)];
            return new Integer(i);
        }
        */
        Integer m = 1;  // 底层 Integer.valueOf(1); -> 阅读源码
        Integer n = 1;  // 底层 Integer.valueOf(1);
        System.out.println(m == n);     // true
        
        
        //128不在 -128 ~ 127 范围内，就 new Integer(128);
        Integer x = 128;    // 底层 Integer.valueOf(128);
        Integer y = 128;    // 底层 Integer.valueOf(128);
        System.out.println(x == y); // False
        
        
        // 只要有基本数据类型就是判断值是否相等
        Integer i1 = 127;
        int i2 = 127;
        System.out.println(i1 == i2);   // true

        Integer i3 = 128;
        int i4 = 128;
        System.out.println(i3 == i4);   // true
    }
}
```





### 11.4 String类

#### 11.4.1 **String 类的理解和创建对象**

1、String对象用于保存字符串，也就是一组字符序列

2、字符串的字符使用Unicode字符编码，一个字符（不区分字母还是汉字）占两个字节

3、String类较常用的构造器

`String s1 = new String();`

`String s2 = new String(String original)`

`String s3 = new String(char[] a)`

`String s4 = new String(char[] a, int startIndex, int count)`

`String s5 = new String(byte[] b)`



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303091522426.png)



4、`String`实现了`Serializable`，说明可以串行化，实现串行化表示可以在网络上传输

5、`String`实现了`Comparable`接口，说明String对象可以比较

6、`String`是final类，不能被其他类继承 

7、`String`有属性`private final char value[];`  用于存放字符串内容

**value是一个final类型，不可以修改，即value不能指向新的地址，但是单个字符可以改变，类似于C语言的指针常量，这里value就是一个指针常量**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303091530223.png)



下边是String的两个构造器，**String只有通过new构造一个对象后才会有属性`value[]`，并且该属性是final的，指向不能改（因为数组是引用类型，value存的是堆区创建的数组的地址），但是可以通过value修改字符的值。**由于**value是private修饰的，并且String没有提供修改它的方法**，因此我们创建String对象后，无法访问它的私有对象，因此我们也就无法通过value属性来修改单个字符值，**因此String是不可变的（但是可以通过反射获取value，然后修改String的值）。而指向不能改是针对于value来说的**，我们创建一个String对象，他指向一个堆区的地址，我们可以改变String的指向，让他指向堆区另一个String对象，但是不能修改其单个字符的值



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303091537228.png)



```java
package com.level6.String_;

public class String01 {
    public static void main(String[] args) {

        final char[] value = {'a', 'b', 'c', 'd'};
        value[2] = 'd';
        System.out.println(value);
        char v2[] = {'x', 'r', 'j'};
        // value = v2;
    }
}
```





#### 11.4.2 创建String两种方式

1、直接赋值：`String s = "xrj";`

2、调用构造器：`String s = new String("xrj");`



**两种创建String对象的区别**

**直接赋值：**先从常量池中查看是否有`“xrj"`数据空间，如果有，直接指向该地址。如果没有就创建，然后指向该地址。s的最终指向是常量池的空间地址

**调用构造器：**先在堆区创建空间，该空间是String对象的，里边维护了value属性，该属性指向了常量池的`xrj`空间。如果常量池中没有`"xrj"`，重新创建，如果有，直接指向。s最终指向的是堆区的String对象的空间



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303091558298.png)





**字符串的`intern()`方法返回的是常量池中的地址**

```java
package com.level6.String_;

public class String02 {
    public static void main(String[] args) {
        String a = "abc";
        String b = "abc";
        System.out.println(a == b); // true,a和b指向的都是常量池中abc的地址

        String c = new String("abc");
        String d = new String("abc");
        System.out.println(c.equals(d));    // true
        System.out.println(c == d); // false

        String a1 = "xrj";
        String a2 = new String("xrj");
        System.out.println(a1.equals(a2));  // true
        System.out.println(a1 == a2);   // false
        System.out.println(a1 == a2.intern());  // true, intern返回的是常量池中字符串地址
        System.out.println(a2 == a2.intern());  // false


        /*
            分析：p1指向堆区Person对象的地址，堆区该Person对象有一个String类型的name属性，name指向常量池中xrj的地址
                 p2指向堆区另一个Person对象的地址，该对象的name指向常量池中xrj的地址
         */
        Person p1 = new Person();
        p1.name = "xrj";
        Person p2 = new Person();
        p2.name = "xrj";
        System.out.println(p1.name.equals(p2.name));    // true
        System.out.println(p1.name == p2.name); // true
        System.out.println(p1.name == "xrj");   // true
    }
}

class Person {
    public String name;
}
```





#### 11.4.3 字符串的特性

1、String是一个final类，代表不可变的字符序列

2、字符串是不可变的，一个字符串对象一旦被分配，其内容不可变



以下语句创建了两个对象

> 分析：首先常量区创建一个hello，然后s1指向hello的地址，然后在常量区创建haha，将s1指向haha的地址

```
String s1 = "hello";
s1 = "haha";
```





以下语句创建了一个对象

> 分析：String a = "abc" + "hello";  等价于  String a = "abchello";
>
> 编译器做了优化，直接创建abchello，因为创建了abc和hello也没有变量指向

```
String a = "abc" + "hello";
```





`String c = "hello" + "abc";` 常量相加，c指向常量池的地址

如果`a = "hello";  b = "abc"`

`String c = a + b;`	变量相加，c指向堆区地址

如果`a = "hello";`

`String c = a + "abc";` c指向堆区地址

```java
package com.level6.String_;

public class String03 {
    public static void main(String[] args) {
        String a = "hello";
        String b = "abc";
        /*
            1. 先创建一个StringBuilder sb = new StringBuilder()
            2. 执行sb.append("hello")
            3. 执行sb.append("abc")
            4. 调用sb.toString()方法    String c = sb.toString();
            public String toString() {
                // Create a copy, don't share the array
                return new String(value, 0, count);
            }
            其实最后c指向的是堆中的对象(String) value[] -> 池中的 "helloabc"
        */
        String c = a + b;
        System.out.println("helloabc" == c);    // false
        System.out.println("helloabc" == c.intern());   // true

        String d = "hello" + "abc";
        System.out.println("helloabc" == d);    // true  d指向常量池

        String e = a + "abc";
        System.out.println(e == "helloabc");    // false
        System.out.println(e.intern() == "helloabc");   // true

    }
}
```





> 分析：
>
> 首先在堆区创建了一个String04的对象，s指向该对象的地址，在该对象中有一个String类型的str属性（在堆区再new一个String对象），它指向堆区的String对象的地址，该String对象中的value指向常量池中xrj的地址。
>
> String04这个对象还有一个final char[] ch属性（数组是引用类型），ch指向堆区的一个数组的地址，该数组为{'j', 'a', 'v', 'a'}
>
> 栈区执行change方法，传参后，形参str指向堆区创建的String对象的地址，然后执行str = "java";后，str指向常量池中java的地址。形参ch指向堆区中字符数组的地址，执行ch[0] = 'h';后，将数组第一个元素改为h。
>
> 最终输出：xrj and hava。因为str = "java";这条语句是将形参的值修改为常量池中的java的地址，对s.str没有影响，因此s.str还是xrj，而s.ch由于ch[0] = 'h';这条语句，将第一个元素改为h，所以s.ch变为hava

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303091650800.png" style="zoom:%;" />

```java
package com.level6.String_;

public class String04 {
    String str = new String("xrj");
    final char[] ch = {'j', 'a', 'v', 'a'};
    public void change(String str, char[] ch) {
        str = "java";
        ch[0] = 'h';
    }

    public static void main(String[] args) {
        String04 s = new String04();
        s.change(s.str, s.ch);
        System.out.print(s.str + " and ");    
        System.out.println(s.ch);   // xrj and hava
    }
}
```





#### 11.4.4 字符串的HashCode方法

> 我们不能使用hashCode方法来比较两个字符串地址是否相同，因为字符串的hashCode值不是地址，是通过对每个字符进行一定规则进行叠加得到的，String类中还有一个hash属性，默认为0，当hash为0时，调用hashCode方法就会计算这个hashCode值，然后赋值给hash，如果hash不为0，就直接返回。通过下面源码可以看出，只有字符串的内容是相同的，则他们的hashCode是相同的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101000051.png)







#### 11.4.5 String类常用方法

`equals()		// 区分大小写，判断内容是否相等`

`equalsIgnoreCase()		// 忽略大小写判断内容是否相等`		

`length()		// 获取字符串长度`

`indexOf()		// 获取字符/字符串在字符串中第一次出现的索引，索引从0开始，找不到返回-1`

`lastIndexOf()	// 获取字符/字符串在字符串中最后一次出现的索引，索引从0开始，找不到返回-1`

`substring()	// 截取指定范围的子串`

`trim()		// 去除前后空格`

`charAt()	// 获取某处索引的字符`

`toUpperCase()	// 转大写`

`toLowerCase()	// 转小写`

`replace()	// 替换字符串中的字符`

`split()		// 分割字符串`

`compareTo()	// 比较两个字符串大小`

`toCharArray()	// 转为字符数组`

`format()	// 格式化字符串`







### 11.5 StringBuffer类

#### 11.5.1 StringBuffer介绍

`StringBuffer代表可变的字符序列，可以对字符串内容进行增删`，是可变长度的

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101115559.png" style="zoom:80%;" />

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101116831.png" style="zoom:80%;" />

1、`StringBuffer`是final类

2、`StringBuffer`实现了`Serializable`接口，可以串行化

3、**继承了抽象类`AbstractStringBuilder`**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101119021.png)

4、`AbstractStringBuilder`的属性`char[] value`存放字符序列。**这里value没有用final修饰，value指向char数组的地址，char数组放在堆区，每次改变字符串时，改变内容，不需要改变地址，除非char数组容量不够用了，会开辟新的空间**



**源码分析**

```java
String str = "hello";
StringBuffer stringBuffer = new StringBuffer(str);
System.out.println(stringBuffer);
```

1、`StringBuffer stringBuffer = new StringBuffer(str);` 执行后会先创建一个大小为 `str.length() +16` 的char数组，然后执行`append(str);`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161157261.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161157692.png)

2、执行`append(str);` 然后进入`super.append(str)`方法，如果`append`的字符串为`null`，就执行`appendNull()`方法，然后判断长度是否超出char数组容量，如果超出就进行扩容，通过`Arrays.copy()`进行扩容。然后执行`str.getChars(0, len, value, count)`方法，该方法调用的是`System.arraycopy()`方法进行元素添加

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161158361.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161159928.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161200158.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161201650.png)



#### 11.5.2 StringBuffer构造器

**`StringBuffer()`**

构造一个不带字符的字符串缓冲区，初始容量为16。

下面是构造步骤，最终构造一个大小为16的char数组返回给value

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101130633.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101131247.png)



**`StringBuffer(int capacity)`**

构造一个不带字符串，指定大小的char数组

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101133661.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101131247.png)



**`StringBuffer(String str)`**

通过给一个`String `创建` StringBuffer`，char[] 数组的大小为str.length() + 16

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303101137727.png)







#### 11.5.3 String和StringBuffer相互转换

```java
package com.level6.StringBuffer_;

public class StringBuffer01 {
    public static void main(String[] args) {
        // String => StringBuffer
        String str = "hello";
        // 1. 使用构造器  返回的是StringBuffer对象，对str本身没有影响
        StringBuffer stringBuffer = new StringBuffer(str);
        System.out.println(stringBuffer);

        // 2.先构造出一个空的StringBuffer，然后append
        StringBuffer stringBuffer1 = new StringBuffer();
        stringBuffer1.append(str);
        System.out.println(stringBuffer1);

        // StringBuffer => String
        StringBuffer stringBuffer2 = new StringBuffer("xrj");
        // 1.使用StringBuffer提供的toString方法
        String s = stringBuffer2.toString();
        System.out.println(s);

        // 2.使用构造器将StringBuffer传入
        String s2 = new String(stringBuffer2);
        System.out.println(s2);
    }
}
```





#### 11.5.4 StringBuffer常用方法

```java
package com.level6.StringBuffer_;

public class StringBufferMethod {
    public static void main(String[] args) {
        StringBuffer s = new StringBuffer("hello");

        // append
        s.append(',');
        s.append("world");
        System.out.println(s);
        s.append("xrj").append(100).append(true).append(10.5);
        System.out.println(s);

        // delete
        s.delete(1, 3); // 删除下标[1, 3)的字符
        System.out.println(s);

        // 修改
        s.replace(0, 4, "xrj"); // 把[0, 4)之间字符串更换为xrj
        System.out.println(s);

        // 查找
        int idx = s.indexOf("world");   // 返回world在s中第一次出现的位置,没有就返回-1
        System.out.println(idx);

        // 插入
        s.insert(3, "nihao");   // 在下标为3的位置，增加字符串nihao
        System.out.println(s);

        // 长度
        System.out.println(s.length());
    }
}
```





**StringBuffer练习**

```java
package com.level6.StringBuffer_;

public class StringBufferExercise01 {
    public static void main(String[] args) {
        String str = null;
        StringBuffer sb = new StringBuffer();
        // append最终会调用AbstractStringBuilder 的append方法，最终会返回null字符串
        /*
        public AbstractStringBuilder append(String str) {
            if (str == null)
                return appendNull();
            int len = str.length();
            ensureCapacityInternal(count + len);
            str.getChars(0, len, value, count);
            count += len;
            return this;
        }
         */
        sb.append(str);
        System.out.println(sb); // null
        System.out.println(sb.length());    // 4

        // super(str.length() + 16);  构造器执行这句话会抛出空指针异常
        StringBuffer sb2 = new StringBuffer(str);
        System.out.println(sb2);
    }
}
```





### 11.6 StringBuilder类

> StringBuilder是一个可变的字符序列。此类提供一个与StringBuffer兼容的API，但不保证同步（StringBuilder不是线程安全）。该类被用作StringBuffer的一个简易替换。用在字符串缓存区被单个线程使用时，优先采用该类

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303110956897.png)

1、StringBuilder 的直接父类 是 AbstractStringBuilder

2、StringBuilder 实现了 Serializable, 即 StringBuilder 的对象可以串行化 

3、在父类中 AbstractStringBuilder 有属性 `char[] value`，不是 final ，该 value 数组存放 字符串内容，存放在堆区

4、StringBuffer 是一个 final 类，不能被继承

5、StringBuilder 的方法，没有做互斥的处理,即没有 synchronized 关键字,因此在单线程的情况下使用





**String、StringBuffer、StringBuilder的比较**

1、StringBuffer和StringBuilder均代表可变字符序列，且方法一样

**2、String：不可变字符序列，效率低，但是复用率高**

**3、StringBuffer：可变字符序列，效率高（增删）、线程安全**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303111018416.png" style="zoom:80%;" />

**4、StringBuilder：可变字符序列，效率最高，线程不安全**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303111019476.png" style="zoom:80%;" />



**String、StringBuffer 和 StringBuilder的选择**

1、如果字符串存在大量的修改操作，一般使用`StringBuffer`和`StringBuilder`

2、如果字符串存在大量的修改操作，并在单线程的情况，使用`StringBuilder`

3、如果字符串存在大量的修改操作，并在多线程的情况，使用`StringBuffer`

4、如果我们字符串很少修改，被多个对象引用，使用String







### 11.7 Arrays类

> Arrays里包含了一系列静态方法，用于管理或操作数组



1、`toString`返回数组的字符串形式

2、sort排序（自然排序和定制排序）  **注意在对数组指定排序时，数组类型必须是引用类型**

- 如果使用`Arrays.sort(arr);` 此时arr数组可以是int类型的，`Arrays.sort()` 方法会使用相应的快速排序算法对数组进行排序，而不需要使用比较器。

- 数组中的元素是包装类型（如 Integer、Double 等），则 `Arrays.sort()` 方法将调用包装类型的 `compareTo()` 方法进行排序，或者使用指定的比较器进行排序。

- 如果数组使用lambda表达式进行比较，例如`Arrays.sort(arr, (a, b) -> b - a);` 那么arr必须是包装类型的，因为要使用指定的比较器比较

```java
package com.level6.Arrays_;

import java.util.Arrays;
import java.util.Comparator;

public class Arrays01 {
    public static void main(String[] args) {
        // toString
        Integer[] arr = {1, 5, 10, 8};
        System.out.println(Arrays.toString(arr));

        // sort
        // 默认排序
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));

        // 指定规则排序
        /*
            1.Arrays.sort(arr, new Comparator()
            2.最终到TimSort类的
            private static <T> void binarySort(T[] a, int lo, int hi, int start, Comparator<? super T> c)
            3.执行binarySort方法，根据动态绑定机制，将我们的匿名内部类传给c
            while (left < right) {
                int mid = (left + right) >>> 1;
                if (c.compare(pivot, a[mid]) < 0)
                    right = mid;
                else
                    left = mid + 1;
                }
         */
        Arrays.sort(arr, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                Integer i1 = (Integer) o1;
                Integer i2 = (Integer) o2;
                return i2 - i1;
            }
        });
        
        // 对数组倒序排序 注意在对数组指定排序时，数组类型必须是引用类型
        Arrays.sort(arr, Collections.reverseOrder());
        System.out.println(Arrays.toString(arr));
        
    }
}
```





**模拟Arrays排序**

```java
package com.level6.Arrays_;

import java.util.Arrays;
import java.util.Comparator;

public class Arrays02 {
    public static void main(String[] args) {
        int[] arr = {5, 9, 1, 20, 35};
        // bubble01(arr);
        System.out.println(Arrays.toString(arr));

        bubble02(arr, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                int i1 = (Integer)o1;
                int i2 = (Integer)o2;
                return i2 - i1;
            }
        });
        System.out.println(Arrays.toString(arr));
    }

    // 普通冒泡排序
    public static void bubble01(int[] arr) {
        for (int i = 0; i < arr.length - 1; i++)
            for (int j = 0; j < arr.length - 1 - i; j++) {
                if (arr[j] > arr[j + 1]) {  // 从小到大排序
                    int tmp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = tmp;
                }
            }
    }

    // 指定规则的冒泡排序
    public static void bubble02(int[] arr, Comparator c) {
        for (int i = 0; i < arr.length - 1; i++)
            for (int j = 0; j < arr.length - 1 - i; j++) {
                if (c.compare(arr[j], arr[j + 1]) > 0) {
                    int tmp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = tmp;
                }
            }
    }
}
```





**Arrays其他常用方法**

```java
package com.level6.Arrays_;

import java.util.Arrays;
import java.util.List;

public class Arrays03 {
    public static void main(String[] args) {
        int arr[] = {2, 5, 45, 65, 102};
        // 二分查找
        /*
            要求数组是有序的，返回查找元素的下标，如果元素不存在，返回 return -(low + 1);
         */
        int idx = Arrays.binarySearch(arr, 6);
        System.out.println(idx);

        // copyOf数组元素复制
        /*
            如果拷贝长度大于arr.length，就添加0
            如果拷贝长度小于0，抛出异常 NegativeArraySizeException
         */
        int arr2[] = Arrays.copyOf(arr, arr.length + 1);
        System.out.println(Arrays.toString(arr2));

        // fill数组元素填充
        int arr3[] = new int[5];
        System.out.println(Arrays.toString(arr3));
        Arrays.fill(arr3, 1);
        System.out.println(Arrays.toString(arr3));

        // equals 比较两个数组内容是否一致
        System.out.println(Arrays.equals(arr, arr2));

        // asList 将一组值转为List
        /*
            1.asList会将(1, 2, 5, 10, 8)转为一个list集合
            2.返回的list编译类型为List(接口)
            3.返回的list运行类型为java.util.Arrays$ArrayList,是Arrays类的静态内部类
         */
        List list = Arrays.asList(1, 2, 5, 10, 8);
        System.out.println(list);
        System.out.println("运行类型 = " + list.getClass());
    }
}
```





### 11.8 System类

1、`exit`：退出当前程序

2、`arraycopy`：复制当前数组，适合底层调用，一般使用`Arrays.copyOf`完成复制数组（底层就是arraycopy）

3、`currentTimeMillens`：返回当前时间距离1970-1-1的毫秒数

4、`gc`：运行垃圾回收机制。`System.gc();`

```java
package com.level6.System_;

import java.util.Arrays;

public class System_ {
    public static void main(String[] args) {
//        System.out.println("ok1");
//        // exit(0),0表示正常退出
//        System.exit(0);
//        System.out.println("ok2");


        // arraycopy
        int src[] = {1, 5, 9};
        int dst[] = new int[3];

        /*
         * src 源数组
         * srcPos： 从源数组的哪个索引位置开始拷贝
         * dest : 目标数组，即把源数组的数据拷贝到哪个数组
         * destPos: 把源数组的数据拷贝到 目标数组的哪个索引
         * length: 从源数组拷贝多少个数据到目标数组
         */
        System.arraycopy(src, 0, dst, 0, 3);
        System.out.println(Arrays.toString(dst));

        // 返回当前时间距离1970-1-1的毫秒数
        System.out.println(System.currentTimeMillis());
    }
}
```





### 11.9 BigInteger

> BigInteger适合保存比较大的数



**常用方法**

1、add 加

2、subtract 减

3、multiply 乘

4、divide 除



```java
package com.level6.BigNum;

import java.math.BigInteger;

public class BigInteger_ {
    public static void main(String[] args) {
        BigInteger bigInteger = new BigInteger("12345156999999999999999999999");
        BigInteger bigInteger1 = new BigInteger("232323");
        BigInteger add = bigInteger.add(bigInteger1);
        System.out.println(add);

        BigInteger subtract = bigInteger.subtract(bigInteger1);
        System.out.println(subtract);

        BigInteger multiply = bigInteger.multiply(bigInteger1);
        System.out.println(multiply);

        BigInteger divide = bigInteger.divide(bigInteger1);
        System.out.println(divide);

    }
}
```





### 11.10 BigDecimal

> BigDecimal适合保存精度更高的浮点数



```java
package com.level6.BigDecimal;

import java.math.BigDecimal;

public class BigDecimal_ {
    public static void main(String[] args) {
        BigDecimal bigDecimal = new BigDecimal("1.111111111111111111111111111110001");
        System.out.println(bigDecimal);
        BigDecimal bigDecimal1 = new BigDecimal("1.3");

        BigDecimal add = bigDecimal.add(bigDecimal);
        System.out.println(add);

        BigDecimal subtract = bigDecimal.subtract(bigDecimal1);
        System.out.println(subtract);

        BigDecimal multiply = bigDecimal.multiply(bigDecimal1);
        System.out.println(multiply);

        // 调用divide方法时，可能会抛出异常ArithmeticException，出现循环小数
        // 可以指定精度BigDecimal.ROUND_CEILING，保留小数到分子的位数
        BigDecimal divide = bigDecimal.divide(bigDecimal1, BigDecimal.ROUND_CEILING);
        System.out.println(divide);
    }
}
```





### 11.11 日期类

#### 11.11.1 Date

1、`Date`：精确到毫秒，代表特定瞬间

2、`SimpleDateFormat`：格式和解析日期的类。它允许进行格式化（日期 -> 文本），解析（文本 -> 日期）和规范化



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303130949321.png)



```java
package com.level6.Date_;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Date01 {
    public static void main(String[] args) throws ParseException {
        // 1.获取当前时间
        // 2.这里的Date类在java.util包下
        // 3.默认输出的日期格式是国外的方式, 因此通常需要对格式进行转换
        Date date = new Date();
        System.out.println(date);

        // 通过指定毫秒数得到时间
        Date date1 = new Date(454564354485l);
        System.out.println(date1);

        // 1.创建SimpleDateFormat对象，可以指定相应的格式
        // 2.这里使用的字母是规定好的
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 hh:mm:ss E");
        String format = sdf.format(date);
        System.out.println(format);
        format = sdf.format(date1);
        System.out.println(format);

        // 1. 可以把一个格式化的 String 转成对应的 Date
        // 2. 得到 Date在输出时，还是按照国外的形式，如果希望指定格式输出，需要转换
        // 3. 在把 String -> Date ， 使用的 sdf 格式需要和你给的 String 的格式一样，否则会抛出转换异常
        String date2 = "1999年10月26日 17:30:35 星期二";
        Date parse = sdf.parse(date2);
        System.out.println(parse);
        System.out.println(sdf.format(parse));
    }
}
```





#### 11.11.2 Calendar

1、第二代日期类，主要就是`Calendar`类（日历）

`public abstract class Calendar implements Serializable, Cloneable, Comparable<Calendar> `

2、`Calendar`类是一个抽象类，它为特定瞬间与一组YEAR、MONTH、DAY_OFMONTH等日历字段之间的转换提供了一些方法

```java
package com.level6.Date_;

import java.util.Calendar;

public class Calender_ {
    public static void main(String[] args) {
        // Calendar是一个抽象类，且构造器是protected的，只能通过getInstance方法获取对象实例
        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar);

        System.out.println("年：" + calendar.get(Calendar.YEAR));
        // 这里+1是因为 Calendar 返回月时候，是按照 0 开始编号
        System.out.println("月：" + (calendar.get(Calendar.MONTH) + 1));
        System.out.println("日：" + calendar.get(Calendar.DAY_OF_MONTH));
        // 如果想得到24进制的小时，使用HOUR_OF_DAY
        System.out.println("小时：" + calendar.get(Calendar.HOUR));
        System.out.println("分钟：" + calendar.get(Calendar.MINUTE));
        System.out.println("秒：" + calendar.get(Calendar.SECOND));

        // Calender 没有专门的格式化方法，所以需要自己来组合显示
        System.out.println(calendar.get(Calendar.YEAR) + "-" + calendar.get(Calendar.MONTH) +
                "-" + calendar.get(Calendar.DAY_OF_MONTH) + " " + calendar.get(Calendar.HOUR) +
                ":" + calendar.get(Calendar.MINUTE) + ":" + calendar.get(Calendar.SECOND));
    }
}
```





#### 11.11.3 LocalDateTime

> JDK1.0中包含了一个java.util.Date类，但是它的大多数方法在JDK1.1引入Calendar类之后就弃用了，而Calendar存在的问题是

1、可变性：像日期时间这样的类应该是不可变的

2、偏移性：Date中年份从1900开始，月份从0开始

3、格式化：格式化只对Date有用，Calendar不行

4、此外，它们也不是线程安全的；不能处理润秒（每隔两天，多输出一秒）



- `LocalDate`（年/月/日）、`LocalTime`（时/分/秒）、`LocalDateTime`（年/月/日/时/分/秒）

- `LocalDate`只包含日期，可以获取日期字段
- `LocalTime`只包含时间，可以获取时间字段
- `LocalDateTime`包含日期+时间，可以获取日期和时间字段

- `DateTimeFormatter` 格式化日期类

```java
DateTimeFormatter dtf = DateTimeFormatter.ofPattern("日期格式");
String format = dtf.format(日期对象);
```



```java
package com.level6.Date_;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;

public class LocalDate_ {
    public static void main(String[] args) {
        // 1. 使用now返回当前时间,返回类型为LocalDateTime
        LocalDateTime ldt = LocalDateTime.now();
        System.out.println(ldt);
        LocalDate ld = LocalDate.now();
        System.out.println(ld);
        LocalTime lt = LocalTime.now();
        System.out.println(lt);

        System.out.println("year = " + ldt.getYear());
        System.out.println("month = " + ldt.getMonth());
        System.out.println("month = " + ldt.getMonthValue());

        // 2.使用DateTimeFormatter.ofPattern可以将LocalDateTime格式化
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
        String format = dtf.format(ldt);
        System.out.println(format);

        // 3.提供plus和minus方法可以对当前时间对象进行加或减
        LocalDateTime localDateTime = ldt.plusDays(3);
        System.out.println(dtf.format(localDateTime));

        LocalDateTime localDateTime1 = ldt.minusMinutes(1234);
        System.out.println(dtf.format(localDateTime1));
    }
}
```



**Instant时间戳**

> 类似于Date

- `Instant` --> `Date`

  `Date date = Date.from(instant);`

- `Date` --> `Instant`

​		`Instant instant = date.toInstant();`

```java
package com.level6.Date_;

import java.util.Date;
import java.time.Instant;

public class Instant_ {
    public static void main(String[] args) {
        // 通过静态方法now()获取表示当前时间戳的对象
        Instant ins = Instant.now();
        System.out.println(ins);

        // 通过from方法可以将instant对象转为Date
        Date date = Date.from(ins);
        System.out.println(date);

        // 通过Date的toInstant()方法可以将Date对象转为Instant
        Instant instant = date.toInstant();
        System.out.println(instant);
    }
}
```









## 12. 集合

> 集合可以动态保存任意多个对象。提供了一系列操作对象的方法



### 12.1 集合框架体系

- `Collection`接口有两个子接口，`List`和`Set`，他们实现的子类都是单列集合
- `Map`接口的实现子类是双列集合

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303151043604.png" style="zoom:80%;" />



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303151045023.png)







### 12.2 Collection接口和常用方法

#### 12.2.1 Collection接口的实现类特点

1、实现`Collection`接口的子类可以存放多个元素，**每个元素可以是`Object`**

2、有些`Collection`的实现类，可以存放重复元素，有些不可以

3、有些`Collection`的实现类，有些是有序的（List），有些是无序的（Set）

4、`Collection`接口没有直接的实现子类，是通过他的子接口`List`和`Set`来实现的





#### 12.2.2 Collection接口常用方法

```java
package com.level7.collection_;

import java.util.Collection;
import java.util.ArrayList;

public class CollectionMethod {
    public static void main(String[] args) {
        Collection list = new ArrayList();

        // add添加单个元素
        list.add(123);  // list.add(new Integer(10)),里边存的都是Object类
        list.add(true);
        list.add("xrj");
        list.add("xyy");
        list.add("x1");
        list.add(1.12);
        list.add("rdwaj");
        System.out.println(list);

        // remove删除元素
        boolean remove1 = list.remove(true);// 返回值为boolean，参数为对象
        System.out.println(list);

        // contains查找元素是否存在
        System.out.println(list.contains(123));

        // size返回list元素个数
        System.out.println(list.size());

        // isEmpty判断是否为空
        System.out.println(list.isEmpty());

        // clear清空
        // list.clear();
        // System.out.println(list);

        // addAll 添加多个元素
        ArrayList list1 = new ArrayList();
        list1.add(1223);
        list1.add("xxx");
        list.addAll(list1);
        System.out.println(list);

        // containsAll 判断多个元素是否都存在
        System.out.println(list.containsAll(list1));

        // removeAll 删除多个元素
        ArrayList list2 = new ArrayList();
        list2.add(456);
        list2.add("xxx");
        list.removeAll(list2);
        System.out.println(list);

        // get 获取元素 Collection没有get方法，List才有
        // System.out.println(list.get(3));	// 参数为list的下标，返回值为对象
    }
}
```





#### 12.2.3 Collection遍历元素-Iterator

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303151058192.png)



1、`Iterator`对象称为迭代器，主要用于遍历`Collection`集合的元素

2、所有实现了`Collection`接口的集合类都有一个`iterator()`方法，用以返回一个实现了`Iterator`接口的对象

3、`Iterator`的结构

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303151101502.png)

```java
package com.level7.collection_;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;
import java.util.List;

public class CollectionIterator {
    public static void main(String[] args) {
        List col = new ArrayList();

        col.add(new Book("三国演义", "罗贯中", 10.1));
        col.add(new Book("小李飞刀", "古龙", 5.1));
        col.add(new Book("红楼梦", "曹雪芹", 34.6));

        // 通过迭代器遍历 col 集合
        Iterator iterator = col.iterator();

        // 快速生成 while => itit
        // 显示所有的快捷键的的快捷键 ctrl + j
        while (iterator.hasNext()) {
            Object obj = iterator.next();   // 返回对象类型为Object
            System.out.println("obj=" + obj);
        }
        
        // 当退出 while 循环后 , 这时 iterator 迭代器，指向最后的元素
        // iterator.next();//NoSuchElementException

        // 如果希望再次遍历，需要重置我们的迭代器
        iterator = col.iterator();
        System.out.println("===第二次遍历===");
        while (iterator.hasNext()) {
            Object obj = iterator.next();
            System.out.println("obj=" + obj);
        }
    }
}

class Book {
    private String name;
    private String author;
    private double price;

    public Book(String name, String author, double price) {
        this.name = name;
        this.author = author;
        this.price = price;
    }

    @Override
    public String toString() {
        return "Book{" +
                "name='" + name + '\'' +
                ", author='" + author + '\'' +
                ", price=" + price +
                '}';
    }
}
```





#### 12.2.4 增强for遍历

> 增强for底层还是调用迭代器。增强for也可以用在普通数组

```java
package com.level7.collection_;

import java.util.ArrayList;
import java.util.List;

public class CollectionFor {
    public static void main(String[] args) {
        List col = new ArrayList();

        col.add(new Book("三国演义", "罗贯中", 10.1));
        col.add(new Book("小李飞刀", "古龙", 5.1));
        col.add(new Book("红楼梦", "曹雪芹", 34.6));

        // 增强for底层还是迭代器
        for (Object book : col) {
            System.out.println(book);
        }
    }
}
```





### 12.3 List

#### 12.3.1 List接口介绍

1、`List`集合类中元素有序，且可重复

2、`List` 集合中的每个元素都有其对应的顺序索引，即支持索引

```java
package com.level7.List_;

import java.util.ArrayList;
import java.util.List;

public class List_ {
    public static void main(String[] args) {
        // 1.List集合类中元素有序，且可重复
        List list = new ArrayList();
        list.add(123);
        list.add("xrj");
        list.add("xyy");
        list.add("xrj");
        System.out.println(list);

        // 2.List 集合中的每个元素都有其对应的顺序索引，即支持索引
        System.out.println(list.get(2));
    }
}
```





#### 12.3.2 List接口常用方法

```java
package com.level7.List_;

import java.util.ArrayList;
import java.util.List;

public class ListMethod {
    public static void main(String[] args) {
        List list = new ArrayList();
        list.add("张三丰");
        list.add("贾宝玉");

        // void add(int index, Object ele):在 index 位置插入 ele 元素
        list.add(1, "xrj");
        System.out.println(list);

        // boolean addAll(int index, Collection eles):从 index 位置开始将 eles 中的所有元素添加进来
        List list2 = new ArrayList();
        list2.add("jack");
        list2.add("tom");
        list.addAll(2, list2);
        System.out.println(list);

        // Object get(int index):获取指定 index 位置的元素
        System.out.println(list.get(3));

        // int indexOf(Object obj):返回 obj 在集合中首次出现的位置
        System.out.println(list.indexOf("tom"));

        // int lastIndexOf(Object obj):返回 obj 在当前集合中末次出现的位置
        list.add("xrj");
        System.out.println(list);
        System.out.println(list.lastIndexOf("xrj"));

        // Object remove(int index):移除指定 index 位置的元素，并返回此元素
        System.out.println(list.remove(0));
        System.out.println(list);

        // Object set(int index, Object ele):设置指定 index 位置的元素为 ele , 相当于是替换.
        list.set(1, "玛丽");
        System.out.println(list);

        // List subList(int fromIndex, int toIndex):返回从 fromIndex 到 toIndex 位置的子集合
        // fromIndex <= subList < toIndex
        List returnlist = list.subList(1, 4);
        System.out.println(returnlist);
    }
}
```





### 12.4  ArrayList底层结构和源码分析

#### 12.4.1 ArrayList注意事项

1、`ArrayList`可以加入`null`，并且可以放多个

2、`ArrayList`是由数组来实现数据存储的

3、`ArrayList`基本等同于`Vector`，`ArrayList`是线程不安全的，因为源码没有`synchronized`关键字修饰，但是执行效率高。多线程情况下不建议使用`ArrayList`





#### 12.4.2 ArrayList源码分析

1、`ArrayList`中维护了一个`Object`类型的数组`transient Object[] elementData;` 	`transient`表示瞬间、短暂，表示该属性不会被序列化

2、**当创建`ArrayList`对象时，如果使用的是无参构造器，则初始化`elementData`的容量为0，第一次添加，则扩容`elementData`为10，如需再次扩容，则扩容**

**`elementData`为1.5倍**

3、**如果使用的是指定大小的构造器，则初始`elementData`容量为指定大小，如需扩容，直接扩容为`elementData`的1.5倍**



```java
package com.level7.List_;

import java.util.ArrayList;

public class ArrayListSource {
    public static void main(String[] args) {

        ArrayList list = new ArrayList();
        // ArrayList list = new ArrayList(8);
        // 使用 for 给 list 集合添加 1-10 数据
        for (int i = 1; i <= 10; i++) {
            list.add(i);
        }
        // 使用 for 给 list 集合添加 11-15 数据
        for (int i = 11; i <= 15; i++) {
            list.add(i);
        }
        list.add(100);
        list.add(200);
        list.add(null);
    }
}
```



**使用无参构造器创建`ArrayList`**

1、执行`ArrayList list = new ArrayList();`  将`elementData`赋一个空数组

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161639454.png)

2、执行`list.add(i);` 由于是无参构造，所以第一次扩容容量为10

​	2.1 add时，先判断当前容量是否可用（初始容量为0）。先确定是否需要扩容，然后在赋值

![](C:\Users\XRJ\Desktop\202303161641166.png)

​	2.2 计算当前所需最小容量

![](C:\Users\XRJ\Desktop\202303161642503.png)

​	2.3 传入的`Object[] elementData`就是`elementData`，`minCapacity`是1，`elementData`初始的时候就是空，因此执行if中的语句，`DEFAULT_CAPACITY`

​		是10，因此返回10

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161642380.png)

​	2.4 现在所需最小容量为10，但是`elementData`的容量为0，所以需要扩容，进入`grow`方法进行扩容。`modCount++`记录集合被修改的次数，即add的次数

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161645661.png)

​	2.5 `oldCapacity`现在是0，`newCapacity`是`oldCapacity`的1.5倍，第一次扩容，所以`newCapacity`也是0，然后`0-10`小于0，执行第一个if，将`newCapacity`置为10，最后执行`elementData = Arrays.copyOf(elementData, newCapacity);` 使用`copyOf`将原数组拷贝到新数组，并将新数组扩容。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161646731.png)

​	2.6 然后就一直返回，将当前add的值加入list中

![](C:\Users\XRJ\Desktop\202303161648217.png)



3、当前容量为10，如果需要存放11个数据时，会继续扩容，这时就是扩容到原来的1.5倍即15个大小





**使用有参构造器创建`ArrayList`**

1、执行`ArrayList list = new ArrayList(8);` 为list创建一个大小为8的数组，后边容量不够就扩容为原始容量1.5倍，和上边流程一致

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161650392.png)







### 12.5 Vector底层结构和源码分析

#### 12.5.1 Vector介绍



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161711757.png)



1、`Vector`底层也是一个数组 `protected Object[] elementData;`

2、`Vector`是线程同步的，即线程安全，它的方法都带有`synchronized`关键字

3、`Vector`的无参构造默认初始化数组大小为10，如果需要扩容，每次扩容两倍

4、`Vector`的有参构造，初始化时容量为指定大小，需要扩容，每次扩容两倍





#### 12.5.2 Vector底层分析

```java
package com.level7.List_;

import java.util.Vector;

public class Vector_ {
    public static void main(String[] args) {
        // Vector vector = new Vector();
        Vector vector = new Vector(8);
        for (int i = 0; i < 20; i++) {
            vector.add(i);
        }
        vector.add(100);
        System.out.println("vector=" + vector);
    }
}
```



**Vector无参构造**

1、执行`Vector vector = new Vector();`语句进入无参构造方法，调用有参构造方法，初始容量为10

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161715134.png)

​	初始指定`initialCapacity`为10，`capacityIncrement`为0

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161717395.png)

2、执行`vector.add(i)`，先判断是否需要扩容，然后赋值，`modCount`表示add多少次

![](C:\Users\XRJ\Desktop\202303161718523.png)

3、如果当前所需最小容量大于数组容量，那么就扩容

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161718592.png)

4、如果需要扩容，就进入`grow`方法。`oldCapacity`是原数组大小，如果`capacityIncrement`大于0，那么我们的容量就增加`capacityIncrement`，但是`capacityIncrementment`默认为0，所以`newCapacity`就是`oldCapacity`的两倍，然后使用`copyOf`方法扩容

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161720823.png)

5、依次返回，将add的值加进去



**Vector有参构造**

1、执行`Vector vector = new Vector(8);`，进入该方法，数组大小初始化为指定大小，如果需要继续扩容和无参构造一样

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303161724217.png)







### 12.6 ArrayList和Vector对比

|           | 底层结构                       | 出现版本 | 线程安全       | 扩容倍数                                                     |
| --------- | ------------------------------ | -------- | -------------- | ------------------------------------------------------------ |
| ArrayList | transient Object[] elementData | JDK1.2   | 不安全，效率高 | 如果是有参构造，每次扩容1.5倍。如果是无参，初始化大小为0，第一次扩容为10，然后每次扩容1.5倍。 |
| Vector    | protected Object[] elementData | JDK1.0   | 安全，效率不高 | 如果是无参，初始化大小为10，每次扩容2倍。如果指定大小，每次扩容2倍。 |









### 12.7 LinkedList底层结构

#### 12.7.1 LinkedList介绍

> 1、LinkedList底层实现了双向链表和双端队列特点
>
> 2、可以添加任意元素（元素可以重复），包括null
>
> 3、线程不安全，没有实现同步





1、`LinkedList`底层维护了一个双向链表

2、`LinkedList`中维护了两个属性`first`和`last`分别指向首节点和尾结点

3、每个节点（Node对象），里边又维护了`prev`，`next`、`item`三个属性，其中通过`prev`指向前一个，通过`next`指向后一个节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171113230.png)



**模拟双向链表**

```java
package com.level7.List_;

public class LinkedList01 {
    public static void main(String[] args) {
        Node jack = new Node("jack");
        Node tom = new Node("tom");
        Node xrj = new Node("xrj");
        jack.next = tom;
        tom.next = xrj;
        tom.pre = jack;
        xrj.pre = tom;

        Node first = jack;
        Node last = xrj;

        // 在tom和xrj之间增加一个xyy
        Node xyy = new Node("xyy");
        xyy.pre = xrj.pre;
        xyy.next = tom.next;
        tom.next = xyy;
        xrj.pre = xyy;

        while (first != null) {
            System.out.println(first);
            first = first.next;
        }

        while (last != null) {
            System.out.println(last);
            last = last.pre;
        }
    }
}

class Node {
    public Object item;
    public Node next;
    public Node pre;

    public Node(Object item) {
        this.item = item;
    }

    @Override
    public String toString() {
        return "item = " + item;

    }
}
```







#### 12.7.2 LinkedList底层操作机制

```java
package com.level7.List_;

import java.util.LinkedList;

public class LinkedListSource {
    public static void main(String[] args) {
        LinkedList list = new LinkedList();
        list.add(10);
        list.add(15);
        list.add(20);
        list.add(1, 50);
        System.out.println(list);

        // 删除节点
        list.remove();  // 默认删除第一个节点
        System.out.println(list);

        // 修改某个节点
        Object origin = list.set(1, 100);  // 返回的是修改前的元素
        System.out.println(origin);
        System.out.println(list);

        // 得到节点对象
        Object o = list.get(2);
        System.out.println(o);
    }
}
```



**插入元素**

1、`LinkedList list = new LinkedList();`调用该语句后，执行构造器

![](C:\Users\XRJ\Desktop\202303171047811.png)

2、`list.add(10);`调用add方法后。会进入`linkLast(e)`这个方法

![](C:\Users\XRJ\Desktop\202303171048195.png)

3、在该方法中，首先让 l 指向当前链表的尾指针即last节点，然后构建新节点，他的pre指针就是 l ，next指针为空，item就是当前add的值。然后让last指针指向新的节点，如果 l 为空，表示当前链表为空，那么这个新节点就是链表第一个节点，就让first指针也指向这个节点。如果不是第一次add，则 l 指向上一个节点，一定不为空，然后 `l.next = newNode;`让上一个节点的next指针指向这个新节点

![](C:\Users\XRJ\Desktop\202303171048870.png)

4、这是`new Node<>(l, e, null)`构造器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171049708.png)





**指定位置插入元素**

1、`list.add(1, 50);`在第一个位置插入50这个元素，进入`public void add(int index, E element)`这个方法，首先判断当前插入的位置index是否合法，即`index >= 0 && index <= size;`如果不合法会抛出下标越界异常。然后判断如果`index == size`说明在末尾插入，否则执行else的语句

![](C:\Users\XRJ\Desktop\202303171140798.png)



2、进入node方法，首先找到插入的位置x是一个Node类型

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171142221.png)



3、进入`linkBefore`方法，`succ`就是需要插入的位置，然后将元素插入进去

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171142677.png)







**删除节点**

1、执行`list.remove();`默认删除第一个节点。执行该语句后进入`remove`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171418504.png)



2、判断first节点是否为空，如果为空就抛出异常

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171421742.png)



3、进入`private E unlinkFirst(Node<E> f)`，f为first节点，next为它的下一个节点，将要删除的这个f的item和next置为空，然后让first节点指向它的下一个节点，首节点就被删除了。如果next节点也为空，说明链表为空，那么让last也为空，否则就是让当前头结点的pre指针为空，然后返回删除的元素。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171419840.png)







### 12.8 ArrayList和LinkedList的比较

|            | 底层结构 | 增删的效率         | 改查效率 |
| ---------- | -------- | ------------------ | -------- |
| ArrayList  | 可变数组 | 较低，数组扩容     | 较高     |
| LinkedList | 双向链表 | 较高，通过链表追加 | 较低     |





### 12.9 Set

#### 12.9.1 Set接口基本介绍

1、无序，没有索引

2、不允许重复元素，最多一个null

3、Set遍历可以使用迭代器和增强for，不能使用下标遍历

```java
package com.level7.Set_;

import sun.font.EAttribute;

import java.util.HashSet;
import java.util.Iterator;
import java.util.Set;

public class Set01 {
    public static void main(String[] args) {
        Set set = new HashSet();
        set.add(2);
        set.add(2);
        set.add(3);
        set.add("jack");
        set.add(5);
        set.add("xrj");
        set.add("xrj");
        set.add("xyy");
        System.out.println(set);
        set.remove("xrj");
        System.out.println(set);


        // 迭代器遍历
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            Object next = iterator.next();
            System.out.println(next);
        }

        // 增强for遍历
        for (Object o : set) {
            System.out.println(o);
        }
        
        // set不能使用下标遍历
    }
}
```





#### 12.9.2  HashSet分析

1、`HashSet`实际上是`HashMap`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303171520284.png)

2、`HashSet`不保证元素是有序的，hash后再确定索引的值

3、不能有重复元素和对象

```java
package com.level7.Set_;

import java.util.HashSet;

public class HashSetSource {
    public static void main(String[] args) {
        HashSet set = new HashSet();
        // 添加成功返回true，添加失败，返回false
        System.out.println(set.add("xrj"));
        System.out.println(set.add("xrj"));
        System.out.println(set.add("xyy"));
        System.out.println(set.add("xyy"));
        System.out.println(set.add("abc"));

        set.remove("xrj");
        System.out.println(set);

        System.out.println("=========================");
        set = new HashSet();
        set.add("xrj"); // 添加成功
        set.add("xrj"); // 添加失败
        set.add(new Dog("xyy"));    // 添加成功
        set.add(new Dog("xyy"));    // 添加成功
        System.out.println(set);
        
        set.add(new String("abc")); // 添加成功
        set.add(new String("abc")); // 添加失败
        System.out.println(set);
    }
}

class Dog {
    private String name;

    public Dog(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                '}';
    }
}
```





#### 12.9.3 HashSet底层机制

> HashSet底层机制是HashMap，HashMap底层是数组+链表+红黑树

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181100758.png)

- 如果一条链表元素个数达到8，但是table大小没有达到64，会先对table进行扩容



```java
package com.level7.Set_;

import java.util.HashSet;

public class HashSource02 {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        hashSet.add("Java");
        hashSet.add("C++");
        hashSet.add("Java");
        System.out.println(hashSet);
    }
}
```



##### **1、第一次add**

1、`HashSet hashSet = new HashSet();`执行该语句，其实是创建了一个`HashMap`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181015244.png)



2、`hashSet.add("Java");`第一次执行add方法。实际调用的是`map.put`方法，里边的`PRESENT`是一个final类型的静态对象，默认为空。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181016624.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181024376.png)



3、`put`方法的`key`就是准备add的变量Java，`value`就是默认的`PRESENT`。调用`putVal`方法，传入的是key的hash值（传这个是为了在下次传入相同元素时做判断），key、value

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181017651.png)

​	3.1、首先计算key的`hash值`，如果`key`为空，他的hash值就是0，否则，他的hash值就是key的`hashCode`和它本身无符号右移16位做异或运算

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181017677.png)



4、进入`putVal`方法。`Node[] tab; Node p; int n, i;`这是定义辅助变量

​			`table`就是`HashMap`的一个数组，初始为`null`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181027587.png)

​	4.1 将`table`赋值给`tab`，如果`tab`为空或者他的长度为0，那么就进`resize()`方法

​	4.3 `resize()`后，现在`tab`就是大小为16的数组，n的大小为16，然后`if ((p = tab[i = (n - 1) & hash]) == null)`表示，让 i 指向当前元素应该存在在			数组的那个下标位置，p是当前下标位置的对象，如果为`null`，表示该位置没存放东西，就存放在这个位置。

​	4.4 `afterNodeInsertion(evict)`是一个空实现，留给`HashMap`的子类实现，最后返回null表示添加成功，然后第三点`put`方法返回null，到第二点的`add`方		法，如果返回null，表示add成功

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181020957.png)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181019083.png" style="zoom:80%;" />



​	4.2、进入`resize()`方法，让`oldTab`指向初始的table（初始为空），如果`oldTab`为空`oldCap = 0` 否则 `oldCap = oldTab.length`

​			`newCap`表示新的table长度，`newThr`相当于新的阈值（当table容量达到阈值大小就扩容），第一次时，`oldCap == 0`，进入`else`语句，

​			`newCap = DEFAULT_INITIAL_CAPACITY`，初始为16，表示第一次扩容大小为16，

​			`newThr = (int)(DEFAULT_LOAD_FACTOR) * DEFAULT_INITIAL_CAPACITY `，`DEFAULT_LOAD_FACTOR`默认为0.75，因此现在阈值为12，即超过12的大			小（即插入第13个元素时），数组就要继续扩容。然后创建大小为`newCap`的数组`newTab`，并赋给`table`，最后返回`newTab`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181028798.png)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181020983.png" style="zoom:80%;" />





##### **2、第二次add不同的值**

1、`hashSet.add("C++");`执行这条语句，首先执行add方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181206607.png)

2、计算hash然后执行put方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181206838.png)

3、执行`putVal`方法，此时table不为空，找到赋值的位置，加入进去，然后返回null

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181207422.png)





##### **3、add相同的值**

1、`hashSet.add("Java");`执行这条语句。首先执行add方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181209603.png)

2、计算完hash值后，执行put方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181209250.png)

3、执行`putVal`方法，此时table不为空，且当前元素为重复值，因此应该赋值的位置不为空，所以进入`else`。`p`是`p = tab[i = (n - 1) & hash]`通过这条语句找到的当前值应该插入的数组对应下标的对象，如果当前值的`hash`和`p.hash`相等，并且`(k = p.key) == key`表示是同一个对象或者`(key != null && key.equals(k)))`表示通过`equals`方法（该方法通过程序员自己指定）比较两个对象内容相同，那么就是和当前位置对象是相同的，就不能插入。如果不满足，进入`else if`，判断当前链表是否是红黑树，如果是红黑树，那么用红黑树方法查找。如果不是红黑树，进入`else`，此时表示就在当前这条链表上查找，`e = p.next`从第二个元素开始查找，如果下一个元素为空，那么将该元素插入他的后边，然后判断元素个数是否达到8，如果达到8（即插入了第9个元素后），并且table大小达到64，就转为红黑树。如果下一个元素不为空，就判断当前元素和链表下一个元素是否是同一个对象或者`equals`后是否相等，如果相等，插入失败，否则继续往后查询

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303181210727.png)





##### 4、HashSet扩容机制和转换红黑树机制

1、`HashSet`底层是`HashMap`，第一次添加时，table数组扩容到16，临界值（`threshold`）是16 × 加载因子（`loadFactor = 0.75`） = 12

2、如果table数组使用到了临界值12，就会扩容到 16 × 2 = 32，新的临界值就是32 × 0.75 = 24，以此类推。不管新增加的元素是增加到数组上，还是链表上，都要size++，size大于临界值就扩容。

3、在Java8中，如果一条链表的元素个数达到`TREEIFY_THRESHOLD`（默认是8），并且table的大小 >= `MIN_TREEIFY_CAPACITY`（默认64），就会进行树化（红黑树），否则仍采用数组扩容机制。

```java
package com.level7.Set_;

import java.util.HashSet;
import java.util.Objects;

public class HashSource03 {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
//        for(int i = 1; i <= 100; i++) {
//            hashSet.add(i);
//        }

        for(int i = 1; i <= 12; i++) {
            hashSet.add(new A(i));
        }
    }
}

class A {
    private int n;

    public A(int n) {
        this.n = n;
    }

    @Override
    public int hashCode() {
        return 10;
    }
}
```





##### 5、HashSet练习

> 1. 定义一个Employee对象放入HashSet
>
> 2. 当name和age的值相同时，认为是相同员工，不能添加到HashSet中
>
> 必须重写Employee的equals和hashCode方法，如果不重写hashCode方法，它他们的hash值是不同的就可以加入重复元素了



```java
package com.level7.Set_;

import java.util.HashSet;
import java.util.Objects;

public class HashSetExercise01 {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        hashSet.add(new Employee("xrj", 20));
        hashSet.add(new Employee("xrj", 20));
        hashSet.add(new Employee("xyy", 10));
        hashSet.add(new Employee("xyy", 10));
        hashSet.add(new Employee("x1", 30));
        hashSet.add(new Employee("x1", 30));
        hashSet.add(new Employee("x2", 40));
        hashSet.add(new Employee("x2", 40));
        System.out.println(hashSet);
    }
}

class Employee {
    private String name;
    private int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return age == employee.age && Objects.equals(name, employee.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```







#### 12.9.4 LinkedHashSet

1、`LinkedHashSet`是`HashSet`的子类

2、`LinkedHashSet`底层是一个`LinkedHashMap`，底层维护了一个`数组 + 链表 + 红黑树 + 双向链表`

3、`LinkedHashSet`根据元素的`hashCode`值来决定元素的存储位置，同时使用双向链表来维护元素的次序

4、`LinkedHashSet`不允许添加重复元素



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191436668.png)





##### 底层源码分析	

```java
package com.level7.Set_;

import java.util.LinkedHashSet;
import java.util.Set;

public class LinkedHashSetSource {
    public static void main(String[] args) {
        Set set = new LinkedHashSet();
        set.add(new String("xrj"));
        set.add(123);
        set.add(123);
        set.add(new People("xyy", 20));
        set.add(666);
        set.add("AAA");
        // 输出顺序和插入顺序是相同的
        System.out.println(set);
    }
}

class People {
    public String name;
    public int age;

    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```



1、`Set set = new LinkedHashSet();`执行这条语句，实际创建的是`LinkedHashMap`，它是`HashMap`的子类

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191440745.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202311051253902.png)

2、`set.add(new String("xrj"));`执行add方法，和`HashSet`一样，先执行`put`方法，然后计算完key的hash值，执行`putVal`方法，最后进入`putVal`方法执行

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191441796.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191443002.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191441840.png)



和`HashSet`不同点在于，这里`newNode`调用的是`LinkedHashMap`的`newNode`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211106604.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211107697.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211108347.png)

`LinkedHashMap`的`newNode`方法创建的是一个`Entry`，它是`Node`的子类，它当中有`before`和`after`节点，返回值类型为`Node`相当于向上转型。然后执行`linkNodeLast(p);` 该方法会使用`before`和`after`节点将这次加入的和上次加入的值连接在一起

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211110807.png)



3、`HashSet`加入的是`Node节点`，而`LinkedHashSet`加入的是`Entry`节点

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191448470.png)



`Entry`是`HashMap.Node`的子类，它里面多了`before`和`after`属性，分别代表他的前一个节点，和后一个节点，以此形成双向链表

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191448380.png)



在table表中可以看到，第一次添加的是`xrj`他在1号位置，他的before为空，因为他是第一个节点。

第二次添加的是`123`，可以看到`xrj`的`after节点`存的是`123`的位置，而`123`的`before节点`存的是`xrj`的位置。

因为他们两个的单链表都只有一个元素，因此`next`为空

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191450559.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191450264.png)

`LinkedHashMap`还有一个`head`和`tail`节点，存储第一个元素地址和最后一个元素地址，所以遍历的时候会按插入元素的顺序输出

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303191452965.png)









#### 12.9.5 TreeSet

> 可以根据指定规则进行排序

##### 1、无参构造

```java
package com.level7.Set_;

import java.util.TreeSet;

public class TreeSet_ {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet();
        treeSet.add("tom");
        treeSet.add("sp");
        treeSet.add("sp");
        treeSet.add("a");
        treeSet.add("abc");
        System.out.println(treeSet);
    }
}
```



1、` TreeSet treeSet = new TreeSet();`执行这条语句，实际创建的是一个`TreeMap`，且没有指定比较器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241127216.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241128315.png)



2、`treeSet.add("tom");`第一次执行`add`方法，调用了`TreeMap`的`put`方法，`key`就是要添加的元素，`value`为null

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241128800.png)

2.1 执行`put`方法，首先取到树的根节点，由于是第一次添加，因此`root`为`null`，然后就进入第一个`if`中，执行`compare(key, key); `。（注意此时`compare`比较的是两个相同的值，且没有用变量接收，它的作用就是用来判断添加的key是否为空）

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241130824.png)

2.2 进入到`compare`方法。因此此时没有传入比较器，所以`comparator`为`null`，就会调用默认的字符串比较方法（因为传入数据为字符串，如果传入的是int数据，他会调用`Intrger`的`compareTo`方法，因为在加入数据时，会自动装箱为`Integer`），且比较的`k1`和`k2`都是`tom`。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241131592.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241131948.png)

2.3 然后执行`root = new Entry<>(key, value, null);`创建一个树节点，他的`parent`为`null`，相当于他是根节点。然后返回`null`添加成功

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241132763.png)



3、`treeSet.add("sp");`  第二次`add`，此时进入`put`方法后，根节点`root`不为空，他不会进入第一个`if`中。`Comparator<? super K> cpr = comparator;`这条语句得到当前传入的比较器为`null`，会进入`else`中，先判断传入的`key`如果为空，抛出异常，然后利用字符串的比较方法，判断当前插入元素和根节点元素的大小，如果当前元素小于根节点就执行`t = t.left`，否则执行`t = t.right`，比根节点小就插入左边，比他大就插入右边。`Entry<K,V> e = new Entry<>(key, value, parent);`创建新的节点，当前节点的父节点就是`parent`，并让`parent.left = e`将他插入左子树

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241148967.png)



4、插入相同元素，`treeSet.add("sp");`	当插入相同元素，即通过`compare`方法判断相等，那么就会执行`t.setValue(value);`去修改值，因为这里是`TreeSet`，`value`都是默认的`null`，所以修改值相当于插入失败。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241339787.png)





##### 2、有参构造

```java
package com.level7.Set_;

import java.util.Comparator;
import java.util.TreeSet;

public class TreeSet_ {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet(new Comparator(){
            @Override
            public int compare(Object o1, Object o2) {
                return ((String)o1).compareTo((String) o2);
            }
        });

        treeSet.add("tom");
        treeSet.add("sp");
        treeSet.add("tom");
        treeSet.add("a");
        treeSet.add("abc");
        System.out.println(treeSet);
    }
}
```



1、`TreeSet treeSet = new TreeSet(new Comparator(){` 执行这条语句，创建了一个`TreeMap`，且比较器不为空

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241347352.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241347694.png)



2、`treeSet.add("tom");`  第一次添加元素，由于根节点为空

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241348541.png)

2.1 先执行`compare(key, key)`，此时传入的`comparator`不为空，会使用传入的这个比较器

![image-20230324134936735](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20230324134936735.png)

由于动态绑定机制，这个`compare`方法就是我们在创建`TreeSet`时传入的比较器，这里还是比较的字符串大小。如果字符串相等就无法插入，和无参一样，只不过每次比较使用的就是我们自定义的比较方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241349614.png)



```java
package com.level7.Set_;

import java.util.Comparator;
import java.util.TreeSet;

public class TreeSet_ {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet(new Comparator(){
            @Override
            public int compare(Object o1, Object o2) {
                // return ((String)o1).compareTo((String) o2);
                return ((String)o1).length() - ((String)o2).length();
            }
        });

        treeSet.add("tom");
        treeSet.add("abc");
        treeSet.add("sp");
        treeSet.add("a");
        treeSet.add("abc");
        System.out.println(treeSet);
    }
}
```

**如果传入的比较器比较的是两个字符串长度，那么当`tom`添加进去后，再去添加`abc`，由于比较器比较长度时两个字符串长度相等，会进行`value`的替换，因此`abc`无法添加进去**







### 12.10 Map接口

**Map接口实现类的特点**

1、`Map`用于保存具有映射关系的数据：`key-value`

2、`Map`中的`key`和`value`可以是任何引用类型的数据，会封装到`HashMap$Node`对象中

3、`Map`中的`key`不允许重复（原因和`HashSet`一样），`value`允许重复

5、`Map`的`key`可以为`null`，`value`也可以为`null`，但是`key`为`null`只能有一个，`value`为`null`可以有多个



```java
package com.level7.Map_;

import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.Set;

public class map01 {
    public static void main(String[] args) {
        Map map = new HashMap();
        map.put("xrj", "123");
        map.put("x2", "123");
        map.put("x3", "123");
        map.put("x4", null);
        map.put(null, null  );
        map.put("xrj", "456");
        System.out.println(map);
        System.out.println(map.get("xrj"));
    }
}
```







#### 12.10.1 Map底层存储

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211449855.png)

1、`key-value`实际是存放在`HashMap$Node node = newNode(hash, key, value, null);`

2、`key-value`为了方便程序员的遍历，还会创建`EntrySet`集合，该集合存放的元素类型为`Map.Entry<K, V>`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211428943.png)

3、`entrySet`中，定义的数据类型为`Map.Entry<K, V>`，但实际存放的数据类型还是`HashMap$Node`，因为`HashMap$Node`是`Map.Entry<K, V>`的子类，因此他可以存放进去

4、当把`HashMap$Node`对象存放到 `entrySet` 中就方便我们的遍历，因为`Map.Entry`提供了两个方法`getKey()`和`getValue()`，可以得到当前`Entry`的`key`和`value`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211437859.png)

5、在`entrySet`中，并没有创建新的数据，而是指向了`table`表中`HashMap$Node`的地址

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303211514646.png)

6、`HashMap`中还有`keySet`方法用于返回一个`Set`表示当前map所有的`key`，`vlaues`方法用于返回一个`Collection`表示当前map所有的`value`

```java
package com.level7.Map_;

import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.Set;

public class map01 {
    public static void main(String[] args) {
        Map map = new HashMap();
        map.put("xrj", "123");
        map.put("x2", "123");
        map.put("x3", "123");
        map.put("x4", null);
        map.put(null, null  );
        map.put("xrj", "456");
        System.out.println(map);
        System.out.println(map.get("xrj"));

        // entrySet()
        Set set = map.entrySet();
        for (Object o : set) {
            // System.out.println(o.getClass());   // class java.util.HashMap$Node
            Map.Entry entry = (Map.Entry) o;
            // 因为Map.Node是默认访问修饰符，因此不能访问
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }

        System.out.println(map.keySet().getClass());	// class java.util.HashMap$KeySet
        System.out.println(map.keySet());
        System.out.println(map.values().getClass());	// class java.util.HashMap$Values
        System.out.println(map.values());
    }
}
```





#### 12.10.2 Map常用方法

```java
package com.level7.Map_;

import java.util.HashMap;
import java.util.Map;

public class MapMethod {
    public static void main(String[] args) {
        Map map = new HashMap();
        map.put("xrj", "abc");
        map.put("xrj", "xyy");
        map.put("x1", "a1");
        map.put("x2", "a2");
        map.put("x3", "a3");
        System.out.println(map);

        // remove:根据键删除该键值对，也可以根据键值对删除
        map.remove("x3");
        System.out.println(map);

        // get: 根据键获取值
        System.out.println(map.get("xrj"));

        // size: 获取元素个数
        System.out.println(map.size());

        // isEmpty: 判断map是否为空
        System.out.println(map.isEmpty());

        // clear: 清空map
        // map.clear();
        // System.out.println(map);

        // containsKey: 查找键是否存在
        System.out.println(map.containsKey("xrj"));
        
        // containsValue: 查找值是否存在
        System.out.println(map.containsValue("a1"));
    }
}
```





#### 12.10.3 Map接口遍历方法

1、`containsKey`	查找键是否存在

2、`keySet`		获取所有的键

3、`entrySet`	获取所有键值对关系

4、`values`		获取所有的值

```java
package com.level7.Map_;

import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

public class Map_for {
    public static void main(String[] args) {
        Map map = new HashMap();
        map.put("xrj", "123");
        map.put("xyy", "456");
        map.put("abc", "789");
        map.put("zs", null);
        map.put(null, "aaa");

        // 1.先获取所有的key，然后通过key遍历map
        Set key = map.keySet();
        for (Object o : key) {
            System.out.println(o + " " + map.get(o));
        }

        // 迭代器遍历
        Iterator iterator = key.iterator();
        while (iterator.hasNext()){
            Object next = iterator.next();
            System.out.println(next + " " + map.get(next));
        }

        System.out.println("=============");
        // 2.通过entrySet遍历
        Set set = map.entrySet();
        for (Object o : set) {
            Map.Entry entry = (Map.Entry)o;
            System.out.println(entry.getKey() + " " + entry.getValue());
        }

        // 迭代器遍历
        Iterator iterator1 = set.iterator();
        while (iterator1.hasNext()) {
            Map.Entry entry = (Map.Entry)iterator1.next();
            System.out.println(entry.getKey() + " " + entry.getValue());
        }
    }
}
```





### 12.11 HashMap

1、与`HashSet`一样，`HashMap`不保证映射的顺序，因为底层是以hash表的方式来存储的（JDK8的`HashMap`底层是数组+链表+红黑树）

2、`HashMap`没有实现同步，因此是线程不安全的



#### 12.11.1 HashMap底层机制

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303221917883.png)



1、`HashMap`底层维护了`Node`类型的数组`table`，默认为`null`

2、当创建对象时，加载因子`loadfactor`初始化为0.75

3、当添加`key-val`时，通过`key`的哈希值得到在`table`的索引。然后判断该索引处是否有元素，如果没有元素就直接添加。如果该索引处有元素，继续判断该元素的`key`和准备加入的`key`相是否等，如果相等，则直接替换val；如果不相等需要判断是树结构还是链表结构，做出相应处理。如果添加时发现容量不够，则需要扩容。

4、第1次添加，则需要扩容table容量为16，临界值`threshold`为12(16*0.75)

5、以后再扩容，则需要扩容table容量为原来的2倍，临界值为原来的2倍，在Java8中，如果一条链表的元素个数超过`TREEIFY_THRESHOLD`(默认是8)，且`table`的大小>= `MIN_TREEIFY_CAPACITY`(默认64)，就会进行树化(红黑树)





#### 12.11.2 源码分析

```java
package com.level7.Map_;

import java.util.HashMap;

public class HashMapSource {
    public static void main(String[] args) {
        HashMap map = new HashMap();
        map.put("java", 10);
        map.put("php", 10);
        map.put("java", 20);    
    }
}
```



1、``HashMap map = new HashMap();`执行这句话，就创建一个`HashMap`，`loadFactor`初始化为0.75

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303221922143.png)

2、`map.put("java", 10);`第一次添加，进入`put`方法，计算`key`的hash值，然后调用`putVal`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303221924552.png)

3、进入`putVal`方法，因为是第一次添加，`table`为`null`，所以需要进入`resize`进入扩容，第一次扩容后大小为16。然后`p = tab[i = (n - 1) & hash]`通过这条语句，`i` 表示当前这个`k-v`应该插入`table`的位置，p 表示当前位置的对象，如果为空，表示当前位置未存放元素，可以直接插入。

`map.put("java", 20); `如果执行这条语句，`p = tab[i = (n - 1) & hash]`执行完这条语句后，p 不为null，说明p这个位置存有元素，进入`else`语句，如果当前插入的元素的`key`的hash值和p 的hash值相等 并且 插入元素的key 要么和p的key是同一个对象，要么`equal`后相等，说明当前插入元素的`key`已经存在，那么`value`需要进行替换，用e 保存当前位置原来的对象，进入`if (e != null)`中进行`value`的替换。

如果`if`的条件不满足，先进入`else if`中判断当前链表是否是树结构，如果是树结构，使用树的方式查找，如果不是，那么就还是链表结构，就一个一个遍历查找，如果找到最后为空，就将当前元素插入进入，同时，元素数量到达8个，即插入第9个元素（且table容量达到64）进行树化，如果遍历过程中，找到和当前插入元素的`key`相同的，就进行`value`的替换。

如果删除元素时，红黑树上元素个数小于8，会退化为链表

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303221924809.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303221925261.png)







### 12.12 Hashtable

1、存放的元素是键值对

2、`Hashtable`的键和值都不能为`null`，否则会抛出空指针异常

3、`Hashtable`使用方法和`HashMap`一样

4、`Hashtable`是线程安全的，`HashMap`是线程不安全的



#### 12.12.1 源码分析

```java
package com.level7.Map_;

import java.util.Hashtable;

public class HashTable01 {
    public static void main(String[] args) {
        Hashtable hashtable = new Hashtable();
        hashtable.put("x1", 123);
        hashtable.put("x2", 123);
        hashtable.put("x3", 123);
        hashtable.put("x4", 123);
        hashtable.put("x5", 123);
        hashtable.put("x6", 123);
        hashtable.put("x7", 123);
        hashtable.put("x8", 123);
        hashtable.put("x9", 123);
        hashtable.put("x0", 123);
    }
}
```



1、`Hashtable hashtable = new Hashtable();`执行这条语句。会创建一个`Hashtable`，初始化大小为11，加载因子为0.75

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303231504736.png)



2、`hashtable.put("x1", 123);`  第一次执行`put`，如果`value`为`null`就抛出空指针异常。然后通过`int hash = key.hashCode();`计算出当前`key`的hash值，如果`key`为null，这里会抛出异常，因此`key`也不可以为null，通过`int index = (hash & 0x7FFFFFFF) % tab.length;`计算出应该插入的位置。`Entry<K,V> entry = (Entry<K,V>)tab[index];`这条语句是得到当前要插入位置的对象，如果不为空，那么就进入`for`查找和当前插入元素的hash值相同并且`equals`后也相等（说明key是相同的），就进行`value`的替换，如果`entry`为空或者在`for`循环中没找到，就执行`addEntry(hash, key, value, index);`来添加新的元素

![](C:\Users\XRJ\Desktop\202303231505647.png)



3、`addEntry`方法中，首先判断当前容量是否达到了临界值，如果达到临界值，就执行`rehash()`方法进行扩容，否则，就创建新的`entry`然后添加进去。

`tab[index] = new Entry<>(hash, key, value, e);`这里传入的`e`是和当前插入元素hash值相同的链表的第一个节点，相当于头插法。

![](C:\Users\XRJ\Desktop\202303231529498.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303231511788.png)



4、`rehash()`方法，每次扩容为原始大小的2倍+1。`Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];`然后创建新的table数组。同时进入`for`循环，将旧的table中的数据复制到新的里边。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303231517863.png)









#### 12.12.2 Hashtable和HashMap对比

|           | 版本 | 线程安全 | 效率 | 允许null键null值 |
| --------- | ---- | -------- | ---- | ---------------- |
| HashMap   | 1.2  | 不安全   | 高   | 可以             |
| Hahstable | 1.0  | 安全     | 较低 | 不可以           |







### 12.13 Properties

1、`Properties`类继承自`Hashtable`类并实现了`Map`接口，也是使用键值对保存数据。他的`put`源码和`Hashtable`相同

2、`Properties`可以用于从`XXX.properties`文件中，加载数据到`Properties`类对象，进行读取和修改

3、`Properties`也不可以有空键和空值

```java
package com.level7.Map_;

import java.util.Properties;

public class Properties_ {
    public static void main(String[] args) {
        Properties properties = new Properties();
        properties.put("x1", 1);
        properties.put("x2", 2);
        properties.put("x3", 3);
        properties.put("x4", "400");
        properties.put(123, 4);
        System.out.println(properties);

        // 修改
        properties.put("x1", 123);
        System.out.println(properties);

        // 删除
        properties.remove("x3");
        System.out.println(properties);

        // 查找
        System.out.println(properties.get("x1"));
        System.out.println(properties.get(123));
        System.out.println(properties.getProperty("x4"));   // getProperty获取的key和value必须都是String
    }
}

```





### 12.14 TreeMap

> 可以将键按指定规则进行排序
>

#### 12.14.1 无参构造

```java
package com.level7.Map_;

import java.util.TreeMap;

public class TreeMap_ {
    public static void main(String[] args) {
        TreeMap treeMap = new TreeMap();
        treeMap.put("jack", "杰克");
        treeMap.put("tom", "汤姆");
        treeMap.put("kristina", "克瑞斯提诺");
        treeMap.put("smith", "斯密斯");
        treeMap.put("xrj", "徐睿杰");
    }
}
```



1、`TreeMap treeMap = new TreeMap();`	执行后会创建一个`TreeMap`，比较器为空

![](C:\Users\XRJ\Desktop\202303241357760.png)

2、`treeMap.put("jack", "杰克");`	执行后，直接进入`put`方法，因此第一次添加，所以根节点`root`为`null`，然后进入默认的`compare`方法来比较`key`的大小，然后创建节点添加进去

![](C:\Users\XRJ\Desktop\202303241358944.png)



3、第二次添加，进入`put`方法后，根节点不为空，但是传入的比较器为空，因此进入下边代码中，比较`key`的大小，如果相等，就进行值的替换。和`TreeSet`一样

![](C:\Users\XRJ\Desktop\202303241359434.png)





#### 12.14.2 有参构造

```java
package com.level7.Map_;

import java.util.Comparator;
import java.util.TreeMap;

public class TreeMap_ {
    public static void main(String[] args) {
        TreeMap treeMap = new TreeMap(new Comparator(){
            @Override
            public int compare(Object o1, Object o2){
                // return ((String)o1).compareTo((String)o2);
                return ((String)o1).length() - ((String)o2).length();
            }
        });
        treeMap.put("jack", "杰克");
        treeMap.put("tom", "汤姆");
        treeMap.put("kristina", "克瑞斯提诺");
        treeMap.put("smith", "斯密斯");
        treeMap.put("xrj", "徐睿杰");
        System.out.println(treeMap);
    }
}
```



1、`TreeMap treeMap = new TreeMap(new Comparator(){` 执行后会创建一个`TreeMap` 传入的有比较器

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241404919.png)

2、之后每次`add`调用的比较器就是我们传入的。

3、`treeMap.put("xrj", "徐睿杰");`当执行到这里时，因为我们传入的比较器是比较字符串长度大小，因此这个传入后，`key`比较后是相等的，无法插入，因此会进行`value`的替换

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303241407228.png)





### 12.15 集合的选择

1、`Collection`接口（单列对象）

​		允许重复：`List`

​				增删多：`LinkedList`【底层维护了一个双向链表】

​				改查多：`ArrayList`【底层维护了一个Object类型的可变数组】

​		不允许重复：`Set`

​				无序：`HashSet`【底层是HashMap，数组+链表+红黑树】

​				排序：`TreeSet`

​				插入和取出顺序一致：`LinkedHashSet`【底层是LinkedHashMap，数组+链表+双向链表】

2、`Map`（键值对）

​		键无序：`HashMap`【底层是：哈希表	jdk7：数组+链表	jdk8：数组+链表+红黑树】

​		键排序：`TreeMap`

​		键插入和取出顺序一致：`LinkedHashMap`

​		读取文件：`Properties`



**不管是`TreeSet`还是`TreeMap`，他们传入值时，`key`的类型必须相同，因为要进行`key`值的比较，如果类型不同无法比较会抛出异常**









### 12.16 Collections工具类

- `Collections`是一个操作`Set`、`List`和`Map`等集合的工具类
- `Collections`中提供了一系列的静态方法对集合元素进行排序、查询和修改等操作

```java
package com.level7.Collections_;

import java.util.*;

public class Collections_ {
    public static void main(String[] args) {
        List list = new ArrayList();
        list.add("tom");
        list.add("tom");
        list.add("smith");
        list.add("king");
        list.add("milan");
        System.out.println("初始: " + list);

        // 1.reverse: 反转List中元素顺序
        Collections.reverse(list);
        System.out.println("reverse: " + list);

        // 2.shuffle: 集合元素随机排序
        Collections.shuffle(list);
        System.out.println("shuffle: " + list);

        // 3.sort自然顺序排序
        Collections.sort(list);
        System.out.println("sort: " + list);

        // 4.指定排序规则
        Collections.sort(list, new Comparator(){
            @Override
            public int compare(Object o1, Object o2) {
                return ((String)o2).compareTo((String) o1);
            }
        });
        System.out.println("指定规则排序: " + list);

        // 5.元素交换
        Collections.swap(list, 1, 2);
        System.out.println("swap: " + list);

        // 6.max()，求自然排序的最大值
        Comparable max = Collections.max(list);
        System.out.println("自然排序max: " + max);

        // 7.max()，求指定规则排序的最大值
        Object max1 = Collections.max(list, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                return ((String) o2).length() - ((String) o1).length();
            }
        });
        System.out.println("指定排序max: " + max1);

        // 7.min() 返回自然排序最小值。指定排序的和max一样
        Comparable min = Collections.min(list);
        System.out.println("自然排序min: " + min);

        // 8.frequency(Collection, Object),求集合中Object出现的次数
        int cnt = Collections.frequency(list, "tom");
        System.out.println("frequency: " + cnt);

        // 9.copy() 集合拷贝
        ArrayList dest = new ArrayList();
        for (int i = 0; i < list.size(); i++) dest.add(0);
        Collections.copy(dest, list);   // 注意此时dest的长度一定要大于等于list的长度
        System.out.println("dest: " + dest);

        // 10.replaceAll(List list，Object oldVal，Object newVal) 用新值替换旧值
        Collections.replaceAll(list, "tom", "xrj");
        System.out.println("replaceAll: " + list);
    }
}
```





### 12.17 练习

```java
package com.level7.Homework;

import java.util.TreeSet;

public class homework04 {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet();
        // 类型转换错误，因为TreeSet没有传入比较器，因此使用的是添加的对象实现Comparable接口的compare方法，但是A对象没有实现，因此抛出异常
        // Comparable<? super K> k = (Comparable<? super K>) key；
        treeSet.add(new A(1, 20));
    }
}

class A{
    public int id;
    public int age;

    public A(int id, int age) {
        this.id = id;
        this.age = age;
    }
}
```





```java
package com.level7.Homework;

import java.util.HashSet;
import java.util.Objects;

public class homework05 {
    public static void main(String[] args) {
        HashSet set = new HashSet();
        Person p1 = new Person("AAA", 24);
        Person p2 = new Person("BBB", 25);
        set.add(p1);
        set.add(p2);
        p1.name = "CCC";
        set.remove(p1);
        /*
          此时什么也没删掉，因为Person的hashCode方法是根据name和age一起重写的，修改了p1的name为CCC后，p1在table表位置不变
          但是在remove时会根据p1的hashCode值来计算他在table表中的位置，此时该位置为空，即什么也没删除
         */
        System.out.println(set);

        set.add(new Person("CCC", 24)); // 可以添加进去，因为此时table中(CCC,24)的位置是根据(AAA,24)计算的
        System.out.println(set);

        // 可以添加进去,因此此时(AAA,24)在table中的位置和p1相同，但是p1的name被改为CCC，所以equals后不相等，会被挂在后边
        set.add(new Person("AAA", 24));
        System.out.println(set);
    }
}

class Person {
    public String name;
    public int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```







## 13 泛型

### 13.1 泛型的引入

> 在ArrayList中，添加三个Dog对象，通过Dog对象的get方法输出name和age

```java
package com.level8;

import java.util.ArrayList;

public class generic01 {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add(new Dog("dog1", 2));
        list.add(new Dog("dog2", 3));
        list.add(new Dog("dog3", 4));
        // 此时如果加入一个Cat对象，遍历时需要向下转型为Dog，因此发生ClassCastException
        // list.add(new Cat("cat", 1));

        for (Object o : list) {
            Dog dog = (Dog) o;
            System.out.println(dog.getName() + " " + dog.getAge());
        }
    }
}

class Dog {
    public String name;
    public int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

class Cat {
    public String name;
    public int age;

    public Cat(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Cat{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



**传统方法存在的问题**

- 不能对加入到集合`ArrayList`中的数据类型进行约束（不安全）

- 遍历的时候，需要进行类型转换，如果集合中数据量较大，对效率有影响



**使用泛型来解决**

```java
package com.level8;

import java.util.ArrayList;

public class generic02 {
    public static void main(String[] args) {
        ArrayList<Dog> list = new ArrayList<Dog>();
        list.add(new Dog("x1", 1));
        list.add(new Dog("x2", 2));
        list.add(new Dog("x3", 3));
        // list.add(new Cat("c1", 2));  报错，类型不一致无法添加
        for (Dog dog : list) {
            System.out.println(dog.getName() + " " + dog.getAge());
        }
    }
}
```





### 13.2 泛型的理解与好处

#### 13.2.1 泛型的好处

- 编译时，检查添加元素的类型，提高了安全性
- 减少了类型转换的次数，提高效率
  - 不使用泛型：Dog 加入 ==>  Object取出 ==>  Object	放入到ArrayList后会先转为Object，在取出时，还需要转为Dog
  - 使用泛型：Dog ==> Dog ==> Dog	 放入和取出不需要类型转换





#### 12.2.2 泛型的介绍

1、泛型又称为参数化类型，是JDK5出现的新特性，解决数据类型的安全性问题

2、在类声明或实例化时只要指定好需要的具体类型即可

3、Java泛型可以保证如果程序在编译时没有警告，运行时就不会产生`ClassCastException`异常

**4、泛型的作用：可以在类声明时通过一个标识表示类中某个属性的类型，或者是某个方法的返回值类型，或者是参数类型**

```java
package com.level8;

public class generic03 {
    public static void main(String[] args) {
        Person<String> p1 = new Person<String>("xrj");
        System.out.println(p1.f());
        p1.show();  // class java.lang.String

        Person<Integer> p2 = new Person<Integer>(123);
        System.out.println(p2.f());
        p2.show();  // class java.lang.Integer
    }
}

class Person<E> {
    E s;

    public Person(E s) {
        this.s = s;
    }

    public E f() {
        return s;
    }

    public void show() {
        System.out.println(s.getClass());
    }
}
```





### 13.3 泛型的使用

1、泛型声明。`interface 接口<T>{}`和`class 类<K,V>{}`

2、其中T、K、V不代表值，而代表类型

3、泛型的实例化`List<String> list = new ArrayList<String>();`

```java
package com.level8;

import java.util.*;

public class genericExercise01 {
    public static void main(String[] args) {
        HashSet<Student> set = new HashSet<Student>();
        Student student1 = new Student("张三", 20);
        Student student2 = new Student("李四", 24);
        Student student3 = new Student("王五", 18);
        Student student4 = new Student("赵六", 22);
        set.add(student1);
        set.add(student2);
        set.add(student3);
        set.add(student4);

        // 增强for
        for (Student s : set) {
            System.out.println(s.getName() + " " + s.getAge());
        }

        // 迭代器
        System.out.println("====================");
        Iterator<Student> iterator = set.iterator();
        while (iterator.hasNext()) {
            Student s = iterator.next();
            System.out.println(s.getName() + " " + s.getAge());
        }


        HashMap map = new HashMap<String, Student>();
        map.put(student1.getName(), student1);
        map.put(student2.getName(), student2);
        map.put(student3.getName(), student3);
        map.put(student4.getName(), student4);

        // entrySet
        // public Set<Map.Entry<K,V>> entrySet() {
        Set<Map.Entry<String, Student>> entry = map.entrySet();

        // 增强for
        System.out.println("===================");
        for (Map.Entry<String, Student> e : entry) {
            System.out.println(e.getKey() + " " + e.getValue());
        }

        // 迭代器
        System.out.println("====================");
        Iterator<Map.Entry<String, Student>> iterator1 = entry.iterator();
        while (iterator1.hasNext()) {
            Map.Entry<String, Student> e = iterator1.next();
            System.out.println(e.getKey() + " " + e.getValue());
        }

        // keySet
        Set<String> keySet = map.keySet();

        // 增强for
        System.out.println("====================");
        for (String key : keySet) {
            System.out.println(key + " " + map.get(key));
        }

        // 迭代器
        System.out.println("====================");
        Iterator<String> iterator2 = keySet.iterator();
        while (iterator2.hasNext()) {
            String key = iterator2.next();
            System.out.println(key + " " + map.get(key));
        }

        // values
        Collection<Student> values = map.values();

        // 增强for
        System.out.println("=====================");
        for (Student s : values) {
            System.out.println(s);
        }

        // 迭代器
        System.out.println("======================");
        Iterator<Student> iterator3 = values.iterator();
        while (iterator3.hasNext()) {
            System.out.println(iterator3.next());
        }
    }
}

class Student {
    public String name;
    public int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```







### 13.4 泛型使用细节

1、`interface List<T>{}`，`public class HashSet<E>{}`等，T和E只能是引用类型

2、在给泛型指定具体类型后，可以传入该类型或其子类型

3、泛型使用形式

​	`List<Integer> list1 = new ArrayList<Integer>();`

​	`List<Integer> list2 = new ArrayList<>();`	后边的类型可以省略不写（只可以省略后边）

3、`List list3 = new ArrayList();`	默认给他的泛型是`Object`

```java
package com.level8;

import java.util.ArrayList;

public class genericDetail {
    public static void main(String[] args) {
        // 1.泛型参数只能是引用类型
        ArrayList<Integer> list = new ArrayList();

        // 2.在给泛型指定具体类型后，可以传入该类型或其子类型
        Pig<A> pig = new Pig(new A());
        pig.f();
        Pig<A> pig1 = new Pig<A>(new B());
        pig1.f();

        // 后边不指定泛型类型，这样可以传入C，E也就是C类型
        Pig<A> pig2 = new Pig(new C());
        pig2.f();
        
        // 但如果后边加上<>就必须是A或者A的子类
        Pig<A> pig3 = new Pig<>(new B());
        pig3.f();
    }
}

class A {}
class B extends A{}
class C{}

class Tiger<E> {
    E e;
    public Tiger() {}
    public Tiger(E e) {
        this.e = e;
    }
}

class Pig<E> {
    E e;
    public Pig(E e) {
        this.e = e;
    }
    public void f() {
        System.out.println(e.getClass()); //运行类型
    }
}
```





### 13.5 自定义泛型类

`class 类名<T, R...> {`

 `成员`

 `} `

1、普通成员可以使用泛型（属性，方法）

2、使用泛型的数组，不能初始化

3、静态方法中不能使用类的泛型，但是可以使用自己的泛型`public static<M > void m1(M m){}`

4、泛型类的类型，是在创建对象时确定的（因为创建对象时，需要指定确定类型）

5、如果在创建对象时，没有指定类型，默认为`Object`

```java
package com.level8;

import java.util.Arrays;

public class CustomGeneric {
    public static void main(String[] args) {
        tiger<String, Integer, Double> tiger = new tiger<>("xrj");
        tiger.setM(12.2);
        tiger.setR(15);
        tiger.setT("123");
        String arr[] = {"a", "b", "c"};
        tiger.setArr(arr);
        System.out.println(tiger);
    }
}

class tiger<T, R, M>{
    String name;
    T t;
    R r;
    M m;
    T[] arr;

    public tiger(String name) {
        this.name = name;
    }

    public tiger(String name, T t, R r, M m, T[] arr) {
        this.name = name;
        this.t = t;
        this.r = r;
        this.m = m;
        this.arr = arr;
    }

    // 静态成员时在类加载时初始化的，类加载时还没有创建对象，因此类型也没有指定
    // static R r2;
    // public static void m1(M m){}

    @Override
    public String toString() {
        return "tiger{" +
                "name='" + name + '\'' +
                ", t=" + t +
                ", r=" + r +
                ", m=" + m +
                ", arr=" + Arrays.toString(arr) +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }

    public R getR() {
        return r;
    }

    public void setR(R r) {
        this.r = r;
    }

    public M getM() {
        return m;
    }

    public void setM(M m) {
        this.m = m;
    }

    public T[] getArr() {
        return arr;
    }

    public void setArr(T[] arr) {
        this.arr = arr;
    }
}
```





### 13.6 自定义泛型接口

`interface 接口名<T, R...>{}`

1、接口中，静态成员也不能使用泛型

2、泛型接口的类型，在继承接口或者实现接口时确定

3、没有指定类型，默认为`Object`

```java
package com.level8;

public class CustomInterfaceGeneric {
    public static void main(String[] args) {

    }
}

interface IA extends IUSB<Integer, String> {}

// 当我们实现IA接口时，由于IA接口继承IUSB接口并且指定了泛型的类型，因此将U替换为Integer，将B替换为String
class AA implements IA {

    @Override
    public Integer get(Integer integer) {
        return null;
    }

    @Override
    public void hi(String s) {

    }
}

class BB implements IUSB<Double, String> {

    @Override
    public Double get(Double aDouble) {
        return null;
    }

    @Override
    public void hi(String s) {

    }
}

interface IUSB<U, B>{
    int n = 10;
    // 接口中的属性都是public static final修饰的，不能使用泛型类型
    // U name;

    // 普通方法可以使用泛型
    U get(U u);

    void hi(B b);

    // jdk8之后，可以在接口中，使用默认方法，可以使用泛型
    default B method(U u) {
        return null;
    }
}
```







### 13.7 自定义泛型方法

`修饰符<T, R> 返回类型 方法名(参数列表){}`

1、泛型方法，可以定义在普通类中，也可以定义在泛型类中

2、当泛型方法被调用时，类型会确定

3、`public void hi(E e){}`	修饰符后没有`<T, R>`	该方法不是泛型方法，而是使用了泛型

```java
package com.level8;

public class CustomMethodGeneric {
    public static void main(String[] args) {
        Car car = new Car();
        car.fly(12, "xrj");
        car.fly("xyy", 1.234);

        Fish<String, Integer> fish = new Fish<>();
        fish.hello(12, 'c');
    }
}

// 普通类
class Car {
    public void run(){}

    public<T, R> void fly(T t, R r){
        System.out.println(t.getClass());
        System.out.println(r.getClass());
    }
}

// 泛型类
class Fish<T, R> {
    // 普通方法
    public void run(){}

    // 泛型方法
    public<U, M> void eat(U u, M m){}

    // hi不是泛型方法，只是使用了泛型
    public void hi(T t){};

    // 泛型方法，可以使用类声明的泛型，也可以使用自己声明的泛型
    public<K> void hello(R r, K k){
        System.out.println(r.getClass());
        System.out.println(k.getClass());
    }
}
```





### 13.8 泛型的继承和通配符

1、泛型不具有继承性

`List<Object> list = new ArrayList<String>();	不对`

2、`<?>`：支持任意泛型类型

3、`<? extends A>`：支持A类以及A类的子类，规定了泛型的上限

4、`<? super A>`：支持A类以及A类的父类，不限于直接父类，规定了泛型的下限

```java
package com.level8;

import java.util.ArrayList;
import java.util.List;

public class GenericExtends {
    public static void main(String[] args) {
        // 错误
        // ArrayList<Object> list = new ArrayList<String >();

        ArrayList<Object> list1 = new ArrayList<>();
        ArrayList<String> list2 = new ArrayList<>();
        ArrayList<x> list3 = new ArrayList<>();
        ArrayList<y> list4 = new ArrayList<>();
        ArrayList<z> list5 = new ArrayList<>();

        printCollection1(list1);
        printCollection1(list2);
        printCollection1(list3);
        printCollection1(list4);
        printCollection1(list5);

        // printCollection2(list1); 错误
        // printCollection2(list2); 错误
        printCollection2(list3);
        printCollection2(list4);
        printCollection2(list5);

        printCollection3(list1);
        // printCollection3(list2);    错误
        printCollection3(list3);
        // printCollection3(list4);    错误
        // printCollection3(list5);    错误
    }

    // List<?> 表示 任意的泛型类型都可以接受
    public static void printCollection1(List<?> c) {
        for (Object object : c) { // 通配符，取出时，就是 Object
            System.out.println(object);
        }
    }

    // 支持x类以及x类的子类
    public static void printCollection2(List<? extends x> c) {
        for (Object object : c) {
            System.out.println(object);
        }
    }

    // ? super 子类类名 x: 支持 x 类以及 x 类的父类，不限于直接父类，
    public static void printCollection3(List<? super x> c) {
        for (Object object : c) {
            System.out.println(object);
        }
    }
}

class x{}
class y extends x{}
class z extends y{}
```





### 13.9 Junit

1、一个类有很多功能代码需要测试，为了测试就要写到`main`方法，如果有多个代码测试，就要来回注释

2、`Junit`是一个`Java`语言的单元测试框架

```java
package com.level8;

import org.junit.Test;

/**
 * @author XRJ
 * @version 1.0
 * @Data 2023/3/29
 */
public class Junit_ {
    public static void main(String[] args) {
        // new Junit_().m1();
        new Junit_().m2();
    }

    @Test
    public void m1() {
        System.out.println("m1被调用");
    }

    @Test
    public void m2() {
        System.out.println("m2被调用");
    }
}
```







## 14 坦克大战

### 14.1 Java绘图

> 下图说明了Java绘图坐标系。坐标原点位于左上角，以像素为单位

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202303301122695.png" style="zoom:80%;" />





#### 14.1.1 绘图原理

`Component`类提供了两个和绘图相关的重要方法

1、`paint(Graphics g)`绘制组件的外观

2、`repaint()` 刷新组件的外观



当组件第一次在屏幕显示的时候，程序会自动调用`paint()`方法来绘制组件

在以下情况`paint()`也会被调用

1、窗口最小化，再最大化

2、窗口大小发生变化

3、`repaint()`方法被调用

```java
package com.level9.draw;

import javax.swing.*;
import java.awt.*;

public class DrawCircle extends JFrame{ // JFrame 对应窗口,可以理解成是一个画框

    private  MyPanel mp = null; // 定义一个画板

    public static void main(String[] args) {
        new DrawCircle();
    }

    public DrawCircle() {
        // 初始化画板
        mp = new MyPanel();
        // 把画板放到画框
        this.add(mp);
        // 设置窗口大小
        this.setSize(400, 300);
        // 当点击窗口的×，程序退出
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        // 可以显示
        this.setVisible(true);
    }
}

// 先定义一个 MyPanel, 继承 JPanel 类， 画图形，就在面板上画
class MyPanel extends JPanel {
    // 1.MyPanel是一个画板
    // 2.Graphics g就是一个画笔
    // 3.Graphics提供了很多画图方法
    @Override
    public void paint(Graphics g) {
        super.paint(g); // 调用父类方法完成初始化
        System.out.println("paint方法被调用");
        // 画一个圆
        g.drawOval(50, 50, 100, 100);
    }
}
```





#### 14.1.2 Graphics类

**Graphics类的常用方法**

1、画直线			`g.drawLine(10, 10, 100, 100);`

2、画矩形边框	`g.drawRect(10, 10, 50, 50);`

3、画椭圆边框	`g.drawOval(50, 50, 100, 200);`

4、填充矩形		`g.fillRect(10, 10, 50, 50);`

5、填充椭圆		`g.fillOval(10, 10, 100, 200);`

6、画图片			`g.drawImage(image, 50, 50, 1124, 800, this);`

7、画字符串		`g.drawString("xrj123", 100, 100);   // 这里(100,100)是左下角`

8、设置画笔字体	`g.setFont(new Font("隶书", Font.BOLD, 50));`

9、设置画笔颜色	`g.setColor(Color.BLUE);`

```java
package com.level9.draw;

import javax.swing.*;
import java.awt.*;

public class DrawCircle extends JFrame{ // JFrame 对应窗口,可以理解成是一个画框

    private  MyPanel mp = null; // 定义一个画板

    public static void main(String[] args) {
        new DrawCircle();
    }

    public DrawCircle() {
        // 初始化画板
        mp = new MyPanel();
        // 把画板放到画框
        this.add(mp);
        // 设置窗口大小
        this.setSize(400, 300);
        // 当点击窗口的×，程序退出
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        // 可以显示
        this.setVisible(true);
    }
}

// 先定义一个 MyPanel, 继承 JPanel 类， 画图形，就在面板上画
class MyPanel extends JPanel {
    // 1.MyPanel是一个画板
    // 2.Graphics g就是一个画笔
    // 3.Graphics提供了很多画图方法
    @Override
    public void paint(Graphics g) {
        super.paint(g); // 调用父类方法完成初始化
        System.out.println("paint方法被调用");
        // 画一个圆
        // g.drawOval(50, 50, 100, 100);

        // 画直线
        // g.drawLine(10, 10, 100, 100);

        // 画矩形边框
        // g.drawRect(10, 10, 50, 50);

        // 画椭圆边框
        // g.drawOval(50, 50, 100, 200);

        // 填充矩形
        // g.setColor(Color.BLUE);
        // g.fillRect(10, 10, 50, 50);

        // 填充椭圆
        // g.setColor(Color.red);
        // g.fillOval(10, 10, 100, 200);

        // 画图片
        // Image image = Toolkit.getDefaultToolkit().getImage("D:\\XRJ\\Java\\code\\JavaBase\\out\\production\\JavaBase\\1.png");   // 获取图片资源
        // g.drawImage(image, 50, 50, 1124, 800, this);

        // 画字符串
        g.setColor(Color.GREEN);
        g.setFont(new Font("隶书", Font.BOLD, 50));
        g.drawString("xrj123", 100, 100);   // 这里(100,100)是左下角

    }
}
```







### 14.2 事件监听器

> java事件处理是采取 委派事件模型 。当事件发生时，会产生事件的对象，此 信息 会传递给 事件的监听者处理，这里所说的信息就是java.awt.event事件类库里某个类所创建的对象，把它称为 事件的对象

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303301613007.png)



1、事件源：事件源是一个产生事件的对象，比如按钮，窗口等

2、事件：事件就是承载事件源状态改变时的对象，比如键盘事件，鼠标事件，窗口事件等，会生成一个事件对象，该对象保存着当前事件很多信息，比如`KeyEvent`对象有含有被按下的`Code`值

3、事件类型

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303301616148.png)

4、事件监听器接口

​	（一）当事件源产生一个对象，可以传送给事件监听者处理

​	（二）事件监听者实际上就是一个类，该类实现了某个事件监听器接口。比如我们的`MyPanel`类就实现了`KeyListener`接口，他可以作为一个事件监听者，对				监听到的事件进行处理

​	（三）事件监听器接口有很多种，不同的事件监听器接口可以监听不同的事件，一个类可以实现多个监听接口

```java
package com.level9.event;

import javax.swing.*;
import java.awt.*;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;

public class ballMove extends JFrame{
    private MyPanel mp;
    public static void main(String[] args) {
        new ballMove();
    }

    public ballMove() {
        mp = new MyPanel();
        this.add(mp);
        // 窗口JFame对象可以监听键盘事件
        this.addKeyListener(mp);
        this.setSize(400, 300);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        this.setVisible(true);
    }
}

// KeyListener是一个监听器，监听键盘的事件
class MyPanel extends JPanel implements KeyListener {

    // 为了让小球可以移动，将坐标设置为变量
    int x = 50, y = 50;
    @Override
    public void paint(Graphics g) {
        super.paint(g);
        g.fillOval(x, y ,20 ,20);
    }

    // 有字符输出时，该方法就会触发
    @Override
    public void keyTyped(KeyEvent e) {

    }

    // 当某个键被按下，该方法触发
    @Override
    public void keyPressed(KeyEvent e) {
        // System.out.println((char) e.getKeyCode() + "被按下..");
        // 根据用户按下的不同键，来处理小球的移动
        // 在java中会对每一个键，分配一个值
        if(e.getKeyCode() == KeyEvent.VK_DOWN)     // VK_DOWN就是向下箭头对应的code
            y++;
        else if(e.getKeyCode() == KeyEvent.VK_UP)
            y--;
        else if(e.getKeyCode() == KeyEvent.VK_RIGHT)
            x++;
        else if (e.getKeyCode() == KeyEvent.VK_LEFT)
            x--;
        this.repaint();
    }

    // 当某个键释放(被松开)，该方法触发
    @Override
    public void keyReleased(KeyEvent e) {

    }
}
```







## 15 多线程

> 线程是进程创建的，是进程的一个实体
>
> 一个进程可以拥有多个线程
>
> 单线程：同一时刻，只允许执行一个线程
>
> 多线程：同一时刻，可以执行多个线程，比如一个迅雷进程，可以同时下载多个文件
>
> 并发：同一时刻，多个任务交替执行，造成一种 "貌似同时" 的错觉
>
> 并行：同一时刻，多个任务同时执行。多核cpu可以实现并行



### 15.1 线程的使用

**创建线程的两种方式**

1、继承`Thread`类，重写`run`方法

2、实现`Runnable`接口，重写`run`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202303311611790.png)





### 15.2 继承Thread

> 1、编写一个程序，开启一个线程，每隔一秒，控制台输出 ” 喵喵，我是小猫咪 “
>
> 2、使用jconsole监控线程执行情况

```java
package com.level10.threadUse;

import java.sql.Time;

public class thread01 {
    public static void main(String[] args) throws InterruptedException{
        Cat cat = new Cat();
        cat.start();

        System.out.println("主线程执行");
        for (int i = 0; i < 60; i++) {
            System.out.println(i + " 主线程名字: " + Thread.currentThread().getName());
            Thread.sleep(1000);
        }
    }
}

// 当一个类继承了Thread，该类就可以作为线程使用
// 重写run方法，实现自己的逻辑
// Thread的run方法也是实现了Runnable接口的run方法
class Cat extends Thread{
    int cnt = 0;
    public void run() {
        while (cnt < 80) {
            System.out.println("喵喵, 我是小猫咪" + cnt++ + " 线程名字: " + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 当该程序启动后，就会创建一个进程，进程默认有一个main线程，然后执行main线程，在main线程中创建了一个子线程Thread0，然后main线程和Thread0线程就会并发执行，通过jconsole可以看到，main线程和Thread0线程交替执行，当main线程结束后，Thread0线程还没有结束，当Thread0线程结束后，程序才会退出

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304011050160.png)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202304011049450.png" style="zoom:90%;" />







**源码分析**

```java
package com.level10.threadUse;

import java.sql.Time;

public class thread01 {
    public static void main(String[] args) throws InterruptedException{
        Cat cat = new Cat();
        cat.start();  // 开启一个线程，最终会执行run方法
        // cat.run();  // 不会开启线程，只是调用一个run方法，run方法执行完毕后，才会执行下边代码，当前只有一个main线程
        System.out.println("主线程执行");
        for (int i = 0; i < 60; i++) {
            System.out.println(i + " 主线程名字: " + Thread.currentThread().getName());
            Thread.sleep(1000);
        }
    }
}

// 当一个类继承了Thread，该类就可以作为线程使用
// 重写run方法，实现自己的逻辑
// Thread的run方法也是实现了Runnable接口的run方法
class Cat extends Thread{
    int cnt = 0;
    public void run() {
        while (cnt < 80) {
            System.out.println("喵喵, 我是小猫咪" + cnt++ + " 线程名字: " + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 如果直接执行`cat.run()`方法，并不会启动线程，而是执行一个普通run方法，通过jconsole可以查看当前也只有一个main进程，run方法执行完毕后才会执行后边代码。而执行`cat.start()`方法，才是真正开启一个线程，他会由JVM调用start0方法，实现多线程



1、`cat.start();` 执行该方法后，会进入`start()`方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304011104323.png)



2、执行`start()`方法后，会进入`start0()`方法，这时才是真正实现多线程的地方。`start0()`是本地方法，是由`JVM`调用的，底层是`C/C++`实现

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304011104640.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304011135671.png)







### 15.3 实现Runnable接口

> Java是单继承的，在某些情况下一个类可能已经继承了某个父类，这时就不能通过继承Thread类来创建线程了
>
> 因此需要通过实现Runnable接口来创建线程



> Dog类实现了Runnable后就是一个线程类，因为Runnable接口只有一个run方法，无法调用start方法启动线程，所以需要创建一个Thread对象，将dog放入Thread对象中，然后调用start方法实现多线程。这里使用了设计模式【代理模式】

```java
package com.level10.threadUse;

public class Thread02 {
    public static void main(String[] args) {
        Dog dog = new Dog();
        // dog.start();    实现Runnable后，线程没有start方法
        // 创建Thread对象，把dog对象(实现了Runnable)放入Thread
        Thread thread = new Thread(dog);
        thread.start();

        System.out.println("主线程执行");
        for (int i = 0; i < 60; i++) {
            System.out.println(i + " 主线程名: " + thread.currentThread().getName());
            try {
                thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Dog implements Runnable {

    @Override
    public void run() {
        int cnt = 0;
        while (cnt < 60) {
            System.out.println("小狗汪汪叫 " + cnt++ + " 线程名: " + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304011148457.png)





**模拟实现Runnable接口开发线程机制**

> 创建tiger对象(实现了Runnable接口)，将其传入threadProxy中，threadProxy的构造器接收的是Runnable的对象，因此可以传入，调用threadProxy的start方法，就去调用start0方法，start0调用run方法，根据动态绑定机制，就会调用tiger的run方法

```java
package com.level10.threadUse;

public class Thread03 {
    public static void main(String[] args) {
        Tiger tiger = new Tiger();
        ThreadProxy threadProxy = new ThreadProxy(tiger);
        threadProxy.start();
    }
}

class Animal {}

class Tiger extends Animal implements Runnable {

    @Override
    public void run() {
        System.out.println("老虎叫");
    }
}

// 线程代理类，模拟Thread类
class ThreadProxy implements Runnable {
    private Runnable target = null;

    @Override
    public void run() {
        if (target != null)
            target.run();
    }

    public void start(){
        start0();
    }

    public void start0() {
        run();
    }

    public ThreadProxy(Runnable target) {
        this.target = target;
    }
}
```







### 15.4 继承Thread VS 实现Runnable接口

1、从Java的设计来看，继承`Thread`和实现`Runnable`接口来创建线程本质上没有任何区别，因为`Thread`类本身就实现了`Runnable`接口

2、实现`Runnable`接口方式更加适合多个线程共享一个资源的情况，并且避免了单继承的限制





### 15.5 线程终止

1、当线程完成任务后，会自动退出

2、还可以使用变量来控制`run`方法退出的方式停止线程，即**通知方式**

```java
package com.level10.exit;

public class ThreadExit {
    public static void main(String[] args) throws InterruptedException {
        T t = new T();
        new Thread(t).start();

        // 主线程休息10秒后，终止子线程
        Thread.sleep(10000);
        t.setLoop(false);
    }
}

class T implements Runnable {
    private boolean loop = true;
    @Override
    public void run() {
        while (loop) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("线程 " + Thread.currentThread().getName() + " 正在运行");
        }
    }

    public void setLoop(boolean loop){
        this.loop = false;
    }
}
```





### 15.6 线程常用方法

1、`setName`				设置线程名称，使之与参数name相同

2、`getName`				返回线程名称

3、`start`					使该线程开始执行，Java虚拟机底层调用该线程的`start0`方法

4、`run`						调用线程对象`run`方法

5、`setPriority`		更改线程优先级

6、`getPriority`		获取线程优先级

7、`sleep`					在指定的毫秒数内让当前正在执行的线程休眠

8、`interrupt`			中断线程



> 1、start底层会创建新的线程，调用run。而run本身是一个普通的方法调用，不会启动新的线程
>
> 2、interrupt，中断线程，但并没有结束线程。一般用于中断正在睡眠的线程
>
> 3、sleep。线程的静态方法，使当前线程休眠
>
> 4、线程优先级最大为10，最小为1

```java
package com.level10.Method;

public class ThreadMethod01 {
    public static void main(String[] args) throws InterruptedException {
        A a = new A();
        a.setName("xrj");
        System.out.println("线程名字 = " + a.getName());	// xrj
        a.setPriority(Thread.MAX_PRIORITY);
        System.out.println("线程优先级 = " + a.getPriority());	// 10

        a.start();

        System.out.println("main线程优先级 = " + Thread.currentThread().getPriority());	// 5

        Thread.sleep(5000);
        a.interrupt();
    }
}

class A extends Thread {

    @Override
    public void run() {
        while (true) {
            for (int i = 0; i < 100; i++) {
                System.out.println(Thread.currentThread().getName() + "  吃包子  " + i);
            }
            try {
                System.out.println("休眠中...");
                Thread.sleep(20000);
            } catch (InterruptedException e) {
                // 当线程执行到一个interrupt方法时，会catch一个异常
                System.out.println(Thread.currentThread().getName() + " 被interrupt了");
            }
        }
    }
}
```





#### 15.6.1 线程插队

1、`yield`：线程礼让。让出cpu，让其他线程执行，在cpu资源充足的时候，不需礼让，只有在cpu资源不够的时候，礼让才有效

2、`join`：线程的插队。插队的线程一旦插队成功，则先执行完插入线程的所有任务。

```java
package com.level10.Method;

public class ThreadMethod02 {
    public static void main(String[] args) throws InterruptedException {
        T t = new T();
        t.start();

        for (int i = 1; i <= 20; i++) {
            Thread.sleep(1000);
            System.out.println("主线程吃了 " + i + " 个包子");
            if (i == 5) {
                System.out.println("主线程吃了5个包子，优先让子线程吃");
                // Thread.yield(); // 优先让子线程吃包子，但是cpu资源充足，不会礼让
                t.join();   // 子线程插队，子线程执行完，主线程才会继续执行
                System.out.println("子线程吃完了，主线程继续吃");
            }
        }
    }
}

class T extends Thread {
    @Override
    public void run(){
        for (int i = 1; i <= 20; i++) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("子线程吃了 " + i + " 个包子");
        }
    }
}
```





### 15.7 用户进程和守护进程

1、用户进程：也叫工作线程，当线程任务执行完毕或者以通知方式结束

2、守护进程：一般是为工作线程服务的，当所有的用户线程结束，守护线程自动结束

3、常见守护线程：垃圾回收机制

```java
package com.level10.Daemon;

public class DaemonThread {
    public static void main(String[] args) throws InterruptedException {
        MyDaemonThread myDaemonThread = new MyDaemonThread();
        myDaemonThread.setDaemon(true); // 将子线程设置为守护线程，主线程结束，守护线程也结束
        myDaemonThread.start();
        for (int i = 1; i <= 10; i++) {
            Thread.sleep(500);
            System.out.println("xrj辛苦的工作 " + i);
        }
    }
}

class MyDaemonThread extends Thread {
    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("xyy快乐的玩耍");
        }
    }
}
```







### 15.8 线程七大状态

**JDK中用`Thread.State`枚举表示了线程的几种状态**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304021148065.png)



线程的`RUNNABLE`状态可以分为`Ready`和`Running`状态。

可以看到在`Running`状态中调用了`yield`进入的是`Ready`就绪态等待被执行，而调用`join`进入的是`Waiting`等待状态，等待插入的线程执行完，才会继续进入`RUNNABLE`状态。

如果在`RUNNABLE`状态调用`sleep`、`wait(time)`或者`join(time)`等，会进入`TimedWaiting`状态，时间结束就返回`RUNNABLE`状态



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304021149451.png)





```java
package com.level10.state;

public class ThreadState {
    public static void main(String[] args) throws InterruptedException {
        T t = new T();
        System.out.println("new后的状态: " + t.getState()); // NEW
        t.start();
        System.out.println("start后的状态: " + t.getState());   // RUNNABLE

        while (t.getState() != Thread.State.TERMINATED) {
            System.out.println(t.getName() + " 状态 " + t.getState());
            Thread.sleep(500);
        }
        System.out.println(t.getName() + " 状态 " + t.getState());    // TERMINATED
    }
}

class T extends Thread {
    @Override
    public void run() {
        while (true) {
            for (int i = 1; i <= 10; i++) {
                System.out.println(" hi " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            break;
        }
    }
}
```







### 15.9 Synchronized

> 多线程编程中，一些数据不允许被多个线程同时访问，此时就要用到同步机制，保证数据在任何同一时刻，最多只有一个线程访问



**线程同步方法**

1、同步代码块

```java
synchronized (对象) {	// 得到对象的锁，才能操作同步代码
	// 需要被同步的代码
}
```

2、同步整个方法

```java
public synchronized void sell(){
	// 需要被同步的代码
}
```



**互斥锁**

1、`Java`中，引入了对象互斥锁的概念，来保证共享数据操作的完整性

2、每个对象都对应于一个可称为  互斥锁  的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象

3、关键字`synchronized`来与对象的互斥锁联系。当某个对象用`synchronized`修饰时，表明该对象在任一时刻只能由一个线程访问

4、同步方法的局限性：程序执行效率低

5、同步方法（非静态的）的锁可以是this，也可以是其他对象。（但是要保证所有线程互斥锁的对象是同一个）

6、同步方法（静态的）的锁为当前类本身



**注意**

1、同步方法如果没有使用`static`修饰，默认锁对象为`this`

2、如果方法用`static`修饰，默认锁对象为`当前类.class`

3、**要求多个线程的锁对象为同一个**

```java
package com.level10.syn;

public class ticketSell {
    public static void main(String[] args) {

        SellTicket sellTicket = new SellTicket();
        new Thread(sellTicket).start();
        new Thread(sellTicket).start();
        new Thread(sellTicket).start();

//        SellTicket01 sellTicket01 = new SellTicket01();
//        SellTicket01 sellTicket02 = new SellTicket01();
//        SellTicket01 sellTicket03 = new SellTicket01();
//        sellTicket01.start();
//        sellTicket02.start();
//        sellTicket03.start();
    }
}

// 使用同步方法synchronized实现线程同步
class SellTicket implements Runnable {
    private int ticketNum = 100;
    private boolean loop = true;
    Object o = new Object();
    // 非静态方法上加锁，这时锁在this对象
    // 也可以在代码块上写synchronized，同步代码块，互斥锁还是在this上
    // 也可以锁其他对象，这里锁上一个Object对象，因为创建的是一个对象，使用了三个线程，所以三个线程使用的是一个Object对象
    public /*synchronized*/ void sell() {
        synchronized (/*this*/ o){
            if (ticketNum <= 0) {
                System.out.println("票卖完了");
                loop = false;
                return;
            }
            System.out.println("窗口" + Thread.currentThread().getName() + " 卖了一张票，剩余票数: " + --ticketNum);
        }
    }
    @Override
    public void run() {
        while (loop) {
            sell();
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class SellTicket01 extends Thread {
    private static int ticketNum = 100;
    private static boolean loop = true;
    // synchronized修饰的是静态方法，锁为当前类.class,锁的是当前这个类
    private static /*synchronized*/ void sell() {
        // 也可以这样写
        synchronized (SellTicket01.class) {
            if (ticketNum <= 0) {
                System.out.println("票卖完了");
                loop = false;
                return;
            }
            System.out.println("窗口" + Thread.currentThread().getName() + " 卖了一张票，剩余票数: " + --ticketNum);
        }
    }
    @Override
    public void run() {
        while (loop) {
            sell();
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```





### 15.10 线程死锁

> 多个线程都占用了对方的资源，不肯相让，就造成死锁。例如A线程拿着1号资源，需要2号资源，B进程拿着2号资源，需要1号资源，他俩就会卡在这里

```java
package com.level10.syn;

public class DeadLock {
    public static void main(String[] args) {
        DeadLockDemo A = new DeadLockDemo(true);
        DeadLockDemo B = new DeadLockDemo(false);
        A.setName("A线程");
        B.setName("B线程");
        A.start();
        B.start();
    }
}


class DeadLockDemo extends Thread {
    static Object o1 = new Object();    // 保证多线程，共享一个对象,这里使用 static
    static Object o2 = new Object();
    boolean flag;
    public DeadLockDemo(boolean flag) {
        this.flag = flag;
    }
    @Override
    public void run() {
        //1. 如果 flag 为 T, 线程 A 就会先得到/持有 o1 对象锁, 然后尝试去获取 o2 对象锁
        //2. 如果线程 A 得不到 o2 对象锁，就会 Blocked
        //3. 如果 flag 为 F, 线程 B 就会先得到/持有 o2 对象锁, 然后尝试去获取 o1 对象锁
        //4. 如果线程 B 得不到 o1 对象锁，就会 Blocked
        if (flag) {
            synchronized (o1) { //对象互斥锁, 下面就是同步代码
                System.out.println(Thread.currentThread().getName() + " 进入 1");
                synchronized (o2) {     // 这里获得 li 对象的监视权
                    System.out.println(Thread.currentThread().getName() + " 进入 2");
                }
            }
        } else {
            synchronized (o2) {
                System.out.println(Thread.currentThread().getName() + " 进入 3");
                synchronized (o1) { // 这里获得 li 对象的监视权
                    System.out.println(Thread.currentThread().getName() + " 进入 4");
                }
            }
        }
    }
}
```





### 15.11 释放锁

**下列操作会释放锁**

1、当前线程同步方法，同步代码块执行结束

2、当前线程在同步代码块、同步方法中遇到`break`，`return`

3、当前线程在同步代码块、同步方法中出现了未处理的`Error`或`Exception`，导致异常结束

4、当前线程在同步代码块、同步方法中执行了线程对象的`wait()`方法，当前线程暂停，并释放锁





**下列操作不会释放锁**

1、线程执行同步代码块或同步方法时，程序调用`Thread.sleep()`、`Thread.yield()`方法暂停线程执行，不会释放锁

2、线程执行同步代码块时，其他线程调用了该线程的`suspend()`方法将该线程挂起，该线程不会释放锁

**3、线程执行同步代码块或同步方法时，调用了其他线程的`join`方法，`join()`不会释放线程对象的锁， 只会释放`thread`对象的锁**

[join()不会释放线程对象的锁， 只会释放thread对象的锁]: https://developer.aliyun.com/article/972954



> `object.wait()`和`thread.join()`
>
> `join()`的底层是`wait()`，`wait()`会释放锁，但是释放的是`thread`的对象锁
>
> 因为我们调用的是`mythread.join()`，所以这里的 “锁” 对象其实是mythread这个对象。那这里wait释放的是mythread这个对象锁
>
> 也就可以这么说
>
> ```java
> synchronized(obj){
>     	thread.join(); //join不释放锁
> }
> ```
>
> ```java
> synchronized(thread){
> 	thread.join(); //join释放锁
> }
> ```

```java
public class DeadLockJoin {

    public static void main(String[] args) {
        Object object = new Object();
        MThread mythread = new MThread("mythread ", object);
        mythread.start();
        synchronized (mythread) {	// 会释放锁
        //synchronized (object) {	// 不会释放锁
            for (int i = 0; i < 100; i++) {
                if (i == 20) {
                    try {
                        System.out.println("开始join");
                        mythread.join();    //main主线程让出CPU执行权，让mythread子线程优先执行
                        System.out.println("结束join");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println(Thread.currentThread().getName() +"==" + i);
            }
        }
        System.out.println("main方法执行完毕");
    }
}

class MThread extends Thread {
    private String name;
    private Object obj;
    public MThread(String name, Object obj) {
        this.name = name;
        this.obj = obj;
    }
    @Override
    public void run() {
        // synchronized (obj) {
        synchronized (this) {
            for (int i = 0; i < 100; i++) {
                System.out.println(name + i);
            }
        }
    }
}
```









## 16 IO流

### 16.1 文件

> 文件在程序里是以流的形式来操作的

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304072054737.png)





#### 16.1.1 常用的文件操作

> File 类是 java.io 包中唯一代表磁盘文件本身的对象。File 类定义了一些与平台无关的方法来操作文件，File类主要用来获取或处理与磁盘文件相关的信息，像文件名、 文件路径、访问权限和修改日期等，还可以浏览子目录层次结构
>
> File 类不具有从文件读取信息和向文件写入信息的功能，它仅描述文件本身的属性

**1、创建文件**

`new File(String pathname)`	根据路径构造一个File对象	

`new File(File parent, String child)`		根据父目录文件 + 子路径创建

`new File(String parent, String child)`	根据父目录 + 子路径创建

`createNewFile`		创建新文件



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304072059569.png)



```java
package com.level11.file;

import org.junit.Test;

import java.io.File;
import java.io.IOException;

public class fileMethod {
    public static void main(String[] args) {

    }

    @Test
    public void test01() {
        String filePath = "e:\\a.txt";
        File file = new File(filePath);
        try {
            file.createNewFile();
            System.out.println("文件创建成功");
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    @Test
    public void test02() {
        File parentFile = new File("e:\\");
        String childPath = "b.txt";
        File file = new File(parentFile, childPath);
        try {
            file.createNewFile();
            System.out.println("文件创建成功");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void test03() {
        String parentPath = "e:\\";
        String childPath = "c.txt";
        File file = new File(parentPath, childPath);
        try {
            file.createNewFile();
            System.out.println("文件创建成功");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```





**2、获取文件相关信息**

`getName`			`getAbsolutePath`			`getParent`			`length`			`exist`			`isFile`			`isDirectory`

```java
package com.level11.file;

import java.io.File;

public class fileInformation {
    public static void main(String[] args) {
        File file = new File("E:\\a.txt");
        System.out.println("文件名 = " + file.getName());
        System.out.println("文件绝对路径 = " + file.getAbsoluteFile());
        System.out.println("文件父目录 = " + file.getParent());
        System.out.println("文件大小(字节) = " + file.length());
        System.out.println("文件是否存在 = " + file.exists());
        System.out.println("是不是一个文件 = " + file.isFile());
        System.out.println("是不是一个目录 = " + file.isDirectory());
    }
}
```





**目录的操作**

`mkdir`	创建一级目录

`mkdirs`	创建多级目录

`delete`	删除空目录或文件

```java
package com.level11.file;

import org.junit.Test;

import java.io.File;
import java.io.IOException;

public class Directory_ {
    public static void main(String[] args) {

    }

    @Test
    // 判断e:\\a.txt是否存在，存在就删除
    public void test01() {
        String filePath = "e:\\a.txt";
        File file = new File(filePath);
        if (file.exists()){
            if (file.delete()) {
                System.out.println(filePath + " 删除成功");
            }else {
                System.out.println(filePath + " 删除失败");
            }
        } else{
            System.out.println("文件不存在");
        }
    }

    @Test
    // 判断e:\\Demo 目录是否存在，存在就删除,java中目录也被看做是文件
    public void test02() {
        String dirPath = "e:\\Demo";
        File file = new File(dirPath);
        if (file.exists()){
            if (file.delete()) {
                System.out.println(dirPath + " 删除成功");
            }else {
                System.out.println(dirPath + " 删除失败");
            }
        } else{
            System.out.println("文件不存在");
            file.mkdir();
            System.out.println(dirPath + " 创建成功");
        }
    }

    @Test
    // 判断e:\\a\\b\\c\\d 目录是否存在，存在就删除，不存在就创建
    public void test03() {
        String dirPath = "e:\\a\\b\\c\\d";
        File file = new File(dirPath);
        if (file.exists()){
            System.out.println("文件存在");
            file.delete();
        }else{
            System.out.println("文件不存在");
            if (file.mkdirs())
                System.out.println(dirPath + " 创建成功");
            else
                System.out.println("创建失败");
        }
    }
}
```





### 16.2 IO流

> 1、I/O是Input/Output的缩写，I/O技术用于处理数据传输。如读/写文件，网络通讯等
>
> 2、Java程序中，对于数据的输入输出以 “ 流 ” 的方式进行
>
> 3、java.io包下提供了各种 “ 流 ” 类和接口。用以获取不同种类的数据，并通过方法输入或输出数据
>
> 4、输入input：读取外部数据（磁盘、光盘等存储设备的数据）到程序（内存）中
>
> 5、输出output：将程序（内存）数据输出到磁盘、光盘等存储设备中



**IO流分类**

- 按操作数据单位不同分为：字节流（8bit），用于二进制文件中。字符流（按字符），用于文本文件
- 按数据流的流向不同分为：输入流、输出流
- 按流的角色不同分为：节点流、处理流/包装流



| 抽象基类 | 字节流       | 字符流 |
| -------- | ------------ | ------ |
| 输入流   | InputStream  | Reader |
| 输出流   | OutputStream | Writer |

> 这四个类都是抽象类，他们派生出来的子类名称都是以其父类名作为子类名后缀



**IO流体系**

![image-20230407223229328](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20230407223229328.png)







#### 16.2.1 InputStream

- `InputStream`：字节输入流。`InputStream`抽象类是所有类字节输入流的超类



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304081528622.png)

`InputStream`常用子类

1、`FileInputStream`：文件输入流

​	`read()` ：读取一个字节数据

​	`read(byte[] b)`：读取多个字节数据

2、`BufferedInputStream`：缓冲字节输入流

3、`ObjectInputStream`：对象字节输入流

```java
package com.level11.inputStream_;

import org.junit.Test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class fileInputStream {
    public static void main(String[] args) {

    }

    // 使用read(),单个字节的读取，效率较低
    @Test
    public void readFile01() {
        String filePath = "e:\\hello.txt";
        FileInputStream fileInputStream = null;
        try {
            // 创建FileInputStream对象，用于读取文件
            fileInputStream = new FileInputStream(filePath);
            // read()每次返回一个字节数据，如果返回-1表示读取完毕
            int readData;
            while ((readData = fileInputStream.read()) != -1) {
                System.out.print((char)readData);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    // 使用read(byte[] b),每次读取多个字节，效率高
    @Test
    public void readFile02() {
        String filePath = "e:\\hello.txt";
        FileInputStream fileInputStream = null;
        try {
            // 创建FileInputStream对象，用于读取文件
            fileInputStream = new FileInputStream(filePath);
            // read(byte[] b)每次读取多个字节数据，如果返回-1表示读取完毕.读取正常就返回读到的字节数，最多读取b.length个字节
            byte b[] = new byte[8];
            int len;
            while ((len = fileInputStream.read(b)) != -1) {
                System.out.println(new String(b, 0, len));
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```







#### 16.2.2 OutputStream



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304081529469.png)



1、`FileOutputStream`：字节输出流 （如果文件不存在，会创建文件）

​	`new FileOutputStream(filePath);`	写入内容时会覆盖原始内容

​	`new FileOutputStream(filePath, boolean append);`	如果将append置位true就是将字节写入文件末尾

​	`write(int b)`：将指定字节写入文件输出流 

​	`write(byte[] b)`：将`b.length`个字节从指定的字节数组写入此文件输出流

​	`write(byte[] b, int off, int len)`：将len字节从位于偏移量off的指定字节数组写入此文件输出流

```java
package com.level11.outputStream;

import java.io.FileOutputStream;
import java.io.IOException;

public class fileOutputStream {
    public static void main(String[] args) {
        // 使用FileOutputStream将输入写入文件，如果文件不存在，就创建
        String filePath = "e:\\a.txt";
        FileOutputStream fileOutputStream = null;

        try {
            // new FileOutputStream(filePath); 写入内容时，会覆盖原来内容
            // fileOutputStream = new FileOutputStream(filePath);

            // new FileOutputStream(filePath, true); 写入内容时，是追加在后边
            fileOutputStream = new FileOutputStream(filePath, true);
            // 写入一个字节
            fileOutputStream.write('x');

            // 写入字符串
            String str = "xrj123";
            // str.getBytes() 将字符串转为字节数组
            fileOutputStream.write(str.getBytes());

            // 从字节数组的off位置开始，写入len个字节
            fileOutputStream.write(str.getBytes(), 3, 3);

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```





#### 16.2.3 FileReader

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304081613363.png)



**`FileReader`相关方法**

1、`new FileReader(File / String)`

2、`read()`：每次读取单个字符，返回该字符，读到文件末尾就返回 -1

3、`read(char[])`：每次读取多个字符，返回读取到的字符数，读到文件末尾返回 -1

4、`new String(char[])`：将`char[]`转为`String`

5、`new String(char[], off, len)`：将`char[]`的指定部分转为`String`

```java
package com.level11.reader;

import org.junit.Test;

import java.io.*;

public class fileReader {
    public static void main(String[] args) {

    }

    // 单个字符读取
    @Test
    public void test01() {
        String filePath = "e:\\a.txt";
        FileReader fileReader = null;

        try {
            fileReader = new FileReader(filePath);

            int data;
            // 每次读取一个字符，返回-1表示读取结束
            while ((data = fileReader.read()) != -1) {
                System.out.print((char)data);
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    // 每次读取多个字符
    @Test
    public void test02() {
        String filePath = "e:\\a.txt";
        FileReader fileReader = null;

        try {
            fileReader = new FileReader(filePath);

            char[] s = new char[8];
            int len;
            // 每次读取多个字符，返回的是读取到的字符长度
            while ((len = fileReader.read(s)) != -1) {
                // 这里一定要这么写，引入如果不写上读取长度，假如这次读了3个字符，那么s的后5个字符还是上次读取的
                System.out.print(new String(s, 0, len));
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```





#### 16.2.4 FileWriter

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304081614140.png)



**`FileWriter`常用方法**

1、`new FileWriter(File / String)`：覆盖模式，相当于流的指针在首段

2、`new FileWriter(File / String, true)`：追加模式，相当于流的指针在尾端

3、`writer(int)`：写入单个字符

4、`writer(char[])`：写入指定字符数组

5、`writer(char[], off, len)`：写入指定数组的指定部分

6、`writer(String)` ：写入整个字符串

7、`writer(string, off, len)`：写入字符串的指定部分

8、`String`类：`toCharArray`：将`String`转换为`char[]`



**注意**

`FileWriter`使用后，必须要关闭（close）或刷新（flush），否则写不到指定文件

```java
package com.level11.writer;

import java.io.FileWriter;
import java.io.IOException;

public class fileWriter {
    public static void main(String[] args) {
        String filePath = "e:\\a.txt";
        FileWriter fileWriter = null;
        char[] s = {'徐', '睿', '杰', 'a'};
        try {
            // 默认覆盖写入
            fileWriter = new FileWriter(filePath);
            // 1.writer(int)：写入单个字符
            fileWriter.write('z');
            // 2.writer(char[])：写入指定字符数组
            fileWriter.write(s);
            // 3.writer(char[], off, len)：写入指定数组的指定部分
            fileWriter.write(s, 0, 3);
            // 4.writer(String) ：写入整个字符串
            fileWriter.write(" xyy");
            // 5.writer(string, off, len)：写入字符串的指定部分
            fileWriter.write(" xrj123456", 0, 6);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 必须close或者flush才可以写入
            try {
                // fileWriter.flush();
                fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }
}
```



> fileWriter.close()和fileWriter.flush()最后都会执行this.out.write()语句，这里才是真正写入文件的地方

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304081701541.png)











### 16.3 节点流和处理流

#### 16.3.1 基本介绍

- 节点流可以从一个特定的数据源读写数据，如`FileReader`、`FileWriter`

  ![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101644531.png)



- 处理流（也叫包装流）是 “  连接  “ 在已存在的流（节点流或处理流）之上，为程序提供更为强大的读写功能，也更加灵活。如`BufferedReader`、`BufferedWriter`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101646688.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101658116.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101702950.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101658243.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101702319.png)

> 在处理流`BufferedReader`和`BufferedWriter`中，分别有一个Reader类型的in对象和Writer类型的out对象，在创建处理流对象时，可以将相应的节点流对象传入，通过处理流操作节点流进而操作数据源



**节点流和处理流的关系和区别**

1、节点流是底层流，直接跟数据源相关

2、处理流（包装流）包装节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入输出

3、处理流对节点流进行包装，使用了修饰器设计模式，不会直接与数据源相关



**处理流的功能**

1、性能的提高：主要以增加缓冲的方式来提高输入输出的效率

2、操作的便捷：处理流可能提供了一系列便捷的方法来一次输入输出大批量数据





#### 16.2.3 BufferedReader

> BufferedReader和BufferedWriter属于字符流，按照字符来读取数据
>
> 关闭处理流时，只需要关闭外层流即可

```java
package com.level11.reader;

import java.io.BufferedReader;
import java.io.FileReader;

public class BufferedReader_ {
    public static void main(String[] args) throws Exception{
        String filePath = "e:\\a.txt";
        BufferedReader bufferedReader = null;

        bufferedReader = new BufferedReader(new FileReader(filePath));
        String line = "";
        // 一次读取一行，但是没有换行
        while ((line = bufferedReader.readLine()) != null) {
            System.out.println(line);
        }
        bufferedReader.close();
    }
}
```

> bufferedReader.close();  在关闭流时，只需要关闭处理流即可，因为处理流最终会调用in.close()，in就是传入的节点流

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101656870.png)





#### 16.2.4 BufferedWriter

```java
package com.level11.writer;

import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class BufferedWriter_ {
    public static void main(String[] args) {
        String filePath = "e:\\xrj.txt";
        BufferedWriter bufferedWriter = null;

        try {
            // bufferedWriter = new BufferedWriter(new FileWriter(filePath, true)); 追加写
            bufferedWriter = new BufferedWriter(new FileWriter(filePath, true));
            // bufferedWriter = new BufferedWriter(new FileWriter(filePath));
            String data = "xrj, 徐睿杰123456";
            bufferedWriter.write(data);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                bufferedWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```





#### 16.2.5 BufferedInputStream 

![image-20230410171138725](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20230410171138725.png)

> BufferedInputStream 是字节流，在创建BufferedInputStream 时，会创建一个内部缓冲区数组



#### 16.2.6 BufferedOutputStream

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304101712651.png)

> BufferedOutputStream 是字节流，实现缓冲的输出流，可以将多个字节写入底层输出流中，而不必对每次字节写入调用底层系统



**利用字节处理流完成图片拷贝**

```java
package com.level11.inputStream;

import java.io.*;

public class BufferedInputStream_ {
    public static void main(String[] args) {
        String srcPath = "e:\\1.jpg";
        String destPath = "e:\\2.jpg";
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;

        try {
            bis = new BufferedInputStream(new FileInputStream(srcPath));
            bos = new BufferedOutputStream(new FileOutputStream(destPath));

            byte[] b = new byte[1024];
            int len;
            while ((len = bis.read(b)) != -1) {
                bos.write(b, 0, len);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (bis != null)
                    bis.close();
                if (bos != null)
                    bos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```







### 16.4 对象流

> 1、将int num = 100; 这个int数据保存到文件中，注意不是100数字，而是int 100，并且还能从文件中直接恢复int 100
>
> 2、将Dog dog = new Dog("x1", 25); 这个dog对象 保存到 文件中，并且能从文件中恢复
>
> 3、上边的要求就是能够将基本数据类型或对象进行 序列化 和 反序列化 操作



**序列化和反序列化**

- 序列化就是在保存数据时，保存数据的值和数据类型
- 反序列化就是在恢复数据时，恢复数据的值和数据类型
- 需要让某个对象支持序列化机制，必须让他的类是可序列化的，为了让类可序列化，须实现以下两个接口之一
  - `Serializable`： 这是一个标记接口，没有方法，推荐使用
  - `Externalizable`：该接口有方法需要实现





#### 16.4.1 对象流介绍

> 功能：提供了对基本类型或对象类型的序列化和反序列化的方法

`ObjectOutputStream`：提供序列化功能

`ObjectInputStream`：提供反序列化功能



`ObjectOutputStream`和`ObjectInputStream`都是**处理流**，他们的构造方法都可以接收一个节点流

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111056446.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111102953.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111056727.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111101967.png)







#### 16.4.2 ObjectOutputStream

```java
package com.level11.outputStream;

import java.io.*;

public class ObjectOutputStream_ {
    public static void main(String[] args) throws IOException {
        // 序列化后，保存的文件格式不是纯文本，而是按照他的格式来，因此什么类型文件都可以
        String filePath = "e:\\data.dat";
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filePath));

        oos.writeInt(100); // int => Integer (实现了Serializable)
        oos.writeDouble(9.5); // double => Double (实现了Serializable)
        oos.writeChar('x'); // char => Character (实现了Serializable)
        oos.writeUTF("xrj1234"); // String (实现了Serializable)
        oos.writeObject(new Dog("x1", 5));

        oos.close();
    }
}
```

```java
package com.level11.outputStream;

import java.io.Serializable;

public class Dog implements Serializable {
    String name;
    int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```



#### 16.4.3 ObjectInputStream

```java
package com.level11.inputStream;

import com.level11.outputStream.Dog;

import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;

public class ObjectInputStream_ {
    public static void main(String[] args) {
        // 指定反序列化文件
        String filePath = "e:\\data.dat";
        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream(filePath));

            // 读取数据(反序列化)顺序需要和保存数据(序列化)顺序一致
            System.out.println(ois.readInt());
            System.out.println(ois.readDouble());
            System.out.println(ois.readChar());
            System.out.println(ois.readUTF());

            // dog的编译类型为Object，运行类型为Dog
            Object dog = ois.readObject();
            System.out.println("运行类型 = " + dog.getClass()); // 运行类型 = class com.level11.outputStream.Dog
            System.out.println(dog);  // Object => Dog

            // 如果想要调用dog的方法，Dog对象必须能够引用到
            dog = (Dog)dog;
            System.out.println(((Dog) dog).getName());

        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            try {
                ois.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```





#### 16.4.4 注意事项

1、读写顺序要一致

2、要求序列化或反序列化的对象的类，需要实现`Serializable`

3、序列化的类中建议添加`SerialVersionUIDAdder`，提高版本兼容性

```java
private static final long serialVersionUID = 1L;
```

> 在 序列化存储/反序列化读取 或者是 序列化传输/反序列化接收 时，JVM 会把传来的字节流中的serialVersionUID与本地相应实体（类）的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。
>
> 在对实体类进行不影响业务流程的升级时，比如只追加了一个附加信息字段，可以不改变序列化版本号，来实现新旧实体类的兼容性（接收方的类里没有的字段被舍弃；多出来的字段赋初始值）。

4、序列化对象时，`static`和`transient`修饰的成员不会被序列化，其他都会被序列化

> transient 关键字的作用是控制变量的序列化，在变量声明前加上该关键字，可以阻止该变量被序列化到文件中，在被反序列化后，transient 变量的值被设为默认初始值（int => 0)。
>
> static修饰的变量也不会被序列化，反序列化后，static修饰的值就是类中初始化的值。（初始化为几就是几）
>
> 除了使用 Transient 关键字以外，还可以将不需要被序列化的字段抽取出来放到父类中，子类实现 Serializable 接口，父类不实现，根据父类序列化规则，父类的字段数据将不被序列化。

5、序列化对象时，要求里面属性的类型也需要实现序列化接口

6、序列化具备可继承性，如果某类实现了序列化，则它所有的子类也默认实现了序列化








### 16.5 标准输入输出流

|                      | 类型        | 默认设备 |
| -------------------- | ----------- | -------- |
| System.in  标准输入  | InputStream | 键盘     |
| System.out  标准输出 | PrintStream | 显示器   |

> 传统方法`System.out.println();`  是使用out对象 将数据输出到显示器
>
> 传统方法`Scanner`是从标准输入键盘接收数据

```java
package com.level11;

public class standard {
    public static void main(String[] args) {
        // public final static InputStream in = null;
        // System.in编译类型 InputStream
        // System.in运行类型 BufferedInputStream
        System.out.println(System.in.getClass());

        // public final static PrintStream out = null;
        // System.out编译类型 PrintStream
        // System.out运行类型 PrintStream
        System.out.println(System.out.getClass());
    }
}
```





### 16.6 转换流

> 读文件默认是utf-8编码，但是如果文件编码不是utf-8，那么读出来的文件就会乱码，而字符流无法指定编码格式，字节流可以指定。通过字节流指定编码格式后，利用转换流将其转为字符流即可

```java
package com.level11.transformation;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

public class CodeQuestion {
    public static void main(String[] args) throws IOException {
        // 读取e:\\a.txt到程序
        // 读文件默认是utf-8编码
        String filePath = "e:\\a.txt";
        BufferedReader br = new BufferedReader(new FileReader(filePath));
        String s = br.readLine();
        System.out.println(s);
        br.close();
    }
}
```





#### 16.6.1 InputStreamReader

> 他可以将字节流转换为一个字符流，`InputStreamReader`是`Reader`的子类，它可以接收一个`InputStream`对象，并指定编码格式，将其转换为Reader
>
> 当处理纯文本数据时，字符流效率更高，将字节流转换为字符流，且可以解决字符乱码问题

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111628347.png)



```java
package com.level11.transformation;

import java.io.*;

public class InputStreamReader_ {
    public static void main(String[] args) throws IOException {
        String filePath = "e:\\a.txt";
        // 1.将 FileInputStream 转化为 InputStreamReader
        // 2.指定编码 gbk
        // InputStreamReader isr = new InputStreamReader(new FileInputStream(filePath), "gbk");
        // 3.将 InputStreamReader 转化为 BufferedReader
        // BufferedReader br = new BufferedReader(isr);

        // 可以将上边两行合为一句
        BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(filePath), "gbk"));
        String s = br.readLine();
        System.out.println(s);
        br.close();
    }
}
```







#### 16.6.2 OutputStreamWriter

> `OutputStreamWriter`是`Writer`的子类，可以将`OutputStream`转换为`Writer`，并且可以指定编码格式

![image-20230411164205573](C:\Users\XRJ\AppData\Roaming\Typora\typora-user-images\image-20230411164205573.png)

```java
package com.level11.transformation;

import com.level7.Map_.TreeMap_;

import java.io.*;

public class OutputStreamWriter_ {
    public static void main(String[] args) throws IOException {
        String filePath = "e:\\a.txt";
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(filePath, true), "gbk"));
        bw.write("123456abcd徐睿杰");
        bw.close();
    }
}
```







### 16.7 打印流

#### 16.7.1 PrintStream

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111701382.png)

```java
package com.level11.print_;

import java.io.IOException;
import java.io.PrintStream;

public class PrintStream_ {
    public static void main(String[] args) throws IOException {
        PrintStream out = System.out;
        // 默认情况下，PrintStream输出数据的位置是 标准输出，即电脑屏幕
        out.print("123abc,你好");
        /*
        public void print(String s) {
            if (s == null) {
                s = "null";
            }
            write(s);
        }
         */
        // 因为print底层调用的是write方法，因此可以直接调用write方法输出
        out.write("123abc,你好".getBytes());
        out.close();

        // 我们可以修改打印流的输出位置
        /*
        public static void setOut(PrintStream out) {
            checkIO();
            setOut0(out);
        }
         */
        System.setOut(new PrintStream("e:\\a.txt"));
        // 123456会被输出到e:\a.txt文件中
        System.out.println("123456");
    }
}
```







#### 16.7.2 PrintWriter

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304111701352.png)

```java
package com.level11.print_;

import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

public class PrintWriter_ {
    public static void main(String[] args) throws IOException {
        // 传入一个PrintStream对象 (OutPutStream的子类)
        // PrintWriter printWriter = new PrintWriter(System.out); // System.out默认打印到屏幕上

        // 传入一个字符串，输出到对应文件中
        // PrintWriter printWriter = new PrintWriter("e:\\a.txt"); // 输出到e:\a.txt中

        // 传入一个Writer对象，Writer指向e:\a.txt文件
        PrintWriter printWriter = new PrintWriter(new FileWriter("e:\\a.txt"));
        printWriter.println("hello, world111");
        printWriter.close(); // 必须close后才会写入
    }
}
```





### 16.8 Properties 类

**传统方法读取`properties`文件**

```java
package com.level11.Properties_;

import java.io.*;

public class Properties01 {
    public static void main(String[] args) throws IOException {
        String filePath = "src\\com\\level11\\Properties_\\mysql.properties";
        BufferedReader bw = new BufferedReader(new FileReader(filePath));

        String line;
        while ((line = bw.readLine()) != null) {
            String str[] = line.split("=");
            System.out.println(str[0] + "的值是: " + str[1]);
        }
    }
}
```





#### 16.8.1 基本介绍

- `Properties`是专门用来读写配置文件的集合类

  配置文件格式：

  `键=值`

  `键=值`

- 键值对等号间没有空格，值不需要引号引起来。默认类型为`String`
- `Properties`常见方法
  - `load` ：加载配置文件的键值对到`Properties`对象
  - `list`：将数据显示到指定设备
  - `getProperty(key)`：根据键获取值
  - `setProperty(key, value)`：设置键值对到`Properties`对象
  - `store`：将`Properties`中的键值对存储到配置文件中，在idea中，保存配置文件，如果有中文，会存储为`unicode`编码



**使用`Properties`类读取`properties`文件**

```java
package com.level11.Properties_;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

public class Properties02 {
    public static void main(String[] args) throws IOException {
        // 1.创建Properties对象
        Properties properties = new Properties();
        // 2.加载指定配置文件
        properties.load(new FileReader("src\\com\\level11\\Properties_\\mysql.properties"));
        // 3.将信息显示在控制台
        properties.list(System.out);
        // 4.根据key获取对应的值
        System.out.println(properties.getProperty("ip"));
        System.out.println(properties.getProperty("user"));
        System.out.println(properties.getProperty("pwd"));
    }
}
```



**使用`Properties`存储文件**

```java
package com.level11.Properties_;

import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;

public class Properties03 {
    public static void main(String[] args) throws IOException {
        Properties properties = new Properties();

        // properties继承自HashTable，setProperty底层是put方法，如果key存在就修改value，不存在就添加
        properties.setProperty("user", "徐睿杰");
        properties.setProperty("ip", "192.168.1.101");
        properties.setProperty("pwd", "123456");

        // 将properties对象存储到文件
        // 这里用FileWriter写文件，遇到中文也可以，如果用FileInputStream，存储中文会存储其unicode编码
        properties.store(new FileWriter("src\\com\\level11\\Properties_\\mysql2.properties"), null);
    }
}
```







## 17 网络编程

### 17.1 相关概念

#### 17.1.1 IP地址

1、IP地址的表示形式（ipv4,4字节）：点分十进制 `xx.xx.xx.xx`

2、每一个十进制数的范围 `0-255`

3、ip地址的组成 = 网络地址 + 主机地址

4、ipv6是16字节，128位，通常用十六进制表示



**ipv4地址分类**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304151645150.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304151646350.png)





#### 17.1.2 域名和端口

**域名**

1、域名的好处是，方便记忆，解决记ip的困难

2、将ip地址映射为域名



**端口号**

1、用于标识计算机上某个特定的网络程序

2、端口范围0-65535【2个字节表示端口】

3、0-1024已被占用，比如 `ssh：22、ftp：21、smtp：25、http：80`

4、常见网络端口号

​	`tomcat:8080`

​	`mysql:3306`

​	`oracle:1521`

​	`sqlserver:1433`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304151605412.png)







#### 17.1.3 网络通信协议

> TCP/IP协议，又叫网络通讯协议，由网络层的ip协议和传输层的tcp协议组成

> 如下图所示，用户数据经过应用层加上了Appl首部，然后经过传输层，加上tcp首部，经过网络层，加上ip首部，最后经过数据链路层加上帧首和帧尾，在物理层发送数据到目标主机，目标主机接收到后，依次解析，去掉一层层首部，最后得到数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304151649698.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304151628709.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304151652428.png)







#### 17.1.4 TCP和UDP

**TCP协议**

1、使用TCP协议前，须建立TCP连接，形成传输数据通道

2、传输前，采用 “ 三次握手 ” 方式 建立连接，**是可靠的**

3、TCP协议进行通信的两个应用进程：客户端、服务端

4、在连接中可进行大数据量的传输

5、传输完毕，需要释放连接，**效率低**



**UDP协议**

1、将数据、源、目的封装成数据包，不需要进行连接

2、每个数据包的大小在64k以内，不适合传输大量数据

3、**是不可靠的**

4、发送数据后无需释放资源，速度快







### 17.2 InetAddress类

1、`getLocalHost`：获取本机`InetAddress`对象

2、`getByName`：根据指定主机名/域名 获取`InetAddress`对象

3、`getHostName`：获取`InetAddress`对象的主机名

4、`getHostAddrss`：获取`InetAddress`对象的ip地址

```java
package com.level12.api;

import java.net.InetAddress;
import java.net.UnknownHostException;

public class API_ {
    public static void main(String[] args) throws UnknownHostException {
        // 获取本机 InetAddress对象
        InetAddress localHost = InetAddress.getLocalHost();
        System.out.println(localHost);  // DESKTOP-TKKF4KS/192.168.198.1

        // 通过主机名获取 InetAddress对象
        InetAddress host1 = InetAddress.getByName("DESKTOP-TKKF4KS");
        System.out.println(host1);      // DESKTOP-TKKF4KS/192.168.198.1

        // 根据域名返回 InetAddress对象
        InetAddress host2 = InetAddress.getByName("www.baidu.com");
        System.out.println(host2);  // www.baidu.com/14.119.104.254

        // 通过 InetAddress对象 获取对应地址
        String address = host2.getHostAddress();
        System.out.println(address);    // 14.119.104.254
    }
}
```





### 17.3 Socket

#### 17.3.1 基本介绍

1、套接字（Socket）开发网络应用程序被广泛应用

2、通信的两端都要有`Socket`，是两台机器间通信的端点

3、网络通信实际上是`Socket`间的通信

4、`Socket`允许程序把网络连接当成一个流，数据在两个`Socket`间通过IO传输

5、一般发起通信的应用程序属于客户端，等待通信请求的为服务端

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161533462.png" style="zoom:67%;" />





#### 17.3.2 TCP网络通信

> 1、基于客户端—服务端的网络通信
>
> 2、底层使用TCP/IP协议
>
> 3、基于Socket的TCP编程

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304161544677.png)





**案例1（使用字节流）**

> 1、编写一个客户端和服务器端
>
> 2、服务器端在9999号端口监听
>
> 3、客户端连接到服务器端，发送 “  hello, server ”，然后退出
>
> 4、服务器端 收到客户端的消息后，输出并退出

```java
package com.level12.Socket_;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketTcp01Service {
    public static void main(String[] args) throws IOException {
        // 1.在本机的9999号端口监听，等待连接
        // 注意：9999端口不能被其他服务占用
        // 一个端口只能被一个服务监听，但是一个serverSocket可以通过accept()返回多个Socket【多个客户端连接服务器的并发】
        ServerSocket serverSocket = new ServerSocket(9999);

        System.out.println("服务端 9999号端口 等待连接");
        // 2.当没有客户端来连接时，程序会阻塞在这里
        // 如果有客户端来连接9999号端口，通过accept()可以接收到，返回一个Socket
        Socket socket = serverSocket.accept();

        System.out.println("服务端  socket " + socket.getClass());

        // 3.得到socket后，通过socket.getInputStream() 得到输入流对象，进行读取
        InputStream inputStream = socket.getInputStream();

        byte[] data = new byte[1024];
        int len;
        while ((len = inputStream.read(data)) != -1) {
            System.out.println(new String(data, 0, len));
        }

        serverSocket.close();
        socket.close();
        inputStream.close();
    }
}
```

```java
package com.level12.Socket_;

import java.io.IOException;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.Socket;

public class SocketTcp01Client {
    public static void main(String[] args) throws IOException {
        // 1.首先建立连接,创建一个连接本机9999端口的socket对象
        // 如果连接成功，返回Socket对象
        Socket socket = new Socket(InetAddress.getLocalHost(), 9999);
        System.out.println("客户端 socket " + socket.getClass());

        // 2.通过socket.getOutputStream()得到一个输出流对象
        OutputStream outputStream = socket.getOutputStream();

        // 3.通过输出流对象写数据发送给服务器端
        outputStream.write("hello, server".getBytes());

        socket.close();
        outputStream.close();
        System.out.println("客户端退出");
    }
}
```







**案例2（使用字节流）**

> 1、编写一个客户端和服务器端
>
> 2、服务器端在9999号端口监听
>
> 3、客户端连接到服务器端，发送 “  hello, server ”，并接收服务端发回的 "  hello, client ", 在退出
>
> 4、服务器端 收到客户端的消息后，输出，然后向客户端发送 "  hello, client ", 再退出
>
> 5、注意如果用BufferedOutputStream,在socket中，字节流也必须flush一下，不能close，close会关闭socket

```java
package com.level12.Socket_;

import java.io.BufferedOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketTcp02Service {
    public static void main(String[] args) throws IOException {
        // 1.服务端监听9999端口，等待连接
        ServerSocket serverSocket = new ServerSocket(9999);
        System.out.println("服务端 9999号端口 等待连接");

        // 2.如果收到连接，返回一个Socket对象
        Socket socket = serverSocket.accept();

        // 3.通过socket.getInputStream()得到客户端的输入
        InputStream inputStream = socket.getInputStream();
        byte[] data = new byte[1024];
        int len;
        while ((len = inputStream.read(data)) != -1) {
            System.out.println(new String(data, 0, len));
        }

        // 读取结束标记
        socket.shutdownInput();
        System.out.println("服务端 收到了 客户端的消息，现在准备向客户端发送消息");

        // 4.通过socket.getOutputStream()向客户端发消息
        BufferedOutputStream outputStream = new BufferedOutputStream(socket.getOutputStream());
        //OutputStream outputStream = socket.getOutputStream();
        outputStream.write("hello, client".getBytes());

        // 设置结束标记
        outputStream.flush();   // 注意如果用BufferedOutputStream,在socket中，字节流也必须flush一下，不能close，close会关闭socket
        socket.shutdownOutput();
        System.out.println("服务端 发型消息完毕，退出");

        // 5.关闭连接
        serverSocket.close();
        socket.close();
        inputStream.close();
        outputStream.close();
    }
}
```

```java
package com.level12.Socket_;

import java.io.BufferedOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.net.InetAddress;
import java.net.Socket;

public class SocketTcp02Client {
    public static void main(String[] args) throws IOException {
        // 1.建立连接(ip+端口),连接成功就返回一个Socket对象
        Socket socket = new Socket(InetAddress.getLocalHost(), 9999);

        // 2.通过socket.getOutputStream()向服务端发送消息
        BufferedOutputStream outputStream = new BufferedOutputStream(socket.getOutputStream());
        //OutputStream outputStream = socket.getOutputStream();
        outputStream.write("hello server".getBytes());

        // 输出一个结束标记，告诉服务端，我的输入结束了
        outputStream.flush();
        socket.shutdownOutput();
        System.out.println("客户端 向服务端发送消息完毕，等待服务端发送的消息");

        // 3.发送完消息后，接收服务端发送的消息
        InputStream inputStream = socket.getInputStream();
        byte[] data = new byte[1024];
        int len;
        while ((len = inputStream.read(data)) != -1) {
            System.out.println(new String(data, 0, len));
        }

        // 设置结束标记
        socket.shutdownInput();
        System.out.println("客户端 收到了 服务端的消息，退出");

        socket.close();
        inputStream.close();
        outputStream.close();
    }
}
```





**案例3（使用字符流）**

> 1、编写一个服务端，和一个客户端
>
> 2、服务端在9999端口监听
>
> 3、客户端连接到服务端，发送 "  hello, server "，并接收服务端回发的 "  hello, client "，再退出
>
> 4、服务端收到客户端发送的消息后，输出，然后给客户端发送 "hello, client"，再退出

```java
package com.level12.Socket_;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketTcp03Service {
    public static void main(String[] args) throws IOException {
        // 1.监听9999端口
        ServerSocket serverSocket = new ServerSocket(9999);

        // 2.建立连接后，返回Socket对象
        Socket socket = serverSocket.accept();

        // 3.Socket对象得到inputStream，然后转为Reader
        BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));

        String data;
        while ((data = reader.readLine()) != null) {
            System.out.println(data);
        }
        /*
        data = reader.readLine();
        System.out.println(data);
        */
        socket.shutdownInput();
        System.out.println("服务端接收数据完毕，准备发送数据给客户端");

        // 4.Socket对象得到outputStream，转为Writer
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
        writer.write("hello, client");

        writer.flush();
        socket.shutdownOutput();
        /*
        写一个换行也可以表示结束符，但是读取的时候必须用readLine读，且不能循环读
        writer.newLine();
        writer.flush();
         */

        serverSocket.close();
        socket.close();
        reader.close();
        writer.close();
    }
}
```

```java
package com.level12.Socket_;

import java.io.*;
import java.net.InetAddress;
import java.net.Socket;

public class SocketTcp03Client {
    public static void main(String[] args) throws IOException {
        // 1.创建Socket对象连接9999端口
        Socket socket = new Socket(InetAddress.getLocalHost(), 9999);

        // 2.将OutputStream转为Writer，然后写数据
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
        writer.write("hello, server");

        writer.flush();
        socket.shutdownOutput();
        /*
        写一个换行也可以表示结束符，但是读取的时候必须用readLine读，且不能循环读
        writer.newLine();
        writer.flush();
         */

        System.out.println("客户端发送数据完毕，准备接收服务端的数据");

        // 3.将InputStream转为Reader，然后读数据
        BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        String data;

        while ((data = reader.readLine()) != null) {
            System.out.println(data);
        }
        /*
        data = reader.readLine();
        System.out.println(data);
        */
        socket.shutdownInput();

        socket.close();
        reader.close();
        writer.close();
    }
}
```





**案例4 （传输文件）**

> 1、编写一个服务端，和一个客户端
>
> 2、服务端在8888端口监听
>
> 3、客户端连接到服务端，发送一张图片 e:\\qie.png 
>
> 4、服务端收到客户端发送的图片后，将图片保存到当前包下，然后给客户端发送 "服务端 图片已收到"，再退出
>
> 5、客户端收到服务端发送的 收到 消息后，在退出
>
> 6、注意：在Socket中，用缓冲流传送数据，不管是字节流还是字符流都要flush一下，不能close，因为close会把Socket也关闭

```java
package com.level12.Socket_;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

public class TCPFileUploadServer {
    public static void main(String[] args) throws IOException {
        // 1.监听8888端口
        ServerSocket serverSocket = new ServerSocket(8888);

        System.out.println("服务端8888端口等待连接");

        // 建立连接后，返回Socket对象
        Socket socket = serverSocket.accept();

        // 2.建立连接后，接收客户端发来的图片,并将图片写入当前目录下
        String filePath = "D:\\qie.png";
        BufferedInputStream bis = new BufferedInputStream(socket.getInputStream());
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(filePath));
        byte[] data = new byte[1024];
        int len;
        while ((len = bis.read(data)) != -1) {
            bos.write(data, 0, len);
        }
        socket.shutdownInput();

        System.out.println("服务端图片已收到");

        // 3.给客户端发送收到信息
        BufferedOutputStream bos2 = new BufferedOutputStream(socket.getOutputStream());
        bos2.write("服务端 图片已收到".getBytes());
        bos2.flush();   // 注意如果用BufferedOutputStream,在socket中，字节流也必须flush一下，不能close，close会关闭socket
        socket.shutdownOutput();

        // 4.关闭资源
        bos.close();
        bis.close();
        bos2.close();
        socket.close();
        serverSocket.close();
    }
}
```

```java
package com.level12.Socket_;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.InetAddress;
import java.net.Socket;

public class TCPFileUploadClient {
    public static void main(String[] args) throws IOException {
        // 1.创建socket连接本机8888端口
        Socket socket = new Socket(InetAddress.getLocalHost(), 8888);

        // 2.发送图片数据给服务端
        String filePath = "e:\\qie.png";
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream(filePath));
        BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());
        byte[] data = new byte[1024];
        int len;
        while ((len = bis.read(data)) != -1) {
            bos.write(data, 0, len);
        }

        // 设置结束标记
        bos.flush(); // 注意如果用BufferedOutputStream,在socket中，字节流也必须flush一下，不能close，close会关闭socket
        socket.shutdownOutput();

        System.out.println("客户端图片发送完毕");

        // 3.接收服务端发送的收到消息
        BufferedInputStream bis2 = new BufferedInputStream(socket.getInputStream());
        while ((len = bis2.read(data)) != -1) {
            System.out.println(new String(data, 0, len));
        }
        socket.shutdownInput();

        // 4.关闭资源
        bis2.close();
        bis.close();
        bos.close();
        socket.close();
    }
}
```





#### 17.3.3 netstat指令

1、`netstat -an`可以查看当前主机网络情况，包括端口监听和网络连接情况

2、`netstat -an | more` 分页显示

3、`listening`表示某个端口在监听。`established`表示两个端口已建立连接

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304171820524.png)



- 当客户端连接到服务端后，实际上客户端也是通过一个端口和服务端进行通讯的，这个端口是通过`TCP/IP`来分配的，是随机的



> 如果我们访问其他主机，在我们本地查看时，外部地址就是其他主机的ip + 端口，其他主机相当于客户端，本机相当于服务端。在其他主机查看时，外部地址就是我们的ip + 端口，我们的主机相当于客户端。下图是因为，服务端和客户端都是我们自己的主机

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304171834541.png)







### 17.4 UDP网络通信

#### 17.4.1 基本介绍

1、类`DatagramSocket`和`DatagramPacket`【数据包/数据报】实现了基于UDP协议的网络程序

2、UDP数据报通过数据报套接字`DatagramSocket`发送和接收，系统不保证UDP数据报一定能安全到达目的地，也不确定什么时候可以到达

3、`DatagramPacket`对象封装了UDP数据报，在数据报中包含了发送端的IP地址和端口号，以及接收端的IP地址和端口号

4、UDP协议中每个数据报都给出了完整的地址信息，因此无需建立发送方和接收方的连接



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304172015458.png)



**基本流程**

1、核心的两个类/对象：`DatagramSocket`和`DatagramPacket`

2、建立发送端和接收端（没有服务端和客户端的概念）

3、发送数据前，建立数据包/报 `DatagramPacket`对象

4、调用`DatagramSocket`的发送、接收方法

5、关闭`DatagramPacket`





**案例**

> 1、编写一个接收端A，和一个发送端B
>
> 2、接收端A在端口9999等待接收数据
>
> 3、发送端B向接收端A发送数据 "  hello, 明天吃火锅 "
>
> 4、接收端A 收到 B的消息后，回复 "  好的，收到 "，然后退出
>
> 5、发送端B收到A的消息后，退出

```java
package com.level12.UDP;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;

public class UDPReceiverA {
    public static void main(String[] args) throws IOException {
        // 1.创建DatagramSocket对象，准备在9999端口接收数据
        DatagramSocket socket = new DatagramSocket(9999);
        System.out.println("A 9999端口监听中");

        // 2.创建DatagramPacket接收数据
        byte[] data = new byte[1024];
        DatagramPacket packet = new DatagramPacket(data, data.length);

        // 3.如果接收到数据就装入DatagramPacket对象中
        // 如果没有数据包发送过来，就会在这里阻塞
        socket.receive(packet);

        // 4.将DatagramPacket对象拆包，拿到数据
        int length = packet.getLength();
        byte[] data1 = packet.getData();
        System.out.println(new String(data1, 0, length));

        System.out.println("接收端 A 收到数据，准备向B发送数据");

        // 5.将发送的数据 数据长度  目的ip  目的端口包装到DatagramPacket对象中
        byte[] bytes = "好的，收到".getBytes();
        DatagramPacket packet1 = new DatagramPacket(bytes, bytes.length, InetAddress.getByName("10.175.217.218"), 9998);

        // 6.发送数据
        socket.send(packet1);

        // 5.关闭资源
        socket.close();
        System.out.println("接收端A退出");
    }
}
```

```java
package com.level12.UDP;

import java.io.IOException;
import java.net.*;

public class UDPSendB {
    public static void main(String[] args) throws IOException {
        // 1.创建DatagramSocket对象，准备在9998端口接收数据
        DatagramSocket socket = new DatagramSocket(9998);

        // 2.创建数据，通过DatagramSocket发送
        byte[] data = "hello, 明天吃火锅".getBytes();
        DatagramPacket packet = new DatagramPacket(data, data.length, InetAddress.getByName("10.175.217.218"), 9999);

        socket.send(packet);

        System.out.println("B发送完数据，准备接收A的数据");

        // 3.创建DatagramPacket对象，接收数据
        byte[] bytes = new byte[1024];
        DatagramPacket packet1 = new DatagramPacket(bytes, bytes.length);

        socket.receive(packet1);

        // 4.收到数据输出
        System.out.println(new String(packet1.getData(), 0, packet1.getLength()));

        // 5.关闭资源
        socket.close();
        System.out.println("发送端B退出");
    }
}
```











## 18 反射

### 18.1 一个需求引出反射

> 1、根据配置文件re.properties指定信息，创建Cat对象并调用方法hi
>
> ```properties
> classfullpath=com.level13.Cat
> method=hi
> ```
>
> 2、这样的需求在学习框架时很多，即通过外部文件配置，在不修改源码的情况下来控制程序，也符合设计模式的ocp原则（开闭原则：不修改源码，扩展功能）



```java
package com.level13;

public class Cat {
    private String name = "jack";

    public void hi() {
        System.out.println("hi " + name);
    }
}
```

```properties
classfullpath=com.level13.Cat
method=hi
```

```java
package com.level13.reflection.question;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.lang.reflect.Method;
import java.util.Properties;

public class ReflectionQuestion {
    public static void main(String[] args) throws Exception {
        // 根据配置文件re.properties指定信息，创建Cat并调用hi方法

        // 1.传统方法 new 对象 ==> 调用方法
        // Cat cat = new Cat();
        // cat.hi();

        // 2.读取文件方式
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/level13/re.properties"));

        String classfullpath = properties.getProperty("classfullpath"); // com.level13.Cat
        String methodName = properties.getProperty("method"); // hi
        System.out.println(classfullpath + "\n" + methodName);

        // new classfillpath() 虽然这里classfullpath的内容是Cat的路径，但是他是字符串，不能创建对象

        // 3.使用反射
        // (1)加载类,获得Class类型的对象
        Class cls = Class.forName(classfullpath);
        // (2)通过cls得到加载的类com.level13.Cat的实例
        Object o = cls.newInstance();
        System.out.println(o.getClass());
        // (3)通过cls获得加载的类com.level13.Cat的Method的方法对象
        // 即在反射中，可以把方法看做对象
        Method method = cls.getMethod(methodName);
        // (4)通过method调用方法: 即通过方法对象来调用方法
        method.invoke(o);   // 传统方法: 对象.方法()  反射机制: 方法.invoke(对象)
    }
}
```





### 18.2 反射机制

1、反射机制允许程序在执行期借助Reflection API取得任何类的内部信息（比如成员变量，构造器，成员方法等），并能操作对象的属性及方法。反射在设计模式和框架底层都会用到

2、加载完类之后，在堆中就产生了一个Class类型的对象（一个类只有一个Class对象），这个对象包含了类的完整结构信息。通过这个对象得到类的结构。这个Class对象就像一面镜子，透过这个镜子可以看到类的结构，所以称为反射



**Java反射机制原理示意图**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231530163.png)

> 当我们编写好代码，通过javac指令去编译时，就会产生.class的字节码文件，该文件包含了类的所有信息。我们知道，创建对象第一步是类加载，字节码文件通过类加载器进行类加载，会产生一个Class类对象，该对象存放在堆区（同时会在方法区存放Cat类的字节码二进制数据/元数据），该对象包含了Cat类的所有信息，通过Class类对象可以创建对象，调用对象方法，操作属性等。同时，我们得到了对象，也可以通过对象来获得该Class对象





**Java反射机制可以完成**

1、在运行时判断任意一个对象所属的类

2、在运行时构造任意一个类的对象

3、在运行时得到任意一个类所具有的的成员变量和方法

4、在运行时调用任意一个对象的成员变量和方法

5、生成动态代理





**反射相关的主要类**

1、`java.lang.Class:`代表一个类，Class对象表示某个类加载后在堆中的对象

2、`java.lang.reflect.Method:`代表类的方法，Method对象表示某个类的方法

3、`java.lang.reflect.Field:`代表类的成员变量，Field对象表示某个类的成员变量

4、`java.lang.reflect.Constructor:`代表类的构造方法，Constructor对象表示构造器



```java
package com.level13.reflection;

import java.io.FileInputStream;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.util.Properties;

public class Reflection01 {
    public static void main(String[] args) throws Exception {
        // 根据配置文件re.properties指定信息，创建Cat并调用hi方法

        // 1.传统方法 new 对象 ==> 调用方法
        // Cat cat = new Cat();
        // cat.hi();

        // 2.读取文件方式
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/com/level13/re.properties"));

        String classfullpath = properties.getProperty("classfullpath"); // com.level13.Cat
        String methodName = properties.getProperty("method"); // hi
        System.out.println(classfullpath + "\n" + methodName);

        // 3.使用反射
        // (1)加载类,获得Class类型的对象
        Class cls = Class.forName(classfullpath);
        // (2)通过cls得到加载的类com.level13.Cat的实例
        Object o = cls.newInstance();
        System.out.println(o.getClass());
        // (3)通过cls获得加载的类com.level13.Cat的Method的方法对象
        // 即在反射中，可以把方法看做对象
        Method method = cls.getMethod(methodName);
        // (4)通过method调用方法: 即通过方法对象来调用方法
        method.invoke(o);   // 传统方法: 对象.方法()  反射机制: 方法.invoke(对象)

        // java.lang.reflect.Field: 代表类的成员变量, Field 对象表示某个类的成员变量
        // 得到name字段,但是name字段是private的，所以访问不到
        // Field name = cls.getField("name"); // 抛出异常NoSuchFieldException
        // 得到public修饰的age属性
        Field ageField = cls.getField("age");
        System.out.println(ageField.get(o)); // 传统方法: 对象.成员变量  反射: 成员变量.get(对象)

        // java.lang.reflect.Constructor: 代表类的构造方法, Constructor 对象表示构造器
        Constructor constructor = cls.getConstructor(); // ()中可以传入参数，这里返回的是无参构造器
        System.out.println(constructor); // public com.level13.Cat()
        Constructor constructor1 = cls.getConstructor(int.class); // int.class就是int的class对象
        System.out.println(constructor1); // public com.level13.Cat(int)
    }
}
```





**反射优点及缺点**

1、优点：可以动态的创建和使用对象（也是框架底层核心），使用灵活

2、缺点：使用反射基本就是解释执行，对执行速度有影响



**反射调用优化**

1、`Method`和`Field`、`Constructor`对象都有`setAccessible()`方法

2、`setAccessible()`作用是启动和禁用访问安全检查的开关

3、参数值为`true`表示反射的对象在使用时取消访问检查，提高反射效率。参数值为`false`则表示反射的对象执行访问检查

```java
package com.level13.reflection;

import com.level13.Cat;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Reflection02 {
    public static void main(String[] args) throws Exception {
        m1();   // m1() 2
        m2();   // m2() 1191
        m3();   // m3() 1108
    }

    public static void m1() {
        Cat cat = new Cat();
        long start = System.currentTimeMillis();
        for (int i = 0; i < 990000000; i++) {
            cat.hi();
        }
        long end = System.currentTimeMillis();
        System.out.println("m1() " + (end - start));
    }

    public static void m2() throws Exception {
        Class<?> cls = Class.forName("com.level13.Cat");
        Object o = cls.newInstance();
        Method hi = cls.getMethod("hi");
        long start = System.currentTimeMillis();
        for (int i = 0; i < 990000000; i++) {
            hi.invoke(o);
        }
        long end = System.currentTimeMillis();
        System.out.println("m2() " + (end - start));
    }

    public static void m3() throws Exception{
        Class<?> cls = Class.forName("com.level13.Cat");
        Object o = cls.newInstance();
        Method hi = cls.getMethod("hi");
        hi.setAccessible(true); // 在反射调用方法时，取消访问检查
        long start = System.currentTimeMillis();
        for (int i = 0; i < 990000000; i++) {
            hi.invoke(o);
        }
        long end = System.currentTimeMillis();
        System.out.println("m3() " + (end - start));
    }
}
```







### 18.3 Class类

#### 18.3.1 基本介绍

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231655267.png)

1、`Class`也是类，因此继承了`Object`类

2、`Class`类不是`new`出来的，而是系统创建的

3、对于某个类的`Class`类对象，在内存中只存在一份，因为类只加载一次

4、每个类的实例都会记得自己是由哪个`Class`实例所生成

5、通过`Class`对象可以得到一个类的完整结构，通过一系列API

6、`Class`对象是存放在堆区的

7、类的字节码二进制数据，是放在方法区的，有的地方称为类的元数据（包括 方法代码、变量名、方法名、访问权限等）

```java
package com.level13.class_;

import com.level13.Cat;

public class Class01 {
    public static void main(String[] args) throws ClassNotFoundException {

        // 1.传统方法创建对象
        // Cat cat = new Cat(10);

        // 2.反射方式创建对象
        Class<?> cls1 = Class.forName("com.level13.Cat");

        // 3.对于某个类的Class类对象，在内存中只有一份，因为类只加载一次
        Class<?> cls2 = Class.forName("com.level13.Cat");
        System.out.println(cls1.hashCode());    // 1735600054
        System.out.println(cls2.hashCode());    // 1735600054

        Class<?> cls3 = Class.forName("com.level13.Dog");
        System.out.println(cls3.hashCode());    // 21685669
    }
}
```



1、执行`Cat cat = new Cat(10);`首先会进入`loadClass`方法，进行类加载，该方法返回一个`Class`对象

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231659810.png)

2、执行`Class<?> cls1 = Class.forName("com.level13.Cat");`首先会先进入`forName`方法，然后执行`getClassLoader`，最后仍会进入`loadClass`方法进行类加载，返回一个`Class`对象。【如果在此之前类加载过，就不会进入`loadClass`方法了】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231701176.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231701012.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231702978.png)





#### 18.3.2 Class类常用方法

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304231707531.png)

```java
package com.level13.class_;

import com.level13.Car;

import java.lang.reflect.Field;

public class Class02 {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchFieldException {
        String classFullPath = "com.level13.Car";

        // 1.获取到Car类对应的Class对象
        // <?>表示不确定的Java类型
        Class<?> cls = Class.forName(classFullPath);
        System.out.println(cls); // 输出cls对象，是哪个类的Class对象  class com.level13.Car
        System.out.println(cls.getClass()); // 输出cls运行类型  class java.lang.Class

        // 2.得到包名
        System.out.println(cls.getPackage().getName()); // com.level13

        // 3.得到全类名
        System.out.println(cls.getName()); // com.level13.Car

        // 4.通过 cls 创建对象实例
        Car car = (Car) cls.newInstance();
        System.out.println(car); // car.toString()

        // 5.通过反射获取属性brand
        Field brand = cls.getField("brand");
        System.out.println(brand.get(car)); // 奔驰

        // 6.通过反射给属性赋值
        brand.set(car, "五菱宏光");
        System.out.println(brand.get(car)); // 五菱宏光

        // 7.获得所有字段
        Field[] fields = cls.getFields();
        for (Field f : fields) {
            System.out.println(f.getName() + " " + f.get(car));
        }
    }
}
```





#### 18.3.3 获取Class类对象

1、前提：已知一个类的全类名，且该类在类路径下，可以通过Class类的静态方法**`forName()`**获取，可能抛出`ClassNotFindException`

​	应用场景：多用于配置文件，读取类全路径，加载类

2、前提：已知具体的类，通过**`类.class`**获取。该方式最为安全可靠，程序性能最高

​	应用场景：多用于参数传递，比如通过反射得到对应构造器对象 `Constructor constructor1 = cls.getConstructor(String.class);`

3、前提：已知某个类的实例，调用该实例的**`getClass()`**方法获取`Class`对象【这里`getClass()`得到的就是该对象的运行类型，运行类型就是它的Class对		  	象）】

​	应用场景：通过创建好的对象，获取Class对象

4、其他方式【类加载器】

​	`ClassLoader classLoader = car.getClass().getClassLoader();`

​	`Class<?> cls4 = classLoader.loadClass("类的全路径名");`

5、基本数据（int, char,boolean,float,double,byte,long,short）按如下方式得到 Class 类对象

​	`Class cls = 基本数据类型.class;`

6、基本数据类型对应的包装类，可以通过` .TYPE` 得到 Class

​	`Class cls = 包装类.TYPE`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304241759518.png)

```java
package com.level13.class_;

import com.level13.Car;

public class getClass_ {
    public static void main(String[] args) throws ClassNotFoundException {
        // 1.Class.forName()
        String classPath = "com.level13.Car";
        Class<?> cls1 = Class.forName(classPath);
        System.out.println(cls1);   // class com.level13.Car

        // 2.类名.class
        Class cls2 = Car.class;
        System.out.println(cls2);   // class com.level13.Car

        // 3.对象.getClass()
        Car car = new Car();
        Class cls3 = car.getClass();
        System.out.println(cls3);   // class com.level13.Car

        // 4.通过类加载器【4种】获得类的Class对象
        ClassLoader classLoader = car.getClass().getClassLoader();
        Class<?> cls4 = classLoader.loadClass(classPath);
        System.out.println(cls4);   // class com.level13.Car

        // cls1, cls2, cls3, cls4都是同一个对象
        System.out.println(cls1.hashCode() + "\n" + cls2.hashCode() + "\n" + cls3.hashCode() + "\n" + cls4.hashCode());

        // 5.基本数据(int, char,boolean,float,double,byte,long,short) 按如下方式得到 Class 类对象
        Class<Integer> cls5 = int.class;
        Class<Double> cls6 = double.class;
        System.out.println(cls5);   // int
        System.out.println(cls6);   // double

        // 6.基本数据类型对应的包装类，可以通过 .TYPE 得到 Class
        Class<Integer> cls7 = Integer.TYPE;
        System.out.println(cls7);   // int

        // int和Integer的Class对象是同一个
        System.out.println(cls5.hashCode() + "\n" + cls7.hashCode());
    }
}
```





#### 18.3.4 哪些类型有Class对象

1、外部类，成员内部类，静态内部类，局部内部类，匿名内部类

2、`interface`：接口

3、数组

4、`enum`：枚举

5、`annotation`：注解

6、基本数据类型

7、`void`

```java
package com.level13.class_;

import java.io.Serializable;

public class allTypeClass {
    public static void main(String[] args) {
        Class<String> cls1 = String.class;  // 外部类
        Class<Serializable> cls2 = Serializable.class;  // 接口
        Class<int[]> cls3 = int[].class;    // 数组
        Class<Integer[][]> cls4 = Integer[][].class;    // 二维数组
        Class<Thread.State> cls5 = Thread.State.class;  // 枚举
        Class<Deprecated> cls6 = Deprecated.class;  // 注解
        Class<Double> cls7 = double.class;  // 基本数据类型
        Class<Void> cls8 = void.class;  // void
        Class<Class> cls9 = Class.class;

        System.out.println(cls1);   // class java.lang.String
        System.out.println(cls2);   // interface java.io.Serializable
        System.out.println(cls3);   // class [I
        System.out.println(cls4);   // class [[Ljava.lang.Integer;
        System.out.println(cls5);   // class java.lang.Thread$State
        System.out.println(cls6);   // interface java.lang.Deprecated
        System.out.println(cls7);   // double
        System.out.println(cls8);   // void
        System.out.println(cls9);   // class java.lang.Class
    }
}
```







### 18.4 类加载

#### 18.4.1 静态加载和动态加载

反射机制是Java实现动态语言的关键，也就是通过反射实现类动态加载

1、静态加载：编译时加载相关的类，如果该类不存在则报错，依赖性强【`javac`编译时就加载】

2、动态加载：运行时加载需要的类，如果运行时不使用该类，即使不存在，也不报错，降低了依赖性【`java`运行时，使用了才加载】

```java
import java.lang.reflect.Method;
import java.util.Scanner;

public class load {
    public static void main(String[] args) throws Exception{
        Scanner sc = new Scanner(System.in);
        String key = sc.next();
        switch (key) {
            case "1":
                // 静态加载，javac编译时会检查Cat类是否存在，如果不存在就报错
                Cat cat = new Cat();    
                cat.hi();
                break;
            case "2":
                // 动态加载，javac编译时不会检查，只有在java命令运行程序且执行这一段代码时才会检查Dog是否存在
                Class<?> cls = Class.forName("Dog");    
                Object o = cls.newInstance();
                Method method = cls.getMethod("hi");
                method.invoke(o);
                System.out.println("hello");
                break;
            default:
                System.out.println("hello");
        }
    }
}

class Cat {
    public void hi(){
        System.out.println("hello");
    }
}
```





**类加载时机**

1、当创建对象时（new）	// 静态加载

2、当子类被加载时，父类也被加载	// 静态加载

3、调用类中的静态成员	// 静态加载

4、通过反射	// 动态加载





#### 18.4.2 类加载过程图

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304241827143.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304241827161.png)





##### 加载

JVM在该阶段的主要目的是将字节码从不同的数据源（可能是class文件，也可能是jar包，甚至网络）转化为二进制字节流加载到内存，并生成一个代表该类的`java.lang.Class`对象





##### 链接阶段——验证

1、目的是为了确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身安全

【类加载时会创建一个`SecurityManager`对象进行检查】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304241832912.png)

2、包括：文件格式验证【是否以魔数 `0xcafebabe` 开头】、元数据验证、字节码验证和符号引用验证

【Dog.class文件内容，以魔数开头】

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304241837126.png)

3、可以考虑使用`-Xverify:none`参数来关闭大部分的类验证措施，缩短虚拟机类加载的时间





##### 链接阶段——准备

- JVM会在该阶段对**静态变量**分配内存，并**默认初始化**。这些变量所使用的内存都将在方法区进行分配

```java
class A {
    // 1.a是实例属性，不是静态变量，在准备阶段不会分配内存
    // 2.b是静态变量，准备阶段分配内存，b是默认初始化为0，而不是20
    // 3.c是static final修饰的常量，它会直接被赋值为30
    private int a = 10;
    private static int b = 20;
    private static final int c = 30;
}
```





##### 链接阶段——解析

- 虚拟机将常量池内的符号引用替换为直接引用的过程

> 在类加载之前，例如A类和B类之间的引用使用的是逻辑地址，而在类加载后，他们之间的引用变为物理地址





##### 初始化

1、到初始化阶段，才真正执行类中定义的Java程序代码，此阶段是执行`<clinit>()`方法的过程

2、`<clinit>()`方法是由编译器按语句在源文件中出现的顺序，依次自动收集类中的所有**静态变量**的赋值动作和**静态代码块**中的语句，并进行合并

3、虚拟机会保证一个类的`<clinit>()`方法在多线程环境中被正确的加锁、同步，如果多个线程同时去初始化一个类，那么只有一个线程去执行这个方法的`<clinit>()`方法，其他线程都需要阻塞等待，直到`clinit()`方法完毕。这也就是为什么一个类只会被加载一次

```java
package com.level13.classLoad_;

public class ClassLoad03 {
    public static void main(String[] args) {
        // 1.加载B类，生成B的class对象
        // 2.链接 num = 0
        // 3.初始化阶段
        // 依次自动收集类中的所有静态变量的赋值动作和静态代码块中的语句，并进行合并
        /*
            clinit() {
                System.out.println("B 静态代码块被执行");
                num = 200;
                num = 100;
            }
            合并: num = 100
         */
        // System.out.println(B.num);

        // 如果new一个B对象才会执行构造器
        /*
            protected Class<?> loadClass(String name, boolean resolve)
                throws ClassNotFoundException
            {
                // 有synchronized修饰，所以保证了某个类在内存中，只有一个Class对象
                synchronized (getClassLoadingLock(name)) {
         */
        new B();
    }
}

class B{

    static {
        System.out.println("B静态代码块被执行");
        num = 200;
    }

    static int num = 100;

    public B() {
        System.out.println("B()构造器被执行");
    }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202304241924840.png)







### 18.5 通过反射获取类的结构信息

**`java.lang.Class`**

1、`getName()`：获取全类名

2、`getSimpleName()`：获取简单类名

3、`getFields()`：获取所有public修饰的属性，包括本类和父类

4、`getDeclaredFields()`：获取本类所有属性

5、`getMethods()`：获取所有public修饰的方法，包括本类和父类

6、`getDeclaredMethods()`：获取本类中所有方法

7、`getConstructors()`：获取本类中所有public修饰的构造器

8、`getDeclaredConstructors()`：获取本类中所有构造器

9、`getPackage()`：以Package形式返回包信息

10、`getSuperclass()`：以class形式返回父类信息

11、`getInterfaces()`：以class[]形式返回接口信息

12、`getAnnotations()`：以Annotation[]形式返回注解信息

```java
package com.level13.reflection;

import org.junit.Test;

import java.lang.annotation.Annotation;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ReflectionUtils {
    public static void main(String[] args) {

    }

    @Test
    public void api01() throws ClassNotFoundException {
        // 得到Class对象
        Class<?> personCls = Class.forName("com.level13.reflection.Person");
        // 1.获取全类名
        System.out.println(personCls.getName());    // com.level13.reflection.Person
        // 2.获取简单类名
        System.out.println(personCls.getSimpleName());  // Person
        // 3.获取所有public修饰的属性，包括本类和父类
        Field[] fields = personCls.getFields();
        for (Field f : fields) {
            System.out.println("本类和父类的public属性: " + f);
        }
        // 4.获取本类所有属性
        Field[] declaredFields = personCls.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            System.out.println("本类所有属性: " + declaredField);
        }
        // 5.获取所有public修饰的方法，包括本类和父类
        Method[] methods = personCls.getMethods();
        for (Method method : methods) {
            System.out.println("本类和父类的public方法: " + method);
        }
        // 6.获取本类中所有方法
        Method[] declaredMethods = personCls.getDeclaredMethods();
        for (Method declaredMethod : declaredMethods) {
            System.out.println("本类中所有方法: " + declaredMethod);
        }
        // 7.获取本类中所有public修饰的构造器
        Constructor<?>[] constructors = personCls.getConstructors();
        for (Constructor<?> constructor : constructors) {
            System.out.println("本类所有public的构造器" + constructor);
        }
        // 8.获取本类中所有构造器
        Constructor<?>[] declaredConstructors = personCls.getDeclaredConstructors();
        for (Constructor<?> declaredConstructor : declaredConstructors) {
            System.out.println("本类中所有构造器: " + declaredConstructor);
        }
        // 9.以Package形式返回包信息
        System.out.println(personCls.getPackage()); // package com.level13.reflection
        // 10.以class形式返回父类信息
        System.out.println(personCls.getSuperclass()); // class com.level13.reflection.A
        // 11.以class[]形式返回接口信息
        Class<?>[] interfaces = personCls.getInterfaces();
        for (Class<?> anInterface : interfaces) {
            System.out.println("接口信息: " + anInterface);
        }
        // 12.以Annotation[]形式返回注解信息
        Annotation[] annotations = personCls.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println("注解: " + annotation);
        }
    }
}

class A {
    public String hobby;

    public void hi() {
    }

    public A() {
    }

    public A(String hobby) {
        this.hobby = hobby;
    }
}

interface IA {
}

interface IB {
}

@Deprecated
class Person extends A implements IA, IB {
    //属性
    public String name;
    protected static int age; // 4 + 8 = 12
    String job;
    private double sal;

    //构造器
    public Person() {
    }

    public Person(String name) {
    }

    //私有的
    private Person(String name, int age) {
    }

    //方法
    public void m1(String name, int age, double sal) {
    }

    protected String m2() {
        return null;
    }

    void m3() {
    }

    private void m4() {
    }
}
```



**`java.lang.reflect.Field`**

1、`getModifiers()`：以`int`形式返回修饰符【默认修饰符为0，`public`为1，`private`为2，`protected`为4，`static`为8，`final`为16】

2、`getType`：以`Class`形式返回类型

3、`getName`：返回属性名



**`java.lang.reflect.Method`**

1、`getModifiers()`：以`int`形式返回修饰符【默认修饰符为0，`public`为1，`private`为2，`protected`为4，`static`为8，`final`为16】

2、`getReturnType`：以`Class`形式获取 返回类型

3、`getName`：返回方法名

4、`getParameterTypes`：以`Class[]`返回参数类型数组



**`java.lang.reflect.Constructor`**

1、`getModifiers()`：以`int`形式返回修饰符

2、`getName`：返回构造器名【全类名】

3、`getParameterTypes`：以`Class[]`返回参数类型数组

```java
public void api02() throws ClassNotFoundException {
    Class<?> personCls = Class.forName("com.level13.reflection.Person");
    // 获取本类所有属性
    Field[] declaredFields = personCls.getDeclaredFields();
    for (Field declaredField : declaredFields) {
        System.out.println("本类中所有属性: " + declaredField.getName() + "  该属性的修饰符: "
                + declaredField.getModifiers() + "  该属性的类型: " + declaredField.getType());
    }

    // 获取本类所有方法
    Method[] declaredMethods = personCls.getDeclaredMethods();
    for (Method declaredMethod : declaredMethods) {
        System.out.println("本类中方法名: " + declaredMethod.getName() + "  该方法的修饰符: "
        + declaredMethod.getModifiers() + "  该方法返回类型: " + declaredMethod.getReturnType());

        // 当前方法的参数类型
        Class<?>[] parameterTypes = declaredMethod.getParameterTypes();
        for (Class<?> parameterType : parameterTypes) {
            System.out.println("该方法参数类型: " + parameterType);
        }
        System.out.println("============");
    }

    // 获取本类所有构造器
    Constructor<?>[] declaredConstructors = personCls.getDeclaredConstructors();
    for (Constructor<?> declaredConstructor : declaredConstructors) {
        System.out.println("本类中构造器名: " + declaredConstructor.getName() + "  该构造器的修饰符: "
        + declaredConstructor.getModifiers());

        // 构造器的参数列表
        Class<?>[] parameterTypes = declaredConstructor.getParameterTypes();
        for (Class<?> parameterType : parameterTypes) {
            System.out.println("该构造器的参数类型: " + parameterType);
        }
        System.out.println("=============");
    }
}
```





### 18.6 通过反射创建对象

1、调用类中的`public`修饰的无参构造器

2、调用类中的指定构造器

3、`Class`类相关方法

- `newInstance`：调用类中的无参构造器，获取对应类的对象
- `getConstructor(Class...clazz)`：根据参数列表，获取对应的`public`构造器对象
- `getDecalaredConstructor(Class...clazz)`：根据参数列表，获取对应的所有构造器对象

4、`Constructor`类相关方法

- `setAccessible`：爆破
- `newInstance(Object...obj)`：调用构造器

```java
package com.level13.reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class ReflectCreateInstance {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchMethodException, InvocationTargetException {
        Class<?> userCls = Class.forName("com.level13.reflection.User");
        // 1.通过public的无参构造器创建对象
        Object user1 = userCls.newInstance();
        System.out.println(user1);

        // 2.通过public的有参构造器创建对象
        Constructor<?> constructor = userCls.getConstructor(String.class);
        Object user2 = constructor.newInstance("xrj");
        System.out.println(user2);

        // 3.通过private修饰的有参构造器创建对象
        Constructor<?> constructor1 = userCls.getDeclaredConstructor(int.class, String.class);
        // 通过爆破，反射可以访问 private 构造器/方法/属性
        constructor1.setAccessible(true);
        Object user3 = constructor1.newInstance(20, "xyy");
        System.out.println(user3);
    }
}

class User { //User 类
    private int age = 10;
    private String name = "xrj123";

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    private User(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public String toString() {
        return "User [age=" + age + ", name=" + name + "]";
    }
}
```





### 18.7 通过反射访问属性

1、根据属性名获取`Field`对象，`Field f = class对象.getDeclaredField(属性名);`

2、爆破：`f.setAccessible(true);`

3、访问

​	`f.set(o, 值);` 	o表示对象，修改该对象的f属性

​	`f.get(o);`	o表示对象，得到该对象的f属性

4、**注意**：如果是静态属性，`set`和`get`中的参数`o`，可以写为`null`

```java
package com.level13.reflection;

import java.lang.reflect.Field;

public class ReflectAccessProperty {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchFieldException {
        Class<?> stuCls = Class.forName("com.level13.reflection.Student");
        // 1.创建对象
        Object o = stuCls.newInstance();
        System.out.println(o.getClass());

        // 2.使用反射获得age对象
        Field age = stuCls.getField("age");
        age.set(o, 10);
        System.out.println(age.get(o));

        // 3.通过反射获得private属性
        Field name = stuCls.getDeclaredField("name");
        // 因为name是private修饰的，所以需要爆破
        name.setAccessible(true);
        name.set(o, "xrj");
        // static属性，也可以将o写为null
        name.set(null, "xyy");
        System.out.println(name.get(o));
    }
}

class Student {
    public int age;
    private static String name;

    public Student() {
    }

    public String toString() {
        return "Student [age=" + age + ", name=" + name + "]";
    }
}
```





### 18.8 通过反射访问方法

1、根据方法名和参数列表获取`Method`方法对象

​	`Method m = Class对象.getDeclaredMethod(方法名, XX.class);`

2、访问：`Object reVal = m.invoke(o, 实参列表);`   o是对象

3、注意：如果是静态方法，则`invoke`的参数o，可以写为`null`

```java
package com.level13.reflection;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class ReflectAccessMethod {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchMethodException, InvocationTargetException {
        Class<?> bossCls = Class.forName("com.level13.reflection.Boss");
        Object o = bossCls.newInstance();

        // 1.调用public的hi方法
        Method hi = bossCls.getMethod("hi", String.class);
        hi.invoke(o, "xrj");

        // 2.调用private方法
        Method say = bossCls.getDeclaredMethod("say", int.class, String.class, char.class);
        say.setAccessible(true);
        System.out.println(say.invoke(o, 20, "xrj", 'a'));
        // static方法，可以使用null
        System.out.println(say.invoke(null, 20, "xrj", 'a'));

        // 在反射中，如果有返回值，统一返回Object，但是运行类型还是和方法定义的返回类型一致
        Object invoke = say.invoke(o, 10, "aaa", 'b');
        System.out.println(invoke.getClass()); // String
    }
}

class Boss {
    public int age;
    private static String name;

    public Boss() {
    }

    private static String say(int n, String s, char c) {
        return n + " " + s + " " + c;
    }

    public void hi(String s) {
        System.out.println("hi " + s);
    }
}
```





### 18.5 反射能否修改final修饰的属性

- **反射是可以修改`final`变量的，但是如果是基本数据类型或者`String`类型的时候，无法通过对象获取修改后的值，因为`JVM`对其进行了内联优化。**

> 1、通过反射可以修改final修饰的变量，但是如果在定义final变量时就赋值，那么编译器会优化，将这个变量全都替换为常量值，所以通过反射虽然是修改了，但是直接获取该值还是原来的值。但是再次通过反射获取该值就是修改后的值了
>
> 2、对于字符串String，他的value数组是用final修饰的，但是value数组指向的位置的数据是private修饰的，所以通过反射修改字符数组的值，字符串的值就被修改。通过反射也可以修改value的指向，同样字符串的值被修改。
>
> 3、当使用 `new` 关键字创建一个字符串对象时，它会在堆内存中创建一个新的字符串对象，并且会创建一个新的字符数组来存储字符串的内容。这个字符数组的内容是字符串的字符序列的副本。并将该字符数组的引用存储在字符串对象`value`中。这个字符数组的内容是字符串内容的副本。因为字符串是不可变的，所以，`value` 存储的是字符数组的地址，而不是直接引用常量池中的内容
>
> 4、通过反射修改字符串对象的 `value` 字段，实际上修改的是堆内存中的字符数组的内容，而不会影响常量池中原始字符串的内容。
>
> 虽然之前通过反射修改了字符串 `s` 在堆内存中的字符数组的内容，但这并不会改变常量池中原始字符串的内容。因为字符串是不可变的，修改 `s` 的字符数组只会影响到 `s` 对象在堆内存中的表示，但不会影响常量池中的原始字符串。所以 输出`" " + s` 创建的新字符串对象引用的仍然是原始字符串对象的内容，即修改之前的值。这是 Java 字符串拼接的工作方式，为了确保字符串的不可变性。所以，虽然 `s` 在堆内存中的字符数组内容已经被修改，但是字符串对象的不可变性仍然保留，而 `" " + s` 操作会创建一个新的字符串对象，引用原始字符串对象的内容，这就是为什么输出的是修改前的值。
>
> 5、字符串对象的哈希码没是在字符串对象创建时计算的，通常会缓存下来，以提高性能，而不是在 `value` 字段的改变时重新计算。除非是创建一个新的String对象，然后指向它，这样哈希值才会改变

[反射可以修改final类型成员变量吗](https://blog.csdn.net/afdafvdaa/article/details/115190755?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-6-115190755-blog-105368076.235%5Ev32%5Epc_relevant_default_base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-6-115190755-blog-105368076.235%5Ev32%5Epc_relevant_default_base&utm_relevant_index=9)

```java
package com.level13;

import sun.java2d.pipe.SpanIterator;

import java.lang.reflect.Field;

public class bpFinal {
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        /*
            String中的value是final修饰的，即value数组不能指向新的地址，但是value的每个字符是可以修改的
            因为value是private修饰的，所以可以通过反射去修改value的每个字符的值
            value被final修饰了，但是反射是可以修改他的指向的，即value的值(地址)可以被修改
         */
        final String s = "ABCD";
        Class cls = String.class;
        System.out.println(s.hashCode()); // 2001986
        System.out.println(s);

        // 得到value数组，因为String的value数组是private修饰的
        Field value = cls.getDeclaredField("value");
        value.setAccessible(true);
        // value.get(s)得到value的值,value是引用类型,所以得到的是一个地址,即字符串存储的地址
        // System.out.println(value.get(s)); // [C@14ae5a5
        // 得到字符串的char数组，这里通过修改str数组的值，即可以修改字符串的值了
        char[] str = (char[]) value.get(s);
        str[0] = 'Z';
        // 如果这里final修饰了s,输出时，如果只输出s，字符串会修改，如果输出"xxx" + s字符串不会被修改
        System.out.println(s); // ZBCD
        System.out.println(" " + s); // ABCD
        System.out.println(s.hashCode()); // 2001986
        // System.out.println(value.get(s)); // [C@14ae5a5

        value.set(s, new char[]{'a', 'b'});
        /*
            value是final修饰的，即指向不能改，但是这里地址修改的原因可能是因为value初始化时，没有赋值，所以即使是final修饰了也可以修改
        */
        // System.out.println(value.get(s)); // [C@7f31245a
        System.out.println(s); // ab
        System.out.println(" " + s); // ABCD
        System.out.println(s.hashCode()); // 2001986
    }

}
```

