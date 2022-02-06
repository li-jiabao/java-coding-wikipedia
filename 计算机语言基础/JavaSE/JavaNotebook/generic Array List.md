---
title: generic Array List
author: lijiabao
date: 2020-12-01 00:17:57.099318700 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
`public class ArrayList<T>{}`

用来对数组进行随心所欲的操作，可以动态变化的数组。
正常的数组只能实现定义好数组的长度再进行使用
```
Scanner s = new Scanner(System.in);
if s.hasNextInt(){
	int arraySize = s.nextInt();
}
int[] array = new int[arraySize]; 
// 只能使用类似的方法做到按运行的值来确定指定数组长度
```

但是这种方式并不能达到动态修改添加数组元素的目的，因此为了达到动态修改数组，就需要使用ArrayList类，他是一个泛型，在创建的时候需要指定类型。
`ArrayList<Integer> array = new ArrayList<>();`
创建了一个数组列表，此时利用这个对象来进行动态增加修改删除和获取数组元素了。
- `get(int index)`：获取指定索引处的值
- `add(T obj)和 add(int index, T obj)`：向数组中添加值，第二个是向指定索引处添加值，如果超出范围，数组列表会重新分配存储
- `set(int index, T obj)`：替换修改指定索引处的值，如果该索引无值，这个方法并不会添加值
- `T remove(int index)`：去除指定索引处的值并返回
- `int size()`获取数组列表的数量,数组使用length属性获取
- `void eusureCapacity(int capacity)`设定数组列表大小，也可在构造方法传入一个数组指定
- `void trimToSize()`减少数组列表的存储能力为当前的数量，gc会回收多余的内存

由于`trimToSize`方法的存在，当你确定你的列表已经完成修改时，可以使用这个方法来释放内存。
如果你想要使用灵活变化和访问方便的时候，可以先用数组列表，进行操作，当你 确定你已经完成多有操作时，可以使用`toArray`方法来转换为数组，这时候就可以使用[]操作符来方便的访问元素了


##### 泛型和原始类型之间的兼容性

为了保证兼容性，JVM会在运行时将泛型的类型擦除。

当你将泛型作为变量转递到指定要原始类型的参数时，并不会报错和警告，但是并不是完全安全的。不过由于虚拟机的作用，并不会出错，只是丢失了编译时检查的好处。

而如果将原始类型给到泛型，会出现警告信息，入如果使用类型转换，又会出现另外一种警告信息，可以使用 -xlint:unchecked查看警告信息。这是由于泛型的缺陷导致的，由于泛型的类型擦除的存在，在运行时，并不会带有类型信息，因此都是一样的，对于这种情况没啥可以解决的，如果不严重就可以使用@SuppressWarning("unchecked")去除警告信息。
