---
title: Enum
author: lijiabao
date: 2020-11-30 23:26:22.910851900 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
枚举类型
`public enum EnumName{CONSTANT1(values), CONSTANT2(values),...,CONSTANTn(values);}`

示例：
```java
public enum Size{
	SMALL("S"),MEDIUM("M"),LARGE("L"),EXTRAL_LARGE("XL");
	private String abbrevation;
	private Size(String abbrevation){
		this.abbrevation = abbrevation;
	}// 构造方法需要根据常量后括号里面的值来确定怎么生成
	public Stirng getAbbrevation(){
		return this.abbrevation;
	}
}// 所有的Enum类型都是Enum类的子类。

Size s = Enum.valueOf(Size.class, "SMALL");
String string = Size.SMALL.toString(); // 返回的是常量的名字
Size[] size = Size.values(); // 返回的是所有元素的数组
int i = Size.SMALL.ordinal();  // 返回的是元素的位置，从0开始
int bool = Size.SMALL.compareTo(Size.LARGE);  // 比较两个元素的位置，也就是定义的顺序，int compareTo(E other)


```