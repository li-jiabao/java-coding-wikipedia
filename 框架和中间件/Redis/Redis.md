# Redis总结

## Redis特点

- 可用来作为分布式缓存
- 基于内存的数据库，性能高，速度快
- 支持数据持久化操作：RDB和AOF两种持久化方案
- 支持事务：事务的实现是通过MULTI和EXEC命令实现
- 主从复制，比较方便搭建分布式下的redis集群方案
- 添加了哨兵机制解决主节点宕机的问题，可以主从切换
- 具有pub/sub订阅机制，类似于消息队列
- 数据结构种类丰富效率高

## Redis快的原因

- 单线程，避免了线程切换带来的性能损耗
- 基于IO多路复用的非阻塞机制
- 完全基于内存，相比磁盘IO更快，没有磁盘IO的阻塞等待
- 底层数据结构的优化：跳表、SDS动态字符串等特殊优化过的数据结构使用

## 影响Redis性能的瓶颈

内存和网络带宽是影响性能的主要瓶颈，CPU（因为采用了单线程）并不会限制Redis的，此外时间复杂度在log(n)和O(n)的任务一般属于IO密集型任务，不属于CPU密集型，因为Redis的底层数据库都是经过特殊优化处理的

## Redis为啥改用多线程

其实后面的Redis并不是一直是单线程，执行命令是单线程，但是执行其他的任务的时候会采用多线程提高效率。引入多线程主要是为了改善IO的效率。多线程主要是用来进行数据的读写、协议解析等等操作，这些操作在多线程下可以更好的提升性能

## Redis事务

事务实现使用的几个命令：

- `multi`：执行multi之后的输入的命令会被加入到排队队列中，这些命令不会执行，如果命令出错，整个事务都会结束，不会成功加入队列
- `exec`：将multi之后输入的排队命令开始按照入队顺序一个一个执行，如果其中某个命令出错，并不会影响后续的命令执行，只有出错命令执行失败
- `watch`：监视某个key对应的缓存记录，可以解决部分并发问题
- `discard`：放弃事务块中

redis事务并不保证操作的原子性

## Redis高并发问题的解决

利用epoll的多路复用技术，一个线程可以处理大量的并发连接。epoll技术是可以解决C10K问题的技术，相比select/poll技术效率更高，因为epoll使用红黑树来快速查找那些有需要处理的时间的连接并将这些需要处理的事件使用一个链表存储，进行处理的时候只将该链表复制到用户空间进行处理

Redis的高性能：

- 单线程，不需要进行线程或者进程的切换，不需要额外的开销
- redis是基于内存的操作，不会受到IO性能瓶颈的影响
- 会影响redis性能的瓶颈：机器的内存、网路带宽

## Redis持久化

### RDB持久化方式

开启一个新的线程来 完成向RDB文件的写操作，主线程继续处理命令，使用单独的子线程来进行持久化。主线程不进行任何的IO操作 ，保证redis的高性能。缺点是可能会丢失一些数据（因为rdb的写时复制技术是需要满足某些条件之后才会进行，比如20秒内发生了多少次的写命令，如果在某个不到20秒的区间服务器出现问题，会导致这一段时间的写操作丢失）持久化文件：`dump.rdb`

写时复制技术：使用缓冲区记录写命令，满足条件之后将缓冲区内容刷入文件中

触发写rdb文件的命令：

- `BGSAVE`：后台处理，不阻塞工作线程
- `SAVE`：会导致工作线程的阻塞

优点：

-  适合大规模数据恢复
- 对数据一致性要求不高的场景很适合
- 节省磁盘空间
- 恢复速度很快

缺点：

- fork子进程需要额外一倍的空间消耗
- 可能会丢失最后一次持久化的数据
- 写时复制技术，数据量庞大时候的性能消耗也很大

### AOF持久化方式

将redis执行的每次写命令记录到单独的日志文件中，当重启redis会重新将持久化的日志文件中恢复数据。AOF重写模式：当日志文件过大时可以对其进行压缩，AOF往往效率低于RDB。不会存在数据丢失的情况。RDB和AOF都开启的情况，读取AOF的持久化文件恢复。

AOF重写机制：当文件过大，会进行文件的重写压缩减小文件大小。只保留可以恢复数据的最小指令集

重写的触发：

- AOF文件大小是上一次重写后大小的一倍且小于64MB时才触发
- 重写虽然减小了磁盘消耗，减少了恢复时间，但是进行重写也是需要消耗一定资源和时间的

