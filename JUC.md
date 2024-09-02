## 一、Java线程

### 1、创建和运行线程

#### 1.1 Thread

```java
// 构造方法的参数是给线程指定名字，推荐
Thread t1 = new Thread("t1") {
 	@Override
 	// run 方法内实现了要执行的任务
 	public void run() {
 		log.debug("hello");
 	}
};
t1.start();
```





#### 1.2 Runnable

- Thread 代表线程 
- Runnable 可运行的任务（线程要执行的代码）
- Thread中不能接受Callable对象

```java
// 创建任务对象
Runnable task2 = new Runnable() {
     @Override
     public void run() {
     	log.debug("hello");
     }
};
// 参数1 是任务对象; 参数2 是线程名字，推荐
Thread t2 = new Thread(task2, "t2");
t2.start(); 
```



**Java 8 可以使用 lambda 简化代码**

如果该接口只有一个方法，则可以使用lambda优化

```java
Runnable task2 = () -> { log.debug("hello"); };

Thread t2 = new Thread(task2, "t2");
t2.start(); 
```

```java
Thread t2 = new Thread(() -> { log.debug("hello"); }, "t2");
t2.start(); 
```





#### 1.3 FutureTask

`FutureTask` 能够接收 `Callable` 类型的参数，用来处理有返回结果的情况



**Runnable接口**

其中`run()`方法没有返回值也不会抛出异常

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```



**Callable接口**

`Callable`接口的`call()`方法有返回值，也会抛出异常

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```



**FutureTask接口**

`FutureTask`接口实现了`Runnable`和`Future`接口，因此他也可以作为`Thread`类的参数传递

`FutureTask`接口可以接收`Callable`类型的参数，同时可以通过`get`方法获取返回值结果，他的`run`方法就是执行`callable`的`call`方法

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}

public class FutureTask<V> implements RunnableFuture<V> {
    ...
    public FutureTask(Runnable runnable, V result) {
        this.callable = Executors.callable(runnable, result);
        this.state = NEW;       // ensure visibility of callable
    }
    
    public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // ensure visibility of callable
    }
        
    public boolean isCancelled() {
        return state >= CANCELLED;
    }

    public boolean isDone() {
        return state != NEW;
    }

    public boolean cancel(boolean mayInterruptIfRunning) {
        ...
    }

    public V get() throws InterruptedException, ExecutionException {
        int s = state;
        if (s <= COMPLETING)
            s = awaitDone(false, 0L);
        return report(s);
    }

    protected void done() { }

    protected void set(V v) {
        if (UNSAFE.compareAndSwapInt(this, stateOffset, NEW, COMPLETING)) {
            outcome = v;
            UNSAFE.putOrderedInt(this, stateOffset, NORMAL); // final state
            finishCompletion();
        }
    }

    public void run() {
        if (state != NEW ||
            !UNSAFE.compareAndSwapObject(this, runnerOffset,
                                         null, Thread.currentThread()))
            return;
        try {
            Callable<V> c = callable;
            if (c != null && state == NEW) {
                V result;
                boolean ran;
                try {
                    result = c.call();
                    ran = true;
                } catch (Throwable ex) {
                    result = null;
                    ran = false;
                    setException(ex);
                }
                if (ran)
                    set(result);
            }
        } finally {
            // runner must be non-null until state is settled to
            // prevent concurrent calls to run()
            runner = null;
            // state must be re-read after nulling runner to prevent
            // leaked interrupts
            int s = state;
            if (s >= INTERRUPTING)
                handlePossibleCancellationInterrupt(s);
        }
    }
    ...
}
```





**测试**

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    FutureTask<Integer> task = new FutureTask<>(new Callable<Integer>() {
        @Override
        public Integer call() throws Exception {
            System.out.println("hello");
            Thread.sleep(2000);
            return 100;
        }
    });

    new Thread(task, "t1").start();

    // task.get()方法会阻塞在这里，直到得到结果
    Integer res = task.get();
    System.out.println(res);
}
```



lambda优化

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    FutureTask<Integer> task = new FutureTask<>(() -> {
            System.out.println("hello");
            Thread.sleep(2000);
            return 100;
        }
    );
    
    new Thread(task, "t1").start();

    // task.get()方法会阻塞在这里，直到得到结果
    Integer res = task.get();
    System.out.println(res);
}
```







### 2、线程方法

| 方法               | static   | 说明                                                         | 注意                                                         |
| ------------------ | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `start()`          |          | 启动一个新线程，在新的线程 运行 run 方法 中的代码            | `start` 方法只是让线程进入就绪，里面代码不一定立刻 运行（CPU 的时间片还没分给它）。每个线程对象的 `start`方法只能调用一次，如果调用了多次会出现 `IllegalThreadStateException` |
| `run()`            |          | 新线程启动后会调用的方法                                     | 如果在构造 Thread 对象时传递了 Runnable 参数，则 线程启动后会调用 Runnable 中的 run 方法，否则默 认不执行任何操作。但可以创建 Thread 的子类对象， 来覆盖默认行为 |
| `join()`           |          | 等待线程运行结束，即插入线程，这个线程执行完毕，才会继续执行当前线程 |                                                              |
| `join(long n)  `   |          | 等待线程运行结束, 最多等待 n 毫秒                            |                                                              |
| `getId()`          |          | 获取线程长整型的 id                                          | id 唯一                                                      |
| `getName()`        |          | 获取线程名                                                   |                                                              |
| `setName(String)`  |          | 修改线程名                                                   |                                                              |
| `getPriority()`    |          | 获取线程优先级                                               |                                                              |
| `setPriority(int)` |          | 修改线程优先级                                               | java中规定线程优先级是1~10 的整数，较大的优先级 能提高该线程被 CPU 调度的机率 |
| `getState()`       |          | 获取线程状态                                                 | Java 中线程状态是用 6 个 enum 表示，分别为：` NEW`, `RUNNABLE, BLOCKED`, `WAITING`,  `TIMED_WAITING`, `TERMINATED` |
| `isInterrupted()`  |          | 判断是否被打断                                               | 不会清除打断标记                                             |
| `isAlive()`        |          | 线程是否存活 （还没有运行完 毕）                             |                                                              |
| `interrupt()`      |          | 打断线程                                                     | 如果被打断线程正在 `sleep`，`wait`，`join` 会导致被打断的线程抛出 `InterruptedException，`并清除打断标记 ；如果打断的是正在运行的线程，则会设置打断标记 ；park 的线程被打断，也会设置打断标记 |
| `interrupted()`    | `static` | 判断当前线程是否被打断                                       | 会清除打断标记                                               |
| `currentThread()`  | `static` | 获取当前正在执行的线程                                       |                                                              |
| `sleep(long n)`    | `static` | 让当前执行的线程休眠n毫秒， 休眠时让出 cpu  的时间片给其它 线程 |                                                              |
| `yield()`          | `static` | 提示线程调度器让出当前线程对 CPU的使用                       | 主要是为了测试和调试                                         |



#### 2.1 sleep

1、调用 `sleep` 会让当前线程从 Running 进入 Timed Waiting 状态（阻塞）

2、其它线程可以使用 `interrupt` 方法打断正在睡眠的线程，这时 `sleep` 方法会抛出 `InterruptedException `

3、睡眠结束后的线程未必会立刻得到执行 

4、建议用 `TimeUnit` 的 `sleep` 代替 `Thread` 的 `sleep` 来获得更好的可读性

5、`sleep()` 方法的过程中，**线程不会释放对象锁**





#### 2.2 yield

1、调用 `yield `会让当前线程从 Running 进入 Runnable 就绪状态，然后调度执行其它线程 

2、具体的实现依赖于操作系统的任务调度器。线程礼让。让出cpu，让其他线程执行，在cpu资源充足的时候，不需礼让，只有在cpu资源不够的时候，礼让才有效

3、**会放弃 CPU 资源，锁资源不会释放**





#### 2.3 线程优先级

1、线程优先级会提示（hint）调度器优先调度该线程，但它仅仅是一个提示，调度器可以忽略它 

2、如果 cpu 比较忙，那么优先级高的线程会获得更多的时间片，但 cpu 空闲时，优先级几乎没作用





#### 2.4 join

`public final synchronized void join()`：等待这个线程结束

原理：调用者轮询检查线程 alive 状态，`t1.join()` 等价于：

```java
public final synchronized void join(long millis) throws InterruptedException {
    // 调用者线程进入 thread 的 waitSet 等待, 直到当前线程运行结束
    while (isAlive()) {
        wait(0);
    }
}
```

- `join` 方法是被 `synchronized` 修饰的，本质上是一个对象锁，其内部的 `wait` 方法调用也是释放锁的，但是**释放的是当前的线程对象锁，而不是外面的锁**
- 当调用某个线程（t1）的 join 方法后，该线程（t1）抢占到 CPU 资源，就不再释放，直到线程执行完毕



线程同步：

- join 实现线程同步，因为会阻塞等待另一个线程的结束，才能继续向下运行
  - 需要外部共享变量，不符合面向对象封装的思想
  - 必须等待线程结束，不能配合线程池使用
- Future 实现（同步）：`get() `方法阻塞等待执行结果
  - main 线程接收结果
  - get 方法是让调用线程同步等待

```java
public class Test {
    static int r = 0;
    public static void main(String[] args) throws InterruptedException {
        test1();
    }
    private static void test1() throws InterruptedException {
        Thread t1 = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            r = 10;
        });
        t1.start();
        t1.join(); // 等待线程执行结束，输出的10
        System.out.println(r);
    }
}
```



**有时效的 join**

`t1.join(1500)`，最多等待1500ms，如果1500ms后t1线程还未结束，也会继续执行当前线程





#### 2.5 interrupt 

`public void interrupt()`：打断这个线程，异常处理机制

`public static boolean interrupted()`：判断当前线程是否被打断，打断返回 true，**清除打断标记**，连续调用两次一定返回 false

`public boolean isInterrupted()`：判断当前线程是否被打断，不清除打断标记

打断的线程会发生上下文切换，操作系统会保存线程信息，抢占到 CPU 后会从中断的地方接着运行（打断不是停止）



`sleep`，`wait`，`join` 这几个方法都会让线程进入阻塞状态 

打断线程，会清空打断状态

```java
private static void test1() throws InterruptedException {
    Thread t1 = new Thread(()->{
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }, "t1");
    t1.start();
    Thread.sleep(500);
    t1.interrupt();
    System.out.println(" 打断状态: " + t1.isInterrupted());  // false
}
```



打断正常运行的线程，不会清空打断状态

```java
private static void test2() throws InterruptedException {
    Thread t2 = new Thread(()->{
        while(true) {
            Thread current = Thread.currentThread();
            boolean interrupted = current.isInterrupted();
            if (interrupted) {
                System.out.println(" 打断状态: " + interrupted); // true
                break;
            }
        }
    }, "t2");
    t2.start();
    Thread.sleep(500);
    t2.interrupt();
}
```





#### 2.6 两阶段终止

需求：在一个线程 T1 中 **优雅** 终止线程 T2，这里的【优雅】指的是给 T2 一个后置处理的机会。 

- 使用线程对象的 `stop()` 方法停止线程 
  - stop 方法会真正杀死线程，如果这时线程锁住了共享资源，那么它被杀死后就再也没有机会释放锁，其它线程将永远无法获取锁 

- 使用 `System.exit(int)` 方法停止线程 
  - 目的仅是停止一个线程，但这种做法会让整个程序都停止




1、`while`循环，判断当前线程是否被打断，是的话就进行后置处理，同时退出

2、如果没有打断，就睡眠两秒再判断，如果在睡眠过程中被打断了，那么会抛出异常，但是此时打断标记为`false`，所以应该手动将打断标记设为`True`，下次循环就会退出了

3、如果睡眠结束后被打断，那么打断标记就是`True`，下次循环就会退出

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401082254891.png)

```java
public class Test {
    public static void main(String[] args) throws InterruptedException {
        TwoPhaseTermination tpt = new TwoPhaseTermination();
        tpt.start();
        Thread.sleep(3500);
        tpt.stop();
    }
}

class TwoPhaseTermination {
    private Thread monitor;

    // 启动监控线程
    public void start() {
        monitor = new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    Thread thread = Thread.currentThread();
                    if (thread.isInterrupted()) {
                        System.out.println("后置处理");
                        break;
                    }
                    try {
                        Thread.sleep(1000);                    // 睡眠
                        System.out.println("执行监控记录");    // 在此被打断不会异常
                    } catch (InterruptedException e) {        // 在睡眠期间被打断，进入异常处理的逻辑
                        e.printStackTrace();
                        // 重新设置打断标记，打断 sleep 会清除打断状态
                        thread.interrupt();
                    }
                }
            }
        });
        monitor.start();
    }

    // 停止监控线程
    public void stop() {
        monitor.interrupt();
    }
}
```





#### 2.7 打断park

park 作用类似 sleep，打断 park 线程，不会清空打断状态

```java
public static void main(String[] args) throws Exception {
    Thread t1 = new Thread(() -> {
        System.out.println("park...");
        LockSupport.park();
        System.out.println("unpark...");
        System.out.println("打断状态：" + Thread.currentThread().isInterrupted());//打断状态：true
    }, "t1");
    t1.start();
    Thread.sleep(2000);
    t1.interrupt();
}
```



如果打断标记已经是 true, 则 park 会失效，因此可以使用`Thread.interrupted()`判断完是否被打断后，同时清除打断标记

```java
public static void main(String[] args) throws Exception {
    Thread t1 = new Thread(() -> {
        System.out.println("park...");
        LockSupport.park();
        System.out.println("unpark...");
        System.out.println("打断状态：" + Thread.currentThread().isInterrupted());//打断状态：true

        LockSupport.park(); //因为打断状态为true，所以park失效，不会阻塞
        System.out.println("unpark..."); 
    }, "t1");
    t1.start();
    Thread.sleep(2000);
    t1.interrupt();
}
```







#### 2.8 守护线程

默认情况下，Java 进程需要等待所有线程都运行结束，才会结束。

有一种特殊的线程叫做守护线程，只要其它非守护线程运行结束了，即使守护线程的代码没有执行完，也会强制结束

```java
Thread t = new Thread() {
    @Override
    public void run() {
        System.out.println("running");
    }
};
// 设置该线程为守护线程
t.setDaemon(true);
t.start();
```



- 用户线程：平常创建的普通线程

- 守护线程：服务于用户线程，只要其它非守护线程运行结束了，即使守护线程代码没有执行完，也会强制结束。守护进程是**脱离于终端并且在后台运行的进程**，脱离终端是为了避免在执行的过程中的信息在终端上显示



> 当运行的线程都是守护线程，Java 虚拟机将退出，因为普通线程执行完后，JVM 是守护线程，不会继续运行下去
>
> 常见的守护线程：
>
> - 垃圾回收器线程就是一种守护线程
> - Tomcat 中的 Acceptor 和 Poller 线程都是守护线程，所以 Tomcat 接收到 shutdown 命令后，不会等待它们处理完当前请求





#### 2.9 不推荐的方法

这些方法已过时，容易破坏同步代码块，造成线程死锁

- `public final void stop()`：停止线程运行

  废弃原因：方法粗暴，除非可能执行 finally 代码块以及释放 synchronized 外，线程将直接被终止，如果线程持有 JUC 的互斥锁可能导致锁来不及释放，造成其他线程永远等待的局面

- `public final void suspend()`：**挂起（暂停）线程运行**

  废弃原因：如果目标线程在暂停时对系统资源持有锁，则在目标线程恢复之前没有线程可以访问该资源，如果**恢复目标线程的线程**在调用 resume 之前会尝试访问此共享资源，则会导致死锁

- `public final void resume()`：恢复线程运行







### 3、线程状态

**操作系统层面**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401082354061.png" style="zoom:80%;" />

- 【初始状态】仅是在语言层面创建了线程对象，还未与操作系统线程关联 
- 【可运行状态】（就绪状态）指该线程已经被创建（与操作系统线程关联），可以由 CPU 调度执行 
- 【运行状态】指获取了 CPU 时间片运行中的状态 
  - 当 CPU 时间片用完，会从【运行状态】转换至【可运行状态】，会导致线程的上下文切换 

- 【阻塞状态】 
  - 如果调用了阻塞 API，如 BIO 读写文件，这时该线程实际不会用到 CPU，会导致线程上下文切换，进入【阻塞状态】 
  - 等 BIO 操作完毕，会由操作系统唤醒阻塞的线程，转换至【可运行状态】 
  - 与【可运行状态】的区别是，对【阻塞状态】的线程来说只要它们一直不唤醒，调度器就一直不会考虑调度它们 

- 【终止状态】表示线程已经执行完毕，生命周期已经结束，不会再转换为其它状态



**Java API 层面**

根据 `Thread.State `枚举，分为六种状态

<img src="https://cdn.nlark.com/yuque/0/2022/png/393192/1649066238858-9cf6c08d-c3aa-478c-a3b0-f32e64f1978a.png" alt="image.png" style="zoom:80%;" />

- `NEW`线程刚被创建，但是还没有调用 `start()` 方法 
- `RUNNABLE` 当调用了 `start()` 方法之后，注意，**Java API** 层面的 `RUNNABLE` 状态涵盖了 **操作系统** 层面的【可运行状态】、【运行状态】和【阻塞状态】（由于 BIO 导致的线程阻塞，在 Java 里无法区分，仍然认为是可运行） 
- `BLOCKED ， WAITING ， TIMED_WAITING` 都是 **Java API** 层面对【阻塞状态】的细分
- `TERMINATED` 当线程代码运行结束





### 4、线程原理

1、Java Virtual Machine Stacks（Java 虚拟机栈）：每个线程启动后，虚拟机就会为其分配一块栈内存

- 每个栈由多个栈帧（Frame）组成，对应着每次方法调用时所占用的内存
- 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法

2、线程上下文切换（Thread Context Switch）：一些原因导致 CPU 不再执行当前线程，转而执行另一个线程

- 线程的 CPU 时间片用完
- 垃圾回收
- 有更高优先级的线程需要运行
- 线程自己调用了 sleep、yield、wait、join、park 等方法

3、程序计数器（Program Counter Register）：记住下一条 JVM 指令的执行地址，是线程私有的

4、当 Context Switch 发生时，需要由操作系统保存当前线程的状态（PCB 中），并恢复另一个线程的状态，包括程序计数器、虚拟机栈中每个栈帧的信息，如局部变量、操作数栈、返回地址等

5、JVM 规范并没有限定线程模型，以 HotSopot 为例：

- Java 的线程是内核级线程（1:1 线程模型），每个 Java 线程都映射到一个操作系统原生线程，需要消耗一定的内核资源（堆栈）
- **线程的调度是在内核态运行的，而线程中的代码是在用户态运行**，所以线程切换（状态改变）会导致用户与内核态转换进行系统调用，这是非常消耗性能

6、Java 中 main 方法启动的是一个进程也是一个主线程，main 方法里面的其他线程均为子线程，main 线程是这些线程的父线程







## 二、共享模型-管程

### 1、共享带来的问题

**需求：**两个线程对初始值为 0 的静态变量一个做自增，一个做自减，各做 5000 次，结果是 0 吗？

```java
static int counter = 0;
public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 5000; i++) {
            counter++;
        }
    }, "t1");
    
    Thread t2 = new Thread(() -> {
        for (int i = 0; i < 5000; i++) {
            counter--;
        }
    }, "t2");
    
    t1.start();
    t2.start();
    t1.join();
    t2.join();
    
    log.debug("{}",counter);
}
```

**结果：**可能为正数、负数、0



**分析**： Java 中对静态变量的自增，自减不是原子操作，对于`i++`而言（**i为静态变量**），实际产生的JVM字节码如下

```java
getstatic i // 获取静态变量i的值
iconst_1 // 将常量值 1 推送到操作数栈顶
iadd // 将操作数栈顶的两个值相加
putstatic i // 将修改后的值存入静态变量i
```

对于`i--`对应的字节码为

```java
getstatic i // 获取静态变量i的值
iconst_1 // 将常量值 1 推送到操作数栈顶
isub // 将操作数栈顶的两个值相减
putstatic i // 将修改后的值存入静态变量i
```



 Java 的内存模型如下，完成静态变量的自增，自减需要在主存和工作内存中进行数据交换

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141710494.png" style="zoom:80%;" />

- 如果是单线程以上 8 行代码是顺序执行（不会交错）没有问题

  <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141710607.png" style="zoom:80%;" />

- 多线程下这 8 行代码可能交错运行

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141711061.png" style="zoom:80%;" />                     <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141711110.png" style="zoom:80%;" />      





**局部变量**

对于局部变量`i`，执行`i++`

```java
public class Demo {
    public static void main(String[] args) {
        int i = 0;
        i++;
    }
}
```

对应的字节码为，局部变量的`i++`在多线程情况下没有线程安全问题，因为每个线程都会创建自己的栈帧，只对自己的变量进行操作

```java
0: iconst_0             // 将常量值 0 推送到操作数栈顶
1: istore_1             // 将操作数栈顶的值存储到局部变量 i
2: iinc      1, 1       // 将局部变量 i 的值增加 1
5: return
```







### 2、临界区 Critical Section

- 一个程序运行多个线程本身是没有问题的 
- 问题出在多个线程访问**共享资源** 
  - 多个线程读**共享资源**其实也没有问题 
  - 在多个线程对**共享资源**读写操作时发生指令交错，就会出现问题 

- 一段代码块内如果存在对**共享资源**的多线程读写操作，称这段代码块为**临界区** 



如下代码存在临界区

```java
static int counter = 0;
static void increment()
// 临界区
{
    counter++; 
}

static void decrement()
// 临界区
{
    counter--; 
}
```



**竞态条件** 

`Race Condition` 多个线程在临界区内执行，由于代码的执行序列不同而导致结果无法预测，称之为发生了竞态条件





### 3、Synchronized

>  Java 中互斥和同步都可以采用 synchronized 关键字来完成，但它们是有区别的： 
>
> - 互斥是保证临界区的竞态条件发生，同一时刻只能有一个线程执行临界区代码 
> - 同步是由于线程执行的先后、顺序不同、需要一个线程等待其它线程运行到某个点



`synchronized` 关键字的使用方式主要有下面 3 种

- 修饰实例方法

- 修饰静态方法

- 修饰代码块



**1、修饰实例方法** 

给当前对象实例加锁，进入同步代码前要获得 **当前对象实例的锁** 。

```java
synchronized void method() {
    //业务代码
}
```



**2、修饰静态方法**

给当前类加锁，会作用于类的所有对象实例 ，进入同步代码前要获得 **当前 class 的锁**。

这是因为静态成员不属于任何一个实例对象，归整个类所有，不依赖于类的特定实例，被类的所有实例共享。

```java
synchronized static void method() {
    //业务代码
}
```

> 静态 `synchronized` 方法和非静态 `synchronized` 方法之间的调用不互斥
>
> - 如果一个线程 A 调用一个实例对象的非静态 `synchronized` 方法，而线程 B 需要调用这个实例对象所属类的静态 `synchronized` 方法，是允许的，不会发生互斥现象，因为访问静态 `synchronized` 方法占用的锁是当前类的锁，而访问非静态 `synchronized` 方法占用的锁是当前实例对象锁。



**3、修饰代码块** 

对括号里指定的对象/类加锁：

- `synchronized(object)` 表示进入同步代码库前要获得 **给定对象的锁**。
- `synchronized(类.class)` 表示进入同步代码前要获得 **给定 Class 的锁**

```java
synchronized(this) {
    //业务代码
}
```



**4、总结：**

- `synchronized` 关键字加到 `static` 静态方法和 `synchronized(class)` 代码块上都是是给 Class 类上锁；
- `synchronized` 关键字加到实例方法上是给对象实例上锁；
- 尽量不要使用 `synchronized(String a)` ，因为 JVM 中，字符串常量池具有缓存功能，多个线程使用相同的字符串值，实际使用的是同一个对象





### 4、变量的线程安全

成员变量和静态变量是否线程安全？ 

- 如果它们没有共享，则线程安全 

- 如果它们被共享了，根据它们的状态是否能够改变，又分两种情况 

  - 如果只有读操作，则线程安全 

  - 如果有读写操作，则这段代码是临界区，需要考虑线程安全 



局部变量是否线程安全？ 

- 局部变量是线程安全的 

- 但局部变量引用的对象则未必 

  - 如果该对象没有逃离方法的作用范围，它是线程安全的 

  - 如果该对象逃离方法的作用范围，需要考虑线程安全





#### 4.1 成员变量线程安全

```java
static final int THREAD_NUMBER = 2;
static final int LOOP_NUMBER = 200;
public static void main(String[] args) {
    ThreadUnsafe test = new ThreadUnsafe();
    for (int i = 0; i < THREAD_NUMBER; i++) {
        new Thread(() -> {
            test.method1(LOOP_NUMBER);
        }, "Thread" + i).start();
    }
}

class ThreadUnsafe {
    ArrayList<String> list = new ArrayList<>();
    public void method1(int loopNumber) {
        for (int i = 0; i < loopNumber; i++) {
            // { 临界区, 会产生竞态条件
            method2();
            method3();
            // } 临界区
        }
    }
    private void method2() {
        list.add("1");
    }
    private void method3() {
        list.remove(0);
    }
}
```



可能出现的线程安全问题：

- 如果线程2 还未 add，线程1 remove 就会报错

- > 1、t1 add之后准备将size记为1，但还没记的时候被 t2抢走,此时size仍为0
  >
  > 2、t2 add操作,并成功将size记为1,然后又被t1抢回,
  >
  > 3、t1 继续未完操作,再次将size记为1,这时又被t2抢走
  >
  > 4、t2 继续操作,remove之后,size记为0,然后又被t1抢走
  >
  > 5、此时t1再去remove时发现size为0,就报了异常

如果将`list`设置为`method1`中的局部变量就不会有线程安全问题了，因为每创建一个线程都会创建一个list对象，互不影响





#### 4.2 private修饰符的作用

对于上边的代码，将`list`作为局部变量，`method2`和`method3`使用`private`修饰是线程安全的，如果将其修改为`public`就会有线程安全问题

- 有其它线程调用 `method2` 和 `method3 `

- 在 情况1 的基础上，为 `ThreadSafe` 类添加子类，子类覆盖 `method2` 或 `method3` 方法

  ```java
  class ThreadSafe {
      public final void method1(int loopNumber) {
          ArrayList<String> list = new ArrayList<>();
          for (int i = 0; i < loopNumber; i++) {
              method2(list);
              method3(list);
          }
      }
      public void method2(ArrayList<String> list) {
          list.add("1");
      }
      public void method3(ArrayList<String> list) {
          list.remove(0);
      }
  }
  
  class ThreadSafeSubClass extends ThreadSafe{
      @Override
      public void method3(ArrayList<String> list) {
          new Thread(() -> {
              list.remove(0);
          }).start();
      }
  }
  ```

  从这个例子可以看出 `private` 或 `final` 提供【安全】的意义所在，也就是开闭原则中的**闭**





#### 4.3 线程安全类

常用的线程安全类如下：

- String 
- Integer 
- StringBuffffer 
- Random 
- Vector 
- Hashtable 
- java.util.concurrent 包下的类



> 1、这些类的每个方法是原子性的，但是多个方法的组合不是原子性的
>
> 2、`String`、`Integer` 等都是不可变类，因为其内部的状态不可以改变，因此它们的方法都是线程安全的 
>
> 3、`String` 的`replace`，`substring `等方法改变值是通过重新创建一个字符串实现的





**案例一**

以下`MyAspect`类是线程不安全的，因为默认情况下，它在ioc容器中是单例的，即多个线程共享。线程1执行完`before`方法后，线程2也去执行`before`方法，此时`start`属性被修改，线程1去执行`after`方法时，计算的时间就不对了。可以使用**环绕通知**来解决，因为环绕通知会将这些变量作为局部变量，保证线程安全

```java
@Aspect
@Component
public class MyAspect {
    private long start = 0L;

