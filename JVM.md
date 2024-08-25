# 一、Java内存结构

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402031215862.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402031215576.png)





## 1、程序计数器

`Program Counter Register` 程序计数器（寄存器）

**作用**：记录下一条 `jvm `指令的执行地址行号。

**特点**：

- 是线程私有的
- 不存在内存溢出



```
0: getstatic #20 // PrintStream out = System.out; 
3: astore_1 // -- 
4: aload_1 // out.println(1); 
5: iconst_1 // -- 
6: invokevirtual #26 // -- 
9: aload_1 // out.println(2); 
10: iconst_2 // -- 
11: invokevirtual #26 // -- 
14: aload_1 // out.println(3); 
15: iconst_3 // -- 
16: invokevirtual #26 // -- 
19: aload_1 // out.println(4); 
20: iconst_4 // -- 
21: invokevirtual #26 // -- 
24: aload_1 // out.println(5); 
25: iconst_5 // -- 
26: invokevirtual #26 // -- 
29: return
```

解释器会解释指令为机器码交给 CPU 执行，程序计数器会记录下一条指令的地址行号，这样下一次解释器会从程序计数器拿到指令然后进行解释执行。多线程的环境下，如果两个线程发生了上下文切换，那么程序计数器会记录线程下一行指令的地址行号，以便于接着往下执行。





## 2、虚拟机栈

> 每个线程运行需要的内存空间，称为虚拟机栈。每个栈由多个**栈帧**（Frame）组成，对应着每次调用方法时所占用的内存。每个线程只能有一个活动栈帧，对应着当前正在执行的方法
>
> 1、垃圾回收是否涉及栈内存？
> 不会。栈内存是方法调用产生的，方法调用结束后会弹出栈。
>
> 2、栈内存分配越大越好吗？
> 不是。因为物理内存是一定的，栈内存越大，可以支持更多的递归调用，但是可执行的线程数就会越少。
>
> 3、方法的局部变量是否线程安全
> 如果方法内部的变量没有逃离方法的作用访问，它是线程安全的，如果是局部变量引用了对象，并逃离了方法的访问，那就要考虑线程安全问题。
>
> 4、每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法返回地址



**栈内存溢出**

栈帧过大、过多、或者第三方类库操作，都有可能造成栈内存溢出` java.lang.stackOverflowError` ，使用 `-Xss256k` 可以指定栈内存大小





## 3、本地方法栈

一些带有 **native** 关键字的方法就是需要 JAVA 去调用本地的C或者C++方法，因为 JAVA 有时候没法直接和操作系统底层交互，所以需要用到本地方法栈，服务于带 native 关键字的方法。





## 4、堆

> 1、通过new关键字创建的对象都会被放在堆内存
>
> 2、堆内存是线程共享的，堆内存中的对象都需要考虑线程安全问题
>
> 3、有垃圾回收机制



**堆内存溢出**

`java.lang.OutofMemoryError ：java heap space. `堆内存溢出。可以使用` -Xmx8m` 来指定堆内存大小。



**堆内存诊断**

- `jps` 工具：查看当前系统中有哪些 java 进程
- `jmap` 工具：生成堆转储快照；查看堆内存占用情况 `jmap - heap 进程id`
- `jconsole` 工具:图形界面的，多功能的监测工具，可以连续监测
- `jvisualvm` 工具
- `jstack`：生成虚拟机当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合





## 5、方法区

> 方法区是各个**线程共享**的内存区域，它用于存储已被虚拟机加载的类信息(比如class文件)、常量、静态变量、即时编译器编译后的代码等数据。



- JDK1.7中，方法区的实现是永久代，使用的是JVM内存。

  > 因为JVM内存是有限的，因此可能会出现OOM，`java.lang.OutOfMemoryError: PermGen space`
  >
  > 使用`-XX:MaxPermSize=8m `指定永久代内存大小

- JDK1.8中，方法区的实现是元空间，使用的是本地内存

  > JD1.8中方法区使用的是本地内存，即操作系统的内存，基本不会出现OOM，`java.lang.OutOfMemoryError: Metaspace`
  >
  > 使用 `-XX:MaxMetaspaceSize=8m` 指定元空间大小





### 5.1 运行时常量池

- **常量池**：就是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量等信息
- **运行时常量池**：常量池是 `*.class` 文件中的，当该类被加载，它的常量池信息就会放入运行时常量池，并把里面的**符号地址**变为**真实地址**



```java
public class Test {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

使用`javap`指令反编译后的字节码如下

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402031221390.png) 

​         

每条指令都会对应常量池表中一个地址，常量池表中的地址可能对应着一个类名、方法名、参数类型等信息，如下图中的`#2，#3, #4`对应上图常量池表的数据

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402031222019.png) 







### 5.2 StringTable

**美团技术博客**：https://tech.meituan.com/2014/03/06/in-depth-understanding-string-intern.html

- 常量池中的字符串仅是符号，只有在被用到时才会转化为对象，就是说在生成字节码文件的时候并不会将常量写入串池中，而是执行到了才会写入**（懒加载）**
- 利用串池的机制，来**避免重复创建字符串对象**
- 字符串**变量**拼接的原理是`StringBuilder + new String(xxx);`

> - 对于编译期可以确定值的字符串，也就是常量字符串 ，jvm 会将其存入字符串常量池。并且，字符串常量拼接得到的字符串常量在编译阶段就已经被存放字符串常量池，这个得益于编译器的优化。Javac 编译器会进行一个叫做 **常量折叠**的代码优化。常量折叠会把常量表达式的值求出来作为常量嵌在最终生成的代码中，这是 Javac 编译器会对源代码做的极少量优化措施之一
>
> - 对于 `String str3 = "str" + "ing";` 编译器会给你优化成 `String str3 = "string";` 。
>
>   并不是所有的常量都会进行折叠，只有编译器在程序编译期就可以确定值的常量才可以：
>
>   - 基本数据类型( `byte`、`boolean`、`short`、`char`、`int`、`float`、`long`、`double`)以及字符串常量。
>   - `final` 修饰的基本数据类型和字符串变量
>   - 字符串通过 “+”拼接得到的字符串、基本数据类型之间算数运算（加减乘除）、基本数据类型的位运算（<<、>>、>>> ）



1、初始的时候，`StringTable`【串池】为空，他是一个`HashTable`结构，不能扩容。

2、常量池中的信息，都会被加载到运行时常量池，这时`a`、`b`、`ab`都是常量池中的符号，还没有变为java字符串对象

3、执行`String s1 = "a"`，字节码`ldc #2`会把符号`a`变为`"a"`字符串对象，然后看`StringTable`中是否存在字符串`"a"`，如果不存在，就将这个字符串放入串池，如果存在，就直接使用串池中的字符串对象

4、执行`String s2 = "b"`，字节码`ldc #3`会把符号`b`变为`"b"`字符串对象，然后看`StringTable`中是否存在字符串`"b"`，如果不存在，就将这个字符串放入串池，如果存在，就直接使用串池中的字符串对象

5、执行`String s3 = "ab"`，字节码`ldc #4 `会把符号`ab`变为字符串`"ab"`对象，然后看`StringTable`中是否存在字符串`"ab"`，如果不存在，就将这个字符串放入串池，如果存在，就直接使用串池中的字符串对象

6、`String s4 = s1 + s2`，底层字节码执行的就是`new StringBuilder().append("a").append("b").toString()`，这里`toString()`就是执行的`new String("ab")`，因此`s4`指向的是堆区地址，而`s3`指向的是串池中的地址，因此`s3`和`s4`不相等

7、`String s5 = "a" + "b"`，字符串常量拼接，编译器底层会进行优化，javac在编译期间，结果就被确定为`ab`，在常量池中找到`"ab"`存在，所以`s5`指向的也是串池中的地址

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    String s1 = "a";
    String s2 = "b";
    String s3 = "ab";
    String s4 = s1 + s2;
    String s5 = "a" + "b";

    System.out.println(s3 == s4); // false
    System.out.println(s3 == s5); // true
}
```





**示例一**

1、当执行到` ldc #2 `时，会把符号 `a `变为` "a" `字符串对象，并放入串池中（hashtable结构 不可扩容）

2、当执行到` ldc #3` 时，会把符号 `b `变为 `"b"`字符串对象，并放入串池中

3、当执行到` ldc #4` 时，会把符号 `ab` 变为 `"ab"` 字符串对象，并放入串池中

最终`StringTable ["a", "b", "ab"]`

**注意**：字符串对象的创建都是懒惰的，只有当运行到那一行字符串且串池中不存在的时候（如 ldc #2）时，该字符串才会被创建并放入串池中。

```java
public class StringTableStudy {
	public static void main(String[] args) {
		String a = "a"; 
		String b = "b";
		String ab = "ab";
	}
}
```

常量池中的信息，都会被加载到运行时常量池中，但这是`a` 、`b`、 `ab` 仅是常量池中的符号，**还没有成为java字符串**

```java
0: ldc           #2                  // String a
2: astore_1
3: ldc           #3                  // String b
5: astore_2
6: ldc           #4                  // String ab
8: astore_3
9: return
```





**示例二**

1、通过拼接的方式来创建字符串的过程是：`StringBuilder().append(“a”).append(“b”).toString()`

2、最后的`toString`方法的返回值是一个新的字符串，但字符串的值和拼接的字符串一致，但是两个不同的字符串，一个存在于串池之中，一个存在于堆内存之中

```java
public class HelloWorld {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2; //new StringBuilder().append("a").append("2").toString()  new String("ab")
        System.out.println(s3 == s4); //false
		// 结果为false,因为s3是存在于串池之中，s4是由StringBuffer的toString方法所返回的一个对象，存在于堆内存之中
    }
}
```

```java
Code:
  stack=2, locals=5, args_size=1
   0: ldc           #2              // String a
   2: astore_1
   3: ldc           #3              // String b
   5: astore_2
   6: ldc           #4              // String ab
   8: astore_3
   9: new           #5              // class java/lang/StringBuilder
  12: dup
  13: invokespecial #6              // Method java/lang/StringBuilder."<init>":()V
  16: aload_1
  17: invokevirtual #7              // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  20: aload_2
  21: invokevirtual #7              // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  24: invokevirtual #8              // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
  27: astore        4
  29: return
```





**示例三**

1、使用拼接字符串常量的方法来创建新的字符串时，因为内容是常量，`javac`在编译期会进行优化，结果已在编译期确定为`ab`，而创建`ab`的时候已经在串池中放入了`"ab"`，所以`s5`直接从串池中获取值，和`s3`相等

2、使用拼接字符串变量的方法来创建新的字符串时，因为内容是变量，只能在运行期确定它的值，所以需要使用`StringBuilder`来创建

```java
public class HelloWorld {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2; //new StringBuilder().append("a").append("2").toString()  new String("ab")
        String s5 = "a" + "b";
        System.out.println(s5 == s3); //true
    }
}
```

```java
Code:
stack=2, locals=6, args_size=1
   0: ldc           #2              // String a
   2: astore_1
   3: ldc           #3              // String b
   5: astore_2
   6: ldc           #4              // String ab
   8: astore_3
   9: new           #5              // class java/lang/StringBuilder
  12: dup
  13: invokespecial #6              // Method java/lang/StringBuilder."<init>":()V
  16: aload_1
  17: invokevirtual #7              // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  20: aload_2
  21: invokevirtual #7              // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  24: invokevirtual #8              // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
  27: astore        4				 
  29: ldc           #4              // String ab  
  31: astore        5				// s5的ab初始化时直接从串池中获取字符串
  33: return
```





### 5.3 intern

**JDK1.8**

调用字符串对象的`intern`方法，会将该字符串对象尝试放入到串池中

- 如果串池中没有该字符串对象，就在常量池中创建一个指向该字符串对象的引用
- 如果有该字符串对象，即字符串常量池中保存了对应的字符串对象的引用，则放入失败

无论放入是否成功，都会返回**串池中**的字符串对象



**实例**

1、`String x = "ab"`，创建字符串`"ab"`，将该字符串放入串池中，此时串池内容为`["ab"]`

2、`String s = new String("a") + new String("b")`，堆区创建字符串`new String("a")`、`new String("b")`和`new String("ab")`，将`“a”`、`"b"`放入串池，此时串池内容`["ab", "a", "b"]`

3、`String s2 = s.intern()`，尝试将字符串`s`放入串池，但是现在串池已经有`ab`这个字符串了，所以放入失败，`s`指向堆区，但是返回的`s2`是串池的字符串`"ab"`

4、对于`new String("a")`这个语句创建了两个对象，第一个对象是`"a"`字符串存储在常量池，第二个对象是在Java堆中的String对象

```java
public static void main(String[] args) {
    String x = "ab";
    String s = new String("a") + new String("b");

    String s2 = s.intern(); // 将这个字符串对象尝试放入串池，现在串池有，放入失败
    System.out.println(s2 == x); // true
    System.out.println(s == x); // false
}
```

```java
public static void main(String[] args) {
    String s = new String("a") + new String("b");

    String s2 = s.intern(); // 将这个字符串对象尝试放入串池，现在串池没有，放入成功
    System.out.println(s2 == "ab"); // true
    System.out.println(s == "ab"); // true
}
```





**JDK1.6**

调用字符串对象的`intern`方法，会将该字符串对象尝试放入到串池中

- 如果串池中没有该字符串对象，会将该字符串对象复制一份，再放入到串池中【因为JDK1.6中，字符串常量池在方法区】，该对象仍然指向堆区地址
- 如果有该字符串对象，则放入失败

无论放入是否成功，都会返回**串池中**的字符串对象







**JDK1.8测试**

```java
public static void main(String[] args) {
    String s1 = "a";
    String s2 = "b";
    String s3 = "a" + "b";
    String s4 = s1 + s2;
    String s5 = "ab";
    String s6 = s4.intern();
    System.out.println(s3 == s4); // false
    System.out.println(s3 == s5); // true
    System.out.println(s3 == s6); // true

    String x2 = new String("c") + new String("d");
    String x1 = "cd";
    x2.intern();
    System.out.println(x1 == x2); // false

    String x4 = new String("e") + new String("f");
    x4.intern();
    String x3 = "ef";
    System.out.println(x3 == x4); // true
}
```







### 5.4 StringTable位置

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402042224348.png" style="zoom:67%;" />

JDK1.6 时，`StringTable`是属于**常量池**的一部分，在方法区中，但是方法区只有在`fullgc`时垃圾才会被回收，回收效率太低。因此从JDK1.7往后，`StringTable`放在**堆**中，`MinorGC`就会触发垃圾回收，减轻无用字符串对内存的占用。`StringTable`在内存紧张时，会发生垃圾回收







### 5.5 StringTable 性能调优

- `StringTable`是由`HashTable`实现的，所以可以适当增加`HashTable`桶的个数，来减少字符串放入串池所需要的时间【避免了Hash冲突】

  `-XX:StringTableSize=桶个数（最少设置为 1009 以上）`

- 考虑是否需要将字符串对象入池，可以通过 `intern` 方法减少重复入池，保证相同字符串在`StringTable`只保存一份





### 5.6 String占用内存大小

通过`new String()`的方式创建一个空字符串对象时，占用内存为40字节

1、String大小

- String对象头，12字节

- `int hash`，4字节

- `char value[] `引用，4字节

- 内存填充4字节

2、`value`数组对象头，12+4=16字节

因此空字符串占用内存为40字节，如果字符串有3个字符，就是48字节，即40+6+2(内存对齐)





## 6、直接内存

- 常见于 NIO 操作时，用于数据缓冲区
- 分配回收成本较高，但读写性能高
- 不受 JVM 内存回收管理