重写流程：

1. `bgrewriteof`触发重写，判断当前是否有bgsave或bgrewriteof在执行，没有就执行这个命令，否则等待命令结束
2. 主进程fork一个子进程进行重写，保证主进程不阻塞

```
set k1 v1
set k2 v2

-> 重写为：set k1 v1 k2 v2
只关注最后的结果，可以减小文件的大小
```

AOF可以设置同步频率：

- always：每次写都会持久化
- everysec：每秒进行同步
- no：不进行同步，同步交给操作系统

优点：

- 可以修复AOF文件的损坏：`redis-check-aof --fix filename`修复AOF文件
- 备份更加稳健，数据丢失概率降低
- 刻度的日志文件，可以处理错误操作



缺点：

- 每一次的写操作命令都记录下来，需要较大的磁盘空间
- 恢复备份的速度比较慢
- 每次写都同步，性能压力较高
- 部分存在bug

## Redis过期键的删除策略

- 定时过期：每个设置过期事件的key都创建一个定时器，到了过期时间就会立即清除过期数据，对内存比较友好，但是会占据大量的CPU资源处理过期数据，会影响缓存的响应时间和吞吐量
- 定期过期：每隔一段时间，检查过期的key，随机的选取一部分key来判定是否需要删除
- 惰性过期：当访问某个key发现已经过期的时候才会清除，这个策略主要节省了CPU的资源，对内存非常不友好，极端情况可能会有大量过期不再被访问的key，一直存在内存没有被清除

`expire`设置过期时间

`persist`设置键永远不过期

## Redis缓存淘汰策略

- `noevicton`：当内存不足与容纳写入的数据时，写入操作会报错
- `allkeys-lru`：最常用的，最近最少使用的删除策略，当内存不足以容纳新的缓存记录，删除最近最少使用的缓存记录
- `allkeys-lfu`：最近最不常使用算法实现，从所有key中删除
- `allkeys-random`：键空间中随机删除一个key
- `volatile-lru`：设置了过期时间的键空间中，移除最近最少使用的key
- `volatile-lfu`：最近最不常使用算法实现，从所有key中删除
- `volatile-random`：设置了过期时间的键空间中，随机删除一个key
- `volatile-ttl`：内存不足，设置了过期时间的键空间中，有更早过期时间的key优先删除

## Redis主从复制

1. 服务器连接主服务器，发送sync命令，主服务器接收到sync命令后，执行`BGSAVE`命令生成RDB文件，使用缓存区记录此后执行的所有写命令
2. 主服务器创建快照文件，发送给从服务器，并在发送期间使用缓冲区记录执行的写命令，快照文件发送完毕之后，开始向从服务器发送存储在缓存区的写命令
3. 从服务器丢弃所有旧数据，载入主服务器发来的快照文件，之后从服务器开始接收主服务器发过来的写命令
4. 主服务器执行一次写命令，就向从服务器发送相同的写命令
5. 一旦主服务器挂掉，从机原地待命，一旦使用`slaveof no one`会将从机反仆为主

`slave of`将redis服务器作为从服务器。

主从复制作用和优点：

- 读写分离：从服务器只进行读操作，主服务器进行写，一主多从，大部分都是读多写少，这种配置更合理
- 高可用的基础
- 故障恢复
- 负载均衡

## Redis哨兵机制

主从节点，如果主节点挂掉了，就会重新推选选举出一个新的主节点

哨兵机制的三阶段：

1. 监控：每个哨兵实例周期的给所有的主库和从库发送PING命令，监测各个及其的运作状态，监测机器是否处于服务状态，如果没有在指定的时间内收到回复，则判定为下线。对于主节点的下线判定需要有超过一般的哨兵都判定为下线的时候，才回被标定为下线（因为网咯存在抖动问题，只有少量哨兵出现没有回复的情况是比较正常的）
2. 选主：看各个节点的打分情况，选举某个得分最高的从节点，将其选取为最新的主节点
3. 通知：将选举的最新主节点告知各个从库，此外还需要告知客户端主节点发生变更，后续的写请求需要发送到这个新选举的主节点

新的主节点的选取规则，打分的细则：

- 从节点的优先级：不同配置的从节点的优先级不一样，配置高的优先级更高
- 从节点的同步复制进度：看从节点复制进度，复制完成度更高的更容易推选为主节点
- 从节点的ID号，当前两者都一致的时候，查看redis实例启动的ID的大小，越小的先启动，选举为主节点的概率就更大