    @Before("execution(* *(..))")
    public void before() {
        start = System.nanoTime();
    }

    @After("execution(* *(..))")
    public void after() {
        long end = System.nanoTime();
        System.out.println("cost time:" + (end-start));
    }
}
```



**案例二**

其中 foo 的行为是不确定的，可能导致不安全的发生，被称之为**外星方法**，虽然`sdf`是局部变量，然后通过`foo(sdf)`方法将该对象暴露出去了，产生线程安全问题。所以`String`类使用`final`修饰，如果不使用`final`修饰，其子类就会覆盖掉`String`类中的方法，可能导致线程安全问题

```java
public abstract class Test {

    public void bar() {
        // 是否安全
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        foo(sdf);
    }

    public abstract foo(SimpleDateFormat sdf);


    public static void main(String[] args) {
        new Test().bar();
    }
    
}
```

```java
public void foo(SimpleDateFormat sdf) {
    String dateStr = "1999-10-11 00:00:00";
    for (int i = 0; i < 20; i++) {
        new Thread(() -> {
            try {
                sdf.parse(dateStr);
            } catch (ParseException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```



**`SimpleDateFormat`线程不安全的原因**

> `SimpleDateFormat`继承自`DateFormat`，而`DateFormat`内部有一个`Calendar`对象，是被`protected`修饰的，即子类`SimpleDateFormat`可以使用

```java
public class SimpleDateFormat extends DateFormat {

    // the official serial version ID which says cryptically
    // which version we're compatible with
    static final long serialVersionUID = 4774881970558875024L;

    // the internal serial version which says which version was written
    // - 0 (default) for version up to JDK 1.1.3
    // - 1 for version from JDK 1.1.4, which includes a new field
    static final int currentSerialVersion = 1;
	...    
}

public abstract class DateFormat extends Format {

    /**
     * The {@link Calendar} instance used for calculating the date-time fields
     * and the instant of time. This field is used for both formatting and
     * parsing.
     *
     * <p>Subclasses should initialize this field to a {@link Calendar}
     * appropriate for the {@link Locale} associated with this
     * <code>DateFormat</code>.
     * @serial
     */
    protected Calendar calendar;
}
```



**1、format方法线程不安全**

> `format`方法源码如下，其中`calendar.setTime(date)`这行代码会使用成员变量`calendar`来设置时间，此时就会导致线程不安全。如果线程1 set后，让出cpu，线程2继续来执行，线程2重新set，然后线程1拿到的就是错误的数据

```java
private StringBuffer format(Date date, StringBuffer toAppendTo,
                            FieldDelegate delegate) {
    // Convert input date to time field list
    calendar.setTime(date);

    boolean useDateFormatSymbols = useDateFormatSymbols();

    for (int i = 0; i < compiledPattern.length; ) {
        int tag = compiledPattern[i] >>> 8;
        int count = compiledPattern[i++] & 0xff;
        if (count == 255) {
            count = compiledPattern[i++] << 16;
            count |= compiledPattern[i++];
        }

        switch (tag) {
        case TAG_QUOTE_ASCII_CHAR:
            toAppendTo.append((char)count);
            break;

        case TAG_QUOTE_CHARS:
            toAppendTo.append(compiledPattern, i, count);
            i += count;
            break;

        default:
            subFormat(tag, count, delegate, toAppendTo, useDateFormatSymbols);
            break;
        }
    }
    return toAppendTo;
}
```



**2、parse方法线程不安全**

> 以下是`parse`方法部分源码，其中`establish`方法中，会执行`cal.clear()`，使用的也是成员变量`calendar`，因此多线程环境下也会有线程安全问题

```java
@Override
public Date parse(String text, ParsePosition pos)
{
    checkNegativeNumberExpression();

    int start = pos.index;
    int oldStart = start;
    int textLength = text.length();

    boolean[] ambiguousYear = {false};

    ...
    Date parsedDate;
        try {
            parsedDate = calb.establish(calendar).getTime();
            // If the year value is ambiguous,
            // then the two-digit year == the default start year
            if (ambiguousYear[0]) {
                if (parsedDate.before(defaultCenturyStart)) {
                    parsedDate = calb.addYear(100).establish(calendar).getTime();
                }
            }
        }
	...

        return parsedDate;
}

Calendar establish(Calendar cal) {
	...

    cal.clear();
    // Set the fields from the min stamp to the max stamp so that
    // the field resolution works in the Calendar.
    for (int stamp = MINIMUM_USER_STAMP; stamp < nextStamp; stamp++) {
        for (int index = 0; index <= maxFieldIndex; index++) {
            if (field[index] == stamp) {
                cal.set(index, field[MAX_FIELD + index]);
                break;
            }
        }
    }
    ...
    return cal;
}

```





**案例三**

这段代码会发生线程安全问题。因为`Integer`对象是不可变的，使用`synchronized (i)`锁的是`Integer`对象，而`Integer`对象执行`i++`时，首先读取`i`的值，加一后，再调用`Integer.valueOf()`重新创建一个`Integer`对象赋值给`i`，所以会产生线程安全问题

```java
private static Integer i = 0;
public static void main(String[] args) throws InterruptedException {
    List<Thread> list = new ArrayList<>();
    for (int j = 0; j < 2; j++) {
        Thread thread = new Thread(() -> {
            for (int k = 0; k < 5000; k++) {
                synchronized (i) {
                    i++;
                }
            }
        }, "" + j);
        list.add(thread);
    }
    list.stream().forEach(t -> t.start());
    list.stream().forEach(t -> {
        try {
            t.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
    log.debug("{}", i);
}
```







### 5、Monitor

Java对象由三部分组成

- 对象头
- 对象体：对象体里放的是非静态的属性，也包括父类的所有非静态属性（private修饰的也在这里，不区分可见性修饰符），基本类型的属性存放的是具体的值，引用类型及数组类型存放的是引用指针。
- 对齐填充



#### 5.1 Java对象头

32 位虚拟机的对象头

**普通对象**

`Mark Word` ：存储对象自身的运行时数据，hashCode、gc年龄以及锁信息等

`Klass Word` ：指向Class对象

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141713214.png)

**数组对象**

相对于普通对象多了记录数组长度

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141713152.png)



> 所以对于一个int类型整数来说，它占用4字节，而一个`Integer`对象，在32位虚拟机中包含了8字节对象头，4字节数据，一共12字节，加上内存对齐，就是16字节



64位虚拟机的对象头

- Markword：存储对象自身运行时数据如hashcode、gc分代年龄及锁信息等，64位系统总共占用8个字节。
- 类型指针：对象指向类元数据地址的指针，jdk8默认开启指针压缩，64位系统占4个字节
- 数组长度：若对象不是数组，则没有该部分，不分配空间大小，若是数组，则为4个字节长度

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141723985.png)







#### 5.2 Mark Word 结构

**32位虚拟机**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141715179.png)



**64位虚拟机**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141715001.png)

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141725133.png" style="zoom:80%;" />

- 对象的hashCode占31位，重写类的hashCode方法返回int类型，只有在无锁情况下，在有调用的情况下会计算该值并写到对象头中，其他情况该值是空的。
- 分代年龄占4位，最大值也就是15，在GC中，当survivor区中对象复制一次，年龄加1，默认是到15之后会移动到老年代。
- 是否偏向锁占1位，无锁和偏向锁的最后两位都是01，使用这一位来标识区分是无锁还是偏向锁。
- 锁标志位占2位，锁状态标记位，同`是否偏向锁标志位`标识对象处于什么锁状态。
- 偏向线程ID占54位，只有偏向锁状态才有，这个ID是操作系统层面的线程唯一id，跟java中的线程id是不一致的
  





#### 5.3 Moniter

Moniter称为**监视器**或者**管程**，是操作系统提供的对象

每个Java对象都可以关联一个Moniter对象，如果使用`synchronized`给对象上锁（重量级），该对象的Mark Word中就被设置指向Moniter对象的指针



**Moniter结构**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401141817549.png)

- 刚开始Moniter中Owner为null
- 当Thread-2执行`synchronized(obj)`后，就会将Moniter的所有者Owner置位Thread-2，Moniter只能有一个Owner
  - `obj`对象的`MarkWord`中最初保存的是对象的hashcode、gc年龄等信息，同时锁标志位为01，表示无锁。当获取锁后，会将这些信息保存在Moniter对象中，然后`MarkWord`存储的就是指向Moniter的指针，锁标志位为10（重量级锁）
- 在Thread-2上锁的过程中，如果Thread-1、Thread-3也来执行`synchronized(obj)`，就会进入`EntryList`，处于`BLOCKED`状态
- Thread-2执行完同步代码块的内容后，唤醒`EntryList`中等待的线程来竞争锁，竞争是非公平的







#### 5.4 Synchronized 字节码

```java
static final Object lock = new Object();
static int counter = 0;
public static void main(String[] args) {
    synchronized (lock) {
        counter++;
    }
}
```



**对应的字节码为**

```java
public static void main(java.lang.String[]);
descriptor: ([Ljava/lang/String;)V
              flags: ACC_PUBLIC, ACC_STATIC
              Code:
              stack=2, locals=3, args_size=1
              0: getstatic #2 // <- lock引用 （synchronized开始）
              3: dup
              4: astore_1 // lock引用 -> slot 1
              5: monitorenter // 将 lock对象 MarkWord 置为 Monitor 指针
              6: getstatic #3 // <- i
              9: iconst_1 // 准备常数 1
              10: iadd // +1
              11: putstatic #3 // -> i
              14: aload_1 // <- lock引用
              15: monitorexit // 将 lock对象 MarkWord 重置, 唤醒 EntryList
              16: goto 24
              19: astore_2 // e -> slot 2 
              20: aload_1 // <- lock引用
              21: monitorexit // 将 lock对象 MarkWord 重置, 唤醒 EntryList
              22: aload_2 // <- slot 2 (e)
              23: athrow // throw e
              24: return
              Exception table:
              from to target type
              6    16  19    any
              19   22  19    any
              LineNumberTable:
              line 8: 0
              line 9: 6
              line 10: 14
              line 11: 24
              LocalVariableTable:
              Start Length Slot Name Signature
              0     25     0    args [Ljava/lang/String;
              StackMapTable: number_of_entries = 2
              frame_type = 255 /* full_frame */
              offset_delta = 19
              locals = [ class "[Ljava/lang/String;", class java/lang/Object ]
              stack = [ class java/lang/Throwable ]
              frame_type = 250 /* chop */
              offset_delta = 4
```

> 0：拿到lock的引用
>
> 4：将lock的引用存储到 slot1 中
>
> 5：将lock对象的 MarkWord 置为 Monitor 指针，原本存储的信息就存储到 Monitor 中
>
> 6-11：执行`counter++`操作
>
> 14：从 slot1 中获取lock对象的引用
>
> 15：将 lock 对象的 MarkWord  重置，原本MarkWord 存储的是hashcode、gc年龄等信息，当 lock 获取锁后，将MarkWord 置位 Monitor 指针。重置就是将这些信息重新写到 MarkWord 中，同时唤醒 EntryList
>
> 16：`goto 24` 执行24行，退出
>
> 
>
> 在Exception table中设置了监控异常的行数，如果6-16行有异常，就去执行19行
>
> 19：将异常信息 e 存储到slot2中
>
> 20：从 slot1 中获取lock对象的引用
>
> 21：将 lock对象 MarkWord 重置, 唤醒 EntryList
>
> 22：从 slot2 中获取异常信息
>
> 23：打印异常信息
>
> 
>
> **注意**
>
> - 通过异常 **try-catch 机制**，确保一定会被解锁
> - 方法级别的 synchronized 不会在字节码指令中有所体现







#### 5.5 轻量级锁 

如果一个对象虽然有多线程要加锁，但加锁的时间是错开的（也就是没有竞争），那么可以使用轻量级锁来优化。 

轻量级锁对使用者是透明的，即语法仍然是 `synchronized` 



如下，`method1`和`method2`方法都对`obj`对象加锁

```java
static final Object obj = new Object();

public static void method1() {
    synchronized( obj ) {
        // 同步块 A
        method2();
    }
}

public static void method2() {
    synchronized( obj ) {
        // 同步块 B
    }
}
```

1、创建 锁记录（Lock Record）对象，每个线程的**栈帧**都会包含一个锁记录的结构，内部可以存储锁定对象的Mark Word 

锁记录有两个属性

- 锁记录地址，同时用`00`标记表示轻量级锁
- `Object referenct`指向锁的对象

锁的对象`obj`中有对象头、对象体，对象头中`Mark Word`存储的是hashcode、gc年龄等信息

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152225410.png" style="zoom:80%;" />

2、让锁记录中 `Object reference` 指向锁对象，并尝试用 **cas** 替换 `Object` 的 `Mark Word`，将 `Mark Word `的值存入锁记录，然后将锁记录的信息存到`Object`的`Mark Word`中

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152227344.png" style="zoom:80%;" />

3、如果 cas 替换成功，对象头中存储了  **锁记录地址和状态 `00 `**，表示由该线程给对象加锁

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152231624.png" style="zoom:80%;" />

4、如果 cas 失败，有两种情况 

- 如果是其它线程已经持有了该 Object 的轻量级锁，这时表明有竞争，进入**锁膨胀**过程 
- 如果是自己执行了` synchronized `锁重入，那么再添加一条 `Lock Record` 作为重入的计数
  - 此时锁记录中`Object reference`指向`Object`，但是由于`Object`的`Mark World`位置已经是`00`轻量级锁状态，因此这条锁记录存储为null

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152232825.png" style="zoom:80%;" />

5、当退出` synchronized` 代码块（解锁时）如果有取值为 null 的锁记录，表示有重入，这时重置锁记录，表示重入计数减一

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152235598.png" style="zoom:80%;" />

6、当退出 `synchronized` 代码块（解锁时）锁记录的值不为 null，这时使用 cas 将 `Mark Word` 的值恢复给对象头 

- 成功，则解锁成功 

- 失败，说明轻量级锁进行了锁膨胀或已经升级为重量级锁，进入重量级锁解锁流程

  



#### 5.6 锁膨胀

锁膨胀：轻量级锁升级为重量级锁

如果在尝试加轻量级锁的过程中，CAS 操作无法成功，这时一种情况就是有其它线程为此对象加上了轻量级锁（有竞争），这时需要进行锁膨胀，将轻量级锁变为重量级锁



```java
static Object obj = new Object();
public static void method1() {
    synchronized( obj ) {
        // 同步块
    }
}
```

1、当 Thread-1 进行轻量级加锁时，Thread-0 已经对该对象加了轻量级锁

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152238829.png)

2、这时 Thread-1 加轻量级锁失败，进入**锁膨胀**流程 

- 为 Object 对象申请 `Monitor` 锁，让 `Object` 的`MARK WORLD`指向`Moniter`锁地址 
- 在堆区创建一个锁记录【`Lock Record`】对象，该对象包含了持有该锁的线程信息，然后`Object`的`MARK WORLD`也会记录这个对象的地址
- 然后 Thread-1进入 `Monitor` 的 `EntryList`，处于`BLOCKED`状态

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152242460.png)

3、当 Thread-0 退出同步块解锁时，使用 cas 将 `Mark Word `的值恢复给对象头，会失败。这时会进入重量级解锁流程，即按照 Monitor 地址找到 Monitor 对象，设置 Owner 为 null，唤醒 EntryList 中 BLOCKED 线程





#### 5.7 自旋优化 

重量级锁竞争的时候，还可以使用自旋(循环尝试获取重量级锁)来进行优化，如果当前线程自旋成功（即这时候持锁线程已经退出了同步块，释放了锁），这时当前线程就可以避免阻塞。 (进入阻塞再恢复，会发生上下文切换，比较耗费性能)



**自旋重试成功的情况**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152304904.png)

**自旋重试失败的情况**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152305008.png)



- 自旋会占用 CPU 时间，单核 CPU 自旋就是浪费，多核 CPU 自旋才能发挥优势。 
- 在 Java 6 之后自旋锁是自适应的，比如对象刚刚的一次自旋操作成功过，那么认为这次自旋成功的可能性会高，就多自旋几次；反之，就少自旋甚至不自旋，总之，比较智能。 
- Java 7 之后不能控制是否开启自旋功能





#### 5.8 偏向锁

- 轻量级锁在没有竞争时（就自己这个线程），每次重入仍然需要执行 CAS 操作。 

- Java 6 中引入了**偏向锁**来做进一步优化：只有第一次使用 CAS 将线程 ID 设置到对象的 Mark Word 头，之后发现这个线程 ID 是自己的就表示没有竞争，不用重新 CAS。以后只要不发生竞争，这个对象就归该线程所有 

> 这里的线程id是操作系统赋予的id 和 Thread的id是不同的



**例**

```java
static final Object obj = new Object();
public static void m1() {
    synchronized( obj ) {
        // 同步块 A
        m2();
    }
}
public static void m2() {
    synchronized( obj ) {
        // 同步块 B
        m3();
    }
}
public static void m3() {
    synchronized( obj ) {
        // 同步块 C
    }
}
```



没有开启偏向锁，会使用轻量级锁 ，每次重入都会执行CAS操作                               开启偏向锁，每次锁重入仅判断当前`ThreadID`是否是自己

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152343106.png" style="zoom:80%;" /><img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152344617.png" style="zoom:80%;" />





**对象头格式**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401152345887.png)

一个对象创建时： 

- 如果开启了偏向锁（默认开启），那么对象创建后，`markword` 值为` 0x05` 即最后 3 位为 101，这时它的 thread、epoch、age 都为 0，不保存hashcode信息 
- 偏向锁默认是延迟的，不会在程序启动时立即生效，如果想避免延迟，可以加 VM 参数 `-XX:BiasedLockingStartupDelay=0` 来禁用延迟
- 如果没有开启偏向锁，那么对象创建后，`markword` 值为 0x01 即最后 3 位为 001，这时它的 hashcode、age 都为 0，第一次用到 hashcode 时才会赋值





**测试**

利用 jol 第三方工具来查看对象头信息

```java
public static void main(String[] args) throws IOException {
    Dog d = new Dog();
    ClassLayout classLayout = ClassLayout.parseInstance(d);
    
    new Thread(() -> {
        log.debug("synchronized 前");
        System.out.println(classLayout.toPrintableSimple(true));
        synchronized (d) {
            log.debug("synchronized 中");
            System.out.println(classLayout.toPrintableSimple(true));
        }
        log.debug("synchronized 后");
        System.out.println(classLayout.toPrintableSimple(true));
    }, "t1").start();
}
```

```
11:08:58.117 c.TestBiased [t1] - synchronized 前
00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101 
11:08:58.121 c.TestBiased [t1] - synchronized 中
00000000 00000000 00000000 00000000 00011111 11101011 11010000 00000101 
11:08:58.121 c.TestBiased [t1] - synchronized 后
00000000 00000000 00000000 00000000 00011111 11101011 11010000 00000101
```

**注意**：处于偏向锁的对象解锁后，线程 id 仍存储于对象头中，也就是偏(心)向某个线程了





**禁用偏向锁**

禁用偏向锁后，创建对象后，最后3位是001，无锁状态。加锁后，变为000，轻量级锁，同时保存了锁记录地址。释放锁后，变回001无锁状态，同时清除锁记录地址

```
11:13:10.018 c.TestBiased [t1] - synchronized 前
00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
11:13:10.021 c.TestBiased [t1] - synchronized 中
00000000 00000000 00000000 00000000 00100000 00010100 11110011 10001000 
11:13:10.021 c.TestBiased [t1] - synchronized 后
00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001
```







#### 5.9 偏向锁的撤销

**1、hashcode**

在`Dog d = new Dog();` 后加上一句 `d.hashCode();`

- 正常状态对象一开始是没有 hashCode 的，第一次调用才生成
- 调用了 `hashCode() `后会撤销该对象的偏向锁

```
11:22:10.386 c.TestBiased [main] - 调用 hashCode:1778535015 
11:22:10.391 c.TestBiased [t1] - synchronized 前
00000000 00000000 00000000 01101010 00000010 01001010 01100111 00000001 
11:22:10.393 c.TestBiased [t1] - synchronized 中
00000000 00000000 00000000 00000000 00100000 11000011 11110011 01101000 
11:22:10.393 c.TestBiased [t1] - synchronized 后
00000000 00000000 00000000 01101010 00000010 01001010 01100111 00000001
```

因为调用了`hashcode()`，但是默认是偏向锁，存储的是线程id，没有内存去存储`hashcode`，因此会撤销偏向锁，用来存储`hashcode`值

- 轻量级锁会在锁记录中记录 hashCode 
- 重量级锁会在 Monitor 中记录 hashCode 





**2、其它线程使用对象**

当有其它线程使用偏向锁对象时【没有发生锁竞争】，会将偏向锁升级为轻量级锁

```java
private static void test2() throws InterruptedException {
    
    Dog d = new Dog();
    
    Thread t1 = new Thread(() -> {
        
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        synchronized (d) {
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        
        synchronized (TestBiased.class) {
            TestBiased.class.notify();
        }
    }, "t1");
    t1.start();
    
    Thread t2 = new Thread(() -> {
        synchronized (TestBiased.class) {
            try {
                TestBiased.class.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        synchronized (d) {
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        
    }, "t2");
    t2.start();
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401160022929.png)





**3、调用 wait/notify**

重量级锁才支持 `wait/notify`，调用后，锁直接升级为重量级锁

```java
public static void main(String[] args) throws InterruptedException {
    Dog d = new Dog();
    
    Thread t1 = new Thread(() -> {
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        synchronized (d) {
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
            try {
                d.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
    }, "t1");
    t1.start();
    
    new Thread(() -> {
        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        synchronized (d) {
            log.debug("notify");
            d.notify();
        }
    }, "t2").start();
}
```

```java
[t1] - 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101 
[t1] - 00000000 00000000 00000000 00000000 00011111 10110011 11111000 00000101 
[t2] - notify 
[t1] - 00000000 00000000 00000000 00000000 00011100 11010100 00001101 11001010
```



**总结**

- 默认情况下，偏向锁是开启的，即这个锁归这个对象所拥有

- 如果有其他线程获取锁或者调用hashcode，那么升级为轻量级锁
- 如果发生锁竞争或者调用`wait/notify`，那么升级为重量级锁







#### 5.10 批量重偏向、撤销

- 对象虽然被多个线程访问，但没有竞争，这时偏向了线程 T1 的对象仍有机会重新偏向 T2，重偏向会重置对象的 Thread ID 。当（某类型对象）撤销偏向锁超过阈值 20 次后，jvm 会在给（所有这种类型的状态为偏向锁的）对象加锁时重新偏向至新的加锁线程
- 当撤销偏向锁阈值超过 40 次后，jvm 会将整个类的所有对象都会变为不可偏向的，新建的该类型对象也是不可偏向的
  - 例如：当前有40个锁对象，刚开始都偏向t1线程。现在t2线程获取这40个锁对象，1-19个锁对象会撤销偏向锁，第20个锁对象往后，会撤销t1的偏向锁，将偏向锁设置为t2【**达到20阈值**】。然后t3线程获取这40个锁对象，由于前19个锁对象已经是非偏向锁了，从第20个开始，又会撤销偏向锁，最后撤销次数达到40阈值后，会将所有的锁变为不可偏向的，即使新创建的对象也是不可偏向的。



**演示批量重偏向**

```java
private static void test3() throws InterruptedException {
    
    Vector<Dog> list = new Vector<>();
    
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 30; i++) {
            Dog d = new Dog();
            list.add(d);
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
        }
        synchronized (list) {
            list.notify();
        }
    }, "t1");
    t1.start();

    Thread t2 = new Thread(() -> {
        synchronized (list) {
            try {
                list.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        log.debug("===============> ");
        for (int i = 0; i < 30; i++) {
            Dog d = list.get(i);
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
    }, "t2");
    t2.start();
}
```

```java
[t1] - 0 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 1 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
...
[t1] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - ===============> 
[t2] - 0 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101  // 原始轻量级锁偏向t1
[t2] - 0 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000  // t2获取锁，将锁升级为轻量级锁
[t2] - 0 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001  // 释放锁后，偏向锁被撤销
...
[t2] - 18 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 18 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 18 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 // 第20次，初始轻量级锁偏向t1
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 // 第20次撤销锁，达到阈值，jvm将后边所有锁偏向t2
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
...
[t2] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101
```









#### 5.11 锁消除

锁消除 ：`JIT`即时编译器会对字节码做进一步优化，下边代码中`o`是一个局部变量，不会共享，所以编译后，不会执行加锁操作，而是直接执行`x++`

```java
public class MyBenchmark {
    static int x = 0;
    public void b() throws Exception {
        //这里的o是局部变量,不会被共享,JIT做热点代码优化时会做锁消除
        Object o = new Object();
        synchronized (o) {
            x++;
        }
    }
}
```







### 6、wait/notify

#### 6.1 原理介绍

当一个线程获取锁后，`Moniter`的`Owner`指向的就是这个线程。当发现条件不满足，这个线程可以调用`obj.wait()`方法，进入`Moniter`的`WaitSet`中进行等待，同时释放锁，直到获取该锁的其他线程调用`obj.notify()`或者`obj.notifyAll()`来唤醒当前线程，当前线程会进入`EntryList`进行锁竞争

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401162148055.png)

- Owner 线程发现条件不满足，调用 wait 方法，即可进入 WaitSet 变为 WAITING 状态 
- BLOCKED 和 WAITING 的线程都处于阻塞状态，不占用 CPU 时间片 
- BLOCKED 线程会在 Owner 线程释放锁时唤醒 
- WAITING 线程会在 Owner 线程调用 notify 或 notifyAll 时唤醒，但唤醒后并不意味者立刻获得锁，仍需进入EntryList 重新竞争





**相关API**

- `obj.wait()` 让进入 object 监视器的线程到 waitSet 等待 
- `wait(long n)` 有时限的等待, 到 n 毫秒后结束等待，或是被 notify
- `obj.notify()` 在 object 上正在 waitSet 等待的线程中挑一个唤醒 
- `obj.notifyAll()` 让 object 上正在 waitSet 等待的线程全部唤醒

它们都是线程之间进行协作的手段，都属于 Object 对象的方法。**必须获得此对象的锁，才能调用这几个方法**

```java
final static Object obj = new Object();

public static void main(String[] args) {
    new Thread(() -> {
        synchronized (obj) {
            log.debug("执行....");
            try {
                obj.wait(); // 让线程在obj上一直等待下去
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug("其它代码....");
        }
    }).start();
    
    new Thread(() -> {
        synchronized (obj) {
            log.debug("执行....");
            try {
                obj.wait(); // 让线程在obj上一直等待下去
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug("其它代码....");
        }
    }).start();
    
    // 主线程两秒后执行
    sleep(2);
    log.debug("唤醒 obj 上其它线程");
    synchronized (obj) {
        obj.notify(); // 唤醒obj上任意一个线程
        // obj.notifyAll(); // 唤醒obj上所有等待线程
    }
}
```



**notify**

```java
20:00:53.096 [Thread-0] c.TestWaitNotify - 执行.... 
20:00:53.099 [Thread-1] c.TestWaitNotify - 执行.... 
20:00:55.096 [main] c.TestWaitNotify - 唤醒 obj 上其它线程
20:00:55.096 [Thread-0] c.TestWaitNotify - 其它代码....
```



**notifyAll**

```java
19:58:15.457 [Thread-0] c.TestWaitNotify - 执行.... 
19:58:15.460 [Thread-1] c.TestWaitNotify - 执行.... 
19:58:17.456 [main] c.TestWaitNotify - 唤醒 obj 上其它线程
19:58:17.456 [Thread-1] c.TestWaitNotify - 其它代码.... 
19:58:17.456 [Thread-0] c.TestWaitNotify - 其它代码....
```







#### 6.2 wait和sleep

`sleep(long n)` 和 `wait(long n)` 的区别

- `sleep` 是 Thread 方法，而 `wait` 是 Object 的方法 
- `sleep` 不需要强制和 `synchronized` 配合使用，但 `wait` 需要和 `synchronized` 一起用，因为只有获取锁的线程才可以调用`wait`方法
- `sleep` 在睡眠的同时，不会释放对象锁，但 `wait` 在等待的时候会释放对象锁 
- 它们的状态都是 `TIMED_WAITING`







#### 6.3 wait正确使用

1、当线程1获取锁之后，发现条件不成立，调用`wait()`方法，释放锁并等待。

2、线程2获取锁后，发现条件不成立，也调用`wait()`方法，释放锁并等待。

3、线程3获取锁后，修改了条件，假设使线程1条件成立，然后线程3调用`notifyAll()`方法，将线程1和线程2都唤醒

4、此时线程1发现条件满足，继续执行，而线程2条件不满足，继续调用`wait()`方法等待。

5、如果使用的是`if`而不是`while`，那么线程2被唤醒后，发现条件不满足就结束了。

```java
synchronized(lock) {
    while(条件不成立) {
        lock.wait();
    }
    // 干活
}

//另一个线程
synchronized(lock) {
    lock.notifyAll();
}
```







#### 6.4 设计模式-保护性暂停

`Guarded Suspension`，用在一个线程等待另一个线程的执行结果 

要点 

- 有一个结果需要从一个线程传递到另一个线程，让他们关联同一个` GuardedObject `，是一对一的
- 如果有结果不断从一个线程到另一个线程那么可以使用消息队列
- JDK 中，join 的实现、Future 的实现，采用的就是此模式 
- 因为要等待另一方的结果，因此归类到同步模式 



**测试**

这里如果用`join`的话，必须等这个线程执行完所有任务结束后，当前线程才可以执行，假设我们只是要获取下载结果，而下载的线程在下载完之后还执行了其他操作，那当前线程就要一直等下去。而如果使用的是`wait`，当下载线程下载完成后，调用`nodify`唤醒当前线程，当前线程就可以处理结果，而下载线程可以继续执行其他任务。

```java
public static void main(String[] args) {
    GuardedObject guardedObject = new GuardedObject();
    
    new Thread(() -> {
        try {
            // 子线程执行下载
            List<String> response = download();
            log.debug("download complete...");
            guardedObject.complete(response);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }).start();
    
    log.debug("waiting...");
    
    // 主线程阻塞等待
    Object response = guardedObject.get();
    log.debug("get response: [{}] lines", ((List<String>) response).size());
}

class GuardedObject {
    
    private Object response;
    private final Object lock = new Object();
    
    public Object get() {
        synchronized (lock) {
            // 条件不满足则等待
            while (response == null) {
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } 
            }
            return response; 
        }
    }
    
    public void complete(Object response) {
        synchronized (lock) {
            // 条件满足，通知等待线程
            this.response = response;
            lock.notifyAll();
        }
    }
    
}
```







#### 6.5 join原理

**join方法源码**

调用`t1.join()`或者`t1.join(2000)`

```java
public final void join() throws InterruptedException {
    join(0);
}

public final synchronized void join(long millis) throws InterruptedException {
    // 记录当前时间
    long base = System.currentTimeMillis();
    long now = 0;

    if (millis < 0) {
        throw new IllegalArgumentException("timeout value is negative");
    }
	
    // 如果millis为0，表示一直等到t1线程结束
    if (millis == 0) {
        while (isAlive()) {
            // wait方法是Object类所拥有的，是由当前线程所拥有的对象调用的（即当前线程的锁对象）
            wait(0);
        }
    } else { // 如果millis不为0
        while (isAlive()) {
            // 计算剩余等待时间，避免被虚假唤醒，如果直接使用wait(millis)，假设等待2s，在第1s时被唤醒了，条件成立，会继续等待2s，就多等待了1s
            long delay = millis - now;
            // 剩余等待时间小于0，直接退出
            if (delay <= 0) {
                break;
            }
            // 等待delay的时间
            wait(delay);
            // 计算已经等待的时间
            now = System.currentTimeMillis() - base;
        }
    }
}
```



`join`只释放调用对象的对象锁

> 在主线程中，调用了`mythread.join();`会等待mythread线程执行完毕，主线程才继续执行。`join()`方法是被`synchronized`修饰的，在主线程中调用`mythread.join()`主线程会获得`mythread`的对象锁，然后调用`wait()`方法后，释放的也是`mythread`的对象锁

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







#### 6.6 生产者/消费者模式

- 与前面的保护性暂停中的 GuardObject 不同，不需要产生结果和消费结果的线程一一对应 
- 消费队列可以用来平衡生产和消费的线程资源 
- 生产者仅负责产生结果数据，不关心数据该如何处理，而消费者专心处理结果数据 
- 消息队列是有容量限制的，满时不会再加入数据【生产者wait】，空时不会再消耗数据 【消费者wait】，生产者生产和消费者消费时都会调用`notifyAll`
- JDK 中各种阻塞队列，采用的就是这种模式

- 异步模式，因为消息产生了，可能不会立即被消费

![image.png](https://cdn.nlark.com/yuque/0/2022/png/393192/1649432391569-d4b5bbfb-cff5-4aa4-9b27-58c860bd1855.png)







### 7、Park/UnPark

#### 7.1 基本使用

`Park`和`UnPark`是`LockSupport` 类中的方法

- `LockSupport.park(); `   暂停当前线程
- `LockSupport.unpark(暂停线程对象)`   恢复某个线程的运行



**与 Object 的 wait & notify 相比** 

- wait，notify 和 notifyAll 必须配合 Object Monitor 一起使用，而 park，unpark 不需要
- park & unpark 是以线程为单位来【阻塞】和【唤醒】线程，而 notify 只能随机唤醒一个等待线程，notifyAll是唤醒所有等待线程，就不那么【精确】 
- park & unpark 可以先 unpark，而 wait & notify 不能先 notify 



**先park再unpark**

```java
Thread t1 = new Thread(() -> {
    log.debug("start...");
    sleep(1);
    log.debug("park...");
    LockSupport.park();
    log.debug("resume...");
},"t1");
t1.start();

sleep(2);
log.debug("unpark...");
LockSupport.unpark(t1);
```

**输出**

```
18:42:52.585 c.TestParkUnpark [t1] - start... 
18:42:53.589 c.TestParkUnpark [t1] - park... 
18:42:54.583 c.TestParkUnpark [main] - unpark... 
18:42:54.583 c.TestParkUnpark [t1] - resume...
```



**先 unpark 再 park**

```java
Thread t1 = new Thread(() -> {
    log.debug("start...");
    sleep(2);
    log.debug("park...");
    LockSupport.park();
    log.debug("resume...");
}, "t1");
t1.start();

sleep(1);
log.debug("unpark...");
LockSupport.unpark(t1);
```

**输出**

```
18:43:50.765 c.TestParkUnpark [t1] - start... 
18:43:51.764 c.TestParkUnpark [main] - unpark... 
18:43:52.769 c.TestParkUnpark [t1] - park... 
18:43:52.769 c.TestParkUnpark [t1] - resume...
```





#### 7.2 原理

每个线程都有自己的一个`Parker`【C代码实现的】 对象，由三部分组成 `_counter` ， `_cond` 和`_mutex` 

`LockSupport.park()`底层就是`Unsafe.park()` 方法，是一个native方法

`LockSupport.unpark(t1)`底层是`Unsafe.unpark(t1)`，是一个native方法



**先park再unpark**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172214504.png" style="zoom:80%;" />

- 当前线程调用 `Unsafe.park()` 方法 

- 检查` _counter` ，当前为 0，这时，获得` _mutex` 互斥锁 

- 线程进入 `_cond` 条件变量阻塞 

- 设置 `_counter = 0`

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172216226.png" style="zoom:80%;" />

- 调用 `Unsafe.unpark(Thread_0)` 方法，设置 `_counter` 为 1 

- 唤醒 `_cond `条件变量中的 Thread_0 

- Thread_0 恢复运行 

- 设置 `_counter` 为 0





**先unpark在park**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172217085.png" style="zoom:80%;" />

- 调用 `Unsafe.unpark(Thread_0) `方法，设置 `_counter` 为 1 

- 当前线程调用 `Unsafe.park()` 方法 

- 检查` _counter` ，当前为 1，这时线程无需阻塞，继续运行 

- 设置` _counter `为 0 







### 8、线程状态转换

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172244497.png" style="zoom:80%;" />



**1、NEW -->  RUNNABLE**

当调用` t.start() `方法时，由 NEW --> RUNNABLE



**2、RUNNABLE <--> WAITING**

线程t用 `synchronized(obj)` 获取了对象锁后 

- 调用`obj.wait() `方法时，t 线程从 RUNNABLE --> WAITING 
- 调用` obj.notify() `， `obj.notifyAll() `， `t.interrupt()` 时 
  - 竞争锁成功，t 线程从WAITING --> RUNNABLE 
  - 竞争锁失败，t 线程从WAITING --> BLOCKED




**3、RUNNABLE <--> WAITING**

- 当前线程调用` t.join() `方法时，当前线程从 RUNNABLE --> WAITING 
  - 注意：当前线程是在t 线程对象的监视器上等待 

- t 线程运行结束，或调用了当前线程的 `interrupt() `时，当前线程从 WAITING --> RUNNABLE



**4、RUNNABLE <--> WAITING**

- 当前线程调用 `LockSupport.park() `方法会让当前线程从 RUNNABLE --> WAITING 
- 调用 `LockSupport.unpark(目标线程)` 或调用了线程 的 `interrupt()` ，会让目标线程从 WAITING -->RUNNABLE 



**5、RUNNABLE <--> TIMED_WAITING**

t 线程用 `synchronized(obj) `获取了对象锁后 

- 调用 `obj.wait(long n) `方法时，t 线程从 RUNNABLE --> TIMED_WAITING 
- t 线程等待时间超过了 n 毫秒，或调用 `obj.notify() `， `obj.notifyAll()` ， `t.interrupt()` 时 
  - 竞争锁成功，t 线程从TIMED_WAITING --> RUNNABLE 
  - 竞争锁失败，t 线程从TIMED_WAITING --> BLOCKED




**6、RUNNABLE <--> TIMED_WAITING**

- 当前线程调用` t.join(long n) `方法时，当前线程从 RUNNABLE --> TIMED_WAITING 
  - 注意：当前线程在t 线程对象的监视器上等待 

- 当前线程等待时间超过了 n 毫秒，或t 线程运行结束，或调用了当前线程的` interrupt()` 时，当前线程从 TIMED_WAITING --> RUNNABLE



**7、RUNNABLE <--> TIMED_WAITING**

- 当前线程调用 `Thread.sleep(long n)` ，当前线程从 RUNNABLE --> TIMED_WAITING 
- 当前线程等待时间超过了 n 毫秒，当前线程从TIMED_WAITING --> RUNNABLE



**8、RUNNABLE <--> TIMED_WAITING**

- 当前线程调用 `LockSupport.parkNanos(long nanos)` 或` LockSupport.parkUntil(long millis)` 时，当前线程从 RUNNABLE --> TIMED_WAITING 
- 调用 `LockSupport.unpark(目标线程) `或调用了线程 的 `interrupt() `，或是等待超时，会让目标线程从 TIMED_WAITING--> RUNNABLE



**9、RUNNABLE <--> BLOCKED**

- t 线程用`synchronized(obj)` 获取对象锁时如果竞争失败，从RUNNABLE --> BLOCKED 
- 持 obj 锁线程的同步代码块执行完毕，会唤醒该对象上所有 BLOCKED的线程重新竞争，如果其中 t 线程 竞争 成功，从 BLOCKED --> RUNNABLE ，其它失败的线程仍然BLOCKED 



**10、RUNNABLE --> TERMINATED **

当前线程所有代码运行完毕，进入 TERMINATED 







### 9、死锁

- `t1 线程` 获得 `A对象` 锁，接下来想获取 `B对象` 的锁

- `t2 线程` 获得 `B对象` 锁，接下来想获取 `A对象` 的锁

此时就会发送死锁



**死锁检测**

1、使用`jps`定位进程id，再用`jstack`定位死锁

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172321715.png)



输入`jstack 8536`通过打印信息可以得知，发生死锁

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172322204.png)



2、使用` jconsole`检测

打开`jconsole`后，选择线程，点击检测死锁，就可以看到当前死锁线程

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401172324277.png)









**活锁**

两个线程互相改变对方的结束条件，最后谁也无法结束

```java
public class TestLiveLock {
    static volatile int count = 10;
    static final Object lock = new Object();
    
    public static void main(String[] args) {
        new Thread(() -> {
            // 期望减到 0 退出循环
            while (count > 0) {
                sleep(0.2);
                count--;
                log.debug("count: {}", count);
            }
        }, "t1").start();
        
        new Thread(() -> {
            // 期望超过 20 退出循环
            while (count < 20) {
                sleep(0.2);
                count++;
                log.debug("count: {}", count);
            }
        }, "t2").start();
        
    }
}
```



**饥饿**

一个线程由于优先级太低，始终得不到 CPU 调度执行，也不能够结束







### 10、ReentrantLock

与 `synchronized` 相比`ReentrantLock`具备如下特点 

- 可中断：假设t1线程通过`lock.lockInterruptibly()`方法等待锁，可以通过`t1.interrupt();`打断
- 可以设置超时时间 
- 可以设置为公平锁【但是公平锁会降低并发度】
- 支持多个条件变量：`synchronized`中，调用`wait`方法后线程进入`waitSet`中等待，只有这一个`waitSet`。而`ReentrantLock`中有多个`waitSet`，可以通过不同条件进入不同的`waitSet`

- 与 `synchronized` 一样，都是可重入锁 





#### 10.1 可打断

通过`lock.lockInterruptibly()`方法获取锁，可以被`interrupt()`方法打断

```java
ReentrantLock lock = new ReentrantLock();

Thread t1 = new Thread(() -> {
    log.debug("启动...");
    
    try {
        //没有竞争就会获取锁
        //有竞争就进入阻塞队列等待,但可以被打断
        lock.lockInterruptibly();
        //lock.lock(); //不可打断
    } catch (InterruptedException e) {
        e.printStackTrace();
        log.debug("等锁的过程中被打断");
        return;
    }
    
    try {
        log.debug("获得了锁");
    } finally {
        lock.unlock();
    }
}, "t1");

lock.lock();
log.debug("获得了锁");
t1.start();

try {
    sleep(1);
    log.debug("执行打断");
    t1.interrupt();
} finally {
    lock.unlock();
}
```





#### 10.2 锁超时

- `lock.tryLock()`：立刻返回结果，只获取一次锁，如果获取锁成功返回true，否则返回false。而`lock`方法如果获取锁失败，会加入队列等待

  ```java
  ReentrantLock lock = new ReentrantLock();
  
  Thread t1 = new Thread(() -> {
      log.debug("启动...");
      if (!lock.tryLock()) {
          log.debug("获取锁失败，返回");
          return;
      }
      try {
          log.debug("获得了锁");
      } finally {
          lock.unlock();
      }
  }, "t1");
  
  lock.lock();
  log.debug("获得了锁");
  t1.start();
  
  try {
      sleep(2);
  } finally {
      lock.unlock();
  }
  
  18:15:02.918 [main] c.TestTimeout - 获得了锁
  18:15:02.921 [t1] c.TestTimeout - 启动... 
  18:15:02.921 [t1] c.TestTimeout - 获取锁失败，返回
  ```

- `lock.tryLock(long timeout, TimeUnit unit)`：在`timeout`时间内获取锁成功返回true，否则返回false

  ```java
  ReentrantLock lock = new ReentrantLock();
  
  Thread t1 = new Thread(() -> {
      log.debug("启动...");
      
      try {
          if (!lock.tryLock(1, TimeUnit.SECONDS)) {
              log.debug("获取等待 1s 后失败，返回");
              return;
          }
      } catch (InterruptedException e) {
          e.printStackTrace();
      }
      try {
          log.debug("获得了锁");
      } finally {
          lock.unlock();
      }
  }, "t1");
  
  lock.lock();
  log.debug("获得了锁");
  t1.start();
  
  try {
      sleep(2);
  } finally {
      lock.unlock();
  }
  
  18:19:40.537 [main] c.TestTimeout - 获得了锁
  18:19:40.544 [t1] c.TestTimeout - 启动... 
  18:19:41.547 [t1] c.TestTimeout - 获取等待 1s 后失败，返回
  ```

  



#### 10.3 多个条件变量

- `synchronized` 中的条件变量，就是`waitSet` ，当条件不满足调用`wait()`方法后，该线程就会进入 `waitSet` 等待 

- `ReentrantLock`支持多个条件变量，当条件不满足时，可以指定该线程去哪个条件变量中等待，同时唤醒操作也可以指定唤醒哪个条件变量



使用要点： 

- `await` 前需要获得锁 
- `await` 执行后，线程会释放锁，进入 `conditionObject `等待 
- `await` 的线程被唤醒（或打断、或超时）去重新竞争 lock 锁 
- 竞争 lock 锁成功后，从 await 后继续执行 



```java
static ReentrantLock lock = new ReentrantLock();

static Condition waitCigaretteQueue = lock.newCondition();
static Condition waitbreakfastQueue = lock.newCondition();

static volatile boolean hasCigrette = false;
static volatile boolean hasBreakfast = false;

public static void main(String[] args) {
    
    new Thread(() -> {
        try {
            lock.lock();
            while (!hasCigrette) {
                try {
                    waitCigaretteQueue.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            log.debug("等到了它的烟");
        } finally {
            lock.unlock();
        }
    }).start();
    
    new Thread(() -> {
        try {
            lock.lock();
            while (!hasBreakfast) {
                try {
                    waitbreakfastQueue.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            log.debug("等到了它的早餐");
        } finally {
            lock.unlock();
        }
    }).start();
    
    sleep(1);
    sendBreakfast();
    sleep(1);
    sendCigarette();
}

private static void sendCigarette() {
    lock.lock();
    try {
        log.debug("送烟来了");
        hasCigrette = true;
        waitCigaretteQueue.signal();
    } finally {
        lock.unlock();
    }
}

private static void sendBreakfast() {
    lock.lock();
    try {
        log.debug("送早餐来了");
        hasBreakfast = true;
        waitbreakfastQueue.signal();
    } finally {
        lock.unlock();
    }
}
```

```java
18:52:27.680 [main] c.TestCondition - 送早餐来了
18:52:27.682 [Thread-1] c.TestCondition - 等到了它的早餐
18:52:28.683 [main] c.TestCondition - 送烟来了
18:52:28.683 [Thread-0] c.TestCondition - 等到了它的烟
```







### 11、同步模式--顺序控制

#### 11.1 固定运行顺序

需求：必须先打印2后打印1



**wait notify实现**

- 需要保证先 wait 再 notify，否则 wait 线程永远得不到唤醒。因此使用了『运行标记』来判断该不该wait 
- 如果有些干扰线程错误地 notify 了 wait 线程，条件不满足时还要重新等待，使用了 while 循环来解决此问题 
- 唤醒对象上的 wait 线程需要使用 notifyAll，因为『同步对象』上的等待线程可能不止一个 

```java
// 用来同步的对象
static Object obj = new Object();
// t2 运行标记， 代表 t2 是否执行过
static boolean t2runed = false;

public static void main(String[] args) {
    
    Thread t1 = new Thread(() -> {
        synchronized (obj) {
            // 如果 t2 没有执行过
            while (!t2runed) { 
                try {
                    // t1 先等一会
                    obj.wait(); 
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println(1);
    });
    
    Thread t2 = new Thread(() -> {
        System.out.println(2);
        synchronized (obj) {
            // 修改运行标记
            t2runed = true;
            // 通知 obj 上等待的线程（可能有多个，因此需要用 notifyAll）
            obj.notifyAll();
        }
    });
    
    t1.start();
    t2.start();
}
```



**Park Unpark实现**

- park 和 unpark 方法比较灵活，他俩谁先调用，谁后调用无所谓。

- 以线程为单位进行『暂停』和『恢复』,不需要『同步对象』和『运行标记』

```java
Thread t1 = new Thread(() -> {
    try { 
        Thread.sleep(1000); 
    } catch (InterruptedException e) { 
    }
    // 当没有『许可』时，当前线程暂停运行；有『许可』时，用掉这个『许可』，当前线程恢复运行
    LockSupport.park();
    System.out.println("1");
});

Thread t2 = new Thread(() -> {
    System.out.println("2");
    // 给线程 t1 发放『许可』（多次连续调用 unpark 只会发放一个『许可』）
    LockSupport.unpark(t1);
});

t1.start();
t2.start();
```





#### 11.2 交替输出

需求：线程 1 输出 a 5 次，线程 2 输出 b 5 次，线程 3 输出 c 5 次。现在要求输出 abcabcabcabcabc 



**wait notify实现**

```java
class SyncWaitNotify {
    private int flag;
    private int loopNumber;
    
    public SyncWaitNotify(int flag, int loopNumber) {
        this.flag = flag;
        this.loopNumber = loopNumber;
    }
    
    public void print(int waitFlag, int nextFlag, String str) {
        for (int i = 0; i < loopNumber; i++) {
            synchronized (this) {
                while (this.flag != waitFlag) {
                    try {
                        this.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                
                System.out.print(str);
                flag = nextFlag;
                this.notifyAll();
            }
        }
    }
}
```

```java
SyncWaitNotify syncWaitNotify = new SyncWaitNotify(1, 5);

new Thread(() -> {
    syncWaitNotify.print(1, 2, "a");
}).start();

new Thread(() -> {
    syncWaitNotify.print(2, 3, "b");
}).start();

new Thread(() -> {
    syncWaitNotify.print(3, 1, "c");
}).start();
```





**lock条件变量**

```java
class AwaitSignal extends ReentrantLock{
    private int loopNumber;

    public AwaitSignal(int loopNumber) {
        this.loopNumber = loopNumber;
    }

    // 参数1 打印内容， 参数2 进入哪一间休息室, 参数3 下一间休息室
    public void print(String str, Condition current, Condition next) {
        for (int i = 0; i < loopNumber; i++) {
            lock();
            try {
                current.await();
                System.out.print(str);
                next.signal();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                unlock();
            }
        }
    }
}
```

```java
public static void main(String[] args) throws InterruptedException {
    
    AwaitSignal awaitSignal = new AwaitSignal(5);
    Condition a = awaitSignal.newCondition();
    Condition b = awaitSignal.newCondition();
    Condition c = awaitSignal.newCondition();
    
    new Thread(() -> {
        awaitSignal.print("a", a, b);
    }).start();
    
    new Thread(() -> {
        awaitSignal.print("b", b, c);
    }).start();
    
    new Thread(() -> {
        awaitSignal.print("c", c, a);
    }).start();
    
    Thread.sleep(1000);
    
    awaitSignal.lock();
    try {
        System.out.println("开始...");
        a.signal();
    } finally {
        awaitSignal.unlock();
    }
    
}
```





**Park Unpark实现**

```java
public class JiaoTiShuChuParkUnpark {

    static Thread t1;
    static Thread t2;
    static Thread t3;

    public static void main(String[] args) {
        ParkUnpark pu = new ParkUnpark(5);

        t1 = new Thread(() -> {
            pu.print("a", t2);
        });
        t2 = new Thread(() -> {
            pu.print("b", t3);
        });
        t3 = new Thread(() -> {
            pu.print("c", t1);
        });
        t1.start();
        t2.start();
        t3.start();

        LockSupport.unpark(t1);
    }
}

class ParkUnpark {

    private int loopNumber;

    public ParkUnpark(int loopNumber) {
        this.loopNumber = loopNumber;
    }

    public void print(String str, Thread next) {
        for (int i = 0; i < loopNumber; i++) {
            LockSupport.park();
            System.out.print(str);
            LockSupport.unpark(next);
        }
    }

}
```









## 三、共享模型-内存

### 1、Java内存模型

JMM 即 Java Memory Model，它定义了**主存**、**工作内存**抽象概念，底层对应着 CPU 寄存器、缓存、硬件内存、CPU 指令优化等。 



JMM 体现在以下几个方面 

- 原子性 - 保证指令不会受到线程上下文切换的影响 
- 可见性 - 保证指令不会受 cpu 缓存的影响 
- 有序性 - 保证指令不会受 cpu 指令并行优化的影响





### 2、可见性

**退不出的循环**

> 以下代码中，线程 t  执行while循环，直到`run`为false，主线程中，将`run`置为false，但是循环并未终止

```java
static boolean run = true;

public static void main(String[] args) throws InterruptedException {

    Thread t = new Thread(()->{
        while(run){
            // ....
        }
    });
    t.start();

    Thread.sleep(1);
    run = false; // 线程t不会如预想的停下来
}
```





**分析**

1、初始状态， t 线程刚开始从主内存读取了 run 的值到工作内存。

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401202315085.png" style="zoom: 60%;" />  

2、因为 t 线程要频繁从主内存中读取 run 的值，JIT 编译器会将 run 的值缓存至自己工作内存中的高速缓存中，减少对主存中 run 的访问，提高效率

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401202315631.png" style="zoom:70%;" /> 

3、1 秒之后，main 线程修改了 run 的值，并同步至主存，而 t 是从自己工作内存中的高速缓存中读取这个变量的值，结果永远是旧值

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401202315712.png" style="zoom:70%;" /> 





**解决方法**

1、使用`volatile`关键字修饰变量：它可以用来修饰**成员变量**和**静态成员变量**，他可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取它的值，线程操作 volatile 变量都是直接操作主存

```java
volatile static boolean run = true; // 使用volatile修饰后，线程每次读取run的值都是去主存读取

public static void main(String[] args) throws InterruptedException {

    Thread t = new Thread(()->{
        while(run){
            // ....
        }
    });
    t.start();

    Thread.sleep(1);
    run = false; // 线程t会停下来
}
```

2、使用`synchronized`：线程遇到`synchronized`关键字时会把工作内存中的缓存清空，这样当前线程栈中共享变量的“副本”就没有了，当再次要用到共享变量时，只能去主内存中拷贝一份新的“副本”，这样就保证了每次读取的共享变量的值都是最新的

```java
public class test02 {

    static boolean run = true;
    static Object object = new Object();

    public static void main(String[] args) throws InterruptedException {

        Thread t = new Thread(()->{
            while(true){
                synchronized (object) {
                    if (!run){
                        break;
                    }
                }
            }
        });
        t.start();

        Thread.sleep(2);
        run = false; // 线程t会停下来
    }
}
```





**volatile和synchronized的区别**

- `volatile`只能保证变量的可见性，不能保证原子性

- `synchronized`既可以保证可见性，也可以保证原子性，但是`synchronized` 属于重量级操作，性能相对更低 
  - 在前边死循环的代码中加入`System.out.println()`，不使用`volatile`，线程t也能正确看到run变量被修改，因为`println`内部使用了`synchronized`





### 3、设计模式--犹豫模式

Balking （犹豫）模式用在一个线程发现另一个线程或本线程已经做了某一件相同的事，那么本线程就无需再做了，直接结束返回 



**实现**

监控线程一般启动一个即可，如果启动多个会造成资源浪费，因此这里通过判断`starting`的状态，如果为true，直接返回，否则将`starting`置位true，然后启动监控线程，保证监控线程只会被创建一次

```java
public class MonitorService {
    
    // 用来表示是否已经有线程已经在执行启动了
    private volatile boolean starting;
    
    public void start() {
        log.info("尝试启动监控线程...");
        synchronized (this) {
            if (starting) {
                return;
            }
            starting = true;
        }
        
        // 真正启动监控线程...
    }
}
```



**实现线程安全的单例**

```java
public final class Singleton {
    
    private Singleton() { }
    
    private static Singleton INSTANCE = null;
    public static synchronized Singleton getInstance() {
        if (INSTANCE != null) {
            return INSTANCE;
        }
        
        INSTANCE = new Singleton();
        return INSTANCE;
    }
}
```







### 4、有序性

#### 4.1 指令重排序

JVM 会在不影响正确性的前提下，可以调整语句的执行顺序

> 下边这段代码，先执行`i = 10`还是先执行`j = 20`，对最终结果不会产生影响，因此执行时，既可以先`i`后`j`，也可以先`j`后`i`

```java
static int i;
static int j;

i = 10; 
j = 20;
```

这种特性称之为**指令重排**，多线程下**指令重排**会影响正确性。





**指令重排序优化**

> 现代处理器会设计为一个时钟周期完成一条执行时间最长的 CPU 指令。
>
> 指令还可以再划分成一个个更小的阶段。例如，每条指令都可以分为： `取指令 - 指令译码 - 执行指令 - 内存访问 - 数据写回` 这 5 个阶段

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401210017118.png)

如果每条指令的这五个阶段都串行执行，效率会很低。



> 现代 CPU 支持**多级指令流水线**，例如支持同时执行 `取指令 - 指令译码 - 执行指令 - 内存访问 - 数据写回` 的处理器，就可以称之为**五级指令流水线**。这时 CPU 可以在一个时钟周期内，同时运行五条指令的不同阶段（相当于一 条执行时间最长的复杂指令），本质上，流水线技术并不能缩短单条指令的执行时间，但它变相地提高了 指令的吞吐率。 

在不改变程序结果的前提下，这些指令的各个阶段可以通过**重排序**和**组合**来实现**指令级并行**

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401210020151.png" style="zoom:80%;" />





**指令重排序导致的诡异结果**

> 1、线程1 先执行，这时 ready = false，所以进入 else 分支结果为 1 
>
> 2、线程2 先执行 num = 2，但没来得及执行 ready = true，线程1 执行，还是进入 else 分支,结果为1 
>
> 3、线程2 执行到 ready = true，线程1 执行，这回进入 if 分支，结果为 4（因为 num 已经执行过了） 
>
> 4、线程2 执行 ready = true，切换到线程1，进入 if 分支，相加为 0，再切回线程2 执行 num = 2 
>
> - 此时就发生了指令重排，是 JIT 编译器在运行时的一些优化【先执行`ready = true`，后执行`num = 2`】，这个现象需要通过大量测试才能复现

```java
int num = 0;
boolean ready = false;

// 线程1 执行此方法
public void actor1(I_Result r) {
    if (ready) {
        r.r1 = num + num;
    } else {
        r.r1 = 1;
    }
}

// 线程2 执行此方法
public void actor2(I_Result r) { 
    //这里可能发生指令重排序
    num = 2;
    ready = true; 
}
```





**解决方法**

> `volatile` 修饰的变量，可以禁用指令重排
>
> 将`ready`变量使用`volatile`修饰后，会在`ready = true`语句后加上写屏障，禁止前边的指令重排

```java
int num = 0;
volatile boolean ready = false;

// 线程1 执行此方法
public void actor1(I_Result r) {
    if (ready) {
        r.r1 = num + num;
    } else {
        r.r1 = 1;
    }
}

// 线程2 执行此方法
public void actor2(I_Result r) { 
    //这里可能发生指令重排序
    num = 2;
    ready = true; 
}
```







### 5、volatile原理

`volatile` 的底层实现原理是内存屏障，Memory Barrier（Memory Fence） 

- 对 `volatile` 变量的写指令后会加入写屏障 
- 对 `volatile` 变量的读指令前会加入读屏障



#### 5.1 保证可见性

- 写屏障（sfence）保证在该屏障之前的，对共享变量的改动，都同步到主存当中

  > 如果没有使用`volatile`修饰，修改一个共享变量时，通常只是修改了自己的工作内存中该变量的值，并没有立即将该变量的值写回主存，当线程结束时，它会将自己工作内存中修改过的共享变量的值写回主存

  ```java
  public void actor2(I_Result r) {
      num = 2;
      ready = true; // ready 是 volatile 赋值带写屏障
      // 写屏障
  }
  ```

- 而读屏障（lfence）保证在该屏障之后，对共享变量的读取，加载的是主存中最新数据

  ```java
  public void actor1(I_Result r) {
      // 读屏障
      // ready 是 volatile 读取值带读屏障
      if(ready) {
          r.r1 = num + num;
      } else {
          r.r1 = 1;
      }
  }
  ```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211537488.png" style="zoom:80%;" />





#### 5.2 保证有序性

- 写屏障会确保指令重排序时，不会将写屏障之前的代码排在写屏障之后

```java
public void actor2(I_Result r) {
    num = 2;
    ready = true; // ready 是 volatile, 赋值带写屏障
    // 写屏障
}
```

- 读屏障会确保指令重排序时，不会将读屏障之后的代码排在读屏障之前

```java
public void actor1(I_Result r) {
    // 读屏障
    // ready 是 volatile 读取值带读屏障
    if(ready) {
        r.r1 = num + num;
    } else {
        r.r1 = 1;
    }
}
```

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211539443.png" style="zoom:80%;" />





`volatile`不能解决指令交错：

- 写屏障仅仅是保证之后的读能够读到最新的结果，但不能保证读跑到它前面去 
- 而有序性的保证也只是保证了本线程内相关代码不被重排序

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211541792.png" style="zoom:80%;" />







### 6、双重校验锁

> `synchronized`内的代码也会发生指令重排序，但是如果这个共享变量的使用完全在`synchronized`内部，即使指令重排序了，也不会有影响，因为每次只有一个线程执行。但是如果对于共享变量的使用，一部分在`synchronized`外部，那么多线程运行时可能就会由于指令重排序造成影响



**双重校验锁实现单例模式**

```java
public final class Singleton {
    private Singleton() { }
    private static Singleton INSTANCE = null;
    
    public static Singleton getInstance() { 
        if (INSTANCE == null) { // t2
            // 首次访问会同步，而之后的使用没有 synchronized
            synchronized (Singleton.class) {
                if (INSTANCE == null) { // t1
                    INSTANCE = new Singleton(); 
                } 
            }
        }
        return INSTANCE;
    }
}
```



多线程环境下，上述代码是有问题的，下边是上述代码中从`if (INSTANCE == null) `往后的字节码

> 0：获取静态变量`INSTANCE`
>
> 3：判断是否为null，如果不为null，跳转到37，获取静态变量`INSTANCE`，然后返回
>
> 6：获取`Singleton.class`类对象
>
> 8：将类对象复制一份引用，用于解锁
>
> 9：将该引用保存起来
>
> 10：加锁
>
> 11：获取静态变量`INSTANCE`
>
> 14：判断`INSTANCE`是否为null，不为null，执行27，拿到锁的类对象，然后释放锁，跳到37,
>
> **17**：`new`一个`Singleton`对象，分配内存
>
> **20**：复制一份这个对象的引用
>
> **21**：对象初始化
>
> **24**：将创建好的对象赋值给`INSTANCE`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211547394.png)  

其中 

- 17 表示创建对象，将对象引用入栈   // new Singleton 
- 20 表示复制一份对象引用   // 引用地址 
- 21 表示利用一个对象引用，调用构造方法 
- 24 表示利用一个对象引用，赋值给 static INSTANCE 



也许 jvm 会优化为：先执行 24，再执行 21。如果两个线程 t1，t2 按如下时间序列执行

> 先执行24，这时 t1 还未完全将构造方法执行完毕，如果在构造方法中要执行很多初始化操作，那么 t2 拿到的是将是一个未初始化完毕的单例 

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211556168.png" style="zoom:80%;" />





**解决方法**

对 `INSTANCE` 使用 `volatile` 修饰即可，可以禁用指令重排，但要注意在 JDK 5 以上的版本的 `volatile` 才会真正有效 

```java
public final class Singleton {
    private Singleton() { }
    private static volatile Singleton INSTANCE = null;
    
    public static Singleton getInstance() {
        // 实例没创建，才会进入内部的 synchronized代码块
        if (INSTANCE == null) { 
            synchronized (Singleton.class) { // t2
                // 也许有其它线程已经创建实例，所以再判断一次
                if (INSTANCE == null) { // t1
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```



**字节码**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211600728.png) 

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211559595.png) 

- 可见性 
  - 写屏障（sfence）保证在该屏障之前的 t1 对共享变量的改动，都同步到主存当中 
  - 读屏障（lfence）保证在该屏障之后 t2 对共享变量的读取，加载的是主存中最新数据 

- 有序性 
  - 写屏障会确保指令重排序时，不会将写屏障之前的代码排在写屏障之后，因此21一定是在24之前执行的，即先初始化再赋值
  - 读屏障会确保指令重排序时，不会将读屏障之后的代码排在读屏障之前 








### 7、happens-before

> `happens-before` 规定了对共享变量的写操作对其它线程的读操作可见，它是可见性与有序性的一套规则总结，抛开以下 happens-before 规则，JMM 并不能保证一个线程对共享变量的写，对于其它线程对该共享变量的读可见 



1、线程解锁 m 之前对变量的写，对于接下来对 m 加锁的其它线程对该变量的读可见

```java
static int x;
static Object m = new Object();

new Thread(()->{
    synchronized(m) {
        x = 10;
    }
},"t1").start();

new Thread(()->{
    synchronized(m) {
        System.out.println(x);
    }
},"t2").start();
```



2、线程对 volatile 变量的写，对接下来其它线程对该变量的读可见

```java
volatile static int x;

new Thread(()->{
    x = 10;
},"t1").start();

new Thread(()->{
    System.out.println(x);
},"t2").start();
```



3、线程 start 前对变量的写，对该线程开始后对该变量的读可见

```java
static int x; 
x = 10;

new Thread(()->{
    System.out.println(x);
},"t2").start();
```



4、线程结束前对变量的写，对其它线程得知它结束后的读可见（比如其它线程调用 t1.isAlive() 或 t1.join()等待它结束），因为线程结束后，会将变量的修改写回主存

```java
static int x;

Thread t1 = new Thread(()->{
    x = 10;
},"t1");
t1.start();

t1.join();
System.out.println(x);
```



5、线程 t1 打断 t2（interrupt）前对变量的写，对于其他线程得知 t2 被打断后对变量的读可见（通过t2.interrupted 或 t2.isInterrupted）

```java
static int x;

public static void main(String[] args) {
    Thread t2 = new Thread(()->{
        while(true) {
            if(Thread.currentThread().isInterrupted()) {
                System.out.println(x);
                break;
            }
        }
    },"t2");
    t2.start();
    
    new Thread(()->{
        sleep(1);
        x = 10;
        t2.interrupt();
    },"t1").start();
    
    while(!t2.isInterrupted()) {
        Thread.yield();
    }
    System.out.println(x);
}
```



6、对变量默认值（0，false，null）的写，对其它线程对该变量的读可见



7、具有传递性，如果 `x hb-> y` 并且 `y hb-> z` 那么有 `x hb-> z` 

> x使用了`volatile`修饰，因此在`x=20`后会加入写屏障，写屏障之前的写操作都会写入主存，虽然y没有使用`volatile`修饰，但是对y的修改也会写入主存

```java
volatile static int x;
static int y;

new Thread(()->{ 
    y = 10;
    x = 20;
},"t1").start();

new Thread(()->{
    // x=20 对 t2 可见, 同时 y=10 也对 t2 可见
    System.out.println(x); 
},"t2").start();
```







## 四、共享模型-无锁

### 1、CAS

CAS即Compare And Swap（比较与交换），每次修改时，会先得到修改前的值，在修改时，如果这个值与之前得到的值一样，那么修改成功，否则修改失败



**示例**

> 多线程情况下执行如下代码，如果`balance`使用的是`int`类型，则会产生并发安全问题。这里使用的是`AtomicInteger`类型，即原子整数，底层基于CAS实现，保证了线程安全

```java
class AccountSafe implements Account {
    
    private AtomicInteger balance; //原子整数
    
    public AccountSafe(Integer balance) {
        this.balance = new AtomicInteger(balance);
    }
    
    @Override
    public Integer getBalance() {
        return balance.get();
    }
    
    @Override
    public void withdraw(Integer amount) {
        while (true) {
            int prev = balance.get();
            int next = prev - amount;
            if (balance.compareAndSet(prev, next)) {
                break;
            }
        }
    }
}
```





**CAS 与 volatile** 

> `AtomicInteger` 内部并没有用锁来保护共享变量的线程安全。以下是部分源码

```java
public class AtomicInteger extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 6214790243416807050L;

    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;

    static {
        try {
            // unsafe.objectFieldOffset是用来获得当前属性在类中的内存地址偏移量，这样才能通过unsafe操作，因为unsafe操作的就是内存
            valueOffset = unsafe.objectFieldOffset 
                (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }

    private volatile int value;

    public AtomicInteger(int initialValue) {
        value = initialValue;
    }

    public AtomicInteger() {
    }
    
    public final int get() {
        return value;
    }

    public final void set(int newValue) {
        value = newValue;
    }

    public final int getAndSet(int newValue) {
    }

    public final boolean compareAndSet(int expect, int update) {
        // 第一个参数是修改的对象，第二个参数表示当前属性偏移量
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
}
```



我们使用的就是`compareAndSet`来进行赋值，该方法是一个原子操作。可以看到，`AtomicInteger`类中`value`属性是使用`volatile`修饰的，这样我们通过`compareAndSet`方法进行赋值时，即使其他线程对`value`进行了修改，这里也是去主存获取，保证了变量的可见性，然后和期望值`except`比较，如果相同就修改，否则返回false。CAS 必须借助 volatile 才能读取到共享变量的最新值来实现【比较并交换】的效果

> 其实 CAS 的底层是 `lock cmpxchg` 指令（X86 架构），在单核 CPU 和多核 CPU 下都能够保证【比较-交换】的原子性。
>
> 在多核状态下，某个核执行到带 lock 的指令时，CPU 会让总线锁住，当这个核把此指令执行完毕，再开启总线。这个过程中不会被线程的调度机制所打断，保证了多个线程对内存操作的准确性，是原子的

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401211727900.png" style="zoom: 65%;" />







**CAS无锁效率高**

- 无锁情况下，即使重试失败，线程始终在高速运行，没有停歇，而 `synchronized` 会让线程在没有获得锁的时候，发生上下文切换，进入阻塞。

  > - 线程就好像高速跑道上的赛车，高速运行时，速度超快，一旦发生上下文切换【RUNNABLE状态切换为BOLCKED状态】，就好比赛车要减速、熄火,等被唤醒又得重新打火、启动、加速... 恢复到高速运行，代价比较大 
  >
  > - 但无锁情况下，因为线程要保持运行，需要额外 CPU 的支持，CPU 在这里就好比高速跑道，没有额外的跑道，线程想高速运行也无从谈起，虽然不会进入阻塞，但由于没有分到时间片，仍然会进入不可运行状态，还是会导致上下文切换。





**CAS特点**

CAS 和 volatile 可以实现无锁并发，适用于线程数少、多核 CPU 的场景

CAS 体现的是无锁并发、无阻塞并发

- 因为没有使用 synchronized，所以线程不会陷入阻塞，这是效率提升的因素之一 
- 但如果竞争激烈，重试必然频繁发生，反而效率会受影响







### 2、原子整数

JUC 并发包提供了

- `AtomicBoolean `
- `AtomicInteger `
- `AtomicLong `



**AtomicInteger **

```java
AtomicInteger i = new AtomicInteger(0);

// 获取并自增（i = 0, 结果 i = 1, 返回 0），类似于 i++
System.out.println(i.getAndIncrement());

// 自增并获取（i = 1, 结果 i = 2, 返回 2），类似于 ++i
System.out.println(i.incrementAndGet());

// 自减并获取（i = 2, 结果 i = 1, 返回 1），类似于 --i
System.out.println(i.decrementAndGet());

// 获取并自减（i = 1, 结果 i = 0, 返回 1），类似于 i--
System.out.println(i.getAndDecrement());

// 获取并加值（i = 0, 结果 i = 5, 返回 0）
System.out.println(i.getAndAdd(5));

// 加值并获取（i = 5, 结果 i = 0, 返回 0）
System.out.println(i.addAndGet(-5));

// 获取并更新（i = 0, p 为 i 的当前值, 结果 i = -2, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
System.out.println(i.getAndUpdate(p -> p - 2));

// 更新并获取（i = -2, p 为 i 的当前值, 结果 i = 0, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
System.out.println(i.updateAndGet(p -> p + 2));

// 获取并计算（i = 0, p 为 i 的当前值, x 为参数1, 结果 i = 10, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
// getAndUpdate 如果在 lambda 中引用了外部的局部变量，要保证该局部变量是 final 的
// getAndAccumulate 可以通过 参数1 来引用外部的局部变量，但因为其不在 lambda 中因此不必是 final
System.out.println(i.getAndAccumulate(10, (p, x) -> p + x));

// 计算并获取（i = 10, p 为 i 的当前值, x 为参数1, 结果 i = 0, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
System.out.println(i.accumulateAndGet(-10, (p, x) -> p + x));
```



其中`updateAndGet`的底层实现如下，接收参数是一个`IntUnaryOperator`的接口，这个接口只有一个方法`int applyAsInt(int operand)`，接收一个int，返回一个int，`applyAsInt`方法定义了接收参数如何计算。`updateAndGet`方法中先获取修改之前的值，然后计算修改后的值，最后通过`CAS`的方式修改

```java
public final int updateAndGet(IntUnaryOperator updateFunction) {
    int prev, next;
    do {
        prev = get();
        next = updateFunction.applyAsInt(prev);
    } while (!compareAndSet(prev, next));
    return next;
}
```







### 3、原子引用

上边的原子整数，只能针对整数类型，如果想针对引用类型，需要使用原子引用

- AtomicReference 
- AtomicStampedReference 
- AtomicMarkableReference 



#### 3.1 AtomicReference 

> 还是上述修改余额额操作，1000个线程调用`withdraw`方法，余额balance为`BigDecimal`类型，因此需要使用`AtomicReference `

```java
class DecimalAccountSafeCas implements DecimalAccount {
    AtomicReference<BigDecimal> ref;
    
    public DecimalAccountSafeCas(BigDecimal balance) {
        ref = new AtomicReference<>(balance);
    }
    
    @Override
    public BigDecimal getBalance() {
        return ref.get();
    }
    
    @Override
    public void withdraw(BigDecimal amount) {
        while (true) {
            BigDecimal prev = ref.get();
            BigDecimal next = prev.subtract(amount);
            if (ref.compareAndSet(prev, next)) {
                break;
            }
        }
    }
}
```







#### 3.2 ABA问题

> 主线程想将`A`通过CAS的方式修改为`C`，但是在获取了修改前的值后，线程t，将`A`修改为`B`，然后又将`B`修改回`A`，此时主线程执行`compareAndSet`方法时，并不知道`A`已经被修改过了，执行仍然成功

```java
static AtomicReference<String> ref = new AtomicReference<>("A");

public static void main(String[] args) throws InterruptedException {
    log.debug("main start...");
    // 获取值 A
    // 这个共享变量被它线程修改过？
    String prev = ref.get();
    
    other();
    
    sleep(1);
    // 尝试改为 C
    log.debug("change A->C {}", ref.compareAndSet(prev, "C"));
}

private static void other() {
    
    new Thread(() -> {
        log.debug("change A->B {}", ref.compareAndSet(ref.get(), "B"));
    }, "t1").start();
    
    sleep(0.5);
    
    new Thread(() -> {
        log.debug("change B->A {}", ref.compareAndSet(ref.get(), "A"));
    }, "t2").start();
    
}
```







#### 3.3 AtomicStampedReference 

`AtomicStampedReference `就是针对`AtomicReference `增加了版本号机制，每次修改时，判断值相等的同时，还要判断版本号是否一样，版本号在每次修改时都会改变，因此可以避免ABA问题。此时虽然其他线程将`A`修改为`B`，然后又将`B`修改为`A`，但是版本号变了，所以主线程修改会失败

```java
static AtomicStampedReference<String> ref = new AtomicStampedReference<>("A", 0);

public static void main(String[] args) throws InterruptedException {
    log.debug("main start...");
    // 获取值 A
    String prev = ref.getReference();
    // 获取版本号
    int stamp = ref.getStamp();
    log.debug("版本 {}", stamp);
    // 如果中间有其它线程干扰，发生了 ABA 现象
    other();
    sleep(1);
    // 尝试改为 C
    log.debug("change A->C {}", ref.compareAndSet(prev, "C", stamp, stamp + 1));
}

private static void other() {
    new Thread(() -> {
        log.debug("change A->B {}", ref.compareAndSet(ref.getReference(), "B", 
                                                      ref.getStamp(), ref.getStamp() + 1));
        log.debug("更新版本为 {}", ref.getStamp());
    }, "t1").start();
    
    sleep(0.5);
    
    new Thread(() -> {
        log.debug("change B->A {}", ref.compareAndSet(ref.getReference(), "A", 
                                                      ref.getStamp(), ref.getStamp() + 1));
        log.debug("更新版本为 {}", ref.getStamp());
    }, "t2").start();
}
```







#### 3.4 AtomicMarkableReference

> `AtomicStampedReference` 可以给原子引用加上版本号，追踪原子引用整个的变化过程，如： `A -> B -> A -> C` ，通过`AtomicStampedReference`，我们可以知道，引用变量中途被更改了几次。 
>
> 有时候，并不关心引用变量更改了几次，只是单纯的关心**是否更改过**，所以就有了 `AtomicMarkableReference `

```java
@Slf4j
public class TestABAAtomicMarkableReference {
    public static void main(String[] args) throws InterruptedException {
        GarbageBag bag = new GarbageBag("装满了垃圾");
        // 参数2 mark 可以看作一个标记，表示垃圾袋满了
        AtomicMarkableReference<GarbageBag> ref = new AtomicMarkableReference<>(bag, true);
        
        log.debug("主线程 start...");
        GarbageBag prev = ref.getReference();
        log.debug(prev.toString());
        
        new Thread(() -> {
            log.debug("打扫卫生的线程 start...");
            bag.setDesc("空垃圾袋");
            while (!ref.compareAndSet(bag, bag, true, false)) {}
            log.debug(bag.toString());
        }).start();
        
        Thread.sleep(1000);
        log.debug("主线程想换一只新垃圾袋？");
        boolean success = ref.compareAndSet(prev, new GarbageBag("空垃圾袋"), true, false);
        log.debug("换了么？" + success);
        
        log.debug(ref.getReference().toString());
    }
}
```







### 4、原子数组

> 原子引用，是针对引用对象做修改，而如果想要修改引用对象本身的值，例如修改数组的值，就用到了原子数组

- `AtomicIntegerArray `
- `AtomicLongArray`
- `AtomicReferenceArray `



**示例**

```java
/**
    参数1，提供数组、可以是线程不安全数组或线程安全数组
    参数2，获取数组长度的方法
    参数3，自增方法，回传 array, index
    参数4，打印数组的方法
*/
// supplier 提供者 无中生有 ()->结果
// function 函数 一个参数一个结果 (参数)->结果 , BiFunction (参数1,参数2)->结果
// consumer 消费者 一个参数没结果 (参数)->void, BiConsumer (参数1,参数2)->void
private static <T> void demo(
    Supplier<T> arraySupplier,
    Function<T, Integer> lengthFun,
    BiConsumer<T, Integer> putConsumer,
    Consumer<T> printConsumer ) {
    
    List<Thread> ts = new ArrayList<>();
    T array = arraySupplier.get(); // 拿到数组对象
    int length = lengthFun.apply(array); // 拿到数组长度
    for (int i = 0; i < length; i++) {
        // 每个线程对数组作 10000 次操作
        ts.add(new Thread(() -> {
            for (int j = 0; j < 10000; j++) {
                putConsumer.accept(array, j % length);
            }
        }));
    }
    ts.forEach(t -> t.start()); // 启动所有线程
    
    ts.forEach(t -> {
        try {
            t.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }); // 等所有线程结束
    printConsumer.accept(array);
}
```

**普通数组**

```java
demo(
    ()->new int[10],
    (array)->array.length,
    (array, index) -> array[index]++,
    array-> System.out.println(Arrays.toString(array))
);

// 结果 [9870, 9862, 9774, 9697, 9683, 9678, 9679, 9668, 9680, 9698] 
```

**AtomicIntegerArray**

```java
demo(
    ()-> new AtomicIntegerArray(10),
    (array) -> array.length(),
    (array, index) -> array.getAndIncrement(index),
    array -> System.out.println(array)
);

// 结果 [10000, 10000, 10000, 10000, 10000, 10000, 10000, 10000, 10000, 10000] 
```







### 5、原子更新器

- `AtomicReferenceFieldUpdater`
- `AtomicIntegerFieldUpdater `
- `AtomicLongFieldUpdater `

利用字段更新器，可以针对对象的某个域（Field）进行原子操作，只能配合` volatile` 修饰的字段使用，否则会出现异常 `Exception in thread "main" java.lang.IllegalArgumentException: Must be volatile type`



```java
public class Test5 {
    private volatile int field;
    
    public static void main(String[] args) {
        AtomicIntegerFieldUpdater fieldUpdater = AtomicIntegerFieldUpdater.newUpdater(Test5.class, "field");
        
        Test5 test5 = new Test5();
        fieldUpdater.compareAndSet(test5, 0, 10);
        // 修改成功 field = 10
        System.out.println(test5.field);
        // 修改成功 field = 20
        fieldUpdater.compareAndSet(test5, 10, 20);
        System.out.println(test5.field);
        // 修改失败 field = 20
        fieldUpdater.compareAndSet(test5, 10, 30);
        System.out.println(test5.field);
    }
}
```







### 6、原子累加器

`AtomicLong`类中的`getAndIncrement`方法可以实现元素的累加。而原子累加器`LongAdder`也是实现的这个功能，但是他的性能要比`AtomicLong`高很多

> 性能提升的原因：`AtomicLong`在CAS失败时，会在while循环中不断重试。而`LongAdder`在有竞争时，设置多个累加单元，Therad-0 累加 Cell[0]，而 Thread-1 累加Cell[1]... 最后将结果汇总。
>
> 这样它们在累加时操作的不同的 Cell 变量，因此减少了 CAS 重试失败，从而提高性能。



**示例**

```java
private static <T> void demo(Supplier<T> adderSupplier, Consumer<T> action) {
    T adder = adderSupplier.get();
    
    long start = System.nanoTime();
    
    List<Thread> ts = new ArrayList<>();
    // 4 个线程，每人累加 50 万
    for (int i = 0; i < 4; i++) {
        ts.add(new Thread(() -> {
            for (int j = 0; j < 500000; j++) {
                action.accept(adder);
            }
        }));
    }
    ts.forEach(t -> t.start());
    
    ts.forEach(t -> {
        try {
            t.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
    long end = System.nanoTime();
    System.out.println(adder + " cost:" + (end - start)/1000_000);
}
```

```java
demo(() -> new LongAdder(), adder -> adder.increment()); // 输出：2000000 cost:9 

demo(() -> new AtomicLong(), adder -> adder.getAndIncrement()); // 输出：2000000 cost:27 
```





#### 6.1 LongAdder 源码分析

**1、LongAdder 类的几个重要属性**

```java
// 累加单元数组, 懒惰初始化
transient volatile Cell[] cells;

// 基础值, 如果没有竞争, 则用 cas 累加这个域
transient volatile long base;

// 在 cells 创建或扩容时, 置为 1, 表示加锁
transient volatile int cellsBusy;
```

**cellsBusy加锁原理**

无锁时，state为0，线程1来加锁，将state从0修改为1，此时线程2来加锁，由于CAS失败，会一直重试，直到线程1释放锁

```java
public class LockCas {
    private AtomicInteger state = new AtomicInteger(0);
    
    public void lock() {
        while (true) {
            if (state.compareAndSet(0, 1)) {
                break;
            }
        }
    }
    
    public void unlock() {
        log.debug("unlock...");
        state.set(0);
    }
}
```



**2、缓存行伪共享问题**

`Cell`需要防止缓存行伪共享问题，`Cell `即为累加单元

```java
@sun.misc.Contended
static final class Cell {

    volatile long value;
    
    Cell(long x) { 
        value = x; 
    }

    // 最重要的方法, 用来 cas 方式进行累加, prev 表示旧值, next 表示新值
    final boolean cas(long prev, long next) {
        return UNSAFE.compareAndSwapLong(this, valueOffset, prev, next);
    }
	// 省略不重要代码
}
```



- 一个线程会对应一个CPU核心，一个CPU核心会有一级缓存、二级缓存、三级缓存等，CPU访问缓存以及内存的所需时间如下，可以看到访问缓存的效率是非常高的
- 而缓存以**缓存行**为单位，每个**缓存行对应着一块内存**，一般是 **64 byte**（8 个 long） ，缓存的加入会造成数据副本的产生，即同一份数据会缓存在不同核心的缓存行中 。CPU 要保证数据的一致性，如果某个 CPU 核心更改了数据，其它 CPU 核心对应的整个缓存行必须失效

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222151839.png" style="zoom: 65%;" /> <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222151154.png" style="zoom: 80%;" /> 



- 因为 Cell 是数组形式，在内存中是连续存储的，一个 Cell 为 24 字节（16 字节的对象头和 8 字节的 value），因此缓存行可以存下 2 个的 Cell 对象。但是如果出现如下情况

  - Core-0 要修改 Cell[0] 

  - Core-1 要修改 Cell[1] 

无论谁修改成功，都会导致对方 Core 的缓存行失效，比如 Core-0 中 `Cell[0]=6000, Cell[1]=8000` 要累加`Cell[0]=6001`, `Cell[1]=8000` ，这时会让 Core-1 的缓存行失效 ;  同理 Core-1修改Cell[1]也会让 Core-0 的缓存行失效

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222156241.png)

- `@sun.misc.Contended` 用来解决这个问题，它的原理是在使用此注解的对象或字段的前后各增加 128 字节大小的padding，从而让 CPU 将对象预读至缓存时占用不同的缓存行，这样，不会造成对方缓存行的失效 



**3、add源码**

```java
public void add(long x) {
    // as 为累加单元数组
    // b 为基础值
    // x 为累加值
    Cell[] as; long b, v; int m; Cell a;
    
    // 进入 if 的两个条件
    // 1. as 不为空, 表示已经发生过竞争, 进入 if
    // 2. cas 给 base 累加时失败了, 表示 base 发生了竞争, 进入 if
    if ((as = cells) != null || !casBase(b = base, b + x)) {
        // uncontended 表示 cell 没有竞争
        boolean uncontended = true;
        if (
            // as 还没有创建
            as == null || (m = as.length - 1) < 0 ||
            // 当前线程对应的 cell 还没有创建
            (a = as[getProbe() & m]) == null ||
            // cas 给当前线程的 cell 累加失败 uncontended=false ( a 为当前线程的 cell )
            !(uncontended = a.cas(v = a.value, v + x))
        ) {
            // 进入 cell 数组创建、cell 创建的流程
            longAccumulate(x, null, uncontended);
        }
    }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222200236.png)



**4、longAccumulate源码**

```java
final void longAccumulate(long x, LongBinaryOperator fn,boolean wasUncontended) {
    int h;
    // 当前线程还没有对应的 cell, 需要随机生成一个 h 值用来将当前线程绑定到 cell
    if ((h = getProbe()) == 0) {
        // 初始化 probe
        ThreadLocalRandom.current();
        // h 对应新的 probe 值, 用来对应 cell
        h = getProbe();
        wasUncontended = true;
    }
    // collide 为 true 表示需要扩容
    boolean collide = false; 
    for (;;) {
        Cell[] as; Cell a; int n; long v;
        // 已经有了 cells
        if ((as = cells) != null && (n = as.length) > 0) {
            // 还没有 cell
            if ((a = as[(n - 1) & h]) == null) {
                // 为 cellsBusy 加锁, 创建 cell, cell 的初始累加值为 x
                // 成功则 break, 否则继续 continue 循环
                if (cellsBusy == 0) {       // Try to attach new Cell
                    Cell r = new Cell(x);   // Optimistically create
                    if (cellsBusy == 0 && casCellsBusy()) {
                        boolean created = false;
                        try {               // Recheck under lock
                            Cell[] rs; int m, j;
                            if ((rs = cells) != null &&
                                (m = rs.length) > 0 &&
                                rs[j = (m - 1) & h] == null) {
                                rs[j] = r;
                                created = true;
                            }
                        } finally {
                            cellsBusy = 0;
                        }
                        if (created)
                            break;
                        continue;           // Slot is now non-empty
                    }
                }
                collide = false;
            }
            // 有竞争, 改变线程对应的 cell 来重试 cas
            else if (!wasUncontended)
                wasUncontended = true;
            // cas 尝试累加, fn 配合 LongAccumulator 不为 null, 配合 LongAdder 为 null
            else if (a.cas(v = a.value, ((fn == null) ? v + x : fn.applyAsLong(v, x))))
                break;
            // 如果 cells 长度已经超过了最大长度, 或者已经扩容, 改变线程对应的 cell 来重试 cas
            else if (n >= NCPU || cells != as)
                collide = false;
            // 确保 collide 为 false 进入此分支, 就不会进入下面的 else if 进行扩容了
            else if (!collide)
                collide = true;
            // 加锁
            else if (cellsBusy == 0 && casCellsBusy()) {
                // 加锁成功, 扩容
                try {
                    if (cells == as) {      // Expand table unless stale
                        Cell[] rs = new Cell[n << 1];
                        for (int i = 0; i < n; ++i)
                            rs[i] = as[i];
                        cells = rs;
                    }
                } finally {
                    cellsBusy = 0;
                }
                collide = false;
                continue;       
            }
            // 改变线程对应的 cell
            h = advanceProbe(h);
        }
        // 还没有 cells, 尝试给 cellsBusy 加锁，cells == as表示cells没有被其他线程创建
        else if (cellsBusy == 0 && cells == as && casCellsBusy()) {
            // 加锁成功, 初始化 cells, 最开始长度为 2, 并填充一个 cell
            boolean init = false;
            try {                           // Initialize table
                if (cells == as) {
                    Cell[] rs = new Cell[2];
                    rs[h & 1] = new Cell(x);
                    cells = rs;
                    init = true;
                }
            } finally {
                cellsBusy = 0;
            }
            if (init) // 成功则 break;
                break;
        }
        // 上两种情况失败, 尝试给 base 累加
        else if (casBase(v = base, ((fn == null) ? v + x : fn.applyAsLong(v, x))))
            break;
    }
}
```

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222217829.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222221451.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401222218237.png)



**5、sum源码**

```java
public long sum() {
    Cell[] as = cells; Cell a;
    long sum = base;
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {
            if ((a = as[i]) != null)
                sum += a.value;
        }
    }
    return sum; 
}
```





### 7、Unsafe类

- Unsafe并不是说该类不安全，而是因为该类直接操作内存，比较复杂，在告诉程序员使用该类有较大风险

- Unsafe 对象提供了非常底层的，操作内存、线程的方法，Unsafe 对象不能直接调用，只能通过反射获得

```java
public class UnsafeAccessor {
    static Unsafe unsafe;
    static {
        try { 
            Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
            theUnsafe.setAccessible(true);
            unsafe = (Unsafe) theUnsafe.get(null);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new Error(e);
        }
    }
    static Unsafe getUnsafe() {
        return unsafe;
    }
}
```



```java
import lombok.Data;
import sun.misc.Unsafe;

import java.lang.reflect.Field;

public class TestUnsafeCAS {

    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {

        Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
        theUnsafe.setAccessible(true);
        Unsafe unsafe = (Unsafe) theUnsafe.get(null);

        // 1. 获取字段的偏移地址
        long idOffset = unsafe.objectFieldOffset(Teacher.class.getDeclaredField("id"));
        long nameOffset = unsafe.objectFieldOffset(Teacher.class.getDeclaredField("name"));

        Teacher t = new Teacher();
        System.out.println(t);

        // 2. 执行 cas 操作
        unsafe.compareAndSwapInt(t, idOffset, 0, 1);
        unsafe.compareAndSwapObject(t, nameOffset, null, "张三");

        // 3. 验证
        System.out.println(t);
    }
}

@Data
class Teacher {
    volatile int id;
    volatile String name;
}
```











## 五、共享模型-不可变

### 1、不可变类

例如：String 类就是不可变的

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
    /** Cache the hash code for the string */
    private int hash; // Default to 0
    
    // ... 
}
```

- 属性用 `final` 修饰保证了该属性是只读的，不能修改 
- 类用 `final` 修饰保证了该类中的方法不能被覆盖，防止子类无意间破坏不可变性 





### 2、保护性拷贝

- String是不可变类，在其`substring`方法中，就是创建一个新对象返回，从而保护String的不可变性

- 构造新字符串对象时，会生成新的 `char[] value`，对内容进行复制 。

- 这种通过创建副本对象来避免共享的手段称为**保护性拷贝（**defensive copy）

```java
public String substring(int beginIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    int subLen = value.length - beginIndex;
    if (subLen < 0) {
        throw new StringIndexOutOfBoundsException(subLen);
    }
    return (beginIndex == 0) ? this : new String(value, beginIndex, subLen);
}
```

```java
public String(char value[], int offset, int count) {
    if (offset < 0) {
        throw new StringIndexOutOfBoundsException(offset);
    }
    if (count <= 0) {
        if (count < 0) {
            throw new StringIndexOutOfBoundsException(count);
        }
        if (offset <= value.length) {
            this.value = "".value;
            return;
        }
    }
    if (offset > value.length - count) {
        throw new StringIndexOutOfBoundsException(offset + count);
    }
    this.value = Arrays.copyOfRange(value, offset, offset+count);
}
```







### 3、享元模式

享元模式（flyweight）是一种通过与其他类似对象共享尽可能多的数据来最小化内存使用的对象



**体现**

在`JDK`中 `Boolean`，`Byte`，`Short`，`Integer`，`Long`，`Character`等包装类提供了 `valueOf` 方法，例如 `Long` 的`valueOf` 会缓存 -128~127 之间的 Long 对象，在这个范围之间会重用对象，大于这个范围，才会新建 Long 对象

```java
public static Long valueOf(long l) {
    final int offset = 128;
    if (l >= -128 && l <= 127) { // will cache
        return LongCache.cache[(int)l + offset];
    }
    return new Long(l);
}
```

- `Byte`, `Short`, `Long` 缓存的范围都是 -128~127 
- `Character` 缓存的范围是 0~127 
- `Integer`的默认范围是 -128~127 
  - 最小值不能变 
  - 但最大值可以通过调整虚拟机参数 `-Djava.lang.Integer.IntegerCache.high` 来改变 
- `Boolean` 缓存了 `TRUE `和 `FALSE`



**BigDecimal**

`BigDecimal`中的`add`方法，返回的是新创建的一个对象

```java
private static BigDecimal add(final long xs, int scale1, final long ys, int scale2) {
    long sdiff = (long) scale1 - scale2;
    if (sdiff == 0) {
        return add(xs, ys, scale1);
    } else if (sdiff < 0) {
        int raise = checkScale(xs,-sdiff);
        long scaledX = longMultiplyPowerTen(xs, raise);
        if (scaledX != INFLATED) {
            return add(scaledX, ys, scale2);
        } else {
            BigInteger bigsum = bigMultiplyPowerTen(xs,raise).add(ys);
            return ((xs^ys)>=0) ? // same sign test
                new BigDecimal(bigsum, INFLATED, scale2, 0)
                : valueOf(bigsum, scale2, 0);
        }
    } else {
        int raise = checkScale(ys,sdiff);
        long scaledY = longMultiplyPowerTen(ys, raise);
        if (scaledY != INFLATED) {
            return add(xs, scaledY, scale1);
        } else {
            BigInteger bigsum = bigMultiplyPowerTen(ys,raise).add(xs);
            return ((xs^ys)>=0) ?
                new BigDecimal(bigsum, INFLATED, scale1, 0)
                : valueOf(bigsum, scale1, 0);
        }
    }
}
```







### 4、final

```java
public class TestFinal {
    final int a = 20; 
}
```

**对应字节码**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401232135836.png) 

`final` 变量的赋值也会通过 `putfield` 指令来完成，同样在这条指令之后也会加入**写屏障**，保证在其它线程读到它的值时不会出现为 0 的情况





**匿名内部类访问的局部变量必须要用final修饰**

> 参考 https://blog.csdn.net/tianjindong0804/article/details/81710268

- 匿名内部类之所以可以访问局部变量，是因为在底层将这个局部变量的值传入到了匿名内部类中，

- 并且以匿名内部类的成员变量的形式存在，这个值的传递过程是通过匿名内部类的构造器完成的。



局部变量用final修饰实际上就是为了保护数据的一致性：对引用变量来说是引用地址的一致性，对基本类型来说就是值的一致性。



匿名内部类将数据拷贝完成后，如果不用final修饰，则原先的局部变量可以发生变化。如果局部变量发生变化后，匿名内部类是不知道的（因为他只是拷贝了局部变量的值，并不是直接使用的局部变量）。

**例如**：原先局部变量指向的是对象A，在创建匿名内部类后，匿名内部类中的成员变量也指向A对象。但过了一段时间局部变量的值指向另外一个B对象，但此时匿名内部类中还是指向原先的A对象。那么程序再接着运行下去，可能就会导致程序运行的结果与预期不同。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401232138616.png)

> 在JDK8中如果我们在匿名内部类中需要访问局部变量，那么这个局部变量不需要用final修饰符修饰。看似是一种编译机制的改变，实际上就是一个语法糖（底层还是帮你加了final）。但通过反编译没有看到底层为我们加上final，但我们无法改变这个局部变量的引用值，如果改变就会编译报错









## 六、线程池

### 1、**ThreadPoolExecutor**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401232356340.png)



#### 1.1 线程池状态 

`ThreadPoolExecutor` 使用 int 的高 3 位来表示线程池状态，低 29 位表示线程数量

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401240000725.png) 

从数字上比较，`TERMINATED` > `TIDYING` > `STOP` > `SHUTDOWN` > `RUNNING` 

> 因为第一位是符号位，`RUNNING` 是负数，所以最小.



这些信息存储在一个原子变量 ctl 中，目的是将线程池状态与线程个数合二为一，这样就可以用一次 cas 原子操作进行赋值

```java
// c 为旧值， ctlOf 返回结果为新值
ctl.compareAndSet(c, ctlOf(targetState, workerCountOf(c))));

// rs 为高 3 位代表线程池状态， wc 为低 29 位代表线程个数，ctl 是合并它们
private static int ctlOf(int rs, int wc) { return rs | wc; }
```







#### 1.2 构造方法

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                         RejectedExecutionHandler handler)
```

- `corePoolSize` ：核心线程数 (最多保留的线程数) 
- `maximumPoolSize`： 最大线程数
- `keepAliveTime` ：生存时间 - 针对救急线程 
- `unit`： 时间单位 - 针对救急线程 
- `workQueue`： 阻塞队列 
- `threadFactory` ：线程工厂 - 可以为线程创建时起名字 
- `handler` ：拒绝策略



**线程池工作流程**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401240004646.png)

1、线程池中刚开始没有线程，当一个任务提交给线程池后，线程池会创建一个新线程来执行任务。 

2、当线程数达到 `corePoolSize` 并没有线程空闲，这时再加入任务，新加的任务会被加入`workQueue` 队列排队，直到有空闲的线程。 

3、如果队列选择了有界队列，那么任务超过了队列大小，并且当前运行的线程数小于最大线程，那么就创建一个线程执行任务

4、如果线程到达 `maximumPoolSize` 仍然有新任务，这时会执行拒绝策略。 jdk 提供了 4 种实现

- `AbortPolicy `：让调用者抛出 `RejectedExecutionException` 异常来拒绝新任务的处理，这是默认策略
- `CallerRunsPolicy` ：让调用者运行任务 ，也就是直接在调用`execute`方法的线程中运行(`run`)被拒绝的任务
- `DiscardPolicy `：不处理新任务，直接丢弃掉
- `DiscardOldestPolicy`： 丢弃队列中最早的任务，本任务取而代之 

5、其它框架也对拒绝策略提供了实现

- `Dubbo` ：在抛出 `RejectedExecutionException` 异常之前会记录日志，并 dump 线程栈信息，方便定位问题 
- `Netty` ：创建一个新线程来执行任务 
- `ActiveMQ`：带超时等待（60s）尝试放入队列
- `PinPoint` ：它使用了一个拒绝策略链，会逐一尝试策略链中每种拒绝策略 

6、当高峰过去后，超过`corePoolSize` 的线程如果一段时间没有任务做，就会销毁，这个时间由`keepAliveTime` 和 `unit` 来控制。核心线程采用的就是懒加载，有任务来了才创建，但是不会被销毁







### 2、Executors

`JDK`中`Executors`类中提供了众多工厂方法来创建各种用途的线程池



#### 2.1 newSingleThreadExecutor

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

- 核心线程数和最大线程数都是1
- 阻塞队列是无界的，最大长度为 `Integer.MAX_VALUE`，可能堆积大量的请求导致OOM



**使用场景**

希望多个任务排队执行。线程数固定为 1，任务数多于 1 时，会放入无界队列排队。任务执行完毕，这唯一的线程也不会被释放。



1、和自己创建一个线程来工作的区别

- 自己创建一个单线程串行执行任务，如果任务执行失败而终止那么没有任何补救措施，而线程池还会新建一个线程，保证池的正常工作 

2、和`Executors.newFixedThreadPool(1)`的区别

- `Executors.newSingleThreadExecutor() `线程个数始终为1，不能修改 
  - `FinalizableDelegatedExecutorService `应用的是装饰器模式，只对外暴露了` ExecutorService` 接口，因此不能调用 `ThreadPoolExecutor` 中特有的方法 

- `Executors.newFixedThreadPool(1)` 初始时为1，以后还可以修改 
  - 对外暴露的是 `ThreadPoolExecutor `对象，可以强转后调用 `setCorePoolSize` 等方法进行修改








#### 2.2 newFixedThreadPool

> 适用于任务量已知，相对耗时的任务

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

- 核心线程数  = 最大线程数（没有救急线程被创建），因此也无需超时时间 
- 阻塞队列是无界的，最大长度为 `Integer.MAX_VALUE`，可能堆积大量的请求导致OOM







#### 2.3 newCachedThreadPool

> 适合任务数比较密集，但每个任务执行时间较短的情况

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

- 核心线程数是 0，最大线程数是 `Integer.MAX_VALUE`，救急线程的空闲生存时间是 60s，意味着创建的全是救急线程，没有任务后60s会销毁

- ` SynchronousQueue`：同步队列，它没有容量，允许创建的线程数量为 `Integer.MAX_VALUE`，即来一个任务，创建一个线程（有线程来取才可以放进去）。如果任务数量过多且执行速度较慢，可能会创建大量的线程，从而导致 OOM







### 3、ThreadFactory

创建线程池时，`threadFactory`参数可以指定一个线程工厂，用来创建线程，并指定线程名称，默认是`Executors.defaultThreadFactory()`

```JAVA
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             Executors.defaultThreadFactory(), defaultHandler);
    }
