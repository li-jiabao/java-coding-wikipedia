# 多线程

## 1 什么是线程、进程？

### 1.1 进程

是计算机进行资源分配、独立运行的的最小单位，进程其实就表示一个运行的程序，操作系统运行程序实际就是进程从创建、运行到死亡的整个过程

### 1.2 线程

是CPU调度的最小单位，线程是比进程更小的执行单位，能够完成进程中的某一个功能（轻量级进程），一个进程在运行过程中可能会创建很多个线程。因为线程比进程体量小，因此线程的上下文切换远比进程上下文的切换开销小很多，而且，用户线程的切换是不需要用户态和内核态的切换的。

Java线程实际上就是操作系统线程，具体的Java线程依赖于宿主机操作系统的线程具体实现。Windows下的Java线程就是基于Win32线程库实现管理的，线程模型是1:1。（线程模型是指操作系统中用户线程和内核线程的比值）

### 1.3 进程线程的关系？

同一个进程内部的线程是共享进程的堆和方法区资源的，不同线程内部有自己的程序计数器和本地方法栈以及虚拟机栈，这些部分是线程私有的。

### 1.4 为什么线程的程序计数器需要设置成私有？

程序计数器是用来记录线程当前运行的指令的内存地址和下一条指令的，如果不设置为私有，当线程进行切换之后，就不再能够找到切换之前到底执行到哪一步骤，将会导致线程切换之后程序不能正确执行。

主要是为了记录各自线程的指令地址和下一条指令的位置，**<font color='red'>保证线程切换之后还能保证回到原来线程的执行位置</font>**

### 1.5 为什么本地方法栈和虚拟机栈设置为私有？

虚拟机栈：每个Java方法在执行的时候会创建一个栈帧用来存储局部变量、常量池引用、操作数栈等内容，方法的调用和结束就等同于虚拟机栈中的方法的入栈和出栈操作。

本地方法栈：保存Java虚拟机使用的native方法的服务的方法栈，功能和虚拟机栈是类似的。hotspot将二者合并了。

这两者设置为私有是**<font color='red'>防止线程内部的局部变量被其他线程访问</font>**，因此将这二者设置为私有

### 1.6 同一个进程下共享的区域？

共享的区域是进程的堆和方法区

- 堆：存储的是进程中的所有对象和数组，这个区域是<font color='red'>垃圾回收的重点区域</font>，这个区域在Java内部细分为好几代：<font color='red'>老年代和新生代</font>
- 方法区：Java中分代是<font color='red'>永久代</font>，存储的是<font color='red'>常量池、jvm加载的类信息、静态变量、即时编译器编译后的代码</font>

### 1.7 线程上下文切换？

当某个线程释放CPU资源时，就会有另外一个线程获取到CPU资源。CPU资源分配是通过时间片来进行的，一个CPU时间片一般是几十毫秒，实际上CPU是在不停切换线程执行任务

线程上下文的切换和操作系统内内部的进程上下文切换类似。

上下文切换：在进行线程的切换的时候，首先将当前的现成的状态保存下来（以便下次运行这个线程的时候可以再次加载线程并从上次暂停的位置重新开始执行），然后找到需要切换到的线程，并恢复该线程的状态（访问保存的线程状态并恢复到上次暂停的位置）

### 1.8 什么是并发、并行？

并发：一个CPU不停切换线程，交替执行，看起来好像是多个任务同时执行一样

并行：同一时间有多个线程在同时进行，并行的实现需要多CPU才可以

## 2 线程的状态？

- 新建NEW：新建了一个线程对象时，但是还没有执行线程的start方法
- 运行RUNNABLE：当线程执行了start方法之后，线程就进入运行状态
- 阻塞BLOCKED：当线程在想要获取锁但锁被其他线程占有的时候
- 等待WAITING：线程等待其他线程做出某些动作的时候（通知或者中断）
- 超时等待TIMED＿WAITING：可以在等待指定时间后自行返回
- 终止TERMINATED：线程执行完毕

