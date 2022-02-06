# JAVA

## Java序列化

序列化的应用场景：

- 当需要将某个对象的状态保存以便下次直接使用
- 类似于游戏的存档机制，需要保留必要的数据，下次直接可以反序列化获取原来的状态
- 用来远程传输对象，例如远程过程调用RPC就需要利用序列化反序列化来辅助RPC实现

序列化就是将对象转换为字节流，反序列化就是将字节流转换为对象

序列化的特点：

- 静态成员不需要保存，对象序列化不关注类中的静态成员

Java的序列化实现：

- 类实现Serializable接口
- 使用`ObjectInputStream`和`ObjectOutputStream`对对象进行反序列化和序列化
- 类中增加`writeObject`和`readObject`方法可以实现自定义序列化策略
- 序列化ID：虚拟机是否允许反序列化，取决于类路径和功能代码是否一直，还需要看两个类的序列化ID是否一致（`private static final long serialVersionUID`）

`transient`防止变量被序列化到文件中，一定程度保证序列化对象的数据安全

## Java复制

### 直接赋值

直接赋值只是将引用指向了该引用所属的对象，并没有创建一个新的对象，其中一个引用改变了对象的内容，另外一个引用指向的对象也会发生同样的改变

### 浅拷贝

只是复制了对象，对象中所有的引用都不会复制一份对象，只是复制了一份引用，因此，浅拷贝对象的引用发生某些修改操作会引起原来引用的对象的内容修改

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

### 深拷贝

不仅复制对象，对于对象引用的对象也进行一份拷贝

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

### 序列化实现深拷贝

对象先实现Serialiable接口，然后再利用对象的ObjectInputStream和ObjectOutputStream序列化和反序列化出一个新的对象即可（保存对象状态，然后重建对象）