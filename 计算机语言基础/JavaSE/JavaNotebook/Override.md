---
title: Override
author: lijiabao
date: 2020-11-21 21:52:22.937928700 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
重写是指子类中的方法名称、变量类型、变量数量、变量顺序以及返回值类型和父类中的一致这种作法。当然，若是子类的返回值类型是父类的子类也可以满足重写的情况
```Java
public Animal{
	public Number getNum(){
		Number num = new Integer(1);
		return num;
	}
}
// 返回值类型子类的是父类的返回值的子类时，也是可以满足重写条件的
public Cat extends Animal{
	@Override
	public Integer getNum(){
		Integer num = new Integer(1);
		return num;
	}
}
```
