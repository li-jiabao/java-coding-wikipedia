---
title: Java data type
author: lijiabao
date: 2020-11-13 22:55:13.906300500 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
- primitive type

前六个原始数据类型有相关的数据类，他们是[[Java Number]]类的子类。
这些数据类和原始数据类型之间发生的类型和类的转换称为**装箱**（将原始数据转变为类）和**拆箱**（将引用类型转变为原始数据类型）

1. byte：字节型，八位，默认值0。 和**Byte**类存在box和unbox关系
2. short：16位，默认值0。**Short**
3. int：32位，默认值0。**Integer**
4. long：64默认值0L。 **Long**
5. float：32位，默认值位0.0f。**Float**
6. double：64位，默认值0d。**Double**
7. char：16位Unicode，字符型，'a', **Character**
8. boolean：表示true或者false，默认false。 **Boolean**

- [[Java String]]

字符串类：定义为了final，因此String类不可以被继承。可以通过类对象创建方法创建字符串对象，也可以使用""传入想要的内容创建。


- reference type
指的是引用类型，类似C中的指针。指向的是一个对象，指向对象的这个变量叫做引用变量，这些类型在声明的时候被指定为特定的对象，一旦指定就不再更改，但可以再将其assign给另外的引用变量（类型需要匹配）引用类型的默认值为null。