### 6.1 直接内存介绍

**文件读写过程**

我们读取写文件时，会先将文件读取到一块内存数组中，然后在将数据写到对应文件中，读取数据的过程如下所示

1、执行读取操作时，会先从用户态切换为内核态

2、将磁盘文件读到系统缓存区，然后将数据在读取到java缓冲区，最后可以被java程序所使用。读取操作需要进行两次，效率会很低

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402042254212.png" style="zoom:50%;" />



**DirectBuffer**

直接内存是操作系统和Java代码**都可以访问的一块区域**，无需将代码从系统内存复制到Java堆内存，从而提高了效率。使用直接内存后，用户态切换为内核态后，将数据从磁盘读取到直接内存中，Java程序就可以直接使用

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402042257587.png" style="zoom:50%;" />





### 6.2 内存溢出

直接内存虽然使用的是操作系统的内存，但也会发生内存溢出，当我们不停的分配直接内存，就会产生OOM

```java
public class test04 {
    static int _100MB = 1024 * 1024 * 100;
    public static void main(String[] args) throws IOException {
        List<ByteBuffer> list = new ArrayList<>();
        int i = 0;
        try {
            while (true) {
                ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_100MB);
                list.add(byteBuffer);
                i++;
            }
        } finally {
            System.out.println(i);
        }
    }
}

36
Exception in thread "main" java.lang.OutOfMemoryError: Direct buffer memory
	at java.nio.Bits.reserveMemory(Bits.java:694)
	at java.nio.DirectByteBuffer.<init>(DirectByteBuffer.java:123)
	at java.nio.ByteBuffer.allocateDirect(ByteBuffer.java:311)
	at com.xrj.Demo01.test04.main(test04.java:24)
```





### 6.3 内存释放原理

直接内存不属于JVM管理，因此Java的垃圾回收是不会回收直接内存的，直接内存的回收是通过`unsafe.freeMemory`来手动释放

```java
public class test04 {
    public static int _1GB = 1024 * 1024 * 1024;

    // 代码执行开始后，通过任务管理器可以看到有1G内存被分配，然后输入任意字符后继续执行，这块直接内存被释放
    public static void main(String[] args) throws IOException, NoSuchFieldException, IllegalAccessException {
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_1GB); // 直接内存分配1G
        System.out.println("分配完毕");
        System.in.read(); // 输入任意字符会继续执行
        System.out.println("开始释放");
        byteBuffer = null;
        System.gc(); // 手动 gc
        System.in.read();
    }
}
```



**ByteBuffer源码**

1、`ByteBuffer.allocateDirect(_1GB)`，分配直接内存，底层调用的是`DirectByteBuffer`，最终通过`base = unsafe.allocateMemory(size)`分配内存

2、`DirectByteBuffer`中还通过`Cleaner`绑定了一个回调方法，`Cleaner`是虚引用类型，当它所关联的对象被回收时，会触发该`Cleaner`的`clean`方法，执行回调任务。这个`Cleaner`关联的是this，即`ByteBuffer`，当`ByteBuffer`被回收时，就会触发这个回调方法，在回调方法中，就会执行`freeMemory`方法释放内存

3、这里调用了一个`Cleaner`的`create`方法，且后台线程还会对虚引用的对象监测，如果虚引用的实际对象（这里是`DirectByteBuffer`）被回收以后，就会调用`Cleaner`的`clean`方法，来清除直接内存中占用的内存

```java
public static ByteBuffer allocateDirect(int capacity) {
    return new DirectByteBuffer(capacity);
}
```

```java
DirectByteBuffer(int cap) {                   // package-private

    super(-1, 0, cap, cap);
    boolean pa = VM.isDirectMemoryPageAligned();
    int ps = Bits.pageSize();
    long size = Math.max(1L, (long)cap + (pa ? ps : 0));
    Bits.reserveMemory(size, cap);

    long base = 0;
    try {
        // 底层通过unsafe.allocateMemory(size)来分配内存
        base = unsafe.allocateMemory(size);
    } catch (OutOfMemoryError x) {
        Bits.unreserveMemory(size, cap);
        throw x;
    }
    unsafe.setMemory(base, size, (byte) 0);
    if (pa && (base % ps != 0)) {
        // Round up to page boundary
        address = base + ps - (base & (ps - 1));
    } else {
        address = base;
    }
    // 通过虚引用，来实现直接内存的释放，this为虚引用的实际对象, 第二个参数是一个回调，实现了 runnable 接口，run 方法中通过 unsafe 释放内存。
    cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
    att = null;
}
```

`Deallocator`是一个回调方法，他实现了`Runnable`接口，其中`run`方法中调用了`freeMemory`方法来释放内存

```java
private static class Deallocator implements Runnable {

    private static Unsafe unsafe = Unsafe.getUnsafe();

    private long address;
    private long size;
    private int capacity;

    private Deallocator(long address, long size, int capacity) {
        assert (address != 0);
        this.address = address;
        this.size = size;
        this.capacity = capacity;
    }

    public void run() {
        if (address == 0) {
            // Paranoia
            return;
        }
        unsafe.freeMemory(address);
        address = 0;
        Bits.unreserveMemory(size, capacity);
    }

}
```

```java
public void clean() {
    if (remove(this)) {
        try {
            this.thunk.run();
        } catch (final Throwable var2) {
            AccessController.doPrivileged(new PrivilegedAction<Void>() {
                public Void run() {
                    if (System.err != null) {
                        (new Error("Cleaner terminated abnormally", var2)).printStackTrace();
                    }

                    System.exit(1);
                    return null;
                }
            });
        }

    }
}
```



**总结**

- 使用了` Unsafe` 类来完成直接内存的分配回收，回收需要主动调用`freeMemory` 方法
- `ByteBuffer` 的实现内部使用了` Cleaner`（虚引用）来检测 `ByteBuffer` 。一旦`ByteBuffer `被垃圾回收，那么会由 `ReferenceHandler`（守护线程） 来调用 `Cleaner` 的` clean` 方法调用` freeMemory `来释放内存



**注意**

1、一般用 jvm 调优时，会加上如下参数：`-XX:+DisableExplicitGC `，这个参数的作用就是禁止我们手动的 GC，比如手动` System.gc() `无效，因为`System.gc()`是一种` full gc`，会回收新生代、老年代，会造成程序执行的时间比较长。所以jvm调优时，都会加上该参数。

2、加上该参数后，手动GC会失效，因此我们使用的直接内存，在内存空间充足时不会被释放，直到执行垃圾回收时，才会被释放。所以我们可以手动通过 `unsafe` 对象调用 `freeMemory `的方式释放内存。









# 二、垃圾回收

## 1、引用计数法

当一个对象被引用时，就将引用对象的值加一，当值为 0 时，就表示该对象不被引用，可以被垃圾收集器回收。但是会出现循环引用的问题，即A引用B，B引用A，导致这两个对象都无法被回收

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052144251.png)



## 2、可达性分析算法

JVM 中的垃圾回收器通过可达性分析来探索所有存活的对象，扫描堆中的对象，看能否沿着 GC Root 对象为起点的引用链找到该对象，如果找不到，则表示可以回收

可以作为 GC Root 的对象

- 虚拟机栈(栈帧中的局部变量表)中引用的对象
- 本地方法栈(Native 方法)中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 所有被同步锁持有的对象
- JNI（Java Native Interface）引用的对象





## 3、四种引用类型

### 3.1 强引用

只有所有 GC Roots 对象都不通过【强引用】引用该对象，该对象才能被垃圾回收。我们使用的大部分引用实际上都是强引用。如果一个对象具有强引用，当内存空间不足，Java 虚拟机宁愿抛出 `OutOfMemoryError` 错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。



### 3.2 软引用

**SoftReference**

如果一个对象只具有软引用，内存空间足够时，垃圾回收器就不会回收它，如果执行完垃圾回收后内存空间仍然不足，会再次发生垃圾回收，回收这些只有软引用的对象。

软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收，JAVA 虚拟机就会把这个软引用加入到与之关联的引用队列中。在引用队列中对这些软引用对象进行回收



### 3.3 弱引用

**WeakReference**

仅有弱引用引用该对象时，在垃圾回收时，无论内存是否充足，都会回收弱引用对象。

弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java 虚拟机就会把这个弱引用加入到与之关联的引用队列中。



### 3.4 虚引用

**PhantomReference**

必须配合引用队列使用，当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。

在 `ByteBuffer` 中，最后会通过`cleaner = Cleaner.create(this,newDeallocator(base, size, cap));`为`DirectByteBuffer`对象添加一个虚引用，当`DirectByteBuffer`被回收时，会将这个虚引用对象添加到引用队列，由 `Reference Handler` 线程调用虚引用相关方法释放直接内存



### 3.5 终结器引用

**FinalReference**）

每个Java对象都继承自Object父类，都会有一个finalize方法，当对象重写了`finalize`方法，并且没有强引用引用这个对象时，虚拟机会为这个对象创建一个终结器引用，当该对象被垃圾回收时，会将这个终结器引用加入引用队列（被引用对象暂时没有被回收），再由一个优先级很低的线程（执行机会很小），`Finalizer `线程去查看引用队列是否有终结器引用，如果有的话，就会过终结器引用找到被引用对象并调用它的 finalize 方法，第二次 GC 时才能回收该对象。

不推荐使用`finalize`方法释放资源：`Finalizer`线程执行机会很小，会导致`finalize`方法迟迟不被执行，导致该对象内存迟迟不被释放



### 3.6 软引用示例

```java
public class SoftReferenceTest {

    public static int _4MB = 4 * 1024 * 1024;

    public static void main(String[] args) throws IOException {
        method1();
        method2();
    }

    // 设置 -Xmx20m , 演示堆内存不足,
    public static void method1() throws IOException {
        ArrayList<byte[]> list = new ArrayList<>();

        for(int i = 0; i < 5; i++) {
            list.add(new byte[_4MB]);
            System.out.println(i);
        }
        System.in.read();
    }

    // 演示 软引用
    public static void method2() throws IOException {
        ArrayList<SoftReference<byte[]>> list = new ArrayList<>();
        for(int i = 0; i < 5; i++) {
            SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB]);
            System.out.println(ref.get());
            list.add(ref);
            System.out.println(list.size());
        }
        System.out.println("循环结束：" + list.size());
        for(SoftReference<byte[]> ref : list) {
            System.out.println(ref.get());
        }
    }
}
```

1、通过`-Xmx20m` 设置对内存大小为20M，执行`method1()`方法，会出现堆内存不足的异常，因为` mehtod1 `中的 `list` 都是强引用

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052146913.png" style="zoom:50%;" />

2、执行`method2()`方法，我们添加虚拟机参数 `-Xmx20m -XX:+PrintGCDetails -verbose:gc`，设置堆内存大小为20M，同时打印垃圾回收的信息。

在`method2()`方法中，首先list通过强引用引用了`SoftReference`，然后`SoftReference`通过软引用引用了byte数组，当内存不够时，就会将软引用的对象回收掉

**输出结果**

```java
[B@1540e19d
1
[B@677327b6
2

[GC (Allocation Failure) [PSYoungGen: 5631K->496K(6144K)] 13823K->12816K(19968K), 0.0042306 secs] [Times: user=0.03 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 496K->0K(6144K)] [ParOldGen: 12320K->12647K(13824K)] 12816K->12647K(19968K), [Metaspace: 3156K->3156K(1056768K)], 0.0042791 secs] [Times: user=0.03 sys=0.01, real=0.01 secs] 
[B@14ae5a5
3
[B@7f31245a
4
[Full GC (Ergonomics) [PSYoungGen: 4269K->4097K(6144K)] [ParOldGen: 12647K->12641K(13824K)] 16916K->16738K(19968K), [Metaspace: 3173K->3173K(1056768K)], 0.0032651 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 4097K->0K(6144K)] [ParOldGen: 12641K->336K(10752K)] 16738K->336K(16896K), [Metaspace: 3173K->3173K(1056768K)], 0.0031070 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
[B@6d6f6e28
5
循环结束：5
null
null
null
null
[B@6d6f6e28
Heap
 PSYoungGen      total 6144K, used 4414K [0x00000007bf980000, 0x00000007c0000000, 0x00000007c0000000)
  eden space 5632K, 78% used [0x00000007bf980000,0x00000007bfdcfb08,0x00000007bff00000)
  from space 512K, 0% used [0x00000007bff00000,0x00000007bff00000,0x00000007bff80000)
  to   space 512K, 0% used [0x00000007bff80000,0x00000007bff80000,0x00000007c0000000)
 ParOldGen       total 10752K, used 336K [0x00000007bec00000, 0x00000007bf680000, 0x00000007bf980000)
  object space 10752K, 3% used [0x00000007bec00000,0x00000007bec54088,0x00000007bf680000)
 Metaspace       used 3189K, capacity 4564K, committed 4864K, reserved 1056768K
  class space    used 349K, capacity 388K, committed 512K, reserved 1048576K
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052147309.png)





**配合引用队列使用**

上面的代码中，当软引用引用的对象被回收了，但是软引用还存在，所以，一般软引用需要搭配一个引用队列使用，当其引用的对象被回收后，这个软引用会自动加入引用队列中，然后我们可以通过引用队列将这些软引用回收掉

```java
public static void method3() throws IOException {
    ArrayList<SoftReference<byte[]>> list = new ArrayList<>();
    // 引用队列
    ReferenceQueue<byte[]> queue = new ReferenceQueue<>();

    for(int i = 0; i < 5; i++) {
        // 关联了引用队列，当软引用所关联的 byte[] 被回收时，软引用自己会加入到 queue 中去
        SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB], queue);
        System.out.println(ref.get());
        list.add(ref);
        System.out.println(list.size());
    }

    // 从队列中获取无用的 软引用对象，并移除
    Reference<? extends byte[]> poll = queue.poll();
    while(poll != null) {
        list.remove(poll);
        poll = queue.poll();
    }

    System.out.println("=====================");
    for(SoftReference<byte[]> ref : list) {
        System.out.println(ref.get());
    }
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052148409.png" style="zoom:50%;" />





### 3.7 弱引用示例

```java
public class WeakReferenceTest {

    public static void main(String[] args) {
        // method1();
        method2();
    }

    public static int _4MB = 4 * 1024 *1024;

    // 演示 弱引用
    public static void method1() {
        List<WeakReference<byte[]>> list = new ArrayList<>();
        for(int i = 0; i < 10; i++) {
            WeakReference<byte[]> weakReference = new WeakReference<>(new byte[_4MB]);
            list.add(weakReference);

            for(WeakReference<byte[]> wake : list) {
                System.out.print(wake.get() + ",");
            }
            System.out.println();
        }
    }

    // 演示 弱引用搭配 引用队列
    public static void method2() {
        List<WeakReference<byte[]>> list = new ArrayList<>();
        ReferenceQueue<byte[]> queue = new ReferenceQueue<>();

        for(int i = 0; i < 9; i++) {
            WeakReference<byte[]> weakReference = new WeakReference<>(new byte[_4MB], queue);
            list.add(weakReference);
            for(WeakReference<byte[]> wake : list) {
                System.out.print(wake.get() + ",");
            }
            System.out.println();
        }
        System.out.println("===========================================");
        Reference<? extends byte[]> poll = queue.poll();
        while (poll != null) {
            list.remove(poll);
            poll = queue.poll();
        }
        for(WeakReference<byte[]> wake : list) {
            System.out.print(wake.get() + ",");
        }
    }
}
```

**method2执行结果**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052148341.png)

