# ConcurrentHashMap原理

## JDK1.7

JDK1.7的时候，采用基础结构是segment数组，每个segment里面保存了一个HashEntry数组，HashEntry用于存储键值对数据。每一个segment守护HashEntry数组，需要对HashEntry数组数据进行修改的时候，首先需要获取对应的segment。

segment实现了ReentrantLock，segement是一种可重入锁。

![image-20210922225759462](https://gitee.com/Jia_bao_Li/img/raw/master/img/ConcurrentHashMap%E7%9A%841.7%E5%AE%9E%E7%8E%B0.png)

## JDK1.8

取消了segment分段锁，采用了CAS和synchronized保证并发安全。

数据结构和HashMap的结构是一样的。

synchronized只锁定当前链表/红黑树的头节点，只要不产生hash冲突，就不会出现并发

![image-20210922225706383](https://gitee.com/Jia_bao_Li/img/raw/master/img/ConcurrentHashMap%E7%9A%841.8%E5%AE%9E%E7%8E%B0.png)

## HashTable线程安全

全表加锁的方式，一把锁 ，每一次访问map都需要竞争锁。

此外，HashTable不允许出现null的键值

![image-20210922230132303](https://gitee.com/Jia_bao_Li/img/raw/master/img/HashTable%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8.png)



## HashTable、HashMap、ConcurrentHashMap区别

- 底层数据结构：HashTable没有链表长度达到8转换为红黑树的操作，1.7时，concurrentHashMap使用segment数组+HasnEntry数组实现，1.8和HashMap使用一样的，数组+链表/红黑树
- 线程安全：除了HashMap，其他两个都是线程安全
- 效率：HashTable最差，CouncurrentHashMap的线程安全效率比HashTable高很多背
- null值支持：HashTable不支持null，HashMap支持一个null键多个null值
- 扩容：HashTable扩容为`2*n+1`，初始大小11.HashMap初始16，扩容为2倍，或者保证是2的幂次方
- 线程安全实现：ConcurrentHash在JDK1.7的时候，采用了segment分段锁数组来保证线程安全，HashEntry数组来存储数据键值对，1.8之后，采用CAS+synchronized保证线程安全，底层结构和HashMap一致。HashTable只使用一把锁锁定整个表