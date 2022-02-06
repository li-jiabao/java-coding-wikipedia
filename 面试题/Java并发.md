# Java并发

## 线程和进程



## 线程安全的概念



## 线程通信方式

- volatile：保证原子性和可见性
- synchronized：保证线程安全，对于临界区资源每次只允许一个线程访问
- ReentrantLock：类似synchronized，可重入锁，可设置为公平锁和非公平锁

## 并发和并行、异步和同步概念



## 创建线程的方式



## 线程池（ThreadPollExecutor）的参数和运行流程

参数：

- `int coreThreadSize`：核心线程数，线程创建之后，会随着任务的不断创建而创建，线程数量达到核心线程数时就会将任务置于等待队列（workQueue）
- `int maxThreadSize`：最大线程数，线程池可以创建的最大线程数量，当核心线程数满和工作队列满的时候，再进来的任务就会创建新的除了核心线程数之外的线程
- `long keepAliveTime`：存活时间：当线程空闲了这个时间之后还没有任务需要分配线程，就会销毁这个线程
- `TimeUnit unit`：存活时间的时间单位
- `BlockingQueue<Runnable> workQueue`：工作队列，当核心线程数满，之后的任务都会被放入工作队列等待线程空闲
- `ThreadFactory threadFactory`：线程工厂，创建线程的工厂，这里决定了线程如何被创建
- `RejectedExecutionHandler handler`：拒绝策略，当工作队列满且达到线程数量了的时候，任务再进来的一个拒绝策略

拒绝策略：

- `ThreadPoolExecutor.abortPolicy`：

线程池执行流程：

1. 创建线程池，随着任务不断增加，不断创建核心线程数量之内的线程
2. 一旦线程数量到达核心线程数量，就会停止创建线程，将任务放入到工作队列中
3. 任务不断添加，直到工作队列满了
4. 当工作队列也满了之后，再有任务进来就直接创建一个最大线程数量之内的线程来执行任务
5. 如果工作队列满了且线程数量达到了最大线程数量，这时候就需要执行任务的拒绝策略，根据线程池创建是指定的拒绝策略来处理后面的任务
6. 直到任务执行完毕且线程数量空闲超过了指定的keepAliveTime，线程就会被销毁

![image-20210916203700227](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E6%89%A7%E8%A1%8C%E4%BB%BB%E5%8A%A1%E7%9A%84%E6%B5%81%E7%A8%8B.png)

### 线程池当任务分配给线程的方式？

通过**轮询线程池中的线程队列**来分配线程执行任务，这个分配是按照某个指定的顺序来进行任务的分配的，因此可以知道，队列中的线程不会存在某个线程一直执行任务而其他的空余线程一直空闲的情况。这个线程的顺序是通过AbstractQueuedSynchronizer的内部类ConditionObject里面的等待队列中的顺序：

线程池中的核心线程如果没有设置参数allowCoreThreadTimeout字段为true，就不会回收核心线程，默认false

```java
// 这个队列其实就是一个链表结构，节点是AbstractQueuedSynchronizer的内部类的Node
public class ConditionObject implements Condition, java.io.Serializable {
        private static final long serialVersionUID = 1173984872572414699L;
        /** First node of condition queue. */
        private transient Node firstWaiter;
        /** Last node of condition queue. */
        private transient Node lastWaiter;

        /**
         * Creates a new {@code ConditionObject} instance.
         */
        public ConditionObject() { }
}


// Node内部类，是一个双向链表
static final class Node {
    
        volatile int waitStatus;

        volatile Node prev;

        volatile Node next;

        /**
         * The thread that enqueued this node.  Initialized on
         * construction and nulled out after use.
         */
        volatile Thread thread;

        /**
         * Link to next node waiting on condition, or the special
         * value SHARED.  Because condition queues are accessed only
         * when holding in exclusive mode, we just need a simple
         * linked queue to hold nodes while they are waiting on
         * conditions. They are then transferred to the queue to
         * re-acquire. And because conditions can only be exclusive,
         * we save a field by using special value to indicate shared
         * mode.
         */
        Node nextWaiter;
}
```



## 使用线程池的优点

- 重用线程：减少线程创建和销毁带来的损耗，降低资源损耗
- 并发控制：可以控制并发量，避免资源竞争带来的很多问题
- 提高响应速度：任务到达时，不用等待线程创建，可以直接就分配线程运行
- 调优和监控





## synchronized和ReentrantLock的区别