```

```JAVA
public static ThreadFactory defaultThreadFactory() {
    return new DefaultThreadFactory();
}
```

```JAVA
static class DefaultThreadFactory implements ThreadFactory {
    private static final AtomicInteger poolNumber = new AtomicInteger(1); // 表示当前是第几个线程池
    private final ThreadGroup group;
    private final AtomicInteger threadNumber = new AtomicInteger(1); // 表示当前是第几个线程
    private final String namePrefix;

    DefaultThreadFactory() {
        SecurityManager s = System.getSecurityManager();
        group = (s != null) ? s.getThreadGroup() :
        Thread.currentThread().getThreadGroup();
        namePrefix = "pool-" +
            poolNumber.getAndIncrement() +
            "-thread-";
    }

    public Thread newThread(Runnable r) {
        // 创建一个线程，执行任务r，线程名为namePrefix+当前线程个数
        Thread t = new Thread(group, r,
                              namePrefix + threadNumber.getAndIncrement(),
                              0);
        if (t.isDaemon())
            t.setDaemon(false);
        if (t.getPriority() != Thread.NORM_PRIORITY)
            t.setPriority(Thread.NORM_PRIORITY);
        return t;
    }
}
```







### 4、任务提交

```java
// 执行任务
void execute(Runnable command);

