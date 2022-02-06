# Java基础

## Java语言特点

跨平台可移植性：Java语言编译好的class文件是跨平台的，跨平台的实现是通过JVM，在不同平台上JVM的版本是不一样，他们会将class文件解析为特定平台上的汇编语言交给操作系统来执行

面向对象：Java是一种面向对象的编程语言

健壮性：Java虚拟机在编译时都会对代码进行检查，以消除错误的产生

安全性：Java针对网络安全做了很多的努力，编译执行之前会对代码进行检查

多线程：Java支持多线程，Java的多线程支持需要依赖操作系统底层的线程实现

Java支持解释执行：Java的解释器可以直接对Java字节码文件进行解释执行，Java实际上是一种编译和解释混合的语言

## Java面向对象的特征

封装：就是将对象内部的具体属性和字段封装保护起来，对于外界想要访问和修改这些内容，使用我们暴露给外部的方法即可，这样就保证了对象的安全性

继承：是为了延用之前对象的代码，也就是想要新增加的对象和已有的对象有很多重复内容，可以借助继承来复用代码，减少开发的消耗

多态：是面向对象最重要的一个特征，不同的方法在不同的对象内部表现出来不一样的特征。使用父类引用指向子类对象，然后表现出来的特征是子类方法的，这就是多态的一种体现。

多态增加了代码的可移植性。此外多态的特征使得代码的设计更加灵活多变。设计模式中的面向接口原则就是为了更好的利用多态这一特性。多态为向后兼容而设计的，提高代码的可扩充性（需要修改代码功能时，只需要改动较少的代码）和可维护性。

多态的特征就是可以将多个对象看成父类，这样可以屏蔽一些不同对象之间的差异，从而书写更加通用的代码，增加代码的复用性、移植性和可扩展性。

## Java的Object中的方法

线程安全通信相关的方法（都是native方法，hotspot的源码可以查看，底层c）：

- `wait()`：让调用这个方法的线程进入等待阻塞状态
- `notify()`：唤醒等待这个对象资源的线程
- `notifyAll()`：唤醒所有线程

功能性方法：

- `int hashCode()`：计算对象的hashCode整型值，是native方法，返回的是对象内存地址的整型值
- `boolean equals(Object o)`：判断两个对象是否相同
- `Object clone()`：复制一个对象
- `String toString()`：获取对象的字符串值

finalize()：进行垃圾回收的方法

## hashCode和equals的使用

equals方法是用来比较两个对象是否内容一致，这个方法Object的默认实现是直接使用==（直接比较两个对象的内存地址是否一致）比较两个对象的引用是否一致，对于需要进行内容的比对对比的话，我们可以自己重写equals方法来判断两个对象是否相等。

hashCode方法也是Object中的方法，这个方法用来获取给定对象唯一的散列码（整数）。当某个对象需要使用HashMap进行存储的时候，就需要借助这个hashCode来确定元素应该所处的位置。默认的Object中的实现就是返回对象所在的内存地址的整数表示。

HashMap中对于这两个方法是如何使用？

- 首先使用hash()（散列算法）方法计算哈希值（键hashCode的高16位和键hashCode的低16位进行异或运算），然后借助这一个值去计算当前元素应该所处的位置，一般对于特定容量的元素位置计算采用取余法（除k取余法），然后就可以找到元素的位置hashMap中为了提高速度，采用了hash&(size-1)
- 如果该位置没有元素，则可以直接将这个元素插入
- 如果已经有元素了，则再比较两个元素是否是同一个元素，这个比较就会使用equals方法来进行判断，如果是同一个，则进行数据的更新，如果不相同，则表示出现了hash冲突，HashMap解决hash冲突采用的链表法

这两个方法之间的联系：

- 同一个对象多次调用hashCode方法，总是返回相同的整型值
- 如果两个对象equals判断为真，则两个对象的hashCode一定要是相同的值
- 如果两个对象hashCode相同，两个对象不一定equals
- 如果两个hashCode不同，则equals一定为false
- 如果两个对象equals，两个对象的hashCode一定相同

两个方法的规范：

- 重写了equals一定要重写hashCode
- 两个equals不同，不要求两个hashCode一定要不同

## Java基础数据类型和包装类

八大基础数据类型：

- byte：1字节
- char：2字节
- short：2字节
- int：4字节
- long：8字节
- float：4字节
- double：8字节
- boolean：1字节

对于每一种基础数据类型都有对应的包装，主要是为了方便在泛型（泛型不支持基本数据类型）中使用基础数据类型：

- Byte
- Character
- Short
- Integer
- Long
- Float
- Double
- Boolean

自动装箱和拆箱：（编译器来实现的）利用了包装类中的一些转换方法比如`xxxValue()`转换为基本数据类型，转换为包装类型就是借助构造器，传入数值。

- 装箱：基础数据类型被包装转换为对象
- 拆箱：包装类被转换为基本数据类型

## ArrayList和LinkedList的区别

底层数据结构不同：前者基于数组实现（连续内存），后者基于链表（内存不一定需要连续）实现

访问方式：数组可以随机访问，此外还可以通过下标直接访问，链表实现只能顺序访问，因此访问多的情况下，最好使用数组结构实现的ArrayList，访问效率更高，数组O(1)，链表O(n)

插入删除：数组结构的插入和删除的效率相对来说比较慢，因为需要移动插入位置后面的元素。而链表结构只需要修改指针指向即可。因此插入删除的时间复杂度是O(1)，数组的是O(n)

两者都是线程不安全的集合类

内存占用：链表由于需要维护额外的指针，相比数组来说消耗的内存更多一些

## JDK1.8的新特性

- lambda表达式的支持
- 函数式接口（只有一个抽象方法的接口）
- 支持接口中定义方法体（默认方法）和静态方法
- Java的Stream流式接口，支持并行流和普通流两种方式，并行流的实现实际上使用的是ForkJoinPool来实现的并行操作
- 方法引用和构造器引用，搭配lambda表达式使用，可以简化代码，增加代码的可读性
- 重复注解，@Repeated
- HashMap的底层变化：hash函数的变化，底层数据结构：数据+链表+红黑树的一种结构，ConcurrentHashMap取消了分段锁
- JVM的元空间Metaspace取代了永久代
- Optionals类：用来解决空指针问题

## Java接口和抽象类的区别

### 具体区别

接口：

- JDK1.8之后可以定义默认方法，可以拥有方法的实现体，之前只能拥方法签名
- 接口中可以定义成员变量，但是成员变量都是public static final的常量
- 类可以实现多个接口
- JDK1.8之后可以定义静态方法，但是静态方法只能通过接口调用

抽象类：

- 可以定义成员变量，可以定义私有成员变量，抽象方法只能声明为public或者protected
- 可以定义抽象方法，也可以不定义抽象方法，定义了抽象方法的类一定要声明为抽象类，没有抽象方法也可以声明为抽象类
- 可以定义静态成员变量，接口在JDK1.8之前不可以定义
- 只可以继承一个抽象类

抽象类和接口都不能进行实例化，抽象类或者接口的实现类都需要实现重写抽象方法。

接口的设计目的：

- 用来规定类有的行为，是一种对有的一种约束，不能约束类不能有某些行为
- 接口一般适用于某些对象的共同特征的定义，比如Flyable、Runnable这一类的特征

抽象类的设计目的：

- 代码复用，对于某些类共同具有的相同行为可以使用抽象类
- 抽象类一般适用于某些对象的共同的抽象概念的定义：比如植物、动物这一类的概念定义

接口是用来设计一种has-a关系的方式，继承/抽象类则是用来设计一种is-a的关系的方式

