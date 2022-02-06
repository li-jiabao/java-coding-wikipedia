---
title: Abstraction
author: lijiabao
date: 2020-11-29 16:40:38.304854000 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
当你不care类的某些方法的具体实现逻辑的时候，可以将其定义为抽象方法等待继承它的类来进行其特有的该方法的实现逻辑，当类中有方法定义为抽象方法的时候，就可以将该类定义为抽象类。

对于抽象类的继承，有两种选择：
- 不用全部重新定义方法，但是这样的话子类还是抽象类
- 定义全部的方法，子类不再是抽象类

抽线类不可以实例化，抽象类么有实际对象，但是抽象类是可以使用多态的，抽象类变量可以指向子类对象。

==定义抽象类的时候，若是implements接口，如果没有覆盖接口中的方法，也不会出错==

抽象是Java的一个重要特性，可以定义抽象类抽象方法。使用abstract关键字来定义

- 抽象类：abstract class ClassName{}，抽象类中可以有抽象方法也可以无抽象方法，但是有抽象方法的类必须定义为抽象类。抽象类中可以定义fields，和正常类一样，只是==抽象类不可以实例化==，只能被继承。抽象类和接口类似，但是抽象类相比接口，还可以定义非静态的fields，有主体的方法（和类中方法定义一样）
```java
public abstract class MyAbstractClass{
	public String Name;  // 可以和接口一样定义public变量
	private String password;  // 比接口还可以定义非public变量
	public static final ID = 12345;
	abstract String getName();   // 抽象方法，需要abstract关键字
	// 如果类中有这个，需要定义为抽象类
	// 抽象类中还可以定义非public的其他修饰符的方法
	protected void printName(){
		System.out.println(this.name);
	}
	public static void getID(){
		System.out.println(this.ID);
	}
}
// 抽象类不可实例化，只可以被继承，也可以使用抽象类来做type cast
```

- 抽象方法：在抽象类中，以abstract为修饰符定义的方法，接口中，方法默认为抽象方法，可以省略不写abstract。抽象方法不可以有主体。


抽象类和接口的使用情形：
- 使用抽象类
1. 当你需要和几个相近的类共享代码的时候
2. 你期望扩展拥有共同方法和属性的抽象类或者需要有除了public 之外的其他修饰符的时候
3. 想要定义非静态和非final的属性的时候，这样使得你可以对对象的状态进行修改

- 使用接口
1. 你需要不相关的类可以使用该接口
2. 想要使用多种继承的好处的时候
3. 只想指定需要的方法而不考虑具体方法如何实现