// 提交任务 task，用返回值 Future 获得任务执行结果
<T> Future<T> submit(Callable<T> task);

// 提交 tasks 中所有任务
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
    throws InterruptedException;

// 提交 tasks 中所有任务，带超时时间，如果一定时间内任务没有执行完，就把后续的取消掉
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
                              long timeout, TimeUnit unit)
    throws InterruptedException;

// 提交 tasks 中所有任务，哪个任务先成功执行完毕，返回此任务执行结果，其它任务取消
<T> T invokeAny(Collection<? extends Callable<T>> tasks)
    throws InterruptedException, ExecutionException;

// 提交 tasks 中所有任务，哪个任务先成功执行完毕，返回此任务执行结果，其它任务取消，带超时时间
<T> T invokeAny(Collection<? extends Callable<T>> tasks,
                long timeout, TimeUnit unit)
 throws InterruptedException, ExecutionException, TimeoutException;
```





#### 4.1 execute

> `execute`方法执行后无返回值，接收一个`Runnable`对象

```java
public static void main(String[] args) {
    ExecutorService pool = Executors.newFixedThreadPool(2);
    pool.execute(new Runnable() {
        @Override
        public void run() {
            System.out.println("running");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("end");
        }
    });
}
```





#### 4.2 submit

> 1、`submit`方法如果接收`Callable`参数，执行后是有返回值的，返回值就是`Callable`接口中`call`方法的返回值【接收`Runnable`类型任务，无返回值，通过`future.get`返回的是null】
>
> 2、返回值是`Future`类型，采用的是保护性暂停模式，即主线程通过`result.get()`获取结果时，获取不到，就调用`wait`等待，等任务执行完毕，在将主线程唤醒

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    ExecutorService pool = Executors.newFixedThreadPool(2);
    Future<String> result = pool.submit(new Callable<String>() {
        @Override
        public String call() throws Exception {
            Thread.sleep(2000);
            return "ok";
        }
    });
    System.out.println(result.get());
}
```