如果某个对象可以拥有某个技能，可以采用接口的设计方式

如果某个对象和某一个对象之间有明显的关系且之间的相似性很高，可以设计为一个抽象继承关系

### 抽象类和接口的使用情形

- 使用抽象类

1. 当你需要和几个相近的类共享代码的时候
2. 你期望扩展拥有共同方法和属性的抽象类或者需要有除了public 之外的其他修饰符的时候
3. 想要定义非静态和非final的属性的时候，这样使得你可以对对象的状态进行修改

- 使用接口

1. 你需要不相关的类可以使用该接口
2. 想要使用多种继承的好处的时候
3. 只想指定需要的方法而不考虑具体方法如何实现

## Java内部类

内部类：定义在类内部的类。体现了一种代码的隐藏机制和访问控制机制。内部类往往之后内部类会调用内部类，因此不用额外创建一个类文件保存。

静态内部类：类的内部声明static class，只能访问外部类的静态成员。会被编译成为一个独立的class文件

普通内部类：类的内部声明class，可以访问外部类的所有成员变量。外部类内部创建实例：`this.new InnerClass()`

局部内部类：定义在方法体中的类，只在方法中生效。可以访问包裹类的所有成员。

匿名内部类：只在某些地方使用一次的场景，在Java的图形化界面设计中可能大量用到。会生成一个OuterClass$1.class文件，数字依据数量会发生变化

内部类的使用：

- 静态：`OuterClass.InnerClass a = new OuterClass.InnerClass()`
- 非静态：`OuterClass.InnerClass a = outObj.new InnerClass()`

使用场景：

- 场景一：当某个类除了它的外部类，不再被其他的类使用时
- 场景二：解决一些非面向对象的语句块
- 场景三：一些多算法场合
- 场景四：适当使用内部类，使得代码更加灵活和富有扩展性

## 高并发集合的问题

Java的集合类中大部分都不是线程安全，因此在高并发下面都是会出现问题的，比如ArrayList、LinkedList、HashMap、HashSet等等都是线程不安全的

实现了线程安全的类：ConcurrentHashMap、Vector、HashTable，后两个类的线程安全实现比较简单粗暴，直接使用synchronized直接保护整个方法来保证线程的安全。ConcurrentHashMap则是对线程安全的实现进行了优化。

Java的阻塞队列则是使用管程或者说条件对象来实现的一种线程安全类，当队列中为空时会阻塞消费线程，限制消费线程来消费，队列满则限制生产线程继续生成内容。

JUC包下面是Java为了线程安全专门设计开发出来的一系列的类，里面大部分是基于CAS实现的线程安全以及利用了AQS（一个并发类设计的框架）设计出来的一些线程安全类，保证高并发下的安全

JUC和AQS则设计到了高并发中的一些很重要的知识，这些内容可以单独汇总称为一部分很大的内容

## HashMap和HashTable的区别

- 扩容上的区别：前者直接扩充为2倍，后者为2倍+1
- 默认容量：HashMap16，HashTable11
- 继承类不同：前者继承AbstractMap，后者继承Dictionary，但是两者都实现了Map
- 迭代器的实现：前者使用Iterator，后者使用Enumeration
- 空值问题：HashMap支持一个null键，支持多个null值，HashTable支持空值的出现
- 线程安全：HashMap线程不安全，HashTable线程安全

## HashMap的扩容机制

JDK1.8之前，每一次扩容之后都需要进行rehash重新计算元素的新位置。

之后进行算法上的优化：因为扩容采用的是扩容为2倍，这样每一次的扩容都是二进制位上的一个移动，每一次扩容就是相当于元容量的左移一位的操作。

hash值计算：`(hashCode)^(hashCode>>16)`通过计算hashCode高16位和hashCode的低16位的异或值

通过hash值计算位置的方式是：`hash&(cap-1)`，因为使用了这样的计算，因此需要保证容量是2的幂次方。因此判断扩容之后位置会不会改变的方式只需要查看原来的hash值和当前新增容量的标志位1进行比较，如果相同，说明需要改变位置，位置的改变通过当前位置加上原来的容量即可