## 哨兵集群

为了保证主节点下线判定的正确率，一般需要布置哨兵集群来保证，且一般哨兵数量最好设置为奇数数量，可以保证过半数量的这个更好判定n/2+1

哨兵集群的构建方法：

1. 在redis-sentinel的conf配置文件中加入配置项：

   ```ini
   sentinel monitor <master-name> <ip> <redis-port> <quorum>
   ```

   - master-name：主节点的名字
   - ip和port：主节点ip和端口
   - quorum：客观下线的判定一句，至少有这么多个哨兵都判定为下线时才认定主节点下线

   ```ini
   sentinel down=after-milliseconds <master-name> <timeout>
   ```

   - timeout：毫秒值，哨兵超过timeout时间不能联通master或者slave，当前哨兵就判定为下线，但是master的下线判定还需要有quorum个哨兵都判定才回认定为下线（从节点不需要）

2. 借助发布/订阅组件哨兵集群。利用redis的pub/sub机制，让哨兵和主库建立连接，可以在主库发布自己的消息，也可以在主库上订阅其他哨兵发布的消息（消息队列的topic完成数据交换）

3. 哨兵想要直到所有从节点的地址：向主节点发送INFO命令即可查看主节点下配置的从节点的信息。从节点的信息在主节点中都有配置

一个哨兵节点可以手机到的信息：

- 所有哨兵节点的ip和port
- 主节点的ip和port
- 主节点挂载的所有从节点的ip和port

## 哨兵集群中选取leader

哨兵监测的流程：

- 当当前哨兵节点监测到主库下线，会发送一个`is-master-down-by-addr`命令通知其他哨兵节点，其他的哨兵节点回复yes或者no
- 只有当回复yes的节点个数不少于n/2+1个的时候才将主节点标记为客观下线，之后才进入到选举主节点的流程

选举leader的机制和要求：

- 只有当某个哨兵实例收到的yes回复超过了设置的quorum时候，当选为leader，然后这个leader负责选举主节点、通知新的主节点并与新的主节点建立联系、通知其他的从节点新的主节点是谁、通知客户端之后写请求都访问这个新的主节点
- 如果本轮没有选举出一个leader哨兵，等哨兵故障转移超时时间的2倍时间之后重新发起一轮选举流程
- 为了保证选举的顺利，首先需要保证网络的质量，此外，哨兵节点的设置最好配置奇数个节点且3个以上

某个哨兵实例的挂掉不会影响到整个选举，只是会存在一定的风险

客户端感知主从切换：

通过redis的pub/sub机制，可以让外部直到当前的切换进度，哨兵提供了一个switch-master的频道，可以让客户端订阅这个频道，客户端可以获得切换后的新主库的ip和port并与之建立连接，继续使用redis

## Redis集群

布置多台redis及其来提供缓存功能，不同redis机器的数据同步通过主从复制的方式来保证数据的统一和一致性

此外，基于主从模式的话，需要有一个redis的主节点来专门负责数据的写入操作，其他的节点通过主从复制机制从主节点上面读取同步数据

布置集群的原因：

- 单点redis提供的性能有限
- 一旦单点redis机器出现故障就没有办法提供服务了
- 但是布置集群的话，可以保证系统的高可用性，就算某个节点宕机下线，还是可以可靠的提供服务

## 大key

指那些key对应的值所占的内存空间比较大，一般认为字符串类型的key对应的value值超过10kb就算大key

大key的危害：

- 操作耗时，存在阻塞风险
- 网络阻塞，每次获取大key产生的网络流量较大
- 内存空间占用较大，集群下slot分片均匀的情况下，会出现数据和查询倾斜情况，部分有大key的节点占用内存多，QPS高
- 大key删除或过期时，会出现qps突降或突升的现象
- 极端情况，可能导致主从复制异常，redis服务器阻塞无法响应请求

大key查找（4.0之前）：

- `redis-rdb-tools`，redis实例上执行bgsave，然后通过分析生成的rdb文件找出大key
- `redis-cli --bigkeys`：统计bigkey的分布情况
- 通过scan、sscan、zscan方式删除若干元素，过期键并不能删除

大key查找（4.0之后）：

- `memory usage`命令查看
- 引入lazy free机制

## Redis分布式锁