### 5、**关闭线程池**

#### 5.1 shutdown

`void shutdown();`

- 调用`shutdown()`方法后，线程池状态变为 `SHUTDOWN`

- 不会接收新任务，但已提交任务会执行完
- 此方法不会阻塞调用线程的执行，即如果主线程调用了`shutdown()`方法，但是线程池还有没执行完的任务，线程池会继续执行，但是主线程不会阻塞，会继续执行之后的代码



**shutdown源码**

```java
public void shutdown() {
    final ReentrantLock mainLock = this.mainLock;
    mainLock.lock();
    try {
        checkShutdownAccess();
        // 修改线程池状态
        advanceRunState(SHUTDOWN);
        // 打断空闲线程，使用interrupt
        interruptIdleWorkers();
        onShutdown(); // 扩展点 ScheduledThreadPoolExecutor
    } finally {
        mainLock.unlock();
    }
    // 尝试终结(没有运行的线程可以立刻终结，如果还有运行的线程也不会等，会继续执行，让没运行完的线程继续运行)
    tryTerminate();
}
```







#### 5.2 shutdownNow

`List<Runnable> shutdownNow();`

- 线程池状态变为 `STOP`

- 不会接收新任务，会将队列中的任务返回
- 并用 `interrupt` 的方式中断正在执行的任务



**shutdownNow源码**

```java
public List<Runnable> shutdownNow() {
    List<Runnable> tasks;
    final ReentrantLock mainLock = this.mainLock;
    mainLock.lock();
    try {
        checkShutdownAccess();
        // 修改线程池状态
        advanceRunState(STOP);
        // 打断所有线程
        interruptWorkers();
        // 获取队列中剩余任务
        tasks = drainQueue();
    } finally {
        mainLock.unlock();
    }
    // 尝试终结
    tryTerminate();
    return tasks; 
}
```





#### 5.3 cancel

通过`submit`方法向线程池提交一个任务时，会返回一个`Future`对象，可以调用`Future`对象的`cancel(boolean mayInterruptIfRunning)`方法将当前任务取消，其中`mayInterruptIfRunning`参数表示是否允许中断正在执行的任务。如果设置为true，表示任务正在执行，还是可以取消。如果为false，任务开始执行后就不会被中断



#### 5.4 其他方法

```java
// 不在 RUNNING 状态的线程池，此方法就返回 true
boolean isShutdown();

// 线程池状态是否是 TERMINATED
boolean isTerminated();

// 调用 shutdown 后，由于调用线程并不会等待所有任务运行结束，因此如果它想在线程池 TERMINATED 后做些事情，可以利用此方法等待
boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;
```







### 6、异步模式之工作线程

1、让有限的工作线程（Worker Thread）来轮流异步处理无限多的任务。也可以将其归类为分工模式，它的典型实现就是线程池，也体现了经典设计模式中的享元模式。

2、不同任务类型应该使用不同的线程池，这样能够避免饥饿，并能提升效率 

> 例如，如果一个餐馆的工人既要招呼客人（任务类型A），又要到后厨做菜（任务类型B）显然效率不高，分成服务员（线程池A）与厨师（线程池B）更合理





#### 6.1 饥饿

固定大小线程池会有饥饿现象 

**需求：**