![image-20220105222937215](https://gitee.com/Jia_bao_Li/img/raw/master/img/HashMap%E7%9A%84%E6%89%A9%E5%AE%B9%E6%9C%BA%E5%88%B6.png)

### 扩容方法的源码

#### 计算新的数组的大小和threadshold的源码

```java

// 先拿到元数组
Node<K,V>[] oldTab = table;
// 获取原数组的容量和阈值
int oldCap = (oldTab == null) ? 0 : oldTab.length;
int oldThr = threshold;
/**
下面这一部分判断则是用来计算扩容之后数组的容量和阈值
*/
int newCap, newThr = 0;
if (oldCap > 0) {
    // 原始容量已经超出了HashMap的最大容量，直接返回原HashMap的数组
    if (oldCap >= MAXIMUM_CAPACITY) {
        // 更新阈值
        threshold = Integer.MAX_VALUE;
        return oldTab;
    }
    // 否则将数组的容量扩充为原来的两倍
    else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
             oldCap >= DEFAULT_INITIAL_CAPACITY)
        // 阈值也扩充为两倍
        newThr = oldThr << 1; 
}
else if (oldThr > 0) // initial capacity was placed in threshold
    newCap = oldThr;
else {               // zero initial threshold signifies using defaults
    newCap = DEFAULT_INITIAL_CAPACITY;
    newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
}

// 设置新阈值的部分代码
if (newThr == 0) {
    float ft = (float)newCap * loadFactor;
    newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
              (int)ft : Integer.MAX_VALUE);
}
threshold = newThr;
```

#### 扩容之后数据转移的源码

对于数据只有一个节点的情况：

- 直接重新计算原来数据在新数组的位置即可，通过hash值和新数组大小进行与运算可以获取到新位置

对于原来数据是多个节点的链表的情况：

1. 创建两个链表来记录需要转移位置的节点和不转移位置的节点
2. 然后判断节点hash值和原数组的与值是否为0判断是否是要转移位置的节点
3. 当链表遍历完毕之后就获取到两个链表，然后将这两个链表放入到新数组中即可完成

对于红黑树节点看下面——详细源码和过程

```java
/**
扩容之后将原来数据转移到新数组部分的代码
*/

// 根据之前计算好的心容量创建出扩容之后的新数组
Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
// 更新HashMap的数组指针指向新创建的数组
table = newTab;
// 开始将旧数组的数据转移到新数组
if (oldTab != null) {
    // 遍历原来的数组
    for (int j = 0; j < oldCap; ++j) {
        Node<K,V> e;
        if ((e = oldTab[j]) != null) {
            oldTab[j] = null;
            // 哈希槽只有一个元素
            if (e.next == null)
                // 不再需要进行rehash，关键就在于数组大小设置为2的幂次方
                // 然后找元素的新位置的时候，利用原来的hash值和现在的新数组大小减一进行与运算即可
                newTab[e.hash & (newCap - 1)] = e;
            // 如果是红黑树的话，需要分割将元素更新到新数组
            else if (e instanceof TreeNode)
                ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
            // 是链表的话则需要遍历链表转移到新HahsMap数组中
            else { // preserve order
                Node<K,V> loHead = null, loTail = null;
                Node<K,V> hiHead = null, hiTail = null;
                Node<K,V> next;
                do {
                    next = e.next;
                    // 如果和原数组大小与运算为0，则元素保留在当前的链表即可
                    if ((e.hash & oldCap) == 0) {
                        if (loTail == null)
                            loHead = e;
                        else
                            loTail.next = e;
                        loTail = e;
                    }
                    // 为1则表示这些节点需要移动到数组的新位置，将这些节点串成一个链表即可
                    else {
                        // 链表节点的插入方法：尾插法
                        if (hiTail == null)
                            hiHead = e;
                        else
                            hiTail.next = e;
                        hiTail = e;
                    }
                } while ((e = next) != null);
                // 将两个链表放到他们在新数组中的位置即可
                if (loTail != null) {
                    loTail.next = null;
                    newTab[j] = loHead;
                }
                if (hiTail != null) {
                    hiTail.next = null;
                    newTab[j + oldCap] = hiHead;
                }
            }
        }
    }
}
return newTab;
```

#### 红黑树节点数据转移的源码

在扩容过程中红黑树节点的变化过程：使用两颗树来记录是否有节点需要转移位置，在遍历节点的同时更新两颗树

- 一颗树记录hash值和原数组大小的与值为0的情况，这棵树记录的是在新数组不需要转移位置的节点
- 一颗树记录hash值和原数组大小的与值为1的情况，这课树记录那些需要在新数组转移位置的节点

判断两颗树节点数量和红黑树转链表的阈值6的情况：

- 如果小于等于6，说明需要从红黑树转换为链表
- 否则将树转换为红黑树

```java
final void split(HashMap<K,V> map, Node<K,V>[] tab, int index, int bit) {
    TreeNode<K,V> b = this;
    // Relink into lo and hi lists, preserving order
    TreeNode<K,V> loHead = null, loTail = null;
    TreeNode<K,V> hiHead = null, hiTail = null;
    int lc = 0, hc = 0;
    for (TreeNode<K,V> e = b, next; e != null; e = next) {
        next = (TreeNode<K,V>)e.next;
        e.next = null;
        if ((e.hash & bit) == 0) {
            if ((e.prev = loTail) == null)
                loHead = e;
            else
                loTail.next = e;
            loTail = e;
            ++lc;
        }
        else {
            if ((e.prev = hiTail) == null)
                hiHead = e;
            else
                hiTail.next = e;
            hiTail = e;
            ++hc;
        }
    }

    if (loHead != null) {
        // 红黑树转换为链表的阈值：6
        // 链表转换为红黑树的阈值：8（Node数量操作64，否则会直接进行扩容）
        if (lc <= UNTREEIFY_THRESHOLD)
            tab[index] = loHead.untreeify(map);
        else {
            tab[index] = loHead;
            if (hiHead != null) // (else is already treeified（else条件表明已经是红黑树）)
                loHead.treeify(tab);
        }
    }
    if (hiHead != null) {
        if (hc <= UNTREEIFY_THRESHOLD)
            tab[index + bit] = hiHead.untreeify(map);
        else {
            tab[index + bit] = hiHead;
            // 下面这一步的用处，如果另外一个头结点为null，说明当前的红黑树并没有变化（没有节点移动）
            // 如果另外一颗树上面有节点，说明发生了节点转移，需要重新生成红黑树
            if (loHead != null)
                hiHead.treeify(tab);
        }
    }
}
```

## HashMap的线程安全的实现方式

- 使用Collections.synchronizedMap()返回一个新的Map，这个新返回的Map就是线程安全的
- 使用改写的线程安全的HashMap--`ConcurrentHashMap`类，这个类为了map的线程安全做了很多实现

方法一：

实现细节：返回了一个封装的Map对象，这个Map对象对HashMap原有的方法进行了封装改写

- 在所有的方法都使用了synchronized来保证线程的安全
- 使用了代理创建了一个新的类

对整个方法进行了线程安全的保护，这样整个锁的粒度很大

方法二：

针对线程安全做了很多的尝试，JDK1.8之前采用的分段锁的设计，降低锁的粒度从而来改善整个类的一个效率。此外还借助了CAS来保证原子性和互斥性。

相比整个方法直接锁住效率更高，性能更好，但是代码更繁琐

建议阅读源码：查看HashMap、ConcurrentHashMap和AQS的源码。



## ConcurrentHashMap实现细节





## String、StringBuilder、StringBuffer

### String

String是Java中的不可变类，定义为final的类，表明这个类是不可变类。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence
```

Java中的String长度是有限制的，并且限制还分成两个，编译时的字符串长度不能超过2的16次方（65535），运行时Java字符串长度不能超过2的32次方（2147483647）。通过length方法的返回值就可以知道

String是常量，当字符串一旦被创建出来，他们的值不会再被改变。像String类型的+操作（本质上就是调用了StringBuilder来创建新的字符串）或者其他操作，都是先创建出来一个新的字符串，然后再将引用指向新创建的字符串。如果字符串需要频繁的被改变，建议还是使用StringBuilder和StringBuffer

### 字符串池

JVM为了减少相同字符串的重复创建，节省内存，单独开辟了一块内存用来保存字符串常量，这一块内存称为字符串常量池。

当出现双引号创建字符串的时候，JVM会首先查询字符串常量池是否存在相同内容的字符串对象引用，存在就直接返回引用，否则才去创建一个字符串常量，然后将引用放入常量池中并返回引用

Java的字符串常量池JDK1.8之后就从堆区放到了永久代（移除了永久代，用元空间代替），之前的字符串常量池是放在了JVM的方法区

### StringBuilder

StringBuilder是可变的字符串类，可以方便的向字符串内部添加和删除字符串。

```java
public final class StringBuilder
   extends AbstractStringBuilder
   implements java.io.Serializable, Comparable<StringBuilder>, CharSequence
```

线程不安全的类。方法中没有添加synchronized签名。正式因为没有使用线程安全，相比StringBuffer来说，StringBuilder的效率会更好一些。

### StringBuffer

```java
public final class StringBuffer
   extends AbstractStringBuilder
   implements java.io.Serializable, Comparable<StringBuffer>, CharSequence
```

是线程安全的类，类中的所有方法签名上面都增加了synchronized来保证线程的安全。



## 位图Java实现之BitSet

位图的使用场景：

- 大数据量的数据的去重
- 仅支持数值型数据的去重，字符串的数据去重这种方法不太可行
- 例如QQ号的去重可以借助这个来实现

```java
public class BitSet implements Cloneable, java.io.Serializable {
    /**
    ADDRESS_BITS+PER_WORD：定义计算元素所处long数组位置的常量，通过位移运算快速计算出元素位置
    BITS_PER_WORD：定义一个元素所拥有的bit数量，64
    BIT_INDEX_WORD：用来计算数值所处元素中bit位置的常量，通过进行与运算快速求解
    */
    private static final int ADDRESS_BITS_PER_WORD = 6;
    private static final int BITS_PER_WORD = 1<<ADDRESS_BITS_PER_WORD;
    private static final int BIT_INDEX_MASK = BITS_PER_WORD-1;
 
    
    /**
    words：记录所有元素的long数组，每一个数组元素可以存储64个数
    wordInUse：已经使用的word的数量
    */
    private long[] words;
    private transient int wordInUse=0;
    
    /**
     确保操作之后不会出现异常错误，每一个公共方法都需要保证这个，检验合法性的方法
    */
    private void checkInvariants() {
        // 保证元素要么为0，要么当前使用的word数量前一个word一定是使用过的（即存在位数被设置过）
        assert(wordsInUse == 0 || words[wordsInUse - 1] != 0);
        // 保证word使用小于word的长度
        assert(wordsInUse >= 0 && wordsInUse <= words.length);
        // 保证word数量合法
        assert(wordsInUse == words.length || words[wordsInUse] == 0);
    }
    
    /**
    计算元素所处的long数组中的索引位置
    */
    private static int wordIndex(int bitIndex) {
        return bitIndex >> ADDRESS_BITS_PER_WORD;
    }
    
    /**
    重新计算使用的word数量，如果最高位的word数值大小为0，说明没有使用，则可以删减这个元素
    */
    private void recalculateWordsInUse() {
        // Traverse the bitset until a used word is found
        int i;
        for (i = wordsInUse-1; i >= 0; i--)
            if (words[i] != 0)
                break;

        wordsInUse = i+1; // The new logical size
    }
    
    /**
    确保设置数值的时候数组元素中容量足够
    */
    private void expandTo(int wordIndex) {
        int wordsRequired = wordIndex+1;
        if (wordsInUse < wordsRequired) {
            ensureCapacity(wordsRequired);
            wordsInUse = wordsRequired;
        }
    }
    
    /**
    当需求大于当前数组长度，需要进行扩容
    */
    private void ensureCapacity(int wordsRequired) {
        if (words.length < wordsRequired) {
            // Allocate larger of doubled size or required size
            int request = Math.max(2 * words.length, wordsRequired);
            words = Arrays.copyOf(words, request);
            sizeIsSticky = false;
        }
    }
    
    /**
    设置当前数的元素位置为访问过/出现过
    */
    public void set(int bitIndex) {
        // 元素数值小于0，抛出异常
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);
        // 计算数值所处的数组位置
        int wordIndex = wordIndex(bitIndex);
        // 确保数组容量可以存储当前数值，容量不够会进行扩容操作
        expandTo(wordIndex);
        // 直接借助或运算将当前数值所处的bit位设置为1
        // 移位运算规定好了当移位数量超过了元素的最大位数，会进行取余运算之后再移位
        // 因此可以直接对数值进行移位即可
        words[wordIndex] |= (1L << bitIndex); // Restores invariants

        // 保证操作之后元素依然保证assert的几个条件
        checkInvariants();
    }
    
    /**
    将该数值设置为没有出现，清除
    */
    public void clear(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);
        // 如果word位置超出范围，直接返回即可
        if (wordIndex >= wordsInUse)
            return;
        
        // 借助二进制的与非结合运算，先取反再进行与运算
        words[wordIndex] &= ~(1L << bitIndex);

        // 重新记录使用的word数量，如果某个word为0，说明没有被使用
        recalculateWordsInUse();
        checkInvariants();
    }
}
```

## Java常见异常类和异常处理

### 异常类

所有异常的公共父类：Throwable，Throwable两个比较大的子类，一个是Exception，一个是Error

Exception的一个子类是RuntimeException，这个异常类是很多的异常的父类，一般项目中如果需要自定义异常，都会直接继承RuntimeException来实现一个公共抽象的父类。

常用或者经常碰见的异常类：ClassNotFoundException、IndexOutOfBoundException、CloneNotSupportedException、IOException、SQLException、NullPointException、StackOverflowException、ClassCastException，NoSuchElementException

堆栈溢出、空指针、类型指定异常、越界、IO、SQL、找不到类、找不到该元素异常等等

```
Exception                           异常类，所有异常的父类
│
├─ RuntimeException                 运行时异常，在运行代码时出现的异常
│  │
│  ├─ NullPointerException          空指针异常，实际业务中常遇见的异常
│  │
│  ├─ IndexOutOfBoundsException     越界异常
│  │
│  ├─ SecurityException             安全异常
│  │
│  └─ IllegalArgumentException      非法参数异常
│     │
│     └─ NumberFormatException      格式异常，字符串格式异常等等，常出现在Number.parseInt()
│
├─ IOException                      IO异常，文件读写和网络层传输时遇见的异常等
│  │
│  ├─ UnsupportedCharsetException   不支持的字符集异常
│  │
│  ├─ FileNotFoundException         文件找不到异常
│  │
│  └─ SocketException               Socket网络传输异常
│
├─ ParseException                   
│
├─ GeneralSecurityException
│
├─ SQLException                      数据库交互异常
│
└─ TimeoutException                  超时异常
```

### 异常处理

try、catch、finally、throw和throws关键字是Java中用来处理异常的几种方式

```java
// throws用来在方法签名的结尾位置指定这个方法可能会出现哪些异常情况，告知使用该方法的用户
// 异常的处理交给用户自己去做
public void handlerException() throws IOException{
    // 如果需要自己处理异常的话，就会使用try、catch语句
    try {
        // 执行你想要的代码
    }catch (IOException e) {
        // 捕获上述代码执行过程中可能出现的IO执行异常，然后在这里进行异常处理的逻辑
    }catch (Exception e) {
        // 捕获Exception较大层的异常
        // 一般等级更高更不具体的异常都会放到更后面进行捕获，不然会导致更具体的异常捕获不到
    } catch (Throwable e) {
        // 捕获等级最高的异常
    } finally  {
        // 在这里的代码不管怎么样都会最终去执行这部分代码
    }
    
    throw new IOException();  // 直接抛出具体的异常类对象
    if (1/0) {
        throw new RuntimeException("出现错误，除零异常");
    }
}
```

#### 使用技巧

1. 只在异常情况下使用异常，异常处理不能代替简单的测试

2. 不要过于细化异常

- 如果你将代码过于细化，会导致代码量的急剧增加，而且代码的逻辑可能也会不清晰
- 需要异常处理的代码可以放在一个try代码块中
- 对于需要捕获的异常创建对应的异常处理

3. 充分利用异常层次结构

- 不要只抛出RuntimeException,寻找适合的子类创建合适的异常类
- 不要只捕获THrowable异常，否则使代码难读难懂
- 考虑检查性异常和非检查型异常的区别，不要为逻辑错误抛出检查型异常

4. 不要压制异常
   有些时候异常的出现远比你压制异常的出现要更好，比如在寻找错误的时候，比如在你debug的时候

5. 早抛出
   对于出错的地方，早点抛出会更好，会比以后抛出NullPointException更好

6. 晚捕获
   不一定非要捕获异常，有时候将异常传递可能会更好

## Java序列化

序列化：将Java对象转换为字符串存储到磁盘中进行持久化的过程

反序列化：将序列化的字符串转换会Java对象的过程

Java的序列化实现：

- 类实现Serializable接口
- 使用`ObjectInputStream`和`ObjectOutputStream`对对象进行反序列化和序列化
- 类中增加`writeObject`和`readObject`方法可以实现自定义序列化策略
- 序列化ID：虚拟机是否允许反序列化，取决于类路径和功能代码是否一直，还需要看两个类的序列化ID是否一致（`private static final long serialVersionUID`）

Java序列化的特点：

- 静态成员不会被序列化，如果某些你需要序列化的成员可以考虑将其设置为static
- 如果父类实现了Serializable接口，子类不必要再实现这个接口
- 如果还有某些成员不想被序列化可以使用transient修饰
- 如果一个对象被一个要序列化的对象引用了，那么这个对象也会被序列化，且这个对象也必须实现Serializable接口

对于序列化，Java中已经有实现的比较好的工具类库，可以使用fastJson这些JSON解析工具类，这些也常被用作对象的持久化，在网络通讯和RPC调用中也是需要使用到这些持久化方式的。



## Java对象拷贝

Java对象有一个clone()可以进行重写，重写之后就可以实现对象的拷贝

浅拷贝：对于内部引用的对象只会拷贝引用，而不会将引用的对象也拷贝一份进来

```java
class ShallowCopy implements Cloneable {
    @Override
    public Object clone() {
        try {
            return (ShallowCopy)super.clone();
        }catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
    
}
```

深拷贝：会将内部引用的对象也拷贝一份改变引用指向新拷贝的对象

```java
class DeepCopy implements Cloneable {
    String name;
    int age;
    ShallowCopy p;
    
