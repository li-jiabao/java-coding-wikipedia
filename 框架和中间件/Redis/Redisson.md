# Redisson

## 1 简介

Redisson是架设在Redis基础上的一个Java驻内存数据网格（In-Memory Data Grid）。充分的利用了Redis键值数据库提供的一系列优势，基于Java实用工具包中常用接口，为使用者提供了一系列具有分布式特性的常用工具类。使得原本作为协调单机多线程并发程序的工具包获得了协调分布式多机多线程并发系统的能力，大大降低了设计和研发大规模分布式系统的难度。同时结合各富特色的分布式服务，更进一步简化了分布式环境中程序相互之间的协作。

Redisson采用了基于NIO的Netty框架，不仅能作为Redis底层驱动客户端，具备提供对Redis各种组态形式的连接功能，对Redis命令能以同步发送、异步形式发送、异步流形式发送或管道形式发送的功能，LUA脚本执行处理，以及处理返回结果的功能，还在此基础上融入了更高级的应用方案，不但将原生的RedisHash，List，Set，String，Geo，HyperLogLog等数据结构封装为Java里大家最熟悉的映射（Map），列表（List），集（Set），通用对象桶（Object Bucket），地理空间对象桶（Geospatial Bucket），基数估计算法（HyperLogLog）等结构，在这基础上还提供了分布式的多值映射（Multimap），本地缓存映射（LocalCachedMap），有序集（SortedSet），计分排序集（ScoredSortedSet），字典排序集（LexSortedSet），列队（Queue），阻塞队列（Blocking Queue），有界阻塞列队（Bounded Blocking Queue），双端队列（Deque），阻塞双端列队（Blocking Deque），阻塞公平列队（Blocking Fair Queue），延迟列队（Delayed Queue），布隆过滤器（Bloom Filter），原子整长形（AtomicLong），原子双精度浮点数（AtomicDouble），BitSet等Redis原本没有的分布式数据结构。不仅如此，Redisson还实现了Redis文档中提到像分布式锁Lock这样的更高阶应用场景。事实上Redisson并没有不止步于此，在分布式锁的基础上还提供了联锁（MultiLock），读写锁（ReadWriteLock），公平锁（Fair Lock），红锁（RedLock），信号量（Semaphore），可过期性信号量（PermitExpirableSemaphore）和闭锁（CountDownLatch）这些实际当中对多线程高并发应用至关重要的基本部件。正是通过实现基于Redis的高阶应用方案，使Redisson成为构建分布式系统的重要工具。

在提供这些工具的过程当中，Redisson广泛的使用了承载于Redis订阅发布功能之上的分布式话题（Topic）功能。使得即便是在复杂的分布式环境下，Redisson的各个实例仍然具有能够保持相互沟通的能力。在以这为前提下，结合了自身独有的功能完善的分布式工具，Redisson进而提供了像分布式远程服务（Remote Service），分布式执行服务（Executor Service）和分布式调度任务服务（Scheduler Service）这样适用于不同场景的分布式服务。使得Redisson成为了一个基于Redis的Java中间件（Middleware）。

Redisson Node的出现作为驻内存数据网格的重要特性之一，使Redisson能够独立作为一个任务处理节点，以系统服务的方式运行并自动加入Redisson集群，具备集群节点弹性增减的能力。然而在真正意义上让Redisson发展成为一个完整的驻内存数据网格的，还是具有将基本上任何复杂、多维结构的对象都能变为分布式对象的分布式实时对象服务（Live Object Service），以及与之相结合的，在分布式环境中支持跨节点对象引用（Distributed Object Reference）的功能。这些特色功能使Redisson具备了在分布式环境中，为Java程序提供了堆外空间（Off-Heap Memory）储存对象的能力。

Redisson提供了使用Redis的最简单和最便捷的方法。Redisson的宗旨是促进使用者对Redis的关注分离（Separation of Concern），从而让使用者能够将精力更集中地放在处理业务逻辑上。如果您现在正在使用其他的Redis的Java客户端，希望Redis命令和Redisson对象匹配列表能够帮助您轻松的将现有代码迁徙到Redisson框架里来。如果Redis的应用场景还仅限于作为缓存使用，您也可以将Redisson轻松的整合到像Spring和Hibernate这样的常用框架里。除此外您也可以间接的通过Java缓存标准规范JCache API (JSR-107)接口来使用Redisson。

Redisson生而具有的高性能，分布式特性和丰富的结构等特点恰巧与Tomcat这类服务程序对会话管理器（Session Manager）的要求相吻合。利用这样的特点，Redisson专门为Tomcat提供了会话管理器（Tomcat Session Manager）。

在此不难看出，Redisson同其他Redis Java客户端有着很大的区别，相比之下其他客户端提供的功能还仅仅停留在作为数据库驱动层面上，比如仅针对Redis提供连接方式，发送命令和处理返回结果等。像上面这些高层次的应用则只能依靠使用者自行实现。

## 2 实现分布式锁