1. 线程尝试获取锁：`setnx(lock,uid,expire)`，获取成功返回true，否则返回false
2. 获取锁成功之后，需要使用expire命令设置锁有效时间，防止死锁
3. 执行业务逻辑
4. 释放锁：首先获取lock对应的值，将这个值和uuid对比，相同就delete删除锁（这个操作需要保证原子性，需要使用lua脚本）

## Redis的lua脚本





## Redis的基本数据类型及其实现

### 字符串

int：采用类似于数组的形式存储，对于int类型的字符串就直接使用数组来存储

SDS简单动态字符串：而对于浮点数和字节类型，采用一种封装的数据结构，内部使用了char数组

```c
typedef struct sdshdr{
    // buf中已占用的字符长度
    unsigned int len;
    // buf中剩余可用的字符长度
    unsigned int free;
    // 数据空间
    char buf[];
}
// buf扩容：buf小于1M，分配给free和len一样的大小
//         buf大于1M，分配给free1M
// 惰性空间释放：字符串缩短，并不减少buf空间，而是修改指针，可能造成内存浪费
```

### list列表

两种数据结构：

- 压缩链表ziplist：元素少且内容长度不大使用ziplist。使用的连续内存空间存储数据，不过元素大小不固定，每个节点存储了上一个节点的length，在entry内部还存了当前节点的长度，方便遍历下一个节点元素
- linkedlist：双向链表

### hash哈希

采用hashtable或者ziplist进行具体的实现

- 元素较少且内容长度不大，使用压缩链表ziplist
- 其他情况直接使用hash表（和Java的HashMap实现类似，哈希冲突采用链表法来解决）

哈希表扩容：

- 为了优化一次性扩容耗时情况，将扩容操作穿插在插入操作过程中
- 在扩容之后，并不将老数据直接搬移到新表，而是在每一次插入数据的时候从就哈希表中拿出一个数据放入新表。每次插入都这样干，多次插入之后，老哈希表的数据就会一点点的全部移动到新表
- 这个称为渐进式rehash

### set集合

采用intset（整数集合）或者hashtable存储

- intset适合数据是正数类型的时候
- 不是正数类型就采用hash的实现方式，只是不需要存储值，只需要存储键即可（和Java的集合实现类似）

## zset有序集合

采用skiplist或者采用ziplist实现

- 数据量不大可以使用ziplist（节省内存）
- 合理配置下，ziplist可以大幅减少内存占用，且几乎不影响性能
- 实际就是将短结构压缩到一起（连续内存）来节省内存（减少了内存碎片，连续排列紧密且访问也很方便）

跳表：

- 创建了多层索引，用来优化查找速度
- redis中的跳表还额外保存了一个BW指针，用来做查找回退操作的
- 时间复杂度：O(log(n))