由于弱引用的特性，当垃圾回收器执行垃圾回收时，这些字节数组对象可能会被回收。但是，垃圾回收器的具体行为是不确定的，它可能会根据不同的策略和条件来决定何时进行垃圾回收。具体的回收时机取决于垃圾回收器的算法、内存压力和对象的可达性等因素。FullGC会将所有仅被弱引用所引用的对象都回收掉





## 4、垃圾回收算法

### 4.1 标记清除

先通过可达性分析算法标记出所有不需要回收的对象，然后将没有标记的对象回收掉。清除的时候，不是将这块内存释放掉，而是将这块内存的起始地址和结束地址记录在一个空闲列表里，下次分配内存时，直接使用这块内存就可以了。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052150584.png)

- **效率问题**：标记和清除两个过程效率都不高。
- **空间问题**：标记清除后会产生大量不连续的内存碎片



### 4.2 标记整理

标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象回收，而是让所有存活的对象向一端移动，然后直接清理掉端边界以外的内存。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052150573.png)

- 速度慢
- 没有内存碎片



### 4.3 复制算法

将内存分为大小相同的两块，每次使用其中的一块。当这一块的内存使用完后，就将还存活的对象复制到另一块去，然后再把使用的空间一次清理掉。这样就使每次的内存回收都是对内存区间的一半进行回收。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052151557.png)

- 不会有内存碎片
- 每次只能使用一半内存
- 不适合老年代，如果存活对象数量比较大，复制性能会变得很差。



### 4.4 分代垃圾回收

在新生代中，每次收集都会有大量对象死去，所以可以选择”标记-复制“算法，只需要付出少量对象的复制成本就可以完成每次垃圾收集。而老年代的对象存活几率是比较高的，而且没有额外的空间对它进行分配担保，所以我们必须选择“标记-清除”或“标记-整理”算法进行垃圾收集。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052151550.png)

1、新创建的对象首先分配在 eden 区

2、新生代空间不足时，触发 minor gc ，eden 区 和 from 区存活的对象使用 copy 复制到 to 中，存活的对象年龄加一，然后交换 from和to

3、minor gc 会引发 stop the world，暂停其他线程，等垃圾回收结束后，恢复用户线程运行

4、当幸存区对象的寿命超过阈值时，会晋升到老年代，最大的寿命是 15（对象头只有4bit存储gc年龄）

5、minor gc后，如果老年代空间不足，那么就触发 full fc ，停止的时间更长





## 5、JVM参数

| 含义               | 参数                                                         |
| ------------------ | ------------------------------------------------------------ |
| 堆初始大小         | -Xms                                                         |
| 堆最大大小         | -Xmx 或 -XX:MaxHeapSize=size                                 |
| 新生代大小         | -Xmn 或 (-XX:NewSize=size + -XX:MaxNewSize=size )            |
| 幸存区比例（动态） | -XX:InitialSurvivorRatio=ratio 和 -XX:+UseAdaptiveSizePolicy |
| 幸存区比例         | -XX:SurvivorRatio=ratio（默认为8，即Eden和s0、s1比例为8:1:1） |
| 晋升阈值           | -XX:MaxTenuringThreshold=threshold                           |
| 晋升详情           | -XX:+PrintTenuringDistribution                               |
| GC详情             | -XX:+PrintGCDetails -verbose:gc                              |
| FullGC 前 MinorGC  | -XX:+ScavengeBeforeFullGC                                    |



**GC分析**

通过下边的代码，给` list` 分配内存，来观察 新生代和老年代的情况，什么时候触发 `minor gc`，什么时候触发 `full gc `等情况，使用前需要设置 jvm 参数。

```java
public class GCTest {
    private static final int _512KB = 512 * 1024;
    private static final int _1MB = 1024 * 1024;
    private static final int _6MB = 6 * 1024 * 1024;
    private static final int _7MB = 7 * 1024 * 1024;
    private static final int _8MB = 8 * 1024 * 1024;

    // -Xms20m -Xmx20m -Xmn10m -XX:+UseSerialGC -XX:+PrintGCDetails -verbose:gc
    public static void main(String[] args) {
        List<byte[]> list = new ArrayList<>();
        list.add(new byte[_6MB]);
        list.add(new byte[_512KB]);
        list.add(new byte[_6MB]);
        list.add(new byte[_512KB]);
        list.add(new byte[_6MB]);
    }
}
```





## 6、垃圾回收器

- 并行收集：指多条垃圾收集线程并行工作，但此时用户线程仍处于等待状态。
- 并发收集：指用户线程与垃圾收集线程同时工作（不一定是并行的可能会交替执行）。用户程序在继续运行，而垃圾收集程序运行在另一个 CPU 上
- 吞吐量：即 CPU 用于运行用户代码的时间与 CPU 总消耗时间的比值（吞吐量 = 运行用户代码时间 / ( 运行用户代码时间 + 垃圾收集时间 )）。例如：虚拟机共运行 100 分钟，垃圾收集器花掉 1 分钟，那么吞吐量就是 99% 。



### 6.1 串行

- 单线程
- 堆内存较少

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052152899.png)

```java
-XX:+UseSerialGC=serial + serialOld
```



**安全点**：让其他线程都在这个点停下来，以免垃圾回收时移动对象地址，使得其他线程找不到被移动的对象

因为是串行的，所以只有一个垃圾回收线程。且在该线程执行回收工作时，其他线程进入阻塞状态



**1、Serial**

Serial 收集器是最基本的、发展历史最悠久的收集器

特点：单线程、简单高效（与其他收集器的单线程相比），采用**复制**算法。对于限定单个 CPU 的环境来说，Serial 收集器由于没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程收集效率。收集器进行垃圾回收时，必须暂停其他所有的工作线程，直到它结束（Stop The World）



**2、ParNew**

ParNew 收集器其实就是 Serial 收集器的多线程版本

特点：多线程、ParNew 收集器默认开启的收集线程数与CPU的数量相同，在 CPU 非常多的环境中，可以使用 -XX:ParallelGCThreads 参数来限制垃圾收集的线程数。和 Serial 收集器一样存在 Stop The World 问题，工作在新生代



**3、Serial Old**

Serial Old 是 Serial 收集器的老年代版本

特点：同样是单线程收集器，采用**标记-整理**算法



### 6.2 吞吐量优先

- 多线程
- 堆内存较大，多核 cpu
- 让单位时间内，STW 的时间最短

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052153899.png)

```java
-XX:+UseParallelGC ~ -XX:+UsePrallerOldGC // 打开其中任意一个，另一个都会生效
-XX:+UseAdaptiveSizePolicy
-XX:GCTimeRatio=ratio // 1/(1+radio)
-XX:MaxGCPauseMillis=ms // 200ms
-XX:ParallelGCThreads=n
```



**1、parallel Scavenge**

与吞吐量关系密切，故也称为吞吐量优先收集器

特点：属于新生代收集器，采用**复制**算法（用到了新生代的幸存区），是并行的多线程收集器（与 ParNew 收集器类似）。该收集器的目标是达到一个可控制的吞吐量。还可以进行 GC自适应调节策略（与 ParNew 收集器最重要的一个区别）

**GC自适应调节策略：**

`Parallel Scavenge` 收集器可设置` -XX:+UseAdptiveSizePolicy` 参数。

当开关打开时不需要手动指定新生代大小(`-Xmn`)、Eden与Survivor 区的比例（`-XX:SurvivorRation`）、晋升老年代的对象年龄 （`-XX:PretenureSizeThreshold`）、堆区大小等，虚拟机会根据系统的运行状况收集性能监控信息，动态设置这些参数以提供最优的停顿时间和最高的吞吐量，这种调节方式称为 GC 的自适应调节策略。



Parallel Scavenge 收集器使用两个参数控制吞吐量：

- `-XX:MaxGCPauseMillis=ms` 控制最大的垃圾收集停顿时间（默认200ms）
- `-XX:GCTimeRatio=rario` 直接设置吞吐量的大小

这两个参数的设置是矛盾的，如果想要最大垃圾收集停顿时间较小，那么就表示堆区空间比较小，但是这样的话，需要频繁的GC，导致吞吐量变小。如果希望吞吐量大，那么堆区空间就比较大，这样GC次数较少，但是会导致一次GC消耗时间较长。因此需要设置合理的值。



**2、Parallel Old** 

`Parallel Scavenge`的老年代版本

特点：多线程，采用**标记-整理**算法（老年代没有幸存区）





### 6.3 响应时间优先

- 多线程
- 堆内存较大，多核 cpu
- 尽可能让 STW 的单次时间最短 

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402052159389.png)

```java
-XX:+UseConcMarkSweepGC ~ -XX:+UseParNewGC ~ SerialOld
-XX:ParallelGCThreads=n ~ -XX:ConcGCThreads=threads
-XX:CMSInitiatingOccupancyFraction=percent
-XX:+CMSScavengeBeforeRemark
```



**CMS 收集器**

Concurrent Mark Sweep，一种以获取最短回收停顿时间为目标的**老年代收集器**，与其配合的是ParNew收集器，工作在新生代

**特点**：基于**标记-清除**算法实现，并发收集【与用户线程同时执行】，因此他的STW时间比较短，但是由于是并发清除，所以会产生内存碎片

**应用场景**：适用于注重服务的响应速度，希望系统停顿时间最短，给用户带来更好的体验等场景下。如 web 程序、b/s 服务



**CMS 收集器运行过程**

1、初始标记：标记 GC Roots 能直接到的对象。速度很快但是仍存在 Stop The World 问题。

2、并发标记：进行 GC Roots Tracing 的过程，找出存活对象且用户线程可并发执行。

3、重新标记：为了修正并发标记期间因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录。仍然存在 Stop The World 问题

4、并发清除：对标记的对象进行清除回收。清除的过程中，可能仍然会有新的垃圾产生，这些垃圾称为浮动垃圾，在下一次垃圾回收时才会被清除掉。



**相关参数设置**

```java
// 并行的垃圾回收线程数，初始标记和重新标记时的线程数
-XX:ParallelGCThreads=n   
// 并发的GC线程数，一般为并行线程数的1/4，即1个线程做垃圾回收，剩下3个线程给用户线程使用
-XX:ConcGCThreads=threads 
```

垃圾回收时是和用户线程一起并发进行的，例如4核CPU，有1个是执行垃圾回收的，剩下3个是用户线程，所以吞吐量会变低

```java
// 当内存占用比例达到percent时，就进行CMS垃圾回收，因为并发清除时会产生浮动垃圾，所以预留一些空间给浮动垃圾使用
-XX:CMSInitiatingOccupancyFraction=percent 
// 重新标记时，有可能新生代对象会引用老年代对象，重新标记会扫描整个堆，但是新生代很多都会作为垃圾直接被回收掉，所以重新标记前，重新对新生代做一次垃圾回收，这样，新生代对象少了，重新标记时扫描的对象也会变少
-XX:+CMSScavengeBeforeRemark 
```



**并发失败**

因为CMS使用的是标记清除法，所以会存在内存碎片，当正在进行并发清除时（FullGC），也会产生浮动垃圾。如果此时用户需要存入一个很大的对象时，新生代放不下去，老年代有浮动垃圾也放不进去，就会退化为 serial Old 收集器，将老年代垃圾进行标记-整理，这种情况会非常耗费时间



### 6.4 G1

Garbage First，JDK9默认使用


**适用场景：**

- 同时注重吞吐量和低延迟（响应时间）
- 超大堆内存，会将堆内存划分为多个大小相等的区域（Region）
- 整体上是标记-整理算法，两个区域之间是复制算法

**相关参数：**

```java
-XX:+UseG1GC
-XX:G1HeapRegionSize=size  // 设置Region大小，1，2，4，8，16
-XX:MaxGCPauseMillis=time  // 最大等待时间
```



**垃圾回收过程**

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707209014739-f1a241c7-f727-41a2-a66a-6a53b7dd199b.png" alt="img" style="zoom:57%;" />

- `Young Collection`：对新生代垃圾收集
- `Young Collection + Concurrent Mark`：如果老年代内存到达一定的阈值了，新生代垃圾收集同时会执行一些并发的标记。
- `Mixed Collection`：会对新生代 + 老年代 + 幸存区等进行混合收集，然后收集结束，会重新进入新生代收集。





**Young Collection**

每个`Region`都可以作为Eden区、surviver区、老年代**。**将堆空间划分连续几个不同小区间，每一个小区间独立回收，可以控制一次回收多少个小区间，方便控制 GC 产生的停顿时间。新生代收集会产生 STW 

E：eden，S：幸存区，O：老年代

执行`YoungGC`时，Eden区以及幸存区存活的对象会放入另一个幸存区。如果对象存活时间达到阈值，会放入老年区

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707209992540-cdba2045-8e02-44a6-a427-1122fbce7794.png)





**Young Collection + CM**

在 Young GC 时会进行 GC Root 的初始标记

当老年代占用堆空间比例达到阈值时，进行并发标记（不会STW），由下面的 JVM 参数决定 

`-XX:InitiatingHeapOccupancyPercent=percent` （默认45%）





**Mixed Collection**

会对 Eden、 Surviver、 Old 进行回收

- 最终标记会 STW
- 拷贝存活会 STW

`-XX:MaxGCPauseMills=xxms `用于指定最长的停顿时间

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707209579030-454d545a-c8fa-48d1-b5f1-add13b9795b8.png" alt="img" style="zoom:50%;" />

`Mixed Collection`只会收集部分的老年代：因为指定了最大停顿时间，如果对所有老年代都进行回收，耗时可能过高。为了保证时间不超过设定的停顿时间，会回收最有价值的老年代（回收后，能够得到更多内存）





**Full GC：**

- SerialGC

新生代内存不足发生的垃圾收集 - minor gc

老年代内存不足发生的垃圾收集 - full gc

- ParallelGC

新生代内存不足发生的垃圾收集 - minor gc

老年代内存不足发生的垃圾收集 - full gc

- CMS

新生代内存不足发生的垃圾收集 - minor gc

老年代内存不足

- G1

新生代内存不足发生的垃圾收集 - minor gc

老年代内存不足（老年代所占内存超过阈值）



对于CMS和G1来说：

- 如果垃圾产生速度慢于垃圾回收速度，不会触发Full GC，还是并发地进行清理
- 如果垃圾产生速度快于垃圾回收速度，便会触发Full GC，然后退化成 serial Old 收集器串行的收集，就会导致停顿的时候长





#### 6.4.1 Young GC跨代引用

- JVM的垃圾收集分为新生代和老年代，新生代的对象很快就会死亡，老年代的对象存活时间较长。因此JVM对新生代的垃圾收集频率比老年代的垃圾收集频率要高

- 但是JVM中存在跨代引用的现象，老年代的对象引用了新生代的对象，或者是新生代的对象引用了老年代的对象。JVM在进行新生代的垃圾回收时，只会对新生代进行扫描，不会扫描老年代，那么如果一个新生代对象仅存在老年代对象对它的引用时，GC是扫描不到它的，自然就不会被标记为存活对象。那么该对象就会被当成垃圾对象回收，当我们通过老年代对象指向该年轻代对象的引用访问该年轻代对象时，就会发生NPE

- 因此GC在进行新生代的垃圾回收时，不能只扫描新生代的对象，还要扫描老年代中存在跨代引用的对象。如果直接把整个老年代都扫描的话，新生代GC的性能就很低，因此JVM定义了一个记忆集（Remember Set），记录非收集区到收集区的引用。比如现在要对新生代进行垃圾回收，那么此时新生代就是收集区，由于此时不需要对老年代进行垃圾收集，所以老年代就是非收集区。

  <img src="https://i-blog.csdnimg.cn/blog_migrate/2d8dcde37f75ce6f12a1ef25886c381d.png" alt="在这里插入图片描述" style="zoom:50%;" />

