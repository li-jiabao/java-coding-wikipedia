---
title: Inheritance
author: lijiabao
date: 2020-12-01 13:06:40.697732400 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
##### 继承的技巧：
1. 将共同的操作和属性放在父类中
2. 尽量不使用protected属性，因为protected提供的保护并不到位，每个人都可以写一个子类来访问你的保护的变量，会破坏封装性，同样，在同一个包下的类也可以访问该保护变量。但是，当你需要在子类重新定义或者还没准备一般使用的方法来说，protected很有用
3. 不要滥用了继承中的is-a关系，只用继承来模拟is-a关系，对于不是is-a关系的不用继承关系，不然有时候使用继承反而使得代码更复杂。
4. 除非所有继承的方法都有意义，否则不适用继承
5. 重写方法时不要改变expected behavior，应该要满足替换原则才好
6. 使用多态而不是类型信息，多态的使用可以省去不必要的代码
7. 不要过度使用反射机制，反射是很脆弱的，编译器不能帮助你找出问题，而且反射很容易出错

## 继承
首先创建一些具有特定内容的对象，但是突然有需要添加一个新的对象，但是只是在原有类的基础上添加了一些新的内容，这个时候，我们想要有一种方法可以在原有的对象上面进行修改添加，从而可以节省许多的工作量和代码量，这种方法就是继承。

继承是指新生类继承了原有类的内容，并在原有类的基础上进行了内容的丰富添加。
```Java
public Animal {
	public void getSex(){
		System.out.print("sex");
	}
}

public Cat extends Animal{
	public void printName(){
		System.out.print("name");
	}
}

```
如上代码，就是在Animal的基础上，通过继承而生成了新类。Cat类继承了原有类的getSex方法，同时自己又新添加了printName方法。

子类继承时，对于父类中的private的变量是没有访问权限的，子类只可以进行重定义覆盖。

继承时，若是重写了构造方法，而没有重写无参数的构造方法时，来使用这个会报错。如果子类没有显示invoke父类的构造方法，java编译器会自动调用父类的无参数构造方法，如果父类没有无参数构造方法，会报编译时错误。Object确实有无参数方法，如果==object是唯一父类，没啥问题==

### override
子类对于父类的方法可以进行override，就是定义和父类的方法名参数和返回值一样的方法，但是访问权限可以比父类的更放松（比如protected 可以是public或者default）当父类的==type cast==应用到子类的时候，调用override方法时，==优先调用的是子类中的==。如果重写方法的时候没有满足条件，并不会出现重写。在annotation中有一个==@Override==，可以在子类重写的方法上标注，如果==编译的时候重写未成功会出现编译错误==。
```java
public Animal{
 	public void getName(){
		System.out.print("name");
	}
}
public Cat extends Animal{
	@Override  // 带上这个annotation时，重写不成功就会报错
	public void getName(){
		System.out.print("cat");
	}
}
```
重写不成功：
- 变量数量和变量顺序对不上
- 变量类型不匹配
- 返回值类型不匹配
- 名称不匹配

### hide
如果定义一个和父类的静态方法同名的方法，只会隐藏父类中的静态方法，并不会重写该方法，并且在决定调用哪一个方法时，主要通过查看是哪个类调用该静态方法

### hide fields
当子类中的fileds名字和父类的fields一致时，就会隐藏父类中的fields（类型不一样也会，并不建议这样去隐藏父类的fields）==在父类中的fields在子类中访问不能用变量名而需要使用super关键字来进行访问==

### this keyword 
用来在构造方法或者实例方法中指代当前对象（当方法的参数变量名和fields冲突时，利用this防止冲突），可以用来调用类中的fileds`this.fieldsName`也可以用来调用实例方法`this.instanceMethodsName()`，还可以用来调用当前类的其他构造方法`this()`可以传入参数。

### super keyword
如果父类的方法被重写了但是还想调用父类的重写方法可以使用super关键字来达成`super.methodName();`也可用来调用父类中的构造方法`super()`，当然也可以用来调用父类的fields`super.fieldsName`。不过super只能在非静态方法中使用或者构造器或者在方法外类中也可使用。
==NOTE：==如果构造器中没有明确invoke父类的构造器，编译器会自动invoke父类的没有参数的构造器，如果==父类中没有这样一个构造器，就会编译错误==。Object确实有这样一个构造器，如果object是唯一父类，没啥问题

在调用父类构造方法时，不管显式还是隐式，都会有一系列的构造器调用，直到回到Object（所有类的父类）这种叫做构造器链（constructor chainning），==当有长而多的class descent时，需要注意了==

### type cast
type cast是使用父类的类型引用，但是指向的是子类的new构造方法，由于这种情况的存在，可以实现多态这另外一个oop的特性。
```Java
public Animal{
 	public void getName(){
		System.out.print("name");
	}
}
public Cat extends Animal{
	public void getName(){
		System.out.print("cat");
	}
	public static void main(String[] args){
		Animal animal = new Cat();
		Cat cat = new Cat();
		Cat cat1 = (Cat)animal;  // 使用这种强制类型引用，可以将拥有父类引用的调用了子类构造方法的变量强制转换为子类,但如果子类索引转换为父类会报错
		animal.getName();  // 尽管使用的父类引用，但是调用被子类重写的方法时，会调用子类的方法
	}
}
```


### [[Object]]
所有类的父类，类等级制度中的最高一层，每个类都显示或者隐式的是Object的子类。