    public Object clone() {
        DeepCopy copy = null;
        try {
            copy = (DeepCopy) super.clone();
            copy.p = (ShallowCopy)p.clone();
            return copy;
        }catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

深拷贝的实现可以通过序列化和反序列化之后获取到的对象实现。

## Java反射

反射是Java中很重要的一种机制，这部分内容和JVM的类加载机制有很大的相关性。

JVM会在第一次读取到某个class的时候将其加载到内存中去，每次加载一个class，JVM都会为这个对象创建一个Class类型的对象实例。这个对象实例是由JVM内部创建的，Class类的构造方法是private的，只能有JVM创建class的实例，Java程序不能创建。这个实例包含了实例指向对象的所有信息：类名、包名、父类、实现接口、定义的属性、定义的方法等

Java中的反射可以让程序可以在运行期间获取到一个对象的所有信息。反射主要目的是为了解决在运行期，对某个实例不了解的情况下，还可以调用获取到方法和属性。Class的实例在内存中只有一份，因此无论如何获取，获取到的都是同一个实例Class。原始数据类型、数组和void也是具有Class实例的。

通过Class的实例对象获取class类的信息的方法称之为反射；

获取对象的Class对象的几种方式：

```java
public void getClassTest() {
    // 1. 使用类名.class获取
    Class cls = String.class;
    // 2. 实例对象的getClass()方法获取
    Class cls = new String().getClass();
    // 3. Class的forName()静态方法，传入类的全路径报名加类名
    Class cls = Class.forName("java.lang.String");
}
```

### 获取class的信息

- `getName()`：获取类名
- `getClassLoader()`：获取类加载器
- `getInterfaces()`：获取实现的接口
- `getPackage()`：获取包信息
- `getDeclaredFields()`：获取声明的属性
- `getDeclaredMethods()`：获取声明的方法
- ....

Method、Field、Constructor..，借助这些类的`setAccessible(ture)`可以访问私有变量

### 使用反射可以获取`private`字段的值，那么类的封装还有什么意义？

答案是正常情况下，我们总是通过`p.name`来访问`Person`的`name`字段，编译器会根据`public`、`protected`和`private`决定是否允许访问字段，这样就达到了数据封装的目的。

而反射是一种非常规的用法，使用反射，首先代码非常繁琐，其次，它更多地是给工具或者底层框架来使用，目的是在不知道目标实例任何信息的情况下，获取特定字段的值。

此外，`setAccessible(true)`可能会失败。如果JVM运行期存在`SecurityManager`，那么它会根据规则进行检查，有可能阻止`setAccessible(true)`。例如，某个`SecurityManager`可能不允许对`java`和`javax`开头的`package`的类调用`setAccessible(true)`，这样可以保证JVM核心库的安全。

### 调用方法

反射API中提供了一个Method类，这个类可以获取到方法的所有信息，以及执行方法的方法invoke

调用public方法：

```java
public void getClassMethodTest() {
    Class cls = String.class;
    // 获取指定方法名和对应的参数数量的方法类的实例
    Method substring = cls.getMethod("substring",int.class);
    // 调用Method的invoke方法执行方法，执行这个方法需要传入对象的实例，已经对应数量的参数值
    String s = (String)substring.invoke(s,6);
    
}
```

调用静态方法：

```java
public static void main(String[] args) throws Exception {
    // 获取Integer.parseInt(String)方法，参数为String:
    Method m = Integer.class.getMethod("parseInt", String.class);
    // 调用该静态方法并获取结果:静态方法不需要传入对象实例，传入null即可
    Integer n = (Integer) m.invoke(null, "12345");
    // 打印调用结果:
    System.out.println(n);
}
```

调用非public方法：

```java
public static void main(String[] args) throws Exception {
    Person p = new Person();
    Method m = p.getClass().getDeclaredMethod("setName", String.class);
    // 设置该方法是可以访问的
    m.setAccessible(true);
    // 调用执行
    m.invoke(p, "Bob");
    System.out.println(p.name);
}
```

多态：

```java
// 传入对象实例的时候可以传入子类对象实例实现多态的效果
public class Main {
    public static void main(String[] args) throws Exception {
        // 获取Person的hello方法:
        Method h = Person.class.getMethod("hello");
        // 对Student实例调用hello方法:
        h.invoke(new Student());
    }
}

class Person {
    public void hello() {
        System.out.println("Person:hello");
    }
}

class Student extends Person {
    public void hello() {
        System.out.println("Student:hello");
    }
}
```



### 调用构造方法

反射API中提供了Constructor构造器类，包含一个构造方法的所有信息，和Method类很相似，不过Constructor调用结果总是返回实例。

执行这个构造方法创建实例可以使用他里面的newInstance()方法：

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // 获取构造方法Integer(int):
        Constructor cons1 = Integer.class.getConstructor(int.class);
        // 调用构造方法:
        Integer n1 = (Integer) cons1.newInstance(123);
        System.out.println(n1);

        // 获取构造方法Integer(String)
        Constructor cons2 = Integer.class.getConstructor(String.class);
        Integer n2 = (Integer) cons2.newInstance("456");
        System.out.println(n2);
    }
}
```



### JVM动态加载

JVM在运行Java程序时，并不会一次性将所有的class全部加载到内存中，而是动态加载，当第一次使用这个class的时候才将这个class的Class对象实例创建到内存中



## Java注解

Java注解是一种类似于标记的东西，可以借助注解给编译器传递信息，注解还可以在程序运行时被解析使用，注解并不会对代码逻辑造成影响。

### 注解的本质

本质上，注解就是一个继承了Annotation的特殊接口，Java运行时动态生成一个具体的动态代理类。当代理对象调用注解接口类中的方法的时候，就会将这个方法转到AnnotationInvocationHandler的invoke方法进行处理，这个方法会从memberValues的map中取出对应的值，memberValues的Java常量池中的内容。

### 注解的分类

- 第一类：不会被编译进入class文件的注解，这一类注解主要是一类给编译器提示的一类标记，编译之后就被编译器丢掉
- 第二类：会被编译进入class文件中的注解，可以对class做动态的修改，实现特殊的一些功能，但是加载之后不会保留在内存中。这类注解大部分底层库使用
- 第三类：程序运行期间可以读取到的注解，在加载之后会一直保存在JVM中。

### 注解的配置参数类型

- 基本数据类型
- String
- 枚举类型
- 基本类型、String、Class以及枚举的数组

### Java中的元注解

- `@Targrt`：注解的作用目标对象
  - `ElementType.TYPE`：类或接口
  - `ElementType.FIELD`：字段
  - `ElementType.METHOD`：方法
  - `ElementType.CONSTRUCTOR`：构造器
  - `ElementType.PARAMETER`：方法参数
- `@Retention`：定义注解的生效范围（声明周期）
  - `RetentionPolicy.SOURCE`：仅编译器生效
  - `RetentionPolicy.CLASS`：仅class文件
  - `RetentionPolicy.RUNTIME`：程序运行期生效
- `@Repeatable`：是否可以重复使用注解在同一个位置
- `@Inherited`：是否可继承

### 定义注解

使用@interface关键字创建接口

```java
// 创建注解类的方法
@Target(ElementType.FIELD)             // 设置注解作用对象
@Retention(RententionPolicy.RUNTIME)   // 运行期间使用注解增强，需要设置为RUNTIME，不然获取不到
public @inteface JsonField {
    public String value() default "这是默认值";
}
```

### 处理注解

那么如何获取到注解中设置的值呢？

借助反射可以获取到当前对象实例下的使用的注解Class实例，然后借助注解class获取该注解实例的所有属性和值

```java
// 对于作用字段上的注解，首先需要获取被注解对象的字段，然后利用字段Field类来获取注解
public class TableClass {
    