- 记忆集只是一个抽象的概念，不同的Java虚拟机对记忆集有不同的实现，比如Hotspot虚拟机对记忆集的实现就是卡表。Hotspot把内存划分为一块一块的区域，这些区域叫做卡页（512k），然后定义一个字节数组`CART_TABLE[]`，这个字节数组就是卡表，卡表中的每一个byte，对应内存中的一个卡页。如果老年代中的一个对象引用了年轻代中的一个对象，那么该老年代对象所在的卡页对应在卡表中的那一个byte就会被修改为1，表示脏卡

  <img src="https://i-blog.csdnimg.cn/blog_migrate/37d15f635f186f9a148ea1d5fa234246.png" alt="在这里插入图片描述" style="zoom:50%;" />

  <img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707210737863-43f1c022-a213-4677-86b1-921557cf93cd.png" alt="img" style="zoom: 38%;" />                            <img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707210745254-b3c6d99f-e19e-4766-893f-cad0ee5a439d.png" alt="img" style="zoom: 35%;" />

- 当JVM要进行新生代垃圾回收时，通过`CART_TABLE`即可得知老年代区域中哪些卡页是脏页，就会扫描老年代中的脏卡，把脏卡中的对象加入到GC Roots中。这样，被老年代对象引用的新生代对象也可以被扫描到，就不会被当成垃圾对象被回收了。



> 卡表：老年代被划为一个个卡表
>
> 脏卡：老年代被划分为多个区域（一个区域512K），如果该区域引用了新生代对象，则该区域被称为脏卡
>
> `Remembered Set`：`Remembered Set` 存在于E（新生代）中，用于保存新生代对象对应的脏卡
>
> 在引用变更时通过`post-write barried` + `dirty card queue``
>
>  ``concurrent refinement threads `更新 `Remembered Set`

​                          



#### 6.4.2 JDK8u20 字符串去重

- 优点：节省大量内存
- 缺点：略微多占用了 cpu 时间，新生代回收时间略微增加



`-XX:+UseStringDeduplication`（默认开启）

**示例**

```java
String s1 = new String("hello"); // char[]{'h','e','l','l','o'}
String s2 = new String("hello"); // char[]{'h','e','l','l','o'}
```

- 将所有新分配的字符串（底层是` char[]` ）放入一个队列
- 当新生代回收时，G1 并发检查是否有重复的字符串
- 如果字符串的值一样，就让他们引用同一个char数组
- 与` String.intern() `的区别
  - `String.intern()` 关注的是字符串对象
  - 字符串去重关注的是 `char[]`
  - 在 JVM 内部，使用了不同的字符串标





#### 6.4.3 JDK 8u40 并发标记类卸载

在并发标记阶段结束以后，就能知道哪些类不再被使用。如果一个类加载器的所有类都不在使用，则卸载它所加载的所有类。一般都是自定义类加载器加载的类被回收。`-XX:+ClassUnloadingWithConcurrentMark` 默认开启

> 方法区主要回收的是无用的类，判定一个类是否是“无用的类”需要同时满足下面 3 个条件：
>
> - 该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。
> - 加载该类的 ClassLoader 已经被回收。
> - 该类对应的 `java.lang.Class `对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。
>
> 虚拟机可以对满足上述 3 个条件的无用类进行回收，这里说的仅仅是“可以”，而并不是和对象一样不使用了就会必然被回收。





#### 6.4.4 JDK 8u60 回收巨型对象

- 一个对象大于region的一半时，就称为巨型对象
- G1不会对巨型对象进行拷贝
- 回收时被优先考虑
- G1会跟踪老年代所有`incoming`引用（对象的脏卡引用数），老年代`incoming`引用为0的巨型对象就可以在新生代垃圾回收时处理掉

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707213411863-6ded7440-a5df-4527-9fd4-61bdc45959b8.png" alt="img" style="zoom:50%;" />





#### 6.4.5 JDK 9 并发标记起始时间的调整

- 并发标记必须在堆空间占满前完成，否则退化为 FullGC
- JDK 9 之前需要使用 `-XX:InitiatingHeapOccupancyPercent`，指定堆内存占用达到多少比例开始执行垃圾回收
- JDK 9 可以动态调整
  - `-XX:InitiatingHeapOccupancyPercent` 用来设置初始值
  - 进行数据采样并动态调整
  - 总会添加一个安全的空档空间






### 6.5 GC调优

查看虚拟机参数：

```java
D:\JavaJDK1.8\bin\java  -XX:+PrintFlagsFinal -version | findstr "GC"
```

可以根据参数去查询具体的信息



**1、调优领域**

内存、锁竞争、cpu 占用、io、gc



**2、确定目标**

根据目标是低延迟还是高吞吐量，选择合适的垃圾回收器。例如高吞吐量，就选择ParallelGC，低延迟就选择CMS，适中就选择G1



**3、最快的 GC 就是不发生GC**

查看 Full GC 前后的内存占用，考虑以下几个问题

- 数据是不是太多？
  - 例如是否存在 `resultSet = statement.executeQuery(“select * from 大表”) ` 这种查询数据很多的sql语句，最好使用limit关键字，限制查询出的结果数量

- 数据表示是否太臃肿
  - 查询一个对象时，是否关联很多无用信息
  - 对象大小，比如`Integer`，对象头16字节，对象体4字节，加上内存对齐就是24字节，而 `int`只有4字节

- 是否存在内存泄漏
  - 例如代码中使用Map做缓存，当Map中数据越来越多，就会产生OOM，此时可以使用软引用或者弱引用，或者使用第三方缓存实现




**4、新生代调优**

新生代的特点

- 所有的 new 操作分配内存是非常快的
  - new一个对象时，这个对象会在Eden区分配，分配速度非常快，因为每个线程都会在Eden区中分配一块私有的区域，称为TLAB（`thread-lcoal allocation buffer`）。当new一个对象时，会先检查TLAB中内存是否充足，充足的话就在这块空间分配。因为对象内存分配也存在线程安全问题，所以也要做线程并发安全的保护，这是由JVM来做的。而TLAB就可以减少这种冲突，因为是每个线程私有的内存，所以不需要考虑并发安全问题。如果TLAB内存不够，就通过CAS+重试机制进行内存分配

- 死亡对象回收零代价
  - 新生代发生垃圾回收时，这些垃圾回收器都是采用的复制算法，即把Eden区、s0区中存活的对象复制到s1中去，复制完，Eden、s0区内存就被释放出来，因此死亡对象回收代价为0

- 大部分对象用过即死
- Minor GC 所用时间远小于 Full GC



**5、新生代内存越大越好吗？**

- 新生代内存太小：频繁触发 Minor GC ，会 STW ，会使得吞吐量下降
- 新生代内存太大：老年代内存占比有所降低，会更频繁地触发 Full GC。而且触发 Minor GC 时，清理新生代所花费的时间会更长

新生代内存设置为能容纳[并发量*(请求-响应)]的数据为宜





**6、幸存区调优**

幸存区需要能够保存 【当前活跃对象+需要晋升的对象】，当前活跃对象时现在还未死亡，但是马上就会被回收

- 如果幸存区过小，JVM会动态调整对象晋升老年代的阈值，可能导致一个年龄很小的对象晋升到了老年代，但是很快就死亡了，需要等到FullGC时才能被回收
- 晋升阈值配置得当，让长时间存活的对象尽快晋升，否则一个长时间存活的对象，每次MinorGC时都会进行复制，消耗性能
  - `-XX:MaxTenuringThreshold=threshold`，设置晋升年龄
  - `-XX:+PrintTenuringDistrubution`，打印对象晋升信息






**7、老年代调优**

以 CMS 为例：

- CMS 的老年代内存越大越好，这样避免由于浮动垃圾引起的并发失败问题
- 先尝试不做调优，如果没有 Full GC 就不用管，否者先尝试调优新生代。
- 观察发现 Full GC 时老年代内存占用，将老年代内存预设调大 `1/4 ~ 1/3`
- `-XX:CMSInitiatingOccupancyFraction=percent`，设置阈值，当堆内存达到一定比例时就进行垃圾回收





**8、gc调优案例**

1、Full GC 和 Minor GC 频繁
查看堆内存大小，可能是新生代空间设置太小。因为新生代空间过小，导致新生代频繁GC，然后短时间内会死亡的对象还未死亡时，会进入幸存区，而幸存区空间不足时，JVM会降低进入老年代的阈值，导致大量短时间内死亡的对象进入老年代，老年代内存满了就触发FullGC。

可以调大新生代内存，当新生代内存空间变大后，新生代GC频率就会降低，这样短时间内死亡的对象就不会进入幸存区，在MinorGC时就会被回收掉。还可以调整进入老年代的年龄阈值，避免短时间内死亡的对象进入老年区，这样就不会触发FullGC。



2、请求高峰期发生 Full GC，单次暂停时间特别长（CMS）

可以打印CMS垃圾回收的详细信息，分析哪个阶段消耗的时间长，如果请求高峰期时，创建很多新对象，这些对象又被黑色对象所引用，那么重新标记阶段就会重新扫描这些对象，如果新创建的对象过多，那么就会导致STW时间过长，可以设置`-XX:+CMSScavengeBeforeRemark` ，即重新标记之前，先做一次MinorGC，这样重新标记扫描的对象就会变少



3、老年代充裕情况下，发生 Full GC（jdk1.7）

JDK1.7时，方法区在永久代，永久代空间是有限的，可能导致FullGC。而JDK1.8使用的是元空间，就避免了这种情况





## 7、三色标记法

从GC Roots开始扫描

- 黑色：该对象被垃圾回收器访问过，且所有的直接引用都被扫描过

- 灰色：该对象被垃圾回收器访问过，至少一个直接引用还未被扫描过

- 白色：没有被垃圾回收器访问过



如果整个标记过程是STW的，那么没有任何问题，但是**并发标记**的过程中，用户线程也在运行，那么对象引用关系就可能发生改变，进而导致两个问题出现。主要的改变有以下两种情况

- 非垃圾变成了垃圾：某个黑色对象被断开引用，因为该对象被标记为黑色，所以他不会被回收，也就是浮动垃圾。影响不大，可以在下次gc时清理
- 垃圾变成了非垃圾：因为是并发清理，当gc线程执行完，用户线程抢占到cpu时间片时，将一个灰色对象引用的白色对象断开，然后连接到一个黑色对象上，此时这个白色对象就不会被扫描到了，在并发清理阶段会被回收掉



### 7.1 读写屏障

这里的屏障和并发编程中的屏障是两回事。这里的屏障就是在读写操作前后插入一段代码，用于记录一些信息、保存某些数据等，概念类似于AOP。



### 7.2 增量更新

CMS基于增量更新的方式保证对象不被回收

- 增量更新是站在新增引用的对象的角度来解决问题。就是在赋值操作之后添加一个写屏障，当黑色对象新增一个白色对象的引用时（`A.f = F`），就通过写屏障将这个引用关系记录下来。然后在重新标记阶段，再以这些引用关系中的黑色对象为根，再扫描一次，以此保证不会漏标。

  <img src="https://i-blog.csdnimg.cn/blog_migrate/5bc8dff52652bcef4a3ba1b2213a5090.png" alt="img" style="zoom:50%;" />

- 实现方式就是在重新标记阶段直接把记录的对象（`A`）变为灰色，放入队列中，再扫描一遍。

- 注意，在重新标记阶段如果用户线程还是继续执行，那么这个GC可能永远也执行不完，所以**重新标记需要STW**，但是这个时间消耗不会太久。





### 7.3 原始快照

G1基于原始快照的方式保证对象不被回收

- 原始快照是站在减少引用对象（`B.f=null`，即B对象）的角度来解决问题。在赋值操作（这里是置空）之前添加一个写屏障，在写屏障中记录被置空的对象引用。即将断开引用的白色对象记录下来

  <img src="https://i-blog.csdnimg.cn/blog_migrate/bba7cf62914a5c99f5c90034d6fd386e.png" alt="在这里插入图片描述" style="zoom:50%;" />

- 在最终标记阶段再以白色对象为根，进行扫描，把原来的白色对象进行标记。（这会产生浮动垃圾的问题，就比如原先这个灰色对象指向白色对象的引用就是应用程序想断开的，但是被使用原始快照算法的垃圾收集器记录了下来，误以为是不该回收的）





### 7.4 对比

- 原始快照相比增量更新来说，会产生更多的浮动垃圾。可能断开的白色对象是真的不需要了，但是最终标记阶段又将其进行了标记，变成浮动垃圾

- 原始快照相比增量更新来说，扫描的对象更少，因为他是直接从删除的白色对象开始扫描的





# 三、类加载与字节码技术

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707389105426-d4cd7fb9-f80e-4abe-b970-6fa7c8add4b9.png" alt="img" style="zoom:67%;" />



