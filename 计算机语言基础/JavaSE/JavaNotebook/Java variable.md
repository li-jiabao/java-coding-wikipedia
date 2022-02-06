---
title: Java variable
author: lijiabao
date: 2021-02-03 23:21:17.433537100 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
类中Java的不同类型的变量，访问的方法最好是：

- static fields：ClassName.filedsName(可以使用对象访问，但实际上，静态变量每个类只有一份拷贝)静态方法访问非静态成员时编译会报错。静态成员在类中多有对象是共享的 。
- instance fields ：Obj.fieldsName(可以使用类访问，实例变量每个对象有一份拷贝)
- this keyword：this指代当前的对象，可以用来访问当前对象的instance变量和构造方法，this可以在构造方法和实例方法中调用来访问当前对象的成员，==this在静态方法使用会报错==。this.fieldsName，this()（当前对象的构造方法的调用，可以传入参数）


可变变量（Type... varName）这种变量形式就是可变数量的变量

#### var关键字的使用
`var`是java10之后新加的一项功能，如果可以从变量的初始值推出变量的类型，用于声明局部变量，而无需指定类型
`Employee a = new Employee("jiaboa",15);`
`var a = new Employee("jiabao",15);`