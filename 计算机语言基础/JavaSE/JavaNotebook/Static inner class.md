---
title: Static inner class
author: lijiabao
date: 2020-12-06 16:02:19.329344900 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
使用静态内部类的情况：

当目的只是为了把类隐藏在另一个类内部的时候

这个时候不需要内部类引用外部类对象，static可以取消产生的引用

在接口中声明的内部类自动成为public static

```java

public class StaticTest{
	public static void main(String[] args){
		Int[] intArray = new int[10];
		for (int i=0; i < 10; i++){
			intArray[i] = Math.random()*100;
		}
		OuterClass.Pair a = OuterClass.minmax(intArray);
		System.out.println(a.getFirst());
		System.out.println(a.getSecond());
	}
}

class OuterClass{
	static class Pair{
		private int first;
		private int second;
		public Pair(int f, int s){
			first = f;
			second = s;
		}
		public int getFirst(){return first;}
		public int getSecond(){return second;}
	}
	public static Pair minmax(int[] array){
		int max = Integer.MAX_VALUE;
		int min = Integer.MIN_VALUE;
		for(int value:array){
			if(value < min) min = value;
			if(value > max) max = value;
		}
		return new Pair(min, max);
	}
}
```