> 1、两个工人是同一个线程池中的两个线程，他们要做的事情是：为客人点餐和到后厨做菜，这是两个阶段的工作 
>
> 2、客人点餐：必须先点完餐，等菜做好，上菜，在此期间处理点餐的工人必须等待 。后厨做菜
>
> 3、比如工人A 处理了点餐任务，接下来它要等着 工人B 把菜做好，然后上菜，这样没有问题。但现在同时来了两个客人，这个时候工人A 和工人B 都去处理点餐了，这时没人做饭了，产生饥饿现象
>
> 4、下边代码就可能产生饥饿现象【不是死锁】，两个线程都处理点餐，都在等待做菜

```java
public class TestStarvation {
    
    static final List<String> MENU = Arrays.asList("地三鲜", "宫保鸡丁", "辣子鸡丁", "烤鸡翅");
    static Random RANDOM = new Random();
    
    static String cooking() {
        return MENU.get(RANDOM.nextInt(MENU.size()));
    }
    
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        
        executorService.execute(() -> {
            log.debug("处理点餐...");
            Future<String> f = executorService.submit(() -> {
                log.debug("做菜");
                return cooking();
            });
            try {
                log.debug("上菜: {}", f.get());
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        });
        
        executorService.execute(() -> {
            log.debug("处理点餐...");
            Future<String> f = executorService.submit(() -> {
                log.debug("做菜");
                return cooking();
            });
            try {
                log.debug("上菜: {}", f.get());
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        });
    }
}
```







#### 6.2 解决饥饿

解决饥饿现象可以增加线程池的大小，不过不是根本解决方案，想要解决饥饿现象，需要针对不同的任务类型，采用不同的线程池

```java
public class TestStarvation {
    
    static final List<String> MENU = Arrays.asList("地三鲜", "宫保鸡丁", "辣子鸡丁", "烤鸡翅");
    static Random RANDOM = new Random();
    
    static String cooking() {
        return MENU.get(RANDOM.nextInt(MENU.size()));
    }
    
    public static void main(String[] args) {
        ExecutorService waiterPool = Executors.newFixedThreadPool(1);
        ExecutorService cookPool = Executors.newFixedThreadPool(1);
        
        waiterPool.execute(() -> {
            log.debug("处理点餐...");
            Future<String> f = cookPool.submit(() -> {
                log.debug("做菜");
                return cooking();
            });
            try {
                log.debug("上菜: {}", f.get());
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        });
        
        waiterPool.execute(() -> {
            log.debug("处理点餐...");
            Future<String> f = cookPool.submit(() -> {
                log.debug("做菜");
                return cooking();
            });
            try {
                log.debug("上菜: {}", f.get());
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        });
    }
}
```







### 7、线程池的大小

- 线程数量过少会导致程序不能充分地利用系统资源、容易导致饥饿 
- 线程数量过多会导致更多的线程上下文切换，占用更多内存 



**CPU密集型**

对于CPU密集型任务，通常采用 **cpu核数 + 1** 能够实现最优的 CPU 利用率，+1 是保证当线程由于页缺失故障（操作系统）或其它原因 

导致暂停时，额外的这个线程就能顶上去，保证 CPU 时钟周期不被浪费 



**I/O密集型**

CPU 不总是处于繁忙状态，例如，当执行业务计算时，这时候会使用 CPU 资源，但你执行 I/O 操作时、远程RPC 调用时，包括进行数据库操作时，这时候 CPU 就闲下来了，可以利用多线程提高它的利用率。 

> **经验公式**
>
> ```
> 线程数 = 核数 * 期望 CPU 利用率 * 总时间(CPU计算时间+等待时间) / CPU 计算时间
> ```
>
> 例如 4 核 CPU 计算时间是 50% ，其它等待时间是 50%，期望 cpu 被 100% 利用，套用公式 
>
> ```
> 4 * 100% * 100% / 50% = 8 
> ```
>
> 例如 4 核 CPU 计算时间是 10% ，其它等待时间是 90%，期望 cpu 被 100% 利用，套用公式 
>
> ```
> 4 * 100% * 100% / 10% = 40 
> ```







### 8、任务调度线程池

#### 8.1 Timer

> 在『任务调度线程池』功能加入之前，可以使用 `java.util.Timer` 来实现定时功能
>
> `Timer `的优点在于简单易用，但由于所有任务都是由同一个线程来调度，因此所有任务都是串行执行的，同一时间只能有一个任务在执行，前一个任务的延迟或异常都将会影响到之后的任务。

```java
public static void main(String[] args) {
    Timer timer = new Timer();
    
    TimerTask task1 = new TimerTask() {
        @Override
        public void run() {
            log.debug("task 1");
            sleep(2);
        }
    };
    
    TimerTask task2 = new TimerTask() {
        @Override
        public void run() {
            log.debug("task 2");
        }
    };
    
    log.debug("start...");
    // 使用 timer 添加两个任务，希望它们都在 1s 后执行
    // 但由于 timer 内只有一个线程来顺序执行队列中的任务，因此『任务1』的延时，影响了『任务2』的执行
    // 甚至如果task1出异常停止后,task2都不会执行
    timer.schedule(task1, 1000);
    timer.schedule(task2, 1000);
}
```







#### 8.2 ScheduledThreadPool

> `ScheduledThreadPool` 和 `SingleThreadScheduledExecutor` 都是使用的无界的延迟阻塞队列`DelayedWorkQueue`，任务队列最大长度为 `Integer.MAX_VALUE`，可能堆积大量的请求，从而导致 OOM。

**源码分析**

```java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}

public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```





**schedule**

> 延迟执行任务

```java
public ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit);
```

- `command`：执行的任务
- `delay`：延迟的时间
- `unit`：时间单位

```java
public static void main(String[] args) {
    ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);

    // 添加两个任务，希望它们都在 1s 后执行
    executor.schedule(() -> {
        System.out.println("任务1，执行时间：" + new Date());
        try { 
            Thread.sleep(2000); 
        } catch (InterruptedException e) { }
    }, 1000, TimeUnit.MILLISECONDS);

    executor.schedule(() -> {
        System.out.println("任务2，执行时间：" + new Date());
    }, 1000, TimeUnit.MILLISECONDS);
}
```





**scheduleAtFixedRate**

> 延迟执行任务，同时，每隔`period`执行一次任务
>
> 如果任务执行时间超过了间隔时间，下一次任务会在上一个任务结束后立马执行。例如，任务执行需要花费2s，但是任务每隔1s执行一次，所以最终效果就是任务执行完会立马执行下一个任务

```java
public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                              long initialDelay,
                                              long period,
                                              TimeUnit unit);
```

- `command`：执行的任务
- `initialDelay`：延迟时间
- `period`：每隔`period`时间执行一次
- `unit`：时间单位

```java
public static void main(String[] args) {
    ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);
    log.debug("start...");

    pool.scheduleAtFixedRate(() -> {
        log.debug("running...");
        sleep(2);
    }, 1, 1, TimeUnit.SECONDS);
}
```





**scheduleWithFixedDelay**

> 延迟执行任务，同时，每隔`period`执行一次任务
>
> 如果任务执行时间超过了间隔时间，下一次任务会在上一个任务执行结束后再间隔`delay`时间执行。例如，任务执行需要花费2s，但是任务每隔1s执行一次，所以最终效果就是任务执行完会间隔1s再执行下一个任务

```java
public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
                                                 long initialDelay,
                                                 long delay,
                                                 TimeUnit unit);
```

- `command`：执行的任务
- `initialDelay`：延迟时间
- `period`：每隔`period`时间执行一次
- `unit`：时间单位

```java
public static void main(String[] args) {
    ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);
    log.debug("start...");
    pool.scheduleWithFixedDelay(()-> {
        log.debug("running...");
        sleep(2);
    }, 1, 1, TimeUnit.SECONDS);
}
```







### 9、正确处理任务执行的异常

> 我们在使用线程池执行任务时，如果任务抛出异常，是不会被捕获的



#### 9.1 手动捕获异常

对于线程池中执行的任务，我们可以使用`try...catch`手动捕获异常

```java
public static void main(String[] args) {
    ExecutorService pool = Executors.newFixedThreadPool(1);
    pool.submit(() -> {
        try {
            System.out.println("task1");
            int i = 1 / 0;
        } catch (Exception e) {
            System.out.println(e);
        }
    });
}
```





#### 9.2 使用Future

如果线程池执行的任务，没有抛出异常，使用`future.get()`获取的就是任务的返回值。如果抛出异常，获取的就是异常信息

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    ExecutorService pool = Executors.newFixedThreadPool(1);
    Future<String> future = pool.submit(() -> {
        System.out.println("task1");
        int i = 1 / 0;
        return "ok";
    });
    System.out.println(future.get());
}
```







### 10、Tomcat线程池

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262151754.png)

- `LimitLatch` 用来限流，可以控制最大连接个数，类似` JUC` 中的 `Semaphore `
- `Acceptor` 只负责接收新的 `socket `连接
- `Poller` 只负责监听 `socket channel `是否有可读的 I/O 事件
- 一旦可读，封装一个任务对象`socketProcessor`，提交给 Executor 线程池处理 
- `Executor `线程池中的工作线程最终负责处理请求 





**扩展了ThreadPoolExecutor**

> Tomcat 线程池扩展了` ThreadPoolExecutor`，行为稍有不同。如果总线程数达到 `maximumPoolSize `，这时不会立刻抛 `RejectedExecutionException` 异常 ，而是再次尝试将任务放入队列，如果还失败，才抛出 `RejectedExecutionException` 异常 





 **tomcat-7.0.42源码**

```java
public void execute(Runnable command, long timeout, TimeUnit unit) {
    submittedCount.incrementAndGet();
    try { 
        super.execute(command); // 先执行父类的execute方法
    } catch (RejectedExecutionException rx) { // 如果父类抛出异常，这里捕获然后处理
        if (super.getQueue() instanceof TaskQueue) {
            final TaskQueue queue = (TaskQueue)super.getQueue(); // 拿到任务队列
            try {
                if (!queue.force(command, timeout, unit)) { // 尝试再次将任务放入队列，如果失败抛出异常
                    submittedCount.decrementAndGet();
                    throw new RejectedExecutionException("Queue capacity is full.");
                }
            } catch (InterruptedException x) {
                submittedCount.decrementAndGet();
                Thread.interrupted();
                throw new RejectedExecutionException(x);
            }
        } else {
            submittedCount.decrementAndGet();
            throw rx;
        }
    }
}
```

```java
public boolean force(Runnable o, long timeout, TimeUnit unit) throws InterruptedException {
    if ( parent.isShutdown() ) 
        throw new RejectedExecutionException(
        "Executor not running, can't force a command into the queue"
    );
    return super.offer(o,timeout,unit); //forces the item onto the queue, to be used if the task 
    is rejected
}
```

​	



**Connector配置**

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262158632.png)![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262159191.png)

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262159160.png) 











## 七、AQS

> `AQS`全称是 `AbstractQueuedSynchronizer`，是阻塞式锁和相关的同步器工具的框架 



**1、state属性**

- 用 state 属性来表示资源的状态（分独占模式和共享模式），子类需要定义如何维护这个状态，控制如何获取锁和释放锁 
  - `getState` ： 获取 state 状态 
  - `setState` ： 设置 state 状态 
  - `compareAndSetState `： cas 机制设置 state 状态 
  - 独占模式是只有一个线程能够访问资源，而共享模式可以允许多个线程访问资源

- state 使用 volatile 配合 cas 保证其修改时的原子性 

**2、等待队列**

- 提供了基于 FIFO 的等待队列，类似于 Monitor 的 EntryList 
- 设计时借鉴了 CLH 队列，它是一种单向无锁队列

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262316541.png) 

**3、条件变量**

- 条件变量来实现等待、唤醒机制，支持多个条件变量，类似于 Monitor 的 WaitSet 







### 1、ReentrantLock 原理

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262319147.png) 





#### 1.1 非公平锁实现原理

##### 获取锁

1、`ReentrantLock`默认为非公平锁实现，`NonfairSync` 继承自 AQS 

```java
public ReentrantLock() {
   sync = new NonfairSync();
}
```



2、执行`lock`方法加锁，这里使用的是非公平锁的`lock`方法

```java
public void lock() {
    sync.lock();
}
```

```java
static final class NonfairSync extends Sync {
    private static final long serialVersionUID = 7316153563782823691L;

    /**
     * Performs lock.  Try immediate barge, backing up to normal
     * acquire on failure.
     */
    final void lock() {
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);
    }

    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}
