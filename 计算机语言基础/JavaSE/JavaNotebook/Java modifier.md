---
title: Java modifier
author: lijiabao
date: 2020-11-29 16:46:37.161521000 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
## Java modifier
- 访问控制符
1. default：默认控制符，仅在同一个包下的类可以访问
2. public：公共控制符，所有的类都可以访问
3. private：仅类自己可以访问。类和接口不可以定义为private。用来实现类中的封装。
4. protected：类自己和子类可以访问，以及同一包下的其他类可以访问。不能修饰类（==内部类==可以），可修饰==构造方法、方法成员、数据成员==。接口、接口成员变量和成员方法不可修饰为protected。子类可以访问符类继承来的protected方法，而不能使用类实例的protected方法


- 非访问控制符

1. static：用来声明静态成员的。静态方法不能访问类中的非静态成员(只能通过类的实例来访问类中的非静态成员)。静态成员通过`ClassName.memberName`来访问
2. final：变量中主要用来声明常量(static final)。final还能用来声明final方法（可以被子类继承但不能override）。final还能用来定义不可变类（不能被继承，final类下的方法默认是final的，但是fields不是）
3. abstract：用来声明抽象类（**抽象类不可实例化**，**只能被继承**，必须对抽象类中的抽象方法实现重写。）和抽象方法（包含abstract关键字的方法，**没有body方法实现**，需要子类重写来实现方法定义，如果**类中有抽象方法，必须声明为抽象类**）
4. transient：定义的变量不会持久化（序列化对象包含该修饰符修饰的内容时JVM会自动跳过它）
5. synchronized：该关键字声明的方法，在同一时间只能有一个线程可访问。
6. volatile：用来保证线程中通信，每次修改都会准确的返回到共享内存。任何时刻，不同的线程都能看到相同的变量值。