这个是Java语言可以使用的redis的分布式锁的框架                                         

锁的粒度越小越好，越小越快（就是锁锁住的数据越少越好

快速使用：

- 加入依赖，也可以直接使用springboot给redisson设置的starter

```xml
<dependency>
   <groupId>org.redisson</groupId>
   <artifactId>redisson</artifactId>
   <version>3.16.5</version>
</dependency>  
```

```java
// 1. Create config object
// 使用程序化配置方法
Config config = new Config();
config.useClusterServers()
       // use "rediss://" for SSL connection
      .addNodeAddress("redis://127.0.0.1:7181");
// 单服务器模式
Config config = new Config();
        config.useSingleServer().setAddress("192.168.50.1:6370");
// or read config from file
config = Config.fromYAML(new File("config-file.yaml")); 
// 2. Create Redisson instance

// Sync and Async API
RedissonClient redisson = Redisson.create(config);

// Reactive API
RedissonReactiveClient redissonReactive = redisson.reactive();

// RxJava3 API
RedissonRxClient redissonRx = redisson.rxJava();
// 3. Get Redis based implementation of java.util.concurrent.ConcurrentMap
RMap<MyKey, MyValue> map = redisson.getMap("myMap");

RMapReactive<MyKey, MyValue> mapReactive = redissonReactive.getMap("myMap");

RMapRx<MyKey, MyValue> mapRx = redissonRx.getMap("myMap");
```



```java
// 4. Get Redis based implementation of java.util.concurrent.locks.Lock
RLock lock = redisson.getLock("myLock");

RLockReactive lockReactive = redissonReactive.getLock("myLock");

RLockRx lockRx = redissonRx.getLock("myLock");
// 4. Get Redis based implementation of java.util.concurrent.ExecutorService
RExecutorService executor = redisson.getExecutorService("myExecutorService");

// over 50 Redis based Java objects and services ...
```

Redisson的优点：

```java
        // 获取分布式锁
        RLock lock = redisson.getLock("lock-name");
        // 加锁
        lock.lock();
        try {
            // 在这里执行业务逻辑
            System.out.println("加锁成功，正在执行业务逻辑。。。");
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            // 业务成不成功都需要释放锁
            lock.unlock();
        }
```

- 锁自动续期，有一个watchdog在不断给键续期，这个续期是在lock方法里面实现的，见下方的源码，通过不断的获取锁
- 锁业务完成，锁的过期时间就不会自动续期了，30s的过期时间，如果业务正常执行完毕，会删除锁，如果断电，锁会自动过期，也不会造成死锁的问题

相比较自己去实现锁的写法，这样的一种机制更加方便了我们的使用，就优点类似于JUC包下面的同步器锁的使用一样，使用别人封装好的代码更加简洁。

### 2.1 自动续期的源码

自动续期只有在使用lock不传入参数或者指定leasetime释放时间为-1的时候会进行

默认设置的过期时间：LockWatchdogTimeout，30s，在配置文件`Config.java`中可以修改这个时间的大小

`private long lockWatchdogTimeout = 30 * 1000;`

```java
public RedissonBaseLock(CommandAsyncExecutor commandExecutor, String name) {
    super(commandExecutor, name);
    this.commandExecutor = commandExecutor;
    this.id = commandExecutor.getConnectionManager().getId();
    // 内部设置的锁释放的时间，过期时间
    this.internalLockLeaseTime = commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout();
    this.entryName = id + ":" + name;
}
```

`lock.lock(10,TimeUnit.SECONDS)`方法调用并不会执行

自动续期的完整的一个调用链：

`lock` ----->    `lock(-1, null, false);`  ------->    `tryAcquire(-1, leaseTime, unit, threadId);` ------->

`tryAcquireAsync(waitTime, leaseTime, unit, threadId);`  --------->

`scheduleExpirationRenewal(threadId);`     ------->     `renewExpiration();`

```java
// 更新过期时间的方法定义
private void renewExpiration() {
    RedissonBaseLock.ExpirationEntry ee = (RedissonBaseLock.ExpirationEntry)EXPIRATION_RENEWAL_MAP.get(this.getEntryName());
    if (ee != null) {
        // 设置定时任务，传入的参数是一个TimerTask
        Timeout task = this.commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
            public void run(Timeout timeout) throws Exception {
                RedissonBaseLock.ExpirationEntry ent = (RedissonBaseLock.ExpirationEntry)RedissonBaseLock.EXPIRATION_RENEWAL_MAP.get(RedissonBaseLock.this.getEntryName());
                if (ent != null) {
                    Long threadId = ent.getFirstThreadId();
                    if (threadId != null) {
                        RFuture<Boolean> future = RedissonBaseLock.this.renewExpirationAsync(threadId);
                        future.onComplete((res, e) -> {
                            if (e != null) {
                                RedissonBaseLock.log.error("Can't update lock " + RedissonBaseLock.this.getRawName() + " expiration", e);
                                RedissonBaseLock.EXPIRATION_RENEWAL_MAP.remove(RedissonBaseLock.this.getEntryName());
                            } else {
                                if (res) {
                                    RedissonBaseLock.this.renewExpiration();
                                } else {
                                    RedissonBaseLock.this.cancelExpirationRenewal((Long)null);
                                }

                            }
                        });
                    }
                }
            }
        }, this.internalLockLeaseTime / 3L, TimeUnit.MILLISECONDS);
        // 在这里设置了一个定时任务，每过LockWatchdogTimeout的1/3时间就更新一次过期时间
        ee.setTimeout(task);
    }
}
```