## 1、类文件结构

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("hello world");
    }
}
```

执行`javac -parameters -d . HellowWorld.java ` 编译后的到的字节码文件

```java
0000000 ca fe ba be 00 00 00 34 00 23 0a 00 06 00 15 09 
0000020 00 16 00 17 08 00 18 0a 00 19 00 1a 07 00 1b 07 
0000040 00 1c 01 00 06 3c 69 6e 69 74 3e 01 00 03 28 29 
0000060 56 01 00 04 43 6f 64 65 01 00 0f 4c 69 6e 65 4e 
0000100 75 6d 62 65 72 54 61 62 6c 65 01 00 12 4c 6f 63 
0000120 61 6c 56 61 72 69 61 62 6c 65 54 61 62 6c 65 01 
0000140 00 04 74 68 69 73 01 00 1d 4c 63 6e 2f 69 74 63 
0000160 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c 6f 
0000200 57 6f 72 6c 64 3b 01 00 04 6d 61 69 6e 01 00 16 
0000220 28 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72 
0000240 69 6e 67 3b 29 56 01 00 04 61 72 67 73 01 00 13 
0000260 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72 69 
0000300 6e 67 3b 01 00 10 4d 65 74 68 6f 64 50 61 72 61 
0000320 6d 65 74 65 72 73 01 00 0a 53 6f 75 72 63 65 46 
0000340 69 6c 65 01 00 0f 48 65 6c 6c 6f 57 6f 72 6c 64
0000360 2e 6a 61 76 61 0c 00 07 00 08 07 00 1d 0c 00 1e 
0000400 00 1f 01 00 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64 
0000420 07 00 20 0c 00 21 00 22 01 00 1b 63 6e 2f 69 74 
0000440 63 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c 
0000460 6f 57 6f 72 6c 64 01 00 10 6a 61 76 61 2f 6c 61 
0000500 6e 67 2f 4f 62 6a 65 63 74 01 00 10 6a 61 76 61 
0000520 2f 6c 61 6e 67 2f 53 79 73 74 65 6d 01 00 03 6f 
0000540 75 74 01 00 15 4c 6a 61 76 61 2f 69 6f 2f 50 72 
0000560 69 6e 74 53 74 72 65 61 6d 3b 01 00 13 6a 61 76 
0000600 61 2f 69 6f 2f 50 72 69 6e 74 53 74 72 65 61 6d 
0000620 01 00 07 70 72 69 6e 74 6c 6e 01 00 15 28 4c 6a 
0000640 61 76 61 2f 6c 61 6e 67 2f 53 74 72 69 6e 67 3b 
0000660 29 56 00 21 00 05 00 06 00 00 00 00 00 02 00 01 
0000700 00 07 00 08 00 01 00 09 00 00 00 2f 00 01 00 01 
0000720 00 00 00 05 2a b7 00 01 b1 00 00 00 02 00 0a 00 
0000740 00 00 06 00 01 00 00 00 04 00 0b 00 00 00 0c 00 
0000760 01 00 00 00 05 00 0c 00 0d 00 00 00 09 00 0e 00 
0001000 0f 00 02 00 09 00 00 00 37 00 02 00 01 00 00 00 
0001020 09 b2 00 02 12 03 b6 00 04 b1 00 00 00 02 00 0a 
0001040 00 00 00 0a 00 02 00 00 00 06 00 08 00 07 00 0b 
0001060 00 00 00 0c 00 01 00 00 00 09 00 10 00 11 00 00 
0001100 00 12 00 00 00 05 01 00 10 00 00 00 01 00 13 00 
0001120 00 00 02 00 14
```

根据 JVM 规范，**类文件结构**如下

```java
u4 			   magic  // 4字节魔数
u2             minor_version;    
u2             major_version;    
u2             constant_pool_count;  // 常量池
cp_info        constant_pool[constant_pool_count-1];    
u2             access_flags;    // 访问修饰符
u2             this_class;    
u2             super_class;   
u2             interfaces_count;   // 接口
u2             interfaces[interfaces_count];   
u2             fields_count;    // 字段
field_info     fields[fields_count];   
u2             methods_count;    // 方法
method_info    methods[methods_count];    
u2             attributes_count;    
attribute_info attributes[attributes_count];
```



### 1.1 魔数

对应字节码文件 0~3 字节，表示它是否是【class】类型的文件

0000000 **ca fe ba be** 00 00 00 34 00 23 0a 00 06 00 15 09



### 1.2 版本

对应字节码文件 4~7 字节，表示类的版本 00 34（52） 表示是 Java 8

0000000 ca fe ba be **00 00 00 34** 00 23 0a 00 06 00 15 09





## 2、字节码指令

### 2.1 javap

Oracle 提供了 javap 工具来反编译 class 文件

```java
javap -v 类名.class  // -v表示输出类文件的详细信息
```



**Java代码**

```java
public class Demo3_1 {   
	public static void main(String[] args) {        
		int a = 10;        
		int b = Short.MAX_VALUE + 1;        
		int c = a + b;        
		System.out.println(c);   
    } 
}
```

**编译后的字节码文件**

```java
[root@localhost ~]# javap -v Demo3_1.class
    Classfile /root/Demo3_1.class
    Last modified Jul 7, 2019; size 665 bytes
    MD5 checksum a2c29a22421e218d4924d31e6990cfc5
    Compiled from "Demo3_1.java"
    public class cn.itcast.jvm.t3.bytecode.Demo3_1
    minor version: 0
    major version: 52
    flags: ACC_PUBLIC, ACC_SUPER
    Constant pool:
        #1 = Methodref #7.#26 // java/lang/Object."<init>":()V
        #2 = Class #27 // java/lang/Short
        #3 = Integer 32768
        #4 = Fieldref #28.#29 //
        java/lang/System.out:Ljava/io/PrintStream;
        #5 = Methodref #30.#31 // java/io/PrintStream.println:(I)V
        #6 = Class #32 // cn/itcast/jvm/t3/bytecode/Demo3_1
        #7 = Class #33 // java/lang/Object
        #8 = Utf8 <init>
        #9 = Utf8 ()V
        #10 = Utf8 Code
        #11 = Utf8 LineNumberTable
        #12 = Utf8 LocalVariableTable
        #13 = Utf8 this
        #14 = Utf8 Lcn/itcast/jvm/t3/bytecode/Demo3_1;
        #15 = Utf8 main
        #16 = Utf8 ([Ljava/lang/String;)V
        #17 = Utf8 args
        #18 = Utf8 [Ljava/lang/String;
        #19 = Utf8 a
        #22 = Utf8 c
        #23 = Utf8 MethodParameters
        #24 = Utf8 SourceFile
        #25 = Utf8 Demo3_1.java
        #26 = NameAndType #8:#9 // "<init>":()V
        #27 = Utf8 java/lang/Short
        #28 = Class #34 // java/lang/System
        #29 = NameAndType #35:#36 // out:Ljava/io/PrintStream;
        #30 = Class #37 // java/io/PrintStream
        #31 = NameAndType #38:#39 // println:(I)V
        #32 = Utf8 cn/itcast/jvm/t3/bytecode/Demo3_1
        #33 = Utf8 java/lang/Object
        #34 = Utf8 java/lang/System
        #35 = Utf8 out
        #36 = Utf8 Ljava/io/PrintStream;
        #37 = Utf8 java/io/PrintStream
        #38 = Utf8 println
        #39 = Utf8 (I)V
    {
    public cn.itcast.jvm.t3.bytecode.Demo3_1();
        descriptor: ()V
        flags: ACC_PUBLIC
        Code:
            stack=1, locals=1, args_size=1 
                0: aload_0
                1: invokespecial #1 // Method java/lang/Object."<init>":()V
                4: return
            LineNumberTable:
                line 6: 0
            LocalVariableTable:
                Start 	Length  Slot  Name   Signature
                0 		5 		0 	  this   Lcn/itcast/jvm/t3/bytecode/Demo3_1;
            
    public static void main(java.lang.String[]);
        descriptor: ([Ljava/lang/String;)V
        flags: ACC_PUBLIC, ACC_STATIC
        Code:
            // stack表示操作数栈最大深度，locals表示局部变量表长度
            stack=2, locals=4, args_size=1
                0: bipush 10
                2: istore_1
                3: ldc #3 // int 32768
                5: istore_2
                6: iload_1
                7: iload_2
                8: iadd
                9: istore_3
                10: getstatic #4 // Field  java/lang/System.out:Ljava/io/PrintStream;
                13: iload_3
                14: invokevirtual #5 // Method  java/io/PrintStream.println:(I)V
                17: return
            LineNumberTable:
                line 8: 0
                line 9: 3
                line 12: 17
            LocalVariableTable:
                Start Length Slot Name   Signature
                0 		18 		0 args   [Ljava/lang/String;
                3 		15 		1 	a 	 I
                6 		12 		2   b 	 I
                10 		8 		3   c    I
        MethodParameters:
          Name   Flags
          args
    }
```





### 2.2 字节码执行流程

1、当代码被执行时，会由类加载器将这个类进行类加载操作。其中，常量池的数据会放入运行时常量池。对于一些比较小的数字，不会存放在常量池，而是和方法的字节码指令存在一起。超过`SHORT`最大值的数字会存放在常量池。

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707390125396-c68553c6-961e-40b4-97e8-22ce214c3755.png" alt="img" style="zoom: 80%;" />

2、方法的字节码会放入方法区

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707391070197-87fc8a17-5ed2-4389-949c-842cace89f49.png" alt="img" style="zoom: 80%;" />

3、main线程开始运行，分配栈帧内存

`stack=2，locals=4 `对应操作数栈有2个空间（每个空间4个字节），局部变量表中有4个槽位

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707391126865-78a70405-846e-4e2b-a50a-0aa4abe077bf.png" alt="img" style="zoom: 80%;" />

4、执行引擎开始执行方法区中的字节码

- `bipush 10`

  > 将一个` byte` 压入操作数栈（其长度会补齐 4 个字节）
  >
  > 类似的指令例如：
  >
  > - `sipush` 将一个 `short` 压入操作数栈（其长度会补齐 4 个字节）
  >
  > - `ldc `将一个` int `压入操作数栈
  >
  > - `ldc2_w` 将一个 `long` 压入操作数栈（分两次压入，因为 long 是 8 个字节）
  >
  > 这里小的数字都是和字节码指令存在一起，超过 `short `范围的数字存入了常量池

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707391919345-aa162327-b1ec-4041-9c6c-2efb8d662224.png)



- `istore 1`

  > 将操作数栈栈顶元素弹出，放入局部变量表的slot 1中

<img src="https://cdn.nlark.com/yuque/0/2024/png/40369427/1707391936929-a6aed0ef-4185-426d-b349-7b586119c15e.png" alt="img" style="zoom:80%;" />



- `ldc #3`

  > 从常量池加载 `#3` 数据到操作数栈
  >
  > **注意**： `Short.MAX_VALUE `是 32767，所以 `32768 = Short.MAX_VALUE + 1 `实际是在编译期间计算好的

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707391965618-de2e8946-934d-4924-87dd-10bb06afa06f.png)



- `istore_2`

  > 将操作数栈中的元素弹出，放到局部变量表的2号位置

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707391991369-4e8d5985-0598-493a-af90-a7f22351cd0b.png)



- `iload1`

  > 将局部变量表中1号位置的元素放入操作数栈中

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392019368-a2dd2fed-deed-4076-bd33-a5bebc7b44c7.png)



- `iload2`

  > 将局部变量表中2号位置的元素放入操作数栈中

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392045716-882ff846-321a-45b7-a157-11684a16c254.png)



- `iadd`

  > 将操作数栈中的两个元素弹出栈并相加，结果在压入操作数栈中

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392061252-3bb783c9-dbbb-4e9a-96c3-0ca50a6df298.png)



- `istore 3`

  > 将操作数栈中的元素弹出，放入局部变量表的3号位置

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392088519-91b3ff4d-2297-41f0-bc1f-2db46e51d4d8.png)



- `getstatic #4`

  > 在运行时常量池中找到`#4`，发现是一个对象。在堆内存中找到该对象，并将其引用放入操作数栈中

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392115465-38e58ebe-57fc-455b-8be5-ccf9c9b79436.png)



- `iload 3`

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392136150-48cd5758-5e2c-438a-b46e-4bec45460263.png)



- `invokevirtual 5`

  > 1、找到常量池` #5` 项
  >
  > 2、定位到方法区 `java/io/PrintStream.println:(I)V `方法
  >
  > 3、生成新的栈帧（分配 locals、stack等）
  >
  > 4、传递参数，执行新栈帧中的字节码

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392170870-7765027c-4687-45c1-b590-db7d4713d698.png)



> 执行完毕，弹出栈帧。清除 main 操作数栈内容

