# 单例模式的实现方式

## 懒汉模式

在还不需要使用类的实例时就已经将类的实例创建好了

```java
public class Singleton01 {
    private static Singleton01 instance = new Singleton01();
    private Singleton01(){}
    
    public static Singleton01 getInstance() {
        return instance;
    }
}
```

```java
public class Singleton01 {
    private static Singleton01 instance;
    static {
        instance = new Singleton01;
    }
    private Singleton01(){}
    
    public static Singleton01 getInstance() {
        return instance;
    }
}
```

## 懒汉模式

懒汉模式是指在需要使用实例的时候才开始创建单例

1. 线程不安全的懒汉模式实现

```java
public class Singleton02 {
    private static Singleton02 instance;
    
    private Singleton02(){}
    
    public static Singleton02 getInstance() {
        if (instance==null) {
            instance = new Singleton02();
        }
        return instance;
    }
}
// 这种方式是线程不安全的
```

2. 线程安全懒汉实现

```java
// 使用了synchronized加锁机制保证线程安全，但是锁粒度太大,且浪费资源
// 每一个创建实例的方法调用时都会去竞争锁资源，带来不必要的开销
public class Singleton03 {
    private static Singleton03 instance;
    
    private Singleton03(){}
    
    public static synchronized Singleton03 getInstance() {
        if (instance==null) {
            instance = new Singleton03();
        }
        return instance;
    }
}
```

3. 降低锁粒度的实现

```java
// 锁的粒度是下降，但是实际上，这个单例的实现还是线程不安全的
// 可能会有两个线程同时走到判是否为空之后的创建实例的这一步，只是有一个先拿到锁，但此时的两个线程各自缓存中保存的instance还是null
// 当线程释放锁之后，另外一个线程执行，还是会进行类的实例化
public class Singleton04 {
    private static volatile Singleton04 instance;
    
    private Singleton04(){}
    
    public static Singleton04 getInstance() {
        if (instance==null) {
            synchronized(Singleton04.class) {
                instance = new Singleton04();
            }
        }
        return instance;
    }
}
```

4. 防止上一种实现的错误，加了一个双重检查的机制，在创建实例的线程安全的代码块中执行的时候，会进行一次判空操作

```java
public class Singleton05 {
    private static volatile Singleton05 instance;
    // 为什么这里需要加上volatile，必须要加嘛？
    // 必须要，因为对象在创建过程中实际上分为了好几个步骤，因为计算计CPU存在指令重排序，一旦这里出现了指令重排序，就会导致实例在创建的时候会出现判空的情况，就会重新创建一个新的实例，导致不是真正的单例实现
    
    private Singleton05(){}
    
    public static Singleton05 getInstance() {
        if (instance==null) {
            synchronized(Singleton05.class) {
                if (instance==null) {
                	instance = new Singleton04();
                }
            }
        }
        return instance;
    }
}
```

## 静态内部类

利用类加载器的懒加载机制从而实现的一种单例模式

```java
public class Singleton06 {
    private static class InstanceHolder {
        // 在静态内部类定义一个常量，但是这个常量由于存在内部类内部，再加上类加载机制的懒加载
        // 此时的这个变量和内部类并不会被加载创建该变量
        private static final Singleton06 instance = new Singleton06();
    }
    
    public static Singleton06 getInstance() {
        // 只有当第一次调用这个方法的时候，才会去加载这个静态内部类并创建这个常量
        return InstanceHolder.instance;
    }
}
```

## 枚举

利用枚举的特点实现，不仅可以解决线程安全问题，还可以解决序列化和反序列化问题

```java
public class Singleton07 {
    private Singleton07() {}
    
    private static enum SingletonHolder {
        SINGLETON;
        private Singleton07 instance = null;
        
        private SingletonHolder() {
            instance = new Singleton07();
        }
        
        public Singleton07 getInstance() {
            return instance;
        }
    }
    public static Singleton07 getInstance() {
        return SingletonHolder.SINGLETON.getInstance();
    }
}
```

