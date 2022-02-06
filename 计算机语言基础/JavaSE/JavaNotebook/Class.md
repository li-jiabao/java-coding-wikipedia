---
title: Class
author: lijiabao
date: 2021-03-03 13:46:28.651166700 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---

getClass()方法查找的是对象的类，不是通过类变量去查找的，这个和动态绑定确定类型的时候是一致的。
Field、Method、Constructor也是类
#### Class类
Class是一个类（类本身也对象，原始数据类型不是），这个类中包含了众多的方法，这些方法可以用来获取其他对象的信息
**反射**：利用Class类==从对象获取类的信息、初始化或者调用等操作==的这一过程叫做反射reflection
**正射**：是指从类获取对象的这一过程，也就是类实例化这一过程。这一过程的具体流程：==JVM首先会加载ClassName.class，加载到内存之后，在方法区/堆（具体哪一个有争议）中，在方法区/堆中创建一个Class对象对应ClassName类==

==获取Class对象==：
- Class.forName("com.Student")==参数传递完整的包加类名==
- obj.getClass()
- ClassName.class

#### Class对象提供的一些功能：
- 运行时判断对象所属类和父类以及包
`obj.getClass(); // 获取Class类`
`obj.getClass().getSimpleName(); // 获取类名`
`obj.getClass().getName(); // 获取完整路径的类名`
`obj.getClass().getPackage(); //获取包名`
`obj.getSuperClass(); // 获取父类，返回的也是Class对象`
`obj.getInterfaces();obj.getInterface(); //返回接口的Class对象，可以调用getMethods()等方法`

- 运行时创建任意类的一个类对象
1. 可以使用getInstance()或者newInstance()
`ClassType obj = ClassObj.newInstance();`使用Class对象中的newInstance()返回类对象。但是如果==类没有无参数构造方法，会报错==。因为newInstance()==调用的时类的无参数构造方法==

2. 还可使用getConstructor()方法获取构造器后调用newInstance()传入对应参数构造对象
```Java
Constructor<?> [] cons = ClassObj.getConstructors();
// 获取类下面的构造方法列表
// 获取到的方法还能使用getParameterType()等获取方法的详情
`ClassName obj = (ClassName)cons[0].newInstance(); 
// 需要进行强制类型转换，不然可能报错，而且这里面的newInstance
// 是可以传参数的只是需要依据是哪一个构造方法来确定传入的参数个数和顺序.
```

- 运行时获取类定义的成员变量和方法
Field、Method、Constructor是类，代表的类中的fields，methods和constructors
`getFields(); // 获取public字段，静态和非静态在这里没有区别`
`getDeclaredFields(); // 获取所有的字段`
`field.get(object); // 获取属性值`
`field.set(object, value); // 设置修改public`
`field.setAccessible(true); // 设置了权限后可以修改访问private属性和方法`
`getMethods(); // 获取所有的共有方法，或访问到父类的`
`getDeclaredMethods(); 获取所有的方法，不会获取到父类的`
调用获取到的方法
`method.invoke(obj, parameter); // 使用invoke方法，传入对象和参数，无参数就传入null，即可调用方法，对于private方法，可以修改权限后调用，setAccessible(true).`

- 在运行时调用任意一个类对象的方法
```java
public static void main(String[] args){
	Class<?> clObj = Class.forName("Person");
	Method[] methods = clObj.getDeclaredMethods(); 
	// 获取所有的方法，没有父类的
	for (Method method: methods){
		method.setAccessible(true);  
		// 设置true，所有方法都有权限访问了
		methos.invoke(person, ...args: null);  // 调用方法
	}
	Method method = clObj.getMethod("methodName", String.class);  
	// 获取特定的方法，第一个参数方法名
	// 第二个参数，参数类型的class对象或者无参数时null。
	
}

```

- 生成动态代理


#### 反射reflection的优缺点
##### 优点
反射可以在不知道运行哪一个类的情况下，获取到类的信息，创建对象以及操作对象，==方便扩展==，是框架设计的灵魂，框架在设计的时候，为了降低耦合度，不能将类型写死，需要考虑扩展功能
==降低了耦合度==，变得更加灵活，在==运行时去确定类型==，绑定对象，体现了==多态==的特性

##### 缺点
反射需要动态类型，JVM没办法优化这部分代码，==执行效率或相对直接初始化对象而有所降低==，业务代码不建议使用。
反射可以访问到private的属性和方法，会==破坏封装性==，有安全隐患，还会==破坏单例==的设计。
反射会使得==代码变得复杂，不容易维护==。