![img](https://cdn.nlark.com/yuque/0/2024/png/40369427/1707392199779-c8ec3719-bddb-43ad-8a3e-af7e2c195e79.png)



- `return`

> 完成 main 方法调用，弹出 main 栈帧，程序结束





### 2.3 字节码分析

**Java代码**

```java
public class Code_11_ByteCodeTest {
    public static void main(String[] args) {

        int i = 0;
        int x = 0;
        while (i < 10) {
            x = x++;
            i++;
        }
        System.out.println(x); // 0
    }
}
```

**反编译后指令**

1、`i++`指令进行自增时，是在局部变量表直接进行增加的，不需要加载到操作数栈

2、`i++`：其字节码为，先执行`iload`加载，然后执行`iinc`自增

3、`++i`：其字节码为，先执行`iinc`自增，然后执行`iload`加载

```java
Code:
     stack=2, locals=3, args_size=1	// 操作数栈分配2个空间，局部变量表分配 3 个空间
        0: iconst_0	// 准备一个常数 0
        1: istore_1	// 将常数 0 放入局部变量表的 1 号槽位 i = 0
        2: iconst_0	// 准备一个常数 0
        3: istore_2	// 将常数 0 放入局部变量的 2 号槽位 x = 0	
        4: iload_1		// 将局部变量表 1 号槽位的数放入操作数栈中
        5: bipush        10	// 将数字 10 放入操作数栈中，此时操作数栈中有 2 个数
        7: if_icmpge     21	// 比较操作数栈中的两个数，如果下面的数大于上面的数，就跳转到 21 。这里的比较是将两个数做减法。因为涉及运算操作，所以会将两个数弹出操作数栈来进行运算。运算结束后操作数栈为空
       10: iload_2		// 将局部变量 2 号槽位的数放入操作数栈中，放入的值是 0 
       11: iinc          2, 1	// 将局部变量 2 号槽位的数加 1 ，自增后，槽位中的值为 1 
       14: istore_2	//将操作数栈中的数放入到局部变量表的 2 号槽位，2 号槽位的值又变为了0
       15: iinc          1, 1 // 1 号槽位的值自增 1 
       18: goto          4 // 跳转到第4条指令
       21: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       24: iload_2
       25: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
       28: return
```





### 2.4 构造方法

#### 1、cinit()V

**Java代码**

```java
public class Code_12_CinitTest {
	static int i = 10;

	static {
		i = 20;
	}

	static {
		i = 30;
	}

	public static void main(String[] args) {
		System.out.println(i); // 30
	}
}
```

**字节码**

编译器会按从上至下的顺序，收集所有 static 静态代码块和静态成员赋值的代码，合并为一个特殊的方法 `cinit()V `，这个方法就是类加载的时候为静态变量赋值

```java
stack=1, locals=0, args_size=0
         0: bipush        10
         2: putstatic     #3                  // Field i:I
         5: bipush        20
         7: putstatic     #3                  // Field i:I
        10: bipush        30
        12: putstatic     #3                  // Field i:I
        15: return
```





#### 2、init()V

**Java代码**

```java
public class Code_13_InitTest {

    private String a = "s1";

    {
        b = 20;
    }

    private int b = 10;

    {
        a = "s2";
    }

    public Code_13_InitTest(String a, int b) {
        this.a = a;
        this.b = b;
    }

    public static void main(String[] args) {
        Code_13_InitTest d = new Code_13_InitTest("s3", 30);
        System.out.println(d.a); // "s3"
        System.out.println(d.b); // 30
    }

}
```

**字节码**

编译器会按从上至下的顺序，收集所有代码块和成员变量赋值的代码，形成新的构造方法，但原始构造方法内的代码总是在后。这是每次创建对象时执行的方法

```java
Code:
     stack=2, locals=3, args_size=3
        0: aload_0
        1: invokespecial #1                  // Method java/lang/Object."<init>":()V
        4: aload_0
        5: ldc           #2                  // String s1
        7: putfield      #3                  // Field a:Ljava/lang/String;
       10: aload_0
       11: bipush        20
       13: putfield      #4                  // Field b:I
       16: aload_0
       17: bipush        10
       19: putfield      #4                  // Field b:I
       22: aload_0
       23: ldc           #5                  // String s2
       25: putfield      #3                  // Field a:Ljava/lang/String;
       // 原始构造方法在最后执行
       28: aload_0
       29: aload_1
       30: putfield      #3                  // Field a:Ljava/lang/String;
       33: aload_0
       34: iload_2
       35: putfield      #4                  // Field b:I
       38: return
```





### 2.5 方法调用

**Java代码**

```java
public class Code_14_MethodTest {

    public Code_14_MethodTest() {

    }

    private void test1() {

    }

    private final void test2() {

    }

    public void test3() {

    }

    public static void test4() {

    }

    public static void main(String[] args) {
        Code_14_MethodTest obj = new Code_14_MethodTest();
        obj.test1();
        obj.test2();
        obj.test3();
        obj.test4();
        Code_14_MethodTest.test4();
    }
}
```

不同方法在调用时，对应的虚拟机指令有所区别

- 私有、构造、被 `final `修饰的方法，在调用时都使用 `invokespecial `指令，编译时就可以确定
- 普通成员方法在调用时，使用` invokevirtual `指令，动态绑定。因为编译期间无法确定该方法的内容【可能调用的是子类的，也可能调用的是父类的】，只有在运行期间才能确定【动态绑定机制】
- 静态方法在调用时使用` invokestatic` 指令

**字节码**

```java
Code:
      stack=2, locals=2, args_size=1
         0: new           #2                  //
         3: dup // 复制一份对象地址压入操作数栈中
         4: invokespecial #3                  // Method "<init>":()V
         7: astore_1
         8: aload_1
         9: invokespecial #4                  // Method test1:()V
        12: aload_1
        13: invokespecial #5                  // Method test2:()V
        16: aload_1
        17: invokevirtual #6                  // Method test3:()V
        20: aload_1
        21: pop
        22: invokestatic  #7                  // Method test4:()V
        25: invokestatic  #7                  // Method test4:()V
        28: return
```

- `new` 是创建【对象】，给对象分配堆内存，执行成功会将【对象引用】压入操作数栈
- `dup` 是复制操作数栈栈顶的内容，本例即为【对象引用】，其中一个引用是要配合 `invokespecial `调用该对象的构造方法`“init”: ()V` （会消耗掉栈顶一个引用），另一个要 配合 `astore_1` 赋值给局部变量，即将这个对象赋值给`obj`
- `final`和`private`修饰的方法，构造方法都是由` invokespecial `指令来调用，属于静态绑定
- 普通成员方法是由 `invokevirtual` 调用
- 可以发现，不管使用对象还是直接使用类调用静态方法，都是执行`invokestatic`指令，只不过使用对象调用时，会先执行`aload_1`，然后执行`pop`，即先加载对象到操作数栈，然后直接弹出。所以调用静态方法时，最好直接使用类名调用，这样会少执行两条指令





### 2.6 多态原理

因为普通成员方法需要在运行时才能确定具体的内容，所以虚拟机需要调用 `invokevirtual` 指令
在执行 `invokevirtual` 指令时，有以下几个步骤

- 先通过栈帧中对象的引用找到对象
- 分析对象头，找到对象实际的 Class
- Class 结构中有 vtable
- 查询 vtable 找到方法的具体地址
- 执行方法的字节码
  





### 2.7 异常处理

#### 1、try-catch

**Java代码**

```java
public class Code_15_TryCatchTest {
    public static void main(String[] args) {
        int i = 0;
        try {
            i = 10;
        } catch (Exception e) {
            i = 20;
        }
    }
}
```

**字节码**

```java
Code:
     stack=1, locals=3, args_size=1
        0: iconst_0
        1: istore_1
        2: bipush        10
        4: istore_1
        5: goto          12
        8: astore_2 // 将异常信息保存到局部变量表slot2的位置，即将异常信息存入变量e中
        9: bipush        20
       11: istore_1
       12: return
     //多出来一个异常表
     Exception table:
        from    to  target type
            // 监控[2,5)行的代码，如果发生异常，判断异常类型是否匹配，匹配的话就跳到第8行
            2     5     8   Class java/lang/Exception 
```



#### 2、多个catch

```java
public class Code_16_MultipleCatchTest {

    public static void main(String[] args) {
        int i = 0;
        try {
            i = 10;
        } catch (ArithmeticException e) {
            i = 20;
        } catch (Exception e) {
            i = 30;
        }
    }
}
```

```java
Code:
     stack=1, locals=3, args_size=1
        0: iconst_0
        1: istore_1
        2: bipush        10
        4: istore_1
        5: goto          19
        8: astore_2 // 异常对象存入slot2
        9: bipush        20
       11: istore_1
       12: goto          19
       15: astore_2 // 异常对象存入slot2
       16: bipush        30
       18: istore_1
       19: return
     Exception table:
        from    to  target type
            2     5     8   Class java/lang/ArithmeticException // 如果出现ArithmeticException，就跳到第8行
            2     5    15   Class java/lang/Exception // 如果出现Exception，就跳到15行
```

- 因为异常出现时，只能进入 `Exception table `中一个分支，所以局部变量表 slot 2 位置被共用





#### 3、finally

```java
public class Code_17_FinallyTest {
    
    public static void main(String[] args) {
        int i = 0;
        try {
            i = 10;
        } catch (Exception e) {
            i = 20;
        } finally {
            i = 30;
        }
    }
}
```

```java
Code:
     stack=1, locals=4, args_size=1
        0: iconst_0
        1: istore_1
        // try块
        2: bipush        10
        4: istore_1
        // try块执行完后，会执行finally    
        5: bipush        30
        7: istore_1
        8: goto          27
       // catch块     
       11: astore_2 // 异常信息放入局部变量表的2号槽位
       12: bipush        20
       14: istore_1
       // catch块执行完后，会执行finally        
       15: bipush        30
       17: istore_1
       18: goto          27
       // 出现异常，但未被 Exception 捕获，会抛出其他异常，这时也需要执行 finally 块中的代码   
       21: astore_3 // 异常信息保存到slot3
       22: bipush        30
       24: istore_1
       25: aload_3 // 加载异常
       26: athrow  // 抛出异常
       27: return
     Exception table:
        from    to  target type
            2     5    11   Class java/lang/Exception
            2     5    21   any
           11    15    21   any
```

- `ﬁnally` 中的代码被复制了 3 份，分别放入 try 流程，catch 流程以及 catch 剩余的异常类型流程





#### 4、finally 中的 return

```java
public class Code_18_FinallyReturnTest {

    public static void main(String[] args) {
        int i = Code_18_FinallyReturnTest.test();
        // 结果为 20
        System.out.println(i);
    }

    public static int test() {
        int i;
        try {
            i = 10;
            return i;
        } finally {
            i = 20;
            return i;
        }
    }
}
```

```java
Code:
     stack=1, locals=3, args_size=0
        0: bipush        10
        2: istore_0
        3: iload_0
        4: istore_1  // 暂存返回值
        5: bipush        20
        7: istore_0
        8: iload_0
        9: ireturn	// ireturn 会返回操作数栈顶的整型值 20
       // 如果出现异常，还是会执行finally 块中的内容，没有抛出异常
       10: astore_2
       11: bipush        20
       13: istore_0
       14: iload_0
       15: ireturn	// 这里没有 athrow 了，也就是如果在 finally 块中如果有返回操作的话，且 try 块中出现异常，会吞掉异常
     Exception table:
        from    to  target type
            0     5    10   any
```

- 由于 `ﬁnally` 中的 `ireturn` 被插入了所有可能的流程，因此返回结果以`finally`的为准
- 跟上例中的` finally` 相比，这里没有 `athrow `了，因此在 `finally` 中出现了 `return`，会吞掉异常，所以不要在`finally`中进行返回操作
  



#### 5、finally 不带 return

```java
public static int test() {
    int i = 10;
    try {
        return i;
    } finally {
        i = 20;
    }
}
```

```java
Code:
     stack=1, locals=3, args_size=0
        0: bipush        10
        2: istore_0 // 赋值给i 10
        3: iload_0	// 加载到操作数栈顶
        4: istore_1 // 加载到局部变量表的1号位置
        5: bipush        20
        7: istore_0 // 赋值给i 20
        8: iload_1 // 加载局部变量表1号位置的数10到操作数栈
        9: ireturn // 返回操作数栈顶元素 10
       10: astore_2
       11: bipush        20
       13: istore_0
       14: aload_2 // 加载异常
       15: athrow // 抛出异常
     Exception table:
        from    to  target type
            3     5    10   any
```

- `try`中返回的值会被保存下来，即使`finally`中修改了该值，返回的仍然是原来保存的值





### 2.8 Synchronized

```java
public class Code_19_SyncTest {
    public static void main(String[] args) {
        Object lock = new Object();
        synchronized (lock) {
            System.out.println("ok");
        }
    }
}
```

```java
Code:
      stack=2, locals=4, args_size=1
         0: new           #2                  // class java/lang/Object
         3: dup // 复制一份栈顶，然后压入栈中。用于函数消耗
         4: invokespecial #1                  // Method java/lang/Object."<init>":()V
         7: astore_1 // 将栈顶的对象地址放到 局部变量表中 1 中
         8: aload_1 // 加载到操作数栈
         9: dup // 复制一份，放到操作数栈，用于加锁时消耗
        10: astore_2 // 将操作数栈顶元素弹出，暂存到局部变量表的 2 号槽位。这时操作数栈中有一份对象的引用
        11: monitorenter // 加锁
        12: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
        15: ldc           #4                  // String ok
        17: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        20: aload_2 // 加载对象到栈顶
        21: monitorexit // 释放锁
        22: goto          30
        // 异常情况的解决方案 释放锁！
        25: astore_3
        26: aload_2
        27: monitorexit
        28: aload_3
        29: athrow
        30: return
        // 异常表！
      Exception table:
         from    to  target type
            12    22    25   any
            25    28    25   any
```

- `Synchronized`会将锁住的对象保存两份，一份加锁使用，另一份用于释放锁使用。通过`Exception table`监控异常，如果出现异常，就释放锁并抛出异常





## 3、语法糖

> 语法糖 ，是指 `Java` 编译器把 `.java` 源码编译为 `.class` 字节码的过程中，自动生成和转换的一些代码，主要是为了减轻程序员的负担
>
> 以下代码的分析，借助了 `javap` 工具，idea 的反编译功能，idea 插件 `jclasslib` 等工具。另外， 编译器转换的结果直接就是 class 字节码，只是为了便于阅读，给出了 几乎等价 的 java 源码方式，并不是编译器还会转换出中间的 java 源码。



### 3.1 默认构造器

```java
public class Candy1 {
}
```

经过编译器优化后

```java
public class Candy1 {
   // 这个无参构造器是java编译器帮我们加上的
   public Candy1() {
      // 即调用父类 Object 的无参构造方法，即调用 java/lang/Object." <init>":()V
      super();
   }
}
```





### 3.2 自动拆装箱

> 基本类型和其包装类型的相互转换过程，称为拆装箱。在 JDK 5 以后，它们的转换可以在编译期自动完成

```java
public class Candy2 {
   public static void main(String[] args) {
      Integer x = 1;
      int y = x;
   }
}
```

经过编译器优化后

```java
public class Candy2 {
   public static void main(String[] args) {
      // 基本类型赋值给包装类型，装箱
      Integer x = Integer.valueOf(1);
      // 包装类型赋值给基本类型，拆箱
      int y = x.intValue();
   }
}
```





### 3.3 泛型集合取值

> 泛型是在 JDK 5 开始加入的特性，但 `Java` 在编译泛型代码后会执行泛型擦除的动作，即泛型信息在编译为字节码之后就丢失了，实际的类型都当做了 Object 类型来处理

```java
public class Candy3 {
   public static void main(String[] args) {
      List<Integer> list = new ArrayList<>();
      list.add(10);
      Integer x = list.get(0);
   }
}
```

对应字节码

```java
Code:
    stack=2, locals=3, args_size=1
       0: new           #2                  // class java/util/ArrayList
       3: dup
       4: invokespecial #3                  // Method java/util/ArrayList."<init>":()V
       7: astore_1
       8: aload_1
       9: bipush        10
      11: invokestatic  #4                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
      // 这里进行了泛型擦除，实际调用的是add(Objcet o)
      14: invokeinterface #5,  2            // InterfaceMethod java/util/List.add:(Ljava/lang/Object;)Z

      19: pop
      20: aload_1
      21: iconst_0
      // 这里也进行了泛型擦除，实际调用的是get(Object o)   
      22: invokeinterface #6,  2            // InterfaceMethod java/util/List.get:(I)Ljava/lang/Object;
	  // 这里进行了类型转换，将 Object 转换成了 Integer
      27: checkcast     #7                  // class java/lang/Integer
      30: astore_2
      31: return
```



1、调用 get 函数取值时，有一个类型转换的操作

```java
Integer x = (Integer) list.get(0);
```

2、如果要将返回结果赋值给一个` int `类型的变量，则还有自动拆箱的操作

```java
int x = (Integer) list.get(0).intValue();
```

3、通过反射可以获得方法参数和方法返回值的泛型信息，局部变量的泛型信息在字节码的`LocalVariableTypeTable`中，无法通过反射获取

```java
public static void main(String[] args) throws NoSuchMethodException {
    // 1. 拿到方法
    Method method = Code_20_ReflectTest.class.getMethod("test", List.class, Map.class);
    // 2. 得到泛型参数的类型信息
    Type[] types = method.getGenericParameterTypes();
    for(Type type : types) {
        // 3. 判断参数类型是否，带泛型的类型。
        if(type instanceof ParameterizedType) {
            ParameterizedType parameterizedType = (ParameterizedType) type;

            // 4. 得到原始类型
            System.out.println("原始类型 - " + parameterizedType.getRawType());
            // 5. 拿到泛型类型
            Type[] arguments = parameterizedType.getActualTypeArguments();
            for(int i = 0; i < arguments.length; i++) {
                System.out.printf("泛型参数[%d] - %s\n", i, arguments[i]);
            }
        }
    }
}

public Set<Integer> test(List<String> list, Map<Integer, Object> map) {
    return null;
}

输出:
原始类型 - interface java.util.List
泛型参数[0] - class java.lang.String
原始类型 - interface java.util.Map
泛型参数[0] - class java.lang.Integer
泛型参数[1] - class java.lang.Object
```





### 3.4 可变参数

> 可变参数是 JDK 5 开始加入的新特性，可变参数经编译后就是数组	

```java
public class Candy4 {
   public static void foo(String... args) {
      // 将 args 赋值给 arr ，可以看出 String... 实际就是 String[]  
      String[] arr = args;
      System.out.println(arr.length);
   }

   public static void main(String[] args) {
      foo("hello", "world");
   }
}
```

可变参数 `String… args` 其实是一个 `String[] args `，经过编译器转换后代码

```java
public class Candy4 {
   public Candy4 {}
   public static void foo(String[] args) {
      String[] arr = args;
      System.out.println(arr.length);
   }

   public static void main(String[] args) {
      foo(new String[]);
   }
}
```

如果调用的是 `foo()` ，即未传递参数时，等价代码为` foo(new String[]{})` ，创建了一个空数组，而不是直接传递的 `null `





### 3.5 foreach 循环

> `foreach`是 JDK 5 开始引入的语法糖

**数组遍历**

```java
public class Candy5 {
	public static void main(String[] args) {
        // 数组赋初值的简化写法也是一种语法糖。
		int[] arr = {1, 2, 3, 4, 5};
		for(int x : arr) {
			System.out.println(x);
		}
	}
}
```

经编译器转换后

```java
public class Candy5 {
    public Candy5() {}

	public static void main(String[] args) {
		int[] arr = new int[]{1, 2, 3, 4, 5};
		for(int i = 0; i < arr.length; ++i) {
			int x = arr[i];
			System.out.println(x);
		}
	}
}
```



**集合遍历**

> 集合要使用 `foreach` ，需要该集合类实现了 `Iterable` 接口，因为集合的遍历需要用到迭代器 `Iterator`

```java
public class Candy5 {
   public static void main(String[] args) {
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
      for (Integer x : list) {
         System.out.println(x);
      }
   }
}
```

经编译器转换后

```java
public class Candy5 {
    public Candy5(){}
    
   public static void main(String[] args) {
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
      // 获得该集合的迭代器
      Iterator<Integer> iterator = list.iterator();
      while(iterator.hasNext()) {
         Integer x = iterator.next();
         System.out.println(x);
      }
   }
}
```





### 3.6 switch 字符串

> 从 JDK 7 开始，`switch` 可以作用于**字符串**和**枚举类**

```java
public class Cnady6 {
   public static void main(String[] args) {
      String str = "hello";
      switch (str) {
         case "hello" :
            System.out.println("h");
            break;
         case "world" :
            System.out.println("w");
            break;
         default:
            break;
      }
   }
}
```

在编译器中执行的操作

```java
public class Candy6 {
   public Candy6() {
      
   }
   public static void main(String[] args) {
      String str = "hello";
      int x = -1;
      // 通过字符串的 hashCode + equals 来判断是否匹配
      switch (str.hashCode()) {
         // hello 的 hashCode
         case 99162322 :
            // 再次比较，避免出现hash冲突
            if(str.equals("hello")) {
               x = 0;
            }
            break;
         // world 的 hashCode
         case 11331880 :
            if(str.equals("world")) {
               x = 1;
            }
            break;
         default:
            break;
      }

      // 用第二个 switch 在进行输出判断
      switch (x) {
         case 0:
            System.out.println("h");
            break;
         case 1:
            System.out.println("w");
            break;
         default:
            break;
      }
   }
}
```

- 在编译期间，单个的 `switch` 被分为了两个
  - 第一个用来匹配字符串，并给 x 赋值
  - 字符串的匹配用到了字符串的 `hashCode`和`equals` 方法
  - 使用 `hashCode` 是为了提高比较效率，使用 `equals` 是防止有 `hashCode` 冲突
  - 第二个用来根据x的值来决定输出语句
    



### 3.7 switch 枚举

```java
enum SEX {
   MALE, FEMALE;
}
public class Candy7 {
   public static void main(String[] args) {
      SEX sex = SEX.MALE;
      switch (sex) {
         case MALE:
            System.out.println("man");
            break;
         case FEMALE:
            System.out.println("woman");
            break;
         default:
            break;
      }
   }
}
```

编译器中执行的代码如下

```java
enum SEX {
   MALE, FEMALE;
}

public class Candy7 {
   /**     
    * 定义一个合成类（仅 jvm 使用，对我们不可见）     
    * 用来映射枚举的 ordinal 与数组元素的关系     
    * 枚举的 ordinal 表示枚举对象的序号，从 0 开始     
    * 即 MALE 的 ordinal()=0，FEMALE 的 ordinal()=1     
    */ 
   static class $MAP {
      // 数组大小即为枚举元素个数，里面存放了 case 用于比较的数字
      static int[] map = new int[2];
      static {
         // ordinal 即枚举元素对应所在的位置，MALE 为 0 ，FEMALE 为 1
         map[SEX.MALE.ordinal()] = 1;
         map[SEX.FEMALE.ordinal()] = 2;
      }
   }

   public static void main(String[] args) {
      SEX sex = SEX.MALE;
      // 将对应位置枚举元素的值赋给 x ，用于 case 操作
      int x = $MAP.map[sex.ordinal()];
      switch (x) {
         case 1:
            System.out.println("man");
            break;
         case 2:
            System.out.println("woman");
            break;
         default:
            break;
      }
   }
}
```





### 3.8 枚举类

> JDK 7 新增了枚举类

```java
enum SEX {
   MALE, FEMALE;
}
```

转换后的代码

```java
public final class Sex extends Enum<Sex> {   
   // 对应枚举类中的元素
   public static final Sex MALE;    
   public static final Sex FEMALE;    
   private static final Sex[] $VALUES;
   
    static {       
    	// 调用构造函数，传入枚举元素的值及 ordinal
    	MALE = new Sex("MALE", 0);    
        FEMALE = new Sex("FEMALE", 1);   
        $VALUES = new Sex[]{MALE, FEMALE}; 
   }
 	
   // 调用父类中的方法
    private Sex(String name, int ordinal) {     
        super(name, ordinal);    
    }
   
    public static Sex[] values() {  
        return $VALUES.clone();  
    }
    public static Sex valueOf(String name) { 
        return Enum.valueOf(Sex.class, name);  
    } 
}
```





### 3.9 try-with-resources

> JDK 7 开始新增了对需要关闭的资源处理的特殊语法，`try-with-resources`，其中资源对象需要实现 `AutoCloseable` 接口，例如`InputStream` 、 `OutputStream `、`Connection` 、`Statement` 、` ResultSet` 等接口都实现了 `AutoCloseable` ，使用 `try-with- resources` 可以不用写` finally `语句块，编译器会帮助生成关闭资源代码
>

```java
public class Candy9 { 
	public static void main(String[] args) {
		try(InputStream is = new FileInputStream("d:\\1.txt")){	
			System.out.println(is); 
		} catch (IOException e) { 
			e.printStackTrace(); 
		} 
	} 
}
```

转换后代码

```java
public class Candy9 { 
    
    public Candy9() { }
   
    public static void main(String[] args) { 
        try {
            InputStream is = new FileInputStream("d:\\1.txt");
            Throwable t = null; 
            try {
                System.out.println(is); 
            } catch (Throwable e1) { 
                // t 是我们代码出现的异常 
                t = e1; 
                throw e1; 
            } finally {
                // 判断了资源不为空 
                if (is != null) { 
                    // 如果我们代码有异常
                    if (t != null) { 
                        try {
                            is.close(); 
                        } catch (Throwable e2) { 
                            // 如果 close 出现异常，作为被压制异常添加到t中，和我们代码的异常放在一起，防止异常信息的丢失
                            t.addSuppressed(e2); 
                        } 
                    } else { 
                        // 如果我们代码没有异常，close 出现的异常就是最后 catch 块中的 e 
                        is.close(); 
                    } 
                } 
            } 
        } catch (IOException e) {
            e.printStackTrace(); 
        } 
    }
}
```





### 3.10 方法重写的桥接方法

方法重写时对返回值分两种情况

- 父子类的返回值完全一致
- 子类返回值是父类返回值的子类

```java
class A { 
	public Number m() { 
		return 1; 
	} 
}
class B extends A { 
	@Override 
	// 子类 m 方法的返回值是 Integer 是父类 m 方法返回值 Number 的子类 	
	public Integer m() { 
		return 2; 
	} 
}
```

转换后代码

```java
class B extends A { 
	public Integer m() { 
		return 2; 
	}
	// 此方法才是真正重写了父类 public Number m() 方法 
	public synthetic bridge Number m() { 
		// 调用 public Integer m() 
		return m(); 
	} 
}
```

- 桥接方法比较特殊，仅对 java 虚拟机可见，并且与原来的 `public Integer m() `没有命名冲突，可以通过反射得到该方法





### 3.11 匿名内部类

```java
public class Candy10 {
   public static void main(String[] args) {
      Runnable runnable = new Runnable() {
         @Override
         public void run() {
            System.out.println("running...");
         }
      };
   }
}
```

转换后代码

```java
public class Candy10 {
   public static void main(String[] args) {
      // 用额外创建的类来创建匿名内部类对象
      Runnable runnable = new Candy10$1();
   }
}

// 创建了一个额外的类，实现了 Runnable 接口
final class Candy10$1 implements Runnable {
   public Demo8$1() {}

   @Override
   public void run() {
      System.out.println("running...");
   }
}
```



**引用局部变量的匿名内部类**

```java
public class Candy11 { 
	public static void test(final int x) { 
		Runnable runnable = new Runnable() { 
			@Override 
			public void run() { 	
				System.out.println("ok:" + x); 
			} 
		}; 
	} 
}
```

转换后代码

```java
// 额外生成的类 
final class Candy11$1 implements Runnable { 
	int val$x; 
	Candy11$1(int x) {  // 将局部变量x赋值给val$x
		this.val$x = x; 
	}
	public void run() { 
		System.out.println("ok:" + this.val$x); 
	} 
}

public class Candy11 { 
	public static void test(final int x) { 
		Runnable runnable = new Candy11$1(x); 
	} 
}
```

这就是为什么匿名内部类引用局部变量时，局部变量必须是 `final` 的：因为在创建 `Candy11$1 `对象时，将 `x` 的值赋值给了 `Candy11$1 `对象的 值后，如果不是 `final `声明的 `x` 值发生了改变，匿名内部类则值不一致。





## 4、类加载

### 4.1 加载

将类的字节码载入方法区（1.8后为元空间，在本地内存中）中，内部采用 C++ 的 `instanceKlass` 描述 java 类，它的重要 ﬁeld如下：

- `_java_mirror `： java 的类镜像，例如对 String 来说，它的镜像类就是 String.class，作用是把 klass 暴露给 java 使用，`_java_mirror`是存放在堆区的，通过反射获取的就是`_java_mirror`，然后可以得到方法区的类信息
- `_super `：父类
- `_ﬁelds `：成员变量
- `_methods` ：方法
- `_constants` ：常量池
- `_class_loader` ：类加载器
- `_vtable` ：虚方法表，多态就是通过续方法表找到对应的方法
- `_itable` ：接口方法
- 如果这个类还有父类没有加载，先加载父类
- 加载和链接可能是交替运行的

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402092247850.png" style="zoom:67%;" />

- `instanceKlass`保存在方法区。JDK 8以后，方法区位于元空间中，而元空间又位于本地内存中
- `_java_mirror`则是保存在堆内存中
- `InstanceKlass`和`*.class`【JAVA镜像类】互相保存了对方的地址
- 类的对象在对象头中保存了`*.class`的地址。让对象可以通过其找到方法区中的`instanceKlass`，从而获取类的各种信息
  



### 4.2 连接

**验证**

> 验证类是否符合 JVM规范，安全性检查，比如判断是否以魔数开头



**准备**

> 为 `static `变量分配空间，设置默认值

- static 变量在 JDK 7 之前存储于` instanceKlass` 末尾【方法区】，从 JDK 7 开始，存储于` _java_mirror `末尾【堆区】
- static 变量分配空间和赋值是两个步骤，分配空间在准备阶段完成，赋值在初始化阶段完成
- 如果 static 变量是 final 的基本类型，以及字符串常量，那么编译阶段值就确定了，赋值在准备阶段完成
- 如果 static 变量是 final 的，但属于引用类型，那么赋值也会在初始化阶段完成



**解析**

> 将常量池中的符号引用解析为直接引用。
>
> 例如类`A`里边有一个类`B`的属性，在执行解析之前，常量池表中，对`B`的引用就是符号引用，`B`可能还没有被加载到内存中，这个引用就是一个字面量。在解析阶段之后，常量池表中，对`B`的引用是直接引用，存储的就是`B`在内存中的地址





### 4.3 初始化

执行`	<cinit>()v` 方法，对`static`变量进行初始化，虚拟机会保证这个类的构造方法的线程安全



**发生的时机**

类初始化是【懒惰的】

- main 方法所在的类，总会被首先初始化

- 首次访问这个类的静态变量或静态方法时会初始化【静态变量在初始化时进行赋值】

- 子类初始化，如果父类还没初始化，会先初始化父类

- 子类访问父类的静态变量，只会触发父类的初始化

- `Class.forName`动态加载类时，会导致类的初始化，但是可以设置`initialize`为`false`表示不初始化

- `new` 会导致初始化

  

**不会导致类初始化的情况**

- 访问类的`static final` 静态常量（基本类型和字符串）不会触发初始化【它们是在准备阶段赋值】
- 调用`类对象.class` 不会触发初始化
- 创建该类的数组不会触发初始化
- `loadClass`方法只会加载类，不会执行类的解析和初始化

```java
public class Load1 {
    static {
        System.out.println("main init");
    }
    public static void main(String[] args) throws ClassNotFoundException {
        // 1. 静态常量（基本类型和字符串）不会触发初始化
//         System.out.println(B.b);
        // 2. 类对象.class 不会触发初始化
//         System.out.println(B.class);
        // 3. 创建该类的数组不会触发初始化
//         System.out.println(new B[0]);
        // 4. 不会初始化类 B，但会加载 B、A
//         ClassLoader cl = Thread.currentThread().getContextClassLoader();
//         cl.loadClass("cn.ali.jvm.test.classload.B");
        // 5. 不会初始化类 B，但会加载 B、A
//         ClassLoader c2 = Thread.currentThread().getContextClassLoader();
//         Class.forName("cn.ali.jvm.test.classload.B", false, c2);


        // 1. 首次访问这个类的静态变量或静态方法时
//         System.out.println(A.a);
        // 2. 子类初始化，如果父类还没初始化，会引发
//         System.out.println(B.c);
        // 3. 子类访问父类静态变量，只触发父类初始化
//         System.out.println(B.a);
        // 4. 会初始化类 B，并先初始化类 A
//         Class.forName("cn.ali.jvm.test.classload.B");
    }

}


class A {
    static int a = 0;
    static {
        System.out.println("a init");
    }
}
class B extends A {
    final static double b = 5.0;
    static boolean c = false;
    static {
        System.out.println("b init");
    }
}
```





## 5、类加载器

> - 对于任意一个类，都必须由加载它的类加载器和这个类本身一起共同确立其在 Java 虚拟机中的唯一性，每一个类加载器，都拥有一个独立的类名称空间。比较两个类是否 相等，只有在这两个类是由同一个类加载器加载的前提下才有意义，否则，即使这两个类来源于同一个 Class 文件，被同一个 Java 虚拟机加载，只要加载它们的类加载器不同，那这两个类就必定不相等。即通过一个二元组`N, L`确定的，`N`是类名，`L`是类加载器
>
> - 数组不是通过classLoader来加载创建的，而是JVM直接创建的；数组类的元素类型，如果是引用类型，那么需要靠类加载器去创建；数组的唯一性也是靠二元组确定的，数组类型的类加载器和数组中元素的类加载器是相同的
> - 类加载器的父子关系是以**组合**关系实现的

| 名称                                      | 加载的类              | 说明                        |
| ----------------------------------------- | --------------------- | --------------------------- |
| Bootstrap ClassLoader（启动类加载器）     | JAVA_HOME/jre/lib     | 无法直接访问                |
| Extension ClassLoader(拓展类加载器)       | JAVA_HOME/jre/lib/ext | 上级为Bootstrap，显示为null |
| Application ClassLoader(应用程序类加载器) | classpath             | 上级为Extension             |
| 自定义类加载器                            | 自定义                | 上级为Application           |

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402101659792.png" style="zoom:50%;" />



**JVM的类加载机制**

- 缓存机制

  > 缓存机制将会保证所有加载过的 Class 都会被缓存，当程序中需要使用某个 Class 时，类加载器先从缓存区中搜寻该Class，只有当缓存区中不存在该 Class 对象时，系统才会读取该类对应的二进制数据，并将其转换成 Class 对象，存入缓冲区中。这就是为什么修改了 Class 后，必须重新启动 JVM ，程序所做的修改才会生效的原因。

- 双亲委派

  > 双亲委派，是先让父类加载器试图加载该 Class ，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类。通俗的讲，就是某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父加载器，依次递归，如果父加载器可以完成类加载任务，就成功返回；只有父加载器无法完成此加载任务时，才自己去加载。

- 全盘负责

  > 全盘负责，就是当一个类加载器负责加载某个 Class 时，该 Class 所依赖和引用其他 Class 也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入。





### 5.1 启动类加载器

> 用 `Bootstrap `类加载器加载类，器加载的是`JAVA_HOME/jre/lib`下的类



将`classpath`下的类使用`BootstrapClassLoader`加载

```java
class F {
    static {
        System.out.println("bootstrap F init");
    }
}

// 用 Bootstrap 类加载器加载类
public class T01_ClassLoader_Bootstrap {
    public static void main(String[] args) throws ClassNotFoundException {
        // forName 可以完成类的加载，也可以类的链接、初始化操作
        Class<?> aClass = Class.forName("com.jvm.t10_class_loader.F");
        System.out.println(aClass.getClassLoader());
    }
}
```

```sh
/Users/Li/IdeaProjects/JvmLearn> java -Xbootclasspath/a:.com.jvm.t10_class_loader.T01_ClassLoader_Bootstrap
bootstrap F init
null
```

输出结果为`null` ，说明 F类 是由启动类加载器加载的

- `-Xbootclasspath` 表示设置 `bootclasspath`
- 其中 `/a:.` 表示将当前目录追加至 `boot classpath `之后
- 可以用这个办法替换核心类
  - `java -Xbootclasspath:<new bootclasspath>` ：使用后边的路径替换原始路径
  - `java -Xbootclasspath/a:<追加路径>`：将设置的路径追加到加载路径之后
  - `java -Xbootclasspath/p:<追加路径>`：将设置的路径追加到加载路径之前



### 5.2 扩展类加载器

如果` classpath` 和 `JAVA_HOME/jre/lib/ext `下有同名类，加载时会使用拓展类加载器加载。当应用程序类加载器发现拓展类加载器已将该同名类加载过了，则不会再次加载。



### 5.3 双亲委派模式

如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行，如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器，如果父类加载器可以完成类加载任务，就成功返回，如果父类加载器无法完成此加载任务，子加载器才会尝试自己去加载

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402101711248.png)