    @JsonField("mall_student")
    private String tableName;
    
    public void parseFieldAnnotation() {
        Field field = TableClass.class.getDeclaredField("tableName");
        // 先判断当前字段是不是当前注解的注解对象
        if (field.isAnnotationPresent(JsonField.class)) {
            // 是才进行相应的注解处理逻辑
            JsonField annotation = field.getAnnotation(JsonField.class);
            System.out.println(annotation.tableName());
        }
    }
}
```



## Java泛型

[Java泛型的详细笔记内容](../计算机语言基础/JavaSE/Java泛型.md)

### 为什么使用泛型?

- 提高代码的复用性，减少代码量
- 增强代码的可读性
- 降低耦合度
- 减少类型转换，对于不适用泛型的代码，有些可能需要进行强制类型转换，泛型代码可以省略类型转换的步骤
- 更高的安全性：泛型是一种编译期间使用的信息，编译器会对泛型代码进行强类型检查，一旦违反了类型安全就会在编译期间报错，减少运行期间可能出现错误的概率

### 泛型类

泛型类就是有一个或者多个类型变量的类，定义了泛型类，就不用再为类的变量类型等细节而烦扰，只需要关注代码的泛型设计即可。

```java
public class ClassName<类型变量>
{
    public ClassName(){}
}
// 示例
// 其中的Key和Value就是Java泛型中的类型变量
// 类型变量用于在类中指定方法的返回类型以及字段和局部变量的类型
public class Transaction<Key, Value>
{
    private Key key;
    private Value value;
    public Transaction()
    {
        this.key = new Key();
        this.value = new Value();
    }
}
```

其实泛型类就相当于普通类的 工厂，泛型类在实际使用的时候可以指定特定的类型变量来实例化泛型

```java
// 第一种实例化泛型类的方法
Transaction<String, Integer> transaction = new Transaction<String, Integer>();
// 第二种泛型类实例化，使用菱形语法，可以忽略构造器中类型变量的指定，减少代码量
Transaction<String, Integer> transaction = new Transaction<>();
// 第三种泛型实例化，使用var关键字，可以忽略变量类型的指定
var transaction = new Transaction<String, Integer>();
// 后面两种实例化都是利用编译器通过现有的代码信息来推测的类型变量，比较方便，可以减少代码量
```

### 泛型方法

泛型方法定义的格式：

`[修饰符] <类型变量>  [返回值]  [方法名](参数类型，可以使用类型变量){}`

```java
public class Util
{
    public static <K, V> boolean funcName(Transaction<K, V> a1, Transaction<K, V> a2)
    {
        return a1.key.compareTo(a2.key) > 0;
    }
    // 上述定义的方法就是一个泛型方法示例
}
```

泛型方法的使用：

```java
public class GenericMethodsTest
{
    public static void main(Stirng[] args)
    {
        var a1 = new Transaction<String, Integer();
        var a2 = new Transaction<String, Integer();
        boolean test = Util.<String, Integer>funcName(a1, a2);
        // 也可以不显示的指定，可以让编译来推测应该使用什么类型
        boolean test = Util.funcName(a1, a2);
    }
}
```

上面的泛型类和泛型方法都提到了编译器的类型推测：泛型的类型推测是Java编译器进行的操作，编译器根据上下文信息，也就是Java的类型声明（应用于泛型类的推测）和类型参数（应用于泛型方法的推测）来推测应该使用哪一种类型来编译，编译器会找到最合适的类型来进行编译。

### 类型擦除

类型擦除是编译器提供的，为了正常的兼容泛型代码：

- 替代所有的类型参数为边界类型参数或者没有限定类型时变成Object，擦除之后的字节码只有普通的类接口和方法，不再存在泛型
- 必要时为了保护类型安全会使用强制类型转换
- 在泛型方法中为了维护多态，使用合成桥方法

泛型擦除确保了不同参数化类型不会产生新的类，因此泛型不会产生运行时开销

### 泛型的不足和局限性

- 泛型不能使用原始数据类型，对于原始数据类型需要使用他们的包装类

- 不能创建参数化类型的实例，需要借助反射来实现这样的一种操作

  ```java
  public static <E> void append(List<E> list)
  {
      E elem = new E();  // 编译时错误
  }
  // 合法的参数化类型的实例获取
  public static <E> void append(List<E> list, Class<E> cls) throws Exception
  {
      E elem = cls.newInstance();  // 这种获取实例的方法是可行的
  }
  list<String> ls = new ArrayList<>();
  append(ls, String.class);  // 调用，传入String的反射类
  ```

- 不能声明类型是（parametered type）类型参数的静态变量

- 不能使用instanceof判断和泛型参数相关的内容

- 不能创建参数化类型的数组

- 不能创建、捕获或者抛出参数化类型的异常

- 对于类型擦除之后方法签名一致的情况，不能进行方法重载

## Java代理实现的方式

### 静态代理

创建一个抽象对象或者接口

```java
public interface RentInterface {
    void rent();   // 创建了一个抽象方法，出租
}
```

创建一个房东类，也就是需要被代理的类

```java
public class Host implements RentInterface {
    @Override
    public void rent() {
        // 房东出租需要做的一些实现
    }
}
```

创建一个代理，也就是现实中的中介

```java
public class Agent implements RentInterface {
    Host host;
    @Override
    public void rent() {
        // 房东可以先进行一部分业务，然后将调用房东的出租方法，达到加强房东出租的过程的一个目的
        checkHouse();
        chargeFromClient();
        payToHost();
        this.host.rent();
        giveKeyToclient();
    }
    public void checkHouse() {
        System.out.println("带客户看房");
    }
    public void chargeFromClient() {
        System.out.println("中介收费");
    }
    
