---
title: catch and handle exception
author: lijiabao
date: 2021-02-21 15:54:32.836956200 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
#### 对于异常处理的最佳方案

对于抛出检查型异常的方法，必须对这个异常进行处理或者是继续传递这个异常（也就是继续抛出，不处理）
哪种方法更好呢？
1. 对于知道如何去处理的异常，就进行捕获
2. 对于不清楚如何处理的，传递这个异常不处理（方法定义时加入throws语句抛出）

#### 捕获异常

有三种主要的异常处理组件和一个stream有关的异常捕获语句：
try语句应该包含至少一个catch或者finally语句，可以拥有多个catch语句。
try-catch-finall这三个来组成的

1. 
```java
try{
	code; // 合法的语句，需要含有可抛出异常的语句
}
catch and finally blocks...
```

2. 
```java
try{
	code; // 和第一种一样
}catch (ExceptionType name) {
	handler block;
	// 此处放置捕获相应的异常之后的处理语句
} catch (ExceptionType1| ExceptionType2 name){
	handler block;
}
```
3. 
```java
try{
	code;
}catch (ExceptionType name) {
	handler block;
}finally{
}

```

4. 特殊的主要用于资源处理的异常语句，implements Closeable的类就可以被用做资源。（和python中的上下文语句，with一样，语句执行结束后自动关闭资源对象）
```java
try (在这里声明需要关闭放置资源丢失的一些操作（如文件读取器和写入器这一类）){
	
}
// 其他的和正常的异常捕获一致，可以有catch和finally语句
```


#### 捕获多个异常

```java
public void exceptionTest(){
	try{
		code block
	}
	catch (Exception e){
		exceptionhandler
	}
	catxh (IOException e){
		exception handler
	}
	...
	catch (EOFException  | FileNotFoundException e){
		// 也可以是这样的对于多个异常采用相同的处理代码块
	}
	finally{
			
	}
}
```

#### 再次抛出异常与异常链

```java
try{
	code block
}
catch (Exception e){
	if (){
		throw new newExeption();
	}
	// 抛出新的异常
}
catch (Exception original){
	if (){
		var e = new newException();
		e.initCause(original);
		throw e;
		// 也可以是这样的异常链的抛出，把原始的异常设置为新异常的起因
		// THrowable cause = causeException.getCause();
		// 这一句使用来查询捕获异常的起因
	}
}
catch (Exception e){
	logger.log(level, message, e);
	throw e;
	// 这是一种只用来记录异常而不处理的用法
}
```

当代码抛出异常时，其代码块后面的语句就会停止运行，并退出当前的这个方法
但有些时候，对于资源的处理是需要清理的，不然会出现问题，因此，就出现了finally语句

异常逻辑结束后，还会进行finally语句块的执行再退出方法



#### 分析堆栈轨迹元素

```
// 调用throwable类的printStackTrace方法访问堆栈轨迹的文本描述信息
var t = new Throwable();
var out = new StringWriter();
t.printStackTrace(new PrintWriter(out));
String description = out.toString();
```
还有另外一种更加灵活的方式，使用StackWalker类，会生成一个StackWalker.StackFrame流，其中的每个实例分别描述一个栈针，用下面的方法去处理这些栈针。

```java
StackWalker walker = StackWalker.getInstance();
walker.forEach(frame -> analyze frame);
// 或者懒人方法
walker.walk(stream -> process stream);
```