**双亲委派优点**

- 避免类重复加载 

  > 双亲委派模式可以避免类的重复加载，当父`ClassLoader`已经加载了该类时，就没有必要让子` ClassLoader` 再加载一次

- 避免核心类被篡改

  > 假设通过网络传递一个名为` java.lang.Integer `的类，通过双亲委托模式传递到启动类加载器，而启动类加载器在核心 Java API 发现这个名字的类，发现该类已被加载，并不会重新加载网络传递的过来的`java.lang.Integer`，而直接返回已加载过的 `Integer.class` ，这样便可以防止核心API库被随意篡改



**源码分析**

类加载时都会调用`loadClass`方法进行加载，上层`ClassLoader`加载不了，才会调用自己的`findClass`方法加载

```java
protected Class<?> loadClass(String name, boolean resolve)
    throws ClassNotFoundException
{
    synchronized (getClassLoadingLock(name)) {
        // 首先查找该类是否已经被该类加载器加载过了
        Class<?> c = findLoadedClass(name);
        // 如果没有被加载过
        if (c == null) {
            long t0 = System.nanoTime();
            try {
                // 看是否被它的上级加载器加载过了 Extension 的上级是Bootstarp，但它显示为null
                if (parent != null) {
                    c = parent.loadClass(name, false);
                } else {
                    // 看是否被启动类加载器加载过
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                // ClassNotFoundException thrown if class not found
                // from the non-null parent class loader
                //捕获异常，但不做任何处理
            }

            if (c == null) {
                // 如果还是没有找到，先让拓展类加载器调用 findClass 方法去找到该类，如果还是没找到，就抛出异常
                // 然后让应用类加载器去找 classpath 下找该类
                long t1 = System.nanoTime();
                c = findClass(name);

                // 记录时间
                sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                sun.misc.PerfCounter.getFindClasses().increment();
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
}
```





