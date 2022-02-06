---
title: create a exception class
author: lijiabao
date: 2021-02-21 15:22:13.178470600 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
#### 创建合适的异常类
习惯的做法：
自定义的异常类中包含两个构造器，一个是默认的构造器，，一个是包含详细错误描述信息的构造器（在超类throwable中toString()方法会返回一个字符串，其中包含这个详细信息，对于调试很有用）

```java
class FileFormatException extends IOException{
	public FileFOrmatException{}
	publix FileFormatException(String gripe){
		super(gripe);
	}
}
// 创建好自己的异常之后，就可以按照标准的异常抛出方法进行抛出
```

对于异常的处理，有两种：
- 抛出，抛出就是不管这个异常
- 捕获：对可能出现的异常进行捕获，并选择相对应的处理方案