```

通过CAS的方式尝试将`state`从0修改为1

- 如果修改成功，就将锁的拥有者设置为当前线程

   <img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401262325266.png" style="zoom:80%;" />

- 如果修改失败，执行`acquire(1)`方法。在`acquire`方法中，通过`tryAcquire`方法尝试再获取一次锁，如果获取成功，就退出当前方法

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

```java
// 上边NonfairSync类的tryAcquire方法
protected final boolean tryAcquire(int acquires) {
    return nonfairTryAcquire(acquires);
}
```

```java
final boolean nonfairTryAcquire(int acquires) {
    // 拿到当前线程
    final Thread current = Thread.currentThread();
    // 获取state状态
    int c = getState();
    // 如果state状态为0，表示锁空闲，可以尝试获取锁
    if (c == 0) {
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    // 如果锁不是空闲的，判断锁的拥有者是否是当前线程
    else if (current == getExclusiveOwnerThread()) {
        // 是的话，就将当前锁的重入次数+1，然后修改state的状态为锁重入次数，返回true
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    // 获取锁失败
    return false;
}
```

- `acquire`方法获取锁失败，会继续执行`acquireQueued(addWaiter(Node.EXCLUSIVE), arg)`方法

```java
private Node addWaiter(Node mode) {
    // 为当前线程创建一个Node
    Node node = new Node(Thread.currentThread(), mode);
    // 拿到队列尾结点
    Node pred = tail;
    // 如果尾结点不为空，将当前节点的上一个节点设置为尾结点，同时通过cas的方式将当前节点设置为尾结点，然后返回当前节点
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    // 如果尾结点为空表示当前队列为空，执行enq方法
    enq(node);
    return node;
}

private Node enq(final Node node) {
    for (;;) {
        // 拿到尾结点
        Node t = tail;
        // 如果尾结点为空，就创建一个空节点作为头结点，并且尾结点也指向该节点，然后继续循环
        if (t == null) { // Must initialize
            if (compareAndSetHead(new Node()))
                tail = head;
        // 下次循环时，尾结点就不为空，将当前节点的前驱设置为t，然后通过cas的方式将当前节点设置为尾结点，并返回
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

```java
// 这里的node就是通过addWaiter()方法返回的节点，此时arg是1
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            // 拿到当前节点的前驱节点p
            final Node p = node.predecessor();
            // 如果p为头结点，表示当前节点是队列中第一个节点，那么就再次尝试获取锁，如果获取锁成功，就将当前节点设置为头结点，将p节点从队列中断开
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 如果p不是头结点，或者获取锁失败
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

```java
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    // 获取前驱节点的状态，默认是0
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL) // 如果ws==-1，就返回true，-1表示节点正在阻塞，他可以将下一个节点唤醒，既然前驱都阻塞了，那么返回true，当前节点也阻塞
        return true;
    if (ws > 0) { // 如果ws>0，表示取消状态，就一直往前找，删除前边所有取消的节点，直到找到ws<=0的节点，然后将该节点的后继设置为node
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else { // 如果ws==0，表示前驱节点还没有阻塞，通过cas的方式将ws设置为-1
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}

// 如果shouldParkAfterFailedAcquire方法返回true表示node节点可以被上一个节点唤醒，那么执行parkAndCheckInterrupt方法，通过part暂停node节点的线程
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}
```





##### 释放锁

1、释放锁调用的是`sync`的`release(1)`方法

```java
public void unlock() {
    sync.release(1);
}
```

```java
public final boolean release(int arg) {
    // 如果tryRelease方法返回true，表示释放锁后，锁空闲
    if (tryRelease(arg)) {
        // 拿到head节点，如果head不为空，并且头节点状态不为0，那么就唤醒头结点的下一个节点
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    // 锁重入次数-1，锁还是被占有的，返回false
    return false;
}

protected final boolean tryRelease(int releases) {
    // getState是获取state状态，如果锁是重入的，那么state>1，c就是当前线程释放一次锁时，state状态
    int c = getState() - releases;
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    // 如果c==0，表示当前线程释放锁后，锁空闲，就将锁的拥有者设置为null，并返回true
    if (c == 0) {
        free = true;
        setExclusiveOwnerThread(null);
    }
    // 如果c!=0，表示锁重入次数-1，返回false
    setState(c);
    return free;
}
```

```java
private void unparkSuccessor(Node node) {
    // 拿到当前头结点的状态
    int ws = node.waitStatus;
    if (ws < 0) // 如果ws<0，那么将状态设置为0
        compareAndSetWaitStatus(node, ws, 0);

    // 拿到头结点的下一个节点
    Node s = node.next;
    // 如果下一个需要唤醒的节点为空，或者状态>0，那么就从队尾向队头遍历，找到最前边一个状态<=0的节点，然后唤醒该节点
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    // 通过unpark将下一个节点唤醒
    if (s != null)
        LockSupport.unpark(s.thread);
}
```



2、唤醒的线程尝试获取锁

- 当前线程开始时通过`park`被暂停的，现在通过`unpark`被唤醒，那么当前线程的节点就是头结点后的第一个节点，会循环再次通过`tryAcquire(arg)`去尝试获取锁，如果获取锁成功，就将头结点断开，将当前节点设置为头结点。
- 如果此时新来一个线程，他抢到了锁，当前被唤醒的线程获取锁失败，他会再次被`park`暂停

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                // 返回中断标记 false
                return interrupted;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```







#### 1.2 可重入原理

```java
static final class NonfairSync extends Sync {
    // ...
    
    // Sync 继承过来的方法
    final boolean nonfairTryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        // 如果已经获得了锁, 线程还是当前线程, 表示发生了锁重入
        else if (current == getExclusiveOwnerThread()) {
            // state++
            int nextc = c + acquires;
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
    
    // Sync 继承过来的方法
    protected final boolean tryRelease(int releases) {
        // state-- 
        int c = getState() - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread())
            throw new IllegalMonitorStateException();
        boolean free = false;
        // 支持锁重入, 只有 state 减为 0, 才释放成功
        if (c == 0) {
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c);
        return free;
    }
}
```





#### 1.3 可打断原理

##### 不可打断

> 不可打断是指当前线程被打断了，但是他还需要去获取锁，如果获取锁失败，继续park，获取锁成功就返回true，然后将当前线程打断标记设为true

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 如果当前线程可以被暂停，那么就执行parkAndCheckInterrupt方法，调用park将当前线程暂停
            // 如果当前线程被打断了，那么会执行interrupted = true，然后该线程会继续去尝试获取锁，如果获取锁失败，继续park，如果获取锁成功，就返回true
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

```java
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    // 如果当前线程被打断，那么Thread.interrupted()会返回true，同时会清除打断标记
    // 因为如果打断标记为true，park会失效，这里清除打断标记是为了使当前线程可以再次被park
    return Thread.interrupted();
}
```

```java
public final void acquire(int arg) {
    // 这里调用的acquireQueued方法，如果返回true，表示当前线程获取锁成功，但是当前线程是被打断的，并且打断标记已经清除，因此需要执行selfInterrupt()
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}

// 将当前线程打断标记重新设置为true
static void selfInterrupt() {
    Thread.currentThread().interrupt();
}
```





##### 可打断

`lockInterruptibly`方法获取锁是可以被打断的，当线程在AQS队列等待时被打断，会直接抛出异常，然后退出

```java
public void lockInterruptibly() throws InterruptedException {
    sync.acquireInterruptibly(1);
}
```

```java
public final void acquireInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    // 当前线程如果获取锁失败，就执行doAcquireInterruptibly方法
    if (!tryAcquire(arg))
        doAcquireInterruptibly(arg);
}
```

```java
private void doAcquireInterruptibly(int arg)
    throws InterruptedException {
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return;
            }
            // 如果当前线程可以被暂停，那么就执行parkAndCheckInterrupt方法，调用park将当前线程暂停
            // 如果当前线程被打断了，就直接抛出异常，然后退出
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```







#### 1.4 公平锁实现原理

> 当前线程尝试获取锁时，会先判断AQS队列中是否有线程，如果有，就获取锁失败，否则才去获取锁

```java
final void lock() {
    acquire(1);
}
```

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

```java
// FairSync类中的tryAcquire实现
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        // 如果锁是空闲的，同时AQS队列中没有线程节点，那么当前线程尝试获取锁
        if (!hasQueuedPredecessors() &&
            compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    // 锁重入
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    // 锁空闲，但是AQS队列有线程  或者 锁被占用，都会返回false
    return false;
}
```

```java
public final boolean hasQueuedPredecessors() {
    // The correctness of this depends on head being initialized
    // before tail and on head.next being accurate if the current
    // thread is first in queue.
    Node t = tail; // Read fields in reverse initialization order
    Node h = head;
    Node s;
    // 如果头结点和尾结点不相同，并且头结点的下一个节点为空，或者当前线程和队列中第一个线程不是同一个线程，就返回true
    return h != t &&
        ((s = h.next) == null || s.thread != Thread.currentThread());
}
```



**公平锁性能不如非公平锁**

- 公平锁获取锁时，如果有线程在等待，那么它会去AQS队列尾部等待，通过`park`方法暂停。当锁空闲且当前线程为AQS头部节点时，在通过`unpark`方法将当前线程唤醒，在整个过程中，线程会从运行状态切换到休眠状态，再从休眠状态恢复成运行状态，但线程每次休眠和恢复都需要从用户态转换成内核态，而这个状态的转换是比较慢的，所以公平锁的执行速度会比较慢。
- 非公平锁在线程获取锁时，会先通过CAS尝试获取锁，如果获取成功就直接拥有锁，如果获取锁失败才会进入等待队列，等待下次尝试获取锁。这样做的好处是，获取锁不用遵循先到先得的规则，从而避免了线程休眠和恢复的操作，这样就加速了程序的执行效率。





#### 1.5 条件变量原理

##### await

每个条件变量就对应着一个等待队列，其实现类是` ConditionObject` 

调用`condition.await()`方法

- 调用`addConditionWaiter`将当前线程创建一个节点，加入到条件变量的等待队列中
- 通过`fullyRelease`方法释放锁，唤醒AQS队列中第一个线程
- 当前线程被`park`阻塞
- 如果线程退出等待队列后，会再次会去竞争锁

```java
public final void await() throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    // 调用addConditionWaiter方法创建一个Node节点，将该节点放到条件变量队列尾部，返回该节点
    Node node = addConditionWaiter();
    // fullyRelease方法，拿到当前锁的state状态，fullyRelease是因为当前线程可能会重入锁，所以要全部释放
    int savedState = fullyRelease(node);
    int interruptMode = 0;
    // 如果该节点还没有转移至 AQS 队列, 阻塞
    while (!isOnSyncQueue(node)) {
        LockSupport.park(this);
        // 如果被打断, 退出等待队列
        if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
            break;
    }
    // 退出等待队列后, 该节点尝试获得锁
    if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
        interruptMode = REINTERRUPT;
    // 所有已取消的 Node 从队列链表删除
    if (node.nextWaiter != null) // clean up if cancelled
        unlinkCancelledWaiters();
    if (interruptMode != 0)
        reportInterruptAfterWait(interruptMode);
}
```

```java
private Node addConditionWaiter() {
    // 拿到条件变量队列中的尾结点
    Node t = lastWaiter;
    // 如果不为空，并且他的状态不是-2，就将队列中已经取消的节点删除
    if (t != null && t.waitStatus != Node.CONDITION) {
        unlinkCancelledWaiters();
        t = lastWaiter;
    }
    // 为当前线程创建一个Node节点，状态为-2，表示等待
    Node node = new Node(Thread.currentThread(), Node.CONDITION);
    // 如果当前队列为空，就将当前node设为头结点，否则将node放到队列尾部
    if (t == null)
        firstWaiter = node;
    else
        t.nextWaiter = node;
    lastWaiter = node;
    return node;
}
```

```java
final int fullyRelease(Node node) {
    boolean failed = true;
    try {
        // 拿到锁的状态
        int savedState = getState();
        // 如果释放锁成功，failed置位false，同时返回锁的状态
        if (release(savedState)) {
            failed = false;
            return savedState;
        } else {
            throw new IllegalMonitorStateException();
        }
    } finally {
        // 如果释放锁失败，将当前线程的waitStatus设为1，表示取消
        if (failed)
            node.waitStatus = Node.CANCELLED;
    }
}

public final boolean release(int arg) {
    // 释放锁，将锁的拥有者设为null，并将锁的状态设为0
    if (tryRelease(arg)) {
        Node h = head;
        // 唤醒AQS队列中第一个线程
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```





##### signal

1、拿到条件变量等待队列中第一个节点，执行`doSignal`方法唤醒该节点

2、将该节点从等待队列断开，并加入AQS队列尾部进行等待，如果成功就直接退出，如果失败就循环唤醒下一个节点

3、将AQS队列中原来的尾结点状态修改为-1，当前节点作为尾结点，状态为0

```java
public final void signal() {
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    // 拿到条件变量的等待队列中第一个节点
    Node first = firstWaiter;
    // 唤醒first线程
    if (first != null)
        doSignal(first);
}
```

```java
private void doSignal(Node first) {
    do {
        // 如果first节点的下一个节点为空，表示当前线程被唤醒后，等待队列为空，就将尾结点置空
        if ( (firstWaiter = first.nextWaiter) == null)
            lastWaiter = null;
        // 将first节点从队列中断开
        first.nextWaiter = null;
    } while (!transferForSignal(first) &&
             (first = firstWaiter) != null);
}
```

```java
final boolean transferForSignal(Node node) {
    // 将当前节点通过cas方式将状态从-2修改为0，如果修改失败，返回false
    if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
        return false;

    // 将当前节点加入AQS队列尾部，同时返回他的上一个节点，即原来的尾结点
    Node p = enq(node);
    // 拿到p的状态
    int ws = p.waitStatus;
    // 如果p的状态大于0，或者将状态修改为-1失败，就将当前线程唤醒
    // 否则，将p的状态修改为-1，用来唤醒当前节点
    if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
        LockSupport.unpark(node.thread);
    return true;
}
```









### 2、读写锁

#### 2.1 **ReentrantReadWriteLock**

当读操作远远高于写操作时，这时候使用 读写锁 让 **读-读** 可以并发，提高性能

```java
ReentrantReadWriteLock rw = new ReentrantReadWriteLock();  // 创建读写锁
ReentrantReadWriteLock.ReadLock r = rw.readLock();  // 拿到读锁
ReentrantReadWriteLock.WriteLock w = rw.writeLock();  // 拿到写锁
```

读读并发、读写互斥、写写互斥





**注意**

1、读锁不支持条件变量，写锁支持

2、重入时不支持升级：即持有读锁的情况下去获取写锁，会导致获取写锁永久等待

```java
r.lock();
try {
    // ...
    w.lock();
    try {
        // ...
    } finally{
        w.unlock();
    }
} finally{
    r.unlock();
}
```

3、重入时支持降级：即持有写锁的情况下去获取读锁

```java
class CachedData {
    Object data;
    // 是否有效，如果失效，需要重新计算 data
    volatile boolean cacheValid;
    final ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
    void processCachedData() {
        rwl.readLock().lock();
        if (!cacheValid) {
            // 获取写锁前必须释放读锁
            rwl.readLock().unlock();
            rwl.writeLock().lock();
            try {
                // 判断是否有其它线程已经获取了写锁、更新了缓存, 避免重复更新
                if (!cacheValid) {
                    data = ...
                    cacheValid = true;
                }
                // 降级为读锁, 释放写锁, 这样能够让其它线程读取缓存
                // 如果释放写锁后在获取读锁，可能会出现，另一个线程先获取写锁，修改了数据，导致获取的数据不一致
                rwl.readLock().lock();
            } finally {
                rwl.writeLock().unlock();
            }
        }
        // 自己用完数据, 释放读锁 
        try {
            use(data);
        } finally {
            rwl.readLock().unlock();
        }
    }
}
```









#### 2.2 读写锁原理

读写锁用的是同一个 Sync

同步器，因此等待队列、state 等也是同一个 ，但是写锁状态占了 state 的低 16 位，而读锁使用的是 state 的高 16 位 

##### 写锁加锁

`w.lock()`

1、调用`tryAcquire`方法尝试获取锁

- 如果当前锁的状态不为0，但是低16位状态为0（没有写锁），即有读锁，或者当前线程不是锁的拥有者，那么获取锁失败
- 否则，去获取锁

2、`tryAcquire`方法获取锁失败，会执行`acquireQueued(addWaiter(Node.EXCLUSIVE), arg)`

- `addWaiter`方法为当前线程创建一个Node，模式为独占锁，加入到AQS队列尾部
- `acquireQueued`方法，如果当前节点的前驱为头结点，那么再次尝试获取锁，如果获取锁失败，就park暂停

```java
public void lock() {
    sync.acquire(1);
}
```

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&  // tryAcquire获取锁失败，就将当前线程关联到一个Node上，模式为独占模式，进入AQS队列等待
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

```java
protected final boolean tryAcquire(int acquires) {
    Thread current = Thread.currentThread();
    // 拿到锁的状态
    int c = getState();
    // 拿到低16位的状态，即写锁状态
    int w = exclusiveCount(c);
    // c!=0表示有读锁或者写锁
    if (c != 0) {
        // w==0表示没人占据写锁，说明读锁被占据，或者当前线程不是获取锁的线程，那么获取写锁失败
        if (w == 0 || current != getExclusiveOwnerThread())
            return false;
        if (w + exclusiveCount(acquires) > MAX_COUNT)
            throw new Error("Maximum lock count exceeded");
        // 锁重入
        setState(c + acquires);
        return true;
    }
    // 如果写锁阻塞或者修改状态失败，那么获取锁失败，非公平状态下，写锁不会阻塞
    if (writerShouldBlock() ||
        !compareAndSetState(c, c + acquires))
        return false;
    setExclusiveOwnerThread(current);
    return true;
}
```

```java
private Node addWaiter(Node mode) {
    // 为当前线程创建一个Node节点，并加入到AQS队列尾部
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    enq(node);
    return node;
}
```

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            // 拿到当前节点的上一个节点p
            final Node p = node.predecessor();
            // 如果p是头结点，那么再次尝试获取锁，如果获取成功，就将该节点从AQS队列移除
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 否则，park暂停
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```





##### 读锁加锁

`r.lock()`

1、`tryAcquireShared`方法尝试获取锁

- 拿到锁的低16位状态，即写锁状态，如果不为0，同时锁的拥有者不是当前线程，那么获取锁失败
- 如果读锁不被阻塞 并且 读锁获取次数小于最大值 并且 修改状态成功 那么就获取锁
  - 非公平锁的实现中，如果AQS中第一个等待线程是写锁，那么读锁就被阻塞

2、`tryAcquireShared`获取锁失败，调用`doAcquireShared`方法

- `addWaiter`方法将当前线程，创建一个Node节点，模式为共享，加入AQS队列
- 如果当前节点的前驱为头结点，那么再次尝试获取锁，如果获取锁失败，就park暂停

```java
public void lock() {
    sync.acquireShared(1);
}
```

```java
public final void acquireShared(int arg) {
    /*
    	tryAcquireShared 返回值表示
        ● -1 表示失败
        ● 0 表示成功，但后继节点不会继续唤醒 
        ● 正数表示成功，而且数值是还有几个后继节点需要唤醒，读写锁返回 1
    */
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
```

```java
protected final int tryAcquireShared(int unused) {
    Thread current = Thread.currentThread();
    int c = getState();
    // 拿到当前锁的低16位状态，如果不为0，表示有写锁，并且拥有者不是当前线程，那么获取锁失败
    if (exclusiveCount(c) != 0 &&
        getExclusiveOwnerThread() != current)
        return -1;
    int r = sharedCount(c); // r是高16位状态
    // 如果读锁不被阻塞 并且 读锁获取次数小于最大值 并且 修改状态成功 那么就获取锁
    if (!readerShouldBlock() &&
        r < MAX_COUNT &&
        compareAndSetState(c, c + SHARED_UNIT)) {
        if (r == 0) {
            firstReader = current;
            firstReaderHoldCount = 1;
        } else if (firstReader == current) {
            firstReaderHoldCount++;
        } else {
            HoldCounter rh = cachedHoldCounter;
            if (rh == null || rh.tid != getThreadId(current))
                cachedHoldCounter = rh = readHolds.get();
            else if (rh.count == 0)
                readHolds.set(rh);
            rh.count++;
        }
        return 1;
    }
    return fullTryAcquireShared(current);
}

// 非公平锁的实现，readerShouldBlock，如果AQS队列第一个线程是写锁，那么返回true，否则返回false
final boolean readerShouldBlock() {
    return apparentlyFirstQueuedIsExclusive();
}
final boolean apparentlyFirstQueuedIsExclusive() {
    Node h, s;
    return (h = head) != null &&
        (s = h.next)  != null &&
        !s.isShared()         &&
        s.thread != null;
}
```

```java
private void doAcquireShared(int arg) {
    // 将当前线程关联到一个 Node 对象上, 模式为共享模式
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            // 获取node的前一个节点p
            final Node p = node.predecessor();
            // 如果p为头结点，就再次尝试获取锁，如果获取锁成功，就将该节点从AQS队列移除
            if (p == head) {
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    if (interrupted)
                        selfInterrupt();
                    failed = false;
                    return;
                }
            }
            // 否则，park暂停
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```





##### 写锁释放

`w.unlock()`

1、`tryRelease`释放写锁

- 获取释放写锁后state状态，如果低16位为0，表示写锁被清空，将写锁拥有者设为null，并返回true

2、如果`tryRelease`返回true，就继续执行`unparkSuccessor`方法唤醒AQS队列中第一个未失效的节点

3、如果唤醒的是一个读锁节点，该节点在`doAcquireShared`方法中被唤醒

- 该节点尝试获取锁，如果获取锁失败，继续park
- 如果获取锁成功，还会执行`setHeadAndPropagate`方法

4、`setHeadAndPropagate`方法中，会将AQS队列头部想要获取读锁的线程全都唤醒，直到遇到写锁线程

```java
public void unlock() {
    sync.release(1);
}
```

```java
public final boolean release(int arg) {
    // 如果释放写锁成功，唤醒AQS队列中第一个线程
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}

protected final boolean tryRelease(int releases) {
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    // 获取释放锁后的状态
    int nextc = getState() - releases;
    // 如果低16位为0，free为true，表示没有写锁
    boolean free = exclusiveCount(nextc) == 0;
    // 如果没有写锁，就将写锁拥有者设为null
    if (free)
        setExclusiveOwnerThread(null);
   	// 修改状态
    setState(nextc);
    return free;
}
```

```java
private void unparkSuccessor(Node node) {
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);
    // 获取头结点的下一个节点
    Node s = node.next;
    // 拿到第一个未失效的节点
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    // 将该节点唤醒
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

```java
private void setHeadAndPropagate(Node node, int propagate) {
    Node h = head; // Record old head for check below
    setHead(node);
    if (propagate > 0 || h == null || h.waitStatus < 0 ||
        (h = head) == null || h.waitStatus < 0) {
        // 如果该节点的下一个节点要获取读锁，那么唤醒它
        Node s = node.next;
        if (s == null || s.isShared())
            doReleaseShared();
    }
}
```







##### 读锁释放

`r.unlock()`

1、`tryReleaseShared`释放读锁，如果释放完后state为0，表示读锁全部释放，返回true

2、如果`tryReleaseShared`方法返回true，就会执行`doReleaseShared`方法

- 如果头结点状态为-1，就先置位0，避免下一个线程被多次唤醒，然后唤醒下一个线程

```java
public void unlock() {
    sync.releaseShared(1);
}
```

```java
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```

```java
protected final boolean tryReleaseShared(int unused) {
    Thread current = Thread.currentThread();
    if (firstReader == current) {
        // assert firstReaderHoldCount > 0;
        if (firstReaderHoldCount == 1)
            firstReader = null;
        else
            firstReaderHoldCount--;
    } else {
        HoldCounter rh = cachedHoldCounter;
        if (rh == null || rh.tid != getThreadId(current))
            rh = readHolds.get();
        int count = rh.count;
        if (count <= 1) {
            readHolds.remove();
            if (count <= 0)
                throw unmatchedUnlockException();
        }
        --rh.count;
    }
    // 拿到释放读锁后state状态，如果state==0，返回true
    for (;;) {
        int c = getState();
        int nextc = c - SHARED_UNIT;
        if (compareAndSetState(c, nextc))
            return nextc == 0;
    }
}
```

```java
private void doReleaseShared() {
    for (;;) {
        Node h = head;
        // 如果头结点不为空，且不等于尾结点，就获取头结点状态，如果是-1，表示可以唤醒下一个节点，那么先将状态置位0，防止 unparkSuccessor 被多次执行
        if (h != null && h != tail) {
            int ws = h.waitStatus;
            if (ws == Node.SIGNAL) {
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                    continue;            // loop to recheck cases
                // 唤醒下一个线程
                unparkSuccessor(h);
            }
            // 如果已经是 0 了，改为 -3，用来解决传播性
            else if (ws == 0 &&
                     !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                continue;                // loop on failed CAS
        }
        if (h == head)                   // loop if head changed
            break;
    }
}
```







#### 2.3 StampedLock

该类自 JDK 8 加入，是为了进一步优化读性能，它的特点是在使用读锁、写锁时都必须配合【戳】使用加解读锁 ，类似乐观锁中的版本号法

```java
// 加解读锁
long stamp = lock.readLock();
lock.unlockRead(stamp);

// 加解写锁
long stamp = lock.writeLock();
lock.unlockWrite(stamp);
```





**乐观读-锁升级**

乐观读，`StampedLock` 支持 `tryOptimisticRead()` 方法（乐观读），读取完毕后需要做一次 **戳校验**， 如果校验通过，表示这期间没有写操作，数据可以安全使用，如果校验没通过，需要重新获取读锁，保证数据安全。



**示例**

```java
class DataContainerStamped {
    
    private int data;
    private final StampedLock lock = new StampedLock();
    
    public DataContainerStamped(int data) {
        this.data = data;
    }
    
    public int read(int readTime) {
        long stamp = lock.tryOptimisticRead();
        log.debug("optimistic read locking...{}", stamp);
        sleep(readTime);
        if (lock.validate(stamp)) {
            log.debug("read finish...{}, data:{}", stamp, data);
            return data;
        }
        // 锁升级 - 读锁
        log.debug("updating to read lock... {}", stamp);
        try {
            stamp = lock.readLock();
            log.debug("read lock {}", stamp);
            sleep(readTime);
            log.debug("read finish...{}, data:{}", stamp, data);
            return data;
        } finally {
            log.debug("read unlock {}", stamp);
            lock.unlockRead(stamp);
        }
    }
    
    public void write(int newData) {
        long stamp = lock.writeLock();
        log.debug("write lock {}", stamp);
        try {
            sleep(2);
            this.data = newData;
        } finally {
            log.debug("write unlock {}", stamp);
            lock.unlockWrite(stamp);
        }
    }
}
```



**测试**

```java
public static void main(String[] args) {
    DataContainerStamped dataContainer = new DataContainerStamped(1);
    new Thread(() -> {
        dataContainer.read(1);
    }, "t1").start();
    
    sleep(0.5);
    new Thread(() -> {
        dataContainer.read(0);
    }, "t2").start();
}

15:58:50.217 c.DataContainerStamped [t1] - optimistic read locking...256 
15:58:50.717 c.DataContainerStamped [t2] - optimistic read locking...256 
15:58:50.717 c.DataContainerStamped [t2] - read finish...256, data:1 
15:58:51.220 c.DataContainerStamped [t1] - read finish...256, data:1
```

```java
public static void main(String[] args) {
    DataContainerStamped dataContainer = new DataContainerStamped(1);
    
    new Thread(() -> {
        dataContainer.read(1);
    }, "t1").start();
    
    sleep(0.5);
    new Thread(() -> {
        dataContainer.write(100);
    }, "t2").start();
}
15:57:00.219 c.DataContainerStamped [t1] - optimistic read locking...256 
15:57:00.717 c.DataContainerStamped [t2] - write lock 384 
15:57:01.225 c.DataContainerStamped [t1] - updating to read lock... 256 
15:57:02.719 c.DataContainerStamped [t2] - write unlock 384 
15:57:02.719 c.DataContainerStamped [t1] - read lock 513 
15:57:03.719 c.DataContainerStamped [t1] - read finish...513, data:1000 
15:57:03.719 c.DataContainerStamped [t1] - read unlock 513
```





**注意** 

`StampedLock` 不支持条件变量 

`StampedLock` 不支持锁重入







### 3、Semaphore

信号量，用来限制能同时访问共享资源的线程上限



**示例**

> 下边代码中，创建的`Semaphore`有3个信号量，表示最多有3个线程可以获取共享资源

```java
public static void main(String[] args) {
    // 1. 创建 semaphore 对象
    Semaphore semaphore = new Semaphore(3);
    // 2. 10个线程同时运行
    for (int i = 0; i < 10; i++) {
        new Thread(() -> {
            // 3. 获取许可
            try {
                semaphore.acquire();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            try {
                log.debug("running...");
                sleep(1);
                log.debug("end...");
            } finally {
                // 4. 释放许可
                semaphore.release();
            }
        }).start();
    }
 }
```







#### 3.1 Semaphore原理

创建`Semaphore`时，指定的`permits`参数其实就是AQS中的state的值

```java
public Semaphore(int permits) {
    sync = new NonfairSync(permits);
}

NonfairSync(int permits) {
    super(permits);
}

Sync(int permits) {
    setState(permits);
}
```





#### 3.2 加锁

1、如果是非公平锁，执行`nonfairTryAcquireShared`方法

- 通过`tryAcquireShared`尝试获取锁，如果小于0表示获取锁失败

2、如果获取锁失败，就执行`doAcquireSharedInterruptibly`方法

- 为当前线程创建Node节点，加到AQS队列尾部
- 如果当前节点的前驱为头结点，那么再次尝试获取锁，如果获取锁失败，就park暂停

```java
public void acquire() throws InterruptedException {
    sync.acquireSharedInterruptibly(1);
}
```

```java
public final void acquireSharedInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    // tryAcquireShared执行后的返回结果如果小于0表示获取锁失败
    if (tryAcquireShared(arg) < 0)
        doAcquireSharedInterruptibly(arg);
}
```

```java
protected int tryAcquireShared(int acquires) {
    return nonfairTryAcquireShared(acquires);
}
```

```java
final int nonfairTryAcquireShared(int acquires) {
    for (;;) {
        // 拿到state状态
        int available = getState();
        // 计算获取锁后状态
        int remaining = available - acquires;
        // 返回remaining
        if (remaining < 0 ||
            compareAndSetState(available, remaining))
            return remaining;
    }
}
```

```java
private void doAcquireSharedInterruptibly(int arg)
    throws InterruptedException {
    // 为当前线程创建Node节点，加入到AQS队列尾部
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        for (;;) {
            // 如果前驱是头结点，再次尝试获取锁
            final Node p = node.predecessor();
            if (p == head) {
                // 当前剩余资源数
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    failed = false;
                    return;
                }
            }
            // 获取锁失败，就park暂停
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```





#### 3.3 解锁

1、`tryReleaseShared`方法释放锁，释放成功后，执行`doReleaseShared`方法

2、`doReleaseShared`方法中，唤醒AQS队列中第一个未失效的线程

3、阻塞线程在`doAcquireSharedInterruptibly`方法中被唤醒，尝试获取锁，如果获取锁成功，就执行`setHeadAndPropagate`方法，并将自己从AQS队列移除

4、`setHeadAndPropagate`方法中，将当前节点设为头结点，如果下一个节点也是共享模式的，也去唤醒

```java
public void release() {
    sync.releaseShared(1);
}
```

```java
public final boolean releaseShared(int arg) {
    // 释放锁成功就执行doReleaseShared方法，唤醒AQS中线程
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```

```java
protected final boolean tryReleaseShared(int releases) {
    for (;;) {
        int current = getState();
        // 释放锁后的状态
        int next = current + releases;
        if (next < current) // overflow
            throw new Error("Maximum permit count exceeded");
        // 修改state状态
        if (compareAndSetState(current, next))
            return true;
    }
}
```

```java
private void doReleaseShared() {
    for (;;) {
        Node h = head;
        if (h != null && h != tail) {
            int ws = h.waitStatus;
            if (ws == Node.SIGNAL) {
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                    continue;            // loop to recheck cases
                unparkSuccessor(h);
            }
            else if (ws == 0 &&
                     !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                continue;                // loop on failed CAS
        }
        if (h == head)                   // loop if head changed
            break;
    }
}
```

```java
private void setHeadAndPropagate(Node node, int propagate) {
    Node h = head; // Record old head for check below
    setHead(node);

    if (propagate > 0 || h == null || h.waitStatus < 0 ||
        (h = head) == null || h.waitStatus < 0) {
        Node s = node.next;
        if (s == null || s.isShared())
            doReleaseShared();
    }
}
```







### 4、**CountdownLatch**

> 用来进行线程同步协作，等待所有线程完成倒计时。 
>
> 其中构造参数用来初始化等待计数值，`await()` 用来等待计数归零，`countDown()` 用来让计数减一



**CountdownLatch源码**

> 1、调用`await`方法后，最终会调用`tryReleaseShared`方法尝试获取锁，如果`state`为0，返回true，获取锁成功，否则，获取锁失败，进入AQS队列等待
>
> 2、调用`countDown`方法后，会将锁计数器减一，如果最终减为0，会去唤醒AQS队列中线程

```java
public class CountDownLatch {
    private static final class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = 4982264981922014374L;

        Sync(int count) {
            setState(count);
        }

        int getCount() {
            return getState();
        }

        protected int tryAcquireShared(int acquires) {
            return (getState() == 0) ? 1 : -1;
        }

        protected boolean tryReleaseShared(int releases) {
            // Decrement count; signal when transition to zero
            for (;;) {
                int c = getState();
                if (c == 0)
                    return false;
                int nextc = c-1;
                if (compareAndSetState(c, nextc))
                    return nextc == 0;
            }
        }
    }
    
    private final Sync sync;
    
    public CountDownLatch(int count) {
        if (count < 0) throw new IllegalArgumentException("count < 0");
        this.sync = new Sync(count);
    }
    
    public void await() throws InterruptedException {
        sync.acquireSharedInterruptibly(1);
    }
    
    public void countDown() {
        sync.releaseShared(1);
    }
    ...
}
```





**示例**

> 1、主线程调用`latch.await()`方法等待，直到`state`状态为0才获取锁成功，当前三个线程都执行了`latch.countDown()`方法后，主线程被唤醒执行
>
> 2、这种方式虽然可以使用`join`替代，但是`join`属于底层API，`CountDownLatch`属于上层API。同时，使用`join`方法必须是当前线程结束才可以，但是多线程场景下大多使用线程池，线程池线程执行完任务后是不会释放的，因此最好使用`CountDownLatch`

```java
public static void main(String[] args) throws InterruptedException {
    CountDownLatch latch = new CountDownLatch(3);
    
    new Thread(() -> {
        log.debug("begin...");
        sleep(1);
        latch.countDown();
        log.debug("end...{}", latch.getCount());
    }).start();
    
    new Thread(() -> {
        log.debug("begin...");
        sleep(2);
        latch.countDown();
        log.debug("end...{}", latch.getCount());
    }).start();
    
    new Thread(() -> {
        log.debug("begin...");
        sleep(1.5);
        latch.countDown();
        log.debug("end...{}", latch.getCount());
    }).start();
    
    log.debug("waiting...");
    latch.await();
    log.debug("wait end...");
}

18:44:00.778 c.TestCountDownLatch [main] - waiting... 
18:44:00.778 c.TestCountDownLatch [Thread-2] - begin... 
18:44:00.778 c.TestCountDownLatch [Thread-0] - begin... 
18:44:00.778 c.TestCountDownLatch [Thread-1] - begin... 
18:44:01.782 c.TestCountDownLatch [Thread-0] - end...2 
18:44:02.283 c.TestCountDownLatch [Thread-2] - end...1 
18:44:02.782 c.TestCountDownLatch [Thread-1] - end...0 
18:44:02.782 c.TestCountDownLatch [main] - wait end...
```





**结合线程池使用**

```java
public static void main(String[] args) throws InterruptedException {
    CountDownLatch latch = new CountDownLatch(3);
    ExecutorService service = Executors.newFixedThreadPool(4);
    
    service.submit(() -> {
        log.debug("begin...");
        sleep(1);
        latch.countDown();
        log.debug("end...{}", latch.getCount());
    });
    
    service.submit(() -> {
        log.debug("begin...");
        sleep(1.5);
        latch.countDown();
        log.debug("end...{}", latch.getCount());
    });
    
    service.submit(() -> {
        log.debug("begin...");
        sleep(2);
        latch.countDown();
        log.debug("end...{}", latch.getCount());
    });
    
    service.submit(()->{
        try {
            log.debug("waiting...");
            latch.await();
            log.debug("wait end...");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
}
```





**应用**

1、游戏加载时，必须10名玩家都加载完毕，才可以开始游戏，这种情况就可以使用`CountDownLatch`来实现，`CountDownLatch`初始值设为10，每有一名玩家加载完毕，就调用`countDown()`方法，而开始游戏的线程则使用`await()`方法等待

2、微服务中，经常需要使用`Feign`远程调用其他服务，平时使用都是串行执行的，如果调用服务非常多，那么执行流程将会非常慢，因此可以使用线程池分别执行不同的远程调用。主线程中使用`await()`方法等待所有远程调用执行完毕，每个线程执行完毕后都去调用`countDown()`方法。如果主线程需要得到每个远程调用的结果，那么线程池最好使用`submit`方式提交，以得到返回值，此时，可以不需要使用`CountDownLatch`了，可以直接使用`Future`的`get()`方法来获取每个任务的返回结果







### 5、CyclicBarrier

> 1、`CountdownLatch`在多个线程执行`countDown`后，state变为0。如果下次还需要使用`CountdownLatch`，需要重新创建，因为state变为0后就无法修改了。而`CyclicBarrier`可以解决这个问题。
>
> 2、`CyclicBarrier`用来进行线程协作，等待线程满足某个计数。构造时设置**计数个数**，每个线程执行到某个需要**同步**的时刻调用 `await() `方法进行等待，当等待的线程数满足**计数个数**时，继续执行。
>
> 3、`CyclicBarrier` 与 `CountDownLatch` 的主要区别在于 `CyclicBarrie`r 是可以重用的 ，`CyclicBarrier` 可以被比喻为『人满发车』



```java
CyclicBarrier cb = new CyclicBarrier(2); // 个数为2时才会继续执行

new Thread(()->{
    System.out.println("线程1开始.."+new Date());
    try {
        cb.await(); // 当个数不足时，等待
    } catch (InterruptedException | BrokenBarrierException e) {
        e.printStackTrace();
    }
    System.out.println("线程1继续向下运行..."+new Date());
}).start();

new Thread(()->{
    System.out.println("线程2开始.."+new Date());
    try { 
        Thread.sleep(2000); 
    } catch (InterruptedException e) {
    }
    try {
        cb.await(); // 2 秒后，线程个数够2，继续运行
    } catch (InterruptedException | BrokenBarrierException e) {
        e.printStackTrace();
    }
    System.out.println("线程2继续向下运行..."+new Date());
}).start();
```







## 八、线程安全的集合

1、原始的线程安全集合如 `Hashtable `， `Vector`都是使用`synchronized`修饰方法保证线程安全，但是性能很差

2、使用 `Collections` 装饰的线程安全集合

- `Collections.SynchronizedCollection `
- `Collections.SynchronizedList `
- `Collections.SynchronizedMap `
- `Collections.SynchronizedSet `
- `Collections.SynchronizedNavigableMap `
- `Collections.SynchronizedNavigableSet`
- `Collections.SynchronizedNavigableSet`
- `Collections.SynchronizedSortedMap `
- `Collections.SynchronizedSortedSet `

**SynchronizedMap**

底层就是对Map进行了代理，调用方法前使用`synchronized`加锁

```java
private static class SynchronizedMap<K,V>
    implements Map<K,V>, Serializable {
    private static final long serialVersionUID = 1978198479659022715L;

    private final Map<K,V> m;     // Backing Map
    final Object      mutex;        // Object on which to synchronize

    SynchronizedMap(Map<K,V> m) {
        this.m = Objects.requireNonNull(m);
        mutex = this;
    }

    SynchronizedMap(Map<K,V> m, Object mutex) {
        this.m = m;
        this.mutex = mutex;
    }

    public int size() {
        synchronized (mutex) {return m.size();}
    }
    public boolean isEmpty() {
        synchronized (mutex) {return m.isEmpty();}
    }
    public boolean containsKey(Object key) {
        synchronized (mutex) {return m.containsKey(key);}
    }
    public boolean containsValue(Object value) {
        synchronized (mutex) {return m.containsValue(value);}
    }
    public V get(Object key) {
        synchronized (mutex) {return m.get(key);}
    }

    public V put(K key, V value) {
        synchronized (mutex) {return m.put(key, value);}
    }
    public V remove(Object key) {
        synchronized (mutex) {return m.remove(key);}
    }
    public void putAll(Map<? extends K, ? extends V> map) {
        synchronized (mutex) {m.putAll(map);}
    }
    public void clear() {
        synchronized (mutex) {m.clear();}
    }
```



3、JUC下的安全集合

- `Blocking` 大部分实现基于锁，并提供用来阻塞的方法 
- `CopyOnWrite` 之类容器修改开销相对较重 
- `Concurrent` 类型的容器 
  - 内部很多操作使用 cas 优化，一般可以提供较高吞吐量 
  - 弱一致性 
    - 遍历时弱一致性，例如，当利用迭代器遍历时，如果容器发生修改，迭代器仍然可以继续进行遍历，这时内容是旧的 
    - 求大小弱一致性，size 操作未必是 100% 准确 
    - 读取弱一致性 


> 遍历时如果发生了修改，对于非安全容器来讲，使用 **fail-fast** 机制也就是让遍历立刻失败，抛出ConcurrentModifificationException，不再继续遍历





### 1、ConcurrentHashMap

#### 1.1 JDK 7中HashMap的并发死链问题

JDK7中，HashMap中添加元素，发生hash冲突时，是通过头插法在链表上插入的，会出现并发死链的问题。而JDK8修改为尾插法，避免了这个问题



**并发死链**

1、假设当前Map中有12个元素，Map的容量为16，其中元素16，35，1由于hash冲突，都在第一个Node节点的链表上。由于JDK7是头插法，因此插入顺序16，35，1，最后在链表上是1，35，16

2、此时线程1和线程2都来执行插入操作，分别插入一个元素，达到阈值，触发扩容操作，扩容会将所有元素重新rehash，重新分配到Node数组中，假设1和35rehash后还在同一个链表上

3、线程1执行扩容操作，先执行到1处，此时`e`是`1 -> 35 -> 16`，`next`是`35 -> 16`

4、线程2开始执行，一直执行完扩容的操作，由于JDK7是头插法，因此会出现`35 -> 1`

**JDK7中扩容代码**

```java
// 将 table 迁移至 newTable
void transfer(Entry[] newTable, boolean rehash) { 
    int newCapacity = newTable.length;
    for (Entry<K,V> e : table) {
        while(null != e) {
            Entry<K,V> next = e.next;
            // 1 处
            if (rehash) {
                e.hash = null == e.key ? 0 : hash(e.key);
            }
            int i = indexFor(e.hash, newCapacity);
            // 2 处
            // 将新元素加入 newTable[i], 原 newTable[i] 作为新元素的 next
            e.next = newTable[i];
            newTable[i] = e;
            e = next;
        }
    }
}
```

5、此时线程1继续执行，`e`指向的`1 -> 35 -> 16`此时变为`1 -> null`，而`next`由`35 -> 16`变为`35 -> 1`。然后头插法将1插入35前边，此时出现了`1 -> 35 -> 1`的情况，出现并发死链的问题





#### 1.2 JDK8中ConcurrentHashMap

##### **重要属性和内部类**

```java
// 默认为0，如果初始化时指定了容量大小，sizeCtl就是该容量
// 当初始化时, 为 -1
// 当扩容时, 为 -(1 + 扩容线程数)
// 当初始化或扩容完成后，为 下一次的扩容的阈值大小
private transient volatile int sizeCtl;

// 整个 ConcurrentHashMap 就是一个 Node[]
static class Node<K,V> implements Map.Entry<K,V> {}

// hash 表
transient volatile Node<K,V>[] table;

// 扩容时的 新 hash 表
private transient volatile Node<K,V>[] nextTable;

// 扩容时如果某个 bin 迁移完毕, 用 ForwardingNode 作为旧 table bin 的头结点
static final class ForwardingNode<K,V> extends Node<K,V> {}

// 用在 compute 以及 computeIfAbsent 时, 用来占位, 计算完成后替换为普通 Node
static final class ReservationNode<K,V> extends Node<K,V> {}

// 作为 treebin 的头节点, 存储 root 和 first
static final class TreeBin<K,V> extends Node<K,V> {}

// 作为 treebin 的节点, 存储 parent, left, right
static final class TreeNode<K,V> extends Node<K,V> {}
```





##### **重要方法**

```java
// 获取 Node[] 中第 i 个 Node
static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i)
 
// cas 修改 Node[] 中第 i 个 Node 的值, c 为旧值, v 为新值
static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i, Node<K,V> c, Node<K,V> v)
 
// 直接修改 Node[] 中第 i 个 Node 的值, v 为新值
static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v)
```





##### **构造器**

```java
public ConcurrentHashMap(int initialCapacity) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException();
    int cap = ((initialCapacity >= (MAXIMUM_CAPACITY >>> 1)) ?
               MAXIMUM_CAPACITY :
               tableSizeFor(initialCapacity + (initialCapacity >>> 1) + 1));
    this.sizeCtl = cap;
}

// concurrencyLevel是并发级别，允许同时进行更新操作的线程数（jdk1.8貌似没用，只有构造器用到了）
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel) {
    if (!(loadFactor > 0.0f) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
    if (initialCapacity < concurrencyLevel) // Use at least as many bins
        initialCapacity = concurrencyLevel; // as estimated threads
    long size = (long)(1.0 + (long)initialCapacity / loadFactor);
    // tableSizeFor 仍然是保证计算的大小是 2^n, 即 16,32,64 ... 
    int cap = (size >= (long)MAXIMUM_CAPACITY) ?
        MAXIMUM_CAPACITY : tableSizeFor((int)size);
    this.sizeCtl = cap; 
}
```





##### **get**

```java
public V get(Object key) {
    Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
    // spread 方法能确保返回结果是正数
    int h = spread(key.hashCode());
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {
        // 如果头结点已经是要查找的 key
        if ((eh = e.hash) == h) {
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                return e.val;
        }
        // hash 为负数表示该 bin 在扩容中或是 treebin, 这时调用 find 方法来查找
        else if (eh < 0)
            return (p = e.find(h, key)) != null ? p.val : null;
        // 正常遍历链表, 用 equals 比较
        while ((e = e.next) != null) {
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
```





##### **put**

```java
public V put(K key, V value) {
    return putVal(key, value, false);
}

// onlyIfAbsent为true表示当前这个key不存在才执行
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // key和value都不能为null，hashmap的key和value可以为null
    if (key == null || value == null) throw new NullPointerException();
    // 获取hash值
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        // f 是链表头节点，fh 是链表头结点的 hash，i 是链表在 table 中的下标
        Node<K,V> f; int n, i, fh;
        // 如果table数组为空，就创建
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        // 如果通过hash值找到的对应位置为空，那么就创建一个Node，放在该位置
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        // 当前线程帮忙扩容，hash值为MOVED，表示当前map正在扩容，MOVED为-1
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            // 对当前链表的头结点加锁，在链表或者红黑树上遍历
            synchronized (f) {
                // 再次确认链表头节点没有被移动
                if (tabAt(tab, i) == f) {
                    // fh>=0表示是链表
                    if (fh >= 0) {
                        binCount = 1;
                        // 遍历链表
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            // 找到相同的key，更新
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            // 找到最后也没有找到相同的key, 新增 Node, 追加至链表尾
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    // 否则是红黑树
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                // 如果链表长度 >= 8, 且数组容量达到64，进行链表转为红黑树
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    // 增加size计数
    addCount(1L, binCount);
    return null;
}
```





##### **initTable**

> `sizeCtl`
>
> - 默认为 `0`，如果初始化时指定了容量大小，`sizeCtl`就是该容量
> - 当初始化时, 为` -1`
> - 当扩容时, 为` -(1 + 扩容线程数)`
> - 当初始化或扩容完成后，为 下一次的扩容的阈值大小

```java
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
        if ((sc = sizeCtl) < 0)
            Thread.yield(); // lost initialization race; just spin
        // 尝试将 sizeCtl 设置为 -1（表示初始化 table）
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
            // 将SIZECTL修改为-1后表示正在初始化，此时其他线程进来，因为SIZECTL小于0，所以会在while中循环yield直到table创建
            try {
                if ((tab = table) == null || tab.length == 0) {
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY; // 如果指定了容量，初始大小就是sizeCtl，否则默认16
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    sc = n - (n >>> 2); // sc = n * 0.75，扩容阈值
                }
            } finally {
                sizeCtl = sc; // 保存为扩容阈值
            }
            break;
        }
    }
    return tab;
}
```





##### **addCount**

执行`put`方法后最后通过`addCount(1L, binCount);`调用`addCount`，底层原理类似于原子累加器`LongAdder`

```java
private final void addCount(long x, int check) {
    CounterCell[] as; long b, s;
    // as不为空，或者向baseCount累加失败
    if ((as = counterCells) != null ||
        !U.compareAndSwapLong(this, BASECOUNT, b = baseCount, s = b + x)) {
        CounterCell a; long v; int m;
        boolean uncontended = true;
        // 如果as为空
        if (as == null || (m = as.length - 1) < 0 ||
            // 当前这个累加单元为空
            (a = as[ThreadLocalRandom.getProbe() & m]) == null ||
            // 向当前这个累加单元，累加失败
            !(uncontended = U.compareAndSwapLong(a, CELLVALUE, v = a.value, v + x))) {
            // 创建累加单元数组和cell, 累加重试
            fullAddCount(x, uncontended);
            return;
        }
        if (check <= 1)
            return;
        // s记录当前集合中元素总数
        s = sumCount();
    }
    if (check >= 0) {
        Node<K,V>[] tab, nt; int n, sc;
        // 如果s达到了扩容阈值，就进行扩容
        while (s >= (long)(sc = sizeCtl) && (tab = table) != null &&
               (n = tab.length) < MAXIMUM_CAPACITY) {
            int rs = resizeStamp(n);
            if (sc < 0) {
                if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                    sc == rs + MAX_RESIZERS || (nt = nextTable) == null ||
                    transferIndex <= 0)
                    break;
                // 其他线程来了，此时nexttable已经创建好了，帮忙扩容
                if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1))
                    transfer(tab, nt);
            }
            // 扩容，修改SIZECTL状态，同时执行transfer方法，此时nexttable还未创建
            else if (U.compareAndSwapInt(this, SIZECTL, sc,
                                         (rs << RESIZE_STAMP_SHIFT) + 2))
                transfer(tab, null);
            s = sumCount();
        }
    }
}
```





##### size

`size` 计算发生在 `put`，`remove` 改变集合元素的操作之中 

- 没有竞争发生，向 `baseCount `累加计数 
- 有竞争发生，新建` counterCells`，向其中的一个 `cell `累加计数 
  - `counterCells` 初始有两个 `cell `
  - 如果计数竞争比较激烈，会创建新的 cell 来累加计数


```java
public int size() {
    long n = sumCount();
    return ((n < 0L) ? 0 :
            (n > (long)Integer.MAX_VALUE) ? Integer.MAX_VALUE :
            (int)n);
}
```

```java
final long sumCount() {
    CounterCell[] as = counterCells; CounterCell a;
    long sum = baseCount;
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {
            if ((a = as[i]) != null)
                sum += a.value;
        }
    }
    return sum;
}
```





##### transfer

扩容时以 链表 为单位进行，需要对 链表头 进行 `synchronized`，这时其它竞争线程会帮助把其它 链表 进行扩容

扩容过程中 `get` 操作拿到的是 `ForwardingNode` 它会让 `get `操作在新 `table` 进行搜索 

```java
// tab是原始table，nextTab是新的table
private final void transfer(Node<K,V>[] tab, Node<K,V>[] nextTab) {
    int n = tab.length, stride;
    if ((stride = (NCPU > 1) ? (n >>> 3) / NCPU : n) < MIN_TRANSFER_STRIDE)
        stride = MIN_TRANSFER_STRIDE; // subdivide range
    // 如果新的table为空，先创建，大小为原来2倍
    if (nextTab == null) {            // initiating
        try {
            @SuppressWarnings("unchecked")
            Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1];
            nextTab = nt;
        } catch (Throwable ex) {      // try to cope with OOME
            sizeCtl = Integer.MAX_VALUE;
            return;
        }
        nextTable = nextTab;
        transferIndex = n;
    }
    int nextn = nextTab.length;
    ForwardingNode<K,V> fwd = new ForwardingNode<K,V>(nextTab);
    boolean advance = true;
    boolean finishing = false; // to ensure sweep before committing nextTab
    // 以链表为单位，一个链表一个链表的从tab迁移到nextTab
    for (int i = 0, bound = 0;;) {
        Node<K,V> f; int fh;
        while (advance) {
            ...
        }
        if (i < 0 || i >= n || i + n >= nextn) {
            int sc;
            if (finishing) {
                nextTable = null;
                table = nextTab;
                sizeCtl = (n << 1) - (n >>> 1);
                return;
            }
            if (U.compareAndSwapInt(this, SIZECTL, sc = sizeCtl, sc - 1)) {
                if ((sc - 2) != resizeStamp(n) << RESIZE_STAMP_SHIFT)
                    return;
                finishing = advance = true;
                i = n; // recheck before commit
            }
        }
        // 如果链表头为null，表示该链表处理完了，就将该链表头结点设为ForwardingNode
        else if ((f = tabAt(tab, i)) == null)
            advance = casTabAt(tab, i, null, fwd);
        // 如果发现链表头已经是ForwardingNode，就去处理下一个链表
        else if ((fh = f.hash) == MOVED)
            advance = true; // already processed
        else {
            // 把当前链表锁住，进行迁移
            synchronized (f) {
                if (tabAt(tab, i) == f) { // 普通节点
                    	...
                    }
                    else if (f instanceof TreeBin) { // 树节点
                        ...
                    }
                }
            }
        }
    }
}
```





#### 1.3 JDK7中ConcurrentHashMap

JDK7中的`ConcurrentHashMap`维护了一个 `segment` 数组，每个 `segment `对应一把锁 

- 优点：如果多个线程访问不同的 `segment`，实际是没有冲突的，与 jdk8 中是类似的 
- 缺点：`Segments `数组默认大小为16，这个容量初始化指定后就不能改变了，并且不是懒惰初始化 



##### 构造器

```java
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel) {
    if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
    if (concurrencyLevel > MAX_SEGMENTS)
        concurrencyLevel = MAX_SEGMENTS;
    // ssize 必须是 2^n, 即 2, 4, 8, 16 ... 表示了 segments 数组的大小
    int sshift = 0;
    int ssize = 1;
    while (ssize < concurrencyLevel) {
        ++sshift;
        ssize <<= 1;
    }
    // segmentShift 默认是 32 - 4 = 28
    this.segmentShift = 32 - sshift;
    // segmentMask 默认是 15 即 0000 0000 0000 1111
    this.segmentMask = ssize - 1;
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    int c = initialCapacity / ssize;
    if (c * ssize < initialCapacity)
        ++c;
    int cap = MIN_SEGMENT_TABLE_CAPACITY;
    while (cap < c)
        cap <<= 1;
    // 创建 segments and segments[0]
    Segment<K,V> s0 =
        new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                         (HashEntry<K,V>[])new HashEntry[cap]);
    Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
    UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
    this.segments = ss; 
}
```



构造完成，如下图所示

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202401312315330.png" style="zoom:80%;" /> 



可以看到 `ConcurrentHashMap` 没有实现懒惰初始化，空间占用不友好。其中` this.segmentShift` 和 `this.segmentMask `的作用是决定将 key 的 hash 结果匹配到哪个 `segment `

例如，根据某一 hash 值求 segment 位置，先将高位向低位移动 `this.segmentShift `位，结果在与`this.segmentMask`做位运算，最终得到1010即下标为10的`segment`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401312318786.png) 





##### put

```java
public V put(K key, V value) {
    Segment<K,V> s;
    if (value == null)
        throw new NullPointerException();
    int hash = hash(key);
    // 计算出 segment 下标
    int j = (hash >>> segmentShift) & segmentMask;
    
    // 获得 segment 对象, 判断是否为 null, 是则创建该 segment
    if ((s = (Segment<K,V>)UNSAFE.getObject 
         (segments, (j << SSHIFT) + SBASE)) == null) {
        // 这时不能确定是否真的为 null, 因为其它线程也发现该 segment 为 null,
        // 因此在 ensureSegment 里用 cas 方式保证该 segment 安全性
        s = ensureSegment(j);
    }
    // 进入 segment 的put 流程
    return s.put(key, hash, value, false);
}
```

`segment` 继承了可重入锁（ReentrantLock），put 方法如下

```java
final V put(K key, int hash, V value, boolean onlyIfAbsent) {
    // 尝试加锁
    HashEntry<K,V> node = tryLock() ? null :
    // 如果不成功, 进入 scanAndLockForPut 流程
    // 如果是多核 cpu 最多 tryLock 64 次, 进入 lock 流程
    // 在尝试期间, 还可以顺便看该节点在链表中有没有, 如果没有顺便创建出来
    scanAndLockForPut(key, hash, value);
    
    // 执行到这里 segment 已经被成功加锁, 可以安全执行
    V oldValue;
    try {
        HashEntry<K,V>[] tab = table;
        int index = (tab.length - 1) & hash;
        HashEntry<K,V> first = entryAt(tab, index);
        for (HashEntry<K,V> e = first;;) {
            if (e != null) {
                // 更新
                K k;
                if ((k = e.key) == key ||
                    (e.hash == hash && key.equals(k))) { 
                    oldValue = e.value;
                    if (!onlyIfAbsent) {
                        e.value = value;
                        ++modCount;
                    }
                    break;
                }
                e = e.next;
            }
            else {
                // 新增
                // 1) 之前等待锁时, node 已经被创建, next 指向链表头
                if (node != null)
                    node.setNext(first);
                else
                    // 2) 创建新 node
                    node = new HashEntry<K,V>(hash, key, value, first);
                int c = count + 1; 
                // 3) 扩容
                if (c > threshold && tab.length < MAXIMUM_CAPACITY)
                    rehash(node);
                else
                    // 将 node 作为链表头
                    setEntryAt(tab, index, node);
                ++modCount;
                count = c;
                oldValue = null;
                break;
            }
        }
    } finally {
        unlock();
    }
    return oldValue; 
}
```







##### rehash

> `rehash`发生在 `put` 中，因为此时已经获得了锁，因此` rehash` 时不需要考虑线程安全

```java
private void rehash(HashEntry<K,V> node) {
    HashEntry<K,V>[] oldTable = table;
    int oldCapacity = oldTable.length;
    int newCapacity = oldCapacity << 1;
    threshold = (int)(newCapacity * loadFactor);
    HashEntry<K,V>[] newTable =
        (HashEntry<K,V>[]) new HashEntry[newCapacity];
    int sizeMask = newCapacity - 1;
    for (int i = 0; i < oldCapacity ; i++) {
        HashEntry<K,V> e = oldTable[i];
        if (e != null) {
            HashEntry<K,V> next = e.next;
            int idx = e.hash & sizeMask;
            if (next == null) // Single node on list
                newTable[idx] = e;
            else { // Reuse consecutive sequence at same slot
                HashEntry<K,V> lastRun = e;
                int lastIdx = idx;
                // 如果next经过rehash后，下标位置与e的位置不一样，如果后边有个next位置一样的，就从next节点开始将后边的一块移动的新的位置去
                for (HashEntry<K,V> last = next; last != null; last = last.next) {
                    int k = last.hash & sizeMask;
                    if (k != lastIdx) {
                        lastIdx = k;
                        lastRun = last;
                    }
                }
                newTable[lastIdx] = lastRun;
                // 剩余节点需要新建
                for (HashEntry<K,V> p = e; p != lastRun; p = p.next) {
                    V v = p.value;
                    int h = p.hash;
                    int k = h & sizeMask;
                    HashEntry<K,V> n = newTable[k];
                    newTable[k] = new HashEntry<K,V>(h, p.key, v, n);
                }
            }
        }
    }
    // 扩容完成, 才加入新的节点
    int nodeIndex = node.hash & sizeMask; // add the new node
    node.setNext(newTable[nodeIndex]);
    newTable[nodeIndex] = node;
    
    // 替换为新的 HashEntry table
    table = newTable; 
}
```





##### get

> get 时并未加锁，用了 UNSAFE 方法保证了可见性，扩容过程中，get 先发生就从旧表取内容，get 后发生就从新表取内容

```java
public V get(Object key) {
    Segment<K,V> s; // manually integrate access methods to reduce overhead
    HashEntry<K,V>[] tab;
    int h = hash(key);
    // u 为 segment 对象在数组中的偏移量
    long u = (((h >>> segmentShift) & segmentMask) << SSHIFT) + SBASE;
    // s 即为 segment
    if ((s = (Segment<K,V>)UNSAFE.getObjectVolatile(segments, u)) != null &&
        (tab = s.table) != null) {
        for (HashEntry<K,V> e = (HashEntry<K,V>) UNSAFE.getObjectVolatile
             (tab, ((long)(((tab.length - 1) & h)) << TSHIFT) + TBASE);
             e != null; e = e.next) {
            K k;
            if ((k = e.key) == key || (e.hash == h && key.equals(k)))
                return e.value;
        }
    }
    return null; 
}
```





##### size 

> - 计算元素个数前，先不加锁计算两次，如果前后两次结果都一样，认为个数正确返回 
> - 如果不一样，进行重试，重试次数超过 3，将所有 segment 锁住，重新计算个数返回

```java
public int size() {
    // Try a few times to get accurate count. On failure due to
    // continuous async changes in table, resort to locking.
    final Segment<K,V>[] segments = this.segments;
    int size;
    boolean overflow; // true if size overflows 32 bits
    long sum; // sum of modCounts
    long last = 0L; // previous sum
    int retries = -1; // first iteration isn't retry
    try {
        for (;;) {
            if (retries++ == RETRIES_BEFORE_LOCK) {
                // 超过重试次数, 需要创建所有 segment 并加锁
                for (int j = 0; j < segments.length; ++j)
                    ensureSegment(j).lock(); // force creation
            }
            sum = 0L;
            size = 0;
            overflow = false;
            for (int j = 0; j < segments.length; ++j) {
                Segment<K,V> seg = segmentAt(segments, j);
                if (seg != null) {
                    sum += seg.modCount;
                    int c = seg.count;
                    if (c < 0 || (size += c) < 0)
                        overflow = true;
                }
            }
            if (sum == last)
                break;
            last = sum;
        }
    } finally {
        if (retries > RETRIES_BEFORE_LOCK) {
            for (int j = 0; j < segments.length; ++j)
                segmentAt(segments, j).unlock();
        }
    }
    return overflow ? Integer.MAX_VALUE : size; 
}
```







### 2、LinkedBlockingQueue

`LinkedBlockingQueue`内部有两把锁，一把锁用于添加数据，一把锁用于取数据。如果队列满了，再次添加时，线程就去`notFull`等待。如果取数据时队列为空，就去`notEmpty`等待

内部节点都是其内部类`Node`封装的

```java
public class LinkedBlockingQueue<E> extends AbstractQueue<E> implements BlockingQueue<E>, java.io.Serializable {
    private final ReentrantLock takeLock = new ReentrantLock();

    /** Wait queue for waiting takes */
    private final Condition notEmpty = takeLock.newCondition();

    /** Lock held by put, offer, etc */
    private final ReentrantLock putLock = new ReentrantLock();

    /** Wait queue for waiting puts */
    private final Condition notFull = putLock.newCondition();
    
    private final AtomicInteger count = new AtomicInteger();  // 使用AtomicInteger表示当前队列容量
    
    static class Node<E> {
        E item;
        Node<E> next;
        Node(E x) { 
            item = x; 
        }
    }
    
    public LinkedBlockingQueue() {
        this(Integer.MAX_VALUE);
    }

    public LinkedBlockingQueue(int capacity) {
        if (capacity <= 0) throw new IllegalArgumentException();
        this.capacity = capacity;
        last = head = new Node<E>(null);
    }
}
```

初始化链表 `last = head = new Node<E>(null);` `Dummy` 节点用来占位，`item` 为 `null`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401312328162.png)



#### 2.1 入队

```java
public void put(E e) throws InterruptedException {
    if (e == null) throw new NullPointerException();
    int c = -1;
	// 创建节点
    Node<E> node = new Node<E>(e);
    final ReentrantLock putLock = this.putLock;
    // 使用AtomicInteger计数
    final AtomicInteger count = this.count;
    // 加锁
    putLock.lockInterruptibly();
    try {
		// 如果队列满了，当前线程就进入notFull等待
        while (count.get() == capacity) {
            notFull.await();
        }
        // 入队
        enqueue(node);
        // c就是当前队列元素数
        c = count.getAndIncrement();
        // 如果容量未满，就唤醒notFull中的一个线程
        if (c + 1 < capacity)
            notFull.signal();
    } finally { 
        // 解锁
        putLock.unlock();
    }
    // c==0表示原来队列中没有元素，现在添加了一个数据，就唤醒NotEmpty中等待的线程
    if (c == 0)
        signalNotEmpty();
}

private void enqueue(Node<E> node) {
    last = last.next = node;
}
```

当一个节点入队` last = last.next = node;`

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401312328024.png)







#### 2.2 出队

```java
public E take() throws InterruptedException {
    E x;
    int c = -1;
    // 当前容量
    final AtomicInteger count = this.count;
    final ReentrantLock takeLock = this.takeLock;
    // 加锁
    takeLock.lockInterruptibly();
    try {
        // 如果队列为空，当前线程去notEmpty等待
        while (count.get() == 0) {
            notEmpty.await();
        }
        // 取出队头元素
        x = dequeue();
        // 容量-1
        c = count.getAndDecrement();
        // c>1表示队列非空，那么就去notEmpty唤醒一个线程
        if (c > 1)
            notEmpty.signal();
    } finally {
        takeLock.unlock(); // 释放锁
    }
    // 如果c == capacity表示队列是满的，现在取出一个元素，队列就可以进去添加元素，唤醒NotFull中的一个线程
    if (c == capacity)
        signalNotFull();
    return x;
}

private E dequeue() {
    // 头结点
    Node<E> h = head;
    // 头结点的下一个节点，这个节点才是有数据的
    Node<E> first = h.next;
    // 头结点的next指向自己，避免乱指，方便gc
    h.next = h; // help GC
    // head指向first
    head = first;
    // 拿到数据
    E x = first.item;
    // 将head值为Dummy节点
    first.item = null;
    return x;
}
```







#### 2.3 性能比较 

主要列举 LinkedBlockingQueue 与 ArrayBlockingQueue 的性能比较 

- Linked 支持有界，Array 强制有界 
- Linked 实现是链表，Array 实现是数组 
- Linked 是懒惰的，而 Array 需要提前初始化 Node 数组 
- Linked 每次入队会生成新 Node，而 Array 的 Node 是提前创建好的 
- Linked 两把锁，Array 一把锁









### 3、ConcurrentLinkedQueue 

`ConcurrentLinkedQueue `的设计与 `LinkedBlockingQueue` 非常像

- 两把【锁】，同一时刻，可以允许两个线程同时（一个生产者与一个消费者）执行 
- dummy 节点的引入让两把【锁】将来锁住的是不同对象，避免竞争 
- 只是这【锁】使用了 cas 来实现 



Tomcat 的 Connector 结构中，Acceptor 作为生产者向 Poller 消费者传递事件信息时，就是采用了`ConcurrentLinkedQueue` 将 `SocketChannel `给 Poller 使用

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202401312332047.png)









### 4、CopyOnWriteArrayList

`CopyOnWriteArraySet`的底层就是`CopyOnWriteArrayList`

底层实现采用了 **写入时拷贝**的思想，增删改操作会将底层数组拷贝一份，更改操作在新数组上执行，这时不影响其它线程的**并发读**，**读写分离**。 



```java
public class CopyOnWriteArrayList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    private static final long serialVersionUID = 8673264195747942595L;

    final transient ReentrantLock lock = new ReentrantLock();

    private transient volatile Object[] array;

    final Object[] getArray() {
        return array;
    }

    final void setArray(Object[] a) {
        array = a;
    }

    public CopyOnWriteArrayList() {
        setArray(new Object[0]);
    }
	...
}
```





**add**

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    // 加锁
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        // 拷贝一份数据
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        // 在新数组中添加元素
        newElements[len] = e;
        // 将新数组设置为array
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```



**get**

```java
public E get(int index) {
    return get(getArray(), index);
}
```





#### 弱一致性

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402012243781.png)



![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202402012244395.png)

> 线程0先通过`get`获取数组，然后线程1删除元素时，会新创建一个数组，删除后，修改其引用。但是线程0获取到的数组还是之前为删除时的，发生了不一致性问题





**迭代器弱一致性**

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();
    list.add(1);
    list.add(2);
    list.add(3);
    Iterator<Integer> iter = list.iterator();
    new Thread(() -> {
        list.remove(0);
        System.out.println(list); // 2 3
    }).start();
    Thread.sleep(1000);
    while (iter.hasNext()) {
        System.out.println(iter.next()); // 1 2 3
    }
}
```