    public void payToHost() {
        System.out.println("中介给房东支付租金");
    }
    
    public void giveKeyToClient() {
        System.out.println("中介把房子钥匙给客户");
    }
}
```



### 动态代理

#### Java的Proxy代理类

Java动态代理的本质就是在运行期间创建某个interface的实例对象。

Java动态代理的实现需要使用到下面的内容：

```java

/**
 * 代理对象实例的调用处理器需要实现的一个接口
 * 每个代理实例都有一个相关联的调用处理器
 * 当代理实例调用了方法的时候，这个方法调用会被编码并且分发到调用处理器的invoke方法处
 */
public interface InvocationHandler {

    /**
     * 处理代理类实例的方法调用并且返回结果
     * 当某个关联了这个调用处理器的代理方法调用时，会唤醒相关联的调用处理器中的invoke方法
     * 
     * @param proxy 被调用方法所处的代理实例
     *
     * @param method 代理类上被调用的Method实例对象。
     * 这个方法的声明类将会是这个方法声明所在的接口，这个接口也许是这个代理接口（代理类从方法继承过来的）的父接口
     * @param   args 对象数组，代理实例对象方法调用时传递过来的参数，如果没有参数就是null
     * 基本数据类型会以包装类的形式传递
     *
     * @return  方法调用的返回值，一定要是Object对象，基本数据类型需要是包装类
     * 可能会出现类型指定异常或者空指针异常
     *
     * @throws  Throwable 抛出Throwable异常基类
     * {@link UndeclaredThrowableException} containing the
     * exception that was thrown by this method will be thrown by the
     * method invocation on the proxy instance.
     *
     * @see     UndeclaredThrowableException
     */
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

还需要借助Proxy对象创建一个实例对象来调用方法：

```java
/**
就是会在运行时动态的生成一个代理类，这个代理类中的实现的接口的方法调用都会被分派到调用处理器的invoke方法进行业务的增强

Proxy：Proxy提供了静态方法创建一个对象实例（获取代理实例），这个对象实例是他实现的某个接口的子类
代理类：指在运行时被创建出来的一个实现了许多指定的接口集合的类
代理实例：指代理类的实例对象，每个代理实例都有相对应的调用处理器类（实现了InvocationHandler接口的类）
通过某个代理接口（proxy interface）在代理实例上调用的方法将会被分发到代理实例绑定的调用处理器的invoke方法
调用这个invoke方法的时候会传递三个参数（代理实例对象，Method对象（区分调用的方法），参数数组，传递方法执行需要的参数类型和参数）
调用处理器的返回结果将会被作为代理实例调用位置的返回结果

代理类需要拥有的特性：
- 代理类是final且非抽象的
- 代理类继承了java.lang.reflect.Proxy
- 代理类必须实现了（被代理的类一定要实现至少一个接口，不然编译时就会报错）创建指定的那些接口，也就是说当执行getInterface()方法的时候会返回和创建时指定的接口集合中一样的接口。
- Proxy.isProxyClass()用来判断某一个类是不是代理类，需要传入类的class对象
*/
public class Proxy implements java.io.Serializable {
    private static final long serialVersionUID = -2222568056686623797L;
    
    // 获取代理类实例的静态，参数是类加载器、接口class数组和InvocationHandler
    // 这个代理类的实例：指定接口中方法调用会被分派到指定的调用处理器InvocationHandler
	public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h) {
        Objects.requireNonNull(h);

        final Class<?> caller = System.getSecurityManager() == null
                                    ? null
                                    : Reflection.getCallerClass();

        /*
         * Look up or generate the designated proxy class and its constructor.
         */
        Constructor<?> cons = getProxyConstructor(caller, loader, interfaces);

        return newProxyInstance(caller, cons, h);
    }
    
}
```

Proxy的简单加强方法的实现：

```java
// 创建一个方法加强的调用处理器
InvocationHandler handler = new MyInvocationHandler(...);
// 创建一个被方法调用器加强之后的对象，Foo是一个接口
Foo f = (Foo) Proxy.newProxyInstance(Foo.class.getClassLoader(),
                                     new Class<?>[] { Foo.class },
                                     handler);
```

##### 代理类的一些特性

- 这个代理类继承了Proxy
- 这个代理类被声明为public final不可变，不可被继承，不可重写方法
- 代理类使用imstanceof和它继承的接口进行判断一定会是true，且可以进行(InterfaceName)强制类型转换，不然会抛出ClassCastException

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;
import proxy.UserService;

public final class UserServiceProxy extends Proxy implements UserService {
    private static Method m1;
    private static Method m2;
    private static Method m4;
    private static Method m0;
    private static Method m3;

