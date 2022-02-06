---
title: Interface
author: lijiabao
date: 2021-02-03 23:53:55.777319300 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
接口时Java中很重要的一部分，接口只可以被implements，并不可以实例化接口。
接口中可以有静态fields(但是默认被定义为final fields，不可修改)，抽象方法（abstract默认就是，可不写）、静态方法（static,必须有方法体）、默认方法（default，必须有方法体）。
==所有的方法默认是public的，因此可省略public==
`access_modifier interface InterfaceName{}`

```Java
public interface FuncInterface{
	static String name = "FuncInterface"; // 默认final不可修改
	void prinName(); // 抽象方法，implements该接口的类必须实现的方法
	String getName();
	default void setName(String name){
		this.name = name;
	}; // 默认方法，必须有方法主体，子类implements时，要么不提该方法，要么重写该方法。
	static String getFields(){
		return this.name
	} // 静态方法可以覆盖hide，不重写利用类对象访问不了static方法，只能通过接口名来访问静态方法
}
```

扩展接口时：对于原有的方法可以不用提，直接从父接口继承过来。特别是default方法，要么不提，要么重写。

default方法的存在可以让原来使用这个接口的类不需要更新还能继续使用，不会出错，不然，接口更新了一个抽象方法之后，原来的类也需要去更新，实现方法重写。这样做可以让用户决定是否更新或者继续使用，从而维护了正常使用让用户不知情的情况下认仍然可以继续使用

Java中的interface也可以用来type reference，正式由于类和接口的reference，组成了Java的多态


### lambda表达式
语法：
`参数 -> 表达式`
`(first, second) -> second-first`
如果一行代码解决不了，可以使用{}来包裹代码块
一般lambda在java中主要使用在那些需要传入函数参数的地方

1. lambda表达式可以访问外围参数，但是有限制，只能访问那些变量值不会改变的，如果可以个改变那么，并发执行多个动作就会不安全
2. lambda表达式捕获的参数必须是事实最终变量（变量初始化之后不会再赋新值）
3. lambda表达式中不可以声明包裹体的变量同名的参数或者同名的局部变量。
4. lambda中使用this是使用的创建这个lambda表达式的方法的this

==lambda表达式类似于别的语言中的闭包==

### 函数式借口
对于那些只有一个抽象方法的接口，就叫做函数式接口
对于函数式接口的对象的创建，可以提供一个lambda表达式