### 5.4 线程上下文类加载器

> 我们在使用 JDBC 时，都需要加载 Driver 驱动，但是不写`Class.forName("com.mysql.jdbc.Driver")`这行代码，也可以让`com.mysql.jdbc.Driver `被正确加载
>
> 1、在`jre/lib`包下有一个`DriverManager`，是启动类加载的，但是jdbc的驱动是各个厂商来实现的不在启动类加载路径下，启动类无法加载，而驱动管理需要用到这些驱动
>
> 2、打破双亲委派，`BootstrapClassLoader`直接请求`AppClassLoader`去classpath下加载驱动（正常是向上委托，这个反过来了），而打破双亲委派的就是这个线程上下文类加载器
>
> 3、启动类加载器加载`DriverManager`，`DriverManager`代码里调用了线程上下文类加载器，这个加载器默认就是使用应用程序类加载器加载类，通过应用程序类加载器加载jdbc驱动
>
> 4、热加载、热部署都是通过打破双亲委派模式实现的，当模块发生变化时，使用新的classLoader来加载他们



1、Java中SPI机制的核心类是`ServiceLoader`，`	ServiceLoader`在包`java.util`下，由`BootstrapClassLoader`加载，然而在`ServiceLoader`中，要实例化第三方厂商的，部署在classpath下的`Service Provide`。根据类加载的传递性，`ServiceLoader`依赖的类，都应该由`BootstrapClassLoader`加载，但是`BootstrapClassLoader`无法加载位于classpath中的第三方厂商的`Service Provide`类

2、因此需要使用线程上下文类加载器`Thread Context ClassLoader`，线程上下文类加载器是`Thread`类中的一个成员属性

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408251751242.png" style="zoom:50%;" />



- 将某个具体的`ClassLoader`存储在线程中，然后再需要的时候取出来

- 通过`Thread.setContextClassLoader()`方法来进行设置，如果创建线程时还未设置，他将从父线程中继承，默认是`AppClassLoader`

- 通过线程上下文类加载器，Java SPI机制就可以反向调用低层的ClasssLoader了

  ```java
  // 获取线程上下文类加载器
  Thread.currentThread().getContextClassLoader()
  // 设置线程上下文类加载器
  Thread.currentThread().setContextClassLoader(new MyClassLoader());
  ```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202408251755567.png" style="zoom:50%;" />







### 5.5 自定义类加载器

**使用场景**

- 想加载非` classpath` 随意路径中的类文件
- 通过接口来使用实现，希望解耦时，常用在框架设计
- 这些类希望予以隔离，不同应用的同名类都可以加载，不冲突，常见于 tomcat 容器



**步骤**

1、继承 ClassLoader 父类

2、要遵从双亲委派机制，重写` findClass` 方法。如果要打破双亲委派机制，重写 `loadClass` 方法

3、读取类文件的字节码

4、调用父类的 `defineClass` 方法来加载类

5、使用者调用该类加载器的 `loadClass `方法



**实现**

```java
public class T05_ClassLoader_MyClassLoader extends ClassLoader {
 
    public static void main(String[] args) throws Exception {
        T05_ClassLoader_MyClassLoader classLoader = new T05_ClassLoader_MyClassLoader();
        Class<?> c1 = classLoader.loadClass("T05_ClassLoader_DefineV1");
        Class<?> c2 = classLoader.loadClass("T05_ClassLoader_DefineV1");
        System.out.println(c1 == c2); // true, 类文件只会加载一次，第一次加载放在自定义类加载器缓存中，第二次加载时发现缓存中已经有了
 
        // 如果使用不同的类加载器；确认唯一类，要包名、类名相同，同时类加载器也得相同，它会加载两次
        T05_ClassLoader_MyClassLoader classLoader2 = new T05_ClassLoader_MyClassLoader();
        Class<?> c3 = classLoader2.loadClass("T05_ClassLoader_DefineV1");
        System.out.println(c1 == c3); // false
 
        c1.newInstance();
    }
 
 
    @Override // name 就是类名称
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        String path = "/Users/li/class_dir/" + name + ".class";
 
        try {
            ByteArrayOutputStream os = new ByteArrayOutputStream();
            Files.copy(Paths.get(path), os);
 
            // 得到字节数组
            byte[] bytes = os.toByteArray();
 
            // byte[]  ->  *.class
            return defineClass(name, bytes, 0, bytes.length);
        } catch (IOException e) {
            e.printStackTrace();
            throw new ClassNotFoundException("类文件未找到", e);
        }
    }
}
```





### 5.6 运行期优化

#### 5.6.1 即时编译

**分层编译**

JVM 将执行状态分成了 5 个层次：

- 0层：解释执行，用解释器将字节码翻译为机器码
- 1层：使用 C1 即时编译器编译执行（不带 proﬁling）
- 2层：使用 C1 即时编译器编译执行（带基本的profiling）
- 3层：使用 C1 即时编译器编译执行（带完全的profiling）
- 4层：使用 C2 即时编译器编译执行

> `profiling` 是指在运行过程中收集一些程序执行状态的数据，例如【方法的调用次数】，【循环的 回边次数】等



**即时编译器（JIT）与解释器的区别**

- 解释器：将字节码解释为机器码，下次即使遇到相同的字节码，仍会执行重复的解释。将字节码解释为针对所有平台都通用的机器码

- 即时编译器：将一些字节码编译为机器码，并存入 Code Cache，下次遇到相同的代码，直接执行，无需再编译。根据平台类型，生成平台特定的机器码

> 对于大部分的不常用的代码，我们无需耗费时间将其编译成机器码，而是采取解释执行的方式运行
>
> 对于仅占据小部分的热点代码，我们则可以将其编译成机器码，以达到理想的运行速度
>
> 执行效率上： Interpreter < C1 < C2，总的目标是发现热点代码（hotspot名称的由来），并优化这些热点代码





#### 5.6.2 逃逸分析

> 逃逸分析（Escape Analysis）是Java Hotspot 虚拟机可以分析新创建对象的使用范围，并决定是否在 Java 堆上分配内存的一项技术

逃逸分析的 JVM 参数如下：

- 开启逃逸分析：`-XX:+DoEscapeAnalysis`
- 关闭逃逸分析：`-XX:-DoEscapeAnalysis`
- 显示分析结果：`-XX:+PrintEscapeAnalysis`
- 逃逸分析技术在 Java SE 6u23+ 开始支持，并默认设置为启用状态，可以不用额外加这个参数





**对象逃逸状态**

- 全局逃逸（GlobalEscape）

  即一个对象的作用范围逃出了当前方法或者当前线程，有以下几种场景

  - 对象是一个静态变量
  - 对象是一个已经发生逃逸的对象
  - 对象作为当前方法的返回值

- 参数逃逸（ArgEscape）

  即一个对象被作为方法参数传递或者被参数引用，但在调用过程中不会发生全局逃逸，这个状态是通过被调方法的字节码确定的

- 没有逃逸

  方法中的对象没有发生逃逸





#### 5.6.3 逃逸分析优化

当一个对象没有逃逸时，可以得到以下几个虚拟机的优化

- 锁消除

  > 线程同步锁是非常牺牲性能的，当编译器确定当前对象只有当前线程使用，那么就会移除该对象的同步锁
  >
  > 例如，`StringBuffer` 和` Vector` 都是用 `synchronized` 修饰线程安全的，但大部分情况下，它们都只是在当前线程中用的，这样编译器就会优化移除掉这些锁操作
  >
  > 锁消除的 JVM 参数如下：
  >
  > - 开启锁消除：`-XX:+EliminateLocks`
  > - 关闭锁消除：-`-XX:-EliminateLocks`
  > - 锁消除在 JDK8 中都是默认开启的，并且锁消除都要建立在逃逸分析的基础上

- 标量替换

  > 标量和聚合量：基础类型和对象的引用可以理解为标量，它们不能被进一步分解。而能被进一步分解的量就是聚合量，比如：对象，对象是聚合量，它又可以被进一步分解成标量，将其成员变量分解为分散的变量，称为标量替换。
  >
  > 如果一个对象没有发生逃逸，那么就不用创建它，只会在栈或者寄存器上创建它用到的成员标量，这样节省了内存空间，也提升了应用程序性能
  >
  > 标量替换的 JVM 参数如下：
  >
  > - 开启标量替换：`-XX:+EliminateAllocations`
  > - 关闭标量替换：`-XX:-EliminateAllocations`
  > - 显示标量替换详情：-`XX:+PrintEliminateAllocations`
  > - 标量替换同样在 JDK8 中都是默认开启的，并且都要建立在逃逸分析的基础上

- 栈上分配

  > 当对象没有发生逃逸时，该对象就可以通过标量替换分解成成员标量分配在栈内存中，和方法的生命周期一致，随着栈帧出栈时销毁，减少了 GC 压力，提高了应用程序性能





#### 5.6.4 方法内联

**JVM内联函数**

> C++ 是否为内联函数由自己决定，Java 由编译器决定。Java 不支持直接声明为内联函数，如果想让他内联，只能够向编译器提出请求，关键字`final`修饰 用来指明那个函数是希望被 JVM 内联的
>
> 一般的函数都不会被当做内联函数，只有声明了`final`后，编译器才会考虑是不是要把该函数变成内联函数

```java
public final void doSomething() {  
    // to do something  
}
```





**方法内联**

如果JVM监测到一些方法被频繁的执行，并且长度不太长时，会进行内联，内联就是把方法内代码拷贝、粘贴到调用者的位置

```java
private static int square(final int i) {
    return i * i;
}

System.out.println(square(9));
```

方法调用被替换后

```java
System.out.println(9 * 9);
```

还能够进行常量折叠（constant folding）的优化

```java
System.out.println(81);
```





#### 5.6.5 反射优化

```java
public class Reflect1 {
   public static void foo() {
      System.out.println("foo...");
   }

   public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
      Method foo = Demo3.class.getMethod("foo");
      for(int i = 0; i<=16; i++) {
         foo.invoke(null);
      }
   }
}
```

`foo.invoke` 0 ~ 15 次调用使用的是 `MethodAccessor `的 `NativeMethodAccessorImpl `实现



**invoke 方法源码**

```java
@CallerSensitive
public Object invoke(Object obj, Object... args)
    throws IllegalAccessException, IllegalArgumentException,
       InvocationTargetException
{
    if (!override) {
        if (!Reflection.quickCheckMemberAccess(clazz, modifiers)) {
            Class<?> caller = Reflection.getCallerClass();
            checkAccess(caller, clazz, obj, modifiers);
        }
    }
    //MethodAccessor是一个接口，有3个实现类，其中有一个是抽象类
    MethodAccessor ma = methodAccessor;             // read volatile
    if (ma == null) {
        ma = acquireMethodAccessor();
    }
    return ma.invoke(obj, args);
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202402102003907.png" style="zoom:80%;" />

会由 `DelegatingMehodAccessorImpl` 去调用 `NativeMethodAccessorImpl`



**NativeMethodAccessorImpl 源码**

```java
class NativeMethodAccessorImpl extends MethodAccessorImpl {
    private final Method method;
    private DelegatingMethodAccessorImpl parent;
    private int numInvocations;

    NativeMethodAccessorImpl(Method var1) {
        this.method = var1;
    }
	
	// 每次进行反射调用，会让numInvocation与ReflectionFactory.inflationThreshold的值（15）进行比较，并使使得		   numInvocation的值+1
	// 如果numInvocation <= inflationThreshold，则会调用本地方法invoke0
    // 如果numInvocation > inflationThreshold，会进入if，执行setDelegate方法，会将反射调用变为正常的方法调用，优化性能
    public Object invoke(Object var1, Object[] var2) throws IllegalArgumentException, InvocationTargetException {
        if (++this.numInvocations > ReflectionFactory.inflationThreshold() && !ReflectUtil.isVMAnonymousClass(this.method.getDeclaringClass())) {
            MethodAccessorImpl var3 = (MethodAccessorImpl)(new MethodAccessorGenerator()).generateMethod(this.method.getDeclaringClass(), this.method.getName(), this.method.getParameterTypes(), this.method.getReturnType(), this.method.getExceptionTypes(), this.method.getModifiers());
            this.parent.setDelegate(var3);
        }

        // numInvocations低于阈值时，执行这个方法，就是通过反射调用，执行速度很慢
        return invoke0(this.method, var1, var2);
    }

    void setParent(DelegatingMethodAccessorImpl var1) {
        this.parent = var1;
    }

    private static native Object invoke0(Method var0, Object var1, Object[] var2);
}
```

- 一开始if条件不满足，就会调用本地方法`invoke0`
- 随着` numInvocation` 的增大，当它大于` ReflectionFactory.inflationThreshold` 的值 15 时，就会将本地方法访问器替换为一个运行时动态生成的访问器，来提高效率，这时会从反射调用变为正常调用，即直接调用 `Reflect1.foo()`
