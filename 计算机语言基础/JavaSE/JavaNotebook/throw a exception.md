---
title: throw a exception
author: lijiabao
date: 2021-02-21 15:12:22.365892800 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
如何指定可被方法抛出的异常：使用throw关键字`throw throwableException;`

throw关键字后面跟着的对象需要是Throwable的子类（直接子类或者间接子类）

直接子类：Throwable的直接子类有两个，Error，Exception

间接子类：Exception的子类，RuntimeException。

```java
// 如何抛出一个异常
public String readDate(Scanner in) throws EOFException{
	while (..){
		if (encount a error){
			throw new EOFException();
		}
	}
}

```
抛出异常的步骤是找到一个合适的异常类，在方法中指出可能遇到的异常，然后创建这个异常类的对象，然后将对象抛出
1. find a appropriate exception class
2. create a object of the exception
3. throw it


#### chained exceptions
有些应用通常在响应处理某个异常的时候，会抛出另外一个异常。
实际上是第一个异常造成第二个异常的出现。
所以知道什么时候一个异常造成另外一个异常是很有帮助的。
==chained exception==可以做到，下面是几个在Throwable中支持chained exceptions的方法和构造器：
传入构造器和initCause方法的Throwable是造成当前异常的异常
- `Throwable(String, Throwable)`:
- `Throwable(Throwable)`
- `Throwable getCause()`：返回的是造成当前错误的异常
- `Throwable initCause(Throwable)`：设置造成当前异常的异常


#### 获取Stack Trace信息
**statck trace**：提供的提供当前线程的执行历史并且列出当异常发生的时候调用的类和方法名字。当异常发生的时候，stack trace是很有用的debug工具
```java
catch(Exception cause){
	StackTraceElement[] elements = cause.getStackTrace();
	for(int i = 0, n = elements.length;i < n; i++){
		System.err.println(elements[i].getFileName() + ":" +elements[i].getLineNumber()+ ":"+elements[i].getMethodName());
}
}

```

#### logging API
使用logging API除了可以将错误异常信息打印，其可以将异常信息导出到文件中。使用：`java.util.logging`
```java
try{
	Handler handler = new FileHandler("output.log")
	Logger.getLogger("").addHandler(handler);
}catch (IOException e){
	Logger logger = Logger.getLogger(package.name);
	StackTraceElement[] elements = e.getStackTrace();
	for (StackTraceElement element:elements){
		logger.log(Level.WARNING, element.getMethodName());
	}
}

```


#### 需要自己写异常类的情况
- 需要写一个在Java平台中没有提供的异常的时候
- 如果用户可以区分你写的异常类和其他vendor写的类的时候
- 你的代码是否抛出好几个相关的异常
- 如果使用了别人的异常，是否用户有那些异常的访问权限，也就是说你的包是否是独立且self-contained

==选择超类==：创建自己异常的时候应该考虑使用哪个作为父类更合适，Error通常用作严重的硬件错误时使用（如那些使得JVM停止运行的错误），大部分时候都选择Exception作为父类（为了可读性考虑，最好将继承Exception的类后面都加上Exception）