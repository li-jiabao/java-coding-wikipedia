---
title: Exception
author: lijiabao
date: 2021-02-21 14:28:25.497501100 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
#### [[Exception Advantages]]

**Advantage 1**：将异常处理代码和正常的代码分离开来


**Advanage 2**：将异常错误推送到stack trace


**Advantage 3**：将错误类型分组并区分错误类型


#### 异常类，抛出异常，查找调用堆栈，分配异常处理器（捕获异常）
==正常的异常机制的整过程==：
创建异常类->
在有需求的方法中抛出异常->
异常发生的时候通过查看调用堆栈确定异常位置->
创建异常类推送给编译器->
根据找到的异常类去和异常处理器进行匹配->
匹配到的异常处理器对异常进行处理->
如果继续出现错误，抛出chained异常->
可用getStackTrace方法去获取StatckTraceElement对象->
利用这个对象去获取具体的错误信息，可以很好进行debug
工作


异常是发生再程序运行期间发生的事件，这些事件会阻碍程序的正常运行。
当一个程序执行时发生错误，这个方法会创建一个对象并将其呈递给运行系统。这个对象就是Exception，他包含了错误的信息，包含有错误的类型和错误发生时程序的状态。创建一个异常对象并将其送到运行系统，这个过程叫做抛出异常。
在方法抛出异常之后，运行系统就会尝试去发现一些步骤去处理，这些步骤是一个方法有序列表，这些方法被用来到达发生错误的方法处，这个方法列表叫做==call stack==
运行系统搜寻调用堆栈来找到包含可以解决这个异常的代码块。这个搜寻从发生错误的方法开始，然后以调用堆栈的反序去找寻。当找到合适的异常处理器==exception handler==时，运行系统将异常对象传递给该处理器，处理器判断是否可以处理该异常是通过判断异常类型是否和处理器的类型一致。
选择处理器处理异常的过程叫做catch exception（捕获异常），如果没有找到合适的处理器处理异常，程序会终止

#### 异常处理的基本要求
合理的Java程序必须重视Catch or Specify requirement。可以抛出异常的代码需要有：
- Try声明（可以捕获异常），try语句必须提供异常处理器。
- 方法必须定义他是否可以抛出异常，方法必须提供throws语句（带有异常列表）


#### 三个大的异常类
不是所有的异常都满足上面的两个条件
下面介绍以下异常中重要的三个类：

1. [[checked exception]]：
这一类异常都是设计良好的应用应该考虑到的并且可以从中修复的一类异常情况，例如访问文件的时候，如果传入一个没有的文件，就会出现java.io.FileNotFoundException。编写良好的程序可以捕获这类异常兵并且提示犯错给出改进。
checked异常满足两个基本要求，所有的异常都是checked异常，除了Error和RuntimeException和他们的子类
2. error exception：
这是程序外部的往往难以预料和处理的异常，例如成功打开了一个文件，但是由于硬件或者系统故障，不能正常读取，这将会抛出java.io.IOError。为了提示异常问题所在会捕获异常，并且打印堆栈trace并退出也是有意义的。Error不遵从两个基本条件。error指的是Error类和他的子类

3. runtime exception：
指的是程序内部的，但是往往是那些无法预料和处理的异常，这些通常就指向程序的bug所在（比如逻辑错误、不适当的API运用）比如传递文件名时，发生逻辑错误而导致传入null给构造器，就会出现NullPointerException
。应用可捕获这类异常，消除这个Bug是很有意义的。Runtime异常不满足两个基本条件，runtime异常是那些RuntimeException和它的子类

==runtime异常和error合称[[unchecked exception]]==


#### 绕过Catch or Specify
有些人认为Catch or Specify要求是一个严重的缺陷，并并且绕过它用一些unchecked异常来取代checked异常。一般来说，这是不建议的。

#### [[catch and handle exception]]


#### [[Specify Exception thrown by method]]


#### [[throw a exception]]


#### [[create a exception class]]


#### [[异常使用技巧]]


#### [[断言]]


#### [[日志logger]]