![](https://gitee.com/Jia_bao_Li/img/raw/master/img/Redis%E8%B7%B3%E8%B7%83%E8%A1%A8.png)





## 缓存一致性问题

### 缓存和数据库面对数据更新时的操作方案

缓存一致性问题指的是缓存和数据库中的数据不一致的这样一类问题，一般针对缓存一致性问题，常有的两种方法：

- 双写模式：既写数据库、又写缓存的这样一种解决
- 缓存失效模式：写数据库、删除缓存

同时不管哪一种模式，还涉及到先后顺序的问题。正常在单机情况下，不管你的操作顺序是怎么样的，只要不存在并发，最后的数据一定是一致的。

但是如果在操作两个操作的间隙，系统崩溃导致某一个操作没有完成，也会导致一定的问题：

如果先操作数据库再操作缓存，数据库操作成功之后，缓存没有写入出现了问题，这个时候两个地方的数据存在不一致，而这个时候只要缓存中的数据设置了过期时间，当缓存失效再需要使用数据的时候，就会去数据库中查找，因此这种情况还是可以保证数据的最终一致性的

如果先操作缓存再操作数据库，一旦缓存中的数据设置了过期时间，一旦缓存失效，最终的数据又会回到之前修改的那种情况，这就会导致业务出现问题，因此这种情况并不能保证数据的最终一致性

因此在操作的顺序上，一般还是选择**先操作数据库再去操作缓存**，这样可以保证数据的最终一致性，或者说可以尽量多的避免出现错误。且这种顺序下，两个操作都没有执行成功的时候，还可以进行重新执行

### 方案存在的并发问题

双写模式下存在的并发的情况会存在一种情况：

- 线程A修改了数据，先修改到数据库，然后再去修改缓存，在修改缓存操作之前，有另外一个线程也来执行了修改数据库和更新缓存的操作，然后线程A再去修改缓存，这个时候缓存的数据已经不是最新的数据，而是旧数据

失效模式在并发下也会出现问题：

- 线程A查数据库写缓存，在查完数据库准备写缓存的这段时间，另外一个线程来修改数据并删除缓存，操作完之后，线程A将查询到的旧数据写入到缓存，也会造成缓存不一致

### 那么对于这些缓存不一致的问题，如何解决？

- 缓存一致性最佳的实际解决：对于缓存设置上失效时间（如果你可以容忍一定时间内的数据不一致的话），因为设置失效时间的时候，都能保证数据的最终一致性。锁的过期时间一般设置为压测的平均执行时长的3-5倍
- 加锁：分为本地锁，分布式锁
- canal：利用数据库的binlog来通知缓存的变更，阿里的canal是一个伪造成mysql数据库的一个从库，利用mysql的主从复制来告知数据库中数据出现了更改，你需要进行相应的操作



### 主从架构存在的问题：

- 如果在主节点的数据还没有复制到slave节点的时候，主节点突然暴毙，然后这个时候某个节点懵逼上位被推举为主节点，此时由于之前设置的分布式锁主节点当前没有，有其他的线程来执行加锁就会成功）这就导致了分布式锁失效的问题
- 上述情况出现的概率很小，但也不是没可能
- Redlock是redis设计者提出的一种解决主从架构问题的算法，有争议，他提出的这个算法和哨兵节点的leader选举机制优点类似，都是需要n/2+1个实例满足某个条件的时候，才会被判定为正确

## Redlock

是redis设计者针对主从架构提出的一种算法，来解决这种问题：

主要的一个判定依据：通过不同redis实例获取锁的时间和是否成功来判定加锁是否成功

1. 客户端获取当前的时间T1
2. 使用相同的Key和Value顺序尝试从N个redis实例上获取锁
3. 客户端获取当前时间T2并减去T1获得T3。当大多数的redis实例（n/2+1）获取锁成功且获取锁所用的总时间T3小于锁的有效时间（过期时间），才被认定为加锁成功，否则失败
4. 上述获取锁成功，就需要执行业务逻辑操作共享资源，key的真正过期时间为原来的有效时间减去T3
5. 上述操作完毕之后，不管是否获取到锁，所有的redis实例都需要进行解锁的操作

## Redisson分布式锁

这是一个别人做好的现成的分布式锁框架，可以直接拿来作为实际业务的分布式锁的实现，简化我们设计分布式锁的步骤

一般加锁在try语句里面写，然后业务也在try里面干，解决的逻辑在finally语句中，不管加锁是否成功都进行解锁操作

设置分布式锁的原子性问题，因此需要使用setnx命令来保证加锁和设置过期时间这一原子操作的进行

为了保证业务不出现较多的问题，锁都需要设置一定的过期时间，如果你不设定过期时间的话，当某个业务获取到分布式锁之后，突然断电崩溃，业务直接退出，这个时候并没有执行解锁的操作，那么这个时候就出现了死锁问题，锁不会被清除，也就不再有线程可以获取到锁。如果设置了过期时间，锁过期会被自动清理。

但是在设置了过期时间的时候，可能存在某些业务比较耗时，在过期时间之内业务没有完成，这个时候，就会存在很多的问题，比如删除的锁不是自己的锁这种情况（redis 中可以使用lua脚本来进行判定是不是自己设置的锁，不是就不能进行删除）

Redisson里面还有一个比较高级的技巧，就是看门狗机制watchdog机制，这是一种自动续期的机制，如果你的业务还在继续过期时间会不断给你续期，直到你的业务完成，就不再进行续期。一般是过了1/3WatchDogTimeOut之后就会给锁续期到WatchDogTimeOut。

自动续期的实现是通过判断持有锁的线程和当前线程是不是同一个，如果是同一个，就执行续期的lua脚本，也是需要保证原子性操作

这个自动续期如果你显式的指定了过期时间的话，就不会再进行自动续期了。

Redisson还实现了很多和JUC包下面一样功能的同步类，都是加一个R开头的

## 保证原子操作的lua脚本

```
/

if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

```
// 续期的脚本
"if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
"redis.call('pexpire', KEYS[1], ARGV[1]); " +
"return 1; " +
"end; " +
"return 0;",
```

