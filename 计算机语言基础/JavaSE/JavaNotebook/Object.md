---
title: Object
author: lijiabao
date: 2020-11-30 23:45:30.204644700 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
object内部有许多的方法：
- `protected Object clone() throws CloneNotSupportedException`：如果类或者他的父类implements了Cloneable接口，可以使用clone方法去获得已有对象的副本。最简单的实现可以调用clone()的方法是定义类去implements Cloneable接口。对于某些类，其Object的clone()方法可以正常工作，但是，当对象包含==外部对象的reference时，需要重写clone()方法，以防外部类修改从而导致副本也跟着一起修改==（意味着原来的对象和他的副本并不是独立的）为了decouple他们，==需要重写clone()来复制对象和外部对象，原来对象指向外部对象，对象副本指向外部对象的副本==，从而使得对象和副本真正独立。
- `public boolean equals(Object obj)`：用来测试两个对象是否相等。Objects的equals方法通过\=\=判断，对于原始数据类型这是正确的，但是对于对象，只是判断object reference是否相等（是否两个对象指向的是同一个对象，需要进行复杂的判断时，需要自己重写这个方法才好）因此为了测试是否含有相同信息，需要重写这个方法。判断的是是否指向的是同一个reference对象
写一个完美的equals方法需要有一下几点：
1. 把明确的参数命名为otherObject，随后，需要将其转换为其他类型时，将其命名为other。并且参数类型需要时`Object`
2. 测试this是否和otherObject一致`if (this == otherObject) return true`,测试身份比测试fields更方便
3. 测试是否otherObject为null，`if (otherObject == null) return false`.这个测试是有要求有的
4. 比较this和otherObject的classes，当子类父类的equals的语义不变时，可以使用instanceof：`if !(otherObject instanceof ClassNmae) return false;`,语义可以改表时，使用getClass：`if (getClass != otherObject.getClass()) return false;`
5. 转换变量时，将其命名为other，并转换为你的ClassName
6. 比较fields，原始数据类可以用等于判断，类就可以使用`Object.equals()`判断，数组元素可以使用`Arrays.equals()`判断，如果所有都匹配，返回true
JAVA Specification 要求equals有以下特点：
1. 映射性（自等性），x.equals(x)要返回true
2. 对称性，x.equals(y)和y.equals(x)返回相同值
3. 可交换性，类似交换律，xy和yz相等，则xz相等
4. 一致性，只要refer没改变，前后返回相同值
5. 任何非null对象和null比较要为false。

- `protected void finalize() throws Throwable`
- `public Class getClass()`：这个方法返回一个类对象，这个类对象下有你可以获取类信息的方法：getSimpleName()、getSuperClass()、and so on。就是Class类，在java.lang包中
- `public int hashCode()`：返回对象的hashcode，是对象的存储地址。equal的两个对象的hashcode值相等。如果重写了equals方法，必须重写hashCode方法，返回多个值的hash和可用，`Object.hash(Object... object)`。
- `public String toString()`：返回对象的字符串，在debug时很有用

以下几个方法使用在线程活动的synchronize
- `public final void notify()`
- `public final void notifyAll()`
- `public final void wait()`
- `public final void wait(long timeout)`
- `public final void wait(long timeout, int nanos)`