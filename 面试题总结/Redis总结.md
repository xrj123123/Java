### Redis总结

**1、zset层高**

跳表在创建节点时候，会生成范围为[0-1]的一个随机数，如果这个随机数小于0.25(相当于概率25%)，那么层数就增加1层，然后继续生成下一个随机数，直到随机数的结果大于0.25结束，最终确定该节点的层数。



**2、listpack**

压缩列表连锁更新的问题，来源于它的结构设计，所以要想彻底解决这个问题，需要设计一个新的数据结构。`Redis `在5.0新设计一个数据结构叫`listpack`，目的是替代压缩列表

- `listpack`中每个节点不再包含前一个节点的长度了，压缩列表每个节点正因为需要保存前一个节点的长度字段，就会有连锁更新的隐患。
- `listpack`采用了压缩列表的很多优秀的设计，比如还是用一块连续的内存空间来紧凑地保存数据，并且为了节省内存的开销，`listpack`节点会采用不同的编码方式保存不同大小的数据。

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202409022342178.png)

`listpack`头包含两个属性，分别记录了`listpack`总字节数和元素数量，然后`listpack` 末尾也有个结尾标识。`listpack entry` 是`listpack`的节点。

每个 `listpack`节点结构如下:

![](https://cdn.jsdelivr.net/gh/xrj123123/Images/202409022343645.png)

- `encoding`：定义该元素的编码类型，会对不同长度的整数和字符串进行编码
- `data`：实际存放的数据
- `len`：`encoding+data`的总长度

可以看到，`listpack`没有压缩列表中**记录前一个节点长度**的字段了，`listpack` 只记录当前节点的长度，当我们向`listpack` 加入一个新元素的时候，不会影响其他节点的长度字段的变化，从而避免了压缩列表的连锁更新问题。



**3、Redis事务**

redis执行一条命令的时候是具备原子性的，因为redis执行命令的时候是单线程来处理的，不存在多线程安全的问题。

如果要保证2条命令的原子性的话，可以使用`lua`脚本，将多个操作写到一个`Lua`脚本中，Redis 会把整个`Lua`脚本作为一个整体执行，在执行的过程中不会被其他命令打断，从而保证了`Lua`脚本中操作的原子性。

> 比如在用redis 实现分布式锁的场景下，解锁期间涉及⒉个操作，分别是先判断锁是不是自己的，是自己的才能删除锁，为了保证这2个操作的原子性，会通过lua 脚本来保证原子性。
>
> ```lua
> if redis.call("get", KEYS[1]) == ARGV[1] then
>     return redis.call("del", KEYS[1])
> else
>     return 0
> end
> ```



**redis事务也可以保证多个操作的原子性**

- 如果redis事务正常执行，没有发生任何错误，那么使用`MULTI`和`EXEC`配合使用，就可以保证多个操作都完成。
- 如果事务执行中有一个操作失败，就无法保证原子性了。

比如有2个操作，第一个操作执行成功，但是第二个操作执行的时候，命令出错了，那事务并不会回滚，因为Redis 中并没有提供回滚机制。

> 事务中的`LPOP`命令对String 类型数据进行操作，入队时没有报错，但是，在EXEC执行时报错了。`LPOP`命令本身没有执行成功，但是事务中的`DECR`命令却成功执行了。
>
> ```sh
> # 开启事务
> 127.0.0.1:6379> MULTI
> OK
> # 发送事务中第一个操作，LPOP命令操作的数据类型不匹配，此时不会报错
> 127.0.0.1:6379> LPOP a:stock
> QUERY
> # 发送事务中第二个操作
> 127.0.0.1:6379> DESC b:stock
> QUERY
> # 实际执行的事物，事物第一个操作执行报错
> 127.0.0.1:6379> EXEC
> 1) (error) MRONGTYPE Operation against a key holding the wrong kind of value
> 2) (integer) 8
> ```