    public UserServiceProxy(InvocationHandler var1) throws  {
        super(var1);
    }

    public final boolean equals(Object var1) throws  {
        // 省略...
    }

    public final String toString() throws  {
        // 省略...
    }

    public final void select() throws  {
        try {
            super.h.invoke(this, m4, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int hashCode() throws  {
        // 省略...
    }

    public final void update() throws  {
        try {
            super.h.invoke(this, m3, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    static {
        try {
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            m4 = Class.forName("proxy.UserService").getMethod("select");
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
            m3 = Class.forName("proxy.UserService").getMethod("update");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }
}

```

- 实际上创建出来的代理类的class字节码中的方法是按照m+数字命名（在静态代码块中创建的Method对象），调用方法时使用`super.h.invoke(this,m1,(Object[])null)`调用，实际上就是传递给InvocationHandler负责处理调用逻辑
- 代理实例上接口方法的调用，将会被分发到InvocationHandler的invoke方法执行。因此可以在这个方法内部对业务逻辑进行加强改进，代理类的InvocationHandler可以使用Proxy.getInvocationHandler()方法进行查看。在创建自己的InvocationHandler的时候，一定要将代理类传递作为InvocationHandler的参数或者说字段，因为在接口方法调用的时候，需要传入对象的实例才能够成功调用，不然会抛出空指针异常
- Java的Object对象中的equals、hashCode以及toString方法的调用都会转发到InvocationHandler中进行处理

##### 代理类对于接口的包、模块的范围是有要求的

- 如果代理实现的接口都是开放的包路径中
  - 如果所有的接口都是public，那么这个代理类也是开放的
  - 如果至少有一个接口是非public的，那么在这个非公共接口的包路径中这个代理类是非公共的。所有的非公共接口必须在同一个包和模块中，不然要代理这些接口是不可能成功的
- 如果至少有一个接口在非公开的包路径下
  - 如果所有的接口都是公共的，那么这个代理类在这个非公开包下是public的
  - 如果有一个是非public的接口，这个代理类在这个包下面就是非public的，非公共接口必须在同一个包和模块中，不然要代理这些接口是不可能成功的

##### 如果代理类实现的接口冲突了怎么办？

- 当两个接口中的方法的签名和参数一样，那么继承接口的顺序就很重要。并不会根据你指定的接口引用类型来决定调用哪个方法，而是会严格按照implements的顺序来决定
- 对于equals、hashCode和toString重名的接口方法，会使用Object的那些方法而不是接口中的
- 对于异常的问题：只会在所有的接口中的方法中指定一个异常，如果抛出的异常不在接口声明的异常中，将会抛出UndeclaredThrownException异常，因此getExceptionTypes获取到的异常中抛出也并不能保证invoke的时候可以成功抛出

#### CGLIB实现动态代理

引入CGLIB依赖，然后创建需要被代理的类即可，被代理的类不需要接口，只需要实现对应的方法即可

```java
public class UserDao {
    public void select() {
        System.out.println("UserDao 查询 selectById");
    }
    public void update() {
        System.out.println("UserDao 更新 update");
    }
}
```

编写方法拦截器：需要继承MethodInterceptor，用来进行方法的拦截回调

```java
import java.lang.reflect.Method;
import java.util.Date;

public class LogInterceptor implements MethodInterceptor {
    /**
     * @param object 表示要进行增强的对象
     * @param method 表示拦截的方法
     * @param objects 数组表示参数列表，基本数据类型需要传入其包装类型，如int-->Integer、long-Long、double-->Double
     * @param methodProxy 表示对方法的代理，invokeSuper方法表示对被代理对象方法的调用
     * @return 执行结果
     * @throws Throwable
     */
    @Override
    public Object intercept(Object object, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        before();
        Object result = methodProxy.invokeSuper(object, objects);   // 注意这里是调用 invokeSuper 而不是 invoke，否则死循环，methodProxy.invokesuper执行的是原始类的方法，method.invoke执行的是子类的方法
        after();
        return result;
    }
    private void before() {
        System.out.println(String.format("log start time [%s] ", new Date()));
    }
    private void after() {
        System.out.println(String.format("log end time [%s] ", new Date()));
    }
    
    // 测试
    public static void main(String[] args) {
        DaoProxy daoProxy = new DaoProxy(); 
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(Dao.class);  // 设置超类，cglib是通过继承来实现的
        enhancer.setCallback(daoProxy);

        Dao dao = (Dao)enhancer.create();   // 创建代理类
        dao.update();
        dao.select();
    }
}

```

还可以配置方法的回调过滤器，进行过滤筛选，针对特定的方法执行不同的回调逻辑或者不执行某些回调逻辑

```java
public class LogInterceptor2 implements MethodInterceptor {
    @Override
    public Object intercept(Object object, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        before();
        Object result = methodProxy.invokeSuper(object, objects);
        after();
        return result;
    }
    private void before() {
        System.out.println(String.format("log2 start time [%s] ", new Date()));
    }
    private void after() {
        System.out.println(String.format("log2 end time [%s] ", new Date()));
    }
}

// 回调过滤器: 在CGLib回调时可以设置对不同方法执行不同的回调逻辑，或者根本不执行回调。
public class DaoFilter implements CallbackFilter {
    @Override
    public int accept(Method method) {
        if ("select".equals(method.getName())) {
            return 0;   // Callback 列表第1个拦截器
        }
        return 1;   // Callback 列表第2个拦截器，return 2 则为第3个，以此类推
    }
}

```

CGLIB创建动态代理类的原理：

1. 查找目标类上所有的非final的public方法定义
2. 将这些方法的定义转换为字节码
3. 将组成的字节码转换成相应的代理的class对象
4. 实现MethodInterceptor接口，用来对代理类上面方法请求的处理

### 面试题

#### 描述动态代理的几种实现方式？分别说出相应的优缺点

代理可以分为 "静态代理" 和 "动态代理"，动态代理又分为 "JDK动态代理" 和 "CGLIB动态代理" 实现。

**静态代理**：代理对象和实际对象都继承了同一个接口，在代理对象中指向的是实际对象的实例，这样对外暴露的是代理对象而真正调用的是 Real Object

- **优点**：可以很好的保护实际对象的业务逻辑对外暴露，从而提高安全性。
- **缺点**：不同的接口要有不同的代理类实现，会很冗余

**JDK 动态代理**：

- 为了解决静态代理中，生成大量的代理类造成的冗余；
- JDK 动态代理只需要实现 InvocationHandler 接口，重写 invoke 方法便可以完成代理的实现，
- jdk的代理是利用反射生成代理类 Proxyxx.class 代理类字节码，并生成对象
- jdk动态代理之所以**只能代理接口**是因为**代理类本身已经extends了Proxy，而java是不允许多重继承的**，但是允许实现多个接口
- **优点**：解决了静态代理中冗余的代理实现类问题。
- **缺点**：JDK 动态代理是基于接口设计实现的，如果没有接口，会抛异常。

**CGLIB 代理**：

- 由于 JDK 动态代理限制了只能基于接口设计，而对于没有接口的情况，JDK方式解决不了；
- CGLib 采用了非常底层的字节码技术，其原理是通过字节码技术为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑，来完成动态代理的实现。
- 实现方式实现 MethodInterceptor 接口，重写 intercept 方法，通过 Enhancer 类的回调方法来实现。
- 但是CGLib在创建代理对象时所花费的时间却比JDK多得多，所以对于单例的对象，因为无需频繁创建对象，用CGLib合适，反之，使用JDK方式要更为合适一些。
- 同时，由于CGLib由于是采用动态创建子类的方法，对于final方法，无法进行代理。
- **优点**：没有接口也能实现动态代理，而且采用字节码增强技术，性能也不错。
- **缺点**：技术实现相对难理解些。



## Java的SPI机制

### 什么是SPI？

Service Provider Interface：服务提供接口，是Java提供的一套被用来给第三方实现扩展的接口，可以用来启用框架的扩展和替换的组件。就是用来给别人扩展实现某些功能而存在的一类接口，也是Java热插拔和模块块化的关键。

服务注册和发现机制，解耦服务提供者和服务使用者

模块化：将一个项目分成多个模块，模块可能存在相互依赖关系

热插拔：热插拔功能就是允许用户在不关闭系统，不切断电源的情况下取出和更换损坏的硬盘、电源或[板卡](https://baike.baidu.com/item/板卡/9943355)等部件，从而提高了系统对灾难的及时恢复能力、扩展性和灵活性等



### SPI中破解双亲委派导致的问题？

子类加载器可以使用父类加载过的类，但是父类加载器不能使用子类加载器加载过的类。就好像Java中的继承关系一样。

Java的SPI机制就是用来让第三方实现某些接口从而提供一些新的功能和模块，类似于JDBC就是SPI，这些SPI接口由Java核心类提供，具体实现在第三方，这些提供者是交给Boostrap ClassLoader来加载，但是第三方的实现类是由自定义的ClassLoader加载，这个时候顶层的类加载器不能使用第三方的自定义类加载器加载过的类

### 解决？

打破双亲委派机制：可以创建自定义类加载器（继承ClassLoader）并重写loadClass和findClass()方法

使用线程上下文类加载器加载类（ContextClassLoader）

Java应用上下文加载器默认使用APPClassLoader，想要在父类加载器使用到子类加载器加载的类可以使用`Thread.currentThread().getContextClassLoader()`

```java
// 使用线程上下文类加载器加载资源
public static void main(String[] args) throws Exception{
    String name = "java/sql/Array.class";
    Enumeration<URL> urls = Thread.currentThread().getContextClassLoader().getResources(name);
    while (urls.hasMoreElements()) {
        URL url = urls.nextElement();
        System.out.println(url.toString());
    }
}
```

### Java的SPI使用过程图

1. 目录创建：在classpath路径下创建META-INF目录
2. 文件创建：在上面创建的目录下面创建配置文件，配置文件名需为扩展接口全名，文件内容是扩展接口的实现类全名，文件编码是UTF-8
3. 使用：ServiceLoader.load(xxx.class)

```
c-spi                          
│
├─ src                 
│  │
│  ├─ main          
│  │  ├─ java      
│  |  |  ├─ cbuc.life.spi.service .spi.service            service层的目录 
│  │  │  │  ├─ impl                                       service的实现类所在的目录
│  │  │  │  |  |-CustomeServiceOne                        service实现类
│  │  │  │  |  |-CustomeServiceTwo
│  │  │  │  |  |-CustomeServiceThree
|  |  |  |  |-ICustomService                              service服务提供接口
│  |
│  |  ├─ resource      
│  │  │  ├─ META_INF.services         
│  │  │  │  |-cubc.life.spi.service.ICustomService        配置文件
|——pom.xml         
```

```java
// 服务提供接口
public interface ICustomService {
    public String getName();
}

// 服务实现者
public class CustomServiceOne implements ICustomService {
    @Override
    public String getName() {
        return "cutomeServiceOne";
    }
}
public class CustomServiceTwo implements ICustomService {
    @Override
    public String getName() {
        return "cutomeServiceTwo";
    }
}
public class CustomServiceThree implements ICustomService {
    @Override
    public String getName() {
        return "cutomeServiceThree";
    }
}

// 使用第三方实现
public class Test {
    public static void main(String[] args) {
        // 服务加载器加载第三方服务实现类
        ServiceLoader<ICustomService> services = ServiceLoader.load(ICustomService.class);
        services.forEach(s->System.out.println(s.getName()));
    }
}
```

### 实现原理

关键在于ServiceLoader类的使用

```java
public final class ServiceLoader<S> implements Iterable<S> {}
```

load()方法：

```java
// 获取指定服务的加载类
public static <S> ServiceLoader<S> load(Class<S> service) {
    ClassLoader cl = Thread.currentThread().getContextClassLoader();
    return new ServiceLoader<>(Reflection.getCallerClass(), service, cl);
}  

// 获取类加载器
public static <S> ServiceLoader<S> load(Class<S> service,
                                        ClassLoader loader)
{
    return new ServiceLoader<>(Reflection.getCallerClass(), service, loader);
}

// 
static <S> ServiceLoader<S> load(Class<S> service,
                                 ClassLoader loader,
                                 Module callerModule)
{
    return new ServiceLoader<>(callerModule, service, loader);
}
```

构造方法：

```java

```