![线程状态](https://cdn.jsdelivr.net/gh/li-jiabao/NoteImg@main/img/%E7%BA%BF%E7%A8%8B%E7%8A%B6%E6%80%81.png)

## 3 创建线程的几种方式?

Java创建线程实质其实是通过计算机操作系统底层对应的创建了一个线程、或者轻量级进程或者进程（具体看操作系统是否支持这些内容）。Linux下面对于Java的`new thread`其实是调用了`clone`的系统调用来创建线程

### 3.1 Thread

创建自定义线程类，继承Thread类并重写run方法

### 3.2 Runnable

创建一个实现Runnable接口的对象传入Thread类构造方法

### 3.3 Callable

Runnable封装的是一个异步运行的任务，你可以把他想象成为一个没有参数和返回值的一部方法。Callable和Runnable类似，但是有返回值。Callable是一个参数化的类型，只有一个方法call

执行callable的一种方法是使用FutureTask，它实现了Future和Runnable接口，运行这个任务的代码：

```Java
Callable<Integer> task = ...;
var futureTask = new FutureTask<Integer>(task);
var t = new Thread(futureTask);
t.start();
...
Integer result = task.get();
// 还有一种更加常见的情况，将Callable传入到一个执行器
```

### 3.4 线程池（Executor创建线程池）

#### 3.3.1 执行器`Executor`

执行器类有许多的静态工厂方法，用来构建线程池：(返回值是`ExecutorService`)

|               方法               |                             描述                             |
| :------------------------------: | :----------------------------------------------------------: |
|      `newCachedThreadPool`       | 必要时（有用现成的，没有再创建）创建新线程，空闲线程保留60s。线程池创建好之后立即执行各个任务 |
|       `newFixedThreadPool`       | 池中包含固定数目的线程，空闲线程一直保留。如果提交的任务多于空闲线程，未分配线程会放到队列中，等待其他任务完成任务再执行 |
|      `newWorkStealingPool`       | 一种适合fork-join任务的线程池，其中复杂任务会分解为更简单的任务。其中复杂的任务会分解为更简单的任务，空闲线程会密取较简单的任务 |
|    `newSingleThreadExecutor`     |           只有一个线程的池，会顺序的执行提交的任务           |
|     `newSceduledThreadPool`      | 用于调度执行的固定线程池,返回值是`ScheduledExecutorService`（给定延迟之后执行任务或者定期执行任务，使用DelayQueue作为任务队列） |
| `newSingleThreadSceduledExector` |  用于调度执行的单线程池,返回值是`ScheduledExecutorService`   |

使用执行器的步骤：

1. 调用执行器类的静态方法创建线程池
2. 调用submit方法提交Runnable或Callable对象
3. 保存好返回的Future对象以便获取结果或者取消任务
4. 当不想再 提交任何任务时调用shutdown

#### 3.3.2 线程池执行线程

可利用Executor类创建线程池之后，向线程池中传入Runnable或者Callable对象运行线程

```java
// Runnable运行

ExecutorService executorService = Executors.newCachedThreadPool();   // 
// ExecutorService executorService = Executors.newFixedThreadPool(5);  // 固定线程池容量
// ExecutorService executorService = Executors.newSingleThreadExecutor();  // 单线程池
for (int i = 0; i < 5; i++){   
    executorService.execute(new TestRunnable());   
}

// Callable运行处理

//1.0
List<Callable<T>> tasks = ...;
List<Future<T>> results = executorService.invokeAll(tasks);
for (Future<T> result: results){
    result.get();
}
// 第一个result.get()会阻塞直到第一个结果可用.

//2.0 
var service = new ExecutorCompletionService<T>(executorService);
for (Callable<T> task: tasks){service.submit(task);}
for (int i=0;i<tasks.size();i++){service.take().get();}

executorService.shutdown();  // 关闭线程池
```

#### 3.3.3 线程池的工作队列

- ArrayBlockingQueue：基于数组实现的有界阻塞队列，先进先出队列
- LinkedBlockingQueue：基于链表实现的阻塞队列，按照先入先出的规则排序元素，吞吐量高于Array实现的，newFixedThreadPool使用这个队列
- SynchronousQueue：只存储一个元素，阻塞队列，基于CAS，插入操作需等到另外一个线程移除操作，否则一直阻塞
- PriorityBlockingQueue：具有优先级的无界阻塞队列，底层数组，队列为空阻塞，不能拿保证优先级元素的顺序，扩容基于CAS
- DelayQueue：底层是优先级阻塞队列，无界阻塞，过期元素才会移除，基于Lock实现
- TransferQueue：生产者一直阻塞知道消费者消费了队列元素，生产者的生产能力由消费者决定

#### 3.3.4 线程池的优缺点

优点：

- 可重用线程，降低资源损耗，减少线程创建销毁带来的开销
- 提高线程的客观理性：可以有效的控制最大的并发线程数量，提高线程资源使用率，避免过多的资源竞争造成阻塞，进行线程的统一分配
- 调优和监控
- 提供定期执行、定时执行、单线程、并发数控制等功能
- 提高响应速度：任务到达时，不需要等到线程创建（线程创建需要耗一定时间的）就能立即执行

缺点：

- 容易遭受死锁、资源不足、线程泄露问题
- 请求过载：工作队列任务堆积过多（可能导致OOM）

#### 3.3.5 为什么阿里不允许使用Executors创建线程池

<font color='red'>Executors返回线程池的弊端：</font>建议使用`ThreadPoolExecutor`创建线程池

- `FixedThreadPool`和`SingleThreadPool`线程池：允许请求的队列长度为`Integer.MAX_VALUE`，可能堆积大量的请求，导致OOM
- `CachedThreadPool`和`ScheduledThreadPool`线程池：允许创建的线程数量为`Integer.MAX_VALUE`，可能大量创建线程，导致OOM发生

#### 3.3.8 `ThreadPoolExecutor`使用

三种线程池：

- `FixedThreadPool`：创建一个固定线程数量的线程池，该线程池的线程数量始终不变。线程池中有空闲线程，就会立即执行提交的任务，没有空闲线程，就会暂存到一个任务队列，等待线程的空闲边将队列任务加入线程
- `SingleThreadExecutor`：创建只有一个线程的线程池。多余的任务会被放到任务队列，线程空闲就会从队列中选择任务执行
- `CachedThreadPool`：线程数量根据实际情况调整。当任务提交时，有空闲线程，直接复用该线程执行任务，如果没有空闲线程，就会创建新的线程处理任务。所有线程在执行完任务之后，会返回线程池重复使用

```java
// 其中的一个构造器，创建线程池
public ThreadPoolExecutor
    (int corePoolSize,  // 核心线程数，定义了最小同时运行的线程数量（队列不满时）
     int maximumPoolSize, // 队列任务达到容量时，可以运行的最大线程数（队列满了就增加线程数量）
     long keepAliveTime,  // 线程数量超过核心线程数，等待一定时间还没有使用就销毁该线程
     TimeUnit unit,       // keepAliveTime的时间单位
     BlockingQueue<Runnable> workQueue,  // 达到核心线程数时，任务加入此队列
     ThreadFactory threadDactory,        // 创建新线程时用到
     RejectedExecutionHandler handler){  // 队列饱和时的拒绝策略（饱和策略）
    if (corePoolSize<0 || maximumPoolSize<=0 || maximumPoolSize<corePoolSize || keepAliveTime <0) throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

```java
// ThreadPoolExecutor线程池的使用
public class ThreadPoolExecutorTest {
    private static final int CORE_POOL_SIZE = 5;
    private static final int MAX_POOL_SIZE = 10;
    private static final int QUEUE_SIZE = 100;
    private static final long KEEP_ALIVE_TIME = 1L;

    public static void main(String[] args) {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(CORE_POOL_SIZE,
                                                             MAX_POOL_SIZE,
                                                             KEEP_ALIVE_TIME,
                                                             TimeUnit.SECONDS,
                                                             new ArrayBlockingQueue<>(QUEUE_SIZE),
                                                             new ThreadPoolExecutor.CallerRunsPolicy());
        for (int i=0;i<10;i++) {
            Runnable work = ()->{System.out.println("fff");};
            executor.execute(work);
        }
        executor.shutdown();  //关闭线程池
        while (!executor.isTerminated()) {

        }
    }
}
```

#### 3.3.7 `ThreadPoolExecutor`原理

```java
// 主要原理就是查看任务怎么提交执行的即可
public void execute(Runnable command) {
    // fail-fast机制，尽早出错是一种设计美德
    if (command == null)
        throw new NullPointerException();
    /*
         * 任务的处理主要分为三步：
         *
         * 1. 如果正在运行的线程数少于核心线程数，尝试开启一个新的线程并且将该任务作为该线程的第一个任务
         *    调用addWork方法之后会自动检查runState（运行状态）和workCount（工作线程数量）
         *    避免不能添加线程而报错，返回错误告知失败消息
         *
         * 2. 如果任务可以成功加入队列，仍然需要双重检查是否需要增加一个线程
         * 	  因为可能上一次检查已存的线程死亡了或者线程池出错
         *    因此需要重新检查状态，如果被停止，有必要的话需要回滚到入队或者没有线程创建一个
         *
         * 3. 如果不能将任务入队，尝试添加一个线程，如果失败，说明已经饱和或者shut down
         *    拒绝该任务
         */
    int c = ctl.get();
    if (workerCountOf(c) < corePoolSize) {
        if (addWorker(command, true))
            return;
        c = ctl.get();
    }
    if (isRunning(c) && workQueue.offer(command)) {
        int recheck = ctl.get();
        if (! isRunning(recheck) && remove(command))
            reject(command);
        else if (workerCountOf(recheck) == 0)
            addWorker(null, false);
    }
    else if (!addWorker(command, false))
        reject(command);
}
```

![image-20210916203700227](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E6%89%A7%E8%A1%8C%E4%BB%BB%E5%8A%A1%E7%9A%84%E6%B5%81%E7%A8%8B.png)

#### 3.3.7 线程池的饱和（拒绝）策略

- 废弃策略`ThreadPoolExecutor.abortPolicy`：抛出异常拒绝新任务的处理`RejectedExecutionException`
- 丢弃策略`ThreadPoolExecutor.DiscardPolicy`：直接丢弃任务不处理
- 丢弃最老任务策略`ThreadPoolExecutor.DiscardOldestPolicy`：丢弃最早未处理的任务
- 调用者运行策略`ThreadPoolExecutor.CallerRunsPolicy`：调用执行自己的线程运行任务。不会丢失任务请求，但是这种策略会降新任务提交速度，影响程序的整体性能。这个策略一般是通过增加队列的容量来实现任务的保留。如果任务不能丢弃且可以承受一定延迟的时候，可以使用这个策略。

## 4 为什么创建线程之后调用start而不是run方法？

当你new一个线程的时候，此时只是创建了一个线程，这个线程还是Runnable，只是处于NEW状态，想要让线程运行就需要调用start方法，这个时候会启动线程进入就绪状态，只要分配到CPU时间片就可以直接运行。

start()执行线程的准备工作，一旦分配到时间片就会自动运行run方法。

但是如果你直接调用run方法，main线程只是将run作为主线程内部的一个方法，直接运行这个方法而不是创建线程并在这个线程下面去运行run方法

## 5 Java中线程的一些特性

### 5.1 Java线程调度算法

资源抢占式。当一个线程使用完CPU之后，操作系统根据线程优先级、线程饥饿情况计算一个总的优先级并分配CPU时间片给某个线程执行。

### 5.2 Java中获取线程dump信息？

死循环、死锁、阻塞、页面打开慢，应该区分析线程dump，这是解决线程问题的一个较好的途径（线程堆栈）

- 获取线程的pid：`jps`命令或者Linux下面使用`ps -ef | grep java`
- 打印线程堆栈：`jstack pid`，Linux环境使用`kill -3 pid`

此外，Thread类提供了`getStackTrace()`方法获取线程堆栈，是一个实例方法

这只是线程堆栈的查看，线程堆栈的分析也是一个大头内容，比较复杂，也难。

### 5.3 线程安全

如果你的代码在多线程环境和单线程环境下执行永远得到相同的结果，那么这就是线程安全的

线程安全的级别：

- 不可变：Java中Stirg、Integer、Long等类都是final类型，这一类变量线程无法更改他们的值，除非新建一个
- 绝对线程安全：不管运行环境怎么样，不需要额外的同步措施都能保证线程安全。Java绝对线程安全类，CopyOnWriteArrayList、CopyOnWriteArraySet类
- 相对线程安全：Vector这一类的类，fail-fast机制（多线程访问+add）会报错ConcurrentModificationException
- 线程不安全：ArrayList、LinkedList

### 5.4 什么是线程阻塞？

几个线程共享临界区资源，当某个线程占用了临界区的资源后，需要该资源的其他线程只能进入该临界区等待，等待会导致线程挂起，一致不能工作，这种情况下就叫做阻塞。

synchronized在线程想要执行的时候，首先需要获取临界区的锁资源，得到锁才能执行，得不到锁就会进入等待而阻塞，直到其他线程释放锁，该等待线程获取锁之后才能执行

线程阻塞的解决方案：

- 减小锁的粒度，降低锁的持有时间
- 锁分离：读写锁分离
- 锁粗化、锁细化

### 5.5 线程饥饿

当某一个线程或多个线程一致获取不到资源，程序一致无法运行，这种情况称为线程饥饿

原因：

- 某个线程一致霸占资源不释放
- 线程的优先级过低一致不能分配到资源

解决方案：

- 设置合理的线程优先级避免线程饥饿的出现
- 线程饥饿本身自己过一段时间也是可能恢复运行的

### 5.6 死锁

当多个线程都被阻塞，他们中的一个或者多个线程都在等待另外一个资源的释放，这种情况下，程序没有办法正常终止，线程被无期限的阻塞。

示例：A线程持有资源2，B线程持有资源1，A等待2的释放，B等待1的释放，两个线程都想获取对方资源，但是又不可能释放，两者陷入僵持，导致死锁。

死锁的解决方案：

- 申请锁资源的时候最好使用tryLock，当一段时间还不能获取资源就直接继续运行后续的代码结束线程
- 避免一个线程同时获取多个锁，避免每个线程在持有锁时持有多个资源，保证每个锁只占用一个资源

### 5.7 活锁

线程之间礼让导致的线程问题，多个线程之间不断礼让资源，导致没有线程可以真正获取到资源并执行

### 5.8 线程的同步问题

当多个线程同时访问某一个数据的时候，如果不采取某些初始保证这个数据的同步，多个线程都对数据进行修改读取，导致最后的结果并不像你所预计的那样，而是会出现数据不对等的问题，也就时多线程和单线程下的数据访问不一致、或者丢失某些数据

多线程出现同步问题的条件：

- 有多个线程在运行
- 这多个线程之间有共享的变量或者资源
- 多线程对该资源的操作非原子性的

<font color='red'>线程同步的解决方案</font>：

- 尽量不在多线程之间使用共享变量，将不必要的共享变量变为局部变量使用
- 使用synchronized同步块或者volatile保证共享变量的安全性，还可以加锁保证线程安全
- 使用ThreadLocal为每个线程创建一个变量副本，各自线程操作自己的变量副本，独自操作互不影响

## 6 并发编程三要素

## Java内存模型JMM

![image-20210914213644396](https://gitee.com/Jia_bao_Li/img/raw/master/img/JMM.png)

### 6.1 原子性

指的是：某一个操作或者多个操作，要么就全部执行成功，要么就都执行失败，整个操作看作一个小的整体，只能一起成功或者一起失败。<font color='red'>一个线程在执行这个操作的过程中不会被其他线程干扰中断。</font>

#### 6.1.1 Java保证原子性的措施

采用锁或者CAS来保证

JVM锁是通过；`lock`和`unlock`操作来保证更大范围的原子性，并没有直接开放给用户使用，而是采用更高层次的字节码指令`monitorenter`和`monitorexit`隐式的使用`lock`和`unlock`

CAS操作时基于指令`cmpxchg`，这个指令操作由`sun.misc.Unsafe`类里面的`CompareAndSwapInt()`和`CompareAndSwapLong()`等几个方法包装提供。JUC中的atomic包下面的原子操作就是基于这些方法实现的

JMM的八个基本原子操作：

- lock锁定：变量标识为线程独占，作用于主内存
- unlock解锁：锁定的变量释放，释放后的变量才可被其他线程锁定，作用于主内存
- read读取：作用于主内存，变量值读取到工作内存，后面load读取的就是这个
- load加载：作用于工作内存，读取read读取的主内存的值到工作内存的变量副本中
- store存储：作用于工作内存，把工作内存的值传送到主内存，供write写入主内存
- write写入：作用于主内存，将store的变量存储到主内存
- use使用：作用于工作内存，将工作内存的变量传递给执行引擎
- assign赋值：作用于工作内存，将执行引擎的值赋给工作内存的变量

#### 6.1.2 处理器保证同步措施

总线锁：在某个CPU在进行数据操作的时候，将总线锁住不再允许其他CPU访问这个数据。`lock`指令

缓存锁：不需要锁住总线，只需要锁住被缓存的共享对象（缓存行），接收到lock指令，通过缓存一致性来保证数据的一致性（处理器内部的缓存和其他处理器缓存的一致性。

### 6.2 有序性

指的是：程序的操作执行严格的按照代码的先后顺序执行，<font color='red'>不会发生指令的重排序</font>

CPU为了改善性能，增加了一种乱序执行的优化手段，CPU将乱序执行后的结果重组得到最终的结果。

Java编译器也有优化手段：指令重排，指令重排的原则是重排前后在**单线程**下并不影响最终的结果，**as-if-serial**，看起来好像是顺寻执行的一样，这是指令重排的前提。

JMM中为了保证指令重排不影响执行结果，制定了一个happens-before准则：Happens-before 是 JMM 的灵魂，它是判断数据是否存在竞争，线程是否安全的非常有用的手段

存在数据依赖性的两个操作不能进行指令重排，举例：写后读，写后写，读后写（存在写操作时的两个访问变量操作就会存在数据依赖性）

#### 6.2.1 如何保证有序性

volatile和synchronized可以保证有序性.

此外JMM还有内存屏障的概念来保证有序性：

- loadload：读读屏障
- loadstore：读写屏障
- storestore：写写屏障
- storeload：写读屏障

CPU层面有三个：

- 读屏障
- 写屏障
- 全能屏障

#### 6.2.2 happens-before规则

《并发编程的艺术》：

> Happens-before 是 JMM 最核心的概念。对应 Java 程序员来说，理解 Happens-before 是理解 JMM 的关键。

《深入理解Java虚拟机》：

> Happens-before 是 JMM 的灵魂，它是判断数据是否存在竞争，线程是否安全的非常有用的手段。

JMM：

> 1）如果一个操作 Happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。
>
> 2）两个操作之间存在 Happens-before 关系，并不意味着 Java 平台的具体实现必须要按照 Happens-before 关系指定的顺序来执行。如果重排序之后的执行结果，与按 Happens-before 关系来执行的结果一致，那么这种重排序并不非法（也就是说，JMM 允许这种重排序）

### 6.3 可见性

某个线程对某个共享资源的修改操作对于另外一个线程可以立即看到这个修改的结果，<font color='red'>线程修改操作立马可见</font>

#### 6.3.1 保证可见性措施

缓存一致性协议：其中常提到的就是MESI协议

JMM中可以使用volatile关键字，synchronized和final关键字也可以保证可见性。

JMM规定的执行8种基本原子操作时准则：

- 在unlock之前，必须先把变量同步回主内存（执行store write操作）

## 7 Java并发同步相关的内容

### 7.1 什么是CAS？

比较并交换，CAS是一种乐观锁的机制，在每次需要修改共享变量的时候，线程并不会阻塞等待另外线程修改完毕之后再去读取或者修改，而是直接取出内存中的值，进行操作之后和目标值进行比对，如果是相等的，说明此时并没有人对这个值进行修改，就可以直接操作并将值写回内存，如果不相等，则一直循环比较，直到和目标值相同。

**CAS存在两个问题：**

- 操作的**原子性问题**：如果在比较完写回内存的这个时候有另外的线程访问修改，可能会导致数据不一致的问题。CAS底层的实现（操作系统层次也有CAS操作）是通过lock锁总线的方式防止原子性问题的出现
- CAS的**ABA问题**：这一问题是指在我进行比较的时候有另外一个线程访问了这个共享变量，随后又将这个值改回为原来的值，这个时候在当前线程看来值仍然是原来的值，但是实际上是被修改访问过了。这成为ABA问题，这一类问题怎么解决？可以通过给变量加一个版本号，每一次访问修改变量都更新一次版本号，只有当版本号和值都想同时才更新内存的值。JUC的atomic包下面有AtomicStampedReference，可以用来设置版本号解决这个问题

### 7.2 什么是自旋锁？

当持有锁的线程可在短时间内释放锁资源的时候，等待锁资源的线程就不必要再去进行内核态和用户态之间的切换，而只需要自旋（等待一小段时间），等到线程释放所资源之后，就可以直接获取到锁而开始运行，这种方式避免了内核态和用户态之间切换带来的消耗。

不过自旋也是需要消耗CPU资源的，如果一段时间线程还没有释放锁资源，等待的线程会一直自旋，一直消耗CPU资源，反而得不偿失，可以设定一定的自旋等待时间。

当线程竞争过大时，自旋锁的效率可能比重量级锁的效率更低，因此需要看并发量和锁竞争程度看是否采用自旋锁的方式提高资源利用率，降低资源消耗。

### 7.3 什么是重量级锁、轻量级锁？

重量级锁就是基于操作系统的Mutex Lock互斥锁实现的一类锁，这类锁机制下，线程的切换需要经历用户态到内核态的切换。这一切换操作对资源消耗很大因此线程竞争不激烈的时候可以考虑不使用重量级锁转向使用轻量级锁（自旋锁）

轻量级锁是相对于重量级锁来说的：减少了内核态和用户态切换带来的消耗。

获取轻量锁的过程：竞争锁的线程首先需要拷贝对象头中的Mark Word到帧栈的锁记录中。拷贝成功后使用CAS操作尝试将对象的Mark Word更新为指向当前线程的指针。如果这个更新动作成功了，那么这个线程就拥有了该对象的锁。如果更新失败，那么意味着有多个线程在竞争。 

当竞争线程尝试占用轻量级锁失败多次之后（使用自旋）轻量级锁就会膨胀为重量级锁，重量级线程指针指向竞争线程，竞争线程也会阻塞，等待轻量级线程释放锁后唤醒他。

### 7.4 公平锁和非公平锁

公平锁：指严格的按照先进先出队列来进行服务

非公平锁：不考虑队列先后，而是每次先尝试获取锁，如果获取到直接执行，获取不到再加入队列尾部

<font color='red'>非公平锁的性能是公平锁性能的5~10倍</font>，因为队列中的线程获取到锁的过程是需要一定时间的，如果这个时候正好有线程直接可以尝试获取锁而直接运行，就减少了这一部分时间的损耗，从而提高了效率和吞吐量

### 7.5 乐观锁和悲观锁

乐观锁：在多线程下，认为我的值并不会被其他线程访问修改，不对数据的操作加锁，而是采用CAS这一类机制来保证数据的同步。这减少了锁的使用，省去了加锁去锁的操作，提高了效率

悲观锁：认为多线程下，谁都会对我的数据进行访问修改，采取一种悲观的态度，对数据操作的过程增加一把锁，从而确保同一时间不会有多个线程同时修改数据。保证了数据的独占性和正确性

### 7.6 独占锁和共享锁

独占锁：指这把锁只能同时有一个线程持有，读写锁里面的写锁就属于独占锁，每次只能有一个线程进行写操作

共享锁：允许一个锁可以有多个线程同时持有，读写锁里面的读锁就属于共享锁，允许同时有多个线程进行读操作

## 8 JVM层的同步机制

### 8.1 Java对象组成

- markword：
  - GC标记信息：用于垃圾回收的对象标记
  - 锁信息：无锁001，偏向锁101，轻量级锁00，重量级锁10
  - hashcode信息
  - 分代年龄信息（最大为15）

![image-20210913235949432](https://gitee.com/Jia_bao_Li/img/raw/master/img/Java%E5%AF%B9%E8%B1%A1%E5%A4%B4markword.png)

- 对象类型指针：有压缩4字节，无压缩8字节，指向的是方法区的内存区域所属的Klass类指针。
- 数组长度（数组类型专属的组成）：记录的是数组的长度
- 实例数据：存储了类定义的对象信息，包含了父类继承的信息
- 对象填充padding

### 8.2 锁的四种状态

无锁->偏向锁->轻量级锁->重量级锁

Java对象锁信息的查看：对象头的markword里面保存了对象的锁信息，可以通过JOL（Java对象布局包）查看对象头信息，对象头信息里面就包含了markword信息，里面就有锁信息。jvm是默认开启偏向锁的，但是jvm的偏向锁实际上是设置了延迟（4s）才开启。JDK在1.8中，默认的时轻量级锁：设置`XX:BiasedLockingStartupDelay=0`立即开启偏向锁

**偏向锁**：比轻量级还轻的一种锁，用来减少线程锁重入（CAS）的开销。存在多线程竞争时，偏向锁会被撤销升级为轻量级锁。偏向锁的目的是提高一个线程执行同步块时的性能（减少cas的次数）

**轻量级锁**：相对重量级锁而言，没有内核态和用户态的切换。一般轻量级锁用在没有多线程竞争或者多线程竞争不激烈的情况，减少重量级锁的性能消耗，一般用在线程交替执行的同步块上。如果出现了同一时间多个线程竞争锁的情况，轻量级锁就会膨胀为重量级锁。

**重量级锁**：依赖于操作系统Mutex Lock互斥锁实现的，操作系统线程切换需要用户态到内核态的切换，需要消耗一定的资源，效率不高。synchronized的monitor锁就是重量级锁

### 8.3 锁升级

锁只存在升级不存在降级：JVM自动完成锁升级。GC的时候才会出现锁降级

- 无锁->偏向锁：可以设置偏向锁的开关

- 偏向锁->轻量级锁的升级：

  - 线程A请求锁，发现对象的MarkWord是无锁状态，尝试CAS设置为偏向锁状态，并写入线程A的ID

  - 线程B也来请求锁，发现MarkWord已经是偏向锁状态，检查线程A是否存在

  如果此时线程A已经不存在

  - 尝试CAS设置为偏向锁状态，并写入线程B的ID

  如果此时线程A存在

  - 暂停线程A
  - 在线程A的栈帧中创建锁记录(Lock Record)
  - 将MarkWord复制到该锁记录中
  - 尝试CAS更新MarkWord，指向该锁记录
  - 更新锁记录的Owner指向MarkWord(?)
  - 设置MarkWord为轻量级锁状态
  - 此时MarkWord与DisplacedMarkWord存储了相同的内容(?)
  - 继续执行线程A
  - 线程B自旋来获取锁

- 轻量级锁->重量级锁的升级：当有存在多个线程同时竞争的时候，锁就会由轻量级锁升级为重量级锁线程A栈帧的锁记录已经复制了MarkWord，并且MarkWord指向了该锁记录

  - 线程B来请求锁，发现MarkWord已经是轻量级锁，尝试自旋(?)

  - 线程B自旋之后还是获取不到锁(多线程竞争下，获取锁需要自旋一段时间时，就会锁膨胀)

  - 更新MarkWord，指向重量级锁(Mutex Lock)(?)
  - 设置MarkWord为重量级锁状态
  - 阻塞线程B

  - 线程A尝试CAS用DisplacedMarkWord替换当前的MarkWord，CAS失败

  - 释放锁
  - 唤醒阻塞的线程

### 8.4 volatile的底层机制

#### 8.4.1 底层机制

CAS保证可见性：`lock addl`底层指令保证CAS操作的原子性

内存屏障保证有序行：JVM内存屏障和操作系统内存屏障保证有序性

#### 8.4.2 什么是HAPPENS-BEFORE原则？

如果一个操作happens-before另外一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前

存在happens-before原则的两个数据存在数据依赖性，存在数据依赖性关系的两个操作执行顺序不能改变

#### 8.4.3 什么是AS-IF-SERIAL?

CPU为了保证CPU的利用率，会对某些指令进行重新排序，不一定按照指定的顺序执行下来。

如果不管你怎么重排序，对最后的结果不会产生影响的这种状况，就属于是as-if-serial，也就是看起来像是顺序执行

两个操作之间存在happens-before关系，并不一定按照该原则指定的顺序执行，如果重排之后的执行结果和原则指定顺序所获得结果一直，这种重排不非法。这一种情况也可称为as-if-serial

### 8.5 synchronized的底层机制

同步块：反编译之后的class文件可以看到，同步块代码由monitorenter和monitorexit包裹。底层就是通过monitor对象来实现的同步。

同步方法：反编译之后右一个标志位ACC_SYNCHRONIZED，有这个标志位时，就需要先尝试获取monitor，获取到才能执行方法，方法执行时，其他线程都不能获取到同一个monitor。

底层就是monitor对象的争抢来保证线程同步。

JDK1.5之前，synchronized就是直接使用的重量级锁，这个锁操作系统的底层就是Mutex Lock实现。

1.5之后，JDK优化了synchronized，增加了偏向锁、自适应自旋锁、轻量级锁

### 8.6 锁优化

- 锁消除：对于不可能出现多线程要求的代码，不采用锁，锁只在需要多线程安全的时候使用
- 锁细化：降低锁的粒度，也就是让需要锁才能执行代码块尽量小
- 锁粗化：往往有时候不是越细越好，如果锁持有时间太短，不断请求释放对于资源损耗也很大
- 锁分离：将不相互影响的操作分离开来加锁，像读写方法，读写锁就是采用这种优化方案设计的
- 减少锁持有时间：对于不需要锁的代码，可以放在非同步代码块里面，同步代码块只是需要线程安全的代码

## 9 JUC并发同步包

### 9.1 AQS

AQS是一个用来构建锁和同步器的框架，使用AQS能能够简单高效的构造同步器。JUC包下面的很多锁和同步器就是基于AQS构建得到的，如：ReentrantLock、Semaphore、ReentrantReadWriteLock、SynchronousQueue、FutureTask等

#### 9.1.1 AQS原理

如果被请求的共享资源空闲，就将申请该共享资源的线程设置为有效工作线程，并将共享资源设置为锁定状态。如果请求的共享资源被占有，需要定义线程阻塞等待和被唤醒时锁的一个分配机制。通过CLH队列锁是西安的：将暂时获取不到的线程加入到队列中。

> CLH队列是一个虚拟双线队列：AQS将每条请求贡献资源的线程封装成为一个CLH队列一个节点来实现锁的分配

![image-20210916132457913](https://gitee.com/Jia_bao_Li/img/raw/master/img/AQS%E5%8E%9F%E7%90%86.png)

AQS中共享资源设置了一个int变量state来表示资源状态`private volatile int state;`。AQS使用CAS机制来实现对该同步状态进行原子操作修改值，state的操作通过`getState()、setState()、compareAndSetState()`，这三个方法设置为protected：

```java
private volatile int state;

protected final int getState() {
	return state;
}

protected final void setState(int newState) {
	this.state = newState;
}

protected final boolean compareAndSetStatw(int expect,int update) {
    return Unsafe.compareAndSwapInt(this,stateOffset,expect,update);
} 
```

#### 9.1.2 资源共享方式

- 独占式：只有一个线程能够执行，如ReentantLock实现。又分为两种公平和非公平：
  - 公平锁：队列中的先后顺序来选取线程执行，先到先执行
  - 非公平锁：锁可用时，线程都去竞争锁，谁竞争到就谁执行
- 共享式：可以多个线程同时执行。Semphore、CountDownLatch、CyclicBarrier

对于AQS自建同步器，只需要实现资源state的获取和释放方式，队列的维护AQS已经实现好了

#### 9.1.3 AQS自定义同步器

AQS底层使用了模板方法模式，使用AQS自定义同步器的步骤：

1. 使用者继承AbstractQueuedSynchronizer并重写指定的方法（对资源state的获取和释放）
2. 将AQS组合再自定义同步器的实现中，并调用模板方法，这些模板方法会调用使用者重写的方法

![image-20210916134118360](https://gitee.com/Jia_bao_Li/img/raw/master/img/AQS%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95.png)

以ReentrantLock举例：state为0时说明资源处于空闲，此时可以分配一个线程作为工作线程`setExclusiveOwnerThread(Thread.currentThread())`，分配之后将state设置为1，只不过此时的state可以继续被当前线程访问，state会+1

以CountDownLatch举例：state表示这state线程是并行的，只要等这些线程全部执行完毕也就是state变为0时，释放资源unpark()某个线程，该线程就会从await处恢复执行。每个线程执行完毕之后执行`countDown()`，state就会CAS并减一。

### 9.2 ReentrantLock

基于CAS自旋实现的锁，用来保证线程安全。可重入的锁

lock、tryLock、lockInterruptly获取锁：

- `lock()`如果锁不可用就阻塞当前线程，可用就返回true
- `tryLock()`：尝试获取锁，获取不到就执行后续的非同步代码块
- `lockInterruptly()`：如果当前线程未被中断，获取锁，表示等待获取锁的线程可被中断，如果线程被中断会报异常，lock方法并不会报异常

支持轮询：

- `Condition newCondition()`：条件对象，获取等待通知组件，可以通知指定的线程，也可以通知单个线程或者全部线程，Condition对象和当前锁是绑定的，当线程获取了锁的时候，可以调用condition对象的await方法，释放锁，可以调用`signal()`和`signalAll()`唤醒等待线程
- condition的唤醒可以是指定条件的线程，而Object类中的notify方法是随机唤醒的。

支持公平锁：

- `new ReentrantLock(true)`开启公平锁，默认关闭

### 9.3 ReadWriteLock



### 9.4 Semphamor

信号量：允许多个线程同时访问，在创建信号量的时候指定可以几个线程同时访问。将Semphore的N设置为1就实现了互斥锁。

`acquire()`获取资源，可以获取到资源返回true，否则返回false。没有获取到锁并不会阻塞当前线程

`release()`释放资源，将线程占用的资源释放

Semphore和ReentrantLock的使用方式类似，且Semphore可以完成ReentrantLock的所有工作，acquire获取锁（可响应中断），release释放锁。

和ReentrantLock的唯一区别就是Semphore支持共享访问方式，多个线程并行，而ReentrantLock是互斥锁，只能一个线程获取资源。

### 9.5 CyclicBarrier

等待所有的线程都到达CyclicBarrier的时候，才让所有的线程开始执行。可以采用这个工具类实现多个线程的**同时**执行

await方法是将当前线程阻塞，等到barrier内部的所有线程都到达await，再让所有的线程从await之后开始执行。

比如跑步比赛：当所有的选手全部进入跑道的时候才开始计时比赛，这个时候就可以使用CyclicBarrier来实现这一操作。

```java
public class UseCyclicBarrier {
    
    private static CyclicBarrier barrier;

    public static class Runner extends Thread {
        @Override
        public void run() {
            try {
                Thread.sleep((int)(Math.random()*1000));
            }catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(this.getName() + "进入赛道了");
            try {
                barrier.await();
            }catch (InterruptedException e) {
                e.printStackTrace();
            }catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
            long start = System.currentTimeMillis();
            try {
                Thread.sleep((int)(100 + Math.random()*1000));
            }catch (InterruptedException e) {
                e.printStackTrace();
            }
            long end = System.currentTimeMillis();
            System.out.println(this.getName() + "冲线了，耗时"+(end-start));
        }
    }

    public static void main(String[] args) {
        // 创建CyclicBarrier对象的两种方式，一种是传入一个int表示支持多少个线程，另外一种额外传入一个Runnable对象
        barrier = new CyclicBarrier(5,()-> System.out.println("所有选手进入赛道，比赛开始"));
        for (int i=0;i<5;i++) {
            Thread t = new Runner();
            t.setName("选手"+i);
            t.start();
        }
    }
}
```

### 9.6 CountDownLatch

CountDownLatch是指等到countdown，当每次countdown方法运行都会减少一个countdown数组，直到该数字为0时，才会释放锁，将await方法阻塞的线程恢复运行。利用countDLatch的await阻塞等待性质，可以实现多个线程的同时执行

可以用来简单的记录跑步比赛成绩排名的实现：

```java
public class UseCountDownLatch {
    
    private static Map<String,Long> map;
    private static CountDownLatch order;
    private static CountDownLatch match;
    
    public static class Runner extends Thread {
        @Ovveride
        public void run() {
            System.out.println(this.getName() + "选手正在等待起跑指令");
            order.await();  // 选手等待指令的实现
            System.out.println(this.getName() + "选手起跑了");
            long start = System.currentTimeMillis();
            try {
                Thread.sleep((int)(1000+Math.random()*1000));
            }catch (InterruptedException e) {
                e.printStackTrace();
            }
            long end = System.currentTimeMillis();
            System.out.println(this.getName() + "选手冲线了");
            map.put(this.getName(),end-start);
        }
    }
    
    public static void main(String[] args) {
        order = new CountDownLatch(1);  // 指定发令员
        match = new CountDownLatch(5);  // 设置参赛选手数量
        map = new LinkedHashMap<>();   // 设置比赛记录器，按照冲线顺序记录
        for (int i=0;i<5;i++) {
            Thread t = new Runner();
            t.setName("选手"+i);
            t.start();
        }
        System.out.println("预备~跑");
        order.countDown();  // 发令员发令
        match.await();
        int count = 1;
        System.out.println("正在统计成绩");
        for (Map.Entry<String,Integer> entry:map.entrySet()) {
            System.out.println("第"+count+"名："+entry.getKey()+"耗时"+entry.getValue());
            count++;
        }
    }
}
```

### 9.7 Exchanger

用于两个线程之间交换数据的一个同步类。可以对多个变量进行交换，只需要在线程内部调用同一个exchange实例对象即可。

```java
public class UseExchanger{
    private static Exchanger<String> exchange = new Exchanger<>();
    
    public static void main(String[] args) {
        new Thread(()->{
            String s = "i am t1";
            String hello = "hello wordl t1";
            try {
                s = exchange.exchange(s);
                hello = exchange.exchange(hello);
            }catch (InterruptedException e) {
                e.printStack();
            }
            Syetem.out.println(this.getName() + "s" + s + "hello" + hello);
        },"T1").start();
        new Thread(()->{
            String s = "i am t2";
            String hello = "hello wordl t2";
            try {
                s = exchange.exchange(s);
                hello = exchange.exchange(hello);
            }catch (InterruptedException e) {
                e.printStack();
            }
            Syetem.out.println(this.getName() + "s" + s + "hello" + hello);
        },"T1").start();
    }
}
```



### 9.8 Phaser

当某个操作需要分为多个阶段，每个阶段又需要多个线程参与的时候，可以使用Phaser类

Phaser类相当于CyclicBarrier和CountDownLatch的功能整合。

比如整个婚礼活动，这个活动分为了好几个阶段：

- 所有人员的出席
- 婚礼进行
- 婚宴开席
- 嘉宾离场
- 新郎新娘洞房

利用Phaser类模拟阶段行进的流程：

- 继承Phaser类
- 重写onAdvance方法。这里定义阶段前进的
- 当所有的线程都到达barrier的时候，就会自动调用onAdvance方法阶段行进
- 整个阶段是定义好的，必须从数字0开始
- onAdvance传入的参数：phase是当前处于第几阶段，registeredParties是目前阶段参加的线程。每个阶段都有打印值，返回值是false，一直到最后一个阶段返回true，所有线程结束
- 线程暂停等待的方法：`arriveAndAwaitAdvance()`
- `register()`方法是注册当前阶段参与人（线程），`arriveAndDeregister()`撤销参与人（线程）
- 线程的run里面包含有对应的阶段需要执行的任务

```java
public class UsePahser {
    
    private static Phaser phaser;
    public static class Person extends Thread {

        private String name;

        public Person(String name) {this.name = name;}

        public void present() {
            System.out.println(name+"出席婚礼现场");
            phaser.arriveAndAwaitAdvance();
        }
        public void wedding() {
            System.out.println(name+"正在参加婚礼现场");
            phaser.arriveAndAwaitAdvance();
        }
        public void eat() {
            if (!"前男友".equals(name)||!"前女友".equals(name)) {
                System.out.println(name+"正在吃席");
                phaser.arriveAndAwaitAdvance();
            }else {
                System.out.println(name+"参加完婚礼现场后很不开心，直接离席不吃了");
                phaser.arriveAndDeregister();
            }
        }
        public void leave() {
            System.out.println(name+"离席");
            phaser.arriveAndAwaitAdvance();
        }
        public void love() {
            if (name=="bride"||name=="bridge") {
                System.out.println(name+"正在洞房");
                phaser.arriveAndAwaitAdvance();
            }else {
                phaser.arriveAndDeregister();
            }
        }
        @Override
        public void run() {
            present();
            wedding();
            eat();
            leave();
            love();
        }
    }

    public static class WeddingPhaser extends Phaser {
        @Override
        public boolean onAdvance(int phase,int registeredParties) {
            switch (phase) {
                case 0:
                    System.out.println("所有人都出席了"+registeredParties);
                    System.out.println();
                    return false;
                case 1:
                    System.out.println("所有人都参加了婚礼"+registeredParties);
                    System.out.println();
                    return false;
                case 2:
                    System.out.println(registeredParties+"参加了婚宴");
                    System.out.println();
                case 3:
                    System.out.println("离席了"+registeredParties);
                    System.out.println();
                    return false;
                case 4:
                    System.out.println("新郎新娘入洞房卿卿我我");
                    System.out.println();
                    return true;
                default:
                    return true;
            }
        }
    }

    public static void main(String[] args) {
        phaser = new WeddingPhaser();
        phaser.bulkRegister(10);
        for (int i=0;i<6;i++) {
            new Person("嘉宾"+i).start();
        }
        new Person("前男友").start();
        new Person("前女友").start();
        new Person("bride").start();
        new Person("bridge").start();
    }
}
```

### 9.9 ThreadLocal

通常情况下，创建的变量是可以被任何一个线程访问修改的，如果想实现每一个线程都有自己的专属本地变量的时候，就需要使用ThreadLocal类了。Threadlocal是让每个线程绑定自己的值。

如果创建一个ThreadLocal变量，那么访问这个变量的每个线程都会有这个变量的本地读本，在线程内部有一个ThreadLocalmap，线程通过set和get方法来获取值或者修改当前线程内部的副本的变量值，从而避免线程（线程争抢资源带来的）的安全问题。

#### 9.9.1 原理

```java
public class Thread implementd Runnable {
    
    // 和此线程相关的ThreadLocal值，ThreadLocal类维护
    ThreadLocal.ThreadLocalMap threadLocals = null;
    // 和此线程有关的InheritableThreadLocal值，InheritedThreadLocal类维护
    ThreadLocal.ThreadLocalMap inheritedThreadLocals = null;
    
}
```

ThreadLocal类其实就是在内部维护了一个ThreadLocalMap类，这个Map是一个类似于Map的数据结构：key是当前对象的Thread对象，值是Object对象

常用的ThreadLocal的使用场景：

- session管理
- 数据库连接管理

```java
private static final ThreadLocal threadSession = new ThreadLocal();

public static Session getSession() throws InfrastructureException {
    Session s = (Session) threadSession.get();
    try {
        if (s==null) {
            s = getSessionFactory().openSession();
            threadSession.set(s);
        }
    }catch (HibernateException ex) {
        throw new InfrastructureException(ex);
    }
    return s;
}
// 保证相同线程获取的Session是同一个
```

#### 9.9.1 ThreadLocal内存泄露问题

ThreadLocalMap中使用的key是ThreadLocal的弱引用，value是强引用。如果ThreadLocal没有外部强引用的使用，垃圾回收会将key回收，value不会。这样就会出现key为null的Entry，如果不处理，value永远不会被GC垃圾挥手，这个时候就会产生泄露。

ThreadLocalMap的实现已经考虑了这一类情况，在调用set、get、remove方法的时候，会清理掉key为null的记录。

使用了ThreadLocal后，最好手动调用remove方法

> 对象只拥有一个弱引用，类似于可有可无的对象，弱引用和软引用的区别：弱引用的生命周期更短，垃圾回收期扫描到只具有弱引用的对象时，就会回收这一部分内存，只不过垃圾回收线程并不一定立马发现（因为垃圾回收线程的优先级不高。
>
> 弱引用可以和引用队列联合使用。如果弱引用引用的对象被垃圾回收，Java虚拟机就会将这个弱引用加入到与之关联的引用队列中。

### 9.10 Atomic

保证了原子操作的原子类，基于CAS实现。引用了Unsafe类里面的compareAndSwapInt()等方法实现的

### 9.11 LockSupport

LockSupport的park和unpark方法用来阻塞和释放线程

## 10 synchronized和ReentrantLock的异同

### 10.1 相同点

- 都是可重入锁（迭代锁）
- 都是阻塞式加锁方案

### 10.2 不同点

- synchronized是jvm层级的实现，而ReentrantLock是javaAPI层面的
- synchronized是基于重量级锁以及相关的一些锁优化实现的
- ReentrantLock则是利用CAS自旋机制保证数据可见性和操作的原子性
- ReentrantLock可实现公平锁和非公平锁
- ReentrantLock可中断`lock.lockInterruptly()`
- ReentrantLock的condition对象可以实现唤醒指定的线程，而synchronized只能唤醒一个或者全部线程
- ReentrantLock加锁和释放都是自主的，synchronized则是jvm操作
- ReentrantLock锁的是线程，而synchronized锁的是特定的monitor对象

## 11 引用的强软弱需

- 强引用：new创建出来的对象的引用，强引用是生活必需品，就算Java内存不足，也**<font color='red'>不会回收Java的强引用</font>**，宁愿排除OOM异常，使程序终止。
- 软引用：可有可无的生活用品，当内存充足的时候，JVM不会回收具有软引用的对象。**<font color='red'>内存不足的时候，就会回收软引用引用的对象。</font>**只要该对象没有被垃圾回收器回收，就还可以继续使用。软引用可以和一个引用队列联合使用，如果软引用的对象被垃圾回收，JVM就把该软引用加入到与之关联的引用队列中
- 弱引用：可有可无，和软引用不同的时，只要垃圾回收器发现这个弱引用对象，**<font color='red'>不管怎么样，都会回收这个对象内存，不过因为垃圾回收线程的优先级不高，不一定可以立刻发现这个需要回收的对象</font>**。弱引用可以和一个引用队列联合使用，如果弱引用对象被回收，JVM就会把该引用加入到与之关联的引用队列中
- 虚引用：虚引用并不会决定对象的生命周期。如果一个对象仅仅持有虚引用，就和没有任何引用一样，任何时候都可能被回收。虚引用和软、弱引用区别是：必须和引用队列搭配使用。**<font color='red'>虚引用用来跟踪对象被垃圾回收的活动</font>**

当垃圾回收期回收一个对象时，如果发现它有虚引用，就会在回收之前将虚引用加入到与之关联的引用队列。程序可以通过引用队列中是否添加虚引用来判断对象是否将被回收。程序如果发现引用队列中有某个虚引用，就可以在该对象被回收前采取必要的行动

程序设计中很少使用弱引用和虚引用，使用软引用更多一点。因为**<font color='red'>软引用可以加速JVM对垃圾回收的回收速度</font>**，维护系统的安全运行，防止OOM等问题产生

## 12 DCL单例模式的实现？

Double Check Lock：双重检查锁，就是实现单例的时候使用了两重if判断检查单例是否为null，为null就需要创建实例对象。第一种检查是为了减少锁的竞争提高单例创建获取时的效率，避免了多个线程竞争锁消耗大量的资源。

DCL单例模式的volatile关键字的主要作用是禁止指令的重排序。创建对象的时候，实际上JVM将其分解为了好几个步骤，这几个步骤是有可能出现乱序执行的情况的，为了防止实例对象创建时，astore_1指令跑到了init前面执行，就会导致内存中现在的值是null，这个时候如果有其他线程正在等待运行的化，就会进入该线程的对象创建流程中，最终这两个对象之间并不是同一个实例：

- `3:dup`是为对象分配内存
- `4:invokespecial`填充对象信息，创建对象实例调用init+数据初始化+末位填充
- `7:atore_1`：将引用指向对象的堆内存中

![image-20210917000850378](C:%5CUsers%5Cjiabao%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210917000850378.png)![image-20210917000850378](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%8F%8D%E6%B1%87%E7%BC%96%E6%9F%A5%E7%9C%8B%E8%BF%87%E7%A8%8B.png)

## `Thread.sleep(0)`的作用

因为Java线程采用抢占式线程调度算法，有时候会出现优先级低的算法一致获取不到CPU资源而一致未能执行的情况，为了让优先级低的线程可以获得资源执行，会使用`Thread.sleep(0)`，<font color='red'>手动触发一次CPU分配时间片的操作</font>，这也是平衡CPU控制权的一种操作

## 什么是FALSE-SHARING?

### 缓存行

内存和缓存进行数据交换的时候，是按照缓存行进行交换，也就是每次交换数据，就算所读取的变量没有占据一个缓存行，也会将这一整个缓存行读取。一般一个缓存行的字节数是64字节（64位电脑）普遍是32-128字节

正是由于缓存行的这种读取数据的特性，可能在读取某个变量的时候，把另外一个变量也给读取进来，这个时候如果出现多线程访问该缓存行的两个变量的时候，一个线程会阻塞另外一个线程的读取（同一个缓存行的变量不能同时被多个线程读取或者修改）

### 如何避免缓存行导致的伪共享？

- 字节填充：也就是将你需要读取的变量前后都填充特定数量的字节数，使得你所需要访问的变量和附近的变量不会出现在同一个缓存行中：示例`long  p1,p2,p3,p4,p5,p6,p7; long var = 10; long  p8,p9,p10,p11,p12,p13,p14;`，这样var就再也不会和任何的变量同处一个缓存行中
- JDK8中新增了一个Contended注解来解决伪共享的问题

## 什么是FAIL-FIRST？



