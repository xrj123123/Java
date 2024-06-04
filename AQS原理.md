AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是基于 **CLH 锁** （Craig, Landin, and Hagersten locks） 实现的。

CLH 锁是对自旋锁的一种改进，是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系），暂时获取不到锁的线程将被加入到该队列中。AQS 将每条请求共享资源的线程封装成一个 CLH 队列锁的一个结点（Node）来实现锁的分配。在 CLH 队列锁中，一个节点表示一个线程，它保存着线程的引用（thread）、 当前节点在队列中的状态（waitStatus）、前驱节点（prev）、后继节点（next）。



1、以`ReentrantLock`为例，当前用`lock.lock()`方法时，会调用`sync.lock();`

```java
public void lock() {
    sync.lock();
}
```

2、在`lock()`方法中，首先尝试通过CAS方式将当前资源状态从0修改为1，如果修改成功表示获取资源成功，就将当前资源拥有者设置为当前线程。如果设置失败，就调用`acquire(1);`

```java
final void lock() {
    if (compareAndSetState(0, 1))
        setExclusiveOwnerThread(Thread.currentThread());
    else
        acquire(1);
}
```

3、在`acquire()`方法中，首先通过`tryAcquire(arg)`尝试获取锁，他会调用`nonfairTryAcquire(int acquires)`方法

- 如果`tryAcquire(arg)`获取锁成功，那么`!tryAcquire(arg)`就是false，就直接退出
- 如果`tryAcquire(arg)`获取锁失败，那么就执行`acquireQueued(addWaiter(Node.EXCLUSIVE), arg)`

这里体现了非公平锁，每个线程获取锁时会尝试直接抢占加塞一次，而CLH队列中可能还有别的线程在等待

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

```java
protected final boolean tryAcquire(int acquires) {
    return nonfairTryAcquire(acquires);
}
```

3.1、在`nonfairTryAcquire`方法中，首先拿到当前资源的状态`state`，如果`state`为0表示没有线程占有，那么就通过CAS方式去占有他；如果`state`不为0，先判断当前资源的拥有者是否为当前线程，是的话就将重入次数+1，并返回true，否则获取锁失败，返回false

```java
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

3.2、获取锁失败，那么就将当前线程加入到队列中，`addWaiter`方法中，首先将当前线程创建一个`Node`节点，然后获取当前队列的尾结点，如果尾结点不为空，那么就将当前节点的前驱设置为尾结点，并通过CAS方式将尾结点设置为当前节点，然后将前驱结点的后继节点设置为当前节点，最后返回这个节点。如果尾结点为空或者通过CAS设置尾结点失败，则执行`enq(node)`方法

```java
private Node addWaiter(Node mode) {
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

3.2.1、`enq`方法中，是一个死循环，先获取到当前的尾结点，如果尾结点为空，那么通过CAS的方式设置头结点为一个空的`Node`，然后尾指针也指向这个节点，继续下次循环，第二次循环时，尾结点就不为null了，此时将当前节点的前驱设置为尾结点，然后通过CAS的方式将尾结点设置为当前节点，最后返回当前节点的前驱结点

```java
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) { // Must initialize
            if (compareAndSetHead(new Node()))
                tail = head;
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

4、执行完`addWaiter(Node.EXCLUSIVE)`方法后，返回的就是已经加入到了双向队列中的节点，然后对该节点执行`acquireQueued`方法，该方法内部也是一个死循环，首先获取当前节点的前驱节点，如果前驱节点为`head`，就表示当前节点是队列中等待的第一个节点，那么就通过`tryAcquire(arg)`方式去获取锁，如果获取锁成功，那么就将当前节点设置为头结点，同时将上一个节点置空，便于gc回收。

如果当前节点的前驱不是`head`或者当前节点获取锁失败，那么就执行`shouldParkAfterFailedAcquire(p, node)`

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
                return interrupted;  // 返回等待过程中是否被中断过
            }
            // 如果自己可以休息了，就通过park()进入waiting状态，直到被unpark()。
            // 如果不可中断的情况下被中断了，那么会从park()中醒过来，发现拿不到资源，从而继续进入park()等待。
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {  
        if (failed) // 如果等待过程中没有成功获取资源（如timeout，或者可中断的情况下被中断了），那么取消结点在队列中的等待。
            cancelAcquire(node);
    }
}
```

5、在`shouldParkAfterFailedAcquire(p, node)`中，首先获得当前节点的前驱结点的状态，如果是正常状态`Node.SIGNAL`就返回true，如果是失效状态`1`，就一直往前找，将当前节点的前驱节点设置为前边不失效的那个节点，如果是其他状态，就通过CAS的方式将前驱结点的状态尝试修改为`Node.SIGNAL`

```java
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)
        // 如果前驱是正常状态，当前节点就可以休息了
        return true;
    if (ws > 0) {
        // 如果前驱节点失效了，放弃获取锁，那么就一直往前找直到第一个正常的节点
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        // 如果前驱正常就把他的状态设置为singal，即他获取锁后通知一下我
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
```

6、`parkAndCheckInterrupt()`方法让当前线程进入`waiting`状态

```java
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);//调用park()使线程进入waiting状态
    return Thread.interrupted();//如果被唤醒，查看自己是不是被中断的。
}
```