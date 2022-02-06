---
title: Inner class
author: lijiabao
date: 2020-12-06 16:08:42.051726200 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
内部类有一个**隐式引用**，创建一个指针指向引用实例化该内部对象的外部对象。

内部类有以下几种：
- 非静态内部类
- 静态内部类：不能访问外部类的对象，只能访问静态成员
- 局部类
- 匿名类

内部类的使用：
	- 静态：`OuterClass.InnerClass a = new OuterClass.InnerClass`
	- 非静态：`OuterClass.InnerClass a = outObj.new InnerClass`

内部类对于外部类的引用正式语法：OuterClass.this

### [[Local class]]

### [[Annoymous class]]

### [[Static inner class]]