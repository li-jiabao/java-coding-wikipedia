---
title: Polymorphism
author: lijiabao
date: 2021-02-16 23:42:48.521370000 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
多态是面对对象编程的一大重要特性，特别在一个父类有众多个子类的时候，多态的特性更容易展现。可以利用接口和类的type reference来实现多态

```java
class Person{
	public Person(){
	}
	public void printName(){
	}
}
class Student{
	public Student(){
	}
	@Override
	public void printName(){
	}
	public printGrade(){
	}
}
public class Test{
	public static void main(String[] args){
		Person person = new Student();  
		// 这种type cast可以实现多态，利用父类的type reference调用子类的重写方法，但是不能调用父类中没有的方法
	}
}
```

#### dynamic binding
动态绑定是Java运行中的特性，这项特性用来去确定调用哪个方法。与动态绑定对应的是静态绑定static binding。
动态绑定的步骤：
1. Java编译器查看声明的对象类型和方法
2. 通过参数对象的类型去确定调用的方法，用参数的类型去找寻最佳的匹配。
3. 在运行时，通过动态绑定来确定调用的方法，利用之前编译器生成的method table去查找应该调用的方法，这个表在类的等级制度下生成的，方法表中主要是类名.方法名(param)。静态绑定是指那些定义为static final的方法，这些方法都是在编译阶段就可以确定哪个类下的哪个方法调用
4. 这个动态绑定主要靠JVM来实现，JVM首先找到调用对象的实际类型，并去该类下查找方法，如果找不到，就去到父类中查找，在类的等级制度中一层一层寻找，如果找不到，抛出错误。（JVM取出方法表，JVM确定对象类型，确定调用方法，JVM调用方法）