更新过期时间的方法：

```java
protected RFuture<Boolean> renewExpirationAsync(long threadId) {
    return evalWriteAsync(getRawName(), LongCodec.INSTANCE, RedisCommands.EVAL_BOOLEAN,
                          "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                          "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                          "return 1; " +
                          "end; " +
                          "return 0;",
                          Collections.singletonList(getRawName()),
                          internalLockLeaseTime, getLockName(threadId));
}
```

不过在实际业务中，一般还是主动设置释放时间更好，不过可以设置为30s，保证业务执行完毕

### 2.2 读写锁

```java
RReadWriteLock lock = redissonClient.getReadWriteLock("  ");      // 获取读写锁
lock.readLock().lock();                            // 读锁
lock.writeLock().lock();                           // 写锁
```

读读不互斥，读写互斥，写写互斥

### 2.3 闭锁CountDownLatch

```java
package com.jiabao.jbmall.product.controller;

import org.redisson.api.RCountDownLatch;
import org.redisson.api.RedissonClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/redisson")
public class RedissonTestController {
    @Autowired
    RedissonClient redisson;

    @GetMapping("/go/{id}")
    @ResponseBody
    public String gogogo(@PathVariable Long id) {
        RCountDownLatch lock = redisson.getCountDownLatch("lock");
        lock.countDown();
        return id+"班放假了";
    }

    @GetMapping("/lock")
    @ResponseBody
    public String lock() {
        RCountDownLatch lock = redisson.getCountDownLatch("lock");
        lock.trySetCount(5);
        try {
            lock.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "都放假了，锁门了";
    }
}
```

#### 2.4 信号量Semaphore

```java
@GetMapping("/leave")
@ResponseBody
public String leave() {
    RSemaphore lock = redisson.getSemaphore("park");
    lock.release();
    return "一辆车开走了";
}

@GetMapping("/park")
@ResponseBody
public String park() {
    RSemaphore lock = redisson.getSemaphore("park");
    try {
        lock.acquire();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return "一辆车停进来了";
}
```

### 2.5分布式锁缓存一致性问题

解决缓存一致性问题：

- 双写模式：先写数据库，再写缓存
  并发下的问题：
  - 线程写数据库，再更新缓存，但是在这个更新缓存之前，另外一个线程执行了写数据库，写缓存的步骤。读到的数据有延迟（是否可以容忍数据的不一致的时间），出现了脏数据（设置过期时间的时候，一定可以保证最终一致性），但是可以保证最终的数据一致性
- 失效模式：先写数据库，再删除缓存
  并发下的问题：
  - 读到之前一个线程还未来得及删除的缓存脏数据

![image-20211205212726967](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%BC%93%E5%AD%98%E4%B8%80%E8%87%B4%E6%80%A7%E7%9A%84%E5%8F%8C%E5%86%99%E6%A8%A1%E5%BC%8F.png)

![image-20211205212645976](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%BC%93%E5%AD%98%E6%95%B0%E6%8D%AE%E4%B8%80%E8%87%B4%E6%80%A7%E7%9A%84%E5%A4%B1%E6%95%88%E6%A8%A1%E5%BC%8F.png)

面对上述的问题，有一些可以改进的方法：

- 使用分布式的读写锁：读数据等待写数据整个操作完成
- 使用canal：一种MySQL主从复制的模式来实现更新缓存的方式，通过缓存订阅MySQL的binlog，数据变更就更新缓存。利用canal还可以实现数据异构，也就是

实际场景下的一种设计方案：

- 如果是用户维度的数据，由于并发几率不高，不用过多的考虑并发带来的问题，缓存的数据只需要加上过期时间，过一段时间触发读的主动更新就可以避免数据一致性的问题
- 如果是菜单、商品介绍等基础数据，可以使用canal订阅binlog的方式，更新数据库的时候主动触发缓存更新，按照先后来执行这些更新缓存的操作
- 缓存数据+过期时间可以解决大部分业务中对于缓存的要求
- 通过加锁来保证并发的读写、写写的时候按照顺序排队，读读数据就不关心那么多，可以允许，因此这种适合使用分布式下的读写锁的使用



总结：

- 放入缓存中的数据一般本身就不应该是实时性高、一致性要求高的数据。因此缓存+过期时间是比较简单的管理方案，保证每天拿到当前最新数据即可
- 不应该过度设计，增加系统的复杂性
- 遇到实时性一致性要求高的数据，应该查数据库，即使速度慢一点