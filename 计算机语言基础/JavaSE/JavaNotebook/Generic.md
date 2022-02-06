---
title: Generic
author: lijiabao
date: 2021-02-17 00:06:52.274702800 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
# Generic
### [[generic Array List]]


### concept
generic is used for ==stronger type checking==(for compile-time safety), and you can use one declaration to have ==many method or many type class which have same name==, but these have different type. Generic can specify a set of related methods or classes with a single generic declaration. 

### why use generic?
* ==stronger type checking and compile-time safety==
* ==elimination cast==
* ==Enable programmers to implement generic algorithms that work on colections of different types, and these are type safe and easier to read.==

### generic class or interfaee
```Java
public class ClassName <T>{
	private T t;
	public ClassName(T t){
		this.t = t;
	}
	public static void main(String[] args){
		ClassName<Number> numberClass = new ClassName<>();  //  <>diamond operator
	}
}
public interface InterfaceName <T>{
	void printHello();
}
```
Above is declaration of generic class. First, **access modifier**, then **ClassName**, then **type parameters **

### generic type 
You can call generic class or generic interface as generic type.
**the diamond operator**：when generic instancialization happen, you can use the diamond to omit the type parameter after new keyword.
**parameterized type**：this is a generic type after pass specific type parameter, you can pass it to type parameters.

### raw type
You can call this type instancialization as raw type when you instancialize a generic type without type parameters.
`ClassName<Integer> integerClass = new ClassName<>();`
`ClassName rawType = new ClassName();`
```Java
ClassName rawtype1 = integerClass;   // ok, no error message

ClassName<Integer> integerClass1 = rawType;   // warning, unchecked conversion
```
**There will get unchecked error message, when mixing legacy code with generic.**

### generic method
```Java
public ClassName<T>{
	private T t;
	
	public <T> void methodName(T t){
		this.t = t;
		System.out.println(t);
}
}
```
Above is the declaration of generic methods.First, **access modifier**,then **parameters type**, then **return type**, then **methodName**, then **parameters in parathesis**

### parameter type name convention
- E, represent collections
- N, represent Number
- T, type, represent Objects, first one
- K, represent key of map
- V, represent values of map
- S, represent second type
- U, represent third type
- V, represent Fouth type
- 
### bounded type parameters
- single bounded
	`<T extends Number>`
It is a bounded type parameter. This can be used for restricting the passed type.
**Bounded type parameter allows you to invoke the bounded type's defined methods.**
- multiple bounded
	`<T extends A1 & A2 & A3>`
	It is a multiple bounded type parameter.If extenden types have class, it must be placed in the first.
	In this case, you can pass these type and its subtypes to be parameter.

### input variable and output variable
- input：pass datas to the code ,provide datas
- output：hold datas which has been updated, use to accept datas.


### wildcard
- upper bounded wildcard ：`<? extends Type>`(input variable, use upper bounded)
- lower bounded wildcard：`<? super type>`(output variable ,use lower bounded. because if use lower bounded, other subclass also can use super to cast type, in case wrong things)
- unbounded wildcard：`<?>` ->Object(unsure whether is input or output)

### generic's characters
- No primitive type：can not pass primitive data type to generic.
- No instance：parameter type can not be instancialized, but you can use reflection to achieve the same effect.`T t = TName.newInstance()`
- No static fields：If have static fields（shared among objects）, will cause compiler don't know use which type。
- No cast：In generic, you don't need to cast type.
- No instanceOf：You can't veryfied the difference between Box<Integer> and Box<String>, which is caused by type erasure.
- No Array：due to type erasure, you can add any type into array, this will cause array can't throws ArrayStoreException.
- No Exception：can't use type parameter in catch block.
- No overload：Due to type erasure, you can't use Box<String> and Box<Integer> to overload method.


### type erasure
generic is only used to strength type check, when comes to runtime, the parameters will be erased by a process which is called as type erasue.
- unbounded : after type erasure, it is Object
- bounded: after type erasure, it is the upper bounded.

For connection, compiler will generate a bridge method.Why need bridge method, it is for ploymorphism